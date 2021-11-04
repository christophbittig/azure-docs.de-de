---
title: Häufig gestellte Fragen zu Azure Managed Instance for Apache Cassandra im Azure-Portal
description: Enthält häufig gestellte Fragen zu Azure Managed Instance for Apache Cassandra. Dieser Artikel enthält Antworten auf Fragen zur Verwendung von verwalteten Instanzen sowie zu Vorteilen, Durchsatzlimits, unterstützten Regionen und anderen Konfigurationsdetails.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 1b5ed1cd49ac4117dac6ff0a6e3bdb916299ec44
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131457409"
---
# <a name="frequently-asked-questions-about-azure-managed-instance-for-apache-cassandra"></a>Häufig gestellte Fragen zu Azure Managed Instance for Apache Cassandra

Dieser Artikel enthält Antworten auf häufig gestellte Fragen zu Azure Managed Instance for Apache Cassandra. Es wird beschrieben, wann Sie verwaltete Instanzen nutzen sollten und welche Vorteile, Durchsatzlimits, unterstützten Regionen und anderen Konfigurationsdetails dabei zu beachten sind.

## <a name="general-faq"></a>Allgemeine häufig gestellte Fragen

### <a name="what-are-the-benefits-azure-managed-instance-for-apache-cassandra"></a>Was sind die Vorteile von Azure Managed Instance for Apache Cassandra?

Die Apache Cassandra-Datenbank ist die richtige Wahl, wenn Sie die Skalierbarkeit und Hochverfügbarkeit sicherstellen möchten, ohne dass sich Leistungsbeeinträchtigungen ergeben. Diese Plattform ist hervorragend für unternehmenskritische Daten geeignet, weil sie über lineare Skalierbarkeit und eine bewährte Fehlertoleranz für Standardhardware bzw. die Cloudinfrastruktur verfügt. Azure Managed Instance for Apache Cassandra ist ein Dienst zum Verwalten der Instanzen von Apache Cassandra-Open-Source-Rechenzentren, die in Azure bereitgestellt werden.

Er kann entweder vollständig in der Cloud oder als Teil einer Hybrid Cloud und eines lokalen Clusters genutzt werden. Dieser Dienst ist eine hervorragende Wahl, wenn Sie die präzisen Konfigurations- und Steuerungsmöglichkeiten der Apache Cassandra-Open-Source-Lösung nutzen möchten, ohne dass hoher Wartungsaufwand anfällt.

### <a name="why-should-i-use-this-service-instead-of-azure-cosmos-db-cassandra-api"></a>Warum sollte ich diesen Dienst anstelle der Cassandra-API für Azure Cosmos DB nutzen?

Azure Managed Instance for Apache Cassandra wird vom Azure Cosmos DB-Team bereitgestellt. Es handelt sich um einen eigenständigen verwalteten Dienst für die Bereitstellung, Wartung und Skalierung von Apache Cassandra-Open-Source-Rechenzentren und -Clustern. Die [Cassandra-API für Azure Cosmos DB](../cosmos-db/cassandra-introduction.md) ist dagegen vom Typ „Platform-as-a-Service“ und dient als Interoperabilitätsebene für das Apache Cassandra Wire Protocol. Wenn Sie möchten, dass sich die Plattform genauso wie alle anderen Apache Cassandra-Cluster verhält, sollten Sie den Managed Instance-Dienst wählen. Weitere Informationen finden Sie im Artikel [Gegenüberstellung von Managed Instance und Cassandra-API für Azure Cosmos DB](compare-cosmosdb-managed-instance.md).

### <a name="is-azure-managed-instance-for-apache-cassandra-dependent-on-azure-cosmos-db"></a>Ist Azure Managed Instance for Apache Cassandra von Azure Cosmos DB abhängig?

Nein. Es besteht keine architekturbezogene Abhängigkeit zwischen Azure Managed Instance for Apache Cassandra und dem Azure Cosmos DB-Back-End. 

#### <a name="can-i-deploy-azure-managed-instance-for-apache-cassandra-in-any-region"></a>Kann ich Azure Managed Instance for Apache Cassandra in jeder beliebigen Region bereitstellen?

Derzeit ist die verwaltete Instanz nur in einer begrenzten Zahl von Regionen verfügbar.

### <a name="what-are-the-storage-and-throughput-limits-of-azure-managed-instance-for-apache-cassandra"></a>Welche Speicher- und Durchsatzlimits gelten für Azure Managed Instance for Apache Cassandra?

Diese Limits richten sich nach den VM-SKUs, die Sie auswählen.

### <a name="how-are-cassandra-repairs-carried-out-in-azure-managed-instance-for-apache-cassandra"></a>Wie werden Cassandra-Reparaturen in Azure Managed Instance for Apache Cassandra durchgeführt?

Wir verwenden [cassandra-reaper.io](http://cassandra-reaper.io/). Es ist so eingerichtet, dass es automatisch für Sie ausgeführt wird.

### <a name="what-is-the-cost-of-azure-managed-instance-for-apache-cassandra"></a>Wie hoch sind die Kosten für Azure Managed Instance for Apache Cassandra?

Für die verwaltete Instanz wird ein geringer Aufschlag auf die zugrunde liegenden VM-Kosten berechnet. Weitere Informationen hierzu finden Sie in der [Preisübersicht](https://azure.microsoft.com/pricing/details/managed-instance-apache-cassandra/).

### <a name="can-i-use-yaml-file-settings-to-configure-behavior"></a>Kann ich das Verhalten mithilfe von YAML-Dateieinstellungen konfigurieren?

Ja. Sie können YAML-Dateikonfigurationen in die Bereitstellung einer Azure Resource Manager-Vorlage einbinden.

### <a name="how-can-i-monitor-infrastructure-along-with-throughput"></a>Wie kann ich die Infrastruktur zusammen mit dem Durchsatz überwachen?

Der [Prometheus](https://prometheus.io/docs/introduction/overview/)-Server wird gehostet, um die Aktivitäten in Ihrem Cluster zu überwachen, und es wird ein Endpunkt verfügbar gemacht. Hiermit wird eine Datenmenge verwaltet, die 10 Minuten oder 10 GB entspricht (je nachdem, welcher Schwellenwert zuerst erreicht wird). Für die Nutzung dieser Überwachung müssen Sie einen [Verbund](https://prometheus.io/docs/prometheus/latest/federation/) einrichten und ein geeignetes Dashboardtool, z. B. Grafana, nutzen.

### <a name="does-azure-managed-instance-for-apache-cassandra-provide-full-backups"></a>Sind mit Azure Managed Instance for Apache Cassandra vollständige Sicherungen möglich?

Ja. Es sind vollständige Sicherungen in Azure Storage und Wiederherstellungen in einem neuen Cluster möglich.

### <a name="how-can-i-migrate-data-from-my-existing-apache-cassandra-cluster-to-azure-managed-instance-for-apache-cassandra"></a>Wie kann ich Daten aus meinem vorhandenen Apache Cassandra-Cluster zu Azure Managed Instance for Apache Cassandra migrieren?

Azure Managed Instance for Apache Cassandra unterstützt alle Features von Apache Cassandra zum Replizieren und Streamen von Daten zwischen Rechenzentren.

### <a name="can-i-pair-an-on-premises-apache-cassandra-cluster-with-the-azure-managed-instance-for-apache-cassandra"></a>Kann ich einen lokalen Apache Cassandra-Cluster mit Azure Managed Instance for Apache Cassandra koppeln?

Ja. Sie können einen Hybridcluster mit eingefügten Azure Virtual Network-Rechenzentren konfigurieren, die vom Dienst bereitgestellt werden. Managed Instance-Rechenzentren können mit lokalen Rechenzentren über denselben Clusterring kommunizieren.

### <a name="where-can-i-give-feedback-on-azure-managed-instance-for-apache-cassandra-features"></a>Wo kann ich Feedback zu den Features von Azure Managed Instance for Apache Cassandra geben?

Teilen Sie uns Ihr Feedback auf der Seite für [UserVoice-Feedback](https://feedback.azure.com/d365community/forum/3002b3be-0d25-ec11-b6e6-000d3a4f0858?c=e6e5c7c4-0d25-ec11-b6e6-000d3a4f0858#) mit, indem Sie die Kategorie „Managed Apache Cassandra“ verwenden.

Um ein Problem mit Ihrem Konto zu beheben, richten Sie im Azure-Portal eine [Anfrage an den Support](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="deployment-specific-faq"></a>Häufig gestellte Fragen zur Bereitstellung

### <a name="will-the-managed-instance-support-node-addition-cluster-status-and-node-status-commands"></a>Unterstützt die verwaltete Instanz Befehle für das Hinzufügen von Knoten sowie für den Cluster- und Knotenstatus?

Alle *schreibgeschützten* Nodetool-Befehle, z. B. `status`, sind über die Azure CLI verfügbar. Vorgänge wie das *Hinzufügen von Knoten* sind nicht verfügbar, da wir die Integrität der Knoten auf der verwalteten Instanz verwalten. Im Hybridmodus können Sie per *Nodetool* eine Verbindung mit dem Cluster herstellen. Die Verwendung von Nodetool wird aber nicht empfohlen, da dies zu einer Destabilisierung des Clusters führen kann. Darüber hinaus kann dies auch dazu führen, dass die Produktionssupport-SLA ungültig wird, die für die Integrität der Rechenzentren von verwalteten Instanzen im Cluster gilt.

### <a name="what-happens-with-various-settings-for-table-metadata"></a>Wie sieht es mit verschiedenen Einstellungen für Tabellenmetadaten aus?

Die Einstellungen für Tabellenmetadaten, z. B. Bloomfilter, Zwischenspeicherung, Lesereparaturänderungen, „gc_grace“ und „compression memtable_flush_period“, werden wie bei allen selbstgehosteten Apache Cassandra-Umgebungen vollständig unterstützt.

## <a name="next-steps"></a>Nächste Schritte

Informationen zu häufig gestellten Fragen anderer APIs finden Sie unter:

* [Erstellen eines Azure SQL Managed Instance-Clusters über das Azure-Portal](create-cluster-portal.md)
* [Bereitstellen eines verwalteten Apache Spark-Clusters mit Azure Databricks](deploy-cluster-databricks.md)
* [Verwalten von Azure Managed Instance for Apache Cassandra-Ressourcen mit der Azure CLI](manage-resources-cli.md)
