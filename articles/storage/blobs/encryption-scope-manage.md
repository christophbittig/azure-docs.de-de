---
title: Erstellen und Verwalten von Verschlüsselungsbereichen
description: Erfahren Sie, wie Sie einen Verschlüsselungsbereich erstellen, um Blobdaten auf Container- oder Blobebene zu isolieren.
services: storage
author: tamram
ms.service: storage
ms.date: 05/10/2021
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: adb5ac550ad998f807918d20f59048882c9a8565
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/04/2021
ms.locfileid: "123470578"
---
# <a name="create-and-manage-encryption-scopes"></a>Erstellen und Verwalten von Verschlüsselungsbereichen

Verschlüsselungsbereiche ermöglichen die Verwaltung der Verschlüsselung auf der Ebene einzelner Blobs oder Container. Sie können Verschlüsselungsbereiche verwenden, um sichere Grenzen zwischen Daten zu erstellen, die sich im selben Speicherkonto befinden, aber zu unterschiedlichen Kunden gehören. Weitere Informationen zu Verschlüsselungsbereichen finden Sie unter [Verschlüsselungsbereiche für Blobspeicher](encryption-scope-overview.md).

In diesem Artikel wird gezeigt, wie Sie einen Verschlüsselungsbereich erstellen. Darüber hinaus wird erläutert, wie Sie beim Erstellen eines Blobs oder Containers einen Verschlüsselungsbereich angeben.

## <a name="create-an-encryption-scope"></a>Erstellen eines Verschlüsselungsbereichs

Sie können einen Verschlüsselungsbereich erstellen, der mit einem von Microsoft verwalteten Schlüssel oder mit einem kundenseitig verwalteten Schlüssel geschützt ist, der in Azure Key Vault oder in einem Azure Key Vault Managed Hardware Security Model (HSM) gespeichert ist. Zum Erstellen eines Verschlüsselungsbereichs mit einem kundenseitig verwalteten Schlüssel müssen Sie zunächst einen Schlüsseltresor oder ein verwaltetes HSM erstellen und den Schlüssel hinzufügen, den Sie für den Bereich verwenden möchten. Für den Schlüsseltresor oder das verwaltete HSM muss der Löschschutz aktiviert sein. Außerdem muss sich der Schlüsseltresor oder das verwaltete HSM in derselben Region befinden wie das Speicherkonto.

Ein Verschlüsselungsbereich wird bei der Erstellung immer automatisch aktiviert. Nach der Erstellung des Verschlüsselungsbereichs können Sie diesen beim Erstellen eines Blobs angeben. Sie können beim Erstellen eines Containers auch einen Standardverschlüsselungsbereich angeben, der automatisch für alle Blobs im Container gilt.

# <a name="portal"></a>[Portal](#tab/portal)

Führen Sie die folgenden Schritte aus, um einen Verschlüsselungsbereich im Azure-Portal zu erstellen:

1. Navigieren Sie zum Speicherkonto im Azure-Portal.
1. Wählen Sie die Einstellung **Verschlüsselung** aus.
1. Wählen Sie die Registerkarte **Verschlüsselungsbereiche** aus.
1. Klicken Sie auf die Schaltfläche **Hinzufügen**, um einen neuen Verschlüsselungsbereich hinzuzufügen.
1. Geben Sie im Bereich **Verschlüsselungsbereich erstellen** einen Namen für den neuen Bereich ein.
1. Wählen Sie den gewünschten Typ der Verschlüsselungsschlüsselunterstützung aus: **Von Microsoft verwaltete Schlüssel** oder **Kundenseitig verwaltete Schlüssel**.
    - Wenn Sie **Von Microsoft verwaltete Schlüssel** ausgewählt haben, klicken Sie auf **Erstellen**, um den Verschlüsselungsbereich zu erstellen.
    - Wenn Sie **Kundenseitig verwaltete Schlüssel** ausgewählt haben, wählen Sie ein Abonnement aus, und geben Sie einen Schlüsseltresor oder ein verwaltetes HSM und einen Schlüssel an, die für diesen Verschlüsselungsbereich verwendet werden sollen.
1. Wenn die Infrastrukturverschlüsselung für das Speicherkonto aktiviert wurde, wird sie für den neuen Verschlüsselungsbereich automatisch aktiviert. Andernfalls können Sie auswählen, ob die Infrastrukturverschlüsselung für den Verschlüsselungsbereich aktiviert werden soll.

    :::image type="content" source="media/encryption-scope-manage/create-encryption-scope-customer-managed-key-portal.png" alt-text="Screenshot: Erstellen eines Verschlüsselungsbereichs über das Azure-Portal":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Installieren Sie zum Erstellen eines Verschlüsselungsbereichs mithilfe von PowerShell das PowerShell-Modul [Az.Storage](https://www.powershellgallery.com/packages/Az.Storage), Version 3.4.0 oder höher.

### <a name="create-an-encryption-scope-protected-by-microsoft-managed-keys"></a>Erstellen eines Verschlüsselungsbereichs, der durch von Microsoft verwaltete Schlüssel geschützt wird

Wenn Sie einen neuen Verschlüsselungsbereich erstellen möchten, der durch von Microsoft verwaltete Schlüssel geschützt wird, rufen Sie den Befehl [New-AzStorageEncryptionScope](/powershell/module/az.storage/new-azstorageencryptionscope) mit dem Parameter `-StorageEncryption` auf.

Wenn die Infrastrukturverschlüsselung für das Speicherkonto aktiviert wurde, wird sie für den neuen Verschlüsselungsbereich automatisch aktiviert. Andernfalls können Sie auswählen, ob die Infrastrukturverschlüsselung für den Verschlüsselungsbereich aktiviert werden soll. Zum Erstellen des neuen Bereichs mit aktivierter Infrastrukturverschlüsselung beziehen Sie den Parameter `-RequireInfrastructureEncryption` mit ein.

Denken Sie daran, die Platzhalterwerte in diesem Beispiel durch Ihre eigenen Werte zu ersetzen:

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$scopeName1 = "customer1scope"

New-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EncryptionScopeName $scopeName1 `
    -StorageEncryption
```

### <a name="create-an-encryption-scope-protected-by-customer-managed-keys"></a>Erstellen eines Verschlüsselungsbereichs, der mit kundenseitig verwalteten Schlüsseln geschützt wird

Wenn Sie einen neuen Verschlüsselungsbereich erstellen möchten, der mit kundenseitig verwalteten Schlüsseln geschützt wird, die in einem Schlüsseltresor oder einem verwalteten HSM gespeichert sind, konfigurieren Sie zunächst kundenseitig verwaltete Schlüssel für das Speicherkonto. Sie müssen dem Speicherkonto eine verwaltete Identität zuweisen und dann die verwaltete Identität verwenden, um die Zugriffsrichtlinie für den Schlüsseltresor oder das verwaltete HSM so zu konfigurieren, dass das Speicherkonto über Zugriffsberechtigungen verfügt.

Zum Konfigurieren von kundenseitig verwalteten Schlüsseln für die Verwendung mit einem Verschlüsselungsbereich muss der Löschschutz für den Schlüsseltresor oder das verwaltete HSM aktiviert werden. Der Schlüsseltresor oder das verwaltete HSM muss sich in derselben Region wie das Speicherkonto befinden.

Denken Sie daran, die Platzhalterwerte in diesem Beispiel durch Ihre eigenen Werte zu ersetzen:

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$keyVaultName = "<key-vault>"
$keyUri = "<key-uri>"
$scopeName2 = "customer2scope"

# Assign a system managed identity to the storage account.
$storageAccount = Set-AzStorageAccount -ResourceGroupName $rgName `
    -Name $accountName `
    -AssignIdentity

# Configure the access policy for the key vault.
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVaultName `
    -ObjectId $storageAccount.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get
```

Rufen Sie als Nächstes den Befehl [New-AzStorageEncryptionScope](/powershell/module/az.storage/new-azstorageencryptionscope) mit dem Parameter `-KeyvaultEncryption` auf, und geben Sie den Schlüssel-URI an. Das Einbeziehen der Schlüsselversion in den Schlüssel-URI ist optional. Wenn Sie die Schlüsselversion auslassen, verwendet der Verschlüsselungsbereich automatisch die neueste Schlüsselversion. Wenn Sie die Schlüsselversion mit einbeziehen, dann müssen Sie die Schlüsselversion manuell aktualisieren, um eine andere Version zu verwenden.

Wenn die Infrastrukturverschlüsselung für das Speicherkonto aktiviert wurde, wird sie für den neuen Verschlüsselungsbereich automatisch aktiviert. Andernfalls können Sie auswählen, ob die Infrastrukturverschlüsselung für den Verschlüsselungsbereich aktiviert werden soll. Zum Erstellen des neuen Bereichs mit aktivierter Infrastrukturverschlüsselung beziehen Sie den Parameter `-RequireInfrastructureEncryption` mit ein.

Denken Sie daran, die Platzhalterwerte in diesem Beispiel durch Ihre eigenen Werte zu ersetzen:

```powershell
New-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EncryptionScopeName $scopeName2 `
    -KeyUri $keyUri `
    -KeyvaultEncryption
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/cli)

Installieren Sie zum Erstellen eines Verschlüsselungsbereichs mit der Azure CLI zunächst mindestens Version 2.20.0 der CLI.

### <a name="create-an-encryption-scope-protected-by-microsoft-managed-keys"></a>Erstellen eines Verschlüsselungsbereichs, der durch von Microsoft verwaltete Schlüssel geschützt wird

Wenn Sie einen neuen Verschlüsselungsbereich erstellen möchten, der durch von Microsoft verwaltete Schlüssel geschützt wird, rufen Sie den Befehl [az storage account encryption-scope create](/cli/azure/storage/account/encryption-scope#az_storage_account_encryption_scope_create) auf, und geben Sie dabei `Microsoft.Storage` für den Parameter `--key-source` an.

Wenn die Infrastrukturverschlüsselung für das Speicherkonto aktiviert wurde, wird sie für den neuen Verschlüsselungsbereich automatisch aktiviert. Andernfalls können Sie auswählen, ob die Infrastrukturverschlüsselung für den Verschlüsselungsbereich aktiviert werden soll. Zum Erstellen des neuen Bereichs mit aktivierter Infrastrukturverschlüsselung beziehen Sie den Parameter `--require-infrastructure-encryption` mit ein, und legen Sie dessen Wert auf `true` fest.

Denken Sie daran, die Platzhalterwerte durch Ihre eigenen Werte zu ersetzen:

```azurecli-interactive
az storage account encryption-scope create \
    --resource-group <resource-group> \
    --account-name <storage-account> \
    --name <scope> \
    --key-source Microsoft.Storage
```

### <a name="create-an-encryption-scope-protected-by-customer-managed-keys"></a>Erstellen eines Verschlüsselungsbereichs, der mit kundenseitig verwalteten Schlüsseln geschützt wird

Wenn Sie einen neuen Verschlüsselungsbereich erstellen möchten, der mit kundenseitig verwalteten Schlüsseln in einem Schlüsseltresor oder einem verwalteten HSM geschützt wird, konfigurieren Sie zunächst kundenseitig verwaltete Schlüssel für das Speicherkonto. Sie müssen dem Speicherkonto eine verwaltete Identität zuweisen und dann die verwaltete Identität verwenden, um die Zugriffsrichtlinie für den Schlüsseltresor so zu konfigurieren, dass das Speicherkonto über Zugriffsberechtigungen verfügt. Weitere Informationen finden Sie unter [Kundenseitig verwaltete Schlüssel für die Azure Storage-Verschlüsselung](../common/customer-managed-keys-overview.md).

Zum Konfigurieren von kundenseitig verwalteten Schlüsseln für die Verwendung mit einem Verschlüsselungsbereich muss der Löschschutz für den Schlüsseltresor oder das verwaltete HSM aktiviert werden. Der Schlüsseltresor oder das verwaltete HSM muss sich in derselben Region wie das Speicherkonto befinden.

Denken Sie daran, die Platzhalterwerte in diesem Beispiel durch Ihre eigenen Werte zu ersetzen:

```azurecli-interactive
az login
az account set --subscription <subscription-id>

az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity

storage_account_principal=$(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query identity.principalId \
    --output tsv)

az keyvault set-policy \
    --name <key-vault> \
    --resource-group <resource_group> \
    --object-id $storage_account_principal \
    --key-permissions get unwrapKey wrapKey
```

Rufen Sie als Nächstes den Befehl [az storage account encryption-scope](/cli/azure/storage/account/encryption-scope#az_storage_account_encryption_scope_create) mit dem Parameter `--key-uri` auf, und geben Sie den Schlüssel-URI an. Das Einbeziehen der Schlüsselversion in den Schlüssel-URI ist optional. Wenn Sie die Schlüsselversion auslassen, verwendet der Verschlüsselungsbereich automatisch die neueste Schlüsselversion. Wenn Sie die Schlüsselversion mit einbeziehen, dann müssen Sie die Schlüsselversion manuell aktualisieren, um eine andere Version zu verwenden.

Wenn die Infrastrukturverschlüsselung für das Speicherkonto aktiviert wurde, wird sie für den neuen Verschlüsselungsbereich automatisch aktiviert. Andernfalls können Sie auswählen, ob die Infrastrukturverschlüsselung für den Verschlüsselungsbereich aktiviert werden soll. Zum Erstellen des neuen Bereichs mit aktivierter Infrastrukturverschlüsselung beziehen Sie den Parameter `--require-infrastructure-encryption` mit ein, und legen Sie dessen Wert auf `true` fest.

Denken Sie daran, die Platzhalterwerte in diesem Beispiel durch Ihre eigenen Werte zu ersetzen:

```azurecli-interactive
az storage account encryption-scope create \
    --resource-group <resource-group> \
    --account-name <storage-account> \
    --name <scope> \
    --key-source Microsoft.KeyVault \
    --key-uri <key-uri>
```

---

Wie Sie die Azure Storage-Verschlüsselung mit kundenseitig verwalteten Schlüsseln in einem Schlüsseltresor oder verwalteten HSM konfigurieren können, erfahren Sie in den folgenden Artikeln:

- [Konfigurieren der Verschlüsselung mit kundenseitig verwalteten Schlüsseln, die in Azure Key Vault gespeichert sind](../common/customer-managed-keys-configure-key-vault.md)
- [Konfigurieren der Verschlüsselung mit kundenseitig verwalteten Schlüsseln, die in Azure Key Vault Managed HSM gespeichert sind](../common/customer-managed-keys-configure-key-vault-hsm.md)

Weitere Informationen zur Infrastrukturverschlüsselung finden Sie unter [Aktivieren der Infrastrukturverschlüsselung für die doppelte Verschlüsselung von Daten](../common/infrastructure-encryption-enable.md).

## <a name="list-encryption-scopes-for-storage-account"></a>Auflisten der Verschlüsselungsbereiche für das Speicherkonto

# <a name="portal"></a>[Portal](#tab/portal)

Navigieren Sie zum Anzeigen der Verschlüsselungsbereiche für ein Speicherkonto im Azure-Portal zur Einstellung **Verschlüsselungsbereiche** für das Speicherkonto. In diesem Bereich können Sie einen Verschlüsselungsbereich aktivieren oder deaktivieren oder den Schlüssel für einen Verschlüsselungsbereich ändern.

:::image type="content" source="media/encryption-scope-manage/list-encryption-scopes-portal.png" alt-text="Screenshot: Liste der Verschlüsselungsbereiche im Azure-Portal":::

Zum Anzeigen von Details für einen kundenseitig verwalteten Schlüssel, einschließlich Schlüssel-URI und -version und ob die Schlüsselversion automatisch aktualisiert wird, folgen Sie dem Link in der Spalte **Schlüssel**.

:::image type="content" source="media/encryption-scope-manage/customer-managed-key-details-portal.png" alt-text="Screenshot: Details für einen Schlüssel, der mit einem Verschlüsselungsbereich verwendet wird":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Rufen Sie den Befehl **Get-AzStorageEncryptionScope** auf, um die für ein Speicherkonto verfügbaren Verschlüsselungsbereiche mit PowerShell aufzulisten. Denken Sie daran, die Platzhalterwerte in diesem Beispiel durch Ihre eigenen Werte zu ersetzen:

```powershell
Get-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -StorageAccountName $accountName
```

Verwenden Sie die Pipelinesyntax, um alle Verschlüsselungsbereiche in einer Ressourcengruppe nach Speicherkonto aufzulisten:

```powershell
Get-AzStorageAccount -ResourceGroupName $rgName | Get-AzStorageEncryptionScope
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/cli)

Rufen Sie den Befehl [az storage account encryption-scope list](/cli/azure/storage/account/encryption-scope#az_storage_account_encryption_scope_list) auf, um die für ein Speicherkonto verfügbaren Verschlüsselungsbereiche mit der Azure CLI aufzulisten. Denken Sie daran, die Platzhalterwerte in diesem Beispiel durch Ihre eigenen Werte zu ersetzen:

```azurecli-interactive
az storage account encryption-scope list \
    --account-name <storage-account> \
    --resource-group <resource-group>
```

---

## <a name="create-a-container-with-a-default-encryption-scope"></a>Erstellen eines Containers mit einem Standardverschlüsselungsbereich

Wenn Sie einen Container erstellen, können Sie einen standardmäßigen Verschlüsselungsbereich angeben. Blobs in diesem Container verwenden dann standardmäßig diesen Bereich.

Ein einzelnes Blob kann mit einem eigenen Verschlüsselungsbereich erstellt werden, sofern der Container nicht so konfiguriert ist, dass alle Blobs den Standardbereich verwenden. Weitere Informationen finden Sie unter [Verschlüsselungsbereiche für Container und Blobs](encryption-scope-overview.md#encryption-scopes-for-containers-and-blobs).

# <a name="portal"></a>[Portal](#tab/portal)

Wenn Sie einen Container mit einem Standardverschlüsselungsbereich im Azure-Portal erstellen möchten, erstellen Sie zunächst den Verschlüsselungsbereich wie unter [Erstellen eines Verschlüsselungsbereichs](#create-an-encryption-scope) beschrieben. Führen Sie anschließend die folgenden Schritte aus, um den Container zu erstellen:

1. Navigieren Sie zur Liste der Container in Ihrem Speicherkonto, und wählen Sie die Schaltfläche **Hinzufügen** aus, um einen neuen Container zu erstellen.
1. Erweitern Sie im Bereich **Neuer Container** die Einstellungen unter **Erweitert**.
1. Wählen Sie in der Dropdownliste **Verschlüsselungsbereich** den Standardverschlüsselungsbereich für den Container aus.
1. Wenn alle Blobs im Container den Standardverschlüsselungsbereich verwenden sollen, aktivieren Sie das Kontrollkästchen **Use this encryption scope for all blobs in the container** (Diesen Verschlüsselungsbereich für alle Blobs im Container verwenden). Ist dieses Kontrollkästchen aktiviert, kann ein einzelnes Blob im Container den Verschlüsselungsbereich nicht überschreiben.

    :::image type="content" source="media/encryption-scope-manage/create-container-default-encryption-scope.png" alt-text="Screenshot: Container mit Standardverschlüsselungsbereich":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Wenn Sie einen Container mit einem Standardverschlüsselungsbereich mit PowerShell erstellen möchten, rufen Sie den Befehl [New-AzStorageContainer](/powershell/module/az.storage/new-azstoragecontainer) auf, und geben Sie dabei den Bereich für den Parameter `-DefaultEncryptionScope` an. Legen Sie den Parameter `-PreventEncryptionScopeOverride` auf `true` fest, um zu erzwingen, dass alle Blobs in einem Container den Standardbereich des Containers verwenden.

```powershell
$containerName1 = "container1"
$ctx = New-AzStorageContext -StorageAccountName $accountName -UseConnectedAccount

# Create a container with a default encryption scope that cannot be overridden.
New-AzStorageContainer -Name $containerName1 `
    -Context $ctx `
    -DefaultEncryptionScope $scopeName1 `
    -PreventEncryptionScopeOverride $true
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/cli)

Wenn Sie einen Container mit einem Standardverschlüsselungsbereich mit der Azure CLI erstellen möchten, rufen Sie den Befehl [az storage container create](/cli/azure/storage/container#az_storage_container_create) auf, und geben Sie dabei den Bereich für den Parameter `--default-encryption-scope` an. Legen Sie den Parameter `--prevent-encryption-scope-override` auf `true` fest, um zu erzwingen, dass alle Blobs in einem Container den Standardbereich des Containers verwenden.

Im folgenden Beispiel wird Ihr Azure AD-Konto genutzt, um den Vorgang zur Erstellung des Containers zu autorisieren. Sie können auch den Kontozugriffsschlüssel verwenden. Weitere Informationen finden Sie unter [Autorisieren des Zugriffs auf Blob- oder Warteschlangendaten mit der Azure-Befehlszeilenschnittstelle](./authorize-data-operations-cli.md).

```azurecli-interactive
az storage container create \
    --account-name <storage-account> \
    --resource-group <resource-group> \
    --name <container> \
    --default-encryption-scope <scope> \
    --prevent-encryption-scope-override true \
    --auth-mode login
```

---

Versucht ein Client, einen Bereich anzugeben, wenn ein Blob in einen Container mit einem Standardverschlüsselungsbereich hochgeladen wird und der Container so konfiguriert ist, dass Blobs den Standardbereich nicht überschreiben können, tritt bei diesem Vorgang ein Fehler auf. Eine Meldung wird angezeigt, die angibt, dass die Anforderung durch die Containerverschlüsselungsrichtlinie untersagt wird.

## <a name="upload-a-blob-with-an-encryption-scope"></a>Hochladen eines Blobs mit einem Verschlüsselungsbereich

Beim Hochladen eines Blobs können Sie einen Verschlüsselungsbereich für das Blob angeben oder den Standardverschlüsselungsbereich für den Container verwenden, sofern einer angegeben wurde.

# <a name="portal"></a>[Portal](#tab/portal)

Wenn Sie ein Blob hochladen und den Verschlüsselungsbereich über das Azure-Portal angeben möchten, erstellen Sie zunächst den Verschlüsselungsbereich wie unter [Erstellen eines Verschlüsselungsbereichs](#create-an-encryption-scope) beschrieben. Führen Sie anschließend die folgenden Schritte aus, um das Blob zu erstellen:

1. Navigieren Sie zu dem Container, in den Sie das Blob hochladen möchten.
1. Wählen Sie die Schaltfläche **Hochladen** aus, und navigieren Sie zum hochzuladenden Blob.
1. Erweitern Sie im Bereich **Blob hochladen** die Einstellungen unter **Erweitert**.
1. Navigieren Sie zum Dropdownmenü im Abschnitt **Verschlüsselungsbereich**. Standardmäßig wird das Blob mit dem Standardverschlüsselungsbereich für den Container erstellt, sofern einer angegeben wurde. Wenn der Container erfordert, dass Blobs den Standardverschlüsselungsbereich verwenden, ist dieser Abschnitt deaktiviert.
1. Wenn Sie einen anderen Bereich für das hochzuladende Blob angeben möchten, wählen Sie **Vorhandenen Bereich auswählen** und dann in der Dropdownliste den gewünschten Bereich aus.

    :::image type="content" source="media/encryption-scope-manage/upload-blob-encryption-scope.png" alt-text="Screenshot: Hochladen eines Blobs mit einem Verschlüsselungsbereich":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Wenn Sie ein Blob mit einem Verschlüsselungsbereich über PowerShell hochladen möchten, rufen Sie den Befehl [Set-AzStorageBlobContent](/powershell/module/az.storage/set-azstorageblobcontent) auf, und geben Sie den Verschlüsselungsbereich für das Blob an.

```powershell
$containerName2 = "container2"
$localSrcFile = "C:\temp\helloworld.txt"
$ctx = New-AzStorageContext -StorageAccountName $accountName -UseConnectedAccount

# Create a new container with no default scope defined.
New-AzStorageContainer -Name $containerName2 -Context $ctx

# Upload a block upload with an encryption scope specified.
Set-AzStorageBlobContent -Context $ctx `
    -Container $containerName2 `
    -File $localSrcFile `
    -Blob "helloworld.txt" `
    -BlobType Block `
    -EncryptionScope $scopeName2
```

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Wenn Sie ein Blob mit einem Verschlüsselungsbereich über die Azure CLI hochladen möchten, rufen Sie den Befehl [az storage blob upload](/cli/azure/storage/blob#az_storage_blob_upload) auf, und geben Sie den Verschlüsselungsbereich für das Blob an.

Wenn Sie Azure Cloud Shell verwenden, führen Sie die unter [Hochladen eines Blobs](storage-quickstart-blobs-cli.md#upload-a-blob) beschriebenen Schritte aus, um eine Datei im Stammverzeichnis zu erstellen. Anschließend können Sie diese Datei mithilfe des folgenden Beispiels in ein Blob hochladen:

```azurecli-interactive
az storage blob upload \
    --account-name <storage-account> \
    --container-name <container> \
    --file <file> \
    --name <file> \
    --encryption-scope <scope>
```

---

## <a name="change-the-encryption-key-for-a-scope"></a>Ändern des Verschlüsselungsschlüssels für einen Bereich

Wenn Sie den Schlüssel, der einen Verschlüsselungsbereich schützt, von einem von Microsoft verwalteten Schlüssel in einen kundenseitig verwalteten Schlüssel ändern möchten, vergewissern Sie sich zunächst, dass Sie kundenseitig verwaltete Schlüssel mit Azure Key Vault oder Key Vault HSM für das Speicherkonto aktiviert haben. Weitere Informationen finden Sie unter [Konfigurieren der Verschlüsselung mit kundenseitig verwalteten Schlüsseln, die in Azure Key Vault gespeichert sind](../common/customer-managed-keys-configure-key-vault.md) oder [Konfigurieren der Verschlüsselung mit kundenseitig verwalteten Schlüsseln, die in Azure Key Vault Managed HSM gespeichert sind](../common/customer-managed-keys-configure-key-vault.md).

# <a name="portal"></a>[Portal](#tab/portal)

Führen Sie die folgenden Schritte aus, um den Schlüssel zu ändern, der einen Bereich im Azure-Portal schützt:

1. Navigieren Sie zur Registerkarte **Verschlüsselungsbereiche**, um die Liste der Verschlüsselungsbereiche für das Speicherkonto anzuzeigen.
1. Wählen Sie neben dem Bereich, den Sie ändern möchten, die Schaltfläche **Mehr** aus.
1. Im Bereich **Verschlüsselungsbereich bearbeiten** können Sie den Verschlüsselungstyp von „Von Microsoft verwaltete Schlüssel“ in „Kundenseitig verwaltete Schlüssel“ ändern und umgekehrt.
1. Wählen Sie zum Auswählen eines neuen kundenseitig verwalteten Schlüssels die Option **Neuen Schlüssel verwenden** aus, und geben Sie den Schlüsseltresor, den Schlüssel und die Schlüsselversion an.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Wenn Sie mit PowerShell den Schlüssel, der einen Verschlüsselungsbereich schützt, von einem kundenseitig verwalteten Schlüssel in einen von Microsoft verwalteten Schlüssel ändern möchten, rufen Sie den Befehl **Update-AzStorageEncryptionScope** auf, und übergeben Sie den Parameter `-StorageEncryption`:

```powershell
Update-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EncryptionScopeName $scopeName2 `
    -StorageEncryption
```

Rufen Sie als Nächstes den Befehl **Update-AzStorageEncryptionScope** auf, und übergeben Sie die Parameter `-KeyUri` und `-KeyvaultEncryption`:

```powershell
Update-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EncryptionScopeName $scopeName1 `
    -KeyUri $keyUri `
    -KeyvaultEncryption
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/cli)

Wenn Sie mit der Azure CLI den Schlüssel, der einen Verschlüsselungsbereich schützt, von einem kundenseitig verwalteten Schlüssel in einen von Microsoft verwalteten Schlüssel ändern möchten, rufen Sie den Befehl [az storage account encryption-scope update](/cli/azure/storage/account/encryption-scope#az_storage_account_encryption_scope_update) auf, und übergeben Sie den Parameter `--key-source` mit dem Wert `Microsoft.Storage`:

```azurecli-interactive
az storage account encryption-scope update \
    --account-name <storage-account> \
    --resource-group <resource-group>
    --name <scope> \
    --key-source Microsoft.Storage
```

Rufen Sie als Nächstes den Befehl **az storage account encryption-scope update** auf, und übergeben Sie den Parameter `--key-uri` sowie den Parameter `--key-source` mit dem Wert `Microsoft.KeyVault`:

```powershell
az storage account encryption-scope update \
    --resource-group <resource-group> \
    --account-name <storage-account> \
    --name <scope> \
    --key-source Microsoft.KeyVault \
    --key-uri <key-uri>
```

---

## <a name="disable-an-encryption-scope"></a>Deaktivieren eines Verschlüsselungsbereichs

Wenn ein Verschlüsselungsbereich deaktiviert ist, wird Ihnen dieser nicht mehr in Rechnung gestellt. Deaktivieren Sie alle Verschlüsselungsbereiche, die nicht benötigt werden, um unnötige Gebühren zu vermeiden. Weitere Informationen finden Sie unter [Azure Storage-Verschlüsselung für ruhende Daten](../common/storage-service-encryption.md).

# <a name="portal"></a>[Portal](#tab/portal)

Navigieren Sie zum Deaktivieren eines Verschlüsselungsbereichs im Azure-Portal zur Einstellung **Verschlüsselungsbereiche** für das Speicherkonto, und wählen Sie den gewünschten Verschlüsselungsbereich und dann **Deaktivieren** aus.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Rufen Sie zum Deaktivieren eines Verschlüsselungsbereichs mit PowerShell den Befehl „Update-AzStorageEncryptionScope“ auf, und fügen Sie den Parameter `-State` mit dem Wert `disabled` ein, wie im folgenden Beispiel gezeigt. Wenn Sie einen Verschlüsselungsbereich wieder aktivieren möchten, rufen Sie den gleichen Befehl auf. Legen Sie dabei den Parameter `-State` auf `enabled` fest. Denken Sie daran, die Platzhalterwerte in diesem Beispiel durch Ihre eigenen Werte zu ersetzen:

```powershell
Update-AzStorageEncryptionScope -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EncryptionScopeName $scopeName1 `
    -State disabled
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/cli)

Rufen Sie zum Deaktivieren eines Verschlüsselungsbereichs mit der Azure CLI den Befehl [az storage account encryption-scope update](/cli/azure/storage/account/encryption-scope#az_storage_account_encryption_scope_update) auf, und fügen Sie den Parameter `--state` mit dem Wert `Disabled` ein, wie im folgenden Beispiel gezeigt. Wenn Sie einen Verschlüsselungsbereich wieder aktivieren möchten, rufen Sie den gleichen Befehl auf. Legen Sie dabei den Parameter `--state` auf `Enabled` fest. Denken Sie daran, die Platzhalterwerte in diesem Beispiel durch Ihre eigenen Werte zu ersetzen:

```azurecli-interactive
az storage account encryption-scope update \
    --account-name <storage-account> \
    --resource-group <resource-group> \
    --name <scope> \
    --state Disabled
```

> [!IMPORTANT]
> Das Löschen eines Verschlüsselungsbereichs ist nicht möglich. Achten Sie darauf, alle derzeit nicht benötigten Verschlüsselungsbereiche zu deaktivieren, um unerwartete Kosten zu vermeiden.

---

## <a name="next-steps"></a>Nächste Schritte

- [Azure Storage encryption for data at rest (Azure Storage-Verschlüsselung für ruhende Daten)](../common/storage-service-encryption.md)
- [Verschlüsselungsbereiche für Blobspeicher](encryption-scope-overview.md)
- [Kundenseitig verwaltete Schlüssel für die Azure Storage-Verschlüsselung](../common/customer-managed-keys-overview.md)
- [Aktivieren von Infrastruktur-Verschlüsselung für Mehrfachverschlüsselung von Daten](../common/infrastructure-encryption-enable.md)
