---
title: Aufrüsten von Azure Blob Storage auf Azure Data Lake Storage Gen2 | Microsoft-Dokumentation
description: Beschreibung hier einfügen.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 10/12/2021
ms.author: normesta
ms.openlocfilehash: 70f191a33eeb4d09ea8e4f14b17cd6ceadcec76e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131003578"
---
# <a name="upgrading-azure-blob-storage-with-azure-data-lake-storage-gen2-capabilities"></a>Aufrüstung von Azure Blob Storage mit Azure Data Lake Storage Gen2-Funktionen

Dieser Artikel hilft Ihnen, Funktionen wie Sicherheit auf Datei- und Verzeichnisebene und schnellere Vorgänge aufzuschließen. Diese Funktionen werden häufig von großen Datenanalyse-Workloads verwendet und werden als Azure Data Lake Storage Gen2 bezeichnet. Die beliebtesten Funktionen umfassen:

- Höherer Durchsatz, Eingabe/Ausgabe-Vorgänge pro Sekunde (IOPS) und Speicherkapazitätsgrenzen.

- Schnellere Vorgänge (z. B. Umbenennungsvorgänge), da Sie mit einzelnen Knoten-URIs arbeiten können.
 
- Effiziente Abfrage-Engine, die nur die Daten überträgt, die für die Durchführung eines bestimmten Vorgangs erforderlich sind.

- Sicherheit auf der Container-, Verzeichnis- und Dateiebene.

Um mehr darüber zu lernen, siehe [Einführung in Azure Data Lake Storage Gen2](data-lake-storage-introduction.md).

Dieser Artikel hilft Ihnen, die Auswirkungen auf die Arbeitsauslastung, Anwendungen, Kosten, Dienstintegrationen, Tools, Funktionen und Dokumentation auszuwerten. Stellen Sie sicher, dass Sie diese Auswirkungen sorgfältig überprüfen. Wenn Sie soweit sind, ein Konto zu aktualisieren, lesen Sie diese Schritt-für-Schritt-Anleitung: [Upgrade von Azure Blob Storage mit Azure Data Lake Storage Gen2 Funktionen](upgrade-to-data-lake-storage-gen2-how-to.md).

> [!IMPORTANT]
> Ein Upgrade ist eine unidirektionale Aktion. Nachdem Sie das Upgrade durchgeführt haben, gibt es keine Möglichkeit, Ihr Konto wiederherzustellen. Wir empfehlen Ihnen, das Upgrade in einer Nichtproduktionsumgebung zu testen.

## <a name="impact-on-availability"></a>Auswirkungen auf die Verfügbarkeit

Planen Sie eine gewisse Ausfallzeit für Ihr Konto ein, während der der Upgrade-Prozess abgeschlossen wird. Schreibvorgänge sind während des Upgrades Ihres Kontos deaktiviert. Lesevorgänge sind nicht deaktiviert, aber wir empfehlen dringend, dass Sie Lesevorgänge anhalten, da diese den Upgrade-Prozess destabilisieren könnten.

## <a name="impact-on-workloads-and-applications"></a>Auswirkungen auf Workloads und Anwendungen

Blob-APIs arbeiten mit Konten, die einen hierarchischen Namespace haben. Daher funktionieren die meisten Anwendungen, die mit Ihrem Konto unter Verwendung dieser APIs interagieren, weiterhin ohne Änderungen.

Eine vollständige Liste der Probleme und Problemumgehungen finden Sie unter [Bekannte Probleme mit Blob-Storage-APIs](data-lake-storage-known-issues.md#blob-storage-apis).

Alle Hadoop-Workloads, die den Windows Azure Storage Blob-Treiber oder den [WASB](https://hadoop.apache.org/docs/current/hadoop-azure/index.html)-Treiber verwenden, müssen so geändert werden, dass sie den [Azure Blob File System (ABFS)](https://hadoop.apache.org/docs/stable/hadoop-azure/abfs.html)-Treiber verwenden. Anders als der WASB-Treiber, der Anforderungen an den Endpunkt des **Blob-Dienstes** stellt, stellt der ABFS-Treiber Anforderungen an den **Data Lake Storage**-Endpunkt Ihres Kontos.

### <a name="data-lake-storage-endpoint"></a>Data Lake Storage-Endpunkt

Ihr aktualisiertes Konto verfügt über einen Data Lake Storage-Endpunkt. Sie können die URL dieses Endpunkts im Azure-Portal finden, indem Sie die Seite **Eigenschaften** Ihres Kontos öffnen.

> [!div class="mx-imgBorder"]
> ![Universell v2 Kategorie](./media/upgrade-to-data-lake-storage-gen2/data-lake-storage-endpoint.png)

Sie müssen Ihre vorhandenen Anwendungen und Workloads nicht ändern, um diesen Endpunkt zu verwenden. [Der Multiprotokollzugriff in Data Lake Storage](data-lake-storage-multi-protocol-access.md) ermöglicht es Ihnen, entweder den Endpunkt des Blob-Dienstes oder den Data Lake Storage-Endpunkt zu verwenden, um mit Ihren Daten zu interagieren. 

Azure-Dienste und -Tools (z. B. AzCopy) können den Data Lake Storage-Endpunkt für die Interaktion mit den Daten in Ihrem Speicherkonto verwenden. Außerdem müssen Sie diesen neuen Endpunkt für alle Vorgänge verwenden, die Sie unter Verwendung der [Data Lake Storage Gen2 SDKs](data-lake-storage-directory-file-acl-dotnet.md), [ PowerShell-Befehle](data-lake-storage-directory-file-acl-powershell.md) oder [Azure CLI-Befehle](data-lake-storage-directory-file-acl-cli.md) ausführen. 

### <a name="directories"></a>Verzeichnisse

Ein Blob-Speicherkonto, das keinen hierarchischen Namespace hat, organisiert Dateien in einem flachen Paradigma und nicht in einem hierarchischen Paradigma. Blobs werden in virtuellen Verzeichnissen organisiert, um eine Ordnerstruktur zu imitieren. Ein virtuelles Verzeichnis bildet einen Teil des Blobnamens und wird durch das Trennzeichen angezeigt. Denn ein virtuelles Verzeichnis ist ein Teil des Blob-Namens: Es existiert nicht als eigenständiges Objekt.

Ihr neues Konto hat einen hierarchischen Namespace. Das bedeutet, dass Verzeichnisse nicht virtuell sind. Sie sind konkrete, unabhängige Objekte, mit denen Sie direkt arbeiten können. Ein Verzeichnis kann existieren, ohne Dateien zu enthalten. Wenn Sie ein Verzeichnis löschen, werden alle Dateien in diesem Verzeichnis entfernt. Sie müssen nicht mehr jedes einzelne Blob löschen, bevor das Verzeichnis verschwindet. 

### <a name="blob-metadata"></a>Blobmetadaten

Vor der Migration sind die Blob-Metadaten mit dem Namen des Blobs und seinem gesamten virtuellen Pfad verknüpft. Nach der Migration sind die Metadaten nur noch mit dem Blob verknüpft. Der virtuelle Pfad zum Blob wird zu einer Sammlung von Verzeichnissen. Die Metadaten eines Blobs werden nicht auf eines dieser Verzeichnisse angewendet. 

### <a name="put-operations"></a>Put-Vorgänge

Wenn Sie einen Blob hochladen und der angegebene Pfad ein Verzeichnis umfasst, das noch nicht existiert, wird dieses Verzeichnis erstellt und ein Blob hinzugefügt. Dieses Verhalten ist im Zusammenhang mit einer hierarchischen Ordnerstruktur folgerichtig. In einem Blob-Speicherkonto, das keinen hierarchischen Namespace hat, wird bei diesem Vorgang kein Verzeichnis erstellt. Stattdessen wird der Verzeichnisname dem Namespace des Blobs hinzugefügt. 

### <a name="list-operations"></a>Auflisten von Vorgängen

Der Vorgang [Blobs listen](/rest/api/storageservices/list-blobs) gibt sowohl Verzeichnisse als auch Dateien zurück. Jedes wird separat aufgelistet. Verzeichnisse erscheinen in der Liste als Blobs mit der Länge 0. In einem Blob-Speicherkonto ohne hierarchischen Namespace gibt ein [Blobs listen](/rest/api/storageservices/list-blobs)-Vorgang nur Blobs und keine Verzeichnisse zurück. Wenn Sie den Vorgang "Data Lake Storage Gen2 [Pfad listen](/rest/api/storageservices/datalakestoragegen2/path/list)“ verwenden, werden Verzeichnisse als Verzeichniseinträge und nicht als Blobs mit Länge 0 angezeigt.

Auch die Reihenfolge der Listen ist anders. Verzeichnisse und Dateien werden in der Reihenfolge des *tiefenorientierten Lastenausgleichs* angezeigt. Ein Blob-Speicherkonto, das keinen hierarchischen Namespace hat, listet Blobs in *lexikographischer* Reihenfolge auf. 

### <a name="operations-to-rename-blobs"></a>Vorgänge zum Umbenennen von Blobs

Das Umbenennen eines Blobs ist wesentlich effizienter, da Clientanwendungen ein Blob in einem einzigen Vorgang umbenennen können. In Konten, die *keinen* hierarchischen Namespace haben, müssen Tools und Anwendungen ein Blob kopieren und dann das Quell-Blob löschen.  

> [!NOTE]
> Wenn Sie ein Blob umbenennen, wird die letzte Änderungszeit des Blob nicht aktualisiert. Das liegt daran, dass der Inhalt des Blobs unverändert bleibt. 

## <a name="impact-on-costs"></a>Auswirkungen auf die Kosten

Die Speicherkosten sind nicht betroffen, wohl aber die Transaktionskosten. Benutzen Sie diese Seiten, um einen Kostenvergleich anzustellen. 

- [Blockblob Preise](https://azure.microsoft.com/pricing/details/storage/blobs/).

- [Azure Data Lake Storage Gen2 Preise](https://azure.microsoft.com/pricing/details/storage/data-lake/).

Sie können auch die Option **Speicherkonten** im [Azure-Preisrechner](https://azure.microsoft.com/pricing/calculator/) verwenden, um die Auswirkungen der Kosten nach einem Upgrade abzuschätzen. 

Abgesehen von den Preisänderungen sollten Sie auch die Kosteneinsparungen berücksichtigen, die mit den Data Lake Storage Gen2-Funktionen verbunden sind. Die Gesamtkosten von dem Eigentum sinken in der Regel aufgrund eines höheren Durchsatzes und optimierter Vorgänge. Ein höherer Durchsatz aktiviert die Übertragung von mehr Daten in kürzerer Zeit. Ein hierarchischer Namespace verbessert die Effizienz der Vorgänge.  

## <a name="impact-on-service-integrations"></a>Auswirkungen auf Dienstintegrationen

Während die meisten Integrationen von Azure-Diensten weiterhin funktionieren, nachdem Sie diese Funktionen aktiviert haben, befinden sich einige von ihnen noch in der Vorschauversion oder werden noch nicht unterstützt. Siehe [Azure-Dienste, die Azure Data Lake Storage Gen2 unterstützen](data-lake-storage-supported-azure-services.md), um die aktuelle Unterstützung für Azure-Dienstintegrationen mit Data Lake Storage Gen2 zu verstehen.

## <a name="impact-on-tools-features-and-documentation"></a>Auswirkungen auf Tools, Funktionen und Dokumentation

Nach dem Upgrade wird sich die Art und Weise der Interaktion mit einigen Funktionen ändern. Dieser Abschnitt beschreibt diese Änderungen.

### <a name="blob-storage-feature-support"></a>Unterstützung von Blob Storage

Während die meisten Blob-Storage-Funktionen weiterhin funktionieren, nachdem Sie diese Funktionen aktiviert haben, werden einige von ihnen weiterhin in der Vorschauversion oder noch nicht unterstützt. 

Siehe [in Azure Data Lake Storage Gen2 verfügbare Blob Storage Funktionen](./storage-feature-support-in-storage-accounts.md), um die aktuelle Unterstützung für Blob Storage Funktionen mit Data Lake Storage Gen2 zu verstehen. 

### <a name="diagnostic-logs"></a>Diagnoseprotokolle

Wenn Sie [Speicher Analysen Protokollierung](../common/storage-analytics-logging.md) aktivieren, haben Sie jetzt die Möglichkeit, das Protokollformat der Version 2.0 zu verwenden.  

Sie brauchen diese neue Version nicht zu verwenden. Allerdings werden alle Vorgänge, die auf den Data Lake-Speicherendpunkt angewendet werden, nur in Protokollen der Version 2.0 aufgezeichnet. Einige Dienste und Tools, die Sie verwenden (z. B. AzCopy), nutzen diesen Endpunkt, um Vorgänge in Ihrem Konto auszuführen. Um sicherzustellen, dass Sie die Protokollierungsinformationen aller Aktivitäten erfassen, sollten Sie das Protokollformat der Version 2.0 verwenden. 

### <a name="azure-lifecycle-management"></a>Azure-Lebenszyklusverwaltung

Richtlinien, die alle Blobs in einem Verzeichnis verschieben oder löschen, löschen das Verzeichnis, das diese Blobs enthält, erst am nächsten Tag. Das liegt daran, dass das Verzeichnis erst dann gelöscht werden kann, wenn alle Blobs, die sich in diesem Verzeichnis befinden, entfernt wurden. Am nächsten Tag wird das Verzeichnis dann gelöscht. 

### <a name="event-grid"></a>Event Grid

Ihr neues Konto verfügt über zwei Endpunkte: den Endpunkt für den Data Lake-Speicher und den Endpunkt für den Blob-Dienst. Dienste, Tools und Anwendungen können von beiden Endpunkten Gebrauch machen, um mit Ihren Daten zu arbeiten. Folglich kann eine Ereignisantwort, die vom Event Grid zurückgegeben wird, einen dieser beiden Endpunkte im Feld **URL** anzeigen, das den betroffenen Blob beschreibt. 

Das folgende JSON zeigt die URL eines Blob, die in der Ereignisantwort erscheint, wenn ein Blob unter Verwendung des Endpunkts des Blob-Dienstes erstellt wird.

:::code language="json" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ConfigurationFiles/change-feed-logs.json" ID="Snippet_BlobURL" highlight="15":::

Die folgende JSON-Datei zeigt die URL eines Blobs, die in der Ereignisantwort angezeigt wird, wenn ein Blob unter Verwendung des Data Lake-Speicherendpunkts erstellt wird.

:::code language="json" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ConfigurationFiles/change-feed-logs.json" ID="Snippet_DataLakeURL" highlight="16":::

Wenn Ihre Anwendungen das Event Grid verwenden, müssen Sie diese Anwendungen möglicherweise ändern, um dies zu berücksichtigen. 

### <a name="storage-explorer"></a>Storage-Explorer

Die folgenden Schaltflächen werden noch nicht im Menüband des Azure Storage Explorer angezeigt.

|Schaltfläche|Grund|
|--|--|
|Kopieren der URL|Noch nicht implementiert|
|Verwalten von Momentaufnahmen|Noch nicht implementiert|
|Wiederherstellen|Abhängig von Blob-Speicherfunktionen, die noch nicht von Data Lake Storage Gen2 unterstützt werden |


Die folgenden Schaltflächen verhalten sich in Ihrem neuen Konto anders.

|Schaltfläche|Blob-Speicher-Verhalten|Data Lake Storage Gen2-Verhalten|
|---|---|---|
|Ordner|Der Ordner ist virtuell und verschwindet, wenn Sie ihm keine Dateien hinzufügen. |Der Ordner existiert auch, wenn ihm keine Dateien hinzugefügt werden.| 
|Umbenennen|Führt zu einer Kopie und anschließend zum Löschen des Quell-Blob|Benennt denselben Blob um. Weitaus effizienter.|

### <a name="documentation"></a>Dokumentation

Eine Anleitung zum Gebrauch der Data Lake Storage Gen2-Funktionen finden Sie hier: [Einführung in Azure Data Lake Storage Gen2](data-lake-storage-introduction.md). 

Es hat sich nichts daran geändert, wo Sie die Anleitung für alle bestehenden Blob-Speicherfunktionen finden. Diese Anleitung finden Sie hier: [Einführung in Azure Blob-Speicher](storage-blobs-introduction.md). 

Wenn Sie zwischen den Inhaltsmengen wechseln, werden Sie einige leichte terminologische Unterschiede feststellen. In den Inhalten von Data Lake Storage Gen2 werden zum Beispiel die Begriffe *Datei* und *Dateisystem* anstelle von *Blob* und  *Container* verwendet. Die Begriffe *Datei* und *Dateisystem* sind tief in der Welt von großer Datenanalyse verwurzelt, in der Data Lake Storage eine lange Geschichte hat. Der Inhalt enthält diese Begriffe, um den Bezug zu diesen Zielgruppen aufrechtzuerhalten. Diese Begriffe beschreiben keine separaten *Dinge*.

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie bereit sind, Ihr Speicherkonto zu aktualisieren, um Data Lake Storage Gen2-Funktionen einzuschließen, lesen Sie diese Schritt-für-Schritt-Anleitung. 
> [!div class="nextstepaction"]
> [Upgrade von Azure Blob Storage mit Azure Data Lake Storage Gen2-Funktionen](upgrade-to-data-lake-storage-gen2-how-to.md)