---
title: Verbindungsmonitor in Azure | Microsoft-Dokumentation
description: In diesem Artikel erfahren Sie, wie Sie mit dem Verbindungsmonitor die Netzwerkkommunikation in einer verteilten Umgebung überwachen.
services: network-watcher
documentationcenter: na
author: vinynigam
manager: agummadi
editor: ''
tags: azure-resource-manager
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/04/2021
ms.author: vinigam
ms.custom: mvc
ms.openlocfilehash: 6b62da14473797653da17b28b5366e26e7c18357
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131082529"
---
# <a name="monitor-network-connectivity-by-using-connection-monitor"></a>Überwachen der Netzwerkkonnektivität mit dem Verbindungsmonitor

> [!IMPORTANT]
> Ab dem 1. Juli 2021 ist es nicht mehr möglich, neue Tests in einem vorhandenen Arbeitsbereich hinzuzufügen oder einen neuen Arbeitsbereich im Netzwerkleistungsmonitor (NPM) zu aktivieren. Sie können auch keine weiteren neuen Verbindungsmonitore im Verbindungsmonitor (klassisch) hinzufügen. Die Tests und Verbindungsmonitore, die vor dem 1. Juli 2021 erstellt wurden, können jedoch weiterhin verwendet werden. 
> 
> Um die Dienstunterbrechung im Zusammenhang mit Ihren aktuellen Workloads auf ein Mindestmaß zu begrenzen, führen Sie vor dem 29. Februar 2024 eine [Migration Ihrer Tests aus dem Netzwerkleistungsmonitor](migrate-to-connection-monitor-from-network-performance-monitor.md) oder [eine Migration vom Verbindungsmonitor (klassisch)](migrate-to-connection-monitor-from-connection-monitor-classic.md) zum neuen Verbindungsmonitor in Azure Network Watcher durch.

Der Verbindungsmonitor bietet eine einheitliche End-to-End-Verbindungsüberwachung in Azure Network Watcher. Das Feature für den Verbindungsmonitor unterstützt Hybrid- und Azure-Cloudbereitstellungen. Network Watcher stellt Tools für das Überwachen, Diagnostizieren und Anzeigen von Verbindungsmetriken für Ihre Azure-Bereitstellungen zur Verfügung.

Nachfolgend sind einige Anwendungsfälle für den Verbindungsmonitor aufgeführt:

- In einer Anwendung mit mehreren Ebenen kommuniziert Ihre Front-End-Webserver-VM mit einer Datenbankserver-VM, und Sie möchten die Netzwerkkonnektivität zwischen beiden VMs überprüfen.
- Beispielsweise möchten Sie, dass VMs in der Region „USA, Osten“ VMs in der Region „USA, Mitte“ pingen, und Sie möchten die regionsübergreifenden Netzwerklatenzen vergleichen.
- Sie verfügen über mehrere lokale Unternehmensstandorte: einen in Seattle, Washington, und einen in Ashburn, Virginia. Ihre Unternehmensstandorte sind mit Microsoft 365-URLs verbunden. Sie möchten für die Benutzer der Microsoft 365-URLs die Latenzen zwischen Seattle und Ashburn vergleichen.
- Ihre Hybridanwendung benötigt Konnektivität mit dem Endpunkt eines Azure-Speicherkontos. Der lokale Standort und die Azure-Anwendung sind mit demselben Endpunkt verbunden. Sie möchten die Latenzen des lokalen Standorts mit den Latenzen der Azure-Anwendung vergleichen.
- Sie möchten die Konnektivität zwischen den lokalen Setups und den Azure-VMs überprüfen, auf denen die Cloudanwendung gehostet wird.

Der Verbindungsmonitor bietet eine Kombination aus den besten Features von zwei Lösungen: dem Feature [Verbindungsmonitor (klassisch)](./network-watcher-monitoring-overview.md#monitor-communication-between-a-virtual-machine-and-an-endpoint) von Network Watcher und den NPM-Features [Dienstkonnektivitätsmonitor](../azure-monitor/insights/network-performance-monitor-service-connectivity.md), [ExpressRoute-Überwachung](../expressroute/how-to-npm.md) und [Leistungsüberwachung](../azure-monitor/insights/network-performance-monitor-performance-monitor.md).

Nachfolgend sind einige Vorteile des Verbindungsmonitors aufgeführt:

* Einheitliche, intuitive Oberfläche zum Überwachen von Azure- und Hybridbereitstellungen
* Regionsübergreifende und arbeitsbereichsübergreifende Konnektivitätsüberwachung
* Häufigere Prüfungen und bessere Einsicht in die Netzwerkleistung
* Schnellere Warnungen für Ihre Hybridbereitstellungen
* Unterstützung für Konnektivitätsprüfungen, die auf HTTP, TCP (Transmission Control Protocol) und ICMP (Internet Control Message Protocol) basieren 
* Metriken und Unterstützung für Log Analytics, sowohl für Azure- als auch für Nicht-Azure-Testsetups

![Diagramm: Interaktion des Verbindungsmonitors mit Azure-VMs, Nicht-Azure-Hosts, Endpunkten und Datenspeicherorten](./media/connection-monitor-2-preview/hero-graphic.png)

Gehen Sie folgendermaßen vor, um den Verbindungsmonitor für die Überwachung zu verwenden: 

1. [Installieren Sie Überwachungs-Agents](#install-monitoring-agents).
1. [Aktivieren Sie Network Watcher für Ihr Abonnement](#enable-network-watcher-on-your-subscription).
1. [Erstellen Sie einen Verbindungsmonitor](#create-a-connection-monitor).
1. [Analysieren Sie Überwachungsdaten, und richten Sie Warnungen ein](#analyze-monitoring-data-and-set-alerts).
1. [Diagnostizieren Sie Problemen in Ihrem Netzwerk](#diagnose-issues-in-your-network).

Die folgenden Abschnitte enthalten weitere Informationen zu diesen Schritten.

## <a name="install-monitoring-agents"></a>Installieren von Überwachungs-Agents

Der Verbindungsmonitor verwendet einfache ausführbare Dateien zum Ausführen von Konnektivitätsprüfungen. Er unterstützt Konnektivitätsprüfungen sowohl in Azure-Umgebungen als auch lokalen Umgebungen. Die verwendete ausführbare Datei hängt davon ab, ob Ihre VM in Azure oder lokal gehostet wird.

### <a name="agents-for-azure-virtual-machines"></a>Agents für Azure-VMs

Damit der Verbindungsmonitor Ihre Azure-VMs als Überwachungsquellen erkennt, installieren Sie die VM-Erweiterung für den Network Watcher-Agent auf den VMs. Diese Erweiterung wird auch als *Network Watcher-Erweiterung* bezeichnet. Für Azure-VMs muss die Erweiterung eine End-to-End-Überwachung und andere erweiterte Funktionen auslösen. 

Sie können die Network Watcher-Erweiterung beim [Erstellen einer VM](./connection-monitor.md#create-the-first-vm) installieren. Die Network Watcher-Erweiterung für [Linux](../virtual-machines/extensions/network-watcher-linux.md) und [Windows](../virtual-machines/extensions/network-watcher-windows.md) kann auch separat installiert und konfiguriert sowie eine separate Problembehandlung durchgeführt werden.

Durch Regeln für eine Netzwerksicherheitsgruppe (NSG) oder Firewall kann die Kommunikation zwischen Quelle und Ziel blockiert werden. Der Verbindungsmonitor erkennt dieses Problem und zeigt es als Diagnosemeldung in der Topologie an. Damit eine Verbindungsüberwachung möglich ist, müssen Sie zunächst sicherstellen, dass die NSG- und Firewallregeln eine Paketübertragung über TCP oder ICMP zwischen der Quelle und dem Ziel zulassen.

### <a name="agents-for-on-premises-machines"></a>Agents für lokale Computer

Damit der Verbindungsmonitor die lokalen Computer als Quellen für die Überwachung erkennt, installieren Sie den Log Analytics-Agent auf den Computern. Aktivieren Sie dann die [Netzwerkleistungsmonitor-Lösung](../network-watcher/connection-monitor-overview.md#enable-the-npm-solution-for-on-premises-machines). Diese Agents sind mit den Log Analytics-Arbeitsbereichen verknüpft. Daher müssen Sie die Arbeitsbereichs-ID und den Primärschlüssel einrichten, bevor die Agents mit der Überwachung beginnen können.

Informationen zum Installieren des Log Analytics-Agents für Windows finden Sie unter [Installieren des Log Analytics-Agents unter Windows](../azure-monitor/agents/agent-windows.md).

Wenn der Pfad Firewalls oder virtuelle Netzwerkappliances umfasst, stellen Sie sicher, dass das Ziel erreichbar ist.

So öffnen Sie den Port

* Führen Sie auf Windows-Computern das PowerShell-Skript [EnableRules.ps1](https://aka.ms/npmpowershellscript) ohne Parameter in einem PowerShell-Fenster mit Administratorrechten aus.

* Für Linux-Computer ändern Sie den PortNumber-Wert manuell. Gehen Sie folgendermaßen vor:
   1. Navigieren Sie zu */var/opt/microsoft/omsagent/npm_state*. 
   1. Öffnen Sie die Datei *npmdregistry*.
   1. Ändern Sie den PortNumber-Wert: ```“PortNumber:<port of your choice>”```.

   > [!NOTE]
   > Die verwendeten Portnummern sollten für alle in einem Arbeitsbereich verwendeten Agents identisch sein. 

Das Skript erstellt die Registrierungsschlüssel, die für die Lösung erforderlich sind. Außerdem erstellt es Regeln für die Windows-Firewall, damit Agents TCP-Verbindungen miteinander herstellen können. Die vom Skript erstellten Registrierungsschlüssel geben an, ob die Debugprotokolle und der Pfad zur Protokolldatei protokolliert werden sollen. Ferner definiert das Skript den für die Kommunikation verwendeten TCP-Port des Agents. Die Werte für diese Schlüssel werden vom Skript automatisch festgelegt. Ändern Sie diese Schlüssel nicht manuell. Standardmäßig ist Port 8084 geöffnet. Sie können einen benutzerdefinierten Port verwenden, indem Sie im Skript den Parameter „portNumber“ angeben. Verwenden Sie auf allen Computern, auf denen das Skript ausgeführt wird, den gleichen Port. 

Weitere Informationen finden Sie unter [Übersicht über den Log Analytics-Agent](../azure-monitor/agents/log-analytics-agent.md#network-requirements) im Abschnitt „Netzwerkanforderungen“.

Das Skript konfiguriert nur die lokale Windows-Firewall. Bei Verwendung einer Netzwerkfirewall müssen Sie sicherstellen, dass diese den Datenverkehr zu dem TCP-Port erlaubt, der vom Netzwerkleistungsmonitor verwendet wird.

Der Windows-Agent von Log Analytics kann mehrfach vernetzt werden, um Daten an mehrere Arbeitsbereiche und System Center Operations Manager-Verwaltungsgruppen zu senden. Der Linux-Agent kann Daten nur an ein einzelnes Ziel senden, entweder einen Arbeitsbereich oder eine Verwaltungsgruppe.

#### <a name="enable-the-npm-solution-for-on-premises-machines"></a>Aktivieren der NPM-Lösung für lokale Computer 

Gehen Sie wie folgt vor, um die NPM-Lösung für lokale Computer zu aktivieren: 

1. Navigieren Sie im Azure-Portal zu **Network Watcher**.
1. Wählen Sie im linken Bereich unter **Überwachung** die Option **Netzwerkleistungsmonitor** aus. 

   Eine Liste der Arbeitsbereiche mit aktivierter NPM-Lösung wird angezeigt, gefiltert nach **Abonnements**. 
1. Um die NPM-Lösung einem neuen Arbeitsbereich hinzuzufügen, wählen Sie oben links **NPM hinzufügen** aus. 
1. Wählen Sie das Abonnement und den Arbeitsbereich aus, in dem Sie die Lösung aktivieren möchten, und klicken Sie auf **Erstellen**.
   
   Nachdem Sie die Lösung aktiviert haben, dauert es einige Minuten, bis der Arbeitsbereich angezeigt wird.

   :::image type="content" source="./media/connection-monitor/network-performance-monitor-solution-enable.png" alt-text="Screenshot: Hinzufügen der NPM-Lösung im Verbindungsmonitor" lightbox="./media/connection-monitor/network-performance-monitor-solution-enable.png":::

Im Gegensatz zu Log Analytics-Agents kann die NPM-Lösung so konfiguriert werden, dass Daten nur an einen einzigen Log Analytics-Arbeitsbereich gesendet werden.

## <a name="enable-network-watcher-on-your-subscription"></a>Aktivieren von Network Watcher für Ihr Abonnement

Für alle Abonnements, die über ein virtuelles Netzwerk verfügen, wird Network Watcher aktiviert. Wenn Sie ein virtuelles Netzwerk in Ihrem Abonnement erstellen, wird Network Watcher automatisch in der Region und für das Abonnement des virtuellen Netzwerks aktiviert. Diese automatische Aktivierung wirkt sich nicht auf Ihre Ressourcen aus, und es fallen auch keine Gebühren an. Stellen Sie sicher, dass Network Watcher für Ihr Abonnement nicht explizit deaktiviert ist. 

Stellen Sie sicher, dass Network Watcher [für Ihre Region verfügbar ist](https://azure.microsoft.com/global-infrastructure/services/?products=network-watcher&regions=all). Weitere Informationen finden Sie unter [Aktivieren von Network Watcher](./network-watcher-create.md).

## <a name="create-a-connection-monitor"></a>Erstellen eines Verbindungsmonitors 

Der Verbindungsmonitor überwacht die Kommunikation in regelmäßigen Abständen. Er informiert Sie über Änderungen der Erreichbarkeit und Latenz. Sie können auch die aktuelle und die frühere Netzwerktopologie zwischen Quell-Agents und Zielendpunkten überprüfen.

Ziele können Azure-VMs oder lokale Computer sein, auf denen ein Überwachungs-Agent installiert ist. Zielendpunkte können Microsoft 365-URLs, Dynamics 365-URLs, benutzerdefinierte URLs, Azure-VM-Ressourcen-IDs, IPv4/IPv6-Adressen, FQDNs oder beliebige Domänennamen sein.

### <a name="access-connection-monitor"></a>Zugreifen auf den Verbindungsmonitor

1. Navigieren Sie im Azure-Portal zu **Network Watcher**.
1. Wählen Sie im linken Bereich unter **Überwachung** die Option **Verbindungsmonitor** aus.

   Es werden alle Verbindungsmonitore angezeigt, die im Verbindungsmonitor erstellt wurden. Um die Verbindungsmonitore anzuzeigen, die in der klassischen Benutzeroberfläche des Verbindungsmonitors erstellt wurden, wählen Sie die Registerkarte **Verbindungsmonitor** aus.
    
  :::image type="content" source="./media/connection-monitor-2-preview/cm-resource-view.png" alt-text="Screenshot: Verbindungsmonitore, die im Verbindungsmonitor erstellt wurden" lightbox="./media/connection-monitor-2-preview/cm-resource-view.png":::

### <a name="create-a-connection-monitor"></a>Erstellen eines Verbindungsmonitors

In Verbindungsmonitoren, die Sie im Feature „Verbindungsmonitor“ erstellen, können Sie sowohl lokale Computer als auch Azure-VMs als Quellen hinzufügen. Mit diesen Verbindungsmonitoren kann auch die Konnektivität mit Endpunkten überwacht werden. Die Endpunkte können sich in Azure oder unter einer beliebigen anderen URL oder IP-Adresse befinden.

Der Verbindungsmonitor umfasst die folgenden Entitäten:

* **Verbindungsmonitorressource**: Eine regionsspezifische Azure-Ressource. Alle folgenden Entitäten sind Eigenschaften einer Verbindungsmonitorressource.
* **Endpunkt**: Eine Quelle oder ein Ziel, die bzw. das an Konnektivitätsprüfungen beteiligt ist. Beispiele für Endpunkte umfassen Azure-VMs, lokale Agents, URLs und IP-Adressen.
* **Testkonfiguration**: Eine protokollspezifische Konfiguration für einen Test. Je nach ausgewähltem Protokoll können Sie den Port, Schwellenwerte, die Testhäufigkeit und andere Eigenschaften definieren.
* **Testgruppe**: Die Gruppe, die Quellendpunkte, Zielendpunkte und Testkonfigurationen enthält. Ein Verbindungsmonitor kann mehrere Testgruppen enthalten.
* **Test**: Die Kombination aus einem Quellendpunkt, einem Zielendpunkt und einer Testkonfiguration. Ein Test ist die differenzierteste Ebene, auf der Überwachungsdaten verfügbar sind. Die Überwachungsdaten umfassen den Prozentsatz von Überprüfungen mit Fehlern und die Roundtripzeit.

 ![Diagramm eines Verbindungsmonitors, in dem die Beziehung zwischen Testgruppen und Tests definiert ist.](./media/connection-monitor-2-preview/cm-tg-2.png)

Sie können einen Verbindungsmonitor über das [Azure-Portal](./connection-monitor-create-using-portal.md), über [ARMClient](./connection-monitor-create-using-template.md) oder über [Azure PowerShell](connection-monitor-create-using-powershell.md) erstellen.

Alle Quellen, Ziele und Testkonfigurationen, die Sie einer Testgruppe hinzufügen, werden in einzelne Tests aufgeteilt. Hier sehen Sie ein Beispiel dafür, wie Quellen und Ziele angeordnet werden:

* Testgruppe: TG1
* Quellen: 3 (A, B, C)
* Ziele: 2 (D, E)
* Testkonfigurationen: 2 (Config 1, Config 2)
* Gesamtzahl erstellter Tests: 12

| Testnummer | `Source` | Destination | Testkonfiguration |
| --- | --- | --- | --- |
| 1 | Ein | D | Config 1 |
| 2 | Ein | D | Config 2 |
| 3 | Ein | E | Config 1 |
| 4 | Ein | E | Config 2 |
| 5 | B | D | Config 1 |
| 6 | B | D | Config 2 |
| 7 | B | E | Config 1 |
| 8 | B | E | Config 2 |
| 9 | C | D | Config 1 |
| 10 | C | D | Config 2 |
| 11 | C | E | Config 1 |
| 12 | C | E | Config 2 |
| | |

### <a name="scale-limits"></a>Skalierungslimits

Für Verbindungsmonitore gelten die folgenden Skalierungslimits:

* Maximale Anzahl von Verbindungsmonitoren pro Abonnement und Region: 100
* Maximale Anzahl von Testgruppen pro Verbindungsmonitor: 20
* Maximale Anzahl von Quellen und Zielen pro Verbindungsmonitor: 100
* Maximale Anzahl von Testkonfigurationen pro Verbindungsmonitor:

## <a name="analyze-monitoring-data-and-set-alerts"></a>Analysieren von Überwachungsdaten und Festlegen von Warnungen

Nachdem Sie einen Verbindungsmonitor erstellt haben, überprüfen die Quellen die Konnektivität mit den Zielen auf Grundlage der Testkonfiguration.

### <a name="checks-in-a-test"></a>Überprüfungen in einem Test

Je nach dem von Ihnen in der Testkonfiguration ausgewählten Protokoll führt der Verbindungsmonitor eine Reihe von Überprüfungen für das Quelle-Ziel-Paar aus. Die Überprüfungen werden gemäß der ausgewählten Testhäufigkeit durchgeführt.

Wenn Sie HTTP verwenden, berechnet der Dienst die Anzahl von HTTP-Antworten, die einen gültigen Antwortcode zurückgegeben haben. Sie können gültige Antwortcodes mithilfe von PowerShell und der Azure CLI festlegen. Das Ergebnis bestimmt den Prozentsatz der Überprüfungen mit Fehlern. Zum Berechnen der Roundtripzeit misst der Dienst die Zeit zwischen einem HTTP-Aufruf und der Antwort.

Wenn Sie TCP oder ICMP verwenden, berechnet der Dienst den Prozentsatz an Paketverlusten, um den Prozentsatz der Überprüfungen mit Fehlern zu ermitteln. Zum Berechnen der Roundtripzeit misst der Dienst die Zeit, die zum Empfangen der Bestätigung (ACK) für die gesendeten Pakete benötigt wird. Wenn Sie Traceroute-Daten für Ihre Netzwerktests aktiviert haben, können Sie Verluste und Latenzen zwischen den Hops für Ihr lokales Netzwerk anzeigen.

### <a name="states-of-a-test"></a>Teststatus

Abhängig von den Daten, die bei den Überprüfungen zurückgegeben werden, können Tests die folgenden Status aufweisen:

* **Erfolgreich**: Die tatsächlichen Werte für den Prozentsatz der Überprüfungen mit Fehlern und die Roundtripzeit liegen innerhalb der angegebenen Schwellenwerte.
* **Fehler**: Die tatsächlichen Werte für den Prozentsatz der Überprüfungen mit Fehlern oder die Roundtripzeit lagen oberhalb der angegebenen Schwellenwerte. Wenn kein Schwellenwert angegeben ist, weist ein Test den Status *Fehler* auf, sobald der Prozentsatz der Überprüfungen mit Fehlern bei 100 liegt.
* **Warnung**: 
     * Wenn ein Schwellenwert angegeben ist und der Verbindungsmonitor bei Überprüfungen mit Fehlern einen Prozentsatz von mehr als 80 Prozent des Schwellenwerts beobachtet, wird der Test als *Warnung* gekennzeichnet.
     * Wenn keine Schwellenwerte angegeben sind, weist der Verbindungsmonitor automatisch einen Schwellenwert zu. Wird dieser Schwellenwert überschritten, ändert sich der Teststatus in *Warnung*.Für die Roundtripzeit (RTT) in TCP- oder ICMP-Tests beträgt der Schwellenwert 750 ms. Für den Prozentsatz der Überprüfungen mit Fehlern beträgt der Schwellenwert 10 Prozent. 
* **Unbestimmt**: Keine Daten im Log Analytics-Arbeitsbereich.Überprüfen Sie die Metriken. 
* **Nicht ausgeführt**: Deaktiviert durch Deaktivierung der Testgruppe.  

### <a name="data-collection-analysis-and-alerts"></a>Datensammlung, Datenanalyse und Datenwarnungen

Die vom Verbindungsmonitor gesammelten Daten werden im Log Analytics-Arbeitsbereich gespeichert. Sie haben diesen Arbeitsbereich beim Erstellen des Verbindungsmonitors eingerichtet. 

Über Azure Monitor-Metriken sind auch Überwachungsdaten verfügbar. Mit Log Analytics können Sie die Überwachungsdaten über den gewünschten Zeitraum aufbewahren. Azure Monitor speichert Metriken standardmäßig nur für 30 Tage. 

Sie können [metrikbasierte Warnungen für die Daten festlegen](https://azure.microsoft.com/blog/monitor-at-scale-in-azure-monitor-with-multi-resource-metric-alerts/).

#### <a name="monitoring-dashboards"></a>Überwachungsdashboards

In den Überwachungsdashboards können Sie eine Liste der Verbindungsmonitore anzeigen, auf die Sie für Ihre Abonnements, Regionen, Zeitstempel, Quellen und Zieltypen zugreifen können.

Wenn Sie von Network Watcher zum Verbindungsmonitor wechseln, können Sie Daten auf folgende Arten anzeigen:

* **Verbindungsmonitor**: Dies ist eine Liste aller Verbindungsmonitore, die für Ihre Abonnements, Regionen, Zeitstempel, Quellen und Zieltypen erstellt wurden. Dies ist die Standardansicht.
* **Testgruppen**: Dies ist eine Liste aller Testgruppen, die für Ihre Abonnements, Regionen, Zeitstempel, Quellen und Zieltypen erstellt wurden. Diese Testgruppen sind nicht nach Verbindungsmonitoren gefiltert.
* **Test**: Dies ist eine Liste aller Tests, die für Ihre Abonnements, Regionen, Zeitstempel, Quellen und Zieltypen ausgeführt wurden. Diese Tests sind nicht nach Verbindungsmonitoren oder Testgruppen gefiltert.

In der folgenden Abbildung sind die drei Datenansichten mit Pfeil 1 gekennzeichnet.

Im Dashboard können Sie die einzelnen Verbindungsmonitore erweitern, um deren Testgruppen anzuzeigen. Anschließend können Sie die einzelnen Testgruppen erweitern, um die darin ausgeführten Tests anzuzeigen. 

Für eine Liste sind folgende Filter verfügbar:

* **Filter der obersten Ebene**: Durchsuchen Sie die Liste nach Text, Entitätstyp (Verbindungsmonitor, Testgruppe oder Test), Zeitstempel und Umfang. Der Umfang umfasst Abonnements, Regionen, Quellen und Zieltypen. Siehe Kasten 1 in der folgenden Abbildung.
* **Zustandsbasierte Filter**: Filtern Sie nach dem Zustand des Verbindungsmonitors, der Testgruppe oder des Tests. Siehe Kasten 2 in der folgenden Abbildung.
* **Warnungsbasierter Filter**: Filtern Sie nach Warnungen, die für die Verbindungsmonitorressource ausgelöst werden. Siehe Kasten 3 in der folgenden Abbildung.

  :::image type="content" source="./media/connection-monitor-2-preview/cm-view.png" alt-text="Screenshot: Filtern von Ansichten der Verbindungsmonitore, Testgruppen und Tests im Verbindungsmonitor" lightbox="./media/connection-monitor-2-preview/cm-view.png":::
    
Wenn Sie beispielsweise alle Tests im Verbindungsmonitor anzeigen möchten, bei denen die Quell-IP-Adresse 10.192.64.56 lautet, gehen Sie folgendermaßen vor:
1. Ändern Sie die Ansicht in **Test**.
1. Geben Sie im Feld **Suche** die Adresse **10.192.64.56** ein.
1. Wählen Sie unter **Umfang** im Filter der obersten Ebene **Quellen** aus.

Wenn Sie nur Tests mit Fehlern im Verbindungsmonitor anzeigen möchten, bei denen die Quell-IP-Adresse 10.192.64.56 lautet, gehen Sie folgendermaßen vor:
1. Ändern Sie die Ansicht in **Test**.
1. Wählen Sie aus den zustandsbasierten Filtern die Option **Fehler** aus.
1. Geben Sie im Feld **Suche** die Adresse **10.192.64.56** ein.
1. Wählen Sie unter **Umfang** im Filter der obersten Ebene **Quellen** aus.

Wenn Sie nur Tests mit Fehlern im Verbindungsmonitor anzeigen möchten, bei denen das Ziel „outlook.office365.com“ lautet, gehen Sie folgendermaßen vor:
1. Ändern Sie die Ansicht in **Test**.
1. Wählen Sie aus den zustandsbasierten Filtern die Option **Fehler** aus.
1. Geben Sie im Feld **Suche** das Ziel **office.live.com** ein.
1. Wählen Sie unter **Umfang** im Filter der obersten Ebene **Ziele** aus.
  
  :::image type="content" source="./media/connection-monitor-2-preview/tests-view.png" alt-text="Screenshot: Gefilterte Ansicht zur ausschließlichen Anzeige von Tests mit Fehlern für das Ziel „outlook.office365.com“" lightbox="./media/connection-monitor-2-preview/tests-view.png":::

Um den Grund für Fehler bei einem Verbindungsmonitor, einer Testgruppe oder einem Test zu erfahren, klicken Sie auf die Spalte **Ursache**. Diese gibt an, welcher Schwellenwert (Prozentsatz für Überprüfungsfehler oder Roundtripzeit) überschritten wurde, und zeigt zugehörige Diagnosemeldungen an.
  
  :::image type="content" source="./media/connection-monitor-2-preview/cm-reason-of-failure.png" alt-text="Screenshot: Fehlerursache für einen Verbindungsmonitor, einen Test oder eine Testgruppe" lightbox="./media/connection-monitor-2-preview/cm-reason-of-failure.png":::
    
Wenn Sie die Trends bei der Roundtripzeit und dem Prozentsatz der Überprüfungen mit Fehlern für einen Verbindungsmonitor anzeigen möchten, gehen Sie folgendermaßen vor:

1. Wählen Sie den Verbindungsmonitor aus, den Sie untersuchen möchten.

    :::image type="content" source="./media/connection-monitor-2-preview/cm-drill-landing.png" alt-text="Screenshot: Metriken für einen Verbindungsmonitor, Anzeige nach Testgruppe" lightbox="./media/connection-monitor-2-preview/cm-drill-landing.png":::

   In diesem Bereich werden die folgenden Abschnitte angezeigt:  
    * **Essentials**: Ressourcenspezifische Eigenschaften des ausgewählten Verbindungsmonitors. 
    * **Zusammenfassung**: 
        * Aggregierte Trendlinien für die Roundtripzeit und den Prozentsatz der Überprüfungen mit Fehlern für alle Tests im Verbindungsmonitor. Sie können eine bestimmte Zeit zum Anzeigen der Details festlegen.
        * Die Top 5 der Testgruppen, Quellen und Ziele basierend auf der Roundtripzeit oder dem Prozentsatz der Überprüfungen mit Fehlern. 
    * **Registerkarten**: Testgruppen, Quellen, Ziele und Testkonfigurationen. Hier werden Testgruppen, Quellen oder Ziele im Verbindungsmonitor aufgelistet. Überprüfen Sie fehlerhafte Tests, aggregierte Roundtripzeit und Prozentwerte für Überprüfungen mit Fehlern. Sie können auch in die Vergangenheit wechseln, um Daten anzuzeigen. 
    * **Probleme**: Probleme auf Hopebene für jeden Test im Verbindungsmonitor. 

   :::image type="content" source="./media/connection-monitor-2-preview/cm-drill-landing-2.png" alt-text="Screenshot: Metriken für einen Verbindungsmonitor, Anzeige nach Testgruppenteil 2" lightbox="./media/connection-monitor-2-preview/cm-drill-landing-2.png":::

1. In diesem Bereich können Sie folgende Aktionen ausführen:

    * Klicken Sie auf **Alle Tests anzeigen**, um alle Tests im Verbindungsmonitor anzuzeigen.

      * Wählen Sie Tests aus, und vergleichen Sie sie.
    
        :::image type="content" source="./media/connection-monitor-2-preview/cm-compare-test.png" alt-text="Screenshot: Vergleich zweier Tests." lightbox="./media/connection-monitor-2-preview/cm-compare-test.png":::
    
      * Verwenden Sie Cluster, um Verbundressourcen wie virtuelle Netzwerke und Subnetze auf die untergeordneten Ressourcen zu erweitern.

      * Zeigen Sie die Topologie für beliebige Tests an, indem Sie die Topologie auswählen.    

    * Klicken Sie auf **Alle Testgruppen anzeigen**, **Testkonfigurationen anzeigen**, **Quellen anzeigen** und **Ziele anzeigen**, um die jeweiligen Details anzuzeigen. 

    * Wählen Sie eine Testgruppe, eine Testkonfiguration, eine Quelle oder ein Ziel aus, um alle Tests in der Entität anzuzeigen.

Wenn Sie die Trends bei der Roundtripzeit und dem Prozentsatz der Überprüfungen mit Fehlern für eine Testgruppe anzeigen möchten, gehen Sie folgendermaßen vor:

* Wählen Sie die Testgruppe aus, die Sie untersuchen möchten. 

   Sie können diese wie im Verbindungsmonitor anzeigen und durchsuchen: Essentials, Zusammenfassung, Tabelle für Testgruppen, Quellen, Ziele und Testkonfigurationen. 

Wenn Sie die Trends bei der Roundtripzeit und dem Prozentsatz der Überprüfungen mit Fehlern für einen Test anzeigen möchten, gehen Sie folgendermaßen vor:

1. Wählen Sie den Test aus, den Sie untersuchen möchten. Sie können die Netzwerktopologie und die End-to-End-Trenddiagramme für den Prozentsatz von Überprüfungen mit Fehlern sowie die Roundtripzeit anzeigen. 

1. Wählen Sie in der Topologie einen beliebigen Hop im Pfad aus, um die erkannten Probleme anzuzeigen. Bei den Hops handelt es sich um Azure-Ressourcen. 

   > [!NOTE]
   > Für lokale Netzwerke steht diese Funktion derzeit nicht zur Verfügung.

   :::image type="content" source="./media/connection-monitor-2-preview/cm-test-topology.png" alt-text="Screenshot: Topologieansicht eines Tests" lightbox="./media/connection-monitor-2-preview/cm-test-topology.png":::

#### <a name="log-queries-in-log-analytics"></a>Protokollabfragen in Log Analytics

Mit Log Analytics lassen sich benutzerdefinierte Ansichten Ihrer Überwachungsdaten erstellen. Alle angezeigten Daten stammen aus Log Analytics. Sie können Daten im Repository interaktiv analysieren. Korrelieren Sie die Daten aus der Agent-Integritätsdiagnose oder anderen Lösungen, die auf Log Analytics basieren. Exportieren Sie die Daten nach Excel oder Power BI, oder erstellen Sie einen freigabefähigen Link.

#### <a name="network-topology-in-connection-monitor"></a>Netzwerktopologie im Verbindungsmonitor 

Normalerweise erstellen Sie eine Verbindungsmonitor-Topologie anhand des Ergebnisses eines Traceroute-Befehls, der vom Agent ausgeführt wird. Der Traceroute-Befehl ruft im Grunde alle Hops von der Quelle bis zum Ziel ab.

In Fällen, in denen sich die Quelle oder das Ziel innerhalb der Azure-Grenzen befindet, erstellen Sie die Topologie jedoch, indem Sie die Ergebnisse von zwei unterschiedlichen Vorgängen zusammenführen.
Der erste Vorgang ist das Ergebnis des Traceroute-Befehls. Der zweite Vorgang ist das Ergebnis eines internen Befehls, der eine logische Route auf Grundlage der Netzwerkkonfiguration (des Kunden) innerhalb der Azure-Grenzen identifiziert. Dieser interne Befehl ähnelt dem Network Watcher-Diagnosetool für den nächsten Hop.  

Da der zweite Vorgang logisch ist und der erste Vorgang in der Regel keine Hops innerhalb der Azure-Grenzen identifiziert, werden für einige Hops im zusammengeführten Ergebnis (hauptsächlich Hops innerhalb von Azure-Grenzen) keine Latenzwerte angezeigt.

#### <a name="metrics-in-azure-monitor"></a>Metriken in Azure Monitor

Bei den Verbindungsmonitoren, die vor der Verbindungsmonitor-Funktionalität erstellt wurden, sind alle vier Metriken verfügbar: „% Fehlerhafte Tests“, „AverageRoundtripMs“, „ChecksFailedPercent“ und „RoundTripTimeMs“. 

Bei den Verbindungsmonitoren, die in der Verbindungsmonitor-Benutzeroberfläche erstellt wurden, stehen die Daten nur für die Metriken „ChecksFailedPercent“, „RoundTripTimeMs“ und „Testergebnis“ verfügbar.

Metriken werden gemäß Überwachungshäufigkeit generiert und beschreiben Aspekte eines Verbindungsmonitors zu einem bestimmten Zeitpunkt. Verbindungsmonitor-Metriken weisen zudem mehrere Dimensionen auf, z. B. SourceName, DestinationName, TestConfiguration und TestGroup. Sie können diese Dimensionen verwenden, um bestimmte Daten zu visualisieren und sie beim Definieren von Warnungen als Ziel festzulegen.

Azure-Metriken ermöglichen derzeit eine Mindestgranularität von 1 Minute. Wenn eine Häufigkeit von weniger als 1 Minute angegeben wird, werden aggregierte Ergebnisse angezeigt.

  :::image type="content" source="./media/connection-monitor-2-preview/monitor-metrics.png" alt-text="Screenshot: Metriken im Verbindungsmonitor" lightbox="./media/connection-monitor-2-preview/monitor-metrics.png":::

Legen Sie bei Verwendung von Metriken den Ressourcentyp auf **Microsoft.Network/networkWatchers/connectionMonitors** fest.

| Metrik | `Display name` | Einheit | Aggregationstyp | BESCHREIBUNG | Dimensionen |
| --- | --- | --- | --- | --- | --- |
| ProbesFailedPercent (klassisch) | Fehlerhafte Tests in Prozent (klassisch) | Prozentwert | Average | Prozentsatz fehlerhafter Tests bei der Konnektivitätsüberwachung.<br>Diese Metrik ist nur für den Verbindungsmonitor (klassisch) verfügbar.  | Keine Dimensionen |
| AverageRoundtripMs (klassisch) | Durchschn. Roundtripzeit (ms) (klassisch) | Millisekunden | Average | Durchschnittliche Netzwerk-Roundtripzeit für die Konnektivitätsüberwachung von Testdatenverkehr, der zwischen Quelle und Ziel gesendet wurde.<br>Diese Metrik ist nur für den Verbindungsmonitor (klassisch) verfügbar. |             Keine Dimensionen |
| ChecksFailedPercent | % der Überprüfungen mit Fehlern | Prozentwert | Average | Prozentsatz der Überprüfungen mit Fehlern für einen Test. | ConnectionMonitorResourceId <br>SourceAddress <br>SourceName <br>SourceResourceId <br>SourceType <br>Protocol <br>DestinationAddress <br>DestinationName <br>DestinationResourceId <br>DestinationType <br>DestinationPort <br>TestGroupName <br>TestConfigurationName <br>Region <br>SourceIP <br>DestinationIP <br>SourceSubnet <br>DestinationSubnet |
| RoundTripTimeMs | Roundtripzeit (ms) | Millisekunden | Average | Roundtripzeit für Überprüfungen, die zwischen Quelle und Ziel gesendet wurden. Es wird kein Durchschnittswert gebildet. | ConnectionMonitorResourceId <br>SourceAddress <br>SourceName <br>SourceResourceId <br>SourceType <br>Protocol <br>DestinationAddress <br>DestinationName <br>DestinationResourceId <br>DestinationType <br>DestinationPort <br>TestGroupName <br>TestConfigurationName <br>Region <br>SourceIP <br>DestinationIP <br>SourceSubnet <br>DestinationSubnet |
| TestResult | Testergebnis | Anzahl | Average | Testergebnisse des Verbindungsmonitors. <br>Interpretation der Ergebniswerte: <br>0 –&nbsp;Unbestimmt <br>1 – Übergeben <br>2 – Warnung <br>3 – Fehler| SourceAddress <br>SourceName <br>SourceResourceId <br>SourceType <br>Protocol <br>DestinationAddress <br>DestinationName <br>DestinationResourceId <br>DestinationType <br>DestinationPort <br>TestGroupName <br>TestConfigurationName <br>SourceIP <br>DestinationIP <br>SourceSubnet <br>DestinationSubnet |
| | |

#### <a name="metric-based-alerts-for-connection-monitor"></a>Auf Metriken basierende Warnungen für den Verbindungsmonitor

Sie können Metrikwarnungen für Verbindungsmonitore mithilfe der folgenden Methoden erstellen: 

* Erstellen Sie Metrikwarnungen im Verbindungsmonitor bei der Erstellung von Verbindungsmonitoren über das [Azure-Portal](connection-monitor-preview-create-using-portal.md#). 
* Erstellen Sie Metrikwarnungen im Verbindungsmonitor über **Warnungen konfigurieren** im Dashboard. 
* In Azure Monitor erstellen Sie Metrikwarnungen wie folgt: 

    1. Wählen Sie die Verbindungsmonitorressource aus, die Sie im Verbindungsmonitor erstellt haben.
    1. Stellen Sie sicher, dass **Metrik** als Signaltyp für den Verbindungsmonitor ausgewählt ist.
    1. Wählen Sie unter **Bedingung hinzufügen** für den **Signalnamen** die Option **ChecksFailedPercent** oder **RoundTripTimeMs** aus.
    1. Wählen Sie für **Signaltyp** die Option **Metriken** aus. Wählen Sie z. B. **ChecksFailedPercent** aus.
    1. Alle Dimensionen für die Metrik werden aufgelistet. Wählen Sie den Dimensionsnamen und -wert aus. Wählen Sie z. B. **Quelladresse** aus, und geben Sie dann die IP-Adresse einer beliebigen Quelle in Ihrem Verbindungsmonitor ein.
    1. Geben Sie unter **Warnungslogik** folgende Werte ein:
        * **Bedingungstyp**: **Statisch**
        * **Bedingung** und **Schwellenwert**
        * **Aggregationsgranularität und Häufigkeit der Auswertung**: Der Verbindungsmonitor aktualisiert die Daten jede Minute.
    1. Wählen Sie unter **Aktionen** Ihre Aktionsgruppe aus.
    1. Legen Sie die Warnungsdetails fest.
    1. Erstellen Sie die Warnungsregel.

  :::image type="content" source="./media/connection-monitor-2-preview/mdm-alerts.jpg" alt-text="Screenshot: Bereich „Regel erstellen“ in Azure Monitor mit hervorgehobenen Feldern für „Quelladresse“ und „Quellendpunktname“" lightbox="./media/connection-monitor-2-preview/mdm-alerts.jpg":::

## <a name="diagnose-issues-in-your-network"></a>Diagnostizieren von Problemen in Ihrem Netzwerk

Mit dem Verbindungsmonitor können Sie Probleme in Ihrem Verbindungsmonitor und Ihrem Netzwerk diagnostizieren. Probleme in Ihrem Hybridnetzwerk werden von den Log Analytics-Agents erkannt, die Sie zuvor installiert haben. Probleme in Azure werden von der Network Watcher-Erweiterung erkannt. 

Sie können Probleme im Azure-Netzwerk in der Netzwerktopologie anzeigen.

Bei Netzwerken, deren Quellen sich auf lokalen VMs befinden, können die folgenden Probleme erkannt werden:

* Timeout bei der Anforderung.
* Der Endpunkt wurde nicht über DNS aufgelöst (vorübergehend oder dauerhaft). Die URL ist ungültig.
* Es wurden keine Hosts gefunden.
* Die Quelle kann keine Verbindung zum Ziel herstellen. Das Ziel ist nicht über ICMP erreichbar.
* Zertifikatbezogene Probleme: 
    * Der Agent muss mit dem Clientzertifikat authentifiziert werden. 
    * Es besteht kein Zugriff auf die Zertifikatssperrliste. 
    * Der Hostname des Endpunkts stimmt nicht mit dem Antragsteller des Zertifikats oder dem alternativen Antragstellernamen überein. 
    * Das Stammzertifikat fehlt im Speicher der vertrauenswürdigen Zertifizierungsstellen auf dem lokalen Computer der Quelle. 
    * Das SSL-Zertifikat ist abgelaufen, ungültig, widerrufen oder nicht kompatibel.

Bei Netzwerken, deren Quellen sich auf Azure-VMs befinden, können die folgenden Probleme erkannt werden:

* Probleme mit Agents:
    * Der Agent wurde angehalten.
    * Fehler bei der DNS-Auflösung.
    * Es lauscht keine Anwendung oder kein Listener am Zielport.
    * Der Socket konnte nicht geöffnet werden.
* Probleme mit dem VM-Status: 
    * Wird gestartet
    * Wird beendet
    * Beendet
    * Zuordnung wird aufgehoben
    * Zuordnung aufgehoben
    * Wird neu gestartet
    * Nicht zugewiesen
* Der ARP-Tabelleneintrag fehlt.
* Der Datenverkehr wurde aufgrund von Problemen mit der lokalen Firewall oder NSG-Regeln blockiert.
* Probleme mit dem Gateway des virtuellen Netzwerks: 
    * Es fehlen Routen.
    * Der Tunnel zwischen zwei Gateways ist getrennt oder fehlt.
    * Das zweite Gateway wurde nicht vom Tunnel gefunden.
    * Es wurden keine Peeringinformationen gefunden.

  > [!NOTE]
  > Wenn eines von zwei verbundenen Gateways sich nicht in derselben Region wie der Quellendpunkt befindet, identifiziert der Verbindungsmonitor es für die Topologieansicht mit „Keine Route erkannt“. Die Konnektivität wird dadurch nicht beeinträchtigt. Dies ist ein bekanntes Problem, das zurzeit behoben wird. 

  * Die Route fehlte in Microsoft Edge.
  * Der Datenverkehr wurde aufgrund von Systemrouten oder einer benutzerdefinierten Route beendet.
  * Das Border Gateway Protocol (BGP) ist für die Gatewayverbindung nicht aktiviert.
  * Der DIP-Test (dynamische IP-Adresse) ist beim Lastenausgleich deaktiviert.

## <a name="compare-azure-connectivity-monitoring-support-types"></a>Vergleichen der Unterstützungsarten für die Azure-Konnektivitätsüberwachung 

Sie können Tests vom Netzwerkleistungsmonitor (NPM) und vom Verbindungsmonitor (klassisch) mit nur einem Mausklick und ohne jegliche Ausfallzeit zum neuesten Verbindungsmonitor migrieren.
 
Die Migration hilft beim Liefern der folgenden Ergebnisse:

* Die Funktionen von Agents und Firewalleinstellungen bleiben unverändert erhalten. Es sind keine Änderungen erforderlich. 

* Vorhandene Verbindungsmonitore werden „Verbindungsmonitor > Testgruppe > Testformat“ zugeordnet. Durch Auswahl von **Bearbeiten** können Sie die Eigenschaften des neuesten Verbindungsmonitors anzeigen und ändern. Laden Sie eine Vorlage herunter, um Änderungen am Verbindungsmonitor vorzunehmen und diese über Azure Resource Manager zu übermitteln. 
* Virtuelle Azure-Computer mit der Network Watcher-Erweiterung senden Daten an den Arbeitsbereich und die Metriken. Verbindungsmonitore stellen die Daten über die neuen Metriken „ChecksFailedPercent“ und „RoundTripTimeMs“ anstatt über die veralteten Metriken „ProbesFailedPercent“ und „AverageRoundtripMs“ zur Verfügung. Die alten Metriken werden zu neuen Metriken migriert, d. h. „ProbesFailedPercent“ zu „ChecksFailedPercent“ und „AverageRoundtripMs“ zu „RoundTripTimeMs“.
* Datenüberwachung:
   * **Warnungen**: Wurden automatisch zu den neuen Metriken migriert.
   * **Analysen und Integrationen**: Erfordern die manuelle Bearbeitung des Satzes der Metriken. 
   
Es gibt mehrere Gründe für die Migration vom Netzwerkleistungsmonitor und dem Verbindungsmonitor (klassisch) zum Verbindungsmonitor. In der folgenden Tabelle sind einige Anwendungsfälle aufgeführt, die zeigen, wie der neueste Verbindungsmonitor gegenüber dem Netzwerkleistungsmonitor und dem Verbindungsmonitor (klassisch) abschneidet. 

 | Feature  | Netzwerkleistungsmonitor | Verbindungsmonitor (klassisch) | Verbindungsmonitor |
 | -------  | --------------------------- | -------------------------- | ------------------ | 
 | Einheitliche Benutzeroberfläche für Azure- und Hybridüberwachung | Nicht verfügbar | Nicht verfügbar | Verfügbar |
 | Abonnement-, regions- und arbeitsbereichsübergreifende Überwachung | Gestattet abonnement- und regionsübergreifende, aber keine arbeitsbereichsübergreifende Überwachung. |   Nicht verfügbar | Gestattet abonnement- und arbeitsbereichsübergreifende Überwachung. Für die arbeitsbereichsübergreifende Überwachung gelten regionale Grenzen.  |
 | Unterstützung zentralisierter Arbeitsbereiche |  Nicht verfügbar | Nicht verfügbar   | Verfügbar |
 | Mehrere Quellen können mehrere Ziele pingen | Die Leistungsüberwachung ermöglicht es mehreren Quellen, mehrere Ziele zu pingen. Der Dienstkonnektivitätsmonitor ermöglicht es mehreren Quellen, einen einzelnen Dienst oder eine URL zu pingen. ExpressRoute ermöglicht es mehreren Quellen, mehrere Ziele zu pingen. | Nicht verfügbar | Verfügbar |
 | Einheitliche Topologie für lokale Standorte, Internethops und Azure | Nicht verfügbar | Nicht verfügbar    | Verfügbar |
 | HTTP-Statuscodeüberprüfungen | Nicht verfügbar  | Nicht verfügbar | Verfügbar |
 | Konnektivitätsdiagnose | Nicht verfügbar | Verfügbar | Verfügbar |
 | Zusammengesetzte Ressourcen: virtuelle Netzwerke, Subnetze und lokale benutzerdefinierte Netzwerke | Die Leistungsüberwachung unterstützt Subnetze, lokale Netzwerke und logische Netzwerkgruppen. Der Dienstkonnektivitätsmonitor und ExpressRoute unterstützen nur die lokale und arbeitsbereichsübergreifende Überwachung. | Nicht verfügbar | Verfügbar |
 | Konnektivitätsmetriken und Dimensionsmessungen |   Nicht verfügbar | Verlust, Latenz und Roundtripzeit. | Verfügbar |
 | Automatisierung: PowerShell, Azure CLI, Terraform | Nicht verfügbar | Verfügbar | Verfügbar |
 | Unterstützung für Linux | Die Leistungsüberwachung unterstützt Linux. Der Dienstkonnektivitätsmonitor und ExpressRoute bieten keine Unterstützung für Linux. | Verfügbar | Verfügbar |
 | Unterstützung für Public, Government, Mooncake und Air-Gapped Cloud | Verfügbar | Verfügbar | Verfügbar|
 | | |


## <a name="faq"></a>Häufig gestellte Fragen

### <a name="does-connection-monitor-support-classic-vms"></a>Unterstützt der Verbindungsmonitor klassische VMs?
Nein, klassische VMs werden vom Verbindungsmonitor nicht unterstützt. Es wird empfohlen, IaaS-Ressourcen (Infrastructure-as-a-Service) vom klassischen Bereitstellungsmodell zum Azure Resource Manager zu migrieren, weil klassische Ressourcen [veraltet](../virtual-machines/classic-vm-deprecation.md) sind. Weitere Informationen finden Sie unter [Plattformgestützte Migration von IaaS-Ressourcen vom klassischen Bereitstellungsmodell zu Azure Resource Manager](../virtual-machines/migration-classic-resource-manager-overview.md).

### <a name="what-if-my-topology-isnt-decorated-or-my-hops-have-missing-information"></a>Was geschieht, wenn meine Topologie nicht ergänzt wird oder wenn Informationen für meine Hops fehlen?
Die Topologie kann von Nicht-Azure zu Azure nur ergänzt werden, wenn sich die Azure-Zielressource und die Verbindungsmonitor-Ressource in derselben Region befinden. 

### <a name="what-happens-if-the-connection-monitor-creation-fails-with-the-following-error-we-dont-allow-creating-different-endpoints-for-the-same-vm"></a>Was geschieht, wenn beim Erstellen des Verbindungsmonitors folgender Fehler auftritt: „Es ist nicht gestattet, verschiedene Endpunkt für dieselbe VM zu erstellen“?
Dieselbe Azure-VM kann nicht mit unterschiedlichen Konfigurationen in demselben Verbindungsmonitor verwendet werden. Beispielsweise wird die Verwendung derselben VM mit einem Filter und ohne einen Filter in demselben Verbindungsmonitor nicht unterstützt.

### <a name="what-happens-if-the-test-failure-reason-is-nothing-to-display"></a>Was geschieht, wenn der Grund für den Testfehler „Keine Elemente für Anzeige vorhanden“ lautet?
Die im Dashboard des Verbindungsmonitors angezeigten Probleme werden während der Topologieerkennung oder der Hoperkundung gefunden. Es kann Fälle geben, in denen der eingestellte Schwellenwert für % Verlust oder RTT überschritten wird, aber keine Probleme bei den Hops gefunden werden.

### <a name="when-the-existing-connection-monitor-classic-is-migrated-to-the-latest-connection-monitor-what-happens-if-the-external-endpoint-tests-are-migrated-with-the-tcp-protocol-only"></a>Was geschieht, wenn beim Migrieren des vorhandenen Verbindungsmonitors (klassisch) zum neuesten Verbindungsmonitor die externen Endpunkttests nur mit dem TCP-Protokoll migriert werden? 
Im Verbindungsmonitor (klassisch) gibt es keine Option zur Protokollauswahl. Daher kann eine Verbindung mit externen Endpunkten nicht mithilfe des HTTP-Protokolls im Verbindungsmonitor (klassisch) angegeben werden. Alle Tests verwenden im Verbindungsmonitor (klassisch) nur das TCP-Protokoll. Aus diesem Grund erstellen wir bei der Migration eine TCP-Konfiguration in Tests im Verbindungsmonitor. 

## <a name="next-steps"></a>Nächste Schritte

Hier erfahren Sie, wie Sie über folgende Optionen einen Verbindungsmonitor erstellen:
    
   * [Azure-Portal](./connection-monitor-create-using-portal.md)  
   * [ARMClient](./connection-monitor-create-using-template.md)
