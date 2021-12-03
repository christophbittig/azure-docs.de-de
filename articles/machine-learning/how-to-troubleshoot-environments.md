---
title: Problembehandlung für Umgebungsimages
titleSuffix: Azure Machine Learning
description: Es wird beschrieben, wie Sie die Problembehandlung durchführen, wenn Probleme mit dem Buildvorgang für Umgebungsimages und mit der Paketinstallation auftreten.
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
author: saachigopal
ms.author: sagopal
ms.date: 10/21/2021
ms.topic: troubleshooting
ms.custom: devx-track-python
ms.openlocfilehash: 75f79ca277a017cce8b506e423faaf2e49485686
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2021
ms.locfileid: "132137551"
---
# <a name="troubleshoot-environment-image-builds"></a>Problembehandlung für Buildvorgänge für Umgebungsimages

Es wird beschrieben, wie Sie die Problembehandlung durchführen, wenn Probleme mit dem Buildvorgang für Docker-Umgebungsimages und mit der Paketinstallation auftreten.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. Probieren Sie die [kostenlose oder kostenpflichtige Version von Azure Machine Learning](https://azure.microsoft.com/free/) aus.
* Das [Azure Machine Learning SDK](/python/api/overview/azure/ml/install).
* Die [Azure CLI](/cli/azure/install-azure-cli)
* Die [CLI-Erweiterung für Azure Machine Learning](reference-azure-machine-learning-cli.md).
* Zum lokalen Debuggen benötigen Sie eine funktionierende Docker-Installation auf Ihrem lokalen System.

## <a name="docker-image-build-failures"></a>Fehler beim Erstellen eines Docker-Images
 
Bei den meisten Fehlern bei der Imageerstellung finden Sie die Grundursache im Buildprotokoll.
Sie finden das Buildprotokoll des Images im Azure Machine Learning-Portal (20\_image\_build\_log.txt) und in den Ausführungsprotokollen Ihrer Azure Container Registry-Tasks.
 
Meist ist es einfacher, Fehler lokal zu reproduzieren. Überprüfen Sie, welche Art von Fehler vorliegt, und probieren Sie eines der folgenden `setuptools` aus:

- Lokales Installieren einer Conda-Abhängigkeit: `conda install suspicious-dependency==X.Y.Z`
- Lokales Installieren einer pip-Abhängigkeit: `pip install suspicious-dependency==X.Y.Z`
- Materialisieren der gesamten Umgebung: `conda create -f conda-specification.yml`

> [!IMPORTANT]
> Stellen Sie sicher, dass die Plattform und der Interpreter in Ihrem lokalen Computecluster mit den entsprechenden Komponenten im Remotecomputecluster übereinstimmen. 

### <a name="timeout"></a>Timeout 
 
Die folgenden Netzwerkprobleme können Timeoutfehler verursachen:

- Geringe Internetbandbreite
- Serverprobleme
- Umfangreiche Abhängigkeiten, die nicht mit den jeweiligen Conda- oder pip-Timeouteinstellungen heruntergeladen werden können
 
Mit Meldungen ähnlich den folgenden Beispielen wird auf das Problem hingewiesen:
 
```
('Connection broken: OSError("(104, \'ECONNRESET\')")', OSError("(104, 'ECONNRESET')"))
```
```
ReadTimeoutError("HTTPSConnectionPool(host='****', port=443): Read timed out. (read timeout=15)",)
```

Wenn Sie eine Fehlermeldung erhalten, versuchen Sie es mit einer der folgenden möglichen Lösungen:
 
- Versuchen Sie, eine andere Quelle für die Abhängigkeit zu verwenden. Beispiele hierfür sind Spiegel, Azure Blob Storage oder andere Python-Feeds.
- Führen Sie ein Update für Conda bzw. PIP durch. Aktualisieren Sie die Timeouteinstellungen, falls Sie eine benutzerdefinierte Docker-Datei verwenden.
- Für einige PIP-Versionen bestehen bekannte Probleme. Erwägen Sie, den Umgebungsabhängigkeiten eine bestimmte Version von pip hinzuzufügen.

### <a name="package-not-found"></a>Paket nicht gefunden

Die folgenden Fehler treten am häufigsten bei der Imageerstellung auf:

- Conda-Paket wurde nicht gefunden:

   ```
   ResolvePackageNotFound: 
   - not-existing-conda-package
   ```

- Das angegebene pip-Paket oder die Version wurde nicht gefunden:

   ```
   ERROR: Could not find a version that satisfies the requirement invalid-pip-package (from versions: none)
   ERROR: No matching distribution found for invalid-pip-package
   ```

- Ungültige geschachtelte pip-Abhängigkeit:

   ```
   ERROR: No matching distribution found for bad-package==0.0 (from good-package==1.0)
   ```

Überprüfen Sie, ob das Paket an den angegebenen Quellen vorhanden ist. Verwenden Sie die [pip-Suche](https://pip.pypa.io/en/stable/reference/pip_search/), um die pip-Abhängigkeiten zu überprüfen:

- `pip search azureml-core`

Verwenden Sie für Conda-Abhängigkeiten die [Conda-Suche](https://docs.conda.io/projects/conda/en/latest/commands/search.html):

- `conda search conda-forge::numpy`

Versuchen Sie als weitere Optionen folgende:
- `pip search -h`
- `conda search -h`

#### <a name="installer-notes"></a>Hinweise zum Installationsprogramm

Stellen Sie sicher, dass die erforderliche Verteilung für die angegebene Plattform und die Version des Python-Interpreters vorhanden ist.

Navigieren Sie für pip-Abhängigkeiten zu `https://pypi.org/project/[PROJECT NAME]/[VERSION]/#files`, um zu ermitteln, ob die erforderliche Version verfügbar ist. Ein Beispiel finden Sie unter https://pypi.org/project/azureml-core/1.11.0/#files.

Überprüfen Sie für Conda-Abhängigkeiten das Paket im Kanalrepository.
Lesen Sie für Kanäle, die von Anaconda, Inc. verwaltet werden, die [Seite zu Anaconda-Paketen](https://repo.anaconda.com/pkgs/).

### <a name="pip-package-update"></a>pip-Paketupdate

Während einer Installation oder Aktualisierung eines pip-Pakets muss der Konfliktlöser ggf. ein Update für ein bereits installiertes Paket durchführen, um die neuen Anforderungen zu erfüllen.
Bei der Deinstallation kann aus verschiedenen Gründen ein Fehler auftreten, dessen Ursache die pip-Version oder die Installationsweise der Abhängigkeit ist.
Das häufigste Szenario ist, dass eine von Conda installierte Abhängigkeit von pip nicht deinstalliert werden konnte.
Erwägen Sie in diesem Fall, die Abhängigkeit mit `conda remove mypackage` zu deinstallieren.

```
  Attempting uninstall: mypackage
    Found existing installation: mypackage X.Y.Z
ERROR: Cannot uninstall 'mypackage'. It is a distutils installed project and thus we cannot accurately determine which files belong to it which would lead to only a partial uninstall.
``` 
### <a name="installer-issues"></a>Probleme mit dem Installationsprogramm

Für bestimmte Installationsprogrammversionen treten Probleme in den Paketkonfliktlösern auf, die zu einem Buildfehler führen können.

Wenn Sie ein benutzerdefiniertes Basisimage oder ein Dockerfile verwenden, wird empfohlen, mindestens Conda-Version 4.5.4 zu verwenden.

Zum Installieren von pip-Abhängigkeiten ist ein pip-Paket erforderlich. Wenn in der Umgebung keine Version angegeben ist, wird die neueste Version verwendet.
Es wird empfohlen, eine bekannte Version von pip zu verwenden, um das Auftreten von vorübergehenden Problemen oder Breaking Changes zu vermeiden, die eventuell durch die aktuelle Version des Tools verursacht werden.

Erwägen Sie, die pip-Version in Ihrer Umgebung anzuheften, falls die folgende Meldung angezeigt wird:

   ```
   Warning: you have pip-installed dependencies in your environment file, but you do not list pip itself as one of your conda dependencies. Conda may not use the correct pip to install your packages, and they may end up in the wrong place. Please add an explicit pip dependency. I'm adding one for you, but still nagging you.
   ```

Fehler im pip-Unterprozess:
   ```
   ERROR: THESE PACKAGES DO NOT MATCH THE HASHES FROM THE REQUIREMENTS FILE. If you have updated the package versions, update the hashes as well. Otherwise, examine the package contents carefully; someone may have tampered with them.
   ```

Die pip-Installation kann auch in einer unendlichen Schleife hängen bleiben, falls für die Abhängigkeiten unlösbare Konflikte bestehen. Wenn Sie lokal arbeiten, sollten Sie für die pip-Version ein Downgrade auf eine frühere Version als 20.3 durchführen. In einer Conda-Umgebung, die aus einer YAML-Datei erstellt wurde, tritt dieses Problem nur auf, wenn „conda-forge“ der Kanal mit der höchsten Priorität ist. Geben Sie zum Beheben des Problems in der Conda-Spezifikationsdatei explizit „pip < 20.3“ (!=20.3 oder =20.2.4-Anheftung an andere Version) als Conda-Abhängigkeit an.

### <a name="modulenotfounderror-no-module-named-distutilsdir_util"></a>ModuleNotFoundError: Kein Modul mit dem Namen "distutils.dir_util"

Beim Einrichten einer Umgebung tritt manchmal das Problem **ModuleNotFoundError: Kein Modul mit dem Namen "distutils.dir_util"** auf. Führen Sie den folgenden Befehl aus, um dies zu beheben:

```bash
apt-get install -y --no-install-recommends python3 python3-distutils && \
ln -sf /usr/bin/python3 /usr/bin/python
```

Wenn Sie mit einem Dockerfile arbeiten, führen Sie den Befehl innerhalb eines RUN-Befehls aus.

```dockerfile
RUN apt-get update && \
  apt-get install -y --no-install-recommends python3 python3-distutils && \
  ln -sf /usr/bin/python3 /usr/bin/python
```

Durch Ausführung dieses Befehls werden die richtigen Modulabhängigkeiten für die Konfiguration Ihrer Umgebung installiert. 

### <a name="build-failure-when-using-spark-packages"></a>Buildfehler bei Verwendung von Spark-Paketen

Konfigurieren Sie die Umgebung so, dass die Pakete nicht vorab zwischengespeichert werden. 

```python
env.spark.precache_packages = False
```

## <a name="service-side-failures"></a>Dienstseitige Fehler

In den folgenden Szenarien finden Sie Informationen zur Problembehandlung bei dienstseitigen Fehlern.

### <a name="youre-unable-to-pull-an-image-from-a-container-registry-or-the-address-couldnt-be-resolved-for-a-container-registry"></a>Sie können ein Image nicht aus einer Containerregistrierung pullen, oder die Adresse konnte für eine Containerregistrierung nicht aufgelöst werden

Mögliche Probleme:
- Der Pfadname für die Containerregistrierung wird möglicherweise nicht richtig aufgelöst. Überprüfen Sie, ob für Imagenamen doppelte Schrägstriche verwendet werden und ob die Richtung der Schrägstriche auf den Linux- bzw. Windows-Hosts korrekt ist.
- Wenn eine Containerregistrierung hinter einem virtuellen Netzwerk einen privaten Endpunkt in einer [nicht unterstützten Region](../private-link/private-link-overview.md#availability) verwendet, sollten Sie die Containerregistrierung über das Portal mit dem Dienstendpunkt (öffentlicher Zugriff) konfigurieren und den Vorgang dann wiederholen.
- Nachdem Sie die Containerregistrierung hinter einem virtuellen Netzwerk platziert haben, führen Sie die [Azure Resource Manager-Vorlage](./how-to-network-security-overview.md) aus, damit der Arbeitsbereich mit der Instanz der Containerregistrierung kommunizieren kann.

### <a name="you-get-a-401-error-from-a-workspace-container-registry"></a>Sie erhalten einen 401-Fehler von der Containerregistrierung eines Arbeitsbereichs

Führen Sie die erneute Synchronisierung von Speicherschlüsseln mit [ws.sync_keys()](/python/api/azureml-core/azureml.core.workspace.workspace#sync-keys--) durch.

### <a name="the-environment-keeps-throwing-a-waiting-for-other-conda-operations-to-finish-error"></a>Fehler der Art „Warten auf den Abschluss anderer Conda-Vorgänge …“ in der Umgebung

Wenn gerade ein Buildvorgang für ein Image durchgeführt wird, wird Conda vom SDK-Client gesperrt. Falls der Prozess abgestürzt ist oder vom Benutzer nicht richtig abgebrochen wurde, verbleibt Conda im gesperrten Zustand. Um dieses Problem zu beheben, müssen Sie die Sperrdatei manuell löschen. 

### <a name="your-custom-docker-image-isnt-in-the-registry"></a>Ihr benutzerdefiniertes Docker-Image befindet sich nicht in der Registrierung

Überprüfen Sie, ob das [richtige Tag](./how-to-use-environments.md#create-an-environment) verwendet wird und Folgendes gilt: `user_managed_dependencies = True`. Mit `Environment.python.user_managed_dependencies = True` wird Conda deaktiviert, und es werden die installierten Pakete des Benutzers verwendet.

### <a name="you-get-one-of-the-following-common-virtual-network-issues"></a>Sie erhalten eines der folgenden allgemeinen Probleme mit dem virtuellen Netzwerk

- Überprüfen Sie, ob sich das Speicherkonto, der Computecluster und die Containerregistrierung im selben Subnetz des virtuellen Netzwerks befinden.
- Wenn sich Ihre Containerregistrierung hinter einem virtuellen Netzwerk befindet, kann sie nicht direkt zum Erstellen von Images verwendet werden. Sie müssen den Computecluster verwenden, um Images zu erstellen.
- Der Speicher muss in den folgenden Fällen möglicherweise hinter einem virtuellen Netzwerk platziert werden:
    - Verwendung von Rückschlüssen oder Private Wheel
    - Auftreten von Dienstfehlern vom Typ „403: Nicht autorisiert“
    - Imagedetails können nicht aus Azure Container Registry abgerufen werden.

### <a name="the-image-build-fails-when-youre-trying-to-access-network-protected-storage"></a>Fehler beim Buildvorgang für das Image, wenn auf den geschützten Netzwerkspeicher zugegriffen werden soll

- Azure Container Registry-Tasks funktionieren hinter einem virtuellen Netzwerk nicht. Wenn der Benutzer seine Containerregistrierung hinter einem virtuellen Netzwerk platziert hat, muss er für die Imageerstellung den Computecluster verwenden.
- Der Speicher sollte sich hinter einem VNet befinden, damit daraus Abhängigkeiten gepullt werden können.

### <a name="you-cant-run-experiments-when-storage-has-network-security-enabled"></a>Ausführung von Experimenten nicht möglich, wenn für Speicher die Netzwerksicherheit aktiviert ist

Wenn Sie Docker-Standardimages nutzen und vom Benutzer verwaltete Abhängigkeiten aktiviert haben, verwenden Sie die [Diensttags](./how-to-network-security-overview.md) „MicrosoftContainerRegistry“ und „AzureFrontDoor.FirstParty“, um Azure Container Registry und die zugehörigen Abhängigkeiten auf die Positivliste zu setzen.

 Weitere Informationen finden Sie unter [Aktivieren virtueller Netzwerke](./how-to-network-security-overview.md).


## <a name="next-steps"></a>Nächste Schritte

- [Trainieren von Scikit-learn-Modellen im großen Stil mit Azure Machine Learning](how-to-train-scikit-learn.md)
- [Trainieren eines Modells mithilfe eines benutzerdefinierten Docker-Images](how-to-train-with-custom-image.md)
