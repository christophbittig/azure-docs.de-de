---
title: 'Schnellstart: Erste Schritte mit Azure Digital Twins-Explorer'
titleSuffix: Azure Digital Twins
description: 'Schnellstart: Verwenden des Azure Digital Twins-Explorer-Beispiels, um ein vordefiniertes Szenario zu visualisieren und zu untersuchen.'
author: baanders
ms.author: baanders
ms.date: 4/27/2021
ms.topic: quickstart
ms.service: digital-twins
ms.openlocfilehash: 26063c9d0c02079b2c40eb85330be499b4bb624e
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/22/2021
ms.locfileid: "114461982"
---
# <a name="quickstart---get-started-with-a-sample-scenario-in-azure-digital-twins-explorer"></a>Schnellstart: Erste Schritte mit einem Beispielszenario in Azure Digital Twins-Explorer

Mit Azure Digital Twins können Sie Livemodelle Ihrer realen Umgebungen erstellen und mit ihnen interagieren. Zuerst modellieren Sie einzelne Elemente als **digitale Zwillinge**. Anschließend verbinden Sie sie mit einem **Wissensgraphen**, der auf Liveereignisse reagieren und nach Informationen abgefragt werden kann.

In diesem Schnellstart untersuchen Sie einen vordefinierten Azure Digital Twins-Graphen mit [Azure Digital Twins-Explorer](concepts-azure-digital-twins-explorer.md). Mit diesem Tool können Sie Ihre Azure Digital Twins-Daten innerhalb des Azure-Portals visualisieren und mit ihnen interagieren.

Sie führen die folgenden Schritte aus:

1. Erstellen einer Azure Digital Twins-Instanz, die Sie mit Azure Digital Twins-Explorer verbinden.
1. Hochladen vordefinierter Modelle und Graphdaten zum Erstellen des Beispielszenarios.
1. Untersuchen des erstellten Szenariographen.
1. Vornehmen von Änderungen am Graphen.
1. Überprüfen Ihrer Erkenntnisse aus dieser Erfahrung.

Der Beispielgraph, den Sie verwenden, stellt ein Gebäude mit zwei Etagen und zwei Räumen dar. Floor0 enthält Room0, Floor1 enthält Room1. Der Graph sieht wie die folgende Abbildung aus:

:::image type="content" source="media/quickstart-azure-digital-twins-explorer/graph-view-full.png" alt-text="Screenshot eines Graphen aus vier kreisförmigen Knoten, die in Azure Digital Twins-Explorer durch Pfeile verbunden sind":::

## <a name="prerequisites"></a>Voraussetzungen

Für diese Schnellstartanleitung ist ein Azure-Abonnement erforderlich. Bei Bedarf können Sie [jetzt kostenlos ein Abonnement erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

Sie müssen auch die Materialien für den im Schnellstart verwendeten Beispielgraphen herunterladen. Mithilfe der nachstehenden Links und Anweisungen können Sie die drei erforderlichen Dateien aus dem [GitHub-Repository „digital-twins-explorer“](https://github.com/Azure-Samples/digital-twins-explorer) herunterladen.
* [Room.json](https://raw.githubusercontent.com/Azure-Samples/digital-twins-explorer/main/client/examples/Room.json): Navigieren Sie zum Link, klicken Sie mit der rechten Maustaste auf eine beliebige Stelle des Bildschirms, und wählen Sie im Kontextmenü Ihres Browsers **Speichern unter** aus. Speichern Sie die Datei im folgenden Fenster „Speichern unter“ irgendwo auf Ihrem Computer unter dem Namen **Room.json**.
* [Floor.json](https://raw.githubusercontent.com/Azure-Samples/digital-twins-explorer/main/client/examples/Floor.json): Navigieren Sie zum Link, klicken Sie mit der rechten Maustaste auf eine beliebige Stelle des Bildschirms, und wählen Sie im Kontextmenü Ihres Browsers **Speichern unter** aus. Speichern Sie die Datei im folgenden Fenster „Speichern unter“ am selben Speicherort wie **Room.json** unter dem Namen **Floor.json**.
* [buildingScenario.xlsx](https://github.com/Azure-Samples/digital-twins-explorer/blob/main/client/examples/buildingScenario.xlsx): Navigieren Sie zum Link, und wählen Sie die Schaltfläche **Herunterladen** aus. Dadurch wird die Datei an Ihren Standardspeicherort für Downloads heruntergeladen.

    :::image type="content" source="media/quickstart-azure-digital-twins-explorer/download-building-scenario.png" alt-text="Screenshot der Datei „digital-twins-explorer/client/examples/buildingScenario.xlsx“ in GitHub. Die Schaltfläche „Herunterladen“ ist hervorgehoben." lightbox="media/quickstart-azure-digital-twins-explorer/download-building-scenario.png":::

## <a name="set-up-azure-digital-twins"></a>Einrichten von Azure Digital Twins

Für die Verwendung von Azure Digital Twins muss zunächst eine Azure Digital Twins-Instanz erstellt werden. Nachdem Sie eine Instanz des Diensts erstellt haben, können Sie in Azure Digital Twins-Explorer eine Verbindung mit der Instanz herstellen, die Sie während des Schnellstarts für die Arbeit mit der Instanz verwenden werden.

Im restlichen Teil dieses Abschnitts werden diese Schritte beschrieben.

### <a name="create-an-azure-digital-twins-instance"></a>Erstellen einer Azure Digital Twins-Instanz

[!INCLUDE [digital-twins-setup-portal.md](../../includes/digital-twins-setup-portal.md)]

3. Füllen Sie die Felder auf der Setup-Registerkarte **Grundlagen** aus. Diese umfassen u. a. Ihr Abonnement, Ihre Ressourcengruppe, den Speicherort und einen Ressourcennamen für Ihre neue Instanz. Aktivieren Sie das Kontrollkästchen **Azure Digital Twins-Datenbesitzerrolle zuweisen**, um sich selbst Berechtigungen für das Verwalten von Daten in der Instanz zu erteilen.

    >[!NOTE]
    > Wenn das Kontrollkästchen „Azure Digital Twins-Datenbesitzerrolle zuweisen“ ausgegraut ist, bedeutet dies, dass Sie in Ihrem Azure-Abonnement nicht über die erforderlichen Berechtigungen für das Verwalten des Benutzerzugriffs auf Ressourcen verfügen. Sie können die Instanz wie in diesem Abschnitt beschrieben weiter erstellen. Anschließend sollte Ihnen eine Person mit den erforderlichen Berechtigungen [diese Rolle für die Instanz zuweisen](how-to-set-up-instance-portal.md#assign-the-role-using-azure-identity-management-iam), bevor Sie den Rest des Schnellstarts abschließen.
    >
    > Allgemeine Rollen, die diese Anforderung erfüllen, heißen **Besitzer** oder **Kontoadministrator**. Ebenso können die beiden Rollen **Benutzerzugriffsadministrator** und **Mitwirkender** kombiniert werden.  

4. Wählen Sie **Überprüfen + Erstellen** aus, um das Erstellen der Instanz abzuschließen.

    :::image type="content" source= "media/quickstart-azure-digital-twins-explorer/create-azure-digital-twins-basics.png" alt-text="Screenshot des Prozesses „Ressource erstellen“ für Azure Digital Twins im Azure-Portal. Die beschriebenen Werte sind eingetragen.":::
    
5. Es wird eine Zusammenfassungsseite mit den eingegebenen Details angezeigt. Bestätigen und erstellen Sie die Instanz, indem Sie **Erstellen** auswählen.

Dadurch werden Sie zur Seite „Übersicht“ weitergeleitet, auf der der Bereitstellungsstatus der Instanz nachverfolgt wird.

### <a name="open-instance-in-azure-digital-twins-explorer"></a>Öffnen der Instanz im Azure Digital Twins-Explorer

Wenn die Bereitstellung der Instanz abgeschlossen ist, verwenden Sie die Schaltfläche **Zu Ressource wechseln**, um zur Übersichtsseite der Instanz im Portal zu navigieren.

:::image type="content" source= "media/quickstart-azure-digital-twins-explorer/deployment-complete.png" alt-text="Screenshot der Bereitstellungsseite für Azure Digital Twins im Azure-Portal. Auf der Seite wird angegeben, dass die Bereitstellung abgeschlossen ist.":::

Wählen Sie als Nächstes die Schaltfläche **Azure Digital Twins-Explorer (Vorschau) öffnen** aus.

:::image type="content" source="media/includes/azure-digital-twins-explorer-portal-access.png" alt-text="Screenshot des Azure-Portal mit der Seite „Übersicht“ für eine Azure Digital Twins-Instanz. Die Schaltfläche „Azure Digital Twins-Explorer (Vorschau) öffnen“ ist hervorgehoben." lightbox="media/includes/azure-digital-twins-explorer-portal-access.png":::

Dadurch wird ein Fenster mit Azure Digital Twins-Explorer Fenster geöffnet, das mit Ihrer Instanz verbunden ist.

:::image type="content" source="media/quickstart-azure-digital-twins-explorer/explorer-blank.png" alt-text="Screenshot: Azure Digital Twins-Explorer in einem Internetbrowser." lightbox="media/quickstart-azure-digital-twins-explorer/explorer-blank.png":::

## <a name="add-the-sample-data"></a>Hinzufügen der Beispieldaten

Importieren Sie nun das Beispielszenario und den Graphen in Azure Digital Twins-Explorer. Das Beispielszenario befindet sich in dem Ordner **digital-twins-explorer-main**, den Sie im Abschnitt [Voraussetzungen](#prerequisites) heruntergeladen haben.

### <a name="models"></a>Modelle

Der erste Schritt bei einer Azure Digital Twins-Lösung besteht darin, das Vokabular für Ihre Umgebung zu definieren. Hierzu erstellen benutzerdefinierte [Modelle](concepts-models.md),die die in Ihrer Umgebung vorhandenen Entitätstypen beschreiben.

Jedes Modell wird in einer Sprache wie JSON-LD geschrieben, die als DTDL (Digital Twin Definition Language) bezeichnet wird. Jedes Modell beschreibt einen einzelnen Entitätstyp in Bezug auf seine **Eigenschaften**, **Telemetrie**, **Beziehungen** und **Komponenten**. Diese Modelle werden später als Grundlage für digitale Zwillinge verwendet, die bestimmte Instanzen dieser Typen darstellen.

Die Modellerstellung umfasst in der Regel drei Schritte:

1. Schreiben der Modelldefinition. Im Schnellstart ist dieser Schritt bereits als Teil der Beispiellösung erfolgt.
1. Überprüfen der Modelldefinition, um sicherzustellen, dass die Syntax richtig ist. Im Schnellstart ist dieser Schritt bereits als Teil der Beispiellösung erfolgt.
1. Hochladen der Modelldefinition in Ihre Azure Digital Twins-Instanz.
 
In diesem Schnellstart wurden die Modelldateien bereits für Sie geschrieben und überprüft. Sie sind in der Lösung enthalten, die Sie heruntergeladen haben. In diesem Abschnitt werden zwei vorgefertigte Modelle in Ihre Instanz hochgeladen, um die folgenden Komponenten einer Gebäudeumgebung zu definieren:

* Etage
* Raum

#### <a name="upload-models"></a>Hochladen von Modellen

Führen Sie die folgenden Schritte aus, um Modelle hochzuladen.

1. Wählen Sie im Panel **Modelle** das Symbol **Modell hochladen** aus, das einen in eine Wolke zeigenden Pfeil darstellt.

   :::image type="content" source="media/quickstart-azure-digital-twins-explorer/upload-model.png" alt-text="Screenshot des Azure Digital Twins-Explorers, in dem das Panel „Modelle“ und das Symbol „Modell hochladen“ hervorgehoben sind." lightbox="media/quickstart-azure-digital-twins-explorer/upload-model.png":::
 
1. Navigieren Sie im eingeblendeten Fenster „Öffnen“ zum Ordner, der die zuvor heruntergeladenen Dateien **Room.json** und **Floor.json** enthält.
1. Wählen Sie **Room.json** und **Floor.json** und dann **Öffnen** aus, um beide hochzuladen. 

Azure Digital Twins-Explorer lädt diese Modelldateien in Ihre Azure Digital Twins-Instanz hoch. Sie sollten im Panel **Modelle** mit ihren Anzeigenamen und vollständigen Modell-IDs zu sehen sein. Sie können die Informationssymbole **View Model** (Modell anzeigen) auswählen, um den zugrunde liegenden DTDL-Code anzuzeigen.

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-azure-digital-twins-explorer/model-info.png" alt-text="Screenshot des Azure Digital Twins-Explorers mit dem Panel „Modelle“ mit zwei Modelldefinitionen: Floor und Room." lightbox="media/quickstart-azure-digital-twins-explorer/model-info.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

### <a name="twins-and-the-twin-graph"></a>Zwillinge und der Zwillingsgraph

Nachdem Sie nun Modelle in Ihre Azure Digital Twins-Instanz hochgeladen haben, können Sie [digitale Zwillinge](concepts-twins-graph.md) hinzufügen, die den Modelldefinitionen entsprechen.

Digitale Zwillinge stellen die tatsächlichen Entitäten in Ihrer Geschäftsumgebung dar. Es kann sich z. B. um Sensoren für eine Farm, Scheinwerfer in einem Auto oder – in diesem Schnellstart – Räume auf einer Etage eines Gebäudes sein. Sie können viele Zwillinge eines beliebigen Modelltyps erstellen, z. B. mehrere Räume, die alle das Modell Room (Raum) verwenden. Sie verbinden diese mit Beziehungen zu einem **Zwillingsgraphen**, der die gesamte Umgebung darstellt.

In diesem Abschnitt werden vorab erstellte Zwillinge hochgeladen, die in einem vorab erstellten Graphen miteinander verknüpft sind. Der Graph enthält zwei Etagen und zwei Räume mit folgendem Layout:

* Floor0 (Etage0)
    - Enthält Room0 (Raum0)
* Floor1 (Etage1)
    - Enthält Room1 (Raum1)

#### <a name="import-the-graph"></a>Importieren des Graphen

Führen Sie diese Schritte aus, um den Graphen zu importieren.

1. Wählen Sie im Panel **Zwillingsdiagramm** das Symbol **Diagramm importieren**, das einen in eine Wolke zeigenden Pfeil darstellt.

   :::image type="content" source="media/quickstart-azure-digital-twins-explorer/import-graph.png" alt-text="Screenshot des Azure Digital Twins-Explorers mit dem Panel „Graphansicht“ mit hervorgehobenem Symbol „Diagramm importieren“." lightbox="media/quickstart-azure-digital-twins-explorer/import-graph.png":::

2. Navigieren Sie im Fenster „Öffnen“ zur zuvor heruntergeladenen Datei **buildingScenario.xlsx**. Diese Datei enthält eine Beschreibung des Beispielgraphen. Wählen Sie **Open**(Öffnen).

   Nach wenigen Sekunden wird von Azure Digital Twins-Explorer eine Ansicht **Import** mit einer Vorschau des zu ladenden Graphen angezeigt.

3. Wählen Sie zum Bestätigen des Hochladens des Graphen rechts oben im Panel „Graph (Vorschau)“ das Symbol **Speichern** aus.

   :::row:::
    :::column:::
        :::image type="content" source="media/quickstart-azure-digital-twins-explorer/graph-preview-save.png" alt-text="Screenshot des Azure Digital Twins-Explorers mit hervorgehobenem Symbol „Speichern“ im Bereich „Graph (Vorschau)“." lightbox="media/quickstart-azure-digital-twins-explorer/graph-preview-save.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
   :::row-end:::

4. Azure Digital Twins-Explorer nutzt die hochgeladene Datei, um die angeforderten Zwillinge und Beziehungen zwischen ihnen zu erstellen. Wenn der Vorgang abgeschlossen ist, wird ein Dialogfeld angezeigt. Klicken Sie auf **Schließen**.

   :::row:::
    :::column:::
        :::image type="content" source="media/quickstart-azure-digital-twins-explorer/import-success.png" alt-text="Screenshot des Azure Digital Twins-Explorers mit einem Dialogfeld, das den Erfolg des Imports des Graphen angibt." lightbox="media/quickstart-azure-digital-twins-explorer/import-success.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
   :::row-end:::

5. Der Graph ist nun in Azure Digital Twins-Explorer hochgeladen. Kehren Sie zurück zum Bereich **Twin Graph** (Zwillingsgraph).
 
   :::image type="content" source="media/quickstart-azure-digital-twins-explorer/twin-graph-tab.png" alt-text="Screenshot des Azure Digital Twins-Explorers mit hervorgehobener Registerkarte „Zwillingsgraph“." lightbox="media/quickstart-azure-digital-twins-explorer/twin-graph-tab.png"::: 
 
6. Um den Graphen zu sehen, wählen Sie im Panel **Abfrage-Explorer** nahe dem oberen Rand des Fensters „Azure Digital Twins-Explorer“ die Schaltfläche **Abfrage ausführen** aus.

   :::image type="content" source="media/quickstart-azure-digital-twins-explorer/run-query.png" alt-text="Screenshot des Azure Digital Twins-Explorers mit hervorgehobenen Schaltfläche „Abfrage ausführen“ rechts oben im Fenster." lightbox="media/quickstart-azure-digital-twins-explorer/run-query.png":::

Diese Aktion führt die Standardabfrage zum Auswählen und Anzeigen aller digitalen Zwillinge aus. Azure Digital Twins-Explorer ruft alle Zwillinge und Beziehungen aus dem Dienst ab. Der durch diese definierte Graph wird im Bereich **Twin Graph** (Zwillingsgraph) gezeichnet.

## <a name="explore-the-graph"></a>Erkunden des Graphen

Nun können Sie sich den hochgeladenen Graphen des Beispielszenarios ansehen.

:::image type="content" source="media/quickstart-azure-digital-twins-explorer/graph-view-full.png" alt-text="Screenshot des Azure Digital Twins-Explorers mit dem Panel „Graphansicht“ und enthaltenem Zwillingsgraphen.":::

Die Kreise (Graph„knoten“) stellen digitale Zwillinge dar. Die Linien stellen Beziehungen dar. Room0 (Raum0) ist im Zwilling Floor0 (Etage0) und Room1 (Raum1) im Zwilling Floor1 (Etage1) enthalten.

Mit einer Maus können Sie auf Teile des Graphen klicken und sie durch Ziehen verschieben.

### <a name="view-twin-properties"></a>Anzeigen von Zwillingseigenschaften

Sie können einen Zwilling auswählen, um eine Liste der zugehörigen Eigenschaften sowie deren Werte im Bereich **Eigenschaften** anzuzeigen.

Hier sehen Sie die Eigenschaften von Room0 (Raum0):

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-azure-digital-twins-explorer/properties-room0.png" alt-text="Screenshot des Azure Digital Twins-Explorers mit hervorgehobenem Panel „Eigenschaften“, der die Eigenschaften $dtId, Temperatur und Luftfeuchtigkeit für Room0 zeigt." lightbox="media/quickstart-azure-digital-twins-explorer/properties-room0.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Die Temperatur in Room0 (Raum0) beträgt 70 Grad Fahrenheit.

Hier sehen Sie die Eigenschaften von Room1 (Raum1):

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-azure-digital-twins-explorer/properties-room1.png" alt-text="Screenshot des Azure Digital Twins-Explorers mit hervorgehobenem Panel „Eigenschaften“, der die Eigenschaften $dtId, Temperatur und Luftfeuchtigkeit für Room1 zeigt." lightbox="media/quickstart-azure-digital-twins-explorer/properties-room1.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Die Temperatur im Room1 (Raum0) beträgt 80 Grad Fahrenheit.

### <a name="query-the-graph"></a>Abfragen des Diagramms

Eine Hauptfunktion von Azure Digital Twins ist das einfache [Abfragen](concepts-query-language.md) des Zwillingsgraphen und das effiziente Beantworten von Fragen zur Umgebung.

Zum Abfragen der Zwillinge in Ihrem Graphen können unter anderem deren **Eigenschaften** verwendet werden. Abfragen auf der Grundlage von Eigenschaften können Ihnen helfen, eine Vielzahl von Fragen zu beantworten. Beispielsweise können Sie Ausreißer in Ihrer Umgebung suchen, die möglicherweise Aufmerksamkeit benötigen.

In diesem Abschnitt führen Sie eine Abfrage aus, um die Frage zu beantworten, wie viele Zwillinge in Ihrer Umgebung eine Temperatur über 75 Grad Fahrenheit aufweisen.

Führen Sie zur Beantwortung dieser Frage im Panel **Abfrage-Explorer** die folgende Abfrage aus.

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="TemperatureQuery":::

Wie Sie weiter oben bei der Betrachtung der Zwillingseigenschaften gesehen haben, weist Room0 (Raum0) eine Temperatur von 70 und Room1 (Raum1) eine Temperatur von 80 Grad Fahrenheit auf. Aus diesem Grund wird hier in den Ergebnissen nur Room1 (Raum1) angezeigt.
    
:::image type="content" source="media/quickstart-azure-digital-twins-explorer/result-query-property-before.png" alt-text="Screenshot des Azure Digital Twins-Explorers mit den Ergebnissen der Eigenschaftenabfrage, die nur Room1 enthalten." lightbox="media/quickstart-azure-digital-twins-explorer/result-query-property-before.png":::

>[!TIP]
> In der Abfrage oben werden auch andere Vergleichsoperatoren (<,>, = oder !=) unterstützt. Sie können in der Abfrage mit diesen Operatoren sowie mit anderen Werten und Zwillingseigenschaften experimentieren, um andere Fragen zu beantworten.

## <a name="edit-data-in-the-graph"></a>Bearbeiten von Daten im Graphen

Sie können Azure Digital Twins-Explorer verwenden, um die Eigenschaften der im Graphen dargestellten Zwillinge zu bearbeiten. In diesem Abschnitt erhöhen Sie die Temperatur von Room0 (Raum0) auf 76 Grad Fahrenheit.

Um zu beginnen, führen Sie die folgende Abfrage erneut aus, um alle digitalen Zwillinge auszuwählen. Dadurch wird der vollständige Graph erneut im Bereich **Twin Graph** (Zwillingsgraph) angezeigt.

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="GetAllTwins":::

Wählen Sie **Room0** aus, um die zugehörige Eigenschaftenliste im Panel **Eigenschaften** anzuzeigen.

Die Eigenschaften in dieser Liste können bearbeitet werden. Wählen Sie den Temperaturwert **70** aus, um die Eingabe eines neuen Werts zu ermöglichen. Geben Sie **76** ein, und wählen Sie das Symbol **Save** (Speichern) aus, um die Temperatur auf **76** zu aktualisieren.

:::row:::
    :::column:::
        :::image type="content" source="media/quickstart-azure-digital-twins-explorer/new-properties-room0.png" alt-text="Screenshot des Azure Digital Twins-Explorers mit Hervorhebung, dass im Panel „Eigenschaften“ Eigenschaften gezeigt werden, die für Room0 bearbeitet werden können." lightbox="media/quickstart-azure-digital-twins-explorer/new-properties-room0.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Nun wird das Fenster **Patch Information** (Patchinformationen) mit dem Patchcode angezeigt, der im Hintergrund mit den [APIs](concepts-apis-sdks.md) von Azure Digital Twins verwendet wurde, um die Aktualisierung durchzuführen. Klicken Sie auf **Schließen**.

### <a name="query-to-see-the-result"></a>Ausführen der Abfrage, um die Ergebnisse anzuzeigen

Vergewissern Sie sich, dass die Aktualisierung der Temperatur für Room0 (Raum0) vom Graphen registriert wurde. Führen Sie dazu erneut die weiter oben beschriebene Abfrage aus, um alle Zwillinge in der Umgebung abzurufen, bei denen die Temperatur über 75 Grad Fahrenheit liegt.

:::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="TemperatureQuery":::

Nachdem die Temperatur von Room0 (Raum0) von 70 in 76 geändert wurde, sollten im Ergebnis beide Zwillinge angezeigt werden.

:::image type="content" source="media/quickstart-azure-digital-twins-explorer/result-query-property-after.png" alt-text="Screenshot des Azure Digital Twins-Explorers mit den Ergebnissen der Eigenschaftenabfrage, die sowohl Room0 als auch Room1 enthalten." lightbox="media/quickstart-azure-digital-twins-explorer/result-query-property-after.png":::

## <a name="review-and-contextualize-learnings"></a>Überprüfen und Kontextualisieren der Erkenntnisse

In diesem Schnellstart haben Sie eine Azure Digital Twins-Instanz erstellt und sie mithilfe von Azure Digital Twins-Explorer mit einem Beispielszenario aufgefüllt.

Anschließend haben Sie folgende Aktionen für den Graphen ausgeführt:

* Sie haben eine Abfrage zur Beantwortung einer szenariobezogenen Frage ausgeführt.
* Sie haben eine Eigenschaft eines digitalen Zwillings bearbeitet.
* Sie haben die Abfrage erneut ausgeführt, um zu sehen, wie sich die Antwort nach Ihrer Aktualisierung geändert hat.

Diese Übung veranschaulicht, wie Sie den Azure Digital Twins-Graphen verwenden können, um Fragen zu einer Umgebung zu beantworten, auch wenn sich diese verändert.

In diesem Schnellstart haben Sie die Temperaturaktualisierung manuell durchgeführt. In Azure Digital Twins werden digitale Zwillinge jedoch häufig mit echten IoT-Geräten verbunden, um automatische, auf Telemetriedaten basierende Aktualisierungen zu erhalten. Auf diese Weise können Sie einen Livegraphen erstellen, der immer den tatsächlichen Zustand Ihrer Umgebung widerspiegelt. Mithilfe von Abfragen können Sie Informationen zu den Vorgängen in Ihrer Umgebung in Echtzeit erhalten.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Zur Bereinigung nach diesem Schnellstart wählen Sie, je nachdem, was Sie als Nächstes tun möchten, welche Ressourcen Sie entfernen möchten.

* **Wenn Sie mit den Tutorials für Azure Digital Twins fortfahren möchten**, können Sie die in dieser Schnellstartanleitung verwendete Instanz in den entsprechenden Artikeln wiederverwenden und müssen sie nicht entfernen.

[!INCLUDE [digital-twins-cleanup-clear-instance.md](../../includes/digital-twins-cleanup-clear-instance.md)]
 
[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

Möglicherweise möchten Sie auch den Ordner des Beispielprojekts von Ihrem lokalen Computer löschen.

## <a name="next-steps"></a>Nächste Schritte

Fahren Sie mit den Tutorials für Azure Digital Twins fort, um Ihr eigenes Azure Digital Twins-Szenario und Ihre eigenen Interaktionstools zu erstellen.

> [!div class="nextstepaction"]
> [Codieren einer Client-App](tutorial-code.md)
