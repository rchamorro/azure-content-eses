
<properties
   pageTitle="Guía de Elasticsearch en Azure | Microsoft Azure"
   description="Guía de Elasticsearch en Azure."
   services=""
   documentationCenter="na"
   authors="mabsimms"
   manager="marksou"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/18/2016"
   ms.author="masimms"/>

# Guía de Elasticsearch en Azure

Elasticsearch es una base de datos y un motor de búsqueda de código abierto de gran escalabilidad. Es adecuado para situaciones que requieren un análisis y un descubrimiento rápidos de la información contenida en grandes conjuntos de datos. Esta guía analiza algunos aspectos claves a tener en cuenta al diseñar un clúster de Elasticsearch:

- **[Ejecución de ElasticSearch en Azure][]**: se proporciona una breve introducción a la estructura general de Elasticsearch y se describe cómo implementar un clúster de Elasticsearch con Azure.
- **[Ajuste del rendimiento de introducción de datos para Elasticsearch en Azure][]**: se describen las opciones de implementación y configuración que se deben tener en cuenta para un clúster de Elasticsearch del que se espera una alta tasa de ingesta de datos.
- **[Ajuste de la agregación de datos y el rendimiento de las consultas para Elasticsearch en Azure][]**: se resumen las opciones que puede tener en cuenta al determinar la mejor forma de optimizar el sistema para el rendimiento de las búsquedas y las consultas.
- **[Configuración de la resistencia y la recuperación de Elasticsearch en Azure][]**: se resumen las opciones disponibles de resistencia y recuperación con Elasticsearch cuando se hospeda en Azure.
- **[Creación de un entorno de pruebas de rendimiento para Elasticsearch en Azure][]**: se describe el modo de configurar un entorno para probar el rendimiento de un clúster de Elasticsearch.
- **[Implementación de un plan de prueba de JMeter][]**: se describe cómo crear y usar una muestra de JUnit que pueda generar y cargar datos en un clúster de Elasticsearch como parte de un plan de pruebas de JMeter.
- **[Implementación de una muestra de JUnit en JMeter para probar el rendimiento de Elasticsearch][]**: resume las experiencias claves adquiridas con la generación y ejecución de ingestas de datos y planes de prueba de consultas. 
- **[Ejecución de pruebas automatizadas de resistencia de Elasticsearch][]**: resume las pruebas de resistencia usadas en el artículo anterior.
- **[Ejecución de pruebas automatizadas de rendimiento de Elasticsearch][]**: resume las pruebas de rendimiento usadas en el artículo anterior.

> [AZURE.NOTE] En esta guía, se da por supuesto que conoce los conceptos básicos de Elasticsearch. Para obtener más información, visite el [sitio web de Elasticsearch](https://www.elastic.co/products/elasticsearch).

[Ejecución de ElasticSearch en Azure]: guidance-elasticsearch-running-on-azure.md
[Ajuste del rendimiento de introducción de datos para Elasticsearch en Azure]: guidance-elasticsearch-tuning-data-ingestion-performance.md
[Creación de un entorno de pruebas de rendimiento para Elasticsearch en Azure]: guidance-elasticsearch-creating-performance-testing-environment.md
[Implementación de un plan de prueba de JMeter]: guidance-elasticsearch-implementing-jmeter-test-plan.md
[Implementación de una muestra de JUnit en JMeter para probar el rendimiento de Elasticsearch]: guidance-elasticsearch-deploying-jmeter-junit-sampler.md
[Ajuste de la agregación de datos y el rendimiento de las consultas para Elasticsearch en Azure]: guidance-elasticsearch-tuning-data-aggregation-and-query-performance.md
[Configuración de la resistencia y la recuperación de Elasticsearch en Azure]: guidance-elasticsearch-configuring-resilience-and-recovery.md
[Ejecución de pruebas automatizadas de resistencia de Elasticsearch]: guidance-elasticsearch-running-automated-resilience-tests.md
[Ejecución de pruebas automatizadas de rendimiento de Elasticsearch]: guidance-elasticsearch-running-automated-performance-tests.md

<!---HONumber=AcomDC_0224_2016 cc test approve-->
