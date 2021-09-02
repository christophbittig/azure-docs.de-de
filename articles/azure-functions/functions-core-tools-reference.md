---
title: Referenz zu Azure Functions Core Tools
description: Referenzdokumentation, die Azure Functions Core Tools („func.exe“) unterstützt.
ms.topic: reference
ms.date: 07/13/2021
ms.openlocfilehash: 90d4a9575c2fe6bb2dc4fbd18132e3bc21e4a07c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122349800"
---
# <a name="azure-functions-core-tools-reference"></a>Referenz zu Azure Functions Core Tools

Dieser Artikel enthält Referenzdokumentation für die Azure Functions Core Tools, mit denen Sie Azure Functions-Projekte auf Ihrem lokalen Computer entwickeln, verwalten und bereitstellen können. Weitere Informationen zur Verwendung der Core Tools finden Sie unter [Arbeiten mit Azure Functions Core Tools](functions-run-local.md). 

Core Tools-Befehle sind in den folgenden Kontexten organisiert, die jeweils einen eindeutigen Satz von Aktionen bereitstellen.

| Befehlskontext | BESCHREIBUNG |
| ----- | ----- |
| [`func`](#func-init) | Befehle, die zum Erstellen und Ausführen von Funktionen auf Ihrem lokalen Computer verwendet werden. |
| [`func azure`](#func-azure-functionapp-fetch-app-settings) | Befehle zum Arbeiten mit Azure-Ressourcen, einschließlich Veröffentlichung. |
| [`func durable`](#func-durable-delete-task-hub)    | Befehle zum Arbeiten mit [Durable Functions](./durable/durable-functions-overview.md). |
| [`func extensions`](#func-extensions-install) | Befehle zum Installieren und Verwalten von Erweiterungen. |
| [`func kubernetes`](#func-kubernetes-deploy) | Befehle zum Arbeiten mit Kubernetes und Azure Functions. |
| [`func settings`](#func-settings-decrypt)   | Befehle zum Verwalten von Umgebungseinstellungen für den lokalen Functions-Host. |
| [`func templates`](#func-templates-list)  | Befehle zum Auflisten verfügbarer Funktionsvorlagen. |

Bevor Sie die Befehle in diesem Artikel verwenden, müssen Sie die [Core Tools installieren](functions-run-local.md#install-the-azure-functions-core-tools). 

## <a name="func-init"></a>func init 

Erstellt ein neues Functions-Projekt in einer bestimmten Sprache.

```command
func init <PROJECT_FOLDER>
```

Wenn Sie `<PROJECT_FOLDER>` angeben, wird das Projekt in einem neuen Ordner mit diesem Namen erstellt. Andernfalls wird der aktuelle Ordner verwendet.

`func init` unterstützt die folgenden Optionen, die nur unter Version 3.x/2.x verfügbar sind, sofern nicht anders angegeben:

| Option     | BESCHREIBUNG                            |
| ------------ | -------------------------------------- |
| **`--csx`** | Erstellt .NET-Funktionen als C#-Skript, dies entspricht dem Verhalten in Version 1.x. Nur mit `--worker-runtime dotnet` gültig. |
| **`--docker`** | Erstellt ein Dockerfile für einen Container mithilfe eines Basisimages, das auf dem ausgewählten Wert für `--worker-runtime` basiert. Verwenden Sie diese Option, wenn Sie die Veröffentlichung in einem benutzerdefinierten Linux-Container planen. |
| **`--docker-only`** |  Fügt einem vorhandenen Projekt eine Dockerfile hinzu. Fordert die Worker-Runtime an, wenn sie nicht angegeben oder in „local.settings.json“ festgelegt wurde. Verwenden Sie diese Option, wenn Sie die Veröffentlichung eines vorhandenen Projekts in einem benutzerdefinierten Linux-Container planen. |
| **`--force`** | Initialisiert das Projekt, auch wenn es bereits Dateien enthält. Diese Einstellung überschreibt vorhandene Dateien mit dem gleichen Namen. Andere Dateien im Projektordner sind nicht betroffen. |
| **`--language`** | Initialisiert ein sprachspezifisches Projekt. Wird derzeit unterstützt, wenn `--worker-runtime` auf `node` festgelegt ist. Die Optionen sind `typescript` und `javascript`. Sie können auch `--worker-runtime javascript` oder `--worker-runtime typescript` verwenden.  |
| **`--managed-dependencies`**  | Installiert verwaltete Abhängigkeiten. Derzeit unterstützt nur die PowerShell-Workerruntime diese Funktion. |
| **`--source-control`** | Steuert, ob ein Git-Repository erstellt wird. Standardmäßig wird kein Repository erstellt. Im Fall von `true` wird ein Repository erstellt. |
| **`--worker-runtime`** | Legt die Runtime der Sprache für das Projekt fest. Unterstützte Werte sind: `csharp`, `dotnet`, `dotnet-isolated`, `javascript`, `node` (JavaScript), `powershell`, `python` und `typescript`. Verwenden Sie [Maven](functions-reference-java.md#create-java-functions) für Java. Verwenden Sie `custom`, um ein sprachunabhängiges Projekt nur mit den Projektdateien zu generieren. Wird kein Wert festgelegt, werden Sie während der Initialisierung zur Auswahl der Runtime aufgefordert. |
|

> [!NOTE]
> Wenn Sie eine der Optionen `--docker` oder `--dockerfile` verwenden, erstellen die Core Tools automatisch die Dockerfile-Datei für C#-, JavaScript-, Python- und PowerShell-Funktionen. Für Java-Funktionen müssen Sie die Dockerfile-Datei manuell erstellen. Verwenden Sie die Azure Functions-[Imageliste](https://github.com/Azure/azure-functions-docker), um das richtige Basisimage für Ihren Container zu finden, der Azure Functions ausführt.

## <a name="func-logs"></a>func logs

Ruft Protokolle für Funktionen ab, die in einem Kubernetes-Cluster ausgeführt werden.

```
func logs --platform kubernetes --name <APP_NAME>
``` 

Die `func logs`-Aktion unterstützt die folgenden Optionen:

| Option     | BESCHREIBUNG                            |
| ------------------------------------------ | -------------------------------------- |
| **`--platform`** | Hostingplattform für die Funktions-App Unterstützte Optionen: `kubernetes`. |
| **`--name`** | Funktions-App-Name in Azure. |

Weitere Informationen finden Sie unter [Azure Functions in Kubernetes mit KEDA](functions-kubernetes-keda.md).

## <a name="func-new"></a>func new

Erstellt eine neue Funktion im aktuellen Projekt basierend auf einer Vorlage.

```
func new
``` 

Die `func new`-Aktion unterstützt die folgenden Optionen:

| Option     | BESCHREIBUNG                            |
| ------------------------------------------ | -------------------------------------- |
| **`--authLevel`** | Hiermit können Sie die Autorisierungsebene für einen HTTP-Trigger festlegen. Folgende Werte werden unterstützt: `function`, `anonymous`, `admin`. Autorisierung wird bei lokaler Ausführung nicht erzwungen. Weitere Informationen finden Sie im Artikel über [HTTP-Bindungen](functions-bindings-http-webhook-trigger.md#authorization-keys). |
| **`--csx`** | (Version 2.x und höher) Generiert dieselben C#-Skriptvorlagen (.csx), die in Version 1.x und im Portal verwendet wurden. |
| **`--language`**, **`-l`**| Die Vorlagenprogrammiersprache, z.B. C#, F# oder JavaScript. Diese Option ist in Version 1.x erforderlich. In Version 2.x und höher verwenden Sie diese Option nicht, da die Sprache von der Workerruntime definiert wird. |
| **`--name`**, **`-n`** | Der Funktionsname. |
| **`--template`**, **`-t`** | Mit dem Befehl `func templates list` können Sie sich die vollständige Liste der verfügbaren Vorlagen für jede unterstützte Sprache anzeigen lassen.   |

Weitere Informationen finden Sie unter [Erstellen einer Funktion](functions-run-local.md#create-func).

## <a name="func-run"></a>func run

*Nur Version 1.x.*

Ermöglicht es Ihnen, eine Funktion direkt aufzurufen, ähnlich wie bei der Ausführung einer Funktion über die Registerkarte **Test** im Azure-Portal. Diese Aktion wird nur in Version 1.x unterstützt. Verwenden Sie für spätere Versionen `func start`, und [rufen Sie den Funktionsendpunkt direkt auf](functions-run-local.md#passing-test-data-to-a-function).

```command
func run
```

Die `func run`-Aktion unterstützt die folgenden Optionen:

| Option     | BESCHREIBUNG                            |
| ------------ | -------------------------------------- |
| **`--content`** | Inlineinhalt, der an die Funktion übergeben wird. |
| **`--debug`** | Anfügen eines Debuggers an den Hostprozess vor dem Ausführen der Funktion.|
| **`--file`** | Der als Inhalt zu verwendende Dateiname.|
| **`--no-interactive`** | Fordert keine Eingabe an, was für Automatisierungsszenarien nützlich ist.|
| **`--timeout`** | Wartezeit (in Sekunden), bis der lokale Functions-Host bereit ist.|

Um z.B. eine HTTP-ausgelöste Funktion aufzurufen und Inhaltstext zu übergeben, führen Sie folgenden Befehl aus:

```
func run MyHttpTrigger --content '{\"name\": \"Azure\"}'
```

## <a name="func-start"></a>func start

Startet den lokalen Laufzeithost und lädt das Funktionsprojekt in den aktuellen Ordner. 

Der jeweilige Befehl hängt von der [Laufzeitversion](functions-versions.md) ab.   

# <a name="v2x"></a>[v2.x+](#tab/v2)

```command
func start
```

`func start` unterstützt die folgenden Optionen:

| Option     | BESCHREIBUNG                            |
| ------------ | -------------------------------------- |
| **`--cert`** | Der Pfad zu einer PFX-Datei, die einen privaten Schlüssel enthält. Wird nur mit `--useHttps` unterstützt. |
| **`--cors`** | Eine durch Trennzeichen getrennte Liste der CORS-Ursprünge ohne Leerzeichen. |
| **`--cors-credentials`** | Ursprungsübergreifende authentifizierte Anforderungen mit Cookies und dem Authentifizierungsheader. |
| **`--dotnet-isolated-debug`** | Wenn auf `true` festgelegt, wird der .NET-Workerprozess angehalten, bis ein Debugger aus dem isolierten .NET-Projekt angefügt wird, das debuggt wird. |
| **`--enable-json-output`** | Gibt Konsolenprotokolle nach Möglichkeit im JSON-Format aus. |
| **`--enableAuth`** | Aktivieren Sie die Pipeline für die Unterstützung der vollständigen Authentifizierung. |
| **`--functions`** | Eine durch Leerzeichen getrennte Liste der zu ladenden Funktionen. |
| **`--language-worker`** | Argumente zum Konfigurieren des Spracharbeitsthreads. Beispielsweise können Sie das Debuggen für den Spracharbeiter aktivieren, indem Sie den [Debugport und andere erforderliche Argumente](https://github.com/Azure/azure-functions-core-tools/wiki/Enable-Debugging-for-language-workers) bereitstellen. |
| **`--no-build`** | Das aktuelle Projekt wird vor der Ausführung nicht erstellt. Nur für .NET-Klassenprojekte. Der Standardwert lautet `false`.  |
| **`--password`** | Entweder das Kennwort oder eine Datei, die das Kennwort für eine PFX-Datei enthält. Nur mit `--cert` verwendet. |
| **`--port`** | Der lokale Port, auf dem gelauscht werden soll. Standardwert: 7071. |
| **`--timeout`** | Das Timeout für den zu startenden Functions-Host in Sekunden. Standardwert: 20 Sekunden.|
| **`--useHttps`** | Erstellen Sie eine Bindung an `https://localhost:{port}` statt an `http://localhost:{port}`. Standardmäßig erstellt diese Option ein vertrauenswürdiges Zertifikat auf Ihrem Computer.|

Wenn das Projekt ausgeführt wird, können Sie [einzelne Funktionsendpunkte überprüfen](functions-run-local.md#passing-test-data-to-a-function).

# <a name="v1x"></a>[v1.x](#tab/v1)

```command
func host start
```

`func start` unterstützt die folgenden Optionen:

| Option     | BESCHREIBUNG                            |
| ------------ | -------------------------------------- |
| **`--cors`** | Eine durch Trennzeichen getrennte Liste der CORS-Ursprünge ohne Leerzeichen. |
| **`--port`** | Der lokale Port, auf dem gelauscht werden soll. Standardwert: 7071. |
| **`--pause-on-error`** | Vor Beenden des Prozesses für weitere Eingaben anhalten. Wird nur beim Starten von Core Tools in einer integrierten Entwicklungsumgebung (IDE) verwendet.|
| **`--script-root`** | Wird zum Angeben des Pfads zum Stamm der Funktions-App verwendet, die ausgeführt oder bereitgestellt werden soll. Diese Option wird für kompilierte Projekte verwendet, die Projektdateien in einem Unterordner generieren. Beispiel: Wenn Sie ein C#-Klassenbibliotheksprojekt erstellen, werden die Dateien „host.json“, „local.settings.json“ und „function.json“ in einem Unterordner des *Stammverzeichnisses* mit einem Pfad wie etwa dem folgenden generiert: `MyProject/bin/Debug/netstandard2.0`. Legen Sie in diesem Fall als Präfix `--script-root MyProject/bin/Debug/netstandard2.0` fest. Dies ist bei der Ausführung in Azure der Stamm der Funktions-App. |
| **`--timeout`** | Das Timeout für den zu startenden Functions-Host in Sekunden. Standardwert: 20 Sekunden.|
| **`--useHttps`** | Erstellen Sie eine Bindung an `https://localhost:{port}` statt an `http://localhost:{port}`. Standardmäßig erstellt diese Option ein vertrauenswürdiges Zertifikat auf Ihrem Computer.|

In Version 1.x können Sie auch den [`func run`-Befehl](#func-run) verwenden, um eine bestimmte Funktion auszuführen und ihr Testdaten zu übergeben. 

---

## <a name="func-azure-functionapp-fetch-app-settings"></a>func azure functionapp fetch-app-settings

Ruft Einstellungen aus einer bestimmten Funktions-App ab.

```command
func azure functionapp fetch-app-settings <APP_NAME>
```

Ein Beispiel finden Sie unter [Abrufen der Speicherverbindungszeichenfolgen](functions-run-local.md#get-your-storage-connection-strings).

Einstellungen werden in die Datei „local.settings.json“ für das Projekt heruntergeladen. Werte auf dem Bildschirm werden aus Sicherheitsgründen maskiert. Sie können die Einstellungen in der Datei „local.settings.json“ schützen, indem Sie [lokale Verschlüsselung aktivieren](#func-settings-encrypt). 

## <a name="func-azure-functionapp-list-functions"></a>func azure functionapp list-functions

Gibt eine Liste der Funktionen in der angegebenen Funktions-App zurück.

```command
func azure functionapp list-functions <APP_NAME>
```
## <a name="func-azure-functionapp-logstream"></a>func azure functionapp logstream

Verbindet die lokale Eingabeaufforderung mit Streamingprotokollen für die Funktions-App in Azure.

```command
func azure functionapp logstream <APP_NAME>
```

Das Standardtimeout für die Verbindung beträgt 2 Stunden. Sie können das Timeout ändern, indem Sie eine App-Einstellung mit dem Namen [SCM_LOGSTREAM_TIMEOUT](functions-app-settings.md#scm_logstream_timeout) mit einem Timeoutwert in Sekunden hinzufügen. Noch nicht unterstützt für Linux-Apps im Verbrauchsplan. Verwenden Sie für diese Apps die Option `--browser`, um Protokolle im Portal anzuzeigen.

Die `deploy`-Aktion unterstützt die folgenden Optionen:

| Option     | BESCHREIBUNG                            |
| ------------ | -------------------------------------- |
| **`--browser`** | Öffnen des Azure Application Insights-Livestreams für die Funktions-App im Standardbrowser. |

Weitere Informationen finden Sie unter [Aktivieren von Streamingprotokollen](functions-run-local.md#enable-streaming-logs).

## <a name="func-azure-functionapp-publish"></a>func azure functionapp publish 

Stellt ein Functions-Projekt für eine vorhandene Funktions-App-Ressource in Azure bereit. 

```command
func azure functionapp publish <FunctionAppName>
```

Weitere Informationen finden Sie unter [Bereitstellen von Projektdateien](functions-run-local.md#project-file-deployment).

Die folgenden Veröffentlichungsoptionen gelten basierend auf der Version:

# <a name="v2x"></a>[v2.x+](#tab/v2)

| Option     | BESCHREIBUNG                            |
| ------------ | -------------------------------------- |
| **`--additional-packages`** | Liste der zu installierenden Pakete beim Erstellen nativer Abhängigkeiten. Beispiel: `python3-dev libevent-dev`. |
| **`--build`**, **`-b`** | Führt beim Bereitstellen in einer Linux-Funktions-App eine Buildaktion durch. Akzeptiert: `remote` und `local`. |
| **`--build-native-deps`** | Überspringt das Generieren des Ordners `.wheels` beim Veröffentlichen von Python-Funktions-Apps. |
| **`--csx`** | Veröffentlicht ein C#-Skriptprojekt (.csx). |
| **`--force`** | In bestimmten Szenarien wird die Überprüfung vor der Veröffentlichung ignoriert. |
| **`--dotnet-cli-params`** | Beim Veröffentlichen kompilierter C#-Funktionen (CSPROJ-Dateien) rufen die Core Tools `dotnet build --output bin/publish` auf. Alle daran übergebenen Parameter werden an die Befehlszeile angefügt. |
|**`--list-ignored-files`** | Zeigt eine Liste mit Dateien an, die bei der Veröffentlichung ignoriert werden (basierend auf der `.funcignore`-Datei). |
| **`--list-included-files`** | Zeigt eine Liste mit Dateien an, die veröffentlicht werden (basierend auf der `.funcignore`-Datei). |
| **`--no-build`** | Das Projekt wird während der Veröffentlichung nicht erstellt. Für Python wird `pip install` nicht ausgeführt. |
| **`--nozip`** | Deaktiviert den Standardmodus `Run-From-Package`. |
| **`--overwrite-settings -y`** | Unterdrückt die Aufforderung zum Überschreiben von App-Einstellungen bei Verwendung von `--publish-local-settings -i`.|
| **`--publish-local-settings -i`** |  Einstellungen zur Veröffentlichung in Azure in „local.settings.json“. Wenn die Einstellung bereits vorhanden ist, werden Sie gefragt, ob sie überschrieben werden soll. Wenn Sie den Microsoft Azure-Speicheremulator verwenden, ändern Sie zunächst die App-Einstellung in eine [tatsächliche Speicherverbindung](functions-run-local.md#get-your-storage-connection-strings). |
| **`--publish-settings-only`**, **`-o`** |  Veröffentlicht nur die Einstellungen, der Inhalt wird übersprungen. Standard ist die Eingabeaufforderung. |
| **`--slot`** | Optionaler Name eines bestimmten Slots, in dem veröffentlicht werden soll. |

# <a name="v1x"></a>[v1.x](#tab/v1)

| Option     | BESCHREIBUNG                            |
| ------------ | -------------------------------------- |
| **`--overwrite-settings -y`** | Unterdrückt die Aufforderung zum Überschreiben von App-Einstellungen bei Verwendung von `--publish-local-settings -i`.|
| **`--publish-local-settings -i`** |  Einstellungen zur Veröffentlichung in Azure in „local.settings.json“. Wenn die Einstellung bereits vorhanden ist, werden Sie gefragt, ob sie überschrieben werden soll. Wenn Sie den Microsoft Azure-Speicheremulator verwenden, ändern Sie zunächst die App-Einstellung in eine [tatsächliche Speicherverbindung](functions-run-local.md#get-your-storage-connection-strings). |

---

## <a name="func-azure-storage-fetch-connection-string"></a>func azure storage fetch-connection-string    

Ruft die Verbindungszeichenfolge für das angegebene Azure-Speicherkonto ab.

```command
func azure storage fetch-connection-string <STORAGE_ACCOUNT_NAME>
```

## <a name="func-deploy"></a>func deploy

Stellt eine Funktions-App in einem benutzerdefinierten Linux-Container in einem Kubernetes-Cluster ohne KEDA bereit.

```command
func deploy --name <FUNCTION_APP> --platform kubernetes --registry <DOCKER_USER>
```

Dieser Befehl erstellt Ihr Projekt als benutzerdefinierten Container und veröffentlicht es mithilfe eines Standardskalierers oder mithilfe von KNative in einem Kubernetes-Cluster. Verwenden Sie zum Veröffentlichen in einem Cluster mit KEDA für dynamische Skalierung stattdessen den [`func kubernetes deploy`-Befehl](#func-kubernetes-deploy). Benutzerdefinierte Container müssen ein Dockerfile enthalten. Um eine App mit einer Dockerfile-Datei zu erstellen, verwenden Sie die Option `--dockerfile` mitdem [`func init`-Befehl](#func-init).     

Die `deploy`-Aktion unterstützt die folgenden Optionen:

| Option     | BESCHREIBUNG                            |
| ------------ | -------------------------------------- |
| **`--config`** | Legt eine optionale Bereitstellungskonfigurationsdatei fest. |
| **`--max`**  | Legt optional die maximale Anzahl von Funktions-App-Instanzen fest, in denen die Bereitstellung erfolgen soll. |
| **`--min`**  | Legt optional die Mindestanzahl von Funktions-App-Instanzen fest, in denen die Bereitstellung erfolgen soll. |
| **`--name`** | Name der Funktions-App (erforderlich). |
| **`--platform`** | Hostingplattform für die Funktions-App (erforderlich). Gültige Optionen sind `kubernetes` und `knative`.|
| **`--registry`** | Der Name einer Docker-Registrierung, bei der der aktuelle Benutzer angemeldet ist (erforderlich). |

Die Core Tools verwenden die lokale Docker-Befehlszeilenschnittstelle, um das Image zu erstellen und zu veröffentlichen. 

Stellen Sie sicher, dass Docker bereits lokal installiert ist. Führen Sie den Befehl `docker login` aus, um eine Verbindung mit Ihrem Konto herzustellen.

## <a name="func-durable-delete-task-hub"></a>func durable delete-task-hub

Löscht alle Speicherartefakte im Durable Functions-Aufgabenhub.

```command
func durable delete-task-hub
```

Die `delete-task-hub`-Aktion unterstützt die folgenden Optionen:

| Option     | BESCHREIBUNG                            |
| ------------ | -------------------------------------- |
| **`--connection-string-setting`** | Optionaler Name der Einstellung mit der zu verwendenden Speicherverbindungszeichenfolge. |
| **`--task-hub-name`** |             Optionaler Name des zu verwendenden Hubs für die dauerhafte Aufgabe. |

Weitere Informationen finden Sie in der [Dokumentation zu Durable Functions](./durable/durable-functions-instance-management.md#delete-a-task-hub).

## <a name="func-durable-get-history"></a>func durable get-history

Gibt den Verlauf der angegebenen Orchestrierungsinstanz zurück.

```command
func durable get-history --id <INSTANCE_ID>
```

Die `get-history`-Aktion unterstützt die folgenden Optionen:

| Option     | BESCHREIBUNG                            |
| ------------ | -------------------------------------- |
| **`--id`** | Gibt die ID einer Orchestrierungsinstanz an (erforderlich). |
| **`--connection-string-setting`** | Optionaler Name der Einstellung mit der zu verwendenden Speicherverbindungszeichenfolge. |
| **`--task-hub-name`** |             Optionaler Name des zu verwendenden Hubs für die dauerhafte Aufgabe. |

Weitere Informationen finden Sie in der [Dokumentation zu Durable Functions](./durable/durable-functions-instance-management.md#azure-functions-core-tools-1).

## <a name="func-durable-get-instances"></a>func durable get-instances

Gibt den Status aller Orchestrierungsinstanzen zurück. Unterstützt Paging mit dem Parameter `top`.

```command
func durable get-instances
```

Die `get-instances`-Aktion unterstützt die folgenden Optionen:

| Option     | BESCHREIBUNG                            |
| ------------ | -------------------------------------- |
| **`--continuation-token`** | Optionales Token, das eine bestimmte Seite bzw. einen bestimmten Abschnitt der zurückzugebenden Anforderungen angibt. |
| **`--connection-string-setting`** | Optionaler Name der App-Einstellung, die die zu verwendende Speicherverbindungszeichenfolge enthält. |
| **`--created-after`** | Ruft optional die nach diesem Datum bzw. dieser Uhrzeit (UTC) erstellten Instanzen ab. Alle nach ISO 8601 formatierten datetime-Werte werden akzeptiert. |
| **`--created-before`** | Ruft optional die vor einem bestimmten Datum bzw. einer bestimmten Uhrzeit (UTC) erstellten Instanzen ab. Alle nach ISO 8601 formatierten datetime-Werte werden akzeptiert. |
| **`--runtime-status`** | Ruft optional die Instanzen ab, deren Status mit einem bestimmten Status übereinstimmt, einschließlich `running`, `completed` und `failed`. Sie können ein oder mehrere durch Leerzeichen getrennte Status angeben. |
| **`--top`** | Begrenzen Sie optional die Anzahl von Datensätzen, die in einer bestimmten Anforderung zurückgegeben werden. |
| **`--task-hub-name`** | Optionaler Name des zu verwendenden Durable Functions-Aufgabenhubs. |

Weitere Informationen finden Sie in der [Dokumentation zu Durable Functions](./durable/durable-functions-instance-management.md#azure-functions-core-tools-2).

## <a name="func-durable-get-runtime-status"></a>func durable get-runtime-status  

Gibt den Status der angegebenen Orchestrierungsinstanz zurück.

```command
func durable get-runtime-status --id <INSTANCE_ID>
```

Die `get-runtime-status`-Aktion unterstützt die folgenden Optionen:

| Option     | BESCHREIBUNG                            |
| ------------ | -------------------------------------- |
| **`--connection-string-setting`** | Optionaler Name der Einstellung mit der zu verwendenden Speicherverbindungszeichenfolge. |
| **`--id`** | Gibt die ID einer Orchestrierungsinstanz an (erforderlich). |
| **`--show-input`** | Wenn festgelegt, enthält die Antwort die Eingabe der Funktion. |
| **`--show-output`** | Wenn festgelegt, enthält die Antwort den Ausführungsverlauf. |
| **`--task-hub-name`** | Optionaler Name des zu verwendenden Durable Functions-Aufgabenhubs. |

Weitere Informationen finden Sie in der [Dokumentation zu Durable Functions](./durable/durable-functions-instance-management.md#azure-functions-core-tools-1).

## <a name="func-durable-purge-history"></a>func durable purge-history

Bereinigt den Zustand der Orchestrierungsinstanz, den Verlauf und den Blobspeicher für Orchestrierungen, die älter als der angegebene Schwellenwert sind.

```command
func durable purge-history
```

Die `purge-history`-Aktion unterstützt die folgenden Optionen:

| Option     | BESCHREIBUNG                            |
| ------------ | -------------------------------------- |
| **`--connection-string-setting`** | Optionaler Name der Einstellung mit der zu verwendenden Speicherverbindungszeichenfolge. |
| **`--created-after`** | Löscht optional den Verlauf der nach diesem Datum bzw. dieser Uhrzeit (UTC) erstellten Instanzen. Alle nach ISO 8601 formatierten datetime-Werte werden akzeptiert. |
| **`--created-before`** | Löscht optional den Verlauf der vor diesem Datum bzw. dieser Uhrzeit (UTC) erstellten Instanzen. Alle nach ISO 8601 formatierten datetime-Werte werden akzeptiert.|
| **`--runtime-status`** | Löscht optional den Verlauf der Instanzen, deren Status mit einem bestimmten Status übereinstimmt, einschließlich `completd`, `terminated`, `canceled` und `failed`. Sie können ein oder mehrere durch Leerzeichen getrennte Status angeben. Wenn Sie `--runtime-status` nicht einschließen, wird der Instanzverlauf unabhängig vom Status gelöscht.|
| **`--task-hub-name`** | Optionaler Name des zu verwendenden Durable Functions-Aufgabenhubs. |

Weitere Informationen finden Sie in der [Dokumentation zu Durable Functions](./durable/durable-functions-instance-management.md#azure-functions-core-tools-7).

## <a name="func-durable-raise-event"></a>func durable raise-event         

Löst ein Ereignis für die angegebene Orchestrierungsinstanz aus.

```command
func durable raise-event --event-name <EVENT_NAME> --event-data <DATA>
```

Die `raise-event`-Aktion unterstützt die folgenden Optionen:

| Option     | BESCHREIBUNG                            |
| ------------ | -------------------------------------- |
| **`--connection-string-setting`** | Optionaler Name der Einstellung mit der zu verwendenden Speicherverbindungszeichenfolge. |
| **`--event-data`** | Daten, die an das Ereignis übergeben werden sollen, entweder inline oder aus einer JSON-Datei (erforderlich). Bei Dateien stellen Sie dem Pfad zur Datei ein kaufmännisches Und (`@`) als Präfix voran, z. B. `@path/to/file.json`. |
| **`--event-name`** | Der Name des auszulösenden Ereignisses (erforderlich).
| **`--id`** | Gibt die ID einer Orchestrierungsinstanz an (erforderlich). |
| **`--task-hub-name`** | Optionaler Name des zu verwendenden Durable Functions-Aufgabenhubs. |

Weitere Informationen finden Sie in der [Dokumentation zu Durable Functions](./durable/durable-functions-instance-management.md#azure-functions-core-tools-5).

## <a name="func-durable-rewind"></a>func durable rewind              

Spult die angegebene Orchestrierungsinstanz zurück.

```command
func durable rewind --id <INSTANCE_ID> --reason <REASON>
```

Die `rewind`-Aktion unterstützt die folgenden Optionen:

| Option     | BESCHREIBUNG                            |
| ------------ | -------------------------------------- |
| **`--connection-string-setting`** | Optionaler Name der Einstellung mit der zu verwendenden Speicherverbindungszeichenfolge. |
| **`--id`** | Gibt die ID einer Orchestrierungsinstanz an (erforderlich). |
| **`--reason`** | Der Grund für das Zurückspulen der Orchestrierungsinstanz (erforderlich).|
| **`--task-hub-name`** | Optionaler Name des zu verwendenden Durable Functions-Aufgabenhubs. |

Weitere Informationen finden Sie in der [Dokumentation zu Durable Functions](./durable/durable-functions-instance-management.md#azure-functions-core-tools-6).

## <a name="func-durable-start-new"></a>func durable start-new

Startet eine neue Instanz der angegebenen Orchestratorfunktion.

```command
func durable start-new --id <INSTANCE_ID> --function-name <FUNCTION_NAME> --input <INPUT>
```

Die `start-new`-Aktion unterstützt die folgenden Optionen:

| Option     | BESCHREIBUNG                            |
| ------------ | -------------------------------------- |
| **`--connection-string-setting`** | Optionaler Name der Einstellung mit der zu verwendenden Speicherverbindungszeichenfolge. |
| **`--function-name`** | Der Name der zu startenden Orchestratorfunktion (erforderlich).|
| **`--id`** | Gibt die ID einer Orchestrierungsinstanz an (erforderlich). |
| **`--input`** | Eingabe für die Orchestratorfunktion, entweder inline oder aus einer JSON-Datei (erforderlich). Bei Dateien stellen Sie dem Pfad zur Datei ein kaufmännisches Und (`@`) als Präfix voran, z. B. `@path/to/file.json`. |
| **`--task-hub-name`** | Optionaler Name des zu verwendenden Durable Functions-Aufgabenhubs. |

Weitere Informationen finden Sie in der [Dokumentation zu Durable Functions](./durable/durable-functions-instance-management.md#azure-functions-core-tools).

## <a name="func-durable-terminate"></a>func durable terminate

Beendet die angegebene Orchestrierungsinstanz.

```command
func durable terminate --id <INSTANCE_ID> --reason <REASON>
```

Die `terminate`-Aktion unterstützt die folgenden Optionen:

| Option     | BESCHREIBUNG                            |
| ------------ | -------------------------------------- |
| **`--connection-string-setting`** | Optionaler Name der Einstellung mit der zu verwendenden Speicherverbindungszeichenfolge. |
| **`--id`** | Gibt die ID einer Orchestrierungsinstanz an (erforderlich). |
| **`--reason`** | Der Grund für das Beenden der Orchestrierungsinstanz (erforderlich). |
| **`--task-hub-name`** | Optionaler Name des zu verwendenden Durable Functions-Aufgabenhubs. |

Weitere Informationen finden Sie in der [Dokumentation zu Durable Functions](./durable/durable-functions-instance-management.md#azure-functions-core-tools-4).

## <a name="func-extensions-install"></a>func extensions install

Installiert Functions-Erweiterungen in einem Nicht-C#-Klassenbibliotheksprojekt. 

Wenn möglich, sollten Sie stattdessen Erweiterungspakete verwenden. Weitere Informationen finden Sie unter [Erweiterungspakete](functions-bindings-register.md#extension-bundles).

Verwenden Sie für C#-Klassenbibliotheken und isolierte .NET-Projekte stattdessen NuGet-Paketinstallationsmethoden, z. B. `dotnet add package`.

Die `install`-Aktion unterstützt die folgenden Optionen:

| Option     | BESCHREIBUNG                            |
| ------------ | -------------------------------------- |
| **`--configPath`** | Pfad des Verzeichnisses, das die Datei „extensions.csproj“ enthält.|
| **`--csx`** |   Unterstützt C#-Skriptprojekte (CSX-Dateien). |
| **`--force`** |  Aktualisiert die Versionen vorhandener Erweiterungen. |
| **`--output`** |  Ausgabepfad für die Erweiterungen. |
| **`--package`** |   Bezeichner für ein bestimmtes Erweiterungspaket. Wenn nicht angegeben, werden alle Erweiterungen, auf die verwiesen wird, wie bei `func extensions sync` installiert.|
| **`--source`** |  NuGet-Feedquelle, wenn nicht NuGet.org verwendet wird.|
| **`--version`** |  Version des Erweiterungspakets. |

Es erfolgt keine Aktion, wenn ein Erweiterungspaket in Ihrer Datei „host.json“ definiert ist.

## <a name="func-extensions-sync"></a>func extensions sync

Installiert alle Erweiterungen, die der Funktions-App hinzugefügt wurden.

Die `sync`-Aktion unterstützt die folgenden Optionen:

| Option     | BESCHREIBUNG                            |
| ------------ | -------------------------------------- |
| **`--configPath`** | Pfad des Verzeichnisses, das die Datei „extensions.csproj“ enthält.|
| **`--csx`** |   Unterstützt C#-Skriptprojekte (CSX-Dateien). |
| **`--output`** |  Ausgabepfad für die Erweiterungen. |

Generiert eine fehlende Datei „extensions.csproj“ neu. Es erfolgt keine Aktion, wenn ein Erweiterungspaket in Ihrer Datei „host.json“ definiert ist.

## <a name="func-kubernetes-deploy"></a>func kubernetes deploy

Stellt ein Functions-Projekt mit KEDA als benutzerdefinierten Docker-Container in einem Kubernetes-Cluster zur Verfügung.

```command
func kubernetes deploy 
```

Dieser Befehl erstellt Ihr Projekt als benutzerdefinierten Container und veröffentlicht es mithilfe von KEDA für dynamische Skalierung in einem Kubernetes-Cluster. Verwenden Sie stattdessen den [`func deploy`-Befehl](#func-deploy), um die Veröffentlichung in einem Cluster mithilfe eines Standardskalierers oder mit KNative auszuführen. Benutzerdefinierte Container müssen ein Dockerfile enthalten. Um eine App mit einer Dockerfile-Datei zu erstellen, verwenden Sie die Option `--dockerfile` mitdem [`func init`-Befehl](#func-init). 

Die folgenden Kubernetes-Bereitstellungsoptionen sind verfügbar:

| Option     | BESCHREIBUNG                            |
| ------------ | -------------------------------------- |
| **`--dry-run`** | Zeigt optional die Bereitstellungsvorlage ohne Ausführung an. |
| **`--config-map-name`** | Optionaler Name einer vorhandenen Konfigurationszuordnung mit [Funktions-App-Einstellungen](functions-how-to-use-azure-function-app-settings.md#settings), die bei der Bereitstellung verwendet werden sollen. Erfordert `--use-config-map`. Das Standardverhalten ist das Erstellen von Einstellungen auf der Grundlage des `Values`-Objekts in der Datei [local.settings.json].|
| **`--cooldown-period`** | Der Abkühlungszeitraum (in Sekunden), nach dem alle Trigger nicht mehr aktiv sind, bevor die Bereitstellung wieder auf 0 (null) herunterskaliert wird ( Standardwert: 300 s). |
| **`--ignore-errors`** | Setzt die Bereitstellung fort, nachdem eine Ressource einen Fehler zurückgegeben hat. Das Standardverhalten ist, dass bei einem Fehler angehalten wird. |
| **`--image-name`** | Der Name des Images, das für die Podbereitstellung verwendet werden soll und aus dem Funktionen gelesen werden. |
| **`--keda-version`** | Legt die zu installierende KEDA-Version fest. Gültige Optionen sind `v1` und `v2` (Standard). |
| **`--keys-secret-name`** | Der Name einer Kubernetes-Geheimnissammlung, die zum Speichern von [Funktionszugriffsschlüsseln](functions-bindings-http-webhook-trigger.md#authorization-keys) verwendet werden soll. |
| **`--max-replicas`** | Legt die maximale Anzahl von Replikaten fest, auf die HPA (Horizontal Pod Autoscaler) skaliert wird. |
| **`--min-replicas`** | Legt die minimale Replikatanzahl fest, unter die HPA nicht skaliert wird. |
| **`--mount-funckeys-as-containervolume`** | Bindet die [Funktionszugriffsschlüssel](functions-bindings-http-webhook-trigger.md#authorization-keys) als Containervolume ein. |
| **`--name`** | Der Name, der für die Bereitstellung und andere Artefakte in Kubernetes verwendet wird. |
| **`--namespace`** | Legt den Kubernetes-Namespace fest, indem die Bereitstellung erfolgen soll. Standardmäßig wird der Standardnamespace verwendet. |
| **`--no-docker`** | Funktionen werden aus dem aktuellen Verzeichnis anstatt aus einem Image gelesen. Erfordert das Einbinden des Imagedateisystems. |
| **`--registry`** | Wenn festgelegt, wird ein Docker-Build ausgeführt, und das Image wird in eine Registrierung mit diesem Namen gepusht. Sie können `--registry` nicht mit `--image-name` verwenden. Verwenden Sie für Docker Ihren Benutzernamen. |
| **`--polling-interval`** | Das Abrufintervall (in Sekunden) zum Überprüfen von Nicht-HTTP-Triggern mit einem Standardwert von 30 Sekunden. |
| **`--pull-secret`** | Das Geheimnis, das für den Zugriff auf private Registrierungsanmeldeinformationen verwendet wird. |
| **`--secret-name`** | Der Name einer vorhandenen Kubernetes-Geheimnissammlung, die [Funktions-App-Einstellungen](functions-how-to-use-azure-function-app-settings.md#settings) enthält, die in der Bereitstellung verwendet werden sollen. Das Standardverhalten ist das Erstellen von Einstellungen auf der Grundlage des `Values`-Objekts in der Datei [local.settings.json]. |
| **`--show-service-fqdn`** | Zeigt die URLs von HTTP-Triggern mit dem Kubernetes-FQDN anstelle des Standardverhaltens der Verwendung einer IP-Adresse an. |
| **`--service-type`** | Legt den Typ von Kubernetes Service fest. Unterstützte Werte sind `ClusterIP`, `NodePort` und `LoadBalancer` (Standardwert). |
| **`--use-config-map`** | Verwenden Sie ein `ConfigMap`-Objekt (v1) anstelle eines `Secret`-Objekts (v1), um die [Funktions-App-Einstellungen](functions-how-to-use-azure-function-app-settings.md#settings) zu konfigurieren. Der Zuordnungsname wird mithilfe von `--config-map-name` festgelegt.|

Die Core Tools verwenden die lokale Docker-Befehlszeilenschnittstelle, um das Image zu erstellen und zu veröffentlichen. 

Stellen Sie sicher, dass Docker bereits lokal installiert ist. Führen Sie den Befehl `docker login` aus, um eine Verbindung mit Ihrem Konto herzustellen.

Weitere Informationen finden Sie unter [Bereitstellen einer Funktions-App in Kubernetes](functions-kubernetes-keda.md#deploying-a-function-app-to-kubernetes).

## <a name="func-kubernetes-install"></a>func kubernetes install

Installiert KEDA in einem Kubernetes-Cluster.

```command
func kubernetes install 
```

Installiert KEDA in dem Cluster, der in der kubectl-Konfigurationsdatei definiert ist.

Die `install`-Aktion unterstützt die folgenden Optionen:

| Option     | BESCHREIBUNG                            |
| ------------ | -------------------------------------- |
| **`--dry-run`** | Zeigt die Bereitstellungsvorlage ohne Ausführung an. |
| **`--keda-version`** | Legt die zu installierende KEDA-Version fest. Gültige Optionen sind `v1` und `v2` (Standard). |
| **`--namespace`** | Unterstützt die Installation in einem bestimmten Kubernetes-Namespace. Wenn nicht festgelegt, wird der Standardnamespace verwendet. |

Weitere Informationen finden Sie unter [Verwalten von KEDA und Funktionen in Kubernetes](functions-kubernetes-keda.md#managing-keda-and-functions-in-kubernetes).

## <a name="func-kubernetes-remove"></a>func kubernetes remove

Entfernt KEDA aus dem Kubernetes-Cluster, der in der kubectl-Konfigurationsdatei definiert ist.

```command
func kubernetes remove 
```

Entfernt KEDA aus dem Cluster, der in der kubectl-Konfigurationsdatei definiert ist.

Die `remove`-Aktion unterstützt die folgenden Optionen:

| Option     | BESCHREIBUNG                            |
| ------------ | -------------------------------------- |
| **`--namespace`** | Unterstützt die Deinstallation aus einem bestimmten Kubernetes-Namespace. Wenn nicht festgelegt, wird der Standardnamespace verwendet. | 

Weitere Informationen finden Sie unter [Deinstallieren von KEDA aus Kubernetes](functions-kubernetes-keda.md#uninstalling-keda-from-kubernetes).

## <a name="func-settings-add"></a>func settings add

Fügt der Sammlung `Values` in der Datei [local.settings.json] eine neue Einstellung hinzu.

```command
func settings add <SETTING_NAME> <VALUE>
```

Ersetzen Sie `<SETTING_NAME>` durch den Namen der App-Einstellung und `<VALUE>` durch den Wert der Einstellung. 

Die `add`-Aktion unterstützt die folgende Option:

| Option     | BESCHREIBUNG                            |
| ------------ | -------------------------------------- |
| **`--connectionString`** | Fügt das Name-Wert-Paar der `ConnectionStrings`-Sammlung anstatt der `Values`-Sammlung hinzu. Verwenden Sie die `ConnectionStrings`-Sammlung nur, wenn bestimmte Frameworks dies erfordern. Weitere Informationen finden Sie unter Datei [local.settings.json]. |

## <a name="func-settings-decrypt"></a>func settings decrypt

Entschlüsselt zuvor verschlüsselte Werte in der `Values`-Sammlung in der Datei [local.settings.json].

```command
func settings decrypt
```

Verbindungszeichenfolgenwerte in der `ConnectionStrings`-Sammlung werden ebenfalls entschlüsselt. In „local.settings.json“ ist `IsEncrypted` ebenfalls auf `false` festgelegt. Verschlüsseln Sie lokale Einstellungen, um die Gefahr des Verlusts von wertvollen Informationen aus „local.settings.json“ zu verringern. In Azure werden Anwendungseinstellungen immer verschlüsselt gespeichert. 

## <a name="func-settings-delete"></a>func settings delete

Entfernt eine vorhandene Einstellung aus der `Values`-Sammlung in der Datei [local.settings.json].

```command
func settings delete <SETTING_NAME>
```

Ersetzen Sie `<SETTING_NAME>` durch den Namen der App-Einstellung und `<VALUE>` durch den Wert der Einstellung. 

Die `delete`-Aktion unterstützt die folgende Option:

| Option     | BESCHREIBUNG                            |
| ------------ | -------------------------------------- |
| **`--connectionString`** | Entfernt das Name-Wert-Paar aus der `ConnectionStrings`-Sammlung anstatt aus der `Values`-Sammlung. |

## <a name="func-settings-encrypt"></a>func settings encrypt

Verschlüsselt die Werte einzelner Elemente in der `Values`-Sammlung in der Datei [local.settings.json].

```command
func settings encrypt
```

Verbindungszeichenfolgenwerte in der `ConnectionStrings`-Sammlung werden ebenfalls verschlüsselt. In dieser Datei „local.settings.json“ wird außerdem `IsEncrypted` auf `true` festgelegt. Damit wird angegeben, dass die lokale Runtime Einstellungen entschlüsselt, bevor sie verwendet werden. Verschlüsseln Sie lokale Einstellungen, um die Gefahr des Verlusts von wertvollen Informationen aus „local.settings.json“ zu verringern. In Azure werden Anwendungseinstellungen immer verschlüsselt gespeichert. 

## <a name="func-settings-list"></a>func settings list

Gibt eine Liste mit Einstellungen in der `Values`-Sammlung in der Datei [local.settings.json] aus. 

```command
func settings list
```

Verbindungszeichenfolgen aus der `ConnectionStrings`-Sammlung werden ebenfalls ausgegeben. Standardmäßig werden Werte aus Sicherheitsgründen maskiert. Sie können die Option `--showValue` verwenden, um den tatsächlichen Wert anzuzeigen.

Die `list`-Aktion unterstützt die folgende Option:

| Option     | BESCHREIBUNG                            |
| ------------ | -------------------------------------- |
| **`--showValue`** | Zeigt die tatsächlichen nicht maskierten Werte in der Ausgabe an. |

## <a name="func-templates-list"></a>func templates list

Listet die verfügbaren Funktionsvorlagen (Trigger) auf.

Die `list`-Aktion unterstützt die folgende Option:

| Option     | BESCHREIBUNG                            |
| ------------ | -------------------------------------- |
| **`--language`** | Die Sprache, nach der zurückgegebene Vorlagen gefiltert werden sollen. Standardmäßig werden alle Sprachen zurückgeben. |

[local.settings.json]: functions-develop-local.md#local-settings-file
