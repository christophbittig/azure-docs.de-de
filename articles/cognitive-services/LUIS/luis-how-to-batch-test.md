---
title: Durchführen eines Batchtests – LUIS
titleSuffix: Azure Cognitive Services
description: Verwenden Sie LUIS-Batchtestsets (Language Understanding), um Äußerungen mit falschen Absichten und Entitäten zu suchen.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/18/2021
ms.openlocfilehash: 0039f84305233f9894c31ba6210a2d531c567498
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131005953"
---
# <a name="batch-testing-with-a-set-of-example-utterances"></a>Batchtests mit einem Satz von Beispieläußerungen

Mit Batchtests wird Ihre aktive trainierte Version überprüft, um ihre Vorhersagegenauigkeit zu messen. Ein Batchtest hilft Ihnen, die Genauigkeit der einzelnen Absichten und Entitäten in Ihrer aktuellen Version zu visualisieren. Überprüfen Sie die Ergebnisse des Batchtests, um geeignete Maßnahmen zum Verbessern der Genauigkeit einzuleiten, etwa indem Sie einer Absicht weitere Beispieläußerungen hinzufügen, wenn Ihre App häufig Fehler beim Erkennen der korrekten Absicht oder beim Bezeichnen von Entitäten innerhalb der Äußerung macht.

## <a name="group-data-for-batch-test"></a>Gruppieren von Daten für Batchtests

Es ist wichtig, dass die für Batchtests verwendeten Äußerungen für LUIS neu sind. Wenn Sie über ein Dataset mit Äußerungen verfügen, teilen Sie die Äußerungen in die folgenden drei Gruppen auf: Beispieläußerungen, die einer Absicht hinzugefügt wurden, Äußerungen, die vom veröffentlichten Endpunkt empfangen wurden und Äußerungen, die für Batchtests von LUIS nach dem Training verwendet werden.

Die von Ihnen verwendete JSON-Batchdatei sollte Äußerungen mit bezeichneten Machine Learning-Entitäten der obersten Ebene enthalten, einschließlich Start- und Endposition. Die Äußerungen sollten nicht Teil der Beispiele sein, die bereits in der App enthalten sind. Es sollte sich um Äußerungen handeln, für die Sie in Bezug auf Absichten und Entitäten positive Vorhersagen treffen möchten.

Sie können Tests nach Absicht bzw. Entität unterteilen oder alle Tests (maximal 1.000 Äußerungen) in derselben Datei vorhalten. 

### <a name="common-errors-importing-a-batch"></a>Häufige Fehler beim Importieren eines Batches

Wenn beim Hochladen der Batchdatei in LUIS Fehler auftreten, überprüfen Sie, ob die folgenden häufig auftretenden Probleme vorliegen:

* Mehr als 1.000 Äußerungen in einer Batchdatei
* Ein JSON-Äußerungsobjekt ohne Entitätseigenschaft. Die Eigenschaft kann ein leeres Array sein.
* Wörter, die in mehreren Entitäten bezeichnet werden
* Entitätsbezeichnungen, die mit einem Leerzeichen beginnen oder enden

## <a name="fixing-batch-errors"></a>Beheben von Batchfehlern

Wenn im Batchtest Fehler auftreten, können Sie entweder weitere Äußerungen zu einer Absicht hinzufügen und/oder weitere Äußerungen mit der Entität bezeichnen, um LUIS bei der Unterscheidung zwischen Absichten zu unterstützen. Wenn Sie Äußerungen hinzugefügt und sie bezeichnet haben und trotzdem noch Vorhersagefehler beim Batchtest erhalten, erwägen Sie, ein [Begriffslisten](luis-concept-feature.md)-Feature mit domänenspezifischem Fachwortschatz hinzuzufügen, um LUIS beim schnelleren Lernen zu unterstützen.


<a name="batch-testing"></a>

## <a name="batch-testing-using-the-luis-portal"></a>Batchtests im LUIS-Portal 

### <a name="import-and-train-an-example-app"></a>Importieren und Trainieren einer Beispiel-App

Importieren Sie eine App, mit der eine Pizzabestellung angenommen wird, z. B. `1 pepperoni pizza on thin crust`.

1.  Laden Sie die [App-JSON-Datei](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/pizza-with-machine-learned-entity.json?raw=true) herunter, und speichern Sie sie.

1. Melden Sie sich beim [LUIS-Portal](https://www.luis.ai) an, und wählen Sie Ihr **Abonnement** und Ihre **Erstellungsressource** aus, um die Apps anzuzeigen, die dieser Erstellungsressource zugewiesen sind.
1. Wählen Sie den Pfeil neben **Neue App** aus, und klicken Sie auf **Als JSON importieren**, um die JSON-Datei in eine neue App zu importieren. Benennen Sie die App `Pizza app`.


1. Wählen Sie oben rechts im Navigationsbereich die Option **Trainieren** aus, um die App zu trainieren.


[!INCLUDE [Entity roles in batch testing - currently not supported](../../../includes/cognitive-services-luis-roles-not-supported-in-batch-testing.md)]

### <a name="batch-test-file"></a>Batchtestdatei

Der JSON-Beispielcode enthält eine Äußerung mit einer bezeichneten Entität, um darzustellen, wie eine Testdatei aussieht. Für Ihre eigenen Tests sollten Sie viele Äußerungen mit der richtigen Absicht und einer bezeichneten Machine Learning-Entität verwenden.

1. Erstellen Sie `pizza-with-machine-learned-entity-test.json` in einem Text-Editor, oder [laden Sie](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/batch-tests/pizza-with-machine-learned-entity-test.json?raw=true) es herunter.

2. Fügen Sie in der Batchdatei im JSON-Format eine Äußerung mit der **Absicht** hinzu, für die Sie im Test eine Vorhersage erhalten möchten.

   [!code-json[Add the intents to the batch test file](~/samples-cognitive-services-data-files/luis/batch-tests/pizza-with-machine-learned-entity-test.json "Add the intent to the batch test file")]

## <a name="run-the-batch"></a>Ausführen des Batchs

1. Wählen Sie auf der oberen Navigationsleiste **Test** aus.

2. Wählen Sie im rechten Bereich **Batch testing panel** (Batchtestbereich) aus.

    ![Link zum Testen in Batches](./media/luis-how-to-batch-test/batch-testing-link.png)

3. Wählen Sie **Importieren** aus. Wählen Sie im daraufhin angezeigten Dialogfeld **Datei auswählen** aus, und suchen Sie eine JSON-Datei mit dem richtigen JSON-Format, die *höchstens 1.000* zu testende Äußerungen enthält.

    Fehler beim Importieren werden auf einer roten Benachrichtigungsleiste am oberen Rand des Browsers gemeldet. Wenn ein Import Fehler aufweist, wird kein Dataset erstellt. Weitere Informationen finden Sie unter [Häufige Fehler](#common-errors-importing-a-batch).

4. Wählen Sie den Speicherort für die Datei `pizza-with-machine-learned-entity-test.json` aus.

5. Versehen Sie das Dataset mit dem Namen `pizza test`, und klicken Sie auf **Fertig**.

6. Wählen Sie die Schaltfläche **Ausführen**. 

7. Nach Abschluss des Batchtests werden die folgenden Spalten angezeigt:

    | Spalte | BESCHREIBUNG |
    | -------- | ------------- |
    | State | Der Status des Tests. **Ergebnisse anzeigen** wird erst angezeigt, nachdem der Test abgeschlossen wurde. |
    | Name | Der Name, den Sie dem Test gegeben haben. |
    | Size | Die Anzahl der Tests in dieser Batchtestdatei. |
    | Zuletzt ausgeführt | Das Datum der letzten Ausführung dieser Batchtestdatei. |
    | Letztes Ergebnis | Die Anzahl erfolgreicher Vorhersagen im Test. |

8. Um detaillierte Ergebnisse des Tests anzuzeigen, wählen Sie **Ergebnisse anzeigen** aus. 

    > [!TIP]
    > * Wenn Sie **Download** auswählen, wird dieselbe Datei heruntergeladen, die Sie hochgeladen haben.
    > * Wenn Sie sehen, dass beim Batchtest ein Fehler aufgetreten ist, entsprach mindestens eine Absicht einer Äußerung nicht der Vorhersage.

<a name="access-batch-test-result-details-in-a-visualized-view"></a>

### <a name="review-batch-results-for-intents"></a>Überprüfen der Batchergebnisse für Absichten

Wählen Sie zum Überprüfen der Batchtestergebnisse **See results** (Ergebnisse anzeigen) aus. Die Testergebnisse zeigen grafisch, wie die Testäußerungen für die aktive Version vorhergesagt wurden.

Das Batchdiagramm zeigt die Ergebnisse in vier Quadranten an. Rechts neben dem Diagramm befindet sich ein Filter. Der Filter enthält Absichten und Entitäten. Wenn Sie einen [Diagrammabschnitt](#review-batch-results-for-intents) oder einen Punkt innerhalb des Diagramms auswählen, werden die zugehörigen Äußerungen unterhalb des Diagramms angezeigt.

Wenn Sie auf das Diagramm zeigen, können Sie mit dem Mausrad die Ansicht im Diagramm vergrößern oder verkleinern. Dies ist hilfreich, wenn es viele Punkte im Diagramm gibt, die nah beieinander gruppiert sind.

Das Diagramm ist in vier Quadranten unterteilt, von denen zwei in Rot angezeigt werden.

1. Wählen Sie in der Filterliste die Absicht **ModifyOrder** aus. Die Äußerung wird als **Richtig positiv** vorhergesagt. Dies bedeutet, dass die Äußerung erfolgreich mit der zugehörigen positiven Vorhersage in der Batchdatei abgeglichen werden konnte.

    > [!div class="mx-imgBorder"]
    > ![Erfolgreicher Abgleich der Äußerung mit der positiven Vorhersage](./media/luis-tutorial-batch-testing/intent-predicted-true-positive.png)

    Die grünen Häkchen in der Filterliste zeigen auch den Erfolg des Tests für jede Absicht an. Alle anderen Absichten sind mit einer positiven Bewertung von „1/1“ angegeben, da die Äußerung für jede Absicht getestet wurde – als negativer Test für alle Absichten, die im Batchtest nicht aufgeführt sind.

1. Wählen Sie die Absicht **Bestätigung** aus. Diese Absicht ist im Batchtest nicht aufgelistet. Es handelt sich hierbei also um einen negativen Test der Äußerung aus dem Batchtest.

    > [!div class="mx-imgBorder"]
    > ![Erfolgreiche negative Vorhersage für die Äußerung für eine nicht in der Batchdatei enthaltene Absicht](./media/luis-tutorial-batch-testing/true-negative-intent.png)

    Der negative Test war erfolgreich. Dies ist am grünen Text im Filter und am Raster zu erkennen.

### <a name="review-batch-test-results-for-entities"></a>Überprüfen der Ergebnisse von Batchtests für Entitäten

Die Entität „ModifyOrder“ als Computerentität mit Unterentitäten zeigt an, ob sich für die Entität der obersten Ebene eine Übereinstimmung ergibt und wie die Vorhersage für die Unterentitäten lautet.

1. Wählen Sie in der Filterliste die Entität **ModifyOrder** und anschließend im Raster den Kreis aus.

1. Die Entitätsvorhersage wird unterhalb des Diagramms angezeigt. Die Anzeige enthält durchgehende Linien für Vorhersagen, die der Erwartung entsprechen, und gepunktete Linien für Vorhersagen, die die Erwartung nicht erfüllen.

    > [!div class="mx-imgBorder"]
    > ![Erfolgreiche Vorhersage des übergeordneten Entitätselements in der Bachdatei](./media/luis-tutorial-batch-testing/labeled-entity-prediction.png)

<a name="filter-chart-results-by-intent-or-entity"></a>

#### <a name="filter-chart-results"></a>Filtern von Diagrammergebnissen

Um das Diagramm nach einer bestimmten Absicht oder Entität zu filtern, wählen Sie die Absicht bzw. die Entität im Filterbereich rechts aus. Die Datenpunkte und ihre Verteilung im Diagramm werden entsprechend Ihrer Auswahl aktualisiert.

![Visualisierte Batchtestergebnisse](./media/luis-how-to-batch-test/filter-by-entity.png)

### <a name="chart-result-examples"></a>Beispiele für Ergebnisse im Diagramm darstellen

Mit dem Diagramm im LUIS-Portal können Sie folgende Aktionen ausführen:
 
#### <a name="view-single-point-utterance-data"></a>Anzeigen von Einzelpunkt-Äußerungsdaten

Zeigen Sie im Diagramm auf einen Datenpunkt, um die Bewertung seiner Vorhersage anzuzeigen. Wählen Sie einen Datenpunkt aus, um die zugehörige Äußerung in der Liste der Äußerungen am unteren Rand der Seite abzurufen.

![Ausgewählte Äußerung](./media/luis-how-to-batch-test/selected-utterance.png)


<a name="relabel-utterances-and-retrain"></a>
<a name="false-test-results"></a>

#### <a name="view-section-data"></a>Anzeigen von Abschnittsdaten

Wählen Sie im Diagramm mit vier Abschnitten den Namen des Abschnitts aus, z.B. **Falsch positive Ergebnisse** rechts oben im Diagramm. Unter dem Diagramm werden alle Äußerungen in diesem Abschnitt in einer Liste angezeigt.

![Ausgewählte Äußerungen nach Abschnitt](./media/luis-how-to-batch-test/selected-utterances-by-section.png)

Im vorhergehenden Bild ist die Äußerung `switch on` mit der Absicht „TurnAllOn“ bezeichnet, erhielt jedoch als Vorhersage die Absicht „None“. Dies ist ein Hinweis darauf, dass für die Absicht „TurnAllOn“ weitere Beispieläußerungen erforderlich sind, um die erwartete Vorhersage zu treffen.

Die beiden Abschnitte des Diagramms in Rot weisen auf Äußerungen hin, bei denen die Vorhersage nicht mit der erwarteten übereinstimmte. Diese zeigen Äußerungen an, für die LUIS mehr Training benötigt.

In den beiden Abschnitten des Diagramms in Grün entsprach die Vorhersage der Erwartung.

## <a name="batch-testing-using-the-rest-api"></a>Batchtests mithilfe der REST-API 

LUIS ermöglicht Ihnen Batchtests im LUIS-Portal und mithilfe der REST-API. Die Endpunkte für die REST-API sind unten aufgeführt. Informationen zu Batchtests im LUIS-Portal finden Sie unter [Tutorial: Batchtests von Datasets](). Verwenden Sie die unten aufgeführten vollständigen URLs, und ersetzen Sie die Platzhalterwerte durch Ihren eigenen LUIS-Vorhersageschlüssel und Ihren Endpunkt. 

Vergessen Sie nicht, im Header Ihren LUIS-Schlüssel zu `Ocp-Apim-Subscription-Key` hinzuzufügen, und legen Sie `Content-Type` auf `application/json` fest.

### <a name="start-a-batch-test"></a>Starten eines Batchtests

Starten Sie einen Batchtest, und verwenden Sie entweder eine App-Versions-ID oder einen Veröffentlichungsslot. Senden Sie eine **POST**-Anforderung an eins der folgenden Endpunktformate. Fügen Sie Ihre Batchdatei in den Text der Anforderung ein.

Veröffentlichungsslot
* `<YOUR-PREDICTION-ENDPOINT>/luis/v3.0-preview/apps/<YOUR-APP-ID>/slots/<YOUR-SLOT-NAME>/evaluations`

App-Versions-ID
* `<YOUR-PREDICTION-ENDPOINT>/luis/v3.0-preview/apps/<YOUR-APP-ID>/versions/<YOUR-APP-VERSION-ID>/evaluations`

Diese Endpunkte geben eine Vorgangs-ID zurück, die Sie verwenden, um den Status zu überprüfen und Ergebnisse abzurufen. 


### <a name="get-the-status-of-an-ongoing-batch-test"></a>Den Status eines laufenden Batchtests abrufen

Verwenden Sie die Vorgangs-ID des Batch Tests, den Sie gestartet haben, um seinen Status aus den folgenden Endpunktformaten abzurufen: 

Veröffentlichungsslot
* `<YOUR-PREDICTION-ENDPOINT>/luis/v3.0-preview/apps/<YOUR-APP-ID>/slots/<YOUR-SLOT-ID>/evaluations/<YOUR-OPERATION-ID>/status`

App-Versions-ID
* `<YOUR-PREDICTION-ENDPOINT>/luis/v3.0-preview/apps/<YOUR-APP-ID>/versions/<YOUR-APP-VERSION-ID>/evaluations/<YOUR-OPERATION-ID>/status`

### <a name="get-the-results-from-a-batch-test"></a>Abrufen der Ergebnisse eines Batchtests

Verwenden Sie die Vorgangs-ID des Batch Tests, den Sie gestartet haben, um seine Ergebnisse aus den folgenden Endpunktformaten abzurufen: 

Veröffentlichungsslot
* `<YOUR-PREDICTION-ENDPOINT>/luis/v3.0-preview/apps/<YOUR-APP-ID>/slots/<YOUR-SLOT-ID>/evaluations/<YOUR-OPERATION-ID>/result`

App-Versions-ID
* `<YOUR-PREDICTION-ENDPOINT>/luis/v3.0-preview/apps/<YOUR-APP-ID>/versions/<YOUR-APP-VERSION-ID>/evaluations/<YOUR-OPERATION-ID>/result`


### <a name="batch-file-of-utterances"></a>Batchdatei mit Äußerungen

Senden Sie eine Batchdatei mit Äußerungen, die als *Dataset* bezeichnet wird, zum Ausführen eines Batchtests. Das Dataset ist eine Datei im JSON-Format, die maximal 1.000 bezeichnete Äußerungen enthält. Sie können bis zu 10 Datasets in einer App testen. Wenn Sie mehr Datasets testen müssen, löschen Sie ein Dataset, und fügen Sie dann ein neues hinzu. Alle benutzerdefinierten Entitäten im Modell werden auch dann im Batchtest-Entitätenfilter angezeigt, wenn keine entsprechenden Entitäten in den Batchdateidaten vorhanden sind.

Die Batchdatei besteht aus Äußerungen. Jeder Äußerung muss eine erwartete Absichtsvorhersage in Verbindung mit allen [Machine-Learning-Entitäten](luis-concept-entity-types.md#machine-learned-ml-entity) zugeordnet sein, deren Erkennung Sie erwarten.

### <a name="batch-syntax-template-for-intents-with-entities"></a>Batchsyntaxvorlage für Absichten mit Entitäten

Verwenden Sie die folgende Vorlage, um die Batchdatei zu starten:

```JSON
{
    "LabeledTestSetUtterances": [
        {
            "text": "play a song",
            "intent": "play_music",
            "entities": [
                {
                    "entity": "song_parent",
                    "startPos": 0,
                    "endPos": 15,
                    "children": [
                        {
                            "entity": "pre_song",
                            "startPos": 0,
                            "endPos": 3
                        },
                        {
                            "entity": "song_info",
                            "startPos": 5,
                            "endPos": 15
                        }
                    ]
                }
            ]
        }
    ]
}

```

Die Batchdatei erkennt anhand der Eigenschaften **startPos** und **endPos** Anfang und Ende einer Entität. Die Werte sind nullbasiert und dürfen nicht mit einem Leerzeichen beginnen oder enden. Dies ist ein Unterschied zu den Abfrageprotokollen, die die Eigenschaften „startIndex“ und „endIndex“ verwenden.

Wenn Sie keine Entitäten testen möchten, schließen Sie die Eigenschaft `entities` ein, und legen Sie den Wert auf ein leeres Array (`[]`) fest.

### <a name="rest-api-batch-test-results"></a>REST-API-Batchtestergebnisse

Von der API werden mehrere Objekte zurückgegeben:

* Information zu den Absichts- und Entitätsmodellen, wie etwa der Genauigkeit, dem Abruf und der F-Bewertung.
* Informationen zu den Entitätsmodellen, wie etwa Genauigkeit, Abruf und F-Bewertung, für jede Entität 
  * Mithilfe des `verbose`-Flags können Sie weitere Informationen zur Entität erhalten, wie etwa `entityTextFScore` und `entityTypeFScore`.
* Bereitgestellte Äußerungen mit den vorhergesagten und bezeichneten Absichtsnamen
* Eine Liste falsch positiver Entitäten und eine Liste falsch negativer Entitäten.

## <a name="next-steps"></a>Nächste Schritte

Wenn sich in Tests herausstellt, dass Ihre LUIS-App nicht die richtige Absichten und Entitäten erkennt, können Sie die Leistung Ihrer LUIS-App verbessern, indem Sie aktiv weitere Äußerungen bezeichnen oder Features hinzufügen.

* [Bezeichnen von vorgeschlagenen Äußerungen mit LUIS](luis-how-to-review-endpoint-utterances.md)
* [Verwenden von Features zum Verbessern der Leistung Ihrer LUIS-App](luis-how-to-add-features.md)
