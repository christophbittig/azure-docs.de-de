---
author: Juliako
ms.service: azure-video-analyzer
ms.topic: include
ms.date: 11/04/2021
ms.author: juliako
ms.openlocfilehash: c8d77cf500956e94610d7fab72d3758aeb767b54
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131860939"
---
Als Teil der Voraussetzungen haben Sie den Beispielcode in einen Ordner heruntergeladen. Führen Sie die unten angegebenen Schritte aus, um die Beispieldateien zu überprüfen und zu bearbeiten.

1. Navigieren Sie in Visual Studio Code zu *src/edge*. Ihre *ENV*-Datei und einige Bereitstellungsvorlagendateien werden angezeigt.
1. Die Datei *deployment.grpcyolov3icpu.template.json* verweist auf das Bereitstellungsmanifest für das Edgegerät. Es enthält einige Platzhalterwerte. Die *ENV*-Datei enthält die Werte für diese Variablen.
1. Navigieren Sie zum Ordner *src/cloud-to-device-console-app*. Darin finden Sie Ihre Datei *appsettings.json* und einige andere Dateien:
    
    * c2d-console-app.csproj: Die Projektdatei für Visual Studio Code.
    * operations.json: Eine Liste mit den Vorgängen, die vom Programm ausgeführt werden sollen.
    * Program.cs: Das Beispiel für den Programmcode. Mit diesem Code wird Folgendes durchgeführt:
    
        * Laden der App-Einstellungen
        * Aufrufen direkter Methoden, die vom Azure Video Analyzer-Modul verfügbar gemacht werden. Sie können das Modul zum Analysieren von Livevideostreams verwenden, indem Sie die zugehörigen direkten Methoden aufrufen.
        * Anhalten der Ausführung, sodass Sie die Ausgabe des Programms im **Terminalfenster** und die vom Modul generierten Ereignisse im **Ausgabefenster** untersuchen können
        * Aufrufen direkter Methoden zur Bereinigung der Ressourcen
1. Bearbeiten Sie die Datei operations.json:
    
    * Ändern Sie den Link zur Pipeline:<br/>` "topologyUrl" : "https://raw.githubusercontent.com/Azure/video-analyzer/main/pipelines/live/topologies/motion-with-grpcExtension/topology.json"`
    * Bearbeiten Sie unter „livePipelineSet“ den Namen der Pipelinetopologie, sodass er mit dem Wert im vorherigen Link übereinstimmt:<br/>`"topologyName" : "EVROnMotionPlusGrpcExtension"`
    * Bearbeiten Sie unter „topologyDelete“ den Namen:<br/>`"name" : "EVROnMotionPlusGrpcExtension"`
