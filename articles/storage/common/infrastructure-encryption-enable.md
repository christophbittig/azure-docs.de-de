---
title: Aktivieren von Infrastruktur-Verschlüsselung für Mehrfachverschlüsselung von Daten
titleSuffix: Azure Storage
description: Kunden, die besonders sicher sein müssen, dass Ihre Daten sicher sind, können auch 256-Bit-AES-Verschlüsselung auf Azure Storage-Infrastrukturebene aktivieren. Wenn die Infrastruktur-Verschlüsselung aktiviert ist, werden Daten im Speicherkonto oder Verschlüsselungsbereich doppelt verschlüsselt, unter Verwendung von zwei verschiedenen Verschlüsselungsalgorithmen und zwei verschiedenen Schlüsseln.
services: storage
author: tamram
ms.service: storage
ms.date: 06/01/2021
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: c4dca05b855666a04d1def09228c596eb931bbf5
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128607005"
---
# <a name="enable-infrastructure-encryption-for-double-encryption-of-data"></a>Aktivieren von Infrastruktur-Verschlüsselung für Mehrfachverschlüsselung von Daten

Alle Daten werden in Azure Storage automatisch in einem Speicherkonto auf dem Servicelevel mit der 256-Bit-AES-Verschlüsselung verschlüsselt, einer der stärksten verfügbaren Blockchiffren, und konform mit dem FIPS 140-2-Standard. Kunden, welche besonders stark auf Datensicherheit achten müssen, können auch 256-Bit-AES-Verschlüsselung auf Azure Storage-Infrastrukturebene für Mehrfachverschlüsselung aktivieren. Die doppelte Verschlüsselung von Azure Storage-Daten schützt vor dem Szenario, dass einer der Verschlüsselungsalgorithmen oder Schlüssel kompromittiert wurde. In diesem Szenario werden die Daten weiterhin durch die zusätzliche Verschlüsselungsebene geschützt.

Die Infrastrukturverschlüsselung kann für das gesamte Speicherkonto oder für einen Verschlüsselungsbereich innerhalb eines Kontos aktiviert werden. Wenn die Infrastrukturverschlüsselung in einem Speicherkonto oder Verschlüsselungsbereich aktiviert ist, werden Daten zweifach verschlüsselt: &mdash; auf Service- und Infrastrukturebene &mdash;, mit zwei unterschiedlichen Verschlüsselungsalgorithmen und zwei verschiedenen Schlüsseln.

Die Verschlüsselung auf dem Servicelevel unterstützt die Verwendung von Schlüsseln, die von Microsoft oder vom Kunden verwaltet werden, mit Azure Key Vault oder Azure Key Vault Managed HSM (Hardware Security Module, Vorschau). Die Verschlüsselung auf Infrastrukturebene basiert auf von Microsoft verwalteten Schlüsseln und verwendet immer einen separaten Schlüssel. Weitere Informationen zur Schlüsselverwaltung mit Azure Storage-Verschlüsselung finden Sie unter [Informationen zur Verwaltung von Verschlüsselungsschlüsseln](storage-service-encryption.md#about-encryption-key-management).

Für die Mehrfachverschlüsselung Ihrer Daten müssen Sie zunächst ein Speicherkonto oder einen Verschlüsselungsbereich erstellen, welcher für Infrastrukturverschlüsselung konfiguriert ist. Dieser Artikel beschreibt, wie Sie Infrastrukturverschlüsselung aktivieren.

## <a name="create-an-account-with-infrastructure-encryption-enabled"></a>Erstellen eines Kontos mit aktivierter Infrastrukturverschlüsselung

Um die Infrastrukturverschlüsselung für ein Speicherkonto zu aktivieren, müssen Sie ein Speicherkonto für die Verwendung der Infrastrukturverschlüsselung zum Zeitpunkt der Kontoerstellung konfigurieren. Die Infrastrukturverschlüsselung kann nicht aktiviert oder deaktiviert werden, nachdem das Konto erstellt wurde. Das Speicherkonto muss vom Typ „Allgemein v2 (GPv2)“ sein.

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

Führen Sie die folgenden Schritte aus, um mit PowerShell ein Speicherkonto mit aktivierter Infrastrukturverschlüsselung zu erstellen:

1. Navigieren Sie im Azure-Portal zur Seite **Speicherkonten**.
1. Wählen Sie die Schaltfläche **Hinzufügen** aus, um ein neues Speicherkonto vom Typ „Universell v2“ hinzuzufügen.
1. Suchen Sie auf der Registerkarte **Erweitert** nach dem Verschlüsselungstyp **Infrastruktur**, und wählen Sie **Aktiviert** aus.
1. Wählen Sie **Überprüfen + erstellen** aus, um das Erstellen des Speicherkontos abzuschließen.

    :::image type="content" source="media/infrastructure-encryption-enable/create-account-infrastructure-encryption-portal.png" alt-text="Screenshot der Aktivierung der Infrastrukturverschlüsselung beim Erstellen eines Kontos":::

Führen Sie die folgenden Schritte im Azure-Portal aus, um zu überprüfen, ob die Infrastrukturverschlüsselung für ein Speicherkonto aktiviert ist:

1. Navigieren Sie zum Speicherkonto im Azure-Portal.
1. Wählen Sie unter **Einstellungen** die Option **Verschlüsselung** aus.

    :::image type="content" source="media/infrastructure-encryption-enable/verify-infrastructure-encryption-portal.png" alt-text="Screenshot der Überprüfung, ob die Infrastrukturverschlüsselung für das Konto aktiviert ist":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Wenn Sie über PowerShell ein Speicherkonto mit aktivierter Infrastrukturverschlüsselung erstellen möchten, muss das [Az.Storage-PowerShell-Modul](https://www.powershellgallery.com/packages/Az.Storage) Version 2.2.0 oder höher installiert sein. Weitere Informationen finden Sie unter [Installieren von Azure PowerShell](/powershell/azure/install-az-ps).

Erstellen Sie als Nächstes ein Speicherkonto vom Typ „Universell v2 (GPv2)“, indem Sie den Befehl [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) aufrufen. Beziehen Sie die `-RequireInfrastructureEncryption`-Option zum Aktivieren der Infrastrukturverschlüsselung ein.

Das folgende Beispiel zeigt, wie Sie ein Speicherkonto vom Typ „Universell v2“ erstellen, das für georedundanten Speicher mit Lesezugriff (Read-Access Geo-Redundant Storage, RA-GRS) konfiguriert ist und für das die Infrastrukturverschlüsselung zum doppelten Verschlüsseln von Daten aktiviert ist. Denken Sie daran, die Platzhalterwerte in Klammern durch Ihre eigenen Werte zu ersetzen:

```powershell
New-AzStorageAccount -ResourceGroupName <resource_group> `
    -AccountName <storage-account> `
    -Location <location> `
    -SkuName "Standard_RAGRS" `
    -Kind StorageV2 `
    -RequireInfrastructureEncryption
```

Um zu überprüfen, ob die Infrastrukturverschlüsselung für ein Speicherkonto aktiviert ist, können Sie den Befehl [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) abrufen. Dieser Befehl gibt einen Satz von Speicherkontoeigenschaften und deren Werte zurück. Rufen Sie das Feld `RequireInfrastructureEncryption` innerhalb der Eigenschaft `Encryption` ab, und überprüfen Sie, ob es auf `True` festgelegt ist.

Im folgenden Beispiel wird der Wert der `RequireInfrastructureEncryption`-Eigenschaft abgerufen. Denken Sie daran, die Platzhalterwerte in eckigen Klammern durch Ihre eigenen Werte zu ersetzen:

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
$account.Encryption.RequireInfrastructureEncryption
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Wenn Sie mithilfe der Azure-Befehlszeilenschnittstelle (Azure CLI) ein Speicherkonto erstellen möchten, für das die Infrastrukturverschlüsselung aktiviert ist, müssen Sie Azure CLI, Version 2.8.0 oder höher, installiert haben. Weitere Informationen finden Sie unter [Installieren der Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli).

Erstellen Sie als Nächstes ein Speicherkonto vom Typ „Allgemein v2 (GPv2)“, indem Sie den Befehl [az storage account create](/cli/azure/storage/account#az_storage_account_create) mit der `--require-infrastructure-encryption option` aufrufen, um die Infrastrukturverschlüsselung zu aktivieren.

Das folgende Beispiel zeigt, wie Sie ein Speicherkonto vom Typ „Universell v2“ erstellen, das für georedundanten Speicher mit Lesezugriff (Read-Access Geo-Redundant Storage, RA-GRS) konfiguriert ist und für das die Infrastrukturverschlüsselung zum doppelten Verschlüsseln von Daten aktiviert ist. Denken Sie daran, die Platzhalterwerte in Klammern durch Ihre eigenen Werte zu ersetzen:

```azurecli-interactive
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --location <location> \
    --sku Standard_RAGRS \
    --kind StorageV2 \
    --require-infrastructure-encryption
```

Um zu überprüfen, ob die Infrastrukturverschlüsselung für ein Speicherkonto aktiviert ist, rufen Sie den Befehl [az storage account show](/cli/azure/storage/account#az-storage-account-show) auf. Dieser Befehl gibt einen Satz von Speicherkontoeigenschaften und deren Werte zurück. Überprüfen Sie, ob das Feld `requireInfrastructureEncryption` innerhalb der Eigenschaft `encryption` auf `true` festgelegt ist.

Im folgenden Beispiel wird der Wert der `requireInfrastructureEncryption`-Eigenschaft abgerufen. Denken Sie daran, die Platzhalterwerte in eckigen Klammern durch Ihre eigenen Werte zu ersetzen:

```azurecli-interactive
az storage account show /
    --name <storage-account> /
    --resource-group <resource-group>
```

# <a name="template"></a>[Vorlage](#tab/template)

Im folgenden JSON-Beispiel wird ein Speicherkonto vom Typ „Universell v2“ erstellt, das für georedundanten Speicher mit Lesezugriff (Read-Access Geo-Redundant Storage, RA-GRS) konfiguriert ist und für das die Infrastrukturverschlüsselung zum doppelten Verschlüsseln von Daten aktiviert ist. Denken Sie daran, die Platzhalterwerte in Klammern durch Ihre eigenen Werte zu ersetzen:

```json
"resources": [
    {
        "type": "Microsoft.Storage/storageAccounts",
        "apiVersion": "2019-06-01",
        "name": "[parameters('<storage-account>')]",
        "location": "[parameters('<location>')]",
        "dependsOn": [],
        "tags": {},
        "sku": {
            "name": "[parameters('Standard_RAGRS')]"
        },
        "kind": "[parameters('StorageV2')]",
        "properties": {
            "accessTier": "[parameters('<accessTier>')]",
            "supportsHttpsTrafficOnly": "[parameters('supportsHttpsTrafficOnly')]",
            "largeFileSharesState": "[parameters('<largeFileSharesState>')]",
            "encryption": {
                "keySource": "Microsoft.Storage",
                "requireInfrastructureEncryption": true,
                "services": {
                    "blob": { "enabled": true },
                    "file": { "enabled": true }
              }
            }
        }
    }
],
```

---

Azure Policy stellt eine integrierte Richtlinie zur Verfügung, mit der die Aktivierung der Infrastrukturverschlüsselung für ein Speicherkonto erforderlich ist. Weitere Informationen finden Sie in [Integrierte Azure Policy-Richtliniendefinitionen](../../governance/policy/samples/built-in-policies.md#storage) im Abschnitt **Speicher**.

## <a name="create-an-encryption-scope-with-infrastructure-encryption-enabled"></a>Erstellen eines Verschlüsselungsbereichs mit aktivierter Infrastrukturverschlüsselung

Wenn die Infrastrukturverschlüsselung für ein Konto aktiviert ist, verwendet jeder für dieses Konto erstellte Verschlüsselungsbereich automatisch die Infrastrukturverschlüsselung. Wenn die Infrastrukturverschlüsselung auf Kontoebene nicht aktiviert ist, haben Sie die Möglichkeit, sie zum Erstellungszeitpunkt des entsprechenden Verschlüsselungsbereichs zu aktivieren. Die Infrastrukturverschlüsselungseinstellung für einen Verschlüsselungsbereich kann nach dem Erstellen des Bereichs nicht mehr geändert werden. Weitere Informationen finden Sie unter [Erstellen eines Verschlüsselungsbereichs](../blobs/encryption-scope-manage.md#create-an-encryption-scope).

## <a name="next-steps"></a>Nächste Schritte

- [Azure Storage encryption for data at rest (Azure Storage-Verschlüsselung für ruhende Daten)](storage-service-encryption.md)
- [Kundenseitig verwaltete Schlüssel für die Azure Storage-Verschlüsselung](customer-managed-keys-overview.md)
- [Verschlüsselungsbereiche für Blobspeicher](../blobs/encryption-scope-overview.md)
