---
title: Konfigurieren von VMware-Syslogs für Azure VMware Solution
description: Hier erfahren Sie, wie Sie Diagnoseeinstellungen konfigurieren, um VMware-Syslogs für Ihre private Azure VMware Solution-Cloud zu erfassen.
ms.topic: how-to
ms.date: 09/24/2021
ms.openlocfilehash: 983a62eb5b094c94048e7580fd53558df002d816
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128700239"
---
# <a name="configure-vmware-syslogs-for-azure-vmware-solution"></a>Konfigurieren von VMware-Syslogs für Azure VMware Solution

Diagnoseeinstellungen dienen dazu, das Streamen des Exports von Plattformprotokollen und Metriken für eine Ressource an das Ziel Ihrer Wahl zu konfigurieren. Sie können bis zu fünf verschiedene Diagnoseeinstellungen erstellen, um unterschiedliche Protokolle und Metriken an unabhängige Ziele zu senden. 

In diesem Artikel konfigurieren Sie eine Diagnoseeinstellung, um VMware-Syslogs für Ihre private Azure VMware Solution-Cloud zu erfassen. Sie speichern das Syslog in einem Speicherkonto, um die vCenter-Protokolle anzuzeigen und zu Diagnosezwecken zu analysieren. 

## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie sicher, dass Sie über eine private Azure VMware Solution-Cloud mit Zugriff auf die vCenter- und NSX-T Manager-Schnittstelle verfügen. 

## <a name="configure-diagnostic-settings"></a>Konfigurieren von Diagnoseeinstellungen

1. Wählen Sie in Ihrer privaten Azure VMware Solution-Cloud **Diagnoseeinstellungen** und dann **Diagnoseeinstellungen hinzufügen** aus.
 
   :::image type="content" source="media/diagnostic-settings/diagnostic-settings-log-analytics-workspace-1.png" alt-text="Screenshot: Konfigurieren von VMware-Syslogs" lightbox="media/diagnostic-settings/diagnostic-settings-log-analytics-workspace-1.png":::


1. Wählen Sie die Optionen **vmwaresyslog**, **Allmetrics** und **In einem Speicherkonto archivieren** aus.

   >[!IMPORTANT]
   >Die Option **An Log Analytics-Arbeitsbereich senden** funktioniert derzeit nicht.
 
1. Wählen Sie das Speicherkonto aus, in dem Sie die Protokolle speichern möchten, und wählen Sie dann **Speichern** aus.

   :::image type="content" source="media/diagnostic-settings/diagnostic-settings-log-analytics-workspace-2.png" alt-text="Screenshot: Optionen zum Speichern der Syslogs" lightbox="media/diagnostic-settings/diagnostic-settings-log-analytics-workspace-2.png":::

1. Wechseln Sie zu Ihrem Speicher und Konto, überprüfen Sie, ob **insights-logs-vmwaresyslog** erstellt wurde, und wählen Sie es aus. 
 
   :::image type="content" source="media/diagnostic-settings/diagnostic-settings-log-analytics-workspace-3.png" alt-text="Screenshot: Erstellte und verfügbare Option „insights-logs-vmwaresyslog“" lightbox="media/diagnostic-settings/diagnostic-settings-log-analytics-workspace-3.png":::


1. Navigieren Sie zum Speicherort, und laden Sie die JSON-Datei herunter, um die Protokolle anzuzeigen.

   :::image type="content" source="media/diagnostic-settings/diagnostic-settings-log-analytics-workspace-4.png" alt-text="Screenshot: Drilldownpfad zur JSON-Datei" lightbox="media/diagnostic-settings/diagnostic-settings-log-analytics-workspace-4.png"::: 

