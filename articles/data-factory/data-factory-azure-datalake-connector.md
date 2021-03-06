<properties
	pageTitle="Movimiento de datos hacia y desde el almacén de Azure Data Lake | Data Factory de Azure"
	description="Obtenga información sobre cómo mover datos hacia y desde el almacén de Azure Data Lake mediante la Factoría de datos de Azure."
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
	ms.date="06/06/2016"
	ms.author="spelluru"/>

# Movimiento de datos hacia y desde el almacén de Azure Data Lake mediante la Factoría de datos de Azure
En este artículo se describe cómo puede usar la actividad de copia en una Factoría de datos de Azure para mover datos al Almacén de Azure Data Lake desde otro almacén de datos, y viceversa. Este artículo se basa en el artículo sobre [actividades de movimiento de datos](data-factory-data-movement-activities.md) que presenta una introducción general del movimiento de datos con la actividad de copia y las combinaciones del almacén de datos admitidas.

> [AZURE.NOTE]
Antes de crear una canalización con una actividad de copia para mover datos hacia y desde un almacén de Azure Data Lake, debe crear una cuenta de almacén de Azure Data Lake. Para obtener más información sobre el almacén de Azure Data Lake, consulte [Introducción al almacén de Azure Data Lake](../data-lake-store/data-lake-store-get-started-portal.md).
>  
> Revise el [tutorial Compilación de la primera canalización ](data-factory-build-your-first-pipeline.md) para ver los pasos detallados para crear una factoría de datos, servicios vinculados, conjuntos de datos y una canalización. Use los fragmentos de código JSON con el Editor de Factoría de datos, Visual Studio o Azure PowerShell para crear las entidades de Factoría de datos.

## Asistente para copia de datos
La manera más sencilla de crear una canalización que copie datos a o desde Azure Data Lake Store es usar el Asistente para copiar datos. Vea [Tutorial: crear una canalización con la actividad de copia mediante el Asistente para copia de Data Factory](data-factory-copy-data-wizard-tutorial.md) para obtener un tutorial rápido sobre la creación de una canalización mediante el Asistente para copiar datos.

En los siguientes ejemplos se proporcionan definiciones JSON que puede usar para crear una canalización mediante el [Portal de Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Muestran cómo copiar datos entre Azure Data Lake Store y el Almacenamiento de blobs de Azure. En cambio, los datos se pueden copiar **directamente** de cualquiera de los orígenes a cualquiera de los receptores indicados [aquí](data-factory-data-movement-activities.md#supported-data-stores) mediante la actividad de copia en Data Factory de Azure.


## Ejemplo: copia de datos de un blob de Azure al almacén de Azure Data Lake
El ejemplo siguiente muestra:

1.	Un servicio vinculado de tipo [AzureStorage](#azure-storage-linked-service-properties).
2.	Un servicio vinculado de tipo [AzureDataLakeStore](#azure-data-lake-linked-service-properties).
3.	Un [conjunto de datos](data-factory-create-datasets.md) de entrada de tipo [AzureBlob](#azure-blob-dataset-type-properties).
4.	Un [conjunto de datos](data-factory-create-datasets.md) de salida de tipo [AzureDataLakeStore](#azure-data-lake-dataset-type-properties).
4.	Una [canalización](data-factory-create-pipelines.md) con una actividad de copia que usa [BlobSource](#azure-blob-copy-activity-type-properties) y [AzureDataLakeStoreSink](#azure-data-lake-copy-activity-type-properties).

El ejemplo copia los datos pertenecientes a una serie temporal desde un almacenamiento de blobs de Azure al almacén de Azure Data Lake cada hora. Las propiedades JSON usadas en estos ejemplos se describen en las secciones que aparecen después de los ejemplos.


**Servicio vinculado de Almacenamiento de Azure:**

	{
	  "name": "StorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

**Servicio vinculado de Azure Data Lake:**

	{
	    "name": "AzureDataLakeStoreLinkedService",
	    "properties": {
	        "type": "AzureDataLakeStore",
	        "typeProperties": {
	            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
				"sessionId": "<session ID>",
	            "authorization": "<authorization URL>"
	        }
	    }
	}

### Creación de un servicio vinculado de Azure Data Lake con el Editor de la Factoría de datos
El procedimiento siguiente proporciona los pasos para crear un servicio vinculado del Almacén de Azure Data Lake con el Editor de la Factoría de datos.

1. Haga clic en **Nuevo almacén de datos** en la barra de comandos y seleccione **Almacén de Azure Data Lake**.
2. En el editor de JSON, en la propiedad **dataLakeStoreUri**, especifique el URI de Data Lake.
3. Haga clic en el botón **Autorizar** de la barra de comandos. Aparecerá una ventana emergente.

	![Botón Autorizar](./media/data-factory-azure-data-lake-connector/authorize-button.png)

4. Use las credenciales para iniciar sesión; debería asignarse un valor a la propiedad **authorization** de JSON.
5. (Opcional) Especifique valores para los parámetros opcionales, como **accountName**, **subscriptionID** y **resourceGroupName**, del código JSON o bien elimine esas propiedades de dicho código.
6. Haga clic en **Implementar** en la barra de comandos para implementar el servicio vinculado.

> [AZURE.IMPORTANT] El código de autorización que ha generado al hacer clic en el botón **Autorizar** expira poco tiempo después. Necesitará **volver a dar la autorización** con el botón **Autorizar** cuando el **token expire** y volver a implementar el servicio vinculado. Para más información, vea [Propiedades del servicio vinculado de Azure Data Lake Store](#azure-data-lake-store-linked-service-properties).



**Conjunto de datos de entrada de blob de Azure:**

Los datos se seleccionan de un nuevo blob cada hora (frecuencia: hora, intervalo: 1). La ruta de acceso de la carpeta y el nombre de archivo para el blob se evalúan dinámicamente según la hora de inicio del segmento que se está procesando. La ruta de acceso de la carpeta usa la parte year, month y day de la hora de inicio y el nombre de archivo, la parte hour. El valor "external": "true" informa al servicio Factoría de datos que esta tabla es externa a la factoría y no la produce una actividad de la factoría de datos.

	{
	  "name": "AzureBlobInput",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "Path": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
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
	            "format": "MM"
	          }
	        },
	        {
	          "name": "Day",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "dd"
	          }
	        },
	        {
	          "name": "Hour",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "HH"
	          }
	        }
	      ]
	    },
	    "external": true,
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    },
	    "policy": {
	      "externalData": {
	        "retryInterval": "00:01:00",
	        "retryTimeout": "00:10:00",
	        "maximumRetry": 3
	      }
	    }
	  }
	}


**Conjunto de datos de salida de Azure Data Lake:**

El ejemplo copia los datos en un almacén de Azure Data Lake. Los nuevos datos se copian en el almacén de Data Lake cada hora.

	{
		"name": "AzureDataLakeStoreOutput",
	  	"properties": {
			"type": "AzureDataLakeStore",
		    "linkedServiceName": "AzureDataLakeStoreLinkedService",
		    "typeProperties": {
				"folderPath": "datalake/output/"
		    },
	    	"availability": {
	      		"frequency": "Hour",
	      		"interval": 1
	    	}
	  	}
	}



**Canalización con actividad de copia:**

La canalización contiene una actividad de copia que está configurada para usar los conjuntos de datos de entrada y de salida y está programada para ejecutarse cada hora. En la definición de JSON de la canalización, el tipo **source** está establecido en **BlobSource** y el tipo **sink** en **AzureDataLakeStoreSink**.

	{  
	    "name":"SamplePipeline",
	    "properties":
		{  
	    	"start":"2014-06-01T18:00:00",
	    	"end":"2014-06-01T19:00:00",
	    	"description":"pipeline with copy activity",
	    	"activities":
			[  
	      		{
	        		"name": "AzureBlobtoDataLake",
		        	"description": "Copy Activity",
		        	"type": "Copy",
		        	"inputs": [
		          	{
			            "name": "AzureBlobInput"
		          	}
		        	],
		        	"outputs": [
		          	{
			            "name": "AzureDataLakeStoreOutput"
		          	}
		        	],
		        	"typeProperties": {
			        	"source": {
		            		"type": "BlobSource",
			    			"treatEmptyAsNull": true,
		            		"blobColumnSeparators": ","
		          		},
		          		"sink": {
		            		"type": "AzureDataLakeStoreSink"
		          		}
		        	},
		       		"scheduler": {
		          		"frequency": "Hour",
		          		"interval": 1
		        	},
		        	"policy": {
		          		"concurrency": 1,
		          		"executionPriorityOrder": "OldestFirst",
		          		"retry": 0,
		          		"timeout": "01:00:00"
		        	}
		      	}
    		]
		}
	}

## Ejemplo: copia de datos del almacén de Azure Data Lake a un blob de Azure
El ejemplo siguiente muestra:

1.	Un servicio vinculado de tipo [AzureDataLakeStore](#azure-data-lake-linked-service-properties).
2.	Un servicio vinculado de tipo [AzureStorage](#azure-storage-linked-service-properties).
3.	Un [conjunto de datos](data-factory-create-datasets.md) de entrada de tipo [AzureDataLakeStore](#azure-data-lake-dataset-type-properties).
4.	Un [conjunto de datos](data-factory-create-datasets.md) de salida de tipo [AzureBlob](#azure-blob-dataset-type-properties).
5.	Una [canalización](data-factory-create-pipelines.md) con una actividad de copia que usa [AzureDataLakeStoreSource](#azure-data-lake-copy-activity-type-properties) y [BlobSink](#azure-blob-copy-activity-type-properties)

El ejemplo copia los datos pertenecientes a una serie temporal desde un almacén de Azure Data Lake a un blob de Azure cada hora. Las propiedades JSON usadas en estos ejemplos se describen en las secciones que aparecen después de los ejemplos.

**Servicio vinculado del almacén de Azure Data Lake:**

	{
	    "name": "AzureDataLakeStoreLinkedService",
	    "properties": {
	        "type": "AzureDataLakeStore",
	        "typeProperties": {
	            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
				"sessionId": "<session ID>",
	            "authorization": "<authorization URL>"
	        }
	    }
	}

> [AZURE.NOTE] Vea los pasos del ejemplo anterior para obtener la dirección URL de autorización.

**Servicio vinculado de Almacenamiento de Azure:**

	{
	  "name": "StorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

**Conjunto de datos de entrada de Azure Data Lake:**

Si se establece **"external": true** y se especifica la directiva **externalData**, se informa al servicio Factoría de datos de Azure que la tabla es externa a la factoría de datos y que no se produce por ninguna actividad de dicha factoría.

	{
		"name": "AzureDataLakeStoreInput",
	  	"properties":
		{
	    	"type": "AzureDataLakeStore",
	    	"linkedServiceName": "AzureDataLakeStoreLinkedService",
		    "typeProperties": {
				"folderPath": "datalake/input/",
            	"fileName": "SearchLog.tsv",
            	"format": {
                	"type": "TextFormat",
	                "rowDelimiter": "\n",
    	            "columnDelimiter": "\t"
    	        }
		    },
		    "external": true,
		    "availability": {
		    	"frequency": "Hour",
		      	"interval": 1
	    	},
	    	"policy": {
	      		"externalData": {
	        		"retryInterval": "00:01:00",
	        		"retryTimeout": "00:10:00",
	        		"maximumRetry": 3
	      		}
	    	}
	  	}
	}

**Conjunto de datos de salida de blob de Azure:**

Los datos se escriben en un nuevo blob cada hora (frecuencia: hora, intervalo: 1). La ruta de acceso de la carpeta para el blob se evalúa dinámicamente según la hora de inicio del segmento que se está procesando. La ruta de acceso de la carpeta usa las partes year, month, day y hours de la hora de inicio.

	{
	  "name": "AzureBlobOutput",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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
	            "format": "MM"
	          }
	        },
	        {
	          "name": "Day",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "dd"
	          }
	        },
	        {
	          "name": "Hour",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "HH"
	          }
	        }
	      ],
	      "format": {
	        "type": "TextFormat",
	        "columnDelimiter": "\t",
	        "rowDelimiter": "\n"
	      }
	    },
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    }
	  }
	}

**Canalización con actividad de copia:**

La canalización contiene una actividad de copia que está configurada para usar los conjuntos de datos de entrada y de salida y está programada para ejecutarse cada hora. En la definición de JSON de canalización, el tipo **source** se establece en **AzureDataLakeStoreSource** y el tipo **sink** en **BlobSink**.


	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    	"start":"2014-06-01T18:00:00",
	    	"end":"2014-06-01T19:00:00",
	    	"description":"pipeline for copy activity",
	    	"activities":[  
	      		{
	        		"name": "AzureDakeLaketoBlob",
		    	    "description": "copy activity",
		    	    "type": "Copy",
		    	    "inputs": [
		    	      {
		    	        "name": "AzureDataLakeStoreInput"
		    	      }
		    	    ],
		    	    "outputs": [
		    	      {
		    	        "name": "AzureBlobOutput"
		    	      }
		    	    ],
		    	    "typeProperties": {
		    	    	"source": {
		            		"type": "AzureDataLakeStoreSource",
		          		},
		          		"sink": {
		            		"type": "BlobSink"
		          		}
		        	},
		       		"scheduler": {
		          		"frequency": "Hour",
		          		"interval": 1
		        	},
		        	"policy": {
		          		"concurrency": 1,
		          		"executionPriorityOrder": "OldestFirst",
		          		"retry": 0,
		          		"timeout": "01:00:00"
		        	}
		      	}
		     ]
		}
	}


## Propiedades del servicio vinculado de Almacén de Azure Data Lake

Puede vincular una cuenta de Almacenamiento de Azure a una Factoría de datos de Azure mediante un servicio vinculado de Almacenamiento de Azure. En la tabla siguiente se proporciona la descripción de los elementos JSON específicos del servicio vinculado de Almacenamiento de Azure.

| Propiedad | Descripción | Obligatorio |
| :-------- | :----------- | :-------- |
| type | La propiedad type debe establecerse en: **AzureDataLakeStore** | Sí |
| dataLakeStoreUri | Especifica información sobre la cuenta de Almacén de Azure Data Lake. Tiene el formato siguiente: https://<nombre de la cuenta de Azure Data Lake>.azuredatalakestore.net/webhdfs/v1 | Sí |
| authorization | Haga clic en el botón **Autorizar** del **Editor de la Factoría de datos** y escriba sus credenciales. De esta forma, se asigna la dirección URL de autorización generada automáticamente a esta propiedad. | Sí |
| sessionId | Identificador de sesión OAuth de la sesión de autorización de OAuth. Cada identificador de sesión es único y solo puede usarse una vez. Se genera automáticamente al usar el Editor de la Factoría de datos. | Sí |  
| accountName | Nombre de la cuenta de Data Lake. | No |
| subscriptionId | Identificador de la suscripción de Azure. | No (si no se especifica, se usa la suscripción de la factoría de datos). |
| resourceGroupName | Nombre del grupo de recursos de Azure. | No (si no se especifica, se usa el grupo de recursos de la factoría de datos). |

## Expiración del token 
El código de autorización que genera al hacer clic en el botón **Autorizar** expira poco tiempo después. Consulte la tabla siguiente para conocer el momento en que expiran los distintos tipos de cuentas de usuario. Puede ver el siguiente mensaje de error cuando el **token de autenticación expira**: Error de operación de credencial: invalid\_grant - AADSTS70002: error al validar las credenciales. AADSTS70008: la concesión de acceso proporcionada expiró o se revocó. Id. de seguimiento: d18629e8-af88-43c5-88e3-d8419eb1fca1 Id. de correlación: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 Marca de tiempo: 2015-12-15 21-09-31Z".


| Tipo de usuario | Expira después de |
| :-------- | :----------- | 
| Cuentas de usuario NO administradas por Azure Active Directory (@hotmail.com, @live.com, etc.) | 12 horas |
| Cuentas de usuario administradas por Azure Active Directory (AAD) | 14 días después de la ejecución del último segmento. <br/><br/>90 días, si un segmento basado en el servicio vinculado basado en OAuth se ejecuta al menos una vez cada 14 días. |

Tenga en cuenta que si cambia la contraseña antes de esta hora de expiración del token, el token caduca inmediatamente y verá el error mencionado anteriormente.

Para evitar o resolver este error, será preciso que vuelva a dar la autorización con el botón **Autorizar** cuando el **token expire** y vuelva a implementar el servicio vinculado. También puede generar valores para las propiedades **sessionId** y **authorization** mediante programación, para lo que usará el código de la sección siguiente.

### Para generar los valores de sessionId y authorization mediante programación 

    if (linkedService.Properties.TypeProperties is AzureDataLakeStoreLinkedService ||
        linkedService.Properties.TypeProperties is AzureDataLakeAnalyticsLinkedService)
    {
        AuthorizationSessionGetResponse authorizationSession = this.Client.OAuth.Get(this.ResourceGroupName, this.DataFactoryName, linkedService.Properties.Type);

        WindowsFormsWebAuthenticationDialog authenticationDialog = new WindowsFormsWebAuthenticationDialog(null);
        string authorization = authenticationDialog.AuthenticateAAD(authorizationSession.AuthorizationSession.Endpoint, new Uri("urn:ietf:wg:oauth:2.0:oob"));

        AzureDataLakeStoreLinkedService azureDataLakeStoreProperties = linkedService.Properties.TypeProperties as AzureDataLakeStoreLinkedService;
        if (azureDataLakeStoreProperties != null)
        {
            azureDataLakeStoreProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
            azureDataLakeStoreProperties.Authorization = authorization;
        }

        AzureDataLakeAnalyticsLinkedService azureDataLakeAnalyticsProperties = linkedService.Properties.TypeProperties as AzureDataLakeAnalyticsLinkedService;
        if (azureDataLakeAnalyticsProperties != null)
        {
            azureDataLakeAnalyticsProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
            azureDataLakeAnalyticsProperties.Authorization = authorization;
        }
    }

Para obtener más información sobre las clases de Data Factory que se usan en el código, vea los temas [Clase AzureDataLakeStoreLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx), [Clase AzureDataLakeAnalyticsLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx) y [Clase AuthorizationSessionGetResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx). Es preciso que agregue una referencia a la versión **2.9.10826.1824** de **Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll** para la clase WindowsFormsWebAuthenticationDialog utilizada en el código.
 

## Propiedades de tipo del conjunto de datos de Azure Data Lake

Para obtener una lista completa de las propiedades y las secciones JSON disponibles para definir conjuntos de datos, consulte el artículo [Creación de conjuntos de datos](data-factory-create-datasets.md). Las secciones como structure, availability y policy de un conjunto de datos JSON son similares en todos los tipos de conjunto de datos (SQL Azure, blob de Azure, tabla de Azure, etc.).

La sección **typeProperties** es diferente en cada tipo de conjunto de datos y proporciona información sobre la ubicación, el formato, etc. de los datos en el almacén de datos. La sección typeProperties para conjuntos de datos de tipo **AzureDataLakeStore** tiene las propiedades siguientes.

| Propiedad | Descripción | Obligatorio |
| :-------- | :----------- | :-------- |
| folderPath | Ruta de acceso al contenedor y a la carpeta del almacén de Azure Data Lake. | Sí |
| fileName | Nombre del archivo en el almacén de Azure Data Lake. La propiedad fileName es opcional y distingue entre mayúsculas y minúsculas. <br/><br/>Si especifica fileName, la actividad (incluida la copia) funciona en el archivo específico.<br/><br/>Cuando no se especifica fileName, la copia incluye todos los archivos de folderPath para el conjunto de datos de entrada.<br/><br/>Cuando no se especifica fileName para un conjunto de datos de salida, el nombre del archivo generado tendrá el formato siguiente: Data.<Guid>.txt (por ejemplo: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt). | No |
| partitionedBy | partitionedBy es una propiedad opcional. Puede usarla para especificar un folderPath dinámico y un nombre de archivo para datos de series temporales. Por ejemplo, se puede parametrizar folderPath por cada hora de datos. Consulte la sección Uso de la propiedad partitionedBy a continuación para obtener información detallada y ejemplos. | No |
| formato | Se admiten los siguientes tipos de formato: **TextFormat**, **AvroFormat**, **JsonFormat** y **OrcFormat**. Debe establecer la propiedad **type** de formato en uno de los siguientes valores. Vea las secciones [Especificación de TextFormat](#specifying-textformat), [Especificación de AvroFormat](#specifying-avroformat), [Especificación de JsonFormat](#specifying-jsonformat) y [Especificación de OrcFormat](#specifying-orcformat) para obtener más detalles. Si desea copiar los archivos tal cual entre los almacenes basados en archivos (copia binaria), puede omitir la sección de formato en las definiciones de conjunto de datos de entrada y salida.| No
| compresión | Especifique el tipo y el nivel de compresión de los datos. Los tipos admitidos son: **GZip**, **Deflate** y **BZip2** y los niveles admitidos son: **óptimo** y **más rápido**. Tenga en cuenta que actualmente la configuración de compresión no es compatible con los datos con formato **AvroFormat** u **OrcFormat**. Vea la sección [Compatibilidad de compresión](#compression-support) para más detalles. | No |

### Uso de la propiedad partitionedBy
Tal como se mencionó anteriormente, puede especificar un valor folderPath dinámico y un nombre de archivo para los datos de series temporales con la sección **partitionedBy**, macros de la Factoría de datos y las variables del sistema: SliceStart y SliceEnd, que indican las horas de inicio y de finalización de un segmento de datos especificado.

Consulte los artículos [Creación de conjuntos de datos](data-factory-create-datasets.md) y [Programación y ejecución](data-factory-scheduling-and-execution.md) para conocer más detalles sobre los conjuntos de datos de las series temporales, la programación y los segmentos.

#### Ejemplo 1

	"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
	"partitionedBy":
	[
	    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
	],

En el anterior ejemplo {Slice} se reemplaza por el valor de la variable del sistema SliceStart de la Factoría de datos en el formato (YYYYMMDDHH) especificado. SliceStart hace referencia a la hora de inicio del segmento. folderPath es diferente para cada segmento. Por ejemplo: wikidatagateway/wikisampledataout/2014100103 o wikidatagateway/wikisampledataout/2014100104

#### Ejemplo 2

	"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
	"fileName": "{Hour}.csv",
	"partitionedBy":
	 [
	    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
	    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
	    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
	    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
	],

En el ejemplo anterior, year, month, day y time de SliceStart se extraen en variables independientes que se usan en las propiedades folderPath y fileName.

[AZURE.INCLUDE [data-factory-file-format](../../includes/data-factory-file-format.md)]
 

### Compatibilidad con la compresión  
El procesamiento de grandes conjuntos de datos puede provocar cuellos de botella de E/S y red. Por lo tanto, los datos comprimidos en almacenes pueden no solo acelerar la transferencia de datos a través de la red y ahorrar espacio en disco, sino también introducir importantes mejoras de rendimiento en el procesamiento de macrodatos. En este momento, se admite la compresión para almacenes de datos basados en archivos como blobs de Azure o el sistema de archivos local.

Para especificar la compresión para un conjunto de datos, use la propiedad **compression** del conjunto de datos JSON como en el ejemplo siguiente:

	{  
		"name": "AzureDatalakeStoreDataSet",  
	  	"properties": {  
	    	"availability": {  
	    		"frequency": "Day",  
	    	  	"interval": 1  
	    	},  
	    	"type": "AzureDatalakeStore",  
	    	"linkedServiceName": "DataLakeStoreLinkedService",  
	    	"typeProperties": {  
	    		"fileName": "pagecounts.csv.gz",  
	    	  	"folderPath": "compression/file/",  
	    	  	"compression": {  
	    	    	"type": "GZip",  
	    	    	"level": "Optimal"  
	    	  	}  
    		}  
	  	}  
	}  
 
Observe que la sección **compression** tiene dos propiedades:
  
- **Type:** el códec de compresión, que puede ser **GZIP**, **Deflate** o **BZIP2**.
- **Level:** la relación de compresión, que puede ser **Optimal** o **Fastest**.
	- **Fastest:** la operación de compresión debe completarse tan pronto como sea posible, incluso si el archivo resultante no se comprime de forma óptima.
	- **Optimal:** la operación de compresión se debe comprimir óptimamente, incluso si tarda más tiempo en completarse.
	
	Consulte el tema [Nivel de compresión](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) para obtener más información.

Supongamos que el conjunto de datos de ejemplo anterior se usa como salida de una actividad de copia; la actividad de copia comprimirá los datos de salida con el códec GZIP con una proporción óptima y, luego, escribirá los datos comprimidos en un archivo denominado pagecounts.csv.gz en el almacén de Azure Data Lake.

Cuando se especifica la propiedad compression en un conjunto de datos de entrada JSON, la canalización puede leer los datos comprimidos desde el origen, y cuando se especifica la propiedad en un conjunto de datos de salida JSON, la actividad de copia puede escribir datos comprimidos en el destino. Estos son algunos escenarios de ejemplo:

- Leer datos comprimidos con GZIP de un almacén de Azure Data Lake, descomprimirlos y escribir los datos de resultado en una base de datos SQL de Azure. Defina el conjunto de datos del almacén de Azure Data Lake de entrada con la propiedad JSON compression en este caso.
- Leer datos de un archivo de texto sin formato del sistema de archivos local, comprimirlos con formato GZip y escribir los datos comprimidos en un almacén de Azure Data Lake. Defina un conjunto de datos de Azure Data Lake de salida con la propiedad JSON compression en este caso.
- Leer datos comprimidos con GZIP de un almacén de Azure Data Lake, descomprimirlos, comprimirlos con BZIP2 y escribir los datos de resultado en un almacén de Azure Data Lake. Defina el conjunto de datos del almacén de Azure Data Lake de entrada con el tipo de compresión establecido en GZIP y el conjunto de datos de salida con el tipo de compresión establecido en BZIP2 en este caso.


## Propiedades de tipo de actividad de copia de Azure Data Lake  
Para obtener una lista completa de las secciones y propiedades disponibles para definir actividades, consulte el artículo [Creación de canalizaciones](data-factory-create-pipelines.md). Propiedades como nombre, descripción, tablas de entrada y salida, varias directivas, etc. están disponibles para todos los tipos de actividades.

Por otro lado, las propiedades disponibles en la sección typeProperties de la actividad varían con cada tipo de actividad y, en caso de la actividad de copia, varían en función de los tipos de orígenes y receptores.

**AzureDataLakeStoreSource** admite las siguientes propiedades **typeProperties**:

| Propiedad | Descripción | Valores permitidos | Obligatorio |
| -------- | ----------- | -------------- | -------- |
| recursive | Indica si los datos se leen de forma recursiva de las subcarpetas o solo de la carpeta especificada. | True (valor predeterminado), False | No |



**AzureDataLakeStoreSink** admite las siguientes propiedades **typeProperties**:

| Propiedad | Descripción | Valores permitidos | Obligatorio |
| -------- | ----------- | -------------- | -------- |
| copyBehavior | Especifica el comportamiento de copia. | **PreserveHierarchy:** conserva la jerarquía de archivos en la carpeta de destino; es decir, la ruta de acceso relativa del archivo de origen que apunta a la carpeta de origen es idéntica a la ruta de acceso relativa del archivo de destino que apunta a la carpeta de destino.<br/><br/>**FlattenHierarchy:** todos los archivos de la carpeta de origen estarán en el primer nivel de la carpeta de destino. Los archivos de destino tendrán un nombre generado automáticamente.<br/><br/>**MergeFiles:** combina todos los archivos de la carpeta de origen en un archivo. Si se especifica el nombre de archivo/blob, el nombre de archivo combinado sería el nombre especificado; de lo contrario, sería el nombre de archivo generado automáticamente. | No |


[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

[AZURE.INCLUDE [data-factory-type-conversion-sample](../../includes/data-factory-type-conversion-sample.md)]

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

## Rendimiento y optimización  
Consulte [Guía de optimización y rendimiento de la actividad de copia](data-factory-copy-activity-performance.md) para obtener más información sobre los factores clave que afectan al rendimiento del movimiento de datos (actividad de copia) en Data Factory de Azure y las diversas formas de optimizarlo.

<!---HONumber=AcomDC_0727_2016-->