---
title: Problembehandlung für die Azure Synapse Analytics-, Azure SQL-Datenbank- und SQL Server-Connectors
titleSuffix: Azure Data Factory & Azure Synapse
description: Hier erfahren Sie, wie Sie Probleme mit den Azure Synapse Analytics-, Azure SQL-Datenbank- und SQL Server-Connectors in Azure Data Factory und Azure Synapse Analytics behandeln.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: f258ba3b8c62d0d290785bbf6a66cc7651286162
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/02/2021
ms.locfileid: "129390528"
---
# <a name="troubleshoot-the-azure-synapse-analytics-azure-sql-database-and-sql-server-connectors-in-azure-data-factory-and-azure-synapse"></a>Problembehandlung für die Azure Synapse Analytics-, Azure SQL-Datenbank- und SQL Server-Connectors in Azure Data Factory und Azure Synapse Analytics

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Dieser Artikel enthält Vorschläge zur Behandlung häufiger Probleme mit den Azure Synapse Analytics-, Azure SQL-Datenbank- und SQL Server-Connectors in Azure Data Factory Azure Synapse.

## <a name="error-code-sqlfailedtoconnect"></a>Fehlercode: SqlFailedToConnect

- **Meldung**: `Cannot connect to SQL Database: '%server;', Database: '%database;', User: '%user;'. Check the linked service configuration is correct, and make sure the SQL Database firewall allows the integration runtime to access.`
- **Ursachen und Empfehlungen:** Dieser Fehler kann verschiedene Ursachen haben. In der Liste unten finden Sie mögliche Ursachenanalysen und Empfehlungen.

    | Ursachenanalyse                                               | Empfehlung                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | Wenn die Fehlermeldung die Zeichenfolge „SqlErrorNumber=47073“ enthält, bedeutet dies für Azure SQL, dass der öffentliche Netzwerkzugriff in der Konnektivitätseinstellung verweigert wird. | Legen Sie in der Azure SQL-Firewall die Option **Öffentlichen Netzwerkzugriff verweigern** auf *Nein* fest. Weitere Informationen finden Sie unter [Azure SQL-Konnektivitätseinstellungen](../azure-sql/database/connectivity-settings.md#deny-public-network-access). |
    | Wenn die Fehlermeldung bei Azure SQL einen SQL-Fehlercode wie „SqlErrorNumber=[errorcode]“ enthält, finden Sie weitere Informationen im Leitfaden zur Problembehandlung von Azure SQL. | Eine Empfehlung finden Sie unter [Beheben von Konnektivitätsproblemen und anderen Fehlern mit Azure SQL-Datenbank und Azure SQL Managed Instance](../azure-sql/database/troubleshoot-common-errors-issues.md). |
    | Überprüfen Sie, ob Port 1433 in der Positivliste der Firewall enthalten ist. | Weitere Informationen finden Sie unter [Von SQL Server verwendete Ports](/sql/sql-server/install/configure-the-windows-firewall-to-allow-sql-server-access#ports-used-by-). |
    | Wenn die Fehlermeldung die Zeichenfolge „SqlException“ enthält, bedeutet dies für SQL-Datenbank, dass für einen bestimmten Vorgang ein Fehler aufgetreten ist. | Um weitere Informationen dazu zu erhalten, suchen Sie in [Datenbank-Engine-Fehler](/sql/relational-databases/errors-events/database-engine-events-and-errors) nach dem SQL-Fehlercode. Wenn Sie weitere Hilfe benötigen, wenden Sie sich an den Azure SQL-Support. |
    | Wenn dies ein vorübergehendes Problem ist (z. B. eine instabile Netzwerkverbindung), fügen Sie zur Behebung in der Aktivitätsrichtlinie einen Wiederholungsversuch hinzu. | Weitere Informationen finden Sie unter [Pipelines und Aktivitäten](./concepts-pipelines-activities.md#activity-policy). |
    | Wenn die Fehlermeldung die Zeichenfolge „Client with IP address '…' is not allowed to access the server“ enthält und Sie versuchen, eine Verbindung zu Azure SQL-Datenbank herzustellen, wird dieser Fehler in der Regel durch ein Problem mit der Firewall von Azure SQL-Datenbank verursacht. | Aktivieren Sie in der Azure SQL Server-Firewallkonfiguration die Option **Azure-Diensten und -Ressourcen den Zugriff auf diesen Server gestatten**. Weitere Informationen finden Sie unter [IP-Firewallregeln für Azure SQL-Datenbank und Azure Synapse](../azure-sql/database/firewall-configure.md). |
    
## <a name="error-code-sqloperationfailed"></a>Fehlercode: SqlOperationFailed

- **Meldung**: `A database operation failed. Please search error to get more details.`

- **Ursachen und Empfehlungen:** Dieser Fehler kann verschiedene Ursachen haben. In der Liste unten finden Sie mögliche Ursachenanalysen und Empfehlungen.

    | Ursachenanalyse                                               | Empfehlung                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | Wenn die Fehlermeldung die Zeichenfolge „SqlException“ enthält, löst SQL-Datenbank einen Fehler aus, der angibt, dass für einen bestimmten Vorgang ein Fehler aufgetreten ist. | Wenn der SQL-Fehler nicht eindeutig ist, versuchen Sie, die Datenbank auf den aktuellen Kompatibilitätsgrad „150“ zu ändern. Es können SQL-Fehler der neuesten Version ausgelöst werden. Weitere Informationen finden Sie in der [Dokumentation](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level#backwardCompat). <br/> Um weitere Informationen zur Problembehandlung von SQL-Problemen zu erhalten, suchen Sie in [Datenbank-Engine-Fehler](/sql/relational-databases/errors-events/database-engine-events-and-errors) nach dem SQL-Fehlercode. Wenn Sie weitere Hilfe benötigen, wenden Sie sich an den Azure SQL-Support. |
    | Wenn die Fehlermeldung die Zeichenfolge „PdwManagedToNativeInteropException“ enthält, wird der Fehler in der Regel durch einen Konflikt zwischen den Spaltengrößen von Quelle und Senke verursacht. | Überprüfen Sie die Größe der Quell- und Senkenspalten. Wenn Sie weitere Hilfe benötigen, wenden Sie sich an den Azure SQL-Support. |
    | Wenn die Fehlermeldung die Zeichenfolge „InvalidOperationException“ enthält, wird der Fehler normalerweise durch ungültige Eingabedaten verursacht. | Wenn Sie ermitteln möchten, in welcher Zeile das Problem auftritt, aktivieren Sie die Fehlertoleranzfunktion für die Kopieraktivität, die problematische Zeilen zur weiteren Untersuchung in den Speicher umleiten kann. Weitere Informationen finden Sie unter [Fehlertoleranz der Kopieraktivität](./copy-activity-fault-tolerance.md). |


## <a name="error-code-sqlunauthorizedaccess"></a>Fehlercode: SqlUnauthorizedAccess

- **Meldung**: `Cannot connect to '%connectorName;'. Detail Message: '%message;'`

- **Ursache:** Die Anmeldeinformationen sind falsch, oder das Anmeldekonto kann nicht auf SQL-Datenbank zugreifen.

- **Empfehlung**:  Überprüfen Sie, ob das Anmeldekonto über ausreichende Berechtigungen für den Zugriff auf die SQL-Datenbank verfügt.


## <a name="error-code-sqlopenconnectiontimeout"></a>Fehlercode: SqlOpenConnectionTimeout

- **Meldung**: `Open connection to database timeout after '%timeoutValue;' seconds.`

- **Ursache:** Das Problem könnte ein vorübergehender SQL-Datenbank-Fehler sein.

- **Empfehlung**:  Wiederholen Sie den Vorgang, um die Verbindungszeichenfolge für den verknüpften Dienst mit einem größeren Timeoutwert für die Verbindung zu aktualisieren.


## <a name="error-code-sqlautocreatetabletypemapfailed"></a>Fehlercode: SqlAutoCreateTableTypeMapFailed

- **Meldung**: `Type '%dataType;' in source side cannot be mapped to a type that supported by sink side(column name:'%columnName;') in autocreate table.`

- **Ursache:** Die Tabelle für die automatische Erstellung kann die Quellanforderung nicht erfüllen.

- **Empfehlung**:  Aktualisieren Sie den Spaltentyp in *mappings* (Zuordnungen), oder erstellen Sie manuell die Senkentabelle auf dem Zielserver.


## <a name="error-code-sqldatatypenotsupported"></a>Fehlercode: SqlDataTypeNotSupported

- **Meldung**: `A database operation failed. Check the SQL errors.`

- **Ursache:** Wenn das Problem in der SQL-Quelle auftritt und der Fehler im Zusammenhang mit dem SqlDateTime-Überlauf steht, liegt der Datenwert über dem logischen Eingabebereich (01.01.1753 12:00:00 Uhr bis 31.12.9999 23:59:59 Uhr).

- **Empfehlung**:  Wandeln Sie den Typ in der SQL-Quellabfrage in eine Zeichenfolge um, oder ändern Sie in der Spaltenzuordnung der Kopieraktivität den Spaltentyp in *String*.

- **Ursache:** Wenn das Problem in der SQL-Senkentabelle auftritt und der Fehler im Zusammenhang mit dem SqlDateTime-Überlauf steht, liegt der Datenwert über dem zulässigen Bereich in der Senkentabelle.

- **Empfehlung**:  Aktualisieren Sie den entsprechenden Spaltentyp in der Senkentabelle auf den Typ *datetime2*.


## <a name="error-code-sqlinvaliddbstoredprocedure"></a>Fehlercode: SqlInvalidDbStoredProcedure

- **Meldung**: `The specified Stored Procedure is not valid. It could be caused by that the stored procedure doesn't return any data. Invalid Stored Procedure script: '%scriptName;'.`

- **Ursache:** Die angegebene gespeicherte Prozedur ist ungültig. Dies kann daran liegen, dass die gespeicherte Prozedur keine Daten zurückgibt.

- **Empfehlung**:  Überprüfen Sie die gespeicherte Prozedur mithilfe von SQL-Tools. Stellen Sie sicher, dass die gespeicherte Prozedur Daten zurückgeben kann.


## <a name="error-code-sqlinvaliddbquerystring"></a>Fehlercode: SqlInvalidDbQueryString

- **Meldung**: `The specified SQL Query is not valid. It could be caused by that the query doesn't return any data. Invalid query: '%query;'`

- **Ursache:** Die angegebene SQL-Abfrage ist ungültig. Dies kann daran liegen, dass die Abfrage keine Daten zurückgibt.

- **Empfehlung**:  Überprüfen Sie die SQL-Abfrage mithilfe von SQL-Tools. Stellen Sie sicher, dass die Abfrage Daten zurückgeben kann.


## <a name="error-code-sqlinvalidcolumnname"></a>Fehlercode: SqlInvalidColumnName

- **Meldung**: `Column '%column;' does not exist in the table '%tableName;', ServerName: '%serverName;', DatabaseName: '%dbName;'.`

- **Ursache:** Die Spalte kann nicht gefunden werden, weil die Konfiguration möglicherweise falsch ist.

- **Empfehlung**:  Überprüfen Sie die Spalte in der Abfrage: *structure* (Struktur) im Dataset und *mappings* (Zuordnungen) in der Aktivität.


## <a name="error-code-sqlbatchwritetimeout"></a>Fehlercode: SqlBatchWriteTimeout

- **Meldung**: `Timeouts in SQL write operation.`

- **Ursache:** Das Problem kann durch einen vorübergehenden SQL-Datenbank-Fehler verursacht werden.

- **Empfehlung**:  Wiederholen Sie den Vorgang. Sollte das Problem weiterhin bestehen, wenden Sie sich an den Azure SQL-Support.


## <a name="error-code-sqlbatchwritetransactionfailed"></a>Fehlercode: SqlBatchWriteTransactionFailed

- **Meldung**: `SQL transaction commits failed.`

- **Ursache:** Wenn die Ausnahmedetails konsistent ein Transaktionstimeout angeben, ist die Netzwerklatenz zwischen Integration Runtime und Datenbank höher als der Standardschwellenwert von 30 Sekunden.

- **Empfehlung**:  Aktualisieren Sie die Verbindungszeichenfolge für den verknüpften SQL-Dienst mit einem Wert für *Verbindungstimeout*, der gleich oder größer als 120 ist, und führen Sie die Aktivität erneut aus.

- **Ursache:** Wenn die Ausnahmedetails zeitweilig angeben, dass die SQL-Verbindung unterbrochen ist, liegt möglicherweise ein vorübergehender Netzwerkfehler oder ein Problem mit der SQL-Datenbank vor.

- **Empfehlung**:  Wiederholen Sie die Aktivität, und überprüfen Sie die Metriken der SQL-Datenbank.


## <a name="error-code-sqlbulkcopyinvalidcolumnlength"></a>Fehlercode: SqlBulkCopyInvalidColumnLength

- **Meldung**: `SQL Bulk Copy failed due to receive an invalid column length from the bcp client.`

- **Ursache:** Es ist ein Fehler beim SQL-Massenkopiervorgang aufgetreten, da vom BCP-Client (Massenkopierhilfsprogramm) eine ungültige Spaltenlänge empfangen wurde.

- **Empfehlung**:  Um zu ermitteln, in welcher Zeile das Problem aufgetreten ist, aktivieren Sie die Funktion für Fehlertoleranz für die Kopieraktivität. Dadurch können problematische Zeilen zur weiteren Untersuchung an den Speicher umgeleitet werden. Weitere Informationen finden Sie unter [Fehlertoleranz der Kopieraktivität](./copy-activity-fault-tolerance.md).


## <a name="error-code-sqlconnectionisclosed"></a>Fehlercode: SqlConnectionIsClosed

- **Meldung**: `The connection is closed by SQL Database.`

- **Ursache:** Die SQL-Verbindung wird durch SQL-Datenbank getrennt, wenn viele Vorgänge gleichzeitig durchgeführt werden und der Server die Verbindung trennt.

- **Empfehlung**:  Wiederholen Sie den Verbindungsversuch. Sollte das Problem weiterhin bestehen, wenden Sie sich an den Azure SQL-Support.

## <a name="error-code-sqlserverinvalidlinkedservicecredentialmissing"></a>Fehlercode: SqlServerInvalidLinkedServiceCredentialMissing

- **Meldung**: `The SQL Server linked service is invalid with its credential being missing.`

- **Ursache**: Der verknüpfte Dienst wurde nicht ordnungsgemäß konfiguriert.

- **Empfehlung**: Überprüfen Sie den mit SQL Server verknüpften Dienst, und behandeln Sie mögliche Probleme. 

## <a name="error-code-sqlparallelfailedtodetectpartitioncolumn"></a>Fehlercode: SqlParallelFailedToDetectPartitionColumn

- **Meldung**: `Failed to detect the partition column with command '%command;', %message;.`

- **Ursache**: Die Tabelle enthält keinen Primärschlüssel oder eindeutigen Schlüssel.

- **Empfehlung**: Überprüfen Sie die Tabelle, um sicherzustellen, dass ein Primärschlüssel oder ein eindeutiger Index erstellt wird. 

## <a name="error-code-sqlparallelfailedtodetectphysicalpartitions"></a>Fehlercode: SqlParallelFailedToDetectPhysicalPartitions

- **Meldung**: `Failed to detect the physical partitions with command '%command;', %message;.`

- **Ursache**: Für die Tabelle werden keine physischen Partitionen erstellt. Überprüfen Sie Ihre Datenbank.

- **Empfehlung**: Informationen zum Behandeln dieses Problems finden Sie unter [Erstellen partitionierter Tabellen und Indizes](/sql/relational-databases/partitions/create-partitioned-tables-and-indexes?view=sql-server-ver15&preserve-view=true).

## <a name="error-code-sqlparallelfailedtogetpartitionrangesynapse"></a>Fehlercode: SqlParallelFailedToGetPartitionRangeSynapse

- **Meldung**: `Failed to get the partitions for azure synapse with command '%command;', %message;.`

- **Ursache**: Für die Tabelle werden keine physischen Partitionen erstellt. Überprüfen Sie Ihre Datenbank.

- **Empfehlung**: Informationen zum Behandeln dieses Problems finden Sie unter [Partitioning tables in dedicated SQL pool](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-tables-partition.md) (Partitionieren von Tabellen in einem dedizierten SQL-Pool).

## <a name="error-message-conversion-failed-when-converting-from-a-character-string-to-uniqueidentifier"></a>Fehlermeldung: Fehler beim Konvertieren einer Zeichenfolge in „uniqueidentifier“.

- **Symptome:** Wenn Sie Daten aus der Tabellendatenquelle (z. B. SQL Server) in Azure Synapse Analytics kopieren, indem Sie das gestaffelte Kopieren und PolyBase verwenden, erhalten Sie den folgenden Fehler:

   `ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException, 
    Message=Error happened when loading data into Azure Synapse Analytics., 
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException, 
    Message=Conversion failed when converting from a character string to uniqueidentifier...`

- **Ursache:** Mit Azure Synapse Analytics PolyBase kann eine leere Zeichenfolge nicht in eine GUID konvertiert werden.

- **Lösung:** Legen Sie in der Senke der Kopieraktivität unter den PolyBase-Einstellungen die Option **use type default** auf *false* fest.


## <a name="error-message-expected-data-type-decimalxx-offending-value"></a>Fehlermeldung: Erwarteter Datentyp: DECIMAL(x,x), Auslösender Wert

- **Symptome:** Wenn Sie Daten aus der Tabellendatenquelle (z. B. SQL Server) in Azure Synapse Analytics kopieren, indem Sie das gestaffelte Kopieren und PolyBase verwenden, erhalten Sie den folgenden Fehler:

    `ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException, 
    Message=Error happened when loading data into Azure Synapse Analytics., 
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException, 
    Message=Query aborted-- the maximum reject threshold (0 rows) was reached while reading from an external source: 1 rows rejected out of total 415 rows processed. (/file_name.txt)  
    Column ordinal: 18, Expected data type: DECIMAL(x,x), Offending value:..`

- **Ursache:** Azure Synapse Analytics PolyBase kann in eine Dezimalspalte keine leere Zeichenfolge (NULL-Wert) einfügen.

- **Lösung:** Legen Sie in der Senke der Kopieraktivität unter den PolyBase-Einstellungen die Option **use type default** auf „false“ fest.


## <a name="error-message-java-exception-message-hdfsbridgecreaterecordreader"></a>Fehlermeldung: Java-Ausnahmemeldung: HdfsBridge::CreateRecordReader

- **Symptome:** Sie kopieren Daten mit PolyBase in Azure Synapse Analytics und erhalten den folgenden Fehler:

    `Message=110802;An internal DMS error occurred that caused this operation to fail.  
    Details: Exception: Microsoft.SqlServer.DataWarehouse.DataMovement.Common.ExternalAccess.HdfsAccessException, 
    Message: Java exception raised on call to HdfsBridge_CreateRecordReader.  
    Java exception message:HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.: Error [HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.] occurred while accessing external file.....`

- **Ursache:** Eine mögliche Ursache ist, dass das Schema (gesamte Spaltenbreite) zu groß ist (größer als 1 MB). Überprüfen Sie das Schema der Azure Synapse Analytics-Zieltabelle, indem Sie die Größe aller Spalten hinzufügen:

    - Int = 4 Byte
    - Bigint = 8 Byte
    - Varchar(n),char(n),binary(n), varbinary(n) = n Byte
    - Nvarchar(n), nchar(n) = n*2 Byte
    - Date = 6 Byte
    - Datetime/(2), smalldatetime = 16 Byte
    - Datetimeoffset = 20 Byte
    - Decimal = 19 Byte
    - Float = 8 Byte
    - Money = 8 Byte
    - Smallmoney = 4 Byte
    - Real = 4 Byte
    - Smallint = 2 Byte
    - Time = 12 Byte
    - Tinyint = 1 Byte

- **Lösung**: 
    - Reduzieren Sie die Spaltenbreite auf weniger als 1 MB.
    - Sie können auch das Masseneinfügen verwenden, indem Sie PolyBase deaktivieren.


## <a name="error-message-the-condition-specified-using-http-conditional-headers-is-not-met"></a>Fehlermeldung: Die mit bedingtem HTTP-Header angegebene Bedingung ist nicht erfüllt.

- **Symptome:** Sie verwenden die SQL-Abfrage zum Abrufen von Daten per Pullvorgang aus Azure Synapse Analytics und erhalten den folgenden Fehler:

    `...StorageException: The condition specified using HTTP conditional header(s) is not met...`

- **Ursache:** Für Azure Synapse Analytics ist beim Abfragen der externen Tabelle in Azure Storage ein Fehler aufgetreten.

- **Lösung:** Führen Sie dieselbe Abfrage in SQL Server Management Studio (SSMS) aus, und überprüfen Sie, ob Sie das gleiche Ergebnis erhalten. Wenn dies der Fall ist, erstellen Sie ein Supportticket für Azure Synapse Analytics, und geben Sie den Namen Ihres Azure Synapse Analytics-Servers und der Datenbank an.


## <a name="performance-tier-is-low-and-leads-to-copy-failure"></a>Die Leistungsstufe ist niedrig und führt zu einem Kopierfehler

- **Symptome:** Sie kopieren Daten in Azure SQL-Datenbank und erhalten den folgenden Fehler: `Database operation failed. Error message from database execution : ExecuteNonQuery requires an open and available Connection. The connection's current state is closed.`

- **Ursache:** Für Azure SQL-Datenbank s1 gelten Grenzwerte für die Eingabe/Ausgabe (E/A).

- **Lösung:** Führen Sie ein Upgrade der Azure SQL-Datenbank-Leistungsstufe durch, um das Problem zu beheben. 


## <a name="sql-table-cant-be-found"></a>Die SQL-Tabelle kann nicht gefunden werden. 

- **Symptome:** Sie kopieren Daten aus einer Hybridumgebung in eine lokale SQL Server-Tabelle und erhalten den folgenden Fehler:`Cannot find the object "dbo.Contoso" because it does not exist or you do not have permissions.`

- **Ursache:** Das aktuelle SQL-Konto verfügt nicht über ausreichende Berechtigungen zum Ausführen von Anforderungen, die von .NET SqlBulkCopy.WriteToServer ausgegeben wurden.

- **Lösung:** Wechseln Sie zu einem SQL-Konto mit höheren Berechtigungen.


## <a name="error-message-string-or-binary-data-is-truncated"></a>Fehlermeldung: String- oder binary-Daten werden abgeschnitten.

- **Symptome:** Beim Kopieren von Daten in eine lokale Azure SQL Server-Tabelle tritt ein Fehler auf. 

- **Ursache:** Die Definition des Cx-SQL-Tabellenschemas weist mindestens eine Spalte mit einer geringeren Länge als erwartet auf.

- **Lösung:** Versuchen Sie Folgendes, um das Problem zu beheben:

    1. Wenden Sie [Fehlertoleranz](./copy-activity-fault-tolerance.md) für die SQL-Senke (insbesondere „redirectIncompatibleRowSettings“) an, um die Zeilen mit dem Problem zu korrigieren.

        > [!NOTE]
        > Eine Fehlertoleranz kann zusätzliche Ausführungszeit mit sich bringen, was möglicherweise zu höheren Kosten führt.

    2. Überprüfen Sie die Spaltenlänge der umgeleiteten Daten anhand des SQL-Tabellenschema genau, um zu ermitteln, welche Spalten aktualisiert werden müssen.

    3. Aktualisieren Sie das Tabellenschema entsprechend.

## <a name="error-code-faileddboperation"></a>Fehlercode: FailedDbOperation

- **Meldung**: `User does not have permission to perform this action.`

- **Empfehlung**: Stellen Sie sicher, dass der im Azure Synapse Analytics-Connector konfigurierte Benutzer über die CONTROL-Berechtigung für die Zieldatenbank verfügen muss, wenn PolyBase zum Laden von Daten verwendet wird. Nähere Informationen finden Sie in diesem [Dokument](./connector-azure-sql-data-warehouse.md#required-database-permission).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Problembehandlung finden Sie in diesen Ressourcen:

- [Leitfaden zur Problembehandlung für Connectors](connector-troubleshoot-guide.md)
- [Data Factory-Blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Data Factory-Funktionsanfragen](/answers/topics/azure-data-factory.html)
- [Azure-Videos](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Q&A-Seite von Microsoft](/answers/topics/azure-data-factory.html)
- [Stack Overflow-Forum für Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Twitter-Informationen über Data Factory](https://twitter.com/hashtag/DataFactory)
