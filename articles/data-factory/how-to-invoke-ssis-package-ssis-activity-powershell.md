---
title: Verwenden der Aktivität „SSIS-Paket ausführen“ mit PowerShell
description: In diesem Artikel wird das Ausführen eines SSIS-Pakets (SQL Server Integration Services) mit PowerShell in einer Azure Data Factory-Pipeline mithilfe der Aktivität „SSIS-Paket ausführen“ beschrieben.
ms.service: data-factory
ms.subservice: integration-services
ms.devlang: powershell
ms.topic: conceptual
ms.author: sawinark
author: swinarko
ms.custom: seo-lt-2019, devx-track-azurepowershell
ms.date: 10/22/2021
ms.openlocfilehash: ab67509d2b9c9eda90268c19bce081d1bb11e942
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131843459"
---
# <a name="run-an-ssis-package-with-the-execute-ssis-package-activity-in-azure-data-factory-with-powershell"></a>Ausführen eines SSIS-Pakets mit der Aktivität „SSIS-Paket ausführen“ in Azure Data Factory mit PowerShell

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In diesem Artikel wird das Ausführen eines SSIS-Pakets (SQL Server Integration Services) in einer Azure Data Factory-Pipeline mithilfe von PowerShell und der Aktivität „SSIS-Paket ausführen“ beschrieben.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Erstellen Sie eine Azure-SSIS Integration Runtime-Instanz (IR), falls Sie noch keine besitzen. Befolgen Sie dazu die ausführliche Anleitung unter [Tutorial: Bereitstellen der Azure-SSIS Integration Runtime in Azure Data Factory](./tutorial-deploy-ssis-packages-azure.md).

## <a name="create-a-data-factory-with-azure-ssis-ir"></a>Erstellen einer Data Factory mit Azure-SSIS IR
Sie können entweder eine vorhandene Data Factory verwenden, in der Azure-SSIS IR bereits bereitgestellt wurde, oder eine neue Data Factory mit Azure-SSIS IR erstellen. Befolgen Sie dann die Schrittanleitungen unter [Tutorial: Bereitstellen von SSIS-Paketen in Azure mit PowerShell](./tutorial-deploy-ssis-packages-azure-powershell.md) folgen.

## <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>Erstellen einer Pipeline mit einer Aktivität „SSIS-Paket ausführen“ 
In diesem Schritt erstellen Sie eine Pipeline mit einer Aktivität „SSIS-Paket ausführen“. Die Aktivität führt Ihr SSIS-Paket aus. 

1. Erstellen Sie im Ordner `C:\ADF\RunSSISPackage` die JSON-Datei `RunSSISPackagePipeline.json` mit Inhalt, der dem folgenden Beispiel ähnelt.

   > [!IMPORTANT]
   > Bevor Sie die Datei speichern, ersetzen Sie Objektnamen, Beschreibungen, Pfade, Eigenschafts- oder Parameterwerte, Kennwörter und andere Variablenwerte. 
    
   ```json
   {
       "name": "RunSSISPackagePipeline",
       "properties": {
           "activities": [{
               "name": "MySSISActivity",
               "description": "My SSIS package/activity description",
               "type": "ExecuteSSISPackage",
               "typeProperties": {
                   "connectVia": {
                       "referenceName": "MyAzureSSISIR",
                       "type": "IntegrationRuntimeReference"
                   },
                   "executionCredential": {
                       "domain": "MyExecutionDomain",
                       "username": "MyExecutionUsername",
                       "password": {
                           "type": "SecureString",
                           "value": "MyExecutionPassword"
                       }
                   },
                   "runtime": "x64",
                   "loggingLevel": "Basic",
                   "packageLocation": {
                       "type": "SSISDB",
                       "packagePath": "MyFolder/MyProject/MyPackage.dtsx"
                   },
                   "environmentPath": "MyFolder/MyEnvironment",
                   "projectParameters": {
                       "project_param_1": {
                           "value": "123"
                       },
                       "project_param_2": {
                           "value": {
                               "value": "@pipeline().parameters.MyProjectParameter",
                               "type": "Expression"
                           }
                       }
                   },
                   "packageParameters": {
                       "package_param_1": {
                           "value": "345"
                       },
                       "package_param_2": {
                           "value": {
                               "type": "AzureKeyVaultSecret",
                               "store": {
                                   "referenceName": "myAKV",
                                   "type": "LinkedServiceReference"
                               },
                               "secretName": "MyPackageParameter"
                           }
                       }
                   },
                   "projectConnectionManagers": {
                       "MyAdonetCM": {
                           "username": {
                               "value": "MyConnectionUsername"
                           },
                           "password": {
                               "value": {
                                   "type": "SecureString",
                                   "value": "MyConnectionPassword"
                               }
                           }
                       }
                   },
                   "packageConnectionManagers": {
                       "MyOledbCM": {
                           "username": {
                               "value": {
                                   "value": "@pipeline().parameters.MyConnectionUsername",
                                   "type": "Expression"
                               }
                           },
                           "password": {
                               "value": {
                                   "type": "AzureKeyVaultSecret",
                                   "store": {
                                       "referenceName": "myAKV",
                                       "type": "LinkedServiceReference"
                                   },
                                   "secretName": "MyConnectionPassword",
                                   "secretVersion": "MyConnectionPasswordVersion"
                               }
                           }
                       }
                   },
                   "propertyOverrides": {
                       "\\Package.MaxConcurrentExecutables": {
                           "value": 8,
                           "isSensitive": false
                       }
                   }
               },
               "policy": {
                   "timeout": "0.01:00:00",
                   "retry": 0,
                   "retryIntervalInSeconds": 30
               }
           }]
       }
   }
   ```

   Geben Sie zum Ausführen von Paketen, die im Dateisystem/in Azure Files gespeichert sind, die Werte für Ihre Paket- und Protokollspeicherort-Eigenschaften wie folgt ein:

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "type": "File",
                       "packagePath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyPackage.dtsx",
                       "typeProperties": {
                           "packagePassword": {
                               "type": "SecureString",
                               "value": "MyEncryptionPassword"
                           },
                           "accessCredential": {
                               "domain": "Azure",
                               "username": "MyStorageAccount",
                               "password": {
                                   "type": "SecureString",
                                   "value": "MyAccountKey"
                               }
                           }
                       }
                   },
                   "logLocation": {
                       "type": "File",
                       "logPath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyLogFolder",
                       "typeProperties": {
                           "accessCredential": {
                               "domain": "Azure",
                               "username": "MyStorageAccount",
                               "password": {
                                   "type": "AzureKeyVaultSecret",
                                   "store": {
                                       "referenceName": "myAKV",
                                       "type": "LinkedServiceReference"
                                   },
                                   "secretName": "MyAccountKey"
                               }
                           }
                       }
                   }
               }
           }
       }
   }
   ```

   Geben Sie zum Ausführen von Paketen in Projekten, die im Dateisystem/in Azure Files gespeichert sind, die Werte für Ihre Paketspeicherort-Eigenschaften wie folgt ein:

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "type": "File",
                       "packagePath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyProject.ispac:MyPackage.dtsx",
                       "typeProperties": {
                           "packagePassword": {
                               "type": "SecureString",
                               "value": "MyEncryptionPassword"
                           },
                           "accessCredential": {
                               "domain": "Azure",
                               "userName": "MyStorageAccount",
                               "password": {
                                   "type": "SecureString",
                                   "value": "MyAccountKey"
                               }
                           }
                       }
                   }
               }
           }
       }
   }
   ```

   Geben Sie zum Ausführen von eingebetteten Paketen die Werte für Ihre Paketspeicherort-Eigenschaften wie folgt ein:

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "type": "InlinePackage",
                       "typeProperties": {
                           "packagePassword": {
                               "type": "SecureString",
                               "value": "MyEncryptionPassword"
                           },
                           "packageName": "MyPackage.dtsx",
                           "packageContent":"My compressed/uncompressed package content",
                           "packageLastModifiedDate": "YYYY-MM-DDTHH:MM:SSZ UTC-/+HH:MM"
                       }
                   }
               }
           }
       }
   }
   ```

   Geben Sie zum Ausführen von Paketen, die in Paketspeichern gespeichert sind, die Werte für Ihre Paket- und Konfigurationsspeicherort-Eigenschaften wie folgt ein:

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "type": "PackageStore",
                       "packagePath": "myPackageStore/MyFolder/MyPackage",
                       "typeProperties": {
                           "packagePassword": {
                               "type": "SecureString",
                               "value": "MyEncryptionPassword"
                           },
                           "accessCredential": {
                               "domain": "Azure",
                               "username": "MyStorageAccount",
                               "password": {
                                   "type": "SecureString",
                                   "value": "MyAccountKey"
                               }
                           },
                           "configurationPath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyConfiguration.dtsConfig",
                           "configurationAccessCredential": {
                               "domain": "Azure",
                               "userName": "MyStorageAccount",
                               "password": {
                                   "type": "AzureKeyVaultSecret",
                                   "store": {
                                       "referenceName": "myAKV",
                                       "type": "LinkedServiceReference"
                                   },
                                   "secretName": "MyAccountKey"
                               }
                           }
                       }
                   }
               }
           }
       }
   }
   ```

2. Navigieren Sie in Azure PowerShell zum Ordner `C:\ADF\RunSSISPackage`.

3. Führen Sie das Cmdlet **Set-AzDataFactoryV2Pipeline** aus, um die Pipeline **RunSSISPackagePipeline** zu erstellen.

   ```powershell
   $DFPipeLine = Set-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                                  -ResourceGroupName $ResGrp.ResourceGroupName `
                                                  -Name "RunSSISPackagePipeline"
                                                  -DefinitionFile ".\RunSSISPackagePipeline.json"
   ```

   Hier ist die Beispielausgabe:

   ```
   PipelineName      : Adfv2QuickStartPipeline
   ResourceGroupName : <resourceGroupName>
   DataFactoryName   : <dataFactoryName>
   Activities        : {CopyFromBlobToBlob}
   Parameters        : {[inputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification], [outputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
   ```

## <a name="run-the-pipeline"></a>Führen Sie die Pipeline aus.
Verwenden Sie das Cmdlet **Invoke-AzDataFactoryV2Pipeline**, um die Pipeline auszuführen. Das Cmdlet gibt die ID der Pipelineausführung für die zukünftige Überwachung zurück.

```powershell
$RunId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                             -ResourceGroupName $ResGrp.ResourceGroupName `
                                             -PipelineName $DFPipeLine.Name
```

## <a name="monitor-the-pipeline"></a>Überwachen der Pipeline

Führen Sie das folgende PowerShell-Skript aus, um den Status der Pipelineausführung kontinuierlich zu überwachen, bis das Kopieren der Daten beendet ist. Kopieren Sie das folgende Skript, fügen Sie es in das PowerShell-Fenster ein, und drücken Sie die EINGABETASTE. 

```powershell
while ($True) {
    $Run = Get-AzDataFactoryV2PipelineRun -ResourceGroupName $ResGrp.ResourceGroupName `
                                               -DataFactoryName $DataFactory.DataFactoryName `
                                               -PipelineRunId $RunId

    if ($Run) {
        if ($run.Status -ne 'InProgress') {
            Write-Output ("Pipeline run finished. The status is: " +  $Run.Status)
            $Run
            break
        }
        Write-Output  "Pipeline is running...status: InProgress"
    }

    Start-Sleep -Seconds 10
}   
```

Sie können die Pipeline auch mithilfe des Azure-Portals überwachen. Schritt-für-Schritt-Anweisungen finden Sie unter [Überwachen der Pipeline](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).

## <a name="schedule-the-pipeline-with-a-trigger"></a>Planen der Pipeline mit einem Trigger
Im vorherigen Schritt wurde die Pipeline auf Anforderung ausgeführt. Alternativ können Sie einen Zeitplantrigger erstellen, um die Pipeline nach Zeitplan (etwa stündlich oder täglich) auszuführen.

1. Erstellen Sie im Ordner `MyTrigger.json` die JSON-Datei `C:\ADF\RunSSISPackage` mit folgendem Inhalt: 
        
   ```json
   {
       "properties": {
           "name": "MyTrigger",
           "type": "ScheduleTrigger",
           "typeProperties": {
               "recurrence": {
                   "frequency": "Hour",
                   "interval": 1,
                   "startTime": "2017-12-07T00:00:00-08:00",
                   "endTime": "2017-12-08T00:00:00-08:00"
               }
           },
           "pipelines": [{
               "pipelineReference": {
                   "type": "PipelineReference",
                   "referenceName": "RunSSISPackagePipeline"
               },
               "parameters": {}
           }]
       }
   }    
   ```
    
1. Navigieren Sie in Azure PowerShell zum Ordner `C:\ADF\RunSSISPackage`.
1. Führen Sie das Cmdlet **Set-AzDataFactoryV2Trigger** aus, um den Trigger zu erstellen. 

   ```powershell
   Set-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                   -DataFactoryName $DataFactory.DataFactoryName `
                                   -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
   ```
1. Standardmäßig befindet sich der Trigger im beendeten Zustand. Starten Sie den Trigger durch Ausführen des Cmdlets **Start-AzDataFactoryV2Trigger**. 

   ```powershell
   Start-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                     -DataFactoryName $DataFactory.DataFactoryName `
                                     -Name "MyTrigger" 
   ```
1. Bestätigen Sie, dass der Trigger sich im gestarteten Zustand befindet, indem Sie das Cmdlet **Get-AzDataFactoryV2Trigger** ausführen. 

   ```powershell
   Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName `
                                   -DataFactoryName $DataFactoryName `
                                   -Name "MyTrigger"     
   ```    
1. Führen Sie nach der nächsten vollen Stunde den folgenden Befehl aus. Wenn die aktuelle Uhrzeit z.B. 15:25 UTC ist, führen Sie den Befehl um 16:00 UTC aus. 
    
   ```powershell
   Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName `
                                      -DataFactoryName $DataFactoryName `
                                      -TriggerName "MyTrigger" `
                                      -TriggerRunStartedAfter "2017-12-06" `
                                      -TriggerRunStartedBefore "2017-12-09"
   ```

   Führen Sie auf Ihrem SQL-Server die folgende Abfrage für die SSISDB-Datenbank ausführen, um zu überprüfen, ob das Paket ausgeführt wurde. 

   ```sql
   select * from catalog.executions
   ```

## <a name="next-steps"></a>Nächste Schritte

- [Ausführen eines SSIS-Pakets mit der Aktivität „SSIS-Paket ausführen“ im Azure Data Factory Studio-Portal](how-to-invoke-ssis-package-ssis-activity.md)
- [Modernisieren und Erweitern von ETL/ELT-Workflows mit SSIS-Aktivitäten in Azure Data Factory-Pipelines](https://techcommunity.microsoft.com/t5/SQL-Server-Integration-Services/Modernize-and-Extend-Your-ETL-ELT-Workflows-with-SSIS-Activities/ba-p/388370)