---
title: Was ist Optische Zeichenerkennung?
titleSuffix: Azure Cognitive Services
description: Die optische Zeichenerkennung (OCR) extrahiert sichtbaren Text in einem Bild und gibt ihn als strukturierte Zeichen folgen zurück.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 06/21/2021
ms.author: pafarley
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 02654c3f196b6c3bc199e636b3601777ed372a99
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "129992532"
---
# <a name="what-is-optical-character-recognition"></a>Was ist Optische Zeichenerkennung?

Die optische Zeichenerkennung (Optical Character Recognition, OCR) ermöglicht es Ihnen, gedruckten oder handschriftlichen Text aus Bildern (z. B. Fotos von Straßenschildern und Produkten) sowie aus Dokumenten (etwa Rechnungen, Finanzberichte, Artikel und vieles mehr) zu extrahieren. Die OCR-Technologien von Microsoft unterstützen das Extrahieren von gedrucktem Text in [mehreren Sprachen](./language-support.md). Nutzen Sie einen [Schnellstart](./quickstarts-sdk/client-library.md), um erste Schritte auszuführen.

![OCR-Demos](./Images/ocr-demo.gif)

Diese Dokumentation enthält die folgenden Arten von Artikeln:
* In den [Schnellstarts](./quickstarts-sdk/client-library.md) finden Sie Schritt-für-Schritt-Anleitungen, mit denen Sie Aufrufe an den Dienst senden können und in kurzer Zeit Ergebnisse erhalten. 
* Die [Anleitungen](./Vision-API-How-to-Topics/call-read-api.md) enthalten Anweisungen zur spezifischeren oder individuelleren Verwendung des Diensts.
<!--* The [conceptual articles](Vision-API-How-to-Topics/call-read-api.md) provide in-depth explanations of the service's functionality and features.
* The [tutorials](./tutorials/storage-lab-tutorial.md) are longer guides that show you how to use this service as a component in broader business solutions. -->

## <a name="read-api"></a>Lese-API 

Die [Lese-API](https://centraluseuap.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d986960601faab4bf452005) für maschinelles Sehen ist die neueste OCR-Technologie von Azure ([Neuerungen kennenlernen](./whats-new.md)), mit der sich gedruckter Text und handschriftlicher Text in mehreren Sprachen, Ziffern und Währungssymbole aus Bildern und mehrseitigen PDF-Dokumenten extrahieren lassen. Die API ist für das Extrahieren von Text aus textlastigen Bildern und mehrseitigen PDF-Dokumenten mit gemischten Sprachen optimiert. Sie unterstützt sowohl die Erkennung von gedrucktem als auch von handschriftlichem Text im selben Bild oder Dokument.

![So konvertiert die optische Zeichenerkennung Bilder und Dokumente in eine strukturierte Ausgabe mit extrahiertem Text](./Images/how-ocr-works.svg)

## <a name="input-requirements"></a>Eingabeanforderungen

Der Aufruf **Read** nimmt Bilder und Dokumente als Eingabe entgegen. Es bestehen die folgenden Anforderungen:

* Unterstützte Dateiformate: JPEG, PNG, BMP, PDF und TIFF
* Für PDF- und TIFF-Dateien werden bis zu 2000 Seiten (nur die ersten beiden Seiten für den Free-Tarif) verarbeitet.
* Die Dateigröße muss weniger als 50 MB (6 MB beim Free-Tarif) betragen und eine Größe von mindestens 50 x 50 Pixel und höchstens 10.000 × 10.000 Pixel aufweisen. 

## <a name="supported-languages"></a>Unterstützte Sprachen
Die Lese-API unterstützt 122 Sprachen bei gedrucktem Text und 7 Sprachen bei handschriftlichem Text, einschließlich Vorschausprachen und -features.

Die optische Zeichenerkennung für gedruckten Text umfasst Unterstützung für Englisch, Französisch, Deutsch, Italienisch, Portugiesisch, Spanisch, Chinesisch, Japanisch, Koreanisch und Russisch (Vorschau) sowie mit dem neuesten Vorschauupdate noch weitere lateinische und kyrillische Sprachen.

Bei handschriftlichem Text umfasst die OCR-Unterstützung Englisch sowie als Vorschauversion auch Unterstützung für Französisch, Deutsch, Italienisch, Portugiesisch, Spanisch und Chinesisch.

Informationen zu den Sprachen und Features während der Vorschau finden Sie unter [Angeben der Modellversion](./Vision-API-How-to-Topics/call-read-api.md#determine-how-to-process-the-data-optional). Eine vollständige Liste finden Sie unter [Sprachunterstützung für maschinelles Sehen](./language-support.md#optical-character-recognition-ocr). Das Vorschaumodell enthält alle Verbesserungen an der derzeit allgemein verfügbaren Version.

## <a name="key-features"></a>Wichtigste Funktionen

Die Lese-API umfasst die folgenden Funktionen:

* Extrahieren von gedrucktem Text in 122 Sprachen
* Extrahieren von handschriftlichem Text in 7 Sprachen
* Textzeilen und Wörter mit Standort und Zuverlässigkeitsbewertungen
* Keine Sprachenerkennung erforderlich
* Unterstützung für gemischte Sprachen und gemischten Modus (gedruckter und handschriftlicher Text)
* Auswählen von Seiten und Seitenbereichen aus großen, mehrseitigen Dokumenten
* Option für die Textzeilenausgabe in natürlicher Leserichtung (nur lateinische Sprachen)
* Handschriftklassifizierung für Textzeilen (nur Lateinisch)
* Verfügbar als Docker-Container ohne Distribution für die lokale Bereitstellung

[Hier](./vision-api-how-to-topics/call-read-api.md) erfahren Sie mehr über die Verwendung der OCR-Features.

## <a name="use-the-cloud-api-or-deploy-on-premise"></a>Verwenden der Cloud-API oder lokale Bereitstellung
Die Read 3.x-Cloud-APIs sind die bevorzugte Option der meisten Kunden, da sie sich einfach integrieren und schnell vorgefertigt produktiv einsetzen lassen. Azure und der Dienst für maschinelles Sehen verarbeiten Skalierungs-, Leistungs-, Datensicherheits- und Complianceanforderungen, während Sie sich auf die Erfüllung der Anforderungen Ihrer Kunden konzentrieren.

Für die lokale Bereitstellung ermöglicht der [Read-Docker-Container (Vorschau)](./computer-vision-how-to-install-containers.md) Ihnen die Bereitstellung der neuen OCR-Funktionen in Ihrer eigenen lokalen Umgebung. Container eignen sich hervorragend für bestimmte Sicherheits- und Datengovernanceanforderungen.

> [!WARNING]
> Die Operationen von Computer Vision 2.0 RecognizeText werden derzeit zugunsten der neuen [Lese-API](#read-api), die in diesem Artikel behandelt wird, veraltet. Bestehende Kunden sollten dazu [übergehen, Lesevorgänge zu nutzen](upgrade-api-versions.md).

## <a name="data-privacy-and-security"></a>Datenschutz und Sicherheit

Wie bei allen Cognitive Services-Diensten müssen Entwickler, die den Maschinelles Sehen-Dienst nutzen, die Microsoft-Richtlinien zu Kundendaten beachten. Weitere Informationen finden Sie im Microsoft Trust Center auf der [Seite zu Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices).

## <a name="next-steps"></a>Nächste Schritte

- Beginnen Sie mit den [Schnellstartanleitungen für die OCR (Read)-REST-API oder für die Clientbibliothek](./quickstarts-sdk/client-library.md).
- Erfahren Sie mehr über die [Read 3.2-REST-API](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d986960601faab4bf452005).
