---
title: Nachverfolgen aktualisierter Dateien mit einem Azure Automation-Watchertask
description: In diesem Artikel erfahren Sie, wie Sie einen Watchertask im Azure Automation-Konto erstellen, um die Erstellung neuer Dateien in einem Ordner zu überwachen.
services: automation
ms.subservice: process-automation
ms.topic: how-to
ms.date: 12/17/2020
ms.openlocfilehash: 0a4b62d60c255d79eaf0042aa075b216d362dbce
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124758501"
---
# <a name="track-updated-files-with-a-watcher-task"></a>Nachverfolgen von aktualisierten Dateien mit einem Watchertask

Azure Automation verwendet einen Watchertask für eine Überwachung auf Ereignisse und Triggeraktionen in PowerShell-Runbooks. Der Watchertask besteht aus zwei Teilen: Watcher und Aktion. Ein Watcherrunbook wird in einem Intervall ausgeführt, das im Watchertask definiert ist, und gibt Daten in ein Aktionsrunbook aus.

> [!NOTE]
> Watchertasks werden in Azure China 21Vianet nicht unterstützt.

> [!IMPORTANT]
> Seit Mai 2020 ist die Verwendung von Azure Logic Apps die empfohlene und unterstützte Methode zum Überwachen auf Ereignisse, zum Planen von wiederkehrenden Aufgaben und zum Auslösen von Aktionen. Informationen finden Sie unter [Planen und Ausführen von wiederkehrenden automatisierten Aufgaben, Prozessen und Workflows mit Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

Dieser Artikel führt Sie durch die Schritte zum Erstellen eines Watchertasks, mit dem Sie überwachen können, ob einem Verzeichnis eine neue Datei hinzugefügt wird. Folgendes wird vermittelt:

* Importieren eines Watcherrunbooks
* Erstellen einer Automation-Variable
* Erstellen eines Aktionsrunbooks
* Erstellen eines Watchertasks
* Auslösen eines Watchers
* Untersuchen der Ausgabe

## <a name="prerequisites"></a>Voraussetzungen

Für diesen Artikel müssen folgende Voraussetzungen erfüllt sein:

* Azure-Abonnement. Wenn Sie noch kein Abonnement haben, können Sie Ihre [MSDN-Abonnentenvorteile aktivieren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oder sich für ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) registrieren.
* [Automation-Konto](./index.yml), um die Watcher- und Aktionsrunbooks und den Watchertask aufzunehmen.
* [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md), in dem der Watchertask ausgeführt wird.
* PowerShell-Runbooks. PowerShell-Workflow-Runbooks werden von Watchertasks nicht unterstützt.

## <a name="import-a-watcher-runbook"></a>Importieren eines Watcherrunbooks

In diesem Artikel wird ein Watcherrunbook namens **Watcherrunbook, das nach neuen Dateien in einem Verzeichnis sucht** verwendet, um in einem Verzeichnis nach neuen Dateien zu suchen. Das Watcherrunbook ruft die Uhrzeit des letzten bekannten Schreibzugriffs auf die Dateien in einem Ordner ab und sucht nach Dateien, die jünger sind als dieser Zeitpunkt.

Sie können dieses Runbook mithilfe der folgenden Schritte über das Portal in Ihr Automation-Konto importieren:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Suchen Sie nach **Automation-Konten**, und wählen Sie diese Option aus. 
1. Wählen Sie auf der Seite **Automation-Konten** den Namen Ihres Automation-Kontos in der Liste aus.
1. Wählen Sie im linken Bereich unter **Prozessautomatisierung** die Option **Runbookkatalog** aus.
1. Stellen Sie sicher, dass in der Dropdownliste **Quelle** die Option **GitHub** ausgewählt ist.
1. Suchen Sie nach **Watcherrunbook**.
1. Wählen Sie **Watcherrunbook, das nach neuen Dateien in einem Verzeichnis sucht** und dann auf der Detailseite die Option **Importieren** aus.
1. Geben Sie einen Namen und optional eine Beschreibung für das Runbook ein, und klicken Sie auf **OK**, um das Runbook in Ihr Automation-Konto zu importieren. In einem Bereich oben rechts im Fenster sollte die Meldung **Import erfolgreich** angezeigt werden.
1. Das importierte Runbook wird in der Liste unter dem von Ihnen festgelegten Namen angezeigt, wenn Sie im linken Bereich „Runbooks“ auswählen.
1. Klicken Sie auf das Runbook, wählen Sie auf der Detailseite des Runbooks **Bearbeiten** aus, und klicken Sie dann auf **Veröffentlichen**. Wählen Sie bei der Aufforderung **Ja** aus, um das Runbook zu veröffentlichen.

Sie können die Runbooks auch von der [Azure Automation-GitHub-Organisation](https://github.com/azureautomation) herunterladen.

1. Navigieren Sie zur Seite der Azure Automation-GitHub-Organisation für [Watch-NewFile.ps1](https://github.com/azureautomation/watcher-runbook-that-looks-for-new-files-in-a-directory#watcher-runbook-that-looks-for-new-files-in-a-directory).
1. Um das Runbook von GitHub herunterzuladen, wählen Sie **Code** auf der rechten Seite aus, und wählen Sie dann **ZIP herunterladen** aus, um den gesamten Code in einer ZIP-Datei herunterzuladen.
1. Extrahieren Sie den Inhalt, und [importieren Sie das Runbook](manage-runbooks.md#import-a-runbook-from-the-azure-portal).

## <a name="create-an-automation-variable"></a>Erstellen einer Automation-Variable

Eine [Automation-Variable](./shared-resources/variables.md) wird verwendet, um die Zeitstempel zu speichern, die das vorhergehende Runbook aus jeder Datei liest und speichert.

1. Wählen Sie unter **Freigegebene Ressourcen** die Option **Variablen** aus, und klicken Sie dann auf **+Variable hinzufügen**.
1. Geben Sie als Namen **Watch-NewFileTimestamp** ein.
1. Wählen Sie den Typ **DateTime** aus. Standardmäßig werden das aktuelle Datum und die aktuelle Uhrzeit verwendet.

   :::image type="content" source="./media/automation-watchers-tutorial/create-new-variable.png" alt-text="Screenshot: Blatt zum Erstellen einer neuen Variablen":::

1. Klicken Sie auf **Erstellen**, um die Automation-Variable zu erstellen.

## <a name="create-an-action-runbook"></a>Erstellen eines Aktionsrunbooks

Ein Aktionsrunbook wird in einem Watchertask verwendet, um Aktionen für die von einem Watcherrunbook übergebenen Daten auszuführen. Sie müssen ein vordefiniertes Aktionsrunbook aus dem Azure-Portal oder aus der [Azure Automation-GitHub-Organisation](https://github.com/azureautomation) importieren.

Sie können dieses Runbook im Azure-Portal in Ihr Automation-Konto importieren:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Suchen Sie nach **Automation-Konten**, und wählen Sie diese Option aus. 
1. Wählen Sie auf der Seite **Automation-Konten** den Namen Ihres Automation-Kontos in der Liste aus.
1. Wählen Sie im linken Bereich unter **Prozessautomatisierung** die Option **Runbookkatalog** aus.
1. Stellen Sie sicher, dass in der Dropdownliste **Quelle** die Option **GitHub** ausgewählt ist.
1. Suchen Sie nach **Watcheraktion**. Wählen Sie **Watcheraktion, die von einem Watcherrunbook ausgelöste Ereignisse verarbeitet** aus, und klicken Sie auf **Importieren**.
1. Ändern Sie optional den Namen des Runbooks auf der Seite „Importieren“, und klicken Sie zum Importieren des Runbooks auf **OK**. Oben rechts im Browser sollte im Benachrichtigungsbereich die Meldung **Import erfolgreich** angezeigt werden.
1. Wechseln Sie zur Seite Ihres Automation-Kontos, und klicken Sie auf der linken Seite auf **Runbooks**. Ihr neues Runbook sollte unter dem Namen aufgeführt werden, den Sie ihm im vorherigen Schritt gegeben haben. Klicken Sie auf das Runbook, wählen Sie auf der Detailseite des Runbooks **Bearbeiten** aus, und klicken Sie dann auf **Veröffentlichen**. Wählen Sie bei der Aufforderung **Ja** aus, um das Runbook zu veröffentlichen.

So erstellen Sie ein Aktionsrunbook, indem Sie es von der [Azure Automation-GitHub-Organisation](https://github.com/azureautomation) herunterladen:

1. Navigieren Sie zur Seite der Azure Automation-GitHub-Organisation für [Process-NewFile.ps1](https://github.com/azureautomation/watcher-action-that-processes-events-triggerd-by-a-watcher-runbook).
1. Um das Runbook von GitHub herunterzuladen, wählen Sie **Code** auf der rechten Seite aus, und wählen Sie dann **ZIP herunterladen** aus, um den gesamten Code in einer ZIP-Datei herunterzuladen.
1. Extrahieren Sie den Inhalt, und [importieren Sie das Runbook](manage-runbooks.md#import-a-runbook-from-the-azure-portal).

## <a name="create-a-watcher-task"></a>Erstellen eines Watchertasks

In diesem Schritt konfigurieren Sie den Watchertask und verweisen dabei auf die Watcher- und Aktionsrunbooks, die in den vorherigen Abschnitten definiert wurden.

1. Navigieren Sie zu Ihrem Automation-Konto, und wählen Sie unter **Prozessautomatisierung** die Option **Watchertasks** aus.
1. Wählen Sie die Seite „Watchertasks“ aus, und klicken Sie auf **+Watchertask hinzufügen**.
1. Geben Sie **WatchMyFolder** als Namen ein.

1. Wählen Sie **Watcher konfigurieren** und dann das Runbook **Watch-NewFile** aus.

1. Geben Sie die folgende Werte für die Parameter ein:

   * **FOLDERPATH**: Ein Ordner in dem Hybrid Runbook Worker, in dem neue Dateien erstellt werden. Beispiel: **d:\examplefiles**.
   * **EXTENSION**: Erweiterung für die Konfiguration. Lassen Sie dieses Feld leer, um alle Dateierweiterungen zu verarbeiten.
   * **RECURSE**: Rekursiver Vorgang. Behalten Sie hier den Standardwert bei.
   * **RUN SETTINGS**: Einstellung für die Ausführung des Runbooks. Wählen Sie den Hybrid Worker aus.

1. Klicken Sie auf **OK** und dann auf **Auswählen**, um zur Seite „Watcher“ zurückzukehren.
1. Wählen Sie **Aktion konfigurieren** und dann das Runbook **Process-NewFile** aus.
1. Geben Sie die folgende Werte für die Parameter ein:

   * **EVENTDATA**: Ereignisdaten. Lassen Sie dieses Feld leer. Die Daten werden vom Watcherrunbook übergeben.
   * **Laufzeiteinstellungen** Einstellung für die Ausführung des Runbooks. Übernehmen Sie hier „Azure“, da dieses Runbook in Azure Automation ausgeführt wird.

1. Klicken Sie auf **OK** und dann auf **Auswählen**, um zur Seite „Watcher“ zurückzukehren.
1. Klicken Sie auf **OK**, um den Watchertask zu erstellen.

   :::image type="content" source="./media/automation-watchers-tutorial/watchertaskcreation.png" alt-text="Screenshot: Konfigurieren der Watcheraktion im Azure-Portal":::


## <a name="trigger-a-watcher"></a>Auslösen eines Watchers

Sie müssen einen Test wie unten beschrieben ausführen, um sicherzustellen, dass der Watchertask erwartungsgemäß funktioniert. 

1. Stellen Sie eine Remoteverbindung mit dem Hybrid Runbook Worker her.
1. Öffnen Sie **PowerShell**, und erstellen Sie im Ordner eine Testdatei.

```azurepowerShell-interactive
New-Item -Name ExampleFile1.txt
```

Das folgende Beispiel zeigt die erwartete Ausgabe.

```output
    Directory: D:\examplefiles


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----       12/11/2017   9:05 PM              0 ExampleFile1.txt
```

## <a name="inspect-the-output"></a>Untersuchen der Ausgabe

1. Navigieren Sie zu Ihrem Automation-Konto, und wählen Sie unter **Prozessautomatisierung** die Option **Watchertasks** aus.
1. Wählen Sie den Watchertask **WatchMyFolder** aus.
1. Klicken Sie unter **Datenströme** auf **Watcherdatenströme anzeigen**, um zu überprüfen, ob der Watcher die neue Datei gefunden und das Aktionsrunbook gestartet hat.
1. Klicken Sie auf **Watcheraktionsaufträge anzeigen**, um den Aktionsrunbookauftrag anzuzeigen. Sie können jeden einzelnen Auftrag auswählen, um die Details anzuzeigen.

   :::image type="content" source="./media/automation-watchers-tutorial/WatcherActionJobs.png" alt-text="Screenshot: Watcheraktionsaufträge im Azure-Portal":::


Im folgenden Beispiel sehen Sie die erwartete Ausgabe, wenn die neue Datei gefunden wurde:

```output
Message is Process new file...

Passed in data is @{FileName=D:\examplefiles\ExampleFile1.txt; Length=0}
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Erstellen Ihres eigenen Runbooks finden Sie unter [Erstellen eines PowerShell-Runbooks](./learn/powershell-runbook-managed-identity.md).