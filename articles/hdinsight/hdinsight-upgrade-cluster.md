---
title: Migrieren von Clustern zu einer neueren Version
titleSuffix: Azure HDInsight
description: Erfahren Sie mehr über Richtlinien für das Migrieren Ihres Azure HDInsight-Clusters zu einer neueren Version.
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/31/2020
ms.openlocfilehash: 6fa655092de1c3e103381ccd58bf840b70a3809e
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130249759"
---
# <a name="migrate-hdinsight-cluster-to-a-newer-version"></a>Migrieren eines HDInsight-Clusters zu einer neueren Version

Um die neuesten HDInsight-Funktionen nutzen zu können, wird empfohlen, HDInsight-Cluster regelmäßig zur neuesten Version zu migrieren. HDInsight unterstützt keine direkten Upgrades, bei denen ein vorhandener Cluster auf eine neuere Komponentenversion aktualisiert wird. Sie müssen einen neuen Cluster mit der gewünschten Komponenten- und Plattformversion erstellen und dann die Anwendungen migrieren, um den neuen Cluster zu verwenden. Befolgen Sie die nachstehenden Leitlinien zum Migrieren Ihrer HDInsight-Clusterversionen.

> [!NOTE]  
> Informationen zu unterstützten HDInsight-Versionen finden Sie unter [HDInsight-Komponentenversionen](hdinsight-component-versioning.md#supported-hdinsight-versions).

## <a name="migration-tasks"></a>Migrationsaufgaben

Der Workflow für ein Upgrade eines HDInsight-Clusters ist wie folgt.
:::image type="content" source="./media/hdinsight-upgrade-cluster/upgrade-workflow-diagram.png" alt-text="HDInsight-Upgradeworkflow – Diagramm" border="false":::

1. Lesen Sie jeden Abschnitt dieses Dokuments, um zu verstehen, welche Änderungen möglicherweise erforderlich sind, wenn Sie Ihren HDInsight-Cluster aktualisieren.
2. Erstellen Sie einen Cluster als Test-/Qualitätssicherungsumgebung. Weitere Informationen zur Erstellung eines Clusters finden Sie unter [Erstellen von Linux-basierten Hadoop-Clustern in HDInsight](hdinsight-hadoop-provision-linux-clusters.md).
3. Kopieren Sie vorhandene Aufträge, Datenquellen und Senken in die neue Umgebung.
4. Führen Sie Überprüfungstests durch, um sicherzustellen, dass Ihre Aufträge im neuen Cluster wie erwartet funktionieren.

Nachdem Sie überprüft haben, dass alles wie erwartet funktioniert, planen Sie für die Migration eine Downtime. Führen Sie während dieser Ausfallzeit die folgenden Aktionen durch:

1. Sichern Sie alle Daten, die vorübergehend lokal auf dem Clusterknoten gespeichert sind. zum Beispiel direkt auf einem Hauptknoten gespeicherte Daten.
1. [Löschen Sie den vorhandenen Cluster.](./hdinsight-delete-cluster.md)
1. Erstellen Sie einen Cluster im gleichen VNET-Subnetz mit der aktuellen (oder einer unterstützten) HDI-Version, die den gleichen Standarddatenspeicher wie der vorherige Cluster verwendet. Dadurch wird dem neuen Cluster ermöglicht, weiterhin mit Ihren vorhandenen Produktionsdaten zu arbeiten.
1. Importieren Sie alle vorübergehenden Daten, die Sie gesichert haben.
1. Starten Sie Aufträge/Verarbeiten Sie weiterhin mithilfe des neuen Clusters.

## <a name="workload-specific-guidance"></a>Workloadspezifischer Leitfaden

Die folgenden Dokumente enthalten Anleitungen zum Migrieren bestimmter Workloads:

* [Migrieren von HBase](./hbase/apache-hbase-migrate-new-version.md)
* [Migrieren von Kafka](./kafka/migrate-versions.md)
* [Migrieren von Hive/Interactive Query](./interactive-query/apache-hive-migrate-workloads.md)

## <a name="backup-and-restore"></a>Sichern und Wiederherstellen

Weitere Informationen zur Datenbanksicherung und -wiederherstellung finden Sie unter [Wiederherstellen einer Datenbank in Azure SQL-Datenbank mit automatisierten Datenbanksicherungen](../azure-sql/database/recovery-using-backups.md).

## <a name="upgrade-scenarios"></a>Upgradeszenarien

Wie bereits erwähnt, empfiehlt Microsoft, HDInsight-Cluster regelmäßig auf die neueste Version zu migrieren, um von neuen Features und Fehlerbehebungen zu profitieren. Sehen Sie sich die folgende Liste von Gründen an, aus denen wir ein Löschen und erneutes Bereitstellen eines Clusters anfordern würden:

* Die Clusterversion ist [eingestellt](hdinsight-retired-versions.md) oder in [Basic-Support](hdinsight-36-component-versioning.md), und es liegt ein Clusterproblem vor, das durch eine neuere Version behoben würde.
* Es wird festgestellt, dass die Grundursache eines Clusterproblems mit einer zu kleinen VM in Zusammenhang steht. [Sehen Sie sich die von Microsoft empfohlene Knotenkonfiguration an](hdinsight-supported-node-configuration.md#all-supported-regions-except-brazil-south-and-japan-west).
* Ein Kunde öffnet einen Supportfall, und das Microsoft-Entwicklerteam stellt fest, dass das Problem bereits in einer neueren Clusterversion behoben wurde.
* Eine Standardmetastore-Datenbank (Ambari, Hive, Oozie, Ranger) hat das Auslastungslimit erreicht. Microsoft bittet Sie, den Cluster mithilfe einer [benutzerdefinierten Metastore-Datenbank](hdinsight-use-external-metadata-stores.md#custom-metastore) neu zu erstellen.
* Die Grundursache eines Clusterproblems ist ein **nicht unterstützter Vorgang**. Nachfolgend sind einige der häufigsten nicht unterstützten Vorgänge aufgeführt:
     * **Verschieben oder Hinzufügen eines Diensts in Ambari**. Beim Anzeigen von Informationen zu den Clusterdiensten in Ambari lautet eine der im Menü „Dienstaktionen“ verfügbaren Aktionen **[Dienstname] verschieben**. Eine weitere Aktion ist **[Dienstname] hinzufügen**. Beide Optionen werden nicht unterstützt.
     * **Beschädigung des Python-Pakets**. HDInsight-Cluster sind von den integrierten Python-Umgebungen (Python 2.7 und Python 3.5) abhängig. Die direkte Installation von benutzerdefinierten Paketen in diesen standardmäßig integrierten Umgebungen kann zu unerwarteten Änderungen der Bibliotheksversionen und zur Unterbrechung des Clusters führen. Informieren Sie sich, wie Sie [benutzerdefinierte externe Python-Pakete für Ihre Spark-Anwendungen sicher installieren](./spark/apache-spark-python-package-installation.md#safely-install-external-python-packages).
     * **Drittanbietersoftware**. Kunden haben die Möglichkeit, Drittanbietersoftware in ihren HDInsight-Clustern zu installieren. Es wird jedoch empfohlen, den Cluster neu zu erstellen, wenn dadurch die vorhandene Funktionalität unterbrochen wird.
     * **Mehrere Workloads im gleichen Cluster**. In HDInsight 4.0 erfordert Hive Warehouse Connector separate Cluster für Spark- und Interactive Query-Workloads. [Führen Sie diese Schritte aus, um beide Cluster in Azure HDInsight einzurichten](interactive-query/apache-hive-warehouse-connector.md). Auch die Integration von [Spark mit HBASE](hdinsight-using-spark-query-hbase.md) erfordert zwei verschiedene Cluster.
     * **Benutzerdefiniertes Kennwort für die Ambari-Datenbank wurde geändert**. Das Kennwort für die Ambari-Datenbank wird während der Clustererstellung festgelegt, und es gibt derzeit keinen Mechanismus, um es zu aktualisieren. Wenn ein Kunde den Cluster mit einer [benutzerdefinierten Ambari-Datenbank](hdinsight-custom-ambari-db.md) bereitgestellt, hat er die Möglichkeit, das Datenbankkennwort in der SQL-Datenbank zu ändern. Es gibt jedoch keine Möglichkeit, dieses Kennwort für einen ausgeführten HDInsight-Cluster zu aktualisieren.

## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie, wie Sie Linux-basierte HDInsight-Cluster erstellen](hdinsight-hadoop-provision-linux-clusters.md)
* [Herstellen von Verbindungen mit HDInsight per SSH](hdinsight-hadoop-linux-use-ssh-unix.md)
* [Verwalten eines Linux-basierten Clusters mithilfe von Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [Versionshinweise zu HDInsight](./hdinsight-version-release.md)
