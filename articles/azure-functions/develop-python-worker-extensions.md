---
title: Entwickeln von Python-Workererweiterungen für Azure Functions
description: Hier erfahren Sie, wie Sie Workererweiterungen erstellen und veröffentlichen, die es Ihnen ermöglichen, Middlewareverhalten in Python-Funktionen zu integrieren, die in Azure ausgeführt werden.
ms.topic: how-to
ms.date: 6/1/2021
ms.custom: devx-track-python
ms.openlocfilehash: c5ee4ec3a8bd6fd6994962ab35453b34cb7fda8b
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124764278"
---
# <a name="develop-python-worker-extensions-for-azure-functions"></a>Entwickeln von Python-Workererweiterungen für Azure Functions

Mit Azure Functions können Sie benutzerdefiniertes Verhalten in die Ausführung von Python-Funktionen integrieren. Dieses Feature ermöglicht die Erstellung von Geschäftslogik, die Kunden problemlos in ihren eigenen Funktions-Apps verwenden können. Weitere Informationen finden Sie in der [Python-Entwicklerreferenz](functions-reference-python.md#python-worker-extensions).

In diesem Tutorial lernen Sie Folgendes: 
> [!div class="checklist"]
> * Erstellen einer Python-Workererweiterung auf Anwendungsebene für Azure Functions 
> * Nutzen der Erweiterung in einer App wie Ihre Kunden 
> * Packen und Veröffentlichen einer Erweiterung für die Nutzung 

## <a name="prerequisites"></a>Voraussetzungen

Für den Einstieg müssen Sie über Folgendes verfügen:

* [Python 3.6.x oder höher](https://www.python.org/downloads/release/python-374/). Die vollständige Liste der unterstützten Python-Versionen in Azure Functions finden Sie im [Python-Entwicklerhandbuch](functions-reference-python.md#python-version).

* [Azure Functions Core Tools](functions-run-local.md#v2) ab Version 3.0.3568

* [Visual Studio Code](https://code.visualstudio.com/) auf einer der [unterstützten Plattformen](https://code.visualstudio.com/docs/supporting/requirements#_platforms) installiert.

## <a name="create-the-python-worker-extension"></a>Erstellen der Python-Workererweiterung

Durch die von Ihnen erstellte Erweiterung wird die verstrichene Zeit eines HTTP-Triggeraufrufs in den Konsolenprotokollen und im HTTP-Antwortkörper gemeldet.

### <a name="folder-structure"></a>Ordnerstruktur

Der Ordner für Ihr Erweiterungsprojekt muss folgende Struktur haben:

```
<python_worker_extension_root>/
 | - .venv/
 | - python_worker_extension_timer/
 | | - __init__.py
 | - setup.py
 | - readme.md
```

| Ordner/Datei | BESCHREIBUNG |
| --- | --- |
| **.venv/** | (Optional) Diese Datei enthält eine virtuelle Python-Umgebung für die lokale Entwicklung. |
| **python_worker_extension/** | Enthält den Quellcode der Python-Workererweiterung. Dieser Ordner enthält das Python-Hauptmodul für die Veröffentlichung in PyPI. |
| **setup.py** | Enthält die Metadaten des Pakets der Python-Workererweiterung. |
| **readme.md** | (Optional) Enthält Informationen zur Verwendung der Erweiterung. Dieser Inhalt wird als Beschreibung auf der Homepage Ihres PyPI-Projekts angezeigt. |

### <a name="configure-project-metadata"></a>Konfigurieren von Projektmetadaten

Erstellen Sie zunächst `setup.py`, um wichtige Informationen zu Ihrem Paket bereitzustellen. Der Abschnitt `install_requires` muss `'azure-functions >= 1.7.0, < 2.0.0'` enthalten, um sicherzustellen, dass Ihre Erweiterung ordnungsgemäß verteilt und in die Funktions-Apps Ihres Kunden integriert wird.

Ändern Sie in der folgenden Vorlage die Felder `author`, `author_email`, `install_requires`, `license`, `packages` und `url` nach Bedarf.

:::code language="python" source="~/azure-functions-python-worker-extension/setup.py":::

Implementieren Sie als Nächstes Ihren Erweiterungscode auf Anwendungsebene.

### <a name="implement-the-timer-extension"></a>Implementieren der Timer-Erweiterung

Fügen Sie in `python_worker_extension_timer/__init__.py` den folgenden Code hinzu, um die Erweiterung auf Anwendungsebene zu implementieren:

:::code language="python" source="~/azure-functions-python-worker-extension/python_worker_extension_timer/__init__.py":::

Dieser Code erbt von [AppExtensionBase](https://github.com/Azure/azure-functions-python-library/blob/dev/azure/functions/extension/app_extension_base.py), sodass die Erweiterung für jede Funktion in der App gilt. Durch Erben von [FuncExtensionBase](https://github.com/Azure/azure-functions-python-library/blob/dev/azure/functions/extension/func_extension_base.py) hätte die Erweiterung auch auf Funktionsebene implementiert werden können.

Bei der Methode `init` handelt es sich um eine Klassenmethode, die vom Worker beim Importieren der Erweiterungsklasse aufgerufen wird. Hier können Initialisierungsaktionen für die Erweiterung ausgeführt werden. In diesem Fall wird eine Hashzuordnung initialisiert, um die Startzeit des Aufrufs für jede Funktion zu erfassen.

Bei der Methode `configure` handelt es sich um eine Methode für Kunden. In Ihrer Infodatei können Sie Ihren Kunden mitteilen, wann sie `Extension.configure()` aufrufen müssen. In der Infodatei müssen auch die Erweiterungsfunktionen, die mögliche Konfiguration und die Verwendung Ihrer Erweiterung dokumentiert werden. In diesem Beispiel können Kunden wählen, ob die verstrichene Zeit in der HTTP-Antwort (`HttpResponse`) gemeldet wird.

Die Methode `pre_invocation_app_level` wird vom Python-Worker aufgerufen, bevor die Funktion ausgeführt wird. Sie stellt die Informationen aus der Funktion zur Verfügung – also beispielsweise Funktionskontext und Argumente. In diesem Beispiel protokolliert die Erweiterung eine Meldung und zeichnet die Startzeit eines Aufrufs basierend auf der zugehörigen Aufruf-ID (invocation_id) auf.

`post_invocation_app_level` wird ähnlich dazu nach der Funktionsausführung aufgerufen. In diesem Beispiel wird die verstrichene Zeit auf der Grundlage von Startzeit und aktueller Zeit berechnet. Außerdem wird der Rückgabewert der HTTP-Antwort überschrieben.

## <a name="consume-your-extension-locally"></a>Lokales Nutzen Ihrer Erweiterung

Nachdem Sie nun eine Erweiterung erstellt haben, können Sie sie in einem App-Projekt verwenden, um sich zu vergewissern, dass sie ordnungsgemäß funktioniert. 

### <a name="create-an-http-trigger-function"></a>Erstellen einer HTTP-Triggerfunktion

1. Erstellen Sie einen neuen Ordner für Ihr App-Projekt, und navigieren Sie dorthin. 

1. Führen Sie über eine geeignete Shell (beispielsweise Bash) den folgenden Befehl aus, um das Projekt zu initialisieren:

    ```bash
    func init --python
    ```

1. Verwenden Sie den folgenden Befehl, um eine neue HTTP-Triggerfunktion zu erstellen, die anonymen Zugriff zulässt:

    ```bash
    func new -t HttpTrigger -n HttpTrigger -a anonymous
    ```

### <a name="activate-a-virtual-environment"></a>Aktivieren einer virtuellen Umgebung

1. Verwenden Sie das entsprechende Verfahren für Ihr Betriebssystem, um eine virtuelle Python-Umgebung zu erstellen:

    # <a name="linux"></a>[Linux](#tab/linux)
    ```bash
    python3 -m venv .venv
    ```
    # <a name="windows"></a>[Windows](#tab/windows)
    ```console
    py -m venv .venv
    ``` 
    ---

1. Verwenden Sie das entsprechende Verfahren für Ihr Betriebssystem, um die virtuelle Python-Umgebung zu aktivieren:
    # <a name="linux"></a>[Linux](#tab/linux)
    ```bash
    source .venv/bin/activate
    ```
    # <a name="windows"></a>[Windows](#tab/windows)
    ```console
    .venv\Scripts\Activate.ps1
    ``` 
    ---

### <a name="configure-the-extension"></a>Konfigurieren der Erweiterung

1. Verwenden Sie den folgenden Befehl, um Remotepakete für Ihr Funktions-App-Projekt zu installieren: 
    
    ```bash
    pip install -r requirements.txt
    ```

1. Installieren Sie die Erweiterung über Ihren lokalen Dateipfad im bearbeitbaren Modus:

    ```bash
    pip install -e <PYTHON_WORKER_EXTENSION_ROOT>
    ```

    Ersetzen Sie in diesem Beispiel `<PYTHON_WORKER_EXTENSION_ROOT>` durch den Dateispeicherort Ihres Erweiterungsprojekts.   
    Wenn ein Kunde Ihre Erweiterung verwendet, fügt er stattdessen den Speicherort Ihres Erweiterungspakets der Datei „requirements.txt“ hinzu, wie in den folgenden Beispielen gezeigt:

    # <a name="pypi"></a>[PyPI](#tab/pypi)
    ```python
    # requirements.txt
    python_worker_extension_timer==1.0.0
    ``` 
    # <a name="github"></a>[GitHub](#tab/github)
    
    ```python
    # requirements.txt
    git+https://github.com/Azure-Samples/python-worker-extension-timer@main
    ```
    ---

1. Öffnen Sie die Projektdatei „local.settings.js“, und fügen Sie `Values` das folgende Feld hinzu:

    ```json
    "PYTHON_ENABLE_WORKER_EXTENSIONS": "1" 
    ```

    Bei Ausführung in Azure fügen Sie stattdessen `PYTHON_ENABLE_WORKER_EXTENSIONS=1` zu den [App-Einstellungen in der Funktions-App](functions-how-to-use-azure-function-app-settings.md#settings) hinzu.

1. Fügen Sie die beiden folgenden Zeilen in \_\_init.py\_\_ vor der Funktion `main` ein:

    ```python
    from python_worker_extension_timer import TimerExtension
    TimerExtension.configure(append_to_http_response=True)
    ```

    Durch diesen Code wird das Modul `TimerExtension` importiert und der Konfigurationswert `append_to_http_response` festgelegt.

### <a name="verify-the-extension"></a>Überprüfen der Erweiterung

1. Starten Sie im Stammordner Ihres App-Projekts mithilfe von `func host start --verbose` den Funktionshost. Der lokale Endpunkt Ihrer Funktion sollte in der Ausgabe als `https://localhost:7071/api/HttpTrigger` angezeigt werden.

1. Senden Sie im Browser eine GET-Anforderung an `https://localhost:7071/api/HttpTrigger`. Daraufhin sollte eine Antwort wie die folgende angezeigt werden – mit angefügten Daten zur verstrichenen Zeit (**TimeElapsed**) für die Anforderung: 

    <pre>
    This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response. (TimeElapsed: 0.0009996891021728516 sec)
    </pre>

## <a name="publish-your-extension"></a>Veröffentlichen der Erweiterung

Nach dem Erstellen und Überprüfen der Erweiterung müssen noch die folgenden Veröffentlichungsaufgaben ausgeführt werden:

> [!div class="checklist"]
> + Auswählen einer Lizenz
> + Erstellen von Infodatei (readme.md) und weiterem Dokumentationsmaterial 
> + Veröffentlichen der Erweiterungsbibliothek in einer Python-Paketregistrierung oder in einem Versionskontrollsystem (Version Control System, VCS)

# <a name="pypi"></a>[PyPI](#tab/pypi)

So veröffentlichen Sie Ihre Erweiterung in PyPI:

1. Führen Sie den folgenden Befehl aus, um `twine` und `wheel` in Ihrer Python-Standardumgebung oder in einer virtuellen Umgebung zu installieren:

    ```bash
    pip install twine wheel
    ```

1. Entfernen Sie den alten Ordner `dist/` aus Ihrem Erweiterungsrepository.

1. Führen Sie den folgenden Befehl aus, um ein neues Paket in `dist/` zu generieren:

    ```bash
    python setup.py sdist bdist_wheel
    ```

1. Führen Sie den folgenden Befehl aus, um das Paket in PyPI hochzuladen:

    ```bash
    twine upload dist/*
    ```

    Im Rahmen des Uploads ist unter Umständen die Angabe Ihrer PyPI-Kontoanmeldeinformationen erforderlich.

Nach diesen Schritten können Kunden Ihre Erweiterung verwenden, indem sie Ihren Paketnamen in die Datei „requirements.txt“ einschließen.

Weitere Informationen finden Sie im [offiziellen Tutorial zur Python-Paketerstellung](https://packaging.python.org/tutorials/packaging-projects/).

# <a name="github"></a>[GitHub](#tab/github)

Der Quellcode der Erweiterung kann auch mit der Datei „setup.py“ in einem GitHub-Repository veröffentlicht werden, wie in [diesem Beispielrepository](https://github.com/Azure-Samples/python-worker-extension-timer) gezeigt. 

Weitere Informationen zur VCS-Unterstützung in pip finden Sie in der [offiziellen pip-Dokumentation](https://pip.pypa.io/en/stable/cli/pip_install/#vcs-support).

---

## <a name="examples"></a>Beispiele

+ Das fertige Beispielerweiterungsprojekt aus diesem Artikel steht im Beispielrepository [python_worker_extension_timer](https://github.com/Azure-Samples/python-worker-extension-timer) zur Verfügung. 

+ Die OpenCensus-Integration ist ein Open-Source-Projekt, das die Erweiterungsschnittstelle verwendet, um Telemetrieablaufverfolgung in Python-basierte Azure Functions-Apps zu integrieren. Informationen zur Implementierung dieser Python-Workererweiterung finden Sie im Repository [opencensus-python-extensions-azure](https://github.com/census-ecosystem/opencensus-python-extensions-azure/tree/main/extensions/functions).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Python-Entwicklung für Azure Functions finden Sie in den folgenden Ressourcen:

* [Python-Entwicklerhandbuch für Azure Functions](functions-reference-python.md)
* [Bewährte Methoden für Azure Functions](functions-best-practices.md)
* [Entwicklerreferenz zu Azure Functions](functions-reference.md)
