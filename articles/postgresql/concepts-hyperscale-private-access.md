---
title: Privater Zugriff (Vorschau) – Hyperscale (Citus) – Azure Database for PostgreSQL
description: In diesem Artikel wird der private Zugriff für Azure Database for PostgreSQL – Hyperscale (Citus) beschrieben.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 10/15/2021
ms.openlocfilehash: 8ddefc9ee135b897dc866826208f50f7f9ad21ed
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131084386"
---
# <a name="private-access-preview-in-azure-database-for-postgresql---hyperscale-citus"></a>Privater Zugriff (Vorschau) in Azure Database for PostgreSQL – Hyperscale (Citus)

[!INCLUDE [azure-postgresql-hyperscale-access](../../includes/azure-postgresql-hyperscale-access.md)]

Auf dieser Seite wird die Option des privaten Zugriffs beschrieben. Informationen zum öffentlichen Zugriff finden Sie [hier](concepts-hyperscale-firewall-rules.md).

> [!NOTE]
>
> Privater Zugriff ist nur in [bestimmten Regionen](concepts-hyperscale-limits.md#regions) als Vorschau verfügbar.
>
> Wenn die Option „Privater Zugriff“ für Ihre Servergruppe nicht ausgewählt werden kann, obwohl sich Ihre Servergruppe in einer zulässigen Region befindet, öffnen Sie bitte eine [Azure-Supportanfrage](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest), und geben Sie Ihre Azure-Abonnement-ID ein, um Zugriff zu erhalten.

## <a name="definitions"></a>Definitionen

**Virtuelles Netzwerk**. Ein Azure Virtual Network (VNET) ist der grundlegende Baustein für private Netzwerke in Azure. Virtuelle Netzwerke ermöglichen vielen Azure-Ressourcentypen (beispielsweise Datenbankservern und Azure-VMs) die sichere Kommunikation miteinander. Virtuelle Netzwerke unterstützen On-Premise-Verbindungen, ermöglichen die Interaktion von Hosts in mehreren virtuellen Netzwerken durch Peering und bieten zusätzliche Vorteile wie Skalierbarkeit, Sicherheitsoptionen und Isolierung. Jeder private Endpunkt für eine Hyperscale (Citus)-Servergruppe erfordert ein zugeordnetes virtuelles Netzwerk.

**Subnetz**. Subnetze segmentieren ein virtuelles Netzwerk in ein oder mehrere Subnetzwerke.
Jedes Subnetzwerk erhält einen Teil des Adressraums, wodurch die Effizienz der Adresszuordnung verbessert wird.  Sie können Ressourcen in Subnetzen mit Netzwerksicherheitsgruppen sichern. Weitere Informationen finden Sie unter Netzwerksicherheitsgruppen.

Wenn Sie ein Subnetz für den privaten Endpunkt eines Hyperscale (Citus) auswählen, stellen Sie sicher, dass in diesem Subnetz genügend private IP-Adressen für Ihre aktuellen und zukünftigen Anforderungen verfügbar sind.

**Privater Endpunkt**. Ein privater Endpunkt ist eine Netzwerkschnittstelle, die eine private IP-Adresse aus einem virtuellen Netzwerk verwendet. Diese Netzwerkschnittstelle stellt eine private und sichere Verbindung zu einem von Azure Private Link unterstützten Dienst her. Private Endpunkte binden die Dienste in Ihr virtuelles Netzwerk ein.

Wenn Sie den privaten Zugriff für Hyperscale (Citus) aktivieren, wird ein privater Endpunkt für den Koordinatorknoten der Servergruppe erstellt. Der Endpunkt erlaubt Hosts im ausgewählten virtuellen Netzwerk den Zugriff auf den Koordinator. Optional können Sie auch private Endpunkte für Workerknoten erstellen.

**Private DNS-Zone**. Eine private Azure DNS-Zone löst Hostnamen innerhalb eines verknüpften virtuellen Netzwerks und innerhalb eines virtuellen Peernetzwerks auf. Domäneneinträge für Hyperscale (Citus)-Knoten werden in einer privaten DNS-Zone erstellt, die für ihre Servergruppe ausgewählt ist.  Achten Sie darauf, vollqualifizierte Domänennamen (FQDN) für PostgreSQL-Verbindungszeichenfolgen von Knoten zu verwenden.

## <a name="private-link"></a>Private Link

Sie können [private Endpunkte](../private-link/private-endpoint-overview.md) für Ihre Hyperscale (Citus)-Servergruppen verwenden, um Hosts in einem virtuellen Netzwerk (VNET) den sicheren Zugriff auf Daten über [Private Link](../private-link/private-link-overview.md) zu erlauben.

Der private Endpunkt der Servergruppe verwendet eine IP-Adresse aus dem Adressraum des virtuellen Netzwerks. Der Datenverkehr zwischen Hosts im virtuellen Netzwerk und Hyperscale (Citus)-Knoten wird über eine private Verbindung im Microsoft-Backbonenetzwerk übertragen, und ist damit nicht dem öffentlichen Internet ausgesetzt.

Anwendungen im virtuellen Netzwerk können eine nahtlose Verbindung mit den Hyperscale (Citus)-Knoten über den privaten Endpunkt herstellen, indem die gleichen Verbindungszeichenfolgen und Autorisierungsmechanismen wie üblich verwendet werden.

Sie können den privaten Zugriff bei der Erstellung der Hyperscale (Citus)-Servergruppe auswählen und jederzeit vom öffentlichen Zugriff zu privatem Zugriff wechseln.

### <a name="using-a-private-dns-zone"></a>Verwenden einer privaten DNS-Zone

Für jeden privaten Endpunkt wird automatisch eine neue private DNS-Zone bereitgestellt, es sei denn, Sie wählen eine der privaten DNS-Zonen aus, die zuvor von Hyperscale (Citus) erstellt wurden. Weitere Informationen finden Sie in der [Übersicht über private DNS-Zonen](../dns/private-dns-overview.md).

Hyperscale (Citus) erstellt DNS-Einträge wie `c.privatelink.mygroup01.postgres.database.azure.com` in der ausgewählten privaten DNS-Zone für jeden Knoten mit einem privaten Endpunkt. Wenn Sie von einem virtuellen Azure VM aus über einen privaten Endpunkt eine Verbindung mit einem Hyperscale (Citus)-Knoten herstellen, löst Azure DNS den FQDN des Knotens in eine private IP-Adresse auf.

Die Einstellungen für private DNS-Zonen und das Peering virtueller Netzwerke sind voneinander unabhängig. Wenn Sie von einem Client, der in einem anderen VNet (derselben Region oder einer anderen Region) bereitgestellt wurde, eine Verbindung zu einem Knoten in der Servergruppe herstellen möchten, müssen Sie die private DNS-Zone mit dem VNet verknüpfen. Weitere Informationen finden Sie unter [Verknüpfen des virtuellen Netzwerks](../dns/private-dns-getstarted-portal.md#link-the-virtual-network).

> [!NOTE]
>
> Der Dienst erstellt auch immer öffentliche CNAME-Einträge wie `c.mygroup01.postgres.database.azure.com` für jeden Knoten. Ausgewählte Computer im öffentlichen Internet können jedoch nur dann eine Verbindung mit dem öffentlichen Hostnamen herstellen, wenn der Datenbankadministrator den [öffentlichen Zugriff](concepts-hyperscale-firewall-rules.md) auf die Servergruppe ermöglicht.

Wenn Sie einen benutzerdefinierten DNS-Server verwenden, müssen Sie eine DNS-Weiterleitung verwenden, um den FQDN der Hyperscale (Citus)-Knoten aufzulösen. Die IP-Adresse der Weiterleitung sollte 168.63.129.16 sein. Der benutzerdefinierte DNS-Server sollte sich innerhalb des VNet befinden oder über die DNS-Servereinstellung des VNet erreichbar sein. Weitere Informationen finden Sie unter [Namensauflösung mithilfe eines eigenen DNS-Servers](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

### <a name="recommendations"></a>Empfehlungen

Beim Aktivieren des privaten Zugriffs für Ihre Hyperscale (Citus)-Servergruppe sollten Sie Folgendes berücksichtigen:

* **Subnetzgröße**: Berücksichtigen Sie bei der Auswahl der Subnetzgröße für die Hyperscale (Citus)-Servergruppe die aktuellen Anforderungen, z. B. IP-Adressen für Koordinator oder alle Knoten in dieser Servergruppe, sowie zukünftige Anforderungen wie die Vergrößerung dieser Servergruppe. Stellen Sie sicher, dass Sie über genügend private IP-Adressen für die aktuellen und zukünftigen Anforderungen verfügen. Beachten Sie, dass in Azure in jedem Subnetz fünf IP-Adressen reserviert sind.
  Ausführlichere Informationen finden Sie [in diesen FAQ](../virtual-network/virtual-networks-faq.md#configuration).
* **Private DNS-Zone**: DNS-Einträge mit privaten IP-Adressen werden vom Hyperscale (Citus) verwaltet. Stellen Sie sicher, dass Sie keine private DNS-Zone löschen, die für Hyperscale (Citus)-Servergruppen verwendet wird.

## <a name="limits-and-limitations"></a>Grenzwerte und Einschränkungen

Weitere Informationen finden Sie unter Hyperscale (Citus) – [Grenzwerte und Einschränkungen](concepts-hyperscale-limits.md).

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie, wie Sie [privaten Zugriff aktivieren und verwalten](howto-hyperscale-private-access.md) (Vorschau)
* Folgen Sie einem [Tutorial](tutorial-hyperscale-private-access.md), um den privaten Zugriff (Vorschau) in Aktion zu sehen.
* Erfahren Sie mehr über [private Endpunkte](../private-link/private-endpoint-overview.md)
* Informieren Sie sich ausführlicher über [virtuelle Netzwerke](../virtual-network/concepts-and-best-practices.md).
* Erfahren Sie mehr über [private DNS-Zonen](../dns/private-dns-overview.md).