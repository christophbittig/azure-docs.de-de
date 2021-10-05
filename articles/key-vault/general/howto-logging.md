---
title: Aktivieren der Protokollierung in Azure Key Vault
description: Hier erfahren Sie, wie Sie die Protokollierung in Azure Key Vault aktivieren. Dabei werden Informationen in einem von Ihnen angegebenen Speicherkonto in Azure gespeichert.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 10/01/2020
ms.author: mbaldwin
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: c537e9a9dd733d84161e59999ff17b8b0251f623
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/26/2021
ms.locfileid: "129061179"
---
# <a name="enable-key-vault-logging"></a>Aktivieren der Protokollierung in Key Vault

Nachdem Sie einen oder mehrere Schlüsseltresore erstellt haben, möchten Sie vermutlich überwachen, wie, wann und von wem auf die Schlüsseltresore zugegriffen wird. Ausführliche Informationen zum Feature finden Sie unter [Azure Key Vault-Protokollierung](logging.md).

Protokollierte Inhalte

* Alle authentifizierten REST-API-Anforderungen, z. B. auch Anforderungen, die aufgrund von Zugriffsberechtigungen, Systemfehlern oder fehlerhaften Anforderungen nicht erfolgreich sind.
* Vorgänge im Schlüsseltresor selbst, z. B. Erstellung, Löschung und Festlegung von Schlüsseltresor-Zugriffsrichtlinien und Aktualisierung von Schlüsseltresor-Attributen wie Tags.
* Vorgänge mit Schlüsseln und Geheimnissen im Schlüsseltresor, einschließlich:
  * Erstellen, Ändern oder Löschen dieser Schlüssel oder Geheimnisse.
  * Signieren, Verifizieren, Verschlüsseln, Entschlüsseln, Ver- und Entpacken von Schlüsseln, Erhalten von Geheimnissen und Auflisten von Schlüsseln und Geheimnissen (und deren Versionen).
* Bei nicht authentifizierten Anforderungen wird eine 401-Antwort zurückgegeben. Beispiele sind Anforderungen ohne Bearertoken, falsch formatierte oder abgelaufene Anforderungen oder Anforderungen, deren Token ungültig ist.  
* Azure Event Grid-Benachrichtigungsereignisse für die folgenden Bedingungen: abgelaufen, demnächst ablaufend und geänderte Tresorzugriffsrichtlinie (das Ereignis bei einer neuen Version wird nicht protokolliert). Ereignisse werden auch dann protokolliert, wenn im Schlüsseltresor ein Ereignisabonnement erstellt wurde. Weitere Informationen finden Sie unter [Azure Key Vault als Event Grid-Quelle](../../event-grid/event-schema-key-vault.md).

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie Folgendes:

* Vorhandenen Schlüsseltresor, der von Ihnen genutzt wird  
* [Azure Cloud Shell](https://shell.azure.com) mit Bash-Umgebung
* Ausreichend Speicherplatz unter Azure für Ihre Schlüsseltresor-Protokolle

Die Befehle in diesem Artikel sind für [Cloud Shell](https://shell.azure.com) mit Bash als Umgebung formatiert.

## <a name="connect-to-your-key-vault-subscription"></a>Herstellen einer Verbindung zu Ihrem Key Vault-Abonnement

Der erste Schritt für das Einrichten der Schlüsselprotokollierung ist das Herstellen einer Verbindung mit dem Abonnement, das Ihren Schlüsseltresor enthält. Dies ist besonders dann wichtig, wenn Ihrem Konto mehrere Abonnements zugeordnet sind.

Mithilfe der Azure-Befehlszeilenschnittstelle können Sie alle Abonnements anzeigen, indem Sie den Befehl [az account list](/cli/azure/account#az_account_list) ausführen. Stellen Sie dann mit dem Befehl [az account set](/cli/azure/account#az_account_set) eine Verbindung mit einem Abonnement her:

```azurecli-interactive
az account list

az account set --subscription "<subscriptionID>"
```

In Azure PowerShell können Sie Ihre Abonnements zunächst auflisten, indem Sie das Cmdlet [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription) ausführen. Dann können Sie mithilfe des Cmdlets [Set-AzContext](/powershell/module/az.accounts/set-azcontext) eine Verbindung mit einem Abonnement herstellen: 

```powershell-interactive
Get-AzSubscription

Set-AzContext -SubscriptionId "<subscriptionID>"
```

## <a name="create-a-storage-account-for-your-logs"></a>Erstellen eines Speicherkontos für Ihre Protokolle

Sie können zwar ein vorhandenes Speicherkonto für Ihre Protokolle verwenden, aber hier soll ein neues für Key Vault-Protokolle dediziertes Speicherkonto erstellt werden. 

Um die Verwaltung noch weiter zu vereinfachen, verwenden Sie außerdem die gleiche Ressourcengruppe, die auch den Schlüsseltresor enthält. Im [Azure CLI-Schnellstart](quick-create-cli.md) und im [Azure PowerShell-Schnellstart](quick-create-powershell.md) wird diese Ressourcengruppe als **myResourceGroup** (meineRessourcengruppe) bezeichnet. Der Standort ist *eastus*. Ersetzen Sie diese Werte gegebenenfalls durch Ihre eigenen. 

Geben Sie außerdem einen Speicherkontonamen an. Speicherkontonamen müssen eindeutig und zwischen drei und 24 Zeichen lang sein, und sie dürfen nur Zahlen und Kleinbuchstaben enthalten. Abschließend erstellen Sie ein Speicherkonto mit der SKU `Standard_LRS`.

Verwenden Sie in der Azure CLI den Befehl [az storage account create](/cli/azure/storage/account#az_storage_account_create). 

```azurecli-interactive
az storage account create --name "<your-unique-storage-account-name>" -g "myResourceGroup" --sku "Standard_LRS"
```

Verwenden Sie bei Verwendung von Azure PowerShell das Cmdlet [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount). Sie müssen den Speicherort angeben, der dem der Ressourcengruppe entspricht.

```powershell
 New-AzStorageAccount -ResourceGroupName myResourceGroup -Name "<your-unique-storage-account-name>" -Type "Standard_LRS" -Location "eastus"
```

Notieren Sie sich in jedem Fall die ID des Speicherkontos. Bei Verwendung der Azure-Befehlszeilenschnittstelle wird die ID in der Ausgabe zurückgegeben. Wenn Sie Azure PowerShell verwenden, erhalten Sie die ID mithilfe des Cmdlet [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount). Weisen Sie die Ausgabe dann der Variable `$sa` zu. Das Speicherkonto wird dann mit `$sa.id` angezeigt. (Die `$sa.Context`-Eigenschaft wird auch später in diesem Artikel noch verwendet.)

```powershell-interactive
$sa = Get-AzStorageAccount -Name "<your-unique-storage-account-name>" -ResourceGroup "myResourceGroup"
$sa.id
```

Die ID des Speicherkontos weist das folgende Format auf: /subscriptions/*Ihre-Abonnement-ID*/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/ *<Ihr-eindeutiger-Speicherkontoname>* .

> [!NOTE]
> Wenn Sie sich für die Verwendung eines bestehenden Speicherkontos entscheiden, muss dieses das gleiche Abonnement wie Ihr Schlüsseltresor verwenden. Anstelle des klassischen Bereitstellungsmodells muss das Konto das Azure Resource Manager-Bereitstellungsmodell verwenden.

## <a name="obtain-your-key-vault-resource-id"></a>Abrufen der Ressourcen-ID des Schlüsseltresors

Im [CLI-Schnellstart](quick-create-cli.md) bzw. [PowerShell-Schnellstart](quick-create-powershell.md) haben Sie einen Schlüssel mit eindeutigem Namen erstellt. Verwenden Sie diesen Namen in den folgenden Schritten erneut. Wenn Sie sich an den Namen Ihres Schlüsseltresors nicht mehr erinnern, können Sie ihn mit dem Azure CLI-Befehl [az keyvault list](/cli/azure/keyvault#az_keyvault_list) oder dem Azure PowerShell-Cmdlet [Get-AzKeyVault](/powershell/module/az.keyvault/get-azkeyvault) abrufen.

Verwenden Sie den Namen Ihres Schlüsseltresors, um nach seiner Ressourcen-ID zu suchen. Verwenden Sie bei Verwendung der Azure-Befehlszeilenschnittstelle den Befehl [az keyvault show](/cli/azure/keyvault#az_keyvault_show).

```azurecli-interactive
az keyvault show --name "<your-unique-keyvault-name>"
```

In Azure PowerShell verwenden Sie das Cmdlet [Get-AzKeyVault](/powershell/module/az.keyvault/get-azkeyvault).

```powershell-interactive
Get-AzKeyVault -VaultName "<your-unique-keyvault-name>"
```

Die Ressourcen-ID für Ihren Schlüsseltresor weist das folgende Format auf: /subscriptions/ *<Ihre-Abonnement-ID>* /resourceGroups/myResourceGroup/providers/Microsoft.KeyVault/vaults/ *<Ihr-eindeutiger-Schlüsseltresorname>* . Speichern Sie ihn für den nächsten Schritt.

## <a name="enable-logging"></a>Aktivieren der Protokollierung

Sie können die Protokollierung für Key Vault mithilfe der Azure-Befehlszeilenschnittstelle, mit Azure PowerShell oder im Azure-Portal aktivieren.

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

### <a name="azure-cli"></a>Azure CLI

Verwenden Sie den Azure CLI-Befehl [az monitor diagnostic-settings create](/cli/azure/monitor/diagnostic-settings) mit der Speicherkonto-ID und der Ressourcen-ID für den Schlüsseltresor wie folgt:

```azurecli-interactive
az monitor diagnostic-settings create --storage-account "<storage-account-id>" --resource "<key-vault-resource-id>" --name "Key vault logs" --logs '[{"category": "AuditEvent","enabled": true}]' --metrics '[{"category": "AllMetrics","enabled": true}]'
```

Optional können Sie eine Aufbewahrungsrichtlinie für Ihre Protokolle festlegen, mit der ältere Protokolle nach einer angegeben Dauer automatisch gelöscht werden. Sie könnten beispielsweise eine Aufbewahrungsrichtlinie so festlegen, dass alle Protokolle, die älter als 90 Tage sind, automatisch gelöscht werden.

Verwenden Sie den Befehl [az monitor diagnostic-settings update](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_update) mithilfe der Azure CLI. 

```azurecli-interactive
az monitor diagnostic-settings update --name "Key vault retention policy" --resource "<key-vault-resource-id>" --set retentionPolicy.days=90
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Verwenden Sie das Cmdlet [Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting). Legen Sie das `-Enabled`-Flag dabei auf `$true` und die `category` auf `AuditEvent` fest. Dabei handelt es sich um die einzige Kategorie für die Protokollierung in Key Vault:

```powershell-interactive
Set-AzDiagnosticSetting -ResourceId "<key-vault-resource-id>" -StorageAccountId $sa.id -Enabled $true -Category "AuditEvent"
```

Optional können Sie eine Aufbewahrungsrichtlinie für Ihre Protokolle festlegen, mit der ältere Protokolle nach einer angegeben Dauer automatisch gelöscht werden. Sie könnten beispielsweise eine Aufbewahrungsrichtlinie so festlegen, dass alle Protokolle, die älter als 90 Tage sind, automatisch gelöscht werden.

In Azure PowerShell verwenden Sie das Cmdlet [Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting).

```powershell-interactive
Set-AzDiagnosticSetting "<key-vault-resource-id>" -StorageAccountId $sa.id -Enabled $true -Category AuditEvent -RetentionEnabled $true -RetentionInDays 90
```

# <a name="azure-portal"></a>[Azure portal](#tab/azure-portal)

Führen Sie die folgenden Schritte aus, um die Diagnoseeinstellungen im Azure-Portal zu konfigurieren:

1. Wählen Sie im Menü im **Ressourcenbereich** die Option **Diagnoseeinstellungen** aus.

   :::image type="content" source="../media/diagnostics-portal-1.png" alt-text="Screenshot der Auswahl von Diagnoseeinstellungen":::

1. Wählen Sie **+Diagnoseeinstellung hinzufügen**  aus.

    :::image type="content" source="../media/diagnostics-portal-2.png" alt-text="Screenshot des Hinzufügens einer Diagnoseeinstellung":::
 
1. Wählen Sie einen Namen für die Diagnoseeinstellung aus. Wählen Sie **AuditEvent** und **An Log Analytics-Arbeitsbereich senden** aus, um die Protokollierung in Azure Monitor für Key Vault zu konfigurieren. Wählen Sie dann das Abonnement und den Log Analytics-Arbeitsbereich aus, an die Sie Ihre Protokolle senden möchten.

    :::image type="content" source="../media/diagnostics-portal-3.png" alt-text="Screenshot der Optionen für Diagnoseeinstellungen":::

    Wählen Sie andernfalls die Optionen aus, die sich auf die Protokolle beziehen, die Sie auswählen möchten.

1. Nachdem Sie die gewünschten Optionen ausgewählt haben, wählen Sie **Speichern** aus.

    :::image type="content" source="../media/diagnostics-portal-4.png" alt-text="Screenshot der Speicherung der ausgewählten Optionen":::

---

## <a name="access-your-logs"></a>Zugreifen auf Ihre Protokolle

Ihre Key Vault-Protokolle befinden sich im Container *insights-logs-auditevent* in dem von Ihnen angegebenen Speicherkonto. Zur Anzeige der Protokolle müssen Sie Blobs herunterladen.

Rufen Sie zunächst alle Blobs im Container ab.  Verwenden Sie in der Azure CLI den Befehl [az storage blob list](/cli/azure/storage/blob#az_storage_blob_list).

```azurecli-interactive
az storage blob list --account-name "<your-unique-storage-account-name>" --container-name "insights-logs-auditevent"
```

Verwenden Sie bei Azure PowerShell das Cmdlet [Get-AzStorageBlob](/powershell/module/az.storage/get-azstorageblob). Geben Sie Folgendes ein, um alle Blobs in diesem Container aufzulisten:

```powershell
Get-AzStorageBlob -Container "insights-logs-auditevent" -Context $sa.Context
```

An der Ausgabe des Azure CLI-Befehls oder des Azure PowerShell-Cmdlets können Sie erkennen, dass die Blobnamen das folgende Format aufweisen: `resourceId=<ARM resource ID>/y=<year>/m=<month>/d=<day of month>/h=<hour>/m=<minute>/filename.json`. Für die Datums- und Zeitwerte wird die koordinierte Weltzeit (UTC) verwendet.

Da dasselbe Speicherkonto zum Erfassen von Protokollen für mehrere Ressourcen verwendet werden kann, ist die vollständige Ressourcen-ID im Blobnamen sehr hilfreich, um nur auf die benötigten Blobs zuzugreifen bzw. diese herunterzuladen.

Laden Sie zunächst alle Blobs herunter. Wenn Sie die Azure-Befehlszeilenschnittstelle verwenden, führen Sie den Befehl [az storage blob download](/cli/azure/storage/blob#az_storage_blob_download) aus. Übergeben Sie dabei die Blobnamen sowie den Pfad zur Datei, in der die Ergebnisse gespeichert werden sollen.

```azurecli-interactive
az storage blob download --container-name "insights-logs-auditevent" --file <path-to-file> --name "<blob-name>" --account-name "<your-unique-storage-account-name>"
```

In Azure PowerShell können Sie das Cmdlet [Get-AzStorageBlobs](/powershell/module/az.storage/get-azstorageblob) ausführen, um eine Liste aller Blobs abzurufen. Übergeben Sie diese Liste dann über eine Pipeline an das Cmdlet [Get-AzStorageBlobContent](/powershell/module/az.storage/get-azstorageblobcontent), um die Protokolle in den von Ihnen ausgewählten Pfad herunterzuladen.

```powershell-interactive
$blobs = Get-AzStorageBlob -Container "insights-logs-auditevent" -Context $sa.Context | Get-AzStorageBlobContent -Destination "<path-to-file>"
```

Beim Ausführen dieses zweiten Cmdlet in PowerShell wird mit dem Trennzeichen `/` in den Blobnamen eine vollständige Ordnerstruktur unter dem Zielordner erstellt. Sie verwenden diese Struktur, um die Blobs herunterzuladen und als Dateien zu speichern.

Verwenden Sie Platzhalter, um Blobs selektiv herunterzuladen. Beispiel:

* Bei Verwendung mehrerer Schlüsseltresore und einem Download von Protokollen nur für einen Schlüsseltresor mit dem Namen CONTOSOKEYVAULT3:

  ```powershell
  Get-AzStorageBlob -Container "insights-logs-auditevent" -Context $sa.Context -Blob '*/VAULTS/CONTOSOKEYVAULT3
  ```

* Wenn Sie über mehrere Ressourcengruppen verfügen und nur Protokolle für eine Ressourcengruppe herunterladen möchten, verwenden Sie `-Blob '*/RESOURCEGROUPS/<resource group name>/*'`:

  ```powershell
  Get-AzStorageBlob -Container "insights-logs-auditevent" -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'
  ```

* Wenn Sie alle Protokolle für den Monat Januar 2019 herunterladen möchten, verwenden Sie `-Blob '*/year=2019/m=01/*'`:

  ```powershell
  Get-AzStorageBlob -Container "insights-logs-auditevent" -Context $sa.Context -Blob '*/year=2016/m=01/*'
  ```

## <a name="use-azure-monitor-logs"></a>Verwenden von Azure Monitor-Protokollen

Sie können die Key Vault-Lösung in Azure Monitor verwenden, um `AuditEvent`-Protokolle von Key Vault zu überprüfen. In Azure Monitor-Protokollen verwenden Sie Protokollabfragen, um Daten zu analysieren und die benötigten Informationen zu erhalten.  Weitere Informationen finden Sie unter [Überwachen einer Key Vault-Instanz](monitor-key-vault.md).

## <a name="next-steps"></a>Nächste Schritte

- Konzeptionelle Informationen auch zur Interpretation von Protokollen in Key Vault finden Sie unter [Key Vault-Protokollierung](logging.md).
- Weitere Informationen zur Verwendung von Azure Monitor in Ihrem Schlüsseltresor finden Sie unter [Überwachen einer Key Vault-Instanz](monitor-key-vault.md).
