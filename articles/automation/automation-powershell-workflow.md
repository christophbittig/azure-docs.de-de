---
title: Grundlagen des PowerShell-Workflows für Azure Automation
description: In diesem Artikel werden die Unterschiede zwischen PowerShell-Workflow und PowerShell sowie Konzepte erläutert, die für Automation-Runbooks gelten.
services: automation
ms.subservice: process-automation
ms.date: 10/29/2018
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: ee42e5aefd457c79fdf82ff388724dd9983c57b9
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131432527"
---
# <a name="learn-powershell-workflow-for-azure-automation"></a>Grundlagen des PowerShell-Workflows für Azure Automation

Runbooks in Azure Automation werden als Windows PowerShell-Workflows implementiert, Windows PowerShell-Skripts, die Windows Workflow Foundation verwenden. Bei einem Workflow handelt es sich um eine Sequenz von programmierten, zusammenhängenden Schritten, mit denen zeitaufwendige Aufgaben ausgeführt werden oder für die mehrere Schritte auf mehreren Geräten oder verwalteten Knoten koordiniert werden müssen. 

Wenngleich ein Workflow mit Windows PowerShell-Syntax geschrieben und über Windows PowerShell gestartet wird, erfolgt die Verarbeitung durch Windows Workflow Foundation. Die Vorteile eines Workflows gegenüber einem normalen Skript liegen darin, dass eine Aktion gleichzeitig für mehrere Geräte ausgeführt und bei Auftreten von Fehlern eine automatische Wiederherstellung durchgeführt wird. 

> [!NOTE]
>  Dieser Artikel gilt für PowerShell 5.1. PowerShell 7.1 (Vorschauversion) unterstützt keine Workflows. Ein PowerShell-Workflow-Skript ähnelt stark einem Windows PowerShell-Skript, weist aber einige wesentliche Unterschiede auf, die für einen neuen Benutzer verwirrend sein können. Daher sollten Sie Ihre Runbooks nur mit dem PowerShell-Workflow schreiben, wenn Sie [Prüfpunkte](#use-checkpoints-in-a-workflow) verwenden müssen. 
 

Ausführliche Informationen zu den Themen in diesem Artikel finden Sie unter [Erste Schritte mit dem Windows PowerShell-Workflow](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj134242(v=ws.11)).

## <a name="use-workflow-keyword"></a>Verwenden des Workflow-Schlüsselworts

Der erste Schritt beim Konvertieren eines PowerShell-Skripts in einen PowerShell-Workflow besteht im Umschließen mit dem `Workflow`-Schlüsselwort. Ein Workflow beginnt mit dem Schlüsselwort `Workflow`, gefolgt vom Hauptteil des Skripts, der in Klammern gesetzt ist. Auf das Schlüsselwort `Workflow` folgt der Name des Workflows, wie in der folgenden Syntax gezeigt:

```powershell
Workflow Test-Workflow
{
    <Commands>
}
```

Der Name des Workflows muss mit dem Namen des Automation-Runbooks übereinstimmen. Wenn das Runbook importiert wird, muss der Dateiname mit dem Workflownamen übereinstimmen und auf **.ps1** enden.

Zum Hinzufügen von Parametern für den Workflow verwenden Sie das Schlüsselwort `Param` genauso wie bei einem Skript.

## <a name="learn-differences-between-powershell-workflow-code-and-powershell-script-code"></a>Weitere Informationen zu den Unterschieden zwischen PowerShell-Workflow-Code und PowerShell-Skriptcode

Der PowerShell-Workflowcode sieht bis auf einige signifikante Änderungen fast genauso wie PowerShell-Skriptcode aus. In den folgenden Abschnitten werden Änderungen beschrieben, die Sie an einem PowerShell-Skript vornehmen müssen, damit es in einem Workflow ausgeführt werden kann.

### <a name="activities"></a>activities

Eine Aktivität ist ein bestimmter Task in einem Workflow, der in einer Sequenz ausgeführt wird. Windows PowerShell Workflow konvertiert viele der Windows PowerShell-Cmdlets automatisch in Aktivitäten, wenn ein Workflow ausgeführt wird. Wenn Sie eines dieser Cmdlets in Ihrem Runbook angeben, wird von Windows Workflow Foundation die entsprechende Aktivität ausgeführt. 

Ein Cmdlet ohne entsprechende Aktivität führt Windows PowerShell-Workflow automatisch in einer [InlineScript](#use-inlinescript)-Aktivität aus. Einige Cmdlets sind hiervon ausgeschlossen und können nicht in einem Workflow verwendet werden – es sei denn, Sie schließen diese Cmdlets explizit in einen InlineScript-Block ein. Weitere Informationen zu Aktivitäten finden Sie unter [Verwenden von Aktivitäten in Skriptworkflows](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj574194(v=ws.11)).

Workflowaktivitäten teilen sich einen Satz allgemeiner Parameter, um ihren Betrieb zu konfigurieren. Siehe [about_WorkflowCommonParameters](/powershell/module/psworkflow/about/about_workflowcommonparameters).

### <a name="positional-parameters"></a>Positionsparameter

Sie können Positionsparameter nicht mit Aktivitäten und Cmdlets in einem Workflow verwenden. Daher müssen Sie Parameternamen verwenden. Betrachten Sie den folgenden Code, mit dem alle ausgeführten Dienste abgerufen werden:

```azurepowershell-interactive
Get-Service | Where-Object {$_.Status -eq "Running"}
```

Wenn Sie versuchen, diesen Code in einem Workflow auszuführen, erhalten Sie eine Meldung wie `Parameter set cannot be resolved using the specified named parameters.`. Um dieses Problem zu beheben, geben Sie den Parameternamen an, wie im folgenden Beispiel gezeigt:

```powershell
Workflow Get-RunningServices
{
    Get-Service | Where-Object -FilterScript {$_.Status -eq "Running"}
}
```

### <a name="deserialized-objects"></a>Deserialisierte Objekte

Objekte in Workflows sind deserialisiert, was bedeutet, dass ihre Eigenschaften weiterhin verfügbar sind, aber nicht ihre Methoden.  Sehen Sie sich beispielsweise den folgenden PowerShell-Code an, der einen Dienst mit der `Stop`-Methode des `Service`-Objekts beendet.

```azurepowershell-interactive
$Service = Get-Service -Name MyService
$Service.Stop()
```

Wenn Sie versuchen, dies in einem Workflow auszuführen, erhalten Sie die Fehlermeldung `Method invocation is not supported in a Windows PowerShell Workflow.`

Eine Möglichkeit ist, diese beiden Codezeilen in einen [InlineScript](#use-inlinescript)-Block einzuschließen. In diesem Fall stellt `Service` ein Dienstobjekt innerhalb des Blocks dar.

```powershell
Workflow Stop-Service
{
    InlineScript {
        $Service = Get-Service -Name MyService
        $Service.Stop()
    }
}
```

Eine weitere Möglichkeit ist die Verwendung eines anderen Cmdlets, mit dem die gleichen Funktionen wie mit der Methode durchgeführt werden (sofern verfügbar). In unserem Beispiel werden mit dem `Stop-Service`-Cmdlet die gleichen Funktionen wie mit der `Stop`-Methode bereitgestellt, und Sie könnten den folgenden Code für einen Workflow verwenden.

```powershell
Workflow Stop-MyService
{
    $Service = Get-Service -Name MyService
    Stop-Service -Name $Service.Name
}
```

## <a name="use-inlinescript"></a>Verwenden von InlineScript

Die `InlineScript`-Aktivität ist nützlich, wenn Sie einen oder mehrere Befehle als herkömmliches PowerShell-Skript ausführen müssen, anstatt als PowerShell-Workflow.  Wenngleich die Befehle in einem Workflow zur Verarbeitung an Windows Workflow Foundation gesendet werden, werden die Befehle in einem InlineScript-Block durch Windows PowerShell verarbeitet.

Der InlineScript-Block verwendet die nachstehende Syntax.

```powershell
InlineScript
{
    <Script Block>
} <Common Parameters>
```

Sie können die Ausgabe eines InlineScript-Blocks zurückgeben, indem Sie die Ausgabe einer Variablen zuweisen. Im unten angegebenen Beispiel wird ein Dienst beendet und anschließend der Dienstname ausgegeben.

```powershell
Workflow Stop-MyService
{
    $Output = InlineScript {
        $Service = Get-Service -Name MyService
        $Service.Stop()
        $Service
    }

    $Output.Name
}
```

Sie können Werte in einen InlineScript-Block übergeben, aber Sie müssen den **$Using** -Bereichsmodifizierer verwenden.  Das folgende Beispiel ist mit dem vorherigen Beispiel identisch, mit der Ausnahme, dass der Dienstname über eine Variable bereitgestellt wird.

```powershell
Workflow Stop-MyService
{
    $ServiceName = "MyService"

    $Output = InlineScript {
        $Service = Get-Service -Name $Using:ServiceName
        $Service.Stop()
        $Service
    }

    $Output.Name
}
```

Obwohl InlineScript-Aktivitäten in bestimmten Workflows möglicherweise kritisch sind, unterstützen sie keine Workflowkonstrukte. Sie sollten sie nur verwenden, wenn dies aus folgenden Gründen erforderlich ist:

* Sie können keine [Prüfpunkte](#use-checkpoints-in-a-workflow) in einem InlineScript-Block verwenden. Wenn innerhalb des Blocks ein Fehler auftritt, muss der Vorgang am Anfang des Blocks neu gestartet werden.
* Sie können in einem InlineScript-Block keine [parallele Ausführung](#use-parallel-processing) nutzen.
* InlineScript-Blöcke beeinträchtigen die Skalierbarkeit des Workflows, da die Windows PowerShell-Sitzung für die gesamte Dauer des InlineScript-Blocks übernommen wird.

Weitere Informationen zur Verwendung von InlineScript finden Sie unter [Ausführen von Windows PowerShell-Befehlen in einem Workflow](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj574197(v=ws.11)) und [about_InlineScript](/powershell/module/psworkflow/about/about_inlinescript).

## <a name="use-parallel-processing"></a>Verwenden der Parallelverarbeitung

Ein Vorteil von Windows PowerShell-Workflows besteht darin, dass sie einen Satz an Befehlen parallel – und nicht wie in einem typischen Skript sequenziell – ausführen können.

Sie können mit dem Schlüsselwort `Parallel` einen Skriptblock mit mehreren Befehlen erstellen, die gleichzeitig ausgeführt werden. Dabei wird die nachstehende Syntax verwendet. In diesem Fall werden Activity1 und Activity2 gleichzeitig gestartet. Activity3 wird erst gestartet, wenn sowohl Activity1 als auch Activity2 abgeschlossen wurden.

```powershell
Parallel
{
    <Activity1>
    <Activity2>
}
<Activity3>
```

Sehen Sie sich beispielsweise die folgenden PowerShell-Befehle an, mit denen mehrere Dateien an ein Netzwerkziel kopiert werden. Diese Befehle werden nacheinander ausgeführt, sodass der Kopiervorgang einer Datei abgeschlossen sein muss, bevor der nächste Vorgang gestartet wird.

```azurepowershell-interactive
Copy-Item -Path C:\LocalPath\File1.txt -Destination \\NetworkPath\File1.txt
Copy-Item -Path C:\LocalPath\File2.txt -Destination \\NetworkPath\File2.txt
Copy-Item -Path C:\LocalPath\File3.txt -Destination \\NetworkPath\File3.txt
```

Im folgenden Workflow werden die gleichen Befehle parallel ausgeführt, sodass die Kopiervorgänge alle gleichzeitig beginnen.  Die Abschlussmeldung wird erst angezeigt, nachdem alle Kopiervorgänge stattgefunden haben.

```powershell
Workflow Copy-Files
{
    Parallel
    {
        Copy-Item -Path "C:\LocalPath\File1.txt" -Destination "\\NetworkPath"
        Copy-Item -Path "C:\LocalPath\File2.txt" -Destination "\\NetworkPath"
        Copy-Item -Path "C:\LocalPath\File3.txt" -Destination "\\NetworkPath"
    }

    Write-Output "Files copied."
}
```

Sie können das Konstrukt `ForEach -Parallel` verwenden, um Befehle für jedes Element in einer Auflistung gleichzeitig zu verarbeiten. Die Elemente in der Auflistung werden parallel ausgeführt, während die Befehle im Skriptblock sequenziell ausgeführt werden. In diesem Prozess wird die nachstehende Syntax verwendet. In diesem Fall wird Activity1 für alle Elemente in der Sammlung gleichzeitig gestartet. Activity2 wird für alle Elemente gestartet, nachdem Activity1 abgeschlossen wurde. Activity3 wird erst gestartet, wenn sowohl Activity1 als auch Activity2 für alle Elemente abgeschlossen wurden. Wir verwenden den Parameter `ThrottleLimit`, um die Parallelität zu beschränken. Ein zu hoher Wert für `ThrottleLimit` kann Probleme verursachen. Der ideale Wert des Parameters `ThrottleLimit` hängt von vielen Faktoren in Ihrer Umgebung ab. Beginnen Sie mit einem niedrigen Wert und probieren Sie verschiedene ansteigende Werte aus, bis Sie einen finden, der für Ihre individuellen Umstände geeignet ist.

```powershell
ForEach -Parallel -ThrottleLimit 10 ($<item> in $<collection>)
{
    <Activity1>
    <Activity2>
}
<Activity3>
```

Das folgende Beispiel ähnelt dem vorherigen Beispiel mit dem parallelen Kopieren der Dateien.  In diesem Fall wird für jede Datei nach Abschluss des Kopiervorgangs eine Meldung angezeigt.  Die Abschlussmeldung wird erst angezeigt, nachdem alle Kopiervorgänge stattgefunden haben.

```powershell
Workflow Copy-Files
{
    $files = @("C:\LocalPath\File1.txt","C:\LocalPath\File2.txt","C:\LocalPath\File3.txt")

    ForEach -Parallel -ThrottleLimit 10 ($File in $Files)
    {
        Copy-Item -Path $File -Destination \\NetworkPath
        Write-Output "$File copied."
    }

    Write-Output "All files copied."
}
```

> [!NOTE]
> Wir raten davon ab, untergeordnete Runbooks parallel auszuführen, da dies häufig zu unzuverlässigen Ergebnissen führt. Die Ausgabe des untergeordneten Runbooks wird in einigen Fällen nicht angezeigt, und die Einstellungen in einem untergeordneten Runbook können sich auf andere untergeordnete Runbooks auswirken. Variablen wie `VerbosePreference`, `WarningPreference` und andere können möglicherweise nicht an die untergeordneten Runbooks weitergegeben werden. Und wenn das untergeordnete Runbook diese Werte ändert, werden sie möglicherweise nach dem Aufruf nicht ordnungsgemäß wiederhergestellt.

## <a name="use-checkpoints-in-a-workflow"></a>Verwenden von Prüfpunkten in einem Workflow

Ein Prüfpunkt ist eine Momentaufnahme des aktuellen Zustands des Workflows, der die aktuellen Werte für Variablen und sämtliche Ausgaben einschließt, die bis zu diesem Punkt generiert wurden. Wenn ein Workflow mit einem Fehler endet oder angehalten wird, startet er bei der nächsten Ausführung am letzten Prüfpunkt, anstatt am Anfang zu beginnen. 

Sie können mithilfe der Aktivität `Checkpoint-Workflow` einen Prüfpunkt in einem Workflow setzen. Azure Automation bietet ein Feature namens [gleichmäßige Auslastung](automation-runbook-execution.md#fair-share), bei dem jedes Runbook, das drei Stunden lang ausgeführt wird, entladen wird, um anderen Runbooks die Ausführung zu ermöglichen. Schließlich wird das entladene Runbook erneut geladen. Wenn dies der Fall ist, wird die Ausführung ab dem letzten Prüfpunkt im Runbook fortgesetzt.

Um zu garantieren, dass das Runbook schließlich abgeschlossen wird, müssen Sie Prüfpunkte in Abständen hinzufügen, die kürzer als 3 Stunden Ausführungsdauer sind. Wenn während jeder Ausführung ein neuer Prüfpunkt hinzugefügt wird, und wenn das Runbook nach drei Stunden wegen eines Fehlers entfernt wird, wird das Runbook unendlich fortgesetzt.

Im folgenden Beispiel führt eine Ausnahme nach „Activity2“ dazu, dass der Workflow beendet wird. Bei der Fortsetzung der Ausführung wird zunächst „Activity2“ ausgeführt, da diese Aktivität unmittelbar auf den zuletzt gesetzten Prüfpunkt folgt.

```powershell
<Activity1>
Checkpoint-Workflow
<Activity2>
<Exception>
<Activity3>
```

Setzen Sie Prüfpunkte in einem Workflow nach Aktivitäten, die möglicherweise anfällig für das Auftreten von Ausnahmen sind und nach dem Fortsetzen eines Workflows nicht wiederholt werden sollen. Angenommen, Ihr Workflow erstellt einen virtuellen Computer. Sie könnten sowohl vor als auch nach den Befehlen zum Erstellen des virtuellen Computers einen Prüfpunkt setzen. Wenn bei der Erstellung ein Fehler auftritt, werden die Befehle wiederholt, wenn der Workflow erneut gestartet wird. Falls für den Workflow nach der erfolgreichen Erstellung ein Fehler auftritt, wird der virtuelle Computer nicht erneut erstellt, wenn der Workflow fortgesetzt wird.

Im folgenden Beispiel werden mehrere Dateien an einen Netzwerkspeicherort kopiert, und nach jeder Datei wird ein Prüfpunkt gesetzt.  Wenn die Verbindung mit dem Netzwerkspeicherort verloren geht, wird der Workflow mit einem Fehler beendet.  Beim erneuten Starten wird der Vorgang am letzten Prüfpunkt fortgesetzt. Nur die Dateien, die bereits kopiert wurden, werden übersprungen.

```powershell
Workflow Copy-Files
{
    $files = @("C:\LocalPath\File1.txt","C:\LocalPath\File2.txt","C:\LocalPath\File3.txt")

    ForEach ($File in $Files)
    {
        Copy-Item -Path $File -Destination \\NetworkPath
        Write-Output "$File copied."
        Checkpoint-Workflow
    }

    Write-Output "All files copied."
}
```

Da Benutzernamen aus Anmeldeinformationen nicht über das Aufrufen der Aktivität [Suspend-Workflow](/powershell/module/psworkflow/about/about_suspend-workflow) bzw. über den letzten Prüfpunkt hinaus gespeichert werden, müssen Sie die Anmeldeinformationen auf NULL festlegen und sie nach dem Aufrufen der Aktivität `Suspend-Workflow` oder des letzten Prüfpunkts erneut aus dem Objektspeicher abrufen.  Sie erhalten andernfalls unter Umständen die folgende Fehlermeldung: `The workflow job cannot be resumed, either because persistence data could not be saved completely, or saved persistence data has been corrupted. You must restart the workflow.`

Der folgende Code veranschaulicht die Behandlung dieser Situation in PowerShell-Workflow-Runbooks.

```powershell
workflow CreateTestVms
{
    $Cred = Get-AzAutomationCredential -Name "MyCredential"
    $null = Connect-AzAccount -Credential $Cred

    $VmsToCreate = Get-AzAutomationVariable -Name "VmsToCreate"

    foreach ($VmName in $VmsToCreate)
        {
        # Do work first to create the VM (code not shown)

        # Now add the VM
        New-AzVM -VM $Vm -Location "WestUs" -ResourceGroupName "ResourceGroup01"

        # Checkpoint so that VM creation is not repeated if workflow suspends
        $Cred = $null
        Checkpoint-Workflow
        $Cred = Get-AzAutomationCredential -Name "MyCredential"
        $null = Connect-AzAccount -Credential $Cred
        }
}
```

> [!NOTE]
> Für nicht grafische PowerShell-Runbooks sind `Add-AzAccount` und `Add-AzureRMAccount` Aliase für [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount). Sie können diese Cmdlets verwenden, oder Sie können Ihre Module in Ihrem Automation-Konto auf die aktuellen Versionen [aktualisieren](automation-update-azure-modules.md). Möglicherweise müssen Sie Ihre Module auch dann aktualisieren, wenn Sie gerade ein neues Automation-Konto erstellt haben. Die Verwendung dieser Cmdlets ist nicht erforderlich, wenn die Authentifizierung mithilfe eines ausführenden Kontos erfolgt, das mit einem Dienstprinzipal konfiguriert ist.

Weitere Informationen zu Prüfpunkten finden Sie unter [Hinzufügen von Prüfpunkten zu einem Skriptworkflow](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj574114(v=ws.11)).

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu PowerShell-Workflow-Runbooks finden Sie unter [Tutorial: Erstellen eines PowerShell-Workflow-Runbooks](learn/automation-tutorial-runbook-textual.md).
