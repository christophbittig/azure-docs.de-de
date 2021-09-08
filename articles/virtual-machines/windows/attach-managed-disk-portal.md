---
title: Anfügen eines verwalteten Datenträgers an einen virtuellen Windows-Computer – Azure
description: Hier erfahren Sie, wie Sie im Azure-Portal einen verwalteten Datenträger an einen virtuellen Windows-Computer anfügen.
author: roygara
ms.service: virtual-machines
ms.collection: windows
ms.topic: how-to
ms.date: 02/06/2020
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: ba6691e343276445bd881d2cbe2f8fcb5d1d314f
ms.sourcegitcommit: d9a2b122a6fb7c406e19e2af30a47643122c04da
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/24/2021
ms.locfileid: "114666525"
---
# <a name="attach-a-managed-data-disk-to-a-windows-vm-by-using-the-azure-portal"></a>Anfügen eines verwalteten Datenträgers an einen virtuellen Windows-Computer im Azure-Portal

In diesem Artikel wird beschrieben, wie Sie im Azure-Portal einen neuen verwalteten Datenträger an einen virtuellen Windows-Computer (Virtual Machine, VM) anfügen. Die Größe des virtuellen Computers bestimmt, wie viele Datenträger Sie anfügen können. Weitere Informationen finden Sie unter [Größen für virtuelle Computer](../sizes.md).


## <a name="add-a-data-disk"></a>Hinzufügen eines Datenträgers

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Suchen Sie nach **Virtuelle Computer**, und wählen Sie diese Option aus.
1. Wählen Sie in der Liste einen virtuellen Computer aus.
1. Wählen Sie im Bereich **Virtueller Computer** die Option **Datenträger** aus.
1. Wählen Sie im Bereich **Datenträger** die Option **Neuen Datenträger erstellen und anfügen** aus.
1. Legen Sie in den Dropdownlisten für den neuen Datenträger die gewünschten Optionen fest, und benennen Sie den Datenträger.
1. Wählen Sie **Speichern** aus, um den neuen Datenträger zu erstellen und an die VM anzufügen.

## <a name="initialize-a-new-data-disk"></a>Initialisieren eines neuen Datenträgers

1. Stellen Sie eine Verbindung zur VM her.
1. Wählen Sie auf dem ausgeführten virtuellen Computer das **Windows-Startmenü** aus, und geben Sie im Suchfeld den Namen **diskmgmt.msc** ein. Die **Datenträgerverwaltungskonsole** wird geöffnet.
1. Die Datenträgerverwaltung erkennt, dass ein neuer nicht initialisierter Datenträger vorhanden ist, und deshalb wird das Fenster **Datenträger initialisieren** angezeigt.
1. Vergewissern Sie sich, dass der neue Datenträger ausgewählt ist, und wählen Sie dann **OK** aus, um ihn zu initialisieren.
1. Der neue Datenträger wird als **Nicht zugeordnet** angezeigt. Klicken Sie mit der rechten Maustaste auf den Datenträger, und wählen **Neues einfaches Volume** aus. Das Fenster **Assistent zum Erstellen neuer einfacher Volumes** wird geöffnet.
1. Führen Sie die Schritte des Assistenten aus, und übernehmen Sie dabei alle Standardeinstellungen. Wählen Sie abschließend **Fertig stellen** aus.
1. Schließen Sie die **Datenträgerverwaltung**.
1. Ein Popupfenster wird angezeigt, in dem Sie darüber informiert werden, dass Sie den neuen Datenträger formatieren müssen, bevor Sie ihn verwenden können. Wählen Sie **Datenträger formatieren** aus.
1. Überprüfen Sie im Fenster **Neuen Datenträger formatieren** die Einstellungen, und wählen Sie dann **Starten** aus.
1. Es wird eine Warnung angezeigt, die Sie darüber informiert, dass bei der Formatierung des Datenträgers alle Daten gelöscht werden. Klicken Sie auf **OK**.
1. Wählen Sie nach Abschluss der Formatierung die Option **OK** aus.

## <a name="next-steps"></a>Nächste Schritte

- Sie können auch [einen Datenträger mithilfe von PowerShell anfügen](attach-disk-ps.md).
- Wenn Ihre Anwendung Laufwerk *D:* für die Datenspeicherung verwenden muss, können Sie [den Laufwerkbuchstaben des temporären Windows-Datenträgers ändern](change-drive-letter.md).