---
title: Häufige Fehler und Meldungen beim Zuordnungsdatenfluss
description: Lernen Sie die Problembehandlung von häufigen Fehlercodes und Meldungen für Zuordnungsdatenflüsse in Azure Data Factory.
ms.author: makromer
author: kromerm
ms.reviewer: daperlov
ms.service: data-factory
ms.subservice: data-flows
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.openlocfilehash: 58b608c7e848dba2ea0d0e56532e51dcc4fe5a7b
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/02/2021
ms.locfileid: "129390529"
---
# <a name="common-error-codes-and-messages"></a>Allgemeine Fehlercodes und Meldungen 

Dieser Artikel listet häufige Fehlercodes und -meldungen auf, die von Zuordnungsdatenflüssen in Azure Data Factory zusammen mit den zugehörigen Ursachen und Empfehlungen gemeldet werden,.

## <a name="error-code-df-executor-sourceinvalidpayload"></a>Fehlercode: DF-Executor-SourceInvalidPayload
- **Meldung**: Data preview, debug, and pipeline data flow execution failed because container does not exist (Fehler beim Anzeigen von Daten in der Vorschau, beim Debuggen und bei der Datenflussausführung in der Pipeline, weil der Container nicht vorhanden ist)
- **Ursache**: Ein Dataset enthält einen Container, der im Speicher nicht vorhanden ist.
- **Empfehlung**: Stellen Sie sicher, dass der Container, auf den Sie in Ihrem Dataset verwiesen haben, vorhanden ist und darauf zugegriffen werden kann.

## <a name="error-code-df-executor-systeminvalidjson"></a>Fehlercode: DF-Executor-SystemInvalidJson

- **Meldung**: JSON parsing error, unsupported encoding or multiline (JSON-Analysefehler: nicht unterstützte Codierung oder mehrzeiliger Text)
- **Ursache**: Mögliche Probleme bei der JSON-Datei: nicht unterstützte Codierung, beschädigte Bytes oder Verwendung der JSON-Quelle als einzelnes Dokument in vielen geschachtelten Zeilen.
- **Empfehlung**: Überprüfen Sie, ob die Codierung der JSON-Datei unterstützt wird. Erweitern Sie in der Quelltransformation, die ein JSON-Dataset verwendet, die Option **JSON-Einstellungen**, und aktivieren Sie **Einzelnes Dokument**.
 
## <a name="error-code-df-executor-broadcasttimeout"></a>Fehlercode: DF-Executor-BroadcastTimeout

- **Meldung**: Broadcast join timeout error, make sure broadcast stream produces data within 60 secs in debug runs and 300 secs in job runs (Übertragungsjoin-Timeoutfehler: Stellen Sie sicher, dass der Übertragungsdatenstrom in Debugausführungen Daten innerhalb von 60 Sekunden und in Auftragsausführungen innerhalb von 300 Sekunden Daten erzeugt.)
- **Ursache**: Für Übertragungen gilt bei Debugausführungen ein Standardtimeout von 60 Sekunden und bei Auftragsausführungen ein Standardtimeout von 300 Sekunden. Der für die Übertragung ausgewählte Datenstrom ist zu groß, um innerhalb dieser Zeitspanne Daten zu erzeugen.
- **Empfehlung**: Überprüfen Sie die Registerkarte **Optimieren** bei Ihren Datenflusstransformationen auf „join“, „exists“ und „lookup“. Die Standardoption für die Übertragung ist **Auto**. Wenn **Auto** festgelegt ist oder Sie die linke oder rechte Seite unter **Fixed** für die Übertragung manuell festlegen, können Sie entweder eine größere Azure Integration Runtime (IR)-Konfiguration festlegen oder die Übertragung deaktivieren. Für die optimale Leistung bei Datenflüssen empfehlen wir, Spark zu erlauben, mithilfe von **Auto** zu übertragen und eine arbeitsspeicheroptimierte Azure IR zu verwenden. 
 
  Wenn Sie den Datenfluss in einer Debugtestausführung über eine Debugpipelineausführung ausführen, könnte diese Bedingung häufiger auftreten. Der Grund: Azure Data Factory drosselt das Übertragungstimeout auf 60 Sekunden, um ein schnelleres Debuggen zu ermöglichen. Sie können das Timeout auf das 300-Sekunden-Timeout einer ausgelösten Ausführung verlängern. Zu diesem Zweck können Sie die Option **Debug** > **Aktivitätsruntime verwenden** auswählen, um die in Ihrer Aktivität „Datenflusspipeline ausführen“ definierte Azure IR zu verwenden.

- **Meldung:** Broadcastjoin-Timeoutfehler: Sie können die Option „Broadcast“ in der join-/exists-/lookup-Transformation auf „Aus“ festlegen, um dieses Problem zu vermeiden. Wenn Sie die Broadcastjoinoption nutzen möchten, um die Leistung zu verbessern, stellen Sie sicher, dass der Broadcast-Datenstrom Daten in Debugausführungen innerhalb von 60 Sekunden und in Auftragsausführungen von 300 Sekunden erstellen kann.
- **Ursache**: Für Übertragungen gilt bei Debugausführungen ein Standardtimeout von 60 Sekunden und bei Auftragsausführungen ein Standardtimeout von 300 Sekunden. Der bei einem Broadcastjoin für die Übertragung ausgewählte Datenstrom ist zu groß, um innerhalb dieser Zeitspanne Daten zu erzeugen. Wenn kein Broadcastjoin verwendet wird, kann der Standardbroadcast durch den Datenfluss denselben Grenzwert erreichen.
- **Empfehlung**: Deaktivieren Sie die Broadcastoption, oder vermeiden Sie die Übertragung großer Datenströme, deren Verarbeitung länger als 60 Sekunden dauern kann. Wählen Sie einen kleineren Datenstrom für die Übertragung. Große Azure SQL Data Warehouse-Tabellen und -Quelldateien sind normalerweise keine gute Wahl. Sollte kein Broadcastjoin vorhanden sein, verwenden Sie einen größeren Cluster, wenn dieser Fehler auftritt.

## <a name="error-code-df-executor-conversion"></a>Fehlercode: DF-Executor-Conversion

- **Meldung**: Converting to a date or time failed due to an invalid character (Fehler beim Umwandeln eines Datums oder einer Uhrzeit aufgrund eines ungültigen Zeichens)
- **Ursache**: Die Daten haben nicht das erwartete Format.
- **Empfehlung**: Verwenden Sie den richtigen Datentyp.

## <a name="error-code-df-executor-invalidcolumn"></a>Fehlercode: DF-Executor-InvalidColumn
- **Meldung**: Column name needs to be specified in the query, set an alias if using a SQL function (Spaltenname muss in der Abfrage angegeben werden. Legen Sie einen Alias fest, wenn Sie eine SQL-Funktion verwenden.)
- **Ursache**: Es wurde kein Spaltenname angegeben.
- **Empfehlung**: Legen Sie einen Alias fest, wenn Sie eine SQL-Funktion wie „min()“ oder „max()“ verwenden.

## <a name="error-code-df-executor-drivererror"></a>Fehlercode: DF-Executor-DriverError
- **Meldung**: INT96 ist ein älterer Zeitstempeltyp, der vom ADF-Datenfluss nicht unterstützt wird. Sie sollten den Spaltentyp auf die aktuellen Typen aktualisieren.
- **Ursache**: Treiberfehler.
- **Empfehlung**: INT96 ist ein Legacy-Zeitstempeltyp, der von Azure Data Factory-Datenfluss nicht unterstützt wird. Sie sollten den Spaltentyp auf den neuesten Typ aktualisieren.

## <a name="error-code-df-executor-blockcountexceedslimiterror"></a>Fehlercode: DF-Executor-BlockCountExceedsLimitError
- **Meldung**: Die Anzahl der Blöcke ohne Commit darf den maximalen Grenzwert von 100.000 Blöcken nicht überschreiten. Überprüfen Sie die Blobkonfiguration.
- **Ursache**: Die maximale Anzahl von Blöcken ohne Commit in einem Blob ist 100.000.
- **Empfehlung**: Weitere Informationen zu diesem Problem erhalten Sie vom Microsoft-Produktteam.

## <a name="error-code-df-executor-partitiondirectoryerror"></a>Fehlercode: DF-Executor-PartitionDirectoryError
- **Meldung**: Der angegebene Quellpfad weist entweder mehrere partitionierte Verzeichnisse (z. B. &lt;Quellpfad&gt;/<Partitionsstammverzeichnis 1>/a=10/b=20, &lt;Quellpfad&gt;/&lt;Partitionsstammverzeichnis 2&gt;/c=10/d=30) oder ein partitioniertes Verzeichnis mit einer anderen Datei oder einem nicht partitionierten Verzeichnis auf (z. B. &lt;Quellpfad&gt;/&lt;Partitionsstammverzeichnis 1&gt;/a=10/b=20, &lt;Quellpfad&gt;/Verzeichnis 2/Datei1). Entfernen Sie das Partitionsstammverzeichnis aus dem Quellpfad, und lesen Sie es durch eine separate Quelltransformation.
- **Ursache**: Der Quellpfad enthält entweder mehrere partitionierte Verzeichnisse oder ein partitioniertes Verzeichnis mit einer anderen Datei oder einem nicht partitionierten Verzeichnis.
- **Empfehlung**: Entfernen Sie das partitionierte Stammverzeichnis aus dem Quellpfad, und lesen Sie es durch eine separate Quelltransformation.

## <a name="error-code-df-executor-invalidtype"></a>Fehlercode: DF-Executor-InvalidType
- **Meldung**: Stellen Sie sicher, dass der Parametertyp mit dem Typ des übergebenen Werts übereinstimmt. Das Übergeben von float-Parametern aus Pipelines wird derzeit nicht unterstützt.
- **Ursache**: Der Datentyp für den deklarierten Typ ist mit dem tatsächlichen Parameterwert nicht kompatibel.
- **Empfehlung**: Vergewissern Sie sich, dass die an den Datenfluss übergebenen Parameterwerte dem deklarierten Typ entsprechen.

## <a name="error-code-df-executor-parseerror"></a>Fehlercode: DF-Executor-ParseError
- **Meldung:** Der Ausdruck kann nicht analysiert werden.
- **Ursache**: Ein Ausdruck hat aufgrund von falscher Formatierung Analysefehler generiert.
- **Empfehlung**: Überprüfen Sie die Formatierung im Ausdruck.

## <a name="error-code-df-executor-systemimplicitcartesian"></a>Fehlercode: DF-Executor-SystemImplicitCartesian
- **Meldung**: Implicit cartesian product for INNER join is not supported, use CROSS JOIN instead. (Implizites kartesisches Produkt wird für INNER JOIN nicht unterstützt. Verwenden Sie stattdessen CROSS JOIN.) Die im Join verwendeten Spalten müssen einen eindeutigen Schlüssel für Zeilen erstellen.
- **Ursache**: Implizite kartesische Produkte für INNER JOINs zwischen logischen Plänen werden nicht unterstützt. Wenn Sie Spalten im Join verwenden, erstellen Sie einen eindeutigen Schlüssel.
- **Empfehlung**: Verwenden Sie bei nicht gleichheitsbasierten Joins CROSS JOIN.

## <a name="error-code-getcommand-outputasync-failed"></a>Fehlercode: GetCommand OutputAsync failed (Fehler bei GetCommand OutputAsync)
- **Meldung**: During Data Flow debug and data preview: GetCommand OutputAsync failed (Beim Debuggen des Datenflusses und Anzeigen von Daten in der Vorschau: Fehler bei GetCommand OutputAsync mit ...)
- **Ursache:** Dies ist ein Fehler beim Back-End-Dienst. 
- **Empfehlung**: Wiederholen Sie den Vorgang, und starten Sie Ihre Debugsitzung neu. Wenn das Problem durch Wiederholung und Neustart nicht behoben werden kann, wenden Sie sich an den Kundensupport. 

## <a name="error-code-df-executor-outofmemoryerror"></a>Fehlercode: DF-Executor-OutOfMemoryError
 
- **Meldung**: Nicht genügend Arbeitsspeicher während der Ausführung im Cluster verfügbar. Wiederholen Sie den Vorgang mit einer Integration Runtime mit einer größeren Anzahl von Kernen und/oder einem arbeitsspeicheroptimierten Computetyp.
- **Ursache**: Der Cluster verfügt nicht über genügend Arbeitsspeicher.
- **Empfehlung**: Debugcluster sind für die Entwicklung gedacht. Nutzen Sie die Datenstichprobenentnahme, den entsprechenden Computetyp und die richtige Größe zur Ausführung der Payload. Leistungstipps finden Sie in der [Mapping Data Flow Performance Guide](concepts-data-flow-performance.md) (Anleitung zur Leistung von Zuordnungsdatenflüssen).

## <a name="error-code-df-executor-illegalargument"></a>Fehlercode: DF-Executor-illegalArgument

- **Meldung**: Stellen Sie sicher, dass in Ihrem verknüpften Dienst der richtige Zugriffsschlüssel verwendet wird.
- **Ursache**: Der Kontoname oder Zugriffsschlüssel ist falsch.
- **Empfehlung**: Stellen Sie sicher, dass in Ihrem verknüpften Dienst der richtige Kontoname oder Zugriffsschlüssel angegeben ist. 

## <a name="error-code-df-executor-columnunavailable"></a>Fehlercode: DF-Executor-ColumnUnavailable
- **Meldung:** Der im Ausdruck verwendete Spaltenname ist nicht verfügbar oder ungültig.
- **Ursache**: In einem Ausdruck wird ein ungültiger oder nicht verfügbarer Spaltenname verwendet.
- **Empfehlung**: Überprüfen Sie die in Ausdrücken verwendeten Spaltennamen.

 ## <a name="error-code-df-executor-outofdiskspaceerror"></a>Fehlercode: DF-Executor-OutOfDiskSpaceError
- **Meldung:** Interner Serverfehler
- **Ursache**: Der Cluster verfügt nicht über genügend Speicherplatz.
- **Empfehlung**: Führen Sie die Pipeline erneut aus. Wenn das Problem dadurch nicht behoben wird, wenden Sie sich an den Kundensupport.


 ## <a name="error-code-df-executor-storeisnotdefined"></a>Fehlercode: DF-Executor-StoreIsNotDefined
- **Meldung:** Die Speicherkonfiguration ist nicht definiert. Dieser Fehler wird möglicherweise durch eine ungültige Parameterzuweisung in der Pipeline verursacht.
- **Ursache**: Es wurde eine ungültige Speicherkonfiguration angegeben.
- **Empfehlung**: Überprüfen Sie die Zuweisung von Parameterwerten in der Pipeline. Ein Parameterausdruck enthält möglicherweise ungültige Zeichen.


## <a name="error-code-4502"></a>Fehlercode: 4502
- **Meldung:** There are substantial concurrent MappingDataflow executions that are causing failures due to throttling under Integration Runtime. (Es gibt viele gleichzeitige MappingDataflow-Ausführungen, die aufgrund von Drosselung unter Integration Runtime zu Fehlern führen).
- **Ursache**: Auf der Integration Runtime wird eine große Anzahl von Datenflussaktivitäten gleichzeitig ausgeführt. Weitere Informationen finden Sie unter [Data Factory-Grenzwerte](../azure-resource-manager/management/azure-subscription-service-limits.md#data-factory-limits).
- **Empfehlung**: Wenn Sie weitere Datenflussaktivitäten parallel ausführen möchten, verteilen Sie sie auf mehrere Integration Runtime-Instanzen.

## <a name="error-code-4510"></a>Fehlercode: 4510
- **Meldung**: Unerwarteter Fehler während der Ausführung. 
- **Ursache**: Da Debugcluster anders funktionieren als Auftragscluster, können übermäßige Debugläufe den Cluster im Laufe der Zeit verschleißen, was zu Arbeitsspeicherproblemen und plötzlichen Neustarts führen kann.
- **Empfehlung**: Starten Sie den Debugcluster neu. Wenn Sie während der Debugsitzung mehrere Dataflows ausführen, verwenden Sie stattdessen Aktivitätsläufe, da die Ausführung auf Aktivitätsebene eine separate Sitzung erstellt, ohne den Hauptdebuggingcluster zu besteuern.

## <a name="error-code-invalidtemplate"></a>Fehlercode: InvalidTemplate
- **Meldung:** The pipeline expression cannot be evaluated. (Der Pipelineausdruck kann nicht ausgewertet werden.)
- **Ursache**: Der in der Datenflussaktivität übergebene Pipelineausdruck wird aufgrund eines Syntaxfehlers nicht richtig verarbeitet.
- **Empfehlung**: Überprüfen Sie Ihre Aktivität in der Aktivitätsüberwachung zur Überprüfung des Ausdrucks.

## <a name="error-code-2011"></a>Fehlercode: 2011
- **Meldung:** The activity was running on Azure Integration Runtime and failed to decrypt the credential of data store or compute connected via a Self-hosted Integration Runtime. (Die Aktivität wurde in Azure Integration Runtime ausgeführt und konnte die Anmeldeinformationen für den Datenspeicher oder Compute, der/das über eine selbstgehostete Integration Runtime verbunden war, nicht entschlüsseln.) Bitte überprüfen Sie die Konfiguration der verknüpften Dienste, die dieser Aktivität zugeordnet sind, und stellen Sie sicher, dass Sie den richtigen Integration Runtime-Typ verwenden.
- **Ursache**: Der Datenfluss unterstützt keine verknüpften Dienste für selbstgehostete Integration Runtime-Instanzen.
- **Empfehlung**: Konfigurieren Sie den Datenfluss zur Ausführung in einer Integration Runtime mit „Verwaltetes virtuelles Netzwerk“.

## <a name="error-code-df-xml-invalidvalidationmode"></a>Fehlercode: DF-Xml-InvalidValidationMode
- **Meldung:** Es wurde ein ungültiger XML-Überprüfungsmodus angegeben.
- **Ursache**: Es wurde ein ungültiger XML-Überprüfungsmodus angegeben.
- **Empfehlung:** Überprüfen Sie den Parameterwert, und geben Sie den richtigen Überprüfungsmodus an.

## <a name="error-code-df-xml-invaliddatafield"></a>Fehlercode: DF-Xml-InvalidDataField
- **Meldung:** Das Feld für beschädigte Datensätze muss den Typ „String“ haben und Nullwerte zulassen.
- **Ursache**: In der XML-Quelle ist für die Spalte `\"_corrupt_record\"` ein ungültiger Datentyp angegeben.
- **Empfehlung**: Stellen Sie sicher, dass die Spalte `\"_corrupt_record\"` in der XML-Quelle den Datentyp „string“ aufweist und NULL-Werte akzeptiert.

## <a name="error-code-df-xml-malformedfile"></a>Fehlercode: DF-Xml-MalformedFile
- **Meldung**: Falsch formatierte XML-Datei mit Pfad im FAILFAST-Modus.
- **Ursache**: Es ist eine falsch formatierte XML-Datei mit Pfad im FAILFAST-Modus vorhanden.
- **Empfehlung:** Aktualisieren Sie den Inhalt der XML-Datei in das richtige Format.

## <a name="error-code-df-xml-invalidreferenceresource"></a>Fehlercode: DF-Xml-InvalidReferenceResource
- **Meldung**: Die Verweisressource in der XML-Datendatei kann nicht aufgelöst werden.
- **Ursache**: Die Verweisressource in der XML-Datendatei kann nicht aufgelöst werden.
- **Empfehlung:** Überprüfen Sie die Verweisressource in der XML-Datendatei.

## <a name="error-code-df-xml-invalidschema"></a>Fehlercode: DF-Xml-InvalidSchema
- **Meldung:** Fehler bei der Schemaüberprüfung.
- **Ursache**: Für die XML-Quelle wurde ein ungültiges Schema angegeben.
- **Empfehlung**: Überprüfen Sie die Schemaeinstellungen für die XML-Quelle, um sicherzustellen, dass es sich um das Teilmengenschema der Quelldaten handelt.

## <a name="error-code-df-xml-unsupportedexternalreferenceresource"></a>Fehlercode: DF-Xml-UnsupportedExternalReferenceResource
- **Meldung:** Die externe Verweisressource in der XML-Datendatei wird nicht unterstützt.
- **Ursache**: Die externe Verweisressource in der XML-Datendatei wird nicht unterstützt.
- **Empfehlung:** Aktualisieren Sie den Inhalt der XML-Datei, wenn die externe Verweisressource aktuell nicht unterstützt wird.

## <a name="error-code-df-gen2-invalidaccountconfiguration"></a>Fehlercode: DF-GEN2-InvalidAccountConfiguration
- **Meldung:** Entweder der Kontoschlüssel oder tenant/spnId/spnCredential/spnCredentialType oder miServiceUri/miServiceToken muss angegeben werden.
- **Ursache**: Im verknüpften ADLS Gen2-Dienst wurde eine ungültige Anmeldeinformation angegeben.
- **Empfehlung**: Aktualisieren Sie den verknüpften ADLS Gen2-Dienst, damit dieser die richtigen Anmeldeinformationen umfasst.

## <a name="error-code-df-gen2-invalidauthconfiguration"></a>Fehlercode: DF-GEN2-InvalidAuthConfiguration
- **Meldung:** Es kann nur eine der drei Authentifizierungsmethoden (Key, ServicePrincipal und MI) angegeben werden.
- **Ursache**: Im verknüpften ADLS Gen2-Dienst ist eine ungültige Authentifizierungsmethode angegeben.
- **Empfehlung**: Aktualisieren Sie den verknüpften ADLS Gen2-Dienst, sodass er eine der folgenden drei Authentifizierungsmethoden verwendet: Key, ServicePrincipal, MI.

## <a name="error-code-df-gen2-invalidserviceprincipalcredentialtype"></a>Fehlercode: DF-GEN2-InvalidServicePrincipalCredentialType
- **Meldung**: Der Typ der Anmeldeinformationen für den Dienstprinzipal ist ungültig.
- **Ursache**: Der Typ der Anmeldeinformationen für den Dienstprinzipal ist ungültig.
- **Empfehlung**: Aktualisieren Sie den verknüpften ADLS Gen2, um den richtigen Anmeldeinformationstyp für den Dienstprinzipal festzulegen.

## <a name="error-code-df-blob-invalidaccountconfiguration"></a>Fehlercode: DF-Blob-InvalidAccountConfiguration
- **Meldung**: Es muss entweder ein Kontoschlüssel oder ein SAS-Token angegeben werden.
- **Ursache**: Im verknüpften Azure-Blobdienst wurde eine ungültige Anmeldeinformation angegeben.
- **Empfehlung**: Verwenden Sie entweder einen Kontoschlüssel oder ein SAS-Token für den verknüpften Azure-Blobdienst.

## <a name="error-code-df-blob-invalidauthconfiguration"></a>Fehlercode: DF-Blob-InvalidAuthConfiguration
- **Meldung:** Es kann nur eine der beiden Authentifizierungsmethoden (Key, SAS) angegeben werden.
- **Ursache**: Im verknüpften Dienst ist eine ungültige Authentifizierungsmethode angegeben.
- **Empfehlung**: Verwenden Sie die Schlüssel- oder SAS-Authentifizierung für den verknüpften Azure-Blobdienst.

## <a name="error-code-df-cosmos-partitionkeymissed"></a>Fehlercode: DF-Cosmos-PartitionKeyMissed
- **Meldung:** Der Partitionsschlüsselpfad muss für Aktualisierungs- und Löschvorgänge angegeben werden.
- **Ursache**: In der Azure Cosmos DB-Senke fehlt der Partitionsschlüsselpfad.
- **Empfehlung**: Verwenden Sie den angegebenen Partitionsschlüssel in den Einstellungen der Azure Cosmos DB-Senke.

## <a name="error-code-df-cosmos-invalidpartitionkey"></a>Fehlercode: DF-Cosmos-InvalidPartitionKey
- **Meldung:** Der Partitionsschlüsselpfad darf für Aktualisierungs- und Löschvorgänge nicht leer sein.
- **Meldung**: Der Partitionsschlüsselpfad für Aktualisierungs- und Löschvorgänge ist leer.
- **Empfehlung**: Verwenden Sie den angegebenen Partitionsschlüssel in den Einstellungen der Azure Cosmos DB-Senke.

- **Meldung**: Der Partitionsschlüssel ist in der Senke für Lösch- und Aktualisierungsvorgänge nicht zugeordnet.
- **Ursache**: Es wurde ein ungültiger Partitionsschlüssel angegeben.
- **Empfehlung**: Verwenden Sie in den Einstellungen der Cosmos DB-Senke den richtigen Partitionsschlüssel, der dem Ihres Containers entspricht.

## <a name="error-code-df-cosmos-idpropertymissed"></a>Fehlercode: DF-Cosmos-IdPropertyMissed
- **Meldung:** Die id-Eigenschaft muss für Lösch- und Aktualisierungsvorgänge zugeordnet werden.
- **Ursache**: Die Eigenschaft `id` für Aktualisierungs- und Löschvorgänge fehlt.
- **Empfehlung**: Stellen Sie sicher, dass die Eingabedaten eine Spalte `id` in den Einstellungen der Cosmos DB-Senke aufweisen. Wenn das nicht der Fall ist, verwenden Sie eine **Auswahl- oder Ableitungstransformation**, um diese Spalte vor der Senke zu generieren.

## <a name="error-code-df-cosmos-invalidpartitionkeycontent"></a>Fehlercode: DF-Cosmos-InvalidPartitionKeyContent
- **Meldung:** Der Partitionsschlüssel muss mit „/“ beginnen.
- **Ursache**: Es wurde ein ungültiger Partitionsschlüssel angegeben.
- **Empfehlung**: Stellen Sie sicher, dass der Partitionsschlüssel in den Einstellungen der Cosmos DB-Senke mit `/` beginnt. Beispiel: `/movieId`.

## <a name="error-code-df-cosmos-invalidconnectionmode"></a>Fehlercode: DF-Cosmos-InvalidConnectionMode
- **Meldung**: Ungültiger Verbindungsmodus.
- **Ursache**: Es ist ein ungültiger Verbindungsmodus angegeben.
- **Empfehlung**: Stellen Sie sicher, dass der unterstützte Modus in den Cosmos DB-Einstellungen **Gateway** und **DirectHttps** lautet.

## <a name="error-code-df-cosmos-invalidaccountconfiguration"></a>Fehlercode: DF-Cosmos-InvalidAccountConfiguration
- **Meldung:** Es muss entweder accountName oder accountEndpoint angegeben werden.
- **Ursache**: Es wurden ungültige Kontoinformationen angegeben.
- **Empfehlung**: Geben Sie im verknüpften Cosmos DB-Dienst den Kontonamen oder den Kontoendpunkt an.

## <a name="error-code-df-github-writenotsupported"></a>Fehlercode: DF-Github-WriteNotSupported
- **Meldung:** Im GitHub-Speicher sind Schreibvorgänge nicht zulässig.
- **Ursache**: Der GitHub-Speicher ist schreibgeschützt.
- **Empfehlung**: Die Definition der Speicherentität befindet sich an einer anderen Stelle.
  
## <a name="error-code-df-pgsql-invalidcredential"></a>Fehlercode: DF-PGSQL-InvalidCredential
- **Meldung:** Benutzer/Kennwort muss angegeben werden.
- **Ursache**: Der Benutzer oder das Kennwort fehlt.
- **Empfehlung**: Stellen Sie sicher, dass die richtigen Anmeldeinformationen im zugehörigen verknüpften PostgreSQL-Dienst festgelegt sind.

## <a name="error-code-df-snowflake-invalidstageconfiguration-only-blob-storage-can-be-used-as-stage"></a>Fehlercode: DF-Snowflake-InvalidStageConfiguration (nur Blobspeicher kann als Stage verwendet werden)
- **Meldung:** Nur der Typ „Blob Storage“ kann als Stage in Lese-/Schreibvorgängen in Snowflake verwendet werden.
- **Ursache**: In Snowflake ist eine ungültige Stagingkonfiguration angegeben.
- **Empfehlung**: Aktualisieren Sie die Snowflake-Stagingeinstellungen, sodass nur der verknüpfte Azure-Blobdienst verwendet wird.

- **Meldung**: Die Snowflake-Phaseneigenschaften müssen mit der Azure Blob- und SAS-Authentifizierung angegeben werden.
- **Ursache**: In Snowflake ist eine ungültige Stagingkonfiguration angegeben.
- **Empfehlung**: Stellen Sie sicher, dass in den Snowflake-Stagingeinstellungen nur die Azure-Blob- und die SAS-Authentifizierung angegeben ist.

## <a name="error-code-df-snowflake-invaliddatatype"></a>Fehlercode: DF-Snowflake-InvalidDataType
- **Meldung:** Der Spark-Typ wird in Snowflake nicht unterstützt.
- **Ursache**: In Snowflake wurde ein ungültiger Datentyp angegeben.
- **Empfehlung**: Verwenden Sie vor dem Anwenden der Snowflake-Senke die Ableitungstransformation, um die verknüpfte Spalte mit den Eingabedaten in den Typ „string“ zu aktualisieren.

## <a name="error-code-df-hive-invalidblobstagingconfiguration"></a>Fehlercode: DF-Hive-InvalidBlobStagingConfiguration
- **Meldung:** Die Stagingeigenschaften des Blobspeichers müssen angegeben werden.
- **Ursache**: In Hive ist eine ungültige Stagingkonfiguration angegeben.
- **Empfehlung**: Überprüfen Sie, ob der Kontoschlüssel, der Kontoname und der Container im verknüpften Blobdienst, der für das Staging verwendet wird, ordnungsgemäß festgelegt sind.

## <a name="error-code-df-hive-invalidgen2stagingconfiguration"></a>Fehlercode: DF-Hive-InvalidGen2StagingConfiguration
- **Meldung:** Im ADLS Gen2-Stagingspeicher werden nur Anmeldeinformationen für den Dienstprinzipalschlüssel unterstützt.
- **Ursache**: In Hive ist eine ungültige Stagingkonfiguration angegeben.
- **Empfehlung**: Aktualisieren Sie den verknüpften ADLS Gen2-Dienst, der für das Staging verwendet wird. Derzeit werden nur die Anmeldeinformationen des Dienstprinzipalschlüssels unterstützt.

- **Meldung:** Die Stagingeigenschaften des ADLS Gen2-Speichers müssen angegeben werden. Entweder der Schlüssel oder tenant/spnId/spnKey oder miServiceUri/miServiceToken ist erforderlich.
- **Ursache**: In Hive ist eine ungültige Stagingkonfiguration angegeben.
- **Empfehlung**: Aktualisieren Sie den verknüpften ADLS Gen2-Dienst mit den richtigen Anmeldeinformationen für das Staging in Hive.

## <a name="error-code-df-hive-invaliddatatype"></a>Fehlercode: DF-Hive-InvalidDataType
- **Meldung:** Nicht unterstützte Spalte(n).
- **Ursache**: Es wurde nicht unterstützte Spalten angegeben.
- **Empfehlung**: Aktualisieren Sie die Spalte mit den Eingabedaten, sodass sie dem von Hive unterstützten Datentyp entsprechen.

## <a name="error-code-df-hive-invalidstoragetype"></a>Fehlercode: DF-Hive-InvalidStorageType
- **Meldung:** Als Speichertyp kann entweder „Blob“ oder „Gen2“ festgelegt werden.
- **Ursache**: Als Speichertyp werden nur „Azure-Blob“ und „ADLS Gen2“ unterstützt.
- **Empfehlung**: Wählen Sie den richtigen Speichertyp aus. Unterstützt werden „Azure-Blob“ und „ADLS Gen2“.

## <a name="error-code-df-delimited-invalidconfiguration"></a>Fehlercode: DF-Delimited-InvalidConfiguration
- **Meldung:** Es müssen entweder leere Zeilen oder benutzerdefinierte Header angegeben werden.
- **Ursache**: Es wurde eine Konfiguration mit einem ungültigen Trennzeichen angegeben.
- **Empfehlung**: Aktualisieren Sie die CSV-Einstellungen, um leere Zeilen oder benutzerdefinierte Header zu verwenden.

## <a name="error-code-df-delimited-columndelimitermissed"></a>Fehlercode: DF-Delimited-ColumnDelimiterMissed
- **Meldung**: Das Spaltentrennzeichen ist für die Analyse erforderlich.
- **Ursache**: Das Spaltentrennzeichen fehlt.
- **Empfehlung**: Vergewissern Sie sich, dass in den CSV-Einstellungen das für Analyse benötigte Spaltentrennzeichen angegeben wurde. 

## <a name="error-code-df-mssql-invalidcredential"></a>Fehlercode: DF-MSSQL-InvalidCredential
- **Meldung:** Entweder user/pwd oder tenant/spnId/spnKey oder miServiceUri/miServiceToken muss angegeben werden.
- **Ursache**: Im verknüpften MSSQL-Dienst wurde eine ungültige Anmeldeinformation angegeben.
- **Empfehlung**: Aktualisieren Sie den zugehörigen verknüpften MSSQL-Dienst mit den richtigen Anmeldeinformationen. Es muss einer der folgenden Werte angegeben werden: **user/pwd**, **tenant/spnId/spnKey** oder **miServiceUri/miServiceToken**.

## <a name="error-code-df-mssql-invaliddatatype"></a>Fehlercode: DF-MSSQL-InvalidDataType
- **Meldung:** Nicht unterstützte(s) Feld(er).
- **Ursache**: Es wurden nicht unterstützte Felder angegeben.
- **Empfehlung:** Ändern Sie die Spalte der Eingabedaten so, dass sie dem in MSSQL unterstützten Datentyp entspricht.

## <a name="error-code-df-mssql-invalidauthconfiguration"></a>Fehlercode: DF-MSSQL-InvalidAuthConfiguration
- **Meldung:** Es kann nur eine der drei Authentifizierungsmethoden (Key, ServicePrincipal und MI) angegeben werden.
- **Ursache**: Im verknüpften MSSQL-Dienst ist eine ungültige Authentifizierungsmethode angegeben.
- **Empfehlung**: Sie können im zugehörigen verknüpften MSSQL-Dienst nur eine der drei verfügbaren Authentifizierungsmethoden („Key“, „ServicePrincipal“ und „MI“) angeben.

## <a name="error-code-df-mssql-invalidcloudtype"></a>Fehlercode: DF-MSSQL-InvalidCloudType
- **Meldung:** Cloudtyp ist ungültig.
- **Ursache**: Es wurde ein ungültiger Cloudtyp angegeben.
- **Empfehlung:** Überprüfen Sie den Cloudtyp im zugehörigen verknüpften MSSQL-Dienst.

## <a name="error-code-df-sqldw-invalidblobstagingconfiguration"></a>Fehlercode: DF-SQLDW-InvalidBlobStagingConfiguration
- **Meldung:** Die Stagingeigenschaften des Blobspeichers müssen angegeben werden.
- **Meldung**: Es wurden ungültige Stagingeinstellungen für den Blobspeicher angegeben.
- **Empfehlung**: Überprüfen Sie, ob der verknüpfte Blobdienst, der für das Staging verwendet wird, die richtigen Eigenschaften aufweist.

## <a name="error-code-df-sqldw-invalidstoragetype"></a>Fehlercode: DF-SQLDW-InvalidStorageType
- **Meldung:** Als Speichertyp kann entweder „Blob“ oder „Gen2“ festgelegt werden.
- **Ursache**: Für das Staging wurde ein ungültiger Speichertyp angegeben.
- **Empfehlung**: Überprüfen Sie den Speichertyp des verknüpften Diensts, der für das Staging verwendet wird. Stellen Sie sicher, dass der Typ entweder „Blob“ oder „Gen2“ lautet.

## <a name="error-code-df-sqldw-invalidgen2stagingconfiguration"></a>Fehlercode: DF-SQLDW-InvalidGen2StagingConfiguration
- **Meldung:** Im ADLS Gen2-Stagingspeicher werden nur Anmeldeinformationen für den Dienstprinzipalschlüssel unterstützt.
- **Ursache**: Für das ADLS Gen2-Speicherstaging wurden ungültige Anmeldeinformationen angegeben.
- **Empfehlung**: Verwenden Sie die Anmeldeinformationen für den Dienstprinzipalschlüssel des verknüpften Gen2-Diensts, der für das Staging verwendet wird.
 

## <a name="error-code-df-sqldw-invalidconfiguration"></a>Fehlercode: DF-SQLDW-InvalidConfiguration
- **Meldung:** Die Stagingeigenschaften des ADLS Gen2-Speichers müssen angegeben werden. Entweder der Schlüssel oder tenant/spnId/spnCredential/spnCredentialType oder miServiceUri/miServiceToken ist erforderlich.
- **Ursache**: Es wurden ungültige ADLS Gen2-Stagingeigenschaften angegeben.
- **Empfehlung**: Aktualisieren Sie die Einstellungen für das ADLS Gen2-Speicherstaging, sodass einer der folgenden Werte verwendet wird: **key**, **tenant/spnId/spnCredential/spnCredentialType** oder **miServiceUri/miServiceToken**.

## <a name="error-code-df-delta-invalidconfiguration"></a>Fehlercode: DF-DELTA-InvalidConfiguration
- **Meldung:** Zeitstempel und Version können nicht gleichzeitig festgelegt werden.
- **Meldung**: Zeitstempel und Version können nicht gleichzeitig festgelegt werden.
- **Empfehlung**: Legen Sie in den Deltaeinstellungen einen Zeitstempel oder eine Version fest.

## <a name="error-code-df-delta-keycolumnmissed"></a>Fehlercode: DF-DELTA-KeyColumnMissed
- **Meldung:** Für nicht einfügbare Vorgänge müssen Schlüsselspalten angegeben werden.
- **Ursache**: Schlüsselspalten für nicht einfügbare Vorgänge fehlen.
- **Empfehlung**: Geben Sie Schlüsselspalten für die Deltasenke an, um nicht einfügbare Vorgänge zu verwenden.

## <a name="error-code-df-delta-invalidtableoperationsettings"></a>Fehlercode: DF-DELTA-InvalidTableOperationSettings
- **Meldung:** Die Optionen zum Neuerstellen und Entfernen können nicht gleichzeitig angegeben werden.
- **Ursache**: Die Optionen zum erneuten Erstellen und Abschneiden können nicht gleichzeitig angegeben werden.
- **Empfehlung**: Aktualisieren Sie die Deltaeinstellungen, um entweder einen Vorgang zum erneuten Erstellen oder zum Abschneiden zu verwenden.

## <a name="error-code-df-excel-worksheetconfigmissed"></a>Fehlercode: DF-Excel-WorksheetConfigMissed
- **Meldung:** Der Name oder Index der Excel-Tabelle ist erforderlich.
- **Ursache**: Es wurde eine ungültige Excel-Arbeitsblattkonfiguration angegeben.
- **Empfehlung**: Überprüfen Sie den Parameterwert, und geben Sie den Namen oder Index der Tabelle zum Lesen der Excel-Daten an.

## <a name="error-code-df-excel-invalidworksheetconfiguration"></a>Fehlercode: DF-Excel-InvalidWorksheetConfiguration
- **Meldung:** Der Name und der Index der Excel-Tabelle können nicht gleichzeitig vorhanden sein.
- **Ursache**: Der Name und der Index des Excel-Blatts wurden gleichzeitig angegeben.
- **Empfehlung**: Überprüfen Sie den Parameterwert, und geben Sie den Namen oder Index der Tabelle zum Lesen der Excel-Daten an.

## <a name="error-code-df-excel-invalidrange"></a>Fehlercode: DF-Excel-InvalidRange
- **Meldung:** Es wurde ein ungültiger Bereich angegeben.
- **Ursache**: Es wurde ein ungültiger Bereich angegeben.
- **Empfehlung:** Überprüfen Sie den Parameterwert, und geben Sie den gültigen Bereich mit dem folgenden Verweis an: [Excel-Format in den Azure Data Factory-Dataseteigenschaften](./format-excel.md#dataset-properties).

## <a name="error-code-df-excel-worksheetnotexist"></a>Fehlercode: DF-Excel-WorksheetNotExist
- **Meldung:** Das Excel-Arbeitsblatt ist nicht vorhanden.
- **Ursache**: Es wurde ein ungültiger Arbeitsblattname oder -index angegeben.
- **Empfehlung**: Überprüfen Sie den Parameterwert, und geben Sie den gültigen Arbeitsblattnamen oder -index zum Lesen der Excel-Daten an.

## <a name="error-code-df-excel-differentschemanotsupport"></a>Fehlercode: DF-Excel-DifferentSchemaNotSupport
- **Meldung:** Das Lesen von Excel-Dateien mit einem anderen Schema wird derzeit nicht unterstützt.
- **Ursache**: Das Lesen von Excel-Dateien mit einem anderen Schema wird derzeit nicht unterstützt.
- **Empfehlung**: Wenden Sie eine der folgenden Optionen an, um dieses Problem zu lösen:
    - Verwenden Sie die Aktivität **ForEach** + **Datenfluss** um Excel-Arbeitsblätter einzeln zu lesen. 
    - Aktualisieren Sie vor dem Lesen der Daten jedes Arbeitsblattschema manuell, sodass es dieselben Spalten enthält.

## <a name="error-code-df-excel-invaliddatatype"></a>Fehlercode: DF-Excel-InvalidDataType
- **Meldung:** Der Datentyp wird nicht unterstützt.
- **Ursache**: Der Datentyp wird nicht unterstützt.
- **Empfehlung**: Ändern Sie den Datentyp für zugehörige Eingabedatenspalten in **string**.

## <a name="error-code-df-excel-invalidfile"></a>Fehlercode: DF-Excel-InvalidFile
- **Meldung:** Es wurde eine ungültige Excel-Datei angegeben. Es werden nur XLSX- und XLS-Dateien unterstützt.
- **Ursache**: Es wurden ungültige Excel-Dateien angegeben.
- **Empfehlung**: Verwenden Sie den Platzhalter zum Filtern, und rufen Sie vor dem Lesen von Daten `.xls`- und `.xlsx`-Excel-Dateien ab.

## <a name="error-code-df-executor-outofmemorysparkbroadcasterror"></a>Fehlercode: DF-Executor-OutOfMemorySparkBroadcastError
- **Meldung**: Das mithilfe der Option „left“ oder „right“ explizit übertragene Dataset muss klein genug sein, um in den Arbeitsspeicher des Knotens aufgenommen zu werden. Sie können die Broadcastoption in einer Transformation vom Typ „join“, „exists“ oder „lookup“ auf „Off“ festlegen, um dieses Problem zu vermeiden, oder eine Integration Runtime mit mehr Arbeitsspeicher verwenden.
- **Ursache**: Die übertragene Tabelle überschreitet die Größenbeschränkung des Knotenarbeitsspeichers bei weitem.
- **Empfehlung**: Die Übertragungsoption „left“ oder „right“ sollte nur für kleinere Datasets verwendet werden, die in den Arbeitsspeicher des Knotens aufgenommen werden können. Konfigurieren Sie entweder eine geeignete Knotengröße, oder deaktivieren Sie die Übertragungsoption.

## <a name="error-code-df-mssql-invalidfirewallsetting"></a>Fehlercode: DF-MSSQL-InvalidFirewallSetting
- **Meldung**: Es konnte keine TCP/IP-Verbindung mit dem Host hergestellt werden. Stellen Sie sicher, dass eine SQL Server-Instanz auf dem Host ausgeführt wird, die TCP/IP-Verbindungen am Port annimmt. Stellen Sie sicher, dass TCP-Verbindungen mit dem Port nicht durch eine Firewall blockiert werden.
- **Ursache**: Die Firewalleinstellungen der SQL-Datenbank blockieren den Zugriff durch den Datenfluss.
- **Empfehlung**: Überprüfen Sie die Firewalleinstellungen für Ihre SQL-Datenbank, und gestatten Sie Azure-Diensten und -Ressourcen den Zugriff auf diesen Server.

## <a name="error-code-df-executor-acquirestoragememoryfailed"></a>Fehlercode: DF-Executor-AcquireStorageMemoryFailed
- **Meldung**: Fehler beim Übertragen von Speicher des Typs „Unroll“ in den Arbeitsspeicher. Für den Cluster stand während der Ausführung nicht genügend Arbeitsspeicher zur Verfügung. Wiederholen Sie den Vorgang unter Verwendung einer Integration Runtime mit mehr Kernen und/oder dem Computetyp „Arbeitsspeicheroptimiert“.
- **Ursache**: Der Cluster umfasst nicht genügend Arbeitsspeicher.
- **Empfehlung**: Verwenden Sie eine Integration Runtime mit mehr Kernen und/oder dem Computetyp „Arbeitsspeicheroptimiert“.

## <a name="error-code-df-cosmos-deletedatafailed"></a>Fehlercode: DF-Cosmos-DeleteDataFailed
- **Meldung**: Fehler beim Löschen von Daten aus Cosmos nach 3 Wiederholungsversuchen.
- **Ursache**: Der Durchsatz für die Cosmos-Sammlung ist niedrig und führt zu einer Drosselung, oder die Zeilendaten sind nicht in Cosmos vorhanden.
- **Empfehlung**: Führen Sie die folgenden Aktionen aus, um dieses Problem zu beheben:
    - Wenn ein 404-Fehler vorliegt, stellen Sie sicher, dass die zugehörigen Zeilendaten in der Cosmos-Sammlung vorhanden sind. 
    - Wenn der Fehler durch eine Drosselung verursacht wird, erhöhen Sie den Durchsatz für die Cosmos-Sammlung, oder legen Sie die automatische Skalierung fest.
    - Wenn bei der Anforderung ein Time out auftritt, legen Sie „Batch size“ in der Cosmos-Senke auf einen kleineren Wert fest, z. B. 1000.

## <a name="error-code-df-sqldw-errorrowsfound"></a>Fehlercode: DF-SQLDW-ErrorRowsFound
- **Ursache**: Beim Schreiben in die Azure Synapse-Senke wurden Fehler festgestellt / ungültige Zeilen gefunden.
- **Empfehlung**: Suchen Sie am Speicherort für abgelehnte Daten (falls konfiguriert) nach den Fehlerzeilen.

## <a name="error-code-df-sqldw-exporterrorrowfailed"></a>Fehlercode: DF-SQLDW-ExportErrorRowFailed
- **Meldung**: Ausnahme beim Schreiben von Fehlerzeilen in den Speicher.
- **Ursache**: Beim Schreiben von Fehlerzeilen in den Speicher ist eine Ausnahme aufgetreten.
- **Empfehlung**: Überprüfen Sie die Konfiguration des abgelehnten verknüpften Datendiensts.

## <a name="error-code-df-executor-fieldnotexist"></a>Fehlercode: DF-Executor-FieldNotExist
- **Meldung**: Das Feld in der Struktur ist nicht vorhanden.
- **Ursache**: In Ausdrücken wurde ungültige oder nicht verfügbare Feldnamen verwendet.
- **Empfehlung**: Überprüfen Sie die in Ausdrücken verwendeten Feldnamen.

## <a name="error-code-df-xml-invalidelement"></a>Fehlercode: DF-Xml-InvalidElement
- **Meldung**: Das XML-Element enthält Unterelemente oder Attribute, die nicht konvertiert werden können.
- **Ursache**: Das XML-Element enthält Unterelemente oder Attribute, die nicht konvertiert werden können.
- **Empfehlung**: Aktualisieren Sie die XML-Datei, sodass das XML-Element die richtigen Unterelemente oder Attribute aufweist.

## <a name="error-code-df-gen2-invalidcloudtype"></a>Fehlercode: DF-GEN2-InvalidCloudType
- **Meldung:** Cloudtyp ist ungültig.
- **Ursache**: Es wurde ein ungültiger Cloudtyp angegeben.
- **Empfehlung**: Überprüfen Sie den Cloudtyp in Ihrem verknüpften ADLS Gen2-Dienst.

## <a name="error-code-df-blob-invalidcloudtype"></a>Fehlercode: DF-Blob-InvalidCloudType
- **Meldung:** Cloudtyp ist ungültig.
- **Ursache**: Es wurde ein ungültiger Cloudtyp angegeben.
- **Empfehlung**: Überprüfen Sie den Cloudtyp im zugehörigen verknüpften Azure-Blobdienst.

## <a name="error-code-df-cosmos-failtoresetthroughput"></a>Fehlercode: DF-Cosmos-FailToResetThroughput
- **Meldung**: Der Vorgang zur Cosmos DB-Durchsatzskalierung kann nicht ausgeführt werden, weil zurzeit ein anderer Skalierungsvorgang ausgeführt wird. Versuchen Sie es später noch mal.
- **Ursache**: Der Vorgang zur Cosmos DB-Durchsatzskalierung kann nicht ausgeführt werden, weil zurzeit ein anderer Skalierungsvorgang ausgeführt wird.
- **Empfehlung**: Melden Sie sich bei Ihrem Cosmos-Konto an, und legen Sie manuell eine automatische Skalierung für den Containerdurchsatz fest. Alternativ können Sie nach Datenflüssen benutzerdefinierte Aktivitäten hinzufügen, um den Durchsatz zurückzusetzen.

## <a name="error-code-df-executor-invalidpath"></a>Fehlercode: DF-Executor-InvalidPath
- **Meldung**: Der Pfad wird nicht in Dateien aufgelöst. Stellen Sie sicher, dass die Datei bzw. der Ordner vorhanden und nicht ausgeblendet ist.
- **Ursache**: Es wurde ein ungültiger Datei-/Ordnerpfad angegeben, der nicht gefunden wurde oder auf den nicht zugegriffen werden kann.
- **Empfehlung**: Überprüfen Sie den Datei-/Ordnerpfad, und stellen Sie sicher, dass er vorhanden und in Ihrem Speicher zugänglich ist.

## <a name="error-code-df-executor-invalidpartitionfilenames"></a>Fehlercode: DF-Executor-InvalidPartitionFileNames
- **Meldung**: Dateinamen dürfen keine leeren Werte enthalten, wenn die Option für Dateinamen pro Partition festgelegt ist.
- **Ursache**: Es werden ungültige Partitionsdateinamen angegeben.
- **Empfehlung**: Überprüfen Sie Ihre Senkeneinstellungen, um den richtigen Wert für Dateinamen zu verwenden.

## <a name="error-code-df-executor-invalidoutputcolumns"></a>Fehlercode: DF-Executor-InvalidOutputColumns
- **Meldung**: Das Ergebnis enthält 0 Ausgabespalten. Stellen Sie sicher, dass mindestens eine Spalte zugeordnet ist.
- **Ursache**: Es ist keine Spalte zugeordnet.
- **Empfehlung**: Überprüfen Sie das Senkenschema, um sicherzustellen, dass mindestens eine Spalte zugeordnet ist.

## <a name="error-code-df-executor-invalidinputcolumns"></a>Fehlercode: DF-Executor-InvalidInputColumns
- **Meldung**: Die Spalte in der Quellkonfiguration wurde im Schema der Quelldaten nicht gefunden.
- **Ursache**: Für die Quelle wurden ungültige Spalten angegeben.
- **Empfehlung**: Überprüfen Sie die Spalten in der Quellkonfiguration, und stellen Sie sicher, dass es sich um eine Teilmenge des Quelldatenschemas handelt.

## <a name="error-code-df-adobeintegration-invalidmaptofilter"></a>Fehlercode: DF-AdobeIntegration-InvalidMapToFilter
- **Meldung:** In der benutzerdefinierten Ressource kann nur ein Schlüssel oder eine ID dem Filter zugeordnet werden.
- **Ursache**: Es wurden ungültige Konfigurationen angegeben.
- **Empfehlung**: Vergewissern Sie sich, dass in den AdobeIntegration-Einstellungen für die benutzerdefinierte Ressource nur ein Schlüssel bzw. eine ID für die Filterung zugeordnet ist.

## <a name="error-code-df-adobeintegration-invalidpartitionconfiguration"></a>Fehlercode: DF-AdobeIntegration-InvalidPartitionConfiguration
- **Meldung:** Es wird nur eine einzelne Partition unterstützt. Das Partitionsschema kann ein RoundRobin- oder Hash-Schema sein.
- **Ursache**: Es wurden ungültige Partitionskonfigurationen angegeben.
- **Empfehlung**: Vergewissern Sie sich, dass in den AdobeIntegration-Einstellungen nur die einzelne Partition festgelegt ist. Das Partitionsschema kann „RoundRobin“ oder „Hash“ lauten.

## <a name="error-code-df-adobeintegration-keycolumnmissed"></a>Fehlercode: DF-AdobeIntegration-KeyColumnMissed
- **Meldung:** Für nicht einfügbare Vorgänge muss der Schlüssel angegeben werden.
- **Ursache**: Schlüsselspalten fehlen.
- **Empfehlung**: Aktualisieren Sie die AdobeIntegration-Einstellungen, um sicherzustellen, dass Schlüsselspalten für nicht einfügbare Vorgänge angegeben sind.

## <a name="error-code-df-adobeintegration-invalidpartitiontype"></a>Fehlercode: DF-AdobeIntegration-InvalidPartitionType
- **Meldung:** Als Partitionstyp muss „roundRobin“ festgelegt werden.
- **Ursache**: Es wurden ungültige Partitionstypen angegeben.
- **Empfehlung**: Aktualisieren Sie die AdobeIntegration-Einstellungen, sodass der Partitionstyp „RoundRobin“ lautet.

## <a name="error-code-df-adobeintegration-invalidprivacyregulation"></a>Fehlercode: DF-AdobeIntegration-InvalidPrivacyRegulation
- **Meldung**: Derzeit wird nur „GDPR“ für die Datenschutz-Grundverordnung (DSGVO) als Datenschutzbestimmung unterstützt.
- **Ursache**: Es wurden ungültige Datenschutzkonfigurationen angegeben.
- **Empfehlung**: Aktualisieren Sie die AdobeIntegration-Einstellungen. Es wird nur die Datenschutzoption „GDPR“ unterstützt.

## <a name="error-code-df-executor-remoterpcclientdisassociated"></a>Fehlercode: DF-Executor-RemoteRPCClientDisassociated
- **Meldung**: Zuordnung des Remote-RPC-Clients wurde aufgehoben. Mögliche Ursachen sind die Überschreitung von Schwellenwerten durch Container oder Netzwerkprobleme.
- **Ursache**: Fehler bei der Datenflussaktivität aufgrund des vorübergehenden Netzwerkproblems oder weil für einen Knoten im Spark-Cluster nicht genügend Arbeitsspeicher verfügbar ist.
- **Empfehlung**: Verwenden Sie die folgenden Optionen, um dieses Problem zu lösen:
  - Option-1: Verwenden Sie einen leistungsstarken Cluster (sowohl Laufwerks- als auch Executor-Knoten verfügen über genügend Arbeitsspeicher für die Verarbeitung von Big Data), um Datenflusspipelines mit der Einstellung „Computetyp“ auf „arbeitsspeicheroptimiert“ ausführen zu können. Die Einstellungen sind in der folgenden Abbildung dargestellt.
        
      :::image type="content" source="media/data-flow-troubleshoot-guide/configure-compute-type.png" alt-text="Screenshot: Konfiguration des Compute-Typs":::   

  - Option 2: Verwenden Sie eine größere Clustergröße (z. B. 48 Kerne), um Ihre Datenflusspipelines ausführen. Weitere Informationen zur Clustergröße finden Sie in diesem Dokument: [Clustergröße](./concepts-integration-runtime-performance.md#cluster-size).
  
  - Option-3: Neupartitionierung Ihrer Eingabedaten. Bei Aufgaben, die im Datenfluss-Spark-Cluster ausgeführt werden, stellt eine Aufgabe eine Partition dar, die auf einem Knoten ausgeführt wird. Wenn die Daten in einer Partition zu groß sind, verbraucht die zugehörige Aufgabe, die auf dem Knoten ausgeführt wird, mehr Arbeitsspeicher als der Knoten selbst, wodurch ein Fehler auftritt. Sie können die Neupartitionierung verwenden, um Datenschiefe zu vermeiden und sicherzustellen, dass die Datengröße in jeder Partition durchschnittlich groß ist, während der Arbeitsspeicherverbrauch nicht zu hoch ist.
    
      :::image type="content" source="media/data-flow-troubleshoot-guide/configure-partition.png" alt-text="Screenshot: Konfiguration der Partitionen":::

    > [!NOTE]
    >  Sie müssen die Datengröße oder die Partitionsnummer der Eingabedaten auswerten und dann unter „Optimieren“ eine passende Partitionsnummer festlegen. Beispielsweise hat der Cluster, den Sie in der Datenflusspipelineausführung verwenden, 8 Kerne und der Arbeitsspeicher jedes Kerns hat 20 GB; die Eingabedaten haben allerdings 1.000 GB bei 10 Partitionen. Wenn Sie den Datenfluss direkt ausführen, wird das OOM-Problem auftreten (1.000 GB/10 > 20 GB). Deshalb ist es besser, die neue Partitionsnummer auf 100 festzulegen (1000GB/100 < 20GB).
    
  - Option-4: Anpassen und Optimieren von Quell-/Senken-/Transformationseinstellungen. Versuchen Sie z. B. alle Dateien in einem Container zu kopieren und verwenden Sie nicht das Platzhaltermuster. Ausführlichere Informationen finden Sie unter [Leistungs- und Optimierungshandbuch für Zuordnungsdatenflüsse)](./concepts-data-flow-performance.md).

## <a name="error-code-df-mssql-errorrowsfound"></a>Fehlercode: DF-MSSQL-ErrorRowsFound
- **Ursache**: Beim Schreiben in der Azure SQL-Datenbanksenke wurden Fehler/Ungültige Zeilen gefunden.
- **Empfehlung**: Suchen Sie am Speicherort für abgelehnte Daten (falls konfiguriert) nach den Fehlerzeilen.

## <a name="error-code-df-mssql-exporterrorrowfailed"></a>Fehlercode: DF-MSSQL-ExportErrorRowFailed
- **Meldung**: Ausnahme beim Schreiben von Fehlerzeilen in den Speicher.
- **Ursache**: Beim Schreiben von Fehlerzeilen in den Speicher ist eine Ausnahme aufgetreten.
- **Empfehlung**: Überprüfen Sie die Konfiguration des abgelehnten verknüpften Datendiensts.

## <a name="error-code-df-synapse-invaliddatabasetype"></a>Fehlercode: DF-Synapse-InvalidDatabaseType
- **Meldung**: Datenbanktyp wird nicht unterstützt.
- **Ursache**: Dieser Datenbanktyp wird nicht unterstützt.
- **Empfehlung**: Überprüfen Sie den Datenbanktyp und ändern Sie ihn in den richtigen.

## <a name="error-code-df-synapse-invalidformat"></a>Fehlercode: DF-Synapse-InvalidFormat
- **Meldung**: Format wird nicht unterstützt.
- **Ursache**: Dieses Format wird nicht unterstützt. 
- **Empfehlung**: Überprüfen Sie das Format und ändern Sie es in das richtige.

## <a name="error-code-df-synapse-invalidtabledbname"></a>Fehlercode: DF-Synapse-InvalidTableDBName
- **Ursache**: Der Name der Tabelle/Datenbank ist ungültig.
- **Empfehlung**: Geben Sie einen gültigen Namen für die Tabelle/Datenbank ein. Gültige Namen enthalten nur alphabetische Zeichen, Zahlen und `_`.

## <a name="error-code-df-synapse-invalidoperation"></a>Fehlercode: DF-Synapse-InvalidOperation
- **Ursache**: Der Vorgang wird nicht unterstützt.
- **Empfehlung**: Nutzen Sie einen gültigen Vorgang.

## <a name="error-code-df-synapse-dbnotexist"></a>Fehlercode: DF-Synapse-DBNotExist
- **Ursache**: Die Datenbank ist nicht vorhanden.
- **Empfehlung**: Überprüfen Sie, ob die Datenbank vorhanden ist.

## <a name="error-code-df-synapse-storedprocedurenotsupported"></a>Fehlercode: DF-Synapse-StoredProcedureNotSupported
- **Meldung**: Verwenden Sie „Gespeicherte Prozedur“ als Quelle wird für serverlosen (bedarfsbasierten) Pool nicht unterstützt.
- **Ursache**: Der serverlose Pool hat Beschränkungen.
- **Empfehlung**: Versuchen Sie erneut, „Abfrage“ als Quelle zu verwenden oder die gespeicherte Prozedur als Ansicht zu speichern, und verwenden Sie dann „Tabelle“ als Quelle, um direkt aus der Ansicht zu lesen.

## <a name="error-code-df-executor-broadcastfailure"></a>Fehlercode: DF-Executor-BroadcastFailure
- **Meldung**: Fehler bei der Ausführung des Datenflusses während des Übertragungsaustauschs. Mögliche Ursachen sind falsch konfigurierte Quellen-Verbindungen oder ein Broadcastjoin-Timeoutfehler. Um sicherzustellen, dass die Quellen ordnungsgemäß konfiguriert sind, testen Sie die Verbindung oder führen Sie eine Quelldatenvorschau in einer Dataflow-Debugsitzung aus. Um das Broadcastjoin-Timeout zu vermeiden, können Sie die Broadcastoption „Aus“ in den Join/Exists/Lookup-Transformationen auswählen. Wenn Sie die Broadcastoption nutzen möchten, um die Leistung zu verbessern, müssen Sie sicherstellen, dass die Broadcast-Datenströme innerhalb von 60 Sekunden Daten für Debugausführungen und innerhalb von 300 Sekunden Daten für Auftragsausführungen erzeugen können. Falls das Problem weiterhin besteht, wenden Sie sich an Kundensupport.

- **Ursache**:  
    1. Der Fehler bei der Quellverbindung/Konfiguration kann zu einem Übertragungsfehler bei Join/Exists/Lookup-Transformationen führen.
    2. Für Übertragungen gilt bei Debugausführungen ein Standardtimeout von 60 Sekunden und bei Auftragsausführungen ein Standardtimeout von 300 Sekunden. Der bei einem Broadcastjoin für die Übertragung ausgewählte Datenstrom ist anscheinend zu groß, um innerhalb dieser Zeitspanne Daten zu erzeugen. Wenn kein Broadcastjoin verwendet wird, kann die vom Datenfluss durchgeführte Standardübertragung denselben Grenzwert erreichen.

- **Empfehlung**:
    - Verwenden Sie für die Quellen eine Datenvorschau, um zu bestätigen, dass die Quellen gut konfiguriert sind. 
    - Deaktivieren Sie die Übertragungsoption oder vermeiden Sie die Übertragung von großen Datenströmen, deren Verarbeitung länger als 60 Sekunden dauern kann. Wählen Sie stattdessen einen kleineren Datenstrom für die Übertragung. 
    - Große SQL-/Data Warehouse-Tabellen und Quelldateien sind hierfür eher ungeeignet. 
    - Sofern kein Broadcastjoin vorhanden ist, verwenden Sie einen größeren Cluster, wenn dieser Fehler auftritt. 
    - Falls das Problem weiterhin besteht, wenden Sie sich an den Kundensupport.

## <a name="error-code-df-cosmos-shorttypenotsupport"></a>Fehlercode: DF-Cosmos-ShortTypeNotSupport
- **Meldung**: Short-Datentypen werden in der Cosmos DB nicht unterstützt.
- **Ursache**: Der Short-Datentyp wird in der Cosmos DB nicht unterstützt.
- **Empfehlung**: Fügen Sie eine abgeleitete Transformation hinzu, um verknüpfte Spalten von Short in Integer zu konvertieren, bevor sie in der Cosmos-Senke verwendet werden.

## <a name="error-code-df-blob-functionnotsupport"></a>Fehlercode: DF-Blob-FunctionNotSupport
- **Meldung**: Dieser Endpunkt unterstützt BlobStorageEvents, SoftDelete oder AutomaticSnapshot nicht. Deaktivieren Sie diese Kontofeatures, wenn Sie diesen Endpunkt verwenden möchten.
- **Ursache**: Azure Blob Storage-Ereignisse, das vorläufige Löschen oder die automatische Momentaufnahme werden in Datenflüssen nicht unterstützt, wenn der verknüpfte Azure Blob Storage-Dienst mit Dienstprinzipal- oder verwalteter Identitätsauthentifizierung erstellt wird.
- **Empfehlung**: Deaktivieren Sie Azure Blob Storage-Ereignisse, das Feature für das vorläufige Löschen oder die automatische Momentaufnahme im Azure Blob-Konto oder verwenden Sie die Schlüsselauthentifizierung, um den verknüpften Dienst zu erstellen.

## <a name="error-code-df-cosmos-invalidaccountkey"></a>Fehlercode: DF-Cosmos-InvalidAccountKey
- **Meldung**: Das Eingabeautorisierungstoken kann die Anforderung nicht verarbeiten. Please check that the expected payload is built as per the protocol, and check the key being used. (Das Eingabeautorisierungstoken kann die Anforderung nicht verarbeiten. Vergewissern Sie sich, dass die erwartete Nutzlast protokollgemäß erstellt wurde, und überprüfen Sie den verwendeten Schlüssel.)
- **Ursache**: Es gibt nicht genügend Berechtigungen zum Lesen/Schreiben von Azure Cosmos DB-Daten.
- **Empfehlung**: Verwenden Sie den Lese-/Schreibschlüssel für den Zugriff auf Azure Cosmos DB.

## <a name="next-steps"></a>Nächste Schritte

Weitere Hilfe zur Problembehandlung finden Sie in diesen Ressourcen:

- [Anleitung zur Problembehandlung bei Zuordnungsdatenflüssen](data-flow-troubleshoot-guide.md)
- [Data Factory-Blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Data Factory-Funktionsanfragen](/answers/topics/azure-data-factory.html)
- [Azure-Videos](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Stack Overflow-Forum für Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Twitter-Informationen über Data Factory](https://twitter.com/hashtag/DataFactory)