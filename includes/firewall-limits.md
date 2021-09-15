---
title: include file
description: include file
services: firewall
author: vhorne
ms.service: firewall
ms.topic: include
ms.date: 08/26/2021
ms.author: victorh
ms.custom: include file
ms.openlocfilehash: dc30e9a22d2de952974b064cb82e8e7adf0579f8
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/27/2021
ms.locfileid: "123078471"
---
| Resource | Begrenzung |
| --- | --- |
| Datendurchsatz |30 GBit/s|
|Regelgrenzwerte|10.000 eindeutige Quellen/Ziele in Netzwerkregeln|
|Maximale Anzahl von DNAT-Regeln|298 (für Firewalls, die mit einer einzigen öffentlichen IP-Adresse konfiguriert sind)<br><br> Die DNAT-Einschränkung ist auf die zugrunde liegende Plattform zurückzuführen. Die maximale Anzahl von DNAT-Regeln beträgt 298. Durch zusätzliche öffentliche IP-Adressen wird die Anzahl verfügbarer DNAT-Regeln jedoch verringert. Beispielsweise lassen zwei öffentliche IP-Adressen 297 DNAT-Regeln zu. Wenn das Protokoll einer Regel sowohl für TCP als auch für UDP konfiguriert ist, zählt dies als zwei Regeln.|
|Mindestgröße für AzureFirewallSubnet |/26|
|Portbereich in Netzwerk- und Anwendungsregeln|1 bis 65535|
|Öffentliche IP-Adressen|Höchstens 250. Alle öffentlichen IP-Adressen können in DNAT-Regeln verwendet werden und zählen zu den verfügbaren SNAT-Ports.|
|IP-Adressen in IP-Gruppen|Maximal 100 IP-Gruppen pro Firewall.<br>Maximal 5000 einzelne IP-Adressen oder IP-Präfixe pro IP-Gruppe.
|Routingtabelle|Standardmäßig verfügt AzureFirewallSubnet über die Route „0.0.0.0/0“, bei der der Wert „NextHopType“ auf **Internet** festgelegt ist.<br><br>Azure Firewall muss über eine direkte Internetverbindung verfügen. Wenn Ihr Subnetz „AzureFirewallSubnet“ eine Standardroute zu Ihrem lokalen Netzwerk über BGP erfasst, müssen Sie diese mit der benutzerdefinierten Route 0.0.0.0/0 überschreiben. Legen Sie dabei den Wert **NextHopType** auf **Internet** fest, um die direkte Internetkonnektivität beizubehalten. Standardmäßig unterstützt Azure Firewall keine Tunnelerzwingung für ein lokales Netzwerk.<br><br>Wenn Ihre Konfiguration jedoch die Tunnelerzwingung für ein lokales Netzwerk erfordert, wird Microsoft dies im Einzelfall unterstützen. Wenden Sie sich in diesem Fall an den Support, damit Ihr Fall überprüft werden kann. Bei einer Annahme wird Ihr Abonnement zugelassen, damit die erforderliche Internetkonnektivität der Firewall auch sicher erhalten bleibt.|
|FQDNs in Netzwerkregeln|Um eine gute Leistung zu erzielen, sollte der Wert von 1.000 FQDNs in allen Netzwerkregeln pro Firewall nicht überschritten werden.|