---
author: eric-urban
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: eur
ms.custom: devx-track-js
ms.openlocfilehash: b4510d750461c1fd693996c464d3dfe28e82fb27
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131507530"
---
## <a name="start-with-some-boilerplate-code"></a>Beginnen mit Codebausteinen

Fügen Sie Code hinzu, der als Gerüst für das Projekt fungiert.

```html
    <!DOCTYPE html>
    <html>
    <head>
    <title>Microsoft Cognitive Services Speech SDK JavaScript Quickstart</title>
    <meta charset="utf-8" />
    </head>
    <body style="font-family:'Helvetica Neue',Helvetica,Arial,sans-serif; font-size:13px;">
    </body>
    </html>
```
## <a name="add-ui-elements"></a>Hinzufügen von Benutzeroberflächenelementen

Sie fügen nun einige grundlegende Benutzeroberflächenelemente für Eingabefelder hinzu, verweisen auf den JavaScript-Code des Speech SDK, und rufen ein Autorisierungstoken ab, sofern verfügbar.

```html
<body style="font-family:'Helvetica Neue',Helvetica,Arial,sans-serif; font-size:13px;">
  <div id="content" style="display:none">
    <table width="100%">
      <tr>
        <td></td>
        <td><h1 style="font-weight:500;">Microsoft Cognitive Services Speech SDK JavaScript Quickstart</h1></td>
      </tr>
      <tr>
        <td align="right"><a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started" target="_blank">Subscription</a>:</td>
        <td><input id="subscriptionKey" type="text" size="40" value="subscription"></td>
      </tr>
      <tr>
        <td align="right">Region</td>
        <td><input id="serviceRegion" type="text" size="40" value="YourServiceRegion"></td>
      </tr>
      <tr>
        <td align="right">Application ID:</td>
        <td><input id="appId" type="text" size="60" value="YOUR_LANGUAGE_UNDERSTANDING_APP_ID"></td>
      </tr>
      <tr>
        <td></td>
        <td><button id="startIntentRecognizeAsyncButton">Start Intent Recognition</button></td>
      </tr>
      <tr>
        <td align="right" valign="top">Input Text</td>
        <td><textarea id="phraseDiv" style="display: inline-block;width:500px;height:200px"></textarea></td>
      </tr>
      <tr>
        <td align="right" valign="top">Result</td>
        <td><textarea id="statusDiv" style="display: inline-block;width:500px;height:100px"></textarea></td>
      </tr>
    </table>
  </div>

  <script src="microsoft.cognitiveservices.speech.sdk.bundle.js"></script>

  <script>
  // Note: Replace the URL with a valid endpoint to retrieve
  //       authorization tokens for your subscription.
  var authorizationEndpoint = "token.php";

  function RequestAuthorizationToken() {
    if (authorizationEndpoint) {
      var a = new XMLHttpRequest();
      a.open("GET", authorizationEndpoint);
      a.setRequestHeader("Content-Type", "application/x-www-form-urlencoded");
      a.send("");
      a.onload = function() {
                var token = JSON.parse(atob(this.responseText.split(".")[1]));
                serviceRegion.value = token.region;
                authorizationToken = this.responseText;
                subscriptionKey.disabled = true;
                subscriptionKey.value = "using authorization token (hit F5 to refresh)";
                console.log("Got an authorization token: " + token);
      }
    }
  }
  </script>

  <script>
    // status fields and start button in UI
    var phraseDiv;
    var statusDiv;
    var startIntentRecognizeAsyncButton;

    // subscription key, region, and appId for LUIS services.
    var subscriptionKey, serviceRegion, appId;
    var authorizationToken;
    var SpeechSDK;
    var recognizer;

    document.addEventListener("DOMContentLoaded", function () {
      startIntentRecognizeAsyncButton = document.getElementById("startIntentRecognizeAsyncButton");
      subscriptionKey = document.getElementById("subscriptionKey");
      serviceRegion = document.getElementById("serviceRegion");
      appId = document.getElementById("appId");
      phraseDiv = document.getElementById("phraseDiv");
      statusDiv = document.getElementById("statusDiv");

      startIntentRecognizeAsyncButton.addEventListener("click", function () {
        startIntentRecognizeAsyncButton.disabled = true;
        phraseDiv.innerHTML = "";
        statusDiv.innerHTML = "";
      });

      if (!!window.SpeechSDK) {
        SpeechSDK = window.SpeechSDK;
        startIntentRecognizeAsyncButton.disabled = false;

        document.getElementById('content').style.display = 'block';
        document.getElementById('warning').style.display = 'none';

        // in case we have a function for getting an authorization token, call it.
        if (typeof RequestAuthorizationToken === "function") {
          RequestAuthorizationToken();
        }
      }
    });
  </script>
```

## <a name="create-a-speech-configuration"></a>Erstellen einer Speech-Konfiguration

Bevor Sie ein `SpeechRecognizer`-Objekt initialisieren können, müssen Sie eine Konfiguration erstellen, die den Abonnementschlüssel und die Abonnementregion verwendet. Fügen Sie diesen Code in die Methode `startRecognizeOnceAsyncButton.addEventListener()` ein.

> [!NOTE]
> Das Speech SDK verwendet für die Erkennung standardmäßig amerikanisches Englisch (en-us). Informationen zum Auswählen der Ausgangssprache finden Sie unter [Angeben der Ausgangssprache für die Spracherkennung](../../../../how-to-specify-source-language.md).


```JavaScript
        // if we got an authorization token, use the token. Otherwise use the provided subscription key
        var speechConfig;
        if (authorizationToken) {
          speechConfig = SpeechSDK.SpeechConfig.fromAuthorizationToken(authorizationToken, serviceRegion.value);
        } else {
          if (subscriptionKey.value === "" || subscriptionKey.value === "subscription") {
            alert("Please enter your Microsoft Cognitive Services Speech subscription key!");
            return;
          }
          startIntentRecognizeAsyncButton.disabled = false;
          speechConfig = SpeechSDK.SpeechConfig.fromSubscription(subscriptionKey.value, serviceRegion.value);
        }

        speechConfig.speechRecognitionLanguage = "en-US";
```

## <a name="create-an-audio-configuration"></a>Erstellen einer Audiokonfiguration

Nun müssen Sie ein `AudioConfig`-Objekt erstellen, das auf Ihr Eingabegerät verweist. Fügen Sie diesen Code in die Methode `startIntentRecognizeAsyncButton.addEventListener()` direkt unterhalb der Speech-Konfiguration ein.

```JavaScript
        var audioConfig = SpeechSDK.AudioConfig.fromDefaultMicrophoneInput();
```

## <a name="initialize-a-intentrecognizer"></a>Initialisieren eines IntentRecognizer-Objekts

Erstellen Sie nun das Objekt `IntentRecognizer` mithilfe der zuvor erstellten Objekte `SpeechConfig` und `AudioConfig`. Fügen Sie diesen Code in die Methode `startIntentRecognizeAsyncButton.addEventListener()` ein.

```JavaScript
        recognizer = new SpeechSDK.IntentRecognizer(speechConfig, audioConfig);
```

## <a name="add-a-languageunderstandingmodel-and-intents"></a>Hinzufügen von LanguageUnderstandingModel und Absichten

Sie müssen der Absichtserkennung ein `LanguageUnderstandingModel` zuordnen und die zu erkennenden Absichten hinzufügen. Wir verwenden Absichten aus der vordefinierten Domäne für die Gebäudeautomatisierung.

Fügen Sie diesen Code unterhalb von `IntentRecognizer` ein. Stellen Sie sicher, dass Sie `"YourLanguageUnderstandingAppId"` durch die ID Ihrer LUIS-App ersetzen.

```JavaScript
        if (appId.value !== "" && appId.value !== "YOUR_LANGUAGE_UNDERSTANDING_APP_ID") {
          var lm = SpeechSDK.LanguageUnderstandingModel.fromAppId(appId.value);

          recognizer.addAllIntents(lm);
        }
```

> [!NOTE]
> Vom Speech SDK werden nur LUIS-v2.0-Endpunkte unterstützt.
> Die v3.0-Endpunkt-URL im Beispielabfragefeld muss manuell geändert werden, um ein v2.0-URL-Muster zu verwenden.
> Von LUIS-v2.0-Endpunkten wird immer eines der beiden folgenden Muster verwendet:
> * `https://{AzureResourceName}.cognitiveservices.azure.com/luis/v2.0/apps/{app-id}?subscription-key={subkey}&verbose=true&q=`
> * `https://{Region}.api.cognitive.microsoft.com/luis/v2.0/apps/{app-id}?subscription-key={subkey}&verbose=true&q=`

## <a name="recognize-an-intent"></a>Erkennen einer Absicht

Rufen Sie die Methode `recognizeOnceAsync()` über das Objekt `IntentRecognizer` auf. Diese Methode teilt dem Spracherkennungsdienst mit, dass Sie einen einzelnen Ausdruck zur Erkennung senden, und dass die Spracherkennung beendet werden soll, sobald der Ausdruck ermittelt wurde.

Fügen Sie den folgenden Code unterhalb der Modellergänzung ein:

```JavaScript
        recognizer.recognizeOnceAsync(
          function (result) {
            window.console.log(result);

            phraseDiv.innerHTML = result.text + "\r\n";

            statusDiv.innerHTML += "(continuation) Reason: " + SpeechSDK.ResultReason[result.reason];
            switch (result.reason) {
              case SpeechSDK.ResultReason.RecognizedSpeech:
                statusDiv.innerHTML += " Text: " + result.text;
                break;
              case SpeechSDK.ResultReason.RecognizedIntent:
                statusDiv.innerHTML += " Text: " + result.text + " IntentId: " + result.intentId;

                // The actual JSON returned from Language Understanding is a bit more complex to get to, but it is available for things like
                // the entity name and type if part of the intent.
                statusDiv.innerHTML += " Intent JSON: " + result.properties.getProperty(SpeechSDK.PropertyId.LanguageUnderstandingServiceResponse_JsonResult);
                phraseDiv.innerHTML += result.properties.getProperty(SpeechSDK.PropertyId.LanguageUnderstandingServiceResponse_JsonResult) + "\r\n";
                break;
              case SpeechSDK.ResultReason.NoMatch:
                var noMatchDetail = SpeechSDK.NoMatchDetails.fromResult(result);
                statusDiv.innerHTML += " NoMatchReason: " + SpeechSDK.NoMatchReason[noMatchDetail.reason];
                break;
              case SpeechSDK.ResultReason.Canceled:
                var cancelDetails = SpeechSDK.CancellationDetails.fromResult(result);
                statusDiv.innerHTML += " CancellationReason: " + SpeechSDK.CancellationReason[cancelDetails.reason];

              if (cancelDetails.reason === SpeechSDK.CancellationReason.Error) {
                statusDiv.innerHTML += ": " + cancelDetails.errorDetails;
              }
            break;
            }
            statusDiv.innerHTML += "\r\n";
            startIntentRecognizeAsyncButton.disabled = false;
          },
          function (err) {
            window.console.log(err);

            phraseDiv.innerHTML += "ERROR: " + err;
            startIntentRecognizeAsyncButton.disabled = false;
          });
```

## <a name="check-your-code"></a>Überprüfen des Codes

 [!code-html [SampleCode](~/samples-cognitive-services-speech-sdk/quickstart/javascript/browser/intent-recognition/index.html)]

## <a name="create-the-token-source-optional"></a>Erstellen der Tokenquelle (optional)

Wenn Sie die Webseite auf einem Webserver hosten möchten, können Sie optional eine Tokenquelle für die Demoanwendung bereitstellen.
Auf diese Weise verlässt Ihr Abonnementschlüssel nie den Server, während Benutzer gleichzeitig die Sprachfunktionen verwenden können, ohne selbst Autorisierungscode eingeben zu müssen.

Erstellen Sie eine neue Datei mit dem Namen `token.php`. In diesem Beispiel wird davon ausgegangen, dass der Webserver die PHP-Skriptsprache mit aktiviertem cURL unterstützt. Geben Sie den folgenden Code ein:

```php
<?php
header('Access-Control-Allow-Origin: ' . $_SERVER['SERVER_NAME']);

// Replace with your own subscription key and service region (e.g., "westus").
$subscriptionKey = 'YourSubscriptionKey';
$region = 'YourServiceRegion';

$ch = curl_init();
curl_setopt($ch, CURLOPT_URL, 'https://' . $region . '.api.cognitive.microsoft.com/sts/v1.0/issueToken');
curl_setopt($ch, CURLOPT_POST, 1);
curl_setopt($ch, CURLOPT_POSTFIELDS, '{}');
curl_setopt($ch, CURLOPT_HTTPHEADER, array('Content-Type: application/json', 'Ocp-Apim-Subscription-Key: ' . $subscriptionKey));
curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);
echo curl_exec($ch);
?>
```

> [!NOTE]
> Autorisierungstoken haben nur eine begrenzte Lebensdauer.
> Dieses vereinfachte Beispiel zeigt nicht, wie Autorisierungstoken automatisch aktualisiert werden. Als Benutzer können Sie die Seite manuell laden oder F5 drücken, um sie zu aktualisieren.

## <a name="build-and-run-the-sample-locally"></a>Erstellen und lokales Ausführen des Beispiels

Um die App zu starten, doppelklicken Sie auf die Datei „index.html“, oder öffnen Sie die Datei „index.html“ mit Ihren bevorzugten Browser. Eine einfache GUI wird angezeigt, über die Sie Ihren LUIS-Schlüssel, die [LUIS-Region](../../../../regions.md) und die LUIS-Anwendungs-ID eingeben können. Nachdem Sie diese Felder eingegeben haben, können Sie auf die entsprechende Schaltfläche klicken, um eine Erkennung mithilfe des Mikrofons auszulösen.

> [!NOTE]
> Diese Methode kann für den Safari-Browser nicht verwendet werden.
> Für Safari muss die Beispielwebseite auf einem Webserver gehostet werden. In Safari können Websites, die aus einer lokalen Datei geladen werden, das Mikrofon nicht verwenden.

## <a name="build-and-run-the-sample-via-a-web-server"></a>Erstellen und Ausführen des Beispiels über einen Webserver

Öffnen Sie zum Starten Ihrer App Ihren bevorzugten Browser, und verweisen Sie in diesem auf die öffentliche URL, unter der Sie den Ordner hosten. Geben Sie Ihre [LUIS-Region](../../../../regions.md) sowie die LUIS-Anwendungs-ID ein, und lösen Sie über das Mikrofon eine Erkennung aus. Wenn dies konfiguriert wurde, wird ein Token aus der Tokenquelle abgerufen, und es wird mit der Erkennung gesprochener Befehle begonnen.

## <a name="next-steps"></a>Nächste Schritte

[!INCLUDE [footer](footer.md)]
