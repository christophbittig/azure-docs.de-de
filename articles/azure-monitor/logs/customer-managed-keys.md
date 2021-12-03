---
title: Kundenseitig verwaltete Schlüssel in Azure Monitor
description: Informationen und Schritte zum Konfigurieren kundenseitig verwalteter Schlüssel für das Verschlüsseln von Daten in Ihren Log Analytics-Arbeitsbereichen mithilfe eines Azure Key Vault-Schlüssels.
ms.topic: conceptual
author: yossi-y
ms.author: yossiy
ms.date: 07/29/2021
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 2378daa38d1fba86bbb9194b7993cee9a862ad4c
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131461967"
---
# <a name="azure-monitor-customer-managed-key"></a>Kundenseitig verwaltete Schlüssel in Azure Monitor 

Daten in Azure Monitor werden mit von Microsoft verwalteten Schlüsseln verschlüsselt. Sie können Ihren eigenen Verschlüsselungsschlüssel verwenden, um die Daten und gespeicherten Abfragen in Ihren Arbeitsbereichen zu schützen. Wenn Sie einen kundenseitig verwalteten Schlüssel angeben, wird der Zugriff auf Ihre Daten mit diesem Schlüssel geschützt und gesteuert. Nach der Konfiguration werden alle Daten, die an Ihre Arbeitsbereiche gesendet werden, mit Ihrem Azure Key Vault-Schlüssel verschlüsselt. Vom Kunden verwaltete Schlüssel ermöglichen eine höhere Flexibilität bei der Verwaltung von Zugriffssteuerungen.

Es wird empfohlen, vor der Konfiguration die [Einschränkungen](#limitationsandconstraints) weiter unten zu überprüfen.

## <a name="customer-managed-key-overview"></a>Übersicht über kundenseitig verwaltete Schlüssel

Die [Verschlüsselung ruhender Daten](../../security/fundamentals/encryption-atrest.md) ist eine übliche Datenschutz- und Sicherheitsanforderung in Organisationen. Sie können die Verschlüsselung ruhender Daten vollständig von Azure verwalten lassen, wobei Ihnen verschiedene Optionen zum genauen Verwalten der Verschlüsselung und Verschlüsselungsschlüssel bereitstehen.

Mit Azure Monitor wird sichergestellt, dass alle Daten und gespeicherten Abfragen im Ruhezustand mit von Microsoft verwalteten Schlüsseln (MMK) verschlüsselt werden. Azure Monitor bietet auch eine Option für Verschlüsselung mit Ihrem eigenen Schlüssel, der in Ihrer [Azure Key Vault](../../key-vault/general/overview.md)-Instanz gespeichert ist. Damit können Sie den Zugriff auf Ihre Daten jederzeit widerrufen. Die Verwendung der Verschlüsselung durch Azure Monitor entspricht der Funktionsweise der [Azure Storage-Verschlüsselung](../../storage/common/storage-service-encryption.md#about-azure-storage-encryption).

Kundenseitig verwaltete Schlüssel werden auf [dedizierten Clustern](./logs-dedicated-clusters.md) bereitgestellt, die mehr Schutz und Kontrolle bieten. In dedizierten Clustern erfasste Daten werden zweimal verschlüsselt: einmal auf der Dienstebene mithilfe von Microsoft verwalteten Schlüsseln oder kundenseitig verwalteten Schlüsseln und einmal auf der Infrastrukturebene anhand von zwei verschiedenen Verschlüsselungsalgorithmen und zwei verschiedenen Schlüsseln. Die [doppelte Verschlüsselung](../../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption) schützt vor dem Szenario, dass einer der Verschlüsselungsalgorithmen oder Schlüssel kompromittiert wurde. In diesem Fall werden die Daten weiterhin durch die zusätzliche Verschlüsselungsebene geschützt. Ein dedizierter Cluster ermöglicht Ihnen außerdem das Schützen Ihrer Daten mit [Lockbox](#customer-lockbox-preview).

Daten, die in den letzten 14 Tagen erfasst wurden, und Daten, die kürzlich in Abfragen verwendet wurden, werden für effizientes Abfragen auch im Cache für heiße Daten (SSD-gestützt) aufbewahrt und unabhängig von der Konfiguration kundenseitig verwalteter Schlüssel mit Microsoft-Schlüsseln verschlüsselt. Ihre Kontrolle des SSD-Datenzugriffs gilt und entspricht der [Schlüsselsperrung](#key-revocation).

Das [Preismodell](./logs-dedicated-clusters.md#cluster-pricing-model) für dedizierte Log Analytics-Cluster erfordert eine Mindestabnahme beginnend bei 500 GB/Tag. Es kann Werte von 500, 1000, 2000 oder 5000 GB/Tag haben.

## <a name="how-customer-managed-key-works-in-azure-monitor"></a>Funktionsweise von kundenseitig verwalteten Schlüsseln in Azure Monitor

Azure Monitor verwendet eine verwaltete Identität, um Zugriff auf Ihre Azure Key Vault-Instanz zu gewähren. Die Identität des Log Analytics-Clusters wird auf Clusterebene unterstützt. Um den Schutz durch kundenseitig verwaltete Schlüssel in mehreren Arbeitsbereichen zu ermöglichen, wird eine neue Log Analytics-*Clusterressource* als temporäre Identitätsverbindung zwischen Key Vault und Log Analytics-Arbeitsbereichen eingesetzt. Der Clusterspeicher verwendet die der *Clusterressource* zugeordnete verwaltete Identität für die Authentifizierung bei Azure Key Vault über Azure Active Directory. 

Nach der Konfiguration des kundenseitig verwalteten Schlüssels werden alle neu erfassten Daten in den mit Ihrem dedizierten Cluster verknüpften Arbeitsbereichen mit Ihrem Schlüssel verschlüsselt. Sie können die Verknüpfung von Arbeitsbereichen mit dem Cluster jederzeit aufheben. Neue Daten werden dann im Log Analytics-Speicher erfasst und mit dem Microsoft-Schlüssel verschlüsselt, während Sie die neuen und alten Daten nahtlos abfragen können.

> [!IMPORTANT]
> Die Funktion für kundenseitig verwaltete Schlüssel ist regional. Azure Key Vault, Cluster und verknüpfte Log Analytics-Arbeitsbereiche müssen sich in der gleichen Region befinden, können jedoch in unterschiedlichen Abonnements enthalten sein.

![Übersicht über kundenseitig verwaltete Schlüssel](media/customer-managed-keys/cmk-overview.png)

1. Key Vault
2. Log Analytics-*Clusterressource* mit verwalteter Identität und Berechtigungen für Key Vault. Die Identität wird an den zugrunde liegenden dedizierten Log Analytics-Clusterspeicher weitergegeben.
3. Dedizierter Log Analytics-Cluster
4. Mit der *Clusterressource* verknüpfte Arbeitsbereiche 

### <a name="encryption-keys-operation"></a>Vorgang für Verschlüsselungsschlüssel

An der Speicherdatenverschlüsselung sind drei Arten von Schlüsseln beteiligt:

- **KEK** (Key Encryption Key): Schlüsselverschlüsselungsschlüssel (Ihr kundenseitig verwalteter Schlüssel)
- **AEK** (Account Encryption Key): Kontoverschlüsselungsschlüssel
- **DEK** (Data Encryption Key): Datenverschlüsselungsschlüssel

Es gelten die folgenden Regeln:

- Die Speicherkonten im Log Analytics-Cluster generieren einen eindeutigen Verschlüsselungsschlüssel für jedes Speicherkonto, der als AEK bezeichnet wird.
- Der AEK dient zum Ableiten von DEKs, bei denen es sich um die Schlüssel handelt, die zum Verschlüsseln der einzelnen, auf den Datenträger geschriebenen Datenblöcke verwendet werden.
- Wenn Sie den Schlüssel in Key Vault konfigurieren und im Cluster darauf verweisen, sendet Azure Storage Anforderungen an Azure Key Vault, um den AEK zu packen und zu entpacken und so Verschlüsselungs- und Entschlüsselungsvorgänge für Daten auszuführen.
- Ihr KEK verlässt niemals Ihre Key Vault-Instanz.
- Azure Storage verwendet die der *Clusterressource* zugeordnete verwaltete Identität für die Authentifizierung und den Zugriff auf Azure Key Vault über Azure Active Directory.

### <a name="customer-managed-key-provisioning-steps"></a>Bereitstellungsschritte für kundenseitig verwaltete Schlüssel

1. Erstellen von Azure Key Vault und Speichern des Schlüssels
1. Erstellen eines Clusters
1. Gewähren von Berechtigungen für Key Vault
1. Aktualisieren des Clusters mit Schlüsselbezeichnerdetails
1. Verknüpfen von Log Analytics-Arbeitsbereichen

Im Azure-Portal wird die Konfiguration kundenseitig verwalteter Schlüssel derzeit nicht unterstützt. Die Bereitstellung kann über [PowerShell](/powershell/module/az.operationalinsights/)-, [CLI](/cli/azure/monitor/log-analytics)- oder [REST](/rest/api/loganalytics/)-Anforderungen vorgenommen werden.

## <a name="storing-encryption-key-kek"></a>Speichern des Verschlüsselungsschlüssels (KEK)

Erstellen Sie eine neue oder verwenden Sie eine vorhandene Azure Key Vault-Instanz in der Region, in der der Cluster geplant ist, und generieren oder importieren Sie dann einen Schlüssel, der für die Protokollverschlüsselung verwendet werden soll. Azure Key Vault muss als wiederherstellbar konfiguriert werden, um Ihren Schlüssel und den Zugriff auf Ihre Daten in Azure Monitor zu schützen. Sie können diese Konfiguration unter den Eigenschaften in Ihrer Key Vault-Instanz überprüfen. Sowohl *Vorläufiges Löschen* als auch *Bereinigungsschutz* sollten aktiviert sein.

![Einstellungen für vorläufiges Löschen und Löschschutz](media/customer-managed-keys/soft-purge-protection.png)

Diese Einstellungen können in Key Vault über die CLI und PowerShell aktualisiert werden:

- [Vorläufiges Löschen](../../key-vault/general/soft-delete-overview.md)
- [Bereinigungsschutz](../../key-vault/general/soft-delete-overview.md#purge-protection) schützt vor erzwungenem Löschen des Geheimnisses/Tresors auch nach vorläufigem Löschen

## <a name="create-cluster"></a>Cluster erstellen

Cluster verwenden verwaltete Identitäten für die Datenverschlüsselung mit Key Vault. Legen Sie beim Erstellen Ihres Clusters die Eigenschaft `type` der Identität auf `SystemAssigned` fest, um den Zugriff auf Ihre Key Vault-Instanz für Pack- und Entpackvorgänge zuzulassen. 
  
  Identitätseinstellungen im Cluster für systemseitig zugewiesene verwaltete Identität
  ```json
  {
    "identity": {
      "type": "SystemAssigned"
      }
  }
  ```

Folgen Sie dem im [Artikel zu dedizierten Clustern](./logs-dedicated-clusters.md#create-a-dedicated-cluster) beschriebenen Verfahren. 

## <a name="grant-key-vault-permissions"></a>Erteilen von Key Vault-Berechtigungen

Erstellen Sie eine Zugriffsrichtlinie in Key Vault, um Berechtigungen für Ihren Cluster zu erteilen. Diese Berechtigungen werden vom zugrunde liegenden Azure Monitor-Speicher verwendet. Öffnen Sie Ihre Key Vault-Instanz im Azure-Portal, und klicken Sie auf *Zugriffsrichtlinien* und dann auf *+ Zugriffsrichtlinie hinzufügen*, um eine Richtlinie mit den folgenden Einstellungen zu erstellen:

- Key permissions (Schlüsselberechtigungen): Wählen Sie *Abrufen*, *Wrap Key* (Schlüssel packen) und *Unwrap Key* (Schlüssel entpacken) aus.
- Wählen Sie den Prinzipal aus: Je nach dem im Cluster verwendeten Identitätstyp (systemseitig oder benutzerseitig zugewiesene verwaltete Identität) geben Sie entweder den Clusternamen oder die Clusterprinzipal-ID für die systemseitig zugewiesene verwaltete Identität oder den Namen der benutzerseitig zugewiesenen verwalteten Identität an.

![Erteilen von Key Vault-Berechtigungen](media/customer-managed-keys/grant-key-vault-permissions-8bit.png)

Die Berechtigung *Abrufen* ist erforderlich, damit sichergestellt werden kann, dass Key Vault als wiederherstellbar konfiguriert ist, um Ihren Schlüssel und den Zugriff auf Ihre Azure Monitor-Daten zu schützen.

## <a name="update-cluster-with-key-identifier-details"></a>Aktualisieren des Clusters mit Schlüsselbezeichnerdetails

Für alle Vorgänge im Cluster ist die Aktionsberechtigung `Microsoft.OperationalInsights/clusters/write` erforderlich. Diese Berechtigung kann über den Besitzer oder einen Mitwirkenden erteilt werden, der die Aktion `*/write` enthält, oder über die Rolle „Analytics-Mitwirkender“, die die Aktion `Microsoft.OperationalInsights/*` enthält.

Mit diesem Schritt wird Azure Monitor Storage über den neuen Schlüssel und die Version informiert, die für die Datenverschlüsselung verwendet werden soll. Nach der Aktualisierung wird der neue Schlüssel zum Packen und Entpacken des Speicherschlüssels (AEK) verwendet.

>[!IMPORTANT]
>- Die Schlüsselrotation kann automatisch erfolgen oder eine explizite Aktualisierung des Schlüssels erfordern. Unter [Schlüsselrotation](#key-rotation) finden Sie Informationen zur Bestimmung des geeigneten Ansatzes, bevor Sie die Schlüsselbezeichnerdetails im Cluster aktualisieren.
>- Das Clusterupdate sollte nicht sowohl Identitäts- als auch Schlüsselbezeichnerdetails in demselben Vorgang enthalten. Wenn Sie beides aktualisieren müssen, sollte das Update in zwei aufeinanderfolgenden Vorgängen erfolgen.

![Erteilen von Key Vault-Berechtigungen](media/customer-managed-keys/key-identifier-8bit.png)

Aktualisieren Sie KeyVaultProperties im Cluster mit den Schlüsselbezeichnerdetails.

Der Vorgang ist asynchron und kann einige Zeit in Anspruch nehmen.

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

–

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli
az account set --subscription "cluster-subscription-id"

az monitor log-analytics cluster update --no-wait --name "cluster-name" --resource-group "resource-group-name" --key-name "key-name" --key-vault-uri "key-uri" --key-version "key-version"

# Wait for job completion when `--no-wait` was used
$clusterResourceId = az monitor log-analytics cluster list --resource-group "resource-group-name" --query "[?contains(name, "cluster-name")].[id]" --output tsv
az resource wait --created --ids $clusterResourceId --include-response-body true

```
# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
Select-AzSubscription "cluster-subscription-id"

Update-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name" -KeyVaultUri "key-uri" -KeyName "key-name" -KeyVersion "key-version" -AsJob

# Check when the job is done when `-AsJob` was used
Get-Job -Command "New-AzOperationalInsightsCluster*" | Format-List -Property *
```

# <a name="rest"></a>[REST](#tab/rest)

```rst
PATCH https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/cluster-name?api-version=2021-06-01
Authorization: Bearer <token> 
Content-type: application/json
 
{
  "properties": {
    "keyVaultProperties": {
      "keyVaultUri": "https://key-vault-name.vault.azure.net",
      "keyName": "key-name",
      "keyVersion": "current-version"
  },
  "sku": {
    "name": "CapacityReservation",
    "capacity": 500
  }
}
```

**Antwort**

Es dauert eine Weile, bis die Weitergabe des Schlüssels abgeschlossen ist. Sie können den Aktualisierungsstatus prüfen, indem Sie eine GET-Anforderung für den Cluster senden, und sich die Eigenschaften *KeyVaultProperties* ansehen. In der Antwort sollte der soeben aktualisierte Schlüssel zurückgegeben werden.

Nach Abschluss der Aktualisierung des Schlüssels sollte die Antwort auf die GET-Anforderung wie folgt aussehen: 202 (Akzeptiert) und Header
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
    "keyVaultProperties": {
      "keyVaultUri": "https://key-vault-name.vault.azure.net",
      "keyName": "key-name",
      "keyVersion": "current-version"
      },
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
```

---

## <a name="link-workspace-to-cluster"></a>Verknüpfen von Arbeitsbereich und Cluster

> [!IMPORTANT]
> Dieser Schritt sollte nur nach Abschluss der Log Analytics-Clusterbereitstellung ausgeführt werden. Wenn Sie vor der Bereitstellung Arbeitsbereiche verknüpfen und Daten erfassen, werden die erfassten Daten gelöscht und können nicht wiederhergestellt werden.

Sie müssen sowohl für Ihren Arbeitsbereich als auch für den Cluster über Schreibberechtigungen verfügen, um diesen Vorgang auszuführen. Dazu gehören die Berechtigungen `Microsoft.OperationalInsights/workspaces/write` und `Microsoft.OperationalInsights/clusters/write`.

Folgen Sie dem im [Artikel zu dedizierten Clustern](./logs-dedicated-clusters.md#link-a-workspace-to-a-cluster) beschriebenen Verfahren.

## <a name="key-revocation"></a>Schlüsselsperrung

> [!IMPORTANT]
> - Die empfohlene Vorgehensweise zum Widerrufen des Zugriffs auf Ihre Daten besteht darin, Ihren Schlüssel zu deaktivieren oder die Zugriffsrichtlinie auf Ihrer Key Vault-Instanz zu löschen.
> - Wenn Sie `identity` `type` für den Cluster auf `None` festlegen, wird auch der Zugriff auf Ihre Daten widerrufen. Dieser Ansatz wird jedoch nicht empfohlen, da Sie ihn nicht ohne Kontaktaufnahme mit dem Support rückgängig machen können.

Im Clusterspeicher werden Änderungen der Schlüsselberechtigungen immer innerhalb einer Stunde berücksichtigt, und der Speicher steht dann nicht mehr zur Verfügung. Neue Daten, die in den mit Ihrem Cluster verknüpften Arbeitsbereichen erfasst wurden, werden gelöscht und können nicht wiederhergestellt werden. Der Zugriff auf die Daten in diesen Arbeitsbereichen ist nicht mehr möglich und bei Abfragen treten Fehler auf. Zuvor erfasste Daten verbleiben im Speicher, solange der Cluster und Ihre Arbeitsbereiche nicht gelöscht werden. Daten, auf die nicht zugegriffen werden kann, unterliegen der Datenaufbewahrungsrichtlinie und werden bereinigt, sobald der Aufbewahrungszeitraum abgelaufen ist. Daten, die in den letzten 14 Tagen erfasst wurden, und Daten, die kürzlich in Abfragen verwendet wurden, werden für effizientes Abfragen auch im Cache für heiße Daten (SSD-gestützt) aufbewahrt. Die Daten auf SSD werden beim Schlüsselsperrungsvorgang gelöscht, und es kann dann nicht mehr darauf zugegriffen werden. Der Speicher des Clusters versucht in regelmäßigen Abständen, die Verschlüsselung mit Key Vault zu entpacken. Sobald Sie die Sperrung rückgängig machen, wird erfolgreich entpackt, SSD-Daten werden erneut aus dem Speicher geladen und die Datenerfassung und -abfrage werden innerhalb von 30 Minuten fortgesetzt.

## <a name="key-rotation"></a>Schlüsselrotation

Für die Schlüsselrotation stehen zwei Modi zur Verfügung: 
- Automatische Rotation: Wenn Sie Ihren Cluster mit ```"keyVaultProperties"``` aktualisieren, dabei aber die Eigenschaft ```"keyVersion"``` weglassen oder auf ```""``` festlegen, werden vom Speicher automatisch die neuesten Versionen verwendet.
- Explizite Aktualisierung der Schlüsselversion: Wenn Sie Ihren Cluster aktualisieren und dabei in der Eigenschaft ```"keyVersion"``` eine Schlüsselversion angeben, ist bei allen neuen Schlüsselversionen eine explizite Aktualisierung von ```"keyVaultProperties"``` im Cluster erforderlich. Weitere Informationen finden Sie unter [Aktualisieren des Clusters mit Schlüsselbezeichnerdetails](#update-cluster-with-key-identifier-details). Wenn Sie in Key Vault eine neue Schlüsselversion generieren, sie aber im Cluster nicht aktualisieren, wird vom Log Analytics-Clusterspeicher weiterhin Ihr vorheriger Schlüssel verwendet. Wenn Sie den alten Schlüssel deaktivieren oder löschen, bevor Sie den neuen Schlüssel im Cluster aktualisieren, wird der Status der [Schlüsselsperrung](#key-revocation) aktiv.

Nach der Schlüsselrotation kann auf alle Ihre Daten weiter zugegriffen werden, da Daten immer mit dem Kontoverschlüsselungsschlüssel (Account Encryption Key, AEK) verschlüsselt werden, während AEK nun mit der neuen Version des Schlüsselverschlüsselungsschlüssels (Key Encryption Key, KEK) in Key Vault verschlüsselt wird.

## <a name="customer-managed-key-for-saved-queries-and-log-alerts"></a>Kundenseitig verwalteter Schlüssel für gespeicherte Abfragen und Protokollwarnungen

Die in Log Analytics verwendete Abfragesprache ist ausdrucksstark und kann vertrauliche Informationen in Kommentaren enthalten, die Sie Abfragen oder in der Abfragesyntax hinzufügen. Einige Organisationen verlangen, dass diese Informationen als Teil der Richtlinie für kundenseitig verwaltete Schlüssel geschützt werden, und Sie müssen Ihre Abfragen mit Ihrem Schlüssel verschlüsselt speichern. Azure Monitor ermöglicht Ihnen das Speichern von Abfragen für *gespeicherter Suchvorgänge* und *Protokollwarnungen* mit Verschlüsselung mit Ihrem Schlüssel in Ihrem eigenen Speicherkonto, sofern Sie mit Ihrem Arbeitsbereich verbunden sind. 

> [!NOTE]
> Log Analytics-Abfragen können je nach verwendetem Szenario in verschiedenen Speichern abgelegt werden. Abfragen bleiben unabhängig von der Konfiguration kundenseitig verwalteter Schlüssel in den folgenden Szenarien mit von Microsoft verwalteten Schlüsseln (MMK) verschlüsselt: Arbeitsmappen in Azure Monitor, Azure-Dashboards, Azure-Logik-App, Azure Notebooks und Automation Runbooks.

Wenn Sie Bring Your Own Storage (BYOS) verwenden und es mit Ihrem Arbeitsbereich verknüpfen, lädt der Dienst Abfragen für *gespeicherte Suchen* und *Protokollwarnungen* in Ihr Speicherkonto hoch. Dies bedeutet, dass Sie das Speicherkonto und die [Richtlinie für die Verschlüsselung ruhender Daten](../../storage/common/customer-managed-keys-overview.md) entweder mit dem gleichen Schlüssel steuern, den Sie zum Verschlüsseln von Daten im Log Analytics-Cluster verwenden, oder mit einem anderen Schlüssel. Sie sind jedoch auch für die mit diesem Speicherkonto verbundenen Kosten verantwortlich. 

**Überlegungen vor dem Festlegen des kundenseitig verwalteten Schlüssels für Abfragen**
* Sie müssen für Ihren Arbeitsbereich und das Speicherkonto über die Berechtigung „Schreiben“ verfügen.
* Stellen Sie sicher, dass Sie Ihr Speicherkonto in derselben Region erstellen, in der sich Ihr Log Analytics-Arbeitsbereich befindet.
* Die *gespeicherten Suchvorgänge* im Speicher werden als Dienstartefakte angesehen und ihr Format kann sich ändern.
* Vorhandene *gespeicherte Suchvorgänge* werden aus dem Arbeitsbereich entfernt. Kopieren Sie alle *gespeicherten Suchvorgänge*, die Sie vor der Konfiguration benötigen. Sie können Ihre *gespeicherten Suchvorgänge* mithilfe von [PowerShell](/powershell/module/az.operationalinsights/get-azoperationalinsightssavedsearch) anzeigen.
* Der Abfrageverlauf wird nicht unterstützt, und Sie können keine Abfragen sehen, die Sie ausgeführt haben.
* Sie können mit dem Arbeitsbereich ein einzelnes Speicherkonto zum Speichern von Abfragen verknüpfen, es kann aber sowohl für Abfragen für *gespeicherte Suchvorgänge* als auch *Protokollwarnungen* verwendet werden.
* Anheften an das Dashboard wird nicht unterstützt.
* Ausgelöste Protokollwarnungen enthalten keine Suchergebnisse oder Warnungsabfragen. Sie können [Warnungsdimensionen](../alerts/alerts-unified-log.md#split-by-alert-dimensions) verwenden, um Kontext in den ausgelösten Warnungen zu erhalten.

**Konfigurieren von BYOS für Abfragen für gespeicherte Suchvorgänge**

Verknüpfen Sie das Speicherkonto für *Abfragen* mit Ihrem Arbeitsbereich. Abfragen für *gespeicherte Suchvorgänge* werden in Ihrem Speicherkonto gespeichert. 

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

–

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli
$storageAccountId = '/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage name>'

az account set --subscription "workspace-subscription-id"

az monitor log-analytics workspace linked-storage create --type Query --resource-group "resource-group-name" --workspace-name "workspace-name" --storage-accounts $storageAccountId
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
$storageAccount.Id = Get-AzStorageAccount -ResourceGroupName "resource-group-name" -Name "storage-account-name"

Select-AzSubscription "workspace-subscription-id"

New-AzOperationalInsightsLinkedStorageAccount -ResourceGroupName "resource-group-name" -WorkspaceName "workspace-name" -DataSourceType Query -StorageAccountIds $storageAccount.Id
```

# <a name="rest"></a>[REST](#tab/rest)

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>/linkedStorageAccounts/Query?api-version=2021-06-01
Authorization: Bearer <token> 
Content-type: application/json
 
{
  "properties": {
    "dataSourceType": "Query", 
    "storageAccountIds": 
    [
      "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>"
    ]
  }
}
```

---

Nach der Konfiguration werden alle neuen Abfragen *gespeicherter Suchvorgänge* in Ihrem Speicher gespeichert.

**Konfigurieren von BYOS für Abfragen für Protokollwarnungen**

Verknüpfen Sie das Speicherkonto für *Warnungen* mit Ihrem Arbeitsbereich. Abfragen für *Protokollwarnungen* werden in Ihrem Speicherkonto gespeichert. 

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

–

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

```azurecli
$storageAccountId = '/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage name>'

az account set --subscription "workspace-subscription-id"

az monitor log-analytics workspace linked-storage create --type ALerts --resource-group "resource-group-name" --workspace-name "workspace-name" --storage-accounts $storageAccountId
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
$storageAccount.Id = Get-AzStorageAccount -ResourceGroupName "resource-group-name" -Name "storage-account-name"

Select-AzSubscription "workspace-subscription-id"

New-AzOperationalInsightsLinkedStorageAccount -ResourceGroupName "resource-group-name" -WorkspaceName "workspace-name" -DataSourceType Alerts -StorageAccountIds $storageAccount.Id
```

# <a name="rest"></a>[REST](#tab/rest)

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>/linkedStorageAccounts/Alerts?api-version=2021-06-01
Authorization: Bearer <token> 
Content-type: application/json
 
{
  "properties": {
    "dataSourceType": "Alerts", 
    "storageAccountIds": 
    [
      "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>"
    ]
  }
}
```

---

Nach der Konfiguration werden alle neuen Warnungsabfragen in Ihrem Speicher gespeichert.

## <a name="customer-lockbox-preview"></a>Kunden-Lockbox (Vorschau)

Lockbox bietet Ihnen die Möglichkeit, die Anforderung eines Microsoft-Technikers für den Zugriff auf Ihre Daten während einer Supportanfrage zu genehmigen oder abzulehnen.

In Azure Monitor haben Sie diese Kontrolle über Daten in Arbeitsbereichen, die mit Ihrem dedizierten Log Analytics-Cluster verknüpft sind. Die Lockbox-Kontrolle gilt für Daten, die in einem dedizierten Log Analytics-Cluster gespeichert sind, wo sie in Speicherkonten des Clusters unter Ihrem Lockbox-geschützten Abonnement isoliert aufbewahrt werden.  

Weitere Informationen finden Sie unter [Kunden-Lockbox für Microsoft Azure](../../security/fundamentals/customer-lockbox-overview.md).

## <a name="customer-managed-key-operations"></a>Vorgänge für kundenseitig verwaltete Schlüssel

Der kundenseitig verwaltete Schlüssel wird im dedizierten Cluster bereitgestellt. Die folgenden Vorgänge werden im Artikel über [dedizierte Cluster](./logs-dedicated-clusters.md#change-cluster-properties) behandelt:

- Abrufen aller Cluster in einer Ressourcengruppe  
- Abrufen aller Cluster in einem Abonnement
- Aktualisieren der *Kapazitätsreservierung* in einem Cluster
- Aktualisieren von *billingType* im Cluster
- Aufheben der Verknüpfung eines Arbeitsbereichs mit einem Cluster
- Löschen von Clustern

## <a name="limitations-and-constraints"></a>Einschränkungen

- Die maximale Anzahl von Clustern pro Region und Abonnement beträgt 2.

- Die maximale Anzahl von Arbeitsbereichen, die mit einem Cluster verknüpft werden können, beträgt 1.000.

- Sie können einen Arbeitsbereich mit Ihrem Cluster verknüpfen und dann die Verknüpfung aufheben. Die Anzahl der Verknüpfungen in einem bestimmten Arbeitsbereich ist innerhalb eines Zeitraums von 30 Tagen auf 2 begrenzt.

- Die Verschlüsselung mit kundenseitig verwaltetem Schlüssel gilt für Daten, die nach dem Konfigurationszeitpunkt neu erfasst werden. Daten, die vor der Konfiguration erfasst wurden, bleiben mit dem Microsoft-Schlüssel verschlüsselt. Sie können vor und nach der Konfiguration des kundenseitig verwalteten Schlüssels erfasste Daten nahtlos abfragen.

- Azure Key Vault muss als wiederherstellbar konfiguriert werden. Die folgenden Eigenschaften sind standardmäßig nicht aktiviert und sollten mithilfe der CLI oder PowerShell konfiguriert werden:<br>
  - [Vorläufiges Löschen](../../key-vault/general/soft-delete-overview.md)
  - Der [Bereinigungsschutz](../../key-vault/general/soft-delete-overview.md#purge-protection) sollte aktiviert werden, wenn Sie sich auch nach dem vorläufigen Löschen vor dem erzwungenen Löschen des Geheimnis/Schlüsseltresors schützen möchten.

- Das Verschieben eines Clusters in eine andere Ressourcengruppe oder ein anderes Abonnement wird derzeit nicht unterstützt.

- Azure Key Vault, Cluster und Arbeitsbereiche müssen sich in derselben Region und in demselben Azure AD-Mandanten (Azure Active Directory) befinden, aber sie können in unterschiedlichen Abonnements enthalten sein.

- Das Clusterupdate sollte nicht sowohl Identitäts- als auch Schlüsselbezeichnerdetails in demselben Vorgang enthalten. Wenn Sie beides aktualisieren müssen, sollte das Update in zwei aufeinanderfolgenden Vorgängen erfolgen.

- Lockbox ist in China derzeit nicht verfügbar. 

- [Doppelte Verschlüsselung](../../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption) wird automatisch für Cluster konfiguriert, die ab Oktober 2020 in unterstützten Regionen erstellt werden. Sie können überprüfen, ob Ihr Cluster für doppelte Verschlüsselung konfiguriert ist. Dazu senden Sie eine GET-Anforderung für den Cluster und beobachten, ob der `isDoubleEncryptionEnabled`-Wert für Cluster mit aktivierter doppelter Verschlüsselung `true` ist. 
  - Wenn Sie einen Cluster erstellen und die Fehlermeldung „Die doppelte Verschlüsselung für Cluster wird von Regionsname nicht unterstützt.“ erhalten, können Sie den Cluster trotzdem ohne doppelte Verschlüsselung erstellen, indem Sie `"properties": {"isDoubleEncryptionEnabled": false}` im REST-Anforderungstext hinzufügen.
  - Die Einstellung für doppelte Verschlüsselung kann nach dem Erstellen des Clusters nicht mehr geändert werden.

  - Wenn Sie `identity` `type` für den Cluster auf `None` festlegen, wird auch der Zugriff auf Ihre Daten widerrufen. Dieser Ansatz wird jedoch nicht empfohlen, da Sie ihn nicht ohne Kontaktaufnahme mit dem Support rückgängig machen können. Die empfohlene Methode zum Widerrufen des Zugriffs auf Ihre Daten ist die [Schlüsselsperrung](#key-revocation).

  - Sie können einen kundenseitig verwalteten Schlüssel mit benutzerseitig zugewiesener verwalteter Identität nicht verwenden, wenn sich Ihre Key Vault-Instanz in einer Private Link-Instanz (VNet) befindet. Für dieses Szenario können Sie die systemseitig zugewiesene verwaltete Identität verwenden.

## <a name="troubleshooting"></a>Problembehandlung

- Verhalten bei Key Vault-Verfügbarkeit
  - Im Normalbetrieb: Der AEK wird für kurze Zeiträume von Storage zwischengespeichert und in regelmäßigen Abständen zum Entpacken in Key Vault zurückgeführt.
    
  - Key Vault-Verbindungsfehler: Storage handhabt vorübergehende Fehler (Timeouts, Verbindungsfehler, DNS-Probleme), indem Schlüssel für die Dauer des Verfügbarkeitsproblems im Cache verbleiben können. Dadurch werden Probleme aufgrund von Ausfällen sowie Verfügbarkeitsprobleme behoben. Die Abfrage- und Erfassungsfunktionen werden ohne Unterbrechung fortgesetzt.
    
- Key Vault-Zugriffsrate: Die Häufigkeit, mit der Azure Monitor Storage für Pack- und Entpackvorgänge auf Key Vault zugreift, liegt zwischen 6 und 60 Sekunden.

- Wenn Sie den Cluster aktualisieren, während der Cluster bereitgestellt oder bereits aktualisiert wird, tritt bei der Aktualisierung ein Fehler auf.

- Wenn beim Erstellen eines Clusters ein Konfliktfehler auftritt, haben Sie Ihren Cluster möglicherweise in den letzten 14 Tagen gelöscht und dieser befindet sich nun im Zeitraum des vorläufigen Löschens. Der Clustername bleibt während des Zeitraums des vorläufigen Löschens reserviert, und Sie können keinen neuen Cluster mit diesem Namen erstellen. Der Name wird nach Ablauf des Zeitraums des vorläufigen Löschens freigegeben, wenn der Cluster dauerhaft gelöscht wird.

- Die Arbeitsbereichverknüpfung mit dem Cluster schlägt fehl, wenn er mit einem anderen Cluster verknüpft ist.

- Wenn Sie einen Cluster erstellen und „KeyVaultProperties“ sofort angeben, tritt bei dem Vorgang möglicherweise ein Fehler auf, da die Zugriffsrichtlinie erst definiert werden kann, nachdem die Systemidentität des Clusters zugewiesen wurde.

- Wenn Sie einen vorhandenen Cluster mit „KeyVaultProperties“ aktualisieren und die Zugriffsrichtlinie für den Schlüsselabruf in Key Vault nicht vorhanden ist, schlägt der Vorgang fehl.

- Wenn Sie den Cluster nicht bereitstellen, vergewissern Sie sich, dass sich Azure Key Vault, Cluster und verknüpfte Log Analytics-Arbeitsbereiche in der gleichen Region befinden. Sie können in unterschiedlichen Abonnements enthalten sein.

- Wenn Sie die Schlüsselversion in Key Vault aktualisieren und die neuen Schlüsselbezeichnerdetails im Cluster nicht aktualisieren, verwendet der Log Analytics-Cluster weiterhin den vorherigen Schlüssel, und auf die Daten kann nicht mehr zugegriffen werden. Aktualisieren Sie neue Schlüsselbezeichnerdetails im Cluster, um mit der Datenerfassung fortzufahren und Daten abfragen zu können.

- Einige Vorgänge dauern lange und können einige Zeit in Anspruch nehmen. Dies sind Erstellen des Clusters, Aktualisieren des Clusterschlüssels und Löschen des Clusters. Sie können den Vorgangsstatus überprüfen, indem Sie eine GET-Anforderung an den Cluster oder Arbeitsbereich senden und die Antwort beobachten. Beispielsweise weist der nicht verknüpfte Arbeitsbereich unter *features* nicht die *clusterResourceId* auf.

- Fehlermeldungen
  
  **Clustererstellung**
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

  **Clusterupdate**
  -  400 –-Der Cluster befindet sich im Zustand „wird gelöscht“. Asynchroner Vorgang wird ausgeführt. Der Cluster muss seinen Vorgang beenden, bevor ein Aktualisierungsvorgang ausgeführt wird.
  -  400 – „KeyVaultProperties“ ist nicht leer, hat aber ein ungültiges Format. Lesen Sie [Key Identifier Update](#update-cluster-with-key-identifier-details) (Aktualisierung des Schlüsselbezeichners).
  -  400 – Fehler beim Überprüfen des Schlüssels in Key Vault. Mögliche Ursachen: Fehlende Berechtigungen, oder der Schlüssel ist nicht vorhanden. Vergewissern Sie sich, dass Sie die [Schlüssel- und Zugriffsrichtlinie in Key Vault](#grant-key-vault-permissions) festgelegt haben.
  -  400 – Der Schlüssel kann nicht wiederhergestellt werden. Key Vault muss auf „Vorläufiges Löschen und Löschschutz“ festgelegt werden. Lesen Sie die [Dokumentation zu Key Vault](../../key-vault/general/soft-delete-overview.md).
  -  400 – Der Vorgang kann jetzt nicht ausgeführt werden. Warten Sie, bis der asynchrone Vorgang beendet wurde, und versuchen Sie es erneut.
  -  400 –-Der Cluster befindet sich im Zustand „wird gelöscht“. Warten Sie, bis der asynchrone Vorgang beendet wurde, und versuchen Sie es erneut.

  **Clusterabruf**
    -  404 – Der Cluster wurde nicht gefunden; möglicherweise wurde er gelöscht. Wenn Sie versuchen, einen Cluster mit diesem Namen zu erstellen, und es zu einem Konflikt kommt, ist der Cluster 14 Tage lang im Zustand „Vorläufig gelöscht“. Sie können den Support kontaktieren, um ihn wiederherzustellen, oder einen anderen Namen zum Erstellen eines neuen Clusters verwenden. 

  **Clusterlöschung**
    -  409 – Ein Cluster kann im Bereitstellungsstatus nicht gelöscht werden. Warten Sie, bis der asynchrone Vorgang beendet wurde, und versuchen Sie es erneut.

  **Arbeitsbereichverknüpfung**
  -  404 – Arbeitsbereich nicht gefunden. Der von Ihnen angegebene Arbeitsbereich ist nicht vorhanden oder wurde gelöscht.
  -  409 – Arbeitsbereichverknüpfung oder Aufheben der Verknüpfung in Bearbeitung.
  -  400 – Der Cluster wurde nicht gefunden, der angegebene Cluster ist nicht vorhanden, oder er wurde gelöscht. Wenn Sie versuchen, einen Cluster mit diesem Namen zu erstellen, und es zu einem Konflikt kommt, ist der Cluster 14 Tage lang im Zustand „Vorläufig gelöscht“. Sie können den Support kontaktieren, um ihn wiederherzustellen.

  **Aufheben der Arbeitsbereichverknüpfung**
  -  404 – Arbeitsbereich nicht gefunden. Der von Ihnen angegebene Arbeitsbereich ist nicht vorhanden oder wurde gelöscht.
  -  409 – Arbeitsbereichverknüpfung oder Aufheben der Verknüpfung in Bearbeitung.
## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über die [Abrechnung dedizierter Log Analytics-Cluster](./manage-cost-storage.md#log-analytics-dedicated-clusters).
- Erfahren Sie mehr über das [Entwerfen von Log Analytics-Arbeitsbereichen](./design-logs-deployment.md).
