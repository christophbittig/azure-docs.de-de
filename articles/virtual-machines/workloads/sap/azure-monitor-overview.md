---
title: 'Azure Monitor für SAP-Lösungen: Übersicht und Architektur | Microsoft-Dokumentation'
description: Dieser Artikel enthält Antworten auf häufig gestellte Fragen zu Azure Monitor für SAP-Lösungen.
author: rdeltcheva
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.date: 07/06/2021
ms.author: radeltch
ms.openlocfilehash: 4b5bcb4aecab0247f47aede883e1bfe771262026
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122347129"
---
# <a name="azure-monitor-for-sap-solutions-preview"></a>Azure Monitor für SAP-Lösungen (Vorschauversion)

## <a name="overview"></a>Übersicht

Dieser Artikel enthält eine Übersicht über die Funktionsweise und die Funktionen von Azure Monitor für SAP-Lösungen.

Azure Monitor für SAP-Lösungen ist ein natives Azure-Überwachungsprodukt für Benutzer, die ihre SAP-Landschaften in Azure betreiben. Das Produkt funktioniert sowohl mit [SAP auf virtuellen Azure-Computern](./hana-get-started.md) als auch mit [SAP in Azure (große Instanzen)](./hana-overview-architecture.md).

Mit Azure Monitor für SAP-Lösungen können Sie Telemetriedaten aus der Azure-Infrastruktur sowie aus Datenbanken an einem zentralen Ort sammeln und Telemetriedaten zur schnelleren Problembehandlung visuell korrelieren.

Azure Monitor für SAP-Lösungen wird über den Azure Marketplace angeboten. Er bietet eine einfache, intuitive Benutzeroberfläche für das Setup und benötigt nur einige wenige Klicks, um die Ressource für Azure Monitor für SAP-Lösungen (als **SAP-Monitorressource** bekannt) bereitzustellen.

Sie können verschiedene Komponenten einer SAP-Landschaft wie etwa virtuelle Azure-Computer, Hochverfügbarkeitscluster, SAP HANA-Datenbanken oder SAP NetWeaver überwachen, indem Sie den entsprechenden **Anbieter** für die jeweilige Komponente hinzufügen.

Unterstützte Infrastruktur:

- Virtueller Azure-Computer
- Azure (große Instanz)

Unterstützte Datenbanken:
- SAP HANA-Datenbank
- Microsoft SQL Server

Azure Monitor für SAP-Lösungen nutzt die Leistungsfähigkeit bestehender [Azure Monitor](../../../azure-monitor/overview.md)-Funktionen wie Log Analytics und [Arbeitsmappen](../../../azure-monitor/visualize/workbooks-overview.md), um weitere Überwachungsfunktionen anzubieten. Sie können [benutzerdefinierte Visualisierungen](../../../azure-monitor/visualize/workbooks-overview.md#getting-started) durch Bearbeiten der Standardarbeitsmappen erstellen, die von Azure Monitor für SAP-Lösungen bereitgestellt werden. Schreiben Sie [benutzerdefinierte Abfragen](../../../azure-monitor/logs/log-analytics-tutorial.md), und erstellen Sie [benutzerdefinierte Warnungen](../../../azure-monitor/alerts/alerts-log.md) mithilfe eines Azure Log Analytics-Arbeitsbereichs. Nutzen Sie einen [flexiblen Aufbewahrungszeitraum](../../../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period), und verbinden Sie Überwachungsdaten mit Ihrem Ticketsystem.

## <a name="what-data-does-azure-monitor-for-sap-solutions-collect"></a>Welche Daten sammelt Azure Monitor für SAP-Lösungen?

Die Datenerfassung in Azure Monitor für SAP-Lösungen hängt von den konfigurierten Anbietern ab. Während der Public Preview werden die folgenden Daten gesammelt.

Hochverfügbarkeits-Pacemaker-Clustertelemetrie:
- Knoten-, Ressourcen- und SBD-Status (STONITH Block Device)
- Pacemaker-Standorteinschränkungen
- Quorumstimmen und Ringzustand
- [Andere](https://github.com/ClusterLabs/ha_cluster_exporter/blob/master/doc/metrics.md)

SAP HANA-Telemetrie:
- CPU-, Arbeitsspeicher-, Datenträger- und Netzwerkauslastung
- HANA-Systemreplikation (HSR)
- HANA-Sicherung
- Status des HANA-Hosts
- Indexserver- und Namenserverrollen

Microsoft SQL Server-Telemetrie:
- CPU-, Arbeitsspeicher- und Datenträgerauslastung
- Hostname, SQL-Instanzname und SAP-System-ID
- Batchanforderungen, Kompilierungen und Seitenlebenserwartung im Zeitverlauf
- Die zehn aufwendigsten SQL-Anweisungen im Zeitverlauf
- Die 12 größten Tabelle im SAP-System
- Im SQL Server-Fehlerprotokoll aufgezeichnete Probleme
- Blockierende Prozesse und SQL-Wartestatistiken im Zeitverlauf

Betriebssystemtelemetrie (Linux) 
- CPU-Auslastung, Forkanzahl, aktive und blockierte Prozesse 
- Arbeitsspeicherauslastung und Verteilung (belegt, zwischengespeichert und gepuffert) 
- Austauschnutzung, Auslagerung und Austauschrate 
- Dateisystemauslastung, Anzahl der pro Blockgerät gelesenen und geschriebenen Bytes 
- Wartezeit bei Lese-/Schreibvorgängen pro Blockgerät 
- Anzahl aktiver E/A-Vorgänge, gelesene/geschriebene Bytes für persistenten Speicher 
- Ein-/ausgehende Netzwerkpakete, ein-/ausgehende Netzwerkbytes 

SAP NetWeaver-Telemetrie:

- Verfügbarkeit von SAP-System- und -Anwendungsservern, einschließlich Instanzprozessverfügbarkeit von Dispatcher, ICM, Gateway, Nachrichtenserver, Enqueue-Server, IGS Watchdog
- Statistiken und Trends zur Arbeitsprozessnutzung
- Statistiken und Trends zu Warteschlangensperren
- Statistiken und Trends zur Warteschlangennutzung

## <a name="data-sharing-with-microsoft"></a>Datenfreigabe mit Microsoft

Azure Monitor für SAP-Lösungen sammelt Systemmetadaten, um einen besseren Support für SAP in Azure zu bieten. PII/EUII wird nicht gesammelt.

Sie können den Datenaustausch mit Microsoft aktivieren, wenn Sie die Ressource für Azure Monitor für SAP-Lösungen erstellen. Wählen Sie hierzu in der Dropdownliste die Option *Freigeben* aus. Es wird empfohlen, die Datenfreigabe zu aktivieren. Die Datenfreigabe liefert den Support- und Entwicklungsteams von Microsoft Informationen zu Ihrer Umgebung. So können wir besseren Support für Ihre unternehmenskritische SAP in Azure-Lösung bereitstellen.

## <a name="architecture-overview"></a>Übersicht über die Architektur

Das folgende Diagramm zeigt in einer allgemeinen Darstellung, wie Azure Monitor für SAP-Lösungen Telemetriedaten aus der SAP HANA-Datenbank sammelt. Die Architektur bleibt gleich – unabhängig davon, ob SAP HANA auf virtuellen Azure-Computern oder in Azure (große Instanzen) bereitgestellt wird.

![Azure Monitor für SAP-Lösungen: Architektur](https://user-images.githubusercontent.com/75772258/115046700-62ff3280-9ef5-11eb-8d0d-cfcda526aeeb.png)

Die Hauptkomponenten der Architektur sind:
- Azure-Portal: Ihr Ausgangspunkt. Sie können innerhalb des Azure-Portals zum Marketplace navigieren und Azure Monitor für SAP-Lösungen entdecken.
- Ressource für Azure Monitor für SAP-Lösungen: Ein Anlaufpunkt für Sie, an dem Sie die Überwachungstelemetrie anzeigen können.
- Verwaltete Ressourcengruppe – Automatisch im Rahmen der Ressourcenbereitstellung der Azure Monitor für SAP-Lösungen bereitgestellt. Die innerhalb der verwalteten Ressourcengruppe bereitgestellten Ressourcen helfen bei der Erfassung der Telemetrie. Die wichtigsten bereitgestellten Ressourcen und ihr Zweck:
   - Virtueller Azure-Computer: Wird auch als *virtueller Collector-Computer* bezeichnet und ist ein virtueller Computer vom Typ „Standard_B2ms“. Der Hauptzweck dieses virtuellen Computers besteht darin, die *Überwachungsnutzdaten* zu hosten. „Überwachungsnutzdaten“ bezieht sich auf die Logik zur Erfassung der Telemetriedaten der Quellsysteme sowie zur Übertragung der Daten an das Überwachungsframework. Im obigen Diagramm enthält die Überwachungsnutzlast die Logik für die Verbindung mit der SAP HANA-Datenbank über den SQL-Port.
   - [Azure Key Vault](../../../key-vault/general/basic-concepts.md): Diese Ressource wird bereitgestellt, um die Anmeldeinformationen der SAP HANA-Datenbank sicher zu verwahren und Informationen über [Anbieter](./azure-monitor-providers.md) zu speichern.
   - Log Analytics-Arbeitsbereich: Das Ziel, an dem die Telemetriedaten gespeichert werden.
      - Die Visualisierung baut auf der Telemetrie in Log Analytics auf und verwendet [Azure-Arbeitsmappen](../../../azure-monitor/visualize/workbooks-overview.md). Sie können die Visualisierung anpassen. Außerdem können Sie Ihre Arbeitsmappen oder eine spezifische Visualisierung innerhalb von Arbeitsmappen für die automatische Aktualisierung an das Azure-Dashboard anheften. Die maximale Aktualisierungshäufigkeit beträgt 30 Minuten.
      - Sie können Ihren vorhandenen Arbeitsbereich innerhalb des gleichen Abonnements als SAP-Monitorressource verwenden, indem Sie diese Option bei der Bereitstellung auswählen.
      - Sie können die Kusto-Abfragesprache (Kusto Query Language, KQL) verwenden, um [Abfragen](../../../azure-monitor/logs/log-query-overview.md) für die unformatierten Tabellen im Log Analytics-Arbeitsbereich auszuführen. Betrachten Sie die *benutzerdefinierten Protokolle*.

> [!Note]
> Sie sind für das Patchen und Warten des virtuellen Computers verantwortlich, der in der verwalteten Ressourcengruppe bereitgestellt wird.

> [!Tip]
> Sie können einen bereits vorhandenen Log Analytics-Arbeitsbereich für die Telemetrieerfassung verwenden, wenn dieser innerhalb des gleichen Azure-Abonnements bereitgestellt wird wie die Ressource für Azure Monitor für SAP-Lösungen.

### <a name="architecture-highlights"></a>Highlights der Architektur

Hier sind die wichtigsten Highlights der Architektur aufgeführt:
 - **Mehrere Instanzen:** Sie können einen Monitor für mehrere Instanzen eines bestimmten Komponententyps (z. B. SAP HANA-Datenbank, Hochverfügbarkeitscluster, Microsoft SQL Server, SAP NetWeaver) über mehrere SAP-SIDs innerhalb eines VNet hinweg mit einer einzelnen Ressource für Azure Monitor für SAP-Lösungen erstellen.
 - **Mehrere Anbieter** – Das obige Architekturdiagramm zeigt als Beispiel den Anbieter „SAP HANA“. Ebenso können Sie weitere Anbieter für entsprechende Komponenten (z. B. für SAP HANA-Datenbank, Hochverfügbarkeitscluster, Microsoft SQL Server, SAP NetWeaver) konfigurieren, um Daten von diesen Komponenten zu sammeln.
 - **Open Source** – Der Quellcode von Azure Monitor für SAP-Lösungen ist in [GitHub](https://github.com/Azure/AzureMonitorForSAPSolutions) verfügbar. Sie können auf den Anbietercode verweisen und mehr über das Produkt erfahren, Beiträge leisten oder Feedback übermitteln.
 - **Erweiterbares Abfrageframework** – SQL-Abfragen zur Erfassung von Telemetriedaten werden in [JSON](https://github.com/Azure/AzureMonitorForSAPSolutions/blob/master/sapmon/content/SapHana.json) geschrieben. Es können leicht weitere SQL-Abfragen zur Erfassung weiterer Telemetriedaten hinzugefügt werden. Wenn in Azure Monitor für SAP-Lösungen bestimmte Telemetriedaten hinzugefügt werden sollen, können Sie über den Link am Ende dieses Artikels entsprechendes Feedback hinterlassen. Sie können sich auch an Ihr Kontoteam wenden, um Feedback zu hinterlassen.

## <a name="pricing"></a>Preise
Azure Monitor für SAP-Lösungen ist ein kostenloses Produkt (keine Lizenzgebühr). Sie sind dafür verantwortlich, die Kosten für die zugrunde liegenden Komponenten in der verwalteten Ressourcengruppe sowie für die Verbrauchskosten im Zusammenhang mit der Datennutzung und -aufbewahrung zu begleichen. Weitere Informationen finden Sie in den Dokumenten zu Azure-Standardpreisen: [Virtuelle Linux-Computer – Preise](https://azure.microsoft.com/pricing/details/virtual-machines/linux/), [Key Vault – Preise](https://azure.microsoft.com/pricing/details/key-vault/), [Queue Storage – Preise](https://azure.microsoft.com/pricing/details/storage/queues/), [Azure Monitor – Preise](https://azure.microsoft.com/pricing/details/monitor/)

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Anbietern von Azure Monitor für SAP-Lösungen finden Sie hier:

> [!div class="nextstepaction"]
> [Anbieter von Azure Monitor für SAP-Lösungen](azure-monitor-providers.md)
