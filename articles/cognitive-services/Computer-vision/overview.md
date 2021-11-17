---
title: Worum handelt es sich bei maschinellem Sehen?
titleSuffix: Azure Cognitive Services
description: Über den Dienst für maschinelles Sehen haben Sie Zugriff auf erweiterte Algorithmen für die Bildverarbeitung und die Rückgabe von Informationen.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 09/28/2021
ms.author: pafarley
ms.custom:
- seodec18
- cog-serv-seo-aug-2020
- contperf-fy21q2
keywords: maschinelles Sehen, Anwendungen für maschinelles Sehen, Dienst für maschinelles Sehen
ms.openlocfilehash: 3165dc875c89618476284aaaff3e5884ab3fe6b1
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132370743"
---
# <a name="what-is-computer-vision"></a>Worum handelt es sich bei maschinellem Sehen?

Über den Azure-Dienst für maschinelles Sehen haben Sie Zugriff auf erweiterte Algorithmen, die Bilder verarbeiten und Informationen anhand der für sie interessanten visuellen Features zurückgeben. 

| Dienst|BESCHREIBUNG|
|---|---|
| [Optische Zeichenerkennung (OCR)](overview-ocr.md)|Der Optische Zeichenerkennungs-Dienst (OCR) extrahiert Text aus Bildern. Sie können die neue Lese-API verwenden, um gedruckten und handschriftlichen Text aus Bildern und Dokumenten zu extrahieren. Hierbei werden auf Deep Learning basierende Modelle verwendet, und es wird Text auf vielen verschiedenen Oberflächen und Hintergründen verarbeitet. Beispiele hierfür sind Geschäftsdokumente, Rechnungen, Belege, Poster, Visitenkarten, Briefe und Whiteboards. Die OCR-APIs unterstützen das Extrahieren von gedrucktem Text in [mehreren Sprachen](./language-support.md). Befolgen Sie unseren [OCR Schnellstart](quickstarts-sdk/client-library.md), um zu beginnen.|
|[Bildanalyse](overview-image-analysis.md)| Der Abbild Analysedienst extrahiert viele visuelle Features aus Bildern, wie z. b. Objekte, Gesichter, nicht jugendfreie Inhalte und automatisch generierte Textbeschreibungen. Befolgen Sie den [Schnellstart für die Bildanalyse](quickstarts-sdk/image-analysis-client-library.md), um zu beginnen.|
| [Räumliche Analyse](intro-to-spatial-analysis-public-preview.md)| Der Dienst für räumliche Analysen analysiert das vorhanden sein und Verschieben von Personen in einem Videofeed und erzeugt Ereignisse, auf die andere Systeme reagieren können. Installieren Sie den [Container für räumliche Analysen](spatial-analysis-container.md), um zu beginnen.|

## <a name="computer-vision-for-digital-asset-management"></a>Maschinelles Sehen für Digital Asset Management (DAM)

Das maschinelle Sehen kann viele DAM-Szenarien (Digital Asset Management) unterstützen. DAM ist der Geschäftsprozess der Organisation, Speicherung und Abfrage von Rich-Media-Medienobjekten und der Verwaltung digitaler Rechte und Berechtigungen. Beispielsweise kann ein Unternehmen Bilder basierend auf sichtbaren Logos, Gesichtern, Objekten, Farben usw. gruppieren und identifizieren. Oder Sie können automatisch [Beschriftungen für Bilder generieren](./Tutorials/storage-lab-tutorial.md) und Schlüsselwörter anhängen, damit sie durchsuchbar sind. Eine All-in-One-DAM-Lösung mit Cognitive Services, Azure Cognitive Search und intelligenter Berichterstellung finden Sie im Leitfaden [Knowledge Mining Solution Accelerator](https://github.com/Azure-Samples/azure-search-knowledge-mining) auf GitHub. Weitere DAM-Beispiele finden Sie im Repository zu [Lösungsvorlagen für maschinelles Sehen](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates).

## <a name="image-requirements"></a>Bildanforderungen

Maschinelles Sehen kann Bilder analysieren, die folgende Anforderungen erfüllen:

- Das Bild muss im JPEG-, PNG-, GIF- oder BMP-Format vorliegen.
- Die Dateigröße muss weniger als 4 MB betragen.
- Das Bild muss größer als 50 x 50 Pixel sein.
  - Für die Lese-API muss die Größe des Bilds zwischen 50 x 50 und 10.000 x 10.000 Pixel liegen.

## <a name="data-privacy-and-security"></a>Datenschutz und Sicherheit

Wie bei allen Cognitive Services-Diensten müssen Entwickler, die den Maschinelles Sehen-Dienst nutzen, die Microsoft-Richtlinien zu Kundendaten beachten. Weitere Informationen finden Sie im Microsoft Trust Center auf der [Seite zu Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices).

## <a name="next-steps"></a>Nächste Schritte

Befolgen Sie einen Schnellstart, um einen Dienst in Ihrer bevorzugten Entwicklungssprache zu implementieren und auszuführen.

* [Schnellstart: Optische Zeichenerkennung (OCR)](quickstarts-sdk/client-library.md)
* [Schnellstart: die Bildanalyse](quickstarts-sdk/image-analysis-client-library.md)
* [Schnellstart: Container für die räumliche Analyse](spatial-analysis-container.md)
