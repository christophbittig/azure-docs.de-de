---
title: Konfigurieren privater Endpunkte für Azure Cosmos DB-Analysespeicher
description: Hier erfahren Sie, wie Sie verwaltete private Endpunkte für Azure Cosmos DB-Analysespeicher einrichten, um den Netzwerkzugriff einzuschränken.
author: AnithaAdusumilli
ms.service: cosmos-db
ms.topic: how-to
ms.date: 03/02/2021
ms.author: anithaa
ms.openlocfilehash: 457182e2f8bf24e911fa2b5e0514b0dba48d6ae0
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/27/2021
ms.locfileid: "123111968"
---
# <a name="configure-azure-private-link-for-azure-cosmos-db-analytical-store"></a>Konfigurieren von Azure Private Link für Azure Cosmos DB-Analysespeicher
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

In diesem Artikel erfahren Sie, wie Sie verwaltete private Endpunkte für Azure Cosmos DB-Analysespeicher einrichten. Wenn Sie den Transaktionsspeicher verwenden, lesen Sie den Artikel [Private Endpunkte für den Transaktionsspeicher](how-to-configure-private-endpoints.md). Mithilfe [verwalteter privater Endpunkte](../synapse-analytics/security/synapse-workspace-managed-private-endpoints.md) können Sie den Netzwerkzugriff von Azure Cosmos DB-Analysespeicher auf das verwaltete virtuelle Netzwerk beschränken, das Ihrem Azure Synapse-Arbeitsbereich zugeordnet ist. Mit verwalteten privaten Endpunkten wird eine private Verbindung mit Ihrem Analysespeicher hergestellt.

> [!NOTE]
> Wenn Sie private DNS-Zonen für Cosmos DB verwenden und einen von Synapse verwalteten privaten Endpunkt für die Unterressource des Analysespeichers erstellen möchten, müssen Sie zunächst eine DNS-Zone für den Analysespeicher (`privatelink.analytics.cosmos.azure.com`) erstellen, der mit Ihrem virtuellen Cosmos DB-Netzwerk verknüpft ist.

## <a name="enable-a-private-endpoint-for-the-analytical-store"></a>Aktivieren eines privaten Endpunkts für den Analysespeicher

### <a name="set-up-azure-synapse-analytics-workspace-with-a-managed-virtual-network"></a>Einrichten eines Azure Synapse Analytics-Arbeitsbereichs mit einem verwalteten virtuellen Netzwerk

[Erstellen Sie einen Arbeitsbereich in Azure Synapse Analytics mit aktivierter Datenexfiltration.](../synapse-analytics/security/how-to-create-a-workspace-with-data-exfiltration-protection.md) Mit dem [Schutz vor Datenexfiltration](../synapse-analytics/security/workspace-data-exfiltration-protection.md) können Sie sicherstellen, dass böswillige Benutzer keine Daten aus Ihren Azure-Ressourcen an Orte außerhalb Ihres Organisationsbereichs kopieren oder übertragen können.

Die folgenden Zugriffsbeschränkungen gelten, wenn der Schutz vor Datenexfiltration für einen Azure Synapse Analytics-Arbeitsbereich aktiviert ist:

* Wenn Sie Azure Spark für Azure Synapse Analytics verwenden, ist nur der Zugriff auf die genehmigten verwalteten privaten Endpunkte für Azure Cosmos DB-Analysespeicher zulässig.

* Wenn Sie serverlose Synapse-SQL-Pools verwenden, können Sie mit Azure Synapse Link jedes Azure Cosmos DB-Konto abfragen. Schreibanforderungen, die [externe Tabellen mit einer CETAS-Anweisung erstellen](../synapse-analytics/sql/develop-tables-cetas.md), sind jedoch nur für die genehmigten verwalteten privaten Endpunkte im virtuellen Arbeitsbereichsnetzwerk zulässig.

> [!NOTE]
> Nachdem der Arbeitsbereich erstellt wurde, können Sie die Konfiguration für das verwaltete virtuelle Netzwerk und die Datenexfiltration nicht mehr ändern.

### <a name="add-a-managed-private-endpoint-for-azure-cosmos-db-analytical-store"></a>Hinzufügen eines verwalteten privaten Endpunkts für Azure Cosmos DB-Analysespeicher

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

1. Navigieren Sie im Azure-Portal zu Ihrem Synapse Analytics-Arbeitsbereich, und öffnen Sie den Bereich **Übersicht**.

1. Starten Sie Synapse Studio, indem Sie zum Bereich **Erste Schritte** navigieren und unter **Synapse Studio öffnen** die Option **Öffnen** auswählen.

1. Öffnen Sie in Synapse Studio die Registerkarte **Verwalten**.

1. Navigieren Sie zu **Verwaltete private Endpunkte**, und wählen Sie **Neu** aus.

   :::image type="content" source="./media/analytical-store-private-endpoints/create-new-private-endpoint.png" alt-text="Erstellen eines neuen privaten Endpunkts für Analysespeicher" border="true":::

1. Wählen Sie den Kontotyp **Azure Cosmos DB (SQL-API)** und dann **Weiter** aus.

   :::image type="content" source="./media/analytical-store-private-endpoints/select-private-endpoint.png" alt-text="Auswählen der Azure Cosmos DB-SQL-API zum Erstellen eines privaten Endpunkts" border="true":::

1. Füllen Sie das Formular **Neuer verwalteter privater Endpunkt** mit den folgenden Details aus:

   * **Name**: Der Name für den verwalteten privaten Endpunkt. Dieser Name kann nach dem Erstellen nicht mehr aktualisiert werden.
   * **Beschreibung**: Geben Sie eine benutzerfreundliche Beschreibung zum Identifizieren des privaten Endpunkts an.
   * **Azure-Abonnement**: Wählen Sie ein Azure Cosmos DB-Konto aus der Liste verfügbarer Konten in Ihren Azure-Abonnements aus.
   * **Azure Cosmos DB-Kontoname**: Wählen Sie ein vorhandenes Azure Cosmos DB-Konto vom Typ SQL oder MongoDB aus.
   * **Untergeordnete Zielressource**: Wählen Sie eine der folgenden Optionen aus: **Analytisch**: Wenn Sie den privaten Endpunkt für Azure Cosmos DB-Analysespeicher hinzufügen möchten.
     **SQL** (oder **MongoDB**): Wenn Sie einen OLTP- oder Transaktionskonto-Endpunkt hinzufügen möchten.

   > [!NOTE]
   > Sie können private Endpunkte sowohl für den Transaktionsspeicher als auch den Analysespeicher dem gleichen Azure Cosmos DB-Konto in einem Azure Synapse Analytics-Arbeitsbereich hinzufügen. Wenn Sie nur analytische Abfragen ausführen möchten, können Sie auch nur den analytischen privaten Endpunkt zuordnen.

   :::image type="content" source="./media/analytical-store-private-endpoints/choose-analytical-private-endpoint.png" alt-text="Auswählen der Option „Analytisch“ für die untergeordnete Zielressource" border="true":::

1. Wechseln Sie nach dem Erstellen zum Namen des privaten Endpunkts, und wählen Sie **Genehmigungen im Azure-Portal verwalten** aus.

1. Navigieren Sie zu Ihrem Azure Cosmos DB-Konto, wählen Sie den privaten Endpunkt aus, und wählen Sie dann **Genehmigen** aus.

1. Navigieren Sie zurück zum Synapse Analytics-Arbeitsbereich, und klicken Sie im Bereich **Verwaltete private Endpunkte** auf **Aktualisieren**. Vergewissern Sie sich, dass der private Endpunkt den Status **Genehmigt** hat.

   :::image type="content" source="./media/analytical-store-private-endpoints/approved-private-endpoint.png" alt-text="Sicherstellen, dass der private Endpunkt genehmigt ist" border="true":::

## <a name="use-apache-spark-for-azure-synapse-analytics"></a>Verwenden von Apache Spark für Azure Synapse Analytics

Wenn Sie einen Azure Synapse-Arbeitsbereich mit aktiviertem Schutz vor Datenexfiltration erstellt haben, wird der ausgehende Zugriff von Synapse Spark auf Azure Cosmos DB-Konten standardmäßig blockiert. Wenn Azure Cosmos DB bereits über einen vorhandenen privaten Endpunkt verfügt, ist der Zugriff darauf für Synapse Spark ebenfalls blockiert.

Zum Genehmigen des Zugriffs auf Azure Cosmos DB-Daten führen Sie die folgenden Schritte aus:

* Wenn Sie Azure Synapse Link zum Abfragen von Azure Cosmos DB-Daten verwenden, fügen Sie einen verwalteten **analytischen** privaten Endpunkt für das Azure Cosmos DB-Konto hinzu.

* Wenn Sie Schreib-/Lesevorgänge in Batches und/oder Schreib-/Lesevorgänge per Streaming in den Transaktionsspeicher verwenden, fügen Sie einen verwalteten privaten *SQL*- oder *MongoDB*-Endpunkt für das Azure Cosmos DB-Konto hinzu. Außerdem sollten Sie *connectionMode* auf *Gateway* festlegen, wie im folgenden Codeausschnitt gezeigt:

  ```python
  # Write a Spark DataFrame into an Azure Cosmos DB container
  # To select a preferred lis of regions in a multi-region account, add .option("spark.cosmos.preferredRegions", "<Region1>, <Region2>")
  
  YOURDATAFRAME.write\
    .format("cosmos.oltp")\
    .option("spark.synapse.linkedService", "<your-Cosmos-DB-linked-service-name>")\
    .option("spark.cosmos.container","<your-Cosmos-DB-container-name>")\
    .option("spark.cosmos.write.upsertEnabled", "true")\
    .option("spark.cosmos.connection.mode", "Gateway")\
    .mode('append')\
    .save()
  
  ```

## <a name="using-synapse-serverless-sql-pools"></a>Verwenden serverloser Synapse-SQL-Pools

Serverlose Synapse-SQL-Pools nutzen Funktionen für mehrere Mandanten, die im verwalteten virtuellen Netzwerk nicht bereitgestellt werden. Wenn das Azure Cosmos DB-Konto über einen vorhandenen privaten Endpunkt verfügt, wird aufgrund von Überprüfungen der Netzwerkisolation für das Azure Cosmos DB-Konto der Zugriff vom serverlosen Synapse-SQL-Pool auf das Konto blockiert.

Zum Konfigurieren der Netzwerkisolation für dieses Konto über einen Synapse-Arbeitsbereich führen Sie die folgenden Schritte aus:

1. Gestatten Sie dem Synapse-Arbeitsbereich den Zugriff auf das Azure Cosmos DB-Konto, indem Sie die Einstellung `NetworkAclBypassResourceId` für das Konto angeben.

   **Mithilfe von PowerShell**

   ```powershell-interactive
   Update-AzCosmosDBAccount -Name MyCosmosDBDatabaseAccount -ResourceGroupName MyResourceGroup -NetworkAclBypass AzureServices -NetworkAclBypassResourceId "/subscriptions/subId/resourceGroups/rgName/providers/Microsoft.Synapse/workspaces/wsName"
   ```

   **Mithilfe der Azure CLI**

   ```azurecli-interactive
   az cosmosdb update --name MyCosmosDBDatabaseAccount --resource-group MyResourceGroup --network-acl-bypass AzureServices --network-acl-bypass-resource-ids "/subscriptions/subId/resourceGroups/rgName/providers/Microsoft.Synapse/workspaces/wsName"
   ```

   > [!NOTE]
   > Das Azure Cosmos DB-Konto und der Azure Synapse Analytics-Arbeitsbereich sollten zum selben Azure Active Directory-Mandanten (Azure AD) gehören.

2. Sie können jetzt von serverlosen SQL-Pools auf das Konto zugreifen, indem Sie T-SQL-Abfragen über Azure Synapse Link verwenden. Um jedoch Netzwerkisolation für die Daten im Analysespeicher sicherzustellen, müssen Sie für dieses Konto einen **analytischen** verwalteten privaten Endpunkt hinzufügen. Andernfalls werden die Daten im Analysespeicher nicht für den öffentlichen Zugriff blockiert.

> [!IMPORTANT]
> Wenn Sie Azure Synapse Link verwenden und Netzwerkisolation für Ihre Daten im Analysespeicher benötigen, müssen Sie das Azure Cosmos DB-Konto dem Synapse-Arbeitsbereich mithilfe eines **analytischen** verwalteten privaten Endpunkts zuordnen.

## <a name="next-steps"></a>Nächste Schritte

* Erste Schritte beim [Abfragen des Analysespeichers mit Azure Synapse Spark 3](../synapse-analytics/synapse-link/how-to-query-analytical-store-spark-3.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json)
* Erste Schritte beim [Interagieren mit Azure Cosmos DB mithilfe von Apache Spark 2 in Azure Synapse Link](../synapse-analytics/synapse-link/how-to-query-analytical-store-spark.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json)
* Erste Schritte zum [Abfragen des Analysespeichers mit serverlosen Azure Synapse-SQL-Pools](../synapse-analytics/sql/query-cosmos-db-analytical-store.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json)
