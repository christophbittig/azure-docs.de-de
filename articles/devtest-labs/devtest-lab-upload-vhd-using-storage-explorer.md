---
title: Hochladen einer VHD-Datei mithilfe von Storage-Explorer
description: Hochladen einer VHD-Datei mithilfe von Microsoft Azure Storage-Explorer in ein DevTest Labs-Lab-Speicherkonto.
ms.topic: how-to
ms.date: 11/05/2021
ms.openlocfilehash: 2531964c056ddbed38da435e16bde3e0f5e1eff9
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132054056"
---
# <a name="upload-a-vhd-file-to-a-labs-storage-account-by-using-storage-explorer"></a>Hochladen einer VHD-Datei in das Speicherkonto eines Labs mit Storage-Explorer

[!INCLUDE [devtest-lab-upload-vhd-selector](../../includes/devtest-lab-upload-vhd-selector.md)]

In Azure DevTest Labs können Sie VHD-Dateien verwenden, um benutzerdefinierte Images für die Bereitstellung von VMs zu erstellen. Dieser Artikel beschreibt, wie Sie [Microsoft Azure Storage-Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) verwenden, um eine VHD-Datei in das Speicherkonto eines Labs hochzuladen. Nachdem Sie die VHD-Datei in DevTest Labs hochgeladen haben, können Sie aus der hochgeladenen Datei ein [benutzerdefiniertes Image](devtest-lab-create-custom-image-from-vhd-using-powershell.md) erstellen. Weitere Informationen zu Datenträgern und VHDs in Azure finden Sie unter [Einführung in verwaltete Datenträger](../virtual-machines/managed-disks-overview.md).

Der Storage-Explorer unterstützt verschiedene Verbindungsoptionen. Dieser Abschnitt beschreibt das Herstellen einer Verbindung mit einem Speicherkonto, das mit Ihrem Azure-Abonnement verknüpft ist. Informationen zu anderen Verbindungsoptionen von Storage-Explorer finden sie unter [Erste Schritte mit Storage-Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md).

## <a name="prerequisites"></a>Voraussetzungen

- [Laden Sie die neueste Version von Microsoft Azure Storage-Explorer herunter, und installieren Sie sie](https://www.storageexplorer.com).

- Rufen Sie den Namen des Lab-Speicherkontos im Azure-Portal ab:

  1. Suchen Sie im [Azure-Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) nach **DevTest Labs**, wählen Sie diese Option aus, und wählen Sie dann Ihr Lab aus der Liste aus.
  1. Wählen Sie auf der Lab-Seite die Option **Konfiguration und Richtlinien** aus. 
  1. Wählen Sie auf der Seite **Konfiguration und Richtlinien** unter **Basen virtueller Computer** die Option **Benutzerdefinierte Images** aus.
  1. Wählen Sie auf der Seite **Benutzerdefinierte Images** die Option **Hinzufügen** aus. 
  1. Wählen Sie auf der Seite **Benutzerdefiniertes Image** unter **VHD** den Link **VHD mit PowerShell hochladen** aus.
     ![Screenshot: Link „VHD mit PowerShell hochladen“.](media/devtest-lab-upload-vhd-using-storage-explorer/upload-image-using-psh.png)
  1. Auf der Seite **Image mit PowerShell hochladen** ein Image erstellen, zeigt der `Destination`-Parameter im Aufruf des Cmdlets **Add-AzureVhd** den Speicherkontonamen des Labs im folgenden Format an: `https://<STORAGE-ACCOUNT-NAME>.blob.core.windows.net/uploads/`.
  1. Kopieren Sie den Namen des Speicherkontos, um ihn in den folgenden Schritten zu verwenden.

## <a name="step-by-step-instructions"></a>Schrittweise Anweisungen

1. Wenn Sie Storage-Explorer öffnen, werden im linken **Explorer**-Bereich alle Azure-Abonnements angezeigt, bei denen Sie angemeldet sind.

   Wenn Sie ein anderes Konto hinzufügen müssen, wählen Sie das Symbol **Kontoverwaltung** aus, und wählen Sie im Bereich **Kontoverwaltung** die Option **Konto hinzufügen** aus.

   ![Screenshot: „Konto hinzufügen“ im Bereich „Kontoverwaltung“.](media/devtest-lab-upload-vhd-using-storage-explorer/add-account-link.png)

   Befolgen Sie die Anweisungen, um sich mit dem Microsoft-Konto anzumelden, das Ihrem Azure-Abonnement zugeordnet ist.

1. Nach der Anmeldung werden im **Explorer**-Bereich die Azure-Abonnements angezeigt, die Ihrem Konto zugeordnet sind. Wählen Sie den Dropdownpfeil neben dem Azure-Abonnement aus, das Sie verwenden möchten. Im linken Bereich werden die Speicherkonten angezeigt, die mit dem ausgewählten Azure-Abonnement verknüpft sind.

   ![Screenshot: Speicherkonten für ein ausgewähltes Azure-Abonnement.](media/devtest-lab-upload-vhd-using-storage-explorer/storage-accounts-list.png)

1. Wählen Sie den Dropdownpfeil neben dem Lab-Speicherkontonamen aus, den Sie zuvor gespeichert haben.

1. Erweitern Sie den Knoten **Blobcontainer**, und wählen Sie dann **uploads** aus.

   ![Screenshot: Erweiterter Knoten „Blobcontainer“ mit dem Verzeichnis „uploads“.](media/devtest-lab-upload-vhd-using-storage-explorer/upload-dir.png)

1. Wählen Sie im rechten Bereich des Storage-Explorers auf der Symbolleiste des Blob-Editors **Hochladen** aus, und wählen Sie dann **Dateien hochladen** aus. 

   ![Screenshot: Schaltfläche „Hochladen“ und „Dateien hochladen“.](media/devtest-lab-upload-vhd-using-storage-explorer/upload-button.png)

1. Wählen Sie im Dialogfeld **Dateien hochladen** neben dem Feld **Ausgewählte Dateien** die Option **...** aus, navigieren Sie zur VHD-Datei auf Ihrem Computer, wählen Sie sie aus, und wählen Sie dann **Öffnen** aus.

1. Ändern Sie unter **Blobtyp** den **Blockblob** in **Seitenblob**.

1. Wählen Sie die Option **Hochladen**.

   ![Screenshot: Dialogfeld „Dateien hochladen“](media/devtest-lab-upload-vhd-using-storage-explorer/upload-file.png)

Im Bereich **Aktivitäten** unten wird der Uploadstatus angezeigt. Das Hochladen der VHD-Datei kann je nach Größe der VHD-Datei und der Verbindungsgeschwindigkeit sehr lange dauern.

![Screenshot: Bereich „Aktivitäten“ mit Uploadstatus.](media/devtest-lab-upload-vhd-using-storage-explorer/upload-status.png)

## <a name="next-steps"></a>Nächste Schritte

- [Erstellen eines benutzerdefinierten Images in Azure DevTest Labs aus einer VHD-Datei mit dem Azure-Portal](devtest-lab-create-template.md)
- [Erstellen eines benutzerdefinierten Azure DevTest Labs-Images aus einer VHD-Datei mithilfe von PowerShell](devtest-lab-create-custom-image-from-vhd-using-powershell.md)

