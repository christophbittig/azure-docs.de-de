---
title: Neuerungen beim maschinellen Sehen
titleSuffix: Azure Cognitive Services
description: Dieser Artikel enthält Neuigkeiten zum maschinellen Sehen.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 05/24/2021
ms.author: pafarley
ms.openlocfilehash: c2cdf1dc396a04adb106ee1b3777eb11e7c36279
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "130045482"
---
# <a name="whats-new-in-computer-vision"></a>Neuerungen beim maschinellen Sehen

Informieren Sie sich über die Neuerungen im Dienst. Dabei kann es sich um Versionshinweise, Videos, Blogbeiträge und andere Informationen handeln. Legen Sie ein Lesezeichen für diese Seite an, um über den Dienst auf dem Laufenden zu bleiben.

## <a name="september-2021"></a>September 2021

### <a name="ocr-read-api-public-preview-supports-122-languages"></a>OCR-API (Lesen) in der Public Preview unterstützt 122 Sprachen
Die [OCR-API (Lesen)](overview-ocr.md) für maschinelles Sehen erhöht die Anzahl [unterstützter Sprachen](language-support.md) in der neuesten Vorschauversion auf 122:
* Die optische Zeichenerkennung für gedruckten Text wird in 49 neuen Sprachen unterstützt, einschließlich Russisch, Bulgarisch und anderen kyrillischen sowie weiteren lateinischen Sprachen.
* Für handschriftlichen Text werden bei der optischen Zeichenerkennung 6 neue Sprachen unterstützt, einschließlich Englisch, Chinesisch (vereinfacht), Französisch, Deutsch, Italienisch, Portugiesisch und Spanisch.
* Verbesserungen bei der Verarbeitung digitaler PDF-Dateien und Text in maschinenlesbaren Bereichen (Machine Readable Zone, MRZ) von Identitätsdokumenten.
* Allgemeine Verbesserungen bei Leistung und KI-Qualität

Informationen zur Verwendung der neuen Previewfunktionen finden Sie in der [OCR-Schrittanleitung](Vision-API-How-to-Topics/call-read-api.md#determine-how-to-process-the-data-optional).

> [!div class="nextstepaction"]
> [Erste Schritte mit der Lese-API](./quickstarts-sdk/client-library.md)


## <a name="august-2021"></a>August 2021

### <a name="image-tagging-language-expansion"></a>Spracherweiterung für die Bildmarkierung

Die [neueste Version (v3.2)](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/56f91f2e778daf14a499f200) der Bildmarkierung unterstützt ab sofort Tags in 50 Sprachen. Weitere Informationen finden Sie auf der Seite mit den [unterstützten Sprachen](language-support.md).

## <a name="may-2021"></a>Mai 2021

### <a name="spatial-analysis-container-update"></a>Update für den Container für die räumliche Analyse

Für den [Container für die räumliche Analyse](spatial-analysis-container.md) wurde eine neue Version mit neuen Features veröffentlicht. Mithilfe dieses Docker-Containers können Sie in Echtzeit gestreamte Videodaten analysieren, um zu ermitteln, wie Personen räumlich zueinander in Beziehung stehen und wie sie sich durch physische Umgebungen bewegen. 

* [Räumliche Analysevorgänge](spatial-analysis-operations.md) können jetzt so konfiguriert werden, dass die Ausrichtung einer Person erkannt wird. 
    * Ein Ausrichtungsklassifizierer kann für die Vorgänge `personcrossingline` und `personcrossingpolygon` aktiviert werden, indem der Parameter `enable_orientation` konfiguriert wird. Die Standardeinstellung ist „Aus“.

* [Räumliche Analysevorgänge bieten](spatial-analysis-operations.md) jetzt auch eine Konfiguration zum Erkennen der Geschwindigkeit einer Person beim Gehen/Laufen.
     * Die Geschwindigkeit kann für die Vorgänge `personcrossingline` und `personcrossingpolygon` erkannt werden, indem der Klassifizierer `enable_speed` aktiviert wird, der standardmäßig deaktiviert ist. Die Ausgabe wird in den Ausgaben `speed`, `avgSpeed` und `minSpeed` widergespiegelt.


## <a name="april-2021"></a>April 2021

### <a name="computer-vision-v32-ga"></a>Allgemeine Verfügbarkeit von Maschinelles Sehen v3.2

Die Maschinelles Sehen-API v3.2 ist jetzt mit den folgenden Updates allgemein verfügbar:
* Verbessertes Bildmarkierungsmodell: analysiert visuelle Inhalte und generiert relevante Tags basierend auf im Bild gezeigten Objekten, Aktionen und Inhalten. Dieses Modell ist über die [Bildmarkierungs-API](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/56f91f2e778daf14a499f200) verfügbar. Weitere Informationen finden Sie in der [Schrittanleitung](./vision-api-how-to-topics/howtocallvisionapi.md) und in der [Übersicht](./overview-image-analysis.md) zur Bildanalyse.
* Aktualisiertes Inhaltsmoderationsmodell: erkennt nicht jugendfreie Inhalte und stellt Flags zum Filtern von Bildern bereit, die nicht jugendfreie und freizügige visuelle Inhalte enthalten. Dieses Modell ist über die [Analyse-API](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/56f91f2e778daf14a499f21b) verfügbar. Weitere Informationen finden Sie in der [Schrittanleitung](./vision-api-how-to-topics/howtocallvisionapi.md) und in der [Übersicht](./overview-image-analysis.md) zur Bildanalyse.
* [OCR (Lesen) für 73 Sprachen verfügbar](./language-support.md#optical-character-recognition-ocr), einschließlich vereinfachtes und traditionelles Chinesisch, Japanisch, Koreanisch und lateinische Sprachen
* [OCR (Lesen)](./overview-ocr.md) auch als [Container ohne Distribution](./computer-vision-how-to-install-containers.md?tabs=version-3-2) für die lokale Bereitstellung verfügbar

> [!div class="nextstepaction"]
> [Allgemeine Verfügbarkeit von Maschinelles Sehen v3.2](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d986960601faab4bf452005)

## <a name="march-2021"></a>März 2021

### <a name="computer-vision-32-public-preview-update"></a>Update der Public Preview von Maschinelles Sehen 3.2

Die Public Preview der Maschinelles Sehen-API v3.2 wurde aktualisiert. Die Vorschauversion verfügt über alle Features des maschinellen Sehens sowie aktualisierte APIs zum Lesen und Analysieren.

> [!div class="nextstepaction"]
> [Siehe Maschinelles Sehen v3.2 (Public Preview 3)](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d986960601faab4bf452005)

## <a name="february-2021"></a>Februar 2021

### <a name="read-api-v32-public-preview-with-ocr-support-for-73-languages"></a>Lese-API v3.2 (Public Preview) mit OCR-Unterstützung für 73 Sprachen
Die Lese-API v3.2 (Public Preview) für maschinelles Sehen ist als Clouddienst und Docker-Container verfügbar und beinhaltet diese Updates:
* [OCR für 73 Sprachen](./language-support.md#optical-character-recognition-ocr), einschließlich vereinfachtes und traditionelles Chinesisch, Japanisch, Koreanisch und lateinische Sprachen
* Natürliche Leserichtung für die Textzeilenausgabe (nur lateinische Sprachen)
* Klassifizierung der Handschrift für Textzeilen zusammen mit einer Konfidenzbewertung (nur lateinische Sprachen)
* Extrahieren von Text nur für ausgewählte Seiten bei mehrseitigen Dokumenten
* Verfügbar als [Container ohne Distribution](./computer-vision-how-to-install-containers.md?tabs=version-3-2) für die lokale Bereitstellung

Weitere Informationen finden Sie in der [Lese-API Schrittanleitung](Vision-API-How-to-Topics/call-read-api.md).

> [!div class="nextstepaction"]
> [Verwenden der Lese-API v3.2 (Public Preview)](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d986960601faab4bf452005)


## <a name="january-2021"></a>Januar 2021

### <a name="spatial-analysis-container-update"></a>Update für den Container für die räumliche Analyse

Für den [Container für die räumliche Analyse](spatial-analysis-container.md) wurde eine neue Version mit neuen Features veröffentlicht. Mithilfe dieses Docker-Containers können Sie in Echtzeit gestreamte Videodaten analysieren, um zu ermitteln, wie Personen räumlich zueinander in Beziehung stehen und wie sie sich durch physische Umgebungen bewegen. 

* [Vorgänge für räumliche Analysen](spatial-analysis-operations.md) können jetzt so konfiguriert werden, dass erkannt wird, ob eine Person eine schützende Gesichtsbedeckung (beispielsweise eine Maske) trägt. 
    * Durch Konfigurieren des Parameters `ENABLE_FACE_MASK_CLASSIFIER` kann für die Vorgänge `personcount`, `personcrossingline` und `personcrossingpolygon` ein Maskenklassifizierer aktiviert werden.
    * Die Attribute `face_mask` und `face_noMask` werden als Metadaten mit Zuverlässigkeitsbewertung für jede im Videostream erkannte Person zurückgegeben.
* Der Vorgang *personcrossingpolygon* wurde erweitert, um die Berechnung der Wartezeit einer Person in einer Zone zu ermöglichen. Sie können den Parameter `type` in der Zonenkonfiguration für den Vorgang auf `zonedwelltime` festlegen, und ein neues Ereignis vom Typ *personZoneDwellTimeEvent* enthält das Feld `durationMs` mit der Anzahl von Millisekunden, die die Person in der Zone verbracht hat.
* **Breaking Change:** Das Ereignis *personZoneEvent* wurde in *personZoneEnterExitEvent* umbenannt. Dieses Ereignis wird durch den Vorgang *personcrossingpolygon* ausgelöst, wenn eine Person die Zone betritt oder verlässt. Außerdem werden Richtungsinformationen in Form der nummerierten Seite der durchquerten Zone bereitgestellt.
* Die Video-URL kann in allen Vorgängen als „Private Parameter/obfuscated“ (Privater Parameter/verschleiert) bereitgestellt werden. Die Verschleierung ist jetzt optional und funktioniert nur, wenn `KEY` und `IV` als Umgebungsvariablen bereitgestellt werden.
* Die Kalibrierung ist für alle Vorgänge standardmäßig aktiviert. Legen Sie `do_calibration: false` fest, um sie zu deaktivieren.
* Unterstützung der automatischen Neukalibrierung über den `enable_recalibration`Parameter wurde hinzugefügt (standardmäßig deaktiviert). Ausführliche Informationen finden Sie unter [Vorgänge der räumlichen Analyse](./spatial-analysis-operations.md)
* Kamerakalibrierungsparameter für `DETECTOR_NODE_CONFIG`. Ausführliche Informationen finden Sie unter [Vorgänge der räumlichen Analyse](./spatial-analysis-operations.md).


## <a name="october-2020"></a>Oktober 2020

### <a name="computer-vision-api-v31-ga"></a>Maschinelles Sehen-API v3.1 GA

Die Maschinelles Sehen-API im Status „Allgemeine Verfügbarkeit“ (GA) wurde auf Version 3.1 aktualisiert.

## <a name="september-2020"></a>September 2020

### <a name="spatial-analysis-container-preview"></a>Container für räumliche Analyse Vorschau

Der [Container für räumliche Analyse](spatial-analysis-container.md) befindet sich nun in der Vorschauphase. Mithilfe des Features der räumlichen Analyse für maschinelles Sehen können Sie in Echtzeit gestreamte Videodaten analysieren, um zu ermitteln, wie Personen räumlich zueinander in Beziehung stehen und wie sie sich durch physische Umgebungen bewegen. Bei der räumlichen Analyse handelt es sich um einen lokal verwendbaren Docker-Container. 

### <a name="read-api-v31-public-preview-adds-ocr-for-japanese"></a>Lese-API v3.1 (Public Preview) jetzt mit OCR für Japanisch
Der Lese-API v3.1 (Public Preview) für maschinelles Sehen wurden folgende Funktionen hinzugefügt:
* OCR für Japanisch
* Für jede Textzeile wird angegeben, ob es sich um Handschrift oder um gedruckten Text handelt, und es wird eine Zuverlässigkeitsbewertung bereitgestellt (nur lateinische Sprachen).
* Bei mehrseitigen Dokumenten wird Text nur für ausgewählte Seiten oder für einen ausgewählten Seitenbereich extrahiert.

* Diese Vorschauversion der Lese-API unterstützt die Sprachen Englisch, Niederländisch, Französisch, Deutsch, Italienisch, Japanisch, Portugiesisch, Chinesisch (vereinfacht) und Spanisch.

Weitere Informationen finden Sie in der [Lese-API Schrittanleitung](Vision-API-How-to-Topics/call-read-api.md).

> [!div class="nextstepaction"]
> [Weitere Informationen zur Lese-API v3.1 (Public Preview 2)](https://westus2.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-preview-2/operations/5d986960601faab4bf452005)

## <a name="july-2020"></a>Juli 2020

### <a name="read-api-v31-public-preview-with-ocr-for-simplified-chinese"></a>Lese-API v3.1 (Public Preview) mit OCR für vereinfachtes Chinesisch
In der Lese-API v3.1 (Public Preview) für maschinelles Sehen wurde Unterstützung für vereinfachtes Chinesisch hinzugefügt.

* Diese Vorschauversion der Lese-API unterstützt die Sprachen Englisch, Niederländisch, Französisch, Deutsch, Italienisch, Portugiesisch, Chinesisch (vereinfacht) und Spanisch.

Weitere Informationen finden Sie in der [Lese-API Schrittanleitung](Vision-API-How-to-Topics/call-read-api.md).

> [!div class="nextstepaction"]
> [Weitere Informationen zur Lese-API v3.1 (Public Preview 1)](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-preview-1/operations/5d986960601faab4bf452005)

## <a name="may-2020"></a>Mai 2020
Maschinelles Sehen-API v3.0 ist nun allgemein verfügbar und beinhaltet Aktualisierungen der Lese-API:

* Unterstützung für Englisch, Niederländisch, Französisch, Deutsch, Italienisch, Portugiesisch und Spanisch
* Höhere Genauigkeit
* Konfidenzbewertung für jedes extrahierte Wort
* Neues Ausgabeformat

Weitere Informationen finden Sie in der [OCR Übersicht](overview-ocr.md).

## <a name="march-2020"></a>März 2020

* TLS 1.2 wird nun für alle HTTP-Anforderungen erzwungen, die an diesen Dienst gerichtet werden. Weitere Informationen finden Sie unter [Sicherheit von Azure Cognitive Services](../cognitive-services-security.md).

## <a name="january-2020"></a>Januar 2020

### <a name="read-api-30-public-preview"></a>Lese-API 3.0 Public Preview

Sie können nun Version 3.0 der Lese-API verwenden, um gedruckten oder handschriftlichen Text aus Bildern zu extrahieren. Verglichen mit früheren Versionen bietet die Version 3.0 Folgendes:
* Höhere Genauigkeit
* Neues Ausgabeformat
* Konfidenzbewertung für jedes extrahierte Wort
* Unterstützung der Sprachen Spanisch und Englisch mit dem Sprachparameter

Informationen zu den ersten Schritten mit der API-Version 3.0 finden Sie in einer [Schnellstartanleitung zur Textextraktion](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/REST/CSharp-hand-text.md?tabs=version-3).

## <a name="cognitive-service-updates"></a>Cognitive Services-Updates

[Azure-Updateankündigungen für Cognitive Services](https://azure.microsoft.com/updates/?product=cognitive-services)
