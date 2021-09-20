---
title: Azure Storage-Lösungen für ML Services in HDInsight – Azure
description: Lernen Sie die verschiedenen Speicheroptionen kennen, die mit ML Services in HDInsight zur Verfügung stehen.
ms.service: hdinsight
ms.topic: how-to
ms.date: 01/02/2020
ROBOTS: NOINDEX
ms.openlocfilehash: 56b7181cafc9a17c2fdb468e1a47d664499dcdcc
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/31/2021
ms.locfileid: "123252907"
---
# <a name="azure-storage-solutions-for-ml-services-on-azure-hdinsight"></a>Azure Storage-Lösungen für ML Services in Azure HDInsight

[!INCLUDE [retirement banner](../includes/ml-services-retirement.md)]

ML Services in HDInsight kann verschiedene Speicherlösungen zum dauerhaften Speichern von Daten, Codes oder Objekten, die Ergebnisse aus der Analyse enthalten, verwenden. Diese Lösungen umfassen die folgenden Optionen:

- [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/)
- [Azure Data Lake Storage Gen1](https://azure.microsoft.com/services/storage/data-lake-storage/)
- [Azure Files](https://azure.microsoft.com/services/storage/files/)

Sie haben auch die Möglichkeit, mit Ihrem HDInsight-Cluster auf mehrere Azure-Speicherkonten oder -Container zuzugreifen. Azure Files ist eine komfortable Datenspeicheroption zur Verwendung auf dem Edgeknoten, mit der Sie eine Azure-Dateifreigabe z. B. auf einem Linux-Dateisystem bereitstellen können. Die Dateifreigaben können mit Azure File jedoch von allen Systemen mit einem unterstützten Betriebssystem, z. B. Windows oder Linux, bereitgestellt und verwendet werden.

Bei der Erstellung eines Apache Hadoop-Clusters in HDInsight geben Sie entweder ein **Azure-Blobspeicher**-Konto oder einen **Data Lake Storage Gen1** an. Ein spezieller Speichercontainer dieses Kontos enthält das Dateisystem für den von Ihnen erstellten Cluster (z.B. das Hadoop Distributed File System). Weitere Informationen und Anleitungen finden Sie unter:

- [Verwenden von Azure Blob Storage mit HDInsight](../hdinsight-hadoop-use-blob-storage.md)
- [Verwenden von Data Lake Storage Gen1 mit Azure HDInsight-Clustern](../hdinsight-hadoop-use-data-lake-storage-gen1.md)

## <a name="use-azure-blob-storage-accounts-with-ml-services-cluster"></a>Verwenden von Azure Blob Storage-Konten mit ML Services-Clustern

Wenn Sie bei der Erstellung Ihres ML Services-Clusters mehr als ein Speicherkonto angegeben haben, wird in den folgenden Anweisungen erläutert, wie Sie ein sekundäres Konto für den Datenzugriff und die Vorgänge auf dem ML Services-Cluster verwenden können. Nehmen wir die folgenden Speicherkonten und Container an: **storage1** und einen Standardcontainer namens **container1** sowie **storage2** mit **container2**.

> [!WARNING]  
> Aus Leistungsgründen wird der HDInsight-Cluster in demselben Rechenzentrum wie das von Ihnen angegebene primäre Speicherkonto erstellt. Die Verwendung eines Speicherkontos an einem anderen Ort als dem HDInsight-Cluster wird nicht unterstützt.

### <a name="use-the-default-storage-with-ml-services-on-hdinsight"></a>Verwenden des Standardspeichers mit ML Services in HDInsight

1. Stellen Sie mit einem SSH-Client eine Verbindung mit dem Edgeknoten Ihres Clusters her. Informationen zum Verwenden von SSH mit HDInsight-Clustern finden Sie unter [Verwenden von SSH mit HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).
  
2. Kopieren Sie die Beispieldatei „mysamplefile.csv“in das Verzeichnis „/share“.

    ```bash
    hadoop fs –mkdir /share
    hadoop fs –copyFromLocal mycsv.scv /share
    ```

3. Wechseln Sie zu R Studio oder einer anderen R-Konsole, und schreiben Sie R-Code, um den Namensknoten auf **default** und den Speicherort der Datei festzulegen, auf die Sie zugreifen möchten.  

    ```R
    myNameNode <- "default"
    myPort <- 0

    #Location of the data:  
    bigDataDirRoot <- "/share"  

    #Define Spark compute context:
    mySparkCluster <- RxSpark(nameNode=myNameNode, consoleOutput=TRUE)

    #Set compute context:
    rxSetComputeContext(mySparkCluster)

    #Define the Hadoop Distributed File System (HDFS) file system:
    hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

    #Specify the input file to analyze in HDFS:
    inputFile <-file.path(bigDataDirRoot,"mysamplefile.csv")
    ```

Alle Verzeichnis- und Dateiverweise zeigen auf das Speicherkonto `wasbs://container1@storage1.blob.core.windows.net`. Dies ist das **Standardspeicherkonto**, das dem HDInsight-Cluster zugeordnet ist.

### <a name="use-the-additional-storage-with-ml-services-on-hdinsight"></a>Verwenden des zusätzlichen Speichers mit ML Services in HDInsight

Angenommen, Sie möchten eine Datei mit dem Namen „mysamplefile1.csv“ verarbeiten, die im Verzeichnis „/private“ von **container2** in **storage2** enthalten ist.

Legen Sie den Namensknotenverweis im R-Code auf das Speicherkonto **storage2** fest.

```R
myNameNode <- "wasbs://container2@storage2.blob.core.windows.net"
myPort <- 0

#Location of the data:
bigDataDirRoot <- "/private"

#Define Spark compute context:
mySparkCluster <- RxSpark(consoleOutput=TRUE, nameNode=myNameNode, port=myPort)

#Set compute context:
rxSetComputeContext(mySparkCluster)

#Define HDFS file system:
hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

#Specify the input file to analyze in HDFS:
inputFile <-file.path(bigDataDirRoot,"mysamplefile1.csv")
```

Alle Verzeichnis- und Dateiverweise zeigen jetzt auf das Speicherkonto `wasbs://container2@storage2.blob.core.windows.net`. Dies ist der von Ihnen angegebene **Namensknoten**.

Konfigurieren Sie das Verzeichnis `/user/RevoShare/<SSH username>` unter **storage2** wie folgt:

```bash
hadoop fs -mkdir wasbs://container2@storage2.blob.core.windows.net/user
hadoop fs -mkdir wasbs://container2@storage2.blob.core.windows.net/user/RevoShare
hadoop fs -mkdir wasbs://container2@storage2.blob.core.windows.net/user/RevoShare/<RDP username>
```

## <a name="use-azure-data-lake-storage-gen1-with-ml-services-cluster"></a>Verwenden einer Azure Data Lake Storage Gen1-Instanz mit einem ML Services-Cluster

Zum Verwenden von Data Lake Storage Gen1 mit Ihrem HDInsight-Cluster müssen Sie Ihrem Cluster Zugriff auf jede Azure Data Lake Storage Gen1-Instanz gewähren, die Sie verwenden möchten. Anleitungen zur Verwendung des Azure-Portals zum Erstellen eines HDInsight-Clusters mit einem Azure Data Lake Storage Gen1 als Standardspeicher oder als zusätzlichen Speicher finden Sie unter [Erstellen eines HDInsight-Clusters mit Data Lake Storage Gen1 mithilfe des Azure-Portals](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

Nun verwenden Sie den Speicher im R-Skript so ähnlich wie ein sekundäres Azure-Speicherkonto (wie im vorherigen Verfahren beschrieben).

### <a name="add-cluster-access-to-your-azure-data-lake-storage-gen1"></a>Hinzufügen von Clusterzugriff auf Ihr Azure Data Lake Storage Gen1

Sie greifen auf Data Lake Storage Gen1 zu, indem Sie einen Azure AD-Dienstprinzipal (Azure Active Directory) verwenden, der Ihrem HDInsight-Cluster zugeordnet ist.

1. Wählen Sie beim Erstellen des HDInsight-Clusters auf der Registerkarte **Datenquelle** eine **Azure AD-Identität für den Cluster** aus.

2. Wählen Sie im Dialogfeld **Azure AD-Identität für den Cluster** unter **AD-Dienstprinzipal auswählen** die Option **Neu erstellen** aus.

Nachdem Sie dem Dienstprinzipal einen Namen gegeben und ein Kennwort dafür erstellt haben, klicken Sie auf **ADLS-Zugriff verwalten**, um den Dienstprinzipal Ihrer Data Lake Storage-Instanz zuzuordnen.

Sie können nach der Clustererstellung Clusterzugriff auf ein oder mehrere Data Lake Storage Gen1-Konten hinzufügen. Öffnen Sie den Azure-Portal-Eintrag für eine Data Lake Storage Gen1-Instanz, und wechseln Sie zu **Daten-Explorer > Zugriff > Hinzufügen**.

### <a name="how-to-access-data-lake-storage-gen1-from-ml-services-on-hdinsight"></a>Zugreifen auf Data Lake Storage Gen1 mit ML Services in HDInsight

Nachdem Sie Zugriff auf eine Data Lake Storage Gen1-Instanz gewährt haben, können Sie den Speicher im ML Services-Cluster auf HDInsight wie ein sekundäres Azure Storage-Konto nutzen. Der einzige Unterschied ist, dass sich das Präfix **wasbs://** wie folgt in **adl://** ändert:

```R
# Point to the ADL Storage (e.g. ADLtest)
myNameNode <- "adl://rkadl1.azuredatalakestore.net"
myPort <- 0

# Location of the data (assumes a /share directory on the ADL account)
bigDataDirRoot <- "/share"  

# Define Spark compute context
mySparkCluster <- RxSpark(consoleOutput=TRUE, nameNode=myNameNode, port=myPort)

# Set compute context
rxSetComputeContext(mySparkCluster)

# Define HDFS file system
hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

# Specify the input file in HDFS to analyze
inputFile <-file.path(bigDataDirRoot,"mysamplefile.csv")
```

Die folgenden Befehle werden zum Konfigurieren der Data Lake Storage Gen1-Instanz mit dem RevoShare-Verzeichnis und zum Hinzufügen der CSV-Beispieldatei aus dem vorherigen Beispiel verwendet:

```bash
hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user
hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare
hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare/<user>

hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/share

hadoop fs -copyFromLocal /usr/lib64/R Server-7.4.1/library/RevoScaleR/SampleData/mysamplefile.csv adl://rkadl1.azuredatalakestore.net/share

hadoop fs –ls adl://rkadl1.azuredatalakestore.net/share
```

## <a name="use-azure-files-with-ml-services-on-hdinsight"></a>Verwenden von Azure Files mit ML Services in HDInsight

Es gibt auch eine komfortable Datenspeicheroption zur Verwendung auf dem Edgeknoten mit der Bezeichnung [Azure Files](https://azure.microsoft.com/services/storage/files/). Hiermit können Sie eine Azure Storage-Dateifreigabe auf einem Linux-Dateisystem bereitstellen. Diese Option kann zum Speichern von Datendateien, R-Skripts und Ergebnisobjekten nützlich sein, die unter Umständen später benötigt werden, besonders wenn es sinnvoll ist, anstelle von HDFS das native Dateisystem auf dem Edgeknoten zu verwenden.

Ein großer Vorteil von Azure Files besteht darin, dass die Dateifreigaben von allen Systemen mit einem unterstützten Betriebssystem, z.B. Windows oder Linux, bereitgestellt und verwendet werden können. Beispielsweise ist die Verwendung durch einen anderen HDInsight-Cluster, der Ihnen oder einem Mitglied Ihres Teams gehört, eine Azure VM oder sogar ein lokales System möglich. Weitere Informationen finden Sie unter

- [How to use Azure Files with Linux (Verwenden von Azure Files mit Linux)](../../storage/files/storage-how-to-use-files-linux.md)
- [Verwenden von Azure Files auf Windows](../../storage/files/storage-dotnet-how-to-use-files.md)

## <a name="next-steps"></a>Nächste Schritte

- [Übersicht über ML Services-Cluster in HDInsight](r-server-overview.md)
- [Compute context options for ML Services on HDInsight](r-server-compute-contexts.md) (Computekontextoptionen für ML Services in HDInsight)
- [Verwenden von Azure Data Lake Storage Gen2 mit Azure HDInsight-Clustern](../hdinsight-hadoop-use-data-lake-storage-gen2.md)
