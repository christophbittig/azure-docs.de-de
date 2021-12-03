---
title: Was ist eine Komponente (Vorschau)?
titleSuffix: Azure Machine Learning
description: Verwenden Sie Azure Machine Learning-Komponenten, um Pipelines für maschinelles Lernen zu erstellen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: keli19
author: likebupt
ms.reviewer: laobri
ms.date: 10/21/2021
ms.topic: conceptual
ms.openlocfilehash: 5846a84df6c11364fed6fa65b852b55c42cd5364
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131566607"
---
# <a name="what-is-an-azure-machine-learning-component-preview"></a>Was ist eine Azure Machine Learning-Komponente (Vorschau)?

Eine Azure Machine Learning-Komponente (früher als Modul bezeichnet) ist ein eigenständiger Code, der einen Schritt in einer Machine Learning-Pipeline übernimmt. Komponenten sind die Bausteine erweiterter Machine Learning-Pipelines (siehe [Erstellen und Ausführen von Machine Learning-Pipelines mit der Azure Machine Learning CLI](how-to-create-component-pipelines-cli.md)). Komponenten können Aufgaben wie Datenverarbeitung, Modelltraining, Modellbewertung usw. ausführen.

Eine Komponente ist vergleichbar mit einer Funktion – sie hat einen Namen, Parameter, erwartet eine Eingabe und gibt eine Ausgabe zurück. Weitere Informationen zum Erstellen einer Komponente finden Sie unter [Erstellen einer Komponente](#define-a-component-preview).

## <a name="why-should-i-use-a-component-preview"></a>Warum sollte ich eine Komponente verwenden (Vorschau)?

Mithilfe von Komponenten können Sie eine gemeinsame Logik pipelineübergreifend verwalten und wiederverwenden.

- **Zusammensetzbar**: Mithilfe von Komponenten können Entwickler komplizierte Logik hinter einer einfachen Schnittstelle verbergen. Komponentenbenutzer müssen sich nicht um die zugrunde liegende Logik kümmern, sondern einfach Parameter bereitstellen.

- **Freigeben und Wiederverwenden**: Komponenten werden automatisch für Benutzer im gleichen Arbeitsbereich freigegeben. Sie können Komponenten über Pipelines, Umgebungen, Arbeitsbereiche und Abonnements hinweg wiederverwenden. Mit der integrierten Versionsnachverfolgung können Sie Änderungen nachverfolgen und Ergebnisse reproduzieren.

- **CLI-Unterstützung**: Verwenden Sie Komponenten, um Pipelines in der CLI (v2) zu erstellen.


## <a name="define-a-component-preview"></a>Definieren einer Komponente (Vorschau)

Um eine Azure Machine Learning-Komponente zu definieren, müssen Sie zwei Dateien bereitstellen:

- Eine Komponentenspezifikation im gültigen [YAML-Format für die Komponentenspezifikation](reference-yaml-component-command.md). Diese Datei gibt folgende Informationen an:
  - Metadaten: Name, Anzeigename, Version, Typ usw.
  - Schnittstelle: Ein- und Ausgaben
  - Befehl, Code und Umgebung: Der Befehl, der Code und die Umgebung zum Ausführen der Komponente
- Ein Skript zum Bereitstellen der tatsächlichen Ausführungslogik.

### <a name="component-specification"></a>Komponentenspezifikation

Die Komponentenspezifikationsdatei definiert die Metadaten und Ausführungsparameter für eine Komponente. Die Komponentenspezifikation teilt Azure Machine Learning mit, wie das von Ihnen bereitgestellte Python-Skript ausgeführt wird.

Das folgende Beispiel ist eine Komponentenspezifikation für eine Trainingskomponente.

```yaml
name: Example_Train
display_name: Example Train
version: 20
type: command
description: Example of a torchvision training component
tags: {category: Component Tutorial, contact: user@contoso.com}
inputs:
  training_data: 
    type: path
    description: Training data organized in torchvision structure
  max_epochs:
    type: integer
    description: Maximum epochs for training
  learning_rate: 
    type: number
    description: Learning rate, default is 0.01
    default: 0.01
  learning_rate_schedule: 
    type: string
    default: time-based 
outputs:
  model_output:
    type: path
code:
  local_path: ./train_src
environment: azureml:AzureML-Minimal:1
command: >-
  python train.py 
  --training_data ${{inputs.training_data}} 
  --max_epochs ${{inputs.max_epochs}}   
  --learning_rate ${{inputs.learning_rate}} 
  --learning_rate_schedule ${{inputs.learning_rate_schedule}} 
  --model_output ${{outputs.model_output}}
```

In der folgenden Tabelle werden die Felder im Beispiel erläutert. Eine vollständige Liste der verfügbaren Felder finden Sie auf der [Referenzseite zur YAML-Komponentenspezifikation](reference-yaml-component-command.md).

| Name                | type                                                     | Erforderlich | BESCHREIBUNG                                                  |
| ------------------- | -------------------------------------------------------- | -------- | ------------------------------------------------------------ |
| name                | Zeichenfolge                                                   | Ja      | Der Name der Komponente. Muss ein eindeutiger Bezeichner der Komponente sein. Muss mit einer Zahl oder einem Buchstaben beginnen und darf nur Buchstaben, Zahlen, `_` und `-` enthalten. Die maximale Länge beträgt 255 Zeichen.|
| version             | Zeichenfolge                                                   | Ja      | Version der Komponente. Muss eine Zeichenfolge sein. |
| display_name        | Zeichenfolge                                                   | Nein       | Anzeigename der Komponente. Der Standardwert ist identisch mit `name`. |
| type                | Zeichenfolge                                                   | Nein       | Der Typ der Komponente. Zurzeit muss dieser Wert auf `command` festgelegt werden.|
| description         | Zeichenfolge                                                   | Nein       | Ausführliche Beschreibung der Komponente. |
| tags                | Dictionary&lt;Zeichenfolge&gt;                                       | Nein | Eine Liste von Schlüssel-Wert-Paaren, um verschiedene Perspektiven der Komponente zu beschreiben. Der Schlüssel und Wert jedes Tags sollte ein Wort oder ein kurzer Ausdruck sein, z. B. `Product:Office`, `Domain:NLP`, `Scenario:Image Classification`. |
| is_deterministic    | boolean                                                  | Nein       | Gibt an, ob die Komponente bei gleichen Eingabedaten immer das gleiche Ergebnis generiert. Der Standardwert lautet `True`. Sollte bei Komponenten, die Daten aus externen Ressourcen laden, z. B. beim Import von Daten aus einer bestimmten URL, auf `False` festgelegt werden, da die Daten möglicherweise aktualisiert werden können. |
| inputs              | Dictionary&lt;Zeichenfolge, Eingabe&gt; | Nein       | Definiert Eingabeports und Parameter der Komponente. Der Zeichenfolgenschlüssel ist der Name der Eingabe, der ein gültiger Python-Variablenname sein muss. |
| outputs             | Dictionary&lt;Zeichenfolge, Ausgabe&gt;                    | Nein       | Definiert Ausgabeports der Komponente. Der Zeichenfolgenschlüssel ist der Name der Ausgabe, der ein gültiger Python-Variablenname sein muss. |
| code                | Zeichenfolge                                                   | Nein       | Pfad zum Quellcode. |
| Environment         | Umgebung                              | Nein       | Die Laufzeitumgebung für die auszuführende Komponente. |
| command             | Zeichenfolge                                             | Nein    | Der Befehl zum Ausführen des Komponentencodes.         |

### <a name="python-script"></a>Python-Skript

Ihr Python-Skript enthält die ausführbare Logik für Ihre Komponente. Ihr Skript teilt Azure Machine Learning mit, was Sie von Ihrer Komponente erwarten.

Zur Ausführung müssen Sie die Argumente für Ihr Python-Skript mit den in der YAML-Spezifikation definierten Argumenten abgleichen. Das folgende Beispiel ist ein Python-Trainingsskript, das der YAML-Spezifikation aus dem vorherigen Abschnitt entspricht.

```python
## Required imports 
import argparse
import os
## Import other dependencies your script needs
from pathlib import Path
from uuid import uuid4
from datetime import datetime

## Define an argument parser that matches the arguments from the components specification file
parser = argparse.ArgumentParser("train")
parser.add_argument("--training_data", type=str, help="Path to training data")
parser.add_argument("--max_epochs", type=int, help="Max # of epochs for the training")
parser.add_argument("--learning_rate", type=float, help="Learning rate")
parser.add_argument("--learning_rate_schedule", type=str, help="Learning rate schedule")
parser.add_argument("--model_output", type=str, help="Path of output model")

args = parser.parse_args()

## Implement your custom logic (in this case a training script)
print ("hello training world...")

lines = [
    f'Training data path: {args.training_data}',
    f'Max epochs: {args.max_epochs}',
    f'Learning rate: {args.learning_rate}',
    f'Learning rate: {args.learning_rate_schedule}',
    f'Model output path: {args.model_output}',
]

for line in lines:
    print(line)

print("mounted_path files: ")
arr = os.listdir(args.training_data)
print(arr)

for filename in arr:
    print ("reading file: %s ..." % filename)
    with open(os.path.join(args.training_data, filename), 'r') as handle:
        print (handle.read())

## Do the train and save the trained model as a file into the output folder.
## Here only output a dummy data for example.
curtime = datetime.now().strftime("%b-%d-%Y %H:%M:%S")
model = f"This is a dummy model with id: {str(uuid4())} generated at: {curtime}\n"
(Path(args.model_output) / 'model.txt').write_text(model)

```

:::image type="content" source="media/concept-component/component-introduction.png" lightbox="media/concept-component/component-introduction.png" alt-text="Konzeptionelle Dokumentation, die die Zuordnung zwischen Quellcodeelementen und der Benutzeroberfläche der Komponente zeigt." :::

## <a name="create-a-component"></a>Erstellen einer Komponente

### <a name="create-a-component-using-cli-v2"></a>Erstellen einer Komponente mithilfe der CLI (v2)

Nachdem Sie die Komponentenspezifikation und Python-Skriptdateien definiert und [die CLI (v2) erfolgreich installiert](how-to-configure-cli.md) haben, können Sie die Komponente wie folgt in Ihren Arbeitsbereichen erstellen:

```azurecli
az ml component create --file my_component.yml --version 1 --resource-group my-resource-group --workspace-name my-workspace
```

Verwenden Sie `az ml component create --help`, um weitere Informationen zum Befehl `create`zu erhalten.

### <a name="create-a-component-in-the-studio-ui"></a>Erstellen einer Komponente über die Studio-Benutzeroberfläche

Sie können eine Komponente auf der Seite **Komponenten** der Studio-Benutzeroberfläche erstellen.

1. Klicken Sie auf der Komponentenseite auf **Neue Komponente**.

    :::image type="content" source="./media/concept-component/ui-create-component.png" lightbox="./media/concept-component/ui-create-component.png" alt-text="Screenshot: Schaltfläche „Neue Komponente“":::

1. Folgen Sie dem Assistenten, um den Erstellungsvorgang abzuschließen.

## <a name="use-components-to-build-ml-pipelines"></a>Verwenden von Komponenten zum Erstellen ML-Pipelines

Sie können die Azure CLI (v2) verwenden, um einen Pipelineauftrag zu erstellen. Weitere Informationen finden Sie unter [Erstellen und Ausführen von ML-Pipelines (CLI)](how-to-create-component-pipelines-cli.md).

## <a name="manage-components"></a>Verwalten von Komponenten

Sie können Komponentendetails überprüfen und die Komponente mithilfe der CLI (v2) verwalten. Verwenden Sie `az ml component -h`, um ausführliche Anweisungen zum Komponentenbefehl abzurufen.

### <a name="list-components"></a>Auflisten von Komponenten

Sie können `az ml component list` verwenden, um alle Komponenten in einem Arbeitsbereich aufzulisten.

Sie können alle erstellten Komponenten in Ihrem Arbeitsbereich auf der Seite **Komponenten** der Studio-Benutzeroberfläche anzeigen.

### <a name="show-details-for-a-component"></a>Anzeigen von Details für eine Komponente

Sie können `az ml component show --name <COMPONENT_NAME>` verwenden, um die Details einer Komponente anzuzeigen.

Sie können Komponentendetails auch auf der Seite **Komponenten** der Studio-Benutzeroberfläche überprüfen.


### <a name="upgrade-a-component"></a>Durchführen von Upgrades für eine Komponente

Sie können `az ml component create --file <NEW_VERSION.yaml>` verwenden, um ein Upgrade für eine Komponente durchzuführen.

Sie können auch auf der Detailseite der Komponente auf **Upgrade** klicken, um ein Upgrade auf eine neue Version der Komponente durchzuführen.

:::image type="content" source="./media/concept-component/upgrade-component.png" lightbox="./media/concept-component/upgrade-component.png" alt-text="Screenshot: Schaltfläche „Upgrade“":::

### <a name="delete-a-component"></a>Löschen einer Komponente

Sie können `az ml component delete --name <COMPONENT_NAME>` verwenden, um eine Komponente zu löschen. 

Sie können auch eine Komponente auswählen und archivieren.

:::image type="content" source="./media/concept-component/archive-component.png" alt-text="Screenshot: Archivierungsoption für eine Komponente":::

## <a name="next-steps"></a>Nächste Schritte

- [YAML-Referenz der Komponente](reference-yaml-component-command.md)
- [Erstellen und Ausführen von ML-Pipelines (CLI)](how-to-create-component-pipelines-cli.md)
- [Erstellen von Machine Learning-Pipelines im Designer](tutorial-designer-automobile-price-train-score.md)
