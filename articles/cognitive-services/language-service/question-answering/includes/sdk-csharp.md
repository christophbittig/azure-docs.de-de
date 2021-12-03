---
title: 'Schnellstart: Clientbibliothek für Fragen und Antworten für .NET'
description: In dieser Schnellstartanleitung werden die ersten Schritte mit der Clientbibliothek für Fragen und Antworten für .NET gezeigt. Führen Sie die nachfolgenden Schritte zum Installieren des Pakets aus, und testen Sie den Beispielcode für grundlegende Aufgaben. Mit Fragen und Antworten können Sie einen Frage-und-Antwort-Dienst auf der Grundlage Ihrer teilweise strukturierten Inhalte bereitstellen. Bei solchen Inhalten kann es sich beispielsweise um FAQ-Dokumente, URLs und Produkthandbücher handeln.
author: mrbullwinkle
ms.author: mbullwin
ms.topic: include
ms.date: 11/11/2021
ms.openlocfilehash: e374ef91c414b9d352b3c0cddd8af59da3e699a0
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132354143"
---
Diese Schnellstartanleitung zur Clientbibliothek für Fragen und Antworten für .NET ermöglicht Folgendes:

* Erhalten einer Antwort auf der Grundlage einer Wissensdatenbank
* Erhalten einer Antwort auf der Grundlage eines Texts, den Sie zusammen mit Ihrer Frage übermitteln
* Erhalten der Zuverlässigkeitsbewertung für die Antwort auf Ihre Frage

 [API-Referenzdokumentation][questionanswering_refdocs]|[Quellcode][questionanswering_client_src] | [Paket (NuGet)][questionanswering_nuget_package]  | [Beispiele][questionanswering_samples] |

[questionanswering_nuget_package]: https://nuget.org/packages/Azure.AI.Language.
[questionanswering_refdocs]: https://docs.microsoft.com/dotnet/api/Azure.AI.Language.QuestionAnswering/
[questionanswering_client_src]: https://github.com/Azure/azure-sdk-for-net/tree/main/sdk/cognitivelanguage/Azure.AI.Language.QuestionAnswering/src/
[questionanswering_samples]: https://github.com/Azure/azure-sdk-for-net/tree/main/sdk/cognitivelanguage/Azure.AI.Language.QuestionAnswering/samples/README.md

## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/cognitive-services)
* Die [Visual Studio-IDE](https://visualstudio.microsoft.com/vs/) oder die aktuelle Version von [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* Für Fragen und Antworten wird eine [Sprachressource](https://ms.portal.azure.com/?quickstart=true#create/Microsoft.CognitiveServicesTextAnalytics) mit aktiviertem Feature „Benutzerdefinierte Fragen und Antworten“ benötigt, um einen API-Schlüssel und Endpunkt zu generieren. <!--TODO: Change link-->
    * Wählen Sie nach der Bereitstellung der Sprachressource **Zu Ressource wechseln** aus. Sie benötigen den Schlüssel und Endpunkt der von Ihnen erstellten Ressource, um eine Verbindung mit der API herstellen zu können. Der Schlüssel und der Endpunkt werden weiter unten in der Schnellstartanleitung in den Code eingefügt.
* Eine bereits vorhandene Wissensdatenbank für die Abfrage. Eine Anleitung zum Einrichten einer Wissensdatenbank finden Sie bei Bedarf unter [**Schnellstart: Fragen und Antworten**](../quickstart/sdk.md). Sie können aber auch eine Wissensdatenbank hinzufügen, die [diese URL für das Surface-Benutzerhandbuch](https://download.microsoft.com/download/7/B/1/7B10C82E-F520-4080-8516-5CF0D803EEE0/surface-book-user-guide-EN.pdf) als Datenquelle verwendet.

## <a name="setting-up"></a>Einrichten

### <a name="cli"></a>Befehlszeilenschnittstelle (CLI)

Verwenden Sie in einem Konsolenfenster (z. B. cmd, PowerShell oder Bash) den Befehl `dotnet new` zum Erstellen einer neuen Konsolen-App mit dem Namen `question-answering-quickstart`. Dieser Befehl erstellt ein einfaches C#-Projekt vom Typ „Hallo Welt“ mit einer einzelnen Quelldatei: *program.cs*.

```console
dotnet new console -n question-answering-quickstart
```

Wechseln Sie zum Ordner der neu erstellten App. Sie können die Anwendung mit folgendem Befehl erstellen:

```console
dotnet build
```

Die Buildausgabe sollte keine Warnungen oder Fehler enthalten.

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

Installieren Sie im Anwendungsverzeichnis mit dem folgenden Befehl die Clientbibliothek für benutzerdefinierte Fragen und Antworten für .NET:

```console
dotnet add package Azure.AI.Language.QuestionAnswering
```

## <a name="query-a-knowledge-base"></a>Abfragen einer Knowledge Base

#### <a name="generate-an-answer-from-a-knowledge-base"></a>Generieren einer Antwort auf der Grundlage einer Wissensdatenbank

Im folgenden Beispiel können Sie eine Wissensdatenbank mithilfe von `GetAnswers` abfragen, um eine Antwort auf Ihre Frage zu erhalten.

Aktualisieren den im Anschluss bereitgestellten Code mit Ihren eigenen Werten für die folgenden Variablen:

|Variablenname | Wert |
|--------------------------|-------------|
| `endpoint`               | Diesen Wert finden Sie im Abschnitt **Schlüssel und Endpunkt**, wenn Sie die Ressource über das Azure-Portal untersuchen. Alternativ finden Sie den Wert unter **Language Studio** > **Fragen und Antworten** > **Wissensdatenbank bereitstellen** > **Vorhersage-URL abrufen**. Beispielendpunkt: `https://southcentralus.api.cognitive.microsoft.com/`|
| `credential` | Diesen Wert finden Sie im Abschnitt **Schlüssel und Endpunkt**, wenn Sie die Ressource über das Azure-Portal untersuchen. Sie können entweder „Schlüssel1“ oder „Schlüssel2“ verwenden. Es gibt immer zwei gültige Schlüssel für die sichere Schlüsselrotation ohne Downtime. Alternativ finden Sie den Wert unter **Language Studio** > **Fragen und Antworten** > **Wissensdatenbank bereitstellen** > **Vorhersage-URL abrufen**. Der Schlüsselwert ist Teil der Beispielanforderung.|
| `projectName` | Der Name Ihres Projekts für Fragen und Antworten.|
| `deploymentName`             | Es gibt zwei mögliche Werte: `test` und `production`. `production` setzt voraus, dass Sie Ihre Wissensdatenbank über **Language Studio** > **Fragen und Antworten** > **Wissensdatenbank bereitstellen** bereitgestellt haben.|

Öffnen Sie im Projektverzeichnis die Datei *program.cs*, und ersetzen Sie den Inhalt durch den folgenden Code:

```csharp
using Azure;
using Azure.AI.Language.QuestionAnswering;
using System;

namespace question_answering
{
    class Program
    {
        static void Main(string[] args)
        {

            Uri endpoint = new Uri("https://{YOUR-ENDPOINT}.api.cognitive.microsoft.com/");
            AzureKeyCredential credential = new AzureKeyCredential("{YOUR-LANGUAGE-RESOURCE-KEY}");
            string projectName = "{YOUR-PROJECT-NAME}";
            string deploymentName = "production";

            string question = "How long should my Surface battery last?";

            QuestionAnsweringClient client = new QuestionAnsweringClient(endpoint, credential);
            QuestionAnsweringProject project = new QuestionAnsweringProject(projectName, deploymentName);

            Response<AnswersResult> response = client.GetAnswers(question, project);

            foreach (KnowledgeBaseAnswer answer in response.Value.Answers)
            {
                Console.WriteLine($"Q:{question}");
                Console.WriteLine($"A:{answer.Answer}");
            }
        }
    }
}
```

In diesem Beispiel werden die Variablen hartcodiert. In der Produktionsumgebung sollten Sie eine sichere Methode zum Speichern Ihrer Anmeldeinformationen sowie zum Zugriff darauf verwenden. Beispielsweise bietet der [Azure-Schlüsseltresor](../../../../key-vault/general/overview.md) sichere Aufbewahrung von Schlüsseln.

Gehen Sie wie folgt vor, nachdem Sie `Program.cs` mit dem obigen Code aktualisiert und die korrekten Variablenwerte angegeben haben: Führen Sie die Anwendung mit dem Befehl `dotnet run` aus dem Anwendungsverzeichnis aus.

```console
dotnet run
```

Die Antwort sieht wie folgt aus:

```console
Q: How much battery life do I have left?
A: If you want to see how much battery you have left, go to **Start  **> **Settings  **> **Devices  **> **Bluetooth & other devices  **, then find your pen. The current battery level will appear under the battery icon.
```

Wenn Sie wissen möchten, wie zuverlässig die Antwort ist, können Sie im Anschluss an die bereits vorhandenen print-Anweisungen eine weitere print-Anweisung hinzufügen:

```csharp
Console.WriteLine($"Q:{question}");
Console.WriteLine($"A:{answer.Answer}");
Console.WriteLine($"({answer.Confidence})"); // add this line
```

Wenn Sie `dotnet run` erneut ausführen, erhalten Sie ein Ergebnis mit einer Zuverlässigkeitsbewertung:

```console
Q:How much battery life do I have left?
A:If you want to see how much battery you have left, go to **Start  **> **Settings  **> **Devices  **> **Bluetooth & other devices  **, then find your pen. The current battery level will appear under the battery icon.
(0.9185)
```

Die Zuverlässigkeitsbewertung gibt einen Wert zwischen 0 und 1 zurück. Multiplizieren Sie diesen Wert mit 100, um eine Prozentangabe zu erhalten. Eine Zuverlässigkeitsbewertung von 0,9185 bedeutet also beispielsweise, dass es sich hierbei mit einer Wahrscheinlichkeit von 91,85 Prozent um die richtige Antwort auf die Frage handelt (basierend auf der Wissensdatenbank).

Wenn Sie Antworten ausschließen möchten, deren Zuverlässigkeitsbewertung unter einem bestimmten Schwellenwert liegt, können Sie mithilfe von `AnswerOptions` die Eigenschaft `ConfidenceScoreThreshold` hinzufügen.

```csharp
QuestionAnsweringClient client = new QuestionAnsweringClient(endpoint, credential);
QuestionAnsweringProject project = new QuestionAnsweringProject(projectName, deploymentName);
AnswersOptions options = new AnswersOptions(); //Add this line
options.ConfidenceThreshold = 0.95; //Add this line

Response<AnswersResult> response = client.GetAnswers(question, project, options); //Add the additional options parameter
```

Da wir aufgrund der vorherigen Ausführung des Codes wissen, dass unsere Zuverlässigkeitsbewertung `.9185` lautet, wird die [Standardantwort](../how-to/change-default-answer.md) zurückgegeben, wenn der Schwellenwert auf `.95` festgelegt wird.

```console
Q:How much battery life do I have left?
A:No good match found in KB
(0)
```

## <a name="query-text-without-a-knowledge-base"></a>Abfragen von Text ohne Wissensdatenbank

Fragen und Antworten kann mit `GetAnswersFromText` auch ohne Wissensdatenbank verwendet werden. In diesem Fall werden für Fragen und Antworten sowohl eine Frage als auch die zugehörigen Textdatensätze bereitgestellt, die nach einer Antwort durchsucht werden sollen, wenn die Anforderung übermittelt wird.

Für dieses Beispiel müssen nur die Variablen für `endpoint` und `credential` geändert werden.

```csharp
using Azure;
using Azure.AI.Language.QuestionAnswering;
using System;
using System.Collections.Generic;


namespace questionansweringcsharp
{
    class Program
    {
        static void Main(string[] args)
        {

            Uri endpoint = new Uri("https://{YOUR-ENDPOINT}.api.cognitive.microsoft.com/");
            AzureKeyCredential credential = new AzureKeyCredential("YOUR-LANGUAGE-RESOURCE-KEY");
            QuestionAnsweringClient client = new QuestionAnsweringClient(endpoint, credential);

            IEnumerable<TextDocument> records = new[]
            {
                new TextDocument("doc1", "Power and charging.It takes two to four hours to charge the Surface Pro 4 battery fully from an empty state. " +
                         "It can take longer if you're using your Surface for power-intensive activities like gaming or video streaming while you're charging it"),
                new TextDocument("doc2", "You can use the USB port on your Surface Pro 4 power supply to charge other devices, like a phone, while your Surface charges. " +
                         "The USB port on the power supply is only for charging, not for data transfer. If you want to use a USB device, plug it into the USB port on your Surface."),
            };

            AnswersFromTextOptions options = new AnswersFromTextOptions("How long does it takes to charge a surface?", records);
            Response<AnswersFromTextResult> response = client.GetAnswersFromText(options);

           foreach (TextAnswer answer in response.Value.Answers)
            {
                if (answer.Confidence > .9)
                {
                    string BestAnswer = response.Value.Answers[0].Answer;

                    Console.WriteLine($"Q:{options.Question}");
                    Console.WriteLine($"A:{BestAnswer}");
                    Console.WriteLine($"Confidence Score: ({response.Value.Answers[0].Confidence:P2})"); //:P2 converts the result to a percentage with 2 decimals of accuracy. 
                    break;
                }
                else
                {
                    Console.WriteLine($"Q:{options.Question}");
                    Console.WriteLine("No answers met the requested confidence score.");
                    break;
                }
            }

        }
    }
}
```

Ersetzen Sie vor dem Ausführen des obigen Codes `Program.cs` durch den Inhalt des obigen Skriptblocks, und ändern Sie die Variablen `endpoint` und `credential` so, dass sie der Sprachressource entsprechen, die Sie im Rahmen der Vorbereitung erstellt haben.

Hier werden alle Antworten durchlaufen, und es wird nur die Antwort mit der höchsten Zuverlässigkeitsbewertung zurückgegeben, die größer als 0,9 ist. Machen Sie sich ausführlicher mit den verfügbaren Optionen für `GetAnswersFromText` vertraut.
