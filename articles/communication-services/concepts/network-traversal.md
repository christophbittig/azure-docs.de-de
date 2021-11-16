---
title: Konzeptionelle Dokumentation für Azure Communication Services – Network Traversal
titleSuffix: An Azure Communication Services Concept Document
description: Erfahren Sie mehr über Azure Communication Services Network Traversal SDKs und REST-APIs.
author: rahulva
manager: shahen
services: azure-communication-services
ms.author: rahulva
ms.date: 09/20/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 02f52069cc9dea270cc8e6a34096af5bd80159d7
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2021
ms.locfileid: "131894930"
---
#  <a name="network-traversal-concepts"></a>Network Traversal-Konzepte   

Echtzeit-Relays lösen das Problem des NAT-Durchlaufs (Network Address Translation) für P2P-Verbindungen (Peer-zu-Peer). Die meisten Geräte im Internet verfügen heutzutage über eine IP-Adresse, die für internen LAN-Datenverkehr (Heim- oder Unternehmensnetzwerk) verwendet wird, oder eine extern sichtbare Adresse (Router oder NAT-Gateway). Um zwei Geräte über das Internet miteinander zu verbinden, ist die externe Adresse erforderlich, die aber in der Regel für ein Gerät hinter einem NAT-Gateway nicht verfügbar ist. Um das Konnektivitätsproblem zu beheben, werden die folgenden Protokolle verwendet:

  STUN (Session Traversal Utilities for NAT) bietet ein Protokoll, mit dem Geräte externe IP-Adresse im Internet austauschen können. Wenn sich die Clients gegenseitig sehen können, ist in der Regel kein Relay über einen TURN-Dienst erforderlich, da die Verbindung mittels Peer-zu-Peer hergestellt werden kann. Die Aufgabe eines STUN-Servers besteht darin, auf die Anforderung der externen IP-Adresse eines Geräts zu reagieren.
  
  TURN (Traversal Using Relays around NAT) ist eine Erweiterung des STUN-Protokolls, die auch die Daten zwischen zwei Endpunkten über einen gegenseitig sichtbaren Server weiterleitet.
        
## <a name="acs-network-traversal-overview"></a>Übersicht über ACS Network Traversal   

WebRTC (Web Real-Time Technologies) ermöglicht Webbrowsern das Streamen von Audio, Video und Daten zwischen Geräten, ohne dass ein Gateway in der Mitte erforderlich ist. Einige der hier aufgeführten häufigen Anwendungsfälle sind Sprache, Video, Übertragungen (Broadcasts) und Bildschirmfreigaben. Um zwei Endpunkte über das Internet miteinander zu verbinden, ist ihre externe IP-Adresse erforderlich. Externe IP-Adressen sind in der Regel nicht für Geräte verfügbar, die sich hinter einer Unternehmensfirewall befinden. Die Protokolle STUN (Session Traversal Utilities for NAT) und TURN (Traversal Using Relays around NAT) werden verwendet, um die Kommunikation der Endpunkte zu unterstützen.

Azure Communication Service bietet Verbindungen mit hoher Bandbreite und geringer Latenz zwischen Peers für Echtzeitkommunikationsszenarien. Der ACS Network Traversal-Dienst hostet TURN-Server für den Einsatz in den NAT-Szenarien. Azure Real-Time Relay Service macht die vorhandene STUN/TURN-Infrastruktur als PaaS-Angebot (Platform-as-a-Service) von Azure verfügbar. Der Dienst stellt STUN- und TURN-Dienste auf niedriger Ebene bereit.  Benutzer werden dann proportional zur Menge der weitergeleiteten Daten abgerechnet. 


## <a name="next-steps"></a>Nächste Schritte:

* Eine Einführung in die Authentifizierung finden Sie unter [Authentifizierung bei Azure Communication Services](./authentication.md).
* Eine Einführung in das Abrufen von Relaykandidaten finden Sie unter [Erstellen und Verwalten von Zugriffstoken](../quickstarts/relay-token.md).
