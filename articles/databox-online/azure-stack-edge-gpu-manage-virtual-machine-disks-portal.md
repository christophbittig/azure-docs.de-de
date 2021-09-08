---
title: Verwalten von VM-Datenträgern auf Azure Stack Edge Pro-GPU-, Pro R- und Mini R-Geräten über das Azure-Portal
description: Erfahren Sie, wie Sie mithilfe des Azure-Portals Datenträger für VMs verwalten, die auf Geräten vom Typ Azure Stack Edge Pro-GPU, Azure Stack Edge Pro R, and Azure Stack Edge Mini R bereitgestellt werden. Dies umfasst auch das Hinzufügen, Ändern der Größe, Trennen und Löschen von Datenträgern.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/02/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to manage disks on a VM running on an Azure Stack Edge Pro device so that I can use it to run applications using Edge compute before sending it to Azure.
ms.openlocfilehash: 8b9be47fb87c626bb5e6f2f20054f6cf249a57d0
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122355162"
---
# <a name="use-the-azure-portal-to-manage-disks-on-the-vms-on-your-azure-stack-edge-pro-gpu"></a>Verwenden des Azure-Portals zum Verwalten von Datenträgern auf VMs auf Azure Stack Edge Pro-GPU-Geräten

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Über das Azure-Portal können Sie Datenträger auf den virtuellen Computern (VMs) bereitstellen, die auf Ihrem Azure Stack Edge Pro-Gerät verfügbar gemacht werden. Die Datenträger werden über die lokale Azure Resource Manager-Instanz auf dem Gerät bereitgestellt und belegen Gerätekapazität. Vorgänge wie das Hinzufügen, Trennen und Löschen eines Datenträgers können über das Azure-Portal durchgeführt werden, das wiederum Aufrufe an die lokale Azure Resource Manager-Instanz sendet, um den Speicher bereitzustellen. 

In diesem Artikel wird erläutert, wie Sie über das Azure-Portal einer vorhandenen VM Datenträger hinzufügen oder Datenträger trennen, entfernen und löschen sowie die Größe der VM selbst ändern.

        
## <a name="about-disks-on-vms"></a>Informationen zu Datenträgern auf VMs

Ihre VM kann einen Datenträger für das Betriebssystem und einen Datenträger für Daten aufweisen. Jeder VM, die auf Ihrem Gerät bereitgestellt wird, ist ein Betriebssystemdatenträger angefügt. Dieser Betriebssystemdatenträger weist ein vorinstalliertes Betriebssystem auf, das beim Erstellen der VM ausgewählt wurde. Dieser Datenträger enthält das Startvolume.

> [!NOTE]
> Sie können die Größe des Betriebssystemdatenträgers für eine auf Ihrem Gerät bereitgestellte VM nicht ändern. Die Größe des Betriebssystemdatenträgers wird durch die von Ihnen ausgewählte VM-Größe bestimmt.

Ein Datenträger für Daten hingegen ist ein verwalteter Datenträger, der an die VM angefügt ist, die auf Ihrem Gerät ausgeführt wird. Ein solcher Datenträger wird verwendet, um Anwendungsdaten zu speichern. Datenträger für Daten sind in der Regel SCSI-Laufwerke. Die Größe der VM bestimmt, wie viele Datenträger für Daten Sie anfügen können. Standardmäßig wird Storage Premium zum Hosten der Datenträger verwendet.

Eine auf Ihrem Gerät bereitgestellte VM kann mitunter einen temporären Datenträger enthalten. Der temporäre Datenträger bietet kurzfristigen Speicher für Anwendungen und Prozesse und ist ausschließlich dafür ausgelegt, Daten wie z. B. Seiten- oder Auslagerungsdateien zu speichern. Daten auf dem temporären Datenträger können während eines Wartungsereignisses verloren gehen, oder wenn Sie eine VM erneut bereitstellen. Während eines erfolgreichen standardmäßigen Neustarts der VM bleiben die Daten auf dem temporären Datenträger erhalten. 


## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie damit beginnen, Datenträger auf den VMs auf Ihrem Gerät über das Azure-Portal zu verwalten, stellen Sie Folgendes sicher:


1. Sie haben Zugriff auf ein aktiviertes Azure Stack Edge Pro-GPU-Gerät. Sie haben auch eine Netzwerkschnittstelle für Compute auf Ihrem Gerät aktiviert. Durch diese Aktion wird ein virtueller Switch für diese Netzwerkschnittstelle auf der VM erstellt. 
    1. Navigieren Sie auf der lokalen Benutzeroberfläche Ihres Geräts zu **Compute**. Wählen Sie die Netzwerkschnittstelle aus, die Sie verwenden möchten, um einen virtuellen Switch zu erstellen.

        > [!IMPORTANT] 
        > Sie können nur einen Port für Compute konfigurieren.

    1. Aktivieren Sie Compute für die Netzwerkschnittstelle. Azure Stack Edge Pro-GPU erstellt und verwaltet einen virtuellen Switch, der dieser Netzwerkschnittstelle entspricht.

1. Auf Ihrem Gerät ist mindestens eine VM bereitgestellt. Informationen zum Erstellen dieser VM finden Sie in den Anweisungen unter [Bereitstellen von VMs auf einem Azure Stack Edge Pro-GPU-Gerät über das Azure-Portal](azure-stack-edge-gpu-deploy-virtual-machine-portal.md).


## <a name="add-a-data-disk"></a>Hinzufügen eines Datenträgers

Führen Sie die folgenden Schritte aus, um einen Datenträger zu einer VM hinzuzufügen, die auf Ihrem Gerät bereitgestellt wird.

1. Navigieren Sie zu dem virtuellen Computer, dem Sie einen Datenträger hinzufügen möchten, und wählen Sie unter **Details** für den virtuellen Computer die Option **Datenträger** aus.
    
    ![Screenshot der Einstellungen unter „Datenträger“ auf der Registerkarte „Details“ eines virtuellen Azure Stack Edge-Computers. Die Bezeichnung „Datenträger“ ist hervorgehoben.](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/add-data-disk-1.png)

1. Wählen Sie auf dem Blatt **Datenträger** unter **Datenträger** die Option **Neuen Datenträger erstellen und anfügen** aus.

    ![Screenshot des Blatts „Datenträger“ in der „Übersicht“ für virtuelle Computer. Die Option „Neuen Datenträger erstellen und anfügen“ unter „Datenträger“ ist hervorgehoben.](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/add-data-disk-2.png)

1. Geben Sie auf dem Blatt **Neuen Datenträger erstellen** die folgenden Parameter ein:

    
    |Feld  |BESCHREIBUNG  |
    |---------|---------|
    |Name     | Ein eindeutiger Name innerhalb der Ressourcengruppe. Nach Erstellung des Datenträgers kann der Name nicht mehr geändert werden.     |
    |Edgeressourcengruppe |Geben Sie die Edge-Ressourcengruppe ein, in der der neue Datenträger gespeichert werden soll.|
    |Size| Die Größe des Datenträgers in GiB. Die maximale Größe eines Datenträgers wird durch die von Ihnen ausgewählte VM-Größe bestimmt. Beim Bereitstellen eines Datenträgers sollten Sie auch den tatsächlichen Speicherplatz auf Ihrem Gerät und andere aktive VM-Workloads berücksichtigen, die Kapazität belegen.  |         

    ![Screenshot des Blatts „Neuen Datenträger erstellen“ für einen virtuellen Computer. Die Schaltfläche „OK“ ist hervorgehoben.](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/add-data-disk-3.png)

    Wählen Sie **OK** aus, und setzen Sie den Vorgang fort.

1. In der Anzeige **Datenträger** ist ein Eintrag zu sehen, der dem neuen Datenträger entspricht. Übernehmen Sie die Standardeinstellung, oder weisen Sie dem Datenträger eine gültige logische Gerätenummer (LUN) zu, und wählen Sie **Speichern** aus. Eine LUN ist ein eindeutiger Bezeichner für einen SCSI-Datenträger. Weitere Informationen finden Sie unter [Was ist eine logische Gerätenummer?](../virtual-machines/linux/azure-to-guest-disk-mapping.md#what-is-a-lun).

    ![Screenshot der Anzeige „Datenträger“ für virtuelle Computer nach dem Hinzufügen eines neuen Datenträgers. Die Datenträgernummer für den neuen Datenträger und die Schaltfläche „Speichern“ sind hervorgehoben.](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/add-data-disk-4.png)

1. Es wird eine Benachrichtigung angezeigt, dass der Datenträger erstellt wird. Nachdem der Datenträger erfolgreich erstellt wurde, wird die VM aktualisiert. 

    ![Screenshot der Benachrichtigung, dass der Datenträger erstellt wird.](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/add-data-disk-5.png)

1. Navigieren Sie zurück zur Seite **Details** des virtuellen Computers. Die Liste der Datenträger wird aktualisiert, um den neu erstellten Datenträger anzuzeigen.

    ![Screenshot der Registerkarte „Details“ für einen virtuellen Computer. Ein neu erstellter Datenträger für den virtuellen Computer ist im Bereich „Datenträger“ hervorgehoben.](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/add-data-disk-6.png)


## <a name="change-a-data-disk"></a>Ändern eines Datenträgers

Führen Sie die folgenden Schritte aus, um einen Datenträger zu ändern, der einer auf Ihrem Gerät bereitgestellten VM zugeordnet ist.

1. Navigieren Sie zu dem virtuellen Computer, dessen Datenträger geändert werden soll, und wählen Sie unter **Details** für den virtuellen Computer die Option **Datenträger** aus.

1. Wählen Sie in der Liste der Datenträger den Datenträger aus, den Sie ändern möchten. Wählen Sie ganz rechts neben dem ausgewählten Datenträger das Bearbeitungssymbol (Stift) aus.  

    ![Screenshot der Datenträger eines virtuellen Computers. Der Abschnitt „Datenträger“ und das Stiftsymbol, das zum Bearbeiten eines Datenträgers verwendet wird, sind hervorgehoben.](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/edit-data-disk-1.png)

1. Auf dem Blatt **Datenträger ändern** können Sie nur die Größe des Datenträgers ändern. Sie können den Namen eines Datenträgers nach seiner Erstellung nicht mehr ändern. Ändern Sie die **Größe** des Datenträgers, und speichern Sie die Änderung.

    ![Screenshot des Blatts „Datenträger ändern“ für einen virtuellen Computer. Die Option „Größe“ und die Schaltfläche „OK“ sind hervorgehoben.](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/edit-data-disk-2.png)

    > [!NOTE]
    > Sie können einen Datenträger nur erweitern. Ein Verkleinern des Datenträgers ist nicht möglich.

1. In der Anzeige **Datenträger** wird die Liste der Datenträger aktualisiert, um den aktualisierten Datenträger anzuzeigen.


## <a name="attach-an-existing-disk"></a>Anfügen eines vorhandenen Datenträgers

Führen Sie die folgenden Schritte aus, um einen vorhandenen Datenträger an die auf Ihrem Gerät bereitgestellte VM anzufügen.

1. Navigieren Sie zu dem virtuellen Computer, an den Sie den vorhandenen Datenträger anfügen möchten, und wählen Sie unter **Details** für den virtuellen Computer die Option **Datenträger** aus.

1. Wählen Sie auf dem Blatt **Datenträger** unter **Datenträger** die Option **Vorhandenen Datenträger anfügen** aus.

    ![Screenshot des Blatts „Datenträger“ für einen virtuellen Azure Stack Edge-Computer. Die Option „Vorhandenen Datenträger anfügen“ ist hervorgehoben.](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/attach-existing-data-disk-1.png)

1. Übernehmen Sie die Standard-LUN, oder weisen Sie eine gültige LUN zu. Wählen Sie einen vorhandenen Datenträger aus der Dropdownliste aus. Wählen Sie **Speichern** aus.

    ![Screenshot des Blatts „Datenträger“ für einen virtuellen Azure Stack Edge-Computer nach dem Hinzufügen eines vorhandenen Datenträgers. Die LUN-Nummer und der Name des Datenträgers sowie die Schaltfläche „Speichern“ sind hervorgehoben.](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/attach-existing-data-disk-2.png)

    Wählen Sie **Speichern** aus, und setzen Sie den Vorgang fort.

1. Es wird eine Benachrichtigung angezeigt, dass die VM aktualisiert wird. Navigieren Sie nach der Aktualisierung der VM zurück zur Seite **Details** des virtuellen Computers. Aktualisieren Sie die Seite, um den neu angefügten Datenträger in der Liste der Datenträger anzuzeigen.

    ![Screenshot des Abschnitts „Datenträger“ im Bereich „Details“ für einen virtuellen Computer. Ein Datenträgereintrag ist hervorgehoben.](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/list-data-disks-2.png)


## <a name="detach-a-data-disk"></a>Trennen eines Datenträgers

Führen Sie die folgenden Schritte aus, um einen Datenträger zu trennen oder zu entfernen, der einer auf Ihrem Gerät bereitgestellten VM zugeordnet ist.

> [!NOTE]
> - Sie können einen Datenträger entfernen, während die VM ausgeführt wird. Stellen Sie sicher, dass der Datenträger nicht aktiv verwendet wird, bevor Sie ihn von der VM trennen.
> - Beim Trennen eines Datenträgers wird dieser nicht automatisch gelöscht. Führen Sie die Schritte im Abschnitt [Löschen eines Datenträgers](#delete-a-data-disk) unten aus.

1. Navigieren Sie zu dem virtuellen Computer, von dem Sie einen Datenträger trennen möchten, und wählen Sie unter **Details** für den virtuellen Computer die Option **Datenträger** aus.

    ![Screenshot des Abschnitts „Datenträger“ im Bereich „Details“ für einen virtuellen Computer. Die Datenträgereinträge unter „Datenträger“ sind hervorgehoben.](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/list-data-disks-1.png)

1. Wählen Sie in der Liste der Datenträger den Datenträger aus, den Sie trennen möchten. Wählen Sie ganz rechts neben dem ausgewählten Datenträger das Trennsymbol („X“) aus. Der ausgewählte Datenträger wird getrennt. Wählen Sie **Speichern** aus.

    ![Screenshot der Anzeige „Datenträger“ für einen virtuellen Computer. Das X-Symbol für einen nicht angefügten Datenträger, der getrennt werden kann, und die Schaltfläche „Speichern“ sind hervorgehoben.](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/detach-data-disk-1.png)

1. Nachdem der Datenträger getrennt wurde, wird die VM aktualisiert. Aktualisieren Sie die Seite, um die aktualisierte Liste der Datenträger anzuzeigen.

    ![Screenshot der aktualisierten Anzeige „Datenträger“ für einen virtuellen Computer nach dem Trennen eines Datenträgers. Die Liste der Datenträger ist hervorgehoben.](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/list-data-disks-2.png)


## <a name="delete-a-data-disk"></a>Löschen eines Datenträgers

Führen Sie die folgenden Schritte aus, um einen Datenträger zu löschen, der keiner auf Ihrem Gerät bereitgestellten VM angefügt ist.

> [!NOTE]
> Vor dem Löschen eines Datenträgers müssen Sie den [Datenträger von der VM trennen](#detach-a-data-disk), falls er verwendet wird.

1. Navigieren Sie auf Ihrem Gerät zu **Virtuelle Computer**, und wechseln Sie zum Bereich **Ressourcen**. Wählen Sie **Datenträger** aus.
 
    ![Screenshot der Registerkarte „Datenträger“ auf dem Blatt „Ressourcen“ für virtuelle Computer. Die Bezeichnung „Ressourcen“ und die Registerkarte „Datenträger“ sind hervorgehoben.](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/delete-disk-1.png)

1. Wählen Sie in der Liste der Datenträger den Datenträger aus, den Sie löschen möchten. Wählen Sie ganz rechts neben dem ausgewählten Datenträger das Löschsymbol (Papierkorb) aus.

    ![Screenshot der Registerkarte „Datenträger“ auf dem Blatt „Ressourcen“ für virtuelle Computer. Ein Datenträger mit einem Papierkorbsymbol zur Angabe, dass er gelöscht werden kann, ist hervorgehoben.](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/delete-disk-2.png)

    Wenn das Löschsymbol nicht angezeigt wird, können Sie den VM-Namen in der Spalte **Angefügte VM** auswählen und den [Datenträger von der VM trennen](#detach-a-data-disk).

1. Es wird eine Meldung angezeigt, in der Sie bestätigen sollen, dass Sie den Datenträger löschen möchten. Der Vorgang kann nicht rückgängig gemacht werden. Wählen Sie **Ja**.

    ![Screenshot der Benachrichtigung, in der Sie aufgefordert werden, das Löschen eines Datenträgers zu bestätigen.](./media/azure-stack-edge-gpu-manage-virtual-machine-disks-portal/delete-disk-3.png)

    Nach Abschluss des Löschvorgangs wird der Datenträger aus der Liste entfernt.


## <a name="next-steps"></a>Nächste Schritte

Informationen zum Bereitstellen von VMs auf Ihrem Azure Stack Edge Pro-Gerät finden Sie unter [Bereitstellen von VMs über das Azure-Portal](azure-stack-edge-gpu-deploy-virtual-machine-portal.md).
