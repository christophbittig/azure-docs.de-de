---
title: 'Tutorial: Trainieren und Bereitstellen eines Modells: Machine Learning in Azure IoT Edge'
description: In diesem Tutorial trainieren Sie ein Machine Learning-Modell mit Azure Machine Learning und verpacken das Modell dann als Containerimage, das als Azure IoT Edge-Modul bereitgestellt werden kann.
author: kgremban
ms.author: kgremban
ms.date: 3/24/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 7dcc92244e858a475e6ef06ba08b14f0e433dc62
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121726389"
---
# <a name="tutorial-train-and-deploy-an-azure-machine-learning-model"></a>Tutorial: Trainieren und Bereitstellen eines Azure Machine Learning-Modells

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

In diesem Artikel führen Sie die folgenden Aufgaben aus:

* Trainieren eines Machine Learning-Modells mithilfe von Azure Machine Learning Studio
* Packen des trainierten Modells als Containerimage
* Bereitstellen des Containerimages als Azure IoT Edge-Modul

Machine Learning Studio ist eine grundlegende Komponente für das Experimentieren, Trainieren und Bereitstellen von Machine Learning-Modellen.

Normalerweise werden die Schritte in diesem Artikel von Data Scientists ausgeführt.

In diesem Abschnitt des Tutorials lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen von Jupyter-Notebooks in einem Azure Machine Learning-Arbeitsbereich zum Trainieren eines Machine Learning-Modells
> * Containerisieren des trainiertes Machine Learning-Modells
> * Erstellen eines IoT Edge-Moduls auf der Grundlage des containerisierten Machine Learning-Modells

## <a name="prerequisites"></a>Voraussetzungen

Dieser Artikel ist Teil einer Tutorialreihe zur Verwendung von Machine Learning für IoT Edge. Jeder Artikel in der Reihe baut auf der Arbeit im vorherigen Artikel auf. Wenn Sie diesen Artikel direkt aufgerufen haben, wechseln Sie zum [ersten Artikel](tutorial-machine-learning-edge-01-intro.md) in der Reihe.

## <a name="set-up-azure-machine-learning"></a>Einrichten von Azure Machine Learning

Wir verwenden Machine Learning Studio, um die beiden Jupyter-Notebooks und die unterstützenden Dateien zu hosten. Hier erstellen und konfigurieren wir ein Azure Machine Learning-Projekt. Wenn Sie Jupyter oder Machine Learning Studio noch nicht verwendet haben, lesen Sie diese beiden Einführungsdokumente:

* **Jupyter Notebook**: [Arbeiten mit Jupyter-Notebooks in Visual Studio Code](https://code.visualstudio.com/docs/python/jupyter-support)
* **Azure Machine Learning**: [Tutorial: Machen Sie sich mit den ersten Schritten mit Azure Machine Learning in Jupyter-Notebooks vertraut](../machine-learning/quickstart-create-resources.md)

> [!NOTE]
> Sobald der Dienst eingerichtet ist, kann von jedem Computer aus auf Machine Learning zugegriffen werden. Während der Einrichtung sollten Sie den virtuellen Entwicklungscomputer nutzen, auf dem alle benötigten Dateien vorhanden sind.

### <a name="install-azure-machine-learning-visual-studio-code-extension"></a>Installieren der Azure Machine Learning-Erweiterung für Visual Studio Code

Diese Erweiterung für Visual Studio Code muss auf dem virtuellen Entwicklungscomputer installiert sein. Wenn Sie eine andere Instanz ausführen, installieren Sie die Erweiterung erneut, wie unter [Einrichten der Azure Machine Learning-Erweiterung für Visual Studio Code](../machine-learning/how-to-setup-vs-code.md) beschrieben.

### <a name="create-an-azure-machine-learning-account"></a>Erstellen eines Azure Machine Learning-Kontos

Um in Azure Ressourcen bereitzustellen und Workloads auszuführen, melden Sie sich mit Ihren Anmeldeinformationen für Ihr Azure-Konto an.

1. Öffnen Sie in Visual Studio Code die Befehlspalette, indem Sie auf der Menüleiste **Ansicht** > **Befehlspalette** auswählen.

1. Geben Sie in der Befehlspalette den Befehl `Azure: Sign In` ein, um den Anmeldeprozess zu starten. Folgen Sie den Anweisungen, um die Anmeldung abzuschließen.

1. Erstellen Sie eine Machine Learning-Compute-Instanz, um die Workload auszuführen. Geben Sie in der Befehlspalette den Befehl `Azure ML: Create Compute` ein.
1. Wählen Sie Ihr Azure-Abonnement aus.
1. Wählen Sie **+ Neuen Azure ML-Arbeitsbereich erstellen** aus, und geben Sie den Namen **turbofandemo** ein.
1. Wählen Sie die Ressourcengruppe aus, die Sie für diese Demo verwendet haben.
1. Der Status der Arbeitsbereichserstellung wird in der unteren rechten Ecke des Visual Studio Code-Fensters angezeigt: **Arbeitsbereich wird erstellt: turbofandemo**. Dieser Schritt kann einige Minuten dauern.
1. Warten Sie, bis der Arbeitsbereich erstellt wurde. Die Meldung **Azure ML workspace turbofandemo created** (Der Azure ML-Arbeitsbereich 'turbofandemo' wurde erstellt.) sollte angezeigt werden.

### <a name="upload-jupyter-notebook-files"></a>Hochladen von Jupyter Notebook-Dateien

Wir laden Beispiel-Notebook-Dateien in einen neuen Machine Learning-Arbeitsbereich hoch.

1. Wechseln Sie zu ml.azure.com, und melden Sie sich an.
1. Wählen Sie Ihr Microsoft-Verzeichnis, Ihr Azure-Abonnement und den neu erstellten Machine Learning-Arbeitsbereich aus.

    :::image type="content" source="media/tutorial-machine-learning-edge-04-train-model/select-studio-workspace.png" alt-text="Screenshot: Auswählen des Azure Machine Learning-Arbeitsbereichs" :::

1. Nachdem Sie sich beim Machine Learning-Arbeitsbereich angemeldet haben, wechseln Sie über das Menü auf der linken Seite zum Abschnitt **Notebooks**.
1. Wählen Sie die Registerkarte **Eigene Dateien** aus.

1. Wählen Sie **Hochladen** (Pfeil nach oben) aus.

1. Wechseln Sie zu **C:\source\IoTEdgeAndMlSample\AzureNotebooks**. Wählen Sie alle Dateien in der Liste aus, und klicken Sie auf **Öffnen**.

1. Aktivieren Sie das Kontrollkästchen **Ich vertraue dem Inhalt dieser Dateien**.

1. Wählen Sie **Hochladen** aus, um mit dem Upload zu beginnen. Wenn der Prozess abgeschlossen ist, wählen Sie **Fertig** aus.

### <a name="jupyter-notebook-files"></a>Jupyter Notebook-Dateien

Sehen wir uns die Dateien einmal genauer an, die Sie in Ihren Machine Learning-Arbeitsbereich hochgeladen haben. Die Aktivitäten in diesem Teil des Tutorials umfassen zwei Notebook-Dateien, die einige unterstützende Dateien verwenden.

* **01-turbofan\_regression.ipynb**: Dieses Notebook verwendet den Machine Learning-Arbeitsbereich zum Erstellen und Ausführen eines Machine Learning-Experiments. Mit dem Notebook werden im Allgemeinen die folgenden Schritte ausgeführt:

  1. Herunterladen von Daten aus dem Azure Storage-Konto, das von der Geräteumgebung erstellt wurde
  1. Untersucht die Daten, bereitet sie vor und verwendet sie dann zum Trainieren des Klassifizierungsmodells.
  1. Bewertet das Modell aus dem Experiment mithilfe eines Testdatasets (Test\_FD003.txt).
  1. Veröffentlicht das beste Klassifizierungsmodell im Machine Learning-Arbeitsbereich.

* **02-turbofan\_deploy\_model.ipynb**: Dieses Notebook verwendet das im vorherigen Notebook erstellte Modell für die Erstellung eines Containerimages, das für die Bereitstellung auf einem IoT Edge-Gerät bereit ist. Mit diesem Notebook werden die folgenden Schritte ausgeführt:

  1. Erstellen eines Bewertungsskripts für das Modell
  1. Erstellt ein Containerimage mithilfe des Klassifizierungsmodells, das im Machine Learning-Arbeitsbereich gespeichert wurde.
  1. Stellt das Image als Webdienst in Azure Container Instances bereit.
  1. Verwenden des Webdiensts zum Überprüfen, ob das Modell und das Image wie erwartet funktionieren Das überprüfte Image wird im Teil [Erstellen und Bereitstellen von benutzerdefinierten IoT Edge-Modulen](tutorial-machine-learning-edge-06-custom-modules.md) dieses Tutorials auf unserem IoT Edge-Gerät bereitgestellt.

* **Test\_FD003.txt**: Diese Datei enthält die Daten, die wir beim Überprüfen unseres trainierten Klassifizierers als Testsatz verwenden. Der Einfachheit halber haben wir die Testdaten so genutzt, wie sie ursprünglich bereitgestellt wurden.
* **RUL\_FD003.txt**: Diese Datei enthält die verbleibende Nutzungsdauer (Remaining Useful Life, RUL) für den letzten Zyklus jedes Geräts in der Datei „Test\_FD003.txt“. Die Dateien „readme.txt“ und „Damage Propagation Modeling.pdf“ unter „C:\\source\\IoTEdgeAndMlSample\\data\\Turbofan“ enthalten eine ausführliche Beschreibung der Daten.
* **Utils.py**: Enthält eine Reihe von Python-Hilfsfunktionen für die Arbeit mit Daten. Das erste Notebook enthält eine ausführliche Beschreibung der Funktionen.
* **README.md**: Diese Datei beschreibt die Verwendung der Notebooks.

## <a name="run-the-jupyter-notebooks"></a>Ausführen der Jupyter-Notebooks

Der Arbeitsbereich wurde erstellt, und Sie können nun die Notebooks ausführen.

1. Wählen Sie **01-turbofan\_regression.ipynb** auf der Seite **Eigene Dateien** aus.

    :::image type="content" source="media/tutorial-machine-learning-edge-04-train-model/select-turbofan-notebook.png" alt-text="Screenshot: Auswählen des ersten Notebooks für die Ausführung":::

1. Falls das Notebook als **Nicht vertrauenswürdig** aufgeführt ist, wählen Sie oben rechts im Notebook das Widget **Nicht vertrauenswürdig** aus. Wenn das Dialogfeld angezeigt wird, wählen Sie **Vertrauen** aus.

1. Lesen Sie die Dokumentation für jede Zelle, und führen Sie sie einzeln aus, um optimale Ergebnisse zu erzielen. Wählen Sie auf der Symbolleiste **Ausführen** aus. Später ist es zweckdienlich, mehrere Zellen auszuführen. Sie können Upgrade- und Ablaufwarnungen ignorieren.

    Wenn eine Zelle ausgeführt wird, wird dafür ein Sternchen zwischen eckigen Klammern angezeigt ([\*]). Wenn der Vorgang für die Zelle abgeschlossen ist, wird das Sternchen durch eine Zahl ersetzt und ggf. die relevante Ausgabe angezeigt. Die Zellen in einem Notebook werden nacheinander erstellt, und es kann jeweils nur eine Zelle ausgeführt werden.

    Sie können auch Ausführungsoptionen im Menü **Zelle** verwenden. Drücken Sie **STR+EINGABETASTE**, um eine Zelle auszuführen, und **UMSCHALTTASTE+EINGABETASTE**, um eine Zelle auszuführen und mit der nächsten Zelle fortzufahren.

    > [!TIP]
    > Führen Sie das gleiche Notebook nicht auf mehreren Tabs im Browser aus, um konsistente Zellvorgänge zu gewährleisten.

1. Geben Sie die Werte für Ihr Azure-Abonnement, die Einstellungen und die Ressourcen in die Zelle nach den Anweisungen **Globale Eigenschaften festlegen** ein. Führen Sie anschließend die Zelle aus.

    ![Screenshot: Festlegen globaler Eigenschaften im Notebook](media/tutorial-machine-learning-edge-04-train-model/set-global-properties.png)

1. Suchen Sie nach der Ausführung in der Zelle vor den **Details zum Arbeitsbereich** nach dem Link, der Sie anweist, sich zur Authentifizierung anzumelden.

    ![Screenshot: Anmeldeaufforderung für die Geräteauthentifizierung](media/tutorial-machine-learning-edge-04-train-model/sign-in-prompt.png)

    Öffnen Sie den Link, und geben Sie den angegebenen Code ein. Dieses Anmeldeverfahren authentifiziert das Jupyter-Notebook für den Zugriff auf Azure-Ressourcen unter Verwendung der plattformübergreifenden Microsoft Azure-Befehlszeilenschnittstelle.

    ![Screenshot: Authentifizierung der Anwendung nach Gerätebestätigung](media/tutorial-machine-learning-edge-04-train-model/cross-platform-cli.png)

1. Kopieren Sie in der Zelle vor **Untersuchen der Ergebnisse** den Wert der Ausführungs-ID, und fügen Sie ihn für die Ausführungs-ID in die Zelle nach **Wiederherstellen einer Ausführung** ein.

   ![Screenshot: Kopieren der Ausführungs-ID zwischen Zellen](media/tutorial-machine-learning-edge-04-train-model/automl-id.png)

1. Führen Sie die übrigen Zellen im Notebook aus.

1. Speichern Sie das Notebook, und kehren Sie zur Projektseite zurück.

1. Öffnen Sie **02-turbofan\_deploy\_model.ipynb**, und führen Sie die einzelnen Zellen aus. Zur Authentifizierung müssen Sie sich in der Zelle nach **Konfigurieren des Arbeitsbereichs** anmelden.

1. Speichern Sie das Notebook, und kehren Sie zur Projektseite zurück.

### <a name="verify-success"></a>Überprüfen des erfolgreichen Abschlusses

Überprüfen Sie, ob Elemente erstellt wurden, um sich zu vergewissern, dass die Notebooks erfolgreich abgeschlossen wurden.

1. Klicken Sie auf der Registerkarte **Meine Dateien** des Machine Learning-Notebooks auf **Aktualisieren**.

1. Überprüfen Sie, ob die folgenden Dateien erstellt wurden.

    | Datei | BESCHREIBUNG |
    | --- | --- |
    | ./aml_config/.azureml/config.json | Die zum Erstellen des Machine Learning-Arbeitsbereichs verwendete Konfigurationsdatei. |
    | ./aml_config/model_config.json | Die Konfigurationsdatei, die Sie zum Bereitstellen des Modells im Machine Learning-Arbeitsbereich **turbofanDemo** in Azure benötigen. |
    | myenv.yml| Diese Datei enthält Informationen zu den Abhängigkeiten für das bereitgestellte Machine Learning-Modell.|

1. Überprüfen Sie, ob die folgenden Azure-Ressourcen erstellt wurden. An einige Ressourcennamen werden zufällig gewählte Zeichen angefügt.

    | Azure-Ressource | Name |
    | --- | --- |
    | Azure Machine Learning-Arbeitsbereich | turborfanDemo |
    | Azure Container Registry | turbofandemoxxxxxxxx |
    | Application Insights | turbofaninsightxxxxxxxx |
    | Azure-Schlüsseltresor | turbofankeyvaultbxxxxxxxx |
    | Azure Storage | turbofanstoragexxxxxxxxx |

### <a name="debugging"></a>Debuggen

Sie können Python-Anweisungen zum Debuggen in das Notebook einfügen, etwa den Befehl `print()` zum Anzeigen von Werten. Wenn nicht definierte Variablen oder Objekte angezeigt werden, führen Sie die Zellen aus, in denen sie zum ersten Mal deklariert oder instanziiert werden.

Möglicherweise müssen Sie zuvor erstellte Dateien und Azure-Ressourcen löschen, falls Sie die Notebooks wiederholen müssen.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Dieses Tutorial ist Teil einer Reihe, in der jeder Artikel auf den Schritten aufbaut, die jeweils im vorherigen Artikel ausgeführt wurden. Warten Sie mit dem Bereinigen von Ressourcen, bis Sie das letzte Tutorial abgeschlossen haben.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben wir zwei in Machine Learning Studio ausgeführte Jupyter-Notebooks verwendet, um mithilfe der Daten aus den Triebwerksgeräten folgende Aktionen durchzuführen:

- Trainieren eines RUL-Klassifizierers
- Speichern des Klassifizierers als Modell
- Erstellen eines Containerimages
- Bereitstellen und Testen des Images als Webdienst

Fahren Sie mit dem nächsten Artikel fort, um ein IoT Edge-Gerät zu erstellen.

> [!div class="nextstepaction"]
> [Konfigurieren eines IoT Edge-Geräts](tutorial-machine-learning-edge-05-configure-edge-device.md)