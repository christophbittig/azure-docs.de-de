---
title: Was ist Custom Vision?
titleSuffix: Azure Cognitive Services
description: Erfahren Sie, wie Sie den Azure Custom Vision-Dienst verwenden, um benutzerdefinierte KI-Modelle zur Erkennung von Objekten oder Klassifizierung von Bildern zu erstellen.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: overview
ms.date: 08/25/2021
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
keywords: bilderkennung, bildbezeichner, app für bilderkennung, custom vision
ms.openlocfilehash: 6bf2f9de103416bc9dd35216bf85c609be966a23
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132372509"
---
# <a name="what-is-custom-vision"></a>Was ist Custom Vision?

Azure Custom Vision ist ein Bilderkennungsdienst zum Erstellen, Bereitstellen und Optimieren benutzerdefinierter Bildbezeichnermodellen. Ein Bildbezeichner wendet entsprechend den erkannten visuellen Merkmalen auf einem Bild Bezeichnungen (die Klassifizierungen oder Objekte darstellen) auf Bilder an. Anders als beim Dienst für [maschinelles Sehen](../computer-vision/overview.md) können Sie bei Custom Vision Ihre eigenen Bezeichnungen angeben und benutzerdefinierte Modelle für deren Erkennung trainieren.

Diese Dokumentation enthält die folgenden Arten von Artikeln:
* In den [Schnellstarts](./getting-started-build-a-classifier.md) finden Sie Schritt-für-Schritt-Anleitungen, mit denen Sie Aufrufe an den Dienst senden können und in kurzer Zeit Ergebnisse erhalten.
* Die [Anleitungen](./test-your-model.md) enthalten Anweisungen zur spezifischeren oder individuelleren Verwendung des Diensts.
* Die [Tutorials](./iot-visual-alerts-tutorial.md) sind ausführlichere Leitfäden, in denen die Verwendung dieses Diensts als Komponente in umfassenderen Unternehmenslösungen veranschaulicht wird.
<!--* The [conceptual articles](Vision-API-How-to-Topics/call-read-api.md) provide in-depth explanations of the service's functionality and features.-->

## <a name="what-it-does"></a>Funktionsbeschreibung

Der Custom Vision-Dienst verwendet einen Machine-Learning-Algorithmus, um Bilder zu analysieren. Sie als Entwickler müssen Gruppen von Bildern übermitteln, auf denen die betreffenden Merkmale vorhanden bzw. nicht vorhanden sind. Die Bezeichnungen wenden Sie selbst beim Übermitteln auf die Bilder an. Der Algorithmus wird dann mit diesen Daten trainiert und berechnet seine eigene Genauigkeit, indem er Tests anhand dieser Bilder durchführt. Nachdem der Algorithmus trainiert wurde, können Sie ihn testen, noch mal trainieren und schließlich für Ihre Bilderkennungs-App verwenden, um [Bilder zu klassifizieren](getting-started-build-a-classifier.md). Darüber hinaus kann das [Modell exportiert](export-your-model.md) und offline verwendet werden.

### <a name="classification-and-object-detection"></a>Klassifizierung und Objekterkennung

Custom Vision bietet zwei Features: Die **[Bildklassifizierung](getting-started-build-a-classifier.md)** wendet eine oder mehrere Bezeichnungen auf ein Bild an. Die **[Objekterkennung](get-started-build-detector.md)** funktioniert ähnlich, gibt aber auch die Koordinaten zurück, an denen sich die angewendeten Bezeichnungen im Bild befinden.

### <a name="optimization"></a>Optimization

Der Custom Vision-Dienst ist für die schnelle Erkennung wesentlicher Unterschiede zwischen Bildern optimiert, sodass Sie anhand einer kleinen Datenmenge mit der Prototyperstellung für Ihr Modell beginnen können. 50 Bilder pro Bezeichnung sind im Allgemeinen ein guter Ausgangspunkt. Für die Erkennung geringfügiger Bildunterschiede (etwa kleinere Risse oder Dellen in Qualitätssicherungsszenarien) ist der Dienst dagegen weniger geeignet.

Darüber hinaus stehen verschiedene Varianten des Custom Vision-Algorithmus zur Verfügung, die jeweils für Bilder mit bestimmtem Material optimiert sind, z. B. für Wahrzeichen oder Einzelhandelsartikel. Weitere Informationen finden Sie unter [Auswählen einer Domäne](select-domain.md).

## <a name="what-it-includes"></a>Lieferumfang

Der Custom Vision-Dienst ist als Gruppe nativer SDKs sowie über eine webbasierte Oberfläche auf der [Custom Vision-Website](https://customvision.ai/) verfügbar. Beide Schnittstellen können einzeln oder zusammen zum Erstellen, Testen und Trainieren eines Modells verwendet werden.

![Custom Vision-Website in einem Chrome-Browserfenster](media/browser-home.png)

## <a name="data-privacy-and-security"></a>Datenschutz und Sicherheit

Wie bei allen Cognitive Services-Diensten müssen Entwickler, die den Custom Vision-Dienst nutzen, die Microsoft-Richtlinien zu Kundendaten beachten. Weitere Informationen finden Sie im Microsoft Trust Center auf der [Seite zu Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices).

## <a name="next-steps"></a>Nächste Schritte

Unter [Schnellstart: Erstellen einer Klassifizierung mit Custom Vision](getting-started-build-a-classifier.md) werden die ersten Schritte mit Custom Vision im Webportal beschrieben. Sie können aber auch eine [Schnellstartanleitung](quickstarts/image-classification.md) durcharbeiten, um einfache Codeszenarien zu implementieren.