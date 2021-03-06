<properties
	pageTitle="Usar Hudson con Almacenamiento de blobs | Microsoft Azure"
	description="Describe cómo usar Hudson con almacenamiento de blobs de Azure como un repositorio para artefactos de compilación."
	services="storage"
	documentationCenter="java"
	authors="rmcmurray"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="Java"
	ms.topic="article"
	ms.date="06/24/2016" 
	ms.author="robmcm"/>

# Uso del almacenamiento de Azure con una solución de integración continua Hudson

## Información general

En la siguiente información se describe cómo usar Almacenamiento de blobs como repositorio de artefactos de compilación creados por una solución de integración continua (CI) de Hudson o como origen de archivos descargables que se pueden usar en un proceso de compilación. Uno de los escenarios en que esto le puede resultar útil es para codificar en un entorno de desarrollo ágil (con el uso de Java o de otros lenguajes), cuando las compilaciones se ejecutan según una integración continua y si necesita un repositorio para los artefactos de compilación, a fin de que, por ejemplo, pueda compartirlos con otros miembros de la organización, con los clientes o para mantener un archivo. Otro es cuando su trabajo de compilación propiamente dicho requiere otros archivos, por ejemplo, la descarga de dependencias como parte de la entrada de compilación.

En este tutorial usará el complemento del Almacenamiento de Azure de la integración continua Hudson habilitada por Microsoft.

## Introducción a Hudson ##

Hudson permite la integración continua de un proyecto de software al hacer posible que los desarrolladores integren fácilmente los cambios de su código y cuenten con compilaciones generadas de manera automática y frecuente, lo que les permite aumentar la productividad. Las compilaciones tienen varias versiones y los artefactos de compilación se pueden cargar en varios repositorios. En este artículo se describe cómo usar el almacenamiento de blobs de Azure como repositorio de los artefactos de compilación. También se mostrará cómo descargar dependencias del almacenamiento de blobs de Azure.

Puede encontrar más información acerca de Hudson en [Meet Hudson](http://wiki.eclipse.org/Hudson-ci/Meet_Hudson).

## Ventajas de usar el servicio BLOB ##

Las ventajas de usar el servicio BLOB para hospedar los artefactos de compilación para el desarrollo ágil son:

- Alta disponibilidad de sus artefactos de compilación o de sus dependencias descargables.
- Rendimiento si la solución de integración continua Hudson carga los artefactos de compilación
- Rendimiento si los clientes y socios descargan los artefactos de compilación
- Control sobre las directivas de acceso de usuarios, con una opción entre acceso anónimo, acceso compartido basado en expiración, acceso mediante firma, acceso privado, etc.

## Requisitos previos ##

Para usar el servicio BLOB con la solución de integración continua Hudson, necesitará lo siguiente:

- Una solución de integración continua Hudson.

    Si no dispone actualmente de una solución de integración continua Hudson, puede ejecutarla mediante la siguiente técnica:

    1. En una máquina habilitada para Java, descargue Hudson WAR de <http://hudson-ci.org/>.
    2. En un símbolo del sistema que se abre en la carpeta que contiene Hudson WAR, ejecute Hudson WAR. Por ejemplo, si ha descargado la versión 3.1.2:

        `java -jar hudson-3.1.2.war`

    3. En el explorador, abra `http://localhost:8080/`. Con esta acción se abre el panel de Hudson.

    4. Tras el primer uso de Hudson, realice la configuración inicial en `http://localhost:8080/`.

    5. Después de terminar la configuración inicial, cancele la instancia en ejecución de Hudson WAR, vuelva a iniciar Hudson WAR y vuelva a abrir el panel de Hudson, `http://localhost:8080/`, que usará para instalar y configurar el complemento de Almacenamiento de Azure.

        Aunque una solución de integración continua Hudson típica se configuraría para ejecutarse como un servicio, la ejecución del archivo war de Hudson en la línea de comandos será suficiente para este tutorial.

- Una cuenta de Azure. Puede iniciar sesión en una cuenta de Azure en <http://www.azure.com>.

- Una cuenta de almacenamiento de Azure. Si aún no tiene una cuenta de almacenamiento, puede crearla con los pasos descritos en [Creación de una cuenta de almacenamiento](storage-create-storage-account.md#create-a-storage-account).

- Es recomendable estar familiarizado con la solución de integración continua Hudson, pero no obligatorio, ya que a continuación se ilustrará un ejemplo básico con los pasos que debe seguir para usar el servicio BLOB como repositorio para los artefactos de compilación de integración continua Hudson.

## Uso del servicio BLOB con la integración continua Hudson ##

Para usar el servicio BLOB con Hudson, necesitará instalar el complemento de almacenamiento de Azure, configurarlo para usar la cuenta de almacenamiento y, a continuación, crear una acción posterior a la compilación que cargue los artefactos de compilación en la cuenta de almacenamiento. Estos pasos se describen en las secciones siguientes.

## Instalación del complemento de almacenamiento de Azure ##

1. En el panel de Hudson, haga clic en **Manage Hudson** (Administrar Hudson).
2. En la página **Administrar Hudson**, haga clic en **Administrar complementos**.
3. Haga clic en la pestaña **Available** (Disponible).
4. Haga clic en **Others** (Otros).
5. En la sección **Cargadores de artefactos**, seleccione **Complemento del Almacenamiento de Microsoft Azure**.
6. Haga clic en **Instalar**.
7. Después de que la instalación se haya completado, reinicie Hudson.

## Configuración del complemento de almacenamiento de Azure para usar la cuenta de almacenamiento ##

1. En el panel de Hudson, haga clic en **Manage Hudson** (Administrar Hudson).
2. En la página **Administrar Hudson**, haga clic en **Configurar sistema**.
3. En la sección **Microsoft Azure Storage Account Configuration** (Configuración de la cuenta de almacenamiento de Microsoft Azure):

    a. Escriba el nombre de la cuenta de almacenamiento, que puede obtener en el [Portal de Azure](https://portal.azure.com).

    b. Escriba la clave de la cuenta de almacenamiento, que también puede obtener en el [Portal de Azure](https://portal.azure.com).

    c. Use el valor predeterminado para **Blob Service Endpoint URL** (Dirección URL del extremo de servicio BLOB) si usa la nube pública de Azure. Si usa una nube de Azure distinta, use el punto de conexión tal y como se especifica en el [Portal de Azure](https://portal.azure.com) para la cuenta de almacenamiento.

    d. Haga clic en **Validate storage credentials** (Validar credenciales de almacenamiento) para validar la cuenta de almacenamiento.

    e. [Opcional] Si tiene más cuentas de almacenamiento que desea que estén disponibles para la integración continua Hudson, haga clic en **Add more storage accounts** (Agregar más cuentas de almacenamiento).

    f. Haga clic en **Save** (Guardar) para guardar la configuración.

## Creación de una acción posterior a la compilación que carga los artefactos de compilación para la cuenta de almacenamiento ##

Con el fin de facilitar instrucciones, primero necesitaremos crear un trabajo que generará varios archivos y, a continuación, agregar la acción posterior a la compilación para cargar estos archivos en la cuenta de almacenamiento.

1. En el panel de Hudson, haga clic en **New Job** (Nuevo trabajo).
2. Asigne al trabajo el nombre **MyJob**, haga clic en **Build a free-style software job** (Compilar un trabajo de software de estilo libre) y, a continuación, haga clic en **OK** (Aceptar).
3. En la sección **Build** (Compilar) de la configuración del trabajo, haga clic en **Add build step** (Agregar paso de compilación) y seleccione **Execute Windows batch command** (Ejecutar comando por lotes de Windows).
4. En **Command** (Comando), use los comandos siguientes:

        md text
        cd text
        echo Hello Azure Storage from Hudson > hello.txt
        date /t > date.txt
        time /t >> date.txt

5. En la sección **Post-build Actio**ns (Acciones posteriores a la compilación) de la configuración del trabajo, haga clic en **Upload artifacts to Microsoft Azure Blob storage** (Cargar artefactos en el almacenamiento de blobs de Microsoft Azure).
6. En **Storage account name** (Nombre de cuenta de almacenamiento), seleccione la cuenta de almacenamiento que desea usar.
7. En **Container Name** (Nombre de contenedor), especifique el nombre del contenedor. (El contenedor se creará si aún no existe cuando se carguen los artefactos de compilación). Puede usar las variables de entorno, así que, para este ejemplo, escriba **${JOB\_NAME}** como nombre del contenedor.

    **Sugerencia**

    Debajo de la sección **Command** (Comando) en la que ha insertado un script para **Execute Windows batch command** (Ejecutar comando por lotes de Windows) hay un vínculo a las variables de entorno reconocidas por Hudson. Haga clic en dicho vínculos para obtener acceso a las descripciones y los nombres de las variables de entorno. Tenga en cuenta que las variables de entorno que contienen caracteres especiales, como **BUILD\_URL**, no se admiten como nombre de contenedor ni como ruta de acceso virtual común.

8. Haga clic en **Make new container public by default** (Hacer público el nuevo contenedor de forma predeterminada) para este ejemplo. (Si desea usar un contenedor privado, también tendrá que crear una firma de acceso compartida para permitir el acceso. Eso va más allá del ámbito de este artículo. Para obtener más información acerca de las firmas de acceso compartido, consulte [Firmas de acceso compartido: Descripción del modelo de firmas de acceso compartido](storage-dotnet-shared-access-signature-part-1.md).
9. [Opcional] Haga clic en **Clean container before uploading** (Limpiar contenedor antes de cargarlo) si desea que se borre el contenido del contenedor antes de cargar los artefactos de compilación (déjela sin marcar si no desea limpiar el contenido del contenedor).
10. Para consultar **List of Artifacts to upload** (Lista de artefactos que cargar), escriba **text/*.txt**.
11. En **Common virtual path for uploaded artifacts** (Ruta de acceso virtual común para artefactos cargados), escriba **${BUILD\\_ID}/${BUILD\\_NUMBER}**.
12. Haga clic en **Save** (Guardar) para guardar la configuración.
13. En el panel de Hudson, haga clic en **Build Now** (Compilar ahora) para ejecutar **MyJob**. Examine el resultado de la consola para consultar el estado. Los mensajes de estado sobre el almacenamiento de Azure se incluirán en los resultados de la consola cuando la acción posterior a la compilación comience a cargar los artefactos de compilación.
14. Tras completar el trabajo satisfactoriamente, abra el blob público si desea examinar los artefactos de compilación.

    a. Inicie sesión en el [Portal de Azure](https://portal.azure.com).

    b. Haga clic en **Storage** (Almacenamiento).

    c. Haga clic en el nombre de la cuenta de almacenamiento que ha usado para Hudson.

    d. Haga clic en **Containers** (Contenedores).

    e. Haga clic en el contenedor llamado **myjob**, que es la versión en minúscula del nombre del trabajo asignado cuando ha creado el trabajo de Hudson. Los nombres de los contenedores y los nombres de los blobs se guardan en minúscula en el almacenamiento de Azure y, además, distinguen mayúsculas de minúsculas. En la lista de blobs del contenedor llamado **myjob**, deben aparecer **hello.txt** y **date.txt**. Copie la URL de cualquiera de estos elementos y ábrala en el explorador. A continuación, verá el archivo de texto cargado como un artefacto de compilación.

Solo se puede crear una acción posterior a la compilación que cargue artefactos en el almacenamiento de blobs de Azure por cada trabajo. Tenga en cuenta que la acción posterior a la compilación para cargar artefactos en el almacenamiento de blobs de Azure puede especificar diferentes archivos (comodines incluidos) y rutas de acceso a los archivos en **List of Artifacts to upload** (Lista de artefactos para cargar) con el uso del punto y coma como separador. Por ejemplo, si su proceso de compilación Hudson produce archivos JAR y TXT en la carpeta **build** (compilar) del área de trabajo, y desea cargar ambos en el almacenamiento de blobs de Azure, use el siguiente código para el valor **List of Artifacts to upload** (Lista de artefactos para cargar): **build/\*.jar;build/\*.txt**. También puede usar sintaxis de dos puntos dobles para especificar una ruta de acceso dentro del nombre del blob. Por ejemplo, si desea que los archivos JAR se carguen mediante archivos **binaries** (binarios) en la ruta de acceso del blob y que los TXT lo hagan por medio de **notices** (notificaciones) en esta misma ruta, use lo siguiente para el valor **List of Artifacts to upload** (Lista de artefactos para cargar): **build/\*.jar::binaries;build/\*.txt::notices**.

## Creación de un paso de compilación que descarga del almacenamiento de blobs de Azure ##

En los siguientes pasos se muestra cómo configurar un paso de compilación para descargar elementos del almacenamiento de blobs de Azure. Esto sería útil si desea incluir elementos en la compilación, por ejemplo, archivos JAR que mantenga en el almacenamiento de blobs de Azure.

1. En la sección **Build** (Compilar) de la configuración del trabajo, haga clic en **Add build step** (Agregar paso de compilación) y seleccione **Download from Azure Blob storage** (Descargar del almacenamiento de blobs de Azure).
2. En **Storage account name** (Nombre de cuenta de almacenamiento), seleccione la cuenta de almacenamiento que desea usar.
3. En **Container name** (Nombre de contenedor), especifique el nombre del contenedor que tiene los blobs que desea descargar. Puede usar variables de entorno.
4. En **Blob name** (Nombre de blob), especifique el nombre del blob. Puede usar variables de entorno. Además, puede usar un asterisco como comodín después de especificar las letras iniciales del nombre del blob. Por ejemplo, **project*** especificaría todos los blobs cuyos nombres comiencen por **project**.
5. [Opcional] En**Download path** (Ruta de acceso de la descarga), especifique la ruta de acceso de la máquina de Hudson en la que desea descargar los archivos del almacenamiento de blobs de Azure. También se pueden usar variables de entorno. (Si no proporciona un valor para **Download path**, los archivos del almacenamiento de blobs de Azure se descargarán en el área de trabajo del trabajo).

Si tiene elementos adicionales que desea descargar del almacenamiento de blobs de Azure, puede crear pasos de compilación adicionales.

Después de ejecutar una compilación, puede comprobar la salida de la consola del historial de compilación, o examinar la ubicación de descarga, para ver si los blobs que esperaba se han descargado correctamente.

## Componentes usados por el servicio BLOB ##

A continuación se ofrece información general acerca de los componentes del servicio BLOB.

- **Cuenta de almacenamiento:** todo el acceso a Almacenamiento de Azure se realiza a través de una cuenta de almacenamiento. Se trata del nivel superior del espacio de nombres para el acceso a los blobs. Una cuenta puede contener una cantidad ilimitada de contenedores, siempre que su tamaño total no supere los 100 TB.
- **Contenedor**: un contenedor proporciona una agrupación de un conjunto de blobs. Todos los blobs deben residir en un contenedor. Además, una cuenta puede disponer de un número ilimitado de contenedores y un contenedor puede almacenar un número ilimitado de blobs.
- **Blob**: archivo de cualquier tipo y tamaño. Existen dos tipos de blobs que pueden almacenarse en Almacenamiento de Azure: blobs en páginas y en bloques. La mayoría de los archivos son blobs en bloques. Un blob en bloques único puede tener un tamaño de hasta 200 GB. En este tutorial se usan blobs en bloques. Los blobs en páginas, que son otro tipo de blobs, pueden tener un tamaño de hasta 1 TB y son más eficaces cuando los intervalos de bytes de un archivo se modifican con frecuencia. Para más información sobre los blobs, consulte [Descripción de los blobs en bloques, en anexos y en páginas](http://msdn.microsoft.com/library/azure/ee691964.aspx).
- **Formato de dirección URL**: los blobs son direccionables con el siguiente formato de dirección URL:

    `http://storageaccount.blob.core.windows.net/container_name/blob_name`

    (El formato anterior se aplica a la nube pública de Azure. Si usa una nube de Azure distinta, use el punto de conexión en el [Portal de Azure](https://portal.azure.com) para determinar el punto de conexión de la URL).

    En el formato anterior, `storageaccount` representa el nombre de la cuenta de almacenamiento, `container_name` representa el nombre del contenedor y `blob_name` representa el nombre del blob. En el nombre del contenedor, puede tener varias rutas de acceso, separadas por una barra diagonal, **/**. El nombre de contenedor utilizado como ejemplo para este tutorial es **MyJob** y **${BUILD\\_ID}/${BUILD\\_NUMBER}** se ha usado para la ruta de acceso virtual común; como resultado, la URL del blob presenta el siguiente formato:

    `http://example.blob.core.windows.net/myjob/2014-05-01_11-56-22/1/hello.txt`

## Pasos siguientes

- [Presentación de Hudson](http://wiki.eclipse.org/Hudson-ci/Meet_Hudson)
- [SDK de almacenamiento de Azure para Java](https://github.com/azure/azure-storage-java)
- [Referencia del SDK de cliente de almacenamiento de Azure](http://dl.windowsazure.com/storage/javadoc/)
- [API de REST de servicios de almacenamiento de Azure](https://msdn.microsoft.com/library/azure/dd179355.aspx)
- [Blog del equipo de almacenamiento de Azure](http://blogs.msdn.com/b/windowsazurestorage/)

Para obtener más información, consulte también el [Centro para desarrolladores de Java](https://azure.microsoft.com/develop/java/).

<!---HONumber=AcomDC_0629_2016-->
