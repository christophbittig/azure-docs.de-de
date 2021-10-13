---
title: Einrichten der agentbasierten Abhängigkeitsanalyse in Azure Migrate
description: In diesem Artikel wird das Einrichten der agentbasierten Abhängigkeitsanalyse in Azure Migrate beschrieben.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 11/25/2020
ms.openlocfilehash: 1ad8f9496bd781d6ed33927b4056073a50e0b5a2
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/05/2021
ms.locfileid: "129535064"
---
# <a name="set-up-dependency-visualization"></a>Einrichten der Abhängigkeitsvisualisierung

In diesem Artikel wird das Einrichten der agentbasierten Abhängigkeitsanalyse in der Azure Migrate-Ermittlung und -Bewertung beschrieben. Mit der [Abhängigkeitsanalyse](concepts-dependency-visualization.md) können Sie Abhängigkeiten zwischen Servern, die Sie bewerten und zu Azure migrieren möchten, besser identifizieren und verstehen.

## <a name="before-you-start"></a>Vorbereitung

- Überprüfen Sie die Unterstützungs- und Bereitstellungsanforderungen für die Agent-basierte Abhängigkeitsanalyse für:
    - [Server in VMware-Umgebung](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agent-based)
    - [Physische Server](migrate-support-matrix-physical.md#agent-based-dependency-analysis-requirements)
    - [Server in Hyper-V-Umgebung](migrate-support-matrix-hyper-v.md#agent-based-dependency-analysis-requirements)
- Stellen Sie sicher, dass Sie:
    - Sie verfügen über ein Azure Migrate-Projekt. Wenn dies nicht der Fall ist, [erstellen](./create-manage-projects.md) Sie jetzt ein entsprechendes Projekt.
    - Vergewissern Sie sich, dass Sie das Azure Migrate-Tool zur Ermittlung und Bewertung zum Projekt [hinzugefügt](how-to-assess.md) haben.
    - Richten Sie eine [Azure Migrate-Appliance](migrate-appliance.md) ein, um lokale Server zu ermitteln. Die Appliance ermittelt lokale Server und sendet Metadaten und Leistungsdaten an die Azure Migrate-Ermittlung und -Bewertung. Richten Sie eine Appliance für Folgendes ein:
        - [Server in VMware-Umgebung](how-to-set-up-appliance-vmware.md)
        - [Server in Hyper-V-Umgebung](how-to-set-up-appliance-hyper-v.md)
        - [Physische Server](how-to-set-up-appliance-physical.md)
- Sie müssen einem Azure Migrate-Projekt einen [Log Analytics-Arbeitsbereich](../azure-monitor/logs/manage-access.md) zuordnen, um die Abhängigkeitsvisualisierung nutzen zu können:
    - Sie können einen Arbeitsbereich erst nach Einrichten der Azure Migrate-Appliance und dem Ermitteln von Servern im Azure Migrate-Projekt anfügen.
    - Stellen Sie sicher, dass Sie über einen Arbeitsbereich in dem Abonnement mit dem Azure Migrate-Projekt verfügen.
    - Der Arbeitsbereich muss sich in einer der Regionen „USA, Osten“, „Asien, Südosten“ oder „Europa, Westen“ befinden. Arbeitsbereiche in anderen Regionen können keinem Projekt zugeordnet werden.
    - Der Arbeitsbereich muss sich in einer Region befinden, in der die [Dienstzuordnung unterstützt wird](../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions).
    - Sie können einem Azure Migrate-Projekt einen neuen oder vorhandenen Log Analytics-Arbeitsbereich zuordnen.
    - Sie fügen den Arbeitsbereich bei der ersten Einrichtung der Visualisierung von Abhängigkeiten für einen Server an. Der Arbeitsbereich für ein Azure Migrate-Projekt kann nach dem Hinzufügen nicht mehr geändert werden.
    - In Log Analytics wird der Arbeitsbereich, der Azure Migrate zugeordnet ist, mit dem Schlüssel des Migrationsprojekts und dem Projektnamen gekennzeichnet.

## <a name="associate-a-workspace"></a>Zuordnen eines Arbeitsbereichs

1. Nachdem Sie die Server für die Bewertung ermittelt haben, klicken Sie unter **Server** > **Azure Migrate: Ermittlung und Bewertung** auf **Übersicht**.  
2. Klicken Sie unter **Azure Migrate: Ermittlung und Bewertung** auf **Zusammenfassung**.
3. Klicken Sie im **OMS-Arbeitsbereich** auf **Konfiguration erforderlich**.

     ![Konfigurieren des Log Analytics-Arbeitsbereichs](./media/how-to-create-group-machine-dependencies/oms-workspace-select.png)   

4. Geben Sie unter **OMS-Arbeitsbereich konfigurieren** an, ob Sie einen neuen Arbeitsbereich erstellen oder einen vorhandenen verwenden möchten.
    - Sie können aus allen Arbeitsbereichen im Projektabonnement einen vorhandenen Arbeitsbereich auswählen.
    - Sie benötigen Lesezugriff auf den Arbeitsbereich, um ihn zuordnen zu können.
5. Wenn Sie einen neuen Arbeitsbereich erstellen, wählen Sie einen Speicherort für ihn aus.

    ![Hinzufügen eines neuen Arbeitsbereichs](./media/how-to-create-group-machine-dependencies/workspace.png)

> [!Note]
> [Erfahren Sie](../azure-monitor/logs/private-link-security.md), wie Sie den OMS-Arbeitsbereich für Verbindungen über private Endpunkt konfigurieren.  

## <a name="download-and-install-the-vm-agents"></a>Herunterladen und Installieren der VM-Agents

Installieren Sie die Agents auf jedem Server, den Sie analysieren möchten.

> [!NOTE]
> Bei Servern, die von System Center Operations Manager 2012 R2 oder höher überwacht werden, müssen Sie den MMA-Agent nicht installieren. Die Dienstzuordnung ist in den Operations Manager integriert. [Befolgen Sie](../azure-monitor/vm/service-map-scom.md#prerequisites) die Anleitung zur Integration.

1. Klicken Sie unter **Azure Migrate: Ermittlung und Bewertung** auf **Ermittelte Server**.
1. Klicken Sie auf **Spalten**, um **Abhängigkeiten (Agent-basiert)** auszuwählen, um die Spalte auf der Seite „Ermittelte Server“ anzuzeigen.

    :::image type="content" source="./media/how-to-create-group-machine-dependencies/columns-inline.png" alt-text="Screenshot, der das Ergebnis nach dem Klicken auf Spalten anzeigt." lightbox="./media/how-to-create-group-machine-dependencies/columns-expanded.png":::

1. Klicken Sie für jeden Server, den Sie mit der Abhängigkeitsvisualisierung analysieren möchten, in der Spalte **Abhängigkeiten** auf **Agent-Installation erforderlich**.
1. Laden Sie auf der Seite **Abhängigkeiten** MMA und Dependency-Agent für Windows oder Linux herunter.
1. Kopieren Sie unter „**MMA-Agent konfigurieren“** die Arbeitsbereichs-ID und den Schlüssel. Sie benötigen diese Angaben für die Installation des MMA-Agents.

    ![Installieren der Agents](./media/how-to-create-group-machine-dependencies/dependencies-install.png)


## <a name="install-the-mma"></a>Installieren des MMA

Installieren Sie den MMA auf jedem Windows- oder Linux-Server, den Sie analysieren möchten.

### <a name="install-mma-on-a-windows-server"></a>Installieren des MMA auf einem Windows-Server

Gehen Sie wie folgt vor, um den Agent auf einem Windows-Server zu installieren:

1. Doppelklicken Sie auf den heruntergeladenen Agent.
2. Klicken Sie auf der Seite **Willkommen** auf **Weiter**. Klicken Sie auf der Seite **Lizenzbedingungen** auf **Ich stimme zu**, um die Lizenzbedingungen zu akzeptieren.
3. Behalten Sie unter **Zielordner** den Standardinstallationsordner bei, oder ändern Sie ihn, und klicken Sie anschließend auf **Weiter**.
4. Wählen Sie unter **Agent-Setupoptionen** die Optionen **Azure Log Analytics** > **Weiter**.
5. Klicken Sie auf **Hinzufügen**, um einen neuen Log Analytics-Arbeitsbereich hinzuzufügen. Fügen Sie die Arbeitsbereichs-ID und den dazugehörigen Schlüssel ein, die bzw. den Sie im Portal kopiert haben. Klicken Sie auf **Weiter**.

Sie können den Agent über die Befehlszeile oder mithilfe einer automatisierten Methode wie Configuration Manager oder Intigua installieren.
- [Weitere Informationen](../azure-monitor/agents/log-analytics-agent.md#installation-options) zur Verwendung dieser Methoden zum Installieren des MMA-Agent.
- Der MMA-Agent kann auch mit diesem [Skript](https://github.com/brianbar-MSFT/Install-MMA) installiert werden.
- [Erfahren Sie mehr](../azure-monitor/agents/agents-overview.md#supported-operating-systems) über die durch den MMA unterstützten Windows-Betriebssysteme.

### <a name="install-mma-on-a-linux-server"></a>Installieren des MMA auf einem Linux-Server

So installieren Sie den MMA auf einem Linux-Server:

1. Übertragen Sie das entsprechende Paket (x86 oder x64) mithilfe von scp/sftp auf Ihren Linux-Computer.

2. Installieren Sie das Bundle mit dem Argument „--install“.

   `sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>`

[Erfahren Sie mehr](../azure-monitor/agents/agents-overview.md#supported-operating-systems) über die Liste der Unterstützungen durch den MMA für Linux-Betriebssysteme. 

## <a name="install-the-dependency-agent"></a>Installieren des Abhängigkeits-Agents

1. Doppelklicken Sie zum Installieren des Abhängigkeits-Agents auf einem Windows-Server auf die Setupdatei, und befolgen Sie die Schritte im Assistenten.

2. Installieren Sie den Abhängigkeits-Agent auf einem Linux-Server als „root“, indem Sie den folgenden Befehl verwenden:

   `sh InstallDependencyAgent-Linux64.bin`

- [Erfahren Sie mehr](../azure-monitor/vm/vminsights-enable-hybrid.md#dependency-agent) darüber, wie Sie den Dependency-Agent mithilfe von Skripts installieren können.
- [Erfahren Sie mehr](../azure-monitor/vm/vminsights-enable-overview.md#supported-operating-systems) über die vom Abhängigkeits-Agent unterstützten Betriebssysteme.


## <a name="create-a-group-using-dependency-visualization"></a>Erstellen von Gruppen mit Abhängigkeitsvisualisierung

Erstellen Sie nun eine Gruppe für die Bewertung. 


> [!NOTE]
> Gruppen, für die Sie Abhängigkeiten visualisieren möchten, sollten höchstens zehn Server enthalten. Wenn Sie über mehr als zehn Computer verfügen, teilen Sie sie in kleinere Gruppen auf.

1. Klicken Sie unter **Azure Migrate: Ermittlung und Bewertung** auf **Ermittelte Server**.
2. Klicken Sie in der Spalte **Abhängigkeiten** auf **Abhängigkeiten anzeigen** für jeden Server, den Sie überprüfen möchten.
3. Auf dem Abhängigkeitsdiagramm sehen Sie Folgendes:
    - Eingehende (Clients) und ausgehende (Server) TCP-Verbindungen vom und zum Server.
    - Abhängige Server, auf denen die Abhängigkeits-Agents nicht installiert sind, werden nach Portnummern gruppiert.
    - Abhängige Server mit installierten Abhängigkeits-Agents werden als separate Felder angezeigt.
    - Prozesse, die auf dem Server ausgeführt werden. Sie können die einzelnen Serverfelder erweitern, um die Prozesse anzuzeigen.
    - Servereigenschaften (einschließlich FQDN, Betriebssystem, MAC-Adresse). Klicken Sie auf das jeweilige Serverfeld, um die Details anzuzeigen.

4. Sie können Abhängigkeiten für verschiedene Zeiträume anzeigen, indem Sie auf im Zeitbereich auf die Zeitdauer klicken.
    - Standardmäßig ist ein Bereich von einer Stunde ausgewählt. 
    - Sie können den Zeitraum ändern oder das Start- und Enddatum und die Dauer angeben.
    - Der Zeitbereich kann maximal eine Stunde lang sein. Falls Sie einen längeren Bereich benötigen, können Sie Azure Monitor nutzen, um abhängige Daten für einen längeren Zeitraum abzufragen.

5. Nachdem Sie die abhängigen Server identifiziert haben, die Sie gruppieren möchten, können Sie die gewünschten Server mit Strg+Klick auswählen und auf **Computer gruppieren** klicken.
6. Geben Sie einen Gruppennamen an.
7. Überprüfen Sie, ob die abhängigen Server von Azure Migrate ermittelt werden.

    - Wenn ein abhängiger Server nicht von der Azure Migrate-Ermittlung und -Bewertung ermittelt wird, können Sie ihn der Gruppe nicht hinzufügen.
    - Um einen Server hinzuzufügen, führen Sie die Ermittlung erneut aus. Überprüfen Sie, ob der Server ermittelt wird.

8. Wenn Sie eine Bewertung für diese Gruppe erstellen möchten, aktivieren Sie das Kontrollkästchen, um eine neue Bewertung für die Gruppe zu erstellen.
8. Klicken Sie auf **OK**, um die Gruppe zu speichern.

Nachdem Sie die Gruppe erstellt haben, empfehlen wir Ihnen, Agents auf allen Servern der Gruppe zu installieren und dann Abhängigkeiten für die gesamte Gruppe zu visualisieren.

## <a name="query-dependency-data-in-azure-monitor"></a>Abfragen von Abhängigkeitsdaten in Azure Monitor

Sie können von der Dienstzuordnung erfasste Abhängigkeitsdaten in dem Log Analytics-Arbeitsbereich abfragen, der dem Azure Migrate-Projekt zugeordnet ist. Log Analytics wird zum Schreiben und Ausführen von Azure Monitor-Protokollabfragen verwendet.

- [Erfahren Sie](../azure-monitor/vm/service-map.md#log-analytics-records), wie Sie in Log Analytics nach Dienstzuordnungsdaten suchen.
- [Verschaffen Sie sich einen Überblick](../azure-monitor/logs/get-started-queries.md) über das Schreiben von Protokollabfragen in [Log Analytics](../azure-monitor/logs/log-analytics-tutorial.md).

Führen Sie eine Abfrage nach Abhängigkeitsdaten wie folgt durch:

1. Navigieren Sie nach der Installation des Agents zum Portal, und klicken Sie auf **Übersicht**.
2. Klicken Sie unter **Azure Migrate: Ermittlung und Bewertung** auf **Übersicht**. Klicken Sie auf den Abwärtspfeil, um **Essentials** zu erweitern.
3. Klicken Sie im **OMS-Arbeitsbereich** auf den Namen des Arbeitsbereichs.
3. Klicken Sie auf der Seite Log Analytics-Arbeitsbereich > **„Allgemein“** auf **„Protokolle“** .
4. Schreiben Sie Ihre Anfrage und klicken Sie auf **„Ausführen“** .

### <a name="sample-queries"></a>Beispielabfragen

Hier finden Sie einige Beispielabfragen, mit denen Sie Abhängigkeitsdaten extrahieren können.

- Sie können die Abfragen ändern, um Ihre bevorzugten Datenpunkte zu extrahieren.
- [Überprüfen](../azure-monitor/vm/service-map.md#log-analytics-records) Sie eine vollständige Liste der Datensätze der Abhängigkeiten.
- [Überprüfen](../azure-monitor/vm/service-map.md#sample-log-searches) Sie zusätzliche Beispielabfragen.

#### <a name="sample-review-inbound-connections"></a>Beispiel: Überprüfen eingehender Verbindungen

Überprüfen Sie eingehende Verbindungen für eine Gruppe von Servern.

- Die Datensätze in der Tabelle für Verbindungsmetriken (VMConnection) stellen keine einzelnen physischen Netzwerkverbindungen dar.
- Mehrere physische Netzwerkverbindungen werden in einer logischen Verbindung gruppiert.
- [Erfahren Sie mehr darüber](../azure-monitor/vm/service-map.md#connections), wie Daten von physischen Netzwerkverbindungen in VMConnection aggregiert werden.

```
// the servers of interest
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
let StartDateTime = datetime(2019-03-25T00:00:00Z);
let EndDateTime = datetime(2019-03-30T01:00:00Z);
VMConnection
| where Direction == 'inbound'
| where TimeGenerated > StartDateTime and TimeGenerated  < EndDateTime
| join kind=inner (ips) on $left.DestinationIp == $right.ips
| summarize sum(LinksEstablished) by Computer, Direction, SourceIp, DestinationIp, DestinationPort
```

#### <a name="sample-summarize-sent-and-received-data"></a>Beispiel: Zusammenfassen von gesendeten und empfangenen Daten

Dieses Beispiel zeigt eine Zusammenfassung des gesendeten und empfangenen Datenvolumens in eingehenden Verbindungen zwischen einer Gruppe von Servern an.

```
// the servers of interest
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
let StartDateTime = datetime(2019-03-25T00:00:00Z);
let EndDateTime = datetime(2019-03-30T01:00:00Z);
VMConnection
| where Direction == 'inbound'
| where TimeGenerated > StartDateTime and TimeGenerated  < EndDateTime
| join kind=inner (ips) on $left.DestinationIp == $right.ips
| summarize sum(BytesSent), sum(BytesReceived) by Computer, Direction, SourceIp, DestinationIp, DestinationPort
```

## <a name="next-steps"></a>Nächste Schritte

[Erstellen einer Bewertung](how-to-create-assessment.md) für eine Gruppe.
