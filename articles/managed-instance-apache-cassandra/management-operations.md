---
title: Verwaltungsvorgänge in Azure Managed Instance for Apache Cassandra
description: Erfahren Sie mehr über die Verwaltungsvorgänge, die von Azure Managed Instance for Apache Cassandra unterstützt werden. Außerdem wird die Trennung von Zuständigkeiten zwischen dem Azure-Supportteam und Kunden bei der Verwaltung eigenständiger und hybrider Cluster erläutert.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: overview
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: d5a3dfe84c18d85d83148dc30a6092b85245e7b5
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131101820"
---
# <a name="management-operations-in-azure-managed-instance-for-apache-cassandra"></a>Verwaltungsvorgänge in Azure Managed Instance for Apache Cassandra

Azure Managed Instance for Apache Cassandra bietet automatisierte Bereitstellungs- und Skalierungsvorgänge für verwaltete Open-Source-basierte Apache Cassandra-Rechenzentren. In diesem Artikel werden die vom Dienst gebotenen Verwaltungsvorgänge und -features vorgestellt. Außerdem wird die Trennung von Zuständigkeiten zwischen dem Azure-Supportteam und Kunden bei der Verwaltung eigenständiger und [hybrider](configure-hybrid-cluster.md) Cluster erläutert.

## <a name="patching"></a>Patching

* Patches auf Betriebssystemebene erfolgen automatisch in einem Rhythmus von etwa zwei Wochen.

* Patches auf Apache Cassandra-Softwareebene erfolgen, sobald Sicherheitsrisiken erkannt werden. Der Rhythmus des Patchens kann variieren.

* Während des Patchens werden Computer rackweise neu gestartet. Es sollte keine Beeinträchtigung auf Anwendungsseite auftreten, solange die **Quorumeinstellung ALL nicht verwendet wird** und der Replikationsfaktor **mindestens 3** ist.

* Die Version in Apache Cassandra hat das Format `X.Y.Z`. Sie können mithilfe von Diensttools die Bereitstellung von Hauptversionen (X) und Nebenversionen (Y) manuell steuern. Die Cassandra-Patches (Z), die für diese Kombination von Haupt- und Nebenversion erforderlich sein können, erfolgen jedoch automatisch.  

## <a name="maintenance"></a>Wartung

* Die [Nodetool-Reparatur](https://docs.datastax.com/cassandra-oss/3.0/cassandra/tools/toolsRepair.html) wird vom Dienst mithilfe [reaper](http://cassandra-reaper.io/) automatisch durchgeführt. Dieses Tool wird einmal pro Woche ausgeführt. Sie können es deaktivieren, wenn Sie für eine [Hybridbereitstellung](configure-hybrid-cluster.md) Ihren eigenen Dienst verwenden.

* Die Überwachung der Knotenintegrität umfasst Folgendes:

  * Aktive Überwachung der Mitgliedschaft jedes Knotens im Cassandra-Ring
  * Aktive Überwachung virtueller Computer, um Probleme mit Azure, virtuellen Computern, Speicher, Linux und der unterstützenden Software auszumachen und zu beheben

* Sie sind für alle Probleme in Bezug auf CPU-, Datenträger- und Netzwerkauslastung zuständig, die durch Ihre Cassandra-Nutzung entstehen können. Beispiele für solche Probleme sind u. a.:

  * Ineffiziente Abfragevorgänge
  * Die Kapazität überschreitender Durchsatz
  * Die Speicherkapazität überschreitende Datenerfassung
  * Falsche Konfigurationseinstellungen für Keyspace
  * Schlechte Datenmodell- oder Partitionsschlüsselstrategie

## <a name="security"></a>Sicherheit

Azure Managed Instance for Apache Cassandra bietet viele integrierte explizite Sicherheitskontrollen und -features:

* Gehärtete Linux-VM-Images mit kontrollierter Lieferkette
* CVE-Überwachung (Common Vulnerability & Exposure) auf Betriebssystemebene
* Rotation von Zertifikaten für Apache Cassandra- und Prometheus-Software, die auf den verwalteten VMs gehostet wird
* Aktive Überprüfung auf Sicherheitsrisiken
* Aktive Überprüfung auf Viren
* Methoden zum Schreiben von sicherem Code

## <a name="hybrid-support"></a>Unterstützung für Hybridlösungen

Wenn ein [Hybridcluster](configure-hybrid-cluster.md) konfiguriert ist, profitieren der gesamte Cluster von automatisierten Reaper-Vorgängen, die im Dienst ausgeführt werden. Dies schließt Rechenzentren ein, die nicht vom Dienst bereitgestellt werden. Ansonsten liegt die Betreuung Ihres lokalen oder extern gehosteten Rechenzentrums in Ihrer Verantwortung.

## <a name="next-steps"></a>Nächste Schritte

Verwenden Sie eine unserer Schnellstartanleitungen:
* [Erstellen einer Azure Managed Instance-Instanz für Apache Cassandra-Cluster im Azure-Portal (Vorschau)](create-cluster-portal.md)
* [Bereitstellen eines verwalteten Apache Spark-Clusters mit Azure Databricks](deploy-cluster-databricks.md)
* [Verwalten von Azure Managed Instance for Apache Cassandra-Ressourcen mit der Azure CLI](manage-resources-cli.md)
