---
title: ID-Dokumentmodell für die Formularerkennung
titleSuffix: Azure Applied AI Services
description: Konzepte, die Datenextraktion und -analyse mithilfe des vordefinierten ID-Dokumentmodells umfassen
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: lajanuar
recommendations: false
ms.custom: ignite-fall-2021
ms.openlocfilehash: d598c3af52f8b62b23b49f9d661a79a2979574a0
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131027296"
---
<!-- markdownlint-disable MD033 -->

# <a name="form-recognizer-id-document-model"></a>ID-Dokumentmodell für die Formularerkennung

Das ID-Dokumentmodell kombiniert leistungsstarke OCR-Funktionen (Optical Character Recognition, optische Zeichenerkennung) mit Deep Learning-Modellen, um wichtige Informationen aus US-amerikanischen Führerscheinen (alle 50 US-Bundesstaaten sowie District of Columbia) und internationalen Reisepässen (mit Ausnahme von Visa und anderen Reisedokumenten) zu analysieren und zu extrahieren. Die API analysiert Identitätsdokumente, extrahiert wichtige Informationen wie Vorname, Nachname, Anschrift und Geburtsdatum und gibt eine strukturierte JSON-Datendarstellung zurück.

***Muster eines US-Führerscheins, der mit Formularerkennung Studio verarbeitet wurde***

:::image type="content" source="media/studio/drivers-license.png" alt-text="Beispielausweis" lightbox="media/overview-id.jpg":::

## <a name="development-options"></a>Entwicklungsoptionen

Die folgenden Ressourcen werden von der Azure-Formularerkennung v2.1 unterstützt:

| Funktion | Ressourcen |
|----------|-------------------------|
|**Ausweisdokumentmodell**| <ul><li>[**Formularerkennungstool für die Bezeichnung**](https://fott-2-1.azurewebsites.net/prebuilts-analyze)</li><li>[**REST-API**](quickstarts/try-sdk-rest-api.md?pivots=programming-language-rest-api#analyze-identity-id-documents)</li><li>[**Clientbibliothek/SDK**](quickstarts/try-sdk-rest-api.md)</li><li>[**Docker-Container für Formularerkennung**](containers/form-recognizer-container-install-run.md?tabs=id-document#run-the-container-with-the-docker-compose-up-command)</li></ul>|

Die folgenden Ressourcen werden von der Azure-Formularerkennung v3.0 unterstützt:

| Funktion | Ressourcen | Modell-ID |
|----------|-------------|-----------|
|**Ausweisdokumentmodell**|<ul><li> [**Formularerkennung Studio**](https://formrecognizer.appliedai.azure.com)</li><li>[**REST-API**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument)</li><li>[**C# SDK**](quickstarts/try-v3-csharp-sdk.md)</li><li>[**Python SDK**](quickstarts/try-v3-python-sdk.md)</li><li>[**Java SDK**](quickstarts/try-v3-java-sdk.md)</li><li>[**JavaScript SDK**](quickstarts/try-v3-javascript-sdk.md)</li></ul>|**prebuilt-idDocument**|

### <a name="try-form-recognizer"></a>Formularerkennung ausprobieren

Erfahren Sie, wie Daten, einschließlich Name, Geburtsdatum, maschinenlesbarer Bereich und Ablaufdatum, aus Ausweisdokumenten mithilfe von Formularerkennung Studio oder unserem Beispielbeschriftungstool extrahiert werden. Sie benötigen Folgendes:

* Azure-Abonnement – Sie können ein [kostenloses Abonnement erstellen](https://azure.microsoft.com/free/cognitive-services/)

* Eine [Formularerkennungsinstanz](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) im Azure-Portal Sie können den kostenlosen Tarif (`F0`) verwenden, um den Dienst auszuprobieren. Wählen Sie nach der Bereitstellung Ihrer Ressource **Zu Ressource wechseln** aus, um Ihren API-Schlüssel und -Endpunkt abzurufen.

 :::image type="content" source="media/containers/keys-and-endpoint.png" alt-text="Screenshot: Schlüssel und Endpunktspeicherort im Azure-Portal":::

#### <a name="form-recognizer-studio-preview"></a>Formularerkennung Studio (Vorschau)

> [!NOTE]
> Formularerkennung Studio ist mit der Vorschau-API (v3.0) verfügbar.

1. Wählen Sie auf der Startseite von Azure-Formularerkennung Studio die Option **Rechnungen** aus.

1. Sie können entweder die Beispielrechnung analysieren oder die Schaltfläche **+ Hinzufügen** auswählen, um Ihr eigenes Beispiel hochzuladen.

1. Wählen Sie die Schaltfläche **Analysieren** aus:

    :::image type="content" source="media/studio/id-document-analyze.png" alt-text="Screenshot: Menü „Ausweisdokument analysieren“":::

    > [!div class="nextstepaction"]
    > [Ausprobieren von Formularerkennung Studio](https://formrecognizer.appliedai.azure.com/studio/prebuilt?formType=idDocument)

#### <a name="sample-labeling-tool"></a>Beispiel-Beschriftungstool

Sie benötigen ein Ausweisdokument. Sie können unser [ID-Beispieldokument](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/DriverLicense.png) verwenden.

1. Wählen Sie auf der Startseite des Beispiel-Beschriftungstools die Option **Vordefiniertes Modell zum Abrufen von Daten verwenden** aus.

1. Wählen Sie die Option **Identity documents** (Ausweisdokumente) aus dem Dropdownmenü **Formulartyp** aus:

    :::image type="content" source="media/try-id-document.png" alt-text="Screenshot: Dropdownmenü des Beispiel-Beschriftungstools für die Auswahl vordefinierter Modelle":::

   > [!div class="nextstepaction"]
   > [Ausprobieren des Beispiel-Beschriftungstools](https://fott-2-1.azurewebsites.net/prebuilts-analyze)

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
> Das [Beispielbeschriftungstool](https://fott-2-1.azurewebsites.net/) unterstützt das BMP-Dateiformat nicht. Dies ist eine Einschränkung des Tools, nicht des Formularerkennungsdiensts.

## <a name="supported-languages-and-locales-v21"></a>Unterstützte Sprachen und Gebietsschemas in Version 2.1

| Modell | Sprache – Gebietsschemacode | Standard |
|--------|:----------------------|:---------|
|ID-Dokument| <ul><li>Englisch (USA) – en-US (Führerschein)</li><li>Seiten mit persönlichen Daten aus internationalen Reisepässen</br> (mit Ausnahme von Visa und anderen Reisedokumenten)</li></ul></br>|Englisch (USA) – en-US|

## <a name="field-extraction"></a>Feldextraktion

|Name| type | BESCHREIBUNG | Standardisierte Ausgabe|
|:-----|:----|:----|:----|
|  CountryRegion | countryRegion | Länder- oder Regionscode, konform zu ISO 3166-Standard |  |
|  DateOfBirth | Date | DOB | yyyy-mm-dd |
|  DateOfExpiration | Date | Ablaufdatum DOB | yyyy-mm-dd |
|  DocumentNumber | String | Relevante Passnummer, Führerscheinnummer usw. |  |
|  FirstName | String | Extrahierter Vorname und ggf. Mittelinitial |  |
|  LastName | String | Extrahierter Nachname |  |
|  Nationality | countryRegion | Länder- oder Regionscode, konform zur ISO 3166-Norm (nur Reisepass) |  |
|  Geschlecht | String | Mögliche extrahierte Werte: „M“, „F“ und „X“ | |
|  MachineReadableZone | Object | Maschinenlesbarer zweizeiliger Bereich (Machine Readable Zone, MRZ) mit jeweils 44 Zeichen | „P<USABROOKS<<JENNIFER<<<<<<<<<<<<<<<<<<<<<<< 3400200135USA8001014F1905054710000307<715816“ |
|  DocumentType | String | Dokumenttyp, z. B. Pass oder Führerschein | „passport“ |
|  Adresse | Zeichenfolge | Extrahierte Adresse (nur beim Führerschein) ||
|  Region | String | Extrahierte Region, Bundesstaat, Provinz usw. (nur Führerschein) |  |

## <a name="form-recognizer-preview-v30"></a>Formularerkennung: Vorschau v3.0

 Die Vorschauversion der Formularerkennung bietet eine Reihe neuer Features und Funktionen:

* Das Modell **ID-Dokument (v3.0)** unterstützt das Extrahieren von Vermerken, Einschränkungen und Fahrzeugklassifizierungen aus US-amerikanischen Führerscheinen.

### <a name="id-document-preview-field-extraction"></a>Ausweisdokumentenvorschau: Feldextraktion

|Name| type | BESCHREIBUNG | Standardisierte Ausgabe|
|:-----|:----|:----|:----|
| 🆕 Endorsements | String | Zusätzliche Fahrberechtigungen, z. B. Genehmigung zum Führen eines Motorrads oder Schulbusses.  | |
| 🆕 Restrictions | String | Eingeschränkte Fahrberechtigungen, z. B. gesperrte oder widerrufene Lizenzen.| |
| 🆕VehicleClassification | String | Fahrzeugklassen, für die eine Fahrberechtigung erteilt wurde. ||
|  CountryRegion | countryRegion | Länder- oder Regionscode, konform zu ISO 3166-Standard |  |
|  DateOfBirth | Date | DOB | yyyy-mm-dd |
|  DateOfExpiration | Date | Ablaufdatum DOB | yyyy-mm-dd |
|  DocumentNumber | String | Relevante Passnummer, Führerscheinnummer usw. |  |
|  FirstName | String | Extrahierter Vorname und ggf. Mittelinitial |  |
|  LastName | String | Extrahierter Nachname |  |
|  Nationality | countryRegion | Länder- oder Regionscode, konform zur ISO 3166-Norm (nur Reisepass) |  |
|  Geschlecht | String | Mögliche extrahierte Werte: „M“, „F“ und „X“ | |
|  MachineReadableZone | Object | Maschinenlesbarer zweizeiliger Bereich (Machine Readable Zone, MRZ) mit jeweils 44 Zeichen | „P<USABROOKS<<JENNIFER<<<<<<<<<<<<<<<<<<<<<<< 3400200135USA8001014F1905054710000307<715816“ |
|  DocumentType | String | Dokumenttyp, z. B. Pass oder Führerschein | „passport“ |
|  Adresse | Zeichenfolge | Extrahierte Adresse (nur beim Führerschein) ||
|  Region | String | Extrahierte Region, Bundesstaat, Provinz usw. (nur Führerschein) |  |

### <a name="migration-guide-and-rest-api-v30"></a>Migrationsleitfaden und REST-API v3.0

* Erfahren Sie, wie Sie die Vorschauversion in Ihren Anwendungen und Workflows verwenden können, indem Sie unseren [**Formularerkennung v3.0-Migrationsleitfaden**](v3-migration-guide.md) befolgen.

* Erkunden Sie die [**REST-API (Vorschau)**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument), um mehr über die Vorschauversion und neue Funktionen zu erfahren.

## <a name="next-steps"></a>Nächste Schritte

* Sehen Sie sich eine Schnellstartanleitung zur Formularerkennung an:

  > [!div class="nextstepaction"]
  > [Formularerkennung: Schnellstart](quickstarts/try-sdk-rest-api.md)

* Erkunden Sie unsere REST-API:

    > [!div class="nextstepaction"]
    > [Formularerkennung-API Version 2.1](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/5f74a7738978e467c5fb8707)
