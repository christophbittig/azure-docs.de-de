---
title: Azure Automation-Runbooktypen
description: In diesem Artikel werden die Runbooktypen beschrieben, die Sie in Azure Automation verwenden können, sowie Aspekte, die Sie bei der Auswahl des geeigneten Typs berücksichtigen sollten.
services: automation
ms.subservice: process-automation
ms.date: 10/28/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: f0fe647d586887bcb2d0a897f57c75a5f0b141b5
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131423931"
---
# <a name="azure-automation-runbook-types"></a>Azure Automation-Runbooktypen

Das Prozessautomatisierungsfeature von Azure Automation unterstützt verschiedene Runbooktypen, die in der folgenden Tabelle definiert sind. Weitere Informationen zur Prozessautomatisierungsumgebung finden Sie unter [Ausführen von Runbooks in Azure Automation](automation-runbook-execution.md).

| type | BESCHREIBUNG |
|:--- |:--- |
| [Grafisch](#graphical-runbooks)|Grafisches Runbook, das auf Windows PowerShell basiert und vollständig im grafischen Editor im Azure-Portal erstellt und bearbeitet wird. |
| [PowerShell-Workflow, grafisch](#graphical-runbooks)|Grafisches Runbook, das auf dem Windows PowerShell-Workflow basiert und vollständig im grafischen Editor im Azure-Portal erstellt und bearbeitet wird. |
| [PowerShell](#powershell-runbooks) |Textrunbook, das auf einem Windows PowerShell-Skript basiert. |
| [PowerShell-Workflow](#powershell-workflow-runbooks)|Textrunbook, das auf einem Windows PowerShell-Workflowskript basiert. |
| [Python](#python-runbooks) |Textrunbook, das auf einem Python-Skript basiert. |

Wenn Sie festlegen, welchen Typ Sie für ein bestimmtes Runbook verwenden möchten, müssen Sie Folgendes berücksichtigen.

* Runbooks können nicht aus einem grafischen in einen textbasierten Typ oder umgekehrt konvertiert werden.
* Es gibt Einschränkungen bei der Verwendung von Runbooks verschiedener Typen als untergeordnete Runbooks. Weitere Informationen finden Sie unter [Untergeordnete Runbooks in Azure Automation](automation-child-runbooks.md).

## <a name="graphical-runbooks"></a>Grafische Runbooks

Sie können grafische Runbooks und grafische PowerShell-Workflow-Runbooks im grafischen Editor im Azure-Portal erstellen und bearbeiten. Es ist jedoch nicht möglich, diesen Runbooktyp mit einem anderen Tool zu erstellen oder zu bearbeiten. Hauptfunktionen von grafischen Runbooks:

* Werden in Dateien in Ihrem Automation-Konto exportiert und dann in ein anderes Automation-Konto importiert
* Generieren PowerShell-Code
* Werden während des Imports in grafische PowerShell-Workflow-Runbooks konvertiert und umgekehrt

### <a name="advantages"></a>Vorteile

* Verwenden ein visuelles Erstellungsmodell zum Einfügen/Verknüpfen/Konfigurieren
* Schwerpunkt auf Datenfluss im Prozess
* Visuelle Darstellung von Verwaltungsprozessen.
* Schließen weitere Runbooks als untergeordnete Runbooks ein, um übergeordnete Workflows zu erstellen
* Fördern modulare Programmierung

### <a name="limitations"></a>Einschränkungen

* Können nicht außerhalb des Azure-Portals erstellt oder bearbeitet werden
* Erfordern möglicherweise Aktivität mit PowerShell-Code, um komplexe Logik auszuführen
* Können nicht in eines der [Textformate](automation-runbook-types.md) konvertiert werden (Textrunbooks können darüber hinaus nicht in das grafische Format überführt werden) 
* Bieten keine Möglichkeit zum Anzeigen oder direkten Bearbeiten des vom grafischen Workflow erstellten PowerShell-Codes. Sie können den erstellten Code in allen Codeaktivitäten anzeigen.
* Keine Möglichkeit zum Ausführen von Runbooks auf einem Hybrid Runbook Worker unter Linux. Weitere Informationen finden Sie unter [Automatisieren von Ressourcen im Rechenzentrum oder in der Cloud mit Hybrid Runbook Worker](automation-hybrid-runbook-worker.md).
* Grafische Runbooks können nicht digital signiert werden.

## <a name="powershell-runbooks"></a>PowerShell-Runbooks

PowerShell-Runbooks basieren auf Windows PowerShell. Sie bearbeiten den Code des Runbooks direkt mit dem Text-Editor im Azure-Portal. Sie können auch einen beliebigen Offline-Texteditor verwenden und das [Runbook in Azure Automation importieren](manage-runbooks.md) .


Die PowerShell-Version wird durch die angegebene **Runtimeversion** bestimmt (d. h. Version 7.1 (Vorschauversion) oder Version 5.1). Der Azure Automation-Dienst unterstützt die neueste PowerShell-Runtime.
 
Dieselben Azure-Sandbox- und Hybrid Runbook Worker-Instanzen können **PowerShell 5.1**- und **PowerShell 7.1**-Runbooks parallel ausführen.   

> [!NOTE]
>  Wenn Sie bei der Ausführung des Runbooks die **Runtimeversion** **7.1 (Vorschauversion)** auswählen, werden PowerShell-Module verwendet, die auf die Runtimeversion 7.1 ausgerichtet sind, und wenn Sie die **Runtimeversion** **5.1** auswählen, werden PowerShell-Module verwendet, die auf die Runtimeversion 5.1 ausgerichtet sind.
 
Stellen Sie sicher, dass Sie die richtige Runtimeversion für Module auswählen.

Beispiel: Wenn Sie ein Runbook für ein SharePoint-Automatisierungsszenario in **Runtimeversion** *7.1 (Vorschauversion)* ausführen, dann importieren Sie das Modul in **Runtimeversion** **7.1 (Vorschauversion)** . Wenn Sie ein Runbook für ein SharePoint-Automatisierungsszenario in **Runtimeversion** **5.1** ausführen, dann importieren Sie das Modul in **Runtimeversion** *5.1*. In diesem Fall sehen Sie zwei Einträge für das Modul, einen für **Runtimeversion** **7.1 (Vorschauversion)** und einen für **5.1**.

:::image type="content" source="./media/automation-runbook-types/runbook-types.png" alt-text="Runbooktypen":::


Derzeit werden PowerShell 5.1 und 7.1 (Vorschauversion) unterstützt.


### <a name="advantages"></a>Vorteile

* Implementierung der gesamten komplexen Logik mit PowerShell-Code ohne die zusätzliche Komplexität des PowerShell-Workflows.
* Schnellerer Start als PowerShell-Workflow-Runbooks, da vor der Ausführung keine Kompilierung erforderlich ist
* Werden in Azure und in Hybrid Runbook Workern sowohl für Windows als auch für Linux ausgeführt

### <a name="limitations---version-51"></a>Einschränkungen: Version 5.1

* Erfordern Kenntnisse zu PowerShell-Skripts
* Können keine [parallele Verarbeitung](automation-powershell-workflow.md#use-parallel-processing) zum gleichzeitigen Ausführen mehrerer Aktionen nutzen
* Können keine [Prüfpunkte](automation-powershell-workflow.md#use-checkpoints-in-a-workflow) zum Fortsetzen des Runbooks bei einem Fehler nutzen
* Sie können nur PowerShell-, PowerShell-Workflow- und grafische Runbooks mithilfe des Cmdlets [Start-AzAutomationRunbook](/powershell/module/az.automation/start-azautomationrunbook) als untergeordnete Runbooks einfügen, wobei ein neuer Auftrag erstellt wird.
* Für Runbooks kann die PowerShell-Anweisung [#Requires](/powershell/module/microsoft.powershell.core/about/about_requires) nicht verwendet werden. Sie wird in der Azure-Sandbox und für Hybrid Runbook Workers nicht unterstützt und führt möglicherweise dazu, dass für den Auftrag ein Fehler auftritt.

### <a name="known-issues---version-51"></a>Bekannte Probleme: Version 5.1

Im Folgenden sind aktuell bekannte Probleme mit PowerShell-Runbooks aufgeführt:

* PowerShell-Runbooks können keine unverschlüsselten [Variablenobjekte](./shared-resources/variables.md) mit einem NULL-Wert abrufen.
* PowerShell-Runbooks können kein Variablenobjekt abrufen, dessen Name `*~*` enthält.
* Der Vorgang [Get-Process](/powershell/module/microsoft.powershell.management/get-process) in einer Schleife in einem PowerShell-Runbook kann nach etwa 80 Iterationen zum Absturz führen.
* Ein PowerShell-Runbook kann einen Fehler verursachen, wenn es versucht, eine große Datenmenge auf einmal in den Ausgabestream zu schreiben. Sie können dieses Problem in der Regel vermeiden, indem Sie nur die für die Arbeit mit großen Objekten benötigten Informationen in die Runbookausgabe einfügen. Anstatt `Get-Process` ohne Einschränkungen zu verwenden, können Sie z. B. nur die erforderlichen Parameter in die Cmdlet-Ausgabe einfügen wie bei `Get-Process | Select ProcessName, CPU`.

### <a name="limitations---71-preview"></a>Einschränkungen: 7.1 (Vorschauversion)

-  Die Azure Automation-internen PowerShell-Cmdlets werden auf einem Linux Hybrid Runbook Worker nicht unterstützt. Sie müssen das `automationassets`-Modul zu Beginn Ihres Python-Runbooks importieren, um auf die Funktionen für freigegebene Ressourcen (Objekte) des Automation-Kontos zugreifen zu können. 
-  Für die PowerShell 7-Runtimeversion werden die Modulaktivitäten nicht für die importierten Module extrahiert.
-  Der *PSCredential*-Runbookparametertyp wird in der PowerShell 7-Runtimeversion nicht unterstützt.
-  PowerShell 7.x unterstützt keine Workflows. Weitere Informationen finden Sie [hier](/powershell/scripting/whats-new/differences-from-windows-powershell?view=powershell-7.1#powershell-workflow&preserve-view=true).
-  PowerShell 7.x unterstützt derzeit keine signierten Runbooks.

### <a name="known-issues---71-preview"></a>Bekannte Probleme: 7.1 (Vorschauversion)

-  Das Ausführen von untergeordneten Skripts mithilfe von `.\child-runbook.ps1` wird in dieser Vorschauversion nicht unterstützt. 
  **Problemumgehung**: Verwenden Sie `Start-AutomationRunbook` (internes Cmdlet) oder `Start-AzAutomationRunbook` (aus dem *Az.Automation-Modul*), um ein anderes Runbook aus dem übergeordneten Runbook zu starten.
-  Runbookeigenschaften, die die Protokollierungsvoreinstellung definieren, werden in der PowerShell 7-Runtime nicht unterstützt.  
  **Problemumgehung**: Legen Sie die Voreinstellung am Anfang des Runbooks explizit wie folgt fest:

      `$VerbosePreference = "Continue"`

      `$ProgressPreference = "Continue"`

-   Vermeiden Sie den Import des Moduls `Az.Accounts` in Version 2.4.0 für die PowerShell 7-Runtime, da es zu unerwartetem Verhalten bei Verwendung dieser Version in Azure Automation führen kann. 
-    Möglicherweise treten Formatierungsprobleme mit Fehlerausgabestreams für den Auftrag auf, der in der PowerShell 7-Runtime ausgeführt wird.

## <a name="powershell-workflow-runbooks"></a>PowerShell-Workflow-Runbooks

PowerShell-Workflow-Runbooks sind Textrunbooks, die auf einem [Windows PowerShell-Workflow](automation-powershell-workflow.md)basieren. Sie bearbeiten den Code des Runbooks direkt mit dem Text-Editor im Azure-Portal. Sie können auch einen beliebigen Offline-Texteditor verwenden und das [Runbook in Azure Automation importieren](manage-runbooks.md) . 

>[!NOTE]
> PowerShell 7.1 unterstützt keine Workflow-Runbooks.

### <a name="advantages"></a>Vorteile

* Implementierung der gesamten komplexen Logik mit PowerShell-Workflowcode.
* Verwendung von [Prüfpunkten](automation-powershell-workflow.md#use-checkpoints-in-a-workflow) zum Fortsetzen des Vorgangs bei einem Fehler
* Verwendung der [Parallelverarbeitung](automation-powershell-workflow.md#use-parallel-processing), um mehrere Aktionen gleichzeitig auszuführen
* Können andere grafische und PowerShell-Workflow-Runbooks als untergeordnete Runbooks enthalten, um übergeordnete Workflows zu erstellen

### <a name="limitations"></a>Einschränkungen

* Erfordern Kenntnisse mit dem PowerShell-Workflow
* Müssen die zusätzliche Komplexität des PowerShell-Workflows (z. B. [deserialisierte Objekte](automation-powershell-workflow.md#deserialized-objects)) verarbeiten
* Benötigen beim Starten länger als PowerShell-Runbooks, da sie vor der Ausführung kompiliert werden müssen
* Ausschließlich PowerShell-Runbooks können mit dem Cmdlet `Start-AzAutomationRunbook` als untergeordnete Runbooks eingeschlossen werden.
* Keine Möglichkeit zum Ausführen von Runbooks auf einem Hybrid Runbook Worker und Linux

## <a name="python-runbooks"></a>Python-Runbooks

Python-Runbooks werden unter Python 2 und 3 kompiliert. Python 3-Runbooks sind derzeit als Vorschau verfügbar. Sie können den Code des Runbooks direkt mit dem Text-Editor im Azure-Portal bearbeiten. Sie können auch einen beliebigen Offline-Text-Editor verwenden und das [Runbook in Azure Automation importieren](manage-runbooks.md).

Python 3-Runbooks werden in den folgenden globalen Azure-Infrastrukturen unterstützt:

* Azure Global
* Azure Government

### <a name="advantages"></a>Vorteile

* Verwendung der stabilen Python-Bibliotheken
* Können in Azure oder auf Hybrid Runbook Workern ausgeführt werden.
* Bei Python 2 werden Windows Hybrid Runbook Workers unterstützt, wenn [Python 2.7](https://www.python.org/downloads/release/latest/python2) installiert ist.
* Für Python 3-Cloudaufträge wird die Python-Version 3.8 unterstützt. Skripts und Pakete aus einer beliebigen 3.x-Version funktionieren möglicherweise, wenn der Code mit mehreren Versionen kompatibel ist.  
* Für Python 3-Hybridaufträge auf Windows-Computern können Sie jede beliebige 3.x-Version installieren, die Sie eventuell verwenden möchten.  
* Für Python 3-Hybridaufträge auf Linux-Computern ist die auf dem Computer installierte Version von Python 3 zum Ausführen von DSC OMSConfig und dem Linux Hybrid Worker erforderlich. Es wird empfohlen, auf Linux-Computern Version 3.6 zu installieren. Andere Versionen sollten jedoch ebenfalls funktionieren, wenn zwischen den Versionen von Python 3 keine Breaking Changes bei Methodensignaturen oder Verträgen aufgetreten sind.

### <a name="limitations"></a>Einschränkungen

* Erfordern Kenntnisse mit Python-Skripts
* Erfordern für die Verwendung von Bibliotheken von Drittanbietern das [Importieren der Pakete](python-packages.md) in das Automation-Konto
* Das Verwenden des Cmdlets **Start-AutomationRunbook** in PowerShell oder einem PowerShell-Workflow zum Starten eines Python 3-Runbooks (Vorschau) funktioniert nicht. Sie können das Cmdlet  **Start-AzAutomationRunbook** aus dem Modul Az.Automation oder das Cmdlet  **Start-AzureRmAutomationRunbook** aus dem Modul AzureRm.Automation verwenden, um diese Einschränkung zu umgehen.  
* Azure Automation unterstützt  **sys.stderr** nicht.

### <a name="multiple-python-versions"></a>Mehrere Python-Versionen

Ein Windows Runbook Worker sucht beim Ausführen eines Python 2-Runbooks zuerst nach der Umgebungsvariable `PYTHON_2_PATH` und überprüft, ob sie auf eine gültige ausführbare Datei verweist. Wenn der Installationsordner beispielsweise `C:\Python2` lautet, wird überprüft, ob `C:\Python2\python.exe` ein gültiger Pfad ist. Wenn er nicht gefunden wird, sucht der Worker nach der Umgebungsvariable `PATH`, um eine ähnliche Überprüfung durchzuführen.

Für Python 3 sucht er zuerst nach der Umgebungsvariable `PYTHON_3_PATH` und greift dann auf die Umgebungsvariable `PATH` zurück.

Wenn Sie nur eine Version von Python verwenden, können Sie der Variable den Installationspfad `PATH` hinzufügen. Wenn Sie beide Versionen auf dem Runbook Worker verwenden möchten, legen Sie für `PYTHON_2_PATH` und `PYTHON_3_PATH` den Speicherort des Moduls für diese Versionen fest.

### <a name="known-issues"></a>Bekannte Probleme

Bei Cloudaufträgen schlagen Python 3-Aufträge manchmal mit einer Ausnahmemeldung `invalid interpreter executable path` fehl. Diese Ausnahme wird möglicherweise angezeigt, wenn ein Auftrag verzögert wird, wenn der Start länger als 10 Minuten dauert oder wenn **Start-AutomationRunbook** verwendet wurde, um Python 3-Runbooks zu starten. Wenn der Auftrag verzögert wurde, sollte ein Neustart des Runbooks ausreichen. Hybridaufträge sollten ohne Probleme funktionieren, wenn Sie die folgenden Schritte ausführen:

1. Erstellen Sie eine neue Umgebungsvariable mit dem Namen `PYTHON_3_PATH`, und geben Sie dafür den Installationsordner an. Wenn der Installationsordner beispielsweise `C:\Python3` ist, muss dieser Pfad der Variablen hinzugefügt werden.
1. Starten Sie den Computer neu, nachdem Sie die Umgebungsvariable festgelegt haben.

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zu PowerShell-Runbooks finden Sie unter [Tutorial: Erstellen eines PowerShell-Runbooks](./learn/powershell-runbook-managed-identity.md).
* Weitere Informationen zu PowerShell-Workflow-Runbooks finden Sie unter [Tutorial: Erstellen eines PowerShell-Workflow-Runbooks](learn/automation-tutorial-runbook-textual.md).
* Weitere Informationen zu grafischen Runbooks finden Sie unter [Tutorial: Erstellen eines grafischen Runbooks](./learn/powershell-runbook-managed-identity.md).
* Weitere Informationen zu Python-Runbooks finden Sie unter [Tutorial: Erstellen eines Python-Runbooks](./learn/automation-tutorial-runbook-textual-python-3.md).