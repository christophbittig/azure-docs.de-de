---
title: Projektionskonzepte
titleSuffix: Azure Cognitive Search
description: Speichern und formen Sie Ihre angereicherten Daten aus der Indizierungspipeline mit KI-Anreicherung in einem Wissensspeicher zur Verwendung in von der Volltextsuche abweichenden Szenarien.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/08/2021
ms.openlocfilehash: 841cd106f1c54e1c35d3b2785eb942842d77f825
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/09/2021
ms.locfileid: "129706767"
---
# <a name="knowledge-store-projections-in-azure-cognitive-search"></a>Projektionen eines Wissensspeichers in Azure Cognitive Search

Projektionen sind das Element einer [Wissensspeicher](knowledge-store-concept-intro.md)-Definition, das den physischen Ausdruck Ihrer Daten in Azure Storage spezifiziert. Eine Projektionsdefinition bestimmt die Anzahl und Art der Datenstrukturen in Azure Storage.

## <a name="types-of-data-structures"></a>Arten von Datenstrukturen

Ein Wissensspeicher ist eine logische Konstruktion, die physisch in Azure Storage als Tabellen, JSON-Objekte oder binäre Bilddateien ausgedrückt wird.

| Projektion | Storage | Verwendung |
|------------|---------|-------|
| Tabellen | Azure Table Storage | Wird für Daten verwendet, die sich am besten als Zeilen und Spalten darstellen lassen. Mit Tabellenprojektionen können Sie eine schematisierte Form oder Projektion definieren. Nur gültige JSON-Objekte können als Tabellen projiziert werden. Da ein angereichertes Dokument Knoten enthalten kann, die keine benannten JSON-Objekte sind, [fügen Sie einen Shaper-Skill hinzu oder verwenden Inlineformen](knowledge-store-projection-shape.md) innerhalb eines Skills, um gültigen JSON-Code zu erstellen. |
| Objekte | Azure Blob Storage | Wird verwendet, wenn Sie eine JSON-Darstellung Ihrer Daten und Anreicherungen benötigen. Wie bei Tabellenprojektionen können nur gültige JSON-Objekte als Objekte projiziert werden, und die Strukturierung kann Ihnen dabei helfen. |
| Dateien | Azure Blob Storage | Wird verwendet, wenn Sie normalisierte, binäre Bilddateien speichern müssen. |

Sie können mehrere Projektionen Ihrer Daten definieren, wenn diese angereichert werden. Mehrere Projektionen sind nützlich, wenn Sie die gleichen Daten für einzelne Anwendungsfälle unterschiedlich formen möchten.

## <a name="basic-definition"></a>Websites Basic Beschreibung

Projektionen sind eine Reihe komplexer Sammlungen unter einer `knowledgeStore` Definition in einem [Skillset-Objekt](/rest/api/searchservice/create-skillset). 

Jeder Satz von Tabellen, Objekten und Dateien ist eine *Projektgruppe*, und Sie können über mehrere Gruppen verfügen, wenn die Speicheranforderungen die Unterstützung verschiedener Tools und Szenarien umfassen. Innerhalb einer einzelnen Gruppe können mehrere Tabellen, Objekte und Dateien enthalten sein. 

Normalerweise wird nur eine Gruppe verwendet, aber das folgende Beispiel zeigt zwei, um die Idee von mehreren Gruppen zu verdeutlichen.

```json
"knowledgeStore" : {
    "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
    "projections": [
        {
            "tables": [],
            "objects": [],
            "files": []
        }, 
        {
            "tables": [],
            "objects": [],
            "files": []
        }
    ]
}
```

## <a name="data-isolation-and-relatedness"></a>Datenisolierung und -verwandtschaft

Die Verwendung mehrerer Sätze von Tabellen-Objekt-Datei-Kombinationen ist zur Unterstützung verschiedener Szenarien nützlich. Sie können einen Satz für den Entwurf und das Debuggen eines Skillsets verwenden, um die Ausgabe zur weiteren Untersuchung zu erfassen, während ein zweiter Satz Ausgaben für eine Online-App sammelt und ein dritter Satz für Data Science-Workloads verwendet wird.

Projektionsgruppen weisen die folgenden Hauptmerkmale für gegenseitige Ausschließlichkeit und Verbundenheit auf. 

| Prinzip | BESCHREIBUNG |
|-----------|-------------|
| Gegenseitige Ausschließlichkeit | Alle Inhalte, die in einer einzelnen Gruppe projiziert werden, sind unabhängig von in anderen Projektionsgruppen projizierten Daten. Diese Unabhängigkeit bedeutet, dass die gleichen Daten unterschiedlich geformt, aber in jeder Projektionsgruppe wiederholt vorhanden sein können. Jede Gruppe erhält Daten aus derselben Quelle (Anreicherungsstruktur), ist jedoch vollständig von der Tabelle-Objekt-Datei-Kombination aller anderen Peerprojektionsgruppen isoliert.|
| Verbundenheit | Projektionen unterstützen Zusammenhängende innerhalb der Gruppe. Innerhalb einer Gruppe ist der Inhalt einer Tabelle mit Inhalten in einem Objekt oder einer Datei verknüpft. Typenübergreifend (Tabellen, Objekte und Dateien) in derselben Gruppe werden Beziehungen beibehalten, wenn ein einzelner Knoten einer Anreicherungsstruktur (z. B. `/document/translated_text`) über verschiedene Tabellen und Objekte projiziert wird. Innerhalb von Tabellen basieren Beziehungen auf einem generierten Schlüssel, und jeder untergeordnete Knoten behält einen Verweis auf den übergeordneten Knoten bei. Stellen Sie sich beispielsweise ein Szenario vor, in dem Sie über ein Dokument mit Bildern und Text verfügen. Sie können den Text in Tabellen oder Objekten und die Bilder in Dateien projizieren, wobei die Tabellen oder Objekte über eine Spalte/Eigenschaft verfügen, die die Datei-URL enthält.|

Die Entscheidung, zusätzliche Projektionsgruppen zu erstellen, basiert häufig auf den Anforderungen für die Datendarstellung. Dies können Sie tun, wenn Sie unterschiedliche Datenbeziehungen wünschen. Zwischen den Daten innerhalb eines Satzes bestehen Beziehungen – vorausgesetzt, diese Beziehungen existieren und können erkannt werden. Wenn Sie zusätzliche Sätze erstellen, besteht zwischen den Dokumenten in den einzelnen Gruppen niemals eine Beziehung. Beispiel für die Verwendung mehrerer Projektionsgruppen: Sie möchten dieselbe Datenprojektion für Ihr Onlinesystem verwenden, und die Daten müssen auf eine bestimmte Art und Weise dargestellt werden. Außerdem möchten Sie dieselbe Datenprojektion in einer Data Science-Pipeline verwenden, wobei die Daten auf andere Weise dargestellt werden.

<!-- ## Knowledge Store composition

The knowledge store consists of an annotation cache and projections. The *cache* is used by the service internally to cache the results from skills and track changes. A *projection* defines the schema and structure of the enrichments that match your intended use.

Within Azure Storage, projections can be articulated as tables, objects, or files.

+ As an object, the projection maps to Blob storage, where the projection is saved to a container, within which are the objects or hierarchical representations in JSON for scenarios like a data science pipeline.

+ As a table, the projection maps to Table storage. A tabular representation preserves relationships for scenarios like data analysis or export as data frames for machine learning. The enriched projections can then be easily imported into other data stores. 

You can create multiple projections in a knowledge store to accommodate various constituencies in your organization. A developer might need access to the full JSON representation of an enriched document, while data scientists or analysts might want granular or modular data structures shaped by your skillset.

For instance, if one of the goals of the enrichment process is to also create a dataset used to train a model, projecting the data into the object store would be one way to use the data in your data science pipelines. Alternatively, if you want to create a quick Power BI dashboard based on the enriched documents the tabular projection would work well. -->

## <a name="table-projections"></a>Tabellenprojektionen

Tabellenprojektionen werden für Szenarien empfohlen, für die eine Untersuchung von Daten erforderlich ist, z. B. die Analyse mit Power BI. Die Tabellendefinition ist eine Liste der Tabellen, die projiziert werden sollen. Für jede Tabelle müssen drei Eigenschaften angegeben werden:

+ tableName: Der Name der Tabelle in Azure Table Storage.

+ generatedKeyName: Der Spaltenname für den Schlüssel, mit dem die Zeile eindeutig identifiziert wird.

+ source: Der Knoten in der Anreicherungsstruktur, aus der Sie die Anreicherungen erstellen. Dieser Knoten ist normalerweise die Ausgabe eines Shaper-Skills, der die Form der Tabelle definiert. Tabellen verfügen über Zeilen und Spalten, und die Strukturierung ist der Mechanismus, mit dem Zeilen und Spalten angegeben werden. Sie können einen [Shaper-Skill oder Inlineformen](knowledge-store-projection-shape.md) verwenden. Der Skill „Shaper“ erzeugt gültigen JSON-Code, kann aber die Ausgabe eines beliebigen Skills sein, sofern der JSON-Code gültig ist. 

Wie in diesem Beispiel veranschaulicht, werden die Schlüsselbegriffe und Entitäten in verschiedenen Tabellen modelliert und behalten einen Verweis auf das übergeordnete Element (MainTable) für jede Zeile bei. 

```json
"knowledgeStore": {
  "storageConnectionString": "an Azure storage connection string",
  "projections" : [
    {
      "tables": [
        { "tableName": "MainTable", "generatedKeyName": "SomeId", "source": "/document/EnrichedShape" },
        { "tableName": "KeyPhrases", "generatedKeyName": "KeyPhraseId", "source": "/document/EnrichedShape/*/KeyPhrases/*" },
        { "tableName": "Entities", "generatedKeyName": "EntityId", "source": "/document/EnrichedShape/*/Entities/*" }
      ]
    },
    {
      "objects": [ ]
    },
    {
      "files": [ ]
    }
  ]
}
```

Der in „source“ angegebene Anreicherungsknoten kann in mehrere Tabellen segmentiert werden. Der Verweis auf „EnrichedShape“ ist die Ausgabe eines Shaper-Skills (nicht dargestellt). Die Eingaben der Skills bestimmen die Tabellenkomposition und die Zeilen, die diese füllen. Generierte Schlüssel und gemeinsame Felder innerhalb jeder Tabelle bilden die Grundlage für Tabellenbeziehungen.

## <a name="object-projections"></a>Objektprojektionen

Objektprojektionen sind JSON-Darstellungen der Anreicherungsstruktur, die aus beliebigen Knoten erstellt werden können. In vielen Fällen kann über die Qualifikation Shaper, mit der eine Tabellenprojektion erstellt wird, auch eine Objektprojektion generiert werden. 

Zum Generieren einer Objektprojektion sind einige objektspezifische Attribute erforderlich:

+ storageContainer: Der Blobcontainer, in dem die Objekte gespeichert werden

+ source: Der Pfad zum Knoten der Anreicherungsstruktur, der als Stamm der Projektion dient.

```json
"knowledgeStore": {
  "storageConnectionString": "an Azure storage connection string",
  "projections" : [
    {
      "tables": [ ]
    },
    {
      "objects": [
        {
          "storageContainer": "hotelreviews", 
          "source": "/document/hotel"
        }
      ]
    },
    {
        "files": [ ]
    }
  ]
}
```

## <a name="file-projections"></a>Dateiprojektionen

Dateiprojektionen wirken sich nur auf die `normalized_images`-Sammlung aus, ähneln aber ansonsten Objektprojektionen darin, dass sie in einem Blobcontainer mit einem Ordnerpräfix des Base64-codierten Werts der Dokument-ID gespeichert werden. 

Dateiprojektionen können nicht denselben Container wie Objektprojektionen verwenden und müssen in einen anderen Container projiziert werden. Dateiprojektionen weisen die gleichen Eigenschaften wie Objektprojektionen auf:

+ storageContainer: Der Blobcontainer, in dem die Objekte gespeichert werden

+ source: Der Pfad zum Knoten der Anreicherungsstruktur, der als Stamm der Projektion dient.

```json
"knowledgeStore": {
  "storageConnectionString": "an Azure storage connection string",
  "projections" : [
    {
      "tables": [ ]
    },
    {
      "objects": [ ]
    },
    {
        "files": [
              {
              "storageContainer": "ReviewImages",
              "source": "/document/normalized_images/*"
            }
        ]
    }
  ]
}
```

## <a name="projection-shaping"></a>Projektionsstrukturierung

Projektionen lassen sich einfacher definieren, wenn die Anreicherungsstruktur ein Objekt enthält, das dem Schema der Projektion entspricht, seien es Tabellen oder Objekte. Die Möglichkeit, Ihre Daten basierend auf der geplanten Nutzung zu formen oder zu strukturieren, wird in der Regel durch Hinzufügen eines [Shaper-Skills](cognitive-search-skill-shaper.md) zu Ihrem Skillset erreicht. Von einem Shaper-Skill erzeugte Formen werden als `source` für eine Projektion verwendet, können aber auch als Eingabe für einen anderen Skill verwendet werden.

Einfach ausgedrückt: Bei Tabellenprojektionen bestimmt der Skill „Shaper“ die Spalten oder Felder in Ihren Tabellen. Eingaben für den Skill „Shaper“ bestehen aus Knoten in einer Anreicherungsstruktur. Die Ausgabe ist eine Struktur, die Sie in der Tabellenprojektion angeben. Im folgenden Beispiel besteht eine Tabellenprojektion namens „mytableprojection“ aus den vom Skill „Shaper“ angegebenen Eingaben.

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "name": "ShaperForTables",
    "description": null,
    "context": "/document",
    "inputs": [
        {
            "name": "metadata_storage_path",
            "source": "/document/metadata_storage_path",
            "sourceContext": null,
            "inputs": []
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
          "name": "reviews_username",
          "source": "/document/reviews_username"
        },
    ],
    "outputs": [
      {
        "name": "output",
        "targetName": "mytableprojection"
      }
    ]
}
```

Mit dem aktualisierten Skill „Shaper“ können Sie ein Objekt aus verschiedenen Knoten der Anreicherungsstruktur erstellen und unter einem neuen übergeordneten Knoten festlegen. Mit diesem Skill können Sie zudem komplexe Typen mit geschachtelten Objekten definieren. Beispiele finden Sie in der [Dokumentation zum Skill „Shaper“](cognitive-search-skill-shaper.md).

## <a name="projection-slicing"></a>Projektionsslicing

Innerhalb einer Tabellenprojektionsgruppe kann ein einzelner Knoten in der Anreicherungsstruktur in mehrere verknüpfte Tabellen segmentiert werden, sodass jede Tabelle eine Kategorie von Daten enthält. Dies kann für die Analyse nützlich sein, um zu steuern, ob und wie verknüpfte Daten aggregiert werden.

Um mehrere untergeordnete Tabellen zu erstellen, beginnen Sie mit der übergeordneten Tabelle, und erstellen Sie dann zusätzliche Tabellen, die auf der Quelle des übergeordneten Elements aufbauen. In diesem Beispiel enthalten „KeyPhrases“ und „Entities“ Segmente (Slices) von „/document/EnrichedShape“.

```json
"tables": [
  { "tableName": "MainTable", "generatedKeyName": "SomeId", "source": "/document/EnrichedShape" },
  { "tableName": "KeyPhrases", "generatedKeyName": "KeyPhraseId", "source": "/document/EnrichedShape/*/KeyPhrases/*" },
  { "tableName": "Entities", "generatedKeyName": "EntityId", "source": "/document/EnrichedShape/*/Entities/*" }
]
```

Beim Projizieren in mehreren Tabellen wird die komplette Form in jeder Tabelle projiziert, es sei denn, ein untergeordneter Knoten ist die Quelle einer anderen Tabelle innerhalb derselben Gruppe. Wenn eine Projektion mit einem Quellpfad hinzugefügt wird, der einer vorhandenen Projektion untergeordnet ist, führt dies dazu, dass der untergeordnete Knoten aus dem übergeordneten Knoten herausgeschnitten und in die neue, noch verknüpfte Tabelle bzw. das Objekt projiziert wird. Mit dieser Methode können Sie einen einzelnen Knoten in einem Shaper-Skill als Quelle für all Ihre Projektionen definieren.

## <a name="projection-lifecycle"></a>Lebenszyklus von Projektionen

Der Lebenszyklus Ihrer Projektionen ist an die Quelldaten in der Datenquelle gebunden. Wenn Quelldaten aktualisiert und neu indiziert werden, werden die Projektionen mit den Ergebnissen der Anreicherungen aktualisiert. So wird sichergestellt, dass die Projektionen konsistent mit den Daten in der Datenquelle sind. Projektionen werden jedoch auch unabhängig in Azure Storage gespeichert. Sie werden nicht gelöscht, wenn der Indexer oder der Suchdienst selbst gelöscht wird. 

## <a name="using-projections"></a>Verwenden von Projektionen

Nach dem Ausführen des Indexers können Sie die projizierten Daten in den Containern oder Tabellen lesen, die Sie durch Projektionen angegeben haben.

Für die Analyse erfolgt die Untersuchung in Power BI einfach durch das Festlegen von Azure Table Storage als Datenquelle. Sie können auf einfache Weise eine Reihe von Visualisierungen für Ihre Daten erstellen, indem Sie die darin enthaltenen Beziehungen verwenden.

Wenn Sie die angereicherten Daten in einer Data Science-Pipeline verwenden möchten, können Sie alternativ [die Daten aus Blobs in einen Pandas-Datenrahmen laden](/azure/architecture/data-science-process/explore-data-blob).

Wenn Sie Ihre Daten schließlich aus dem Wissensspeicher exportieren möchten, enthält Azure Data Factory Connectors zum Exportieren der Daten in die gewünschte Datenbank. 

## <a name="next-steps"></a>Nächste Schritte

Erstellen Sie als nächsten Schritt entsprechend den Anweisungen Ihren ersten Wissensspeicher unter Verwendung von Beispieldaten.

> [!div class="nextstepaction"]
> [Erstellen von Wissensspeichern in REST](knowledge-store-create-rest.md).

Erweiterte Konzepte, wie Aufteilen in Slices, Inlinestrukturierung und Beziehungen, finden Sie unter [Definieren von Projektionen in einem Wissensspeicher](knowledge-store-projections-examples.md).
