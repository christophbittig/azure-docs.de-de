---
title: Weitere Informationen finden Sie unter Transformieren von Daten durch Ausführen eines Synapse-Notebooks
description: In diesem Artikel erfahren Sie, wie Sie eine Synapse-Notebookaktivität und eine Synapse-Pipeline erstellen und entwickeln.
services: synapse analytics
author: ruixinxu
ms.service: synapse-analytics
ms.subservice: pipeline
ms.topic: conceptual
ms.date: 05/19/2021
ms.author: ruxu
ms.reviewer: ''
ms.custom: devx-track-python
ms.openlocfilehash: 9c021b8b0aec025458179ffb4859c351572762ee
ms.sourcegitcommit: ef448159e4a9a95231b75a8203ca6734746cd861
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/30/2021
ms.locfileid: "123186981"
---
# <a name="transform-data-by-running-a-synapse-notebook"></a>Weitere Informationen finden Sie unter Transformieren von Daten durch Ausführen eines Synapse-Notebooks

[!INCLUDE[appliesto-adf-xxx-md](../data-factory/includes/appliesto-xxx-asa-md.md)]

Die [Azure Synapse-Notebookaktivität](../data-factory/concepts-pipelines-activities.md) in einer Synapse-Pipeline führt ein Synapse-Notebook in Ihrem Azure Synapse-Arbeitsbereich aus. Dieser Artikel baut auf dem Artikel zu [Datentransformationsaktivitäten](../data-factory/transform-data.md) auf, der eine allgemeine Übersicht über die Datentransformation und die unterstützten Transformationsaktivitäten bietet. 

## <a name="create-a-synapse-notebook-activity"></a>Erstellen einer Synapse-Notebookaktivität

Sie können eine Synapse-Notebookaktivität direkt über die Canvas der Synapse-Pipeline oder über den Notebook-Editor erstellen. Die Synapse-Notebookaktivität wird im Spark-Pool ausgeführt, der im Synapse-Notebook ausgewählt wird. 

### <a name="add-a-synapse-notebook-activity-from-pipeline-canvas"></a>Hinzufügen einer Synapse-Notebookaktivität über eine Pipelinecanvas

Ziehen Sie das **Synapse-Notebook** unter **Aktivitäten** in die Synapse-Pipelinecanvas. Wählen Sie das Feld für Synapse-Notebookaktivitäten aus, und konfigurieren Sie die **Einstellungen** der Notebookinhalte für die aktuelle Aktivität. Sie können ein vorhandenes Notebook aus dem aktuellen Arbeitsbereich auswählen oder ein neues hinzufügen. 

![screenshot-showing-create-notebook-activity](./media/synapse-notebook-activity/create-synapse-notebook-activity.png)

### <a name="add-a-notebook-to-synapse-pipeline"></a>Hinzufügen eines Notebooks zur Synapse-Pipeline

Wählen Sie in der oberen rechten Ecke **Zur Pipeline hinzufügen** aus, um ein Notebook zu einer vorhandenen Pipeline hinzuzufügen oder eine neue Pipeline zu erstellen.

![screenshot-showing-add-notebook-to-pipeline](./media/synapse-notebook-activity/add-to-pipeline.png)

## <a name="passing-parameters"></a>Übergeben von Parametern

### <a name="designate-a-parameters-cell"></a>Festlegen einer Parameterzelle

# <a name="classic-notebook"></a>[Klassisches Notebook](#tab/classical)

Klicken Sie zum Parametrisieren Ihres Notebooks auf die Auslassungspunkte (...), um auf das Menü für weitere Zellenaktionen ganz rechts zuzugreifen. Klicken Sie dann auf **Parameterzelle umschalten**, um die Zelle als Parameterzelle festzulegen.

[![screenshot-showing-toggle-parameter](./media/synapse-notebook-activity/toggle-parameter-cell.png)](./media/synapse-notebook-activity/toggle-parameter-cell.png#lightbox)

# <a name="preview-notebook"></a>[Notebook mit Vorschau](#tab/preview)

Um Ihr Notebook zu parametrisieren, wählen Sie die Auslassungspunkte (...) aus, um auf der Zellensymbolleiste auf **Weitere Befehle** zuzugreifen. Klicken Sie dann auf **Parameterzelle umschalten**, um die Zelle als Parameterzelle festzulegen.

[![screenshot-showing-azure-notebook-toggle-parameter](./media/synapse-notebook-activity/azure-notebook-toggle-parameter-cell.png)](./media/synapse-notebook-activity/azure-notebook-toggle-parameter-cell.png#lightbox)

---

Azure Data Factory sucht nach der Parameterzelle und verwendet deren Werte als Standardwerte für die Parameter, die zur Ausführungszeit übergeben werden. Die Ausführungs-Engine fügt eine neue Zelle mit Eingabeparametern unter der Parameterzelle hinzu, um die Standardwerte zu überschreiben. 


### <a name="assign-parameters-values-from-a-pipeline"></a>Zuweisen von Parameterwerten über eine Pipeline

Sobald Sie ein Notebook mit Parametern erstellt haben, können Sie dieses über eine Pipeline mit der Synapse-Notebookaktivität ausführen. Nachdem Sie die Aktivität zu Ihrer Pipelinecanvas hinzugefügt haben, können Sie die Parameterwerte auf der Registerkarte **Einstellungen** im Abschnitt **Base parameters** (Basisparameter) festlegen. 

[![screenshot-showing-assign-a-parameter](./media/synapse-notebook-activity/assign-parameter.png)](./media/synapse-notebook-activity/assign-parameter.png#lightbox)

Beim Zuweisen von Parameterwerten können Sie die [Pipelineausdruckssprache](../data-factory/control-flow-expression-language-functions.md) oder [Systemvariablen](../data-factory/control-flow-system-variables.md) verwenden.


## <a name="read-synapse-notebook-cell-output-value"></a>Lesen des Ausgabewerts einer Synapse-Notebookzelle

Sie können den Ausgabewert der Notebookzelle in den nächsten Aktivitäten folgendermaßen lesen:
1. Rufen Sie die API [mssparkutils.notebook.exit](./spark/microsoft-spark-utilities.md#exit-a-notebook) in Ihrer Synapse-Notebookaktivität auf, um den Wert zurückzugeben, den Sie in der Aktivitätsausgabe anzeigen möchten, zum Beispiel:  

    ```python
    mssparkutils.notebook.exit("hello world") 
    ```
    
    Wenn Sie den Notebookinhalt speichern und die Pipeline erneut auslösen, enthält die Ausgabe für die Notebookaktivität den exitValue-Wert, der für die in Schritt 2 folgenden Aktivitäten verwendet werden kann. 

2.  Lesen Sie die Eigenschaft exitValue aus der Ausgabe der Notebookaktivität. Mit diesem Beispielausdruck können Sie überprüfen, ob der von der Ausgabe der Notebookaktivität abgerufene Wert für exitValue der Zeichenfolge „hello world“ entspricht: 

    [![screenshot-showing-read-exit-value](./media/synapse-notebook-activity/synapse-read-exit-value.png)](./media/synapse-notebook-activity/synapse-read-exit-value.png#lightbox)


## <a name="run-another-synapse-notebook"></a>Ausführen eines weiteren Synapse-Notebooks 

Sie können in einer Synapse-Notebookaktivität auf andere Notebooks verweisen, indem Sie [%run magic](./spark/apache-spark-development-using-notebooks.md#notebook-reference) oder die [Notebook-Hilfsprogramme mssparkutils](./spark/microsoft-spark-utilities.md#notebook-utilities) aufrufen. Beide unterstützen geschachtelte Funktionsaufrufe. Berücksichtigen Sie je nach Szenario die folgenden wichtigen Unterschiede zwischen diesen beiden Methoden:

- [%run magic](./spark/apache-spark-development-using-notebooks.md#notebook-reference) kopiert alle Zellen aus dem Notebook, auf das verwiesen wird, in die Zelle %run und teilt den Variablenkontext. Wenn notebook1 über `%run notebook2` auf notebook2 verweist und notebook2 die Funktion [mssparkutils.notebook.exit](./spark/microsoft-spark-utilities.md#exit-a-notebook) aufruft, wird die Zellenausführung in notebook1 beendet. Sie sollten %run magic verwenden, wenn Sie eine Notebookdatei einschließen möchten.
- Die [Notebook-Hilfsprogramme mssparkutils](./spark/microsoft-spark-utilities.md#notebook-utilities) rufen das Notebook, auf das verwiesen wird, als Methode oder Funktion auf. Der Variablenkontext wird nicht geteilt. Wenn notebook1 über `mssparkutils.notebook.run("notebook2")` auf notebook2 verweist und notebook2 die Funktion [mssparkutils.notebook.exit](./spark/microsoft-spark-utilities.md#exit-a-notebook) aufruft, wird die Zellenausführung in notebook1 fortgesetzt. Sie sollten die Notebook-Hilfsprogramme mssparkutils verwenden, wenn Sie ein Notebook importieren möchten.

>[!Note]
> Das Ausführen eines weiteren Synapse-Notebooks über eine Synapse-Pipeline funktioniert nur für ein Notebook mit aktivierter Vorschau.


## <a name="see-notebook-activity-run-history"></a>Anzeigen des Verlaufs für ausgeführte Notebookaktivitäten

Unter **Pipelineausführungen** auf der Registerkarte **Überwachen** werden die ausgelösten Pipelines aufgeführt. Öffnen Sie die Pipeline, die Notebookaktivitäten enthält, um den Ausführungsverlauf anzuzeigen. 

Wählen Sie die Schaltfläche **Notebook öffnen** aus, um die Momentaufnahme der letzten Notebookausführung anzuzeigen, die auch Zelleneingaben und -ausgaben enthält. 
![see-notebook-activity-history](./media/synapse-notebook-activity/input-output-open-notebook.png)


Wählen Sie die Schaltfläche **Eingabe** oder **Ausgabe** aus, um die Eingaben oder Ausgaben der Notebookaktivität anzuzeigen. Wenn in Ihrer Pipeline ein Benutzerfehler auftritt, können Sie **Ausgabe** auswählen. Im Feld **Ergebnis** ist eine ausführliche Rückverfolgung des Benutzerfehlers vorhanden.

![screenshot-showing-see-output-user-error](./media/synapse-notebook-activity/notebook-output-user-error.png)


## <a name="synapse-notebook-activity-definition"></a>Definition der Synapse-Notebookaktivität

Hier finden Sie eine JSON-Beispieldefinition für eine Synapse-Notebookaktivität:

```json
{
    "name": "parameter_test",
    "type": "SynapseNotebook",
    "dependsOn": [],
    "policy": {
        "timeout": "7.00:00:00",
        "retry": 0,
        "retryIntervalInSeconds": 30,
        "secureOutput": false,
        "secureInput": false
    },
    "userProperties": [],
    "typeProperties": {
        "notebook": {
            "referenceName": "parameter_test",
            "type": "NotebookReference"
        },
        "parameters": {
            "input": {
                "value": {
                    "value": "@pipeline().parameters.input",
                    "type": "Expression"
                }
            }
        }
    }
}

```


## <a name="synapse-notebook-activity-output"></a>Ausgabe der Synapse-Notebookaktivität

Hier finden Sie ein JSON-Beispiel für die Ausgabe einer Synapse-Notebookaktivität:

```json

{
    "status": {
        "Status": 1,
        "Output": {
            "status": <livySessionInfo>
            },
            "result": {
                "runId": "<GUID>",
                "runStatus": "Succeed",
                "message": "Notebook execution is in Succeeded state",
                "lastCheckedOn": "2021-03-23T00:40:10.6033333Z",
                "errors": {
                    "ename": "",
                    "evalue": ""
                },
                "sessionId": 4,
                "sparkpool": "sparkpool",
                "snapshotUrl": "https://myworkspace.dev.azuresynapse.net/notebooksnapshot/{guid}",
                "exitCode": "abc" // return value from user notebook via mssparkutils.notebook.exit("abc")
            }
        },
        "Error": null,
        "ExecutionDetails": {}
    },

    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (West US 2)",
    "executionDuration": 234,
    "durationInQueue": {
        "integrationRuntimeQueue": 0
    },
    "billingReference": {
        "activityType": "ExternalActivity",
        "billableDuration": [
            {
                "meterType": "AzureIR",
                "duration": 0.06666666666666667,
                "unit": "Hours"
            }
        ]
    }
}

```
