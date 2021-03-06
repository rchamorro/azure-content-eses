<properties 
	pageTitle="Un paseo por Analytics de Application Insights | Microsoft Azure" 
	description="Ejemplos breves de todas las principales consultas de Analytics, la potente herramienta de búsqueda de Application Insights." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/29/2016" 
	ms.author="awills"/>


 
# Un paseo por Analytics de Application Insights


[Analytics](app-insights-analytics.md) es la característica de búsqueda eficaz de [Application Insights](app-insights-overview.md). En estas páginas se describe el lenguaje de consulta de Analytics.


* **[Ver el vídeo de introducción](https://applicationanalytics-media.azureedge.net/home_page_video.mp4)**.
* **[Probar Analytics en nuestros datos simulados](https://analytics.applicationinsights.io/demo)** si su aplicación aún no envía datos a Application Insights.


Recorramos algunas preguntas básicas para comenzar.

## Conexión a los datos de Application Insights

Abra Analytics desde la [hoja de información general](app-insights-dashboards.md) de su instancia de Application Insights:

![Abra portal.azure.com, abra su recurso de Application Insights y haga clic en Análisis.](./media/app-insights-analytics-tour/001.png)

	
## [Take](app-insights-analytics-reference.md#take-operator): mostrarme n filas

Los puntos de datos que registran las operaciones de usuario (normalmente solicitudes HTTP recibidas mediante la aplicación web) se almacenan en una tabla denominada `requests`. Cada fila es un punto de datos de telemetría procedente del SDK de Application Insights en una aplicación.

Comencemos examinando algunas filas de ejemplo de la tabla:

![results](./media/app-insights-analytics-tour/010.png)

> [AZURE.NOTE] Coloque el cursor en algún lugar de la instrucción antes de hacer clic en Go (Ir). Puede dividir una instrucción en varias líneas, pero no incluya líneas en blanco en una instrucción. Las líneas en blanco son una forma práctica de tener varias consultas diferentes en la ventana.


Elija las columnas y ajuste sus posiciones:

![Hacer clic en la selección de columna en la parte superior derecha de los resultados](./media/app-insights-analytics-tour/030.png)


Expanda cualquier elemento para ver los detalles:
 
![Seleccione Table (Tabla) y use Configure Columns (Configurar columnas).](./media/app-insights-analytics-tour/040.png)

> [AZURE.NOTE] Haga clic en el encabezado de una columna para cambiar el orden de los resultados disponibles en el explorador web. Tenga en cuenta que, para un conjunto grande de resultados, el número de filas que se descargan en el explorador es limitado. Recuerde que esta forma de ordenación no siempre muestra los elementos mayores o menores reales. Para ello, debería usar el operador `top` o `sort`.

## [Top](app-insights-analytics-reference.md#top-operator) y [sort](app-insights-analytics-reference.md#sort-operator)

`take` resulta útil para obtener un ejemplo rápido de un resultado, pero muestra filas de la tabla sin ningún orden determinado. Para obtener una vista ordenada, use `top` (para un ejemplo) o `sort` (toda la tabla).

Muéstrame las primeras n filas, ordenadas por una columna en particular:

```AIQL

	requests | top 10 by timestamp desc 
```

* *Sintaxis:* la mayoría de los operadores tienen parámetros de palabra clave como `by`.
* `desc` = orden descendente, `asc` = orden ascendente.

![](./media/app-insights-analytics-tour/260.png)

`top...` es una manera más eficiente de decir `sort ... | take...`. Podríamos haber escrito:

```AIQL

	requests | sort by timestamp desc | take 10
```

El resultado sería el mismo, pero se ejecutaría un poco más lento. (También podría escribir `order`, que es un alias de `sort`).

Los encabezados de columna en la vista de tabla también pueden utilizarse para ordenar los resultados en la pantalla. Pero por supuesto, si ha usado `take` o `top` para recuperar solo parte de una tabla, solamente reordenará los registros que ha recuperado.


## [Project](app-insights-analytics-reference.md#project-operator): seleccionar, cambiar nombre y calcular columnas

Use [`project`](app-insights-analytics-reference.md#project-operator) para seleccionar solamente las columnas que desea:

```AIQL

    requests | top 10 by timestamp desc
             | project timestamp, name, resultCode
```

![](./media/app-insights-analytics-tour/240.png)


También puede cambiar el nombre de las columnas y definir otras nuevas:

```AIQL

    requests 
    | top 10 by timestamp desc 
    | project  
            name, 
            response = resultCode,
            timestamp, 
            ['time of day'] = floor(timestamp % 1d, 1s)
```

![result](./media/app-insights-analytics-tour/270.png)

* Los [nombres de columna](app-insights-analytics-reference.md#names) pueden incluir espacios o símbolos si están entre corchetes: `['...']` o `["..."]`.
* `%` es el operador de módulo habitual.
* `1d` (es decir, el dígito uno y luego una "d") es un literal de intervalo de tiempo que significa un día. Aquí se indican otros literales de intervalo de tiempo: `12h`, `30m`, `10s` y `0.01s`.
* `floor` (alias `bin`) redondea un valor a la baja al múltiplo más cercano del valor base que proporciona. Por lo tanto, `floor(aTime, 1s)` redondea un tiempo a la baja al segundo más cercano.

Las [expresiones](app-insights-analytics-reference.md#scalars) pueden incluir todos los operadores habituales (`+`, `-`, etc.) y hay una amplia gama de funciones útiles.

    

## [Extend](app-insights-analytics-reference.md#extend-operator): columnas de cálculo

Si solo desea agregar columnas a las ya existentes, use [`extend`](app-insights-analytics-reference.md#extend-operator):

```AIQL

    requests 
    | top 10 by timestamp desc
    | extend timeOfDay = floor(timestamp % 1d, 1s)
```

El uso de [`extend`](app-insights-analytics-reference.md#extend-operator) es menos detallado que [`project`](app-insights-analytics-reference.md#project-operator) si desea conservar todas las columnas existentes.


## Acceso a objetos anidados

Se puede acceder fácilmente a los objetos anidados. Por ejemplo, en la transmisión de excepciones verá objetos estructurados así:

![result](./media/app-insights-analytics-tour/520.png)

Es posible aplanarlo si selecciona las propiedades que le interesan:

```AIQL

    exceptions | take 10
    | extend method1 = tostring(details[0].parsedStack[1].method)
```

Tenga en cuenta que debe utilizar una [conversión](app-insights-analytics-reference.md#casts) al tipo adecuado.

## Propiedades y medidas personalizadas

Si la aplicación adjunta [dimensiones personalizadas (propiedades) y medidas personalizadas](app-insights-api-custom-events-metrics.md#properties) a eventos, las verá en los objetos `customDimensions` y `customMeasurements`.


Por ejemplo, si la aplicación incluye:

```C#

    var dimensions = new Dictionary<string, string> 
                     {{"p1", "v1"},{"p2", "v2"}};
    var measurements = new Dictionary<string, double>
                     {{"m1", 42.0}, {"m2", 43.2}};
	telemetryClient.TrackEvent("myEvent", dimensions, measurements);
```

Para extraer estos valores en Analytics:

```AIQL

    customEvents
    | extend p1 = customDimensions.p1, 
      m1 = todouble(customMeasurements.m1) // cast to expected type

``` 

> [AZURE.NOTE] En el [Explorador de métricas](app-insights-metrics-explorer.md), todas las medidas personalizadas adjuntas a cualquier tipo de telemetría aparecen juntas en la hoja de métricas junto con las métricas enviadas mediante `TrackMetric()`. Sin embargo, en Analytics las medidas personalizadas siguen adjuntas al tipo de telemetría donde se realizaron y las métricas aparecen en su propia transmisión `metrics`.


## [Summarize](app-insights-analytics-reference.md#summarize-operator): adición de grupos de filas

`Summarize` aplica una *función de agregación* específica sobre grupos de filas.

Por ejemplo, el tiempo que su aplicación web tarda en responder a una solicitud se notifica en el campo `duration`. Veamos el tiempo medio de respuesta de todas las solicitudes:

![](./media/app-insights-analytics-tour/410.png)

O bien, podríamos separar el resultado en solicitudes de nombres diferentes:


![](./media/app-insights-analytics-tour/420.png)

`Summarize` recopila los puntos de datos de la transmisión en grupos que la cláusula `by` calcula por igual. Cada valor de la expresión `by` (cada nombre de la operación en el ejemplo anterior) da como resultado una fila de la tabla de resultados.

O bien, podríamos agrupar los resultados en función de la hora del día:

![](./media/app-insights-analytics-tour/430.png)

Observe cómo usamos la función `bin` (también conocida como "`floor`"). Si solo usáramos `by timestamp`, cada fila de entrada terminaría en su pequeño grupo. Para cualquier escalar continuo, como horas o números, tenemos que dividir el intervalo continuo en un número manejable de valores discretos y `bin`, que realmente es solo la función `floor` de redondeo a la baja, es la forma más sencilla de hacerlo.

Podemos usar la misma técnica para reducir los intervalos de cadenas:


![](./media/app-insights-analytics-tour/440.png)

Tenga en cuenta que puede usar `name=` para establecer el nombre de una columna de resultados, en las expresiones de agregación o mediante la cláusula by.

## Recuento de datos muestreados

`sum(itemCount)` es la agregación recomendada para contar eventos. En muchos casos, itemCount == 1, por lo que la función simplemente cuenta el número de filas del grupo. Pero cuando el [muestreo](app-insights-sampling.md) está en funcionamiento, solo se retendrá una fracción de los eventos originales como puntos de datos en Application Insights, por lo que para cada punto de datos que vea, existen eventos `itemCount`.

Por ejemplo, si el muestreo descarta el 75 % de los eventos originales, itemCount ==4 en los registros retenidos; es decir, para cada registro retenido, había cuatro registros originales.

El muestreo adaptable hace que itemCount sea mayor durante los períodos en que la aplicación se utiliza más.

Por lo tanto, resumir itemCount proporciona una buena estimación del número original de eventos.


![](./media/app-insights-analytics-tour/510.png)

También existe una agregación `count()` (y una operación de recuento) para los casos en los que realmente desea contar el número de filas de un grupo.


Existe una gama de [funciones de agregación](app-insights-analytics-reference.md#aggregations).


## Crear gráficos de los resultados


```AIQL

    exceptions 
       | summarize count()  
         by bin(timestamp, 1d)
```

De forma predeterminada, los resultados se muestran como una tabla:

![](./media/app-insights-analytics-tour/225.png)


Podemos hacerlo mejor que la vista de la tabla. Echemos un vistazo a los resultados en la vista del gráfico con la opción de barra vertical:

![Haga clic Chart (Gráfico) y después elija Vertical bar chart (Gráfico de barras verticales) y asigne los ejes x e y.](./media/app-insights-analytics-tour/230.png)

Tenga en cuenta que aunque no ordenamos los resultados por tiempo (como puede ver en la visualización de la tabla), la visualización del gráfico siempre muestra las fechas en el orden correcto.


## [Where](app-insights-analytics-reference.md#where-operator): filtrado de una condición.

Si ha configurado la supervisión de Application Insights tanto para el lado [cliente](app-insights-javascript.md) como para el lado servidor de la aplicación, parte de la telemetría de la base de datos procede de los exploradores.

Veamos solo las excepciones de las que informan los exploradores:

```AIQL

    exceptions 
    | where device_Id == "browser" 
    |  summarize count() 
       by device_BrowserVersion, outerExceptionMessage 
```

![](./media/app-insights-analytics-tour/250.png)

El operador `where` acepta una expresión booleana. He aquí algunos puntos clave:

 * `and`, `or`: Operadores booleanos
 * `==`, `<>`: igual que y no igual que
 * `=~`, `!=`: cadena que no distingue entre mayúsculas y minúsculas igual que y no igual que. Hay muchos más operadores de comparación de cadenas.

Lea toda la información sobre las [expresiones escalares](app-insights-analytics-reference.md#scalars).

### Filtrado de eventos

Buscar solicitudes incorrectas:

```AIQL

    requests 
    | where isnotempty(resultCode) and toint(resultCode) >= 400
```

`responseCode` tiene una cadena de tipo, por lo que debemos [convertirlo](app-insights-analytics-reference.md#casts) para una comparación numérica.

Resumir las diferentes respuestas:

```AIQL

    requests
    | where isnotempty(resultCode) and toint(resultCode) >= 400
    | summarize count() 
      by resultCode
```

## Gráficos de tiempo

Mostrar cuántos eventos hay cada día:

```AIQL

    requests
      | summarize event_count=count()
        by bin(timestamp, 1d)
```

Seleccionar la opción de visualización de gráfico:

![timechart](./media/app-insights-analytics-tour/080.png)

El eje x para los gráficos de líneas debe ser de tipo DateTime.

## Varias series 

Use varios valores en una cláusula `summarize by` para crear una fila independiente para cada combinación de valores:

```AIQL

    requests 
      | summarize event_count=count()   
        by bin(timestamp, 1d), client_StateOrProvince
```

![](./media/app-insights-analytics-tour/090.png)

Para mostrar varias líneas en un gráfico, haga clic en **Split by** (Dividir por) y elija una columna.

![](./media/app-insights-analytics-tour/100.png)



## Ciclo medio diario

¿Cómo varía el uso a lo largo del día normal?

Contar solicitudes por el módulo de tiempo un día, discretizadas en horas:

```AIQL

    requests
    | extend hour = floor(timestamp % 1d , 1h) 
          + datetime("2016-01-01")
    | summarize event_count=count() by hour
```

![Gráfico de líneas de horas en un día normal](./media/app-insights-analytics-tour/120.png)

>[AZURE.NOTE] Observe que actualmente tenemos convertir las duraciones de tiempo en fechas y horas para mostrar en el gráfico.


## Comparación de varias series diarias

¿Cómo varía el uso a lo largo de la hora del día en distintos estados?

```AIQL
    requests
     | extend hour= floor( timestamp % 1d , 1h)
           + datetime("2001-01-01")
     | summarize event_count=count() 
       by hour, client_StateOrProvince
```

Dividir el gráfico por estado:

![Dividir por client\_StateOrProvince](./media/app-insights-analytics-tour/130.png)


## Trazado de una distribución

¿Cuántas sesiones existen de longitudes diferentes?

```AIQL

    requests 
    | where isnotnull(session_Id) and isnotempty(session_Id) 
    | summarize min(timestamp), max(timestamp) 
      by session_Id 
    | extend sessionDuration = max_timestamp - min_timestamp 
    | where sessionDuration > 1s and sessionDuration < 3m 
    | summarize count() by floor(sessionDuration, 3s) 
    | project d = sessionDuration + datetime("2016-01-01"), count_
```

La última línea es necesaria para convertir a fecha y hora (actualmente, el eje x de un gráfico de líneas solo puede ser una fecha y hora).

La cláusula `where` excluye las sesiones únicas (sessionDuration==0) y establece la longitud del eje X.


![](./media/app-insights-analytics-tour/290.png)



## [Percentiles](app-insights-analytics-reference.md#percentiles)

¿Qué intervalos de duraciones cubren diferentes porcentajes de sesiones?

Utilice la consulta anterior, pero reemplace la última línea:

```AIQL

    requests 
    | where isnotnull(session_Id) and isnotempty(session_Id) 
    | summarize min(timestamp), max(timestamp) 
      by session_Id 
    | extend sesh = max_timestamp - min_timestamp 
    | where sesh > 1s
    | summarize count() by floor(sesh, 3s) 
    | summarize percentiles(sesh, 5, 20, 50, 80, 95)
```

También eliminamos el límite superior en la cláusula where con el fin de obtener cifras correctas, incluidas las sesiones con más de una solicitud:

![result](./media/app-insights-analytics-tour/180.png)

De lo que podemos ver que:

* El 5 % de las sesiones tienen una duración de menos de 3 minutos 34 s;
* El 50 % de las sesiones dura menos de 36 minutos;
* El 5 % de las sesiones dura más de 7 días.

Para obtener un desglose independiente para cada país, simplemente tiene que conservar la columna client\_CountryOrRegion por separado en ambos operadores de resumen:

```AIQL

    requests 
    | where isnotnull(session_Id) and isnotempty(session_Id) 
    | summarize min(timestamp), max(timestamp) 
      by session_Id, client_CountryOrRegion
    | extend sesh = max_timestamp - min_timestamp 
    | where sesh > 1s
    | summarize count() by floor(sesh, 3s), client_CountryOrRegion
    | summarize percentiles(sesh, 5, 20, 50, 80, 95)
	  by client_CountryOrRegion
```

![](./media/app-insights-analytics-tour/190.png)


## [Join](app-insights-analytics-reference.md#join)

Tenemos acceso a varias tablas, incluidas las solicitudes y las excepciones.

Para encontrar las excepciones relacionadas con una solicitud que devolvió una respuesta de error, podemos combinar las tablas en `session_Id`:

```AIQL

    requests 
    | where toint(responseCode) >= 500 
    | join (exceptions) on operation_Id 
    | take 30
```


Es recomendable usar `project` para seleccionar solo las columnas que se necesitan antes de realizar la combinación. En las mismas cláusulas, cambiamos el nombre de la columna de marca de tiempo.



## [Let](app-insights-analytics-reference.md#let-clause): asignación de un resultado a una variable

Use [let](./app-insights-analytics-reference.md#let-statements) para separar las partes de la expresión anterior. Los resultados no cambian:

```AIQL

    let bad_requests = 
      requests
        | where  toint(resultCode) >= 500  ;
    bad_requests
    | join (exceptions) on session_Id 
    | take 30
```

> Sugerencia: en el cliente de Analytics, no incluya líneas en blanco entre las partes. Asegúrese de ejecutar todo.


* **[Probar Analytics en nuestros datos simulados](https://analytics.applicationinsights.io/demo)** si su aplicación aún no envía datos a Application Insights.


[AZURE.INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]

<!---HONumber=AcomDC_0803_2016-->