---
title: Was ist räumliche Analyse?
titleSuffix: Azure Cognitive Services
description: In diesem Dokument werden die grundlegenden Konzepte und Features des Azure-Containers zur räumlichen Analyse erläutert.
services: cognitive-services
author: nitinme
manager: nitinme
ms.author: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 10/06/2021
ms.custom: contperf-fy22q2
ms.openlocfilehash: fd160aa2ba4a626e12db638694cb6d971b19fbd2
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131469937"
---
# <a name="what-is-spatial-analysis"></a>Was ist räumliche Analyse?

Sie können die räumliche Analyse für maschinelles Sehen verwenden, um Streamingvideos von Kameras zu erfassen, Erkenntnisse zu extrahieren und Ereignisse zu generieren, die von anderen Systemen verwendet werden. Der Dienst erkennt die Anwesenheit und die Bewegungen von Personen im Video. Er kann beispielsweise die Personen zählen, die einen bestimmten Bereich betreten, oder messen, ob eine Mund-Nasen-Bedeckung getragen wird und die geltenden Abstandsregeln eingehalten werden. Durch die Verarbeitung von Videostreams aus physischen Bereichen können Sie ableiten, wie Menschen diese nutzen, und den Wert der Bereiche für Ihre Organisation maximieren. 

<!--This documentation contains the following types of articles:
* The [quickstarts](./quickstarts-sdk/analyze-image-client-library.md) are step-by-step instructions that let you make calls to the service and get results in a short period of time. 
* The [how-to guides](./Vision-API-How-to-Topics/HowToCallVisionAPI.md) contain instructions for using the service in more specific or customized ways.
* The [conceptual articles](tbd) provide in-depth explanations of the service's functionality and features.
* The [tutorials](./tutorials/storage-lab-tutorial.md) are longer guides that show you how to use this service as a component in broader business solutions.-->

## <a name="what-it-does"></a>Funktionsbeschreibung
Die räumliche Analyse erfasst Videos und erkennt dann Personen im Video. Nachdem Personen erkannt wurden, verfolgt das System diese nach, während sie sich im Laufe der Zeit bewegen, und generiert dann Ereignisse, wenn die Personen mit Regionen von Interesse interagieren. Alle Vorgänge liefern Erkenntnisse aus dem Sichtfeld einer einzelnen Kamera. 

### <a name="people-counting"></a>Personenzählung
Bei diesem Vorgang werden die Personen mithilfe des Vorgangs „PersonCount“ in einer bestimmten Zone im Laufe der Zeit gezählt. Er generiert eine unabhängige Anzahl für jeden verarbeiteten Frame, ohne zu versuchen, Personen über Frames hinweg nachzuverfolgen. Mit diesem Vorgang kann die Anzahl von Personen in einem Bereich geschätzt oder eine Warnung generiert werden, wenn eine Person erscheint.

![Die räumliche Analyse zählt die Personen im Sichtfeld der Kamera.](https://user-images.githubusercontent.com/11428131/139924111-58637f2e-f2f6-42d8-8812-ab42fece92b4.gif)

### <a name="entrance-counting"></a>Zutrittszählung
Mit diesem Feature wird überwacht, wie lange sich Personen in einem Bereich aufhalten oder wann sie durch eine Tür treten. Für diese Überwachung können die Vorgänge „PersonCrossingPolygon“ oder „PersonCrossingLine“ verwendet werden. In Einzelhandelsszenarien kann mit diesen Vorgängen die Wartezeit an der Kasse oder die Verweildauer vor einer Auslage gemessen werden. Darüber hinaus können diese Vorgänge in anderen Szenarien für Geschäftsgebäude die Personenbewegung in einer Lobby oder auf einer bestimmten Etage messen.

![Die räumliche Analyse misst die Wartezeit an der Kasse.](https://user-images.githubusercontent.com/11428131/137016574-0d180d9b-fb9a-42a9-94b7-fbc0dbc18560.gif)

### <a name="social-distancing-and-facemask-detection"></a>Abstandsregeln und Gesichtsmaskenerkennung 
Mit diesem Feature wird analysiert, wie gut Personen die Abstandsregeln in einem Bereich einhalten. Mithilfe des PersonDistance-Vorgangs kalibriert sich das System automatisch selbst, wenn Personen in einem Bereich umhergehen. Anschließend wird ermittelt, wann Personen einen bestimmten Abstandsschwellenwert (6 Fuß oder 10 Fuß) überschreiten.

![Bei der räumlichen Analyse werden Ereignisse bei Verletzung der Abstandsregeln visualisiert und Linien zwischen Personen angezeigt, die die Entfernung angeben.](https://user-images.githubusercontent.com/11428131/139924062-b5e10c0f-3cf8-4ff1-bb58-478571c022d7.gif)

Die räumliche Analyse kann außerdem so konfiguriert werden, dass erkannt wird, ob eine Person eine schützende Gesichtsbedeckung (beispielsweise eine Maske) trägt. Für die Vorgänge „PersonCount“, „PersonCrossingLine“ und „PersonCrossingPolygon“ kann durch Konfigurieren des Parameters `ENABLE_FACE_MASK_CLASSIFIER` ein Maskenklassifizierer aktiviert werden.

![Die räumliche Analyse klassifiziert, ob Personen in einem Aufzug Gesichtsmasken tragen.](https://user-images.githubusercontent.com/11428131/137015842-ce524f52-3ac4-4e42-9067-25d19b395803.png)

## <a name="get-started"></a>Erste Schritte

Befolgen Sie die [Schnellstartanleitung](spatial-analysis-container.md), um den Container zur räumlichen Analyse einzurichten und mit der Analyse von Videos zu beginnen.

## <a name="responsible-use-of-spatial-analysis-technology"></a>Verantwortungsvolle Verwendung der Technologie für räumliche Analysen

Informationen zur verantwortungsvollen Verwendung der Technologie für räumliche Analysen finden Sie im [Transparenzhinweis](/legal/cognitive-services/computer-vision/transparency-note-spatial-analysis?context=%2fazure%2fcognitive-services%2fComputer-vision%2fcontext%2fcontext). Die Transparenzhinweise von Microsoft helfen Ihnen zu verstehen, wie unsere KI-Technologie funktioniert und welche Entscheidungen Systembesitzer treffen können, die die Systemleistung und das Systemverhalten beeinflussen. Sie konzentrieren sich darauf, wie wichtig es ist, das gesamte System zu betrachten, einschließlich der Technologie, der Menschen und der Umgebung.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Schnellstart: Container für die räumliche Analyse](spatial-analysis-container.md)
