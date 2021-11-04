---
title: 'Schnellstart: Erstellen Ihrer App im LUIS-Portal'
description: In dieser Schnellstartanleitung lernen Sie, eine LUIS-App zu erstellen, die die vordefinierte Domäne `HomeAutomation` zum Ein- und Ausschalten von Beleuchtungen und Haushaltsgeräten verwendet. Diese vordefinierte Domäne bietet Absichten, Entitäten und Beispieläußerungen, die Sie nutzen können. Am Ende dieser Schnellstartanleitung verfügen Sie über einen LUIS-Endpunkt in der Cloud.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 10/20/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 70972607788568fbc06f9efe06fe083fd8d8ac01
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131073622"
---
# <a name="quickstart-build-your-app-in-luis-portal"></a>Schnellstart: Erstellen Ihrer App im LUIS-Portal

> [!NOTE]
> Eine neuere Version der Language Understanding-Funktionen ist jetzt als Teil von Azure Cognitive Services for Language verfügbar. Weitere Informationen finden Sie in der [Dokumentation zu Azure Cognitive Services for Language](../language-service/index.yml). Informationen zu Language Understanding-Funktionen innerhalb des Sprachdiensts finden Sie unter [Conversational Language Understanding](../language-service/conversational-language-understanding/overview.md), [Benutzerdefinierte Erkennung benannter Entitäten](../language-service/custom-named-entity-recognition/overview.md) und [Benutzerdefinierte Klassifizierung](../language-service/custom-classification/overview.md).

In dieser Schnellstartanleitung erstellen Sie mithilfe der vordefinierten Domäne „HomeAutomation“ zum Ein- und Ausschalten von Beleuchtungen und Haushaltsgeräten eine LUIS-App. Diese vordefinierte Domäne bietet Absichten, Entitäten und Beispieläußerungen, die Sie nutzen können. Versuchen Sie als Nächstes, Ihre App anzupassen, indem Sie weitere Absichten und Entitäten hinzufügen. Am Ende dieser Schnellstartanleitung verfügen Sie über einen LUIS-Endpunkt in der Cloud.

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

[!INCLUDE [Select authoring resource](./includes/select-authoring-resource.md)]

## <a name="create-a-new-app"></a>Erstellen einer neuen App

Sie können Ihre Anwendungen auf der Seite **Meine Apps** erstellen und verwalten.

### <a name="create-an-application"></a>Erstellen einer Anwendung

Klicken Sie zum Erstellen einer Anwendung auf **+ Neue App**. 

Geben Sie im angezeigten Fenster die folgenden Informationen ein:

|Name  |BESCHREIBUNG  |
|---------|---------|
|Name     | Ein Name für Ihre App. Beispiel: „Home Automation“.        |
|culture     | Die Sprache, die Ihre App versteht und spricht.   |
|Beschreibung | Eine Beschreibung für Ihre App.
|Vorhersageressource | Die Vorhersageressource, die Abfragen empfängt. |

Wählen Sie **Fertig** aus.

>[!NOTE]
>Die Kultur kann nach dem Erstellen der Anwendung nicht mehr geändert werden.

## <a name="add-prebuilt-domain"></a>Hinzufügen einer vordefinierten Domäne

LUIS bietet eine Reihe vordefinierter Domänen, die Ihnen die ersten Schritte mit Ihrer Anwendung erleichtern können. Eine App mit vordefinierter Domänen ist bereits mit [Absichten](./luis-concept-intent.md), [Entitäten](./luis-concept-entity-types.md) und [Äußerungen](./luis-concept-utterance.md) aufgefüllt.

1. Wählen Sie im linken Navigationsbereich die Option **Prebuilt domains** (Vordefinierte Domänen) aus.
2. Suchen Sie nach **HomeAutomation**.
3. Wählen Sie auf der Karte „HomeAutomation“ die Option **Domäne hinzufügen** aus.

    > [!div class="mx-imgBorder"]
    > ![Wählen Sie die Option „Vordefinierte Domänen“ aus, und suchen Sie nach „HomeAutomation“. Wählen Sie auf der Karte „HomeAutomation“ die Option „Domäne hinzufügen“ aus.](media/luis-quickstart-new-app/home-automation.png)

    Wenn die Domäne erfolgreich hinzugefügt wurde, enthält das Feld für die vordefinierte Domäne eine Schaltfläche **Remove domain** (Domäne entfernen).

## <a name="check-out-intents-and-entities"></a>Überprüfen von Absichten und Entitäten

1. Wählen Sie im linken Navigationsmenü die Option **Absichten** aus, um die Absichten der Domäne „HomeAutomation“ anzuzeigen. Hierin sind Beispieläußerungen enthalten, z. B. `HomeAutomation.QueryState` und `HomeAutomation.SetDevice`.

    > [!NOTE]
    > **None** (Keine) ist eine Absicht, die von allen LUIS-Apps bereitgestellt wird. Diese Absicht verwenden Sie zum Behandeln von Äußerungen, die keiner Funktionalität Ihrer App entsprechen.

2. Wählen Sie die Absicht **HomeAutomation.TurnOff** aus. Die Absicht enthält eine Liste von Beispieläußerungen, die mit Entitäten beschriftet sind.

    > [!div class="mx-imgBorder"]
    > [![Screenshot: Absicht „HomeAutomation.TurnOff“](media/luis-quickstart-new-app/home-automation-turnoff.png "Screenshot: Absicht „HomeAutomation.TurnOff“")](media/luis-quickstart-new-app/home-automation-turnoff.png)

3. Wählen Sie **Entitäten** aus, wenn Sie Entitäten für die App anzeigen möchten. Wenn Sie auf eine der Entitäten klicken, z. B. **HomeAutomation.DeviceName**, wird eine Liste mit den zugeordneten Werten angezeigt. 
 
    :::image type="content" source="media/luis-quickstart-new-app/entities-page.png" alt-text="Alternativer Bildtext" lightbox="media/luis-quickstart-new-app/entities-page.png":::

## <a name="train-the-luis-app"></a>Trainieren der LUIS-App
Nachdem Ihre Anwendung mit Absichten, Entitäten und Äußerungen aufgefüllt wurde, müssen Sie die Anwendung trainieren, damit die von Ihnen vorgenommenen Änderungen übernommen werden können.

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="test-your-app"></a>Testen Ihrer App

Nachdem Sie Ihre App trainiert haben, können Sie sie testen.

1. Wählen Sie im Navigationsbereich rechts oben die Option **Testen** aus.

1. Geben Sie eine Testäußerung in den interaktiven Testbereich ein, und drücken Sie die EINGABETASTE. Beispiel: *Turn off the lights*.

    In diesem Beispiel wurde *Turn off the lights* korrekt als Absicht mit der höchsten Bewertung von **HomeAutomation.TurnOff** identifiziert.

    :::image type="content" source="media/luis-quickstart-new-app/review-test-inspection-pane-in-portal.png" alt-text="Screenshot: Testbereich mit hervorgehobener Äußerung" lightbox="media/luis-quickstart-new-app/review-test-inspection-pane-in-portal.png":::

1. Wählen Sie **Überprüfen** aus, um weitere Informationen zur Vorhersage anzuzeigen.

    :::image type="content" source="media/luis-quickstart-new-app/test.png" alt-text="Screenshot: Testbereich mit Überprüfungsinformationen" lightbox="media/luis-quickstart-new-app/test.png":::

1. Schließen Sie den Testbereich.

## <a name="customize-your-application"></a>Anpassen Ihrer Anwendung

Neben den vordefinierten Domänen ermöglicht LUIS Ihnen das Erstellen eigener benutzerdefinierter Anwendungen oder das Anpassen vordefinierter Anwendungen.

### <a name="create-intents"></a>Erstellen von Absichten

So fügen Sie Ihrer App weitere Absichten hinzu

1. Wählen Sie im linken Navigationsbereich **Absichten** aus.
2. Klicken Sie auf **Erstellen**
3. Geben Sie den Namen der Absicht (`HomeAutomation.AddDeviceAlias`) ein, und wählen Sie dann „Fertig“ aus.

### <a name="create-entities"></a>Erstellen von Entitäten

So fügen Sie Ihrer App weitere Entitäten hinzu

1. Wählen Sie im linken Navigationsmenü **Entitäten** aus.
2. Klicken Sie auf **Erstellen**
3. Geben Sie den Namen der Absicht (`HomeAutomation.DeviceAlias`) ein, und wählen Sie unter **Typ** die Option „Durch maschinelles Lernen erworben“ und dann **Erstellen** aus.

### <a name="add-example-utterances"></a>Hinzufügen von Beispieläußerungen

Beispieläußerungen sind Text, den ein Benutzer in einen Chatbot oder eine andere Clientanwendung eingibt. Sie bilden die Absicht des Texts des Benutzers auf eine LUIS-Absicht ab.

Fügen Sie auf der Seite **Absichten** für `HomeAutomation.AddDeviceAlias` unter **Beispieläußerung** die folgenden Beispieläußerungen hinzu.

|#|Beispiele für Äußerungen|
|--|--|
|1|`Add alias to my fan to be wind machine`|
|2|`Alias lights to illumination`|
|3|`nickname living room speakers to our speakers a new fan`|
|4|`rename living room tv to main tv`|

[!INCLUDE [best-practice-utterances](./includes/best-practice-utterances.md)]


### <a name="label-example-utterances"></a>Bezeichnen von Beispieläußerungen

Das Bezeichnen Ihrer Äußerungen ist erforderlich, da Sie eine ML-Entität hinzugefügt haben. Anhand der Bezeichnung lernt Ihre Anwendung, wie die von Ihnen erstellten ML-Entitäten extrahiert werden.

[!INCLUDE [how-to-label](./includes/how-to-label.md)]

## <a name="create-prediction-resource"></a>Erstellen einer Vorhersageressource
An diesem Punkt ist die Erstellung Ihrer Anwendung abgeschlossen. Sie müssen eine Vorhersageressource erstellen, um Ihre Anwendung zu veröffentlichen und Vorhersagen in einem Chatbot oder einer anderen Clientanwendung über den Vorhersageendpunkt zu erhalten.

So erstellen Sie eine Vorhersageressource über das LUIS-Portal

[!INCLUDE [add-pred-resource-portal](./includes/add-prediction-resource-portal.md)]


## <a name="publish-the-app-to-get-the-endpoint-url"></a>Veröffentlichen der App zum Abrufen der Endpunkt-URL

[!INCLUDE [LUIS How to Publish steps](./includes/howto-publish.md)]



## <a name="query-the-v3-api-prediction-endpoint"></a>Abfragen des V3-API-Vorhersageendpunkts

[!INCLUDE [LUIS How to get endpoint first step](./includes/v3-prediction-endpoint.md)]

2. Stellen Sie in der Adressleiste des Browsers für die Abfragezeichenfolge sicher, dass die URL die folgenden Werte enthält. Fügen Sie diese Angaben hinzu, falls sie in der Abfragezeichenfolge nicht enthalten sind:

    * `verbose=true`
    * `show-all-intents=true`

3. Navigieren Sie in der Adressleiste des Browsers an das Ende der URL, und geben Sie *turn off the living room light* für die Abfragezeichenfolge ein. Drücken Sie anschließend die EINGABETASTE.

    ```json
    {
        "query": "turn off the living room light",
        "prediction": {
            "topIntent": "HomeAutomation.TurnOff",
            "intents": {
                "HomeAutomation.TurnOff": {
                    "score": 0.969448864
                },
                "HomeAutomation.QueryState": {
                    "score": 0.0122336326
                },
                "HomeAutomation.TurnUp": {
                    "score": 0.006547436
                },
                "HomeAutomation.TurnDown": {
                    "score": 0.0050634006
                },
                "HomeAutomation.SetDevice": {
                    "score": 0.004951761
                },
                "HomeAutomation.TurnOn": {
                    "score": 0.00312553928
                },
                "None": {
                    "score": 0.000552945654
                }
            },
            "entities": {
                "HomeAutomation.Location": [
                    "living room"
                ],
                "HomeAutomation.DeviceName": [
                    [
                        "living room light"
                    ]
                ],
                "HomeAutomation.DeviceType": [
                    [
                        "light"
                    ]
                ],
                "$instance": {
                    "HomeAutomation.Location": [
                        {
                            "type": "HomeAutomation.Location",
                            "text": "living room",
                            "startIndex": 13,
                            "length": 11,
                            "score": 0.902181149,
                            "modelTypeId": 1,
                            "modelType": "Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "HomeAutomation.DeviceName": [
                        {
                            "type": "HomeAutomation.DeviceName",
                            "text": "living room light",
                            "startIndex": 13,
                            "length": 17,
                            "modelTypeId": 5,
                            "modelType": "List Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "HomeAutomation.DeviceType": [
                        {
                            "type": "HomeAutomation.DeviceType",
                            "text": "light",
                            "startIndex": 25,
                            "length": 5,
                            "modelTypeId": 5,
                            "modelType": "List Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ]
                }
            }
        }
    }
    ```

Erfahren Sie mehr über den [V3-Vorhersageendpunkt](luis-migration-api-v3.md).


## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Nächste Schritte

* [Iteratives App-Design](./luis-concept-app-iteration.md)
* [bewährten Methoden](./luis-concept-best-practices.md)
