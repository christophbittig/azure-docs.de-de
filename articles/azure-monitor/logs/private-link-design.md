---
title: Entwerfen Ihres Private Link-Setups
description: Entwerfen Ihres Private Link-Setups
author: noakup
ms.author: noakuper
ms.topic: conceptual
ms.date: 08/01/2021
ms.openlocfilehash: 6bc664d5f7dab7d000f1ea540db56e240c307e15
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131006656"
---
# <a name="design-your-private-link-setup"></a>Entwerfen Ihres Private Link-Setups

Vor dem Einrichten Ihrer Azure Monitor Private Link-Instanz sollten Sie Ihre Netzwerktopologie und insbesondere Ihre DNS-Routingtopologie berücksichtigen.
Wie unter [Funktionsweise](./private-link-security.md#how-it-works) erläutert, wirkt sich das Einrichten eines Private Link auf den Datenverkehr zu allen Azure Monitor-Ressourcen aus. Dies gilt insbesondere für Application Insights-Ressourcen. Darüber hinaus ist nicht nur das Netzwerk betroffen, das mit dem privaten Endpunkt verbunden ist, sondern auch alle anderen Netzwerke, die dasselbe DNS verwenden.

Der einfachste und sicherste Ansatz sieht wie folgt aus:
1. Erstellen Sie eine einzelne Private Link-Verbindung mit einem einzelnen privaten Endpunkt und einer einzelnen AMPLS-Instanz. Wenn für Ihre Netzwerke ein Peering besteht, erstellen Sie die Private Link-Verbindung im gemeinsam genutzten VNET (oder Hub-VNET).
2. Fügen Sie dieser AMPLS-Instanz *alle* Azure Monitor-Ressourcen (Application Insights-Komponenten und Log Analytics-Arbeitsbereiche) hinzu.
3. Blockieren Sie den ausgehenden Netzwerkdatenverkehr so weit wie möglich.


## <a name="plan-by-network-topology"></a>Planen nach Netzwerktopologie

### <a name="guiding-principle-avoid-dns-overrides-by-using-a-single-ampls"></a>Leitprinzip: Vermeiden Sie DNS-Außerkraftsetzungen mithilfe einer einzelnen AMPLS-Instanz.
Einige Netzwerke bestehen aus mehreren VNETs oder anderen verbundenen Netzwerken. Wenn diese Netzwerke dasselbe DNS nutzen, würde das Einrichten einer Private Link-Instanz für eines der Netzwerke das DNS aktualisieren und sich auf den Datenverkehr in allen Netzwerken auswirken.

Im folgenden Diagramm stellt VNET 10.0.1.x zuerst eine Verbindung mit AMPLS1 her und ordnet IP-Adressen aus seinem Bereich die globalen Azure Monitor-Endpunkte zu. Später stellt VNET 10.0.2.x eine Verbindung mit AMPLS2 her und setzt mit IP-Adressen aus seinem Bereich die DNS-Zuordnung der **gleichen globalen Endpunkte** außer Kraft. Da zwischen diesen VNETs keine Peerbeziehung besteht, kann das erste VNET diese Endpunkte nun nicht erreichen.

Um diesen Konflikt zu vermeiden, erstellen Sie nur ein einzelnes AMPLS-Objekt pro DNS.

![Diagramm der DNS-Außerkraftsetzungen in mehreren VNETs](./media/private-link-security/dns-overrides-multiple-vnets.png)


### <a name="hub-and-spoke-networks"></a>Hub-and-Spoke-Netzwerke
Hub-and-Spoke-Netzwerke sollten eine einzelne Private Link-Verbindung verwenden, die auf das Hubnetzwerk (Hauptnetzwerk) und nicht auf jedes Spoke-VNet festgelegt wird. 

![Hub-and-Spoke: privater Endpunkt](./media/private-link-security/hub-and-spoke-with-single-private-endpoint.png)

> [!NOTE]
> Möglicherweise bevorzugen Sie absichtlich separate Private Link-Instanzen für Ihre Spoke-VNETs, um z. B. jedem VNET Zugriff auf eine begrenzte Anzahl von Überwachungsressourcen zu gewähren. In solchen Fällen können Sie einen dedizierten privaten Endpunkt und eine AMPLS-Instanz für jedes VNET erstellen, **müssen jedoch auch überprüfen, ob sie nicht die gleichen DNS-Zonen gemeinsam verwenden, um DNS-Außerkraftsetzungen zu vermeiden**.

### <a name="peered-networks"></a>Netzwerke mit Peering
Das Peering von Netzwerken wird in verschiedenen anderen Topologien als Hub-Spoke verwendet. Solche Netzwerke können die IP-Adressen der jeweils anderen erreichen und nutzen wahrscheinlich dasselbe DNS. Auch in diesem Fall empfehlen wir, eine einzelne Private Link-Instanz in einem Netzwerk zu erstellen, auf das Ihre anderen Netzwerke zugreifen können. Vermeiden Sie es, mehrere private Endpunkte und AMPLS-Objekte zu erstellen, da letztlich nur die letzte Festlegung im DNS gilt.

### <a name="isolated-networks"></a>Isolierte Netzwerke
Wenn für Ihre Netzwerke kein Peering vorhanden ist, **müssen Sie auch deren DNS trennen, um Private Links verwenden zu können**. Danach erstellen Sie für jedes Netzwerk einen eigenen privaten Endpunkt und ein eigenes AMPLS-Objekt. Ihre AMPLS-Objekte können mit denselben oder anderen Arbeitsbereichen/Komponenten verknüpft sein.

### <a name="testing-locally-edit-your-machines-hosts-file-instead-of-the-dns"></a>Lokales Testen: Bearbeiten der Hostdatei Ihres Computers anstelle des DNS 
Zum lokalen Testen von Private Links, ohne andere Clients in Ihrem Netzwerk zu beeinträchtigen, stellen Sie sicher, dass Sie Ihr DNS nicht aktualisieren, wenn Sie Ihren privaten Endpunkt erstellen. Bearbeiten Sie stattdessen die Datei „hosts“ auf Ihrem Computer so, dass sie Anforderungen an die Private Link-Endpunkte sendet:
* Richten Sie einen Private Link ein, aber wählen Sie bei der Verbindung mit einem privaten Endpunkt aus, dass die automatische Integration mit dem DNS **nicht** erfolgen soll (Schritt 5b).
* Konfigurieren Sie die relevanten Endpunkte in den Hostdateien Ihrer Computer. Informationen zum Überprüfen der Azure Monitor-Endpunkte, die zugeordnet werden müssen, finden Sie unter [Überprüfen der DNS-Einstellungen Ihres Endpunkts](./private-link-configure.md#reviewing-your-endpoints-dns-settings).

Dieser Ansatz wird für Produktionsumgebungen nicht empfohlen.

## <a name="control-how-private-links-apply-to-your-networks"></a>Steuern der Anwendung von Private Links auf Ihre Netzwerke
Mit den Private Links-Zugriffsmodi (eingeführt im September 2021) können Sie steuern, wie Private Links Ihren Netzwerkdatenverkehr beeinflusst. Diese Einstellungen können für Ihr AMPLS-Objekt (für alle verbundenen Netzwerke) oder für bestimmte Netzwerke gelten, die mit ihm verbunden sind.

Die Wahl des richtigen Zugriffsmodus wirkt sich nachteilig auf Ihren Netzwerkdatenverkehr aus. Jeder dieser Modi kann für Datenerfassung und Abfragen getrennt festgelegt werden:

* Nur privat – Ermöglicht es dem VNet, nur Private Link-Ressourcen (Ressourcen im AMPLS) zu erreichen. Das ist der sicherste Arbeitsmodus, der eine Datenexfiltration verhindert. Um dies zu erreichen, wird der Datenverkehr zu Azure Monitor-Ressourcen aus dem AMPLS heraus blockiert.
![Diagramm des AMPLS-Zugriffsmodus „Nur privat“](./media/private-link-security/ampls-private-only-access-mode.png)
* Offen – Ermöglicht es dem VNet, sowohl Private Link-Ressourcen als auch Ressourcen außerhalb des AMPLS zu erreichen (wenn sie [Datenverkehr aus öffentlichen Netzwerken akzeptieren](./private-link-design.md#control-network-access-to-your-resources)). Der Zugriffsmodus „Offen“ verhindert zwar nicht die Datenexfiltration, bietet aber dennoch die anderen Vorteile von Private Links: Der Datenverkehr zu Private Link-Ressourcen wird über private Endpunkte gesendet, überprüft und über das Microsoft-Backbone übertragen. Der Modus „Offen“ eignet sich für eine gemischte Arbeitsweise (Zugriff auf einige Ressourcen öffentlich und auf andere über Private Link) oder während eines schrittweisen Onboardingprozesses.
![Diagramm für den AMPLS-Zugriffsmodus „Offen“](./media/private-link-security/ampls-open-access-mode.png) Zugriffsmodi werden separat für Erfassung und Abfragen festgelegt. Sie können z. B. den Modus „Nur privat“ für die Erfassung und den Modus „Offen“ für Abfragen festlegen.

Seien Sie vorsichtig, wenn Sie Ihren Zugriffsmodus auswählen. Die Verwendung des Zugriffsmodus „Nur privat“ blockiert den Datenverkehr zu Ressourcen, die nicht im AMPLS enthalten sind, in allen Netzwerken, die dasselbe DNS nutzen, unabhängig von Abonnement oder Mandant (mit Ausnahme von Log Analytics-Erfassungsanforderungen, wie nachfolgend erläutert). Wenn Sie nicht alle Azure Monitor-Ressourcen zum AMPLS hinzufügen können, beginnen Sie mit dem Hinzufügen ausgewählter Ressourcen und der Anwendung des Zugriffsmodus „Offen“. Erst nachdem Sie *alle* Azure Monitor-Ressourcen zu Ihrem AMPLS hinzugefügt haben, wechseln Sie in den Modus „Nur privat“, um maximale Sicherheit zu gewährleisten.

Siehe [Verwendung von APIs und Befehlszeile](./private-link-configure.md#use-apis-and-command-line) für Konfigurationsdetails und Beispiele.

> [!NOTE]
> Die Log Analytics-Erfassung verwendet ressourcenspezifische Endpunkte. Daher entspricht sie nicht den AMPLS-Zugriffsmodi. **Legen Sie die Netzwerkfirewall so fest, dass Datenverkehr an öffentliche Endpunkte blockiert wird, unabhängig von den AMPLS-Zugriffsmodi, um sicherzustellen, dass Log Analytics-Erfassungsanforderungen nicht über die AMPLS auf Arbeitsbereiche zugreifen können.**

### <a name="setting-access-modes-for-specific-networks"></a>Festlegen von Zugriffsmodi für bestimmte Netzwerke
Die in der AMPLS-Ressource festgelegten Zugriffsmodi gelten für alle Netzwerke, aber Sie können diese Einstellungen für bestimmte Netzwerke außer Kraft setzen.

Im folgenden Diagramm verwendet VNet1 den Modus „Offen“ und VNet2 den Modus „Nur privat“. Folglich können Anforderungen von VNet1 „Arbeitsbereich1“ und „Komponente2“ über Private Link erreichen und „Komponente3“ nicht über eine Private Link-Instanz (wenn sie [Datenverkehr aus öffentlichen Netzwerken akzeptiert](./private-link-design.md#control-network-access-to-your-resources)). Allerdings können die Anforderungen von „VNet2“ die „Komponente3“ nicht erreichen. 
![Diagramm der gemischten Zugriffsmodi](./media/private-link-security/ampls-mixed-access-modes.png)


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
Ihre Log Analytics-Arbeitsbereiche oder Application Insights-Komponenten können wie folgt festgelegt werden:
* Akzeptieren oder Blockieren der Erfassung aus öffentlichen Netzwerken (Netzwerke, die nicht mit der Ressourcen-AMPLS verbunden sind).
* Akzeptieren oder Blockieren von Abfragen aus öffentlichen Netzwerken (Netzwerke, die nicht mit der Ressourcen-AMPLS verbunden sind).

Diese Granularität ermöglicht es Ihnen, den Zugriff je nach Bedarf pro Arbeitsbereich festzulegen. Sie können beispielsweise die Erfassung nur über mit Private Link verbundene Netzwerke (d. h. bestimmte VNets) zulassen, aber dennoch Abfragen aus allen öffentlichen und privaten Netzwerken akzeptieren. 

Das Blockieren von Abfragen aus öffentlichen Netzwerken bedeutet, dass Clients (Computer, SDKs usw.) außerhalb der verbundenen AMPLS-Instanzen keine Daten in der Ressource abfragen können. Diese Daten umfassen Protokolle, Metriken und den Live Metrics Stream. Das Blockieren von Abfragen aus öffentlichen Netzwerken wirkt sich auf alle Erfahrungen aus, die diese Abfragen ausführen, z. B. Arbeitsmappen, Dashboards, Insights im Azure-Portal und Abfragen, die außerhalb des Azure-Portals ausgeführt werden.

Siehe [Ressourcenzugriffsflags setzen](./private-link-configure.md#set-resource-access-flags) für Konfigurationsdetails.

### <a name="exceptions"></a>Ausnahmen

#### <a name="diagnostic-logs"></a>Diagnoseprotokolle
Protokolle und Metriken, die über [Diagnoseeinstellungen](../essentials/diagnostic-settings.md) in einen Arbeitsbereich hochgeladen werden, werden über einen sicheren privaten Microsoft-Kanal geleitet und nicht durch diese Einstellungen gesteuert.

#### <a name="azure-resource-manager"></a>Azure Resource Manager
Die Einschränkung des Zugriffs in der oben beschriebenen Weise gilt für Daten in der Ressource. Konfigurationsänderungen – einschließlich Aktivierung und Deaktivierung dieser Zugriffseinstellungen – werden jedoch von Azure Resource Manager verwaltet. Zum Steuern dieser Einstellungen sollten Sie den Zugriff auf Ressourcen mithilfe geeigneter Rollen, Berechtigungen, Netzwerkkontrollen und Überwachungsfunktionen einschränken. Weitere Informationen finden Sie unter [Rollen, Berechtigungen und Sicherheit in Azure Monitor](../roles-permissions-security.md).

> [!NOTE]
> Abfragen, die über die ARM-API (Azure Resource Management) gesendet werden, können Azure Monitor Private Links nicht verwenden. Diese Abfragen werden nur dann nicht blockiert, wenn die Zielressource Abfragen aus öffentlichen Netzwerken zulässt (festgelegt über das Blatt „Netzwerkisolation“ oder [mithilfe der CLI](./private-link-configure.md#set-resource-access-flags)).
>
> Die folgenden Erfahrungen sind für die Ausführung von Abfragen über die ARM-API bekannt:
> * Sentinel
> * LogicApp-Connector
> * Lösung für die Updateverwaltung
> * Lösung für die Änderungsnachverfolgung
> * VM Insights
> * Container Insights
> * Log Analytics-Blatt „Zusammenfassung zum Arbeitsbereich“ (zeigt das Lösungsdashboard)

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
Beim Erfassungsprozess für benutzerdefinierte Protokolle werden Speicherkonten verwendet. Standardmäßig sind dies dienstseitig verwaltete Speicherkonten. Um jedoch benutzerdefinierte Protokolle in Private Links zu erfassen, müssen Sie Ihre eigenen Speicherkonten verwenden und sie Log Analytics-Arbeitsbereichen zuordnen.

Weitere Informationen zum Herstellen einer Verbindung mit Ihrem eigenen Speicherkonto finden Sie unter [Verwenden von kundenseitig verwalteten Speicherkonten in Azure Monitor Log Analytics](private-storage.md), insbesondere in den Abschnitten [Verwenden von privaten Verbindungen](private-storage.md#using-private-links) und [Verknüpfen von Speicherkonten mit dem Log Analytics-Arbeitsbereich](private-storage.md#link-storage-accounts-to-your-log-analytics-workspace).

### <a name="automation"></a>Automation
Wenn Sie Log Analytics-Lösungen verwenden, die ein Automation-Konto erfordern (z. B. Updateverwaltung, Änderungsnachverfolgung oder den Bestand), sollten Sie auch eine Private Link-Instanz für Ihr Automation-Konto erstellen. Weitere Informationen finden Sie unter [Verwenden von Azure Private Link zum sicheren Verbinden von Netzwerken mit Azure Automation](../../automation/how-to/private-link-security.md).

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

### <a name="network-subnet-size"></a>Größe des Netzwerk-Subnetzes
Das kleinste unterstützte IPv4-Subnetz ist /27 (unter Verwendung von CIDR-Subnetzdefinitionen). Azure VNets [können so klein wie /29](../../virtual-network/virtual-networks-faq.md#how-small-and-how-large-can-vnets-and-subnets-be) sein, aber Azure [reserviert 5 IP-Adressen](../../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets) und die Einrichtung von Azure Monitor Private Link erfordert mindestens 11 zusätzliche IP-Adressen, selbst wenn eine Verbindung zu einem einzelnen Arbeitsbereich besteht. [Prüfen Sie die DNS-Einstellungen Ihres Endpunkts](./private-link-configure.md#reviewing-your-endpoints-dns-settings) für die detaillierte Liste der Azure Monitor Private Link Endpunkte.


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
- Hier erfahren Sie mehr über [privaten Speicher](private-storage.md) für benutzerdefinierte Protokolle und kundenseitig verwaltete Schlüssel (Customer Managed Keys, CMKs).
- Erfahren Sie mehr über [Private Link für Automation](../../automation/how-to/private-link-security.md).