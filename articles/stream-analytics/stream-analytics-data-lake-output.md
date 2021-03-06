<properties
	pageTitle="Salida de los Análisis de transmisiones del Almacén de Data Lake | Microsoft Azure"
	description="Configuración de la autenticación y la autorización de un Almacén de Azure Data Lake en un trabajo de Análisis de transmisiones"
	keywords=""
	services="stream-analytics"
	documentationCenter=""
	authors="jeffstokes72"
	manager="paulettm"
	editor="cgronlun"
/>

<tags
	ms.service="stream-analytics"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="big-data"
	ms.date="07/27/2016"
	ms.author="jeffstok"
/>

# Salida de los Análisis de transmisiones del Almacén de Data Lake

Los trabajos de Análisis de transmisiones admiten varios métodos de salida, y uno de ellos es el [Almacén de Azure Data Lake](https://azure.microsoft.com/services/data-lake-store/). El Almacén de Azure Data Lake es un repositorio de gran escala en toda la empresa para cargas de trabajo de análisis de macrodatos. El Almacén de Data Lake permite almacenar datos de cualquier tamaño, tipo y velocidad de ingesta para realizar análisis exploratorios y operativos. En este artículo se trata la autorización, la configuración y la renovación de la autorización de un Almacén de Azure Data Lake en los Análisis de transmisiones del Portal de Azure clásico.

> [AZURE.NOTE] En este momento, la creación y la configuración de salidas del Almacén de Data Lake **solo** se admiten en el Portal de Azure clásico.

## Autorización de una cuenta del Almacén de Data Lake

1.  Cuando se selecciona el Almacén de Data Lake como una salida en el Portal de administración de Azure, se le pedirá que autorice el uso de su Almacén de Data Lake existente o pida acceso a la vista previa del Almacén de Data Lake mediante el Portal de Azure clásico.

    ![](media/stream-analytics-data-lake-output/stream-analytics-data-lake-output-authorization.jpg)

2.  Si ya dispone de acceso al Almacén de Data Lake, haga clic en “Autorizar ahora” y, durante unos segundos, se mostrará una página con el mensaje “Redirigiendo a la autorización...”. Esta página se cerrará automáticamente y se le mostrará la página donde podrá configurar la salida del Almacén de Data Lake.

Si no se ha registrado para la vista previa del Almacén de Data Lake, puede hacer clic en el vínculo “Regístrese ahora” para iniciar la solicitud, o bien siga las [instrucciones de introducción](../data-lake-store/data-lake-store-get-started-portal.md).

## Configuración de las propiedades de salida del Almacén de Data Lake

Una vez que se haya autenticado la cuenta del Almacén de Data Lake, podrá configurar las propiedades para su salida del Almacén de Data Lake. En la siguiente tabla podrá encontrar una lista de nombres de propiedades y su descripción para configurar la salida del Almacén de Data Lake.

<table>
<tbody>
<tr>
<td><B>NOMBRE DE PROPIEDAD</B></td>
<td><B>DESCRIPCIÓN</B></td>
</tr>
<tr>
<td>Alias de salida</td>
<td>Se trata de un nombre descriptivo utilizado en las consultas para dirigir la salida de la consulta a este Almacén de Data Lake.</td>
</tr>
<tr>
<td>Cuenta del Almacén de Data Lake</td>
<td>El nombre de la cuenta de almacenamiento a donde está enviando la salida Se le mostrará una lista desplegable de cuentas del Almacén de Data Lake a las que el usuario cuya sesión se ha iniciado en el portal tiene acceso.</td>
</tr>
<tr>
<td>Patrón del prefijo de la ruta de acceso [<I>opcional</I>]</td>
<td>La ruta de acceso utilizada para escribir sus archivos en la cuenta del Almacén de Data Lake especificada. <BR>{date}, {time}<BR>Ejemplo 1: carpeta1/registros/{date}/{time}<BR>Ejemplo 2: carpeta1/registros/{date}</td>
</tr>
<tr>
<td>Formato de fecha [<I>opcional</I>]</td>
<td>Si el token de fecha se usa en la ruta de acceso de prefijo, puede seleccionar el formato de fecha en el que se organizan los archivos. Ejemplo: AAAA/MM/DD</td>
</tr>
<tr>
<td>Formato de hora [<I>opcional</I>]</td>
<td>Si el token de hora se usa en la ruta de acceso de prefijo, puede seleccionar el formato de hora en el que se organizan los archivos. Actualmente, el único valor admitido es HH.</td>
</tr>
<tr>
<td>Formato de serialización de eventos</td>
<td>Formato de serialización para los datos de salida. Se admiten JSON, CSV y Avro.</td>
</tr>
<tr>
<td>Codificación</td>
<td>Si el formato CSV o JSON, debe especificarse una codificación. Por el momento, UTF-8 es el único formato de codificación compatible.</td>
</tr>
<tr>
<td>Delimitador</td>
<td>Solo se aplica para la serialización de CSV. Análisis de transmisiones admite un número de delimitadores comunes para la serialización de datos CSV. Los valores admitidos son la coma, punto y coma, espacio, tabulador y barra vertical.</td>
</tr>
<tr>
<td>Formato</td>
<td>Solo se aplica para la serialización de JSON. Separado por líneas especifica que la salida se formateará de tal forma que cada objeto JSON esté separado por una línea nueva. Matriz especifica que la salida se formateará como una matriz de objetos JSON.</td>
</tr>
</tbody>
</table>

## Renovación de la autorización del Almacén de Data Lake

Actualmente, existe una limitación según la que el token de autenticación debe actualizarse manualmente cada 90 días para todos los trabajos con salida del Almacén de Data Lake. También tendrá que volver a autenticar la cuenta del Almacén de Data Lake si ha cambiado la contraseña desde que se creó o autenticó por última vez su trabajo. Un síntoma de este problema es la ausencia de salidas de trabajos y un error en los registros de operaciones en el que se indica que se debe volver a conceder la autorización.

Para resolver este problema, detenga su trabajo en ejecución y vaya a la salida del Almacén de Data Lake. Haga clic en el vínculo “Renovar autorización” y, durante unos segundos, se mostrará una página con el mensaje “Redirigiendo a la autorización...”. La página se cerrará automáticamente y, si la operación se realiza correctamente, indicará “La autorización se ha renovado correctamente”. Después, debe hacer clic en “Guardar” en la parte inferior de la página y podrá continuar reiniciando el trabajo desde la hora de la última detención para evitar la pérdida de datos.

![](media/stream-analytics-data-lake-output/stream-analytics-data-lake-output-renew-authorization.jpg)

<!---HONumber=AcomDC_0727_2016-->