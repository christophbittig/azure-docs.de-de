---
title: 'Hinzufügen von Connectors für Confluent Cloud: Azure-Partnerlösungen'
description: In diesem Artikel wird beschrieben, wie Sie Connectors für Confluent Cloud installieren, die Sie mit Azure-Ressourcen verwenden.
ms.service: partner-services
ms.topic: conceptual
ms.date: 09/03/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: a5ef809be7518c4d99ff208e769af918fe579d4f
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/04/2021
ms.locfileid: "123480694"
---
# <a name="add-connectors-for-confluent-cloud"></a>Hinzufügen von Connectors für Confluent Cloud

In diesem Artikel wird beschrieben, wie Sie Connectors für Azure-Ressourcen für Confluent Cloud installieren.

## <a name="connector-to-azure-cosmos-db"></a>Connector für Azure Cosmos DB

**Der vollständig verwaltete Connector für den Azure Cosmos DB-Senkenconnector** ist in Confluent Cloud allgemein verfügbar. Durch den vollständig verwalteten Connector müssen keine benutzerdefinierten Integrationen mehr entwickelt und verwaltet werden, und der gesamtbetriebliche Aufwand für das Verbinden Ihrer Daten zwischen Confluent Cloud und Azure Cosmos DB wird reduziert. Der Microsoft Azure Cosmos DB-Senkenconnector für Confluent Cloud liest und schreibt Daten in eine bzw. aus einer Microsoft Azure Cosmos DB-Instanz. Der Connector fragt Daten aus Kafka ab und schreibt sie in Datenbankcontainer.

Informationen zum Einrichten Ihres Connectors finden Sie unter [Azure Cosmos DB-Senkenconnector für Confluent Cloud](https://docs.confluent.io/cloud/current/connectors/cc-azure-cosmos-sink.html).

Der **selbstverwaltete Azure Cosmos DB-Connector** muss manuell installiert werden. Laden Sie zunächst eine Uber-JAR-Datei von der Seite [Cosmos DB Releases](https://github.com/microsoft/kafka-connect-cosmosdb/releases) herunter. Alternativ können Sie [eine eigene Uber-JAR-Datei direkt aus dem Quellcode erstellen](https://github.com/microsoft/kafka-connect-cosmosdb/blob/dev/doc/README_Sink.md#install-sink-connector). Schließen Sie die Installation ab, indem Sie die Anweisungen befolgen, die in der Confluent-Dokumentation zum [manuellen Installieren von Connectors](https://docs.confluent.io/home/connect/install.html#install-connector-manually) beschrieben sind.  

## <a name="next-steps"></a>Nächste Schritte

Hilfe bei der Problembehandlung finden Sie unter [Troubleshooting Apache Kafka for Confluent Cloud solutions](troubleshoot.md) (Problembehandlung in Apache Kafka für Confluent Cloud-Lösungen).
