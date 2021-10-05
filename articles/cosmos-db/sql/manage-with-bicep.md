---
title: Erstellen und Verwalten von Azure Cosmos DB mit Bicep
description: Verwenden von Bicep zum Erstellen und Konfigurieren der Core-API (SQL) von Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 09/13/2021
ms.author: mjbrown
ms.openlocfilehash: b5a3fcdffe3c93a396bb607595b1ee02775a26f2
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128598561"
---
# <a name="manage-azure-cosmos-db-core-sql-api-resources-with-bicep"></a>Verwalten von Ressourcen für die Core-API (SQL) von Azure Cosmos DB mit Bicep

[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

In diesem Artikel erfahren Sie, wie Sie Ihre Azure Cosmos DB-Konten, -Datenbanken und -Container mithilfe von Bicep bereitstellen und verwalten.

In diesem Artikel werden Bicep-Beispiele für Core-API-Konten (SQL) gezeigt. Es gibt jedoch auch Bicep-Beispiele für die [Cassandra](../cassandra/manage-with-bicep.md)-, [Gremlin](../graph/manage-with-bicep.md)-, [MongoDB](../mongodb/manage-with-bicep.md)- und [Tabellen](../table/manage-with-bicep.md)-APIs.

> [!IMPORTANT]
>
> * Die Länge von Kontonamen ist auf 44 Zeichen (ausschließlich Kleinbuchstaben) beschränkt.
> * Sie müssen die Bicep-Datei mit aktualisierter Angabe der RU/s erneut bereitstellen, um die Durchsatzwerte ändern zu können.
> * Wenn Sie einem Azure Cosmos-Konto Speicherorte hinzufügen oder aus ihm entfernen, können Sie nicht gleichzeitig andere Eigenschaften ändern. Diese Vorgänge müssen separat ausgeführt werden.
> * Azure Cosmos DB-Ressourcen können nicht umbenannt werden, da dies gegen die Arbeitsweise von Azure Resource Manager mit Ressourcen-URIs verstößt.
> * Wenn Sie Durchsatz auf Datenbankebene bereitstellen und für alle Container freigeben möchten, wenden Sie die Durchsatzwerte auf die Eigenschaft „Datenbankoptionen“ an.

Wenn Sie eine der unten aufgeführten Azure Cosmos DB-Ressourcen erstellen möchten, kopieren Sie das folgende Beispiel in eine neue Bicep-Datei. Optional können Sie eine Parameterdatei erstellen, die beim Bereitstellen mehrerer Instanzen derselben Ressource mit unterschiedlichen Namen und Werten verwendet werden soll. Für das Bereitstellen von Azure Resource Manager-Vorlagen stehen Ihnen viele Möglichkeiten zur Verfügung, beispielsweise die [Azure-Befehlszeilenschnittstelle](../../azure-resource-manager/bicep/deploy-cli.md), [Azure PowerShell](../../azure-resource-manager/bicep/deploy-powershell.md) und die [Cloud Shell](../../azure-resource-manager/bicep/deploy-cloud-shell.md).

<a id="create-autoscale"></a>

## <a name="azure-cosmos-account-with-autoscale-throughput"></a>Azure Cosmos-Konto mit automatisch skaliertem Durchsatz

Erstellt ein Azure Cosmos-Konto in zwei Regionen mit Optionen für Konsistenz und Failover, wobei Datenbank und Container für den automatisch skalierten Durchsatz konfiguriert sind, für den die meisten Richtlinienoptionen aktiviert sind

:::code language="bicep" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-sql-autoscale/main.bicep":::

<a id="create-analytical-store"></a>

## <a name="azure-cosmos-account-with-analytical-store"></a>Azure Cosmos-Konto mit Analysespeicher

Erstellt ein Azure Cosmos-Konto in einer Region mit einem Container mit aktivierter analytischer Gültigkeitsdauer und Optionen für die manuelle oder automatische Skalierung des Durchsatzes

:::code language="bicep" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-sql-analytical-store/main.bicep":::

<a id="create-manual"></a>

## <a name="azure-cosmos-account-with-standard-provisioned-throughput"></a>Azure Cosmos-Konto mit standardmäßig bereitgestelltem Durchsatz

Erstellt ein Azure Cosmos-Konto in zwei Regionen mit Optionen für Konsistenz und Failover, wobei Datenbank und Container für den Standarddurchsatz konfiguriert sind, für den die meisten Richtlinienoptionen aktiviert sind

:::code language="bicep" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-sql/main.bicep":::

<a id="create-sproc"></a>

## <a name="azure-cosmos-db-container-with-server-side-functionality"></a>Azure Cosmos DB-Container mit serverseitiger Funktionalität

Erstellt ein Azure Cosmos-Konto, eine Datenbank und einen Container mit einer gespeicherten Prozedur, einem Trigger und einer benutzerdefinierten Funktion

:::code language="bicep" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-sql-container-sprocs/main.bicep":::

<a id="create-rbac"></a>

## <a name="azure-cosmos-db-account-with-azure-ad-and-rbac"></a>Azure Cosmos DB-Konto mit Azure AD und RBAC

Erstellt ein Azure Cosmos-Konto, eine nativ verwaltete Rollendefinition und eine nativ verwaltete Rollenzuweisung für eine AAD-Identität

:::code language="bicep" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-sql-rbac/main.bicep":::

<a id="free-tier"></a>

## <a name="free-tier-azure-cosmos-db-account"></a>Azure Cosmos DB-Konto im Free-Tarif

Erstellt ein Azure Cosmos-Konto im Free-Tarif und eine Datenbank mit gemeinsam genutztem Durchsatz, der von bis zu 25 Containern verwendet werden kann

:::code language="bicep" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-free/main.bicep":::

## <a name="next-steps"></a>Nächste Schritte

Hier sind einige zusätzlichen Ressourcen:

* [Bicep-Dokumentation](../../azure-resource-manager/bicep/index.yml)
* [Installieren von Bicep-Tools](../../azure-resource-manager/bicep/install.md)
