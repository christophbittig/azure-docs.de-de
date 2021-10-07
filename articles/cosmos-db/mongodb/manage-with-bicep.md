---
title: Erstellen und verwalten Sie MongoDB API für Azure Cosmos DB mit Bicep
description: Verwenden Sie Bicep, um MongoDB API Azure Cosmos DB API zu erstellen und zu konfigurieren.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 09/13/2021
ms.author: mjbrown
ms.openlocfilehash: e6ded17c15b9a126a996f4e6f368a5ab5c735ed9
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128700418"
---
# <a name="manage-azure-cosmos-db-mongodb-api-resources-using-bicep"></a>Verwalten Sie Azure Cosmos DB MongoDB API-Ressourcen mit Bicep

[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

In diesem Artikel lernen Sie, wie Sie Bicep verwenden, um Ihre Azure Cosmos DB-Konten für MongoDB API, Datenbanken und Sammlungen bereitzustellen und zu verwalten.

Dieser Artikel zeigt Bicep-Beispiele für Gremlin-API-Konten. Sie können auch Bicep-Beispiele für [SQL](../sql/manage-with-bicep.md) finden. [Cassandra](../cassandra/manage-with-bicep.md) und [Gremlin](../graph/manage-with-bicep.md) sowie [Tabellen](../table/manage-with-bicep.md)- APIs.

> [!IMPORTANT]
>
> * Die Länge von Kontonamen ist auf 44 Zeichen (ausschließlich Kleinbuchstaben) beschränkt.
> * Sie müssen die Vorlage mit aktualisierter Angabe der RU/s erneut bereitstellen, um die Durchsatzwerte ändern zu können.
> * Wenn Sie einem Azure Cosmos-Konto Speicherorte hinzufügen oder aus ihm entfernen, können Sie nicht gleichzeitig andere Eigenschaften ändern. Diese Vorgänge müssen separat ausgeführt werden.

Um eine der unten aufgeführten Azure Cosmos DB-Ressourcen zu erstellen, kopieren Sie das folgende Beispiel in eine neue bicep-Datei. Sie können optional eine Parameterdatei erstellen, die bei der Bereitstellung mehrerer Instanzen derselben Ressource mit unterschiedlichen Namen und Werten verwendet wird. Es gibt viele Möglichkeiten, Azure Resource Manager-Vorlagen bereitzustellen, einschließlich [Azure CLi](../../azure-resource-manager/bicep/deploy-cli.md), [Azure PowerShell](../../azure-resource-manager/bicep/deploy-powershell.md) und [ Cloud Shell](../../azure-resource-manager/bicep/deploy-cloud-shell.md).

<a id="create-autoscale"></a>

## <a name="mongodb-api-with-autoscale-provisioned-throughput"></a>MongoDB-API mit autoskaliertem bereitgestelltem Durchsatz

Mit dieser Vorlage wird ein Azure Cosmos-Konto für MongoDB API (3.2, 3.6 oder 4.0) mit zwei Sammlungen erstellt, die den autoskalierten Durchsatz auf Datenbankebene freigeben.

:::code language="bicep" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-mongodb-autoscale/main.bicep":::

<a id="create-manual"></a>

## <a name="mongodb-api-with-standard-provisioned-throughput"></a>MongoDB API mit standardmäßig bereitgestelltem Durchsatz

Erstellen Sie ein Azure Cosmos-Konto für MongoDB API (3.2, 3.6 oder 4.0) mit zwei Sammlungen, die 400 RU/s Normdurchsatz (manuell) auf Datenbankebene freigeben.

:::code language="bicep" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-mongodb/main.bicep":::

## <a name="next-steps"></a>Nächste Schritte

Hier sind einige zusätzlichen Ressourcen:

* [Bicep-Dokumentation](../../azure-resource-manager/bicep/index.yml)
* [Installieren von Bicep-Tools](../../azure-resource-manager/bicep/install.md)
* Versuchen Sie, die Kapazitätsplanung für eine Migration zu Azure Cosmos DB durchzuführen? Sie können Informationen zu Ihrem vorhandenen Datenbankcluster für die Kapazitätsplanung verwenden.
  * Wenn Sie nur die Anzahl der virtuellen Kerne und Server in Ihrem vorhandenen Datenbankcluster kennen, lesen Sie die Informationen zum [Schätzen von Anforderungseinheiten mithilfe von virtuellen Kernen oder virtuellen CPUs](../convert-vcore-to-request-unit.md)
  * Wenn Sie die typischen Anforderungsraten für Ihre aktuelle Datenbank-Workload kennen, lesen Sie die Informationen zum [Schätzen von Anforderungseinheiten mit dem Azure Cosmos DB-Kapazitätsplaner](estimate-ru-capacity-planner.md)
