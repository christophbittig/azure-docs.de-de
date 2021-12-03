---
title: 'Formularerkennung: Visitenkartenmodell'
titleSuffix: Azure Applied AI Services
description: Konzepte für die Datenextraktion und -analyse mit dem vordefinierten Visitenkartenmodell
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: lajanuar
recommendations: false
ms.custom: ignite-fall-2021
ms.openlocfilehash: 30b83d0edea0c3b54b0d15300e043c7f86fa6573
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131027733"
---
<!-- markdownlint-disable MD033 -->

# <a name="form-recognizer-business-card-model"></a>Formularerkennung: Visitenkartenmodell

Das Visitenkartenmodell kombiniert leistungsstarke OCR-Funktionen (Optical Character Recognition, optische Zeichenerkennung) mit Deep Learning-Modellen, um wichtige Informationen aus Visitenkartenbildern zu analysieren und zu extrahieren. Die API analysiert gedruckte Visitenkarten, extrahiert Schlüsselinformationen wie Vorname, Nachname, Firmenname, E-Mail-Adresse und Telefonnummer und gibt eine strukturierte JSON-Datendarstellung zurück.

***Beispielvisitenkarte, die mit Formularerkennung Studio verarbeitet wurde***

:::image type="content" source="./media/studio/overview-business-card-studio.png" alt-text="Beispielvisitenkarte" lightbox="./media/overview-business-card.jpg":::

## <a name="development-options"></a>Entwicklungsoptionen

Die folgenden Ressourcen werden von der Azure-Formularerkennung v2.1 unterstützt:

| Funktion | Ressourcen |
|----------|-------------------------|
|**Modell für Visitenkarten**|  <ul><li>[**Formularerkennungstool für die Bezeichnung**](https://fott-2-1.azurewebsites.net/prebuilts-analyze)</li><li>[**REST-API**](quickstarts/try-sdk-rest-api.md?pivots=programming-language-rest-api#analyze-business-cards)</li><li>[**Clientbibliothek/SDK**](quickstarts/try-sdk-rest-api.md)</li><li>[**Docker-Container für Formularerkennung**](containers/form-recognizer-container-install-run.md?tabs=business-card#run-the-container-with-the-docker-compose-up-command)</li></ul>|

Die folgenden Ressourcen werden von der Azure-Formularerkennung v3.0 unterstützt:

| Funktion | Ressourcen | Modell-ID |
|----------|-------------|-----------|
|**Modell für Visitenkarten**| <ul><li>[**Formularerkennung Studio**](https://formrecognizer.appliedai.azure.com)</li><li>[**REST-API**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument)</li><li>[**C# SDK**](quickstarts/try-v3-csharp-sdk.md)</li><li>[**Python SDK**](quickstarts/try-v3-python-sdk.md)</li><li>[**Java SDK**](quickstarts/try-v3-java-sdk.md)</li><li>[**JavaScript SDK**](quickstarts/try-v3-javascript-sdk.md)</li></ul>|**prebuilt-businessCard**|

### <a name="try-form-recognizer"></a>Formularerkennung ausprobieren

Hier erfahren Sie, wie Daten, einschließlich Name, Titel, Adresse, E-Mail-Adresse und Unternehmensname, mithilfe von Formularerkennung Studio oder unserem Beispielbeschriftungstool aus Visitenkarten extrahiert werden. Sie benötigen Folgendes:

* Azure-Abonnement – Sie können ein [kostenloses Abonnement erstellen](https://azure.microsoft.com/free/cognitive-services/)

* Eine [Formularerkennungsinstanz](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) im Azure-Portal Sie können den kostenlosen Tarif (`F0`) verwenden, um den Dienst auszuprobieren. Wählen Sie nach der Bereitstellung Ihrer Ressource **Zu Ressource wechseln** aus, um Ihren API-Schlüssel und -Endpunkt abzurufen.

 :::image type="content" source="media/containers/keys-and-endpoint.png" alt-text="Screenshot: Schlüssel und Endpunktspeicherort im Azure-Portal":::

#### <a name="form-recognizer-studio-preview"></a>Formularerkennung Studio (Vorschau)

> [!NOTE]
> Formularerkennung Studio ist mit der Vorschau-API (v3.0) verfügbar.

1. Wählen Sie auf der Startseite von Azure-Formularerkennung Studio die Option **Visitenkarten** aus.

1. Sie können entweder die Beispielvisitenkarte analysieren oder die Schaltfläche **+ Hinzufügen** auswählen, um Ihr eigenes Beispiel hochzuladen.

1. Wählen Sie die Schaltfläche **Analysieren** aus:

    :::image type="content" source="media/studio/business-card-analyze.png" alt-text="Screenshot: Menü „Visitenkarte analysieren“":::

    > [!div class="nextstepaction"]
    > [Ausprobieren von Formularerkennung Studio](https://formrecognizer.appliedai.azure.com/studio/prebuilt?formType=businessCard)

#### <a name="sample-labeling-tool"></a>Beispiel-Beschriftungstool

Sie benötigen ein Visitenkartendokument. Sie können unser [Beispieldokument für Visitenkarten](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/businessCard.png) verwenden.

  1. Wählen Sie auf der Startseite des Beispiel-Beschriftungstools die Option **Vordefiniertes Modell zum Abrufen von Daten verwenden** aus.

  1. Wählen Sie **Visitenkarte** aus dem Dropdownmenü **Formulartyp** aus:

      :::image type="content" source="media/try-business-card.png" alt-text="Screenshot: Dropdownmenü des Beispiel-Beschriftungstools für die Auswahl vordefinierter Modelle":::

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

## <a name="supported-languages-and-locales"></a>Unterstützte Sprachen und Gebietsschemas

>[!NOTE]
 > Es ist nicht erforderlich, ein Gebietsschema anzugeben. Dies ist ein optionaler Parameter. Die Deep Learning-Technologie der Formularerkennung erkennt automatisch die Sprache des Texts in Ihrem Bild.

| Modell | Sprache – Gebietsschemacode | Standard |
|--------|:----------------------|:---------|
|Visitenkarte| <ul><li>Englisch (USA) – en-US</li><li> Englisch (Australien) – en-AU</li><li>Englisch (Kanada) – en-CA</li><li>Englisch (Vereinigtes Königreich) – en-GB</li><li>Englisch (Indien) – en-IN</li></ul>  | Automatisch erkannt |

## <a name="field-extraction"></a>Feldextraktion

|Name| type | BESCHREIBUNG |Standardisierte Ausgabe |
|:-----|:----|:----|:----:|
| ContactNames | Array von Objekten | Kontaktname |  |
| FirstName | String | Vorname des Kontakts |  |
| LastName | String | Nachname des Kontakts |  |
| CompanyNames | Array von Zeichenfolgen | Name(n) des Unternehmens|  |
| Departments | Array von Zeichenfolgen | Abteilung(en) oder Kontaktorganisation(en) |  |
| JobTitles | Array von Zeichenfolgen | Aufgeführte Position(en) des Kontakts |  |
| E-Mails | Array von Zeichenfolgen | E-Mail-Adresse(n) des Kontakts |  |
| Websites | Array von Zeichenfolgen | Website(s) des Unternehmens |  |
| Adressen | Array von Zeichenfolgen | Aus der Visitenkarte extrahierte Adresse(n) | |
| MobilePhones | Array aus Telefonnummern | Mobiltelefonnummer(n) von der Visitenkarte |+1 xxx xxx xxxx |
| Faxnummern | Array aus Telefonnummern | Faxnummer(n) von der Visitenkarte | +1 xxx xxx xxxx |
| WorkPhones | Array aus Telefonnummern | Telefonnummer(n) (geschäftlich) von der Visitenkarte | +1 xxx xxx xxxx |
| OtherPhones     | Array aus Telefonnummern | Andere Telefonnummer(n) von der Visitenkarte | +1 xxx xxx xxxx |

## <a name="form-recognizer-preview-v30"></a>Formularerkennung Vorschau v3.0

 Die Vorschauversion der Formularerkennung bietet eine Reihe neuer Features und Möglichkeiten.

* Erfahren Sie, wie Sie die Vorschauversion in Ihren Anwendungen und Workflows verwenden können, indem Sie unseren [**Formularerkennung v3.0-Migrationsleitfaden**](v3-migration-guide.md) befolgen.

* Erkunden Sie die [**REST-API (Vorschau)**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument), um mehr über die Vorschauversion und neue Funktionen zu erfahren.

## <a name="next-steps"></a>Nächste Schritte

* Sehen Sie sich eine Schnellstartanleitung zur Formularerkennung an:

  > [!div class="nextstepaction"]
  > [Formularerkennung: Schnellstart](quickstarts/try-sdk-rest-api.md)

* Erkunden Sie unsere REST-API:

    > [!div class="nextstepaction"]
    > [Formularerkennung-API Version 2.1](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeBusinessCardAsync)
