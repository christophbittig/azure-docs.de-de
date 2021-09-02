---
title: 'Tutorial: Azure Notebook: Personalisierung'
titleSuffix: Azure Cognitive Services
description: In diesem Tutorial wird ein Personalisierungsschleifensystem in einer Azure Notebook-Instanz simuliert, mit dem vorgeschlagen wird, welche Art von Kaffee ein Kunde bestellen soll. Die Benutzer und ihre Präferenzen werden in einem Benutzerdataset gespeichert. Informationen zum Kaffee sind ebenfalls verfügbar und werden in einem Kaffeedataset gespeichert.
author: jeffmend
ms.author: jeffme
ms.manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: tutorial
ms.date: 04/27/2020
ms.custom: devx-track-python
ms.openlocfilehash: 66d32a61a3c8f3b88562bdff0c541ec5d88e65d8
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122830975"
---
# <a name="tutorial-use-personalizer-in-azure-notebook"></a>Tutorial: Verwenden der Personalisierung in Azure Notebook

In diesem Tutorial wird in einer Azure Notebook-Instanz eine Personalisierungsschleife ausgeführt, um den damit verbundenen End-to-End-Lebenszyklus zu veranschaulichen.

In der Schleife wird vorgeschlagen, welche Art von Kaffee ein Kunde bestellen soll. Die Benutzer und ihre Präferenzen werden in einem Benutzerdataset gespeichert. Informationen zum Kaffee werden in einem Kaffeedataset gespeichert.

## <a name="users-and-coffee"></a>Benutzer und Kaffee

Das Notebook simuliert die Benutzerinteraktion mit einer Website und wählt aus dem Dataset nach dem Zufallsprinzip einen Benutzer, eine Tageszeit und einen Wettertyp aus. Eine Zusammenfassung der Benutzerinformationen lautet:

|Kunden: Kontextfeatures|Tageszeiten|Wetter|
|--|--|--|
|Alina<br>Bernd<br>Cathrin<br>David|Morgens<br>Nachmittags<br>Abends|Sonnig<br>Regnerisch<br>Schnee|

Damit die Personalisierung im Laufe der Zeit lernen kann, sind dem _System_ auch die Details zur Kaffeeauswahl für jede Person bekannt.

|Kaffee: Aktionsfeatures|Temperaturtypen|Ursprungsorte|Röstungsarten|Bio|
|--|--|--|--|--|
|Cappuccino|Heiß|Kenia|Dark|Bio|
|Cold Brew|Kalt|Brasilien|Leicht|Bio|
|Iced Mocha|Kalt|Äthiopien|Leicht|Kein Bio|
|Latte|Heiß|Brasilien|Dark|Kein Bio|

Der **Zweck** der Personalisierungsschleife besteht darin, so häufig wie möglich die beste Übereinstimmung zwischen den Benutzern und dem Kaffee zu finden.

Der Code für dieses Tutorial steht im [GitHub-Repository mit den Personalisierungsbeispielen](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/tree/master/samples/azurenotebook) zur Verfügung.

## <a name="how-the-simulation-works"></a>Funktionsweise der Simulation

Zu Beginn der Systemausführung sind die Vorschläge der Personalisierung nur in 20 bis 30 Prozent der Fälle erfolgreich. Dieser Erfolg wird durch die an die Relevanz-API der Personalisierung gesendete Relevanzbewertung 1 angegeben. Nach einigen Rangfolge- und Relevanzaufrufen verbessert sich das System.

Führen Sie nach der ersten Anforderung eine Offlineauswertung durch. Die Personalisierung kann so die Daten überprüfen und eine bessere Lernrichtlinie vorschlagen. Wenden Sie die neue Lernrichtlinie an, und führen Sie mit dem Notebook erneut 20 Prozent der vorherigen Anforderungsanzahl durch. Die Schleife führt mit der neuen Lernrichtlinie zu einer besseren Leistung.

## <a name="rank-and-reward-calls"></a>Einstufen nach Rangfolge und Belohnen von Aufrufen

Für jeden der Tausende von Aufrufen des Personalisierungsdiensts sendet Azure Notebook die Rangfolgenanforderung (**Rank**) an die REST-API:

* Eindeutige ID für das Rangfolgen-/Anforderungsereignis
* Kontextfeatures: Zufällige Auswahl des Benutzers, des Wetters und der Tageszeit – Simulation eines Benutzers auf einer Website oder einem mobilen Gerät
* Aktionen mit Features: _Alle_ Kaffeedaten, aus denen die Personalisierung einen Vorschlag erstellt

Das System empfängt die Anforderung und vergleicht diese Vorhersage dann mit der bereits bekannten Wahl des Benutzers für die gleiche Tageszeit und das gleiche Wetter. Falls die bekannte Wahl mit der vorhergesagten Auswahl übereinstimmt, wird der Belohnungswert „1“ (**Reward**) an die Personalisierung gesendet. Andernfalls lautet zurückgesendete Relevanzwert „0“.

> [!Note]
> Da es sich hierbei um eine Simulation handelt, ist der Algorithmus für die Belohnung nicht kompliziert. Bei einem echten Szenario sollte für den Algorithmus eine Geschäftslogik verwendet werden, wobei ggf. Gewichtungen für unterschiedliche Aspekte der Erfahrung des Kunden vergeben werden, um das Belohnungsergebnis zu ermitteln.


## <a name="prerequisites"></a>Voraussetzungen

* Ein [Azure Notebook](https://notebooks.azure.com/)-Konto
* Eine [Azure-Personalisierungsressource](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer)
    * Wenn Sie die Personalisierungsressource bereits verwendet haben, sollten Sie sicherstellen, dass Sie im Azure-Portal für die Ressource die [Daten löschen](how-to-settings.md#clear-data-for-your-learning-loop).
* Laden Sie alle Dateien für [dieses Beispiel](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/tree/master/samples/azurenotebook) in ein Azure Notebook-Projekt hoch.

Dateibeschreibungen:

* [Personalizer.ipynb](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/samples/azurenotebook/Personalizer.ipynb) ist das Jupyter-Notebook für dieses Tutorial.
* Das [Benutzerdataset](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/samples/azurenotebook/users.json) wird in einem JSON-Objekt gespeichert.
* Das [Kaffeedataset](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/samples/azurenotebook/coffee.json) wird in einem JSON-Objekt gespeichert.
* Das [Beispiel für den JSON-Code einer Anforderung](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/samples/azurenotebook/example-rankrequest.json) weist das erwartete Format für eine POST-Anforderung an die Rangfolge-API auf.

## <a name="configure-personalizer-resource"></a>Konfigurieren der Personalisierungsressource

Konfigurieren Sie im Azure-Portal Ihre [Personalisierungsressource](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer) so, dass die **Häufigkeit der Modellaktualisierung** auf 15 Sekunden und die **Wartezeit für Belohnung** ebenfalls auf 15 Sekunden festgelegt ist. Diese Werte finden Sie auf der Seite **[Konfiguration](how-to-settings.md#configure-service-settings-in-the-azure-portal)** .

|Einstellung|Wert|
|--|--|
|Häufigkeit der Modellaktualisierung|15 Sekunden|
|Wartezeit für Belohnung|15 Sekunden|

Diese Werte sind absichtlich niedrig gehalten, um für dieses Tutorial die Änderungen zu verdeutlichen. Sie sollten nicht in einem Produktionsszenario verwendet werden, ohne vorher zu prüfen, ob für Ihre Personalisierungsschleife damit das gewünschte Ziel erreicht wird.

## <a name="set-up-the-azure-notebook"></a>Einrichten der Azure Notebook-Instanz

1. Ändern Sie den Kernel in `Python 3.6`.
1. Öffnen Sie die Datei `Personalizer.ipynb` .

## <a name="run-notebook-cells"></a>Ausführen von Notebookzellen

Führen Sie die einzelnen ausführbaren Zellen aus, und warten Sie auf die Rückgabe. Der Vorgang ist abgeschlossen, wenn in den Klammern neben der Zelle anstelle von `*` eine Zahl angezeigt wird. In den folgenden Abschnitten wird beschrieben, welche programmgesteuerten Vorgänge in den einzelnen Zellen ablaufen und was als Ausgabe zu erwarten ist.

### <a name="include-the-python-modules"></a>Einfügen der Python-Module

Dient zum Einfügen der erforderlichen Python-Module. Die Zelle hat keine Ausgabe.

```python
import json
import matplotlib.pyplot as plt
import random
import requests
import time
import uuid
```

### <a name="set-personalizer-resource-key-and-name"></a>Festlegen des Schlüssels und Namens für die Personalisierungsressource

Suchen Sie im Azure-Portal auf der Seite **Schnellstart** Ihrer Personalisierungsressource nach Ihrem Schlüssel und Endpunkt. Ändern Sie den Wert von `<your-resource-name>` in den Namen Ihrer Personalisierungsressource. Ändern Sie den Wert von `<your-resource-key>` in Ihren Personalisierungsschlüssel.

```python
# Replace 'personalization_base_url' and 'resource_key' with your valid endpoint values.
personalization_base_url = "https://<your-resource-name>.cognitiveservices.azure.com/"
resource_key = "<your-resource-key>"
```

### <a name="print-current-date-and-time"></a>Drucken des aktuellen Datums und der aktuellen Uhrzeit
Verwenden Sie diese Funktion, um sich die Start- und Endzeiten der iterativen Funktion bzw. der Iterationen zu notieren.

Diese Zellen haben keine Ausgabe. Wenn diese Funktion aufgerufen wird, gibt sie das aktuelle Datum und die Uhrzeit aus.

```python
# Print out current datetime
def currentDateTime():
    currentDT = datetime.datetime.now()
    print (str(currentDT))
```

### <a name="get-the-last-model-update-time"></a>Abrufen des Zeitpunkts der letzten Modellaktualisierung

Wenn die Funktion `get_last_updated` aufgerufen wird, gibt die Funktion das Datum und die Uhrzeit der letzten Änderung zur Aktualisierung des Modells aus.

Diese Zellen haben keine Ausgabe. Wenn diese Funktion aufgerufen wird, gibt sie das Datum des letzten Modelltrainings aus.

Für die Funktion wird eine GET REST-API zum [Abrufen der Modelleigenschaften](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/GetModelProperties) verwendet.

```python
# ititialize variable for model's last modified date
modelLastModified = ""
```

```python
def get_last_updated(currentModifiedDate):

    print('-----checking model')

    # get model properties
    response = requests.get(personalization_model_properties_url, headers = headers, params = None)

    print(response)
    print(response.json())

    # get lastModifiedTime
    lastModifiedTime = json.dumps(response.json()["lastModifiedTime"])

    if (currentModifiedDate != lastModifiedTime):
        currentModifiedDate = lastModifiedTime
        print(f'-----model updated: {lastModifiedTime}')
```

### <a name="get-policy-and-service-configuration"></a>Abrufen der Richtlinien- und Dienstkonfiguration

Dient zum Überprüfen des Dienststatus mit diesen beiden REST-Aufrufen.

Diese Zellen haben keine Ausgabe. Wenn diese Funktion aufgerufen wird, gibt sie die Dienstwerte aus.

```python
def get_service_settings():

    print('-----checking service settings')

    # get learning policy
    response = requests.get(personalization_model_policy_url, headers = headers, params = None)

    print(response)
    print(response.json())

    # get service settings
    response = requests.get(personalization_service_configuration_url, headers = headers, params = None)

    print(response)
    print(response.json())
```

### <a name="construct-urls-and-read-json-data-files"></a>Erstellen von URLs und Lesen von JSON-Datendateien

Mit dieser Zelle wird Folgendes durchgeführt:

* Die in REST-Aufrufen verwendeten URLs werden erstellt.
* Der Sicherheitsheader wird festgelegt, indem der Schlüssel Ihrer Personalisierungsressource verwendet wird.
* Der zufällige Ausgangswert für die Rangfolgeereignis-ID wird festgelegt.
* Die JSON-Datendateien werden eingelesen.
* Die `get_last_updated`-Methode wird aufgerufen (Lernrichtlinie wurde in der Beispielausgabe entfernt).
* Die `get_service_settings`-Methode wird aufgerufen.

Die Zelle enthält die Ausgabe des Aufrufs der Funktionen `get_last_updated` und `get_service_settings`.

```python
# build URLs
personalization_rank_url = personalization_base_url + "personalizer/v1.0/rank"
personalization_reward_url = personalization_base_url + "personalizer/v1.0/events/" #add "{eventId}/reward"
personalization_model_properties_url = personalization_base_url + "personalizer/v1.0/model/properties"
personalization_model_policy_url = personalization_base_url + "personalizer/v1.0/configurations/policy"
personalization_service_configuration_url = personalization_base_url + "personalizer/v1.0/configurations/service"

headers = {'Ocp-Apim-Subscription-Key' : resource_key, 'Content-Type': 'application/json'}

# context
users = "users.json"

# action features
coffee = "coffee.json"

# empty JSON for Rank request
requestpath = "example-rankrequest.json"

# initialize random
random.seed(time.time())

userpref = None
rankactionsjsonobj = None
actionfeaturesobj = None

with open(users) as handle:
    userpref = json.loads(handle.read())

with open(coffee) as handle:
    actionfeaturesobj = json.loads(handle.read())

with open(requestpath) as handle:
    rankactionsjsonobj = json.loads(handle.read())

get_last_updated(modelLastModified)
get_service_settings()

print(f'User count {len(userpref)}')
print(f'Coffee count {len(actionfeaturesobj)}')
```

Überprüfen Sie, ob in der Ausgabe für `rewardWaitTime` und `modelExportFrequency` jeweils 15 Sekunden als Wert angegeben ist.

```console
-----checking model
<Response [200]>
{'creationTime': '0001-01-01T00:00:00+00:00', 'lastModifiedTime': '0001-01-01T00:00:00+00:00'}
-----model updated: "0001-01-01T00:00:00+00:00"
-----checking service settings
<Response [200]>
{...learning policy...}
<Response [200]>
{'rewardWaitTime': '00:00:15', 'defaultReward': 0.0, 'rewardAggregation': 'earliest', 'explorationPercentage': 0.2, 'modelExportFrequency': '00:00:15', 'logRetentionDays': -1}
User count 4
Coffee count 4
```

### <a name="troubleshooting-the-first-rest-call"></a>Problembehandlung für ersten REST-Aufruf

Diese vorherige Zelle ist die erste Zelle, die einen Aufruf der Personalisierung durchführt. Stellen Sie sicher, dass der REST-Statuscode in der Ausgabe `<Response [200]>` lautet. Wenn Sie einen Fehler erhalten (z. B. 404), obwohl Sie sicher sind, dass Ressourcenschlüssel und -name korrekt sind, sollten Sie das Notebook neu laden.

Stellen Sie sicher, dass für Kaffee und Benutzer jeweils der Wert „4“ angegeben ist. Falls Sie einen Fehler erhalten, sollten Sie überprüfen, ob Sie alle drei JSON-Dateien hochgeladen haben.

### <a name="set-up-metric-chart-in-azure-portal"></a>Einrichten eines Metrikdiagramms im Azure-Portal

Später in diesem Tutorial können Sie den Langzeitprozess mit 10.000 Anforderungen im Browser in einem Textfeld verfolgen, das aktualisiert wird. Unter Umständen ist dies in einem Diagramm oder als Gesamtsumme einfacher zu sehen, nachdem der Langzeitprozess abgeschlossen ist. Verwenden Sie die Metriken der Ressource, um diese Informationen anzuzeigen. Sie können das Diagramm jetzt erstellen, da Sie eine Anforderung an den Dienst gesendet haben. Aktualisieren Sie das Diagramm dann regelmäßig, während der Langzeitprozess aktiv ist.

1. Wählen Sie im Azure-Portal Ihre Personalisierungsressource aus.
1. Wählen Sie in der Ressourcennavigation unter „Überwachung“ die Option **Metriken** aus.
1. Wählen Sie im Diagramm die Option **Metrik hinzufügen** aus.
1. Der Ressourcen- und Metriknamespace sind bereits festgelegt. Sie müssen die Metrik für **erfolgreiche Aufrufe** und die Aggregation für die **Summe** auswählen.
1. Ändern Sie die Einstellung des Zeitfilters auf die letzten vier Stunden.

    ![Richten Sie das Metrikdiagramm im Azure-Portal ein, indem Sie die Metrik für erfolgreiche Aufrufe für die letzten vier Stunden hinzufügen.](./media/tutorial-azure-notebook/metric-chart-setting.png)

    Im Diagramm sollten drei erfolgreiche Aufrufe angezeigt werden.

### <a name="generate-a-unique-event-id"></a>Generieren einer eindeutigen Ereignis-ID

Diese Funktion generiert eine eindeutige ID für jeden Rangfolgeaufruf. Die ID wird verwendet, um die Informationen zum Rangfolge- und Belohnungsaufruf zu ermitteln. Dieser Wert kann aus einem Geschäftsprozess stammen, z. B. einer Webansicht-ID oder Transaktions-ID.

Die Zelle hat keine Ausgabe. Wenn diese Funktion aufgerufen wird, wird die eindeutige ID ausgegeben.

```python
def add_event_id(rankjsonobj):
    eventid = uuid.uuid4().hex
    rankjsonobj["eventId"] = eventid
    return eventid
```

### <a name="get-random-user-weather-and-time-of-day"></a>Abrufen von Benutzer, Wetter und Tageszeit nach dem Zufallsprinzip

Mit dieser Funktion werden ein eindeutiger Benutzer, das Wetter und die Tageszeit ausgewählt, und anschließend werden diese Elemente dem JSON-Objekt hinzugefügt, das an die Rangfolgeanforderung gesendet wird.

Die Zelle hat keine Ausgabe. Wenn die Funktion aufgerufen wird, gibt sie den Benutzernamen, das Wetter und die Tageszeit nach dem Zufallsprinzip zurück.

Liste mit vier Benutzern und ihren Präferenzen (aus Platzgründen sind nur einige Präferenzen aufgeführt):

```json
{
  "Alice": {
    "Sunny": {
      "Morning": "Cold brew",
      "Afternoon": "Iced mocha",
      "Evening": "Cold brew"
    }...
  },
  "Bob": {
    "Sunny": {
      "Morning": "Cappucino",
      "Afternoon": "Iced mocha",
      "Evening": "Cold brew"
    }...
  },
  "Cathy": {
    "Sunny": {
      "Morning": "Latte",
      "Afternoon": "Cold brew",
      "Evening": "Cappucino"
    }...
  },
  "Dave": {
    "Sunny": {
      "Morning": "Iced mocha",
      "Afternoon": "Iced mocha",
      "Evening": "Iced mocha"
    }...
  }
}
```

```python
def add_random_user_and_contextfeatures(namesoption, weatheropt, timeofdayopt, rankjsonobj):
    name = namesoption[random.randint(0,3)]
    weather = weatheropt[random.randint(0,2)]
    timeofday = timeofdayopt[random.randint(0,2)]
    rankjsonobj['contextFeatures'] = [{'timeofday': timeofday, 'weather': weather, 'name': name}]
    return [name, weather, timeofday]
```


### <a name="add-all-coffee-data"></a>Hinzufügen aller Kaffeedaten

Mit dieser Funktion wird die gesamte Kaffeeliste dem JSON-Objekt hinzugefügt, das an die Rangfolgeanforderung gesendet wird.

Die Zelle hat keine Ausgabe. Wenn diese Funktion aufgerufen wird, wird keine Änderung des `rankjsonobj`-Elements durchgeführt.


Das Beispiel für die Features einer einzelnen Kaffeesorte lautet:

```json
{
    "id": "Cappucino",
    "features": [
    {
        "type": "hot",
        "origin": "kenya",
        "organic": "yes",
        "roast": "dark"

    }
}
```

```python
def add_action_features(rankjsonobj):
    rankjsonobj["actions"] = actionfeaturesobj
```

### <a name="compare-prediction-with-known-user-preference"></a>Vergleichen der Vorhersage mit der bekannten Benutzerpräferenz

Diese Funktion wird für jede Iteration nach dem Aufruf der Rangfolge-API aufgerufen.

Mit dieser Funktion wird die Benutzerpräferenz in Bezug auf den Kaffee nach dem Wetter und der Tageszeit mit dem Personalisierungsvorschlag für den Benutzer basierend auf den Filtern verglichen. Wenn sich eine Übereinstimmung mit dem Vorschlag ergibt, wird „1“ als Bewertungspunktzahl zurückgegeben, andernfalls „0“. Die Zelle hat keine Ausgabe. Wenn diese Funktion aufgerufen wird, wird die Bewertungspunktzahl ausgegeben.

```python
def get_reward_from_simulated_data(name, weather, timeofday, prediction):
    if(userpref[name][weather][timeofday] == str(prediction)):
        return 1
    return 0
```

### <a name="loop-through-calls-to-rank-and-reward"></a>Schleife für Rangfolge- und Belohnungsaufrufe

Die nächste Zelle umfasst die _Hauptarbeit_ des Notebooks: Abrufen eines zufälligen Benutzers, Abrufen der Kaffeeliste und Senden beider Angaben an die Rangfolge-API. Die Vorhersage wird mit den bekannten Präferenzen des Benutzers verglichen, und anschließend wird der Belohnungswert zurück an den Personalisierungsdienst gesendet.

Die Schleife wird so häufig ausgeführt, wie dies unter `num_requests` angegeben ist. Die Personalisierung benötigt einige Tausend Rangfolge- und Belohnungsaufrufe, um ein Modell erstellen zu können.

Unten ist ein Beispiel für den JSON-Code angegeben, der an die Rangfolge-API gesendet wird. Aus Platzgründen ist nicht die gesamte Kaffeeliste angegeben. Den vollständigen JSON-Code für Kaffee finden Sie in `coffee.json`.

An die Rangfolge-API gesendeter JSON-Code:

```json
{
   'contextFeatures':[
      {
         'timeofday':'Evening',
         'weather':'Snowy',
         'name':'Alice'
      }
   ],
   'actions':[
      {
         'id':'Cappucino',
         'features':[
            {
               'type':'hot',
               'origin':'kenya',
               'organic':'yes',
               'roast':'dark'
            }
         ]
      }
        ...rest of coffee list
   ],
   'excludedActions':[

   ],
   'eventId':'b5c4ef3e8c434f358382b04be8963f62',
   'deferActivation':False
}
```

JSON-Codeantwort der Rangfolge-API:

```json
{
    'ranking': [
        {'id': 'Latte', 'probability': 0.85 },
        {'id': 'Iced mocha', 'probability': 0.05 },
        {'id': 'Cappucino', 'probability': 0.05 },
        {'id': 'Cold brew', 'probability': 0.05 }
    ],
    'eventId': '5001bcfe3bb542a1a238e6d18d57f2d2',
    'rewardActionId': 'Latte'
}
```

Abschließend wird für jede Schleife eine zufällige Auswahl für Benutzer, Wetter, Tageszeit und ermittelter Belohnungswert angezeigt. Mit dem Belohnungswert „1“ wird angegeben, dass die Personalisierungsressource für den Benutzer, das Wetter und die Tageszeit die richtige Kaffeesorte ausgewählt hat.

```console
1 Alice Rainy Morning Latte 1
```

In der Funktion wird Folgendes verwendet:

* Rangfolge: POST-REST-API zum [Abrufen der Rangfolge](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank).
* Belohnung: POST-REST-API zum [Melden der Belohnung](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward).

```python
def iterations(n, modelCheck, jsonFormat):

    i = 1

    # default reward value - assumes failed prediction
    reward = 0

    # Print out dateTime
    currentDateTime()

    # collect results to aggregate in graph
    total = 0
    rewards = []
    count = []

    # default list of user, weather, time of day
    namesopt = ['Alice', 'Bob', 'Cathy', 'Dave']
    weatheropt = ['Sunny', 'Rainy', 'Snowy']
    timeofdayopt = ['Morning', 'Afternoon', 'Evening']


    while(i <= n):

        # create unique id to associate with an event
        eventid = add_event_id(jsonFormat)

        # generate a random sample
        [name, weather, timeofday] = add_random_user_and_contextfeatures(namesopt, weatheropt, timeofdayopt, jsonFormat)

        # add action features to rank
        add_action_features(jsonFormat)

        # show JSON to send to Rank
        print('To: ', jsonFormat)

        # choose an action - get prediction from Personalizer
        response = requests.post(personalization_rank_url, headers = headers, params = None, json = jsonFormat)

        # show Rank prediction
        print ('From: ',response.json())

        # compare personalization service recommendation with the simulated data to generate a reward value
        prediction = json.dumps(response.json()["rewardActionId"]).replace('"','')
        reward = get_reward_from_simulated_data(name, weather, timeofday, prediction)

        # show result for iteration
        print(f'   {i} {currentDateTime()} {name} {weather} {timeofday} {prediction} {reward}')

        # send the reward to the service
        response = requests.post(personalization_reward_url + eventid + "/reward", headers = headers, params= None, json = { "value" : reward })

        # for every N rank requests, compute total correct  total
         total =  total + reward

        # every N iteration, get last updated model date and time
        if(i % modelCheck == 0):

            print("**** 10% of loop found")

            get_last_updated(modelLastModified)

        # aggregate so chart is easier to read
        if(i % 10 == 0):
            rewards.append( total)
            count.append(i)
             total = 0

        i = i + 1

    # Print out dateTime
    currentDateTime()

    return [count, rewards]
```

## <a name="run-for-10000-iterations"></a>Ausführen von 10.000 Iterationen
Führen Sie für die Personalisierungsschleife 10.000 Iterationen aus. Dies ist ein zeitintensives Ereignis. Schließen Sie nicht den Browser, in dem das Notebook ausgeführt wird. Aktualisieren Sie das Metrikdiagramm im Azure-Portal in regelmäßigen Abständen, um die Gesamtzahl der Dienstaufrufe anzuzeigen. Wenn ungefähr 20.000 Aufrufe und ein Rangfolge- und Belohnungsaufruf für jede Iteration der Schleife durchgeführt wurden, sind die Iterationen abgeschlossen.

```python
# max iterations
num_requests = 200

# check last mod date N% of time - currently 10%
lastModCheck = int(num_requests * .10)

jsonTemplate = rankactionsjsonobj

# main iterations
[count, rewards] = iterations(num_requests, lastModCheck, jsonTemplate)
```



## <a name="chart-results-to-see-improvement"></a>Anzeigen der Ergebnisse in einem Diagramm zur Verdeutlichung der Verbesserung

Erstellen Sie aus `count` und `rewards` ein Diagramm.

```python
def createChart(x, y):
    plt.plot(x, y)
    plt.xlabel("Batch of rank events")
    plt.ylabel("Correct recommendations per batch")
    plt.show()
```

## <a name="run-chart-for-10000-rank-requests"></a>Ausführen der Diagrammerstellung für 10.000 Rangfolgeanforderungen

Führen Sie die Funktion `createChart` aus.

```python
createChart(count,rewards)
```

## <a name="reading-the-chart"></a>Lesen des Diagramms

In diesem Diagramm ist der Erfolg des Modells für die aktuelle Standardlernrichtlinie dargestellt.

![In diesem Diagramm ist der Erfolg der aktuellen Lernrichtlinie für die Dauer des Tests dargestellt.](./media/tutorial-azure-notebook/azure-notebook-chart-results.png)


Das ideale Ziel besteht darin, dass sich für die Schleife nach Abschluss des Tests eine Erfolgsrate ergibt, die nahe bei 100 Prozent liegt (abzüglich der Durchsuchung). Der Standardwert für die Durchsuchung beträgt 20 %.

`100-20=80`

Sie finden diesen Durchsuchungswert im Azure-Portal auf der Seite **Konfiguration** für die Personalisierungsressource.

Sie können eine bessere Lernrichtlinie ermitteln, indem Sie basierend auf Ihren Daten für die Rangfolge-API im Portal für Ihre Personalisierungsschleife eine [Offlinebewertung](how-to-offline-evaluation.md) durchführen.

## <a name="run-an-offline-evaluation"></a>Durchführen einer Offlinebewertung

1. Öffnen Sie im Azure-Portal die Seite **Bewertungen** der Personalisierungsressource.
1. Wählen Sie die Option **Bewertung erstellen** aus.
1. Geben Sie die erforderlichen Daten für den Bewertungsnamen und den Datumsbereich für die Schleifenbewertung ein. Der Datumsbereich sollte nur die Tage enthalten, die für Ihre Bewertung relevant sind.
    ![Öffnen Sie im Azure-Portal die Seite „Bewertungen“ der Personalisierungsressource. Wählen Sie „Bewertung erstellen“ aus. Geben Sie den Namen und Datumsbereich für die Bewertung ein.](./media/tutorial-azure-notebook/create-offline-evaluation.png)

    Mit dieser Offlinebewertung soll ermittelt werden, ob für die in dieser Schleife verwendeten Features und Aktionen eine bessere Lernrichtlinie vorhanden ist. Stellen Sie sicher, dass die **Ermittlung zur Optimierung** aktiviert ist, um diese bessere Lernrichtlinie zu ermitteln.

1. Wählen Sie **OK** aus, um die Bewertung zu starten.
1. Auf der Seite **Bewertungen** werden die neue Bewertung und der aktuelle Status angezeigt. Die Dauer dieser Bewertung hängt davon ab, wie viele Daten Sie nutzen. Nach einigen Minuten können Sie zu dieser Seite zurückkehren, um die Ergebnisse zu prüfen.
1. Wählen Sie nach Abschluss der Bewertung die Option **Vergleich verschiedener Lernrichtlinien** aus. Es werden die verfügbaren Lernrichtlinien und das jeweilige Verhalten für die Daten angezeigt.
1. Wählen Sie in der Tabelle die oberste Lernrichtlinie und dann die Option **Anwenden** aus. Die _beste_ Lernrichtlinie wird auf Ihr Modell angewendet, und es wird ein neuer Trainingslauf durchgeführt.

## <a name="change-update-model-frequency-to-5-minutes"></a>Ändern der Häufigkeit der Modellaktualisierung in „5 Minuten“

1. Wählen Sie im Azure-Portal für die Personalisierungsressource die Seite **Konfiguration** aus.
1. Ändern Sie die **Häufigkeit der Modellaktualisierung** und die **Wartezeit für Belohnung** jeweils in „5 Minuten“, und wählen Sie anschließend **Speichern** aus.

Informieren Sie sich weiter über die [Wartezeit für Belohnung](concept-rewards.md#reward-wait-time) und die [Häufigkeit der Modellaktualisierung](how-to-settings.md#model-update-frequency).

```python
#Verify new learning policy and times
get_service_settings()
```

Überprüfen Sie, ob in der Ausgabe für `rewardWaitTime` und `modelExportFrequency` jeweils „5 Minuten“ als Wert angegeben ist.
```console
-----checking model
<Response [200]>
{'creationTime': '0001-01-01T00:00:00+00:00', 'lastModifiedTime': '0001-01-01T00:00:00+00:00'}
-----model updated: "0001-01-01T00:00:00+00:00"
-----checking service settings
<Response [200]>
{...learning policy...}
<Response [200]>
{'rewardWaitTime': '00:05:00', 'defaultReward': 0.0, 'rewardAggregation': 'earliest', 'explorationPercentage': 0.2, 'modelExportFrequency': '00:05:00', 'logRetentionDays': -1}
User count 4
Coffee count 4
```

## <a name="validate-new-learning-policy"></a>Überprüfen der neuen Lernrichtlinie

Wechseln Sie zur Azure Notebooks-Datei zurück, und führen Sie die gleiche Schleife aus, aber nur für 2.000 Iterationen. Aktualisieren Sie das Metrikdiagramm im Azure-Portal in regelmäßigen Abständen, um die Gesamtzahl der Dienstaufrufe anzuzeigen. Wenn ungefähr 4.000 Aufrufe und ein Rangfolge- und Belohnungsaufruf für jede Iteration der Schleife durchgeführt wurden, sind die Iterationen abgeschlossen.

```python
# max iterations
num_requests = 2000

# check last mod date N% of time - currently 10%
lastModCheck2 = int(num_requests * .10)

jsonTemplate2 = rankactionsjsonobj

# main iterations
[count2, rewards2] = iterations(num_requests, lastModCheck2, jsonTemplate)
```

## <a name="run-chart-for-2000-rank-requests"></a>Ausführen der Diagrammerstellung für 2.000 Rangfolgeanforderungen

Führen Sie die Funktion `createChart` aus.

```python
createChart(count2,rewards2)
```

## <a name="review-the-second-chart"></a>Überprüfen des zweiten Diagramms

Im zweiten Diagramm sollte ein merklicher Anstieg bei den Rangfolgevorhersagen, für die sich Übereinstimmungen mit den Benutzerpräferenzen ergeben, zu beobachten sein.

![Im zweiten Diagramm sollte ein merklicher Anstieg bei den Rangfolgevorhersagen, für die sich Übereinstimmungen mit den Benutzerpräferenzen ergeben, zu beobachten sein.](./media/tutorial-azure-notebook/azure-notebook-chart-results-happy-graph.png)

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Falls Sie die Tutorialreihe nicht weiter durcharbeiten möchten, sollten Sie die folgenden Ressourcen bereinigen:

* Löschen Sie Ihr Azure Notebook-Projekt.
* Löschen Sie Ihre Personalisierungsressource.

## <a name="next-steps"></a>Nächste Schritte

Sie finden die in diesem Beispiel verwendeten [Jupyter-Notebook- und Datendateien](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/tree/master/samples/azurenotebook) im GitHub-Repository für die Personalisierung.

