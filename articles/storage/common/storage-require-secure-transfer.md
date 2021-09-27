---
title: Erzwingen einer sicheren Übertragung für sichere Verbindungen
titleSuffix: Azure Storage
description: Erfahren Sie, wie Sie eine sichere Übertragung für Anforderungen an Azure Storage erzwingen. Wenn Sie eine sichere Übertragung für ein Speicherkonto benötigen, werden alle Anforderungen zurückgewiesen, die von einer unsicheren Verbindung stammen.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 06/01/2021
ms.author: tamram
ms.reviewer: fryu
ms.subservice: common
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 65d877bce0bdcab35248d4b9a41b92f46c132903
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128630715"
---
# <a name="require-secure-transfer-to-ensure-secure-connections"></a>Erzwingen einer sicheren Übertragung für sichere Verbindungen

Sie können Ihr Speicherkonto so konfigurieren, dass nur Anforderungen von sicheren Verbindungen akzeptiert werden, indem Sie die Eigenschaft **Sichere Übertragung erforderlich** für das Speicherkonto festlegen. Wenn Sie eine sichere Übertragung benötigen, werden alle Anforderungen zurückgewiesen, die von einer unsicheren Verbindung stammen. Microsoft empfiehlt, immer eine sichere Übertragung für sämtliche Speicherkonten zu erzwingen.

Wenn eine sichere Übertragung erforderlich ist, müssen Aufrufe von Azure Storage-REST-API-Vorgängen über HTTPS erfolgen. Jede Anforderung über HTTP wird abgelehnt. Die Eigenschaft **Sichere Übertragung erforderlich** ist standardmäßig aktiviert, wenn Sie ein Speicherkonto erstellen.

Azure Policy bietet eine integrierte Richtlinie, um sicherzustellen, dass für Ihre Speicherkonten eine sichere Übertragung erforderlich ist. Weitere Informationen finden Sie in [Integrierte Azure Policy-Richtliniendefinitionen](../../governance/policy/samples/built-in-policies.md#storage) im Abschnitt **Speicher**.

Das Herstellen einer Verbindung mit einer Azure-Dateifreigabe über SMB ohne Verschlüsselung führt zu einem Fehler, wenn für das Speicherkonto eine sichere Übertragung erforderlich ist. Beispiele für unsichere Verbindungen sind Verbindungen, die über SMB 2.1 oder SMB 3.x ohne Verschlüsselung hergestellt werden.

> [!NOTE]
> Da Azure Storage keine Unterstützung von HTTPS für benutzerdefinierte Domänennamen bietet, wird diese Option nicht angewendet, wenn ein benutzerdefinierter Domänenname verwendet wird.
>
> Diese Einstellung für eine sichere Übertragung gilt nicht für TCP. Ungeschützte Verbindungen über NFS 3.0-Protokollunterstützung in Azure Blob Storage mit TCP werden erfolgreich erstellt.

## <a name="require-secure-transfer-in-the-azure-portal"></a>Erzwingen einer sicheren Übertragung im Azure-Portal

Sie können die Einstellung **Sichere Übertragung erforderlich** beim Erstellen eines Speicherkontos im [Azure-Portal](https://portal.azure.com) aktivieren. Sie können sie aber auch für vorhandene Speicherkonten aktivieren.

### <a name="require-secure-transfer-for-a-new-storage-account"></a>Vorschreiben einer sicheren Übertragung für ein neues Speicherkonto

1. Öffnen Sie den Bereich **Speicherkonto erstellen** im Azure-Portal.
1. Aktivieren Sie auf der Seite **Erweitert** das Kontrollkästchen **Sichere Übertragung aktivieren**.

   ![Blatt „Speicherkonto erstellen“](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_1.png)

### <a name="require-secure-transfer-for-an-existing-storage-account"></a>Vorschreiben einer sicheren Übertragung für ein vorhandenes Speicherkonto

1. Wählen Sie ein vorhandenes Speicherkonto im Azure-Portal aus.
1. Wählen Sie im Menübereich des Speicherkontos unter **Einstellungen** die Option **Konfiguration** aus.
1. Wählen Sie unter **Sichere Übertragung erforderlich** die Option **Aktiviert** aus.

   ![Menübereich „Speicherkonto“](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_2.png)

## <a name="require-secure-transfer-from-code"></a>Erzwingen einer sicheren Übertragung in Code

Wenn Sie programmgesteuert eine sichere Übertragung erzwingen möchten, legen Sie die *enableHttpsTrafficOnly*-Eigenschaft für das Speicherkonto auf *True* fest. Sie können diese Eigenschaft mithilfe der Speicherressourcenanbieter-REST-API, Clientbibliotheken oder Tools festlegen:

- [REST-API](/rest/api/storagerp/storageaccounts)
- [PowerShell](/powershell/module/az.storage/set-azstorageaccount)
- [BEFEHLSZEILENSCHNITTSTELLE (CLI)](/cli/azure/storage/account)
- [NodeJS](https://www.npmjs.com/package/@azure/arm-storage/)
- [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage)
- [Python SDK](https://pypi.org/project/azure-mgmt-storage)
- [Ruby SDK](https://rubygems.org/gems/azure_mgmt_storage)

## <a name="require-secure-transfer-with-powershell"></a>Erzwingen einer sicheren Übertragung mit PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Für dieses Beispiel ist Az-Version 0.7 des Azure PowerShell-Moduls oder höher erforderlich. Führen Sie `Get-Module -ListAvailable Az` aus, um die Version zu ermitteln. Wenn Sie eine Installation oder ein Upgrade ausführen müssen, finden Sie unter [Install and configure Azure PowerShell](/powershell/azure/install-Az-ps) (Installieren des Azure PowerShell-Moduls) Informationen dazu.

Führen Sie zum Starten `Connect-AzAccount` aus, um eine Verbindung mit Azure herzustellen.

 Verwenden Sie die folgende Befehlszeile, um die Einstellung zu überprüfen:

```powershell
Get-AzStorageAccount -Name "{StorageAccountName}" -ResourceGroupName "{ResourceGroupName}"
StorageAccountName     : {StorageAccountName}
Kind                   : Storage
EnableHttpsTrafficOnly : False
...

```

Verwenden Sie die folgende Befehlszeile, um die Einstellung zu aktivieren:

```powershell
Set-AzStorageAccount -Name "{StorageAccountName}" -ResourceGroupName "{ResourceGroupName}" -EnableHttpsTrafficOnly $True
StorageAccountName     : {StorageAccountName}
Kind                   : Storage
EnableHttpsTrafficOnly : True
...

```

## <a name="require-secure-transfer-with-azure-cli"></a>Erzwingen einer sicheren Übertragung mit der Azure-Befehlszeilenschnittstelle

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 Verwenden Sie den folgenden Befehl, um die Einstellung zu überprüfen:

```azurecli-interactive
az storage account show -g {ResourceGroupName} -n {StorageAccountName}
{
  "name": "{StorageAccountName}",
  "enableHttpsTrafficOnly": false,
  "type": "Microsoft.Storage/storageAccounts"
  ...
}

```

Verwenden Sie den folgenden Befehl, um die Einstellung zu aktivieren:

```azurecli-interactive
az storage account update -g {ResourceGroupName} -n {StorageAccountName} --https-only true
{
  "name": "{StorageAccountName}",
  "enableHttpsTrafficOnly": true,
  "type": "Microsoft.Storage/storageAccounts"
  ...
}

```

## <a name="next-steps"></a>Nächste Schritte

[Sicherheitsempfehlungen für Blob Storage](../blobs/security-recommendations.md)
