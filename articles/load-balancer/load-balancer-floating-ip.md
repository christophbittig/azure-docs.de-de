---
title: Konfiguration der unverankerten IP von Azure Load Balancer
description: Übersicht über die unverankerte IP von Azure Load Balancer
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/13/2020
ms.author: allensu
ms.openlocfilehash: a72eceba85e14e058fa7c8ba2b109009e46e6df3
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2021
ms.locfileid: "129357449"
---
# <a name="azure-load-balancer-floating-ip-configuration"></a>Konfiguration der unverankerten IP von Azure Load Balancer

Load Balancer verfügt über mehrere Funktionen für UDP- und TCP-Anwendungen.

## <a name="floating-ip"></a>Unverankerte IP

In einigen Anwendungsszenarien ist es eine Priorität bzw. Anforderung, den gleichen Port für mehrere Anwendungsinstanzen auf einer einzelnen VM im Back-End-Pool zu verwenden. Häufige Beispiele für die Wiederverwendung von Ports sind: 
- Clustering für Hochverfügbarkeit
- Virtuelle Netzwerkgeräte
- Verfügbarmachen mehrerer TLS-Endpunkte ohne erneute Verschlüsselung. 

Wenn Sie den Back-End-Port in mehreren Regeln wiederverwenden möchten, müssen Sie in der Regeldefinition Floating IP aktivieren.

Wenn Floating IP aktiviert ist, ändert Azure die Zuordnung der IP-Adressen in die Front-End-IP-Adresse des Load Balancer-Front-End anstelle der IP-Adresse der Back-End-Instanz. 

Ohne Floating IP werden von Azure die IP-Adressen der VM-Instanzen verfügbar gemacht. Durch die Aktivierung von Floating IP wird die Zuordnung der IP-Adressen in die Front-End-IP-Adresse des Lastenausgleichsmoduls geändert, um für zusätzliche Flexibilität zu sorgen. [Hier](load-balancer-multivip-overview.md)erhalten Sie weitere Informationen.

## <a name="limitations"></a><a name = "limitations"></a>Einschränkungen

- Unverankerte IP wird für sekundäre IP-Konfigurationen in Szenarien mit Lastenausgleich derzeit nicht unterstützt.

## <a name="next-steps"></a>Nächste Schritte

- Informationen zu den ersten Schritten mit einer Load Balancer-Instanz finden Sie unter [Schnellstart: Erstellen einer Load Balancer Standard-Instanz für den Lastenausgleich virtueller Computer im Azure-Portal](quickstart-load-balancer-standard-public-portal.md).
- Informieren Sie sich über [Azure Load Balancer – Ausgehende Verbindungen](load-balancer-outbound-connections.md).
- Weitere Informationen zu [Azure Load Balancer](load-balancer-overview.md).
- Informationen zu [Integritätstests](load-balancer-custom-probe-overview.md)
- Weitere Informationen zu [Diagnosen für Standard Load Balancer](load-balancer-standard-diagnostics.md).
- Weitere Informationen zu [Netzwerksicherheitsgruppen](../virtual-network/network-security-groups-overview.md).
