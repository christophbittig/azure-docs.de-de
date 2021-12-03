---
title: Konfigurieren von VMware-Syslogs für Azure VMware Solution
description: Hier erfahren Sie, wie Sie Diagnoseeinstellungen konfigurieren, um VMware-Syslogs für Ihre private Azure VMware Solution-Cloud zu erfassen.
ms.topic: how-to
ms.date: 09/24/2021
ms.openlocfilehash: 48422f6c0769953cee20c373ade8b8056ff0fee7
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2021
ms.locfileid: "131892766"
---
# <a name="configure-vmware-syslogs-for-azure-vmware-solution"></a>Konfigurieren von VMware-Syslogs für Azure VMware Solution

Diagnoseeinstellungen dienen dazu, das Streamen des Exports von Plattformprotokollen und Metriken für eine Ressource an das Ziel Ihrer Wahl zu konfigurieren. Sie können bis zu fünf verschiedene Diagnoseeinstellungen erstellen, um unterschiedliche Protokolle und Metriken an unabhängige Ziele zu senden. 

In diesem Artikel konfigurieren Sie eine Diagnoseeinstellung, um VMware-Syslogs für Ihre private Azure VMware Solution-Cloud zu erfassen. Sie speichern das Syslog in einem Speicherkonto, um die vCenter-Protokolle anzuzeigen und zu Diagnosezwecken zu analysieren. 

## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie sicher, dass Sie über eine private Azure VMware Solution-Cloud mit Zugriff auf die vCenter- und NSX-T Manager-Schnittstelle verfügen. 

## <a name="configure-diagnostic-settings"></a>Konfigurieren von Diagnoseeinstellungen

1. Wählen Sie in Ihrer privaten Azure VMware Solution-Cloud **Diagnoseeinstellungen** und dann **Diagnoseeinstellungen hinzufügen** aus.
 
   :::image type="content" source="media/diagnostic-settings/diagnostic-settings-log-analytics-workspace-1.png" alt-text="Screenshot: Konfigurieren von VMware-Syslogs" lightbox="media/diagnostic-settings/diagnostic-settings-log-analytics-workspace-1.png":::


1. Wählen Sie **vmwaresyslog**, **Alle Metriken** aus, und wählen Sie eine der folgenden angezeigten Optionen aus.

   >[!IMPORTANT]
   >Die Option **An Log Analytics-Arbeitsbereich senden** ist derzeit nicht verfügbar.
 
   ### <a name="archive-to-storage-account"></a>In Speicherkonto archivieren

    1. Wählen Sie unter **Diagnoseeinstellung** das Speicherkonto aus, in dem Sie die Protokolle speichern möchten, und wählen Sie dann **Speichern** aus.

       :::image type="content" source="media/diagnostic-settings/diagnostic-settings-log-analytics-workspace-2.png" alt-text="Screenshot: Optionen zum Speichern der Syslogs" lightbox="media/diagnostic-settings/diagnostic-settings-log-analytics-workspace-2.png":::

    1. Navigieren Sie zu Ihren **Speicherkonten**, überprüfen Sie, ob **Insight logs vmwaresyslog** erstellt wurde, und wählen Sie den Eintrag aus. 
 
       :::image type="content" source="media/diagnostic-settings/diagnostic-settings-log-analytics-workspace-3.png" alt-text="Screenshot: Erstellte und verfügbare Option „insights-logs-vmwaresyslog“" lightbox="media/diagnostic-settings/diagnostic-settings-log-analytics-workspace-3.png":::


    1. Durchsuchen Sie **Insight logs vmwarelog**, um nach der JSON-Datei zum Anzeigen der Protokolle zu suchen und sie herunterzuladen.

       :::image type="content" source="media/diagnostic-settings/diagnostic-settings-log-analytics-workspace-4.png" alt-text="Screenshot: Drilldownpfad zur JSON-Datei" lightbox="media/diagnostic-settings/diagnostic-settings-log-analytics-workspace-4.png"::: 

   ### <a name="stream-to-microsoft-azure-event-hubs"></a>Streamen an Microsoft Azure Event Hubs

    1. Wählen Sie unter **Diagnoseeinstellung** unter „Zieldetails“ die Option **An einen Event Hub streamen** aus. 
    1. Wählen Sie im Dropdownmenü **Event Hub-Namespace** aus, wohin die Protokolle gesendet werden sollen, und wählen Sie dann **Speichern** aus.
    
       :::image type="content" source="media/diagnostic-settings/stream-event-hub.png" alt-text="Screenshot: Drilldownpfad zum Senden der Protokolle." lightbox="media/diagnostic-settings/stream-event-hub.png"::: 




