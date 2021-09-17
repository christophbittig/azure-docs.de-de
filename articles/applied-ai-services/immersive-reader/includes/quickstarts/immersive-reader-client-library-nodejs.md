---
title: 'Schnellstart: Node.js-Clientbibliothek für den plastischen Reader'
titleSuffix: Azure Applied AI Services
description: In dieser Schnellstartanleitung erstellen Sie eine Web-App von Grund auf neu und fügen die Funktion der API für den plastischen Reader hinzu.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: applied-ai-services
ms.subservice: immersive-reader
ms.topic: include
ms.date: 09/14/2020
ms.author: nitinme
ms.custom: devx-track-js
ms.openlocfilehash: de4dda828b821db66d89387f1f63bf20372fd005
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121784455"
---
[Plastischer Reader](https://www.onenote.com/learningtools) ist ein inklusiv konzipiertes Tool, das bewährte Techniken implementiert, um das Leseverständnis von Leseanfängern, Sprachenlernenden und Personen mit Lernunterschieden, wie z. B. Dyslexie, zu verbessern. Sie können den plastischen Reader in Ihren Anwendungen verwenden, um Text zu isolieren, den Fokus zu verbessern, Bilder für häufig verwendete Wörter anzuzeigen, Wortarten hervorzuheben, markierten Text laut vorzulesen, Wörter und Ausdrücke in Echtzeit zu übersetzen und vieles mehr.

In dieser Schnellstartanleitung erstellen Sie eine Web-App von Grund auf neu und integrieren den plastischen Reader mithilfe der Clientbibliothek für den plastischen Reader. Ein vollständiges Arbeitsbeispiel für diesen Schnellstart finden Sie auf [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/quickstart-nodejs).

## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/cognitive-services)
* Eine Ressource des plastischen Readers, die für die Authentifizierung mit Azure Active Directory konfiguriert ist. Befolgen Sie [diese Anweisungen](../../how-to-create-immersive-reader.md) für die Einrichtung. Einige der hier erstellten Werte benötigen Sie bei der Konfiguration der Umgebungseigenschaften. Speichern Sie die Ausgabe Ihrer Sitzung zur späteren Verwendung in einer Textdatei.
* [Node.js](https://nodejs.org/) und [YARN](https://yarnpkg.com)
* Eine IDE (beispielsweise [Visual Studio Code](https://code.visualstudio.com/))

## <a name="create-a-nodejs-web-app-with-express"></a>Erstellen einer Node.js-Web-App mit Express

Erstellen Sie eine Node.js-Web-App mithilfe des Tools `express-generator`.

```bash
npm install express-generator -g
express --view=pug quickstart-nodejs
cd quickstart-nodejs
```

Installieren Sie YARN-Abhängigkeiten, und fügen Sie die Abhängigkeiten `request` und `dotenv` hinzu. Sie werden später in der Schnellstartanleitung benötigt.

```bash
yarn
yarn add request
yarn add dotenv
```

Installieren Sie die Bibliotheken **axios** und **qs** mit dem folgenden Befehl:

```bash
npm install axios qs
```

## <a name="set-up-authentication"></a>Einrichten der Authentifizierung

### <a name="configure-authentication-values"></a>Konfigurieren der Authentifizierungswerte

Erstellen Sie im Stammverzeichnis des Projekts eine neue Datei mit dem Namen _.env_. Fügen Sie den folgenden Code in die Datei ein. Verwenden Sie dabei die Werte, die beim Erstellen der Ressource des plastischen Readers angegeben wurden.
Verwenden Sie weder Anführungszeichen noch die Zeichen „{“ und „}“.

```text
TENANT_ID={YOUR_TENANT_ID}
CLIENT_ID={YOUR_CLIENT_ID}
CLIENT_SECRET={YOUR_CLIENT_SECRET}
SUBDOMAIN={YOUR_SUBDOMAIN}
```

Committen Sie diese Datei nicht in Ihre Quellcodeverwaltung, da sie Geheimnisse enthält, die nicht für die Öffentlichkeit bestimmt sind.

Öffnen Sie als Nächstes die Datei _app.js_, und fügen Sie am Anfang der Datei Folgendes hinzu. Dadurch werden die in der ENV-Datei definierten Eigenschaften als Umgebungsvariablen in den Knoten geladen.

```javascript
require('dotenv').config();
```

### <a name="update-the-router-to-acquire-the-token"></a>Aktualisieren des Routers zum Abrufen des Tokens

Öffnen Sie die Datei _routes\index.js_, und ersetzen Sie den automatisch generierten Code durch den folgenden Code.

Dieser Code erstellt einen API-Endpunkt, der unter Verwendung Ihres Dienstprinzipalkennworts ein Azure AD-Authentifizierungstoken bezieht. Darüber hinaus wird die Unterdomäne abgerufen. Anschließend wird ein Objekt zurückgegeben, das das Token und die Unterdomäne enthält.

```javascript
var axios = require('axios');
var express = require('express');
var router = express.Router();
var qs = require('qs');

/* GET home page. */
router.get('/', function(req, res, next) {
  res.render('index', { title: 'Express' });
});

router.get('/GetTokenAndSubdomain', function(req, res) {
    try {
        var config ={
            headers: {
                'content-type': 'application/x-www-form-urlencoded'
            }
        }
        var data = {
            grant_type: 'client_credentials',
            client_id: process.env.CLIENT_ID,
            client_secret: process.env.CLIENT_SECRET,
            resource: 'https://cognitiveservices.azure.com/'
        };
        var url = `https://login.windows.net/${process.env.TENANT_ID}/oauth2/token`
        console.log(qs.stringify(data));
        axios.post(url, qs.stringify(data), config)
        .then(function (response) {
            var token = response.data.access_token;
            var subdomain = process.env.SUBDOMAIN;
            return res.send({token, subdomain});
        })
        .catch(function (response) {
            if (response.status !== 200) {
                return res.send({error :  "Unable to acquire Azure AD token. Check the debugger for more information."})
            }
        });
    } catch (error) {
        console.log(error);
        return res.status(500).send('CogSvcs IssueToken error');
    }
});

module.exports = router;
```

Der API-Endpunkt **GetTokenAndSubdomain** muss durch eine Authentifizierung (beispielsweise [OAuth](https://oauth.net/2/)) geschützt werden, um zu verhindern, dass nicht autorisierte Benutzer Token abrufen und für Ihren Dienst „Plastischer Reader“ bzw. für die Abrechnung verwenden. Diese Schritte sind in dieser Schnellstartanleitung nicht beschrieben.

## <a name="add-sample-content"></a>Hinzufügen von Beispielinhalten

Nun fügen Sie dieser Web-App Beispielinhalte hinzu. Öffnen Sie _views\index.pug_, und ersetzen Sie den automatisch generierten Code durch dieses Beispiel:

```pug
doctype html
html
   head
      title Immersive Reader Quickstart Node.js

      link(rel='icon', href='data:;base64,iVBORw0KGgo=')

      link(rel='stylesheet', href='https://stackpath.bootstrapcdn.com/bootstrap/3.4.1/css/bootstrap.min.css')

      // A polyfill for Promise is needed for IE11 support.
      script(src='https://cdn.jsdelivr.net/npm/promise-polyfill@8/dist/polyfill.min.js')

      script(src='https://contentstorage.onenote.office.net/onenoteltir/immersivereadersdk/immersive-reader-sdk.1.1.0.js')
      script(src='https://code.jquery.com/jquery-3.3.1.min.js')

      style(type="text/css").
        .immersive-reader-button {
          background-color: white;
          margin-top: 5px;
          border: 1px solid black;
          float: right;
        }
   body
      div(class="container")
        button(class="immersive-reader-button" data-button-style="iconAndText" data-locale="en")

        h1(id="ir-title") About Immersive Reader
        div(id="ir-content" lang="en-us")
          p Immersive Reader is a tool that implements proven techniques to improve reading comprehension for emerging readers, language learners, and people with learning differences. The Immersive Reader is designed to make reading more accessible for everyone. The Immersive Reader

            ul
                li Shows content in a minimal reading view
                li Displays pictures of commonly used words
                li Highlights nouns, verbs, adjectives, and adverbs
                li Reads your content out loud to you
                li Translates your content into another language
                li Breaks down words into syllables

          h3 The Immersive Reader is available in many languages.

          p(lang="es-es") El Lector inmersivo está disponible en varios idiomas.
          p(lang="zh-cn") 沉浸式阅读器支持许多语言
          p(lang="de-de") Der plastische Reader ist in vielen Sprachen verfügbar.
          p(lang="ar-eg" dir="rtl" style="text-align:right") يتوفر \"القارئ الشامل\" في العديد من اللغات.

script(type="text/javascript").
  function getTokenAndSubdomainAsync() {
        return new Promise(function (resolve, reject) {
            $.ajax({
                url: "/GetTokenAndSubdomain",
                type: "GET",
                success: function (data) {
                    if (data.error) {
                        reject(data.error);
                    } else {
                        resolve(data);
                    }
                },
                error: function (err) {
                    reject(err);
                }
            });
        });
    }

    $(".immersive-reader-button").click(function () {
        handleLaunchImmersiveReader();
    });

    function handleLaunchImmersiveReader() {
        getTokenAndSubdomainAsync()
            .then(function (response) {
                const token = response["token"];
                const subdomain = response["subdomain"];
                // Learn more about chunk usage and supported MIME types https://docs.microsoft.com/azure/cognitive-services/immersive-reader/reference#chunk
                const data = {
                    title: $("#ir-title").text(),
                    chunks: [{
                        content: $("#ir-content").html(),
                        mimeType: "text/html"
                    }]
                };
                // Learn more about options https://docs.microsoft.com/azure/cognitive-services/immersive-reader/reference#options
                const options = {
                    "onExit": exitCallback,
                    "uiZIndex": 2000
                };
                ImmersiveReader.launchAsync(token, subdomain, data, options)
                    .catch(function (error) {
                        alert("Error in launching the Immersive Reader. Check the console.");
                        console.log(error);
                    });
            })
            .catch(function (error) {
                alert("Error in getting the Immersive Reader token and subdomain. Check the console.");
                console.log(error);
            });
    }

    function exitCallback() {
        console.log("This is the callback function. It is executed when the Immersive Reader closes.");
    }
```

Beachten Sie, dass der gesamte Text über das Attribut **lang** verfügt, das die Sprachen des Texts beschreibt. Dieses Attribut unterstützt den plastischen Reader dabei, relevante Sprach- und Grammatikfeatures bereitzustellen.

## <a name="build-and-run-the-app"></a>Erstellen und Ausführen der App

Die Web-App ist nun bereit. Führen Sie den folgenden Befehl aus, um die App zu starten:

```bash
npm start
```

Navigieren Sie in Ihrem Browser zu _http://localhost:3000_ . Daraufhin sollte Folgendes angezeigt werden:

![Beispiel-App: Node.js](../../media/quickstart-nodejs/1-buildapp.png)

## <a name="launch-the-immersive-reader"></a>Starten des plastischen Readers

Wenn Sie auf die Schaltfläche „Plastischer Reader“ klicken, sehen Sie, dass der plastische Reader mit dem Inhalt auf der Seite gestartet wurde.

![Plastischer Reader: Node.js](../../media/quickstart-nodejs/2-viewimmersivereader.png)

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erstellen einer Ressource und Konfigurieren von AAD](../../how-to-create-immersive-reader.md)