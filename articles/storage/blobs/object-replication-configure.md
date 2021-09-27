---
title: Konfigurieren der Objektreplikation
titleSuffix: Azure Storage
description: Erfahren Sie, wie Sie die Objektreplikation so konfigurieren, dass Blockblobs asynchron aus einem Container in einem Speicherkonto in ein anderes kopiert werden.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 09/02/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 395b3d14571b9cf8ab2080c77aadf686dc8aea29
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128599277"
---
# <a name="configure-object-replication-for-block-blobs"></a>Konfigurieren der Objektreplikation für Blockblobs

Die Objektreplikation kopiert Blockblobs asynchron zwischen einem Quellspeicherkonto und einem Zielkonto. Wenn Sie die Objektreplikation konfigurieren, erstellen Sie eine Replikationsrichtlinie, in der das Quellspeicherkonto und das Zielkonto angegeben werden. Eine Replikationsrichtlinie enthält mindestens eine Regel, die einen Quellcontainer und einen Zielcontainer angibt sowie anzeigt, welche Blockblobs im Quellcontainer repliziert werden. Weitere Informationen zur Objektreplikation finden Sie unter [Objektreplikation für Blockblobs](object-replication-overview.md).

In diesem Artikel wird beschrieben, wie Sie eine Objektreplikationsrichtlinie über das Azure-Portal, PowerShell oder die Azure CLI konfigurieren. Sie können auch eine der Azure Storage-Ressourcenanbieter-Clientbibliotheken verwenden, um die Objektreplikation zu konfigurieren.

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie die Objektreplikation konfigurieren, erstellen Sie die Quell- und Zielspeicherkonten, wenn diese noch nicht vorhanden sind. Bei den Quell- und Zielkonten kann es sich entweder um Speicherkonten vom Typ „Allgemein v2“ oder um Premium-Blockblobkonten (Vorschauversion) handeln. Weitere Informationen finden Sie unter [Erstellen eines Azure Storage-Kontos](../common/storage-account-create.md).

Für die Objektreplikation muss die Blobversionsverwaltung sowohl für das Quell- als auch für das Zielkonto aktiviert sein. Darüber hinaus muss für das Quellkonto der Blobänderungsfeed aktiviert sein. Informationen zur Blobversionsverwaltung finden Sie unter [Blobversionsverwaltung](versioning-overview.md). Weitere Informationen zum Änderungsfeed finden Sie unter [Unterstützung für den Änderungsfeed in Azure Blob Storage](storage-blob-change-feed.md). Beachten Sie, dass die Aktivierung dieser Features zu zusätzlichen Kosten führen kann.

Zum Konfigurieren einer Richtlinie für die Objektreplikation für ein Speicherkonto muss Ihnen die Azure Resource Manager-Rolle **Mitwirkender** zugewiesen sein, die auf Ebene des Speicherkontos oder höher gilt. Weitere Informationen finden Sie in der Dokumentation zur rollenbasierten Zugriffssteuerung von Azure (Azure Role-Based Access Control, Azure RBAC) unter [In Azure integrierte Rollen](../../role-based-access-control/built-in-roles.md).

> [!IMPORTANT]
> Die Objektreplikation für Blockblobkonten vom Typ „Premium“ befindet sich derzeit in der **Vorschauversion**. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten rechtliche Bedingungen. Sie gelten für diejenigen Azure-Features, die sich in der Beta- oder Vorschauversion befinden oder aber anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.

## <a name="configure-object-replication-with-access-to-both-storage-accounts"></a>Konfigurieren der Objektreplikation mit Zugriff auf beide Speicherkonten

Wenn Sie Zugriff auf das Quell- und das Zielspeicherkonto haben, können Sie die Richtlinie für die Objektreplikation für beide Konten konfigurieren. Die folgenden Beispiele zeigen, wie die Objektreplikation mit dem Azure-Portal, mit PowerShell oder mit der Azure CLI konfiguriert wird.

### <a name="azure-portal"></a>[Azure portal](#tab/portal)

Wenn Sie die Objektreplikation im Azure-Portal konfigurieren, müssen Sie nur die Richtlinie für das Quellkonto konfigurieren. Das Azure-Portal erstellt die Richtlinie für das Zielkonto automatisch, nachdem Sie sie für das Quellkonto konfiguriert haben.

Führen Sie die folgenden Schritte aus, um eine Replikationsrichtlinie im Azure-Portal zu erstellen:

1. Navigieren Sie im Azure-Portal zum Quellspeicherkonto.
1. Wählen Sie unter **Blob-Dienst** die Option **Objektreplikation** aus.
1. Wählen Sie **Replikationsregeln einrichten** aus.
1. Wählen Sie das Zielabonnement und das Zielspeicherkonto aus.
1. Wählen Sie im Abschnitt **Containerpaare** einen Quellcontainer aus dem Quellkonto und einen Zielcontainer aus dem Zielkonto aus. Sie können bis zu 10 Containerpaare pro Replikationsrichtlinie erstellen.

    Die folgende Abbildung zeigt einen Satz von Replikationsregeln.

    :::image type="content" source="media/object-replication-configure/configure-replication-policy.png" alt-text="Screenshot von Replikationsregeln im Azure-Portal":::

1. Wenn gewünscht, geben Sie einen oder mehrere Filter an, um nur Blobs zu kopieren, die einem Präfixmuster entsprechen. Wenn Sie z. B. das Präfix `b` angeben, werden nur Blobs repliziert, deren Name mit diesem Buchstaben beginnt. Sie können ein virtuelles Verzeichnis als Teil des Präfixes angeben. Die Präfixzeichenfolge unterstützt keine Platzhalterzeichen.

    Die folgende Abbildung zeigt Filter, die einschränken, welche Blobs als Teil einer Replikationsregel kopiert werden.

    :::image type="content" source="media/object-replication-configure/configure-replication-copy-prefix.png" alt-text="Screenshot von Filtern für eine Replikationsregel":::

1. Standardmäßig wird der Kopierumfang so festgelegt, dass nur neue Objekte kopiert werden. Um alle Objekte im Container oder Objekte ab einem benutzerdefinierten Datum und Zeitpunkt zu kopieren, wählen Sie den Link **Ändern** aus, und konfigurieren Sie den Kopierumfang für das Containerpaar.

    Die folgende Abbildung zeigt einen benutzerdefinierten Kopierbereich, mit dem Objekte ab einem bestimmten Datum und einer bestimmten Uhrzeit kopiert werden.

    :::image type="content" source="media/object-replication-configure/configure-replication-copy-scope.png" alt-text="Screenshot mit benutzerdefiniertem Kopierumfang für die Objektreplikation":::

1. Wählen Sie **Speichern und anwenden** aus, um die Replikationsrichtlinie zu erstellen und mit der Replikation von Daten zu beginnen.

Nachdem Sie die Objektreplikation konfiguriert haben, werden die Replikationsrichtlinie und die Regeln im Azure-Portal angezeigt, wie in der folgenden Abbildung dargestellt.

:::image type="content" source="media/object-replication-configure/object-replication-policies-portal.png" alt-text="Screenshot der Richtlinie für die Objektreplikation im Azure-Portal":::

### <a name="powershell"></a>[PowerShell](#tab/powershell)

Um eine Replikationsrichtlinie mit PowerShell zu erstellen, installieren Sie zunächst Version [2.5.0](https://www.powershellgallery.com/packages/Az.Storage/2.5.0) oder höher des PowerShell-Moduls „Az.Storage“. Weitere Informationen zum Installieren von Azure PowerShell finden Sie unter [Installieren von Azure PowerShell mit PowerShellGet](/powershell/azure/install-az-ps).

Im folgenden Beispiel wird gezeigt, wie Sie eine Replikationsrichtlinie zuerst für das Zielkonto und dann für das Quellkonto erstellen. Denken Sie daran, die Werte in eckigen Klammern durch Ihre eigenen Werte zu ersetzen:

```powershell
# Sign in to your Azure account.
Connect-AzAccount

# Set variables.
$rgName = "<resource-group>"
$srcAccountName = "<source-storage-account>"
$destAccountName = "<destination-storage-account>"
$srcContainerName1 = "source-container1"
$destContainerName1 = "dest-container1"
$srcContainerName2 = "source-container2"
$destContainerName2 = "dest-container2"

# Enable blob versioning and change feed on the source account.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName `
    -EnableChangeFeed $true `
    -IsVersioningEnabled $true

# Enable blob versioning on the destination account.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName `
    -IsVersioningEnabled $true

# List the service properties for both accounts.
Get-AzStorageBlobServiceProperty -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName
Get-AzStorageBlobServiceProperty -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName

# Create containers in the source and destination accounts.
Get-AzStorageAccount -ResourceGroupName $rgname -StorageAccountName $srcAccountName |
    New-AzStorageContainer $srcContainerName1
Get-AzStorageAccount -ResourceGroupName $rgname -StorageAccountName $destAccountName |
    New-AzStorageContainer $destContainerName1
Get-AzStorageAccount -ResourceGroupName $rgname -StorageAccountName $srcAccountName |
    New-AzStorageContainer $srcContainerName2
Get-AzStorageAccount -ResourceGroupName $rgname -StorageAccountName $destAccountName |
    New-AzStorageContainer $destContainerName2

# Define replication rules for each container.
$rule1 = New-AzStorageObjectReplicationPolicyRule -SourceContainer $srcContainerName1 `
    -DestinationContainer $destContainerName1 `
    -PrefixMatch b
$rule2 = New-AzStorageObjectReplicationPolicyRule -SourceContainer $srcContainerName2 `
    -DestinationContainer $destContainerName2  `
    -MinCreationTime 2021-09-01T00:00:00Z

# Create the replication policy on the destination account.
$destPolicy = Set-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName `
    -PolicyId default `
    -SourceAccount $srcAccountName `
    -Rule $rule1,$rule2

# Create the same policy on the source account.
Set-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName `
    -InputObject $destPolicy
```

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Installieren Sie zum Erstellen einer Replikationsrichtlinie über die Azure-Befehlszeilenschnittstelle zunächst mindestens Version 2.11.1 der Azure CLI. Weitere Informationen finden Sie unter [Erste Schritte mit der Azure-Befehlszeilenschnittstelle](/cli/azure/get-started-with-azure-cli).

Aktivieren Sie als Nächstes die Blobversionsverwaltung für das Quell- und Zielspeicherkonto sowie für das Quellkonto auch den Änderungsfeed, indem Sie den Befehl [az storage account blob-service-properties update](/cli/azure/storage/account/blob-service-properties#az_storage_account_blob_service_properties_update) aufrufen. Denken Sie daran, die Werte in eckigen Klammern durch Ihre eigenen Werte zu ersetzen:

```azurecli
az login

az storage account blob-service-properties update \
    --resource-group <resource-group> \
    --account-name <source-storage-account> \
    --enable-versioning \
    --enable-change-feed

az storage account blob-service-properties update \
    --resource-group <resource-group> \
    --account-name <dest-storage-account> \
    --enable-versioning
```

Erstellen Sie die Quell- und Zielcontainer in ihren jeweiligen Speicherkonten.

```azurecli
az storage container create \
    --account-name <source-storage-account> \
    --name source-container-1 \
    --auth-mode login
az storage container create \
    --account-name <source-storage-account> \
    --name source-container-2 \
    --auth-mode login

az storage container create \
    --account-name <dest-storage-account> \
    --name dest-container-1 \
    --auth-mode login
az storage container create \
    --account-name <dest-storage-account> \
    --name dest-container-2 \
    --auth-mode login
```

Erstellen Sie eine neue Replikationsrichtlinie und eine zugehörige Regel für das Zielkonto, indem Sie den Befehl [az storage account or-policy create](/cli/azure/storage/account/or-policy#az_storage_account_or_policy_create) aufrufen.

```azurecli
az storage account or-policy create \
    --account-name <dest-storage-account> \
    --resource-group <resource-group> \
    --source-account <source-storage-account> \
    --destination-account <dest-storage-account> \
    --source-container source-container-1 \
    --destination-container dest-container-1 \
    --min-creation-time '2021-09-01T00:00:00Z' \
    --prefix-match a

```

Azure Storage legt die Richtlinien-ID für die neue Richtlinie fest, wenn diese erstellt wird. Um der Richtlinie zusätzliche Regeln hinzuzufügen, rufen Sie [az storage account or-policy rule add](/cli/azure/storage/account/or-policy/rule#az_storage_account_or_policy_rule_add) auf und geben dabei die Richtlinien-ID an.

```azurecli
az storage account or-policy rule add \
    --account-name <dest-storage-account> \
    --resource-group <resource-group> \
    --source-container source-container-2 \
    --destination-container dest-container-2 \
    --policy-id <policy-id> \
    --prefix-match b
```

Erstellen Sie als Nächstes die Richtlinie für das Quellkonto mithilfe der Richtlinien-ID.

```azurecli
az storage account or-policy show \
    --resource-group <resource-group> \
    --account-name <dest-storage-account> \
    --policy-id <policy-id> |
    az storage account or-policy create --resource-group <resource-group> \
    --account-name <source-storage-account> \
    --policy "@-"
```

---

## <a name="configure-object-replication-with-access-to-only-the-destination-account"></a>Konfigurieren der Objektreplikation mit reinem Zugriff auf das Zielkonto

Wenn Sie keine Berechtigungen für das Quellspeicherkonto haben, können Sie die Objektreplikation für das Zielkonto konfigurieren und eine JSON-Datei mit der Richtliniendefinition bereitstellen, damit ein anderer Benutzer die gleiche Richtlinie für das Quellkonto erstellen kann. Wenn sich das Quellkonto z. B. in einem anderen Azure AD-Mandanten als das Zielkonto befindet, können Sie mit diesem Ansatz die Objektreplikation konfigurieren.

> [!NOTE]
> Die mandantenübergreifende Objektreplikation ist für ein Speicherkonto standardmäßig zulässig. Um die Mandantenreplikation zu verhindern, können Sie die Eigenschaft **AllowCrossTenantReplication** (Vorschau) so festlegen, dass die mandantenübergreifende Objektreplikation für Ihre Speicherkonten nicht zugelassen wird. Weitere Informationen finden Sie unter [Verhindern der Objektreplikation zwischen Azure Active Directory-Mandanten](object-replication-prevent-cross-tenant-policies.md).

Die Beispiele in diesem Abschnitt zeigen, wie Sie die Objektreplikationsrichtlinie für das Zielkonto konfigurieren und dann die JSON-Datei für diese Richtlinie abrufen, die ein anderer Benutzer zum Konfigurieren der Richtlinie für das Quellkonto verwenden kann.

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

Führen Sie die folgenden Schritte aus, um die Objektreplikation für das Zielkonto mit einer JSON-Datei im Azure-Portal zu konfigurieren:

1. Erstellen Sie eine lokale JSON-Datei, die die Replikationsrichtlinie für das Zielkonto definiert. Legen Sie das Feld **policyId** auf *default* fest, damit Azure Storage die Richtlinien-ID definiert.

    Eine einfache Möglichkeit, eine JSON-Datei zu erstellen, die eine Replikationsrichtlinie definiert, besteht darin, zuerst im Azure-Portal eine Testreplikationsrichtlinie zwischen zwei Speicherkonten zu erstellen. Anschließend können Sie die Replikationsregeln herunterladen und die JSON-Datei nach Bedarf ändern.

1. Navigieren Sie im Azure-Portal zu den Einstellungen für die **Objektreplikation** für das Zielkonto.
1. Wählen Sie **Replikationsregeln hochladen** aus.
1. Laden Sie die JSON-Datei hoch. Im Azure-Portal werden die Richtlinie und die Regeln angezeigt, die erstellt werden, wie in der folgenden Abbildung dargestellt.

    :::image type="content" source="media/object-replication-configure/replication-rules-upload-portal.png" alt-text="Screenshot des Hochladens einer JSON-Datei zum Definieren einer Replikationsrichtlinie":::

1. Wählen Sie **Hochladen** aus, um die Replikationsrichtlinie für das Zielkonto zu erstellen.

Sie können dann eine JSON-Datei mit der Richtliniendefinition herunterladen, die Sie einem anderen Benutzer geben können, damit dieser das Quellkonto konfiguriert. Führen Sie die folgenden Schritte aus, um diese JSON-Datei herunterzuladen:

1. Navigieren Sie im Azure-Portal zu den Einstellungen für die **Objektreplikation** für das Zielkonto.
1. Wählen Sie neben der Richtlinie, die Sie herunterladen möchten, die Schaltfläche **Mehr** und dann **Regeln herunterladen** aus, wie in der folgenden Abbildung dargestellt.

    :::image type="content" source="media/object-replication-configure/replication-rules-download-portal.png" alt-text="Screenshot des Herunterladens von Replikationsregeln in eine JSON-Datei":::

1. Speichern Sie die JSON-Datei auf Ihrem lokalen Computer, um sie für andere Benutzer freizugeben, die die Richtlinie für das Quellkonto konfigurieren.

Die heruntergeladene JSON-Datei enthält die Richtlinien-ID, die Azure Storage für die Richtlinie für das Zielkonto erstellt hat. Sie müssen dieselbe Richtlinien-ID verwenden, um die Objektreplikation für das Quellkonto zu konfigurieren.

Beachten Sie, dass beim Hochladen einer JSON-Datei zum Erstellen einer Replikationsrichtlinie für das Zielkonto über das Azure-Portal nicht automatisch dieselbe Richtlinie im Quellkonto erstellt wird. Ein anderer Benutzer muss die Richtlinie für das Quellkonto erstellen, bevor Azure Storage mit der Replikation von Objekten beginnt.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Zum Herunterladen einer JSON-Datei, die die Definition der Replikationsrichtlinien für das Zielkonto enthält, mit PowerShell wenden Sie den Befehl [Get-AzStorageObjectReplicationPolicy](/powershell/module/az.storage/get-azstorageobjectreplicationpolicy) an, um die Richtlinie zurückzugeben. Konvertieren Sie anschließend die Richtlinie in der JSON-Datei, und speichern Sie diese als lokale Datei, wie im folgenden Beispiel gezeigt. Denken Sie daran, die Werte in eckigen Klammern und den Dateipfad durch Ihre eigenen Werte zu ersetzen:

```powershell
$rgName = "<resource-group>"
$destAccountName = "<destination-storage-account>"

$destPolicy = Get-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName
$destPolicy | ConvertTo-Json -Depth 5 > c:\temp\json.txt
```

Wenn Sie die JSON-Datei verwenden möchten, um die Replikationsrichtlinie für das Quellkonto mit PowerShell zu definieren, müssen Sie die lokale Datei abrufen und aus JSON in ein Objekt konvertieren. Rufen Sie dann den Befehl [Set-AzStorageObjectReplicationPolicy](/powershell/module/az.storage/set-azstorageobjectreplicationpolicy) auf, um die Richtlinie für das Quellkonto zu konfigurieren, wie im folgenden Beispiel gezeigt.

Stellen Sie beim Ausführen des Beispiels sicher, dass Sie den Parameter `-ResourceGroupName` auf die Ressourcengruppe für das Quellkonto und den Parameter `-StorageAccountName` auf den Namen des Quellkontos festlegen. Denken Sie außerdem daran, die Werte in eckigen Klammern und den Dateipfad durch Ihre eigenen Werte zu ersetzen:

```powershell
$object = Get-Content -Path C:\temp\json.txt | ConvertFrom-Json
Set-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName `
    -PolicyId $object.PolicyId `
    -SourceAccount $object.SourceAccount `
    -DestinationAccount $object.DestinationAccount `
    -Rule $object.Rules
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Um die Definition der Replikationsrichtlinien für das Zielkonto über die Azure-Befehlszeilenschnittstelle in eine JSON-Datei zu schreiben, rufen Sie den Befehl [az storage account or-policy show](/cli/azure/storage/account/or-policy#az_storage_account_or_policy_show) auf und leiten die Ausgabe in eine Datei um.

Im folgenden Beispiel wird die Richtliniendefinition in eine JSON-Datei mit dem Namen *policy.json* geschrieben. Denken Sie daran, die Werte in eckigen Klammern und den Dateipfad durch Ihre eigenen Werte zu ersetzen:

```azurecli
az storage account or-policy show \
    --account-name <dest-account-name> \
    --policy-id  <policy-id> > policy.json
```

Wenn Sie die JSON-Datei verwenden möchten, um die Replikationsrichtlinie für das Quellkonto mithilfe der Azure-Befehlszeilenschnittstelle zu konfigurieren, rufen Sie den Befehl [az storage account or-policy create](/cli/azure/storage/account/or-policy#az_storage_account_or_policy_create) auf, und verweisen Sie auf die Datei *policy.json*. Denken Sie daran, die Werte in eckigen Klammern und den Dateipfad durch Ihre eigenen Werte zu ersetzen:

```azurecli
az storage account or-policy create \
    -resource-group <resource-group> \
    --source-account <source-account-name> \
    --policy @policy.json
```

---

## <a name="check-the-replication-status-of-a-blob"></a>Überprüfen des Replikationsstatus eines Blobs

Sie können den Replikationsstatus für ein Blob im Quellkonto über das Azure-Portal, PowerShell oder die Azure CLI überprüfen. Objektreplikationseigenschaften werden erst gefüllt, wenn die Replikation entweder abgeschlossen oder fehlgeschlagen ist.

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

Führen Sie die folgenden Schritte aus, um den Replikationsstatus für ein Blob im Quellkonto im Azure-Portal zu überprüfen:

1. Navigieren Sie im Azure-Portal zum Quellkonto.
1. Suchen Sie den Container, der das Quellblob enthält.
1. Wählen Sie das Blob aus, um seine Eigenschaften anzuzeigen. Wenn das Blob erfolgreich repliziert wurde, sehen Sie im Abschnitt **Objektreplikation**, dass der Status auf *Vollständig* festgelegt ist. Die ID der Replikationsrichtlinie und die ID der Regel, die die Objektreplikation für diesen Container regelt, sind ebenfalls aufgeführt.

:::image type="content" source="media/object-replication-configure/check-replication-status-source.png" alt-text="Der Screenshot zeigt den Replikationsstatus für ein Blob im Quellkonto.":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Um den Replikationsstatus für ein Blob im Quellkonto mit PowerShell zu prüfen, rufen Sie den Wert der **ReplicationStatus**-Eigenschaft der Objektreplikation ab, wie im folgenden Beispiel gezeigt. Denken Sie daran, die Werte in eckigen Klammern durch Ihre eigenen Werte zu ersetzen:

```powershell
$ctxSrc = (Get-AzStorageAccount -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName).Context
$blobSrc = Get-AzStorageBlob -Container $srcContainerName1 `
    -Context $ctxSrc `
    -Blob <blob-name>
$blobSrc.BlobProperties.ObjectReplicationSourceProperties[0].Rules[0].ReplicationStatus
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Um den Replikationsstatus für ein Blob im Quellkonto mit der Azure CLI zu prüfen, rufen Sie den Wert der **status**-Eigenschaft der Objektreplikation ab, wie im folgenden Beispiel gezeigt:

```azurecli
az storage blob show \
    --account-name <source-account-name> \
    --container-name <source-container-name> \
    --name <source-blob-name> \
    --query 'objectReplicationSourceProperties[].rules[].status' \
    --output tsv \
    --auth-mode login
```

---

Wenn der Replikationsstatus für ein Blob im Quellkonto auf einen Fehler hinweist, untersuchen Sie die folgenden möglichen Ursachen:

- Stellen Sie sicher, dass die Objektreplikationsrichtlinie im Zielkonto konfiguriert ist.
- Überprüfen Sie, ob der Zielcontainer noch vorhanden ist.
- Wenn das Quellblob im Rahmen eines Schreibvorgangs mit einem vom Kunden bereitgestellten Schlüssel verschlüsselt wurde, tritt bei der Objektreplikation ein Fehler auf. Weitere Informationen zu vom Kunden bereitgestellten Schlüsseln finden Sie unter [Angeben eines Verschlüsselungsschlüssels bei Stellen einer Anforderung für Blob Storage](encryption-customer-provided-keys.md).

## <a name="remove-a-replication-policy"></a>Entfernen einer Replikationsrichtlinie

Um eine Replikationsrichtlinie und ihre zugehörigen Regeln zu entfernen, verwenden Sie das Azure-Portal, die PowerShell oder die CLI.

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

Führen Sie die folgenden Schritte aus, um eine Replikationsrichtlinie im Azure-Portal zu entfernen:

1. Navigieren Sie im Azure-Portal zum Quellspeicherkonto.
1. Wählen Sie unter **Einstellungen** die Option **Objektreplikation** aus.
1. Klicken Sie auf die Schaltfläche **Mehr** neben dem Richtliniennamen.
1. Wählen Sie **Regeln löschen** aus.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Um eine Replikationsrichtlinie zu entfernen, löschen Sie die Richtlinie sowohl im Quell- als auch im Zielkonto. Wenn Sie die Richtlinie löschen, werden auch alle Regeln gelöscht, die ihr zugeordnet sind.

```powershell
# Remove the policy from the destination account.
Remove-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName `
    -PolicyId $destPolicy.PolicyId

# Remove the policy from the source account.
Remove-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName `
    -PolicyId $destPolicy.PolicyId
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Um eine Replikationsrichtlinie zu entfernen, löschen Sie die Richtlinie sowohl im Quell- als auch im Zielkonto. Wenn Sie die Richtlinie löschen, werden auch alle Regeln gelöscht, die ihr zugeordnet sind.

```azurecli
az storage account or-policy delete \
    --policy-id <policy-id> \
    --account-name <source-storage-account> \
    --resource-group <resource-group>

az storage account or-policy delete \
    --policy-id <policy-id> \
    --account-name <dest-storage-account> \
    --resource-group <resource-group>
```

---

## <a name="next-steps"></a>Nächste Schritte

- [Objektreplikation für Blockblobs](object-replication-overview.md)
- [Verhindern der Objektreplikation zwischen Azure Active Directory-Mandanten](object-replication-prevent-cross-tenant-policies.md)
- [Aktivieren und Verwalten der Blobversionsverwaltung](versioning-enable.md)
- [Verarbeiten von Änderungsfeeds in Azure Blob Storage](storage-blob-change-feed-how-to.md)
