---
title: KI-Anreicherungskonzepte
titleSuffix: Azure Cognitive Search
description: Inhaltsextrahierung, Verarbeitung natürlicher Sprache (Natural Language Processing, NLP) und Bildverarbeitung werden zum Erstellen durchsuchbarer Inhalte in Azure Cognitive Search-Indizes mit sowohl vordefinierten kognitiven Fähigkeiten als auch benutzerdefinierten KI-Algorithmen verwendet.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/10/2021
ms.custom: references_regions
ms.openlocfilehash: 8010de7d6aa0af0d096a0c7ed23740f23734cda2
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131554391"
---
# <a name="ai-enrichment-in-azure-cognitive-search"></a>KI-Anreicherung in Azure Cognitive Search

In Azure Cognitive Search bezieht sich KI-Anreicherung auf integrierte kognitive Qualifikationen und benutzerdefinierte Qualifikationen, die während der Indizierung Analysen, Transformationen und die Generierung von Inhalten hinzufügen. Anreicherungen erstellen neue Informationen, wo bisher keine vorhanden waren: Extrahieren von Informationen aus Bildern, Erkennen von Stimmungen, Schlüsselausdrücke und Entitäten aus Text, um nur einige zu nennen. Anreicherungen fügen auch Struktur zu undifferenziertem Text hinzu. All diese Prozesse führen dazu, dass zuvor nicht durchsuchbare Inhalte für Volltextsuchszenarien verfügbar sind. In vielen Fällen sind angereicherte Dokumente für von der Suche abweichenden Szenarien nützlich, z. B. für das Knowledge Mining.

Die Anreicherung wird durch ein [**Skillset**](cognitive-search-working-with-skillsets.md) definiert, das an einen [**Indexer**](search-indexer-overview.md) angefügt ist. Der Indexer extrahiert den Inhalt und richtet ihn ein, während das Skillset neue Informationen und Strukturen aus Bildern, Blobs und anderen unstrukturierten Datenquellen identifiziert, analysiert und erstellt. Das Ergebnis einer Anreicherungspipeline ist entweder ein [**Suchindex**](search-what-is-an-index.md) oder ein [**Wissensspeicher**](knowledge-store-concept-intro.md).

![Diagramm der KI-Anreicherungspipeline](./media/cognitive-search-intro/cogsearch-architecture.png "Übersicht über die KI-Anreicherungspipeline")

Ein Skillset kann integrierte Skills aus Cognitive Search enthalten oder in einem [*benutzerdefinierten Skill*](cognitive-search-create-custom-skill-example.md) bereitgestellte externe Verarbeitung einbetten. Zu Beispielen für benutzerdefinierte Skills zählen ein benutzerdefiniertes Entitätsmodul oder ein benutzerdefinierter Dokumentenklassifizierer, das bzw. der auf eine bestimmte Domäne ausgerichtet ist, wie z. B. Finanzen, wissenschaftliche Veröffentlichungen oder Medizin.

Bei eingebetteten Skills wird zwischen folgenden Kategorien unterschieden:

+ Zu den Skills in Bezug auf die **Verarbeitung natürlicher Sprache** gehören [Entitätserkennung](cognitive-search-skill-entity-recognition-v3.md), [Sprachenerkennung](cognitive-search-skill-language-detection.md), [Schlüsselbegriffserkennung](cognitive-search-skill-keyphrases.md), Textbearbeitung, [Stimmungserkennung (einschließlich Opinion Mining)](cognitive-search-skill-sentiment-v3.md) und [PII-Erkennung](cognitive-search-skill-pii-detection.md). Mit diesen Fähigkeiten wird unstrukturierter Text in Form von durchsuchbaren und filterbaren Feldern in einem Index zugeordnet.

+ Die **Bildverarbeitungsfähigkeiten** umfassen [Optical Character Recognition (OCR)](cognitive-search-skill-ocr.md) und die Identifizierung von [visuellen Features](cognitive-search-skill-image-analysis.md), z.B. Gesichtserkennung, Bildinterpretation, Bilderkennung (berühmte Personen und Wahrzeichen) oder Attribute wie Bildausrichtung. Diese Fähigkeiten erstellen Textdarstellungen von Bildinhalt, sodass er mit den Abfragefunktionen von Azure Cognitive Search durchsucht werden kann.

Eingebettete Skills in Azure Cognitive Search basieren auf vortrainierten Machine Learning-Modellen in Cognitive Services-APIs: [Maschinelles Sehen](../cognitive-services/computer-vision/index.yml) und [Textanalyse](../cognitive-services/text-analytics/overview.md). Wenn Sie diese Ressourcen während der Inhaltsverarbeitung nutzen möchten, können Sie eine Cognitive Services-Ressource anfügen.

Die Verarbeitung von natürlicher Sprache und Bildern wird während der Phase der Datenerfassung angewendet, wobei die Ergebnisse zu einem Teil einer Dokumentkomposition in einem durchsuchbaren Index in Azure Cognitive Search werden. Daten stammen aus einem Azure-Dataset und werden dann über eine Indizierungspipeline übertragen, indem jeweils die erforderlichen [integrierten Fähigkeiten](cognitive-search-predefined-skills.md) verwendet werden.  

## <a name="feature-availability"></a>Verfügbarkeit von Funktionen

KI-Anreicherung ist in Regionen verfügbar, in denen auch Azure Cognitive Services verfügbar ist. Auf der Seite [Verfügbare Azure-Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/?products=search) können Sie die aktuelle Verfügbarkeit von KI-Anreicherung überprüfen. KI-Anreicherung ist in allen unterstützten Regionen mit Ausnahme der folgenden verfügbar:

+ Australien, Südosten
+ China, Norden 2
+ Norwegen, Osten
+ Deutschland, Westen-Mitte

Wenn sich Ihr Suchdienst in einer dieser Regionen befindet, können Sie keine Skillsets erstellen und verwenden, aber alle anderen Suchdienstfunktionen sind verfügbar und werden vollständig unterstützt.

## <a name="when-to-use-ai-enrichment"></a>Einsatzgebiete der KI-Anreicherung

Sie sollten eine Anreicherung in Betracht ziehen, wenn es sich bei Ihren Rohinhalten um unstrukturierten Text, um Bildinhalte oder um Inhalte handelt, für die Spracherkennung und -übersetzung benötigt werden. Die Anwendung von KI in Form der integrierten kognitiven Qualifikationen kann den Nutzen dieser Inhalte in Ihren Such- und Data Science-Apps erhöhen. 

Darüber hinaus können Sie auch eine benutzerdefinierte Qualifikation hinzufügen, wenn Sie über Open-Source-, Drittanbieter- oder Erstanbietercode verfügen, den Sie in die Pipeline integrieren möchten. Zu dieser Kategorie gehören Klassifizierungsmodelle, mit denen wichtige Merkmale verschiedener Dokumenttypen identifiziert werden. Es könnte aber jedes beliebige Paket verwendet werden, das den Nutzen Ihrer Inhalte erhöht.

### <a name="use-cases-for-built-in-skills"></a>Anwendungsfälle für integrierte Skills

Ein [Skillset](cognitive-search-defining-skillset.md) mit integrierten Skills eignet sich sehr gut für die folgenden Anwendungsszenarien:

+ [Optische Zeichenerkennung (Optical Character Recognition, OCR)](cognitive-search-skill-ocr.md), die Schriftarten und handschriftlichen Text in gescannten Dokumenten (JPEG) erkennt, ist möglicherweise der am häufigsten verwendete Skill. Durch Anfügen des OCR-Skills wird Text aus JPEG-Dateien identifiziert, extrahiert und erfasst.

+ [Textübersetzung](cognitive-search-skill-text-translation.md) von mehrsprachigen Inhalten ist ein weiterer häufig verwendeter Skill. Die Spracherkennung ist in die Textübersetzung integriert, aber Sie können [Sprachenerkennung](cognitive-search-skill-language-detection.md) auch unabhängig ausführen, wenn Sie nur die Sprachcodes des Inhalts in Ihrem Korpus wünschen.

+ PDF-Dateien mit Kombinationen aus Bild und Text. Texte in PDF-Dateien können während der Indizierung extrahiert werden, ohne dass die Schritte zur Anreicherung ausgeführt werden. Beim Hinzufügen von Bildverarbeitung und natürlicher Sprachverarbeitung erzielen Sie jedoch häufig ein besseres Ergebnis als bei einer Standardindizierung.

+ Unstrukturierte oder teilweise strukturierte Dokumente mit Inhalten, die eine inhärente Bedeutung oder einen Kontext haben, der im größeren Dokument ausgeblendet ist. 

  Blobs enthalten häufig umfangreiche Inhalte, die in ein einzelnes „Feld“ gepackt sind. Durch das Anfügen von Fähigkeiten zur Bildverarbeitung und natürlicher Sprachverarbeitung an einen Indexer können Sie neue Informationen erstellen, die in den Rohdaten noch vorhanden sind, aber sonst nicht als unterschiedliche Felder aufgeführt werden. Einige einsatzbereite, integrierte kognitive Skills, die hilfreich sein können: [Schlüsselbegriffserkennung](cognitive-search-skill-keyphrases.md) und [Entitätserkennung](cognitive-search-skill-entity-recognition-v3.md) (Personen, Organisationen und Standorte, um einige zu nennen).

  Darüber hinaus können Sie mit integrierten Fähigkeiten Inhalte durch Textaufteilung, Textzusammenführung und Shape-Vorgänge neu strukturieren.

### <a name="use-cases-for-custom-skills"></a>Anwendungsfälle für benutzerdefinierte Skills

Benutzerdefinierte Fähigkeiten können komplexere Szenarien unterstützen, z. B. das Erkennen von Formularen oder die benutzerdefinierte Entitätserkennung mithilfe eines Modells, das Sie bereitstellen und in der [benutzerdefinierten Skills-Webschnittstelle](cognitive-search-custom-skill-interface.md) umschließen. Beispiele für benutzerdefinierte Fähigkeiten sind die [Formularerkennung](../applied-ai-services/form-recognizer/overview.md), die Integration der [Bing-Entitätssuche-API](./cognitive-search-create-custom-skill-example.md) und [die Erkennung von benutzerdefinierten Entitäten](https://github.com/Microsoft/SkillsExtractorCognitiveSearch).

## <a name="enrichment-steps"></a>Anreicherungsschritte <a name="enrichment-steps"></a>

Eine Anreicherungspipeline besteht aus [*Indexern*](search-indexer-overview.md), die über bestimmte [*Skillsets*](cognitive-search-working-with-skillsets.md) verfügen. Ein Skillset definiert die Anreicherungsschritte, und der Indexer steuert das Skillset. Beim Konfigurieren eines Indexers können Sie verschiedene Eigenschaften einbeziehen. Dazu gehören beispielsweise Ausgabefeldzuordnungen, die angereicherte Inhalte an einen [Suchindex](search-what-is-an-index.md) senden, oder Projektionen, die Datenstrukturen in einem [Wissensspeicher](knowledge-store-concept-intro.md) definieren.

Nach der Indizierung können Sie über Suchanforderungen über sämtliche [von Azure Cognitive Search unterstützte Abfragetypen](search-query-overview.md) auf Inhalte zugreifen.

### <a name="step-1-connection-and-document-cracking-phase"></a>Schritt 1: Phase der Entschlüsselung von Verbindung und Dokument

Indexer stellen mithilfe von Informationen, die in einer Indexerdatenquelle bereitgestellt werden, eine Verbindung mit externen Quellen her. Wenn sich der Indexer mit der Ressource verbindet, [„zerlegt“ er die Dokumente](search-indexer-overview.md#document-cracking), um Text und Bilder zu extrahieren. Bildinhalte können an Skills weitergeleitet werden, die die Bildverarbeitung durchführen, während Textinhalte in die Warteschlange für die Textverarbeitung gestellt werden. 

![Phase der Dokumententschlüsselung](./media/cognitive-search-intro/document-cracking-phase-blowup.png "Dokumententschlüsselung")

In diesem Schritt werden alle anfänglichen oder rohen Inhalte zusammengestellt, die eine KI-Anreicherung durchlaufen. Für jedes Dokument wird eine Anreicherungsstruktur erstellt. Anfänglich ist die Struktur nur eine Stammknotendarstellung, aber sie wächst und wird während der Ausführung des Skillsets strukturiert.

### <a name="step-2-skillset-enrichment-phase"></a>Schritt 2: Phase der Skillsetanreicherung

Ein Skillset definiert die atomaren Vorgänge, die für jedes Dokument ausgeführt werden. Für aus einer PDF-Datei extrahierten Texte und extrahierte Bilder, könnte ein Skillset beispielsweise die Entitätserkennung, die Sprachenerkennung oder die Schlüsselbegriffserkennung anwenden, um neue Felder in Ihrem Index zu erstellen, die nativ in der Quelle nicht verfügbar sind. 

![Phase der Anreicherung](./media/cognitive-search-intro/enrichment-phase-blowup.png "Phase der Anreicherung")

 Eine Skillset kann minimal oder höchst komplex sein und bestimmt nicht nur den Verarbeitungstyp, sondern auch die Reihenfolge der Vorgänge. Die meisten Skillsets enthalten etwa drei bis fünf Skills.

Ein Skillset bietet zusammen mit den als Bestandteil eines Indexers definierten [Ausgabefeldzuordnungen](cognitive-search-output-field-mapping.md) eine umfassende Beschreibung der Anreicherungspipeline. Weitere Informationen zum Zusammensetzen all dieser Teile finden Sie unter [Definieren einer Fähigkeitengruppe](cognitive-search-defining-skillset.md).

Die Pipeline generiert intern eine Sammlung angereicherter Dokumente. Sie können entscheiden, welche Teile der angereicherten Dokumente indizierbaren Feldern in Ihrem Suchindex zugeordnet werden sollen. Wenn Sie beispielsweise die Fähigkeiten „Schlüsselbegriffserkennung“ und „Entitätserkennung“ angewendet haben, würden diese neuen Felder ein Bestandteil des angereicherten Dokuments und können Feldern in Ihrem Index zugeordnet werden. Weitere Informationen zu Eingabe-/Ausgabeformationen finden Sie unter [Anmerkungen](cognitive-search-concept-annotations-syntax.md).

### <a name="step-3-indexing"></a>Schritt 3: Indizierung

Die Indizierung ist der Prozess, bei dem rohe und angereicherte Inhalte als Felder in einem Suchindex erfasst werden, bzw. als [Projektionen](knowledge-store-projection-overview.md), wenn auch ein Wissensspeicher erstellt wird. Derselbe angereicherte Inhalt kann in beiden angezeigt werden, indem er mithilfe von impliziten oder expliziten Feldzuordnungen an die richtigen Felder gesendet wird.

Angereicherte Inhalte werden während der Ausführung des Skillsets generiert und sind temporär, es sei denn, Sie speichern sie. Damit angereicherte Inhalte in einem Suchindex angezeigt werden, muss der Indexer über Zuordnungsinformationen verfügen, sodass er angereicherten Inhalt an ein Feld in einem Suchindex senden kann. [Ausgabefeldzuordnungen](cognitive-search-output-field-mapping.md) stellen diese Verbindungen her.

## <a name="saving-enriched-output"></a>Speichern der angereicherten Ausgabe

In Azure Cognitive Search speichert ein Indexer die von ihm erstellte Ausgabe.

Ein [**durchsuchbarer Index**](search-what-is-an-index.md) ist ein Index der Ausgaben, der immer von einem Indexer erstellt wird. Die Indexangabe ist eine Indexeranforderung, und wenn Sie ein Skillset anfügen, werden die Ausgabe des Skillsets und alle Felder, die direkt aus der Quelle zugeordnet werden, zum Auffüllen des Index verwendet. In der Regel werden die Ergebnisse bestimmter Skills, z. B. Schlüsselbegriffe oder Stimmungswerte, im Index in zu diesem Zweck erstellten Feldern erfasst.

Ein [**Wissensspeicher**](knowledge-store-concept-intro.md) ist eine optionale Ausgabe, die für nachgeschaltete Apps wie Knowledge Mining verwendet wird. Ein Wissensspeicher wird durch ein Skillset definiert. Seine Definition bestimmt, ob Ihre angereicherten Dokumente als Tabellen oder Objekte (Dateien oder Blobs) projiziert werden. Tabellarische Projektionen eignen sich gut für interaktive Analysen in Tools wie Power BI, während Dateien und Blobs typischerweise in Data Science- oder ähnlichen Prozessen verwendet werden.

Schließlich kann ein Indexer für die potenzielle Wiederverwendung in nachfolgenden Skillsetausführungen [**angereicherte Dokumente**](cognitive-search-incremental-indexing-conceptual.md) in Azure Blob Storage zwischenspeichern. Der Cache dient zur internen Verwendung. Zwischengespeicherte Anreicherungen können von demselben Skillset verwendet werden, das Sie zu einem späteren Zeitpunkt erneut ausführen. Das Zwischenspeichern ist hilfreich, wenn Ihr Skillset Bildanalyse oder OCR umfasst und Sie Zeit und Kosten für die erneute Verarbeitung von Bilddateien vermeiden möchten.

Indizes und Wissensspeicher sind vollständig unabhängig voneinander. Sie müssen zwar einen Index anfügen, um die Indexeranforderungen zu erfüllen, aber wenn Ihr einziges Ziel ein Wissensspeicher ist, können Sie den Index ignorieren, nachdem er aufgefüllt wurde. Löschen Sie ihn jedoch nicht. Wenn Sie den Indexer und das Skillset erneut ausführen möchten, benötigen Sie den Index, damit der Indexer ausgeführt werden kann.

## <a name="using-enriched-content"></a>Verwenden angereicherter Inhalte

Nach Abschluss der Verarbeitung verfügen Sie über einen [Suchindex](search-what-is-an-index.md) mit angereicherten Dokumenten, die mit Azure Cognitive Search im Volltext durchsucht werden können. Entwickler und Benutzer greifen über das [**Abfragen des Indexes**](search-query-overview.md) auf den von der Pipeline generierten angereicherten Inhalt zu. Der Index ist vergleichbar mit beliebigen anderen Indizes, die für Azure Cognitive Search erstellt werden können: Sie können Textanalyse durch benutzerdefinierte Analysen ergänzen, Fuzzysuchabfragen aufrufen, Filter hinzufügen oder zur Optimierung der Suchrelevanz mit Bewertungsprofilen experimentieren.

Sie könnten auch einen [Wissensspeicher](knowledge-store-concept-intro.md) verwenden. Der Wissensspeicher enthält Daten, die in Knowledge Mining-Szenarien wie Analysen oder Machine Learning genutzt werden können. Sie können den [ Storage Browser ](knowledge-store-view-storage-explorer.md), [Power BI](knowledge-store-connect-power-bi.md) oder jede Anwendung, die eine Verbindung zu Azure Storage herstellt.

## <a name="checklist-a-typical-workflow"></a>Prüfliste: Typischer Workflow

1. Beim Starten eines Projekts ist es hilfreich, zunächst mit einer Teilmenge von Daten zu arbeiten. Der Indexer- und Skillsetentwurf ist ein iterativer Prozess, und die Iteration erfolgt schneller, wenn Sie mit einem kleinen repräsentativen Datensatz arbeiten.

1. Erstellen Sie eine [Datenquelle](/rest/api/searchservice/create-data-source), die eine Verbindung zu Ihren Daten angibt.

1. Erstellen Sie ein [Skillset](/rest/api/searchservice/create-skillset) zum Hinzufügen von Anreicherungen.

1. Erstellen Sie ein [Indexschema](/rest/api/searchservice/create-index), das einen Suchindex definiert.

1. Erstellen Sie einen [Indexer](/rest/api/searchservice/create-indexer), der alle oben genannten Komponenten zusammenführt. Der erstellte oder ausgeführte Indexer ruft die Daten ab, führt das Skillset aus und lädt den Index.

1. Führen Sie zum Auswerten von Ergebnissen und zum Ändern von Code Abfragen aus, um Fähigkeitengruppen, Schemas oder die Indexerkonfiguration zu aktualisieren.

Zur Iteration der oben genannten Schritte [setzen Sie den Indexer zurück](search-howto-reindex.md), bevor Sie die Pipeline neu erstellen, oder löschen Sie die Objekte bei jeder Ausführung, und erstellen Sie sie neu (empfohlen, wenn Sie den Free-Tarif verwenden). Sie sollten auch die [Zwischenspeicherung von Anreicherungen aktivieren](cognitive-search-incremental-indexing-conceptual.md), um vorhandene Anreicherungen nach Möglichkeit wiederzuverwenden.

## <a name="next-steps"></a>Nächste Schritte

+ [Schnellstart: Erstellen eines Skillsets für Textübersetzung und Entität](cognitive-search-quickstart-blob.md)
+ [Schnellstart: Erstellen eines Skillsets für OCR-Bilder](cognitive-search-quickstart-ocr.md)
+ [Tutorial: Informationen zu den REST-APIs für die KI-Anreicherung](cognitive-search-tutorial-blob.md)
+ [Skillsetkonzepte](cognitive-search-working-with-skillsets.md)
+ [Wissensspeicher: Konzepte](knowledge-store-concept-intro.md)
+ [Erstellen eines Skillsets](cognitive-search-defining-skillset.md)
+ [Erstellen von Wissensspeichern](knowledge-store-create-rest.md)