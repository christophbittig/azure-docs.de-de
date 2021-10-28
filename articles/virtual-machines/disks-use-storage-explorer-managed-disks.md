---
title: Verwenden von Azure Storage-Explorer zum Verwalten von verwalteten Azure-Datenträgern
description: Erfahren Sie, wie Sie mit Azure Storage-Explorer einen verwalteten Azure-Datenträger regionsübergreifend hochladen, herunterladen und migrieren sowie eine Momentaufnahme eines verwalteten Datenträgers erstellen können.
author: roygara
ms.author: rogarana
ms.date: 09/07/2021
ms.topic: how-to
ms.service: storage
ms.subservice: disks
ms.openlocfilehash: 3206eb5b7092a43561cd114082ac567a63bff61b
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130237926"
---
# <a name="use-azure-storage-explorer-to-manage-azure-managed-disks"></a>Verwenden von Azure Storage-Explorer zum Verwalten von verwalteten Azure-Datenträgern

**Gilt für:** :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows-VMs :heavy_check_mark: Flexible Skalierungsgruppen :heavy_check_mark: Einheitliche Skalierungsgruppen

Der Azure Storage-Explorer bietet umfangreiche Funktionen, mit denen Sie folgende Aufgaben ausführen können:

- Hochladen, Herunterladen und Kopieren von verwalteten Datenträgern.
- Erstellen von Momentaufnahmen der virtuellen Festplatte des Betriebssystems oder Datenträgers.
- Migrieren von Daten aus der lokalen Implementierung in Azure.
- Migrieren von Daten innerhalb von Azure-Regionen.

## <a name="prerequisites"></a>Voraussetzungen

Damit Sie die Schritte in diesem Artikel ausführen können, benötigen Sie Folgendes:

- Ein Azure-Abonnement.
- Mindestens einen verwalteten Azure-Datenträger.
- Die neueste Version des [Azure Storage-Explorers](https://azure.microsoft.com/features/storage-explorer/).

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="connect-to-an-azure-subscription"></a>Herstellen einer Verbindung mit einem Azure-Abonnement

Wenn Ihr Storage-Explorer nicht mit Azure verbunden ist, können Sie ihn nicht zum Verwalten von Ressourcen verwenden. Führen Sie die Schritte in diesem Abschnitt aus, um den Storage-Explorer mit Ihrem Azure-Konto zu verbinden. Anschließend können Sie den Storage-Explorer zum Verwalten Ihrer Datenträger verwenden.

1. Öffnen Sie den Azure Storage-Explorer, und wählen Sie in der Symbolleiste das Symbol **Verbinden** aus.

    [![Screenshot: Azure Storage-Explorer mit Darstellung des Symbols „Verbinden“.](media/disks-upload-vhd-to-managed-disk-storage-explorer/plug-in-icon-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/plug-in-icon-lrg.png#lightbox)

1. Wählen Sie im Dialogfeld **Mit Azure Storage verbinden** die Option **Abonnement** aus.

    [![Screenshot: Azure Storage-Explorer mit hervorgehobener Option „Abonnement“.](media/disks-upload-vhd-to-managed-disk-storage-explorer/connect-to-azure-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/connect-to-azure-lrg.png#lightbox)

1. Wählen Sie die gewünschte Umgebung und dann **Weiter** aus. Sie können auch **Benutzerdefinierte Umgebungen verwalten** auswählen, um eine benutzerdefinierte Umgebung zu konfigurieren und hinzuzufügen.

    [![Screenshot: Azure Storage-Explorer mit hervorgehobener Option „Azure-Umgebung“.](media/disks-upload-vhd-to-managed-disk-storage-explorer/choose-environment-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/choose-environment-lrg.png#lightbox)

1. Geben Sie im Dialogfeld **Anmeldung** Ihre Azure-Anmeldeinformationen ein.

    ![Screenshot zur Darstellung des Dialogfelds „Anmeldung“.](media/disks-upload-vhd-to-managed-disk-storage-explorer/sign-in.png)

1. Wählen Sie in der Liste Ihr Abonnement und dann **Explorer öffnen** aus.

    [![Screenshot: Azure Storage-Explorer mit hervorgehobener Schaltfläche „Explorer öffnen“.](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-subscription-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-subscription-lrg.png#lightbox)

## <a name="upload-an-on-premises-vhd"></a>Hochladen einer lokalen VHD

Sie können eine lokale VHD-Datei (virtuelle Festplatte) in Azure hochladen und damit ein Image erstellen. Führen Sie die Schritte in diesem Abschnitt aus, um Ihre Quelldatei hochzuladen.

1. Erweitern Sie im Bereich **Explorer** den Eintrag **Datenträger**, und wählen Sie die Ressourcengruppe aus, in die Ihr Datenträger hochgeladen werden soll.

    [![Screenshot: Azure Storage-Explorer mit hervorgehobenem Knoten „Datenträger“ zum Hochladen eines Datenträgers.](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1-lrg.png#lightbox)

1. Wählen Sie im Detailbereich der Ressourcengruppe den Eintrag **Hochladen** aus.

    [![Screenshot: Azure Storage-Explorer mit hervorgehobener Schaltfläche „Hochladen“.](media/disks-upload-vhd-to-managed-disk-storage-explorer/upload-button-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/upload-button-lrg.png#lightbox)

1. Geben Sie im Dialogfeld **VHD hochladen** Ihre VHD-Quelldatei, den Namen des Datenträgers, den Betriebssystemtyp, die Region zum Hochladen des Datenträgers und den Kontotyp an. Wenn die Region Verfügbarkeitszonen unterstützt, können Sie eine Zone Ihrer Wahl auswählen. Wählen Sie **Erstellen** aus. Daraufhin wird der Datenträger hochgeladen.

    [![Screenshot: Dialogfeld „VHD hochladen“ im Azure Storage-Explorer.](media/disks-upload-vhd-to-managed-disk-storage-explorer/upload-vhd-dialog-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/upload-vhd-dialog-lrg.png#lightbox)

1. Der Uploadstatus wird nun in **Aktivitäten** angezeigt.

    [![Screenshot: Azure Storage-Explorer mit hervorgehobenem Bereich „Aktivitäten“, der Meldungen zum Uploadstatus enthält.](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-uploading-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-uploading-lrg.png#lightbox)

Wenn der Uploadvorgang abgeschlossen wurde und der Datenträger nicht im Bereich **Aktivitäten** angezeigt wird, wählen Sie **Aktualisieren** aus.

## <a name="download-a-managed-disk"></a>Herunterladen eines verwalteten Datenträgers

Führen Sie die Schritte in diesem Abschnitt aus, um einen verwalteten Datenträger auf eine lokale VHD herunterzuladen. Der Status eines Datenträgers muss **Nicht angefügt** lauten, damit der Datenträger heruntergeladen werden kann.

1. Erweitern Sie im Bereich **Explorer** den Eintrag **Datenträger**, und wählen Sie die Ressourcengruppe aus, aus der Ihr Datenträger heruntergeladen werden soll.

    [![Screenshot: Azure Storage-Explorer mit hervorgehobenem Knoten „Datenträger“ zum Herunterladen von Datenträgern.](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1-dl-lrg.png#lightbox)

1. Wählen Sie im Detailbereich der Ressourcengruppe den Datenträger aus, den Sie herunterladen möchten.
1. Wählen Sie **Herunterladen** und dann den Speicherort aus, an dem Sie den Datenträger speichern möchten.

    [![Screenshot: Azure Storage-Explorer mit hervorgehobener Schaltfläche „Herunterladen“.](media/disks-upload-vhd-to-managed-disk-storage-explorer/download-button-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/download-button-lrg.png#lightbox)

1. Wählen Sie **Speichern** aus, um den Download zu starten. Der Downloadstatus wird im Bereich **Aktivitäten** angezeigt.

    [![Screenshot: Azure Storage-Explorer mit hervorgehobenem Bereich „Aktivitäten“, der Meldungen zum Downloadstatus enthält.](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-downloading-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-downloading-lrg.png#lightbox)

## <a name="copy-a-managed-disk"></a>Kopieren eines verwalteten Datenträgers

Mit Storage-Explorer können Sie einen verwalteten Datenträger innerhalb einer Region oder regionsübergreifend kopieren. So kopieren Sie einen Datenträger:

1. Erweitern Sie im Bereich **Explorer** die Dropdownliste **Datenträger**, und wählen Sie die Ressourcengruppe mit dem Datenträger aus, der kopiert werden soll.

    [![Screenshot: Azure Storage-Explorer mit hervorgehobenem Knoten „Datenträger“ zum Kopieren eines Datenträgers.](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1-lrg.png#lightbox)

1. Wählen Sie im Bereich mit den Details der Ressourcengruppe den Datenträger aus, den Sie kopieren möchten, und wählen Sie **Kopieren** aus.

    [![Screenshot: Azure Storage-Explorer mit hervorgehobener Schaltfläche „Kopieren“.](media/disks-upload-vhd-to-managed-disk-storage-explorer/copy-button-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/copy-button-lrg.png#lightbox)

1. Erweitern Sie im Bereich **Explorer** den Eintrag **Datenträger**, und wählen Sie die Ressourcengruppe aus, in die der Datenträger eingefügt werden soll.

    [![Screenshot: Azure Storage-Explorer mit hervorgehobenem Knoten „Datenträger“ zum Einfügen eines Datenträgers.](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg2-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg2-lrg.png#lightbox)

1. Wählen Sie im Detailbereich der Ressourcengruppe den Eintrag **Einfügen** aus.

    [![Screenshot: Azure Storage-Explorer mit hervorgehobener Schaltfläche „Einfügen“.](media/disks-upload-vhd-to-managed-disk-storage-explorer/paste-button-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/paste-button-lrg.png#lightbox)

1. Geben Sie im Dialogfeld **Datenträger einfügen** die entsprechenden Werte ein. In unterstützten Regionen können Sie auch eine Verfügbarkeitszone angeben.

    [![Screenshot: Formular zum Einfügen eines Datenträgers im Azure Storage-Explorer.](media/disks-upload-vhd-to-managed-disk-storage-explorer/paste-disk-dialog-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/paste-disk-dialog-lrg.png#lightbox)

1. Wählen Sie **Einfügen** aus, um den Kopiervorgang zu starten. Der Status wird unter **Aktivitäten** angezeigt.

    [![Screenshot: Azure Storage-Explorer mit hervorgehobenem Bereich „Aktivitäten“, der Statusmeldungen zum Kopieren und Einfügen enthält.](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-copying-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-copying-lrg.png#lightbox)

## <a name="create-a-snapshot"></a>Erstellen einer Momentaufnahme

1. Erweitern Sie im Bereich **Explorer** den Eintrag **Datenträger**, und wählen Sie die Ressourcengruppe mit dem Datenträger aus, für den eine Momentaufnahme erstellt werden soll.

    [![Screenshot: Azure Storage-Explorer mit hervorgehobenem Knoten „Datenträger“ zum Erstellen von Datenträgermomentaufnahmen.](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1-dl-lrg.png#lightbox)

1. Wählen Sie im Bereich mit den Details der Ressourcengruppe den Datenträger aus, für den Sie eine Momentaufnahme erstellen möchten, und wählen Sie **Momentaufnahme erstellen** aus.

    [![Screenshot: Azure Storage-Explorer mit hervorgehobener Schaltfläche „Momentaufnahme erstellen“.](media/disks-upload-vhd-to-managed-disk-storage-explorer/create-snapshot-button-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/create-snapshot-button-lrg.png#lightbox)

1. Geben Sie in **Momentaufnahme erstellen** den Namen der Momentaufnahme sowie die Ressourcengruppe an, in der die Momentaufnahme erstellt werden soll. Wählen Sie **Erstellen** aus.

    [![Screenshot: Dialogfeld „Momentaufnahme erstellen“ im Azure Storage-Explorer.](media/disks-upload-vhd-to-managed-disk-storage-explorer/create-snapshot-dialog-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/create-snapshot-dialog-lrg.png#lightbox)

1. Nachdem die Momentaufnahme erstellt wurde, können Sie in **Aktivitäten** die Option **Im Portal öffnen** auswählen, um die Momentaufnahme im Azure-Portal anzuzeigen.

    [![Screenshot: Azure Storage-Explorer mit hervorgehobenem Link im Bereich „Aktivitäten“, der Statusmeldungen zur Momentaufnahme enthält.](media/disks-upload-vhd-to-managed-disk-storage-explorer/open-in-portal-sml.png)](media/disks-upload-vhd-to-managed-disk-storage-explorer/open-in-portal-lrg.png#lightbox)

## <a name="next-steps"></a>Nächste Schritte

- [Erstellen eines virtuellen Computers anhand einer VHD mithilfe des Azure-Portals](./windows/create-vm-specialized-portal.md)
- [Anfügen eines verwalteten Datenträgers an einen virtuellen Windows-Computer im Azure-Portal](./windows/attach-managed-disk-portal.md)