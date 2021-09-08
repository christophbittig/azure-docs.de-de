---
title: Verwenden von Azure Private Link zum Verbinden von Netzwerken mit Azure Monitor
description: Richten Sie Azure Monitor Private Link Scope ein, um Netzwerke sicher mit Azure Monitor zu verbinden.
author: noakup
ms.author: noakuper
ms.topic: conceptual
ms.date: 10/05/2020
ms.openlocfilehash: bdd47962b56324f9832070b13644b5489ee38989
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122354932"
---
# <a name="use-azure-private-link-to-connect-networks-to-azure-monitor"></a>Verwenden von Azure Private Link zum Verbinden von Netzwerken mit Azure Monitor

Mit [Azure Private Link](../../private-link/private-link-overview.md) können Sie Azure-PaaS-Dienste (Platform-as-a-Service) über private Endpunkte sicher mit Ihrem virtuellen Netzwerk verknüpfen. Bei vielen Diensten richten Sie einfach für jede Ressource einen Endpunkt ein. Azure Monitor ist jedoch eine Zusammenstellung verschiedener miteinander verbundener Dienste, die zur Überwachung Ihrer Workloads ineinandergreifen. 

## <a name="advantages"></a>Vorteile

Private Link bietet folgende Möglichkeiten:

- Herstellen einer privaten Verbindung mit Azure Monitor ohne öffentlichen Netzwerkzugriff
- Sicherstellen, dass der Zugriff auf Überwachungsdaten nur über autorisierte private Netzwerke erfolgt
- Verhindern von Datenexfiltration aus Ihren privaten Netzwerken durch Definieren bestimmter Azure Monitor-Ressourcen, die eine Verbindung über Ihren privaten Endpunkt herstellen
- Sichere Verbindung zwischen Ihrem privaten lokalen Netzwerk und Azure Monitor über ExpressRoute und Private Link
- Kapseln des gesamten Datenverkehrs innerhalb des Microsoft Azure-Backbonenetzwerks

Weitere Informationen finden Sie unter [Hauptvorteile von Private Link](../../private-link/private-link-overview.md#key-benefits).

## <a name="how-it-works"></a>Funktionsweise

Azure Monitor Private Link Scope (AMPLS) stellt Verbindungen privater Endpunkte (und der darin enthaltenen VNETs) mit einer oder mehreren Azure Monitor-Ressourcen her – Log Analytics-Arbeitsbereichen und Application Insights-Komponenten.

![Diagramm der grundlegenden Ressourcentopologie](./media/private-link-security/private-link-basic-topology.png)

* Der private Endpunkt in Ihrem VNET ermöglicht, Azure Monitor-Endpunkte über private IP-Adressen aus dem Pool Ihres Netzwerks zu erreichen, anstatt die öffentlichen IPs dieser Endpunkte zu verwenden. Auf diese Weise können Sie Ihre Azure Monitor-Ressourcen weiterhin verwenden, ohne das VNET für nicht benötigten ausgehenden Datenverkehr zu öffnen. 
* Der Datenverkehr vom privaten Endpunkt zu Ihren Azure Monitor-Ressourcen wird über das Microsoft Azure-Backbone und nicht über öffentliche Netzwerke weitergeleitet. 
* Sie können Ihre einzelnen Arbeitsbereiche oder Komponenten so konfigurieren, dass Erfassung und Abfragen von öffentlichen Netzwerken zugelassen oder verweigert werden. Dies bietet Schutz auf Ressourcenebene, sodass Sie den Datenverkehr zu bestimmten Ressourcen kontrollieren können.

> [!NOTE]
> Eine einzelne Azure Monitor-Ressource kann zu mehreren Azure Monitor Private Link-Bereichen gehören, aber Sie können ein einzelnes virtuelles Netzwerk nicht mit mehr als einem Bereich verbinden. 

### <a name="azure-monitor-private-links-and-your-dns-its-all-or-nothing"></a>Azure Monitor Private Link und Ihr DNS – hier gilt alles oder nichts
Einige Azure Monitor-Dienste verwenden globale Endpunkte. Dies bedeutet, dass sie Anforderungen aller Arbeitsbereiche und Komponenten erfüllen. Wenn Sie eine Private Link-Verbindung einrichten, wird Ihr DNS so aktualisiert, dass Azure Monitor-Endpunkte privaten IP-Adressen zugeordnet werden, damit Datenverkehr über Private Link gesendet werden kann. Im Fall globaler Endpunkte wirkt sich das Einrichten von Private Link (auch für eine einzelne Ressource) auf den Datenverkehr zu allen Ressourcen aus. Anders ausgedrückt: Es ist nicht möglich, eine Private Link-Verbindung nur für eine bestimmte Komponente oder einen bestimmten Arbeitsbereich herzustellen.

#### <a name="global-endpoints"></a>Globale Endpunkte
Am wichtigsten ist, dass Datenverkehr an die folgenden globalen Endpunkte über Private Link gesendet wird:
* Alle Application Insights-Endpunkte: Endpunkte für die Erfassung, Livemetriken, Profiler, Debugger usw. für Application Insights-Endpunkte sind global.
* Der Abfrageendpunkt: Der Endpunkt für Abfragen an Application Insights- und Log Analytics-Ressourcen ist global.

Dies bedeutet, dass der gesamte Application Insights-Datenverkehr an Private Link gesendet wird und dass alle Abfragen – sowohl an Application Insights- als auch an Log Analytics-Ressourcen – an Private Link gesendet werden.

Datenverkehr an Application Insights-Ressourcen, die Ihrem AMPLS nicht hinzugefügt wurden, besteht die Private Link-Validierung nicht und wird nicht gesendet.

![Diagramm des Alles-oder-nichts-Verhaltens](./media/private-link-security/all-or-nothing.png)

#### <a name="resource-specific-endpoints"></a>Ressourcenspezifische Endpunkte
Alle Log Analytics-Endpunkte, mit Ausnahme des Abfrageendpunkts, sind arbeitsbereichsspezifisch. Das Erstellen einer Private Link-Instanz für einen bestimmten Log Analytics-Arbeitsbereich wirkt sich daher nicht auf die Erfassung (oder anderen) Datenverkehr an andere Arbeitsbereiche aus, die weiterhin die öffentlichen Log Analytics-Endpunkte verwenden. Alle Abfragen werden jedoch über Private Link gesendet.

### <a name="azure-monitor-private-link-applies-to-all-networks-that-share-the-same-dns"></a>Azure Monitor Private Link gilt für alle Netzwerke, die dasselbe DNS verwenden.
Einige Netzwerke bestehen aus mehreren VNETs oder anderen verbundenen Netzwerken. Wenn diese Netzwerke dasselbe DNS nutzen, würde das Einrichten einer Private Link-Instanz für eines der Netzwerke das DNS aktualisieren und sich auf den Datenverkehr in allen Netzwerken auswirken. Dies ist aufgrund des oben beschriebenen Alles-oder-nichts-Verhaltens besonders wichtig.

![Diagramm der DNS-Außerkraftsetzungen in mehreren VNETs](./media/private-link-security/dns-overrides-multiple-vnets.png)

Im obigen Diagramm stellt VNET 10.0.1.x zuerst eine Verbindung mit AMPLS1 her und ordnet IP-Adressen aus seinem Bereich die globalen Azure Monitor-Endpunkte zu. Später stellt VNET 10.0.2.x eine Verbindung mit AMPLS2 her und setzt mit IP-Adressen aus seinem Bereich die DNS-Zuordnung der *gleichen globalen Endpunkte* außer Kraft. Da zwischen diesen VNETs keine Peerbeziehung besteht, kann das erste VNET diese Endpunkte nun nicht erreichen.


## <a name="next-steps"></a>Nächste Schritte
- [Entwerfen Ihres Private Link-Setups](private-link-design.md)
- Weitere Informationen zum [Konfigurieren von Private Link](private-link-configure.md)

<h3><a id="connect-to-a-private-endpoint"></a></h3>
