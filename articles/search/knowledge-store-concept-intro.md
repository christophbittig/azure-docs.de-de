---
title: 'Wissensspeicher: Konzepte'
titleSuffix: Azure Cognitive Search
description: Senden Sie angereicherte Dokumente an Azure Storage, um sie in Azure Cognitive Search sowie in anderen Anwendungen anzeigen, umstrukturieren und nutzen zu können.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/02/2021
ms.openlocfilehash: 659e625aa98a92c2730e76bd847dfdddc3c955bf
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131577325"
---
# <a name="knowledge-store-in-azure-cognitive-search"></a>Wissensspeicher in Azure Cognitive Search

Ein Wissensspeicher ist eine von einer Cognitive Search-[KI-Anreicherungspipeline](cognitive-search-concept-intro.md) erstellte Datensenke und speichert angereicherte Inhalte in Tabellen und Blobcontainern in Azure Storage für unabhängige Analysen oder Downstreamverarbeitung in Nicht-Suchszenarien wie Knowledge Mining.

Wenn Sie in der Vergangenheit kognitive Skills verwendet haben, wissen Sie bereits, dass *Skillsets* ein Dokument durch eine Sequenz von Anreicherungen verschieben, die atomische Transformationen aufrufen, z. B. das Erkennen von Entitäten oder das Übersetzen von Text. Bei der Ausgabe kann es sich um einen Suchindex oder um Projektionen in einem Wissensspeicher handeln. Die beiden Ausgaben in Form des Suchindexes und Wissensspeichers schließen sich gegenseitig aus und werden durch dieselbe Pipeline erzeugt. Sie werden von denselben Eingaben abgeleitet, ergeben jedoch eine Ausgabe, die strukturiert, gespeichert und in unterschiedlichen Anwendungen verwendet wird.

:::image type="content" source="media/knowledge-store-concept-intro/knowledge-store-concept-intro.svg" alt-text="Pipeline mit Skillset" border="false":::

Physisch betrachtet handelt es sich bei einem Wissensspeicher um [Azure Storage](../storage/common/storage-account-overview.md) (Azure Table Storage, Azure Blob Storage oder beides). Jedes Tool und jeder Prozess, das bzw. der eine Verbindung mit Azure Storage herstellen kann, kann die Inhalte eines Wissensspeichers nutzen.

In der Anzeige von Speicherbrowser sieht ein Wissensspeicher wie jede andere Sammlung von Tabellen, Objekten oder Dateien aus. Das folgende Beispiel zeigt einen Wissensspeicher, der aus drei Tabellen mit Feldern besteht, die entweder aus der Datenquelle übertragen oder durch Anreicherungen erstellt werden (siehe „Standpunktbewertung“ und „translated_text“).

:::image type="content" source="media/knowledge-store-concept-intro/kstore-in-storage-explorer.png" alt-text="Skills lesen aus der und schreiben in die Anreicherungsstruktur" border="true":::

## <a name="benefits-of-knowledge-store"></a>Vorteile von Wissensspeichern

Die Hauptvorteile eines Wissensspeichers sind der flexible Zugriff auf Inhalte und die Fähigkeit, Daten zu strukturieren.

Im Gegensatz zu einem Suchindex, auf den nur über Abfragen in Cognitive Search zugegriffen werden kann, kann von jedem Tool, jeder App oder jedem Prozess, der Verbindungen zu Azure Storage unterstützt, auf einen Wissensspeicher zugegriffen werden. Diese Flexibilität eröffnet neue Szenarien für die Nutzung der analysierten und angereicherten Inhalte, die von einer Anreicherungspipeline erzeugt werden.

Das gleiche Skillset, das Daten anreichert, kann auch zum Strukturieren von Daten verwendet werden. Einige Tools wie Power BI funktionieren besser mit Tabellen, während eine Data Science-Workload möglicherweise eine komplexe Datenstruktur in einem Blobformat erfordert. Das Hinzufügen einer [Shaper-Skill](cognitive-search-skill-shaper.md) zu einem Skillset gibt Ihnen die Kontrolle über die Strukturierung Ihrer Daten. Sie können diese Strukturierungen dann an Projektionen übergeben, bei denen es sich entweder um Tabellen oder Blobs handelt, um physische Datenstrukturen zu erstellen, die der beabsichtigten Verwendung der Daten entsprechen.

Im folgenden Video werden diese und weitere Vorteile erläutert.

> [!VIDEO https://www.youtube.com/embed/XWzLBP8iWqg?version=3]

## <a name="knowledge-store-definition"></a>Definition des Wissensspeichers

Ein Wissensspeicher wird in einer Skillsetdefinition definiert und besteht aus zwei Komponenten: 

+ einer Verbindungszeichenfolge zu Azure Storage

+ [**Projektionen**](knowledge-store-projection-overview.md), die bestimmen, ob der Wissensspeicher aus Tabellen, Objekten oder Dateien besteht. 

Das Projektionselement ist ein Array. Sie können mehrere Sätze von Tabellenobjekt-Datei-Kombinationen innerhalb eines Wissensspeichers erstellen.

```json
"knowledgeStore": {
   "storageConnectionString":"<YOUR-AZURE-STORAGE-ACCOUNT-CONNECTION-STRING>",
   "projections":[
      {
         "tables":[ ],
         "objects":[ ],
         "files":[ ]
      }
   }
```

Der Typ der Projektion, den Sie in dieser Struktur angeben, bestimmt den vom Wissensspeicher verwendeten Speichertyp.

+ `tables`-Objekte projizieren angereicherte Inhalte in Table Storage. Definieren Sie eine Tabellenprojektion, wenn Sie tabellarische Berichtsstrukturen für Eingaben in Analysetools benötigen oder als Datenrahmen in andere Datenspeicher exportieren müssen. Sie können mehrere `tables`-Objekte in derselben Projektionsgruppe angeben, um eine Teilmenge oder einen Querschnitt angereicherter Dokumente zu erhalten. Tabellenbeziehungen innerhalb derselben Projektionsgruppe bleiben erhalten, sodass Sie mit allen arbeiten können.

  Projizierter Inhalt wird nicht aggregiert oder normalisiert. Der folgende Screenshot zeigt eine nach Schlüsselbegriffen sortierte Tabelle mit dem übergeordneten Dokument, das in der angrenzenden Spalte angegeben ist. Im Gegensatz zur Datenerfassung während der Indizierung gibt es keine linguistische Analyse oder Aggregation von Inhalten. Pluralformen und Unterschiede bei der Groß-/Kleinschreibung werden als eindeutige Instanzen betrachtet.

  :::image type="content" source="media/kstore-keyphrases-per-document.png" alt-text="Screenshot: Schlüsselbegriffe und Dokumente in einer Tabelle" border="true":::

+ `objects`-Objekte projizieren JSON-Dokumente in Blob Storage. Die physische Darstellung eines `object` ist eine hierarchische JSON-Struktur, die ein angereichertes Dokument darstellt.

+ `files`-Objekte projizieren Bilddateien in Blob Storage. Bei einer `file` handelt es sich um ein aus einem Dokument extrahiertes Bild, das intakt in Blob Storage übertragen wurde. Obwohl die Datei den Namen "files" hat, wird sie im Blob-Storage und nicht im Dateispeicher angezeigt.

## <a name="create-a-knowledge-store"></a>Erstellen von Wissensspeichern

Verwenden Sie das Portal oder eine API, um einen Wissensspeicher zu erstellen. Sie benötigen [Azure Storage](../storage/index.yml), ein [Skillset](cognitive-search-working-with-skillsets.md) und einen [Indexer](search-indexer-overview.md). Da Indexer einen Suchindex benötigen, müssen Sie auch eine Indexdefinition bereitstellen.

Nutzen Sie den Portalansatz, um die schnellste Route zu einem fertigen Wissensspeicher zu erstellen. Oder wählen Sie die REST-API aus, um ein besseres Verständnis darüber zu erhalten, wie Objekte definiert und miteinander verbunden sind.

### <a name="azure-portal"></a>[**Azure-Portal**](#tab/kstore-portal)

[**Erstellen Sie Ihren ersten Wissensspeicher in vier Schritten**](knowledge-store-create-portal.md) mit dem **Datenimport-Assistenten**.

1. Definieren Ihres Datenschemas

1. Definieren Sie Ihr Skillset, und geben Sie einen Wissensspeicher an.

1. Erstellen Sie ein Indexschema. Der Assistent benötigt das Schema und kann eines für Sie ableiten.

1. Führen Sie den Assistenten aus. Die Extraktion, Anreicherung und Speicherung erfolgen in diesem letzten Schritt.

Der Assistent automatisiert Aufgaben, die Sie andernfalls manuell bearbeiten müssten. Vor allem Strukturierung sowie Projektionen (Definitionen physischer Datenstrukturen in Azure Storage) werden für Sie erstellt. 

### <a name="rest"></a>[**REST**](#tab/kstore-rest)

[**Erstellen Ihres ersten Wissensspeichers mithilfe von Postman**](knowledge-store-create-rest.md) ist ein Tutorial, das Sie durch die Objekte und Anforderungen dieser [Wissensspeichersammlung](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/knowledge-store) führt.

Die REST-API-Version `2020-06-30` kann verwendet werden, um einen Wissensspeicher durch Ergänzungen zu einem Skillset zu erstellen.

+ [Create Skillset (api-version=2020-06-30)](/rest/api/searchservice/create-skillset)
+ [Update Skillset (api-version=2020-06-30)](/rest/api/searchservice/update-skillset)

Innerhalb des Skillsets:

+ Geben Sie die Projektionen an, die sie in Azure Storage integrieren möchten (Tabellen, Objekte, Dateien).
+ Fügen Sie Ihrem Skillset einen Shaper-Skill hinzu, um das Schema und den Inhalt der Projektion zu bestimmen.
+ Weisen Sie die benannte Form einer Projektion zu.

### <a name="net-sdk"></a>[**.NET SDK**](#tab/kstore-dotnet)

.NET-Entwickler: Verwenden Sie die [KnowledgeStore-Klasse](/dotnet/api/azure.search.documents.indexes.models.knowledgestore) in der Azure.Search.DocUments-Clientbibliothek.

---

<a name="tools-and-apps"></a>

## <a name="connect-with-apps"></a>Herstellen einer Verbindung mit Apps

Nachdem die Anreicherungen in Storage gespeichert sind, können Sie beliebige Tools oder Technologien mit einer Verbindung mit Azure Blob Storage oder Azure Table Storage verwenden, um die Inhalte zu untersuchen, zu analysieren oder zu nutzen. Die folgende Liste gibt einen ersten Einblick:

+ Mit dem [Speicherbrowser](knowledge-store-view-storage-explorer.md) können Sie die Struktur und den Inhalt angereicherter Dokumente anzeigen. Nutzen Sie dies als Ihr Basistool zum Anzeigen des Inhalts von Wissensspeichern.

+ Nutzen Sie [Power BI](knowledge-store-connect-power-bi.md) für die Berichterstellung und Analyse. 

+ Verwenden Sie [Azure Data Factory](../data-factory/index.yml) für die weiterführende Bearbeitung.

## <a name="content-lifecycle"></a>Inhaltslebenszyklus

Bei jeder Ausführung des Indexers und des Skillsets wird der Wissensspeicher aktualisiert, wenn sich das Skillset oder die zugrunde liegenden Quelldaten geändert haben. Alle vom Indexer übernommenen Änderungen werden über den Anreicherungsprozess an die Projektionen im Wissensspeicher weitergegeben, um sicherzustellen, dass Ihre projizierten Daten eine aktuelle Darstellung des Inhalts in der ursprünglichen Datenquelle sind. 

> [!Note]
> Während Sie die Daten in den Projektionen bearbeiten können, werden alle Änderungen beim nächsten Pipelineaufruf überschrieben, vorausgesetzt, das Dokument in den Quelldaten wird aktualisiert. 

### <a name="changes-in-source-data"></a>Änderungen an Quelldaten

Für Datenquellen, die die Änderungsnachverfolgung unterstützen, verarbeitet ein Indexer neue und geänderte Dokumente und umgeht vorhandene Dokumente, die bereits verarbeitet wurden. Zeitstempelinformationen variieren je nach Datenquelle, aber in einem Blobcontainer untersucht der Indexer das `lastmodified`-Datum, um zu bestimmen, welche Blobs erfasst werden müssen.

### <a name="changes-to-a-skillset"></a>Änderungen an einem Skillset

Wenn Sie Änderungen an einem Skillset vornehmen, sollten Sie das [Zwischenspeichern angereicherter Dokumente aktivieren](cognitive-search-incremental-indexing-conceptual.md), um vorhandene Anreicherungen nach Möglichkeit wiederzuverwenden.

Ohne inkrementelles Zwischenspeichern verarbeitet der Indexer Dokumente immer in der Reihenfolge des hohen Grenzwerts, ohne rückwärts zu gehen. Blobs verarbeitet der Indexer nach `lastModified` sortiert, unabhängig von Änderungen an den Indexereinstellungen oder am Skillset. Wenn Sie ein Skillset ändern, werden zuvor verarbeitete Dokumente nicht aktualisiert, um das neue Skillset widerzuspiegeln. Dokumente, die nach der Änderung des Skillsets verarbeitet werden, verwenden das neue Skillset, was dazu führt, dass Indexdokumente eine Mischung aus alten und neuen Skillsets sind.

Beim inkrementellen Zwischenspeichern und nach einer Aktualisierung des Skillsets verwendet der Indexer alle Anreicherungen, die vom Skillset nicht betroffen sind. Upstreamanreicherungen werden aus dem Cache abgerufen, ebenso wie alle Anreicherungen, die unabhängig und von der geänderten Qualifikation isoliert sind.

### <a name="deletions"></a>Löschungen

Ein Indexer kann zwar Strukturen und Inhalte in Azure Storage erstellen und aktualisieren, aber nicht löschen. Projektionen sind auch dann weiterhin vorhanden, wenn Indexer oder Skillset gelöscht wird. Als Besitzer des Speicherkontos sollten Sie eine Projektion löschen, wenn sie nicht mehr benötigt wird. 

## <a name="next-steps"></a>Nächste Schritte

Ein Wissensspeicher ermöglicht die Aufbewahrung angereicherter Dokumente, was bei der Entwicklung eines Skillsets hilfreich ist, sowie die Erstellung neuer Strukturen und Inhalte zur Nutzung durch beliebige Clientanwendungen, die auf ein Azure Storage-Konto zugreifen können.

Am einfachsten lassen sich angereicherte Dokumente über das [Portal](knowledge-store-create-portal.md) erstellen. Sie können aber auch Postman und die REST-API verwenden. Dies empfiehlt sich, wenn Sie nachvollziehen möchten, wie Objekte programmseitig erstellt und referenziert werden.

> [!div class="nextstepaction"]
> [Erstellen eines Wissensspeichers mithilfe von Postman und REST](knowledge-store-create-rest.md)
