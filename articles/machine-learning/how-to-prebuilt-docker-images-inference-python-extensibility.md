---
title: Python-Erweiterbarkeit für vordefinierte Docker-Images
titleSuffix: Azure Machine Learning
description: Erweitern von vordefinierten Docker-Images mit einer Erweiterbarkeitslösung für Python-Pakete
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
ms.author: ssambare
author: shivanissambare
ms.date: 10/21/2021
ms.topic: how-to
ms.reviewer: larryfr
ms.custom: deploy, docker, prebuilt
ms.openlocfilehash: 037de82a9123014151cc26013550348459b57ca7
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131561497"
---
# <a name="python-package-extensibility-for-prebuilt-docker-images-preview"></a>Erweiterbarkeit von Python-Paketen für vordefinierte Docker-Images (Vorschau)

Die [vordefinierten Docker-Images für Modellrückschlüsse](concept-prebuilt-docker-images-inference.md) enthalten Pakete für gängige Machine Learning-Frameworks. Es gibt zwei Methoden, die zum Hinzufügen von Python-Paketen __ohne Neuerstellung des Docker-Images__ verwendet werden können:

* [Dynamische Installation](#dynamic): Bei diesem Ansatz wird eine Datei mit [Anforderungen](https://pip.pypa.io/en/stable/cli/pip_install/#requirements-file-format) genutzt, um Python-Pakete automatisch wiederherzustellen, wenn der Docker-Container gestartet wird.

    Sie können die Verwendung dieser Methode für die __schnelle Prototyperstellung__ erwägen. Beim Starten des Images werden Pakete mit der Datei `requirements.txt` wiederhergestellt. Bei dieser Methode erhöht sich die Startdauer des Images, und Sie müssen länger warten, bis von der Bereitstellung Anforderungen verarbeitet werden können.

* [Vorab installierte Python-Pakete](#preinstalled): Sie geben ein Verzeichnis mit vorab installierten Python-Paketen an. Während der Bereitstellung wird dieses Verzeichnis in den Container eingebunden, damit es von Ihrem Eingabeskript (`score.py`) genutzt werden kann.

    Verwenden Sie diesen Ansatz für __Bereitstellungen in der Produktion__. Da das Verzeichnis mit den Paketen in das Image eingebunden ist, kann es auch dann verwendet werden, wenn Ihre Bereitstellungen nicht über Zugriff auf das öffentliche Internet verfügen. Ein Beispiel hierfür ist die Bereitstellung in einem geschützten virtuellen Azure-Netzwerk.

> [!IMPORTANT]
> Die Nutzung der Erweiterbarkeit von Python-Paketen für vorgefertigte Docker-Images mit Azure Machine Learning ist derzeit in der Vorschau. Die Vorschaufunktionalität wird „wie besehen“ zur Verfügung gestellt, ohne Garantien hinsichtlich Support oder Vereinbarung zum Servicelevel (Service Level Agreement, SLA). Weitere Informationen finden Sie in den [zusätzlichen Nutzungsbedingungen für Microsoft Azure-Vorschauversionen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure Machine Learning-Arbeitsbereich. Ein Tutorial zur Erstellung eines Arbeitsbereichs finden Sie unter [Erste Schritte mit Azure Machine Learning](quickstart-create-resources.md).
* Kenntnisse in Bezug auf die Verwendung von Azure Machine Learning-[Umgebungen](how-to-use-environments.md).
* Kenntnisse in Bezug auf das [Bereitstellen von Modellen mit dem Azure Machine Learning-Dienst](how-to-deploy-and-where.md).

<a id="dynamic"></a>

## <a name="dynamic-installation"></a>Dynamische Installation

Bei diesem Ansatz wird eine Datei mit [Anforderungen](https://pip.pypa.io/en/stable/cli/pip_install/#requirements-file-format) verwendet, um Python-Pakete automatisch wiederherzustellen, wenn das Image gestartet wird.

Führen Sie die folgenden Schritte aus, um Ihr vordefiniertes Docker-Containerimage über die Datei „requirements.txt“ zu erweitern:

1. Erstellen Sie die Datei `requirements.txt` zusätzlich zu Ihrem Skript `score.py`.
2. Fügen Sie **alle** erforderlichen Pakete der Datei `requirements.txt` hinzu.
3. Legen Sie die Umgebungsvariable `AZUREML_EXTRA_REQUIREMENTS_TXT` in Ihrer Azure Machine Learning-[Umgebung](how-to-use-environments.md) auf den Speicherort der Datei `requirements.txt` fest.

Nach der Bereitstellung werden die Pakete automatisch für Ihr Bewertungsskript wiederhergestellt.

> [!TIP]
> Auch für die Prototyperstellung empfehlen wir Ihnen, die einzelnen Paketversionen in der Datei `requirements.txt` anzuheften.
> Verwenden Sie beispielsweise nicht nur `scipy`, sondern `scipy == 1.2.3` oder auch `scipy > 1.2.3`.
> Wenn Sie keine genaue Versionsangabe anheften und für `scipy` eine neue Version veröffentlicht wird, kann dies zu einer Beschädigung Ihres Bewertungsskripts und zu Fehlern während der Bereitstellung und Skalierung führen.

Im folgenden Beispiel wird das Festlegen der Umgebungsvariablen `AZUREML_EXTRA_REQUIRMENTS_TXT` veranschaulicht:

```python
from azureml.core import Environment
from azureml.core.conda_dependencies import CondaDependencies 

myenv = Environment(name="my_azureml_env")
myenv.docker.enabled = True
myenv.docker.base_image = <MCR-path>
myenv.python.user_managed_dependencies = True

myenv.environment_variables = {
    "AZUREML_EXTRA_REQUIREMENTS_TXT": "requirements.txt"
}
```

Das folgende Diagramm enthält eine grafische Darstellung des Prozesses bei der dynamischen Installation:

:::image type="content" source="./media/how-to-prebuilt-docker-images-inference-python-extensibility/dynamic-install-python-extend.svg" alt-text="Diagramm: Prozess bei der dynamischen Installation":::

<a id="preinstalled"></a>

## <a name="pre-installed-python-packages"></a>Vorab installierte Python-Pakete

Bei diesem Ansatz wird ein von Ihnen bereitgestelltes Verzeichnis in das Image eingebunden. Die Python-Pakete aus diesem Verzeichnis können dann vom Eingabeskript (`score.py`) verwendet werden.

Führen Sie die folgenden Schritte aus, um Ihr vordefiniertes Docker-Containerimage um vorab installierte Python-Pakete zu erweitern:

> [!IMPORTANT]
> Sie müssen Pakete verwenden, die mit Python 3.7 kompatibel sind. Alle aktuellen Images sind an Python 3.7 angeheftet.

1. Erstellen Sie mit [virtualenv](https://virtualenv.pypa.io/) eine virtuelle Umgebung.

1. Installieren Sie Ihre Abhängigkeiten. Wenn Sie beispielsweise über eine Liste mit Abhängigkeiten in der Datei `requirements.txt` verfügen, können Sie sie für die Installation mit `pip install -r requirements.txt` nutzen (oder nur `pip install` für einzelne Abhängigkeiten).

1. Achten Sie beim Angeben der Umgebungsvariablen `AZUREML_EXTRA_PYTHON_LIB_PATH` darauf, dass Sie auf das richtige Standortpaketverzeichnis verweisen, da es je nach Umgebungsname und Python-Version variiert. Mit dem folgenden Code wird das Festlegen des Pfads für eine virtuelle Umgebung mit dem Namen `myenv` und Python 3.7 veranschaulicht:


    ```python
    from azureml.core import Environment
    from azureml.core.conda_dependencies import CondaDependencies 

    myenv = Environment(name='my_azureml_env')
    myenv.docker.enabled = True
    myenv.docker.base_image = <MCR-path>
    myenv.python.user_managed_dependencies = True

    myenv.environment_variables = {
        "AZUREML_EXTRA_PYTHON_LIB_PATH": "myenv/lib/python3.7/site-packages"
    }
    ```

Das folgende Diagramm enthält eine grafische Darstellung des Prozesses mit vorab installierten Paketen:

:::image type="content" source="./media/how-to-prebuilt-docker-images-inference-python-extensibility/pre-install-python-extend.svg" alt-text="Diagramm: Prozess mit vorab installierten Paketen":::

### <a name="common-problems"></a>Häufige Probleme

Die Einbindungslösung funktioniert nur, wenn Ihr Verzeichnis `myenv` mit den Standortpaketen Ihre gesamten Abhängigkeiten enthält. Falls für Ihre lokale Umgebung Abhängigkeiten verwendet werden, die an einem anderen Standort installiert sind, sind sie nicht im Image verfügbar.

Hier sind einige Gründe angegeben, die zu diesem Problem führen können:

* Mit `virtualenv` wird standardmäßig eine isolierte Umgebung erstellt. Nachdem Sie die virtuelle Umgebung aktiviert haben, __können keine globalen Abhängigkeiten mehr verwendet werden__.
* Falls bei Ihnen eine Umgebungsvariable `PYTHONPATH` auf Ihre globalen Abhängigkeiten verweist, __kann dies zu einer Beeinträchtigung Ihrer virtuellen Umgebung führen__. Führen Sie `pip list` und `pip freeze` nach dem Aktivieren Ihrer Umgebung aus, um sicherzustellen, dass diese keine unerwünschten Abhängigkeiten enthält.
* Für __Conda- und `virtualenv`-Umgebungen können sich Beeinträchtigungen ergeben__. Stellen Sie sicher, dass nicht gleichzeitig eine [Conda-Umgebung](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html) und `virtualenv` verwendet werden.

## <a name="limitations"></a>Einschränkungen

### <a name="modelpackage"></a>Model.package()

* Mit der [Model.package()](/python/api/azureml-core/azureml.core.model(class))-Methode können Sie ein Modellpaket in Form eines Docker-Images oder Dockerfile-Buildkontexts erstellen. Beim Verwenden von „Model.package()“ mit vordefinierten Docker-Images für Rückschlüsse wird eine zwischengeschaltete Imageerstellung ausgelöst, bei der der reguläre Benutzer in einen root-Benutzer geändert wird.

* Wir empfehlen Ihnen, unsere Lösungen für die Erweiterbarkeit von Python-Paketen zu verwenden. Falls andere Abhängigkeiten erforderlich sind (z. B. `apt`-Pakete), sollten Sie ein eigenes [Dockerfile zur Erweiterung des Rückschlussimages](how-to-extend-prebuilt-docker-image-inference.md#buildmodel) erstellen.

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

* Muss der Dateiname beim Erweiterbarkeitsansatz mit „requirements.txt“ unbedingt `requirements.txt` lauten?

        
    ```python
    myenv.environment_variables = {
        "AZUREML_EXTRA_REQUIREMENTS_TXT": "name of your pip requirements file goes here"
    }
    ```

* Können Sie zusammenfassen, welche Unterschiede zwischen dem Ansatz mit `requirements.txt` und dem *Einbindungsansatz* bestehen?

    Verwenden Sie den Ansatz mit *requirements.txt*, um mit der Prototyperstellung zu beginnen.
    Wenn Sie nach einigen Iterationen sicher sind, welche Pakete (und Versionen) Sie für eine erfolgreiche Modellbereitstellung benötigen, sollten Sie zur *Einbindungslösung* wechseln.
        
    Hier sind ausführliche Vergleichsinformationen angegeben.

    | Verglichenes Element | requirements.txt (dynamische Installation) | Paketeinbindung |
    | ----- | ----- | ------ |
    | Lösung  | Erstellen Sie die Datei `requirements.txt`, mit der die angegebenen Pakete installiert werden, wenn der Container gestartet wird. | Erstellen Sie eine lokale Python-Umgebung mit allen Abhängigkeiten. Binden Sie dieses Verzeichnis zur Laufzeit in den Container ein. |
    | Paketinstallation           | Keine zusätzliche Installation (vorausgesetzt, pip ist bereits installiert)                                                                                                          | Installation einer virtuellen Umgebung oder Conda-Umgebung.                                                                                   |
    | Einrichtung der virtuellen Umgebung              | Es ist keine zusätzliche Einrichtung der virtuellen Umgebung erforderlich, da Benutzer die aktuelle lokale Benutzerumgebung bei Bedarf mit „pip freeze“ pullen können, um die Datei `requirements.txt` zu erstellen. | Sie müssen eine bereinigte virtuelle Umgebung einrichten. Je nach aktueller lokaler Benutzerumgebung müssen ggf. noch weitere Schritte ausgeführt werden.                        |
    | [Debuggen](how-to-inference-server-http.md)                 | Die Einrichtung und das Debuggen des Servers sind einfach, da die Abhängigkeiten eindeutig aufgelistet sind. | Eine nicht bereinigte virtuelle Umgebung kann zu Problemen beim Debuggen des Servers führen. Beispielsweise ist es unter Umständen nicht eindeutig, ob Fehler aus der Umgebung oder aus dem Benutzercode stammen. |
    | Konsistenz beim Aufskalieren | Hierbei besteht keine Konsistenz, da eine Abhängigkeit von externen PyPi-Paketen und dem Anheften der Abhängigkeiten durch Benutzer besteht. Es kann sein, dass diese externen Downloads unzuverlässig sind.                                 | Basiert ausschließlich auf der Benutzerumgebung, sodass keine Konsistenzprobleme bestehen.                                                                             |

* Warum befinden sich meine Datei `requirements.txt` und das Verzeichnis mit den eingebundenen Abhängigkeiten nicht im Container?

    Überprüfen Sie lokal, ob die Umgebungsvariablen richtig festgelegt sind. Überprüfen Sie anschließend, ob die angegebenen Pfade fehlerfrei und vorhanden sind.
    Überprüfen Sie, ob Sie Ihr Quellverzeichnis im Konstruktor für die Rückschlusskonfiguration ([inference config](/python/api/azureml-core/azureml.core.model.inferenceconfig#constructor)) richtig festgelegt haben.

* Kann ich Python-Paketabhängigkeiten im vordefinierten Docker-Rückschlussimage außer Kraft setzen?

    Ja. Wenn Sie eine andere Version des Python-Pakets verwenden möchten, die bereits in einem Rückschlussimage installiert ist, wird Ihre Version von unserer Erweiterbarkeitslösung berücksichtigt. Stellen Sie sicher, dass keine Konflikte zwischen den beiden Versionen bestehen.

## <a name="best-practices"></a>Bewährte Methoden

* Weitere Informationen finden Sie in der Dokumentation unter [Laden registrierter Modelle](how-to-deploy-advanced-entry-script.md#load-registered-models). Binden Sie beim Registrieren eines Modellverzeichnisses nicht Ihr Bewertungsskript, Ihr Verzeichnis mit den eingebundenen Abhängigkeiten oder die darin enthaltene Datei `requirements.txt` ein.


* Weitere Informationen zum Laden eines registrierten oder lokalen Modells finden Sie im Artikel zum [Bereitstellen eines Modells](how-to-deploy-and-where.md?tabs=azcli#define-a-dummy-entry-script).

## <a name="bug-fixes"></a>Fehlerbehebungen

### <a name="2021-07-26"></a>26.07.2021

* `AZUREML_EXTRA_REQUIREMENTS_TXT` und `AZUREML_EXTRA_PYTHON_LIB_PATH` gelten jetzt immer relativ zum Verzeichnis des Bewertungsskripts.
Wenn sich beispielsweise sowohl die Datei „requirements.txt“ als auch das Bewertungsskript in **my_folder** befinden, muss `AZUREML_EXTRA_REQUIREMENTS_TXT` auf „requirements.txt“ festgelegt werden. `AZUREML_EXTRA_REQUIREMENTS_TXT` wird nicht mehr auf **my_folder/requirements.txt** festgelegt.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Bereitstellen eines Modells finden Sie in [diesem Artikel](how-to-deploy-and-where.md).

Informationen zur Problembehandlung für Bereitstellungen von vordefinierten Docker-Images finden Sie in [diesem Artikel](how-to-troubleshoot-prebuilt-docker-image-inference.md).
