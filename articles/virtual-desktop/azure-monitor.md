---
title: Verwenden von Azure Monitor für Azure Virtual Desktop – Azure
description: 'Vorgehensweise: Verwenden von Azure Monitor für Azure Virtual Desktop.'
author: Heidilohr
ms.topic: how-to
ms.date: 03/31/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: b985ec9e88f4285da326fcf41e19141e61a0dd10
ms.sourcegitcommit: e0ef8440877c65e7f92adf7729d25c459f1b7549
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/09/2021
ms.locfileid: "113564192"
---
# <a name="use-azure-monitor-for-azure-virtual-desktop-to-monitor-your-deployment"></a>Verwenden von Azure Monitor für Azure Virtual Desktop zum Überwachen Ihrer Bereitstellung

Azure Monitor für Azure Virtual Desktop ist ein Dashboard, das auf Azure Monitor Workbooks basiert und IT-Experten dabei unterstützt, ihre Azure Virtual Desktop-Umgebungen zu verstehen. In diesem Thema wird erläutert, wie Sie Azure Monitor für Azure Virtual Desktop zur Überwachung Ihrer Azure Virtual Desktop-Umgebungen einrichten.

## <a name="requirements"></a>Requirements (Anforderungen)

Bevor Sie Azure Monitor für Azure Virtual Desktop verwenden, müssen folgende Einrichtungsaufgaben erfüllt sein:

- Alle überwachten Azure Virtual Desktop-Umgebungen müssen auf der neuesten Version von Azure Virtual Desktop basieren, die mit Azure Resource Manager kompatibel ist.
- Mindestens ein Log Analytics-Arbeitsbereich muss konfiguriert sein. Verwenden Sie einen bestimmten Log Analytics-Arbeitsbereich für Ihre Azure Virtual Desktop-Sitzungshosts, um sicherzustellen, dass Leistungszähler und Ereignisse nur von Sitzungshosts in Ihrer Azure Virtual Desktop-Bereitstellung erfasst werden.
- Aktivieren Sie die Datensammlung für die folgenden Elemente in Ihrem Log Analytics-Arbeitsbereich:
    - Diagnose aus Ihrer Azure Virtual Desktop-Umgebung
    - Empfohlene Leistungsindikatoren von Ihren Azure Virtual Desktop-Sitzungshosts
    - Empfohlene Windows-Ereignisprotokolle von Ihren Azure Virtual Desktop-Sitzungshosts

 Der in diesem Artikel beschriebene Dateneinrichtungsprozess ist der Einzige, den Sie zum Überwachen von Azure Virtual Desktop benötigen. Sie können alle anderen Elemente, die Daten an Ihren Log Analytics Arbeitsbereich senden, deaktivieren, um Kosten zu sparen.

Jeder Benutzer, der Azure Monitor für Azure Virtual Desktop für Ihre Umgebung einsetzt, benötigt außerdem die folgenden Lesezugriffsberechtigungen:

- Lesezugriff auf die Azure-Abonnements, die Ihre Azure Virtual Desktop-Ressourcen enthalten
- Lesezugriff auf die Ressourcengruppen des Abonnements, die Ihre Azure Virtual Desktop-Sitzungshosts enthalten
- Lesezugriff auf den Log Analytics-Arbeitsbereich

>[!NOTE]
> Der Lesezugriff ermöglicht Administratoren nur das Anzeigen von Daten. Zum Verwalten von Ressourcen im Azure Virtual Desktop-Portal sind andere Berechtigungen erforderlich.

## <a name="open-azure-monitor-for-azure-virtual-desktop"></a>Öffnen von Azure Monitor für Azure Virtual Desktop

Sie können Azure Monitor für Azure Virtual Desktop mit einer der folgenden Methoden öffnen:

- Wechseln Sie zu [aka.ms/azmonwvdi](https://aka.ms/azmonwvdi).
- Suchen Sie im Azure-Portal nach **Azure Virtual Desktop**, und wählen Sie diese Option aus. Dann wählen Sie **Insights** aus.
- Suchen Sie im Azure-Portal nach **Azure Monitor**, und wählen Sie diese Option. Wählen Sie unter **Insights** die Option **Insights Hub** und dann **Azure Virtual Desktop** aus.
Wenn die Seite geöffnet ist, geben Sie das **Abonnement**, die **Ressourcengruppe**, den **Host Pool** und den **Zeitbereich** der Umgebung ein, die Sie überwachen möchten.

>[!NOTE]
>Azure Virtual Desktop unterstützt derzeit nur jeweils das Überwachen eines Abonnements, einer Ressourcengruppe und eines Hostpools. Wenn Sie die zu überwachende Umgebung nicht finden können, suchen Sie in der [Dokumentation zur Problembehandlung](troubleshoot-azure-monitor.md) nach bekannten Funktionsanforderungen und Problemen.

## <a name="log-analytics-settings"></a>Log-Analyseeinstellungen

Um Azure Monitor für Azure Virtual Desktop verwenden zu können, benötigen Sie mindestens einen Log Analytics-Arbeitsbereich. Verwenden Sie einen bestimmten Log Analytics-Arbeitsbereich für Ihre Azure Virtual Desktop-Sitzungshosts, um sicherzustellen, dass Leistungszähler und Ereignisse nur von Sitzungshosts in Ihrer Azure Virtual Desktop-Bereitstellung erfasst werden. Wenn Sie bereits einen Arbeitsbereich eingerichtet haben, fahren Sie [mit Einrichten unter Verwendung der Konfigurations-Arbeitsmappe](#set-up-using-the-configuration-workbook)fort. Zum Einrichten, siehe [Erstellen eines Log Analytics-Arbeitsbereichs im Azure-Portal](../azure-monitor/logs/quick-create-workspace.md).

>[!NOTE]
>Es gelten die Standardgebühren für Datenspeicher für Log Analytics. Für den Einstieg empfiehlt es sich, das Modell mit nutzungsbasierter Bezahlung auszuwählen und dann beim Skalieren der Bereitstellung und der Aufnahme von mehr Daten anzupassen. Weitere Informationen finden Sie unter [Azure Monitor – Preise](https://azure.microsoft.com/pricing/details/monitor/).

## <a name="set-up-using-the-configuration-workbook"></a>Einrichten mit Hilfe der Konfigurations-Arbeitsmappe

Wenn Sie Azure Monitor für Azure Virtual Desktop zum ersten Mal öffnen, müssen Sie Azure Monitor für Ihre Azure Virtual Desktop-Umgebung einrichten. So konfigurieren Sie die Ressourcen:

1. Öffnen Sie Azure Monitor for Azure Virtual Desktop im Azure-Portal unter [aka.ms/azmonwvdi](https://aka.ms/azmonwvdi), und wählen Sie dann die **Konfigurations-Arbeitsmappe** aus.
2. Wählen Sie unter **Abonnement**, **Ressourcengruppe** und **Host-Pool**, eine zu konfigurierende Umgebung aus.

Die Konfigurationsarbeitsmappe richtet Ihre Überwachungsumgebung ein und ermöglicht Ihnen eine Überprüfung der Konfiguration, nachdem Sie den Einrichtungsvorgang abgeschlossen haben. Es ist wichtig, Ihre Konfiguration zu überprüfen, wenn Elemente im Dashboard nicht korrekt angezeigt werden oder wenn die Produktgruppe Aktualisierungen veröffentlicht, die neue Einstellungen erfordern.

### <a name="resource-diagnostic-settings"></a>Diagnoseeinstellungen für Ressourcen

Zum Erfassen von Informationen über die Azure Virtual Desktop-Infrastruktur müssen Sie mehrere Diagnoseeinstellungen auf Ihren Azure Virtual Desktop-Hostpools und -Arbeitsbereichen aktivieren (dies ist Ihr Azure Virtual Desktop-Arbeitsbereich, nicht Ihr Log Analytics-Arbeitsbereich). Weitere Informationen zu Hostpools, Arbeitsbereichen und anderen Azure Virtual Desktop-Ressourcenobjekten finden Sie in unserem [Umgebungshandbuch](environment-setup.md).

Weitere Informationen zur Azure Virtual Desktop-Diagnose und den unterstützten Diagnosetabellen finden Sie unter [Verwenden von Log Analytics für die Diagnosefunktion](diagnostics-log-analytics.md).

So legen Sie Ihre Einstellungen für die Ressourcendiagnose in der Konfigurationsarbeitsmappe fest: 

1. Wählen Sie die Registerkarte **Ressourcen-Diagnose-Einstellungen** in der Konfigurations-Arbeitsmappe aus. 
2. Wählen Sie **Log Analytics-Arbeitsbereich** aus, um die Azure Virtual Desktop-Diagnose zu senden. 

#### <a name="host-pool-diagnostic-settings"></a>Diagnoseeinstellungen für den Hostpool

So richten Sie die Host Pool Diagnose mithilfe des Abschnitts Diagnose Einstellungen für Ressourcen in der Konfigurations-Arbeitsmappe ein:

1. Prüfen Sie unter **Hostpool**, ob die Diagnose von Azure Virtual Desktop aktiviert ist. Wenn dies nicht der Fall ist, wird eine Fehlermeldung angezeigt, die besagt: "Es wurde keine vorhandene Diagnosekonfiguration für den ausgewählten Hostpool gefunden." Sie müssen die folgenden unterstützten Diagnosetabellen aktivieren:

    - Prüfpunkt
    - Fehler
    - Verwaltung
    - Verbindung
    - HostRegistration
    - AgentHealthStatus
    
    >[!NOTE]
    > Wenn die Fehlermeldung nicht angezeigt wird, müssen Sie die Schritte 2 bis 4 nicht ausführen.

2. Wählen Sie **Hostpool konfigurieren** aus.
3. Klicken Sie auf **Bereitstellen**.
4. Aktualisieren Sie die Konfigurationsarbeitsmappe.

#### <a name="workspace-diagnostic-settings"></a>Arbeitsbereich-Diagnoseeinstellungen 

So richten Sie die Arbeitsbereichsdiagnose mithilfe des Abschnitts für die Ressourcendiagnoseeinstellungen in der Konfigurationsarbeitsmappe ein:

 1. Überprüfen Sie unter **Arbeitsbereich**, ob die Azure Virtual Desktop-Diagnose für den Azure Virtual Desktop-Arbeitsbereich aktiviert ist. Wenn dies nicht der Fall ist, wird eine Fehlermeldung angezeigt, die besagt: "Es wurde keine vorhandene Diagnosekonfiguration für den ausgewählten Hostpool gefunden." Sie müssen die folgenden unterstützten Diagnosetabellen aktivieren:
 
    - Prüfpunkt
    - Fehler
    - Verwaltung
    - Feed
    
    >[!NOTE]
    > Wenn die Fehlermeldung nicht angezeigt wird, müssen Sie die Schritt 2-4 nicht ausführen.

2. Wählen Sie **Konfigurieren eines Arbeitsbereichs**.
3. Klicken Sie auf **Bereitstellen**.
4. Aktualisieren Sie die Konfigurationsarbeitsmappe.

### <a name="session-host-data-settings"></a>Einstellungen für Sitzungshostdaten

Um Informationen über Ihre Azure Virtual Desktop-Sitzungshosts zu sammeln, müssen Sie den Log Analytics-Agenten auf allen Sitzungshosts im Hostpool installieren, sicherstellen, dass die Sitzungshosts an einen Log Analytics-Arbeitsbereich senden, und die Einstellungen des Log Analytics-Agenten so konfigurieren, dass Leistungsdaten und Windows-Ereignisprotokolle gesammelt werden.

Der Arbeitsbereich Log Analytics, an den Sie Sitzungshost-Daten senden, muss nicht identisch sein, an den Sie Diagnosedaten senden. Wenn Sie über Azure-Sitzungshosts außerhalb Ihrer Azure Virtual Desktop-Umgebung verfügen, sollten Sie einen bestimmten Log Analytics-Arbeitsbereich für die Azure Virtual Desktop-Sitzungshosts haben. 

So legen Sie den Log Analytics Arbeitsbereich fest, in dem Sie Sitzungs-Hostdaten sammeln möchten: 

1. Wählen Sie die Registerkarte **Sitzungs-Host Dateneinstellungen** in der Konfigurations Arbeitsmappe aus. 
2. Hiermit wählen Sie den **Log Analytics-Arbeitsbereich** aus, an den Sitzungs-Hostdaten gesendet werden sollen. 

#### <a name="session-hosts"></a>Sitzungshosts

Im letzten Schritt müssen Sie den Log Analytics-Agent auf allen Hosts im Host-Pool installieren, um Daten von den Hosts an den ausgewählten Arbeitsbereich zu senden. Wenn Log Analytics nicht für alle Sitzungshosts im Hostpool konfiguriert ist, wird oben in den Einstellungen für **Sitzungshostdaten** ein Abschnitt **Sitzungshosts** mit der Meldung „Einige Hosts im Hostpool senden keine Daten an den ausgewählten Log Analytics-Arbeitsbereich."

>[!NOTE]
> Wenn Sie den Abschnitt **Sitzungshosts** oder eine Fehlermeldung nicht sehen, sind alle Sitzungshosts korrekt eingerichtet. Fahren Sie mit dem Einrichten der Anweisungen für [Arbeitsbereichs-Leistungsindikatoren](#workspace-performance-counters)fort.

So richten Sie die restlichen Sitzungs-Hosts mithilfe der Konfigurations-Arbeitsmappe ein:

1. Wählen Sie **Hosts dem Arbeitsbereich** hinzufügen. 
2. Aktualisieren Sie die Konfigurationsarbeitsmappe.

>[!NOTE]
>Der Hostcomputer muss aktiv sein, um die Log Analytics-Erweiterung zu installieren. Wenn bei der automatischen Bereitstellung auf einem Host ein Fehler auftritt, können Sie die Erweiterung immer auch manuell auf einem Host installieren. Informationen zum manuellen Installieren der Erweiterung finden Sie unter [Log Analytics-VM-Erweiterung für Windows](../virtual-machines/extensions/oms-windows.md).

#### <a name="workspace-performance-counters"></a>Leistungsindikatoren für den Arbeitsbereich

Sie müssen bestimmte Leistungsindikatoren aktivieren, um Leistungsinformationen von ihren Sitzungs-Hosts zu sammeln und an den Arbeitsbereich Log Analytics zu senden.

Wenn Sie bereits Leistungsindikatoren aktiviert haben und diese entfernen möchten, folgen Sie den Anweisungen unter [Konfigurieren von Leistungsindikatoren](../azure-monitor/agents/data-sources-performance-counters.md). Sie können Leistungszähler an der gleichen Stelle hinzufügen und entfernen.

So richten Sie Leistungsindikatoren mithilfe der Konfigurations-Arbeitsmappe ein: 

1. Aktivieren Sie unter Arbeitsbereichs- **Leistungsindikatoren** in der Konfigurations Arbeitsmappe die Option **konfigurierte** Indikatoren, um die Indikatoren anzuzeigen, die Sie bereits für das Senden an den Arbeitsbereich Log Analytics aktiviert haben. Überprüfen Sie **fehlende Zähler**, um sicherzustellen, dass Sie alle erforderlichen Zähler aktiviert haben.
2. Wenn Sie fehlende Zähler haben, wählen Sie **Leistungsindikatoren konfigurieren** aus.
3. Wählen Sie **Konfiguration anwenden** aus.
4. Aktualisieren Sie die Konfigurationsarbeitsmappe.
5. Stellen Sie sicher, dass alle erforderlichen Indikatoren aktiviert sind, indem Sie die Liste **fehlende** Leistungsindikatoren überprüfen. 

#### <a name="configure-windows-event-logs"></a>Konfigurieren der Windows-Ereignisprotokolle

Außerdem müssen Sie bestimmte Windows-Ereignisprotokolle aktivieren, um Fehler, Warnungen und Informationen von den Sitzungs-Hosts zu erfassen und diese an den Arbeitsbereich Log Analytics zu senden.

Wenn Sie Windows-Ereignisprotokolle bereits aktiviert haben und diese entfernen möchten, befolgen Sie die Anweisungen unter [Konfigurieren von Windows-Ereignisprotokollen](../azure-monitor/agents/data-sources-windows-events.md#configuring-windows-event-logs).  Sie können Windows-Ereignisprotokolle am gleichen Standort hinzufügen und entfernen.

So richten Sie Windows-Ereignisprotokolle anhand der Konfigurations-Arbeitsmappe ein:

1. Überprüfen Sie unter **Konfiguration von Windows-Ereignisprotokollen** die Option **konfigurierte Ereignisprotokolle**, um die Ereignisprotokolle anzuzeigen, die Sie bereits für das Senden an den Arbeitsbereich Log Analytics freigegeben haben. Überprüfen Sie **fehlende Ereignisprotokolle**, um sicherzustellen, dass Sie alle Windows-Ereignisprotokolle aktiviert haben.
2. Wenn Sie über fehlende Windows-Ereignisprotokolle verfügen, wählen Sie **Ereignisse konfigurieren** aus.
3. Klicken Sie auf **Bereitstellen**.
4. Aktualisieren Sie die Konfigurationsarbeitsmappe.
5. Stellen Sie sicher, dass alle erforderlichen Windows-Ereignisprotokolle aktiviert sind, indem Sie die Liste der **fehlenden Ereignisprotokolle** überprüfen. 

>[!NOTE]
>Wenn die automatische Ereignisbereitstellung fehlschlägt, wählen Sie **Agentenkonfiguration öffnen** in der Konfigurationsarbeitsmappe, um fehlende Windows-Ereignisprotokolle manuell hinzuzufügen. 

## <a name="optional-configure-alerts"></a>Optional: Konfigurieren von Warnungen

Azure Monitor für Azure Virtual Desktop ermöglicht Ihnen die Überwachung von Azure Monitor-Warnungen, die innerhalb Ihres ausgewählten Abonnements im Kontext Ihrer Azure Virtual Desktop-Daten auftreten. Bei Azure Monitor-Warnungen handelt es sich um ein optionales Feature in Ihren Azure-Abonnements, und Sie müssen sie separat von Azure Monitor für Azure Virtual Desktop einrichten. Sie können das Azure Monitor-Warnungsframework verwenden, um benutzerdefinierte Warnungen für Azure Virtual Desktop-Ereignisse, -Diagnosen und -Ressourcen festzulegen. Weitere Informationen zu Azure Monitor-Warnungen finden sie unter [Erstellen, Anzeigen und Verwalten von Protokollwarnungen mithilfe von Azure Monitor](../azure-monitor/alerts/alerts-log.md).

## <a name="diagnostic-and-usage-data"></a>Diagnose- und Nutzungsdaten

Wenn Sie den Azure Monitor-Dienst verwenden, sammelt Microsoft automatisch Nutzungs- und Leistungsdaten. Microsoft verwendet diese Daten, um die Qualität, Sicherheit und Integrität des Diensts zu verbessern.

Um genaue und effiziente Funktionen zur Problembehandlung bereitzustellen, enthalten die gesammelten Daten die Portalsitzungs-ID, die Azure Active Directory-Benutzer-ID und den Namen der Portalregisterkarte, auf der das Ereignis aufgetreten ist. Microsoft erfasst weder Namen noch Adressen oder andere Kontaktinformationen.

Weitere Informationen zur Sammlung und Nutzung von Daten finden Sie in den [Datenschutzbestimmungen für Onlinedienste von Microsoft](https://privacy.microsoft.com/privacystatement).

>[!NOTE]
>Informationen zum Anzeigen oder Löschen Ihrer personenbezogenen Daten, die vom Dienst gesammelt werden, finden Sie unter [Anträge betroffener Personen für Azure im Rahmen der DSGVO](/microsoft-365/compliance/gdpr-dsr-azure). Allgemeine Informationen zur DSGVO finden Sie im [Abschnitt über DSGVO für Service Trust Portal](https://servicetrust.microsoft.com/ViewPage/GDPRGetStarted).

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun Azure Monitor für Ihre Azure Virtual Desktop-Umgebung konfiguriert haben, finden Sie hier einige Ressourcen, die Ihnen bei der Überwachung Ihrer Umgebung helfen könnten:

- Sehen Sie sich unser [Glossar](azure-monitor-glossary.md) an, um mehr über Begriffe und Konzepte im Zusammenhang mit Azure Monitor für Azure Virtual Desktop zu erfahren.
- Informationen dazu, wie Sie Ihre Datenspeicherkosten schätzen, messen und verwalten können, finden Sie unter [Schätzung der Azure Monitor-Kosten](azure-monitor-costs.md).
- Wenn ein Problem auftritt, finden Sie weitere Informationen im [Leitfaden zur Problembehebung](troubleshoot-azure-monitor.md).
- Informationen zu den Neuerungen der einzelnen Versionsupdates finden Sie unter [Neuerungen in Azure Monitor für Azure Virtual Desktop](whats-new-azure-monitor.md).