<properties 
	pageTitle="SDK y API para .NET de DocumentDB | Microsoft Azure" 
	description="Obtenga toda la información sobre el SDK y la API para .NET como, por ejemplo, fechas de lanzamiento, fechas de retirada y cambios de una versión a otra del SDK para .NET de DocumentDB." 
	services="documentdb" 
	documentationCenter=".net" 
	authors="rnagpal" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="08/09/2016" 
	ms.author="rnagpal"/>

# SDK y API de DocumentDB 

> [AZURE.SELECTOR]
- [.NET](documentdb-sdk-dotnet.md)
- [Node.js](documentdb-sdk-node.md)
- [Java](documentdb-sdk-java.md)
- [Python](documentdb-sdk-python.md)
- [REST](https://go.microsoft.com/fwlink/?LinkId=402413)
- [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)

## SDK y API para .NET de DocumentDB

<table>
<tr><td>**Descarga del SDK**</td><td>[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/)</td></tr>
<tr><td>**Documentación de la API**</td><td>[Documentación de referencia de la API para .NET](https://msdn.microsoft.com/library/azure/dn948556.aspx)</td></tr>
<tr><td>**Ejemplos**</td><td>[Ejemplos de código. NET](documentdb-dotnet-samples.md)</td></tr>
<tr><td>**Introducción**</td><td>[Introducción al SDK de .NET de DocumentDB] (documentdb-get-started.md)</td></tr>
<tr><td>**Tutorial de la aplicación web**</td><td>[Desarrollo de aplicaciones web con DocumentDB] (documentdb-dotnet-application.md)</td></tr>
<tr><td>**Plataforma admitida actualmente**</td><td>[Microsoft .NET Framework 4.5] (https://www.microsoft.com/download/details.aspx?id=30653)</td></tr>
</table></br>

## Notas de la versión

### <a name="1.9.2"/>[1\.9.2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.9.2)
> [AZURE.IMPORTANT] Puede recibir el error System.NotSupportedException al consultar colecciones con particiones. Para evitarlo, desactive la opción Preferencia de 32 bits en la ventana Propiedades del proyecto, que se encuentra en la pestaña Compilación.

  - Se ha agregado compatibilidad con las consultas paralelas en las colecciones particionadas.
  - Se ha agregado compatibilidad con las consultas ORDER BY y TOP entre particiones en las colecciones particionadas.
  - Se han corregido las referencias que faltaban en DocumentDB.Spatial.Sql.dll y Microsoft.Azure.Documents.ServiceInterop.dll, que se necesitan para hacer referencia a un proyecto de DocumentDB con una referencia al paquete NuGet de DocumentDB.
  - Se ha corregido la funcionalidad relacionada con el uso de parámetros de diferentes tipos al utilizar las funciones definidas por el usuario de LINQ.
  - Se ha corregido un error en las cuentas replicadas globalmente que provocaba que las llamadas de Upsert se dirigieran a ubicaciones de lectura y no de escritura.
  - Se han agregado a la interfaz IDocumentClient los métodos que faltaban:
      - El método UpsertAttachmentAsync que toma mediaStream y opciones como parámetros
      - El método CreateAttachmentAsync que toma opciones como un parámetro
      - El método CreateOfferQuery que toma querySpec como un parámetro
  - Se ha quitado el sello de las clases públicas expuestas en la interfaz IDocumentClient.

### <a name="1.8.0"/>[1\.8.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.8.0)
  - Se ha agregado compatibilidad con cuentas de base de datos de varias regiones.
  - Se ha agregado compatibilidad con el reintento en solicitudes limitadas. El usuario puede personalizar el número de reintentos y el tiempo de espera máximo mediante la configuración de la propiedad ConnectionPolicy.RetryOptions.
  - Se ha agregado una nueva interfaz IDocumentClient que define las firmas de todas las propiedades y métodos de DocumenClient. Como parte de este cambio, también se han cambiado los métodos de extensión que crean IQueryable y IOrderedQueryable por los métodos de la propia clase DocumentClient.
  - Se ha agregado la opción de configuración para definir el valor de ServicePoint.ConnectionLimit para un identificador URI de punto de conexión dado de DocumentDB. Use ConnectionPolicy.MaxConnectionLimit para cambiar el valor predeterminado, que está establecido en 50.
  - Se ha dejado de utilizar IPartitionResolver y su implementación. La compatibilidad con IPartitionResolver está ahora obsoleta. Se recomienda usar colecciones con particiones para conseguir un almacenamiento y un rendimiento más elevados.


### <a name="1.7.1"/>[1\.7.1](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.7.1)
  - Se ha agregado una sobrecarga al identificador URI según el método ExecuteStoredProcedureAsync que toma RequestOptions como un parámetro.
  
### <a name="1.7.0"/>[1\.7.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.7.0)
  - Se ha agregado compatibilidad con período de vida (TTL) para los documentos.

### <a name="1.6.3"/>[1\.6.3](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.6.3)
  - Se corrige un error en el paquete de Nuget del SDK de .NET para empaquetarlo como parte de una solución de servicio en la nube de Azure.
  
### <a name="1.6.2"/>[1\.6.2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.6.2)
  - Se han implementado [colecciones particionadas](documentdb-partition-data.md) y [niveles de rendimiento definidos por el usuario](documentdb-performance-levels.md).

### <a name="1.5.3"/>[1\.5.3](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.5.3)
  - **[Corregido]** La consulta del punto de conexión de DocumentDB genera el error System.Net.Http.HttpRequestException al copiar el contenido en una secuencia.

### <a name="1.5.2"/>[1\.5.2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.5.2)
  - Compatibilidad de LINQ ampliada, incluidos nuevos operadores de paginación, expresiones condicionales y comparación de intervalos.
    - Operador Take para habilitar el comportamiento de SELECT TOP en LINQ
    - Operador CompareTo para habilitar las comparaciones de intervalos de cadenas
    - Operadores conditional (?) y coalesce (??)
  - **[Corregido]** ArgumentOutOfRangeException al combinar la proyección Model en la consulta Where-In de LINQ. [81](https://github.com/Azure/azure-documentdb-dotnet/issues/81)

### <a name="1.5.1"/>[1\.5.1](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.5.1)
 - **[Corregido]** Si Select no es la última expresión, el proveedor LINQ suponía que no había ninguna proyección y generaba SELECT * incorrectamente. [#58](https://github.com/Azure/azure-documentdb-dotnet/issues/58)

### <a name="1.5.0"/>[1\.5.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.5.0)
 - Upsert implementada, métodos de UpsertXXXAsync agregados
 - Mejoras de rendimiento para todas las solicitudes
 - Compatibilidad del proveedor de LINQ con los métodos condicionales, de fusión y CompareTo para cadenas
 - **[Corregido]** proveedor LINQ--> la implementación del método Contains en List para generar el mismo SQL que en IEnumerable y en una matriz
 - **[Corregido]** BackoffRetryUtility usa el mismo HttpRequestMessage de nuevo en lugar de crear uno nuevo al reintentar
 - **[Obsoleto]** UriFactory.CreateCollection--> debería usar ahora UriFactory.CreateDocumentCollection
 
### <a name="1.4.1"/>[1\.4.1](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.4.1)
 - **[Corregido]** problemas de localización al usar la información de referencia cultural diferente de EN como nl-NL etc.
 
### <a name="1.4.0"/>[1\.4.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.4.0)
  - Enrutamiento por identificador.
    - Nueva aplicación auxiliar UriFactory para ayudar a construir vínculos de recursos basados en identificador
    - Nuevas sobrecargas en DocumentClient para tener en cuenta URI
  - Se agregaron IsValid() y IsValidDetailed() en LINQ para geoespaciales
  - Compatibilidad mejorada con el proveedor de LINQ
    - **Math**: Abs, Acos, Asin, Atan, Ceiling, Cos, Exp, Floor, Log, Log10, Pow, Round, Sign, Sin, Sqrt, Tan, Truncate
    - **String**: Concat, Contains, EndsWith, IndexOf, Count, ToLower, TrimStart, Replace, Reverse, TrimEnd, StartsWith, SubString, ToUpper
    - **Array**: Concat, Contains, Count
    - Operador **IN**

### <a name="1.3.0"/>[1\.3.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.3.0)
  - Se agregó compatibilidad para la modificación de las directivas de indexación
    - Nuevo método de ReplaceDocumentCollectionAsync en DocumentClient
    - Nueva propiedad IndexTransformationProgress en ResourceResponse<T> para realizar un seguimiento del porcentaje de progreso de cambios de la directiva de índice
    - DocumentCollection.IndexingPolicy ahora es mutable
  - Se agregó compatibilidad para consulta e indexación espacial
    - Nuevo espacio de nombres Microsoft.Azure.Documents.Spatial para serializar y deserializar tipos espaciales como punto y polígono
    - Nueva clase de SpatialIndex para la indexación de datos GeoJSON almacenados en DocumentDB
  - **[Corregido]**: consulta SQL incorrecta generada a partir de la expresión linq [n.º 38](https://github.com/Azure/azure-documentdb-net/issues/38)

### <a name="1.2.0"/>[1\.2.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.2.0)
- Dependencia de Newtonsoft.Json v5.0.7
- Cambios para admitir Order By
  - Compatibilidad del proveedor LINQ para OrderBy() u OrderByDescending()
  - IndexingPolicy para admitir Order By
  
		**NB: Possible breaking change** 
  
    	If you have existing code that provisions collections with a custom indexing policy, then your existing code will need to be updated to support the new IndexingPolicy class. If you have no custom indexing policy, then this change does not affect you.

### <a name="1.1.0"/>[1\.1.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.1.0)
- Compatibilidad para las particiones de datos mediante las nuevas clases HashPartitionResolver y RangePartitionResolver y el IPartitionResolver
- Serialización de DataContract
- Compatibilidad con el GUID de proveedor de LINQ
- Compatibilidad con UDF en LINQ

### <a name="1.0.0"/>[1\.0.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.0.0)
- SDK de GA

> [AZURE.NOTE]
Se produjo un cambio del nombre de paquete de NuGet entre la vista previa y la disponibilidad general. Pasamos de **Microsoft.Azure.Documents.Client** a **Microsoft.Azure.DocumentDB** <br/>


### <a name="0.9.x-preview"/>[0\.9.x-preview](https://www.nuget.org/packages/Microsoft.Azure.Documents.Client)
- SDK de vista previa [obsoleto]

## Fechas de lanzamiento y de retirada
Microsoft notificará la retirada de un SDK con al menos **12 meses** de antelación para facilitar la transición a una versión compatible o más reciente.

Solo se agregan nuevas características, funcionalidad y optimizaciones al SDK actual, por lo que se recomienda actualizar siempre a la última versión del SDK tan pronto como sea posible.

El servicio rechazará cualquier solicitud realizada en DocumentDB mediante un SDK retirado.

> [AZURE.WARNING]
Todas las versiones del SDK de Azure DocumentDB para .NET anteriores a la versión **1.0.0** se retirarán el **29 de febrero de 2016**.
 
<br/>
 
| Versión | Fecha de lanzamiento | Fecha de retirada 
| ---	  | ---	         | ---
| [1\.9.1](#1.9.1) | 20 de julio, 2016 |--- 
| [1\.9.0](#1.9.0) | 09 de julio, 2016 |--- 
| [1\.8.0](#1.8.0) | 14 de junio, 2016 |--- 
| [1\.7.1](#1.7.1) | 06 de mayo, 2016 |--- 
| [1\.7.0](#1.7.0) | 26 de abril, 2016 |--- 
| [1\.6.3](#1.6.3) | 08 de abril, 2016 |--- 
| [1\.6.2](#1.6.2) | 29 de marzo, 2016 |--- 
| [1\.5.3](#1.5.3) | 19 de febrero, 2016 |--- 
| [1\.5.2](#1.5.2) | 14 de diciembre, 2015 |--- 
| [1\.5.1](#1.5.1) | 23 de noviembre, 2015 |--- 
| [1\.5.0](#1.5.0) | 05 de octubre, 2015 |--- 
| [1\.4.1](#1.4.1) | 25 de agosto, 2015 |--- 
| [1\.4.0](#1.4.0) | 13 de agosto, 2015 |--- 
| [1\.3.0](#1.3.0) | 05 de agosto, 2015 |--- 
| [1\.2.0](#1.2.0) | 06 de julio, 2015 |--- 
| [1\.1.0](#1.1.0) | 30 de abril, 2015 |--- 
| [1\.0.0](#1.0.0) | 08 de abril, 2015 |--- 
| [0\.9.3-versión preliminar](#0.9.x-preview) | 12 de marzo, 2015 | 29 de febrero, 2016 
| [0\.9.2-versión preliminar](#0.9.x-preview) | Enero, 2015 | 29 de febrero, 2016 
| [.9.1-versión preliminar](#0.9.x-preview) |13 de octubre, 2014 | 29 de febrero, 2016 
| [0\.9.0-versión preliminar](#0.9.x-preview) | 21 de agosto, 2014 | 29 de febrero, 2016

## P+F
[AZURE.INCLUDE [documentdb-sdk-faq](../../includes/documentdb-sdk-faq.md)]

## Otras referencias

Para más información sobre DocumentDB, vea la página del servicio [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/).

<!---HONumber=AcomDC_0810_2016-->