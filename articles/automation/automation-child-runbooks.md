---
title: Erstellen modularer Runbooks in Azure Automation
description: In diesem Artikel erfahren Sie, wie Sie ein Runbook erstellen, das von einem anderen Runbook aufgerufen wird.
services: automation
ms.subservice: process-automation
ms.date: 10/29/2021
ms.topic: how-to
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 4eff80637886901644b36b19f74d79ae83e7cfe3
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131455490"
---
# <a name="create-modular-runbooks-in-automation"></a>Modulare Runbooks in Automation erstellen

Eine bewährte Methode in Azure Automation ist das Erstellen von wiederverwendbaren, modularen Runbooks mit einer diskreten Funktion, die andere Runbooks aufrufen. Ein übergeordnetes Runbook ruft häufig untergeordnete Runbooks zum Durchführen erforderlicher Funktionen auf. 

Es gibt zwei Möglichkeiten zum Aufrufen eines untergeordneten Runbooks: inline oder über ein Cmdlet. In der folgenden Tabelle sind die Unterschiede zusammengefasst, die Ihnen bei der Entscheidung helfen, welche Methode für Ihre Szenarien besser ist.

|  | Inline | Cmdlet |
|:--- |:--- |:--- |
| **Job** |Untergeordnete Runbooks werden im selben Auftrag ausgeführt wie das übergeordnete Runbook. |Für das untergeordnete Runbook wird ein separater Auftrag erstellt. |
| **Ausführung** |Das übergeordnete Runbook wird erst nach Abschluss des untergeordneten Runbooks fortgesetzt. |Das übergeordnete Runbook wird sofort fortgesetzt, nachdem das untergeordnete Runbook gestartet wurde, *oder* das übergeordnete Runbook wartet, bis der untergeordnete Auftrag abgeschlossen ist. |
| **Ausgabe** |Ausgaben des untergeordneten Runbooks werden direkt an das übergeordnete Runbook zurückgegeben. |Das übergeordnete Runbook muss die Ausgabe aus dem untergeordneten Runbookauftrag abrufen, *oder* das übergeordnete Runbook kann die Ausgabe direkt aus dem untergeordneten Runbook abrufen. |
| **Parameter** |Werte für die Parameter des untergeordneten Runbooks werden separat angegeben, und es können beliebige Datentypen verwendet werden. |Werte für die Parameter des untergeordneten Runbooks müssen in einer einzigen Hashtabelle kombiniert werden. In dieser Hashtabelle können nur einfache, Array- und Objektdatentypen, die JSON-Serialisierung nutzen, gespeichert werden. |
| **Automation-Konto** |Das übergeordnete Runbook kann nur im gleichen Automation-Konto enthaltene untergeordnete Runbooks verwenden. |Übergeordnete Runbooks können untergeordnete Runbooks aus allen Automation-Konten des gleichen Azure-Abonnements und sogar eines anderen Abonnements verwenden, mit dem eine Verbindung besteht. |
| **Veröffentlichung** |Ein untergeordnetes Runbook muss vor der Veröffentlichung des übergeordneten Runbooks veröffentlicht werden. |Ein untergeordnetes Runbook kann jederzeit veröffentlicht werden, bevor das übergeordnete Runbook gestartet wird. |

## <a name="call-a-child-runbook-by-using-inline-execution"></a>Aufrufen eines untergeordneten Runbooks mittels Inlineausführung

Um ein Runbook inline in einem anderen Runbook aufzurufen, verwenden Sie den Namen des Runbooks und geben Werte für die zugehörigen Parameter wie bei einer Aktivität oder einem Cmdlet an. Alle Runbooks im selben Automation-Konto können auf diese Weise von allen anderen Runbooks verwendet werden. Das übergeordnete Runbook wartet auf den Abschluss des untergeordneten Runbooks, bevor es mit der nächsten Zeile fortfährt, und sämtliche Ausgaben werden direkt an das übergeordnete Runbook zurückgegeben.

Wenn Sie ein Runbook inline aufrufen, wird es im selben Auftrag ausgeführt wie das übergeordnete Runbook. Das untergeordnete Runbook wird nicht im Auftragsverlauf angezeigt. Sämtliche Ausnahmen und Datenstromausgaben des untergeordneten Runbooks sind dem übergeordneten Runbook zugeordnet. Dieses Verhalten führt zu weniger Aufträgen und erleichtert die Nachverfolgung und Problembehandlung.

Beim Veröffentlichen eines Runbooks müssen alle von ihm aufgerufenen untergeordneten Runbooks bereits veröffentlicht sein. Der Grund: Azure Automation erstellt beim Kompilieren eines Runbooks eine Zuordnung zu allen untergeordneten Runbooks. Wenn die untergeordneten Runbooks noch nicht veröffentlicht wurden, scheint das übergeordnete Runbook ordnungsgemäß zu veröffentlichen, erzeugt aber eine Ausnahme, wenn es gestartet wird. 

Im Fall einer Ausnahme können Sie das übergeordnete Runbook erneut veröffentlichen, um korrekt auf die untergeordneten Runbooks zu verweisen. Sie brauchen das übergeordnete Runbook nicht erneut zu veröffentlichen, wenn ein untergeordnetes Runbook geändert wird, da die Verknüpfung bereits erstellt wurde.

Die Parameter eines inline aufgerufenen untergeordneten Runbooks können einen beliebigen Datentyp aufweisen (einschließlich komplexer Objekte). Es gibt keine [JSON-Serialisierung](start-runbooks.md#work-with-runbook-parameters) wie beim Starten des Runbooks über das Azure-Portal oder mit dem Cmdlet [Start-AzAutomationRunbook](/powershell/module/Az.Automation/Start-AzAutomationRunbook).

### <a name="runbook-types"></a>Runbooktypen

Derzeit werden PowerShell 5.1 und 7.1 (Vorschauversion) unterstützt, und nur bestimmte Runbooktypen können sich gegenseitig aufrufen:

* Ein [PowerShell-Runbook](automation-runbook-types.md#powershell-runbooks) und ein [grafisches Runbook](automation-runbook-types.md#graphical-runbooks) können sich gegenseitig inline aufrufen, da beide auf PowerShell basieren.
* Ein [PowerShell-Workflow-Runbook](automation-runbook-types.md#powershell-workflow-runbooks) und ein grafisches PowerShell-Workflow-Runbook können sich gegenseitig inline aufrufen, da beide auf einem PowerShell-Workflow basieren.
* PowerShell-Typen und PowerShell-Workflowtypen können sich nicht gegenseitig inline aufrufen. Sie müssen `Start-AzAutomationRunbook` verwenden.

Die Veröffentlichungsreihenfolge von Runbooks spielt nur für PowerShell-Workflow- und grafische PowerShell-Workflow-Runbooks eine Rolle.

Wenn Ihr Runbook ein untergeordnetes grafisches Runbook oder ein untergeordnetes PowerShell-Workflow-Runbook mit Inlineausführung aufruft, verwendet es den Namen des Runbooks. Der Name muss mit `.\\` beginnen, um anzugeben, dass sich das Skript im lokalen Verzeichnis befindet.

### <a name="example"></a>Beispiel

Im folgenden Beispiel wird ein untergeordnetes Testrunbook gestartet, das ein komplexes Objekt, eine ganze Zahl und einen booleschen Wert akzeptiert. Die Ausgabe des untergeordneten Runbooks wird einer Variablen zugewiesen. In diesem Fall ist das untergeordnete Runbook ein PowerShell-Workflow-Runbook.

```powershell
$vm = Get-AzVM -ResourceGroupName "LabRG" -Name "MyVM"
$output = PSWF-ChildRunbook -VM $vm -RepeatCount 2 -Restart $true
```

Hier ist das gleiche Beispiel mit einem PowerShell-Runbook als untergeordnetem Element.

```powershell
$vm = Get-AzVM -ResourceGroupName "LabRG" -Name "MyVM"
$output = .\PS-ChildRunbook.ps1 -VM $vm -RepeatCount 2 -Restart $true
```

## <a name="start-a-child-runbook-by-using-a-cmdlet"></a>Starten eines untergeordneten Runbooks mithilfe eines Cmdlets

> [!IMPORTANT]
> Wenn Ihr Runbook ein untergeordnetes Runbook mit dem Cmdlet `Start-AzAutomationRunbook`und dem Parameter `Wait` aufruft und das untergeordnete Runbook ein Objektergebnis erzeugt, tritt möglicherweise ein Fehler auf. Informationen zum Beheben des Fehlers finden Sie unter [Szenario: Objektverweis wurde nicht auf eine Objektinstanz festgelegt](troubleshoot/runbooks.md#child-runbook-object). In diesem Artikel erfahren Sie, wie Sie die Logik zum Abrufen der Ergebnisse mithilfe des Cmdlets [Get-AzAutomationJobOutputRecord](/powershell/module/az.automation/get-azautomationjoboutputrecord) implementieren.

Sie können `Start-AzAutomationRunbook` verwenden, um ein Runbook wie unter [Starten eines Runbooks mit Windows PowerShell](start-runbooks.md#start-a-runbook-with-powershell) beschrieben zu starten. Für die Verwendung dieses Cmdlets gibt es zwei Modi.

- Das Cmdlet gibt die Auftrags-ID zurück, sobald der Auftrag für das untergeordnete Runbook erstellt wird. 
- Das Cmdlet wartet, bis der untergeordnete Auftrag beendet ist, und gibt die Ausgabe des untergeordneten Runbooks zurück. Ihr Skript aktiviert diesen Modus durch Angabe des Parameters `Wait`.

Der Auftrag eines mit einem Cmdlet gestarteten untergeordneten Runbooks wird getrennt vom Auftrag des übergeordneten Runbooks ausgeführt. Im Vergleich zum Inlinestart des Runbooks führt dieses Verhalten zu mehr Aufträgen und erschwert deren Nachverfolgung. Das übergeordnete Runbook kann mehrere untergeordnete Runbooks asynchron starten, ohne auf ihren Abschluss zu warten. Für diese parallele Ausführung, bei der untergeordnete Runbooks inline aufgerufen werden, muss das übergeordnete Runbook das Schlüsselwort [Parallel](automation-powershell-workflow.md#use-parallel-processing) verwenden.

Die Ausgabe des Child-Runbooks kehrt aus Zeitgründen nicht zuverlässig zum Parent-Runbook zurück. Außerdem werden Variablen wie `$VerbosePreference`, `$WarningPreference` und andere möglicherweise nicht an die untergeordneten Runbooks weitergegeben. Um diese Probleme zu vermeiden, können Sie die untergeordneten Runbooks mithilfe von `Start-AzAutomationRunbook` mit dem Parameter `Wait` als separate Automation-Aufträge starten. Durch diese Technik wird das übergeordnete Runbook blockiert, bis das untergeordnete Runbook abgeschlossen wurde.

Wenn Sie nicht möchten, dass das übergeordnete Runbook beim Warten blockiert wird, können Sie das untergeordnete Runbook mit `Start-AzAutomationRunbook` ohne den Parameter `Wait` starten. In diesem Fall muss Ihr Runbook [Get-AzAutomationJob](/powershell/module/az.automation/get-azautomationjob) verwenden, um auf den Auftragsabschluss zu warten. Zum Abrufen der Ergebnisse müssen zudem [Get-AzAutomationJobOutput](/powershell/module/az.automation/get-azautomationjoboutput) und [Get-AzAutomationJobOutputRecord](/powershell/module/az.automation/get-azautomationjoboutputrecord) verwendet werden.

Parameter für ein per Cmdlet gestartetes untergeordnetes Runbook werden wie unter [Runbook-Parameter](start-runbooks.md#work-with-runbook-parameters) beschrieben als Hashtabelle bereitgestellt. Sie können nur einfache Datentypen verwenden. Enthält das Runbook einen Parameter mit einem komplexen Datentyp, muss es inline aufgerufen werden.

Beim Starten untergeordneter Runbooks als separate Aufträge geht möglicherweise der Abonnementkontext verloren. Damit das untergeordnete Runbook Cmdlets des Azure-Moduls für ein bestimmtes Azure-Abonnement ausführen kann, muss sich das untergeordnete Runbook unabhängig vom übergeordneten Runbook bei diesem Abonnement authentifizieren.

Wenn bei Aufträgen innerhalb des gleichen Automation-Kontos mehrere Abonnements verwendet werden, ändert sich durch die Auswahl eines Abonnements in einem Auftrag unter Umständen auch der aktuell ausgewählte Abonnementkontext für andere Aufträge. Verwenden Sie zur Vermeidung dieser Situation `Disable-AzContextAutosave -Scope Process` am Anfang jedes Runbooks. Bei dieser Aktion wird nur der Kontext der Runbookausführung gespeichert.

### <a name="example"></a>Beispiel

Im folgenden Beispiel wird ein untergeordnetes Runbook mit Parametern gestartet und auf den Abschluss des Runbooks gewartet. Hierzu wird das Cmdlet `Start-AzAutomationRunbook` mit dem Parameter `Wait` verwendet. Nach Abschluss des untergeordneten Runbooks erfasst das Beispiel die Cmdlet-Ausgabe aus dem untergeordneten Runbook. Das Skript muss sich bei Ihrem Azure-Abonnement authentifizieren, um `Start-AzAutomationRunbook` verwenden zu können.

```powershell
# Ensure that the runbook does not inherit an AzContext
Disable-AzContextAutosave -Scope Process

# Connect to Azure with system-assigned managed identity
$AzureContext = (Connect-AzAccount -Identity).context

# set and store context
$AzureContext = Set-AzContext -SubscriptionName $AzureContext.Subscription -DefaultProfile $AzureContext

$params = @{"VMName"="MyVM";"RepeatCount"=2;"Restart"=$true}

Start-AzAutomationRunbook `
    -AutomationAccountName 'MyAutomationAccount' `
    -Name 'Test-ChildRunbook' `
    -ResourceGroupName 'LabRG' `
    -DefaultProfile $AzureContext `
    -Parameters $params -Wait
```

Wenn Sie möchten, dass das Runbook mit der systemseitig zugewiesenen verwalteten Identität ausgeführt wird, lassen Sie den Code unverändert. Wenn Sie lieber eine benutzerseitig zugewiesene verwaltete Identität verwenden möchten, gehen Sie wie folgt vor:
1. Entfernen Sie `$AzureContext = (Connect-AzAccount -Identity).context` aus Zeile 5.
1. Fügen Sie stattdessen `$AzureContext = (Connect-AzAccount -Identity -AccountId <ClientId>).context` ein.
1. Geben Sie die Client-ID ein.

## <a name="next-steps"></a>Nächste Schritte

* Um Ihr Runbook auszuführen, siehe [Starten eines Runbooks in Azure Automation](start-runbooks.md).
* Informationen zum Überwachen von Runbookvorgängen finden Sie unter [Runbookausgabe und -meldungen in Azure Automation](automation-runbook-output-and-messages.md).
