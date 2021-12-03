---
title: Event-Grid-Datenverbindung für Azure Synapse Data Explorer (Vorschau)
description: Dieser Artikel bietet eine Übersicht über das Erfassen (Laden) von Daten in Azure Synapse Data Explorer aus Event Grid.
ms.topic: how-to
ms.date: 11/02/2021
author: shsagir
ms.author: shsagir
ms.reviewer: tzgitlin
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.openlocfilehash: 8eb32d529c8024733ba6d0bbdec72cc370ebb5d6
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478615"
---
# <a name="event-grid-data-connection-preview"></a>Event-Grid-Datenverbindung (Vorschau)

Die Event Grid-Erfassung ist eine Pipeline, die auf Azure Storage lauscht und Azure Data Explorer aktualisiert, um Informationen zu pullen, wenn abonnierte Ereignisse auftreten. Data Explorer ermöglicht eine kontinuierliche Erfassung aus dem Azure-Speicher (Blobspeicher und ADLSv2) mit einem [Azure-Event-Grid](/azure/event-grid/overview)-Abonnement für Blob-generierte oder von einem Blob umbenannte Benachrichtigungen und das Streamen dieser Benachrichtigungen an Data Explorer über einen Event Hub.

Die Event Grid-Erfassungspipeline umfasst mehrere Schritte. Sie erstellen eine Zieltabelle in Data Explorer, in der die [Daten in einem bestimmten Format](#data-format) erfasst werden. Anschließend erstellen Sie eine Event-Grid-Datenverbindung in Data Explorer. Die Event Grid-Datenverbindung muss über Informationen zum [Ereignisrouting](#events-routing) verfügen, z. B. die Tabelle, an die Daten gesendet werden sollen, und die Tabellenzuordnung. Außerdem geben Sie [Erfassungseigenschaften](#ingestion-properties) an, die die zu erfassenden Daten, die Zieltabelle und die Zuordnung beschreiben. Sie können Beispieldaten generieren und [Blobs hochladen](#upload-blobs) oder [Blobs umbenennen](#rename-blobs), um Ihre Verbindung zu testen. [Löschen Sie Blobs](#delete-blobs-using-storage-lifecycle) nach der Erfassung. Sie können diesen Prozess auch über das [Azure-Portal](data-explorer-ingest-event-grid-portal.md) verwalten. <!-- , using [one-click ingestion](one-click-ingestion-new-table.md), programmatically with [C#](data-connection-event-grid-csharp.md) or [Python](data-connection-event-grid-python.md), or with the [Azure Resource Manager template](data-connection-event-grid-resource-manager.md). -->

<!-- For general information about data ingestion in Data Explorer, see [Data Explorer data ingestion overview](ingest-data-overview.md). -->

## <a name="data-format"></a>Datenformat

- Siehe [Unterstützte Formate](data-explorer-ingest-data-supported-formats.md).
- Siehe [Unterstützte Komprimierungen](data-explorer-ingest-data-supported-formats.md#supported-data-compression-formats).
    - Die ursprüngliche Größe der nicht komprimierten Daten sollte Teil der Blob-Meta-Daten sein. Andernfalls wird sie von Data Explorer geschätzt. Das Größenlimit für die Erfassung von nicht komprimierten Daten pro Datei ist 4 GB.

> [!NOTE]
> Das Abonnement von Event Grid-Benachrichtigungen kann in Azure Storage-Konten für `BlobStorage`, `StorageV2` oder [Data Lake Storage Gen2](/azure/storage/blobs/data-lake-storage-introduction) festgelegt werden.

## <a name="ingestion-properties"></a>Erfassungseigenschaften

Sie können [Erfassungseigenschaften](data-explorer-ingest-data-properties.md) für die Bloberfassung über die Blobmetadaten angeben.
Sie können die folgenden Eigenschaften festlegen:

[!INCLUDE [ingestion-properties-event-grid](../includes/data-explorer-event-grid-ingestion-properties.md)]

## <a name="events-routing"></a>Ereignisrouting

Beim Einrichten einer Blob-Speicher-Verbindung mit einem Data-Explorer-Cluster geben Sie die Eigenschaften für die Zieltabelle an:

- Tabellenname
- Datenformat
- mapping

Diese Einrichtung ist das Standardrouting für Ihre Daten und wird mitunter auch als `static routing` bezeichnet.
Sie können mithilfe von Blobmetadaten auch Zieltabelleneigenschaften für jeden Blob angeben. Die Daten werden wie mit den [Erfassungseigenschaften](#ingestion-properties) festgelegt dynamisch weitergeleitet.

Das folgende Beispiel zeigt, wie Sie vor dem Hochladen Erfassungseigenschaften für die Blobmetadaten festlegen. Blobs werden an verschiedene Tabellen weitergeleitet.

Weitere Informationen finden Sie unter [Hochladen von Blobs](#upload-blobs).

```csharp
// Blob is dynamically routed to table `Events`, ingested using `EventsMapping` data mapping
blob = container.GetBlockBlobReference(blobName2);
blob.Metadata.Add("rawSizeBytes", "4096‬"); // the uncompressed size is 4096 bytes
blob.Metadata.Add("kustoTable", "Events");
blob.Metadata.Add("kustoDataFormat", "json");
blob.Metadata.Add("kustoIngestionMappingReference", "EventsMapping");
blob.UploadFromFile(jsonCompressedLocalFileName);
```

## <a name="upload-blobs"></a>Hochladen von Blobs

Sie können ein Blob aus einer lokalen Datei erstellen, Erfassungseigenschaften für die Blobmetadaten festlegen und das Blob hochladen. Beispiele finden Sie unter [Erfassen von Blobs in Data Explorer durch das Abonnieren von Event-Grid-Benachrichtigungen](data-explorer-ingest-event-grid-portal.md#generate-sample-data).

> [!NOTE]
> - Verwenden Sie `BlockBlob`, um Daten zu generieren. `AppendBlob` wird nicht unterstützt.
> - Die Verwendung des Azure Data Lake Gen2 Storage SDK erfordert, dass Sie `CreateFile` zum Hochladen von Dateien verwenden sowie am Ende `Flush` ausführen und dabei den Parameter „close“ auf „true“ festlegen.
<!-- > For a detailed example of Data Lake Gen2 SDK correct usage, see [upload file using Azure Data Lake SDK](data-connection-event-grid-csharp.md#upload-file-using-azure-data-lake-sdk). -->
> - Wenn der Empfang eines Ereignisses vom Event Hub-Endpunkt nicht bestätigt wird, wird von Azure Event Grid ein Wiederholungsmechanismus aktiviert. Falls diese Wiederholung der Zustellung nicht erfolgreich ist, kann Event Grid die nicht übermittelten Ereignisse per *Dead-Lettering* (Unzustellbare Nachrichten) an ein Speicherkonto senden. Weitere Informationen finden Sie unter [Event Grid – Nachrichtenübermittlung und -wiederholung](/azure/event-grid/delivery-and-retry#retry-schedule-and-duration).

## <a name="rename-blobs"></a>Umbenennen von Blobs

Wenn Sie ADLSv2 verwenden, können Sie ein Blob umbenennen, um die Bloberfassung in Data Explorer auszulösen. Beispiele finden Sie unter [Erfassen von Blobs in Data Explorer durch das Abonnieren von Event-Grid-Benachrichtigungen](data-explorer-ingest-event-grid-portal.md#generate-sample-data).

> [!NOTE]
> - Das Umbenennen von Verzeichnissen ist in ADLSv2 möglich, löst jedoch keine Ereignisse zur *Umbenennung von Blobs* und keine Erfassung von Blobs innerhalb des Verzeichnisses aus. Wenn Sie Blobs nach dem Umbenennen erfassen möchten, benennen Sie die gewünschten Blobs direkt um.
> - Wenn Sie Filter definiert haben, um bestimmte Themen beim [Erstellen der Datenverbindung](data-explorer-ingest-event-grid-portal.md#create-an-event-grid-data-connection) nachzuverfolgen.<!-- or while creating [Event Grid resources manually](ingest-data-event-grid-manual.md#create-an-event-grid-subscription), these filters are applied on the destination file path. -->

## <a name="delete-blobs-using-storage-lifecycle"></a>Löschen von Blobs mithilfe des Speicherlebenszyklus

Der Data Explorer löscht Blobs nach der Erfassung nicht. Informationen zum Löschen von Blobs finden Sie unter [Azure Blob Storage-Lebenszyklus](/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal). Es wird empfohlen, die Blobs für drei bis fünf Tage beizubehalten.

## <a name="known-event-grid-issues"></a>Bekannte Probleme mit Event Grid

- Beachten Sie Folgendes, wenn Sie Data Explorer zum [Exportieren](/azure/data-explorer/kusto/management/data-export/export-data-to-storage?context=/azure/synapse-analytics/context/context) der Dateien für die Event-Grid-Erfassung verwenden: 
    - Event Grid-Benachrichtigungen werden nicht ausgelöst, wenn die für den Exportbefehl oder für eine [externe Tabelle](/azure/data-explorer/kusto/management/data-export/export-data-to-an-external-table?context=/azure/synapse-analytics/context/context) angegebene Verbindungszeichenfolge eine Verbindungszeichenfolge im [ADLS Gen2-Format](/azure/data-explorer/kusto/api/connection-strings/storage?context=/azure/synapse-analytics/context/context#azure-data-lake-storage-gen2) ist (z. B. `abfss://filesystem@accountname.dfs.core.windows.net`), das Speicherkonto aber nicht für den hierarchischen Namespace aktiviert ist.
    - Wenn das Konto nicht für den hierarchischen Namespace aktiviert ist, muss die Verbindungszeichenfolge dem [Blob Storage](/azure/data-explorer/kusto/api/connection-strings/storage?context=/azure/synapse-analytics/context/context#azure-blob-storage)-Format entsprechen (z. B. `https://accountname.blob.core.windows.net`). Der Export funktioniert auch bei Verwendung der ADLS Gen2-Verbindungszeichenfolge wie erwartet, es werden jedoch keine Benachrichtigungen ausgelöst, und die Event Grid-Erfassung erfolgt nicht.

## <a name="next-steps"></a>Nächste Schritte

- [Erfassen von Blobs in Data Explorer durch das Abonnieren von Event-Grid-Benachrichtigungen](data-explorer-ingest-event-grid-portal.md)
<!-- - [Create an Event Grid data connection for Data Explorer by using C#](data-connection-event-grid-csharp.md)
- [Create an Event Grid data connection for Data Explorer by using Python](data-connection-event-grid-python.md)
- [Create an Event Grid data connection for Data Explorer by using Azure Resource Manager template](data-connection-event-grid-resource-manager.md)
- [Use one-click ingestion to ingest CSV data from a container to a new table in Data Explorer](one-click-ingestion-new-table.md) -->
