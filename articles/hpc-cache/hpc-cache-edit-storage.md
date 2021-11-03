---
title: Aktualisieren von Azure HPC Cache-Speicherzielen
description: Bearbeiten von Azure HPC Cache-Speicherzielen
author: femila
ms.service: hpc-cache
ms.topic: how-to
ms.date: 06/30/2021
ms.author: femila
ms.openlocfilehash: dd7c65e80df04172adb7b59d304ff8d616edf9cd
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131082567"
---
# <a name="edit-storage-targets"></a>Bearbeiten von Speicherzielen

Sie können Speicherziele mithilfe des Azure-Portals oder der Azure CLI ändern. Beispielsweise können Sie Zugriffsrichtlinien, Nutzungsmodelle und Namespacepfade für ein vorhandenes Speicherziel ändern.

> [!TIP]
> Lesen Sie [Verwalten von Speicherzielen](manage-storage-targets.md), um zu erfahren, wie Sie Speicherziele löschen oder anhalten oder zwischengespeicherte Daten in den Back-End-Speicher schreiben lassen.

Je nach Speichertyp können Sie die folgenden Werte für Speicherziele ändern:

* Bei Bobspeicherzielen können Sie den Namespacepfad und die Zugriffsrichtlinie ändern.

* Für NFS-Speicherziele können Sie die folgenden Werte ändern:

  * Namespacepfade
  * Zugriffsrichtlinie
  * Der einem Namespacepfad zugeordnete Speicherexport oder das entsprechende Exportunterverzeichnis
  * Nutzungsmodell

* Bei ADLS-NFS-Speicherzielen können Sie den Namespacepfad, die Zugriffsrichtlinie und das Nutzungsmodell ändern.

Sie können den Namen, Typ oder das Back-End-Speichersystem eines Speicherziels nicht bearbeiten. Wenn Sie diese Eigenschaften ändern müssen, müssen Sie das Speicherziel löschen und einen Ersatz mit dem neuen Wert erstellen.

Im [Video zur Verwaltung von Azure HPC Cache](https://azure.microsoft.com/resources/videos/managing-hpc-cache/) wird gezeigt, wie Sie ein Speicherziel im Azure-Portal bearbeiten.

## <a name="change-a-blob-storage-targets-namespace-path-or-access-policy"></a>Ändern des Namespacepfads oder der Zugriffsrichtlinie eines Blobspeicherziels

Bei Namespacepfaden handelt es sich um die Pfade, die Clients verwenden, um dieses Speicherziel einzubinden. Weitere Informationen finden Sie unter [Planen des aggregierten Namespace](hpc-cache-namespace.md) und [Einrichten des aggregierten Namespace](add-namespace-paths.md).

Ändern Sie mit Azure-Portal oder Azure CLI den Namespacepfad oder die Zugriffsrichtlinie.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Aktualisieren Sie mit der Seite **Namespace** für Ihre Azure HPC Cache-Instanz den Namespacepfad oder die Clientzugriffsrichtlinie. Die Seite „Namespace“ wird im Artikel [Einrichten des aggregierten Namespace](add-namespace-paths.md) im Detail beschrieben.

1. Klicken Sie auf den Pfad, den Sie ändern möchten.
   ![Screenshot der Seite „Namespace“ mit dem Cursor über einem Element in der Spalte „Namespacepfad“ (erste Spalte links). Der Name ist als Link formatiert, und der Cursor gibt an, dass darauf geklickt werden kann.](media/edit-select-namespace.png)

1. Verwenden Sie das Bearbeitungsfenster, um den neuen virtuellen Pfad einzugeben oder die Zugriffsrichtlinie zu aktualisieren.

   ![Screenshot der Seite „Namespace“ nach dem Klicken auf einen Blobnamespacepfad. Die Bearbeitungsfelder werden rechts in einem Bereich angezeigt.](media/update-namespace-blob.png)

Klicken Sie auf **OK**, nachdem Sie die Änderungen vorgenommen haben, um das Speicherziel zu aktualisieren, oder klicken Sie auf **Abbrechen**, um die Änderungen zu verwerfen.

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

[Einrichten der Azure CLI für Azure HPC Cache](./az-cli-prerequisites.md).

Verwenden Sie den Befehl [az hpc-cache blob-storage-target update](/cli/azure/hpc-cache/blob-storage-target#az_hpc_cache_blob_storage_target_update), um den Namespace eines Blobspeicherziels zu ändern. Nur der `--virtual-namespace-path`-Wert kann geändert werden.

  ```azurecli
  az hpc-cache blob-storage-target update --cache-name cache-name --name target-name \
    --resource-group rg --virtual-namespace-path "/new-path"
  ```

---

## <a name="update-an-nfs-storage-target"></a>Aktualisieren eines neuen NFS-Speicherziels

Bei NFS-Speicherzielen können Sie virtuelle Namespacepfade ändern oder hinzufügen, die Werte für den NFS-Export oder das Unterverzeichnis ändern, auf den ein Namespacepfad verweist, und das Nutzungsmodell ändern.

Speicherziele in Caches mit bestimmten Typen von benutzerdefinierten DNS-Einstellungen verfügen auch über ein Steuerelement zum Aktualisieren Ihrer IP-Adressen. (Diese Art von Konfiguration ist selten.) Erfahren Sie unter [Aktualisieren der IP-Adresse (nur benutzerdefinierte DNS-Konfigurationen)](manage-storage-targets.md#update-ip-address-custom-dns-configurations-only), wie Sie die DNS-Einstellungen aktualisieren.

Details finden Sie unten:

* [Ändern aggregierter Namespacewerte](#change-aggregated-namespace-values) (virtueller Namespacepfad, Zugriffsrichtlinie, Export und Exportunterverzeichnis)
* [Ändern des Nutzungsmodells](#change-the-usage-model)

### <a name="change-aggregated-namespace-values"></a>Ändern aggregierter Namespacewerte

Sie können das Azure-Portal oder die Azure CLI verwenden, um den clientseitigen Namespacepfad, den Speicherexport und das Exportunterverzeichnis (wenn verwendet) zu ändern. Wenn Sie die Zugriffsrichtlinie ändern müssen, verwenden Sie das Azure-Portal.

Lesen Sie die Anleitung unter [NFS-Namespacepfade](add-namespace-paths.md#nfs-namespace-paths), wenn Sie wissen möchten, wie Sie mehrere gültige Pfade für ein Speicherziel erstellen.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Aktualisieren Sie mit der Seite **Namespace** für Ihre Azure HPC Cache-Instanz die Namespacewerte inklusive der Clientzugriffsrichtlinie. Diese Seite wird im Artikel [Einrichten des aggregierten Namespace](add-namespace-paths.md) im Detail beschrieben.

![Screenshot der Seite „Namespace“ im Portal mit der geöffneten NFS-Aktualisierungsseite rechts](media/update-namespace-nfs.png)

1. Klicken Sie auf den Namen des Pfads, den Sie ändern möchten.
1. Verwenden Sie das Bearbeitungsfenster, um neue Werte für den virtuellen Pfad, den Export oder das Unterverzeichnis einzugeben oder eine andere Zugriffsrichtlinie auszuwählen.
1. Klicken Sie auf **OK**, nachdem Sie die Änderungen vorgenommen haben, um das Speicherziel zu aktualisieren, oder klicken Sie auf **Abbrechen**, um die Änderungen zu verwerfen.

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

[Einrichten der Azure CLI für Azure HPC Cache](./az-cli-prerequisites.md).

Verwenden Sie die ``--junction``-Option im [az hpc-cache nfs-storage-target update](/cli/azure/hpc-cache/nfs-storage-target)-Befehl, um den Namespacepfad, den NFS-Export oder das Exportunterverzeichnis zu ändern.

Der Parameter ``--junction`` verwendet diese Werte:

* ``namespace-path``: Der clientseitige virtuelle Dateipfad.
* ``nfs-export``: Der Speichersystemexport, der dem clientseitigen Pfad zugeordnet werden soll.
* ``target-path`` (optional): Ein Unterverzeichnis des Exports, falls erforderlich.

Beispiel: ``--junction namespace-path="/nas-1" nfs-export="/datadisk1" target-path="/test"``

Sie müssen in der ``--junction``-Anweisung alle drei Werte für jeden Pfad angeben. Verwenden Sie die vorhandenen Werte für alle Werte, die Sie nicht ändern möchten.

Der Cachename, der Name des Speicherziels und die Ressourcengruppe sind für alle Aktualisierungsbefehle ebenfalls erforderlich.

Beispielbefehl:

```azurecli
az hpc-cache nfs-storage-target update --cache-name mycache \
  --name st-name --resource-group doc-rg0619 \
  --junction namespace-path="/new-path" nfs-export="/my-export" target-path="my-subdirectory"
```

---

### <a name="change-the-usage-model"></a>Ändern des Nutzungsmodells

Das Nutzungsmodell beeinflusst, wie Daten im Cache gespeichert werden. Weitere Informationen finden Sie unter [Grundlegendes zu Cache-Verwendungsmodellen](cache-usage-models.md).

> [!NOTE]
> Sie können nicht zwischen **Umfangreiche, seltene Schreibvorgänge lesen** und anderen Nutzungsmodellen wechseln. Weitere Informationen finden Sie unter [Ändern von Nutzungsmodellen](cache-usage-models.md#change-usage-models).

Wenn Sie das Nutzungsmodell für ein NFS-Speicherziel ändern möchten, verwenden Sie eine der folgenden Methoden.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Öffnen Sie die Seite **Speicherziele** im Azure-Portal. Klicken Sie auf den Namen eines Speicherziels in der Liste, um die Bearbeitungsseite zu öffnen.

![Screenshot der Bearbeitungsseite für ein NFS-Speicherziel](media/edit-storage-nfs.png)

Verwenden Sie den Selektor im Dropdown, um ein neues Nutzungsmodell auszuwählen. Klicken Sie auf **OK**, um das Speicherziel zu aktualisieren, oder klicken Sie auf **Abbrechen**, um die Änderungen zu verwerfen.

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

[Einrichten der Azure CLI für Azure HPC Cache](./az-cli-prerequisites.md).

Verwenden Sie den [az hpc-cache nfs-storage-target update](/cli/azure/hpc-cache/nfs-storage-target#az_hpc_cache_nfs_storage_target_update)-Befehl.

Der Aktualisierungsbefehl ist beinahe identisch mit dem Befehl, den Sie verwenden, um ein NFS-Speicherziel hinzuzufügen. Details und Beispiele finden Sie unter [Erstellen eines NFS-Speicherziels](hpc-cache-add-storage.md#create-an-nfs-storage-target).

Aktualisieren Sie die Option ``--nfs3-usage-model``, um das Nutzungsmodell zu ändern. Beispiel: ``--nfs3-usage-model WRITE_WORKLOAD_15``

Die Werte für Cachename, für den Namen des Speicherziels und für die Ressourcengruppe sind ebenfalls erforderlich.

Wenn Sie die Namen der Nutzungsmodelle überprüfen möchten, verwenden Sie den Befehl [az hpc-cache usage-model list](/cli/azure/hpc-cache/usage-model#az_hpc_cache_usage-model-list).

Wenn der Cache beendet wurde oder sich nicht in einem fehlerfreien Zustand befindet, wird das Update angewendet, nachdem der Cache fehlerfrei ist.

---

## <a name="update-an-adls-nfs-storage-target"></a>Aktualisieren eines ADLS-NFS-Speicherziels

Ähnlich wie bei NFS-Speicherzielen können Sie bei ADLS-NFS-Speicherzielen den Namespacepfad, die Zugriffsrichtlinie und das Nutzungsmodell ändern.

### <a name="change-an-adls-nfs-namespace-path"></a>Ändern eines ADLS-NFS-Namespacepfads

Verwenden Sie die Seite **Namespace** für Ihre Azure HPC Cache-Instanz, um Namespacewerte zu aktualisieren. Diese Seite wird im Artikel [Einrichten des aggregierten Namespace](add-namespace-paths.md) im Detail beschrieben.

![Screenshot der Seite „Namespace“ im Portal mit der geöffneten ADLS-NFS-Aktualisierungsseite rechts](media/update-namespace-adls.png)

1. Klicken Sie auf den Namen des Pfads, den Sie ändern möchten.
1. Verwenden Sie das Bearbeitungsfenster, um den neuen virtuellen Pfad einzugeben oder die Zugriffsrichtlinie zu aktualisieren.
1. Klicken Sie auf **OK**, nachdem Sie die Änderungen vorgenommen haben, um das Speicherziel zu aktualisieren, oder klicken Sie auf **Abbrechen**, um die Änderungen zu verwerfen.

### <a name="change-adls-nfs-usage-models"></a>Ändern der ADLS-NFS-Nutzungsmodelle

Die Konfiguration für ADLS-NFS-Nutzungsmodelle ist mit der Auswahl des NFS-Nutzungsmodells identisch. Lesen Sie die Anleitung für das Portal unter [Ändern des Nutzungsmodells](#change-the-usage-model) im Abschnitt „NFS“ weiter oben. Weitere Tools zum Aktualisieren von ADLS-NFS-Speicherzielen sind in Entwicklung.

## <a name="next-steps"></a>Nächste Schritte

* Informationen zum Beenden, Löschen und Leeren einzelner Speicherziele finden Sie unter [Verwalten von Speicherzielen](manage-storage-targets.md).
* Weitere Informationen zu Speicherzieloptionen finden Sie unter [Hinzufügen von Speicherzielen](hpc-cache-add-storage.md).
* Weitere Tipps zur Verwendung virtueller Pfade finden Sie unter [Planen des aggregierten Namespace](hpc-cache-namespace.md).
