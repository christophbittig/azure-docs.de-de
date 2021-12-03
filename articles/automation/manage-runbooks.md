---
title: Verwalten von Runbooks in Azure Automation
description: In diesem Artikel erfahren Sie, wie Sie Runbooks in Azure Automation verwalten.
services: automation
ms.subservice: process-automation
ms.date: 11/02/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 99f86065b9a33125e3ca4cf72af5b8ec442e3cc0
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131455455"
---
# <a name="manage-runbooks-in-azure-automation"></a>Verwalten von Runbooks in Azure Automation

Sie können Azure Automation ein Runbook hinzufügen, indem Sie entweder ein neues Runbook erstellen oder ein vorhandenes Runbook aus einer Datei oder aus dem [Runbookkatalog](automation-runbook-gallery.md) importieren. Dieser Artikel enthält Informationen zum Verwalten eines Runbooks sowie empfohlene Muster und bewährte Methoden für den Runbookentwurf. Einzelheiten zum Zugreifen auf Communityrunbooks und -module finden Sie in [Runbook- und Modulkataloge für Azure Automation](automation-runbook-gallery.md).

## <a name="create-a-runbook"></a>Erstellen eines Runbooks

Erstellen Sie ein neues Runbook in Azure Automation über das Azure-Portal oder die PowerShell. Nachdem das Runbook erstellt wurde, können Sie es mithilfe der folgenden Informationen bearbeiten:

* [Bearbeiten von Textrunbooks in Azure Automation](automation-edit-textual-runbook.md)
* [Grundlagen der wichtigsten PowerShell-Workflowkonzepte für Automation-Runbooks](automation-powershell-workflow.md)
* [Verwalten von Python 2-Paketen in Azure Automation](python-packages.md)
* [Verwalten von Python 3-Paketen (Vorschau) in Azure Automation](python-3-packages.md)

### <a name="create-a-runbook-in-the-azure-portal"></a>Erstellen eines Runbooks im Azure-Portal

1. Melden Sie sich beim Azure-[Portal](https://portal.azure.com) an.
1. Suchen Sie nach **Automation-Konten**, und wählen Sie diese Option aus.
1. Wählen Sie auf der Seite **Automation-Konten** in der entsprechenden Liste Ihr Automation-Konto aus.
1. Wählen Sie im Automation-Konto unter **Prozessautomatisierung** die Option **Runbooks** aus, um die Liste der Runbooks zu öffnen.
1. Klicken Sie auf **Runbook erstellen**.
    1. Benennen Sie das Runbook.
    1. In der Dropdownliste **Runbooktyp**: Wählen Sie den [Typ](automation-runbook-types.md) aus. Der Name des Runbooks muss mit einem Buchstaben beginnen und darf Buchstaben, Zahlen, Unterstriche und Bindestriche enthalten.
    1. Wählen Sie die **Runtimeversion** aus.
    1. Geben Sie eine anwendbare **Beschreibung** ein.
1. Klicken Sie auf **Erstellen**, um das Runbook zu erstellen.

### <a name="create-a-runbook-with-powershell"></a>Erstellen eines Runbooks mit PowerShell

Verwenden Sie das Cmdlet [New-AzAutomationRunbook](/powershell/module/az.automation/new-azautomationrunbook), um ein leeres Runbook zu erstellen. Verwenden Sie den Parameter `Type`, um einen der für `New-AzAutomationRunbook` definierten Runbooktypen anzugeben.

Das folgenden Beispiel zeigt, wie Sie ein neues leeres Runbook erstellen.

```azurepowershell-interactive
$params = @{
    AutomationAccountName = 'MyAutomationAccount'
    Name                  = 'NewRunbook'
    ResourceGroupName     = 'MyResourceGroup'
    Type                  = 'PowerShell'
}
New-AzAutomationRunbook @params
```

## <a name="import-a-runbook"></a>Importieren eines Runbooks

Sie können ein PowerShell- oder PowerShell-Workflowskript ( **.ps1**), ein grafisches Runbook ( **.graphrunbook**) oder ein Python 2- oder Python 3-Skript ( **.py**) importieren, um ein eigenes Runbook zu erstellen. Geben Sie den [Runbook-Typ](automation-runbook-types.md) an, der beim Import erstellt wird. Berücksichtigen Sie dabei die folgenden Aspekte.

* Eine **.ps1**-Datei ohne Workflow kann in ein [PowerShell-Runbook](automation-runbook-types.md#powershell-runbooks) oder [PowerShell-Workflow-Runbook](automation-runbook-types.md#powershell-workflow-runbooks) importiert werden. Wenn Sie sie in ein PowerShell-Workflow-Runbook importieren, wird sie in einen Workflow konvertiert. In diesem Fall sind im Runbook Kommentare enthalten, um die vorgenommenen Änderungen zu beschreiben.

* Eine **.ps1**-Datei mit einem PowerShell-Workflow kann nur in ein [PowerShell-Workflow-Runbook](automation-runbook-types.md#powershell-workflow-runbooks) importiert werden. Wenn die Datei mehrere PowerShell-Workflows enthält, schlägt der Import fehl. Sie müssen jeden Workflow in einer eigenen Datei speichern und einzeln importieren.

* Eine **.ps1**-Datei mit einem PowerShell-Workflow darf nicht in ein [PowerShell-Runbook](automation-runbook-types.md#powershell-runbooks) importiert werden, da sie von der PowerShell-Skript-Engine nicht erkannt werden kann.

* Importieren Sie eine **.graphrunbook**-Datei nur in ein neues [grafisches Runbook](automation-runbook-types.md#graphical-runbooks).

### <a name="import-a-runbook-from-the-azure-portal"></a>Importieren eines Runbooks im Azure-Portal

Mit dem folgenden Verfahren können eine Skriptdatei in Azure Automation importieren.

> [!NOTE]
> Sie können über das Portal nur eine **.ps1**-Datei in ein PowerShell-Workflow-Runbook importieren.

1. Suchen Sie im Azure-Portal nach **Automation-Konten** und wählen Sie diese Option aus.
1. Wählen Sie auf der Seite **Automation-Konten** in der entsprechenden Liste Ihr Automation-Konto aus.
1. Wählen Sie im Automation-Konto unter **Prozessautomatisierung** die Option **Runbooks** aus, um die Liste der Runbooks zu öffnen.
1. Klicken Sie auf **Runbook importieren**. Sie können eine der folgenden Optionen auswählen:
    1. **Nach Datei suchen**: Wählt eine Datei auf Ihrem lokalen Computer aus.
    1. **Aus Katalog durchsuchen**: Sie können ein vorhandenes Runbook im Katalog suchen und auswählen.
1. Wählen Sie die Datei aus.
1. Wenn das Feld **Name** aktiviert ist, haben Sie die Möglichkeit, den Namen des Runbooks zu ändern. Der Name muss mit einem Buchstaben beginnen und darf Buchstaben, Zahlen, Unterstriche und Bindestriche enthalten.
1. Der [**Runbooktyp**](automation-runbook-types.md) wird automatisch ausgefüllt, Sie können den Typ jedoch unter Berücksichtigung der geltenden Einschränkungen ändern.
1. Die **Runtimeversion** wird entweder automatisch ausgefüllt, oder Sie wählen die Version aus der Dropdownliste aus.
1. Klicken Sie auf **Importieren**. Das neue Runbook wird in der Liste der Runbooks für das Automation-Konto angezeigt.
1. Sie müssen das [Runbook veröffentlichen](#publish-a-runbook), bevor Sie es ausführen können.

> [!NOTE]
> Nachdem Sie ein grafisches Runbook importiert haben, können Sie es in einen anderen Typ konvertieren. Sie können allerdings ein grafisches Runbook nicht in ein Textrunbook konvertieren.

### <a name="import-a-runbook-with-powershell"></a>Importieren eines Runbooks mit PowerShell

Verwenden Sie das Cmdlet [Import-AzAutomationRunbook](/powershell/module/az.automation/import-azautomationrunbook), um eine Skriptdatei als Entwurf eines Runbooks zu importieren. Wenn das Runbook bereits vorhanden ist, tritt beim Import ein Fehler auf, sofern Sie nicht den Parameter `Force` mit dem Cmdlet verwenden.

Das folgende Beispiel zeigt, wie Sie eine Skriptdatei in ein Runbook importieren.

```azurepowershell-interactive
$params = @{
    AutomationAccountName = 'MyAutomationAccount'
    Name                  = 'Sample_TestRunbook'
    ResourceGroupName     = 'MyResourceGroup'
    Type                  = 'PowerShell'
    Path                  = 'C:\Runbooks\Sample_TestRunbook.ps1'
}
Import-AzAutomationRunbook @params
```

## <a name="handle-resources"></a>Behandeln von Ressourcen

Wenn Ihr Runbook eine [Ressource](automation-runbook-execution.md#resources) erstellt, sollte das Skript prüfen, ob die Ressource bereits vorhanden ist, bevor versucht wird, sie erneut zu erstellen. Hier ist ein einfaches Beispiel.

```powershell
$vmName = 'WindowsVM1'
$rgName = 'MyResourceGroup'
$myCred = Get-AutomationPSCredential 'MyCredential'

$vmExists = Get-AzResource -Name $vmName -ResourceGroupName $rgName
if (-not $vmExists) {
    Write-Output "VM $vmName does not exist, creating"
    New-AzVM -Name $vmName -ResourceGroupName $rgName -Credential $myCred
} else {
    Write-Output "VM $vmName already exists, skipping"
}
```

## <a name="retrieve-details-from-activity-log"></a>Abrufen von Details aus dem Aktivitätsprotokoll

Sie können aus dem [Aktivitätsprotokoll](automation-runbook-execution.md#activity-logging) des Automation-Kontos Runbookdetails abrufen, z. B. die Person oder das Konto, die bzw. das das Runbook gestartet hat. Das folgende PowerShell-Beispiel gibt den letzten Benutzer an, der das angegebene Runbook ausgeführt hat.

```powershell-interactive
$rgName = 'MyResourceGroup'
$accountName = 'MyAutomationAccount'
$runbookName = 'MyRunbook'
$startTime = (Get-Date).AddDays(-1)

$params = @{
    ResourceGroupName = $rgName
    StartTime         = $startTime
}
$JobActivityLogs = (Get-AzLog @params).Where( { $_.Authorization.Action -eq 'Microsoft.Automation/automationAccounts/jobs/write' })

$JobInfo = @{}
foreach ($log in $JobActivityLogs) {
    # Get job resource
    $JobResource = Get-AzResource -ResourceId $log.ResourceId

    if ($null -eq $JobInfo[$log.SubmissionTimestamp] -and $JobResource.Properties.Runbook.Name -eq $runbookName) {
        # Get runbook
        $jobParams = @{
            ResourceGroupName     = $rgName
            AutomationAccountName = $accountName
            Id                    = $JobResource.Properties.JobId
        }
        $Runbook = Get-AzAutomationJob @jobParams | Where-Object RunbookName -EQ $runbookName

        # Add job information to hashtable
        $JobInfo.Add($log.SubmissionTimestamp, @($Runbook.RunbookName, $Log.Caller, $JobResource.Properties.jobId))
    }
}
$JobInfo.GetEnumerator() | Sort-Object Key -Descending | Select-Object -First 1
```

## <a name="track-progress"></a>Nachverfolgen des Status

Es empfiehlt sich, Runbooks so zu schreiben, dass sie modular aufgebaut sind und eine Logik aufweisen, die problemlos wiederverwendet und neu gestartet werden kann. Über die Nachverfolgung des Verlaufs in einem Runbook können Sie sicherstellen, dass die Runbooklogik ordnungsgemäß ausgeführt wird, falls Probleme auftreten.

Sie können den Fortschritt eines Runbooks mithilfe einer externen Quelle verfolgen, wie z. B. einem Speicherkonto, einer Datenbank oder gemeinsam genutzten Dateien. Erstellen Sie die Logik in Ihrem Runbook, um zunächst den Zustand der letzten Aktion zu überprüfen. Dann kann die Logik auf Grundlage der Ergebnisse der Überprüfung bestimmte Aufgaben im Runbook entweder überspringen oder fortsetzen.

## <a name="prevent-concurrent-jobs"></a>Gleichzeitige Aufträge verhindern

Einige Runbooks verhalten sich möglicherweise merkwürdig, wenn sie in mehreren Aufträgen gleichzeitig ausgeführt werden. In diesem Fall ist es wichtig, dass ein Runbook Logik implementiert, um festzustellen, ob bereits ein laufender Auftrag vorhanden ist. Hier ist ein einfaches Beispiel.

```powershell
# Ensures you do not inherit an AzContext in your runbook
Disable-AzContextAutosave -Scope Process

# Connect to Azure with system-assigned managed identity
$AzureContext = (Connect-AzAccount -Identity).context

# set and store context
$AzureContext = Set-AzContext -SubscriptionName $AzureContext.Subscription `
    -DefaultProfile $AzureContext

# Check for already running or new runbooks
$runbookName = "runbookName"
$resourceGroupName = "resourceGroupName"
$automationAccountName = "automationAccountName"

$jobs = Get-AzAutomationJob -ResourceGroupName $resourceGroupName `
    -AutomationAccountName $automationAccountName `
    -RunbookName $runbookName `
    -DefaultProfile $AzureContext

# Check to see if it is already running
$runningCount = ($jobs.Where( { $_.Status -eq 'Running' })).count

if (($jobs.Status -contains 'Running' -and $runningCount -gt 1 ) -or ($jobs.Status -eq 'New')) {
    # Exit code
    Write-Output "Runbook $runbookName is already running"
    exit 1
} else {
    # Insert Your code here
    Write-Output "Runbook $runbookName is not running"
}
```

Wenn Sie möchten, dass das Runbook mit der vom System zugewiesenen verwalteten Identität ausgeführt wird, lassen Sie den Code so, wie er ist. Wenn Sie lieber eine benutzerseitig zugewiesene verwaltete Identität verwenden möchten, gehen Sie wie folgt vor:

1. Entfernen Sie `$AzureContext = (Connect-AzAccount -Identity).context` aus Zeile 5.
1. Fügen Sie stattdessen `$AzureContext = (Connect-AzAccount -Identity -AccountId <ClientId>).context` ein.
1. Geben Sie die Client-ID ein.

## <a name="handle-transient-errors-in-a-time-dependent-script"></a>Behandeln vorübergehender Fehler in einem zeitabhängigen Skript

Ihre Runbooks müssen zuverlässig sein und mit [Fehlern](automation-runbook-execution.md#errors) – einschließlich vorübergehenden – umgehen können, die zu einem Neustart oder Ausfall führen können. Wenn ein Runbook ausfällt, versucht Azure Automation die erneute Ausführung.

Wenn Ihr Runbook normalerweise innerhalb einer bestimmten Zeitspanne ausgeführt wird, lassen Sie die Implementierungslogik des Skripts die Ausführungszeit überprüfen. Diese Überprüfung stellt sicher, dass Vorgänge wie Starten, Herunterfahren oder Aufskalieren nur zu bestimmten Zeiten erfolgen.

> [!NOTE]
> Die Ortszeit des Azure-Sandboxprozesses ist auf UTC festgelegt. Berechnungen für Datum und Uhrzeit in Ihren Runbooks müssen dies berücksichtigen.

## <a name="work-with-multiple-subscriptions"></a>Verwenden mehrerer Abonnements

Ihr Runbook muss in der Lage sein, mit [Abonnements](automation-runbook-execution.md#subscriptions) zu arbeiten. Für den Umgang mit mehreren Abonnements verwendet Ihr Runbook z. B. das Cmdlet [Disable-AzContextAutosave](/powershell/module/Az.Accounts/Disable-AzContextAutosave). Mit diesem Cmdlet wird sichergestellt, dass der Authentifizierungskontext nicht von einem anderen Runbook abgerufen wird, das in derselben Sandbox ausgeführt wird. 

```powershell
# Ensures you do not inherit an AzContext in your runbook
Disable-AzContextAutosave -Scope Process

# Connect to Azure with system-assigned managed identity
$AzureContext = (Connect-AzAccount -Identity).context

# set and store context
$AzureContext = Set-AzContext -SubscriptionName $AzureContext.Subscription `
    -DefaultProfile $AzureContext

$childRunbookName = 'childRunbookDemo'
$resourceGroupName = "resourceGroupName"
$automationAccountName = "automationAccountName"

$startParams = @{
    ResourceGroupName     = $resourceGroupName
    AutomationAccountName = $automationAccountName
    Name                  = $childRunbookName
    DefaultProfile        = $AzureContext
}
Start-AzAutomationRunbook @startParams
```

Wenn Sie möchten, dass das Runbook mit der vom System zugewiesenen verwalteten Identität ausgeführt wird, lassen Sie den Code so, wie er ist. Wenn Sie lieber eine benutzerseitig zugewiesene verwaltete Identität verwenden möchten, gehen Sie wie folgt vor:

1. Entfernen Sie `$AzureContext = (Connect-AzAccount -Identity).context` aus Zeile 5.
1. Fügen Sie stattdessen `$AzureContext = (Connect-AzAccount -Identity -AccountId <ClientId>).context` ein.
1. Geben Sie die Client-ID ein.

## <a name="work-with-a-custom-script"></a>Verwenden eines benutzerdefinierten Skripts

> [!NOTE]
> Es ist normalerweise nicht möglich, benutzerdefinierte Skripts und Runbooks auf einem Host auszuführen, auf dem ein Log Analytics-Agent installiert ist.

So verwenden Sie ein benutzerdefiniertes Skript

1. Erstellen Sie ein Automation-Konto.
2. Stellen Sie eine [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md)-Rolle bereit.
3. Auf einem Linux-Computer benötigen Sie dazu erhöhte Berechtigungen. Melden Sie sich an, um [Signaturüberprüfungen zu deaktivieren](automation-linux-hrw-install.md#turn-off-signature-validation).

## <a name="test-a-runbook"></a>Testen eines Runbooks

Wenn Sie ein Runbook testen, wird die [Entwurfsversion](#publish-a-runbook) ausgeführt, und alle darin ausgeführten Aktionen werden abgeschlossen. Es wird kein Auftragsverlauf erstellt. Allerdings werden die Datenströme [Ausgabe](automation-runbook-output-and-messages.md#use-the-output-stream) sowie [Warnung und Fehler](automation-runbook-output-and-messages.md#working-with-message-streams) im Bereich **Testausgabe** angezeigt. Nachrichten an den [ausführlichen Datenstrom](automation-runbook-output-and-messages.md#write-output-to-verbose-stream) werden nur dann im Ausgabebereich, wenn die Variable [VerbosePreference](automation-runbook-output-and-messages.md#work-with-preference-variables) auf `Continue` festgelegt ist.

Auch wenn die Entwurfsversion ausgeführt wird, wird das Runbook trotzdem normal ausgeführt, und alle Aktionen werden auf die Ressourcen in der Umgebung angewendet. Aus diesem Grund sollten Sie Runbooks nur auf Ressourcen testen, die sich nicht in der Produktionsumgebung befinden.

Die Vorgehensweise zum Testen der einzelnen [Typen von Runbooks](automation-runbook-types.md) ist identisch. Es besteht kein Unterschied zwischen Tests mit dem Text-Editor und dem grafischen Editor im Azure-Portal.

1. Öffnen Sie die Entwurfsversion des Runbooks entweder im [Text-Editor](automation-edit-textual-runbook.md) oder [grafischen Editor](automation-graphical-authoring-intro.md).
1. Klicken Sie auf **Testen**, um die **Testseite** zu öffnen.
1. Wenn das Runbook über Parameter verfügt, werden diese im linken Bereich aufgeführt. Dort können Sie die für den Test zu verwendenden Werte angeben.
1. Wenn Sie den Test auf einem [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md) ausführen möchten, ändern Sie die **Testlaufeinstellungen** in **Hybrid Worker**, und wählen Sie den Namen der Zielgruppe aus. Andernfalls behalten Sie den Standardwert **Azure** bei, um den Test in der Cloud auszuführen.
1. Klicken Sie auf **Starten**, um den Test zu starten.
1. Mithilfe der Schaltflächen im Bereich **Ausgabe** können Sie während des Tests ein [PowerShell-Workflow](automation-runbook-types.md#powershell-workflow-runbooks)- oder [graphisches](automation-runbook-types.md#graphical-runbooks) Runbook zu beenden oder anzuhalten. Wenn Sie das Runbook anhalten, wird die aktuelle Aktivität vor der Unterbrechung abgeschlossen. Nachdem das Runbook angehalten wurde, können Sie es beenden oder erneut starten.
1. Untersuchen Sie die Ausgabe des Runbooks im Bereich **Ausgabe**.

## <a name="publish-a-runbook"></a>Veröffentlichen eines Runbooks

Wenn Sie ein neues Runbooks erstellen oder importieren, müssen Sie es veröffentlichen, bevor Sie es ausführen können. Jedes Runbook in Azure Automation umfasst eine Entwurfsversion und eine veröffentlichte Version. Nur die veröffentlichte Version kann ausgeführt werden, und nur die Entwurfsversion kann bearbeitet werden. Die veröffentlichte Version bleibt von Änderungen an der Entwurfsversion unberührt. Wenn die Entwurfsversion zur Verfügung gestellt werden soll, können Sie sie veröffentlichen, wodurch die Entwurfsversion die aktuell veröffentlichte Version überschreibt.

### <a name="publish-a-runbook-in-the-azure-portal"></a>Veröffentlichen eines Runbooks im Azure-Portal

1. Suchen Sie im Azure-Portal nach **Automation-Konten** und wählen Sie diese Option aus.
1. Wählen Sie auf der Seite **Automation-Konten** in der entsprechenden Liste Ihr Automation-Konto aus.
1. Öffnen Sie das Runbook in Ihrem Automation-Konto.
1. Klicken Sie auf **Bearbeiten**.
1. Klicken Sie auf **Veröffentlichen**, und wählen Sie dann als Antwort in der Bestätigungsmeldung die Option **Ja** aus.

### <a name="publish-a-runbook-using-powershell"></a>Veröffentlichen eines Runbooks mithilfe von PowerShell

Verwenden Sie das Cmdlet [Publish-AzAutomationRunbook](/powershell/module/Az.Automation/Publish-AzAutomationRunbook), um Ihr Runbook zu veröffentlichen. 

```azurepowershell-interactive
$accountName = "MyAutomationAccount"
$runbookName = "Sample_TestRunbook"
$rgName = "MyResourceGroup"

$publishParams = @{
    AutomationAccountName = $accountName
    ResourceGroupName     = $rgName
    Name                  = $runbookName
}
Publish-AzAutomationRunbook @publishParams
```

## <a name="schedule-a-runbook-in-the-azure-portal"></a>Erstellen eines Zeitplans für ein Runbook im Azure-Portal

Nachdem Ihr Runbook veröffentlicht wurde, können Sie einen Zeitplan für seinen Betrieb erstellen:

1. Suchen Sie im Azure-Portal nach **Automation-Konten** und wählen Sie diese Option aus.
1. Wählen Sie auf der Seite **Automation-Konten** in der entsprechenden Liste Ihr Automation-Konto aus.
1. Wählen Sie das Runbook aus der Liste der Runbooks aus.
1. Wählen Sie unter **Ressourcen** die Option **Zeitpläne** aus.
1. Wählen Sie **Zeitplan hinzufügen** aus.
1. Wählen Sie im Bereich **Runbook planen** die Option **Zeitplan mit Runbook verknüpfen** aus.
1. Wählen Sie im Bereich **Zeitplan** die Option **Neuen Zeitplan erstellen** aus.
1. Geben Sie im Bereich **Neuer Zeitplan** einen Namen, eine Beschreibung und andere Parameter ein.
1. Nachdem der Zeitplan erstellt wurde, markieren Sie ihn, und klicken Sie auf **OK**. Er sollte jetzt mit Ihrem Runbook verknüpft sein.
1. Suchen Sie in Ihrem Postfach nach einer E-Mail, die Sie über den Status des Runbooks informiert.

## <a name="obtain-job-statuses"></a>Abrufen des Auftragsstatus

### <a name="view-statuses-in-the-azure-portal"></a>Anzeigen von Status im Azure-Portal

Details zur Auftragsbehandlung in Azure Automation werden in [Aufträgen](automation-runbook-execution.md#jobs) bereitgestellt. Wenn Sie Ihre Runbookaufträge anzeigen möchten, greifen Sie im Azure-Portal auf Ihr Automation-Konto zu. Auf der rechten Seite wird unter **Auftragsstatistik** eine Zusammenfassung aller Runbookaufträge angezeigt.

![Die Kachel „Auftragsstatistik“](./media/manage-runbooks/automation-account-job-status-summary.png)

In der Zusammenfassung wird die Anzahl aller ausgeführten Aufträge neben einer grafischen Darstellung des Auftragsstatus angezeigt.

Wenn Sie auf die Kachel klicken, wird die Seite „Aufträge“ angezeigt, die eine Liste mit einer Zusammenfassung aller ausgeführten Aufträge enthält. Auf dieser Seite werden der Status, Runbookname sowie die Start- und Abschlusszeit jedes Auftrags angezeigt.

:::image type="content" source="./media/manage-runbooks/automation-account-jobs-status-blade.png" alt-text="Screenshot der Seite „Aufträge“.":::

Sie können die Auftragsliste filtern, indem Sie **Aufträge filtern** auswählen. Filtern Sie anhand eines bestimmten Runbooks, eines Auftragsstatus oder einer Option in der Dropdownliste, und geben Sie die Zeitspanne für die Suche an.

![Filtern des Auftragsstatus](./media/manage-runbooks/automation-account-jobs-filter.png)

Alternativ können Sie für ein bestimmtes Runbook eine Auftragszusammenfassung anzeigen, indem Sie in Ihrem Automation-Konto zuerst auf der Seite „Runbooks“ das Runbook und dann **Aufträge** auswählen. Mit dieser Aktion wird die Seite „Aufträge“ angezeigt. Wenn Sie dort auf einen Auftragsdatensatz klicken, werden die zugehörigen Details und Ausgaben angezeigt.

:::image type="content" source="./media/manage-runbooks/automation-runbook-job-summary-blade.png" alt-text="Screenshot der Seite „Aufträge“ mit hervorgehobener Schaltfläche „Fehler“.":::

### <a name="retrieve-job-statuses-using-powershell"></a>Abrufen des Auftragsstatus mithilfe von PowerShell

Verwenden Sie das Cmdlet [Get-AzureAutomationJob](/powershell/module/Az.Automation/Get-AzAutomationJob), um die für ein Runbook erstellten Aufträge und die Details zu einem bestimmten Auftrag anzuzeigen. Wenn Sie ein Runbook mit `Start-AzAutomationRunbook` starten, wird der entsprechende Auftrag zurückgegeben. Verwenden Sie [Get-AzAutomationJobOutput](/powershell/module/Az.Automation/Get-AzAutomationJobOutput), um die Auftragsausgabe abzurufen.

Die folgenden Beispielbefehle rufen den letzten Auftrag für ein Beispielrunbook ab und zeigen seinen Status, die für die Runbookparameter bereitgestellten Werte und die Auftragsausgabe an.

```azurepowershell-interactive
$getJobParams = @{
    AutomationAccountName = 'MyAutomationAccount'
    ResourceGroupName     = 'MyResourceGroup'
    Runbookname           = 'Test-Runbook'
}
$job = (Get-AzAutomationJob @getJobParams | Sort-Object LastModifiedDate -Desc)[0]
$job | Select-Object JobId, Status, JobParameters

$getOutputParams = @{
    AutomationAccountName = 'MyAutomationAccount'
    ResourceGroupName     = 'MyResourceGroup'
    Id                    = $job.JobId
    Stream                = 'Output'
}
Get-AzAutomationJobOutput @getOutputParams
```

Das folgende Beispiel ruft die Ausgabe für einen bestimmten Auftrag ab und gibt die einzelnen Datensätze zurück. Wenn es eine [Ausnahme](automation-runbook-execution.md#exceptions) für einen der Datensätze gibt, gibt das Skript die Ausnahme anstelle des Werts aus. Dieses Verhalten ist hilfreich, da Ausnahmen zusätzliche Informationen liefern können, die bei der Ausgabe normalerweise nicht protokolliert werden.

```azurepowershell-interactive
$params = @{
    AutomationAccountName = 'MyAutomationAccount'
    ResourceGroupName     = 'MyResourceGroup'
    Stream                = 'Any'
}
$output = Get-AzAutomationJobOutput @params

foreach ($item in $output) {
    $jobOutParams = @{
        AutomationAccountName = 'MyAutomationAccount'
        ResourceGroupName     = 'MyResourceGroup'
        Id                    = $item.StreamRecordId
    }
    $fullRecord = Get-AzAutomationJobOutputRecord @jobOutParams

    if ($fullRecord.Type -eq 'Error') {
        $fullRecord.Value.Exception
    } else {
        $fullRecord.Value
    }
}
```

## <a name="next-steps"></a>Nächste Schritte

* Einzelheiten zur Runbookverwaltung finden Sie unter [Ausführen von Runbooks in Azure Automation](automation-runbook-execution.md).
* Informationen zum Vorbereiten eines PowerShell-Runbooks finden Sie unter [Bearbeiten von Textrunbooks in Azure Automation](automation-edit-textual-runbook.md).
* Informationen zum Behandeln von Problemen bei der Runbookausführung finden Sie unter [Beheben von Runbookproblemen](troubleshoot/runbooks.md).
