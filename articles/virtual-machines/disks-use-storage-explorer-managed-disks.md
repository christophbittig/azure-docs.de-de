---
title: Verwenden von Azure Storage-Explorer zum Verwalten von verwalteten Azure-Datenträgern
description: Erfahren Sie, wie Sie mit Azure Storage-Explorer einen verwalteten Azure-Datenträger regionsübergreifend hochladen, herunterladen und migrieren sowie eine Momentaufnahme eines verwalteten Datenträgers erstellen können.
author: roygara
ms.author: rogarana
ms.date: 09/25/2019
ms.topic: how-to
ms.service: storage
ms.subservice: disks
ms.openlocfilehash: 1ef24210e033c5e0af623dfa6f3cd79146732640
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122692293"
---
# <a name="use-azure-storage-explorer-to-manage-azure-managed-disks"></a>Verwenden von Azure Storage-Explorer zum Verwalten von verwalteten Azure-Datenträgern

**Gilt für:** :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows-VMs :heavy_check_mark: Flexible Skalierungsgruppen :heavy_check_mark: Einheitliche Skalierungsgruppen

Storage-Explorer 1.10.0 ermöglicht Benutzern das Hochladen, Herunterladen und Kopieren verwalteter Datenträger sowie das Erstellen von Momentaufnahmen. Aufgrund dieser zusätzlichen Funktionen können Sie Storage-Explorer zum Migrieren von Daten an einem lokalen Standort nach Azure und zum Migrieren von Daten zwischen Azure-Regionen verwenden.

## <a name="prerequisites"></a>Voraussetzungen

Damit Sie diesen Artikel nachvollziehen können, benötigen Sie Folgendes:
- Ein Azure-Abonnement
- Mindestens einen verwalteten Azure-Datenträger
- Die neueste Version von [Azure Storage-Explorer](https://azure.microsoft.com/features/storage-explorer/)

## <a name="connect-to-an-azure-subscription"></a>Herstellen einer Verbindung mit einem Azure-Abonnement

Wenn Storage-Explorer über keine Verbindung zu Azure verfügt, können Sie Storage-Explorer nicht zum Verwalten von Ressourcen verwenden. In diesem Abschnitt wird das Herstellen einer Verbindung zu Ihrem Azure-Konto übergangen, damit Sie Ressourcen mithilfe von Storage-Explorer verwalten können.

1. Starten Sie Azure Storage-Explorer und klicken Sie auf das **Plug-in**-Symbol auf der linken Seite.

    ![Klicken auf das Plug-in-Symbol](media/disks-upload-vhd-to-managed-disk-storage-explorer/plug-in-icon.png)

1. Wählen Sie **Azure-Konto hinzufügen** aus und klicken Sie dann auf **Weiter**.

    ![Hinzufügen eines Azure-Kontos](media/disks-upload-vhd-to-managed-disk-storage-explorer/connect-to-azure.png)

1. Geben Sie im Dialogfeld **Azure-Anmeldung** ihre Azure-Anmeldeinformationen ein.

    ![Azure-Anmeldedialogfeld](media/disks-upload-vhd-to-managed-disk-storage-explorer/sign-in.png)

1. Wählen Sie Ihr Abonnement in der Liste aus, und klicken Sie dann auf **Anwenden**.

    ![Wählen Sie Ihr Abonnement aus.](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-subscription.png)

## <a name="upload-a-managed-disk-from-an-on-prem-vhd"></a>Hochladen eines verwalteten Datenträgers von einer lokalen VHD

1. Erweitern Sie im linken Bereich **Datenträger** und wählen Sie die Ressourcengruppe aus, in die der Datenträger hochgeladen werden soll.

    ![Auswahl von Ressourcengruppe 1](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. Wählen Sie **Hochladen** aus.

    ![Auswählen von „Hochladen“](media/disks-upload-vhd-to-managed-disk-storage-explorer/upload-button.png)

1. Geben Sie in **VHD hochladen** die Quell-VHD, den Namen des Datenträgers, den Betriebssystemtyp, die Region, in die Sie den Datenträger hochladen möchten, sowie den Kontotyp an. In einigen Regionen werden Verfügbarkeitszonen unterstützt. Für diese Regionen können Sie wahlfrei eine Zone auswählen.
1. Wählen Sie **Erstellen** aus. Daraufhin wird der Datenträger hochgeladen.

    ![Dialogfeld „VHD hochladen“](media/disks-upload-vhd-to-managed-disk-storage-explorer/upload-vhd-dialog.png)

1. Der Uploadstatus wird nun in **Aktivitäten** angezeigt.

    ![Uploadstatus](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-uploading.png)

1. Wenn der Upload abgeschlossen wurde und der Datenträger nicht im rechten Bereich angezeigt wird, wählen Sie **Aktualisieren** aus.

## <a name="download-a-managed-disk"></a>Herunterladen eines verwalteten Datenträgers

In den folgenden Schritten wird erläutert, wie ein verwalteter Datenträger auf eine lokale VHD heruntergeladen wird. Der Status eines Datenträgers muss **Nicht angefügt** sein, damit er heruntergeladen werden kann. Sie können Sie einen **angefügten** Datenträger nicht herunterladen.

1. Erweitern Sie im linken Bereich ggf. **Datenträger** und wählen Sie die Ressourcengruppe aus, aus der Sie den Datenträger herunterladen möchten.

    ![Auswahl von Ressourcengruppe 1](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. Wählen Sie im rechten Bereich den Datenträger aus, den Sie herunterladen möchten.
1. Wählen Sie **Herunterladen** und dann den Speicherort aus, an dem Sie den Datenträger speichern möchten.

    ![Herunterladen eines verwalteten Datenträgers](media/disks-upload-vhd-to-managed-disk-storage-explorer/download-button.png)

1. Wählen Sie **Speichern** aus. Daraufhin wird der Datenträger heruntergeladen. Der Downloadstatus wird in **Aktivitäten** angezeigt.

    ![Downloadstatus](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-downloading.png)

## <a name="copy-a-managed-disk"></a>Kopieren eines verwalteten Datenträgers

Mit Storage-Explorer können Sie einen verwalteten Datenträger innerhalb einer Region oder regionsübergreifend kopieren. So kopieren Sie einen Datenträger:

1. Wählen Sie in der Dropdown-Liste **Datenträger** auf der linken Seite die Ressourcengruppe aus, die den Datenträger enthält, den Sie kopieren möchten.

    ![Auswahl von Ressourcengruppe 1](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. Wählen Sie im rechten Bereich den Datenträger aus, den Sie kopieren möchten, und wählen Sie **Kopieren** aus.

    ![Kopieren eines verwalteten Datenträgers](media/disks-upload-vhd-to-managed-disk-storage-explorer/copy-button.png)

1. Wählen Sie im linken Bereich die Ressourcengruppe aus, in die der Datenträger eingefügt werden soll.

    ![Auswahl von Ressourcengruppe 2](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg2.png)

1. Wählen Sie im rechten Bereich **Einfügen** aus.

    ![Einfügen eines verwalteten Datenträgers](media/disks-upload-vhd-to-managed-disk-storage-explorer/paste-button.png)

1. Geben Sie im Dialogfeld **Datenträger einfügen** die entsprechenden Werte ein. Sie können in unterstützten Regionen auch eine Verfügbarkeitszone angeben.

    ![Dialogfeld „Datenträger einfügen“](media/disks-upload-vhd-to-managed-disk-storage-explorer/paste-disk-dialog.png)

1. Wählen Sie **Einfügen** aus. Daraufhin wird der Datenträger kopiert. Der Status wird in **Aktivitäten** angezeigt.

    ![Status von Kopieren/Einfügen](media/disks-upload-vhd-to-managed-disk-storage-explorer/activity-copying.png)

## <a name="create-a-snapshot"></a>Erstellen einer Momentaufnahme

1. Wählen Sie in der Dropdown-Liste **Datenträger** auf der linken Seite die Ressourcengruppe aus, die den Datenträger enthält, für den Sie eine Momentaufnahme erstellen möchten.

    ![Auswahl von Ressourcengruppe 1](media/disks-upload-vhd-to-managed-disk-storage-explorer/select-rg1.png)

1. Wählen Sie auf der rechten Seite den Datenträger aus, für den Sie eine Momentaufnahme erstellen möchten, und wählen Sie **Momentaufnahme erstellen** aus.

    ![Erstellen einer Momentaufnahme](media/disks-upload-vhd-to-managed-disk-storage-explorer/create-snapshot-button.png)

1. Geben Sie in **Momentaufnahme erstellen** den Namen der Momentaufnahme sowie die Ressourcengruppe an, in der die Momentaufnahme erstellt werden soll. Klicken Sie anschließend auf **Erstellen**.

    ![Dialogfeld „Momentaufnahme erstellen“](media/disks-upload-vhd-to-managed-disk-storage-explorer/create-snapshot-dialog.png)

1. Nachdem die Momentaufnahme erstellt wurde, können Sie **Im Portal öffnen** in **Aktivitäten** auswählen, um die Momentaufnahme im Azure-Portal anzuzeigen.

    ![Öffnen einer Momentaufnahme im Portal](media/disks-upload-vhd-to-managed-disk-storage-explorer/open-in-portal.png)

## <a name="next-steps"></a>Nächste Schritte


Erfahren Sie, wie ein [virtueller Computer anhand einer VHD mithilfe des Azure-Portals erstellt](windows/create-vm-specialized-portal.md) wird.

Erfahren Sie, wie Sie [im Azure-Portal einen verwalteten Datenträger an einen virtuellen Windows-Computer anfügen](windows/attach-managed-disk-portal.md).