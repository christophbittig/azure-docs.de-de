---
title: Tutorial zur Rotation von Ressourcen mit zwei Sätzen mit Anmeldeinformationen
description: In diesem Tutorial erfahren Sie, wie Sie die Rotation eines Geheimnisses für Ressourcen automatisieren, bei denen zwei Sätze mit Anmeldeinformationen für die Authentifizierung verwendet werden.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: rotation
ms.service: key-vault
ms.subservice: secrets
ms.topic: tutorial
ms.date: 06/22/2020
ms.author: jalichwa
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 60d99ea9b1add522701a82bbd461e4ef064286aa
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131070449"
---
# <a name="automate-the-rotation-of-a-secret-for-resources-that-have-two-sets-of-authentication-credentials"></a>Automatisieren der Geheimnisrotation für Ressourcen, die über zwei Sätze mit Anmeldeinformationen für die Authentifizierung verfügen

Die beste Möglichkeit zur Authentifizierung bei Azure-Diensten ist die Verwendung einer [verwalteten Identität](../general/authentication.md). Es gibt jedoch einige Szenarien, in denen dies nicht möglich ist. In diesen Fällen werden Zugriffsschlüssel oder Kennwörter verwendet. Sie sollten Zugriffsschlüssel und Kennwörter häufig rotieren.

In diesem Tutorial wird gezeigt, wie Sie die regelmäßige Rotation von Geheimnissen für Datenbanken und Dienste automatisieren, bei denen zwei Sätze mit Anmeldeinformationen für die Authentifizierung verwendet werden. In diesem Tutorial wird veranschaulicht, wie Sie Azure Storage-Kontoschlüssel rotieren, die in Azure Key Vault als Geheimnisse gespeichert sind. Sie verwenden eine Funktion, die über eine Azure Event Grid-Benachrichtigung ausgelöst wird. 

> [!NOTE]
> Speicherkontoschlüssel können in Key Vault automatisch verwaltet werden, wenn Sie Shared Access Signature-Token für den delegierten Zugriff auf das Speicherkonto bereitstellen. Es gibt Dienste, für die Speicherkonto-Verbindungsschlüssel mit Zugriffsschlüsseln erforderlich sind. Für dieses Szenario empfehlen wir diese Lösung.

Hier ist die Rotationslösung dargestellt, die in diesem Tutorial beschrieben wird: 

![Diagramm: Rotationslösung](../media/secrets/rotation-dual/rotation-diagram.png)

Bei dieser Lösung werden von Azure Key Vault individuelle Speicherkonto-Zugriffsschlüssel als Versionen desselben Geheimnisses gespeichert, und für nachfolgende Versionen wird zwischen dem Primär- und dem Sekundärschlüssel gewechselt. Wenn ein Zugriffsschlüssel in der aktuellen Version des Geheimnisses gespeichert wird, wird der Alternativschlüssel erneut generiert und für Key Vault als die neue und aktuelle Version des Geheimnisses hinzugefügt. Die Lösung ermöglicht den gesamten Rotationszyklus für die Anwendung, um die Aktualisierung auf den aktuellsten erneut generierten Schlüssel durchzuführen. 

1. 30 Tage vor dem Ablaufdatum eines Geheimnisses veröffentlicht Key Vault das Ereignis für den baldigen Ablauf in Event Grid.
1. Event Grid überprüft die Ereignisabonnements und ruft per HTTP POST den Funktions-App-Endpunkt auf, der dieses Ereignis abonniert hat.
1. Die Funktions-App identifiziert den Alternativschlüssel (nicht den aktuellen) und ruft das Speicherkonto für die erneute Generierung auf.
1. Die Funktions-App fügt den erneut generierten Schlüssel Azure Key Vault als neue Version des Geheimnisses hinzu.

## <a name="prerequisites"></a>Voraussetzungen
* Ein Azure-Abonnement. [Erstellen Sie ein kostenloses Abonnement.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Azure [Cloud Shell](https://shell.azure.com/). Dieses Tutorial verwendet die Cloud Shell im Azure-Portal mit der PowerShell-Umgebung.
* Azure Key Vault:
* Zwei Azure-Speicherkonten.

Sie können den folgenden Bereitstellungslink verwenden, falls Sie nicht über einen vorhandenen Schlüsseltresor und vorhandene Speicherkonten verfügen:

[![Link für die Bereitstellung in Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2FARM-Templates%2FInitial-Setup%2Fazuredeploy.json)

1. Wählen Sie unter **Ressourcengruppe** die Option **Neu erstellen** aus. Geben Sie der Gruppe den Namen **vaultrotation**, und wählen Sie dann **OK** aus.
1. Klicken Sie auf **Überprüfen + erstellen**.
1. Klicken Sie auf **Erstellen**.

    ![Screenshot: Erstellung einer Ressourcengruppe](../media/secrets/rotation-dual/dual-rotation-1.png)

Sie verfügen jetzt über einen Schlüsseltresor und zwei Speicherkonten. Sie können dieses Setup in der Azure-Befehlszeilenschnittstelle oder in Azure PowerShell überprüfen, indem Sie diesen Befehl ausführen:
# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)
```azurecli
az resource list -o table -g vaultrotation
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Get-AzResource -Name 'vaultrotation*' | Format-Table
```
---

Das Ergebnis sieht in etwa wie diese Ausgabe aus:

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
vaultrotation-kv         vaultrotation      westus      Microsoft.KeyVault/vaults
vaultrotationstorage     vaultrotation      westus      Microsoft.Storage/storageAccounts
vaultrotationstorage2    vaultrotation      westus      Microsoft.Storage/storageAccounts
```

## <a name="create-and-deploy-the-key-rotation-function"></a>Erstellen und Bereitstellen der Funktion für die Schlüsselrotation

Als Nächstes erstellen Sie zusätzlich zu anderen erforderlichen Komponenten eine Funktions-App mit einer systemseitig verwalteten Identität. Außerdem stellen Sie die Rotationsfunktion für die Speicherkontoschlüssel bereit.

Für die Rotationsfunktion der Funktions-App werden die folgenden Komponenten und Konfigurationseinstellungen benötigt:
- Einen Azure App Service-Plan
- Ein Speicherkonto zum Verwalten der Trigger der Funktions-App
- Eine Zugriffsrichtlinie zum Zugreifen auf Geheimnisse in Key Vault
- Eine Zuweisung der Dienstrolle „Speicherkonto-Schlüsseloperator“ zur Funktions-App, um den Zugriff auf die Zugriffsschlüssel des Speicherkontos zu ermöglichen
- Eine Funktion für die Schlüsselrotation mit einem Ereignisauslöser und einem HTTP-Trigger (bedarfsgesteuerte Rotation)
- Ein Event Grid-Ereignisabonnement für das Ereignis **SecretNearExpiry**

1. Wählen Sie den Link zur Bereitstellung der Vorlage in Azure aus: 

   [![Link zur Bereitstellung der Vorlage in Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2FARM-Templates%2FFunction%2Fazuredeploy.json)

1. Wählen Sie in der Liste **Ressourcengruppe** die Option **vaultrotation** aus.
1. Geben Sie im Feld **Storage Account RG** (RG des Speicherkontos) den Namen der Ressourcengruppe ein, in der Ihr Speicherkonto enthalten ist. Übernehmen Sie den Standardwert **[resourceGroup().name]** , falls sich Ihr Speicherkonto bereits in der Ressourcengruppe befindet, in der Sie die Funktion für die Schlüsselrotation bereitstellen möchten.
1. Geben Sie im Feld **Name des Speicherkontos** den Namen des Speicherkontos ein, das die zu rotierenden Zugriffsschlüssel enthält. Behalten Sie den Standardwert **[concat(resourceGroup().name, 'storage')]** bei, wenn Sie ein Speicherkonto verwenden, das in [Voraussetzungen](#prerequisites) erstellt wurde.
1. Geben Sie im Feld **Key Vault RG** (RG des Schlüsseltresors) den Namen der Ressourcengruppe ein, in der sich Ihr Schlüsseltresor befindet. Übernehmen Sie den Standardwert **[resourceGroup().name]** , falls sich Ihr Schlüsseltresor bereits in der Ressourcengruppe befindet, in der Sie die Funktion für die Schlüsselrotation bereitstellen möchten.
1. Geben Sie im Feld **Name des Schlüsseltresors** den Namen des Schlüsseltresors ein. Behalten Sie den Standardwert **[concat(resourceGroup().name, '-kv')]** bei, wenn Sie einen Schlüsseltresor verwenden, der in [Voraussetzungen](#prerequisites) erstellt wurde.
1. Wählen Sie im Feld **App Service-Plantyp** den Hostingplan aus. Der **Premium-Plan** wird nur benötigt, wenn sich Ihr Schlüsseltresor hinter einer Firewall befindet.
1. Geben Sie im Feld **Name der Funktions-App** den Namen der Funktions-App ein.
1. Geben Sie im Feld **Name des Geheimnisses** den Namen des Geheimnisses ein, unter dem Sie die Zugriffsschlüssel speichern möchten.
1. Geben Sie im Feld **Repository-URL** den GitHub-Speicherort des Funktionscodes ein. Für dieses Tutorial können Sie **https://github.com/Azure-Samples/KeyVault-Rotation-StorageAccountKey-PowerShell.git** verwenden.
1. Klicken Sie auf **Überprüfen + erstellen**.
1. Klicken Sie auf **Erstellen**.

   ![Screenshot: Erstellen und Bereitstellen der Funktion](../media/secrets/rotation-dual/dual-rotation-2.png)

Nachdem Sie die obigen Schritte ausgeführt haben, verfügen Sie über ein Speicherkonto, eine Serverfarm, eine Funktions-App und Application Insights. Nach Abschluss der Bereitstellung wird die folgende Seite angezeigt:

   ![Screenshot: Seite „Ihre Bereitstellung wurde abgeschlossen“](../media/secrets/rotation-dual/dual-rotation-3.png)
> [!NOTE]
> Wenn ein Fehler auftritt, können Sie die Option **Erneut bereitstellen** auswählen, um die Bereitstellung der Komponenten abzuschließen.


Bereitstellungsvorlagen und Code für die Rotationsfunktion finden Sie in [Azure-Beispielen](https://github.com/Azure-Samples/KeyVault-Rotation-StorageAccountKey-PowerShell).

## <a name="add-the-storage-account-access-keys-to-key-vault"></a>Hinzufügen der Speicherkonto-Zugriffsschlüssel zu Key Vault

Legen Sie zuerst Ihre Zugriffsrichtlinie so fest, dass für Ihren Benutzerprinzipal Berechtigungen zum **Verwalten von Geheimnissen** gewährt werden:
# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)
```azurecli
az keyvault set-policy --upn <email-address-of-user> --name vaultrotation-kv --secret-permissions set delete get list
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Set-AzKeyVaultAccessPolicy -UserPrincipalName <email-address-of-user> --name vaultrotation-kv -PermissionsToSecrets set,delete,get,list
```
---

Sie können jetzt ein neues Geheimnis mit einem Speicherkonto-Zugriffsschlüssel als Wert erstellen. Darüber hinaus benötigen Sie die Ressourcen-ID des Speicherkontos, die Gültigkeitsdauer des Geheimnisses und die Schlüssel-ID zum Hinzufügen zum Geheimnis, damit der Schlüssel von der Rotationsfunktion im Speicherkonto erneut generiert werden kann.

Ermitteln Sie die Ressourcen-ID des Speicherkontos. Sie finden diesen Wert in der `id`-Eigenschaft.

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)
```azurecli
az storage account show -n vaultrotationstorage
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Get-AzStorageAccount -Name vaultrotationstorage -ResourceGroupName vaultrotation | Select-Object -Property *
```
---

Listen Sie die Speicherkonto-Zugriffsschlüssel auf, damit Sie die Schlüsselwerte abrufen können:
# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)
```azurecli
az storage account keys list -n vaultrotationstorage
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Get-AzStorageAccountKey -Name vaultrotationstorage -ResourceGroupName vaultrotation
```
---

Fügen Sie dem Schlüsseltresor ein Geheimnis mit einem auf morgen festgelegten Ablaufdatum, einer Gültigkeitsdauer von 60 Tagen und einer Ressourcen-ID für das Speicherkonto hinzu. Führen Sie diesen Befehl aus, indem Sie Ihre abgerufenen Werte für `key1Value` und `storageAccountResourceId` verwenden:

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)
```azurecli
$tomorrowDate = (get-date).AddDays(+1).ToString("yyyy-MM-ddTHH:mm:ssZ")
az keyvault secret set --name storageKey --vault-name vaultrotation-kv --value <key1Value> --tags "CredentialId=key1" "ProviderAddress=<storageAccountResourceId>" "ValidityPeriodDays=60" --expires $tomorrowDate
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
$tomorrowDate = (Get-Date).AddDays(+1).ToString('yyy-MM-ddTHH:mm:ssZ')
$secretVaule = ConvertTo-SecureString -String '<key1Value>' -AsPlainText -Force
$tags = @{
    CredentialId='key1'
    ProviderAddress='<storageAccountResourceId>'
    ValidityPeriodDays='60'
}
Set-AzKeyVaultSecret -Name storageKey -VaultName vaultrotation-kv -SecretValue $secretVaule -Tag $tags -Expires $tomorrowDate
```
---

Das obige Geheimnis löst das `SecretNearExpiry`-Ereignis innerhalb weniger Minuten aus. Dieses Ereignis löst wiederum die Funktion aus, um den geheimen Schlüssel mit dem Ablaufdatum zu rotieren, das auf 60 Tage festgelegt ist. In dieser Konfiguration würde das Ereignis „SecretNearExpiry“ alle 30 Tage (30 Tage vor Ablauf) ausgelöst werden und die Rotationsfunktion würde abwechselnd zwischen Key1 und Key2 rotieren.

Sie können überprüfen, ob Zugriffsschlüssel erneut generiert wurden, indem Sie den Speicherkontoschlüssel und das Key Vault-Geheimnis abrufen und vergleichen.

Verwenden Sie diesen Befehl, um die Informationen zum Geheimnis abzurufen:
# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)
```azurecli
az keyvault secret show --vault-name vaultrotation-kv --name storageKey
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Get-AzKeyVaultSecret -VaultName vaultrotation-kv -Name storageKey -AsPlainText
```
---

Beachten Sie, dass `CredentialId` auf einen anderen `keyName` aktualisiert und `value` erneut generiert wird.

![Screenshot: Ausgabe des Befehls „A Z keyvault secret show“ für das erste Speicherkonto](../media/secrets/rotation-dual/dual-rotation-4.png)

Rufen Sie die Zugriffsschlüssel ab, um die Werte zu vergleichen:
# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)
```azurecli
az storage account keys list -n vaultrotationstorage 
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Get-AzStorageAccountKey -Name vaultrotationstorage -ResourceGroupName vaultrotation
```
---

Beachten Sie, dass `value` des Schlüssels mit dem Geheimnis im Schlüsseltresor übereinstimmt:

![Screenshot: Ausgabe des Befehls „A Z storage account keys list“ für das erste Speicherkonto](../media/secrets/rotation-dual/dual-rotation-5.png)

## <a name="add-storage-accounts-for-rotation"></a>Hinzufügen von Speicherkonten für die Rotation

Sie können dieselbe Funktions-App wiederverwenden, um Schlüssel für mehrere Speicherkonten zu rotieren. 

Zum Hinzufügen von Speicherkontoschlüsseln zu einer vorhandenen Funktion für die Rotation benötigen Sie Folgendes:
- Eine Zuweisung der Dienstrolle „Speicherkonto-Schlüsseloperator“ zur Funktions-App, um den Zugriff auf die Zugriffsschlüssel des Speicherkontos zu ermöglichen
- Ein Event Grid-Ereignisabonnement für das Ereignis **SecretNearExpiry**

1. Wählen Sie den Link zur Bereitstellung der Vorlage in Azure aus: 

   [![Link zur Bereitstellung der Vorlage in Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2FARM-Templates%2FAdd-Event-Subscriptions%2Fazuredeploy.json)

1. Wählen Sie in der Liste **Ressourcengruppe** die Option **vaultrotation** aus.
1. Geben Sie im Feld **Storage Account RG** (RG des Speicherkontos) den Namen der Ressourcengruppe ein, in der Ihr Speicherkonto enthalten ist. Übernehmen Sie den Standardwert **[resourceGroup().name]** , falls sich Ihr Speicherkonto bereits in der Ressourcengruppe befindet, in der Sie die Funktion für die Schlüsselrotation bereitstellen möchten.
1. Geben Sie im Feld **Name des Speicherkontos** den Namen des Speicherkontos ein, das die zu rotierenden Zugriffsschlüssel enthält.
1. Geben Sie im Feld **Key Vault RG** (RG des Schlüsseltresors) den Namen der Ressourcengruppe ein, in der sich Ihr Schlüsseltresor befindet. Übernehmen Sie den Standardwert **[resourceGroup().name]** , falls sich Ihr Schlüsseltresor bereits in der Ressourcengruppe befindet, in der Sie die Funktion für die Schlüsselrotation bereitstellen möchten.
1. Geben Sie im Feld **Name des Schlüsseltresors** den Namen des Schlüsseltresors ein.
1. Geben Sie im Feld **Name der Funktions-App** den Namen der Funktions-App ein.
1. Geben Sie im Feld **Name des Geheimnisses** den Namen des Geheimnisses ein, unter dem Sie die Zugriffsschlüssel speichern möchten.
1. Klicken Sie auf **Überprüfen + erstellen**.
1. Klicken Sie auf **Erstellen**.

   ![Screenshot: Erstellen eines weiteren Speicherkontos](../media/secrets/rotation-dual/dual-rotation-7.png)

### <a name="add-another-storage-account-access-key-to-key-vault"></a>Hinzufügen eines weiteren Speicherkonto-Zugriffsschlüssels zu Key Vault

Ermitteln Sie die Ressourcen-ID des Speicherkontos. Sie finden diesen Wert in der `id`-Eigenschaft.
# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)
```azurecli
az storage account show -n vaultrotationstorage2
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Get-AzStorageAccount -Name vaultrotationstorage -ResourceGroupName vaultrotation | Select-Object -Property *
```
---

Listen Sie die Speicherkonto-Zugriffsschlüssel auf, damit Sie den Wert für „key2“ abrufen können:
# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)
```azurecli
az storage account keys list -n vaultrotationstorage2
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Get-AzStorageAccountKey -Name vaultrotationstorage2 -ResourceGroupName vaultrotation
```
---

Fügen Sie dem Schlüsseltresor ein Geheimnis mit einem auf morgen festgelegten Ablaufdatum, einer Gültigkeitsdauer von 60 Tagen und einer Ressourcen-ID für das Speicherkonto hinzu. Führen Sie diesen Befehl aus, indem Sie Ihre abgerufenen Werte für `key2Value` und `storageAccountResourceId` verwenden:

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)
```azurecli
$tomorrowDate = (Get-Date).AddDays(+1).ToString('yyyy-MM-ddTHH:mm:ssZ')
az keyvault secret set --name storageKey2 --vault-name vaultrotation-kv --value <key2Value> --tags "CredentialId=key2" "ProviderAddress=<storageAccountResourceId>" "ValidityPeriodDays=60" --expires $tomorrowDate
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
$tomorrowDate = (get-date).AddDays(+1).ToString("yyyy-MM-ddTHH:mm:ssZ")
$secretVaule = ConvertTo-SecureString -String '<key1Value>' -AsPlainText -Force
$tags = @{
    CredentialId='key2';
    ProviderAddress='<storageAccountResourceId>';
    ValidityPeriodDays='60'
}
Set-AzKeyVaultSecret -Name storageKey2 -VaultName vaultrotation-kv -SecretValue $secretVaule -Tag $tags -Expires $tomorrowDate
```
---

Verwenden Sie diesen Befehl, um die Informationen zum Geheimnis abzurufen:
# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)
```azurecli
az keyvault secret show --vault-name vaultrotation-kv --name storageKey2
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Get-AzKeyVaultSecret -VaultName vaultrotation-kv -Name storageKey2 -AsPlainText
```
---

Beachten Sie, dass `CredentialId` auf einen anderen `keyName` aktualisiert und `value` erneut generiert wird.

![Screenshot: Ausgabe des Befehls „A Z keyvault secret show“ für das zweite Speicherkonto](../media/secrets/rotation-dual/dual-rotation-8.png)

Rufen Sie die Zugriffsschlüssel ab, um die Werte zu vergleichen:
# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)
```azurecli
az storage account keys list -n vaultrotationstorage 
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Get-AzStorageAccountKey -Name vaultrotationstorage -ResourceGroupName vaultrotation
```
---

Beachten Sie, dass `value` des Schlüssels mit dem Geheimnis im Schlüsseltresor übereinstimmt:

![Screenshot: Ausgabe des Befehls „A Z storage account keys list“ für das zweite Speicherkonto](../media/secrets/rotation-dual/dual-rotation-9.png)

## <a name="key-vault-rotation-functions-for-two-sets-of-credentials"></a>Key Vault-Rotationsfunktionen für zwei Sätze von Anmeldeinformationen

Vorlage für Rotationsfunktionen für zwei Sätze von Anmeldeinformationen und einige sofort einsatzbereite Funktionen:

- [Projektvorlage](https://serverlesslibrary.net/sample/bc72c6c3-bd8f-4b08-89fb-c5720c1f997f)
- [Redis Cache](https://serverlesslibrary.net/sample/0d42ac45-3db2-4383-86d7-3b92d09bc978)
- [Speicherkonto](https://serverlesslibrary.net/sample/0e4e6618-a96e-4026-9e3a-74b8412213a4)
- [Cosmos DB](https://serverlesslibrary.net/sample/bcfaee79-4ced-4a5c-969b-0cc3997f47cc)

> [!NOTE]
> Die oben genannten Rotationsfunktionen werden von einem Mitglied der Community und nicht von Microsoft erstellt. Die Community-Instanz von Azure Functions wird von keinem Microsoft-Supportprogramm oder -Dienst unterstützt und in der vorliegenden Form ohne jegliche Gewährleistung zur Verfügung gestellt.

## <a name="next-steps"></a>Nächste Schritte

- Tutorial: [Geheimnisrotation für einen Satz von Anmeldeinformationen](./tutorial-rotation.md)
- Übersicht: [Überwachen von Key Vault mit Azure Event Grid](../general/event-grid-overview.md)
- Gewusst wie: [Erstellen Ihrer ersten Funktion im Azure-Portal](../../azure-functions/functions-get-started.md)
- Vorgehensweise: [Verwenden von Logic Apps zum Empfangen einer E-Mail bei Statusänderungen von Key Vault-Geheimnissen](../general/event-grid-logicapps.md)
- Referenz: [Azure Event Grid-Ereignisschema für Azure Key Vault](../../event-grid/event-schema-key-vault.md)
