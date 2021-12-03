---
title: Problembehandlung für vordefinierte Docker-Images
titleSuffix: Azure Machine Learning
description: Enthält die Problembehandlungsschritte für die Verwendung von vordefinierten Docker-Images für Rückschlüsse.
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
ms.author: ssambare
author: shivanissambare
ms.date: 10/21/2021
ms.topic: how-to
ms.reviewer: larryfr
ms.custom: deploy, docker, prebuilt, troubleshoot
ms.openlocfilehash: f57a59ffa9ad316e0ae1da76efb843673303541f
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131556158"
---
# <a name="troubleshooting-prebuilt-docker-images-for-inference"></a>Problembehandlung: Vordefinierte Docker-Images für Rückschlüsse

Es wird beschrieben, wie Sie Probleme behandeln, die bei der Verwendung von vordefinierten Docker-Images für Rückschlüsse mit Azure Machine Learning auftreten können.

> [!IMPORTANT]
> Die Verwendung der [Erweiterbarkeit von Python-Paketen für vordefinierte Docker-Images](how-to-prebuilt-docker-images-inference-python-extensibility.md) mit Azure Machine Learning befindet sich derzeit in der Vorschauversion. Die Vorschaufunktionalität wird „wie besehen“ zur Verfügung gestellt, ohne Garantien hinsichtlich Support oder Vereinbarung zum Servicelevel (Service Level Agreement, SLA). Weitere Informationen finden Sie in den [zusätzlichen Nutzungsbedingungen für Microsoft Azure-Vorschauversionen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="model-deployment-failed"></a>Fehler bei der Modellbereitstellung

Wenn bei der Modellbereitstellung ein Fehler auftritt, werden in [Azure Machine Learning Studio](https://ml.azure.com/) keine Protokolle angezeigt, und `service.get_logs()` wird wieder auf „None“ festgelegt.
Falls in der Funktion „init()“ von „score.py“ ein Problem auftritt, werden von `service.get_logs()` Protokolle dafür zurückgegeben.

Daher müssen Sie den Container lokal ausführen, indem Sie einen der unten angegebenen Befehle verwenden und `<MCR-path>` durch einen Imagepfad ersetzen. Eine Liste mit den Images und Pfaden finden Sie unter [Vordefinierte Docker-Images für Rückschlüsse](concept-prebuilt-docker-images-inference.md).

### <a name="mounting-extensibility-solution"></a>Einbinden einer Erweiterbarkeitslösung

Navigieren Sie zu dem Verzeichnis, in dem `score.py` enthalten ist, und führen Sie Folgendes aus:

```bash
docker run -it -v $(pwd):/var/azureml-app -e AZUREML_EXTRA_PYTHON_LIB_PATH="myenv/lib/python3.7/site-packages" <mcr-path>
```

### <a name="requirementstxt-extensibility-solution"></a>Erweiterbarkeitslösung „requirements.txt“

Navigieren Sie zu dem Verzeichnis, in dem `score.py` enthalten ist, und führen Sie Folgendes aus:

```bash
docker run -it -v $(pwd):/var/azureml-app -e AZUREML_EXTRA_REQUIREMENTS_TXT="requirements.txt" <mcr-path>
```

## <a name="enable-local-debugging"></a>Aktivieren des lokalen Debuggens

Mit dem lokalen Rückschlussserver können Sie Ihr Eingabeskript (`score.py`) schnell debuggen. Falls das zugrunde liegende Bewertungsskript einen Fehler enthält, kann der Server das Modell nicht initialisieren oder bereitstellen. Stattdessen wird eine Ausnahme ausgelöst und der Ort angegeben, an dem die Probleme aufgetreten sind. [Weitere Informationen zum HTTP-Rückschlussserver von Azure Machine Learning](how-to-inference-server-http.md)

## <a name="for-common-model-deployment-issues"></a>Informationen zu häufigen Problemen bei der Modellbereitstellung

Informationen zu Problemen beim Bereitstellen eines Modells aus Azure Machine Learning in Azure Container Instances (ACI) oder Azure Kubernetes Service (AKS) finden Sie unter [Behandeln von Problemen bei der Modellimplementierung](how-to-troubleshoot-deployment.md).

## <a name="init-or-run-failing-to-write-a-file"></a>Fehler beim Schreiben einer Datei mit „init()“ oder „run()“

Da HTTP-Server in unseren vordefinierten Docker-Images nicht als *root-Benutzer* ausgeführt werden, verfügen diese ggf. nicht über Zugriffsrechte für alle Verzeichnisse. Führen Sie Schreibvorgänge nur für Verzeichnisse durch, für die Sie Zugriffsrechte besitzen. Ein Beispiel hierfür ist das Verzeichnis `/tmp` im Container.

## <a name="extra-python-packages-not-installed"></a>Keine zusätzlichen Python-Pakete installiert

* Überprüfen Sie, ob die Umgebungsvariable oder der Dateiname einen Tippfehler enthält.
* Überprüfen Sie das Containerprotokoll darauf, ob `pip install -r <your_requirements.txt>` installiert ist.
* Überprüfen Sie, ob das Quellverzeichnis im Konstruktor für die Rückschlusskonfiguration ([inference config](/python/api/azureml-core/azureml.core.model.inferenceconfig#constructor)) richtig festgelegt wurde.
* Wenn die Installation nicht gefunden werden kann und im Protokoll eine Meldung der Art „Datei nicht gefunden“ enthalten ist, sollten Sie überprüfen, ob der im Protokoll angezeigte Dateiname korrekt ist.
* Wenn die Installation gestartet wurde, aber nicht erfolgreich war oder zu einem Timeout geführt hat, sollten Sie nach Möglichkeit dieselbe Datei `requirements.txt` lokal mit der gleichen Python- und pip-Version in einer bereinigten Umgebung (kein Cacheverzeichnis: `pip install --no-cache-dir -r requriements.txt`) installieren. Ermitteln Sie, ob das Problem lokal reproduziert werden kann.

## <a name="mounting-solution-failed"></a>Fehler beim Einbinden der Lösung

* Überprüfen Sie, ob die Umgebungsvariable oder der Verzeichnisname einen Tippfehler enthält.
* Die Umgebungsvariable muss auf den relativen Pfad der Datei `score.py` festgelegt werden.
* Überprüfen Sie, ob das Quellverzeichnis im Konstruktor für die Rückschlusskonfiguration ([inference config](/python/api/azureml-core/azureml.core.model.inferenceconfig#constructor)) richtig festgelegt wurde.
* Beim Verzeichnis muss es sich um das Verzeichnis „site-packages“ der Umgebung handeln.
* Wenn von `score.py` immer noch `ModuleNotFound` zurückgegeben wird und sich das Modul eigentlich im eingebundenen Verzeichnis befinden sollte, sollten Sie versuchen, `sys.path` in `init()` oder `run()` auszugeben. So können Sie überprüfen, ob ggf. ein Pfad fehlt.

## <a name="building-an-image-based-on-the-prebuilt-docker-image-failed"></a>Fehler beim Erstellen eines Images basierend auf dem vordefinierten Docker-Image

* Wenn bei der Installation des apt-Pakets ein Fehler aufgetreten ist, sollten Sie vor dem Ausführen des apt-Befehls überprüfen, ob der Benutzer als „root“ festgelegt wurde. (Achten Sie darauf, dass Sie dann wieder vom root-Benutzer zum regulären Benutzer wechseln.) 

## <a name="image-built-based-on-the-prebuilt-docker-image-cant-boot-up"></a>Auf dem vordefinierten Docker-Image basierendes Image kann nicht gestartet werden

* Der reguläre Benutzer (nicht „root“) muss als `dockeruser` festgelegt sein. Andernfalls muss der Besitzer der folgenden Verzeichnisse auf den Benutzernamen festgelegt sein, den Sie beim Ausführen des Images verwenden möchten:

    ```bash
    /var/runit
    /var/log
    /var/lib/nginx
    /run
    /opt/miniconda
    /var/azureml-app
    ```

* Wenn `ENTRYPOINT` für das neu erstellte Image geändert wurde, müssen der HTTP-Server und die zugehörigen Komponenten mit `runsvdir /var/runit` geladen werden.

## <a name="next-steps"></a>Nächste Schritte

* [Hinzufügen von Python-Paketen zu vordefinierten Images](how-to-prebuilt-docker-images-inference-python-extensibility.md)
* [Verwenden eines vordefinierten Pakets als Basis für ein neues Dockerfile](how-to-extend-prebuilt-docker-image-inference.md)