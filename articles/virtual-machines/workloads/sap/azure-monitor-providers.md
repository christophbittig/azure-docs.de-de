---
title: Anbieter von Azure Monitor für SAP-Lösungen | Microsoft-Dokumentation
description: Dieser Artikel bietet Antworten auf häufig gestellte Fragen zu Azure Monitor für SAP-Lösungsanbieter.
author: rdeltcheva
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.date: 07/06/2021
ms.author: radeltch
ms.openlocfilehash: 5061ee2131ee708c38a68dca02c18a21085b5430
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128616857"
---
# <a name="azure-monitor-for-sap-solutions-providers-preview"></a>Anbieter von Azure Monitor für SAP-Lösungen (Vorschau)

## <a name="overview"></a>Übersicht

In diesem Artikel werden die verschiedenen Anbieter beschrieben, die derzeit für Azure Monitor für SAP-Lösungen verfügbar sind.

Im Kontext von Azure Monitor für SAP-Lösungen bezieht sich ein *Anbietertyp* auf einen bestimmten *Anbieter*. Beispiel: *SAP HANA*, das für eine bestimmte Komponente innerhalb der SAP-Landschaft konfiguriert ist, wie die SAP HANA-Datenbank. Ein Anbieter enthält die Verbindungsinformationen für die entsprechende Komponente und hilft, Telemetriedaten von dieser Komponente zu sammeln. Eine Ressource von Azure Monitor für SAP-Lösungen (auch als SAP-Monitorressource bezeichnet) kann mit mehreren Anbietern desselben Anbietertyps oder mehreren Anbietern verschiedener Anbietertypen konfiguriert werden.
   
Sie können verschiedene benutzerdefinierte Anbietertypen konfigurieren, um die Datensammlung aus der entsprechenden Komponente in ihrer SAP-Landschaft zu ermöglichen. Beispielsweise können Sie einen Anbieter für den Anbietertyp „SAP HANA“, einen anderen Anbieter für den Anbietertyp „Hochverfügbarkeitscluster“ usw. konfigurieren.  

Sie können auch mehrere Anbieter eines bestimmten Anbietertyps konfigurieren, um dieselbe SAP-Monitorressource und die zugehörige verwaltete Gruppe wiederzuverwenden. Weitere Informationen zu verwalteten Ressourcengruppen finden Sie unter [Verwalten von Azure Resource Manager-Ressourcengruppen über das Azure-Portal](../../../azure-resource-manager/management/manage-resource-groups-portal.md).

Für die öffentliche Vorschau werden die folgenden Anbietertypen unterstützt:   
- SAP NetWeaver
- SAP HANA
- Microsoft SQL Server
- Hochverfügbarkeitscluster
- Betriebssystem

![Anbieter von Azure Monitor für SAP-Lösungen](https://user-images.githubusercontent.com/75772258/115047655-5a5b2c00-9ef6-11eb-9e0c-073e5e1fcd0e.png)

Wir empfehlen Ihnen, bei der Bereitstellung der SAP-Monitorressource mindestens einen Anbieter aus den verfügbaren Anbietertypen zu konfigurieren. Durch die Konfiguration eines Anbieters starten Sie die Datenerfassung aus der entsprechenden Komponente, für die der Anbieter konfiguriert ist.   

Wenn Sie zum Zeitpunkt der Bereitstellung der SAP-Monitorressource keine Anbieter konfigurieren, werden keine Telemetriedaten erfasst, obwohl die SAP-Monitorressource erfolgreich bereitgestellt wird. Sie können Anbieter nach der Bereitstellung über die SAP-Monitorressource innerhalb des Azure-Portals hinzufügen. Sie können jederzeit Anbieter in der SAP-Monitorressource hinzufügen oder löschen.

## <a name="provider-type-sap-netweaver"></a>Anbietertyp: SAP NetWeaver

Sie können einen oder mehrere Anbieter des Anbietertyps „SAP NetWeaver“ konfigurieren, um die Datenerfassung aus der SAP NetWeaver-Ebene zu ermöglichen. Der AMS NetWeaver-Anbieter nutzt die vorhandene [SAPControl-Webdienstschnittstelle](https://www.sap.com/documents/2016/09/0a40e60d-8b7c-0010-82c7-eda71af511fa.html), um die entsprechenden Telemetriedaten abzurufen.

Für das aktuelle Release sind die folgenden SOAP-Webmethoden die Standardmethoden, die von AMS standardmäßig aufgerufen werden.

![image1](https://user-images.githubusercontent.com/75772258/114600036-820d8280-9cb1-11eb-9f25-d886ab1d5414.png)

In der öffentlichen Vorschau können Sie die folgenden Daten mit dem SAP NetWeaver-Anbieter erwarten: 
- Verfügbarkeit von Systemen und Instanzen
- Arbeitsprozessverwendung
- Warteschlangenverwendung
- Statistik zu in die Warteschlange eingereihten Sperren

![image](https://user-images.githubusercontent.com/75772258/114581825-a9f2eb00-9c9d-11eb-8e6f-79cee7c5093f.png)

## <a name="provider-type-sap-hana"></a>Anbietertyp: SAP HANA

Sie können einen oder mehrere Anbieter des Anbietertyps *SAP HANA* konfigurieren, um die Datenerfassung aus der SAP HANA-Datenbank zu ermöglichen. Der SAP HANA-Anbieter stellt über den SQL-Port eine Verbindung mit der SAP HANA-Datenbank her, pullt Telemetriedaten aus der Datenbank und pusht sie in den Log Analytics-Arbeitsbereich Ihres Abonnements. Der SAP HANA-Anbieter führt die Erfassung aus der SAP HANA-Datenbank jede Minute durch.  

In der öffentlichen Vorschau können Sie die folgenden Daten mit dem SAP HANA-Anbieter erwarten:
- Zugrunde liegende Infrastrukturverwendung
- SAP HANA-Hoststatus
- SAP HANA System Replication (SAP HANA-Systemreplikation)
- Telemetriedaten für die SAP HANA-Sicherung 

Zum Konfigurieren des SAP HANA-Anbieters ist Folgendes erforderlich:
- Host-IP-Adresse 
- HANA SQL-Portnummer
- Benutzername und Kennwort für SYSTEMDB

Es wird empfohlen, dass Sie den SAP HANA-Anbieter für SYSTEMDB konfigurieren. Weitere Anbieter können jedoch auch für andere Datenbankmandanten konfiguriert werden.

![Anbieter von Azure Monitor für SAP-Lösungen – SAP HANA](./media/azure-monitor-sap/azure-monitor-providers-hana.png)

## <a name="provider-type-microsoft-sql-server"></a>Anbietertyp: Microsoft SQL Server

Sie können einen oder mehrere Anbieter des Anbietertyps *Microsoft SQL Server* konfigurieren, um die Datenerfassung von [SQL Server auf virtuellen Computern](https://azure.microsoft.com/services/virtual-machines/sql-server/) zu ermöglichen. Der SQL Server-Anbieter stellt über den SQL-Port eine Verbindung mit Microsoft SQL Server her. Anschließend werden Telemetriedaten aus der Datenbank gepullt und in den Log Analytics-Arbeitsbereich in Ihrem Abonnement gepusht. Konfigurieren Sie SQL Server für die SQL-Authentifizierung und für die Anmeldung mit dem SQL Server-Benutzernamen und -Kennwort. Legen Sie die SAP-Datenbank als Standarddatenbank für den Anbieter fest. Der SQL Server-Anbieter erfasst Daten zwischen alle 60 Sekunden bis zu jeder Stunde von SQL Server.  

In der öffentlichen Vorschau können Sie die folgenden Daten mit dem SQL Server-Anbieter erwarten:
- Zugrunde liegende Infrastrukturverwendung
- Wichtige SQL-Anweisungen
- Größte Tabelle
- Im SQL Server-Fehlerprotokoll aufgezeichnete Probleme
- Blockierende Prozesse und andere  

Zum Konfigurieren des Microsoft SQL Server-Anbieters ist Folgendes erforderlich:
- SAP-System-ID
- Host-IP-Adresse
- SQL Server-Portnummer
- SQL Server-Benutzername und -Kennwort

![Anbieter von Azure Monitor für SAP-Lösungen – SQL](./media/azure-monitor-sap/azure-monitor-providers-sql.png)

## <a name="provider-type-high-availability-cluster"></a>Anbietertyp: Hochverfügbarkeitscluster
Sie können einen oder mehrere Anbieter des Anbietertyps *Hochverfügbarkeitscluster* konfigurieren, um die Datenerfassung aus Pacemaker-Clustern innerhalb der SAP-Landschaft zu ermöglichen. Der Anbieter von Hochverfügbarkeitsclustern verbindet sich mit Pacemaker mithilfe des [ha_cluster_exporter](https://github.com/ClusterLabs/ha_cluster_exporter) für **SUSE**-basierte Cluster und mithilfe von [Performance Co-Pilot](https://access.redhat.com/articles/6139852) für **RHEL**-basierte Cluster. Anschließend pullt AMS Telemetriedaten aus der Datenbank und pusht sie in den Log Analytics-Arbeitsbereich in Ihrem Abonnement. Der Anbietertyp „Hochverfügbarkeitscluster“ erfasst alle 60 Sekunden Daten von Pacemaker.  

In der öffentlichen Vorschau können Sie die folgenden Daten mit Anbietern von Hochverfügbarkeitsclustern erwarten:   
 - Clusterstatus dargestellt als Rollup des Knoten- und Ressourcenstatus 
 - Standorteinschränkungen
 - Trends
 - [Sonstige](https://github.com/ClusterLabs/ha_cluster_exporter/blob/master/doc/metrics.md) 

![Anbieter von Azure Monitor für SAP-Lösungen – Hochverfügbarkeitscluster](./media/azure-monitor-sap/azure-monitor-providers-pacemaker-cluster.png)

Die Konfiguration eines Anbieters von Hochverfügbarkeitsclustern erfolgt in zwei Hauptschritten:

1. Installieren Sie [ha_cluster_exporter](https://github.com/ClusterLabs/ha_cluster_exporter) in *jedem* Knoten innerhalb des Pacemaker-Clusters.

   Für das Installieren von „ha_cluster_exporter“ gibt es zwei Möglichkeiten:
   
   - Verwenden Sie benutzerdefinierte Azure Automation-Skripts, um Hochverfügbarkeitscluster bereitzustellen. Die Skripts installieren [ha_cluster_exporter](https://github.com/ClusterLabs/ha_cluster_exporter) in jedem Clusterknoten.  
   - Führen Sie eine [manuelle Installation](https://github.com/ClusterLabs/ha_cluster_exporter#manual-clone--build) durch. 

2. Konfigurieren Sie den Anbieter von Hochverfügbarkeitsclustern in *jedem* Knoten innerhalb des Pacemaker-Clusters.

   Zum Konfigurieren des Anbieters für Hochverfügbarkeitscluster sind die folgenden Informationen erforderlich:
   
   - **Name**. Ein Name für diesen Anbieter. Dieser sollte für die Instanz der Azure Monitor für SAP-Lösung eindeutig sein.
   - **Prometheus-Endpunkt:** `http://<servername or ip address>:9664/metrics`.
   - **SID:** Verwenden Sie für SAP-Systeme die SAP-SID. Verwenden Sie für andere Systeme (z. B. NFS-Cluster) einen aus drei Zeichen bestehenden Namen für den Cluster. Die SID muss sich von anderen überwachten Clustern unterscheiden.   
   - **Clustername:** Der Clustername, der beim Erstellen des Clusters verwendet wird. Sie finden den Clusternamen in der Clustereigenschaft `cluster-name`.
   - **Hostname**. Der Linux-Hostname des virtuellen Computers (VM).  

## <a name="provider-type-os-linux"></a>Anbietertyp: Betriebssystem (Linux)
Sie können einen oder mehrere Anbieter für den Bereitstellungstyp (Linux) konfigurieren, damit die Datensammlung von BareMetal- oder VM-Knoten aktiviert wird. Der Betriebssystemanbieter (Linux) stellt mithilfe des [Node_Exporter](https://github.com/prometheus/node_exporter) -Endpunkts eine Verbindung mit BareMetal- oder VM-Knoten her. Anschließend pullt er Telemetriedaten aus den Knoten und pusht sie in den Log Analytics-Arbeitsbereich in Ihrem Abonnement. Der Betriebssystemanbieter (Linux) sammelt alle 60 Sekunden Daten für die meisten Metriken von den Knoten. 

In der öffentlichen Vorschau können Sie die folgenden Daten mit dem Betriebssystemanbieter (Linux) erwarten: 
   - CPU-Auslastung, CPU-Auslastung nach Prozess 
   - Datenträgerauslastung, E/A-Lese- und -Schreibvorgänge 
   - Arbeitsspeicherverteilung, Arbeitsspeicherauslastung, Auslastung des Auslagerungsspeichers 
   - Netzwerkauslastung, Details zum im Netzwerk ein- und ausgehenden Datenverkehr 

Die Konfiguration eines Betriebssystemanbieters (Linux) erfolgt in zwei Hauptschritten:
1. Installieren Sie [Node_Exporter](https://github.com/prometheus/node_exporter) auf allen BareMetal- oder VM-Knoten.
   Für das Installieren von [Node_Exporter](https://github.com/prometheus/node_exporter) gibt es zwei Möglichkeiten: 
      - Für die automatisierte Installation mit Ansible verwenden Sie [Node_Exporter](https://github.com/prometheus/node_exporter) auf jedem BareMetal- oder VM-Knoten, um den Betriebssystemanbieter (Linux) zu installieren.  
      - Führen Sie eine  [manuelle Installation](https://prometheus.io/docs/guides/node-exporter/) aus.

2. Konfigurieren Sie einen Betriebssystemanbieter (Linux) für jede BareMetal- oder VM-Knoteninstanz in Ihrer Umgebung. 
   Zum Konfigurieren des Betriebssystemanbieters (Linux) sind die folgenden Informationen erforderlich: 
      - Name. Ein Name für diesen Anbieter. Dieser sollte für die Instanz der Azure Monitor für SAP-Lösungen eindeutig sein. 
      - Endpunkt von Node Exporter. Normalerweise `http://<servername or ip address>:9100/metrics`. 

> [!NOTE]
> 9100 ist ein Port, der für den Endpunkt von Node_Exporter verfügbar gemacht wird.

> [!Warning]
> Stellen Sie sicher, dass Node Exporter nach dem Neustart des Knotens weiter ausgeführt wird. 

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie Azure Monitor für SAP-Lösungen über das Azure-Portal bereitstellen.

> [!div class="nextstepaction"]
> [Bereitstellen von Azure Monitor für SAP-Lösungen über das Azure-Portal](./azure-monitor-sap-quickstart.md)
