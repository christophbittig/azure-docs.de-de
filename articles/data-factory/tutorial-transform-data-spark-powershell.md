---
title: 'Transformieren von Daten mit Spark in Azure Data Factory '
description: Dieses Tutorial bietet Schrittanleitungen zum Transformieren von Daten mithilfe einer Spark-Aktivität in Azure Data Factory.
ms.service: data-factory
ms.subservice: tutorials
ms.topic: tutorial
ms.date: 01/22/2018
author: nabhishek
ms.author: abnarain
ms.openlocfilehash: ec69d0d4afe96a79e421a5a89d38b2f59cd3a796
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121740933"
---
# <a name="transform-data-in-the-cloud-by-using-spark-activity-in-azure-data-factory"></a>Transformieren von Daten in der Cloud mithilfe einer Spark-Aktivität in Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In diesem Tutorial verwenden Sie Azure PowerShell, um eine Data Factory-Pipeline zu erstellen, die Daten mithilfe einer Spark-Aktivität und einem bedarfsgesteuerten verknüpften HDInsight-Dienst transformiert. In diesem Tutorial führen Sie die folgenden Schritte aus:

> [!div class="checklist"]
> * Erstellen einer Data Factory. 
> * Erstellen und Bereitstellen von verknüpften Diensten
> * Erstellen und Bereitstellen einer Pipeline. 
> * Starten einer Pipelineausführung
> * Überwachen der Pipelineausführung.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* **Azure Storage-Konto**. Sie erstellen ein Python-Skript und eine Eingabedatei und laden diese in Azure Storage hoch. Die Ausgabe des Spark-Programms wird in diesem Storage-Konto gespeichert. Der bedarfsgesteuerte Spark-Cluster verwendet dieses Storage-Konto als primären Speicher.  
* **Azure PowerShell**. Befolgen Sie die Anweisungen unter [Get started with Azure PowerShell cmdlets](/powershell/azure/install-Az-ps) (Erste Schritte mit Azure PowerShell-Cmdlets).


### <a name="upload-python-script-to-your-blob-storage-account"></a>Hochladen eines Python-Skripts in Ihr Blob Storage-Konto
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
2. Ersetzen Sie **&lt;storageAccountName&gt;** durch den Namen Ihres Azure Storage-Kontos. Speichern Sie dann die Datei. 
3. Erstellen Sie in Azure Blob Storage einen Container mit dem Namen **adftutorial**, falls dieser noch nicht vorhanden ist. 
4. Erstellen Sie einen Ordner mit dem Namen **spark**.
5. Erstellen Sie unterhalb des Ordners **spark** einen Unterordner mit dem Namen **script**. 
6. Laden Sie die Datei **WordCount_Spark.py** in den Unterordner **script** hoch. 


### <a name="upload-the-input-file"></a>Hochladen der Eingabedatei
1. Erstellen Sie eine Datei mit dem Namen **minecraftstory.txt** und etwas Text darin. Das Spark-Programm zählt die Wörter in diesem Text. 
2. Erstellen Sie im Ordner `spark` einen Unterordner mit dem Namen `inputfiles`. 
3. Laden Sie `minecraftstory.txt` in den Unterordner `inputfiles` hoch. 

## <a name="author-linked-services"></a>Erstellen verknüpfter Dienste
In diesem Abschnitt erstellen Sie zwei verknüpfte Dienste: 
    
- Einen verknüpften Azure Storage-Dienst, der ein Azure Storage-Konto mit der Data Factory verbindet. Dieser Speicher wird vom bedarfsgesteuerten HDInsight-Cluster verwendet. Er enthält auch das Spark-Skript, das ausgeführt werden soll. 
- Einen bedarfsgesteuerten verknüpften HDInsight-Dienst. Azure Data Factory erstellt automatisch einen HDInsight-Cluster, führt das Spark-Programm aus und löscht dann den HDInsight-Cluster, wenn dieser für einen vorab konfigurierten Zeitraum inaktiv ist. 

### <a name="azure-storage-linked-service"></a>Mit Azure Storage verknüpfter Dienst
Erstellen Sie in Ihrem bevorzugten Editor eine JSON-Datei, kopieren Sie die folgende JSON-Definition eines verknüpften Azure Storage-Diensts hinein, und speichern Sie die Datei als **MyStorageLinkedService.json**.  

```json
{
    "name": "MyStorageLinkedService",
    "properties": {
      "type": "AzureStorage",
      "typeProperties": {
        "connectionString": "DefaultEndpointsProtocol=https;AccountName=<storageAccountName>;AccountKey=<storageAccountKey>"
      }
    }
}
```
Aktualisieren Sie &lt;storageAccountName&gt; und &lt;storageAccountKey&gt; mit dem Namen und dem Schlüssel Ihres Azure Storage-Kontos. 


### <a name="on-demand-hdinsight-linked-service"></a>Bedarfsgesteuerter verknüpfter HDInsight-Dienst
Erstellen Sie in Ihrem bevorzugten Editor eine JSON-Datei, kopieren Sie die folgende JSON-Definition eines verknüpften Azure HDInsight-Diensts hinein, und speichern Sie die Datei als **MyOnDemandSparkLinkedService.json**.  

```json
{
    "name": "MyOnDemandSparkLinkedService",
    "properties": {
      "type": "HDInsightOnDemand",
      "typeProperties": {
        "clusterSize": 2,
        "clusterType": "spark",
        "timeToLive": "00:15:00",
        "hostSubscriptionId": "<subscriptionID> ",
        "servicePrincipalId": "<servicePrincipalID>",
        "servicePrincipalKey": {
          "value": "<servicePrincipalKey>",
          "type": "SecureString"
        },
        "tenant": "<tenant ID>",
        "clusterResourceGroup": "<resourceGroupofHDICluster>",
        "version": "3.6",
        "osType": "Linux",
        "clusterNamePrefix":"ADFSparkSample",
        "linkedServiceName": {
          "referenceName": "MyStorageLinkedService",
          "type": "LinkedServiceReference"
        }
      }
    }
}
```
Aktualisieren Sie die Werte der folgenden Eigenschaften in der Definition des verknüpften Diensts: 

- **hostSubscriptionId**. Ersetzen Sie &lt;subscriptionID&gt; durch die ID Ihres Azure-Abonnements. Der bedarfsgesteuerte HDInsight-Cluster wird in diesem Abonnement erstellt. 
- **tenant**. Ersetzen Sie &lt;tenantID&gt; durch die ID Ihres Azure-Mandanten. 
- **servicePrincipalId**, **servicePrincipalKey**. Ersetzen Sie &lt;servicePrincipalID&gt; und &lt;servicePrincipalKey&gt; durch die ID und den Schlüssel Ihres Dienstprinzipals in Azure Active Directory. Dieser Dienstprinzipal muss Mitglied der Rolle „Mitwirkender“ in dem Abonnement oder der Ressourcengruppe sein, in dem bzw. der der Cluster erstellt wird. Weitere Informationen finden Sie unter [Erstellen einer Azure Active Directory-Anwendung und eines Dienstprinzipals](../active-directory/develop/howto-create-service-principal-portal.md). Die **Dienstprinzipal-ID** entspricht der *Anwendungs-ID* und ein **Dienstprinzipalschlüssel** dem Wert für ein *Clientgeheimnis*.
- **clusterResourceGroup**. Ersetzen Sie &lt;resourceGroupOfHDICluster&gt; durch den Namen der Ressourcengruppe, in der der HDInsight-Cluster erstellt werden muss. 

> [!NOTE]
> Die Gesamtanzahl von Kernen, die Sie in jeder von Azure HDInsight unterstützten Azure-Region verwenden können, ist begrenzt. Bei bedarfsgesteuerten verknüpften HDInsight-Diensten wird der HDInsight-Cluster in Azure Storage im gleichen Speicherort erstellt, der als primärer Speicher verwendet wird. Stellen Sie sicher, dass Sie über genügend Kernkontingente verfügen, sodass der Cluster erfolgreich erstellt werden kann. Weitere Informationen finden Sie unter [Einrichten von Clustern in HDInsight mit Hadoop, Spark, Kafka usw](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md). 


## <a name="author-a-pipeline"></a>Erstellen einer Pipeline 
In diesem Schritt erstellen Sie eine neue Pipeline mit einer Spark-Aktivität. Die Aktivität verwendet das Beispiel **word count**. Laden Sie die Inhalte von diesem Speicherort herunter, falls Sie dies nicht bereits getan haben.

Erstellen Sie in Ihrem bevorzugten Editor eine JSON-Datei, kopieren Sie die folgende JSON-Definition einer Pipelinedefinition hinein, und speichern Sie die Datei als **MySparkOnDemandPipeline.json**. 

```json
{
  "name": "MySparkOnDemandPipeline",
  "properties": {
    "activities": [
      {
        "name": "MySparkActivity",
        "type": "HDInsightSpark",
        "linkedServiceName": {
            "referenceName": "MyOnDemandSparkLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
          "rootPath": "adftutorial/spark",
          "entryFilePath": "script/WordCount_Spark.py",
          "getDebugInfo": "Failure",
          "sparkJobLinkedService": {
            "referenceName": "MyStorageLinkedService",
            "type": "LinkedServiceReference"
          }
        }
      }
    ]
  }
}
```

Beachten Sie folgende Punkte: 

- „rootPath“ zeigt auf den Spark-Ordner im Container „adftutorial“. 
- „entryFilePath“ zeigt auf die Datei „WordCount_Spark.py“ im Unterordner „script“ des Ordners „spark“. 


## <a name="create-a-data-factory"></a>Erstellen einer Data Factory 
Sie haben in JSON-Dateien Definitionen für einen verknüpften Dienst und eine Pipeline erstellt. Jetzt erstellen Sie eine Data Factory und stellen die JSON-Dateien für den verknüpften Dienst und die Pipeline mithilfe von PowerShell-Cmdlets bereit. Führen Sie nacheinander die folgenden PowerShell-Befehle aus: 

1. Legen Sie nacheinander die Variablen fest.

    **Ressourcengruppenname**
    ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup" 
    ```

    **Data Factory-Name. Muss global eindeutig sein.** 
    ```powershell
    $dataFactoryName = "MyDataFactory09102017"
    ```
    
    **Pipelinename**
    ```powershell
    $pipelineName = "MySparkOnDemandPipeline" # Name of the pipeline
    ```
2. Starten Sie **PowerShell**. Lassen Sie Azure PowerShell bis zum Ende dieser Schnellstartanleitung geöffnet. Wenn Sie PowerShell schließen und erneut öffnen, müssen Sie die Befehle erneut ausführen. Eine Liste der Azure-Regionen, in denen Data Factory derzeit verfügbar ist, finden Sie, indem Sie die für Sie interessanten Regionen auf der folgenden Seite auswählen und dann **Analysen** erweitern, um **Data Factory** zu finden: [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/). Die von der Data Factory verwendeten Datenspeicher (Azure Storage, Azure SQL-Datenbank usw.) und Computedienste (HDInsight usw.) können sich in anderen Regionen befinden.

    Führen Sie den folgenden Befehl aus, und geben Sie den Benutzernamen und das Kennwort ein, den bzw. das Sie bei der Anmeldung beim Azure-Portal verwendet haben:
        
    ```powershell
    Connect-AzAccount
    ```        
    Führen Sie den folgenden Befehl aus, um alle Abonnements für dieses Konto anzuzeigen:

    ```powershell
    Get-AzSubscription
    ```
    Führen Sie den folgenden Befehl aus, um das gewünschte Abonnement auszuwählen: Ersetzen Sie **SubscriptionId** durch die ID Ihres Azure-Abonnements:

    ```powershell
    Select-AzSubscription -SubscriptionId "<SubscriptionId>"    
    ```  
3. Erstellen Sie die Ressourcengruppe: ADFTutorialResourceGroup. 

    ```powershell
    New-AzResourceGroup -Name $resourceGroupName -Location "East Us" 
    ```
4. Erstellen Sie die Data Factory. 

    ```powershell
     $df = Set-AzDataFactoryV2 -Location EastUS -Name $dataFactoryName -ResourceGroupName $resourceGroupName
    ```

    Führen Sie den folgenden Befehl aus, um die Ausgabe anzuzeigen: 

    ```powershell
    $df
    ```
5. Wechseln Sie zu dem Ordner, in dem Sie die JSON-Dateien erstellt haben, und führen Sie den folgenden Befehl aus, um einen verknüpften Azure Storage-Dienst bereitzustellen: 
       
    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "MyStorageLinkedService" -File "MyStorageLinkedService.json"
    ```
6. Führen Sie den folgenden Befehl aus, um einen bedarfsgesteuerten verknüpften Spark-Dienst bereitzustellen: 
       
    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "MyOnDemandSparkLinkedService" -File "MyOnDemandSparkLinkedService.json"
    ```
7. Führen Sie den folgenden Befehl aus, um eine Pipeline bereitzustellen: 
       
    ```powershell
    Set-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name $pipelineName -File "MySparkOnDemandPipeline.json"
    ```
    
## <a name="start-and-monitor-a-pipeline-run"></a>Starten und Überwachen einer Pipelineausführung  

1. Starten einer Pipelineausführung Die ID der Pipelineausführung wird für die zukünftige Überwachung ebenfalls erfasst.

    ```powershell
    $runId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName $pipelineName  
    ```
2. Führen Sie das folgende Skript aus, um den Status der Pipelineausführung kontinuierlich zu überwachen, bis sie beendet ist.

    ```powershell
    while ($True) {
        $result = Get-AzDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
    
        if(!$result) {
            Write-Host "Waiting for pipeline to start..." -foregroundcolor "Yellow"
        }
        elseif (($result | Where-Object { $_.Status -eq "InProgress" } | Measure-Object).count -ne 0) {
            Write-Host "Pipeline run status: In Progress" -foregroundcolor "Yellow"
        }
        else {
            Write-Host "Pipeline '"$pipelineName"' run finished. Result:" -foregroundcolor "Yellow"
            $result
            break
        }
        ($result | Format-List | Out-String)
        Start-Sleep -Seconds 15
    }

    Write-Host "Activity `Output` section:" -foregroundcolor "Yellow"
    $result.Output -join "`r`n"

    Write-Host "Activity `Error` section:" -foregroundcolor "Yellow"
    $result.Error -join "`r`n" 
    ```  
3. Hier ist die Ausgabe der Beispielausführung: 

    ```
    Pipeline run status: In Progress
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : 
    ActivityName      : MySparkActivity
    PipelineRunId     : 94e71d08-a6fa-4191-b7d1-cf8c71cb4794
    PipelineName      : MySparkOnDemandPipeline
    Input             : {rootPath, entryFilePath, getDebugInfo, sparkJobLinkedService}
    Output            : 
    LinkedServiceName : 
    ActivityRunStart  : 9/20/2017 6:33:47 AM
    ActivityRunEnd    : 
    DurationInMs      : 
    Status            : InProgress
    Error             :
    …
    
    Pipeline ' MySparkOnDemandPipeline' run finished. Result:
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : MyDataFactory09102017
    ActivityName      : MySparkActivity
    PipelineRunId     : 94e71d08-a6fa-4191-b7d1-cf8c71cb4794
    PipelineName      : MySparkOnDemandPipeline
    Input             : {rootPath, entryFilePath, getDebugInfo, sparkJobLinkedService}
    Output            : {clusterInUse, jobId, ExecutionProgress, effectiveIntegrationRuntime}
    LinkedServiceName : 
    ActivityRunStart  : 9/20/2017 6:33:47 AM
    ActivityRunEnd    : 9/20/2017 6:46:30 AM
    DurationInMs      : 763466
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    Activity Output section:
    "clusterInUse": "https://ADFSparkSamplexxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.azurehdinsight.net/"
    "jobId": "0"
    "ExecutionProgress": "Succeeded"
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)"
    Activity Error section:
    "errorCode": ""
    "message": ""
    "failureType": ""
    "target": "MySparkActivity"
    ```
4. Vergewissern Sie sich, dass im Ordner `spark` des Containers „adftutorial“ ein Ordner mit dem Namen `outputfiles` und der Ausgabe des Spark-Programms erstellt wurde. 


## <a name="next-steps"></a>Nächste Schritte
Die Pipeline in diesem Beispiel kopiert Daten in einem Azure Blob Storage von einem Speicherort in einen anderen. Sie haben Folgendes gelernt: 

> [!div class="checklist"]
> * Erstellen einer Data Factory. 
> * Erstellen und Bereitstellen von verknüpften Diensten
> * Erstellen und Bereitstellen einer Pipeline. 
> * Starten einer Pipelineausführung
> * Überwachen der Pipelineausführung.

Fahren Sie mit dem nächsten Tutorial fort, um zu erfahren, wie Sie Daten transformieren, indem Sie ein Hive-Skript in einem Azure HDInsight-Cluster ausführen, der sich in einem virtuellen Netzwerk befindet. 

> [!div class="nextstepaction"]
> [Tutorial: Transformieren von Daten mithilfe von Hive in Azure Virtual Network](tutorial-transform-data-hive-virtual-network.md)





