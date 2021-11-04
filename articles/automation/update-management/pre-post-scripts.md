---
title: Verwalten von Pre- und Post-Skripts in der Bereitstellung der Updateverwaltung in Azure
description: In diesem Artikel erfahren Sie, wie Sie Pre- und Post-Skripts für Updatebereitstellungen konfigurieren und verwalten.
services: automation
ms.subservice: update-management
ms.date: 09/16/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 19101cea8f435f09cb37aa2340f0bc02788442f3
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131459062"
---
# <a name="manage-pre-scripts-and-post-scripts"></a>Verwalten von Pre- und Post-Skripts

Pre- und Post-Skripts sind Runbooks, die vor (Pre-Aufgaben) und nach (Post-Aufgaben) einer Updatebereitstellung in Ihrem Azure Automation-Konto ausgeführt werden. Pre- und Post-Skripts werden nicht lokal, sondern im Azure-Kontext ausgeführt. Pre-Skripts werden zu Beginn der Updatebereitstellung ausgeführt. Unter Windows werden Postskripts am Ende der Bereitstellung sowie nach allen konfigurierten Neustarts ausgeführt. Unter Linux werden Postskripts nach dem Ende der Bereitstellung ausgeführt, aber nicht nach einem Neustart des Computers. 

## <a name="pre-script-and-post-script-requirements"></a>Anforderungen an Pre- und Post-Skripts

Damit ein Runbook als Pre- oder Post-Skript verwendet werden kann, müssen Sie es in Ihr Automation-Konto importieren und [veröffentlichen](../manage-runbooks.md#publish-a-runbook).

Zurzeit werden als Skripts vor oder nach der Bereitstellung nur PowerShell- und Python 2-Runbooks unterstützt. Andere Runbook-Typen wie Python 3, grafisch, PowerShell-Workflow und grafischer PowerShell-Workflow werden derzeit nicht als Skripts vor oder nach der Bereitstellung unterstützt.

## <a name="pre-script-and-post-script-parameters"></a>Parameter für Pre- und Post-Skripts

Beim Konfigurieren von Pre- und Post-Skripts können Sie genau wie beim Planen eines Runbooks Parameter übergeben. Parameter werden beim Erstellen der Updatebereitstellung definiert. Pre- und Post-Skripts unterstützen folgende Typen:

* [char]
* [byte]
* [int]
* [long]
* [decimal]
* [single]
* [double]
* [DateTime]
* [string]

Runbookparameter für Pre- und Post-Skripts unterstützen keine Boole-, Objekt- oder Arraytypen. Bei solchen Werten treten in Runbooks Fehler auf. 

Wenn Sie einen anderen Objekttyp benötigen, können Sie eine Typumwandlung in einen anderen Typ mit Ihrer eigenen Logik im Runbook durchführen.

Neben den Standardrunbookparametern steht auch der `SoftwareUpdateConfigurationRunContext`-Parameter (JSON-Zeichenfolgentyp) zur Verfügung. Wenn Sie diesen Parameter in Ihrem Pre- oder Post-Skript-Runbook definieren, wird er automatisch von der Updatebereitstellung übergeben. Der Parameter enthält Informationen zur Updatebereitstellung, bei denen es sich um eine Teilmenge der von der [SoftwareUpdateconfigurations-API](/rest/api/automation/softwareupdateconfigurations/getbyname#updateconfiguration) zurückgegebenen Informationen handelt. Die folgenden Abschnitte definieren die zugeordneten Eigenschaften.

### <a name="softwareupdateconfigurationruncontext-properties"></a>SoftwareUpdateConfigurationRunContext-Eigenschaften

|Eigenschaft  |type |BESCHREIBUNG  |
|---------|---------|---------|
|SoftwareUpdateConfigurationName     |String | Der Name der Softwareupdatekonfiguration.        |
|SoftwareUpdateConfigurationRunId     |GUID | Die eindeutige ID für die Ausführung        |
|SoftwareUpdateConfigurationSettings     || Eine Sammlung von Eigenschaften im Zusammenhang mit der Softwareupdatekonfiguration.         |
|SoftwareUpdateConfigurationSettings.OperatingSystem     |Int | Die Zielbetriebssysteme für die Updatebereitstellung. `1` = Windows und `2` = Linux        |
|SoftwareUpdateConfigurationSettings.Duration     |Timespan (HH:MM:SS) | Die maximale Dauer der Ausführung der Updatebereitstellung im Format `PT[n]H[n]M[n]S` gemäß ISO 8601 (das so genannte „Wartungsfenster“).<br> Beispiel: 02:00:00         |
|SoftwareUpdateConfigurationSettings.WindowsConfiguration     || Eine Sammlung von Eigenschaften im Zusammenhang mit Windows-Computern.         |
|SoftwareUpdateConfigurationSettings.WindowsConfiguration.excludedKbNumbers     |String | Eine durch Leerzeichen getrennte Liste von KBs, die von der Updatebereitstellung ausgeschlossen sind.        |
|SoftwareUpdateConfigurationSettings.WindowsConfiguration.includedKbNumbers     |String | Eine durch Leerzeichen getrennte Liste von KBs, die in die Updatebereitstellung einbezogen sind.        |
|SoftwareUpdateConfigurationSettings.WindowsConfiguration.UpdateCategories     |Integer | 1 = „Critical“;<br> 2 = „Security“<br> 4 = „UpdateRollUp“<br> 8 = „FeaturePack“<br> 16 = „ServicePack“<br> 32 = „Definition“<br> 64 = „Tools“<br> 128 = „Updates“        |
|SoftwareUpdateConfigurationSettings.WindowsConfiguration.rebootSetting     |String | Neustarteinstellungen für die Updatebereitstellung. Die Werte sind `IfRequired`, `Never`, `Always`.      |
|SoftwareUpdateConfigurationSettings.LinuxConfiguration     || Eine Sammlung von Eigenschaften im Zusammenhang mit Linux-Computern.         |
|SoftwareUpdateConfigurationSettings.LinuxConfiguration.IncludedPackageClassifications |Integer |0 = „Unclassified“<br> 1 = „Critical“<br> 2 = „Security“<br> 4 = „Other“|
|SoftwareUpdateConfigurationSettings.LinuxConfiguration.IncludedPackageNameMasks |String | Eine durch Leerzeichen getrennte Liste von Paketnamen, die in die Updatebereitstellung einbezogen sind. |
|SoftwareUpdateConfigurationSettings.LinuxConfiguration.ExcludedPackageNameMasks |String |Eine durch Leerzeichen getrennte Liste von Paketnamen, die von der Updatebereitstellung ausgeschlossen sind. |
|SoftwareUpdateConfigurationSettings.LinuxConfiguration.RebootSetting |String |Neustarteinstellungen für die Updatebereitstellung. Die Werte sind `IfRequired`, `Never`, `Always`.      |
|SoftwareUpdateConfiguationSettings.AzureVirtualMachines     |Zeichenfolgenarray | Eine Liste mit Ressourcen-IDs für die virtuellen Azure-Computer in der Updatebereitstellung.        |
|SoftwareUpdateConfigurationSettings.NonAzureComputerNames|Zeichenfolgenarray |Eine Liste mit den FQDNs der Azure-fremden Computer in der Updatebereitstellung.|

Das folgende Beispiel ist eine JSON-Zeichenfolge, die den **SoftwareUpdateConfigurationSettings**-Eigenschaften für einen Linux-Computer übergeben wird:

```json
"SoftwareUpdateConfigurationSettings": {
     "OperatingSystem": 2,
     "WindowsConfiguration": null,
     "LinuxConfiguration": {
         "IncludedPackageClassifications": 7,
         "ExcludedPackageNameMasks": "fgh xyz",
         "IncludedPackageNameMasks": "abc bin*",
         "RebootSetting": "IfRequired"
     },
     "Targets": {
         "azureQueries": null,
         "nonAzureQueries": ""
     },
     "NonAzureComputerNames": [
        "box1.contoso.com",
        "box2.contoso.com"
     ],
     "AzureVirtualMachines": [
        "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Compute/virtualMachines/vm-01"
     ],
     "Duration": "02:00:00",
     "PSComputerName": "localhost",
     "PSShowComputerName": true,
     "PSSourceJobInstanceId": "2477a37b-5262-4f4f-b636-3a70152901e9"
 }
```

Das folgende Beispiel ist eine JSON-Zeichenfolge, die den **SoftwareUpdateConfigurationSettings**-Eigenschaften für einen Windows-Computer übergeben wird:

```json
"SoftwareUpdateConfigurationRunContext": {
    "SoftwareUpdateConfigurationName": "sampleConfiguration",
    "SoftwareUpdateConfigurationRunId": "00000000-0000-0000-0000-000000000000",
    "SoftwareUpdateConfigurationSettings": {
      "operatingSystem": "Windows",
      "duration": "02:00:00",
      "windows": {
        "excludedKbNumbers": [
          "168934",
          "168973"
        ],
        "includedUpdateClassifications": "Critical",
        "rebootSetting": "IfRequired"
      },
      "azureVirtualMachines": [
        "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/vm-01",
        "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/vm-02",
        "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/vm-03"
      ],
      "nonAzureComputerNames": [
        "box1.contoso.com",
        "box2.contoso.com"
      ]
    }
  }
```

Ein vollständiges Beispiel mit allen Eigenschaften finden Sie unter: [Abrufen der Softwareupdatekonfiguration anhand des Namens](/rest/api/automation/softwareupdateconfigurations/getbyname#examples).

> [!NOTE]
> Das `SoftwareUpdateConfigurationRunContext`-Objekt kann doppelte Einträge für Computer enthalten. Dies kann dazu führen, dass Pre- und Post-Skripts mehrmals auf dem gleichen Computer ausgeführt werden. Verwenden Sie zur Umgehung dieses Verhaltens `Sort-Object -Unique`, um nur eindeutige VM-Namen auszuwählen.

## <a name="use-a-pre-script-or-post-script-in-a-deployment"></a>Verwenden eines Pre- oder Post-Skripts in einer Bereitstellung

Wenn Sie ein Pre- oder Post-Skript in einer Updatebereitstellung verwenden möchten, müssen Sie zunächst eine Updatebereitstellung erstellen. Wählen Sie **Vor und nach dem Vorgang auszuführende Skripts** aus. Daraufhin wird die Seite **Vor und nach dem Vorgang auszuführende Skripts auswählen** angezeigt.

![Auswählen der Skripts](./media/pre-post-scripts/select-scripts.png)

Wählen Sie das Skript aus, das Sie verwenden möchten. In diesem Beispiel verwenden wir das Runbook **UpdateManagement-TurnOnVms**. Wenn Sie das Runbook auswählen, wird die Seite **Skript konfigurieren** geöffnet. Wählen Sie **Skript vor Vorgang** und anschließend **OK** aus.

Wiederholen Sie diese Schritte für das Skript **UpdateManagement-TurnOffVms**. Wählen Sie diesmal für **Skripttyp** jedoch **Skript nach Vorgang** aus.

Im Abschnitt **Ausgewählte Elemente** werden nun die beiden ausgewählten Skripts angezeigt. Eines ist ein Pre-Skript, das andere ein Post-Skript:

![Ausgewählte Elemente](./media/pre-post-scripts/selected-items.png)

Schließen Sie die Konfiguration der Updatebereitstellung ab.

Nach Abschluss der Updatebereitstellung können Sie unter **Updatebereitstellungen** die Ergebnisse anzeigen. Wie Sie sehen, wird der Status des Pre- und Post-Skripts angezeigt:

![Updateergebnisse](./media/pre-post-scripts/update-results.png)

Wenn Sie die Ausführung der Updatebereitstellung auswählen, werden weitere Details der Pre- und Post-Skripts angezeigt. Dort finden Sie auch einen Link zur Skriptquelle zum Zeitpunkt der Ausführung.

![Ergebnisse der Bereitstellungsausführung](./media/pre-post-scripts/deployment-run.png)

## <a name="stop-a-deployment"></a>Beenden einer Bereitstellung

Wenn Sie eine Bereitstellung basierend auf einem vorbereitenden Skript beenden möchten, müssen Sie eine Ausnahme [auslösen](../automation-runbook-execution.md#throw). Andernfalls werden die Bereitstellung und das Post-Skript trotzdem ausgeführt. Der folgende Codeausschnitt veranschaulicht das Auslösen einer Ausnahme mithilfe von PowerShell.

```powershell
#In this case, we want to terminate the patch job if any run fails.
#This logic might not hold for all cases - you might want to allow success as long as at least 1 run succeeds
foreach($summary in $finalStatus)
{
    if ($summary.Type -eq "Error")
    {
        #We must throw in order to fail the patch deployment.
        throw $summary.Summary
    }
}
```

In Python 2 wird die Ausnahmebehandlung in einem [try](https://www.python-course.eu/exception_handling.php) -Block verarbeitet.

## <a name="interact-with-machines"></a>Interagieren mit Computern

Pre- und Post-Skripts werden nicht direkt auf den Computern in Ihrer Bereitstellung, sondern als Runbooks in Ihrem Automation-Konto ausgeführt. Pre- und Post-Aufgaben werden auch im Azure-Kontext ausgeführt und haben keinen Zugriff auf Azure-fremde Computer. In den folgenden Abschnitten erfahren Sie, wie Sie direkt mit den Computern interagieren. Dabei spielt es keine Rolle, ob es sich um virtuelle Azure-Computer oder um Azure-fremde Computer handelt.

### <a name="interact-with-azure-machines"></a>Interaktion mit Azure-Computern

Pre- und Post-Aufgaben werden nicht nativ auf den virtuellen Azure-Computern in Ihrer Bereitstellung, sondern als Runbooks ausgeführt. Für die Interaktion mit den Azure-VMs benötigen Sie die folgenden Elemente:

* Eine [verwaltete Identität](../automation-security-overview.md#managed-identities) oder ein ausführendes Konto
* Ein Runbook, das Sie ausführen möchten

Verwenden Sie das Cmdlet [Invoke-AzVMRunCommand](/powershell/module/az.compute/invoke-azvmruncommand) für die Interaktion mit Ihren Azure-VMs. Ein Beispiel dafür finden Sie im Runbookbeispiel [Updateverwaltung: Ausführen eines Skripts mit dem Befehl „Run“](https://github.com/azureautomation/update-management-run-script-with-run-command).

### <a name="interact-with-non-azure-machines"></a>Interagieren mit Azure-fremden Computern

Pre- und Post-Aufgaben werden im Azure-Kontext ausgeführt und haben keinen Zugriff auf Azure-fremde Computer. Für die Interaktion mit den Azure-fremden Computern benötigen Sie Folgendes:

* Eine [verwaltete Identität](../automation-security-overview.md#managed-identities) oder ein ausführendes Konto
* Einen auf dem Computer installierten Hybrid Runbook Worker
* Ein Runbook, das Sie lokal ausführen möchten
* Ein übergeordnetes Runbook

Für die Interaktion mit Azure-fremden Computern wird ein übergeordnetes Runbook im Azure-Kontext ausgeführt. Dieses Runbook ruft mit dem Cmdlet [Start-AzAutomationRunbook](/powershell/module/Az.Automation/Start-AzAutomationRunbook) ein untergeordnetes Runbook auf. Sie müssen den `RunOn`-Parameter und den Namen des Hybrid Runbook Workers, auf dem das Skript ausgeführt werden soll, angeben. Weitere Informationen finden Sie im Runbookbeispiel [Update Management – run script locally](https://github.com/azureautomation/update-management-run-script-locally) (Updateverwaltung – Skript lokal ausführen).

## <a name="abort-patch-deployment"></a>Abbrechen der Bereitstellung von Patches

Falls Ihr Pre-Skript einen Fehler zurückgibt, empfiehlt es sich ggf., die Bereitstellung abzubrechen. Dazu müssen Sie in Ihrem Skript für jegliche Logik, die einen Fehler zur Folge hätte, einen Fehler [auslösen](/powershell/module/microsoft.powershell.core/about/about_throw).

```powershell
if (<My custom error logic>)
{
    #Throw an error to fail the patch deployment.
    throw "There was an error, abort deployment"
}
```

Wenn Sie in Python 2 einen Fehler auslösen möchten, wenn ein bestimmter Zustand eintritt, verwenden Sie eine [raise](https://docs.python.org/2.7/reference/simple_stmts.html#the-raise-statement)-Anweisung.

```python
If (<My custom error logic>)
   raise Exception('Something happened.')
```

## <a name="samples"></a>Beispiele

Beispiele für Pre- und Post-Skripts können aus der [Azure Automation-GitHub-Organisation](https://github.com/azureautomation) und dem [PowerShell-Katalog](https://www.powershellgallery.com/packages?q=Tags%3A%22UpdateManagement%22+Tags%3A%22Automation%22) heruntergeladen oder über das Azure-Portal importiert werden. Wählen Sie hierzu in Ihrem Automation-Konto unter **Prozessautomatisierung** die Option **Runbookkatalog** aus. Wählen Sie **Updateverwaltung** als Filter aus.

![Katalogliste](./media/pre-post-scripts/runbook-gallery.png)

Alternativ können Sie anhand der Namen in der folgenden Liste nach den Skripts suchen:

* Update Management – Turn On VMs (Updateverwaltung – VMs aktivieren)
* Update Management – Turn Off VMs (Updateverwaltung – VMs deaktivieren)
* Update Management – Run Script Locally (Updateverwaltung – Skript lokal ausführen)
* Update Management – Template for Pre/Post Scripts (Updateverwaltung – Vorlage für Pre-/Post-Skripts)
* Update Management – Run Script with Run Command (Updateverwaltung – Skript mit Ausführungsbefehl ausführen)

> [!IMPORTANT]
> Nachdem Sie die Runbooks importiert haben, müssen Sie sie veröffentlichen, um sie verwenden zu können. Suchen Sie hierzu in Ihrem Automation-Konto nach dem gewünschten Runbook, und wählen Sie **Bearbeiten** > **Veröffentlichen** aus.

Die Beispiele basieren alle auf der einfachen Vorlage, die im folgenden Beispiel definiert ist. Mit dieser Vorlage können Sie Ihr eigenes Runbook für die Verwendung mit Pre- und Post-Skripts erstellen. Die erforderliche Logik für die Authentifizierung bei Azure und die Behandlung des Parameters `SoftwareUpdateConfigurationRunContext` ist in der Vorlage enthalten.

```powershell
<#
.SYNOPSIS
 Barebones script for Update Management Pre/Post

.DESCRIPTION
  This script is intended to be run as a part of Update Management pre/post-scripts.
  It requires the Automation account's system-assigned managed identity.

.PARAMETER SoftwareUpdateConfigurationRunContext
  This is a system variable which is automatically passed in by Update Management during a deployment.
#>

param(
    [string]$SoftwareUpdateConfigurationRunContext
)

#region BoilerplateAuthentication
# Ensures you do not inherit an AzContext in your runbook
Disable-AzContextAutosave -Scope Process

# Connect to Azure with system-assigned managed identity
$AzureContext = (Connect-AzAccount -Identity).context

# set and store context
$AzureContext = Set-AzContext -SubscriptionName $AzureContext.Subscription -DefaultProfile $AzureContext
#endregion BoilerplateAuthentication

#If you wish to use the run context, it must be converted from JSON
$context = ConvertFrom-Json $SoftwareUpdateConfigurationRunContext
#Access the properties of the SoftwareUpdateConfigurationRunContext
$vmIds = $context.SoftwareUpdateConfigurationSettings.AzureVirtualMachines | Sort-Object -Unique
$runId = $context.SoftwareUpdateConfigurationRunId

Write-Output $context

#Example: How to create and write to a variable using the pre-script:
<#
#Create variable named after this run so it can be retrieved
New-AzAutomationVariable -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccount -Name $runId -Value "" -Encrypted $false
#Set value of variable
Set-AutomationVariable -Name $runId -Value $vmIds
#>

#Example: How to retrieve information from a variable set during the pre-script
<#
$variable = Get-AutomationVariable -Name $runId
#>
```

Wenn Sie möchten, dass das Runbook mit der systemseitig zugewiesenen verwalteten Identität ausgeführt wird, lassen Sie den Code unverändert. Wenn Sie lieber eine benutzerseitig zugewiesene verwaltete Identität verwenden möchten, gehen Sie wie folgt vor:
1. Entfernen Sie in Zeile 22 `$AzureContext = (Connect-AzAccount -Identity).context`.
1. Fügen Sie stattdessen `$AzureContext = (Connect-AzAccount -Identity -AccountId <ClientId>).context` ein.
1. Geben Sie die Client-ID ein.

> [!NOTE]
> Für nicht grafische PowerShell-Runbooks sind `Add-AzAccount` und `Add-AzureRMAccount` Aliase für [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount). Sie können diese Cmdlets verwenden, oder Sie können Ihre Module in Ihrem Automation-Konto auf die aktuellen Versionen [aktualisieren](../automation-update-azure-modules.md). Möglicherweise müssen Sie Ihre Module auch dann aktualisieren, wenn Sie gerade ein neues Automation-Konto erstellt haben.

## <a name="next-steps"></a>Nächste Schritte

Einzelheiten zur Updateverwaltung finden Sie unter [Verwalten von Updates und Patches für Ihre VMs](manage-updates-for-vm.md).
