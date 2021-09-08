---
title: Konfigurieren von Warnungen und arbeiten mit Metriken in Azure VMware Solution
description: Erfahren Sie, wie Benachrichtigungen zum Empfangen von Benachrichtigungen verwendet werden. Außerdem erfahren Sie mehr über das Arbeiten mit Metriken, um tiefere Erkenntnisse in Ihre Azure VMware-Lösung Private Cloud zu erhalten.
ms.topic: how-to
ms.date: 07/23/2021
ms.openlocfilehash: 718838d5335ff10200fc41029a6431a96552894b
ms.sourcegitcommit: 6f21017b63520da0c9d67ca90896b8a84217d3d3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/23/2021
ms.locfileid: "114653574"
---
# <a name="configure-azure-alerts-in-azure-vmware-solution"></a>Konfigurieren von Azure-Warnungen in Azure VMware Solution 

In diesem Artikel erfahren Sie, wie Sie [Azure-Aktionsgruppen](../azure-monitor/alerts/action-groups.md) in [Microsoft Azure Warnungen](../azure-monitor/alerts/alerts-overview.md) konfigurieren, um Benachrichtigungen über ausgelöste Ereignisse zu erhalten, die Sie definieren. Außerdem erfahren Sie mehr über die Verwendung von [Azure Monitor Metriken](../azure-monitor/essentials/data-platform-metrics.md), um tiefere Erkenntnisse in Ihre Azure VMware-Lösung Private Cloud zu erhalten.

>[!NOTE]
>Incidents, die sich auf die Verfügbarkeit eines Azure VMware Solution-Hosts und die entsprechende Wiederherstellung auswirken, werden automatisch an den Kontoadministrator, Dienstadministrator (klassische Berechtigung), die Co-Administratoren (klassische Berechtigung) und Besitzer (RBAC-Rolle) der Abonnements mit den privaten Azure VMware Solution-Clouds gesendet.

## <a name="supported-metrics-and-activities"></a>Unterstützte Metriken und Aktivitäten

Die folgenden Metriken sind über Azure Monitor Metriken sichtbar.

| **Signalname**                                                         | **Signaltyp** | **Überwachungsdienst** |
|-------------------------------------------------------------------------|-----------------|---------------------|
| Gesamtkapazität des Datenträgers mit dem Datenspeicher                                           | Metrik          | Plattform            |
| Prozentsatz des verwendeten Datenträgers mit dem Datenspeicher                                          | Metrik          | Plattform            |
| CPU-Prozentsatz                                                          | Metrik          | Plattform            |
| Durchschnittlicher effektiver Arbeitsspeicher                                                | Metrik          | Plattform            |
| Durchschnittlicher Overhead des Arbeitsspeichers                                                 | Metrik          | Plattform            |
| Durchschnittlicher gesamter Arbeitsspeicher                                                    | Metrik          | Plattform            |
| Durchschnittliche Arbeitsspeicherauslastung                                                    | Metrik          | Plattform            |
| Verwendeter Datenträger mit dem Datenspeicher                                                     | Metrik          | Plattform            |
| Alle Verwaltungsvorgänge                                           | Aktivitätsprotokoll    | Administrative      |
| Registrieren des Microsoft.AVS-Ressourcenanbieters. (Microsoft.AVS/privateClouds) | Aktivitätsprotokoll    | Administrative      |
| Eine PrivateCloud erstellen oder aktualisieren. (Microsoft.AVS/privateClouds)          | Aktivitätsprotokoll    | Administrative      |
| Eine PrivateCloud erstellen oder aktualisieren. (Microsoft.AVS/privateClouds)                    | Aktivitätsprotokoll    | Administrative      |

## <a name="configure-an-alert-rule"></a>Eine Benachrichtigungsregel konfigurieren
1. Wählen Sie auf der Private Cloud Azure VMware-Lösung die Option **Monitor**  >  **Warnungen** und dann **Neue Benachrichtigungsregel** aus.
 
   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/create-new-alert-rule.png" alt-text="Screenshot, der zeigt, wo Sie eine Warnungsregel in ihrer Azure VMware-Lösung Private Cloud konfigurieren können." lightbox="media/configure-alerts-for-azure-vmware-solution/create-new-alert-rule.png":::

   Ein neuer Konfigurationsbildschirm wird geöffnet, auf dem Sie Folgendes ausführen:
   - Definieren des Umfangs
   - Konfigurieren einer Bedingung
   - Einrichten der Aktionsgruppe
   - Festlegen der Details der Benachrichtigungsregel
    
   :::image type="content" source="../devtest-labs/media/activity-logs/create-alert-rule-done.png" alt-text="Screenshot des Fensters „Warnungsregel erstellen“." lightbox="../devtest-labs/media/activity-logs/create-alert-rule-done.png":::

1. Wählen Sie unter **Umfang**, die Zielressource aus, die Sie überwachen möchten. Standardmäßig wurde die Azure VMware-Lösung Private Cloud, über die Sie das Menü Warnungen geöffnet haben, definiert.

1. Wählen Sie unter **Bedingung** die Option **Bedingung hinzufügen** aus, und wählen Sie im Fenster, das geöffnet wird, das Signal aus, das Sie für die Warnungsregel erstellen möchten. 

   In unserem Beispiel haben wir den **Prozentsatz des verwendeten Datenträger DataStore-Daten** Trägers ausgewählt, der aus einer SLA-Perspektive der [Azure VMware-Lösung](https://aka.ms/avs/sla) relevant ist. 

   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/configure-signal-logic-options.png" alt-text="Screenshot des Fensters „Signallogik konfigurieren“ mit den Signalen, die für die Warnungsregel erstellt werden sollen."::: 

1. Definieren Sie die Logik, durch die die Warnung ausgelöst wird, und wählen Sie dann **abgeschlossen** aus. 

   In unserem Beispiel wurden nur der **Schwellenwert** und die **Frequenz der Auswertung** angepasst. 
   
   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/define-alert-logic-threshold.png" alt-text="Screenshot mit Angabe von Schwellenwert, Operator, Aggregationsart und -granularität, Schwellenwert und Häufigkeit der Auswertung der Signalwarnungslogik."::: 

1. Klicken Sie unter **Aktionen** auf **Add action groups** (Aktionsgruppen auswählen). Die Aktionsgruppe definiert, *wie* die Benachrichtigung empfangen wird und *wer* Sie empfängt.   Benachrichtigungen können per e-Mail, SMS, [Azure Mobile App-Pushbenachrichtigung](https://azure.microsoft.com/features/azure-portal/mobile-app/) oder Sprachnachricht empfangen werden.

1. Wählen Sie eine vorhandene Aktionsgruppe oder **Aktionsgruppe erstellen** aus, um eine neue Gruppe zu erstellen.
 
1. Weisen Sie in dem Fenster, das geöffnet wird, auf der Registerkarte **Grundlagen** der Aktionsgruppe einen Namen und einen Anzeigenamen zu.

1. Wählen Sie auf der Registerkarte **Benachrichtigungen** den **Benachrichtigungstyp** und **Name** aus. Klicken Sie anschließend auf **OK**.

   Unser Beispiel basiert auf einer E-Mail-Benachrichtigung.

   :::image type="content" source="../azure-monitor/alerts/media/action-groups/action-group-2-notifications.png" alt-text="Screenshot mit den E-Mail-, SMS-, Push- und Spracheinstellungen für die Warnung." lightbox="../azure-monitor/alerts/media/action-groups/action-group-2-notifications.png":::    

1. (Optional) Konfigurieren Sie die **Aktionen**, wenn Sie proaktive Aktionen ausführen und Benachrichtigungen zu dem Ereignis erhalten möchten. Wählen Sie einen verfügbaren **Aktionstyp** aus, und wählen Sie dann **überprüfen + erstellen**. 

   - **Automation-Runbooks**: zum Automatisieren von Aufgaben basierend auf Warnungen

   - **Azure Functions**: für benutzerdefinierte, ereignisgesteuerte serverlose Codeausführung

   - **ITSM**: für die Integration in einen Dienstanbieter wie ServiceNow zum Erstellen eines Tickets

   - **Logik-App**: für die Orchestrierung komplexerer Workflows

   - **Webhooks**: zum Auslösen eines Prozesses in einem anderen Dienst


1. Geben Sie unter den **Details der Alarmregel** einen Namen, eine Beschreibung, eine Ressourcengruppe zum Speichern der Alarmregel und den Schweregrad an. Wählen Sie **Warnungsregel erstellen** aus.
   
   Die Warnungsregel ist sichtbar und kann über die Azure-Portal verwaltet werden.

   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/existing-alert-rule.png" alt-text="Screenshot, der die neue Warnungsregel im Fenster „Regeln“ zeigt." lightbox="media/configure-alerts-for-azure-vmware-solution/existing-alert-rule.png":::      

   Sobald eine Metrik den in einer Warnungsregel definierten Schwellenwert erreicht, wird das Menü **Warnungen** aktualisiert und sichtbar gemacht.

   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/threshold-alert.png" alt-text="Screenshot der Warnung nach Erreichen des in der Warnregel festgelegten Schwellenwerts." lightbox="media/configure-alerts-for-azure-vmware-solution/threshold-alert.png":::     

   Abhängig von der konfigurierten Aktionsgruppe erhalten Sie eine Benachrichtigung über das konfigurierte Medium. In unserem Beispiel haben wir die e-Mail-Adresse konfiguriert.
    
   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/alert-notification.png" alt-text="Screenshot einer Azure Monitor Warnung mit der Fehlerzeichenfolge und dem Ereignis „Datum und Uhrzeit“ ausgelöst."::: 

## <a name="work-with-metrics"></a>Überwachen mit Metriken

1. Wählen Sie in Ihrer Azure VMware Solution Private Cloud die Option **Monitoring** > **Metriken**. Wählen Sie dann die gewünschte Metrik aus der Dropdown-Dropdown-Option aus.
    
   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/monitoring-metrics.png" alt-text="Screenshot, der das Fenster „Metriken“ mit Fokus auf das Dropdownfeld „Metrik“ zeigt." lightbox="media/configure-alerts-for-azure-vmware-solution/monitoring-metrics.png":::   

1. Sie können die Parameter des Diagramms ändern, z. b. den **Zeitbereich** oder die **Granularität der Zeit**. 

   Folgende andere Optionen sind verfügbar:
   - **Logs anbohren** und die Daten im zugehörigen Log Analytics-Arbeitsbereich abfragen
   - **Heften Sie dieses Diagramm** der Einfachheit halber an ein Azure-Dashboard.

   :::image type="content" source="media/configure-alerts-for-azure-vmware-solution/monitoring-metrics-time-range-granularity.png" alt-text="Screenshot, der die Optionen für Zeitbereich und Granularität der Zeit für die Metrik zeigt." lightbox="media/configure-alerts-for-azure-vmware-solution/monitoring-metrics-time-range-granularity.png":::  
 
 
## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun eine Alarmregel für Ihre Azure VMware Solution Private Cloud konfiguriert haben, möchten Sie vielleicht noch mehr darüber erfahren:
- [Azure Monitor-Metriken](../azure-monitor/essentials/data-platform-metrics.md)
- [Azure Monitor-Warnungen](../azure-monitor/alerts/alerts-overview.md)
- [Azure-Aktionsgruppen](../azure-monitor/alerts/action-groups.md)

Sie können auch mit einer der anderen Azure- [Lösungen](index.yml) für die VMware-Lösung fortfahren.
