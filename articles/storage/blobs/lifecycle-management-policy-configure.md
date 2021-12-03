---
title: Konfigurieren einer Richtlinie für die Lebenszyklusverwaltung
titleSuffix: Azure Storage
description: Konfigurieren Sie eine Richtlinie für die Lebenszyklusverwaltung, um Daten während des Datenlebenszyklus automatisch zwischen den Ebenen „Heiß“, „Kalt“ und „Archiv“ zu verschieben.
author: tamram
ms.author: tamram
ms.date: 08/18/2021
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.reviewer: yzheng
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 0fc4fce280db7a2d4744fc8466ff9297699494a3
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131460354"
---
# <a name="configure-a-lifecycle-management-policy"></a>Konfigurieren einer Richtlinie für die Lebenszyklusverwaltung

Die Azure Storage-Lebenszyklusverwaltung bietet eine regelbasierte Richtlinie, mit deren Hilfe Sie den Übergang von Blob-Daten auf die optimalen Zugriffsebenen und den Ablauf der Daten am Ende des Datenlebenszyklus umsetzen können. Eine Lebenszyklusrichtlinie fungiert für ein Basisblob und optional für die Versionen oder Momentaufnahmen des Blobs. Weitere Informationen zu Richtlinien für die Lebenszyklusverwaltung finden Sie unter [Optimieren der Kosten durch automatisches Verwalten des Datenlebenszyklus](lifecycle-management-overview.md).

Eine Richtlinie für die Lebenszyklusverwaltung besteht aus mindestens einer Regel, die eine Reihe von Aktionen definiert, die basierend auf einer erfüllten Bedingung durchgeführt werden sollen. Für ein Basisblob können Sie eine von zwei Bedingungen überprüfen:

- Die Anzahl der Tage seit der letzten Änderung des Blobs.
- Die Anzahl der Tage seit des letzten Zugriffs auf den Blob. Um diese Bedingung in einer Aktion zu verwenden, müssen Sie [zunächst optional die Nachverfolgung der Zugriffszeit aktivieren](#optionally-enable-access-time-tracking).

Wenn die ausgewählte Bedingung True ist, führt die Verwaltungsrichtlinie die angegebene Aktion aus. Wenn Sie z. B. eine Aktion zum Verschieben eines Blobs von der heißen Ebene in die kalte Ebene definiert haben, wenn es 30 Tage lang nicht geändert wurde, dann wird das Blob 30 Tage nach dem letzten Schreibvorgang auf diesem Blob von der Richtlinie für die Lebenszyklusverwaltung verschoben.

Bei einer Blobmomentaufnahme oder -version ist die überprüfte Bedingung die Anzahl der Tage seit dem Erstellen der Momentaufnahme oder Version.

## <a name="optionally-enable-access-time-tracking"></a>Aktivieren Sie optional die Nachverfolgung der Zugriffszeit.

Vor dem Konfigurieren einer Richtlinie für die Lebenszyklusverwaltung können Sie die Zeitnachverfolgung für den Blobzugriff aktivieren. Wenn die Nachverfolgung der Zugriffszeit aktiviert ist, kann eine Richtlinie für die Lebenszyklusverwaltung eine Aktion enthalten, die auf dem Zeitpunkt basiert, zu dem der letzte Zugriff auf das Blob mit einem Lese- oder Schreibvorgang erfolgte.

#### <a name="portal"></a>[Portal](#tab/azure-portal)

Führen Sie die folgenden Schritte aus, um die Nachverfolgung der letzten Zugriffszeit mit dem Azure-Portal zu aktivieren:

1. Navigieren Sie zum Speicherkonto im Azure-Portal.
1. Wählen Sie im Abschnitt **Datenverwaltung** die Option **Zugriffsüberwachung aktivieren** aus.

    :::image type="content" source="media/lifecycle-management-policy-configure/last-access-tracking-enable.png" alt-text="Screenshot: Aktivieren der letzten Zugriffsnachverfolgung in Azure-Portal":::

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Um die Nachverfolgung der letzten Zugriffszeit mit PowerShell zu aktivieren, rufen Sie den [Befehl Enable-AzStorageBlobLastAccessTimeTracking](/powershell/module/az.storage/enable-azstoragebloblastaccesstimetracking) auf, wie im folgenden Beispiel gezeigt. Denken Sie daran, die Platzhalterwerte in eckigen Klammern durch Ihre eigenen Werte zu ersetzen:

```powershell
# Initialize these variables with your values.
$rgName = "<resource-group>"
$accountName = "<storage-account>"

Enable-AzStorageBlobLastAccessTimeTracking  -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -PassThru
```

#### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Um die Nachverfolgung der letzten Zugriffszeit mit Azure CLI zu aktivieren, rufen Sie den Befehl [az storage account blob-service-properties update](/cli/azure/storage/account/blob-service-properties#az_storage_account_blob_service_properties_update) auf, wie im folgenden Beispiel gezeigt. Denken Sie daran, die Platzhalterwerte in eckigen Klammern durch Ihre eigenen Werte zu ersetzen:

```azurecli
az storage account blob-service-properties update \
    --resource-group <resource-group> \
    --account-name <storage-account> \
    --enable-last-access-tracking true
```

# <a name="template"></a>[Vorlage](#tab/template)

Um die Nachverfolgung der letzten Zugriffszeit für ein neues oder vorhandenes Speicherkonto mit einer Azure Resource Manager-Vorlage zu aktivieren, schließen Sie das **lastAccessTimeTrackingPolicy-Objekt** in die Vorlagendefinition ein. Weitere Informationen finden Sie unter [Microsoft.Storage/storageAccounts/blobServices 2021-02-01 – Bicep- und ARM-Vorlagenreferenz](/azure/templates/microsoft.storage/2021-02-01/storageaccounts/blobservices?tabs=json). Das Objekt **lastAccessTimeTrackingPolicy** ist in der Azure Storage-Ressourcenanbieter-REST-API für die Versionen 2019-06-01 und höher verfügbar.

---

Verwenden Sie die Eigenschaft **daysAfterLastAccessTimeGreaterThan**, um die Anzahl der Tage nach dem letzten Zugriff anzugeben, nach denen eine Aktion für ein Blob unternommen werden soll.

## <a name="create-or-manage-a-policy"></a>Erstellen oder Verwalten einer Richtlinie

Sie können eine Richtlinie für die Lebenszyklusverwaltung mit dem Azure-Portal, PowerShell, Azure CLI oder einer Azure Resource Manager-Vorlage hinzufügen oder entfernen.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Es gibt zwei Möglichkeiten zum Hinzufügen einer Richtlinie über das Azure-Portal.

- [Listenansicht](#list-view)
- [Codeansicht](#code-view)

#### <a name="list-view"></a>Listenansicht

1. Navigieren Sie im Azure-Portal zu Ihrem Speicherkonto.
1. Wählen Sie unter **Datenverwaltung** die Option **Lebenszyklusverwaltung** aus, um Lebenszyklusverwaltungsrichtlinien anzuzeigen oder zu ändern.
1. Wählen Sie die Registerkarte **Listenansicht** aus.

1. Wählen Sie **Regel hinzufügen** aus, und geben Sie Ihrer Regel im Formular **Details** einen Namen. Darüber hinaus können Sie Werte für **Regelbereich**, **Blobtyp** und **Blobuntertyp** festlegen. Im folgenden Beispiel wird der Bereich zum Filtern von Blobs festgelegt. Daraufhin wird die Registerkarte **Filtersatz** angezeigt.

   :::image type="content" source="media/lifecycle-management-policy-configure/lifecycle-management-details.png" alt-text="Seite mit Details zum Hinzufügen einer Regel in der Lebenszyklusverwaltung im Azure-Portal":::

1. Wählen Sie **Basisblobs** aus, um die Bedingungen für Ihre Regel festzulegen. Im nachstehenden Beispiel werden Blobs in den kalten Speicher verschoben, wenn sie während 30 Tagen nicht geändert wurden.

   :::image type="content" source="media/lifecycle-management-policy-configure/lifecycle-management-base-blobs.png" alt-text="Seite mit Basisblobs in der Lebenszyklusverwaltung im Azure-Portal":::

   Die Option **Letzter Zugriff** ist nur verfügbar, wenn Sie die Nachverfolgung der Zugriffszeit aktiviert haben. Informationen zum Aktivieren der Zugriffsüberwachung finden Sie unter [Optionales Aktivieren der Nachverfolgung der Zugriffszeit](#optionally-enable-access-time-tracking).

1. Wenn Sie auf der Seite **Details** die Option **Limit blobs with filters** (Blobs mit Filtern einschränken) ausgewählt haben, wählen Sie **Filtersatz** aus, um einen optionalen Filter hinzuzufügen. Das folgende Beispiel filtert nach Blobs, deren Name mit *log* in einem Container namens *sample-container* beginnt.

   :::image type="content" source="media/lifecycle-management-policy-configure/lifecycle-management-filter-set.png" alt-text="Seite mit dem Filtersatz für Lebenszyklusverwaltung im Azure-Portal":::

1. Wählen Sie **Hinzufügen** aus, um die neue Richtlinie hinzuzufügen.

#### <a name="code-view"></a>Codeansicht

1. Navigieren Sie im Azure-Portal zu Ihrem Speicherkonto.
1. Wählen Sie unter **Datenverwaltung** die Option **Lebenszyklusverwaltung** aus, um Lebenszyklusverwaltungsrichtlinien anzuzeigen oder zu ändern.
1. Wählen Sie die Registerkarte **Codeansicht** aus. Auf dieser Registerkarte können Sie eine Richtlinie für die Lebenszyklusverwaltung in JSON definieren.

Im folgenden JSON-Beispiel wird eine Lebenszyklusrichtlinie definiert, die ein Blockblob, dessen Name mit *log* beginnt, auf die kalte Ebene verschiebt, wenn mehr als 30 Tage seit der Änderung des Blobs vergangen sind.

   ```json
   {
     "rules": [
       {
         "enabled": true,
         "name": "move-to-cool",
         "type": "Lifecycle",
         "definition": {
           "actions": {
             "baseBlob": {
               "tierToCool": {
                 "daysAfterModificationGreaterThan": 30
               }
             }
           },
           "filters": {
             "blobTypes": [
               "blockBlob"
             ],
             "prefixMatch": [
               "sample-container/log"
             ]
           }
         }
       }
     ]
   }
   ```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Verwenden Sie die folgenden Befehle, um eine Richtlinie für die Lebenszyklusverwaltung mit PowerShell hinzuzufügen:

- Rufen Sie den Befehl [Add-AzStorageAccountManagementPolicyAction](/powershell/module/az.storage/add-azstorageaccountmanagementpolicyaction) auf, um die Aktionen zu definieren, die eine Regel bilden.
- Rufen Sie den Befehl [New-AzStorageAccountManagementPolicyFilter](/powershell/module/az.storage/new-azstorageaccountmanagementpolicyfilter) auf, um einen oder mehrere Filter für eine Regel anzugeben.
- Rufen Sie den Befehl [New-AzStorageAccountManagementPolicyRule](/powershell/module/az.storage/new-azstorageaccountmanagementpolicyrule) auf, um eine Richtlinienregel zu erstellen, die Aktionen und optionale Filter enthält.
- Rufen Sie den Befehl [Set-AzStorageAccountManagementPolicy](/powershell/module/az.storage/set-azstorageaccountmanagementpolicy) auf, um die Richtlinie für das Speicherkonto zu erstellen.

Das folgende Beispiel zeigt, wie Sie mit jedem dieser Befehle eine Lebenszyklusrichtlinie erstellen. Denken Sie daran, die Platzhalterwerte in eckigen Klammern durch Ihre eigenen Werte zu ersetzen:

```powershell
# Initialize the following variables with your values.
$rgName = "<resource-group>"
$accountName = "<storage-account>"

# Create a new action object.
$action = Add-AzStorageAccountManagementPolicyAction -BaseBlobAction Delete `
    -daysAfterModificationGreaterThan 180
Add-AzStorageAccountManagementPolicyAction -InputObject $action `
    -BaseBlobAction TierToArchive `
    -daysAfterModificationGreaterThan 90
Add-AzStorageAccountManagementPolicyAction -InputObject $action `
    -BaseBlobAction TierToCool `
    -daysAfterModificationGreaterThan 30
Add-AzStorageAccountManagementPolicyAction -InputObject $action `
    -SnapshotAction Delete `
    -daysAfterCreationGreaterThan 90
Add-AzStorageAccountManagementPolicyAction -InputObject $action `
    -BlobVersionAction TierToArchive `
    -daysAfterCreationGreaterThan 90

# Create a new filter object.
$filter = New-AzStorageAccountManagementPolicyFilter -PrefixMatch ab,cd `
    -BlobType blockBlob

# Create a new rule object.
$rule1 = New-AzStorageAccountManagementPolicyRule -Name sample-rule `
    -Action $action `
    -Filter $filter

# Create the policy.
Set-AzStorageAccountManagementPolicy -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -Rule $rule1
```

#### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Um eine Richtlinie für die Lebenszyklusverwaltung mit Azure CLI hinzuzufügen, schreiben Sie die Richtlinie in eine JSON-Datei, und rufen Sie dann den Befehl [az storage account management-policy create](/cli/azure/storage/account/management-policy#az_storage_account_management_policy_create) auf, um die Richtlinie zu erstellen.

Das folgende Beispiel zeigt, wie Sie mit jedem dieser Befehle eine Lebenszyklusrichtlinie erstellen. Denken Sie daran, die Platzhalterwerte in eckigen Klammern durch Ihre eigenen Werte zu ersetzen:

```azurecli
az storage account management-policy create \
    --account-name <storage-account> \
    --policy @policy.json \
    --resource-group <resource-group>
```

# <a name="template"></a>[Vorlage](#tab/template)

Um eine Richtlinie für die Lebenszyklusverwaltung mit einer Azure Resource Manager-Vorlage zu definieren, schließen Sie das **Microsoft.Storage/storageAccounts/managementPolicies-Objekt** in Ihre Vorlage ein. Weitere Informationen zur Konfiguration finden Sie unter [Microsoft.Storage/storageAccounts/managementPolicies 2021-02-01 – Bicep- und ARM-Vorlagenreferenz](/azure/templates/microsoft.storage/2021-02-01/storageaccounts/managementpolicies?tabs=json). Das Objekt **Microsoft.Storage/storageAccounts/managementPolicies** ist in der rest-API des Azure Storage-Ressourcenanbieters für die Versionen 2018-11-01 und höher verfügbar.

---

Eine Richtlinie für das Lebenszyklusmanagement muss vollständig gelesen oder geschrieben werden. Teilaktualisierungen werden nicht unterstützt.

> [!NOTE]
> Wenn Sie Firewallregeln für Ihr Speicherkonto aktivieren, werden Anforderungen für die Lebenszyklusverwaltung möglicherweise blockiert. Sie können die Sperre dieser Anforderungen durch Bereitstellen von Ausnahmen für vertrauenswürdige Microsoft-Dienste aufheben. Weitere Informationen finden Sie im Abschnitt **Ausnahmen** unter [Konfigurieren von Firewalls und virtuellen Netzwerken](../common/storage-network-security.md#exceptions).

## <a name="see-also"></a>Siehe auch

- [Optimieren von Kosten durch automatisches Verwalten des Datenlebenszyklus](lifecycle-management-overview.md)
- [Zugriffsebenen „Heiß“, „Kalt“ und „Archiv“ für Blobdaten](access-tiers-overview.md)
