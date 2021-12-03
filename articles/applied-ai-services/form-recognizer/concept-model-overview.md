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
ms.custom: ignite-fall-2021
ms.openlocfilehash: 0ac003f812078f2bb3b27710068b7350468ad8fb
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131027137"
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

:::image type="content" source="media/studio/general-document.png" alt-text="Screenshot: Symbol „Allgemeines Dokument“ in Studio":::

* Die allgemeine Dokumenten-API unterstützt die meisten Formulartypen, analysiert Ihre Dokumente und ordnet Werte den von ihr gefundenen Schlüsseln und Einträgen in Tabellen zu. Sie eignet sich ideal für die Extraktion gängiger Schlüssel-Werte-Paare aus Dokumenten. Sie können das allgemeine Dokumentenmodell als Alternative zum [Training eines benutzerdefinierten Modells ohne Labels](compose-custom-models.md#train-without-labels) verwenden.

* Das allgemeine Dokumentenmodell ist ein vortrainiertes Modell und kann direkt über die REST-API aufgerufen werden.

* Das allgemeine Dokumentenmodell unterstützt Named Entity Recognition (NER) für verschiedene Entitätskategorien. NER ist die Fähigkeit, verschiedene Entitäten im Text zu identifizieren und sie in vordefinierte Klassen oder Typen zu kategorisieren. Beispiel: Person, Ort, Ereignis, Produkt und Organisation. Die Extraktion von Entitäten kann in Szenarien nützlich sein, in denen Sie die extrahierten Werte validieren möchten. Die Entitäten werden aus dem gesamten Inhalt extrahiert.

***Mit [Formularerkennung Studio](https://formrecognizer.appliedai.azure.com/studio/prebuilt?formType=document) verarbeitetes Beispieldokument***:

:::image type="content" source="media/studio/general-document-analyze.png" alt-text="Screenshot: Allgemeine Dokumentenanalyse in Formularerkennung Studio.":::

> [!div class="nextstepaction"]
> [Mehr erfahren: allgemeines Dokumentenmodell](concept-general-document.md)

### <a name="layout"></a>Layout

:::image type="content" source="media/studio/layout.png" alt-text="Screenshot: Symbol „Layout“ in Studio":::

Die Layout-API analysiert und extrahiert Text, Tabellen und Kopfzeilen, Auswahlmarkierungen und Strukturinformationen aus Formularen und Dokumenten.

***Beispielformular, das mit dem [Beispielbeschriftungstool der Formularerkennung](https://fott-2-1.azurewebsites.net/) verarbeitet wurde – Layoutfeature***:

:::image type="content" source="media/overview-layout.png" alt-text="Screenshot: Analysieren des in Formularerkennung Studio verarbeiteten Beispieldokuments":::

> [!div class="nextstepaction"]
> [Mehr erfahren: Layout-Modell](concept-layout.md)

### <a name="invoice"></a>Rechnung

:::image type="content" source="media/studio/invoice.png" alt-text="Screenshot: Symbol „Rechnung“ in Studio":::

Das Rechnungsmodell analysiert und extrahiert Schlüsselinformationen aus Verkaufsrechnungen. Die API analysiert Rechnungen in verschiedenen Formaten und extrahiert Schlüsselinformationen wie Kundenname, Rechnungsadresse, Fälligkeitsdatum und fälliger Betrag.

***Beispielrechnung, die mit dem [Beispielbeschriftungstool der Formularerkennung](https://fott-2-1.azurewebsites.net/) verarbeitet wurde***:

:::image type="content" source="./media/overview-invoices.jpg" alt-text="Beispielrechnung" lightbox="./media/overview-invoices.jpg":::

> [!div class="nextstepaction"]
> [Mehr erfahren: Rechnungsmodell](concept-invoice.md)

### <a name="receipt"></a>Rechnung

:::image type="content" source="media/studio/receipt.png" alt-text="Screenshot: Symbol „Beleg“ in Studio":::

Das Quittungsmodell analysiert und extrahiert Schlüsselinformationen aus Verkaufsquittungen. Die API analysiert gedruckte und handschriftliche Quittungen und extrahiert Schlüsselinformationen wie Händlername, Händlertelefonnummer, Transaktionsdatum, Steuer und Transaktionssumme. 

***Beispielbeleg verarbeitet mit dem [Beispiel-Beschriftungstool der Azure-Formularerkennung](https://fott-2-1.azurewebsites.net/)***:

:::image type="content" source="./media/overview-receipt.jpg" alt-text="Beispielbeleg" lightbox="./media/overview-receipt.jpg":::

> [!div class="nextstepaction"]
> [Mehr erfahren: Eingangsmodell](concept-receipt.md)

### <a name="id-document"></a>ID-Dokument

:::image type="content" source="media/studio/id-document.png" alt-text="Screenshot: Symbol „Ausweisdokument“ in Studio":::

Das ID-Dokumentmodell analysiert und extrahiert Schlüsselinformationen aus US-Führerscheinen (alle 50 Bundesstaaten und District of Columbia) und biografischen Seiten von internationalen Reisepässen (ohne Visa und andere Reisedokumente). Die API analysiert Ausweisdokumente und extrahiert Schlüsselinformationen wie Vorname, Nachname, Adresse und Geburtsdatum.

***Muster eines US-Führerscheins, der mit dem [Beispielbeschriftungstool der Formularerkennung](https://fott-2-1.azurewebsites.net/) verarbeitet wurde***:

:::image type="content" source="./media/id-example-drivers-license.jpg" alt-text="Beispielausweis" lightbox="./media/overview-id.jpg":::

> [!div class="nextstepaction"]
> [Mehr erfahren: Identitätsdokumentmodell](concept-id-document.md)

### <a name="business-card"></a>Visitenkarte

:::image type="content" source="media/studio/business-card.png" alt-text="Screenshot: Symbol „Visitenkarte“ in Studio":::

Das Visitenkartenmodell analysiert und extrahiert Schlüsselinformationen aus Visitenkartenbildern. Die API analysiert gedruckte Visitenkartenbilder und extrahiert Schlüsselinformationen wie Vorname, Nachname, Firmenname, E-Mail-Adresse und Telefonnummer.

***Beispielvisitenkarte, die mit dem [Beispielbeschriftungstool der Formularerkennung](https://fott-2-1.azurewebsites.net/) verarbeitet wurde***:

:::image type="content" source="./media/overview-business-card.jpg" alt-text="Beispielvisitenkarte" lightbox="./media/overview-business-card.jpg":::

> [!div class="nextstepaction"]
> [Mehr erfahren: Visitenkartenmodell](concept-business-card.md)

### <a name="custom"></a>Benutzerdefiniert

 :::image type="content" source="media/studio/custom.png" alt-text="Screenshot: Symbol „Benutzerdefiniert“ in Studio":::

Das benutzerdefinierte Modell analysiert und extrahiert Daten aus Formularen und Dokumenten, die für Ihr Unternehmen spezifisch sind. Bei der API handelt es sich um ein maschinelles Lernprogramm, das darauf trainiert ist, Formularfelder innerhalb Ihrer unterschiedlichen Inhalte zu erkennen und Schlüssel-Wert-Paare sowie Tabellendaten zu extrahieren. Für den Einstieg benötigen Sie nur fünf Beispiele desselben Formulartyps, und Ihr benutzerdefiniertes Modell kann mit oder ohne bezeichnete Datasets trainiert werden.

***Beispiel für ein benutzerdefiniertes Formular, das mit dem [Beispielbeschriftungstool der Formularerkennung](https://fott-2-1.azurewebsites.net/) verarbeitet wurde***:

:::image type="content" source="media/analyze.png" alt-text="Screenshot: Fenster „analyze-a-custom-form“ (benutzerdefiniertes Formular analysieren) der Formularerkennung":::

> [!div class="nextstepaction"]
> [Mehr erfahren: Benutzerdefiniertes Modell](concept-custom.md)

## <a name="data-extraction"></a>Extrahieren von Daten

 | **Modell**   | **Textextraktion** |**Schlüssel-Werte-Paare** |**Fields**|**Auswahlmarkierungen**   | **Tabellen**   |**Entitäten** |
  | --- | :---: |:---:| :---: | :---: |:---: |:---: |
  |🆕Allgemeines Dokument  | ✓  |  ✓ || ✓  | ✓  | ✓  |
  | Layout  | ✓  |   || ✓  | ✓  |   |
  | Rechnung  | ✓ | ✓  |✓| ✓  | ✓ ||
  |Rechnung  | ✓  |   ✓ |✓|   |  ||
  | ID-Dokument | ✓  |   ✓  |✓|   |   ||
  | Visitenkarte    | ✓  |   ✓ | ✓|  |   ||
  | Benutzerdefiniert             |✓  |  ✓ || ✓  | ✓  | ✓  |

## <a name="input-requirements"></a>Eingabeanforderungen

* Die besten Ergebnisse erzielen Sie, wenn Sie pro Dokument ein deutliches Foto oder einen hochwertigen Scan bereitstellen.
* Unterstützte Dateiformate: JPEG, PNG, BMP, TIFF und PDF (in Text eingebettet oder gescannt). In Text eingebettete PDF-Dateien sind am besten geeignet, um die Möglichkeit von Fehlern beim Extrahieren und Auffinden von Zeichen auszuschließen.
* In den Formaten PDF und TIFF können bis zu 2.000 Seiten verarbeitet werden (bei einem kostenlosen Abonnement werden nur die ersten beiden Seiten verarbeitet).
* Die Dateigröße muss unter 50 MB liegen.
* Bei Bildern müssen die Abmessungen zwischen 50 × 50 Pixel und 10.000 × 10.000 Pixel liegen.
* Die PDF-Abmessungen sind bis zu 17 × 17 Zoll, sodass die Papierformate Legal oder A3 hineinpassen, oder kleiner.
* Der Gesamtumfang der Trainingsdaten beträgt 500 Seiten oder weniger.
* Wenn Ihre PDFs kennwortgeschützt sind, müssen Sie die Sperre vor dem Senden entfernen.
* Für unbeaufsichtigtes Lernen (ohne beschriftete Daten) gilt Folgendes:
  * Die Daten müssen Schlüssel und Werte enthalten.
  * Die Schlüssel müssen über oder links von den Werten stehen; sie dürfen nicht unter oder rechts von ihnen stehen.

> [!NOTE]
> Das [Beispielbeschriftungstool](https://fott-2-1.azurewebsites.net/) unterstützt das BMP-Dateiformat nicht. Dies ist eine Einschränkung des Tools, nicht des Formularerkennungsdienstes.

## <a name="form-recognizer-preview-v30"></a>Formularerkennung Vorschau v3.0

  Formularerkennung v3.0 (Vorschau) bietet eine Reihe neuer Funktionen und Möglichkeiten:

* [**Allgemeines Dokument (Vorschau)**](concept-general-document.md)Modell ist eine neue API, die ein vorab trainiertes Modell verwendet, um Text, Tabellen, Strukturen, Schlüssel-Wert-Paare und benannte Entitäten aus Formularen und Dokumenten zu extrahieren.
* Das Modell [**Receipt (preview)**](concept-receipt.md) unterstützt die Verarbeitung von einseitigen Hotelbelegen.
* [**ID-Dokument (Vorschau)**](concept-id-document.md) Modell unterstützt Vermerke, Einschränkungen und Fahrzeugklassifizierungen aus US-Führerscheinen.
* [**Custom Model API (Vorschau)**](concept-custom.md) unterstützt die Erkennung von Unterschriften für benutzerdefinierte Formulare.

### <a name="version-migration"></a>Versions-Migration

Erfahren Sie, wie Sie Formularerkennung v3.0 in Ihren Anwendungen verwenden können, indem Sie unserem [**Formularerkennung v3.0 Migrationsleitfaden**](v3-migration-guide.md) folgen

## <a name="next-steps"></a>Nächste Schritte

* [Lernen Sie, wie Sie Ihre eigenen Formulare und Dokumente verarbeiten können](quickstarts/try-sample-label-tool.md) mit unserem [Beispieltool der Formularerkennung](https://fott-2-1.azurewebsites.net/)

* Führen Sie einen [Schnellstart für die Formularerkennung](quickstarts/try-sdk-rest-api.md) durch und beginnen Sie mit der Erstellung einer Anwendung zur Formularverarbeitung in der Entwicklungssprache Ihrer Wahl.
