---
title: Verwenden von Azure Private Link zum Verbinden von Netzwerken mit Azure Monitor
description: Richten Sie Azure Monitor Private Link Scope ein, um Netzwerke sicher mit Azure Monitor zu verbinden.
author: noakup
ms.author: noakuper
ms.topic: conceptual
ms.date: 10/05/2020
ms.openlocfilehash: 872a9c1f58974f1394286a6dd8f2a1c35892930c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131078178"
---
# <a name="use-azure-private-link-to-connect-networks-to-azure-monitor"></a>Verwenden von Azure Private Link zum Verbinden von Netzwerken mit Azure Monitor

Mit [Azure Private Link](../../private-link/private-link-overview.md) können Sie Azure-PaaS-Ressourcen (Platform-as-a-Service) über private Endpunkte sicher mit Ihrem virtuellen Netzwerk verknüpfen. Azure Monitor ist eine Zusammenstellung verschiedener miteinander verbundener Dienste, die zur Überwachung Ihrer Workloads ineinandergreifen. Ein Azure Monitor Private Link verbindet einen privaten Endpunkt mit einer Reihe von Azure Monitor-Ressourcen und definiert so die Grenzen Ihres Überwachungsnetzwerks. Dieser Satz wird als AMPLS (Azure Monitor Private Link Scope) bezeichnet.


## <a name="advantages"></a>Vorteile

Private Link bietet folgende Möglichkeiten:

- Herstellen einer privaten Verbindung mit Azure Monitor ohne öffentlichen Netzwerkzugriff
- Sicherstellen, dass der Zugriff auf Überwachungsdaten nur über autorisierte private Netzwerke erfolgt
- Verhindern von Datenexfiltration aus Ihren privaten Netzwerken durch Definieren bestimmter Azure Monitor-Ressourcen, die eine Verbindung über Ihren privaten Endpunkt herstellen
- Sichere Verbindung zwischen Ihrem privaten lokalen Netzwerk und Azure Monitor über ExpressRoute und Private Link
- Kapseln des gesamten Datenverkehrs innerhalb des Microsoft Azure-Backbonenetzwerks

Weitere Informationen finden Sie unter [Hauptvorteile von Private Link](../../private-link/private-link-overview.md#key-benefits).

## <a name="how-it-works"></a>Funktionsweise

### <a name="overview"></a>Übersicht
Ein Azure Monitor Private Link-Bereich stellt Verbindungen privater Endpunkte (und der darin enthaltenen VNets) mit einer oder mehreren Azure Monitor-Ressourcen her – Log Analytics-Arbeitsbereichen und Application Insights-Komponenten.

![Diagramm der grundlegenden Ressourcentopologie](./media/private-link-security/private-link-basic-topology.png)

* Ein Azure Monitor Private Link stellt eine Verbindung zwischen einem privaten Endpunkt und einer Reihe von Azure Monitor-Ressourcen her – Log Analytics-Arbeitsbereiche und Application Insights-Ressourcen. Dieser Satz wird als AMPLS (Azure Monitor Private Link Scope) bezeichnet.
* Der private Endpunkt in Ihrem VNET ermöglicht, Azure Monitor-Endpunkte über private IP-Adressen aus dem Pool Ihres Netzwerks zu erreichen, anstatt die öffentlichen IPs dieser Endpunkte zu verwenden. Auf diese Weise können Sie Ihre Azure Monitor-Ressourcen weiterhin verwenden, ohne das VNET für nicht benötigten ausgehenden Datenverkehr zu öffnen. 
* Der Datenverkehr vom privaten Endpunkt zu Ihren Azure Monitor-Ressourcen wird über das Microsoft Azure-Backbone und nicht über öffentliche Netzwerke weitergeleitet.
* Sie können Ihren Azure Monitor Private Link-Bereich (oder bestimmte Netzwerke, die eine Verbindung mit ihm herstellen) so konfigurieren, dass er den bevorzugten Zugriffsmodus verwendet – entweder nur Datenverkehr zu Private Link-Ressourcen zulässt oder Datenverkehr sowohl zu Private Link-Ressourcen als auch zu Nicht-Private Link-Ressourcen (Ressourcen außerhalb des AMPLS) zulässt.
* Sie können Ihre einzelnen Arbeitsbereiche oder Komponenten so konfigurieren, dass Erfassung und Abfragen von öffentlichen Netzwerken zugelassen oder verweigert werden. Dies bietet Schutz auf Ressourcenebene, sodass Sie den Datenverkehr zu bestimmten Ressourcen kontrollieren können.

> [!NOTE]
> Ein VNet kann sich nur mit einem einzelnen AMPLS verbinden, der bis zu 50 Ressourcen auflistet, die über eine Private Link-Instanz erreicht werden können.

### <a name="azure-monitor-private-link-relies-on-your-dns"></a>Azure Monitor Private Link basiert auf Ihrem DNS
Wenn Sie eine Private Link-Verbindung einrichten, werden Ihre DNS-Zonen so festgelegt, dass Azure Monitor-Endpunkte privaten IP-Adressen zugeordnet werden, damit Datenverkehr über Private Link gesendet werden kann. Azure Monitor verwendet sowohl ressourcenspezifische Endpunkte als auch regionale oder globale Endpunkte, die den Datenverkehr zu mehreren Arbeitsbereichen/Komponenten verarbeiten. Bei regionalen und globalen Endpunkten wirkt sich das Einrichten einer Private Link-Instanz (auch für eine einzelne Ressource) auf die DNS-Zuordnung aus, die den Datenverkehr zu **allen** Ressourcen steuert. Mit anderen Worten: Der Datenverkehr zu allen Arbeitsbereichen oder Komponenten könnte von einem einzelnen Private Link-Setup betroffen sein.

#### <a name="global-endpoints"></a>Globale Endpunkte
Am wichtigsten ist, dass Datenverkehr an die folgenden globalen Endpunkte über Private Link gesendet wird:
* Alle Application Insights-Endpunkte: Endpunkte für die Erfassung, Livemetriken, Profiler, Debugger usw. für Application Insights-Endpunkte sind global.
* Der Abfrageendpunkt: Der Endpunkt für Abfragen an Application Insights- und Log Analytics-Ressourcen ist global.

Dies bedeutet, dass der gesamte Application Insights-Datenverkehr an Private Link gesendet wird und dass alle Abfragen – sowohl an Application Insights- als auch an Log Analytics-Ressourcen – an Private Link gesendet werden.

Datenverkehr an Application Insights-Ressourcen, die Ihrem AMPLS nicht hinzugefügt wurden, besteht die Private Link-Validierung nicht und wird nicht gesendet.

#### <a name="resource-specific-endpoints"></a>Ressourcenspezifische Endpunkte
Alle Log Analytics-Endpunkte, mit Ausnahme des Abfrageendpunkts, sind arbeitsbereichsspezifisch. Das Erstellen einer Private Link-Instanz für einen bestimmten Log Analytics-Arbeitsbereich wirkt sich daher nicht auf die Erfassung an andere Arbeitsbereiche aus, die weiterhin die öffentlichen Endpunkte verwenden.


> [!NOTE]
> Erstellen Sie nur einen einzelnen AMPLS für alle Netzwerke, die dasselbe DNS teilen. Das Erstellen mehrerer AMPLS-Ressourcen führt dazu, dass die DNS-Zonen von Azure Monitor sich gegenseitig außer Kraft setzen und bestehende Umgebungen unterbrechen.

### <a name="private-link-access-modes-private-only-vs-open"></a>Private Link-Zugriffsmodi: „Nur privat“ oder „Offen“
Wie in [Azure Monitor Private Link basiert auf Ihrem DNS](#azure-monitor-private-link-relies-on-your-dns) beschrieben, sollte nur eine einzelne AMPLS-Ressource für alle Netzwerke erstellt werden, die dasselbe DNS verwenden. Folglich verfügen Unternehmen, die ein einzelnes globales oder regionales DNS verwenden, über eine einzelne Private Link-Instanz, um den Datenverkehr zu sämtlichen Azure Monitor-Ressourcen über alle globalen oder regionalen Netzwerke hinweg zu verwalten.

Für Private Link-Instanzen, die vor September 2021 erstellt wurden, bedeutet dies Folgendes: 
* Die Protokollerfassung funktioniert nur für Ressourcen im AMPLS. Die Erfassung in allen anderen Ressourcen wird verweigert (in allen Netzwerken, die dasselbe DNS teilen), unabhängig von Abonnement oder Mandant.
* Abfragen haben ein offeneres Verhalten, sodass Abfrageanforderungen sogar Ressourcen erreichen können, die sich nicht im AMPLS befinden. Damit sollte vermieden werden, dass Kundenabfragen zu Ressourcen, die nicht im AMPLS enthalten sind, unterbrochen werden, und ressourcenbezogene Abfragen sollten die vollständigen Ergebnisse zurückgeben.

Dieses Verhalten erwies sich jedoch für einige Kunden als zu restriktiv (da es die Erfassung von Ressourcen unterbricht, die nicht im AMPLS enthalten sind), für andere als zu freizügig (da es die Abfrage von Ressourcen gestattet, die nicht im AMPLS enthalten sind) und generell als verwirrend.

Für Private Link-Instanzen, die ab September 2021 erstellt werden, gelten daher neue obligatorische AMPLS-Einstellungen, die ausdrücklich festlegen, wie sich Private Link-Instanzen auf den Netzwerkdatenverkehr auswirken sollen. Wenn Sie eine neue AMPLS-Ressource erstellen, müssen Sie jetzt die gewünschten Zugriffsmodi auswählen, und zwar getrennt für Erfassung und Abfragen. 
* Modus „Nur privat“: Gestattet ausschließlich den Datenverkehr zu Private Link-Ressourcen
* Modus „Offen“: Verwendet Private Link, um mit Ressourcen im AMPLS zu kommunizieren, gestattet aber auch, dass der Datenverkehr mit anderen Ressourcen fortgesetzt wird. Weitere Informationen finden Sie unter [Steuern der Anwendung von Private Links auf Ihre Netzwerke](./private-link-design.md#control-how-private-links-apply-to-your-networks).

> [!NOTE]
> Die Log Analytics-Erfassung verwendet ressourcenspezifische Endpunkte. Daher entspricht sie nicht den AMPLS-Zugriffsmodi. **Legen Sie die Netzwerkfirewall so fest, dass Datenverkehr an öffentliche Endpunkte blockiert wird, unabhängig von den AMPLS-Zugriffsmodi, um sicherzustellen, dass Log Analytics-Erfassungsanforderungen nicht über die AMPLS auf Arbeitsbereiche zugreifen können.**

## <a name="next-steps"></a>Nächste Schritte
- [Entwerfen Ihres Private Link-Setups](private-link-design.md)
- Weitere Informationen zum [Konfigurieren von Private Link](private-link-configure.md)
- Hier erfahren Sie mehr über [privaten Speicher](private-storage.md) für benutzerdefinierte Protokolle und kundenseitig verwaltete Schlüssel (CMK).
<h3><a id="connect-to-a-private-endpoint"></a></h3>
