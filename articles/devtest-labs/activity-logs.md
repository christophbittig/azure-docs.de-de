---
title: Aktivitätsprotokolle
description: Dieser Artikel enthält die Schritte zum Anzeigen von Aktivitätsprotokollen in Azure DevTest Labs.
ms.topic: how-to
ms.date: 07/10/2020
ms.openlocfilehash: e5c7453a1cc4959f6517050ed4c1896890b2610b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128654267"
---
# <a name="view-activity-logs-for-labs-in-azure-devtest-labs"></a>Anzeigen von Aktivitätsprotokollen in Azure DevTest Labs 
Nachdem Sie ein oder mehrere Labs erstellt haben, möchten Sie vermutlich überwachen, wer zu welcher Zeit und auf welche Weise auf Ihre Labs zugreift und diese ändert und verwaltet. Azure DevTest Labs verwendet Azure Monitor, und zwar insbesondere **Aktivitätsprotokolle**, um Informationen zu solchen Vorgängen in Labs bereitzustellen. 

In diesem Artikel wird erläutert, wie Sie Aktivitätsprotokolle für ein Lab in Azure DevTest Labs anzeigen.

## <a name="view-activity-log-for-a-lab"></a>Anzeigen des Aktivitätsprotokolls für ein Lab

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie **Alle Dienste** und dann im Abschnitt **DEVOPS** die Option **DevTest Labs**. Wählen Sie im Abschnitt **DEVOPS** das Sternchen (*) neben **DevTest Labs** aus. Mit dieser Aktion wird **DevTest Labs** dem linken Navigationsmenü hinzugefügt, sodass Sie beim nächsten Mal einfach darauf zugreifen können. Anschließend können Sie im linken Navigationsmenü **DevTest Labs** auswählen.

    ![All services (Alle Dienste) – Auswählen von „DevTest Labs“](./media/devtest-lab-create-lab/all-services-select.png)
1. Wählen Sie in der Liste mit den Labs Ihr Lab aus.
1. Wählen Sie auf der Startseite Ihres Labs im linken Menü die Option **Konfiguration und Richtlinien** aus. 

    :::image type="content" source="./media/activity-logs/configuration-policies-link.png" alt-text="Auswählen von „Konfiguration und Richtlinien“ im linken Menü":::
1. Wählen Sie auf der Seite **Konfiguration und Richtlinien** im linken Menü unter **Verwalten** die Option **Aktivitätsprotokoll** aus. Hier sollten Sie Einträge zu Vorgängen im Lab sehen. 

    :::image type="content" source="./media/activity-logs/activity-log.png" alt-text="Aktivitätsprotokoll":::    
1. Wählen Sie ein Ereignis aus, um Details dazu anzuzeigen. Auf der Seite **Zusammenfassung** finden Sie Informationen wie z. B. den Namen des Vorgangs, einen Zeitstempel und die ausführende Person. 
    
    :::image type="content" source="./media/activity-logs/stop-vm-event.png" alt-text="Ereignis „VM beenden“ – Zusammenfassung":::        
1. Wechseln Sie zur Registerkarte **JSON**, um weitere Details anzuzeigen. Im folgenden Beispiel sehen Sie den Namen der VM und den Vorgang, der für die VM ausgeführt wurde (sie wurde beendet).

    :::image type="content" source="./media/activity-logs/stop-vm-event-json.png" alt-text="Ereignis „VM beenden“ – JSON":::           
1. Wechseln Sie zur Registerkarte **Änderungsverlauf (Vorschau)** , um den Verlauf der Änderungen anzuzeigen. Im folgenden Beispiel sehen Sie, die Änderung, die für die VM vorgenommen wurde. 

    :::image type="content" source="./media/activity-logs/change-history.png" alt-text="Ereignis „VM beenden“ – Änderungsverlauf":::             
1. Wählen Sie die Änderung in der Liste des Änderungsverlaufs aus, um weitere Details zur Änderung anzuzeigen. 

    :::image type="content" source="./media/activity-logs/change-details.png" alt-text="Ereignis „VM beenden“ – Änderungsdetails":::             

Weitere Informationen zu Aktivitätsprotokollen finden Sie unter [Azure-Aktivitätsprotokoll](../azure-monitor/essentials/activity-log.md).

## <a name="next-steps"></a>Nächste Schritte

- Informationen zum Festlegen von **Warnungen** für Aktivitätsprotokolle finden Sie unter [Erstellen von Warnungen](create-alerts.md).
- Weitere Informationen zu Aktivitätsprotokollen finden Sie unter [Azure-Aktivitätsprotokoll](../azure-monitor/essentials/activity-log.md).
