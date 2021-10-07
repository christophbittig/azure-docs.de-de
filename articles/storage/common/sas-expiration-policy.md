---
title: Erstellen einer Ablaufrichtlinie für Shared Access Signatures
titleSuffix: Azure Storage
description: Erstellen Sie eine Richtlinie für das Speicherkonto, die die Zeitspanne definiert, für die eine Shared Access Signature (SAS) gültig sein soll. Hier erfahren Sie, wie Sie Richtlinienverstöße überwachen, um Sicherheitsrisiken zu beheben.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 09/14/2021
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: common
ms.openlocfilehash: 8d9a381e80c829acc2f87aa6a856a651b19315ae
ms.sourcegitcommit: 3ef5a4eed1c98ce76739cfcd114d492ff284305b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128709071"
---
# <a name="create-an-expiration-policy-for-shared-access-signatures"></a>Erstellen einer Ablaufrichtlinie für Shared Access Signatures

Sie können eine Shared Access Signature (SAS) verwenden, um den Zugriff auf Ressourcen in Ihrem Azure Storage-Konto zu delegieren. Ein SAS-Token enthält die Zielressource, die gewährten Berechtigungen und das Intervall, über das der Zugriff zulässig ist. Gemäß bewährten Methoden wird empfohlen, das Intervall für eine SAS für den Fall zu beschränken, dass sie kompromittiert wird. Indem Sie eine SAS-Ablaufrichtlinie für Ihre Speicherkonten festlegen, können Sie eine empfohlene Obergrenze für den Ablauf angeben, wenn ein Benutzer eine SAS erstellt.

Eine SAS-Ablaufrichtlinie verhindert nicht, dass ein Benutzer eine SAS mit einem Ablauf erstellt, der den von der Richtlinie empfohlenen Grenzwert überschreitet. Wenn ein Benutzer eine SAS erstellt, die gegen die Richtlinie verstößt, wird eine Warnung zusammen mit dem empfohlenen maximalen Intervall angezeigt. Wenn Sie eine Diagnoseeinstellung für die Protokollierung mit Azure Monitor konfiguriert haben, schreibt Azure Storage in eine Eigenschaft in den Protokollen, wenn ein Benutzer eine SAS erstellt, die nach dem empfohlenen Intervall abläuft.

Weitere Informationen zu SAS (Shared Access Signatures) finden Sie unter [Gewähren von eingeschränktem Zugriff auf Azure Storage-Ressourcen mithilfe von SAS (Shared Access Signature)](storage-sas-overview.md).

## <a name="create-a-sas-expiration-policy"></a>Erstellen einer Richtlinie für den SAS-Ablauf

Wenn Sie eine SAS-Ablaufrichtlinie für ein Speicherkonto erstellen, gilt die Richtlinie für jeden SAS-Typ, den Sie für dieses Speicherkonto erstellen können, einschließlich einer Dienst-SAS, einer SAS für die Benutzerdelegierung oder einer Konto-SAS.

Verwenden Sie zum Konfigurieren einer SAS-Ablaufrichtlinie für ein Speicherkonto das Azure-Portal, die PowerShell oder die Azure CLI.

### <a name="azure-portal"></a>[Azure portal](#tab/azure-portal)

Führen Sie die folgenden Schritte aus, um eine SAS-Ablaufrichtlinie im Azure-Portal zu erstellen:

1. Navigieren Sie zum Speicherkonto im Azure-Portal.
1. Klicken Sie unter **Einstellungen** auf **Konfiguration**.
1. Suchen Sie die Einstellung **Empfohlene Obergrenze für das SAS (Shared Access Signature)-Ablaufintervall zulassen**, und legen Sie sie auf **Aktiviert** fest.
1. Geben Sie das empfohlene Intervall für alle neuen Shared Access Signatures an, die für Ressourcen in diesem Speicherkonto erstellt werden.

    :::image type="content" source="media/sas-expiration-policy/configure-sas-expiration-policy-portal.png" alt-text="Screenshot: Konfigurieren einer SAS-Ablaufrichtlinie im Azure-Portal":::

1. Wählen Sie die Schaltfläche **Speichern** aus, um die Änderungen zu speichern.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Verwenden Sie zum Erstellen einer SAS-Ablaufrichtlinie den Befehl [Set-AzStorageAccount,](/powershell/module/az.storage/set-azstorageaccount), und legen Sie dann den `-SasExpirationPeriod`-Parameter auf die Anzahl von Tagen, Stunden, Minuten und Sekunden fest, die ein SAS-Token ab dem Zeitpunkt der SAS-Signatur aktiv sein kann. Die Zeichenfolge, die Sie für den Parameter `-SasExpirationPeriod` angeben, verwendet das folgende Format: `<days>.<hours>:<minutes>:<seconds>`. Wenn die SAS beispielsweise 1 Tag, 12 Stunden, 5 Minuten und 6 Sekunden nach der Signatur ablaufen soll, verwenden Sie die Zeichenfolge `1.12:05:06`.

```powershell
$account = Set-AzStorageAccount -ResourceGroupName <resource-group> `
    -Name <storage-account-name> `
    -SasExpirationPeriod <days>.<hours>:<minutes>:<seconds>
```

> [!TIP]
> Sie können die SAS-Ablaufrichtlinie auch beim Erstellen eines Speicherkontos festlegen, indem Sie den Parameter `-SasExpirationPeriod` des Befehls [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) festlegen.

Um zu überprüfen, ob die Richtlinie angewendet wurde, verwenden Sie die Eigenschaft `SasPolicy` von [PSStorageAccount](/dotnet/api/microsoft.azure.commands.management.storage.models.psstorageaccount), die im vorherigen Befehl an die Variable `$account` zurückgegeben wurde. 
  
```powershell
$account.SasPolicy
```

Der SAS-Ablaufzeitraum wird in der Konsolenausgabe angezeigt.

> [!div class="mx-imgBorder"]
> ![SAS-Ablaufzeitraum](./media/storage-sas-expiration-policy/sas-policy-console-output.png)

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Verwenden Sie zum Erstellen einer SAS-Ablaufrichtlinie den Befehl [az storage account update](/cli/azure/storage/account#az_storage_account_update), und legen Sie dann den `--key-exp-days`-Parameter auf die Anzahl von Tagen, Stunden, Minuten und Sekunden fest, die ein SAS-Token ab dem Zeitpunkt der SAS-Signatur aktiv sein kann. Die Zeichenfolge, die Sie für den Parameter `--key-exp-days` angeben, verwendet das folgende Format: `<days>.<hours>:<minutes>:<seconds>`. Wenn die SAS beispielsweise 1 Tag, 12 Stunden, 5 Minuten und 6 Sekunden nach der Signatur ablaufen soll, verwenden Sie die Zeichenfolge `1.12:05:06`.

```azurecli-interactive
az storage account update \
  --name <storage-account> \
  --resource-group <resource-group> \
  --sas-exp <days>.<hours>:<minutes>:<seconds>
```

> [!TIP]
> Sie können die SAS-Ablaufrichtlinie auch beim Erstellen eines Speicherkontos festlegen, indem Sie den Parameter `--key-exp-days` des Befehls [az storage account create](/cli/azure/storage/account#az_storage_account_create) festlegen.

Um zu überprüfen, ob die Richtlinie angewendet wurde, rufen Sie den Befehl [az storage account show](/cli/azure/storage/account#az_storage_account_show) auf, und verwenden Sie die Zeichenfolge `{SasPolicy:sasPolicy}` für den Parameter `-query`.
  
```azurecli-interactive
az storage account show \
  --name <storage-account> \
  --resource-group <resource-group> \
  --query "{SasPolicy:sasPolicy}"
```

Der SAS-Ablaufzeitraum wird in der Konsolenausgabe angezeigt.

```json
{
  "SasPolicy": {
    "expirationAction": "Log",
    "sasExpirationPeriod": "1.12:05:06"
  }
}
```

---

## <a name="query-logs-for-policy-violations"></a>Abfrageprotokolle für Richtlinienverstöße

Erstellen Sie zunächst eine Diagnoseeinstellung, die Protokolle an einen Azure Log Analytics-Arbeitsbereich sendet, um die Erstellung einer SAS zu protokollieren, die über ein längeres Intervall gültig ist, als von der SAS-Ablaufrichtlinie empfohlen. Weitere Informationen finden Sie unter [Senden von Protokollen an Azure Log Analytics](../blobs/monitor-blob-storage.md#send-logs-to-azure-log-analytics).

Verwenden Sie als Nächstes eine Azure Monitor-Protokollabfrage, um zu bestimmen, ob eine SAS abgelaufen ist. Erstellen Sie eine neue Abfrage in Ihrem Log Analytics-Arbeitsbereich, fügen Sie den folgenden Abfragetext hinzu, und klicken Sie auf **Ausführen**.

```kusto
StorageBlobLogs | where SasExpiryStatus startswith "Policy Violated" 
```

## <a name="see-also"></a>Weitere Informationen

- [Gewähren von eingeschränktem Zugriff auf Azure Storage-Ressourcen mithilfe von SAS (Shared Access Signature)](storage-sas-overview.md)
- [Erstellen einer Dienst-SAS](/rest/api/storageservices/create-service-sas)
- [Erstellen einer SAS für die Benutzerdelegierung](/rest/api/storageservices/create-user-delegation-sas)
- [Erstellen einer Konto-SAS](/rest/api/storageservices/create-account-sas)