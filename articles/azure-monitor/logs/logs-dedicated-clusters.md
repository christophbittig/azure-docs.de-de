---
title: Dedizierte Azure Monitor-Protokollcluster
description: Kunden, die den Mindestabnahmetarif erfüllen, können dedizierte Cluster verwenden.
ms.topic: conceptual
author: yossi-y
ms.author: yossiy
ms.date: 07/29/2021
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 6f798cb4bc1511c0656c697f04e2d1763ca54321
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132308596"
---
# <a name="azure-monitor-logs-dedicated-clusters"></a>Dedizierte Azure Monitor-Protokollcluster

Dedizierte Azure Monitor-Protokollcluster sind eine Bereitstellungsoption, die erweiterte Funktionen für Kunden von Azure Monitor-Protokollen bietet. Kunden können auswählen, welche ihrer Log Analytics-Arbeitsbereiche in dedizierten Clustern gehostet werden sollen.

Bei dedizierten Clustern müssen sich Kunden verpflichten, mindestens 500 GB Daten pro Tag zu erfassen. Sie können einen vorhandenen Arbeitsbereich ohne Datenverlust oder Dienstunterbrechung zu einem dedizierten Cluster migrieren. 

Folgende Funktionen erfordern dedizierte Cluster:

- **[Kundenseitig verwaltete Schlüssel](../logs/customer-managed-keys.md)** : Die Clusterdaten werden mithilfe von Schlüsseln verschlüsselt, die vom Kunden bereitgestellt und gesteuert werden.
- **[Lockbox](../logs/customer-managed-keys.md#customer-lockbox-preview)** : Steuerung von Zugriffsanforderungen von Mitarbeitern des Microsoft-Supports für Ihre Daten.
- **[Doppelte Verschlüsselung](../../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption)** : schützt vor dem Szenario, dass einer der Verschlüsselungsalgorithmen oder Schlüssel kompromittiert wurde. In diesem Fall werden die Daten weiterhin durch die zusätzliche Verschlüsselungsebene geschützt.
- **[Verfügbarkeitszonen](../../availability-zones/az-overview.md)** : Schützen Sie Ihre Daten vor Rechenzentrumsausfällen, indem Zonen physisch durch Standorte getrennt und mit unabhängiger Stromversorgung, Kühlung und Vernetzung ausgestattet sind. Durch die physische Aufteilung in Zonen und die unabhängige Infrastruktur ist ein Incident wesentlich weniger wahrscheinlich, da der Arbeitsbereich auf die Ressourcen aus beliebigen Zonen zurückgreifen kann. Dedizierte Cluster werden mit Verfügbarkeitszonen erstellt, die für Datenresilienz in den Regionen aktiviert sind, in denen [Azure Verfügbarkeitszonen aufweist](../../availability-zones/az-overview.md#azure-regions-with-availability-zones). Die Konfiguration von Verfügbarkeitszonen im Cluster kann nach dem Erstellen nicht mehr geändert werden, und die Einstellungen können in der Eigenschaft `isAvailabilityZonesEnabled` des Clusters überprüft werden. [Azure Monitor-Verfügbarkeitszonen](./availability-zones.md) decken einen größeren Teil des Diensts ab und erweitern die Azure Monitor-Resilienz automatisch, wenn sie in Ihrer Region verfügbar sind.
- **[Mehrere Arbeitsbereiche](../logs/cross-workspace-query.md)** : Wenn ein Kunde mehr als einen Arbeitsbereich für die Produktion verwendet, ist es möglicherweise sinnvoll, einen dedizierten Cluster zu verwenden. Arbeitsbereichsübergreifende Abfragen werden schneller ausgeführt, wenn sich alle Arbeitsbereiche im selben Cluster befinden. Möglicherweise ist die Verwendung eines dedizierten Clusters auch kostengünstiger, da die zugewiesenen Mindestabnahmenstufen die gesamte Clustererfassung berücksichtigen und auf alle Arbeitsbereiche angewendet werden, auch wenn einige davon klein sind und nicht für den Mindestabnahmerabatt berechtigt sind.


## <a name="management"></a>Verwaltung 

Dedizierte Cluster werden mit einer Azure-Ressource verwaltet, die Azure Monitor-Protokollcluster darstellt. Vorgänge werden programmgesteuert mit der [CLI](/cli/azure/monitor/log-analytics/cluster), mit [PowerShell](/powershell/module/az.operationalinsights) oder [REST](/rest/api/loganalytics/clusters) ausgeführt.

Nachdem ein Cluster erstellt wurde, können Arbeitsbereiche mit ihm verknüpft werden, und in ihnen neu erfasste Daten werden im Cluster gespeichert. Die Verknüpfung von Arbeitsbereichen mit einem Cluster kann jederzeit aufgehoben werden, und neue Daten werden in freigegebenen Log Analytics-Clustern gespeichert. Der Vorgang zum Verknüpfen und Aufheben der Verknüpfung wirkt sich nicht auf Ihre Abfragen und den Zugriff auf Daten vor und nach dem Vorgang mit Aufbewahrung in Arbeitsbereichen aus. Der Cluster und die Arbeitsbereiche müssen sich in derselben Region befinden, um Verknüpfungen zu ermöglichen.

Für alle Vorgänge auf Clusterebene ist die Aktionsberechtigung `Microsoft.OperationalInsights/clusters/write` für den Cluster erforderlich. Diese Berechtigung kann über den Besitzer oder einen Mitwirkenden erteilt werden, der die Aktion `*/write` enthält, oder über die Rolle „Analytics-Mitwirkender“, die die Aktion `Microsoft.OperationalInsights/*` enthält. Weitere Informationen zu den Log Analytics-Berechtigungen finden Sie unter [Verwalten des Zugriffs auf Protokolldaten und Arbeitsbereiche in Azure Monitor](./manage-access.md). 


## <a name="cluster-pricing-model"></a>Preismodell für Cluster

Dedizierte Log Analytics-Cluster verwenden ein Preismodell mit einer Mindestabnahme (ehemals als „Kapazitätsreservierung“ bezeichnet) von mindestens 500 GB/Tag. Jede den Tarif überschreitende Nutzung wird mit dem effektiven Prozentsatz pro GB dieser Mindestabnahmestufe abgerechnet. Mindestabnahme-Tarifinformationen finden Sie auf der Seite [Azure Monitor – Preise]( https://azure.microsoft.com/pricing/details/monitor/).  

Die Mindestabnahme für Cluster wird programmgesteuert mit Azure Resource Manager anhand des Parameters `Capacity` unter `Sku` konfiguriert. `Capacity` wird in GB-Einheiten angegeben und kann Werte von 500, 1000, 2000 oder 5000 GB/Tag aufweisen.

Für die Abrechnung des Verbrauchs in einem Cluster stehen zwei Modi zur Verfügung. Diese können beim Konfigurieren Ihres Clusters mithilfe des Parameters `billingType` festgelegt werden. 

1. **Cluster (Standardeinstellung)** : Erfasste Daten werden auf Clusterebene abgerechnet. Die erfassten Datenmengen aus den einzelnen Arbeitsbereichen, die einem Cluster zugeordnet sind, werden aggregiert, um die tägliche Abrechnung für den Cluster zu berechnen. 

2. **Arbeitsbereiche**: Die Commitment Tier-Kosten für Ihren Cluster werden anteilig auf die Arbeitsbereiche im Cluster umgelegt, und zwar nach dem Datenerfassungsvolumen jedes Arbeitsbereichs (nach Berücksichtigung der Zuweisungen pro Knoten von [Microsoft Defender für Cloud](../../security-center/index.yml) für jeden Arbeitsbereich). Die vollständigen Details dieses Preismodells werden [hier](./manage-cost-storage.md#log-analytics-dedicated-clusters) erläutert. 

Wenn Ihr Arbeitsbereich den alten Tarif auf Knotenbasis verwendet, wird er, wenn er mit einem Cluster verknüpft ist, nicht mehr pro Knoten abgerechnet, sondern mit den erfassten Daten der Mindestabnahmestufe des Clusters verrechnet. Datenzuordnungen pro Knoten von Microsoft Defender für Cloud werden weiterhin angewendet.

Vollständige Informationen zur Abrechnung für dedizierte Log Analytics-Cluster finden Sie [hier](./manage-cost-storage.md#log-analytics-dedicated-clusters).

## <a name="create-a-dedicated-cluster"></a>Erstellen eines dedizierten Clusters

Sie müssen die folgenden Eigenschaften angeben, wenn Sie einen neuen dedizierten Cluster erstellen:

- **ClusterName**
- **ResourceGroupName**: Sie sollten eine zentrale IT-Ressourcengruppe verwenden, da Cluster in der Regel von vielen Teams in der Organisation gemeinsam genutzt werden. Weitere Entwurfsaspekte finden Sie unter [Entwerfen Ihrer Azure Monitor-Protokollbereitstellung](../logs/design-logs-deployment.md).
- **Location**
- **SkuCapacity**: Die Mindestabnahme (ehemals als „Kapazitätsreservierungen“ bezeichnet) kann auf 500, 1.000, 2.000 oder 5.000 GB/Tag festgelegt werden. Weitere Informationen zu den Clusterkosten finden Sie unter [Verwalten von Kosten für Log Analytics-Cluster](./manage-cost-storage.md#log-analytics-dedicated-clusters). 

Das Benutzerkonto, mit dem die Cluster erstellt werden, muss über die Standardberechtigung für die Erstellung von Azure-Ressourcen verfügen: `Microsoft.Resources/deployments/*` und die Clusterschreibberechtigung `Microsoft.OperationalInsights/clusters/write`. Dazu muss in seinen Rollenzuweisungen diese spezifische Aktion, `Microsoft.OperationalInsights/*` oder `*/write` angegeben sein.

Nachdem Sie die Clusterressource erstellt haben, können Sie weitere Eigenschaften wie *sku*, *keyVaultProperties oder *billingType* bearbeiten. Weitere Einzelheiten finden Sie unten.

Pro Abonnement und Region können bis zu zwei aktive Cluster vorhanden sein. Wenn der Cluster gelöscht wird, ist er weiterhin für 14 Tage reserviert. Pro Abonnement und Region können bis zu vier reservierte Cluster (aktiv oder kürzlich gelöscht) vorhanden sein.

> [!NOTE]
> Die Clustererstellung löst die Ressourcenzuordnung und -bereitstellung aus. Dieser Vorgang kann einige Stunden dauern.
> Dedizierte Cluster werden abgerechnet, sobald sie unabhängig von der Datenerfassung bereitgestellt werden. Es wird empfohlen, die Bereitstellung vorzubereiten, um die Bereitstellung und die Verknüpfung der Arbeitsbereiche mit dem Cluster zu beschleunigen. Überprüfen Sie Folgendes:
> - Eine Liste der ersten Arbeitsbereiche, die mit dem Cluster verknüpft werden sollen, wurde identifiziert.
> - Sie verfügen über Berechtigungen für das Abonnement, das für den Cluster und alle zu verknüpfenden Arbeitsbereiche vorgesehen ist.

**BEFEHLSZEILENSCHNITTSTELLE (CLI)**
```azurecli
az account set --subscription "cluster-subscription-id"

az monitor log-analytics cluster create --no-wait --resource-group "resource-group-name" --name "cluster-name" --location "region-name" --sku-capacity "daily-ingestion-gigabyte"

# Wait for job completion when `--no-wait` was used
$clusterResourceId = az monitor log-analytics cluster list --resource-group "resource-group-name" --query "[?contains(name, "cluster-name")].[id]" --output tsv
az resource wait --created --ids $clusterResourceId --include-response-body true
```

**PowerShell**

```powershell
Select-AzSubscription "cluster-subscription-id"

New-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name" -Location "region-name" -SkuCapacity "daily-ingestion-gigabyte" -AsJob

# Check when the job is done when `-AsJob` was used
Get-Job -Command "New-AzOperationalInsightsCluster*" | Format-List -Property *
```

**REST-API**

*Call* 

```rest
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2021-06-01
Authorization: Bearer <token>
Content-type: application/json

{
  "identity": {
    "type": "systemAssigned"
    },
  "sku": {
    "name": "capacityReservation",
    "Capacity": 500
    },
  "properties": {
    "billingType": "Cluster",
    },
  "location": "<region>",
}
```

*Antwort*

202 (Akzeptiert) und ein Header

### <a name="check-cluster-provisioning-status"></a>Überprüfen des Bereitstellungsstatus für den Cluster

Es dauert eine Weile, bis die Bereitstellung des Log Analytics-Clusters abgeschlossen ist. Verwenden Sie eine der folgenden Methoden, um die Eigenschaft *ProvisioningState* zu überprüfen: Während der Bereitstellung lautet dieser Wert *ProvisioningAccount*, und nach Abschluss des Vorgangs lautet er *Succeeded*.

**BEFEHLSZEILENSCHNITTSTELLE (CLI)**

```azurecli
az account set --subscription "cluster-subscription-id"

az monitor log-analytics cluster show --resource-group "resource-group-name" --name "cluster-name"
```

**PowerShell**

```powershell
Select-AzSubscription "cluster-subscription-id"

Get-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name"
```
 
**REST-API**

Senden Sie eine GET-Anforderung für die Clusterressource und überprüfen Sie den Wert für *provisioningState*. Während der Bereitstellung lautet dieser Wert *ProvisioningAccount*, und nach Abschluss des Vorgangs lautet er *Succeeded*.

  ```rest
  GET https://management.azure.com/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name?api-version=2021-06-01
  Authorization: Bearer <token>
  ```

  **Antwort**

  ```json
  {
    "identity": {
      "type": "SystemAssigned",
      "tenantId": "tenant-id",
      "principalId": "principal-id"
    },
    "sku": {
      "name": "capacityreservation",
      "capacity": 500
    },
    "properties": {
      "provisioningState": "ProvisioningAccount",
      "clusterId": "cluster-id",
      "billingType": "Cluster",
      "lastModifiedDate": "last-modified-date",
      "createdDate": "created-date",
      "isDoubleEncryptionEnabled": false,
      "isAvailabilityZonesEnabled": false,
      "capacityReservationProperties": {
        "lastSkuUpdate": "last-sku-modified-date",
        "minCapacity": 500
      }
    },
    "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
    "name": "cluster-name",
    "type": "Microsoft.OperationalInsights/clusters",
    "location": "cluster-region"
  }
  ```

Die GUID *principalId* wird bei der Clustererstellung vom verwalteten Identitätsdienst generiert.

---


## <a name="link-a-workspace-to-a-cluster"></a>Verknüpfen eines Arbeitsbereichs mit einem Cluster

Wenn ein Log Analytics-Arbeitsbereich mit einem dedizierten Cluster verknüpft ist, werden neue Daten, die im Arbeitsbereich erfasst werden, an den neuen Cluster weitergeleitet, während vorhandene Daten im vorhandenen Cluster verbleiben. Wenn der dedizierte Cluster mithilfe von kundenseitig verwalteten Schlüsseln (Customer-managed keys, CMK) verschlüsselt wird, werden nur neue Daten mit dem Schlüssel verschlüsselt. Das System abstrahiert diesen Unterschied, sodass Sie den Arbeitsbereich wie gewohnt abfragen können, während das System clusterübergreifende Abfragen im Hintergrund ausführt.

Ein Cluster kann mit bis zu 1.000 Arbeitsbereichen verknüpft werden. Verknüpfte Arbeitsbereiche befinden sich in derselben Region wie der Cluster. Um das System-Back-End zu schützen und die Fragmentierung von Daten zu vermeiden, kann ein Arbeitsbereich nicht mehr als zweimal pro Monat mit einem Cluster verknüpft werden.

Um den Verknüpfungsvorgang auszuführen, benötigen Sie Schreibberechtigungen für den Arbeitsbereich und die Clusterressource:

- Im Arbeitsbereich: *Microsoft.OperationalInsights/workspaces/write*
- In der Clusterressource: *Microsoft.OperationalInsights/clusters/write*

Zusätzlich zu den Abrechnungsaspekten behält der verknüpfte Arbeitsbereich seine eigenen Einstellungen wie etwa die Länge der Datenaufbewahrung bei.

Der Arbeitsbereich und der Cluster können sich in verschiedenen Abonnements befinden. Es ist möglich, dass sich der Arbeitsbereich und der Cluster in unterschiedlichen Mandanten befinden, wenn Azure Lighthouse verwendet wird, um beide einem einzelnen Mandanten zuzuordnen.

Das Verknüpfen eines Arbeitsbereichs kann nur nach Abschluss der Log Analytics-Clusterbereitstellung ausgeführt werden.

> [!WARNING]
> Das Verknüpfen eines Arbeitsbereichs mit einem Cluster erfordert es, mehrere Back-End-Komponenten zu synchronisieren und die Cachehydration zu gewährleisten. Da dieser Vorgang bis zu zwei Stunden dauern kann, sollten Sie ihn asynchron ausführen.

Verwenden Sie die folgenden Befehle, um einen Arbeitsbereich mit einem Cluster zu verbinden.

**BEFEHLSZEILENSCHNITTSTELLE (CLI)**
```azurecli
# Find cluster resource ID
az account set --subscription "cluster-subscription-id"
$clusterResourceId = az monitor log-analytics cluster list --resource-group "resource-group-name" --query "[?contains(name, "cluster-name")].[id]" --output tsv

# Link workspace
az account set --subscription "workspace-subscription-id"
az monitor log-analytics workspace linked-service create --no-wait --name cluster --resource-group "resource-group-name" --workspace-name "workspace-name" --write-access-resource-id $clusterResourceId

# Wait for job completion when `--no-wait` was used
$workspaceResourceId = az monitor log-analytics workspace list --resource-group "resource-group-name" --query "[?contains(name, "workspace-name")].[id]" --output tsv
az resource wait --deleted --ids $workspaceResourceId --include-response-body true
```

**PowerShell**

```powershell
Select-AzSubscription "cluster-subscription-id"

# Find cluster resource ID
$clusterResourceId = (Get-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name").id

Select-AzSubscription "workspace-subscription-id"

# Link the workspace to the cluster
Set-AzOperationalInsightsLinkedService -ResourceGroupName "resource-group-name" -WorkspaceName "workspace-name" -LinkedServiceName cluster -WriteAccessResourceId $clusterResourceId -AsJob

# Check when the job is done
Get-Job -Command "Set-AzOperationalInsightsLinkedService" | Format-List -Property *
```

**REST-API**

Verwenden Sie den folgenden REST-Aufruf zum Verknüpfen eines Clusters:

*Senden*

```rest
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>/linkedservices/cluster?api-version=2021-06-01 
Authorization: Bearer <token>
Content-type: application/json

{
  "properties": {
    "WriteAccessResourceId": "/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/clusters/<cluster-name>"
    }
}
```

*Antwort*

202 (Akzeptiert) und Header

---

### <a name="check-workspace-link-status"></a>Überprüfen des Verknüpfungsstatus des Arbeitsbereichs
  
Wenn ein Cluster mit kundenverwalteten Schlüsseln konfiguriert ist, werden Daten, die nach Abschluss des Verknüpfungsvorgangs in den Arbeitsbereichen erfasst wurden, verschlüsselt mit Ihrem verwalteten Schlüssel gespeichert. Der Vorgang zum Verknüpfen des Arbeitsbereichs kann bis zu 90 Minuten dauern. Sie können den Status überprüfen, indem Sie eine Get-Anforderung an den Arbeitsbereich senden und beobachten, ob die Eigenschaft *clusterResourceId* in der Antwort unter *Features* vorhanden ist.

**BEFEHLSZEILENSCHNITTSTELLE (CLI)**
```azurecli
az account set --subscription "workspace-subscription-id"

az monitor log-analytics workspace show --resource-group "resource-group-name" --workspace-name "workspace-name"
```

**PowerShell**

```powershell
Select-AzSubscription "workspace-subscription-id"

Get-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name" -Name "workspace-name"
```

**REST-API**

*Call*

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>?api-version=2021-06-01
Authorization: Bearer <token>
```

*Antwort*

```json
{
  "properties": {
    "source": "Azure",
    "customerId": "workspace-name",
    "provisioningState": "Succeeded",
    "sku": {
      "name": "pricing-tier-name",
      "lastSkuUpdate": "Tue, 28 Jan 2020 12:26:30 GMT"
    },
    "retentionInDays": 31,
    "features": {
      "legacy": 0,
      "searchVersion": 1,
      "enableLogAccessUsingOnlyResourcePermissions": true,
      "clusterResourceId": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name"
    },
    "workspaceCapping": {
      "dailyQuotaGb": -1.0,
      "quotaNextResetTime": "Tue, 28 Jan 2020 14:00:00 GMT",
      "dataIngestionStatus": "RespectQuota"
    }
  },
  "id": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/microsoft.operationalinsights/workspaces/workspace-name",
  "name": "workspace-name",
  "type": "Microsoft.OperationalInsights/workspaces",
  "location": "region"
}
```

---


## <a name="change-cluster-properties"></a>Ändern der Clustereigenschaften

Nachdem Sie Ihre Clusterressource erstellt und vollständig bereitgestellt haben, können Sie zusätzliche Eigenschaften mit der CLI, mit PowerShell oder mit der REST-API bearbeiten. Die zusätzlichen Eigenschaften, die nach der Bereitstellung des Clusters festgelegt werden können, umfassen Folgendes:

- **keyVaultProperties**: Enthält den Schlüssel in Azure Key Vault mit den folgenden Parametern: *KeyVaultUri*, *KeyName*, *KeyVersion*. Weitere Informationen finden Sie unter [Aktualisieren des Clusters mit Schlüsselbezeichnerdetails](../logs/customer-managed-keys.md#update-cluster-with-key-identifier-details).
- **Identität**: Die für die Authentifizierung bei Key Vault verwendete Identität. Diese kann vom System oder vom Benutzer zugewiesen sein.
- **billingType**: Abrechnungszuordnung für die Clusterressource und deren Daten. Enthält für die folgenden Werte:
  - **Cluster (Standard)** : Die Kosten für Ihren Cluster werden der Clusterressource zugeordnet.
  - **Arbeitsbereiche**: Die Kosten für Ihren Cluster werden proportional den Arbeitsbereichen im Cluster zugeordnet. Wenn die Gesamtmenge der erfassten Daten unter der Mindestabnahme liegt, wird ein Teil des Verbrauchs über die Clusterressource abgerechnet. Weitere Informationen zum Clusterpreismodell finden Sie unter [Dedizierte Log Analytics-Cluster](./manage-cost-storage.md#log-analytics-dedicated-clusters).


>[!IMPORTANT]
>Das Clusterupdate sollte nicht sowohl Identitäts- als auch Schlüsselbezeichnerdetails in demselben Vorgang enthalten. Wenn Sie beides aktualisieren müssen, sollte das Update in zwei aufeinanderfolgenden Vorgängen erfolgen.

> [!NOTE]
> Die Eigenschaft *billingType* wird in PowerShell nicht unterstützt.

## <a name="get-all-clusters-in-resource-group"></a>Abrufen aller Cluster in einer Ressourcengruppe

**BEFEHLSZEILENSCHNITTSTELLE (CLI)**

```azurecli
az account set --subscription "cluster-subscription-id"

az monitor log-analytics cluster list --resource-group "resource-group-name"
```

**PowerShell**

```powershell
Select-AzSubscription "cluster-subscription-id"

Get-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name"
```

**REST-API**

*Call*

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters?api-version=2021-06-01
Authorization: Bearer <token>
```

*Antwort*
  
```json
{
  "value": [
    {
      "identity": {
        "type": "SystemAssigned",
        "tenantId": "tenant-id",
        "principalId": "principal-id"
      },
      "sku": {
        "name": "capacityreservation",
        "capacity": 500
      },
      "properties": {
        "provisioningState": "Succeeded",
        "clusterId": "cluster-id",
        "billingType": "Cluster",
        "lastModifiedDate": "last-modified-date",
        "createdDate": "created-date",
        "isDoubleEncryptionEnabled": false,
        "isAvailabilityZonesEnabled": false,
        "capacityReservationProperties": {
          "lastSkuUpdate": "last-sku-modified-date",
          "minCapacity": 500
        }
      },
      "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
      "name": "cluster-name",
      "type": "Microsoft.OperationalInsights/clusters",
      "location": "cluster-region"
    }
  ]
}
```

---



## <a name="get-all-clusters-in-subscription"></a>Abrufen aller Cluster in einem Abonnement

**BEFEHLSZEILENSCHNITTSTELLE (CLI)**

```azurecli
az account set --subscription "cluster-subscription-id"

az monitor log-analytics cluster list
```

**PowerShell**

```powershell
Select-AzSubscription "cluster-subscription-id"

Get-AzOperationalInsightsCluster
```
**REST-API**

*Call*

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/providers/Microsoft.OperationalInsights/clusters?api-version=2021-06-01
Authorization: Bearer <token>
```
    
*Antwort*
    
Dasselbe wie bei „Cluster in einer Ressourcengruppe“, jedoch im Abonnementbereich.

---


## <a name="update-commitment-tier-in-cluster"></a>Aktualisieren der Mindestabnahmestufe im Cluster

Wenn sich das Datenvolumen Ihrer verknüpften Arbeitsbereiche im Laufe der Zeit ändert und Sie die Mindestabnahme entsprechend aktualisieren möchten. Die Stufe wird in GB-Einheiten angegeben und kann Werte von 500, 1000, 2000 oder 5000 GB/Tag haben. Beachten Sie, dass Sie nicht den vollständigen REST-Anforderungstext angeben müssen, aber die SKU einschließen sollten.

**BEFEHLSZEILENSCHNITTSTELLE (CLI)**

```azurecli
az account set --subscription "cluster-subscription-id"

az monitor log-analytics cluster update --resource-group "resource-group-name" --name "cluster-name"  --sku-capacity 500
```

### <a name="powershell"></a>PowerShell

```powershell
Select-AzSubscription "cluster-subscription-id"

Update-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name" -SkuCapacity 500
```

### <a name="rest-api"></a>REST-API

*Call*

```rest
PATCH https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2021-06-01
Authorization: Bearer <token>
Content-type: application/json

{
  "sku": {
    "name": "capacityReservation",
    "Capacity": 2000
  }
}
```

---


### <a name="update-billingtype-in-cluster"></a>Aktualisieren von billingType im Cluster

Die Eigenschaft *billingType* bestimmt die Abrechnungszuordnung für den Cluster und dessen Daten:
- *Cluster* (Standard): Die Abrechnung wird der Clusterressource zugeordnet.
- *Arbeitsbereiche*: Die Abrechnung wird den verknüpften Arbeitsbereichen proportional zugeordnet. Wenn das Datenvolumen aus allen Arbeitsbereichen unterhalb der Mindestabnahmeebene liegt, wird das verbleibende Volume dem Cluster zugeordnet.

**REST**

*Call*

```rest
PATCH https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2021-06-01
Authorization: Bearer <token>
Content-type: application/json

{
  "properties": {
    "billingType": "Workspaces",
    }  
}
```

### <a name="unlink-a-workspace-from-cluster"></a>Aufheben der Verknüpfung eines Arbeitsbereichs mit einem Cluster

Sie können die Verknüpfung eines Arbeitsbereichs mit einem Cluster aufheben. Nach dem Aufheben der Verknüpfung eines Arbeitsbereichs mit dem Cluster werden neue Daten, die diesem Arbeitsbereich zugeordnet sind, nicht an den dedizierten Cluster gesendet. Außerdem erfolgt die Abrechnung des Arbeitsbereichs nicht mehr über den Cluster. Alte Daten des nicht verknüpften Arbeitsbereichs können im Cluster verbleiben. Wenn diese Daten mithilfe von kundenseitig verwalteten Schlüsseln (Customer-managed keys, CMK) verschlüsselt werden, werden die Key Vault-Geheimnisse beibehalten. Das System abstrahiert diese Änderung von Log Analytics-Benutzern. Benutzer können den Arbeitsbereich einfach wie gewohnt abfragen. Das System führt bei Bedarf clusterübergreifende Abfragen für das Back-End ohne einen Hinweis auf die Benutzer durch.  

> [!WARNING] 
> Es gibt eine Begrenzung von zwei Verknüpfungsvorgängen für einen bestimmten Arbeitsbereich innerhalb eines Monats. Berücksichtigen und planen Sie das Aufheben der Verknüpfung von Aktionen gründlich.

Verwenden Sie den folgenden Befehl zum Aufheben der Verknüpfung eines Arbeitsbereichs mit einem Cluster:

**BEFEHLSZEILENSCHNITTSTELLE (CLI)**

```azurecli
az account set --subscription "workspace-subscription-id"

az monitor log-analytics workspace linked-service delete --resource-group "resource-group-name" --workspace-name "workspace-name" --name cluster
```

**PowerShell**

```powershell
Select-AzSubscription "workspace-subscription-id"

# Unlink a workspace from cluster
Remove-AzOperationalInsightsLinkedService -ResourceGroupName "resource-group-name" -WorkspaceName {workspace-name} -LinkedServiceName cluster
```

---


## <a name="delete-cluster"></a>Löschen von Clustern

Es wird empfohlen, die Verknüpfung aller Arbeitsbereiche mit einem dedizierten Cluster aufzuheben, bevor Sie ihn löschen. Sie benötigen *Schreibberechtigungen* für die Clusterressource. Wenn Sie einen Cluster löschen, verlieren Sie den Zugriff auf alle Daten, die aus zurzeit und früher verknüpften Arbeitsbereichen im Cluster erfasst wurden. Dieser Vorgang ist nicht umkehrbar. Wenn Sie Ihren Cluster löschen, solange Arbeitsbereiche verknüpft sind, werden diese automatisch getrennt, und es werden stattdessen neue Daten im Log Analytics-Speicher erfasst.

Eine Clusterressource, die in den letzten 14 Tagen gelöscht wurde, verbleibt im vorläufig gelöschten Zustand, und ihr Name bleibt reserviert. Nach Ablauf des Zeitraums für vorläufiges Löschen wird der Cluster endgültig gelöscht, und sein Name kann wieder verwendet werden.

> [!WARNING] 
> - Die Wiederherstellung vorläufig gelöschter Cluster wird nicht unterstützt, und der Cluster kann nach dem Löschen nicht wiederhergestellt werden.
> - Es gibt eine Begrenzung von vier Clustern pro Abonnement. Dazu zählen sowohl aktive als auch vorläufig gelöschte Cluster. Kunden sollten keine wiederkehrenden Prozeduren erstellen, mit denen Cluster erstellt und gelöscht werden. Dies hat erhebliche Auswirkungen auf Log Analytics-Back-End-Systeme.

Mit den folgenden Befehlen können Sie ein Cluster löschen:

**BEFEHLSZEILENSCHNITTSTELLE (CLI)**
```azurecli
az account set --subscription "cluster-subscription-id"

az monitor log-analytics cluster delete --resource-group "resource-group-name" --name $clusterName
```

**PowerShell**

```powershell
Select-AzSubscription "cluster-subscription-id"

Remove-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name"
```

**REST-API**

Verwenden Sie den folgenden REST-Aufruf zum Löschen eines Clusters:

```rest
DELETE https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2021-06-01
Authorization: Bearer <token>
```

  **Antwort**

  200 – OK

---



## <a name="limits-and-constraints"></a>Grenzen und Einschränkungen

- In jeder Region und jedem Abonnement können maximal zwei aktive Cluster erstellt werden.

- In jeder Region und jedem Abonnement können maximal vier reservierte Cluster erstellt werden (aktiv oder kürzlich gelöscht).

- Maximal 1.000 Log Analytics-Arbeitsbereiche können mit einem Cluster verknüpft werden.

- Maximal zwei Vorgänge zur Verknüpfung von Arbeitsbereichen sind innerhalb von 30 Tagen für einen bestimmten Arbeitsbereich zulässig.

- Das Verschieben eines Clusters in eine andere Ressourcengruppe oder ein anderes Abonnement wird derzeit nicht unterstützt.

- Das Clusterupdate sollte nicht sowohl Identitäts- als auch Schlüsselbezeichnerdetails in demselben Vorgang enthalten. Wenn Sie beides aktualisieren müssen, sollte das Update in zwei aufeinanderfolgenden Vorgängen erfolgen.

- Lockbox ist in China derzeit nicht verfügbar. 

- [Doppelte Verschlüsselung](../../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption) wird automatisch für Cluster konfiguriert, die ab Oktober 2020 in unterstützten Regionen erstellt werden. Sie können überprüfen, ob Ihr Cluster für doppelte Verschlüsselung konfiguriert ist. Dazu senden Sie eine GET-Anforderung für den Cluster und beobachten, ob der `isDoubleEncryptionEnabled`-Wert für Cluster mit aktivierter doppelter Verschlüsselung `true` ist. 
  - Wenn Sie einen Cluster erstellen und die Fehlermeldung „Die doppelte Verschlüsselung für Cluster wird von Regionsname nicht unterstützt.“ erhalten, können Sie den Cluster trotzdem ohne doppelte Verschlüsselung erstellen, indem Sie `"properties": {"isDoubleEncryptionEnabled": false}` im REST-Anforderungstext hinzufügen.
  - Die Einstellung für doppelte Verschlüsselung kann nach dem Erstellen des Clusters nicht mehr geändert werden.

## <a name="troubleshooting"></a>Problembehandlung

- Wenn beim Erstellen eines Clusters ein Konfliktfehler auftritt, haben Sie Ihren Cluster möglicherweise in den letzten 14 Tagen gelöscht und dieser befindet sich nun im vorläufig gelöschten Zustand. Der Clustername bleibt während des Zeitraums des vorläufigen Löschens reserviert, und Sie können keinen neuen Cluster mit diesem Namen erstellen. Der Name wird nach Ablauf des Zeitraums des vorläufigen Löschens freigegeben, wenn der Cluster dauerhaft gelöscht wird.

- Wenn Sie den Cluster aktualisieren, während der Cluster bereitgestellt oder bereits aktualisiert wird, tritt bei der Aktualisierung ein Fehler auf.

- Einige Vorgänge sind umfassend und können einige Zeit in Anspruch nehmen. Dies sind: *Cluster erstellen*, *Clusterschlüssel aktualisieren* und *Cluster löschen*. Sie können den Vorgangsstatus überprüfen, indem Sie eine GET-Anforderung an den Cluster oder Arbeitsbereich senden und die Antwort beobachten. Beispielsweise weist der nicht verknüpfte Arbeitsbereich unter *features* nicht die *clusterResourceId* auf.

- Die Arbeitsbereichverknüpfung mit dem Cluster schlägt fehl, wenn er mit einem anderen Cluster verknüpft ist.

## <a name="error-messages"></a>Fehlermeldungen
  
### <a name="cluster-create"></a>Cluster erstellen

-  400 – Der Clustername ist ungültig. Der Clustername darf die Zeichen „a–z“, „A–Z“, „0–9“ und die Länge „3–63“ enthalten.
-  400 – Der Text der Anforderung ist NULL oder hat ein ungültiges Format.
-  400 – Der SKU-Name ist ungültig. Legen Sie den SKU-Namen auf „capacityReservation“ fest.
-  400 – Kapazität wurde bereitgestellt, aber SKU ist nicht „capacityReservation“. Legen Sie den SKU-Namen auf „capacityReservation“ fest.
-  400 – Fehlende Kapazität in SKU. Legen Sie den Kapazitätswert auf 500, 1000, 2000 oder 5000 GB/Tag fest.
-  400 – Die Kapazität ist für 30 Tage gesperrt. Eine Verringerung der Kapazität ist 30 Tage nach dem Update zulässig.
-  400 – Es wurde keine SKU festgelegt. Legen Sie den SKU-Namen auf „capacityReservation“ und den Kapazitätswert auf 500, 1000, 2000 oder 5000 GB/Tag fest.
-  400 – „Identität“ ist NULL oder leer. Legen Sie die Identität mit dem Typ „systemAssigned“ fest.
-  400 – „KeyVaultProperties“ werden bei der Erstellung festgelegt. Aktualisieren Sie „KeyVaultProperties“ nach der Clustererstellung.
-  400 – Der Vorgang kann jetzt nicht ausgeführt werden. Der asynchrone Vorgang befindet sich in einem anderen Zustand als „erfolgreich“. Der Cluster muss seinen Vorgang beenden, bevor ein Aktualisierungsvorgang ausgeführt wird.

### <a name="cluster-update"></a>Clusterupdate

-  400 –-Der Cluster befindet sich im Zustand „wird gelöscht“. Asynchroner Vorgang wird ausgeführt. Der Cluster muss seinen Vorgang beenden, bevor ein Aktualisierungsvorgang ausgeführt wird.
-  400 – „KeyVaultProperties“ ist nicht leer, hat aber ein ungültiges Format. Lesen Sie [Key Identifier Update](../logs/customer-managed-keys.md#update-cluster-with-key-identifier-details) (Aktualisierung des Schlüsselbezeichners).
-  400 – Fehler beim Überprüfen des Schlüssels in Key Vault. Mögliche Ursachen: Fehlende Berechtigungen, oder der Schlüssel ist nicht vorhanden. Vergewissern Sie sich, dass Sie die [Schlüssel- und Zugriffsrichtlinie in Key Vault](../logs/customer-managed-keys.md#grant-key-vault-permissions) festgelegt haben.
-  400 – Der Schlüssel kann nicht wiederhergestellt werden. Key Vault muss auf „Vorläufiges Löschen und Löschschutz“ festgelegt werden. Lesen Sie die [Dokumentation zu Key Vault](../../key-vault/general/soft-delete-overview.md).
-  400 – Der Vorgang kann jetzt nicht ausgeführt werden. Warten Sie, bis der asynchrone Vorgang beendet wurde, und versuchen Sie es erneut.
-  400 –-Der Cluster befindet sich im Zustand „wird gelöscht“. Warten Sie, bis der asynchrone Vorgang beendet wurde, und versuchen Sie es erneut.

### <a name="cluster-get"></a>Clusterabruf

 -  404 – Der Cluster wurde nicht gefunden; möglicherweise wurde er gelöscht. Wenn Sie versuchen, einen Cluster mit diesem Namen zu erstellen, und es zu einem Konflikt kommt, ist der Cluster 14 Tage lang im Zustand „Vorläufig gelöscht“. Sie können den Support kontaktieren, um ihn wiederherzustellen, oder einen anderen Namen zum Erstellen eines neuen Clusters verwenden. 

### <a name="cluster-delete"></a>Clusterlöschung

 -  409 – Ein Cluster kann im Bereitstellungsstatus nicht gelöscht werden. Warten Sie, bis der asynchrone Vorgang beendet wurde, und versuchen Sie es erneut.

### <a name="workspace-link"></a>Arbeitsbereichverknüpfung

-  404 – Arbeitsbereich nicht gefunden. Der von Ihnen angegebene Arbeitsbereich ist nicht vorhanden oder wurde gelöscht.
-  409 – Arbeitsbereichverknüpfung oder Aufheben der Verknüpfung in Bearbeitung.
-  400 – Der Cluster wurde nicht gefunden, der angegebene Cluster ist nicht vorhanden, oder er wurde gelöscht. Wenn Sie versuchen, einen Cluster mit diesem Namen zu erstellen, und es zu einem Konflikt kommt, ist der Cluster 14 Tage lang im Zustand „Vorläufig gelöscht“. Sie können den Support kontaktieren, um ihn wiederherzustellen.

### <a name="workspace-unlink"></a>Aufheben der Arbeitsbereichverknüpfung
-  404 – Arbeitsbereich nicht gefunden. Der von Ihnen angegebene Arbeitsbereich ist nicht vorhanden oder wurde gelöscht.
-  409 – Arbeitsbereichverknüpfung oder Aufheben der Verknüpfung in Bearbeitung.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über die [Abrechnung dedizierter Log Analytics-Cluster](./manage-cost-storage.md#log-analytics-dedicated-clusters).
- Erfahren Sie mehr über das [Entwerfen von Log Analytics-Arbeitsbereichen](../logs/design-logs-deployment.md).
