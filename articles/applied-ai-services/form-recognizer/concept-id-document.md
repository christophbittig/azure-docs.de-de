---
title: ID-Dokumentmodell für die Formularerkennung
titleSuffix: Azure Applied AI Services
description: Konzepte, die Datenextraktion und -analyse mithilfe des vordefinierten ID-Dokumentmodells umfassen
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 10/07/2021
ms.author: lajanuar
recommendations: false
ms.openlocfilehash: 98364df02238c36f7e15f00eaff6ec124631987c
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/19/2021
ms.locfileid: "130178237"
---
<!-- markdownlint-disable MD033 -->

# <a name="form-recognizer-id-document-model"></a>ID-Dokumentmodell für die Formularerkennung

Das ID-Dokumentmodell kombiniert leistungsstarke OCR-Funktionen (Optical Character Recognition, optische Zeichenerkennung) mit Deep Learning-Modellen, um wichtige Informationen aus US-amerikanischen Führerscheinen (alle 50 US-Bundesstaaten sowie District of Columbia) und internationalen Reisepässen (mit Ausnahme von Visa und anderen Reisedokumenten) zu analysieren und zu extrahieren. Die API analysiert Identitätsdokumente, extrahiert wichtige Informationen wie Vorname, Nachname, Anschrift und Geburtsdatum und gibt eine strukturierte JSON-Datendarstellung zurück.

***Muster eines US-Führerscheins, der mit dem [Beschriftungsprogramm der Formularerkennung](https://fott-2-1.azurewebsites.net/) bearbeitet wurde:***

:::image type="content" source="./media/id-example-drivers-license.jpg" alt-text="Beispielausweis" lightbox="./media/overview-id.jpg":::

## <a name="try-form-recognizer-studio-preview"></a>Testen der Studioversion der Formularerkennung (Vorschau)

* Die Studioversion der Formularerkennung ist mit der Vorschau-API (v3.0) verfügbar.

* Extrahieren Sie den Namen, die maschinenlesbare Zone und das Ablaufdatum mit dem ID-Dokumentfeature der Formularerkennung in der Studioversion:

> [!div class="nextstepaction"]
> [Studioversion der Formularerkennung testen](https://formrecognizer.appliedai.azure.com/studio/prebuilt?formType=idDocument)

## <a name="try-it-sample-labeling-tool"></a>Jetzt testen: Tool für die Beschriftung von Beispielen

Indem Sie unser Tool für die Beschriftung von Beispielen ausprobieren, können Sie selbst sehen, wie ID-Dokumentdaten extrahiert werden. Sie benötigen Folgendes:

* Azure-Abonnement – Sie können ein [kostenloses Abonnement erstellen](https://azure.microsoft.com/free/cognitive-services/).

* Eine [Instanz der Formularerkennung](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) im Azure-Portal. Sie können den kostenlosen Tarif (`F0`) verwenden, um den Dienst auszuprobieren. Klicken Sie nach der Bereitstellung Ihrer Ressource auf **Zu Ressource wechseln**, um Ihren API-Schlüssel und -Endpunkt abzurufen.

 :::image type="content" source="media/containers/keys-and-endpoint.png" alt-text="Screenshot: Schlüssel und Endpunktspeicherort im Azure-Portal.":::

* Ein ID-Dokument. Sie können unser [ID-Beispieldokument](https://raw.githubusercontent.com/MicrosoftDocs/azure-docs/master/articles/applied-ai-services/form-recognizer/media/id-license.jpg) verwenden.

> [!div class="nextstepaction"]
  > [Testen](https://fott-2-1.azurewebsites.net/prebuilts-analyze)

Gehen Sie auf der Benutzeroberfläche der Formularerkennung folgendermaßen vor:

  1. Wählen Sie **Vordefiniertes Modell zum Abrufen von Daten verwenden** aus.
  1. Wählen Sie aus dem Dropdownmenü **Formulartyp** die Option **Beleg** aus:

  :::image type="content" source="media/try-id-document.png" alt-text="Screenshot: Dropdownmenü des Tools für die Beschriftung von Beispielen zur Auswahl vordefinierter Modelle.":::

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
  * Die Schlüssel müssen über oder links von den Werten stehen; sie dürfen nicht darunter oder rechts davon stehen.

> [!NOTE]
> Das [Tool für die Beschriftung von Beispielen](https://fott-2-1.azurewebsites.net/) unterstützt das BMP-Dateiformat nicht. Dies ist eine Einschränkung des Tools, nicht des Formularerkennungsdiensts.

## <a name="supported-languages-and-locales-v21"></a>Unterstützte Sprachen und Gebietsschemas in Version 2.1

| Modell | Sprache – Gebietsschemacode | Standard |
|--------|:----------------------|:---------|
|ID-Dokument| <ul><li>Englisch (USA) – en-US (Führerschein)</li><li>Seiten mit persönlichen Daten aus internationalen Reisepässen</br> (mit Ausnahme von Visa und anderen Reisedokumenten)</li></ul></br>|Englisch (USA) – en-US|

## <a name="key-value-pair-extraction"></a>Schlüssel-Wert-Paar-Extraktion

|Name| type | BESCHREIBUNG | Standardisierte Ausgabe|
|:-----|:----|:----|:----|
|  CountryRegion | countryRegion | Länder- oder Regionscode, konform zu ISO 3166-Standard |  |
|  DateOfBirth | date | DOB | yyyy-mm-dd |
|  DateOfExpiration | date | Ablaufdatum DOB | yyyy-mm-dd |
|  DocumentNumber | Zeichenfolge | Relevante Passnummer, Führerscheinnummer usw. |  |
|  FirstName | Zeichenfolge | Extrahierter Vorname und ggf. Mittelinitial |  |
|  LastName | Zeichenfolge | Extrahierter Nachname |  |
|  Nationality | countryRegion | Länder- oder Regionscode, konform zur ISO 3166-Norm (nur Reisepass) |  |
|  Geschlecht | Zeichenfolge | Mögliche extrahierte Werte: „M“, „F“ und „X“ | |
|  MachineReadableZone | Objekt | Maschinenlesbarer zweizeiliger Bereich (Machine Readable Zone, MRZ) mit jeweils 44 Zeichen | „P<USABROOKS<<JENNIFER<<<<<<<<<<<<<<<<<<<<<<< 3400200135USA8001014F1905054710000307<715816“ |
|  DocumentType | Zeichenfolge | Dokumenttyp, z. B. Pass oder Führerschein | „passport“ |
|  Adresse | Zeichenfolge | Extrahierte Adresse (nur beim Führerschein) ||
|  Region | Zeichenfolge | Extrahierte Region, Bundesstaat, Provinz usw. (nur Führerschein) |  |

## <a name="form-recognizer-preview-v30"></a>Formularerkennung: Vorschau v3.0

 Die Vorschauversion der Formularerkennung bietet eine Reihe neuer Features und Funktionen:

* Das Modell **ID-Dokument (v3.0)** unterstützt das Extrahieren von Vermerken, Einschränkungen und Fahrzeugklassifizierungen aus US-amerikanischen Führerscheinen.

    ### <a name="id-document-preview-key-value-pair-extraction"></a>Extrahieren von Schlüssel-Wert-Paaren in der Vorschau des ID-Dokuments

    |Name| type | BESCHREIBUNG | Standardisierte Ausgabe|
    |:-----|:----|:----|:----|
    | 🆕 Endorsements | Zeichenfolge | Zusätzliche Fahrberechtigungen, z. B. Genehmigung zum Führen eines Motorrads oder Schulbusses.  | |
    | 🆕 Restrictions | Zeichenfolge | Eingeschränkte Fahrberechtigungen, z. B. gesperrte oder widerrufene Lizenzen.| |
    | 🆕VehicleClassification | Zeichenfolge | Fahrzeugklassen, für die eine Fahrberechtigung erteilt wurde. ||
    |  CountryRegion | countryRegion | Länder- oder Regionscode, konform zu ISO 3166-Standard |  |
    |  DateOfBirth | date | DOB | yyyy-mm-dd |
    |  DateOfExpiration | date | Ablaufdatum DOB | yyyy-mm-dd |
    |  DocumentNumber | Zeichenfolge | Relevante Passnummer, Führerscheinnummer usw. |  |
    |  FirstName | Zeichenfolge | Extrahierter Vorname und ggf. Mittelinitial |  |
    |  LastName | Zeichenfolge | Extrahierter Nachname |  |
    |  Nationality | countryRegion | Länder- oder Regionscode, konform zur ISO 3166-Norm (nur Reisepass) |  |
    |  Geschlecht | Zeichenfolge | Mögliche extrahierte Werte: „M“, „F“ und „X“ | |
    |  MachineReadableZone | Objekt | Maschinenlesbarer zweizeiliger Bereich (Machine Readable Zone, MRZ) mit jeweils 44 Zeichen | „P<USABROOKS<<JENNIFER<<<<<<<<<<<<<<<<<<<<<<< 3400200135USA8001014F1905054710000307<715816“ |
    |  DocumentType | Zeichenfolge | Dokumenttyp, z. B. Pass oder Führerschein | „passport“ |
    |  Adresse | Zeichenfolge | Extrahierte Adresse (nur beim Führerschein) ||
    |  Region | Zeichenfolge | Extrahierte Region, Bundesstaat, Provinz usw. (nur Führerschein) |  |

* Im [**Migrationsleitfaden für die Formularerkennung v3.0**](v3-migration-guide.md) erfahren Sie, wie Sie die Vorschauversion in Ihren Anwendungen und Workflows verwenden können.

* Erkunden Sie die [**REST-API (Vorschau)**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument), um mehr über die Vorschauversion und neue Funktionen zu erfahren.

## <a name="next-steps"></a>Nächste Schritte

* Sehen Sie sich eine Schnellstartanleitung zur Formularerkennung an:

  > [!div class="nextstepaction"]
  > [Formularerkennung: Schnellstart](quickstarts/try-sdk-rest-api.md)

* Erkunden Sie unsere REST-API:

    > [!div class="nextstepaction"]
    > [Formularerkennung-API Version 2.1](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/5f74a7738978e467c5fb8707)
