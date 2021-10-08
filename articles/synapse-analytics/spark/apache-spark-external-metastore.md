---
title: Verwenden eines externen Hive-Metastores für Azure Synapse Spark-Pool
description: Erfahren Sie, wie man einen externen Hive-Metastores für Azure Synapse Spark-Pool einrichtet.
keywords: Externer Hive-Metastore, Freigabe, Synapse
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.author: yanacai
author: yanancai
ms.date: 09/08/2021
ms.openlocfilehash: 9a2d822d84d6356fbf34b45e24501e4426f1b06d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128700190"
---
# <a name="use-external-hive-metastore-for-synapse-spark-pool-preview"></a>Verwenden eines externen Hive-Metastores für Azure Synapse Spark-Pool (Vorschau)

Azure Synapse Analytics ermöglicht es Apache Spark-Pools im selben Arbeitsbereich, einen verwalteten HMS-kompatiblen Metastore (Hive Metastore Service) als ihren Katalog freizugeben. Wenn Kunden den Hive-Katalog außerhalb des Arbeitsbereichs beibehalten und Katalogobjekte für andere Compute-Engines außerhalb des Arbeitsbereichs freigeben möchten, z. B. HDInsight und Azure Databricks, können sie eine Verbindung mit einem externen Hive-Metastore herstellen. In diesem Artikel erfahren Sie, wie man Synapse Spark mit einem externen Apache Hive Metastore verbindet. 

## <a name="supported-hive-metastore-versions"></a>Unterstützte Hive-Metastore-Versionen

Das Merkmal funktioniert sowohl mit Spark 2.4 als auch mit Spark 3.0. Die folgende Tabelle zeigt die unterstützten HMS-Versionen (Hive Metastore Service) für jede Spark-Version.


|Spark-Version|HMS 1.2.X|HMS 2.1.X|HMS 2.3.x|HMS 3.1.X|
|--|--|--|--|--|
|2.4|Ja|Ja|Ja|Nein|
|3|Ja|Ja|Ja|Ja|

## <a name="set-up-hive-metastore-linked-service"></a>Einrichten eines verknüpften Diensts für den Hive-Metastore

> [!NOTE]
> Nur die Azure SQL-Datenbank wird als externer Hive-Metastore unterstützt.

Führen Sie die folgenden Schritte aus, um einen verknüpften Dienst zum externen Hive-Metastore im Synapse-Arbeitsbereich einzurichten.

1.  Öffnen Sie Synapse Studio, navigieren Sie links zu **„Verwalten“ > „Verknüpfte Dienste“** , und klicken Sie dann auf **Neu**, um einen neuen verknüpften Dienst zu erstellen.

    :::image type="content" source="./media/use-external-metastore/set-up-hive-metastore-linked-service.png" alt-text="Einrichten eines verknüpften Diensts für den Hive-Metastore" border="true":::

2.  Wählen Sie **Azure SQL-Datenbank** aus, und klicken Sie auf **Weiter**.

3.  Geben Sie den **Namen** des verknüpften Dienstes an. Notiere Sie sich den Namen des verknüpften Dienstes. Diese Informationen werden in Kürze zum Konfigurieren von Spark verwendet.

4.  Sie können entweder die Azure SQL-Datenbank für den externen Hive-Metastore aus der Azure-Abonnementliste auswählen oder die Informationen manuell eingeben.

5.  Derzeit wird nur die SQL-Authentifizierung als Authentifizierungstyp unterstützt. Geben Sie den **Benutzernamen** und das **Kennwort** an, um die Verbindung einzurichten.

6.  Klicken Sie auf **Verbindung testen**, um den Benutzernamen und das Kennwort zu überprüfen.

7.  Klicken Sie auf **Erstellen**, um den verknüpften Dienst zu erstellen. 

### <a name="test-connection-and-get-the-metastore-version-in-notebook"></a>Testen Sie die Verbindung rufen Sie die Metastore-Version in Notebook ab
Einige Einstellungen für die Netzwerksicherheitsregel können den Zugriff vom Spark-Pool auf die externe Hive-Metastore-Datenbank blockieren. Führen Sie vor dem Konfigurieren des Spark-Pools den folgenden Code in einem beliebigen Notebook des Spark-Pools aus, um die Verbindung mit der externen Hive-Metastore-Datenbank zu testen. 

Sie können Ihre Hive-Metastoreversion auch über die Ausgabeergebnissen abrufen. Die Hive-Metastoreversion wird in der Spark-Konfiguration verwendet.

```
%%spark 
import java.sql.DriverManager 
/** this url could be copied from Azure portal > Azure SQL database > Connection strings > JDBC **/ 
val url = s"jdbc:sqlserver://{your_servername_here}.database.windows.net:1433;database={your_database_here};user={your_username_here};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;" 
try { 
    val connection = DriverManager.getConnection(url) 
    val result = connection.createStatement().executeQuery("select t.SCHEMA_VERSION from VERSION t") 
    result.next(); 
    println(s"Successful to test connection. Hive metastore version is ${result.getString(1)}") 
} catch { 
    case ex: Throwable =>println(s"Failed to establish connection:\n $ex") 
}  
```

## <a name="configure-spark-to-use-the-external-hive-metastore"></a>Konfigurieren Sie Spark für die Verwendung des externen Hive-Metastores
Nachdem Sie den verknüpften Dienst erfolgreich für den externen Hive-Metastore erstellt haben, müssen Sie einige Konfigurationen in Spark einrichten, um den externen Hive-Metastore zu verwenden. Sie können die Konfiguration sowohl auf Spark-Poolebene als auch auf Spark-Sitzungsebene einrichten. 

Hier finden Sie die Konfigurationen und Beschreibungen:

> [!NOTE]
> Die Standardversion des Hive-Metastore ist Version 2.3. Sie müssen `spark.sql.hive.metastore.version` und `spark.sql.hive.metastore.jars` nicht festlegen, wenn Sie Hive-Metastore Version 2.3 verwenden. Nur `spark.hadoop.hive.synapse.externalmetastore.linkedservice.name` ist erforderlich.

|Spark-Konfiguration|Beschreibung|
|--|--|
|`spark.sql.hive.metastore.version`|Unterstützte Versionen: <ul><li>`1.2`</li><li>`2.1`</li><li>`3.1`</li></ul> Stellen Sie sicher, dass Sie die ersten zwei Teile ohne den dritten Teil verwenden|
|`spark.sql.hive.metastore.jars`|<ul><li>Version 1.2: `/opt/hive-metastore/lib-1.2/*:/usr/hdp/current/hadoop-client/lib/*` </li><li>Version 2.1: `/opt/hive-metastore/lib-2.1/*:/usr/hdp/current/hadoop-client/lib/*` </li><li>Version 3.1: `/opt/hive-metastore/lib-3.1/*:/usr/hdp/current/hadoop-client/lib/*`</li></ul>|
|`spark.hadoop.hive.synapse.externalmetastore.linkedservice.name`|Name des von Ihnen erstellten verknüpften Dienstes zur Azure SQL-Datenbank.|

### <a name="configure-spark-pool"></a>Konfigurieren eines Spark-Pools 
Wenn Sie den Spark-Pool erstellen, geben Sie auf der Registerkarte **Zusätzliche Einstellungen** die folgenden Konfigurationen in einer Textdatei an, und laden Sie diese im Abschnitt **Apache Spark-Konfiguration** hoch. Sie können auch das Kontextmenü für einen vorhandenen Spark-Pool verwenden. Wählen Sie „Apache Spark-Konfiguration“, um diese Konfigurationen hinzuzufügen.

   :::image type="content" source="./media/use-external-metastore/config-spark-pool.png" alt-text="Konfigurieren des Spark-Pools":::

Aktualisieren Sie die Metastoreversion und den Namen des verknüpften Dienstes und speichern Sie die folgenden Konfigurationen in einer Textdatei für die Spark-Poolkonfiguration:

```
spark.sql.hive.metastore.version <your hms version, Make sure you use the first 2 parts without the 3rd part>
spark.hadoop.hive.synapse.externalmetastore.linkedservice.name <your linked service name to Azure SQL DB>
spark.sql.hive.metastore.jars /opt/hive-metastore/lib-<your hms version, 2 parts>/*:/usr/hdp/current/hadoop-client/lib/*
```

Hier sehen Sie ein Beispiel für die Metastoreversion 2.1 mit einem verknüpften Dienst namens „HiveCatalog21“:

```
spark.sql.hive.metastore.version 2.1
spark.hadoop.hive.synapse.externalmetastore.linkedservice.name HiveCatalog21
spark.sql.hive.metastore.jars /opt/hive-metastore/lib-2.1/*:/usr/hdp/current/hadoop-client/lib/*
```

### <a name="configure-a-spark-session"></a>Konfigurieren einer Spark-Sitzung
Wenn Sie Ihren Spark-Pool nicht konfigurieren möchten, können Sie mithilfe des Magic-Befehls „%%configure“ auch die Spark-Sitzung in Notebook konfigurieren. Hier ist der Code angegeben. Dieselbe Konfiguration kann auch auf einen Spark-Batchauftrag angewendet werden. 

```
%%configure -f
{
    "conf":{
        "spark.sql.hive.metastore.version":"<your linked service name to Azure SQL DB>",
        "spark.hadoop.hive.synapse.externalmetastore.linkedservice.name":"<your linked service name to Azure SQL DB>",
        "spark.sql.hive.metastore.jars":"/opt/hive-metastore/lib-<your hms version, 2 parts>/*:/usr/hdp/current/hadoop-client/lib/*"
    }
}
```

### <a name="run-queries-to-verify-the-connection"></a>Führen Sie Abfragen durch, um die Verbindung zu überprüfen
Versuchen Sie nach dem Festlegen all dieser Einstellungen, Katalogobjekte aufzulisten, indem Sie die folgende Abfrage im Spark-Notebook ausführen, um die Verbindung mit dem externen Hive-Metastore zu überprüfen.
```python
spark.sql("show databases").show()
```

## <a name="set-up-storage-connection"></a>Einrichten einer Speicherverbindung
Der verknüpfte Dienst zur Datenbank des Hive-Metastores bietet nur Zugriff auf Hive-Katalogmetadaten. Um die vorhandenen Tabellen abzufragen, müssen Sie eine Verbindung mit dem Speicherkonto einrichten, in dem auch die zugrunde liegenden Daten für Ihre Hive-Tabellen gespeichert werden.

### <a name="set-up-connection-to-adls-gen-2"></a>Einrichten der Verbindung mit ADLS Gen 2
#### <a name="workspace-primary-storage-account"></a>Primäres Speicherkonto des Arbeitsbereichs
Wenn die zugrunde liegenden Daten Ihrer Hive-Tabellen im primären Speicherkonto Ihres Arbeitsbereichs gespeichert werden, sind keine zusätzlichen Einstellungen erforderlich. Dies funktioniert nur, solange Sie bei der Arbeitsbereichserstellung die Anweisungen zum Einrichten des Speichers befolgt haben.

#### <a name="other-adls-gen-2-account"></a>Anderes ADLS Gen 2-Konto
Wenn die zugrunde liegenden Daten Ihrer Hive-Kataloge in einem anderen ADLS Gen 2-Konto gespeichert sind, müssen Sie sicherstellen, dass die Benutzer, die Spark-Abfragen ausführen, über die Rolle **Mitwirkender an Storage-Blobdaten** für das ADLS Gen2-Speicherkonto verfügen. 

### <a name="set-up-connection-to-blob-storage"></a>Einrichten der Verbindung mit Blob Storage
Wenn die zugrunde liegenden Daten Ihrer Hive-Tabellen im Azure Blob-Speicherkonto gespeichert sind, führen Sie die folgenden Schritte aus, um die Verbindung einzurichten:

1.  Öffnen Sie Synapse Studio, navigieren Sie zu **„Daten“ > Registerkarte „Verknüpft“ > Schaltfläche „Hinzufügen“**  > **Verbindung mit externen Daten herstellen**.

    :::image type="content" source="./media/use-external-metastore/connect-to-storage-account.png" alt-text="Herstellen einer Verbindung mit dem Speicherkonto" border="true":::

2.  Wählen Sie **Azure Blob Storage** aus, und klicken Sie auf **Weiter**.
3.  Geben Sie den **Namen** des verknüpften Dienstes an. Notieren Sie sich den Namen des verknüpften Dienstes. Diese Informationen werden in Kürze in der Spark-Sitzungskonfiguration verwendet.
4.  Wählen Sie das Azure Blob Storage-Konto aus. Stellen Sie sicher, dass **Kontoschlüssel** als Authentifizierungsmethode ausgewählt ist. Derzeit kann Spark Pool nur über den Kontoschlüssel auf das Blob Storage-Konto zugreifen.
5.  Klicken Sie auf **Verbindung testen** und dann auf **Erstellen**.
6.  Nachdem Sie den verknüpften Dienst für das Blob Storage-Konto erstellt haben, stellen Sie beim Ausführen von Spark-Abfragen sicher, dass Sie den folgenden Spark-Code im Notebook ausführen, um Zugriff auf das Blob Storage-Konto für die Spark-Sitzung zu erhalten. Weitere Informationen dazu, warum dies erforderlich ist, finden Sie [hier](./apache-spark-secure-credentials-with-tokenlibrary.md).

```
%%pyspark
blob_account_name = "<your blob storage account name>"
blob_container_name = "<your container name>"
from pyspark.sql import SparkSession
sc = SparkSession.builder.getOrCreate()
token_library = sc._jvm.com.microsoft.azure.synapse.tokenlibrary.TokenLibrary
blob_sas_token = token_library.getConnectionString("<blob storage linked service name>")
spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name), blob_sas_token)
```

Nach dem Einrichten von Speicherverbindungen können Sie die vorhandenen Tabellen im Hive-Metastore abfragen.

## <a name="known-limitations"></a>Bekannte Einschränkungen

- Der Synapse Studio-Objekt-Explorer zeigt weiterhin Objekte im verwalteten Synapse-Metastore anstelle des externen HMS an. Wir arbeiten an einer Verbesserung hierfür.
- [SQL <-> Spark Synchronisierung](../sql/develop-storage-files-spark-tables.md) funktioniert nicht, wenn Sie externen HMS benutzen.  
- Nur Azure SQL-Datenbank wird als externe Datenbank des Hive-Metastores unterstützt. Nur die SQL-Autorisierung wird unterstützt.
- Spark funktioniert derzeit nur mit externen Hive-Tabellen sowie nicht transaktionalen bzw. nicht mit ACID verwalteten Hive-Tabellen. Aktuell werden transaktionale bzw. mit Hive ACID verwaltete Tabellen nicht unterstützt.
- Die Apache Ranger-Integration wird derzeit nicht unterstützt.

## <a name="troubleshooting"></a>Problembehandlung
### <a name="see-below-error-when-querying-a-hive-table-with-data-stored-in-blob-storage"></a>Der folgende Fehler wird beim Abfragen einer Hive-Tabelle mit in Blob Storage gespeicherten Daten angezeigt
```
Py4JJavaError : An error occurred while calling o241.load. : org.apache.hadoop.fs.azure.AzureException: org.apache.hadoop.fs.azure.AzureException: No credentials found for account demohdicatalohdistorage.blob.core.windows.net in the configuration, and its container demohdicatalog-2021-07-15t23-42-51-077z is not accessible using anonymous credentials. Please check if the container exists first. If it is not publicly available, you have to provide account credentials.
```

Wenn Sie die Schlüsselauthentifizierung für Ihr Speicherkonto über den verknüpften Dienst verwenden, müssen Sie einen zusätzlichen Schritt ausführen, um das Token für die Spark-Sitzung zu erhalten. Führen Sie den folgenden Code aus, um Ihre Spark-Sitzung zu konfigurieren, bevor Sie die Abfrage ausführen. Mehr darüber, warum dies erforderlich ist, erfahren Sie hier.

```
%%pyspark
blob_account_name = "<your blob storage account name>"
blob_container_name = "<your container name>"
from pyspark.sql import SparkSession
sc = SparkSession.builder.getOrCreate()
token_library = sc._jvm.com.microsoft.azure.synapse.tokenlibrary.TokenLibrary
blob_sas_token = token_library.getConnectionString("<blob storage linked service name>")
spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name), blob_sas_token)
```

### <a name="see-below-error-when-query-a-table-stored-in-adls-gen2-account"></a>Der folgende Fehler wird angezeigt, wenn Sie eine in einem ADLS Gen2-Konto gespeicherte Tabelle abfragen
```
Py4JJavaError : An error occurred while calling o305.load. : Operation failed: "This request is not authorized to perform this operation using this permission.", 403, HEAD
```

Dies kann daran liegen, dass Benutzer, die die Spark-Abfrage ausführen, nicht über die erforderlichen Zugriffsrechte für das zugrunde liegende Speicherkonto verfügen. Stellen Sie sicher, dass die Benutzer, die Spark-Abfragen ausführen, über die Rolle **Mitwirkender an Storage-Blobdaten** für das ADLS Gen2-Speicherkonto verfügen. Dieser Schritt kann später nach dem Erstellen des verknüpften Diensts ausgeführt werden.

### <a name="hms-schema-related-settings"></a>Einstellungen im Zusammenhang mit dem HMS-Schema 
Um das Ändern des HMS-Back-End-Schemas bzw. der Version zu vermeiden, werden die folgenden Hive-Konfigurationen standardmäßig vom System festgelegt: 
```
spark.hadoop.hive.metastore.schema.verification true 
spark.hadoop.hive.metastore.schema.verification.record.version false 
spark.hadoop.datanucleus.fixedDatastore true 
spark.hadoop.datanucleus.schema.autoCreateAll false 
```

Wenn Ihre HMS-Version 1.2.1 oder 1.2.2 lautet, liegt in Hive ein Problem vor, wo ausschließlich 1.2.0 verlangt wird, wenn Sie spark.hadoop.hive.metastore.schema.verification auf „true“ festlegen. Ein Vorschlag ist, entweder Ihre HMS-Version in Version 1.2.0 zu ändern oder die folgenden beiden Konfigurationen zu überschreiben, um dieses Problem zu umgehen:

```
spark.hadoop.hive.metastore.schema.verification false 
spark.hadoop.hive.synapse.externalmetastore.schema.usedefault false
```

Wenn Sie Ihre HMS-Version migrieren müssen, wird die Verwendung des [Hive-Schematools](https://cwiki.apache.org/confluence/display/Hive/Hive+Schema+Tool) empfohlen. Wenn der HMS von HDInsight-Clustern verwendet wurde, sollten Sie die [bereitgestellte HDI-Version](https://docs.microsoft.com/azure/hdinsight/interactive-query/apache-hive-migrate-workloads) verwenden. 

### <a name="when-sharing-the-metastore-with-hdinsight-40-spark-clusters-i-cannot-see-the-tables"></a>Beim Freigeben des Metastores für Spark-Cluster in HDInsight 4.0 kann ich die Tabellen nicht sehen
Wenn Sie den Hive-Katalog für einen Spark-Cluster in HDInsight 4.0 freigeben möchten, stellen Sie sicher, dass Ihre Eigenschaft `spark.hadoop.metastore.catalog.default` in Synapse Spark dem Wert in HDInsight Spark entspricht. Standardwert: `Spark`.

### <a name="when-sharing-the-hive-metastore-with-hdinsight-40-hive-clusters-i-can-list-the-tables-successfully-but-only-get-empty-result-when-i-query-the-table"></a>Beim Freigeben des Hive-Metastores für Hive-Cluster in HDInsight 4.0 kann ich die Tabellen erfolgreich auflisten, erhalten beim Abfragen der Tabelle aber nur ein leeres Ergebnis
Wie im Abschnitt zu den Einschränkungen erwähnt, unterstützt der Synapse Spark-Pool nur externe Hive-Tabellen sowie nicht transaktionale bzw. nicht mit ACID verwaltete Tabellen. Transaktionale bzw. mit ACID verwaltete Hive-Tabellen werden derzeit nicht unterstützt. Standardmäßig werden bei Hive-Clustern in HDInsight 4.0 alle verwalteten Tabellen als transaktionale bzw. mit ACID verwaltete Tabellen erstellt, weshalb Sie leere Ergebnisse erhalten, wenn Sie diese Tabellen abfragen. 