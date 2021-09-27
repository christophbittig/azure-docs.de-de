---
title: Netzwerksicherheit für Azure Service Bus
description: In diesem Artikel werden Netzwerksicherheitsfeatures wie Diensttags, IP-Firewallregeln, Dienstendpunkte und private Endpunkte beschrieben.
ms.topic: conceptual
ms.date: 09/20/2021
ms.openlocfilehash: db09ca367d228100eb344c63cca31047fd5e3624
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128575951"
---
# <a name="network-security-for-azure-service-bus"></a>Netzwerksicherheit für Azure Service Bus 
In diesem Artikel wird beschrieben, wie Sie die folgenden Sicherheitsfunktionen mit Azure Service Bus verwenden: 

- Diensttags
- IP-Firewallregeln
- Netzwerkdienstendpunkte
- Private Endpunkte


## <a name="service-tags"></a>Diensttags
Ein Diensttag steht für eine Gruppe von IP-Adresspräfixen eines bestimmten Azure-Diensts. Microsoft verwaltet die Adresspräfixe, für die das Diensttag gilt, und aktualisiert das Tag automatisch, wenn sich die Adressen ändern. Auf diese Weise wird die Komplexität häufiger Updates an Netzwerksicherheitsregeln minimiert. Weitere Informationen zu Diensttags finden Sie unter [Diensttags: Übersicht](../virtual-network/service-tags-overview.md).

Sie können Diensttags verwenden, um Netzwerkzugriffssteuerungen in [Netzwerksicherheitsgruppen](../virtual-network/network-security-groups-overview.md#security-rules) oder in der [Azure Firewall](../firewall/service-tags.md) zu definieren. Verwenden Sie Diensttags anstelle von spezifischen IP-Adressen, wenn Sie Sicherheitsregeln erstellen. Wenn Sie den Diensttagnamen (beispielsweise **ServiceBus**) im entsprechenden *Quell*- oder *Zielfeld* einer Regel angeben, können Sie den Datenverkehr für den entsprechenden Dienst zulassen oder verweigern.

| Diensttag | Zweck | Eingehend oder ausgehend möglich? | Regional möglich? | Einsatz mit Azure Firewall möglich? |
| --- | -------- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **ServiceBus** | Azure Service Bus-Datenverkehr, der die Dienstebene „Premium“ verwendet. | Ausgehend | Ja | Ja |


> [!NOTE]
> Sie können Diensttags nur für **Premium**-Namespaces verwenden. Wenn Sie einen **Standard**-Namespace verwenden, verwenden Sie die IP-Adresse, die beim Ausführen des folgenden Befehls angezeigt wird: `nslookup <host name for the namespace>`. Beispiel: `nslookup contosons.servicebus.windows.net`. 

## <a name="ip-firewall"></a>IP-Firewall 
Standardmäßig kann auf Service Bus-Namespaces über das Internet zugegriffen werden, solange die Anforderung eine gültige Authentifizierung und Autorisierung aufweist. Mit der IP-Firewall können Sie den Zugriff auf eine Gruppe von IPv4-Adressen oder IPv4-Adressbereichen in der [CIDR-Notation (Classless Inter-Domain Routing)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) weiter einschränken.

Diese Funktion ist in Szenarien hilfreich, in denen Azure Service Bus nur von bestimmten bekannten Websites aus zugänglich sein soll. Mithilfe von Firewallregeln können Sie Regeln konfigurieren, um Datenverkehr von bestimmten IPv4-Adressen zuzulassen. Wenn Sie z. B. Service Bus mit [Azure Express Route] [express-route] verwenden, können Sie eine **Firewallregel** erstellen, um Datenverkehr nur von den IP-Adressen Ihrer lokalen Infrastruktur oder von Adressen eines NAT-Gateways eines Unternehmen zuzulassen. 

Die IP-Firewallregeln werden auf der Service Bus-Namespaceebene angewendet. Daher gelten die Regeln für alle Clientverbindungen mit einem beliebigen unterstützten Protokoll. Jeder Verbindungsversuch über eine IP-Adresse, die nicht mit einer IP-Zulassungsregel im Service Bus-Namespace übereinstimmt, wird als nicht autorisiert abgelehnt. In der Antwort wird die IP-Regel nicht erwähnt. IP-Filterregeln werden der Reihe nach angewendet, und die erste Regel, die eine Übereinstimmung mit der IP-Adresse ergibt, bestimmt die Aktion (Zulassen oder Ablehnen).

Weitere Informationen finden Sie unter [Konfigurieren einer IP-Firewall für einen Service Bus-Namespace](service-bus-ip-filtering.md).

## <a name="network-service-endpoints"></a>Netzwerkdienstendpunkte
Die Integration von Service Bus mit [VNET-Dienstendpunkten](service-bus-service-endpoints.md) ermöglicht den sicheren Zugriff auf Messagingfunktionen für Workloads, z. B. an virtuelle Netzwerke (VNETs) gebundene virtuelle Computer. Der Pfad für den Netzwerkdatenverkehr ist dabei an beiden Enden geschützt.

Nachdem die Konfiguration der Bindung an mindestens einen Dienstendpunkt des VNET-Subnetzes durchgeführt wurde, akzeptiert der entsprechende Service Bus-Namespace nur noch Datenverkehr von autorisierten virtuellen Netzwerken. Aus Sicht des virtuellen Netzwerks wird durch die Bindung eines Service Bus-Namespace an einen Dienstendpunkt ein isolierter Netzwerktunnel vom Subnetz des virtuellen Netzwerks zum Messagingdienst konfiguriert.

Das Ergebnis ist eine private und isolierte Beziehung zwischen den Workloads, die an das Subnetz gebunden sind, und dem entsprechenden Service Bus-Namespace, obwohl sich die beobachtbare Netzwerkadresse des Messaging-Dienstendpunkts in einem öffentlichen IP-Bereich befindet.

> [!IMPORTANT]
> Virtuelle Netzwerke werden nur in Service Bus-Namespaces im [Tarif Premium](service-bus-premium-messaging.md) unterstützt.
> 
> Bei der Verwendung von VNET-Dienstendpunkten mit Service Bus sollten Sie diese Endpunkte nicht in Anwendungen aktivieren, in denen Service Bus-Namespaces der Tarife Standard und Premium gemischt werden. Die Tarif Standard unterstützt keine VNETs. Der Endpunkt ist nur auf Namespaces im Premium-Tarif beschränkt.

### <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>Erweiterte Sicherheitsszenarien basierend auf der VNET-Integration 

Lösungen, für die eine strikte und auf mehrere Bereiche aufgeteilte Sicherheit erforderlich ist und bei denen die VNET-Subnetze die Segmentierung zwischen den einzelnen aufgeteilten Diensten bereitstellen, benötigen im Allgemeinen weiterhin Kommunikationspfade zwischen den Diensten, die sich in diesen Bereichen befinden.

Für alle direkten IP-Routen zwischen den Bereichen (auch für HTTPS per TCP/IP) besteht die Gefahr, dass ab der Vermittlungsschicht Sicherheitsrisiken ausgenutzt werden. Bei Messagingdiensten werden vollständig isolierte Kommunikationspfade bereitgestellt, für die Nachrichten während des Übergangs zwischen Parteien sogar auf Datenträger geschrieben werden. Workloads in zwei einzelnen virtuellen Netzwerken, die beide an dieselbe Service Bus-Instanz gebunden sind, können über Nachrichten effizient und zuverlässig kommunizieren, während die Integrität der jeweiligen Netzwerkisolationsgrenze aufrechterhalten wird.
 
Dies bedeutet, dass Ihre sicherheitsrelevanten Cloudlösungen nicht nur Zugriff auf zuverlässige und skalierbare Azure-Funktionen für asynchrones Messaging eines Branchenführers haben, sondern dass das Messaging jetzt auch genutzt werden kann, um Kommunikationspfade zwischen sicheren Lösungsbereichen zu erstellen. Diese sind deutlich sicherer als alle Optionen des Peer-to-Peer-Kommunikationsmodus, einschließlich HTTPS und andere per TLS geschützte Socketprotokolle.

### <a name="bind-service-bus-to-virtual-networks"></a>Binden von Service Bus an virtuelle Netzwerke

*VNET-Regeln* sind das Feature für die Firewallsicherheit, mit dem gesteuert wird, ob Ihr Azure Service Bus-Server Verbindungen eines bestimmten VNET-Subnetzes akzeptiert.

Das Binden eines Service Bus-Namespace an ein virtuelles Netzwerk ist ein Prozess mit zwei Schritten. Zunächst müssen Sie einen **VNET-Dienstendpunkt** in einem Virtual Network-Subnetz erstellen und für **Microsoft.ServiceBus** aktivieren, wie unter [Übersicht über Dienstendpunkte](service-bus-service-endpoints.md) beschrieben. Nachdem Sie den Dienstendpunkt hinzugefügt haben, binden Sie den Service Bus-Namespace mit einer **VNET-Regel** daran.

Die VNET-Regel ist eine Zuordnung des Service Bus-Namespace zu einem Subnetz eines virtuellen Netzwerks. Während die Regel vorhanden ist, wird allen Workloads, die an das Subnetz gebunden sind, Zugriff auf den Service Bus-Namespace gewährt. Service Bus stellt selbst niemals ausgehende Verbindungen her, muss keinen Zugriff erhalten und erhält daher niemals die Gewährung des Zugriffs auf Ihr Subnetz, indem diese Regel aktiviert wird.

Weitere Informationen finden Sie unter [Konfigurieren von Dienstendpunkten eines virtuellen Netzwerks für einen Service Bus-Namespace](service-bus-service-endpoints.md).

## <a name="private-endpoints"></a>Private Endpunkte

Mit Azure Private Link können Sie über einen **privaten Endpunkt** in Ihrem virtuellen Netzwerk auf Azure-Dienste wie Azure Service Bus, Azure Storage und Azure Cosmos DB sowie auf in Azure gehostete Kunden-/Partnerdienste zugreifen.

Ein privater Endpunkt ist eine Netzwerkschnittstelle, die Sie privat und sicher mit einem von Azure Private Link betriebenen Dienst verbindet. Der private Endpunkt verwendet eine private IP-Adresse aus Ihrem VNET und bindet den Dienst dadurch in Ihr VNET ein. Der gesamte für den Dienst bestimmte Datenverkehr kann über den privaten Endpunkt geleitet werden. Es sind also keine Gateways, NAT-Geräte, ExpressRoute-/VPN-Verbindungen oder öffentlichen IP-Adressen erforderlich. Der Datenverkehr zwischen Ihrem virtuellen Netzwerk und dem Dienst wird über das Microsoft-Backbone-Netzwerk übertragen und dadurch vom öffentlichen Internet isoliert. Sie können eine Verbindung mit einer Instanz einer Azure-Ressource herstellen, was ein Höchstmaß an Granularität bei der Zugriffssteuerung ermöglicht.

Weitere Informationen finden Sie unter [Was ist Azure Private Link?](../private-link/private-link-overview.md).

> [!NOTE]
> Dieses Feature wird mit dem Tarif **Premium** von Azure Service Bus unterstützt. Weitere Informationen zum Premium-Tarif finden Sie im Artikel [Service Bus Premium- und Standard-Tarif für Messaging](service-bus-premium-messaging.md).


Weitere Informationen finden Sie unter [Konfigurieren privater Endpunkte für einen Service Bus-Namespace](private-link-service.md).


## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie in folgenden Artikeln:

- [Konfigurieren einer IP-Firewall für einen Service Bus-Namespace](service-bus-ip-filtering.md)
- [Konfigurieren von Dienstendpunkten eines virtuellen Netzwerks für einen Service Bus-Namespace](service-bus-service-endpoints.md)
- [Konfigurieren privater Endpunkte für einen Service Bus-Namespace](private-link-service.md)