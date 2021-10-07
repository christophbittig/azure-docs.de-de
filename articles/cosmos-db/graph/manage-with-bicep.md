---
title: Erstellen und Verwalten der Azure Cosmos DB-Gremlin-API mit Bicep
description: Verwenden Sie Bicep zum Erstellen und Konfigurieren der Azure Cosmos DB-Gremlin-API.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 9/13/2021
ms.author: mjbrown
ms.openlocfilehash: a5ac7ad31cb09ab57948f9a7b1eb86e07ec608a5
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128700215"
---
# <a name="manage-azure-cosmos-db-gremlin-api-resources-using-bicep"></a>Verwalten von Ressourcen für die Gremlin-API von Azure Cosmos DB mit Bicep

[!INCLUDE[appliesto-gremlin-api](../includes/appliesto-gremlin-api.md)]

In diesem Artikel erfahren Sie, wie Sie Ihre Azure Cosmos DB-Gremlin-API-Konten, -Datenbanken und -Graphen mithilfe von Bicep bereitstellen und verwalten.

In diesem Artikel werden Bicep-Beispiele für Gremlin-API-Konten gezeigt. Es gibt jedoch auch Bicep-Beispiele für [SQL](../sql/manage-with-bicep.md)-, [Cassandra](../cassandra/manage-with-bicep.md)-, [MongoDB](../mongodb/manage-with-bicep.md)- und [Tabellen](../table/manage-with-bicep.md)-APIs.

> [!IMPORTANT]
>
> * Die Länge von Kontonamen ist auf 44 Zeichen (ausschließlich Kleinbuchstaben) beschränkt.
> * Sie müssen die Vorlage mit aktualisierter Angabe der RU/s erneut bereitstellen, um die Durchsatzwerte ändern zu können.
> * Wenn Sie einem Azure Cosmos-Konto Speicherorte hinzufügen oder aus ihm entfernen, können Sie nicht gleichzeitig andere Eigenschaften ändern. Diese Vorgänge müssen separat ausgeführt werden.

Wenn Sie eine der unten aufgeführten Azure Cosmos DB-Ressourcen erstellen möchten, kopieren Sie das folgende Beispiel in eine neue Bicep-Datei. Optional können Sie eine Parameterdatei erstellen, die beim Bereitstellen mehrerer Instanzen derselben Ressource mit unterschiedlichen Namen und Werten verwendet werden soll. Für das Bereitstellen von Azure Resource Manager-Vorlagen stehen Ihnen viele Möglichkeiten zur Verfügung, beispielsweise die [Azure-Befehlszeilenschnittstelle](../../azure-resource-manager/bicep/deploy-cli.md), [Azure PowerShell](../../azure-resource-manager/bicep/deploy-powershell.md) und die [Cloud Shell](../../azure-resource-manager/bicep/deploy-cloud-shell.md).

<a id="create-autoscale"></a>

## <a name="gremlin-api-with-autoscale-provisioned-throughput"></a>Gremlin-API mit per Autoskalierung bereitgestelltem Durchsatz

Erstellen Sie ein Azure Cosmos-Konto für die Gremlin-API mit einer Datenbank und einem Graphen mit automatisch skaliertem Durchsatz.

:::code language="bicep" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-gremlin-autoscale/main.bicep":::

<a id="create-manual"></a>

## <a name="gremlin-api-with-standard-provisioned-throughput"></a>Gremlin-API mit standardmäßig bereitgestelltem Durchsatz

Erstellen Sie ein Azure Cosmos-Konto für die Gremlin-API mit einer Datenbank und einem Graphen mit standardmäßig bereitgestelltem Durchsatz.

:::code language="bicep" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-gremlin/main.bicep":::

## <a name="next-steps"></a>Nächste Schritte

Hier sind einige zusätzlichen Ressourcen:

* [Bicep-Dokumentation](../../azure-resource-manager/bicep/index.yml)
* [Installieren von Bicep-Tools](../../azure-resource-manager/bicep/install.md)
