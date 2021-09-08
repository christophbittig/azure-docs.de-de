---
title: Datei einfügen
description: Datei einfügen
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: include
ms.date: 10/29/2019
ms.author: jaredro
ms.custom: include file
ms.openlocfilehash: 02def8321ce5df33fb89ca8d9f6c24167c15bbb6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122346387"
---
### <a name="what-is-expressroute-direct"></a>Was ist ExpressRoute Direct?

ExpressRoute Direct bietet Kunden die Möglichkeit, sich direkt mit dem globalen Netzwerk von Microsoft zu verbinden, und zwar an strategisch über die ganze Welt verteilten Peeringstandorten. ExpressRoute Direct bietet duale Konnektivität mit 100 oder 10 GBit/s, die eine Aktiv/Aktiv-Konnektivität nach Bedarf unterstützt. 

### <a name="how-do-customers-connect-to-expressroute-direct"></a>Wie stellen Kunden eine Verbindung mit ExpressRoute Direct her? 

Kunden müssen mit ihren lokalen Carriern und Co-Location-Anbietern zusammenarbeiten, um eine Verbindung mit ExpressRoute-Routern herzustellen, um die Vorteile von ExpressRoute Direct nutzen zu können.

### <a name="what-locations-currently-support-expressroute-direct"></a>Welche Standorte unterstützen derzeit ExpressRoute Direct? 

Überprüfen Sie die Verfügbarkeit auf der [Seite mit Standorten](../articles/expressroute/expressroute-locations-providers.md). 

### <a name="what-is-the-sla-for-expressroute-direct"></a>Welche SLA gilt für ExpressRoute Direct?

ExpressRoute Direct verwendet die gleiche [Unternehmensstufe wie ExpressRoute](https://azure.microsoft.com/support/legal/sla/expressroute/v1_3/). 

### <a name="what-scenarios-should-customers-consider-with-expressroute-direct"></a>Welche Szenarien sollten Kunden mit ExpressRoute Direct in Betracht ziehen?  

ExpressRoute Direct bietet Kunden direkte Portpaare mit 100 oder 10 GBit/s in den globalen Microsoft-Backbone. Zu den Szenarien, die den Kunden den größten Nutzen bringen, gehören: massive Datenerfassung, physische Isolierung für regulierte Märkte und dedizierte Kapazität für Burstszenarien wie Rendering. 

### <a name="what-is-the-billing-model-for-expressroute-direct"></a>Welches Abrechnungsmodell gilt für ExpressRoute Direct? 

ExpressRoute Direct wird für das Portpaar zu einem festen Betrag in Rechnung gestellt. Standardverbindungen sind ohne zusätzliche Stunden enthalten, und Premium wird mit einem leichten Aufpreis berechnet. Ausgehende Datenübertragungen werden pro Verbindung basierend auf der Zone des Peeringstandorts abgerechnet.

### <a name="when-does-billing-start-and-stop-for-the-expressroute-direct-port-pairs"></a>Wann beginnt und endet die Abrechnung für die ExpressRoute Direct-Portpaare?

ExpressRoute Direct-Portpaare werden 45 Tage nach Beginn der Erstellung der ExpressRoute Direct-Ressource berechnet, oder wenn 1 oder beide Links aktiviert werden, was auch immer zuerst eintritt. Die 45-Tage-Karenzzeit wird Kunden gewährt, damit diese den Querverbindungsprozess mit dem Housinganbieter abschließen können.

Die Portpaare von ExpressRoute Direct werden Ihnen nicht mehr in Rechnung gestellt, nachdem Sie die direkten Ports gelöscht und die Querverbindungen entfernt haben. 
