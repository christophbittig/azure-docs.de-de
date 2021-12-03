---
title: Verwenden des Endpunkts der Vorhersage für das programmgesteuerte Testen von Bildern mit einer Klassifizierung – Custom Vision
titleSuffix: Azure Cognitive Services
description: API-Nutzung für das programmgesteuerte Testen von Bildern mit Ihrer Custom Vision Service-Klassifizierung
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 10/27/2021
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: 99e2fbce479b575759a442f9dc278723adb2b25f
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132062373"
---
# <a name="call-the-prediction-api"></a>Aufrufen der Vorhersage-API

Nachdem Sie Ihr Modell trainiert haben, können Sie Bilder programmgesteuert testen, indem Sie sie an den Endpunkt der Vorhersage-API senden. In diesem Leitfaden erfahren Sie, wie Sie die Vorhersage-API zum Bewerten eines Bilds aufrufen. Sie erfahren mehr über die verschiedenen Möglichkeiten für die Konfiguration dieser API entsprechend Ihren Anforderungen.


> [!NOTE]
> Dieses Dokument veranschaulicht die Verwendung der .NET-Clientbibliothek für C#, um ein Bild an die Vorhersage-API zu übermitteln. Weitere Informationen und Beispiele finden Sie in der [Vorhersage-API-Referenz](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c15).

## <a name="setup"></a>Einrichten

### <a name="publish-your-trained-iteration"></a>Veröffentlichen der trainierten Iteration

Wählen Sie auf der [Custom Vision-Webseite](https://customvision.ai) Ihr Projekt und dann die Registerkarte __Leistung__ aus.

Um Bilder an die Vorhersage-API zu senden, müssen Sie zuerst Ihre Iteration für die Vorhersage veröffentlichen. Dazu wählen Sie __Veröffentlichen__ aus und geben einen Namen für die veröffentlichte Iteration an. Auf diese Weise wird Ihr Modell für die Vorhersage-API Ihrer Azure-Ressource von Custom Vision zugänglich.

![Die Registerkarte „Leistung“ wird mit einem roten Rechteck um die Schaltfläche „Veröffentlichen“ angezeigt.](./media/use-prediction-api/unpublished-iteration.png)

Sobald das Modell erfolgreich veröffentlicht wurde, wird neben der Iteration in der linken Seitenleiste die Bezeichnung „Veröffentlicht“ angezeigt. Ihr Name wird in der Beschreibung der Iteration angezeigt.

![Die Registerkarte „Leistung“ wird mit einem roten Rechteck um die Bezeichnung „Veröffentlicht“ und den Namen der veröffentlichten Iteration angezeigt.](./media/use-prediction-api/published-iteration.png)

### <a name="get-the-url-and-prediction-key"></a>Abrufen der Vorhersage-URL und des Vorhersage-Schlüssels

Nachdem das Modell veröffentlicht wurde, können Sie die erforderlichen Informationen abrufen, indem Sie __Vorhersage-URL__ auswählen. Dadurch wird ein Dialogfeld mit Informationen zur Verwendung der Vorhersage-API geöffnet, einschließlich __Vorhersage-URL__ und __Vorhersage-Schlüssel__.

![Die Registerkarte „Leistung“ wird mit einem roten Rechteck um die Schaltfläche „Vorhersage-URL“ angezeigt.](./media/use-prediction-api/published-iteration-prediction-url.png)

![Die Registerkarte „Leistung“ wird mit einem roten Rechteck um den Wert „Vorhersage-URL“ für die Verwendung einer Bilddatei und um den Wert „Vorhersage-Schlüssel“ angezeigt.](./media/use-prediction-api/prediction-api-info.png)

## <a name="submit-data-to-the-service"></a>Übermitteln von Daten an den Dienst

In diesem Leitfaden wird davon ausgegangen, dass Sie bereits ein **[CustomVisionPredictionClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.prediction.customvisionpredictionclient?view=azure-dotnet-preview)** -Objekt namens `predictionClient` mit Ihrem Custom Vision-Vorhersageschlüssel und einer Endpunkt-URL erstellt haben. Anweisungen zum Einrichten dieses Features finden Sie in einem der [Schnellstarts](quickstarts/image-classification.md).

In diesem Leitfaden verwenden Sie ein lokales Bild. Laden Sie daher ein Bild herunter, das Sie an Ihr trainiertes Modell übermitteln möchten. Der folgende Code fordert den Benutzer auf, einen lokalen Pfad anzugeben, und ruft den Bytestream der Datei unter diesem Pfad ab.

```csharp
Console.Write("Enter image file path: ");
string imageFilePath = Console.ReadLine();
byte[] byteData = GetImageAsByteArray(imageFilePath);
```

Nehmen Sie die folgenden Hilfsmethoden auf:

```csharp
private static byte[] GetImageAsByteArray(string imageFilePath)
{
    FileStream fileStream = new FileStream(imageFilePath, FileMode.Open, FileAccess.Read);
    BinaryReader binaryReader = new BinaryReader(fileStream);
    return binaryReader.ReadBytes((int)fileStream.Length);
}
```

Die Methode **[ClassifyImageAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.prediction.customvisionpredictionclientextensions.classifyimageasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_CustomVision_Prediction_CustomVisionPredictionClientExtensions_ClassifyImageAsync_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Prediction_ICustomVisionPredictionClient_System_Guid_System_String_System_IO_Stream_System_String_System_Threading_CancellationToken_)** verwendet die Projekt-ID und das lokal gespeicherte Bild und bewertet das Bild anhand des angegebenen Modells.

```csharp
// Make a prediction against the new project
Console.WriteLine("Making a prediction:");
var result = predictionApi.ClassifyImageAsync(project.Id, publishedModelName, byteData);
```

## <a name="determine-how-to-process-the-data"></a>Festlegen, wie die Daten verarbeitet werden sollen

Sie können optional konfigurieren, wie der Dienst den Bewertungsvorgang ausführt, indem Sie alternative Methoden auswählen. (Sehen Sie sich die Methoden der **[CustomVisionPredictionClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.prediction.customvisionpredictionclient?view=azure-dotnet)** -Klasse an.) 

Sie können der Einfachheit halber eine nicht asynchrone Version der oben genannten Methode verwenden. Dies kann jedoch dazu führen, dass das Programm für einen beträchtlichen Zeitraum gesperrt wird.

Bei den Methoden vom Typ **-WithNoStore** wird vorausgesetzt, dass der Dienst das Vorhersagebild nach Abschluss der Vorhersage nicht beibehält. Normalerweise behält der Dienst diese Bilder bei, sodass Sie sie als Trainingsdaten für zukünftige Iterationen Ihres Modells hinzufügen können.

Mit den Methoden vom Typ **-WithHttpMessages** wird die unformatierte HTTP-Antwort des API-Aufrufs zurückgegeben.

## <a name="get-results-from-the-service"></a>Abrufen von Ergebnissen aus dem Dienst

Der Dienst gibt Ergebnisse in Form eines **[ImagePrediction](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.prediction.models.imageprediction?view=azure-dotnet)** -Objekts zurück. Die Eigenschaft **Predictions** enthält eine Liste von **[PredictionModel](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.prediction.models.predictionmodel?view=azure-dotnet)** -Objekten, die jeweils eine einzelne Objektvorhersage darstellen. Sie enthalten den Namen der Bezeichnung und die Koordinaten des Begrenzungsrahmens, an denen das Objekt im Bild erkannt wurde. Ihre App kann diese Daten dann analysieren, um beispielsweise das Bild mit beschrifteten Objektfeldern auf einem Bildschirm anzuzeigen. 

## <a name="next-steps"></a>Nächste Schritte

In dieser Anleitung haben Sie erfahren, wie Sie Bilder an Ihren benutzerdefinierten Bildklassifizierer bzw. an die Bilderkennung senden und eine programmgesteuerte Antwort mit dem C# SDK erhalten. Als Nächstes erfahren Sie, wie Sie End-to-End-Szenarien mit C# abschließen oder die ersten Schritte mit einem SDK für eine andere Sprache durchführen können.

* [Schnellstart: Erstellen eines Bildklassifizierungsprojekts mit der Custom Vision-Clientbibliothek oder der REST-API](quickstarts/image-classification.md)
