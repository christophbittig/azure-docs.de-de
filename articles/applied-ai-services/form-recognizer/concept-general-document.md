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
ms.openlocfilehash: 49ee6ed3ff8f23cb819a901aba3f370b95901fa9
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/09/2021
ms.locfileid: "129716439"
---
<!-- markdownlint-disable MD033 -->

# <a name="form-recognizer-general-document-model--preview"></a>Formularerkennung: Allgemeines Dokumentmodell | Vorschau

Das allgemeine Dokumentenvorschaumodell kombiniert leistungsstarke OCR-Funktionen (optische Zeichenerkennung) mit Deep Learning-Modellen zur Extraktion von Schlüssel-Wert-Paaren und Entitäten aus Dokumenten. Allgemeines Dokument ist nur mit der Vorschau-API (v3.0) verfügbar.  Weitere Informationen zur Verwendung der Vorschau-API (v3.0) finden Sie in unserem [Migrationsleitfaden](v3-migration-guide.md).

* Die allgemeine Dokumenten-API unterstützt die meisten Formulartypen, analysiert Ihre Dokumente und ordnet Werte den von ihr gefundenen Schlüsseln und Einträgen in Tabellen zu. Sie eignet sich ideal für die Extraktion gängiger Schlüssel-Werte-Paare aus Dokumenten. Sie können das allgemeine Dokumentenmodell als Alternative zum [Trainieren eines benutzerdefinierten Modells ohne Bezeichnungen](compose-custom-models.md#train-without-labels) verwenden.

## <a name="try-form-recognizer-studio-preview"></a>Ausprobieren von Formularerkennung Studio (Vorschau)

* Formularerkennung Studio und das allgemeine Dokumentmodell sind mit der Vorschau-API (v3.0) verfügbar.

* Extrahieren Sie Tabellen, Werte und Entitäten aus Formularen und Dokumenten mit unserem Feature „Allgemeine Dokumente“ aus dem Formularerkennung Studio:

##### <a name="sample-document-processed-in-the-form-recognizer-studio"></a>Mit dem [Formularerkennung Studio](https://formrecognizer.appliedai.azure.com/studio/prebuilt?formType=document) verarbeitetes Beispieldokument:

:::image type="content" source="media/general-document-analyze.png" alt-text="Screenshot: Allgemeine Dokumentenanalyse in Formularerkennung Studio":::

> [!div class="nextstepaction"]
> [Ausprobieren von Formularerkennung Studio](https://formrecognizer.appliedai.azure.com/studio/prebuilt?formType=document)

## <a name="general-document-features"></a>Allgemeine Dokumentfeatures

* Es ist nicht erforderlich, ein benutzerdefiniertes Modell zum Extrahieren von Schlüssel-Wert-Paaren zu trainieren.

* Eine einzelne API wird verwendet, um Schlüssel-Wert-Paar, Entitäten, Text, Tabellen und Strukturen aus Dokumenten zu extrahieren.

* Es handelt sich um ein vortrainiertes Modell, das regelmäßig mit neuen Daten trainiert wird, um die Abdeckung und Genauigkeit zu verbessern.

* Das allgemeine Dokumentmodell unterstützt strukturierte, teilweise strukturierte und unstrukturierte Daten.

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

* Die besten Ergebnisse erzielen Sie, wenn Sie pro Dokument ein deutliches Foto oder einen hochwertigen Scan einreichen.
* Unterstützte Dateiformate: JPEG, PNG, BMP, TIFF und PDF (in Text eingebettet oder gescannt). In Text eingebettete PDF-Dateien sind am besten geeignet, um die Möglichkeit von Fehlern beim Extrahieren und Auffinden von Zeichen auszuschließen.
* Bei PDF und TIFF können bis zu 2000 Seiten verarbeitet werden (bei einem kostenlosen Abonnement werden nur die ersten beiden Seiten verarbeitet).
* Die Dateigröße muss weniger als 50 MB betragen.
* Bei Bildern müssen die Abmessungen zwischen 50 × 50 Pixel und 10.000 × 10.000 Pixel liegen.
* Die PDF-Abmessungen sind bis zu 17 x 17 Zoll, was dem Papierformat Legal oder A3 entspricht, oder kleiner.
* Der Gesamtumfang der Trainingsdaten beträgt 500 Seiten oder weniger.
* Wenn Ihre PDFs passwortgeschützt sind, müssen Sie die Sperre vor der Einreichung aufheben.
* Für unüberwachtes Lernen (ohne bezeichnete Daten):
  * Die Daten müssen Schlüssel und Werte enthalten.
  * Die Schlüssel müssen über oder links von den Werten stehen; sie dürfen nicht unter oder rechts von ihnen stehen.

## <a name="supported-languages-and-locales"></a>Unterstützte Sprachen und Gebietsschemas

| Modell | Sprache – Gebietsschemacode | Standard |
|--------|:----------------------|:---------|
|Allgemeines Dokument| <ul><li>Englisch (USA) – en-US</li></ul>| Englisch (USA) – en-US|

### <a name="named-entity-recognition-ner-categories"></a>NER-Kategorien (Erkennung benannter Entitäten)

| Category | type | BESCHREIBUNG |
|-----------|-------|--------------------|
| Person | Zeichenfolge | Der partielle oder vollständige Name einer Person. |
|PersonType | Zeichenfolge | Der Auftragstyp oder die Rolle einer Person.  |
| Standort | Zeichenfolge | Natürliche und von Menschen hergestellte Wahrzeichen, Gebäude, geografische Merkmale und geopolitische Entitäten |
| Organization | Zeichenfolge | Firmen, politische Gruppen, Musikgruppen, Sportvereine, Regierungsstellen und öffentliche Organisationen |
| Ereignis | Zeichenfolge | Historische Ereignisse, gesellschaftliche Ereignisse und Naturereignisse |
| Produkt | Zeichenfolge |Physische Objekte verschiedener Kategorien. |
| Skill | Zeichenfolge | Eine Funktion, Kenntnis oder ein Fachwissen |
| Adresse | Zeichenfolge | Vollständige Postanschrift |
| Telefonnummer | Zeichenfolge| Telefonnummern | 
E-Mail | Zeichenfolge | E-Mail-Adresse. |
| URL | Zeichenfolge| Website-URLs und -Links|
| IP-Adresse | Zeichenfolge| Netzwerk-IP-Adressen |
| Datetime | Zeichenfolge| Datums- und Uhrzeitangaben |
| Menge | Zeichenfolge | numerische Messungen und Einheiten |

## <a name="considerations"></a>Überlegungen

* Die Extraktion von Entitäten kann in Szenarien nützlich sein, in denen Sie die extrahierten Werte überprüfen möchten. Die Entitäten werden für den gesamten Inhalt der Dokumente extrahiert, nicht nur für die extrahierten Werte.

* Schlüssel sind Ausschnitte von Text, der aus dem Dokument extrahiert wurde. Für teilweise strukturierte Dokumente müssen Schlüssel möglicherweise einem vorhandenen Schlüsselwörterbuch zugeordnet werden.

* Erwarten Sie, dass Schlüssel-Wert-Paare mit einem Schlüssel, aber ohne Wert angezeigt werden. Wenn ein Benutzer z. B. keine E-Mail-Adresse in das Formular eingeben möchte.

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie, wie Sie die Vorschauversion in Ihren Anwendungen und Workflows verwenden können, indem Sie unseren [**Formularerkennung v3.0 Migrationsleitfaden**](v3-migration-guide.md) befolgen.

* Erkunden Sie unsere [**REST-API (Vorschau)** ](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v3-0-preview-1/operations/AnalyzeDocument), um mehr über die Vorschauversion und neue Funktionen zu erfahren.

> [!div class="nextstepaction"]
> [Ausprobieren von Formularerkennung Studio](https://formrecognizer.appliedai.azure.com/studio)
