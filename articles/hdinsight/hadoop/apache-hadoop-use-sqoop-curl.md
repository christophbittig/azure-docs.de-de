---
title: Verwenden von Curl zum Exportieren von Daten mit Apache Sqoop in Azure HDInsight
description: Erfahren Sie, wie Sie Apache Sqoop-Aufträge mithilfe von Curl remote an Azure HDInsight übermitteln.
ms.service: hdinsight
ms.topic: how-to
ms.date: 01/06/2020
ms.openlocfilehash: f2a097e18f6e1f285335a89574efd720fd251e92
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/17/2021
ms.locfileid: "122639664"
---
# <a name="run-apache-sqoop-jobs-in-hdinsight-with-curl"></a>Ausführen von Apache Sqoop-Aufträgen in HDInsight mit Curl

[!INCLUDE [sqoop-selector](../includes/hdinsight-selector-use-sqoop.md)]

Erfahren Sie, wie Sie Apache Sqoop-Aufträge mithilfe von Curl in einem Apache Hadoop-Cluster in HDInsight ausführen können. In diesem Artikel wird veranschaulicht, wie Sie Daten aus Azure Storage exportieren und mit Curl in eine SQL Server-Datenbank importieren. Dieser Artikel ist eine Fortsetzung von [Verwenden von Apache Sqoop mit Hadoop in HDInsight](./hdinsight-use-sqoop.md).

Curl wird verwendet, um zu veranschaulichen, wie Sie über unformatierte HTTP-Anforderungen zum Ausführen, Überwachen und Abrufen der Ergebnisse der Sqoop-Aufträge mit HDInsight interagieren können. Dies funktioniert mithilfe der WebHCat REST-API (ehemals Templeton), die von Ihrem HDInsight-Cluster bereitgestellt wird.

## <a name="prerequisites"></a>Voraussetzungen

* Die unter [Verwenden von Apache Sqoop mit Hadoop in HDInsight](./hdinsight-use-sqoop.md) beschriebene [Einrichtung der Testumgebung](./hdinsight-use-sqoop.md#create-cluster-and-sql-database) muss abgeschlossen sein.

* Ein Client zum Abfragen der Azure SQL-Datenbank. Erwägen Sie die Verwendung von [SQL Server Management Studio](../../azure-sql/database/connect-query-ssms.md) oder [Visual Studio Code](../../azure-sql/database/connect-query-vscode.md).

* [Curl](https://curl.haxx.se/). Curl ist ein Tool zum Übertragen von Daten aus einem oder in einen HDInsight-Cluster.

* [jq](https://stedolan.github.io/jq/). Das Hilfsprogramm jq wird verwendet, um die von REST-Anforderungen zurückgegebenen JSON-Daten zu verarbeiten.

* Vertrautheit mit Sqoop. Weitere Informationen finden Sie im [Sqoop-Benutzerhandbuch](https://sqoop.apache.org/docs/1.4.7/SqoopUserGuide.html).

## <a name="submit-apache-sqoop-jobs-by-using-curl"></a>Übermitteln von Apache Sqoop-Aufträgen mithilfe von Curl.

Verwenden Sie Curl, um Daten mit Apache Sqoop-Aufträgen aus Azure Storage für SQL Server zu exportieren.

> [!NOTE]  
> Wenn Sie Curl oder eine andere REST-Kommunikation mit WebHCat verwenden, müssen Sie die Anforderungen authentifizieren, indem Sie den Benutzernamen und das Kennwort des Administrators des HDInsight-Clusters bereitstellen. Sie müssen auch den Clusternamen als Teil des URIs (Uniform Resource Identifier) verwenden, um die Anforderungen an den Server zu senden.

Ersetzen Sie beim Verwenden der Befehle in diesem Abschnitt die Option `USERNAME` zur Authentifizierung im Cluster durch den Benutzer und `PASSWORD` durch das Kennwort für das Benutzerkonto. Ersetzen Sie `CLUSTERNAME` durch den Namen Ihres Clusters.

Die REST-API wird durch [Standardauthentifizierung](https://en.wikipedia.org/wiki/Basic_access_authentication)gesichert. Sie sollten Anforderungen immer über HTTPS (Secure HTTP) stellen, um sicherzustellen, dass Ihre Anmeldeinformationen sicher an den Server gesendet werden.

1. Legen Sie zur Verbesserung der Benutzerfreundlichkeit die unten aufgeführten Variablen fest. Dieses Beispiel basiert auf einer Windows-Umgebung, die nach Bedarf für Ihre Umgebung überarbeitet werden kann.

    ```cmd
    set CLUSTERNAME=
    set USERNAME=admin
    set PASSWORD=
    set SQLDATABASESERVERNAME=
    set SQLDATABASENAME=
    set SQLPASSWORD=
    set SQLUSER=sqluser
    ```

1. Verwenden Sie den folgenden Befehl in einer Befehlszeile, um zu überprüfen, ob Sie die Verbindung zum HDInsight-Cluster herstellen können:

    ```cmd
    curl -u %USERNAME%:%PASSWORD% -G https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/status
    ```

    Sie sollten eine Antwort empfangen, die in etwa der im Folgenden aufgeführten entspricht:

    ```json
    {"status":"ok","version":"v1"}
    ```

1. Geben Sie den folgenden Befehl an, um einen Sqoop-Auftrag zu übermitteln:

    ```cmd
    curl -u %USERNAME%:%PASSWORD% -d user.name=%USERNAME% -d command="export --connect jdbc:sqlserver://%SQLDATABASESERVERNAME%.database.windows.net;user=%SQLUSER%@%SQLDATABASESERVERNAME%;password=%PASSWORD%;database=%SQLDATABASENAME% --table log4jlogs --export-dir /example/data/sample.log --input-fields-terminated-by \0x20 -m 1" -d statusdir="wasb:///example/data/sqoop/curl" https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/sqoop
    ```

    Folgende Parameter werden in diesem Befehl verwendet:

   * **-d**: Da `-G` nicht verwendet wird, verwendet die Anforderung standardmäßig die POST-Methode. `-d` gibt die Datenwerte an, die mit der Anforderung gesendet werden.

       * **user.name** – Der Benutzer, der den Befehl ausführt

       * **command** – Der auszuführende Sqoop-Befehl.

       * **statusdir** – Das Verzeichnis, in das die Statusangaben für diesen Auftrag geschrieben werden.

     Dieser Befehl gibt eine Auftrags-ID zurück, mit der der Status des Auftrags überprüft werden kann.

       ```json
       {"id":"job_1415651640909_0026"}
       ```

1. Verwenden Sie den folgenden Befehl, um den Status des Auftrags zu prüfen. Ersetzen Sie `JOBID` durch den Wert, der im vorherigen Schritt zurückgegeben wurde. Wenn der Rückgabewert z. B. `{"id":"job_1415651640909_0026"}` lautet, wäre `JOBID` entsprechend `job_1415651640909_0026`. Ändern Sie den Speicherort von `jq` nach Bedarf.

    ```cmd
    set JOBID=job_1415651640909_0026

    curl -G -u %USERNAME%:%PASSWORD% -d user.name=%USERNAME% https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/jobs/%JOBID% | C:\HDI\jq-win64.exe .status.state
    ```

    Wenn der Auftrag abgeschlossen ist, wird der Status **ERFOLGREICH** angezeigt.

   > [!NOTE]  
   > Diese Curl-Anforderung gibt ein JSON-Dokument (JavaScript Object Notation) mit Informationen zum Auftrag zurück. Mithilfe von "jq" wird nur der Statuswert abgerufen.

1. Sobald der Status des Auftrags zu **ERFOLGREICH** wechselt, können Sie die Ergebnisse des Auftrags aus Azure Blob Storage abrufen. Der mit der Abfrage übergebene Parameter `statusdir` enthält den Speicherort der Ausgabedatei. In diesem Fall ist dies `wasb:///example/data/sqoop/curl`. Diese Adresse speichert die Ausgabe des Auftrags im Verzeichnis `example/data/sqoop/curl` des Standardspeichercontainers, der von Ihrem HDInsight-Cluster verwendet wird.

    Sie können über das Azure-Portal auf die Blobs „stderr“ und „stdout“ zugreifen.

1. Um zu überprüfen, ob Daten exportiert wurden, führen Sie auf Ihrem SQL-Client die folgenden Abfragen zum Anzeigen der exportierten Daten aus:

    ```sql
    SELECT COUNT(*) FROM [dbo].[log4jlogs] WITH (NOLOCK);
    SELECT TOP(25) * FROM [dbo].[log4jlogs] WITH (NOLOCK);
    ```

## <a name="limitations"></a>Einschränkungen

* Massenexport: Bei Linux-basiertem HDInsight unterstützt der zum Exportieren von Daten nach Microsoft SQL Server oder Azure SQL-Datenbank verwendete Sqoop-Connector derzeit keine Masseneinfügungen.
* Batchverarbeitung: Wenn Sie in Linux-basiertem HDInsight zum Ausführen von Einfügevorgängen den Schalter `-batch` verwenden, führt Sqoop mehrere Einfügevorgänge aus, anstatt diese zu einem Batch zusammenzufassen.

## <a name="summary"></a>Zusammenfassung

Wie in diesem Dokument veranschaulicht, können Sie unformatierte HTTP-Anforderungen dazu verwenden, um Sqoop-Aufträge auf dem HDInsight-Cluster auszuführen, zu überwachen und deren Ergebnisse anzuzeigen.

Weitere Informationen zur REST-Schnittstelle, die in diesem Artikel verwendet wird, finden Sie unter <a href="https://sqoop.apache.org/docs/1.99.3/RESTAPI.html" target="_blank">Sqoop REST API Guide</a> (Leitfaden zur Sqoop-REST-API).

## <a name="next-steps"></a>Nächste Schritte

[Verwenden von Sqoop mit Hadoop in HDInsight](hdinsight-use-sqoop.md)

Weitere HDInsight-Artikel zu curl:

* [Erstellen von Apache Hadoop-Clustern mithilfe der Azure-REST-API](../hdinsight-hadoop-create-linux-clusters-curl-rest.md)
* [Ausführen von Apache Hive-Abfragen mit Apache Hadoop in HDInsight mit REST](apache-hadoop-use-hive-curl.md)
* [Ausführen von MapReduce-Aufträgen mit Apache Hadoop in HDInsight mithilfe von REST](apache-hadoop-use-mapreduce-curl.md)
