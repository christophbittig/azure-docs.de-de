---
title: Formularerkennungsmodelle
titleSuffix: Azure Applied AI Services
description: Konzepte für die Datenextraktion und -analyse mit vorgefertigten Modellen
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 10/07/2021
ms.author: lajanuar
recommendations: false
ms.openlocfilehash: f2e2ef59d4c3608065edab4ffd1d1ec55122f2ad
ms.sourcegitcommit: af303268d0396c0887a21ec34c9f49106bb0c9c2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/11/2021
ms.locfileid: "129754613"
---
<!-- markdownlint-disable MD033 -->

# <a name="form-recognizer-models"></a>Formularerkennungsmodelle

 Mit den vorgefertigten Modellen von Azure Formularerkennung können Sie Ihren Anwendungen und Abläufen intelligente Formularverarbeitung hinzufügen, ohne dass Sie Ihre eigenen Modelle trainieren und erstellen müssen. Vorgefertigte Modelle verwenden optische Zeichenerkennung (OCR) in Kombination mit Deep-Learning-Modellen, um vordefinierte Text- und Datenfelder zu identifizieren und zu extrahieren, die für bestimmte Formular- und Dokumenttypen üblich sind. Die Formularerkennung analysiert Formular- und Dokumentdaten und gibt dann eine organisierte, strukturierte JSON-Antwort zurück. Formularerkennung v2.1 unterstützt die Modelle Rechnung, Quittung, ID-Dokument und Visitenkarte.

## <a name="model-overview"></a>Übersicht über das Modell

| **Modell**   | **Beschreibung**   |
| --- | --- |
| 🆕[ Allgemeines Dokument (Vorschau)](#general-document-preview) | Extrahieren Sie Text, Tabellen, Strukturen, Schlüssel-Wert-Paare und benannte Entitäten.  |
| [Layout](#layout)  | Extrahiert Text- und Layoutinformationen aus Dokumenten.  |
| [Rechnung](#invoice)  | Extrahieren Sie Schlüsselinformationen aus englischen Rechnungen.  |
| [Rechnung](#receipt)  | Extrahieren Sie Schlüsselinformationen aus englischen Quittungen.  |
| [Ausweisdokument](#id-document)  | Extrahieren von Schlüsselinformationen aus US-Führerscheinen und internationalen Pässen.  |
| [Visitenkarte](#business-card)  | Extrahieren von Schlüsselinformationen aus englischen Visitenkarten.  |
| [Benutzerdefiniert](#custom) |  Extrahieren Sie Daten aus Formularen und Dokumenten, die für Ihr Unternehmen spezifisch sind. Benutzerdefinierte Modelle werden speziell für Ihre individuellen Daten und Anwendungsfälle trainiert. |

### <a name="general-document-preview"></a>Allgemeines Dokument (Vorschau)

* Die allgemeine Dokumenten-API unterstützt die meisten Formulartypen, analysiert Ihre Dokumente und ordnet Werte den von ihr gefundenen Schlüsseln und Einträgen in Tabellen zu. Sie eignet sich ideal für die Extraktion gängiger Schlüssel-Werte-Paare aus Dokumenten. Sie können das allgemeine Dokumentenmodell als Alternative zum [Training eines benutzerdefinierten Modells ohne Labels](compose-custom-models.md#train-without-labels) verwenden.

* Das allgemeine Dokumentenmodell ist ein vortrainiertes Modell und kann direkt über die REST-API aufgerufen werden.

* Das allgemeine Dokumentenmodell unterstützt Named Entity Recognition (NER) für verschiedene Entitätskategorien. NER ist die Fähigkeit, verschiedene Entitäten im Text zu identifizieren und sie in vordefinierte Klassen oder Typen zu kategorisieren, wie z. B.: Person, Ort, Ereignis, Produkt und Organisation. Die Extraktion von Entitäten kann in Szenarien nützlich sein, in denen Sie die extrahierten Werte validieren möchten. Die Entitäten werden aus dem gesamten Inhalt extrahiert.

##### <a name="sample-document-processed-in-the-form-recognizer-studio"></a>Mit dem [Formularerkennung Studio](https://formrecognizer.appliedai.azure.com/studio/prebuilt?formType=document) verarbeitetes Beispieldokument:

:::image type="content" source="media/general-document-analyze.png" alt-text="Screenshot: Allgemeine Dokumentenanalyse in Formularerkennung Studio.":::

> [!div class="nextstepaction"]
> [Mehr erfahren: allgemeines Dokumentenmodell](concept-general-document.md)

### <a name="layout"></a>Layout

Die Layout-API analysiert und extrahiert Text, Tabellen und Überschriften, Auswahlmarkierungen und Strukturinformationen aus Dokumenten und gibt eine strukturierte JSON-Datendarstellung zurück.

##### <a name="sample-form-processed-with-form-recognizer-sample-labeling-tool--layout-feature"></a>Beispielformular, das mit dem Beschriftungswerkzeug der [Formularerkennung verarbeitet wurde ](https://fott-2-1.azurewebsites.net/) Layout-Funktion:

:::image type="content" source="media/overview-layout.png" alt-text="{alt-text}":::

> [!div class="nextstepaction"]
> [Mehr erfahren: Layout-Modell](concept-layout.md)

### <a name="invoice"></a>Rechnung

Das Rechnungsmodell analysiert und extrahiert Schlüsselinformationen aus Verkaufsrechnungen. Die API analysiert Rechnungen in verschiedenen Formaten, extrahiert Schlüsselinformationen wie Kundenname, Rechnungsadresse, Fälligkeitsdatum und fälliger Betrag und gibt eine strukturierte JSON-Datendarstellung zurück.

##### <a name="sample-invoice-processed-with-form-recognizer-sample-labeling-tool"></a>Beispielrechnung verarbeitet mit [Formularerkennung und Beschriftungstool](https://fott-2-1.azurewebsites.net/):

:::image type="content" source="./media/overview-invoices.jpg" alt-text="Beispielrechnung" lightbox="./media/overview-invoices.jpg":::

> [!div class="nextstepaction"]
> [Mehr erfahren: Rechnungsmodell](concept-invoice.md)

### <a name="receipt"></a>Rechnung

Das Quittungsmodell analysiert und extrahiert Schlüsselinformationen aus Verkaufsquittungen. Die API analysiert gedruckte und handschriftliche Quittungen, extrahiert Schlüsselinformationen wie Händlername, Händlertelefonnummer, Transaktionsdatum, Steuer und Transaktionssumme und gibt eine strukturierte JSON-Datendarstellung zurück.

##### <a name="sample-receipt-processed-with-form-recognizer-sample-labeling-tool"></a>Mustereingang verarbeitet mit [Formularerkennung und Musterbeschriftungswerkzeug](https://fott-2-1.azurewebsites.net/):

:::image type="content" source="./media/overview-receipt.jpg" alt-text="Beispielbeleg" lightbox="./media/overview-receipt.jpg":::

> [!div class="nextstepaction"]
> [Mehr erfahren: Eingangsmodell](concept-receipt.md)

### <a name="id-document"></a>ID-Dokument

Das ID-Dokumentmodell analysiert und extrahiert Schlüsselinformationen aus US-Führerscheinen (alle 50 Bundesstaaten und District of Columbia) und biografischen Seiten von internationalen Reisepässen (ohne Visa und andere Reisedokumente). Die API analysiert Identitätsdokumente, extrahiert Schlüsselinformationen wie Vorname, Nachname, Adresse und Geburtsdatum und gibt eine strukturierte JSON-Datendarstellung zurück.

##### <a name="sample-us-drivers-license-processed-with-form-recognizer-sample-labeling-tool"></a>Muster eines US-Führerscheins, der mit dem Beschriftungsprogramm der Formularerkennung [ bearbeitet wurde](https://fott-2-1.azurewebsites.net/):

:::image type="content" source="./media/id-example-drivers-license.jpg" alt-text="Beispielausweis" lightbox="./media/overview-id.jpg":::

> [!div class="nextstepaction"]
> [Mehr erfahren: Identitätsdokumentmodell](concept-id-document.md)

### <a name="business-card"></a>Visitenkarte

Das Visitenkartenmodell analysiert und extrahiert Schlüsselinformationen aus Visitenkartenbildern. Die API analysiert gedruckte Visitenkarten, extrahiert Schlüsselinformationen wie Vorname, Nachname, Firmenname, E-Mail-Adresse und Telefonnummer und gibt eine strukturierte JSON-Datendarstellung zurück.

##### <a name="sample-business-card-processed-with-form-recognizer-sample-labeling-tool"></a>Mustervisitenkarte, bearbeitet mit [Formularerkennung, Musterbeschriftungstool](https://fott-2-1.azurewebsites.net/):

:::image type="content" source="./media/overview-business-card.jpg" alt-text="Beispielvisitenkarte" lightbox="./media/overview-business-card.jpg":::

> [!div class="nextstepaction"]
> [Mehr erfahren: Visitenkartenmodell](concept-business-card.md)

### <a name="custom"></a>Benutzerdefiniert

Das benutzerdefinierte Modell analysiert und extrahiert Daten aus Formularen und Dokumenten, die für Ihr Unternehmen spezifisch sind. Bei der API handelt es sich um ein maschinelles Lernprogramm, das darauf trainiert ist, Formularfelder innerhalb Ihrer unterschiedlichen Inhalte zu erkennen und Schlüssel-Wert-Paare sowie Tabellendaten zu extrahieren. Für den Einstieg benötigen Sie nur fünf Beispiele desselben Formulartyps, und Ihr benutzerdefiniertes Modell kann mit oder ohne bezeichnete Datasets trainiert werden.

##### <a name="sample-custom-form-processed-with-form-recognizer-sample-labeling-tool"></a>Beispiel für ein benutzerdefiniertes Formular, das mit dem Beschriftungswerkzeug der Formularerkennung [ bearbeitet wurde](https://fott-2-1.azurewebsites.net/):

:::image type="content" source="media/analyze.png" alt-text="Screenshot: Fenster „analyze-a-custom-form“ (benutzerdefiniertes Formular analysieren) der Formularerkennung":::

> [!div class="nextstepaction"]
> [Mehr erfahren: Benutzerdefiniertes Modell](concept-custom.md)

## <a name="data-extraction"></a>Extrahieren von Daten

 | **Modell**   | **Textextraktion** |**Schlüssel-Werte-Paare** |**Auswahlmarkierungen**   | **Tabellen**   |**Entitäten** |
  | --- | :---: |:---:| :---: | :---: |:---: |
  |🆕Allgemeines Dokument  | ✓  |  ✓ | ✓  | ✓  | ✓  |
  | Layout  | ✓  |   | ✓  | ✓  |   |
  | Rechnung  | ✓ | ✓  | ✓  | ✓ ||
  |Rechnung  | ✓  |   ✓ |   |  ||
  | ID-Dokument | ✓  |   ✓  |   |   ||
  | Visitenkarte    | ✓  |   ✓ |   |   ||
  | Benutzerdefiniert             |✓  |  ✓ | ✓  | ✓  | ✓  |

## <a name="input-requirements"></a>Eingabeanforderungen

* Die besten Ergebnisse erzielen Sie, wenn Sie pro Dokument ein deutliches Foto oder einen hochwertigen Scan einreichen.
* Unterstützte Dateiformate: JPEG, PNG, BMP, TIFF und PDF (in Text eingebettet oder gescannt). In Text eingebettete PDF-Dateien sind am besten geeignet, um die Möglichkeit von Fehlern beim Extrahieren und Auffinden von Zeichen auszuschließen.
* Bei PDF und TIFF können bis zu 2000 Seiten verarbeitet werden (bei einem kostenlosen Abonnement werden nur die ersten beiden Seiten verarbeitet).
* Die Dateigröße muss weniger als 50 MB betragen.
* Bei Bildern müssen die Abmessungen zwischen 50 × 50 Pixel und 10.000 × 10.000 Pixel liegen.
* Die PDF-Abmessungen sind bis zu 17 x 17 Zoll, was dem Papierformat Legal oder A3 entspricht, oder kleiner.
* Der Gesamtumfang der Trainingsdaten beträgt 500 Seiten oder weniger.
* Wenn Ihre PDFs passwortgeschützt sind, müssen Sie die Sperre vor der Einreichung aufheben.
* Für unüberwachtes Lernen (ohne gelabelte Daten):
  * Die Daten müssen Schlüssel und Werte enthalten.
  * Die Schlüssel müssen über oder links von den Werten stehen; sie dürfen nicht unter oder rechts von ihnen stehen.

> [!NOTE]
> Das [Probenbeschriftungswerkzeug](https://fott-2-1.azurewebsites.net/) unterstützt nicht das BMP-Dateiformat. Dies ist eine Einschränkung des Tools, nicht des Formularerkennungsdienstes.

## <a name="form-recognizer-preview-v30"></a>Formularerkennung Vorschau v3.0

  Formularerkennung v3.0 (Vorschau) bietet eine Reihe neuer Funktionen und Möglichkeiten:

* [**Allgemeines Dokument (Vorschau)** ](concept-general-document.md)Modell ist eine neue API, die ein vorab trainiertes Modell verwendet, um Text, Tabellen, Strukturen, Schlüssel-Wert-Paare und benannte Entitäten aus Formularen und Dokumenten zu extrahieren.
* Das Modell [**Receipt (preview)** ](concept-receipt.md) unterstützt die Verarbeitung von einseitigen Hotelbelegen.
* [**ID-Dokument (Vorschau)** ](concept-id-document.md) Modell unterstützt Vermerke, Einschränkungen und Fahrzeugklassifizierungen aus US-Führerscheinen.
* [**Custom Model API (Vorschau)** ](concept-custom.md) unterstützt die Erkennung von Unterschriften für benutzerdefinierte Formulare.

### <a name="version-migration"></a>Versions-Migration

Erfahren Sie, wie Sie Formularerkennung v3.0 in Ihren Anwendungen verwenden können, indem Sie unserem [**Formularerkennung v3.0 Migrationsleitfaden**](v3-migration-guide.md) folgen

## <a name="next-steps"></a>Nächste Schritte

* [Lernen Sie, wie Sie Ihre eigenen Formulare und Dokumente verarbeiten können](quickstarts/try-sample-label-tool.md) mit unserem [Beispieltool der Formularerkennung](https://fott-2-1.azurewebsites.net/)

* Führen Sie einen [Schnellstart für die Formularerkennung](quickstarts/try-sdk-rest-api.md) durch und beginnen Sie mit der Erstellung einer Anwendung zur Formularverarbeitung in der Entwicklungssprache Ihrer Wahl.
