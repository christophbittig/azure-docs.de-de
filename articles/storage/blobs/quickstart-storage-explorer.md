---
title: 'Schnellstart: Erstellen eines Blobs mit Azure Storage-Explorer'
titleSuffix: Azure Storage
description: Hier erfahren Sie, wie Sie mit Azure Storage-Explorer einen Container und ein Blob erstellen, das Blob auf Ihren lokalen Computer herunterladen und alle Blobs im Container anzeigen.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.date: 09/10/2021
ms.author: tamram
ms.openlocfilehash: abf75bb39610deb54b72002cde3dd8e28131ed94
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124838989"
---
# <a name="quickstart-use-azure-storage-explorer-to-create-a-blob"></a>Schnellstart: Verwenden von Azure Storage-Explorer zum Erstellen eines Blobs

In diesem Schnellstart erfahren Sie, wie Sie [Azure Storage-Explorer](https://azure.microsoft.com/features/storage-explorer/) verwenden, um einen Container und ein Blob zu erstellen. Als Nächstes erfahren Sie, wie Sie den Blob auf Ihren lokalen Computer herunterladen, und wie Sie alle Blobs in einem Container anzeigen. Außerdem erfahren Sie, wie Sie eine Momentaufnahme eines Blobs erstellen, Zugriffsrichtlinien für Container verwalten und eine SAS (Shared Access Signature) erstellen.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

Diese Schnellstartanleitung setzt voraus, dass Sie Azure Storage-Explorer installiert haben. Informationen zum Installieren von Azure Storage-Explorer für Windows, Macintosh oder Linux finden Sie unter [Azure Storage-Explorer](https://azure.microsoft.com/features/storage-explorer/).

## <a name="log-in-to-storage-explorer"></a>Anmelden an Storage-Explorer

Nach dem ersten Start wird das Fenster **Microsoft Azure Storage-Explorer – Verbinden** angezeigt. Es gibt verschiedene Möglichkeiten, wie Sie den Storage-Explorer mit Speicherkonten verbinden können. Die folgende Tabelle enthält die verschiedenen Verbindungsmöglichkeiten:

|Aufgabe|Zweck|
|---|---|
|Hinzufügen eines Azure-Kontos | Führt die Umleitung auf die Anmeldeseite Ihrer Organisation durch, um Sie für Azure zu authentifizieren. |
|Verwenden einer Verbindungszeichenfolge oder eines Shared Access Signature-URI | Kann verwendet werden, um mit einem SAS-Token oder einer freigegebenen Verbindungszeichenfolge direkt auf einen Container oder ein Speicherkonto zuzugreifen. |
|Verwenden eines Speicherkontonamens und -schlüssels| Verwenden Sie den Speicherkontonamen und -schlüssel Ihres Speicherkontos, um eine Verbindung mit Azure Storage herzustellen.|

Wählen Sie **Azure-Konto hinzufügen**, und klicken Sie auf **Anmelden...** . Befolgen Sie die Anweisungen auf dem Bildschirm, um sich an Ihrem Azure-Konto anzumelden.

:::image type="content" source="media/quickstart-storage-explorer/storage-explorer-connect.png" alt-text="Screenshot, der das Fenster Microsoft Azure Storage-Explorer – Verbinden zeigt":::

Nachdem der Storage-Explorer die Verbindung hergestellt hat, zeigt er die Registerkarte **Explorer** an. Diese Ansicht bietet Ihnen Insight zu all Ihren Azure-Speicher-Konten sowie zu lokalem Speicher, der über den [Azurite-Speicheremulator](../common/storage-use-azurite.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json), [Cosmos DB](../../cosmos-db/storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)-Konten oder [Azure Stack](/azure-stack/user/azure-stack-storage-connect-se?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)-Umgebungen konfiguriert ist.

:::image type="content" source="media/quickstart-storage-explorer/storage-explorer-main-page.png" alt-text="Screenshot mit der Hauptseite von Storage-Explorer":::

## <a name="create-a-container"></a>Erstellen eines Containers

Erweitern Sie das Speicherkonto, das Sie im vorherigen Schritt erstellt haben, um einen Container zu erstellen. Wählen Sie **BLOB-Container**, klicken Sie mit der rechten Maustaste, und wählen Sie **BLOB-Container erstellen**. Geben Sie den Namen für den Blobcontainer ein. Eine Liste mit den Regeln und Einschränkungen für die Benennung von Blobcontainern finden Sie unter [Erstellen eines Containers](storage-quickstart-blobs-dotnet.md#create-a-container). Drücken Sie nach Abschluss des Vorgangs die **EINGABETASTE**, um den Blobcontainer zu erstellen. Nach der erfolgreichen Erstellung des Blobcontainers wird er im Ordner **BLOB-Container** für das ausgewählte Speicherkonto angezeigt.

## <a name="upload-blobs-to-the-container"></a>Hochladen von Blobs in den Container

Blobspeicher unterstützt Block-, Anfüge- und Seitenblobs. Seitenblobs sind VHD-Dateien, mit denen IaaS-VMs gesichert werden. Anfügeblobs dienen der Protokollierung und können z.B. verwendet werden, um beim Schreiben in eine Datei zusätzliche Daten hinzuzufügen. Die meisten Dateien, die im Blob Storage gespeichert werden, sind allerdings Blockblobs.

Wählen Sie im Menüband des Containers die Option **Hochladen**. Bei diesem Vorgang besteht die Option, einen Ordner oder eine Datei hochzuladen.

Wählen Sie die Dateien oder den Ordner für den Upload aus. Wählen Sie den **BLOB-Typ** aus. Zulässige Optionen sind **Anfüge**-, **Seiten**- oder **Blockblobs**.

Wählen Sie **VHD-/VHDX-Dateien als Seitenblobs hochladen (empfohlen)** , falls Sie eine VHD- oder VHDX-Datei hochladen.

Geben Sie im Feld **In Ordner hochladen (optional)** den Namen eines Ordners ein, in dem die Dateien oder Ordner unter dem Container gespeichert werden sollen. Wenn kein Ordner ausgewählt ist, werden die Dateien in den Ordner auf der Ebene direkt unterhalb des Containers hochgeladen.

:::image type="content" source="media/quickstart-storage-explorer/storage-explorer-upload-blob.png" alt-text="Microsoft Azure Storage-Explorer – Blob hochladen":::

Wenn Sie **OK** wählen, werden die ausgewählten Dateien in die Warteschlange für den Upload eingereiht, und jede Datei wird hochgeladen. Nach Abschluss des Uploads werden die Ergebnisse im Fenster **Aktivitäten** angezeigt.

## <a name="view-blobs-in-a-container"></a>Anzeigen von Blobs in einem Container

Wählen Sie in der Anwendung **Azure Storage-Explorer** einen Container unter einem Speicherkonto aus. Im Hauptbereich wird eine Liste mit den Blobs des ausgewählten Containers angezeigt.

:::image type="content" source="media/quickstart-storage-explorer/storage-explorer-list-blobs.png" alt-text="Screenshot, der die Auswahl eines Containers im Microsoft Azure Storage-Explorer zeigt ":::

## <a name="download-blobs"></a>Herunterladen von Blobs

Wählen Sie im Menüband bei ausgewähltem Blob die Option **Herunterladen**, um mit dem **Azure Storage-Explorer** Blobs herunterzuladen. Ein Dialogfeld für Dateien wird geöffnet, und Sie können einen Dateinamen eingeben. Wählen Sie **Speichern**, um mit dem Herunterladen eines Blobs an den lokalen Speicherort zu beginnen.

## <a name="manage-snapshots"></a>Verwalten von Momentaufnahmen

Azure Storage-Explorer bietet die Möglichkeit zum Erstellen und Verwalten von [Momentaufnahmen](./snapshots-overview.md) für Ihre Blobs. Klicken Sie zum Erstellen einer Momentaufnahme eines Blobs mit der rechten Maustaste auf das Blob, und wählen Sie **Momentaufnahme erstellen** aus. Klicken Sie zum Anzeigen von Momentaufnahmen für ein Blob mit der rechten Maustaste auf das Blob, und wählen Sie **Manage Snapshots** (Momentaufnahmen verwalten). Auf der aktuellen Registerkarte wird eine Liste mit den Momentaufnahmen des Blobs angezeigt.

:::image type="content" source="media/quickstart-storage-explorer/storage-explorer-snapshots.png" alt-text="Screenshot, der zeigt, wie Momentaufnahmen von Blob erstellt und verwaltet werden":::

## <a name="generate-a-shared-access-signature"></a>Generieren einer freigegebenen Zugriffssignatur

Sie können den Storage-Explorer verwenden, um eine freigegebene Zugriffssignatur (SAS) zu erstellen. Klicken Sie mit der rechten Maustaste auf ein Speicherkonto, einen Container oder ein Blob, und wählen Sie die Option **Get Shared Access Signature...** (Shared Access Signature abrufen...). Wählen Sie die Start- und Ablaufzeit und die Berechtigungen für die SAS-URL aus, und klicken Sie auf **Erstellen**. Storage-Explorer generiert das SAS-Token mit den von Ihnen angegebenen Parametern und zeigt es zum Kopieren an.

:::image type="content" source="media/quickstart-storage-explorer/storage-explorer-shared-access-signature.png" alt-text="Screenshot zeigt, wie man ein SAS0 generiert":::

Wenn Sie ein SAS für ein Speicherkonto erstellen, generiert Storage-Explorer ein Konto-SAS. Weitere Informationen über das Konto SAS finden Sie unter [Erstellen einer Konto-SAS](/rest/api/storageservices/create-account-sas).

Wenn Sie einen SAS für einen Container oder Blob erstellen, generiert Storage-Explorer eine Dienst-SAS. Weitere Informationen über die Dienst-SAS finden Sie unter [Erstellen einer Dienst-SAS](/rest/api/storageservices/create-service-sas).

> [!NOTE]
> Wenn Sie eine SAS mit Storage-Explorer erstellen, wird die SAS immer mit dem Schlüssel des Speicherkontos zugewiesen. Storage-Explorer unterstützt derzeit nicht die Erstellung einer Benutzerdelegations-SAS, d. h. einer SAS, die mit Azure AD-Anmeldeinformationen signiert ist.

## <a name="next-steps"></a>Nächste Schritte

In diesem Schnellstart haben Sie gelernt, wie Sie mit **Azure Storage-Explorer** Dateien zwischen einem lokalen Datenträger und Azure Blob Storage übertragen. Um mehr über die Arbeit mit Blob-Storage zu lernen, gehen Sie zur Übersicht von Blob-Speicher.

> [!div class="nextstepaction"]
> [Einführung in Azure Blob Storage](./storage-blobs-introduction.md)