---
title: Vorbereiten des Netzwerks Ihrer Organisation für Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Erfahren Sie, welche Netzwerkanforderungen für Sprach- und Videoanrufe mit Azure Communication Services gelten.
author: nmurav
manager: chpalm
services: azure-communication-services
ms.author: nmurav
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 9b7728d77ebba14dd127c797898609c9ae6fed0d
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/31/2021
ms.locfileid: "123258985"
---
# <a name="network-recommendations"></a>Netzwerkempfehlungen

Dieser Artikel bietet eine Übersicht darüber, wie die Netzwerkumgebung sich auf die Qualität von Sprach- und Videoanrufen auswirkt. Bei der Qualität von Azure Communication Services-Echtzeitmedien – Audio- und Videomedien sowie die gemeinsame Nutzung von Anwendungen – spielen viele Faktoren eine Rolle. Einige dieser Faktoren sind Netzwerkqualität und -bandbreite sowie Firewall-, Host- und Gerätekonfigurationen.

## <a name="network-quality"></a>Netzwerkqualität

Die Qualität von Echtzeitmedien über IP wird durch die Qualität der zugrunde liegenden Netzwerkkonnektivität erheblich beeinflusst. Am wichtigsten ist hierbei das Ausmaß der folgenden Faktoren:

* **Wartezeit**: Dies ist die Zeit, die benötigt wird, um ein IP-Paket im Netzwerk von Punkt A zu Punkt B zu übertragen. Diese Verzögerung bei der Weitergabe im Netzwerk hängt von der physischen Entfernung zwischen den beiden Punkten sowie vom Overhead auf den Geräten ab, über die Ihr Datenverkehr fließt. Die Latenz wird für die einfache Strecke oder für die Roundtripdauer (Round Trip Time, RTT) gemessen.
* **Paketverlust**: Steht für den Prozentsatz an Paketen, die innerhalb eines bestimmten Zeitfensters verloren gehen. Paketverluste wirken sich direkt auf die Audioqualität aus – vom Verlust kleiner Einzelpakete mit kaum spürbaren Auswirkungen bis hin zu direkt aufeinanderfolgenden Burstverlusten, die zu vollständigen Audioausfällen führen.
* **Jitter (zwischen eintreffenden Paketen)** : Dies ist die durchschnittliche Änderung der Verzögerung zwischen aufeinanderfolgenden Paketen. In Communication Services kann ein gewisses Maß an Jitter durch Pufferung ausgeglichen werden. Nur wenn der Jitter-Grad die Pufferung überschreitet, nimmt ein Teilnehmer die damit verbundenen Auswirkungen wahr.

## <a name="network-bandwidth"></a>Netzwerkbandbreite

Stellen Sie sicher, dass Ihr Netzwerk für die Unterstützung der Bandbreite konfiguriert ist, die für gleichzeitige Communication Services-Mediensitzungen und andere Geschäftsanwendungen benötigt wird. Das Testen des End-to-End-Netzwerkpfads auf Engpässe bei der Bandbreite ist für die erfolgreiche Bereitstellung Ihrer Communication Services-Multimedialösung von entscheidender Bedeutung.

Die folgenden Bandbreitenanforderungen gelten für die JavaScript SDKs.

|Bandbreite|Szenarien|
|:--|:--|
|40 Kbit/s|Peer-to-Peer-Audioanrufe|
|500 Kbit/s|Peer-to-Peer-Audioanrufe und Bildschirmfreigabe|
|500 Kbit/s|Peer-to-Peer-Videoanrufe hoher Qualität mit 360 Pixeln bei 30 FPS|
|1,2 MBit/s|Peer-to-Peer-Videoanrufe in HD-Qualität mit einer HD-Auflösung von 720 Pixeln bei 30 FPS|
|500 Kbit/s|Gruppenvideoanrufe mit 360 Pixeln bei 30 FPS|
|1,2 MBit/s|HD-Gruppenvideoanrufe mit einer HD-Auflösung von 720 Pixeln bei 30 FPS| 

Die folgenden Bandbreitenanforderungen gelten für die nativen SDKs für Windows, Android und iOS.

|Bandbreite|Szenarien|
|:--|:--|
|30 Kbit/s|Peer-to-Peer-Audioanrufe |
|130 Kbit/s|Peer-to-Peer-Audioanrufe und Bildschirmfreigabe|
|500 Kbit/s|Peer-to-Peer-Videoanrufe hoher Qualität mit 360 Pixeln bei 30 FPS|
|1,2 MBit/s|Peer-to-Peer-Videoanrufe in HD-Qualität mit einer HD-Auflösung von 720 Pixeln bei 30 FPS|
|1,5 Mbit/s|Peer-to-Peer-Videoanrufe in HD-Qualität mit einer HD-Auflösung von 1080 Pixeln bei 30 FPS |
|500 Kbit/s bis 1 Mbit/s|Video-Gruppenanrufe|
|1 Mbit/s bis 2 Mbit/s|HD-Gruppenvideoanrufe, Videos mit 540 Pixeln auf einem Bildschirm mit 1080 Pixeln|

## <a name="firewall-configuration"></a>Firewallkonfiguration

Für Communication Services-Verbindungen wird Internetkonnektivität mit bestimmten Ports und IP-Adressen benötigt, um eine Multimediaumgebung mit hoher Qualität bereitstellen zu können. Communication Services kann auch ohne Zugriff auf diese Ports und IP-Adressen funktionieren. Ein optimales Benutzererlebnis wird aber nur erzielt, wenn die empfohlenen Ports und IP-Adressbereiche verfügbar sind.

| Category | IP-Adressbereiche oder FQDN | Ports | 
| :-- | :-- | :-- |
| Mediendatenverkehr | [IP-Adressbereiche für die öffentliche Azure-Cloud](https://www.microsoft.com/download/confirmation.aspx?id=56519) | UDP 3478 bis 3481, TCP-Port 443 |
| Signalisierung, Telemetrie, Registrierung| *.skype.com, *.microsoft.com, *.azure.net, *.azureedge.net, *.office.com, *.trouter.io | TCP-Ports 443 und 80 |

## <a name="network-optimization"></a>Netzwerkoptimierung

Die folgenden Aufgaben sind optional und für die Einführung von Communication Services nicht unbedingt erforderlich. Verwenden Sie diese Anleitung zum Optimieren Ihres Netzwerks und der Leistung von Communication Services oder bei bestehenden Netzwerkeinschränkungen.
Eine weitere Optimierung kann in den folgenden Fällen ratsam sein:

* Communication Services wird nur langsam ausgeführt. Möglicherweise verfügen Sie nicht über ausreichend Bandbreite.
* Anrufe werden immer wieder verworfen. Diese Ausfälle können durch die Firewall oder Proxyblocker verursacht werden.
* Bei Anrufen treten kurze Unterbrechungen oder Rauschen auf, oder Stimmen klingen wie Roboter. Diese Probleme können durch Jitter oder Paketverluste verursacht werden.

| Schritt zur Netzwerkoptimierung | Details |
| :-- | :-- |
| Planen Ihres Netzwerks | In dieser Dokumentation finden Sie Informationen zu den Mindestanforderungen, die Netzwerke in Bezug auf Anrufe erfüllen müssen. Sehen Sie sich hierzu das [Teams-Beispiel zur Planung Ihres Netzwerks](/microsoftteams/tutorial-network-planner-example) an. |
| Externe Namensauflösung | Stellen Sie sicher, dass alle Computer, auf denen die Communications Services SDKs ausgeführt werden, externe DNS-Abfragen auflösen können, um die von Communications Services bereitgestellten Dienste zu ermitteln. Vergewissern Sie sich auch, dass der Zugriff nicht durch Ihre Firewalls verhindert wird. Stellen Sie sicher, dass die Adressen „*.skype.com“, „*.microsoft.com“, „*.azure.net“, „*.azureedge.net“, „*.office.com“ und „*.trouter.io“ von den SDKs aufgelöst werden können. |
| Aufrechterhalten der Sitzungspersistenz | Stellen Sie sicher, dass Ihre Firewall die zugeordneten NAT-Adressen (Netzwerkadressenübersetzung) oder die Ports für UDP nicht ändert.
Überprüfen der NAT-Poolgröße | Überprüfen Sie die für die Benutzerkonnektivität erforderliche Größe des NAT-Pools. Wenn mehrere Benutzer und Geräte über [Netzwerk- oder Portadressenübersetzung](/office365/enterprise/nat-support-with-office-365) auf Communication Services zugreifen, stellen Sie sicher, dass die hinter jeder öffentlich routingfähigen IP-Adresse verborgenen Geräte die unterstützte Anzahl nicht überschreiten. Vergewissern Sie sich, dass die richtigen öffentlichen IP-Adressen den NAT-Pools zugewiesen sind, um eine Überlastung der Ports zu verhindern. Eine Überlastung der Ports trägt dazu bei, dass interne Benutzer und Geräte keine Verbindung mit Communication Services herstellen können. |
| Anleitung zur Angriffserkennung und -verhinderung | Falls Sie in Ihrer Umgebung ein [Intrusion-Detection-System](../../../network-watcher/network-watcher-intrusion-detection-open-source-tools.md) oder ein Intrusion-Prevention-System (IDS/IPS, System zur Erkennung bzw. Verhinderung von Angriffen) bereitgestellt haben, um die Sicherheit für ausgehende Verbindungen zu erhöhen, lassen Sie alle Communication Services-URLs zu. |
| Konfigurieren eines VPN mit getrennten Tunneln | Geben Sie einen alternativen Pfad für Teams-Datenverkehr an, mit dem das virtuelle private Netzwerk (VPN) umgangen wird. Dies wird meist als [VPN mit getrennten Tunneln](/windows/security/identity-protection/vpn/vpn-routing) bezeichnet. Die Verwendung von getrennten Tunneln bedeutet, dass Datenverkehr für Communications Services nicht über das VPN fließt, sondern direkt an Azure. Das Umgehen Ihres VPN wirkt sich positiv auf die Medienqualität aus und verringert die Auslastung für die VPN-Geräte und das Netzwerk Ihrer Organisation. Arbeiten Sie mit Ihrem VPN-Anbieter zusammen, um ein VPN mit getrennten Tunneln zu implementieren. Weitere Gründe für die Empfehlung, das VPN zu umgehen: <ul><li> VPNs sind normalerweise nicht für die Unterstützung von Echtzeitmedien konzipiert bzw. konfiguriert.</li><li> VPNs unterstützen möglicherweise auch das UDP nicht, das für Communication Services erforderlich ist.</li><li>VPNs bewirken zudem eine zusätzliche Verschlüsselung von Mediendatenverkehr, der bereits verschlüsselt ist.</li><li>Die Konnektivität mit Communication Services ist möglicherweise nicht effizient, wenn der Datenverkehr über ein VPN-Gerät geleitet werden muss, das einen Engpass darstellen kann.</li></ul>|
| Implementieren von QoS (Quality of Service) | Nutzen Sie [Quality of Service (QoS)](/microsoftteams/qos-in-teams), um die Priorisierung für Pakete zu konfigurieren. QoS verbessert die Anrufqualität und unterstützt Sie bei der Überwachung und Problembehandlung. QoS sollte in allen Segmenten eines verwalteten Netzwerks implementiert werden. Auch wenn für ein Netzwerk ausreichende Bandbreite bereitgestellt wurde, kann QoS zu einer Entschärfung von Risiken bei unerwarteten Netzwerkereignissen beitragen. Bei QoS wird der Sprachdatenverkehr priorisiert, damit diese unerwarteten Ereignisse keine negativen Auswirkungen auf die Qualität haben. | 
| Optimieren des WLAN | Ähnlich wie VPNs sind auch WLANs nicht unbedingt für die Unterstützung von Echtzeitmedien konzipiert oder konfiguriert. Die Planung bzw. Optimierung eines WLAN für die Unterstützung von Communication Services ist ein wichtiger Aspekt, um eine Bereitstellung mit hoher Qualität zu erzielen. Beachten Sie folgende Faktoren: <ul><li>Implementieren Sie QoS oder WiFi Multimedia, um sicherzustellen, dass Mediendatenverkehr über Ihre WLAN-Verbindungen richtig priorisiert wird.</li><li>Planen und optimieren Sie die WLAN-Bänder und die Platzierung der Zugriffspunkte. Der 2,4-GHz-Bereich kann je nach Platzierung der Zugriffspunkte eine geeignete Lösung sein. Häufig werden Zugriffspunkte aber durch andere Geräte von Verbrauchern beeinflusst, die in demselben Bereich betrieben werden. Der 5-GHz-Bereich ist aufgrund seiner Dichte besser für Echtzeitmedien geeignet, aber es werden mehr Zugriffspunkte benötigt, um eine ausreichende Abdeckung zu erzielen. Endpunkte müssen diesen Bereich unterstützen und so konfiguriert sein, dass diese Bänder entsprechend genutzt werden.</li><li>Bei Verwendung eines Dualband-WLAN sollten Sie erwägen, die so genannten Bandsteuerung zu implementieren. Die Bandsteuerung (Band Steering) ist ein Verfahren, das von WLAN-Anbietern implementiert wurde, um Dualband-Clientgeräte zur Nutzung des 5-GHz-Bereichs zu bewegen.</li><li>Wenn die Zugriffspunkte desselben Kanals zu nah beieinander liegen, kann es zu einer Signalüberlappung und unerwünschten Konkurrenzsituationen kommen, die für Benutzer zu Leistungseinbußen führen. Stellen Sie sicher, dass für benachbarte Zugriffspunkte Kanäle genutzt werden, die sich nicht überlappen.</li></ul> Jeder WLAN-Anbieter gibt seine eigenen Empfehlungen zur Bereitstellung der WLAN-Lösung heraus. Eine genaue Anleitung erhalten Sie bei Ihrem WLAN-Anbieter.|

## <a name="operating-systems-and-browsers-for-javascript-sdks"></a>Betriebssysteme und Browser (für JavaScript SDKs)

Die Communication Services SDKs für Sprache und Video unterstützen bestimmte Betriebssysteme und Browser.
Informieren Sie sich in der [Dokumentation zu den Konzepten für Anrufe](./calling-sdk-features.md) über die Betriebssysteme und Browser, die von den SDKs für Sprachanrufe unterstützt werden.

## <a name="next-steps"></a>Nächste Schritte

Die folgenden Artikel sind möglicherweise für Sie von Interesse:

- [Anrufbibliotheken](./calling-sdk-features.md)
- [Client-Server-Architektur](../client-and-server-architecture.md)
- [Topologien für Anrufabläufe](../call-flows.md)
