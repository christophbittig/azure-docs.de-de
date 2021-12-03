---
author: vhorne
ms.service: application-gateway
ms.topic: include
ms.date: 09/09/2021
ms.author: victorh
ms.openlocfilehash: 76fed7a6f79dafd42c6262f4f73bd21a7c2e45a7
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131860693"
---
| Resource | Begrenzung | Hinweis |
| --- | --- | --- |
| Azure Application Gateway |1\.000 pro Abonnement | |
| Front-End-IP-Konfigurationen |2 |1 öffentliche und 1 private |
| Front-End-Ports |100<sup>1</sup> | |
| Back-End-Adresspools |100<sup>1</sup> | |
| Back-End-Server pro Pool |1\.200 | |
| HTTP-Listener |200<sup>1</sup> |Beschränkt auf 100 aktive Listener, die Datenverkehr weiterleiten. Aktive Listener = Gesamtanzahl von Listenern - nicht aktive Listener.<br>Wenn eine Standardkonfiguration innerhalb einer Routingregel zur Weiterleitung von Datenverkehr (z. B. mit einem Listener, einem Back-End-Pool und HTTP-Einstellungen) festgelegt ist, dann zählt dies ebenfalls als Listener. Weitere Informationen finden Sie in den [Häufig gestellten Fragen zu Application Gateway](../articles/application-gateway/application-gateway-faq.yml#what-is-considered-an-active-listener-versus-inactive-listener).|
| HTTP-Lastenausgleichsregeln |400<sup>1</sup> | |
| Back-End-HTTP-Einstellungen |100<sup>1</sup> | |
| Instanzen pro Gateway |V1 SKU – 32<br>V2 SKU – 125 | |
| SSL-Zertifikate |100<sup>1</sup> |1 pro HTTP-Listener |
| Maximale SSL-Zertifikatgröße |V1 SKU – 10 KB<br>V2 SKU - 16 KB| |
| Authentifizierungszertifikate |100 | |
| Vertrauenswürdige Stammzertifikate |100 | |
| Minimales Anforderungstimeout |1 Sekunde | |
| Maximales Anforderungstimeout für privates Back-End |24 Stunden | |
| Maximales Anforderungstimeout für externes Back-End |4 Minuten | |
| Anzahl der Websites |100<sup>1</sup> |1 pro HTTP-Listener |
| URL-Zuordnungen pro Listener |1 | |
| Maximale Anzahl pfadbasierter Regeln pro URL-Zuordnung|100||
| Umleitungskonfiguration |100<sup>1</sup>| |
| Anzahl von Regelsätzen für das erneute Generieren |400| |
| Anzahl von Header- oder URL-Konfigurationen pro Regelsatz für das erneute Generieren|40| |
| Anzahl von Bedingungen pro Regelsatz für das erneute Generieren|40| |
| Gleichzeitige WebSocket-Verbindungen |Mittelgroße Gateways 20.000<sup>2</sup><br> Große Gateways 50.000<sup>2</sup>| |
| Maximale URL-Länge|32 KB| |
| Maximale Headergröße für HTTP/2 |16 KB| |
| Maximale Dateiuploadgröße (Standard-SKU) |V2 – 4 GB<br>V1 – 2 GB | |
| Maximale Dateiuploadgröße (WAF-SKU) |V1 Mittel – 100 MB<br>V1 Groß – 500 MB<br>V2 – 750 MB<br>V2 (mit CRS 3.2 oder neuer) – 4 GB| |
| WAF-Textgrößenbeschränkung (ohne Dateien)|V1 oder V2 (mit CRS 3.1 und älter) – 128 KB<br>V2 (mit CRS 3.2 oder neuer) – 2 MB| |
| Maximale benutzerdefinierte WAF-Regeln|100||
| Maximale WAF-Ausschlüsse pro Application Gateway|40||

<sup>1</sup> Bei WAF-fähigen SKUs muss die Anzahl von Ressourcen auf 40 beschränkt werden.

<sup>2</sup> Der Grenzwert gilt pro Application Gateway-Instanz nicht pro Application Gateway-Ressource.
