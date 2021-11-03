---
title: Schützen von Datenverkehr, der für private Endpunkte von Azure Virtual WAN bestimmt ist
description: Es wird beschrieben, wie Sie Netzwerk- und Anwendungsregeln zum Schützen von Datenverkehr verwenden, der für private Endpunkte von Azure Virtual WAN bestimmt ist.
services: firewall-manager
author: KumudD
ms.service: firewall-manager
ms.topic: how-to
ms.date: 04/02/2021
ms.author: kumud
ms.openlocfilehash: 58dd59bc47c15dd5f6b04588b441297b9fbb7968
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131068682"
---
# <a name="secure-traffic-destined-to-private-endpoints-in-azure-virtual-wan"></a>Schützen von Datenverkehr, der für private Endpunkte von Azure Virtual WAN bestimmt ist

> [!NOTE]
> Dieser Artikel gilt nur für geschützte virtuelle Hubs. Wenn Sie Datenverkehr für private Endpunkte untersuchen möchten, die Azure Firewall in einem virtuellen Hubnetzwerk verwenden, finden Sie weitere Informationen unter [Verwenden der Azure Firewall zum Überprüfen des Datenverkehrs für einen privaten Endpunkt](../private-link/inspect-traffic-with-azure-firewall.md).

Der [private Azure-Endpunkt](../private-link/private-endpoint-overview.md) ist der grundlegende Baustein für [Azure Private Link](../private-link/private-link-overview.md). Private Endpunkte ermöglichen die Bereitstellung von Azure-Ressourcen in einem virtuellen Netzwerk, um privat mit Private Link-Ressourcen zu kommunizieren.

Private Endpunkte ermöglichen Ressourcenzugriff auf den Private Link-Dienst, der in einem virtuellen Netzwerk bereitgestellt wird. Der Zugriff auf den privaten Endpunkt durch Peering virtueller Netzwerke und lokale Netzwerkverbindungen erweitern die Konnektivität.

Unter Umständen müssen Sie Datenverkehr von Clients, der für Dienste bestimmt ist, die über private Endpunkte in einem virtuellen Netzwerk mit Virtual WAN-Verbindung verfügbar gemacht werden, entweder lokal oder in Azure filtern. In diesem Artikel wird die entsprechende Vorgehensweise beschrieben, indem ein [geschützter virtueller Hub](../firewall-manager/secured-virtual-hub.md) mit [Azure Firewall](../firewall/overview.md) als Sicherheitsanbieter genutzt wird.

Von Azure Firewall wird Datenverkehr mit einer der folgenden Methoden gefiltert:

* [FQDN in Netzwerkregeln](../firewall/fqdn-filtering-network-rules.md) für TCP- und UDP-Protokolle
* [FQDN in Anwendungsregeln](../firewall/features.md#application-fqdn-filtering-rules) für HTTP, HTTPS und MSSQL.
* IP-Adressen, Port und Protokoll mit [Netzwerkregeln](../firewall/features.md#network-traffic-filtering-rules) für Quelle und Ziel

Verwenden Sie Anwendungsregeln für Netzwerkregeln, um Datenverkehr zu untersuchen, der für private Endpunkte bestimmt ist.
Ein geschützter virtueller Hub wird von Microsoft verwaltet und kann nicht mit einer [privaten DNS-Zone](../dns/private-dns-privatednszone.md) verknüpft werden. Dies ist erforderlich, um den FQDN einer [Private Link-Ressource](../private-link/private-endpoint-overview.md#private-link-resource) in die entsprechende IP-Adresse des privaten Endpunkts aufzulösen.

Die SQL-FQDN-Filterung wird nur im [Proxymodus](../azure-sql/database/connectivity-architecture.md#connection-policy) unterstützt (Port 1433). Im Modus *Proxy* kommt es unter Umständen zu längeren Wartezeiten als im Modus *Umleiten*. Wenn Sie weiterhin den Umleitungsmodus (Standardmodus für Clients, die eine Verbindung innerhalb von Azure herstellen) verwenden möchten, können Sie den Zugriff mit dem FQDN in Netzwerkregeln der Firewall filtern.

## <a name="filter-traffic-using-fqdn-in-network-and-application-rules"></a>Filtern von Datenverkehr per FQDN in Netzwerk- und Anwendungsregeln

Mit den folgenden Schritten ermöglichen Sie für Azure Firewall das Filtern von Datenverkehr per FQDN in Netzwerk- und Anwendungsregeln:

1. Stellen Sie einen virtuellen Computer mit [DNS-Weiterleitung](../private-link/private-endpoint-dns.md#virtual-network-and-on-premises-workloads-using-a-dns-forwarder) in einem virtuellen Netzwerk bereit, das mit dem geschützten virtuellen Hub verbunden und mit den privaten DNS-Zonen verknüpft ist, in denen die A-Eintragstypen für die privaten Endpunkte gehostet werden.

2. Konfigurieren Sie die [Einstellungen für das benutzerdefinierte DNS](../firewall/dns-settings.md#configure-custom-dns-servers---azure-portal) so, dass auf die IP-Adresse des virtuellen Computers mit der DNS-Weiterleitung verwiesen wird. Aktivieren Sie anschließend den DNS-Proxy in der Firewallrichtlinie, die der bereitgestellten Azure Firewall-Instanz auf dem geschützten virtuellen Hub zugeordnet ist.

3. Führen Sie die Konfiguration der [benutzerdefinierten DNS-Server](../virtual-network/manage-virtual-network.md#change-dns-servers) für die virtuellen Netzwerke, die mit dem geschützten virtuellen Hub verbunden sind, so durch, dass auf die private IP-Adresse verwiesen wird, die der bereitgestellten Azure Firewall-Instanz auf dem geschützten virtuellen Hub zugeordnet ist.

4. Konfigurieren Sie die lokalen DNS-Server so, dass DNS-Abfragen für die öffentlichen DNS-Zonen der privaten Endpunkte an die private IP-Adresse weitergeleitet werden, die der bereitgestellten Azure Firewall-Instanz auf dem geschützten virtuellen Hub zugeordnet sind.

5. Konfigurieren Sie je nach Bedarf eine [Anwendungsregel](../firewall/tutorial-firewall-deploy-portal.md#configure-an-application-rule) oder [Netzwerkregel](../firewall/tutorial-firewall-deploy-portal.md#configure-a-network-rule) in der Firewallrichtlinie, die der bereitgestellten Azure Firewall-Instanz auf dem geschützten virtuellen Hub zugeordnet ist. Verwenden Sie hierfür den *Zieltyp*-FQDN und den öffentlichen FQDN der Private Link-Ressource als *Ziel*.

6. Navigieren Sie in der Firewallrichtlinie, die der bereitgestellten Azure Firewall-Instanz auf dem geschützten virtuellen Hub zugeordnet ist, zu *Geschützte virtuelle Hubs*. Wählen Sie dann den geschützten virtuellen Hub aus, auf dem die Filterung des Datenverkehrs, der für private Endpunkte bestimmt ist, konfiguriert wird.

7. Navigieren Sie zu **Sicherheitskonfiguration**, und wählen Sie unter **Private traffic** (Privater Datenverkehr) die Option **Send via Azure Firewall** (Über Azure Firewall senden) aus.

8. Wählen Sie die Option **Präfixe für privaten Datenverkehr bearbeiten** aus, um die CIDR-Präfixe zu bearbeiten, die per Azure Firewall auf dem geschützten virtuellen Hub untersucht werden. Fügen Sie wie folgt ein /32-Präfix für jeden privaten Endpunkt hinzu:

   > [!IMPORTANT]
   > Wenn diese /32-Präfixe nicht konfiguriert werden, wird Azure Firewall von dem Datenverkehr, der für private Endpunkte bestimmt ist, umgangen.

   :::image type="content" source="./media/private-link-inspection-secure-virtual-hub/firewall-manager-security-configuration.png" alt-text="Sicherheitskonfiguration für Firewall Manager" border="true":::

Diese Schritte funktionieren nur, wenn die Clients und privaten Endpunkte in unterschiedlichen virtuellen Netzwerken bereitgestellt werden, die mit demselben geschützten virtuellen Hub verbunden sind. Darüber hinaus funktionieren sie auch für lokale Clients. Wenn die Clients und privaten Endpunkte in demselben virtuellen Netzwerk bereitgestellt werden, muss eine UDR mit /32-Routen für die privaten Endpunkte erstellt werden. Konfigurieren Sie diese Routen so, dass **Typ des nächsten Hops** auf **Virtuelle Appliance** und **Adresse des nächsten Hops** auf die private IP-Adresse der auf dem geschützten virtuellen Hub bereitgestellten Azure Firewall-Instanz festgelegt ist. Die Option **Gatewayrouten verteilen** muss auf **Ja** festgelegt werden.

Im folgenden Diagramm sind die Abläufe des DNS und des Datenverkehrs für die unterschiedlichen Clients dargestellt, die bei der Verbindungsherstellung mit einem privaten Endpunkt erfolgen, der in einem Azure Virtual WAN bereitgestellt wurde:

:::image type="content" source="./media/private-link-inspection-secure-virtual-hub/private-link-inspection-virtual-wan-architecture.png" alt-text="Datenverkehrsfluss" border="true":::

## <a name="troubleshooting"></a>Problembehandlung

Hier sind die wichtigsten Probleme angegeben, die ggf. auftreten, wenn Sie Datenverkehr filtern möchten, der für private Endpunkte bestimmt ist und einen geschützten virtuellen Hub durchläuft:

- Clients können keine Verbindung mit privaten Endpunkten herstellen.

- Azure Firewall wird umgangen. Dieses Symptom kann überprüft werden, indem ermittelt wird, ob in den Protokolleinträgen von Azure Firewall Netzwerk- oder Anwendungsregeln fehlen.

In den meisten Fällen wird dies durch eines der folgenden Probleme verursacht:

- Fehlerhafte Auflösung von DNS-Namen

- Fehlerhafte Routingkonfiguration

### <a name="incorrect-dns-name-resolution"></a>Fehlerhafte Auflösung von DNS-Namen

1. Vergewissern Sie sich, dass die DNS-Server des virtuellen Netzwerks auf *Custom* (Benutzerdefiniert) festgelegt sind und dass es sich bei der IP-Adresse um die private IP-Adresse von Azure Firewall auf dem geschützten virtuellen Hub handelt.

   Azure CLI:

   ```azurecli-interactive
   az network vnet show --name <VNET Name> --resource-group <Resource Group Name> --query "dhcpOptions.dnsServers"
   ```
2. Überprüfen Sie, ob Clients in demselben virtuellen Netzwerk, in dem sich auch der virtuelle Computer mit der DNS-Weiterleitung befindet, den öffentlichen FQDN des privaten Endpunkts in die entsprechende eigene private IP-Adresse auflösen können, indem der für die DNS-Weiterleitung konfigurierte virtuelle Computer direkt abgefragt wird.

   Linux:

   ```bash
   dig @<DNS forwarder VM IP address> <Private endpoint public FQDN>
   ```
3. Überprüfen Sie die Azure Firewall-Protokolleinträge für *AzureFirewallDNSProxy*, und vergewissern Sie sich, dass das Empfangen und Auflösen von DNS-Abfragen von den Clients möglich ist.

   ```kusto
   AzureDiagnostics
   | where Category contains "DNS"
   | where msg_s contains "database.windows.net"
   ```
4. Vergewissern Sie sich, dass der *DNS-Proxy* aktiviert wurde und ein *benutzerdefinierter* DNS-Server, für den auf die IP-Adresse des virtuellen Computers mit der DNS-Weiterleitung verwiesen wird, in der Firewallrichtlinie konfiguriert wurde, die der Azure Firewall-Instanz auf dem geschützten virtuellen Hub zugeordnet ist.

   Azure CLI:

   ```azurecli-interactive
   az network firewall policy show --name <Firewall Policy> --resource-group <Resource Group Name> --query dnsSettings
   ```

### <a name="incorrect-routing-configuration"></a>Fehlerhafte Routingkonfiguration

1. Überprüfen Sie die *Sicherheitskonfiguration* in der Firewallrichtlinie, die der auf dem geschützten virtuellen Hub bereitgestellten Azure Firewall-Instanz zugeordnet ist. Stellen Sie sicher, dass hierfür in der Spalte **PRIVATE TRAFFIC** (PRIVATER DATENVERKEHR) der Status **Secured by Azure Firewall** (Durch Azure Firewall geschützt) für alle VNET- und Branchverbindungen angezeigt wird, für die Sie Datenverkehr filtern möchten.

   :::image type="content" source="./media/private-link-inspection-secure-virtual-hub/firewall-policy-private-traffic-configuration.png" alt-text="Per Azure Firewall geschützter privater Datenverkehr" border="true":::

2. Überprüfen Sie die **Sicherheitskonfiguration** in der Firewallrichtlinie, die der auf dem geschützten virtuellen Hub bereitgestellten Azure Firewall-Instanz zugeordnet ist. Stellen Sie sicher, dass unter **Präfixe für privaten Datenverkehr bearbeiten** für jede private IP-Adresse eines privaten Endpunkts, für die Sie Datenverkehr filtern möchten, ein /32-Eintrag vorhanden ist.

   :::image type="content" source="./media/private-link-inspection-secure-virtual-hub/firewall-manager-security-configuration.png" alt-text="Sicherheitskonfiguration für Firewall Manager: Präfixe für privaten Datenverkehr" border="true":::

3. Untersuchen Sie auf dem geschützten virtuellen Hub im Virtual WAN die effektiven Routen für die Routingtabellen, die den VNET- und Branchverbindungen zugeordnet sind, für die Sie Datenverkehr filtern möchten. Stellen Sie sicher, dass für jede private IP-Adresse eines privaten Endpunkts, für die Sie Datenverkehr filtern möchten, /32-Einträge vorhanden sind.

   :::image type="content" source="./media/private-link-inspection-secure-virtual-hub/secured-virtual-hub-effective-routes.png" alt-text="Geschützter virtueller Hub: Effektive Routen" border="true":::

4. Untersuchen Sie die effektiven Routen auf den NICs, die an die virtuellen Computer in den virtuellen Netzwerken angefügt sind, für die Sie Datenverkehr filtern möchten. Stellen Sie sicher, dass für jede private IP-Adresse eines privaten Endpunkts, für die Sie Datenverkehr filtern möchten, /32-Einträge vorhanden sind.
 
   Azure CLI:

   ```azurecli-interactive
   az network nic show-effective-route-table --name <Network Interface Name> --resource-group <Resource Group Name> -o table
   ```
5. Untersuchen Sie die Routingtabellen Ihrer lokalen Routinggeräte. Machen Sie sich mit den Adressräumen der virtuellen Netzwerke vertraut, in denen die privaten Endpunkte bereitgestellt werden.

   Von Azure Virtual WAN werden die Präfixe, die unter **Präfixe für privaten Datenverkehr bearbeiten** in der Firewallrichtlinie für die **Sicherheitskonfiguration** konfiguriert sind, nicht für die lokale Umgebung angekündigt. Es ist zu erwarten, dass die /32-Einträge nicht in den Routingtabellen Ihrer lokalen Routinggeräte angezeigt werden.

6. Sehen Sie sich **AzureFirewallApplicationRule** und **AzureFirewallNetworkRule** in den Azure Firewall-Protokollen an. Stellen Sie sicher, dass Datenverkehr, der für private Endpunkte bestimmt ist, protokolliert wird.

   Protokolleinträge vom Typ **AzureFirewallNetworkRule** enthalten keine FQDN-Informationen. Filtern Sie beim Untersuchen der Netzwerkregeln nach IP-Adresse und Port.

   Beim Filtern von Datenverkehr, der für private Endpunkte unter [Azure Files](../storage/files/storage-files-introduction.md) bestimmt ist, werden Protokolleinträge vom Typ **AzureFirewallNetworkRule** nur generiert, wenn ein Client erstmalig eingebunden wird oder eine Verbindung mit der Dateifreigabe herstellt. Von Azure Firewall werden für Dateien auf der Dateifreigabe keine Protokolle für [CRUD](https://en.wikipedia.org/wiki/Create,_read,_update_and_delete)-Vorgänge generiert. Der Grund ist, dass CRUD-Vorgänge über den dauerhaften TCP-Kanal übertragen werden, der geöffnet wird, wenn der Client erstmalig eine Verbindung mit der Dateifreigabe herstellt oder darin eingebunden wird.

   Beispiel für Protokollabfrage per Anwendungsregel:

   ```kusto
   AzureDiagnostics
   | where msg_s contains "database.windows.net"
   | where Category contains "ApplicationRule"
   ```
## <a name="next-steps"></a>Nächste Schritte

- [Verwenden der Azure Firewall zum Überprüfen des Datenverkehrs für einen privaten Endpunkt](../private-link/inspect-traffic-with-azure-firewall.md)
