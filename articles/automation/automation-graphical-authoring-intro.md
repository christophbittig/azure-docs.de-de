---
title: Erstellen von grafischen Azure Automation-Runbooks
description: In diesem Artikel erfahren Sie, wie Sie ein grafisches Runbook erstellen, ohne mit Code zu arbeiten.
services: automation
ms.subservice: process-automation
ms.date: 10/21/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2ce84a91fcb91be0326571a95cfa92fdfa3b33ef
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130220262"
---
# <a name="author-graphical-runbooks-in-azure-automation"></a>Erstellen von grafischen Azure Automation-Runbooks

Alle Runbooks in Azure Automation sind Windows PowerShell-Workflows. Grafische Runbooks und grafische PowerShell-Workflow-Runbooks generieren PowerShell-Code, den die Automation-Worker ausführen, Sie jedoch weder anzeigen noch ändern können. Sie können ein grafisches Runbook in ein grafisches PowerShell-Workflow-Runbook konvertieren und umgekehrt. Diese Runbooks können jedoch nicht in ein Textrunbook konvertiert werden. Außerdem kann der grafische Automatisierungs-Editor kein Textrunbook importieren.

Mithilfe der grafischen Erstellung können Sie ohne die Komplexität des zugrunde liegenden Windows PowerShell- oder PowerShell-Workflow-Codes Runbooks für Azure Automation erstellen. Sie können der Canvas Aktivitäten aus einer Bibliothek mit Cmdlets und Runbooks hinzufügen, Verknüpfungen erstellen und die Elemente konfigurieren, um einen Workflow zu erhalten. Wenn Sie schon einmal mit System Center Orchestrator oder Service Management Automation (SMA) gearbeitet haben, dürfte Ihnen die grafischen Erstellung bekannt vorkommen. Dieser Artikel bietet eine Einführung in die Konzepte, die Sie für den Einstieg in die Erstellung eines grafischen Runbooks benötigen.

> [!NOTE]
> Sie können einem grafischen Runbook keine digitale Signatur hinzufügen. Diese Funktion wird in Azure Automation nicht unterstützt.
>

## <a name="overview-of-graphical-editor"></a>Übersicht über den grafischen Editor

Sie können den grafischen Editor im Azure-Portal öffnen, indem Sie ein grafisches Runbook erstellen oder bearbeiten.

![Grafischer Arbeitsbereich](media/automation-graphical-authoring-intro/runbook-graphical-editor.png)

Die folgenden Abschnitte beschreiben die Steuerelemente im grafischen Editor.

### <a name="canvas-control"></a>Canvas-Steuerelement

Das Canvas-Steuerelement ermöglicht Ihnen, Ihr Runbook zu entwerfen. Sie können über die Knoten im Steuerelement „Bibliothek“ dem Runbook Aktivitäten hinzufügen und mit Verknüpfungen Verbindungen mit ihnen herstellen, um die Logik des Runbooks zu definieren. Über die Steuerelemente am unteren Rand der Canvas können Sie die Ansicht vergrößern und verkleinern.

### <a name="library-control"></a>Steuerelement "Bibliothek"

Über das Steuerelement "Bibliothek" wählen Sie die [Aktivitäten](#use-activities) aus, die Sie Ihrem Runbook hinzufügen möchten. Sie fügen diese der Canvas hinzu, wo Sie sie mit anderen Aktivitäten verbinden können. Das Steuerelement „Bibliothek“ enthält die in der folgenden Tabelle definierten Abschnitte.

| `Section` | BESCHREIBUNG |
|:--- |:--- |
| Cmdlets |Alle Cmdlets, die in Ihrem Runbook verwendet werden können. Die Cmdlets sind nach Modul angeordnet. Es sind alle Module verfügbar, die Sie in Ihrem Automation-Konto installiert haben. |
| Runbooks |Die Runbooks in Ihrem Automation-Konto. Sie können diese Runbooks der Canvas hinzufügen, damit sie als untergeordnete Runbooks verwendet werden. Es werden nur Runbooks angezeigt, deren Kerntyp mit dem des bearbeiteten Runbooks identisch ist. Für grafische Runbooks werden nur PowerShell-basierte Runbooks angezeigt. Für grafische PowerShell-Workflow-Runbooks werden nur Runbooks auf PowerShell-Workflow-Basis angezeigt. |
| Objekte |Die [Automation-Objekte](/previous-versions/azure/dn939988(v=azure.100)) in Ihrem Automation-Konto, die Sie in Ihrem Runbook verwenden können. Wenn Sie Ihrem Runbook ein Objekt hinzufügen, fügt es eine Workflowaktivität zum Abrufen des ausgewählten Objekts hinzu. Im Fall von Variablenobjekten können Sie auswählen, ob Sie eine Aktivität zum Abrufen der Variable oder eine Aktivität zum Festlegen der Variable hinzufügen möchten. |
| Steuerelement "Runbook" |Steuerelementaktivitäten, die im aktuellen Runbook verwendet werden können. Eine Aktivität vom Typ „Verbindung“ akzeptiert mehrere Eingaben und wartet, bis sämtliche dieser Eingaben abgeschlossen wurden, bevor der Workflow fortgesetzt wird. Eine Aktivität vom Typ „Code“ führt abhängig vom Typ des grafischen Runbooks mindestens eine PowerShell- oder PowerShell-Workflow-Codezeile aus. Sie können diese Aktivität für benutzerdefinierten Code oder für Funktionen nutzen, die sich mit anderen Aktivitäten nur schwer umsetzen lassen. |

### <a name="configuration-control"></a>Steuerelement "Konfiguration"

Mit dem Steuerelement „Konfiguration“ können Sie Details für ein auf der Canvas ausgewähltes Objekt angeben. Welche Eigenschaften in diesem Steuerelement zur Verfügung stehen, hängt von der Art des ausgewählten Objekts ab. Wenn Sie im Steuerelement „Konfiguration“ eine Option wählen, werden zusätzliche Blätter zum Angeben weiterer Informationen geöffnet.

### <a name="test-control"></a>Steuerelement "Test"

Das Steuerelement "Test" wird beim ersten Start des grafischen Editors nicht angezeigt. Es wird geöffnet, wenn Sie ein grafisches Runbook interaktiv testen.

## <a name="use-activities"></a>Verwenden von Aktivitäten

Aktivitäten sind die Bausteine eines Runbooks. Bei einer Aktivität kann es sich um ein PowerShell-Cmdlet, um ein untergeordnetes Runbook oder einen Workflow handeln. Sie können dem Runbook eine Aktivität hinzufügen, indem Sie mit der rechten Maustaste im Steuerelement „Bibliothek“ auf das Runbook klicken und die Option **Zur Canvas hinzufügen** auswählen. Anschließend können Sie die Aktivität per Drag & Drop an einer beliebigen Stelle auf der Canvas platzieren. Die Position der Aktivität auf der Canvas hat keinen Einfluss auf den Vorgang im Runbook. Sie können das Layout Ihres Runbooks so gestalten, wie es Ihnen zur visuellen Darstellung der Vorgänge am geeignetsten erscheint.

![Zum Zeichenbereich hinzufügen](media/automation-graphical-authoring-intro/add-to-canvas-cmdlet.png)

Wählen Sie eine Aktivität auf der Canvas aus, um ihre Eigenschaften und Parameter im Blatt „Konfiguration“ zu konfigurieren. Sie können die Bezeichnung der Aktivität in einen für Sie aussagekräftigen Namen ändern. Das Runbook führt weiterhin das ursprüngliche Cmdlet aus. Sie ändern einfach den Anzeigenamen, der im grafischen Editor verwendet wird. Beachten Sie, dass die Bezeichnung innerhalb des Runbooks eindeutig sein muss.

### <a name="parameter-sets"></a>Parametersätze

Ein Parametersatz definiert die erforderlichen und optionalen Parameter, die Werte für ein bestimmtes Cmdlet akzeptieren. Alle Cmdlets müssen über mindestens einen Parametersatz verfügen, einige enthalten mehrere Sätze. Wenn ein Cmdlet mehrere Parametersätze aufweist, können Sie Parameter erst konfigurieren, nachdem Sie den zu verwendenden Parametersatz ausgewählt haben. Sie ändern den durch eine Aktivität verwendeten Parametersatz, indem Sie **Parametersatz** auswählen und einen anderen Satz angeben. In diesem Fall gehen alle bereits konfigurierten Parameterwerte verloren.

Im folgenden Beispiel besitzt das Cmdlet [Get-AzVM](/powershell/module/az.compute/get-azvm) drei Parametersätze. Im Beispiel wird der Parametersatz **ListVirtualMachineInResourceGroupParamSet** mit einem einzelnen optionalen Parameter zum Zurückgeben aller virtuellen Computer in einer Ressourcengruppe verwendet. Im Beispiel wird auch der Parametersatz **GetVirtualMachineInResourceGroupParamSet** verwendet, um den zurückzugebenden virtuellen Computer anzugeben. Dieser Satz verfügt über zwei obligatorische Parameter und einen optionalen Parameter.

![Parametersatz](media/automation-graphical-authoring-intro/get-azvm-parameter-sets.png)

#### <a name="parameter-values"></a>Parameterwerte

Wenn Sie einen Wert für einen Parameter angeben, wählen Sie eine Datenquelle aus, um festzulegen, wie der Wert angegeben wird. Die für einen bestimmten Parameter verfügbaren Datenquellen richten sich nach den gültigen Werten für diesen Parameter. Beispielsweise ist NULL keine verfügbare Option für einen Parameter, der keine NULL-Werte zulässt.

| Data source | BESCHREIBUNG |
|:--- |:--- |
| Konstanter Wert |Geben Sie einen Wert für den Parameter ein. Diese Datenquelle ist nur für die folgenden Datentypen verfügbar: Int32, Int64, String, Boolean, DateTime, Switch. |
| Aktivitätsausgabe |Verwenden Sie die Ausgabe einer Aktivität, die der aktuellen Aktivität im Workflow vorausgeht. Alle gültigen Aktivitäten werden aufgelistet. Verwenden Sie für den Parameterwert nur die Aktivität, die die Ausgabe erzeugt. Wenn es sich bei der Aktivitätsausgabe um ein Objekt mit mehreren Eigenschaften handelt, können Sie nach Auswahl der Aktivität den Namen einer bestimmten Eigenschaft eingeben. |
| Runbookeingabe |Wählen Sie eine Runbookeingabe als Eingabe für den Aktivitätsparameter aus. |
| Variablenobjekt |Wählen Sie eine Automation-Variable als Eingabe aus. |
| Anmeldeinformationsobjekt |Wählen Sie ein Automation-Anmeldeinformationsobjekt als Eingabe aus. |
| Zertifikatobjekt |Wählen Sie ein Automation-Zertifikatobjekt als Eingabe aus. |
| Verbindungsobjekt |Wählen Sie ein Automation-Verbindungsobjekt als Eingabe aus. |
| PowerShell-Ausdruck |Geben Sie einen einfachen [PowerShell-Ausdruck](#work-with-powershell-expressions) an. Der Ausdruck wird vor der Aktivität ausgewertet, und das Ergebnis wird für den Parameterwert verwendet. Sie können Variablen verwenden, um auf die Ausgabe einer Aktivität oder einen Eingabeparameter für ein Runbook zu verweisen. |
| Nicht konfiguriert |Löscht einen Wert, der zuvor konfiguriert wurde. |

#### <a name="optional-additional-parameters"></a>Optionale zusätzliche Parameter

Alle Cmdlets bieten die Möglichkeit, zusätzliche Parameter festzulegen. Hierbei handelt es sich um allgemeine PowerShell-Parameter oder andere benutzerdefinierte Parameter. Der grafische Editor zeigt ein Textfeld an, in dem Sie unter Verwendung der PowerShell-Syntax Parameter bereitstellen können. Um beispielsweise den allgemeinen Parameter `Verbose` zu verwenden, geben Sie `-Verbose:$True` an.

### <a name="retry-activity"></a>Wiederholen der Aktivität

Die Wiederholungsfunktion für eine Aktivität ermöglicht deren mehrmalige Ausführung, bis eine bestimmte Bedingung erfüllt ist (ähnlich wie bei einer Schleife). Sie können dieses Feature für Aktivitäten nutzen, die mehrmals ausgeführt werden müssen, fehleranfällig sind oder möglicherweise mehrere Versuche für eine erfolgreiche Ausführung benötigen. Sie können mit diesem Feature auch die Gültigkeit der ausgegebenen Informationen überprüfen.

Wenn Sie eine Wiederholung für eine Aktivität aktivieren, können Sie eine Verzögerung und eine Bedingung festlegen. Die Verzögerung ist die Zeit (gemessen in Sekunden oder Minuten), die das Runbook wartet, bevor die Aktivität erneut ausgeführt wird. Wenn Sie keine Verzögerung angeben, wird die Aktivität sofort nach Abschluss des Vorgangs erneut ausgeführt.

:::image type="content" source="media/automation-graphical-authoring-intro/retry-delay.png" alt-text="Screenshot der Einstellungen für das Aktivieren der Wiederholungsfunktion.":::

Die Wiederholungsbedingung ist ein PowerShell-Ausdruck, der nach jeder Ausführung der Aktivität ausgewertet wird. Wenn der Ausdruck „True“ ergibt, wird die Aktivität erneut ausgeführt. Wenn der Ausdruck „False“ ergibt, wird die Aktivität nicht erneut ausgeführt, und das Runbook fährt mit der nächsten Aktivität fort.

:::image type="content" source="media/automation-graphical-authoring-intro/retry-condition.png" alt-text="Der Screenshot zeigt das Feld „Wiederholen, bis diese Bedingung wahr ist“ und Beispiele von PowerShell-Ausdrücken, die in der Wiederholungsbedingung verwendet werden können.":::

Die Wiederholungsbedingung kann eine Variable namens `RetryData` enthalten, die Zugriff auf Informationen zu den Aktivitätswiederholungen bereitstellt. Diese Variable weist die in der folgenden Tabelle aufgeführten Eigenschaften auf:

| Eigenschaft | BESCHREIBUNG |
|:--- |:--- |
| `NumberOfAttempts` |Häufigkeit, mit der die Aktivität ausgeführt wurde. |
| `Output` |Die Ausgabe nach der letzten Ausführung der Aktivität. |
| `TotalDuration` |Vergangene Zeit seit dem ersten Start der Aktivität. |
| `StartedAt` |Uhrzeit (im UTC-Format), zu der die Aktivität zuerst gestartet wurde. |

Es folgen Beispiele von Bedingungen für die Wiederholung von Aktivitäten.

```powershell-interactive
# Run the activity exactly 10 times.
$RetryData.NumberOfAttempts -ge 10
```

```powershell-interactive
# Run the activity repeatedly until it produces any output.
$RetryData.Output.Count -ge 1
```

```powershell-interactive
# Run the activity repeatedly until 2 minutes has elapsed.
$RetryData.TotalDuration.TotalMinutes -ge 2
```

Wenn Sie eine Wiederholungsbedingung für eine Aktivität konfiguriert haben, verfügt die Aktivität über zwei optische Hinweise, um Sie daran zu erinnern. Einer wird in der Aktivität angezeigt, der andere beim Überprüfen der Aktivitätskonfiguration.

![Visuelle Indikatoren für Aktivitätswiederholung](media/automation-graphical-authoring-intro/runbook-activity-retry-visual-cue.png)

### <a name="workflow-script-control"></a>Steuerelement "Workflowskript"

Bei einem Workflowskript-Steuerelement handelt es sich um eine spezielle Aktivität, die je nach Art des zu erstellenden grafischen Runbooks ein PowerShell- oder PowerShell-Workflow-Skript akzeptiert. Dieses Steuerelement bietet Funktionen, die andernfalls möglicherweise nicht verfügbar wären. Es kann keine Parameter akzeptieren, kann jedoch Variablen für die Aktivitätsausgabe und Runbookeingabeparameter verwenden. Alle Ausgaben der Aktivität werden dem Datenbus hinzugefügt. Eine Ausnahme ist die Ausgabe ohne ausgehende Verknüpfung. In diesem Fall wird die Ausgabe der Ausgabe des Runbooks hinzugefügt.

Der folgende Code beispielsweise führt Datumsberechnungen unter Verwendung einer Runbookeingabevariable namens `NumberOfDays` durch. Anschließend wird ein berechneter DateTime-Wert als Ausgabe an nachfolgende Aktivitäten im Runbook gesendet.

```powershell-interactive
$DateTimeNow = (Get-Date).ToUniversalTime()
$DateTimeStart = ($DateTimeNow).AddDays(-$NumberOfDays)}
$DateTimeStart
```

## <a name="use-links-for-workflow"></a>Verwenden von Links für Workflows

Eine Verknüpfung in einem grafischen Runbook verbindet zwei Aktivitäten miteinander. Sie wird auf der Canvas als Pfeil dargestellt, der von der Quellaktivität zur Zielaktivität zeigt. Die Aktivitäten werden in Richtung des Pfeils ausgeführt, wobei die Zielaktivität gestartet wird, sobald die Quellaktivität abgeschlossen wurde.

### <a name="create-a-link"></a>Erstellen einer Verknüpfung

Sie können eine Verknüpfung zwischen zwei Aktivitäten erstellen, indem Sie die Quellaktivität auswählen und auf den Kreis am unteren Ende der Form klicken. Ziehen Sie den Pfeil bis zur Zielaktivität, und lassen Sie die Maustaste los.

![Erstellen einer Verknüpfung](media/automation-graphical-authoring-intro/create-link-options.png)

Wählen Sie die Verknüpfung aus, um ihre Eigenschaften im Blatt "Konfiguration" zu konfigurieren. Zu den Eigenschaften zählt beispielsweise der Verknüpfungstyp, der in der folgenden Tabelle beschrieben wird.

| Verknüpfungstyp | BESCHREIBUNG |
|:--- |:--- |
| Pipeline |Die Zielaktivität wird einmal für jede Objektausgabe der Quellaktivität ausgeführt. Die Zielaktivität wird nicht ausgeführt, wenn die Quellaktivität zu keiner Ausgabe führt. Die Ausgabe der Quellaktivität steht als Objekt zur Verfügung. |
| Sequenz |Die Zielaktivität wird nur einmal ausgeführt, wenn sie die Ausgabe der Quellaktivität empfängt. Die Ausgabe der Quellaktivität steht als Array aus Objekten zur Verfügung. |

### <a name="start-runbook-activity"></a>Starten einer Runbookaktivität

Ein grafisches Runbook wird mit allen Aktivitäten gestartet, die keine eingehende Verknüpfung besitzen. Es gibt oft nur eine Aktivität, die als Startaktivität für das Runbook dient. Wenn mehrere Aktivitäten keine eingehende Verknüpfung aufweisen, wird das Runbook durch eine parallele Ausführung dieser Aktivitäten gestartet. Die Verknüpfungen werden durchlaufen, um nach jeder Beendigung einer Aktivität weitere Aktivitäten auszuführen.

### <a name="specify-link-conditions"></a>Angeben von Verknüpfungsbedingungen

Wenn Sie für eine Verknüpfung eine Bedingung angeben, wird die Zielaktivität nur ausgeführt, wenn die Bedingung als „True“ ausgewertet wird. Sie verwenden üblicherweise eine `ActivityOutput`-Variable in einer Bedingung, um die Ausgabe der Quellaktivität abzurufen.

Für eine Pipelineverknüpfung müssen Sie eine Bedingung für ein einzelnes Objekt angeben. Das Runbook wertet die Bedingung für jedes Objekt aus, das von der Quellaktivität ausgegeben wird. Dann führt es die Zielaktivität für jedes Objekt aus, das die Bedingung erfüllt. Wenn Sie beispielsweise `Get-AzVM` als Quellaktivität verwenden, kann die folgende Syntax für eine bedingte Pipelineverknüpfung verwendet werden, um nur virtuelle Computer aus der Ressourcengruppe Group1 abzurufen.

```powershell-interactive
$ActivityOutput['Get Azure VMs'].Name -match "Group1"
```

Bei einer Sequenzverknüpfung wertet das Runbook die Bedingung nur einmal aus, da ein einzelnes Array mit sämtlichen Objekten zurückgegeben wird, die von der Quellaktivität ausgegeben werden. Aus diesem Grund kann das Runbook zum Filtern keine Sequenzverknüpfung verwenden, wie dies bei einer Pipelineverknüpfung der Fall ist. Die Sequenzverknüpfung kann einfach bestimmen, ob die nächste Aktivität ausgeführt wird.

Betrachten Sie beispielsweise den folgende Reihe von Aktivitäten aus unserem Runbook **Start VM**:

![Bedingte Verknüpfung mit Sequenzen](media/automation-graphical-authoring-intro/runbook-conditional-links-sequence.png)

Das Runbook verwendet drei verschiedene Sequenzverknüpfungen, die Werte der Eingabeparameter `VMName` und `ResourceGroupName` überprüfen, um die entsprechende Aktion zu bestimmen. Mögliche Aktionen sind Starten eines einzelnen virtuellen Computers, Starten aller virtuellen Computer in der Ressourcengruppe oder Starten aller VMs in einem Abonnement. Für die Sequenzverknüpfung zwischen `Connect to Azure` und `Get single VM`gilt die folgende Bedingungslogik:

```powershell-interactive
<#
Both VMName and ResourceGroupName runbook input parameters have values
#>
(
(($VMName -ne $null) -and ($VMName.Length -gt 0))
) -and (
(($ResourceGroupName -ne $null) -and ($ResourceGroupName.Length -gt 0))
)
```

Wenn Sie eine bedingte Verknüpfung verwenden, werden die verfügbaren Daten aus der Quellaktivität für andere Aktivitäten in dieser Verzweigung durch die Bedingung gefiltert. Wenn eine Aktivität die Quelle für mehrere Verknüpfungen ist, richten sich die für Aktivitäten verfügbaren Daten in jeder Verzweigung nach der Bedingung in der Verknüpfung, die mit dieser Verzweigung verbunden ist.

So startet beispielsweise die Aktivität `Start-AzVM` im nachstehenden Runbook alle virtuellen Computer. Sie verfügt über zwei bedingte Verknüpfungen: Die erste bedingte Verknüpfung verwendet den Ausdruck `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -eq $true`, um zu filtern, ob die `Start-AzVM`-Aktivität erfolgreich abgeschlossen wurde. Die zweite bedingte Verknüpfung verwendet den Ausdruck `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -ne $true`, um zu filtern, ob die `Start-AzVm`-Aktivität den virtuellen Computer nicht starten kann.

![Beispiel einer bedingten Verknüpfung](media/automation-graphical-authoring-intro/runbook-conditional-links.png)

Alle Aktivitäten, die auf die erste Verknüpfung folgen und die Aktivitätsausgabe von `Get-AzureVM` verwenden, empfangen nur die virtuellen Computer, die zum Zeitpunkt der Ausführung von `Get-AzureVM` gestartet wurden. Alle Aktivitäten, die der zweiten Verknüpfung folgen, rufen nur die virtuellen Computer ab, die zum Zeitpunkt der Ausführung von `Get-AzureVM` beendet waren. Alle Aktivitäten, die der dritten Verknüpfung folgen, rufen alle virtuellen Computer ab, unabhängig von deren Ausführungsstatus.

### <a name="use-junctions"></a>Verwenden von Verbindungen

Eine Verbindung ist eine spezielle Aktivität, die wartet, bis alle eingehenden Verzweigungen abgeschlossen wurden. Auf diese Weise kann das Runbook mehrere Aktivitäten parallel ausführen, und vor dem Fortfahren sicherstellen, dass alle Aktivitäten abgeschlossen wurden.

Wenngleich eine Verbindung eine unbegrenzte Anzahl von eingehenden Verknüpfungen aufweisen kann, darf nur eine dieser Verknüpfungen eine Pipeline sein. Für die Anzahl der eingehenden Sequenzverknüpfungen gilt keine Beschränkung. Sie können die Verbindung mit mehreren eingehenden Pipelineverknüpfungen erstellen und das Runbook speichern, bei der Ausführung tritt jedoch ein Fehler auf.

Das nachstehende Beispiel ist ein Teil eines Runbooks, das einen Satz virtueller Computer startet, während gleichzeitig Patches heruntergeladen werden, die auf diese Computer angewendet werden sollen. Es nutzt eine Verbindung, um sicherzustellen, dass beide Prozesse abgeschlossen sind, bevor das Runbook fortgesetzt wird.

![Verbindung](media/automation-graphical-authoring-intro/runbook-junction.png)

### <a name="work-with-cycles"></a>Arbeit mit Zyklen

Wenn eine Zielaktivität eine Verknüpfung zurück auf die Quellaktivität oder eine andere Aktivität aufweist, die letztlich eine Verknüpfung mit der zugehörigen Quellaktivität aufweist, wird ein Zyklus gebildet. Die grafische Erstellung unterstützt derzeit keine Zyklen. Wenn Ihr Runbook einen Zyklus aufweist, wird dieser ordnungsgemäß gespeichert, empfängt bei der Ausführung jedoch einen Fehler.

![Zyklus](media/automation-graphical-authoring-intro/runbook-cycle.png)

### <a name="share-data-between-activities"></a>Freigeben von Daten zwischen Aktivitäten

Sämtliche Daten, die von einer Aktivität mit ausgehender Verknüpfung ausgegeben werden, werden in den Datenbus für das Runbook geschrieben. Alle Aktivitäten im Runbook können die Daten im Datenbus zum Auffüllen von Parameterwerten oder zur Nutzung in Skriptcode verwenden. Eine Aktivität kann auf die Ausgabe einer beliebigen vorherigen Aktivität im Workflow zugreifen.

Wie die Daten in den Datenbus geschrieben werden, richtet sich nach dem Typ der Verknüpfung der Aktivität. Bei einer Pipelineverknüpfung werden die Daten als mehrere Objekte ausgegeben. Bei einer Sequenzverknüpfung werden die Daten als Array ausgegeben. Wenn nur ein Wert vorliegt, wird dieser als ein einzelnes Elementarray ausgegeben.

Ihr Runbook bietet zwei Möglichkeiten für den Zugriff auf Daten im Datenbus: 
* Verwenden Sie eine Aktivitätsausgabe-Datenquelle.
* Verwenden Sie einen PowerShell-Ausdruck als Datenquelle.

Der erste Mechanismus verwendet eine Aktivitätsausgabe-Datenquelle, um einen Parameter einer anderen Aktivität aufzufüllen. Wenn es sich bei der Ausgabe um ein Objekt handelt, kann das Runbook eine einzelne Eigenschaft festlegen.

![Aktivitätsausgabe](media/automation-graphical-authoring-intro/activity-output-datasource-revised20165.png)

Der zweite Datenzugriffsmechanismus ruft die Ausgabe einer Aktivität in einer PowerShell-Ausdrucksdatenquelle oder einer Workflowskriptaktivität mit einer `ActivityOutput`-Variablen mithilfe der unten gezeigten Syntax ab. Wenn es sich bei der Ausgabe um ein Objekt handelt, kann das Runbook eine einzelne Eigenschaft festlegen.

```powershell-interactive
$ActivityOutput['Activity Label']
$ActivityOutput['Activity Label'].PropertyName
```

### <a name="use-checkpoints"></a>Verwenden von Prüfpunkten

Sie können [Prüfpunkte](automation-powershell-workflow.md#use-checkpoints-in-a-workflow) in einem grafischen PowerShell-Workflow-Runbook festlegen, indem Sie für eine beliebige Aktivität die Option **Prüfpunkt für Runbook** auswählen. Dadurch wird ein Prüfpunkt festgelegt, nachdem die Aktivität ausgeführt wurde.

![Prüfpunkt](media/automation-graphical-authoring-intro/set-checkpoint.png)

Prüfpunkte stehen nur in grafischen PowerShell-Workflow-Runbooks zur Verfügung, nicht in grafischen Runbooks. Wenn das Runbook Azure-Cmdlets verwendet, sollte auf jede Aktivität, nach der ein Prüfpunkt festgelegt wird, eine `Connect-AzAccount`-Aktivität folgen. Der Verbindungsvorgang wird verwendet, wenn das Runbook angehalten wird und von diesem Prüfpunkt auf einem anderen Worker neu gestartet werden muss.

## <a name="handle-runbook-input"></a>Verarbeiten von Runbookeingaben

Ein Runbook erfordert Eingaben durch den Benutzer, der es über das Azure-Portal oder (wenn das aktuelle Runbook als untergeordnetes Runbook verwendet wird) aus einem anderen Runbook startet. Beispielsweise muss der Benutzer für ein Runbook, das einen virtuellen Computer erstellt, bei jedem Start des Runbooks möglicherweise Informationen wie den Namen des virtuellen Computers und andere Eigenschaften bereitstellen.

Das Runbook akzeptiert die Eingabe bei Definition eines oder mehrerer Eingabeparameter. Der Benutzer stellt bei jedem Start des Runbooks Werte für diese Parameter bereit. Wenn der Benutzer das Runbook mithilfe des Azure-Portals startet, wird der Benutzer aufgefordert, Werte für jeden Eingabeparameter anzugeben, der vom Runbook unterstützt wird.

Beim Erstellen Ihres Runbooks können Sie auf dessen Eingabeparameter zugreifen, indem Sie auf **Eingabe und Ausgabe** auf der Symbolleiste des Runbooks klicken. Daraufhin wird das Steuerelement „Eingabe und Ausgabe“ geöffnet, in dem Sie einen vorhandenen Eingabeparameter bearbeiten oder durch Klicken auf **Eingabe hinzufügen** einen neuen Eingabeparameter erstellen können.

![Eingabe hinzufügen](media/automation-graphical-authoring-intro/runbook-edit-add-input.png)

Jeder Eingabeparameter wird durch die Eigenschaften in der folgenden Tabelle definiert:

| Eigenschaft | Beschreibung |
|:--- |:--- |
| Name | Erforderlich. Der Name des Parameters. Der Name muss innerhalb des Runbooks eindeutig sein. Er muss mit einem Buchstaben beginnen und darf nur Buchstaben, Zahlen und Unterstriche enthalten. Der Name darf kein Leerzeichen enthalten. |
| BESCHREIBUNG |Optional. Beschreibung zum Zweck des Eingabeparameters. |
| type | Optional. Der für den Parameterwert erwartete Datentyp. Das Azure-Portal bietet ein geeignetes Steuerelement zum Eingeben des jeweiligen Datentyps für jeden Parameter. Folgende Parametertypen werden unterstützt: String, Int32, Int64, Decimal, Boolean, DateTime und Object. Wenn kein Datentyp ausgewählt wird, wird standardmäßig „String“ verwendet.|
| Obligatorisch. | Optional. Die Einstellung gibt an, ob ein Wert für den Parameter angegeben werden muss. Wenn Sie `yes` auswählen, muss beim Starten des Runbooks ein Wert angegeben werden. Wenn Sie `no` auswählen, ist beim Starten des Runbooks kein Wert erforderlich, und es kann ein Standardwert verwendet werden. Das Runbook kann nicht gestartet werden, wenn der Wert für einen erforderlichen Parameter fehlt, für den kein Standardwert definiert wurde. |
| Standardwert | Optional. Der Wert, der für einen Parameter verwendet wird, wenn dieser beim Start des Runbooks nicht übergeben wird. Wählen Sie `Custom` aus, um einen Standardwert festzulegen. Wählen Sie `None` aus, wenn Sie keinen Standardwert angeben möchten. |

## <a name="handle-runbook-output"></a>Verarbeiten von Runbookausgaben

Die grafische Erstellung speichert Daten, die von einer Aktivität ohne ausgehende Verknüpfung erstellt werden, in der [Ausgabe des Runbooks](./automation-runbook-output-and-messages.md). Die Ausgabe wird mit dem Runbookauftrag gespeichert und steht einem übergeordneten Runbook zur Verfügung, wenn das Runbook als untergeordnetes Runbook verwendet wird.

## <a name="work-with-powershell-expressions"></a>Arbeiten mit PowerShell-Ausdrücken

Einer der Vorteile der grafischen Erstellung ist, dass Sie ein Runbook erstellen können, wenn Sie nur über wenig PowerShell-Kenntnisse verfügen. Derzeit benötigen Sie aber einige PowerShell-Kenntnisse, um bestimmte [Parameterwerte](#use-activities) einzufügen und [Verknüpfungsbedingungen](#use-links-for-workflow) festzulegen. Dieser Abschnitt enthält eine kurze Einführung in PowerShell-Ausdrücke. Alle Details von PowerShell sind unter [Skripterstellung mit Windows PowerShell](/powershell/scripting/overview)verfügbar.

### <a name="use-a-powershell-expression-as-a-data-source"></a>Verwenden eines PowerShell-Ausdrucks als Datenquelle

Sie können einen PowerShell-Ausdruck als Datenquelle verwenden, um den Wert eines [Aktivitätsparameters](#use-activities) mit den Ergebnissen von PowerShell-Code zu füllen. Bei dem Ausdruck kann es sich um eine einzelne Codezeile handeln, mit der eine einfache Funktion durchgeführt wird, oder um mehrere Zeilen, mit denen die Schritte einer komplexen Logik ausgeführt werden. Alle Ausgaben eines Befehls, der keiner Variablen zugewiesen ist, werden an den Parameterwert ausgegeben.

Mit dem folgenden Befehl wird beispielsweise das aktuelle Datum ausgegeben.

```powershell-interactive
Get-Date
```

Mit den nächsten Codeausschnitten wird aus dem aktuellen Datum eine Zeichenfolge erstellt und einer Variablen zugewiesen. Der Code sendet die Inhalte der Variablen an die Ausgabe.

```powershell-interactive
$string = "The current date is " + (Get-Date)
$string
```

Mit den folgenden Befehlen wird das aktuelle Datum ausgewertet, und es wird eine Zeichenfolge zurückgegeben, die angibt, ob der aktuelle Tag ein Tag am Wochenende oder ein Wochentag ist.

```powershell-interactive
$date = Get-Date
if (($date.DayOfWeek = "Saturday") -or ($date.DayOfWeek = "Sunday")) { "Weekend" }
else { "Weekday" }
```

### <a name="use-activity-output"></a>Verwenden von Aktivitätsausgaben

Zum Verwenden der Ausgabe einer vorherigen Aktivität in Ihrem Runbook verwenden Sie die `ActivityOutput`-Variable mit der unten angegebenen Syntax.

```powershell-interactive
$ActivityOutput['Activity Label'].PropertyName
```

Beispielsweise können Sie über eine Aktivität mit einer Eigenschaft verfügen, die den Namen eines virtuellen Computers erfordert. In diesem Fall kann das Runbook den folgenden Ausdruck verwenden.

```powershell-interactive
$ActivityOutput['Get-AzureVM'].Name
```

Falls für die Eigenschaft anstelle eines einfachen Namens das Objekt des virtuellen Computers erforderlich ist, gibt das Runbook das gesamte Objekt mit der folgenden Syntax zurückgeben.

```powershell-interactive
$ActivityOutput['Get-AzureVM']
```

Das Runbook kann die Ausgabe einer Aktivität in einen komplexeren Ausdruck wie den folgenden verwenden. Dieser Ausdruck verkettet Text zum Namen der VM.

```powershell-interactive
"The computer name is " + $ActivityOutput['Get-AzureVM'].Name
```

### <a name="compare-values"></a>Vergleichen von Werten

Verwenden Sie [Vergleichsoperatoren](/powershell/module/microsoft.powershell.core/about/about_comparison_operators) , um Werte zu vergleichen, oder bestimmen Sie, ob ein Wert mit einem bestimmten Muster übereinstimmt. Bei einem Vergleich wird entweder der Wert „True“ oder „False“ zurückgegeben.

Mit der folgenden Bedingung wird beispielsweise bestimmt, ob der virtuelle Computer einer Aktivität mit dem Namen `Get-AzureVM` derzeit beendet ist.

```powershell-interactive
$ActivityOutput["Get-AzureVM"].PowerState -eq "Stopped"
```

Die folgende Bedingung bestimmt, ob sich derselbe virtuelle Computer in einem anderen Zustand als „Beendet“ befindet.

```powershell-interactive
$ActivityOutput["Get-AzureVM"].PowerState -ne "Stopped"
```

Sie können in Ihrem Runbook mehrere Bedingungen mit einem [logischen Operator](/powershell/module/microsoft.powershell.core/about/about_logical_operators) wie `-and` oder `-or` verknüpfen. Mit der folgenden Bedingung wird beispielsweise überprüft, ob sich der virtuelle Computer aus dem vorherigen Beispiel im Zustand „Beendet“ oder „Wird beendet“ befindet.

```powershell-interactive
($ActivityOutput["Get-AzureVM"].PowerState -eq "Stopped") -or ($ActivityOutput["Get-AzureVM"].PowerState -eq "Stopping")
```

### <a name="use-hashtables"></a>Verwenden von Hashtabellen

[Hashtabellen](/powershell/module/microsoft.powershell.core/about/about_hash_tables) sind Name-Wert-Paare, die zum Zurückgeben einer Gruppe von Werten hilfreich sind. Es kann auch vorkommen, dass eine Hashtabelle als Wörterbuch bezeichnet wird. Eigenschaften für bestimmte Aktivitäten erwarten anstelle eines einfachen Werts eine Hashtabelle.

Erstellen Sie eine Hashtabelle mit der folgenden Syntax. Sie kann eine beliebige Anzahl von Einträgen enthalten, die aber jeweils durch einen Namen und einen Wert definiert sind.

```powershell-interactive
@{ <name> = <value>; [<name> = <value> ] ...}
```

Mit dem folgenden Ausdruck wird beispielsweise eine Hashtabelle erstellt, die in der Datenquelle für einen Aktivitätsparameter verwendet wird, der eine Hashtabelle mit Werten für eine Internetsuche erwartet.

```powershell-interactive
$query = "Azure Automation"
$count = 10
$h = @{'q'=$query; 'lr'='lang_ja';  'count'=$Count}
$h
```

Im folgenden Beispiel wird die Ausgabe einer Aktivität mit dem Namen `Get Twitter Connection` zum Auffüllen einer Hashtabelle mit Werten verwendet.

```powershell-interactive
@{'ApiKey'=$ActivityOutput['Get Twitter Connection'].ConsumerAPIKey;
    'ApiSecret'=$ActivityOutput['Get Twitter Connection'].ConsumerAPISecret;
    'AccessToken'=$ActivityOutput['Get Twitter Connection'].AccessToken;
    'AccessTokenSecret'=$ActivityOutput['Get Twitter Connection'].AccessTokenSecret}
```

## <a name="authenticate-to-azure-resources"></a>Authentifizieren bei Azure-Ressourcen

Zur Verwaltung von Azure-Ressourcen verwendete Runbooks in Azure Automation erfordern eine Authentifizierung bei Azure. Das [ausführende Konto](./automation-security-overview.md) (auch als Dienstprinzipal bezeichnet) ist der Standardmechanismus, mit dem ein Automation-Runbook in Ihrem Abonnement auf Azure Resource Manager-Ressourcen zugreift. Diese Funktion können Sie einem grafischen Runbook hinzufügen, indem Sie der Canvas das Verbindungsobjekt `AzureRunAsConnection` hinzufügen, das das PowerShell-Cmdlet [Get-AutomationConnection](/system-center/sma/manage-global-assets) verwendet. Sie können auch das Cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) hinzufügen. Dieses Szenario ist im folgenden Beispiel dargestellt.

![Authentifizierungsaktivitäten für „Ausführen als“](media/automation-graphical-authoring-intro/authenticate-run-as-account.png)

Die `Get Run As Connection`-Aktivität oder `Get-AutomationConnection` wird mit einer konstanten Wertdatenquelle mit dem Namen `AzureRunAsConnection` konfiguriert.

![Verbindungskonfiguration für „Ausführen als“](media/automation-graphical-authoring-intro/authenticate-runas-parameterset.png)

Die nächste Aktivität (`Connect-AzAccount`) fügt das authentifizierte ausführende Konto hinzu, damit es im Runbook verwendet werden kann.

![Parametersatz Connect-AzAccount](media/automation-graphical-authoring-intro/authenticate-conn-to-azure-parameter-set.png)

>[!NOTE]
>Für PowerShell-Runbooks sind `Add-AzAccount` und `Add-AzureRMAccount` Aliase für `Connect-AzAccount`. Beachten Sie, dass diese Aliase für Ihre grafischen Runbooks nicht verfügbar sind. Ein grafisches Runbook kann nur `Connect-AzAccount` selbst verwenden.

Für die Parameterfelder **APPLICATIONID**, **CERTIFICATETHUMBPRINT** und **TENANTID** muss der Name der Eigenschaft für den Feldpfad angegeben werden, da die Aktivität ein Objekt mit mehreren Eigenschaften ausgibt. Andernfalls kann beim Ausführen des Runbooks keine Authentifizierung erfolgen. Dies sind die Mindestanforderungen, die für die Authentifizierung Ihres Runbooks mit dem ausführenden Konto erfüllt sein müssen.

Einige Abonnenten erstellen ein Automation-Konto mithilfe eines [Azure AD-Benutzerkontos](./shared-resources/credentials.md) zum Verwalten der klassischen Azure-Bereitstellung oder für Azure Resource Manager-Ressourcen. Um die Abwärtskompatibilität für diese Abonnenten aufrechtzuerhalten, ist der in Ihrem Runbook zu verwendende Authentifizierungsmechanismus das Cmdlet `Add-AzureAccount` mit einem [Anmeldeinformationenobjekt](./shared-resources/credentials.md). Das Objekt stellt einen Active Directory-Benutzer mit Zugriff auf das Azure-Konto dar.

Sie können diese Funktionalität für Ihr grafisches Runbook aktivieren, indem Sie der Canvas ein Anmeldeinformationenobjekt, gefolgt von einer `Add-AzureAccount`-Aktivität hinzufügen, die das Anmeldeinformationenobjekt als Eingabe verwendet. Siehe folgendes Beispiel.

![Authentifizierungsaktivitäten](media/automation-graphical-authoring-intro/authentication-activities.png)

Das Runbook muss sich am Anfang und nach jedem Prüfpunkt authentifizieren. Daher müssen Sie nach jeder `Checkpoint-Workflow`-Aktivität eine `Add-AzureAccount`-Aktivität verwenden. Es ist keine zusätzliche Anmeldeinformationenaktivität erforderlich.

![Aktivitätsausgabe](media/automation-graphical-authoring-intro/authentication-activity-output.png)

## <a name="export-a-graphical-runbook"></a>Exportieren eines grafischen Runbooks

Sie können nur die veröffentlichte Version eines grafisch Runbooks exportieren. Wenn das Runbook noch nicht veröffentlicht wurde, ist die Schaltfläche **Exportieren** deaktiviert. Beim Klicken auf die Schaltfläche **Exportieren** wird das Runbook auf Ihren lokalen Computer heruntergeladen. Der Name der Datei entspricht dem Namen des Runbooks mit der Erweiterung **.graphrunbook**.

## <a name="import-a-graphical-runbook"></a>Importieren eines grafischen Runbooks

Sie können eine Datei für ein grafisches Runbook oder für ein grafisches PowerShell-Workflow-Runbook importieren, indem Sie beim Hinzufügen eines Runbooks die Option **Importieren** auswählen. Wenn Sie die zu importierende Datei auswählen, können Sie den Namen beibehalten oder einen neuen vergeben. Im Feld **Runbooktyp** wird der Typ des Runbooks angezeigt, nachdem die ausgewählte Datei bewertet wurde. Wenn Sie versuchen, einen anderen Typ auszuwählen, der nicht korrekt ist, zeigt der grafische Editor eine Meldung an, die auf potenzielle Konflikte hinweist und darauf, dass während der Konvertierung Syntaxfehler auftreten können.

![Runbook importieren](media/automation-graphical-authoring-intro/runbook-import.png)

## <a name="test-a-graphical-runbook"></a>Testen eines grafischen Runbooks

Jedes grafische Runbook in Azure Automation umfasst eine Entwurfsversion und eine veröffentlichte Version. Sie können nur die veröffentlichte Version ausführen, während Sie nur die Entwurfsversion bearbeiten können. Die veröffentlichte Version bleibt von Änderungen an der Entwurfsversion unberührt. Wenn die Entwurfsversion zur Nutzung bereit ist, können Sie sie veröffentlichen und auf diese Weise die derzeit veröffentlichte Version mit Ihrer Entwurfsversion überschreiben.

Sie können die Entwurfsversion eines Runbooks im Azure-Portal testen, während die veröffentlichte Version unverändert bleibt. Alternativ können Sie ein neues Runbook testen, bevor es veröffentlicht wird, damit Sie überprüfen können, ob das Runbook vor den Versionsersetzungen ordnungsgemäß funktionierte. Beim Testen eines Runbooks wird die Entwurfsversion ausgeführt und sichergestellt, dass alle ausgeführten Aktionen abgeschlossen werden. Es wird kein Auftragsverlauf erstellt, aber im Fensterbereich „Testausgabe“ wird die Ausgabe angezeigt.

Sie öffnen das Steuerelement „Test“ für Ihr grafisches Runbook, indem Sie das Runbook zur Bearbeitung öffnen und dann auf **Testbereich** klicken. Das Steuerelement „Test“ fordert alle benötigten Eingabeparameter an, und Sie können das Runbook starten, indem Sie auf **Start** klicken.

## <a name="publish-a-graphical-runbook"></a>Veröffentlichen eines grafischen Runbooks

Veröffentlichen Sie ein grafisches Runbook, indem Sie das Runbook zur Bearbeitung öffnen und dann auf **Veröffentlichen** klicken. Mögliche Statuswerte für das Runbook sind:

* Neu: Das Runbook wurde noch nicht veröffentlicht. 
* Veröffentlicht: Das Runbook wurde veröffentlicht.
* In Bearbeitung: Das Runbook wurde nach der Veröffentlichung bearbeitet und die Entwurfsversion und die veröffentlichte Version unterscheiden sich voneinander.

![Statuswerte für ein Runbook](media/automation-graphical-authoring-intro/runbook-statuses-revised20165.png)

Sie haben die Möglichkeit, ein Runbook auf die veröffentlichte Version zurückzusetzen. Damit werden alle Änderungen, die seit der letzten Veröffentlichung des Runbooks vorgenommen wurden, verworfen. Die Entwurfsversion des Runbooks wird durch die veröffentlichte Version ersetzt.

## <a name="next-steps"></a>Nächste Schritte

* Informationen zu den ersten Schritten mit grafischen Runbooks finden Sie unter [Tutorial: Erstellen eines grafischen Runbooks](./learn/powershell-runbook-managed-identity.md).
* Weitere Informationen zu den verschiedenen Runbooktypen sowie zu ihren Vorteilen und Einschränkungen finden Sie unter [Azure Automation-Runbooktypen](automation-runbook-types.md).
* Informationen, wie Sie sich mithilfe des ausführenden Automation-Kontos authentifizieren, finden Sie unter [Ausführendes Konto](automation-security-overview.md#run-as-account).
* Eine Referenz zu den PowerShell-Cmdlets finden Sie unter [Az.Automation](/powershell/module/az.automation/#automation).