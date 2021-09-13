---
title: Behandeln von Connector- und Formatproblemen in Zuordnungsdatenflüssen
description: Hier erfahren Sie, wie Sie connector- und formatbezogene Datenflussprobleme in Azure Data Factory beheben.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 08/17/2021
ms.openlocfilehash: 79a64a7eb1e06fef3c9e534a69324faaf9f23107
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122867534"
---
# <a name="troubleshoot-connector-and-format-issues-in-mapping-data-flows-in-azure-data-factory"></a>Behandeln von Connector- und Formatproblemen in Zuordnungsdatenflüssen in Azure Data Factory


In diesem Artikel werden Methoden zur Behandlung von connector- und formatbezogenen Problemen für Zuordnungsdatenflüsse in Azure Data Factory (ADF) behandelt.

## <a name="azure-blob-storage"></a>Azure Blob Storage

### <a name="account-kind-of-storage-general-purpose-v1-doesnt-support-service-principal-and-mi-authentication"></a>Die Kontoart des Speichers (Universell v1) unterstützt keine Dienstprinzipalauthentifizierung und keine Authentifizierung über die verwaltete Instanz

#### <a name="symptoms"></a>Symptome

Wenn Sie in Datenflüssen Azure Blob Storage (Universell v1) mit dem Dienstprinzipal oder Authentifizierung über die verwaltete Instanz verwenden, wird möglicherweise die folgende Fehlermeldung angezeigt:

`com.microsoft.dataflow.broker.InvalidOperationException: ServicePrincipal and MI auth are not supported if blob storage kind is Storage (general purpose v1)`

#### <a name="cause"></a>Ursache

Bei Verwendung des verknüpften Azure Blob-Diensts in Datenflüssen wird Authentifizierung über verwaltete Identität oder Dienstprinzipal nicht unterstützt, wenn der Kontotyp leer ist oder der Wert „Storage“ lautet. Diese Situation wird in Abbildung 1 und Abbildung 2 unten gezeigt.

Abbildung 1: Die Kontoart im verknüpften Azure Blob Storage-Dienst

:::image type="content" source="./media/data-flow-troubleshoot-connector-format/storage-account-kind.png" alt-text="Screenshot: Art des Speicherkontos im verknüpften Azure Blob Storage-Dienst."::: 

Abbildung 2: Seite „Speicherkonto“

:::image type="content" source="./media/data-flow-troubleshoot-connector-format/storage-account-page.png" alt-text="Screenshot: Seite „Speicherkonto“." lightbox="./media/data-flow-troubleshoot-connector-format/storage-account-page.png"::: 


#### <a name="recommendation"></a>Empfehlung

Um dieses Problem zu lösen, beachten Sie die folgenden Empfehlungen:

- Wenn die Art des Speicherkontos im verknüpften Azure Blob-Dienst **None** (Ohne) lautet, geben Sie die richtige Kontoart an. Ziehen Sie Abbildung 3 zu Rate, um dies zu erreichen. Abbildung 2 zeigt, um Sie die Art des Speicherkontos ermitteln und überprüfen und bestätigen können, dass es sich bei der Kontoart nicht um „Storage (Universell v1)“ handelt.

    Abbildung 3: Angeben der Art des Speicherkontos im verknüpften Azure Blob Storage-Dienst

    :::image type="content" source="./media/data-flow-troubleshoot-connector-format/specify-storage-account-kind.png" alt-text="Screenshot: Angeben der Art des Speicherkontos im verknüpften Azure Blob Storage-Dienst."::: 
    

- Wenn die Kontoart „Storage (Universell v1)“ ist, aktualisieren Sie Ihr Speicherkonto auf **Universell v2**, oder wählen Sie eine andere Authentifizierungsmethode aus.

    Abbildung 4: Durchführen eines Upgrades des Speicherkontos auf „Universell v2“

    :::image type="content" source="./media/data-flow-troubleshoot-connector-format/upgrade-storage-account.png" alt-text="Screenshot: Upgrade des Speicherkontos auf „Universell v2“." lightbox="./media/data-flow-troubleshoot-connector-format/upgrade-storage-account.png"::: 

## <a name="azure-cosmos-db-and-json-format"></a>Azure Cosmos DB- und JSON-Format

### <a name="support-customized-schemas-in-the-source"></a>Unterstützen benutzerdefinierter Schemas in der Quelle

#### <a name="symptoms"></a>Symptome
Wenn Sie den ADF-Datenfluss verwenden möchten, um Daten aus Cosmos DB/JSON in andere Datenspeicher zu verschieben oder zu übertragen, werden möglicherweise einige Spalten der Quelldaten ausgelassen. 

#### <a name="cause"></a>Ursache 
Bei den schemafreien Connectors (Spaltennummer, Spaltenname und Spaltendatentyp der einzelnen Zeilen können sich im Vergleich zu anderen unterscheiden) werden von ADF standardmäßig Beispielzeilen (etwa die Daten der ersten 100 oder 1.000 Zeilen) verwendet, um das Schema abzuleiten, und das abgeleitete Ergebnis wird als Schema zum Lesen von Daten verwendet. Wenn Ihre Datenspeicher also über zusätzliche Spalten verfügen, die nicht in Beispielzeilen vorkommen, werden die Daten dieser zusätzlichen Spalten nicht gelesen, verschoben oder in Senkendatenspeicher übertragen.

#### <a name="recommendation"></a>Empfehlung
ADF bietet Optionen zum Anpassen des Quellschemas, um das Standardverhalten zu überschreiben und zusätzliche Felder aufzunehmen. Zum Lesen der Daten können Sie zusätzliche Spalten angeben, die im Ergebnis der Schemaableitung in der Datenfluss-Quellenprojektion ggf. fehlen, und Sie können eine der folgenden Optionen anwenden, um das benutzerdefinierte Schema festzulegen. In der Regel wird die **1. Option** bevorzugt.

- **1. Option:** Verglichen mit den ursprünglichen Quelldaten, bei denen es sich ggf. um eine große Datei, eine große Tabelle oder einen großen Container mit Millionen von Zeilen und komplexen Schemas handelt, können Sie eine temporäre Tabelle/einen temporären Container mit wenigen Zeilen erstellen, die alle zu lesenden Spalten enthalten, und dann mit dem folgenden Vorgang fortfahren: 

    1. Verwenden Sie die **Debugeinstellungen** für die Datenflussquelle, um **Import projection** (Projektion importieren) mit Beispieldateien/-tabellen zu konfigurieren und somit das vollständige Schema zu erhalten. Sie können die in der folgenden Abbildung gezeigten Schritte ausführen:<br/>

        ![Screenshot: Erster Teil der ersten Option zum Anpassen des Quellschemas](./media/data-flow-troubleshoot-connector-format/customize-schema-option-1-1.png)<br/>
         1. Wählen Sie auf der Datenflusscanvas die Option **Debugeinstellungen** aus.
         1. Wählen Sie im Popupbereich unter der Registerkarte **cosmosSource** die Option **Beispieltabelle** aus, und geben Sie den Namen Ihrer Tabelle in den Block **Tabelle** ein.
         1. Klicken Sie auf **Save** (Speichern), um Ihre Einstellungen zu speichern.
         1. Wählen Sie **Import projection** (Projektion importieren) aus.<br/>  
    
    1. Ändern Sie die **Debugeinstellungen** zurück, um das Quelldataset für die restliche Datenverschiebung/-transformation zu verwenden. Fahren Sie mit den Schritten aus der folgenden Abbildung fort:<br/>

        ![Screenshot: Zweiter Teil der ersten Option zum Anpassen des Quellschemas](./media/data-flow-troubleshoot-connector-format/customize-schema-option-1-2.png) <br/>   
         1. Wählen Sie auf der Datenflusscanvas die Option **Debugeinstellungen** aus.
         1. Wählen Sie im Popupbereich unter der Registerkarte **cosmosSource** die Option **Quelldataset** aus.
         1. Klicken Sie auf **Save** (Speichern), um Ihre Einstellungen zu speichern.<br/>
    
    Das angepasste Schema wird nun von der ADF-Datenflussruntime beim Lesen von Daten aus dem ursprünglichen Datenspeicher berücksichtigt und verwendet. <br/>

- **2. Option:** Wenn Sie mit dem Schema und der domänenspezifischen Sprache der Quelldaten vertraut sind, können Sie das Datenflussquellskript manuell aktualisieren, um zusätzliche/fehlende Spalten hinzuzufügen, die beim Lesen der Daten berücksichtigt werden sollen. Die folgende Abbildung zeigt ein entsprechendes Beispiel: 

    ![Screenshot: Zweite Option zum Anpassen des Quellschemas](./media/data-flow-troubleshoot-connector-format/customize-schema-option-2.png)

### <a name="support-map-type-in-the-source"></a>Unterstützungszuordnungstyp in der Quelle

#### <a name="symptoms"></a>Symptome
In ADF-Datenflüssen kann der Zuordnungsdatentyp in der Cosmos DB- oder JSON-Quelle nicht direkt unterstützt werden, sodass Sie den Zuordnungsdatentyp nicht unter „Import projection“ (Projektion importieren) abrufen können.

#### <a name="cause"></a>Ursache
Bei Cosmos DB und JSON handelt es sich um eine schemafreie Konnektivität, und der zugehörige Spark-Connector verwendet Beispieldaten, um das Schema abzuleiten, und dieses Schema wird dann als Cosmos DB/JSON-Quellschema verwendet. Beim Rückschluss auf das Schema kann der Spark-Connector von Cosmos DB/JSON die Objektdaten nur als Struktur (struct) und nicht als Zuordnungsdatentyp ableiten, weshalb der Zuordnungstyp nicht direkt unterstützt werden kann.

#### <a name="recommendation"></a>Empfehlung 
Um dieses Problem zu lösen, sehen Sie sich die folgenden Beispiele und Schritte an, um das Skript (DSL) der Cosmos DB/JSON-Quelle manuell zu aktualisieren, damit der Zuordnungsdatentyp unterstützt wird.

**Beispiele**:

:::image type="content" source="./media/data-flow-troubleshoot-connector-format/script-example.png" alt-text="Screenshot: Beispiele zum Aktualisieren des Skripts (DSL) der Cosmos DB/JSON-Quelle" lightbox="./media/data-flow-troubleshoot-connector-format/script-example.png"::: 
    
**Schritt 1**: Öffnen Sie das Skript der Datenflussaktivität.

:::image type="content" source="./media/data-flow-troubleshoot-connector-format/open-script.png" alt-text="Screenshot: Öffnen des Skripts der Datenflussaktivität" ::: 
    
**Schritt 2**: Aktualisieren Sie die DSL, um die Unterstützung des Zuordnungstyps abzurufen, indem Sie auf die obigen Beispiele verweisen.

:::image type="content" source="./media/data-flow-troubleshoot-connector-format/update-dsl.png" alt-text="Screenshot: Aktualisieren der DSL" ::: 

Zuordnungstypunterstützung:

|type |Wird der Zuordnungstyp unterstützt?   |Kommentare|
|-------------------------|-----------|------------|
|Excel, CSV  |Nein      |Beides sind Quellen für Tabellendaten mit primitivem Typ, sodass keine Notwendigkeit besteht, den Zuordnungstyp zu unterstützen. |
|Orc, Avro |Ja |Keine.|
|JSON|Ja |Der Zuordnungstyp kann nicht direkt unterstützt werden. Befolgen Sie den Empfehlungsteil in diesem Abschnitt, um das Skript (DSL) unter der Quellprojektion zu aktualisieren.|
|Cosmos DB |Ja |Der Zuordnungstyp kann nicht direkt unterstützt werden. Befolgen Sie den Empfehlungsteil in diesem Abschnitt, um das Skript (DSL) unter der Quellprojektion zu aktualisieren.|
|Parquet |Ja |Derzeit wird der komplexe Datentyp für das Parquet-Dataset nicht unterstützt, daher müssen Sie „Import projection“ (Projektion importieren) unter der Parquet-Datenquelle des Datenflusses verwenden, um den Zuordnungstyp abzurufen.|
|XML |Nein |Keine.|

### <a name="consume-json-files-generated-by-copy-activities"></a>Nutzen von JSON-Dateien, die von Kopieraktivitäten generiert werden

#### <a name="symptoms"></a>Symptome

Wenn Sie die Kopieraktivität verwenden, um JSON-Dateien zu generieren, und dann versuchen, diese Dateien in Datenflüssen zu lesen, tritt die folgende Fehlermeldung auf: `JSON parsing error, unsupported encoding or multiline`

#### <a name="cause"></a>Ursache

Es gibt die folgenden Einschränkungen für JSON für Kopiervorgänge bzw. Datenflüsse:

- Bei JSON-Dateien mit Unicode-Codierung (utf-8, utf-16, utf-32) generieren Kopieraktivitäten die JSON-Dateien immer mit BOM.
- Die JSON-Datenflussquelle mit aktivierter Option „Einzelnes Dokument“ unterstützt keine Unicode-Codierung mit BOM.

    :::image type="content" source="./media/data-flow-troubleshoot-connector-format/enabled-single-document.png" alt-text="Screenshot: Aktivierte Option „Einzelnes Dokument“."::: 


Daher treten Probleme auf, wenn die folgenden Kriterien erfüllt sind:

- Das von der Kopieraktivität verwendete Senkendataset ist auf Unicode-Codierung (utf-8, utf-16, utf-16be, utf-32, utf-32be) festgelegt, oder der Standardwert wird verwendet.
- Die Kopiersenke ist so festgelegt, dass sie das Dateimuster „Array von Objekten“ verwendet, wie in der folgenden Abbildung gezeigt, und zwar unabhängig davon, ob „Einzelnes Dokument“ in der JSON-Datenquelle des Datenflusses aktiviert ist. 

    :::image type="content" source="./media/data-flow-troubleshoot-connector-format/array-of-objects-pattern.png" alt-text="Screenshot: Festgelegtes Muster „Array von Objekten“."::: 
   
#### <a name="recommendation"></a>Empfehlung

- Verwenden Sie immer das Standarddateimuster oder das explizite Muster „Satz von Objekten“ in der Kopiersenke, wenn die generierten Dateien in Datenflüssen verwendet werden.
- Deaktivieren Sie die Option „Einzelnes Dokument“ in der JSON-Quelle des Datenflusses.

>[!Note]
> Die Verwendung von „Satz von Objekten“ ist auch aus Leistungssicht die empfohlene Vorgehensweise. Da der JSON-Code „Einzelnes Dokument“ im Datenfluss das parallele Lesen für einzelne große Dateien nicht aktivieren kann, hat diese Empfehlung keine negativen Auswirkungen.

### <a name="the-query-with-parameters-does-not-work"></a>Die Abfrage mit Parametern funktioniert nicht

#### <a name="symptoms"></a>Symptome

Zuordnungsdatenflüsse in Azure Data Factory unterstützen die Verwendung von Parametern. Die Parameterwerte werden von der aufrufenden Pipeline über die Aktivität „Execute Dataflow“ (Datenfluss ausführen) festgelegt, und die Verwendung von Parametern macht Ihre Datenflüsse universell, flexibel und wiederverwendbar. Sie können Datenflusseinstellungen und -ausdrücke mit den folgenden Parametern parametrisieren: [Parametrisieren von Zuordnungsdatenflüssen](./parameters-data-flow.md).

Nachdem Parameter festgelegt und in der Abfrage der Datenflussquelle verwendet wurden, sind sie nicht wirksam.

#### <a name="cause"></a>Ursache

Dieser Fehler tritt aufgrund der falschen Konfiguration auf.

#### <a name="recommendation"></a>Empfehlung

Verwenden Sie die folgenden Regeln, um Parameter in der Abfrage festzulegen. Ausführlichere Informationen finden Sie unter [Erstellen von Ausdrücken im Zuordnungsdatenfluss](./concepts-data-flow-expression-builder.md).

1. Wenden Sie am Anfang der SQL-Anweisung doppelte Anführungszeichen an.
2. Schließen Sie den Parameter in einfache Anführungszeichen ein.
3. Verwenden Sie Kleinbuchstaben für alle CLAUSE-Anweisungen.

Beispiel:

:::image type="content" source="./media/data-flow-troubleshoot-connector-format/set-parameter-in-query.png" alt-text="Screenshot: Festgelegter Parameter in der Abfrage."::: 

## <a name="azure-data-lake-storage-gen1"></a>Azure Data Lake Storage Gen1

### <a name="fail-to-create-files-with-service-principle-authentication"></a>Fehler beim Erstellen von Dateien mit Dienstprinzipalauthentifizierung

#### <a name="symptoms"></a>Symptome
Wenn Sie versuchen, Daten aus verschiedenen Quellen in die ADLS gen1-Senke zu verschieben oder an sie zu übertragen, schlägt der Auftrag möglicherweise mit der folgenden Fehlermeldung fehl, wenn die Authentifizierungsmethode des verknüpften Diensts „Dienstprinzipalauthentifizierung“ ist:

`org.apache.hadoop.security.AccessControlException: CREATE failed with error 0x83090aa2 (Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.). [2b5e5d92-xxxx-xxxx-xxxx-db4ce6fa0487] failed with error 0x83090aa2 (Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.)`

#### <a name="cause"></a>Ursache

Die RWX-Berechtigung oder die Dataseteigenschaft ist nicht ordnungsgemäß festgelegt.

#### <a name="recommendation"></a>Empfehlung

- Wenn der Zielordner nicht über die richtigen Berechtigungen verfügt, lesen Sie dieses Dokument, um die richtige Berechtigung in Gen1 zuzuweisen: [Verwenden von Dienstprinzipalauthentifizierung](./connector-azure-data-lake-store.md#use-service-principal-authentication).

- Wenn der Zielordner über die richtige Berechtigung verfügt und Sie die Dateinameneigenschaft im Datenfluss verwenden, um den richtigen Ordner und Dateinamen als Ziel zu verwenden, aber die Dateipfadeigenschaft des Datasets wie im Beispiel in den folgenden Abbildungen gezeigt nicht auf den Zieldateipfad festgelegt ist (normalerweise also keine Festlegung erfolgt ist), tritt dieser Fehler auf, weil das Back-End-System versucht, Dateien auf der Grundlage des Dateipfads des Datasets zu erstellen, und der Dateipfad des Datasets nicht die richtige Berechtigung aufweist.
    
    :::image type="content" source="./media/data-flow-troubleshoot-connector-format/file-path-property.png" alt-text="Screenshot: Dateipfadeigenschaft"::: 
    
    :::image type="content" source="./media/data-flow-troubleshoot-connector-format/file-name-property.png" alt-text="Screenshot: Dateinameneigenschaft"::: 

    
    Es gibt zwei Möglichkeiten zur Behebung dieses Problems:
    1. Weisen Sie dem Dateipfad des Datasets die WX-Berechtigung zu.
    1. Legen Sie den Dateipfad des Datasets als Ordner mit WX-Berechtigung fest, und legen Sie den restlichen Ordnerpfad und den Dateinamen in Datenflüssen fest.

## <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

### <a name="failed-with-an-error-error-while-reading-file-xxx-it-is-possible-the-underlying-files-have-been-updated"></a>Fehler: „Fehler beim Lesen der Datei XXX. Möglicherweise wurden die zugrunde liegenden Dateien aktualisiert.“

#### <a name="symptoms"></a>Symptome

Wenn Sie die ADLS Gen2 als Senke im Datenfluss verwenden (zum Anzeigen einer Vorschau der Daten, zum Debuggen oder Auslösen einer Ausführung usw.) und die Partitionseinstellung auf der Registerkarte **Optimieren** in der Phase **Senke** nicht die Standardeinstellung ist, tritt bei einem Auftrag möglicherweise ein Fehler mit der folgenden Fehlermeldung auf:

`Job failed due to reason: Error while reading file abfss:REDACTED_LOCAL_PART@prod.dfs.core.windows.net/import/data/e3342084-930c-4f08-9975-558a3116a1a9/part-00000-tid-7848242374008877624-5df7454e-7b14-4253-a20b-d20b63fe9983-1-1-c000.csv. It is possible the underlying files have been updated. You can explicitly invalidate the cache in Spark by running 'REFRESH TABLE tableName' command in SQL or by recreating the Dataset/DataFrame involved.`

#### <a name="cause"></a>Ursache

1. Sie weisen Ihrer Authentifizierung über eine verwaltete Instanz oder einen Dienstprinzipal keine ordnungsgemäße Berechtigung zu.
1. Möglicherweise verfügen Sie über einen benutzerdefinierten Auftrag zum Verarbeiten von Dateien, die Sie nicht wünschen. Dies wirkt sich auf die mittlere Ausgabe des Datenflusses aus.

#### <a name="recommendation"></a>Empfehlung
1. Überprüfen Sie, ob Ihr verknüpfter Dienst über die R/W/E-Berechtigung für Gen2 verfügt. Wenn Sie Authentifizierung über eine verwaltete Instanz oder einen Dienstprinzipal verwenden, gewähren Sie zumindest die Rolle „Mitwirkender an Speicherblobdaten“ in der Zugriffssteuerung (IAM).
1. Vergewissern Sie sich, dass Sie über bestimmte Aufträge verfügen, die Dateien an einen anderen Ort verschieben/löschen, dessen Name nicht mit Ihrer Regel übereinstimmt. Da Datenflüsse Partitionsdateien zuerst in den Zielordner schreiben und dann die Zusammenführungs- und Umbenennungsvorgänge durchführen, stimmt der Name der mittleren Datei möglicherweise nicht mit Ihrer Regel überein.

## <a name="azure-database-for-postgresql"></a>Azure Database for PostgreSQL

### <a name="encounter-an-error-failed-with-exception-handshake_failure"></a>Fehler: Fehler mit Ausnahme: handshake_failure 

#### <a name="symptoms"></a>Symptome
Sie verwenden Azure PostgreSQL als Quelle oder Senke im Datenfluss, z. B. zum Anzeigen einer Vorschau von Daten und das Debuggen/Auslösen der Ausführung. Möglicherweise schlägt der Auftrag mit der folgenden Fehlermeldung fehl: 

   `PSQLException: SSL error: Received fatal alert: handshake_failure `<br/>
   `Caused by: SSLHandshakeException: Received fatal alert: handshake_failure.`

#### <a name="cause"></a>Ursache 
Wenn Sie den flexiblen Server oder Hyperscale (Citus) für Ihren Azure PostgreSQL-Server verwenden, gibt es eine Einschränkung in Azure Databricks, da das System über Spark auf dem Azure Databricks-Cluster erstellt wird: Unser System wird blockiert, um eine Verbindung mit dem flexiblen Server oder Hyperscale (Citus) herzustellen. Sie können die folgenden beiden Links als Referenz verwenden.
- [Handshake schlägt beim Versuch fehl, über SSL eine Verbindung aus Azure Databricks mit Azure PostgreSQL herzustellen](/answers/questions/170730/handshake-fails-trying-to-connect-from-azure-datab.html)
 
- [MCW-Real-time-data-with-Azure-Database-for-PostgreSQL-Hyperscale](https://github.com/microsoft/MCW-Real-time-data-with-Azure-Database-for-PostgreSQL-Hyperscale/blob/master/Hands-on%20lab/HOL%20step-by%20step%20-%20Real-time%20data%20with%20Azure%20Database%20for%20PostgreSQL%20Hyperscale.md)<br/>
    Weitere Informationen finden Sie in der folgenden Abbildung in diesem Artikel:<br/>

    ![Screenshot: Verweisender Inhalt aus dem Artikel oben.](./media/data-flow-troubleshoot-connector-format/handshake-failure-cause-2.png)

#### <a name="recommendation"></a>Empfehlung
Sie können versuchen, dieses Problems mithilfe von Kopieraktivitäten zu beheben. 

## <a name="azure-sql-database"></a>Azure SQL-Datenbank
 
### <a name="unable-to-connect-to-the-sql-database"></a>Verbindung mit SQL-Datenbank nicht möglich

#### <a name="symptoms"></a>Symptome

Ihre Azure SQL-Datenbank kann in den Bereichen data copy, dataset preview-data und test-connection im verknüpften Dienst gut funktionieren, schlägt jedoch mit einem Fehler wie `Cannot connect to SQL database: 'jdbc:sqlserver://powerbasenz.database.windows.net;..., Please check the linked service configuration is correct, and make sure the SQL database firewall allows the integration runtime to access` fehl, wenn die gleiche Azure SQL-Datenbank als Quelle oder Senke im Datenfluss verwendet wird

#### <a name="cause"></a>Ursache

Es gibt falsche Firewalleinstellungen auf Ihrem Azure SQL-Datenbank-Server, sodass er nicht von der Datenflusslaufzeit verbunden werden kann. Wenn Sie derzeit versuchen, den Datenfluss zum Lesen/Schreiben Azure SQL-Datenbank zu verwenden, wird Azure Databricks zum Erstellen eines Spark-Clusters zum Ausführen des Auftrags verwendet, unterstützt jedoch keine festen IP-Adressbereiche. Weitere Informationen finden Sie unter [Azure Integration Runtime-IP-Adressen.](./azure-integration-runtime-ip-addresses.md)

#### <a name="recommendation"></a>Empfehlung

Überprüfen Sie die Firewalleinstellungen Ihrer Azure SQL-Datenbank, und legen Sie sie auf „Zugriff auf Azure-Dienste zulassen“ fest, anstatt den festen IP-Adressbereich festzulegen.

:::image type="content" source="./media/data-flow-troubleshoot-connector-format/allow-access-to-azure-service.png" alt-text="Screenshot: Zulassen des Zugriffs auf den Azure-Dienst in den Firewalleinstellungen."::: 

### <a name="syntax-error-when-using-queries-as-input"></a>Syntaxfehler bei Verwendung von Abfragen als Eingabe

#### <a name="symptoms"></a>Symptome

Wenn Sie Abfragen als Eingabe in der Datenflussquelle mit Azure SQL verwenden, tritt die folgende Fehlermeldung auf:

`at Source 'source1': shaded.msdataflow.com.microsoft.sqlserver.jdbc.SQLServerException: Incorrect syntax XXXXXXXX.`

:::image type="content" source="./media/data-flow-troubleshoot-connector-format/error-detail.png" alt-text="Screenshot der Fehlerdetails"::: 

#### <a name="cause"></a>Ursache

Die in der Datenflussquelle verwendete Abfrage sollte als Unterabfrage ausgeführt werden können. Der Grund für den Fehler ist, dass entweder die Abfragesyntax falsch ist oder die Abfrage nicht als untergeordnete Abfrage ausgeführt werden kann. Sie können die folgende Abfrage in SSMS ausführen, um sie zu überprüfen:

`SELECT top(0) * from ($yourQuery) as T_TEMP`

#### <a name="recommendation"></a>Empfehlung

Erstellen Sie eine fehlerfreie Abfrage, und testen Sie sie zunächst in SSMS.

### <a name="failed-with-an-error-sqlserverexception-111212-operation-cannot-be-performed-within-a-transaction"></a>Fehler: „SQLServerException: 111212. Der Vorgang kann nicht innerhalb einer Transaktion ausgeführt werden.“

#### <a name="symptoms"></a>Symptome

Wenn Sie die Azure SQL-Datenbank als Senke im Datenfluss verwenden, um eine Vorschau der Daten anzuzeigen, zu debuggen, eine Ausführung auslösen und andere Aktivitäten auszuführen, tritt bei Ihrem Auftrag möglicherweise die folgende Fehlermeldung auf:

`{"StatusCode":"DFExecutorUserError","Message":"Job failed due to reason: at Sink 'sink': shaded.msdataflow.com.microsoft.sqlserver.jdbc.SQLServerException: 111212;Operation cannot be performed within a transaction.","Details":"at Sink 'sink': shaded.msdataflow.com.microsoft.sqlserver.jdbc.SQLServerException: 111212;Operation cannot be performed within a transaction."}`

#### <a name="cause"></a>Ursache
Der Fehler „`111212;Operation cannot be performed within a transaction.`“ tritt nur im dedizierten Synapse-SQL-Pool auf. Sie verwenden jedoch fälschlicherweise die Azure SQL-Datenbank als Connector.

#### <a name="recommendation"></a>Empfehlung
Vergewissern Sie sich, dass Ihre SQL-Datenbank ein dedizierter Synapse-SQL-Pool ist. Wenn dies der Fall ist, verwenden Sie Azure Synapse Analytics als Connector, wie in der folgenden Abbildung gezeigt.

:::image type="content" source="./media/data-flow-troubleshoot-connector-format/synapse-analytics-connector.png" alt-text="Screenshot: Azure Synapse Analytics-Connector."::: 

### <a name="data-with-the-decimal-type-become-null"></a>Daten mit dem Dezimaltyp werden zu NULL

#### <a name="symptoms"></a>Symptome

Sie möchten Daten in eine Tabelle in der SQL-Datenbank einfügen. Wenn die Daten den Dezimaltyp aufweisen und in eine Spalte mit dem Dezimaltyp in der SQL-Datenbank eingefügt werden müssen, wird der Datenwert ggf. in NULL geändert.

Wenn Sie die Vorschau in vorherigen Phasen ausführen, wird der Wert wie in der folgenden Abbildung angezeigt:

:::image type="content" source="./media/data-flow-troubleshoot-connector-format/value-in-previous-stage.png" alt-text="Screenshot: Wert in den vorherigen Phasen."::: 

In der Senkenphase wird der Wert zu NULL, was in der folgenden Abbildung gezeigt wird.

:::image type="content" source="./media/data-flow-troubleshoot-connector-format/value-in-sink-stage.png" alt-text="Screenshot, der den Wert in der Senkenphase zeigt."::: 

#### <a name="cause"></a>Ursache
Der Dezimaltyp verfügt über Skalierungs- und Genauigkeitseigenschaften. Wenn Ihr Datentyp nicht mit dem Datentyp in der Senkentabelle übereinstimmt, überprüft das System, ob die Zieldezimalzahl breiter als die ursprüngliche Dezimalzahl ist, und es erfolgt kein Überlauf des ursprünglichen Werts in die Zieldezimalzahl. Aus diesem Grund wird der Wert in NULL umgewandelt.

#### <a name="recommendation"></a>Empfehlung
Überprüfen und vergleichen Sie den Dezimaltyp der Daten und der Tabelle in der SQL-Datenbank, und ändern Sie die Skalierung und Genauigkeit in die gleiche Angabe.

Mit toDecimal (IDecimal, scale, precision) können Sie herausfinden, ob die ursprünglichen Daten in die Zielskalierung und -genauigkeit umgewandelt werden können. Wenn NULL zurückgegeben wird, bedeutet dies, dass die Daten beim Einfügen nicht umgewandelt und weiter weiterverarbeitet werden können.

## <a name="azure-synapse-analytics"></a>Azure Synapse Analytics

### <a name="serverless-pool-sql-on-demand-related-issues"></a>Probleme im Zusammenhang mit einem serverlosen Pool (SQL On-Demand)

#### <a name="symptoms"></a>Symptome
Sie verwenden Azure Synapse Analytics, und der verknüpfte Dienst ist effektiv ein serverloser Synapse-Pool. Er hieß früher SQL On-Demand-Pool und kann anhand des Servernamens unterschieden werden, der `ondemand` enthält, z. B. `space-ondemand.sql.azuresynapse.net`. Es können mehrere Fehler wie unten aufgeführt auftreten:<br/>

1. Wenn Sie den serverlosen Synapse-Pool als Senke verwenden möchten, tritt der folgende Fehler auf:<br/>
`Sink results in 0 output columns. Please ensure at least one column is mapped`
1. Wenn Sie in der Quelle „enable staging“ (Staging aktivieren) auswählen, tritt der folgende Fehler auf: `shaded.msdataflow.com.microsoft.sqlserver.jdbc.SQLServerException: Incorrect syntax near 'IDENTITY'.`
1. Wenn Sie Daten aus einer externen Tabelle abrufen möchten, tritt der folgende Fehler auf: `shaded.msdataflow.com.microsoft.sqlserver.jdbc.SQLServerException: External table 'dbo' is not accessible because location does not exist or it is used by another process.`
1. Wenn Sie Daten aus Cosmos DB über einen serverlosen Pool durch query/from view abrufen möchten, tritt der folgende Fehler auf: `Job failed due to reason: Connection reset.`
1. Wenn Sie Daten aus einer Ansicht abrufen möchten, treten möglicherweise verschiedene Fehler auf.

#### <a name="cause"></a>Ursache
Die Ursachen der Symptome werden unten angegeben:
1. Der serverlose Pool kann nicht als Senke verwendet werden. Das Schreiben von Daten in die Datenbank wird nicht unterstützt.
1. Der serverlose Pool unterstützt das gestagte Laden von Daten nicht, sodass „enable staging“ (Staging aktivieren) nicht unterstützt wird. 
1. Die von Ihnen verwendete Authentifizierungsmethode verfügt nicht über die richtige Berechtigung für die externe Datenquelle, auf die die externe Tabelle verweist.
1. Es gibt eine bekannte Einschränkung im serverlosen Synapse-Pool, die das Abrufen von Cosmos DB-Daten aus Datenflüssen blockiert.
1. Die Ansicht ist eine virtuelle Tabelle, die auf einer SQL-Anweisung basiert. Die Grundursache befindet sich in der Anweisung der Sicht.

#### <a name="recommendation"></a>Empfehlung

Sie können die folgenden Schritte ausführen, um Ihre Probleme entsprechend zu beheben.
1. Sie sollten einen serverlosen Pool besser nicht als Senke verwenden.
1. Verwenden Sie „enable staging“ (Staging aktivieren) nicht in der Quelle für den serverlosen Pool.
1. Nur der Dienstprinzipal bzw. die verwaltete Identität, der oder die über die Berechtigung für die externen Tabellendaten verfügt, kann sie abfragen. Sie sollten der externen Datenquelle die Berechtigung „Mitwirkender an Speicherblobdaten“ für die Authentifizierungsmethode erteilen, die Sie in ADF verwenden.
    >[!Note]
    > Die Benutzerkennwortauthentifizierung kann keine externen Tabellen abfragen. Weitere Informationen finden Sie in diesem Artikel: [Sicherheitsmodell](../synapse-analytics/metadata/database.md#security-model).

1. Sie können die Kopieraktivität verwenden, um Cosmos DB-Daten aus dem serverlosen Pool abzurufen.
1. Sie können die SQL-Anweisung, mit der die Ansicht erstellt wird, für das Engineeringsupportteam bereitstellen, das bei der Analyse helfen kann, ob die Anweisung oder etwas anderes zu einem Authentifizierungsproblem führt.


### <a name="load-small-size-data-to-data-warehouse-without-staging-is-slow"></a>Das Laden kleiner Datenmengen in Data Warehouse ohne Staging ist langsam 

#### <a name="symptoms"></a>Symptome
Wenn Sie kleine Datenmengen in Data Warehouse ohne Staging laden, dauert dies sehr lange. Die Datengröße beträgt z. B. 2 MB, aber es dauert mehr als 1 Stunde, bis der Ladevorgang abgeschlossen ist. 

#### <a name="cause"></a>Ursache
Dieses Problem wird durch die Zeilenanzahl und nicht durch die Größe verursacht. Die Zeilenanzahl beträgt einige Tausend, und jede Einfügung muss in eine unabhängige Anforderung gepackt werden, den Steuerknoten durchlaufen, eine neue Transaktion starten, Sperren erhalten und wiederholt den Verteilungsknoten durchlaufen. Massenladen ruft die Sperre ein Mal ab, und jeder Verteilungsknoten führt die Einfügung durch effiziente Batchverarbeitung im Arbeitsspeicher durch.

Wenn 2 MB als wenige Datensätze eingefügt werden, geschieht dies schnell. Beispielsweise ginge es schnell, wenn jeder Datensatz 500 KB * 4 Zeilen beträgt. 

#### <a name="recommendation"></a>Empfehlung
Sie müssen Staging aktivieren, um die Leistung zu verbessern.


### <a name="read-empty-string-value--as-null-with-the-enable-staging"></a>Lesen eines leeren Zeichenfolgenwerts ("") als NULL mit Aktivieren von Staging 

#### <a name="symptoms"></a>Symptome
Wenn Sie Synapse als Quelle im Datenfluss verwenden, z. B. zum Anzeigen einer Vorschau von Daten und Debuggen/Auslösen der Ausführung, und Staging aktivieren, um PolyBase zu verwenden, wird der Spaltenwert in NULL geändert, wenn er einen leeren Zeichenfolgenwert (`""`) enthält.

#### <a name="cause"></a>Ursache
Das Datenfluss-Back-End verwendet Parquet als PolyBase-Format, und es gibt eine bekannte Einschränkung im Synapse SQL-Pool Gen2, die den leeren Zeichenfolgenwert automatisch in NULL ändert.

#### <a name="recommendation"></a>Empfehlung
Sie können versuchen, dieses Problem mit den folgenden Methoden zu beheben:
1. Wenn die Datengröße klein ist, können Sie **Enable staging** (Staging aktivieren) in der Quelle deaktivieren. Die Leistung wird jedoch beeinträchtigt.
1. Wenn Sie Staging aktivieren müssen, können Sie die **iifNull()** -Funktion verwenden, um die spezifische Spalte manuell von NULL in einen leeren Zeichenfolgenwert zu ändern.

### <a name="managed-service-identity-error"></a>Fehler der verwalteten Dienstidentität

#### <a name="symptoms"></a>Symptome
Wenn Sie Synapse als Quelle/Senke im Datenfluss verwenden, um eine Vorschau von Daten anzuzeigen, zu debuggen oder eine Ausführung auszulösen usw. und Staging für die Verwendung von PolyBase aktivieren, und der verknüpfte Dienst des Stagingspeichers (Blob, Gen2 usw.) erstellt wird, um Authentifizierung der verwalteten Identität (MI) zu verwenden, kann der Auftrag mit dem folgenden Fehler fehlschlagen, der in der Abbildung gezeigt wird: <br/>

![Screenshots: Fehler der Dienstidentität.](./media/data-flow-troubleshoot-connector-format/service-identity-error.png)

#### <a name="error-message"></a>Fehlermeldung
`shaded.msdataflow.com.microsoft.sqlserver.jdbc.SQLServerException: Managed Service Identity has not been enabled on this server. Please enable Managed Service Identity and try again.`

#### <a name="cause"></a>Ursache
1. Wenn der SQL-Pool aus dem Synapse-Arbeitsbereich erstellt wird, wird Authentifizierung über die verwaltete Instanz für den Stagingspeicher mit PolyBase für den alten SQL-Pool nicht unterstützt. 
1. Wenn der SQL-Pool die alte Data Warehouse-Version (DWH) ist, wird die verwaltete Instanz des SQL-Servers nicht dem Stagingspeicher zugewiesen.

#### <a name="recommendation"></a>Empfehlung
Sie müssen bestätigen, dass der SQL-Pool über den Synapse-Arbeitsbereich erstellt wird.

- Wenn der SQL-Pool über den Synapse-Arbeitsbereich erstellt wird, müssen Sie die verwaltete Instanz des Arbeitsbereichs erneut registrieren. Gehen Sie wie folgt vor, um dieses Problem zu umgehen, indem Sie die verwaltete Instanz des Arbeitsbereichs erneut registrieren:
    1. Navigieren Sie im Azure-Portal zu Ihrem Synapse-Arbeitsbereich.
    1. Navigieren Sie zum Blatt **Verwaltete Identitäten**.
    1. Wenn die Option **Allow Pipelines** (Pipelines zulassen) bereits aktiviert ist, müssen Sie diese Einstellung deaktivieren und speichern.
    1. Aktivieren Sie die Option **Allow Pipelines** (Pipelines zulassen), und speichern sie.

- Wenn es sich bei dem SQL-Pool um die alte DWH-Version handelt, aktivieren Sie die verwaltete Instanz nur für Ihren SQL-Server, und weisen Sie die Berechtigung für den Stagingspeicher der verwaltete Instanz Ihres SQL-Servers zu. Sie können die Schritte in diesem Artikel als Beispiel verwenden: [Verwenden von VNET-Dienstendpunkten und Regeln für Server in Azure SQL-Datenbank](../azure-sql/database/vnet-service-endpoint-rule-overview.md#steps).

### <a name="failed-with-an-error-sqlserverexception-not-able-to-validate-external-location-because-the-remote-server-returned-an-error-403"></a>Fehler: „SQLServerException: Externer Speicherort kann nicht überprüft werden, da der Remoteserver einen Fehler zurückgegeben hat: (403)“

#### <a name="symptoms"></a>Symptome

Wenn Sie SQLDW als Senke zum Auslösen und Ausführen von Datenflussaktivitäten verwenden, tritt bei der Aktivität möglicherweise ein Fehler wie der folgende auf: `"SQLServerException: Not able to validate external location because the remote server returned an error: (403)"`

#### <a name="cause"></a>Ursache
1. Wenn Sie die verwaltete Identität in der Authentifizierungsmethode im ADLS Gen2-Konto als Staging verwenden, kann cx die Authentifizierungskonfiguration möglicherweise nicht ordnungsgemäß festlegen.
1. Bei der VNET-Integration Runtime müssen Sie die verwaltete Identität in der Authentifizierungsmethode im ADLS Gen2-Konto als Staging verwenden. Wenn Ihre Azure Storage-Staginginstanz mit einem VNET-Dienstendpunkt konfiguriert ist, müssen Sie Authentifizierung über die verwaltete Identität mit für das Speicherkonto aktivierter Option „Vertrauenswürdigen Microsoft-Diensten den Zugriff auf dieses Speicherkonto erlauben“ verwenden.
1. Überprüfen Sie, ob der Ordnername das Leerzeichen oder andere Sonderzeichen enthält, z. B. `Space " < > # % |`.
Ordnernamen, die bestimmte Sonderzeichen enthalten, werden im Data Warehouse-Kopierbefehl derzeit nicht unterstützt.

#### <a name="recommendation"></a>Empfehlung

Weitere Informationen zu Ursache 1 finden Sie im folgenden Dokument mit Schritten zur Lösung dieses Problems: [Verwenden von VNET-Dienstendpunkten und Regeln für Server in Azure SQL-Datenbank](../azure-sql/database/vnet-service-endpoint-rule-overview.md#steps).

Verwenden Sie für Ursache 2 eine der folgenden Optionen als Problemumgehung:

- Option-1: Bei der VNET-Integration Runtime müssen Sie die verwaltete Identität in der Authentifizierungsmethode im ADLS GEN 2-Konto als Staging verwenden.

- Option-2: Wenn Ihre Azure Storage-Staginginstanz mit einem VNET-Dienstendpunkt konfiguriert ist, müssen Sie Authentifizierung über die verwaltete Identität mit für das Speicherkonto aktivierter Option „Vertrauenswürdigen Microsoft-Diensten den Zugriff auf dieses Speicherkonto erlauben“ verwenden. Weitere Informationen finden Sie in diesem Dokument: [Kopiervorgang mit Staging mit PolyBase](./connector-azure-sql-data-warehouse.md#staged-copy-by-using-polybase).

Verwenden Sie für Ursache 3 eine der folgenden Optionen als Problemumgehung:

- Option-1: Benennen Sie den Ordner um, und vermeiden Sie die Verwendung von Sonderzeichen im Ordnernamen.
- Option-2: Entfernen Sie die Eigenschaft `allowCopyCommand:true` im Datenflussskript. Beispiel:

    :::image type="content" source="./media/data-flow-troubleshoot-connector-format/remove-allow-copy-command-true.png" alt-text="Screenshot: Entfernen von „allowcopycommand:true“."::: 


### <a name="failed-with-an-error-this-operation-is-not-permitted-on-a-non-empty-directory"></a>Fehler: „Dieser Vorgang ist für ein nicht leeres Verzeichnis unzulässig“.

#### <a name="symptoms"></a>Symptome

Wenn Sie Azure Synapse Analytics als Senke im Datenfluss verwenden, um eine Vorschau der Daten anzuzeigen, zu debuggen bzw. eine Ausführung auszulösen oder andere Aktivitäten auszuführen und das Aktivieren von Staging auf TRUE festgelegt ist, schlägt Ihr Auftrag möglicherweise mit der folgenden Fehlermeldung fehl:

`DF-SYS-01 at Sink 'sink': Unable to stage data before write. Check configuration/credentials of storage.`<br/>
`org.apache.hadoop.fs.azure.AzureException: com.microsoft.azure.storage.StorageException: This operation is not permitted on a non-empty directory.`

#### <a name="cause"></a>Ursache
Sie verwenden Azure Blob Storage als verknüpften Stagingdienst, um eine Verknüpfung mit einem Speicherkonto zu erstellen, das über den aktivierten hierarchischen Namespace verfügt, und dieses Konto verwendet Schlüsselauthentifizierung im verknüpften Dienst.

:::image type="content" source="./media/data-flow-troubleshoot-connector-format/storage-account-configuration.png" alt-text="Screenshot: Speicherkontokonfiguration":::. 

#### <a name="recommendation"></a>Empfehlung
Erstellen Sie einen verknüpften Azure Data Lake Gen2-Dienst für den Speicher, und wählen Sie den Gen2-Speicher als verknüpften Stagingdienst in Datenflussaktivitäten aus.

## <a name="common-data-model-format"></a>Common Data Model-Format

### <a name="modeljson-files-with-special-characters"></a>Model.json-Dateien mit Sonderzeichen

#### <a name="symptoms"></a>Symptome 
Möglicherweise tritt ein Problem auf, dass der endgültige Name der Datei „model.json“ Sonderzeichen enthält.  

#### <a name="error-message"></a>Fehlermeldung  
`at Source 'source1': java.lang.IllegalArgumentException: java.net.URISyntaxException: Relative path in absolute URI: PPDFTable1.csv@snapshot=2020-10-21T18:00:36.9469086Z. ` 

#### <a name="recommendation"></a>Empfehlung  
Ersetzen Sie die Sonderzeichen im Dateinamen. Dies funktioniert in der Synapse, aber nicht in ADF.  

### <a name="no-data-output-in-the-data-preview-or-after-running-pipelines"></a>Keine Datenausgabe in der Datenvorschau oder nach dem Ausführen von Pipelines

#### <a name="symptoms"></a>Symptome
Wenn Sie die Datei „manifest.json“ für CDM verwenden, werden keine Daten in der Datenvorschau oder nach dem Ausführen einer Pipeline angezeigt. Es werden nur Header angezeigt. Die folgende Abbildung zeigt dieses Problem.<br/>

![Screenshot: Symptom „Keine Datenausgabe“.](./media/data-flow-troubleshoot-connector-format/no-data-output.png)

#### <a name="cause"></a>Ursache
Das Manifestdokument beschreibt den CDM-Ordner (z. B. die im Ordner vorhandenen Entitäten, Verweise auf diese Entitäten und die Daten, die dieser Instanz entsprechen). In Ihrem Manifestdokument fehlen die `dataPartitions`-Informationen, die für ADF angeben, wo die Daten gelesen werden sollen, und da es leer ist, werden keine Daten zurückgegeben. 

#### <a name="recommendation"></a>Empfehlung
Aktualisieren Sie das Manifestdokument mit den `dataPartitions`-Informationen. Sie können dieses Beispielmanifestdokument verwenden, um Ihr Dokument zu aktualisieren: [Common Data Model-Metadaten: Manifestdokument „manifest-Example“](/common-data-model/cdm-manifest#example-manifest-document).

### <a name="json-array-attributes-are-inferred-as-separate-columns"></a>JSON-Arrayattribute werden als separate Spalten abgeleitet

#### <a name="symptoms"></a>Symptome 
Möglicherweise tritt ein Problem auf, bei dem ein Attribut (Zeichenfolgentyp) der CDM-Entität über ein JSON-Array als Daten verfügt. Wenn diese Daten gefunden werden, leitet ADF die Daten fälschlicherweise als separate Spalten ab. Wie Sie in den folgenden Abbildungen sehen können, wird ein einzelnes Attribut in der Quelle (msfp_otherproperties) als separate Spalte in der Vorschau des CDM-Connectors abgeleitet.<br/> 

- In den CSV-Quelldaten (siehe zweite Spalte): <br/>

    ![Screenshot: Attribut in den CSV-Quelldaten.](./media/data-flow-troubleshoot-connector-format/json-array-csv.png)

- In der Vorschau der CDM-Quelldaten: <br/>

    ![Screenshot: Separate Spalte in den CDM-Quelldaten.](./media/data-flow-troubleshoot-connector-format/json-array-cdm.png)

 
Sie können auch versuchen, abweichende Spalten zuzuordnen und den Datenflussausdruck zu verwenden, um dieses Attribut als Array zu transformieren. Da dieses Attribut beim Lesen jedoch als separate Spalte gelesen wird, funktioniert die Transformation in ein Array nicht.  

#### <a name="cause"></a>Ursache
Dieses Problem wird wahrscheinlich durch die Kommas in Ihrem JSON-Objektwert für diese Spalte verursacht. Da ihre Datendatei als CSV-Datei erwartet wird, gibt das Komma an, dass es sich um das Ende des Werts einer Spalte handelt. 

#### <a name="recommendation"></a>Empfehlung
Um dieses Problem zu lösen, müssen Sie Ihre JSON-Spalte in doppelte Anführungszeichen einschließen und innere Anführungszeichen mit einem umgekehrten Schrägstrich (`\`) vermeiden. Auf diese Weise kann der Inhalt des Werts dieser Spalte vollständig als einzelne Spalte gelesen werden.  
  
>[!Note]
>Das CDM informiert nicht darüber, dass der Datentyp des Spaltenwerts JSON ist, aber es informiert darüber, dass es sich um eine Zeichenfolge handelt, die als solche analysiert wird.

### <a name="unable-to-fetch-data-in-the-data-flow-preview"></a>Daten können in der Datenflussvorschau nicht abgerufen werden

#### <a name="symptoms"></a>Symptome
Sie verwenden CDM mit von Power BI generierter Datei „model.json“. Wenn Sie eine Vorschau der CDM-Daten mithilfe der Datenflussvorschau anzeigen, tritt ein Fehler auf: `No output data.`

#### <a name="cause"></a>Ursache
 Der folgende Code ist in den Partitionen in der Datei „model.json“ vorhanden, die vom Power BI-Datenfluss generiert wird.
```json
"partitions": [  
{  
"name": "Part001",  
"refreshTime": "2020-10-02T13:26:10.7624605+00:00",  
"location": "https://datalakegen2.dfs.core.windows.net/powerbi/salesEntities/salesPerfByYear.csv @snapshot=2020-10-02T13:26:10.6681248Z"  
}  
```
Für diese Datei „model.json“ besteht das Problem im Namensschema der Datenpartitionsdatei, das Sonderzeichen aufweist, und unterstützende Dateipfade mit „@“ sind derzeit nicht vorhanden.  

#### <a name="recommendation"></a>Empfehlung
Entfernen Sie den `@snapshot=2020-10-02T13:26:10.6681248Z`-Teil aus dem Namen der Datenpartitionsdatei und der Datei „model.json“, und versuchen Sie es dann erneut. 

### <a name="the-corpus-path-is-null-or-empty"></a>Der Korpuspfad ist NULL oder leer

#### <a name="symptoms"></a>Symptome
Wenn Sie CDM im Datenfluss mit dem Modellformat verwenden, können Sie keine Vorschau der Daten anzeigen, und der folgende Fehler tritt auf: `DF-CDM_005 The corpus path is null or empty`. Der Fehler wird in der folgenden Abbildung gezeigt:  

![Screenshot: Korpuspfadfehler.](./media/data-flow-troubleshoot-connector-format/corpus-path-error.png)

#### <a name="cause"></a>Ursache
Ihr Datenpartitionspfad in „model.json“ verweist auf einen Blobspeicherort und nicht auf Ihren Data Lake. Der Speicherort sollte die Basis-URL **.dfs.core.windows.net** für ADLS Gen2 aufweisen. 

#### <a name="recommendation"></a>Empfehlung
Um dieses Problem zu beheben, lesen Sie diesen Artikel: [ADF fügt Unterstützung für Inlinedatasets und Common Data Model zu Datenflüssen hinzu](https://techcommunity.microsoft.com/t5/azure-data-factory/adf-adds-support-for-inline-datasets-and-common-data-model-to/ba-p/1441798). Die folgende Abbildung zeigt, wie Sie den Korpuspfadfehler gemäß diesem Artikel beheben können.

![Screenshot: Beheben des Korpuspfadfehlers.](./media/data-flow-troubleshoot-connector-format/fix-format-issue.png)

### <a name="unable-to-read-csv-data-files"></a>CSV-Datendateien können nicht gelesen werden

#### <a name="symptoms"></a>Symptome 
Sie verwenden das Inlinedataset als allgemeines Datenmodell mit dem Manifest als Quelle und haben die Eintragsmanifestdatei, den Stammpfad, den Entitätsnamen und den Pfad angegeben. Im Manifest befinden sich die Datenpartitionen mit dem Speicherort der CSV-Datei. In der Zwischenzeit sind das Entitätsschema und das CSV-Schema identisch, und alle Überprüfungen waren erfolgreich. In der Datenvorschau werden jedoch nur das Schema und nicht die Daten geladen, und die Daten sind unsichtbar. Die folgende Abbildung zeigt dies:

![Screenshot: Problem, dass Datendateien nicht gelesen werden können.](./media/data-flow-troubleshoot-connector-format/unable-read-data.png)

#### <a name="cause"></a>Ursache
Ihr CDM-Ordner ist nicht in logische und physische Modelle getrennt, und nur physische Modelle sind im CDM-Ordner vorhanden. In den folgenden beiden Artikeln wird der Unterschied beschrieben: [Logische Definitionen](/common-data-model/sdk/logical-definitions) und [Auflösen einer logischen Entitätsdefinition](/common-data-model/sdk/convert-logical-entities-resolved-entities).<br/> 

#### <a name="recommendation"></a>Empfehlung
Versuchen Sie, für den Datenfluss, der CDM als Quelle verwendet, ein logisches Modell als Entitätsreferenz zu verwenden, und verwenden Sie das Manifest, das den Ort der physisch aufgelösten Entitäten und die Datenpartitionsorte beschreibt. Im öffentlichen CDM-GitHub-Repository finden Sie einige Beispiele für Definitionen logischer Entitäten: [CDM-schemaDocuments](https://github.com/microsoft/CDM/tree/master/schemaDocuments).<br/>

Ein guter Ausgangspunkt für die Bildung Ihres Korpus ist das Kopieren der Dateien im Ordner „schema documents“ (nur diese Ebene im GitHub-Repository) und Speichern dieser Dateien in einem Ordner. Anschließend können Sie eine der vordefinierten logischen Entitäten im Repository (als Ausgangs- oder Referenzpunkt) verwenden, um Ihr logisches Modell zu erstellen.<br/>

Nachdem der Korpus eingerichtet wurde, wird empfohlen, CDM als Senke in Datenflüssen zu verwenden, damit ein wohlgeformter CDM-Ordner ordnungsgemäß erstellt werden kann. Sie können Ihr CSV-Dataset als Quelle verwenden und es dann in das von Ihnen erstellte CDM-Modell einspeisen.

## <a name="csv-and-excel-format"></a>CSV- und Excel-Format

### <a name="set-the-quote-character-to-no-quote-char-is-not-supported-in-the-csv"></a>Festlegen des Anführungszeichens auf „no quote char“ (kein Anführungszeichen) wird in der CSV-Datei nicht unterstützt
 
#### <a name="symptoms"></a>Symptome

Es gibt mehrere Probleme, die in der CSV-Datei nicht unterstützt werden, wenn das Anführungszeichen auf „no quote char“ festgelegt ist:

1. Wenn das Anführungszeichen auf „no quote char“ festgelegt ist, kann das Mehrzeichen-Spaltentrennzeichen nicht mit denselben Buchstaben beginnen und enden.
2. Wenn das Anführungszeichen auf „no quote char“ festgelegt ist, darf das Mehrzeichen-Spaltentrennzeichen kein Escapezeichen enthalten: `\`.
3. Wenn das Anführungszeichen auf „no quote char“ festgelegt ist, darf der Spaltenwert kein Zeilentrennzeichen enthalten.
4. Das Anführungszeichen und das Escapezeichen dürfen nicht beide leer sein (kein Anführungszeichen und kein Escapezeichen), wenn der Spaltenwert ein Spaltentrennzeichen enthält.

#### <a name="cause"></a>Ursache

Die Ursachen der Symptome werden unten mit Beispielen angegeben:
1. Beginn und Ende mit den gleichen Buchstaben.<br/>
`column delimiter: $*^$*`<br/>
`column value: abc$*^    def`<br/>
`csv sink: abc$*^$*^$*def ` <br/>
`will be read as "abc" and "^&*def"`<br/>

2. Das Mehrzeichentrennzeichen enthält Escapezeichen.<br/>
`column delimiter: \x`<br/>
`escape char:\`<br/>
`column value: "abc\\xdef"`<br/>
Das Escapezeichen wird für das Spaltentrennzeichen oder das Escapezeichen verwendet.

3. Der Spaltenwert enthält das Zeilentrennzeichen. <br/>
`We need quote character to tell if row delimiter is inside column value or not.`

4. Das Anführungszeichen und das Escapezeichen sind leer, und der Spaltenwert enthält Spaltentrennzeichen.<br/>
`Column delimiter: \t`<br/>
`column value: 111\t222\t33\t3`<br/>
`It will be ambigious if it contains 3 columns 111,222,33\t3 or 4 columns 111,222,33,3.`<br/>

#### <a name="recommendation"></a>Empfehlung
Das erste Symptom und das zweite Symptom können derzeit nicht behoben werden. Für das dritte und vierte Symptom können Sie die folgenden Methoden anwenden:
- Verwenden Sie für Symptom 3 nicht „no quote char“ für eine mehrzeilige CSV-Datei.
- Legen Sie für Symptom 4 entweder das Anführungszeichen oder das Escapezeichen als nicht leer fest, oder Sie können alle Spaltentrennzeichen in Ihren Daten entfernen.

### <a name="read-files-with-different-schemas-error"></a>Fehler beim Lesen von Dateien mit unterschiedlichen Schemas

#### <a name="symptoms"></a>Symptome

Wenn Sie Datenflüsse verwenden, um Dateien wie CSV- und Excel-Dateien mit unterschiedlichen Schemas zu lesen, schlägt das Debuggen des Datenflusses, die Sandbox oder die Aktivitätsausführung fehl.
- Bei CSV-Dateien liegt ein Datenfehlabgleich vor, wenn das Schema der Dateien unterschiedlich ist. 

    ![Screenshot: Erster Schemafehler.](./media/data-flow-troubleshoot-connector-format/schema-error-1.png)

- Bei Excel tritt ein Fehler auf, wenn das Schema der Datei unterschiedlich ist.

    ![Screenshot: Zweiter Schemafehler.](./media/data-flow-troubleshoot-connector-format/schema-error-2.png)

#### <a name="cause"></a>Ursache

Das Lesen von Dateien mit unterschiedlichen Schemas im Datenfluss wird nicht unterstützt.

#### <a name="recommendation"></a>Empfehlung

Wenn Sie dennoch Dateien wie CSV- und Excel-Dateien mit unterschiedlichen Schemas im Datenfluss übertragen möchten, können Sie die folgenden Möglichkeiten als Problemumgehung nutzen:

- Für CSV müssen Sie das Schema verschiedener Dateien manuell zusammenführen, um das vollständige Schema zu erhalten. Beispielsweise enthält file_1 die Spalten `c_1, c_2, c_3`, während file_2 die Spalten `c_3, c_4,... c_10` enthält, sodass das zusammengeführte und das vollständige Schema `c_1, c_2... c_10` ist. Erstellen Sie dann auch für andere Dateien das gleiche vollständige Schema, obwohl es keine Daten enthält, z. B. weist file_x nur die Spalten `c_1, c_2, c_3, c_4` auf. Fügen Sie der Datei zusätzliche Spalten `c_5, c_6, ... c_10` hinzu. Dies kann funktionieren.

- Für Excel können Sie dieses Problem beheben, indem Sie eine der folgenden Optionen anwenden:

    - **Option-1**: Sie müssen das Schema verschiedener Dateien manuell zusammenführen, um das vollständige Schema zu erhalten. Beispielsweise enthält file_1 die Spalten `c_1, c_2, c_3`, während file_2 die Spalten `c_3, c_4,... c_10` enthält, sodass das zusammengeführte und vollständige Schema `c_1, c_2... c_10` ist. Sorgen Sie dann dafür, dass auch andere Dateien das gleiche Schema aufweisen, auch wenn es keine Daten enthält. file_x mit dem Blatt „Sheet_1“ weist z. B. nur die Spalten `c_1, c_2, c_3, c_4` auf. Fügen Sie dem Blatt auch zusätzliche Spalten `c_5, c_6, ... c_10` hinzu, dann kann es funktionieren.
    - **Option-2**: Verwenden Sie **range (z. B. A1:G100) und firstRowAsHeader=false**, dann können Daten aus allen Excel-Dateien geladen werden, obwohl der Spaltenname und die Anzahl unterschiedlich sind.

## <a name="delta-format"></a>Delta-Format

### <a name="the-sink-does-not-support-the-schema-drift-with-upsert-or-update"></a>Die Senke unterstützt die Schemabweichung mit upsert oder update nicht

#### <a name="symptoms"></a>Symptome
Es kann vorkommen, dass die Deltasenke in Zuordnungsdatenflüssen keine Schemaabweichung mit upsert/update unterstützt. Das Problem besteht darin, dass die Schemabweichung nicht funktioniert, wenn das Delta das Ziel in einem Zuordnungsdatenfluss ist und der Benutzer einen update-/upsert-Vorgang konfiguriert. 

Wenn der Quelle nach einem „anfänglichen“ Laden in das Delta eine Spalte hinzugefügt wird, tritt bei den nachfolgenden Aufträgen ein Fehler auf, dass die neue Spalte nicht gefunden wurde. Dies geschieht, wenn Sie upsert/update mit der Änderungszeile ausführen. Dies scheint nur für Einfügungen zu funktionieren.

#### <a name="error-message"></a>Fehlermeldung
`DF-SYS-01 at Sink 'SnkDeltaLake': org.apache.spark.sql.AnalysisException: cannot resolve target.BICC_RV in UPDATE clause given columns target. `

#### <a name="cause"></a>Ursache
Dies ist ein Problem beim Deltaformat aufgrund der Einschränkung der E/A-Deltabibliothek, die in der Datenflusslaufzeit verwendet wird. Dieses Problem wird noch behoben.

#### <a name="recommendation"></a>Empfehlung
Um dieses Problem zu beheben, müssen Sie zunächst das Schema aktualisieren und die Daten dann schreiben. Sie können folgende Schritte ausführen: <br/>
1. Erstellen Sie einen Datenfluss, der eine Deltasenke mit ausschließlicher Einfügungemöglichkeit mit der Option zum Mergen des Schemas enthält, um das Schema zu aktualisieren. 
1. Verwenden Sie nach Schritt 1 delete/upsert/update, um die Zielsenke zu ändern, ohne das Schema zu ändern. <br/>



## <a name="snowflake"></a>Snowflake

### <a name="unable-to-connect-to-the-snowflake-linked-service"></a>Es kann keine Verbindung mit dem verknüpften Snowflake-Dienst hergestellt werden

#### <a name="symptoms"></a>Symptome

Beim Erstellen des verknüpften Snowflake-Diensts im öffentlichen Netzwerk tritt der folgende Fehler auf, und Sie verwenden die Integration Runtime für automatische Auflösung.

`ERROR [HY000] [Microsoft][Snowflake] (4) REST request for URL https://XXXXXXXX.east-us- 2.azure.snowflakecomputing.com.snowflakecomputing.com:443/session/v1/login-request?requestId=XXXXXXXXXXXXXXXXXXXXXXXXX&request_guid=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXX` 

:::image type="content" source="./media/data-flow-troubleshoot-connector-format/connection-fail-error.png" alt-text="Screenshot: Verbindungsfehler."::: 

#### <a name="cause"></a>Ursache

Sie haben den Kontonamen nicht in dem Format angewendet, das im Snowflake-Kontodokument angegeben ist (einschließlich zusätzlicher Segmente, die die Region und Cloudplattform identifizieren), z. B. `XXXXXXXX.east-us-2.azure`. Weitere Informationen finden Sie in diesem Dokument: [Eigenschaften des verknüpften Diensts](./connector-snowflake.md#linked-service-properties).

#### <a name="recommendation"></a>Empfehlung

Um das Problem zu beheben, ändern Sie das Format des Kontonamens. Die Rolle sollte eine der in der folgenden Abbildung gezeigten Rollen sein, aber die Standardrolle ist **Öffentlich**.

:::image type="content" source="./media/data-flow-troubleshoot-connector-format/account-role.png" alt-text="Screenshot: Kontorollen."::: 

### <a name="sql-access-control-error-insufficient-privileges-to-operate-on-schema"></a>SQL-Zugriffssteuerungsfehler: „Unzureichende Berechtigungen zum Ausführen des Schemas“

#### <a name="symptoms"></a>Symptome

Wenn Sie versuchen, „import projection“ (Projektion importieren), „data preview“ (Datenvorschau) usw. in der Snowflake-Quelle von Datenflüssen zu verwenden, treten Fehler wie `net.snowflake.client.jdbc.SnowflakeSQLException: SQL access control error: Insufficient privileges to operate on schema` auf.

#### <a name="cause"></a>Ursache

Dieser Fehler tritt aufgrund der falschen Konfiguration auf. Wenn Sie den Datenfluss zum Lesen von Snowflake-Daten verwenden, wird die Abfrage für Snowflake nicht direkt von der Azure Databricks-Runtime (ADB) ausgewählt. Stattdessen wird eine temporäre Phase erstellt, und Daten werden aus Tabellen in die Phase abgerufen und dann von ADB komprimiert und gepullt. Die folgende Abbildung zeigt diesen Vorgang.

:::image type="content" source="./media/data-flow-troubleshoot-connector-format/snowflake-data-read-model.png" alt-text="Screenshot: Snowflake-Datenlesemodell."::: 

Daher sollte der in ADB verwendete Benutzer bzw. die Rolle über die erforderliche Berechtigung zum Ausführen dieses Vorgangs in Snowflake verfügen. In der Regel verfügt der Benutzer bzw. die Rolle jedoch nicht über die Berechtigung, da die Datenbank auf der Freigabe erstellt wird. 

#### <a name="recommendation"></a>Empfehlung
Um dieses Problem zu beheben, können Sie verschiedene Datenbanken und Ansichten über der freigegebenen Datenbank erstellen, um über ADB darauf zuzugreifen. Weitere Informationen finden Sie in [Snowflake](https://community.snowflake.com/s/question/0D50Z000095ktE4SAI/insufficient-privileges-to-operate-on-schema).

### <a name="failed-with-an-error-snowflakesqlexception-ip-xxxx-is-not-allowed-to-access-snowflake-contact-your-local-security-administrator"></a>Fehler: „SnowflakeSQLException: IP x.x.x.x darf nicht auf Snowflake zugreifen. Wenden Sie sich an Ihren lokalen Sicherheitsadministrator.“

#### <a name="symptoms"></a>Symptome

Wenn Sie Snowflake in Azure Data Factory verwenden, können Sie test-connection erfolgreich im verknüpften Snowflake-Dienst, preview-data/import-schema für das Snowflake-Dataset verwenden und copy/lookup/get-metadata oder andere Aktivitäten mit ihm ausführen. Wenn Sie Snowflake jedoch in der Datenflussaktivität verwenden, tritt möglicherweise ein Fehler wie `SnowflakeSQLException: IP 13.66.58.164 is not allowed to access Snowflake. Contact your local security administrator.` auf.

#### <a name="cause"></a>Ursache

Der Azure Data Factory Datenfluss unterstützt nicht die Verwendung fester IP-Adressbereiche. Ausführlichere Informationen finden Sie unter [Azure Integration Runtime IP-Adressen](./azure-integration-runtime-ip-addresses.md).

#### <a name="recommendation"></a>Empfehlung

Um dieses Problem zu beheben, können Sie die Firewalleinstellungen des Snowflake-Kontos mit den folgenden Schritten ändern:

1. Sie können die IP-Adressbereichsliste der Diensttags über den Downloadlink „Diensttags-IP-Adressbereich“ abrufen: [Ermitteln von Diensttags mithilfe herunterladbarer JSON-Dateien](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files).

    :::image type="content" source="./media/data-flow-troubleshoot-connector-format/ip-range-list.png" alt-text="Screenshot: Liste der IP-Adressbereiche."::: 

1. Wenn Sie einen Datenfluss in der Region „southcentralus“ ausführen, müssen Sie den Zugriff von allen Adressen mit dem Namen „AzureCloud.southcentralus“ zulassen. Beispiel:

    :::image type="content" source="./media/data-flow-troubleshoot-connector-format/allow-access-with-name.png" alt-text="Screenshot, der zeigt, wie der Zugriff von allen Adressen mit dem betreffenden Namen zugelassen wird."::: 

### <a name="queries-in-the-source-does-not-work"></a>Abfragen in der Quelle funktionieren nicht

#### <a name="symptoms"></a>Symptome

Wenn Sie versuchen, Daten aus Snowflake mithilfe einer Abfrage zu lesen, tritt möglicherweise ein Fehler wie der folgende auf:

1. `SQL compilation error: error line 1 at position 7 invalid identifier 'xxx'`
2. `SQL compilation error: Object 'xxx' does not exist or not authorized.`

#### <a name="cause"></a>Ursache

Dieser Fehler tritt aufgrund der falschen Konfiguration auf.

#### <a name="recommendation"></a>Empfehlung

Für Snowflake werden die folgenden Regeln zum Speichern von Bezeichnern zum Zeitpunkt der Erstellung/Definition und deren Auflösung in Abfragen und anderen SQL-Anweisungen angewendet:

Wenn ein Bezeichner (Tabellenname, Schemaname, Spaltenname usw.) nicht mit Anführungszeichen versehen ist, wird er standardmäßig in Großbuchstaben gespeichert und aufgelöst, und es wird nicht zwischen Groß- und Kleinschreibung unterschieden. Beispiel:

:::image type="content" source="./media/data-flow-troubleshoot-connector-format/unquoted-identifier.png" alt-text="Screenshot: Beispiel für Bezeichner ohne Anführungszeichen."lightbox="./media/data-flow-troubleshoot-connector-format/unquoted-identifier.png"::: 

Da keine Groß-/Kleinschreibung beachtet wird, können Sie die folgende Abfrage verwenden, um Snowflake-Daten zu lesen. Das Ergebnis ist identisch:<br/>
- `Select MovieID, title from Public.TestQuotedTable2`<br/>
- `Select movieId, title from Public.TESTQUOTEDTABLE2`<br/>
- `Select movieID, TITLE from PUBLIC.TESTQUOTEDTABLE2`<br/>

Wenn ein Bezeichner (Tabellenname, Schemaname, Spaltenname usw.) in doppelte Anführungszeichen eingeschlossen ist, wird er gespeichert und genau wie eingegeben aufgelöst, einschließlich der Groß-/Kleinschreibung, da diese beachtet wird. Die folgende Abbildung zeigt ein Beispiel. Weitere Informationen finden Sie in diesem Dokument: [Bezeichneranforderungen](https://docs.snowflake.com/en/sql-reference/identifiers-syntax.html#identifier-requirements).

:::image type="content" source="./media/data-flow-troubleshoot-connector-format/double-quoted-identifier.png" alt-text="Screenshot: Beispiel für einen in doppelte Anführungszeichen eingeschlossenen Bezeichner." lightbox="./media/data-flow-troubleshoot-connector-format/double-quoted-identifier.png"::: 

Da der Bezeichner mit Beachtung von Groß-/Kleinschreibung (Tabellenname, Schemaname, Spaltenname usw.) Kleinbuchstaben aufweist, müssen Sie den Bezeichner beim Lesen von Daten mit der Abfrage in Anführungszeichen einschließen. Beispiel: <br/>

- Wählen Sie **"movieId"** , **"title"** aus Public. **"testQuotedTable2"** aus.

Wenn bei der Snowflake-Abfrage ein Fehler auftritt, überprüfen Sie mit den folgenden Schritten, ob bei einigen Bezeichnern (Tabellenname, Schemaname, Spaltenname usw.) zwischen Groß- und Kleinschreibung unterschieden wird:

1. Melden Sie sich beim Snowflake-Server (`https://{accountName}.azure.snowflakecomputing.com/`, ersetzen Sie {accountName} durch Ihren Kontonamen) an, um den Bezeichner (Tabellenname, Schemaname, Spaltenname usw.) zu überprüfen.

1. Erstellen Sie Arbeitsblätter, um die Abfrage zu testen und zu überprüfen:
    - Führen Sie `Use database {databaseName}` aus, und ersetzen Sie dabei {databaseName} durch den Namen Ihrer Datenbank.
    - Führen Sie eine Abfrage mit einer Tabelle aus. Beispiel: `select "movieId", "title" from Public."testQuotedTable2"`
    
1. Nachdem die SQL-Abfrage von Snowflake getestet und überprüft wurde, können Sie sie direkt in der Snowflake-Quelle des Datenflusses verwenden.

### <a name="the-expression-type-does-not-match-the-column-data-type-expecting-variant-but-got-varchar"></a>Der Ausdruckstyp passt nicht zum Spaltendatentyp: VARIANT wurde erwartet, aber VARCHAR abgerufen. 

#### <a name="symptoms"></a>Symptome

Wenn Sie versuchen, Daten in die Snowflake-Tabelle zu schreiben, tritt möglicherweise der folgende Fehler auf:

`java.sql.BatchUpdateException: SQL compilation error: Expression type does not match column data type, expecting VARIANT but got VARCHAR`

#### <a name="cause"></a>Ursache

Der Spaltentyp der Eingabedaten ist „string“, der sich vom VARIANT-Typ der zugehörigen Spalte in der Snowflake-Senke unterscheidet.

Wenn Sie Daten mit komplexen Schemas (Array/Zuordnung/Struktur) in einer neuen Snowflake-Tabelle speichern, wird der Datenflusstyp automatisch in seinen physischen Typ VARIANT konvertiert.

:::image type="content" source="./media/data-flow-troubleshoot-connector-format/physical-type-variant.png" alt-text="Screenshot: VARIANT-Typ in einer Tabelle"::: 

Die zugehörigen Werte werden als JSON-Zeichenfolgen gespeichert, wie in der folgenden Abbildung dargestellt.

:::image type="content" source="./media/data-flow-troubleshoot-connector-format/json-string.png" alt-text="Screenshot: Gespeicherte JSON-Zeichenfolge"::: 

#### <a name="recommendation"></a>Empfehlung

Für den Snowflake-Typ VARIANT kann nur ein Datenflusswert vom Typ „struct“, „map“ oder „array“ akzeptiert werden. Wenn der Wert Ihrer Eingabedatenspalte JSON oder XML oder eine andere Zeichenfolge ist, verwenden Sie eine der folgenden Optionen, um dieses Problem zu lösen:

- **Option 1**: Verwenden Sie die [Analysetransformation](./data-flow-parse.md), bevor Sie Snowflake als Senke verwenden, um den Wert der Eingabedatenspalte z. B. in einen struct-, map- oder array-Typ zu konvertieren. Beispiel:

    :::image type="content" source="./media/data-flow-troubleshoot-connector-format/parse-transformation.png" alt-text="Screenshot: Analysetransformation"::: 

    > [!Note]
    > Der Wert der Snowflake-Spalte vom Typ VARIANT wird in Spark standardmäßig als Zeichenfolge gelesen.

- **Option 2**: Melden Sie sich an Ihrem Snowflake-Server an (`https://{accountName}.azure.snowflakecomputing.com/`, ersetzen Sie {accountName} durch Ihren Kontonamen), um das Schema Ihrer Snowflake-Zieltabelle zu ändern. Wenden Sie die folgenden Schritte an, indem Sie die Abfrage unter jedem Schritt ausführen.
    1. Erstellen Sie eine neue Spalte mit VARCHAR, um die Werte zu speichern. <br/>
        ```SQL
        alter table tablename add newcolumnname varchar;
        ```    
    1. Kopieren Sie den Wert von VARIANT in die neue Spalte. <br/>
    
        ```SQL
        update tablename t1 set newcolumnname = t1."details"
        ```
    1. Löschen Sie die nicht verwendete VARIANT-Spalte. <br/>
        ```SQL
        alter table tablename drop column "details";
        ```
    1. Benennen Sie die neue Spalte in den alten Namen um. <br/>
        ```SQL
        alter table tablename rename column newcolumnname to "details";
        ```

## <a name="next-steps"></a>Nächste Schritte
Weitere Hilfe zur Problembehandlung finden Sie in diesen Ressourcen:

*  [Problembehandlung bei Zuordnungsdatenflüssen in Azure Data Factory](data-flow-troubleshoot-guide.md)
*  [Data Factory-Blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory-Funktionsanfragen](/answers/topics/azure-data-factory.html)
*  [Azure-Videos](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Stack Overflow-Forum für Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter-Informationen über Data Factory](https://twitter.com/hashtag/DataFactory)
