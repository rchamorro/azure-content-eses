<properties
	pageTitle="Análisis de tendencias en Visual Studio | Microsoft Azure"
	description="Analizar, mostrar y explorar las tendencias de la telemetría de Application Insights en Visual Studio."
	services="application-insights"
    documentationCenter=".net"
	authors="numberbycolors"
	manager="douge"/>

<tags
	ms.service="application-insights"
	ms.workload="tbd"
	ms.tgt_pltfrm="ibiza"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="07/14/2016"
	ms.author="daviste"/>

# Análisis de tendencias en Visual Studio

La herramienta Tendencias de Application Insights muestra cómo los eventos de telemetría de la aplicación importantes cambian a lo largo del tiempo, lo que ayuda a identificar rápidamente los problemas y las anomalías. Al vincularle a información de diagnóstico más detallada, Tendencias puede ayudarle a mejorar el rendimiento de la aplicación, realizar un seguimiento de las causas de las excepciones y revelar información de los eventos personalizados.

![Ejemplo de la ventana de tendencias](./media/app-insights-trends/app-insights-trends-hero-750.png)

> [AZURE.NOTE] Tendencias de Application Insights está disponible en Visual Studio 2015 Update 3 y posterior, o bien con la [extensión Developer Analytics Tools](https://visualstudiogallery.msdn.microsoft.com/82367b81-3f97-4de1-bbf1-eaf52ddc635a), versión 5.209 y posteriores.

## Tendencias de Application Insights

Puede abrir la ventana de Tendencias de Application Insights de alguna de las maneras siguientes:

* En el botón de la barra de herramientas de Application Insights, elija **Explorar tendencias de telemetría**.
* En el menú contextual del proyecto, elija **Application Insights > Explorar tendencias de telemetría**.
* En la barra de menús de Visual Studio, elija **Vista > Otras ventanas > Tendencias de Application Insights**.

Verá un aviso para seleccionar un recurso. Si es así, haga clic en **Seleccionar un recurso**, inicie sesión con una suscripción de Azure y elija el recurso de Application Insights para el que desea analizar las tendencias de telemetría.

## Elección de un análisis de tendencias

![Menú de tipos comunes de análisis de tendencias](./media/app-insights-trends/app-insights-trends-1-750.png)

Para empezar, elija entre uno de los cinco análisis de tendencias comunes, cada de los cuales analiza los datos de las últimas 24 horas:

* **Investigue los problemas de rendimiento con las solicitudes del servidor**: solicitudes realizadas al servicio, agrupadas por tiempos de respuesta
* **Analice los errores en las solicitudes del servidor**: solicitudes realizadas al servicio, agrupadas por código de respuesta HTTP
* **Examine las excepciones de la aplicación**: excepciones del servicio, agrupadas por tipo de excepción
* **Compruebe el rendimiento de las dependencias de la aplicación**: servicios llamados por su servicio, agrupados por tiempos de respuesta
* **Inspeccione sus eventos personalizados**: eventos personalizados que se han configurado para el servicio, agrupados por tipo de evento

Estos análisis pregenerados están disponibles más adelante, en el botón **Ver tipos comunes de análisis de telemetría**, situado en la esquina superior izquierda de la ventana Tendencias.

## Visualización de tendencias en la aplicación

Tendencias de Application Insights crea una visualización de la serie temporal o de la telemetría de su aplicación. Cada visualización de la serie temporal muestra un tipo de telemetría, agrupados por una propiedad de esa telemetría, a lo largo de un intervalo de tiempo específico.

Por ejemplo, puede ver las solicitudes del servidor, agrupadas por el país en el que se originaron, a lo largo de las últimas 24 horas. En este ejemplo, cada burbuja de la visualización representaría un recuento de las solicitudes del servidor para un país o región específico durante una hora.

Utilice los controles de la parte superior de la ventana para ajustar los tipos de telemetría que ve. En primer lugar, elija los tipos de telemetría en los que está interesado:

* **Tipo de telemetría**: solicitudes del servidor, excepciones, dependencias o eventos personalizados.
* **Intervalo de tiempo**: en cualquier momento entre los últimos 30 minutos y los últimos tres días.
* **Agrupar por**: tipo excepción, identificador del problema, país o región, etc.

A continuación, haga clic en **Analizar telemetría** para ejecutar la consulta.

Para desplazarse entre las burbujas de la visualización:

* Haga clic para seleccionar una burbuja; esto actualiza los filtros situados en la parte inferior de la ventana y resume los eventos que se produjeron durante un período específico.
* Haga doble clic en una burbuja para ir a la herramienta de búsqueda y vea todos los eventos de telemetría individuales que se produjeron durante ese período.
* Presione la tecla **Ctrl** y, a continuación, haga clic en una burbuja para anular su selección en la visualización.

> [AZURE.TIP] Las herramientas de tendencias y de búsqueda le ayudan a identificar los eventos de telemetría que están ocasionando problemas con el servicio. Por ejemplo, si los clientes observan que la aplicación tiene menos capacidad de respuesta una tarde, puede empezar a analizar el problema mediante Tendencias. Analice las solicitudes al servicio que se han realizado durante las últimas horas, agrupadas por tiempo de respuesta. Vea si hay un clúster de solicitudes lentas inusualmente grande. A continuación, haga doble clic en esa burbuja para ir a la herramienta Buscar, que dispone de un filtro para filtrar esos eventos de solicitud. En Buscar puede explorar el contenido de esas solicitudes y encontrar el código en cuestión para resolver el problema.

## Filtro de tendencias específicas

Detecte tendencias más específicas con los controles de filtro en la parte inferior de la ventana. Para aplicar un filtro, haga clic en su nombre. Puede cambiar rápidamente entre distintos filtros para detectar tendencias que pueden estar ocultas en una determinada dimensión de la telemetría. Si aplica un filtro en una dimensión, como, por ejemplo, la de Tipo de excepción, aún podrá hacer clic en los filtros de otras dimensiones, aunque aparezcan atenuados. Para dejar de aplicar un filtro, vuelva a hacer clic en él. Presione la tecla **Ctrl** y, después, haga clic para seleccionar varios filtros de la misma dimensión.

![Filtros de tendencia](./media/app-insights-trends/TrendsFiltering-750.png)

¿Qué ocurre si desea aplicar varios filtros?

1. Aplique el primer filtro.
2. Haga clic en el botón **Apply selected filters and query again** (Aplicar filtros seleccionados y volver a consultar) para usar el nombre de la dimensión de su primer filtro. Esto volverá a consultar la telemetría específicamente para los eventos que coinciden con el primer filtro.
3. Aplique un segundo filtro.
4. Repita el proceso para buscar tendencias en subconjuntos específicos de la telemetría. Por ejemplo, puede realizar una consulta para las solicitudes de servidor denominadas "GET Home/Index" que procedían de Alemania y que recibieron un código de estado 500.

Para dejar de aplicar uno de estos filtros, haga clic en el botón **Remove selected filters and query again** (Quitar filtros seleccionados y volver a consultar) de la dimensión.

![Varios filtros](./media/app-insights-trends/TrendsFiltering2-750.png)

## Búsqueda de anomalías

La herramienta Tendencias puede resaltar las burbujas de eventos anómalos en comparación con otras burbujas de la misma serie temporal. En la lista desplegable **Tipo de vista**, elija **Recuentos en el ciclo (resaltar anomalías)** o **Porcentajes en el ciclo (resaltar anomalías)**. Las burbujas rojas son anómalas.

Las anomalías se definen como burbujas con recuentos o porcentajes que superan 2,1 veces la desviación estándar de los recuentos o porcentajes y que se produjeron en los últimos dos períodos (por ejemplo, 48 horas si está viendo las últimas 24 horas).

![Los puntos de color indican anomalías](./media/app-insights-trends/TrendsAnomalies-750.png)

> [AZURE.TIP] El proceso de resaltar las anomalías puede ser especialmente útil para buscar valores atípicos en series temporales de pequeñas burbujas que, de lo contrario, pueden parecer de tamaño similar.

## <a name="next"></a>Pasos siguientes


[Trabajo con Application Insights en Visual Studio](app-insights-visual-studio.md)Busque la telemetría, consulte los datos de CodeLens y configure Application Insights, todo ello dentro de Visual Studio.

[Adición de más datos](app-insights-asp-net-more.md)Supervise el uso, la disponibilidad, las dependencias y las excepciones. Integrar seguimientos de marcos de registro. Escribir telemetría personalizada.

[Trabajo con el portal de Application Insights](app-insights-dashboards.md): paneles, eficaces herramientas de diagnóstico y análisis, alertas, un mapa activo de dependencias de la aplicación y exportación de la telemetría.

<!---HONumber=AcomDC_0810_2016-->