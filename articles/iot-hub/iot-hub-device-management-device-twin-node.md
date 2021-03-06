<properties
	pageTitle="Administración de dispositivos gemelos desde Centro de IoT | Microsoft Azure"
	description="Tutorial de Centro de IoT de Azure para la administración de dispositivos que describen cómo utilizar dispositivos gemelos."
	services="iot-hub"
	documentationCenter=".net"
	authors="juanjperez"
	manager="timlt"
	editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="dotnet"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="04/29/2016"
 ms.author="juanpere"/>

# Tutorial: Uso del dispositivo gemelo con Node.js (versión preliminar)

[AZURE.INCLUDE [iot-hub-device-management-twin-selector](../../includes/iot-hub-device-management-twin-selector.md)]

## Introducción

La administración de dispositivos de Centro de IoT de Azure presenta al dispositivo gemelo, una representación del lado del servicio de un dispositivo físico. A continuación se encuentra un diagrama que muestra los distintos componentes del dispositivo gemelo.

![][img-twin]

En este tutorial, nos centramos en las propiedades del dispositivo. Para obtener más información sobre el resto de componentes, consulte [Introducción a la administración de dispositivos de Centro de IoT de Azure][lnk-dm-overview].

Las propiedades del dispositivo son un diccionario predefinido de propiedades que describen el dispositivo físico. El dispositivo físico es el maestro de cada propiedad del dispositivo y es el almacén de autoridad de cada valor correspondiente. Una representación "coherente en última instancia" de estas propiedades se almacena en el dispositivo gemelo que está en la nube. La coherencia y actualización están sujetos a la configuración de la sincronización, que se describe a continuación. Algunos ejemplos de propiedades del dispositivo incluyen la versión del firmware, el nivel de la batería y el nombre del fabricante.

## Sincronización de propiedades del dispositivo

El dispositivo físico es el origen de autoridad de las propiedades del dispositivo. Los valores seleccionados en el dispositivo físico se sincronizan automáticamente con el dispositivo gemelo en el Centro de IoT a través del patrón de *observación/notificación* descrito por [LWM2M][lnk-lwm2m].

Cuando el dispositivo físico se conecta al Centro de IoT, el servicio inicia *observaciones* en las propiedades seleccionadas del dispositivo. Luego, el dispositivo físico *notifica* al Centro de IoT los cambios en las propiedades del dispositivo. Para implementar la histéresis, **pmin** (el tiempo mínimo entre notificaciones) se establece en 5 minutos. Esto significa que en las propiedades, el dispositivo físico no envía una notificación al Centro de IoT con una frecuencia superior a una vez cada 5 minutos, aunque haya un cambio. Para garantizar la actualización, **pmax** (el tiempo máximo entre notificaciones) se establece en 6 horas. Esto significa que en las propiedades, el dispositivo físico envía una notificación al Centro de IoT al menos una vez cada 6 horas, aunque no se haya producido ningún cambio en la propiedad en ese período.

Cuando el dispositivo físico se desconecta, la sincronización se detiene. La sincronización se reinicia cuando el dispositivo se vuelve a conectar al servicio. Para garantizar la actualización, siempre puede comprobar la hora de la última actualización de una propiedad.

A continuación se enumera la lista completa de propiedades del dispositivo que se observan automáticamente:

![][img-observed]

## Ejecución del ejemplo de dispositivo gemelo

En el siguiente ejemplo se amplía la función del tutorial [Introducción a la administración de dispositivos de Centro de IoT de Azure][lnk-get-started]. Comenzando por tener los distintos dispositivos en ejecución, usa al dispositivo gemelo para leer y cambiar las propiedades de un dispositivo simulado.

### Requisitos previos 

Antes de ejecutar este ejemplo, debe haber completado los pasos de [Introducción a la administración de dispositivos de Centro de IoT de Azure][lnk-get-started]. Esto significa que los dispositivos simulados deben estar en ejecución. Si ya realizó el proceso anteriormente, reinicie ahora los dispositivos simulados.

### Inicio del ejemplo

Para iniciar el ejemplo, debe ejecutar ```jobClient_devicePropertyReadWrite.js```. Así se leen las propiedades del dispositivo tanto del dispositivo gemelo como del dispositivo físico. También cambia una propiedad del dispositivo en el dispositivo físico. Para iniciar el ejemplo, siga los pasos que se indican a continuación:

1.  Desde la carpeta raíz donde clonó el repositorio **azure-iot-sdks**, vaya al directorio **azure-iot-sdks/node/service/samples**.

2.  Abra **jobClient\_devicePropertyReadWrite.js** y reemplace el marcador de posición por su cadena de conexión del Centro de IoT.

2.  Ejecute `node jobClient_devicePropertyReadWrite.js`.

Debería ver el resultado en la ventana de la línea de comandos que muestra el uso del dispositivo gemelo. El ejemplo realiza el siguiente proceso:

1.  Lectura superficial: imprime las propiedades de dispositivo `BatteryLevel` y `Timezone` en el dispositivo gemelo.

2.  Lectura profunda: leer la propiedad de dispositivo `Timezone` del dispositivo físico.

3. Lectura superficial: imprime las propiedades de dispositivo `BatteryLevel` y `Timezone` en el dispositivo gemelo.

4.  Escritura profunda: escribir la propiedad del dispositivo **Timezone** en el dispositivo físico.

5.  Lectura profunda: leer la propiedad del dispositivo **Timezone** del dispositivo físico para ver que ha cambiado.

6.  Lectura superficial: imprime las propiedades de dispositivo `BatteryLevel` y `Timezone` en el dispositivo gemelo.

### Lectura superficial

Hay diferencia entre las lecturas *superficiales* y las escrituras y lecturas *profundas*. Una lectura superficial devuelve el valor de la propiedad solicitada desde el dispositivo gemelo almacenado en Centro de IoT de Azure. Este será el valor de la operación de notificación anterior. No se puede realizar una escritura superficial porque el dispositivo físico es el origen de autoridad de las propiedades del dispositivo. Una lectura superficial es simplemente leer la propiedad desde el dispositivo gemelo:

```
deviceInfo.deviceProperties.BatteryLevel.value
```

Para determinar la actualización de estos valores, puede comprobar la hora en que se realizó la última actualización:

```
deviceInfo.deviceProperties.BatteryLevel.lastUpdatedTime
```

Las propiedades del servicio, que solo se almacenan en el dispositivo gemelo, se pueden leer de forma parecida. No se sincronizan con el dispositivo.

### Lectura profunda

Una lectura profunda inicia un trabajo del dispositivo para leer el valor de la propiedad solicitada desde el dispositivo físico. Los trabajos de dispositivos se presentaron en [Introducción a la administración de dispositivos de Centro de IoT de Azure][lnk-dm-overview] y se describen detalladamente en [Tutorial: How to use device jobs to update device firmware][lnk-dm-jobs] (Tutorial: Uso de trabajos de dispositivos para actualizar el firmware del dispositivo). La lectura profunda le proporcionará un valor más actualizado de la propiedad del dispositivo, porque la actualización no está limitada por el intervalo de notificación. El trabajo envía un mensaje al dispositivo físico y actualiza el dispositivo gemelo con el valor más reciente solo para la propiedad especificada. No actualiza todo el dispositivo gemelo.

```
scheduleDevicePropertyRead(jobId, deviceIds, propertyNames, done)
```

No puede hacer una lectura profunda de las propiedades del servicio o etiquetas, porque no se sincronizarán con el dispositivo.

### Escritura profunda

Si desea cambiar una propiedad del dispositivo grabable, puede hacerlo con una escritura profunda que inicie un trabajo de dispositivo para escribir el valor en el dispositivo físico. No todas las propiedades de dispositivo se pueden grabar. Para ver una lista completa, consulte el apéndice A de [Introducing the Azure IoT Hub device management client library][lnk-dm-library] (Introducción a la biblioteca de administración de dispositivos del Centro de IoT de Azure).

El trabajo envía un mensaje al dispositivo físico para actualizar la propiedad especificada. El dispositivo gemelo no se actualiza inmediatamente al finalizar el trabajo. Debe esperar a l siguiente intervalo de notificación. Una vez que se produzca la sincronización, podrá ver el cambio en el dispositivo gemelo con una lectura superficial.

```
scheduleDevicePropertyWrite(jobId, deviceIds, properties, done)
```

### Detalles de la implementación del simulador de dispositivos

Vamos a investigar lo que hay que hacer en el lado del dispositivo para implementar el patrón de observación y notificación, y las lecturas y escrituras profundas.

Dado que la sincronización de las propiedades del dispositivo se controla completamente a través de la biblioteca de cliente de DM de Centro de IoT de Azure, lo único que necesita hacer es llamar a la API para establecer la propiedad del dispositivo (nivel de batería en este ejemplo) a intervalos regulares. Cuando el servicio realiza una lectura profunda, se devuelve el último valor establecido. Cuando el servicio realiza una escritura profunda, se llama a este método set. En **iotdm\_simple\_sample.c** puede ver un ejemplo:

```
int level = get_batterylevel();  // call to platform specific code 
set_device_batterylevel(0, level);
```

En lugar de utilizar el método set, puede implementar una devolución de llamada. Para obtener más información sobre esta opción, consulte [Introducing the Azure IoT Hub device management library][lnk-dm-library] (Introducción a la biblioteca de administración de dispositivos del Centro de IoT de Azure).

## Pasos siguientes

Para más información acerca de las características de administración de dispositivos de Centro de IoT de Azure puede realizar los tutoriales:

- [How to find device twins using queries (Búsqueda de dispositivos gemelos mediante consultas)][lnk-tutorial-queries]
- [How to use device jobs to update device firmware (Uso de trabajos de dispositivos para actualizar el firmware del dispositivo)][lnk-tutorial-jobs]
- [Habilitación de los dispositivos administrados detrás de una puerta de enlace de IoT][lnk-dm-gateway]
- [Introducción a la biblioteca de cliente de administración de dispositivos de Centro de IoT de Azure][lnk-library-c]
- Las bibliotecas de cliente de administración de dispositivos proporcionan un ejemplo de un extremo a otro mediante un [dispositivo Intel Edison][lnk-edison].

Para explorar aún más las funcionalidades de Centro de IoT, consulte:

- [Diseño de la solución][lnk-design]
- [Guía del desarrollador][lnk-devguide]
- [SDK de puerta de enlace de IoT (beta): envío de mensajes del dispositivo a la nube con un dispositivo simulado usando Linux][lnk-gateway]
- [Administración de Centros de IoT a través del portal de Azure][lnk-portal]

<!-- images and links -->
[img-twin]: media/iot-hub-device-management-device-twin/image1.png
[img-observed]: media/iot-hub-device-management-device-twin/image2.png

[lnk-lwm2m]: http://technical.openmobilealliance.org/Technical/technical-information/release-program/current-releases/oma-lightweightm2m-v1-0
[lnk-dm-overview]: iot-hub-device-management-overview.md
[lnk-dm-library]: iot-hub-device-management-library.md
[lnk-get-started]: iot-hub-device-management-get-started.md
[lnk-tutorial-queries]: iot-hub-device-management-device-query.md
[lnk-dm-jobs]: iot-hub-device-management-device-jobs.md
[lnk-edison]: https://github.com/Azure/azure-iot-sdks/tree/dmpreview/c/iotdm_client/samples/iotdm_edison_sample

[lnk-tutorial-queries]: iot-hub-device-management-device-query.md
[lnk-tutorial-jobs]: iot-hub-device-management-device-jobs.md
[lnk-dm-gateway]: iot-hub-gateway-device-management.md
[lnk-library-c]: iot-hub-device-management-library.md

[lnk-design]: iot-hub-guidance.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
[lnk-portal]: iot-hub-manage-through-portal.md

<!---HONumber=AcomDC_0713_2016-->