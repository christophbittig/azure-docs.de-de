---
title: Überwachen von geschützten Azure Backup-Workloads
description: In diesem Artikel erfahren Sie mehr über die Überwachungs- und Benachrichtigungsfunktionen für Azure Backup-Workloads im Azure-Portal.
ms.topic: conceptual
ms.date: 11/02/2021
ms.assetid: 86ebeb03-f5fa-4794-8a5f-aa5cbbf68a81
author: v-amallick
ms.service: backup
ms.author: v-amallick
ms.openlocfilehash: afc23090ce15621542ec6035c67d36b85a3414c1
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131439773"
---
# <a name="monitoring-azure-backup-workloads"></a>Überwachen von Azure Backup-Workloads

Azure Backup enthält mehrere Sicherheitslösungen, deren Einsatz von den Anforderungen an die Sicherheit und der Infrastrukturtopologie (lokal oder Azure) abhängt. Alle Benutzer und Administratoren der Sicherung sollten die Vorgänge in allen Lösungen verfolgen können und in wichtigen Fällen benachrichtigt werden. In diesem Artikel werden die Überwachungs- und Benachrichtigungsfunktionen in Azure Backup näher erläutert.

[!INCLUDE [backup-center.md](../../includes/backup-center.md)]

## <a name="backup-items-in-recovery-services-vault"></a>Sicherungselemente im Recovery Services-Tresor

Sie können alle Ihre Sicherungselemente über einen Recovery Services-Tresor überwachen. Wenn Sie im **Backup Center** zum Abschnitt **Sicherungsinstanzen** navigieren, wird eine Ansicht mit einer eine detaillierten Liste geöffnet, in der alle Sicherungselemente des angegebenen Workloadtyps aufgelistet sind, mit Informationen zum letzten Sicherungsstatus für jedes Element, dem letzten verfügbaren Wiederherstellungspunkt und so weiter.

:::image type="content" source="./media/backup-azure-monitoring-laworkspace/backup-center-instances-inline.png" alt-text="Screenshot: Anzeigen von Sicherungselementen für den Recovery Services-Tresor." lightbox="./media/backup-azure-monitoring-laworkspace/backup-center-instances-expanded.png":::

>[!NOTE]
>Für Elemente, die mit DPM nach Azure gesichert wurden, zeigt die Liste alle mit dem DPM-Server geschützten Datenquellen an (sowohl auf dem Datenträger als auch online). Wird der Schutz für die Datenquelle unter Beibehaltung der Sicherungsdaten beendet, wird die Datenquelle weiterhin im Portal aufgeführt. Sie können zu den Details der Datenquelle navigieren, um zu sehen, ob die Wiederherstellungspunkte auf dem Datenträger, online oder an beiden Orten vorhanden sind. Auch für Datenquellen, deren Onlineschutz beendet wurde, deren Daten aber erhalten bleiben, wird die Abrechnung der Onlinewiederherstellungspunkte fortgesetzt, bis die Daten vollständig gelöscht werden.
>
> Die DPM-Version muss DPM 1807 (5.1.378.0) oder DPM 2019 (Version 10.19.58.0 oder höher) sein, damit die Sicherungselemente im Portal des Recovery Services-Tresors sichtbar sind.

## <a name="backup-jobs-in-backup-center"></a>Sicherungsaufträge im Backup Center

Azure Backup bietet integrierte Überwachungs- und Warnungsfunktionen für durch Azure Backup geschützte Workloads. Wenn Sie im **Backup Center** zum Bereich **Sicherungsaufträge** navigieren, können Sie die aktuellen Sicherungs- und Wiederherstellungsaufträge in Ihren Tresoren anzeigen.

:::image type="content" source="./media/backup-azure-monitoring-laworkspace/backup-center-jobs-inline.png" alt-text="Screenshot: Integrierte Überwachung des Recovery Services-Tresors." lightbox="./media/backup-azure-monitoring-laworkspace/backup-center-jobs-expanded.png":::

Aufträge werden generiert, wenn Vorgänge wie das Konfigurieren der Sicherung, die Sicherung, Wiederherstellung oder das Löschen der Sicherung durchgeführt werden.

Im Folgenden werden die Aufträge der folgenden Azure Backup-Lösungen vorgestellt:

- Azure VM Backup
- Azure-Dateisicherung
- Azure-Workloadsicherung wie SQL und SAP HANA
- Microsoft Azure Recovery Services-Agent (MARS)

Aufträge aus System Center Data Protection Manager (SC-DPM) und Microsoft Azure Backup Server (MABS) werden nicht aufgeführt.

> [!NOTE]
> Azure-Workloads wie SQL- und SAP HANA-Sicherungen in Azure-VMs umfassen viele Sicherungsaufträge. Protokollsicherungen können beispielsweise alle 15 Minuten ausgeführt werden. Für solche Datenbankworkloads werden darum nur vom Benutzer ausgelöste Vorgänge angezeigt. Geplante Sicherungsvorgänge werden nicht angezeigt.

## <a name="backup-alerts-in-recovery-services-vault"></a>Sicherungswarnungen im Recovery Services-Tresor

Bei Warnungen handelt es sich in erster Linie um Szenarios, in denen Benutzer benachrichtigt werden, damit Sie entsprechende Maßnahmen ergreifen können. Im Abschnitt **Sicherungswarnungen** werden die von Azure Backup generierten Warnungen angezeigt. Diese Warnungen werden vom Dienst definiert. Es können keine benutzerdefinierten Warnungen erstellt werden.

### <a name="alert-scenarios"></a>Warnungsszenarios

Die folgenden Szenarios werden vom Dienst als solche definiert, in denen Warnungen ausgelöst werden können.

- Fehler bei der Sicherung/Wiederherstellung
- Sicherung für den Microsoft Azure Recovery Services-Agent (MARS) mit Warnungen abgeschlossen
- Beenden des Schutzes unter Beibehaltung der Daten/Beenden des Schutzes inklusive Löschung der Daten
- Funktion für vorläufiges Löschen für Tresor deaktiviert
- Nicht unterstützter Sicherungstyp für Datenbankworkloads

### <a name="alerts-from-the-following-azure-backup-solutions-are-shown-here"></a>Hier sehen Sie die Warnungen der folgenden Azure Backup-Lösungen:

- Azure-VM-Sicherungen
- Azure-Dateisicherungen
- Azure-Workloadsicherungen wie SQL, SAP HANA
- Microsoft Azure Recovery Services-Agent (MARS)

> [!NOTE]
> Warnungen aus System Center Data Protection Manager (SC-DPM) und Microsoft Azure Backup Server (MABS) werden hier nicht aufgeführt.

### <a name="consolidated-alerts"></a>Konsolidierte Warnungen

Für Azure-Workloadsicherungslösungen wie SQL und SAP Hana können Protokollsicherungen sehr häufig generiert werden (gemäß der Richtlinie bis zu alle 15 Minuten). Es ist also auch möglich, dass die Fehler bei der Protokollsicherung sehr häufig auftreten (bis zu alle 15 Minuten). In diesem Szenario ist der Endbenutzer überfordert, wenn für jeden auftretenden Fehler eine Warnung ausgelöst wird. Daher wird für das erste Vorkommen eine Warnung gesendet, und wenn spätere Fehler aufgrund derselben Grundursache auftreten, werden keine weiteren Warnungen generiert. Die erste Warnung wird mit der Fehleranzahl aktualisiert. Wenn die Warnung jedoch vom Benutzer deaktiviert wird, löst das nächste Vorkommen eine andere Warnung aus, und diese wird als erste Warnung für dieses Vorkommen behandelt. Auf diese Weise führt Azure Backup eine Warnungskonsolidierung für SQL- und SAP Hana-Sicherungen durch. Bedarfsgesteuerte Sicherungsaufträge werden nicht konsolidiert.

### <a name="exceptions-when-an-alert-is-not-raised"></a>Ausnahmen, bei denen keine Warnung ausgelöst wird

Es gibt folgende Ausnahmefälle, in denen keine Warnung ausgelöst wird. Sie lauten wie folgt:

- Wenn der Benutzer den ausgeführten Auftrag explizit abgebrochen hat
- Wenn der Auftrag fehlschlägt, weil ein anderer Sicherungsauftrag gerade ausgeführt wird (keine Handlung erforderlich, da gewartet werden muss, bis der aktuelle Auftrag abgeschlossen ist)
- Wenn der Sicherungsauftrag der VM fehlschlägt, weil die gesicherte Azure-VM nicht mehr existiert
- [Konsolidierte Warnungen](#consolidated-alerts)

Diese Ausnahmen wurden eingeführt, da das Ergebnis der oben aufgeführten Vorgänge, die in erster Linie vom Benutzer ausgelöst werden, direkt im Portal bzw. im PowerShell- oder CLI-Client dargestellt wird. Sie sind dem Benutzer also bekannt, sodass keine Benachrichtigung erforderlich ist.

### <a name="alert-types"></a>Warnungstypen

Je nach Schweregrad der Warnung werden diese drei Arten zugeordnet:

- **Kritisch**: Im Prinzip führt jeder Fehler bei der Sicherung oder Wiederherstellung (geplant oder vom Benutzer ausgelöst) zu einer Warnung, die als „Kritisch“ angezeigt wird. Das gilt auch für destruktive Vorgänge wie das Löschen der Sicherung.
- **Warnung:** Wenn der Sicherungsvorgang erfolgreich durchgeführt wurde, aber einige Warnungen auslöst werden, werden diese als „Warnung“ aufgeführt. Warnungen sind zurzeit nur für Sicherungen des Azure Backup-Agents verfügbar.
- **Informativ:** Derzeit werden keine informativen Warnungen von Azure Backup generiert.

## <a name="notification-for-backup-alerts"></a>Benachrichtigungen für Azure Backup-Warnungen

> [!NOTE]
> Sie können die Benachrichtigungen nur über das Azure-Portal konfigurieren. PowerShell, Befehlszeilenschnittstelle, REST-API und Azure Resource Manager-Vorlagen werden nicht unterstützt.

Wenn eine Warnung ausgelöst wird, werden die Benutzer benachrichtigt. Azure Backup enthält einen Mechanismus, der Benachrichtigungen per E-Mail sendet. Sie können einzelne E-Mail-Adressen oder Verteilerlisten angeben, die benachrichtigt werden sollen, wenn eine Warnung generiert wurde. Sie können ebenfalls Benachrichtigungen für jede einzelne Warnung aktivieren oder diese in einer stündlichen Übersicht gruppieren.

![Screenshot: in den Recovery Services-Tresor integrierte E-Mail-Benachrichtigung](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltnotification.png)

Wenn Sie die Benachrichtigungen konfiguriert haben, erhalten Sie eine Begrüßungs-E-Mail bzw. eine Einführungs-E-Mail. Dadurch wird bestätigt, dass Azure Backup E-Mails an diese Adressen senden kann, wenn eine Warnung ausgelöst wird.<br>

Wenn die Häufigkeit auf eine stündliche Übersicht festgelegt und eine Warnung innerhalb einer Stunde ausgelöst und behoben wurde, ist diese nicht in der stündlichen Übersicht enthalten.

> [!NOTE]
>
> - Wenn ein destruktiver Vorgang wie das **Beenden des Schutzes inklusive Löschung** der Daten durchgeführt wird, wird eine Warnung ausgelöst und eine E-Mail an den Besitzer des Abonnements sowie an die Administratoren und Co-Admins des Abonnements gesendet, auch wenn die Benachrichtigungen für den Recovery Services-Tresor nicht konfiguriert wurden.
> - Verwenden Sie [Log Analytics](backup-azure-monitoring-use-azuremonitor.md#using-log-analytics-workspace), um Benachrichtigungen für erfolgreiche Aufträge zu konfigurieren.

## <a name="inactivating-alerts"></a>Deaktivieren von Warnungen

Um eine aktive Warnung zu deaktivieren bzw. aufzulösen, können Sie das Listenelement auswählen, das der Warnung entspricht, die Sie deaktivieren möchten. Dadurch wird ein Bildschirm geöffnet, auf dem detaillierte Informationen zur Warnung angezeigt werden. Oberhalb der Warnung finden Sie eine Schaltfläche zum **Deaktivieren**. Wenn Sie diese Schaltfläche auswählen, ändert sich der Status der Warnung zu **Inaktiv**. Sie können eine Warnung auch deaktivieren, indem Sie mit der rechten Maustaste auf das Listenelement klicken, das dieser Warnung entspricht, und dann **Deaktivieren** auswählen.

![Screenshot: Deaktivierung von Backup Center-Warnungen](media/backup-azure-monitoring-laworkspace/vault-alert-inactivate.png)

## <a name="azure-monitor-alerts-for-azure-backup-preview"></a>Azure Monitor-Warnungen für Azure Backup (Vorschau)

Azure Backup stellt über Azure Monitor auch Warnungen bereit, um Benutzern übergreifend über unterschiedliche Azure-Dienste (einschließlich Azure Backup) eine einheitliche Oberfläche für die Warnungsverwaltung zu bieten. Mit Azure Monitor-Warnungen können Sie Warnungen an jeden Benachrichtigungskanal weiterleiten, der von Azure Monitor unterstützt wird, z. B. E-Mail, ITSM, Webhook, eine Logik-App usw.

Derzeit stehen in Azure Backup zwei Haupttypen von integrierten Warnungen zur Verfügung:

* **Sicherheitswarnungen:** In Szenarios wie dem Löschen von Sicherungsdaten oder dem Deaktivieren des vorläufigen Löschens für einen Tresor werden Sicherheitswarnungen (mit dem Schweregrad 0) ausgelöst und im Azure-Portal angezeigt oder von anderen Clients (PowerShell, CLI und REST-API) verarbeitet. Sicherheitswarnungen werden standardmäßig generiert und können nicht deaktiviert werden. Sie können jedoch die Szenarios steuern, für die die Benachrichtigungen (z. B. E-Mails) ausgelöst werden sollen. Weitere Informationen zum Konfigurieren von Benachrichtigungen finden Sie unter [Aktionsregeln](../azure-monitor/alerts/alerts-action-rules.md).
* **Auftragsfehlerwarnungen:** Für Szenarios wie Sicherungs- und Wiederherstellungsfehler stellt Azure Backup integrierte Warnungen über Azure Monitor (Schweregrad 1) bereit. Im Gegensatz zu Sicherheitswarnungen können Sie Azure Monitor-Warnungen für Auftragsfehlerszenarios deaktivieren, z. B. wenn Sie bereits über Log Analytics benutzerdefinierte Warnungsregeln für Auftragsfehler konfiguriert haben und nicht bei jedem Auftragsfehler integrierte Warnungen ausgelöst werden müssen. Warnungen zu Auftragsfehlern sind standardmäßig deaktiviert. Weitere Informationen finden Sie im Abschnitt zum [Aktivieren von Warnungen für diese Szenarios](#turning-on-azure-monitor-alerts-for-job-failure-scenarios).
 
In der folgenden Tabelle werden die verschiedenen, derzeit über Azure Monitor (als Vorschau) verfügbaren Sicherungswarnungen und die unterstützten Workload-/Tresortypen zusammengefasst:

| **Warnungskategorie** | **Name der Warnung** | **Unterstützte Workload-/Tresortypen** | **Beschreibung** | 
| ------------------ | -------------  | ------------------------------------------ | -------- |
| Sicherheit | Löschen von Sicherungsdaten | <li> Virtueller Azure-Computer <br><br> <li> SQL auf Azure-VMs (Szenarios ohne Verfügbarkeitsgruppen) <br><br> <li> SAP HANA in Azure-VM <br><br> <li> Azure Backup-Agent <br><br> <li> DPM <br><br> <li> Azure Backup Server <br><br> <li> Azure Database for PostgreSQL-Server <br><br> <li> Azure-Blobs <br><br> <li> Azure Managed Disks  | Diese Warnung wird ausgelöst, wenn ein Benutzer die Sicherung beendet und Sicherungsdaten löscht. (Hinweis: Wenn das Feature zum vorläufigen Löschen für den Tresor deaktiviert ist, wird die Warnung „Sicherungsdaten löschen“ nicht empfangen.) |
| Sicherheit | Upcoming Purge (Anstehende Bereinigung) | <li> Virtueller Azure-Computer <br><br> <li> SQL auf Azure-VMs (Szenarios ohne Verfügbarkeitsgruppen) <br><br> <li> SAP HANA in Azure-VM | Diese Warnung wird für alle Workloads ausgelöst, die das vorläufige Löschen unterstützen, wenn Sicherungsdaten für ein Element in 2 Tagen dauerhaft von Azure Backup gelöscht werden. | 
| Sicherheit | Der Löschvorgang ist abgeschlossen. | <li> Virtueller Azure-Computer <br><br> <li> SQL auf Azure-VMs (Szenarios ohne Verfügbarkeitsgruppen) <br><br> <li> SAP HANA in Azure-VM | Löschen von Sicherungsdaten |
| Sicherheit | Vorläufiges Löschen für den Tresor deaktiviert | Recovery Services-Tresore | Diese Warnung wird ausgelöst, wenn die vorläufig gelöschten Sicherungsdaten eines Elements endgültig von Azure Backup gelöscht wurden. | 
| Aufträge | Fehler bei der Sicherung. | <li> Virtueller Azure-Computer <br><br> <li> SQL auf Azure-VMs (Szenarios ohne Verfügbarkeitsgruppen) <br><br> <li> SAP HANA in Azure-VM <br><br> <li> Azure Backup-Agent <br><br> <li> Azure Files <br><br> <li> Azure Database for PostgreSQL-Server <br><br> <li> Azure Managed Disks | Diese Warnung wird ausgelöst, wenn bei einem Sicherungsauftrag ein Fehler aufgetreten ist. Warnungen zu Sicherungsfehlern sind standardmäßig deaktiviert. Weitere Informationen finden Sie im Abschnitt zum [Aktivieren von Warnungen für dieses Szenario](#turning-on-azure-monitor-alerts-for-job-failure-scenarios). | 
| Aufträge | Fehler bei der Wiederherstellung. | <li> Virtueller Azure-Computer <br><br> <li> SQL auf Azure-VMs (Szenarios ohne Verfügbarkeitsgruppen) <br><br> <li> SAP HANA in Azure-VM <br><br> <li> Azure Backup-Agent <br><br> <li> Azure Files <br><br> <li> Azure Database for PostgreSQL-Server <br><br> <li> Azure-Blobs <br><br> <li> Azure Managed Disks| Diese Warnung wird ausgelöst, wenn bei einem Wiederherstellungsauftrag ein Fehler aufgetreten ist. Warnungen zu Wiederherstellungsfehlern sind standardmäßig deaktiviert. Weitere Informationen finden Sie im Abschnitt zum [Aktivieren von Warnungen für dieses Szenario](#turning-on-azure-monitor-alerts-for-job-failure-scenarios). | 

### <a name="turning-on-azure-monitor-alerts-for-job-failure-scenarios"></a>Aktivieren von Azure Monitor-Warnungen für Szenarios mit Auftragsfehlern

Führen Sie die folgenden Schritte aus, um Azure Monitor-Warnungen in Szenarien mit Sicherungs- und Wiederherstellungsfehlern zu aktivieren:

1. Öffnen Sie das Azure-Portal, und suchen Sie nach **Previewfunktionen**.

    ![Screenshot: Anzeigen der Previewfunktionen im Portal](media/backup-azure-monitoring-laworkspace/portal-preview-features.png)

2. Sie können die Liste aller Previewfunktionen anzeigen, die Sie aktivieren können.

    * Wenn Sie Auftragsfehlerwarnungen für Workloads erhalten möchten, die in Recovery Services-Tresoren gesichert sind, wählen Sie das Flag **EnableAzureBackupJobFailureAlertsToAzureMonitor** aus, das zu dem Anbieter „Microsoft.RecoveryServices“ (Spalte 3) gehört.
    * Wenn Sie Auftragsfehlerwarnungen für Workloads erhalten möchten, die in den Sicherungstresoren gesichert sind, wählen Sie das Flag **EnableAzureBackupJobFailureAlertsToAzureMonitor** aus, das zu dem Anbieter „Microsoft.DataProtection“ (Spalte 3) gehört.

    ![Screenshot: Vorschau der Warnungsregistrierung](media/backup-azure-monitoring-laworkspace/alert-preview-feature-flags.png)

3. Klicken Sie auf **Registrieren**, um dieses Feature für Ihr Abonnement zu aktivieren.
    > [!NOTE]
    > Es kann bis zu 24 Stunden dauern, bis die Registrierung wirksam wird. Wenn Sie dieses Feature für mehrere Abonnements aktivieren möchten, wiederholen Sie den obigen Vorgang, indem Sie das relevante Abonnement oben im Bildschirm auswählen. Es wird außerdem empfohlen, das Vorschauflag noch mal zu registrieren, um weiterhin Warnungen zu erhalten, wenn im Abonnement nach der ersten Registrierung eine neue Ressource erstellt wurde.

4. Als bewährte Methode wird auch empfohlen, den Ressourcenanbieter zu registrieren, um sicherzustellen, dass die Informationen zur Registrierung von Funktionen wie erwartet mit dem Azure Backup synchronisiert werden. Führen Sie zum Registrieren des Ressourcenanbieters den folgenden PowerShell-Befehl in dem Abonnement aus, für das Sie das Featureflag registriert haben.

```powershell
Register-AzResourceProvider -ProviderNamespace <ProviderNamespace>
```

Verwenden Sie zum Empfangen von Warnungen für Recovery Services-Tresore den Wert _Microsoft.RecoveryServices_ für den _Parameter ProviderNamespace._ Um Warnungen für Backup-Tresore zu empfangen, verwenden Sie den Wert _Microsoft.DataProtection_.

### <a name="viewing-fired-alerts-in-the-azure-portal"></a>Anzeigen ausgelöster Warnungen im Azure-Portal 

Sobald eine Warnung für einen Tresor ausgelöst wurde, können Sie die Warnung im Azure-Portal anzeigen, indem Sie zu Backup Center navigieren. Auf der Registerkarte **Überblick** wird eine Übersicht der aktiven Warnungen nach Schweregrad angezeigt. Es werden zwei Arten von Warnungen angezeigt:

* **Datenquellenwarnungen:** Warnungen, die an eine bestimmte Datenquelle gebunden sind, die gerade gesichert wird (z. B. Sicherungs- oder Wiederherstellungsfehler auf einer VM, Löschen von Sicherungsdaten einer Datenbank usw.), werden im Abschnitt **Datenquellenwarnungen** angezeigt.
* **Globale Warnungen:** Warnungen, die nicht an eine bestimmte Datenquelle gebunden sind (z. B. das Deaktivieren der Funktion eines Tresors für das vorläufige Löschen), werden im Abschnitt **Global Alerts** (Globale Warnungen) angezeigt.

Alle oben genannten Warnungstypen werden weiter in Warnungen der Typen **Sicherheit** und **konfiguriert** unterteilt. Derzeit decken Sicherheitswarnungen Szenarios ab, in denen Sicherungsdaten gelöscht werden oder das vorläufige Löschen für einen Tresor (wie im obigen Abschnitt beschrieben für die infrage kommenden Workloads) deaktiviert wird. Konfigurierte Warnungen decken Sicherungs- und Wiederherstellungsfehler ab, da diese Warnungen erst nach der Registrierung des Features im Vorschauportal ausgelöst werden.

![Screenshot: Anzeigen von Warnungen in Backup Center](media/backup-azure-monitoring-laworkspace/backup-center-azure-monitor-alerts.png)

Wenn Sie auf eine der Zahlen (oder auf das Menüelement **Benachrichtigungen**) klicken, wird eine Liste aller aktiven Warnungen geöffnet, die unter Anwendung der relevanten Filter ausgelöst wurden. Sie können nach einer Reihe von Eigenschaften filtern, z. B. Abonnement, Ressourcengruppe, Tresor, Schweregrad, Status usw. Sie können auf jede der Warnungen klicken, um weitere Details zur Warnung zu erhalten, z. B. die betroffene Datenquelle, eine Warnungsbeschreibung und eine empfohlene Aktion usw.

![Screenshot: Anzeigen von Warnungsdetails](media/backup-azure-monitoring-laworkspace/backup-center-alert-details.png) 

Sie können den Status einer Warnung in **Bestätigt** oder **Geschlossen** ändern, indem Sie auf **Warnungsstatus ändern** klicken.

![Screenshot: Ändern des Warnungsstatus](media/backup-azure-monitoring-laworkspace/backup-center-change-alert-state.png) 

> [!NOTE]
> - In Backup Center werden derzeit nur Warnungen für Azure-basierte Workloads angezeigt. Wenn Sie Warnungen zu lokalen Ressourcen anzeigen möchten, müssen Sie zum Recovery Services-Tresor navigieren und auf das Menüelement **Benachrichtigungen** klicken.
> - In Backup Center werden nur Azure Monitor-Warnungen angezeigt. Warnungen, die von der älteren Warnungslösung ausgelöst wurden, (auf die über die Registerkarte [Sicherungswarnungen](#backup-alerts-in-recovery-services-vault) im Recovery Services-Tresor zugegriffen wird,) werden in Backup Center nicht angezeigt.
Weitere Informationen zu Azure Monitor-Warnungen finden Sie unter [Überblick über Warnungen in Azure](../azure-monitor/alerts/alerts-overview.md).

### <a name="configuring-notifications-for-alerts"></a>Konfigurieren von Benachrichtigungen für Warnungen

Wenn Sie Benachrichtigungen für Azure Monitor-Warnungen konfigurieren möchten, müssen Sie eine Aktionsregel erstellen. Die folgenden Schritte veranschaulichen, wie Sie eine Aktionsregel erstellen, um E-Mail-Benachrichtigungen an eine bestimmte E-Mail-Adresse zu senden. Ähnliche Anweisungen gelten für das Weiterleiten dieser Warnungen an andere Benachrichtigungskanäle wie ITSM, Webhook, eine Logik-App usw.

1. Navigieren Sie im Azure-Portal zu **Backup Center**. Klicken Sie auf das Menüelement **Benachrichtigungen**, und wählen Sie **Aktionen verwalten** aus.

    ![Screenshot: „Aktionen verwalten“ in Backup Center](media/backup-azure-monitoring-laworkspace/backup-center-manage-actions.png) 

2. Navigieren Sie zur Registerkarte **Aktionsregeln (Vorschau)** , und klicken Sie auf **Neue Aktionsregel**.

    ![Screenshot: Erstellen einer neuen Aktionsregel](media/backup-azure-monitoring-laworkspace/azure-monitor-create-action-rule.png) 

3. Wählen Sie den Bereich aus, auf den die Aktionsregel angewendet werden soll. Sie können die Aktionsregel auf alle Ressourcen innerhalb eines Abonnements anwenden. Optional können Sie auch Filter auf die Warnungen anwenden, um beispielsweise nur Benachrichtigungen für Warnungen mit einem bestimmten Schweregrad zu generieren.

    ![Screenshot: Festlegen des Geltungsbereichs einer Aktionsregel](media/backup-azure-monitoring-laworkspace/azure-monitor-action-rule-scope.png) 

4. Erstellen Sie eine Aktionsgruppe. Eine Aktionsgruppe ist das Ziel, an das die Warnungsbenachrichtigung gesendet werden soll, z. B. eine E-Mail-Adresse.
 
    ![Screenshot: Erstellen einer neuen Aktionsgruppe](media/backup-azure-monitoring-laworkspace/azure-monitor-create-action-group.png) 

5. Wählen Sie auf der Registerkarte **Allgemeine Informationen** den Namen der Aktionsgruppe sowie das Abonnement und die Ressourcengruppe aus, unter denen die Gruppe erstellt werden soll.

    ![Screenshot: grundlegende Eigenschaften der Aktionsgruppe](media/backup-azure-monitoring-laworkspace/azure-monitor-action-groups-basic.png) 

6. Wählen Sie auf der Registerkarte **Benachrichtigungen** die Option **Email/SMS message/Push/Voice** (E-Mail/SMS/Push/Sprachanruf) aus, und geben Sie die E-Mail-ID des Empfängers ein.

    ![Screenshot: Festlegen von Benachrichtigungseigenschaften](media/backup-azure-monitoring-laworkspace/azure-monitor-email.png) 

7. Klicken Sie zum Bereitstellen der Aktionsgruppe auf **Überprüfen und erstellen** und dann auf **Erstellen**.

8. Speichern Sie abschließend die Aktionsregel.

[Weitere Informationen zu Aktionsregeln in Azure Monitor](../azure-monitor/alerts/alerts-action-rules.md)


## <a name="next-steps"></a>Nächste Schritte

[Überwachen von Azure Backup-Workloads mithilfe von Azure Monitor](backup-azure-monitoring-use-azuremonitor.md)
