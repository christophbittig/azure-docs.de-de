---
title: 'Schnellstart: Azure Blob Storage-Bibliothek v12 – C++'
description: In dieser Schnellstartanleitung erfahren Sie, wie mithilfe der Azure Blob Storage-Clientbibliothek Version 12 für C++ ein Container und ein Blob in Blob Storage (Objektspeicher) erstellt werden. Als Nächstes erfahren Sie, wie Sie den Blob auf Ihren lokalen Computer herunterladen, und wie Sie alle Blobs in einem Container auflisten.
author: kyle-patterson
ms.author: kylepa
ms.date: 06/21/2021
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: 5b961145b065f2ce63497b32d4ba533dda04c68f
ms.sourcegitcommit: 5be51a11c63f21e8d9a4d70663303104253ef19a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/25/2021
ms.locfileid: "112894267"
---
# <a name="quickstart-azure-blob-storage-client-library-v12-for-c"></a>Schnellstart: Azure Blob Storage-Clientbibliothek v12 für C++

Hier erfahren Sie mehr über die ersten Schritte mit der Azure Blob Storage-Clientbibliothek v12 für C++. Azure Blob Storage ist die Objektspeicherlösung von Microsoft für die Cloud. Führen Sie die Schritte zum Installieren des Pakets aus, und testen Sie den Beispielcode für grundlegende Aufgaben. Blob Storage ist für die Speicherung großer Mengen unstrukturierter Daten optimiert.

Mit der Azure Blob Storage-Clientbibliothek v12 für C++ ist Folgendes möglich:

- Erstellen eines Containers
- Hochladen eines Blobs in Azure Storage
- Auflisten aller Blobs in einem Container
- Herunterladen des Blobs auf den lokalen Computer
- Löschen eines Containers

Ressourcen:

- [API-Referenzdokumentation](https://azure.github.io/azure-sdk-for-cpp/storage.html)
- [Quellcode der Bibliothek](https://github.com/Azure/azure-sdk-for-cpp/tree/master/sdk/storage)
- [Beispiele](../common/storage-samples-c-plus-plus.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Voraussetzungen

- [Azure-Abonnement](https://azure.microsoft.com/free/)
- [Azure-Speicherkonto](../common/storage-account-create.md)
- [C++-Compiler](https://azure.github.io/azure-sdk/cpp_implementation.html#supported-platforms)
- [CMake](https://cmake.org/)
- [Vcpkg: C- und C++-Paket-Manager](https://github.com/microsoft/vcpkg/blob/master/docs/README.md)

## <a name="setting-up"></a>Einrichten

In diesem Abschnitt wird beschrieben, wie ein Projekt zur Verwendung mit der Azure Blob Storage-Clientbibliothek v12 für C++ vorbereitet wird.

### <a name="install-the-packages"></a>Installieren der Pakete

Mit dem Befehl `vcpkg install` werden das Azure Storage Blobs SDK für C++ und die erforderlichen Abhängigkeiten installiert:

```console
vcpkg.exe install azure-storage-blobs-cpp:x64-windows
```

Besuchen Sie GitHub, um weitere Informationen zum Abrufen und Erstellen des [Azure SDK für C++](https://github.com/Azure/azure-sdk-for-cpp/) zu erhalten.

### <a name="create-the-project"></a>Erstellen des Projekts

Erstellen Sie in Visual Studio eine neue C++-Konsolenanwendung für Windows mit dem Namen *BlobQuickstartV12*.

:::image type="content" source="./media/quickstart-blobs-c-plus-plus/vs-create-project.jpg" alt-text="Visual Studio-Dialogfeld zum Konfigurieren einer neuen C++-Windows-Konsolen-App":::

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Objektmodell

Azure Blob Storage ist für die Speicherung großer Mengen unstrukturierter Daten optimiert. Unstrukturierte Daten sind Daten, die keinem bestimmten Datenmodell und keiner bestimmten Definition entsprechen (also beispielsweise Text- oder Binärdaten). Blob Storage bietet drei Typen von Ressourcen:

- Das Speicherkonto
- Einen Container im Speicherkonto
- Ein Blob im Container

Im folgenden Diagramm ist die Beziehung zwischen diesen Ressourcen dargestellt.

![Diagramm der Blob Storage-Architektur](./media/storage-blobs-introduction/blob1.png)

Verwenden Sie diese C++-Klassen um mit diesen Ressourcen zu interagieren:

- [BlobServiceClient:](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/12.0.0/class_azure_1_1_storage_1_1_blobs_1_1_blob_service_client.html) Die `BlobServiceClient`-Klasse ermöglicht Ihnen, Azure Storage-Ressourcen und Blobcontainer zu bearbeiten.
- [BlobContainerClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/12.0.0/class_azure_1_1_storage_1_1_blobs_1_1_blob_container_client.html): Die `BlobContainerClient`-Klasse ermöglicht Ihnen, Azure Storage-Container und deren Blobs zu bearbeiten.
- [BlobClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/12.0.0/class_azure_1_1_storage_1_1_blobs_1_1_blob_client.html): Die `BlobClient`-Klasse ermöglicht Ihnen, Azure Storage-Blobs zu bearbeiten. Dies ist die Basisklasse für alle spezialisierten Blobklassen.
- [BlockBlobClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/12.0.0/class_azure_1_1_storage_1_1_blobs_1_1_block_blob_client.html): Die `BlockBlobClient`-Klasse ermöglicht es Ihnen, Azure Storage-Blockblobs zu bearbeiten.

## <a name="code-examples"></a>Codebeispiele

Mit diesen Beispielcodeausschnitten wird veranschaulicht, wie folgende Vorgänge mit der Azure Blob Storage-Clientbibliothek für C++ durchgeführt werden:

- [Hinzufügen von Includedateien](#add-include-files)
- [Abrufen der Verbindungszeichenfolge](#get-the-connection-string)
- [Container erstellen](#create-a-container)
- [Hochladen von Blobs in einen Container](#upload-blobs-to-a-container)
- [Auflisten der Blobs in einem Container](#list-the-blobs-in-a-container)
- [Herunterladen von Blobs](#download-blobs)
- [Löschen eines Containers](#delete-a-container)

### <a name="add-include-files"></a>Hinzufügen von Includedateien

Über das Projektverzeichnis:

1. Öffnen Sie in Visual Studio die Projektmappendatei *BlobQuickstartV12.sln*.
1. Öffnen Sie in Visual Studio die Quelldatei *BlobQuickstartV12.cpp*.
1. Entfernen Sie den Code in `main`, der automatisch generiert wurde.
1. Hinzufügen von `#include`-Anweisungen

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" ID="Snippet_Includes":::

### <a name="get-the-connection-string"></a>Abrufen der Verbindungszeichenfolge

Der folgende Code ruft die Verbindungszeichenfolge für das Speicherkonto aus der Umgebungsvariablen ab, die unter [Konfigurieren der Speicherverbindungszeichenfolge](#configure-your-storage-connection-string) erstellt wurde.

Fügen Sie diesen Code in `main()` hinzu:

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" ID="Snippet_ConnectionString":::

### <a name="create-a-container"></a>Erstellen eines Containers

Erstellen Sie eine Instanz der [BlobContainerClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/12.0.0/class_azure_1_1_storage_1_1_blobs_1_1_blob_container_client.html)-Klasse, indem Sie die [CreateFromConnectionString](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/12.0.0/class_azure_1_1_storage_1_1_blobs_1_1_blob_container_client.html#a5d253aacb6e20578b7f5f233547be3e2)-Methode aufrufen. Rufen Sie anschließend [CreateIfNotExists](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/12.0.0/class_azure_1_1_storage_1_1_blobs_1_1_blob_container_client.html#ab3ef187d2e30e1a19ebadf45d0fdf9c4) auf, um den tatsächliche Container in Ihrem Speicherkonto zu erstellen.

> [!IMPORTANT]
> Die Containernamen müssen klein geschrieben werden. Weitere Informationen zum Benennen von Containern und Blobs finden Sie unter [Naming and Referencing Containers, Blobs, and Metadata](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) (Benennen von Containern, Blobs und Metadaten und Verweisen auf diese).

Fügen Sie am Ende von `main()` den folgenden Code hinzu:

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" ID="Snippet_CreateContainer":::

### <a name="upload-blobs-to-a-container"></a>Hochladen von Blobs in einen Container

Der folgende Codeausschnitt führt folgende Aktionen durch:

1. Deklarieren einer Zeichenfolge, die „Hello Azure!“ enthält
1. Abrufen eines Verweises auf ein [BlockBlobClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_block_blob_client.html)-Objekt durch Aufrufen von [GetBlockBlobClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_blob_container_client.html#acd8c68e3f37268fde0010dd478ff048f) für den Container aus dem Abschnitt [Erstellen eines Containers](#create-a-container)
1. Hochladen der Zeichenfolge in das Blob durch Aufrufen der Funktion [UploadFrom](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/1.0.0-beta.2/class_azure_1_1_storage_1_1_blobs_1_1_block_blob_client.html#af93af7e37f8806e39481596ef253f93d). Mit dieser Funktion wird das Blob erstellt, falls es nicht bereits vorhanden ist, oder aktualisiert, falls es bereits vorhanden ist.

Fügen Sie am Ende von `main()` den folgenden Code hinzu:

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" ID="Snippet_UploadBlob":::

### <a name="list-the-blobs-in-a-container"></a>Auflisten der Blobs im Container

Listen Sie die Blobs im Container auf, indem Sie die [ListBlobs](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/12.0.0/class_azure_1_1_storage_1_1_blobs_1_1_blob_container_client.html#a8f7e6653ef69788cb4fb23ec85d4fd96)-Funktion aufrufen. Dem Container wurde nur ein Blob hinzugefügt, sodass bei diesem Vorgang nur dieses Blob zurückgegeben wird.

Fügen Sie am Ende von `main()` den folgenden Code hinzu:

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" ID="Snippet_ListBlobs":::

### <a name="download-blobs"></a>Herunterladen von Blobs

Rufen Sie die Eigenschaften des aktualisierten Blobs ab. Deklarieren Sie anschließend ein neues `std::vector<uint8_t>`-Objekt, und ändern Sie seine Größe, indem Sie die Eigenschaften des hochgeladenen Blobs verwenden. Laden Sie das zuvor erstellte Blob in das neue `std::vector<uint8_t>`-Objekt herunter, indem Sie die Funktion [DownloadTo](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/12.0.0/class_azure_1_1_storage_1_1_blobs_1_1_blob_client.html#aa0bcbdbf6ebd17e4888cced4736420f7) in der [BlobClient](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/12.0.0/class_azure_1_1_storage_1_1_blobs_1_1_blob_client.html)-Basisklasse aufrufen. Zeigen Sie abschließend die heruntergeladenen Blobdaten an.

Fügen Sie am Ende von `main()` den folgenden Code hinzu:

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" ID="Snippet_DownloadBlob":::

### <a name="delete-a-blob"></a>Löschen eines Blobs

Mit dem folgenden Code wird das Blob durch Aufrufen der Funktion [BlobClient.Delete](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/12.0.0/class_azure_1_1_storage_1_1_blobs_1_1_blob_client.html#ac121df9f5dfcdeb80f47b50543df0984) aus dem Azure Blob Storage-Container gelöscht.

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" ID="Snippet_DeleteBlob":::

### <a name="delete-a-container"></a>Löschen eines Containers

Im folgenden Code werden die von der App erstellten Ressourcen bereinigt, indem der gesamte Container mithilfe von [BlobContainerClient.Delete](https://azuresdkdocs.blob.core.windows.net/$web/cpp/azure-storage-blobs/12.0.0/class_azure_1_1_storage_1_1_blobs_1_1_blob_container_client.html#ae7661b6d8f37213b409504c0e9a68d69) gelöscht wird.

Fügen Sie am Ende von `main()` den folgenden Code hinzu:

:::code language="cpp" source="~/azure-storage-snippets/blobs/quickstarts/C++/V12/BlobQuickstartV12/BlobQuickstartV12/BlobQuickstartV12.cpp" ID="Snippet_DeleteContainer":::

## <a name="run-the-code"></a>Ausführen des Codes

Diese App erstellt einen Container und lädt eine Textdatei in Azure Blob Storage hoch. Im Beispiel werden dann die Blobs im Container aufgelistet, und die Datei wird heruntergeladen und der Dateiinhalt angezeigt. Schließlich löscht die App das Blob und den Container.

Die Ausgabe der App sieht etwa wie das folgende Beispiel aus:

```output
Azure Blob Storage v12 - C++ quickstart sample
Creating container: myblobcontainer
Uploading blob: blob.txt
Listing blobs...
Blob name: blob.txt
Downloaded blob contents: Hello Azure!
Deleting blob: blob.txt
Deleting container: myblobcontainer
```

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung wurde beschrieben, wie Sie Blobs per C++ hochladen, herunterladen und auflisten. Außerdem haben Sie erfahren, wie Sie einen Azure Blob Storage-Container erstellen und löschen.

Ein C++-Blob Storage-Beispiel finden Sie hier:

> [!div class="nextstepaction"]
> [Beispiel: Azure Blob Storage SDK v12 für C++](https://github.com/Azure/azure-sdk-for-cpp/tree/master/sdk/storage/azure-storage-blobs/sample)
