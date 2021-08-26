---
title: Übersicht über Netzwerkkonzepte – Azure Database for PostgreSQL – Flexible Server
description: In diesem Artikel erfahren Sie mehr über die Konnektivitäts- und Netzwerkoptionen in der Bereitstellungsoption „Flexibler Server“ von Azure Database for PostgreSQL.
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: conceptual
ms.date: 07/08/2021
ms.openlocfilehash: 3d35eed46082d162afed5a2c9685265812b1e2d7
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122339962"
---
# <a name="networking-overview-for-azure-database-for-postgresql---flexible-server-preview"></a>Übersicht über den Netzwerkbetrieb bei Azure Database for PostgreSQL – Flexibler Server (Vorschau)

In diesem Artikel werden die Konnektivitäts- und Netzwerkkonzepte für Azure Database for PostgreSQL – Flexible Server beschrieben. Die Bereitstellungsoption „Flexibler Server“ befindet sich derzeit in der Vorschau.

Wenn Sie eine Instanz von Azure Database for PostgreSQL – Flexibler Server (einen *flexiblen Server*) erstellen, müssen Sie eine der folgenden Netzwerkoptionen auswählen: **Privater Zugriff (VNet-Integration)** oder **Öffentlicher Zugriff (zulässige IP-Adressen)** . 

> [!NOTE]
> Sie können Ihre Netzwerkoption nach der Erstellung des Servers nicht mehr ändern. 

Die folgenden Eigenschaften gelten unabhängig davon, ob Sie den privaten oder den öffentlichen Zugriff wählen:

* Verbindungen von zulässigen IP-Adressen aus müssen beim PostgreSQL-Server mit gültigen Anmeldeinformationen authentifiziert werden.
* Für den Netzwerkdatenverkehr wird die [Verbindungsverschlüsselung](#tls-and-ssl) erzwungen.
* Der Server verfügt über einen vollqualifizierten Domänennamen (Fully Qualified Domain Name, FQDN). Für die `hostname`-Eigenschaft in Verbindungszeichenfolgen wird empfohlen, den FQDN anstelle einer IP-Adresse zu verwenden.
* Über beide Optionen wird der Zugriff auf Serverebene, nicht auf Datenbank- oder auf Tabellenebene gesteuert. Der Zugriff auf Datenbanken, Tabellen und andere Objekte wird mithilfe der Rolleneigenschaften in PostgreSQL gesteuert.

> [!NOTE]
> Da Azure Database for PostgreSQL ein verwalteter Datenbankdienst ist, erhalten Benutzer keinen Host- oder Betriebssystemzugriff, um Konfigurationsdateien wie `pg_hba.conf` anzuzeigen oder zu ändern. Der Inhalt der Dateien wird abhängig von den Netzwerkeinstellungen automatisch aktualisiert.

## <a name="private-access-vnet-integration"></a>Privater Zugriff (VNET-Integration)

Sie können einen flexiblen Server in Ihrem [virtuellen Azure-Netzwerk (VNet)](../../virtual-network/virtual-networks-overview.md) bereitstellen. Virtuelle Azure-Netzwerke ermöglichen eine private und sichere Netzwerkkommunikation. Ressourcen in einem VNet können über private IP-Adressen kommunizieren, die in diesem Netzwerk zugewiesen wurden.

Diese Netzwerkoption bietet sich an, wenn Sie die folgenden Funktionen benötigen:

* Verbinden von Azure-Ressourcen im gleichen virtuellen Netzwerk mit Ihrem flexiblen Server über private IP-Adressen
* Herstellen einer Verbindung von Azure-externen Ressourcen mit Ihrem flexiblen Server über ein VPN oder eine Azure ExpressRoute-Leitung
* Sicherstellen, dass der flexible Server nicht über einen öffentlichen Endpunkt verfügt, auf den über das Internet zugegriffen werden kann

:::image type="content" source="./media/how-to-manage-virtual-network-portal/flexible-pg-vnet-diagram.png" alt-text="Abbildung der Funktionsweise des Peerings virtueller Netzwerke, von denen eines einen flexiblen Server enthält":::

Im obigen Diagramm ist Folgendes zu sehen:
- Flexible Server werden in das Subnetz 10.0.1.0/24 des virtuellen Netzwerk „VNet-1“ eingefügt.
- Anwendungen, die in verschiedenen Subnetzen innerhalb desselben VNet bereitgestellt werden, können direkt auf flexible Server zugreifen.
- Anwendungen, die in einem anderen VNet (VNet-2) bereitgestellt wurden, haben keinen direkten Zugriff auf flexible Server. Sie können erst nach einem [Peering virtueller Netzwerke für eine private DNS-Zone](#private-dns-zone-and-virtual-network-peering) auf die flexiblen Server zugreifen.
   
### <a name="virtual-network-concepts"></a>Virtuelle Netzwerke: Konzepte

Ein virtuelles Azure-Netzwerk (VNet) enthält einen privaten IP-Adressraum, der für Ihre Verwendung konfiguriert ist. Ihr virtuelles Netzwerk muss in derselben Azure-Region wie Ihr flexibler Server bereitgestellt werden. Weitere Informationen zu virtuellen Netzwerken finden Sie unter [Übersicht über Azure Virtual Network](../../virtual-network/virtual-networks-overview.md).

Nachstehend werden einige Konzepte erläutert, die Sie kennen sollten, wenn Sie virtuelle Netzwerke mit Instanzen von PostgreSQL – Flexibler Server verwenden.

* **Delegiertes Subnetz:** Ein virtuelles Netzwerk enthält Subnetze (untergeordnete Netzwerke). Subnetze bieten Ihnen die Möglichkeit, Ihr virtuelles Netzwerk in kleinere Adressräume einzuteilen. Azure-Ressourcen werden in bestimmten Subnetzen innerhalb eines virtuellen Netzwerks bereitgestellt. 

  Ihr flexibler Server muss sich in einem Subnetz befinden, das *delegiert* ist. Das heißt, nur Instanzen von Azure Database for PostgreSQL – Flexibler Server können dieses Subnetz verwenden. Im delegierten Subnetz können sich keine anderen Azure-Ressourcentypen befinden. Sie delegieren ein Subnetz, indem Sie seine Delegierungseigenschaft mit `Microsoft.DBforPostgreSQL/flexibleServers` zuweisen.

  > [!IMPORTANT]
  > Die Namen `AzureFirewallSubnet`, `AzureFirewallManagementSubnet`, `AzureBastionSubnet` und `GatewaySubnet` sind in Azure reserviert. Verwenden Sie keinen dieser Namen als Subnetznamen.

* **Netzwerksicherheitsgruppen (NSG)** Mit Sicherheitsregeln in Netzwerksicherheitsgruppen können Sie den Typ des ein- und ausgehenden Netzwerkdatenverkehrs von VNet-Subnetzen und Netzwerkschnittstellen filtern. Weitere Informationen finden Sie in der [Übersicht über Netzwerksicherheitsgruppen](../../virtual-network/network-security-groups-overview.md).

  Anwendungssicherheitsgruppen (ASGs) vereinfachen die Steuerung der Sicherheit auf Schicht 4 mithilfe von NSGs für flache Netzwerke. Folgendes können Sie damit schnell ausführen:
  
  - Verknüpfen von VMs mit einer ASG oder Entfernen von VMs aus einer ASG
  - Dynamisches Anwenden von Regeln auf diese VMs oder Entfernen von Regeln für diese VMs 
  
  Weitere Informationen finden Sie in der [Übersicht über ASGs](../../virtual-network/application-security-groups.md). 
  
  Derzeit werden keine NSGs unterstützt, bei denen eine ASG Teil der Regel mit Azure Database for PostgreSQL – Flexibler Server ist. Es wird derzeit empfohlen, die [IP-basierte Filterung von Quelle oder Ziel](../../virtual-network/network-security-groups-overview.md#security-rules) in einer NSG zu verwenden. 

  > [!IMPORTANT]
  > Die Funktionen von Azure Database for PostgreSQL – Flexibler Server erfordern die Möglichkeit, ausgehenden Datenverkehr an die Zielports 5432 und 6432 zu senden. Wenn Sie Netzwerksicherheitsgruppen (NSGs) erstellen, um ausgehenden Datenverkehr von Azure Database for PostgreSQL – Flexibler Server zu blockieren, stellen Sie sicher, dass Datenverkehr an diese Zielports zugelassen wird. 

* **Integration privater DNS-Zonen:** Mit der Integration privater Azure-DNS-Zonen können Sie das private DNS innerhalb des aktuellen VNet oder eines beliebigen Peer-VNet in derselben Region auflösen, in dem die private DNS-Zone verknüpft ist. 

### <a name="using-a-private-dns-zone"></a>Verwenden einer privaten DNS-Zone

Wenn Sie über das Azure-Portal oder mit der Azure-Befehlszeilenschnittstelle flexible Server mit einem virtuellen Netzwerk erstellen, wird automatisch eine neue private DNS-Zone für jeden Server in Ihrem Abonnement bereitgestellt. Dabei wird der von Ihnen bereitgestellte Servername verwendet. 

Wenn Sie eine Azure-API, eine Azure Resource Manager-Vorlage (ARM-Vorlage) oder Terraform verwenden, erstellen Sie private DNS-Zonen, die auf `postgres.database.azure.com` enden. Verwenden Sie diese Zonen beim Konfigurieren flexibler Server mit privatem Zugriff. Weitere Informationen finden Sie in der [Übersicht über private DNS-Zonen](../../dns/private-dns-overview.md).


### <a name="integration-with-a-custom-dns-server"></a>Integration mit einem benutzerdefinierten DNS-Server

Wenn Sie einen benutzerdefinierten DNS-Server verwenden, müssen Sie eine DNS-Weiterleitung verwenden, um den FQDN von Azure Database for PostgreSQL – Flexibler Server aufzulösen. Die IP-Adresse der Weiterleitung sollte [168.63.129.16](../../virtual-network/what-is-ip-address-168-63-129-16.md) sein. 

Der benutzerdefinierte DNS-Server sollte sich innerhalb des VNet befinden oder über die DNS-Servereinstellung des VNet erreichbar sein. Weitere Informationen finden Sie unter [Namensauflösung mithilfe eines eigenen DNS-Servers](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

### <a name="private-dns-zone-and-virtual-network-peering"></a>Private DNS-Zone und Peering virtueller Netzwerke

Die Einstellungen für private DNS-Zonen und das Peering virtueller Netzwerke sind voneinander unabhängig. Wenn Sie von einem Client aus, der in einem anderen VNet derselben Region oder einer anderen Region bereitgestellt wurde, eine Verbindung mit dem flexiblen Server herstellen möchten, müssen Sie die private DNS-Zone mit dem VNet verknüpfen. Weitere Informationen finden Sie unter [Verknüpfen des virtuellen Netzwerks](../../dns/private-dns-getstarted-portal.md#link-the-virtual-network).

> [!NOTE]
> Es können ausschließlich private DNS-Zonen verknüpft werden, deren Namen auf `postgres.database.azure.com` enden.

### <a name="unsupported-virtual-network-scenarios"></a>Nicht unterstützte virtuelle Netzwerkszenarios

Im Folgenden finden Sie einige Einschränkungen bei der Arbeit mit virtuellen Netzwerken:

* Für einen in einem virtuellen Netzwerk bereitgestellten flexiblen Server darf kein öffentlicher Endpunkt (oder öffentliche IP-Adresse oder DNS) festgelegt sein.
* Nachdem der flexible Server in einem VNet und einem Subnetz bereitgestellt wurde, kann er nicht in ein anderes VNet oder Subnetz verschoben werden. Auch das VNet kann in keine andere Ressourcengruppe oder kein anderes Abonnement verschoben werden.
* Die Subnetzgröße (Adressräume) kann nicht mehr erhöht werden, wenn bereits Ressourcen im Subnetz vorhanden sind.
* Flexible Server unterstützen Azure Private Link nicht. Stattdessen wird die Einfügung in virtuelle Netzwerke verwendet, um den flexiblen Server in einem VNet verfügbar zu machen. 


## <a name="public-access-allowed-ip-addresses"></a>Öffentlicher Zugriff (zugelassene IP-Adressen)

Wenn Sie sich für die öffentliche Zugriffsmethode entscheiden, erfolgt der Zugriff über das Internet auf Ihren flexiblen Server über einen öffentlichen Endpunkt. Der öffentliche Endpunkt ist eine öffentlich auflösbare DNS-Adresse. Der Ausdruck *zulässige IP-Adressen* bezieht sich auf einen Bereich von IP-Adressen, denen Sie die Berechtigung erteilen, auf den Server zuzugreifen. Diese Berechtigungen heißen *Firewallregeln*. 

Diese Netzwerkoption bietet sich an, wenn Sie die folgenden Funktionen benötigen:

* Herstellen einer Verbindung von Azure-Ressourcen aus, die keine virtuellen Netzwerke unterstützen
* Herstellen einer Verbindung von Azure-externen Ressourcen aus, die weder per VPN noch per ExpressRoute verbunden sind
* Sicherstellen, dass der flexible Server über einen öffentlichen Endpunkt verfügt, auf den über das Internet zugegriffen werden kann

Die öffentliche Zugriffsmethode weist u. a. folgende Eigenschaften auf:

* Nur die IP-Adressen, die Sie zulassen, dürfen auf Ihre Instanz von PostgreSQL – Flexibler Server zugreifen. Standardmäßig sind keine IP-Adressen zugelassen. Sie können IP-Adressen während oder nach der Servererstellung hinzufügen.
* Ihr PostgreSQL-Server verfügt über einen öffentlich auflösbaren DNS-Namen.
* Ihr flexibler Server befindet sich nicht in einem Ihrer virtuellen Azure-Netzwerke.
* Der Netzwerkdatenverkehr zu und von Ihrem Server erfolgt nicht über ein privates Netzwerk. Der Datenverkehr verwendet die allgemeinen Internetpfade.

### <a name="firewall-rules"></a>Firewallregeln

Wenn ein Verbindungsversuch von einer IP-Adresse stammt, die Sie über eine Firewallregel nicht zugelassen haben, empfängt der ursprüngliche Client einen Fehler.

### <a name="allowing-all-azure-ip-addresses"></a>Zulassen aller Azure-IP-Adressen

Wenn eine feste IP-Adresse für ausgehenden Datenverkehr für Ihren Azure-Dienst nicht verfügbar ist, können Sie die Aktivierung von Verbindungen von allen IP-Adressen in Azure-Rechenzentren in Erwägung ziehen.

> [!IMPORTANT]
> Die Option **Allow public access from Azure services and resources within Azure** (Öffentlichen Zugriff von Azure-Diensten und -Ressourcen aus gestatten) konfiguriert die Firewall so, dass alle von Azure ausgehenden Verbindungen zugelassen werden, einschließlich der Verbindungen aus Abonnements anderer Kunden. Stellen Sie bei Verwendung dieser Option sicher, dass Ihre Anmelde- und Benutzerberechtigungen den Zugriff auf autorisierte Benutzer beschränken.

### <a name="troubleshooting-public-access-issues"></a>Behandeln von Problemen mit dem öffentlichen Zugriff
Wenn der Zugriff auf den Azure Database for PostgreSQL-Dienst nicht das erwartete Verhalten aufweist, sind folgende Ursachen möglich:

* **Änderungen an der Positivliste sind noch nicht wirksam:** Änderungen an der Firewallkonfiguration des Azure Database for PostgreSQL-Servers werden möglicherweise erst nach fünf Minuten wirksam.

* **Fehler bei der Authentifizierung:** Wenn ein Benutzer nicht über die Berechtigungen für den Azure Database for PostgreSQL-Server verfügt oder das verwendete Kennwort falsch ist, wird die Verbindung mit dem Azure Database for PostgreSQL-Server verweigert. Durch das Erstellen einer Firewalleinstellung wird Clients lediglich die Möglichkeit gegeben, einen Verbindungsversuch zum Server zu unternehmen. Jeder Client muss weiterhin die erforderlichen Sicherheitsanmeldeinformationen bereitstellen.

* **Die dynamische Client-IP-Adresse verhindert den Zugriff:** Wenn Sie über eine Internetverbindung mit dynamischer IP-Adresszuweisung verfügen und Probleme beim Passieren der Firewall auftreten, probieren Sie eine der folgenden Lösungen aus:

   * Fragen Sie Ihren Internetdienstanbieter (ISP) nach dem IP-Adressbereich, der den Clientcomputern zugewiesen ist, mit denen auf den Azure Database for PostgreSQL-Server zugegriffen wird. Fügen Sie dann den IP-Adressbereich als Firewallregel hinzu.
   * Verwenden Sie stattdessen die statische IP-Adressierung für die Clientcomputer, und fügen Sie dann die statische IP-Adresse als Firewallregel hinzu.

* **Firewallregel ist nicht für das IPv6-Format verfügbar:** Firewallregeln müssen das IPv4-Format aufweisen. Wenn Sie Firewallregeln im IPv6-Format angeben, wird ein Validierungsfehler angezeigt.

## <a name="host-name"></a>Hostname

Unabhängig von Ihrer Netzwerkoption empfiehlt es sich, beim Herstellen einer Verbindung mit Ihrem flexiblen Server immer einen FQDN als Hostnamen zu verwenden. Es ist nicht gewährleistet, dass die IP-Adresse des Servers statisch bleibt. Mithilfe des FQDN können Sie verhindern, dass Änderungen an der Verbindungszeichenfolge vorgenommen werden. 

Ein Beispiel, bei dem ein FQDN als Hostname verwendet wird, ist `hostname = servername.postgres.database.azure.com`. Vermeiden Sie nach Möglichkeit die Verwendung von `hostname = 10.0.0.4` (eine private Adresse) oder `hostname = 40.2.45.67` (eine öffentliche Adresse).


## <a name="tls-and-ssl"></a>TLS und SSL

Azure Database for PostgreSQL – Flexibler Server erzwingt das Verbinden Ihrer Clientanwendungen mit dem PostgreSQL-Dienst per TLS (Transport Layer Security). TLS ist ein Standardprotokoll der Branche, das verschlüsselte Netzwerkverbindungen zwischen dem Datenbankserver und Clientanwendungen gewährleistet. Bei der TLS handelt es sich um eine aktualisierte Version von Secure Sockets Layer (SSL). 

Azure Database for PostgreSQL unterstützt TLS 1.2 und höher. In [RFC 8996](https://datatracker.ietf.org/doc/rfc8996/) gibt die Internet Engineering Task Force (IETF) explizit an, dass TLS 1.0 und TLS 1.1 nicht verwendet werden dürfen. Beide Protokolle wurden Ende 2019 als veraltet gekennzeichnet.

Alle eingehenden Verbindungen, die frühere Versionen des TLS-Protokolls verwenden (z. B. TLS 1.0 und TLS 1.1), werden verweigert.

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie, wie Sie einen flexiblen Server mit **privatem Zugriff (VNet-Integration)** über das [Azure-Portal](how-to-manage-virtual-network-portal.md) oder die [Azure-Befehlszeilenschnittstelle](how-to-manage-virtual-network-cli.md) erstellen.
* Erfahren Sie, wie Sie einen flexiblen Server mit **öffentlichem Zugriff (zulässige IP-Adressen)** über das [Azure-Portal](how-to-manage-firewall-portal.md) oder die [Azure-Befehlszeilenschnittstelle](how-to-manage-firewall-cli.md) erstellen.
