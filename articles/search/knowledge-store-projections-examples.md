---
title: Definieren von Projektionen
titleSuffix: Azure Cognitive Search
description: Dieser Artikel beschreibt die Syntax zum Definieren von Tabellen-, Objekt- sowie Dateiprojektionen in einem Wissensspeicher und veranschaulicht dies durch Beispiele.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/15/2021
ms.openlocfilehash: 0aa980d2268f6d055f3aa05a8e30d57712c022df
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131563623"
---
# <a name="define-projections-in-a-knowledge-store"></a>Definieren von Projektionen in einem Wissensspeicher

[Projektionen](knowledge-store-projection-overview.md) sind der Teil der [Definition eines Wissensspeichers](knowledge-store-concept-intro.md), der bestimmt, wie durch KI angereicherte Inhalte in Azure Storage gespeichert werden. Projektionen bestimmen den Typ, die Menge und die Zusammensetzung der Datenstrukturen, die Ihre Inhalte enthalten.

In diesem Artikel lernen Sie die Syntax für die einzelnen Projektionstypen kennen:

+ [Tabellenprojektionen](#define-a-table-projection)
+ [Objektprojektionen](#define-an-object-projection)
+ [Dateiprojektionen](#define-a-file-projection)

Projektionen sind unter der Eigenschaft „knowledgeStore“ eines Skillsets definiert.

```json
"knowledgeStore" : {
    "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
    "projections": [
      {
        "tables": [ ],
        "objects": [ ],
        "files": [ ]
      }
    ]
```

Wenn Sie vorm Loslegen weitere Hintergrundinformationen benötigen, finden Sie den Workflow und Tipps dazu in [dieser Checkliste](knowledge-store-projection-overview.md#checklist-for-getting-started).

> [!TIP]
> [Aktivieren Sie die Anreicherungszwischenspeicherung (Vorschau)](search-howto-incremental-index.md) beim Entwickeln von Projektionen, damit Sie vorhandene Anreicherungen wiederverwenden können, während Sie Projektionsdefinitionen bearbeiten.  Dies ist eine Previewfunktion. Achten Sie daher darauf, die Vorschau-REST-API (api-version=2020-06-30-preview oder höher) für die Indexeranforderung zu verwenden. Ohne Zwischenspeicherung führen einfache Änderungen an einer Projektion zu einer vollständigen erneuten Verarbeitung angereicherter Inhalte. Durch Zwischenspeichern der Anreicherungen können Sie Projektionen durchiterieren, ohne dass Kosten für die Verarbeitung von Skillsets anfallen.

## <a name="requirements"></a>Anforderungen

Alle Projektionen verfügen über Quell- und Zieleigenschaften. Die Quelle besteht immer aus Inhalten einer Anreicherungsstruktur, die während der Ausführung des Skillsets erstellt wurde. Das Ziel ist der Name und Typ des Objekts, das in Azure Storage erstellt und geladen wird.

Mit Ausnahme von Dateiprojektionen, die nur binäre Images akzeptieren, muss die Quelle folgende Kriterien erfüllen:

+ Gültige JSON
+ Ein Pfad zu einem Knoten in der Anreicherungsstruktur (z  B. `"source": "/document/objectprojection"`)

Während ein Knoten zu einem einzelnen Feld auflösen kann, kommt die Darstellung als Verweis auf eine komplexe Struktur häufiger vor. Komplexe Strukturen werden mit einer Strukturierungsmethodik erstellt, entweder mit einem [Shaper-Skill](cognitive-search-skill-shaper.md) oder einer [Inline-Strukturdefinition](knowledge-store-projection-shape.md#inline-shape), aber in der Regel mit einem Shaper-Skill.

Shaper-Skills werden bevorzugt, da die meisten Skills nicht selbständig gültigen JSON-Code ausgeben. In vielen Fällen kann die gleiche Datenform, die von einem Shaper-Skill erstellt wurde, gleichermaßen von Tabellen- und Objektprojektionen verwendet werden.

Angesichts der Anforderungen an die Quelleingabe ist es praktisch unabdingbar, zu wissen, wie Daten [strukturiert](knowledge-store-projection-shape.md) werden, insbesondere beim Arbeiten mit Tabellen.

## <a name="define-a-table-projection"></a>Definieren einer Tabellenprojektion

Tabellenprojektionen werden für Szenarien empfohlen, für die das Untersuchen von Daten erforderlich ist, z. B. die Analyse mit Power BI oder Workloads, die Datenrahmen nutzen. Der Tabellenabschnitt eines Projektionsarrays ist eine Liste von Tabellen, die Sie projizieren möchten.

Zum Definieren einer Tabellenprojektion verwenden Sie das Array `tables` in der projections-Eigenschaft. Für Tabellenprojektionen sind drei Eigenschaften erforderlich:

| Eigenschaft | BESCHREIBUNG |
|----------|-------------|
| tableName | Bestimmt den Namen einer neuen Tabelle, die in Azure Table Storage erstellt wird.  |
| generatedKeyName | Spaltenname für den Schlüssel, mit dem alle Zeilen eindeutig identifiziert wird. Der Wert wird vom System generiert. Wenn Sie diese Eigenschaft weglassen, wird automatisch eine Spalte erstellt, für die als Namenskonvention der Tabellenname und -schlüssel verwendet werden. |
| source | Ein Pfad zu einem Knoten in einer Anreicherungsstruktur. Der Knoten sollte ein Verweis auf eine komplexe Struktur sein, die bestimmt, welche Spalten in der Tabelle erstellt werden.|

In Tabellenprojektionen ist „source“ normalerweise die Ausgabe eines [Shaper-Skills](cognitive-search-skill-shaper.md), der die Form der Tabelle definiert. Tabellen verfügen über Zeilen und Spalten, und die Strukturierung ist der Mechanismus, mit dem Zeilen und Spalten angegeben werden. Sie können einen [Shaper-Skill oder Inlineformen](knowledge-store-projection-shape.md) verwenden. Der Shaper-Skill erzeugt gültigen JSON-Code, aber „source“ kann die Ausgabe eines beliebigen Skills sein, sofern es sich um gültigen JSON-Code handelt.

> [!NOTE]
> Für Tabellenprojektionen gelten die von Azure Storage vorgegebenen [Speicherlimits](/rest/api/storageservices/understanding-the-table-service-data-model). Die Entitätsgröße darf 1 MB nicht überschreiten und eine einzelne Eigenschaft darf nicht größer als 64 KB sein. Aufgrund dieser Einschränkungen stellen Tabellen eine gute Lösung für die Speicherung einer großen Anzahl kleiner Entitäten dar.

### <a name="single-table-example"></a>Einzeltabellenbeispiel

Das Schema einer Tabelle wird teilweise durch die Projektion (Tabellenname und Schlüssel) und auch durch die Quelle angegeben, die die Form der Tabelle (Spalten) bereitstellt. Dieses Beispiel zeigt nur eine Tabelle, sodass Sie sich auf die Details der Definition konzentrieren können.

```json
"projections" : [
  {
    "tables": [
      { "tableName": "Hotels", "generatedKeyName": "HotelId", "source": "/document/tableprojection" }
    ]
  }
]
```

Spalten werden von der „source“ abgeleitet. Die folgende Datenstruktur, die HotelId, HotelName, Category und Description enthält, führt zur Erstellung dieser Spalten in der Tabelle.

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "name": "#3",
    "description": null,
    "context": "/document",
    "inputs": [
    {
        "name": "HotelId",
        "source": "/document/HotelId"
    },
    {
        "name": "HotelName",
        "source": "/document/HotelName"
    },
    {
        "name": "Category",
        "source": "/document/Category"
    },
    {
        "name": "Description",
        "source": "/document/Description"
    },
    ],
    "outputs": [
    {
        "name": "output",
        "targetName": "tableprojection"
    }
    ]
}
```

### <a name="multiple-table-slicing-example"></a>Beispiel für mehrere Tabellen (Slicing)

Ein gängiges Muster für Tabellenprojektionen ist die Verwendung mehrerer zugehöriger Tabellen, in denen vom System partitionKey- und rowKey-Spalten generiert werden, um tabellenübergreifende Beziehungen für alle Tabellen in derselben Projektionsgruppe zu unterstützen. 

Das Erstellen mehrerer Tabellen kann nützlich sein, wenn Sie steuern möchten, wie zugehörige Daten aggregiert werden. Wenn angereicherte Inhalte nicht zugehörige oder unabhängige Komponenten enthalten, können Sie diese Felder in angrenzende Tabellen aufteilen, etwa wenn die aus einem Dokument extrahierten Schlüsselwörter nicht zu den Entitäten, die im gleichen Dokument erkannt werden, gehören.

Beim Projizieren in mehreren Tabellen wird die komplette Form in jeder Tabelle projiziert, es sei denn, ein untergeordneter Knoten ist die Quelle einer anderen Tabelle innerhalb derselben Gruppe. Wenn eine Projektion mit einem Quellpfad hinzugefügt wird, der einer vorhandenen Projektion untergeordnet ist, führt dies dazu, dass der untergeordnete Knoten aus dem übergeordneten Knoten herausgeschnitten und in die neue, aber zugehörige Tabelle projiziert wird. Mit dieser Methode können Sie einen einzelnen Knoten in einem Shaper-Skill als Quelle für all Ihre Projektionen definieren.

Das Muster für mehrere Tabellen besteht aus:

+ Einer Tabelle als übergeordnete oder Haupttabelle
+ Zusätzliche Tabellen, die Segmente des angereicherten Inhalts enthalten

Angenommen, ein Shaper-Skill gibt eine „EnrichedShape“ (angereicherte Struktur) aus, die Hotelinformationen sowie angereicherte Inhalte wie Schlüsselbegriffe, Orte und Organisationen enthält. Die Haupttabelle enthält Felder, die das Hotel beschreiben (ID, Name, Beschreibung, Adresse, Kategorie). Für Schlüsselbegriffe wäre die Schlüsselbegriffsspalte vorgesehen. Für Entitäten wäre die Entitätsspalte vorgesehen.

```json
"projections" : [
  {
    "tables": [
    { "tableName": "MainTable", "generatedKeyName": "HotelId", "source": "/document/EnrichedShape" },
    { "tableName": "KeyPhrases", "generatedKeyName": "KeyPhraseId", "source": "/document/EnrichedShape/*/KeyPhrases/*" },
    { "tableName": "Entities", "generatedKeyName": "EntityId", "source": "/document/EnrichedShape/*/Entities/*" }
    ]
  }
]
```

### <a name="naming-relationships"></a>Namensbeziehungen

Mithilfe der Eigenschaften `generatedKeyName` und `referenceKeyName` werden Daten über Tabellen hinweg oder sogar über Projektionstypen hinweg miteinander verknüpft. Jede Zeile in der untergeordneten Tabelle verfügt über eine Eigenschaft, die zurück auf das übergeordnete Element verweist. Der Name der Spalte oder Eigenschaft im untergeordneten Element ist der `referenceKeyName` des übergeordneten Elements. Wenn kein `referenceKeyName` bereitgestellt wird, verwendet der Dienst standardmäßig den `generatedKeyName` des übergeordneten Elements. 

Power BI benötigt diese generierten Schlüssel, um Beziehungen innerhalb der Tabellen zu ermitteln. Wenn Sie die Spalte in der untergeordneten Tabelle anders benennen möchten, legen Sie die `referenceKeyName`-Eigenschaft der übergeordneten Tabelle fest. Ein Beispiel wäre das Festlegen von `generatedKeyName` auf die ID in der tblDocument-Tabelle und von `referenceKeyName` auf die DocumentID. Dies würde dazu führen, dass die Spalte in den Tabellen tblEntities und tblKeyPhrases die Dokument-ID mit dem Namen DocumentID enthält.

## <a name="define-an-object-projection"></a>Definieren einer Objektprojektion

Objektprojektionen sind JSON-Darstellungen der Anreicherungsstruktur, die aus beliebigen Knoten erstellt werden können. Im Vergleich zu Tabellenprojektionen sind Objektprojektionen einfacher zu definieren und werden bei der Projektion ganzer Dokumente verwendet. Objektprojektionen sind auf eine einzelne Projektion in einem Container beschränkt und können nicht aufgeteilt werden.

Zum Definieren einer Objektprojektion verwenden Sie das Array `objects` in der projections-Eigenschaft. Für Objektprojektionen sind drei Eigenschaften erforderlich:

| Eigenschaft | BESCHREIBUNG |
|----------|-------------|
| storageContainer | Bestimmt den Namen eines neuen Containers, der in Azure Storage erstellt wird.  |
| generatedKeyName | Spaltenname für den Schlüssel, mit dem alle Zeilen eindeutig identifiziert wird. Der Wert wird vom System generiert. Wenn Sie diese Eigenschaft weglassen, wird automatisch eine Spalte erstellt, für die als Namenskonvention der Tabellenname und -schlüssel verwendet werden. |
| Quelle | Ein Pfad zu einem Knoten in einer Anreicherungsstruktur, der als Stamm der Projektion dient. Der Knoten ist normalerweise ein Verweis auf eine komplexe Datenstruktur, die die Blobstruktur bestimmt.|

Im folgenden Beispiel werden einzelne Hoteldokumente, ein Hoteldokument pro Blob, in den Container `hotels` projiziert.

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
        "storageContainer": "hotels",
        "source": "/document/objectprojection",
        }
      ]
    },
    {
        "files": [ ]
    }
  ]
}
```

Die Quelle ist die Ausgabe des Shaper-Skills namens „objectprojection“. Jedes Blob enthält eine JSON-Darstellung der einzelnen Feldeingaben.

```json
    {
      "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
      "name": "#3",
      "description": null,
      "context": "/document",
      "inputs": [
        {
          "name": "HotelId",
          "source": "/document/HotelId"
        },
        {
          "name": "HotelName",
          "source": "/document/HotelName"
        },
        {
          "name": "Category",
          "source": "/document/Category"
        },
        {
          "name": "keyPhrases",
          "source": "/document/HotelId/keyphrases/*"
        },
      ],
      "outputs": [
        {
          "name": "output",
          "targetName": "objectprojection"
        }
      ]
    }
```

## <a name="define-a-file-projection"></a>Definieren einer Dateiprojektion

Dateiprojektionen sind immer binäre, normalisierte Bilder, wobei sich die Normalisierung auf potenzielle Größenänderungen und Drehungen bezieht, die bei der Ausführung von Skillsets verwendet werden können. Dateiprojektionen werden ähnlich wie Objektprojektionen als Blobs in Azure Storage erstellt und enthalten Binärdaten (im Gegensatz zu JSON).

Zum Definieren einer Dateiprojektion verwenden Sie das Array `files` in der projections-Eigenschaft. Für Dateiprojektionen sind drei Eigenschaften erforderlich:

| Eigenschaft | BESCHREIBUNG |
|----------|-------------|
| storageContainer | Bestimmt den Namen eines neuen Containers, der in Azure Storage erstellt wird.  |
| generatedKeyName | Spaltenname für den Schlüssel, mit dem alle Zeilen eindeutig identifiziert wird. Der Wert wird vom System generiert. Wenn Sie diese Eigenschaft weglassen, wird automatisch eine Spalte erstellt, für die als Namenskonvention der Tabellenname und -schlüssel verwendet werden. |
| Quelle | Ein Pfad zu einem Knoten in einer Anreicherungsstruktur, der als Stamm der Projektion dient. Bei Bilddateien ist die Quelle immer `/document/normalized_images/*`.  Dateiprojektionen wirken sich nur auf die `normalized_images`-Sammlung aus. Weder Indexer noch ein Skillset geben das ursprüngliche nicht normalisierte Bild weiter.|

Das Ziel ist immer ein Blobcontainer mit einem Ordnerpräfix des Base64-codierten Werts der Dokument-ID. Wenn mehrere Bilder vorhanden sind, werden sie zusammen im selben Ordner platziert. Dateiprojektionen können nicht denselben Container wie Objektprojektionen verwenden und müssen in einen anderen Container projiziert werden. 

Im folgenden Beispiel werden alle normalisierten Bilder, die aus dem Dokumentknoten eines angereicherten Dokuments extrahiert wurden, in den Container `myImages` projiziert.

```json
"projections": [
    {
        "tables": [ ],
        "objects": [ ],
        "files": [
            {
                "storageContainer": "myImages",
                "source": "/document/normalized_images/*"
            }
        ]
    }
]
```

## <a name="test-projections"></a>Testprojektionen

Sie können Projektionen anhand der folgenden Schritte verarbeiten:

1. Legen Sie die `storageConnectionString`-Eigenschaft des Wissensspeichers auf eine gültige Verbindungszeichenfolge für ein Speicherkonto vom Typ „Universell v2“ fest.  

1. [Aktualisieren Sie das Skillset](/rest/api/searchservice/update-skillset), indem Sie eine PUT-Anforderung mit Ihrer Projektionsdefinition im Text des Skillsets ausgeben.

1. [Führen Sie den Indexer](/rest/api/searchservice/run-indexer) aus, um das Skillset in die Ausführung zu versetzen. 

1. [Überwachen Sie Ausführung des Indexers](search-howto-monitor-indexers.md), um über den Fortschritt und etwaige Fehler informiert zu sein.

1. [Verwenden Sie den Speicherbrowser](knowledge-store-view-storage-explorer.md), um die Objekterstellung in Azure Storage zu überprüfen.

1. Wenn Sie Tabellen projiziert haben, [importieren Sie sie in Power BI](knowledge-store-connect-power-bi.md), um Sie zu bearbeiten und zu visualisieren. In den meisten Fällen ermittelt Power BI automatisch die Beziehungen zwischen Tabellen.

## <a name="common-issues"></a>Häufige Probleme

Das Auslassen eines der folgenden Schritte kann zu unerwarteten Ergebnissen führen. Überprüfen Sie die folgenden Bedingungen, wenn Ihre Ausgabe nicht richtig aussieht.

+ Zeichenfolgenanreicherungen sind nicht in gültige JSON-Dateien strukturiert. Wenn Zeichenfolgen angereichert werden (z. B. `merged_content` mit Schlüsselausdrücken), wird die angereicherte Eigenschaft als untergeordnetes Element von `merged_content` innerhalb der Anreicherungsstruktur dargestellt. Die Standarddarstellung ist kein wohlgeformtes JSON-Objekt. Sie müssen zur Projektionszeit sicherstellen, dass sie in ein gültiges JSON-Objekt mit einem Namen und einem Wert transformiert wird. Die Verwendung eines Shaper-Skills oder das Definieren von Inline-Strukturen hilft bei der Behebung dieses Problems.

+ Auslassen von `/*` am Ende eines Quellpfads. Wenn die Quelle einer Projektion z. B. `/document/projectionShape/keyPhrases` ist, wird das Array mit den Schlüsselausdrücken als einzelnes Objekt/einzelne Zeile projiziert. Legen Sie stattdessen den Quellpfad auf `/document/projectionShape/keyPhrases/*` fest, um für jeden Schlüsselausdruck eine einzelne Zeile oder ein einzelnes Objekt zu erzeugen.

+ Pfadsyntaxfehler. Bei [Pfadselektoren](cognitive-search-concept-annotations-syntax.md) wird die Groß-/Kleinschreibung berücksichtigt. Dies kann zu Warnungen über fehlende Eingaben führen, wenn Sie nicht die richtige Schreibung für den Selektor verwenden. 

## <a name="next-steps"></a>Nächste Schritte

Der nächste Schritt führt Sie durch die Strukturierung und Projektion der Ausgabe eines umfangreichen Skillsets. Wenn Ihr Skillset komplex ist, enthält der folgende Artikel Beispiele für Strukturen und Projektionen.

> [!div class="nextstepaction"]
> [Ausführliches Beispiel für Strukturen und Projektionen](knowledge-store-projection-example-long.md)
