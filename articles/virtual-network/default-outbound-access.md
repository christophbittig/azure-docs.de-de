---
title: Standardzugriff in ausgehender Richtung in Azure
titleSuffix: Azure Virtual Network
description: Informationen zum Standardzugriff in ausgehender Richtung in Azure.
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: conceptual
ms.date: 07/13/2021
ms.author: allensu
ms.openlocfilehash: f4c06fb4c97ea38c494f18e5bf72e9eb015299a8
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/20/2021
ms.locfileid: "122515920"
---
# <a name="default-outbound-access-in-azure"></a>Standardzugriff in ausgehender Richtung in Azure

In Azure wird virtuellen Computern in einem virtuellen Netzwerk ohne explizite ausgehende Konnektivität eine standardmäßige öffentliche IP-Adresse in ausgehender Richtung zugewiesen. Diese IP-Adresse ermöglicht ausgehende Konnektivität zwischen den Ressourcen und dem Internet. Dieser Zugriff wird als Standardzugriff in ausgehender Richtung bezeichnet. 

Beispiele für explizite ausgehende Konnektivität sind virtuelle Computer:

* Wird in einem Subnetz erstellt, das einem NAT Gateway zugeordnet ist.
* Im Back-End-Pool eines Load Balancers "Standard" mit definierten Ausgangsregeln.
* Im Back-End-Pool des öffentlichen Load Balancers "Basic".
* Virtuelle Computer mit ihnen explizit zugeordneten öffentlichen IP-Adressen.

## <a name="how-is-default-outbound-access-provided"></a>Wie wird ausgehender Standardzugriff bereitgestellt?

Die öffentliche IPv4-Adresse, die für den Zugriff verwendet wird, wird als IP-Adresse für ausgehenden Standardzugriff bezeichnet. Diese IP-Adresse ist implizit und gehört Microsoft. Diese IP-Adresse kann sich ändern, und es wird nicht empfohlen, sie für Produktions-Workloads zu verwenden.

## <a name="when-is-default-outbound-access-provided"></a>Wann wird ausgehender Standardzugriff bereitgestellt?

Wenn Sie einen virtuellen Computer in Azure bereitstellen und er keine explizite ausgehende Konnektivität hat, wird ihm eine Standard-IP-Adresse für ausgehenden Zugriff zugewiesen.
## <a name="why-is-disabling-default-outbound-access-recommended"></a>Warum wird das Deaktivieren des ausgehenden Standardzugriffs empfohlen?

* Standardmäßig sicher
    
    * Es wird nicht empfohlen, ein virtuelles Netzwerk standardmäßig mithilfe des Null-Vertrauen-Netzwerksicherheitsprinzips für das Internet zu öffnen.

* Explizit vs. implizit

    * Es wird empfohlen, explizite Konnektivitätsmethoden anstelle impliziter Methoden zu verwenden, wenn Sie Zugriff auf Ressourcen in Ihrem virtuellen Netzwerk gewähren.

* Verlieren von IP-Adresse

    * Die Standardzugriff-IP-Adresse in ausgehender Richtung gehört nicht den Kunden. Änderungen dieser IP-Adresse sind vorbehalten.  Jede Abhängigkeit von dieser IP-Adresse kann in Zukunft Probleme verursachen.

## <a name="how-can-i-disable-default-outbound-access"></a>Wie kann ich den ausgehenden Standardzugriff deaktivieren?

Es gibt mehrere Möglichkeiten, den ausgehenden Standardzugriff zu deaktivieren:

1.  Hinzufügen einer expliziten ausgehenden Konnektivitätsmethode

    * Dem Subnetz Ihres virtuellen Computers ein NAT-Gateway zuordnen.

    * Einen Load Balancer Standard mit konfigurierten Ausgangsregeln zuordnen.

    * Der Netzwerkschnittstelle des virtuellen Computers eine öffentliche IP-Adresse zuordnen.

2.  Modus „Flexible Orchestrierung für VM-Skalierungsgruppen“ verwenden.

    * Flexible Skalierungen sind standardmäßig sicher. Keiner der Instanzen, die über flexible Skalierungsgruppen erstellt werden, ist die standardmäßige IP-Adresse für den ausgehenden Zugriff zugeordnet. Weitere Informationen finden Sie unter [Flexibler Orchestrierungsmodus für Skalierungsgruppen für virtuelle Computer](https://aka.ms/vmssflex/docs)

## <a name="if-i-need-outbound-access-what-is-the-recommended-way"></a>Was wird empfohlen, wenn ich ausgehenden Zugriff brauche?

NAT-Gateway ist der empfohlene Weg für explizite ausgehende Verbindungen. Es kann auch eine Firewall für diesen Zugriff verwendet werden.

## <a name="limitations"></a>Einschränkungen

* Konnektivität ist möglicherweise für Windows-Aktualisierungen erforderlich.
* Die IP-Adresse für Standardzugriff in ausgehender Richtung unterstützt keine fragmentierten Pakete. 

## <a name="next-steps"></a>Nächste Schritte

Sie können weitere Informationen zu ausgehenden Verbindungen in Azure und Azure Virtual Network NAT (NAT-Gateway) finden unter:

* [SNAT (Source Network Address Translation, Quellnetzwerkadress-Übersetzung) für ausgehende Verbindungen](../load-balancer/load-balancer-outbound-connections.md).

* [Was ist Azure Virtual Network NAT?](./nat-gateway/nat-overview.md)
