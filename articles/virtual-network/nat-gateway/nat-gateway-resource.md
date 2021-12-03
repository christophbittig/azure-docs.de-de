---
title: Entwerfen von virtuellen Netzwerken mit NAT-Gatewayressourcen
titleSuffix: Azure Virtual Network NAT
description: Es wird beschrieben, wie Sie virtuelle Netzwerke mit NAT-Gatewayressourcen entwerfen.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: nat
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/11/2021
ms.author: allensu
ms.openlocfilehash: d0af0d38ef53e4ba90dcf0e7f4cbcc60c52b87a8
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132373135"
---
# <a name="designing-virtual-networks-with-nat-gateway-resources"></a>Entwerfen von virtuellen Netzwerken mit NAT-Gatewayressourcen

NAT-Gatewayressourcen sind Teil von [Virtual Network NAT](nat-overview.md) und stellen die Internetverbindung in ausgehender Richtung für ein oder mehrere Subnetze eines virtuellen Netzwerks bereit. Im Subnetz des virtuellen Netzwerks wird angegeben, welches NAT-Gateway verwendet wird. NAT ermöglicht die „Übersetzung der Quellnetzwerkadresse“ (Source Network Address Translation, SNAT) für ein Subnetz.  Mit NAT-Gatewayressourcen wird angegeben, welche statischen IP-Adressen von virtuellen Computern beim Erstellen ausgehender Datenflüsse verwendet werden. Statische IP-Adressen stammen aus Ressourcen für öffentliche IP-Adressen (PIP), Ressourcen für öffentliche IP-Adresspräfixe oder aus beiden Quellen. Wird eine Präfixressource für öffentliche IP-Adressen verwendet, werden alle IP-Adressen der gesamten Präfixressource für öffentliche IP-Adressen von einer NAT-Gatewayressource genutzt. Für eine NAT-Gatewayressource können für die beiden Quellen jeweils insgesamt bis zu 16 statische IP-Adressen verwendet werden.

<p align="center">
  <img src="media/nat-overview/flow-direction1.svg" alt="Figure depicts a NAT gateway resource that consumes all IP addresses for a public IP prefix and directs that traffic to and from two subnets of virtual machines and a virtual machine scale set." width="256" title="Virtual Network NAT für Internetverbindung in ausgehender Richtung">
</p>

*Abbildung: Virtual Network NAT für Internetverbindung in ausgehender Richtung*

## <a name="how-to-deploy-nat"></a>Bereitstellen von NAT

Die Konfiguration und Nutzung von NAT-Gateways wurde bewusst einfach gehalten:  

NAT-Gatewayressource:
- Erstellen einer regionalen oder zonenbezogenen NAT-Gatewayressource
- Zuweisen von IP-Adressen
- Ändern des TCP-Leerlauftimeouts bei Bedarf (optional)  Lesen Sie den Abschnitt [Timer](#timers), <ins>bevor</ins> Sie die Standardeinstellung ändern.

Virtuelles Netzwerk:
- Konfigurieren Sie das Subnetz des virtuellen Netzwerks so, dass ein NAT-Gateway verwendet wird.

Benutzerdefinierte Routen sind nicht erforderlich.


## <a name="design-guidance"></a>Leitfaden zum Entwurf

Lesen Sie diesen Abschnitt, um sich mit den Aspekten in Bezug auf das Entwerfen virtueller Netzwerke mit NAT vertraut zu machen.  

### <a name="connecting-to-azure-services"></a>Herstellen einer Verbindung mit Azure-Diensten

Beim Herstellen einer Verbindung mit Azure-Diensten wird empfohlen, [Private Link](../../private-link/private-link-overview.md) zu nutzen. 

Mit Private Link werden Azure-Dienstressourcen an Ihr virtuelles Netzwerk gebunden und damit wird auch der Zugriff auf Ihre Azure-Dienstressourcen gesteuert. Wenn Sie beispielsweise auf Azure Storage zugreifen, verwenden Sie einen privaten Endpunkt für den Speicher, um sicherzustellen, dass Ihre Verbindung vollständig privat ist.

### <a name="connecting-to-the-internet"></a>Herstellen einer Verbindung mit dem Internet

NAT wird für ausgehende Szenarien für alle Produktionsworkloads empfohlen, bei denen Sie eine Verbindung mit einem öffentlichen Endpunkt herstellen müssen. Die folgenden Szenarien sind Beispiele dafür, wie Sie sicherstellen können, dass eingehender Datenverkehr mit NAT-Gateway für ausgehenden Datenverkehr zugleich vorhanden ist.

#### <a name="nat-and-vm-with-instance-level-public-ip"></a>NAT und VM mit öffentlicher IP-Adresse auf Instanzebene

<p align="center">
  <img src="media/nat-overview/flow-direction2.svg" alt="Figure depicts a NAT gateway that supports outbound traffic to the internet from a virtual network and inbound traffic with an instance-level public IP." width="300" title="Virtual Network NAT und VM mit öffentlicher IP-Adresse auf Instanzebene">
</p>

*Abbildung: Virtual Network NAT und VM mit öffentlicher IP-Adresse auf Instanzebene*

| Direction | Resource |
|:---:|:---:|
| Eingehend | VM mit öffentlicher IP-Adresse auf Instanzebene |
| Ausgehend | NAT Gateway |

Von der VM wird das NAT-Gateway für ausgehenden Datenverkehr genutzt.  Ursprünglich eingehender Datenverkehr ist nicht betroffen.

#### <a name="nat-and-vm-with-standard-public-load-balancer"></a>NAT und virtueller Computer mit öffentlichem Standard Load Balancer

<p align="center">
  <img src="media/nat-overview/flow-direction3.svg" alt="Figure depicts a NAT gateway that supports outbound traffic to the internet from a virtual network and inbound traffic with a public load balancer." width="350" title="Virtual Network NAT und VM mit öffentlichem Load Balancer">
</p>

*Abbildung: Virtual Network NAT und VM mit öffentlichem Load Balancer*

| Direction | Resource |
|:---:|:---:|
| Eingehend | Öffentlicher Load Balancer |
| Ausgehend | NAT Gateway |

Alle Ausgangskonfigurationen einer Lastenausgleichsregel oder von Ausgangsregeln werden durch das NAT-Gateway ersetzt.  Ursprünglich eingehender Datenverkehr ist nicht betroffen.

#### <a name="nat-and-vm-with-instance-level-public-ip-and-standard-public-load-balancer"></a>NAT und virtueller Computer mit öffentlicher IP-Adresse auf Instanzebene und öffentlichem Standard Load Balancer

<p align="center">
  <img src="media/nat-overview/flow-direction4.svg" alt="Figure depicts a NAT gateway that supports outbound traffic to the internet from a virtual network and inbound traffic with an instance-level public IP and a public load balancer." width="425" title="Virtual Network NAT und VM mit öffentlicher IP-Adresse auf Instanzebene und öffentlichem Load Balancer">
</p>

*Abbildung: Virtual Network NAT und VM mit öffentlicher IP-Adresse auf Instanzebene und öffentlichem Load Balancer*

| Direction | Resource |
|:---:|:---:|
| Eingehend | VM mit öffentlicher IP-Adresse auf Instanzebene und öffentlichem Load Balancer |
| Ausgehend | NAT Gateway |

Alle Ausgangskonfigurationen einer Lastenausgleichsregel oder von Ausgangsregeln werden durch das NAT-Gateway ersetzt.  Die VM nutzt auch das NAT-Gateway für ausgehenden Datenverkehr.  Ursprünglich eingehender Datenverkehr ist nicht betroffen.

## <a name="performance"></a>Leistung

Jede NAT-Gatewayressource kann einen Durchsatz von bis zu 50 GBit/s bereitstellen. Zum Aufskalieren können Sie Ihre Bereitstellungen in mehrere Subnetze unterteilen und jedem Subnetz oder jeder Gruppe von Subnetzen ein NAT-Gateway zuweisen.

Jedes NAT-Gateway kann 64.000 TCP- bzw. UDP-Datenflüsse pro zugewiesener ausgehender IP-Adresse unterstützen.  Ausführlichere Informationen finden Sie im folgenden Abschnitt zur Übersetzung der Quellnetzwerkadresse (Source Network Address Translation, SNAT). Spezifische Informationen zur Problembehandlung finden Sie im Artikel [Problembehandlung für Azure Virtual Network NAT-Konnektivität](./troubleshoot-nat.md).

## <a name="source-network-address-translation"></a>Übersetzung der Quellnetzwerkadresse (Source Network Address Translation, SNAT)

Bei der Übersetzung der Quellnetzwerkadresse (Source Network Address Translation, SNAT) wird die Quelle eines Datenflusses so umgeschrieben, dass dieser von einer anderen IP-Adresse ausgeht.  Für NAT-Gatewayressourcen wird eine Variante von SNAT genutzt, die allgemein als Portadressübersetzung (Port Address Translation, PAT) bezeichnet wird. Bei PAT werden die Quelladresse und der Quellport neu geschrieben. Bei SNAT gibt es keine feste Beziehung zwischen der Anzahl von privaten Adressen und den zugehörigen übersetzten öffentlichen Adressen.  

### <a name="fundamentals"></a>Grundlagen

Wir sehen uns ein Beispiel mit vier Datenflüssen an, um das grundlegende Konzept zu beschreiben.  Das NAT-Gateway verwendet die Ressource für öffentliche IP-Adressen 65.52.1.1, und die VM stellt Verbindungen mit 65.52.0.1 her.

| Flow | Quelltupel | Zieltupel |
|:---:|:---:|:---:|
| 1 | 192.168.0.16:4283 | 65.52.0.1:80 |
| 2 | 192.168.0.16:4284 | 65.52.0.1:80 |
| 3 | 192.168.0.17.5768 | 65.52.0.1:80 |

Diese Datenflüsse sehen nach dem PAT-Vorgang ggf. wie folgt aus:

| Flow | Quelltupel | Quelltupel nach SNAT | Zieltupel | 
|:---:|:---:|:---:|:---:|
| 1 | 192.168.0.16:4283 | **65.52.1.1:1234** | 65.52.0.1:80 |
| 2 | 192.168.0.16:4284 | **65.52.1.1:1235** | 65.52.0.1:80 |
| 3 | 192.168.0.17.5768 | **65.52.1.1:1236** | 65.52.0.1:80 |

Dem Ziel wird die Quelle des Datenflusses als 65.52.0.1 (SNAT-Quelltupel) mit dem zugewiesenen Port angezeigt.  Der PAT-Vorgang wie in der obigen Tabelle wird auch als „SNAT mit Portmaskierung“ bezeichnet.  Mehrere private Quellen werden hinter der IP-Adresse und dem Port maskiert.  

#### <a name="source-snat-port-reuse"></a>Wiederverwendung von (SNAT-)Quellports

NAT-Gateways verwenden (SNAT-)Quellports opportunistisch wieder.  Im Folgenden wird dieses Konzept als zusätzlicher Datenfluss neben den obigen Datenflüssen veranschaulicht.  Die VM im Beispiel verfügt über einen Datenfluss zu 65.52.0.2.

| Flow | Quelltupel | Zieltupel |
|:---:|:---:|:---:|
| 4 | 192.168.0.16:4285 | 65.52.0.2:80 |

Ein NAT-Gateway übersetzt Datenfluss 4 wahrscheinlich in einen Port, der auch für andere Ziele verwendet werden kann.  Weitere Informationen zur richtigen Dimensionierung bei der IP-Adressbereitstellung finden Sie unter [Skalierung](#scaling).

| Flow | Quelltupel | Quelltupel nach SNAT | Zieltupel | 
|:---:|:---:|:---:|:---:|
| 4 | 192.168.0.16:4285 | 65.52.1.1:**1234** | 65.52.0.2:80 |

Die Umsetzung der Zuweisung von Quellports im obigen Beispiel ist nicht verbindlich.  Die obige Abbildung enthält nur eine Darstellung des grundlegenden Konzepts.

Die Bereitstellung von SNAT über NAT unterscheidet sich in mehreren Punkten von [Load Balancer](../../load-balancer/load-balancer-outbound-connections.md).

### <a name="on-demand"></a>Bei Bedarf

Von NAT werden bedarfsgesteuerte SNAT-Ports für neue ausgehende Datenverkehrsflüsse bereitgestellt. Alle verfügbaren SNAT-Ports des Bestands werden von allen virtuellen Computern in Subnetzen genutzt, für die NAT konfiguriert ist. 

<p align="center">
  <img src="media/nat-overview/lb-vnnat-chart.svg" alt="Figure depicts inventory of all available SNAT ports used by any virtual machine on subnets configured with N A T." width="550" title="Virtual Network NAT: Bedarfsgesteuerte SNAT in ausgehender Richtung">
</p>

*Abbildung: Virtual Network NAT: Bedarfsgesteuerte SNAT in ausgehender Richtung*

Über die IP-Konfiguration eines virtuellen Computers können je nach Bedarf Datenflüsse in ausgehender Richtung erstellt werden.  Eine Vorabzuteilung (Planung pro Instanz mit Überbereitstellung für Notfälle) ist nicht erforderlich.  

<p align="center">
  <img src="media/nat-overview/exhaustion-threshold.svg" alt="Figure depicts inventory of all available SNAT ports used by any virtual machine on subnets configured with N A T with exhaustion threshold." width="550" title="Unterschiede bei Auslastungsszenarien">
</p>

*Abbildung: Unterschiede bei Auslastungsszenarien*

Nach der Freigabe eines SNAT-Ports kann dieser von allen virtuellen Computern in Subnetzen genutzt werden, für die NAT konfiguriert ist.  Bei der bedarfsgesteuerten Zuteilung können SNAT-Ports von dynamischen und divergenten Workloads in Subnetzen je nach Bedarf genutzt werden.  Solange ein Bestand an SNAT-Ports verfügbar ist, sind SNAT-Datenflüsse erfolgreich. SNAT-Port-Hotspots profitieren dagegen von einem größeren Bestand. SNAT-Ports bleiben für virtuelle Computer, die diese nicht aktiv benötigen, nicht ungenutzt.

### <a name="scaling"></a>Skalierung

Die Skalierung von NAT ist hauptsächlich eine Funktion, bei der es um die Verwaltung des freigegebenen verfügbaren SNAT-Portbestands geht. Für NAT ist ein ausreichender Bestand an SNAT-Ports erforderlich, um für alle Subnetze einer NAT-Gatewayressource die erwartete Spitzenlast für ausgehende Datenflüsse abdecken zu können.  Sie können öffentliche IP-Adressressourcen, öffentliche IP-Präfixressourcen oder beides verwenden, um einen Bestand an SNAT-Ports zu erstellen.  

>[!NOTE]
>Wenn Sie eine Präfixressource für öffentliche IP-Adressen zuweisen, wird das gesamte Präfix für öffentliche IP-Adressen verwendet.  Sie können keine Präfixressource für öffentliche IP-Adressen zuweisen und dann einzelne IP-Adressen anderen Ressourcen zuweisen.  Wenn Sie einzelne IP-Adressen aus einem Präfix für öffentliche IP-Adressen mehreren Ressourcen zuweisen möchten, müssen Sie einzelne öffentliche IP-Adressen aus der Präfixressource für öffentliche IP-Adressen erstellen und sie bei Bedarf anstelle dieser Präfixressource zuweisen.

Bei SNAT werden private Adressen mindestens einer öffentlichen IP-Adresse zugeordnet. Hierbei werden die Quelladresse und der Quellport neu erstellt. Für eine NAT-Gatewayressource werden für diesen Übersetzungsvorgang 64.000 Ports (SNAT-Ports) pro konfigurierter öffentlicher IP-Adresse verwendet. NAT-Gatewayressourcen können auf bis zu 16 IP-Adressen und 1 Million SNAT-Ports hochskaliert werden. Wenn eine öffentliche IP-Präfixressource bereitgestellt wird, wird für jede IP-Adresse des Präfixbereichs ein Bestand an SNAT-Ports angelegt. Wenn Sie weitere öffentliche IP-Adressen hinzufügen, wird die Anzahl von verfügbaren SNAT-Ports des Bestands erhöht. Für TCP und UDP werden separate SNAT-Portbestände verwendet, die nicht miteinander in Beziehung stehen.

NAT-Gatewayressourcen verwenden (SNAT-)Quellports opportunistisch wieder. Als Orientierung beim Entwurf sollten Sie für Skalierungszwecke davon ausgehen, dass für jeden Datenfluss ein neuer SNAT-Port benötigt wird, und die Gesamtanzahl von verfügbaren IP-Adressen für ausgehenden Datenverkehr entsprechend skalieren.  Sie sollten die geplante Größe sorgfältig berücksichtigen und eine entsprechende Anzahl von IP-Adressen bereitstellen.

SNAT-Ports für verschiedene Ziele werden höchstwahrscheinlich nach Möglichkeit wiederverwendet, und wenn die SNAT-Ports fast ausgelastet sind, tritt für die Datenflüsse möglicherweise ein Fehler auf.  

Weitere Informationen finden Sie z. B. im Abschnitt über [SNAT-Grundlagen](#source-network-address-translation).


### <a name="protocols"></a>Protokolle

NAT-Gatewayressourcen interagieren mit IP- und IP-Transportheadern von UDP- und TCP-Datenflüssen und sind gegenüber Nutzlasten auf Anwendungsebene agnostisch.  Andere IP-Protokolle werden nicht unterstützt.

### <a name="timers"></a>Timer

>[!IMPORTANT]
>Ein hoher Wert für den Leerlauftimer kann dazu führen, dass die Wahrscheinlichkeit einer SNAT-Überlastung unnötigerweise zunimmt. Je höher der von Ihnen angegebene Timerwert ist, desto länger werden die SNAT-Ports vom NAT-Vorgang belegt, bis der Zeitpunkt für das Leerlauftimeout erreicht ist. Wenn für Ihre Datenflüsse das Leerlauftimeout erreicht wurde, tritt hierfür sowieso ein Fehler auf, und es kommt zu einem unnötigen Verbrauch von SNAT-Ports aus dem Bestand.  Für Datenflüsse, für die nach zwei Stunden ein Fehler auftritt, wäre dies auch nach dem Standardzeitraum von vier Minuten der Fall gewesen. Die Verlängerung des Zeitraums für das Leerlauftimeout ist nur das letzte Mittel, das Sie sehr sparsam einsetzen sollten. Wenn sich ein Datenfluss niemals im Leerlaufzustand befindet, wird er durch das Leerlauftimeout nicht beeinträchtigt.

Das TCP-Leerlauftimeout kann für alle Datenflüsse auf einen Wert zwischen vier Minuten (Standardwert) und 120 Minuten (2 Stunden) festgelegt werden.  Darüber hinaus können Sie den Leerlaufzeitgeber mit Datenverkehr im Datenfluss zurücksetzen.  Ein empfohlenes Muster zur Aktualisierung von Verbindungen mit langer Leerlaufzeit und zur Erkennung des Livezustands von Endpunkten sind TCP-Keep-Alives.  TCP-Keep-Alives werden als doppelte ACK-Vorgänge für die Endpunkte angezeigt und sind mit geringem Aufwand verbunden und für die Anwendungsschicht unsichtbar.

Die folgenden Zeitgeber werden für die Freigabe von SNAT-Ports verwendet:

| Zeitgeber | Wert |
|---|---|
| TCP FIN (TCP-Beendigung) | 60 Sekunden |
| TCP RST (TCP-Zurücksetzung) | 10 Sekunden |
| TCP half open (TCP halb geöffnet) | 30 Sekunden |

Ein SNAT-Port ist für eine IP-Zieladresse und den entsprechenden Zielport nach fünf Sekunden bereit für die Wiederverwendung.

>[!NOTE] 
>Diese Zeitgebereinstellungen können sich ändern. Die Werte sind hier nur als Hilfe bei der Problembehandlung angegeben, und Sie sollten noch keine Abhängigkeit von bestimmten Zeitgebern einrichten.

## <a name="limitations"></a>Einschränkungen

- Grundlegende Load Balancer und öffentliche IP-Adressen im Basis-Standard sind nicht mit NAT kompatibel. Verwenden Sie stattdessen Load Balancer der Standard-SKU und öffentliche IP-Dateien.
- Die IP-Fragmentierung wird über das NAT-Gateway nicht unterstützt.

## <a name="next-steps"></a>Nächste Schritte

* Informieren Sie sich über [Virtual Network NAT](nat-overview.md).
* Informieren Sie sich über [Metriken und Warnungen für NAT-Gatewayressourcen](nat-metrics.md).
* Informieren Sie sich über die [Problembehandlung im Zusammenhang mit der Azure Virtual Network NAT-Konnektivität](troubleshoot-nat.md).
