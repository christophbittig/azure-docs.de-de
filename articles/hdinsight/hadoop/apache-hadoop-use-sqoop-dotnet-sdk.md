---
title: Ausführen von Apache Sqoop-Aufträgen mit .NET und HDInsight – Azure
description: Erfahren Sie, wie Sie mit dem HDInsight .NET-SDK einen Apache Sqoop-Import und -Export zwischen einem Apache Hadoop-Cluster und einer Azure SQL-Datenbank ausführen.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, hdiseo17may2017, devx-track-csharp
ms.date: 01/14/2020
ms.openlocfilehash: 744025c8d74efe5e01a452853db2c854dca4a7f9
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/17/2021
ms.locfileid: "122639662"
---
# <a name="run-apache-sqoop-jobs-by-using-net-sdk-for-apache-hadoop-in-hdinsight"></a>Ausführen von Apache Sqoop-Aufträgen mit .NET SDK für Apache Hadoop in HDInsight

[!INCLUDE [sqoop-selector](../includes/hdinsight-selector-use-sqoop.md)]

Erfahren Sie, wie das Azure HDInsight .NET-SDK zum Ausführen von Apache Sqoop-Aufträgen in HDInsight zum Importieren und Exportieren zwischen einem HDInsight-Cluster und einer Azure SQL-Datenbank oder SQL Server-Datenbank verwendet wird.

## <a name="prerequisites"></a>Voraussetzungen

* Die unter [Verwenden von Apache Sqoop mit Hadoop in HDInsight](./hdinsight-use-sqoop.md) beschriebene [Einrichtung der Testumgebung](./hdinsight-use-sqoop.md#create-cluster-and-sql-database) muss abgeschlossen sein.

* [Visual Studio](https://visualstudio.microsoft.com/vs/community/).

* Vertrautheit mit Sqoop. Weitere Informationen finden Sie im [Sqoop-Benutzerhandbuch](https://sqoop.apache.org/docs/1.4.7/SqoopUserGuide.html).

## <a name="use-sqoop-on-hdinsight-clusters-with-the-net-sdk"></a>Verwenden von Sqoop auf HDInsight-Clustern mit dem .NET-SDK

Das HDInsight .NET-SDK enthält .NET-Clientbibliotheken, die das Arbeiten mit HDInsight-Clustern in .NET vereinfachen. In diesem Abschnitt erstellen Sie eine C#-Konsolenanwendung zum Exportieren der `hivesampletable` in die Azure SQL-Datenbanktabelle, die Sie als Voraussetzung erstellt haben.

## <a name="set-up"></a>Einrichten

1. Starten Sie Visual Studio, und erstellen Sie eine C#-Konsolenanwendung.

1. Navigieren Sie zu **Extras** > **NuGet-Paket-Manager** > **Paket-Manager-Konsole**, und führen Sie den folgenden Befehl aus:

    ```
    Install-Package Microsoft.Azure.Management.HDInsight.Job
    ```

## <a name="sqoop-export"></a>Sqoop-Export

Aus Hive in SQL Server:  In diesem Beispiel werden Daten aus der Hive-Tabelle `hivesampletable` in die Tabelle `mobiledata` in SQL-Datenbank exportiert.

1. Verwenden Sie den folgenden Code in der Datei „Program.cs“. Bearbeiten Sie den Code, um die Werte für `ExistingClusterName` und `ExistingClusterPassword` festzulegen.

    ```csharp
    using Microsoft.Azure.Management.HDInsight.Job;
    using Microsoft.Azure.Management.HDInsight.Job.Models;
    using Hyak.Common;
    
    namespace SubmitHDInsightJobDotNet
    {
        class Program
        {
            private static HDInsightJobManagementClient _hdiJobManagementClient;
    
            private const string ExistingClusterName = "<Your HDInsight Cluster Name>";
            private const string ExistingClusterPassword = "<Cluster User Password>";
            private const string ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";
            private const string ExistingClusterUsername = "admin";
    
            static void Main(string[] args)
            {
                System.Console.WriteLine("The application is running ...");
    
                var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
                _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);
    
                SubmitSqoopJob();
    
                System.Console.WriteLine("Press ENTER to continue ...");
                System.Console.ReadLine();
            }
    
            private static void SubmitSqoopJob()
            {
                var sqlDatabaseServerName = ExistingClusterName + "dbserver";
                var sqlDatabaseLogin = "sqluser";
                var sqlDatabaseLoginPassword = ExistingClusterPassword;
                var sqlDatabaseDatabaseName = ExistingClusterName + "db";
    
                // Connection string for using Azure SQL Database; Comment if using SQL Server
                var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ".database.windows.net;user=" + sqlDatabaseLogin + "@" + sqlDatabaseServerName + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
    
                // Connection string for using SQL Server; Uncomment if using SQL Server
                // var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ";user=" + sqlDatabaseLogin + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
    
                //sqoop start
                var tableName = "mobiledata";
    
                var parameters = new SqoopJobSubmissionParameters
                {
                     Command = "export --connect " + connectionString + " --table " + tableName + " --hcatalog-table hivesampletable"
                };
                //sqoop end
    
                System.Console.WriteLine("Submitting the Sqoop job to the cluster...");
                var response = _hdiJobManagementClient.JobManagement.SubmitSqoopJob(parameters);
                System.Console.WriteLine("Validating that the response is as expected...");
                System.Console.WriteLine("Response status code is " + response.StatusCode);
                System.Console.WriteLine("Validating the response object...");
                System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
            }
        }
    }
    ```

1. Wählen Sie zum Ausführen des Programms die Taste **F5**.

## <a name="sqoop-import"></a>Sqoop-Import

Aus SQL Server in Azure Storage. Dieses Beispiel setzt voraus, dass der oben aufgeführte Exportvorgang durchgeführt wurde.  In diesem Beispiel werden Daten aus der Tabelle `mobiledata` in SQL-Datenbank in das Verzeichnis `wasb:///tutorials/usesqoop/importeddata` unter dem Standardspeicherkonto des Clusters importiert.

1. Ersetzen Sie den oben angegebenen Code im Block `//sqoop start //sqoop end` durch den folgenden Code:

    ```csharp
    var tableName = "mobiledata";
    var exportDir = "/tutorials/usesqoop/importeddata";
    
    var parameters = new SqoopJobSubmissionParameters
    {
        Command = "import --connect " + connectionString + " --table " + tableName + " --target-dir " +  exportDir + " --fields-terminated-by \\t --lines-terminated-by \\n -m 1"
    };
    ```

1. Wählen Sie zum Ausführen des Programms die Taste **F5**.

## <a name="limitations"></a>Einschränkungen

Für Linux-basiertes HDInsight gelten die folgenden Einschränkungen:

* Massenexport: Derzeit unterstützt der zum Exportieren von Daten nach Microsoft SQL Server oder Azure SQL-Datenbank verwendete Sqoop-Connector keine Masseneinfügungen.

* Batchverarbeitung: Wenn Sie die Option `-batch` verwenden, führt Sqoop mehrere Einfügevorgänge aus, anstatt diese zu einem Batch zusammenzufassen.

## <a name="next-steps"></a>Nächste Schritte

Sie haben nun gelernt, wie Sie Sqoop verwenden. Weitere Informationen finden Sie unter:

* [Verwenden von Apache Oozie mit HDInsight](../hdinsight-use-oozie-linux-mac.md): Verwenden der Sqoop-Aktion in einem Oozie-Workflow.
* [Hochladen von Daten in HDInsight](../hdinsight-upload-data.md): Weitere Methoden zum Hochladen von Daten in HDInsight oder Azure Blob Storage.
