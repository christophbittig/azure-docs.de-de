---
title: Entwerfen Ihres Private Link-Setups
description: Entwerfen Ihres Private Link-Setups
author: noakup
ms.author: noakuper
ms.topic: conceptual
ms.date: 08/01/2021
ms.openlocfilehash: ccee8017bce3109cc6d47bbeb225ca7c1edf5d66
ms.sourcegitcommit: 47491ce44b91e546b608de58e6fa5bbd67315119
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/16/2021
ms.locfileid: "122343206"
---
# <a name="design-your-private-link-setup"></a>Entwerfen Ihres Private Link-Setups

Vor dem Einrichten Ihres Azure Monitor Private Link-Setups sollten Sie Ihre Netzwerktopologie und insbesondere Ihre DNS-Routingtopologie berücksichtigen.
Wie unter [Funktionsweise](./private-link-security.md#how-it-works) erläutert, wirkt sich das Einrichten eines Private Link auf den Datenverkehr zu allen Azure Monitor-Ressourcen aus. Dies gilt insbesondere für Application Insights-Ressourcen. Darüber hinaus ist nicht nur das Netzwerk betroffen, das mit dem privaten Endpunkt verbunden ist, sondern auch alle anderen Netzwerke, die dasselbe DNS verwenden.

> [!NOTE]
> Der einfachste und sicherste Ansatz sieht wie folgt aus:
> 1. Erstellen Sie eine einzelne Private Link-Verbindung mit einem einzelnen privaten Endpunkt und einer einzelnen AMPLS-Instanz. Wenn für Ihre Netzwerke ein Peering besteht, erstellen Sie die Private Link-Verbindung im gemeinsam genutzten VNET (oder Hub-VNET).
> 2. Fügen Sie dieser AMPLS-Instanz *alle* Azure Monitor-Ressourcen (Application Insights-Komponenten und Log Analytics-Arbeitsbereiche) hinzu.
> 3. Blockieren Sie den ausgehenden Netzwerkdatenverkehr so weit wie möglich.

Wenn es aus irgendeinem Grund nicht möglich ist, eine einzelne Private Link-Verbindung und eine einzelne AMPLS-Instanz (Azure Monitor Private Link Scope) zu verwenden, ist die nächstbeste Lösung, isolierte Private Link-Verbindungen für Isolationsnetzwerke zu erstellen. Wenn Sie Spoke-VNETs verwenden (oder sich daran ausrichten können), befolgen Sie die Anweisungen unter [Hub-Spoke-Netzwerktopologie in Azure](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke). Richten Sie dann separate Private Link-Einstellungen in den entsprechenden Spoke-VNETs ein. **Stellen Sie auch sicher, dass DNS-Zonen getrennt werden**, da die gemeinsame Nutzung von DNS-Zonen mit anderen Spoke-Netzwerken zu DNS-Außerkraftsetzungen führt.

## <a name="plan-by-network-topology"></a>Planen nach Netzwerktopologie
### <a name="hub-spoke-networks"></a>Hub-Spoke-Netzwerke
Hub-Spoke-Topologien können das Problem von DNS-Außerkraftsetzungen durch die Einrichtung von Private Link im Hub-VNet (Haupt-VNet) anstelle einer Instanz für jedes Spoke-VNet vermeiden. Dieses Setup ist insbesondere dann sinnvoll, wenn die von den Spoke-VNETs verwendeten Azure Monitor-Ressourcen gemeinsam genutzt werden. 

![Hub-and-Spoke: privater Endpunkt](./media/private-link-security/hub-and-spoke-with-single-private-endpoint.png)

> [!NOTE]
> Möglicherweise bevorzugen Sie absichtlich separate Private Link-Instanzen für Ihre Spoke-VNETs, um z. B. jedem VNET Zugriff auf eine begrenzte Anzahl von Überwachungsressourcen zu gewähren. In solchen Fällen können Sie einen dedizierten privaten Endpunkt und eine AMPLS-Instanz für jedes VNET erstellen, **müssen jedoch auch überprüfen, ob sie nicht die gleichen DNS-Zonen gemeinsam verwenden, um DNS-Außerkraftsetzungen zu vermeiden**.

### <a name="peered-networks"></a>Netzwerke mit Peering
Das Peering von Netzwerken wird in verschiedenen anderen Topologien als Hub-Spoke verwendet. Solche Netzwerke können die IP-Adressen der jeweils anderen erreichen und nutzen wahrscheinlich dasselbe DNS. In solchen Fällen ähnelt die Empfehlung der Hub-Spoke-Topologie: Wählen Sie ein einzelnes Netzwerk aus, das von allen anderen (relevanten) Netzwerken erreicht wird, und legen Sie die Private Link-Verbindung in diesem Netzwerk fest. Vermeiden Sie es, mehrere private Endpunkte und AMPLS-Objekte zu erstellen, da letztlich nur die letzte Festlegung im DNS gilt.

### <a name="isolated-networks"></a>Isolierte Netzwerke
#<a name="if-your-networks-arent-peered-you-must-also-separate-their-dns-in-order-to-use-private-links-once-thats-done-you-can-create-a-private-link-for-one-or-many-network-without-affecting-traffic-of-other-networks-that-means-creating-a-separate-private-endpoint-for-each-network-and-a-separate-ampls-object-your-ampls-objects-can-link-to-the-same-workspacescomponents-or-to-different-ones"></a>Wenn für Ihre Netzwerke kein Peering vorhanden ist, **müssen Sie auch deren DNS trennen, um Private Links verwenden zu können**. Sobald dies geschehen ist, können Sie einen Private Link für ein Netzwerk (oder mehrere) erstellen, ohne den Datenverkehr anderer Netzwerke zu beeinträchtigen. Das bedeutet, dass Sie einen separaten privaten Endpunkt für jedes Netzwerk und ein separates AMPLS-Objekt erstellen. Ihre AMPLS-Objekte können mit denselben oder anderen Arbeitsbereichen/Komponenten verknüpft sein.

### <a name="testing-locally-edit-your-machines-hosts-file-instead-of-the-dns"></a>Lokales Testen: Bearbeiten der Hostdatei Ihres Computers anstelle des DNS 
Als lokale Umgehung des Alles-oder-nichts-Verhaltens können Sie festlegen, dass Ihr DNS nicht mit den Private Link-Einträgen aktualisiert wird, und stattdessen die Hostdateien auf ausgewählten Computern bearbeiten, sodass nur diese Computer Anforderungen an die Private Link-Endpunkte senden.
* Richten Sie einen Private Link ein, aber wählen Sie bei der Verbindung mit einem privaten Endpunkt aus, dass die automatische Integration mit dem DNS **nicht** erfolgen soll (Schritt 5b).
* Konfigurieren Sie die relevanten Endpunkte in den Hostdateien Ihrer Computer. Informationen zum Überprüfen der Azure Monitor-Endpunkte, die zugeordnet werden müssen, finden Sie unter [Überprüfen der DNS-Einstellungen Ihres Endpunkts](./private-link-configure.md#reviewing-your-endpoints-dns-settings).

Dieser Ansatz wird für Produktionsumgebungen nicht empfohlen.

## <a name="consider-ampls-limits"></a>Berücksichtigen von AMPLS-Einschränkungen
Für das AMPLS-Objekt gelten die folgenden Einschränkungen:
* Ein VNET kann nur eine Verbindung mit **einem einzigen** AMPLS-Objekt herstellen. Dies bedeutet, dass das AMPLS-Objekt Zugriff auf alle Azure Monitor-Ressourcen bieten muss, auf die das VNET Zugriff haben sollte.
* Ein AMPLS-Objekt kann eine Verbindung mit höchstens 50 Azure Monitor-Ressourcen herstellen.
* Eine Azure Monitor-Ressource (Arbeitsbereich oder Application Insights-Komponente) kann eine Verbindung mit höchstens fünf AMPLS-Objekten herstellen.
* Ein AMPLS-Objekt kann eine Verbindung mit höchstens zehn privaten Endpunkten herstellen.

Im Diagramm unten ist Folgendes dargestellt:
* Jedes VNET stellt eine Verbindung mit **einem einzigen** AMPLS-Objekt her.
* AMPLS A stellt eine Verbindung mit zwei Arbeitsbereichen und einer Application Insights-Komponente her und verwendet damit drei von 50 der möglichen Verbindungen mit Azure Monitor-Ressourcen.
* Workspace2 stellt eine Verbindung mit AMPLS A und AMPLS B her und verwendet damit zwei von fünf der möglichen AMPLS-Verbindungen.
* AMPLS B ist mit privaten Endpunkten von zwei VNETs (VNet2 und VNet3) verbunden und verwendet damit zwei von zehn der möglichen Verbindungen mit privaten Endpunkten.

![Diagramm der AMPLS-Einschränkungen](./media/private-link-security/ampls-limits.png)


## <a name="control-network-access-to-your-resources"></a>Festlegen des Netzwerkzugriffs auf Ihre Ressourcen
Ihre Log Analytics-Arbeitsbereiche oder Application Insights-Komponenten können so festgelegt werden, dass sie den Zugriff aus öffentlichen Netzwerken, d. h. Netzwerken, die nicht mit der AMPLS-Instanz der Ressource verbunden sind, akzeptieren oder blockieren.
Diese Granularität ermöglicht es Ihnen, den Zugriff je nach Bedarf pro Arbeitsbereich festzulegen. Sie können beispielsweise die Erfassung nur über mit Private Link verbundene Netzwerke (d. h. bestimmte VNETs) zulassen, aber dennoch Abfragen aus allen öffentlichen und privaten Netzwerken akzeptieren. Beachten Sie, dass das Blockieren von Abfragen aus öffentlichen Netzwerken bedeutet, dass Clients (Computer, SDKs usw.) außerhalb der verbundenen AMPLS-Instanzen keine Daten in der Ressource abfragen können. Dies schließt den Zugriff auf Protokolle, Metriken, den Livemetrikstream sowie darauf aufsetzende Funktionalität ein, wie z. B. Arbeitsmappen, Dashboards, auf Abfrage-APIs basierende Clientfunktionen, Erkenntnisse im Azure-Portal und vieles mehr. Funktionen, die außerhalb des Azure-Portals ausgeführt werden und Log Analytics-Daten abfragen, sind ebenfalls von dieser Einstellung betroffen.


## <a name="application-insights-considerations"></a>Überlegungen für Application Insights
* Sie müssen Ressourcen zum Hosten der überwachten Workloads einem Private Link hinzufügen. Ein Beispiel finden Sie unter [Verwenden privater Endpunkte für eine Azure-Web-App](../../app-service/networking/private-endpoint.md).
* Nicht-Portal-Verbrauchsfunktionen müssen ebenfalls in dem privat verknüpften virtuellen Netzwerk ausgeführt werden, in dem sich die überwachten Workloads befinden.
* Um Private Links für Profiler und Debugger zu unterstützen, müssen Sie Ihr [eigenes Speicherkonto bereitstellen](../app/profiler-bring-your-own-storage.md). 

> [!NOTE]
> Um arbeitsbereichsbasierte Application Insights-Ressourcen vollständig zu schützen, müssen Sie den Zugriff sowohl auf die Application Insights-Ressource als auch auf den zugrunde liegenden Log Analytics-Arbeitsbereich sperren.

## <a name="log-analytics-considerations"></a>Überlegungen für Log Analytics

### <a name="log-analytics-solution-packs-download"></a>Herunterladen von Log Analytics-Lösungspaketen
Log Analytics-Agents müssen auf ein globales Speicherkonto zugreifen, um Lösungspakete herunterzuladen. Bei Private Link-Setups, die ab dem 19. April 2021 (oder ab Juni 2021 auf Azure Sovereign Clouds) erstellt werden, ist der Lösungspaketspeicher der Agents über die private Verbindung erreichbar. Diese Funktion wird durch eine für „blob.core.windows.net“ erstellte DNS-Zone ermöglicht.

Wenn Ihr Private Link-Setup vor dem 19. April 2021 erstellt wurde, ist der Lösungspaketspeicher nicht über eine private Verbindung erreichbar. In diesem Fall stehen Ihnen zwei Möglichkeiten zur Verfügung:
* Sie können Ihren Azure Monitor Private Link-Bereich und den damit verbundenen privaten Endpunkt neu erstellen.
* Sie können dafür sorgen, dass Ihre Agents das Speicherkonto über den öffentlichen Endpunkt erreichen, indem Sie der Zulassungsliste Ihrer Firewall die folgenden Regeln hinzufügen:

    | Cloudumgebung | Agent-Ressource | Ports | Direction |
    |:--|:--|:--|:--|
    |Azure – Öffentlich     | scadvisorcontent.blob.core.windows.net         | 443 | Ausgehend
    |Azure Government | usbn1oicore.blob.core.usgovcloudapi.net | 443 |  Ausgehend
    |Azure China 21Vianet      | mceast2oicore.blob.core.chinacloudapi.cn| 443 | Ausgehend

### <a name="collecting-custom-logs-and-iis-log-over-private-link"></a>Erfassen von benutzerdefinierten Protokollen und des IIS-Protokolls über Private Link
Beim Erfassungsprozess für benutzerdefinierte Protokolle werden Speicherkonten verwendet. Standardmäßig sind dies dienstseitig verwaltete Speicherkonten. Um jedoch benutzerdefinierte Protokolle in Private Links zu erfassen, müssen Sie Ihre eigenen Speicherkonten verwenden und sie Log Analytics-Arbeitsbereichen zuordnen. Informieren Sie sich in diesem Artikel zur [Befehlszeile](/cli/azure/monitor/log-analytics/workspace/linked-storage), wie Sie solche Konten einrichten.

Weitere Informationen zum Verbinden Ihres eigenen Speicherkontos finden Sie unter [Kundeneigene Speicherkonten für die Protokollerfassung](private-storage.md).

### <a name="automation"></a>Automation
Wenn Sie Log Analytics-Lösungen verwenden, die ein Automation-Konto erfordern, z. B. Updateverwaltung, Änderungsnachverfolgung oder den Bestand, sollten Sie auch eine separate Private Link-Instanz für Ihr Automation-Konto einrichten. Weitere Informationen finden Sie unter [Verwenden von Azure Private Link zum sicheren Verbinden von Netzwerken mit Azure Automation](../../automation/how-to/private-link-security.md).

> [!NOTE]
> Einige Produkte und Azure-Portalfunktionen fragen Daten über Azure Resource Manager ab und sind daher nicht in der Lage, Daten über einen Private Link abzufragen, es sei denn, Private Link-Einstellungen werden auch auf Resource Manager angewendet. Um dies zu umgehen, können Sie Ihre Ressourcen so konfigurieren, dass Abfragen aus öffentlichen Netzwerken akzeptiert werden, wie es unter [Steuern des Netzwerkzugriffs auf Ihre Ressourcen](./private-link-design.md#control-network-access-to-your-resources) erläutert ist (die Erfassung kann auf Private Link-Netzwerke beschränkt bleiben).
Es wurden die folgenden Produkte und Funktionen ermittelt, die Arbeitsbereiche über Azure Resource Manager abfragen:
> * LogicApp-Connector
> * Lösung für die Updateverwaltung
> * Lösung für die Änderungsnachverfolgung
> * Das Blatt „Zusammenfassung zum Arbeitsbereich“ im Portal (zeigt das Lösungsdashboard)
> * VM Insights
> * Container Insights

## <a name="requirements"></a>Requirements (Anforderungen)
### <a name="agents"></a>Agents
Die neuesten Versionen von Windows- und Linux-Agents müssen verwendet werden, um eine sichere Erfassung in Log Analytics-Arbeitsbereichen zu ermöglichen. Ältere Versionen können keine Überwachungsdaten über ein privates Netzwerk hochladen.

**Log Analytics-Windows-Agent**

Verwenden Sie die Log Analytics-Agent-Version 10.20.18038.0 oder höher.

**Log Analytics-Linux-Agent**

Verwenden Sie die Agent-Version 1.12.25 oder höher. Falls dies nicht möglich ist, führen Sie die folgenden Befehle auf Ihrem virtuellen Computer aus.

```cmd
$ sudo /opt/microsoft/omsagent/bin/omsadmin.sh -X
$ sudo /opt/microsoft/omsagent/bin/omsadmin.sh -w <workspace id> -s <workspace key>
```
### <a name="azure-portal"></a>Azure-Portal
Um Azure Monitor-Portalfunktionalität wie Application Insights und Log Analytics zu verwenden, müssen Sie den Zugriff auf die Erweiterungen für Azure-Portal und Azure Monitor in den privaten Netzwerken zulassen. Fügen Sie [Diensttags](../../firewall/service-tags.md) für **AzureActiveDirectory**, **AzureResourceManager**, **AzureFrontDoor.FirstParty** und **AzureFrontdoor.Frontend** Ihrer Netzwerksicherheitsgruppe hinzu.

### <a name="programmatic-access"></a>Programmgesteuerter Zugriff
Um die REST-API, die [CLI](/cli/azure/monitor) oder PowerShell mit Azure Monitor in privaten Netzwerken zu verwenden, fügen Sie die [Diensttags](../../virtual-network/service-tags-overview.md) für **AzureActiveDirectory** und **AzureResourceManager** Ihrer Firewall hinzu.

### <a name="application-insights-sdk-downloads-from-a-content-delivery-network"></a>Application Insights SDK-Downloads aus einem Content Delivery Network
Bündeln Sie den JavaScript-Code in Ihrem Skript, sodass der Browser nicht versucht, Code aus einem CDN herunterzuladen. Ein Beispiel finden Sie auf [GitHub](https://github.com/microsoft/ApplicationInsights-JS#npm-setup-ignore-if-using-snippet-setup).

### <a name="browser-dns-settings"></a>Browser-DNS-Einstellungen
Wenn Sie eine Verbindung mit den Azure Monitor-Ressourcen über Private Link herstellen, muss der Datenverkehr zu diesen Ressourcen über den privaten Endpunkt erfolgen, der in Ihrem Netzwerk konfiguriert ist. Um den privaten Endpunkt zu aktivieren, aktualisieren Sie Ihre DNS-Einstellungen, wie unter [Herstellen einer Verbindung mit einem privaten Endpunkt](./private-link-configure.md#connect-to-a-private-endpoint) beschrieben. Einige Browser verwenden eigene DNS-Einstellungen anstelle der von Ihnen festgelegten DNS-Einstellungen. Der Browser versucht möglicherweise, eine Verbindung mit öffentlichen Azure Monitor-Endpunkten herzustellen und den privaten Link vollständig zu umgehen. Vergewissern Sie sich, dass die Browsereinstellungen DNS-Einstellungen nicht überschreiben und keine alten Einstellungen zwischenspeichern. 

### <a name="querying-limitation-externaldata-operator"></a>Einschränkung bei Abfragen: externaldata-Operator
Der [`externaldata`-Operator](/azure/data-explorer/kusto/query/externaldata-operator?pivots=azuremonitor) wird von Private Link nicht unterstützt, da er zwar Daten aus Speicherkonten liest, jedoch nicht sicherstellt, dass privat auf den Speicher zugegriffen wird.

## <a name="next-steps"></a>Nächste Schritte
- Erfahren Sie mehr über das [Konfigurieren von Private Link](private-link-configure.md).
- Erfahren Sie mehr über [privaten Speicher](private-storage.md).
- Erfahren Sie mehr über [Private Link für Automation](../../automation/how-to/private-link-security.md).