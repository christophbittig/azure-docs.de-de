---
title: Überwachen von Azure Backup mit Azure Monitor
description: Überwachen von Azure Backup-Workloads und Erstellen von benutzerdefinierten Warnungen mit Azure Monitor.
ms.topic: conceptual
ms.date: 06/04/2019
ms.assetid: 01169af5-7eb0-4cb0-bbdb-c58ac71bf48b
ms.openlocfilehash: f1c729a9a724bb397b01a74c2f5853c1b2a74e25
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131455015"
---
# <a name="monitor-at-scale-by-using-azure-monitor"></a>Überwachen im richtigen Maßstab mithilfe von Azure Monitor

Azure Backup stellt [integrierte Überwachungs-und Warnungsfunktionen](backup-azure-monitoring-built-in-monitor.md) in einem Recovery Services-Tresor bereit. Diese Funktionen sind ohne zusätzliche Verwaltungsinfrastruktur verfügbar. Dieser integrierte Dienst ist jedoch in den folgenden Szenarien eingeschränkt:

- Wenn Sie Daten aus mehreren Recovery Services-Tresoren über mehrere Abonnements hinweg überwachen
- Wenn E-Mail *nicht* der bevorzugte Benachrichtigungskanal ist
- Wenn Benutzer Warnungen für mehrere Szenarien erhalten möchten
- Wenn Sie Informationen von einer lokalen Komponente wie System Center Data Protection Manager in Azure anzeigen möchten, die das Portal nicht in [**Sicherungsaufträgen**](backup-azure-monitoring-built-in-monitor.md#backup-jobs-in-backup-center) oder [**Sicherungswarnungen**](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault) anzeigt

## <a name="using-log-analytics-workspace"></a>Verwenden des Log Analytics-Arbeitsbereichs

### <a name="create-alerts-by-using-log-analytics"></a>Erstellen von Warnungen mit Log Analytics

In Azure Monitor können Sie Ihre eigenen Warnungen in einem Log Analytics-Arbeitsbereich erstellen. Im Arbeitsbereich verwenden Sie *Azure-Aktionsgruppen*, um Ihren bevorzugten Benachrichtigungsmechanismus auszuwählen.

> [!IMPORTANT]
> Weitere Informationen zu den Kosten für das Erstellen dieser Abfrage finden Sie unter[Azure Monitor-Preise](https://azure.microsoft.com/pricing/details/monitor/).

Öffnen Sie den Abschnitt **Protokolle** des Log Analytics-Arbeitsbereichs, und erstellen Sie eine Abfrage für Ihre eigenen Protokolle. Wenn Sie **Neue Warnungsregel** auswählen, wird die Azure Monitor-Seite für die Warnungserstellung geöffnet. Die folgende Abbildung zeigt dies.

![Erstellen einer Warnung in einem Log Analytics-Arbeitsbereich](media/backup-azure-monitoring-laworkspace/custom-alert.png)

Hier ist die Ressource bereits als Log Analytics-Arbeitsbereich gekennzeichnet, und die Integration von Aktionsgruppen wird bereitgestellt.

![Die Log Analytics-Seite für die Warnungserstellung](media/backup-azure-monitoring-laworkspace/inkedla-azurebackup-createalert.jpg)

#### <a name="alert-condition"></a>Warnungsbedingung

Das definierende Merkmal einer Warnung ist die auslösende Bedingung. Wählen Sie **Bedingung** aus, um die Kusto-Abfrage auf der Seite **Protokolle** automatisch zu laden, wie in der folgenden Abbildung dargestellt. Hier können Sie die Bedingung so bearbeiten, dass sie Ihren Anforderungen entspricht. Weitere Informationen finden Sie unter [Kusto-Beispielabfragen](#sample-kusto-queries).

![Einrichten einer Warnungsbedingung](media/backup-azure-monitoring-laworkspace/la-azurebackup-alertlogic.png)

Bei Bedarf können Sie die Kusto-Abfrage bearbeiten. Wählen Sie einen Schwellenwert, einen Zeitraum und eine Häufigkeit aus. Der Schwellenwert bestimmt, wann die Warnung ausgelöst wird. Der Zeitraum ist das Zeitfenster, in dem die Abfrage ausgeführt wird. Wenn der Schwellenwert z.B. größer als 0 ist, der Zeitraum 5 Minuten und die Häufigkeit 5 Minuten beträgt, führt die Regel die Abfrage alle 5 Minuten aus, wobei die letzten 5 Minuten überprüft werden. Wenn die Anzahl der Ergebnisse größer als 0 ist, werden Sie über die ausgewählte Aktionsgruppe benachrichtigt.

> [!NOTE]
> Um die Warnungsregel einmal täglich auszuführen, ändern Sie für alle an einem bestimmten Tag erstellten Ereignisse/Protokolle den Wert von „period“ und „frequency“ in „1.440“, d. h. 24 Stunden.

#### <a name="alert-action-groups"></a>Warnungsaktionsgruppen

Verwenden Sie eine Aktionsgruppe, um einen Benachrichtigungskanal anzugeben. Wählen Sie unter **Aktionsgruppen** die Option **Neu erstellen** aus, um die verfügbaren Benachrichtigungsmechanismen anzuzeigen.

![Verfügbare Benachrichtigungsmechanismen im Fenster „Aktionsgruppe hinzufügen“](media/backup-azure-monitoring-laworkspace/LA-AzureBackup-ActionGroup.png)

Sie können alle Warnungs- und Überwachungsanforderungen von Log Analytics allein erfüllen, oder Sie können Log Analytics verwenden, um integrierte Benachrichtigungen zu ergänzen.

Weitere Informationen finden Sie unter [Erstellen, Anzeigen und Verwalten von Protokollwarnungen mithilfe von Azure Monitor](../azure-monitor/alerts/alerts-log.md) und [Erstellen und Verwalten von Aktionsgruppen im Azure-Portal](../azure-monitor/alerts/action-groups.md).

### <a name="sample-kusto-queries"></a>Kusto-Beispielabfragen

Die Standarddiagramme zeigen Ihnen Kusto-Abfragen für einfache Szenarien, auf deren Grundlage Sie Warnungen erstellen können. Sie können die Abfragen auch ändern, um die Daten abzurufen, zu denen Sie Warnungen erhalten möchten. Fügen Sie die folgenden Kusto-Beispielabfragen auf der Seite **Protokolle** ein, und erstellen Sie dann Warnungen für die Abfragen:

- Alle erfolgreiche Sicherungsaufträge

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup"
    | summarize arg_max(TimeGenerated,*) by JobUniqueId
    | where JobStatus=="Completed"
    ````

- Alle fehlgeschlagenen Sicherungsaufträge

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup"
    | summarize arg_max(TimeGenerated,*) by JobUniqueId
    | where JobStatus=="Failed"
    ````

- Alle erfolgreiche Azure VM-Sicherungsaufträge

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup"
    | summarize arg_max(TimeGenerated,*) by JobUniqueId
    | where JobStatus=="Completed"
    | join kind=inner
    (
        CoreAzureBackup
        | where OperationName == "BackupItem"
        | where BackupItemType=="VM" and BackupManagementType=="IaaSVM"
        | distinct BackupItemUniqueId, BackupItemFriendlyName
    )
    on BackupItemUniqueId
    ````

- Alle erfolgreichen Sicherungsaufträge für das SQL-Protokoll

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup" and JobOperationSubType=="Log"
    | summarize arg_max(TimeGenerated,*) by JobUniqueId
    | where JobStatus=="Completed"
    | join kind=inner
    (
        CoreAzureBackup
        | where OperationName == "BackupItem"
        | where BackupItemType=="SQLDataBase" and BackupManagementType=="AzureWorkload"
        | distinct BackupItemUniqueId, BackupItemFriendlyName
    )
    on BackupItemUniqueId
    ````

- Alle erfolgreiche Aufträge des Azure Backup-Agents

    ````Kusto
    AddonAzureBackupJobs
    | where JobOperation=="Backup"
    | summarize arg_max(TimeGenerated,*) by JobUniqueId
    | where JobStatus=="Completed"
    | join kind=inner
    (
        CoreAzureBackup
        | where OperationName == "BackupItem"
        | where BackupItemType=="FileFolder" and BackupManagementType=="MAB"
        | distinct BackupItemUniqueId, BackupItemFriendlyName
    )
    on BackupItemUniqueId
    ````

- Beanspruchter Sicherungsspeicher pro Sicherungselement

    ````Kusto
    CoreAzureBackup
    //Get all Backup Items
    | where OperationName == "BackupItem"
    //Get distinct Backup Items
    | distinct BackupItemUniqueId, BackupItemFriendlyName
    | join kind=leftouter
    (AddonAzureBackupStorage
    | where OperationName == "StorageAssociation"
    //Get latest record for each Backup Item
    | summarize arg_max(TimeGenerated, *) by BackupItemUniqueId
    | project BackupItemUniqueId , StorageConsumedInMBs)
    on BackupItemUniqueId
    | project BackupItemUniqueId , BackupItemFriendlyName , StorageConsumedInMBs
    | sort by StorageConsumedInMBs desc
    ````

### <a name="diagnostic-data-update-frequency"></a>Häufigkeit der Aktualisierung der Diagnosedaten

Die Diagnosedaten aus dem Tresor werden mit einer gewissen Verzögerung in den Log Analytics-Arbeitsbereich übertragen. Jedes Ereignis erreicht den Log Analytics-Arbeitsbereich *20 bis 30 Minuten* nach dem Übertragung aus dem Recovery Services-Tresor. Hier finden Sie weitere Details zu dieser Verzögerung:

- Lösungsübergreifend werden die im Sicherungsdienst integrierten Warnungen sofort nach ihrer Erstellung gepusht. Daher werden sie normalerweise nach 20 bis 30 Minuten im Log Analytics-Arbeitsbereich angezeigt.
- Lösungsübergreifend werden bedarfsgesteuerte Sicherungsaufträge und Wiederherstellungsaufträge gepusht, sobald sie *abgeschlossen sind*.
- Die geplanten Sicherungsaufträge aus allen Lösungen (außer SQL- und SAP HANA-Sicherung) werden gepusht, sobald sie *abgeschlossen* sind.
- Da Protokollsicherungen alle 15 Minuten auftreten können, werden für SQL- und SAP HANA-Sicherungen Informationen für alle abgeschlossenen geplanten Sicherungsaufträge (einschließlich Protokolle) als Batch gesammelt und alle 6 Stunden gepusht.
- Alle anderen Informationen wie Sicherungselemente, Richtlinien, Wiederherstellungspunkte, Speicher usw. werden lösungsübergreifend *mindestens einmal täglich* gepusht.
- Eine Änderung der Sicherungskonfiguration (wie einer Änderung der Richtlinie oder Bearbeitungsrichtlinie) löst einen Push aller zugehörigen Sicherungsinformationen aus.

> [!NOTE]
> Die gleiche Verzögerung gilt für andere Ziele für Diagnosedaten, z. B. Speicherkonten und Event Hubs.

## <a name="using-the-recovery-services-vaults-activity-logs"></a>Verwenden der Aktivitätsprotokolle des Recovery Services-Tresors

> [!CAUTION]
> Die folgenden Schritte gelten nur für *Azure-VM-Sicherungen.* Diese Schritte können nicht für Lösungen wie den Azure Backup-Agent, SQL-Sicherungen in Azure oder Azure Files verwendet werden.

Sie können auch Aktivitätsprotokolle verwenden, um Benachrichtigungen zu Ereignissen zu erhalten, etwa zur erfolgreichen Sicherung. Führen Sie zunächst die folgenden Schritte aus:

1. Melden Sie sich beim Azure-Portal an.
2. Öffnen Sie den relevanten Recovery Services-Tresor.
3. Öffnen Sie in den Eigenschaften des Tresors den Abschnitt **Aktivitätsprotokoll**.

Identifizieren Sie das geeignete Protokoll, und erstellen Sie eine Warnung:

1. Überprüfen Sie, ob Sie Aktivitätsprotokolle für erfolgreiche Sicherungen erhalten, indem Sie die in der folgenden Abbildung gezeigten Filter anwenden. Ändern Sie den Wert für die **Zeitspanne** nach Bedarf, um Datensätze anzuzeigen.

   ![Filtern, um Aktivitätsprotokolle für Azure-VM-Sicherungen zu ermitteln](media/backup-azure-monitoring-laworkspace/activitylogs-azurebackup-vmbackups.png)

2. Wählen Sie den Vorgangsnamen aus, um die relevanten Details anzuzeigen.
3. Wählen Sie **Neue Warnungsregel** aus, um die Seite **Regel erstellen** zu öffnen.
4. Erstellen Sie eine Warnung anhand der unter [Erstellen, Anzeigen und Verwalten von Aktivitätsprotokollwarnungen mit Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md) beschriebenen Schritte.

   ![Neue Warnungsregel](media/backup-azure-monitoring-laworkspace/new-alert-rule.png)

Hier ist die Ressource der Recovery Services-Tresor selbst. Wiederholen Sie die gleichen Schritte für alle Tresore, in denen Sie über Aktivitätsprotokolle benachrichtigt werden möchten. Die Bedingung weist keinen Schwellenwert, keinen Zeitraum und keine Häufigkeit auf, da diese Warnung auf Ereignissen basiert. Sobald das entsprechende Aktivitätsprotokoll erstellt wurde, wird die Warnung ausgelöst.

## <a name="using-log-analytics-to-monitor-at-scale"></a>Verwenden von Log Analytics zum Überwachen im richtigen Maßstab

In Azure Monitor können Sie alle Warnungen anzeigen, die aus Aktivitätsprotokollen und Log Analytics-Arbeitsbereichen erstellt wurden. Öffnen Sie einfach den Bereich **Warnungen** auf der linken Seite.

Obwohl Sie Benachrichtigungen über Aktivitätsprotokolle erhalten können, empfehlen wir dringend die Verwendung von Log Analytics anstelle von Aktivitätsprotokollen für die Überwachung im richtigen Maßstab. Dies ist der Grund:

- **Eingeschränkte Szenarien**: Benachrichtigungen über Aktivitätsprotokolle gelten nur für Azure-VM-Sicherungen. Die Benachrichtigungen müssen für jeden Recovery Services-Tresor eingerichtet werden.
- **Definitionsübereinstimmung**: Die geplante Sicherungsaktivität passt nicht zur aktuellen Definition von Aktivitätsprotokollen. Stattdessen wird sie an [Ressourcenprotokollen](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace) ausgerichtet. Diese Ausrichtung führt zu unerwarteten Auswirkungen, wenn sich die Daten, die durch den Aktivitätsprotokollkanal fließen, ändern.
- **Probleme mit dem Aktivitätsprotokollkanal**: In Recovery Services-Tresoren folgen Aktivitätsprotokolle, die aus Azure Backup gepusht werden, einem neuen Modell. Leider wirkt sich diese Änderung auf die Generierung von Aktivitätsprotokollen in Azure Government, Azure Deutschland und Azure China 21Vianet aus. Wenn Benutzer dieser Clouddienste Warnungen aus Aktivitätsprotokollen in Azure Monitor erstellen oder konfigurieren, werden die Warnungen nicht ausgelöst. Außerdem werden diese Protokolle in allen öffentlichen Azure-Regionen nicht angezeigt, wenn ein Benutzer [Recovery Services-Aktivitätsprotokolle in einem Log Analytics-Arbeitsbereich erfasst](../azure-monitor/essentials/activity-log.md).

Verwenden Sie einen Log Analytics-Arbeitsbereich für die ordnungsgemäße Überwachung und Warnungserstellung für alle Ihre durch Azure Backup geschützten Workloads.

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Erstellen von benutzerdefinierten Abfragen finden Sie unter [Log Analytics-Datenmodell](backup-azure-reports-data-model.md).
