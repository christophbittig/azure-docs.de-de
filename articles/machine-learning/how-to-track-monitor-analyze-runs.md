---
title: Nachverfolgen, Überwachen und Analysieren von Ausführungen
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie Ihre Experimente für maschinelles Lernen mit dem Azure Machine Learning Python SDK starten, überwachen und verfolgen können.
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
author: swinner95
ms.author: shwinne
ms.reviewer: sgilley
ms.date: 10/21/2021
ms.topic: how-to
ms.custom: devx-track-python, devx-track-azurecli
ms.openlocfilehash: f0816e7d308eb36f6fb131419bcbcdd384bdc37b
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131560756"
---
# <a name="start-monitor-and-track-run-history"></a>Ausführungsverlauf starten, überwachen und verfolgen

Das [Azure Machine Learning SDK für Python](/python/api/overview/azure/ml/intro), die [Machine Learning-CLI](reference-azure-machine-learning-cli.md) und [Azure Machine Learning Studio](https://ml.azure.com) bieten verschiedene Methoden zum Überwachen, Organisieren und Verwalten Ihrer Ausführungen zumr Training und Experimentieren. Ihr ML-Ausführungsverlauf ist ein wichtiger Bestandteil eines erklärbaren und wiederholbaren ML-Entwicklungsprozesses.

In diesem Artikel wird gezeigt, wie Sie die folgenden Aufgaben ausführen:

* Überwachen der Ausführungsleistung
* Fügen Sie den Ausführungsanzeigenamen hinzu. 
* Erstellen einer benutzerdefinierten Ansicht. 
* Hinzufügen einer Ausführungsbeschreibung 
* Markieren und Suchen von Ausführungen
* Führen Sie die Suche über Ihren Ausführungsverlauf aus. 
* Abbrechen oder Fehler von Ausführungen
* Erstellen untergeordneter Ausführungen
* Überwachen des Ausführungsstatus per E-Mail-Benachrichtigung
 

> [!TIP]
> Informationen zur Überwachung des Azure Machine Learning-Diensts und der zugehörigen Azure-Dienste finden Sie unter [Überwachen von Azure Machine Learning](monitor-azure-machine-learning.md).
> Informationen zur Überwachung von Modellen, die als Webdienste bereitgestellt werden, finden Sie unter [Sammeln von Daten von Modellen in der Produktion](how-to-enable-data-collection.md) sowie unter [Überwachen und Erfassen von Daten von ML-Webdienst-Endpunkten](how-to-enable-app-insights.md).

## <a name="prerequisites"></a>Voraussetzungen

Sie benötige folgende Elemente:

* Ein Azure-Abonnement. Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie ein kostenloses Konto erstellen, bevor Sie beginnen. Probieren Sie die [kostenlose oder kostenpflichtige Version von Azure Machine Learning](https://azure.microsoft.com/free/) noch heute aus.

* Ein [Azure Machine Learning-Arbeitsbereich](how-to-manage-workspace.md).

* Das Azure Machine Learning SDK für Python (Version 1.0.21 oder höher). Wie Sie die neueste Version des SDK installieren oder auf diese aktualisieren, erfahren Sie unter [Installieren des Azure Machine Learning SDK für Python](/python/api/overview/azure/ml/install).

    Um Ihre Version des Azure Machine Learning SDK zu überprüfen, verwenden Sie den folgenden Code:

    ```python
    print(azureml.core.VERSION)
    ```

* Die [Azure CLI](/cli/azure/?preserve-view=true&view=azure-cli-latest) und die [CLI-Erweiterung für Azure Machine Learning](reference-azure-machine-learning-cli.md).


## <a name="monitor-run-performance"></a>Überwachen der Ausführungsleistung

* Starten einer Ausführung und deren Protokollierungsprozess

    # <a name="python"></a>[Python](#tab/python)
    
    1. Richten Sie Ihr Experiment durch Importieren der Klassen [Workspace](/python/api/azureml-core/azureml.core.workspace.workspace), [Experiment](/python/api/azureml-core/azureml.core.experiment.experiment), [Run](/python/api/azureml-core/azureml.core.run%28class%29) und [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig) aus dem [azureml.core](/python/api/azureml-core/azureml.core)-Paket ein.
    
        ```python
        import azureml.core
        from azureml.core import Workspace, Experiment, Run
        from azureml.core import ScriptRunConfig
        
        ws = Workspace.from_config()
        exp = Experiment(workspace=ws, name="explore-runs")
        ```
    
    1. Starten Sie eine Ausführung und ihren Protokollierungsprozess mit der [`start_logging()`](/python/api/azureml-core/azureml.core.experiment%28class%29#start-logging--args----kwargs-)-Methode.
    
        ```python
        notebook_run = exp.start_logging()
        notebook_run.log(name="message", value="Hello from run!")
        ```
        
    # <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)
    
    Führen Sie die folgenden Schritte aus, um eine Ausführung Ihres Experiments zu starten:
    
    1. Verwenden Sie in einer Shell oder Eingabeaufforderung die Azure CLI, um sich bei Ihrem Azure-Abonnement zu authentifizieren:
    
        ```azurecli-interactive
        az login
        ```
        
        [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 
    
    1. Fügen Sie eine Arbeitsbereichskonfiguration an den Ordner an, der Ihr Trainingsskript enthält. Ersetzen Sie `myworkspace` durch Ihren Azure Machine Learning-Arbeitsbereich. Ersetzen Sie `myresourcegroup` durch die Azure-Ressourcengruppe, die Ihren Arbeitsbereich enthält:
    
        ```azurecli-interactive
        az ml folder attach -w myworkspace -g myresourcegroup
        ```
    
        Dieser Befehl erstellt ein `.azureml`-Unterverzeichnis, das RUNCONFIG-Beispieldateien und Conda-Umgebungsdateien enthält. Es enthält auch eine `config.json`-Datei, die für die Kommunikation mit dem Azure Machine Learning-Arbeitsbereich verwendet wird.
    
        Weitere Informationen finden Sie unter [az ml folder attach](/cli/azure/ml(v1)/folder#az_ml_folder_attach).
    
    2. Verwenden Sie den folgenden Befehl zum Starten der Ausführung. Wenn Sie diesen Befehl verwenden, geben Sie den Namen der runconfig-Datei (den Text vor \*.runconfig, wenn Sie Ihr Dateisystem betrachten) gegen den Parameter -c an.
    
        ```azurecli-interactive
        az ml run submit-script -c sklearn -e testexperiment train.py
        ```
    
        > [!TIP]
        > Mit dem Befehl `az ml folder attach` wurde ein `.azureml`-Unterverzeichnis erstellt, das zwei RUNCONFIG-Beispieldateien enthält.
        >
        > Wenn Sie ein Python-Skript haben, das programmgesteuert ein Laufzeitkonfigurationsobjekt erstellt, können Sie es mit [RunConfig.save()](/python/api/azureml-core/azureml.core.runconfiguration#save-path-none--name-none--separate-environment-yaml-false-) als RUNCPNFIG-Datei speichern.
        >
        > Weitere RUNCONFIG-Beispieldateien finden Sie unter [https://github.com/MicrosoftDocs/pipelines-azureml/](https://github.com/MicrosoftDocs/pipelines-azureml/).
    
        Weitere Informationen finden Sie unter [az ml run submit-script](/cli/azure/ml(v1)/run#az_ml_run_submit-script).

    # <a name="studio"></a>[Studio](#tab/azure-studio)

    Ein Beispiel für das Trainieren eines Modells im Azure Machine Learning-Designer finden Sie unter [Tutorial: Prognostizieren von Automobilpreisen mit dem Designer](tutorial-designer-automobile-price-train-score.md).

    ---

* Überwachen des Status einer Ausführung

    # <a name="python"></a>[Python](#tab/python)
    
    * Rufen Sie den Status einer Ausführung mit der [`get_status()`](/python/api/azureml-core/azureml.core.run%28class%29#get-status--)-Methode ab.
    
        ```python
        print(notebook_run.get_status())
        ```
    
    * Um die Ausführungs-ID, die Ausführungszeit und andere Details über die Ausführung abzurufen, verwenden Sie die [`get_details()`](/python/api/azureml-core/azureml.core.workspace.workspace#get-details--) Methode.
    
        ```python
        print(notebook_run.get_details())
        ```
    
    * Wenn die Ausführung erfolgreich abgeschlossen wurde, verwenden Sie die [`complete()`](/python/api/azureml-core/azureml.core.run%28class%29#complete--set-status-true-)-Methode, um sie als abgeschlossen zu markieren.
    
        ```python
        notebook_run.complete()
        print(notebook_run.get_status())
        ```
    
    * Wenn Sie das Python-Entwurfsmuster `with...as` verwenden, markiert sich die Ausführung automatisch selbst als abgeschlossen, wenn sie außerhalb des Bereichs liegt. Sie müssen die Ausführung nicht manuell als abgeschlossen markieren.
        
        ```python
        with exp.start_logging() as notebook_run:
            notebook_run.log(name="message", value="Hello from run!")
            print(notebook_run.get_status())
        
        print(notebook_run.get_status())
        ```
    
    # <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)
    
    * Verwenden Sie den folgenden Befehl, um eine Liste der Ausführungen für Ihr Experiment anzuzeigen. Ersetzen Sie `experiment` durch den Namen Ihres Experiments:
    
        ```azurecli-interactive
        az ml run list --experiment-name experiment
        ```
    
        Dieser Befehl gibt ein JSON-Dokument zurück, in dem Informationen zu Ausführungen für dieses Experiment aufgelistet sind.
    
        Weitere Informationen finden Sie unter [az ml experiment list](/cli/azure/ml(v1)/experiment#az_ml_experiment_list).
    
    * Verwenden Sie den folgenden Befehl, um Informationen zu einer bestimmten Ausführung anzuzeigen. Ersetzen Sie `runid` durch die ID der Ausführung:
    
        ```azurecli-interactive
        az ml run show -r runid
        ```
    
        Dieser Befehl gibt ein JSON-Dokument zurück, in dem Informationen zur Ausführung aufgelistet sind.
    
        Weitere Informationen finden Sie unter [az ml run show](/cli/azure/ml(v1)/run#az_ml_run_show).
    
    
    # <a name="studio"></a>[Studio](#tab/azure-studio)
    
    ---    
   
## <a name="run-display-name"></a>Ausführungsanzeigename 
Der Ausführungsanzeigename ist ein optionaler und anpassbarer Name, den Sie für Ihre Ausführung angeben können. So ändern Sie den Ausführungsanzeigenamen:

1. Navigieren Sie zur Ausführungsliste. 

2. Wählen Sie die Ausführung aus, um den Anzeigenamen auf der Seite mit den Ausführungsdetails zu ändern.

3. Klicken Sie auf die Schaltfläche **Bearbeiten**, um den Ausführungsanzeigenamen zu ändern. 

:::image type="content" source="media/how-to-track-monitor-analyze-runs/display-name.gif" alt-text="Screenshot: Bearbeiten des Anzeigenamens":::

## <a name="custom-view"></a>Benutzerdefinierte Ansicht 
    
So zeigen Sie Ihre Ausführungen in Studio an: 
    
1. Navigieren Sie zur Registerkarte **Experimente**.
    
1. Wählen Sie entweder **Alle Experimente** aus, um alle Ausführungen in einem Experiment anzuzeigen, oder **Alle Ausführungen**, um alle im Arbeitsbereich übermittelten Ausführungen anzuzeigen.
    
Auf der Seite **Alle Ausführungen** können Sie die Liste der Ausführungen nach Tags, Experimenten, Computeziel und mehr filtern, um Ihre Arbeit besser zu organisieren und zu optimieren.  
    
1. Nehmen Sie Anpassungen an der Seite vor, indem Sie zu vergleichende Ausführungen auswählen, Diagramme hinzufügen oder Filter anwenden. Diese Änderungen können als **Benutzerdefinierte Ansicht** gespeichert werden, sodass Sie problemlos zu Ihrer Arbeit zurückkehren können. Benutzer mit Arbeitsbereich-Berechtigungen können die benutzerdefinierte Ansicht bearbeiten oder anzeigen. Außerdem können Sie die benutzerdefinierte Ansicht zur besseren Zusammenarbeit für Teammitglieder freigeben, indem Sie **Ansicht freigeben** auswählen.   

1. Wenn Sie die Ausführungsprotokolle anzeigen möchten, wählen Sie eine bestimmte Ausführung aus, und auf der Registerkarte **Ausgaben und Protokolle** finden Sie Diagnose- und Fehlerprotokolle für Ihre Ausführung.

:::image type="content" source="media/how-to-track-monitor-analyze-runs/custom-views-2.gif" alt-text="Screenshot: Erstellen einer benutzerdefinierten Ansicht":::
    

## <a name="run-description"></a>Ausführungsbeschreibung 

Eine Ausführungsbeschreibung kann einer Ausführung hinzugefügt werden, um mehr Informationen zur Ausführung zu liefern, u. a. zum Kontext. Sie können diese Beschreibungen auch in der Liste der Ausführungen suchen und die Ausführungsbeschreibung als Spalte der Liste der Ausführungen hinzufügen. 

Navigieren Sie zur Seite **Ausführungsdetails** für Ihre Ausführung, und wählen Sie das Bearbeitungs- oder Stiftsymbol aus, um Beschreibungen für Ihre Ausführung hinzuzufügen, zu bearbeiten oder zu löschen. Wenn Sie die Änderungen an der Liste der Ausführungen beibehalten möchten, speichern Sie die Änderungen in Ihrer vorhandenen „Benutzerdefinierten Ansicht“ oder einer neuen „Benutzerdefinierten Ansicht“. Das Markdown-Format wird für Ausführungsbeschreibungen unterstützt, sodass, wie unten angezeigt, Abbildungen eingebettet werden können und Deep Linking ermöglicht wird.

:::image type="content" source="media/how-to-track-monitor-analyze-runs/run-description-2.gif" alt-text="Screenshot: Erstellen einer Ausführungsbeschreibung"::: 

## <a name="tag-and-find-runs"></a>Markieren und Suchen von Ausführungen

In Azure Machine Learning können Sie Eigenschaften und Tags zum Organisieren und Abfragen wichtiger Informationen Ihrer Ausführungen verwenden.

* Hinzufügen von Eigenschaften und Tags

    # <a name="python"></a>[Python](#tab/python)
    
    Um Ihren Ausführungen durchsuchbare Metadaten hinzuzufügen, verwenden Sie die [`add_properties()`](/python/api/azureml-core/azureml.core.run%28class%29#add-properties-properties-)-Methode. Der folgende Code fügt der Ausführung z. B. die `"author"`-Eigenschaft hinzu:
    
    ```Python
    local_run.add_properties({"author":"azureml-user"})
    print(local_run.get_properties())
    ```
    
    Eigenschaften sind unveränderlich, sodass sie eine permanente Aufzeichnung zu Überwachungszwecken erstellen. Das folgende Codebeispiel führt zu einem Fehler, weil `"azureml-user"` im vorstehenden Code bereits als Wert der `"author"`-Eigenschaft hinzugefügt wurde:
    
    ```Python
    try:
        local_run.add_properties({"author":"different-user"})
    except Exception as e:
        print(e)
    ```
    
    Im Gegensatz zu Eigenschaften können Tags geändert werden. Um durchsuchbare und aussagekräftige Informationen für Consumer Ihres Experiments hinzuzufügen, verwenden Sie die [`tag()`](/python/api/azureml-core/azureml.core.run%28class%29#tag-key--value-none-)-Methode.
    
    ```Python
    local_run.tag("quality", "great run")
    print(local_run.get_tags())
    
    local_run.tag("quality", "fantastic run")
    print(local_run.get_tags())
    ```
    
    Sie können auch einfache Zeichenfolgentags hinzufügen. Wenn diese Tags in das Tag-Wörterbuch als Schlüssel aufgenommen wurden, haben sie den Wert `None`.
    
    ```Python
    local_run.tag("worth another look")
    print(local_run.get_tags())
    ```
    
    # <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)
    
    > [!NOTE]
    > Mit der CLI können Sie nur Tags hinzufügen oder aktualisieren.
    
    Verwenden Sie den folgenden Befehl, um ein Tag hinzuzufügen oder zu aktualisieren:
    
    ```azurecli-interactive
    az ml run update -r runid --add-tag quality='fantastic run'
    ```
    
    Weitere Informationen finden Sie unter [az ml run update](/cli/azure/ml(v1)/run#az_ml_run_update).
    
    # <a name="studio"></a>[Studio](#tab/azure-studio)
    
    Sie können im Studio Ausführungstags hinzufügen, bearbeiten oder löschen. Navigieren Sie zur Seite **Ausführungsdetails** für Ihre Ausführung, und wählen Sie das Bearbeitungs- oder Stiftsymbol aus, um Tags für Ihre Ausführungen hinzuzufügen, zu bearbeiten oder zu löschen. Sie können auch auf der Seite mit der Ausführungsliste diese Tags suchen und danach filtern.
    
    :::image type="content" source="media/how-to-track-monitor-analyze-runs/run-tags.gif" alt-text="Screenshot: Ausführungstags hinzufügen, bearbeiten oder löschen":::
    
    ---

* Abfragen von Eigenschaften und Tags

    Sie können Ausführungen in einem Experiment abfragen, um eine Liste der Ausführungen zurückzugeben, die mit bestimmten Eigenschaften und Tags übereinstimmen.

    # <a name="python"></a>[Python](#tab/python)
    
    ```Python
    list(exp.get_runs(properties={"author":"azureml-user"},tags={"quality":"fantastic run"}))
    list(exp.get_runs(properties={"author":"azureml-user"},tags="worth another look"))
    ```
    
    # <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)
    
    Die Azure CLI unterstützt [JMESPath](http://jmespath.org)-Abfragen, mit denen Ausführungen nach Eigenschaften und Tags gefiltert werden können. Wenn Sie eine JMESPath-Abfrage mit der Azure CLI verwenden möchten, geben Sie diese mit dem Parameter `--query` an. Die folgenden Beispiele zeigen einige Abfragen mithilfe von Eigenschaften und Tags:
    
    ```azurecli-interactive
    # list runs where the author property = 'azureml-user'
    az ml run list --experiment-name experiment [?properties.author=='azureml-user']
    # list runs where the tag contains a key that starts with 'worth another look'
    az ml run list --experiment-name experiment [?tags.keys(@)[?starts_with(@, 'worth another look')]]
    # list runs where the author property = 'azureml-user' and the 'quality' tag starts with 'fantastic run'
    az ml run list --experiment-name experiment [?properties.author=='azureml-user' && tags.quality=='fantastic run']
    ```
    
    Weitere Informationen zum Abfragen von Azure CLI-Ergebnissen finden Sie unter [Abfragen der Azure CLI-Befehlsausgabe](/cli/azure/query-azure-cli?preserve-view=true&view=azure-cli-latest).
    
    # <a name="studio"></a>[Studio](#tab/azure-studio)
    
    Navigieren Sie zur Liste **Alle Ausführungen**, um nach bestimmten Ausführungen zu suchen. Hier haben Sie zwei Möglichkeiten:
    
    1. Verwenden Sie die Schaltfläche **Filter hinzufügen**, und wählen Sie Filter für Tags aus, um Ihre Ausführungen nach Tags zu filtern, die den Ausführungen zugewiesen wurden. <br><br>
    oder
    
    1. Verwenden Sie die Suchleiste, um anhand von Ausführungsmetadaten wie Ausführungsstatus, Beschreibungen, Experimentnamen und Absendernamen Ausführungen schnell zu finden. 
    
## <a name="cancel-or-fail-runs"></a>Abbrechen oder Fehler von Ausführungen

Wenn Sie einen Fehler bemerken oder die Ausführung zu lange dauert, können Sie die Ausführung abbrechen.

# <a name="python"></a>[Python](#tab/python)

Verwenden Sie zum Abbrechen einer Ausführung mit dem SDK die [`cancel()`](/python/api/azureml-core/azureml.core.run%28class%29#cancel--)-Methode:

```python
src = ScriptRunConfig(source_directory='.', script='hello_with_delay.py')
local_run = exp.submit(src)
print(local_run.get_status())

local_run.cancel()
print(local_run.get_status())
```

Wenn Ihre Ausführung abgeschlossen ist, aber Fehler enthält (etwa weil ein falsches Trainingsskript verwendet wurde), können Sie sie mit der [`fail()`](/python/api/azureml-core/azureml.core.run%28class%29#fail-error-details-none--error-code-none---set-status-true-)-Methode als fehlerhaft markieren.

```python
local_run = exp.submit(src)
local_run.fail()
print(local_run.get_status())
```

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Verwenden Sie zum Abbrechen einer Ausführung mit der CLI den folgenden Befehl. Ersetzen Sie `runid` durch die ID der Ausführung.

```azurecli-interactive
az ml run cancel -r runid -w workspace_name -e experiment_name
```

Weitere Informationen finden Sie unter [az ml run cancel](/cli/azure/ml(v1)/run#az_ml_run_cancel).

# <a name="studio"></a>[Studio](#tab/azure-studio)

Führen Sie die folgenden Schritte aus, um eine Ausführung in Studio abzubrechen:

1. Navigieren Sie entweder im Abschnitt **Experimente** oder im Abschnitt **Pipelines** zur ausgeführten Pipeline. 

1. Wählen Sie die Nummer der Pipelineausführung aus, die Sie abbrechen möchten.

1. Wählen Sie auf der Symbolleiste die Option **Abbrechen** aus.

---

## <a name="create-child-runs"></a>Erstellen untergeordneter Ausführungen

Erstellen Sie untergeordnete Ausführungen, um verwandte Ausführungen zu gruppieren, wie etwa für unterschiedliche Iterationen zum Optimieren von Hyperparametern.

> [!NOTE]
> Untergeordnete Ausführungen können nur mit dem SDK erstellt werden.

Dieses Codebeispiel verwendet das `hello_with_children.py`-Skript, um einen Batch von fünf untergeordneten Ausführungen aus einer übermittelten Ausführung mithilfe der [`child_run()`](/python/api/azureml-core/azureml.core.run%28class%29#child-run-name-none--run-id-none--outputs-none-)-Methode zu erstellen:

```python
!more hello_with_children.py
src = ScriptRunConfig(source_directory='.', script='hello_with_children.py')

local_run = exp.submit(src)
local_run.wait_for_completion(show_output=True)
print(local_run.get_status())

with exp.start_logging() as parent_run:
    for c,count in enumerate(range(5)):
        with parent_run.child_run() as child:
            child.log(name="Hello from child run", value=c)
```

> [!NOTE]
> Sobald sie den gültigen Bereich verlassen haben, werden untergeordnete Ausführungen automatisch als abgeschlossen markiert.

Um viele untergeordnete Ausführungen effizient zu erstellen, verwenden Sie die [`create_children()`](/python/api/azureml-core/azureml.core.run.run#create-children-count-none--tag-key-none--tag-values-none-)-Methode. Weil jeder Erstellungsvorgang zu einem Netzwerkaufruf führt, ist das Erstellen eines Ausführungsbatches effizienter als ein jeweils einzelnes Erstellen der Ausführungen.

### <a name="submit-child-runs"></a>Senden untergeordneter Ausführungen

Untergeordnete Ausführungen können auch aus einer übergeordneten Ausführung gesendet werden. So können Sie Hierarchien aus übergeordneten und untergeordneten Ausführungen erstellen. Sie können keine elternlose Child-Ausführung erstellen: Auch wenn die Parent-Ausführung nichts anderes tut, als Child-Ausführungen zu starten, ist es trotzdem notwendig, die Hierarchie zu erstellen. Der Status aller Ausführungen ist unabhängig: Eine übergeordnete Ausführung kann den Erfolgsstatus `"Completed"` aufweisen, auch wenn eine untergeordnete Ausführung abgebrochen wurde oder zu einem Fehler führte.  

Möglicherweise möchten Sie, dass untergeordnete Ausführungen eine andere Ausführungskonfiguration verwenden als die übergeordnete Ausführung. Sie könnten beispielsweise eine weniger leistungsfähige CPU-basierte Konfiguration für die übergeordnete Ausführung und GPU-basierte Konfigurationen für die untergeordneten Ausführungen verwenden. Ein weiterer häufiger Wunsch ist es, jeder Child-Ausführung unterschiedliche Argumente und Daten zu übergeben. Erstellen Sie zum Anpassen einer untergeordneten Ausführung ein Objekt vom Typ `ScriptRunConfig` für die untergeordnete Ausführung. 

> [!IMPORTANT]
> Um eine untergeordnete Ausführung von einer übergeordneten Ausführung auf einem Remotecomputer zu übermitteln, müssen Sie sich zuerst im übergeordneten Ausführungscode beim Arbeitsbereich anmelden. Standardmäßig verfügt das Ausführungskontextobjekt in einer Remoteversion nicht über Anmeldeinformationen zum Übermitteln untergeordneter Ausführungen. Verwenden Sie die Anmeldeinformationen für einen Dienstprinzipal oder eine verwaltete Identität. Weitere Informationen zur Authentifizierung finden Sie unter [Einrichten der Authentifizierung](how-to-setup-authentication.md).

Der folgende Code:

- Ruft eine benannte `"gpu-cluster"`Computerressource aus dem Arbeitsbereich`ws` ab
- Es durchläuft verschiedene Argumentwerte, die an die untergeordneten `ScriptRunConfig`-Objekte übergeben werden sollen.
- Es erstellt und übermittelt eine neue untergeordnete Ausführung und verwendet dabei benutzerdefinierte Computeressourcen und Argumente.
- Es blockiert die weitere Ausführung, bis alle untergeordneten Ausführungen beendet sind.

```python
# parent.py
# This script controls the launching of child scripts
from azureml.core import Run, ScriptRunConfig

compute_target = ws.compute_targets["gpu-cluster"]

run = Run.get_context()

child_args = ['Apple', 'Banana', 'Orange']
for arg in child_args: 
    run.log('Status', f'Launching {arg}')
    child_config = ScriptRunConfig(source_directory=".", script='child.py', arguments=['--fruit', arg], compute_target=compute_target)
    # Starts the run asynchronously
    run.submit_child(child_config)

# Experiment will "complete" successfully at this point. 
# Instead of returning immediately, block until child runs complete

for child in run.get_children():
    child.wait_for_completion()
```

Um auf effiziente Weise mehrere untergeordnete Ausführungen mit identischen Konfigurationen, Argumenten und Eingaben zu erstellen, verwenden Sie die [`create_children()`](/python/api/azureml-core/azureml.core.run.run#create-children-count-none--tag-key-none--tag-values-none-)-Methode. Weil jeder Erstellungsvorgang zu einem Netzwerkaufruf führt, ist das Erstellen eines Ausführungsbatches effizienter als ein jeweils einzelnes Erstellen der Ausführungen.

In einer untergeordneten Ausführung können Sie die ID der übergeordneten Ausführung anzeigen:

```python
## In child run script
child_run = Run.get_context()
child_run.parent.id
```

### <a name="query-child-runs"></a>Abfragen von untergeordneten Ausführungen

Um die untergeordneten Ausführungen eines bestimmten übergeordneten Elements abzufragen, verwenden Sie die [`get_children()`](/python/api/azureml-core/azureml.core.run%28class%29#get-children-recursive-false--tags-none--properties-none--type-none--status-none---rehydrate-runs-true-)-Methode. Über das Argument ``recursive = True`` können Sie eine geschachtelte Struktur aus untergeordneten Elementen und Enkelelementen abfragen.

```python
print(parent_run.get_children())
```

### <a name="log-to-parent-or-root-run"></a>Protokollieren in einer übergeordneten oder der Stammausführung

Sie können über das Feld `Run.parent` auf die Ausführung zugreifen, die die aktuelle untergeordnete Ausführung gestartet hat. Ein häufiger Anwendungsfall für `Run.parent`ist die Zusammenfassung von Protokollergebnissen an einer einzigen Stelle. Untergeordnete Ausführungen werden asynchron ausgeführt, und es gibt keine Garantie für die Reihenfolge oder Synchronisation, abgesehen von der Fähigkeit der übergeordneten Ausführung, auf die Fertigstellung ihrer untergeordneten Ausführungen zu warten.

```python
# in child (or even grandchild) run

def root_run(self : Run) -> Run :
    if self.parent is None : 
        return self
    return root_run(self.parent)

current_child_run = Run.get_context()
root_run(current_child_run).log("MyMetric", f"Data from child run {current_child_run.id}")

```

## <a name="monitor-the-run-status-by-email-notification"></a>Überwachen des Ausführungsstatus per E-Mail-Benachrichtigung

1. Wählen Sie im [Azure-Portal](https://ms.portal.azure.com/) auf der linken Navigationsleiste die Registerkarte **Überwachen** aus. 

1. Wählen Sie **Diagnoseeinstellungen** und dann **+ Diagnoseeinstellung hinzufügen** aus.

    ![Screenshot der Diagnoseeinstellungen für die E-Mail-Benachrichtigung](./media/how-to-track-monitor-analyze-runs/diagnostic-setting.png)

1. Führen Sie für die Diagnoseeinstellung Folgendes aus: 
    1. Wählen Sie unter **Kategoriedetails** das **AmlRunStatusChangedEvent** aus. 
    1. Wählen Sie in den **Zieldetails** die Option **An Log Analytics-Arbeitsbereich senden** aus, und geben Sie das **Abonnement** und den **Log Analytics-Arbeitsbereich** an. 

    > [!NOTE]
    > Der **Azure Log Analytics-Arbeitsbereich** ist ein anderer Typ von Azure-Ressource als der **Azure Machine Learning Service-Arbeitsbereich**. Wenn diese Liste keine Optionen enthält, können Sie [einen Log Analytics-Arbeitsbereich erstellen](../azure-monitor/logs/quick-create-workspace.md). 
    
    ![Speicherort für E-Mail-Benachrichtigungen](./media/how-to-track-monitor-analyze-runs/log-location.png)

1. Fügen Sie auf der Registerkarte **Protokolle** eine **Neue Warnungsregel** hinzu. 

    ![Neue Warnungsregel](./media/how-to-track-monitor-analyze-runs/new-alert-rule.png)

1. Weitere Informationen finden Sie unter [Erstellen, Anzeigen und Verwalten von Protokollwarnungen mithilfe von Azure Monitor](../azure-monitor/alerts/alerts-log.md).

## <a name="example-notebooks"></a>Beispielnotebooks

Die folgenden Notebooks veranschaulichen die Konzepte in diesem Artikel:

* Weitere Informationen zu den Protokollierungs-APIs finden Sie im [Protokollierungs-API-Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api/logging-api.ipynb).

* Weitere Informationen zum Verwalten von Ausführungen mit dem Azure Machine Learning-SDK finden Sie in dem Beitrag zum [Verwalten von Notebooks von Ausführungen](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/manage-runs/manage-runs.ipynb).

## <a name="next-steps"></a>Nächste Schritte

* Informationen zum Protokollieren von Metriken für Ihre Experimente finden Sie unter [Protokollieren von Metriken während Trainingsausführungen](how-to-log-view-metrics.md).
* Informationen zum Überwachen von Ressourcen und Protokollen aus Azure Machine Learning finden Sie unter [Überwachen von Azure Machine Learning](monitor-azure-machine-learning.md).
