---
title: Azure Storage-Beispiele mit .NET | Microsoft Docs
description: Sie können Beispielcode und -anwendungen für Azure Storage anzeigen, herunterladen und ausführen. Entdecken Sie Beispiele für erste Schritte mit Blobs, Warteschlangen, Tabellen und Dateien, indem Sie die Speicherclientbibliotheken für .NET verwenden.
author: normesta
ms.author: normesta
ms.date: 10/01/2020
ms.service: storage
ms.subservice: common
ms.topic: sample
ms.openlocfilehash: 947afa47c3ad5903e22cdd114a8020956861aa45
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128633733"
---
# <a name="azure-storage-samples-using-v12-net-client-libraries"></a>Azure Storage-Beispiele mit .NET-Clientbibliotheken (v12)

Die folgende Tabelle enthält eine Übersicht über unser Beispielrepository und in jedem Beispiel behandelten Szenarien. Klicken Sie auf die Links, um den zugehörigen Beispielcode in GitHub anzuzeigen.

> [!NOTE]
> In diesen Beispielen wird die aktuelle .NET v12-Bibliothek für Azure Storage verwendet. Älteren v11-Code finden Sie im GitHub-Repository unter [Azure Blob Storage-Beispiele für .NET](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started).

## <a name="blob-samples"></a>Blobbeispiele

### <a name="authentication"></a>Authentifizierung

:::row:::
   :::column span="":::
      [Authentifizieren mithilfe einer Verbindungszeichenfolge](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample02_Auth.cs#L27)
   :::column-end:::
   :::column span="":::
      [Authentifizieren mit Anmeldeinformationen eines gemeinsam verwendeten Schlüssels](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample02_Auth.cs#L91)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Authentifizieren mit einer Azure-Identität](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample01a_HelloWorld.cs#L210)
   :::column-end:::
   :::column span="":::
      [Authentifizieren mithilfe eines Active Directory-Tokens](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample02_Auth.cs#L177)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [Anonymer Zugriff auf ein öffentliches Blob](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample02_Auth.cs#L55)
   :::column-end:::
:::row-end:::

### <a name="batching"></a>Batching

:::row:::
   :::column span="":::
      [Löschen mehrerer Blobs in einer Anforderung](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs.Batch/samples/Sample03b_BatchingAsync.cs#L22)
   :::column-end:::
   :::column span="":::
      [Festlegen mehrerer Blobzugriffsebenen in einer Anforderung](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/storage/Azure.Storage.Blobs.Batch/samples/Sample03b_BatchingAsync.cs#L56)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Differenzierte Steuerung in einer Batchanforderung](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/storage/Azure.Storage.Blobs.Batch/samples/Sample03b_BatchingAsync.cs#L90)
   :::column-end:::
   :::column span="":::
      [Erkennen von Fehlern in einem fehlerhaften untergeordneten Vorgang](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/storage/Azure.Storage.Blobs.Batch/samples/Sample03b_BatchingAsync.cs#L136)
   :::column-end:::
:::row-end:::

### <a name="blob"></a>Blob

:::row:::
   :::column span="":::
      [Hochladen einer Datei in ein Blob](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample01b_HelloWorldAsync.cs#L21)
   :::column-end:::
   :::column span="":::
      [Herunterladen eines Blobs in eine Datei](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample01b_HelloWorldAsync.cs#L66)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Herunterladen eines Images](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample01b_HelloWorldAsync.cs#L109)
   :::column-end:::
   :::column span="":::
      [Auflisten aller Blobs in einem Container](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample01b_HelloWorldAsync.cs#L128)
   :::column-end:::
:::row-end:::

### <a name="troubleshooting"></a>Problembehandlung

:::row:::
   :::column span="2":::
      [Auslösen eines behebbaren Fehlers mithilfe eines Containerclients](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples/Sample01b_HelloWorldAsync.cs#L166)
   :::column-end:::
:::row-end:::

## <a name="data-lake-storage-gen2-samples"></a>Data Lake Storage Gen2-Beispiele

### <a name="authentication"></a>Authentifizierung

:::row:::
   :::column span="":::
      [Anonymer Zugriff auf eine öffentliche Datei](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample02_Auth.cs#L28)
   :::column-end:::
   :::column span="":::
      [Authentifizieren mit Anmeldeinformationen eines gemeinsam verwendeten Schlüssels](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample02_Auth.cs#L79)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Authentifizieren mithilfe einer SAS (Shared Access Signature)](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample02_Auth.cs#L114)
   :::column-end:::
   :::column span="":::
      [Authentifizieren mithilfe eines Active Directory-Tokens](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample02_Auth.cs#L164)
   :::column-end:::
:::row-end:::

### <a name="file-system"></a>Dateisystem

:::row:::
   :::column span="":::
      [Erstellen einer Datei mithilfe eines Dateisystemclients](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L22)
   :::column-end:::
   :::column span="":::
      [Abrufen von Eigenschaften für eine Datei und ein Verzeichnis](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L560)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [Umbenennen einer Datei und eines Verzeichnisses](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L511)
   :::column-end:::
:::row-end:::

### <a name="directory"></a>Verzeichnis

:::row:::
   :::column span="":::
      [Erstellen eines Verzeichnisses](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L93)
   :::column-end:::
   :::column span="":::
      [Erstellen einer Datei mithilfe eines Verzeichnisclients](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L55)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Auflisten von Verzeichnissen](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L275)
   :::column-end:::
   :::column span="":::
      [Durchlaufen von Dateien und Verzeichnissen](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L318)
   :::column-end:::
:::row-end:::

### <a name="file"></a>Datei

:::row:::
   :::column span="":::
      [Hochladen einer Datei](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L126)
   :::column-end:::
   :::column span="":::
      [Hochladen durch Anhängen an eine Datei](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L169)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Herunterladen einer Datei](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L224)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Festlegen und Abrufen einer Zugriffssteuerungsliste für Dateien](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L468)
   :::column-end:::
   :::column span="":::
      [Festlegen und Abrufen der Berechtigungen einer Datei](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L426)
   :::column-end:::
:::row-end:::

### <a name="troubleshooting"></a>Problembehandlung

:::row:::
   :::column span="2":::
      [Auslösen eines behebbaren Fehlers](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples/Sample01b_HelloWorldAsync.cs#L389)
   :::column-end:::
:::row-end:::

## <a name="azure-files-samples"></a>Azure Files-Beispiele

### <a name="authentication"></a>Authentifizierung

:::row:::
   :::column span="":::
      [Authentifizieren mithilfe einer Verbindungszeichenfolge](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.Shares/samples/Sample02_Auth.cs#L24)
   :::column-end:::
   :::column span="":::
      [Authentifizieren mit Anmeldeinformationen eines gemeinsam verwendeten Schlüssels](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.Shares/samples/Sample02_Auth.cs#L52)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [Authentifizieren mithilfe einer SAS (Shared Access Signature)](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.Shares/samples/Sample02_Auth.cs#L86)
   :::column-end:::
:::row-end:::

### <a name="file-shares"></a>Dateifreigaben

:::row:::
   :::column span="":::
      [Erstellen einer Freigabe und Hochladen einer Datei](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.Shares/samples/Sample01b_HelloWorldAsync.cs#L21)
   :::column-end:::
   :::column span="":::
      [Herunterladen einer Datei](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.Shares/samples/Sample01b_HelloWorldAsync.cs#L68)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [Durchlaufen von Dateien und Verzeichnissen](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.Shares/samples/Sample01b_HelloWorldAsync.cs#L107)
   :::column-end:::
:::row-end:::

### <a name="troubleshooting"></a>Problembehandlung

:::row:::
   :::column span="2":::
      [Auslösen eines behebbaren Fehlers mithilfe eines Freigabeclients](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.Shares/samples/Sample01b_HelloWorldAsync.cs#L141)
   :::column-end:::
:::row-end:::

## <a name="queue-samples"></a>Warteschlangenbeispiele

### <a name="authentication"></a>Authentifizierung

:::row:::
   :::column span="":::
      [Authentifizieren mit Azure Active Directory](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples/Sample01b_HelloWorldAsync.cs#L167)
   :::column-end:::
   :::column span="":::
      [Authentifizieren mithilfe einer Verbindungszeichenfolge](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples/Sample02_Auth.cs#L24)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Authentifizieren mit Anmeldeinformationen eines gemeinsam verwendeten Schlüssels](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples/Sample02_Auth.cs#L52)
   :::column-end:::
   :::column span="":::
      [Authentifizieren mithilfe einer SAS (Shared Access Signature)](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples/Sample02_Auth.cs#L86)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [Authentifizieren mithilfe eines Active Directory-Tokens](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples/Sample02_Auth.cs#L140)
   :::column-end:::
:::row-end:::

### <a name="queue"></a>Warteschlange

:::row:::
   :::column span="2":::
      [Erstellen einer Warteschlange und Hinzufügen einer Nachricht](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples/Sample01b_HelloWorldAsync.cs#L24)
   :::column-end:::
:::row-end:::

### <a name="message"></a>`Message`

:::row:::
   :::column span="":::
      [Empfangen und Verarbeiten von Nachrichten](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples/Sample01b_HelloWorldAsync.cs#L61)
   :::column-end:::
   :::column span="":::
      [Einsehen von Nachrichten](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples/Sample01b_HelloWorldAsync.cs#L90)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [Empfangen von Nachrichten und Aktualisieren des Sichtbarkeitstimeouts](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples/Sample01b_HelloWorldAsync.cs#L115)
   :::column-end:::
:::row-end:::

### <a name="troubleshooting"></a>Problembehandlung

:::row:::
   :::column span="2":::
      [Auslösen eines behebbaren Fehlers mithilfe eines Warteschlangenclients](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples/Sample01b_HelloWorldAsync.cs#L188)
   :::column-end:::
:::row-end:::

## <a name="table-samples-v11"></a>Tabellenbeispiele (v11)

:::row:::
   :::column span="":::
      [Erstellen einer Tabelle](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/Common.cs#L40)
   :::column-end:::
   :::column span="":::
      [Löschen einer Entität/Tabelle](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Einfügen/Zusammenführen/Ersetzen von Entitäten](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/SamplesUtils.cs#L41)
   :::column-end:::
   :::column span="":::
      [Abfragen von Entitäten](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/AdvancedSamples.cs#L672)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Abfragen von Tabellen](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs)
   :::column-end:::
   :::column span="":::
      [Tabellen-ACL/-eigenschaften](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/AdvancedSamples.cs#L224)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [Aktualisieren einer Entität](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs#L51)
   :::column-end:::
:::row-end:::

## <a name="azure-code-sample-libraries"></a>Bibliotheken mit Azure-Codebeispielen

Die gesamten Bibliotheken mit .NET-Beispielen finden Sie hier:

- [Azure-Blobcodebeispiele](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples)
- [Azure Data Lake-Codebeispiele](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/samples)
- [Azure Files-Codebeispiele](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.Shares/samples)
- [Azure Queue-Codebeispiele](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples)

Sie können die einzelnen Bibliotheken im GitHub-Repository suchen und klonen.

## <a name="getting-started-guides"></a>Leitfäden mit ersten Schritten

Lesen Sie den folgenden Leitfäden, um Anleitungen zum Installieren und zu den ersten Schritten mit den Azure Storage-Clientbibliotheken zu erhalten.

- [Erste Schritte mit dem Azure Blob-Dienst in .NET](../blobs/storage-quickstart-blobs-dotnet.md)
- [Erste Schritte mit dem Azure-Warteschlangendienst in .NET](../queues/storage-quickstart-queues-dotnet.md)
- [Erste Schritte mit dem Azure-Tabellenspeicherdienst in .NET](../../cosmos-db/tutorial-develop-table-dotnet.md)
- [Erste Schritte mit dem Azure-Dateidienst in .NET](../files/storage-dotnet-how-to-use-files.md)

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Beispielen für andere Sprachen:

- Java: [Azure Storage-Beispiele mit Java](storage-samples-java.md)
- Python: [Azure Storage-Beispiele mit Python](storage-samples-python.md)
- JavaScript/Node.js: [Azure Storage-Beispiele mit JavaScript](storage-samples-javascript.md)
- C++: [Azure Storage-Beispiele mit C++](storage-samples-c-plus-plus.md)
- Alle anderen Sprachen: [Beispiele für Azure Storage](storage-samples.md)
