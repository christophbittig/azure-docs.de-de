---
title: Azure Monitor-Arbeitsmappen mit benutzerdefinierten Parametern
description: Vereinfachen der komplexen Berichterstellung mit vordefinierten und benutzerdefiniert parametrisierten Arbeitsmappen
services: azure-monitor
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/20/2020
ms.openlocfilehash: fdf7b67a15cf8bd40dadbb709a2829408fa42a57
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129230553"
---
# <a name="interactive-workbooks"></a>Interaktive Arbeitsmappen

Mit Arbeitsmappen können Autoren interaktive Berichte und Erfahrungen für Ihre Kunden erstellen. Verschiedene Formen der Interaktivität werden unterstützt.

## <a name="parameter-changes"></a>Parameteränderungen

Wenn der Benutzer einer Arbeitsmappe einen Parameter aktualisiert, wird jedes Steuerelement, das diesen Parameter verwendet, automatisch aktualisiert und neu gezeichnet, um den neuen Zustand widerzuspiegeln. Auf diese Weise unterstützen die meisten Berichte im Azure-Portal die Interaktivität. Arbeitsmappen ermöglichen dies auf unkomplizierte Weise, und der Aufwand für Benutzer ist dabei minimal.

Erfahren Sie mehr über [Parameter in Arbeitsmappen](workbooks-parameters.md)

## <a name="grid-tile-chart-selections"></a>Raster-, Kachel- und Diagrammauswahl

Arbeitsmappen ermöglichen Benutzern das Entwickeln von Szenarien, bei denen durch Klicken auf eine Zeile in einem Raster nachfolgende Diagramme entsprechend dem Inhalt der Zeile aktualisiert werden.

Beispielsweise kann ein Benutzer über ein Raster verfügen, in dem eine Liste von Anforderungen und einige Statistiken wie z. B. Fehleranzahlen aufgeführt werden. Dieses kann so eingerichtet werden, dass durch einen Klick auf eine Zeile, die einer Anforderung entspricht, darunter liegende detaillierte Diagramme aktualisiert werden, sodass ein Filter für die konkrete Anforderung gegeben ist.

### <a name="setting-up-interactivity-on-grid-row-click"></a>Einrichten der Interaktivität für Rasterzeilen-Klicks

1. Versetzen Sie die Arbeitsmappe in den Bearbeitungsmodus, indem Sie auf das Symbolleistenelement _Bearbeiten_ klicken.
2. Klicken Sie auf den Link _Abfrage hinzufügen_, um der Arbeitsmappe ein Protokollabfrage-Steuerelement hinzuzufügen.
3. Wählen Sie für den Abfragetyp _Protokoll_ aus, wählen Sie den Ressourcentyp (z. B. Application Insights) sowie die Zielressourcen aus.
4. Geben Sie über den Abfrage-Editor die KQL für Ihre Analyse ein.

    ```kusto
    requests
    | summarize AllRequests = count(), FailedRequests = countif(success == false) by Request = name
    | order by AllRequests desc
    ```

5. Führen Sie `Run query` aus, um die Ergebnisse anzuzeigen.
6. Wählen Sie in der Fußzeile der Abfrage das Symbol _Erweiterte Einstellungen_ (das Zahnradsymbol) aus. Dadurch wird der Bereich „Erweiterte Einstellungen“ geöffnet.
7. Aktivieren Sie die Einstellung `When an item is selected, export a parameter`.
8. Wählen Sie unter der aktivierten Einstellung *Parameter hinzufügen* aus, und füllen Sie ihn mit den unten angegebenen Informationen aus.
    1. Zu exportierendes Feld: `Request`
    2. Parametername: `SelectedRequest`
    3. Standardwert: `All requests`
9. „Speichern“ auswählen

    ![Screenshot des erweiterten Editors mit Einstellungen zum Exportieren von Feldern als Parameter.](./media/workbooks-interactive/export-parameters-add.png)

10. Wählen Sie `Done Editing`aus.
11. Fügen Sie mit den Schritten 2 und 3 ein weiteres Abfragesteuerelement hinzu.
12. Geben Sie über den Abfrage-Editor die KQL für Ihre Analyse ein.
    ```kusto
    requests
    | where name == '{SelectedRequest}' or 'All Requests' == '{SelectedRequest}'
    | summarize ['{SelectedRequest}'] = count() by bin(timestamp, 1h)
    ```
13. Führen Sie `Run query` aus, um die Ergebnisse anzuzeigen.
14. Ändern Sie _Visualisierung_ in `Area chart`.
15. Wählen Sie eine Zeile im ersten Raster aus. Sie stellen fest, dass im darunterliegenden Flächendiagramm zur ausgewählten Anforderung gefiltert wird.

Der erhaltene Bericht sieht im Bearbeitungsmodus wie folgt aus:

![Screenshot der ersten beiden Abfragen im Bearbeitungsmodus.](./media/workbooks-interactive//interactivity-grid-create.png)

Die Abbildung unten zeigt einen ausführlicheren interaktiven Bericht im Lesemodus, der auf denselben Prinzipien basiert. Im Bericht werden mithilfe von Raster-Klicks Parameter exportiert; diese wiederum werden in zwei Diagrammen und einem Textblock verwendet.

![Screenshot eines Berichts mit Rasterklicks im Lesemodus.](./media/workbooks-interactive/interactivity-grid-read-mode.png)

### <a name="exporting-the-contents-of-an-entire-row"></a>Exportieren des Inhalts einer gesamten Zeile

Gelegentlich ist es wünschenswert, den gesamten Inhalt der ausgewählten Zeile und nicht nur eine bestimmte Spalte zu exportieren. Legen Sie in derartigen Fällen die `Field to export`-Eigenschaft im obigen Schritt 7.1 nicht fest. Arbeitsmappen exportieren den gesamten Zeileninhalt als JSON-Code in den Parameter.

Analysieren Sie im verweisenden KQL-Steuerelement mithilfe der `todynamic`-Funktion den JSON-Code und rufen Sie die einzelnen Spalten auf.

## <a name="grid-cell-clicks"></a>Rasterzellen-Klicks

In Arbeitsmappen können Autoren Interaktivität über eine spezielle Art von Rasterspalten-Renderer einrichten. Dieser wird als `link renderer` bezeichnet. Ein Linkrenderer konvertiert eine Rasterzelle gemäß dem Inhalt der Zelle in einen Link. Arbeitsmappen unterstützen viele Arten von Linkrenderern, u. a. Renderer, mit denen Blätter mit einer Ressourcenübersicht, Eigenschaftenbehälter-Viewer, App Insights-Suche, Nutzung, Ablaufverfolgung von Transaktionen usw. geöffnet werden können.

### <a name="setting-up-interactivity-using-grid-cell-clicks"></a>Einrichten der Interaktivität mithilfe von Rasterzellen-Klicks

1. Versetzen Sie die Arbeitsmappe in den Bearbeitungsmodus, indem Sie auf das Symbolleistenelement _Bearbeiten_ klicken.
2. Klicken Sie auf den Link _Abfrage hinzufügen_, um der Arbeitsmappe ein Protokollabfrage-Steuerelement hinzuzufügen.
3. Wählen Sie für den Abfragetyp _Protokoll_ aus, wählen Sie den Ressourcentyp (z. B. Application Insights) sowie die Zielressourcen aus.
4. Geben Sie über den Abfrage-Editor die KQL für Ihre Analyse ein.

    ```kusto
    requests
    | summarize Count = count(), Sample = any(pack_all()) by Request = name
    | order by Count desc
    ```

5. Führen Sie `Run query` aus, um die Ergebnisse anzuzeigen.
6. Wählen Sie _Spalteneinstellungen_ aus, um den Bereich mit Einstellungen zu öffnen.
7. Legen Sie im Abschnitt _Spalten_ Folgendes fest:
    1. _Beispiel_ – Säulenrenderer: `Link`, Zu öffnende Ansicht: `Cell Details`, Linkbezeichnung: `Sample`
    2. _Anzahl_ – Säulenrenderer: `Bar`, Farbpalette: `Blue`, Minimalwert: `0`
    3. _Anforderung_ – Säulenrenderer: `Automatic`
    4. Wählen Sie _Speichern und schließen_ aus, um die Änderungen anzuwenden.

    ![Screenshot der Registerkarte mit den Spalteneinstellungen.](./media/workbooks-interactive/column-settings.png)

8. Klicken Sie auf einen der `Sample`-Links im Raster. Dadurch wird ein Bereich mit Details zur getesteten Anforderung geöffnet.

    ![Screenshot des Detailbereichs der getesteten Anforderung.](./media/workbooks-interactive/details.png)

### <a name="link-renderer-actions"></a>Aktionen des Linkrenderers

| Aktion des Links | Aktion beim Klicken |
|:------------- |:-------------|
| `Generic Details` | Zeigt die Zeilenwerte auf einer Kontextregisterkarte für das Eigenschaftenraster an |
| `Cell Details` | Zeigt den Zellenwert auf einer Kontextregisterkarte für das Eigenschaftenraster an Nützlich, wenn die Zelle einen dynamischen Typ mit Informationen enthält (z. B. JSON mit Anforderungseigenschaften wie Standort, Rolleninstanz usw.). |
| `Cell Details` | Zeigt den Zellenwert auf einer Kontextregisterkarte für das Eigenschaftenraster an Nützlich, wenn die Zelle einen dynamischen Typ mit Informationen enthält (z. B. JSON mit Anforderungseigenschaften wie Standort, Rolleninstanz usw.). |
| `Custom Event Details` | Öffnet die Application Insights-Suchdetails mit der ID des benutzerdefinierten Ereignisses (`itemId`) in der Zelle |
| `* Details` | Ähnlich wie „Benutzerdefinierte Ereignisdetails“, mit Ausnahme von Abhängigkeiten, Ausnahmen, Seitenaufrufen, Anforderungen und Ablaufverfolgungen. |
| `Custom Event User Flows` | Öffnet die Benutzerflows-Erfahrung von Application Insights, pivotiert für den Namen des benutzerdefinierten Ereignisses in der Zelle. |
| `* User Flows` | Ähnlich wie „Benutzerdefinierte Ereignisbenutzerflows“, mit Ausnahme von Ausnahmen, Seitenaufrufen und Anforderungen. |
| `User Timeline` | Öffnet die Benutzerzeitachse mit der Benutzer-ID (user_Id) in der Zelle. |
| `Session Timeline` | Öffnet die Sucherfahrung von Application Insights für den Wert in der Zelle (z. B. Suchen nach dem Text „abc“, wobei „abc“ der Wert in der Zelle ist). |
| `Resource overview` | Öffnet die Übersicht zur Ressource im Portal gemäß dem Wert der Ressourcen-ID in der Zelle. |

## <a name="conditional-visibility"></a>Bedingte Sichtbarkeit

Mithilfe der Arbeitsmappe können Benutzer bestimmte Steuerelemente auf der Grundlage der Parameterwerte einblenden oder ausblenden. Dadurch können Berichte je nach Eingaben und Telemetriestatus unterschiedlich aussehen. Ein Beispiel: Für Kunden wird eine Übersicht angezeigt, wenn alles in Ordnung ist. Sollte ein Fehler vorliegen, werden die kompletten Details angezeigt.

### <a name="setting-up-interactivity-using-conditional-visibility"></a>Einrichten der Interaktivität mithilfe der bedingten Sichtbarkeit

1. Führen Sie die Schritte im Abschnitt [Einrichten der Interaktivität für Rasterzeilenklicks](#setting-up-interactivity-on-grid-row-click) aus, um zwei interaktive Steuerelemente einzurichten.
2. Fügen Sie einen neuen Parameter am Anfang hinzu:
    1. Name: `ShowDetails`
    2. Parametertyp: `Drop down`
    3. Erforderlich: `checked`
    4. Daten abrufen aus: `JSON`
    5. JSON-Eingabe: `["Yes", "No"]`
    6. Speichern Sie die Änderungen, um einen Commit auszuführen.

    ![Nachdem Sie die Schaltfläche „Parameter hinzufügen“ ausgewählt haben, wird der Bereich „Parameter bearbeiten“ angezeigt.](./media/workbooks-interactive/edit-parameter.png)

3. Legen Sie den Parameterwert auf `Yes` fest.

    ![Über der Schaltfläche „Bearbeitung abgeschlossen“ wird eine Dropdownliste angezeigt, über die Sie den Parameterwert festlegen können.](./media/workbooks-interactive/set-parameter.png)

4. Wählen Sie im Abfragesteuerelement mit dem Flächendiagramm das Symbol _Erweiterte Einstellungen_ (das Zahnradsymbol) aus.
5. Aktivieren Sie die Einstellung `Make this item conditionally visible`.
    1. Dieses Element ist sichtbar, wenn der Wert des `ShowDetails`-Parameters `equals` `Yes`
6. Wählen Sie _Bearbeitung abgeschlossen_ aus, um die Änderungen zu committen.
7. Wählen Sie auf der Symbolleiste der Arbeitsmappe _Bearbeitung abgeschlossen_ aus, um in den Lesemodus zu wechseln.
8. Ändern Sie den Wert des Parameters `ShowDetails` in `No`. Sie stellen fest, dass das Diagramm unten ausgeblendet wird.

In der folgenden Abbildung ist das Diagramm eingeblendet; dabei ist `ShowDetails` auf `Yes` festgelegt.

![Screenshot zum Veranschaulichen der bedingten Sichtbarkeit mit eingeblendetem Diagramm](./media/workbooks-interactive/interactivity-conditional-visibility-visible.png)

In der folgenden Abbildung ist das Diagramm ausgeblendet; dabei ist `ShowDetails` auf `No` festgelegt.

![Screenshot zum Veranschaulichen der bedingten Sichtbarkeit mit ausgeblendetem Diagramm](./media/workbooks-interactive/interactivity-conditional-visibility-invisible.png)

## <a name="interactivity-with-multiple-selections-in-grids-and-charts"></a>Interaktivität mit Mehrfachauswahl in Rastern und Diagrammen

Sie können mit den Schritten für Abfrage und Metriken auch Parameter exportieren, wenn eine Zeile ausgewählt ist (oder mehrere Zeilen).

![Screenshot der Einstellungen für den Parameterexport mit mehreren Parametern. ](./media/workbooks-interactive/interactivity-export-parameters.png)

1. Wechseln Sie im Abfrageschritt, in dem das Raster angezeigt wird, zu den erweiterten Einstellungen.
2. Aktivieren Sie das Kontrollkästchen `When items are selected, export parameters`. Es werden weitere Steuerelemente angezeigt.
3. Aktivieren Sie das Kontrollkästchen `allow selection of multiple values`.
    1. Die angezeigte Visualisierung ermöglicht die Mehrfachauswahl. Die Werte von exportierten Parametern sind Arrays von Werten wie bei der Verwendung von Parametern aus Dropdownlisten mit Mehrfachauswahl.
    2. Wenn diese Option deaktiviert ist, wird für die angezeigte Visualisierung nur das zuletzt ausgewählte Element berücksichtigt. Es wird nur jeweils ein einzelner Wert exportiert.
4. Verwenden Sie für jeden Parameter, den Sie exportieren möchten, die Schaltfläche *Parameter hinzufügen*. Es wird ein Popupfenster mit den Einstellungen für den zu exportierenden Parameter angezeigt.

Wenn die einfache Auswahl aktiviert ist, kann der Autor angeben, welches Feld der ursprünglichen Daten exportiert werden soll. Zu den Feldern gehören der zu verwendende „Parametername“, der „Parametertyp“ und der „Standardwert“, wenn keine Auswahl erfolgt (optional).

Wenn die Mehrfachauswahl aktiviert ist, gibt der Autor an, welches Feld der ursprünglichen Daten exportiert werden soll. Zu den Feldern gehören „Parametername“, „Parametertyp“, „Umschließen mit“ und „Trennzeichen“. Die Werte von „Umschließen mit“ und „Trennzeichen“ werden verwendet, wenn die Arraywerte beim Ersetzen in einer Abfrage in Text umgewandelt werden. Wenn bei der Mehrfachauswahl keine Werte ausgewählt werden, ist der Standardwert ein leeres Array.

> [!NOTE]
> Bei Mehrfachauswahl werden nur eindeutige Werte exportiert. Es erfolgt keine Ausgabe von Arraywerten wie z. B. „1,1,2,1“, sondern stattdessen „1,2“.

Sie können die Einstellung „Zu exportierendes Feld“ in den Exporteinstellungen leer lassen. In diesem Fall werden alle verfügbaren Felder in den Daten als in Zeichenfolgen konvertierte JSON-Objekte mit Schlüssel:Wert-Paaren exportiert. Bei Rastern und Titeln sind dies alle Felder im Raster. Bei Diagrammen sind die verfügbaren Felder die x,y-Reihen und die Bezeichnung (abhängig vom Diagrammtyp).

Parameter werden standardmäßig als Text exportiert. Wenn Sie jedoch wissen, dass es sich bei dem Feld um eine Abonnement- oder Ressourcen-ID handelt, verwenden Sie diese als Exportparametertyp. Dadurch können diese Parameter an nachgelagerten Stellen, die diese Parametertypen erfordern, auch als solche verwendet werden.

## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie mehr](./workbooks-overview.md#visualizations) über die vielen umfassenden Visualisierungsoptionen für Arbeitsmappen.
* [Steuern](./workbooks-access-control.md) Sie den Zugriff auf Ihre Arbeitsmappenressourcen, und geben Sie diese frei.