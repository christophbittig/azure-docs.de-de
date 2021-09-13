---
title: 'Wechseln der Replikationsappliance in Azure Site Recovery: Vorschau'
description: In diesem Artikel wird beschrieben, wie Sie zwischen verschiedenen Replikationsappliances wechseln, während Sie VMware-VMs in Azure Site Recovery in Azure replizieren (Vorschau)
ms.service: site-recovery
ms.topic: article
ms.date: 08/19/2021
ms.openlocfilehash: f95ed991833a9f6ed1b0635a8678e393f152cb56
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/19/2021
ms.locfileid: "122446865"
---
# <a name="switch-azure-site-recovery-replication-appliance"></a>Wechseln der Azure Site Recovery-Replikationsappliance

>[!NOTE]
> Die Informationen in diesem Artikel gelten für die Vorschauversion von Azure Site Recovery.

Sie müssen eine [lokale Azure Site Recovery-Replikationsappliance erstellen und bereitstellen](deploy-vmware-azure-replication-appliance-preview.md), wenn Sie [Azure Site Recovery](site-recovery-overview.md) für die Notfallwiederherstellung von VMware-VMs und physischen Servern in Azure verwenden. Ausführliche Informationen zur Replikationsappliance finden Sie in den Informationen zur [Architektur](vmware-azure-architecture-preview.md). Sie können abhängig von den Kapazitätsanforderungen Ihrer Organisation mehrere Replikationsappliances erstellen und verwenden.

In diesem Artikel erfahren Sie, wie Sie zwischen Replikationsappliances wechseln können.

## <a name="application-resilience"></a>Anwendungsresilienz

Wenn Sie in der klassischen Architektur die Resilienz Ihres Konfigurationsservers aufrechterhalten müssen, sollten Sie in der Regel regelmäßige manuelle Sicherungen des Computers erstellen. Dies ist ein sehr umständlicher und fehleranfälliger Prozess.  

Diese Vorschau bietet eine bessere Möglichkeit, die Resilienz Ihrer Appliances zu steigern. Wenn Ihre Replikationsappliance ausfällt, oder Sie ein Gleichgewicht zwischen den auf einer Appliance ausgeführten Computern herstellen müssen, starten Sie einfach eine andere Replikationsappliance, und stellen Sie alle Ihre Computer auf die neue Appliance um.


## <a name="consideration-for-switching-replication-appliance"></a>Überlegungen zum Wechseln der Replikationsappliance

Sie können die Replikationsappliance in den folgenden Szenarien wechseln:

- Sie müssen einen Wechsel ausführen, falls Ihre aktuelle Azure Site Recovery-Replikationsappliance ausgefallen ist, d. h. alle ihre Komponenten haben keinen Heartbeat mehr.
  - Eine Appliance gilt nur dann als ausgefallen, wenn alle Komponenten keinen Heartbeat aufweisen. Selbst wenn eine der Komponenten einen Heartbeat aufweist, wird der Wechsel blockiert.
  - Wenn Ihre aktuelle Appliance ausgefallen ist, müssen Sie erneut Anmeldeinformationen angeben, um auf die Computer zuzugreifen, die Sie wechseln möchten. Wenn Sie einen Lastenausgleich nutzen und sich Ihre aktuelle Appliance noch in einem nicht kritischen Zustand befindet, werden die Anmeldeinformationen automatisch ausgewählt, und Sie müssen sie nicht erneut eingeben, während Sie zu einer anderen Appliance wechseln.
- Möglicherweise müssen Sie den Wechsel ausführen, falls Sie einen Lastenausgleich für Ihre Replikationsappliance durchführen müssen.
- Wenn Sie versuchen, einen Wechsel mit dem Ziel eines Lastenausgleichs auf einer Appliance auszuführen, sollten sich alle Komponenten Ihrer aktuellen Appliance entweder im fehlerfreien Zustand oder im Warnungszustand befinden. Fehlender Heartbeat nur einer einzigen Komponente blockiert den Wechsel.
-  Stellen Sie sicher, dass sich die Appliance, zu der Sie wechseln, entweder im fehlerfreien Zustand oder im Warnungszustand befindet, damit der Vorgang erfolgreich ist.
-  Nur die Computer, die von einer lokalen Anwendung in Azure repliziert werden, können ausgewählt werden, wenn der Wechsel zu einer anderen Appliance ausgeführt wird.  


## <a name="switch-a-replication-appliance"></a>Wechseln einer Replikationsappliance

Hier sehen Sie beispielsweise das Szenario, in dem der Zustand von Replikationsappliance 1 (RA1) kritisch geworden ist, und Sie die geschützten Workloads zu Replikationsappliance 2 (RA2) verschieben möchten, die sich in fehlerfreiem Zustand befindet. Vielleicht möchten Sie auch die Workloads unter RA1 für Lastenausgleichs- oder Organisationsebenenänderungen zu RA2 verschieben.

**Führen Sie die folgenden Schritte aus, um eine Appliance zu wechseln**:

1. Gehen Sie zum Blatt **Site Recovery-Infrastruktur**, und wählen Sie **ASR-Replikationsappliance** aus.

   Die Liste der verfügbaren Appliances und deren Integrität wird angezeigt. RA2 ist hier beispielsweise fehlerfrei.

   ![Liste fehlerfreier Replikationsappliances](./media/switch-replication-appliance-preview/appliance-health.png)

2. Wählen Sie die Replikationsappliance (RA1) und dann **Appliance wechseln** aus.

   ![Auswählen der zu wechselnden Replikationsappliance](./media/switch-replication-appliance-preview/select-switch-appliance.png)


3. Wählen Sie unter **Computer auswählen** die Computer aus, für die Sie ein Failover zu einer anderen Replikationsappliance (RA2) durchführen möchten. Klicken Sie auf **Weiter**.

   >[!NOTE]
   > Nur die Computer, die von der aktuellen Appliance geschützt wurden, werden in der Liste angezeigt. Computer, für die ein Failover ausgeführt wurde, sind hier nicht vorhanden.  

    ![Auswählen von Computern für den Wechsel](./media/switch-replication-appliance-preview/select-machines.png)

4. Wählen Sie auf der Seite **Quelleinstellungen** für jeden der ausgewählten Computer eine andere Replikationsappliance aus.

   ![Quelleinstellungen für die Replikationsappliance](./media/switch-replication-appliance-preview/source-settings.png)

   >[!NOTE]
   > Wenn Ihre aktuelle Appliance ausgefallen ist, müssen Sie die Anmeldeinformationen für den Zugriff auf die Computer auswählen. Andernfalls wird das Feld deaktiviert.

5. Überprüfen Sie die Auswahl, und klicken Sie anschließend auf **Appliance wechseln**.

   ![Überprüfen der Replikationsappliance](./media/switch-replication-appliance-preview/review-switch-appliance.png)

   Sobald die Neusynchronisierung abgeschlossen ist, wechseln die virtuellen Computer, die zu einer neuen Appliance verschoben werden, in den fehlerfreien Replikationsstatus.

## <a name="next-steps"></a>Nächste Schritte
[Einrichten der Notfallwiederherstellung in Azure für lokale VMware-VMs](vmware-azure-tutorial.md)
