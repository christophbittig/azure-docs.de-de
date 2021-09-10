---
title: Architektur von SAP HANA in Azure (große Instanzen) | Microsoft-Dokumentation
description: Lernen Sie die Architektur für die Bereitstellung von SAP HANA in Azure (große Instanzen) kennen.
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/21/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fee1aab009bdbf84acf1a73244d6686db50e4e3f
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/22/2021
ms.locfileid: "114450704"
---
# <a name="sap-hana-large-instances-architecture-on-azure"></a>Architektur von SAP HANA in Azure (große Instanzen)

In diesem Artikel wird die Architektur für die Bereitstellung von SAP HANA in Azure (große Instanzen) (auch als BareMetal-Infrastruktur bezeichnet) beschrieben. 

Allgemein betrachtet befindet sich die SAP-Anwendungsschicht bei SAP HANA in Azure (große Instanzen) auf VMs. Die Datenbankebene basiert auf der von SAP zertifizierten HLI (HANA, große Instanzen). Die HLI befindet sich in der gleichen Azure-Region wie die Azure IaaS-VMs.

> [!NOTE]
> Stellen Sie die SAP-Anwendungsschicht in derselben Azure-Region bereit wie die SAP-Datenbank-Managementsystem(DBMS)-Schicht. Diese Regel ist in veröffentlichten Informationen zu SAP-Workloads in Azure umfassend dokumentiert. 

## <a name="architectural-overview"></a>Übersicht über die Architektur

Die Gesamtarchitektur von SAP HANA in Azure (Große Instanzen) basiert auf einer SAP TDI-zertifizierten Hardwarekonfiguration. Die Hardware ist ein nicht virtualisierter Bare-Metal-Hochleistungsserver für die SAP-HANA-Datenbank. Es bietet Ihnen die Flexibilität, die Ressourcen für die SAP-Anwendungsschicht zu skalieren, um Ihren Anforderungen gerecht zu werden.

![Architekturübersicht über SAP HANA in Azure (große Instanzen)](./media/hana-overview-architecture/image1-architecture.png)

Die dargestellte Architektur ist in drei Abschnitte unterteilt:

- **Rechts**: Zeigt eine lokale Infrastruktur, die verschiedene Anwendungen in Rechenzentren ausführt, sodass Endbenutzer auf branchenspezifische Anwendungen wie SAP zugreifen können. Im Idealfall wird diese lokale Infrastruktur über [ExpressRoute](https://azure.microsoft.com/services/expressroute/) mit Azure verbunden.

- **Mitte**: Zeigt Azure-IaaS und (in diesem Fall) die Verwendung von VMs zum Hosten von SAP- oder anderen Anwendungen, die SAP HANA als DBMS nutzen. Kleinere HANA-Instanzen, die mit dem von VMs bereitgestellten Arbeitsspeicher arbeiten, werden zusammen mit ihrer Anwendungsschicht auf VMs bereitgestellt. Weitere Informationen zu VMs finden Sie unter [Virtuelle Computer](https://azure.microsoft.com/services/virtual-machines/).

   Azure-Netzwerkdienste werden verwendet, um SAP-Systeme mit anderen Anwendungen in virtuellen Netzwerken zu gruppieren. Diese virtuellen Netzwerke stellen eine Verbindung mit lokalen Systemen und SAP HANA in Azure (große Instanzen) her.

   Informationen zu SAP NetWeaver-Anwendungen und Datenbanken, deren Ausführung in Azure unterstützt wird, finden Sie im [SAP-Supporthinweis 1928533 – SAP-Anwendungen in Azure: Unterstützte Produkte und Azure-VM-Typen](https://launchpad.support.sap.com/#/notes/1928533). Eine Dokumentation zur Bereitstellung von SAP-Lösungen in Azure finden Sie in den Artikeln zu folgenden Themen:

  -  [Verwenden von SAP auf Windows-VMs](./get-started.md?toc=/azure/virtual-machines/linux/toc.json)
  -  [Verwenden von SAP-Lösungen auf Azure-VMs](get-started.md)

- **Links:** Zeigt die SAP HANA TDI-zertifizierte Hardware im Azure-Umfeld der großen Instanz. Die Einheiten von HANA (große Instanzen) sind mit den virtuellen Netzwerken Ihres Azure-Abonnements verbunden. Dies geschieht mithilfe der gleichen Technologie, die lokale Server für die Verbindung mit Azure verwenden. Im Mai 2019 wurde eine Optimierung eingeführt, die die Kommunikation zwischen den Einheiten von HANA (große Instanzen) und den Azure-VMs ohne ExpressRoute-Gateways ermöglicht. Diese Optimierung, die als ExpressRoute FastPath bezeichnet wird, wird im vorherigen Diagramm durch rote Linien dargestellt.

## <a name="components-of-the-azure-large-instance-stamp"></a>Komponenten des Azure-Stapels für große Instanzen

Im Azure-Stapel für große Instanzen selbst werden die folgenden Komponenten kombiniert:

- **Computing**: Auf verschiedenen Generationen von Intel Xeon-Prozessoren basierende Server, welche die erforderliche Rechenleistung bieten und SAP HANA-zertifiziert sind.
- **Netzwerk:** Ein einheitliches Hochgeschwindigkeitsnetzwerk-Fabric, das die Computing-, Speicher- und LAN-Komponenten miteinander verbindet.
- **Speicher:** Eine Speicherinfrastruktur, auf die über ein einheitliches Netzwerkfabric zugegriffen wird. Die verfügbare Speicherkapazität hängt von der konkret bereitgestellten Konfiguration von SAP HANA in Azure (große Instanzen) ab. Mehr Speicherkapazität ist gegen eine zusätzliche monatliche Gebühr erhältlich.

## <a name="tenants"></a>Mandanten

Innerhalb der mehrinstanzenfähigen Infrastruktur des Stapels für große Instanzen werden Kunden als isolierte Mandanten bereitgestellt. Bei der Bereitstellung des Mandanten benennen Sie ein Azure-Abonnement in Ihrer Azure-Registrierung. Diesem Azure-Abonnement werden die Kosten von HANA (große Instanz) in Rechnung gestellt. Diese Mandanten weisen eine 1:1-Beziehung mit dem Azure-Abonnement auf. 

In einem Netzwerk ist der Zugriff auf eine Einheit von HANA (große Instanz), die in einem Mandanten in einer Azure-Region bereitgestellt ist, über verschiedene, zu unterschiedlichen Azure-Abonnements gehörende virtuelle Netzwerke möglich. Diese Azure-Abonnements müssen zur selben Azure-Registrierung gehören.

## <a name="availability-across-regions"></a>Regionsübergreifende Verfügbarkeit

Wie bei VMs wird SAP HANA in Azure (große Instanzen) in mehreren Azure-Regionen angeboten. Funktionen zur Notfallwiederherstellung können wahlweise abonniert werden. Die verschiedenen Stapel für große Instanzen sind innerhalb einer geopolitischen Azure-Region miteinander verbunden. Umfelder von HANA (große Instanz) in „USA, Westen“ und „USA, Osten“ sind beispielsweise zur Replikation zwecks Notfallwiederherstellung über eine dedizierte Netzwerkverbindung verbunden.

## <a name="available-skus"></a>Verfügbare SKUs

Genauso wie Sie bei Azure zwischen verschiedenen VM-Typen wählen können, können Sie zwischen verschiedenen SKUs von HANA (große Instanzen) wählen. Sie können die SKU auswählen, die für den jeweiligen SAP HANA-Workloadtyp geeignet ist. Für variierende Workloads wendet SAP basierend auf den Intel-Prozessorgenerationen ein geeignetes Verhältnis zwischen Arbeitsspeicher und Prozessorsockets an. Weitere Informationen zu verfügbaren SKUs finden Sie unter [Verfügbare SKUs für HLI](hana-available-skus.md).

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über die Netzwerkarchitektur von SAP HANA (große Instanzen).

> [!div class="nextstepaction"]
> [SAP HANA-Netzwerkarchitektur (große Instanzen)](hana-network-architecture.md)
