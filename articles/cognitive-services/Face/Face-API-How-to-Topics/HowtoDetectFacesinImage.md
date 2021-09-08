---
title: 'Aufrufen der Erkennungs-API: Gesichtserkennung'
titleSuffix: Azure Cognitive Services
description: In diesem Leitfaden wird gezeigt, wie mithilfe der Gesichtserkennung aus einem bestimmten Bild Attribute wie Alter, Emotionen oder Kopfhaltung extrahiert werden können.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 08/04/2021
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: 1fe1cddcbd194ec4b8ca25edfc4907631cb18df1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122355685"
---
# <a name="call-the-detect-api"></a>Aufrufen der Erkennungs-API

In diesem Leitfaden wird gezeigt, wie mithilfe der Gesichtserkennungs-API aus einem bestimmten Bild Attribute wie Alter, Emotionen oder Kopfhaltung extrahiert werden können. Sie erfahren, welche verschiedenen Möglichkeiten Sie für die Konfiguration dieser API entsprechend Ihren Anforderungen haben.

Die Codeausschnitte in diesem Leitfaden wurden in C# unter Verwendung der Clientbibliothek für die Gesichtserkennung von Azure Cognitive Services geschrieben. Die gleiche Funktionalität ist auch über die [REST-API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) verfügbar.


## <a name="setup"></a>Einrichten

Für diesen Leitfaden wird davon ausgegangen, dass Sie bereits ein [FaceClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient)-Objekt namens `faceClient` mit einem Gesichtserkennungsabonnementschlüssel und einer Gesichtserkennungs-Endpunkt-URL erstellt haben. Anweisungen zum Einrichten dieses Feature finden Sie in den folgenden Schnellstarts.

## <a name="submit-data-to-the-service"></a>Übermitteln von Daten an den Dienst

Um Gesichter zu suchen und ihre Positionen auf einem Bild abzurufen, rufen Sie die Methoden [DetectWithUrlAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync) oder [DetectWithStreamAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync) auf. **DetectWithUrlAsync** akzeptiert eine URL-Zeichenfolge als Eingabe, und **DetectWithStreamAsync** akzeptiert den unformatierten Bytestream eines Bilds als Eingabe.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="basic1":::

Sie können die zurückgegebenen [DetectedFace](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface)-Objekte mit der zugehörigen eindeutigen ID sowie ein Rechteck (Gesichtsrahmen) abfragen, mit dem die Pixelkoordinaten des Gesichts angegeben werden. Auf diese Weise können Sie feststellen, welche Gesichts-ID welchem Gesicht im ursprünglichen Bild zugeordnet ist.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="basic2":::

Weitere Informationen dazu, wie Sie die Position und die Dimensionen des Gesichts analysieren, finden Sie unter [FaceRectangle](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.facerectangle). Normalerweise enthält dieses Rechteck Augen, Augenbrauen, Nase und Mund. Die Oberseite des Kopfs, die Ohren und das Kinn sind meist nicht enthalten. Um das Gesichtsrechteck zum Ausschneiden eines vollständigen Kopfs oder zum Produzieren eines Porträts in der Halbtotale zu verwenden, sollten Sie das Rechteck in alle Richtungen erweitern.

## <a name="determine-how-to-process-the-data"></a>Festlegen, wie die Daten verarbeitet werden sollen

Das Hauptaugenmerk dieses Leitfadens liegt auf dem Detect-Aufruf, z. B. welche Argumente Sie übergeben und was Sie mit den zurückgegebenen Daten tun können. Wir empfehlen Ihnen, nur die Features abzufragen, die Sie benötigen. Jeder weitere Vorgang nimmt mehr Zeit in Anspruch.

### <a name="get-face-landmarks"></a>Abrufen von Gesichtsmerkmalpunkten

[Gesichtsmerkmale](../concepts/face-detection.md#face-landmarks) bestehen aus mehreren einfach zu findenden Punkten im Gesicht. Beispiele hierfür sind die Pupillen oder die Nasenspitze. Um Daten zu Gesichtsmerkmalen abzurufen, legen Sie den Parameter _detectionModel_ auf `DetectionModel.Detection01` und den Parameter _returnFaceLandmarks_ auf `true` fest.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="landmarks1":::

### <a name="get-face-attributes"></a>Abrufen von Gesichtsattributen

Zusätzlich zu Gesichtsrahmen und Gesichtsmerkmalpunkten kann die Gesichtserkennungs-API mehrere konzeptuelle Gesichtsattribute analysieren. Eine vollständige Liste finden Sie im Abschnitt [Attribute](../concepts/face-detection.md#attributes), in dem die Konzepte beschrieben sind.

Um Gesichtsattribute zu analysieren, legen Sie den Parameter _detectionModel_ auf `DetectionModel.Detection01` und den Parameter _returnFaceAttributes_ auf eine Liste von [FaceAttributeType Enum](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype)-Werten fest.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="attributes1":::


## <a name="get-results-from-the-service"></a>Abrufen von Ergebnissen aus dem Dienst

### <a name="face-landmark-results"></a>Ergebnisse zu Gesichtsmerkmalen

Der folgende Code zeigt, wie Sie die Position von Nase und Pupillen abrufen können:

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="landmarks2":::

Sie können die Punktdaten für Gesichtsmerkmale auch verwenden, um die Ausrichtung eines Gesichts exakt zu bestimmen. Beispielsweise können Sie die Ausrichtung des Gesichts als Vektor vom Mittelpunkt des Munds zum Mittelpunkt der Augen definieren. Dieser Vektor wird mit dem folgenden Code berechnet:

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="direction":::

Wenn Ihnen die Ausrichtung des Gesichts bekannt ist, können Sie den rechteckigen Gesichtsrahmen drehen, um ihn noch besser am Gesicht auszurichten. Zum Ausschneiden von Gesichtern aus einem Bild können Sie Bilder programmgesteuert drehen, damit Gesichter immer senkrecht dargestellt werden.


### <a name="face-attribute-results"></a>Ergebnisse von Gesichtsattributen

Der folgende Code zeigt, wie Sie die Gesichtsattributdaten abrufen können, die Sie im ursprünglichen Aufruf angefordert haben.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="attributes2":::

Weitere Informationen zu den einzelnen Attributen finden Sie im Leitfaden [Face detection and attributes](../concepts/face-detection.md) (Gesichtserkennung und -attribute) zu den Konzepten.

## <a name="next-steps"></a>Nächste Schritte

In diesem Leitfaden haben Sie erfahren, wie die verschiedenen Funktionen der Gesichtserkennung und -analyse verwendet werden. Als Nächstes integrieren Sie diese Features in eine App, um Gesichtserkennungsdaten von Benutzern hinzuzufügen.

- [Tutorial: Hinzufügen von Benutzern zu einem Gesichtserkennungsdienst](../enrollment-overview.md)

## <a name="related-articles"></a>Verwandte Artikel

- [Referenzdokumentation (REST)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
- [Referenzdokumentation (.NET SDK)](/dotnet/api/overview/azure/cognitiveservices/face-readme)
