---
title: Migrieren vom Netzwerkleistungsmonitor zum Verbindungsmonitor
titleSuffix: Azure Network Watcher
description: Erfahren Sie, wie Sie vom Netzwerkleistungsmonitor zum Verbindungsmonitor migrieren.
services: network-watcher
documentationcenter: na
author: vinynigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: vinigam
ms.openlocfilehash: 0ec16b16c8e71d764fb0fe21520eb407493ed8d7
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/30/2021
ms.locfileid: "113105361"
---
# <a name="migrate-to-connection-monitor-from-network-performance-monitor"></a>Migrieren vom Netzwerkleistungsmonitor zum Verbindungsmonitor

> [!IMPORTANT]
> Ab dem 1. Juli 2021 ist es im Netzwerkleistungsmonitor nicht mehr möglich, neue Tests in einem vorhandenen Arbeitsbereich hinzuzufügen oder einen neuen Arbeitsbereich zu aktivieren. Sie können weiterhin die Tests verwenden, die vor dem 1. Juli 2021 erstellt wurden. Migrieren Sie Ihre Tests vor dem 29. Februar 2024 aus dem Netzwerkleistungsmonitor zum neuen Verbindungsmonitor in Azure Network Watcher, um Dienstunterbrechungen im Zusammenhang mit Ihren aktuellen Workloads zu minimieren.

Sie können Tests vom Netzwerkleistungsmonitor (NPM) mit nur einem Mausklick und ohne Ausfallzeit zum neuen, verbesserten Verbindungsmonitor migrieren. Weitere Informationen zu den Vorteilen finden Sie unter [Verbindungsmonitor](./connection-monitor-overview.md).


## <a name="key-points-to-note"></a>Wichtige Hinweise

Durch die Migration können die folgenden Ergebnisse erzielt werden:

* Die Funktion lokaler Agents und die Firewalleinstellungen bleiben unverändert erhalten. Es sind keine Änderungen erforderlich. Log Analytics-Agents, die auf Azure-VMs installiert sind, müssen durch die [Network Watcher-Erweiterung](../virtual-machines/extensions/network-watcher-windows.md) ersetzt werden.
* Vorhandene Test werden „Verbindungsmonitor“ > „Testgruppe“ > „Testformat“ zugeordnet. Wählen Sie **Bearbeiten** aus, um die Eigenschaften des neuen Verbindungsmonitors anzuzeigen und zu ändern. Laden Sie eine Vorlage herunter, um Änderungen vorzunehmen, und übermitteln Sie sie mit Azure Resource Manager.
* Agents senden Daten sowohl an den Log Analytics-Arbeitsbereich als auch an Metriken.
* Datenüberwachung:
   * **Daten in Log Analytics:** Vor der Migration verbleiben die Daten im Arbeitsbereich, in dem NPM in der Tabelle „NetworkMonitoring“ konfiguriert ist. Nach der Migration werden die Daten in den Tabellen „NetworkMonitoring“, „NWConnectionMonitorTestResult“ und „NWConnectionMonitorPathResult“ im selben Arbeitsbereich gespeichert. Nach dem Deaktivieren der Tests in NPM werden die Daten nur noch in den Tabellen „NWConnectionMonitorTestResult“ und „NWConnectionMonitorPathResult“ gespeichert.
   * **Protokollbasierte Warnungen, Dashboards und Integrationen**: Sie müssen die Abfragen anhand der neuen Tabellen „NWConnectionMonitorTestResult“ und „NWConnectionMonitorPathResult“ manuell bearbeiten. Informationen zum Neuerstellen der Warnungen in Metriken finden Sie unter [Überwachung der Netzwerkkonnektivität mit dem Verbindungsmonitor](./connection-monitor-overview.md#metrics-in-azure-monitor).
* Für ExpressRoute-Überwachung:
    * **Vollständige Verluste und Latenzen:** Dies wird vom Verbindungsmonitor einfacher als von NPM unterstützt, da Benutzer nicht konfigurieren müssen, welche Verbindungen und Peerings überwacht werden sollen. Verbindungen im Pfad werden automatisch ermittelt, und Daten werden in Metriken zur Verfügung gestellt (schneller als bei Log Analytics, wo NPM die Ergebnisse gespeichert hat). Die Topologie funktioniert auch unverändert.
    * **Bandbreitenmessungen:** Mit der Einführung von Metriken bezüglich der Bandbreite, hat sich der auf Log Analytics basierende Ansatz von NPM bei der Bandbreitenüberwachung für ExpressRoute-Kunden als ineffizient erwiesen. Diese Funktion ist jetzt im Verbindungsmonitor verfügbar.
    
## <a name="prerequisites"></a>Voraussetzungen

* Stellen Sie sicher, dass Network Watcher in Ihrem Abonnement und in der Region des Log Analytics-Arbeitsbereichs aktiviert ist. Andernfalls wird die folgende Fehlermeldung angezeigt: „Bevor Sie die Migration starten, aktivieren Sie die Network Watcher-Erweiterung im Auswahlabonnement und am Standort des ausgewählten LA-Arbeitsbereichs.“
* Falls Sie eine Azure-VM verwenden, die einer anderen Region/einem anderen Abonnement angehört als die/dem, die/das der Log Analytics-Arbeitsbereich als Endpunkt verwendet, stellen Sie sicher, dass Network Watcher für dieses Abonnement und diese Region aktiviert ist.   
* Virtuelle Azure-Computer, auf denen Log Analytics-Agents installiert sind, müssen mit der Network Watcher-Erweiterung aktiviert werden.

## <a name="migrate-the-tests"></a>Migrieren der Tests

Gehen Sie folgendermaßen vor, um von Netzwerkleistungsmonitor zu Verbindungsmonitor zu migrieren:

1. Wählen Sie in Network Watcher die Option **Verbindungsmonitor** und anschließend die Registerkarte **Import tests from NPM** (Tests aus dem NPM importieren) aus. 

    :::image type="content" source="./media/connection-monitor-2-preview/migrate-npm-to-cm-preview.png" alt-text="Migrieren von Tests von Netzwerkleistungsmonitor zu Verbindungsmonitor" lightbox="./media/connection-monitor-2-preview/migrate-npm-to-cm-preview.png":::
    
1. Wählen Sie in den Dropdownlisten Ihr Abonnement und den Arbeitsbereich und dann das NPM-Feature aus, das Sie migrieren möchten. 
1. Wählen Sie zum Migrieren der Tests **Importieren** aus.
* Wenn NPM im Arbeitsbereich nicht aktiviert ist, wird ein Fehler mit dem Hinweis angezeigt, dass keine gültige NPM-Konfiguration gefunden wurde. 
* Wenn in der Funktion, die Sie in Schritt 2 ausgewählt haben, keine Tests vorhanden sind, wird ein Fehler mit dem Hinweis angezeigt, dass <feature> im ausgewählten Arbeitsbereich nicht konfiguriert ist.
* Wenn keine gültigen Tests verfügbar sind, wird ein Fehler mit dem Hinweis angezeigt, dass der ausgewählte Arbeitsbereich keine gültigen Tests enthält.
* Ihre Tests können Agents enthalten, die nicht mehr aktiv sind, aber möglicherweise in der Vergangenheit aktiv waren. Es wird ein Fehler mit dem Hinweis angezeigt, dass nur einige Tests Agents enthalten, die nicht mehr aktiv sind. Liste der inaktiven Agents: {0}. Diese Agents wurden möglicherweise in der Vergangenheit ausgeführt, sind aber jetzt heruntergefahren/werden nicht mehr ausgeführt. Aktivieren Sie Agents, und migrieren Sie zu Verbindungsmonitor. Klicken Sie auf „Weiter“, um die Tests zu migrieren, die keine inaktiven Agents enthalten.

Nach dem Beginn der Migration werden die folgenden Änderungen durchgeführt: 
* Es wird eine neue Verbindungsmonitorressource erstellt.
   * Pro Region und Abonnement wird ein Verbindungsmonitor erstellt. Bei Tests mit lokalen Agents weist der Name des neuen Verbindungsmonitors das Format `<workspaceName>_"workspace_region_name"` auf. Bei Tests mit Azure-Agents weist der Name des neuen Verbindungsmonitors das Format `<workspaceName>_<Azure_region_name>` auf.
   * Die Überwachungsdaten werden jetzt in dem Log Analytics-Arbeitsbereich, in dem NPM aktiviert ist, in neuen Tabellen namens „NWConnectionMonitorTestResult“ und „NWConnectionMonitorPathResult“ gespeichert. 
   * Der Testname wird als Name der Testgruppe übernommen. Die Testbeschreibung wird nicht migriert.
   * Quell- und Zielendpunkte werden in der neuen Testgruppe erstellt und verwendet. Für lokale Agents werden die Endpunkte in `<workspaceName>_<FQDN of on-premises machine>` formatiert. Die Beschreibung von Agents wird nicht migriert.
   * Der Zielport und das Testintervall werden in eine Testkonfiguration namens `TC_<protocol>_<port>` und `TC_<protocol>_<port>_AppThresholds` verschoben. Das Protokoll wird anhand der Portwerte festgelegt. Für ICMP werden die Testkonfigurationen mit `TC_<protocol>` und `TC_<protocol>_AppThresholds` benannt. Falls Erfolgsschwellenwerte und andere optionale Eigenschaften festgelegt sind, werden sie migriert, andernfalls bleiben sie leer.
   * Wenn die Migrationstests Agents enthalten, die nicht ausgeführt werden, müssen Sie diese aktivieren und die Migration wiederholen.
* NPM wird nicht deaktiviert, daher können die migrierten Tests weiterhin Daten an die Tabellen „NetworkMonitoring“, „NWConnectionMonitorTestResult“ und „NWConnectionMonitorPathResult“ senden. Dadurch wird sichergestellt, dass vorhandene protokollbasierte Warnungen und Integrationen nicht beeinträchtigt werden.
* Der neu erstellte Verbindungsmonitor wird unter „Verbindungsmonitor“ angezeigt.

Achten Sie nach der Migration auf Folgendes:
* Deaktivieren Sie die Tests in NPM manuell. Bis dies geschehen ist, werden Ihnen diese weiterhin in Rechnung gestellt. 
* Wenn Sie NPM deaktivieren, erstellen Sie Ihre Warnungen für die Tabellen „NWConnectionMonitorTestResult“ und „NWConnectionMonitorPathResult“ neu, oder verwenden Sie Metriken. 
* Migrieren Sie externe Integrationen zu den Tabellen „NWConnectionMonitorTestResult“ und „NWConnectionMonitorPathResult“. Beispiele für externe Integrationen sind Dashboards in Power BI und Grafana sowie Integrationen mit SIEM-Systemen (Security Information and Event Management).

## <a name="common-errors-encountered"></a>Mögliche allgemeine Fehler

Im Anschluss finden Sie einige allgemeine Fehler, die während der Migration auftreten können: 

| Fehler  |    `Reason`   |
|---|---|
| „No valid NPM config found. Go to NPM UI to check config“ (Keine gültige NPM-Konfiguration gefunden. Überprüfen Sie die Konfiguration auf der NPM-Benutzeroberfläche.)     |     Dieser Fehler tritt auf, wenn der Benutzer „Import Tests from NPM“ (Tests aus dem NPM importieren) auswählt, um die Tests zu migrieren, der NPM im Arbeitsbereich aber nicht aktiviert ist.   |
|„Workspace selected does not have 'Service Connectivity Monitor' config“ (Der ausgewählte Arbeitsbereich verfügt nicht über die Konfiguration „Dienstkonnektivitätsmonitor“.)    |       Dieser Fehler tritt auf, wenn der Benutzer Tests vom NPM-Dienstkonnektivitätsmonitor zum Verbindungsmonitor migriert, im Dienstkonnektivitätsmonitor aber keine Tests konfiguriert sind. |
|„Workspace selected does not have 'ExpressRoute Monitor' config“ (Der ausgewählte Arbeitsbereich verfügt nicht über die Konfiguration „ExpressRoute-Monitor“.)    |     Dieser Fehler tritt auf, wenn der Benutzer Tests vom NPM-ExpressRoute-Monitor zum Verbindungsmonitor migriert, im ExpressRoute-Monitor aber keine Tests konfiguriert sind.  |
|„Workspace selected does not have 'Performance Monitor' config“ (Der ausgewählte Arbeitsbereich verfügt nicht über die Konfiguration „Leistungsmonitor“.)    |      Dieser Fehler tritt auf, wenn der Benutzer Tests vom NPM-Leistungsmonitor zum Verbindungsmonitor migriert, im Leistungsmonitor aber keine Tests konfiguriert sind. |
|Der ausgewählte Arbeitsbereich verfügt über keine gültigen Tests vom Typ „{0}“.    |      Dieser Fehler tritt auf, wenn der Benutzer Tests vom NPM zum Verbindungsmonitor migriert, aber in dem Feature, das der Benutzer für die Migration ausgewählt hat, keine gültigen Tests vorhanden sind.  |
|„Before you attempt migrate, please enable Network watcher extension in selection subscription and location of LA workspace selected“ (Bevor Sie die Migration starten, aktivieren Sie die Network Watcher-Erweiterung im Auswahlabonnement und am Standort des ausgewählten LA-Arbeitsbereichs.)      |      Dieser Fehler tritt auf, wenn der Benutzer Tests vom NPM zum Verbindungsmonitor migriert und die Network Watcher-Erweiterung im ausgewählten LA-Arbeitsbereich nicht aktiviert ist. Der Benutzer muss die NW-Erweiterung vor der Migration von Tests aktivieren. |
|Wenige Tests vom Typ „{1}“ enthalten Agents, die nicht mehr aktiv sind. Liste der inaktiven Agents: {0}. Diese Agents wurden möglicherweise in der Vergangenheit ausgeführt, sind aber jetzt heruntergefahren/werden nicht mehr ausgeführt. Aktivieren Sie Agents, und migrieren Sie zu Verbindungsmonitor. Klicken Sie auf „Weiter“, um die Tests zu migrieren, die keine inaktiven Agents enthalten.       |    Dieser Fehler tritt auf, wenn der Benutzer Tests vom NPM zum Verbindungsmonitor migriert und einige ausgewählte Tests inaktive Network Watcher-Agents oder NW-Agents enthalten, die nicht mehr aktiv sind, in der Vergangenheit aber aktiv waren und heruntergefahren wurden. Der Benutzer kann die Auswahl dieser Tests aufheben und die Tests, die keine inaktiven Agents enthalten, auswählen und migrieren.  |
|Ihre Tests vom Typ „{1}“ enthalten Agents, die nicht mehr aktiv sind. Liste der inaktiven Agents: {0}. Diese Agents wurden möglicherweise in der Vergangenheit ausgeführt, sind aber jetzt heruntergefahren/werden nicht mehr ausgeführt. Aktivieren Sie Agents, und migrieren Sie zum Verbindungsmonitor.     | Dieser Fehler tritt auf, wenn der Benutzer Tests vom NPM zum Verbindungsmonitor migriert und die ausgewählten Tests inaktive Network Watcher-Agents oder NW-Agents enthalten, die nicht mehr aktiv sind, in der Vergangenheit aber aktiv waren und heruntergefahren wurden. Der Benutzer muss die Agents aktivieren und anschließend mit der Migration dieser Tests zum Verbindungsmonitor fortfahren.    |
|„An error occurred while importing tests to connection monitor“ (Fehler beim Importieren von Tests in den Verbindungsmonitor.)     |    Dieser Fehler tritt auf, wenn der Benutzer versucht, Tests vom NPM zum Verbindungsmonitor zu migrieren, die Migration aber aufgrund von Fehlern nicht erfolgreich war. |



## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Verbindungsmonitor finden Sie in den folgenden Artikeln:
* [Migrieren von Verbindungsmonitor (klassisch) zu Verbindungsmonitor](./migrate-to-connection-monitor-from-connection-monitor-classic.md)
* [Erstellen eines Verbindungsmonitors mithilfe des Azure-Portals](./connection-monitor-create-using-portal.md)
