---
title: Erstellen und Verwalten der Azure Cosmos DB-Cassandra-API mit Bicep
description: Verwenden Sie Bicep zum Erstellen und Konfigurieren der Azure Cosmos DB-Cassandra-API.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 9/13/2021
ms.author: mjbrown
ms.openlocfilehash: 0433f6830100fc23420006ec2b980b7ccf7f9692
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128598482"
---
# <a name="manage-azure-cosmos-db-cassandra-api-resources-using-bicep"></a>Verwalten von Ressourcen für die Cassandra-API von Azure Cosmos DB mit Bicep

[!INCLUDE[appliesto-cassandra-api](../includes/appliesto-cassandra-api.md)]

In diesem Artikel erfahren Sie, wie Sie Ihre Cassandra-API-Konten, -Keyspaces und -Tabellen in Azure Cosmos DB mithilfe von Bicep bereitstellen und verwalten.

In diesem Artikel werden Bicep-Beispiele für Cassandra-API-Konten gezeigt. Es gibt jedoch auch Bicep-Beispiele für [SQL](../sql/manage-with-bicep.md)-, [Gremlin](../graph/manage-with-bicep.md)-, [MongoDB](../mongodb/manage-with-bicep.md)- und [Tabellen](../table/manage-with-bicep.md)-APIs.

> [!IMPORTANT]
>
> * Die Länge von Kontonamen ist auf 44 Zeichen (ausschließlich Kleinbuchstaben) beschränkt.
> * Sie müssen die Vorlage mit aktualisierter Angabe der RU/s erneut bereitstellen, um die Durchsatzwerte ändern zu können.
> * Wenn Sie einem Azure Cosmos-Konto Speicherorte hinzufügen oder aus ihm entfernen, können Sie nicht gleichzeitig andere Eigenschaften ändern. Diese Vorgänge müssen separat ausgeführt werden.

Wenn Sie eine der unten aufgeführten Azure Cosmos DB-Ressourcen erstellen möchten, kopieren Sie das folgende Beispiel in eine neue Bicep-Datei. Optional können Sie eine Parameterdatei erstellen, die beim Bereitstellen mehrerer Instanzen derselben Ressource mit unterschiedlichen Namen und Werten verwendet werden soll. Für das Bereitstellen von Azure Resource Manager-Vorlagen stehen Ihnen viele Möglichkeiten zur Verfügung, beispielsweise die [Azure-Befehlszeilenschnittstelle](../../azure-resource-manager/bicep/deploy-cli.md), [Azure PowerShell](../../azure-resource-manager/bicep/deploy-powershell.md) und die [Cloud Shell](../../azure-resource-manager/bicep/deploy-cloud-shell.md).

<a id="create-autoscale"></a>

## <a name="cassandra-api-with-autoscale-provisioned-throughput"></a>Cassandra-API mit per Autoskalierung bereitgestelltem Durchsatz

Erstellt ein Azure Cosmos-Konto in zwei Regionen mit Optionen für Konsistenz und Failover, wobei ein Keyspace und eine Tabelle mit Autoskalierungsdurchsatz konfiguriert sind

:::code language="bicep" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-cassandra-autoscale/main.bicep":::

<a id="create-manual"></a>

## <a name="cassandra-api-with-standard-provisioned-throughput"></a>Cassandra-API mit bereitgestelltem Standarddurchsatz

Erstellt ein Azure Cosmos-Konto in zwei Regionen mit Optionen für Konsistenz und Failover, wobei ein Keyspace und eine Tabelle mit Standarddurchsatz konfiguriert sind

:::code language="bicep" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-cassandra/main.bicep":::

## <a name="next-steps"></a>Nächste Schritte

Hier sind einige zusätzlichen Ressourcen:

* [Bicep-Dokumentation](../../azure-resource-manager/bicep/index.yml)
* [Installieren von Bicep-Tools](../../azure-resource-manager/bicep/install.md)
