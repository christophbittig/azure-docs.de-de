---
title: Wartungssteuerung für Upgrades von Betriebssystemimages in Azure-VM-Skalierungsgruppen über das Azure-Portal
description: Hier erfahren Sie, wie Sie mithilfe der Wartungssteuerung und des Azure-Portals steuern, wann automatische Upgrades von Betriebssystemimages in Ihren Azure-VM-Skalierungsgruppen eingeführt werden.
author: ju-shim
ms.service: virtual-machine-scale-sets
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 06/01/2021
ms.author: jushiman
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 656d72663045b4604b9a94cbc686150925caca83
ms.sourcegitcommit: e1874bb73cb669ce1e5203ec0a3777024c23a486
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/16/2021
ms.locfileid: "112202038"
---
# <a name="maintenance-control-for-os-image-upgrades-on-azure-virtual-machine-scale-sets-using-azure-portal"></a>Wartungssteuerung für Upgrades von Betriebssystemimages in Azure-VM-Skalierungsgruppen über das Azure-Portal

Über die Wartungssteuerung können Sie entscheiden, wann automatische Gastbetriebssystemimageupgrades auf Ihre VM-Skalierungsgruppen angewendet werden sollen. In diesem Thema werden die Optionen mit dem Azure-Portal für die Wartungssteuerung behandelt. Weitere Informationen zur Verwendung der Wartungssteuerung finden Sie unter [Wartungssteuerung für Azure-VM-Skalierungsgruppen](virtual-machine-scale-sets-maintenance-control.md).


## <a name="create-a-maintenance-configuration"></a>Erstellen einer Wartungskonfiguration

1. Melden Sie sich beim Azure-Portal an.

1. Suchen Sie nach **Wartungskonfigurationen**.
    
    :::image type="content" source="media/virtual-machine-scale-sets-maintenance-control-portal/maintenance-configurations-search-bar.png" alt-text="Screenshot des Öffnens von Wartungskonfigurationen":::

1. Wählen Sie **Hinzufügen**.

    :::image type="content" source="media/virtual-machine-scale-sets-maintenance-control-portal/maintenance-configurations-add.png" alt-text="Screenshot des Hinzufügens einer Wartungskonfiguration":::

1. Wählen Sie auf der Registerkarte „Grundlagen“ ein Abonnement und eine Ressourcengruppe aus, geben Sie einen Namen für die Konfiguration an, und wählen Sie anschließend eine Region und dann *Betriebssystemimage-Upgrade* als Bereich aus. Wählen Sie **Weiter** aus.
    
    :::image type="content" source="media/virtual-machine-scale-sets-maintenance-control-portal/maintenance-configurations-basics-tab.png" alt-text="Screenshot der Grundlagen der Wartungskonfiguration":::

1. Auf der Registerkarte „Zeitplan“ können Sie ein geplantes Fenster deklarieren, in dem Azure die Updates auf Ihre Ressourcen anwendet. Legen Sie ein Startdatum, ein Wartungsfenster und die Wiederholungen fest. Nachdem Sie ein geplantes Fenster erstellt haben, müssen Sie die Updates nicht mehr manuell anwenden. Wählen Sie **Weiter** aus. 

    > [!IMPORTANT]
    > Die **Dauer** des Wartungsfensters muss mindestens *5 Stunden* betragen. Die Wartung muss mindestens ein Mal pro Tag **wiederholt** werden. 

    :::image type="content" source="media/virtual-machine-scale-sets-maintenance-control-portal/maintenance-configurations-schedule-tab.png" alt-text="Screenshot: Wartungskonfigurationszeitplan":::

1. Auf der Registerkarte „Zuweisung“ können Sie die Ressourcen entweder jetzt zuweisen oder den Schritt überspringen und die Ressourcen nach der Bereitstellung der Wartungskonfiguration zuweisen. Wählen Sie **Weiter** aus.

1. Fügen Sie Tags und Werte hinzu. Wählen Sie **Weiter** aus.
    
    :::image type="content" source="media/virtual-machine-scale-sets-maintenance-control-portal/maintenance-configurations-tags-tab.png" alt-text="Screenshot des Hinzufügens von Tags zur Wartungskonfiguration":::

1. Überprüfen Sie die Zusammenfassung. Klicken Sie auf **Erstellen**.

1. Wenn Ihre Bereitstellung abgeschlossen ist, wählen Sie **Go to resource** (Zu Ressource wechseln) aus.


## <a name="assign-the-configuration"></a>Zuweisen der Konfiguration

Wählen Sie auf der Seite „Details“ der Wartungskonfiguration **Zuweisungen** und dann **Ressource zuweisen** aus. 

![Screenshot des Zuweisens einer Ressource](media/virtual-machine-scale-sets-maintenance-control-portal/maintenance-configurations-add-assignment.png)

Wählen Sie die Ressourcen von VM-Skalierungsgruppen, denen die Wartungskonfiguration zugewiesen werden soll, und dann **OK** aus.  


## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erfahren Sie mehr über Wartung und Updates für virtuelle Computer, die in Azure ausgeführt werden.](maintenance-and-updates.md)
