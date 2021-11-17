---
title: Informationen zum Modellparser „Digital Twins“ | Microsoft-Dokumentation
description: Hier erhalten Sie Informationen, wie Sie als Entwickler den DTDL-Parser zur Überprüfung von Modellen verwenden.
author: rido-min
ms.author: rmpablos
ms.date: 10/21/2020
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-develop
services: iot-develop
ms.openlocfilehash: eec6c2e70ff11d9da162db79867279abb10d4506
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132399274"
---
# <a name="understand-the-digital-twins-model-parser"></a>Informationen zum Modellparser „Digital Twins“

Die Digital Twins Definition Language (DTDL) wird unter [Digital Twins Definition Language](https://github.com/Azure/opendigitaltwins-dtdl) beschrieben. Benutzer können das _Digital Twins Model Parser_-NuGet-Paket verwenden, um ein Modell zu überprüfen und abzufragen, das in mehreren Dateien definiert ist.

## <a name="install-the-dtdl-model-parser"></a>Installieren des DTDL-Modellparsers

Der Parser steht unter NuGet.org mit folgender ID zur Verfügung: [Microsoft.Azure.DigitalTwins.Parser](https://www.nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser). Verwenden Sie zur Installation des Parsers einen kompatiblen NuGet-Paket-Manager, z. B. den in Visual Studio oder in der `dotnet`-CLI.

```bash
dotnet add package Microsoft.Azure.DigitalTwins.Parser
```

> [!NOTE]
> Zum Zeitpunkt der Artikelerstellung lautet die Parser-Version `3.12.7`.

## <a name="use-the-parser-to-validate-a-model"></a>Verwenden des Parsers zur Überprüfung eines Modells

Ein Modell kann aus mehreren in JSON-Dateien beschriebenen Schnittstellen bestehen. Sie können den Parser verwenden, um alle Dateien in einem bestimmten Ordner zu laden, und Sie können den Parser verwenden, um alle Dateien als Ganzes zu überprüfen, einschließlich zwischen den Dateien enthaltenen Verweisen:

1. Erstellen Sie eine `IEnumerable<string>`-Schnittstelle mit einer Liste aller Modellinhalte:

    ```csharp
    using System.IO;

    string folder = @"c:\myModels\";
    string filespec = "*.json";

    List<string> modelJson = new List<string>();
    foreach (string filename in Directory.GetFiles(folder, filespec))
    {
        using StreamReader modelReader = new StreamReader(filename);
        modelJson.Add(modelReader.ReadToEnd());
    }
    ```

1. Instanziieren Sie `ModelParser`, und rufen Sie `ParseAsync` auf:

    ```csharp
    using Microsoft.Azure.DigitalTwins.Parser;

    ModelParser modelParser = new ModelParser();
    IReadOnlyDictionary<Dtmi, DTEntityInfo> parseResult = await modelParser.ParseAsync(modelJson);
    ```

1. Überprüfen Sie, ob Überprüfungsfehler auftreten. Wenn der Parser Fehler entdeckt, wird `ParsingException` mit einer Liste der Fehler zurückgegeben:

    ```csharp
    try
    {
        IReadOnlyDictionary<Dtmi, DTEntityInfo> parseResult = await modelParser.ParseAsync(modelJson);
    }
    catch (ParsingException pex)
    {
        Console.WriteLine(pex.Message);
        foreach (var err in pex.Errors)
        {
            Console.WriteLine(err.PrimaryID);
            Console.WriteLine(err.Message);
        }
    }
    ```

1. Untersuchen Sie `Model`. Wenn die Überprüfung erfolgreich war, können Sie die Modellparser-API verwenden, um das Modell zu untersuchen. Der folgende Codeausschnitt zeigt, wie Sie alle geparsten Modelle durchlaufen, und die vorhandenen Eigenschaften anzeigen:

    ```csharp
    foreach (var item in parseResult)
    {
        Console.WriteLine($"\t{item.Key}");
        Console.WriteLine($"\t{item.Value.DisplayName?.Values.FirstOrDefault()}");
    }
    ```

## <a name="next-steps"></a>Nächste Schritte

Die in diesem Artikel erläuterte Modellparser-API ermöglicht es in vielen Szenarios, Aufgaben zu automatisieren oder zu überprüfen, die von DTDL-Modellen abhängig sind. Sie können beispielsweise dynamisch eine Benutzeroberfläche aus den Informationen im Modell erstellen.
