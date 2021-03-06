<properties 
	pageTitle="Movimiento de datos de MongoDB mediante Data Factory | Microsoft Azure" 
	description="Obtenga información acerca de cómo mover los datos de la base de datos de MongoDB mediante Data Factory de Azure." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/04/2016" 
	ms.author="spelluru"/>

# Movimiento de datos de MongoDB mediante Data Factory de Azure

En este artículo se describe cómo se puede usar la actividad de copia en Data Factory de Azure para mover datos de una base de datos local de MongoDB a otro almacén de datos. Este artículo se basa en el artículo sobre [actividades de movimiento de datos](data-factory-data-movement-activities.md) que presenta información general del movimiento de datos con la actividad de copia y las combinaciones de almacén de datos de origen o de receptor que admite la actividad de copia.

El servicio Data Factory admite la conexión a orígenes de MongoDB locales mediante Data Management Gateway. Consulte el artículo [Data Management Gateway](data-factory-data-management-gateway.md) para más información acerca de Data Management Gateway y el artículo [Movimiento de datos entre orígenes locales y la nube con Data Management Gateway](data-factory-move-data-between-onprem-and-cloud.md) para obtener instrucciones detalladas sobre cómo configurar la puerta de enlace de una canalización de datos para mover los datos.

> [AZURE.NOTE] Tiene que aprovechar la puerta de enlace para conectar con MongoDB, incluso si está hospedado en máquinas virtuales de IaaS de Azure. Si está intentando conectarse a una instancia de MongoDB hospedada en la nube, también puede instalar la instancia de puerta de enlace en la máquina virtual de IaaS.

Data Factory solo admite actualmente el movimiento de datos desde MongoDB a otros almacenes de datos, pero no de otros almacenes de datos a MongoDB.

## Requisitos previos
Para que el servicio Data Factory de Azure pueda conectarse a la base de datos de MongoDB local, debe instalar lo siguiente:

- Data Management Gateway 2.0 o una versión posterior en la misma máquina que hospeda la base de datos o en una máquina distinta, para evitar que se dispute los recursos con la base de datos. Data Management Gateway es un software que conecta orígenes de datos locales a servicios en la nube de forma segura y administrada. Consulte el artículo [Data Management Gateway](data-factory-data-management-gateway.md) para más detalles sobre Data Management Gateway.
  
	Cuando instale la puerta de enlace, se instalará automáticamente el controlador ODBC de Microsoft MongoDB que se utiliza para establecer la conexión con MongoDB.

## Asistente para copia de datos
La manera más fácil de crear una canalización que copie datos de una base de datos de MongoDB en cualquiera de los almacenes de datos receptores admitidos es usar el Asistente para copiar datos. Consulte [Tutorial: crear una canalización con la actividad de copia mediante el Asistente para copia de Data Factory](data-factory-copy-data-wizard-tutorial.md) para ver un tutorial rápido sobre la creación de una canalización mediante el Asistente para copiar datos.

En el siguiente ejemplo, se proporcionan definiciones JSON de ejemplo que puede usar para crear una canalización mediante el [Portal de Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md).

## Ejemplo: Copiar datos de MongoDB a un blob de Azure
En este ejemplo, se muestra cómo copiar datos de una base de datos de MongoDB local a un Almacenamiento de blobs de Azure. Sin embargo, se pueden copiar datos **directamente** a cualquiera de los receptores indicados [aquí](data-factory-data-movement-activities.md#supported-data-stores) mediante la actividad de copia en Data Factory de Azure.
 
El ejemplo consta de las siguientes entidades de factoría de datos:

1.	Un servicio vinculado de tipo [OnPremisesMongoDB](#linked-service-properties).
2.	Un servicio vinculado de tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties)
3.	Un [conjunto de datos](data-factory-create-datasets.md) de entrada de tipo [MongoDBCollection](#dataset-type-properties).
4.	Un [conjunto de datos](data-factory-create-datasets.md) de salida de tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4.	Una [canalización](data-factory-create-pipelines.md) con la actividad de copia que usa [MongoDBSource](#copy-activity-type-properties) y [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

El ejemplo copia cada hora los datos de un resultado de consulta de la base de datos de MongoDB en un blob. Las propiedades JSON usadas en estos ejemplos se describen en las secciones que aparecen después de los ejemplos.

Como primer paso, configure la puerta de enlace de administración de datos según las instrucciones del artículo [Data Management Gateway](data-factory-data-management-gateway.md).

**Servicio vinculado de MongoDB**

	{ 
	    "name": "OnPremisesMongoDbLinkedService", 
	    "properties": 
	    { 
	        "type": "OnPremisesMongoDb", 
	        "typeProperties": 
	        { 
	            "authenticationType": "<Basic or Anonymous>", 
	            "server": "< The IP address or host name of the MongoDB server >",  
				"port": "<The number of the TCP port that the MongoDB server uses to listen for client connections.>", 
	            "username": "<username>", 
	            "password": "<password>",
	           "authSource": "< The database that you want to use to check your credentials for authentication. >",
	            "databaseName": "<database name>",
	            "gatewayName": "<mygateway>"
	        } 
		}
	} 


**Servicio vinculado de Almacenamiento de Azure**

	{
	  "name": "AzureStorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

**Conjunto de datos de entrada de MongoDB**: si se establece "external" en "true", se informa al servicio Data Factory que la tabla es externa a la factoría de datos y que no la genera ninguna actividad de la factoría de datos.
	
	{
	     "name":  "MongoDbInputDataset",
	    "properties": { 
	        "type": "MongoDbCollection", 
	        "linkedServiceName": "OnPremisesMongoDbLinkedService", 
	        "typeProperties": { 
	            "collectionName": "<Collection name>"	
	        }, 
	        "availability": {
	            "frequency": "Hour",
	            "interval": 1
	        },
			"external": true
	    } 
	}



**Conjunto de datos de salida de blob de Azure**

Los datos se escriben en un nuevo blob cada hora (frecuencia: hora, intervalo: 1). La ruta de acceso de la carpeta para el blob se evalúa dinámicamente según la hora de inicio del segmento que se está procesando. La ruta de acceso de la carpeta usa las partes year, month, day y hours de la hora de inicio.

	{
	    "name": "AzureBlobOutputDataSet",
	    "properties": {
	        "type": "AzureBlob",
	        "linkedServiceName": "AzureStorageLinkedService",
	        "typeProperties": {
	            "folderPath": "mycontainer/frommongodb/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
	            "format": {
	                "type": "TextFormat",
	                "rowDelimiter": "\n",
	                "columnDelimiter": "\t"
	            },
	            "partitionedBy": [
	                {
	                    "name": "Year",
	                    "value": {
	                        "type": "DateTime",
	                        "date": "SliceStart",
	                        "format": "yyyy"
	                    }
	                },
	                {
	                    "name": "Month",
	                    "value": {
	                        "type": "DateTime",
	                        "date": "SliceStart",
	                        "format": "%M"
	                    }
	                },
	                {
	                    "name": "Day",
	                    "value": {
	                        "type": "DateTime",
	                        "date": "SliceStart",
	                        "format": "%d"
	                    }
	                },
	                {
	                    "name": "Hour",
	                    "value": {
	                        "type": "DateTime",
	                        "date": "SliceStart",
	                        "format": "%H"
	                    }
	                }
	            ]
	        },
	        "availability": {
	            "frequency": "Hour",
	            "interval": 1
	        }
	    }
	}



**Canalización con actividad de copia**

La canalización contiene una actividad de copia que está configurada para usar los conjuntos de datos de entrada y de salida y está programada para ejecutarse cada hora. En la definición de la canalización JSON, el tipo **source** se establece en **MongoDbSource** y el tipo **sink**, en **BlobSink**. La consulta SQL especificada para la propiedad **query** selecciona los datos de la última hora que se van a copiar.
	
	{
	    "name": "CopyMongoDBToBlob",
	    "properties": {
	        "description": "pipeline for copy activity",
	        "activities": [
	            {
	                "type": "Copy",
	                "typeProperties": {
	                    "source": {
	                        "type": "MongoDbSource",
	                        "query": "$$Text.Format('select * from  MyTable where LastModifiedDate >= {{ts\'{0:yyyy-MM-dd HH:mm:ss}\'}} AND LastModifiedDate < {{ts\'{1:yyyy-MM-dd HH:mm:ss}\'}}', WindowStart, WindowEnd)"
	                    },
	                    "sink": {
	                        "type": "BlobSink",
	                        "writeBatchSize": 0,
	                        "writeBatchTimeout": "00:00:00"
	                    }
	                },
	                "inputs": [
	                    {
	                        "name": "MongoDbInputDataset"
	                    }
	                ],
	                "outputs": [
	                    {
	                        "name": "AzureBlobOutputDataSet"
	                    }
	                ],
	                "policy": {
	                    "timeout": "01:00:00",
	                    "concurrency": 1
	                },
	                "scheduler": {
	                    "frequency": "Hour",
	                    "interval": 1
	                },
	                "name": "MongoDBToAzureBlob"
	            }
	        ],
	        "start": "2016-06-01T18:00:00Z",
	        "end": "2016-06-01T19:00:00Z"
	    }
	}


## Propiedades del servicio vinculado

En la tabla siguiente se proporciona la descripción de los elementos JSON específicos del servicio vinculado de **OnPremisesMongoDB**.

| Propiedad | Descripción | Obligatorio |
| -------- | ----------- | -------- | 
| type | La propiedad type debe establecerse en: **OnPremisesMongoDb** | Sí |
| server | Dirección IP o nombre de host del servidor de MongoDB. | Sí | 
| puerto | Puerto TCP que el servidor de MongoDB utiliza para escuchar las conexiones del cliente. | Valor predeterminado opcional: 27017 |
| authenticationType | Básica o anónima. | Sí | 
| nombre de usuario | Cuenta de usuario para tener acceso a MongoDB. | Sí (si se usa la autenticación básica).|
| contraseña | Contraseña del usuario. |	Sí (si se usa la autenticación básica). | 
| authSource | Nombre de la base de datos de MongoDB que desea usar para comprobar las credenciales de autenticación. | Opcional (si se usa la autenticación básica). Valor predeterminado: se utiliza la cuenta de administrador y la base de datos especificada mediante la propiedad databaseName. |  
| databaseName | Nombre de la base de datos de MongoDB a la que desea acceder. | Sí |
| gatewayName | Nombre de la puerta de enlace que accede al almacén de datos. | Sí | 
| encryptedCredential | Credencial cifrada por la puerta de enlace. | Opcional |


Consulte [Configuración de credenciales y seguridad](data-factory-move-data-between-onprem-and-cloud.md#set-credentials-and-security) para más información acerca de cómo configurar las credenciales para un origen de datos de MongoDB local.

## Propiedades de tipo de conjunto de datos

Para una lista completa de las secciones y propiedades disponibles para definir conjuntos de datos, vea el artículo [Creación de conjuntos de datos](data-factory-create-datasets.md). Las secciones como structure, availability y policy de un conjunto de datos JSON son similares en todos los tipos de conjunto de datos (SQL Azure, blob de Azure, tabla de Azure, etc.).

La sección **typeProperties** es diferente en cada tipo de conjunto de datos y proporciona información acerca de la ubicación de los datos en el almacén de datos. La sección typeProperties del conjunto de datos de tipo **MongoDbCollection** tiene las propiedades siguientes:

| Propiedad | Descripción | Obligatorio |
| -------- | ----------- | -------- |
| collectionName | Nombre de la colección en la base de datos de MongoDB. | Sí | 

## Propiedades de tipo de actividad de copia

Para obtener una lista completa de las secciones y propiedades disponibles para definir actividades, consulte el artículo [Creación de canalizaciones](data-factory-create-pipelines.md). Propiedades como nombre, descripción, tablas de entrada y salida, varias directivas, etc. están disponibles para todos los tipos de actividades.

Por otro lado, las propiedades disponibles en la sección **typeProperties** de la actividad varían con cada tipo de actividad y, en el caso de la actividad de copia, varían en función de los tipos de orígenes y receptores.

En caso de la actividad de copia si el origen es de tipo **MongoDbSource**, están disponibles las propiedades siguientes en la sección typeProperties:

| Propiedad | Descripción | Valores permitidos | Obligatorio |
| -------- | ----------- | -------------- | -------- |
| query | Utilice la consulta personalizada para leer los datos. | Cadena de consulta SQL-92. Por ejemplo: select * from MyTable. | No (si se especifica **collectionName** de **dataset**) | 

## Esquema de Data Factory
El servicio de Data Factory de Azure deduce el esquema de una colección de MongoDB mediante el uso de los últimos 100 documentos de la colección. Si estos 100 documentos no contienen el esquema completo, se pueden omitir algunas columnas durante la operación de copia.

## Asignación de tipos para MongoDB

Como se mencionó en el artículo sobre [actividades del movimiento de datos](data-factory-data-movement-activities.md), la actividad de copia realiza conversiones automáticas de los tipos de origen a los tipos de receptor con el siguiente enfoque de dos pasos:

1. Conversión de tipos de origen nativos al tipo .NET
2. Conversión de tipo .NET al tipo del receptor nativo

Al mover datos a MongoDB, se usarán las asignaciones siguientes de tipos MongoDB a tipos .NET.

| Tipo de MongoDB | Tipo .NET Framework |
| ------------------- | ------------------- | 
| Binary | Byte |
| Booleano | Booleano |
| Date | DateTime |
| NumberDouble | Doble |
| NumberInt | Int32 |
| NumberLong | Int64 |
| ObjectID | String |
| String | String |
| UUID | Guid | 
| Objeto | Renormalizado en columnas acopladas con “\_” como separador anidado |

> [AZURE.NOTE]  
Para más información sobre la compatibilidad para matrices con tablas virtuales, consulte la sección [Compatibilidad para tipos complejos que usan tablas virtuales](#support-for-complex-types-using-virtual-tables) que aparece más adelante.

En este momento, no se admiten los siguientes tipos de datos de MongoDB: DBPointer, JavaScript, Clave Max y Min, Expresión regular, Símbolo, Marca de tiempo, Sin definir

## Compatibilidad para tipos complejos que usan tablas virtuales
Data Factory de Azure utiliza un controlador ODBC integrado para conectarse a una base de datos de MongoDB y copiar datos de dicha base de datos. Para los tipos complejos como matrices u objetos con diferentes tipos en los documentos, el controlador volverá a normalizar los datos en las tablas virtuales correspondientes. En concreto, si una tabla contiene estas columnas, el controlador generará las siguientes tablas virtuales:

-	Una **tabla base**, que contiene los mismos datos que la tabla real, salvo las columnas de tipo complejo. La tabla base utiliza el mismo nombre que la tabla real a la que representa.
-	Una **tabla virtual** para cada columna de tipo complejo, que ampliará los datos anidados. Para asignar un nombre a las tablas virtuales, se utiliza el nombre de la tabla real, un separador “\_” y el nombre de la matriz u objeto.

Las tablas virtuales hacen referencia a los datos de la tabla real, lo que permite al controlador obtener acceso a los datos no normalizados. Consulte el ejemplo de la siguiente sección para más información. Para acceder al contenido de las matrices de MongoDB, puede crear consultas y combinar las tablas virtuales.

Si utiliza el [Asistente para copia](data-factory-data-movement-activities.md#data-factory-copy-wizard), podrá consultar una vista intuitiva de la lista de tablas de la base de datos de MongoDB (incluidas las tablas virtuales) y una vista previa de los datos incluidos. También puede crear una consulta en el Asistente para copia y validarla para ver el resultado.

### Ejemplo

Por ejemplo, la tabla “ExampleTable” que aparece a continuación es una tabla de MongoDB que tiene una columna con una matriz de objetos en cada celda: Facturas y una columna con una matriz de tipos escalares: Clasificaciones.

\_id | Nombre del cliente | Facturas | Nivel de servicios | Clasificaciones
--- | ------------- | -------- | ------------- | -------
1111 | ABC | [{invoice\_id:”123”, artículo:”tostadora”, precio:”456”, descuento:”0.2”}, {invoice\_id:”124”, artículo:”horno”,precio: ”1235”,descuento: ”0.2”}] | Silver | [5,6]
2222 | XYZ | [{invoice\_id:”135”, artículo:”frigorífico”,precio: ”12543”,descuento: ”0.0”}] | Gold | [1,2]

El controlador generará varias tablas virtuales que representan a esta tabla. La primera tabla virtual es la tabla base y se denomina “ExampleTable”, tal y como se muestra a continuación. La tabla base contiene todos los datos de la tabla original, pero los datos de las matrices se han omitido y se ampliarán en las tablas virtuales.

\_id | Nombre del cliente | Nivel de servicios
--- | ------------- | -------------
1111 | ABC | Silver
2222 | XYZ | Gold

Las siguientes tablas muestran las tablas virtuales que representan las matrices originales en el ejemplo. Cada una de estas tablas contiene lo siguiente

- Una referencia a la columna de clave principal original correspondiente a la fila de la matriz original (a través del identificador de la columna)
- Una indicación de la posición de los datos dentro de la matriz original
- Los datos ampliados para cada elemento de la matriz

Tabla “ExampleTable\_Invoices”:

\_id | ExampleTable\_Invoices\_dim1\_idx | invoice\_id | item | price | Descuento
--- | -------------- | ---------- | ---- | ----- | --------
1111 | 0 | 123 | tostadora | 456 | 0,2
1111 | 1 | 124 | horno | 1235 | 0,2
2222 | 0 | 135 | frigorífico | 12543 | 0\.0

Tabla “ExampleTable\_Ratings”:

\_id | ExampleTable\_Ratings\_dim1\_idx | ExampleTable\_Ratings
--- | ------------- | -------------
1111 | 0 | 5
1111 | 1 | 6
2222 | 0 | 1
2222 | 1 | 2

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## Rendimiento y optimización  
Consulte [Guía de optimización y rendimiento de la actividad de copia](data-factory-copy-activity-performance.md) para más información sobre los factores clave que afectan al rendimiento del movimiento de datos (actividad de copia) en Data Factory de Azure y las diversas formas de optimizarlo.

## Pasos siguientes
Consulte el artículo [Movimiento de datos entre orígenes locales y la nube con Data Management Gateway](data-factory-move-data-between-onprem-and-cloud.md) para obtener instrucciones paso a paso para crear una canalización de datos que mueva los datos de un almacén de datos local a un almacén de datos de Azure.

<!---HONumber=AcomDC_0810_2016-->