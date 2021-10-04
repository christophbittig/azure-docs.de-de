---
title: Tutorial zum Überprüfen des Datenuploads aus Azure Data Box Disk in ein Speicherkonto
description: In diesem Tutorial erfahren Sie, wie Sie die Daten überprüfen, die aus Azure Data Box Disk in ein Azure-Speicherkonto hochgeladen wurden.
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.localizationpriority: high
ms.date: 09/17/2019
ms.author: alkohli
ms.openlocfilehash: 53e2db3728d92a862fce64ba1fc379a2ae2205ce
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128591632"
---
::: zone target="docs"

# <a name="tutorial-verify-data-upload-from-azure-data-box-disk"></a>Tutorial: Überprüfen des Datenuploads aus Azure Data Box Disk

Dies ist das letzte Tutorial der Reihe „Bereitstellen von Azure Data Box Disk“. In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Überprüfen des Datenuploads in Azure
> * Löschen von Daten vom Data Box-Datenträger

## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie zunächst sicher, dass Sie die Schritte unter [Tutorial: Zurücksenden von Azure Data Box Disk](data-box-disk-deploy-picked-up.md) ausgeführt haben.


## <a name="verify-data-upload-to-azure"></a>Überprüfen des Datenuploads in Azure

Nachdem die Datenträger vom Kurierdienst abgeholt wurden, wird der Auftragsstatus im Portal in **Picked up** (Abgeholt) geändert. Außerdem wird eine Nachverfolgungs-ID angezeigt.

![Datenträger abgeholt](media/data-box-disk-deploy-picked-up/data-box-portal-pickedup.png)

Nachdem der Datenträger bei Microsoft eingegangen ist und gescannt wurde, wird der Auftragsstatus in **Received** (Empfangen) geändert. 

![Datenträger empfangen](media/data-box-disk-deploy-picked-up/data-box-portal-received.png)

Die Daten werden automatisch kopiert, nachdem die Datenträger im Azure-Rechenzentrum mit einem Server verbunden wurden. Je nach Datengröße kann der Kopiervorgang einige Stunden oder auch einige Tage dauern. Sie können den Status des Kopiervorgangs im Portal verfolgen.

Nachdem der Kopiervorgang abgeschlossen ist, wird der Auftragsstatus in **Completed** (Abgeschlossen) geändert.

![Kopieren der Daten abgeschlossen](media/data-box-disk-deploy-picked-up/data-box-portal-completed.png)

Wenn beim Kopieren Fehler auftreten, finden Sie weitere Informationen unter [Problembehandlung bei Uploadfehlern ](data-box-disk-troubleshoot-upload.md).

Stellen Sie sicher, dass sich Ihre Daten in den Speicherkonten befinden, bevor Sie sie aus der Quelle löschen. Ihre Daten können sich an folgenden Orten befinden:

- In Ihren Azure Storage-Konten. Wenn Sie die Daten in Data Box kopieren, werden die Daten abhängig vom Typ in einen der folgenden Pfade in Ihrem Azure Storage-Konto hochgeladen:

  - Blockblobs und Seitenblobs: `https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt`
  - Azure Files: `https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt`

    Alternativ hierzu können Sie auch im Azure-Portal auf Ihr Azure-Speicherkonto zugreifen und von dort aus entsprechend navigieren.

- In Ihren Ressourcengruppen für verwaltete Datenträger. Beim Erstellen von verwalteten Datenträgern werden die VHDs als Seitenblobs hochgeladen und dann in verwaltete Datenträger konvertiert. Die verwalteten Datenträger werden an die Ressourcengruppen angefügt, die zum Zeitpunkt der Auftragserstellung angegeben waren.

  - Wenn der Kopiervorgang auf verwaltete Datenträger in Azure erfolgreich war, können Sie im Azure-Portal zu **Auftragsdetails** navigieren und sich die Ressourcengruppen notieren, die für verwaltete Datenträger angegeben sind.

      ![Anzeigen der Auftragsdetails](media/data-box-disk-deploy-picked-up/order-details-resource-group.png)

    Wechseln Sie zu der Ressourcengruppe, die Sie notiert haben, und suchen Sie Ihre verwalteten Datenträger.

      ![Ressourcengruppe für verwaltete Datenträger](media/data-box-disk-deploy-picked-up/resource-group-attached-managed-disk.png)

    > [!NOTE]
    > Wird ein Seitenblob während eines Datenkopiervorgangs nicht in einen verwalteten Datenträger konvertiert, bleibt es im Speicherkonto, und Ihnen wird der Speicher in Rechnung gestellt.

  -  Wenn Sie eine VHDX oder eine dynamische oder differenzierende VHD kopiert haben, wird die VHDX bzw. VHD als Blockblob in das Stagingspeicherkonto hochgeladen. Wechseln Sie zu Ihrem **Stagingspeicherkonto > Blobs**, und wählen Sie den geeigneten Container aus: StandardSSD, StandardHDD oder PremiumSSD. Die VHDX/VHDs sollten als Blockblobs in Ihrem Stagingspeicherkonto angezeigt werden.
  

  
::: zone-end

::: zone target="chromeless"

## <a name="verify-data-upload-to-azure"></a>Überprüfen des Datenuploads in Azure

Nachdem die Daten in Azure hochgeladen wurden, überprüfen Sie, ob sich Ihre Daten in den Speicherkonten befinden, bevor Sie sie aus der Quelle löschen. Ihre Daten können sich an folgenden Orten befinden:

- In Ihren Azure Storage-Konten. Wenn Sie die Daten in Data Box kopieren, werden die Daten abhängig vom Typ in einen der folgenden Pfade in Ihrem Azure Storage-Konto hochgeladen:

    - **Blockblobs und Seitenblobs**: `https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt`

    - **Azure Files**: `https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt`

- In Ihren Ressourcengruppen für verwaltete Datenträger. Beim Erstellen von verwalteten Datenträgern werden die VHDs als Seitenblobs hochgeladen und dann in verwaltete Datenträger konvertiert. Die verwalteten Datenträger werden an die Ressourcengruppen angefügt, die zum Zeitpunkt der Auftragserstellung angegeben waren.

::: zone-end

Führen Sie die folgenden Schritte aus, um sich zu vergewissern, dass die Daten in Azure hochgeladen wurden:

1. Navigieren Sie zu dem Speicherkonto, das Ihrem Datenträgerauftrag zugeordnet ist.
2. Navigieren Sie zu **Blob-Dienst > Blobs durchsuchen**. Die Liste mit den Containern wird angezeigt. Gemäß dem Unterordner, den Sie unter den Ordnern *BlockBlob* und *PageBlob* erstellt haben, werden unter Ihrem Speicherkonto Container mit demselben Namen erstellt.
    Falls die Ordnernamen nicht den Azure-Namenskonventionen entsprechen, tritt beim Hochladen der Daten nach Azure ein Fehler auf.

3. Verwenden Sie Microsoft Azure Storage-Explorer, um zu überprüfen, ob das gesamte Dataset geladen wurde. Fügen Sie das Speicherkonto an, das der Data Box Disk-Bestellung entspricht, und sehen Sie sich die Liste mit den Blobcontainern an. Wählen Sie einen Container aus, und klicken Sie auf **…Mehr** und dann auf **Folder statistics** (Ordnerstatistik). Im Bereich **Aktivitäten** wird die Statistik für diesen Ordner angezeigt, z.B. die Anzahl von Blobs und die Gesamtgröße der Blobs. Die Gesamtgröße der Blobs in Byte sollte mit der Größe des Datasets übereinstimmen.

    ![Ordnerstatistik in Storage-Explorer](media/data-box-disk-deploy-picked-up/folder-statistics-storage-explorer.png)

## <a name="erasure-of-data-from-data-box-disk"></a>Löschen von Daten vom Data Box-Datenträger

Nachdem die Daten in Azure hochgeladen wurden, löscht Data Box Disk die Daten auf den Datenträgern gemäß dem Standard [NIST SP 800-88](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).

::: zone target="docs"

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Informationen zu Azure Data Box-Datenträgern erhalten, z.B.:

> [!div class="checklist"]
> * Überprüfen des Datenuploads in Azure
> * Löschen von Daten vom Data Box-Datenträger


Fahren Sie mit dem nächsten Artikel zur Vorgehensweise fort, um zu erfahren, wie Sie den Data Box-Datenträger über das Azure-Portal verwalten.

> [!div class="nextstepaction"]
> [Verwalten von Data Box Disk über das Azure-Portal](./data-box-portal-ui-admin.md)

::: zone-end




