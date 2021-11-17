---
title: Azure-VMware-Lösung – DNS-Weiterleitung von der privaten Cloud zu lokalen Standorten
description: Hier wird beschrieben, wie Sie Ihren Private CloudSimple-Cloud-DNS-Server in die Lage versetzen, die Suche nach lokalen Ressourcen weiterzuleiten.
author: suzizuber
ms.author: v-szuber
ms.date: 02/29/2020
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 96810b4653cc6ce2bff92e1fa8455d39f6f13fda
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132322092"
---
# <a name="enable-cloudsimple-private-cloud-dns-servers-to-forward-dns-lookup-of-on-premises-resources-to-your-dns-servers"></a>Ermöglichen Sie es Private CloudSimple-Cloud-DNS-Servern, die DNS-Suche von lokalen Ressourcen an Ihre DNS-Server weiterzuleiten.

DNS-Server in privaten Clouds können die DNS-Suche nach beliebigen lokalen Ressourcen an Ihre DNS-Server weiterleiten.  Die Aktivierung der Suche ermöglicht es den vSphere-Komponenten der privaten Cloud, alle in Ihrer lokalen Umgebung ausgeführten Dienste zu suchen und mit ihnen über vollqualifizierte Domänennamen (FQDN) zu kommunizieren.

## <a name="scenarios"></a>Szenarien 

Die Weiterleitung der DNS-Suche für Ihren lokalen DNS-Server ermöglicht es Ihnen, Ihre private Cloud für die folgenden Szenarien zu nutzen:

* Verwenden der privaten Cloud als Setup für die Notfallwiederherstellung für Ihre lokale VMware-Lösung
* Verwenden des lokalen Active Directory als Identitätsquelle für Ihre vSphere-Instanz der privaten Cloud
* Verwenden von HCX für die Migration virtueller Computer von lokalen Standorten in die private Cloud

## <a name="before-you-begin"></a>Voraussetzungen

Damit die DNS-Weiterleitung funktioniert, muss eine Netzwerkverbindung von Ihrem privaten Cloudnetzwerk zu Ihrem lokalen Netzwerk bestehen.  Sie können die Netzwerkverbindung folgendermaßen einrichten:

* [Herstellen einer Verbindung aus der lokalen Umgebung mit CloudSimple mithilfe von ExpressRoute](on-premises-connection.md)
* [Einrichten eines Site-to-Site-VPN-Gateways](./vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)

Damit die DNS-Weiterleitung funktioniert, müssen an dieser Verbindung Firewallports geöffnet werden.  Die verwendeten Ports sind TCP-Port 53 oder UDP-Port 53.

> [!NOTE]
> Wenn Sie Site-to-Site-VPN verwenden, muss Ihr lokales DNS-Serversubnetz als Teil der lokalen Präfixe hinzugefügt werden.

## <a name="request-dns-forwarding-from-private-cloud-to-on-premises"></a>Anfordern der DNS-Weiterleitung von der privaten Cloud zu lokalen Standorten

Zum Aktivieren der DNS-Weiterleitung von der privaten Cloud zu lokalen Standorten übermitteln Sie eine [Supportanfrage](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) mit den folgenden Informationen.

* Problemtyp: **Technisch**
* Abonnement: **Wählen Sie das Abonnement aus, in dem der CloudSimple-Dienst bereitgestellt wird.**
* Dienst: **VMware Solution by CloudSimple**
* Problemtyp: **Ratgeber oder Fragen**.
* Problemuntertyp: **Benötige Hilfe bei NW**
* Geben Sie den Domänennamen Ihrer lokalen Domäne im Detailbereich an.
* Stellen Sie im Detailbereich die Liste Ihrer lokalen DNS-Server bereit, an die die Suche von Ihrer privaten Cloud weitergeleitet wird.

## <a name="next-steps"></a>Nächste Schritte

* [Weitere Informationen zur Konfiguration einer lokalen Firewall](on-premises-firewall-configuration.md)
* [Lokale DNS-Serverkonfiguration](on-premises-dns-setup.md)
