---
title: Migrieren zu Azure Event Hubs für Apache Kafka
description: In diesem Artikel wird erläutert, wie Sie Clients von Apache Kafka zu Azure Event Hubs migrieren.
ms.topic: article
ms.date: 09/20/2021
ms.openlocfilehash: 5c8a9598f0f1c8018f53057638bd726eed6afc42
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128662918"
---
# <a name="migrate-to-azure-event-hubs-for-apache-kafka-ecosystems"></a>Migrieren zu Azure Event Hubs für Apache Kafka-Ökosysteme
Azure Event Hubs macht einen Apache Kafka-Endpunkt verfügbar, der es Ihnen ermöglicht, eine Verbindung mit Event Hubs herzustellen, indem Sie das Kafka-Protokoll verwenden. Durch minimale Änderungen an Ihrer vorhandenen Kafka-Anwendung können Sie eine Verbindung mit Azure Event Hubs herstellen und die Vorteile des Azure-Ökosystems nutzen. Event Hubs funktioniert mit vielen Ihrer vorhandenen Kafka-Anwendungen, einschließlich MirrorMaker. Weitere Informationen finden Sie unter [Event Hubs für Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md).

## <a name="pre-migration"></a>Vor der Migration 

### <a name="create-an-azure-account"></a>Erstellen eines Azure-Kontos
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) erstellen, bevor Sie beginnen.

### <a name="create-an-event-hubs-namespace"></a>Erstellen eines Event Hubs-Namespace
Befolgen Sie die schrittweise Anleitung in dem Artikel [Erstellen eines Event Hubs](event-hubs-create.md), um einen Event Hubs-Namespace und einen Event Hub zu erstellen. 

### <a name="connection-string"></a>Verbindungszeichenfolge
Führen Sie die Schritte aus dem Artikel [Abrufen einer Verbindungszeichenfolge aus dem Portal](event-hubs-get-connection-string.md#get-connection-string-from-the-portal) aus. Und notieren Sie sich die Verbindungszeichenfolge zur späteren Verwendung. 

### <a name="fully-qualified-domain-name-fqdn"></a>Vollqualifizierter Domänenname (FQDN)
Möglicherweise benötigen Sie auch den FQDN, der auf Ihren Event Hub-Namespace verweist. Den FQDN finden Sie innerhalb Ihrer Verbindungszeichenfolge wie folgt:

`Endpoint=sb://`**`mynamespace.servicebus.windows.net`**`/;SharedAccessKeyName=XXXXXX;SharedAccessKey=XXXXXX`

Wenn Ihr Event Hubs-Namespace in einer nicht öffentlichen Cloud bereitgestellt wird, kann Ihr Domänenname abweichen (z. b. \*.servicebus.chinacloudapi.cn, \*.servicebus.usgovcloudapi.net oder \*.servicebus.cloudapi.de).

## <a name="migration"></a>Migration 

### <a name="update-your-kafka-client-configuration"></a>Aktualisieren Ihrer Kafka-Clientkonfiguration

Um eine Verbindung mit einem Kafka-fähigen Event Hub herzustellen, müssen Sie die Kafka-Clientkonfigurationen aktualisieren. Wenn Sie Probleme haben, Ihre zu finden, suchen Sie nach der Stelle, wo `bootstrap.servers` in Ihrer Anwendung festgelegt ist.

Fügen Sie die folgenden Konfigurationen dort ein, wo es in Ihrer Anwendung sinnvoll ist. Stellen Sie sicher, dass Sie die `bootstrap.servers`- und `sasl.jaas.config`-Werte so aktualisieren, dass sie den Client an Ihren Event Hubs Kafka-Endpunkt mit der richtigen Authentifizierung weiterleiten. 

```
bootstrap.servers={MYNAMESPACE}.servicebus.windows.net:9093
request.timeout.ms=60000
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{CONNECTION STRING TO YOUR NAMESPACE}";
``` 

Wenn `sasl.jaas.config` in Ihrem Framework als Konfiguration nicht unterstützt wird, müssen Sie die Konfigurationen suchen, die zum Festlegen des SASL-Benutzernamens und -Kennworts verwendet werden, und diese stattdessen verwenden. Legen Sie den Benutzernamen auf `$ConnectionString` und das Kennwort auf Ihre Event Hubs-Verbindungszeichenfolge fest.

## <a name="post-migration"></a>Nach der Migration
Führen Sie Ihre Kafka-Anwendung aus, die Ereignisse an den Event Hub sendet. Vergewissern Sie sich dann im Azure-Portal, dass der Event Hub die Ereignisse empfängt. Wechseln Sie auf der Seite **Übersicht** Ihres Event Hubs-Namespace zur Ansicht **Nachrichten** im Abschnitt **Metrik**. Aktualisieren Sie die Seite, um das Diagramm zu aktualisieren. Es kann einige Sekunden dauern, bis angezeigt wird, dass die Nachrichten empfangen wurden. 

[![Überprüfen, ob der Event Hub die Nachrichten empfangen hat](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png)](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png#lightbox)


## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu Event Hubs und Event Hubs für Kafka finden Sie in folgenden Artikeln:  

- [Apache Kafka-Leitfaden zur Problembehandlung für Event Hubs](apache-kafka-troubleshooting-guide.md)
- [Häufig gestellte Fragen (FAQ) zu Event Hubs für Apache Kafka](apache-kafka-frequently-asked-questions.yml)
- [Apache Kafka-Entwicklerleitfaden für Azure Event Hubs](apache-kafka-developer-guide.md)
- [Empfohlene Konfigurationen](apache-kafka-configurations.md)