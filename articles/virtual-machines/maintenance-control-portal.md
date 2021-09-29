---
title: Wartungssteuerung für virtuelle Azure-Computer über das Azure-Portal
description: Erfahren Sie, wie Sie mithilfe der Wartungssteuerung und des Azure-Portals steuern, wann eine Wartung auf Ihre virtuellen Azure-Computer angewandt wird.
author: cynthn
ms.service: virtual-machines
ms.subservice: maintenance
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 04/22/2020
ms.author: cynthn
ms.openlocfilehash: 6ac4be6b1c0654ea46bbbfe587a72839c3f2896b
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129216629"
---
# <a name="control-updates-with-maintenance-control-and-the-azure-portal"></a>Steuern von Updates mit der Wartungssteuerung und dem Azure-Portal

**Gilt für:** :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows-VMs :heavy_check_mark: Flexible Skalierungsgruppen :heavy_check_mark: Einheitliche Skalierungsgruppen

Mit der Wartungssteuerung können Sie entscheiden, wann Sie Updates auf Ihre isolierten VMs und Ihre dedizierten Azure-Hosts anwenden. In diesem Thema werden die Optionen mit dem Azure-Portal für die Wartungssteuerung behandelt. Weitere Informationen zu den Vorteilen der Verwendung der Wartungssteuerung, ihren Einschränkungen und anderen Verwaltungsoptionen finden Sie unter [Verwalten von Plattformupdates mit der Wartungssteuerung](maintenance-control.md).

## <a name="create-a-maintenance-configuration"></a>Erstellen einer Wartungskonfiguration

1. Melden Sie sich beim Azure-Portal an.

1. Suchen Sie nach **Wartungskonfigurationen**.
    
    :::image type="content" source="media/virtual-machines-maintenance-control-portal/maintenance-configurations-search-bar.png" alt-text="Screenshot des Öffnens von Wartungskonfigurationen":::

1. Klicken Sie auf **Hinzufügen**.

    :::image type="content" source="media/virtual-machines-maintenance-control-portal/maintenance-configurations-add-2.png" alt-text="Screenshot des Hinzufügens einer Wartungskonfiguration":::

1. Wählen Sie in der Registerkarte Grundlagen ein Abonnement und eine Ressourcengruppe aus, geben Sie einen Namen für die Konfiguration an und wählen Sie anschließend eine Region und dann *Host* als Bereich aus. Klicken Sie auf **Weiter**.
    
    :::image type="content" source="media/virtual-machines-maintenance-control-portal/maintenance-configurations-basics-tab.png" alt-text="Screenshot der Grundlagen der Wartungskonfiguration":::

1. Auf der Registerkarte „Zeitplan“ können Sie ein geplantes Fenster deklarieren, in dem Azure die Updates auf Ihre Ressourcen anwendet. Legen Sie ein Startdatum, ein Wartungsfenster und die Wiederholungen fest. Nachdem Sie ein geplantes Fenster erstellt haben, müssen Sie die Updates nicht mehr manuell anwenden. Klicken Sie auf **Weiter**. 

    > [!IMPORTANT]
    > Die **Dauer** des Wartungsfensters muss *2 Stunden* oder länger sein. Die Wartung muss mindestens ein Mal in 35 Tagen **wiederholt** werden. 

    :::image type="content" source="media/virtual-machines-maintenance-control-portal/maintenance-configurations-schedule-tab.png" alt-text="Screenshot des Wartungskonfigurationszeitplans":::

1. In der Registerkarte Zuweisung können Sie die Ressourcen entweder jetzt zuweisen, oder den Schritt überspringen und die Ressourcen später zuweisen, nachdem Sie die Wartungskonfiguration bereitgestellt haben. Klicken Sie auf **Weiter**.

1. Fügen Sie Tags und Werte hinzu. Klicken Sie auf **Weiter**.
    
    :::image type="content" source="media/virtual-machines-maintenance-control-portal/maintenance-configurations-tags-tab.png" alt-text="Screenshot des Hinzufügens von Tags zur Wartungskonfiguration":::

1. Überprüfen Sie die Zusammenfassung. Klicken Sie auf **Erstellen**.

1. Klicken Sie nach Abschluss der Bereitstellung auf **Zu Ressource wechseln**.


## <a name="assign-the-configuration"></a>Zuweisen der Konfiguration

Klicken Sie auf der Seite „Details“ der Wartungskonfiguration auf „Zuweisungen“ und dann auf **Ressource zuweisen**. 

![Screenshot des Zuweisens einer Ressource](media/virtual-machines-maintenance-control-portal/maintenance-configurations-add-assignment.png)

Wählen Sie die Ressourcen aus, denen die Wartungskonfiguration zugewiesen werden soll, und klicken Sie auf **OK**. In der Spalte **Typ** wird angezeigt, ob es sich bei der Ressource um eine isolierte VM oder einen dedizierten Azure-Host handelt. Die VM muss sich im ausgeführten Zustand befinden, damit die Konfiguration zugewiesen werden kann. Wenn Sie versuchen, einer angehaltenen VM eine Konfiguration zuzuweisen, tritt ein Fehler auf. 

<!---Shantanu to add details about the error case--->

![Screenshot des Auswählens einer Ressource](media/virtual-machines-maintenance-control-portal/maintenance-configurations-select-resource.png)

## <a name="check-configuration"></a>Überprüfen der Konfiguration

Mithilfe von **Wartungskonfigurationen** können Sie sich vergewissern, dass die Konfiguration ordnungsgemäß angewandt wurde, oder Wartungskonfigurationen überprüfen, die derzeit zugewiesen sind. In der Spalte **Typ** wird angezeigt, ob die Konfiguration einer isolierten VM oder einem dedizierten Azure-Host zugewiesen ist. 

![Screenshot des Überprüfens einer Wartungskonfiguration](media/virtual-machines-maintenance-control-portal/maintenance-configurations-host-type.png)

Sie können auch die Konfiguration für einen bestimmten virtuellen Computer auf der Seite „Eigenschaften“ überprüfen. Klicken Sie auf **Wartung**, um die Konfiguration anzuzeigen, die diesem virtuellen Computer zugewiesen ist.

![Screenshot des Überprüfens der Wartung für einen Host](media/virtual-machines-maintenance-control-portal/maintenance-configurations-check-config.png)

## <a name="check-for-pending-updates"></a>Prüfen auf ausstehende Updates

Es gibt auch zwei Möglichkeiten, zu überprüfen, ob für eine Wartungskonfiguration Updates ausstehen. Klicken Sie unter **Wartungskonfigurationen** in den Details für die Konfiguration auf **Zuweisungen**, und überprüfen Sie den **Wartungsstatus**.

![Screenshot des Überprüfens ausstehender Updates](media/virtual-machines-maintenance-control-portal/maintenance-configurations-pending.png)

Sie können auch einen bestimmten Host mithilfe von **Virtual Machines** oder der Eigenschaften des dedizierten Hosts überprüfen. 

![Screenshot: Hervorgehobener Wartungsstatus](media/virtual-machines-maintenance-control-portal/maintenance-configurations-pending-vm.png)

## <a name="apply-updates"></a>Anwenden von Updates

Sie können ausstehende Updates nach Bedarf anwenden. Klicken Sie in den Details des virtuellen Computers oder in den Details von Azure Dedicated Host auf **Wartung** und anschließend auf **Wartungsfunktion jetzt anwenden**. Aufrufe zum Anwenden von Updates können bis zu zwei Stunden dauern.

![Screenshot des Anwendens ausstehender Updates](media/virtual-machines-maintenance-control-portal/maintenance-configurations-apply-updates-now.png)

## <a name="check-the-status-of-applying-updates"></a>Überprüfen Sie den Status für das Anwenden von Updates. 

Sie können den Status der Updates für eine Konfiguration unter **Wartungskonfigurationen** oder mithilfe von **Virtual Machines** überprüfen. Klicken Sie in den VM-Details auf **Wartung**. Im folgenden Beispiel ist unter **Wartungsstatus** zu erkennen, dass ein Update **aussteht**.

![Screenshot des Überprüfens des Status ausstehender Updates](media/virtual-machines-maintenance-control-portal/maintenance-configurations-status.png)

## <a name="delete-a-maintenance-configuration"></a>Löschen einer Wartungskonfiguration

Um eine Konfiguration zu löschen, öffnen Sie die Konfigurationsdetails, und klicken Sie auf **Löschen**.

![Screenshot: Löschen einer Konfiguration](media/virtual-machines-maintenance-control-portal/maintenance-configurations-delete.png)


## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie unter [Wartung und Updates](maintenance-and-updates.md).
