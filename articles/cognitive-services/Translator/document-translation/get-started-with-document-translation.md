---
title: Erste Schritte bei der Dokumentübersetzung
description: Es wird beschrieben, wie Sie einen Dienst für die Dokumentübersetzung mit der Programmiersprache C#, Go, Java, Node.js oder Python bzw. den entsprechenden Plattformen erstellen.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: quickstart
ms.date: 09/16/2021
ms.author: lajanuar
recommendations: false
ms.openlocfilehash: 051e218b8c0bce8249c6fbe3dd785d797130b61f
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "130062726"
---
# <a name="get-started-with-document-translation"></a>Erste Schritte bei der Dokumentübersetzung

 In diesem Artikel wird beschrieben, wie Sie die Dokumentübersetzung mit HTTP-REST-API-Methoden verwenden. Die Dokumentübersetzung ist ein cloudbasiertes Feature des [Azure-Textübersetzungsdiensts](../translator-overview.md).  Die API für die Dokumentübersetzung ermöglicht die Übersetzung gesamter Dokumente unter Beibehaltung der Struktur und Textformatierung des Quelldokuments.

## <a name="prerequisites"></a>Voraussetzungen

> [!NOTE]
>
> 1. Beim Erstellen einer Cognitive Services-Ressource im Azure-Portal haben Sie normalerweise die Möglichkeit, einen Abonnementschlüssel für mehrere Dienste oder nur für einen Dienst zu erstellen. Dokumentübersetzung wird derzeit aber nur für die Textübersetzungsressource (einzelner Dienst) unterstützt und ist **nicht** in der Cognitive Services-Ressource (mehrere Dienste) enthalten.
> 2. Die Dokumentübersetzung ist **nur** im Diensttarif „S1 Standard“ (nutzungsbasierte Bezahlung) oder im Tarif „D3 Volume Discount“ (Volumenrabatt) verfügbar. _Weitere Informationen finden Sie unter_ [Cognitive Services-Preise – Translator](https://azure.microsoft.com/pricing/details/cognitive-services/translator/).
>

Zunächst benötigen Sie Folgendes:

* Ein aktives [**Azure-Konto**](https://azure.microsoft.com/free/cognitive-services/).  Falls Sie noch kein Konto haben, können Sie ein [**kostenloses Konto erstellen**](https://azure.microsoft.com/free/).

* Eine [**Textübersetzungsressource in Form eines einzelnen Diensts**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation) (**keine** Cognitive Services-Ressource mit mehreren Diensten)

* Ein [**Azure Blob Storage-Konto**](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). Sie erstellen Container zum Speichern und Organisieren Ihrer Blobdaten unter Ihrem Speicherkonto.

## <a name="custom-domain-name-and-subscription-key"></a>Der Name Ihrer benutzerdefinierten Domäne und der Abonnementschlüssel

> [!IMPORTANT]
>
> * **Für alle API-Anforderungen an den Dienst für die Dokumentübersetzung muss ein Endpunkt einer benutzerdefinierten Domäne verwendet werden**.
> * Zum Senden von HTTP-Anforderungen für die Dokumentübersetzung verwenden Sie nicht den Endpunkt, der im Azure-Portal auf der Ressourcenseite _Schlüssel und Endpunkt_ angegeben ist, und auch nicht den globalen Übersetzungsendpunkt `api.cognitive.microsofttranslator.com`.

### <a name="what-is-the-custom-domain-endpoint"></a>Was ist der Endpunkt einer benutzerdefinierten Domäne?

Der Endpunkt einer benutzerdefinierten Domäne ist eine URL, die mit Ihrem Ressourcennamen, Hostnamen und den Unterverzeichnissen für die Textübersetzung formatiert ist:

```http
https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0
```

### <a name="find-your-custom-domain-name"></a>Ermitteln des Namens Ihrer benutzerdefinierten Domäne

Der Parameter **NAME-OF-YOUR-RESOURCE** (auch als *Name der benutzerdefinierten Domäne* bezeichnet) steht hier für den Wert, den Sie beim Erstellen Ihrer Textübersetzungsressource im Feld **Name** eingegeben haben.

:::image type="content" source="../media/instance-details.png" alt-text="Abbildung: Azure-Portal, Ressource erstellen, Details zur Instanz, Feld „Name“":::

### <a name="get-your-subscription-key"></a>Abrufen des Abonnementschlüssels

Für Anforderungen, die an den Textübersetzungsdienst gesendet werden, wird ein schreibgeschützter Schlüssel für die Authentifizierung des Zugriffs benötigt.

1. Wählen Sie **Zu Ressource wechseln** aus, nachdem eine von Ihnen erstellte neue Ressource bereitgestellt wurde. Navigieren Sie direkt zu Ihrer Ressourcenseite, falls Sie über eine vorhandene Ressource für die Dokumentübersetzung verfügen.
1. Klicken Sie in der linken Schiene unter *Ressourcenverwaltung* auf **Schlüssel und Endpunkt**.
1. Kopieren Sie Ihren Abonnementschlüssel, und fügen Sie ihn an einem geeigneten Ort ein, z. B. den *Editor von Microsoft*.
1. Sie fügen ihn dann in den folgenden Code ein, um Ihre Anforderung für den Dokumentübersetzungsdienst zu authentifizieren.

:::image type="content" source="../media/translator-keys.png" alt-text="Abbildung: Feld „Abonnementschlüssel“ im Azure-Portal":::

## <a name="create-azure-blob-storage-containers"></a>Erstellen des Azure Blob Storage-Containers

Für Quell- und Zieldateien müssen Sie in Ihrem [**Azure-Blobspeicherkonto**](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM) [**Container erstellen**](../../../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container).

* **Quellcontainer**: In diesen Container laden Sie Ihre Dateien für die Übersetzung hoch (erforderlich).
* **Zielcontainer**: In diesem Container werden Ihre übersetzten Dateien gespeichert (erforderlich).

> [!NOTE]
> Die Dokumentübersetzung unterstützt Glossare als Blobs in Zielcontainern (keine separaten Glossarcontainer). Wenn Sie ein benutzerdefiniertes Glossar hinzufügen möchten, fügen Sie es dem Zielcontainer hinzu, und fügen Sie das ` glossaryUrl` in die Anforderung ein.  Wenn das Übersetzungssprachpaar nicht im Glossar vorhanden ist, wird es nicht angewendet. *Siehe dazu* [Übersetzen von Dokumenten mithilfe von benutzerdefinierten Glossaren](#translate-documents-using-a-custom-glossary).

### <a name="create-sas-access-tokens-for-document-translation"></a>**Erstellen von SAS-Zugriffstoken für die Dokumentübersetzung**

Die URLs `sourceUrl`, `targetUrl` und `glossaryUrl` (optional) müssen ein SAS-Token (Shared Access Signature) enthalten, das als Abfragezeichenfolge angefügt ist. Das Token kann Ihrem Container oder bestimmten Blobs zugewiesen sein. *Informationen hierzu finden Sie unter* [**Erstellen von SAS-Token für die Verarbeitung der Dokumentübersetzung**](create-sas-tokens.md).

* Ihr **Quellcontainer** oder Blob muss über Zugriff vom Typ **Lesen** und **Auflisten** verfügen.
* Ihr **Zielcontainer** oder Blob muss über Zugriff vom Typ **Schreiben** und **Auflisten** verfügen.
* Ihr **Glossar**-Blob muss über Zugriff vom Typ **Lesen** und **Auflisten** verfügen.

> [!TIP]
>
> * Falls Sie **mehrere** Dateien (Blobs) während eines Vorgangs übersetzen, sollten Sie den **SAS-Zugriff auf Containerebene delegieren**.
> * Falls Sie **nur eine** Datei (Blob) während eines Vorgangs übersetzen, sollten Sie den **SAS-Zugriff auf Blobebene delegieren**.
>

## <a name="document-translation-http-requests"></a>Dokumentübersetzung: HTTP-Anforderungen

Eine Batchanforderung für die Dokumentübersetzung wird als POST-Anforderung an Ihren Endpunkt mit dem Textübersetzungsdienst gesendet. Wenn der Vorgang erfolgreich ist, gibt die POST-Methode den Antwortcode `202 Accepted` zurück, und die Batchanforderung wird vom Dienst erstellt.

### <a name="http-headers"></a>HTTP-Header

Jede Anforderung der API für die Dokumentübersetzung enthält die folgenden Header:

|HTTP-Header|BESCHREIBUNG|
|---|--|
|Ocp-Apim-Subscription-Key|**Erforderlich**: Dieser Wert ist der Azure-Abonnementschlüssel für Ihre Textübersetzungs- oder Cognitive Services-Ressource.|
|Content-Type|**Erforderlich**: Gibt den Inhaltstyp der Nutzdaten an. „application/json“ oder „charset=UTF-8“ werden als Werte akzeptiert.|
|Content-Length|**Erforderlich**: Steht für die Länge des Anforderungstexts.|

### <a name="post-request-body-properties"></a>Eigenschaften des POST-Anforderungstexts

* Die URL für die POST-Anforderung lautet: POST `https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0/batches`.
* Der POST-Anforderungstext ist ein JSON-Objekt mit dem Namen `inputs`.
* Das Objekt `inputs` enthält die beiden Containeradressen für `sourceURL` und `targetURL` für Ihre Quell- und Zielsprachpaare.
* Die Felder `prefix` und `suffix` (optional) werden verwendet, um Dokumente im Container zu filtern (einschließlich der Ordner).
* Ein Wert für das Feld `glossaries` (optional) wird angewendet, wenn das Dokument übersetzt wird.
* Die `targetUrl` für die einzelnen Zielsprachen muss jeweils eindeutig sein.

>[!NOTE]
> Wenn am Ziel bereits eine Datei mit dem gleichen Namen vorhanden ist, tritt beim Auftrag ein Fehler auf.

<!-- markdownlint-disable MD024 -->
### <a name="translate-all-documents-in-a-container"></a>Übersetzen aller Dokumente in einem Container

```json
{
    "inputs": [
        {
            "source": {
                "sourceUrl": "https://my.blob.core.windows.net/source-en?sv=2019-12-12&st=2021-03-05T17%3A45%3A25Z&se=2021-03-13T17%3A45%3A00Z&sr=c&sp=rl&sig=SDRPMjE4nfrH3csmKLILkT%2Fv3e0Q6SWpssuuQl1NmfM%3D"
            },
            "targets": [
                {
                    "targetUrl": "https://my.blob.core.windows.net/target-fr?sv=2019-12-12&st=2021-03-05T17%3A49%3A02Z&se=2021-03-13T17%3A49%3A00Z&sr=c&sp=wdl&sig=Sq%2BYdNbhgbq4hLT0o1UUOsTnQJFU590sWYo4BOhhQhs%3D",
                    "language": "fr"
                }
            ]
        }
    ]
}
```

### <a name="translate-a-specific-document-in-a-container"></a>Übersetzen eines bestimmten Dokuments in einen Container

* Stellen Sie sicher, dass Sie „storageType“: „File“ angegeben haben.
* Stellen Sie sicher, dass Sie Glossar-URL- und SAS-Token für das spezifische Blob/Dokument (nicht für den Container) erstellt haben.
* Stellen Sie sicher, dass Sie den Zieldateinamen als Teil der Ziel-URL angegeben haben – obwohl das SAS-Token für den Container noch immer vorhanden ist.
* Die Beispielanforderung unten zeigt, wie ein einzelnes Dokument in zwei Zielsprachen übersetzt wird.

```json
{
    "inputs": [
        {
            "storageType": "File",
            "source": {
                "sourceUrl": "https://my.blob.core.windows.net/source-en/source-english.docx?sv=2019-12-12&st=2021-01-26T18%3A30%3A20Z&se=2021-02-05T18%3A30%3A00Z&sr=c&sp=rl&sig=d7PZKyQsIeE6xb%2B1M4Yb56I%2FEEKoNIF65D%2Fs0IFsYcE%3D"
            },
            "targets": [
                {
                    "targetUrl": "https://my.blob.core.windows.net/target/try/Target-Spanish.docx?sv=2019-12-12&st=2021-01-26T18%3A31%3A11Z&se=2021-02-05T18%3A31%3A00Z&sr=c&sp=wl&sig=AgddSzXLXwHKpGHr7wALt2DGQJHCzNFF%2F3L94JHAWZM%3D",
                    "language": "es"
                },
                {
                    "targetUrl": "https://my.blob.core.windows.net/target/try/Target-German.docx?sv=2019-12-12&st=2021-01-26T18%3A31%3A11Z&se=2021-02-05T18%3A31%3A00Z&sr=c&sp=wl&sig=AgddSzXLXwHKpGHr7wALt2DGQJHCzNFF%2F3L94JHAWZM%3D",
                    "language": "de"
                }
            ]
        }
    ]
}
```

### <a name="translate-documents-using-a-custom-glossary"></a>Übersetzen von Dokumente mithilfe von benutzerdefinierten Glossaren

```json
{
    "inputs": [
        {
            "source": {
                "sourceUrl": "https://myblob.blob.core.windows.net/source",
                "filter": {
                    "prefix": "myfolder/"
                }
            },
            "targets": [
                {
                    "targetUrl": "https://myblob.blob.core.windows.net/target",
                    "language": "es",
                    "glossaries": [
                        {
                            "glossaryUrl": "https:// myblob.blob.core.windows.net/glossary/en-es.xlf",
                            "format": "xliff"
                        }
                    ]
                }
            ]
        }
    ]
}
```

## <a name="use-code-to-submit-document-translation-requests"></a>Senden von Dokumentübersetzungsanforderungen mithilfe von Code

### <a name="set-up-your-coding-platform"></a>Einrichten der Codierungsplattform

### <a name="c"></a>[C#](#tab/csharp)

* Erstellen Sie ein neues Projekt.
* Ersetzen Sie den Code in „Program.cs“ durch den unten abgebildeten C#-Code.
* Legen Sie Ihre URL-Werte für den Endpunkt, Abonnementschlüssel und Container in Program.cs fest.
* Fügen Sie für die Verarbeitung der JSON-Daten das [Newtonsoft.Json-Paket mit der .NET CLI](https://www.nuget.org/packages/Newtonsoft.Json/) hinzu.
* Führen Sie das Programm aus dem Projektverzeichnis aus.

### <a name="nodejs"></a>[Node.js](#tab/javascript)

* Erstellen Sie ein neues Node.js-Projekt.
* Installieren Sie die Axios-Bibliothek mit `npm i axios`.
* Kopieren Sie den unten angegebenen Code und fügen Sie ihn in Ihr Projekt ein.
* Legen Sie Ihre URL-Werte für den Endpunkt, Abonnementschlüssel und Container fest.
* Sie führen das Programm aus.

### <a name="python"></a>[Python](#tab/python)

* Erstellen Sie ein neues Projekt.
* Kopieren Sie den Code aus einem der Beispiele, und fügen Sie ihn in Ihr Projekt ein.
* Legen Sie Ihre URL-Werte für den Endpunkt, Abonnementschlüssel und Container fest.
* Führen Sie das Programm aus. Beispiel: `python translate.py`.

### <a name="java"></a>[Java](#tab/java)

* Erstellen Sie ein Arbeitsverzeichnis für Ihr Projekt. Beispiel:

```powershell
mkdir sample-project
```

* Erstellen Sie in Ihrem Projektverzeichnis die folgende Struktur mit Unterverzeichnissen:

  src</br>
&emsp; └ main</br>
&emsp;&emsp;&emsp;└ java

```powershell
mkdir -p src/main/java/
```

**HINWEIS**: Java-Quelldateien (z. B. _sample.java_) befinden sich in „src/main/**java**“.

* Initialisieren Sie Ihr Projekt mit Gradle in Ihrem Stammverzeichnis (z. B. *sample-project*):

```powershell
gradle init --type basic
```

* Wenn Sie zur Auswahl einer **DSL** aufgefordert werden, wählen Sie **Kotlin** aus.

* Aktualisieren Sie die Datei `build.gradle.kts`. Beachten Sie, dass Sie Ihren `mainClassName` passend zum Beispiel aktualisieren müssen:

  ```java
  plugins {
    java
    application
  }
  application {
    mainClassName = "{NAME OF YOUR CLASS}"
  }
  repositories {
    mavenCentral()
  }
  dependencies {
    compile("com.squareup.okhttp:okhttp:2.5.0")
  }
  ```

* Erstellen Sie im Verzeichnis **java** eine Java-Datei, kopieren Sie den Code aus dem Beispiel, und fügen Sie ihn in die Datei ein. Vergessen Sie nicht, Ihren Abonnementschlüssel und Endpunkt hinzuzufügen.

* **Führen Sie den Buildvorgang für das Beispiel im Stammverzeichnis durch, und führen Sie es aus**:

```powershell
gradle build
gradle run
```

### <a name="go"></a>[Go](#tab/go)

* Erstellen Sie ein neues Go-Projekt.
* Fügen Sie den unten stehenden Code hinzu.
* Legen Sie Ihre URL-Werte für den Endpunkt, Abonnementschlüssel und Container fest.
* Speichern Sie die Datei mit der Erweiterung „.go“.
* Öffnen Sie auf einem Computer, auf dem Go installiert ist, eine Eingabeaufforderung.
* Erstellen Sie die Datei. Beispielsweise mit „go build example-code.go“.
* Führen Sie die Datei aus, beispielsweise mit „example-code“.

 ---

> [!IMPORTANT]
>
> Für die unten angegebenen Codebeispiele führen Sie für Ihren Schlüssel und Endpunkt das Hartcodieren durch, wo dies angegeben ist. Achten Sie darauf, den Schlüssel nach Abschluss des Vorgangs aus Ihrem Code zu entfernen und ihn nicht öffentlich zugänglich zu machen.  Informationen zum sicheren Speichern und Zugreifen auf Ihre Anmeldeinformationen finden Sie unter [Sicherheit von Azure Cognitive Services](../../cognitive-services-security.md?tabs=command-line%2ccsharp).
>
> Je nach Vorgang müssen Sie ggf. die folgenden Felder aktualisieren:
>>>
>> * `endpoint`
>> * `subscriptionKey`
>> * `sourceURL`
>> * `targetURL`
>> * `glossaryURL`
>> * `id` (Auftrags-ID)
>>

#### <a name="locating--the-id-value"></a>Ermitteln des Werts für `id`

* Sie finden die `id` des Auftrags im URL-Wert `Operation-Location` des Antwortheaders der POST-Methode. Der letzte Parameter der URL ist die Auftrags-ID ( **`id`** ) des Vorgangs:

|**Antwortheader**|**Ergebnis-URL**|
|-----------------------|----------------|
Operation-Location   | https://<<span>NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0/batches/9dce0aa9-78dc-41ba-8cae-2e2f3c2ff8ec</span>

* Sie können auch eine Anforderung zum Abrufen von Aufträgen (**GET Jobs**) verwenden, um die Auftrags-ID (`id`) für die Dokumentübersetzung zu ermitteln.

>

 ## <a name="translate-documents"></a>Übersetzen von Dokumenten

### <a name="c"></a>[C#](#tab/csharp)

```csharp

    using System;
    using System.Net.Http;
    using System.Threading.Tasks;
    using System.Text;


    class Program
    {

        static readonly string route = "/batches";

        private static readonly string endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0";

        private static readonly string subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";

        static readonly string json = ("{\"inputs\": [{\"source\": {\"sourceUrl\": \"https://YOUR-SOURCE-URL-WITH-READ-LIST-ACCESS-SAS\",\"storageSource\": \"AzureBlob\",\"language\": \"en\",\"filter\":{\"prefix\": \"Demo_1/\"} }, \"targets\": [{\"targetUrl\": \"https://YOUR-TARGET-URL-WITH-WRITE-LIST-ACCESS-SAS\",\"storageSource\": \"AzureBlob\",\"category\": \"general\",\"language\": \"es\"}]}]}");

        static async Task Main(string[] args)
        {
            using HttpClient client = new HttpClient();
            using HttpRequestMessage request = new HttpRequestMessage();
            {

                StringContent content = new StringContent(json, Encoding.UTF8, "application/json");

                request.Method = HttpMethod.Post;
                request.RequestUri = new Uri(endpoint + route);
                request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
                request.Content = content;

                HttpResponseMessage  response = await client.SendAsync(request);
                string result = response.Content.ReadAsStringAsync().Result;
                if (response.IsSuccessStatusCode)
                {
                    Console.WriteLine($"Status code: {response.StatusCode}");
                    Console.WriteLine();
                    Console.WriteLine($"Response Headers:");
                    Console.WriteLine(response.Headers);
                }
                else
                    Console.Write("Error");

            }

        }

    }
}
```

### <a name="nodejs"></a>[Node.js](#tab/javascript)

```javascript

const axios = require('axios').default;

let endpoint = 'https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0';
let route = '/batches';
let subscriptionKey = '<YOUR-SUBSCRIPTION-KEY>';

let data = JSON.stringify({"inputs": [
  {
      "source": {
          "sourceUrl": "https://YOUR-SOURCE-URL-WITH-READ-LIST-ACCESS-SAS",
          "storageSource": "AzureBlob",
          "language": "en",
          "filter":{
              "prefix": "Demo_1/"
          }
      },
      "targets": [
          {
              "targetUrl": "https://YOUR-TARGET-URL-WITH-WRITE-LIST-ACCESS-SAS",
              "storageSource": "AzureBlob",
              "category": "general",
              "language": "es"}]}]});

let config = {
  method: 'post',
  baseURL: endpoint,
  url: route,
  headers: {
    'Ocp-Apim-Subscription-Key': subscriptionKey,
    'Content-Type': 'application/json'
  },
  data: data
};

axios(config)
.then(function (response) {
  let result = { statusText: response.statusText, statusCode: response.status, headers: response.headers };
  console.log()
  console.log(JSON.stringify(result));
})
.catch(function (error) {
  console.log(error);
});
```

### <a name="python"></a>[Python](#tab/python)

```python

import requests

endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0"
subscriptionKey =  '<YOUR-SUBSCRIPTION-KEY>'
path = '/batches'
constructed_url = endpoint + path

payload= {
    "inputs": [
        {
            "source": {
                "sourceUrl": "https://YOUR-SOURCE-URL-WITH-READ-LIST-ACCESS-SAS",
                "storageSource": "AzureBlob",
                "language": "en",
                "filter":{
                    "prefix": "Demo_1/"
                }
            },
            "targets": [
                {
                    "targetUrl": "https://YOUR-TARGET-URL-WITH-WRITE-LIST-ACCESS-SAS",
                    "storageSource": "AzureBlob",
                    "category": "general",
                    "language": "es"
                }
            ]
        }
    ]
}
headers = {
  'Ocp-Apim-Subscription-Key': subscriptionKey,
  'Content-Type': 'application/json'
}

response = requests.post(constructed_url, headers=headers, json=payload)

print(f'response status code: {response.status_code}\nresponse status: {response.reason}\nresponse headers: {response.headers}')
```

### <a name="java"></a>[Java](#tab/java)

```java

import java.io.*;
import java.net.*;
import java.util.*;
import com.squareup.okhttp.*;

public class DocumentTranslation {
    String subscriptionKey = "'<YOUR-SUBSCRIPTION-KEY>'";
    String endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0";
    String path = endpoint + "/batches";

    OkHttpClient client = new OkHttpClient();

    public void post() throws IOException {
        MediaType mediaType = MediaType.parse("application/json");
        RequestBody body = RequestBody.create(mediaType,  "{\n \"inputs\": [\n {\n \"source\": {\n \"sourceUrl\": \"https://YOUR-SOURCE-URL-WITH-READ-LIST-ACCESS-SAS\",\n \"filter\": {\n  \"prefix\": \"Demo_1\"\n  },\n  \"language\": \"en\",\n \"storageSource\": \"AzureBlob\"\n  },\n \"targets\": [\n {\n \"targetUrl\": \"https://YOUR-TARGET-URL-WITH-WRITE-LIST-ACCESS-SAS\",\n \"category\": \"general\",\n\"language\": \"fr\",\n\"storageSource\": \"AzureBlob\"\n }\n ],\n \"storageType\": \"Folder\"\n }\n  ]\n}");
        Request request = new Request.Builder()
                .url(path).post(body)
                .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey)
                .addHeader("Content-type", "application/json")
                .build();
        Response response = client.newCall(request).execute();
        System.out.println(response.code());
        System.out.println(response.headers());
    }

  public static void main(String[] args) {
        try {
            DocumentTranslation sampleRequest = new DocumentTranslation();
            sampleRequest.post();
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}
```

### <a name="go"></a>[Go](#tab/go)

```go

package main

import (
 "bytes"
 "fmt"
"net/http"
)

func main() {
endpoint := "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0"
subscriptionKey := "<YOUR-SUBSCRIPTION-KEY>"
uri := endpoint + "/batches"
method := "POST"

var jsonStr = []byte(`{"inputs":[{"source":{"sourceUrl":"https://YOUR-SOURCE-URL-WITH-READ-LIST-ACCESS-SAS","storageSource":"AzureBlob","language":"en","filter":{"prefix":"Demo_1/"}},"targets":[{"targetUrl":"https://YOUR-TARGET-URL-WITH-WRITE-LIST-ACCESS-SAS","storageSource":"AzureBlob","category":"general","language":"es"}]}]}`)

req, err := http.NewRequest(method, endpoint, bytes.NewBuffer(jsonStr))
req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)
req.Header.Add("Content-Type", "application/json")

client := &http.Client{}

if err != nil {
    fmt.Println(err)
    return
  }
  res, err := client.Do(req)
  if err != nil {
    fmt.Println(err)
    return
  }
  defer res.Body.Close()
  fmt.Println("response status:",  res.Status)
  fmt.Println("response headers",  res.Header)
}
```

---

## <a name="get-file-formats"></a>Abrufen von Dateiformaten

Rufen Sie eine Liste mit den unterstützten Dateiformaten ab. Wenn der Vorgang erfolgreich ist, wird mit dieser Methode der Antwortcode `200 OK` zurückgegeben.

### <a name="c"></a>[C#](#tab/csharp)

```csharp

using System;
using System.Net.Http;
using System.Threading.Tasks;


class Program
{


    private static readonly string endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0";

    static readonly string route = "/documents/formats";

    private static readonly string subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";

    static async Task Main(string[] args)
    {

        HttpClient client = new HttpClient();
            using HttpRequestMessage request = new HttpRequestMessage();
            {
                request.Method = HttpMethod.Get;
                request.RequestUri = new Uri(endpoint + route);
                request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);


                HttpResponseMessage response = await client.SendAsync(request);
                string result = response.Content.ReadAsStringAsync().Result;

                Console.WriteLine($"Status code: {response.StatusCode}");
                Console.WriteLine($"Response Headers: {response.Headers}");
                Console.WriteLine();
                Console.WriteLine(result);
            }
}
```

### <a name="nodejs"></a>[Node.js](#tab/javascript)

```javascript

const axios = require('axios');

let endpoint = 'https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0';
let subscriptionKey = '<YOUR-SUBSCRIPTION-KEY>';
let route = '/documents/formats';

let config = {
  method: 'get',
  url: endpoint + route,
  headers: {
    'Ocp-Apim-Subscription-Key': subscriptionKey
  }
};

axios(config)
.then(function (response) {
  console.log(JSON.stringify(response.data));
})
.catch(function (error) {
  console.log(error);
});

```

### <a name="java"></a>[Java](#tab/java)

```java
import java.io.*;
import java.net.*;
import java.util.*;
import com.squareup.okhttp.*;

public class GetFileFormats {

    String subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";
    String endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0";
    String url = endpoint + "/documents/formats";
    OkHttpClient client = new OkHttpClient();

    public void get() throws IOException {
        Request request = new Request.Builder().url(
                url).method("GET", null).addHeader("Ocp-Apim-Subscription-Key", subscriptionKey).build();
        Response response = client.newCall(request).execute();
            System.out.println(response.body().string());
        }

    public static void main(String[] args) throws IOException {
        try{
            GetJobs jobs = new GetJobs();
            jobs.get();
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}

```

### <a name="python"></a>[Python](#tab/python)

```python

import http.client

host = '<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com'
parameters = '//translator/text/batch/v1.0/documents/formats'
subscriptionKey =  '<YOUR-SUBSCRIPTION-KEY>'
conn = http.client.HTTPSConnection(host)
payload = ''
headers = {
  'Ocp-Apim-Subscription-Key': subscriptionKey
}
conn.request("GET", parameters , payload, headers)
res = conn.getresponse()
data = res.read()
print(res.status)
print()
print(data.decode("utf-8"))
```

### <a name="go"></a>[Go](#tab/go)

```go

package main

import (
  "fmt"
  "net/http"
  "io/ioutil"
)

func main() {

  endpoint := "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0"
  subscriptionKey := "<YOUR-SUBSCRIPTION-KEY>"
  uri := endpoint + "/documents/formats"
  method := "GET"

  client := &http.Client {
  }
  req, err := http.NewRequest(method, uri, nil)

  if err != nil {
    fmt.Println(err)
    return
  }
  req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

  res, err := client.Do(req)
  if err != nil {
    fmt.Println(err)
    return
  }
  defer res.Body.Close()

  body, err := ioutil.ReadAll(res.Body)
  if err != nil {
    fmt.Println(err)
    return
  }
  fmt.Println(res.StatusCode)
  fmt.Println(string(body))
}
```

---

## <a name="get-job-status"></a>Abrufen des Auftragsstatus

Rufen Sie den aktuellen Status für einen bestimmten Auftrag und eine Zusammenfassung aller Aufträge in einer Anforderung der Dokumentübersetzung ab. Wenn der Vorgang erfolgreich ist, wird mit dieser Methode der Antwortcode `200 OK` zurückgegeben.
<!-- markdownlint-disable MD024 -->

### <a name="c"></a>[C#](#tab/csharp)

```csharp

using System;
using System.Net.Http;
using System.Threading.Tasks;


class Program
{


    private static readonly string endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0";

    static readonly string route = "/batches/{id}";

    private static readonly string subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";

    static async Task Main(string[] args)
    {

        HttpClient client = new HttpClient();
            using HttpRequestMessage request = new HttpRequestMessage();
            {
                request.Method = HttpMethod.Get;
                request.RequestUri = new Uri(endpoint + route);
                request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);


                HttpResponseMessage response = await client.SendAsync(request);
                string result = response.Content.ReadAsStringAsync().Result;

                Console.WriteLine($"Status code: {response.StatusCode}");
                Console.WriteLine($"Response Headers: {response.Headers}");
                Console.WriteLine();
                Console.WriteLine(result);
            }
}
```

### <a name="nodejs"></a>[Node.js](#tab/javascript)

```javascript

const axios = require('axios');

let endpoint = 'https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0';
let subscriptionKey = '<YOUR-SUBSCRIPTION-KEY>';
let route = '/batches/{id}';

let config = {
  method: 'get',
  url: endpoint + route,
  headers: {
    'Ocp-Apim-Subscription-Key': subscriptionKey
  }
};

axios(config)
.then(function (response) {
  console.log(JSON.stringify(response.data));
})
.catch(function (error) {
  console.log(error);
});

```

### <a name="java"></a>[Java](#tab/java)

```java

import java.io.*;
import java.net.*;
import java.util.*;
import com.squareup.okhttp.*;

public class GetJobStatus {

    String subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";
    String endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0";
    String url = endpoint + "/batches/{id}";
    OkHttpClient client = new OkHttpClient();

    public void get() throws IOException {
        Request request = new Request.Builder().url(
                url).method("GET", null).addHeader("Ocp-Apim-Subscription-Key", subscriptionKey).build();
        Response response = client.newCall(request).execute();
            System.out.println(response.body().string());
        }

    public static void main(String[] args) throws IOException {
        try{
            GetJobs jobs = new GetJobs();
            jobs.get();
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}

```

### <a name="python"></a>[Python](#tab/python)

```python

import http.client

host = '<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com'
parameters = '//translator/text/batch/v1.0/batches/{id}'
subscriptionKey =  '<YOUR-SUBSCRIPTION-KEY>'
conn = http.client.HTTPSConnection(host)
payload = ''
headers = {
  'Ocp-Apim-Subscription-Key': subscriptionKey
}
conn.request("GET", parameters , payload, headers)
res = conn.getresponse()
data = res.read()
print(res.status)
print()
print(data.decode("utf-8"))
```

### <a name="go"></a>[Go](#tab/go)

```go

package main

import (
  "fmt"
  "net/http"
  "io/ioutil"
)

func main() {

  endpoint := "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0"
  subscriptionKey := "<YOUR-SUBSCRIPTION-KEY>"
  uri := endpoint + "/batches/{id}"
  method := "GET"

  client := &http.Client {
  }
  req, err := http.NewRequest(method, uri, nil)

  if err != nil {
    fmt.Println(err)
    return
  }
  req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

  res, err := client.Do(req)
  if err != nil {
    fmt.Println(err)
    return
  }
  defer res.Body.Close()

  body, err := ioutil.ReadAll(res.Body)
  if err != nil {
    fmt.Println(err)
    return
  }
  fmt.Println(res.StatusCode)
  fmt.Println(string(body))
}
```

---

## <a name="get-document-status"></a>Abrufen des Dokumentstatus

### <a name="brief-overview"></a>Kurze Übersicht

Rufen Sie den Status eines bestimmten Dokuments in einer Anforderung der Dokumentübersetzung ab. Wenn der Vorgang erfolgreich ist, wird mit dieser Methode der Antwortcode `200 OK` zurückgegeben.

### <a name="c"></a>[C#](#tab/csharp)

```csharp

using System;
using System.Net.Http;
using System.Threading.Tasks;


class Program
{


    private static readonly string endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0";

    static readonly string route = "/{id}/document/{documentId}";

    private static readonly string subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";

    static async Task Main(string[] args)
    {

        HttpClient client = new HttpClient();
            using HttpRequestMessage request = new HttpRequestMessage();
            {
                request.Method = HttpMethod.Get;
                request.RequestUri = new Uri(endpoint + route);
                request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);


                HttpResponseMessage response = await client.SendAsync(request);
                string result = response.Content.ReadAsStringAsync().Result;

                Console.WriteLine($"Status code: {response.StatusCode}");
                Console.WriteLine($"Response Headers: {response.Headers}");
                Console.WriteLine();
                Console.WriteLine(result);
            }
}
```

### <a name="nodejs"></a>[Node.js](#tab/javascript)

```javascript

const axios = require('axios');

let endpoint = 'https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0';
let subscriptionKey = '<YOUR-SUBSCRIPTION-KEY>';
let route = '/{id}/document/{documentId}';

let config = {
  method: 'get',
  url: endpoint + route,
  headers: {
    'Ocp-Apim-Subscription-Key': subscriptionKey
  }
};

axios(config)
.then(function (response) {
  console.log(JSON.stringify(response.data));
})
.catch(function (error) {
  console.log(error);
});

```

### <a name="java"></a>[Java](#tab/java)

```java

import java.io.*;
import java.net.*;
import java.util.*;
import com.squareup.okhttp.*;

public class GetDocumentStatus {

    String subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";
    String endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0";
    String url = endpoint + "/{id}/document/{documentId}";
    OkHttpClient client = new OkHttpClient();

    public void get() throws IOException {
        Request request = new Request.Builder().url(
                url).method("GET", null).addHeader("Ocp-Apim-Subscription-Key", subscriptionKey).build();
        Response response = client.newCall(request).execute();
            System.out.println(response.body().string());
        }

    public static void main(String[] args) throws IOException {
        try{
            GetJobs jobs = new GetJobs();
            jobs.get();
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}

```

### <a name="python"></a>[Python](#tab/python)

```python

import http.client

host = '<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com'
parameters = '//translator/text/batch/v1.0/{id}/document/{documentId}'
subscriptionKey =  '<YOUR-SUBSCRIPTION-KEY>'
conn = http.client.HTTPSConnection(host)
payload = ''
headers = {
  'Ocp-Apim-Subscription-Key': subscriptionKey
}
conn.request("GET", parameters , payload, headers)
res = conn.getresponse()
data = res.read()
print(res.status)
print()
print(data.decode("utf-8"))
```

### <a name="go"></a>[Go](#tab/go)

```go

package main

import (
  "fmt"
  "net/http"
  "io/ioutil"
)

func main() {

  endpoint := "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0"
  subscriptionKey := "<YOUR-SUBSCRIPTION-KEY>"
  uri := endpoint + "/{id}/document/{documentId}"
  method := "GET"

  client := &http.Client {
  }
  req, err := http.NewRequest(method, uri, nil)

  if err != nil {
    fmt.Println(err)
    return
  }
  req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

  res, err := client.Do(req)
  if err != nil {
    fmt.Println(err)
    return
  }
  defer res.Body.Close()

  body, err := ioutil.ReadAll(res.Body)
  if err != nil {
    fmt.Println(err)
    return
  }
  fmt.Println(res.StatusCode)
  fmt.Println(string(body))
}
```

---

## <a name="delete-job"></a>Auftrag löschen

### <a name="brief-overview"></a>Kurze Übersicht

Brechen Sie den derzeit ausgeführten oder einen in die Warteschlange eingereihten Auftrag ab. Nur Dokumente, für die die Übersetzung noch nicht gestartet wurde, werden abgebrochen.

### <a name="c"></a>[C#](#tab/csharp)

```csharp

using System;
using System.Net.Http;
using System.Threading.Tasks;


class Program
{


    private static readonly string endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0";

    static readonly string route = "/batches/{id}";

    private static readonly string subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";

    static async Task Main(string[] args)
    {

        HttpClient client = new HttpClient();
            using HttpRequestMessage request = new HttpRequestMessage();
            {
                request.Method = HttpMethod.Delete;
                request.RequestUri = new Uri(endpoint + route);
                request.Headers.Add("Ocp-Apim-Subscription-Key", subscriptionKey);


                HttpResponseMessage response = await client.SendAsync(request);
                string result = response.Content.ReadAsStringAsync().Result;

                Console.WriteLine($"Status code: {response.StatusCode}");
                Console.WriteLine($"Response Headers: {response.Headers}");
                Console.WriteLine();
                Console.WriteLine(result);
            }
}
```

### <a name="nodejs"></a>[Node.js](#tab/javascript)

```javascript

const axios = require('axios');

let endpoint = 'https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0';
let subscriptionKey = '<YOUR-SUBSCRIPTION-KEY>';
let route = '/batches/{id}';

let config = {
  method: 'delete',
  url: endpoint + route,
  headers: {
    'Ocp-Apim-Subscription-Key': subscriptionKey
  }
};

axios(config)
.then(function (response) {
  console.log(JSON.stringify(response.data));
})
.catch(function (error) {
  console.log(error);
});

```

### <a name="java"></a>[Java](#tab/java)

```java

import java.io.*;
import java.net.*;
import java.util.*;
import com.squareup.okhttp.*;

public class DeleteJob {

    String subscriptionKey = "<YOUR-SUBSCRIPTION-KEY>";
    String endpoint = "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0";
    String url = endpoint + "/batches/{id}";
    OkHttpClient client = new OkHttpClient();

    public void get() throws IOException {
        Request request = new Request.Builder().url(
                url).method("DELETE", null).addHeader("Ocp-Apim-Subscription-Key", subscriptionKey).build();
        Response response = client.newCall(request).execute();
            System.out.println(response.body().string());
        }

    public static void main(String[] args) throws IOException {
        try{
            GetJobs jobs = new GetJobs();
            jobs.get();
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}

```

### <a name="python"></a>[Python](#tab/python)

```python

import http.client

host = '<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com'
parameters = '//translator/text/batch/v1.0/batches/{id}'
subscriptionKey =  '<YOUR-SUBSCRIPTION-KEY>'
conn = http.client.HTTPSConnection(host)
payload = ''
headers = {
  'Ocp-Apim-Subscription-Key': subscriptionKey
}
conn.request("DELETE", parameters , payload, headers)
res = conn.getresponse()
data = res.read()
print(res.status)
print()
print(data.decode("utf-8"))
```

### <a name="go"></a>[Go](#tab/go)

```go

package main

import (
  "fmt"
  "net/http"
  "io/ioutil"
)

func main() {

  endpoint := "https://<NAME-OF-YOUR-RESOURCE>.cognitiveservices.azure.com/translator/text/batch/v1.0"
  subscriptionKey := "<YOUR-SUBSCRIPTION-KEY>"
  uri := endpoint + "/batches/{id}"
  method := "DELETE"

  client := &http.Client {
  }
  req, err := http.NewRequest(method, uri, nil)

  if err != nil {
    fmt.Println(err)
    return
  }
  req.Header.Add("Ocp-Apim-Subscription-Key", subscriptionKey)

  res, err := client.Do(req)
  if err != nil {
    fmt.Println(err)
    return
  }
  defer res.Body.Close()

  body, err := ioutil.ReadAll(res.Body)
  if err != nil {
    fmt.Println(err)
    return
  }
  fmt.Println(res.StatusCode)
  fmt.Println(string(body))
}
```

---

## <a name="content-limits"></a>Grenzwerte für den Inhalt

In der folgenden Tabelle sind die Grenzwerte für Daten aufgelistet, die Sie an die Dokumentübersetzung senden können.

|attribute | Begrenzung|
|---|---|
|Dokumentgröße| ≤ 40 MB |
|Gesamtzahl an Dateien|≤ 1.000 |
|Gesamtgröße des Inhalts in einem Batch | ≤ 250 MB|
|Anzahl von Zielsprachen in einem Batch| ≤ 10 |
|Größe der Übersetzungsspeicherdatei| ≤ 10 MB|

Die Dokumentübersetzung kann nicht verwendet werden, um geschützte Dokumente zu übersetzen, z. B. Dokumente mit einem verschlüsselten Kennwort oder eingeschränktem Zugriff zum Kopieren von Inhalten.

## <a name="troubleshooting"></a>Problembehandlung

### <a name="common-http-status-codes"></a>Allgemeine HTTP-Statuscodes

| HTTP-Statuscode | BESCHREIBUNG | Mögliche Ursache |
|------------------|-------------|-----------------|
| 200 | OK | Die Anforderung wurde erfolgreich gesendet. |
| 400 | Ungültige Anforderung | Ein erforderlicher Parameter fehlt, ist leer oder Null. Oder der an einen erforderlichen oder optionalen Parameter übergebene Wert ist ungültig. Ein häufiges Problem sind zu lange Kopfzeilen. |
| 401 | Nicht autorisiert | Die Anforderung ist nicht autorisiert. Stellen Sie sicher, dass Ihr Abonnementschlüssel oder -token gültig ist und sich in der richtigen Region befindet. Achten Sie bei der Verwaltung Ihres Abonnements im Azure-Portal darauf, dass Sie die **Textübersetzungsressource** (einzelner Dienst) nutzen und _nicht_ die **Cognitive Services-Ressource** (mehrere Dienste).
| 429 | Zu viele Anforderungen | Sie haben das Kontingent oder die Rate der Anforderungen überschritten, das bzw. die für Ihr Abonnement zulässig ist. |
| 502 | Ungültiges Gateway    | Netzwerk- oder serverseitiges Problem. Kann auch auf ungültige Header hinweisen. |

## <a name="learn-more"></a>Erfahren Sie mehr

* [Textübersetzung v3 API-Referenz](../reference/v3-0-reference.md)
* [Sprachunterstützung](../language-support.md)
* [Abonnements in Azure API Management](../../../api-management/api-management-subscriptions.md).

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erstellen eines angepassten Sprachsystems mit Benutzerdefinierter Translator](../custom-translator/overview.md)
>
>
