---
title: Verwenden von Azure Storage-Explorer mit Azure Data Lake Storage Gen2
description: Verwenden Sie Azure Storage-Explorer, um Verzeichnisse, Dateien und Zugriffssteuerungslisten (ACLs) in Speicherkonten zu verwalten, für die der hierarchische Namespace aktiviert ist.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 02/17/2021
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 5307a56ba2384f9e0294634530823238fd903859
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124766747"
---
# <a name="use-azure-storage-explorer-to-manage-directories-and-files-in-azure-data-lake-storage-gen2"></a>Verwenden von Azure Storage-Explorer zum Verwalten von Verzeichnissen und Dateien in Azure Data Lake Storage Gen2

In diesem Artikel erfahren Sie, wie Sie [Azure Storage-Explorer](https://azure.microsoft.com/features/storage-explorer/) zum Erstellen und Verwalten von Verzeichnissen und Dateien in Speicherkonten verwenden, für die der hierarchische Namespace aktiviert ist.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Abonnement. Siehe [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/).

- Ein Speicherkonto, für das der hierarchische Namespace aktiviert ist. Befolgen Sie [diese Anleitung](../common/storage-account-create.md) für die Erstellung.

- Azure Storage-Explorer muss auf dem lokalen Computer installiert sein. Informationen zum Installieren von Azure Storage-Explorer für Windows, Macintosh oder Linux finden Sie unter [Azure Storage-Explorer](https://azure.microsoft.com/features/storage-explorer/).

> [!NOTE]
> Storage Explorer nutzt bei Verwendung von Azure Data Lake Storage Gen2 sowohl den Blob-[Endpunkt](../common/storage-private-endpoints.md#private-endpoints-for-azure-storage) (blob) als auch den Data Lake Storage Gen2-Endpunkt (dfs). Wenn der Zugriff auf Azure Data Lake Storage Gen2 über private Endpunkte konfiguriert wird, stellen Sie sicher, dass zwei private Endpunkte für das Speicherkonto erstellt werden: einer mit der Zielunterressource `blob` und der andere mit der Zielunterressource `dfs`.

## <a name="sign-in-to-storage-explorer"></a>Anmelden bei Storage-Explorer

Nach dem erstmaligen Starten von Storage-Explorer wird das Fenster **Microsoft Azure Storage-Explorer – Verbinden** angezeigt. Obwohl der Storage-Explorer mehrere Möglichkeiten zur Verbindung mit Speicherkonten bietet, wird derzeit nur eine Möglichkeit zur Verwaltung von ACLs unterstützt.

|Aufgabe|Zweck|
|---|---|
|Hinzufügen eines Azure-Kontos | Führt die Umleitung auf die Anmeldeseite Ihrer Organisation durch, um Sie für Azure zu authentifizieren. Dies ist derzeit die einzige unterstützte Authentifizierungsmethode, wenn Sie ACLs verwalten und festlegen möchten.|
|Verwenden einer Verbindungszeichenfolge oder eines Shared Access Signature-URI | Kann verwendet werden, um mit einem SAS-Token oder einer freigegebenen Verbindungszeichenfolge direkt auf einen Container oder ein Speicherkonto zuzugreifen. |
|Verwenden eines Speicherkontonamens und -schlüssels| Verwenden Sie den Speicherkontonamen und -schlüssel Ihres Speicherkontos, um eine Verbindung mit Azure Storage herzustellen.|

Wählen Sie **Azure-Konto hinzufügen**, und klicken Sie auf **Anmelden...** . Befolgen Sie die Anweisungen auf dem Bildschirm, um sich an Ihrem Azure-Konto anzumelden.

![Screenshot: Microsoft Azure Storage-Explorer mit hervorgehobener Option „Azure-Konto hinzufügen“ und der Schaltfläche „Anmelden“](media/quickstart-storage-explorer/storage-explorer-connect.png)

Nach Abschluss des Verbindungsvorgangs wird Azure Storage-Explorer geladen, und die Registerkarte **Explorer** wird angezeigt. So erhalten Sie einen Einblick in Ihre gesamten Azure Storage-Konten und in den lokalen Speicher, der über den [Azurite-Speicheremulator](../common/storage-use-azurite.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json), [Cosmos DB](../../cosmos-db/storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)-Konten oder [Azure Stack](/azure-stack/user/azure-stack-storage-connect-se?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)-Umgebungen konfiguriert wurde.

![Fenster „Microsoft Azure Storage-Explorer – Verbinden“](media/quickstart-storage-explorer/storage-explorer-main-page.png)

## <a name="create-a-container"></a>Erstellen eines Containers

Ein Container enthält Verzeichnisse und Dateien. Erweitern Sie das Speicherkonto, das Sie im vorherigen Schritt erstellt haben, um einen Container zu erstellen. Wählen Sie die Option **Blobcontainer** aus, klicken Sie mit der rechten Maustaste darauf, und wählen Sie **Blobcontainer erstellen** aus. Geben Sie den Namen für den Container ein. Eine Liste mit den Regeln und Einschränkungen für die Benennung von Containern finden Sie unter [Erstellen eines Containers](storage-quickstart-blobs-dotnet.md#create-a-container). Drücken Sie nach Abschluss des Vorgangs die **EINGABETASTE**, um den Container zu erstellen. Nach der erfolgreichen Erstellung des Containers wird dieser im Ordner **Blobcontainer** für das ausgewählte Speicherkonto angezeigt.

![Microsoft Azure Storage-Explorer – Erstellen eines Containers](media/data-lake-storage-explorer/creating-a-filesystem.png)

## <a name="create-a-directory"></a>Erstellen eines Verzeichnisses

Wählen Sie den Container aus, den Sie im vorherigen Schritt erstellt haben, um ein Verzeichnis zu erstellen. Wählen Sie auf dem Menüband „Container“ die Schaltfläche **Neuer Ordner** aus. Geben Sie den Namen für das Verzeichnis ein. Drücken Sie nach Abschluss des Vorgangs die **EINGABETASTE**, um das Verzeichnis zu erstellen. Nachdem das Verzeichnis erstellt wurde, wird es im Editor-Fenster angezeigt.

![Microsoft Azure Storage-Explorer – Erstellen eines Verzeichnisses](media/data-lake-storage-explorer/creating-a-directory.png)

## <a name="upload-blobs-to-the-directory"></a>Hochladen von Blobs in das Verzeichnis

Wählen Sie auf dem Menüband „Verzeichnis“ die Schaltfläche **Hochladen** aus. Bei diesem Vorgang besteht die Option, einen Ordner oder eine Datei hochzuladen.

Wählen Sie die Dateien oder den Ordner für den Upload aus.

![Microsoft Azure Storage-Explorer – Blob hochladen](media/data-lake-storage-explorer/upload-file.png)

Wenn Sie **OK** wählen, werden die ausgewählten Dateien in die Warteschlange für den Upload eingereiht, und jede Datei wird hochgeladen. Nach Abschluss des Uploads werden die Ergebnisse im Fenster **Aktivitäten** angezeigt.

## <a name="view-blobs-in-a-directory"></a>Anzeigen von Blobs in einem Verzeichnis

Wählen Sie in der Anwendung **Azure Storage-Explorer** ein Verzeichnis unter einem Speicherkonto aus. Im Hauptbereich wird eine Liste mit den Blobs des ausgewählten Verzeichnisses angezeigt.

![Microsoft Azure Storage-Explorer – Blobs eines Verzeichnisses auflisten](media/data-lake-storage-explorer/list-files.png)

## <a name="download-blobs"></a>Herunterladen von Blobs

Wählen Sie auf dem Menüband bei ausgewählter Datei die Option **Herunterladen** aus, um mit **Azure Storage-Explorer** Dateien herunterzuladen. Ein Dialogfeld für Dateien wird geöffnet, und Sie können einen Dateinamen eingeben. Wählen Sie **Speichern** aus, um mit dem Herunterladen einer Datei in den lokalen Speicherort zu beginnen.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie Datei- und Verzeichnisberechtigungen durch Festlegen von Zugriffssteuerungslisten (Access Control Lists, ACLs) verwalten.

> [!div class="nextstepaction"]
> [Verwenden von Azure Storage-Explorer zum Verwalten von Zugriffssteuerungslisten in Azure Data Lake Storage Gen2](./data-lake-storage-explorer-acl.md)
