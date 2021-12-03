---
title: Was ist eine Bildanalyse?
titleSuffix: Azure Cognitive Services
description: Der Image Analysis Service verwendet vorab trainierte Ki-Modelle, um viele verschiedene visuelle Features aus Bildern zu extrahieren.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 06/21/2021
ms.author: pafarley
keywords: maschinelles Sehen, Anwendungen für maschinelles Sehen, Dienst für maschinelles Sehen
ms.openlocfilehash: 46e01af8fb0022e2e003565a07dca6e7e2713dda
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132372566"
---
# <a name="what-is-image-analysis"></a>Was ist eine Bildanalyse?

Der Maschinelles Sehen Image Analysis Service kann eine Vielzahl von visuellen Features aus Ihren Bildern extrahieren. Beispielsweise kann es ermitteln, ob ein Bild nicht jugendfreie Inhalte aufweist, oder es können bestimmte Marken oder Objekte oder menschliche Gesichter gesucht werden.

Sie können die Bild-Analyse in Ihrer Anwendung über ein Clientbibliothek-SDK oder durch direktes Aufrufen der [REST API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/5d986960601faab4bf452005) nutzen. Im [Schnellstart](quickstarts-sdk/image-analysis-client-library.md) erfahren Sie mehr zu den ersten Schritten.

> [!div class="nextstepaction"]
> [Erste Schritte](quickstarts-sdk/image-analysis-client-library.md)

Diese Dokumentation enthält die folgenden Arten von Artikeln:
* In den [Schnellstarts](./quickstarts-sdk/image-analysis-client-library.md) finden Sie Schritt-für-Schritt-Anleitungen, mit denen Sie Aufrufe an den Dienst senden können und in kurzer Zeit Ergebnisse erhalten. 
* Die [Anleitungen](./Vision-API-How-to-Topics/HowToCallVisionAPI.md) enthalten Anweisungen zur spezifischeren oder individuelleren Verwendung des Diensts.
* Die [konzeptionellen Artikel](concept-tagging-images.md) enthalten ausführliche Erläuterungen der Funktionen und Features eines Diensts.
* Die [Tutorials](./tutorials/storage-lab-tutorial.md) sind ausführlichere Leitfäden, in denen die Verwendung dieses Diensts als Komponente in umfassenderen Unternehmenslösungen veranschaulicht wird.

## <a name="image-analysis-features"></a>Funktionen für die Bildanalyse

Sie können Bilder analysieren, um Erkenntnisse zu visuellen Merkmalen und Eigenschaften zu gewinnen. Alle Features in der folgenden Liste werden von der [API für die Bildanalyse](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/56f91f2e778daf14a499f21b) bereitgestellt. Nutzen Sie einen [Schnellstart](./quickstarts-sdk/image-analysis-client-library.md), um erste Schritte auszuführen.


### <a name="tag-visual-features"></a>Markieren visueller Merkmale

Erkennen und markieren Sie visuelle Merkmale in einem Bild auf der Grundlage von Tausenden von erkennbaren Objekten, Lebewesen, Landschaften und Aktionen. Wenn die Tags nicht eindeutig sind oder nicht zum Allgemeinwissen gehören, enthält die API-Antwort Hinweise, um den Kontext des Tags zu verdeutlichen. Die Markierung ist nicht auf den Hauptinhalt (etwa eine Person im Vordergrund) beschränkt, sondern bezieht auch die Umgebung (Innen- oder Außenbereich), Möbel, Werkzeuge, Pflanzen, Tiere, Zubehör, Geräte und Ähnliches mit ein. [Markieren visueller Merkmale](concept-tagging-images.md)

:::image type="content" source="Images/tagging.png" alt-text="Ein Bild eines Skateboarders mit Tags auf der rechten Seite":::


### <a name="detect-objects"></a>Erkennen von Objekten

Die Objekterkennung ähnelt dem Tagging, die API gibt jedoch die Koordinaten des Begrenzungsrahmens für jedes angewendete Tag zurück. Wenn ein Bild z. B. einen Hund, eine Katze und eine Person enthält, werden diese Objekte bei der Erkennung zusammen mit ihren Koordinaten im Bild aufgelistet. Sie können diese Funktion verwenden, um weitere Beziehungen zwischen den Objekten in einem Bild zu verarbeiten. Außerdem können Sie feststellen, ob mehrere Instanzen des gleichen Tags in einem Bild enthalten sind. [Erkennen von Objekten](concept-object-detection.md)

:::image type="content" source="Images/detect-objects.png" alt-text="Ein Bild eines Büros mit einem Laptop (gelb umrahmt)":::


### <a name="detect-brands"></a>Erkennen von Marken

Erkennen Sie auf Bildern oder in Videos kommerzielle Marken auf der Grundlage einer Datenbank mit Tausenden Logos aus der ganzen Welt. Mit diesem Feature können Sie beispielsweise ermitteln, welche Marken in sozialen Medien besonders beliebt sind oder besonders gerne in Medien platziert werden. [Erkennen von Marken](concept-brand-detection.md)

### <a name="categorize-an-image"></a>Kategorisieren eines Bilds

Erkennen und kategorisieren Sie ein gesamtes Bild unter Verwendung einer [Kategorietaxonomie](Category-Taxonomy.md) mit über-/untergeordneten vererbbaren Hierarchien. Kategorien können einzeln oder in Kombination mit unseren neuen Markierungsmodellen verwendet werden.<br/>Als Sprache für die Markierung und Kategorisierung von Bildern wird derzeit nur Englisch unterstützt. [Kategorisieren eines Bilds](concept-categorizing-images.md)

### <a name="describe-an-image"></a>Beschreiben eines Bilds

Generieren Sie eine Beschreibung eines gesamten Bilds mit vollständigen Sätzen in lesbarer Sprache. Algorithmen für maschinelles Sehen generieren verschiedene Beschreibungen auf der Grundlage der im Bild erkannten Objekte. Die Beschreibungen werden jeweils ausgewertet, und eine Zuverlässigkeitsbewertung wird generiert. Dann wird eine Liste in der Reihenfolge von höchster Zuverlässigkeitsbewertung zu niedrigster zurückgegeben. [Beschreiben eines Bilds](concept-describing-images.md)

:::image type="content" source="Images/description.png" alt-text="Ein Bild mit Kühen mit einer einfachen Beschreibung auf der rechten Seite":::

### <a name="detect-faces"></a>Erkennen von Gesichtern

Erkennen Sie Gesichter in einem Bild, und stellen Sie Informationen zu den einzelnen Gesichtern bereit. Maschinelles Sehen gibt für jedes erkannte Gesicht die Koordinaten, ein Rechteck, das Geschlecht und das Alter zurück.<br/>Maschinelles Sehen bietet einige Funktionen des Diensts [Gesichtserkennung](../face/index.yml). Der Gesichtserkennungsdienst kann für eine eingehendere Analyse (Gesichtsausdruck, Kopfhaltung und Ähnliches) verwendet werden. [Erkennen von Gesichtern](concept-detecting-faces.md)

### <a name="detect-image-types"></a>Erkennen von Bildtypen

Erkennen Sie Merkmale eines Bilds – also beispielsweise, ob es sich bei dem Bild um eine Strichzeichnung handelt oder wie wahrscheinlich es ist, dass es sich bei dem Bild um ein ClipArt handelt. [Erkennen von Bildtypen](concept-detecting-image-types.md)

### <a name="detect-domain-specific-content"></a>Erkennen domänenspezifischer Inhalte

Verwenden Sie Domänenmodelle, um domänenspezifische Inhalte (etwa berühmte Personen und Orientierungspunkte) in einem Bild zu erkennen. Wenn ein Bild also beispielsweise Personen enthält, kann maschinelles Sehen auf ein Domänenmodell für berühmte Personen zurückgreifen und so ermitteln, ob es sich bei den Personen auf dem Bild um berühmte Personen handelt. [Erkennen domänenspezifischer Inhalte](concept-detecting-domain-content.md)

### <a name="detect-the-color-scheme"></a>Erkennen des Farbschemas

Analysieren Sie die Farben eines Bilds. Maschinelles Sehen kann ermitteln, ob es sich um ein Schwarzweißbild oder um ein Farbbild handelt. Bei Farbbildern kann maschinelles Sehen außerdem die dominante Farbe sowie Akzentfarben erkennen. [Erkennen des Farbschemas](concept-detecting-color-schemes.md)



### <a name="generate-a-thumbnail"></a>Generieren von Miniaturansichten

Analysieren Sie den Inhalt eines Bilds, um eine geeignete Miniaturansicht für das Bild zu generieren. Die Maschinelles Sehen-API generiert zunächst eine hochwertige Miniaturansicht und analysiert dann die Objekte im Bild, um den *relevanten Bereich* zu bestimmen. Anschließend wird das Bild auf den relevanten Bereich zugeschnitten. Das Seitenverhältnis der generierten Miniaturansicht kann sich bei Bedarf vom Seitenverhältnis des ursprünglichen Bilds unterscheiden. [Generieren von Miniaturansichten](concept-generating-thumbnails.md)

:::image type="content" source="Images/thumbnail-demo.png" alt-text="Ein Bild mit einer Person auf einem Berg, mit zugeschnittenen Versionen auf der rechten Seite":::


### <a name="get-the-area-of-interest"></a>Abrufen des relevanten Bereichs

Analysieren Sie den Inhalt eines Bilds, um die Koordinaten des *relevanten Bereichs* zurückzugeben. Anstatt das Bild zuzuschneiden und eine Miniaturansicht zu generieren, gibt maschinelles Sehen die Koordinaten des Begrenzungsrahmens des Bereichs zurück, sodass die aufrufende Anwendung das ursprüngliche Bild nach Bedarf ändern kann. [Abrufen des relevanten Bereichs](concept-generating-thumbnails.md#area-of-interest)

### <a name="moderate-content-in-images"></a>Moderieren von Bildinhalten

Maschinelles Sehen ermöglicht die [Erkennung nicht jugendfreier Inhalte](concept-detecting-adult-content.md) in einem Bild sowie die Rückgabe einer Zuverlässigkeitsbewertung für verschiedene Klassifizierungen. Der Schwellenwert für die Kennzeichnung von Inhalten kann mithilfe eines Schiebereglers nach Bedarf angepasst werden.

## <a name="image-requirements"></a>Bildanforderungen

Die Bild-Analyse kann Bilder analysieren, die folgende Anforderungen erfüllen:

- Das Bild muss im JPEG-, PNG-, GIF- oder BMP-Format vorliegen.
- Die Dateigröße muss weniger als 4 MB betragen.
- Das Bild muss größer als 50 x 50 Pixel sein.

## <a name="data-privacy-and-security"></a>Datenschutz und Sicherheit

Wie bei allen Cognitive Services-Diensten müssen Entwickler, die den Maschinelles Sehen-Dienst nutzen, die Microsoft-Richtlinien zu Kundendaten beachten. Weitere Informationen finden Sie im Microsoft Trust Center auf der [Seite zu Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices).

## <a name="next-steps"></a>Nächste Schritte

Führen Sie anhand der Schnellstartanleitung in der bevorzugten Entwicklungssprache erste Schritte für die Bild-Analyse aus:

- [Schnellstart: Verwenden der Clientbibliothek für maschinelles Sehen](./quickstarts-sdk/image-analysis-client-library.md)
