---
title: HTTP-Rückschlussserver von Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Erfahren Sie, wie Sie die lokale Entwicklung mit dem HTTP-Rückschlussserver für Azure Machine Learning aktivieren.
author: shivanissambare
ms.author: ssambare
ms.reviewer: larryfr
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.custom: inference server, local development, local debugging, devplatv2
ms.date: 05/14/2021
ms.openlocfilehash: 924995fe9330a44b52a40a8e3eb651efdeb24398
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122340318"
---
# <a name="azure-machine-learning-inference-http-server-preview"></a>HTTP-Rückschlussserver von Azure Machine Learning (Vorschau)

Der HTTP-Rückschlussserver von Azure Machine Learning [(Vorschau)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) ist ein Python-Paket, mit dem Sie Ihr Einstiegsskript (`score.py`) in einer lokalen Entwicklungsumgebung problemlos überprüfen können. Wenn ein Problem mit dem Bewertungsskript vorliegt, gibt der Server einen Fehler zurück. Außerdem wird die Position zurückgegeben, an der der Fehler aufgetreten ist.

Der Server kann auch verwendet werden, wenn Validierungsgates in einer Pipeline für Continuous Integration und Continuous Deployment erstellt werden. Starten Sie beispielsweise den Server mit dem Kandidatenskript, und führen Sie die Testsammlung für den lokalen Endpunkt aus.

## <a name="prerequisites"></a>Voraussetzungen

- Erfordert: Python 3.7 oder höher

## <a name="installation"></a>Installation

> [!NOTE]
> Installieren Sie den Server in einer virtuellen Umgebung, um Paketkonflikte zu vermeiden.

Führen Sie den folgenden Befehl in Ihrer Eingabeaufforderung bzw. in Ihrem Terminal aus, um das `azureml-inference-server-http package` zu installieren:

```bash
python -m pip install azureml-inference-server-http
```

## <a name="use-the-server"></a>Verwenden des Servers

1. Erstellen Sie ein Verzeichnis zum Speichern Ihrer Dateien:

    ```bash
    mkdir server_quickstart
    cd server_quickstart
    ```

1. Erstellen Sie eine virtuelle Umgebung, und aktivieren Sie sie, um Paketkonflikte zu vermeiden:

    ```bash
    virtualenv myenv
    source myenv/bin/activate
    ```

1. Installieren Sie das `azureml-inference-server-http`-Paket aus dem [pypi](https://pypi.org/project/azureml-inference-server-http/)-Feed:

    ```bash
    python -m pip install azureml-inference-server-http
    ```

1. Erstellen Sie Ihr Einstiegsskript (`score.py`). Im folgenden Beispiel wird ein einfaches Einstiegsskript erstellt:

    ```bash
    echo '
    import time

    def init():
        time.sleep(1)

    def run(input_data):
        return {"message":"Hello, World!"}
    ' > score.py
    ```

1. Starten Sie den Server, und legen Sie `score.py` als Einstiegsskript fest:

    ```bash
    azmlinfsrv --entry_script score.py
    ```

    > [!NOTE]
    > Der Server wird auf 0.0.0.0 gehostet. Dies bedeutet, dass er auf alle IP-Adressen des Hostcomputers lauscht.

1. Senden Sie eine Bewertungsanforderung mithilfe von `curl` an den Server:

    ```bash
    curl -p 127.0.0.1:5001/score
    ```

    Der Server sollte wie folgt reagieren.

    ```bash
    {"message": "Hello, World!"}
    ```

Nun können Sie das Bewertungsskript ändern und Ihre Änderungen testen, indem Sie den Server erneut ausführen.

## <a name="server-routes"></a>Serverrouten

Der Server lauscht bei diesen Routen an Port 5001.

| Name | Route|
| --- | --- |
| Livetest | 127.0.0.1:5001/|
| Score | 127.0.0.1:5001/score|

## <a name="server-parameters"></a>Serverparameter

Die folgende Tabelle enthält die vom Server akzeptierten Parameter:

| Parameter | Erforderlich | Standard | BESCHREIBUNG |
| ---- | --- | ---- | ----|
| entry_script | True | – | Der relative oder absolute Pfad zum Bewertungsskript.|
| model_dir | False | – | Der relative oder absolute Pfad zum Verzeichnis mit dem Modell, das für Rückschlüsse verwendet wird.
| port | Falsch | 5001 | Der bereitstellende Port des Servers.|
| worker_count | Falsch | 1 | Die Anzahl von Arbeitsthreads, die gleichzeitige Anforderungen verarbeiten. |
| appinsights_instrumentation_key | False | – | Dies ist der Instrumentierungsschlüssel für Application Insights, wo die Protokolle veröffentlicht werden. |

## <a name="request-flow"></a>Anforderungsflow

In den folgenden Schritten wird erläutert, wie der HTTP-Rückschlussserver für Azure Machine Learning eingehende Anforderungen verarbeitet:

1. Ein Python CLI-Wrapper umgibt den Netzwerkstapel des Servers und wird zum Starten des Servers verwendet.
1. Ein Client sendet eine Anforderung an den Server.
1. Wenn eine Anforderung empfangen wird, durchläuft sie den [WSGI-Server](https://www.fullstackpython.com/wsgi-servers.html) und wird dann an einen der Worker gesendet.
    - [Gunicorn](https://docs.gunicorn.org/) wird unter __Linux__ verwendet.
    - [Waitress](https://docs.pylonsproject.org/projects/waitress/) wird unter __Windows__ verwendet.
1. Die Anforderungen werden dann von einer [Flask](https://flask.palletsprojects.com/)-App verarbeitet, die das Einstiegsskript und alle Abhängigkeiten lädt.
1. Schließlich wird die Anforderung an Ihr Einstiegsskript gesendet. Das Einstiegsskript sendet dann einen Rückschlussaufruf an das geladene Modell und gibt eine Antwort zurück.

:::image type="content" source="./media/how-to-inference-server-http/inference-server-architecture.png" alt-text="Diagramm des HTTP-Serverprozesses":::

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

### <a name="do-i-need-to-reload-the-server-when-changing-the-score-script"></a>Muss ich den Server neu laden, wenn ich das Bewertungsskript ändere?

Nachdem Sie Ihr Bewertungsskript (`score.py`) geändert haben, beenden Sie den Server mit `ctrl + c`. Starten Sie ihn dann mithilfe von `azmlinfsrv --entry_script score.py` neu.

### <a name="which-os-is-supported"></a>Welches Betriebssystem wird unterstützt?

Der Azure Machine Learning-Rückschlussserver wird unter Windows- und Linux-basierten Betriebssystemen ausgeführt.

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zum Erstellen eines Einstiegsskripts und zum Bereitstellen von Modellen finden Sie unter [Bereitstellen eines Modells mithilfe von Azure Machine Learning](how-to-deploy-and-where.md).
* Weitere Informationen unter [Vordefinierte Docker-Images für Rückschlüsse (Vorschau)](concept-prebuilt-docker-images-inference.md)