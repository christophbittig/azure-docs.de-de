---
author: nitinme
manager: nitinme
ms.service: applied-ai-services
ms.subservice: immersive-reader
ms.topic: include
ms.date: 03/04/2021
ms.author: nitinme
ms.openlocfilehash: cb2f79cec51a5aea6b905ee7bac8447e135a5f8c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131253373"
---
## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/cognitive-services)
* Eine Ressource des plastischen Readers, die für die Authentifizierung mit Azure Active Directory konfiguriert ist. Befolgen Sie [diese Anweisungen](../../how-to-create-immersive-reader.md) für die Einrichtung.  Einige der hier erstellten Werte benötigen Sie bei der Konfiguration der Umgebungseigenschaften. Speichern Sie die Ausgabe Ihrer Sitzung zur späteren Verwendung in einer Textdatei.
* [Git](https://git-scm.com/).
* [Immersive Reader SDK](https://github.com/microsoft/immersive-reader-sdk).
* [Python](https://www.python.org/downloads/) und [pip](https://docs.python.org/3/installing/index.html). Ab Python 3.4 ist pip standardmäßig in den binären Python-Installationsprogrammen enthalten.
* [Flask](https://flask.palletsprojects.com/en/1.0.x/)
* [Jinja](http://jinja.pocoo.org/docs/2.10/)
* [virtualenv](https://virtualenv.pypa.io/en/latest/) und [virtualenvwrapper-win für Windows](https://pypi.org/project/virtualenvwrapper-win/) oder [virtualenvwrapper für OSX](https://virtualenvwrapper.readthedocs.io/en/latest/)
* [requests-Modul](https://pypi.org/project/requests/2.7.0/)
* Eine IDE (beispielsweise [Visual Studio Code](https://code.visualstudio.com/))

## <a name="configure-authentication-credentials"></a>Konfigurieren von Anmeldeinformationen für die Authentifizierung

Erstellen Sie eine neue Datei namens **.env**, und fügen Sie die folgenden Namen und Werte ein. Verwenden Sie dabei die Werte, die beim Erstellen der Ressource des plastischen Readers angegeben wurden.

```text
TENANT_ID={YOUR_TENANT_ID}
CLIENT_ID={YOUR_CLIENT_ID}
CLIENT_SECRET={YOUR_CLIENT_SECRET}
SUBDOMAIN={YOUR_SUBDOMAIN}
```

Committen Sie diese Datei nicht in die Quellcodeverwaltung, da sie Geheimnisse enthält, die nicht für die Öffentlichkeit bestimmt sind.

Schützen Sie den API-Endpunkt **getimmersivereadertoken** mit einer Art von Authentifizierung, etwa [OAuth](https://oauth.net/2/). Durch die Authentifizierung wird verhindert, dass nicht autorisierte Benutzer Token für den Dienst „Plastischer Reader“ und die Abrechnung erhalten. Dies ist jedoch nicht Inhalt dieses Tutorials.

## <a name="create-a-python-web-app-on-windows"></a>Erstellen einer Python-Web-App unter Windows

Erstellen Sie mithilfe von `flask` eine Python-Web-App unter Windows.

Installieren Sie [Git](https://git-scm.com/).

Öffnen Sie im Anschluss an die Git-Installation eine Eingabeaufforderung, und klonen Sie das Git-Repository des Immersive Reader SDK in einem Ordner auf Ihrem Computer.

```cmd
git clone https://github.com/microsoft/immersive-reader-sdk.git
```

Installieren Sie [Python](https://www.python.org/downloads/).

Aktivieren Sie das Kontrollkästchen für das **Hinzufügen von Python zu PATH**.

![Dialogfeld für die Python-Installation unter Windows: Schritt 1](../../media/pythoninstallone.jpg)

Fügen Sie unter **Optionale Features** Features hinzu, indem Sie die entsprechenden Kontrollkästchen aktivieren, und wählen Sie dann **Weiter** aus.

![Dialogfeld für die Python-Installation unter Windows: Schritt 2](../../media/pythoninstalltwo.jpg)

Wählen Sie **Custom installation** (Benutzerdefinierte Installation) aus, und legen Sie den Installationspfad auf Ihren Stammordner (beispielsweise `C:\Python37-32\`) fest. Wählen Sie dann **Installieren** aus.

![Dialogfeld für die Python-Installation unter Windows: Schritt 3](../../media/pythoninstallthree.jpg)

Öffnen Sie nach Anschluss der Python-Installation eine Eingabeaufforderung, und wechseln Sie mithilfe von `cd` zum Ordner mit den Python-Skripts.

```cmd
cd C:\Python37-32\Scripts
```

Installieren Sie Flask.

```cmd
pip install flask
```

Installieren Sie Jinja2. Hierbei handelt es sich um eine Vorlagen-Engine mit umfassenden Funktionen für Python.

```cmd
pip install jinja2
```

Installieren Sie virtualenv. Mit diesem Tool werden isolierte Python-Umgebungen erstellt.

```cmd
pip install virtualenv
```

Installieren Sie virtualenvwrapper-win. Virtualenvwrapper dient zur Vereinfachung der Verwendung von virtualenv.

```cmd
pip install virtualenvwrapper-win
```

Installieren Sie das requests-Modul. requests ist eine Apache2-lizenzierte, in Python geschriebene HTTP-Bibliothek.

```cmd
pip install requests
```

Installieren Sie das Modul „python-dotenv“. Dieses Modul liest das Schlüssel-Wert-Paar aus der ENV-Datei und fügt es der Umgebungsvariablen hinzu.

```cmd
pip install python-dotenv
```

Erstellen Sie eine virtuelle Umgebung.

```cmd
mkvirtualenv advanced-python
```

Wechseln Sie mithilfe von `cd` zum Stammordner des Beispielprojekts.

```cmd
cd C:\immersive-reader-sdk\js\samples\advanced-python
```

Stellen Sie eine Verbindung zwischen dem Beispielprojekt und der Umgebung her. Mit dieser Aktion wird die neu erstellte virtuelle Umgebung dem Stammordner des Beispielprojekts zugeordnet.

```cmd
setprojectdir .
```

Aktivieren Sie die virtuelle Umgebung.

```cmd
activate
```

Das Projekt sollte nun aktiv sein, und an der Eingabeaufforderung wird in etwa Folgendes angezeigt: `(advanced-python) C:\immersive-reader-sdk\js\samples\advanced-python>`.

Deaktivieren Sie die Umgebung.

```cmd
deactivate
```

Das Präfix `(advanced-python)` sollte nicht mehr vorhanden sein, da die Umgebung deaktiviert ist.

Wenn Sie die Umgebung wieder aktivieren möchten, führen Sie `workon advanced-python` im Stammordner des Beispielprojekts aus.

```cmd
workon advanced-python
```

### <a name="start-the-immersive-reader-with-sample-content"></a>Starten von Plastischer Reader mit Beispielinhalt

Wenn die Umgebung aktiv ist, geben Sie `flask run` im Stammordner des Beispielprojekts ein, um das Beispielprojekt auszuführen.

```cmd
flask run
```

Öffnen Sie Ihren Browser, und navigieren Sie zu http://localhost:5000.

## <a name="create-a-python-web-app-on-osx"></a>Erstellen einer Python-Web-App unter OSX

Erstellen Sie mithilfe von `flask` eine Python-Web-App unter OSX.

Installieren Sie [Git](https://git-scm.com/).

Öffnen Sie im Anschluss an die Git-Installation ein Terminal, und klonen Sie das Git-Repository des Immersive Reader SDK in einem Ordner auf Ihrem Computer.

```bash
git clone https://github.com/microsoft/immersive-reader-sdk.git
```

Installieren Sie [Python](https://www.python.org/downloads/).

Der Python-Stammordner (beispielsweise `Python37-32`) sollte sich nun im Ordner „Applications“ befinden.

Öffnen Sie nach Anschluss der Python-Installation ein Terminal, und wechseln Sie mithilfe von `cd` zum Ordner mit den Python-Skripts.

```bash
cd immersive-reader-sdk/js/samples/advanced-python
```

Installieren Sie pip.

```bash
curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py
```

Führen Sie zur Vermeidung von Berechtigungsproblemen den folgenden Code aus, um pip für den aktuell angemeldeten Benutzer zu installieren:

```bash
python get-pip.py --user
```

```bash
sudo nano /etc/paths
```

- Geben Sie Ihr Kennwort ein, wenn Sie dazu aufgefordert werden.
- Fügen Sie den Pfad Ihrer pip-Installation Ihrer PATH-Variablen hinzu.
- Geben Sie den hinzuzufügenden Pfad am Ende der Datei als letztes Listenelement ein (beispielsweise `PATH=$PATH:/usr/local/bin`).
- Drücken Sie zum Beenden **STRG+X**.
- Geben Sie **J** ein, um den geänderten Puffer zu speichern.
- Das ist alles! Geben Sie zu Testzwecken in einem neuen Terminalfenster Folgendes ein: `echo $PATH`.

Installieren Sie Flask.

```bash
pip install flask --user
```

Installieren Sie Jinja2. Hierbei handelt es sich um eine Vorlagen-Engine mit umfassenden Funktionen für Python.

```bash
pip install Jinja2 --user
```

Installieren Sie virtualenv. Mit diesem Tool werden isolierte Python-Umgebungen erstellt.

```bash
pip install virtualenv --user
```

Installieren Sie virtualenvwrapper. Virtualenvwrapper dient zur Vereinfachung der Verwendung von virtualenv.

```bash
pip install virtualenvwrapper --user
```

Installieren Sie das requests-Modul. requests ist eine Apache2-lizenzierte, in Python geschriebene HTTP-Bibliothek.

```bash
pip install requests --user
```

Installieren Sie das Modul „python-dotenv“. Dieses Modul liest das Schlüssel-Wert-Paar aus der ENV-Datei und fügt es der Umgebungsvariablen hinzu.

```bash
pip install python-dotenv --user
```

Wählen Sie einen Ordner für Ihre virtuellen Umgebungen aus, und führen Sie den folgenden Befehl aus:

```bash
mkdir ~/.virtualenvs
```

Wechseln Sie mithilfe von `cd` zum Ordner mit der Python-Beispielanwendung des Immersive Reader SDK.

```bash
cd immersive-reader-sdk/js/samples/advanced-python
```

Erstellen Sie eine virtuelle Umgebung.

```bash
mkvirtualenv -p /usr/local/bin/python3 advanced-python
```

Stellen Sie eine Verbindung zwischen dem Beispielprojekt und der Umgebung her. Mit dieser Aktion wird die neu erstellte virtuelle Umgebung dem Stammordner des Beispielprojekts zugeordnet.

```bash
setprojectdir .
```

Aktivieren Sie die virtuelle Umgebung.

```bash
activate
```

Das Projekt sollte nun aktiv sein, und an der Eingabeaufforderung wird in etwa Folgendes angezeigt: `(advanced-python) /immersive-reader-sdk/js/samples/advanced-python>`.

Deaktivieren Sie die Umgebung.

```bash
deactivate
```

Das Präfix `(advanced-python)` sollte nicht mehr vorhanden sein, da die Umgebung deaktiviert ist.

Wenn Sie die Umgebung wieder aktivieren möchten, führen Sie `workon advanced-python` im Stammordner des Beispielprojekts aus.

```bash
workon advanced-python
```

## <a name="start-the-immersive-reader-with-sample-content"></a>Starten von Plastischer Reader mit Beispielinhalt

Wenn die Umgebung aktiv ist, geben Sie `flask run` im Stammordner des Beispielprojekts ein, um das Beispielprojekt auszuführen.

```bash
flask run
```

Öffnen Sie Ihren Browser, und navigieren Sie zu http://localhost:5000.

## <a name="next-steps"></a>Nächste Schritte

* Machen Sie sich mit dem [Immersive Reader SDK](https://github.com/microsoft/immersive-reader-sdk) und der [zugehörigen Referenz](../../reference.md) vertraut.
* Sehen Sie sich Codebeispiele auf [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/) an.
