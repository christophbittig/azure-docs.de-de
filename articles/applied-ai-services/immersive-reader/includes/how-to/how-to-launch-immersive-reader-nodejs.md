---
author: nitinme
manager: nitinme
ms.service: applied-ai-services
ms.subservice: immersive-reader
ms.topic: include
ms.date: 03/04/2021
ms.author: nitinme
ms.openlocfilehash: 4b316349833a29ed5e93a2fce2fb3f7250ce1854
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131253357"
---
## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/cognitive-services)
* Eine Ressource des plastischen Readers, die für die Authentifizierung mit Azure Active Directory konfiguriert ist. Befolgen Sie [diese Anweisungen](../../how-to-create-immersive-reader.md) für die Einrichtung.  Einige der hier erstellten Werte benötigen Sie bei der Konfiguration der Umgebungseigenschaften. Speichern Sie die Ausgabe Ihrer Sitzung zur späteren Verwendung in einer Textdatei.
* [Node.js](https://nodejs.org/) und [YARN](https://yarnpkg.com)
* Eine IDE (beispielsweise [Visual Studio Code](https://code.visualstudio.com/))

## <a name="create-a-nodejs-web-app-with-express"></a>Erstellen einer Node.js-Web-App mit Express

Erstellen Sie eine Node.js-Web-App mithilfe des Tools `express-generator`.

```bash
npm install express-generator -g
express --view=pug myapp
cd myapp
```

Installieren Sie YARN-Abhängigkeiten, und fügen Sie die Abhängigkeiten `request` und `dotenv` hinzu. Sie werden später in diesem Tutorial benötigt.

```bash
yarn
yarn add request
yarn add dotenv
```

## <a name="acquire-an-azure-ad-authentication-token"></a>Abrufen eines Azure AD-Authentifizierungstokens

Schreiben Sie als Nächstes eine Back-End-API, um ein Azure AD-Authentifizierungstoken abzurufen.

Für diesen Teil benötigen Sie einige Werte aus dem obigen Schritt zur Konfiguration der Azure AD-Authentifizierung. Greifen Sie auf die Textdatei zurück, die Sie in dieser Sitzung gespeichert haben.

````text
TenantId     => Azure subscription TenantId
ClientId     => Azure AD ApplicationId
ClientSecret => Azure AD Application Service Principal password
Subdomain    => Immersive Reader resource subdomain (resource 'Name' if the resource was created in the Azure portal, or 'CustomSubDomain' option if the resource was created with Azure CLI Powershell. Check the Azure portal for the subdomain on the Endpoint in the resource Overview page, for example, 'https://[SUBDOMAIN].cognitiveservices.azure.com/')
````

Erstellen Sie mit diesen Werten eine neue Datei namens _.env_, und fügen Sie den folgenden Code ein. Geben Sie dabei Ihre benutzerdefinierten Eigenschaftswerte von oben an. Verwenden Sie weder Anführungszeichen noch die Zeichen „{“ und „}“.

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

Öffnen Sie die Datei _routes\index.js_, und ersetzen Sie ihren Inhalt durch den folgenden Code.

Dieser Code erstellt einen API-Endpunkt, der unter Verwendung Ihres Dienstprinzipalkennworts ein Azure AD-Authentifizierungstoken bezieht. Darüber hinaus wird die Unterdomäne abgerufen. Anschließend wird ein Objekt zurückgegeben, das das Token und die Unterdomäne enthält.

```javascript
var request = require('request');
var express = require('express');
var router = express.Router();

router.get('/getimmersivereaderlaunchparams', function(req, res) {
    request.post ({
                headers: {
                    'content-type': 'application/x-www-form-urlencoded'
                },
                url: `https://login.windows.net/${process.env.TENANT_ID}/oauth2/token`,
                form: {
                    grant_type: 'client_credentials',
                    client_id: process.env.CLIENT_ID,
                    client_secret: process.env.CLIENT_SECRET,
                    resource: 'https://cognitiveservices.azure.com/'
                }
        },
        function(err, resp, tokenResponse) {
                if (err) {
                    return res.status(500).send('CogSvcs IssueToken error');
                }

                const token = JSON.parse(tokenResponse).access_token;
                const subdomain = process.env.SUBDOMAIN;
                return res.send({token: token, subdomain: subdomain});
        }
  );
});

/* GET home page. */
router.get('/', function(req, res, next) {
  res.render('index', { title: 'Express' });
});

module.exports = router;

```

Der API-Endpunkt **getimmersivereaderlaunchparams** muss durch eine Authentifizierung (beispielsweise [OAuth](https://oauth.net/2/)) geschützt werden, um zu verhindern, dass nicht autorisierte Benutzer Token abrufen und für Ihren Dienst „Plastischer Reader“ und die Abrechnung verwenden. Dies wird jedoch in diesem Tutorial nicht behandelt.

## <a name="launch-the-immersive-reader-with-sample-content"></a>Starten von Plastischer Reader mit Beispielinhalt

1. Öffnen Sie _views\layout.pug_, und fügen Sie unter dem Tag `head` den folgenden Code vor dem Tag `body` hinzu. Diese Tags vom Typ `script` laden das [SDK für Plastischer Reader](https://github.com/microsoft/immersive-reader-sdk) sowie JQuery.

    ```pug
    script(src='https://contentstorage.onenote.office.net/onenoteltir/immersivereadersdk/immersive-reader-sdk.0.0.2.js')
    script(src='https://code.jquery.com/jquery-3.3.1.min.js')
    ```

2. Öffnen Sie die Datei _views\index.pug_, und ersetzen Sie ihren Inhalt durch den folgenden Code. Dieser Code füllt die Seite mit Beispielinhalten und fügt eine Schaltfläche zum Starten von Plastischer Reader hinzu.

    ```pug
    extends layout

    block content
          h2(id='title') Geography
          p(id='content') The study of Earth's landforms is called physical geography. Landforms can be mountains and valleys. They can also be glaciers, lakes or rivers.
          div(class='immersive-reader-button' data-button-style='iconAndText' data-locale='en-US' onclick='launchImmersiveReader()')
          script.

            function getImmersiveReaderLaunchParamsAsync() {
                    return new Promise((resolve, reject) => {
                        $.ajax({
                                url: '/getimmersivereaderlaunchparams',
                                type: 'GET',
                                success: data => {
                                        resolve(data);
                                },
                                error: err => {
                                        console.log('Error in getting token and subdomain!', err);
                                        reject(err);
                                }
                        });
                    });
            }

            async function launchImmersiveReader() {
                    const content = {
                            title: document.getElementById('title').innerText,
                            chunks: [{
                                    content: document.getElementById('content').innerText + '\n\n',
                                    lang: 'en'
                            }]
                    };

                    const launchParams = await getImmersiveReaderLaunchParamsAsync();
                    const token = launchParams.token;
                    const subdomain = launchParams.subdomain;

                    ImmersiveReader.launchAsync(token, subdomain, content);
            }
    ```

3. Die Web-App ist nun bereit. Führen Sie den folgenden Befehl aus, um die App zu starten:

    ```bash
    npm start
    ```

4. Navigieren Sie in Ihrem Browser zu _http://localhost:3000_ . Auf der Seite sollte der oben angegebene Inhalt angezeigt werden. Klicken Sie auf die Schaltfläche **Immersive Reader** (Plastischer Reader), um Plastischer Reader mit Ihrem Inhalt zu starten.

## <a name="specify-the-language-of-your-content"></a>Angeben der Sprache Ihres Inhalts

Plastischer Reader unterstützt zahlreiche Sprachen. Gehen Sie wie folgt vor, um die Sprache Ihres Inhalts anzugeben:

1. Öffnen Sie _views\index.pug_, und fügen Sie unter dem Tag `p(id=content)`, das Sie im vorherigen Schritt hinzugefügt haben, den folgenden Code hinzu. Dieser Code fügt Ihrer Seite spanischen Inhalt hinzu.

    ```pug
    p(id='content-spanish') El estudio de las formas terrestres de la Tierra se llama geografía física. Los accidentes geográficos pueden ser montañas y valles. También pueden ser glaciares, lagos o ríos.
    ```

2. Fügen Sie im JavaScript-Code über dem Aufruf von `ImmersiveReader.launchAsync` Folgendes hinzu. Dieser Code übergibt den spanischen Inhalt an Plastischer Reader.

    ```pug
    content.chunks.push({
      content: document.getElementById('content-spanish').innerText + '\n\n',
      lang: 'es'
    });
    ```

3. Navigieren Sie erneut zu _http://localhost:3000_ . Daraufhin sollte der spanische Text auf der Seite angezeigt werden, und wenn Sie auf **Immersive Reader** (Plastischer Reader) klicken, wird er auch in Plastischer Reader angezeigt.

## <a name="specify-the-language-of-the-immersive-reader-interface"></a>Angeben der Benutzeroberflächensprache von Plastischer Reader

Die Benutzeroberflächensprache von Plastischer Reader entspricht standardmäßig den Spracheinstellungen des Browsers. Mit dem folgenden Code können Sie auch die Benutzeroberflächensprache von Plastischer Reader angeben.

1. Ersetzen Sie in _views\index.pug_ den Aufruf von `ImmersiveReader.launchAsync(token, subdomain, content)` durch den folgenden Code:

    ```javascript
    const options = {
        uiLang: 'fr',
    }
    ImmersiveReader.launchAsync(token, subdomain, content, options);
    ```

2. Navigieren Sie zu _http://localhost:3000_. Wenn Sie Plastischer Reader starten, wird die Benutzeroberfläche auf Französisch angezeigt.

## <a name="launch-the-immersive-reader-with-math-content"></a>Starten von Plastischer Reader mit mathematischem Inhalt

Mathematische Inhalte können mithilfe von [MathML](https://developer.mozilla.org/en-US/docs/Web/MathML) in Plastischer Reader eingeschlossen werden.

1. Fügen Sie in _views\index.pug_ über dem Aufruf von `ImmersiveReader.launchAsync` den folgenden Code hinzu:

    ```javascript
    const mathML = '<math xmlns="https://www.w3.org/1998/Math/MathML" display="block"> \
      <munderover> \
        <mo>∫</mo> \
        <mn>0</mn> \
        <mn>1</mn> \
      </munderover> \
      <mrow> \
        <msup> \
          <mi>x</mi> \
          <mn>2</mn> \
        </msup> \
        <mo>ⅆ</mo> \
        <mi>x</mi> \
      </mrow> \
    </math>';

    content.chunks.push({
      content: mathML,
      mimeType: 'application/mathml+xml'
    });
    ```

2. Navigieren Sie zu _http://localhost:3000_. Wenn Sie Plastischer Reader starten und nach unten scrollen, sehen Sie die mathematische Formel.

## <a name="next-steps"></a>Nächste Schritte

* Machen Sie sich mit dem [SDK für Plastischer Reader](https://github.com/microsoft/immersive-reader-sdk) und der [zugehörigen Referenz](../../reference.md) vertraut.
* Sehen Sie sich Codebeispiele auf [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/advanced-csharp).
