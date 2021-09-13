---
title: Zertifizierung von SAP HANA in Azure (große Instanzen) | Microsoft-Dokumentation
description: Erfahren Sie mehr über die Zertifizierung von SAP HANA in Azure (große Instanzen).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/14/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ade0e82131f65183ac9471bb77d0ab0cc92ae285
ms.sourcegitcommit: 192444210a0bd040008ef01babd140b23a95541b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/15/2021
ms.locfileid: "114219638"
---
# <a name="certification"></a>Zertifizierung

Neben der NetWeaver-Zertifizierung erfordert SAP eine spezielle Zertifizierung für SAP HANA, damit SAP HANA auf bestimmten Infrastrukturen unterstützt wird, z. B. Azure IaaS und BareMetal-Infrastruktur.

Der wichtigste SAP-Hinweis zu NetWeaver und in gewissem Maße zur SAP HANA-Zertifizierung ist [SAP-Hinweis 1928533 – SAP-Anwendungen in Azure: Unterstützte Produkte und Azure-VM-Typen](https://launchpad.support.sap.com/#/notes/1928533).

Die Zertifizierungsdatensätze für SAP HANA in Azure (große Instanzen) finden Sie auf der Website [Zertifizierte SAP HANA-IaaS-Plattformen](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). 

Die SAP HANA-Typen in Azure (große Instanzen), die auf der Website als „zertifizierte IaaS-Plattformen“ bezeichnet werden, bieten Microsoft- und SAP-Kunden die Möglichkeit, Folgendes bereitzustellen:

- Large SAP Business Suite
- SAP BW
- S/4 HANA
- BW/4HANA
- Andere SAP HANA-Workloads in Azure. 

Die Lösung beruht auf dem dedizierten SAP HANA-zertifizierten Hardwareumfeld ([SAP HANA Tailored Datacenter Integration – TDI](https://scn.sap.com/docs/DOC-63140)). Wenn Sie eine mit SAP HANA TDI konfigurierte Lösung ausführen, funktionieren alle oben SAP HANA-basierten Anwendungen in der Hardwareinfrastruktur.

Im Vergleich zum Ausführen von SAP HANA auf virtuellen Computern bietet diese Lösung den Vorteil deutlich größerer Arbeitsspeichervolumes. 

## <a name="key-concepts"></a>Wichtige Begriffe

Um diese Lösung nutzen zu können, müssen Sie die folgenden wichtigen Aspekte verstehen:

- Die SAP-Anwendungsschicht und Nicht-SAP-Anwendungen werden auf VMs ausgeführt, die in den üblichen Azure-Hardwareumfeldern gehostet werden.
- Die lokale(n) Infrastruktur, Rechenzentren und Anwendungsbereitstellungen des Kunden werden über ExpressRoute (empfohlen) oder ein virtuelles privates Netzwerk (VPN) mit der Cloudplattform verbunden. Auch Active Directory und DNS werden auf Azure erweitert.
- Die SAP HANA-Datenbankinstanz für HANA-Workload wird auf SAP HANA in Azure (große Instanzen) ausgeführt. Das Umfeld von HANA (große Instanz) ist mit dem Azure-Netzwerk verbunden, sodass auf VMs ausgeführte Software mit der HANA-Instanz interagieren kann, die in HANA (große Instanz) ausgeführt wird.
- Hardware von SAP HANA in Azure (große Instanzen) ist dedizierte Hardware, die in einer IaaS mit vorinstalliertem SUSE Linux Enterprise Server oder Red Hat Enterprise Linux bereitgestellt wird. Wie bei VMs sind Sie für weitere Updates und die Wartung des Betriebssystems zuständig.
- Die Installation von HANA oder weiterer Komponenten, die zum Ausführen von SAP HANA auf Einheiten von HANA (große Instanz) erforderlich sind, liegt in Ihrer Verantwortung. Sie sind auch für den gesamten laufenden Betrieb und die laufende Verwaltung von SAP HANA in Azure zuständig.
- Sie können auch andere Komponenten in Ihrem Azure-Abonnement installieren, die eine Verbindung mit SAP HANA in Azure (große Instanzen) herstellen. Beispielsweise Komponenten, die die Kommunikation mit der SAP HANA ermöglichen, z. B.:        
    - Jumpserver
    - RDP-Server
    - SAP HANA Studio
    - SAP Data Services für SAP BI-Szenarios
    - Netzwerküberwachungslösungen.
- Wie in Azure bietet HANA (große Instanz) Unterstützung für Funktionen für Hochverfügbarkeit und Notfallwiederherstellung.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über verfügbare SKUs für SAP HANA (große Instanzen).

> [!div class="nextstepaction"]
> [Verfügbare SKUs für HLI](hana-available-skus.md)
