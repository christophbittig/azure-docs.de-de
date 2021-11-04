---
title: Herstellen einer Verbindung mit einer Compute-Instanz in Visual Studio Code (Vorschau)
titleSuffix: Azure Machine Learning
description: Hier erfahren Sie, wie Sie eine Verbindung mit einer Azure Machine Learning-Computeinstanz in Visual Studio Code herstellen, um interaktive Jupyter Notebook- und Remoteentwicklungsworkloads auszuführen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: luquinta
author: luisquintanilla
ms.date: 10/21/2021
ms.openlocfilehash: 11d9e42d2a54caa723fc7e3add7ebfb9d236048d
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131558400"
---
# <a name="connect-to-an-azure-machine-learning-compute-instance-in-visual-studio-code-preview"></a>Herstellen einer Verbindung mit einer Azure Machine Learning-Compute-Instanz in Visual Studio Code (Vorschau)

In diesem Artikel erfahren Sie, wie Sie mithilfe von Visual Studio Code eine Verbindung mit einer Azure Machine Learning-Compute-Instanz herstellen.

Eine [Azure Machine Learning Compute-Instanz](concept-compute-instance.md) ist eine vollständig verwaltete cloudbasierte Arbeitsstation für Datenanalysten und stellt Verwaltungs- und Unternehmensbereitschaftsfunktionen für IT-Administratoren bereit.

Es gibt zwei Möglichkeiten, wie Sie von Visual Studio Code aus eine Verbindung mit einer Compute-Instanz herstellen können:

* Remotecompute-Instanz. Diese Option bietet Ihnen eine komplett ausgestattete Entwicklungsumgebung zum Erstellen Ihrer Projekte für maschinelles Lernen.
* Über einen Jupyter Notebook-Remoteserver. Mit dieser Option können Sie eine Compute-Instanz als Jupyter Notebook-Remoteserver konfigurieren.

## <a name="configure-a-remote-compute-instance"></a>Konfigurieren einer Remotecompute-Instanz

Um eine Remotecompute-Instanz für die Entwicklung zu konfigurieren, sind ein paar Voraussetzungen erforderlich.

* Die Azure Machine Learning-Erweiterung für Visual Studio Code. Weitere Informationen finden Sie in der Anleitung zur Einrichtung der [Azure Machine Learning-Erweiterung für Visual Studio Code](how-to-setup-vs-code.md).
* Einen Azure Machine Learning-Arbeitsbereich. [Verwenden Sie die Azure Machine Learning-Erweiterung für Visual Studio Code, um einen neuen Arbeitsbereich zu erstellen](how-to-manage-resources-vscode.md#create-a-workspace), wenn Sie noch nicht über einen verfügen.
* Azure Machine Learning-Compute-Instanz. [Verwenden Sie die Azure Machine Learning-Erweiterung für Visual Studio Code, um eine neue Compute-Instanz zu erstellen](how-to-manage-resources-vscode.md#create-compute-instance), wenn Sie keine besitzen.

> [!IMPORTANT]
> Informationen zum Herstellen einer Verbindung mit einer Compute-Instanz hinter einer Firewall finden Sie unter [Verwenden des Arbeitsbereichs hinter einer Firewall für Azure Machine Learning](how-to-access-azureml-behind-firewall.md#visual-studio-code-hosts).

So stellen Sie eine Verbindung mit Ihrer Remotecompute-Instanz her

# <a name="studio"></a>[Studio](#tab/studio)

Navigieren Sie zu [ml.azure.com](https://ml.azure.com).

> [!IMPORTANT]
> Um von Visual Studio Code aus eine Verbindung mit Ihrer Remotecompute-Instanz herzustellen, stellen Sie sicher, dass das Konto, mit dem Sie in Azure Machine Learning Studio angemeldet sind, dasselbe ist, das Sie in Visual Studio Code verwenden.

### <a name="compute"></a>Compute

1. Wählen Sie die Registerkarte **Compute** aus.
1. Wählen Sie in der Spalte *Application URI* (Anwendungs-URI) die Option **VS Code** für die Compute-Instanz aus, mit der Sie eine Verbindung herstellen möchten.

:::image type="content" source="media/how-to-set-up-vs-code-remote/studio-compute-instance-vs-code-launch.png" alt-text="Herstellen einer Verbindung mit einer Compute-Instanz: VS Code Azure ML Studio" lightbox="media/how-to-set-up-vs-code-remote/studio-compute-instance-vs-code-launch.png":::

### <a name="notebook"></a>Notebook

1. Wählen Sie die Registerkarte **Notebook** aus.
1. Wählen Sie auf der Registerkarte *Notebook* die zu bearbeitende Datei aus.
1. Wählen Sie **Editors > Edit in VS Code (Vorschau)** (Editoren > In VS Code bearbeiten) aus.

:::image type="content" source="media/how-to-set-up-vs-code-remote/studio-notebook-compute-instance-vs-code-launch.png" alt-text="Herstellen einer Verbindung mit einer Compute-Instanz: VS Code Azure ML Notebook" lightbox="media/how-to-set-up-vs-code-remote/studio-notebook-compute-instance-vs-code-launch.png":::

# <a name="vs-code"></a>[VS-Code](#tab/extension)

### <a name="azure-machine-learning-extension"></a>Azure Machine Learning-Erweiterung

1. Starten Sie in VS Code die Azure Machine Learning-Erweiterung.
1. Erweitern Sie den Knoten **Compute-Instanzen** in Ihrer Erweiterung.
1. Klicken Sie mit der rechten Maustaste auf die Compute-Instanz, mit der Sie eine Verbindung herstellen möchten, und wählen Sie **Connect to Compute Instance** (Mit Compute-Instanz verbinden) aus.

:::image type="content" source="media/how-to-set-up-vs-code-remote/vs-code-compute-instance-launch.png" alt-text="Herstellen einer Verbindung mit einer Compute-Instanz: Azure ML-Erweiterung von Visual Studio Code" lightbox="media/how-to-set-up-vs-code-remote/vs-code-compute-instance-launch.png":::

### <a name="command-palette"></a>Befehlspalette

1. Öffnen Sie in VS Code die Befehlspalette, indem Sie **Ansicht > Befehlspalette** auswählen.
1. Geben Sie in das Textfeld **Azure ML: Connect to Compute Instance** (Mit Compute-Instanz verbinden) ein.
1. Wählen Sie Ihr Abonnement aus.
1. Wählen Sie Ihren Arbeitsbereich aus.
1. Wählen Sie Ihre Compute-Instanz aus oder erstellen Sie eine neue.

---

Es wird ein neues Fenster für Ihre Remotecompute-Instanz geöffnet. Wenn Sie versuchen, eine Verbindung mit einer Remotecompute-Instanz herzustellen, werden die folgenden Aufgaben ausgeführt:

1. Autorisierung Es werden einige Prüfungen durchgeführt, um sicherzustellen, dass der Benutzer, der versucht, eine Verbindung herzustellen, zur Verwendung der Compute-Instanz berechtigt ist.
1. VS Code Remote Server ist auf der Compute-Instanz installiert.
1. Für die Interaktion in Echtzeit wird eine WebSocket-Verbindung hergestellt.

Sobald die Verbindung hergestellt ist, wird sie aufrechterhalten. Zu Beginn der Sitzung wird ein Token ausgegeben, das automatisch aktualisiert wird, um die Verbindung mit Ihrer Compute-Instanz aufrechtzuerhalten.

Nachdem Sie eine Verbindung mit Ihrer Remotecompute-Instanz hergestellt haben, verwenden Sie den Editor, um Folgendes auszuführen:

* [Erstellen und verwalten Sie Dateien auf Ihrer Remotecompute-Instanz oder Dateifreigabe](https://code.visualstudio.com/docs/editor/codebasics).
* Verwenden Sie das [integrierte VS Code-Terminal](https://code.visualstudio.com/docs/editor/integrated-terminal), um [Befehle und Anwendungen auf Ihrer Remotecompute-Instanz auszuführen](how-to-access-terminal.md).
* [Debuggen von Skripts und Anwendungen](https://code.visualstudio.com/Docs/editor/debugging)
* [Verwenden von VS Code zum Verwalten Ihrer Git-Repositorys](concept-train-model-git-integration.md)

## <a name="configure-compute-instance-as-remote-notebook-server"></a>Konfigurieren der Compute-Instanz als Jupyter Notebook-Remoteserver

Sie benötigen folgende Voraussetzungen, um eine Compute-Instanz als Jupyter Notebook-Remoteserver konfigurieren zu können:

* Die Azure Machine Learning-Erweiterung für Visual Studio Code. Weitere Informationen finden Sie in der Anleitung zur Einrichtung der [Azure Machine Learning-Erweiterung für Visual Studio Code](how-to-setup-vs-code.md).
* Einen Azure Machine Learning-Arbeitsbereich. [Verwenden Sie die Azure Machine Learning-Erweiterung für Visual Studio Code, um einen neuen Arbeitsbereich zu erstellen](how-to-manage-resources-vscode.md#create-a-workspace), wenn Sie noch nicht über einen verfügen.

So stellen Sie eine Verbindung mit einer Compute-Instanz her:

1. Öffnen Sie eine Jupyter Notebook-Instanz in Visual Studio Code.
1. Wählen Sie **Jupyter-Server** aus, wenn das integrierte Notebook geladen wird.

    > [!div class="mx-imgBorder"]
    > ![Screenshot: Jupyter Notebook-Remoteserver](media/how-to-set-up-vs-code-remote/launch-server-selection-dropdown.png)

    Alternativ können Sie auch die Befehlspalette verwenden:

    1. Öffnen Sie hierzu die Befehlspalette, indem Sie in der Menüleiste **Ansicht > Befehlspalette** auswählen.
    1. Geben Sie `Azure ML: Connect to Compute instance Jupyter server` in das Textfeld ein.

1. Wählen Sie `Azure ML Compute Instances` aus der Liste der Optionen für den Jupyter-Server aus.
1. Wählen Sie in der Liste Ihr Abonnement aus. Wenn Sie bereits Ihren Azure Machine Learning-Standardarbeitsbereich konfiguriert haben, wird dieser Schritt übersprungen.
1. Wählen Sie Ihren Arbeitsbereich aus.
1. Wählen Sie Ihre Compute-Instanz aus der Liste aus. Wenn Sie noch keine haben, klicken Sie auf **Create new Azure ML Compute Instance** (Neue Azure ML Compute-Instanz) erstellen, und befolgen Sie die Anweisungen, um eine Instanz zu erstellen.
1. Sie müssen Visual Studio Code erneut laden, damit die Änderungen wirksam werden.
1. Öffnen Sie ein Jupyter Notebook, und führen Sie eine Zelle aus.

> [!IMPORTANT]
> Sie **müssen** eine Zelle ausführen, um die Verbindung herzustellen.

Nun können Sie weitere Zellen in Ihrem Jupyter Notebook ausführen.

> [!TIP]
> Sie können auch mit Python-Skriptdateien (.py) arbeiten, die Jupyter-ähnliche Codezellen enthalten. Weitere Informationen finden Sie in der [Visual Studio Code-Dokumentation zu Python Interactive](https://code.visualstudio.com/docs/python/jupyter-support-py).

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie die Visual Studio Code-Remoteerweiterung eingerichtet haben, können Sie eine Compute-Instanz als Remotecompute-Instanz aus Visual Studio Code verwenden, um den [Code interaktiv zu debuggen](how-to-debug-visual-studio-code.md).

[Tutorial: Trainieren Ihres ersten ML-Modells](tutorial-1st-experiment-sdk-train.md) zeigt, wie eine Compute-Instanz mit einem integrierten Notebook verwendet wird.
