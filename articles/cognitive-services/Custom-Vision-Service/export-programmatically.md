---
title: Programmgesteuertes Exportieren eines Modells
titleSuffix: Azure Cognitive Services
description: Verwenden Sie die Custom Vision-Clientbibliothek, um ein trainiertes Modell zu exportieren.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 06/28/2021
ms.author: pafarley
ms.openlocfilehash: 0a9a804c98dc40c34ec8747df9eee7705d25a99b
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/16/2021
ms.locfileid: "114285538"
---
# <a name="export-a-model-programmatically"></a>Programmgesteuertes Exportieren eines Modells

Alle Exportoptionen, die auf der [Custom Vision-Website](https://www.customvision.ai/) verfügbar sind, können auch programmgesteuert über die Clientbibliotheken ausgeführt werden. Sie sollten dies tun, damit Sie den Prozess des erneuten Trainings und aktualisieren der Modelliteration, die Sie auf einem lokalen Gerät verwenden, vollständig automatisieren können.

In diesem Leitfaden erfahren Sie, wie Sie Ihr Modell mit dem Python SDK in eine ONNX-Datei exportieren.

## <a name="create-a-training-client"></a>Erstellen eines Trainingsclients

Sie benötigen ein [CustomVisionTrainingClient](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.customvisiontrainingclient)-Objekt, um eine Modelliteration zu exportieren. Erstellen Sie Variablen für Ihre Custom Vision-Trainingsressourcen, Azure-Endpunkt und Abonnementschlüssel, und verwenden Sie sie, um das Clientobjekt zu erstellen.

```python
ENDPOINT = "PASTE_YOUR_CUSTOM_VISION_TRAINING_ENDPOINT_HERE"
training_key = "PASTE_YOUR_CUSTOM_VISION_TRAINING_SUBSCRIPTION_KEY_HERE"

credentials = ApiKeyCredentials(in_headers={"Training-key": training_key})
trainer = CustomVisionTrainingClient(ENDPOINT, credentials)
```

> [!IMPORTANT]
> Denken Sie daran, die Schlüssel aus Ihrem Code zu entfernen, wenn Sie fertig sind, und machen Sie sie niemals öffentlich zugänglich. In der Produktionsumgebung sollten Sie eine sichere Methode zum Speichern Ihrer Anmeldeinformationen sowie zum Zugriff darauf verwenden. Weitere Informationen finden Sie im Artikel zur [Sicherheit](../cognitive-services-security.md) von Cognitive Services.

## <a name="call-the-export-method"></a>Aufrufen der Exportmethode

Rufen Sie die Methode **export_iteration** auf.
* Geben Sie die Projekt-ID und die Iterations-ID des Modells an, das Sie exportieren möchten. 
* Der *platform*-Parameter gibt die zu exportierende Plattform an: Zulässige Werte sind `CoreML`, `TensorFlow`, `DockerFile`, `ONNX`, `VAIDK` und `OpenVino`. 
* Der *flavor*-Parameter gibt das Format des exportierenden Modells an: Zulässige Werte sind `Linux`, `Windows`, `ONNX10`, `ONNX12`, `ARM`, `TensorFlowNormal` und `TensorFlowLite`.
* Der *raw*-Parameter bietet Ihnen die Möglichkeit, die unformatierte JSON-Antwort zusammen mit der Objektmodellantwort abzurufen.

```python
project_id = "PASTE_YOUR_PROJECT_ID"
iteration_id = "PASTE_YOUR_ITERATION_ID"
platform = "ONNX"
flavor = "ONNX10"
export = trainer.export_iteration(project_id, iteration_id, platform, flavor, raw=False)
```

Weitere Informationen finden Sie im Abschnitt zur Methode **[export_iteration](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.operations.customvisiontrainingclientoperationsmixin?view=azure-python#export-iteration-project-id--iteration-id--platform--flavor-none--custom-headers-none--raw-false----operation-config-)** .

## <a name="download-the-exported-model"></a>Herunterladen des exportierten Modells

Als Nächstes rufen Sie die **get_exports**-Methode auf, um den Status des Exportvorgangs zu überprüfen. Der Vorgang wird asynchron ausgeführt, daher sollten Sie diese Methode abfragen, bis der Vorgang abgeschlossen ist. Nach Abschluss des Vorgangs können Sie den URI abrufen, mit dem Sie die Modelliteration auf Ihr Gerät herunterladen können.

```python
while (export.status == "Exporting"):
    print ("Waiting 10 seconds...")
    time.sleep(10)
    exports = trainer.get_exports(project_id, iteration_id)
    # Locate the export for this iteration and check its status  
    for e in exports:
        if e.platform == export.platform and e.flavor == export.flavor:
            export = e
            break
    print("Export status is: ", export.status)
```

Weitere Informationen finden Sie im Abschnitt zur Methode **[get_exports](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.operations.customvisiontrainingclientoperationsmixin?view=azure-python#get-exports-project-id--iteration-id--custom-headers-none--raw-false----operation-config-)** .

Anschließend können Sie das exportierte Modell programmgesteuert an einen Speicherort auf Ihrem Gerät herunterladen.

```python
if export.status == "Done":
    # Success, now we can download it
    export_file = requests.get(export.download_uri)
    with open("export.zip", "wb") as file:
        file.write(export_file.content)
```

## <a name="next-steps"></a>Nächste Schritte

Integrieren Sie Ihr exportiertes Modell in eine Anwendung, indem Sie einen der folgenden Artikel oder eines der Beispiele untersuchen:

* [Verwenden Ihres Tensorflow-Modells mit Python](export-model-python.md)
* [Verwenden Ihres ONNX-Modells mit Windows Machine Learning](custom-vision-onnx-windows-ml.md)
* Weitere Informationen finden Sie im Beispiel für das [Core ML-Modell in einer iOS-Anwendung](https://go.microsoft.com/fwlink/?linkid=857726) für die Bildklassifizierung in Echtzeit mit Swift.
* Weitere Informationen finden Sie im Beispiel für das [Tensorflow-Modell in einer Android-Anwendung](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample) für die Bildklassifizierung in Echtzeit unter Android.
* Weitere Informationen finden Sie im Beispiel für das [Core ML-Modell mit Xamarin](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel) für die Bildklassifizierung in Echtzeit in einer Xamarin-iOS-App.