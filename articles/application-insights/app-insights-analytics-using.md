<properties 
	pageTitle="Uso de Analytics: la herramienta de búsqueda eficaz de Application Insights | Microsoft Azure" 
	description="Uso de Analytics, la eficaz herramienta de búsqueda de Application Insights. " 
	services="application-insights" 
    documentationCenter=""
	authors="danhadari" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/15/2016" 
	ms.author="danha"/>


# Uso de Analytics en Application Insights


[Analytics](app-insights-analytics.md) es la eficaz característica de búsqueda de [Application Insights](app-insights-overview.md). En estas páginas se describe el lenguaje de consulta de Analytics.

* **[Ver el vídeo de introducción](https://applicationanalytics-media.azureedge.net/home_page_video.mp4)**.
* **[Probar Analytics en nuestros datos simulados](https://analytics.applicationinsights.io/demo)** si su aplicación aún no envía datos a Application Insights.

## Apertura de Analytics

En el recurso de inicio de su aplicación en Application Insights, haga clic en Analytics. p ![Abra portal.azure.com, abra su recurso de Application Insights y haga clic en Análisis.](./media/app-insights-analytics-using/001.png)

El tutorial en línea le dará algunas ideas sobre lo que puede hacer.

Podrá encontrar un [paseo más amplio aquí](app-insights-analytics-tour.md).

## Consulta de la telemetría

### Escriba una consulta.

![](./media/app-insights-analytics-using/150.png)

Comience con los nombres de cualquiera de las tablas que aparecen a la izquierda (o los operadores [range](app-insights-analytics-reference.md#range-operator) o [union](app-insights-analytics-reference.md#union-operator)). Use `|` para crear una canalización de [operadores](app-insights-analytics-reference.md#queries-and-operators). IntelliSense le indicará los operadores y algunos de los elementos de la expresión que se puede utilizar.

Consulte la [información general del lenguaje Analytics](app-insights-analytics-tour.md) y la [referencia del lenguaje](app-insights-analytics-reference.md).

### Ejecución de una consulta

![Ejecución de una consulta](./media/app-insights-analytics-using/130.png)

1. Puede utilizar saltos de línea sencillos en las consultas.
2. Coloque el cursor dentro o al final de la consulta que desea ejecutar.
3. Haga clic en Ir para ejecutar la consulta.
4. No ponga líneas en blanco en la consulta. Puede mantener varias consultas separadas en una pestaña de consulta; para ello, sepárelas con líneas en blanco. Solo se ejecutará la que tiene el cursor.

### Almacenamiento de una consulta

![Almacenamiento de una consulta](./media/app-insights-analytics-using/140.png)

1. Guarde el archivo de consulta actual.
2. Abra un archivo de consulta guardado.
3. Cree un archivo de consulta.


## Visualización de los detalles

Expanda cualquier fila de los resultados para ver la lista completa de sus propiedades. Puede expandir más cualquier propiedad que tenga un valor estructurado: por ejemplo, las dimensiones personalizadas o la pila indicada en una excepción.

![Expansión de una fila](./media/app-insights-analytics-using/070.png)

 

## Disposición de los resultados

Puede ordenar, filtrar, paginar y agrupar los resultados devueltos desde la consulta.

> [AZURE.NOTE] Ordenar, agrupar y filtrar en el explorador no vuelve a ejecutar la consulta. Solo vuelve a ordenar los resultados devueltos por la última consulta.
> 
> Para realizar estas tareas en el servidor antes de que se devuelvan los resultados, escriba la consulta con los operadores [sort](app-insights-analytics-reference.md#sort-operator), [summarize](app-insights-analytics-reference.md#summarize-operator) y [where](app-insights-analytics-reference.md#where-operator).

Elija las columnas que desea ver, arrastre los encabezados de columna para reorganizarlos y cambie el tamaño de las columnas arrastrando sus bordes.

![Organización de columnas](./media/app-insights-analytics-using/030.png)

### Ordenación y filtrado de elementos

Haga clic en el encabezado de una columna para ordenar los resultados. Vuelva a hacer clic para ordenar de otra manera, y haga clic una tercera vez para volver a la ordenación original devuelta por la consulta.

Utilice el icono de filtro para restringir la búsqueda.

![Ordenación y filtrado de columnas](./media/app-insights-analytics-using/040.png)



### Agrupación de elementos

Para ordenar por más de una columna, use la agrupación. Primero, habilítela y, después, arrastre los encabezados de columna en el espacio por encima de la tabla.

![Grupo](./media/app-insights-analytics-using/060.png)



### ¿Faltan algunos resultados?

Hay un límite de 10 000 filas en los resultados devueltos desde el portal. Se mostrará una advertencia si sobrepasa el límite. Si esto sucede, al ordenar los resultados de la tabla no siempre mostrará todos los resultados primeros o últimos reales.

Es recomendable evitar llegar al límite. Utilice operadores como:

* [where timestamp > ago(3d)](app-insights-analytics-reference.md#where-operator)
* [top 100 by timestamp](app-insights-analytics-reference.md#top-operator)
* [take 100](app-insights-analytics-reference.md#take-operator)
* [summarize](app-insights-analytics-reference.md#summarize-operator)



## Diagramas

Seleccione el tipo de diagrama que desea:

![](./media/app-insights-analytics-using/230.png)

Si tiene varias columnas de los tipos correctos, puede elegir los ejes X e Y, así como una columna de dimensiones para dividir los resultados.

De manera predeterminada, los resultados se muestran en un principio en forma de tabla y el diagrama se selecciona manualmente. Sin embargo, para seleccionar un diagrama se puede usar la [directiva render](app-insights-analytics-reference.md#render-directive) al final de una consulta.

Puede anclar un diagrama a uno de sus [paneles compartidos](app-insights-dashboards.md); simplemente haga clic en la chincheta. (Solo disponible para las aplicaciones en un plan de tarifa de pago).

## Exportación a Excel

Una vez que haya ejecutado una consulta, puede descargar un archivo .csv. Haga clic en **Export, to Excel** (Exportar, a Excel).

## Exportación a Power BI

1. Coloque el cursor en una consulta y elija **Export to Power BI** (Exportación a Power BI).

    ![](./media/app-insights-analytics-using/240.png)

    Esta operación descarga un archivo de script de M.

3. Copie el script del lenguaje M en el editor de consultas avanzadas de Power BI Desktop.
 * Abra el archivo exportado.
 * En Power BI Desktop seleccione: **Obtener datos, Consulta en blanco, Editor avanzado** y pegue el script de lenguaje M.

    ![](./media/app-insights-analytics-using/250.png)

4. Edite las credenciales, en caso de que sea necesario, y podrá generar el informe.

    ![](./media/app-insights-analytics-using/260.png)




[AZURE.INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]

<!---HONumber=AcomDC_0727_2016-->