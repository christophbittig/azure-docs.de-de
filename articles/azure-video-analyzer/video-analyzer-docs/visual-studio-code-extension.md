---
title: Verwenden der Visual Studio Code-Erweiterung für Azure Video Analyzer
description: In diesem Referenzartikel wird erläutert, wie Sie die verschiedenen Funktionen der Visual Studio Code-Erweiterung für Azure Video Analyzer verwenden.
ms.service: azure-video-analyzer
ms.topic: reference
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: b0faa61b724420ad04cc489d51539c134569dd05
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132287113"
---
# <a name="visual-studio-code-extension-for-azure-video-analyzer"></a>Verwenden der Visual Studio Code-Erweiterung für Azure Video Analyzer

Azure Video Analyzer ist eine Plattform, welche das Erstellen von Videoanalyseprogrammen vereinfacht, und die zugehörige Visual Studio Code-Erweiterung ist ein Tool, um das Lernen dieser Plattform zu vereinfachen.  Dieser Artikel ist ein Verweis auf die verschiedenen Funktionen, welche von der Erweiterung angeboten werden.  Es werden die Grundlagen folgender Themen behandelt:

* Pipelinetopologien: Erstellen, Bearbeiten, Löschen, Anzeigen des JSON-Code
* Livepipelines: Erstellung, Aktivierung, Deaktivierung, Löschung, Anzeigen des JSON-Code
* Bearbeiten einer Pipelinetopologie: Module, Parameter, Systemvariablen, Verbindungen, Validierung

Wenn Sie die Erweiterung nicht für die Verbindung mit Ihrem Edgegerät eingerichtet haben, befolgen Sie die Schnellstartanleitung für die [Verwendung der Visual Studio Code-Erweiterung für Azure Video Analyzer](./edge/use-visual-studio-code-extension.md).

## <a name="managing-pipelines-topology"></a>Verwalten der Pipelinetopologie

Klicken Sie zum Erstellen einer Topologie links unter Ihrem Modul mit der rechten Maustaste auf `Pipelines topologies` und wählen Sie `Create pipeline topology` aus.  Dadurch wird eine neue leere Topologie geöffnet.  Sie können dann entweder eine der vorgefertigten Topologien laden, indem Sie oben in der Dropdownliste auswählen `Try sample topologies` oder selbst eine erstellen.  

Nachdem alle erforderlichen Bereiche abgeschlossen sind, müssen Sie die Topologie mit dem `Save` oben rechts speichern.  Für Beispieltopologien sollte das erforderliche Feld vorab ausgefüllt werden.  Dadurch wird es für die Verwendung beim Erstellen von Livepipelines verfügbar.

Um eine vorhandene Topologie zu bearbeiten, klicken Sie links unter Pipelinetopologien mit der rechten Maustaste auf den Namen der Topologie und wählen Sie `Edit pipeline topology` aus.

Um eine vorhandene Topologie zu löschen, klicken Sie links unter Pipelinetopologien mit der rechten Maustaste auf den Namen der Topologie und wählen Sie `Delete pipeline topology` aus.  Livepipelines müssen zuerst entfernt werden.

Wenn Sie den zugrunde liegenden JSON-Code hinter einer vorhandenen Topologie anzeigen möchten, klicken Sie links unter Pipelinetopologien mit der rechten Maustaste auf den Namen der Topologie und wählen Sie `Show pipeline topology JSON` aus.

## <a name="live-pipelines"></a>Livepipelines

Um eine Livepipeline zu erstellen, klicken Sie links unter Pipelinetopologien mit der rechten Maustaste auf den Namen der Topologie und wählen Sie `Create live pipeline` aus.  Sie müssen dann einen Namen für die Live-Pipeline und alle erforderlichen Parameter eingeben, bevor Sie fortfahren.  Rechts oben können Sie entweder auf `Save` klicken, um es in einem inaktiven Zustand zu speichern, oder auf `Save and activate`, um die Livepipeline sofort zu starten. 

Klicken Sie zum Aktivieren einer vorhandenen Livepipeline links unter Pipelinetopologien mit der rechten Maustaste auf den Namen der Livepipeline und wählen Sie `Activate live pipeline` aus.

Um eine ausgeführte Instanz zu deaktivieren, klicken Sie links unter Pipelinetopologien mit der rechten Maustaste auf die Livepipeline und wählen Sie `Deactivate live pipeline` aus.  Dadurch wird die Livepipeline nicht gelöscht.

Klicken Sie zum Löschen einer vorhandenen Livepipeline links unter Pipelinetopologien mit der rechten Maustaste auf die Livepipeline und wählen Sie `Delete live pipeline` aus.  Sie können eine aktive Livepipeline nicht löschen.

Wenn Sie den zugrunde liegenden JSON-Code hinter einer vorhandenen Topologie anzeigen möchten, klicken Sie links unter Pipelinetopologien mit der rechten Maustaste auf den Namen der Topologie und wählen Sie `Show live pipeline JSON` aus.

## <a name="editing-a-topology"></a>Bearbeiten einer Topologie 

Pipelinetopologien werden aus einer Vielzahl von Teilen erstellt. Informationen zu diesen Teilen finden Sie in der Dokumentation zum [Pipeline](pipeline.md)-Konzept. In diesem Abschnitt geht es um die Teile der Schnittstelle, mit denen Sie eine Topologie erstellen oder bearbeiten können.

### <a name="modules"></a>Module

Module sind auf der linken Seite verfügbar und in Quellen, Prozessoren und Senken gruppiert.  Um ihrer Topologie eines hinzuzufügen, ziehen Sie es in den Bearbeitungsbereich.

Wenn Sie ein anderes Modul desselben Typs mit den gleichen Startparametern wünschen, können Sie es duplizieren, indem Sie mit der rechten Maustaste auf das Modul klicken und `Copy` auswählen.

Wenn Sie ein Modul aus der Topologie entfernen möchten, klicken Sie mit der rechten Maustaste auf das Modul, und wählen Sie `Delete` aus.

### <a name="parameters"></a>Parameter

Sie können die Attribute in einem Modul bearbeiten, indem Sie im Bearbeitungsbereich mit der linken Maustaste darauf klicken.  Dadurch wird ein Bereich auf der rechten Seite mit einer Liste aller Elemente angezeigt.  Erforderliche Elemente sind mit einem roten Sternchen gekennzeichnet.  Der Informationstext enthält eine kurze Beschreibung nach dem Namen der einzelnen Elemente.

Da Topologien effektiv Vorlagen sind, aus denen Sie mehrere Livepipelines erstellen können, sollten Sie einige Elemente zum Zeitpunkt der Erstellung ausfüllen.  Dies können Sie mit Parametern erreichen.  Um dies zu aktivieren, klicken Sie mit der rechten Maustaste auf die drei Punkte rechts neben dem Attribut, und wählen Sie `Parameterize` aus.  Hierdurch wird ein Fenster angezeigt.

Im Parametrisierungsfenster können Sie einen neuen Parameter erstellen, der als Wert funktioniert, den Sie bei der Erstellung der Livepipeline ausfüllen, oder einen vorhandenen auswählen.  Zum Erstellen eines neuen müssen Sie den Namen und den Typ eingeben.  Optional können Sie einen Standardwert eingeben, wenn dieser nur manchmal geändert werden muss.  Wenn eine Livepipeline erstellt wird, sind nur Parameter ohne Standardwert erforderlich.  Abschließend klicken Sie auf `Set`.

Wenn Sie Ihre vorhandenen Parameter verwalten möchten, können Sie dazu oben die `Manage parameters` Option verwenden.  Im angezeigten Bereich können Sie neue Parameter hinzufügen und vorhandene Parameter bearbeiten oder löschen.

### <a name="system-variable"></a>Systemvariable

Beim Erstellen einer Reihe von Livepipelines gibt es wahrscheinlich Fälle, in denen Sie Variablen verwenden möchten, um Dateien oder Ausgaben zu benennen.  Beispielsweise können Sie einen Videoclip mit dem Namen der Livepipeline und Datum/Uhrzeit benennen, damit Sie wissen, woher er zu welchem Zeitpunkt stammt.  Video Analyzer stellt drei Systemvariablen zur Verfügung, die Sie in Ihren Modulen als Hilfe verwenden können.

| Systemvariable        | Beschreibung                                                  | Beispiel              |
| :--------------------- | :----------------------------------------------------------- | :------------------- |
| System.Runtime.DateTime        | UTC-Datum/-Uhrzeit im dateikonformen ISO8601-Format (Basisdarstellung YYYYMMDDThhmmss). | 20200222T173200Z     |
| System.TopologyName    | Vom Benutzer angegebener Name der ausgeführten Pipeline-Topologie.          | IngestAndRecord      |
| System.PipelineName    | Vom Benutzer angegebener Name der ausgeführten Live-Pipeline.          | camera001            |

### <a name="connections"></a>Verbindungen 

Wenn Sie eine Topologie erstellen, müssen Sie die verschiedenen Module miteinander verbinden.  Dies erfolgt mit Verbindungen.  Ziehen Sie vom Kreis am Rand eines Moduls auf den Kreis des nächsten Moduls, in das Daten fließen sollen.  Dadurch wird eine Verbindung hergestellt.

Standardmäßig senden Verbindungen alle Daten von einem Modul an ein anderes. Wenn Sie nur Audiodaten oder Anwendungsdaten senden möchten, können Sie mit der linken Maustaste auf die Verbindung klicken und die Ausgabetypen bearbeiten. Auswählbare Datentypen sind Video, Audio und Anwendung. Wenn Sie keinen der Ausgabetypen auswählen, werden alle anwendbaren Daten vom Absenderknoten aus gesendet.
