<properties
	pageTitle="Captura de una máquina virtual de Linux para usarla como plantilla | Microsoft Azure"
	description="Obtenga información sobre cómo capturar una imagen de una máquina virtual de Azure basada en Linux creada con el modelo de implementación de Azure Resource Manager."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="dlepow"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/19/2016"
	ms.author="danlep"/>


# Captura de una máquina virtual con Linux para usarla como plantilla de Resource Manager

Use la interfaz de la línea de comandos (CLI) de Azure para capturar y generalizar una máquina virtual de Azure con Linux y así poder usarla como plantilla de Azure Resource Manager cuando desee crear otras máquinas virtuales. Esta plantilla especifica el disco del sistema operativo y cualquier otro disco de datos asociado a la máquina virtual. No incluye los recursos de redes virtuales y tendrá crear una máquina virtual del Administrador de recursos de Azure, por lo que en la mayoría de los casos necesitará configurarlos por separado antes de crear otra máquina virtual que usa la plantilla.

>[AZURE.TIP]Si está interesado en la creación de una imagen de la máquina virtual Linux personalizada y su carga en Azure para poder crear máquinas virtuales desde la imagen, consulte [Upload and create a VM from custom disk image](virtual-machines-linux-upload-vhd.md) (Carga y creación de una máquina virtual desde una imagen de disco personalizada).

## Antes de empezar

En estos pasos se da por sentado que ya creó un máquina virtual de Azure con el modelo de implementación del Administrador de recursos de Azure, que configuró el sistema operativo, que adjuntó cualquier disco de datos y que realizó otras personalizaciones como la instalación de aplicaciones. Puede hacerlo de varias maneras, incluido a través de la CLI de Azure. Si no todavía no lo hizo, consulte estas instrucciones para usar la CLI de Azure en modo de Administrador de recursos de Azure:

- [Creación de una máquina virtual con Linux en Azure mediante la CLI](virtual-machines-linux-quick-create-cli.md)

- [Implementación y administración de máquinas virtuales con plantillas del Administrador de recursos de Azure y CLI de Azure](virtual-machines-linux-cli-deploy-templates.md)

Por ejemplo, podría crear un grupo de recursos denominado *MyResourceGroup* en la región Centro de EE. UU. Después, use el comando **azure vm quick-create** similar al siguiente para implementar una máquina virtual LTS de Ubuntu 14.04 en el grupo de recursos.

 	azure vm quick-create -g MyResourceGroup -n <your-virtual-machine-name> "centralus" -y Linux -Q canonical:ubuntuserver:14.04.2-LTS:latest -u <your-user-name> -p <your-password>

Después de aprovisionar y ejecutar la máquina virtual, debe [asociar y montar un disco de datos](virtual-machines-linux-add-disk.md).


## Captura de la imagen de la máquina virtual

1. Cuando esté listo para capturar la máquina virtual, conéctese a ella con el cliente SSH.

2. En la ventana SSH, escriba el siguiente comando. Observe que el resultado de **waagent** puede variar levemente según la versión de esta utilidad:

	`sudo waagent -deprovision+user`

	Este comando intentará limpiar el sistema y dejarlo adecuado para un reaprovisionamiento. Esta operación ejecuta las siguientes tareas:

	- Quita las claves de host de SSH (si Provisioning.RegenerateSshHostKeyPair es "y" en el archivo de configuración)
	- Borra la configuración de Nameserver en /etc/resolvconf
	- Quita la contraseña del usuario `root` desde /etc/shadow (si Provisioning.DeleteRootPassword es "y" en el archivo de configuración)
	- Quita concesiones del cliente de DHCP en caché
	- Restablece el nombre de host a localhost.localdomain
	- Elimina la última cuenta de usuario aprovisionada (obtenida desde /var/lib/waagent) y los datos asociados.

	>[AZURE.NOTE] El desaprovisionamiento elimina los archivos y datos en un esfuerzo por generalizar la imagen. Solo puede ejecutar este comando en una máquina virtual que quiera capturar como imagen. No garantiza que se haya borrado toda información sensible de la imagen o que sea adecuada para su redistribución a terceros.

3. Escriba **y** para continuar. Puede agregar el parámetro **-force** para evitar este paso de confirmación.

4. Escriba **exit** para cerrar el cliente SSH.

	>[AZURE.NOTE] En los siguientes pasos se supone que ya ha [instalado la CLI de Azure](../xplat-cli-install.md) en el equipo cliente.

5. Desde el equipo cliente, abra la CLI de Azure e inicie sesión en su suscripción de Azure. Para obtener más información, consulte [Conexión a una suscripción de Azure desde la CLI de Azure](../xplat-cli-connect.md).

6. Asegúrese de que está en modo de Administrador de recursos:

	`azure config mode arm`

7. Detenga la máquina virtual que ya desaprovisionó con el comando siguiente:

	`azure vm deallocate -g <your-resource-group-name> -n <your-virtual-machine-name>`

8. Generalice la máquina virtual con el comando siguiente:

	`azure vm generalize –g <your-resource-group-name> -n <your-virtual-machine-name>`

9. Ahora, capture la imagen y una plantilla de archivo local con el comando siguiente:

	`azure vm capture <your-resource-group-name>  <your-virtual-machine-name> <your-vhd-name-prefix> -t <path-to-your-template-file-name.json>`

	Este comando crea una imagen de sistema operativo generalizada, con el prefijo de nombre de disco duro virtual que haya especificado para los discos de máquina virtual. De forma predeterminada, se crean los archivos del disco duro virtual de imagen en la misma cuenta de almacenamiento que usa la máquina virtual original. (Los VHD para cualquier máquina virtual nueva que cree desde la imagen, se almacenarán en la misma cuenta). La opción **-t** crea una plantilla de archivo JSON local que se puede usar para crear una nueva máquina virtual a partir de la imagen.

>[AZURE.TIP] Para buscar la ubicación de una imagen, abra la plantilla de archivo JSON. En **storageProfile**, busque el **uri** de la **imagen** ubicado en el contenedor **system**. Por ejemplo, el uri de la imagen de disco del sistema operativo es similar a `https://xxxxxxxxxxxxxx.blob.core.windows.net/system/Microsoft.Compute/Images/vhds/<your-vhd-name-prefix>-osDisk.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`.

## Implementación de una nueva máquina virtual desde la imagen capturada
Ahora use la imagen con una plantilla para crear una nueva máquina virtual de Linux. Estos pasos le muestran cómo usar la CLI de Azure y la plantilla del archivo JSON que creó con el comando `azure vm capture` para crear la máquina virtual en una nueva red virtual.

### Crear recursos de red

Para usar la plantilla, deberá configurar primero una red virtual y la tarjeta de interfaz de red para la nueva máquina virtual. Recomendamos que cree un nuevo grupo de recursos para estos recursos en la ubicación donde se almacena la imagen de la máquina virtual. Ejecute comandos similares a los siguientes, sustituyendo los nombres por sus recursos y por una ubicación de Azure adecuada ("centralus" en estos comandos):

	azure group create <your-new-resource-group-name> -l "centralus"

	azure network vnet create <your-new-resource-group-name> <your-vnet-name> -l "centralus"

	azure network vnet subnet create <your-new-resource-group-name> <your-vnet-name> <your-subnet-name>

	azure network public-ip create <your-new-resource-group-name> <your-ip-name> -l "centralus"

	azure network nic create <your-new-resource-group-name> <your-nic-name> -k <your-subnetname> -m <your-vnet-name> -p <your-ip-name> -l "centralus"

Para implementar una máquina virtual desde la imagen mediante el JSON que guardó durante la captura, necesita el identificador de la tarjeta de interfaz de red. Puede obtenerla con la ejecución de este comando.

	azure network nic show <your-new-resource-group-name> <your-nic-name>

El **id.** de la salida es una cadena similar a esta.

	/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/<your-new-resource-group-name>/providers/Microsoft.Network/networkInterfaces/<your-nic-name>



### Crear una nueva implementación
Ahora ejecute el siguiente comando para crear la máquina virtual desde la imagen capturada de la máquina virtual y del archivo de plantilla JSON que guardó.

	azure group deployment create <your-new-resource-group-name> <your-new-deployment-name> -f <path-to-your-template-file-name.json>

Se le pedirá que proporcione un nuevo nombre de la máquina virtual, el nombre de usuario de administrador y la contraseña, así como el identificador de la tarjeta de interfaz de red que creó.

	info:    Executing command group deployment create
	info:    Supply values for the following parameters
	vmName: mynewvm
	adminUserName: myadminuser
	adminPassword: ********
	networkInterfaceId: /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resource Groups/mynewrg/providers/Microsoft.Network/networkInterfaces/mynewnic

Si la implementación se realizó correctamente, verá un resultado similar al siguiente ejemplo.

	+ Initializing template configurations and parameters
	+ Creating a deployment
	info:    Created template deployment "dlnewdeploy"
	+ Waiting for deployment to complete
	data:    DeploymentName     : mynewdeploy
	data:    ResourceGroupName  : mynewrg
	data:    ProvisioningState  : Succeeded
	data:    Timestamp          : 2015-10-29T16:35:47.3419991Z
	data:    Mode               : Incremental
	data:    Name                Type          Value


	data:    ------------------  ------------  -------------------------------------

	data:    vmName              String        mynewvm


	data:    vmSize              String        Standard_D1


	data:    adminUserName       String        myadminuser


	data:    adminPassword       SecureString  undefined


	data:    networkInterfaceId  String        /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/mynewrg/providers/Microsoft.Network/networkInterfaces/mynewnic
	info:    group deployment create command OK

### Comprobar la implementación

Ahora conéctese por SSH a la máquina virtual que creó para comprobar la implementación y empezar a usar la nueva máquina virtual. Para conectarse mediante SSH, busque la dirección IP de la máquina virtual que creó mediante la ejecución del comando siguiente:

	azure network public-ip show <your-new-resource-group-name> <your-ip-name>

La dirección IP pública aparece en la salida del comando. De forma predeterminada, se conecta a la máquina virtual de Linux mediante SSH en el puerto 22.

## Creación de máquinas virtuales adicionales con la plantilla

Use la imagen capturada y la plantilla para implementar máquinas virtuales adicionales siguiendo los pasos descritos en la sección anterior.

* Asegúrese de que la imagen de la máquina virtual se encuentra en la misma cuenta de almacenamiento que hospedará el disco duro virtual de la máquina virtual.
* Copie el archivo JSON de plantilla y escriba un valor único para el **uri** del disco duro virtual de todas las máquinas virtuales.
* Cree una nueva tarjeta de interfaz de red en la misma red virtual o en otra.
* Cree una implementación del grupo de recursos en el que configuró la red virtual, mediante el uso del archivo JSON de la de plantilla modificada.

Si quiere que la red se configure automáticamente al crear una máquina virtual de la imagen, use la [plantilla 101-vm-from-user-image](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image) en GitHub. Esta plantilla crea una máquina virtual desde la imagen personalizada, así como la red virtual necesaria, la dirección IP pública y la tarjeta de interfaz de red de recursos. Para obtener un tutorial acerca del uso de la plantilla en el Portal de Azure, consulte [Crear una máquina virtual desde una imagen personalizada mediante una plantilla del Administrador de recursos de Azure](http://codeisahighway.com/how-to-create-a-virtual-machine-from-a-custom-image-using-an-arm-template/).

## Uso del comando azure vm create

Por lo general, querrá usar una plantilla del Administrador de recursos para crear una máquina virtual a partir de la imagen. Sin embargo, puede crear la máquina virtual _imperativamente_ usando el comando **azure vm create** con el parámetro **-Q** (**--image-urn**). También podrá transferir el parámetro **-d** (**--os-disk-vhd**) para especificar la ubicación del archivo .vhd del sistema operativo para la nueva máquina virtual. Debe estar en el contenedor de discos duros virtuales de la cuenta de almacenamiento donde se almacena el archivo VHD. El comando copiará el VHD para la nueva máquina virtual automáticamente en el contenedor de discos duros virtuales.

Haga lo siguiente antes de ejecutar **azure vm create** con la imagen:

1.	Cree un nuevo grupo de recursos o identifique un grupo de recursos existente para la implementación.

2.	Cree un recurso de direcciones IP públicas y un recurso de tarjeta de interfaz de red para la nueva máquina virtual. Para conocer los pasos para crear una red virtual, la dirección IP pública y la tarjeta de interfaz de red con la CLI, consulte más arriba este artículo. (El comando **azure vm create** también puede crear una nueva tarjeta de interfaz de red, pero necesitará pasar parámetros adicionales para una red virtual y una subred).


A continuación, ejecute un comando similar a los siguientes, pasando los URI para el nuevo archivo VHD del sistema operativo y la imagen existente.

	azure vm create <your-resource-group-name> <your-new-vm-name> eastus Linux -d "https://xxxxxxxxxxxxxx.blob.core.windows.net/vhds/<your-new-VM-prefix>.vhd" -Q "https://xxxxxxxxxxxxxx.blob.core.windows.net/system/Microsoft.Compute/Images/vhds/<your-image-prefix>-osDisk.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd" -z Standard_A1 -u <your-admin-name> -p <your-admin-password> -f <your-nic-name>

Para obtener opciones de comando adicionales, ejecute `azure help vm create`.

## Pasos siguientes

Para administrar las máquinas virtuales con la CLI, consulte las tareas de [Implementación y administración de máquinas virtuales con plantillas del Administrador de recursos de Azure y CLI de Azure](virtual-machines-linux-cli-deploy-templates.md).

<!---HONumber=AcomDC_0720_2016-->