---
title: Problembehandlung bei der Hive-Migration von 3.6 zu 4.0 – Azure HDInsight
description: Leitfaden zur Problembehandlung für die Migration von Hive-Workloads von HDInsight 3.6 zu 4.0
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/12/2021
ms.openlocfilehash: eecf8cd36c1f631176ce836523be802d751ed55b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128625960"
---
# <a name="troubleshooting-guide-for-migration-of-hive-workloads-from-hdinsight-36-to-hdinsight-40"></a>Leitfaden zur Problembehandlung für die Migration von Hive-Workloads von HDInsight 3.6 zu HDInsight 4.0

Dieser Artikel enthält Antworten auf einige der häufigsten Probleme, mit denen Kunden bei der Migration von Hive-Workloads von HDInsight 3.6 zu HDInsight 4.0 konfrontiert werden.

## <a name="reduce-latency-when-running-describe-table_name"></a>Reduzieren der Latenz bei der Ausführung von `DESCRIBE TABLE_NAME`

Problemumgehung:

* Erhöhen Sie die maximale Anzahl von Objekten (Tabellen/Partitionen), die in einem Batch aus dem Metastore abgerufen werden können. Legen Sie den Wert auf eine große Zahl fest (Standardwert: 300), bis zufriedenstellende Latenzzeiten erreicht sind. Je höher die Anzahl ist, desto weniger Roundtrips sind zum Hive-Metastore-Server erforderlich. Dies kann jedoch auch zu einem höheren Arbeitsspeicherbedarf auf der Clientseite führen.

  `hive.metastore.batch.retrieve.max=2000`

* Starten Sie Hive und alle nicht mehr aktuellen Dienste neu.

## <a name="unable-to-query-gzipped-text-file-if-skipheaderlinecount-and-skipfooterlinecount-are-set-for-table"></a>GZIP-Textdatei kann nicht abgefragt werden, wenn „skip.header.line.count“ und „skip.footer.line.count“ für die Tabelle festgelegt sind

Das Problem wurde in Interactive Query 4.0 behoben, jedoch noch nicht in Interactive Query 3.1.0.

Problemumgehung:
* Erstellen Sie eine Tabelle ohne Verwendung von ```"skip.header.line.count"="1"``` und ```"skip.footer.line.count"="1"```, und erstellen Sie dann eine Sicht aus der ursprünglichen Tabelle, die die Kopf-/Fußzeile in der Abfrage ausschließt.

## <a name="unable-to-use-unicode-characters"></a>Unicode-Zeichen können nicht verwendet werden

Problemumgehung:
1. Stellen Sie eine Verbindung zur Hive-Metastore-Datenbank für Ihren Cluster her.

2. Erstellen Sie mit dem folgenden Befehl eine Sicherung der Tabellen `TBLS` und `TABLE_PARAMS`:
    ```sql
        select * into tbls_bak from tbls;
        select * into table_params_bak from table_params;
    ```

3. Ändern Sie die betroffenen Spaltentypen manuell in `nvarchar(max)`.
    ```sql 
        alter table TABLE_PARAMS alter column PARAM_VALUE nvarchar(max);
        alter table TBLS alter column VIEW_EXPANDED_TEXT nvarchar(max) null;    
        alter table TBLS alter column VIEW_ORIGINAL_TEXT nvarchar(max) null;
    ```

## <a name="create-table-as-select-ctas-creates-a-new-table-with-same-uuid"></a>CTAS (Create Table As Select) erstellt eine neue Tabelle mit derselben UUID

Hive 3.1 (HDInsight 4.0) umfasst eine integrierte UDF zum Generieren eindeutiger UUIDs. Die Hive-UUID()-Methode generiert auch mit CTAS eindeutige IDs. Sie können diese wie folgt verwenden.
```hql
create table rhive as
select uuid() as UUID
from uuid_test
```

## <a name="hive-job-output-format-differs-from-hdinsight-36"></a>Ausgabeformat von Hive-Aufträgen weicht von HDInsight 3.6 ab

Dies liegt am Unterschied von WebHCat(Templeton) zwischen HDInsight 3.6 und HDInsight 4.0.

* Hive-REST-API: ```arg=--showHeader=false -d arg=--outputformat=tsv2 -d``` hinzufügen

* .NET SDK: Argumente von ```HiveJobSubmissionParameters``` initialisieren
    ```csharp
    List<string> args = new List<string> { { "--showHeader=false" }, { "--outputformat=tsv2" } };
                var parameters = new HiveJobSubmissionParameters
                {
                    Query = "SELECT clientid,market from hivesampletable LIMIT 10",
                    Defines = defines,
                    Arguments = args
                };
    ```

## <a name="reduce-hive-internal-table-creation-latency"></a>Reduzieren der Latenz beim Erstellen interner Hive-Tabellen

1. Löschen Sie unter „Advanced hive-site“ und „Advanced hivemetastore-site“ den Wert ```org.apache.hive.hcatalog.listener.DbNotificationListener``` für ```hive.metastore.transactional.event.listeners```. 

2. Entfernen Sie einen ggf. für ```hive.metastore.event.listeners``` vorhandenen Wert.

3. DbNotificationListener ist nur erforderlich, wenn Sie REPL-Befehle verwenden, und kann andernfalls problemlos entfernt werden.

    :::image type="content" source="./media/apache-hive-40-migration-guide/hive-reduce-internal-table-creation-latency.png" alt-text="Reduzieren der internen Tabellenlatenz in HDInsight 4.0" border="true":::

## <a name="change-hive-default-table-location"></a>Ändern des Hive-Standardtabellenspeicherorts

Diese Verhaltensänderung in HDInsight 4.0 (Hive 3.1) ist entwurfsbedingt. Der Hauptgrund für diese Änderung ist die Dateiberechtigungssteuerung. 

Geben Sie den Speicherort in der Create Table-Anweisung an, um externe Tabellen in einem benutzerdefinierten Speicherort zu erstellen.

## <a name="disable-acid-in-hdinsight-40"></a>Deaktivieren von ACID in HDInsight 4.0

Es wird empfohlen, ACID in HDInsight 4.0 zu aktivieren. Die meisten der neuesten Verbesserungen, sowohl funktionale Verbesserungen als auch Leistungsverbesserungen, in Hive werden nur für ACID-Tabellen verfügbar gemacht.

Schritte zum Deaktivieren von ACID in HDInsight 4.0:
1. Ändern Sie die folgenden Hive-Konfigurationen in Ambari:

    ```text
    hive.strict.managed.tables=false
    hive.support.concurrency=false; 
    hive.txn.manager=org.apache.hadoop.hive.ql.lockmgr.DummyTxnManager;
    hive.enforce.bucketing=false;
    hive.compactor.initiator.on=false;
    hive.compactor.worker.threads=0;
    hive.create.as.insert.only=false;
    metastore.create.as.acid=false;
    ```

2. Starten Sie den Hive-Dienst neu.

> [!IMPORTANT]
> Microsoft rät davon ab, die gleichen Daten/Speicher für Hive-verwaltete Tabellen von HDInsight 3.6 und HDInsight 4.0 zu nutzen. Dies ist ein nicht unterstütztes Szenario.

* Normalerweise sollten die oben aufgeführten Konfigurationen sogar vor dem Erstellen von Hive-Tabellen im HDInsight 4.0-Cluster festgelegt werden. ACID sollte nicht deaktiviert werden, nachdem verwaltete Tabellen erstellt wurden. Dies kann zu Datenverlusten oder inkonsistenten Ergebnissen führen. Daher wird empfohlen, dies einmalig beim Erstellen eines neuen Clusters festzulegen und danach nicht mehr zu ändern.

* Das Deaktivieren von ACID nach dem Erstellen von Tabellen ist riskant. Führen Sie die folgenden Schritte aus, um potenzielle Datenverluste oder Inkonsistenz zu vermeiden, sofern Sie dies jedoch unbedingt tun möchten:

    1. Erstellen Sie eine externe Tabelle mit demselben Schema, und kopieren Sie die Daten aus der ursprünglichen verwalteten Tabelle mit dem CTAS-Befehl ```create external table e_t1 select * from m_t1```.    
    2. Löschen Sie die verwaltete Tabelle mit ```drop table m_t1```.
    3. Deaktivieren Sie ACID mit den vorgeschlagenen Konfigurationen.        
    4. Erstellen Sie „m_t1“ erneut, und kopieren Sie Daten mit dem CTAS-Befehl ```create table m_t1 select * from e_t1``` aus der externen Tabelle.        
    5. Löschen Sie die externe Tabelle mit ```drop table e_t1```.

Achten Sie darauf, dass alle verwalteten Tabellen in externe Tabellen konvertiert und gelöscht werden, bevor Sie ACID deaktivieren. Vergleichen Sie außerdem das Schema und die Daten nach jedem Schritt, um Unstimmigkeiten zu vermeiden.

## <a name="create-hive-external-table-with-755-permission"></a>Erstellen einer Hive-externen Tabelle mit 755-Berechtigung

Dieses Problem kann mit einer der beiden folgenden Optionen behoben werden:

1. Legen Sie die Ordnerberechtigung manuell auf 757 oder 777 fest, damit Hive-Benutzer in das Verzeichnis schreiben können.

2. Ändern Sie den „Hive Authorization Manager“ von ```org.apache.hadoop.hive.ql.security.authorization.StorageBasedAuthorizationProvider``` in ```org.apache.hadoop.hive.ql.security.authorization.MetaStoreAuthzAPIAuthorizerEmbedOnly```.

MetaStoreAuthzAPIAuthorizerEmbedOnly bewirkt, dass Sicherheitsüberprüfungen deaktiviert werden, da der Hive-Metastore nicht in HDInsight 4.0 eingebettet ist. Dies kann jedoch andere potenzielle Probleme mit sich bringen. Gehen Sie mit Bedacht vor, wenn Sie diese Option verwenden.

## <a name="permission-errors-in-hive-job-after-upgrading-to-hdinsight-40"></a>Berechtigungsfehler im Hive-Auftrag nach dem Upgrade auf HDInsight 4.0

* In HDInsight 4.0 werden alle Clusterformen mit Hive-Komponenten mit einem neuen Autorisierungsanbieter konfiguriert: 

  `org.apache.hadoop.hive.ql.security.authorization.StorageBasedAuthorizationProvider`

* HDFS-Dateiberechtigungen sollten dem Hive-Benutzer für die Datei zugewiesen werden, auf die zugegriffen wird. Die Fehlermeldung enthält erforderliche Details zum Beheben des Problems.

* Sie können auch zum Anbieter ```MetaStoreAuthzAPIAuthorizerEmbedOnly``` wechseln, der in Hive-Clustern von HDInsight 3.6 verwendet wird.

  `org.apache.hadoop.hive.ql.security.authorization.MetaStoreAuthzAPIAuthorizerEmbedOnly`

  :::image type="content" source="./media/apache-hive-40-migration-guide/hive-job-permission-errors.png" alt-text="Festlegen der Autorisierung auf MetaStoreAuthzAPIAuthorizerEmbedOnly" border="true":::

## <a name="unable-to-query-table-with-opencsvserde"></a>Tabellenabfrage mit OpenCSVSerde nicht möglich

Beim Lesen von Daten aus einer Tabelle im `csv`-Format kann eine Ausnahme wie die folgende ausgelöst werden:
```text
MetaException(message:java.lang.UnsupportedOperationException: Storage schema reading not supported)
```

Problemumgehung:

* Fügen Sie die Konfiguration `metastore.storage.schema.reader.impl`=`org.apache.hadoop.hive.metastore.SerDeStorageSchemaReader` in `Custom hive-site` über die Ambari-Benutzeroberfläche hinzu.

* Starten Sie alle nicht mehr aktuellen Hive-Dienste neu.

## <a name="next-steps"></a>Nächste Schritte

[!INCLUDE [troubleshooting next steps](../includes/hdinsight-troubleshooting-next-steps.md)]
