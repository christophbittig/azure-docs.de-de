---
title: Verwenden des Portals für Wartungsbenachrichtigungen
description: Zeigen Sie Wartungsbenachrichtigungen für in Azure ausgeführte virtuelle Computer über das Portal an, und starten Sie eine Self-Service-Wartung.
author: shants123
ms.service: virtual-machines
ms.subservice: maintenance-control
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/19/2019
ms.author: shants
ms.openlocfilehash: 8ec87f5377fad1e42ad5b6e96f32a4dae241a4ca
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122698790"
---
# <a name="handling-planned-maintenance-notifications-using-the-portal"></a>Behandeln von Benachrichtigungen zu geplanten Wartungen über das Portal

**Gilt für:** :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows-VMs :heavy_check_mark: Flexible Skalierungsgruppen :heavy_check_mark: Einheitliche Skalierungsgruppen

Nachdem eine [geplante Wartung](maintenance-notifications.md) angesetzt wurde, können Sie nach einer Liste der virtuellen Computer suchen, die betroffen sind. 

Sie können im Azure-Portal nach VMs suchen, für die eine Wartung geplant ist.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Klicken Sie im linken Navigationsbereich auf **Virtuelle Computer**.

3. Klicken Sie im Bereich mit den virtuellen Computern auf die Schaltfläche **Spalten bearbeiten**, um die Liste mit den verfügbaren Spalten zu öffnen.

4. Wählen Sie die folgenden Spalten aus, und fügen Sie sie hinzu:

   **Wartungsstatus**: Zeigt den Wartungsstatus für den virtuellen Computer an. Mögliche Werte:
      
    | Wert | BESCHREIBUNG |
    |-------|-------------|
    | Jetzt starten | Der virtuelle Computer befindet sich im Zeitfenster für die Self-Service-Wartung, in dem Sie die Wartung selbst initiieren können. Informationen zum Starten der Wartung für Ihren virtuellen Computer finden Sie weiter unten. | 
    | Geplant | Die Wartung für den virtuellen Computer ist geplant und kann nicht von Ihnen initiiert werden. Informationen zum Wartungszeitfenster erhalten Sie, indem Sie in dieser Ansicht „Wartung – geplantes Fenster“ auswählen oder auf den virtuellen Computer klicken. | 
    | Bereits aktualisiert | Ihr virtueller Computer wurde bereits aktualisiert, und zu diesem Zeitpunkt ist keine weitere Aktion erforderlich. | 
    | Versuchen Sie es später noch mal. | Sie haben erfolglos versucht, die Wartung zu initiieren. Die Option für die Self-Service-Wartung kann zu einem späteren Zeitpunkt verwendet werden. | 
    | Jetzt erneut versuchen | Sie können eine zuvor nicht erfolgreiche selbst initiierte Wartung wiederholen. | 
    | - | Ihre VM ist nicht Teil einer geplanten Wartung. |

   **Wartung – Self-Service-Fenster:** Zeigt das Zeitfenster an, in dem Sie die Wartung für Ihre virtuellen Computer selbst starten können.
   
   **Wartung – geplantes Fenster:** Zeigt das Zeitfenster an, in dem Azure auf Ihrem virtuellen Computer die Wartung abschließt. 



## <a name="notification-and-alerts-in-the-portal"></a>Benachrichtigungen und Warnungen im Portal

Azure sendet eine E-Mail an die Gruppe der Abonnementbesitzer und -mitbesitzer, um sie über den Zeitplan für eine geplante Wartung zu informieren. Sie können Azure-Aktivitätsprotokollwarnungen erstellen, um weitere Empfänger und Kanäle hinzuzufügen. Weitere Informationen finden Sie unter [Erstellen von Aktivitätsprotokollwarnungen zu Dienstbenachrichtigungen](../service-health/alerts-activity-log-service-notifications-portal.md).

Stellen Sie sicher, dass Sie den **Ereignistyp** als **Geplante Wartung** und **Dienste** als **VM-Skalierungsgruppen** und/oder **Virtuelle Computer** einstellen.

## <a name="start-maintenance-on-your-vm-from-the-portal"></a>Starten der Wartung für Ihren virtuellen Computer über das Portal

In der Ansicht mit den VM-Details können Sie auch weitere wartungsbezogene Details anzeigen.  
Am oberen Rand der VM-Detailansicht wird ein neues Benachrichtigungsmenüband angezeigt, wenn Ihr virtueller Computer von einer Welle für eine geplante Wartung betroffen ist. Darüber hinaus wird eine neue Option zum Starten der Wartung hinzugefügt (sofern möglich). 


Klicken Sie auf die Wartungsbenachrichtigung, um die Wartungsseite mit weiteren Details zur geplanten Wartung anzuzeigen. Von dort aus können Sie die Wartung für Ihren virtuellen Computer **starten**.

Nach dem Start der Wartung wird Ihr virtueller Computer gewartet, und der Wartungsstatus wird innerhalb weniger Minuten mit dem entsprechenden Ergebnis aktualisiert.

Wenn Sie das Self-Service-Zeitfenster verpasst haben, wird das Zeitfenster, in dem Ihre VM von Azure gewartet wird, weiterhin angezeigt. 


## <a name="next-steps"></a>Nächste Schritte

Sie können die geplante Wartung auch mithilfe der [Azure-Befehlszeilenschnittstelle](maintenance-notifications-cli.md) oder mit [PowerShell](maintenance-notifications-powershell.md) vornehmen.
