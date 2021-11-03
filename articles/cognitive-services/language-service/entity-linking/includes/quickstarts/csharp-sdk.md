---
title: 'Schnellstart: Entitätsverknüpfungs-Clientbibliothek für C#'
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: include
ms.date: 11/02/2021
ms.author: aahi
ms.custom: ignite-fall-2021
ms.openlocfilehash: f6f7a53a0d3b0d5f99aadadfee59e77102a68f42
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131029979"
---
[Referenzdokumentation](/dotnet/api/azure.ai.textanalytics?preserve-view=true&view=azure-dotnet) | [Quellcode der Bibliothek](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics) | [Paket (NuGet)](https://www.nuget.org/packages/Azure.AI.TextAnalytics/5.1.0) | [Weitere Beispiele](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics/samples)


## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/cognitive-services)
* [Visual Studio-IDE](https://visualstudio.microsoft.com/vs/)
* Sobald Sie über Ihr Azure-Abonnement verfügen, sollten Sie im Azure-Portal <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="Erstellen einer Sprachressource"  target="_blank">eine Sprachressource erstellen</a>, um Ihren Schlüssel und Endpunkt abzurufen.  Klicken Sie nach Abschluss der Bereitstellung auf **Zu Ressource wechseln**.
    * Sie benötigen den Schlüssel und Endpunkt der von Ihnen erstellten Ressource, um Ihre Anwendung mit der API zu verbinden. Der Schlüssel und der Endpunkt werden weiter unten in der Schnellstartanleitung in den Code eingefügt.
    * Sie können den kostenlosen Tarif (`F0`) verwenden, um den Dienst zu testen, und später für die Produktion auf einen kostenpflichtigen Tarif upgraden.
* Sie benötigen eine Sprachressource des Standard-Tarifs (S), um die Analysefunktion verwenden zu können.

## <a name="setting-up"></a>Einrichten

### <a name="create-a-new-net-core-application"></a>Erstellen einer neuen .NET Core-Anwendung

Erstellen Sie über die Visual Studio-IDE eine neue .NET Core-Konsolenanwendung. Dadurch wird ein Projekt vom Typ „Hallo Welt“ mit einer einzelnen C#-Quelldatei (*program.cs*) erstellt.

Installieren Sie die Clientbibliothek, indem Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf die Projektmappe klicken und **NuGet-Pakete verwalten** auswählen. Wählen Sie im daraufhin geöffneten Paket-Manager die Option **Durchsuchen** aus, und suchen Sie nach `Azure.AI.TextAnalytics`. Wählen Sie die Version `5.1.0` und anschließend **Installieren** aus. Sie können auch die [Paket-Manager-Konsole](/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package) verwenden.

## <a name="code-example"></a>Codebeispiel

Kopieren Sie den folgenden Code in die Datei *Program.cs*. Denken Sie daran, die Variable `key` durch den Schlüssel für Ihre Ressource und die Variable `endpoint` durch den Endpunkt für Ihre Ressource zu ersetzen. 

[!INCLUDE [find the key and endpoint for a resource](../../../includes/find-azure-resource-info.md)]

```csharp
using Azure;
using System;
using System.Globalization;
using Azure.AI.TextAnalytics;

namespace EntityLinkingExample
{
    class Program
    {
        private static readonly AzureKeyCredential credentials = new AzureKeyCredential("replace-with-your-key-here");
        private static readonly Uri endpoint = new Uri("replace-with-your-endpoint-here");
        
        // Example method for recognizing entities and providing a link to an online data source
        static void EntityLinkingExample(TextAnalyticsClient client)
        {
            var response = client.RecognizeLinkedEntities(
                "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, " +
                "to develop and sell BASIC interpreters for the Altair 8800. " +
                "During his career at Microsoft, Gates held the positions of chairman, " +
                "chief executive officer, president and chief software architect, " +
                "while also being the largest individual shareholder until May 2014.");
            Console.WriteLine("Linked Entities:");
            foreach (var entity in response.Value)
            {
                Console.WriteLine($"\tName: {entity.Name},\tID: {entity.DataSourceEntityId},\tURL: {entity.Url}\tData Source: {entity.DataSource}");
                Console.WriteLine("\tMatches:");
                foreach (var match in entity.Matches)
                {
                    Console.WriteLine($"\t\tText: {match.Text}");
                    Console.WriteLine($"\t\tScore: {match.ConfidenceScore:F2}\n");
                }
            }
        }

        static void Main(string[] args)
        {
            var client = new TextAnalyticsClient(endpoint, credentials);
            EntityLinkingExample(client);

            Console.Write("Press any key to exit.");
            Console.ReadKey();
        }

    }
}

```

### <a name="output"></a>Output

```console
Linked Entities:
    Name: Microsoft,        ID: Microsoft,  URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
    Matches:
            Text: Microsoft
            Score: 0.55

            Text: Microsoft
            Score: 0.55

    Name: Bill Gates,       ID: Bill Gates, URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
    Matches:
            Text: Bill Gates
            Score: 0.63

            Text: Gates
            Score: 0.63

    Name: Paul Allen,       ID: Paul Allen, URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
    Matches:
            Text: Paul Allen
            Score: 0.60

    Name: April 4,  ID: April 4,    URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
    Matches:
            Text: April 4
            Score: 0.32

    Name: BASIC,    ID: BASIC,      URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
    Matches:
            Text: BASIC
            Score: 0.33

    Name: Altair 8800,      ID: Altair 8800,        URL: https://en.wikipedia.org/wiki/Altair_8800  Data Source: Wikipedia
    Matches:
            Text: Altair 8800
            Score: 0.88
```
