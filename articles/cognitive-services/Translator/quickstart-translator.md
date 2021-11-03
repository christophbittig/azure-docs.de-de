---
title: 'Schnellstart: Erste Schritte mit der Textübersetzung'
titleSuffix: Azure Cognitive Services
description: Lernen Sie, wie Sie mit dem Textübersetzungsdienst Text übersetzen, Text transkribieren, die Sprache erkennen und mehr. Es stehen Beispiele in C#, Java, JavaScript und Python zur Verfügung.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 07/06/2021
ms.author: lajanuar
ms.custom: cog-serv-seo-aug-2020
keywords: Textübersetzung, Übersetzerdienst, Text übersetzen, Text transkribieren, Sprachenerkennung
ms.openlocfilehash: 4b3aa5c75f18126f14fb3fae6c3a7a9a93b39710
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131011824"
---
# <a name="quickstart-get-started-with-translator"></a>Schnellstart: Erste Schritte mit der Textübersetzung

In dieser Schnellstartanleitung erfahren Sie, wie Sie den Textübersetzungsdienst mithilfe von REST verwenden. Sie beginnen mit einfachen Beispielen und fahren dann mit einigen Konfigurationsoptionen für den Kern fort, die bei der Entwicklung häufig verwendet werden, darunter:

* [Übersetzung](#translate-text)
* [Transliteration](#transliterate-text)
* [Sprachenerkennung](#detect-language)
* [Berechnen der Satzlänge](#get-sentence-length)
* [Abrufen alternativer Übersetzungen](#dictionary-lookup-alternate-translations) und [Beispiele für die Verwendung von Wörtern in einem Satz](#dictionary-examples-translations-in-context)

## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/cognitive-services/)
* Sobald Sie über ein Azure-Abonnement verfügen, erstellen Sie eine [Textübersetzungsressource](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation) im Azure-Portal, um Ihren Schlüssel und Endpunkt zu erhalten. Wählen Sie nach Abschluss der Bereitstellung **Zu Ressource wechseln** aus.
  * Sie benötigen den Schlüssel und Endpunkt der Ressource, um Ihre Anwendung mit dem Textübersetzungsdienst zu verbinden. Der Schlüssel und der Endpunkt werden weiter unten in der Schnellstartanleitung in den Code eingefügt. Diese Werte sind im Azure-Portal auf der Seite **Schlüssel und Endpunkt** aufgeführt:

    :::image type="content" source="media/keys-and-endpoint-portal.png" alt-text="Screenshot: Seite mit Schlüsseln und Endpunkt im Azure-Portal.":::

* Sie können den kostenlosen Tarif (F0) verwenden, um den Dienst zu testen, und später für die Produktion auf einen kostenpflichtigen Tarif upgraden.

## <a name="platform-setup"></a>Plattformeinrichtung

# <a name="c"></a>[C#](#tab/csharp)

* Erstellen Sie ein neues Projekt: `dotnet new console -o your_project_name`
* Ersetzen Sie den Code in „Program.cs“ durch den unten abgebildeten C#-Code.
* Legen Sie die Werte von Abonnementschlüssel und Endpunkt in „Program.cs“ fest.
* [Fügen Sie „Newtonsoft.Json“ mithilfe der .NET-CLI hinzu](https://www.nuget.org/packages/Newtonsoft.Json/).
* Führen Sie das Programm aus dem Projektverzeichnis aus: ``dotnet run``

> [!div class="nextstepaction"]
> [Ich habe ein Projekt erstellt.](#headers) [Es ist ein Problem aufgetreten.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Csharp&Product=Translator&Page=quickstart-translator&Section=platform-setup)

# <a name="go"></a>[Go](#tab/go)

* Erstellen Sie ein neues Go-Projekt in Ihrem bevorzugten Code-Editor.
* Fügen Sie den unten stehenden Code hinzu.
* Ersetzen Sie den `subscriptionKey`-Wert durch einen für Ihr Abonnement gültigen Zugriffsschlüssel.
* Speichern Sie die Datei mit der Erweiterung „.go“.
* Öffnen Sie auf einem Computer, auf dem Go installiert ist, eine Eingabeaufforderung.
* Erstellen Sie die Datei, beispielsweise mit „go build example-code.go“.
* Führen Sie die Datei aus, beispielsweise mit „example-code“.

> [!div class="nextstepaction"]
> [Ich habe ein Projekt erstellt.](#headers) [Es ist ein Problem aufgetreten.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Product=Translator&Page=quickstart-translator&Section=platform-setup)

# <a name="java"></a>[Java](#tab/java)

* Erstellen Sie ein Arbeitsverzeichnis für Ihr Projekt. Beispiel: `mkdir sample-project`.
* Initialisieren Sie Ihr Projekt mit Gradle: `gradle init --type basic`. Wenn Sie zur Auswahl einer **DSL** aufgefordert werden, wählen Sie **Kotlin** aus.
* Aktualisieren Sie `build.gradle.kts`. Beachten Sie, dass Sie Ihren `mainClassName` passend zum Beispiel aktualisieren müssen.
  ```java
  plugins {
    java
    application
  }
  application {
    mainClassName = "<NAME OF YOUR CLASS>"
  }
  repositories {
    mavenCentral()
  }
  dependencies {
    compile("com.squareup.okhttp:okhttp:2.5.0")
    compile("com.google.code.gson:gson:2.8.5")
  }
  ```
* Erstellen Sie eine Java-Datei, und kopieren Sie den Code aus dem bereitgestellten Beispiel hinein. Vergessen Sie nicht, ihren Abonnementschlüssel hinzuzufügen.
* Führen Sie das Beispiel aus: `gradle run`.

> [!div class="nextstepaction"]
> [Ich habe ein Projekt erstellt.](#headers) [Es ist ein Problem aufgetreten.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Java&Product=Translator&Page=quickstart-translator&Section=platform-setup)

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

* Erstellen Sie in Ihrer bevorzugten IDE oder einem Editor ein neues Projekt.
* Kopieren Sie den Code aus einem der Beispiele in Ihr Projekt.
* Legen Sie Ihren Abonnementschlüssel fest.
* Führen Sie das Programm aus. Beispiel: `node Translate.js`.

> [!div class="nextstepaction"]
> [Ich habe ein Projekt erstellt.](#headers) [Es ist ein Problem aufgetreten.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Nodejs&Product=Translator&Page=quickstart-translator&Section=platform-setup)

# <a name="python"></a>[Python](#tab/python)

* Erstellen Sie in Ihrer bevorzugten IDE oder einem Editor ein neues Projekt.
* Kopieren Sie den Code aus einem der Beispiele in Ihr Projekt.
* Legen Sie Ihren Abonnementschlüssel fest.
* Führen Sie das Programm aus. Beispiel: `python translate.py`.

> [!div class="nextstepaction"]
> [Ich habe ein Projekt erstellt.](#headers) [Es ist ein Problem aufgetreten.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Python&Product=Translator&Page=quickstart-translator&Section=platform-setup)

---

## <a name="headers"></a>Header 

Beim Aufrufen des Textübersetzungsdiensts über REST müssen Sie sicherstellen, dass jede Anforderung die folgenden Header enthält. Keine Sorge, wir fügen die Header in den folgenden Abschnitten in den Beispielcode ein. 

<table width="100%">
  <th width="20%">Header</th>
  <th>BESCHREIBUNG</th>
  <tr>
    <td>Authentifizierungsheader</td>
    <td><em>Erforderlicher Anforderungsheader</em>.<br/><code>Ocp-Apim-Subscription-Key</code><br/><br/><em>Erforderlicher Anforderungsheader bei Verwendung einer Cognitive Services-Ressource. Optional, wenn eine Textübersetzungsressource verwendet wird.</em>.<br/><code>Ocp-Apim-Subscription-Region</code><br/><br/>Weitere Informationen finden Sie in den <a href="/azure/cognitive-services/translator/reference/v3-0-reference#authentication">verfügbaren Optionen für die Authentifizierung</a>.</td>
  </tr>
  <tr>
    <td>Content-Type</td>
    <td><em>Erforderlicher Anforderungsheader</em>.<br/>Gibt den Inhaltstyp der Nutzlast an.<br/> Der zulässige Wert ist <code>application/json; charset=UTF-8</code>.</td>
  </tr>
  <tr>
    <td>Content-Length</td>
    <td><em>Erforderlicher Anforderungsheader</em>.<br/>Die Länge des Anforderungstexts.</td>
  </tr>
  <tr>
    <td>X-ClientTraceId</td>
    <td><em>Optional:</em><br/>Eine vom Client erstellte GUID zur eindeutigen Identifizierung der Anforderung. Sie können diesen Header nur weglassen, wenn Sie die Ablaufverfolgungs-ID in die Abfragezeichenfolge über einen Abfrageparameter namens <code>ClientTraceId</code> einschließen.</td>
  </tr>
</table> 

## <a name="keys-and-endpoints"></a>Keys and endpoints (Schlüssel und Endpunkte)

In den Beispielen auf dieser Seite werden aus Gründen der Einfachheit hartcodierte Schlüssel und Endpunkte verwendet. Denken Sie daran, **den Schlüssel aus Ihrem Code zu entfernen, wenn Sie fertig sind**, und ihn **niemals zu veröffentlichen**. In der Produktionsumgebung sollten Sie eine sichere Methode zum Speichern Ihrer Anmeldeinformationen sowie zum Zugriff darauf verwenden. Weitere Informationen finden Sie im Cognitive Services-Artikel zur [Sicherheit](../cognitive-services-security.md).

## <a name="translate-text"></a>Text übersetzen 

Der Kernvorgang des Textübersetzungsdiensts besteht im Übersetzen von Text. In diesem Abschnitt erstellen Sie eine Anforderung, die eine einzelne Quelle (`from`) annimmt und zwei Ausgaben (`to`) zur Verfügung stellt. Anschließend überprüfen wir einige Parameter, die verwendet werden können, um sowohl die Anforderung als auch die Antwort anzupassen. 

# <a name="c"></a>[C#](#tab/csharp)

```csharp
using System;
using System.Net.Http;
using System.Text;
using System.Threading.Tasks;
using Newtonsoft.Json; // Install Newtonsoft.Json with NuGet

class Program
{
    private static readonly string subscriptionKey = "YOUR-SUBSCRIPTION-KEY";
    private static readonly string endpoint = "https://api.cognitive.microsofttranslator.com/";

    // Add your location, also known as region. The default is global.
    // This is required if using a Cognitive Services resource.
    private static readonly string location = "YOUR_RESOURCE_LOCATION";
    
    static async Task Main(string[] args)
    {
        // Input and output languages are defined as parameters.
        string route = "/translate?api-version=3.0&from=en&to=de&to=it";
        string textToTranslate = "Hello, world!";
        object[] body = new object[] { new { Text = textToTranslate } };
        var requestBody = JsonConvert.SerializeObject(body);
    
        using (var client = new HttpClient())
        using (var request = new HttpRequestMessage())
        {
            // Build the request.
            request.Method = HttpMethod.Post;
            request.RequestUri = new Uri(endpoint + route);
            request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");
            request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
            request.Headers.Add("Ocp-Apim-Subscription-Region", location);
    
            // Send the request and get response.
            HttpResponseMessage response = await client.SendAsync(request).ConfigureAwait(false);
            // Read response as a string.
            string result = await response.Content.ReadAsStringAsync();
            Console.WriteLine(result);
        }
    }
}
```

> [!div class="nextstepaction"]
> [Ich habe Text übersetzt.](#detect-language) [Es ist ein Problem aufgetreten.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Csharp&Product=Translator&Page=quickstart-translator&Section=translate-text)

# <a name="go"></a>[Go](#tab/go)

```go
package main

import (
    "bytes"
    "encoding/json"
    "fmt"
    "log"
    "net/http"
    "net/url"
)

func main() {
    subscriptionKey := "YOUR-SUBSCRIPTION-KEY"
    // Add your location, also known as region. The default is global.
    // This is required if using a Cognitive Services resource.
    location := "YOUR_RESOURCE_LOCATION";
    endpoint := "https://api.cognitive.microsofttranslator.com/"
    uri := endpoint + "/translate?api-version=3.0"

    // Build the request URL. See: https://golang.org/pkg/net/url/#example_URL_Parse
    u, _ := url.Parse(uri)
    q := u.Query()
    q.Add("from", "en")
    q.Add("to", "de")
    q.Add("to", "it")
    u.RawQuery = q.Encode()

    // Create an anonymous struct for your request body and encode it to JSON
    body := []struct {
        Text string
    }{
        {Text: "Hello, world!"},
    }
    b, _ := json.Marshal(body)

    // Build the HTTP POST request
    req, err := http.NewRequest("POST", u.String(), bytes.NewBuffer(b))
    if err != nil {
        log.Fatal(err)
    }
    // Add required headers to the request
    req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)
    req.Header.Add("Ocp-Apim-Subscription-Region", location)
    req.Header.Add("Content-Type", "application/json")

    // Call the Translator API
    res, err := http.DefaultClient.Do(req)
    if err != nil {
        log.Fatal(err)
    }

    // Decode the JSON response
    var result interface{}
    if err := json.NewDecoder(res.Body).Decode(&result); err != nil {
        log.Fatal(err)
    }
    // Format and print the response to terminal
    prettyJSON, _ := json.MarshalIndent(result, "", "  ")
    fmt.Printf("%s\n", prettyJSON)
}
```

> [!div class="nextstepaction"]
> [Ich habe Text übersetzt.](#detect-language) [Es ist ein Problem aufgetreten.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Product=Translator&Page=quickstart-translator&Section=translate-text)


# <a name="java"></a>[Java](#tab/java)

```java
import java.io.*;
import java.net.*;
import java.util.*;
import com.google.gson.*;
import com.squareup.okhttp.*;

public class Translate {
    private static String subscriptionKey = "YOUR_SUBSCRIPTION_KEY";

    // Add your location, also known as region. The default is global.
    // This is required if using a Cognitive Services resource.
    private static String location = "YOUR_RESOURCE_LOCATION";

    HttpUrl url = new HttpUrl.Builder()
        .scheme("https")
        .host("api.cognitive.microsofttranslator.com")
        .addPathSegment("/translate")
        .addQueryParameter("api-version", "3.0")
        .addQueryParameter("from", "en")
        .addQueryParameter("to", "de")
        .addQueryParameter("to", "it")
        .build();

    // Instantiates the OkHttpClient.
    OkHttpClient client = new OkHttpClient();

    // This function performs a POST request.
    public String Post() throws IOException {
        MediaType mediaType = MediaType.parse("application/json");
        RequestBody body = RequestBody.create(mediaType,
                "[{\"Text\": \"Hello World!\"}]");
        Request request = new Request.Builder().url(url).post(body)
                .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey)
                .addHeader("Ocp-Apim-Subscription-Region", location)
                .addHeader("Content-type", "application/json")
                .build();
        Response response = client.newCall(request).execute();
        return response.body().string();
    }

    // This function prettifies the json response.
    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonElement json = parser.parse(json_text);
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }

    public static void main(String[] args) {
        try {
            Translate translateRequest = new Translate();
            String response = translateRequest.Post();
            System.out.println(prettify(response));
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}
```

> [!div class="nextstepaction"]
> [Ich habe Text übersetzt.](#detect-language) [Es ist ein Problem aufgetreten.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Java&Product=Translator&Page=quickstart-translator&Section=translate-text)

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

```Javascript
const axios = require('axios').default;
const { v4: uuidv4 } = require('uuid');

var subscriptionKey = "YOUR_SUBSCRIPTION_KEY";
var endpoint = "https://api.cognitive.microsofttranslator.com";

// Add your location, also known as region. The default is global.
// This is required if using a Cognitive Services resource.
var location = "YOUR_RESOURCE_LOCATION";

axios({
    baseURL: endpoint,
    url: '/translate',
    method: 'post',
    headers: {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
        'Ocp-Apim-Subscription-Region': location,
        'Content-type': 'application/json',
        'X-ClientTraceId': uuidv4().toString()
    },
    params: {
        'api-version': '3.0',
        'from': 'en',
        'to': ['de', 'it']
    },
    data: [{
        'text': 'Hello World!'
    }],
    responseType: 'json'
}).then(function(response){
    console.log(JSON.stringify(response.data, null, 4));
})
```

> [!div class="nextstepaction"]
> [Ich habe Text übersetzt.](#detect-language) [Es ist ein Problem aufgetreten.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Nodejs&Product=Translator&Page=quickstart-translator&Section=translate-text)


# <a name="python"></a>[Python](#tab/python)
```python
import requests, uuid, json

# Add your subscription key and endpoint
subscription_key = "YOUR_SUBSCRIPTION_KEY"
endpoint = "https://api.cognitive.microsofttranslator.com"

# Add your location, also known as region. The default is global.
# This is required if using a Cognitive Services resource.
location = "YOUR_RESOURCE_LOCATION"

path = '/translate'
constructed_url = endpoint + path

params = {
    'api-version': '3.0',
    'from': 'en',
    'to': ['de', 'it']
}
constructed_url = endpoint + path

headers = {
    'Ocp-Apim-Subscription-Key': subscription_key,
    'Ocp-Apim-Subscription-Region': location,
    'Content-type': 'application/json',
    'X-ClientTraceId': str(uuid.uuid4())
}

# You can pass more than one object in body.
body = [{
    'text': 'Hello World!'
}]

request = requests.post(constructed_url, params=params, headers=headers, json=body)
response = request.json()

print(json.dumps(response, sort_keys=True, ensure_ascii=False, indent=4, separators=(',', ': ')))
```

> [!div class="nextstepaction"]
> [Ich habe Text übersetzt.](#detect-language) [Es ist ein Problem aufgetreten.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Python&Product=Translator&Page=quickstart-translator&Section=translate-text)

---

Nach einem erfolgreichen Aufruf sollten Sie die folgende Antwort sehen: 

```JSON
[
    {
        "translations": [
            {
                "text": "Hallo Welt!",
                "to": "de"
            },
            {
                "text": "Salve, mondo!",
                "to": "it"
            }
        ]
    }
]
```

## <a name="detect-language"></a>Sprache erkennen

Wenn Sie wissen, dass Sie eine Übersetzung benötigen, die Sprache des Texts aber nicht kennen, der an den Textübersetzungsdienst gesendet werden soll, können Sie den Sprachenerkennungsvorgang verwenden. Es gibt mehr als eine Möglichkeit, die Sprache des Ausgangstexts zu bestimmen. In diesem Abschnitt erfahren Sie, wie Sie die Sprachenerkennung mithilfe des `translate`-Endpunkts und des `detect`-Endpunkts verwenden. 

### <a name="detect-source-language-during-translation"></a>Erkennen der Quellsprache während der Übersetzung

Wenn Sie den `from`-Parameter nicht in Ihre Übersetzungsanforderung aufnehmen, versucht der Textübersetzungsdienst, die Sprache des Ausgangstexts zu erkennen. In der Antwort erhalten Sie die erkannte Sprache (`language`) und eine Vertrauensbewertung (`score`). Je näher `score` an `1.0` liegt, desto zuverlässiger wurde die Sprache richtig erkannt.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
using System;
using System.Net.Http;
using System.Text;
using System.Threading.Tasks;
using Newtonsoft.Json; // Install Newtonsoft.Json with NuGet

class Program
{
    private static readonly string subscriptionKey = "YOUR-SUBSCRIPTION-KEY";
    private static readonly string endpoint = "https://api.cognitive.microsofttranslator.com/";

    // Add your location, also known as region. The default is global.
    // This is required if using a Cognitive Services resource.
    private static readonly string location = "YOUR_RESOURCE_LOCATION";
    
    static async Task Main(string[] args)
    {
        // Output languages are defined as parameters, input language detected.
        string route = "/translate?api-version=3.0&to=de&to=it";
        string textToTranslate = "Hello, world!";
        object[] body = new object[] { new { Text = textToTranslate } };
        var requestBody = JsonConvert.SerializeObject(body);
    
        using (var client = new HttpClient())
        using (var request = new HttpRequestMessage())
        {
            // Build the request.
            request.Method = HttpMethod.Post;
            request.RequestUri = new Uri(endpoint + route);
            request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");
            request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
            request.Headers.Add("Ocp-Apim-Subscription-Region", location);
    
            // Send the request and get response.
            HttpResponseMessage response = await client.SendAsync(request).ConfigureAwait(false);
            // Read response as a string.
            string result = await response.Content.ReadAsStringAsync();
            Console.WriteLine(result);
        }
    }
}
```

> [!div class="nextstepaction"]
> [Ich habe während der Übersetzung die Ausgangssprache erkannt.](#detect-source-language-without-translation) [Es ist ein Problem aufgetreten.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Csharp&Product=Translator&Page=quickstart-translator&Section=detect-source-language-during-translation)


# <a name="go"></a>[Go](#tab/go)

```go
package main

import (
    "bytes"
    "encoding/json"
    "fmt"
    "log"
    "net/http"
    "net/url"
)

func main() {
    subscriptionKey := "YOUR-SUBSCRIPTION-KEY"
    // Add your location, also known as region. The default is global.
    // This is required if using a Cognitive Services resource.
    location := "YOUR_RESOURCE_LOCATION";
    endpoint := "https://api.cognitive.microsofttranslator.com/"
    uri := endpoint + "/translate?api-version=3.0"

    // Build the request URL. See: https://golang.org/pkg/net/url/#example_URL_Parse
    u, _ := url.Parse(uri)
    q := u.Query()
    q.Add("to", "de")
    q.Add("to", "it")
    u.RawQuery = q.Encode()

    // Create an anonymous struct for your request body and encode it to JSON
    body := []struct {
        Text string
    }{
        {Text: "Hello, world!"},
    }
    b, _ := json.Marshal(body)

    // Build the HTTP POST request
    req, err := http.NewRequest("POST", u.String(), bytes.NewBuffer(b))
    if err != nil {
        log.Fatal(err)
    }
    // Add required headers to the request
    req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)
    req.Header.Add("Ocp-Apim-Subscription-Region", location)
    req.Header.Add("Content-Type", "application/json")

    // Call the Translator API
    res, err := http.DefaultClient.Do(req)
    if err != nil {
        log.Fatal(err)
    }

    // Decode the JSON response
    var result interface{}
    if err := json.NewDecoder(res.Body).Decode(&result); err != nil {
        log.Fatal(err)
    }
    // Format and print the response to terminal
    prettyJSON, _ := json.MarshalIndent(result, "", "  ")
    fmt.Printf("%s\n", prettyJSON)
}
```

> [!div class="nextstepaction"]
> [Ich habe während der Übersetzung die Ausgangssprache erkannt.](#detect-source-language-without-translation) [Es ist ein Problem aufgetreten.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Product=Translator&Page=quickstart-translator&Section=detect-source-language-during-translation)


# <a name="java"></a>[Java](#tab/java)

```java
import java.io.*;
import java.net.*;
import java.util.*;
import com.google.gson.*;
import com.squareup.okhttp.*;

public class Translate {
    private static String subscriptionKey = "YOUR_SUBSCRIPTION_KEY";

    // Add your location, also known as region. The default is global.
    // This is required if using a Cognitive Services resource.
    private static String location = "YOUR_RESOURCE_LOCATION";

    HttpUrl url = new HttpUrl.Builder()
        .scheme("https")
        .host("api.cognitive.microsofttranslator.com")
        .addPathSegment("/translate")
        .addQueryParameter("api-version", "3.0")
        .addQueryParameter("to", "de")
        .addQueryParameter("to", "it")
        .build();

    // Instantiates the OkHttpClient.
    OkHttpClient client = new OkHttpClient();

    // This function performs a POST request.
    public String Post() throws IOException {
        MediaType mediaType = MediaType.parse("application/json");
        RequestBody body = RequestBody.create(mediaType,
                "[{\"Text\": \"Hello World!\"}]");
        Request request = new Request.Builder().url(url).post(body)
                .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey)
                .addHeader("Ocp-Apim-Subscription-Region", location)
                .addHeader("Content-type", "application/json")
                .build();
        Response response = client.newCall(request).execute();
        return response.body().string();
    }

    // This function prettifies the json response.
    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonElement json = parser.parse(json_text);
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }

    public static void main(String[] args) {
        try {
            Translate translateRequest = new Translate();
            String response = translateRequest.Post();
            System.out.println(prettify(response));
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}
```

> [!div class="nextstepaction"]
> [Ich habe während der Übersetzung die Ausgangssprache erkannt.](#detect-source-language-without-translation) [Es ist ein Problem aufgetreten.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Java&Product=Translator&Page=quickstart-translator&Section=detect-source-language-during-translation)


# <a name="nodejs"></a>[Node.js](#tab/nodejs)

```javascript
const axios = require('axios').default;
const { v4: uuidv4 } = require('uuid');

var subscriptionKey = "YOUR_SUBSCRIPTION_KEY";
var endpoint = "https://api.cognitive.microsofttranslator.com";

// Add your location, also known as region. The default is global.
// This is required if using a Cognitive Services resource.
var location = "YOUR_RESOURCE_LOCATION";

axios({
    baseURL: endpoint,
    url: '/translate',
    method: 'post',
    headers: {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
        'Ocp-Apim-Subscription-Region': location,
        'Content-type': 'application/json',
        'X-ClientTraceId': uuidv4().toString()
    },
    params: {
        'api-version': '3.0',
        'from': 'en',
        'to': ['de', 'it']
    },
    data: [{
        'text': 'Hello World!'
    }],
    responseType: 'json'
}).then(function(response){
    console.log(JSON.stringify(response.data, null, 4));
})
```

> [!div class="nextstepaction"]
> [Ich habe während der Übersetzung die Ausgangssprache erkannt.](#detect-source-language-without-translation) [Es ist ein Problem aufgetreten.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Nodejs&Product=Translator&Page=quickstart-translator&Section=detect-source-language-during-translation)


# <a name="python"></a>[Python](#tab/python)
```python
import requests, uuid, json

# Add your subscription key and endpoint
subscription_key = "YOUR_SUBSCRIPTION_KEY"
endpoint = "https://api.cognitive.microsofttranslator.com"

# Add your location, also known as region. The default is global.
# This is required if using a Cognitive Services resource.
location = "YOUR_RESOURCE_LOCATION"

path = '/translate'
constructed_url = endpoint + path

params = {
    'api-version': '3.0',
    'to': ['de', 'it']
}
constructed_url = endpoint + path

headers = {
    'Ocp-Apim-Subscription-Key': subscription_key,
    'Ocp-Apim-Subscription-Region': location,
    'Content-type': 'application/json',
    'X-ClientTraceId': str(uuid.uuid4())
}

# You can pass more than one object in body.
body = [{
    'text': 'Hello World!'
}]

request = requests.post(constructed_url, params=params, headers=headers, json=body)
response = request.json()

print(json.dumps(response, sort_keys=True, ensure_ascii=False, indent=4, separators=(',', ': ')))
```

> [!div class="nextstepaction"]
> [Ich habe während der Übersetzung die Ausgangssprache erkannt.](#detect-source-language-without-translation) [Es ist ein Problem aufgetreten.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Python&Product=Translator&Page=quickstart-translator&Section=detect-source-language-during-translation)


---

Nach einem erfolgreichen Aufruf sollten Sie die folgende Antwort sehen: 

```json
[
    {
        "detectedLanguage": {
            "language": "en",
            "score": 1.0
        },
        "translations": [
            {
                "text": "Hallo Welt!",
                "to": "de"
            },
            {
                "text": "Salve, mondo!",
                "to": "it"
            }
        ]
    }
]
```

### <a name="detect-source-language-without-translation"></a>Erkennen der Quellsprache ohne Übersetzung

Es ist möglich, den Textübersetzungsdienst zum Erkennen der Sprache von Quelltext zu verwenden, ohne eine Übersetzung durchzuführen. Zu diesem Zweck verwenden Sie den [`/detect`](./reference/v3-0-detect.md)-Endpunkt. 

# <a name="c"></a>[C#](#tab/csharp)

```csharp
using System;
using System.Net.Http;
using System.Text;
using System.Threading.Tasks;
using Newtonsoft.Json; // Install Newtonsoft.Json with NuGet

class Program
{
    private static readonly string subscriptionKey = "YOUR-SUBSCRIPTION-KEY";
    private static readonly string endpoint = "https://api.cognitive.microsofttranslator.com/";

    // Add your location, also known as region. The default is global.
    // This is required if using a Cognitive Services resource.
    private static readonly string location = "YOUR_RESOURCE_LOCATION";    
    
    static async Task Main(string[] args)
    {
        // Just detect language
        string route = "/detect?api-version=3.0";
        string textToLangDetect = "Ich würde wirklich gern Ihr Auto um den Block fahren ein paar Mal.";
        object[] body = new object[] { new { Text = textToLangDetect } };
        var requestBody = JsonConvert.SerializeObject(body);
    
        using (var client = new HttpClient())
        using (var request = new HttpRequestMessage())
        {
            // Build the request.
            request.Method = HttpMethod.Post;
            request.RequestUri = new Uri(endpoint + route);
            request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");
            request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
            request.Headers.Add("Ocp-Apim-Subscription-Region", location);
    
            // Send the request and get response.
            HttpResponseMessage response = await client.SendAsync(request).ConfigureAwait(false);
            // Read response as a string.
            string result = await response.Content.ReadAsStringAsync();
            Console.WriteLine(result);
        }
    }
}
```

> [!div class="nextstepaction"]
> [Ich habe ohne Übersetzung Ausgangssprachen erkannt.](#transliterate-text) [Es ist ein Problem aufgetreten.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Csharp&Product=Translator&Page=quickstart-translator&Section=detect-source-language-without-translation)


# <a name="go"></a>[Go](#tab/go)

```go
package main

import (
    "bytes"
    "encoding/json"
    "fmt"
    "log"
    "net/http"
    "net/url"
)

func main() {
    subscriptionKey := "YOUR-SUBSCRIPTION-KEY"
    // Add your location, also known as region. The default is global.
    // This is required if using a Cognitive Services resource.
    location := "YOUR_RESOURCE_LOCATION";   

    endpoint := "https://api.cognitive.microsofttranslator.com/"
    uri := endpoint + "/detect?api-version=3.0"

    // Build the request URL. See: https://golang.org/pkg/net/url/#example_URL_Parse
    u, _ := url.Parse(uri)
    q := u.Query()
    u.RawQuery = q.Encode()

    // Create an anonymous struct for your request body and encode it to JSON
    body := []struct {
        Text string
    }{
        {Text: "Ich würde wirklich gern Ihr Auto um den Block fahren ein paar Mal."},
    }
    b, _ := json.Marshal(body)

    // Build the HTTP POST request
    req, err := http.NewRequest("POST", u.String(), bytes.NewBuffer(b))
    if err != nil {
        log.Fatal(err)
    }
    // Add required headers to the request
    req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)
    req.Header.Add("Ocp-Apim-Subscription-Region", location)
    req.Header.Add("Content-Type", "application/json")

    // Call the Translator API
    res, err := http.DefaultClient.Do(req)
    if err != nil {
        log.Fatal(err)
    }

    // Decode the JSON response
    var result interface{}
    if err := json.NewDecoder(res.Body).Decode(&result); err != nil {
         log.Fatal(err)
    }
    // Format and print the response to terminal
    prettyJSON, _ := json.MarshalIndent(result, "", "  ")
    fmt.Printf("%s\n", prettyJSON)
}
```

> [!div class="nextstepaction"]
> [Ich habe ohne Übersetzung Ausgangssprachen erkannt.](#transliterate-text) [Es ist ein Problem aufgetreten.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Product=Translator&Page=quickstart-translator&Section=detect-source-language-without-translation)

# <a name="java"></a>[Java](#tab/java)

```java
import java.io.*;
import java.net.*;
import java.util.*;
import com.google.gson.*;
import com.squareup.okhttp.*;

public class Detect {
    private static String subscriptionKey = "YOUR_SUBSCRIPTION_KEY";

    // Add your location, also known as region. The default is global.
    // This is required if using a Cognitive Services resource.
    private static String location = "YOUR_RESOURCE_LOCATION";

    HttpUrl url = new HttpUrl.Builder()
        .scheme("https")
        .host("api.cognitive.microsofttranslator.com")
        .addPathSegment("/detect")
        .addQueryParameter("api-version", "3.0")
        .build();

    // Instantiates the OkHttpClient.
    OkHttpClient client = new OkHttpClient();

    // This function performs a POST request.
    public String Post() throws IOException {
        MediaType mediaType = MediaType.parse("application/json");
        RequestBody body = RequestBody.create(mediaType,
                "[{\"Text\": \"Ich würde wirklich gern Ihr Auto um den Block fahren ein paar Mal.\"}]");
        Request request = new Request.Builder().url(url).post(body)
                .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey)
                .addHeader("Ocp-Apim-Subscription-Region", location)
                .addHeader("Content-type", "application/json")
                .build();
        Response response = client.newCall(request).execute();
        return response.body().string();
    }

    // This function prettifies the json response.
    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonElement json = parser.parse(json_text);
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }

    public static void main(String[] args) {
        try {
            Detect detectRequest = new Detect();
            String response = detectRequest.Post();
            System.out.println(prettify(response));
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}
```

> [!div class="nextstepaction"]
> [Ich habe ohne Übersetzung Ausgangssprachen erkannt.](#transliterate-text) [Es ist ein Problem aufgetreten.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Java&Product=Translator&Page=quickstart-translator&Section=detect-source-language-without-translation)

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

```javascript
const axios = require('axios').default;
const { v4: uuidv4 } = require('uuid');

var subscriptionKey = "YOUR_SUBSCRIPTION_KEY";
var endpoint = "https://api.cognitive.microsofttranslator.com";

// Add your location, also known as region. The default is global.
// This is required if using a Cognitive Services resource.
var location = "YOUR_RESOURCE_LOCATION";

axios({
    baseURL: endpoint,
    url: '/detect',
    method: 'post',
    headers: {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
        'Ocp-Apim-Subscription-Region': location,
        'Content-type': 'application/json',
        'X-ClientTraceId': uuidv4().toString()
    },
    params: {
        'api-version': '3.0'
    },
    data: [{
        'text': 'Ich würde wirklich gern Ihr Auto um den Block fahren ein paar Mal.'
    }],
    responseType: 'json'
}).then(function(response){
    console.log(JSON.stringify(response.data, null, 4));
})
```

> [!div class="nextstepaction"]
> [Ich habe ohne Übersetzung Ausgangssprachen erkannt.](#transliterate-text) [Es ist ein Problem aufgetreten.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Nodejs&Product=Translator&Page=quickstart-translator&Section=detect-source-language-without-translation)

# <a name="python"></a>[Python](#tab/python)
```python
import requests, uuid, json

# Add your subscription key and endpoint
subscription_key = "YOUR_SUBSCRIPTION_KEY"
endpoint = "https://api.cognitive.microsofttranslator.com"

# Add your location, also known as region. The default is global.
# This is required if using a Cognitive Services resource.
location = "YOUR_RESOURCE_LOCATION"

path = '/detect'
constructed_url = endpoint + path

params = {
    'api-version': '3.0'
}
constructed_url = endpoint + path

headers = {
    'Ocp-Apim-Subscription-Key': subscription_key,
    'Ocp-Apim-Subscription-Region': location,
    'Content-type': 'application/json',
    'X-ClientTraceId': str(uuid.uuid4())
}

# You can pass more than one object in body.
body = [{
    'text': 'Ich würde wirklich gern Ihr Auto um den Block fahren ein paar Mal.'
}]

request = requests.post(constructed_url, params=params, headers=headers, json=body)
response = request.json()

print(json.dumps(response, sort_keys=True, ensure_ascii=False, indent=4, separators=(',', ': ')))
```

> [!div class="nextstepaction"]
> [Ich habe ohne Übersetzung Ausgangssprachen erkannt.](#transliterate-text) [Es ist ein Problem aufgetreten.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Python&Product=Translator&Page=quickstart-translator&Section=detect-source-language-without-translation)

---

Beim Verwenden des `/detect`-Endpunkts enthält die Antwort alternative erkannte Sprachen und informiert Sie darüber, ob Übersetzung und Transliteration für alle erkannten Sprachen unterstützt werden. Nach einem erfolgreichen Aufruf sollten Sie die folgende Antwort sehen: 

```json
[

    {

        "language": "de",

        "score": 1.0,

        "isTranslationSupported": true,

        "isTransliterationSupported": false

    }

]
```

## <a name="transliterate-text"></a>Transliteration von Text 

Transliteration ist der Vorgang der Umwandlung eines Worts oder Ausdrucks aus der Schrift (Alphabet) einer Sprache in eine andere, ausgehend von der phonetischen Ähnlichkeit. Beispielsweise können Sie Transliteration verwenden, um „สวัสดี“ (`thai`) in „sawatdi“ (`latn`) zu konvertieren. Es gibt mehrere Möglichkeiten zum Durchführen von Transliteration. In diesem Abschnitt erfahren Sie, wie Sie die Sprachenerkennung mithilfe des `translate`-Endpunkts und des `transliterate`-Endpunkts verwenden. 

### <a name="transliterate-during-translation"></a>Transliteration während der Übersetzung

Wenn Sie in eine Sprache übersetzen, die ein anderes Alphabet (oder andere Phoneme) als ihre Quelle verwendet, benötigen Sie möglicherweise Transliteration. In diesem Beispiel wird „Hello“ von Englisch in Thailändisch übersetzt. Über das Ergebnis einer Übersetzung ins Thailändische hinaus erhalten Sie eine Transliteration des übersetzten Satzes, die das lateinische Alphabet verwendet.

Zum Abrufen einer Übersetzung vom `translate`-Endpunkt verwenden Sie den Parameter `toScript`.

> [!NOTE]
> Eine vollständige Liste der verfügbaren Sprachen und Transliterationen finden Sie unter [Sprachunterstützung](language-support.md).

# <a name="c"></a>[C#](#tab/csharp)

```csharp
using System;
using System.Net.Http;
using System.Text;
using System.Threading.Tasks;
using Newtonsoft.Json; // Install Newtonsoft.Json with NuGet

class Program
{
    private static readonly string subscriptionKey = "YOUR-SUBSCRIPTION-KEY";
    private static readonly string endpoint = "https://api.cognitive.microsofttranslator.com/";

    // Add your location, also known as region. The default is global.
    // This is required if using a Cognitive Services resource.
    private static readonly string location = "YOUR_RESOURCE_LOCATION";    
    
    static async Task Main(string[] args)
    {
        // Output language defined as parameter, with toScript set to latn
        string route = "/translate?api-version=3.0&to=th&toScript=latn";
        string textToTransliterate = "Hello";
        object[] body = new object[] { new { Text = textToTransliterate } };
        var requestBody = JsonConvert.SerializeObject(body);
    
        using (var client = new HttpClient())
        using (var request = new HttpRequestMessage())
        {
            // Build the request.
            request.Method = HttpMethod.Post;
            request.RequestUri = new Uri(endpoint + route);
            request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");
            request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
            request.Headers.Add("Ocp-Apim-Subscription-Region", location);
    
            // Send the request and get response.
            HttpResponseMessage response = await client.SendAsync(request).ConfigureAwait(false);
            // Read response as a string.
            string result = await response.Content.ReadAsStringAsync();
            Console.WriteLine(result);
        }
    }
}
```

> [!div class="nextstepaction"]
> [Ich habe Text während der Übersetzung transliteriert.](#transliterate-without-translation) [Es ist ein Problem aufgetreten.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Csharp&Product=Translator&Page=quickstart-translator&Section=transliterate-during-translation)

# <a name="go"></a>[Go](#tab/go)

```go
package main

import (
    "bytes"
    "encoding/json"
    "fmt"
    "log"
    "net/http"
    "net/url"
)

func main() {
    subscriptionKey := "YOUR-SUBSCRIPTION-KEY"
    // Add your location, also known as region. The default is global.
    // This is required if using a Cognitive Services resource.
    location := "YOUR_RESOURCE_LOCATION";   
    endpoint := "https://api.cognitive.microsofttranslator.com/"
    uri := endpoint + "/translate?api-version=3.0"

    // Build the request URL. See: https://golang.org/pkg/net/url/#example_URL_Parse
    u, _ := url.Parse(uri)
    q := u.Query()
    q.Add("to", "th")
    q.Add("toScript", "latn")
    u.RawQuery = q.Encode()

    // Create an anonymous struct for your request body and encode it to JSON
    body := []struct {
        Text string
    }{
        {Text: "Hello"},
    }
    b, _ := json.Marshal(body)

    // Build the HTTP POST request
    req, err := http.NewRequest("POST", u.String(), bytes.NewBuffer(b))
    if err != nil {
        log.Fatal(err)
    }
    // Add required headers to the request
    req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)
    req.Header.Add("Ocp-Apim-Subscription-Region", location)
    req.Header.Add("Content-Type", "application/json")

    // Call the Translator API
    res, err := http.DefaultClient.Do(req)
    if err != nil {
        log.Fatal(err)
    }

    // Decode the JSON response
    var result interface{}
    if err := json.NewDecoder(res.Body).Decode(&result); err != nil {
        log.Fatal(err)
    }
    // Format and print the response to terminal
    prettyJSON, _ := json.MarshalIndent(result, "", "  ")
    fmt.Printf("%s\n", prettyJSON)
}
```

> [!div class="nextstepaction"]
> [Ich habe Text während der Übersetzung transliteriert.](#transliterate-without-translation) [Es ist ein Problem aufgetreten.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Product=Translator&Page=quickstart-translator&Section=transliterate-during-translation)

# <a name="java"></a>[Java](#tab/java)

```java
import java.io.*;
import java.net.*;
import java.util.*;
import com.google.gson.*;
import com.squareup.okhttp.*;

public class Translate {
    private static String subscriptionKey = "YOUR_SUBSCRIPTION_KEY";

    // Add your location, also known as region. The default is global.
    // This is required if using a Cognitive Services resource.
    private static String location = "YOUR_RESOURCE_LOCATION";

    HttpUrl url = new HttpUrl.Builder()
        .scheme("https")
        .host("api.cognitive.microsofttranslator.com")
        .addPathSegment("/translate")
        .addQueryParameter("api-version", "3.0")
        .addQueryParameter("to", "th")
        .addQueryParameter("toScript", "latn")
        .build();

    // Instantiates the OkHttpClient.
    OkHttpClient client = new OkHttpClient();

    // This function performs a POST request.
    public String Post() throws IOException {
        MediaType mediaType = MediaType.parse("application/json");
        RequestBody body = RequestBody.create(mediaType,
                "[{\"Text\": \"Hello\"}]");
        Request request = new Request.Builder().url(url).post(body)
                .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey)
                .addHeader("Ocp-Apim-Subscription-Region", location)
                .addHeader("Content-type", "application/json")
                .build();
        Response response = client.newCall(request).execute();
        return response.body().string();
    }

    // This function prettifies the json response.
    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonElement json = parser.parse(json_text);
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }

    public static void main(String[] args) {
        try {
            Translate translateRequest = new Translate();
            String response = translateRequest.Post();
            System.out.println(prettify(response));
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}
```

> [!div class="nextstepaction"]
> [Ich habe Text während der Übersetzung transliteriert.](#transliterate-without-translation) [Es ist ein Problem aufgetreten.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Java&Product=Translator&Page=quickstart-translator&Section=transliterate-during-translation)

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

```javascript
const axios = require('axios').default;
const { v4: uuidv4 } = require('uuid');

var subscriptionKey = "YOUR_SUBSCRIPTION_KEY";
var endpoint = "https://api.cognitive.microsofttranslator.com";

// Add your location, also known as region. The default is global.
// This is required if using a Cognitive Services resource.
var location = "YOUR_RESOURCE_LOCATION";

axios({
    baseURL: endpoint,
    url: '/translate',
    method: 'post',
    headers: {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
        'Ocp-Apim-Subscription-Region': location,
        'Content-type': 'application/json',
        'X-ClientTraceId': uuidv4().toString()
    },
    params: {
        'api-version': '3.0',
        'to': 'th',
        'toScript': 'latn'
    },
    data: [{
        'text': 'Hello'
    }],
    responseType: 'json'
}).then(function(response){
    console.log(JSON.stringify(response.data, null, 4));
})
```

> [!div class="nextstepaction"]
> [Ich habe Text während der Übersetzung transliteriert.](#transliterate-without-translation) [Es ist ein Problem aufgetreten.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Nodejs&Product=Translator&Page=quickstart-translator&Section=transliterate-during-translation)

# <a name="python"></a>[Python](#tab/python)
```Python
import requests, uuid, json

# Add your subscription key and endpoint
subscription_key = "YOUR_SUBSCRIPTION_KEY"
endpoint = "https://api.cognitive.microsofttranslator.com"

# Add your location, also known as region. The default is global.
# This is required if using a Cognitive Services resource.
location = "YOUR_RESOURCE_LOCATION"

path = '/translate'
constructed_url = endpoint + path

params = {
    'api-version': '3.0',
    'to': 'th',
    'toScript': 'latn'
}
constructed_url = endpoint + path

headers = {
    'Ocp-Apim-Subscription-Key': subscription_key,
    'Ocp-Apim-Subscription-Region': location,
    'Content-type': 'application/json',
    'X-ClientTraceId': str(uuid.uuid4())
}

# You can pass more than one object in body.
body = [{
    'text': 'Hello'
}]
request = requests.post(constructed_url, params=params, headers=headers, json=body)
response = request.json()

print(json.dumps(response, sort_keys=True, ensure_ascii=False, indent=4, separators=(',', ': ')))
```

> [!div class="nextstepaction"]
> [Ich habe Text während der Übersetzung transliteriert.](#transliterate-without-translation) [Es ist ein Problem aufgetreten.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Python&Product=Translator&Page=quickstart-translator&Section=transliterate-during-translation)

---

Nach einem erfolgreichen Aufruf sollten Sie die folgende Antwort sehen. Beachten Sie, dass die Antwort vom `translate`-Endpunkt die erkannte Ausgangssprache mit einer Vertrauensbewertung, eine Übersetzung im Alphabet der Zielsprache und eine Transliteration unter Verwendung des lateinischen Alphabets umfasst.

```json
[
    {
        "detectedLanguage": {
            "language": "en",
            "score": 1.0
        },
        "translations": [
            {
                "text": "สวัสดี",
                "to": "th",
                "transliteration": {
                    "script": "Latn",
                    "text": "sawatdi"
                }
            }
        ]
    }
]
```

### <a name="transliterate-without-translation"></a>Transliteration ohne Übersetzung

Sie können ferner den `transliterate`-Endpunkt verwenden, um eine Transliteration abzurufen. Beim Verwenden des Transliterationsendpunkts müssen Sie die Ausgangssprache (`language`), die Ausgangsschrift/das Ausgangsalphabet (`fromScript`) und die Zielschrift/das Zielalphabet (`toScript`) als Parameter angeben. In diesem Beispiel rufen wir die Transliteration für สวัสดี ab. 

> [!NOTE]
> Eine vollständige Liste der verfügbaren Sprachen und Transliterationen finden Sie unter [Sprachunterstützung](language-support.md).

# <a name="c"></a>[C#](#tab/csharp)

```csharp
using System;
using System.Net.Http;
using System.Text;
using System.Threading.Tasks;
using Newtonsoft.Json; // Install Newtonsoft.Json with NuGet

class Program
{
    private static readonly string subscriptionKey = "YOUR-SUBSCRIPTION-KEY";
    private static readonly string endpoint = "https://api.cognitive.microsofttranslator.com/";

    // Add your location, also known as region. The default is global.
    // This is required if using a Cognitive Services resource.
    private static readonly string location = "YOUR_RESOURCE_LOCATION";   
    
    static async Task Main(string[] args)
    {
        // For a complete list of options, see API reference.
        // Input and output languages are defined as parameters.
        string route = "/translate?api-version=3.0&to=th&toScript=latn";
        string textToTransliterate = "Hello";
        object[] body = new object[] { new { Text = textToTransliterate } };
        var requestBody = JsonConvert.SerializeObject(body);
    
        using (var client = new HttpClient())
        using (var request = new HttpRequestMessage())
        {
            // Build the request.
            request.Method = HttpMethod.Post;
            request.RequestUri = new Uri(endpoint + route);
            request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");
            request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
            request.Headers.Add("Ocp-Apim-Subscription-Region", location);
    
            // Send the request and get response.
            HttpResponseMessage response = await client.SendAsync(request).ConfigureAwait(false);
            // Read response as a string.
            string result = await response.Content.ReadAsStringAsync();
            Console.WriteLine(result);
        }
    }
}
```

> [!div class="nextstepaction"]
> [Ich habe ohne Übersetzung Text transliteriert.](#get-sentence-length) [Es ist ein Problem aufgetreten.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Csharp&Product=Translator&Page=quickstart-translator&Section=transliterate-without-translation)

# <a name="go"></a>[Go](#tab/go)

```go
package main

import (
    "bytes"
    "encoding/json"
    "fmt"
    "log"
    "net/http"
    "net/url"
)

func main() {
    subscriptionKey := "YOUR-SUBSCRIPTION-KEY"
    // Add your location, also known as region. The default is global.
    // This is required if using a Cognitive Services resource.
    location := "YOUR_RESOURCE_LOCATION";
    endpoint := "https://api.cognitive.microsofttranslator.com/"
    uri := endpoint + "/transliterate?api-version=3.0"

    // Build the request URL. See: https://golang.org/pkg/net/url/#example_URL_Parse
    u, _ := url.Parse(uri)
    q := u.Query()
    q.Add("language", "th")
    q.Add("fromScript", "thai")
    q.Add("toScript", "latn")
    u.RawQuery = q.Encode()

    // Create an anonymous struct for your request body and encode it to JSON
    body := []struct {
        Text string
    }{
        {Text: "สวัสดี"},
    }
    b, _ := json.Marshal(body)

    // Build the HTTP POST request
    req, err := http.NewRequest("POST", u.String(), bytes.NewBuffer(b))
    if err != nil {
        log.Fatal(err)
    }
    // Add required headers to the request
    req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)
    req.Header.Add("Ocp-Apim-Subscription-Region", location)
    req.Header.Add("Content-Type", "application/json")

    // Call the Translator API
    res, err := http.DefaultClient.Do(req)
    if err != nil {
        log.Fatal(err)
    }

    // Decode the JSON response
    var result interface{}
    if err := json.NewDecoder(res.Body).Decode(&result); err != nil {
        log.Fatal(err)
    }
    // Format and print the response to terminal
    prettyJSON, _ := json.MarshalIndent(result, "", "  ")
    fmt.Printf("%s\n", prettyJSON)
}
```

> [!div class="nextstepaction"]
> [Ich habe ohne Übersetzung Text transliteriert.](#get-sentence-length) [Es ist ein Problem aufgetreten.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Product=Translator&Page=quickstart-translator&Section=transliterate-without-translation)

# <a name="java"></a>[Java](#tab/java)

```java
import java.io.*;
import java.net.*;
import java.util.*;
import com.google.gson.*;
import com.squareup.okhttp.*;

public class Transliterate {
    private static String subscriptionKey = "YOUR_SUBSCRIPTION_KEY";

    // Add your location, also known as region. The default is global.
    // This is required if using a Cognitive Services resource.
    private static String location = "YOUR_RESOURCE_LOCATION";

    HttpUrl url = new HttpUrl.Builder()
        .scheme("https")
        .host("api.cognitive.microsofttranslator.com")
        .addPathSegment("/transliterate")
            .addQueryParameter("api-version", "3.0")
            .addQueryParameter("language", "th")
            .addQueryParameter("fromScript", "thai")
            .addQueryParameter("toScript", "latn")
        .build();

    // Instantiates the OkHttpClient.
    OkHttpClient client = new OkHttpClient();

    // This function performs a POST request.
    public String Post() throws IOException {
        MediaType mediaType = MediaType.parse("application/json");
        RequestBody body = RequestBody.create(mediaType,
                "[{\"Text\": \"สวัสดี\"}]");
        Request request = new Request.Builder().url(url).post(body)
                .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey)
                .addHeader("Ocp-Apim-Subscription-Region", location)
                .addHeader("Content-type", "application/json")
                .build();
        Response response = client.newCall(request).execute();
        return response.body().string();
    }

    // This function prettifies the json response.
    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonElement json = parser.parse(json_text);
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }

    public static void main(String[] args) {
        try {
            Transliterate transliterateRequest = new Transliterate();
            String response = transliterateRequest.Post();
            System.out.println(prettify(response));
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}
```

> [!div class="nextstepaction"]
> [Ich habe ohne Übersetzung Text transliteriert.](#get-sentence-length) [Es ist ein Problem aufgetreten.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Java&Product=Translator&Page=quickstart-translator&Section=transliterate-without-translation)

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

```javascript
const axios = require('axios').default;
const { v4: uuidv4 } = require('uuid');

var subscriptionKey = "YOUR_SUBSCRIPTION_KEY";
var endpoint = "https://api.cognitive.microsofttranslator.com";

// Add your location, also known as region. The default is global.
// This is required if using a Cognitive Services resource.
var location = "YOUR_RESOURCE_LOCATION";

axios({
    baseURL: endpoint,
    url: '/transliterate',
    method: 'post',
    headers: {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
        'Ocp-Apim-Subscription-Region': location,
        'Content-type': 'application/json',
        'X-ClientTraceId': uuidv4().toString()
    },
    params: {
        'api-version': '3.0',
        'language': 'th',
        'fromScript': 'thai',
        'toScript': 'latn'
    },
    data: [{
        'text': 'สวัสดี'
    }],
    responseType: 'json'
}).then(function(response){
    console.log(JSON.stringify(response.data, null, 4));
})
```

> [!div class="nextstepaction"]
> [Ich habe ohne Übersetzung Text transliteriert.](#get-sentence-length) [Es ist ein Problem aufgetreten.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Nodejs&Product=Translator&Page=quickstart-translator&Section=transliterate-without-translation)

# <a name="python"></a>[Python](#tab/python)
```python
import requests, uuid, json

# Add your subscription key and endpoint
subscription_key = "YOUR_SUBSCRIPTION_KEY"
endpoint = "https://api.cognitive.microsofttranslator.com"

# Add your location, also known as region. The default is global.
# This is required if using a Cognitive Services resource.
location = "YOUR_RESOURCE_LOCATION"

path = '/transliterate'
constructed_url = endpoint + path

params = {
    'api-version': '3.0',
    'language': 'th',
    'fromScript': 'thai',
    'toScript': 'latn'
}
constructed_url = endpoint + path

headers = {
    'Ocp-Apim-Subscription-Key': subscription_key,
    'Ocp-Apim-Subscription-Region': location,
    'Content-type': 'application/json',
    'X-ClientTraceId': str(uuid.uuid4())
}

# You can pass more than one object in body.
body = [{
    'text': 'สวัสดี'
}]

request = requests.post(constructed_url, params=params, headers=headers, json=body)
response = request.json()

print(json.dumps(response, sort_keys=True, indent=4, separators=(',', ': ')))
```

> [!div class="nextstepaction"]
> [Ich habe ohne Übersetzung Text transliteriert.](#get-sentence-length) [Es ist ein Problem aufgetreten.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Python&Product=Translator&Page=quickstart-translator&Section=transliterate-without-translation)

---

Nach einem erfolgreichen Aufruf sollten Sie die folgende Antwort sehen. Im Gegensatz zu einem Aufruf des `translate`-Endpunkts gibt `transliterate` nur die `script` und den Ausgabe-`text` zurück. 

```json
[
    {
        "script": "latn",
        "text": "sawatdi"
    }
]
```

## <a name="get-sentence-length"></a>Abrufen der Satzlänge

Mit dem Textübersetzungsdienst können Sie die Zeichenanzahl für einen Satz oder eine Reihe von Sätzen bestimmen. Die Antwort wird als Array zurückgegeben, das die Zeichenanzahl für jeden erkannten Satz enthält. Sie können Satzlängen mit den Endpunkten `translate` und `breaksentence` abrufen.

### <a name="get-sentence-length-during-translation"></a>Abrufen der Satzlänge während der Übersetzung

Sie können die Zeichenanzahl sowohl für den Ausgangstext als auch für die übersetzte Ausgabe mithilfe des `translate`-Endpunkts abrufen. Um die Satzlänge (`srcSenLen` und `transSenLen`) zurückzugeben, müssen Sie den `includeSentenceLength`-Parameter auf `True` festlegen.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
using System;
using System.Net.Http;
using System.Text;
using System.Threading.Tasks;
using Newtonsoft.Json; // Install Newtonsoft.Json with NuGet

class Program
{
    private static readonly string subscriptionKey = "YOUR-SUBSCRIPTION-KEY";
    private static readonly string endpoint = "https://api.cognitive.microsofttranslator.com/";

    // Add your location, also known as region. The default is global.
    // This is required if using a Cognitive Services resource.
    private static readonly string location = "YOUR_RESOURCE_LOCATION";   
    
    static async Task Main(string[] args)
    {
        // Include sentence length details.
        string route = "/translate?api-version=3.0&to=es&includeSentenceLength=true";
        string sentencesToCount = 
                "Can you tell me how to get to Penn Station? Oh, you aren't sure? That's fine.";
        object[] body = new object[] { new { Text = sentencesToCount } };
        var requestBody = JsonConvert.SerializeObject(body);
    
        using (var client = new HttpClient())
        using (var request = new HttpRequestMessage())
        {
            // Build the request.
            request.Method = HttpMethod.Post;
            request.RequestUri = new Uri(endpoint + route);
            request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");
            request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
            request.Headers.Add("Ocp-Apim-Subscription-Region", location);
    
            // Send the request and get response.
            HttpResponseMessage response = await client.SendAsync(request).ConfigureAwait(false);
            // Read response as a string.
            string result = await response.Content.ReadAsStringAsync();
            Console.WriteLine(result);
        }
    }
}
```

> [!div class="nextstepaction"]
> [Ich habe die Satzlänge während der Übersetzung abgerufen.](#get-sentence-length-without-translation) [Es ist ein Problem aufgetreten.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Csharp&Product=Translator&Page=quickstart-translator&Section=get-sentence-length-during-translation)

# <a name="go"></a>[Go](#tab/go)

```go
package main

import (
    "bytes"
    "encoding/json"
    "fmt"
    "log"
    "net/http"
    "net/url"
)

func main() {
    subscriptionKey := "YOUR-SUBSCRIPTION-KEY"
    // Add your location, also known as region. The default is global.
    // This is required if using a Cognitive Services resource.
    location := "YOUR_RESOURCE_LOCATION";
    endpoint := "https://api.cognitive.microsofttranslator.com/"
    uri := endpoint + "/translate?api-version=3.0"

    // Build the request URL. See: https://golang.org/pkg/net/url/#example_URL_Parse
    u, _ := url.Parse(uri)
    q := u.Query()
    q.Add("to", "es")
    q.Add("includeSentenceLength", "true")
    u.RawQuery = q.Encode()

    // Create an anonymous struct for your request body and encode it to JSON
    body := []struct {
        Text string
    }{
        {Text: "Can you tell me how to get to Penn Station? Oh, you aren't sure? That's fine."},
    }
    b, _ := json.Marshal(body)

    // Build the HTTP POST request
    req, err := http.NewRequest("POST", u.String(), bytes.NewBuffer(b))
    if err != nil {
        log.Fatal(err)
    }
    // Add required headers to the request
    req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)
    req.Header.Add("Ocp-Apim-Subscription-Region", location)
    req.Header.Add("Content-Type", "application/json")

    // Call the Translator API
    res, err := http.DefaultClient.Do(req)
    if err != nil {
        log.Fatal(err)
    }

    // Decode the JSON response
    var result interface{}
    if err := json.NewDecoder(res.Body).Decode(&result); err != nil {
        log.Fatal(err)
    }
    // Format and print the response to terminal
    prettyJSON, _ := json.MarshalIndent(result, "", "  ")
    fmt.Printf("%s\n", prettyJSON)
}
```

> [!div class="nextstepaction"]
> [Ich habe die Satzlänge während der Übersetzung abgerufen.](#get-sentence-length-without-translation) [Es ist ein Problem aufgetreten.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Product=Translator&Page=quickstart-translator&Section=get-sentence-length-during-translation)

# <a name="java"></a>[Java](#tab/java)

```java
import java.io.*;
import java.net.*;
import java.util.*;
import com.google.gson.*;
import com.squareup.okhttp.*;

public class Translate {
    private static String subscriptionKey = "YOUR_SUBSCRIPTION_KEY";

    // Add your location, also known as region. The default is global.
    // This is required if using a Cognitive Services resource.
    private static String location = "YOUR_RESOURCE_LOCATION";

    HttpUrl url = new HttpUrl.Builder()
        .scheme("https")
        .host("api.cognitive.microsofttranslator.com")
        .addPathSegment("/translate")
        .addQueryParameter("api-version", "3.0")
        .addQueryParameter("to", "es")
        .addQueryParameter("includeSentenceLength", "true")
        .build();

    // Instantiates the OkHttpClient.
    OkHttpClient client = new OkHttpClient();

    // This function performs a POST request.
    public String Post() throws IOException {
        MediaType mediaType = MediaType.parse("application/json");
        RequestBody body = RequestBody.create(mediaType,
                "[{\"Text\": \"Can you tell me how to get to Penn Station? Oh, you aren\'t sure? That\'s fine.\"}]");
        Request request = new Request.Builder().url(url).post(body)
                .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey)
                .addHeader("Ocp-Apim-Subscription-Region", location)
                .addHeader("Content-type", "application/json")
                .build();
        Response response = client.newCall(request).execute();
        return response.body().string();
    }

    // This function prettifies the json response.
    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonElement json = parser.parse(json_text);
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }

    public static void main(String[] args) {
        try {
            Translate translateRequest = new Translate();
            String response = translateRequest.Post();
            System.out.println(prettify(response));
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}
```

> [!div class="nextstepaction"]
> [Ich habe die Satzlänge während der Übersetzung abgerufen.](#get-sentence-length-without-translation) [Es ist ein Problem aufgetreten.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Java&Product=Translator&Page=quickstart-translator&Section=get-sentence-length-during-translation)

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

```javascript
const axios = require('axios').default;
const { v4: uuidv4 } = require('uuid');

var subscriptionKey = "YOUR_SUBSCRIPTION_KEY";
var endpoint = "https://api.cognitive.microsofttranslator.com";

// Add your location, also known as region. The default is global.
// This is required if using a Cognitive Services resource.
var location = "YOUR_RESOURCE_LOCATION";

axios({
    baseURL: endpoint,
    url: '/translate',
    method: 'post',
    headers: {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
        'Ocp-Apim-Subscription-Region': location,
        'Content-type': 'application/json',
        'X-ClientTraceId': uuidv4().toString()
    },
    params: {
        'api-version': '3.0',
        'to': 'es',
        'includeSentenceLength': true
    },
    data: [{
        'text': 'Can you tell me how to get to Penn Station? Oh, you aren\'t sure? That\'s fine.'
    }],
    responseType: 'json'
}).then(function(response){
    console.log(JSON.stringify(response.data, null, 4));
})
```

> [!div class="nextstepaction"]
> [Ich habe die Satzlänge während der Übersetzung abgerufen.](#get-sentence-length-without-translation) [Es ist ein Problem aufgetreten.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Nodejs&Product=Translator&Page=quickstart-translator&Section=get-sentence-length-during-translation)

# <a name="python"></a>[Python](#tab/python)
```python
import requests, uuid, json

# Add your subscription key and endpoint
subscription_key = "YOUR_SUBSCRIPTION_KEY"
endpoint = "https://api.cognitive.microsofttranslator.com"

# Add your location, also known as region. The default is global.
# This is required if using a Cognitive Services resource.
location = "YOUR_RESOURCE_LOCATION"

path = '/translate'
constructed_url = endpoint + path

params = {
    'api-version': '3.0',
    'to': 'es',
    'includeSentenceLength': True
}
constructed_url = endpoint + path

headers = {
    'Ocp-Apim-Subscription-Key': subscription_key,
    'Ocp-Apim-Subscription-Region': location,
    'Content-type': 'application/json',
    'X-ClientTraceId': str(uuid.uuid4())
}

# You can pass more than one object in body.
body = [{
    'text': 'Can you tell me how to get to Penn Station? Oh, you aren\'t sure? That\'s fine.'
}]
request = requests.post(constructed_url, params=params, headers=headers, json=body)
response = request.json()

print(json.dumps(response, sort_keys=True, ensure_ascii=False, indent=4, separators=(',', ': ')))
```

> [!div class="nextstepaction"]
> [Ich habe die Satzlänge während der Übersetzung abgerufen.](#get-sentence-length-without-translation) [Es ist ein Problem aufgetreten.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Python&Product=Translator&Page=quickstart-translator&Section=get-sentence-length-during-translation)

---

Nach einem erfolgreichen Aufruf sollten Sie die folgende Antwort sehen. Über die erkannte Ausgangssprache und die Übersetzung hinaus erhalten Sie die Zeichenanzahl für jeden erkannten Satz, sowohl für die Quelle (`srcSentLen`) als auch für die Übersetzung (`transSentLen`).

```json
[
    {
        "detectedLanguage": {
            "language": "en",
            "score": 1.0
        },
        "translations": [
            {
                "sentLen": {
                    "srcSentLen": [
                        44,
                        21,
                        12
                    ],
                    "transSentLen": [
                        48,
                        18,
                        10
                    ]
                },
                "text": "¿Puedes decirme cómo llegar a la estación Penn? ¿No estás seguro? Está bien.",
                "to": "es"
            }
        ]
    }
]
```

### <a name="get-sentence-length-without-translation"></a>Abrufen der Satzlänge ohne Übersetzung

Der Textübersetzungsdienst lässt Sie die Satzlänge auch ohne Übersetzung abrufen, wenn Sie den `breaksentence`-Endpunkt verwenden. 

# <a name="c"></a>[C#](#tab/csharp)

```csharp
using System;
using System.Net.Http;
using System.Text;
using System.Threading.Tasks;
using Newtonsoft.Json; // Install Newtonsoft.Json with NuGet

class Program
{
    private static readonly string subscriptionKey = "YOUR-SUBSCRIPTION-KEY";
    private static readonly string endpoint = "https://api.cognitive.microsofttranslator.com/";

    // Add your location, also known as region. The default is global.
    // This is required if using a Cognitive Services resource.
    private static readonly string location = "YOUR_RESOURCE_LOCATION";   
    
    static async Task Main(string[] args)
    {
        // Only include sentence length details.
        string route = "/breaksentence?api-version=3.0";
        string sentencesToCount = 
                "Can you tell me how to get to Penn Station? Oh, you aren't sure? That's fine.";
        object[] body = new object[] { new { Text = sentencesToCount } };
        var requestBody = JsonConvert.SerializeObject(body);
    
        using (var client = new HttpClient())
        using (var request = new HttpRequestMessage())
        {
            // Build the request.
            request.Method = HttpMethod.Post;
            request.RequestUri = new Uri(endpoint + route);
            request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");
            request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
            request.Headers.Add("Ocp-Apim-Subscription-Region", location);
    
            // Send the request and get response.
            HttpResponseMessage response = await client.SendAsync(request).ConfigureAwait(false);
            // Read response as a string.
            string result = await response.Content.ReadAsStringAsync();
            Console.WriteLine(result);
        }
    }
}
```

> [!div class="nextstepaction"]
> [Ich habe ohne Übersetzung die Satzlänge abgerufen.](#dictionary-lookup-alternate-translations) [Es ist ein Problem aufgetreten.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Csharp&Product=Translator&Page=quickstart-translator&Section=get-sentence-length-without-translation)

# <a name="go"></a>[Go](#tab/go)

```go
package main

import (
    "bytes"
    "encoding/json"
    "fmt"
    "log"
    "net/http"
    "net/url"
)

func main() {
    subscriptionKey := "YOUR-SUBSCRIPTION-KEY"
    // Add your location, also known as region. The default is global.
    // This is required if using a Cognitive Services resource.
    location := "YOUR_RESOURCE_LOCATION";
    endpoint := "https://api.cognitive.microsofttranslator.com/"
    uri := endpoint + "/breaksentence?api-version=3.0"

    // Build the request URL. See: https://golang.org/pkg/net/url/#example_URL_Parse
    u, _ := url.Parse(uri)
    q := u.Query()
    u.RawQuery = q.Encode()

    // Create an anonymous struct for your request body and encode it to JSON
    body := []struct {
        Text string
    }{
        {Text: "Can you tell me how to get to Penn Station? Oh, you aren't sure? That's fine."},
    }
    b, _ := json.Marshal(body)

    // Build the HTTP POST request
    req, err := http.NewRequest("POST", u.String(), bytes.NewBuffer(b))
    if err != nil {
        log.Fatal(err)
    }
    // Add required headers to the request
    req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)
    req.Header.Add("Ocp-Apim-Subscription-Region", location)
    req.Header.Add("Content-Type", "application/json")

    // Call the Translator API
    res, err := http.DefaultClient.Do(req)
    if err != nil {
        log.Fatal(err)
    }

    // Decode the JSON response
    var result interface{}
    if err := json.NewDecoder(res.Body).Decode(&result); err != nil {
        log.Fatal(err)
    }
    // Format and print the response to terminal
    prettyJSON, _ := json.MarshalIndent(result, "", "  ")
    fmt.Printf("%s\n", prettyJSON)
}
```

> [!div class="nextstepaction"]
> [Ich habe ohne Übersetzung die Satzlänge abgerufen.](#dictionary-lookup-alternate-translations) [Es ist ein Problem aufgetreten.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Product=Translator&Page=quickstart-translator&Section=get-sentence-length-without-translation)

# <a name="java"></a>[Java](#tab/java)

```java
import java.io.*;
import java.net.*;
import java.util.*;
import com.google.gson.*;
import com.squareup.okhttp.*;

public class BreakSentence {
    private static String subscriptionKey = "YOUR_SUBSCRIPTION_KEY";

    // Add your location, also known as region. The default is global.
    // This is required if using a Cognitive Services resource.
    private static String location = "YOUR_RESOURCE_LOCATION";

    HttpUrl url = new HttpUrl.Builder()
        .scheme("https")
        .host("api.cognitive.microsofttranslator.com")
        .addPathSegment("/breaksentence")
        .addQueryParameter("api-version", "3.0")
        .build();

    // Instantiates the OkHttpClient.
    OkHttpClient client = new OkHttpClient();

    // This function performs a POST request.
    public String Post() throws IOException {
        MediaType mediaType = MediaType.parse("application/json");
        RequestBody body = RequestBody.create(mediaType,
                "[{\"Text\": \"Can you tell me how to get to Penn Station? Oh, you aren\'t sure? That\'s fine.\"}]");
        Request request = new Request.Builder().url(url).post(body)
                .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey)
                .addHeader("Ocp-Apim-Subscription-Region", location)
                .addHeader("Content-type", "application/json")
                .build();
        Response response = client.newCall(request).execute();
        return response.body().string();
    }

    // This function prettifies the json response.
    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonElement json = parser.parse(json_text);
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }

    public static void main(String[] args) {
        try {
            BreakSentence breakSentenceRequest = new BreakSentence();
            String response = breakSentenceRequest.Post();
            System.out.println(prettify(response));
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}
```

> [!div class="nextstepaction"]
> [Ich habe ohne Übersetzung die Satzlänge abgerufen.](#dictionary-lookup-alternate-translations) [Es ist ein Problem aufgetreten.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Java&Product=Translator&Page=quickstart-translator&Section=get-sentence-length-without-translation)

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

```javascript
const axios = require('axios').default;
const { v4: uuidv4 } = require('uuid');

var subscriptionKey = "YOUR_SUBSCRIPTION_KEY";
var endpoint = "https://api.cognitive.microsofttranslator.com";

// Add your location, also known as region. The default is global.
// This is required if using a Cognitive Services resource.
var location = "YOUR_RESOURCE_LOCATION";

axios({
    baseURL: endpoint,
    url: '/breaksentence',
    method: 'post',
    headers: {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
        'Ocp-Apim-Subscription-Region': location,
        'Content-type': 'application/json',
        'X-ClientTraceId': uuidv4().toString()
    },
    params: {
        'api-version': '3.0'
    },
    data: [{
        'text': 'Can you tell me how to get to Penn Station? Oh, you aren\'t sure? That\'s fine.'
    }],
    responseType: 'json'
}).then(function(response){
    console.log(JSON.stringify(response.data, null, 4));
})
```

> [!div class="nextstepaction"]
> [Ich habe ohne Übersetzung die Satzlänge abgerufen.](#dictionary-lookup-alternate-translations) [Es ist ein Problem aufgetreten.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Nodejs&Product=Translator&Page=quickstart-translator&Section=get-sentence-length-without-translation)

# <a name="python"></a>[Python](#tab/python)
```python
import requests, uuid, json

# Add your subscription key and endpoint
subscription_key = "YOUR_SUBSCRIPTION_KEY"
endpoint = "https://api.cognitive.microsofttranslator.com"

# Add your location, also known as region. The default is global.
# This is required if using a Cognitive Services resource.
location = "YOUR_RESOURCE_LOCATION"

path = '/breaksentence'
constructed_url = endpoint + path

params = {
    'api-version': '3.0'
}
constructed_url = endpoint + path

headers = {
    'Ocp-Apim-Subscription-Key': subscription_key,
    'Ocp-Apim-Subscription-Region': location,
    'Content-type': 'application/json',
    'X-ClientTraceId': str(uuid.uuid4())
}

# You can pass more than one object in body.
body = [{
    'text': 'Can you tell me how to get to Penn Station? Oh, you aren\'t sure? That\'s fine.'
}]

request = requests.post(constructed_url, params=params, headers=headers, json=body)
response = request.json()

print(json.dumps(response, sort_keys=True, indent=4, separators=(',', ': ')))
```

> [!div class="nextstepaction"]
> [Ich habe ohne Übersetzung die Satzlänge abgerufen.](#dictionary-lookup-alternate-translations) [Es ist ein Problem aufgetreten.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Python&Product=Translator&Page=quickstart-translator&Section=get-sentence-length-without-translation)

---

Nach einem erfolgreichen Aufruf sollten Sie die folgende Antwort sehen. Anders als ein Aufruf des `translate`-Endpunkts gibt `breaksentence` nur die Zeichenanzahl für den Ausgangstext in einem Array mit dem Namen `sentLen` zurück. 

```json
[
    {
        "detectedLanguage": {
            "language": "en",
            "score": 1.0
        },
        "sentLen": [
            44,
            21,
            12
        ]
    }
]
```

## <a name="dictionary-lookup-alternate-translations"></a>Wörterbuchsuche (Alternative Übersetzungen)

Mithilfe des  -Endpunks können Sie alternative Übersetzungen für ein Wort oder einen Satz abrufen. Wenn Sie beispielsweise das Wort „Shark“ aus `en` in `es` übersetzen, gibt dieser Endpunkt sowohl „tiburón“ als auch „escualo“ zurück.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
using System;
using System.Net.Http;
using System.Text;
using System.Threading.Tasks;
using Newtonsoft.Json; // Install Newtonsoft.Json with NuGet

class Program
{
    private static readonly string subscriptionKey = "YOUR-SUBSCRIPTION-KEY";
    private static readonly string endpoint = "https://api.cognitive.microsofttranslator.com/";

    // Add your location, also known as region. The default is global.
    // This is required if using a Cognitive Services resource.
    private static readonly string location = "YOUR_RESOURCE_LOCATION"; 
    
    static async Task Main(string[] args)
    {
        // See many translation options
        string route = "/dictionary/lookup?api-version=3.0&from=en&to=es";
        string wordToTranslate = "shark";
        object[] body = new object[] { new { Text = wordToTranslate } };
        var requestBody = JsonConvert.SerializeObject(body);
    
        using (var client = new HttpClient())
        using (var request = new HttpRequestMessage())
        {
            // Build the request.
            request.Method = HttpMethod.Post;
            request.RequestUri = new Uri(endpoint + route);
            request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");
            request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
            request.Headers.Add("Ocp-Apim-Subscription-Region", location);
    
            // Send the request and get response.
            HttpResponseMessage response = await client.SendAsync(request).ConfigureAwait(false);
            // Read response as a string.
            string result = await response.Content.ReadAsStringAsync();
            Console.WriteLine(result);
        }
    }
}
```

> [!div class="nextstepaction"]
> [Ich habe alternative Übersetzungen abgerufen.](#dictionary-examples-translations-in-context) [Es ist ein Problem aufgetreten.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Csharp&Product=Translator&Page=quickstart-translator&Section=dictionary-lookup-alternate-translations)

# <a name="go"></a>[Go](#tab/go)

```go
package main

import (
    "bytes"
    "encoding/json"
    "fmt"
    "log"
    "net/http"
    "net/url"
)

func main() {
    subscriptionKey := "YOUR-SUBSCRIPTION-KEY"
    // Add your location, also known as region. The default is global.
    // This is required if using a Cognitive Services resource.
    location := "YOUR_RESOURCE_LOCATION";
    endpoint := "https://api.cognitive.microsofttranslator.com/"
    uri := endpoint + "/dictionary/lookup?api-version=3.0"

    // Build the request URL. See: https://golang.org/pkg/net/url/#example_URL_Parse
    u, _ := url.Parse(uri)
    q := u.Query()
    q.Add("from", "en")
    q.Add("to", "es")
    u.RawQuery = q.Encode()

    // Create an anonymous struct for your request body and encode it to JSON
    body := []struct {
        Text string
    }{
        {Text: "shark"},
    }
    b, _ := json.Marshal(body)

    // Build the HTTP POST request
    req, err := http.NewRequest("POST", u.String(), bytes.NewBuffer(b))
    if err != nil {
        log.Fatal(err)
    }
    // Add required headers to the request
    req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)
    req.Header.Add("Ocp-Apim-Subscription-Region", location)
    req.Header.Add("Content-Type", "application/json")

    // Call the Translator API
    res, err := http.DefaultClient.Do(req)
    if err != nil {
        log.Fatal(err)
    }

    // Decode the JSON response
    var result interface{}
    if err := json.NewDecoder(res.Body).Decode(&result); err != nil {
        log.Fatal(err)
    }
    // Format and print the response to terminal
    prettyJSON, _ := json.MarshalIndent(result, "", "  ")
    fmt.Printf("%s\n", prettyJSON)
}
```

> [!div class="nextstepaction"]
> [Ich habe alternative Übersetzungen abgerufen.](#dictionary-examples-translations-in-context) [Es ist ein Problem aufgetreten.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Product=Translator&Page=quickstart-translator&Section=dictionary-lookup-alternate-translations)

# <a name="java"></a>[Java](#tab/java)

```java
import java.io.*;
import java.net.*;
import java.util.*;
import com.google.gson.*;
import com.squareup.okhttp.*;

public class DictionaryLookup {
    private static String subscriptionKey = "YOUR_SUBSCRIPTION_KEY";

    // Add your location, also known as region. The default is global.
    // This is required if using a Cognitive Services resource.
    private static String location = "YOUR_RESOURCE_LOCATION";

    HttpUrl url = new HttpUrl.Builder()
        .scheme("https")
        .host("api.cognitive.microsofttranslator.com")
        .addPathSegment("/dictionary/lookup")
        .addQueryParameter("api-version", "3.0")
        .addQueryParameter("from", "en")
        .addQueryParameter("to", "es")
        .build();

    // Instantiates the OkHttpClient.
    OkHttpClient client = new OkHttpClient();

    // This function performs a POST request.
    public String Post() throws IOException {
        MediaType mediaType = MediaType.parse("application/json");
        RequestBody body = RequestBody.create(mediaType,
                "[{\"Text\": \"Shark\"}]");
        Request request = new Request.Builder().url(url).post(body)
                .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey)
                .addHeader("Ocp-Apim-Subscription-Region", location)
                .addHeader("Content-type", "application/json")
                .build();
        Response response = client.newCall(request).execute();
        return response.body().string();
    }

    // This function prettifies the json response.
    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonElement json = parser.parse(json_text);
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }

    public static void main(String[] args) {
        try {
            DictionaryLookup dictionaryLookupRequest = new DictionaryLookup();
            String response = dictionaryLookupRequest.Post();
            System.out.println(prettify(response));
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}
```

> [!div class="nextstepaction"]
> [Ich habe alternative Übersetzungen abgerufen.](#dictionary-examples-translations-in-context) [Es ist ein Problem aufgetreten.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Java&Product=Translator&Page=quickstart-translator&Section=dictionary-lookup-alternate-translations)

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

```javascript
const axios = require('axios').default;
const { v4: uuidv4 } = require('uuid');

var subscriptionKey = "YOUR_SUBSCRIPTION_KEY";
var endpoint = "https://api.cognitive.microsofttranslator.com";

// Add your location, also known as region. The default is global.
// This is required if using a Cognitive Services resource.
var location = "YOUR_RESOURCE_LOCATION";

axios({
    baseURL: endpoint,
    url: '/dictionary/lookup',
    method: 'post',
    headers: {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
        'Ocp-Apim-Subscription-Region': location,
        'Content-type': 'application/json',
        'X-ClientTraceId': uuidv4().toString()
    },
    params: {
        'api-version': '3.0',
        'from': 'en',
        'to': 'es'
    },
    data: [{
        'text': 'shark'
    }],
    responseType: 'json'
}).then(function(response){
    console.log(JSON.stringify(response.data, null, 4));
})
```

> [!div class="nextstepaction"]
> [Ich habe alternative Übersetzungen abgerufen.](#dictionary-examples-translations-in-context) [Es ist ein Problem aufgetreten.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Nodejs&Product=Translator&Page=quickstart-translator&Section=dictionary-lookup-alternate-translations)

# <a name="python"></a>[Python](#tab/python)
```python
import requests, uuid, json

# Add your subscription key and endpoint
subscription_key = "YOUR_SUBSCRIPTION_KEY"
endpoint = "https://api.cognitive.microsofttranslator.com"

# Add your location, also known as region. The default is global.
# This is required if using a Cognitive Services resource.
location = "YOUR_RESOURCE_LOCATION"

path = '/dictionary/lookup'
constructed_url = endpoint + path

params = {
    'api-version': '3.0',
    'from': 'en',
    'to': 'es'
}
constructed_url = endpoint + path

headers = {
    'Ocp-Apim-Subscription-Key': subscription_key,
    'Ocp-Apim-Subscription-Region': location,
    'Content-type': 'application/json',
    'X-ClientTraceId': str(uuid.uuid4())
}

# You can pass more than one object in body.
body = [{
    'text': 'shark'
}]
request = requests.post(constructed_url, params=params, headers=headers, json=body)
response = request.json()

print(json.dumps(response, sort_keys=True, ensure_ascii=False, indent=4, separators=(',', ': ')))
```

> [!div class="nextstepaction"]
> [Ich habe alternative Übersetzungen abgerufen.](#dictionary-examples-translations-in-context) [Es ist ein Problem aufgetreten.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Python&Product=Translator&Page=quickstart-translator&Section=dictionary-lookup-alternate-translations)

---

Nach einem erfolgreichen Aufruf sollten Sie die folgende Antwort sehen. Wir möchten dies aufschlüsseln, da der JSON-Code komplexer als viele der anderen Beispiele in diesem Artikel ist. Das Array `translations` enthält eine Liste mit Übersetzungen. Jedes Objekt in diesem Array umfasst eine Vertrauensbewertung (`confidence`), den für die Anzeige auf dem Endbenutzerdisplay optimierten Text (`displayTarget`), den normalisierten Text (`normalizedText`), den Teil der Sprache (`posTag`) und Informationen über frühere Übersetzungen (`backTranslations`). Weitere Informationen über die Antwort finden Sie unter [Wörterbuchsuche](reference/v3-0-dictionary-lookup.md)

```json
[
    {
        "displaySource": "shark",
        "normalizedSource": "shark",
        "translations": [
            {
                "backTranslations": [
                    {
                        "displayText": "shark",
                        "frequencyCount": 45,
                        "normalizedText": "shark",
                        "numExamples": 0
                    }
                ],
                "confidence": 0.8182,
                "displayTarget": "tiburón",
                "normalizedTarget": "tiburón",
                "posTag": "OTHER",
                "prefixWord": ""
            },
            {
                "backTranslations": [
                    {
                        "displayText": "shark",
                        "frequencyCount": 10,
                        "normalizedText": "shark",
                        "numExamples": 1
                    }
                ],
                "confidence": 0.1818,
                "displayTarget": "escualo",
                "normalizedTarget": "escualo",
                "posTag": "NOUN",
                "prefixWord": ""
            }
        ]
    }
]
```

## <a name="dictionary-examples-translations-in-context"></a>Wörterbuchbeispiele (Übersetzungen im Kontext)

Nachdem Sie eine Wörterbuchsuche durchgeführt haben, können Sie den Ausgangstext und die Übersetzung an den `dictionary/examples`-Endpunkt übergeben, um eine Liste mit Beispielen zu erhalten, in denen beide Begriffe im Kontext eines Satzes oder Ausdrucks gezeigt werden. Ausgehend vom vorherigen Beispiel verwenden Sie den `normalizedText` und das `normalizedTarget` aus der Antwort der Wörterbuchsuche als `text` bzw. `translation`. Die Parameter für die Ausgangssprache (`from`) und das Ausgabeziel (`to`) sind erforderlich. 

# <a name="c"></a>[C#](#tab/csharp)

```csharp
using System;
using System.Net.Http;
using System.Text;
using System.Threading.Tasks;
using Newtonsoft.Json; // Install Newtonsoft.Json with NuGet

class Program
{
    private static readonly string subscriptionKey = "YOUR-SUBSCRIPTION-KEY";
    private static readonly string endpoint = "https://api.cognitive.microsofttranslator.com/";

    // Add your location, also known as region. The default is global.
    // This is required if using a Cognitive Services resource.
    private static readonly string location = "YOUR_RESOURCE_LOCATION"; 
    
    static async Task Main(string[] args)
    {
        // See examples of terms in context
        string route = "/dictionary/examples?api-version=3.0&from=en&to=es";
        object[] body = new object[] { new { Text = "Shark",  Translation = "tiburón" } } ;
        var requestBody = JsonConvert.SerializeObject(body);
    
        using (var client = new HttpClient())
        using (var request = new HttpRequestMessage())
        {
            // Build the request.
            request.Method = HttpMethod.Post;
            request.RequestUri = new Uri(endpoint + route);
            request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");
            request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
            request.Headers.Add("Ocp-Apim-Subscription-Region", location);
    
            // Send the request and get response.
            HttpResponseMessage response = await client.SendAsync(request).ConfigureAwait(false);
            // Read response as a string.
            string result = await response.Content.ReadAsStringAsync();
            Console.WriteLine(result);
        }
    }
}
```

> [!div class="nextstepaction"]
> [Ich habe Übersetzungen im Kontext abgerufen.](#next-steps) [Es ist ein Problem aufgetreten.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Csharp&Product=Translator&Page=quickstart-translator&Section=dictionary-examples-translations-in-context)

# <a name="go"></a>[Go](#tab/go)

```go
package main

import (
    "bytes"
    "encoding/json"
    "fmt"
    "log"
    "net/http"
    "net/url"
)

func main() {
    subscriptionKey := "YOUR-SUBSCRIPTION-KEY"
    // Add your location, also known as region. The default is global.
    // This is required if using a Cognitive Services resource.
    location := "YOUR_RESOURCE_LOCATION";
    endpoint := "https://api.cognitive.microsofttranslator.com/"
    uri := endpoint + "/dictionary/examples?api-version=3.0"

    // Build the request URL. See: https://golang.org/pkg/net/url/#example_URL_Parse
    u, _ := url.Parse(uri)
    q := u.Query()
    q.Add("from", "en")
    q.Add("to", "es")
    u.RawQuery = q.Encode()

    // Create an anonymous struct for your request body and encode it to JSON
    body := []struct {
        Text        string
        Translation string
    }{
        {
            Text:        "Shark",
            Translation: "tiburón",
        },
    }
    b, _ := json.Marshal(body)

    // Build the HTTP POST request
    req, err := http.NewRequest("POST", u.String(), bytes.NewBuffer(b))
    if err != nil {
        log.Fatal(err)
    }
    // Add required headers to the request
    req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)
    req.Header.Add("Ocp-Apim-Subscription-Region", location)
    req.Header.Add("Content-Type", "application/json")

    // Call the Translator Text API
    res, err := http.DefaultClient.Do(req)
    if err != nil {
        log.Fatal(err)
    }

    // Decode the JSON response
    var result interface{}
    if err := json.NewDecoder(res.Body).Decode(&result); err != nil {
        log.Fatal(err)
    }
    // Format and print the response to terminal
    prettyJSON, _ := json.MarshalIndent(result, "", "  ")
    fmt.Printf("%s\n", prettyJSON)
}
```

> [!div class="nextstepaction"]
> [Ich habe Übersetzungen im Kontext abgerufen.](#next-steps) [Es ist ein Problem aufgetreten.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Product=Translator&Page=quickstart-translator&Section=dictionary-examples-translations-in-context)

# <a name="java"></a>[Java](#tab/java)

```java
import java.io.*;
import java.net.*;
import java.util.*;
import com.google.gson.*;
import com.squareup.okhttp.*;

public class DictionaryExamples {
    private static String subscriptionKey = "YOUR_SUBSCRIPTION_KEY";

    // Add your location, also known as region. The default is global.
    // This is required if using a Cognitive Services resource.
    private static String location = "YOUR_RESOURCE_LOCATION";

    HttpUrl url = new HttpUrl.Builder()
        .scheme("https")
        .host("api.cognitive.microsofttranslator.com")
        .addPathSegment("/dictionary/examples")
        .addQueryParameter("api-version", "3.0")
        .addQueryParameter("from", "en")
        .addQueryParameter("to", "es")
        .build();

    // Instantiates the OkHttpClient.
    OkHttpClient client = new OkHttpClient();

    // This function performs a POST request.
    public String Post() throws IOException {
        MediaType mediaType = MediaType.parse("application/json");
        RequestBody body = RequestBody.create(mediaType,
                "[{\"Text\": \"Shark\", \"Translation\": \"tiburón\"}]");
        Request request = new Request.Builder().url(url).post(body)
                .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey)
                .addHeader("Ocp-Apim-Subscription-Region", location)
                .addHeader("Content-type", "application/json")
                .build();
        Response response = client.newCall(request).execute();
        return response.body().string();
    }

    // This function prettifies the json response.
    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonElement json = parser.parse(json_text);
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }

    public static void main(String[] args) {
        try {
            DictionaryExamples dictionaryExamplesRequest = new DictionaryExamples();
            String response = dictionaryExamplesRequest.Post();
            System.out.println(prettify(response));
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}
```

> [!div class="nextstepaction"]
> [Ich habe Übersetzungen im Kontext abgerufen.](#troubleshooting) [Es ist ein Problem aufgetreten.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Java&Product=Translator&Page=quickstart-translator&Section=dictionary-examples-translations-in-context)

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

```javascript
const axios = require('axios').default;
const { v4: uuidv4 } = require('uuid');

var subscriptionKey = "YOUR_SUBSCRIPTION_KEY";
var endpoint = "https://api.cognitive.microsofttranslator.com";

// Add your location, also known as region. The default is global.
// This is required if using a Cognitive Services resource.
var location = "YOUR_RESOURCE_LOCATION";

axios({
    baseURL: endpoint,
    url: '/dictionary/examples',
    method: 'post',
    headers: {
        'Ocp-Apim-Subscription-Key': subscriptionKey,
        'Ocp-Apim-Subscription-Region': location,
        'Content-type': 'application/json',
        'X-ClientTraceId': uuidv4().toString()
    },
    params: {
        'api-version': '3.0',
        'from': 'en',
        'to': 'es'
    },
    data: [{
        'text': 'shark',
        'translation': 'tiburón'
    }],
    responseType: 'json'
}).then(function(response){
    console.log(JSON.stringify(response.data, null, 4));
})
```

> [!div class="nextstepaction"]
> [Ich habe Übersetzungen im Kontext abgerufen.](#next-steps) [Es ist ein Problem aufgetreten.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Nodejs&Product=Translator&Page=quickstart-translator&Section=dictionary-examples-translations-in-context)

# <a name="python"></a>[Python](#tab/python)
```python
import requests, uuid, json

# Add your subscription key and endpoint
subscription_key = "YOUR_SUBSCRIPTION_KEY"
endpoint = "https://api.cognitive.microsofttranslator.com"

# Add your location, also known as region. The default is global.
# This is required if using a Cognitive Services resource.
location = "YOUR_RESOURCE_LOCATION"

path = '/dictionary/examples'
constructed_url = endpoint + path

params = {
    'api-version': '3.0',
    'from': 'en',
    'to': 'es'
}
constructed_url = endpoint + path

headers = {
    'Ocp-Apim-Subscription-Key': subscription_key,
    'Ocp-Apim-Subscription-Region': location,
    'Content-type': 'application/json',
    'X-ClientTraceId': str(uuid.uuid4())
}

# You can pass more than one object in body.
body = [{
    'text': 'shark',
    'translation': 'tiburón'
}]

request = requests.post(constructed_url, params=params, headers=headers, json=body)
response = request.json()

print(json.dumps(response, sort_keys=True, ensure_ascii=False, indent=4, separators=(',', ': ')))
```

> [!div class="nextstepaction"]
> [Ich habe Übersetzungen im Kontext abgerufen.](#next-steps) [Es ist ein Problem aufgetreten.](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Python&Product=Translator&Page=quickstart-translator&Section=dictionary-examples-translations-in-context)

---

Nach einem erfolgreichen Aufruf sollten Sie die folgende Antwort sehen. Weitere Informationen über die Antwort finden Sie unter [Wörterbuchsuche](reference/v3-0-dictionary-examples.md)

```json
[
    {
        "examples": [
            {
                "sourcePrefix": "More than a match for any ",
                "sourceSuffix": ".",
                "sourceTerm": "shark",
                "targetPrefix": "Más que un fósforo para cualquier ",
                "targetSuffix": ".",
                "targetTerm": "tiburón"
            },
            {
                "sourcePrefix": "Same with the mega ",
                "sourceSuffix": ", of course.",
                "sourceTerm": "shark",
                "targetPrefix": "Y con el mega ",
                "targetSuffix": ", por supuesto.",
                "targetTerm": "tiburón"
            },
            {
                "sourcePrefix": "A ",
                "sourceSuffix": " ate it.",
                "sourceTerm": "shark",
                "targetPrefix": "Te la ha comido un ",
                "targetSuffix": ".",
                "targetTerm": "tiburón"
            }
        ],
        "normalizedSource": "shark",
        "normalizedTarget": "tiburón"
    }
]
```

## <a name="troubleshooting"></a>Problembehandlung

### <a name="common-http-status-codes"></a>Allgemeine HTTP-Statuscodes

| HTTP-Statuscode | BESCHREIBUNG | Mögliche Ursache |
|------------------|-------------|-----------------|
| 200 | OK | Die Anforderung wurde erfolgreich gesendet. |
| 400 | Ungültige Anforderung | Ein erforderlicher Parameter fehlt, ist leer oder Null. Oder der an einen erforderlichen oder optionalen Parameter übergebene Wert ist ungültig. Ein häufiges Problem sind zu lange Kopfzeilen. |
| 401 | Nicht autorisiert | Die Anforderung ist nicht autorisiert. Stellen Sie sicher, dass Ihr Abonnementschlüssel oder -token gültig ist und sich in der richtigen Region befindet. *Siehe auch* [Authentifizierung](reference/v3-0-reference.md#authentication).|
| 429 | Zu viele Anforderungen | Sie haben das Kontingent oder die Rate der Anforderungen überschritten, das bzw. die für Ihr Abonnement zulässig ist. |
| 502 | Ungültiges Gateway    | Netzwerk- oder serverseitiges Problem. Kann auch auf ungültige Header hinweisen. |

### <a name="java-users"></a>Java-Benutzer

Wenn Verbindungsprobleme auftreten, ist möglicherweise das SSL-Zertifikat abgelaufen. Installieren Sie zum Beheben dieses Problems [DigiCertGlobalRootG2.crt](http://cacerts.digicert.com/DigiCertGlobalRootG2.crt) im privaten Speicher.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Anpassen und Verbessern der Übersetzung](customization.md)
