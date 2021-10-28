---
title: Schützen von öffentlichen Azure SQL Managed Instance-Endpunkten
description: Hier erfahren Sie, wie Sie öffentliche Endpunkte in Azure SQL Managed Instance sicher verwenden.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: sqldbrb=1
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto, sstein
ms.date: 05/08/2019
ms.openlocfilehash: 2718816adfeaa8fa98c93ea1ed394c6634b5cb7b
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130250443"
---
# <a name="use-azure-sql-managed-instance-securely-with-public-endpoints"></a>Sicheres Verwenden von Azure SQL Managed Instance mit öffentlichen Endpunkten
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL Managed Instance kann Benutzerkonnektivität über [öffentliche Endpunkte](public-endpoint-configure.md) bereitstellen. In diesem Artikel wird erläutert, wie Sie diese Konfiguration sicherer machen.

## <a name="scenarios"></a>Szenarien

Azure SQL Managed Instance bietet einen privaten Endpunkt, um Konnektivität aus dem virtuellen Netzwerk heraus zu ermöglichen. Die Standardoption ist das Bereitstellen maximaler Isolation. Es gibt jedoch Szenarien, in denen Sie eine Verbindung mit einem öffentlichen Endpunkt bereitstellen müssen:

- Die verwaltete Instanz muss in nur mehrinstanzenfähige Platform-as-a-Service-Angebote (PaaS) integriert werden.
- Sie benötigen einen höheren Durchsatz des Datenaustauschs, als mit Verwendung eines VPN möglich ist.
- Verbot von PaaS innerhalb der Unternehmensnetzwerke durch Unternehmensrichtlinien

## <a name="deploy-a-managed-instance-for-public-endpoint-access"></a>Bereitstellen einer verwalteten Instanz für den Zugriff über den öffentlichen Endpunkt

Auch wenn dies nicht unbedingt erforderlich ist, werden verwaltete Instanzen mit Zugriff auf einen öffentlichen Endpunkt meist in einem dedizierten isolierten virtuellen Netzwerk erstellt. In dieser Konfiguration wird das virtuelle Netzwerk nur zur Isolation des virtuellen Clusters verwendet. Es spielt keine Rolle, wenn der IP-Adressraum der verwalteten Instanz sich mit dem IP-Adressraum eines Unternehmensnetzwerks überschneidet.

## <a name="secure-data-in-motion"></a>Sichern von Daten während der Übertragung

Der Datenverkehr von SQL Managed Instance wird immer verschlüsselt, wenn der Clienttreiber die Verschlüsselung unterstützt. Datenübertragungen zwischen der verwalteten Instanz und anderen virtuellen Computern in Azure oder Azure-Diensten bleibt immer auf dem Backbone von Azure. Wenn eine Verbindung zwischen der verwalteten Instanz und einem lokalen Netzwerk besteht, empfiehlt sich die Verwendung von Azure ExpressRoute. Mit ExpressRoute vermeiden Sie das Verschieben von Daten über das öffentliche Internet. Für die private Konnektivität der verwalteten Instanz kann nur privates Peering verwendet werden.

## <a name="lock-down-inbound-and-outbound-connectivity"></a>Sperren eingehender und ausgehender Konnektivität

Das folgende Diagramm zeigt die empfohlenen Sicherheitskonfigurationen:

![Sicherheitskonfigurationen für das Sperren eingehender und ausgehender Konnektivität](./media/public-endpoint-overview/managed-instance-vnet.png)

Eine verwaltete Instanz verfügt über eine öffentliche Endpunktadresse, die für einen Kunden dediziert ist. Dieser Endpunkt teilt die IP-Adresse mit dem [Verwaltungsendpunkt](management-endpoint-find-ip-address.md), verwendet jedoch einen anderen Port. Legen Sie für die clientseitige ausgehende Firewall und in den Netzwerksicherheitsgruppen-Regeln diese IP-Adresse des öffentlichen Endpunkts fest, um ausgehende Konnektivität zu beschränken.

Um sicherzustellen, dass Datenverkehr zur verwalteten Instanz aus vertrauenswürdigen Quellen stammt, sollten nur Verbindungen mit Quellen mit bekannten IP-Adressen hergestellt werden. Verwenden Sie eine Netzwerksicherheitsgruppe, um den Zugriff auf den öffentlichen Endpunkt der verwalteten Instanz auf Port 3342 zu beschränken.

Wenn Clients eine Verbindung von einem lokalen Netzwerk herstellen müssen, stellen Sie sicher, dass die ursprüngliche Adresse in eine Gruppe bekannter IP-Adressen übersetzt wird. Wenn das nicht möglich ist (eine mobile Belegschaft ist z.B. ein typisches Szenario), sollten Sie [Point-to-Site-VPN-Verbindungen und einen privaten Endpunkt](point-to-site-p2s-configure.md) verwenden.

Wenn Verbindungen von Azure aus gestartet werden, sollte der Datenverkehr von einer gut bekannten zugewiesenen [virtuellen IP-Adresse](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip) (z.B. einem virtuellen Computer) kommen. Um die Verwaltung virtueller IP-Adressen (VIP) zu vereinfachen, sollten Sie [öffentliche IP-Adresspräfixe](../../virtual-network/ip-services/public-ip-address-prefix.md) verwenden.

## <a name="next-steps"></a>Nächste Schritte

- Informationen zum Konfigurieren des öffentlichen Endpunkts für verwaltete Instanzen: [Konfigurieren des öffentlichen Endpunkts](public-endpoint-configure.md)