---
title: Belegmodell der Formularerkennung
titleSuffix: Azure Applied AI Services
description: Konzepte, die die Datenextraktion und -analyse mithilfe des vordefinierten Belegmodells umfassen
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: lajanuar
recommendations: false
ms.custom: ignite-fall-2021
ms.openlocfilehash: 111dcc2ab07c83e164e054395b0804f46c07d748
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131027064"
---
<!-- markdownlint-disable MD033 -->

# <a name="form-recognizer-receipt-model"></a>Belegmodell der Formularerkennung

Das Belegmodell kombiniert leistungsstarke OCR-Funktionen (Optical Character Recognition, optische Zeichenerkennung) mit Deep Learning-Modellen, um wichtige Informationen aus Kaufbelegen zu analysieren und zu extrahieren. Belege können verschiedene Formate und unterschiedliche Qualität aufweisen, einschließlich gedruckter und handschriftlicher Belege. Die API extrahiert Schlüsselinformationen wie Händlername, Händlertelefonnummer, Transaktionsdatum, Steuer und Transaktionssumme und gibt eine strukturierte JSON-Datendarstellung zurück.

***Beispielbeleg verarbeitet mit dem [Beispiel-Beschriftungstool der Azure-Formularerkennung](https://fott-2-1.azurewebsites.net/)***:

:::image type="content" source="media/studio/overview-receipt.png" alt-text="Beispielbeleg" lightbox="media/overview-receipt.jpg":::

## <a name="development-options"></a>Entwicklungsoptionen

Die folgenden Ressourcen werden von der Azure-Formularerkennung v2.1 unterstützt:

| Funktion | Ressourcen |
|----------|-------------------------|
|**Belegmodell**| <ul><li>[**Formularerkennungstool für die Bezeichnung**](https://fott-2-1.azurewebsites.net/prebuilts-analyze)</li><li>[**REST-API**](quickstarts/try-sdk-rest-api.md?pivots=programming-language-rest-api#analyze-receipts)</li><li>[**Clientbibliothek/SDK**](quickstarts/try-sdk-rest-api.md)</li><li>[**Docker-Container für Formularerkennung**](containers/form-recognizer-container-install-run.md?tabs=receipt#run-the-container-with-the-docker-compose-up-command)</li></ul>|

Die folgenden Ressourcen werden von der Azure-Formularerkennung v3.0 unterstützt:

| Funktion | Ressourcen | Modell-ID |
|----------|-------------|-----------|
|**Belegmodell**| <ul><li>[**Formularerkennung Studio**](https://formrecognizer.appliedai.azure.com)</li><li>[**REST-API**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument)</li><li>[**C# SDK**](quickstarts/try-v3-csharp-sdk.md)</li><li>[**Python SDK**](quickstarts/try-v3-python-sdk.md)</li></ul>|**prebuilt-receipt**|

### <a name="try-form-recognizer"></a>Formularerkennung ausprobieren

Hier erfahren Sie, wie die Daten, einschließlich Transaktionszeit und -datum sowie Händlerinformationen und Beträge, mithilfe von Azure-Formularerkennung Studio oder unserem Beispiel-Beschriftungstool aus den Belegen extrahiert werden. Sie benötigen Folgendes:

* Azure-Abonnement – Sie können ein [kostenloses Abonnement erstellen](https://azure.microsoft.com/free/cognitive-services/).

* Eine [Instanz der Formularerkennung](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) im Azure-Portal. Sie können den kostenlosen Tarif (`F0`) verwenden, um den Dienst auszuprobieren. Wählen Sie nach der Bereitstellung Ihrer Ressource **Zu Ressource wechseln** aus, um Ihren API-Schlüssel und -Endpunkt abzurufen.

 :::image type="content" source="media/containers/keys-and-endpoint.png" alt-text="Screenshot: Schlüssel und Endpunktspeicherort im Azure-Portal":::

#### <a name="form-recognizer-studio-preview"></a>Formularerkennung Studio (Vorschau)

> [!NOTE]
> Formularerkennung Studio ist mit der Vorschau-API (v3.0) verfügbar.

1. Wählen Sie auf der Startseite von Azure-Formularerkennung Studio die Option **Belege** aus.

1. Sie können entweder den Beispielbeleg analysieren oder die Schaltfläche **+ Hinzufügen** auswählen, um Ihr eigenes Beispiel hochzuladen.

1. Wählen Sie die Schaltfläche **Analysieren** aus:

    :::image type="content" source="media/studio/receipt-analyze.png" alt-text="Screenshot des Menüs „Beleg analysieren“":::

    > [!div class="nextstepaction"]
    > [Ausprobieren von Formularerkennung Studio](https://formrecognizer.appliedai.azure.com/studio/prebuilt?formType=receipt)

#### <a name="sample-labeling-tool"></a>Beispiel-Beschriftungstool

Sie benötigen ein Belegdokument. Sie können unser [Beispielbelegdokument](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/contoso-receipt.png) verwenden.

1. Wählen Sie auf der Startseite des Beispiel-Beschriftungstools die Option **Vordefiniertes Modell zum Abrufen von Daten verwenden** aus.

1. Wählen Sie aus dem Dropdownmenü **Formulartyp** die Option **Beleg** aus:

      :::image type="content" source="media/try-receipt.png" alt-text="Screenshot: Dropdownmenü des Beispiel-Beschriftungstools für die Auswahl vordefinierter Modelle":::

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

## <a name="supported-languages-and-locales-v21"></a>Unterstützte Sprachen und Gebietsschemas in Version 2.1

>[!NOTE]
 > Es ist nicht erforderlich, ein Gebietsschema anzugeben. Dies ist ein optionaler Parameter. Die Deep Learning-Technologie der Formularerkennung erkennt automatisch die Sprache des Texts in Ihrem Bild.

| Modell | Sprache – Gebietsschemacode | Standard |
|--------|:----------------------|:---------|
|Rechnung| <ul><li>Englisch (USA) – en-US</li><li> Englisch (Australien) – en-AU</li><li>Englisch (Kanada) – en-CA</li><li>Englisch (Vereinigtes Königreich) – en-GB</li><li>Englisch (Indien) – en-IN</li></ul>  | Automatisch erkannt |

## <a name="field-extraction"></a>Feldextraktion

|Name| type | BESCHREIBUNG | Standardisierte Ausgabe |
|:-----|:----|:----|:----|
| ReceiptType | String | Der Typ des Belegs |  Aufgeschlüsselt |
| MerchantName | String | Der Name des Händlers, der den Beleg ausstellt |  |
| MerchantPhoneNumber | phoneNumber | Die aufgeführte Telefonnummer des Händlers | +1 xxx xxx xxxx |
| MerchantAddress | String | Die aufgeführte Adresse des Händlers |   |
| TransactionDate | Datum | Das Datum der Ausstellung des Belegs | yyyy-mm-dd |
| TransactionTime | Time | Die Uhrzeit der Ausstellung des Belegs | hh-mm-ss (24 Stunden)  |
| Gesamt | Betrag (USD)| Die Gesamttransaktion des Belegs | Gleitkommazahl mit zwei Dezimalstellen|
| Subtotal (Zwischensumme) | Betrag (USD) | Die Zwischensumme des Belegs, oft vor Steuern | Gleitkommazahl mit zwei Dezimalstellen|
| Tax (Steuern) | Betrag (USD) | Die Steuern auf dem Beleg (oft Mehrwertsteuer oder Äquivalent) | Gleitkommazahl mit zwei Dezimalstellen |
| Tipp | Betrag (USD) | Vom Käufer gegebenes Trinkgeld | Gleitkommazahl mit zwei Dezimalstellen|
| Elemente | Array von Objekten | Die extrahierten Positionen mit extrahierten Werten für Name, Menge, Stückpreis und Gesamtpreis | |
| Name | String | Name des Elements | |
| Menge | Number | Menge der einzelnen Positionen | Integer |
| Preis | Number | Einzelpreis der einzelnen Positionen| Gleitkommazahl mit zwei Dezimalstellen |
| Gesamtpreis | Number | Gesamtpreis der Position | Gleitkommazahl mit zwei Dezimalstellen |

## <a name="form-recognizer-preview-v30"></a>Formularerkennung Vorschau v3.0

 Die Vorschauversion der Formularerkennung bietet eine Reihe neuer Features und Möglichkeiten. Das Modell **Beleg** unterstützt die Verarbeitung von einseitigen Hotelbelegen.

### <a name="hotel-receipt-field-extraction"></a>Feldextraktion bei Hotelbelegen

|Name| type | BESCHREIBUNG | Standardisierte Ausgabe |
|:-----|:----|:----|:----|
| ArrivalDate | Datum | Ankunftsdatum | yyyy-mm-dd |
| Währung | Währung | Währungseinheit der Belegbeträge. Beispiel: USD, EUR oder MIXED, wenn mehrere Werte gefunden werden ||
| DepartureDate | Datum | Abreisedatum | yyyy-mm-dd |
| Items | Array | | |
| Items.*.Category | String | Art der Position, z. B. Raum, Steuer usw. |  |
| Items.*.Date | Datum | Datum der Position | yyyy-mm-dd |
| Items.*.Description | String | Beschreibung der Position | |
| Items.*.TotalPrice |  Number | Gesamtpreis der Position | Integer |
| Gebietsschema | String | Gebietsschema des Belegs, z. B. en-US. | ISO-Code im Format „Sprache-Land“   |
| MerchantAddress | String | Die aufgeführte Adresse des Händlers | |
| MerchantAliases | Array| | |
| MerchantAliases.* | String | Alternativer Name des Händlers |  |
| MerchantName | String | Der Name des Händlers, der den Beleg ausstellt | |
| MerchantPhoneNumber | phoneNumber | Die aufgeführte Telefonnummer des Händlers | +1 xxx xxx xxxx|
| ReceiptType | String | Art des Belegs, z. B. Hotel, aufgeschlüsselt | |
| Gesamt | Number | Die Gesamttransaktion des Belegs | Gleitkommazahl mit zwei Dezimalstellen |

### <a name="hotel-receipt-supported-languages-and-locales"></a>Unterstützte Sprachen und Gebietsschemas für Hotelbelege

| Modell | Sprache – Gebietsschemacode | Standard |
|--------|:----------------------|:---------|
|Beleg (Hotel) | <ul><li>Englisch (USA) – en-US</li></ul>| Englisch (USA) – en-US|

### <a name="migration-guide-and-rest-api-v30"></a>Migrationsleitfaden und REST-API v3.0

* Erfahren Sie, wie Sie die Vorschauversion in Ihren Anwendungen und Workflows verwenden können, indem Sie unseren [**Formularerkennung v3.0-Migrationsleitfaden**](v3-migration-guide.md) befolgen.

* Erkunden Sie die [**REST-API (Vorschau)**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument), um mehr über die Vorschauversion und neue Funktionen zu erfahren.

## <a name="next-steps"></a>Nächste Schritte

* Sehen Sie sich eine Schnellstartanleitung zur Formularerkennung an:

  > [!div class="nextstepaction"]
  > [Formularerkennung: Schnellstart](quickstarts/try-sdk-rest-api.md)

* Erkunden Sie unsere REST-API:

    > [!div class="nextstepaction"]
    > [Formularerkennung-API Version 2.1](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeReceiptAsync)
