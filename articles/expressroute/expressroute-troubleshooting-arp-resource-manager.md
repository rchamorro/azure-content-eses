<properties 
   pageTitle="Guía de solución de problemas de ExpressRoute: obtención de tablas ARP | Microsoft Azure"
   description="En esta página se proporcionan instrucciones sobre cómo obtener tablas ARP para un circuito ExpressRoute"
   documentationCenter="na"
   services="expressroute"
   authors="ganesr"
   manager="carolz"
   editor="tysonn"/>
<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="06/06/2016"
   ms.author="ganesr"/>

#Guía de solución de problemas de ExpressRoute: obtención de tablas ARP en el modelo de implementación de Resource Manager

> [AZURE.SELECTOR]
[PowerShell - Resource Manager](expressroute-troubleshooting-arp-resourcemanager.md)
[PowerShell - Classic](expressroute-troubleshooting-arp-classic.md)

Este artículo le guía por los pasos necesarios para comprender las tablas ARP del circuito ExpressRoute.

>[AZURE.IMPORTANT] Este documento está pensado para ayudarle a diagnosticar y corregir problemas sencillos. No pretende sustituir al soporte técnico de Microsoft. Si no puede resolver el problema siguiendo las instrucciones que se describen a continuación, abra una incidencia de soporte técnico dirigida al [soporte técnico de Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## Protocolo de resolución de direcciones (ARP) y tablas ARP
El Protocolo de resolución de direcciones (ARP) es un protocolo de nivel 2 definido en [RFC 826](https://tools.ietf.org/html/rfc826). ARP se utiliza para asignar la dirección Ethernet (dirección MAC) con una dirección IP.

La tabla ARP proporciona una asignación de la dirección IPv4 y la dirección MAC para un emparejamiento determinado. La tabla ARP de un emparejamiento de circuito ExpressRoute proporciona la siguiente información para cada interfaz (principal y secundaria)

1. Asignación de la dirección IP de la interfaz del enrutador local a la dirección MAC
2. Asignación de la dirección IP de la interfaz del enrutador de ExpressRoute a la dirección MAC
3. Antigüedad de la asignación

Las tablas ARP pueden ayudar a validar la configuración de nivel 2 y a solucionar los problemas básicos de conectividad de nivel 2.

Ejemplo de tabla ARP:

		Age InterfaceProperty IpAddress  MacAddress    
		--- ----------------- ---------  ----------    
		 10 On-Prem           10.0.0.1 ffff.eeee.dddd
		  0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


En la siguiente sección se proporciona información sobre cómo puede ver las tablas ARP que ven los enrutadores de borde de ExpressRoute.

## Requisitos previos para comprender las tablas ARP

Asegúrese de que cumple los siguientes requisitos previos antes de seguir adelante

 - Un circuito ExpressRoute válido configurado con al menos un emparejamiento. El circuito debe estar completamente configurado por el proveedor de conectividad. Usted (o su proveedor de conectividad) debe haber configurado al menos uno de los emparejamientos (privado de Azure, público de Azure y Microsoft) en este circuito.
 - Intervalos de direcciones IP usados para configurar los emparejamientos (privado de Azure, público de Azure y Microsoft). Revise los ejemplos de asignación de dirección IP en [Requisitos de enrutamiento de ExpressRoute](expressroute-routing.md) para comprender cómo se asignan las direcciones IP a las interfaces en su sitio y el sitio de ExpressRoute. Puede obtener información sobre la configuración de emparejamiento en la página [Creación y modificación del enrutamiento de un circuito ExpressRoute](expressroute-howto-routing-arm.md).
 - Información del equipo de red o del proveedor de conectividad sobre las direcciones MAC de las interfaces usadas con estas direcciones IP.
 - Debe tener como mínimo el módulo más reciente de PowerShell para Azure (versión 1.50 o superior).

## Obtención de las tablas ARP para el circuito ExpressRoute
En esta sección se proporcionan instrucciones sobre cómo ver las tablas ARP por emparejamiento mediante PowerShell. Usted o su proveedor de conectividad deben haber configurado el emparejamiento antes de seguir adelante. Cada circuito tiene dos rutas de acceso (principal y secundaria). Puede comprobar en la tabla ARP cada ruta de acceso de forma independiente.

### Tablas ARP para el emparejamiento privado de Azure
El siguiente cmdlet proporciona el emparejamiento privado de Azure para las tablas ARP:

		# Required Variables
		$RG = "<Your Resource Group Name Here>"
		$Name = "<Your ExpressRoute Circuit Name Here>"
		
		# ARP table for Azure private peering - Primary path
		Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePrivatePeering -DevicePath Primary
		
		# ARP table for Azure private peering - Secodary path
		Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePrivatePeering -DevicePath Secondary 

A continuación se muestra el resultado de ejemplo de una de las rutas de acceso:

		Age InterfaceProperty IpAddress  MacAddress    
		--- ----------------- ---------  ----------    
		 10 On-Prem           10.0.0.1 ffff.eeee.dddd
		  0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


### Tablas ARP para el emparejamiento público de Azure
El siguiente cmdlet proporciona las tablas ARP para el emparejamiento público de Azure:

		# Required Variables
		$RG = "<Your Resource Group Name Here>"
		$Name = "<Your ExpressRoute Circuit Name Here>"
		
		# ARP table for Azure public peering - Primary path
		Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePublicPeering -DevicePath Primary
		
		# ARP table for Azure public peering - Secodary path
		Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType AzurePublicPeering -DevicePath Secondary 


A continuación se muestra el resultado de ejemplo de una de las rutas de acceso:

		Age InterfaceProperty IpAddress  MacAddress    
		--- ----------------- ---------  ----------    
		 10 On-Prem           64.0.0.1 ffff.eeee.dddd
		  0 Microsoft         64.0.0.2 aaaa.bbbb.cccc


### Tablas ARP para el emparejamiento de Microsoft
El siguiente cmdlet proporciona las tablas ARP para el emparejamiento de Microsoft:

		# Required Variables
		$RG = "<Your Resource Group Name Here>"
		$Name = "<Your ExpressRoute Circuit Name Here>"
		
		# ARP table for Microsoft peering - Primary path
		Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType MicrosoftPeering -DevicePath Primary
		
		# ARP table for Microsoft peering - Secodary path
		Get-AzureRmExpressRouteCircuitARPTable -ResourceGroupName $RG -ExpressRouteCircuitName $Name -PeeringType MicrosoftPeering -DevicePath Secondary 


A continuación se muestra el resultado de ejemplo de una de las rutas de acceso:

		Age InterfaceProperty IpAddress  MacAddress    
		--- ----------------- ---------  ----------    
		 10 On-Prem           65.0.0.1 ffff.eeee.dddd
		  0 Microsoft         65.0.0.2 aaaa.bbbb.cccc


## Uso de esta información
La tabla ARP de un emparejamiento se puede usar para determinar o validar la configuración y la conectividad de nivel 2. En esta sección se proporciona información general sobre la apariencia de las tablas ARP en distintos escenarios.

### Tabla ARP cuando un circuito está en estado operativo (estado esperado)

 - La tabla ARP tendrá una entrada para el lado local con una dirección IP válida y la dirección MAC y una entrada similar para el lado de Microsoft. 
 - El último octeto de la dirección IP local siempre será un número impar.
 - El último octeto de la dirección IP de Microsoft siempre será un número par.
 - La misma dirección MAC aparecerá en el lado de Microsoft para los 3 emparejamientos (principales o secundarios). 


		Age InterfaceProperty IpAddress  MacAddress    
		--- ----------------- ---------  ----------    
		 10 On-Prem           65.0.0.1 ffff.eeee.dddd
		  0 Microsoft         65.0.0.2 aaaa.bbbb.cccc

### Tabla ARP cuando el lado del proveedor de conectividad o local tiene problemas

 - Una sola entrada aparecerá en la tabla ARP. En ella se mostrará la asignación entre la dirección MAC y la dirección IP usadas en el lado de Microsoft. 

		Age InterfaceProperty IpAddress  MacAddress    
		--- ----------------- ---------  ----------    
		  0 Microsoft         65.0.0.2 aaaa.bbbb.cccc

>[AZURE.NOTE] Abra una solicitud de soporte técnico con su proveedor de conectividad para depurar tales problemas.


### Tabla ARP cuando el lado de Microsoft tiene problemas

 - Cuando hay problemas en el lado de Microsoft, no verá una tabla ARP para un emparejamiento. 
 -  Abra una incidencia de soporte técnico dirigida al [soporte técnico de Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Especifique que tiene un problema con la conectividad de nivel 2. 

## Pasos siguientes

 - Validar las configuraciones de nivel 3 para el circuito ExpressRoute
	 - Obtener el resumen de ruta para determinar el estado de las sesiones BGP 
	 - Obtener la tabla de rutas para determinar qué prefijos se anuncian a través de ExpressRoute
 - Validar la transferencia de datos revisando los bytes de entrada y de salida
 - Si sigue teniendo problemas, abra una incidencia de soporte técnico dirigida al [soporte técnico de Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

<!---HONumber=AcomDC_0615_2016-->