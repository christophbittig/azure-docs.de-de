---
title: Ausführen von Apache Hive-Abfragen per HDInsight .NET-SDK – Azure
description: Erfahren Sie, wie Sie Apache Hadoop-Aufträge per HDInsight .NET-SDK an Azure HDInsight Apache Hadoop übermitteln können.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-csharp
ms.date: 12/24/2019
ms.openlocfilehash: 8c2dc7cef889a0488352ace443771d118b0d0613
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/17/2021
ms.locfileid: "122639672"
---
# <a name="run-apache-hive-queries-using-hdinsight-net-sdk"></a>Ausführen von Apache Hive-Abfragen per HDInsight .NET-SDK

[!INCLUDE [hive-selector](../includes/hdinsight-selector-use-hive.md)]

Erfahren Sie, wie Sie Apache Hive-Abfragen mit dem HDInsight .NET-SDK übermitteln. Sie schreiben ein C#-Programm, um eine Hive-Abfrage zum Auflisten von Hive-Tabellen zu senden, und die Ergebnisse werden angezeigt.

> [!NOTE]  
> Die Schritte in diesem Artikel müssen auf einem Windows-Client ausgeführt werden. Informationen zur Verwendung eines Linux-, OS X- oder Unix-Clients für Hive erhalten Sie, indem Sie die Registerkartenauswahl am Anfang des Artikels nutzen.

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie mit diesem Artikel beginnen können, benötigen Sie Folgendes:

* Einen Apache Hadoop-Cluster in HDInsight. Siehe [Erste Schritte bei der Verwendung von Linux-basiertem Hadoop in HDInsight](apache-hadoop-linux-tutorial-get-started.md).

    > [!IMPORTANT]  
    > Ab dem 15. September 2017 unterstützt das HDInsight .NET SDK nur zurückgegebene Hive-Abfrageergebnisse von Azure Storage-Konten. Wenn Sie dieses Beispiel mit einem HDInsight-Cluster verwenden, der Azure Data Lake Storage als primären Speicher nutzt, können Sie keine Suchergebnisse mit dem .NET SDK abrufen.

* [Visual Studio](https://visualstudio.microsoft.com/vs/community/) 2013 und höher. Mindestens die Workload **.NET-Desktopentwicklung** muss installiert sein.

## <a name="run-a-hive-query"></a>Ausführen einer Hive-Abfrage

Das HDInsight .NET SDK enthält .NET-Clientbibliotheken, die das Arbeiten mit HDInsight-Clustern in .NET vereinfachen.

1. Erstellen Sie in Visual Studio eine C#-Konsolenanwendung.

1. Führen Sie in der NuGet-Paket-Manager-Konsole den folgenden Befehl aus:

    ```console
    Install-Package Microsoft.Azure.Management.HDInsight.Job
    ```

1. Bearbeiten Sie den folgenden Code, um die Werte für Variablen zu initialisieren: `ExistingClusterName, ExistingClusterUsername, ExistingClusterPassword,DefaultStorageAccountName,DefaultStorageAccountKey,DefaultStorageContainerName`. Verwenden Sie dann den überarbeiteten Code als gesamten Inhalt von **Program.cs** in Visual Studio.

    ```csharp
    using System.Collections.Generic;
    using System.IO;
    using System.Text;
    using System.Threading;
    using Microsoft.Azure.Management.HDInsight.Job;
    using Microsoft.Azure.Management.HDInsight.Job.Models;
    using Hyak.Common;

    namespace SubmitHDInsightJobDotNet
    {
        class Program
        {
            private static HDInsightJobManagementClient _hdiJobManagementClient;

            private const string ExistingClusterName = "<Your HDInsight Cluster Name>";
            private const string ExistingClusterUsername = "<Cluster Username>";
            private const string ExistingClusterPassword = "<Cluster User Password>";

            // Only Azure Storage accounts are supported by the SDK
            private const string DefaultStorageAccountName = "<Default Storage Account Name>";
            private const string DefaultStorageAccountKey = "<Default Storage Account Key>";
            private const string DefaultStorageContainerName = "<Default Blob Container Name>";

            private const string ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";

            static void Main(string[] args)
            {
                System.Console.WriteLine("The application is running ...");

                var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
                _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);

                SubmitHiveJob();

                System.Console.WriteLine("Press ENTER to continue ...");
                System.Console.ReadLine();
            }

            private static void SubmitHiveJob()
            {
                Dictionary<string, string> defines = new Dictionary<string, string> { { "hive.execution.engine", "tez" }, { "hive.exec.reducers.max", "1" } };
                List<string> args = new List<string> { { "argA" }, { "argB" } };
                var parameters = new HiveJobSubmissionParameters
                {
                    Query = "SHOW TABLES",
                    Defines = defines,
                    Arguments = args
                };

                System.Console.WriteLine("Submitting the Hive job to the cluster...");
                var jobResponse = _hdiJobManagementClient.JobManagement.SubmitHiveJob(parameters);
                var jobId = jobResponse.JobSubmissionJsonResponse.Id;
                System.Console.WriteLine("Response status code is " + jobResponse.StatusCode);
                System.Console.WriteLine("JobId is " + jobId);

                System.Console.WriteLine("Waiting for the job completion ...");

                // Wait for job completion
                var jobDetail = _hdiJobManagementClient.JobManagement.GetJob(jobId).JobDetail;
                while (!jobDetail.Status.JobComplete)
                {
                    Thread.Sleep(1000);
                    jobDetail = _hdiJobManagementClient.JobManagement.GetJob(jobId).JobDetail;
                }

                // Get job output
                var storageAccess = new AzureStorageAccess(DefaultStorageAccountName, DefaultStorageAccountKey,
                    DefaultStorageContainerName);
                var output = (jobDetail.ExitValue == 0)
                    ? _hdiJobManagementClient.JobManagement.GetJobOutput(jobId, storageAccess) // fetch stdout output in case of success
                    : _hdiJobManagementClient.JobManagement.GetJobErrorLogs(jobId, storageAccess); // fetch stderr output in case of failure

                System.Console.WriteLine("Job output is: ");

                using (var reader = new StreamReader(output, Encoding.UTF8))
                {
                    string value = reader.ReadToEnd();
                    System.Console.WriteLine(value);
                }
            }
        }
    }
    ```

1. Drücken Sie **F5**, um die Anwendung auszuführen.

Die Ausgabe der Anwendung sollte Folgendem ähneln:

:::image type="content" source="./media/apache-hadoop-use-hive-dotnet-sdk/hdinsight-hadoop-use-hive-net-sdk-output.png" alt-text="Ausgabe des HDInsight Hadoop Hive-Auftrags" border="true":::

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel erfahren Sie, wie Sie Apache Hive-Abfragen mit dem HDInsight .NET SDK übermitteln. Weitere Informationen finden Sie in den folgenden Artikeln:

* [Erste Schritte mit Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md)
* [Erstellen von Apache Hadoop-Clustern in HDInsight](../hdinsight-hadoop-provision-linux-clusters.md)
* [HDInsight .NET SDK-Referenz](/dotnet/api/overview/azure/hdinsight)
* [Verwenden von Apache Sqoop mit HDInsight](apache-hadoop-use-sqoop-mac-linux.md)
* [Erstellen von .NET HDInsight-Anwendungen für die nicht interaktive Authentifizierung](../hdinsight-create-non-interactive-authentication-dotnet-applications.md)
