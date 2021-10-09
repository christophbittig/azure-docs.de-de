---
title: Zugreifen auf ein Compute-Instanzterminal in Ihrem Arbeitsbereich
titleSuffix: Azure Machine Learning
description: Verwenden Sie das Terminal auf einer Compute-Instanz für Git-Vorgänge, um Pakete zu installieren und Kernel hinzuzufügen.
services: machine-learning
author: abeomor
ms.author: osomorog
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.date: 02/05/2021
ms.openlocfilehash: b4b953c527010cb4ec15f0adf52df6bb3b0d7db0
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128674944"
---
# <a name="access-a-compute-instance-terminal-in-your-workspace"></a>Zugreifen auf ein Compute-Instanzterminal in Ihrem Arbeitsbereich

Greifen Sie auf das Terminal einer Compute-Instanz in Ihrem Arbeitsbereich zu:

* Verwenden Sie Dateien aus Git und Versionsdateien. Diese Dateien werden in Ihrem Arbeitsbereichsdateisystem gespeichert und sind nicht auf eine einzelne Compute-Instanz beschränkt.
* Installieren Sie Pakete auf der Compute-Instanz.
* Erstellen Sie zusätzliche Kernel auf der Compute-Instanz.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
* Ein Machine Learning-Arbeitsbereich. Weitere Informationen finden Sie unter [Erstellen eines Azure Machine Learning-Arbeitsbereichs](how-to-manage-workspace.md).

## <a name="access-a-terminal"></a>Zugreifen auf ein Terminal

So greifen Sie auf das Terminal zu

1. Öffnen Sie Ihren Arbeitsbereich in [Azure Machine Learning Studio](https://ml.azure.com).
1. Wählen Sie auf der linken Seite **Notebooks** aus.
1. Wählen Sie das Bild für **Terminal öffnen** aus.

    :::image type="content" source="media/how-to-use-terminal/open-terminal-window.png" alt-text="Terminalfenster öffnen":::

1. Wenn eine Compute-Instanz ausgeführt wird, wird das Terminalfenster für diese Compute-Instanz angezeigt.
1. Wenn keine Compute-Instanz ausgeführt wird, verwenden Sie den Abschnitt **Compute** auf der rechten Seite, um eine Compute-Instanz zu starten oder zu erstellen.
    :::image type="content" source="media/how-to-use-terminal/start-or-create-compute.png" alt-text="Starten oder Erstellen einer Compute-Instanz":::

Zusätzlich zu den oben genannten Schritten können Sie auch über das Terminal zugreifen:

* RStudio: Wählen Sie die Registerkarte **Terminal** oben links aus.
* Jupyter Lab:  Wählen Sie die Kachel **Terminal** unter der Überschrift **Other** (Andere) auf der Registerkarte „Launcher“ aus.
* Jupyter:  Wählen Sie **Neu > Terminal** oben rechts auf der Registerkarte „Dateien“ aus.
* Wenn Sie beim Erstellen der Compute-Instanz den SSH-Zugriff aktiviert haben, können Sie eine SSH-Verbindung mit dem Computer herstellen.

## <a name="copy-and-paste-in-the-terminal"></a>Kopieren und Einfügen in das Terminal

> * Windows: Drücken Sie `Ctrl-Insert` zum Kopieren und `Ctrl-Shift-v` oder `Shift-Insert` zum Einfügen.
> * Mac OS: Drücken Sie `Cmd-c` zum Kopieren und `Cmd-v` zum Einfügen.
> * FireFox/IE unterstützen die Berechtigungen für die Zwischenablage möglicherweise nicht ordnungsgemäß.

## <a name="use-files-from-git-and-version-files"></a><a name=git></a> Verwenden von Dateien aus Git und Versionsdateien

Greifen Sie über das Terminal auf alle Git-Vorgänge zu. Alle Git-Dateien und -Ordner werden in Ihrem Arbeitsbereichsdateisystem gespeichert. Diese Speicherung ermöglicht es Ihnen, diese Dateien von einer beliebigen Compute-Instanz in Ihrem Arbeitsbereich zu verwenden.

> [!NOTE]
> Fügen Sie Ihre Dateien und Ordner irgendwo unter dem Ordner **~/cloudfiles/code/Users** hinzu, sodass sie in allen Ihren Jupyter-Umgebungen sichtbar sind.

Erfahren Sie mehr über das [Klonen von Git-Repositorys in Ihrem Arbeitsbereichsdateisystem](concept-train-model-git-integration.md#clone-git-repositories-into-your-workspace-file-system).

## <a name="install-packages"></a>Installieren von Paketen

 Installieren Sie Pakete über ein Terminalfenster. Installieren Sie Python-Pakete in der Umgebung **Python 3.8 – AzureML**.  Installieren Sie R-Pakete in der **R**-Umgebung.

Oder Sie können Pakete direkt in einem Jupyter Notebook oder in RStudio installieren:

* Verwenden Sie die Registerkarte **Pakete** unten rechts oder die Registerkarte **Konsole** oben links.  
* Python: Fügen Sie Installationscode hinzu, und führen Sie ihn in einer Zelle im Jupyter-Notebook aus.

> [!NOTE]
> Für die Paketverwaltung innerhalb eines Notebooks verwenden Sie die magischen Funktionen **%pip** oder **%conda**, um Pakete automatisch in den **aktuell aktiven Kernel** zu installieren, anstatt **!pip** oder **!conda**, die sich auf alle Pakete beziehen (einschließlich der Pakete außerhalb des aktuell aktiven Kernels).

## <a name="add-new-kernels"></a>Hinzufügen neuer Kernel

> [!WARNING]
>  Wenn Sie Ihre Compute-Instanz anpassen, sollten Sie darauf achten, die conda-Umgebung **azureml_py36** oder **azureml_py38** nicht zu löschen.  Löschen Sie auch nicht die Kernel **Python 3.6 - AzureML** oder **Python 3.8 - AzureML**. Diese sind für die Funktionalität von Jupyter bzw. JupyterLab erforderlich.

So fügen Sie einen neuen Jupyter-Kernel zur Compute-Instanz hinzu:

1. Verwenden Sie das Terminalfenster, um eine neue Umgebung zu erstellen.  Beispielsweise erstellt der folgende Code `newenv`:

    ```shell
    conda create --name newenv
    ```

1. Aktivieren Sie die Umgebung.  Beispielsweise nach dem Erstellen von `newenv`:

    ```shell
    conda activate newenv
    ```

1. Installieren Sie das pip- und ipykernel-Paket in der neuen Umgebung, und erstellen Sie einen Kernel für diese conda-Umgebung.

    ```shell
    conda install pip
    conda install ipykernel
    python -m ipykernel install --user --name newenv --display-name "Python (newenv)"
    ```

Jeder der [verfügbaren Jupyter-Kernel](https://github.com/jupyter/jupyter/wiki/Jupyter-kernels) kann installiert werden.

## <a name="manage-terminal-sessions"></a>Verwalten von Terminalsitzungen

 Wählen Sie **View active Sessions** (Aktive Sitzungen anzeigen) auf der Terminalsymbolleiste aus, um eine Liste aller aktiven Terminalsitzungen anzuzeigen. Wenn keine aktiven Sitzungen vorhanden sind, wird diese Registerkarte deaktiviert.

Schließen Sie alle nicht verwendeten Sitzungen, um die Ressourcen ihrer Compute-Instanz beizubehalten.
