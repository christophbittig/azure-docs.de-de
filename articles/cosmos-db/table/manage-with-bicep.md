---
title: Azure Cosmos DB Tabellen-API mit Bicep erstellen und verwalten
description: Verwenden Sie Bicep, um Azure Cosmos DB Tabellen-API zu erstellen und zu konfigurieren.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: how-to
ms.date: 09/13/2021
ms.author: mjbrown
ms.openlocfilehash: fe911a945de6b7b15c49641b7aff3850c3759da2
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128598446"
---
# <a name="manage-azure-cosmos-db-table-api-resources-using-bicep"></a>Verwalten Sie Azure Cosmos DB Tabellen-API-Ressourcen mit Bicep

[!INCLUDE[appliesto-table-api](../includes/appliesto-table-api.md)]

In diesem Artikel lernen Sie, wie Sie Bicep verwenden, um Ihre Azure Cosmos DB Tabellen-API-Konten und -Tabellen bereitzustellen und zu verwalten.

Dieser Artikel enthält nur Beispiele für Tabellen-API-Konten. Sie können auch Bicep-Beispiele für die Artikel [Cassandra](../cassandra/manage-with-bicep.md), [ Gremlin](../graph/manage-with-bicep.md), [MongoDB](../mongodb/manage-with-bicep.md), [SQL](../sql/manage-with-bicep.md) finden.

> [!IMPORTANT]
>
> * Die Länge von Kontonamen ist auf 44 Zeichen (ausschließlich Kleinbuchstaben) beschränkt.
> * Sie müssen die Vorlage mit aktualisierter Angabe der RU/s erneut bereitstellen, um die Durchsatzwerte ändern zu können.
> * Wenn Sie einem Azure Cosmos-Konto Speicherorte hinzufügen oder aus ihm entfernen, können Sie nicht gleichzeitig andere Eigenschaften ändern. Diese Vorgänge müssen separat ausgeführt werden.

Wenn Sie eine der unten aufgeführten Azure Cosmos DB-Ressourcen erstellen möchten, kopieren Sie das folgende Beispiel in eine neue Bicep-Datei. Optional können Sie eine Parameterdatei erstellen, die beim Bereitstellen mehrerer Instanzen derselben Ressource mit unterschiedlichen Namen und Werten verwendet werden soll. Für das Bereitstellen von Azure Resource Manager-Vorlagen stehen Ihnen viele Möglichkeiten zur Verfügung, beispielsweise die [Azure-Befehlszeilenschnittstelle](../../azure-resource-manager/bicep/deploy-cli.md), [Azure PowerShell](../../azure-resource-manager/bicep/deploy-powershell.md) und die [Cloud Shell](../../azure-resource-manager/bicep/deploy-cloud-shell.md).

> [!TIP]
> Aktivieren Sie im Azure-Portal den Durchsatz auf Kontoebene, um bei Verwendung der Tabellen-API die gemeinsame Nutzung des Durchsatzes zu aktivieren. Es ist nicht möglich, den gemeinsamen Durchsatz auf Kontoebene mithilfe von Bicep festzulegen.

<a id="create-autoscale"></a>

## <a name="azure-cosmos-account-for-table-with-autoscale-throughput"></a>Azure Cosmos-Konto für eine Tabelle mit automatisch skaliertem Durchsatz

Erstellen Sie ein Azure Cosmos-Konto für Tabellen-API mit einer Tabelle mit automatischem Durchsatz.

:::code language="bicep" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-table-autoscale/main.bicep":::

<a id="create-manual"></a>

## <a name="azure-cosmos-account-for-table-with-standard-provisioned-throughput"></a>Azure Cosmos-Konto für Tabellen mit standardmäßig bereitgestelltem Durchsatz

Erstellen Sie ein Azure Cosmos-Konto für Tabellen-API mit einer Tabelle mit Normdurchsatz.

:::code language="bicep" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-table/main.bicep":::

## <a name="next-steps"></a>Nächste Schritte

Hier sind einige zusätzlichen Ressourcen:

* [Bicep-Dokumentation](../../azure-resource-manager/bicep/index.yml)
* [Installieren von Bicep-Tools](../../azure-resource-manager/bicep/install.md)
