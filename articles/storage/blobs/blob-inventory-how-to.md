---
title: Aktivieren von Berichten zur Azure Storage-Blobinventur
description: Verschaffen Sie sich einen Überblick über Ihre Container, Blobs, Momentaufnahmen und Blobversionen in einem Speicherkonto.
services: storage
author: normesta
ms.service: storage
ms.date: 08/16/2021
ms.topic: how-to
ms.author: normesta
ms.reviewer: klaasl
ms.subservice: blobs
ms.openlocfilehash: ace430aa1e48d7e769f82d7258da241a0716a16d
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/05/2021
ms.locfileid: "129455643"
---
# <a name="enable-azure-storage-blob-inventory-reports"></a>Aktivieren von Berichten zur Azure Storage-Blobinventur

Der Blobinventur-Feature von Azure Storage bietet eine Übersicht über Ihre Container, Blobs, Momentaufnahmen und Blobversionen innerhalb eines Speicherkontos. Nutzen Sie den Inventurbericht, um verschiedene Attribute von Blobs und Containern zu verstehen, z. B. die Gesamtdatengröße, das Alter, den Verschlüsselungsstatus, die Unveränderlichkeitsrichtlinie, die gesetzliche Aufbewahrungspflicht und so weiter. Der Bericht enthält eine Übersicht über Ihre Daten, die Sie auch für Geschäfts- und Complianceanforderungen nutzen können.

Weitere Informationen zu Blobinventurberichten finden Sie unter [Azure Storage-Blobinventur](blob-inventory.md).

Sie aktivieren Blobinventurberichte, indem Sie Ihrem Speicherkonto eine Richtlinie mit mindestens einer Regel hinzufügen. Sie können eine Richtlinie über das [Azure-Portal](https://portal.azure.com/) hinzufügen, bearbeiten oder entfernen.

## <a name="enable-inventory-reports"></a>Aktivieren von Bestandsberichten

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Melden Sie sich zunächst beim [Azure-Portal](https://portal.azure.com/) an.

2. Suchen Sie nach Ihrem Speicherkonto, und zeigen Sie die Kontoübersicht an.

3. Wählen Sie **Blobinventur** unter **Datenmanagement**.

4. Wählen Sie **Erste Inventurregel hinzufügen** aus.

   Die Seite **Regel hinzufügen** wird angezeigt.

5. Auf der Seite **Regel hinzufügen** können Sie Ihre neue Regel benennen.

6. Wählen Sie einen Container.

7. Wählen Sie unter **Objekttyp für die Inventur**, ob Sie einen Bericht für die Blobs oder für die Container erstellen möchten.

   Wenn Sie **Blob** auswählen, können Sie unter **Blob-Untertyp** die Blobtypen auswählen, die in den Bericht aufgenommen werden sollen. Darüber hinaus können Sie wählen, ob Blobversionen und/oder Momentaufnahmen in Ihren Inventurbericht aufgenommen werden sollen.

   > [!NOTE]
   > Versionen und Momentaufnahmen müssen im Konto aktiviert sein, damit eine neue Regel mit der entsprechenden aktivierten Option gespeichert werden kann.

8. Wählen Sie das Format Ihrer Berichte sowie die Felder aus, die Sie in den Bericht integrieren möchten.

9. Wählen Sie aus, wie oft Sie Berichte erstellen möchten.

9. Fügen Sie optional einen Präfix-Abgleich hinzu, um Blobs in Ihrem Inventurbericht zu filtern.

10. Wählen Sie **Speichern** aus.

    :::image type="content" source="./media/blob-inventory-how-to/portal-blob-inventory.png" alt-text="Screenshot des Hinzufügens einer Blobbestandsregel über das Azure-Portal":::

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

<a id="powershell"></a>

Sie können eine Richtlinie über das Azure PowerShell-Modul hinzufügen, bearbeiten oder entfernen.

1. Öffnen Sie ein Windows PowerShell-Befehlsfenster.

2. Stellen Sie sicher, dass das neueste Azure PowerShell-Modul installiert ist. Weitere Informationen finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-Az-ps).

3. Melden Sie sich mit dem Befehl `Connect-AzAccount` bei Ihrem Azure-Abonnement an, und befolgen Sie die Anweisungen auf dem Bildschirm.

   ```powershell
   Connect-AzAccount
   ```

4. Wenn Ihre Identität mehreren Abonnements zugeordnet ist, legen Sie das aktive Abonnement fest.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   Ersetzen Sie den Platzhalterwert `<subscription-id>` durch die ID Ihres Abonnements.

5. Rufen Sie den Kontext des Speicherkontos ab, der das zu verwendende Speicherkonto definiert.

   ```powershell
   $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
   $ctx = $storageAccount.Context
   ```

   - Ersetzen Sie den Platzhalterwert `<resource-group-name>` durch den Namen Ihrer Ressourcengruppe.

   - Ersetzen Sie den Platzhalterwert `<storage-account-name>` durch den Namen Ihres Speicherkontos.

6. Erstellen Sie Inventurregeln mithilfe des Befehls [New-AzStorageBlobInventoryPolicyRule](/powershell/module/az.storage/new-azstorageblobinventorypolicyrule). Jede Regel listet Berichtsfelder auf. Eine vollständige Liste der Berichtsfelder finden Sie unter [Azure Storage-Blobinventur](blob-inventory.md).

   ```powershell
    $containerName = "my-container"

    $rule1 = New-AzStorageBlobInventoryPolicyRule -Name Test1 -Destination $containerName -Disabled -Format Csv -Schedule Daily -PrefixMatch con1,con2 `
                -ContainerSchemaField Name,Metadata,PublicAccess,Last-modified,LeaseStatus,LeaseState,LeaseDuration,HasImmutabilityPolicy,HasLegalHold

    $rule2 = New-AzStorageBlobInventoryPolicyRule -Name test2 -Destination $containerName -Format Parquet -Schedule Weekly  -BlobType blockBlob,appendBlob -PrefixMatch aaa,bbb `
                -BlobSchemaField name,Last-Modified,Metadata,LastAccessTime

    $rule3 = New-AzStorageBlobInventoryPolicyRule -Name Test3 -Destination $containerName -Format Parquet -Schedule Weekly -IncludeBlobVersion -IncludeSnapshot -BlobType blockBlob,appendBlob -PrefixMatch aaa,bbb `
                -BlobSchemaField name,Creation-Time,Last-Modified,Content-Length,Content-MD5,BlobType,AccessTier,AccessTierChangeTime,Expiry-Time,hdi_isfolder,Owner,Group,Permissions,Acl,Metadata,LastAccessTime

    $rule4 = New-AzStorageBlobInventoryPolicyRule -Name test4 -Destination $containerName -Format Csv -Schedule Weekly -BlobType blockBlob -BlobSchemaField Name,BlobType,Content-Length,Creation-Time

   ```

7. Nutzen Sie [Set-AzStorageBlobInventoryPolicy](/powershell/module/az.storage/set-azstorageblobinventorypolicy), um eine Blobinventurrichtlinie zu erstellen. Übergeben Sie mithilfe des `-Rule`-Parameters Regeln für diesen Befehl.

   ```powershell
   $policy = Set-AzStorageBlobInventoryPolicy -StorageAccount $storageAccount -Rule $rule1,$rule2,$rule3,$rule4  
   ```

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

<a id="cli"></a>

Sie können eine Richtlinie über die [Azure-Befehlszeilenschnittstelle (CLI)](/cli/azure/) hinzufügen, bearbeiten oder entfernen.

1. Öffnen Sie zunächst [Azure Cloud Shell](../../cloud-shell/overview.md), oder falls Sie die Azure-CLI lokal [installiert](/cli/azure/install-azure-cli) haben, öffnen Sie eine Befehlskonsolenanwendung wie Windows PowerShell.

2. Wenn Ihre Identität mehreren Abonnements zugeordnet ist, legen Sie das aktive Abonnement fest.

   ```azurecli
      az account set --subscription <subscription-id>
   ```

   Ersetzen Sie den Platzhalterwert `<subscription-id>` durch die ID Ihres Abonnements.

3. Definieren Sie die Regeln Ihrer Richtlinie in einem JSON-Dokument. Im Folgenden wird der Inhalt einer JSON-Beispieldatei mit dem Namen `policy.json` dargestellt.

    ```json
    {
    "enabled": true,
    "type": "Inventory",
    "rules": [
      {
        "enabled": true,
        "name": "inventoryPolicyRule2",
        "destination": "mycontainer",
        "definition": {
          "filters": {
            "blobTypes": [
              "blockBlob"
            ],
            "prefixMatch": [
              "inventoryprefix1",
              "inventoryprefix2"
            ],
            "includeSnapshots": true,
            "includeBlobVersions": true
          },
          "format": "Csv",
          "schedule": "Daily",
          "objectType": "Blob",
          "schemaFields": [
            "Name",
            "Creation-Time",
            "Last-Modified",
            "Content-Length",
            "Content-MD5",
            "BlobType",
            "AccessTier",
            "AccessTierChangeTime",
            "Snapshot",
            "VersionId",
            "IsCurrentVersion",
            "Metadata"
          ]
        }
      }
     ]
   }

   ```

4. Erstellen Sie eine Blobinventurrichtlinie mithilfe des [az storage account blob-inventory-policy](/cli/azure/storage/account/blob-inventory-policy#az_storage_account_blob_inventory_policy_create)-Befehls. Geben Sie den Namen Ihres JSON-Dokuments mithilfe des Parameters `--policy` an.

   ```azurecli
   az storage account blob-inventory-policy create -g myresourcegroup --account-name mystorageaccount --policy @policy.json
   ```

---

## <a name="next-steps"></a>Nächste Schritte

- [Berechnen der Anzahl und Gesamtgröße von Blobs pro Container](calculate-blob-count-size.md)
- [Verwalten des Azure Blob Storage-Lebenszyklus](./lifecycle-management-overview.md)
