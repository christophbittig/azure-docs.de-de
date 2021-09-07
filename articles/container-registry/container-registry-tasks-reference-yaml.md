---
title: 'YAML-Referenz: ACR Tasks'
description: Referenz für die Definition von Aufgaben in YAML für Azure Container Registry Tasks (ACR Tasks), einschließlich Aufgabeneigenschaften, Schritttypen, Schritteigenschaften und integrierter Variablen.
ms.topic: reference
ms.date: 07/08/2020
ms.openlocfilehash: 31e96c64ef8209e5e18add9508fe379f1eb0f414
ms.sourcegitcommit: 025a2bacab2b41b6d211ea421262a4160ee1c760
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/06/2021
ms.locfileid: "113302661"
---
# <a name="acr-tasks-reference-yaml"></a>Referenz zu ACR Tasks: YAML

Die Definition von mehrstufigen Aufgaben in ACR Tasks stellt einen containerbasierten Computegrundtyp bereit, der auf das Erstellen, Testen und Patchen von Containern ausgerichtet ist. In diesem Artikel werden die Befehle, Parameter, Eigenschaften und Syntax für die YAML-Dateien behandelt, in denen mehrstufige Aufgaben definiert werden.

Dieser Artikel enthält Referenzinformationen für das Erstellen von YAML-Dateien für mehrstufige Aufgaben für ACR Tasks. Eine Einführung in ACR Tasks finden Sie unter [ACR Tasks overview](container-registry-tasks-overview.md) (Übersicht über ACR Tasks).

## <a name="acr-taskyaml-file-format"></a>Format der Datei „acr-task.yaml“

ACR Tasks unterstützt die Deklaration von mehrstufigen Aufgaben in der YAML-Standardsyntax. Die Schritte einer Aufgabe werden in einer YAML-Datei definiert. Sie können die Aufgabe dann manuell ausführen, indem Sie die Datei an den Befehl [az acr run][az-acr-run] übergeben. Alternativ dazu können Sie die Datei verwenden, um mithilfe von [az acr task create][az-acr-task-create] eine Aufgabe zu erstellen, die nach einem Git-Commit, einer Aktualisierung des Basisimages oder gemäß einem Zeitplan automatisch ausgelöst wird. In diesem Artikel wird für die Datei, die die Schritte enthält, der Dateiname `acr-task.yaml` verwendet. ACR Tasks unterstützt jedoch jeden gültigen Dateinamen mit einer [unterstützten Erweiterung](#supported-task-filename-extensions).

Die Primitiven der obersten Ebene in `acr-task.yaml` sind **Aufgabeneigenschaften**, **Schritttypen** und **Schritteigenschaften**:

* [Aufgabeneigenschaften](#task-properties) gelten während der gesamten Aufgabenausführung für alle Schritte. Es sind mehrere globale Aufgabeneigenschaften verfügbar:
  * `version`
  * `stepTimeout`
  * `workingDirectory`
* [Aufgabenschritttypen](#task-step-types) stellen die Aktionstypen dar, die in einer Aufgabe ausgeführt werden können. Drei Schritttypen sind verfügbar:
  * `build`
  * `push`
  * `cmd`
* [Aufgabenschritteigenschaften](#task-step-properties) sind Parameter, die für einen einzelnen Schritt gelten. Es sind mehrere Eigenschaften verfügbar, beispielsweise:
  * `startDelay`
  * `timeout`
  * `when`
  * und viele mehr

Das folgende Codebeispiel zeigt das grundlegende Format einer Datei `acr-task.yaml` mit einigen allgemeinen Schritteigenschaften. Dieses Beispiel ist keine vollständige Darstellung aller verfügbaren Schritteigenschaften oder Verwendungsmöglichkeiten der Schritttypen, bietet aber eine kurze Übersicht über das grundlegende Dateiformat.

```yml
version: # acr-task.yaml format version.
stepTimeout: # Seconds each step may take.
steps: # A collection of image or container actions.
  - build: # Equivalent to "docker build," but in a multi-tenant environment
  - push: # Push a newly built or retagged image to a registry.
    when: # Step property that defines either parallel or dependent step execution.
  - cmd: # Executes a container, supports specifying an [ENTRYPOINT] and parameters.
    startDelay: # Step property that specifies the number of seconds to wait before starting execution.
```

### <a name="supported-task-filename-extensions"></a>Unterstützte Aufgabendateierweiterungen

ACR Tasks hat mehrere Dateierweiterungen reserviert (einschließlich `.yaml`), die als Aufgabendatei verarbeitet werden. Alle Erweiterungen, die *nicht* in der folgenden Liste enthalten sind, werden von ACR Tasks als Dockerfile-Dateien interpretiert: yaml, .yml, .toml, .json, .sh, .bash, .zsh, .ps1, .ps, .cmd, .bat, .ts, .js, .php, .py, .rb und .lua.

YAML ist derzeit das einzige Dateiformat, das von ACR Tasks unterstützt wird. Die anderen Dateierweiterungen sind zur möglichen zukünftigen Unterstützung reserviert.

## <a name="run-the-sample-tasks"></a>Ausführen der Beispielaufgaben

In den folgenden Abschnitten dieses Artikels wird auf mehrere Beispielaufgabendateien verwiesen. Die Beispielaufgaben sind in einem öffentlichen GitHub-Repository verfügbar: [Azure-Samples/acr-tasks][acr-tasks]. Sie können sie mit dem Befehl [az acr run][az-acr-run] der Azure-Befehlszeilenschnittstelle (Azure CLI) ausführen. Die Beispielbefehle sehen in etwa wie folgt aus:

```azurecli
az acr run -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

Die Formatierung der Beispielbefehle setzt voraus, dass Sie eine Standardregistrierung in der Azure CLI konfiguriert haben, und enthalten den `--registry`-Parameter daher nicht. Verwenden Sie zum Konfigurieren einer Standardregistrierung den Befehl [az config][az-config] mit dem Befehl `set`, der ein Schlüssel-Wert-Paar vom Typ `defaults.acr=REGISTRY_NAME` akzeptiert.

Der folgende Befehl konfiguriert die Azure CLI beispielsweise mit einer Standardregistrierung namens „myregistry“:

```azurecli
az config set defaults.acr=myregistry
```

## <a name="task-properties"></a>Aufgabeneigenschaften

Aufgabeneigenschaften werden in der Regel am Anfang einer Datei vom Typ `acr-task.yaml` aufgeführt und sind globale Eigenschaften, die während der gesamten Ausführung der Aufgabenschritte gelten. Einige dieser globalen Eigenschaften können in einem einzelnen Schritt außer Kraft gesetzt werden.

| Eigenschaft | type | Optional | BESCHREIBUNG | Außerkraftsetzung unterstützt | Standardwert |
| -------- | ---- | -------- | ----------- | ------------------ | ------------- |
| `version` | Zeichenfolge | Ja | Die Version der Datei `acr-task.yaml`, die vom ACR Tasks-Dienst analysiert wird. ACR Tasks versucht, die Abwärtskompatibilität zu gewährleisten. Dieser Wert ermöglicht es ACR Tasks jedoch, die Kompatibilität innerhalb einer definierten Version sicherzustellen. Ohne Angabe wird standardmäßig die neueste Version verwendet. | Nein | Keine |
| `stepTimeout` | int (Sekunden) | Ja | Die maximale Anzahl von Sekunden, für die ein Schritt ausgeführt werden kann. Wenn die `stepTimeout`-Eigenschaft für eine Aufgabe angegeben wird, wird damit die Standardeigenschaft `timeout` für alle Schritte festgelegt. Wenn die `timeout`-Eigenschaft für einen Schritt angegeben wird, setzt sie die für die Aufgabe angegebene `stepTimeout`-Eigenschaft außer Kraft.<br/><br/>Die Summe der „stepTimeout“-Werte für eine Aufgabe sollte dem Wert der Ausführungseigenschaft `timeout` der Aufgabe entsprechen (z. B. Festlegung per Übergabe von `--timeout` an den Befehl `az acr task create`). Wenn der Ausführungswert `timeout` der Aufgabe kleiner ist, hat sie Priorität.  | Ja | 600 (10 Minuten) |
| `workingDirectory` | Zeichenfolge | Ja | Das Arbeitsverzeichnis des Containers während der Laufzeit. Wenn die Eigenschaft für eine Aufgabe angegeben wird, legt sie die `workingDirectory`-Standardeigenschaft für alle Schritte fest. Wird die Eigenschaft für einen Schritt angegeben, überschreibt sie die für die Aufgabe angegebene Eigenschaft. | Ja | `c:\workspace` in Windows oder `/workspace` in Linux |
| `env` | [string, string, ...] | Ja |  Ein Array von Zeichenfolgen im Format `key=value`, die die Umgebungsvariablen für die Aufgabe definieren. Wenn die Eigenschaft für eine Aufgabe angegeben wird, legt sie die `env`-Standardeigenschaft für alle Schritte fest. Wird die Eigenschaft für einen Schritt angegeben, überschreibt sie sämtliche von der Aufgabe geerbten Umgebungsvariablen. | Ja | Keine |
| `secrets` | [Geheimnis, Geheimnis, ...] | Ja | Array mit [Geheimnisobjekten](#secret). | Nein | Keine |
| `networks` | [Netzwerk, Netzwerk, ...] | Ja | Array mit [Netzwerkobjekten](#network). | Nein | Keine |
| `volumes` | [volume, volume, ...] | Ja | Array aus [volume](#volume)-Objekten. Gibt Volumes mit Quellinhalten an, die in einen Schritt eingebunden werden sollen. | Nein | Keine |

### <a name="secret"></a>secret

Das Geheimnisobjekt hat folgende Eigenschaften:

| Eigenschaft | type | Optional | BESCHREIBUNG | Standardwert |
| -------- | ---- | -------- | ----------- | ------- |
| `id` | Zeichenfolge | Nein | Der Bezeichner des Geheimnisses. | Keine |
| `keyvault` | Zeichenfolge | Ja | Die Geheimnis-URL von Azure Key Vault. | Keine |
| `clientID` | Zeichenfolge | Ja | Die Client-ID der [benutzerseitig zugewiesenen verwalteten Identität](container-registry-tasks-authentication-managed-identity.md) für Azure-Ressourcen. | Keine |

### <a name="network"></a>Netzwerk

Das Netzwerkobjekt hat folgende Eigenschaften:

| Eigenschaft | type | Optional | BESCHREIBUNG | Standardwert |
| -------- | ---- | -------- | ----------- | ------- | 
| `name` | Zeichenfolge | Nein | Der Name des Netzwerks. | Keine |
| `driver` | Zeichenfolge | Ja | Der Treiber für die Netzwerkverwaltung. | Keine |
| `ipv6` | bool | Ja | Aktivierungsstatus der IPv6-Netzwerkfunktionen. | `false` |
| `skipCreation` | bool | Ja | Gibt an, ob die Netzwerkerstellung übersprungen werden soll. | `false` |
| `isDefault` | bool | Ja | Gibt an, ob das Netzwerk ein mit Azure Container Registry bereitgestelltes Standardnetzwerk ist. | `false` |

### <a name="volume"></a>Volume

Das volume-Objekt weist die folgenden Eigenschaften auf.

| Eigenschaft | type | Optional | BESCHREIBUNG | Standardwert |
| -------- | ---- | -------- | ----------- | ------- | 
| `name` | Zeichenfolge | Nein | Der Name des einzubindenden Volumes. Darf nur alphanumerische Zeichen sowie „-“ und „_“ enthalten. | Keiner |
| `secret` | map[string]string | Nein | Jeder Schlüssel der Zuordnung ist der Name einer im Volume erstellten und aufgefüllten Datei. Jeder Wert ist die Zeichenfolgenversion des Geheimnisses. Geheimniswerte müssen Base64-codiert sein. | Keiner |

## <a name="task-step-types"></a>Aufgabenschritttypen

ACR Tasks unterstützt drei Schritttypen. Jeder Schritttyp unterstützt mehrere Eigenschaften, die im Abschnitt zum jeweiligen Schritttyp erläutert werden.

| Schritttyp | BESCHREIBUNG |
| --------- | ----------- |
| [`build`](#build) | Erstellt ein Containerimage mit der vertrauten `docker build`-Syntax. |
| [`push`](#push) | Führt einen `docker push` von neu erstellten oder erneut gekennzeichneten Images in eine Containerregistrierung aus. Azure Container Registry, andere private Registrierungen und der öffentliche Docker-Hub werden unterstützt. |
| [`cmd`](#cmd) | Führt einen Container als Befehl aus, wobei Parameter an den `[ENTRYPOINT]` des Containers übergeben werden. Der Schritttyp `cmd` unterstützt Parameter wie `env` und `detach` sowie andere bekannte `docker run`-Befehlsoptionen, die Komponenten- und Funktionstests mit gleichzeitiger Containerausführung ermöglichen. |

## <a name="build"></a>build

Dieser Schritttyp erstellt ein Containerimage. Der Schritttyp `build` stellt eine mehrinstanzenfähige, sichere Methode zum Ausführen von `docker build` in der Cloud als Primitiven der ersten Klasse dar.

### <a name="syntax-build"></a>Syntax: build

```yml
version: v1.1.0
steps:
  - [build]: -t [imageName]:[tag] -f [Dockerfile] [context]
    [property]: [value]
```

Der Schritttyp `build` unterstützt die Parameter in der folgenden Tabelle. Darüber hinaus unterstützt der Schritttyp `build` alle Buildoptionen des Befehls [docker build](https://docs.docker.com/engine/reference/commandline/build/) (etwa `--build-arg`) zum Festlegen von Buildzeitvariablen.

| Parameter | BESCHREIBUNG | Optional |
| --------- | ----------- | :-------: |
| `-t` &#124; `--image` | Definiert das vollständig qualifizierte `image:tag` des erstellten Images.<br /><br />Da Images für interne Aufgabenprüfungen verwendet werden können (z. B. Funktionstests), erfordern nicht alle Images einen `push` in eine Registrierung. Um ein Image innerhalb einer Aufgabenausführung anzugeben, erfordert das Image jedoch einen Namen, auf den verwiesen werden kann.<br /><br />Im Gegensatz zu `az acr build` bietet die Ausführung von ACR Tasks kein Standardverhalten für Pushvorgänge. Das Standardszenario bei ACR Tasks setzt voraus, dass ein Image erstellt, überprüft und anschließend gepusht werden kann. Informationen zum optionalen Pushen von erstellten Images finden Sie im Abschnitt zu [push](#push). | Ja |
| `-f` &#124; `--file` | Gibt die Dockerfile-Datei an, die an `docker build` übergeben wird. Ist keine Datei angegeben, wird die Dockerfile-Standarddatei im Stamm des Kontexts verwendet. Wenn Sie ein Dockerfile angeben möchten, übergeben Sie den Dateinamen relativ zum Stamm des Kontexts. | Ja |
| `context` | Das an `docker build` übergebene Stammverzeichnis. Das Stammverzeichnis jeder Aufgabe wird auf ein freigegebenes Arbeitsverzeichnis ([WorkingDirectory](#task-step-properties)) festgelegt und enthält den Stamm des zugehörigen geklonten Git-Verzeichnisses. | Nein |

### <a name="properties-build"></a>Eigenschaften: build

Der Schritttyp `build` unterstützt die folgenden Eigenschaften. Details zu diesen Eigenschaften finden Sie im Abschnitt [Aufgabenschritteigenschaften](#task-step-properties) dieses Artikels.

| Eigenschaften | type | Erforderlich |
| -------- | ---- | -------- |
| `detach` | bool | Optional |
| `disableWorkingDirectoryOverride` | bool | Optional |
| `entryPoint` | Zeichenfolge | Optional |
| `env` | [string, string, ...] | Optional |
| `expose` | [string, string, ...] | Optional |
| `id` | Zeichenfolge | Optional |
| `ignoreErrors` | bool | Optional |
| `isolation` | Zeichenfolge | Optional |
| `keep` | bool | Optional |
| `network` | Objekt (object) | Optional |
| `ports` | [string, string, ...] | Optional |
| `pull` | bool | Optional |
| `repeat` | INT | Optional |
| `retries` | INT | Optional |
| `retryDelay` | int (Sekunden) | Optional |
| `secret` | Objekt (object) | Optional |
| `startDelay` | int (Sekunden) | Optional |
| `timeout` | int (Sekunden) | Optional |
| `volumeMount` | Objekt (object) | Optional |
| `when` | [string, string, ...] | Optional |
| `workingDirectory` | Zeichenfolge | Optional |

### <a name="examples-build"></a>Beispiele: build

#### <a name="build-image---context-in-root"></a>Erstellen eines Images – Kontext im Stammverzeichnis

```azurecli
az acr run -f build-hello-world.yaml https://github.com/AzureCR/acr-tasks-sample.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-hello-world.yaml)]

#### <a name="build-image---context-in-subdirectory"></a>Erstellen eines Images – Kontext in einem Unterverzeichnis

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world -f hello-world.dockerfile ./subDirectory
```

## <a name="push"></a>push

Dieser Schritttyp pusht ein oder mehrere erstellte oder erneut gekennzeichnete Images in eine Containerregistrierung. Er unterstützt das Pushen in private Registrierungen wie Azure Container Registry oder den öffentlichen Docker-Hub.

### <a name="syntax-push"></a>Syntax: push

Der Schritttyp `push` unterstützt eine Sammlung von Images. Die YAML-Sammlungssyntax unterstützt Inlineformate und geschachtelte Formate. Das Pushen eines einzelnen Images wird in der Regel mithilfe von Inlinesyntax dargestellt:

```yml
version: v1.1.0
steps:
  # Inline YAML collection syntax
  - push: ["$Registry/hello-world:$ID"]
```

Verwenden Sie zum Pushen mehrerer Images eine geschachtelte Syntax, um die Lesbarkeit zu verbessern:

```yml
version: v1.1.0
steps:
  # Nested YAML collection syntax
  - push:
    - $Registry/hello-world:$ID
    - $Registry/hello-world:latest
```

### <a name="properties-push"></a>Eigenschaften: push

Der Schritttyp `push` unterstützt die folgenden Eigenschaften. Details zu diesen Eigenschaften finden Sie im Abschnitt [Aufgabenschritteigenschaften](#task-step-properties) dieses Artikels.

| Eigenschaft | type | Erforderlich |
| -------- | ---- | -------- |
| `env` | [string, string, ...] | Optional |
| `id` | Zeichenfolge | Optional |
| `ignoreErrors` | bool | Optional |
| `startDelay` | int (Sekunden) | Optional |
| `timeout` | int (Sekunden) | Optional |
| `when` | [string, string, ...] | Optional |

### <a name="examples-push"></a>Beispiele: push

#### <a name="push-multiple-images"></a>Pushen mehrerer Images

```azurecli
az acr run -f build-push-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-push-hello-world.yaml)]

#### <a name="build-push-and-run"></a>Erstellen, Pushen und Ausführen

```azurecli
az acr run -f build-run-hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/build-run-hello-world.yaml -->
[!code-yml[task](~/acr-tasks/build-run-hello-world.yaml)]

## <a name="cmd"></a>cmd

Der Schritttyp `cmd` führt einen Container aus.

### <a name="syntax-cmd"></a>Syntax: cmd

```yml
version: v1.1.0
steps:
  - [cmd]: [containerImage]:[tag (optional)] [cmdParameters to the image]
```

### <a name="properties-cmd"></a>Eigenschaften: cmd

Der Schritttyp `cmd` unterstützt die folgenden Eigenschaften:

| Eigenschaft | type | Erforderlich |
| -------- | ---- | -------- |
| `detach` | bool | Optional |
| `disableWorkingDirectoryOverride` | bool | Optional |
| `entryPoint` | Zeichenfolge | Optional |
| `env` | [string, string, ...] | Optional |
| `expose` | [string, string, ...] | Optional |
| `id` | Zeichenfolge | Optional |
| `ignoreErrors` | bool | Optional |
| `isolation` | Zeichenfolge | Optional |
| `keep` | bool | Optional |
| `network` | Objekt (object) | Optional |
| `ports` | [string, string, ...] | Optional |
| `pull` | bool | Optional |
| `repeat` | INT | Optional |
| `retries` | INT | Optional |
| `retryDelay` | int (Sekunden) | Optional |
| `secret` | Objekt (object) | Optional |
| `startDelay` | int (Sekunden) | Optional |
| `timeout` | int (Sekunden) | Optional |
| `volumeMount` | Objekt (object) | Optional |
| `when` | [string, string, ...] | Optional |
| `workingDirectory` | Zeichenfolge | Optional |

Details zu diesen Eigenschaften finden Sie im Abschnitt [Aufgabenschritteigenschaften](#task-step-properties) dieses Artikels.

### <a name="examples-cmd"></a>Beispiele: cmd

#### <a name="run-hello-world-image"></a>Ausführen des Images „hello-world“

Dieser Befehl führt die Aufgabendatei `hello-world.yaml` aus, die auf das Image [hello-world](https://hub.docker.com/_/hello-world/) im Docker-Hub verweist.

```azurecli
az acr run -f hello-world.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.yaml -->
[!code-yml[task](~/acr-tasks/hello-world.yaml)]

#### <a name="run-bash-image-and-echo-hello-world"></a>Ausführen des Images „bash“ und Zurückgeben von „hello world“

Dieser Befehl führt die Aufgabendatei `bash-echo.yaml` aus, die auf das Image [bash](https://hub.docker.com/_/bash/) im Docker-Hub verweist.

```azurecli
az acr run -f bash-echo.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo.yaml -->
[!code-yml[task](~/acr-tasks/bash-echo.yaml)]

#### <a name="run-specific-bash-image-tag"></a>Ausführen eines spezifischen Bash-Imagetags

Geben Sie zum Ausführen einer bestimmten Imageversion das Tag im `cmd`-Schritt an.

Dieser Befehl führt die Aufgabendatei `bash-echo-3.yaml` aus, die auf das Image [bash:3.0](https://hub.docker.com/_/bash/) im Docker-Hub verweist.

```azurecli
az acr run -f bash-echo-3.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo-3.yaml -->
[!code-yml[task](~/acr-tasks/bash-echo-3.yaml)]

#### <a name="run-custom-images"></a>Ausführen von benutzerdefinierten Images

Die Schritttyp `cmd` verweist anhand des `docker run`-Standardformats auf Images. Für Images ohne vorangestellte Registrierung wird angenommen, dass sie aus „docker.io“ stammen. Das vorherige Beispiel könnte auch wie folgt dargestellt werden:

```yml
version: v1.1.0
steps:
  - cmd: docker.io/bash:3.0 echo hello world
```

Durch die Verwendung der `docker run`-Standardkonvention für Imageverweise kann `cmd` Images aus einer privaten Registrierung oder aus dem öffentlichen Docker-Hub ausführen. Wenn Sie auf Images in derselben Registrierung verweisen, in der ACR Tasks ausgeführt wird, müssen Sie keine Anmeldeinformationen für die Registrierung angeben.

* Führen Sie ein Image aus einer Azure-Containerregistrierung aus. Im folgenden Beispiel wird von einer Registrierung mit dem Namen `myregistry` und dem benutzerdefinierten Image `myimage:mytag` ausgegangen.

    ```yml
    version: v1.1.0
    steps:
        - cmd: myregistry.azurecr.io/myimage:mytag
    ```

* Generalisieren Sie den Verweis auf die Registrierung mit einer Run-Variable oder einem Alias.

    Anstatt den Namen Ihrer Registrierung in der Datei `acr-task.yaml` hartzucodieren, können Sie eine [Run-Variable](#run-variables) oder einen [Alias](#aliases) verwenden, damit der Name besser portiert werden kann. Die `Run.Registry`-Variable oder der `$Registry`-Alias wird zur Laufzeit auf den Namen der Registrierung erweitert, in der die Aufgabe ausgeführt wird.

    Um die vorherige Aufgabe zu generalisieren, damit sie in jeder Azure-Containerregistrierung funktioniert, verweisen Sie z. B. im Imagenamen auf die $Registry-Variable:

    ```yml
    version: v1.1.0
    steps:
      - cmd: $Registry/myimage:mytag
    ```

#### <a name="access-secret-volumes"></a>Zugreifen auf Geheimnisvolumes

Die Eigenschaft `volumes` ermöglicht die Angabe von Volumes und deren Geheimnisinhalten für die Schritte `build` und `cmd` in einer Aufgabe. In jedem Schritt listet eine optionale `volumeMounts`-Eigenschaft die Volumes und die entsprechenden Containerpfade auf, die in diesem Schritt in den Container eingebunden werden sollen. Geheimnisse werden im Einbindungspfad jedes Volumes als Dateien bereitgestellt.

Führen Sie eine Aufgabe aus, und binden Sie zwei Geheimnisse in einen Schritt ein: ein in einem Schlüsseltresor gespeichertes und ein in der Befehlszeile angegebenes Geheimnis:

```azurecli
az acr run -f mounts-secrets.yaml --set-secret mysecret=abcdefg123456 https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/mounts-secrets.yaml -->
[!code-yml[task](~/acr-tasks/mounts-secrets.yaml)]

## <a name="task-step-properties"></a>Aufgabenschritteigenschaften

Jeder Schritttyp unterstützt mehrere dem jeweiligen Typ entsprechende Eigenschaften. In der folgenden Tabelle werden alle verfügbaren Schritteigenschaften beschrieben. Nicht alle Schritttypen unterstützen alle Eigenschaften. Informationen zu den verfügbaren Eigenschaften für die einzelnen Schritttypen finden Sie in den Referenzabschnitten zu den Schritttypen [cmd](#cmd), [build](#build) und [push](#push).

| Eigenschaft | type | Optional | BESCHREIBUNG | Standardwert |
| -------- | ---- | -------- | ----------- | ------- |
| `detach` | bool | Ja | Gibt an, ob der Container bei der Ausführung getrennt werden soll. | `false` |
| `disableWorkingDirectoryOverride` | bool | Ja | Gibt an, ob die `workingDirectory`-Überschreibungsfunktion deaktiviert werden soll. Verwenden Sie diese Eigenschaft in Kombination mit `workingDirectory`, um sämtliche Aspekte des Arbeitsverzeichnisses des Containers zu steuern. | `false` |
| `entryPoint` | Zeichenfolge | Ja | Setzt den `[ENTRYPOINT]` des Containers für einen Schritt außer Kraft. | Keine |
| `env` | [string, string, ...] | Ja | Ein Array von Zeichenfolgen im `key=value`-Format, die die Umgebungsvariablen für den Schritt definieren. | Keine |
| `expose` | [string, string, ...] | Ja | Array mit verfügbar gemachten Ports aus dem Container. |  Keine |
| [`id`](#example-id) | Zeichenfolge | Ja | Identifiziert den Schritt innerhalb der Aufgabe eindeutig. Andere Schritte in der Aufgabe können auf die `id` eines Schritts verweisen, beispielsweise zur Abhängigkeitsüberprüfung mit `when`.<br /><br />Die `id` ist auch der Name des ausgeführten Containers. Prozesse, die in anderen Containern in der Aufgabe ausgeführt werden, können beispielsweise als DNS-Hostnamen auf die `id` verweisen oder durch einen Verweis mit Docker-Protokollen [Id] auf den Container zugreifen. | `acb_step_%d`, wobei `%d` der nullbasierte Index des Schritts (von oben nach unten) in der YAML-Datei ist. |
| `ignoreErrors` | bool | Ja | Gibt an, ob der Schritt als erfolgreich markiert werden soll (unabhängig davon, ob bei der Containerausführung ein Fehler aufgetreten ist). | `false` |
| `isolation` | Zeichenfolge | Ja | Die Isolationsstufe des Containers. | `default` |
| `keep` | bool | Ja | Gibt an, ob der Container des Schritts nach der Ausführung beibehalten werden soll. | `false` |
| `network` | Objekt (object) | Ja | Gibt ein Netzwerk an, in dem der Container ausgeführt wird. | Keine |
| `ports` | [string, string, ...] | Ja | Array mit Ports, die aus dem Container für den Host veröffentlicht werden. |  Keine |
| `pull` | bool | Ja | Gibt an, ob vor dem Ausführen des Containers ein Pullvorgang erzwungen werden soll, um jegliches Zwischenspeicherverhalten zu verhindern. | `false` |
| `privileged` | bool | Ja | Gibt an, ob der Container im privilegierten Modus ausgeführt werden soll. | `false` |
| `repeat` | INT | Ja | Die Anzahl von Wiederholungsversuchen für die Containerausführung. | 0 |
| `retries` | INT | Ja | Die Anzahl von Wiederholungsversuchen im Falle einer nicht erfolgreichen Containerausführung. Ein erneuter Versuch findet nur statt, wenn der Exitcode des Containers nicht Null ist. | 0 |
| `retryDelay` | int (Sekunden) | Ja | Die Verzögerung zwischen erneuten Ausführungsversuchen für einen Container (in Sekunden). | 0 |
| `secret` | Objekt (object) | Ja | Gibt ein Azure Key Vault-Geheimnis oder eine [verwaltete Identität für Azure-Ressourcen](container-registry-tasks-authentication-managed-identity.md) an. | Keine |
| `startDelay` | int (Sekunden) | Ja | Verzögerung der Ausführung eines Containers (in Sekunden). | 0 |
| `timeout` | int (Sekunden) | Ja | Maximale Anzahl von Sekunden, für die ein Schritt ausgeführt werden kann, bevor er beendet wird. | 600 |
| [`when`](#example-when) | [string, string, ...] | Ja | Konfiguriert die Abhängigkeit eines Schritts von einem oder mehreren anderen Schritten innerhalb der Aufgabe. | Keine |
| `user` | Zeichenfolge | Ja | Der Benutzername oder die Benutzer-ID eines Containers. | Keine |
| `workingDirectory` | Zeichenfolge | Ja | Legt das Arbeitsverzeichnis für einen Schritt fest. ACR Tasks erstellt standardmäßig ein Stammverzeichnis als Arbeitsverzeichnis. Wenn Ihr Buildvorgang mehrere Schritte umfasst, können Schritte an früherer Stelle im Vorgang jedoch Artefakte für spätere Schritte freigeben, indem dasselbe Arbeitsverzeichnisses angegeben wird. | `c:\workspace` in Windows oder `/workspace` in Linux |

### <a name="volumemount"></a>volumeMount

Das volumeMount-Objekt weist die folgenden Eigenschaften auf.

| Eigenschaft | type | Optional | BESCHREIBUNG | Standardwert |
| -------- | ---- | -------- | ----------- | ------- | 
| `name` | Zeichenfolge | Nein | Der Name des einzubindenden Volumes. Dieser muss genau mit dem Namen in einer `volumes`-Eigenschaft übereinstimmen. | Keine |
| `mountPath`   | Zeichenfolge | nein | Der absolute Pfad zum Einbinden von Dateien in den Container.  | Keiner |

### <a name="examples-task-step-properties"></a>Beispiele: Aufgabenschritteigenschaften

#### <a name="example-id"></a>Beispiel: id

Im folgenden Beispiel werden zwei Images erstellt und ein Funktionstestimage instanziiert. Jeder Schritt wird durch eine eindeutige `id` identifiziert, auf die andere Schritte in der Aufgabe in ihrer `when`-Eigenschaft verweisen.

```azurecli
az acr run -f when-parallel-dependent.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel-dependent.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel-dependent.yaml)]

#### <a name="example-when"></a>Beispiel: when

Die `when`-Eigenschaft gibt die Abhängigkeit eines Schritts von anderen Schritten innerhalb der Aufgabe an. Sie unterstützt zwei Parameterwerte:

* `when: ["-"]`: Gibt an, dass keine Abhängigkeit von anderen Schritten besteht. Ein Schritt mit `when: ["-"]` wird sofort ausgeführt und ermöglicht die gleichzeitige Ausführung von Schritten.
* `when: ["id1", "id2"]`: Gibt an, dass der Schritt von den Schritten mit `id` „id1“ und `id` „id2“ abhängig ist. Dieser Schritt wird erst ausgeführt, nachdem die Schritte „id1“ und „id2“ abgeschlossen wurden.

Wenn `when` nicht in einem Schritt angegeben ist, ist dieser Schritt vom Abschluss des vorangehenden Schritts in der Datei `acr-task.yaml` abhängig.

Sequenzielle Schrittausführung ohne `when`:

```azurecli
az acr run -f when-sequential-default.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-sequential-default.yaml -->
[!code-yml[task](~/acr-tasks/when-sequential-default.yaml)]

Sequenzielle Schrittausführung mit `when`:

```azurecli
az acr run -f when-sequential-id.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-sequential-id.yaml -->
[!code-yml[task](~/acr-tasks/when-sequential-id.yaml)]

Parallele Imageerstellung:

```azurecli
az acr run -f when-parallel.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel.yaml)]

Parallele Imageerstellung und Abhängigkeitstest:

```azurecli
az acr run -f when-parallel-dependent.yaml https://github.com/Azure-Samples/acr-tasks.git
```

<!-- SOURCE: https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel-dependent.yaml -->
[!code-yml[task](~/acr-tasks/when-parallel-dependent.yaml)]

## <a name="run-variables"></a>Run-Variablen

ACR Tasks enthält einen Standardsatz von Variablen, die für Aufgabenschritte verfügbar sind, wenn diese ausgeführt werden. Auf diese Variablen kann mithilfe des Formats `{{.Run.VariableName}}` zugegriffen werden, wobei `VariableName` eine der folgenden Variablen ist:

* `Run.ID`
* `Run.SharedVolume`
* `Run.Registry`
* `Run.RegistryName`
* `Run.Date`
* `Run.OS`
* `Run.Architecture`
* `Run.Commit`
* `Run.Branch`
* `Run.TaskName`

Die Variablennamen sind in der Regel selbsterklärend. Details zu häufig verwendeten Variablen finden Sie nachfolgend. Ab YAML `v1.1.0` können Sie anstelle der meisten Run-Variablen einen abgekürzten vordefinierten [Aufgabenalias](#aliases) verwenden. Anstelle von `{{.Run.Registry}}` können Sie beispielsweise den Alias `$Registry` verwenden.

### <a name="runid"></a>Run.ID

Jede Ausführung über `az acr run` oder triggerbasierte Ausführung von Aufgaben, die mit `az acr task create` erstellt wurden, verfügt über eine eindeutige ID. Die ID stellt die aktuelle Ausführung dar.

Diese Variable wird in der Regel zur eindeutigen Kennzeichnung eines Images verwendet:

```yml
version: v1.1.0
steps:
    - build: -t $Registry/hello-world:$ID .
```

### <a name="runsharedvolume"></a>Run.SharedVolume

Der eindeutige Bezeichner für ein freigegebenes Volume, auf das alle Aufgabenschritte zugreifen können. Das Volume wird in Windows unter `c:\workspace` bzw. in Linux unter `/workspace` eingebunden. 

### <a name="runregistry"></a>Run.Registry

Der vollqualifizierte Servername der Registrierung. Diese Variable wird in der Regel verwendet, um generisch auf die Registrierung zu verweisen, in der die Aufgabe ausgeführt wird.

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world:$ID .
```

### <a name="runregistryname"></a>Run.RegistryName

Der Name der Containerregistrierung. Wird normalerweise in Aufgabenschritten verwendet, für die kein vollqualifizierter Servername erforderlich ist, z. B. `cmd`-Schritte, mit denen Azure CLI-Befehle in Registrierungen ausgeführt werden.

```yml
version 1.1.0
steps:
# List repositories in registry
- cmd: az login --identity
- cmd: az acr repository list --name $RegistryName
```

### <a name="rundate"></a>Run.Date

Die aktuelle UTC-Zeit, zu der die Ausführung gestartet wurde.

### <a name="runcommit"></a>Run.Commit

Für eine Aufgabe, die durch einen Commit an ein GitHub-Repository ausgelöst wurde, die Commit-ID.

### <a name="runbranch"></a>Run.Branch

Für eine Aufgabe, die durch einen Commit an ein GitHub-Repository ausgelöst wurde, der Branchname.

## <a name="aliases"></a>Aliase

Ab `v1.1.0` unterstützt ACR Tasks Aliase, die für Aufgabenschritte verfügbar sind, wenn diese ausgeführt werden. Aliase ähneln in ihrem Konzept den in Bash und einigen anderen Befehlsshells unterstützten Befehlsverknüpfungen. 

Mit einem Alias können Sie einen Befehl oder eine Befehlsgruppe (einschließlich Optionen und Dateinamen) starten, indem Sie ein einzelnes Wort eingeben.

ACR Tasks unterstützen verschiedene vordefinierte Aliase und auch benutzerdefinierte Aliase, die Sie erstellen.

### <a name="predefined-aliases"></a>Vordefinierte Aliase

Folgende Aufgabenaliase stehen zur Verwendung anstelle von [Run-Variablen](#run-variables) zur Verfügung:

| Alias | Run-Variable |
| ----- | ------------ |
| `ID` | `Run.ID` |
| `SharedVolume` | `Run.SharedVolume` |
| `Registry` | `Run.Registry` |
| `RegistryName` | `Run.RegistryName` |
| `Date` | `Run.Date` |
| `OS` | `Run.OS` |
| `Architecture` | `Run.Architecture` |
| `Commit` | `Run.Commit` |
| `Branch` | `Run.Branch` |

Stellen Sie in Aufgabenschritten wie im folgenden Beispiel einem Alias die `$`-Anweisung voran:

```yml
version: v1.1.0
steps:
  - build: -t $Registry/hello-world:$ID -f hello-world.dockerfile .
```

### <a name="image-aliases"></a>Imagealiase

Jeder der folgenden Aliase verweist auf ein stabiles Image in Microsoft Container Registry (MCR). Sie können auf jeden dieser Aliase im Abschnitt `cmd` einer Aufgabendatei ohne Verwendung einer Anweisung verweisen.

| Alias | Image |
| ----- | ----- |
| `acr` | `mcr.microsoft.com/acr/acr-cli:0.1` |
| `az` | `mcr.microsoft.com/acr/azure-cli:a80af84` |
| `bash` | `mcr.microsoft.com/acr/bash:a80af84` |
| `curl` | `mcr.microsoft.com/acr/curl:a80af84` |

In der folgenden Beispielaufgabe werden mehrere Aliase zum [Bereinigen](container-registry-auto-purge.md) von Imagetags, die älter als 7 Tage sind, im Repository `samples/hello-world` in der Ausführungsregistrierung verwendet:

```yml
version: v1.1.0
steps:
  - cmd: acr tag list --registry $RegistryName --repository samples/hello-world
  - cmd: acr purge --registry $RegistryName --filter samples/hello-world:.* --ago 7d
```

### <a name="custom-alias"></a>Benutzerdefinierter Alias

Definieren Sie einen benutzerdefinierten Alias in der YAML-Datei, und verwenden Sie ihn wie im folgenden Beispiel gezeigt. Ein Alias darf nur alphanumerische Zeichen enthalten. Als Standardanweisung zum Erweitern eines Alias wird das `$`-Zeichen verwendet.

```yml
version: v1.1.0
alias:
  values:
    repo: myrepo
steps:
  - build: -t $Registry/$repo/hello-world:$ID -f Dockerfile .
```

Sie können eine Verknüpfung mit einer lokalen YAML-Datei oder einer YAML-Remotedatei für benutzerdefinierte Aliasdefinitionen herstellen. Im folgenden Beispiel wird eine Verknüpfung mit einer YAML-Datei in Azure Blob Storage hergestellt:

```yml
version: v1.1.0
alias:
  src:  # link to local or remote custom alias files
    - 'https://link/to/blob/remoteAliases.yml?readSasToken'
[...]
```

## <a name="next-steps"></a>Nächste Schritte

Eine Übersicht über mehrstufige Aufgaben finden Sie unter [Run multi-step build, test, and patch tasks in ACR Tasks](container-registry-tasks-multi-step.md) (Ausführen von mehrstufigen Build-, Test- und Patchaufgaben in ACR Tasks).

Informationen zu einstufigen Buildaufgaben finden Sie unter [ACR Tasks overview](container-registry-tasks-overview.md) (Übersicht über ACR Tasks).



<!-- IMAGES -->

<!-- LINKS - External -->
[acr-tasks]: https://github.com/Azure-Samples/acr-tasks

<!-- LINKS - Internal -->
[az-acr-run]: /cli/azure/acr#az_acr_run
[az-acr-task-create]: /cli/azure/acr/task#az_acr_task_create
[az-config]: /cli/azure/reference-index#az_config
