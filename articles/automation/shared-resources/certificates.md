---
title: Verwalten von Zertifikaten in Azure Automation
description: In diesem Artikel erfahren Sie, wie Sie Zertifikate für den Zugriff durch Runbooks und DSC-Konfigurationen verwenden.
services: automation
ms.subservice: shared-capabilities
ms.date: 12/22/2020
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: daed24e27c1dec0a6dec1bf3b12f8290078027b1
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129274783"
---
# <a name="manage-certificates-in-azure-automation"></a>Verwalten von Zertifikaten in Azure Automation

Zertifikate werden in Azure Automation sicher gespeichert, sodass Runbooks oder DSC-Konfigurationen über das Cmdlet [Get-AzAutomationCertificate](/powershell/module/Az.Automation/Get-AzAutomationCertificate) für Azure Resource Manager-Ressourcen darauf zugreifen können. Die sichere Speicherung der Zertifikate ermöglicht es Ihnen, Runbooks und DSC-Konfigurationen zu erstellen, die Zertifikate für die Authentifizierung verwenden, oder diese zu Azure- oder Drittanbieterressourcen hinzuzufügen.

>[!NOTE]
>Zu den sicheren Objekten in Azure Automation gehören Anmeldeinformationen, Zertifikate, Verbindungen und verschlüsselte Variablen. Diese Objekte werden mithilfe eines eindeutigen Schlüssels, der für jedes Automation-Konto generiert wird, verschlüsselt und in Automation gespeichert. Automation speichert den Schlüssel in dem vom System verwalteten Key Vault-Dienst. Vor dem Speichern eines sicheren Objekts lädt Automation den Schlüssel aus Key Vault und verwendet ihn dann zum Verschlüsseln des Objekts.

## <a name="powershell-cmdlets-to-access-certificates"></a>PowerShell-Cmdlets für den Zugriff auf Zertifikate

Über die Cmdlets in der folgenden Tabelle können Sie Zertifikate für Automation mit PowerShell erstellen und verwalten. Diese sind im Lieferumfang der Az-Module enthalten.

|Cmdlet |BESCHREIBUNG|
| --- | ---|
|[Get-AzAutomationCertificate](/powershell/module/Az.Automation/Get-AzAutomationCertificate)|Ruft Informationen über ein Zertifikat zur Verwendung in einem Runbook oder einer DSC-Konfiguration ab. Das Zertifikat selbst können Sie nur über das interne Cmdlet `Get-AutomationCertificate` abrufen.|
|[New-AzAutomationCertificate](/powershell/module/Az.Automation/New-AzAutomationCertificate)|Erstellt ein neues Zertifikat in Automation.|
|[Remove-AzAutomationCertificate](/powershell/module/Az.Automation/Remove-AzAutomationCertificate)|Entfernt ein Zertifikat aus Automation.|
|[Set-AzAutomationCertificate](/powershell/module/Az.Automation/Set-AzAutomationCertificate)|Legt die Eigenschaften für ein vorhandenes Zertifikat fest, lädt die Zertifikatdatei hoch und legt das Kennwort für eine **PFX**-Datei fest.|

Das Cmdlet [Add-AzureCertificate](/powershell/module/servicemanagement/azure.service/add-azurecertificate) kann auch verwendet werden, um ein Dienstzertifikat für den angegebenen Clouddienst hochzuladen.

## <a name="internal-cmdlets-to-access-certificates"></a>Interne Cmdlets für den Zugriff auf Zertifikate

Das interne Cmdlet in der folgenden Tabelle wird für den Zugriff auf Zertifikate in Ihren Runbooks verwendet. Dieses Cmdlet ist im globalen Modul `Orchestrator.AssetManagement.Cmdlets` enthalten. Weitere Informationen finden Sie unter [Interne Cmdlets](modules.md#internal-cmdlets).

| Internes Cmdlet | BESCHREIBUNG |
|:---|:---|
|`Get-AutomationCertificate`|Ruft ein Zertifikat zur Verwendung in einem Runbook oder einer DSC-Konfiguration ab. Gibt ein [System.Security.Cryptography.X509Certificates.X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2)-Objekt zurück.|

> [!NOTE]
> Vermeiden Sie die Verwendung von Variablen im `Name`-Parameter von `Get-AutomationCertificate` in einem Runbook oder einer DSC-Konfiguration. Solche Variablen können die Ermittlung von Abhängigkeiten zwischen Runbooks oder DSC-Konfigurationen und Automation-Variablen zur Entwurfszeit erschweren.

## <a name="python-functions-to-access-certificates"></a>Python-Funktionen für den Zugriff auf Zertifikate

Verwenden Sie die Funktion in der folgenden Tabelle für den Zugriff auf Zertifikate in einem Python 2- und Python 3-Runbook. Python 3-Runbooks sind derzeit als Vorschau verfügbar.

| Funktion | BESCHREIBUNG |
|:---|:---|
| `automationassets.get_automation_certificate` | Ruft Informationen zu einem Zertifikatasset ab. |

> [!NOTE]
> Sie müssen das Modul `automationassets` am Anfang Ihres Python-Runbooks importieren, um auf die Assetfunktionen zugreifen zu können.

## <a name="create-a-new-certificate"></a>Erstellen eines neuen Zertifikats

Wenn Sie ein neues Zertifikat erstellen, laden Sie eine CER- oder PFX-Datei in Automation hoch. Wenn Sie das Zertifikat als exportierbar kennzeichnen, können Sie es aus dem Automation-Zertifikatspeicher übertragen. Ist das Zertifikat nicht exportierbar, können Sie es nur zum Signieren innerhalb des Runbooks oder der DSC-Konfiguration verwenden. Für Automation muss der Zertifikatanbieter **Microsoft Enhanced RSA and AES Cryptographic Provider** lauten.

### <a name="create-a-new-certificate-with-the-azure-portal"></a>Erstellen eines neuen Zertifikats im Azure-Portal

1. Wählen Sie in Ihrem Automation-Konto im linken Bereich **Zertifikate** unter **Freigegebene Ressourcen** aus.
1. Wählen Sie auf der Seite **Zertifikate** die Option **Zertifikat hinzufügen** aus.
1. Geben Sie im Feld **Name** einen Namen für das Zertifikat ein.
1. Um nach einer **CER**- oder **PFX**-Datei zu suchen, wählen Sie unter **Zertifikatdatei hochladen** die Option **Datei auswählen** aus. Wenn Sie eine **PFX**-Datei auswählen, geben Sie ein Kennwort an und legen fest, ob das Zertifikat exportiert werden kann.
1. Wählen Sie **Erstellen** aus, um das neue Zertifikatobjekt zu speichern.

### <a name="create-a-new-certificate-with-powershell"></a>Erstellen eines neuen Zertifikats mit PowerShell

Das folgende Beispiel zeigt, wie Sie ein neues Automation-Zertifikat erstellen und es als exportierbar kennzeichnen. Dieses Beispiel importiert eine vorhandene **PFX**-Datei.

```powershell-interactive
$certificateName = 'MyCertificate'
$PfxCertPath = '.\MyCert.pfx'
$CertificatePassword = ConvertTo-SecureString -String 'P@$$w0rd' -AsPlainText -Force
$ResourceGroup = "ResourceGroup01"

New-AzAutomationCertificate -AutomationAccountName "MyAutomationAccount" -Name $certificateName -Path $PfxCertPath -Password $CertificatePassword -Exportable -ResourceGroupName $ResourceGroup
```

### <a name="create-a-new-certificate-with-a-resource-manager-template"></a>Erstellen eines neuen Zertifikats mit einer Resource Manager-Vorlage

Das folgende Beispiel veranschaulicht, wie Sie in PowerShell mithilfe einer Resource Manager-Vorlage ein Zertifikat in Ihrem Automation-Konto bereitstellen:

```powershell-interactive
$AutomationAccountName = "<automation account name>"
$PfxCertPath = '<PFX cert path and filename>'
$CertificatePassword = '<password>'
$certificateName = '<certificate name>' #A name of your choosing
$ResourceGroupName = '<resource group name>' #The one that holds your automation account
$flags = [System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::Exportable `
    -bor [System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::PersistKeySet `
    -bor [System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::MachineKeySet
# Load the certificate into memory
$PfxCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($PfxCertPath, $CertificatePassword, $flags)
# Export the certificate and convert into base 64 string
$Base64Value = [System.Convert]::ToBase64String($PfxCert.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12))
$Thumbprint = $PfxCert.Thumbprint


$json = @"
{
    '`$schema': 'https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#',
    'contentVersion': '1.0.0.0',
    'resources': [
        {
            'name': '$AutomationAccountName/$certificateName',
            'type': 'Microsoft.Automation/automationAccounts/certificates',
            'apiVersion': '2015-10-31',
            'properties': {
                'base64Value': '$Base64Value',
                'thumbprint': '$Thumbprint',
                'isExportable': true
            }
        }
    ]
}
"@

$json | out-file .\template.json
New-AzResourceGroupDeployment -Name NewCert -ResourceGroupName $ResourceGroupName -TemplateFile .\template.json
```

## <a name="get-a-certificate"></a>Abrufen eines Zertifikats

Verwenden Sie das interne Cmdlet `Get-AutomationCertificate`, um ein Zertifikat abzurufen. Sie können das Cmdlet [Get-AzAutomationCertificate](/powershell/module/Az.Automation/Get-AzAutomationCertificate) nicht verwenden, da dieses Cmdlet Informationen zum Zertifikatobjekt zurückgibt, aber nicht das Zertifikat selbst.

### <a name="textual-runbook-examples"></a>Beispiele für Textrunbooks

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Das folgende Beispiel zeigt, wie Sie ein Zertifikat zu einem Clouddienst in einem Runbook hinzufügen. In diesem Beispiel wird das Kennwort aus einer verschlüsselten Automation-Variable abgerufen.

```powershell-interactive
$serviceName = 'MyCloudService'
$cert = Get-AutomationCertificate -Name 'MyCertificate'
$certPwd = Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
-AutomationAccountName "MyAutomationAccount" -Name 'MyCertPassword'
Add-AzureCertificate -ServiceName $serviceName -CertToDeploy $cert
```

# <a name="python-2"></a>[Python 2](#tab/python2)

Das folgende Beispiel zeigt, wie Sie auf Zertifikate in Python 2-Runbooks zugreifen.

```python
# get a reference to the Azure Automation certificate
cert = automationassets.get_automation_certificate("AzureRunAsCertificate")

# returns the binary cert content  
print cert
```

# <a name="python-3"></a>[Python 3](#tab/python3)

Das folgende Beispiel zeigt, wie Sie auf Zertifikate in Python 3-Runbooks (Vorschau) zugreifen.

```python
# get a reference to the Azure Automation certificate
cert = automationassets.get_automation_certificate("AzureRunAsCertificate")

# returns the binary cert content  
print (cert)
```

---

### <a name="graphical-runbook-example"></a>Beispiel für ein grafisches Runbook

Fügen Sie einem grafischen Runbook eine Aktivität für das interne Cmdlet `Get-AutomationCertificate` hinzu, indem Sie im Bibliotheksbereich mit der rechten Maustaste auf das Zertifikat klicken und anschließend **Zur Canvas hinzufügen** auswählen.

![Screenshot: Hinzufügen eines Zertifikats zur Canvas](../media/certificates/automation-certificate-add-to-canvas.png)

Die folgende Abbildung zeigt ein Beispiel für die Verwendung eines Zertifikats in einem grafischen Runbook.

![Screenshot: Beispiel für die grafische Erstellung](../media/certificates/graphical-runbook-add-certificate.png)

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu den Cmdlets, die für den Zugriff auf Zertifikate verwendet werden, finden Sie unter [Verwalten von Modulen in Azure Automation](modules.md).
* Allgemeine Informationen zu Runbooks finden Sie unter [Ausführen von Runbooks in Azure Automation](../automation-runbook-execution.md).
* Ausführliche Informationen zu DSC-Konfigurationen finden Sie in der [Übersicht über die Zustandskonfiguration](../automation-dsc-overview.md).
