<properties
   pageTitle="Migración del almacenamiento de datos SQL de Azure existente a almacenamiento premium | Microsoft Azure"
   description="Instrucciones para migrar un almacenamiento de datos SQL existente a almacenamiento premium"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="happynicolle"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/05/2016"
   ms.author="nicw;barbkess;sonyama"/>

# Información de migración al Almacenamiento premium
En el servicio Almacenamiento de datos SQL, se introdujo recientemente el [Almacenamiento premium para poder predecir el rendimiento de manera más eficaz][]. Ahora se pueden migrar los almacenes de datos existentes del Almacenamiento estándar al premium. Siga leyendo para obtener más información sobre cómo y cuándo se producen las migraciones automáticas, además de cómo realizar migraciones manuales en caso de que prefiera tener el control en los tiempos de inactividad.

Si tiene más de un almacén de datos, emplee el [programa de migración automática][] siguiente para determinar cuándo se migrará.

## Determinación del tipo de almacenamiento
Si creó un almacenamiento de datos antes de las fechas siguientes, significa que está utilizando el Almacenamiento estándar. Además, en cada Almacenamiento de datos del Almacenamiento estándar que se migre habrá un aviso en la parte superior de la hoja Almacenamiento de datos del [Portal de Azure][] con el siguiente mensaje: *An upcoming upgrade to premium storage will require an outage. Learn more ->* (Se va a realizar próximamente una actualización al Almacenamiento premium que precisa interrumpir el servicio. Obtenga más información.)

| **Región** | **Almacenamiento de datos creado antes de esta fecha** |
| :------------------ | :-------------------------------- |
| Australia Oriental | Almacenamiento premium no disponible todavía |
| Sudeste de Australia | 5 de agosto de 2016 |
| Sur de Brasil | 5 de agosto de 2016 |
| Centro de Canadá | 25 de mayo de 2016 |
| Este de Canadá | 26 de mayo de 2016 |
| Central EE. UU.: | 26 de mayo de 2016 |
| Este de China | Almacenamiento premium no disponible todavía |
| Norte de China | Almacenamiento premium no disponible todavía |
| Asia oriental | 25 de mayo de 2016 |
| Este de EE. UU. | 26 de mayo de 2016 |
| Este de EE. UU. 2 | 27 de mayo de 2016 |
| India central | 27 de mayo de 2016 |
| Sur de India | 26 de mayo de 2016 |
| India occidental | Almacenamiento premium no disponible todavía |
| Este de Japón | 5 de agosto de 2016 |
| Oeste de Japón | Almacenamiento premium no disponible todavía |
| Centro-Norte de EE. UU | Almacenamiento premium no disponible todavía |
| Europa del Norte | 5 de agosto de 2016 |
| Centro-Sur de EE. UU | 27 de mayo de 2016 |
| Sudeste asiático | 24 de mayo de 2016 |
| Europa occidental | 25 de mayo de 2016 |
| Oeste de EE. UU. | 26 de mayo de 2016 |

## Información de la migración automática
De forma predeterminada, la base de datos se migrará automáticamente durante las 18:00 y las 6:00 (la hora local de su región) en algún momento de la [programación de migración automática][] siguiente. Durante la migración, no podrá utilizarse el almacén de datos existente. Estimamos que el proceso de migración dura aproximadamente 1 hora por cada TB de almacenamiento de cada almacén de datos. Asimismo, nos aseguraremos de que no se le cobre nada en ningún momento del proceso.

> [AZURE.NOTE] Durante la migración, no podrá usar el almacén de datos existente. Cuando finalice el proceso, el almacenamiento de datos volverá a estar en línea.

A continuación, encontrará los pasos que Microsoft realizará para completar la migración sin que usted tenga que hacer nada. Para este ejemplo, imagine que su almacenamiento de datos del Almacenamiento estándar se denomina "miAD".

1.	Microsoft cambiará el nombre de miAD a miAD\_ DO\_NOT\_USE\_ [Marca de tiempo].
2.	Microsoft pausará miAD\_ DO\_NOT\_USE\_ [Marca de tiempo]. Durante este periodo, Microsoft realizará una copia de seguridad. Es posible que observe que se llevan a cabo varias tareas de pausa y reanudación si se producen problemas durante este proceso.
3.	Microsoft creará un nuevo almacenamiento de datos denominado "miAD" en el Almacenamiento premium a partir de la copia de seguridad realizada en el paso 2 anterior. miAD no aparecerá hasta que acabe el proceso de restauración.
4.	Cuando finalice, se devolverán miAD en el mismo nivel de DWU y el estado de en pausa o activo que hubiera antes de la migración.
5.	Cuando acabe el proceso de migración, Microsoft eliminará miAD\_DO\_NOT\_USE\_ [Marca de tiempo].
	
> [AZURE.NOTE] Esta configuración no se transmitirá como parte de la migración:
> 
>	-  Auditing at the Database level will need to be re-enabled
>	-  Firewall rules at the **Database** level will need to be re-added.  Firewall rules at the **Server** level will not be impacted.

### Programa de migración automática
La migración automática se llevará a cabo desde las 18:00 a las 6:00 (la hora local de dicha región) en algún momento durante el programa de interrupción siguiente.

| **Región** | **Fecha de inicio estimada** | **Fecha de finalización estimada** |
| :------------------ | :--------------------------- | :--------------------------- |
| Australia Oriental | Sin determinar | Sin determinar |
| Sudeste de Australia | 10 de agosto de 2016 | 24 de agosto de 2016 |
| Sur de Brasil | 10 de agosto de 2016 | 24 de agosto de 2016 |
| Centro de Canadá | 23 de junio de 2016 | 1 de julio de 2016 |
| Este de Canadá | 23 de junio de 2016 | 1 de julio de 2016 |
| Central EE. UU.: | 23 de junio de 2016 | 4 de julio de 2016 |
| Este de China | Sin determinar | Sin determinar |
| Norte de China | Sin determinar | Sin determinar |
| Asia oriental | 23 de junio de 2016 | 1 de julio de 2016 |
| Este de EE. UU. | 23 de junio de 2016 | 11 de julio de 2016 |
| Este de EE. UU. 2 | 23 de junio de 2016 | 8 de julio de 2016 |
| India central | 23 de junio de 2016 | 1 de julio de 2016 |
| Sur de India | 23 de junio de 2016 | 1 de julio de 2016 |
| India occidental | Sin determinar | Sin determinar |
| Este de Japón | 10 de agosto de 2016 | 24 de agosto de 2016 |
| Oeste de Japón | Sin determinar | Sin determinar |
| Centro-Norte de EE. UU | Sin determinar | Sin determinar |
| Europa del Norte | 10 de agosto de 2016 | 24 de agosto de 2016 |
| Centro-Sur de EE. UU | 23 de junio de 2016 | 2 de julio de 2016 |
| Sudeste asiático | 23 de junio de 2016 | 1 de julio de 2016 |
| Europa occidental | 23 de junio de 2016 | 8 de julio de 2016 |
| Oeste de EE. UU. | 23 de junio de 2016 | 7 de julio de 2016 |

## Migración manual al Almacenamiento premium
Si desea tener el control en los tiempos de inactividad, puede realizar los pasos siguientes para migrar un almacén de datos existente del Almacenamiento estándar al premium. Si decide realizar la migración manual, debe hacerlo antes de que se inicie la automática en dicha región para evitar riesgos de que esta última cause algún conflicto (consulte la [programación de migración automática][]).

### Instrucciones de migración manual
Si desea tener el control en los tiempos de inactividad, puede migrar manualmente el almacén de datos mediante la funcionalidad de copia de seguridad o restauración. Se espera que el componente de restauración de la migración tarde aproximadamente 1 hora por cada TB de almacenamiento en cada almacenamiento de datos. Si desea conservar el mismo nombre cuando se complete la migración, siga estos pasos para [cambiar el nombre durante la migración][].

1.	[Pause][] el almacenamiento de datos, que realizará una copia de seguridad automática
2.	Lleve a cabo la [restauración][] a partir de la instantánea más reciente
3.	Elimine el almacenamiento de datos existente del Almacenamiento estándar. **Si no puede realizar este paso, se le cobrará por los dos almacenamientos de datos.**

> [AZURE.NOTE] Esta configuración no se transmitirá como parte de la migración:
> 
>	-  Auditing at the Database level will need to be re-enabled
>	-  Firewall rules at the **Database** level will need to be re-added.  Firewall rules at the **Server** level will not be impacted.

#### Opcional: pasos para cambiar el nombre durante la migración 
Dos bases de datos que se encuentren en el mismo servidor lógico no pueden tener el mismo nombre. Almacenamiento de datos SQL ahora permite cambiar el nombre de DW.

Para este ejemplo, imagine que su almacenamiento de datos del Almacenamiento estándar se denomina "miAD".

1.	Cambie el nombre "MyDW" con el comando ALTER DATABASE que sigue a algo como "MyDW\_BeforeMigration". Esto eliminará todas las transacciones existentes y debe realizarse en la base de datos maestra para que sea correcta.
```
ALTER DATABASE CurrentDatabasename MODIFY NAME = NewDatabaseName;
```
2.	[Pause][] "MyDW\_BeforeMigration", que realizará una copia de seguridad automática
3.	Realice una [restauración][] a partir de la instantánea más reciente de una nueva base de datos con el nombre que usó para tener (ej: "MyDW")
4.	Elimine "MyDW\_BeforeMigration". **Si no puede realizar este paso, se le cobrará por los dos almacenamientos de datos.**

> [AZURE.NOTE] Esta configuración no se transmitirá como parte de la migración:
> 
>	-  Auditing at the Database level will need to be re-enabled
>	-  Firewall rules at the **Database** level will need to be re-added.  Firewall rules at the **Server** level will not be impacted.

## Pasos siguientes
Con la migración al Almacenamiento premium, también aumentamos la cantidad de archivos de blob de base de datos en la arquitectura subyacente de Almacenamiento de datos. Si tiene algún problema de rendimiento, se recomienda recompilar los índices de almacén de columnas en clúster con el script siguiente. Con esta acción, se forzará a algunos de los datos existentes a los blobs adicionales. Si no realiza ninguna acción, los datos se redistribuirán naturalmente con el tiempo a medida que carga más datos en las tablas de Almacenamiento de datos.

**Requisitos previos:**

1.	Almacenamiento de datos se debe ejecutar con 1000 DWU o más (consulte el [escalado de la potencia de proceso][]).
2.	El usuario que ejecuta el script debe tener el [rol mediumrc][] o superior.
	1.	Para agregar un usuario a este rol, ejecute lo siguiente:
		1.	````EXEC sp_addrolemember 'xlargerc', 'MyUser'````

````sql
-------------------------------------------------------------------------------
-- Paso 1: Crear tabla para controlar la recompilación de índice.
-- Ejecutar como usuario en mediumrc o superior.
--------------------------------------------------------------------------------
create table sql\_statements WITH (distribution = round\_robin) as select 'alter index all on ' + s.name + '.' + t.NAME + ' rebuild;' as statement, row\_number() over (order by s.name, t.name) as sequence from sys.schemas s inner join sys.tables t on s.schema\_id = t.schema\_id where is\_external = 0 ; go
 
--------------------------------------------------------------------------------
-- Paso 2: Ejecutar recompilaciones de índice. Si se produce un error de script, puede volver a ejecutar lo que aparece a continuación para reiniciar donde se quedó.
-- Ejecutar como usuario en mediumrc o superior.
--------------------------------------------------------------------------------

declare @nbr\_statements int = (select count(*) from sql\_statements) declare @i int = 1 while(@i <= @nbr\_statements) begin declare @statement nvarchar(1000)= (select statement from sql\_statements where sequence = @i) print cast(getdate() as nvarchar(1000)) + ' Executing... ' + @statement exec (@statement) delete from sql\_statements where sequence = @i set @i += 1 end;
go
-------------------------------------------------------------------------------
-- Paso 3: Limpiar la tabla que se creó en el paso 1
--------------------------------------------------------------------------------
drop table sql\_statements; go ````

Si tiene problemas con el Almacén de datos, [cree una incidencia de soporte técnico][] e indique que la posible causa es la migración al Almacenamiento premium.

<!--Image references-->

<!--Article references-->
[programa de migración automática]: #automatic-migration-schedule
[programación de migración automática]: #automatic-migration-schedule
[self-migration to Premium Storage]: #self-migration-to-premium-storage
[cree una incidencia de soporte técnico]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Azure paired region]: ./best-practices-availability-paired-regions.md
[main documentation site]: ./services/sql-data-warehouse.md
[Pause]: ./sql-data-warehouse-manage-compute-portal.md/#pause-compute
[restauración]: ./sql-data-warehouse-manage-database-restore-portal.md
[cambiar el nombre durante la migración]: #optional-steps-to-rename-during-migration
[escalado de la potencia de proceso]: ./sql-data-warehouse-manage-compute-portal/#scale-compute-power
[rol mediumrc]: ./sql-data-warehouse-develop-concurrency/#workload-management

<!--MSDN references-->


<!--Other Web references-->
[Almacenamiento premium para poder predecir el rendimiento de manera más eficaz]: https://azure.microsoft.com/es-ES/blog/azure-sql-data-warehouse-introduces-premium-storage-for-greater-performance/
[Portal de Azure]: https://portal.azure.com

<!---HONumber=AcomDC_0810_2016-->