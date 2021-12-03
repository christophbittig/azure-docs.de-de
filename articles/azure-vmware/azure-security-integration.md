---
title: Integrieren von Microsoft Defender für Cloud in Azure VMware Solution
description: Erfahren Sie, wie Sie Ihre Azure VMware Solution-VMs mit den nativen Sicherheitstools von Azure über das Dashboard für den Workloadschutz schützen.
ms.topic: how-to
ms.date: 06/14/2021
ms.openlocfilehash: f1ec6d6282a773cca3164f377f7efd8bdaa42c77
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132312686"
---
# <a name="integrate-microsoft-defender-for-cloud-with-azure-vmware-solution"></a>Integrieren von Microsoft Defender für Cloud in Azure VMware Solution 

Microsoft Defender für Cloud bietet erweiterten Bedrohungsschutz für Azure VMware Solution- und lokale VMs. Die Lösung bewertet das Sicherheitsrisiko von Azure VMware Solution-VMs und gibt bei Bedarf Warnungen aus. Diese Sicherheitswarnungen können zur Behebung an Azure Monitor weitergeleitet werden. Sie können Sicherheitsrichtlinien in Microsoft Defender für Cloud definieren. Weitere Informationen finden Sie unter [Arbeiten mit Sicherheitswarnungen](../security-center/tutorial-security-policy.md). 

Microsoft Defender für Cloud bietet zahlreiche Funktionen, darunter:
- Überwachung der Dateiintegrität
- Erkennung dateiloser Angriffe
- Bewertung von Patches für Betriebssysteme 
- Bewertung von Sicherheitsfehlkonfigurationen
- Bewertung von Endpoint Protection

Das Diagramm zeigt die integrierte Überwachungsarchitektur der integrierten Sicherheit für Azure VMware Solution-VMs.
 
:::image type="content" source="media/azure-security-integration/azure-integrated-security-architecture.png" alt-text="Diagramm: Architektur der integrierten Azure-Sicherheit" border="false":::

Der **Log Analytics-Agent** erfasst Protokolldaten aus Azure, Azure VMware Solution und lokalen VMs. Die Protokolldaten werden an Azure Monitor-Protokolle gesendet und in einem **Log Analytics-Arbeitsbereich** gespeichert. Jeder Arbeitsbereich verfügt über ein eigenes Datenrepository und eine eigene Konfiguration zum Speichern von Daten.  Nachdem die Protokolle erfasst wurden, bewertet **Microsoft Defender für Cloud** den Sicherheitsrisikostatus von Azure VMware Solution-VMs und löst bei jedem kritischen Sicherheitsrisiko eine Warnung aus. Nach der Bewertung wird der Sicherheitsrisikostatus von Microsoft Defender für Cloud an Microsoft Sentinel weitergeleitet, um einen Incident zu erstellen und eine Zuordnung zu anderen Bedrohungen vorzunehmen.  Microsoft Defender für Cloud ist über den Microsoft Defender für Cloud-Connector mit Microsoft Sentinel verbunden. 

## <a name="prerequisites"></a>Voraussetzungen

- [Planen der optimierten Verwendung von Defender für Cloud](../security-center/security-center-planning-and-operations-guide.md).

- [Überprüfen der unterstützten Plattformen in Defender für Cloud](../security-center/security-center-os-coverage.md).

- [Erstellen Sie einen Log Analytics-Arbeitsbereich](../azure-monitor/logs/quick-create-workspace.md), um Daten aus verschiedenen Quellen zu sammeln.

- [Aktivieren Sie Microsoft Defender für Cloud in Ihrem Abonnement](../security-center/security-center-get-started.md). 

   >[!NOTE]
   >Microsoft Defender für Cloud ist ein vorkonfiguriertes Tool, das keine Bereitstellung erfordert, aber aktiviert werden muss.

- [Aktivieren von Microsoft Defender für Cloud](../security-center/enable-azure-defender.md). 


## <a name="add-azure-vmware-solution-vms-to-defender-for-cloud"></a>Hinzufügen von Azure VMware Solution-VMs zu Defender für Cloud

1. Suchen Sie im Azure-Portal nach **Azure Arc**, und wählen Sie den Eintrag aus.

2. Klicken Sie unter „Ressourcen“ auf **Server** und dann auf **+Hinzufügen**.

   :::image type="content" source="media/azure-security-integration/add-server-to-azure-arc.png" alt-text="Screenshot: Azure Arc-Seite „Server“ zum Hinzufügen einer Azure VMware Solution-VM zu Azure":::

3. Wählen Sie **Skript generieren** aus.
 
   :::image type="content" source="media/azure-security-integration/add-server-using-script.png" alt-text="Screenshot: Azure Arc-Seite mit der Option zum Hinzufügen eines Servers über ein interaktives Skript"::: 
 
4. Klicken Sie auf der Registerkarte **Voraussetzungen** auf **Weiter**.

5. Geben Sie auf der Registerkarte **Ressourcendetails** die folgenden Details ein, und wählen Sie dann **Weiter: Tags** aus. 

    - Subscription

    - Resource group

    - Region 

    - Betriebssystem

    - Details zum Proxyserver
    
6. Klicken Sie auf der Registerkarte **Tags** auf **Weiter**.

7. Klicken Sie auf der Registerkarte **Skript herunterladen und ausführen** auf **Herunterladen**.

8. Geben Sie Ihr Betriebssystem an, und führen Sie das Skript auf Ihrer Azure VMware Solution-VM aus.

## <a name="view-recommendations-and-passed-assessments"></a>Anzeigen von Empfehlungen und bestandenen Bewertungen

Empfehlungen und Bewertungen bieten Ihnen Details zur Sicherheitsintegrität Ihrer Ressource. 

1. Wählen Sie in Microsoft Defender für Cloud im linken Bereich **Inventar** aus.

2. Wählen Sie als Ressourcentyp **Server – Azure Arc** aus.
 
   :::image type="content" source="media/azure-security-integration/select-resource-in-security-center.png" alt-text="Screenshot: Seite „Inventar“ von Microsoft Defender für Cloud mit der unter „Ressourcentyp“ ausgewählten Option „Server – Azure Arc“.":::

3. Wählen Sie den Namen Ihrer Ressource aus. Daraufhin wird eine Seite geöffnet, auf die Details zur Sicherheitsintegrität Ihrer Ressource angezeigt werden.

4. Wählen Sie unter **Empfehlungsliste** die Registerkarten **Empfehlungen**, **Bestandene Bewertungen** und **Nicht verfügbare Bewertungen** aus, um die jeweiligen Details anzuzeigen.

   :::image type="content" source="media/azure-security-integration/view-recommendations-assessments.png" alt-text="Screenshot: Sicherheitsempfehlungen und -bewertungen von Microsoft Defender für Cloud.":::

## <a name="deploy-a-microsoft-sentinel-workspace"></a>Bereitstellen eines Microsoft Sentinel-Arbeitsbereichs

Microsoft Sentinel bietet Sicherheitsanalysen, Warnungserkennung und automatisierte Reaktionen auf Bedrohungen innerhalb einer Umgebung. Es handelt sich um eine cloudnative SIEM-Lösung (Security Information & Event Management), der ein Log Analytics-Arbeitsbereich zugrunde liegt.

Da Microsoft Sentinel auf einem Log Analytics-Arbeitsbereich basiert, müssen Sie lediglich den Arbeitsbereich auswählen, den Sie verwenden möchten.

1. Suchen Sie im Azure-Portal nach **Microsoft Sentinel**, und wählen Sie den Eintrag aus.

2. Klicken Sie auf der Seite mit den Microsoft Sentinel-Arbeitsbereichen auf **+Hinzufügen**.

3. Wählen Sie den Log Analytics-Arbeitsbereich aus, und klicken Sie auf **Hinzufügen**.

## <a name="enable-data-collector-for-security-events"></a>Aktivieren eines Datensammlers für sicherheitsrelevante Ereignisse

1. Wählen Sie auf der Seite der Microsoft Sentinel-Arbeitsbereiche den konfigurierten Arbeitsbereich aus.

2. Wählen Sie unter „Konfiguration“ die Option **Datenconnectors** aus.

3. Wählen Sie in der Spalte „Connectorname“ den Eintrag **Sicherheitsereignisse** aus, und klicken Sie dann auf **Connectorseite öffnen**.

4. Wählen Sie auf der Connectorseite die Ereignisse aus, die Sie streamen möchten, und klicken Sie dann auf **Änderungen übernehmen**.

   :::image type="content" source="media/azure-security-integration/select-events-you-want-to-stream.png" alt-text="Screenshot: Seite „Sicherheitsereignisse“ in Microsoft Sentinel, auf der Sie die zu streamenden Ereignisse auswählen können.":::




## <a name="connect-microsoft-sentinel-with-microsoft-defender-for-cloud"></a>Verbinden Microsoft Sentinel mit Microsoft Defender für Cloud  

1. Wählen Sie auf der Seite „Microsoft Sentinel-Arbeitsbereich“ den konfigurierten Arbeitsbereich aus.

2. Wählen Sie unter „Konfiguration“ die Option **Datenconnectors** aus.

3. Wählen Sie **Microsoft Defender für Cloud** aus der Liste aus, und wählen Sie dann **Connectorseite öffnen** aus.

   :::image type="content" source="media/azure-security-integration/connect-security-center-with-azure-sentinel.png" alt-text="Screenshot: Seite „Datenconnectors“ in Microsoft Sentinel mit der Auswahl zum Verbinden von Microsoft Defender für Cloud mit Microsoft Sentinel.":::

4. Wählen Sie **Verbinden** aus, um Microsoft Defender für Cloud mit Microsoft Sentinel zu verbinden.

5. Aktivieren Sie **Incident erstellen**, um einen Incident für Microsoft Defender für Cloud zu generieren.

## <a name="create-rules-to-identify-security-threats"></a>Erstellen von Regeln zum Identifizieren von Sicherheitsbedrohungen

Nachdem Sie Datenquellen mit Microsoft Sentinel verbunden haben, können Sie Regeln erstellen, um Warnungen für erkannte Bedrohungen zu generieren. Im folgenden Beispiel wird eine Regel für Versuche erstellt, sich mit einem falschen Kennwort bei einem Windows-Server anzumelden.

1. Wählen Sie auf der Übersichtsseite von Microsoft Sentinel unter „Konfigurationen“ die Option **Analysen** aus.

2. Wählen Sie unter „Konfigurationen“ die Option **Analyse** aus.

3. Klicken Sie auf **+Erstellen**, und wählen Sie in der Dropdownliste den Eintrag **Geplante Abfrageregel** aus.

4. Geben Sie auf der Registerkarte **Allgemein** die erforderlichen Informationen ein, und wählen Sie dann **Weiter: Regellogik festlegen** aus.

    - Name

    - BESCHREIBUNG

    - Taktik

    - Schweregrad

    - Status

5. Geben Sie auf der Registerkarte **Regellogik festlegen** die erforderlichen Informationen ein, und klicken Sie dann auf **Weiter**.

    - Regelabfrage (gezeigt wird die Beispielabfrage)
    
        ```
        SecurityEvent
        |where Activity startswith '4625'
        |summarize count () by IpAddress,Computer
        |where count_ > 3
        ```
        
    - Entitäten zuordnen

    - Abfrageplanung

    - Benachrichtigungsschwellenwert

    - Ereignisgruppierung

    - Unterdrücken


6. Aktivieren Sie auf der Registerkarte **Incidenteinstellungen** die Option **Incidents aus Warnungen erstellen, die von dieser Analyseregel ausgelöst werden**, und wählen Sie **Weiter: Automatisierte Antwort** aus.
 
    :::image type="content" source="../sentinel/media/tutorial-detect-threats-custom/general-tab.png" alt-text="Screenshot: Analyseregel-Assistent zum Erstellen einer neuen Regel in Microsoft Sentinel.":::

7. Klicken Sie auf **Weiter: Review** (Weiter: Überprüfen).

8. Überprüfen Sie die Informationen auf der Registerkarte **Überprüfen + erstellen**, und klicken Sie auf **Erstellen**.

>[!TIP]
>Nach dem dritten fehlerhaften Anmeldeversuch beim Windows-Server löst die erstellte Regel einen Incident für jeden erfolglosen Versuch aus.

## <a name="view-alerts"></a>Anzeigen von Warnungen

Sie können generierte Incidents mit Microsoft Sentinel anzeigen. Sie können auch Incidents zuweisen und nach der Auflösung schließen – alles innerhalb von Microsoft Sentinel.

1. Navigieren Sie zur Übersichtsseite von Microsoft Sentinel.

2. Wählen Sie unter „Bedrohungsmanagement“ die Option **Incidents** aus.

3. Wählen Sie einen Incident aus, und weisen Sie ihn dann einem Team zur Auflösung zu.

    :::image type="content" source="media/azure-security-integration/assign-incident.png" alt-text="Screenshot: Seite „Incidents“ von Microsoft Sentinel mit ausgewähltem Incident und der Option zum Zuweisen des Incidents für die Auflösung.":::

>[!TIP]
>Nachdem das Problem behoben wurde, können Sie den Incident schließen.

## <a name="hunt-security-threats-with-queries"></a>Suchen nach Sicherheitsbedrohungen mithilfe von Abfragen

Sie können Abfragen erstellen oder die verfügbaren vordefinierten Abfragen in Microsoft Sentinel verwenden, um Bedrohungen in Ihrer Umgebung zu identifizieren. In den folgenden Schritten wird eine vordefinierte Abfrage ausgeführt.

1. Wählen Sie auf der Übersichtsseite von Microsoft Sentinel unter „Bedrohungsverwaltung“ die Option **Hunting** aus. Eine Liste vordefinierter Abfragen wird angezeigt.

   >[!TIP]
   >Sie können auch eine neue Abfrage erstellen, indem Sie **Neue Abfrage** auswählen. 
   >
   >:::image type="content" source="../sentinel/media/hunting/save-query.png" alt-text="Screenshot Seite „Hunting“ von Microsoft Sentinel mit hervorgehobener Option „+Neue Abfrage“.":::

3. Wählen Sie eine Abfrage aus, und klicken Sie dann auf **Abfrage ausführen**.

4. Klicken Sie auf **Ergebnisse anzeigen**, um die Ergebnisse zu überprüfen.



## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie sich mit dem Schützen Ihrer Azure VMware Solution-VMs vertraut gemacht haben, informieren Sie sich über die folgenden Themen:

- [Verwenden des Dashboards für den Workloadschutz](../security-center/azure-defender-dashboard.md)
- [Erweiterte Erkennung von mehrstufigen Angriffen in Microsoft Sentinel](../azure-monitor/logs/quick-create-workspace.md)
- [Integrieren von nativen Azure-Diensten in Azure VMware Solution](integrate-azure-native-services.md)
