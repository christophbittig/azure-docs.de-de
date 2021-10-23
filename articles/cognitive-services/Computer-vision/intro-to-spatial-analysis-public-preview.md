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
ms.openlocfilehash: 9553c7e177bdc78b071d6ed68725879e46c9ef5e
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/07/2021
ms.locfileid: "129668445"
---
# <a name="what-is-spatial-analysis"></a>Was ist räumliche Analyse?

Räumliche Analyse ist ein KI-Dienst, der Unternehmen dabei unterstützt, den Wert ihrer physischen Plätze zu maximieren, indem die Bewegungen und Präsenz der Menschen in einem bestimmten Bereich nachvollzogen werden. Der Dienst bietet Ihnen die Möglichkeit, Videos von Überwachungskameras zu erfassen, Erkenntnisse aus Videostreams zu gewinnen und Ereignisse zu generieren, die von anderen Systemen verwendet werden können. Mithilfe von Videos von Kamerastreams kann der Dienst beispielsweise die Personen zählen, die einen bestimmten Bereich betreten, oder messen, ob eine Mund-Nasen-Bedeckung getragen wird und die geltenden Abstandsregeln eingehalten werden.

<!--This documentation contains the following types of articles:
* The [quickstarts](./quickstarts-sdk/analyze-image-client-library.md) are step-by-step instructions that let you make calls to the service and get results in a short period of time. 
* The [how-to guides](./Vision-API-How-to-Topics/HowToCallVisionAPI.md) contain instructions for using the service in more specific or customized ways.
* The [conceptual articles](tbd) provide in-depth explanations of the service's functionality and features.
* The [tutorials](./tutorials/storage-lab-tutorial.md) are longer guides that show you how to use this service as a component in broader business solutions.-->

## <a name="what-it-does"></a>Funktionsbeschreibung

Die grundlegenden Vorgänge der räumlichen Analyse basieren auf einem System, das Videos erfasst, Personen im Video erkennt, diese verfolgt, während sie sich bewegen, und Ereignisse generiert, wenn Personen mit relevanten Bereichen interagieren.

## <a name="spatial-analysis-features"></a>Funktionen für räumliche Analysen

| Funktion | Definition |
|------|------------|
| **Personenerkennung** | Diese Komponente beantwortet die Frage, wo sich die Personen auf einem Bild befinden. Sie findet Personen in einem Bild und übergibt Koordinaten eines Begrenzungsrahmens, die die Aufenthaltsorte der einzelnen Personen markiert, an die Komponente zur **Personenverfolgung**. |
| **Personenverfolgung** | Diese Komponente verbindet die erkannten Personen im Laufe der Zeit, während sich diese vor der Kamera bewegen. Dafür verwendet sie temporale Logik zu den üblichen Wegen der Menschen und grundlegende Informationen zu ihrem äußeren Erscheinungsbild. Personen werden nicht über mehrere Kameras hinweg beobachtet. Wenn eine Person für etwas länger als eine Minute aus dem Sichtfeld hinaustritt und dann wieder in das Sichtfeld läuft, wird diese vom System als neue Person wahrgenommen. Durch das Tracking von Personen können keine Personen kameraübergreifend eindeutig identifiziert werden. Die Gesichtserkennung oder die Nachverfolgung der Gangart werden nicht genutzt. |
| **Gesichtsmaskenerkennung** | Diese Komponente erkennt die Position des Gesichts einer Person im Kamerasichtfeld und identifiziert das Vorhandensein einer Gesichtsmaske. Zu diesem Zweck scannt der KI-Vorgang Bilder aus einem Video. Immer dann, wenn ein Gesicht erkannt wird, stellt der Dienst einen Begrenzungsrahmen um das Gesicht bereit. Mithilfe der Objekterkennungsfunktionen kann nun im Begrenzungsrahmen identifiziert werden, ob die Person eine Gesichtsmaske trägt. Die Gesichtsmaskenerkennung kann nicht zwischen Gesichtern unterscheiden oder Gesichtsmerkmale vorhersagen oder erkennen und keine Gesichtserkennung durchführen. |
| **Relevanter Bereich** | Diese Komponente ist eine benutzerdefinierte Zone oder Linie im Eingangsvideobild. Wenn eine Person mit diesem Bereich auf dem Video interagiert, erzeugt das System ein Ereignis. Beispielsweise ist für den Vorgang **PersonCrossingLine** eine Linie im Video definiert. Wenn eine Person diese Linie überquert, wird ein Ereignis generiert. |
| **Event** | Ein Ereigniss stellen die primäre Ausgabe der räumlichen Analyse dar. Jeder Vorgang löst ein bestimmtes Ereignis entweder regelmäßig (etwa einmal pro Minute) oder bei jedem Auftreten eines bestimmten Auslösers aus. Das Ereignis umfasst Informationen darüber, was im Eingabevideo passiert ist, enthält aber keine Bilder oder Videos. Beispielsweise kann der Vorgang **PeopleCount** jedes Mal bei Änderung der Personenanzahl (Trigger) oder einmal pro Minute (regelmäßig) ein Ereignis auslösen, das die aktuelle Anzahl enthält. |

## <a name="get-started"></a>Erste Schritte

Befolgen Sie die [Schnellstartanleitung](spatial-analysis-container.md), um den Container zur räumlichen Analyse einzurichten und mit der Analyse von Videos zu beginnen.

## <a name="responsible-use-of-spatial-analysis-technology"></a>Verantwortungsvolle Verwendung der Technologie für räumliche Analysen

Informationen zur verantwortungsvollen Verwendung der Technologie für räumliche Analysen finden Sie im [Transparenzhinweis](/legal/cognitive-services/computer-vision/transparency-note-spatial-analysis?context=%2fazure%2fcognitive-services%2fComputer-vision%2fcontext%2fcontext). Die Transparenzhinweise von Microsoft sollen Ihnen helfen zu verstehen, wie unsere KI-Technologie funktioniert, welche Entscheidungen Systembesitzer treffen können, die die Systemleistung und das Systemverhalten beeinflussen, und wie wichtig es ist, das gesamte System zu betrachten, einschließlich der Technologie, der Menschen und der Umgebung.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Schnellstart: Container für die räumliche Analyse](spatial-analysis-container.md)
