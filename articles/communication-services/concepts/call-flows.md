---
title: Anrufabläufe in Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Enthält Informationen zu Anrufabläufen in Azure Communication Services.
author: probableprime
manager: chpalm
services: azure-communication-services
ms.author: rifox
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.subservice: calling
ms.openlocfilehash: 70eec46f882e9aedb8a30c6dede2f0b4a420c553
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128672303"
---
# <a name="call-flow-basics"></a>Grundlegendes zu Anrufabläufen

Der folgende Abschnitt enthält eine Übersicht über die Anrufabläufe in Azure Communication Services. Signalisierungs- und Medienabläufe hängen von den Typen der Anrufe ab, die von Ihren Benutzern getätigt werden. Beispiele für Anruftypen sind 1:1 VoIP, 1:1 Festnetz und Gruppenanrufe mit einer Mischung aus VoIP- und Festnetzteilnehmern. Lesen Sie den Artikel zu den [Anruftypen](./voice-video-calling/about-call-types.md).

## <a name="about-signaling-and-media-protocols"></a>Informationen zu Signalisierungs- und Medienprotokollen

Wenn Sie einen Peer-to-Peer- oder Gruppenanruf tätigen, werden im Hintergrund zwei Protokolle verwendet: HTTP (REST) für die Signalisierung und SRTP für Medien.

Die Signalisierung zwischen den SDKs oder zwischen SDKs und Communication Services-Signalisierungscontrollern wird per HTTP REST (TLS) durchgeführt. Für Echtzeit-Mediendatenverkehr (Real-Time Media Traffic, RTP) wird das User Datagram-Protokoll (UDP) bevorzugt. Falls die Nutzung von UDP durch Ihre Firewall verhindert wird, wird vom SDK für Medien das Transmission Control-Protokoll (TCP) genutzt.

Wir sehen uns nun die Signalisierungs- und Medienprotokolle in unterschiedlichen Szenarien an.

## <a name="call-flow-cases"></a>Anwendungsfälle für Anrufabläufe

### <a name="case-1-voip-where-a-direct-connection-between-two-devices-is-possible"></a>Fall 1: VoIP, wobei eine direkte Verbindung zwischen zwei Geräten möglich ist

Bei VoIP- oder Videoanrufen vom Typ 1:1 wird für den Datenverkehr der direkte Pfad bevorzugt. „Direkter Pfad“ bedeutet, dass eine direkte Verbindung hergestellt wird, wenn sich zwei SDKs gegenseitig direkt erreichen können. Dies ist normalerweise möglich, wenn sich zwei SDKs in demselben Subnetz befinden (z. B. im Subnetz 192.168.1.0/24) oder wenn sich die Geräte jeweils im Livezustand in Subnetzen befinden, die sich gegenseitig erkennen können (SDKs in den Subnetzen 10.10.0.0/16 und 192.168.1.0/24 können sich gegenseitig erreichen).

:::image type="content" source="./media/call-flows/about-voice-case-1.png" alt-text="Diagramm: Direkter VoIP-Anruf zwischen Benutzern und Communication Services":::

### <a name="case-2-voip-where-a-direct-connection-between-devices-is-not-possible-but-where-connection-between-nat-devices-is-possible"></a>Fall 2: VoIP, wobei keine direkte Verbindung zwischen Geräten möglich ist, dafür aber eine Verbindung zwischen NAT-Geräten

Wenn zwei Geräte in Subnetzen angeordnet sind, die sich nicht erreichen können (z. B. wenn Alice in einem Coffee-Shop und Bob im Homeoffice arbeitet), aber die Verbindung zwischen den NAT-Geräten möglich ist, stellen die clientseitigen SDKs die Konnektivität über die NAT-Geräte her.

Für Alice ist dies die Netzwerkadressenübersetzung (NAT) des Coffee-Shops, und für Bob die NAT im Homeoffice. Das Gerät von Alice sendet die externe Adresse ihrer NAT, und Bob geht genauso vor. Die SDKs erhalten die externen Adressen über einen STUN-Dienst (Session Traversal Utilities for NAT, Sitzungsdurchlauf-Hilfsprogramme für NAT), der von Azure Communication Services kostenlos bereitgestellt wird. Die Logik, mit der der Handshake zwischen Alice und Bob verarbeitet wird, ist in die von Azure Communication Services bereitgestellten SDKs eingebettet. (Sie müssen keine zusätzliche Konfiguration durchführen.)

:::image type="content" source="./media/call-flows/about-voice-case-2.png" alt-text="Diagramm: VoIP-Anruf mit Nutzung einer STUN-Verbindung":::

### <a name="case-3-voip-where-neither-a-direct-nor-nat-connection-is-possible"></a>Fall 3: VoIP, wobei weder eine direkte noch eine NAT-Verbindung möglich ist

Falls sich mindestens eines der beiden Clientgeräte hinter einer symmetrischen NAT befindet, ist ein separater Clouddienst erforderlich, um die Medien zwischen den beiden SDKs zu übertragen. Dieser Dienst wird als TURN (Traversal Using Relays around NAT, Durchlauf mit Signalisierung für NAT) bezeichnet und wird ebenfalls von Communication Services bereitgestellt. Für das Communication Services SDK für Anrufe werden automatisch TURN-Dienste basierend auf den erkannten Netzwerkbedingungen genutzt. Die Nutzung des TURN-Diensts von Microsoft wird separat berechnet.

:::image type="content" source="./media/call-flows/about-voice-case-3.png" alt-text="Diagramm: VoIP-Anruf mit Nutzung einer TURN-Verbindung":::

### <a name="case-4-group-calls-with-pstn"></a>Fall 4: Gruppenanrufe per Festnetz (PSTN)

Bei der Signalisierung und den Medien für Festnetzanrufe (PSTN) wird die Telefonieressource von Azure Communication Services genutzt. Diese Ressource ist auch mit anderen Netzbetreibern verbunden.

Der PSTN-Mediendatenverkehr fließt über eine Komponente, die als „Medienprozessor“ bezeichnet wird.

:::image type="content" source="./media/call-flows/about-voice-pstn.png" alt-text="Diagramm: PSTN-Gruppenanruf mit Communication Services":::

> [!NOTE]
> Hinweis für Benutzer, die sich mit der Medienverarbeitung auskennen: Unser Medienprozessor ist gleichzeitig ein Back-to-Back-Benutzer-Agent gemäß der Definition in [RFC 3261 SIP: Session Initiation-Protokoll](https://tools.ietf.org/html/rfc3261). Dies bedeutet, dass er beim Verarbeiten von Anrufen zwischen Microsoft und den Netzen von Netzbetreibern Codecs übersetzen kann. Der Signalisierungscontroller von Azure Communication Services ist die Implementierung eines SIP-Proxys über den gleichen RFC von Microsoft.

Bei Gruppenanrufen fließen die Medien- und Signalisierungsdaten immer über das Azure Communication Services-Back-End. Die Audio- bzw. Videodaten aller Teilnehmer werden in der Medienprozessor-Komponente gemischt. Alle Mitglieder eines Gruppenanrufs senden ihre Audio- bzw. Videostreams an den Medienprozessor, von dem gemischte Medienstreams zurückgegeben werden.

Als Echtzeit-Standardprotokoll (RTP) für Gruppenanrufe wird das User Datagram-Protokoll (UDP) verwendet.

> [!NOTE]
> Der Medienprozessor kann als Einheit für die Steuerung mehrerer Punkte (Multipoint Control Unit, MCU) oder als Einheit für die selektive Weiterleitung (Selective Forwarding Unit, SFU) fungieren.

:::image type="content" source="./media/call-flows/about-voice-group-calls.png" alt-text="Diagramm: Ablauf des UDP-Medienprozesses in Communication Services":::

Falls für das SDK UDP für Medien aufgrund von Firewalleinschränkungen nicht verwendet werden kann, wird versucht, das Transmission Control-Protokoll (TCP) zu nutzen. Beachten Sie, dass für die Medienprozessor-Komponente UDP erforderlich ist. In diesem Fall wird der TURN-Dienst von Communication Services also dem Gruppenanruf hinzugefügt, um TCP in UDP zu übersetzen. Hierbei werden Ihnen die TURN-Gebühren berechnet, sofern die TURN-Funktionen nicht manuell deaktiviert werden.

:::image type="content" source="./media/call-flows/about-voice-group-calls-2.png" alt-text="Diagramm: Ablauf des TCP-Medienprozesses in Communication Services":::

### <a name="case-5-communication-services-sdk-and-microsoft-teams-in-a-scheduled-teams-meeting"></a>Fall 5: Communication Services SDK und Microsoft Teams in einer geplanten Teams-Besprechung

Die Signalisierung wird über den Signalisierungscontroller abgewickelt. Medien durchlaufen den Medienprozessor. Signalisierungscontroller und Medienprozessor werden von Communication Services und Microsoft Teams gemeinsam genutzt.

:::image type="content" source="./media/call-flows/teams-communication-services-meeting.png" alt-text="Diagramm: Communication Services SDK und Teams-Client in einer geplanten Teams-Besprechung":::



## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erste Schritte mit Anrufen](../quickstarts/voice-video-calling/getting-started-with-calling.md)

Die Artikel zu den folgenden Themen könnten Sie auch interessieren:

- [Anruftypen](../concepts/voice-video-calling/about-call-types.md)
- [Client-Server-Architektur](./client-and-server-architecture.md)
- [Topologien für Anrufabläufe](./detailed-call-flows.md)
