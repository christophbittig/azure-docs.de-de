---
title: 'Hinzufügen von Connectors für Confluent Cloud: Azure-Partnerlösungen'
description: In diesem Artikel wird beschrieben, wie Sie Connectors für Confluent Cloud installieren, die Sie mit Azure-Ressourcen verwenden.
ms.service: partner-services
ms.topic: conceptual
ms.date: 06/07/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: c4a15a54c5b9fb3b73a3fcaf81d63a9e176b49c5
ms.sourcegitcommit: 096e7972e2a1144348f8d648f7ae66154f0d4b39
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/23/2021
ms.locfileid: "112534725"
---
# <a name="add-connectors-for-confluent-cloud"></a>Hinzufügen von Connectors für Confluent Cloud

In diesem Artikel wird beschrieben, wie Sie Connectors für Azure-Ressourcen für Confluent Cloud installieren.

## <a name="connector-to-azure-cosmos-db"></a>Connector für Azure Cosmos DB

Installieren Sie den Cosmos DB-Connector vom Confluent Hub-Client aus, wie es in der [Confluent Hub-Auflistung](https://www.confluent.io/hub/microsoftcorporation/kafka-connect-cosmos) empfohlen wird. 

Um den Connector manuell zu installieren, laden Sie zuerst eine Uber-JAR-Datei von der [Seite mit Cosmos DB Releases](https://github.com/microsoft/kafka-connect-cosmosdb/releases) herunter. Alternativ können Sie [eine eigene Uber-JAR-Datei direkt aus dem Quellcode erstellen](https://github.com/microsoft/kafka-connect-cosmosdb/blob/dev/doc/README_Sink.md#install-sink-connector). Schließen Sie die Installation ab, indem Sie die Anweisungen befolgen, die in der Confluent-Dokumentation zum [manuellen Installieren von Connectors](https://docs.confluent.io/home/connect/install.html#install-connector-manually) beschrieben sind.  

## <a name="next-steps"></a>Nächste Schritte

Hilfe bei der Problembehandlung finden Sie unter [Troubleshooting Apache Kafka for Confluent Cloud solutions](troubleshoot.md) (Problembehandlung in Apache Kafka für Confluent Cloud-Lösungen).
