---
title: Konfigurieren des Verbindungsmonitors für Azure ExpressRoute
description: Konfigurieren Sie die cloudbasierte Überwachung der Netzwerkkonnektivität für Azure ExpressRoute-Leitungen. Dies umfasst die Überwachung über das private Peering mit ExpressRoute und das Microsoft-Peering.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 07/28/2021
ms.author: duau
ms.openlocfilehash: 794e841d8d50683374c86301ec55bc4f6ab53ec6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122346975"
---
# <a name="configure-connection-monitor-for-expressroute"></a>Konfigurieren des Verbindungsmonitors für ExpressRoute

In diesem Artikel konfigurieren Sie eine Verbindungsmonitor-Erweiterung zum Überwachen von ExpressRoute. Der Verbindungsmonitor ist eine cloudbasierte Lösung für die Netzwerküberwachung, die die Konnektivität zwischen Azure-Cloudbereitstellungen und lokalen Standorten (Zweigstellen usw.) überwacht. Der Verbindungsmonitor ist Teil der Azure Monitor-Protokolle.  Mit der Erweiterung können Sie auch die Netzwerkkonnektivität für Ihre privaten und Microsoft-Peeringverbindungen überwachen. Wenn Sie den Verbindungsmonitor für ExpressRoute konfigurieren, können Sie Netzwerkprobleme erkennen, die identifiziert und entfernt werden sollen.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Mit dem Verbindungsmonitor für ExpressRoute haben Sie folgende Möglichkeiten:

* Überwachen der Verluste und der Wartezeit über verschiedene VNets sowie das Einrichten von Warnungen

* Überwachen aller Pfade (einschließlich redundanter Pfade) des Netzwerks.

* Behandeln von vorübergehenden Netzwerkproblemen und Point-in-Time-Netzwerkproblemen, die schwer reproduzierbar sind

* Ermitteln eines bestimmten Segments des Netzwerks, das für die beeinträchtigte Leistung verantwortlich ist

## <a name="workflow"></a><a name="workflow"></a>-Workflow

Überwachungs-Agents werden auf mehreren Servern (lokal und in Azure) installiert. Die Agents kommunizieren miteinander, indem sie TCP-Handshakepakete senden. Die Kommunikation zwischen den Agents ermöglicht es Azure, die Netzwerktopologie und den Pfad zuzuordnen, die der Datenverkehr nutzen kann.

1. Erstellen Sie einen Log Analytics-Arbeitsbereich.

1. Installieren und konfigurieren Sie Software-Agents. (Sie müssen keine Software-Agents installieren und konfigurieren, wenn die Überwachung nur über Microsoft-Peering erfolgen soll.)

    * Installieren Sie Überwachungs-Agents auf den lokalen Servern und den virtuellen Azure-Computern (für das private Peering).
    * Konfigurieren Sie die Einstellungen der Server der Überwachungs-Agents, um diesen die Kommunikation zu ermöglichen. (Öffnen Sie die Firewallports usw.)

1. Konfigurieren Sie Regeln für die Netzwerksicherheitsgruppen, um den auf virtuellen Azure-Computern installierten Überwachungs-Agents die Kommunikation mit den lokalen Überwachungs-Agents zu ermöglichen.

1. Aktivieren Sie Network Watcher für Ihr Abonnement.

1. Einrichten der Überwachung: Erstellen Sie Verbindungsmonitore mit Testgruppen, um Quell- und Zielendpunkte in Ihrem Netzwerk zu überwachen.

Wenn Sie bereits den Netzwerkleistungsmonitor (veraltet) oder Verbindungsmonitor verwenden, um andere Objekte oder Dienste zu überwachen, und Sie bereits über einen Log Analytics-Arbeitsbereich in einer der unterstützten Regionen verfügen. Sie können Schritt 1 und Schritt 2 überspringen und mit der Konfiguration in Schritt 3 beginnen.

## <a name="create-a-workspace"></a><a name="configure"></a> Erstellen eines Arbeitsbereichs

Erstellen Sie einen Arbeitsbereich im Abonnement mit den VNETs, die mit den ExpressRoute-Leitungen verknüpft sind.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Wählen Sie in dem Abonnement, das die virtuellen Netzwerke enthält, die mit Ihrer ExpressRoute-Leitung verbunden sind, die Option **+ Ressource erstellen** aus. Suchen Sie nach *Log Analytics-Arbeitsbereich*, und wählen Sie dann **Erstellen** aus.

   >[!NOTE]
   >Sie können einen neuen Arbeitsbereich erstellen oder einen vorhandenen Arbeitsbereich verwenden. Wenn Sie einen vorhandenen Arbeitsbereich verwenden möchten, müssen Sie sicherstellen, dass der Arbeitsbereich zur neuen Abfragesprache migriert wurde. [Weitere Informationen](../azure-monitor/logs/log-query-overview.md)
   >

    :::image type="content" source="./media/how-to-configure-connection-monitor/search-log-analytics.png" alt-text="Screenshot: Suche nach Log Analytics beim Erstellen einer Ressource":::

1. Erstellen Sie einen Arbeitsbereich, indem Sie die folgenden Informationen eingeben oder auswählen.  

    | Einstellungen | Wert |
    | -------- | ----- |
    | Subscription | Wählen Sie das Abonnement mit der ExpressRoute-Leitung aus. |
    | Ressourcengruppe | Erstellen Sie eine neue Ressourcengruppe, oder wählen Sie eine bestehende Ressourcengruppe aus. |
    | Name | Geben Sie einen Namen ein, um diesen Arbeitsbereich zu identifizieren. |
    | Region | Wählen Sie eine Region aus, in der dieser Arbeitsbereich erstellt wird. |

    :::image type="content" source="./media/how-to-configure-connection-monitor/create-workspace-basic.png" alt-text="Screenshot: Registerkarte zum Erstellen eines Log Analytics-Arbeitsbereichs":::

     >[!NOTE]
     >Die ExpressRoute-Verbindung kann sich in einer beliebigen Region in der Welt befinden. Sie muss sich nicht in der gleichen Region befinden wie der Arbeitsbereich.
     >

1. Wählen Sie zum Überprüfen entsprechend die Option **Überprüfen + erstellen** und anschließend **Erstellen** aus, um den Arbeitsbereich bereitzustellen. Nachdem der Arbeitsbereich bereitgestellt wurde, fahren Sie mit dem nächsten Abschnitt fort, um die Überwachungslösung zu konfigurieren.

## <a name="configure-monitoring-solution"></a><a name="npm"></a>Konfigurieren der Überwachungslösung

Vervollständigen Sie das folgende Azure PowerShell-Skript, indem Sie die Werte für *$SubscriptionId*, *$location*, *$resourceGroup* und *$workspaceName* ersetzen. Führen Sie dann das Skript aus, um die Überwachungslösung zu konfigurieren.

```azurepowershell-interactive
$subscriptionId = "Subscription ID should come here"
Select-AzSubscription -SubscriptionId $subscriptionId

$location = "Workspace location should come here"
$resourceGroup = "Resource group name should come here"
$workspaceName = "Workspace name should come here"

$solution = @{
    Location          = $location
    Properties        = @{
        workspaceResourceId = "/subscriptions/$($subscriptionId)/resourcegroups/$($resourceGroup)/providers/Microsoft.OperationalInsights/workspaces/$($workspaceName)"
    }
    Plan              = @{
        Name          = "NetworkMonitoring($($workspaceName))" 
        Publisher     = "Microsoft"
        Product       = "OMSGallery/NetworkMonitoring"
        PromotionCode = ""
    }
    ResourceName      = "NetworkMonitoring($($workspaceName))" 
    ResourceType      = "Microsoft.OperationsManagement/solutions" 
    ResourceGroupName = $resourceGroup
}

New-AzureRmResource @solution -Force
```

Nachdem Sie die Überwachungslösung konfiguriert haben: Fahren Sie mit dem nächsten Schritt der Installation und Konfiguration der Überwachungs-Agents auf Ihren Servern fort.

## <a name="install-and-configure-agents-on-premises"></a><a name="agents"></a>Lokales Installieren und Konfigurieren von Agents

### <a name="download-the-agent-setup-file"></a><a name="download"></a>Laden Sie die Setup-Datei für den Agent herunter.

1. Navigieren Sie zum **Log Analytics-Arbeitsbereich**, und wählen Sie unter *Einstellungen* die Option **Agent-Verwaltung** aus. Laden Sie den Agent herunter, der dem Betriebssystem Ihres Computers entspricht.

    :::image type="content" source="./media/how-to-configure-connection-monitor/download-agent.png" alt-text="Screenshot: Seite „Agent-Verwaltung“ im Arbeitsbereich":::

1. Kopieren Sie dann die **Arbeitsbereich-ID** und den **Primärschlüssel**, und fügen Sie diese in Editor ein.

    :::image type="content" source="./media/how-to-configure-connection-monitor/copy-id-key.png" alt-text="Screenshot: Arbeitsbereichs-ID und Primärschlüssel":::

1. Für Windows-Computer laden Sie das PowerShell-Skript [*EnableRules.ps1*](https://aka.ms/npmpowershellscript) herunter und führen es in einem PowerShell-Fenster mit Administratorrechten aus. Das PowerShell-Skript öffnet den relevanten Firewallport für die TCP-Transaktionen. 
 
    Bei Linux-Computern muss die Portnummer mit den folgenden Schritten manuell geändert werden:

    * Navigieren Sie zum Pfad „/var/opt/microsoft/omsagent/npm_state“.
    * Öffnen Sie die Datei „npmdregistry“.
    * Ändern Sie den Wert für die Portnummer `PortNumber:<port of your choice>`.

### <a name="install-log-analytics-agent-on-each-monitoring-server"></a><a name="installagentonprem"></a>Installieren des Log Analytics-Agents auf jedem Überwachungsserver

Aus Redundanzgründen wird empfohlen, den Log Analytics-Agent auf mindestens zwei Servern auf beiden Seiten der ExpressRoute-Leitung zu installieren. Beispiel: Ihr lokales und virtuelles Azure-Netzwerk. Führen Sie die folgenden Schritte aus, um die Agents zu installieren:

1. Wählen Sie unten das entsprechende Betriebssystem aus, um die Schritte zum Installieren des Log Analytics-Agents auf Ihren Servern durchzuführen.

    * [Windows](../azure-monitor/agents/agent-windows.md#install-agent-using-setup-wizard)
    * [Linux](../azure-monitor/agents/agent-linux.md)

1. Nach Abschluss wird der Microsoft Monitoring Agent in der Systemsteuerung angezeigt. Sie können Ihre Konfiguration überprüfen und die [Konnektivität des Agents](../azure-monitor/agents/agent-windows.md#verify-agent-connectivity-to-azure-monitor) mit den Azure Monitor-Protokollen verifizieren.

1. Wiederholen Sie die Schritte 1 und 2 für die anderen lokalen Computer, die Sie für die Überwachung verwenden möchten.

### <a name="install-network-watcher-agent-on-each-monitoring-server"></a><a name="installagentazure"></a>Installieren des Network Watcher-Agents auf jedem Überwachungsserver

#### <a name="new-azure-virtual-machine"></a>Neuer virtueller Azure-Computer

Wenn Sie einen neuen virtuellen Azure-Computer für die Überwachung der Konnektivität Ihres VNets erstellen, können Sie den Network Watcher-Agent beim [Erstellen der VM](../network-watcher/connection-monitor.md#create-the-first-vm) installieren.

#### <a name="existing-azure-virtual-machine"></a>Vorhandener virtueller Azure-Computer

Wenn Sie eine vorhandene VM zur Überwachung der Konnektivität verwenden, können Sie den Netzwerk-Agent separat für [Linux](../virtual-machines/extensions/network-watcher-linux.md) und [Windows](../virtual-machines/extensions/network-watcher-windows.md) installieren.

### <a name="open-the-firewall-ports-on-the-monitoring-agent-servers"></a><a name="firewall"></a>Öffnen der Firewallports auf den Servern des Überwachungs-Agents

Firewallregeln können die Kommunikation zwischen Quell- und Zielserver blockieren. Der Verbindungsmonitor erkennt dieses Problem und zeigt es als Diagnosemeldung in der Topologie an. Damit eine Verbindungsüberwachung möglich ist, müssen Sie zunächst sicherstellen, dass die Firewallregeln eine Paketübertragung über TCP oder ICMP zwischen der Quelle und dem Ziel zulassen.

#### <a name="windows"></a>Windows

Bei Windows-Computern können Sie ein PowerShell-Skript ausführen, um die Registrierungsschlüssel zu erstellen, die für den Verbindungsmonitor erforderlich sind. Das Skript erstellt außerdem Regeln für die Windows-Firewall, damit Agents TCP-Verbindungen miteinander herstellen können. Die vom Skript erstellten Registrierungsschlüssel geben an, ob die Debugprotokolle und der Pfad zur Protokolldatei protokolliert werden sollen. Ferner definieren sie den für die Kommunikation verwendeten Agent-TCP-Port. Die Werte für diese Schlüssel werden vom Skript automatisch festgelegt. Ändern Sie diese Schlüssel nicht manuell.

Port 8084 wird standardmäßig geöffnet. Sie können einen benutzerdefinierten Port verwenden, indem Sie im Skript den Parameter „portNumber“ angeben. In diesem Fall müssen Sie jedoch den gleichen Port für alle Server angeben, auf denen Sie das Skript ausführen.

>[!NOTE]
>Das PowerShell-Skript „EnableRules“ konfiguriert Windows-Firewallregeln nur auf dem Server, auf dem das Skript ausgeführt wird. Bei Verwendung einer Netzwerkfirewall sollten Sie sicherstellen, dass diese den Datenverkehr zum TCP-Port erlaubt, der vom Verbindungsmonitor verwendet wird.
>

Öffnen Sie auf den Agent-Servern ein PowerShell-Fenster mit Administratorrechten. Führen Sie das PowerShell-Skript [EnableRules](https://aka.ms/npmpowershellscript) (das Sie zuvor heruntergeladen haben) aus. Verwenden Sie keine Parameter.

:::image type="content" source="./media/how-to-configure-connection-monitor/enable-rules-script.png" alt-text="Screenshot: Ausführen des Skripts zum Aktivieren von Regeln im PowerShell-Fenster":::

#### <a name="linux"></a>Linux

Auf Linux-Computern müssen die zu verwendenden Portnummern manuell geändert werden:

1. Navigieren Sie zum Pfad „/var/opt/microsoft/omsagent/npm_state“.
1. Öffnen Sie die Datei „npmdregistry“.
1. Ändern Sie den Wert für die Portnummer `PortNumber:\<port of your choice\>`. Die verwendeten Portnummern sollten für alle in einem Arbeitsbereich verwendeten Agents identisch sein.

## <a name="configure-network-security-group-rules"></a><a name="opennsg"></a>Konfigurieren von Regeln für Netzwerksicherheitsgruppen

Um Server in Azure zu überwachen, müssen Sie Regeln für Netzwerksicherheitsgruppen (NSG) konfigurieren, um TCP- oder ICMP-Datenverkehr vom Verbindungsmonitor zuzulassen. Der Standardport ist **8084, über den der auf der Azure-VM installierte Überwachungs-Agent mit einem lokalen Überwachungs-Agent kommunizieren kann.

Weitere Informationen über NSG finden Sie im Tutorial zum [Filtern des Netzwerkdatenverkehrs](../virtual-network/tutorial-filter-network-traffic.md).

> [!NOTE]
> Stellen Sie sicher, dass Sie die Agents (sowohl den lokalen Server-Agent als auch den Azure-Server-Agent) installiert und das PowerShell-Skript ausgeführt haben, bevor Sie mit diesem Schritt fortfahren.
>

## <a name="enable-network-watcher"></a><a name="enablenetworkwatcher"></a>Aktivieren von Network Watcher

Für alle Abonnements, die über ein virtuelles Netzwerk verfügen, wird Network Watcher aktiviert. Stellen Sie sicher, dass Network Watcher für Ihr Abonnement nicht explizit deaktiviert ist. Weitere Informationen finden Sie unter [Aktivieren von Network Watcher](../network-watcher/network-watcher-create.md).

## <a name="create-a-connection-monitor"></a><a name="createcm"></a> Erstellen eines Verbindungsmonitors

Eine Übersicht darüber, wie Sie einen Verbindungsmonitor, Tests und Testgruppen für Quell- und Zielendpunkte in Ihrem Netzwerk erstellen, finden Sie unter [Erstellen eines Verbindungsmonitors](../network-watcher/connection-monitor-create-using-portal.md). Verwenden Sie die folgenden Schritte, um die Verbindungsüberwachung für privates Peering und Microsoft-Peering zu konfigurieren.

1. Navigieren Sie im Azure-Portal zu Ihrer **Network Watcher**-Ressource, und wählen Sie **Verbindungsmonitor** unter *Überwachung* aus. Wählen Sie dann **Erstellen** aus, um einen neuen Verbindungsmonitor zu erstellen.

    :::image type="content" source="./media/how-to-configure-connection-monitor/create-connection-monitor.png" alt-text="Screenshot: Verbindungsmonitor in Network Watcher":::

1. Wählen Sie auf der Registerkarte der **Grundeinstellungen** des Workflows für die Erstellung für das Feld *Region* die gleiche Region aus, in der Sie Ihren Log Analytics-Arbeitsbereich bereitgestellt haben. Wählen Sie für die *Arbeitsbereichskonfiguration* den vorhandenen Log Analytics-Arbeitsbereich aus, den Sie zuvor erstellt haben. Wählen Sie dann **Weiter: Testgruppen >>** aus.

    :::image type="content" source="./media/how-to-configure-connection-monitor/connection-monitor-basic.png" alt-text="Screenshot: Registerkarte der „Grundeinstellungen“ zum Erstellen des Verbindungsmonitors":::

1. Auf der Seite „Testgruppendetails hinzufügen“ fügen Sie die Quell- und Zielendpunkte für Ihre Testgruppe hinzu. Außerdem richten Sie die Testkonfigurationen zwischen ihnen ein. Geben Sie einen **Namen** für diese Testgruppe ein.

    :::image type="content" source="./media/how-to-configure-connection-monitor/add-test-group-details.png" alt-text="Screenshot: Seite „Testgruppendetails hinzufügen“":::

1. Wählen Sie **Quelle hinzufügen** aus, und navigieren Sie zur Registerkarte **Nicht-Azure-Endpunkte**. Wählen Sie die lokalen Ressourcen aus, auf denen der Log Analytics-Agent installiert ist, dessen Konnektivität Sie überwachen möchten, und wählen Sie dann **Endpunkte hinzufügen** aus.

    :::image type="content" source="./media/how-to-configure-connection-monitor/add-source-endpoints.png" alt-text="Screenshot: Hinzufügen von Quellendpunkten":::

1. Wählen Sie als nächstes **Ziele hinzufügen** aus. 

    Um die Konnektivität über **privates Peering** von ExpressRoute zu überwachen, navigieren Sie zur Registerkarte **Azure Endpunkte**. Wählen Sie die Azure-Ressourcen aus, auf denen der Network Watcher-Agent installiert ist, den Sie für die Überwachung der Konnektivität zu Ihren virtuellen Netzwerken in Azure benötigen. Stellen Sie sicher, dass Sie die private IP-Adresse jeder dieser Ressourcen in der Spalte *IP* auswählen. Wählen Sie **Endpunkte hinzufügen** aus, um diese Endpunkte zu Ihrer Liste der Ziele für die Testgruppe hinzuzufügen.
    
    :::image type="content" source="./media/how-to-configure-connection-monitor/add-destination-endpoints.png" alt-text="Screenshot: Hinzufügen von Azure-Zielendpunkten":::

    Um die Konnektivität über **Microsoft Peering** von ExpressRoute zu überwachen, navigieren Sie zur Registerkarte **Externe Adressen**. Wählen Sie die Microsoft-Dienstendpunkte aus, zu denen Sie die Konnektivität über Microsoft Peering überwachen möchten. Wählen Sie **Endpunkte hinzufügen** aus, um diese Endpunkte zu Ihrer Liste der Ziele für die Testgruppe hinzuzufügen.

    :::image type="content" source="./media/how-to-configure-connection-monitor/add-external-destination-endpoints.png" alt-text="Screenshot: Hinzufügen von externen Zielendpunkten":::

1. Wählen Sie jetzt **Testkonfiguration hinzufügen** aus. Wählen Sie als Protokoll **TCP** aus, und geben Sie den **Zielport** ein, den Sie auf Ihren Servern geöffnet haben. Konfigurieren Sie dann Ihre **Testhäufigkeit** und die **Schwellenwerte für fehlerhafte Prüfungen und die Roundtripzeit (RTT)** . Wählen Sie dann **Testkonfiguration hinzufügen** aus.

    :::image type="content" source="./media/how-to-configure-connection-monitor/add-test-configuration.png" alt-text="Screenshot: Seite „Testkonfiguration hinzufügen“":::

1. Wählen Sie **Testgruppe hinzufügen** aus, sobald Sie Ihre Quellen, Ziele und die Testkonfiguration hinzugefügt haben.

    :::image type="content" source="./media/how-to-configure-connection-monitor/add-test-group-details-configured.png" alt-text="Screenshot: Konfigurieren eines Details von „Testgruppendetails hinzufügen“" lightbox="./media/how-to-configure-connection-monitor/add-test-group-details-configured-expanded.png":::

1. Wählen Sie **Weiter: Warnung erstellen >>** aus, wenn Sie Warnungen erstellen möchten. Nachdem Sie fertig sind, wählen Sie **Überprüfen + erstellen** und danach **Erstellen** aus.

## <a name="view-results"></a>Anzeigen der Ergebnisse

1. Navigieren Sie zu Ihrer **Network Watcher**-Ressource, und wählen Sie **Verbindungsmonitor** unter *Überwachung* aus. Der neue Verbindungsmonitor sollte nach fünf Minuten angezeigt werden. Um die Netzwerktopologie und die Leistungsdiagramme des Verbindungsmonitors anzuzeigen, wählen Sie den Test in der Dropdownliste der Testgruppen aus.

    :::image type="content" source="./media/how-to-configure-connection-monitor/overview.png" alt-text="Screenshot: Übersichtsseite des Verbindungsmonitors" lightbox="./media/how-to-configure-connection-monitor/overview-expanded.png":::

1. Im Bereich **Leistungsanalyse** können Sie den Prozentsatz der fehlerhaften Prüfungen und die Ergebnisse der einzelnen Tests in Bezug auf die Roundtripzeit anzeigen. Sie können den Zeitrahmen für die angezeigten Daten anpassen, indem Sie die Dropdownliste am oberen Rand des Panels auswählen.

    :::image type="content" source="./media/how-to-configure-connection-monitor/performance-analysis.png" alt-text="Screenshot: Panel „Leistungsanalyse“" lightbox="./media/how-to-configure-connection-monitor/performance-analysis-expanded.png":::

1. Wenn Sie das Panel **Leistungsanalyse** schließen, wird die vom Verbindungsmonitor ermittelte Netzwerktopologie zwischen den von Ihnen ausgewählten Quell- und Zielendpunkten angezeigt. In dieser Ansicht werden die bidirektionalen Pfade des Datenverkehrs zwischen Ihren Quell- und Zielendpunkten angezeigt. Sie können auch die Hop-by-Hop-Latenz von Paketen anzeigen, bevor sie das Edgenetzwerk von Microsoft erreichen. 

    :::image type="content" source="./media/how-to-configure-connection-monitor/topology.png" alt-text="Screenshot: Netzwerktopologie im Verbindungsmonitor" lightbox="./media/how-to-configure-connection-monitor/topology-expanded.png":::

    Wenn Sie einen Hop in der Topologieansicht auswählen, werden zusätzliche Informationen über den Hop angezeigt. Alle Probleme, die der Verbindungsmonitor in Bezug auf den Hop erkannt hat, werden hier ebenfalls angezeigt.

    :::image type="content" source="./media/how-to-configure-connection-monitor/hop-details.png" alt-text="Screenshot: Weitere Informationen zu einem Netzwerkhop":::

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum [Überwachen von Azure ExpressRoute](monitor-expressroute.md)