---
title: Einführung zu Azure Managed Instance for Apache Cassandra
description: Erfahren Sie mehr über Azure Managed Instance for Apache Cassandra. Dieser Dienst verwaltet die Bereitstellung und Skalierung von nativen Open-Source-Instanzen von Apache Cassandra in Azure.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: overview
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 5d704e9fc46918624014da84ccdae46dc25817ea
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131084878"
---
# <a name="what-is-azure-managed-instance-for-apache-cassandra"></a>Was ist Azure Managed Instance for Apache Cassandra?

Der Dienst Azure Managed Instance for Apache Cassandra bietet automatisierte Bereitstellungs- und Skalierungsvorgänge für verwaltete Open-Source-basierte Apache Cassandra-Rechenzentren. Dieser Dienst unterstützt Sie dabei, Hybridszenarien zu beschleunigen und die laufende Wartung zu verringern. Bei der allgemeinen Veröffentlichung wird er umfassende Integrations- und Datenverschiebungsfunktionen mit der [Azure Cosmos DB-Cassandra-API](../cosmos-db/cassandra-introduction.md) bieten.

:::image type="content" source="./media/introduction/icon.gif" alt-text="Azure Managed Instance for Apache Cassandra ist ein verwalteter Dienst für Apache Cassandra." border="false":::

## <a name="key-benefits"></a>Hauptvorteile

### <a name="hybrid-deployments"></a>Hybridbereitstellungen

Mit diesem Dienst können Sie problemlos verwaltete Instanzen von Apache Cassandra-Rechenzentren, die automatisch als VM-Skalierungsgruppen bereitgestellt werden, in einem neuen oder vorhandenen Azure-VNET platzieren. Diese Rechenzentren können Ihrem vorhandenen Apache Cassandra-Ring, der lokal über [Azure ExpressRoute](/azure/architecture/reference-architectures/hybrid-networking/expressroute) in Azure ausgeführt wird, oder einer anderen Cloudumgebung hinzugefügt werden.

- **Vereinfachte Bereitstellung:** Nach Herstellen der Hybridkonnektivität ist die Bereitstellung über das Gossipprotokoll einfach.
- **Gehostete Metriken:** Die Metriken werden in [Prometheus](https://prometheus.io/docs/introduction/overview/) gehostet, um Aktivitäten in Ihrem Cluster zu überwachen.

### <a name="simplified-scaling"></a>Vereinfachte Skalierung

In der verwalteten Instanz wird das Hoch- und Herunterskalieren von Knoten in einem Rechenzentrum vollständig verwaltet. Sie geben die Anzahl der benötigten Knoten ein, und der Skalierungsorchestrator übernimmt die Einrichtung des Vorgangs innerhalb des Cassandra-Rings.

### <a name="managed-and-cost-effective"></a>Verwaltet und kostengünstig

Der Dienst stellt Verwaltungsvorgänge für die folgenden allgemeinen Apache Cassandra-Aufgaben bereit:

- Bereitstellen eines Clusters
- Bereitstellen eines Rechenzentrums
- Skalieren eines Rechenzentrums
- Löschen eines Rechenzentrums
- Starten einer Reparaturaktion für einen Keyspace
- Ändern der Konfiguration eines Rechenzentrums
- Einrichten von Sicherungen

Das Preismodell ist flexibel, bedarfsgesteuert, instanzbasiert und frei von Lizenzierungsgebühren. Dieses Preismodell ermöglicht Ihnen die Anpassung an Ihre spezifischen Workloadanforderungen. Wählen Sie aus, wie viele Kerne, welche VM-SKU, Arbeitsspeichergröße und Datenträger-Speicherplatzgröße Sie benötigen.

## <a name="next-steps"></a>Nächste Schritte

Verwenden Sie eine unserer Schnellstartanleitungen:

* [Erstellen einer Azure Managed Instance-Instanz für Apache Cassandra-Cluster im Azure-Portal (Vorschau)](create-cluster-portal.md)
* [Bereitstellen eines verwalteten Apache Spark-Clusters mit Azure Databricks](deploy-cluster-databricks.md)
* [Verwalten von Azure Managed Instance for Apache Cassandra-Ressourcen mit der Azure CLI](manage-resources-cli.md)
