---
title: Grundlegendes zur Netzwerkkonnektivität für Azure Virtual Desktop
titleSuffix: Azure
description: Hier erhalten Sie Informationen zur Netzwerkkonnektivität für Azure Virtual Desktop.
author: gundarev
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: denisgun
ms.openlocfilehash: 409a86883cf010390b3d37c8d3af945ed3702eae
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131851843"
---
# <a name="understanding-azure-virtual-desktop-network-connectivity"></a>Grundlegendes zur Netzwerkkonnektivität für Azure Virtual Desktop

Azure Virtual Desktop bietet die Möglichkeit, Clientsitzungen auf den Sitzungshosts zu hosten, die in Azure ausgeführt werden. Microsoft verwaltet Teile der Dienste im Auftrag des Kunden und stellt sichere Endpunkte zum Verbinden von Clients und Sitzungshosts bereit. Das folgende Diagramm bietet eine allgemeine Übersicht über die von Azure Virtual Desktop verwendeten Netzwerkverbindungen.

:::image type="content" source="media/azure-virtual-desktop-network-connections.svg" alt-text="Diagramm: Netzwerkverbindungen von Azure Virtual Desktop" lightbox="media/azure-virtual-desktop-network-connections.svg":::

## <a name="session-connectivity"></a>Sitzungskonnektivität

Azure Virtual Desktop verwendet Remotedesktopprotokoll (RDP), um Remotefunktionen für die Anzeige und Eingabe über Netzwerkverbindungen bereitzustellen. RDP wurde anfänglich mit Windows NT 4.0 Terminal Server Edition veröffentlicht und entwickelte sich mit jedem Release von Microsoft Windows und Windows Server kontinuierlich weiter. Von Anfang an wurde RDP so entwickelt, dass es vom zugrunde liegenden Transportstapel unabhängig ist, und heute unterstützt es mehrere Arten von Transporten.

## <a name="reverse-connect-transport"></a>Reverse Connection-Transport

Azure Virtual Desktop verwendet den Reverse Connection-Transport zum Einrichten der Remotesitzung und für den Transport des RDP-Datenverkehrs. Im Gegensatz zu Bereitstellungen der lokalen Remotedesktopdienste verwendet der Reverse Connection-Transport keinen TCP-Listener, um eingehende RDP-Verbindungen zu empfangen. Stattdessen wird die ausgehende Konnektivität mit der Azure Virtual Desktop-Infrastruktur über die HTTPS-Verbindung verwendet.

## <a name="session-host-communication-channel"></a>Kommunikationskanal des Sitzungshosts

Beim Start des Azure Virtual Desktop-Sitzungshosts richtet der Remote Desktop Agent Loader-Dienst den beständigen Kommunikationskanal des Azure Virtual Desktop-Brokers ein. Dieser Kommunikationskanal überlagert eine sichere TLS-Verbindung (Transport Layer Security) und fungiert als Bus für den Dienstnachrichtenaustausch zwischen Sitzungshost und Azure Virtual Desktop-Infrastruktur.

## <a name="client-connection-sequence"></a>Clientverbindungssequenz

Die Clientverbindungssequenz wird im Folgenden beschrieben:

1. Der unterstützte Azure Virtual Desktop-Clientbenutzer abonniert den Azure Virtual Desktop-Arbeitsbereich.
2. Azure Active Directory authentifiziert den Benutzer und gibt das Token zurück, das zum Aufzählen der für einen Benutzer verfügbaren Ressourcen verwendet wird.
3. Der Client übergibt das Token an den Feedabonnementdienst von Azure Virtual Desktop.
4. Der Feedabonnementdienst von Azure Virtual Desktop überprüft das Token.
5. Der Feedabonnementdienst von Azure Virtual Desktop übergibt die Liste der verfügbaren Desktops und RemoteApps in Form einer digital signierten Verbindungskonfiguration zurück an den Client.
6. Der Client speichert die Verbindungskonfiguration für jede verfügbare Ressource in einem Satz von RDP-Dateien.
7. Wenn ein Benutzer die Ressource zum Verbinden auswählt, verwendet der Client die zugehörige RDP-Datei, stellt die sichere TLS 1.2-Verbindung mit der nächstgelegenen Azure Virtual Desktop-Gatewayinstanz her und übergibt die Verbindungsinformationen.
8. Das Azure Virtual Desktop-Gateway überprüft die Anforderung und fordert den Azure Virtual Desktop-Broker auf, die Verbindung zu orchestrieren.
9. Der Azure Virtual Desktop-Broker identifiziert den Sitzungshost und verwendet den zuvor eingerichteten permanenten Kommunikationskanal, um die Verbindung zu initialisieren.
10. Der Remotedesktopstack initiiert die TLS 1.2-Verbindung mit derselben Azure Virtual Desktop-Gatewayinstanz, die vom Client verwendet wird.
11. Nachdem sowohl der Client als auch der Sitzungshost mit dem Gateway verbunden sind, beginnt das Gateway mit der Übermittlung der Rohdaten zwischen beiden Endpunkten. Dadurch wird der grundlegende Reverse Connection-Transport für das RDP festgelegt.
12. Nachdem der Basistransport festgelegt wurde, startet der Client den RDP-Handshake.

## <a name="connection-security"></a>Verbindungssicherheit

TLS 1.2 wird für alle Verbindungen verwendet, die von den Clients und Sitzungshosts mit den Komponenten der Azure Virtual Desktop-Infrastruktur initiiert werden. Azure Virtual Desktop verwendet dasselbe TLS 1.2-Verschlüsselungsverfahren wie [Azure Front Door](../frontdoor/concept-end-to-end-tls.md#supported-cipher-suites). Es ist wichtig, sicherzustellen, dass sowohl Clientcomputer als auch Sitzungshosts diese Verschlüsselungsverfahren verwenden können.
Für den Reverse Connection-Transport stellen sowohl der Client als auch der Sitzungshost eine Verbindung mit dem Azure Virtual Desktop-Gateway her. Nach dem Einrichten der TCP-Verbindung überprüft der Client oder Sitzungshost das Zertifikat des Azure Virtual Desktop-Gateways.
Nach dem Einrichten des Basistransports stellt RDP mithilfe der Zertifikate des Sitzungshosts eine geschachtelte TLS-Verbindung zwischen dem Client und dem Sitzungshost her. Standardmäßig wird das Zertifikat, das für die RDP-Verschlüsselung verwendet wird, vom Betriebssystem bei der Bereitstellung selbst generiert. Bei Bedarf können Kunden zentral verwaltete Zertifikate bereitstellen, die von der Unternehmenszertifizierungsstelle ausgestellt werden. Weitere Informationen zur Konfiguration von Zertifikaten finden Sie in der [Windows Server-Dokumentation](/troubleshoot/windows-server/remote/remote-desktop-listener-certificate-configurations).

## <a name="next-steps"></a>Nächste Schritte

* Informationen zu den Bandbreitenanforderungen für Azure Virtual Desktop finden Sie unter [Bandbreitenanforderungen des Remotedesktopprotokolls](rdp-bandwidth.md).
* Informationen zu den ersten Schritten mit Quality of Service (QoS) für Azure Virtual Desktop finden Sie unter [Implementieren von Quality of Service (QoS) für Windows Virtual Desktop (Vorschau)](rdp-quality-of-service-qos.md).
