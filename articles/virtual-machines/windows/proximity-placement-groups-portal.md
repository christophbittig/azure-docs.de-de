---
title: Erstellen einer Näherungsplatzierungsgruppe über das Portal
description: Erfahren Sie, wie Sie eine Näherungsplatzierungsgruppe über das Azure-Portal erstellen.
author: cynthn
ms.service: virtual-machines
ms.subservice: proximity-placement-groups
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 3/8/2021
ms.author: cynthn
ms.openlocfilehash: 37e959615ea9e5d483a081c7059556d36b5fc8ff
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122694866"
---
# <a name="create-a-proximity-placement-group-using-the-azure-portal"></a>Erstellen einer Näherungsplatzierungsgruppe über das Azure-Portal

**Gilt für:** :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows-VMs 

Um den Abstand zwischen den VMs so stark wie möglich zu verringern und somit die geringstmögliche Latenz zu erzielen, sollten Sie sie in einer [Näherungsplatzierungsgruppe](../co-location.md#proximity-placement-groups) bereitstellen.

Eine Näherungsplatzierungsgruppe ist eine logische Gruppierung, mit der ein möglichst geringer Abstand zwischen Azure-Compute-Ressourcen sichergestellt wird. Näherungsplatzierungsgruppen sind für Workloads hilfreich, die eine geringe Latenz erfordern.

> [!NOTE]
> Näherungsplatzierungsgruppen können nicht mit dedizierten Hosts verwendet werden.
>
> Wenn Sie Verfügbarkeitszonen in Verbindung mit Platzierungsgruppen verwenden möchten, müssen Sie sicherstellen, dass sich die VMs in der Platzierungsgruppe auch alle in derselben Verfügbarkeitszone befinden.
>

## <a name="create-the-proximity-placement-group"></a>Erstellen der Näherungsplatzierungsgruppe

1. Geben Sie **Näherungsplatzierungsgruppe** in die Suche ein.
1. Wählen Sie unter **Dienste** in den Suchergebnissen **Näherungsplatzierungsgruppen** aus.
1. Wählen Sie auf der Seite **Näherungsplatzierungsgruppen** die Option **Hinzufügen** aus.
1. Stellen Sie sicher, dass auf der Registerkarte **Grundlagen** unter **Projektdetails** das richtige Abonnement ausgewählt ist.
1. Wählen Sie unter **Ressourcengruppe** entweder die Option **Neu erstellen** aus, um eine neue Ressourcengruppe zu erstellen, oder wählen Sie in der Dropdownliste eine bereits vorhandene leere Ressourcengruppe aus. 
1. Wählen Sie unter **Region** den Standort aus, an dem die Näherungsplatzierungsgruppe erstellt werden soll.
1. Geben Sie in **Name der Näherungsplatzierungsgruppe** einen Namen ein, und wählen Sie dann **Überprüfen und erstellen** aus.
1. Wählen Sie nach bestandenen Überprüfung die Option **Erstellen** aus, um die Näherungsplatzierungsgruppe zu erstellen.

    ![Screenshot: Erstellen einer Näherungsplatzierungsgruppe](./media/ppg/ppg.png)


## <a name="create-a-vm"></a>Erstellen einer VM

1. Während Sie einen virtuellen Computer im Portal erstellen, wechseln Sie zur Registerkarte **Erweitert**. 
1. Wählen Sie in der Auswahl **Näherungsplatzierungsgruppe** die richtige Platzierungsgruppe aus. 

    ![Screenshot des Abschnitts „Näherungsplatzierungsgruppe“ beim Erstellen eines neuen virtuellen Computers im Portal](./media/ppg/vm-ppg.png)

1. Wenn Sie alle anderen erforderlichen Optionen vorgenommen haben, wählen Sie **Überprüfen und erstellen** aus.
1. Wählen Sie nach der erfolgreichen Überprüfung **Erstellen** aus, um den virtuellen Computer in der Platzierungsgruppe bereitzustellen.


## <a name="add-vms-in-an-availability-set-to-a-proximity-placement-group"></a>Hinzufügen von VMs in einer Verfügbarkeitsgruppe zu einer Näherungsplatzierungsgruppe

Wenn die VM Teil der Verfügbarkeitsgruppe ist, müssen Sie die Verfügbarkeitsgruppe der Platzierungsgruppe hinzufügen, bevor Sie die VMs hinzufügen.

1. Suchen Sie im [Portal](https://portal.azure.com) nach *Verfügbarkeitsgruppen*, und wählen Sie Ihre Verfügbarkeitsgruppe in den Ergebnissen aus.
1. Beenden Sie jede VM in der Verfügbarkeitsgruppe, bzw. heben Sie ihre Zuordnung auf, indem Sie die VM auswählen und dann auf der Seite für die VM **Beenden** auswählen. Wählen Sie dann **OK** aus, um die VM zu beenden.
1. Stellen Sie auf der Seite für Ihre Verfügbarkeitsgruppe sicher, dass als **Status** für alle VMs **Beendet (Zuordnung aufgehoben)** aufgeführt ist.
1. Klicken Sie im linken Menü auf **Konfiguration**.
1. Wählen Sie unter **Näherungsplatzierungsgruppe** in der Dropdownliste eine Platzierungsgruppe und dann **Speichern** aus.
1. Wählen Sie im linken Menü **Übersicht** aus, um wieder die Liste der VMs anzuzeigen. 
1. Wählen Sie jeden virtuellen Computer in der Verfügbarkeitsgruppe aus, und wählen Sie dann auf der Seite für die einzelnen VMs **Start** aus. 


## <a name="add-existing-vm-to-placement-group"></a>Hinzufügen einer vorhandenen VM zur Platzierungsgruppe 


1. Wählen Sie auf der Seite für den virtuellen Computer die Option **Beenden** aus.
1. Wenn als Status der VM **Beendet (Zuordnung aufgehoben)** aufgeführt ist, wählen Sie im linken Menü **Konfiguration** aus.
1. Wählen Sie unter **Näherungsplatzierungsgruppe** in der Dropdownliste eine Platzierungsgruppe und dann **Speichern** aus.
1. Wählen Sie im linken Menü **Übersicht** und dann **Starten** aus, um die VM neu zu starten.

 

## <a name="next-steps"></a>Nächste Schritte

Sie können auch die [Azure PowerShell](proximity-placement-groups.md) zum Erstellen von Näherungsplatzierungsgruppen verwenden.