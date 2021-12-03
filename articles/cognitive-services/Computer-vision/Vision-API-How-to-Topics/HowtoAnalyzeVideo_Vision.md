---
title: Analysieren von Videos nahezu in Echtzeit – maschinelles Sehen
titleSuffix: Azure Cognitive Services
description: Erfahren Sie, wie Sie nahezu in Echtzeit Analysen für Frames aus einem Livevideostream durchführen, indem Sie die Maschinelles Sehen-API verwenden.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 09/09/2019
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: bb35a93a2cfbd7fc90e614161eb7a95a5098871b
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132063682"
---
# <a name="analyze-videos-in-near-real-time"></a>Analysieren von Videos nahezu in Echtzeit

Dieser Artikel beschreibt, wie Sie nahezu in Echtzeit Analysen für Frames aus einem Livevideostream durchführen, indem Sie die Maschinelles Sehen-API verwenden. Die grundlegenden Elemente einer solchen Analyse sind folgende:

- Abrufen von Frames von einer Videoquelle
- Auswählen der zu analysierenden Frames
- Übermitteln der Frames an die API
- Nutzen der einzelnen Analyseergebnisse, die vom API-Aufruf zurückgegeben werden

Die Beispiele in diesem Artikel sind in C# geschrieben. Navigieren Sie zur Seite [Video frame analysis sample](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) auf GitHub, um auf den Code zuzugreifen.

## <a name="approaches-to-running-near-real-time-analysis"></a>Ansätze zum Ausführen von Analysen nahezu in Echtzeit

Es gibt mehrere Möglichkeiten, das Problem zur Durchführung der Analyse von Videodatenströmen nahezu in Echtzeit zu lösen. In diesem Artikel werden drei davon (mit zunehmender Komplexität) beschrieben.

### <a name="design-an-infinite-loop"></a>Entwerfen einer Endlosschleife

Der einfachste Entwurf für die Analyse nahezu in Echtzeit ist eine Endlosschleife. In jeder Iteration dieser Schleife erfassen Sie einen Frame, analysieren ihn und nutzen dann das Ergebnis:

```csharp
while (true)
{
    Frame f = GrabFrame();
    if (ShouldAnalyze(f))
    {
        AnalysisResult r = await Analyze(f);
        ConsumeResult(r);
    }
}
```

Dieser Ansatz ist geeignet, wenn Ihre Analyse aus einem einfachen clientseitigen Algorithmus besteht. Wenn die Analyse jedoch in der Cloud erfolgt, kann ein API-Aufruf aufgrund der Wartezeit mehrere Sekunden dauern. Während dieses Zeitraums werden keine Bilder erfasst, und Ihr Thread leistet praktisch nichts. Die maximale Bildfrequenz ist durch die Wartezeit der API-Aufrufe eingeschränkt.

### <a name="allow-the-api-calls-to-run-in-parallel"></a>Zulassen einer parallelen Ausführung von API-Aufrufen

Eine einfache Single-Thread-Schleife ist bei einem einfachen clientseitigen Algorithmus sinnvoll, sie ist jedoch aufgrund der Wartezeit von API-Aufrufen in der Cloud etwas problematisch. Dieses Problem lässt sich beheben, indem Sie zulassen, dass API-Aufrufe mit langer Ausführungsdauer parallel zum Erfassen der Frames durchgeführt werden können. In C# ist dies mithilfe eines aufgabenbasierten Parallelismus möglich. Sie können beispielsweise den folgenden Code ausführen:

```csharp
while (true)
{
    Frame f = GrabFrame();
    if (ShouldAnalyze(f))
    {
        var t = Task.Run(async () =>
        {
            AnalysisResult r = await Analyze(f);
            ConsumeResult(r);
        }
    }
}
```

Bei diesem Ansatz starten Sie jede Analyse in einer separaten Aufgabe. Die Aufgabe wird im Hintergrund ausgeführt, während Sie weiterhin neue Frames erfassen. Dadurch wird vermieden, dass der Hauptthread blockiert ist, während Sie auf die Rückgabe eines API-Aufrufs warten. Dieser Ansatz hat jedoch Nachteile:
* Dabei gehen einige Garantien verloren, die für die einfache Version gelten. Möglicherweise werden mehrere API-Aufrufe parallel ausgeführt, und die Ergebnisse werden unter Umständen in der falschen Reihenfolge zurückgegeben. 
* Er kann auch dazu führen, dass mehrere Threads gleichzeitig auf die ConsumeResult()-Funktion zugreifen. Falls die Funktion nicht threadsicher ist, kann dies gefährlich sein. 
* Zudem werden die erstellten Aufgaben mit dem einfachen Code nicht nachverfolgt, sodass Ausnahmen untergehen. Daher müssen Sie einen Thread vom Typ „Consumer“ hinzufügen, mit dem die Analyseaufgaben nachverfolgt, Ausnahmen ausgelöst und Aufgaben mit langer Ausführungsdauer beendet werden und der zudem sicherstellt, dass die Ergebnisse nacheinander in der richtigen Reihenfolge verarbeitet werden.

### <a name="design-a-producer-consumer-system"></a>Entwerfen eines Producer-Consumer-Systems

Der letzte Ansatz besteht im Entwerfen eines „Producer-Consumer“-Systems. Dabei erstellen Sie einen Producer-Thread, der der zuvor beschriebenen Endlosschleife ähnelt. Die Analyseergebnisse werden aber nicht sofort genutzt, wenn sie verfügbar sind. Stattdessen stellt der Producer die Aufgaben in eine Warteschlange, um sie nachzuverfolgen.

```csharp
// Queue that will contain the API call tasks.
var taskQueue = new BlockingCollection<Task<ResultWrapper>>();

// Producer thread.
while (true)
{
    // Grab a frame.
    Frame f = GrabFrame();

    // Decide whether to analyze the frame.
    if (ShouldAnalyze(f))
    {
        // Start a task that will run in parallel with this thread.
        var analysisTask = Task.Run(async () =>
        {
            // Put the frame, and the result/exception into a wrapper object.
            var output = new ResultWrapper(f);
            try
            {
                output.Analysis = await Analyze(f);
            }
            catch (Exception e)
            {
                output.Exception = e;
            }
            return output;
        }

        // Push the task onto the queue.
        taskQueue.Add(analysisTask);
    }
}
```

Außerdem erstellen Sie einen Consumer-Thread, der Aufgaben aus der Warteschlange entfernt, auf den Abschluss der Verarbeitung wartet und entweder das Ergebnis anzeigt oder die jeweilige Ausnahme auslöst. Mithilfe der Warteschlange stellen Sie sicher, dass die Ergebnisse nacheinander in der richtigen Reihenfolge genutzt werden, ohne dass die maximale Bildfrequenz des Systems eingeschränkt wird.

```csharp
// Consumer thread.
while (true)
{
    // Get the oldest task.
    Task<ResultWrapper> analysisTask = taskQueue.Take();
 
    // Wait until the task is completed.
    var output = await analysisTask;

    // Consume the exception or result.
    if (output.Exception != null)
    {
        throw output.Exception;
    }
    else
    {
        ConsumeResult(output.Analysis);
    }
}
```

## <a name="implement-the-solution"></a>Implementieren der Lösung

### <a name="get-started-quickly"></a>Schneller Einstieg

Damit Ihre App so bald wie möglich ausgeführt werden kann, haben wir das System implementiert, das im vorherigen Abschnitt beschrieben wurde. Es ist flexibel, sodass viele Szenarien unterstützt werden, und gleichzeitig ist es sehr benutzerfreundlich. Navigieren Sie zur Seite [Video frame analysis sample](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/) auf GitHub, um auf den Code zuzugreifen.

Die Bibliothek enthält die `FrameGrabber`-Klasse, mit der das zuvor beschriebene Producer-Consumer-System implementiert wird, um Videoframes von einer Webcam zu verarbeiten. Die Benutzer können die genaue Form des API-Aufrufs angeben, und für die Klasse werden Ereignisse verwendet, um den aufrufenden Code darüber zu informieren, dass ein neuer Frame erfasst wurde oder ein neues Analyseergebnis verfügbar ist.

Zur Veranschaulichung einiger Möglichkeiten stellen wir zwei Beispiel-Apps bereit, die die Bibliothek nutzen. 

Die erste Beispiel-App ist eine einfache Konsolen-App, die Frames von der Standardwebcam erfasst und diese anschließend zur Gesichtserkennung an den Gesichtserkennungsdienst übergibt. Eine vereinfachte Version der App wird im folgenden Code reproduziert:

```csharp
using System;
using System.Linq;
using Microsoft.Azure.CognitiveServices.Vision.Face;
using Microsoft.Azure.CognitiveServices.Vision.Face.Models;
using VideoFrameAnalyzer;

namespace BasicConsoleSample
{
    internal class Program
    {
        const string ApiKey = "<your API key>";
        const string Endpoint = "https://<your API region>.api.cognitive.microsoft.com";

        private static async Task Main(string[] args)
        {
            // Create grabber.
            FrameGrabber<DetectedFace[]> grabber = new FrameGrabber<DetectedFace[]>();

            // Create Face Client.
            FaceClient faceClient = new FaceClient(new ApiKeyServiceClientCredentials(ApiKey))
            {
                Endpoint = Endpoint
            };

            // Set up a listener for when we acquire a new frame.
            grabber.NewFrameProvided += (s, e) =>
            {
                Console.WriteLine($"New frame acquired at {e.Frame.Metadata.Timestamp}");
            };

            // Set up a Face API call.
            grabber.AnalysisFunction = async frame =>
            {
                Console.WriteLine($"Submitting frame acquired at {frame.Metadata.Timestamp}");
                // Encode image and submit to Face service.
                return (await faceClient.Face.DetectWithStreamAsync(frame.Image.ToMemoryStream(".jpg"))).ToArray();
            };

            // Set up a listener for when we receive a new result from an API call.
            grabber.NewResultAvailable += (s, e) =>
            {
                if (e.TimedOut)
                    Console.WriteLine("API call timed out.");
                else if (e.Exception != null)
                    Console.WriteLine("API call threw an exception.");
                else
                    Console.WriteLine($"New result received for frame acquired at {e.Frame.Metadata.Timestamp}. {e.Analysis.Length} faces detected");
            };

            // Tell grabber when to call the API.
            // See also TriggerAnalysisOnPredicate
            grabber.TriggerAnalysisOnInterval(TimeSpan.FromMilliseconds(3000));

            // Start running in the background.
            await grabber.StartProcessingCameraAsync();

            // Wait for key press to stop.
            Console.WriteLine("Press any key to stop...");
            Console.ReadKey();

            // Stop, blocking until done.
            await grabber.StopProcessingAsync();
        }
    }
}
```

Die zweite Beispiel-App ist etwas interessanter. Mit ihr können Sie auswählen, welche API für die Videoframes aufgerufen werden soll. Auf der linken Seite zeigt die App eine Vorschau des Livevideos an. Auf der rechten Seite zeigt sie eine Überlagerung des aktuellen API-Ergebnisses zum entsprechenden Frame.

In den meisten Modi kommt es zu einer sichtbaren Verzögerung zwischen dem Livevideo auf der linken und der visualisierten Analyse auf der rechten Seite. Diese Verzögerung ist die Zeit, die für den API-Aufruf benötigt wird. Eine Ausnahme ist der Modus „EmotionsWithClientFaceDetect“. Hierbei wird die Gesichtserkennung lokal auf dem Clientcomputer per OpenCV durchgeführt, bevor Bilder an Cognitive Services übermittelt werden. 

Mit diesen Ansatz können Sie das erkannte Gesicht sofort visualisieren. Die Emotionen können Sie später, nach der Rückgabe des API-Aufrufs, aktualisieren. Dies veranschaulicht die Möglichkeit eines „Hybridansatzes“. Dabei wird auf dem Client eine einfache Verarbeitung durchgeführt. Anschließend können Cognitive Services-APIs genutzt werden, um diese Verarbeitung bei Bedarf durch eingehendere Analysen zu untermauern.

![Die LiveCameraSample-App, die ein Bild mit Tags anzeigt](../../Video/Images/FramebyFrame.jpg)

### <a name="integrate-the-samples-into-your-codebase"></a>Integrieren von Beispielen in Ihre Codebasis

Führen Sie die folgenden Schritte aus, um mit diesem Beispiel zu beginnen:

1. [Erstellen eines Azure-Kontos](https://azure.microsoft.com/free/cognitive-services/). Wenn Sie bereits ein Konto besitzen, können Sie mit dem nächsten Schritt fortfahren.
2. Erstellen von Ressourcen für maschinelles Sehen und Gesichtserkennung im Azure-Portal, um Ihren Schlüssel und Ihren Endpunkt abzurufen. Achten Sie darauf, beim Setup den Free-Tarif (F0) auszuwählen.
   - [Maschinelles Sehen](https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision)
   - [Gesichtserkennung](https://portal.azure.com/#create/Microsoft.CognitiveServicesFace) Klicken Sie nach dem Bereitstellen der Ressourcen auf **Zu Ressource wechseln**, um für jede Ressource Ihren Schlüssel und Endpunkt zu erfassen. 
3. Klonen Sie das GitHub-Repository [Cognitive-Samples-VideoFrameAnalysis](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/).
4. Öffnen Sie das Beispiel in Visual Studio 2015 oder höher, erstellen Sie die Beispielanwendungen, und führen Sie sie aus:
    - Für BasicConsoleSample ist der Schlüssel für die Gesichtserkennung direkt in [BasicConsoleSample/Program.cs](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/blob/master/Windows/BasicConsoleSample/Program.cs) hartcodiert.
    - Geben Sie für LiveCameraSample im Bereich **Einstellungen** der App die Schlüssel ein. Die Schlüssel werden sitzungsübergreifend als Benutzerdaten beibehalten.

Wenn Sie soweit sind, die Beispiele zu integrieren, verweisen Sie in Ihren eigenen Projekten auf die VideoFrameAnalyzer-Bibliothek.

Die Bild-, Sprach-, Video- und Textanalysefunktionen von VideoFrameAnalyzer nutzen Azure Cognitive Services. Microsoft erhält die Bild-, Audio- und Videodaten sowie alle anderen Daten, die Sie (über diese App) hochladen, und nutzt sie ggf. zur Verbesserung von Diensten. Wir bitten Sie darum, uns beim Schützen der Personen zu helfen, deren Daten über Ihre App an Azure Cognitive Services gesendet werden.

## <a name="summary"></a>Zusammenfassung

In diesem Artikel haben Sie erfahren, wie Sie mithilfe der Dienste für Gesichtserkennung und maschinelles Sehen nahezu in Echtzeit Analysen von Livevideostreams ausführen. Außerdem haben Sie gelernt, wie unser Beispielcode Sie bei Ihrem Einstieg unterstützt.

Wir freuen uns über Ihr Feedback und Ihre Vorschläge im [GitHub-Repository](https://github.com/Microsoft/Cognitive-Samples-VideoFrameAnalysis/). Umfassenderes API-Feedback können Sie uns über unsere [UserVoice](https://feedback.azure.com/d365community/forum/09041fae-0b25-ec11-b6e6-000d3a4f0858)-Website zukommen lassen.

