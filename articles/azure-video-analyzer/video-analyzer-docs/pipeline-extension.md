---
title: Pipelineerweiterung – Azure Video Analyzer
description: Azure Video Analyzer ermöglicht es Ihnen, seine Pipeline-Verarbeitungsfunktionen über einen Pipeline-Erweiterungsknoten zu erweitern. In diesem Artikel wird der Pipelineerweiterungsknoten beschrieben.
ms.service: azure-video-analyzer
ms.topic: conceptual
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 7641524fb712d9564fca46ef40e8949f4dd9bad1
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131851578"
---
# <a name="pipeline-extension"></a>Pipelineerweiterung

Azure Video Analyzer ermöglicht es Ihnen, seine Pipeline-Verarbeitungsfunktionen über einen Pipeline-Erweiterungsknoten zu erweitern. Ihr Analyseerweiterungs-Plug-In kann herkömmliche Bildverarbeitungstechniken oder KI-Modelle des maschinellen Sehens nutzen. Zur Aktivierung von Pipelineerweiterungen wird ein Erweiterungsprozessorknoten in einen Pipelineflow eingeschlossen. Der Erweiterungsprozessorknoten leitet Videoframes an den konfigurierten Endpunkt weiter und fungiert als Schnittstelle für Ihre Erweiterung. Die Verbindung kann mit einem lokalen oder entfernten Endpunkt hergestellt und bei Bedarf durch Authentifizierung und TLS-Verschlüsselung gesichert werden. Außerdem ermöglicht der Pipelineerweiterungsprozessor-Knoten optional die Skalierung und Codierung der Videoframes, bevor sie an Ihre benutzerdefinierte Erweiterung übermittelt werden.

Video Analyzer unterstützt die folgenden Pipelineerweiterungsprozessoren:

* [Verarbeitungsknoten für die HTTP-Erweiterung](pipeline.md#http-extension-processor) 
* [gRPC-Erweiterungsprozessor](pipeline.md#grpc-extension-processor)
* [Cognitive Services-Erweiterungsprozessor](pipeline.md#cognitive-services-extension-processor)

Der Pipelineerweiterungsknoten erwartet, dass das Analyse-Erweiterungs-Plug-In die Ergebnisse im JSON-Format zurückgibt. Idealerweise sollten die Ergebnisse dem [Schemaobjektmodell für Rückschlussmetadaten](inference-metadata-schema.md) entsprechen.

[!INCLUDE [available-features](./includes/available-features.md)]

## <a name="http-extension-processor"></a>Verarbeitungsknoten für die HTTP-Erweiterung

Der HTTP-Erweiterungsprozessor ermöglicht Erweiterungsszenarien mit dem [HTTP-Protokoll](http-extension-protocol.md), bei denen es nicht in erster Linie auf Leistung und/oder optimale Ressourcenverwendung ankommt. Sie können Ihre eigene KI für eine Pipeline über einen HTTP-REST-Endpunkt verfügbar machen.

Der HTTP-Erweiterungsprozessorknoten kann in folgenden Fällen verwendet werden:

* Sie möchten die Interoperabilität mit vorhandenen HTTP-Rückschlusssystemen verbessern.
* Datenübertragung mit geringer Leistung ist akzeptabel.
* Sie möchten eine einfache Anforderung/Antwort-Schnittstelle für Video Analyzer verwenden.

## <a name="grpc-extension-processor"></a>gRPC-Erweiterungsprozessor

Der gRPC-Erweiterungsprozessor ermöglicht Erweiterungsszenarien mit einem gRPC-basierten, hochleistungsfähigen [strukturierten Protokoll](grpc-extension-protocol.md). Er eignet sich perfekt für Szenarien, in denen Leistung und/oder optimale Ressourcennutzung Priorität haben bzw. hat. Mit dem gRPC-Erweiterungsprozessor können Sie in vollem Umfang von den strukturierten Datendefinitionen profitieren. gRPC bietet eine hohe Inhaltsübertragungsleistung. Hierzu wird Folgendes genutzt:

* [Integrierter, gemeinsam genutzter Arbeitsspeicher](https://en.wikipedia.org/wiki/Shared_memory) oder
* Direktes Einbetten des Inhalts in den Text von gRPC-Nachrichten

Der gRPC-Erweiterungsprozessor kann zum Senden von Eigenschaften sowie zum Austauschen von Rückschlussnachrichten verwendet werden. Verwenden Sie einen gRPC-Erweiterungsprozessorknoten daher in folgenden Fällen:

* Sie möchten einen strukturierten Vertrag verwenden (beispielsweise strukturierte Nachrichten für Anforderungen und Antworten).
* Sie möchten für die Kommunikation [Protokollpuffer (protobuf)](https://developers.google.com/protocol-buffers) als zugrunde liegendes Nachrichtenaustauschformat verwenden.
* Sie möchten mit einem gRPC-Server in einer einzelnen Streamsitzung kommunizieren, anstatt das herkömmlichen Anforderung/Antwort-Modell zu verwenden, das einen benutzerdefinierten Anforderungshandler erfordert, um eingehende Anforderungen zu analysieren und die richtigen Implementierungsfunktionen aufzurufen.
* Sie möchten bei der Kommunikation zwischen Video Analyzer und Ihrem Modul eine geringe Wartezeit und einen hohen Durchsatz erzielen.

## <a name="cognitive-services-extension-processor"></a>Cognitive Services-Erweiterungsprozessor

Der Cognitive Services-Erweiterungsprozessor (von Microsoft entwickelte und unterstützte KI) ist ein benutzerdefinierter Erweiterungsprozessor, der es Video Analyzer ermöglicht, mit den [Computer Vision Spatial Analysis](../../cognitive-services/computer-vision/overview.md)-Funktionen unter Verwendung von gRPC-basiertem, hochleistungsfähigem [strukturiertem Protokoll](grpc-extension-protocol.md) gut zusammenzuarbeiten. 

Verwenden Sie einen Cognitive Services-Erweiterungsprozessorknoten in folgenden Situationen:

* Sie möchten eine bessere Interoperabilität mit vorhandenen [Vorgängen der räumlichen Analyse](../../cognitive-services/computer-vision/intro-to-spatial-analysis-public-preview.md) erreichen.
* Sie möchten alle Vorteile des gRPC-Protokolls, der Genauigkeit und Leistung von **von Microsoft entwickelter und unterstützter KI** nutzen.
* Sie analysieren mehrere Kamerafeeds mit geringer Latenz und hohem Durchsatz.

## <a name="use-your-inferencing-model"></a>Verwenden Ihres Rückschlussmodells

Mithilfe von Pipelineerweiterungen können Sie Rückschlussmodelle Ihrer Wahl für jede verfügbare Rückschlussruntime ausführen. Hierzu zählen etwa ONNX, TensorFlow, PyTorch und andere in Ihrem eigenen Docker-Container. Die benutzerdefinierte Rückschlusserweiterung sollte zusammen mit dem Video Analyzer-Edgemodul bereitgestellt werden, um eine optimale Leistung zu erzielen, und wird dann über den in Ihrer Pipelinetopologie enthaltenen HTTP-, gRPC- oder Cognitive Services-Erweiterungsprozessor aufgerufen. Darüber hinaus kann die Häufigkeit der Aufrufe für Ihre benutzerdefinierte Erweiterung gedrosselt werden, indem optional dem Pipelineerweiterungsprozessor ein [Bewegungsmelderprozessor](pipeline.md#motion-detection-processor) vorgeschaltet wird.

Das folgende Diagramm zeigt den Datenfluss im Überblick:

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/pipeline-extension/ai-inference-results.svg" alt-text="Rückschlussmodell":::
 
## <a name="samples"></a>Beispiele

Sie können mithilfe eines unserer Schnellstarts einsteigen, der Video Analyzer mit dem vordefinierten Erweiterungsdienst mit [HTTP-Erweiterungsprozessor](pipeline.md#http-extension-processor) oder bei hohen Bildfrequenzen mit dem [gRPC-Erweiterungsprozessor](pipeline.md#grpc-extension-processor) veranschaulicht.

## <a name="next-steps"></a>Nächste Schritte 

Konzept: [Videoaufzeichnung](video-recording.md)
