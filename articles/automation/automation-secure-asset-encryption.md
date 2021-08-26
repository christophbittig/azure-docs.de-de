---
title: Verschlüsselung sicherer Ressourcen in Azure Automation
description: Dieser Artikel enthält Konzepte zum Konfigurieren des Automation-Kontos für die Verwendung der Verschlüsselung.
services: automation
ms.service: automation
ms.subservice: process-automation
author: snehithm
ms.author: snmuvva
ms.date: 07/27/2021
ms.topic: conceptual
manager: kmadnani
ms.openlocfilehash: 82f64ec617972b4e280e2b383c51829f648a3bef
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122339256"
---
# <a name="encryption-of-secure-assets-in-azure-automation"></a>Verschlüsselung sicherer Ressourcen in Azure Automation

Zu den sicheren Objekten in Azure Automation gehören Anmeldeinformationen, Zertifikate, Verbindungen und verschlüsselte Variablen. Diese Ressourcen werden in Azure Automation mithilfe mehrerer Verschlüsselungsstufen geschützt. Basierend auf dem Schlüssel der obersten Ebene, der für die Verschlüsselung verwendet wird, gibt es zwei Modelle für die Verschlüsselung:

- Verwendung der von Microsoft verwalteten Schlüssel
- Verwenden von Schlüsseln, die Sie verwalten

## <a name="microsoft-managed-keys"></a>Von Microsoft verwaltete Schlüssel

Standardmäßig verwendet Ihr Azure Automation-Konto von Microsoft verwaltete Schlüssel.

Jede sichere Ressource wird in Azure Automation mit einem eindeutigen Schlüssel (Datenverschlüsselungsschlüssel), der für jedes Automation-Konto generiert wird, verschlüsselt und gespeichert. Diese Schlüssel selbst werden mithilfe eines anderen eindeutigen Schlüssels, der für jedes Konto generiert wird, verschlüsselt und in Azure Automation gespeichert. Dieser Schlüssel wird als Kontoverschlüsselungsschlüssel (Account Encryption Key, AEK) bezeichnet. Diese Kontoverschlüsselungsschlüssel werden mithilfe von von Microsoft verwalteten Schlüsseln in Azure Automation verschlüsselt und gespeichert.

## <a name="keys-that-you-manage-with-key-vault"></a>Schlüssel, die Sie mit Key Vault verwalten

Sie können die Verschlüsselung sicherer Ressourcen für Ihr Automation-Konto mit Ihren eigenen Schlüsseln verwalten. Wenn Sie einen kundenseitig verwalteten Schlüssel auf der Ebene des Automation-Kontos angeben, wird dieser Schlüssel zum Schutz und zur Kontrolle des Zugriffs auf den Kontoverschlüsselungsschlüssel für das Automation-Konto verwendet. Dieser wiederum wird zum Ver- und Entschlüsseln aller sicheren Ressourcen verwendet. Vom Kunden verwaltete Schlüssel bieten größere Flexibilität beim Erstellen, Rotieren, Deaktivieren und Widerrufen von Zugriffssteuerungen. Sie können auch die zum Schutz Ihrer sicheren Ressourcen verwendeten Verschlüsselungsschlüssel überwachen.

Verwenden Sie Azure Key Vault, um kundenseitig verwaltete Schlüssel zu speichern. Sie können entweder Ihre eigenen Schlüssel erstellen und in einem Schlüsseltresor speichern oder mit den Azure Key Vault-APIs Schlüssel generieren. 

Das Aktivieren der Azure Firewall für [Azure Key Vault](../key-vault/general/network-security.md) blockiert den Zugriff von Azure Automation-Runbooks für diesen Dienst. Der Zugriff wird auch dann blockiert, wenn die Firewallausnahme zum Zulassen vertrauenswürdiger Microsoft-Dienste aktiviert ist, da Automation nicht zur Liste der vertrauenswürdigen Dienste gehört. Bei aktivierter Firewall kann der Zugriff nur mithilfe eines Hybrid Runbook Workers und eines [VNT-Dienstendpunkts](../key-vault/general/overview-vnet-service-endpoints.md) erfolgen.

Weitere Informationen zum Azure-Schlüsseltresor finden Sie unter [Was ist der Azure-Schlüsseltresor?](../key-vault/general/overview.md)

## <a name="use-of-customer-managed-keys-for-an-automation-account"></a>Verwenden von kundenseitig verwalteten Schlüsseln für ein Automation-Konto

Wenn Sie die Verschlüsselung mit kundenseitig verwalteten Schlüsseln für ein Automation-Konto verwenden, wird der Kontoverschlüsselungsschlüssel von Azure Automation mit dem kundenseitig verwalteten Schlüssel im zugeordneten Schlüsseltresor umschlossen. Das Aktivieren von kundenseitig verwalteten Schlüsseln wirkt sich nicht auf die Leistung aus. Das Konto wird ohne Verzögerung sofort mit dem neuen Schlüssel verschlüsselt.

Ein neues Automation-Konto wird immer mit von Microsoft verwalteten Schlüsseln verschlüsselt. Es ist nicht möglich, zum Zeitpunkt der Kontoerstellung vom Kunden verwaltete Schlüssel zu aktivieren. Kundenseitig verwaltete Schlüssel werden in Azure Key Vault gespeichert. Für den Schlüsseltresor müssen Zugriffsrichtlinien bereitgestellt werden, mit denen Schlüsselberechtigungen für die verwaltete Identität erteilt werden, die dem Automation-Konto zugeordnet ist. Die verwaltete Identität ist erst verfügbar, nachdem das Speicherkonto erstellt wurde.

Wenn Sie den für die Verschlüsselung sicherer Azure Automation-Ressourcen verwendeten Schlüssel ändern, indem Sie kundenseitig verwaltete Schlüssel aktivieren oder deaktivieren, die Schlüsselversion aktualisieren oder einen anderen Schlüssel angeben, wird die Verschlüsselung des Kontoverschlüsselungsschlüssels zwar geändert, aber die sicheren Ressourcen in Ihrem Azure Automation-Konto müssen nicht erneut verschlüsselt werden.

> [!NOTE]
> Um kundenseitig verwaltete Schlüssel mithilfe von Azure Automation REST-API-Aufrufen zu aktivieren, müssen Sie die API-Version 2020-01-13-preview verwenden.

## <a name="prerequisites-for-using-customer-managed-keys-in-azure-automation"></a>Voraussetzungen für die Verwendung kundenseitig verwalteter Schlüssel in Azure Automation

Vor dem Aktivieren kundenseitig verwalteter Schlüssel für ein Automation-Konto müssen Sie sicherstellen, dass die folgenden Voraussetzungen erfüllt sind:

- Ein [Azure Key Vault](../key-vault/general/basic-concepts.md) mit aktivierten Eigenschaften **Vorläufiges Löschen** und **Nicht bereinigen**. Diese Eigenschaften sind erforderlich, um die Wiederherstellung von Schlüsseln bei versehentlicher Löschung zu ermöglichen.
- Für die Azure Automation-Verschlüsselung werden nur RSA-Schlüssel unterstützt. Weitere Informationen zu Schlüsseln finden Sie unter [Informationen zu Schlüsseln, Geheimnissen und Zertifikaten in Azure Key Vault](../key-vault/general/about-keys-secrets-certificates.md).
- Das Automation-Konto und der Schlüsseltresor können sich in verschiedenen Abonnements befinden, müssen aber im selben Azure Active Directory-Mandanten sein.
- Überprüfen Sie bei Verwendung von PowerShell, ob das [PowerShell-Modul „Azure Az“](/powershell/azure/new-azureps-module-az) installiert ist. Informationen zur einer Installation oder einem Upgrade finden Sie unter [Installieren des PowerShell-Moduls „Azure Az“](/powershell/azure/install-az-ps).

## <a name="generate-and-assign-a-new-system-assigned-identity-for-an-automation-account"></a>Generieren und Zuweisen einer neuen systemseitig zugewiesenen Identität für ein Automation-Konto

Wenn Sie kundenseitig verwaltete Schlüssel mit einem Automation-Konto verwenden möchten, muss sich Ihr Automation-Konto bei dem Schlüsseltresor authentifizieren, der kundenseitig verwaltete Schlüssel speichert. Azure Automation verwendet systemseitig zugewiesene verwaltete Identitäten, um das Konto bei Azure Key Vault zu authentifizieren. Weitere Informationen zu verwalteten Identitäten finden Sie unter [Was sind verwaltete Identitäten für Azure-Ressourcen?](../active-directory/managed-identities-azure-resources/overview.md).

### <a name="using-powershell"></a>PowerShell

Verwenden Sie das PowerShell-Cmdlet [Set-AzAutomationAccount](/powershell/module/az.automation/set-azautomationaccount), um ein vorhandenes Azure Automation-Konto zu ändern. Der Parameter `-AssignSystemIdentity` generiert eine neue systemseitig zugewiesene Identität für das Automation-Konto und weist diese zu, um sie mit anderen Diensten wie Azure Key Vault zu verwenden. Weitere Informationen finden Sie unter [Was sind verwaltete Identitäten für Azure-Ressourcen?](../active-directory/managed-identities-azure-resources/overview.md) und [Informationen zu Azure Key Vault](../key-vault/general/overview.md). Führen Sie den folgenden Code aus:

```powershell
# Revise variables with your actual values.
$resourceGroup = "ResourceGroupName"
$automationAccount = "AutomationAccountName"
$vaultName = "KeyVaultName"
$keyName = "KeyName"

Set-AzAutomationAccount `
    -ResourceGroupName $resourceGroup `
    -Name $automationAccount `
    -AssignSystemIdentity
```

Die Ausgabe sollte in etwa wie folgt aussehen:

:::image type="content" source="./media/automation-secure-asset-encryption/set-azautomationaccount.png" alt-text="Ausgabe des Cmdlets „Set-AzAutomationAccount“.":::

Ermitteln Sie die `PrincipalId` zur späteren Verwendung. Führen Sie den folgenden Code aus:

```powershell
$principalID = (Get-AzAutomationAccount `
    -ResourceGroupName $resourceGroup `
    -Name $automationAccount).Identity.PrincipalId

$principalID
```

### <a name="using-rest"></a>Verwenden von REST

Konfigurieren einer systemseitig zugewiesenen verwalteten Identität mit dem folgenden REST-API-Aufruf für das Automation-Konto:

```http
PATCH https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name?api-version=2020-01-13-preview
```

Anforderungstext:

```json
{ 
 "identity": 
 { 
  "type": "SystemAssigned" 
  } 
}
```

Die systemseitig zugewiesene Identität für das Automation-Konto wird in einer Antwort ähnlich der folgenden zurückgegeben:

```json
{
 "name": "automation-account-name",
 "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name",
 ..
 "identity": {
    "type": "SystemAssigned",
    "principalId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
    "tenantId": "bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb"
 },
..
}
```

## <a name="configuration-of-the-key-vault-access-policy"></a>Konfiguration der Key Vault-Zugriffsrichtlinie

Nachdem dem Automation-Konto eine systemseitig zugewiesene verwaltete Identität zugewiesen wurde, konfigurieren Sie den Zugriff auf den Schlüsseltresor, in dem kundenseitig verwaltete Schlüssel gespeichert sind. Azure Automation erfordert, dass die Identität über die Vorgangsberechtigungen **Get**, **Recover**, **WrapKey** und **UnwrapKey** verfügt, um auf die kundenseitig verwalteten Schlüssel zuzugreifen.

### <a name="using-powershell"></a>PowerShell

Verwenden Sie das PowerShell-Cmdlet [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy), um die erforderlichen Berechtigungen zu erteilen. Verwenden Sie dann das Cmdlet [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey), um einen Schlüssel im Schlüsseltresor zu erstellen. Führen Sie den folgenden Code aus:

```powershell
Set-AzKeyVaultAccessPolicy `
    -VaultName $vaultName `
    -ObjectId $principalID `
    -PermissionsToKeys Get, Recover, UnwrapKey, WrapKey

Add-AzKeyVaultKey `
    -VaultName $vaultName `
    -Name $keyName `
    -Destination 'Software'
```

Die Ausgabe sollte in etwa wie folgt aussehen:

:::image type="content" source="./media/automation-secure-asset-encryption/add-azkeyvaultkey.png" alt-text="Ausgabe des Cmdlets „Add-AzKeyVaultKey“.":::

### <a name="using-rest"></a>Verwenden von REST

Die Zugriffsrichtlinie kann mit dem folgenden REST-API-Aufruf festgelegt werden:

```http
PUT https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/sample-group/providers/Microsoft.KeyVault/vaults/sample-vault/accessPolicies/add?api-version=2018-02-14
```

Anforderungstext:

```json
{
  "properties": {
    "accessPolicies": [
      {
        "tenantId": "bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb",
        "objectId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
        "permissions": {
          "keys": [
            "get",
            "recover",
            "wrapKey",
            "unwrapKey"
          ],
          "secrets": [],
          "certificates": []
        }
      }
    ]
  }
}
```

> [!NOTE]
> Für die Felder **tenantId** und **objectId** müssen die Werte von **identity.tenantId** und **identity.principalId** aus der Antwort der verwalteten Identität für das Automation-Konto bereitgestellt werden.

## <a name="reconfigure-automation-account-to-use-customer-managed-key"></a>Neukonfigurieren des Automation-Kontos für die Verwendung von kundenseitig verwalteten Schlüsseln

Wenn Sie Ihr Automation-Konto von von Microsoft verwalteten Schlüsseln auf kundenseitig verwaltete Schlüssel umstellen möchten, können Sie diese Änderung mittels Azure PowerShell oder mit einer Azure Resource Manager-Vorlage vornehmen.

### <a name="using-powershell"></a>PowerShell

Verwenden Sie das PowerShell-Cmdlet [Set-AzAutomationAccount](/powershell/module/az.automation/set-azautomationaccount), um das Automation-Konto so neu zu konfigurieren, dass es kundenseitig verwaltete Schlüssel verwendet.

```powershell
$vaultURI = (Get-AzKeyVault -VaultName $vaultName).VaultUri
$keyVersion = (Get-AzKeyVaultKey -VaultName $vaultName -KeyName $keyName).Version

Set-AzAutomationAccount `
    -ResourceGroupName $resourceGroup `
    -Name $automationAccount `
    -AssignSystemIdentity `
    -KeyName $keyName `
    -KeyVaultUri $vaultURI `
    -KeyVersion $keyVersion `
    -KeyVaultEncryption 
```

Sie können die Änderung verifizieren, indem Sie den folgenden Befehl ausführen:

```powershell
(Get-AzAutomationAccount `
    -ResourceGroupName $resourceGroup `
    -Name $automationAccount).Encryption `
    |  ConvertTo-Json 
```

Die Ausgabe sollte in etwa wie folgt aussehen:

:::image type="content" source="./media/automation-secure-asset-encryption/get-azautomationaccount.png" alt-text="Ausgabe des Cmdlets „Get-AzAutomationAccount“.":::

### <a name="using-rest"></a>Verwenden von REST

Verwenden Sie den folgenden REST-API-Aufruf:

```http
PATCH https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name?api-version=2020-01-13-preview
```

Anforderungstext:

```json
{
    "identity": {
    "type": "SystemAssigned"
    },
    "properties": {
        "encryption": {
            "keySource": "Microsoft.Keyvault",
            "keyvaultProperties": {
                "keyName": "sample-vault-key",
                "keyvaultUri": "https://sample-vault-key12.vault.azure.net",
                "keyVersion": "7c73556c521340209371eaf623cc099d"
            }
        }
    }
}
```

Beispiel für eine Antwort

```json
{
  "name": "automation-account-name",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name",
  ..
  "properties": {
    ..
    "encryption": {
      "keyvaultProperties": {
         "keyName": "sample-vault-key",
          "keyvaultUri": "https://sample-vault-key12.vault.azure.net",
          "keyVersion": "7c73556c521340209371eaf623cc099d"
      },
      "keySource": "Microsoft.Keyvault"
    },
    ..
  }
}
```

## <a name="rotation-of-a-customer-managed-key"></a>Rotation eines kundenseitig verwalteten Schlüssels

Sie können einen vom Kunden verwalteten Schlüssel in Azure Key Vault entsprechend Ihren Konformitätsrichtlinien rotieren. Wenn der Schlüssel rotiert wurde, müssen Sie das Automation-Konto aktualisieren, damit der neue Schlüssel-URI verwendet wird.

Durch Rotieren des Schlüssels wird keine Neuverschlüsselung sicherer Ressourcen im Automation-Konto ausgelöst. Es ist keine weitere Aktion erforderlich.

## <a name="revocation-of-access-to-a-customer-managed-key"></a>Widerrufen des Zugriffs auf einen kundenseitig verwalteten Schlüssel

Zum Widerrufen des Zugriffs auf von Kunden verwaltete Schlüssel können Sie PowerShell oder die Azure-Befehlszeilenschnittstelle verwenden. Weitere Informationen finden Sie unter [Azure Key Vault – PowerShell](/powershell/module/az.keyvault/) oder [Azure Key Vault – CLI](/cli/azure/keyvault). Durch das Widerrufen des Zugriffs wird der Zugriff auf alle sicheren Ressourcen im Automation-Konto wirksam blockiert, da Azure Automation keinen Zugriff mehr auf den Verschlüsselungsschlüssel hat.

## <a name="next-steps"></a>Nächste Schritte

- Informationen zu Azure Key Vault finden Sie unter [Was ist Azure Key Vault?](../key-vault/general/overview.md).
- Informationen zum Arbeiten mit Zertifikaten finden Sie unter [Verwalten von Zertifikaten in Azure Automation](shared-resources/certificates.md).
- Informationen zum Behandeln von Anmeldeinformationen finden Sie unter [Verwalten von Anmeldeinformationen in Azure Automation](shared-resources/credentials.md).
- Informationen zum Verwalten von Automation-Variablen finden Sie unter [Verwalten von Variablen in Azure Automation](shared-resources/variables.md).
- Hilfe beim Arbeiten mit Verbindungen finden Sie unter [Verwalten von Verbindungen in Azure Automation](automation-connections.md).
