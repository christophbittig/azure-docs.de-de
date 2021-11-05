---
title: Inline-L7-DDoS-Schutz mit Gateway Load Balancer und Partner-NVAs
description: Erfahren Sie, wie Sie Inline-L7-DDoS-Schutz mit Gateway Load Balancer und Partner-NVAs erstellen und aktivieren.
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.author: yitoh
ms.date: 10/21/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 79e4507cb573dfe6893081df737a0e604810fbc5
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095203"
---
# <a name="inline-l7-ddos-protection-with-gateway-load-balancer-and-partner-nvas"></a>Inline-L7-DDoS-Schutz mit Gateway Load Balancer und Partner-NVAs

Azure DDoS Protection ist eine Always-On-, aber keine Inline-Lösung, und es vergehen 30–60 Sekunden vom Zeitpunkt der Erkennung eines Angriffs bis zu seiner Entschärfung. Azure DDoS Protection Standard arbeitet außerdem auf L3/4-Ebene (Netzwerkschicht) und führt keine Untersuchung der Paketnutzdaten durch, d. h. auf Anwendungsebene (L7).  
Workloads mit hoher Empfindlichkeit in Bezug auf die Latenzzeiten, die keine 30–60 Sekunden Anlaufzeit tolerieren können, bis der DDoS-Schutz greift, benötigen Inline-Schutz. Inline-Schutz bedeutet, dass der gesamte Datenverkehr immer die Pipeline für den DDoS-Schutz durchläuft. Darüber hinaus ist es für Szenarien wie den Webschutz oder den Schutz von Gaming-Workloads (UDP) von entscheidender Bedeutung, die Nutzdaten der Pakete zu untersuchen, um Low-Volume-Angriffe abzuschwächen, die eine Schwachstelle in der Anwendungsschicht (L7) ausnutzen. 

Partner-NVAs, die mit Gateway Load Balancer bereitgestellt werden und in Azure DDoS Protection Standard integriert sind, bieten umfassenden Inline-L7-DDoS-Schutz für Hochleistungs- und Hochverfügbarkeitsszenarien. Inline-L7-DDoS-Schutz in Kombination mit Azure DDoS Protection Standard bietet umfassenden L3/L7-Schutz gegen High-Volume- und Low-Volume-DDoS-Angriffe. 

## <a name="what-is-a-gateway-load-balancer"></a>Was ist Gateway Load Balancer?
Gateway Load Balancer (Gateway LB) ist eine SKU von Azure Load Balancer, die speziell für Hochleistungs- und Hochverfügbarkeitsszenarien mit Network Virtual Appliances (NVAs) von Drittanbietern konzipiert ist.

Mit den Funktionen von Gateway LB können Sie NVAs mühelos bereitstellen, skalieren und verwalten – die Verkettung einer Gateway Load Balancer-Instanz mit Ihrem öffentlichen Endpunkt erfordert lediglich einen Klick.  Sie können mit Gateway LB Appliances für eine Vielzahl von Szenarien in den Netzwerkpfad einfügen. Hierzu zählen beispielsweise Szenarien mit Firewalls, erweiterter Paketanalyse, Systeme für Angriffserkennung und -schutz oder benutzerdefinierte, auf Ihre Anforderungen zugeschnittene Szenarien. In Szenarien mit NVAs ist es besonders wichtig, dass die Flows „symmetrisch“ sind – dadurch wird sichergestellt, dass die Sitzungen aufrechterhalten werden und symmetrisch sind. Gateway Load Balancer verwaltet die Flowsymmetrie für eine bestimmte Instanz im Back-End-Pool.

Weitere Informationen zu Gateway Load Balancer finden Sie in den Produktinformationen und der Dokumentation zu [Gateway Load Balancer](https://aka.ms/gatewaylb). 

## <a name="inline-ddos-protection-with-gateway-lb-and-partner-nvas"></a>Inline-DDoS-Schutz mit Gateway LB und Partner-NVAs

DDoS-Angriffe auf Workloads mit hoher Latenzempfindlichkeit (z. B. Spiele) können Ausfälle von 2–10 Sekunden verursachen, die zu einer Unterbrechung der Verfügbarkeit führen. Gateway Load Balancer ermöglicht den Schutz solcher Workloads, indem sichergestellt wird, dass die relevanten NVAs in den Eingangspfad des Internetdatenverkehrs eingebunden werden. Nach der Verkettung mit einer öffentlichen Load Balancer Standard-Front-End- oder IP-Konfiguration auf einer VM ist keine zusätzliche Konfiguration erforderlich, um sicherzustellen, dass Datenverkehr zum und vom Anwendungsendpunkt an die Gateway LB-Instanz gesendet wird. 

Eingehender Datenverkehr wird immer über die NVAs im Pfad überprüft, und der bereinigte Datenverkehr wird an die Back-End-Infrastruktur (Gameserver) zurückgegeben. 

Der Datenverkehr fließt vom virtuellen Consumernetzwerk zum virtuellen Anbieternetzwerk und anschließend zurück zum virtuellen Consumernetzwerk. Das virtuelle Consumernetzwerk und das virtuelle Anbieternetzwerk können sich in verschiedenen Abonnements, Mandanten oder Regionen befinden, um die Flexibilität zu erhöhen und die Verwaltung zu vereinfachen.

![Inline-DDoS-Schutz über Gateway Load Balancer](./media/ddos-glb.png)
 
Die Aktivierung von Azure DDoS Protection Standard im VNet des öffentlichen Load Balancer Standard-Front-Ends oder im VNet der VM bietet Schutz vor L3/4-DDoS-Angriffen. 
1.  Ungefilterter Gamingdatenverkehr aus dem Internet wird an die öffentliche Load Balancer-IP-Adresse der Gameserver geleitet. 
2.  Ungefilterter Gamingdatenverkehr wird an die private IP-Adresse der verketteten Gateway Load Balancer-Instanz umgeleitet. 
3.  Der ungefilterte Gamingdatenverkehr wird über die Partner-NVAs in Echtzeit auf DDoS-Angriffe überprüft. 
4.  Gefilterter Gamingdatenverkehr wird zur endgültigen Verarbeitung an die Gameserver zurückgesendet.
5.  Azure DDoS Protection Standard auf den Load Balancer-Instanzen der Gameserver bietet Schutz vor L3/4-DDoS-Angriffen, und die DDoS-Schutzrichtlinien werden automatisch auf das Verkehrsprofil der Gameserver und den Anwendungsumfang abgestimmt. 

## <a name="next-steps"></a>Nächste Schritte
- Weitere Informationen zu [Partnern für den Inline-L7-DDoS-Schutz](https://aka.ms/inlineddospartners)
- Weitere Informationen zu [Azure DDoS Protection Standard](https://aka.ms/ddosprotectiondocs)
- Weitere Informationen zu [Gateway Load Balancer](https://aka.ms/gatewaylb)
