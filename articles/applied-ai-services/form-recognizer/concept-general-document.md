---
title: 'Formularerkennung: Allgemeines Dokumentmodell | Vorschau'
titleSuffix: Azure Applied AI Services
description: Konzepte, die Datenextraktion und -analyse mithilfe eines vordefinierten allgemeinen Dokumentenvorschaumodells umfassen
author: vkurpad
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 10/07/2021
ms.author: lajanuar
recommendations: false
ms.custom: ignite-fall-2021
ms.openlocfilehash: 63d381f96a9781f2f3ab1abfd45d03c968d6dad8
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131027517"
---
<!-- markdownlint-disable MD033 -->

# <a name="form-recognizer-general-document-model-preview"></a>Formularerkennung: Allgemeines Dokumentmodell (Vorschau)

Das allgemeine Dokumentenvorschaumodell kombiniert leistungsstarke OCR-Funktionen (optische Zeichenerkennung) mit Deep Learning-Modellen zur Extraktion von Schlüssel-Wert-Paaren und Entitäten aus Dokumenten. Allgemeines Dokument ist nur mit der Vorschau-API (v3.0) verfügbar.  Weitere Informationen zur Verwendung der Vorschau-API (v3.0) finden Sie in unserem [Migrationsleitfaden](v3-migration-guide.md).

Die allgemeine Dokumenten-API unterstützt die meisten Formulartypen, analysiert Ihre Dokumente und ordnet Werte den von ihr gefundenen Schlüsseln und Einträgen in Tabellen zu. Sie eignet sich ideal für die Extraktion gängiger Schlüssel-Werte-Paare aus Dokumenten. Sie können das allgemeine Dokumentenmodell als Alternative zum [Trainieren eines benutzerdefinierten Modells ohne Bezeichnungen](compose-custom-models.md#train-without-labels) verwenden.

## <a name="general-document-features"></a>Allgemeine Dokumentfeatures

* Es ist nicht erforderlich, ein benutzerdefiniertes Modell zum Extrahieren von Schlüssel-Wert-Paaren zu trainieren.

* Eine einzelne API wird verwendet, um Schlüssel-Wert-Paar, Entitäten, Text, Tabellen und Strukturen aus Dokumenten zu extrahieren.

* Es handelt sich um ein vortrainiertes Modell, das regelmäßig mit neuen Daten trainiert wird, um die Abdeckung und Genauigkeit zu verbessern.

* Das allgemeine Dokumentmodell unterstützt strukturierte, teilweise strukturierte und unstrukturierte Daten.

***Mit Formularerkennung Studio verarbeitetes Beispieldokument***

:::image type="content" source="media/studio/general-document-analyze.png" alt-text="Screenshot: Allgemeine Dokumentenanalyse in Formularerkennung Studio":::

## <a name="development-options"></a>Entwicklungsoptionen

Die folgenden Ressourcen werden von der Azure-Formularerkennung v3.0 unterstützt:

| Funktion | Ressourcen |
|----------|-------------------------|
|🆕 **Allgemeines Dokumentmodell**|<ul ><li>[**Formularerkennung Studio**](https://formrecognizer.appliedai.azure.com)</li><li>[**REST-API**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument)</li><li>[**C# SDK**](quickstarts/try-v3-csharp-sdk.md)</li><li>[**Python SDK**](quickstarts/try-v3-python-sdk.md)</li><li>[**Java SDK**](quickstarts/try-v3-java-sdk.md)</li><li>[**JavaScript SDK**](quickstarts/try-v3-javascript-sdk.md)</li></ul>|

### <a name="try-form-recognizer"></a>Formularerkennung ausprobieren

Erfahren Sie, wie Daten, einschließlich Tabellen, Werte und Entitäten, aus Formularen und Dokumenten mithilfe von Formularerkennung Studio oder unserem Beispielbeschriftungstool extrahiert werden. Sie benötigen Folgendes:

* Azure-Abonnement – Sie können ein [kostenloses Abonnement erstellen](https://azure.microsoft.com/free/cognitive-services/)

* Eine [Formularerkennungsinstanz](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) im Azure-Portal Sie können den kostenlosen Tarif (`F0`) verwenden, um den Dienst auszuprobieren. Wählen Sie nach der Bereitstellung Ihrer Ressource **Zu Ressource wechseln** aus, um Ihren API-Schlüssel und -Endpunkt abzurufen.

 :::image type="content" source="media/containers/keys-and-endpoint.png" alt-text="Screenshot: Schlüssel und Endpunktspeicherort im Azure-Portal":::

#### <a name="form-recognizer-studio-preview"></a>Formularerkennung Studio (Vorschau)

> [!NOTE]
> Formularerkennung Studio und das allgemeine Dokumentmodell sind mit der Vorschau-API (v3.0) verfügbar.

1. Wählen Sie auf der Startseite von Formularerkennung Studio die Option **Allgemeine Dokumente** aus.

1. Sie können entweder das Beispieldokument analysieren oder die Schaltfläche **+ Hinzufügen** auswählen, um Ihr eigenes Beispiel hochzuladen.

1. Wählen Sie die Schaltfläche **Analysieren** aus:

    :::image type="content" source="media/studio/general-document-analyze-1.png" alt-text="Screenshot: Menü „Allgemeines Dokument analysieren“":::

    > [!div class="nextstepaction"]
    > [Ausprobieren von Formularerkennung Studio](https://formrecognizer.appliedai.azure.com/studio/prebuilt?formType=document)

## <a name="key-value-pairs"></a>Schlüssel-Wert-Paare

Schlüssel-Wert-Paare sind bestimmte Bereiche innerhalb des Dokuments, die eine Bezeichnung oder einen Schlüssel und die zugehörige Antwort oder den zugehörigen Wert identifizieren. In einer strukturierten Form kann dies die Bezeichnung und der Wert sein, den der Benutzer für dieses Feld eingegeben hat. In einem unstrukturierten Dokument kann es sich um das Datum handeln, an dem ein Vertrag basierend auf dem Text in einem Absatz ausgeführt wurde.  Das KI-Modell wird trainiert, um identifizierbare Schlüssel und Werte basierend auf einer Vielzahl von Dokumenttypen, Formaten und Strukturen zu extrahieren.

Schlüssel können auch isoliert existieren, wenn das Modell feststellt, dass ein Schlüssel ohne zugehörigen Wert vorhanden ist, oder wenn optionale Felder verarbeitet werden. Beispielsweise kann ein Feld für den zweiten Vornamen in einigen Fällen in einem Formular leer gelassen werden. Schlüssel-Wert-Paare sind immer Textabschnitte, die im Dokument enthalten sind. Wenn Sie Dokumente besitzen, in denen derselbe Wert auf unterschiedliche Weise beschrieben wird, z. B. ein Kunde oder ein Benutzer, ist der zugeordnete Schlüssel basierend auf dem Dokumentinhalt entweder „Kunde“ oder „Benutzer“. 

## <a name="entities"></a>Entitäten

Modelle für die Verarbeitung natürlicher Sprache können Teile der Sprache identifizieren und jedes Token oder Wort klassifizieren. Das Erkennungsmodell für benannte Entitäten kann Entitäten wie Personen, Standorte und Datumsangaben identifizieren, um eine umfassendere Erfahrung zu ermöglichen. Durch das Identifizieren von Entitäten können Sie zwischen Kundentypen unterscheiden, z. B. Einzelperson oder Organisation.
Das Schlüssel-Wert-Paar-Extraktionsmodell und das Modell zur Identifikation von Entitäten werden parallel für das gesamte Dokument und nicht nur für die Werte der extrahierten Schlüssel-Wert-Paare ausgeführt. Dadurch wird sichergestellt, dass komplexe Strukturen, bei denen ein Schlüssel nicht identifiziert werden kann, weiterhin angereichert werden, indem die Entitäten identifiziert werden, auf die verwiesen wird. Sie können weiterhin Schlüssel oder Werte mit Entitäten abgleichen, die auf den Offsets der identifizierten Spannen basieren.

* Das allgemeine Dokumentenmodell ist ein vortrainiertes Modell und kann direkt über die REST-API aufgerufen werden. 

* Das allgemeine Dokumentenmodell unterstützt Named Entity Recognition (NER) für verschiedene Entitätskategorien. NER ist die Fähigkeit, verschiedene Entitäten im Text zu identifizieren und sie in vordefinierte Klassen oder Typen zu kategorisieren. Beispiel: Person, Ort, Ereignis, Produkt und Organisation. Die Extraktion von Entitäten kann in Szenarien nützlich sein, in denen Sie die extrahierten Werte überprüfen möchten. Die Entitäten werden aus dem gesamten Inhalt und nicht nur aus den extrahierten Werten extrahiert.

## <a name="general-document-model-data-extraction"></a>Datenextraktion für das allgemeine Dokumentmodell

| **Modell**   | **Textextraktion** |**Schlüssel-Werte-Paare** |**Auswahlmarkierungen**   | **Tabellen**   |**Entitäten** |
| --- | :---: |:---:| :---: | :---: |:---: |
|Allgemeines Dokument  | ✓  |  ✓ | ✓  | ✓  | ✓  |

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

## <a name="supported-languages-and-locales"></a>Unterstützte Sprachen und Gebietsschemas

| Modell | Sprache – Gebietsschemacode | Standard |
|--------|:----------------------|:---------|
|Allgemeines Dokument| <ul><li>Englisch (USA) – en-US</li></ul>| Englisch (USA) – en-US|

### <a name="named-entity-recognition-ner-categories"></a>NER-Kategorien (Erkennung benannter Entitäten)

| Category | type | BESCHREIBUNG |
|-----------|-------|--------------------|
| Person | String | Der partielle oder vollständige Name einer Person. |
| PersonType | String | Der Auftragstyp oder die Rolle einer Person.  |
| Standort | String | natürliche und von Menschen hergestellte Wahrzeichen, Gebäude, geografische Merkmale und geopolitische Entitäten |
| Organization | String | Firmen, politische Gruppen, Musikgruppen, Sportvereine, Regierungsstellen und öffentliche Organisationen |
| Ereignis | String | Historische Ereignisse, gesellschaftliche Ereignisse und Naturereignisse |
| Produkt | String |Physische Objekte verschiedener Kategorien. |
| Skill | String | Eine Funktion, Kenntnis oder ein Fachwissen |
| Adresse | Zeichenfolge | Vollständige Postanschrift |
| Telefonnummer | String| Telefonnummern | 
| Email | String | E-Mail-Adresse. |
| URL | String | Website-URLs und -Links |
| IP-Adresse | String | Netzwerk-IP-Adressen |
| Datetime | String | Datums- und Uhrzeitangaben |
| Menge | String | numerische Messungen und Einheiten |

## <a name="considerations"></a>Überlegungen

* Die Extraktion von Entitäten kann in Szenarien nützlich sein, in denen Sie die extrahierten Werte überprüfen möchten. Die Entitäten werden für den gesamten Inhalt der Dokumente extrahiert, nicht nur für die extrahierten Werte.

* Schlüssel sind Ausschnitte von Text, der aus dem Dokument extrahiert wurde. Für teilweise strukturierte Dokumente müssen Schlüssel möglicherweise einem vorhandenen Schlüsselwörterbuch zugeordnet werden.

* Erwarten Sie, dass Schlüssel-Wert-Paare mit einem Schlüssel, aber ohne Wert angezeigt werden. Wenn ein Benutzer z. B. keine E-Mail-Adresse in das Formular eingeben möchte.

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie, wie Sie die Vorschauversion in Ihren Anwendungen und Workflows verwenden können, indem Sie unseren [**Formularerkennung v3.0-Migrationsleitfaden**](v3-migration-guide.md) befolgen.

* Erkunden Sie die [**REST-API (Vorschau)**](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument), um mehr über die Vorschauversion und neue Funktionen zu erfahren.

> [!div class="nextstepaction"]
> [Ausprobieren von Formularerkennung Studio](https://formrecognizer.appliedai.azure.com/studio)
