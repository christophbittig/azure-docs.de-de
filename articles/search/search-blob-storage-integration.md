---
title: Durchsuchen von Azure Blob Storage-Inhalten
titleSuffix: Azure Cognitive Search
description: In diesem Artikel erfahren Sie mehr über das Extrahieren von Text aus Azure-Blobs sowie darüber, wie diesen in einem Azure Cognitive Search-Index über die Volltextsuche suchbar machen.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/14/2021
ms.openlocfilehash: 14d6aedf282819e8fcd7c5f03ff182a991954607
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129279163"
---
# <a name="search-over-azure-blob-storage-content"></a>Durchsuchen von Azure Blob Storage-Inhalten

Das Durchsuchen verschiedenster in Azure Blob Storage gespeicherter Inhalte kann ein schwierig zu lösendes Problem sein. In diesem Artikel wird der grundlegende Workflow für die Extraktion von Inhalt und Metadaten aus Blobs sowie das Senden derselben an einen Suchindex in Azure Cognitive Search erläutert. Der resultierende Index kann über die Volltextsuche abgefragt werden.

> [!NOTE]
> Sie sind bereits mit dem Workflow und der Struktur vertraut? [Konfigurieren eines Blobindexers](search-howto-indexing-azure-blob-storage.md) ist Ihr nächster Schritt.

## <a name="what-it-means-to-add-full-text-search-to-blob-data"></a>Bedeutung des Hinzufügens der Volltextsuche zu Blobdaten

Azure Cognitive Search ist ein Suchdienst, der die Indizierung und Abfrage von Workloads mithilfe von benutzerdefinierten Indizes unterstützt, die Ihre durchsuchbaren, in der Cloud gehosteten Inhalte enthalten. Die Zusammenstellung Ihrer durchsuchbaren Inhalte mit der Abfrage-Engine ist aus Leistungsgründen erforderlich, damit Ergebnisse mit einer Geschwindigkeit zurückgegeben werden, die Benutzer von Suchabfragen erwarten.

Cognitive Search und Azure Blob Storage sind auf Indizierungsebene miteinander integriert. Dabei werden Ihre Blobinhalte als Suchdokumente importiert, die in *invertierte Indizes* und andere Suchstrukturen indiziert werden, die Textabfragen in freier Form sowie Filterausdrücke unterstützen. Da Ihre Blobinhalte in einem Suchindex indiziert werden, können Sie die gesamte Bandbreite der Abfragefeatures von Azure Cognitive Search nutzen, um Informationen in Ihrem Blobinhalt zu finden.

Als Eingaben fungieren Ihre Blobs in einem einzelnen Container in Azure Blob Storage. Blobs können aus nahezu beliebigen Arten von Textdaten bestehen. Wenn Ihre Blobs Bilder enthalten, können Sie der [Blobindizierung KI-Anreicherung hinzufügen](search-blob-ai-integration.md), um Text zu erstellen und aus Bildern zu extrahieren.

Die Ausgabe ist stets ein Azure Cognitive Search-Index, der für schnelles Suchen, Abrufen und Erkunden von Texten in Clientanwendungen genutzt wird. Dazwischen befindet sich die Architektur der Indizierungspipeline selbst. Die Pipeline basiert auf der *Indexer*-Funktion, die im weiteren Verlauf dieses Artikels erläutert wird.

Nach Erstellen und Auffüllen des Indexes existiert er unabhängig von Ihrem Blobcontainer. Sie können jedoch Indizierungsvorgänge noch einmal ausführen, um Ihren Index auf Grundlage von geänderten Dokumenten zu aktualisieren. Zeitstempelinformationen zu einzelnen Blobs werden zur Erkennung von Änderungen verwendet. Sie können sich entweder für die geplante Ausführung oder die bedarfsgesteuerte Indizierung als Aktualisierungsmechanismus entscheiden.

## <a name="required-resources"></a>Erforderliche Ressourcen

Sie benötigen sowohl Azure Cognitive Search als auch Azure Blob Storage. In Azure Blob Storage benötigen Sie einen Container, der Quellinhalte bereitstellt.

Sie können direkt auf der Portalseite des Speicherkontos beginnen. Wählen Sie auf der linken Navigationsseite unter **Blob-Dienst** die Option **Azure Cognitive Search hinzufügen** aus, um einen neuen Dienst zu erstellen oder einen bestehenden auszuwählen. 

Nachdem Sie Ihrem Speicherkonto Azure Cognitive Search hinzugefügt haben, können Sie den Standardprozess zum Indizieren von Blobdaten befolgen. Wir empfehlen für eine einfache erste Eingabe von Daten den Assistenten **Daten importieren** in Azure Cognitive Search. Oder rufen Sie die REST-APIs mit einem Tool wie Postman auf. Dieses Tutorial führt Sie durch die Schritte zum Aufrufen der Rest-API in Postman: [Indizieren und Durchsuchen von teilweise strukturierten Daten (JSON-Blobs) in Azure Cognitive Search](search-semi-structured-data.md). 

## <a name="use-a-blob-indexer"></a>Verwenden eines Blobindexers

Ein *Indexer* ist ein datenquellenabhängiger Subdienst in Cognitive Search, der eine interne Logik für das Datensampling, das Lesen von Metadaten, das Abrufen von Daten und das Serialisieren von Daten aus nativen Formaten in JSON-Dokumente zum anschließenden Import aufweist. 

Blobs in Azure Storage werden mithilfe des [Blob Storage-Indexers von Azure Cognitive Search](search-howto-indexing-azure-blob-storage.md) indiziert. Sie können diesen Indexer mit dem Assistenten **Daten importieren**, mit einer REST-API oder dem .NET SDK aufrufen. Im Code verwenden Sie diesen Indexer, indem Sie den Typ festlegen und Verbindungsinformationen bereitstellen, die ein Azure-Speicherkonto zusammen mit einem Blobcontainer enthalten. Sie können Ihre Blobs unterteilen, indem Sie ein virtuelles Verzeichnis erstellen, das Sie dann als Parameter übergeben können, oder indem Sie nach einer Dateityperweiterung filtern.

Ein Indexer [„entschlüsselt das Dokument“](search-indexer-overview.md#document-cracking) und öffnet ein Blob, um den Inhalt zu untersuchen. Nach dem Herstellen einer Verbindung mit der Datenquelle ist dies der erste Schritt in der Pipeline. Bei Blobdaten werden an dieser Stelle PDF-Dateien, Office-Dokumente und andere Inhaltstypen erkannt. Die Dokumententschlüsselung mit Textextraktion erfolgt gebührenfrei. Wenn Ihre Blobs Bildinhalte enthalten, werden Bilder ignoriert, es sei denn, Sie fügen [KI-Erweiterungen](search-blob-ai-integration.md) hinzu. Die Standardindizierung gilt nur für Textinhalte.

Der Blobindexer verfügt über Konfigurationsparameter und unterstützt die Nachverfolgung von Änderungen, wenn die zugrunde liegenden Daten genügend Informationen liefern. Weitere Informationen zur Kernfunktionalität finden Sie unter [Blob Storage-Indexer in Azure Cognitive Search](search-howto-indexing-azure-blob-storage.md).

### <a name="supported-access-tiers"></a>Unterstützte Zugriffsebenen

Die Blob Storage-[Zugriffsebenen](../storage/blobs/access-tiers-overview.md) lauten „Heiß“, „Kalt“ und „Archiv“. Indexer können nur auf „Heiß“ und „Kalt“ zugreifen. 

### <a name="supported-content-types"></a>Unterstützte Inhaltstypen

Wenn Sie einen Blobindexer auf einen Container anwenden, können Sie mit einer einzigen Abfrage aus den folgenden Inhaltstypen Text und Metadaten extrahieren:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

### <a name="indexing-blob-metadata"></a>Indizieren der Metadaten von Blobs

Das Indizieren von benutzerdefinierten Metadaten sowie von Systemeigenschaften für jedes Blob ist ein häufig auftretendes Szenario, in dem Sie Blobs jedes Inhaltstyps unkompliziert durchsehen können. Auf diese Weise werden die Informationen für alle Blobs unabhängig vom Dokumenttyp indiziert und in einem Index in Ihrem Suchdienst gespeichert. Mit dem neuen Index können Sie dann den gesamten Blob Storage-Inhalt sortieren und filtern sowie Facetten bilden.

> [!NOTE]
> Blobindextags werden vom Blob Storage-Dienst nativ indiziert und für Abfragen verfügbar gemacht. Wenn die Schlüssel-Wert-Attribute des Blob Indizierungs- und Filterungsfunktionen erfordern, sollten Blobindextags anstelle von Metadaten genutzt werden.
>
> Weitere Informationen über den Blobindex finden Sie unter [Verwalten und Suchen von Daten in Azure Blob Storage mit dem Blobindex](../storage/blobs/storage-manage-find-blobs.md).

### <a name="indexing-json-blobs"></a>Indizieren von JSON-Blobs

Indexer können so konfiguriert werden, dass sie strukturierten Inhalt aus Blobs extrahieren, die eine JSON-Datei enthalten. Ein Indexer kann aus JSON-Blobs lesen und den strukturierten Inhalt analysieren, um ihn den entsprechenden Feldern in einem Suchdokument zuzuweisen. Indexer können außerdem Blobs annehmen, die ein Array von JSON-Objekten enthalten und jedes Element mit einem separaten Suchdokument verknüpfen. Sie können einen Analysemodus festlegen, der den Typ des vom Indexer erzeugten JSON-Objekts beeinflusst.

## <a name="search-blob-content-in-a-search-index"></a>Durchsuchen von Blobinhalten in einem Suchindex 

Die Ausgabe einer Indizierung ist ein Suchindex, der für die interaktive Untersuchung mithilfe von Freitextabfragen und gefilterten Abfragen in einer Client-App verwendet wird. Für die erste Erkundung und Überprüfung von Inhalten empfehlen wir, im Portal im [Suchexplorer](search-explorer.md) die Dokumentstruktur zu untersuchen. Sie können im Suchexplorer die [einfache Abfragesyntax](query-simple-syntax.md), [vollständige Abfragesyntax](query-lucene-syntax.md) und [Filterausdruckssyntax](query-odata-filter-orderby-syntax.md) verwenden.

Eine dauerhaftere Lösung besteht darin, Abfrageeingaben zu sammeln und die Antwort als Suchergebnisse in einer Clientanwendung darzustellen. Im folgenden C#-Tutorial wird das Erstellen einer Suchanwendung erläutert: [Erstellen Ihrer ersten Anwendung in Azure Cognitive Search](tutorial-csharp-create-first-app.md).

## <a name="next-steps"></a>Nächste Schritte

+ [Hochladen, Herunterladen und Auflisten von Blobs mit dem Azure-Portal (Azure Blob Storage)](../storage/blobs/storage-quickstart-blobs-portal.md)
+ [Einrichten eines Blobindexers (Azure Cognitive Search)](search-howto-indexing-azure-blob-storage.md)
