---
title: Überwachen von SAP in Azure (Vorschau) | Microsoft-Dokumentation
description: Hier erfahren Sie mehr über die Überwachung von SAP in Azure.
author: mamccrea
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.custom: subject-monitoring
ms.date: 10/13/2021
ms.author: mamccrea
ms.openlocfilehash: cf68247c9d259aff6ffe6d2e7cd9be76eea6f237
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132063268"
---
# <a name="monitor-sap-on-azure-preview"></a>Überwachen von SAP in Azure (Vorschau)

Wenn Sie über unternehmenskritische Anwendungen und Geschäftsprozesse verfügen, die auf Azure-Ressourcen beruhen, sollten Sie Verfügbarkeit, Leistung und Betrieb dieser Ressourcen überwachen. 

In diesem Artikel wird beschrieben, wie SAP in Azure mithilfe von Azure Monitor für SAP-Lösungen überwacht wird. Azure Monitor für SAP-Lösungen verwendet bestimmte Teile der [Azure Monitor](../../../azure-monitor/overview.md)-Infrastruktur.

## <a name="overview"></a>Übersicht

Azure Monitor für SAP-Lösungen ist ein natives Azure-Überwachungsprodukt für Benutzer, die ihre SAP-Landschaften in Azure betreiben. Es funktioniert sowohl mit [SAP auf virtuellen Azure-Computern](./hana-get-started.md) als auch mit [SAP in Azure (große Instanzen)](./hana-overview-architecture.md).

Mit Azure Monitor für SAP-Lösungen können Sie Telemetriedaten aus der Azure-Infrastruktur sowie aus Datenbanken an einem zentralen Ort sammeln und die Daten zur schnelleren Problembehandlung visuell korrelieren.

Sie können verschiedene Komponenten einer SAP-Landschaft wie beispielsweise Azure-VMs, Hochverfügbarkeitscluster, SAP HANA-Datenbanken oder SAP NetWeaver überwachen, indem sie den entsprechenden **Anbieter** für die jeweilige Komponente hinzufügen. Weitere Informationen finden Sie unter [Bereitstellen von Azure Monitor für SAP-Lösungen über das Azure-Portal](azure-monitor-sap-quickstart.md).

Unterstützte Infrastruktur:

- Virtueller Azure-Computer
- Azure (große Instanz)

Unterstützte Datenbanken:
- SAP HANA-Datenbank
- Microsoft SQL Server

Azure Monitor für SAP-Lösungen verwendet die [Azure Monitor](../../../azure-monitor/overview.md)-Funktionen von [Log Analytics](../../../azure-monitor/logs/log-analytics-overview.md) und [Arbeitsmappen](../../../azure-monitor/visualize/workbooks-overview.md). Sie können damit folgende Aufgaben durchführen:

- Erstellen [benutzerdefinierter Visualisierungen](../../../azure-monitor/visualize/workbooks-overview.md#getting-started) durch Bearbeiten der Standardarbeitsmappen, die von Azure Monitor für SAP-Lösungen bereitgestellt werden 
- Schreiben [benutzerdefinierter Abfragen](../../../azure-monitor/logs/log-analytics-tutorial.md)
- Erstellen [benutzerdefinierter Warnungen](../../../azure-monitor/alerts/alerts-log.md) mithilfe eines Azure Log Analytics-Arbeitsbereichs 
- Nutzen des [flexiblen Aufbewahrungszeitraums](../../../azure-monitor/logs/manage-cost-storage.md#change-the-data-retention-period) für Azure Monitor-Protokolle/Log Analytics 
- Verbinden von Überwachungsdaten mit Ihrem Ticketsystem

## <a name="what-data-does-azure-monitor-for-sap-solutions-collect"></a>Welche Daten sammelt Azure Monitor für SAP-Lösungen?

Azure Monitor für SAP-Lösungen erfasst Azure Monitor-Metriken oder -Ressourcenprotokolldaten nicht wie viele andere Azure-Ressourcen. Stattdessen werden benutzerdefinierte Protokolle direkt an das Azure Monitor-Protokollsystem gesendet, wo Sie dann die integrierten Features von Log Analytics verwenden können.

Die Datenerfassung in Azure Monitor für SAP-Lösungen hängt von den konfigurierten Anbietern ab. Während der öffentlichen Vorschauphase werden die folgenden Daten gesammelt.

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
- Datenbankzuwachs
- Wichtigste Tabellen
- Dateisystemauslastung

Microsoft SQL Server-Telemetrie:
- CPU-, Arbeitsspeicher- und Datenträgerauslastung
- Hostname, SQL-Instanzname und SAP-System-ID
- Batchanforderungen, Kompilierungen und Seitenlebenserwartung im Zeitverlauf
- Die zehn aufwendigsten SQL-Anweisungen im Zeitverlauf
- Die 12 größten Tabelle im SAP-System
- Im SQL Server-Fehlerprotokoll aufgezeichnete Probleme
- Blockierende Prozesse und SQL-Wartestatistiken im Zeitverlauf

Betriebssystemtelemetrie (Linux): 
- CPU-Auslastung, Forkanzahl, aktive und blockierte Prozesse 
- Arbeitsspeicherauslastung und Verteilung auf belegt, zwischengespeichert und gepuffert 
- Austauschnutzung, Auslagerung und Austauschrate 
- Dateisystemauslastung, Anzahl der pro Blockgerät gelesenen und geschriebenen Bytes 
- Latenz von Lese-/Schreibvorgängen pro Blockgerät 
- Anzahl aktiver E/A-Vorgänge, gelesene/geschriebene Bytes für persistenten Speicher 
- Eingehende/ausgehende Netzwerkpakete, eingehende/ausgehende Netzwerkbytes 

SAP NetWeaver-Telemetrie:

- Verfügbarkeit von SAP-System- und -Anwendungsservern, einschließlich Instanzprozessverfügbarkeit von Dispatcher, ICM, Gateway, Nachrichtenserver, Enqueue-Server, IGS Watchdog
- Statistiken und Trends zur Arbeitsprozessnutzung
- Statistiken und Trends zu Warteschlangensperren
- Statistiken und Trends zur Warteschlangennutzung

## <a name="data-sharing-with-microsoft"></a>Datenfreigabe mit Microsoft

Azure Monitor für SAP-Lösungen sammelt Systemmetadaten, um einen verbesserten Support für SAP in Azure zu bieten. PII/EUII wird nicht gesammelt.

Sie können den Datenaustausch mit Microsoft aktivieren, wenn Sie die Ressource für Azure Monitor für SAP-Lösungen erstellen. Wählen Sie hierzu in der Dropdownliste die Option *Freigeben* aus. Es wird empfohlen, die Datenfreigabe zu aktivieren. Die Datenfreigabe bietet den Microsoft-Teams für Support und Entwicklung Informationen zu Ihrer Umgebung. So können wir besseren Support für Ihre unternehmenskritische SAP in Azure-Lösung bereitstellen.

## <a name="architecture-overview"></a>Übersicht über die Architektur

Das folgende Diagramm zeigt in einer allgemeinen Darstellung, wie Azure Monitor für SAP-Lösungen Telemetriedaten aus der SAP HANA-Datenbank sammelt. Die Architektur ist dieselbe unabhängig davon, ob SAP HANA auf Azure-VMs oder in Azure (große Instanzen) bereitgestellt wird.

![Azure Monitor für SAP-Lösungen: Architektur](https://user-images.githubusercontent.com/75772258/115046700-62ff3280-9ef5-11eb-8d0d-cfcda526aeeb.png)

Die Hauptkomponenten der Architektur sind:
- **Azure-Portal**: Ihr Ausgangspunkt. Sie können innerhalb des Azure-Portals zum Marketplace navigieren und Azure Monitor für SAP-Lösungen entdecken.
- **Ressource für Azure Monitor für SAP-Lösungen**: Ein Anlaufpunkt für Sie, an dem Sie die Überwachungstelemetrie anzeigen können.
- **Verwaltete Ressourcengruppe**: Wird automatisch im Rahmen der Ressourcenbereitstellung der Azure Monitor für SAP-Lösungen bereitgestellt. Die innerhalb der verwalteten Ressourcengruppe bereitgestellten Ressourcen unterstützen Sie bei der Erfassung von Telemetriedaten. Die wichtigsten bereitgestellten Ressourcen und ihr Zweck:
   - **Azure-VM**: Wird auch als *Collector-VM* bezeichnet, eine Standard_B2ms-VM. Der Hauptzweck dieser VM besteht darin, die *Überwachungsnutzdaten* zu hosten. „Überwachungsnutzdaten“ bezieht sich auf die Logik zur Erfassung der Telemetriedaten von den Quellsystemen und zur Übertragung der Daten an das Überwachungsframework. Im vorherigen Diagramm enthält die Überwachungsnutzlast die Logik für die Verbindung mit der SAP HANA-Datenbank über den SQL-Port.
   - **[Azure Key Vault](../../../key-vault/general/basic-concepts.md)** : Diese Ressource wird bereitgestellt, um die Anmeldeinformationen der SAP HANA-Datenbank sicher zu verwahren und Informationen zu [Anbietern](./azure-monitor-providers.md) zu speichern.
   - **Log Analytics-Arbeitsbereich**: Das Ziel, in dem die Telemetriedaten gespeichert werden
      - Die Visualisierung baut auf der Telemetrie in Log Analytics auf und verwendet [Azure-Arbeitsmappen](../../../azure-monitor/visualize/workbooks-overview.md). Sie können die Visualisierung anpassen. Außerdem können Sie Ihre Arbeitsmappen oder eine spezifische Visualisierung innerhalb von Arbeitsmappen für die automatische Aktualisierung an das Azure-Dashboard anheften. Die maximale Aktualisierungshäufigkeit beträgt alle 30 Minuten.
      - Sie können Ihren vorhandenen Arbeitsbereich innerhalb desselben Abonnements als SAP-Monitorressource verwenden, indem Sie diese Option bei der Bereitstellung auswählen.
      - Sie können die Kusto-Abfragesprache (KQL) verwenden, um [Abfragen](../../../azure-monitor/logs/log-query-overview.md) für die unformatierten Tabellen im Log Analytics-Arbeitsbereich auszuführen. Betrachten Sie die *benutzerdefinierten Protokolle*.

> [!Note]
> Sie sind für das Patchen und Warten der VM verantwortlich, die in der verwalteten Ressourcengruppe bereitgestellt wird.

> [!Tip]
> Sie können einen bestehenden Log Analytics-Arbeitsbereich für die Telemetrieerfassung verwenden, wenn dieser innerhalb desselben Azure-Abonnements wie die Ressource für Azure Monitor für SAP-Lösungen bereitgestellt wird.

### <a name="architecture-highlights"></a>Highlights der Architektur

Hier sind die wichtigsten Highlights der Architektur aufgeführt:
 - **Mehrere Instanzen**: Sie können mehrere Instanzen eines bestimmten Komponententyps (z. B. SAP HANA-Datenbank, Hochverfügbarkeitscluster, Microsoft SQL Server, SAP NetWeaver) über mehrere SAP-SIDs innerhalb eines VNet mit einer einzelnen Ressource für Azure Monitor für SAP-Lösungen überwachen.
 - **Mehrere Anbieter**: Das obige Architekturdiagramm zeigt als Beispiel den Anbieter „SAP HANA“. Ebenso können Sie weitere Anbieter für entsprechende Komponenten konfigurieren, um Daten von diesen Komponenten zu erfassen. Beispiel: HANA-Datenbank, Hochverfügbarkeitscluster, Microsoft SQL Server und SAP NetWeaver. 
 - **Open Source** – Der Quellcode von Azure Monitor für SAP-Lösungen ist in [GitHub](https://github.com/Azure/AzureMonitorForSAPSolutions) verfügbar. Sie können auf den Anbietercode verweisen und mehr über das Produkt erfahren, Beiträge leisten oder Feedback übermitteln.
 - **Erweiterbares Abfrageframework** – SQL-Abfragen zur Erfassung von Telemetriedaten werden in [JSON](https://github.com/Azure/AzureMonitorForSAPSolutions/blob/master/sapmon/content/SapHana.json) geschrieben. Es können leicht weitere SQL-Abfragen zur Erfassung weiterer Telemetriedaten hinzugefügt werden. Sie können anfordern, dass in Azure Monitor für SAP-Lösungen bestimmte Telemetriedaten hinzugefügt werden. Hinterlassen Sie hierzu Ihr Feedback über den Link am Ende dieses Artikels. Sie können auch Feedback bereitstellen, indem Sie sich an Ihr Kontoteam wenden.

## <a name="analyzing-metrics"></a>Analysieren von Metriken

Azure Monitor für SAP-Lösungen unterstützt keine Metriken. 

## <a name="analyzing-logs"></a>Analysieren von Protokollen

Azure Monitor für SAP-Lösungen unterstützt keine Ressourcenprotokolle oder Aktivitätsprotokolle. Eine Liste der von Azure Monitor-Protokollen verwendeten Tabellen, die von Log Analytics abgefragt werden können, finden Sie in der [Datenreferenz zur Überwachung von SAP in Azure](monitor-sap-on-azure-reference.md#azure-monitor-logs-tables). 

### <a name="sample-kusto-queries"></a>Kusto-Beispielabfragen

> [!IMPORTANT]
> Wenn Sie im Menü von Azure Monitor für SAP-Lösungen die Option **Protokolle** auswählen, wird Log Analytics geöffnet, und der Abfragebereich wird auf die aktuelle Instanz von Azure Monitor für SAP-Lösungen festgelegt. Dies bedeutet, dass Protokollabfragen nur Daten aus dieser Ressource umfassen. Wenn Sie eine Abfrage ausführen möchten, die Daten aus anderen Konten oder Daten aus anderen Azure-Diensten enthält, wählen Sie im Menü **Azure Monitor** die Option **Protokolle** aus. Ausführliche Informationen finden Sie unter [Protokollabfragebereich und Zeitbereich in Azure Monitor Log Analytics](../../../azure-monitor/logs/scope.md).

Sie können Kusto-Abfragen für die Überwachung Ihrer SAP in Azure-Ressourcen verwenden. Hier sehen Sie eine Beispielabfrage, die Ihnen Daten aus einem benutzerdefinierten Protokoll für einen angegebenen Zeitbereich liefert. Sie geben den Zeitbereich und die Anzahl der Zeilen an. In diesem Beispiel erhalten Sie fünf Datenzeilen für den ausgewählten Zeitbereich. 

```Kusto
custom log name 
| take 5

```

## <a name="alerts"></a>Alerts

Azure Monitor-Warnungen informieren Sie proaktiv, wenn wichtige Bedingungen in Ihren Überwachungsdaten gefunden werden. Sie ermöglichen Ihnen, Probleme in Ihrem System zu identifizieren und zu beheben, bevor Ihre Kunden sie bemerken.

Sie können Warnungen in Azure Monitor für SAP-Lösungen über das Azure-Portal konfigurieren. Weitere Informationen finden Sie unter [Konfigurieren von Warnungen in Azure Monitor für SAP-Lösungen über das Azure-Portal](azure-monitor-alerts-portal.md).

## <a name="create-azure-monitor-for-sap-solutions-resources"></a>Erstellen von Ressourcen für Azure Monitor für SAP-Lösungen

Ihnen stehen mehrere Optionen zum Bereitstellen von Azure Monitor für SAP-Lösungen und zum Konfigurieren von Anbietern zur Verfügung: 
- Sie können Azure Monitor für SAP-Lösungen direkt über das Azure-Portal bereitstellen. Weitere Informationen finden Sie unter [Bereitstellen von Azure Monitor für SAP-Lösungen über das Azure-Portal](azure-monitor-sap-quickstart.md).
- Verwenden Sie Azure PowerShell. Weitere Informationen finden Sie unter [Bereitstellen von Azure Monitor für SAP-Lösungen über Azure PowerShell](azure-monitor-sap-quickstart-powershell.md).
- Verwenden Sie die CLI-Erweiterung. Weitere Informationen finden Sie im [SAP HANA-Befehlsmodul](https://github.com/Azure/azure-hanaonazure-cli-extension#sapmonitor).

## <a name="pricing"></a>Preise
Azure Monitor für SAP-Lösungen ist ein kostenloses Produkt (keine Lizenzgebühr). Sie sind dafür verantwortlich, die Kosten für die zugrunde liegenden Komponenten in der verwalteten Ressourcengruppe zu begleichen. Sie sind auch für die Verbrauchskosten verantwortlich, die mit der Datennutzung und -aufbewahrung verbunden sind. Weitere Informationen finden Sie in den Standarddokumenten zu Azure-Preisen:
- [Preise für Azure-VMs](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)
- [Preise für Azure Key Vault](https://azure.microsoft.com/pricing/details/key-vault/)
- [Preise für Azure-Speicherkonten](https://azure.microsoft.com/pricing/details/storage/queues/)
- [Preise für Azure Log Analytics und Warnungen](https://azure.microsoft.com/pricing/details/monitor/)

## <a name="next-steps"></a>Nächste Schritte

- Eine Liste benutzerdefinierter Protokolle, die für Azure Monitor für SAP-Lösungen relevant sind, und Informationen zu entsprechenden Datentypen finden Sie in der [Datenreferenz zur Überwachung von SAP in Azure](monitor-sap-on-azure-reference.md).
- Informationen zu den Anbietern, die für Azure Monitor für SAP-Lösungen verfügbar sind, finden Sie unter [Anbieter von Azure Monitor für SAP-Lösungen](azure-monitor-providers.md).
