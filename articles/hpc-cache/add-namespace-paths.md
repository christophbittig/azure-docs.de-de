---
title: Konfigurieren des aggregierten Azure HPC Cache-Namespace
description: Erstellen von clientseitigen Pfaden für den Back-End-Speicher mit Azure HPC Cache
author: femila
ms.service: hpc-cache
ms.topic: how-to
ms.date: 04/22/2021
ms.author: femila
ms.openlocfilehash: 903ed4b3d17b0fc7290e5ce2755518cf768ac342
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131015369"
---
# <a name="set-up-the-aggregated-namespace"></a>Einrichten des aggregierten Namespace

Nachdem Sie Speicherziele erstellt haben, müssen Sie für diese auch Namespacepfade erstellen. Clientcomputer verwenden diese virtuellen Pfade, um über den Cache auf Dateien zuzugreifen, anstatt direkt eine Verbindung mit dem Back-End-Speicher herzustellen. Mit diesem System können Cacheadministratoren Back-End-Speichersysteme ändern, ohne Clientanweisungen neu schreiben zu müssen.

Weitere Informationen zu dieser Funktion finden Sie unter [Planen des aggregierten Namespace](hpc-cache-namespace.md).

Auf der Seite **Namespace** im Azure-Portal werden die Pfade angezeigt, die Clients verwenden, um über den Cache auf Ihre Daten zuzugreifen. Auf dieser Seite können Sie Namespacepfade erstellen, entfernen oder ändern. Sie können Namespacepfade auch über die Azure-Befehlszeilenschnittstelle konfigurieren.

Alle clientseitigen Pfade, die für diesen Cache definiert wurden, werden auf der **Namespace**-Seite aufgelistet. Speicherziele, für die keine Namespace-Pfade definiert wurden, werden noch nicht in der Tabelle angezeigt.

Sie können die Tabellenspalten sortieren und sich mit dem aggregierten Namespace des Caches vertraut machen. Klicken Sie auf die Pfeile im Spaltenheader, um die Pfade zu sortieren.

[ ![Screenshot der Seite „Namespace“ im Portal mit zwei Pfaden in einer Tabelle. Spaltenüberschriften: „Namespacepfad“, „Speicherziel“, „Exportpfad“, „Unterverzeichnis für Export“ und „Clientzugriffsrichtlinie“. Die Pfadnamen in der ersten Spalte sind klickbare Links. Schaltflächen im oberen Bereich: Hinzufügen, Aktualisieren und Löschen des Namespacepfads](media/namespace-page.png) ](media/namespace-page.png#lightbox)

## <a name="add-or-edit-namespace-paths"></a>Hinzufügen oder Bearbeiten des Namespacepfads

Sie müssen mindestens einen Namespacepfad erstellen, damit Clients auf das Speicherziel zugreifen können. (Weitere Informationen zum Clientzugriff finden Sie unter [Einbinden einer Azure HPC Cache-Instanz](hpc-cache-mount.md).)

Wenn Sie kürzlich ein Speicherziel hinzugefügt oder eine Zugriffsrichtlinie angepasst haben, kann es ein oder zwei Minuten dauern, bis Sie einen Namespace-Pfad erstellen können.

### <a name="blob-namespace-paths"></a>Blob-Namespacepfade

Ein Azure Blob Storage-Ziel kann nur einen Namespacepfad enthalten.

Befolgen Sie die nachstehenden Anweisungen, um den Pfad über das Azure-Portal oder die Azure-Befehlszeilenschnittstelle festzulegen oder zu ändern.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Öffnen Sie im Azure-Portal die Einstellungsseite **Namespace**. Auf dieser Seite können Sie Namespacepfade hinzufügen, ändern oder löschen.

* **Hinzufügen eines neuen Pfads:** Klicken Sie oben auf die Schaltfläche **+ Hinzufügen**, und geben Sie die entsprechenden Informationen im Bearbeitungsbereich ein.

  ![Screenshot der Bearbeitungsfelder des hinzugefügten Namespace mit einem ausgewählten Blobspeicherziel. Die Export- und Unterverzeichnispfade werden auf „/“ festgelegt und können nicht bearbeitet werden.](media/namespace-add-blob.png)

  * Geben Sie den Pfad ein, den Clients für den Zugriff auf dieses Speicherziel verwenden werden.

  * Wählen Sie die Zugriffsrichtlinie aus, die für diesen Pfad verwendet werden soll. Erfahren Sie mehr über das Anpassen des Clientzugriffs in [Verwenden von Clientzugriffsrichtlinien](access-policies.md).

  * Wählen Sie in der Dropdownliste das Speicherziel aus. Wenn ein Blobspeicherziel bereits über einen Namespacepfad verfügt, kann es nicht ausgewählt werden.

  * Für ein Azure Blob Storage-Ziel werden die Export- und Unterverzeichnispfade automatisch auf ``/`` festgelegt.

* **Ändern eines vorhandenen Pfads:** Klicken Sie auf den Namespacepfad. Der Bearbeitungsbereich wird geöffnet. Sie können den Pfad und die Zugriffsrichtlinie ändern, aber nicht zu einem anderen Speicherziel wechseln.

* **Löschen eines Namespacepfads:** Aktivieren Sie das Kontrollkästchen links neben dem Pfad, und klicken Sie auf die Schaltfläche **Löschen**.

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

[Einrichten der Azure CLI für Azure HPC Cache](./az-cli-prerequisites.md).

Wenn Sie die Azure-Befehlszeilenschnittstelle verwenden, müssen Sie beim Erstellen des Speicherziels einen Namespacepfad hinzufügen. Weitere Informationen finden Sie unter [Hinzufügen eines neuen Azure Blob Storage-Ziels](hpc-cache-add-storage.md?tabs=azure-cli#add-a-new-azure-blob-storage-target).

Verwenden Sie den Befehl [az hpc-cache blob-storage-target update](/cli/azure/hpc-cache/blob-storage-target#az_hpc_cache_blob_storage_target_update), um den Namespacepfad eines Ziels zu aktualisieren. Die Argumente für den update-Befehl ähneln den Argumenten vom create-Befehl, mit dem Unterschied, dass der Containername oder das Speicherkonto nicht übergeben werden.

Über die Azure-Befehlszeilenschnittstelle können Sie einen Namespacepfad nicht aus einem Blob Storage-Ziel löschen, Sie können aber den Pfad mit einem anderen Wert überschreiben.

---

### <a name="nfs-namespace-paths"></a>NFS-Namespacepfade

Ein NFS-Speicherziel kann mehrere virtuelle Pfade aufweisen, solange jeder Pfad ein anderes Export- oder Unterverzeichnis in demselben Speichersystem darstellt.

Wenn Sie den Namespace für ein NFS-Speicherziel planen, beachten Sie, dass jeder Pfad eindeutig sein muss und kein Unterverzeichnis eines anderen Namespacepfads sein darf. Bei dem Namespacepfad ``/parent-a`` können Sie beispielsweise keine Namespacepfade wie ``/parent-a/user1`` und ``/parent-a/user2`` erstellen. Diese Verzeichnispfade sind im Namespace bereits als Unterverzeichnisse von ``/parent-a`` verfügbar.

Alle Namespacepfade für ein NFS-Speichersystem werden in einem Speicherziel erstellt.

Geben Sie für jeden NFS-Namespacepfad den clientseitigen Pfad, das Verzeichnis für den Speichersystemexport und optional ein Exportunterverzeichnis an.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Öffnen Sie im Azure-Portal die Einstellungsseite **Namespace**. Auf dieser Seite können Sie Namespacepfade hinzufügen, bearbeiten oder löschen.

* **So fügen Sie einen neuen Pfad hinzu** Klicken Sie oben auf die Schaltfläche **+ Hinzufügen**, und geben Sie die entsprechenden Informationen im Bearbeitungsbereich ein.
* **So ändern Sie einen vorhandenen Pfad** Klicken Sie auf den Namespacepfad. Der Bearbeitungsbereich wird geöffnet, und Sie können den Pfad ändern.
* **So löschen Sie einen Namespacepfad** Aktivieren Sie das Kontrollkästchen links neben dem Pfad, und klicken Sie auf die Schaltfläche **Löschen**.

Geben Sie diese Werte für die einzelnen Namespacepfade ein:

* **Namespacepfad:** der clientseitige Dateipfad.

* **Clientzugriffsrichtlinie**: Wählen Sie die Zugriffsrichtlinie aus, die für diesen Pfad verwendet werden soll. Erfahren Sie mehr über das Anpassen des Clientzugriffs in [Verwenden von Clientzugriffsrichtlinien](access-policies.md).

* **Speicherziel:** Wenn Sie einen neuen Namespacepfad erstellen, wählen Sie im Dropdownmenü ein Speicherziel aus.

* **Exportpfad:** Geben Sie den Pfad zum NFS-Export ein. Achten Sie darauf, den Exportnamen richtig einzugeben. Im Portal wird zwar die Syntax für dieses Feld überprüft, der Export wird jedoch erst überprüft, wenn die Änderung übermittelt wird.

* **Unterverzeichnis für Export:** Wenn über den Pfad ein bestimmtes Unterverzeichnis des Exports eingebunden werden soll, geben Sie es hier ein. Lassen Sie dieses Feld andernfalls leer.

![Screenshot: Namespaceseite im Portal mit der geöffneten Bearbeitungsseite rechts. Das Bearbeitungsformular zeigt Einstellungen für den Pfad eines NFS-Speicherzielnamespace.](media/namespace-edit-nfs.png)

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

[Einrichten der Azure CLI für Azure HPC Cache](./az-cli-prerequisites.md).

Wenn Sie die Azure-Befehlszeilenschnittstelle verwenden, müssen Sie beim Erstellen des Speicherziels mindestens einen Namespacepfad hinzufügen. Weitere Informationen finden Sie unter [Hinzufügen eines neuen NFS-Speicherziels](hpc-cache-add-storage.md?tabs=azure-cli#add-a-new-nfs-storage-target).

Verwenden Sie den Befehl [az hpc-cache nfs-storage-target update](/cli/azure/hpc-cache/nfs-storage-target#az_hpc_cache_nfs_storage_target_update), um den Namespacepfad des Ziels zu aktualisieren oder weitere Pfade hinzuzufügen. Verwenden Sie die Option ``--junction``, um alle gewünschten Namespacepfade anzugeben.

Die für den update-Befehl verwendeten Optionen ähneln denen vom create-Befehl, mit dem Unterschied, dass die Informationen zum Speichersystem (IP-Adresse oder Hostname) nicht übergeben werden und dass das Verwendungsmodell optional ist. Weitere Informationen zur Syntax der Option ``--junction`` finden Sie unter [Hinzufügen eines neuen NFS-Speicherziels](hpc-cache-add-storage.md?tabs=azure-cli#add-a-new-nfs-storage-target).

---

### <a name="adls-nfs-namespace-paths"></a>ADLS-NFS-Namespacepfade

Wie bei einem regulären Blob Storage-Ziel hat ein ADLS-NFS-Speicherziel nur einen Export, sodass es auch nur einen Namespacepfad aufweisen kann.

Befolgen Sie die nachstehenden Anweisungen, um den Pfad über das Azure-Portal festzulegen oder zu ändern.

Öffnen Sie die Einstellungsseite **Namespace**.

* **Hinzufügen eines neuen Pfads:** Klicken Sie oben auf die Schaltfläche **+ Hinzufügen**, und geben Sie die entsprechenden Informationen im Bearbeitungsbereich ein.

  ![Screenshot der Bearbeitungsfelder des hinzugefügten Namespace mit einem ausgewählten ADLS-NFS-Speicherziel Die Export- und Unterverzeichnispfade werden auf „/“ festgelegt und können nicht bearbeitet werden.](media/namespace-add-adls.png)

  * Geben Sie den Pfad ein, den Clients für den Zugriff auf dieses Speicherziel verwenden werden.

  * Wählen Sie die Zugriffsrichtlinie aus, die für diesen Pfad verwendet werden soll. Erfahren Sie mehr über das Anpassen des Clientzugriffs in [Verwenden von Clientzugriffsrichtlinien](access-policies.md).

  * Wählen Sie in der Dropdownliste das Speicherziel aus. Wenn ein ADLS-NFS-Speicherziel bereits über einen Namespacepfad verfügt, kann es nicht ausgewählt werden.

  * Für ein ADLS-NFS-Speicherziel werden die Export- und Unterverzeichnispfade automatisch auf ``/`` festgelegt.

* **Ändern eines vorhandenen Pfads:** Klicken Sie auf den Namespacepfad. Der Bearbeitungsbereich wird geöffnet. Sie können den Pfad und die Zugriffsrichtlinie ändern, aber nicht zu einem anderen Speicherziel wechseln.

* **Löschen eines Namespacepfads:** Aktivieren Sie das Kontrollkästchen links neben dem Pfad, und klicken Sie auf die Schaltfläche **Löschen**.

## <a name="next-steps"></a>Nächste Schritte

Nach dem Erstellen des aggregierten Namespace für Ihre Speicherziele können Sie Clients im Cache einbinden. Weitere Informationen finden Sie in den folgenden Artikeln.

* [Einbinden der Azure HPC Cache-Instanz](hpc-cache-mount.md)
* [Daten in Azure-Blobspeicher verschieben](hpc-cache-ingest.md)
