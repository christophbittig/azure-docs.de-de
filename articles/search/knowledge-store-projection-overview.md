---
title: Projektionskonzepte
titleSuffix: Azure Cognitive Search
description: Stellt Projektionskonzepte und bewährte Methoden vor. Wenn Sie einen Wissensspeicher in Cognitive Search erstellen, bestimmen Projektionen den Typ, die Menge und die Zusammensetzung von Objekten in Azure Storage.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/15/2021
ms.openlocfilehash: 1b698ae5b03a7d622e4fccb717789be8cb330057
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131561288"
---
# <a name="knowledge-store-projections-in-azure-cognitive-search"></a>Projektionen eines Wissensspeichers in Azure Cognitive Search

Projektionen sind die physischen Tabellen, Objekte und Dateien in einem [**Wissensspeicher**](knowledge-store-concept-intro.md), die Inhalte aus einer KI-Anreicherungspipeline von Cognitive Search akzeptieren. Wenn Sie einen Wissensspeicher erstellen, ist das Definieren und Strukturieren von Projektionen der größte Teil der Arbeit.

In diesem Artikel werden Projektionskonzepte und -workflows vorgestellt, damit Sie vor dem Programmieren über Hintergrundkenntnisse verfügen.

Projektionen werden in Cognitive Search-Skillsets definiert, aber die Endergebnisse sind die Tabellen-, Objekt- und Bilddateiprojektionen in Azure Storage.

:::image type="content" source="media/knowledge-store-concept-intro/kstore-in-storage-explorer.png" alt-text="Projektionen in Azure Storage" border="true":::

## <a name="types-of-projections-and-usage"></a>Arten von Projektionen und deren Verwendung

Ein Wissensspeicher ist eine logische Konstruktion, die physisch lose Sammlung von als Tabellen, JSON-Objekten oder binären Bilddateien in Azure Storage ausgedrückt wird.

| Projektion | Storage | Verwendung |
|------------|---------|-------|
| [Tabellen](knowledge-store-projections-examples.md#define-a-table-projection) | Azure Table Storage | Wird für Daten verwendet, die am besten als Zeilen und Spalten dargestellt werden, oder wenn Sie präzise Darstellungen Ihrer Daten benötigen (z. B. als Datenrahmen). Tabellenprojektionen ermöglichen es Ihnen, eine schematisierte Form zu definieren, indem Sie einen [Shaper-Skill oder die Inlinestrukturierung](knowledge-store-projection-shape.md) verwenden, um Spalten und Zeilen anzugeben. Sie können Inhalte basierend auf vertrauten Normalisierungsprinzipien in mehreren Tabellen organisieren. Tabellen, die sich in derselben Gruppe befinden, werden automatisch verknüpft. |
| [Objekte](knowledge-store-projections-examples.md#define-an-object-projection) | Azure Blob Storage | Wird verwendet, wenn Sie die vollständige JSON-Darstellung Ihrer Daten und Anreicherungen in einem JSON-Dokument benötigen. Wie bei Tabellenprojektionen können nur gültige JSON-Objekte als Objekte projiziert werden, und die Strukturierung kann Ihnen dabei helfen. |
| [Dateien](knowledge-store-projections-examples.md#define-a-file-projection) | Azure Blob Storage | Wird verwendet, wenn Sie normalisierte, binäre Bilddateien speichern müssen. |

## <a name="projection-definition"></a>Projektionsdefinition

Projektionen werden unter der Eigenschaft „knowledgeStore“ eines [Skillsets](/rest/api/searchservice/create-skillset) angegeben. Projektionsdefinitionen werden während des Indexeraufrufs verwendet, um Objekte in Azure Storage mit angereichertem Inhalt zu erstellen und zu laden. Wenn Sie mit diesen Konzepten nicht vertraut sind, beginnen Sie mit [der KI-Anreicherung](cognitive-search-concept-intro.md), um eine Einführung zu erhalten.

Das folgende Beispiel veranschaulicht die Platzierung von Projektionen unter „knowledgeStore“ und die grundlegende Konstruktion. Name, Typ und Inhaltsquelle bilden eine Projektionsdefinition.

```json
"knowledgeStore" : {
    "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<Acct Name>;AccountKey=<Acct Key>;",
    "projections": [
      {
        "tables": [
          { "tableName": "ks-museums-main", "generatedKeyName": "ID", "source": "/document/tableprojection" },
          { "tableName": "ks-museumEntities", "generatedKeyName": "ID","source": "/document/tableprojection/Entities/*" }
        ],
        "objects": [
          { "storageContainer": "ks-museums", "generatedKeyName": "ID", "source": "/document/objectprojection" }
        ],
        "files": [ ]
      }
    ]
```

## <a name="projection-groups"></a>Projektionsgruppen

Projektionen sind ein Array komplexer Sammlungen. Das bedeutet, dass Sie mehrere Sätze jedes Typs festlegen können. Es ist üblich, nur eine Projektionsgruppe zu verwenden, aber Sie können mehrere verwenden, wenn Speicheranforderungen die Unterstützung verschiedener Tools und Szenarien umfassen. Sie können beispielsweise eine Gruppe für den Entwurf und das Debuggen eines Skillsets verwenden, während ein zweiter Satz Ausgaben für eine Online-App sammelt und ein dritter Satz für Data Science-Workloads verwendet wird.

Die gleiche Skillsetausgabe wird verwendet, um alle Gruppen unter Projektionen aufzufüllen. Das folgende Beispiel veranschaulicht zwei Vorgehensweisen.

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

Projektionsgruppen weisen die folgenden Hauptmerkmale für gegenseitige Ausschließlichkeit und Verbundenheit auf. 

| Prinzip | BESCHREIBUNG |
|-----------|-------------|
| Gegenseitige Ausschließlichkeit | Jede Gruppe ist vollständig von anderen Gruppen isoliert, um verschiedene Datenstrukturierungsszenarien zu unterstützen. Wenn Sie beispielsweise unterschiedliche Tabellenstrukturen und -kombinationen testen, würden Sie jeden Satz in einer anderen Projektionsgruppe für AB-Tests platzieren. Jede Gruppe erhält Daten aus derselben Quelle (Anreicherungsstruktur), ist jedoch vollständig von der Tabelle-Objekt-Datei-Kombination aller anderen Peerprojektionsgruppen isoliert.|
| Verbundenheit | Innerhalb einer Projektionsgruppe sind Inhalte in Tabellen, Objekten und Dateien verknüpft. Der Wissensspeicher verwendet generierte Schlüssel als Verweispunkte auf einen gemeinsamen übergeordneten Knoten. Stellen Sie sich beispielsweise ein Szenario vor, in dem Sie über ein Dokument mit Bildern und Text verfügen. Sie können den Text in Tabellen und die Bilder in Binärdateien projizieren, und sowohl Tabellen als auch Objekte verfügen über eine Spalte/Eigenschaft, die die Datei-URL enthält.|

## <a name="projection-source"></a>„Quelle“ der Projektion

Der Quellparameter ist die dritte Komponente einer Projektionsdefinition. Da Projektionen Daten aus einer KI-Anreicherungspipeline speichern, ist die Quelle einer Projektion immer die Ausgabe eines Skills. Daher kann die Ausgabe ein einzelnes Feld sein (z. B. ein Feld mit übersetzten Text), ist aber häufig ein Verweis auf eine Datenform.

Datenformen stammen aus Ihrem Skillset. Unter allen integrierten Skills, die in Cognitive Search bereitgestellt werden, gibt es einen Hilfsskill namens [**Shaper**](cognitive-search-skill-shaper.md), der zum Erstellen von Datenformen verwendet wird. Sie können (beliebig viele) Shaper-Skills hinzufügen, um die Projektionen im Wissensspeicher zu unterstützen.

Formen werden häufig mit Tabellenprojektionen verwendet, wobei die Form nicht nur angibt, welche Zeilen in die Tabelle aufgenommen werden, sondern auch, welche Spalten erstellt werden (Sie können auch eine Form an eine Objektprojektion übergeben).

Formen können komplex sein, und es würde den Rahmen sprengen, sie hier eingehend zu erörtern, aber das folgende Beispiel veranschaulicht kurz eine einfache Form. Die Ausgabe des Shaper-Skills wird als Quelle einer Tabellenprojektion angegeben. Innerhalb der Tabellenprojektion selbst befinden sich Spalten für „metadata-storage_path“, „reviews_text“, „reviews_title“ usw., wie in der Form angegeben.

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

## <a name="projection-lifecycle"></a>Lebenszyklus von Projektionen

Der Lebenszyklus Ihrer Projektionen ist an die Quelldaten in der Datenquelle gebunden. Wenn Quelldaten aktualisiert und neu indiziert werden, werden die Projektionen mit den Ergebnissen der Anreicherungen aktualisiert. So wird sichergestellt, dass die Projektionen konsistent mit den Daten in der Datenquelle sind. Projektionen werden jedoch auch unabhängig in Azure Storage gespeichert. Sie werden nicht gelöscht, wenn der Indexer oder der Suchdienst selbst gelöscht wird. 

## <a name="consume-in-apps"></a>Nutzung in Apps

Nachdem der Indexer ausgeführt wurde, stellen Sie eine Verbindung zu Projektionen her, und nutzen Sie die Daten in anderen Apps und Workloads.

+ Verwenden Sie den [Storage-Browser](knowledge-store-view-storage-explorer.md), um die Objekterstellung und den Inhalt zu überprüfen.

+ Verwenden Sie [Power BI für die Datenuntersuchung](knowledge-store-connect-power-bi.md). Dieses Tool funktioniert am besten, wenn sich die Daten in Azure Table Storage befinden. Innerhalb von Power BI können Sie Daten in neuen Tabellen bearbeiten, die einfacher abzufragen und zu analysieren sind.

+ Verwenden Sie angereicherte Daten im Blobcontainer in einer Data Science-Pipeline. Beispielsweise können Sie die [Daten aus Blobs in einen Pandas-Datenrahmen laden](/azure/architecture/data-science-process/explore-data-blob).

+ Wenn Sie Ihre Daten schließlich aus dem Wissensspeicher exportieren möchten, enthält Azure Data Factory Connectors zum Exportieren der Daten in die gewünschte Datenbank.

## <a name="checklist-for-getting-started"></a>Checkliste für die ersten Schritte

Denken Sie daran, dass Projektionen ausschließlich für Wissensspeicher gelten und nicht zum Strukturieren eines Suchindexes verwendet werden.

1. Rufen Sie in Azure Storage eine Verbindungszeichenfolge von **Zugriffsschlüsseln** ab, und vergewissern Sie sich, dass das Konto vom Typ „StorageV2 (Universell V2)“ ist.

1. Machen Sie sich in Azure Storage mit vorhandenen Inhalten in Containern und Tabellen vertraut, sodass Sie für die Projektionen Namen auswählen, die nicht zueinander in Konflikt stehen. Ein Wissensspeicher ist eine lose Sammlung von Tabellen und Containern. Erwägen Sie, verwandte Objekte unter Verwendung einer Benennungskonvention nachzuverfolgen.

1. In Cognitive Search sollten Sie im Indexer die [Anreicherungszwischenspeicherung (Vorschau) aktivieren](search-howto-incremental-index.md) und dann den [Indexer ausführen](search-howto-run-reset-indexers.md), um das Skillset auszuführen und den Cache aufzufüllen. Dies ist eine Previewfunktion. Achten Sie daher darauf, die Vorschau-REST-API (api-version=2020-06-30-preview oder höher) für die Indexeranforderung zu verwenden. Nachdem der Cache aufgefüllt wurde, können Sie Projektionsdefinitionen in einem Wissensspeicher kostenlos ändern (solange die Skills selbst nicht geändert werden).

1. In Ihrem Code werden alle Projektionen ausschließlich in einem Skillset definiert. Es gibt keine Indexereigenschaften (z. B. Feldzuordnungen oder Ausgabefeldzuordnungen), die für Projektionen gelten. Innerhalb einer Skillsetdefinition konzentrieren Sie sich auf zwei Bereiche: knowledgeStore-Eigenschaft und Skills-Array.

   1. Geben Sie unter „knowledgeStore“ die Tabellen-, Objekt- und Dateiprojektionen im Abschnitt `projections` an. Objekttyp, Objektname und Menge (gemäß der Anzahl der von Ihnen definierten Projektionen) werden in diesem Abschnitt bestimmt.

   1. Bestimmen Sie anhand des Skills-Arrays, auf welche Skillausgaben in der `source` jeder Projektion verwiesen wird. Alle Projektionen verfügen über eine Quelle. Die Quelle kann die Ausgabe eines Upstream-Skills sein, ist jedoch häufig die Ausgabe eines Shaper-Skills. Die Zusammensetzung Ihrer Projektion wird durch Formen bestimmt. 

1. Wenn Sie Projektionen zu einem vorhandenen Skillset hinzufügen, [aktualisieren Sie das Skillset](/rest/api/searchservice/update-skillset), und [führen Sie den Indexer aus](/rest/api/searchservice/run-indexer).

1. Überprüfen Sie ihre Ergebnisse in Azure Storage. Vermeiden Sie bei nachfolgenden Ausführungen Namenskonflikte, indem Sie Objekte in Azure Storage löschen oder Projektnamen im Skillset ändern.

## <a name="next-steps"></a>Nächste Schritte

Überprüfen der Syntax und Beispiele für jeden Projektionstyp.

> [!div class="nextstepaction"]
> [Definieren von Projektionen in einem Wissensspeicher](knowledge-store-projections-examples.md)