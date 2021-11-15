---
title: 'Grenzwerte und Grenzen: Fragen und Antworten'
description: Fragen und Antworten weist Metagrenzwerte für Teile der Wissensdatenbank und des Diensts auf. Es ist wichtig, Ihre Wissensdatenbank innerhalb dieser Grenzwerte zu halten, um sie testen und veröffentlichen zu können.
ms.service: cognitive-services
ms.subservice: language-service
author: mrbullwinkle
ms.author: mbullwin
ms.topic: conceptual
ms.date: 11/02/2021
ms.openlocfilehash: 185a76aeb4aeeadf4fea9b0b316b5905b1f598a5
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478859"
---
# <a name="project-limits-and-boundaries"></a>Projektgrenzwerte und -grenzen

Bei den folgenden Grenzwerten von Fragen und Antworten handelt es sich um eine Kombination aus den [Grenzwerten für den Azure Cognitive Search-Tarif](../../../../search/search-limits-quotas-capacity.md) und den Grenzwerten für Fragen und Antworten. Beide Grenzwerte haben Einfluss darauf, wie viele Wissensdatenbanken Sie pro Ressource erstellen können und wie groß die Wissensdatenbanken jeweils werden können.

## <a name="knowledge-bases"></a>Wissensdatenbanken

Die maximale Anzahl von Wissensdatenbanken basiert auf den [Grenzwerten für den Azure Cognitive Search-Dienst](../../../../search/search-limits-quotas-capacity.md).

|**Azure Cognitive Search-Tarif** | **Free** | **Grundlegend** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|Maximal zulässige Anzahl der veröffentlichten Wissensdatenbanken|2|14|49|199|199|2\.999|

 Wenn Ihr Tarif beispielsweise 15 zulässige Indizes aufweist, können Sie 14 Wissensdatenbanken veröffentlichen (1 Index pro veröffentlichter Wissensdatenbank). Der 15. Index (`testkb`) wird für alle Wissensdatenbanken zum Erstellen und Testen verwendet.

## <a name="extraction-limits"></a>Grenzwerte für die Extraktion

### <a name="file-naming-constraints"></a>Benennungskonventionen für Dateien

Dateinamen dürfen keines der folgenden Zeichen enthalten:

|Verwenden Sie nicht das Zeichen|
|--|
|Einfache Anführungszeichen `'`|
|Doppelte Anführungszeichen `"`|

### <a name="maximum-file-size"></a>Maximale Dateigröße

|Format|Max. Dateigröße (MB)|
|--|--|
|`.docx`|10|
|`.pdf`|25|
|`.tsv`|10|
|`.txt`|10|
|`.xlsx`|3|

### <a name="maximum-number-of-files"></a>Maximale Anzahl von Dateien

> [!NOTE]
> Für Fragen und Antworten gelten derzeit keine Grenzwerte für die Anzahl der Quellen, die hinzugefügt werden können. Der Durchsatz ist derzeit auf 10 Transaktionen pro Sekunde sowohl für Verwaltungs-APIs als auch für Vorhersage-APIs begrenzt.

### <a name="maximum-number-of-deep-links-from-url"></a>Maximale Anzahl von Deep-Links für die URL

Die maximale Anzahl von Deep-Links, die zum Extrahieren von Frage-Antwort-Paaren aus einer URL-Seite durchsucht werden können, beträgt **20**.

## <a name="metadata-limits"></a>Grenzwerte für Metadaten

Metadaten werden als textbasiertes `key:value`-Paar dargestellt, z. B. `product:windows 10`. Die Metadaten werden in Kleinbuchstaben gespeichert und verglichen. Die maximale Anzahl von Metadatenfeldern basiert auf den **[Grenzwerten für den Azure Cognitive Search-Dienst](../../../../search/search-limits-quotas-capacity.md)** .

Wenn Sie Projekte mit mehreren Sprachen in einer einzelnen Sprachressource auswählen, gibt es einen dedizierten Testindex pro Projekt/Wissensdatenbank. Daher wird der Grenzwert pro Projekt/Wissensdatenbank im Sprachdienst angewandt.

|**Azure Cognitive Search-Tarif** | **Free** | **Grundlegend** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|Maximale Anzahl von Metadatenfeldern pro Sprachdienst (pro Wissensdatenbank)|1\.000|100*|1\.000|1\.000|1\.000|1\.000|

Wenn Sie sich nicht für Projekte mit mehreren Sprachen entscheiden, gelten die Grenzwerte für alle Wissensdatenbanken im Sprachdienst.

|**Azure Cognitive Search-Tarif** | **Free** | **Grundlegend** |**S1** | **S2**| **S3** |**S3 HD**|
|---|---|---|---|---|---|----|
|Maximale Anzahl von Metadatenfeldern pro Sprachdienst (alle Wissensdatenbanken)|1\.000|100*|1\.000|1\.000|1\.000|1\.000|

### <a name="by-name-and-value"></a>Nach Name und Wert

Die Länge und die zulässigen Zeichen für den Metadatennamen und -wert sind in der folgenden Tabelle aufgeführt:

|Element|Zulässige Zeichen|RegEx-Musterabgleich|Maximale Anzahl von Zeichen|
|--|--|--|--|
|Name (Schlüssel)|Zulässig:<br>Alphanumerische Zeichen (Buchstaben und Ziffern)<br>`_` (Unterstrich)<br> Darf keine Leerzeichen enthalten.|`^[a-zA-Z0-9_]+$`|100|
|Wert|Alles zulässig mit Ausnahme von:<br>`:` (Doppelpunkt)<br>`|` (senkrechter Strich)<br>Nur ein Wert ist zulässig.|`^[^:|]+$`|500|
|||||

## <a name="knowledge-base-content-limits"></a>Grenzwerte für die Inhalte einer Wissensdatenbank
Allgemeine Grenzwerte für die Inhalte in der Knowledge Base:
* Länge des Antworttexts: 25.000 Zeichen
* Länge des Fragentexts: 1.000 Zeichen
* Länge des Texts für den Metadatenschlüssel: 100 Zeichen
* Länge des Texts für den Metadatenwert: 500 Zeichen
* Unterstützte Zeichen für den Metadatennamen: Buchstaben, Ziffern und `_`
* Unterstützte Zeichen für den Metadatenwerte: Alle Zeichen außer `:` und `|`
* Länge des Dateinamens: 200
* Unterstützte Dateiformate: „.tsv“, „.pdf“, „.txt“, „.docx“, „.xlsx“.
* Maximale Anzahl von alternativen Fragen: 300
* Maximale Anzahl von Frage-Antwort-Paaren: Abhängig vom ausgewählten **[Azure Cognitive Search-Tarif](../../../../search/search-limits-quotas-capacity.md#document-limits)** . Ein Frage-Antwort-Paar wird einem Dokument im Azure Cognitive Search-Index zugeordnet.
* URL/HTML-Seite: Eine Million Zeichen

## <a name="create-project-call-limits"></a>Grenzwerte bei Aufrufen zum Erstellen von Projekten:

Dies sind Grenzwerte für jede Aktion zum Erstellen von Projekten/Wissensdatenbanken, also z. B. für das Aufrufen von *Neues Projekt erstellen* oder der REST-API zum Erstellen eines Projekt oder einer Wissensdatenbank.

* Empfohlene maximale Anzahl von alternativen Fragen pro Antwort: 300
* Maximale Anzahl von URLs: 10
* Maximale Anzahl von Dateien: 10
* Maximale Anzahl zulässiger QnAs pro Aufruf: 1000

## <a name="update-knowledge-base-call-limits"></a>Grenzwerte für Aufrufe zum Aktualisieren einer Wissensdatenbank

Dabei handelt es sich um die Grenzwerte für die einzelnen Aktualisierungsaktionen, d. h. Auswählen von *Speichern* oder Aufrufen der REST-API mit einer Aktualisierungsanforderung.
* Länge jedes Quellnamens: 300
* Empfohlene maximale Anzahl hinzugefügter oder gelöschter alternativer Fragen: 300
* Maximale Anzahl hinzugefügter oder gelöschter Metadatenfelder: 10
* Maximale Anzahl der URLs, die aktualisiert werden können: 5
* Maximale Anzahl zulässiger QnAs pro Aufruf: 1000

## <a name="add-unstructured-file-limits"></a>Hinzufügen von Grenzwerten für unstrukturierte Dateien

> [!NOTE]
> * Wenn Sie größere Dateien verwenden müssen, als der Grenzwert zulässt, können Sie die Datei in kleinere Dateien aufteilen, bevor Sie sie an die API senden. 

Diese stellen die Grenzwerte dar, wenn unstrukturierte Dateien zum *Erstellen eines Projekts* oder zum Aufrufen der REST-API zum Erstellen einer Wissensdatenbank verwendet werden:
* Länge der Datei: Wir extrahieren die ersten 32.000 Zeichen.
* Maximal drei Antworten pro Datei

## <a name="prebuilt-question-answering-limits"></a>Vordefinierte Grenzwerte für die Beantwortung von Fragen

> [!NOTE]
> * Wenn Sie größere Dokumente verwenden müssen, als der Grenzwert zulässt, können Sie den Text in kleinere Textabschnitte aufteilen, bevor Sie ihn an die API senden. 
> * Ein Dokument ist eine einzelne aus Textzeichen bestehende Zeichenfolge.  

Diese stellen die Grenzwerte dar, wenn die REST-API zum Beantworten einer Frage verwendet wird, ohne ein Projekt/eine Wissensdatenbank zu erstellen:
* Anzahl der Dokumente: 5
* Maximale Größe eines einzelnen Dokuments: 5.120 Zeichen
* Maximal drei Antworten pro Dokument