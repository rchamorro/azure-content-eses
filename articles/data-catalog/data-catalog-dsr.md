<properties
   pageTitle="Orígenes de datos compatibles con el Catálogo de datos de Azure | Microsoft Azure"
   description="Especificación de los orígenes de datos compatibles actualmente."
   services="data-catalog"
   documentationCenter=""
   authors="spelluru"
   manager="paulettm"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="07/25/2016"
   ms.author="trhabe"/>

# Orígenes de datos compatibles con el Catálogo de datos de Azure

Los usuarios del Catálogo de datos de Azure pueden publicar metadatos mediante una API pública, una herramienta de registro con un solo clic o la especificación manual de información directamente en el portal web del Catálogo de datos. En la siguiente cuadrícula se resumen todos los orígenes que admite el catálogo actualmente y las funcionalidades de publicación de cada uno de ellos. También se enumeran las herramientas de datos externos que cada origen puede iniciar desde nuestra experiencia "abierta" del portal. A continuación encontrará una segunda cuadrícula que tiene una especificación más técnica de las propiedades de conexión de los orígenes de datos.


## Lista de orígenes de datos que se admiten

<table>

    <tr>
       <td><b>Objeto de origen de datos</b></td>
       <td><b>API</b></td>
       <td><b>Entrada manual</b></td>
       <td><b>Herramienta de registro</b></td>
       <td><b>Open-In Tools</b></td>
       <td><b>Notas</b></td>
    </tr>

    <tr>
      <td>Directorio del Almacén de Azure Data Lake</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Archivo del Almacén de Azure Data Lake</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Blob de almacenamiento de Azure</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Directorio de almacenamiento de Azure</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Tabla de almacenamiento de Azure</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td>
        <font size="2"></font>
      </td>
      <td>
        <font size="2"></font>
      </td>
    </tr>

    <tr>
      <td>Directorio HDFS</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Archivo HDFS</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Tabla de Hive</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Vista de Hive</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Tabla de MySQL</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Vista de MySQL</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Tabla de base de datos de Oracle</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Vista de la Base de datos de Oracle</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Otro (activo genérico)</td>
      <td>✓</td>
      <td>✓</td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Tabla de Almacenamiento de datos SQL</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI, SQL Server Data Tools</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Vista de Almacenamiento de datos SQL</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI, SQL Server Data Tools</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Dimensión SQL Server Analysis Services</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>KPI de SQL Server Analysis Services</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Medida SQL Server Analysis Services</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Tabla de SQL Server Analysis Services</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Informe de SQL Server Reporting Services</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Browser</font></td>
      <td><font size=2>Solo servidores de modo nativo. No se admite el modo de SharePoint.</font></td>
    </tr>

    <tr>
      <td>Tabla de SQL Server</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI, SQL Server Data Tools</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Vista de SQL Server</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel, PowerBI, SQL Server Data Tools</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Tabla de Teradata</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Vista de Teradata</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>Excel</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Vista de SAP HANA</td>
      <td>✓</td>
      <td>✓</td>
      <td>✓</td>
      <td><font size=2>PowerBI</font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Tabla de Db2</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Vista de Db2</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Archivo de sistema de archivos</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Directorio FTP</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Archivo FTP</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Informe HTTP</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Punto de conexión HTTP</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Archivo HTTP</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Conjunto de entidades de OData</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Función de OData</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Tabla de PostgreSQL</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Vista de PostgreSQL</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Vista de SAP HANA</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td> Objeto de Salesforce</td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

    <tr>
      <td>Lista de SharePoint </td>
      <td>✓</td>
      <td></td>
      <td></td>
      <td><font size=2></font></td>
      <td><font size=2></font></td>
    </tr>

</table>

Si necesita compatibilidad con fuentes adicionales, envíe una solicitud de característica mediante el [foro del Catálogo de datos de Azure](http://go.microsoft.com/fwlink/?LinkID=616424&clcid=0x409).


<br> <br>
## Especificación de referencia de origen de datos
> [AZURE.NOTE] La columna "Estructura de DSL" de la tabla siguiente enumera solo las propiedades de conexión para el contenedor de propiedades "address" que utiliza el Catálogo de datos de Azure (es decir, el contenedor de propiedades "address" puede contener otras propiedades de conexión del origen de datos que persiste en el Catálogo de datos de Azure, pero que no usa).
<table>
    <tr>
       <td><b>Tipo de origen</b></td>
       <td><b>Tipo de recurso</b></td>
       <td><b>Tipos de objeto</b></td>
       <td><b>Estructura de DSL<b></td>
    </tr>
    <tr>
      <td>Almacén de Azure Data Lake</td>
      <td>Contenedor</td>
      <td>Data Lake</td>
      <td>
        <font size=2> protocolo: webhdfs
            <br>autenticación: {básica, oauth}
            <br>dirección:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>
    <tr>
      <td>Almacén de Azure Data Lake</td>
      <td>Tabla</td>
      <td>Directorio, archivo</td>
      <td>
        <font size=2> protocolo: webhdfs
            <br>autenticación: {básica, oauth}
            <br>dirección:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>
    <tr>
      <td>Almacenamiento de Azure</td>
      <td>Contenedor</td>
      <td>Contenedor</td>
      <td>
        <font size=2> protocolo: azure-blobs
            <br>autenticación: {azure-access-key}
            <br>dirección:
            <br>&#160;&#160;&#160;&#160;&#160; dominio
            <br>&#160;&#160;&#160;&#160;&#160; cuenta
            <br>&#160;&#160;&#160;&#160;&#160; contenedor </font>
      </td>
    </tr>
    <tr>
      <td>Almacenamiento de Azure</td>
      <td>Tabla</td>
      <td>Blob, directorio</td>
      <td>
        <font size=2> protocolo: azure-blobs
            <br>autenticación: {azure-access-key}
            <br>dirección:
            <br>&#160;&#160;&#160;&#160;&#160; dominio
            <br>&#160;&#160;&#160;&#160;&#160; cuenta
            <br>&#160;&#160;&#160;&#160;&#160; contenedor
            <br>&#160;&#160;&#160;&#160;&#160; nombre </font>
      </td>
    </tr>
    <tr>
      <td>Almacenamiento de Azure</td>
      <td>Contenedor</td>
      <td>Contenedor</td>
      <td>
        <font size=2> protocolo: azure-tables
            <br>autenticación: {azure-access-key}
            <br>dirección:
            <br>&#160;&#160;&#160;&#160;&#160; dominio
            <br>&#160;&#160;&#160;&#160;&#160; cuenta </font>
      </td>
    </tr>
    <tr>
      <td>Almacenamiento de Azure</td>
      <td>Tabla</td>
      <td>Tabla</td>
      <td>
        <font size=2> protocolo: azure-tables
            <br>autenticación: {azure-access-key}
            <br>dirección:
            <br>&#160;&#160;&#160;&#160;&#160; dominio
            <br>&#160;&#160;&#160;&#160;&#160; cuenta
            <br>&#160;&#160;&#160;&#160;&#160; nombre </font>
      </td>
    </tr>
    <tr>
      <td>Cosmos</td>
      <td>Contenedor</td>
      <td>Clúster virtual</td>
      <td>
        <font size=2> protocolo: cosmos
            <br>autenticación: {básica, windows}
            <br>dirección:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>
    <tr>
      <td>Cosmos</td>
      <td>Tabla</td>
      <td>Transmisión, conjunto de transmisiones, vista</td>
      <td>
        <font size=2> protocolo: cosmos
            <br>autenticación: {básica, windows}
            <br>dirección:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>
    <tr>
      <td>DataZen</td>
      <td>Contenedor</td>
      <td>Sitio</td>
      <td>
        <font size=2> protocolo: http
            <br>autenticación: {no, básica, windows, oauth}
            <br>dirección:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>
    <tr>
      <td>DataZen</td>
      <td>Informe</td>
      <td>Informe, panel</td>
      <td>
        <font size=2> protocolo: http
            <br>autenticación: {no, básica, windows, oauth}
            <br>dirección:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>
    <tr>
      <td>Db2</td>
      <td>Contenedor</td>
      <td>Base de datos</td>
      <td>
        <font size=2> protocolo: db2
            <br>autenticación: {básica, windows}
            <br>dirección:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; base de datos </font>
      </td>
    </tr>
    <tr>
      <td>Db2</td>
      <td>Tabla</td>
      <td>Tabla, vista</td>
      <td>
        <font size=2> protocolo: db2
            <br>autenticación: {básica, windows}
            <br>dirección:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; objeto
            <br>&#160;&#160;&#160;&#160;&#160; esquema </font>
      </td>
    </tr>
    <tr>
      <td>Sistema de archivos</td>
      <td>Tabla</td>
      <td>Archivo</td>
      <td>
        <font size=2> protocolo: archivo
            <br>autenticación: {no, básica, windows}
            <br>dirección:
            <br>&#160;&#160;&#160;&#160;&#160; ruta de acceso </font>
      </td>
    </tr>
    <tr>
      <td>Ftp</td>
      <td>Tabla</td>
      <td>Directorio, archivo</td>
      <td>
        <font size=2> protocolo: ftp
            <br>autenticación: {no, básica, windows}
            <br>dirección:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>
    <tr>
      <td>sistema de archivos distribuido de Hadoop</td>
      <td>Contenedor</td>
      <td>Clúster</td>
      <td>
        <font size=2> protocolo: webhdfs
            <br>autenticación: {básica, oauth}
            <br>dirección:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>
    <tr>
      <td>sistema de archivos distribuido de Hadoop</td>
      <td>Tabla</td>
      <td>Directorio, archivo</td>
      <td>
        <font size=2> protocolo: webhdfs
            <br>autenticación: {básica, oauth}
            <br>dirección:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>
    <tr>
      <td>Hive</td>
      <td>Contenedor</td>
      <td>Base de datos</td>
      <td>
        <font size=2> protocolo: hive
            <br>autenticación: {hdinsight, básica, nombre de usuario, no}
            <br>dirección:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>connectionProperties:
            <br>&#160;&#160;&#160;&#160;&#160; serverProtocol: {hive2} </font>
      </td>
    </tr>
    <tr>
      <td>Hive</td>
      <td>Tabla</td>
      <td>Tabla, vista</td>
      <td>
        <font size=2> protocolo: hive
            <br>autenticación: {hdinsight, básica, nombre de usuario, no}
            <br>dirección:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; objeto
            <br>connectionProperties:
            <br>&#160;&#160;&#160;&#160;&#160; serverProtocol: {hive2} </font>
      </td>
    </tr>
    <tr>
      <td>Http</td>
      <td>Contenedor</td>
      <td>Sitio</td>
      <td>
        <font size=2> protocolo: http
            <br>autenticación: {no, básica, windows, oauth}
            <br>dirección:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>
    <tr>
      <td>Http</td>
      <td>Informe</td>
      <td>Informe, panel</td>
      <td>
        <font size=2> protocolo: http
            <br>autenticación: {no, básica, windows, oauth}
            <br>dirección:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>
    <tr>
      <td>Http</td>
      <td>Tabla</td>
      <td>Punto de conexión, archivo</td>
      <td>
        <font size=2> protocolo: http
            <br>autenticación: {no, básica, windows, oauth}
            <br>dirección:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>
    <tr>
      <td>MySQL</td>
      <td>Contenedor</td>
      <td>Base de datos</td>
      <td>
        <font size=2> protocolo: mysql
            <br>autenticación: {protocolo, windows}
            <br>dirección:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; base de datos </font>
      </td>
    </tr>
    <tr>
      <td>MySQL</td>
      <td>Tabla</td>
      <td>Tabla, vista</td>
      <td>
        <font size=2> protocolo: mysql
            <br>autenticación: {protocolo, windows}
            <br>dirección:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; objeto </font>
      </td>
    </tr>
    <tr>
      <td>OData</td>
      <td>Contenedor</td>
      <td>Contenedor de entidades</td>
      <td>
        <font size=2> protocolo: odata
            <br>autenticación: {no, básica, windows}
            <br>dirección:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>
    <tr>
      <td>OData</td>
      <td>Tabla</td>
      <td>Conjunto de entidades, función</td>
      <td>
        <font size=2> protocolo: odata
            <br>autenticación: {no, básica, windows}
            <br>dirección:
            <br>&#160;&#160;&#160;&#160;&#160; url
            <br>&#160;&#160;&#160;&#160;&#160; recurso </font>
      </td>
    </tr>
    <tr>
      <td>Base de datos de Oracle</td>
      <td>Contenedor</td>
      <td>Base de datos</td>
      <td>
        <font size=2> protocolo: oracle
            <br>autenticación: {protocolo, windows}
            <br>dirección:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; base de datos </font>
      </td>
    </tr>
    <tr>
      <td>Base de datos de Oracle</td>
      <td>Tabla</td>
      <td>Tabla, vista</td>
      <td>
        <font size=2> protocolo: oracle
            <br>autenticación: {protocolo, windows}
            <br>dirección:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; schema
            <br>&#160;&#160;&#160;&#160;&#160; objeto </font>
      </td>
    </tr>
    <tr>
      <td>Postgresql</td>
      <td>Contenedor</td>
      <td>Base de datos</td>
      <td>
        <font size=2> protocolo: postgresql
            <br>autenticación: {básica, windows}
            <br>dirección:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; base de datos </font>
      </td>
    </tr>
    <tr>
      <td>Postgresql</td>
      <td>Tabla</td>
      <td>Tabla, vista</td>
      <td>
        <font size=2> protocolo: postgresql
            <br>autenticación: {básica, windows}
            <br>dirección:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; schema
            <br>&#160;&#160;&#160;&#160;&#160; objeto </font>
      </td>
    </tr>
    <tr>
      <td>Power BI</td>
      <td>Contenedor</td>
      <td>Sitio</td>
      <td>
        <font size=2> protocolo: http
            <br>autenticación: {no, básica, windows, oauth}
            <br>dirección:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>
    <tr>
      <td>Power BI</td>
      <td>Informe</td>
      <td>Informe, panel</td>
      <td>
        <font size=2> protocolo: http
            <br>autenticación: {no, básica, windows, oauth}
            <br>dirección:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>
    <tr>
      <td>Salesforce</td>
      <td>Tabla</td>
      <td>Objeto</td>
      <td>
        <font size=2> protocolo: salesforce-com
            <br>autenticación: {básica, windows}
            <br>dirección:
            <br>&#160;&#160;&#160;&#160;&#160; loginServer
            <br>&#160;&#160;&#160;&#160;&#160; class
            <br>&#160;&#160;&#160;&#160;&#160; itemName </font>
      </td>
    </tr>
    <tr>
      <td>SAP Hana</td>
      <td>Contenedor</td>
      <td>Server</td>
      <td>
        <font size=2> protocolo: sap-hana-sql
            <br>autenticación: {protocolo, windows}
            <br>dirección:
            <br>&#160;&#160;&#160;&#160;&#160; servidor </font>
      </td>
    </tr>
    <tr>
      <td>SAP Hana</td>
      <td>Tabla</td>
      <td>Ver</td>
      <td>
        <font size=2> protocolo: sap-hana-sql
            <br>autenticación: {protocolo, windows}
            <br>dirección:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; schema
            <br>&#160;&#160;&#160;&#160;&#160; objeto </font>
      </td>
    </tr>
    <tr>
      <td>SharePoint</td>
      <td>Tabla</td>
      <td>Enumerar</td>
      <td>
        <font size=2> protocolo: sharepoint-list
            <br>autenticación: {básica, windows}
            <br>dirección:
            <br>&#160;&#160;&#160;&#160;&#160; url </font>
      </td>
    </tr>
    <tr>
      <td>Almacenamiento de datos SQL</td>
      <td>Comando</td>
      <td>Procedimiento almacenado</td>
      <td>
        <font size=2> protocolo: tds
            <br>autenticación: {protocolo, windows}
            <br>dirección:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; schema
            <br>&#160;&#160;&#160;&#160;&#160; objeto </font>
      </td>
    </tr>
    <tr>
      <td>Almacenamiento de datos SQL</td>
      <td>Contenedor</td>
      <td>Base de datos</td>
      <td>
        <font size=2> protocolo: tds
          <br>autenticación: {protocolo, windows}
          <br>dirección:
          <br>&#160;&#160;&#160;&#160;&#160; server
          <br>&#160;&#160;&#160;&#160;&#160; base de datos </font>
      </td>
    </tr>
    <tr>
      <td>Almacenamiento de datos SQL</td>
      <td>Tabla</td>
      <td>Tabla, vista</td>
      <td>
        <font size=2> protocolo: tds
            <br>autenticación: {protocolo, windows}
            <br>dirección:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; schema
            <br>&#160;&#160;&#160;&#160;&#160; objeto </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>Comando</td>
      <td>Procedimiento almacenado</td>
      <td>
        <font size=2> protocolo: tds
            <br>autenticación: {protocolo, windows}
            <br>dirección:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; schema
            <br>&#160;&#160;&#160;&#160;&#160; objeto </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>Contenedor</td>
      <td>Base de datos</td>
      <td>
        <font size=2> protocolo: tds
            <br>autenticación: {protocolo, windows}
            <br>dirección:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; base de datos </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server</td>
      <td>Tabla</td>
      <td>Tabla, vista, función con valores de tabla</td>
      <td>
        <font size=2> protocolo: tds
            <br>autenticación: {protocolo, windows}
            <br>dirección:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; schema
            <br>&#160;&#160;&#160;&#160;&#160; objeto </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services - Multidimensional</td>
      <td>Contenedor</td>
      <td>Modelo</td>
      <td>
        <font size=2> protocolo: analysis-services
            <br>autenticación: {windows, básica, anónima, no}
            <br>dirección:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; modelo </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services - Multidimensional</td>
      <td>KPI</td>
      <td>KPI</td>
      <td>
        <font size=2> protocolo: analysis-services
            <br>autenticación: {windows, básica, anónima, no}
            <br>dirección:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; modelo
            <br>&#160;&#160;&#160;&#160;&#160; objeto
            <br>&#160;&#160;&#160;&#160;&#160; objectType: {KPI} </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services - Multidimensional</td>
      <td>Measure</td>
      <td>Measure</td>
      <td>
        <font size=2> protocolo: analysis-services
            <br>autenticación: {windows, básica, anónima, no}
            <br>dirección:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; modelo
            <br>&#160;&#160;&#160;&#160;&#160; objeto
            <br>&#160;&#160;&#160;&#160;&#160; objectType: {Measure} </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Analysis Services - Multidimensional</td>
      <td>Tabla</td>
      <td>Dimension Data</td>
      <td>
        <font size=2> protocolo: analysis-services
            <br>autenticación: {windows, básica, anónima, no}
            <br>dirección:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; modelo
            <br>&#160;&#160;&#160;&#160;&#160; objeto
            <br>&#160;&#160;&#160;&#160;&#160; objectType: {Dimension} </font>
      </td>
    </tr>
    <tr>
      <td>Tabular de SQL Server Analysis Services</td>
      <td>Contenedor</td>
      <td>Modelo</td>
      <td>
        <font size=2> protocolo: analysis-services
            <br>autenticación: {windows, básica, anónima, no}
            <br>dirección:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; modelo </font>
      </td>
    </tr>
    <tr>
      <td>Tabular de SQL Server Analysis Services</td>
      <td>KPI</td>
      <td>KPI</td>
      <td>
        <font size=2> protocolo: analysis-services
            <br>autenticación: {windows, básica, anónima, no}
            <br>dirección:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; modelo
            <br>&#160;&#160;&#160;&#160;&#160; objeto
            <br>&#160;&#160;&#160;&#160;&#160; objectType: {KPI} </font>
      </td>
    </tr>
    <tr>
      <td>Tabular de SQL Server Analysis Services</td>
      <td>Measure</td>
      <td>Measure</td>
      <td>
        <font size=2> protocolo: analysis-services
            <br>autenticación: {windows, básica, anónima, no}
            <br>dirección:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; modelo
            <br>&#160;&#160;&#160;&#160;&#160; objeto
            <br>&#160;&#160;&#160;&#160;&#160; objectType: {Measure} </font>
      </td>
    </tr>
    <tr>
      <td>Tabular de SQL Server Analysis Services</td>
      <td>Tabla</td>
      <td>Tabla</td>
      <td>
        <font size=2> protocolo: analysis-services
            <br>autenticación: {windows, básica, anónima, no}
            <br>dirección:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; modelo
            <br>&#160;&#160;&#160;&#160;&#160; objeto
            <br>&#160;&#160;&#160;&#160;&#160; objectType: {Table} </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Reporting Services</td>
      <td>Contenedor</td>
      <td>Server</td>
      <td>
        <font size=2> protocolo: reporting-services
            <br>autenticación: (windows)
            <br>dirección:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; versión: {ReportingService2010} </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Reporting Services</td>
      <td>Informe</td>
      <td>Informe</td>
      <td>
        <font size=2> protocolo: reporting-services
            <br>autenticación: (windows)
            <br>dirección:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; path
            <br>&#160;&#160;&#160;&#160;&#160; versión: {ReportingService2010} </font>
      </td>
    </tr>
    <tr>
      <td>Teradata</td>
      <td>Contenedor</td>
      <td>Base de datos</td>
      <td>
        <font size=2> protocolo: teradata
            <br>autenticación: {protocolo, windows}
            <br>dirección:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; base de datos </font>
      </td>
    </tr>
    <tr>
      <td>Teradata</td>
      <td>Tabla</td>
      <td>Tabla, vista</td>
      <td>
        <font size=2> protocolo: teradata
            <br>autenticación: {protocolo, windows}
            <br>dirección:
            <br>&#160;&#160;&#160;&#160;&#160; server
            <br>&#160;&#160;&#160;&#160;&#160; database
            <br>&#160;&#160;&#160;&#160;&#160; objeto </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Master Data Services</td>
      <td>Contenedor</td>
      <td>Modelo</td>
      <td>
        <font size="2"> protocol: mssql-mds
          <br>autenticación: (windows)
          <br>dirección:
          <br>&#160;&#160;&#160;&#160;&#160; url
          <br>&#160;&#160;&#160;&#160;&#160; modelo
          <br>&#160;&#160;&#160;&#160;&#160; versión </font>
      </td>
    </tr>
    <tr>
      <td>SQL Server Master Data Services</td>
      <td>Tabla</td>
      <td>Entidad</td>
      <td>
        <font size="2"> protocol: mssql-mds
          <br>autenticación: (windows)
          <br>dirección:
          <br>&#160;&#160;&#160;&#160;&#160; url
          <br>&#160;&#160;&#160;&#160;&#160; modelo
          <br>&#160;&#160;&#160;&#160;&#160; versión
          <br>&#160;&#160;&#160;&#160;&#160; entidad </font>
      </td>
    </tr>
    <tr>
      <td>Otro (ninguno de los anteriores)</td>
      <td>*</td>
      <td>*</td>
      <td>
        <font size=2> protocolo: generic-asset
            <br>dirección:
            <br>&#160;&#160;&#160;&#160;&#160; assetId </font>
      </td>
    </tr>
</table>

<!---HONumber=AcomDC_0727_2016-->