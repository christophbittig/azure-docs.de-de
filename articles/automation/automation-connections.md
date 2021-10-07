---
title: Verwalten von Verbindungen in Azure Automation
description: In diesem Artikel wird beschrieben, wie Sie Azure Automation-Verbindungen mit externen Diensten oder Anwendungen verwalten und mit ihnen in Runbooks arbeiten.
services: automation
ms.subservice: shared-capabilities
ms.date: 12/22/2020
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b4e96045ec49a337d317583826cb3f50c6161f39
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129276055"
---
# <a name="manage-connections-in-azure-automation"></a>Verwalten von Verbindungen in Azure Automation

Ein Azure Automation-Verbindungsobjekt enthält die unten aufgeführten Informationen. Diese Informationen sind für die Verbindungsherstellung mit einem externen Dienst oder einer externen Anwendung aus einem Runbook oder einer DSC-Konfiguration erforderlich. 

* Für die Authentifizierung benötigte Informationen, z. B. Benutzername und Kennwort
* Verbindungsinformationen wie URL oder Port

Das Verbindungsobjekt enthält alle Eigenschaften für die Verbindungsherstellung mit einer bestimmten Anwendung, was das Erstellen mehrerer Variablen überflüssig macht. Sie können die Werte für eine Verbindung an einem Ort bearbeiten, und Sie können den Namen einer Verbindung in einem einzelnen Parameter an ein Runbook oder eine DSC-Konfiguration übergeben. Das Runbook oder die Konfiguration greift mithilfe des internen Cmdlets `Get-AutomationConnection` auf die Eigenschaften für eine Verbindung zu.

Wenn Sie eine Verbindung erstellen, müssen Sie einen Verbindungstyp angeben. Der Verbindungstyp ist eine Vorlage, die einen Satz von Eigenschaften definiert. Sie können Azure Automation mithilfe eines Integrationsmoduls mit einer Metadatendatei einen Verbindungstyp hinzufügen. Es ist außerdem möglich, einen Verbindungstyp mithilfe der [Azure Automation-API](/previous-versions/azure/reference/mt163818(v=azure.100)) zu erstellen, wenn das Integrationsmodul einen Verbindungstyp enthält und in Ihr Automation-Konto importiert wird. 

>[!NOTE]
>Zu den sicheren Objekten in Azure Automation gehören Anmeldeinformationen, Zertifikate, Verbindungen und verschlüsselte Variablen. Diese Objekte werden mithilfe eines eindeutigen Schlüssels verschlüsselt und in Azure Automation gespeichert, der für jedes Automation-Konto generiert wird. Azure Automation speichert den Schlüssel im vom System verwalteten Schlüsseltresor. Vor dem Speichern einer sicheren Ressource lädt Azure Automation den Schlüssel aus dem Schlüsseltresor und verwendet ihn dann zum Verschlüsseln der Ressource. 

## <a name="connection-types"></a>Verbindungstypen

Azure Automation stellt die folgenden integrierten Verbindungstypen zur Verfügung:

* `Azure`: Stellt eine Verbindung dar, die zum Verwalten von klassischen Ressourcen verwendet wird.
* `AzureServicePrincipal`: Stellt eine Verbindung dar, die vom ausführenden Azure-Konto verwendet wird.
* `AzureClassicCertificate`: Stellt eine Verbindung dar, die vom klassischen ausführenden Azure-Konto verwendet wird.

In den meisten Fällen müssen Sie keine Verbindungsressource erstellen, da sie bei der Erstellung eines [ausführenden Kontos](automation-security-overview.md) erstellt wird.

## <a name="powershell-cmdlets-to-access-connections"></a>PowerShell-Cmdlets für den Zugriff auf Verbindungen

Mit den Cmdlets in der folgenden Tabelle können Sie Automation-Verbindungen mit PowerShell erstellen und verwalten. Diese sind im Lieferumfang der Az-Module enthalten.

|Cmdlet|BESCHREIBUNG|
|---|---|
|[Get-AzAutomationConnection](/powershell/module/az.automation/get-azautomationconnection)|Ruft Informationen zu einer Verbindung ab.|
|[New-AzAutomationConnection](/powershell/module/az.automation/new-azautomationconnection)|Erstellt eine neue Verbindung.|
|[Remove-AzAutomationConnection](/powershell/module/Az.Automation/Remove-AzAutomationConnection)|Entfernt eine vorhandene Verbindung.|
|[Set-AzAutomationConnectionFieldValue](/powershell/module/Az.Automation/Set-AzAutomationConnectionFieldValue)|Legt den Wert für ein bestimmtes Feld einer vorhandenen Verbindung fest.|

## <a name="internal-cmdlets-to-access-connections"></a>Interne Cmdlets für den Zugriff auf Verbindungen

Das internen Cmdlet in der folgenden Tabelle wird für den Zugriff auf Verbindungen in Ihren Runbooks und DSC-Konfigurationen verwendet. Dieses Cmdlet ist im globalen Modul `Orchestrator.AssetManagement.Cmdlets` enthalten. Weitere Informationen finden Sie unter [Interne Cmdlets](shared-resources/modules.md#internal-cmdlets).

|Internes Cmdlet|BESCHREIBUNG|
|---|---|
|`Get-AutomationConnection` | Ruft die Werte der verschiedenen Felder in der Verbindung ab und gibt sie als [Hashtabelle](/powershell/module/microsoft.powershell.core/about/about_hash_tables) zurück. Sie können diese Hashtabelle mit den entsprechenden Befehlen im Runbook oder in der DSC-Konfiguration verwenden.|

>[!NOTE]
>Vermeiden Sie die Verwendung von Variablen im Parameter `Name` von `Get-AutomationConnection`. Durch die Verwendung von Variablen in diesem Fall kann die Ermittlung von Abhängigkeiten zwischen Runbooks oder DSC-Konfigurationen und Verbindungsressourcen zur Entwurfszeit erschwert werden.

## <a name="python-functions-to-access-connections"></a>Python-Funktionen für den Zugriff auf Verbindungen

Mit der Funktion in der folgenden Tabelle wird auf Verbindungen in einem Python 2- und Python 3-Runbook zugegriffen. Python 3-Runbooks sind derzeit als Vorschau verfügbar.

| Funktion | BESCHREIBUNG |
|:---|:---|
| `automationassets.get_automation_connection` | Ruft eine Verbindung ab. Gibt ein Wörterbuch mit den Eigenschaften der Verbindung zurück. |

> [!NOTE]
> Sie müssen das Modul `automationassets` oben im Python-Runbook importieren, um auf die Ressourcenfunktionen zugreifen zu können.

## <a name="create-a-new-connection"></a>Erstellen einer neuen Verbindung

### <a name="create-a-new-connection-with-the-azure-portal"></a>Herstellen einer neuen Verbindung mit dem Azure-Portal

So erstellen Sie eine neue Verbindung im Azure-Portal

1. Klicken Sie in Ihrem Automation-Konto unter **Freigegebene Ressourcen** auf **Verbindungen**.
2. Klicken Sie auf der Seite „Verbindungen“ auf **+ Verbindung hinzufügen**.
4. Wählen Sie im Feld **Typ** im Bereich „Neue Verbindung“ den zu erstellenden Verbindungstyp aus. Zur Auswahl stehen `Azure`, `AzureServicePrincipal` und `AzureClassicCertificate`. 
5. Das Formular stellt Eigenschaften für den Verbindungstyp bereit, den Sie ausgewählt haben. Geben Sie die erforderlichen Daten ein, und klicken Sie auf **Erstellen** , um die neue Verbindung zu speichern.

### <a name="create-a-new-connection-with-windows-powershell"></a>Herstellen einer neuen Verbindung mit Windows-PowerShell

Erstellen Sie eine neue Verbindung mit Windows PowerShell und dem Cmdlet `New-AzAutomationConnection`. Dieses Cmdlet verfügt über einen Parameter `ConnectionFieldValues`, der eine Hashtabelle erwartet, mit deren Hilfe die Werte für jede der im Verbindungstyp definierten Eigenschaften festgelegt werden.

Sie können die folgenden Beispielbefehle als Alternative zum Erstellen des ausführenden Kontos im Portal verwenden, um eine neue Verbindungsressource zu erstellen.

```powershell
$ConnectionAssetName = "AzureRunAsConnection"
$ConnectionFieldValues = @{"ApplicationId" = $Application.ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Cert.Thumbprint; "SubscriptionId" = $SubscriptionId}
New-AzAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureServicePrincipal -ConnectionFieldValues $ConnectionFieldValues
```

Beim Erstellen Ihres Automation-Kontos sind mehrere globale Standardmodule sowie der Verbindungstyp `AzureServicePrincipal` zum Erstellen der Verbindungsressource `AzureRunAsConnection` enthalten. Wenn Sie versuchen, eine neue Verbindungsressource zum Herstellen einer Verbindung mit einem Dienst oder einer Anwendung zu erstellen, schlägt der Vorgang fehl, weil der Verbindungstyp noch nicht in Ihrem Automation-Konto definiert ist. Weitere Informationen zum Erstellen Ihres eigenen Verbindungstyps für ein benutzerdefiniertes Modul finden Sie unter [Hinzufügen eines Verbindungstyps](#add-a-connection-type).

## <a name="add-a-connection-type"></a>Hinzufügen eines Verbindungstyps

Wenn Ihr Runbook oder Ihre DSC-Verbindung eine Verbindung mit einem externen Dienst herstellt, müssen Sie einen Verbindungstyp in einem [benutzerdefinierten Modul](shared-resources/modules.md#custom-modules), das als „Integrationsmodul“ bezeichnet wird, definieren. Dieses Modul enthält eine Metadatendatei, die Verbindungstypeigenschaften angibt, **&lt;Modulname&gt;-Automation.json** heißt und sich im Modulordner Ihrer komprimierten **ZIP**-Datei befindet. Die Datei enthält die Felder einer Verbindung, die zum Herstellen einer Verbindung mit dem entsprechenden System oder Dienst, das/den das Modul darstellt, erforderlich sind. Mit dieser Datei können Sie die Feldnamen, die Datentypen, den Verschlüsselungsstatus sowie den optionalen Status für den Verbindungstyp festlegen. 

Das folgende Beispiel ist eine Vorlage im **JSON**-Dateiformat, die Eigenschaften für Benutzername und Kennwort für einen benutzerdefinierten Verbindungstyp namens `MyModuleConnection` definiert:

```json
{
   "ConnectionFields": [
   {
      "IsEncrypted":  false,
      "IsOptional":  true,
      "Name":  "Username",
      "TypeName":  "System.String"
   },
   {
      "IsEncrypted":  true,
      "IsOptional":  false,
      "Name":  "Password",
      "TypeName":  "System.String"
   }
   ],
   "ConnectionTypeName":  "MyModuleConnection",
   "IntegrationModuleName":  "MyModule"
}
```

## <a name="get-a-connection-in-a-runbook-or-dsc-configuration"></a>Abrufen einer Verbindung in einem Runbook oder einer DSC-Konfiguration

Sie rufen eine Verbindung in einem Runbook oder einer DSC-Konfiguration mit dem internen Cmdlet `Get-AutomationConnection` ab. Dieses Cmdlet wird gegenüber dem Cmdlet `Get-AzAutomationConnection` bevorzugt, da es die Verbindungswerte anstelle von Informationen über die Verbindung abruft.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Das folgende Beispiel zeigt, wie das ausführende Konto für die Authentifizierung bei Azure Resource Manager-Ressourcen in Ihrem Runbook verwendet wird. Dabei wird ein Verbindungsobjekt verwendet, das für das ausführende Konto steht und auf den zertifikatbasierten Dienstprinzipal verweist.

```powershell
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
```

# <a name="python"></a>[Python](#tab/python2)

Das folgende Beispiel zeigt die Authentifizierung mithilfe der Verbindung mit dem ausführenden Konto in einem Python 2- und Python 3-Runbook.

```python
""" Tutorial to show how to authenticate against Azure resource manager resources """
import azure.mgmt.resource
import automationassets

def get_automation_runas_credential(runas_connection):
    """ Returns credentials to authenticate against Azure resource manager """
    from OpenSSL import crypto
    from msrestazure import azure_active_directory
    import adal

    # Get the Azure Automation Run As service principal certificate
    cert = automationassets.get_automation_certificate("AzureRunAsCertificate")
    pks12_cert = crypto.load_pkcs12(cert)
    pem_pkey = crypto.dump_privatekey(
        crypto.FILETYPE_PEM, pks12_cert.get_privatekey())

    # Get Run As connection information for the Azure Automation service principal
    application_id = runas_connection["ApplicationId"]
    thumbprint = runas_connection["CertificateThumbprint"]
    tenant_id = runas_connection["TenantId"]

    # Authenticate with service principal certificate
    resource = "https://management.core.windows.net/"
    authority_url = ("https://login.microsoftonline.com/" + tenant_id)
    context = adal.AuthenticationContext(authority_url)
    return azure_active_directory.AdalAuthentication(
        lambda: context.acquire_token_with_client_certificate(
            resource,
            application_id,
            pem_pkey,
            thumbprint)
    )


# Authenticate to Azure using the Azure Automation Run As service principal
runas_connection = automationassets.get_automation_connection(
    "AzureRunAsConnection")
azure_credential = get_automation_runas_credential(runas_connection)
```

---

### <a name="graphical-runbook-examples"></a>Beispiele für grafische Runbooks

Sie können einem grafischen Runbook eine Aktivität für das interne Cmdlet `Get-AutomationConnection` hinzufügen. Klicken Sie einfach mit der rechten Maustaste in den Bibliotheksbereich des grafischen Editors, und wählen Sie **Zum Zeichenbereich hinzufügen** aus.

![Hinzufügen zur Canvas](media/automation-connections/connection-add-canvas.png)

Die folgende Abbildung zeigt ein Beispiel für die Verwendung eines Verbindungsobjekts in einem grafischen Runbook. Dieses Beispiel verwendet das `Constant value`-Dataset für die Aktivität `Get RunAs Connection`, die ein Verbindungsobjekt für die Authentifizierung nutzt. Es wird eine [Pipelineverknüpfung](automation-graphical-authoring-intro.md#use-links-for-workflow) verwendet, da der Parameter `ServicePrincipalCertificate` so festgelegt wurde, dass er ein einzelnes Objekt erwartet.

![Abrufen von Verbindungen](media/automation-connections/automation-get-connection-object.png)

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu den Cmdlets, die für den Zugriff auf Verbindungen verwendet werden, finden Sie unter [Verwalten von Modulen in Azure Automation](shared-resources/modules.md).
* Allgemeine Informationen zu Runbooks finden Sie unter [Ausführen von Runbooks in Azure Automation](automation-runbook-execution.md).
* Ausführliche Informationen zu DSC-Konfigurationen finden Sie in der [Übersicht über State Configuration](automation-dsc-overview.md).
