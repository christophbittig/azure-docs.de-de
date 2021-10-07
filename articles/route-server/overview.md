---
title: Was ist Azure Route Server?
description: Hier erfahren Sie, wie Azure Route Server das Routing zwischen Ihrem virtuellen Netzwerkgerät (Network Virtual Appliance, NVA) und Ihrem virtuellen Netzwerk vereinfachen kann.
services: route-server
author: duongau
ms.service: route-server
ms.topic: overview
ms.date: 09/27/2021
ms.author: duau
ms.openlocfilehash: 34835f8f3e536b875c7eb4afd176ab00fb1f417c
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/28/2021
ms.locfileid: "129091424"
---
# <a name="what-is-azure-route-server"></a>Was ist Azure Route Server? 

Azure Route Server vereinfacht das dynamische Routing zwischen Ihrem virtuellen Netzwerkgerät (Network Virtual Appliance, NVA) und Ihrem virtuellen Netzwerk. Sie können Routinginformationen direkt über das Border Gateway Protocol-Routingprotokoll (BGP-Routingprotokoll) zwischen jedem NVA, das das BGP-Routingprotokoll unterstützt, und dem softwaredefinierten Azure-Netzwerk (Software Defined Network, SDN) in Azure Virtual Network (VNet) austauschen, ohne Routingtabellen manuell konfigurieren oder verwalten zu müssen. Azure Route Server ist ein vollständig verwalteter Dienst, der mit Hochverfügbarkeit konfiguriert ist.

> [!IMPORTANT]
> Wenn Sie vor dem 1. September eine Azure Route Server-Instanz erstellt haben und ihr keine öffentliche IP-Adresse zugeordnet ist, müssen Sie die Route Server-Instanz neu erstellen, damit sie zu Verwaltungszwecken eine IP-Adresse abrufen kann.

## <a name="how-does-it-work"></a>Wie funktioniert dies?

Im folgenden Diagramm wird die Funktionsweise und Verwendung von Azure Route Server mit einem SDWAN-NVA und einem Sicherheits-NVA in einem virtuellen Netzwerk dargestellt. Nachdem Sie das BGP-Peering eingerichtet haben, empfängt Azure Route Server eine lokale Route (10.250.0.0/16) vom SDWAN-Gerät und eine Standardroute (0.0.0.0/0) von der Firewall. Diese Routen werden dann automatisch auf den virtuellen Computern (Virtual Machines, VMs) im virtuellen Netzwerk konfiguriert. Folglich wird der gesamte an das lokale Netzwerk gerichtete Datenverkehr an das SDWAN-Gerät gesendet. Und der gesamte Datenverkehr in das Internet wird an die Firewall gesendet. In umgekehrter Richtung sendet Azure Route Server die Adresse des virtuellen Netzwerks (10.1.0.0/16) an beide NVAs. Das SDWAN-Gerät kann sie an das lokale Netzwerk weitergeben.

![Diagramm, in dem die Konfiguration von Azure Route Server in einem virtuellen Netzwerk dargestellt ist.](./media/overview/route-server-overview.png)

## <a name="key-benefits"></a>Hauptvorteile 

Azure Route Server vereinfacht die Konfiguration, Verwaltung und Bereitstellung Ihres NVAs in Ihrem virtuellen Netzwerk.  

* Sie müssen die Routingtabelle auf Ihrem NVA nicht mehr manuell aktualisieren, wenn die Adresse Ihres virtuellen Netzwerks aktualisiert wird. 

* Sie müssen [benutzerdefinierte Routen](../virtual-network/virtual-networks-udr-overview.md) nicht mehr manuell aktualisieren, wenn Ihr NVA neue Routen ankündigt oder alte Routen zurückzieht. 

* Sie können mehrere Instanzen Ihres NVA mittels Peering mit Azure Route Peering verbinden. Sie können die BGP-Attribute auf Ihrem NVA konfigurieren und in Abhängigkeit von Ihrem Entwurf (z. B. Aktiv/Aktiv für Leistung oder Aktiv/Passiv für Resilienz) Azure Route Server mitteilen, welche NVA-Instanz aktiv oder passiv ist. 

* Die Schnittstelle zwischen dem NVA und Azure Route Server basiert auf einem allgemeinen Standardprotokoll. Solange Ihr NVA BGP unterstützt, können Sie es mittels Peering mit Azure Route Server verbinden. Weitere Informationen finden Sie unter [Von Route Server unterstützte Routingprotokolle](route-server-faq.md#protocol).

* Sie können Azure Route Server in jedem neuen oder vorhandenen virtuellen Netzwerk bereitstellen. 

## <a name="faq"></a>Häufig gestellte Fragen

Häufig gestellte Fragen zu Azure Route Server finden Sie unter [Azure Route Server: Häufig gestellte Fragen](route-server-faq.md).

## <a name="next-steps"></a>Nächste Schritte

- [Schnellstart: Erstellen und Konfigurieren einer Route Server-Instanz mithilfe von Azure PowerShell](quickstart-configure-route-server-powershell.md)
- [Erfahren Sie, wie Azure Route Server mit Azure ExpressRoute und Azure VPN verwendet wird](expressroute-vpn-support.md).
