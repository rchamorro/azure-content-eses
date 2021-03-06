<properties
   pageTitle="Continuidad del negocio en la nube: recuperación de la base de datos (Base de datos SQL) | Microsoft Azure"
   description="Obtenga información acerca de cómo la Base de datos SQL de Azure permite la continuidad del negocio en la nube y la recuperación de la base de datos, y ayuda a que las aplicaciones críticas de la nube se sigan ejecutando."
   keywords="continuidad del negocio, continuidad del negocio en la nube, recuperación de desastres de la base de datos, recuperación de la base de datos"
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/20/2016"
   ms.author="carlrab"/>

# Introducción a la continuidad empresarial con Base de datos SQL de Azure

En este artículo de introducción se describen las funcionalidades de continuidad empresarial y recuperación ante desastres que proporciona Base de datos SQL de Azure. Esta solución ofrece opciones, recomendaciones y tutoriales para recuperarse de eventos de interrupción que podrían provocar la pérdida de información o que su base de datos y aplicación dejen de estar disponibles. Por ejemplo, explicaremos qué hacer en caso de que un error del usuario o la aplicación afecte a la integridad de los datos, se produzca una interrupción en una región de Azure o su aplicación requiera mantenimiento.

## Características de Base de datos SQL que puede utilizar para proporcionar continuidad empresarial

Base de datos SQL ofrece diversas funcionalidades de continuidad empresarial, incluidas las copias de seguridad automatizadas y la replicación de base de datos opcional. Cada una de ellas posee distintas características que abarcan los conceptos de tiempo de recuperación calculado (ERT) y pérdida de datos potencial de transacciones recientes. Cuando entienda estas opciones, puede elegir las que más relevantes considere y, en la mayoría de los escenarios, utilizarlas juntas con distintos objetivos. A medida que desarrolle el plan de continuidad empresarial, tendrá que saber el tiempo máximo aceptable para que la aplicación se recupere por completo tras un evento de interrupción. A esto se le denomina "objetivo de tiempo de recuperación" (RTO). También debe conocer la cantidad máxima de actualizaciones de datos recientes (intervalo de tiempo) que la aplicación puede tolerar perder al recuperarse después de un evento de interrupción. A esto se le conoce como "objetivo de punto de recuperación" (RPO).

### Uso de copias de seguridad para recuperar bases de datos

Base de datos SQL realiza automáticamente una combinación de copias de seguridad completas semanales, copias de seguridad diferenciales cada hora y copias de seguridad del registro de transacciones cada 5 minutos con el fin de proteger su empresa contra la pérdida de datos. Estas copias de seguridad se guardan en un almacenamiento con redundancia local durante 35 días en el caso de las bases de datos de los niveles de servicio Estándar y Premium, y durante 7 para las de Básico. Obtenga más información sobre los niveles de servicio en [este artículo](sql-database-service-tiers.md). Si el periodo de retención del nivel de servicio no se ajusta a los requisitos de su empresa, puede ampliarlo [cambiando dicho nivel de servicio](sql-database-scale-up.md). Las copias de seguridad completas y diferenciales de bases de datos también se replican en un [centro de datos asociado](../best-practices-availability-paired-regions.md) con el fin de brindar protección frente a interrupciones en el centro de datos. Consulte el artículo sobre [copias de seguridad automáticas de bases de datos](sql-database-automated-backups.md) para obtener más información.

Puede utilizar este tipo de copia de seguridad para recuperar una base de datos después de que se produzcan diferentes eventos de interrupción tanto en su centro de datos como en otro. Con las copias de seguridad automáticas de bases de datos, el tiempo estimado de recuperación dependerá de varios factores, como el número total de bases de datos que se están recuperando a la vez en la misma región, el tamaño de estas, el tamaño del registro de transacciones y el ancho de banda de red. En la mayoría de los casos, es inferior a 12 horas. Cuando se lleva a cabo un proceso de recuperación en otra región de datos, la posible pérdida de datos solo es de 1 hora gracias al almacenamiento con redundancia geográfica de las copias de seguridad diferenciales de bases de datos que se realizan cada hora.

> [AZURE.IMPORTANT] Para poder efectuar una recuperación con copias de seguridad automatizadas, debe ser miembro del rol de colaborador de SQL Server o propietario de la suscripción. Consulte el artículo [RBAC: Roles integrados](../active-directory/role-based-access-built-in-roles.md). Las recuperaciones se pueden realizar a través del Portal de Azure, PowerShell o la API de REST. (Transact-SQL no es compatible).

Utilice las copias de seguridad automatizadas como mecanismo de recuperación y de continuidad empresarial si se cumplen los siguientes requisitos en su aplicación:

- No se considera crítica.
- No tiene un SLA vinculante, por lo que no incurrirá en responsabilidades financieras en caso de tiempos de inactividad de 24 horas o más.
- Tiene una tasa de cambio de datos reducida (por ejemplo, transacciones por hora) y se tolera perder hasta una hora de datos.
- Los costos son un factor fundamental.

Si necesita recuperaciones más rápidas, utilice la [replicación geográfica activa](sql-database-geo-replication-overview.md) (la describimos a continuación). Si tiene que recuperar datos de un periodo de más de 35 días, plantéese la posibilidad de archivar la base de datos de forma regular en un BACPAC (un archivo comprimido que contiene el esquema de la base de datos y los datos asociados) guardado en el almacenamiento de blobs de Azure o en otra ubicación que prefiera. Para obtener más información sobre cómo crear un archivo de base de datos que guarde coherencia transaccional, consulte los artículos sobre cómo [crear copias de bases de datos](sql-database-copy.md) y [exportarlas](sql-database-export.md).

### Uso de la replicación geográfica activa para reducir el tiempo de recuperación y limitar la pérdida de datos asociada a una recuperación

Además de utilizar las copias de seguridad para recuperar bases de datos en caso de interrupciones en el negocio, puede usar la [replicación geográfica activa](sql-database-geo-replication-overview.md) para configurar que una base de datos tenga un máximo de 4 bases de datos secundarias legibles en las regiones que prefiera. Estas bases de datos secundarias se mantienen sincronizadas con la principal a través de un mecanismo de replicación asincrónica. Esta característica se utiliza para proteger su negocio de interrupciones en el centro de datos o durante una actualización de la aplicación. La replicación geográfica activa también puede emplearse para que los usuarios repartidos por distintas ubicaciones del mundo puedan realizar consultas de solo lectura de manera más eficaz.

Si la base de datos principal se desconecta de forma inesperada o debido a actividades de mantenimiento, puede convertir rápidamente una base de datos secundaria en principal (a este proceso también se le denomina "conmutación por error") y configurar que las aplicaciones se conecten a esta principal. Con las conmutaciones por error planeadas no se pierden datos. Sin embargo, con las no planeadas, se pierde una pequeña cantidad de información en las transacciones muy recientes debido a la naturaleza de la replicación asincrónica. Tras una conmutación por error, puede realizar una conmutación por recuperación según un plan o cuando el centro de datos vuelva a estar en línea. En todos los casos, los usuarios experimentarán un tiempo de inactividad reducido y tendrán que volver a conectarse.

> [AZURE.IMPORTANT] Para usar la replicación geográfica activa, debe ser el propietario de la suscripción o tener permisos administrativos en SQL Server. Puede realizar tareas de configuración y conmutación por error a través del Portal de Azure, PowerShell o la API de REST con los permisos de la suscripción o, bien mediante Transact-SQL con los permisos de SQL Server.

Use la replicación geográfica activa si su aplicación cumple los criterios siguientes:

- Es crítica.
- Tiene un SLA que no se permite que se produzcan tiempos de inactividad de más de 24 horas.
- El tiempo de inactividad incurrirá en responsabilidades financieras.
- Tiene una tasa de cambio de datos elevada y no se acepta perder una hora de datos.
- El costo adicional por utilizar la replicación geográfica activa es menor que el de la posible responsabilidad financiera y la pérdida de negocio asociada que habría que asumir.

## Recuperación de bases de datos tras un error del usuario o la aplicación

Nadie es perfecto. Un usuario podría eliminar de forma involuntaria algunos datos, una tabla importante o, incluso, una base de datos entera. También, una aplicación podría sobrescribir accidentalmente datos correctos por otros no válidos debido a un defecto en esta.

Estas son las opciones de recuperación que tiene para este caso.

### Realización de una restauración a un momento dado

Puede utilizar las copias de seguridad automatizadas para restaurar la base de datos a un momento dado conocido y sin problemas, siempre que sea dentro del periodo de retención de la base de datos. Después de restaurar la base de datos, puede reemplazar la original por la restaurada o copiar la información que necesite de los datos restaurados en la base de datos original. Si la base de datos utiliza la replicación geográfica activa, se recomienda copiar los datos que precise de la copia restaurada en la original. Si reemplaza la base de datos original por la restaurada, debe volver a configurar y sincronizar la replicación geográfica activa (proceso que puede llevar bastante tiempo en bases de datos de gran tamaño).

Para obtener más información y ver los pasos detallados de cómo restaurar una base de datos a un momento dado mediante el Portal de Azure o PowerShell, consulte el artículo sobre [restauración a un momento dado](sql-database-recovery-using-backups.md#point-in-time-restore). Transact-SQL no se puede utilizar para este fin.

### Restauración de una base de datos eliminada

Si se elimina la base de datos, pero no el servidor lógico, puede restaurar la base de datos eliminada al momento en que se suprimió. De este modo, se restaura una copia de seguridad de la base de datos en el mismo servidor SQL lógico desde el que se eliminó. Puede restaurarla usando el nombre original o proporcionando un nuevo nombre a la base de datos restaurada.

Para obtener más información y ver los pasos detallados de cómo restaurar una base de datos eliminada a través del Portal de Azure o PowerShell, consulte el artículo sobre [restauración de bases de datos eliminadas](sql-database-recovery-using-backups.md#deleted-database-restore). Transact-SQL no se puede utilizar para este fin.

> [AZURE.IMPORTANT] Si se elimina el servidor lógico, no se podrá recuperar una base de datos eliminada.

### Importación a partir de un archivo de base de datos

Si la pérdida de datos se produjo fuera del periodo de retención actual de las copias de seguridad automatizadas y ha estado archivando la base de datos, puede [importar un BACPAC archivado](sql-database-import.md) en una nueva base de datos. En este punto, puede reemplazar la base de datos original por la importada o copiar la información que necesite de los datos importados en la original.

## Recuperación de una base de datos en otra región tras una interrupción en el centro de datos regional de Azure

<!-- Explain this scenario -->

Aunque es poco habitual, en los centros de datos de Azure pueden producirse interrupciones. Cuando esto ocurre, provoca también una interrupción en el negocio que podría extenderse solo unos pocos minutos o, incluso, horas.

- Una opción consiste en esperar a que la base de datos vuelva a estar en línea cuando termine la interrupción del centro de datos. Esto puede hacerse con las aplicaciones que pueden permitirse que la base de datos esté desconectada. Por ejemplo, los proyectos de desarrollo o las pruebas gratuitas no tienen que estar en funcionamiento constantemente. Cuando se produce una interrupción en un centro de datos, no sabe cuánto durará, por lo que esta opción solo es útil si no necesita utilizar la base de datos durante un tiempo.
- Otra opción consiste en realizar una conmutación por error a otra región de datos si utiliza la replicación geográfica activa o la recuperación mediante copias de seguridad de bases de datos con redundancia geográfica (restauración geográfica). La conmutación por error solo lleva unos segundos, mientras que la recuperación a partir de copias de seguridad dura horas.

Cuando pase a la acción, el tiempo que se tarde en recuperar las bases de datos y la cantidad de información que se pierde en el caso de una interrupción en el centro de datos dependerá de cómo decida usar las características de continuidad empresarial —que describimos anteriormente— de la aplicación. De hecho, puede decidir usar una combinación de copias de seguridad de bases de datos y la replicación geográfica activa según los requisitos de la aplicación. Para ver una explicación de las consideraciones de diseño de las aplicaciones para bases de datos independientes y grupos elásticos con estas características de continuidad empresarial, consulte [Diseño de una aplicación para la recuperación ante desastres en la nube mediante replicación geográfica activa en Base de datos SQL](sql-database-designing-cloud-solutions-for-disaster-recovery.md) y [Estrategias de recuperación ante desastres para aplicaciones que usan el grupo elástico de Base de datos SQL](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).

En las siguientes secciones se ofrece información general de los pasos para realizar tareas de recuperación mediante copias de seguridad de bases de datos o la replicación geográfica activa. Para ver los pasos detallados, como el planeamiento de los requisitos, los pasos posteriores a la recuperación y los detalles sobre cómo simular una interrupción para llevar a cabo tareas de recuperación ante desastres, consulte el artículo [Restauración de una base de datos SQL de Azure o una conmutación por error en una secundaria](sql-database-disaster-recovery.md).

### Preparativos para interrupciones

Con independencia de la característica de continuidad empresarial que vaya a utilizar, debe hacer lo siguiente:

- Identificar y preparar el servidor de destino, incluidas las reglas de firewall de nivel de servidor, los inicios de sesión y los permisos de nivel de base de datos maestra
- Determinar cómo se redirigirán los clientes y las aplicaciones cliente al nuevo servidor
- Documentar otras dependencias, como las alertas y la configuración de auditoría
 
Si no se planea ni prepara correctamente, el proceso de conectar las aplicaciones después de una conmutación por error o una recuperación llevará más tiempo y, probablemente, también haya que solucionar problemas en momentos de estrés, por lo que no es nada recomendable.

### Conmutación por error de la base de datos secundaria de replicación geográfica 

Si utiliza la replicación geográfica activa como mecanismo de recuperación, [fuerce una conmutación por error a una base de datos secundaria de replicación geográfica](sql-database-disaster-recovery.md#failover-to-geo-replicated-secondary-database). En cuestión de segundos, la base de datos secundaria pasa a ser la nueva principal y podrá registrar nuevas transacciones y responder a las consultas (solo perderá unos segundos de información en los datos que no se habían replicado). Para obtener información sobre cómo automatizar el proceso de conmutación por error, consulte [Diseño de una aplicación para la recuperación ante desastres en la nube mediante replicación geográfica activa en Base de datos SQL](sql-database-designing-cloud-solutions-for-disaster-recovery.md).

> [AZURE.NOTE] Cuando el centro de datos vuelve a estar en línea, si lo desea, puede conmutar por recuperación al principal original.

### Realización de restauraciones geográficas 

Si utiliza copias de seguridad automatizadas con replicación de almacenamiento con redundancia geográfica como mecanismo de recuperación, [inicie una recuperación de base de datos mediante la restauración geográfica](sql-database-disaster-recovery.md#recover-using-geo-restore). El proceso de recuperación dura 12 horas en la mayoría de los casos, y hay una pérdida de datos de hasta 1 hora en función de cuándo se realizó y replicó la última copia de seguridad diferencial que se ejecuta cada hora. Hasta que no se complete la recuperación, la base de datos no podrá registrar transacciones ni responder a las consultas.

> [AZURE.NOTE] Si el centro de datos vuelve a estar en línea antes de cambiar la aplicación en la base de datos recuperada, bastará con cancelar el proceso de recuperación.

### Realización de tareas posteriores a la recuperación y conmutación por error 

Cuando efectúe la recuperación con cualquiera de los mecanismos para llevarla a cabo, debe realizar las siguientes tareas adicionales antes de que los usuarios y las aplicaciones vuelvan a conectarse:

- Redirija los clientes y las aplicaciones cliente al nuevo servidor y a la base de datos restaurada.
- Asegúrese de aplicar reglas de firewall de nivel de servidor adecuadas para que se conecten los usuarios (o use [firewalls de nivel de base de datos](sql-database-firewall-configure.md#creating-database-level-firewall-rules)).
- No se olvide de emplear permisos de nivel de base de datos maestra e inicios de sesión apropiados (o use [usuarios contenidos](https://msdn.microsoft.com/library/ff929188.aspx)).
- Configure la auditoría según corresponda.
- Configure las alertas según corresponda.

## Actualice una aplicación con el mínimo tiempo de inactividad posible.

A veces, necesita desconectar una aplicación debido a un mantenimiento planeado, por ejemplo, para actualizarla. En el artículo [Administración de actualizaciones graduales de aplicaciones en la nube mediante la replicación geográfica activa de Base de datos SQL](sql-database-manage-application-rolling-upgrade.md) se explica cómo utilizar la replicación geográfica activa para poder actualizar gradualmente su aplicación en la nube con el objetivo de minimizar el tiempo de inactividad durante este proceso. Además, se describe una ruta de recuperación para cuando se empiece a detectar errores. En este artículo veremos dos métodos diferentes de organizar el proceso de actualización y comentaremos las ventajas y desventajas de cada opción.

## Pasos siguientes

Para ver una explicación de las consideraciones de diseño de las aplicaciones para bases de datos independientes y grupos elásticos, consulte [Diseño de una aplicación para la recuperación ante desastres en la nube mediante replicación geográfica activa en Base de datos SQL](sql-database-designing-cloud-solutions-for-disaster-recovery.md) y [Estrategias de recuperación ante desastres para aplicaciones que usan el grupo elástico de Base de datos SQL](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md).

<!---HONumber=AcomDC_0803_2016-->