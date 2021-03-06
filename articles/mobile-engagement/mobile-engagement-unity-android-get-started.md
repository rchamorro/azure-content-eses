<properties
	pageTitle="Introducción a Azure Mobile Engagement para la implementación de Unity para Android"
	description="Aprenda a usar Azure Mobile Engagement con los análisis y las notificaciones push para aplicaciones de Unity que se implementan en dispositivos de iOS."
	services="mobile-engagement"
	documentationCenter="unity"
	authors="piyushjo"
	manager=""
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-unity-android"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="03/25/2016"
	ms.author="piyushjo" />

# Introducción a Azure Mobile Engagement para la implementación de Unity para Android

[AZURE.INCLUDE [Modificador del tutorial de imagen prominente](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

En este tema se muestra cómo usar Azure Mobile Engagement para comprender el uso de su aplicación y cómo enviar notificaciones push a usuarios segmentados de una aplicación de Unity durante la implementación en un dispositivo de Android. Este tutorial utiliza el tutorial de iniciación clásico de Unity como punto de partida. Debe seguir los pasos de este [tutorial](mobile-engagement-unity-roll-a-ball.md) antes de continuar con la integración de Mobile Engagement que se muestra en el tutorial siguiente.

Este tutorial requiere lo siguiente:

+ [Editor de Unity](http://unity3d.com/get-unity)
+ [SDK de Unity para Mobile Engagement](https://aka.ms/azmeunitysdk)
+ SDK de Google Android

> [AZURE.NOTE] Para completar este tutorial, deberá tener una cuenta de Azure activa. En caso de no tener ninguna, puede crear una cuenta de evaluación gratuita en tan solo unos minutos. Para obtener más información, consulte [Evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fes-ES%2Fdocumentation%2Farticles%2Fmobile-engagement-unity-android-get-started).

##<a id="setup-azme"></a>Configuración de Mobile Engagement para una aplicación Android

[AZURE.INCLUDE [Creación de la aplicación de Mobile Engagement en el portal](../../includes/mobile-engagement-create-app-in-portal.md)]

##<a id="connecting-app"></a>Conectar la aplicación al backend de Mobile Engagement

###Importación del paquete de Unity

1. Descargue el [paquete de Unity para Mobile Engagement](https://aka.ms/azmeunitysdk) y guárdelo en el equipo local. 

2. Vaya a **Assets -> Import Package -> Custom Package** (Recursos -> Importar paquete -> Paquete personalizado) y seleccione el paquete que descargó en el paso anterior.

	![][70]

3. Asegúrese de que se seleccionan todos los archivos y haga clic en el botón **Import** (Importar).

	![][71]

4. Una vez que la importación termina correctamente, verá los archivos del SDK importados en el proyecto.

	![][72]

###Actualización de EngagementConfiguration

1. Abra el archivo de script **EngagementConfiguration** desde la carpeta del SDK y actualice **ANDROID\_CONNECTION\_STRING** con la cadena de conexión que obtuvo anteriormente desde el Portal de Azure.  

	![][73]

2. Guarde el archivo.

3. Ejecute **File -> Engagement -> Generate Android Manifest** (Archivo -> Engagement - > Generar manifiesto Android). Se trata del complemento agregado por el SDK de Mobile Engagement; al hacer clic en él se actualizará automáticamente la configuración del proyecto.

	![][74]

> [AZURE.IMPORTANT] Asegúrese de ejecutarlo cada vez que actualice el archivo **EngagementConfiguration**; de lo contrario, los cambios no se reflejarán en la aplicación.

###Configuración de la aplicación para un seguimiento básico

1. Abra el script **Playercontroller** asociado al objeto Player para su edición. 

2. Agregue la siguiente instrucción using:

		using Microsoft.Azure.Engagement.Unity;

3. Agregue lo siguiente al método `Start()`.
    
        EngagementAgent.Initialize();
        EngagementAgent.StartActivity("Home");

###Implementación y ejecución de la aplicación
Asegúrese de tener instalado el SDK de Android en su equipo antes de intentar implementar esta aplicación Unity en su dispositivo.

1. Conecte un dispositivo Android al equipo. 

2. Abra **File -> Build Settings** (Archivo -> Configuración de compilación).

	![][40]

3. Seleccione **Android** y, a continuación, haga clic en **Switch Platform** (Cambiar plataforma).

	![][51]

	![][52]

4. Haga clic en **Player settings** (Configuración del Reproductor) y proporcione un identificador de agrupación de trabajos válido.

	![][53]

5. Por último, haga clic en **Build And Run** (Compilar y ejecutar).

	![][54]

6. Se le pedirá que proporcione un nombre de carpeta para almacenar el paquete de Android.

7. Si todo va bien, el paquete se implementará en el dispositivo conectado en su dispositivo conectado y con eso debería ver ya el juego de Unity en el teléfono.

##<a id="monitor"></a>Conexión de la aplicación con la supervisión en tiempo real

[AZURE.INCLUDE [Conectar la aplicación con la supervisión en tiempo real](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>Habilitación de las notificaciones push y la mensajería en aplicación

[AZURE.INCLUDE [Habilitación del servicio de mensajería en la nube de Google](../../includes/mobile-engagement-enable-google-cloud-messaging.md)]

###Actualización de EngagementConfiguration

1. Abra el archivo de script **EngagementConfiguration** desde la carpeta del SDK y actualice **ANDROID\_GOOGLE\_NUMBER** con el **número de proyecto de Google** que obtuvo anteriormente en el portal Google Cloud Developer (desarrollador en la nube de Google). Se trata de un valor de cadena, así que asegúrese de encerrarlo entre comillas dobles. 

	![][75]

2. Guarde el archivo .

3. Ejecute **File -> Engagement -> Generate Android Manifest** (Archivo -> Engagement - > Generar manifiesto Android). Se trata del complemento agregado por el SDK de Mobile Engagement; al hacer clic en él se actualizará automáticamente la configuración del proyecto.

	![][74]

###Configuración de la aplicación para recibir notificaciones

1. Abra el script **Playercontroller** asociado al objeto Player para su edición. 

2. Agregue lo siguiente al método `Start()`.

		EngagementReachAgent.Initialize();

3. Ahora que se ha actualizado la aplicación, implemente y ejecute la aplicación en un dispositivo de acuerdo con las instrucciones que se indican a continuación.

[AZURE.INCLUDE [Envío de notificaciones desde el portal](../../includes/mobile-engagement-android-send-push-from-portal.md)]

<!-- Images -->
[40]: ./media/mobile-engagement-unity-android-get-started/40.png
[70]: ./media/mobile-engagement-unity-android-get-started/70.png
[71]: ./media/mobile-engagement-unity-android-get-started/71.png
[72]: ./media/mobile-engagement-unity-android-get-started/72.png
[73]: ./media/mobile-engagement-unity-android-get-started/73.png
[74]: ./media/mobile-engagement-unity-android-get-started/74.png
[75]: ./media/mobile-engagement-unity-android-get-started/75.png
[51]: ./media/mobile-engagement-unity-android-get-started/51.png
[52]: ./media/mobile-engagement-unity-android-get-started/52.png
[53]: ./media/mobile-engagement-unity-android-get-started/53.png
[54]: ./media/mobile-engagement-unity-android-get-started/54.png

<!---HONumber=AcomDC_0330_2016-->