<properties
	pageTitle="Uso del SDK de iOS para Aplicaciones móviles de Azure"
	description="Uso del SDK de iOS para Aplicaciones móviles de Azure"
	services="app-service\mobile"
	documentationCenter="ios"
	authors="krisragh"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="06/30/2016"
	ms.author="krisragh"/>

# Uso de la biblioteca de cliente de iOS para Aplicaciones móviles de Azure

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

Esta guía muestra cómo realizar algunas tareas comunes a través del [SDK de iOS de Aplicaciones móviles de Azure](https://github.com/Azure/azure-mobile-apps-ios-client/blob/master/README.md#ios-client-sdk). Si está familiarizado con Aplicaciones móviles de Azure, complete primero [Inicio rápido de Aplicaciones móviles de Azure] para crear un back-end, crear una tabla y descargar un proyecto de Xcode para iOS pregenerada. En esta guía, nos centramos en el SDK de iOS de cliente. Para obtener más información sobre el SDK del lado servidor de .NET para el back-end, consulte [Trabajo con el back-end de .NET](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)

## Documentación de referencia

La documentación de referencia para el SDK de cliente de iOS se encuentra aquí: [Referencia de cliente de iOS para aplicaciones móviles de Azure](http://azure.github.io/azure-mobile-apps-ios-client/).

##<a name="Setup"></a>Configuración y requisitos previos

En esta guía se asume que ha creado un back-end con una tabla. En esta guía se asume que la tabla tiene el mismo esquema que las tablas de dichos tutoriales. En esta guía también se supone que en el código se hace referencia a `MicrosoftAzureMobile.framework` e importa `MicrosoftAzureMobile/MicrosoftAzureMobile.h`.

##<a name="create-client"></a>Creación del cliente

Para obtener acceso al back-end de Aplicaciones móviles de Azure en el proyecto, cree un `MSClient`. Reemplace `AppUrl` por la dirección URL de la aplicación. Puede dejar `gatewayURLString` y `applicationKey` vacías. Si configura una puerta de enlace para la autenticación, rellene `gatewayURLString` con la dirección URL de la puerta de enlace.

**Objective-C**:

```
MSClient *client = [MSClient clientWithApplicationURLString:@"AppUrl"];
```

**SWIFT**:

```
let client = MSClient(applicationURLString: "AppUrl")
```


##<a name="table-reference"></a>Creación de una referencia de tabla

Para acceder a los datos o actualizarlos, cree una referencia a la tabla de back-end. Reemplace `TodoItem` por el nombre de la tabla.

**Objective-C**:

```
MSTable *table = [client tableWithName:@"TodoItem"];
```

**SWIFT**:

```
let table = client.tableWithName("TodoItem")
```


##<a name="querying"></a>Consulta de datos

Para crear una consulta de base de datos, consulte el objeto `MSTable`. La consulta siguiente obtiene todos los elementos de `TodoItem` y registra el texto de cada elemento.

**Objective-C**:

```
[table readWithCompletion:^(MSQueryResult *result, NSError *error) {
		if(error) { // error is nil if no error occured
				NSLog(@"ERROR %@", error);
		} else {
				for(NSDictionary *item in result.items) { // items is NSArray of records that match query
						NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
				}
		}
}];
```

**SWIFT**:

```
table.readWithCompletion { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let items = result?.items {
        for item in items {
            print("Todo Item: ", item["text"])
        }
    }
}
```

##<a name="filtering"></a>Filtro de datos devueltos

Para filtrar los resultados, hay muchas opciones disponibles.

Para filtrar mediante un predicado, use un `NSPredicate` y `readWithPredicate`. Los siguientes filtros devolvieron datos para buscar solo los elementos Todo incompletos.

**Objective-C**:

```
// Create a predicate that finds items where complete is false
NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];
// Query the TodoItem table
[table readWithPredicate:predicate completion:^(MSQueryResult *result, NSError *error) {
		if(error) {
				NSLog(@"ERROR %@", error);
		} else {
				for(NSDictionary *item in result.items) {
						NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
				}
		}
}];
```

**SWIFT**:

```
// Create a predicate that finds items where complete is false
let predicate =  NSPredicate(format: "complete == NO")
// Query the TodoItem table
table.readWithPredicate(predicate) { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let items = result?.items {
        for item in items {
            print("Todo Item: ", item["text"])
        }
    }
}
```

##<a name="query-object"></a>Uso de MSQuery

Para realizar una consulta compleja (como de ordenación y paginación), cree un objeto `MSQuery` directamente o mediante un predicado:

**Objective-C**:

```
MSQuery *query = [table query];
MSQuery *query = [table queryWithPredicate: [NSPredicate predicateWithFormat:@"complete == NO"]];
```

**SWIFT**:

```
let query = table.query()
let query = table.queryWithPredicate(NSPredicate(format: "complete == NO"))
```

`MSQuery` le permite controlar varios comportamientos de consulta, incluidos los siguientes. Ejecutar una consulta `MSQuery` llamando a `readWithCompletion` en él, tal como se muestra en el ejemplo siguiente.
* Especificar el orden de los resultados
* Limitar qué campos se devuelven
* Limitar el número de registros que se devolverán
* Especificar el recuento total en la respuesta
* Especificar parámetros de la cadena de consulta personalizados en la solicitud
* Aplicar funciones adicionales


## <a name="sorting"></a>Ordenación de datos con MSQuery

Para ordenar los resultados, echemos un vistazo a un ejemplo. Para ordenar en primer lugar en orden ascendente por campo `text` y, a continuación, por orden descendente por campo `completion`, invoque `MSQuery`:

**Objective-C**:

```
[query orderByAscending:@"text"];
[query orderByDescending:@"complete"];
[query readWithCompletion:^(MSQueryResult *result, NSError *error) {
		if(error) {
				NSLog(@"ERROR %@", error);
		} else {
				for(NSDictionary *item in result.items) {
						NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
				}
		}
}];
```

**SWIFT**:

```
query.orderByAscending("text")
query.orderByDescending("complete")
query.readWithCompletion { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let items = result?.items {
        for item in items {
            print("Todo Item: ", item["text"])
        }
    }
}
```


## <a name="selecting"></a><a name="parameters"></a>Limitación de campos y expansión de los parámetros de cadena de consulta con MSQuery

Para limitar los campos que se devolverán en una consulta, especifique los nombres de los campos en la propiedad **selectFields**. Esto solamente devuelven los campos de texto y aquellos que se hayan rellenado:

**Objective-C**:

```
query.selectFields = @[@"text", @"complete"];
```

**Swift**:

```
query.selectFields = ["text", "complete"]
```

Para incluir parámetros de cadena de consulta adicionales en la solicitud al servidor (por ejemplo, porque los utiliza un script de servidor personalizado), introduzca `query.parameters`:

**Objective-C**:

```
query.parameters = @{
	@"myKey1" : @"value1",
	@"myKey2" : @"value2",
};
```

**Swift**:

```
query.parameters = ["myKey1": "value1", "myKey2": "value2"]
```

##<a name="inserting"></a>Insertar datos

Para insertar una nueva fila en la tabla, cree un nuevo `NSDictionary` e invoque `table insert`. Servicios móviles genera automáticamente columnas nuevas basadas en `NSDictionary` si [Esquema dinámico] no está deshabilitado.

Si no se proporciona `id`, el backend genera automáticamente un nuevo identificador único. Proporcione su propio `id` para utilizar direcciones de correo electrónico, nombres de usuario o sus propios valores personalizados como Id. Proporcionar su propio ID puede facilitar las combinaciones y la lógica de la base de datos de tipo empresarial.

`result` contiene el nuevo elemento que se insertó; dependiendo de la lógica del servidor, puede tener datos modificados o adicionales en comparación con lo que se pasó al servidor.

**Objective-C**:

```
NSDictionary *newItem = @{@"id": @"custom-id", @"text": @"my new item", @"complete" : @NO};
[table insert:newItem completion:^(NSDictionary *result, NSError *error) {
	if(error) {
		NSLog(@"ERROR %@", error);
	} else {
		NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
	}
}];
```

**Swift**:

```
let newItem = ["id": "custom-id", "text": "my new item", "complete": false]
table.insert(newItem) { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let item = result {
        print("Todo Item: ", item["text"])
    }
}
```

##<a name="modifying"></a>Modificación de datos

Para actualizar una fila existente, modifique un elemento y llame a `update`:

**Objective-C**:

```
NSMutableDictionary *newItem = [oldItem mutableCopy]; // oldItem is NSDictionary
[newItem setValue:@"Updated text" forKey:@"text"];
[table update:newItem completion:^(NSDictionary *result, NSError *error) {
	if(error) {
		NSLog(@"ERROR %@", error);
	} else {
		NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
	}
}];
```

**Swift**:

```
if let newItem = oldItem.mutableCopy() as? NSMutableDictionary {
    newItem["text"] = "Updated text"
    table2.update(newItem as [NSObject: AnyObject], completion: { (result, error) -> Void in
        if let err = error {
            print("ERROR ", err)
        } else if let item = result {
            print("Todo Item: ", item["text"])
        }
    })
}
```

Como alternativa, proporcione el identificador de fila y el campo actualizado:

**Objective-C**:

```
[table update:@{@"id":@"custom-id", @"text":"my EDITED item"} completion:^(NSDictionary *result, NSError *error) {
	if(error) {
		NSLog(@"ERROR %@", error);
	} else {
		NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
	}
}];
```

**SWIFT**:

```
table.update(["id": "custom-id", "text": "my EDITED item"]) { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let item = result {
        print("Todo Item: ", item["text"])
    }
}
```

Como mínimo, debe establecerse el atributo `id` al realizar actualizaciones.

##<a name="deleting"></a>Eliminación de datos

Para eliminar un elemento, invoque `delete` con el elemento:

**Objective-C**:

```
[table delete:item completion:^(id itemId, NSError *error) {
	if(error) {
		NSLog(@"ERROR %@", error);
	} else {
		NSLog(@"Todo Item ID: %@", itemId);
	}
}];
```

**Swift**:

```
table.delete(newItem as [NSObject: AnyObject]) { (itemId, error) in
    if let err = error {
        print("ERROR ", err)
    } else {
        print("Todo Item ID: ", itemId)
    }
}
```

O bien elimínelo proporcionando un identificador de fila:

**Objective-C**:

```
[table deleteWithId:@"37BBF396-11F0-4B39-85C8-B319C729AF6D" completion:^(id itemId, NSError *error) {
	if(error) {
		NSLog(@"ERROR %@", error);
	} else {
		NSLog(@"Todo Item ID: %@", itemId);
	}
}];
```

**SWIFT**:

```
table.deleteWithId("37BBF396-11F0-4B39-85C8-B319C729AF6D") { (itemId, error) in
    if let err = error {
        print("ERROR ", err)
    } else {
        print("Todo Item ID: ", itemId)
    }
}
```

Como mínimo, el atributo `id` debe establecerse a la hora de efectuar eliminaciones.

##<a name="customapi"></a>Llamada a una API personalizada

Con una API personalizada, puede exponer cualquier funcionalidad de back-end. No necesita asignar a una operación de tabla. No solo obtendrá más control sobre la mensajería, también podrá leer o establecer encabezados y cambiar el formato del cuerpo de la respuesta. Para aprender cómo crear una API personalizada en el back-end, lea [API personalizadas](app-service-mobile-node-backend-how-to-use-server-sdk.md#work-easy-apis).

Para llamar a una API personalizada, llame a `MSClient.invokeAPI`, como se muestra a continuación. El contenido de la solicitud y la respuesta se tratan como JSON. Para utilizar otros tipos de medios, [use la otra sobrecarga de `invokeAPI`](http://azure.github.io/azure-mobile-services/iOS/v3/Classes/MSClient.html#//api/name/invokeAPI:data:HTTPMethod:parameters:headers:completion:).

Para realizar una solicitud `GET` en lugar de una solicitud `POST`, establezca el parámetro `HTTPMethod` en `"GET"` y el parámetro `body` en `nil` (puesto que las solicitudes GET no tienen cuerpos de mensaje). Si la API personalizada es compatible con otros verbos HTTP, cambie `HTTPMethod` de acuerdo a ello.

**Objective-C**:
```
[self.client invokeAPI:@"sendEmail"
                  body:@{ @"contents": @"Hello world!" }
            HTTPMethod:@"POST"
            parameters:@{ @"to": @"bill@contoso.com", @"subject" : @"Hi!" }
               headers:nil
            completion: ^(NSData *result, NSHTTPURLResponse *response, NSError *error) {
                if(error) {
                    NSLog(@"ERROR %@", error);
                } else {
                    // Do something with result
                }
            }];
```

**Swift**:

```
client.invokeAPI("sendEmail",
            body: [ "contents": "Hello World" ],
            HTTPMethod: "POST",
            parameters: [ "to": "bill@contoso.com", "subject" : "Hi!" ],
            headers: nil)
            {
                (result, response, error) -> Void in
                if let err = error {
                    print("ERROR ", err)
                } else if let res = result {
                          // Do something with result
                }
        }
```

##<a name="templates"></a>Procedimiento: Registro de plantillas push para enviar notificaciones entre plataformas

Para registrar plantillas, basta con pasar las plantillas con el método **client.push registerDeviceToken** en la aplicación cliente.

**Objective-C**:

```
[client.push registerDeviceToken:deviceToken template:iOSTemplate completion:^(NSError *error) {
	if(error) {
		NSLog(@"ERROR %@", error);
	}
}];
```

**SWIFT**:

```
    client.push?.registerDeviceToken(NSData(), template: iOSTemplate, completion: { (error) in
        if let err = error {
            print("ERROR ", err)
        }
    })
```

Las plantillas serán de tipo NSDictionary y pueden contener varias plantillas con el formato siguiente:

**Objective-C**:

```
NSDictionary *iOSTemplate = @{ @"templateName": @{ @"body": @{ @"aps": @{ @"alert": @"$(message)" } } } };
```

**Swift**:

```
let iOSTemplate = ["templateName": ["body": ["aps": ["alert": "$(message)"]]]]
```

Tenga en cuenta que se eliminará todas las etiquetas por seguridad. Para agregar etiquetas a las instalaciones o plantillas dentro de las instalaciones, vea [Trabajar con el SDK del servidor back-end de .NET para Aplicaciones móviles de Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#tags).

Para enviar notificaciones mediante estas plantillas registradas, trabaje con [API de Centros de notificaciones](https://msdn.microsoft.com/library/azure/dn495101.aspx).

##<a name="errors"></a>Gestión de errores

Al realizar una llamada a un servicio móvil, el bloque de finalización contiene un parámetro `NSError`. En caso de producirse un error, este parámetro no será nulo. En su código, debe marcar este parámetro y administrar el error según sea necesario, como se muestra en los fragmentos de código anteriores.

El archivo [`<WindowsAzureMobileServices/MSError.h>`](https://github.com/Azure/azure-mobile-services/blob/master/sdk/iOS/src/MSError.h) define las constantes `MSErrorResponseKey`, `MSErrorRequestKey` y `MSErrorServerItemKey` para conseguir más datos relacionados con el error, que se pueden obtener de la siguiente manera:

**Objective-C**:

```
NSDictionary *serverItem = [error.userInfo objectForKey:MSErrorServerItemKey];
```

**Swift**:

```
let serverItem = error.userInfo[MSErrorServerItemKey]
```

Además, el archivo define constantes para cada código de error, que se pueden usar como se indica a continuación:

**Objective-C**:

```
if (error.code == MSErrorPreconditionFailed) {
```

**Swift**:

```
if (error.code == MSErrorPreconditionFailed) {
```

## <a name="adal"></a>Autenticación de usuarios con la biblioteca de autenticación de Active Directory

Puede utilizar la biblioteca de autenticación de Active Directory (ADAL) para iniciar la sesión de los usuarios en su aplicación con Azure Active Directory. Con frecuencia, esta opción es preferible al uso de los métodos `loginAsync()`, ya que proporciona una experiencia UX más nativa y permite personalizaciones adicionales.

1. Configure su back-end de aplicación móvil para el inicio de sesión en AAD siguiendo el tutorial [Configuración de la aplicación del Servicio de aplicaciones para usar el inicio de sesión de Azure Active Directory](app-service-mobile-how-to-configure-active-directory-authentication.md). Asegúrese de completar el paso opcional de registrar una aplicación cliente nativa. Para iOS, se recomienda (aunque no es obligatorio) que el URI de redirección tenga el formato `<app-scheme>://<bundle-id>`. Para más detalles, consulte el [inicio rápido de iOS para ADAL](active-directory-devquickstarts-ios.md#em1-determine-what-your-redirect-uri-will-be-for-iosem).

2. Instale ADAL mediante Cocoapods. Edite el podfile para incluir lo siguiente; sustituya **YOUR-PROJECT** por el nombre de su proyecto de Xcode:

		source 'https://github.com/CocoaPods/Specs.git'
		link_with ['YOUR-PROJECT']
		xcodeproj 'YOUR-PROJECT'
y el POD:

		pod 'ADALiOS'

3. Mediante el Terminal, ejecute `pod install` desde el directorio que contiene el proyecto y abra el área de trabajo del Xcode generado (no el proyecto).

4. Agregue el siguiente código a la aplicación, según el lenguaje que esté utilizando. En cada caso, realice las sustituciones siguientes:

* Reemplace **INSERT-AUTHORITY-HERE** por el nombre del inquilino en el que aprovisionó la aplicación. El formato debe ser https://login.windows.net/contoso.onmicrosoft.com. Este valor se puede copiar de la pestaña Dominio de Azure Active Directory en el [Portal de Azure clásico].

* Reemplace **INSERT-RESOURCE-ID-HERE** por el id. de cliente del back-end de la aplicación móvil. Puede obtenerlo en la pestaña **Avanzadas** en **Configuración de Azure Active Directory** en el portal.

* Reemplace **INSERT-CLIENT-ID-HERE** por el id. de cliente que copió de la aplicación cliente nativa.

* Reemplace **INSERT-REDIRECT-URI-HERE** por el punto de conexión _/.auth/login/done_ del sitio, mediante el esquema HTTPS. Este valor debería ser similar a \_https://contoso.azurewebsites.net/.auth/login/done_.

**Objective-C**:

	#import <ADALiOS/ADAuthenticationContext.h>
	#import <ADALiOS/ADAuthenticationSettings.h>
	// ...
	- (void) authenticate:(UIViewController*) parent
	           completion:(void (^) (MSUser*, NSError*))completionBlock;
	{
	    NSString *authority = @"INSERT-AUTHORITY-HERE";
	    NSString *resourceId = @"INSERT-RESOURCE-ID-HERE";
	    NSString *clientId = @"INSERT-CLIENT-ID-HERE";
	    NSURL *redirectUri = [[NSURL alloc]initWithString:@"INSERT-REDIRECT-URI-HERE"];
	    ADAuthenticationError *error;
	    ADAuthenticationContext *authContext = [ADAuthenticationContext authenticationContextWithAuthority:authority error:&error];
	    authContext.parentController = parent;
	    [ADAuthenticationSettings sharedInstance].enableFullScreen = YES;
	    [authContext acquireTokenWithResource:resourceId
	                                 clientId:clientId
	                              redirectUri:redirectUri
	                          completionBlock:^(ADAuthenticationResult *result) {
	                              if (result.status != AD_SUCCEEDED)
	                              {
	                                  completionBlock(nil, result.error);;
	                              }
	                              else
	                              {
	                                  NSDictionary *payload = @{
	                                                            @"access_token" : result.tokenCacheStoreItem.accessToken
	                                                            };
	                                  [client loginWithProvider:@"aad" token:payload completion:completionBlock];
	                              }
	                          }];
	}


**Swift**:

	// add the following imports to your bridging header:
	//		#import <ADALiOS/ADAuthenticationContext.h>
	//		#import <ADALiOS/ADAuthenticationSettings.h>

	func authenticate(parent: UIViewController, completion: (MSUser?, NSError?) -> Void) {
		let authority = "INSERT-AUTHORITY-HERE"
		let resourceId = "INSERT-RESOURCE-ID-HERE"
		let clientId = "INSERT-CLIENT-ID-HERE"
		let redirectUri = NSURL(string: "INSERT-REDIRECT-URI-HERE")
		var error: AutoreleasingUnsafeMutablePointer<ADAuthenticationError?> = nil
		let authContext = ADAuthenticationContext(authority: authority, error: error)
		authContext.parentController = parent
		ADAuthenticationSettings.sharedInstance().enableFullScreen = true
		authContext.acquireTokenWithResource(resourceId, clientId: clientId, redirectUri: redirectUri) { (result) in
		        if result.status != AD_SUCCEEDED {
		            completion(nil, result.error)
		        }
		        else {
		            let payload: [String: String] = ["access_token": result.tokenCacheStoreItem.accessToken]
		            client.loginWithProvider("aad", token: payload, completion: completion)
		        }
    		}
	}


## <a name="facebook-sdk"></a>Autenticación de usuarios con SDK de Facebook para iOS

Puede usar el SDK de Facebook para iOS para que los usuarios inicien sesión en su aplicación con Facebook. Con frecuencia, esta opción es preferible al uso de los métodos `loginAsync()`, ya que proporciona una experiencia de usuario más nativa y permite personalizaciones adicionales.

1. Configure el back-end de aplicación móvil para el inicio de sesión en Facebook siguiendo el tutorial [Configuración de la aplicación Servicio de aplicaciones para usar el inicio de sesión de Facebook](app-service-mobile-how-to-configure-facebook-authentication.md).

2. Instale el SDK de Facebook para iOS siguiendo la documentación [SDK de Facebook para iOS: primeros pasos](https://developers.facebook.com/docs/ios/getting-started). En lugar de crear una nueva aplicación, puede agregar la plataforma iOS en el registro existente.

    La documentación de Facebook incluye algún código de Objective-C en el delegado de la aplicación. Si está utilizando **Swift**, puede usar las siguientes traducciones para AppDelegate.swift:
  
		// Add the following import to your bridging header:
		//		#import <FBSDKCoreKit/FBSDKCoreKit.h>
		
		func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject : AnyObject]?) -> Bool {
			FBSDKApplicationDelegate.sharedInstance().application(application, didFinishLaunchingWithOptions: launchOptions)
			// Add any custom logic here.
			return true
		}

		func application(application: UIApplication, openURL url: NSURL, sourceApplication: String?, annotation: AnyObject?) -> Bool {
			let handled = FBSDKApplicationDelegate.sharedInstance().application(application, openURL: url, sourceApplication: sourceApplication, annotation: annotation)
			// Add any custom logic here.
			return handled
		}

3. Además de agregar `FBSDKCoreKit.framework` al proyecto, también puede agregar una referencia a `FBSDKLoginKit.framework` de la misma manera.

4. Agregue el siguiente código a la aplicación, según el lenguaje que esté utilizando.

**Objective-C**:

	#import <FBSDKLoginKit/FBSDKLoginKit.h>
	#import <FBSDKCoreKit/FBSDKAccessToken.h>
	// ...
	- (void) authenticate:(UIViewController*) parent
	           completion:(void (^) (MSUser*, NSError*)) completionBlock;
	{	    
	    FBSDKLoginManager *loginManager = [[FBSDKLoginManager alloc] init];
	    [loginManager
	     logInWithReadPermissions: @[@"public_profile"]
	     fromViewController:parent
	     handler:^(FBSDKLoginManagerLoginResult *result, NSError *error) {
	         if (error) {
	             completionBlock(nil, error);
	         } else if (result.isCancelled) {
	             completionBlock(nil, error);
	         } else {
	             NSDictionary *payload = @{
	                                       @"access_token":result.token.tokenString
	                                       };
	             [client loginWithProvider:@"facebook" token:payload completion:completionBlock];
	         }
	     }];
	}


**Swift**:

	// Add the following imports to your bridging header:
	//		#import <FBSDKLoginKit/FBSDKLoginKit.h>
	//		#import <FBSDKCoreKit/FBSDKAccessToken.h>
	
	func authenticate(parent: UIViewController, completion: (MSUser?, NSError?) -> Void) {
		let loginManager = FBSDKLoginManager()
		loginManager.logInWithReadPermissions(["public_profile"], fromViewController: parent) { (result, error) in
			if (error != nil) {
				completion(nil, error)
			}
			else if result.isCancelled {
				completion(nil, error)
			}
			else {
				let payload: [String: String] = ["access_token": result.token.tokenString]
				client.loginWithProvider("facebook", token: payload, completion: completion)
			}
		}
	}

## <a name="twitter-fabric"></a>Autenticación de usuarios con Fabric de Twitter para iOS

Puede usar Fabric para iOS para que los usuarios inicien sesión en su aplicación con Twitter. Con frecuencia, esta opción es preferible al uso de los métodos `loginAsync()`, ya que proporciona una experiencia de usuario más nativa y permite personalizaciones adicionales.

1. Configure su back-end de aplicación móvil para el inicio de sesión en Twitter siguiendo el tutorial [Configuración de la aplicación Servicio de aplicaciones para usar el inicio de sesión de Twitter](app-service-mobile-how-to-configure-twitter-authentication.md).

2. Agregue Fabric al proyecto siguiendo la documentación [Fabric for iOS - Getting Started](https://docs.fabric.io/ios/fabric/getting-started.html) (Fabric para iOS: primeros pasos) y configurando TwitterKit.

    > [AZURE.NOTE] De forma predeterminada, Fabric creará una nueva aplicación de Twitter para usted. Puede cambiarla registrando la clave de usuario y el secreto de consumidor que creó anteriormente mediante los fragmentos de código siguientes. Asimismo, puede reemplazar los valores de clave de usuario y de secreto de consumidor que proporcione al Servicio de aplicaciones por los valores que aparecen en [Fabric Dashboard](https://www.fabric.io/home) (Panel de Fabric). Si elige esta opción, asegúrese de establecer la dirección URL de devolución de llamada en un valor de marcador de posición, como `https://<yoursitename>.azurewebsites.net/.auth/login/twitter/callback`.

	Si decide utilizar los secretos que creó anteriormente, agregue lo siguiente al delegado de la aplicación:
	
	**Objective-C**:

		#import <Fabric/Fabric.h>
		#import <TwitterKit/TwitterKit.h>
		// ...
		- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
		{
		    [[Twitter sharedInstance] startWithConsumerKey:@"your_key" consumerSecret:@"your_secret"];
		    [Fabric with:@[[Twitter class]]];
			// Add any custom logic here.
		    return YES;
		}
		
	**Swift**:
	
		import Fabric
		import TwitterKit
		// ...
		func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject : AnyObject]?) -> Bool {
			Twitter.sharedInstance().startWithConsumerKey("your_key", consumerSecret: "your_secret")
			Fabric.with([Twitter.self])
			// Add any custom logic here.
			return true
		}
	
3. Agregue el siguiente código a la aplicación, según el lenguaje que esté utilizando.

**Objective-C**:

	#import <TwitterKit/TwitterKit.h>
	// ...
	- (void)authenticate:(UIViewController*)parent completion:(void (^) (MSUser*, NSError*))completionBlock
	{
		[[Twitter sharedInstance] logInWithCompletion:^(TWTRSession *session, NSError *error) {
			if (session) {
				NSDictionary *payload = @{
											@"access_token":session.authToken,
											@"access_token_secret":session.authTokenSecret
										};
				[client loginWithProvider:@"twitter" token:payload completion:completionBlock];
			} else {
				completionBlock(nil, error);
			}
	    }];
	}

**Swift**:

	import TwitterKit
	// ...
	func authenticate(parent: UIViewController, completion: (MSUser?, NSError?) -> Void) {
		let client = self.table!.client
		Twitter.sharedInstance().logInWithCompletion { session, error in
			if (session != nil) {
				let payload: [String: String] = ["access_token": session!.authToken, "access_token_secret": session!.authTokenSecret]
				client.loginWithProvider("twitter", token: payload, completion: completion)
			} else {
				completion(nil, error)
			}
		}
	}

<!-- Anchors. -->

[What is Mobile Services]: #what-is
[Concepts]: #concepts
[Setup and Prerequisites]: #Setup
[How to: Create the Mobile Services client]: #create-client
[How to: Create a table reference]: #table-reference
[How to: Query data from a mobile service]: #querying
[Filter returned data]: #filtering
[Sort returned data]: #sorting
[Return data in pages]: #paging
[Select specific columns]: #selecting
[How to: Bind data to the user interface]: #binding
[How to: Insert data into a mobile service]: #inserting
[How to: Modify data in a mobile service]: #modifying
[How to: Authenticate users]: #authentication
[Cache authentication tokens]: #caching-tokens
[How to: Upload images and large files]: #blobs
[How to: Handle errors]: #errors
[How to: Design unit tests]: #unit-testing
[How to: Customize the client]: #customizing
[Customize request headers]: #custom-headers
[Customize data type serialization]: #custom-serialization
[Next Steps]: #next-steps
[How to: Use MSQuery]: #query-object

<!-- Images. -->

<!-- URLs. -->
[Inicio rápido de Aplicaciones móviles de Azure]: app-service-mobile-ios-get-started.md

[Add Mobile Services to Existing App]: /develop/mobile/tutorials/get-started-data
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Validate and modify data in Mobile Services by using server scripts]: /develop/mobile/tutorials/validate-modify-and-augment-data-ios
[Mobile Services SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Authentication]: /develop/mobile/tutorials/get-started-with-users-ios
[iOS SDK]: https://developer.apple.com/xcode

[Handling Expired Tokens]: http://go.microsoft.com/fwlink/p/?LinkId=301955
[Live Connect SDK]: http://go.microsoft.com/fwlink/p/?LinkId=301960
[Permissions]: http://msdn.microsoft.com/library/windowsazure/jj193161.aspx
[Service-side Authorization]: mobile-services-javascript-backend-service-side-authorization.md
[Use scripts to authorize users]: /develop/mobile/tutorials/authorize-users-in-scripts-ios
[Esquema dinámico]: http://go.microsoft.com/fwlink/p/?LinkId=296271
[How to: access custom parameters]: /develop/mobile/how-to-guides/work-with-server-scripts#access-headers
[Create a table]: http://msdn.microsoft.com/library/windowsazure/jj193162.aspx
[NSDictionary object]: http://go.microsoft.com/fwlink/p/?LinkId=301965
[ASCII control codes C0 and C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[CLI to manage Mobile Services tables]: ../virtual-machines-command-line-tools.md#Mobile_Tables
[Conflict-Handler]: mobile-services-ios-handling-conflicts-offline-data.md#add-conflict-handling

<!---HONumber=AcomDC_0803_2016-->