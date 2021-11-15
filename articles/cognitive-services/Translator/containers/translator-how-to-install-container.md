---
title: Installieren und Ausführen von Docker-Containern für die Textübersetzungs-API
titleSuffix: Azure Cognitive Services
description: Verwenden Sie den Docker-Container für die Textübersetzungs-API, um Text zu übersetzen.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: how-to
ms.date: 10/14/2021
ms.author: lajanuar
recommendations: false
keywords: Lokal, Docker, Container, Identifizieren
ms.openlocfilehash: dbee487212f56eb5c99d3cc785cf8a2205280fcf
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131426143"
---
# <a name="install-and-run-translator-containers-preview"></a>Installieren und Ausführen von Containern für die Textübersetzung (Vorschau)

Mithilfe von Containern können Sie verschiedene Features des Textübersetzungsdiensts in Ihrer eigenen Umgebung ausführen. Container eignen sich hervorragend für bestimmte Sicherheits- und Datengovernanceanforderungen. In diesem Artikel erfahren Sie, wie Sie einen Container für die Textübersetzung herunterladen, installieren und ausführen.

Mit Containern für die Textübersetzung können Sie eine Anwendungsarchitektur für die Textübersetzung erstellen, die sowohl von widerstandsfähigen Cloudfunktionen als auch von der Edgeposition profitieren kann.

Sehen Sie sich die Liste der [unterstützten Sprachen](../language-support.md) bei der Verwendung von Übersetzercontainern an. 

> [!IMPORTANT]
>
> * Der Container für die Textübersetzung befindet sich in der beschränkten Vorschauversion. Um ihn zu verwenden, müssen Sie eine Onlineanforderung übermitteln und ihn genehmigen lassen. Weitere Informationen finden Sie nachfolgend unter [Anfordern der Genehmigung für die Containerausführung](#request-approval-to-run-container).
> * Der Container für die Textübersetzung unterstützt im Vergleich zu den Cloudangeboten eingeschränkte Features.  Weitere Informationen finden Sie unter **Container: Übersetzen**.

<!-- markdownlint-disable MD033 -->

## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen ein aktives [**Azure-Konto**](https://azure.microsoft.com/free/cognitive-services/), um zu beginnen.  Falls Sie noch kein Konto haben, können Sie ein [**kostenloses Konto erstellen**](https://azure.microsoft.com/free/).

Außerdem benötigen Sie Folgendes:

| Erforderlich | Zweck |
|--|--|
| Kenntnisse zu Docker | <ul><li>Sie sollten über Grundkenntnisse der Konzepte von Docker, einschließlich Registrierungen, Repositorys, Container und Containerimages, verfügen und die grundlegenden [Begriffe und Befehle](/dotnet/architecture/microservices/container-docker-introduction/docker-terminology) für `docker` kennen.</li></ul> |
| Docker-Engine | <ul><li>Die Docker-Engine muss auf einem [Hostcomputer](#host-computer) installiert sein. Für die Docker-Umgebung stehen Konfigurationspakete für [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) und [Linux](https://docs.docker.com/engine/installation/#supported-platforms) zur Verfügung. Eine Einführung in Docker und Container finden Sie in der [Docker-Übersicht](https://docs.docker.com/engine/docker-overview/).</li><li> Docker muss so konfiguriert werden, dass die Container eine Verbindung mit Azure herstellen und Abrechnungsdaten an Azure senden können. </li><li> Unter **Windows** muss Docker auch für die Unterstützung von **Linux**-Containern konfiguriert werden.</li></ul> |
| Ressource für die Textübersetzung | <ul><li>Eine Ressource für die [Textübersetzung](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation) in Azure mit einer anderen Region als „global“, zugeordnetem API-Schlüssel und Endpunkt-URI. Beide Werte sind erforderlich, um den Container zu starten. Sie befinden sich auf der Übersichtsseite der Ressource.</li></ul>|
|||

|Optional|Zweck|
|---------|----------|
|Azure CLI (Befehlszeilenschnittstelle) |<ul><li> Mit der [Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli) können Sie eine Reihe von Onlinebefehlen verwenden, um Azure-Ressourcen zu erstellen und zu verwalten. Sie ist für die Installation in Windows-, macOS- und Linux-Umgebungen verfügbar und kann in einem Docker-Container und in Azure Cloud Shell ausgeführt werden.</li></ul> |
|||

## <a name="required-elements"></a>Erforderliche Elemente

Alle Cognitive Services-Container erfordern drei primäre Elemente:

* **EULA-Annahmeeinstellung**. Endbenutzer-Lizenzbedingungen (EULA), für die der Wert `Eula=accept` festgelegt ist.

* **API-Schlüssel** und **Endpoint-URL**.  Der API-Schlüssel wird verwendet, um den Container zu starten. Sie können die Werte für API-Schlüssel und Endpunkt-URL abrufen, indem Sie zur Seite _Schlüssel und Endpunkt_ der Textübersetzungsressource navigieren und das Symbol `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span> auswählen.

> [!IMPORTANT]
>
> * Abonnementschlüssel werden für den Zugriff auf Ihre Cognitive Service-API verwendet. Geben Sie Ihre Schlüssel nicht weiter. Speichern Sie diese beispielsweise sicher mit Azure Key Vault. Es wird außerdem empfohlen, diese Schlüssel regelmäßig neu zu generieren. Für einen API-Aufruf ist nur ein Schlüssel erforderlich. Beim erneuten Generieren des ersten Schlüssels können Sie den zweiten Schlüssel für kontinuierlichen Zugriff auf den Dienst verwenden.

## <a name="host-computer"></a>Hostcomputer

[!INCLUDE [Host Computer requirements](../../../../includes/cognitive-services-containers-host-computer.md)]

## <a name="container-requirements-and-recommendations"></a>Containeranforderungen und -empfehlungen

In der folgenden Tabelle werden die minimalen und empfohlenen Spezifikationen für die Textübersetzungscontainer beschrieben. Mindestens 2 Gigabyte (GB) Arbeitsspeicher sind erforderlich, und jede CPU muss eine Geschwindigkeit von mindestens 2,6 Gigahertz (GHz) aufweisen. und Arbeitsspeicher in Gigabyte (GB), der für jede Textübersetzung reserviert wird. In der folgenden Tabelle werden die minimale und empfohlene Zuordnung von Ressourcen für jeden Textübersetzungscontainer beschrieben.

| Container | Minimum |Empfohlen | Sprachpaar |
|-----------|---------|---------------|----------------------|
| Verbundene Textübersetzung |2 Kerne, 2 GB Arbeitsspeicher |4 Kerne, 8 GB Arbeitsspeicher | 4 |
|||

Es wird empfohlen, für jedes Sprachpaar 2 GB an Arbeitsspeicher bereitzustellen. Standardmäßig verfügt der Offlinecontainer für die Textübersetzung über vier Sprachpaare. Kernanzahl und Arbeitsspeicher entsprechen den Einstellungen `--cpus` und `--memory`, die im Rahmen des Befehls `docker run` verwendet werden.

> [!NOTE]
>
> * CPU-Kern und Arbeitsspeicher entsprechen den Einstellungen `--cpus` und `--memory`, die von Docker als Bestandteil des run-Befehls verwendet werden.
>
> * Die Mindestanforderungen und empfohlenen Spezifikationen basieren auf Docker-Grenzwerten und nicht auf den Ressourcen des Hostcomputers.

## <a name="request-approval-to-run-container"></a>Anfordern der Genehmigung für die Containerausführung

Vervollständigen und übermitteln Sie den [**Azure Cognitive Services-Antrag für beschränkte Dienste**](https://aka.ms/csgate-translator), um den Zugriff auf den Container anzufordern.

[!INCLUDE [Request access to public preview](../../../../includes/cognitive-services-containers-request-access.md)]

## <a name="get-container-images-with-docker-commands"></a>Abrufen von Containerimages mit **Docker-Befehlen**

> [!IMPORTANT]
>
> * In den Docker-Befehlen in den folgenden Abschnitten wird der umgekehrte Schrägstrich (`\`) als Zeilenfortsetzungszeichen verwendet. Ersetzen oder entfernen Sie diesen je nach den Anforderungen des Hostbetriebssystems.
> * Die Optionen `EULA`, `Billing` und `ApiKey` müssen angegeben werden, um den Container auszuführen, andernfalls wird der Container nicht gestartet.

Verwenden Sie den Befehl [docker run](https://docs.docker.com/engine/reference/commandline/run/), um ein Containerimage aus Microsoft Container Registry herunterzuladen und auszuführen.

```Docker
docker run --rm -it -p 5000:80 --memory 12g --cpus 4 \
-v /mnt/d/TranslatorContainer:/usr/local/models \
-e apikey={API_KEY} \
-e eula=accept \
-e billing={ENDPOINT_URI} \
-e Languages=en,fr,es,ar,ru  \
mcr.microsoft.com/azure-cognitive-services/translator/text-translation
```

Der obige Befehl führt Folgendes aus:

* Lädt einen Container für die Textübersetzung aus dem Containerimage herunter und führt ihn aus.
* Ordnet 12 Gigabyte (GB) Arbeitsspeicher und vier CPU-Kerne zu.
* Verfügbarmachen des TCP-Ports 5000 und Zuweisen einer Pseudo-TTY-Verbindung für den Container
* Akzeptiert die Endbenutzer-Lizenzbedingungen (EULA).
* Konfiguriert den Abrechnungsendpunkt.
* Lädt Übersetzungsmodelle für die Sprachen Englisch, Französisch, Spanisch, Arabisch und Russisch herunter.
* Entfernt den Container automatisch, nachdem er beendet wurde. Das Containerimage ist auf dem Hostcomputer weiterhin verfügbar.

### <a name="run-multiple-containers-on-the-same-host"></a>Ausführen mehrerer Container auf dem gleichen Host

Wenn Sie beabsichtigen, mehrere Container mit offengelegten Ports auszuführen, stellen Sie sicher, dass jeder Container mit einem anderen offengelegten Port ausgeführt wird. Führen Sie beispielsweise den ersten Container an Port 5000 und den zweiten Container an Port 5001 aus.

Sie können diesen Container und einen anderen Azure Cognitive Services-Container zusammen auf dem Host ausführen. Sie können auch mehrere Container desselben Cognitive Services-Containers ausführen.

## <a name="query-the-containers-translator-endpoint"></a>Abfragen des Textübersetzungsendpunkts des Containers

 Der Container stellt eine REST-basierte Endpunkt-API für die Textübersetzung bereit. Hier folgt eine Beispielanforderung:

```curl
curl -X POST "http://localhost:5000/translate?api-version=3.0&from=en&to=zh-HANS"
    -H "Content-Type: application/json" -d "[{'Text':'Hello, what is your name?'}]"
```

> [!NOTE]
> Wenn Sie die cURL POST-Anforderung ausführen, bevor der Container bereit ist, erhalten Sie eine Antwort vom Typ *Der Dienst ist vorübergehend nicht verfügbar*. Warten Sie, bis der Container bereit ist, und versuchen Sie es dann erneut.

## <a name="stop-the-container"></a>Beenden des Containers

[!INCLUDE [How to stop the container](../../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshoot"></a>Problembehandlung

### <a name="validate-that-a-container-is-running"></a>Überprüfen auf aktive Container

Es gibt mehrere Möglichkeiten zu überprüfen, ob ein Container aktiv ist:

* Der Container stellt eine Homepage unter `\` als visuelle Validierung zur Verfügung, dass der Container ausgeführt wird.

* Sie können Ihren bevorzugten Webbrowser öffnen und zu der externen IP-Adresse und dem verfügbar gemachten Port des betreffenden Containers navigieren. Verwenden Sie die verschiedenen URLs der untenstehenden Anforderungen, um zu überprüfen, ob der Container ausgeführt wird. Die unten aufgeführten Beispiel-URLs für Anforderungen sind `http://localhost:5000`, aber Ihr spezifischer Container kann variieren. Denken Sie daran, dass Sie zur **externen IP-Adresse** Ihres Containers und zum verfügbar gemachten Port navigieren.

| Anforderungs-URL | Zweck |
|--|--|
| `http://localhost:5000/` | Der Container stellt eine Startseite bereit. |
| `http://localhost:5000/ready` | Mit GET angefordert. Dies ermöglicht eine Überprüfung, dass der Container bereit ist, eine Abfrage des Modells zu akzeptieren.  Diese Anforderung kann für [Live- und Bereitschaftstests](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) von Kubernetes verwendet werden. |
| `http://localhost:5000/status` | Mit GET angefordert.  Dies ermöglicht eine Überprüfung, ob der zum Starten des Containers verwendete API-Schlüssel gültig ist, ohne dass eine Endpunktabfrage veranlasst wird. Diese Anforderung kann für [Live- und Bereitschaftstests](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/) von Kubernetes verwendet werden. |
| `http://localhost:5000/swagger` | Der Container stellt eine umfassende Dokumentation für die Endpunkte sowie die Funktion **Jetzt ausprobieren** bereit. Diese Funktion ermöglicht Ihnen die Eingabe Ihrer Einstellungen in einem webbasierten HTML-Formular, sodass Sie die Abfrage ausführen können, ohne Code schreiben zu müssen. Nach der Rückgabe der Abfrage wird ein cURL-Beispielbefehl bereitgestellt, der das erforderliche Format für HTTP-Header und -Text veranschaulicht. |

:::image type="content" source="../../../../includes/media/cognitive-services-containers-api-documentation/container-webpage.png" alt-text="Homepage des Containers":::

[!INCLUDE [Diagnostic container](../../containers/includes/diagnostics-container.md)]

## <a name="text-translation-code-samples"></a>Codebeispiele für die Textübersetzung

### <a name="translate-text-with-swagger"></a>Übersetzen von Text mit Swagger

#### <a name="english-leftrightarrow-german"></a>Englisch &leftrightarrow; Deutsch

Navigieren Sie zur Swagger-Seite: `<http://localhost:5000/swagger/index.html>`.

1. Wählen Sie **POST /translate** aus.
1. Klicken Sie auf **Try it out** (Ausprobieren).
1. Geben Sie für den **From**-Parameter den Wert `en` ein.
1. Geben Sie für den **To**-Parameter den Wert `de` ein.
1. Geben Sie für den **api-version**-Parameter den Wert `3.0` ein.
1. Ersetzen Sie unter **texts** den folgenden `string`-Wert durch den unten folgenden JSON-Code:

```json
  [
        {
            "text": "hello, how are you"
        }
  ]
```

Klicken Sie auf **Execute** (Ausführen). Die Übersetzungen werden im **Response Body** (Antworttext) ausgegeben. Es sollte eine Antwort ähnlich der folgenden angezeigt werden:

```json
"translations": [
      {
          "text": "hallo, wie geht es dir",
          "to": "de"
      }
    ]
```

### <a name="translate-text-with-python"></a>Übersetzen von Text mit Python

```python
import requests, json

url = 'http://localhost:5000/translate?api-version=3.0&from=en&to=fr'
headers = { 'Content-Type': 'application/json' }
body = [{ 'text': 'Hello, how are you' }]

request = requests.post(url, headers=headers, json=body)
response = request.json()

print(json.dumps(
    response,
    sort_keys=True,
     indent=4,
     ensure_ascii=False,
     separators=(',', ': ')))
```

### <a name="translate-text-with-cnet-console-app"></a>Übersetzen von Text mit der C#/.NET-Konsolen-App

Starten Sie Visual Studio, und erstellen Sie eine neue Konsolenanwendung. Bearbeiten Sie die `*.csproj`-Datei, um den `<LangVersion>7.1</LangVersion>`-Knoten hinzuzufügen – hiermit wird C# 7.1 angegeben. Fügen Sie das NuGet-Paket [Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/) in der Version 11.0.2 hinzu.

Ersetzen Sie in `Program.cs` den gesamten vorhandenen Code durch den folgenden:

```csharp
using Newtonsoft.Json;
using System;
using System.Net.Http;
using System.Text;
using System.Threading.Tasks;

namespace TranslateContainer
{
    class Program
    {
        const string ApiHostEndpoint = "http://localhost:5000";
        const string TranslateApi = "/translate?api-version=3.0&from=en&to=de";

        static async Task Main(string[] args)
        {
            var textToTranslate = "Sunny day in Seattle";
            var result = await TranslateTextAsync(textToTranslate);

            Console.WriteLine(result);
            Console.ReadLine();
        }

        static async Task<string> TranslateTextAsync(string textToTranslate)
        {
            var body = new object[] { new { Text = textToTranslate } };
            var requestBody = JsonConvert.SerializeObject(body);

            var client = new HttpClient();
            using (var request =
                new HttpRequestMessage
                {
                    Method = HttpMethod.Post,
                    RequestUri = new Uri($"{ApiHostEndpoint}{TranslateApi}"),
                    Content = new StringContent(requestBody, Encoding.UTF8, "application/json")
                })
            {
                // Send the request and await a response.
                var response = await client.SendAsync(request);

                return await response.Content.ReadAsStringAsync();
            }
        }
    }
}
```

## <a name="summary"></a>Zusammenfassung

In diesem Artikel haben Sie die Konzepte und den Workflow zum Herunterladen, Installieren und Ausführen von Containern für die Textübersetzung kennengelernt. Jetzt wissen Sie Folgendes:

* Die Textübersetzung stellt Linux-Container für Docker bereit.
* Containerimages werden aus der Containerregistrierung heruntergeladen und in Docker ausgeführt.
* Sie können die REST-API zum Aufrufen des Vorgangs „translate“ (Übersetzen) im Container für die Textübersetzung verwenden, indem Sie den Host-URI des Containers angeben.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Weitere Informationen zu Azure Cognitive Services-Containern](../../containers/index.yml?context=%2fazure%2fcognitive-services%2ftranslator%2fcontext%2fcontext)
