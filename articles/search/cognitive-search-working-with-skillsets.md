---
title: Skillsetkonzepte
titleSuffix: Azure Cognitive Search
description: In Qualifikationsgruppen erstellen Sie eine KI-Anreicherungspipeline in der kognitiven Azure-Suche. Informieren Sie sich über wichtige Konzepte und Details zur Zusammenstellung von Qualifikationsgruppen.
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/10/2021
ms.openlocfilehash: 08bf1a57c4b7d4905693c3f11964feeb04c8beef
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131014859"
---
# <a name="skillset-concepts-in-azure-cognitive-search"></a>Skillsetkonzepte in Azure Cognitive Search

Dieser Artikel richtet sich an Entwickler, die ausführlichere Informationen über die Konzepte und den Aufbau von Skillsets benötigen. Es wird vorausgesetzt, dass der Leser mit den allgemeinen Konzepten und Workflows der [KI-Anreicherung](cognitive-search-concept-intro.md) vertraut ist.

Bei einem Skillset handelt es sich um eine wiederverwendbare Ressource in Azure Cognitive Search, die an einen [Indexer](search-indexer-overview.md) angefügt ist. Es enthält mindestens einen Skill, also eine atomischen Vorgang, mit dem integrierte KI-Prozesse oder externe benutzerdefinierte Prozesse über Dokumente aufgerufen werden, die aus einer externen Datenquelle abgerufen wurden.

Skills werden während der gesamten Skillsetverarbeitung in einem angereicherten Dokument gelesen und dort hin geschrieben. Bei einem angereicherten Dokument handelt es sich anfänglich nur um den unformatierten Inhalt, der aus einer Datenquelle extrahiert wurde. Mit jeder Skillausführung gewinnt es jedoch an Struktur und Inhalt. Letztendlich werden Knoten in einem angereicherten Dokument [Feldern](cognitive-search-output-field-mapping.md) in einem Suchindex oder [Projektionen](knowledge-store-projection-overview.md) in einem Wissensspeicher zugeordnet, sodass der Inhalt entsprechend weitergeleitet werden kann, sodass er von anderen Apps abgefragt oder verwendet werden kann.

:::image type="content" source="media/knowledge-store-concept-intro/knowledge-store-concept-intro.svg" alt-text="Pipeline mit Skillset" border="false":::

## <a name="skillset-definition"></a>Definition von Qualifikationsgruppen

Bei einem Skillset handelt es sich um eine Reihe von *Skills*, die einen atomischen Anreicherungsvorgang darstellen, wie z. B. das Übersetzen von Text, das Extrahieren von Schlüsselausdrücken oder das Durchführen einer optischen Zeichenerkennung in einer Bilddatei. Bei Skills kann es sich um [integrierte Skills](cognitive-search-predefined-skills.md) von Microsoft handeln oder um [benutzerdefinierte Skills](cognitive-search-create-custom-skill-example.md), die die Modelle bzw. die Verarbeitungslogik enthalten, die Sie bereitstellen. Damit werden angereicherte Dokumente erstellt, die bei der Indizierung verwendet oder in einen Wissensspeicher projiziert werden.

Skills weisen einen Typ, einen Kontext und Eingaben und Ausgaben auf, die häufig miteinander verkettet werden. Im folgenden Beispiel sind zwei [integrierte Skills](cognitive-search-predefined-skills.md) dargestellt, die zusammen verwendet werden. Zudem wird ein Teil der Terminologie rund um die Definition von Skillsets vorgestellt. 

+ Der erste Skill ist ein [Skill zur Textaufteilung](cognitive-search-skill-textsplit.md), der den Inhalt des Quellfelds „reviews_text“ als Eingabe akzeptiert und diesen Inhalt als Ausgabe in „Seiten“ mit 5000 Zeichen aufteilt. Die Aufteilung eines umfangreichen Texts in kleinere Abschnitte kann bei der Verarbeitung natürlicher Sprache zu besseren Ergebnissen führen.

+ Der zweite Skill ist ein [Skill zur Stimmungserkennung](cognitive-search-skill-sentiment.md), der „Seiten“ als Eingabe akzeptiert und ein neues Feld namens „Sentiment“ (Stimmung) als Ausgabe erstellt, das die Ergebnisse der Stimmungsanalyse enthält.

```json
{
    "skills": [
        {
            "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
            "name": "#1",
            "description": null,
            "context": "/document/reviews_text",
            "defaultLanguageCode": "en",
            "textSplitMode": "pages",
            "maximumPageLength": 5000,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/reviews_text"
                }
            ],
            "outputs": [
                {
                    "name": "textItems",
                    "targetName": "pages"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
            "name": "#2",
            "description": null,
            "context": "/document/reviews_text/pages/*",
            "defaultLanguageCode": "en",
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/reviews_text/pages/*",
                }
            ],
            "outputs": [
                {
                    "name": "score",
                    "targetName": "Sentiment"
                }
            ]
        }
. . . 
}
```

Wichtige Punkte, die beim obigen Beispiel zu beachten sind: Eingaben und Ausgaben sind Name/Wert-Paare, Sie können die Ausgaben eines Skills mit den Eingaben von nachgelagerten Skills abgleichen und alle Skills weisen einen Kontext auf, der bestimmt, an welcher Stelle in der Anreicherungsstruktur die Verarbeitung erfolgt.

Weitere Informationen zur Formulierung von Eingaben und Ausgaben finden Sie unter [Verweisen auf Anmerkungen](cognitive-search-concept-annotations-syntax.md).

## <a name="enrichment-tree"></a>Anreicherungsstruktur

Bei einem angereicherten Dokument handelt es sich um eine temporäre baumähnliche Struktur, die während der Skillausführung erstellt wird und mit der alle über den Skill vorgenommenen Änderungen erfasst und in einer Hierarchie aus adressierbaren Knoten dargestellt werden. Knoten enthalten zudem auch alle nicht angereicherten Felder, von der externen Datenquelle unverändert übergeben werden. Ein angereichertes Dokument ist für die Dauer der Ausführung eines Skillsets vorhanden, kann jedoch zwischengespeichert oder in einem Wissensspeicher gespeichert werden. 

Bei einem angereicherten Dokument handelt es sich anfänglich nur um den Inhalt, der bei der [*Dokumententschlüsselung*](search-indexer-overview.md#document-cracking) aus der Datenquelle extrahiert wird. Dabei werden Texte und Bilder aus der Quelle extrahiert und für die Sprach- oder Bildanalyse verfügbar gemacht. 

Der Ausgangsinhalt ist der *Wurzelknoten* (`document\content`) und ist in der Regel ein ganzes Dokument oder ein normalisiertes Bild, das beim Cracken des Dokuments aus einer Datenquelle extrahiert wird. Die Art, wie der Inhalt in einer Anreicherungsstruktur formuliert wird, variiert je nach Datenquellentyp. In der folgenden Tabelle wird für verschiedene unterstützte Datenquellen der Zustand eines Dokuments gezeigt, das in die Anreicherungspipeline wechselt:

|Datenquelle\Analysemodus|Standard|JSON, JSON-Zeilen & CSV|
|---|---|---|
|Blob Storage|/document/content<br>/document/normalized_images/*<br>…|/document/{key1}<br>/document/{key2}<br>…|
|Azure SQL|/document/{column1}<br>/document/{column2}<br>…|– |
|Cosmos DB|/document/{key1}<br>/document/{key2}<br>…|–|

Beim Ausführen von Skills wird der Anreicherungsstruktur die Ausgabe in Form von neuen Knoten hinzugefügt. Diese Knoten können als Eingabe für nachgelagerte Skills verwendet werden und werden schließlich in einen Wissensspeicher projiziert oder Indexfeldern zugeordnet. Skills, die Inhalte wie übersetzte Zeichenfolgen erstellen, schreiben ihre Ausgabe in das angereicherte Dokument. Entsprechend lesen Skills, die die Ausgabe von vorgelagerten Skills verwenden, im angereicherten Dokument, um die erforderlichen Eingaben abzurufen.

:::image type="content" source="media/cognitive-search-working-with-skillsets/skillset-def-enrichment-tree.png" alt-text="Skills lesen aus der und schreiben in die Anreicherungsstruktur" border="false":::

Eine Anreicherungsstruktur besteht aus extrahiertem Inhalt und Metadaten, die aus der Quelle abgerufen werden, sowie aus allen neuen Knoten, die von einem Skill erstellt werden, z. B.`translated_text` aus dem Skill [Textübersetzung](cognitive-search-skill-text-translation.md), `locations` aus dem Skill [Entitätserkennung](cognitive-search-skill-entity-recognition-v3.md) oder `keyPhrases` aus dem Skill [Schlüsselbegriffserkennung](cognitive-search-skill-keyphrases.md). Obwohl Sie eine [Anreicherungsstruktur](cognitive-search-debug-session.md) über einen visuellen Editor visualisieren und damit arbeiten können, handelt es sich größtenteils um eine interne Struktur. 

Anreicherungen sind nicht änderbar: Die Knoten können nach der Erstellung nicht bearbeitet werden. Mit steigender Komplexität Ihrer Qualifikationen wird auch Ihre Anreicherungsstruktur komplexer, aber nicht alle Knoten in der Anreicherungsstruktur müssen in den Index oder Wissensspeicher aufgenommen werden. Sie können nur eine Teilmenge der Anreicherungsausgaben selektiv speichern, sodass Sie nur die Elemente erhalten bleiben, die Sie verwenden möchten.

Da die Eingaben und Ausgaben eines Skills aus Anreicherungsstrukturen gelesen und in diese geschrieben werden, besteht eine der Aufgaben beim Skillsetentwurf darin, [Ausgabefeldzuordnungen](cognitive-search-output-field-mapping.md) zu erstellen, mit denen Inhalte aus der Anreicherungsstruktur in ein Feld in einem Suchindex verschoben werden. Entsprechend können Sie beim Erstellen eines Wissensspeichers Ausgaben [Formen](knowledge-store-projection-shape.md) zuordnen, die Projektionen zugewiesen sind.

> [!NOTE]
> Das Anreicherungsstrukturformat ermöglicht es der Anreicherungspipeline, selbst primitiven Datentypen Metadaten anzufügen. Die Metadaten sind keine gültigen JSON-Objekte, können jedoch in Projektionsdefinitionen in einem Wissensspeicher in ein gültiges JSON-Format projiziert werden. Weitere Informationen hierzu finden Sie unter [Der Skill „Shaper“](cognitive-search-skill-shaper.md).

## <a name="context"></a>Kontext

Jeder Skill verfügt über einen Kontext, bei dem es sich um das gesamte Dokument (`/document`) oder einen Knoten weiter unten in der Struktur (`/document/countries/`) handeln kann. Ein Kontext bestimmt Folgendes:

+ Die Anzahl der Skillausführungen für einen einzelnen Wert (einmal pro Feld, pro Dokument) oder für Kontextwerte vom Typ Sammlung, wobei das Hinzufügen von `/*` dazu führt, dass einmal für jede Instanz in der Sammlung ein Skill aufgerufen wird. 

+ Ausgabedeklaration oder die Stelle, an der in der Anreicherungsstruktur die Skillausgaben hinzugefügt werden. Ausgaben werden der Struktur immer als untergeordnete Elemente des Kontextknotens hinzugefügt.

+ Form der Eingaben. Bei Sammlungen mit mehreren Ebenen hat das Festlegen des Kontexts auf die übergeordnete Sammlung Auswirkungen auf die Form der Eingabe für den Skill. Wenn Sie beispielsweise über eine Anreicherungsstruktur mit einer Liste mit Ländern/Regionen verfügen, von denen jedes Land bzw. jede Region mit einer Liste mit Bundesländern/Kantonen angereichert ist, die wiederum jeweils eine Liste mit Postleitzahlen enthalten, bestimmt die Art, wie Sie den Kontext festlegen, die Art, wie die Eingabe interpretiert wird.

|Kontext|Eingabe|Form der Eingabe|Aufruf einer Qualifikation|
|-------|-----|--------------|----------------|
|`/document/countries/*` |`/document/countries/*/states/*/zipcodes/*` |Eine Liste aller Postleitzahlen im Land oder der Region |Einmal pro Land oder Region |
|`/document/countries/*/states/*` |`/document/countries/*/states/*/zipcodes/*` |Eine Liste aller Postleitzahlen im Bundesland | Einmal pro Kombination aus Land oder Region und Bundesland/Kanton|

## <a name="enrichment-example"></a>Beispiel für eine Anreicherung

In diesem Beispiel wird anhand des [Skillsets für Hotelbewertungen](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotelreviews/HotelReviews_skillset.json) als Bezugspunkt erläutert, wie sich eine [Anreicherungsstruktur](cognitive-search-working-with-skillsets.md#enrichment-tree) mithilfe von konzeptionellen Diagrammen durch die Ausführung von Skills weiterentwickelt.

Anhand dieses Beispiels wird zudem Folgendes erläutert:

+ Die Funktionsweise des Kontexts und der Eingaben eines Skills, um zu bestimmen, wie oft ein Skill ausgeführt wird
+ Die Form der Eingabe in Abhängigkeit vom Kontext

In diesem Beispiel enthalten Quellfelder aus einer CSV-Datei Kundenbewertungen zu Hotels („reviews_text“) und Bewertungen („reviews_rating“). Über den Indexer werden Metadatenfelder aus Blob Storage hinzugefügt und über Skills übersetzte Texte, Stimmungsbewertungen und die Schlüsselbegriffserkennung.

Im Beispiel für Hotelbewertungen stellt ein „Dokument“ innerhalb des Anreicherungsprozesses eine einzelne Hotelbewertung dar.

> [!TIP]
> Im [Azure-Portal](knowledge-store-create-portal.md) oder über [Postman und die REST-APIs](knowledge-store-create-rest.md) können Sie einen Suchindex und einen Wissensspeicher für diese Daten erstellen. Sie können auch [Debugsitzungen](cognitive-search-debug-session.md) verwenden, um Informationen über Zusammensetzung, Abhängigkeiten und Auswirkungen von Skillsets auf eine Anreicherungsstruktur zu erhalten. Die Bilder in diesem Artikel stammen von Debugsitzungen.

Konzeptionell sieht die anfängliche Anreicherungsstruktur wie folgt aus:

![Anreicherungsstruktur nach der Dokumententschlüsselung](media/cognitive-search-working-with-skillsets/enrichment-tree-doc-cracking.png "Anreicherungsstruktur nach der Dokumententschlüsselung")

Der Stammknoten für alle Anreicherungen ist `"/document"`. Wenn Sie Blob-Indexer verwenden, verfügt der `"/document"`-Knoten über die untergeordneten Knoten `"/document/content"` und `"/document/normalized_images"`. Wenn Sie, wie in diesem Beispiel, CSV-Daten verwenden, werden die Spaltennamen den Knoten unter `"/document"` zugeordnet.

### <a name="skill-1-split-skill"></a>Qualifikation 1: Qualifikation „Aufteilung“

Wenn der Quellinhalt aus großen Textblöcken besteht, ist es hilfreich, ihn in kleinere Komponenten zu zerlegen, um eine größere Genauigkeit bei der Erkennung von Sprache, Stimmung und Schlüsselbegriffen zu erreichen. Es stehen zwei Einheiten zur Verfügung: Seiten und Sätze. Eine Seite besteht aus ungefähr 5.000 Zeichen.

Ein Skill zur Textaufteilung erfolgt normalerweise an erster Stelle in einem Skillset.

```json
"@odata.type": "#Microsoft.Skills.Text.SplitSkill",
"name": "#1",
"description": null,
"context": "/document/reviews_text",
"defaultLanguageCode": "en",
"textSplitMode": "pages",
"maximumPageLength": 5000,
"inputs": [
{
    "name": "text",
    "source": "/document/reviews_text"
}
],
"outputs": [
{
    "name": "textItems",
    "targetName": "pages"
}
```

Mit dem Skillkontext `"/document/reviews_text"` wird der Skill „Aufteilung“ einmal für `reviews_text` ausgeführt. Die Qualifikationsausgabe ist eine Liste, in der `reviews_text` in 5000-Zeichen-Segmente aufgeteilt ist. Die Ausgabe des Skills „Aufteilung“ wird `pages` benannt und der Anreicherungsstruktur hinzugefügt. Mit dem Feature `targetName` können Sie eine Qualifikationsausgabe umbenennen, bevor sie der Anreicherungsstruktur hinzugefügt wird.

Die Anreicherungsstruktur verfügt jetzt über einen neuen Knoten, der unter den Kontext der Qualifikation platziert wird. Dieser Knoten ist für alle Qualifikationen, Projektionen oder Ausgabefeldzuordnungen verfügbar. 
 
![Anreicherungsstruktur nach Qualifikation 1](media/cognitive-search-working-with-skillsets/enrichment-tree-skill1.png "Anreicherungsstruktur nach Ausführung von Qualifikation 1")

Um auf eine der Anreicherungen zuzugreifen, die einem Knoten durch eine Qualifikation hinzugefügt wurden, ist der vollständige Pfad für die Anreicherung erforderlich. Wenn Sie z.B. den Text aus dem Knoten ```pages``` als Eingabe für eine andere Qualifikation verwenden möchten, müssen Sie ihn als ```"/document/reviews_text/pages/*"``` angeben. Weitere Informationen zu Pfaden finden Sie unter [Verweisen auf Anmerkungen](cognitive-search-concept-annotations-syntax.md).

### <a name="skill-2-language-detection"></a>Qualifikation 2: Sprachenerkennung

Dokumente zur Hotelbewertung enthalten Kundenfeedback in mehreren Sprachen. Der Skill zur Spracherkennung bestimmt, welche Sprache verwendet wird. Das Ergebnis wird dann an die Extraktion von Schlüsselbegriffen und die Erkennung von Stimmungen (nicht dargestellt) weitergeleitet, wobei die Sprache bei der Erkennung von Stimmungen und Begriffen berücksichtigt wird.

Die Qualifikation „Spracherkennung“ ist zwar die dritte in der Qualifikationsgruppe definierte Qualifikation (Qualifikation 3), sie wird aber als nächste Qualifikation ausgeführt. Da keine Eingaben erforderlich sind, wird sie nicht blockiert und parallel mit der vorherigen Qualifikation ausgeführt. Genau wie die Qualifikation „Aufteilung“ wird die Qualifikation „Spracherkennung“ auch einmal für jedes Dokument aufgerufen. Die Anreicherungsstruktur verfügt jetzt über einen neuen Knoten für die Sprache.

 ![Anreicherungsstruktur nach Qualifikation 2](media/cognitive-search-working-with-skillsets/enrichment-tree-skill2.png "Anreicherungsstruktur nach Ausführung von Skill 2")

### <a name="skills-3-and-4-sentiment-analysis-and-key-phrase-detection"></a>Skills 3 und 4 (Stimmungsanalyse und Schlüsselbegriffserkennung)

Kundenfeedback spiegelt eine Reihe von positiven und negativen Erfahrungen wider. Mit dem Skill „Stimmungsanalyse“ wird das Feedback analysiert und entlang eines Kontinuums eine Bewertung von negativen zu positiven Werten oder bei einer unbestimmten Stimmung neutral zugewiesen. Parallel zur Stimmungsanalyse werden mit der Schlüsselbegriffserkennung folgerichtig erscheinende Wörter und kurze Ausdrücke identifiziert und extrahiert.

Im Kontext von `/document/reviews_text/pages/*` werden die beiden Skills „Stimmungsanalyse“ und „Schlüsselbegriffserkennung“ einmal für jedes Element in der Sammlung `pages` aufgerufen. Die Ausgabe der Qualifikation ist ein Knoten unter dem zugeordneten page-Element. 

Wenn Sie sich jetzt die restlichen Qualifikationen in der Qualifikationsgruppe anschauen, sollten Sie sich vorstellen können, wie die Anreicherungsstruktur mit der Ausführung jeder weiteren Qualifikation weiter wächst. Einige Qualifikationen, wie z.B. die Qualifikation „Zusammenführen“ und die Qualifikation „Shaper“, erstellen auch neue Knoten, verwenden jedoch nur Daten aus vorhandenen Knoten und erstellen keine eigenen neuen Anreicherungen.

![Anreicherungsstruktur nach allen Qualifikationen](media/cognitive-search-working-with-skillsets/enrichment-tree-final.png "Anreicherungsstruktur nach allen Qualifikationen")

Die Farben der Connectors in der Struktur oben zeigen an, dass die Anreicherungen durch unterschiedliche Qualifikationen erstellt wurden, und die Knoten müssen einzeln adressiert werden und sind nicht Teil des Objekts, das bei der Auswahl des übergeordneten Knotens zurückgegeben wird.

### <a name="skill-5-shaper-skill"></a>Skill 5: Skill „Shaper“

Wenn die Ausgabe einen [Wissensspeicher](knowledge-store-concept-intro.md) enthält, fügen Sie in einem letzten Schritt den [Skill „Shaper“](cognitive-search-skill-shaper.md) hinzu. Mit dem Skill „Shaper“ werden aus Knoten in einer Anreicherungsstruktur Formen erstellt. Es kann beispielsweise vorkommen, dass Sie mehrere Knoten in einer einzelnen Form konsolidieren möchten. Dann können Sie diese Form als Tabelle projizieren (aus den Knoten werden die Spalten einer Tabelle) und anhand des Namens an eine Tabellenprojektion übergeben.

Der Skill „Shaper“ erleichtert die Arbeit, da er sich auf die Strukturierung unter einem Skill konzentriert. Alternativ können Sie die Inlinestrukturierung innerhalb einzelner Projektionen verwenden. Mit dem Skill „Shaper“ wird einer Anreicherungsstruktur weder etwas hinzugefügt noch etwas weggenommen, er wird also nicht visualisiert. Der Skill „Shaper“ ist vielmehr ein Mittel, mit dem sich eine bereits vorhandene Anreicherungsstruktur neu formulieren lässt. Vom Konzept her ist dies vergleichbar mit der Erstellung von Sichten aus Tabellen in einer Datenbank.

```json
{
  "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
  "name": "#5",
  "description": null,
  "context": "/document",
  "inputs": [
    {
      "name": "name",
      "source": "/document/name"
    },
    {
      "name": "reviews_date",
      "source": "/document/reviews_date"
    },
    {
      "name": "reviews_rating",
      "source": "/document/reviews_rating"
    },
    {
      "name": "reviews_text",
      "source": "/document/reviews_text"
    },
    {
      "name": "reviews_title",
      "source": "/document/reviews_title"
    },
    {
      "name": "AzureSearch_DocumentKey",
      "source": "/document/AzureSearch_DocumentKey"
    },
    {
      "name": "pages",
      "sourceContext": "/document/reviews_text/pages/*",
      "inputs": [
        {
          "name": "Sentiment",
          "source": "/document/reviews_text/pages/*/Sentiment"
        },
        {
          "name": "LanguageCode",
          "source": "/document/Language"
        },
        {
          "name": "Page",
          "source": "/document/reviews_text/pages/*"
        },
        {
          "name": "keyphrase",
          "sourceContext": "/document/reviews_text/pages/*/Keyphrases/*",
          "inputs": [
            {
              "name": "Keyphrases",
              "source": "/document/reviews_text/pages/*/Keyphrases/*"
            }
          ]
        }
      ]
    }
  ],
  "outputs": [
    {
      "name": "output",
      "targetName": "tableprojection"
    }
  ]
}
```

## <a name="next-steps"></a>Nächste Schritte

Nach der Einführung und einem Beispiel erstellen Sie als Nächstes Ihr erstes Skillset mithilfe von [integrierten Skills](cognitive-search-predefined-skills.md).

> [!div class="nextstepaction"]
> [Erstes Skillset erstellen](cognitive-search-defining-skillset.md)
