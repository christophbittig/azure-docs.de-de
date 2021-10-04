---
title: 'Transformieren von Daten mit Spark in Azure Data Factory '
description: Dieses Tutorial enthält Schritt-für-Schritt-Anleitungen zum Transformieren von Daten mithilfe einer Spark-Aktivität in Azure Data Factory.
ms.service: data-factory
ms.subservice: tutorials
ms.topic: tutorial
author: nabhishek
ms.author: abnarain
ms.date: 06/07/2021
ms.openlocfilehash: 22b6aad6d57fd2dc6e53313ea65a1813781ead9f
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124769672"
---
# <a name="transform-data-in-the-cloud-by-using-a-spark-activity-in-azure-data-factory"></a>Transformieren von Daten in der Cloud mithilfe einer Spark-Aktivität in Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In diesem Tutorial verwenden Sie das Azure-Portal, um eine Azure Data Factory-Pipeline zu erstellen. In dieser Pipeline werden Daten transformiert, indem eine Spark-Aktivität und ein bedarfsabhängiger verknüpfter Azure HDInsight-Dienst verwendet wird. 

In diesem Tutorial führen Sie die folgenden Schritte aus:

> [!div class="checklist"]
> * Erstellen einer Data Factory. 
> * Erstellen Sie eine Pipeline, für die eine Spark-Aktivität verwendet wird.
> * Auslösen einer Pipelineausführung
> * Überwachen der Pipelineausführung.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* **Azure-Speicherkonto**. Sie erstellen ein Python-Skript und eine Eingabedatei und laden diese in Azure Storage hoch. Die Ausgabe des Spark-Programms wird in diesem Speicherkonto gespeichert. Der bedarfsgesteuerte Spark-Cluster verwendet dieses Storage-Konto als primären Speicher.  

> [!NOTE]
> HdInsight unterstützt nur universelle Speicherkonten mit dem Standard-Tarif. Stellen Sie sicher, dass das Konto kein Premium- oder ausschließlich für Blobs vorgesehenes Speicherkonto ist.

* **Azure PowerShell**. Befolgen Sie die Anweisungen unter [Get started with Azure PowerShell cmdlets](/powershell/azure/install-Az-ps) (Erste Schritte mit Azure PowerShell-Cmdlets).


### <a name="upload-the-python-script-to-your-blob-storage-account"></a>Hochladen des Python-Skripts in Ihr BLOB-Speicherkonto
1. Erstellen Sie eine Python-Datei mit dem Namen **WordCount_Spark.py** und dem folgenden Inhalt: 

    ```python
    import sys
    from operator import add
    
    from pyspark.sql import SparkSession
    
    def main():
        spark = SparkSession\
            .builder\
            .appName("PythonWordCount")\
            .getOrCreate()
            
        lines = spark.read.text("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/inputfiles/minecraftstory.txt").rdd.map(lambda r: r[0])
        counts = lines.flatMap(lambda x: x.split(' ')) \
            .map(lambda x: (x, 1)) \
            .reduceByKey(add)
        counts.saveAsTextFile("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/outputfiles/wordcount")
        
        spark.stop()
    
    if __name__ == "__main__":
        main()
    ```
1. Ersetzen Sie *&lt;storageAccountName&gt;* durch den Namen Ihres Azure-Speicherkontos. Speichern Sie dann die Datei. 
1. Erstellen Sie in Azure Blob Storage einen Container mit dem Namen **adftutorial**, falls dieser noch nicht vorhanden ist. 
1. Erstellen Sie einen Ordner mit dem Namen **spark**.
1. Erstellen Sie unterhalb des Ordners **spark** einen Unterordner mit dem Namen **script**. 
1. Laden Sie die Datei **WordCount_Spark.py** in den Unterordner **script** hoch. 


### <a name="upload-the-input-file"></a>Hochladen der Eingabedatei
1. Erstellen Sie eine Datei mit dem Namen **minecraftstory.txt** und etwas Text darin. Das Spark-Programm zählt die Wörter in diesem Text. 
1. Erstellen Sie im Ordner **spark** einen Unterordner mit dem Namen **inputfiles**. 
1. Laden Sie die Datei **minecraftstory.txt** in den Unterordner **inputfiles** hoch. 

## <a name="create-a-data-factory"></a>Erstellen einer Data Factory

1. Starten Sie den Webbrowser **Microsoft Edge** oder **Google Chrome**. Die Data Factory-Benutzeroberfläche wird zurzeit nur in den Webbrowsern Microsoft Edge und Google Chrome unterstützt.
1. Klicken Sie im linken Menü auf **Neu** und anschließend auf **Daten + Analysen** und **Data Factory**. 
   
   :::image type="content" source="./media/tutorial-transform-data-spark-portal/new-azure-data-factory-menu.png" alt-text="Auswahl von Data Factory im Bereich &quot;Neu&quot;":::
1. Geben Sie im Bereich **Neue Data Factory** unter **Name** den Namen **ADFTutorialDataFactory** ein. 
      
   :::image type="content" source="./media/tutorial-transform-data-spark-portal/new-azure-data-factory.png" alt-text="Bereich &quot;Neue Data Factory&quot;":::
 
   Der Name der Azure Data Factory muss *global eindeutig* sein. Ändern Sie den Namen der Data Factory, wenn die folgende Fehlermeldung angezeigt wird. (Verwenden Sie beispielsweise **&lt;IhrName&gt;ADFTutorialDataFactory**.) Benennungsregeln für Data Factory-Artefakte finden Sie im Artikel [Azure Data Factory – Benennungsregeln](naming-rules.md).
  
   :::image type="content" source="./media/tutorial-transform-data-spark-portal/name-not-available-error.png" alt-text="Fehler, wenn ein Name nicht verfügbar ist":::
1. Wählen Sie unter **Abonnement** Ihr Azure-Abonnement aus, in dem die Data Factory erstellt werden soll. 
1. Führen Sie unter **Ressourcengruppe** einen der folgenden Schritte aus:
     
   - Wählen Sie die Option **Use existing**(Vorhandene verwenden) und dann in der Dropdownliste eine vorhandene Ressourcengruppe. 
   - Wählen Sie **Neu erstellen**, und geben Sie den Namen einer Ressourcengruppe ein.   
         
   Bei einigen Schritten dieses Tutorials wird davon ausgegangen, dass Sie eine Ressourcengruppe mit dem Namen **ADFTutorialResourceGroup** verwenden. Weitere Informationen über Ressourcengruppen finden Sie unter [Verwenden von Ressourcengruppen zum Verwalten von Azure-Ressourcen](../azure-resource-manager/management/overview.md).  
1. Wählen Sie **V2** als **Version** aus.
1. Wählen Sie unter **Standort** den Standort für die Data Factory aus. 

   Eine Liste der Azure-Regionen, in denen Data Factory derzeit verfügbar ist, finden Sie, indem Sie die für Sie interessanten Regionen auf der folgenden Seite auswählen und dann **Analysen** erweitern, um **Data Factory** zu finden: [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/). Die von Data Factory verwendeten Datenspeicher (etwa Azure Storage und Azure SQL-Datenbank) und Computedienste (etwa HDInsight) können sich in anderen Regionen befinden.

1. Klicken Sie auf **Erstellen**.

1. Nach Abschluss der Erstellung wird die Seite **Data Factory** angezeigt. Klicken Sie auf die Kachel **Erstellen und überwachen**, um die Anwendung für die Data Factory-Benutzeroberfläche auf einer separaten Registerkarte zu starten.

    :::image type="content" source="./media/tutorial-transform-data-spark-portal/data-factory-home-page.png" alt-text="Startseite der Data Factory mit der Kachel &quot;Erstellen und überwachen&quot;":::

## <a name="create-linked-services"></a>Erstellen von verknüpften Diensten
In diesem Abschnitt erstellen Sie zwei verknüpfte Dienste: 
    
- Einen **verknüpften Azure Storage-Dienst**, der ein Azure-Speicherkonto mit der Data Factory verknüpft. Dieser Speicher wird vom bedarfsgesteuerten HDInsight-Cluster verwendet. Er enthält auch das Spark-Skript, das ausgeführt werden soll. 
- Einen **bedarfsgesteuerten verknüpften HDInsight-Dienst**. Azure Data Factory erstellt automatisch einen HDInsight-Cluster und führt das Spark-Programm aus. Anschließend wird der HDInsight-Cluster gelöscht, nachdem er sich für einen vorkonfigurierten Zeitraum im Leerlauf befunden hat. 

### <a name="create-an-azure-storage-linked-service"></a>Erstellen eines verknüpften Azure Storage-Diensts

1. Wechseln Sie auf der Startseite im linken Bereich zur Registerkarte **Verwalten**. 

   :::image type="content" source="media/doc-common-process/get-started-page-manage-button.png" alt-text="Screenshot mit der Registerkarte „Verwalten“.":::

1. Wählen Sie unten im Fenster die Option **Verbindungen** und dann **+ Neu**. 

   :::image type="content" source="./media/tutorial-transform-data-spark-portal/new-connection.png" alt-text="Schaltflächen zum Erstellen einer neuen Verbindung":::
1. Wählen Sie im Fenster **New Linked Service** (Neuer verknüpfter Dienst) die Option **Datenspeicher** > **Azure Blob Storage** und dann **Weiter**. 

   :::image type="content" source="./media/tutorial-transform-data-spark-portal/select-azure-storage.png" alt-text="Auswählen der Kachel &quot;Azure Blob Storage&quot;":::
1. Wählen Sie für **Speicherkontoname** den Namen in der Liste aus, und wählen Sie anschließend die Option **Speichern**. 

   :::image type="content" source="./media/tutorial-transform-data-spark-portal/new-azure-storage-linked-service.png" alt-text="Feld zum Angeben des Speicherkontonamens":::


### <a name="create-an-on-demand-hdinsight-linked-service"></a>Erstellen eines bedarfsgesteuerten verknüpften HDInsight-Diensts

1. Wählen Sie erneut die Schaltfläche **+ Neu**, um einen weiteren verknüpften Dienst zu erstellen. 
1. Wählen Sie im Fenster **New Linked Service** (Neuer verknüpfter Dienst) die Option **Compute** > **Azure HDInsight** und dann **Weiter**. 

   :::image type="content" source="./media/tutorial-transform-data-spark-portal/select-azure-hdinsight.png" alt-text="Auswählen der Kachel &quot;Azure HDInsight&quot;":::
1. Führen Sie im Fenster **New Linked Service** (Neuer verknüpfter Dienst) die folgenden Schritte aus: 

   a. Geben Sie unter **Name** die Zeichenfolge **AzureHDInsightLinkedService** ein.
   
   b. Vergewissern Sie sich, dass unter **Typ** die Option **On-demand HDInsight** (HDInsight (bedarfsgesteuert)) ausgewählt ist.
   
   c. Wählen Sie unter **Mit Azure Storage verknüpfter Dienst** die Option **AzureBlobStorage1** aus. Diesen verknüpften Dienst haben Sie in einem früheren Schritt erstellt. Sollten Sie einen anderen Namen verwendet haben, geben Sie hier stattdessen diesen Namen ein. 
   
   d. Wählen Sie unter **Clustertyp** die Option **spark**.
   
   e. Geben Sie unter **Dienstprinzipal-ID** die ID des Dienstprinzipals ein, der zum Erstellen eines HDInsight-Clusters berechtigt ist. 
   
      Dieser Dienstprinzipal muss Mitglied der Rolle „Mitwirkender“ in dem Abonnement oder der Ressourcengruppe sein, in dem bzw. der der Cluster erstellt wird. Weitere Informationen finden Sie unter [Erstellen einer Azure Active Directory-Anwendung und eines Dienstprinzipals mit Ressourcenzugriff mithilfe des Portals](../active-directory/develop/howto-create-service-principal-portal.md). Die **Dienstprinzipal-ID** entspricht der *Anwendungs-ID* und ein **Dienstprinzipalschlüssel** dem Wert für ein *Clientgeheimnis*.
   
   f. Geben Sie unter **Dienstprinzipalschlüssel** den Schlüssel ein. 
   
   g. Wählen Sie unter **Ressourcengruppe** dieselbe Ressourcengruppe aus, die Sie auch beim Erstellen der Data Factory verwendet haben. Der Spark-Cluster wird in dieser Ressourcengruppe erstellt. 
   
   h. Erweitern Sie **Betriebssystemtyp**.
   
   i. Geben Sie einen Namen für **Clusterbenutzername** ein. 
   
   j. Geben Sie das **Clusterkennwort** für den Benutzer ein. 
   
   k. Wählen Sie **Fertig stellen** aus. 

   :::image type="content" source="./media/tutorial-transform-data-spark-portal/azure-hdinsight-linked-service-settings.png" alt-text="Einstellungen für den verknüpften HDInsight-Dienst":::

> [!NOTE]
> Die Gesamtanzahl von Kernen, die Sie in jeder von Azure HDInsight unterstützten Azure-Region verwenden können, ist begrenzt. Beim bedarfsgesteuerten verknüpften HDInsight-Dienst wird der HDInsight-Cluster an demselben Azure Storage-Speicherort erstellt, der als primärer Speicher verwendet wird. Stellen Sie sicher, dass Sie über genügend Kernkontingente verfügen, sodass der Cluster erfolgreich erstellt werden kann. Weitere Informationen finden Sie unter [Einrichten von Clustern in HDInsight mit Hadoop, Spark, Kafka usw](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md). 

## <a name="create-a-pipeline"></a>Erstellen einer Pipeline

1. Klicken Sie auf die Schaltfläche **+** (Pluszeichen), und wählen Sie im Menü dann die Option **Pipeline**.

   :::image type="content" source="./media/tutorial-transform-data-spark-portal/new-pipeline-menu.png" alt-text="Schaltflächen zum Erstellen einer neuen Pipeline":::
1. Erweitern Sie in der Toolbox **Aktivitäten** die Option **HDInsight**. Ziehen Sie die **Spark**-Aktivität aus der Toolbox **Aktivitäten** auf die Oberfläche des Pipeline-Designers. 

   :::image type="content" source="./media/tutorial-transform-data-spark-portal/drag-drop-spark-activity.png" alt-text="Ziehen der Spark-Aktivität":::
1. Führen Sie in den Eigenschaften im unteren Bereich des Fensters für die **Spark**-Aktivität die folgenden Schritte aus: 

   a. Wechseln Sie zur Registerkarte **HDI Cluster** (HDI-Cluster).
   
   b. Wählen Sie den verknüpften Dienst **AzureHDInsightLinkedService** aus, den Sie im vorherigen Verfahren erstellt haben. 
        
   :::image type="content" source="./media/tutorial-transform-data-spark-portal/select-hdinsight-linked-service.png" alt-text="Angeben des verknüpften HDInsight-Diensts":::
1. Wechseln Sie zur Registerkarte **Script/Jar** (Skript/JAR), und führen Sie die folgenden Schritte aus: 

   a. Wählen Sie unter **Mit Auftrag verknüpfter Dienst** die Option **AzureBlobStorage1** aus.
   
   b. Wählen Sie **Speicher durchsuchen**.

   :::image type="content" source="./media/tutorial-transform-data-spark-portal/specify-spark-script.png" alt-text="Angeben des Spark-Skripts auf der Registerkarte &quot;Skript/JAR&quot;":::
   
   c. Navigieren Sie zum Ordner **adftutorial/spark/script**, wählen Sie **WordCount_Spark.py** aus, und wählen Sie dann die Option **Fertig stellen**.      

1. Wählen Sie zum Überprüfen der Pipeline in der Symbolleiste die Schaltfläche **Überprüfen**. Wählen Sie die Schaltfläche **>>** (Pfeil nach rechts), um das Überprüfungsfenster zu schließen. 
    
   :::image type="content" source="./media/tutorial-transform-data-spark-portal/validate-button.png" alt-text="Schaltfläche &quot;Überprüfen&quot;":::
1. Wählen Sie **Alle veröffentlichen**. Die Data Factory-Benutzeroberfläche veröffentlicht Entitäten (verknüpfte Dienste und Pipeline) für den Azure Data Factory-Dienst. 
    
   :::image type="content" source="./media/tutorial-transform-data-spark-portal/publish-button.png" alt-text="Schaltfläche &quot;Alle veröffentlichen&quot;":::


## <a name="trigger-a-pipeline-run"></a>Auslösen einer Pipelineausführung
Wählen Sie in der Symbolleiste die Option **Trigger hinzufügen** und dann **Jetzt auslösen**. 

:::image type="content" source="./media/tutorial-transform-data-spark-portal/trigger-now-menu.png" alt-text="Schaltflächen &quot;Trigger&quot; und &quot;Jetzt auslösen&quot;":::

## <a name="monitor-the-pipeline-run"></a>Überwachen der Pipelineausführung

1. Wechseln Sie zur Registerkarte **Überwachen**. Vergewissern Sie sich, dass eine Pipelineausführung angezeigt wird. Die Erstellung eines Spark-Clusters dauert etwa 20 Minuten. 
   
1. Wählen Sie von Zeit zu Zeit die Option **Aktualisieren**, um den Status der Pipelineausführung zu überprüfen. 

   :::image type="content" source="./media/tutorial-transform-data-spark-portal/monitor-tab.png" alt-text="Registerkarte zum Überwachen von Pipelineausführungen mit der Schaltfläche &quot;Aktualisieren&quot;":::

1. Wenn Sie mit der Pipelineausführung verknüpfte Aktivitätsausführungen anzeigen möchten, wählen Sie in der Spalte **Aktionen** die Optionen **View Activity Runs** (Aktivitätsausführungen anzeigen).

   :::image type="content" source="./media/tutorial-transform-data-spark-portal/pipeline-run-succeeded.png" alt-text="Status der Pipelineausführung"::: 

   Durch Wählen des Links **Alle Pipelineausführungen** im oberen Bereich können Sie zur Ansicht mit den Pipelineausführungen zurückkehren.

   :::image type="content" source="./media/tutorial-transform-data-spark-portal/activity-runs.png" alt-text="Ansicht &quot;Aktivitätsausführungen&quot;":::

## <a name="verify-the-output"></a>Überprüfen der Ausgabe
Vergewissern Sie sich, dass die Ausgabedatei im Ordner „spark/outputfiles/wordcount“ des Containers „adftutorial“ erstellt wird. 

:::image type="content" source="./media/tutorial-transform-data-spark-portal/verity-output.png" alt-text="Speicherort der Ausgabedatei":::

Die Datei sollte für jedes Wort aus der Eingabetextdatei angeben, wie oft es in der Datei vorkommt. Beispiel: 

```
(u'This', 1)
(u'a', 1)
(u'is', 1)
(u'test', 1)
(u'file', 1)
```

## <a name="next-steps"></a>Nächste Schritte
Die Pipeline in diesem Beispiel transformiert Daten mithilfe einer Spark-Aktivität und eines bedarfsgesteuerten verknüpften HDInsight-Diensts. Sie haben Folgendes gelernt: 

> [!div class="checklist"]
> * Erstellen einer Data Factory. 
> * Erstellen Sie eine Pipeline, für die eine Spark-Aktivität verwendet wird.
> * Auslösen einer Pipelineausführung
> * Überwachen der Pipelineausführung.

Fahren Sie mit dem nächsten Tutorial fort, um zu erfahren, wie Sie Daten transformieren, indem Sie ein Hive-Skript in einem Azure HDInsight-Cluster ausführen, der sich in einem virtuellen Netzwerk befindet: 

> [!div class="nextstepaction"]
> [Tutorial: Transformieren von Daten mithilfe von Hive in Azure Virtual Network](tutorial-transform-data-hive-virtual-network-portal.md)





