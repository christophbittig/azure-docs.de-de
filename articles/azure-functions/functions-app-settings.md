---
title: Referenz zu App-Einstellungen für Azure Functions
description: Referenzdokumentation für die App-Einstellungen für Azure Functions oder Umgebungsvariablen.
ms.topic: conceptual
ms.date: 07/27/2021
ms.openlocfilehash: 6b860e40acd118bc708ab00001707755272525b6
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128663609"
---
# <a name="app-settings-reference-for-azure-functions"></a>Referenz zu App-Einstellungen für Azure Functions

App-Einstellungen in einer Funktionen-App enthalten globale Konfigurationsoptionen, die sich auf alle Funktionen dieser Funktionen-App auswirken. Bei der lokalen Ausführung wird auf diese Einstellungen als lokale [Umgebungsvariablen](functions-develop-local.md#local-settings-file) zugegriffen. In diesem Artikel werden die in Funktionen-Apps verfügbaren App-Einstellungen aufgelistet.

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

Es gibt andere globale Konfigurationsoptionen in der Datei [host.json](functions-host-json.md) und in der Datei [local.settings.json](functions-develop-local.md#local-settings-file).
Beispielwerte für Verbindungszeichenfolgen werden aus Gründen der Lesbarkeit abgeschnitten.

> [!NOTE]
> Sie können Anwendungseinstellungen verwenden, um host.json-Einstellungswerte zu überschreiben, ohne die Datei „host.json“ ändern zu müssen. Dies ist hilfreich für Szenarien, in denen bestimmte host.json-Einstellungen für eine bestimmte Umgebung konfiguriert oder geändert werden müssen. So können Sie auch host.json-Einstellungen ändern, ohne das Projekt erneut veröffentlichen zu müssen. Weitere Informationen finden Sie im [Referenzartikel zu „host.json“](functions-host-json.md#override-hostjson-values). Zur Durchführung von Änderungen an den Funktions-App-Einstellungen muss Ihre Funktions-App neu gestartet werden.

## <a name="appinsights_instrumentationkey"></a>APPINSIGHTS_INSTRUMENTATIONKEY

Der Instrumentierungsschlüssel für Application Insights. Verwenden Sie jeweils nur `APPINSIGHTS_INSTRUMENTATIONKEY` oder `APPLICATIONINSIGHTS_CONNECTION_STRING`. Wenn Application Insights in einer Sovereign Cloud ausgeführt wird, verwenden Sie `APPLICATIONINSIGHTS_CONNECTION_STRING`. Weitere Informationen finden Sie unter [Konfigurieren der Überwachung für Azure Functions](configure-monitoring.md).

|Schlüssel|Beispielwert|
|---|------------|
|APPINSIGHTS_INSTRUMENTATIONKEY|`55555555-af77-484b-9032-64f83bb83bb`|

## <a name="applicationinsights_connection_string"></a>APPLICATIONINSIGHTS_CONNECTION_STRING

Die Verbindungszeichenfolge für Application Insights. Verwenden Sie in den folgenden Fällen `APPLICATIONINSIGHTS_CONNECTION_STRING` anstelle von `APPINSIGHTS_INSTRUMENTATIONKEY`:

+ Für Ihre Funktions-App sind die zusätzlichen Anpassungen erforderlich, die bei Verwendung der Verbindungszeichenfolge unterstützt werden.
+ Ihre Application Insights-Instanz wird in einer Sovereign Cloud ausgeführt, für die ein benutzerdefinierter Endpunkt erforderlich ist.

Weitere Informationen finden Sie unter [Verbindungszeichenfolgen](../azure-monitor/app/sdk-connection-string.md).

|Schlüssel|Beispielwert|
|---|------------|
|APPLICATIONINSIGHTS_CONNECTION_STRING|`InstrumentationKey=...`|

## <a name="azure_function_proxy_disable_local_call"></a>AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL

Standardmäßig nutzen [Functions-Proxys](functions-proxies.md) eine Verknüpfung, um API-Aufrufe von Proxys direkt an Funktionen in der gleichen Functions-App zu senden. Die Verknüpfung wird anstelle einer neuen HTTP-Anforderung verwendet. Diese Einstellung ermöglicht das Deaktivieren dieses Verknüpfungsverhaltens.

|Schlüssel|Wert|BESCHREIBUNG|
|-|-|-|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|`true`|Aufrufe mit einer Back-End-URL, die auf eine Funktion in der lokalen Funktions-App verweist, werden nicht direkt an die Funktion gesendet. Stattdessen werden die Anforderungen wieder an das HTTP-Front-End für die Funktions-App zurückgeleitet.|
|AZURE_FUNCTION_PROXY_DISABLE_LOCAL_CALL|`false`|Aufrufe mit einer Back-End-URL, die auf eine Funktion in der lokalen Funktions-App verweist, werden direkt an die Funktion weitergeleitet. Dies ist der Standardwert. |

## <a name="azure_function_proxy_backend_url_decode_slashes"></a>AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES

Diese Einstellung steuert, ob die Zeichen `%2F` in Routenparametern als Schrägstrich decodiert werden, wenn sie in die Back-End-URL eingefügt werden.

|Schlüssel|Wert|Beschreibung|
|-|-|-|
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|`true`|Routenparameter mit codierten Schrägstrichen werden decodiert. |
|AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES|`false`|Alle Routenparameter werden unverändert weitergegeben (Standardverhalten). |

Sehen Sie sich beispielsweise die Datei „proxies.json“ für eine Funktions-App in der Domäne `myfunction.com` an.

```JSON
{
    "$schema": "http://json.schemastore.org/proxies",
    "proxies": {
        "root": {
            "matchCondition": {
                "route": "/{*all}"
            },
            "backendUri": "example.com/{all}"
        }
    }
}
```

Wenn `AZURE_FUNCTION_PROXY_BACKEND_URL_DECODE_SLASHES` auf `true` festgelegt ist, wird die URL `example.com/api%2ftest` in `example.com/api/test` aufgelöst. Standardmäßig bleibt die URL unverändert (`example.com/test%2fapi`). Weitere Informationen finden Sie unter [Verwenden von Azure-Funktionsproxys](functions-proxies.md).

## <a name="azure_functions_environment"></a>AZURE_FUNCTIONS_ENVIRONMENT

Konfiguriert in Version 2.x oder höher der Functions-Runtime das App-Verhalten auf der Grundlage der Runtimeumgebung. Dieser Wert wird während der Initialisierung gelesen und kann auf einen beliebigen Wert festgelegt werden. Nur die Werte von `Development`, `Staging` und `Production` werden von der Laufzeit berücksichtigt. Wenn diese Anwendungseinstellung bei der Ausführung in Azure nicht vorhanden ist, wird `Production` als Umgebung angenommen. Verwenden Sie diese Einstellung anstelle von `ASPNETCORE_ENVIRONMENT`, wenn Sie die Laufzeitumgebung in Azure in einen anderen Wert als `Production` ändern müssen. Die Azure Functions Core Tools legen `AZURE_FUNCTIONS_ENVIRONMENT` auf `Development` fest, wenn die Ausführung auf einem lokalen Computer erfolgt, und dies kann nicht in der Datei „local.settings.json“ überschrieben werden. Weitere Informationen finden Sie unter [Umgebungsbasierte Startklasse und Methoden](/aspnet/core/fundamentals/environments#environment-based-startup-class-and-methods).

## <a name="azurefunctionsjobhost__"></a>AzureFunctionsJobHost__\*

In Version 2.x und höheren Versionen der Functions-Runtime können Anwendungseinstellungen Einstellungen in [host.json](functions-host-json.md) in der aktuellen Umgebung außer Kraft setzen. Diese Außerkraftsetzungen werden als Anwendungseinstellungen namens `AzureFunctionsJobHost__path__to__setting` ausgedrückt. Weitere Informationen finden Sie unter [Außerkraftsetzung der Werte in der Datei „host.json“](functions-host-json.md#override-hostjson-values).

## <a name="azurewebjobsdashboard"></a>AzureWebJobsDashboard

Optionale Verbindungszeichenfolge für das Speicherkonto zum Speichern von Protokollen und zu deren Anzeige auf der Registerkarte **Überwachen** im Portal. Diese Einstellung ist nur für Apps gültig, die für Version 1.x der Azure Functions-Runtime gedacht sind. Das Speicherkonto muss ein allgemeines Konto sein, das BLOBs, Warteschlangen und Tabellen unterstützt. Weitere Informationen finden Sie unter [Speicherkontoanforderungen](storage-considerations.md#storage-account-requirements).

|Schlüssel|Beispielwert|
|---|------------|
|AzureWebJobsDashboard|`DefaultEndpointsProtocol=https;AccountName=...`|

> [!NOTE]
> Für bessere Leistung und Benutzerfreundlichkeit verwenden dir Runtimeversion 2.x und höhere Versionen APPINSIGHTS_INSTRUMENTATIONKEY und App Insights anstelle von `AzureWebJobsDashboard` für die Überwachung.

## <a name="azurewebjobsdisablehomepage"></a>AzureWebJobsDisableHomepage

`true` bedeutet, dass die standardmäßige Landing Page deaktiviert wird, die für die Stamm-URL einer Funktionen-App angezeigt wird. Der Standardwert ist `false`.

|Schlüssel|Beispielwert|
|---|------------|
|AzureWebJobsDisableHomepage|`true`|

Wenn diese App-Einstellung ausgelassen oder auf `false` gesetzt wird, erfolgt als Reaktion auf die URL `<functionappname>.azurewebsites.net` die Anzeige einer Seite, die dem folgenden Beispiel ähnelt.

![Landing Page der Funktionen-App](media/functions-app-settings/function-app-landing-page.png)

## <a name="azurewebjobsdotnetreleasecompilation"></a>AzureWebJobsDotNetReleaseCompilation

`true` bedeutet, dass beim Kompilieren von .NET-Code der Releasemodus verwendet wird, während `false` bedeutet, dass der Debugmodus verwendet wird. Der Standardwert ist `true`.

|Schlüssel|Beispielwert|
|---|------------|
|AzureWebJobsDotNetReleaseCompilation|`true`|

## <a name="azurewebjobsfeatureflags"></a>AzureWebJobsFeatureFlags

Eine durch Trennzeichen getrennte Liste der zu aktivierenden Features der Betaversion. Durch diese Flags aktivierten Features der Betaversion sind nicht bereit für die Produktion, können aber zur experimentellen Verwendung aktiviert werden, bevor sie live geschaltet werden.

|Schlüssel|Beispielwert|
|---|------------|
|AzureWebJobsFeatureFlags|`feature1,feature2`|

## <a name="azurewebjobssecretstoragetype"></a>AzureWebJobsSecretStorageType

Gibt das Repository oder den Anbieter an, das bzw. der zum Speichern von Schlüsseln verwendet wird. Aktuell sind die unterstützten Repositorys der Blobspeicher („Blob“) und das lokale Dateisystem („Dateien“). In Version 2 ist der Blobspeicher die Standardeinstellung, in Version 1 das Dateisystem.

|Schlüssel|Beispielwert|
|---|------------|
|AzureWebJobsSecretStorageType|Dateien|

## <a name="azurewebjobsstorage"></a>AzureWebJobsStorage

Die Azure Functions-Runtime verwendet diese Verbindungszeichenfolge für das Speicherkonto für den Normalbetrieb. Einige Verwendungsmöglichkeiten für dieses Speicherkonto umfassen Schlüsselverwaltung, Timer-Triggerverwaltung und Event Hubs-Prüfpunkte. Das Speicherkonto muss ein allgemeines Konto sein, das BLOBs, Warteschlangen und Tabellen unterstützt. Weitere Informationen finden Sie unter [Speicherkonto](functions-infrastructure-as-code.md#storage-account) und [Anforderungen an das Speicherkonto](storage-considerations.md#storage-account-requirements).

|Schlüssel|Beispielwert|
|---|------------|
|AzureWebJobsStorage|`DefaultEndpointsProtocol=https;AccountName=...`|

## <a name="azurewebjobs_typescriptpath"></a>AzureWebJobs_TypeScriptPath

Der Pfad zum Compiler, der für TypeScript verwendet wird. Bei Bedarf können Sie den Standardwert außer Kraft setzen.

|Schlüssel|Beispielwert|
|---|------------|
|AzureWebJobs_TypeScriptPath|`%HOME%\typescript`|

## <a name="function_app_edit_mode"></a>FUNCTION\_APP\_EDIT\_MODE

Bestimmt, ob die Bearbeitung im Azure-Portal aktiviert ist. Gültige Werte sind „readwrite“ und „readonly“.

|Schlüssel|Beispielwert|
|---|------------|
|FUNCTION\_APP\_EDIT\_MODE|`readonly`|

## <a name="functions_extension_version"></a>FUNCTIONS\_EXTENSION\_VERSION

Die Version der Functions-Runtime, die ihre Funktions-App hostet. Eine Tilde (`~`) mit Hauptversion bedeutet, dass die neueste Version dieser Hauptversion verwendet werden soll (z. B. „~3“). Wenn neue Versionen für dieselbe Hauptversion verfügbar sind, werden sie automatisch in der Funktionen-App installiert. Verwenden Sie die vollständige Versionsnummer (z. B. „3.0.12345“), um die App an eine bestimmte Version anzuheften. Der Standardwert lautet „~3“. Der Wert `~1` heftet Ihre App an Version 1.x der Runtime an. Weitere Informationen finden Sie unter [Einstellen von Runtimeversionen von Azure Functions als Ziel](functions-versions.md). Mit dem Wert `~4` können Sie eine Vorschauversion von Azure Functions ausführen, um die Vorschauversion von .NET 6.0 zu verwenden. Weitere Informationen finden Sie auf der Seite zur [frühen Vorschauversion von Azure Functions v4](https://aka.ms/functions-dotnet6earlypreview-wiki).

|Schlüssel|Beispielwert|
|---|------------|
|FUNCTIONS\_EXTENSION\_VERSION|`~3`|

## <a name="functions_v2_compatibility_mode"></a>FUNCTIONS\_V2\_COMPATIBILITY\_MODE

Diese Einstellung ermöglicht die Ausführung Ihrer Funktions-App in einem mit Version 2.x kompatiblen Modus unter Runtimeversion 3.x. Verwenden Sie diese Einstellung nur, wenn beim [Aktualisieren Ihrer Funktions-App von Runtimeversion 2.x auf 3.x](functions-versions.md#migrating-from-2x-to-3x) Probleme auftreten.

>[!IMPORTANT]
> Diese Einstellung ist nur als kurzfristige Problemumgehung gedacht, während Sie Ihre App für die ordnungsgemäße Ausführung unter Version 3.x aktualisieren. Diese Einstellung wird unterstützt, solange die [Runtime 2.x unterstützt wird](functions-versions.md). Wenn Probleme auftreten, die die Ausführung Ihrer App unter Version 3.x ohne diese Einstellung verhindern, [melden Sie Ihr Problem](https://github.com/Azure/azure-functions-host/issues/new?template=Bug_report.md).

[FUNCTIONS\_EXTENSION\_VERSION](functions-app-settings.md#functions_extension_version) muss dafür auf `~3` festgelegt werden.

|Schlüssel|Beispielwert|
|---|------------|
|FUNCTIONS\_V2\_COMPATIBILITY\_MODE|`true`|

## <a name="functions_worker_process_count"></a>FUNCTIONS\_WORKER\_PROCESS\_COUNT

Gibt die maximale Anzahl von Sprachworkerprozessen mit einem Standardwert von `1` an. Der zulässige Höchstwert ist `10`. Funktionsaufrufe werden gleichmäßig zwischen Sprachworkerprozessen verteilt. Sprachworkerprozesse werden so lange alle 10 Sekunden erzeugt, bis die von FUNCTIONS\_WORKER\_PROCESS\_COUNT festgelegte Anzahl erreicht ist. Die Verwendung mehrerer Sprachworkerprozesse ist nicht dasselbe wie [Skalierung](functions-scale.md). Erwägen Sie die Verwendung dieser Einstellung, wenn Ihr Workload aus einer Mischung von CPU-gebundenen und E/A-gebundenen Aufrufen besteht. Diese Einstellung gilt für alle .NET-Sprachen.

|Schlüssel|Beispielwert|
|---|------------|
|FUNCTIONS\_WORKER\_PROCESS\_COUNT|`2`|

## <a name="functions_worker_runtime"></a>FUNCTIONS\_WORKER\_RUNTIME

Die Sprachworkerruntime, die in der Funktionen-App geladen werden soll.  Dies entspricht der Sprache, die in der Anwendung verwendet wird (z. B. `dotnet`). Ab Version 2.x der Azure Functions-Runtime kann eine bestimmte Funktions-App nur eine einzige Sprache unterstützen.

|Schlüssel|Beispielwert|
|---|------------|
|FUNCTIONS\_WORKER\_RUNTIME|`node`|

Gültige Werte:

| Wert | Sprache |
|---|---|
| `dotnet` | [C# (Klassenbibliothek)](functions-dotnet-class-library.md)<br/>[C# (Skript)](functions-reference-csharp.md) |
| `dotnet-isolated` | [C# (isolierter Prozess)](dotnet-isolated-process-guide.md) |
| `java` | [Java](functions-reference-java.md) |
| `node` | [JavaScript](functions-reference-node.md)<br/>[TypeScript](functions-reference-node.md#typescript) |
| `powershell` | [PowerShell](functions-reference-powershell.md) |
| `python` | [Python](functions-reference-python.md) |

## <a name="mdmaxbackgroundupgradeperiod"></a>MDMaxBackgroundUpgradePeriod

Steuert den Zeitraum für Hintergrundupdates von verwalteten Abhängigkeiten für PowerShell-Funktions-Apps. Der Standardwert ist `7.00:00:00` (wöchentlich).

Jeder PowerShell-Workerprozess löst die Überprüfung auf Modulupgrades im PowerShell-Katalog beim Start des Prozesses und alle `MDMaxBackgroundUpgradePeriod` danach aus. Wenn im PowerShell-Katalog eine neue Modulversion verfügbar ist, wird sie im Dateisystem installiert und PowerShell-Workern zur Verfügung gestellt. Wenn Sie diesen Wert verringern, erhält Ihre Funktions-App schneller eine neuere Modulversion. Dies steigert aber auch den App-Ressourceneinsatz (Netzwerk-E/A, CPU, Speicher). Wenn Sie diesen Wert erhöhen, wird der App-Ressourceneinsatz verringert, aber auch die Bereitstellung neuer Modulversionen für Ihre App verzögert.

|Schlüssel|Beispielwert|
|---|------------|
|MDMaxBackgroundUpgradePeriod|`7.00:00:00`|

Weitere Informationen finden Sie unter [Abhängigkeitsverwaltung](functions-reference-powershell.md#dependency-management).

## <a name="mdnewsnapshotcheckperiod"></a>MDNewSnapshotCheckPeriod

Gibt an, wie oft jeder PowerShell-Worker überprüft, ob Upgrades verwalteter Abhängigkeiten installiert wurden. Die Standardhäufigkeit ist `01:00:00` (stündlich).

Nach der Installation neuer Modulversionen im Dateisystem müssen alle PowerShell-Workerprozesse neu gestartet werden. Das Neustarten von PowerShell-Workern wirkt sich auf die Verfügbarkeit der App aus, da dadurch die Ausführung der aktuellen Funktion unterbrochen werden kann. Bis zum Abschluss des Neustarts aller PowerShell-Workerprozesse können Funktionsaufrufe entweder die alte oder neue Modulversion verwenden. Alle PowerShell-Worker werden innerhalb von `MDNewSnapshotCheckPeriod` neu gestartet.

In jeder `MDNewSnapshotCheckPeriod` überprüft der PowerShell-Worker, ob Upgrades verwalteter Abhängigkeiten installiert wurden. Falls Upgrades installiert wurden, wird ein Neustart initiiert. Das Erhöhen dieses Wert verringert die Häufigkeit von Unterbrechungen durch Neustarts. Die Erhöhung kann jedoch auch die Zeit verlängern, in der Funktionsaufrufe nicht deterministisch entweder die alte oder die neue Modulversion verwenden können.

|Schlüssel|Beispielwert|
|---|------------|
|MDNewSnapshotCheckPeriod|`01:00:00`|

Weitere Informationen finden Sie unter [Abhängigkeitsverwaltung](functions-reference-powershell.md#dependency-management).


## <a name="mdminbackgroundupgradeperiod"></a>MDMinBackgroundUpgradePeriod

Zeitraum nach einer vorherigen Überprüfung auf Upgrades verwalteter Abhängigkeiten, bevor eine weitere Upgradeüberprüfung gestartet wird. Der Standardwert ist `1.00:00:00` (täglich).

Um übermäßige Modulupgrades bei häufigen Workerneustarts zu vermeiden, wird die Überprüfung auf Modulupgrades nicht durchgeführt, wenn ein Worker diese Prüfung bereits innerhalb der letzten `MDMinBackgroundUpgradePeriod` ausgelöst hat.

|Schlüssel|Beispielwert|
|---|------------|
|MDMinBackgroundUpgradePeriod|`1.00:00:00`|

Weitere Informationen finden Sie unter [Abhängigkeitsverwaltung](functions-reference-powershell.md#dependency-management).

## <a name="pip_extra_index_url"></a>PIP\_EXTRA\_INDEX\_URL

Durch den Wert für diese Einstellung wird eine benutzerdefinierte Paketindex-URL für Python-Apps angegeben. Verwenden Sie diese Einstellung, wenn Sie einen Remotebuild mit benutzerdefinierten Abhängigkeiten ausführen müssen, die sich in einem zusätzlichen Paketindex befinden.

|Schlüssel|Beispielwert|
|---|------------|
|PIP\_EXTRA\_INDEX\_URL|`http://my.custom.package.repo/simple` |

Weitere Informationen finden Sie in der Python-Entwicklerreferenz unter [Benutzerdefinierte Abhängigkeiten](functions-reference-python.md#remote-build-with-extra-index-url).

## <a name="python_isolate_worker_dependencies-preview"></a>PYTHON\_ISOLATE\_WORKER\_DEPENDENCIES (Vorschauversion)

Die Konfiguration ist spezifisch für Python-Funktions-Apps. Sie definiert die Priorisierung der Modulladereihenfolge. Wenn ihre Python-Funktions-Apps Probleme im Zusammenhang mit Modulkollisionen haben (z. B. wenn Sie protobuf, tensorflow oder grpcio in Ihrem Projekt verwenden), sollte das Festlegen dieser App-Einstellung auf `1` Ihr Problem beheben. Standardmäßig ist dieser Wert auf `0` festgelegt. Dieses Kennzeichen befindet sich zurzeit in der Vorschau.

|Schlüssel|Wert|BESCHREIBUNG|
|---|-----|-----------|
|PYTHON\_ISOLATE\_WORKER\_DEPENDENCIES|`0`| Priorisiert das Laden der Python-Bibliotheken aus den Abhängigkeiten interner Python-Worker. Für Bibliotheken von Drittanbietern, die in „requirements.txt“ definiert sind, kann ggf. Shadowing durchgeführt werden. |
|PYTHON\_ISOLATE\_WORKER\_DEPENDENCIES|`1`| Priorisiert das Laden der Python-Bibliotheken aus dem in „requirements.txt“ definierten Paket der Anwendung. Dadurch wird verhindert, dass Ihre Bibliotheken mit den Bibliotheken des internen Python-Workers kollidieren. |

## <a name="python_enable_worker_extensions"></a>PYTHON\_ENABLE\_WORKER\_EXTENSIONS

Die Konfiguration ist spezifisch für Python-Funktions-Apps. Wenn diese Einstellung auf `1` festgelegt wird, kann der Worker [Python-Workererweiterungen](functions-reference-python.md#python-worker-extensions) laden, die in „requirements.txt“ definiert sind. Sie ermöglicht Ihrer Funktions-App den Zugriff auf neue Features, die von Paketen von Drittanbietern bereitgestellt werden. Sie kann auch das Verhalten des Ladens und Aufrufs von Funktionen in Ihrer App ändern. Stellen Sie sicher, dass die von Ihnen ausgewählte Erweiterung vertrauenswürdig ist, da Sie das Risiko ihrer Verwendung tragen. Azure Functions gewährt keine ausdrücklichen Gewährleistungen für Erweiterungen. Informationen zur Verwendung einer Erweiterung finden Sie auf der jeweiligen Manpage oder in der Infodatei zur Erweiterung. Standardmäßig wird dieser Wert auf `0` festgelegt.

|Key|Wert|BESCHREIBUNG|
|---|-----|-----------|
|PYTHON\_ENABLE\_WORKER\_EXTENSIONS|`0`| Deaktiviert alle Python-Workererweiterungen. |
|PYTHON\_ENABLE\_WORKER\_EXTENSIONS|`1`| Erlaubt dem Python-Worker das Laden von Erweiterungen aus „requirements.txt“. |

## <a name="python_threadpool_thread_count"></a>PYTHON\_THREADPOOL\_THREAD\_COUNT

Gibt die maximale Anzahl von Threads an, die von einem Python-Sprachworker zum Ausführen von Funktionsaufrufen verwendet werden. Der Standardwert lautet `1` für die Python-Version `3.8` und niedriger. Für die Python-Version `3.9` und höher ist der Wert auf `None` festgelegt. Beachten Sie, dass diese Einstellung nicht die Anzahl von Threads garantiert, die bei Ausführungen festgelegt werden. Diese Einstellung ermöglicht es Python, die Anzahl von Threads auf den angegebenen Wert zu erhöhen. Die Einstellung gilt nur für Python-Funktions-Apps. Darüber hinaus gilt die Einstellung für synchrone Funktionsaufrufe und nicht für Coroutinen.

|Schlüssel|Beispielwert|Maximalwert|
|---|------------|---------|
|PYTHON\_THREADPOOL\_THREAD\_COUNT|2|32|

## <a name="scale_controller_logging_enabled"></a>SCALE\_CONTROLLER\_LOGGING\_ENABLED

_Diese Einstellung befindet sich derzeit in der Vorschauphase._

Diese Einstellung dient zum Steuern der Protokollierung über den Azure Functions-Skalierungscontroller. Weitere Informationen finden Sie unter [Skalierungscontrollerprotokolle (Vorschau)](functions-monitoring.md#scale-controller-logs).

|Schlüssel|Beispielwert|
|-|-|
|SCALE_CONTROLLER_LOGGING_ENABLED|`AppInsights:Verbose`|

Der Wert für diesen Schlüssel wird im Format `<DESTINATION>:<VERBOSITY>` bereitgestellt, das wie folgt definiert ist:

[!INCLUDE [functions-scale-controller-logging](../../includes/functions-scale-controller-logging.md)]

## <a name="scm_logstream_timeout"></a>SCM\_LOGSTREAM\_TIMEOUT

Steuert das Timeout in Sekunden, wenn eine Verbindung mit Streamingprotokollen vorhanden ist. Der Standardwert beträgt 7.200 Sekunden (2 Stunden). 

|Schlüssel|Beispielwert|
|-|-|
|SCM_LOGSTREAM_TIMEOUT|`1800`|

Der Beispielwert von `1800` oben legt ein Timeout von 30 Minuten fest. Weitere Informationen finden Sie unter [Aktivieren von Streamingprotokollen](functions-run-local.md#enable-streaming-logs).

## <a name="website_contentazurefileconnectionstring"></a>WEBSITE\_CONTENTAZUREFILECONNECTIONSTRING

Die Verbindungszeichenfolge für das Speicherkonto, in dem der Code der Funktions-App und die Konfiguration in ereignisgesteuerten, unter Windows ausgeführten Skalierungsplänen gespeichert werden. Weitere Informationen finden Sie unter [Erstellen einer Funktions-App](functions-infrastructure-as-code.md#windows).

|Schlüssel|Beispielwert|
|---|------------|
|WEBSITE_CONTENTAZUREFILECONNECTIONSTRING|`DefaultEndpointsProtocol=https;AccountName=...`|

Wird nur bei der Bereitstellung für einen Premium-Plan oder Verbrauchsplan verwendet, der unter Windows ausgeführt wird. Wird nicht für Verbrauchspläne unter Linux unterstützt. Das Ändern oder Entfernen dieser Einstellung kann dazu führen, dass Ihre Funktions-App nicht gestartet wird. Weitere Informationen finden Sie in [diesem Artikel zur Problembehandlung](functions-recover-storage-account.md#storage-account-application-settings-were-deleted).

## <a name="website_contentovervnet"></a>WEBSITE\_CONTENTOVERVNET

Der Wert `1` ermöglicht die Skalierung Ihrer Funktions-App, wenn Sie Ihr Speicherkonto auf ein virtuelles Netzwerk beschränken. Sie sollten diese Einstellung aktivieren, wenn Sie Ihr Speicherkonto auf ein virtuelles Netzwerk einschränken. Weitere Informationen finden Sie unter [Einschränken Ihres Speicherkontos auf ein virtuelles Netzwerk](configure-networking-how-to.md#restrict-your-storage-account-to-a-virtual-network).

|Schlüssel|Beispielwert|
|---|------------|
|WEBSITE_CONTENTOVERVNET|`1`|

Wird mit den Plänen [Premium](functions-premium-plan.md) und [Dediziert (App Service)](dedicated-plan.md) (Standard oder höher) unterstützt. Wird nicht unterstützt, wenn es auf einem [Verbrauchsplan](consumption-plan.md) ausgeführt wird. 

## <a name="website_contentshare"></a>WEBSITE\_CONTENTSHARE

Der Dateipfad für den Funktions-App-Code und die Konfiguration in einem ereignisgesteuerten Skalierungsplan unter Windows. Wird mit WEBSITE_CONTENTAZUREFILECONNECTIONSTRING verwendet. Standardmäßig wird eine eindeutige Zeichenfolge verwendet, die mit dem Namen der Funktionen-App beginnt. Weitere Informationen finden Sie unter [Erstellen einer Funktionen-App](functions-infrastructure-as-code.md#windows).

|Schlüssel|Beispielwert|
|---|------------|
|WEBSITE_CONTENTSHARE|`functionapp091999e2`|

Wird nur bei der Bereitstellung für einen Premium-Plan oder Verbrauchsplan verwendet, der unter Windows ausgeführt wird. Wird nicht für Verbrauchspläne unter Linux unterstützt. Das Ändern oder Entfernen dieser Einstellung kann dazu führen, dass Ihre Funktions-App nicht gestartet wird. Weitere Informationen finden Sie in [diesem Artikel zur Problembehandlung](functions-recover-storage-account.md#storage-account-application-settings-were-deleted).

Wenn Sie während der Bereitstellung eine Azure Resource Manager-Vorlage zum Erstellen einer Funktions-App verwenden, schließen Sie WEBSITE_CONTENTSHARE nicht in die Vorlage ein. Diese Sloteinstellung wird während der Bereitstellung generiert. Weitere Informationen finden Sie unter [Automatisieren der Ressourcenbereitstellung für Ihre Funktions-App in Azure Functions](functions-infrastructure-as-code.md#windows).

## <a name="website_dns_server"></a>WEBSITE\_DNS\_SERVER

Legt den von einer App verwendeten DNS-Server beim Auflösen von IP-Adressen fest. Diese Einstellung ist häufig erforderlich, wenn bestimmte Netzwerkfunktionen wie [private Zonen von Azure DNS](functions-networking-options.md#azure-dns-private-zones) und [private Endpunkte](functions-networking-options.md#restrict-your-storage-account-to-a-virtual-network) verwendet werden.

|Schlüssel|Beispielwert|
|---|------------|
|WEBSITE\_DNS\_SERVER|`168.63.129.16`|

## <a name="website_enable_brotli_encoding"></a>WEBSITE\_ENABLE\_BROTLI\_ENCODING

Steuert, ob Brotli-Codierung für die Komprimierung anstelle der GZIP-Standardkomprimierung verwendet wird. Wenn `WEBSITE_ENABLE_BROTLI_ENCODING` auf `1` festgelegt ist, wird Brotli-Codierung verwendet, andernfalls wird GZIP-Codierung verwendet.

## <a name="website_max_dynamic_application_scale_out"></a>WEBSITE\_MAX\_DYNAMIC\_APPLICATION\_SCALE\_OUT

Die maximale Anzahl der Instanzen, auf denen die App aufskaliert werden kann. Dieser Wert ist standardmäßig unbegrenzt.

> [!IMPORTANT]
> Diese Einstellung befindet sich in der Vorschauphase.  Eine [App-Eigenschaft für das maximale Aufskalieren einer Funktion](./event-driven-scaling.md#limit-scale-out) wurde hinzugefügt und ist die empfohlene Methode zum Begrenzen des Aufskalierens.

|Schlüssel|Beispielwert|
|---|------------|
|WEBSITE\_MAX\_DYNAMIC\_APPLICATION\_SCALE\_OUT|`5`|

## <a name="website_node_default_version"></a>WEBSITE\_NODE\_DEFAULT_VERSION

_Nur Windows._
Legt die Version von Node.js fest, die beim Ausführen Ihrer Funktions-App unter Windows verwendet werden soll. Verwenden Sie eine Tilde (~), damit die Laufzeit die neueste verfügbare Version der Zielhauptversion verwendet. Wenn Sie beispielsweise auf `~10` festlegen, wird die neueste Version von Node.js 10 verwendet. Wenn eine Hauptversion mittels Tilde vorgegeben wird, müssen Sie die Nebenversion nicht manuell aktualisieren.

|Schlüssel|Beispielwert|
|---|------------|
|WEBSITE\_NODE\_DEFAULT_VERSION|`~10`|

## <a name="website_run_from_package"></a>WEBSITE\_RUN\_FROM\_PACKAGE

Ermöglicht es Ihrer Funktions-App, über eine bereitgestellte Paketdatei ausgeführt zu werden.

|Schlüssel|Beispielwert|
|---|------------|
|WEBSITE\_RUN\_FROM\_PACKAGE|`1`|

Gültige Werte sind entweder eine URL, die in den Speicherort einer Bereitstellungspaketdatei aufgelöst werden kann, oder `1`. Bei einer Festlegung auf `1` muss sich das Paket im Ordner `d:\home\data\SitePackages` befinden. Wenn Sie die Zip-Bereitstellung mit dieser Einstellung verwenden, wird das Paket automatisch an diesen Speicherort hochgeladen. In der Vorschau wurde diese Einstellung als `WEBSITE_RUN_FROM_ZIP` bezeichnet. Weitere Informationen finden Sie unter [Ausführen Ihrer Azure Functions aus einem Paket](run-functions-from-deployment-package.md).

## <a name="website_time_zone"></a>WEBSITE\_TIME\_ZONE

Ermöglicht das Festlegen der Zeitzone für Ihre Funktions-App.

|Schlüssel|OS|Beispielwert|
|---|--|------------|
|WEBSITE\_TIME\_ZONE|Windows|`Eastern Standard Time`|
|WEBSITE\_TIME\_ZONE|Linux|`America/New_York`|

[!INCLUDE [functions-timezone](../../includes/functions-timezone.md)]

## <a name="website_vnet_route_all"></a>WEBSITE\_VNET\_ROUTE\_ALL

Gibt an, ob der gesamte ausgehende Datenverkehr von der App über das virtuelle Netzwerk weitergeleitet wird. Der Einstellungswert `1` gibt an, dass der gesamte Datenverkehr über das virtuelle Netzwerk weitergeleitet wird. Sie müssen diese Einstellung verwenden, wenn Sie die Features der [regionalen Integration des virtuellen Netzwerks](functions-networking-options.md#regional-virtual-network-integration) verwenden. Sie wird auch verwendet, wenn ein [Virtual Network NAT Gateway verwendet wird, um eine statische ausgehende IP-Adresse zu definieren](functions-how-to-use-nat-gateway.md).

|Schlüssel|Beispielwert|
|---|------------|
|WEBSITE\_VNET\_ROUTE\_ALL|`1`|

## <a name="next-steps"></a>Nächste Schritte

[Informationen zum Aktualisieren von App-Einstellungen](functions-how-to-use-azure-function-app-settings.md#settings)

[Weitere Informationen finden Sie unter den globalen Einstellungen in der Datei host.json](functions-host-json.md)

[Weitere App-Einstellungen für App Service-Apps](https://github.com/projectkudu/kudu/wiki/Configurable-settings)
