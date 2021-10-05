---
title: Verwenden des HeadPose-Attributs
titleSuffix: Azure Cognitive Services
description: Erfahren Sie, wie Sie mit dem HeadPose-Attribut das Gesichtsrechteck automatisch drehen oder Kopfbewegungen in einem Videofeed erkennen können.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: sample
ms.date: 02/23/2021
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: bbb43768aee85d5c3cde83563645fa649a4cbcc4
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/28/2021
ms.locfileid: "129092184"
---
# <a name="use-the-headpose-attribute"></a>Verwenden des HeadPose-Attributs

In dieser Anleitung erfahren Sie, wie Sie das HeadPose-Attribut eines erkannten Gesichts verwenden können, um einige wichtige Szenarien umzusetzen.

## <a name="rotate-the-face-rectangle"></a>Drehen des Gesichtsrechtecks

Das Gesichtsrechteck, das bei jedem erkannten Gesicht zurückgegeben wird, markiert die Position und Größe des Gesichts im Bild. Standardmäßig ist das Rechteck immer mit dem Bild ausgerichtet (seine Seiten sind vertikal und horizontal). Dies kann für das Umrahmen von abgewinkelten bzw. geneigten Gesichtern ineffizient sein. In Situationen, in denen Sie programmgesteuert Gesichter in einem Bild zuschneiden möchten, ist es besser, das Rechteck für den Zuschnitt drehen zu können.

Die Beispiel-App [Cognitive Services Face WPF (Windows Presentation Foundation)](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF) verwendet das HeadPose-Attribut, um die Rechtecke der erkannten Gesichter zu drehen.

### <a name="explore-the-sample-code"></a>Untersuchen des Beispielcodes

Sie können das Gesichtsrechteck programmgesteuert drehen, indem Sie das HeadPose-Attribut verwenden. Wenn Sie dieses Attribut bei der Gesichtserkennung angeben (siehe [Aufrufen der Erkennungs-API](HowtoDetectFacesinImage.md)), können Sie es später abfragen. Die folgende Methode aus der App [Cognitive Services Face WPF](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF) nimmt eine Liste von **DetectedFace**-Objekten und gibt eine Liste von **[Face](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/app-samples/Cognitive-Services-Face-WPF/Sample-WPF/Controls/Face.cs)**-Objekten zurück. **Face** ist hier eine benutzerdefinierte Klasse, die Gesichtsdaten speichert, einschließlich der aktualisierten Rechteckkoordinaten. Neue Werte werden für **top** (oben), **left** (links), **width** (Breite) und **height** (Höhe) berechnet, und ein neues Feld **FaceAngle** gibt die Drehung an.

```csharp
/// <summary>
/// Calculate the rendering face rectangle
/// </summary>
/// <param name="faces">Detected face from service</param>
/// <param name="maxSize">Image rendering size</param>
/// <param name="imageInfo">Image width and height</param>
/// <returns>Face structure for rendering</returns>
public static IEnumerable<Face> CalculateFaceRectangleForRendering(IList<DetectedFace> faces, int maxSize, Tuple<int, int> imageInfo)
{
    var imageWidth = imageInfo.Item1;
    var imageHeight = imageInfo.Item2;
    var ratio = (float)imageWidth / imageHeight;
    int uiWidth = 0;
    int uiHeight = 0;
    if (ratio > 1.0)
    {
        uiWidth = maxSize;
        uiHeight = (int)(maxSize / ratio);
    }
    else
    {
        uiHeight = maxSize;
        uiWidth = (int)(ratio * uiHeight);
    }

    var uiXOffset = (maxSize - uiWidth) / 2;
    var uiYOffset = (maxSize - uiHeight) / 2;
    var scale = (float)uiWidth / imageWidth;

    foreach (var face in faces)
    {
        var left = (int)(face.FaceRectangle.Left * scale + uiXOffset);
        var top = (int)(face.FaceRectangle.Top * scale + uiYOffset);

        // Angle of face rectangles, default value is 0 (not rotated).
        double faceAngle = 0;

        // If head pose attributes have been obtained, re-calculate the left & top (X & Y) positions.
        if (face.FaceAttributes?.HeadPose != null)
        {
            // Head pose's roll value acts directly as the face angle.
            faceAngle = face.FaceAttributes.HeadPose.Roll;
            var angleToPi = Math.Abs((faceAngle / 180) * Math.PI);

            // _____       | / \ |
            // |____|  =>  |/   /|
            //             | \ / |
            // Re-calculate the face rectangle's left & top (X & Y) positions.
            var newLeft = face.FaceRectangle.Left +
                face.FaceRectangle.Width / 2 -
                (face.FaceRectangle.Width * Math.Sin(angleToPi) + face.FaceRectangle.Height * Math.Cos(angleToPi)) / 2;

            var newTop = face.FaceRectangle.Top +
                face.FaceRectangle.Height / 2 -
                (face.FaceRectangle.Height * Math.Sin(angleToPi) + face.FaceRectangle.Width * Math.Cos(angleToPi)) / 2;

            left = (int)(newLeft * scale + uiXOffset);
            top = (int)(newTop * scale + uiYOffset);
        }

        yield return new Face()
        {
            FaceId = face.FaceId?.ToString(),
            Left = left,
            Top = top,
            OriginalLeft = (int)(face.FaceRectangle.Left * scale + uiXOffset),
            OriginalTop = (int)(face.FaceRectangle.Top * scale + uiYOffset),
            Height = (int)(face.FaceRectangle.Height * scale),
            Width = (int)(face.FaceRectangle.Width * scale),
            FaceAngle = faceAngle,
        };
    }
}
```

### <a name="display-the-updated-rectangle"></a>Anzeigen des aktualisierten Rechtecks

Von hier aus können Sie die zurückgegebenen **Face**-Objekte in Ihrer Anzeige verwenden. Die folgenden Zeilen aus [FaceDetectionPage.xaml](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/app-samples/Cognitive-Services-Face-WPF/Sample-WPF/Controls/FaceDetectionPage.xaml) zeigen, wie das neue Rechteck aus diesen Daten gerendert wird:

```xaml
 <DataTemplate>
    <Rectangle Width="{Binding Width}" Height="{Binding Height}" Stroke="#FF26B8F4" StrokeThickness="1">
        <Rectangle.LayoutTransform>
            <RotateTransform Angle="{Binding FaceAngle}"/>
        </Rectangle.LayoutTransform>
    </Rectangle>
</DataTemplate>
```

## <a name="detect-head-gestures"></a>Erkennen von Kopfbewegungen

Sie können Kopfbewegungen wie Nicken oder Kopfschütteln erkennen, indem Sie HeadPose-Änderungen in Echtzeit nachverfolgen. Sie können diese Funktion als eine benutzerdefinierte Liveerkennung verwenden.

Bei einer Liveerkennung wird ermittelt, ob ein Subjekt eine reale Person oder lediglich eine Bild- oder Videodarstellung ist. Die Erkennung von Kopfbewegungen stellt eine Möglichkeit dar, um sicherzustellen, dass es sich um eine lebendige Person und nicht um eine Bilddarstellung handelt.

> [!CAUTION]
> Damit Sie Kopfbewegungen in Echtzeit erkennen können, müssen Sie die Gesichtserkennungs-API in einer sehr hohen Frequenz (mehrmals pro Sekunde) aufrufen. Dies ist mit einem Abonnement im Free-Tarif (f0) nicht möglich. Wenn Sie über ein Abonnement in einem kostenpflichtigen Tarif verfügen, sollten Sie unbedingt vorab die Kosten für schnelle API-Aufrufe berechnen, die für die Erkennung von Kopfbewegungen erforderlich sind.

Das [HeadPose-Beispiel der Gesichtserkennung](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/FaceAPIHeadPoseSample) auf GitHub ist ein funktionsfähiges Beispiel für die Erkennung von Kopfbewegungen.

## <a name="next-steps"></a>Nächste Schritte

Ein funktionierendes Beispiel für gedrehte Gesichtsrechtecke finden Sie in der App [Cognitive Services Face WPF](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples/Cognitive-Services-Face-WPF) auf GitHub. Sie können sich auch die App für das [HeadPose-Beispiel der Gesichtserkennung](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/app-samples) ansehen, die das HeadPose-Attribut in Echtzeit verfolgt, um Kopfbewegungen zu erkennen.