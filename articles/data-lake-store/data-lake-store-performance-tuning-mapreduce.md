---
title: Optimierung der Leistung von Azure Data Lake Storage Gen1 – MapReduce
description: Hier erfahren Sie mehr über die Leistungsoptimierung für MapReduce in Azure Data Lake Storage Gen1, einschließlich der Parameter, Anleitungen, einer Beispielberechnung und Einschränkungen.
author: normesta
ms.service: data-lake-store
ms.topic: how-to
ms.date: 12/19/2016
ms.author: normesta
ms.openlocfilehash: cbe7c9b8ac535d6f69ac6043524d1e720b9a96fc
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128660954"
---
# <a name="performance-tuning-guidance-for-mapreduce-on-hdinsight-and-azure-data-lake-storage-gen1"></a>Anleitung für die Leistungsoptimierung für MapReduce in HDInsight und Azure Data Lake Storage Gen1

## <a name="prerequisites"></a>Voraussetzungen

* **Ein Azure-Abonnement**. Siehe [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/).
* **Ein Azure Data Lake Storage Gen1-Konto**. Eine Anleitung zur Erstellung finden Sie unter [Erste Schritte mit Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md).
* **Ein Azure HDInsight-Cluster** mit Zugriff auf ein Data Lake Storage Gen1-Konto. Weitere Informationen finden Sie unter [Erstellen eines HDInsight-Clusters mit Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md). Stellen Sie sicher, dass Remotedesktop für den Cluster aktiviert ist.
* **Verwendung von MapReduce in HDInsight**. Weitere Informationen finden Sie unter [Verwenden von MapReduce mit Hadoop in HDInsight](../hdinsight/hadoop/hdinsight-use-mapreduce.md).
* Überprüfen Sie die **Richtlinien für die Leistungsoptimierung bei Data Lake Storage Gen1**. Allgemeine Leistungskonzepte finden Sie unter [Anleitung für die Leistungsoptimierung von Data Lake Storage Gen1](./data-lake-store-performance-tuning-guidance.md).

## <a name="parameters"></a>Parameter

Im Folgenden finden Sie die wichtigsten Parameter, die Sie für die Ausführung von MapReduce konfigurieren können, um die Leistung in Azure Data Lake Storage Gen1 zu verbessern:

|Parameter      | BESCHREIBUNG  |
|---------|---------|
|`Mapreduce.map.memory.mb`  |  Die Menge an Arbeitsspeicher, die jedem Mapper zugeordnet werden soll.  |
|`Mapreduce.job.maps`     |  Die Anzahl von Zuordnungsaufgaben pro Auftrag.  |
|`Mapreduce.reduce.memory.mb`     |  Die Menge an Arbeitsspeicher, die jedem Reducer zugeordnet werden soll.  |
|`Mapreduce.job.reduces`    |   Die Anzahl von Reduzierungsaufgaben pro Auftrag.  |

### <a name="mapreducemapmemory--mapreducereducememory"></a>„Mapreduce.map.memory“ / „Mapreduce.reduce.memory“

Passen Sie diese Zahl basierend auf der Speichermenge an, die für die Zuordnungs- und/oder Reduzierungsaufgabe benötigt wird. Sie können die Standardwerte von `mapreduce.map.memory` und `mapreduce.reduce.memory` in Ambari über die Yarn-Konfiguration anzeigen. Navigieren Sie in Ambari zu YARN, und zeigen Sie die Registerkarte **Configs** (Konfigurationen) an. Der YARN-Arbeitsspeicher wird angezeigt.

### <a name="mapreducejobmaps--mapreducejobreduces"></a>„Mapreduce.job.maps“ / „Mapreduce.job.reduces“

Hiermit wird die maximale Anzahl der zu erstellenden Mapper oder Reducer bestimmt. Die Anzahl der Teilungen legt fest, wie viele Mapper für den MapReduce-Auftrag erstellt werden. Daher erhalten Sie möglicherweise weniger Mapper als angefordert, wenn weniger Teilungen als angeforderte Mapper vorhanden sind.

## <a name="guidance"></a>Anleitungen

### <a name="step-1-determine-number-of-jobs-running"></a>Schritt 1: Ermitteln der Anzahl von ausgeführten Aufträgen

Standardmäßig nutzt MapReduce den gesamten Cluster für Ihren Auftrag. Sie können einen kleineren Teil des Clusters verwenden, indem Sie weniger Mapper verwenden als Container verfügbar sind. Bei den Anleitungen in diesem Dokument wird davon ausgegangen, dass Ihre Anwendung die einzige Anwendung ist, die in Ihrem Cluster ausgeführt wird.

### <a name="step-2-set-mapreducemapmemorymapreducereducememory"></a>Schritt 2: Festlegen von „mapreduce.map.memory/mapreduce.reduce.memory“

Die Größe des Arbeitsspeichers für Mapper/Reducer-Aufgaben richtet sich nach Ihrem jeweiligen Auftrag. Sie können die Arbeitsspeichergröße reduzieren, wenn Sie die Parallelität erhöhen möchten. Die Anzahl von gleichzeitig ausgeführten Tasks richtet sich nach der Anzahl von Containern. Durch Verringern der Arbeitsspeichermenge pro Mapper oder Reducer können mehr Container erstellt werden, wodurch mehr Mapper oder Reducer gleichzeitig ausgeführt werden können. Wenn die Arbeitsspeichermenge zu stark verringert wird, steht für einige Prozesse möglicherweise nicht genügend Arbeitsspeicher zur Verfügung. Wenn beim Ausführen Ihres Auftrags ein Heapfehler angezeigt wird, erhöhen Sie den Arbeitsspeicher pro Mapper oder Reducer. Denken Sie daran, dass beim Hinzufügen weiterer Container zusätzlicher Overhead für jeden zusätzlichen Container entsteht. Dadurch kann die Leistung beeinträchtigt werden. Eine andere Möglichkeit besteht darin, mehr Arbeitsspeicher zur Verfügung zu stellen, indem Sie einen Cluster verwenden, der über mehr Arbeitsspeicher verfügt, oder indem Sie die Anzahl von Knoten in Ihrem Cluster erhöhen. Eine größere Menge an Arbeitsspeicher ermöglicht die Verwendung einer größeren Anzahl von Containern, was wiederum mehr Parallelität bedeutet.

### <a name="step-3-determine-total-yarn-memory"></a>Schritt 3: Ermitteln des YARN-Gesamtarbeitsspeichers

Berücksichtigen Sie beim Optimieren von „mapreduce.job.maps/mapreduce.job.reduces“ die Menge an YARN-Gesamtarbeitsspeicher, die für die Nutzung zur Verfügung steht. Diese Informationen sind in Ambari verfügbar. Navigieren Sie zu YARN, und zeigen Sie die Registerkarte **Configs** an. Die Größe des YARN-Arbeitsspeichers wird in diesem Fenster angezeigt. Multiplizieren Sie den YARN-Arbeitsspeicher mit der Anzahl von Knoten in Ihrem Cluster, um den YARN-Gesamtarbeitsspeicher zu erhalten.

`Total YARN memory = nodes * YARN memory per node`

Wenn Sie einen leeren Cluster verwenden, kann der Arbeitsspeicher der YARN-Gesamtarbeitsspeicher für Ihren Cluster sein. Wenn andere Anwendungen auch Arbeitsspeicher belegen, können Sie nur einen Teil des Clusterarbeitsspeichers nutzen, indem Sie die Anzahl von Mappern oder Reducern auf die Anzahl von Containern verringern, die Sie verwenden möchten.

### <a name="step-4-calculate-number-of-yarn-containers"></a>Schritt 4: Berechnen der Anzahl von YARN-Containern

YARN-Container geben vor, wie viel Parallelität für den Auftrag verfügbar ist. Teilen Sie den YARN-Gesamtarbeitsspeicher durch „mapreduce.map.memory“.

`# of YARN containers = total YARN memory / mapreduce.map.memory`

### <a name="step-5-set-mapreducejobmapsmapreducejobreduces"></a>Schritt 5: Festlegen von „mapreduce.job.maps/mapreduce.job.reduces“

Legen Sie „mapreduce.job.maps/mapreduce.job.reduces“ mindestens auf die Anzahl verfügbarer Container fest. Sie können weiter experimentieren, indem Sie die Anzahl von Mappern und Reducern erhöhen, um zu ermitteln, ob sich die Leistung verbessert. Denken Sie daran, dass eine größere Anzahl von Mappern zu weiterem Overhead führt und zu viele Mapper daher die Leistung beeinträchtigen können.

CPU-Planung und -Isolierung sind standardmäßig deaktiviert, sodass die Anzahl von YARN-Containern durch den Arbeitsspeicher begrenzt ist.

## <a name="example-calculation"></a>Beispielberechnung

Angenommen, Sie haben einen Cluster mit 8 D14-Knoten und möchten einen E/A-intensiven Auftrag ausführen. Dann müssen Sie folgende Berechnungen anstellen:

### <a name="step-1-determine-number-of-jobs-running"></a>Schritt 1: Ermitteln der Anzahl von ausgeführten Aufträgen

In unserem Beispiel gehen wir davon aus, dass unser Auftrag als einziger ausgeführt wird.

### <a name="step-2-set-mapreducemapmemorymapreducereducememory"></a>Schritt 2: Festlegen von „mapreduce.map.memory/mapreduce.reduce.memory“

Bei unserem Beispiel führen Sie einen E/A-intensiven Auftrag aus und entscheiden, dass 3 GB Arbeitsspeicher für Zuordnungsaufgaben ausreicht.

`mapreduce.map.memory = 3GB`

### <a name="step-3-determine-total-yarn-memory"></a>Schritt 3: Ermitteln des YARN-Gesamtarbeitsspeichers

`total memory from the cluster is 8 nodes * 96GB of YARN memory for a D14 = 768GB`

### <a name="step-4-calculate--of-yarn-containers"></a>Schritt 4: Berechnen der Anzahl von YARN-Containern

`# of YARN containers = 768 GB of available memory / 3 GB of memory = 256`

### <a name="step-5-set-mapreducejobmapsmapreducejobreduces"></a>Schritt 5: Festlegen von „mapreduce.job.maps/mapreduce.job.reduces“

`mapreduce.map.jobs = 256`

## <a name="limitations"></a>Einschränkungen

**Data Lake Storage Gen1-Einschränkung**

Azure Data Lake Storage Gen1 ist ein mehrinstanzenfähiger Dienst und legt daher Bandbreitengrenzwerte auf Kontoebene fest. Wenn Sie diese Grenzwerte erreichen, werden Taskfehler angezeigt. Dies lässt sich durch Beobachten der Drosselungsfehler in den Taskprotokollen ermitteln. Wenn Sie für einen Auftrag mehr Bandbreite benötigen, wenden Sie sich an uns.

Um zu prüfen, ob eine Drosselung vorliegt, müssen Sie die Debugprotokollierung auf Clientseite aktivieren. Gehen Sie hierzu wie folgt vor:

1. Fügen Sie die folgende Eigenschaft in die log4j-Eigenschaften unter „Ambari > YARN > Config > Advanced yarn-log4j“ ein: log4j.logger.com.microsoft.azure.datalake.store=DEBUG.

2. Starten Sie alle Knoten und Dienste neu, damit die Konfiguration wirksam wird.

3. Wenn eine Drosselung vorliegt, wird in der YARN-Protokolldatei ein HTTP 429-Fehlercode angezeigt. Die YARN-Protokolldatei befindet sich hier: /tmp/&lt;Benutzer&gt;/yarn.log

## <a name="examples-to-run"></a>Beispiele für die Ausführung

Zur Veranschaulichung, wie MapReduce in Data Lake Storage Gen1 ausgeführt wird, finden Sie hier Beispielcode, der mit den folgenden Einstellungen in einem Cluster ausgeführt wurde:

* D14v2 mit 16 Knoten
* Hadoop-Cluster mit HDI 3.6

Im Folgenden finden Sie einige Beispielbefehle zum Ausführen von MapReduce Teragen, Terasort und Teravalidate. Sie können diese Befehle basierend auf Ihren Ressourcen anpassen.

### <a name="teragen"></a>Teragen

```
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 10000000000 adl://example/data/1TB-sort-input
```

### <a name="terasort"></a>Terasort

```
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 -Dmapreduce.job.reduces=512 -Dmapreduce.reduce.memory.mb=3072 adl://example/data/1TB-sort-input adl://example/data/1TB-sort-output
```

### <a name="teravalidate"></a>Teravalidate

```
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapreduce.job.maps=512 -Dmapreduce.map.memory.mb=3072 adl://example/data/1TB-sort-output adl://example/data/1TB-sort-validate
```