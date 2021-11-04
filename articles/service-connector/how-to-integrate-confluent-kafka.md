---
title: Integrieren von Apache Kafka in Confluent Cloud mit Service Connector
description: Integrieren von Apache Kafka in Confluent Cloud in Ihre Anwendung mit Service Connector
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: how-to
ms.date: 10/29/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: c0b5c47982fb0b3af7b2ecac6fde12f1e2f2f101
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131021530"
---
# <a name="integrate-apache-kafka-on-confluent-cloud-with-service-connector"></a>Integrieren von Apache Kafka in Confluent Cloud mit Service Connector

Auf dieser Seite werden die unterstützten Authentifizierungstypen und Clienttypen von Apache Kafka in Confluent Cloud mit Service mithilfe von Service Connector angezeigt. Möglicherweise können Sie weiterhin eine Verbindung mit Apache Kafka in anderen Programmiersprachen herstellen, ohne den Service-Connector zu verwenden. Diese Seite zeigt auch den Namen und den Wert der Standard-Umgebungsvariablen (oder die Spring Boot-Konfiguration), die Sie erhalten, wenn Sie die Dienstverbindung erstellen. Sie können mehr über die [Namenskonvention für Service Connector-Umgebungsvariablen](concept-service-connector-internals.md) erfahren.

## <a name="supported-compute-service"></a>Unterstützter Computing-Dienst

- Azure App Service
- Azure Spring Cloud

## <a name="supported-authentication-types-and-client-types"></a>Unterstützte Authentifizierungstypen und Clienttypen

| Clienttyp | Systemseitig zugewiesene verwaltete Identität | Vom Benutzer zugewiesene verwaltete Identität | Geheimkennwort/Verbindungszeichenfolge | Dienstprinzipal |
| --- | --- | --- | --- | --- |
| .Net | | | ![Ja-Symbol](./media/green-check.png) | |
| Java | | | ![Ja-Symbol](./media/green-check.png) | |
| Java - Spring Boot | | | ![Ja-Symbol](./media/green-check.png) | |
| Node.js | | | ![Ja-Symbol](./media/green-check.png) | |
| Python | | | ![Ja-Symbol](./media/green-check.png) | |

## <a name="default-environment-variable-names-or-application-properties"></a>Namen von Standard-Umgebungsvariablen oder Anwendungseigenschaften

### <a name="net-java-nodejs-and-python"></a>.NET, Java, Node.JS und Python

| Name der Standard-Umgebungsvariable | BESCHREIBUNG | Beispielwert |
| --- | --- | --- |
| AZURE_CONFLUENTCLOUDKAFKA_BOOTSTRAPSERVER | Ihr Kafka-Bootstrapserver | `pkc-{serverName}.eastus.azure.confluent.cloud:9092` |
| AZURE_CONFLUENTCLOUDKAFKA_KAFKASASLCONFIG | Ihre Kafka SASL-Konfiguration | `org.apache.kafka.common.security.plain.PlainLoginModule required username='{bootstrapServerKey}' password='{bootstrapServerSecret}';` |
| AZURE_CONFLUENTCLOUDSCHEMAREGISTRY_URL | Ihre Confluent-Registrierungs-URL | `https://psrc-{serverName}.westus2.azure.confluent.cloud` |
| AZURE_CONFLUENTCLOUDSCHEMAREGISTRY_USERINFO |  Ihre Confluent-Registrierungsbenutzerinformationen | `{schemaRegistryKey} + ":" + {schemaRegistrySecret}` |

### <a name="spring-boot"></a>Spring Boot

| Name der Standard-Umgebungsvariable | BESCHREIBUNG | Beispielwert |
| --- | --- | --- |
| spring.kafka.properties.bootstrap.servers | Ihr Kafka-Bootstrapserver | `pkc-{serverName}.eastus.azure.confluent.cloud:9092` |
| spring.kafka.properties.sasl.jaas.config | Ihre Kafka SASL-Konfiguration | `org.apache.kafka.common.security.plain.PlainLoginModule required username='{bootstrapServerKey}' password='{bootstrapServerSecret}';` |
| spring.kafka.properties.schema.registry.url | Ihre Confluent-Registrierungs-URL | `https://psrc-{serverName}.westus2.azure.confluent.cloud` |
| spring.kafka.properties.schema.registry.basic.auth.user.info | Ihre Confluent-Registrierungsbenutzerinformationen | `{schemaRegistryKey} + ":" + {schemaRegistrySecret}` |

## <a name="next-steps"></a>Nächste Schritte

Befolgen Sie die unten aufgeführten Tutorials, um mehr über Service Connector zu erfahren.

> [!div class="nextstepaction"]
> [Informationen zu Service Connector-Konzepten](./concept-service-connector-internals.md)
