---
title: Unterschiede zwischen Azure Managed Instance for Apache Cassandra und der Cassandra-API in Azure Cosmos DB
description: Hier finden Sie Informationen zu den Unterschieden zwischen Azure Managed Instance for Apache Cassandra und der Cassandra-API in Azure Cosmos DB. Außerdem erfahren Sie, welche Vorteile die Dienste haben und wann sie verwendet werden sollten.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 1927911563775711d6f0ec969ada82f1be286650
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131032489"
---
# <a name="differences-between-azure-managed-instance-for-apache-cassandra-and-azure-cosmos-db-cassandra-api"></a>Unterschiede zwischen Azure Managed Instance for Apache Cassandra und der Cassandra-API in Azure Cosmos DB 

Dieser Artikel enthält Informationen zu den Unterschieden zwischen Azure Managed Instance for Apache Cassandra und der Cassandra-API in Azure Cosmos DB. Der Artikel enthält Empfehlungen zur Wahl eines der beiden Dienste, und Sie erfahren, wann Sie eine eigene Apache Cassandra-Umgebung hosten sollten.

## <a name="key-differences"></a>Wesentliche Unterschiede

Azure Managed Instance for Apache Cassandra bietet automatisierte Bereitstellung, Skalierung und Vorgänge zur Aufrechterhaltung der Knotenintegrität für verwaltete Open-Source-basierte Apache Cassandra-Instanzen in Azure. Darüber hinaus lässt sich hiermit die Kapazität bereits vorhandener lokaler oder cloudbasierter selbstgehosteter Apache Cassandra-Cluster aufskalieren. Hierzu werden dem vorhandenen Clusterring verwaltete Cassandra-Rechenzentren hinzugefügt.

Bei der [Cassandra-API](../cosmos-db/cassandra-introduction.md) in Azure Cosmos DB handelt es sich um eine Kompatibilitätsebene über dem global verteilten cloudnativen Datenbankdienst [Azure Cosmos DB](../cosmos-db/index.yml) von Microsoft. Die Kombination dieser Dienste in Azure bietet eine Reihe von Optionen für Benutzer von Apache Cassandra in komplexen Hybrid Cloud-Umgebungen.

## <a name="how-to-choose"></a>Wofür soll ich mich entscheiden?

Die folgende Tabelle enthält die gängigen Szenarien, Workloadanforderungen und passenden Einsatzmöglichkeiten für die einzelnen Bereitstellungsansätze:

| |Selbstgehostete Azure Cassandra-Instanz (lokal oder in Azure) | Azure Managed Instance for Apache Cassandra | Cassandra-API für Azure Cosmos DB |
|---------|---------|---------|---------|
|**Bereitstellungstyp**| Sie verfügen über eine hochgradig angepasste Apache Cassandra-Bereitstellung mit benutzerdefinierten Patches oder Snitches. | Sie verfügen über eine Open Source-basierte Apache Cassandra-Standardbereitstellung ohne benutzerdefinierten Code. | Ihnen reicht eine Plattform, die nicht auf Apache Cassandra basiert, aber mit allen Open-Source-Clienttreibern auf [Wire Protocol](../cosmos-db/cassandra-support.md)-Ebene kompatibel ist. |
| **Operativer Mehraufwand**| Sie verfügen über Cassandra-Experten, die Ihre Cluster bereitstellen, konfigurieren und verwalten können.  | Sie möchten den operativen Mehraufwand für Ihre Apache Cassandra-Knotenintegrität verringern, ohne jedoch die Kontrolle über die Konfigurationen auf Plattformebene (wie Replikation und Konsistenz) aufzugeben. | Sie möchten eine vollständig verwaltete PaaS-Datenbank (Platform-as-a-Service) in der Cloud verwenden und den operativen Mehraufwand beseitigen. |
| **Betriebssystemanforderungen**| Sie müssen VM-Betriebssystemimages (benutzerdefiniert oder Gold-Version) verwalten. | Sie können einfache Images verwenden, möchten aber Kontrolle über SKUs, Arbeitsspeicher, Datenträger und IOPS haben. | Sie möchten, dass die Kapazitätsbereitstellung vereinfacht und als normalisierte Einzelmetrik mit direkter Beziehung zum Durchsatz ausgedrückt wird (beispielsweise in Form von [Anforderungseinheiten](../cosmos-db/request-units.md) in Azure Cosmos DB). |
| **Preismodell**| Sie möchten Verwaltungssoftware wie Datastax-Tools verwenden und haben kein Problem mit Lizenzierungskosten. | Sie bevorzugen eine reine Open-Source-Lizenzierung und Preise auf VM-Instanzbasis. | Sie möchten cloudnative Preise verwenden – einschließlich Angebote für [Autoskalierung](../cosmos-db/manage-scale-cassandra.md#use-autoscale) und [serverlos](../cosmos-db/serverless.md). |
| **Analyse**| Sie möchten uneingeschränkte Kontrolle über die Bereitstellung von Analysepipelines, unabhängig vom Zusatzaufwand für die Erstellung und Wartung. | Sie möchten cloudbasierte Analysedienste wie Azure Databricks verwenden. | Sie möchten eine hybride, in die Plattform integrierte Transaktionsanalyse in Quasi-Echtzeit mit [Azure Synapse Link für Cosmos DB](../cosmos-db/synapse-link.md). |
| **Arbeitsauslastungsmuster**| Ihre Arbeitsauslastung ist relativ konstant, und die Knoten im Cluster müssen nicht oft skaliert werden. | Ihre Arbeitsauslastung schwankt, und Sie müssen mühelos Knoten in einem Rechenzentrum hoch- oder herunterskalieren oder Rechenzentren hinzufügen/entfernen können. | Ihre Arbeitsauslastung schwankt häufig, und Sie müssen schnell und in großem Umfang hoch- oder herunterskalieren können. |
| **SLAs**| Sie sind zufrieden mit Ihren Prozessen für die Erfüllung von SLAs in Bezug auf Konsistenz, Durchsatz, Verfügbarkeit und Notfallwiederherstellung. | Sie sind zufrieden mit Ihren Prozessen für die Erfüllung von SLAs in Bezug auf Konsistenz, Durchsatz und Verfügbarkeit, benötigen aber Unterstützung bei Sicherungen. | Sie benötigen vollumfängliche SLAs für Konsistenz, Durchsatz, Verfügbarkeit und Notfallwiederherstellung. |

## <a name="next-steps"></a>Nächste Schritte

Verwenden Sie eine unserer Schnellstartanleitungen:

* [Erstellen einer Azure Managed Instance-Instanz für Apache Cassandra-Cluster im Azure-Portal (Vorschau)](create-cluster-portal.md)
