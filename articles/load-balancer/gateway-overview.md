---
title: Gateway-Lastverteiler (Vorschau)
titleSuffix: Azure Load Balancer
description: Übersicht über den Gateway-Lastverteiler-SKU für den Azure Lastenausgleich.
ms.service: load-balancer
author: asudbring
ms.author: allensu
ms.date: 11/02/2021
ms.topic: conceptual
ms.custom: ignite-fall-2021
ms.openlocfilehash: 22548d5bb75bb70c20296ddd68fde5d232213403
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095916"
---
# <a name="gateway-load-balancer-preview"></a>Gateway-Lastverteiler (Vorschau)

Der Gateway-Lastverteiler ist eine SKU des Azure Lastenausgleich-Portfolios, das für Szenarien mit hoher Leistung und Verfügbarkeit mit virtuellen Netzwerkgeräten von Drittanbietern (Network Virtual Appliances, NVAs) konzipiert ist. Mit den Funktionen des Gateway-Lastverteilers können Sie NVAs problemlos bereitstellen, skalieren und verwalten. Das Verknüpfen eines Gateways-Lastverteilers mit Ihrem öffentlichen Endpunkt erfordert nur einen Klick. 

Sie können Appliances für verschiedene Arten von Szenarien transparent einfügen, beispielsweise:

* Firewalls
* Erweiterte Paketanalyse
* Angriffserkennungs- und Schutzsysteme
* Datenverkehrsspiegelung
* Inline-DDoS
* Benutzerdefinierte Appliances

Mit dem Gateway-Lastenverteiler können Sie ohne zusätzlichen Verwaltungsaufwand problemlos erweiterte Netzwerkfunktionen hinzufügen oder entfernen. Er bietet die Technologie, die Sie benötigen, um sicherzustellen, dass der gesamte Datenverkehr an einen öffentlichen Endpunkt vor Ihrer Anwendung zuerst an die Appliance gesendet wird. In Szenarien mit NVAs ist es besonders wichtig, dass Flows symmetrisch sind. Der Gateway Lastverteiler sorgt dafür, dass der Datenfluss an eine bestimmte Instanz im Backend-Pool gebunden bleibt und die Datenflüsse symmetrisch sind. Daher wird eine konsistente Route zu Ihrem virtuellen Netzwerkgerät sichergestellt – ohne zusätzliche manuelle Konfiguration. Daher durchlaufen Pakete den gleichen Netzwerkpfad in beide Richtungen, und Appliances, die diese benötigen, können nahtlos funktionieren.

Der Integritätstest überprüft sämtliche Ports und leitet Datenverkehr mithilfe der Regel für Ha-Ports an die Back-End-Instanzen weiter. Datenverkehr, der an den und vom Gateway-Lastverteiler gesendet wird, verwendet das VXLAN-Protokoll. 

> [!IMPORTANT]
> Der Gateway-Lastverteiler befindet sich derzeit in der Vorschauphase.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="benefits"></a>Vorteile

Der Gateway-Lastverteiler hat die folgenden Vorteile:

* Integriert virtuelle Geräte transparent in den Netzwerkpfad.

* Einfaches Hinzufügen oder Entfernen virtueller Netzwerkgeräte im Netzwerkpfad. 

* Einfache Skalierung bei gleichzeitiger Kostenverwaltung.

* Verbessern der Verfügbarkeit virtueller Netzwerkgeräte.

* Regions- und abonnementübergreifendes Verketten von Anwendungen

Ein öffentlicher Lastverteiler-Standard oder die IP-Konfiguration eines virtuellen Computers kann mit einem Gateway-Lastverteiler verkettet werden. Nach der Verkettung mit einer öffentlichen Standard-Lastverteiler Front-End- oder IP-Konfiguration auf einem virtuellen Computer ist keine zusätzliche Konfiguration erforderlich, um sicherzustellen, dass Datenverkehr zum und vom Anwendungsendpunkt an den Gateway-Lastverteiler gesendet wird.

Datenverkehr wird vom virtuellen Consumernetzwerk in das virtuelle Netzwerk des Anbieters verschoben. Der Datenverkehr wird dann an das virtuelle Consumernetzwerk zurückgegeben. Das virtuelle Consumernetzwerk und das virtuelle Anbieternetzwerk können zwischen verschiedenen Abonnements, Mandanten oder Regionen bestehen, wodurch Verwaltungsaufwand reduziert wird.

:::image type="content" source="./media/gateway-overview/gateway-load-balancer-diagram.png" alt-text="Diagramm des Gateway-Lastverteilers":::

*Diagramm des Gateway-Lastverteilers*.

## <a name="components"></a>Komponenten

Der Gateway Lastverteiler besteht aus den folgenden Komponenten:

* **Front-End-IP-Konfiguration** – die IP-Adresse Ihres Gateway-Lastverteilers. Diese IP-Adresse ist nur privat. 

* **Lastenausgleichsregeln** – mithilfe einer Lastenausgleichsregel wird definiert, wie eingehender Datenverkehr auf alle Instanzen innerhalb des Back-End-Pools verteilt werden soll. Eine Lastenausgleichsregel ordnet mehreren Back-End-IP-Adressen und Ports eine bestimmte Front-End-IP-Konfiguration und einen Port zu. 

    * Gateway-Lastverteiler Regeln können nur Regeln für Ha-Ports sein. 

    * Eine Gateway-Lastverteiler Regel kann bis zu zwei Back-End-Pools zugeordnet werden. 

* **Back-End-Pools** – die Gruppe von virtuellen Computern oder Instanzen in einer VM-Skalierungsgruppe, die eingehende Anforderungen bearbeitet. Für eine kosteneffiziente Skalierung zur Bewältigung großer Mengen an eingehendem Datenverkehr empfiehlt es sich in der Regel, dem Back-End-Pool weitere Instanzen hinzuzufügen. Die Konfiguration von Load Balancer wird automatisch angepasst, wenn Sie Instanzen hoch- oder herunterskalieren. Durch Hinzufügen bzw. das Entfernen virtueller Computer aus dem Back-End-Pool wird der Lastenausgleich ohne zusätzliche Vorgänge neu konfiguriert. Der Back-End-Pool wird für jeden beliebigen virtuellen Computer in einem einzelnen virtuellen Netzwerk verwendet. 

* **Tunnel Schnittstellen** – Gateway-Lastverteiler Back-End-Pools verfügen über eine weitere Komponente, die als Tunnelschnittstellen bezeichnet wird. Mit der Tunnelschnittstelle können die Appliances im Back-End sicherstellen, dass Netzwerkflows wie erwartet verarbeitet werden. Jeder Back-End-Pool kann bis zu 2 Tunnelschnittstellen aufweisen. Tunnel Schnittstellen können entweder intern oder extern sein. Für Datenverkehr, der in Ihren Back-End-Pool eingeht, sollten Sie den externen Typ verwenden. Für Datenverkehr, der von Ihrer Appliance an die Anwendung geht, sollten Sie den internen Typ verwenden.

* **Kette** – Auf einen Gateway-Lastverteiler kann von einem öffentlichen Standard-Lastverteiler Front-End oder einer öffentlichen IP-Konfiguration auf einem virtuellen Computer verwiesen werden. Das Hinzufügen erweiterter Netzwerkfunktionen in einer bestimmten Sequenz wird als Dienstverkettung bezeichnet. Daher wird dieser Verweis als Kette bezeichnet.

## <a name="pricing"></a>Preise

Gateway-Lastverteiler während der Vorschauversion werden nicht berechnet. 

Informationen zu Preisen, die während der allgemeinen Verfügbarkeit gelten, finden Sie unter [Lastverteiler-Preise](https://azure.microsoft.com/pricing/details/load-balancer/).

## <a name="limitations"></a>Einschränkungen

* Gateway-Lastverteiler gibt es nicht im Tarif Allgemeiner Lastverteiler.
* Die Unterstützung von Gateway-Lastverteiler-Portals ist im Cloud- und Regierungs-Cloudbereich in China derzeit nicht verfügbar. CLI, PowerShell, Vorlagen oder Alternativen können verwendet werden.
* Die mandantenübergreifende Verkettung wird über die Azure-Portal nicht unterstützt.

## <a name="next-steps"></a>Nächste Schritte

- Informationen zum Erstellen eines Gateway-Lastverteilers finden Sie unter [Erstellen eines Gateways-Lastverteilers mithilfe des Azure-Portals](tutorial-gateway-portal.md).
- Weitere Informationen zu [Azure Load Balancer](load-balancer-overview.md).
