---
title: Erstellen umfangreicher Datenkopierpipelines mit dem metadatengesteuerten Ansatz im Tool zum Kopieren von Daten
description: Dieser Artikel enthält Informationen zum metadatengesteuerten Ansatz im ADF-Tool zum Kopieren von Daten.
author: dearandyxu
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/19/2021
ms.author: yexu
ms.openlocfilehash: e2263db67214fb6fea91c8a8cefa65a981475ec3
ms.sourcegitcommit: deb5717df5a3c952115e452f206052737366df46
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122681597"
---
# <a name="build-large-scale-data-copy-pipelines-with-metadata-driven-approach-in-copy-data-tool-preview"></a>Erstellen umfangreicher Datenkopierpipelines mit dem metadatengesteuerten Ansatz im Tool zum Kopieren von Daten (Vorschau)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Wenn Sie große Mengen von Objekten kopieren möchten (z. B. Tausende von Tabellen) oder Daten aus einer Vielzahl von Quellen laden möchten, ist es am besten, die Namensliste der Objekte mit dem erforderlichen Kopierverhalten in eine Steuertabelle einzugeben und dann parametrisierte Pipelines zu verwenden, um diese aus der Steuertabelle zu lesen und entsprechend auf die Aufträge anzuwenden.  Auf diese Weise können Sie die zu kopierende Objektliste problemlos verwalten (z. B. hinzufügen/entfernen), indem Sie einfach die Objektnamen in der Steuertabelle aktualisieren, anstatt die Pipelines erneut bereitzustellen. Darüber hinaus können Sie an einem zentralen Ort ganz einfach überprüfen, welche Objekte von welchen Pipelines/Triggern mit definiertem Kopierverhalten kopiert werden. 

Das Tool zum Kopieren von Daten in ADF erleichtert das Erstellen solcher metadatengesteuerten Datenkopierpipelines. Nachdem Sie einen intuitiven Ablauf auf einer assistentenbasierten Benutzeroberfläche durchlaufen haben, kann das Tool parametrisierte Pipelines und SQL-Skripts generieren, mit denen Sie entsprechend externe Steuertabellen erstellen können. Nachdem Sie die generierten Skripte zum Erstellen der Steuertabelle in Ihrer SQL-Datenbank ausgeführt haben, lesen Ihre Pipelines die Metadaten aus der Steuertabelle und wenden sie automatisch auf die Kopieraufträge an.

## <a name="create-metadata-driven-copy-jobs-from-copy-data-tool"></a>Erstellen metadatengesteuerter Kopieraufträge mit dem Tool zum Kopieren von Daten

1. Wählen Sie im Tool zum Kopieren von Daten die Option **Metadata-driven copy task** (Metadatengesteuerte Kopieraufgabe) aus.

   Sie müssen die Verbindung und den Tabellennamen Ihrer Steuertabelle eingeben, damit die generierte Pipeline Metadaten daraus liest.

   ![Auswählen des Aufgabentyps](./media/copy-data-tool-metadata-driven/select-task-type.png)

2. Geben Sie die **Verbindung Ihrer Quelldatenbank** ein. Sie können auch den [parametrisierten verknüpften Dienst](parameterize-linked-services.md) verwenden.

   ![Auswählen des parametrisierten verknüpften Diensts](./media/copy-data-tool-metadata-driven/select-parameterized-linked-service.png)

3. Wählen Sie den zu kopierenden **Tabellennamen** aus.

   ![Tabelle auswählen](./media/copy-data-tool-metadata-driven/select-table.png)

   > [!NOTE]
   > Wenn Sie tabellarischen Datenspeicher auswählen, können Sie auf der nächsten Seite entweder vollständiges Laden oder inkrementelles Laden auswählen. Wenn Sie Speicher auswählen, können Sie auf der nächsten Seite nur das vollständige Laden auswählen. Das inkrementelle Laden ausschließlich neuer Dateien aus dem Speicher wird derzeit nicht unterstützt.  

4. Wählen Sie das **Ladeverhalten** aus.
   >[!TIP]
   >Wenn Sie einen vollständigen Kopiervorgang für alle Tabellen durchführen möchten, wählen Sie **Full load all tables** (Alle Tabellen vollständig laden) aus. Wenn Sie einen inkrementellen Kopiervorgang durchführen möchten, können Sie **configure for each table individually** (für jede Tabelle einzeln konfigurieren) und **Delta load** (Deltaladevorgang) sowie den Namen und anfänglichen Wert der Grenzwertspalte für jede Tabelle auswählen. 

5. Wählen Sie **Zieldatenspeicher** aus. 

6. Auf der Seite **Einstellungen** können Sie mit der Option **Number of concurrent copy tasks** (Anzahl gleichzeitiger Kopieraufgaben) die maximale Anzahl von Kopieraktivitäten zum gleichzeitigen Kopieren von Daten aus Ihrem Quellspeicher festlegen. Der Standardwert lautet 20. 

   ![Seite "Einstellungen"](./media/copy-data-tool-metadata-driven/settings.png)

7. Nach der Pipelinebereitstellung können Sie die SQL-Skripts von der Benutzeroberfläche kopieren oder herunterladen, um eine Steuertabelle und eine gespeicherte Prozedur zu erstellen. 

   ![Herunterladen von Skripts](./media/copy-data-tool-metadata-driven/download-scripts.png)

   Es werden zwei SQL-Skripts angezeigt.
   
    - Mit dem erste SQL-Skript werden zwei Steuertabellen erstellt. In der Hauptsteuertabelle wird die Tabellenliste, der Dateipfad oder das Kopierverhalten gespeichert. In der Verbindungssteuertabelle wird der Verbindungswert Ihres Datenspeichers gespeichert, wenn Sie den parametrisierten verknüpften Dienst verwendet haben.  
    - Mit dem zweiten SQL-Skript wird eine gespeicherte Prozedur erstellt. Hiermit wird der Grenzwert in der Hauptsteuertabelle bei jedem Abschluss der inkrementellen Kopieraufträge aktualisiert. 

8. Öffnen Sie **SSMS**, um eine Verbindung mit dem Steuertabellenserver herzustellen, und führen Sie die beiden SQL-Skripts aus, um Steuertabellen und die gespeicherte Prozedur zu erstellen.

   ![Erstellen eines Steuertabellenskripts](./media/copy-data-tool-metadata-driven/create-control-table-script.png)

9. Fragen Sie die Hauptsteuertabelle und die Verbindungssteuertabelle ab, um die darin enthaltenen Metadaten zu überprüfen.

   **Hauptsteuertabelle**
   ![Abfragen von Steuertabellenskript 1](./media/copy-data-tool-metadata-driven/query-control-table.png)

   **Verbindungssteuertabelle**
   ![Abfragen von Steuertabellenskript 2](./media/copy-data-tool-metadata-driven/query-connection-control-table.png)

10. Kehren Sie zum ADF-Portal zurück, um Pipelines anzuzeigen und zu debuggen. Es wird ein Ordner mit dem Namen „MetadataDrivenCopyTask_### _######“ angezeigt. **Klicken** Sie auf die Pipeline mit dem Namen „MetadataDrivenCopyTask_###_TopLevel“, und klicken Sie dann auf **debug run** (Debugausführung). 

    Sie müssen die folgenden Parameter eingeben:
   
    | Parametername | BESCHREIBUNG | 
    |:--- |:--- |
    |MaxNumberOfConcurrentTasks |Sie können jederzeit die maximale Anzahl gleichzeitig ausgeführter Kopieraktivitäten vor der Pipelineausführung ändern. Der Standardwert ist der Wert, den Sie im Tool zum Kopieren von Daten eingegeben haben. |
    |MainControlTableName | Sie können jederzeit den Namen der Hauptsteuertabelle ändern, damit die Pipeline vor der Ausführung die Metadaten aus dieser Tabelle abruft.  |
    |ConnectionControlTableName |Sie können jederzeit den Namen der Verbindungssteuertabelle (optional) ändern, damit die Pipeline vor der Ausführung die Metadaten im Zusammenhang mit der Datenspeicherverbindung abruft. |
    |MaxNumberOfObjectsReturnedFromLookupActivity |Damit der Grenzwert für ausgegebene Lookup-Aktivität nicht erreicht wird, kann die maximale Anzahl der von der Lookup-Aktivität zurückgegebenen Objekte festgelegt werden. In den meisten Fällen muss der Standardwert nicht geändert werden.  |
    |windowStart |Wenn Sie einen dynamischen Wert (z. B. jjjj/mm/tt) als Ordnerpfad eingeben, wird der Parameter verwendet, um die aktuelle Triggerzeit zum Ausfüllen des dynamischen Ordnerpfads an die Pipeline zu übergeben. Wenn die Pipeline durch einen Zeitplantrigger oder einen Trigger für rollierende Fenster ausgelöst wird, müssen die Benutzer den Wert dieses Parameters nicht eingeben. Beispielwert: 2021-01-25T01:49:28Z | 
   

11. Aktivieren Sie den Trigger, um die Pipelines zu operationalisieren.

    ![Aktivieren des Triggers](./media/copy-data-tool-metadata-driven/enable-trigger.png)


## <a name="update-control-table-by-copy-data-tool"></a>Aktualisieren der Steuertabelle mithilfe des Tools zum Kopieren von Daten
Sie können die Steuertabelle jederzeit direkt aktualisieren, indem Sie das zu kopierende Objekt hinzufügen oder entfernen oder das Kopierverhalten für jede Tabelle ändern. Es wird auch eine Benutzeroberfläche im Tool zum Kopieren von Daten erstellt, um die Bearbeitung der Steuertabelle zu erleichtern.

1. Klicken Sie mit der rechten Maustaste auf die Pipeline der obersten Ebene, **MetadataDrivenCopyTask_xxx_TopLevel**, und wählen Sie dann **Edit control table** (Steuertabelle bearbeiten) aus.

   ![Bearbeiten der Steuertabelle 1](./media/copy-data-tool-metadata-driven/edit-control-table.png)

2. Wählen Sie Zeilen in der Steuertabelle aus, die Sie bearbeiten möchten.

   ![Bearbeiten der Steuertabelle 2](./media/copy-data-tool-metadata-driven/edit-control-table-select-tables.png)

3. Führen Sie das Tool zum Kopieren von Daten aus, und es wird ein neues SQL-Skript für Sie erstellt. Führen Sie das SQL-Skript erneut aus, um die Steuertabelle zu aktualisieren.

   ![Bearbeiten der Steuertabelle 3](./media/copy-data-tool-metadata-driven/edit-control-table-create-script.png)

   > [!NOTE]
   > Die Pipeline wird NICHT erneut bereitgestellt. Mit dem neu erstellten SQL-Skript können Sie nur die Steuertabelle aktualisieren. 

## <a name="control-tables"></a>Steuertabellen

### <a name="main-control-table"></a>Hauptsteuertabelle
Jede Zeile in der Steuertabelle enthält die Metadaten für ein zu kopierendes Objekt (z. B. eine Tabelle).

| Spaltenname | BESCHREIBUNG | 
|:--- |:--- |
| Id | Eindeutige ID des zu kopierenden Objekts. |
| SourceObjectSettings | Metadaten des Quelldatasets. Dies kann ein Schemaname, ein Tabellenname usw. sein. Hier finden Sie ein [Beispiel](connector-azure-sql-database.md#dataset-properties). |
| SourceConnectionSettingsName | Der Name der Quellverbindungseinstellung in der Verbindungssteuertabelle. Der Vorgang ist optional. |
| CopySourceSettings | Metadaten der Quelleigenschaft in der Kopieraktivität. Dabei kann es sich um eine Abfrage, Partitionen usw. handeln. Hier finden Sie ein [Beispiel](connector-azure-sql-database.md#azure-sql-database-as-the-source). |
| SinkObjectSettings | Metadaten des Zieldatasets. Dies kann ein Dateiname, ein Ordnerpfad,ein Tabellenname usw. sein. Hier finden Sie ein [Beispiel](connector-azure-data-lake-storage.md#azure-data-lake-storage-gen2-as-a-sink-type). Wenn ein dynamischer Ordnerpfad angegeben ist, wird der Variablenwert nicht hier in die Steuertabelle geschrieben. |
| SinkConnectionSettingsName | Der Name der Zielverbindungseinstellung in der Verbindungssteuertabelle. Der Vorgang ist optional. |
| CopySinkSettings | Metadaten der Senkeneigenschaft in der Kopieraktivität. Dabei kann es sich um „preCopyScript“, „tableOption“ usw. handeln. Hier finden Sie ein [Beispiel](connector-azure-sql-database.md#azure-sql-database-as-the-sink). |
| CopyActivitySettings | Metadaten der „translator“-Eigenschaft in der Kopieraktivität. Hiermit wird die Spaltenzuordnung definiert. |
| TopLevelPipelineName | Name der Pipeline der obersten Ebene, die dieses Objekt kopieren kann. |
| TriggerName | Triggername, der die Pipeline zum Kopieren dieses Objekts auslösen kann. Bei einer Debugausführung lautet der Name „Sandbox“. Bei einer manuellen Ausführung lautet der Name „Manuell“. Bei einer geplanten Ausführung entspricht der Name dem zugeordneten Triggernamen. Es können mehrere Namen eingegeben werden. |
| DataLoadingBehaviorSettings |Vollständiger Ladevorgang oder Deltaladevorgang. |
| TaskId | Die Reihenfolge der zu kopierenden Objekte entspricht der TaskId in der Steuertabelle (ORDER BY [TaskId] DESC). Wenn große Mengen von Objekten kopiert werden sollen, aber nur eine begrenzte Anzahl gleichzeitiger Kopiervorgänge zulässig ist, können Sie die TaskId für jedes Objekt ändern, um festzulegen, welche Objekte früher kopiert werden können. Der Standardwert ist 0. |
| CopyEnabled | Geben Sie an, ob das Element im Datenerfassungsprozess aktiviert ist. Zulässige Werte: 1 (aktiviert), 0 (deaktiviert). Der Standardwert ist 1. |

### <a name="connection-control-table"></a>Verbindungssteuertabelle
Jede Zeile in der Steuertabelle enthält eine Verbindungseinstellung für den Datenspeicher.

| Spaltenname | BESCHREIBUNG | 
|:--- |:--- |
| Name | Name der parametrisierten Verbindung in der Hauptsteuertabelle. |
| ConnectionSettings | Die Verbindungseinstellungen. Dabei kann es sich um den Datenbanknamen, Servernamen usw. handeln. |

## <a name="pipelines"></a>Pipelines
Das Tool zum Kopieren von Daten generiert drei Ebenen von Pipelines generiert.

### <a name="metadatadrivencopytask_xxx_toplevel"></a>MetadataDrivenCopyTask_xxx_TopLevel
Diese Pipeline berechnet die Gesamtzahl der Objekte (Tabellen usw.), die bei dieser Ausführung kopiert werden müssen, ermittelt die Anzahl sequenzieller Batches basierend auf der maximal zulässigen Anzahl gleichzeitiger Kopieraufgaben und führt dann eine weitere Pipeline aus, um verschiedene Batches sequenziell zu kopieren. 

#### <a name="parameters"></a>Parameter
| Parametername | BESCHREIBUNG | 
|:--- |:--- |
| MaxNumberOfConcurrentTasks | Sie können jederzeit die maximale Anzahl gleichzeitig ausgeführter Kopieraktivitäten vor der Pipelineausführung ändern. Der Standardwert ist der Wert, den Sie im Tool zum Kopieren von Daten eingegeben haben. |
| MainControlTableName | Der Tabellenname der Hauptsteuertabelle. Die Pipeline ruft vor der Ausführung die Metadaten aus dieser Tabelle ab. |
| ConnectionControlTableName | Der Tabellenname der Verbindungssteuertabelle (optional). Die Pipeline ruft vor der Ausführung die Metadaten im Zusammenhang mit der Datenspeicherverbindung ab. |
| MaxNumberOfObjectsReturnedFromLookupActivity | Damit der Grenzwert für ausgegebene Lookup-Aktivität nicht erreicht wird, kann die maximale Anzahl der von der Lookup-Aktivität zurückgegebenen Objekte festgelegt werden. In den meisten Fällen muss der Standardwert nicht geändert werden.  |
| windowStart | Wenn Sie einen dynamischen Wert (z. B. jjjj/mm/tt) als Ordnerpfad eingeben, wird der Parameter verwendet, um die aktuelle Triggerzeit zum Ausfüllen des dynamischen Ordnerpfads an die Pipeline zu übergeben. Wenn die Pipeline durch einen Zeitplantrigger oder einen Trigger für rollierende Fenster ausgelöst wird, müssen die Benutzer den Wert dieses Parameters nicht eingeben. Beispielwert: 2021-01-25T01:49:28Z |

#### <a name="activities"></a>Aktivitäten
| Name der Aktivität | Aktivitätstyp | BESCHREIBUNG |
|:--- |:--- |:--- |
| GetSumOfObjectsToCopy  | Nachschlagen | Berechnet die Gesamtzahl der Objekte (Tabellen usw.), die bei dieser Ausführung kopiert werden müssen. |
| CopyBatchesOfObjectsSequentially | ForEach | Ermittelt die Anzahl sequenzieller Batches basierend auf der maximal zulässigen Anzahl gleichzeitiger Kopieraufgaben und führt dann eine weitere Pipeline aus, um verschiedene Batches sequenziell zu kopieren.  |
| CopyObjectsInOneBtach | Pipeline ausführen | Führt eine weitere Pipeline aus, um einen Batch von Objekten zu kopieren. Die Objekte, die zu diesem Batch gehören, werden parallel kopiert. | 


### <a name="metadatadrivencopytask_xxx_-middlelevel"></a>MetadataDrivenCopyTask_xxx_ MiddleLevel
Diese Pipeline kopiert einen Batch von Objekten. Die Objekte, die zu diesem Batch gehören, werden parallel kopiert. 

#### <a name="parameters"></a>Parameter
| Parametername | BESCHREIBUNG | 
|:--- |:--- |
| MaxNumberOfObjectsReturnedFromLookupActivity | Damit der Grenzwert für ausgegebene Lookup-Aktivität nicht erreicht wird, kann die maximale Anzahl der von der Lookup-Aktivität zurückgegebenen Objekte festgelegt werden.  In den meisten Fällen muss der Standardwert nicht geändert werden.  | 
| TopLayerPipelineName | Der Name der Pipeline der obersten Ebene. | 
| TriggerName | Der Name des Triggers. | 
| CurrentSequentialNumberOfBatch | Die ID des sequenziellen Batches. | 
| SumOfObjectsToCopy | Die Gesamtzahl der zu kopierenden Objekte. | 
| SumOfObjectsToCopyForCurrentBatch | Die Anzahl der im aktuellen Batch zu kopierenden Objekte. | 
| MainControlTableName | Der Name der Hauptsteuertabelle. |
| ConnectionControlTableName | Der Name der Verbindungssteuertabelle. |

#### <a name="activities"></a>Aktivitäten
| Name der Aktivität | Aktivitätstyp | BESCHREIBUNG |
|:--- |:--- |:--- |
| DivideOneBatchIntoMultipleGroups | ForEach  | Unterteilt Objekte aus einem einzigen Batch in mehrere parallele Gruppen, um zu vermeiden, dass der Ausgabegrenzwert der Lookup-Aktivität erreicht wird. |
| GetObjectsPerGroupToCopy | Nachschlagen | Ruft Objekte (Tabellen usw.) aus der Steuertabelle ab, die in diese Gruppe kopiert werden müssen. Die Reihenfolge der zu kopierenden Objekte entspricht der TaskId in der Steuertabelle (ORDER BY [TaskId] DESC). |
| CopyObjectsInOneGroup | Pipeline ausführen | Führt eine weitere Pipeline aus, um Objekte aus einer Gruppe zu kopieren. Die Objekte, die zu dieser Gruppe gehören, werden parallel kopiert. |


### <a name="metadatadrivencopytask_xxx_-bottomlevel"></a>MetadataDrivenCopyTask_xxx_ BottomLevel
Diese Pipeline kopiert Objekte aus einer Gruppe. Die Objekte, die zu dieser Gruppe gehören, werden parallel kopiert.  

#### <a name="parameters"></a>Parameter
| Parametername | BESCHREIBUNG | 
|:--- |:--- |
| ObjectsPerGroupToCopy | Die Anzahl der in der aktuellen Gruppe zu kopierenden Objekte. | 
| ConnectionControlTableName | Der Name der Verbindungssteuertabelle. | 
| windowStart | Hiermit wird die aktuelle Triggerzeit zum Ausfüllen des dynamischen Ordnerpfads (sofern vom Benutzer konfiguriert) an die Pipeline übergeben. | 

#### <a name="activities"></a>Aktivitäten
| Name der Aktivität | Aktivitätstyp | BESCHREIBUNG |
|:--- |:--- |:--- |
| ListObjectsFromOneGroup | ForEach | Listet Objekte aus einer Gruppe auf und iteriert jedes dieser Objekte zu nachgelagerten Aktivitäten. |
| RouteJobsBasedOnLoadingBehavior | Schalter | Überprüft das Ladeverhalten für jedes Objekt. Im Fall des Standardwerts oder „FullLoad“ wird ein vollständiger Ladevorgang durchgeführt. Im Fall von „DeltaLoad“ wird ein inkrementeller Ladevorgang über eine Grenzwertspalte durchgeführt, um Änderungen zu erkennen. |
| FullLoadOneObject | Kopieren | Erstellt eine vollständige Momentaufnahme für dieses Objekt und kopiert sie in das Ziel. |
| DeltaLoadOneObject | Kopieren | Kopiert nur die geänderten Daten seit der letzten Ausführung, indem der Wert in der Grenzwertspalte verglichen wird, um Änderungen zu erkennen. |
| GetMaxWatermarkValue | Nachschlagen | Fragt das Quellobjekt ab, um den Höchstwert aus der Grenzwertspalte abzurufen. |
| UpdateWatermarkColumnValue | StoreProcedure | Schreibt den neuen Grenzwert in die Steuertabelle zurück, um ihn beim nächsten Mal zu verwenden. |

### <a name="known-limitations"></a>Bekannte Einschränkungen
- Das Tool zum Kopieren von Daten unterstützt derzeit keine metadatengesteuerte Erfassung zum inkrementellen Kopieren ausschließlich neuer Dateien. Dazu können Sie jedoch eigene parametrisierte Pipelines verwenden.
- IR-Name, Datenbanktyp und Dateiformattyp können in ADF nicht parametrisiert werden. Wenn Sie beispielsweise Daten sowohl von Oracle Server als auch von SQL Server erfassen möchten, benötigen Sie zwei verschiedene parametrisierte Pipelines. Die einzelne Steuertabelle kann jedoch von zwei Sätzen von Pipelines gemeinsam genutzt werden. 
- In generierten SQL-Skripts verwendet das Tool zum Kopieren von Daten OPENJSON. Wenn Sie SQL Server zum Hosten der Steuertabelle verwenden, muss es sich um SQL Server 2016 (13.x) oder höher handeln, damit die OPENJSON-Funktion unterstützt wird.


## <a name="next-steps"></a>Nächste Schritte
Machen Sie sich mit diesen Tutorials vertraut, die das Tool zum Kopieren von Daten verwenden:

- [Schnellstart: Erstellen einer Data Factory mithilfe des Tools zum Kopieren von Daten](quickstart-create-data-factory-copy-data-tool.md)
- [Tutorial: Kopieren von Daten in Azure mit dem Tool zum Kopieren von Daten](tutorial-copy-data-tool.md) 
- [Tutorial: Kopieren lokaler Daten nach Azure mit dem Tool zum Kopieren von Daten](tutorial-hybrid-copy-data-tool.md)
