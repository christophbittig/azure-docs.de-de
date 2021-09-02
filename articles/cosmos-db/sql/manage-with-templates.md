---
title: Erstellen und Verwalten von Azure Cosmos DB mit Resource Manager-Vorlagen
description: Verwenden von Azure Resource Manager-Vorlagen zum Erstellen und Konfigurieren der Core-API (SQL) von Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 06/13/2021
ms.author: mjbrown
ms.openlocfilehash: 0f511f51183e167c4ad42c6c2f5a8425b49bcbb4
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/27/2021
ms.locfileid: "123115279"
---
# <a name="manage-azure-cosmos-db-core-sql-api-resources-with-azure-resource-manager-templates"></a>Verwalten von Ressourcen für die Core-API (SQL) von Azure Cosmos DB mit Azure Resource Manager-Vorlagen

[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

In diesem Artikel erfahren Sie, wie Sie Ihre Azure Cosmos DB-Konten, -Datenbanken und -Container mithilfe von Azure Resource Manager-Vorlagen bereitstellen und verwalten.

Dieser Artikel enthält nur Azure Resource Manager-Vorlagenbeispiele für Core-API-Konten (SQL). Es gibt jedoch auch Vorlagenbeispiele für [Cassandra-](../cassandra/templates-samples.md)-, [Gremlin](../graph/resource-manager-template-samples.md)-, [MongoDB](../mongodb/resource-manager-template-samples.md)- und [Table](../table/resource-manager-templates.md)-APIs.

> [!IMPORTANT]
>
> * Die Länge von Kontonamen ist auf 44 Zeichen (ausschließlich Kleinbuchstaben) beschränkt.
> * Sie müssen die Vorlage mit aktualisierter Angabe der RU/s erneut bereitstellen, um die Durchsatzwerte ändern zu können.
> * Wenn Sie einem Azure Cosmos-Konto Speicherorte hinzufügen oder aus ihm entfernen, können Sie nicht gleichzeitig andere Eigenschaften ändern. Diese Vorgänge müssen separat ausgeführt werden.
> * Azure Cosmos DB-Ressourcen können nicht umbenannt werden, da dies gegen die Arbeitsweise von Azure Resource Manager mit Ressourcen-URIs verstößt.
> * Wenn Sie Durchsatz auf Datenbankebene bereitstellen und für alle Container freigeben möchten, wenden Sie die Durchsatzwerte auf die Eigenschaft „Datenbankoptionen“ an.

Wenn Sie eine der unten aufgeführten Azure Cosmos DB-Ressourcen erstellen möchten, kopieren Sie die folgende Beispielvorlage in eine neue JSON-Datei. Optional können Sie eine JSON-Parameterdatei erstellen, die beim Bereitstellen mehrerer Instanzen derselben Ressource mit unterschiedlichen Namen und Werten verwendet werden soll. Für das Bereitstellen von Azure Resource Manager-Vorlagen stehen Ihnen viele Möglichkeiten zur Verfügung, beispielsweise das [Azure-Portal](../../azure-resource-manager/templates/deploy-portal.md), die [Azure CLI](../../azure-resource-manager/templates/deploy-cli.md), [Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md) und [GitHub](../../azure-resource-manager/templates/deploy-to-azure-button.md).

<a id="create-autoscale"></a>

## <a name="azure-cosmos-account-with-autoscale-throughput"></a>Azure Cosmos-Konto mit automatisch skaliertem Durchsatz

Mit dieser Vorlage wird ein Azure Cosmos-Konto in zwei Regionen mit Optionen für Konsistenz und Failover erstellt, wobei Datenbank und Container für den automatisch skalierten Durchsatz konfiguriert sind, für den die meisten Richtlinienoptionen aktiviert sind. Diese Vorlage ist auch für die Bereitstellung mit nur einem Klick im Katalog der Azure-Schnellstartvorlagen verfügbar.

[:::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Bereitstellen in Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.documentdb%2Fcosmosdb-sql-autoscale%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-sql-autoscale/azuredeploy.json":::

<a id="create-analytical-store"></a>

## <a name="azure-cosmos-account-with-analytical-store"></a>Azure Cosmos-Konto mit Analysespeicher

Mit dieser Vorlage wird ein Azure Cosmos-Konto in einer Region mit einem Container mit aktivierter analytischer Gültigkeitsdauer und Optionen für die manuelle oder automatische Skalierung des Durchsatzes erstellt. Diese Vorlage ist auch für die Bereitstellung mit nur einem Klick im Katalog der Azure-Schnellstartvorlagen verfügbar.

[:::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Bereitstellen in Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.documentdb%2Fcosmosdb-sql-analytical-store%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-sql-analytical-store/azuredeploy.json":::

<a id="create-manual"></a>

## <a name="azure-cosmos-account-with-standard-provisioned-throughput"></a>Azure Cosmos-Konto mit standardmäßig bereitgestelltem Durchsatz

Mit dieser Vorlage wird ein Azure Cosmos-Konto in zwei Regionen mit Optionen für Konsistenz und Failover erstellt, wobei Datenbank und Container für den Standarddurchsatz konfiguriert sind, für den die meisten Richtlinienoptionen aktiviert sind. Diese Vorlage ist auch für die Bereitstellung mit nur einem Klick im Katalog der Azure-Schnellstartvorlagen verfügbar.

[:::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Bereitstellen in Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.documentdb%2Fcosmosdb-sql%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-sql/azuredeploy.json":::

<a id="create-sproc"></a>

## <a name="azure-cosmos-db-container-with-server-side-functionality"></a>Azure Cosmos DB-Container mit serverseitiger Funktionalität

Mit dieser Vorlage werden ein Azure Cosmos-Konto, eine Datenbank und ein Container mit einer gespeicherten Prozedur, einem Trigger und einer benutzerdefinierten Funktion erstellt. Diese Vorlage ist auch für die Bereitstellung mit nur einem Klick im Katalog der Azure-Schnellstartvorlagen verfügbar.

[:::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Bereitstellen in Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.documentdb%2Fcosmosdb-sql-container-sprocs%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-sql-container-sprocs/azuredeploy.json":::

<a id="create-rbac"></a>

## <a name="azure-cosmos-db-account-with-azure-ad-and-rbac"></a>Azure Cosmos DB-Konto mit Azure AD und RBAC

Mit dieser Vorlage wird ein SQL Cosmos-Konto erstellt, eine nativ gepflegte Rollendefinition und eine nativ gepflegte Rollenzuweisung für eine AAD-Identität. Diese Vorlage ist auch für die Bereitstellung mit nur einem Klick im Katalog der Azure-Schnellstartvorlagen verfügbar.

[:::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Bereitstellen in Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.documentdb%2Fcosmosdb-sql-rbac%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-sql-rbac/azuredeploy.json":::

<a id="free-tier"></a>

## <a name="free-tier-azure-cosmos-db-account"></a>Azure Cosmos DB-Konto im Free-Tarif

Mit dieser Vorlage werden ein Azure Cosmos-Konto im Free-Tarif und eine Datenbank mit gemeinsam genutztem Durchsatz erstellt, der von bis zu 25 Containern gemeinsam genutzt werden kann. Diese Vorlage ist auch für die Bereitstellung mit nur einem Klick im Katalog der Azure-Schnellstartvorlagen verfügbar.

[:::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Bereitstellen in Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.documentdb%2Fcosmosdb-free%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-free/azuredeploy.json":::

## <a name="next-steps"></a>Nächste Schritte

Hier sind einige zusätzlichen Ressourcen:

* [Dokumentation zu Azure Resource Manager](../../azure-resource-manager/index.yml)
* [Schema von Azure Cosmos DB-Ressourcenanbietern](/azure/templates/microsoft.documentdb/allversions)
* [Schnellstartvorlagen für Azure Cosmos DB](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Documentdb&pageNumber=1&sort=Popular)
* [Beheben gängiger Azure-Bereitstellungsfehler mit Azure Resource Manager](../../azure-resource-manager/templates/common-deployment-errors.md)