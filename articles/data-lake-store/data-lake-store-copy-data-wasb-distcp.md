---
title: Kopieren von Daten in und aus WASB in Azure Data Lake Storage Gen1 mit DistCp
description: Verwenden des Tools DistCp zum Kopieren von Daten zwischen Azure Storage-Blobs und Azure Data Lake Storage Gen1
author: normesta
ms.service: data-lake-store
ms.topic: how-to
ms.date: 01/03/2020
ms.author: normesta
ms.openlocfilehash: 5e1f4cda87bbc73218826e76e3e11760c31dc159
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128637432"
---
# <a name="use-distcp-to-copy-data-between-azure-storage-blobs-and-azure-data-lake-storage-gen1"></a>Kopieren von Daten zwischen Azure Storage-Blobs und Azure Data Lake Storage Gen1 mit DistCp

> [!div class="op_single_selector"]
> * [Verwenden von DistCp](data-lake-store-copy-data-wasb-distcp.md)
> * [Verwenden von AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)
>
>

Bei einem HDInsight-Cluster mit Zugriff auf Azure Data Lake Storage Gen1 können Sie Tools aus dem Hadoop-Ökosystem wie DistCp verwenden, um Daten aus einem HDInsight-Clusterspeicher (WASB) in ein Data Lake Storage Gen1-fähiges Konto zu kopieren (und umgekehrt). Dieser Artikel beschreibt die Verwendung des Tools DistCp.

## <a name="prerequisites"></a>Voraussetzungen

* **Ein Azure-Abonnement**. Siehe [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/).
* **Ein Azure Data Lake Storage Gen1-Konto**. Eine Anleitung zur Erstellung finden Sie unter [Erste Schritte mit Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md).
* **Ein Azure HDInsight-Cluster** mit Zugriff auf ein Data Lake Storage Gen1-Konto. Weitere Informationen finden Sie unter [Erstellen eines HDInsight-Clusters mit Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md). Stellen Sie sicher, dass Remotedesktop für den Cluster aktiviert ist.

## <a name="use-distcp-from-an-hdinsight-linux-cluster"></a>Verwenden von DistCp in einem HDInsight-Linux-Cluster

Ein HDInsight-Cluster bietet das Tool DistCp, das zum Kopieren von Daten aus verschiedenen Quellen in einen HDInsight-Cluster verwendet werden kann. Wenn Sie den HDInsight-Cluster so konfiguriert haben, dass er Data Lake Storage Gen1 als zusätzlichen Speicher verwendet, können Sie DistCp ebenfalls direkt zum Kopieren von Daten in ein bzw. aus einem Data Lake Storage Gen1-Konto nutzen. In diesem Abschnitt wird die Verwendung des Tools DistCp erläutert.

1. Verwenden Sie SSH auf Ihrem Desktop, um eine Verbindung mit dem Cluster herzustellen. Informationen hierzu finden Sie unter [Verbinden mit einem Linux-basierten HDInsight-Cluster](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md). Führen Sie die Befehle von der SSH-Eingabeaufforderung aus.

1. Überprüfen Sie, ob Sie auf die Azure Storage-Blobs (WASB) zugreifen können. Führen Sie den folgenden Befehl aus:

   ```
   hdfs dfs –ls wasb://<container_name>@<storage_account_name>.blob.core.windows.net/
   ```

   Die Ausgabe enthält eine Liste der Inhalte im Speicherblob.

1. Überprüfen Sie zudem, ob Sie vom Cluster auf das Data Lake Storage Gen1-Konto zugreifen können. Führen Sie den folgenden Befehl aus:

   ```
   hdfs dfs -ls adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/
   ```

    Die Ausgabe stellt eine Liste der Dateien und Ordner im Data Lake Storage Gen1-Konto bereit.

1. Verwenden Sie DistCp zum Kopieren von Daten aus WASB in ein Data Lake Storage Gen1-Konto.

   ```
   hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder
   ```

    Der Befehl kopiert den Inhalt des Ordners **/example/data/gutenberg/** in WASB in den Ordner **/myfolder** im Data Lake Storage Gen1-Konto.

1. Verwenden Sie DistCp ebenso zum Kopieren von Daten aus einem Data Lake Storage Gen1-Konto nach WASB.

   ```
   hadoop distcp adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg
   ```

    Der Befehl kopiert den Inhalt des Ordners **/myfolder** im Data Lake Storage Gen1-Konto in den Ordner **/example/data/gutenberg/** in WASB.

## <a name="performance-considerations-while-using-distcp"></a>Überlegungen zur Leistung bei der Verwendung von DistCp

Da die kleinste Granularitätseinheit von DistCp eine einzelne Datei ist, stellt die maximale Anzahl gleichzeitiger Kopien den wichtigsten Parameter für die Data Lake Storage Gen1-Optimierung dar. Sie können die Anzahl gleichzeitiger Kopien steuern, indem Sie den Parameter für die Anzahl von Zuordnungen (m) über die Befehlszeile festlegen. Dieser Parameter gibt die maximale Anzahl von Zuordnungen an, die zum Kopieren von Daten verwendet werden. Der Standardwert lautet 20.

Beispiel:

```
 hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_storage_gen1_account>.azuredatalakestore.net:443/myfolder -m 100
```

### <a name="how-to-determine-the-number-of-mappers-to-use"></a>Wie bestimme ich die Anzahl der zu verwendenden Zuordnungen?

Hier sind einige hilfreiche Informationen zur Vorgehensweise angegeben.

* **Schritt 1: Bestimmen des YARN-Gesamtarbeitsspeichers** – Zunächst muss der YARN-Arbeitsspeicher bestimmt werden, der für den Cluster zur Verfügung steht, in dem Sie den DistCp-Auftrag ausführen. Diese Information finden Sie im dem Cluster zugeordneten Ambari-Portal. Navigieren Sie zu YARN, und zeigen Sie die Registerkarte **Configs** (Konfigurationen) an, um den YARN-Arbeitsspeicher zu ermitteln. Um den YARN-Gesamtarbeitsspeicher zu erhalten, multiplizieren Sie den YARN-Arbeitsspeicher pro Knoten mit der Anzahl von Knoten in Ihrem Cluster.

* **Schritt 2: Berechnen der Anzahl von Zuordnungen**: Der Wert von **m** entspricht dem Quotienten des YARN-Gesamtarbeitsspeichers dividiert durch die YARN-Containergröße. Die YARN-Containergröße finden Sie auch im Ambari-Portal. Navigieren Sie zu YARN, und zeigen Sie die Registerkarte **Configs** an. Die YARN-Containergröße wird in diesem Fenster angezeigt. Die Formel zur Berechnung der Anzahl von Zuordnungen (**m**) sieht wie folgt aus:

   `m = (number of nodes * YARN memory for each node) / YARN container size`

Beispiel:

Angenommen, Sie verfügen über einen Cluster mit vier D14v2-Knoten und möchten 10 TB an Daten aus zehn unterschiedlichen Ordnern übertragen. Jeder Ordner enthält unterschiedliche Datenmengen, und auch die Dateigrößen in den einzelnen Ordnern sind unterschiedlich.

* YARN-Gesamtarbeitsspeicher: Über das Ambari-Portal ermitteln Sie, dass der YARN-Arbeitsspeicher für einen D14-Knoten 96 GB beträgt. Für einen Cluster mit vier Knoten beträgt der YARN-Gesamtarbeitsspeicher demnach:

   `YARN memory = 4 * 96GB = 384GB`

* Anzahl von Zuordnungen: Über das Ambari-Portal ermitteln Sie, dass die YARN-Containergröße für einen D14-Clusterknoten 3072 beträgt. Die Anzahl von Zuordnungen beträgt somit:

   `m = (4 nodes * 96GB) / 3072MB = 128 mappers`

Wenn andere Anwendungen Arbeitsspeicher verwenden, können Sie festlegen, dass für DistCp nur ein Teil des YARN-Arbeitsspeichers Ihres Clusters verwendet werden soll.

### <a name="copying-large-datasets"></a>Kopieren umfangreicher Datasets

Wenn das zu verschiebende Dataset sehr groß ist (beispielsweise > 1 TB) oder Sie über zahlreiche unterschiedliche Ordner verfügen, empfiehlt sich unter Umständen die Verwendung mehrerer DistCp-Aufträge. Dadurch ergibt sich zwar wahrscheinlich kein Leistungsgewinn, die Aufträge werden jedoch verteilt, sodass im Falle eines Fehlers bei einem Auftrag nicht das gesamte Projekt, sondern lediglich der betroffene Auftrag neu gestartet werden muss.

### <a name="limitations"></a>Einschränkungen

* Zur Optimierung der Leistung versucht DistCp, Zuordnungen mit ähnlicher Größe zu erstellen. Eine höhere Anzahl von Zuordnungen führt nicht unbedingt zu mehr Leistung.

* DistCp ist auf eine einzelne Zuordnung pro Datei beschränkt. Aus diesem Grund sollte die Anzahl von Zuordnungen die Anzahl von Dateien nicht überschreiten. Der Umstand, dass DistCp einer Datei nur eine einzelne Zuordnung zuweisen kann, beschränkt den Umfang der Parallelität, die beim Kopieren großer Dateien verwendet werden kann.

* Wenn Sie über eine geringe Anzahl großer Dateien verfügen, teilen Sie diese in Dateiblöcke mit jeweils 256 MB auf, um das Parallelitätspotenzial zu erhöhen.

* Beim Kopieren aus einem Azure Blob Storage-Konto wird Ihr Kopierauftrag unter Umständen auf der Blob Storage-Seite gedrosselt. Dies beeinträchtigt die Leistung Ihres Kopierauftrags. Weitere Informationen zu den Einschränkungen von Azure Blob Storage finden Sie unter [Einschränkungen von Azure-Abonnements und Diensten](../azure-resource-manager/management/azure-subscription-service-limits.md).

## <a name="see-also"></a>Weitere Informationen

* [Kopieren von Daten aus Azure Storage-Blobs in Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)
* [Schützen von Daten in Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Verwenden von Azure Data Lake Analytics mit Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Erstellen von HDInsight-Clustern mithilfe von Azure Data Lake Storage Gen1 im Azure-Portal](data-lake-store-hdinsight-hadoop-use-portal.md)
