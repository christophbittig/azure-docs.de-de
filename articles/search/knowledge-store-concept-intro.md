---
title: 'Wissensspeicher: Konzepte'
titleSuffix: Azure Cognitive Search
description: Senden Sie angereicherte Dokumente an Azure Storage, um sie in Azure Cognitive Search sowie in anderen Anwendungen anzeigen, umstrukturieren und nutzen zu können.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/10/2021
ms.openlocfilehash: baa4a78e23b83fa7c138e71b92dba12ba23a3ab6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122339520"
---
# <a name="knowledge-store-in-azure-cognitive-search"></a>Wissensspeicher in Azure Cognitive Search

Der Wissensspeicher ist ein Feature von Azure Cognitive Search, das Ausgaben aus einer [KI-Anreicherungspipeline](cognitive-search-concept-intro.md) in Azure Storage zur unabhängigen Analyse oder Downstreamverarbeitung speichert. Ein *angereichertes Dokument* ist eine Pipelineausgabe, die auf der Grundlage von Inhalten erstellt wurde, die mithilfe von KI-Prozessen extrahiert, strukturiert und analysiert wurden. In einer KI-Standardpipeline sind angereicherte Dokumente kurzlebig: Sie werden nur während der Indizierung verwendet und anschließend verworfen. Beim Erstellen eines Wissensspeichers werden die angereicherten Dokumente zur Überprüfung oder für andere Knowledge Mining-Szenarien beibehalten.

Wenn Sie in der Vergangenheit kognitive Fähigkeiten genutzt haben, wissen Sie bereits, dass Dokumente mithilfe von *Skillsets* auf verschiedene Weisen angereichert werden. Bei der Ausgabe kann es sich um einen Suchindex oder um Projektionen in einem Wissensspeicher handeln. Die beiden Ausgaben in Form des Suchindexes und Wissensspeichers werden durch dieselbe Pipeline erzeugt. Sie werden von denselben Eingaben abgeleitet, resultieren jedoch in einer Ausgabe, die strukturiert, gespeichert und in unterschiedlichen Anwendungen verwendet wird.

:::image type="content" source="media/knowledge-store-concept-intro/knowledge-store-concept-intro.svg" alt-text="Pipeline mit Skillset" border="false":::

<!-- previous version of the architecture diagram
:::image type="content" source="media/knowledge-store-concept-intro/annotationstore_sans_internalcache.png" alt-text="Knowledge store in pipeline diagram" border="false"::: -->

Physisch betrachtet handelt es sich bei einem Wissensspeicher um [Azure Storage](../storage/common/storage-account-overview.md) (Azure Table Storage, Azure Blob Storage oder beides). Jedes Tool und jeder Prozess, das bzw. der eine Verbindung mit Azure Storage herstellen kann, kann die Inhalte eines Wissensspeichers nutzen.

Über Storage-Explorer betrachtet, ist ein Wissensspeicher nur eine Sammlung von Tabellen, Objekten oder Dateien. Das folgende Beispiel zeigt einen Wissensspeicher, der aus drei Tabellen mit Feldern besteht, die entweder aus der Datenquelle übertragen oder durch Anreicherungen erstellt werden (siehe „Standpunktbewertung“ und „translated_text“).

:::image type="content" source="media/knowledge-store-concept-intro/kstore-in-storage-explorer.png" alt-text="Skills lesen aus der und schreiben in die Anreicherungsstruktur" border="true":::

## <a name="benefits-of-knowledge-store"></a>Vorteile von Wissensspeichern

Ein Wissensspeicher enthält die Struktur, den Kontext und den eigentlichen Inhalt. Diese Informationen werden aus unstrukturierten und teilweise strukturierten Datendateien wie Blobs und analysierten Bilddateien oder sogar aus strukturierten Daten gewonnen, die umgeformt wurden. In einer [ausführlichen exemplarischen Vorgehensweise](knowledge-store-create-rest.md) können Sie genau sehen, wie ein komplexes JSON-Dokument in Unterstrukturen partitioniert, in neue Strukturen zusammengesetzt und für nachfolgende Prozesse wie Machine Learning- und Data Science-Workloads verfügbar gemacht wird.

Auch wenn es nützlich ist, zu sehen, was eine KI-Anreicherungspipeline erzeugen kann, liegt das eigentliche Potenzial von Wissensspeichern in der Möglichkeit, Daten neu zu strukturieren. Sie können mit einem grundlegenden Skillset beginnen und dieses wiederholt durchlaufen, um weitere Strukturebenen hinzuzufügen. Diese können dann zu neuen Strukturen kombiniert werden, die sowohl in Azure Cognitive Search als auch in anderen Apps verwendet werden können.

Wissensspeicher bieten u. a. folgende Vorteile:

+ Nutzen Sie angereicherte Dokumente auch in anderen [Analyse- und Berichtstools](#tools-and-apps) als Search. Power BI mit Power Query ist eine hervorragende Wahl, allerdings sind auch alle Tools oder Apps, die eine Verbindung mit Azure Storage herstellen können, in der Lage, Daten aus einem von Ihnen erstellten Wissensspeicher abzurufen.

+ Optimieren Sie eine KI-basierte Indizierungspipeline während der Debuggingschritte und der Definition von Qualifikationen. Ein Wissensspeicher zeigt das Produkt einer Qualifikationsgruppendefinition in einer KI-Indizierungspipeline. Sie können diese Ergebnisse verwenden, um eine bessere Qualifikationsgruppe zu entwerfen, da Sie genau sehen können, wie die Anreicherungen aussehen. Verwenden Sie den [Storage-Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows) in Azure Storage, um die Inhalte eines Wissensspeichers anzuzeigen.

+ Formen Sie die Daten um. Die Umstrukturierung ist in Skillsets codiert, aber der [Skill „Shaper“](cognitive-search-skill-shaper.md) in Azure Cognitive Search bietet explizite Kontrolle und die Möglichkeit, mehrere Formen zu erstellen. Die Strukturierung ermöglicht Ihnen die Definition einer Projektion für die beabsichtigte Verwendung der Daten unter Beibehaltung der Beziehungen.

> [!VIDEO https://www.youtube.com/embed/XWzLBP8iWqg?version=3]

## <a name="knowledge-store-definition"></a>Definition des Wissensspeichers

Während der Indexerausführung werden Wissensspeicher in Ihrem [Azure Storage-Konto](../storage/common/storage-account-overview.md) erstellt, indem Azure Table Storage, Azure Blob Storage oder beides verwendet wird. 

Die Datenstrukturen innerhalb von Azure Storage werden durch das `projections`-Element einer `knowledgeStore`-Definition in einem Skillset festgelegt. [Projektionen](knowledge-store-projection-overview.md) können als Tabellen, Objekte oder Dateien formuliert werden, und Sie können über mehrere Sätze (oder *Projektionsgruppen*) verfügen und mehrere Datenstrukturen für unterschiedliche Zwecke erstellen.

```json
"knowledgeStore":{
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

+ `objects`-Objekte projizieren JSON-Dokumente in Blob Storage. Die physische Darstellung eines `object` ist eine hierarchische JSON-Struktur, die ein angereichertes Dokument darstellt.

+ `files`-Objekte projizieren Bilddateien in Blob Storage. Bei einer `file` handelt es sich um ein aus einem Dokument extrahiertes Bild, das intakt in Blob Storage übertragen wurde.

## <a name="create-a-knowledge-store"></a>Erstellen von Wissensspeichern

Verwenden Sie das Portal oder eine API, um einen Wissensspeicher zu erstellen. Sie benötigen [Azure Storage](../storage/index.yml), ein [Skillset](cognitive-search-working-with-skillsets.md) und einen [Indexer](search-indexer-overview.md). Da Indexer einen Suchindex benötigen, müssen Sie auch eine Indexdefinition bereitstellen.

### <a name="azure-portal"></a>[**Azure-Portal**](#tab/kstore-portal)

[Erstellen Sie Ihren ersten Wissensspeicher in vier Schritten](knowledge-store-connect-power-bi.md) mit dem **Datenimport-Assistenten**. In diesem Assistenten werden die folgenden Tasks behandelt:

1. Wählen Sie eine unterstützte Datenquelle aus, die den Rohdateninhalt bereitstellt.

1. Legen Sie die Anreicherung fest: Fügen Sie eine Cognitive Services-Ressource an, wählen Sie Qualifikationen aus, und geben Sie einen Wissensspeicher an. In diesem Schritt wählen Sie ein Azure Storage-Konto aus und entscheiden, ob Sie Objekte, Tabellen oder beides erstellen möchten.

1. Erstellen Sie ein Indexschema. Der Assistent benötigt das Schema und kann eines für Sie ableiten.

1. Führen Sie den Assistenten aus. Die Extraktion, Anreicherung und Speicherung erfolgen in diesem letzten Schritt.

Wenn Sie den Assistenten verwenden, werden mehrere zusätzliche Tasks intern verarbeitet, die Sie andernfalls im Code behandeln müssten. Sowohl Strukturierung als auch Projektionen (Definitionen physischer Datenstrukturen in Azure Storage) werden für Sie erstellt. Wenn Sie einen Wissensspeicher manuell erstellen, muss Ihr Code diese Schritte übernehmen.

### <a name="rest"></a>[**REST**](#tab/kstore-rest)

Die REST-API-Version `2020-06-30` kann verwendet werden, um einen Wissensspeicher durch Ergänzungen zu einem Skillset zu erstellen.

+ [Create Skillset (api-version=2020-06-30)](/rest/api/searchservice/create-skillset)
+ [Update Skillset (api-version=2020-06-30)](/rest/api/searchservice/update-skillset)

`knowledgeStore` wird innerhalb eines [Skillsets](cognitive-search-working-with-skillsets.md) definiert, das wiederum von einem [Indexer](search-indexer-overview.md) aufgerufen wird. Während der Anreicherung wird von Azure Cognitive Search ein Bereich in Ihrem Azure Storage-Konto erstellt. Anschließend werden die angereicherten Dokumente je nach Konfiguration als Blobs oder in Tabellen projiziert.

Tasks, die ihr Code verarbeiten muss:

+ Geben Sie die Projektionen an, die sie in Azure Storage integrieren möchten (Tabellen, Objekte, Dateien).
+ Fügen Sie Ihrem Skillset einen Shaper-Skill hinzu, um das Schema und den Inhalt der Projektion zu bestimmen.
+ Weisen Sie die benannte Form einer Projektion zu.

Um sich auf einfache Weise damit vertraut zu machen, [erstellen Sie Ihren ersten Wissensspeicher mithilfe von Postman](knowledge-store-create-rest.md).

### <a name="net-sdk"></a>[**.NET SDK**](#tab/kstore-dotnet)

.NET-Entwickler: Verwenden Sie die [KnowledgeStore-Klasse](/dotnet/api/azure.search.documents.indexes.models.knowledgestore) in der Azure.Search.DocUments-Clientbibliothek.

---

<a name="tools-and-apps"></a>

## <a name="connect-with-apps"></a>Herstellen einer Verbindung mit Apps

Nachdem die Anreicherungen in Storage gespeichert sind, können Sie beliebige Tools oder Technologien mit einer Verbindung mit Azure Blob Storage oder Azure Table Storage verwenden, um die Inhalte zu untersuchen, zu analysieren oder zu nutzen. Die folgende Liste gibt einen ersten Einblick:

+ Mit [Storage-Explorer](knowledge-store-view-storage-explorer.md) können Sie die Struktur und den Inhalt angereicherter Dokumente anzeigen. Nutzen Sie dies als Ihr Basistool zum Anzeigen des Inhalts von Wissensspeichern.

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

Oder sehen Sie sich [Projektionen](knowledge-store-projection-overview.md) einmal genauer an. Eine exemplarische Vorgehensweise anhand eines Beispiels, das erweiterte Projektionskonzepte wie Aufteilen in Slices, Inlinestrukturierung und Beziehungen behandelt, finden Sie unter [Definieren von Projektionen in einem Wissensspeicher](knowledge-store-projections-examples.md).
