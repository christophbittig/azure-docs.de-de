---
title: Referenz zu Umgebungsvariablen und App-Einstellungen
description: Dieser Artikel beschreibt die häufig verwendeten Umgebungsvariablen und welche mit App-Einstellungen geändert werden können.
ms.topic: article
ms.date: 06/14/2021
ms.openlocfilehash: e315a51e7d160bcc2d8864cfa954924f0fe4c094
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2021
ms.locfileid: "132025751"
---
# <a name="environment-variables-and-app-settings-in-azure-app-service"></a>Umgebungsvariablen und App-Einstellungen in Azure App Service

In [Azure App Service](overview.md) sind bestimmte Einstellungen für die Bereitstellungs- oder Laufzeitumgebung als Umgebungsvariablen verfügbar. Einige dieser Einstellungen können angepasst werden, wenn Sie sie manuell als [App-Einstellungen festlegen](configure-common.md#configure-app-settings). Diese Referenz zeigt die Variablen, die Sie verwenden oder anpassen können.

## <a name="app-environment"></a>App-Umgebung

Die folgenden Umgebungsvariablen hängen allgemein mit der App-Umgebung zusammen.

| Einstellungsname| BESCHREIBUNG | Beispiel |
|-|-|-|
| `WEBSITE_SITE_NAME` | Schreibgeschützt. App-Name. ||
| `WEBSITE_RESOURCE_GROUP` | Schreibgeschützt. Name der Azure-Ressourcengruppe, die die App-Ressource enthält. ||
| `WEBSITE_OWNER_NAME` | Schreibgeschützt. Enthält die Azure-Abonnement-ID, die die App, die Ressourcengruppe und den Webspace besitzt. ||
| `REGION_NAME` | Schreibgeschützt. Regionsname der App. ||
| `WEBSITE_PLATFORM_VERSION` | Schreibgeschützt. App Service-Plattformversion. ||
| `HOME` | Schreibgeschützt. Pfad zum Startverzeichnis (z. B. `D:\home` für Windows). ||
| `SERVER_PORT` | Schreibgeschützt. Der Port, an dem die App lauschen soll. | |
| `WEBSITE_WARMUP_PATH`  | Ein relativer Pfad zum Pingen, um die App vorzubereiten. Er beginnt mit einem Schrägstrich. Der Standardwert ist `/`; er pingt den Stammpfad. Der spezifische Pfad kann von einem nicht authentifizierten Client wie Azure Traffic Manager gepingt werden, selbst wenn die [App Service-Authentifizierung](overview-authentication-authorization.md) so festgelegt ist, dass nicht authentifizierte Clients abgelehnt werden. (HINWEIS: Diese App-Einstellung ändert nicht den von AlwaysOn verwendeten Pfad.) ||
| `WEBSITE_COMPUTE_MODE` | Schreibgeschützt. Gibt an, ob die App auf dedizierten (`Dedicated`) oder freigegebenen (`Shared`) VMs ausgeführt wird. ||
| `WEBSITE_SKU` | Schreibgeschützt. SKU der App. Mögliche Werte sind `Free`, `Shared`, `Basic` und `Standard`. ||
| `SITE_BITNESS` | Schreibgeschützt. Zeigt an, ob die App eine 32-Bit- (`x86`) oder 64-Bit-App (`AMD64`) ist. ||
| `WEBSITE_HOSTNAME` | Schreibgeschützt. Primärer Hostname für die App. Benutzerdefinierte Hostnamen werden hier nicht berücksichtigt. ||
| `WEBSITE_VOLUME_TYPE` | Schreibgeschützt. Zeigt den derzeit verwendeten Speichervolumentyp an. ||
| `WEBSITE_NPM_DEFAULT_VERSION` | npm-Standardversion, die von der App verwendet wird. ||
| `WEBSOCKET_CONCURRENT_REQUEST_LIMIT` | Schreibgeschützt. Limit für gleichzeitige Websocket-Anforderungen. Für die **Standardebene** und höher ist der Wert `-1`, aber es gibt immer noch ein Limit pro VM basierend auf Ihrer VM-Größe (siehe [Cross VM Numerical Limits](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#cross-vm-numerical-limits)) (VM-übergreifende numerische Grenzwerte). ||
| `WEBSITE_PRIVATE_EXTENSIONS` | Legen Sie den Wert auf `0` fest, um die Verwendung privater Websiteerweiterungen zu deaktivieren. ||
| `WEBSITE_TIME_ZONE` | Standardmäßig ist die Zeitzone für die App immer UTC. Sie können sie in einen der gültigen Werte ändern, die in [TimeZone](/previous-versions/windows/it-pro/windows-vista/cc749073(v=ws.10)) aufgeführt sind. Wenn der angegebene Wert nicht erkannt wird, wird UTC verwendet. | `Atlantic Standard Time` |
| `WEBSITE_ADD_SITENAME_BINDINGS_IN_APPHOST_CONFIG` | Bei einem Failover oder einer Neukonfiguration des Speichervolumens wird Ihre App auf ein Standbyspeichervolumen umgeschaltet. Die Standardeinstellung `1` verhindert, dass der Workerprozess wieder verwendet wird, wenn sich die Speicherinfrastruktur ändert. Wenn Sie eine WCF-App (Windows Communication Foundation) ausführen, deaktivieren Sie sie, indem Sie sie auf `0` festlegen. Die Einstellung ist slotspezifisch, daher sollten Sie sie in allen Slots festlegen. ||
| `WEBSITE_PROACTIVE_AUTOHEAL_ENABLED` | Standardmäßig wird für eine VM-Instanz proaktiv Auto Heal eingesetzt, wenn sie mehr als 30 Sekunden lang mehr als 90 % des zugewiesenen Arbeitsspeichers verwendet oder wenn 80 % der gesamten Anforderungen in den letzten zwei Minuten länger als 200 Sekunden dauern. Wenn eine VN-Instanz eine dieser Regeln ausgelöst hat, ist der Wiederherstellungsprozess ein überlappender Neustart der Instanz. Legen Sie den Wert auf `false` fest, um dieses Wiederherstellungsverhalten zu deaktivieren. Der Standardwert lautet `true`. Weitere Informationen finden Sie in [Introducing Proactive Auto Heal](https://azure.github.io/AppService/2017/08/17/Introducing-Proactive-Auto-Heal.html). ||
| `WEBSITE_PROACTIVE_CRASHMONITORING_ENABLED` | Wenn der w3wp.exe-Prozess auf einer VM-Instanz Ihrer App aufgrund einer nicht behandelten Ausnahme mehr als dreimal in 24 Stunden abstürzt, wird ein Debuggerprozess an den Hauptworkerprozess auf dieser Instanz angefügt und erfasst ein Speicherabbild, wenn der Workerprozess noch mal abstürzt. Dieses Speicherabbild wird dann analysiert, und die Aufrufliste des Threads, der den Absturz verursacht hat, wird in den App Service-Protokollen protokolliert. Legen Sie den Wert auf `false` fest, um dieses automatische Überwachungsverhalten zu deaktivieren. Der Standardwert lautet `true`. Weitere Informationen finden Sie in [Proactive Crash Monitoring in Azure App Service](https://azure.github.io/AppService/2021/03/01/Proactive-Crash-Monitoring-in-Azure-App-Service.html). ||
| `WEBSITE_DAAS_STORAGE_SASURI` | Während der Absturzüberwachung (proaktiv oder manuell) werden die Speicherabbilder standardmäßig gelöscht. Zum Speichern der Speicherabbilder in einem Speicherblobcontainer geben Sie den SAS-URI an.  ||
| `WEBSITE_CRASHMONITORING_ENABLED` | Legen Sie den Wert auf `true` fest, um die [Absturzüberwachung](https://azure.github.io/AppService/2020/08/11/Crash-Monitoring-Feature-in-Azure-App-Service.html) manuell zu aktivieren. Sie müssen auch `WEBSITE_DAAS_STORAGE_SASURI` und `WEBSITE_CRASHMONITORING_SETTINGS` festlegen. Der Standardwert lautet `false`. Diese Einstellung hat keine Auswirkungen, wenn das Remotedebuggen aktiviert ist. Wenn diese Einstellung auf `true`festgelegt ist, ist auch die [proaktive Absturzüberwachung](https://azure.github.io/AppService/2020/08/11/Crash-Monitoring-Feature-in-Azure-App-Service.html) deaktiviert. ||
| `WEBSITE_CRASHMONITORING_SETTINGS` | Ein JSON-Code im folgenden Format: `{"StartTimeUtc": "2020-02-10T08:21","MaxHours": "<elapsed-hours-from-StartTimeUtc>","MaxDumpCount": "<max-number-of-crash-dumps>"}`. Erforderlich, um die [Absturzüberwachung](https://azure.github.io/AppService/2020/08/11/Crash-Monitoring-Feature-in-Azure-App-Service.html) zu konfigurieren, wenn `WEBSITE_CRASHMONITORING_ENABLED` angegeben ist. Damit nur die Aufrufliste protokolliert und das Absturzabbild nicht im Speicherkonto gespeichert wird, fügen Sie `,"UseStorageAccount":"false"` im JSON-Code hinzu. ||
| `REMOTEDEBUGGINGVERSION` | Version für Remotedebuggen. ||
| `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` | Standardmäßig erstellt App Service bei der App-Erstellung einen freigegebenen Speicher für Sie. Wenn Sie stattdessen ein benutzerdefiniertes Speicherkonto verwenden möchten, legen Sie den Wert auf die Verbindungszeichenfolge Ihres Speicherkontos fest. Informationen zu Funktionen finden Sie in der [Referenz zu App-Einstellungen für Functions](../azure-functions/functions-app-settings.md#website_contentazurefileconnectionstring). | `DefaultEndpointsProtocol=https;AccountName=<name>;AccountKey=<key>` |
| `WEBSITE_CONTENTSHARE` | Wenn Sie mit `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` ein benutzerdefiniertes Speicherkonto angeben, erstellt App Service eine Dateifreigabe in diesem Speicherkonto für Ihre App. Zur Verwendung eines benutzerdefinierten Namens legen Sie diese Variable auf den gewünschten Namen fest. Wenn keine Dateifreigabe mit dem angegebenen Namen vorhanden ist, wird sie von App Service erstellt. | `myapp123` |
| `WEBSITE_SCM_ALWAYS_ON_ENABLED` | Schreibgeschützt. Zeigt an, ob Always On aktiviert ist (`1`) oder nicht (`0`). ||
| `WEBSITE_SCM_SEPARATE_STATUS` | Schreibgeschützt. Zeigt an, ob die Kudu-App in einem separaten Prozess (`1`) ausgeführt wird oder nicht (`0`). ||

<!-- 
WEBSITE_PROACTIVE_STACKTRACING_ENABLED
WEBSITE_CLOUD_NAME
WEBSITE_MAXIMUM_CONCURRENTCOLDSTARTS
HOME_EXPANDED
USERPROFILE
WEBSITE_ISOLATION
WEBSITE_OS | only appears on windows
WEBSITE_CLASSIC_MODE
 -->

## <a name="variable-prefixes"></a>Variablenpräfixe

Die folgende Tabelle zeigt Umgebungsvariablenpräfixe, die von App Service für verschiedene Zwecke verwendet werden.

| Einstellungsname | BESCHREIBUNG |
|-|-|
| `APPSETTING_` | Gibt an, dass eine Variable vom Kunden als App-Einstellung in der App-Konfiguration festgelegt wird. Sie wird als App-Einstellung in eine .NET-App eingefügt. |
| `MAINSITE_` | Gibt an, dass eine Variable für die App selbst spezifisch ist. |
| `SCMSITE_` | Gibt an, dass eine Variable für die Kudu-App spezifisch ist. |
| `SQLCONNSTR_` | Gibt eine SQL Server-Verbindungszeichenfolge in der App-Konfiguration an. Sie wird als Verbindungszeichenfolge in eine .NET-App eingefügt. |
| `SQLAZURECONNSTR_` | Gibt eine Azure SQL-Datenbank-Verbindungszeichenfolge in der App-Konfiguration an. Sie wird als Verbindungszeichenfolge in eine .NET-App eingefügt. |
| `POSTGRESQLCONNSTR_` | Gibt eine PostgreSQL-Verbindungszeichenfolge in der App-Konfiguration an. Sie wird als Verbindungszeichenfolge in eine .NET-App eingefügt. |
| `CUSTOMCONNSTR_` | Gibt eine benutzerdefinierte Verbindungszeichenfolge in der App-Konfiguration an. Sie wird als Verbindungszeichenfolge in eine .NET-App eingefügt. |
| `MYSQLCONNSTR_` | Gibt eine Azure SQL-Datenbank-Verbindungszeichenfolge in der App-Konfiguration an. Sie wird als Verbindungszeichenfolge in eine .NET-App eingefügt. |
| `AZUREFILESSTORAGE_` | Eine Verbindungszeichenfolge zu einer benutzerdefinierten Freigabe für eine Container-App in Azure Files. |
| `AZUREBLOBSTORAGE_` | Eine Verbindungszeichenfolge zu einem benutzerdefinierten Speicherkonto für eine Container-App in Azure Blob Storage. |
| `NOTIFICATIONHUBCONNSTR_` | Gibt eine Verbindungszeichenfolge zu einem Notification Hub in Azure Notification Hubs an. |
| `SERVICEBUSCONNSTR_` | Gibt eine Verbindungszeichenfolge zu einer Instanz von Azure Service Bus an. |
| `EVENTHUBCONNSTR_` | Gibt eine Verbindungszeichenfolge zu einem Event Hub in Azure Event Hubs an. |
| `DOCDBCONNSTR_` | Gibt eine Verbindungszeichenfolge zu einer Datenbank von Azure Cosmos DB an. |
| `REDISCACHECONNSTR_` | Gibt eine Verbindungszeichenfolge zu einem Cache in Azure Cache for Redis an. |
| `FILESHARESTORAGE_` | Gibt eine Verbindungszeichenfolge zu einer benutzerdefinierten Dateifreigabe an. |

## <a name="deployment"></a>Bereitstellung

Die folgenden Umgebungsvariablen beziehen sich auf die App-Bereitstellung. Variablen im Zusammenhang mit der App Service-Buildautomatisierung finden Sie unter [Buildautomatisierung](#build-automation).

| Einstellungsname| BESCHREIBUNG |
|-|-|
| `DEPLOYMENT_BRANCH`| Legen Sie für die [lokale Git-](deploy-local-git.md) oder [Cloud-Git-Bereitstellung](deploy-continuous-deployment.md) (z. B. GitHub) den Wert auf den Branch in Azure fest, in dem Sie bereitstellen möchten. Standardmäßig ist dies `master`. |
| `WEBSITE_RUN_FROM_PACKAGE`| Legen Sie den Wert auf `1` fest, um die App aus einem lokalen ZIP-Paket auszuführen, oder legen Sie ihn auf eine externe URL fest, um die App aus einem Remote-ZIP-Paket auszuführen. Weitere Informationen finden Sie unter [Direktes Ausführen Ihrer App in Azure App Service aus einem ZIP-Paket](deploy-run-package.md). |
| `WEBSITE_USE_ZIP` | Veraltet. Verwenden Sie `WEBSITE_RUN_FROM_PACKAGE`. |
| `WEBSITE_RUN_FROM_ZIP` | Veraltet. Verwenden Sie `WEBSITE_RUN_FROM_PACKAGE`. | 
| `WEBSITE_WEBDEPLOY_USE_SCM` | Legen Sie den Wert für WebDeploy auf `false` fest, um die Verwendung der Kudu-Bereitstellungs-Engine zu beenden. Der Standardwert lautet `true`. Legen Sie zum Bereitstellen in Linux-Apps mithilfe von Visual Studio (WebDeploy/MSDeploy) den Wert auf `false` fest. |
| `MSDEPLOY_RENAME_LOCKED_FILES` | Legen Sie den Wert auf `1` fest, um zu versuchen, DLLs umzubenennen, wenn sie während einer WebDeploy-Bereitstellung nicht kopiert werden können. Diese Einstellung ist nicht anwendbar, wenn `WEBSITE_WEBDEPLOY_USE_SCM` auf `false` festgelegt ist. |
| `WEBSITE_DISABLE_SCM_SEPARATION` | Standardmäßig werden die Haupt-App und die Kudu-App in verschiedenen Sandboxes ausgeführt. Wenn Sie die App beenden, wird die Kudu-App weiterhin ausgeführt, und Sie können Git deploy und MSDeploy weiterhin verwenden. Jede App verfügt über eigene lokale Dateien. Das Deaktivieren dieser Trennung (Einstellung auf `true`) ist ein Legacymodus, der nicht mehr vollständig unterstützt wird. |
| `WEBSITE_ENABLE_SYNC_UPDATE_SITE` | Legen Sie diesen Wert auf `1` fest, um sicherzustellen, dass REST-API-Aufrufe zur Aktualisierung von `site` und `siteconfig` vollständig auf alle Instanzen angewendet werden, bevor sie zurückgegeben werden. Der Standard ist `1` bei der Bereitstellung mit einer ARM-Vorlage, um Racebedingungen bei nachfolgenden ARM-Aufrufen zu vermeiden. |
| `WEBSITE_START_SCM_ON_SITE_CREATION` | Legen Sie den Wert in einer ARM-Vorlagenbereitstellung in der ARM-Vorlage auf `1` fest, um die Kudu-App im Rahmen der App-Erstellung vorab zu starten. |
| `WEBSITE_START_SCM_WITH_PRELOAD` | Legen Sie ihn für Linux-Apps auf `true` fest, um das Vorabladen der Kudu-App zu erzwingen, wenn Always On aktiviert ist, indem Sie ihre URL pingen. Der Standardwert lautet `false`. Für Windows Apps-wird die Kudu-App immer vorab geladen. |

<!-- 
WEBSITE_RUN_FROM_PACKAGE_BLOB_MI_RESOURCE_ID
-->

## <a name="build-automation"></a>Buildautomatisierung

# <a name="kudu-windows"></a>[Kudu (Windows)](#tab/kudu)

Die Kudu-Buildkonfiguration gilt für native Windows-Apps und wird verwendet, um das Verhalten von Git-basierten (oder ZIP-basierten) Bereitstellungen zu steuern.

| Einstellungsname| BESCHREIBUNG | Beispiel |
|-|-|-|
| `SCM_BUILD_ARGS` | Fügen Sie Elemente am Ende der MsBuild-Befehlszeile hinzu, sodass alle vorherigen Teile der Standardbefehlszeile überschrieben werden. | So erstellen Sie einen bereinigten Build: `-t:Clean;Compile`|
| `SCM_SCRIPT_GENERATOR_ARGS` | Kudu verwendet den [hier](http://blog.amitapple.com/post/38418009331/azurewebsitecustomdeploymentpart2) beschriebenen Befehl `azure site deploymentscript`, um ein Bereitstellungsskript zu generieren. Er erkennt automatisch den Typ des Sprachframeworks und bestimmt die Parameter, die an den Befehl übergeben werden sollen. Diese Einstellung überschreibt die automatisch generierten Parameter. | So behandeln Sie Ihr Repository als einfache Inhaltsdateien: `--basic -p <folder-to-deploy>` |
| `SCM_TRACE_LEVEL` | Buildablaufverfolgungsebene. Der Standardwert lautet `1`. Legen Sie für eine erweiterte Ablaufverfolgung die Einstellung auf höhere Werte (bis zu 4) fest. | `4` |
| `SCM_COMMAND_IDLE_TIMEOUT` | Timeout in Sekunden, bis zu dem für jeden Befehl, der vom Buildprozess gestartet wird, gewartet werden soll, wenn keine Ausgabe erzeugt wird. Danach wird der Befehl als im Leerlauf angesehen und beendet. Der Standard ist `60` (eine Minute). In Azure gibt es auch ein allgemeines Leerlaufanforderungstimeout, bei dem Clients nach 230 Sekunden getrennt werden. Danach wird der Befehl jedoch weiterhin serverseitig ausgeführt. | |
| `SCM_LOGSTREAM_TIMEOUT` | Timeout der Inaktivität in Sekunden vor dem Beenden des Protokollstreamings. Der Standard ist `1800` (30 Minuten).| |
| `SCM_SITEEXTENSIONS_FEED_URL` | URL des Katalogs für Websiteerweiterungen. Der Standardwert lautet `https://www.nuget.org/api/v2/`. Die URL des alten Feeds lautet `http://www.siteextensions.net/api/v2/`. | |
| `SCM_USE_LIBGIT2SHARP_REPOSITORY` | Legen Sie den Wert auf `0` fest, um git.exe anstelle von libgit2sharp für Git-Vorgänge zu verwenden. | |
| `WEBSITE_LOAD_USER_PROFILE` | Wenn der Fehler `The specified user does not have a valid profile.` während der ASP.NET-Buildautomatisierung eintritt (z. B. während der Git-Bereitstellung), legen Sie diese Variable auf `1` fest, um ein vollständiges Benutzerprofil in der Buildumgebung zu laden. Diese Einstellung ist nur anwendbar, wenn `WEBSITE_COMPUTE_MODE` auf `Dedicated` festgelegt ist. | |
| `WEBSITE_SCM_IDLE_TIMEOUT_IN_MINUTES` | Timeout in Minuten für die SCM (Kudu)-Site. Der Standardwert lautet `20`. | |
| `SCM_DO_BUILD_DURING_DEPLOYMENT` | Bei der [ZIP-Bereitstellung](deploy-zip.md) geht die Bereitstellungs-Engine davon aus, dass eine ZIP-Datei ohne weitere Maßnahmen ausführungsfähig ist, und führt deshalb auch keine Buildautomatisierung aus. Wenn dieselbe Buildautomatisierung wie in [Git deploy](deploy-local-git.md) aktiviert werden soll, legen Sie den Wert auf `true` fest. |

<!-- 
SCM_GIT_USERNAME
SCM_GIT_EMAIL
 -->

# <a name="oryx-linux"></a>[Oryx (Linux)](#tab/oryx)

Die Oryx-Buildkonfiguration gilt für Linux-Apps und wird verwendet, um das Verhalten von Git-basierten (oder ZIP-basierten) Bereitstellungen zu steuern. Siehe [Oryx-Konfiguration](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md).

-----

## <a name="language-specific-settings"></a>Sprachspezifische Einstellungen

In diesem Abschnitt werden die konfigurierbaren Laufzeiteinstellungen für jedes unterstützte Sprachframework angezeigt. Zusätzliche Einstellungen sind während der [Buildautomatisierung](#build-automation) zur Bereitstellungszeit verfügbar.

# <a name="net"></a>[.NET](#tab/dotnet)

<!-- 
| DOTNET_HOSTING_OPTIMIZATION_CACHE | 
 -->
| Einstellungsname | BESCHREIBUNG |
|-|-|
| `PORT` | Schreibgeschützt. Bei Linux-Apps der Port, an dem die .NET-Runtime im Container lauscht. |
| `WEBSITE_ROLE_INSTANCE_ID` | Schreibgeschützt. Die ID der aktuellen Instanz. |
| `HOME` | Schreibgeschützt. Verzeichnis, das auf freigegebenen Speicher verweist (`/home`). |
| `DUMP_DIR` | Schreibgeschützt. Verzeichnis für die Absturzabbilder (`/home/logs/dumps`). |
| `APP_SVC_RUN_FROM_COPY` | Nur Linux-Apps. Standardmäßig wird die App von `/home/site/wwwroot` ausgeführt, einem freigegebenen Verzeichnis für alle horizontal skalierten Instanzen. Legen Sie diese Variable auf `true` fest, um die App in ein lokales Verzeichnis in Ihrem Container zu kopieren und von dort aus auszuführen. Stellen Sie bei Verwendung dieser Option sicher, dass Sie keinen Verweis auf `/home/site/wwwroot` hartcodieren. Verwenden Sie stattdessen einen Pfad relativ zu `/home/site/wwwroot`. |
| `MACHINEKEY_Decryption` | Bei nativen Windows-Apps oder Windows-Container-Apps wird diese Variable in die App-Umgebung oder den Container eingefügt, um kryptografische ASP.NET-Routinen zu aktivieren (siehe [machineKey-Element](/previous-versions/dotnet/netframework-4.0/w8h3skw9(v=vs.100))). Um den Standardwert `decryption` zu überschreiben, konfigurieren Sie ihn als App Service-App-Einstellung, oder legen Sie ihn direkt im `machineKey`-Element der *Web.config*-Datei fest. |
| `MACHINEKEY_DecryptionKey` | Bei nativen Windows-Apps oder Windows-Container-Apps wird diese Variable in die App-Umgebung oder den Container eingefügt, um kryptografische ASP.NET-Routinen zu aktivieren (siehe [machineKey-Element](/previous-versions/dotnet/netframework-4.0/w8h3skw9(v=vs.100))). Um den automatisch generierten Wert `decryptionKey` zu überschreiben, konfigurieren Sie ihn als App Service-App-Einstellung, oder legen Sie ihn direkt im `machineKey`-Element der *Web.config*-Datei fest.|
| `MACHINEKEY_Validation` | Bei nativen Windows-Apps oder Windows-Container-Apps wird diese Variable in die App-Umgebung oder den Container eingefügt, um kryptografische ASP.NET-Routinen zu aktivieren (siehe [machineKey-Element](/previous-versions/dotnet/netframework-4.0/w8h3skw9(v=vs.100))). Um den Standardwert `validation` zu überschreiben, konfigurieren Sie ihn als App Service-App-Einstellung, oder legen Sie ihn direkt im `machineKey`-Element der *Web.config*-Datei fest.|
| `MACHINEKEY_ValidationKey` | Bei nativen Windows-Apps oder Windows-Container-Apps wird diese Variable in die App-Umgebung oder den Container eingefügt, um kryptografische ASP.NET-Routinen zu aktivieren (siehe [machineKey-Element](/previous-versions/dotnet/netframework-4.0/w8h3skw9(v=vs.100))). Um den automatisch generierten Wert `validationKey` zu überschreiben, konfigurieren Sie ihn als App Service-App-Einstellung, oder legen Sie ihn direkt im `machineKey`-Element der *Web.config*-Datei fest.|
<!-- | `USE_DOTNET_MONITOR` | if =true then /opt/dotnetcore-tools/dotnet-monitor collect --urls "http://0.0.0.0:50051" --metrics true --metricUrls "http://0.0.0.0:50050" > /dev/null 2>&1 & -->

# <a name="java"></a>[Java](#tab/java)

| Einstellungsname | BESCHREIBUNG | Beispiel |
|-|-|-|
| `JAVA_HOME` | Pfad des Java-Installationsverzeichnisses ||
| `JAVA_OPTS` | Umgebungsvariablen, die für Java SE-Apps an den Befehl `java` übergeben werden sollen. Kann Systemvariablen enthalten. Verwenden Sie für Tomcat `CATALINA_OPTS`. | `-Dmysysproperty=%DRIVEPATH%` |
| `AZURE_JAVA_APP_PATH` | Die Umgebungsvariable kann von benutzerdefinierten Skripts verwendet werden, sodass sie nach dem Kopieren in die lokale Datei über einen Speicherort für app.jar verfügen. | |
| `SKIP_JAVA_KEYSTORE_LOAD` | Wert 1, damit die Zertifikate nicht automatisch von App Service in den Keystore geladen werden. ||
| `WEBSITE_JAVA_JAR_FILE_NAME` | Die zu verwendende JAR-Datei. Fügt .jar an, wenn die Zeichenfolge nicht mit .jar endet. Der Standardwert ist app.jar. ||
| `WEBSITE_JAVA_WAR_FILE_NAME` | Die zu verwendende WAR-Datei. Fügt .war an, wenn die Zeichenfolge nicht mit .war endet. Der Standardwert ist app.war. ||
| `JAVA_ARGS` | Java-Optionen, die für verschiedene Java-Webserver erforderlich sind. Der Standardwert lautet `-noverify -Djava.net.preferIPv4Stack=true`. ||
| `JAVA_WEBSERVER_PORT_ENVIRONMENT_VARIABLES` | Umgebungsvariablen, die von gängigen Java-Webframeworks für Serverports verwendet werden. Zu den enthaltenen Frameworks gehören: Spring, Micronaut, Grails, MicroProfile Thorntail, Helidon, Ratpack und Quarksus. ||
| `JAVA_TMP_DIR` | Java-Argumenten als `-Dsite.tempdir` hinzugefügt. Wird standardmäßig auf `TEMP` festgelegt. ||
| `WEBSITE_SKIP_LOCAL_COPY` | Standardmäßig wird die bereitgestellte Datei app.jar aus `/home/site/wwwroot` an einen lokalen Speicherort kopiert. Damit dieses Verhalten deaktiviert und app.jar direkt aus `/home/site/wwwroot` geladen wird, legen Sie diese Variable auf `1` oder `true` fest. Diese Einstellung hat keine Auswirkungen, wenn der lokale Cache aktiviert ist. | |
| `TOMCAT_USE_STARTUP_BAT` | Nur native Windows-Apps. Standardmäßig wird der Tomcat-Server mit seiner `startup.bat` gestartet. Wenn er stattdessen mithilfe von `catalina.bat` initiiert werden soll, legen Sie den Wert auf `0` oder `False` fest. | `%LOCAL_EXPANDED%\tomcat` |
| `CATALINA_OPTS` | Umgebungsvariablen, die für Tomcat-Apps an den Befehl `java` übergeben werden sollen. Kann Systemvariablen enthalten. | |
| `CATALINA_BASE` | Legen Sie zur Verwendung einer benutzerdefinierten Tomcat-Installation den Wert auf den Speicherort der Installation fest. | |
| `WEBSITE_JAVA_MAX_HEAP_MB` | Der maximale Java-Heap in MB. Diese Einstellung ist nur wirksam, wenn eine experimentelle Tomcat-Version verwendet wird. | |
| `WEBSITE_DISABLE_JAVA_HEAP_CONFIGURATION` | Deaktivieren Sie `WEBSITE_JAVA_MAX_HEAP_MB` manuell, indem Sie diese Variable auf `true` oder `1` festlegen. | |
| `WEBSITE_AUTH_SKIP_PRINCIPAL` | Standardmäßig wird die folgende Tomcat-[HttpServletRequest-Schnittstelle](https://tomcat.apache.org/tomcat-5.5-doc/servletapi/javax/servlet/http/HttpServletRequest.html) mit Daten versorgt, wenn Sie die integrierte [Authentifizierung](overview-authentication-authorization.md) aktivieren: `isSecure`, `getRemoteAddr`, `getRemoteHost`, `getScheme`, `getServerPort`. Legen Sie zur Deaktivierung den Wert auf `1` fest.  | |
| `WEBSITE_SKIP_FILTERS` | Wenn alle von App Service hinzugefügten Servletfilter aktiviert werden sollen, legen Sie den Wert auf `1` fest. ||
| `IGNORE_CATALINA_BASE` | Standardmäßig überprüft App Service, ob die Tomcat-Variable `CATALINA_BASE` definiert ist. Wenn dies nicht der Fall ist, wird überprüft, ob `%HOME%\tomcat\conf\server.xml` vorhanden ist. Wenn die Datei vorhanden ist, wird `CATALINA_BASE` auf `%HOME%\tomcat` festgelegt. Damit dieses Verhalten deaktiviert und `CATALINA_BASE` entfernt wird, legen Sie diese Variable auf `1` oder `true` fest. ||
| `PORT` | Schreibgeschützt. Bei Linux-Apps der Port, an dem die Java-Runtime im Container lauscht. | |
| `WILDFLY_VERSION` | Schreibgeschützt. Für JBoss-Apps (Linux) die WildFly-Version. | |
| `TOMCAT_VERSION` | Schreibgeschützt. Für Linux-Tomcat-Apps die Tomcat-Version. ||
| `JBOSS_HOME` | Schreibgeschützt. Für JBoss-Apps (Linux) der Pfad der WildFly-Installation. | |
| `AZURE_JETTY9_CMDLINE` | Schreibgeschützt. Für native Windows-Apps Befehlszeilenargumente zum Starten von Jetty 9. | |
| `AZURE_JETTY9_HOME` | Schreibgeschützt. Bei nativen Windows-Apps der Pfad zur Jetty 9-Installation.| |
| `AZURE_JETTY93_CMDLINE` | Schreibgeschützt. Für native Windows-Apps Befehlszeilenargumente zum Starten von Jetty 9.3. | |
| `AZURE_JETTY93_HOME` | Schreibgeschützt. Bei nativen Windows-Apps der Pfad zur Jetty 9.3-Installation. | |
| `AZURE_TOMCAT7_CMDLINE` | Schreibgeschützt. Für native Windows-Apps Befehlszeilenargumente zum Starten von Tomcat 7. | |
| `AZURE_TOMCAT7_HOME` | Schreibgeschützt. Bei nativen Windows-Apps der Pfad zur Tomcat 7-Installation. | |
| `AZURE_TOMCAT8_CMDLINE` | Schreibgeschützt. Für native Windows-Apps Befehlszeilenargumente zum Starten von Tomcat 8. | |
| `AZURE_TOMCAT8_HOME` | Schreibgeschützt. Bei nativen Windows-Apps der Pfad zur Tomcat 8-Installation. | |
| `AZURE_TOMCAT85_CMDLINE` | Schreibgeschützt. Für native Windows-Apps Befehlszeilenargumente zum Starten von Tomcat 8.5. | |
| `AZURE_TOMCAT85_HOME` | Schreibgeschützt. Bei nativen Windows-Apps der Pfad zur Tomcat 8.5-Installation. | |
| `AZURE_TOMCAT90_CMDLINE` | Schreibgeschützt. Für native Windows-Apps Befehlszeilenargumente zum Starten von Tomcat 9. | |
| `AZURE_TOMCAT90_HOME` | Schreibgeschützt. Bei nativen Windows-Apps der Pfad zur Tomcat 9-Installation. | |
| `AZURE_SITE_HOME` | Der Wert, der den Java-Argumenten als `-Dsite.home` hinzugefügt wird. Der Standardwert ist der Wert von `HOME`. | |
| `HTTP_PLATFORM_PORT` | Java-Argumenten als `-Dport.http` hinzugefügt. Die folgenden Umgebungsvariablen, die von verschiedenen Java-Webframeworks verwendet werden, werden ebenfalls auf diesen Wert festgelegt: `SERVER_PORT`, `MICRONAUT_SERVER_PORT`, `THORNTAIL_HTTP_PORT`, `RATPACK_PORT`, `QUARKUS_HTTP_PORT`, `PAYARAMICRO_PORT`. ||
| `AZURE_LOGGING_DIR` | Nur native Windows-Apps. Java-Argumenten als `-Dsite.logdir` hinzugefügt. Der Standardwert lautet `%HOME%\LogFiles\`. ||

<!-- 
WEBSITE_JAVA_COPY_ALL
AZURE_SITE_APP_BASE
 -->

# <a name="nodejs"></a>[Node.js](#tab/node)

| Einstellungsname | BESCHREIBUNG |
|-|-|
| `PORT` | Schreibgeschützt. Bei Linux-Apps der Port, an dem die Node.js-Runtime im Container lauscht. |
| `WEBSITE_ROLE_INSTANCE_ID` | Schreibgeschützt. Die ID der aktuellen Instanz. |
| `PM2HOME` | |
| `WEBSITE_NODE_DEFAULT_VERSION` | Bei nativen Windows-Apps die Standardknotenversion, die von der App verwendet wird. Jede der [unterstützten Node.js-Versionen](configure-language-nodejs.md#show-nodejs-version) kann hier verwendet werden. |

<!-- APPSVC_REMOTE_DEBUGGING
APPSVC_REMOTE_DEBUGGING_BREAK
APPSVC_TUNNEL_PORT -->

# <a name="python"></a>[Python](#tab/python)

| Einstellungsname | BESCHREIBUNG |
|-|-|
| `APPSVC_VIRTUAL_ENV` | Schreibgeschützt. |
| `PORT` | Schreibgeschützt. Bei Linux-Apps der Port, an dem die Python-App im Container lauscht. |

<!-- APPSVC_REMOTE_DEBUGGING
APPSVC_TUNNEL_PORT | -debugAdapter ptvsd -debugPort $APPSVC_TUNNEL_PORT"
APPSVC_REMOTE_DEBUGGING_BREAK | debugArgs+=" -debugWait" -->

# <a name="php"></a>[PHP](#tab/php)

| Einstellungsname | BESCHREIBUNG | Beispiel|
|-|-|-|
| `PHP_Extensions` | Durch Trennzeichen getrennte Liste von PHP-Erweiterungen. | `extension1.dll,extension2.dll,Name1=value1` |
| `PHP_ZENDEXTENSIONS` | Legen Sie für native Windows-Apps den Wert auf den Pfad der XDebug-Erweiterung fest, z. B. `D:\devtools\xdebug\2.6.0\php_7.2\php_xdebug-2.6.0-7.2-vc15-nts.dll`. Legen Sie für Linux-Apps den Wert auf `xdebug` fest, um die XDebug-Version des PHP-Containers zu verwenden. ||
| `PHP_VERSION` | Schreibgeschützt. Die ausgewählte PHP-Version. ||
| `PORT` | Schreibgeschützt. Der Port, an dem der Apache-Server im Container lauscht. ||
| `WEBSITE_ROLE_INSTANCE_ID` | Schreibgeschützt. Die ID der aktuellen Instanz. ||
| `WEBSITE_PROFILER_ENABLE_TRIGGER` | Legen Sie den Wert auf `TRUE` fest, um `xdebug.profiler_enable_trigger=1` und `xdebug.profiler_enable=0` zur Standard-`php.ini` hinzuzufügen. ||
| `WEBSITE_ENABLE_PHP_ACCESS_LOGS` | Legen Sie den Wert auf `TRUE` fest, um Anforderungen an den Server zu protokollieren (`CustomLog \dev\stderr combined` wird `/etc/apache2/apache2.conf` hinzugefügt). ||
| `APACHE_SERVER_LIMIT` | Apache-spezifische Variable. Der Standardwert lautet `1000`. ||
| `APACHE_MAX_REQ_WORKERS` | Apache-spezifische Variable. Der Standardwert lautet `256`. ||

<!-- 
ZEND_BIN_PATH
MEMCACHESHIM_REDIS_ENABLE
MEMCACHESHIM_PORT 
APACHE_LOG_DIR | RUN sed -i 's!ErrorLog ${APACHE_LOG_DIR}/error.log!ErrorLog /dev/stderr!g' /etc/apache2/apache2.conf 
APACHE_RUN_USER | RUN sed -i 's!User ${APACHE_RUN_USER}!User www-data!g' /etc/apache2/apache2.conf 
APACHE_RUN_GROUP | RUN sed -i 's!User ${APACHE_RUN_GROUP}!Group www-data!g' /etc/apache2/apache2.conf  
-->

# <a name="ruby"></a>[Ruby](#tab/ruby)

| Einstellungsname | BESCHREIBUNG | Beispiel |
|-|-|-|
| `PORT` | Schreibgeschützt. Port, an dem die Rails-App im Container lauscht. ||
| `WEBSITE_ROLE_INSTANCE_ID` | Schreibgeschützt. Die ID der aktuellen Instanz. ||
| `RAILS_IGNORE_SPLASH` | Standardmäßig wird eine Standard-Begrüßungsseite angezeigt, wenn keine Gemfile gefunden wird. Legen Sie diese Variable auf einen beliebigen Wert fest, um die Begrüßungsseite zu deaktivieren. ||
| `BUNDLE_WITHOUT` | Legen Sie zum Hinzufügen von `--without`-Optionen zu `bundle install` die Variable auf die Gruppen fest, die Sie ausschließen möchten, getrennt durch Leerzeichen. Standardmäßig werden alle Gems installiert. | `test development` |
| `BUNDLE_INSTALL_LOCATION` | Verzeichnis zum Installieren von Gems. Der Standardwert lautet `/tmp/bundle`. ||
| `RUBY_SITE_CONFIG_DIR` | Sitekonfigurationsverzeichnis. Der Standardwert lautet `/home/site/config`. Der Container sucht in diesem Verzeichnis nach gezippten Gems. ||
| `SECRET_KEY_BASE` | Standardmäßig wird eine zufällige Basis geheimer Schlüssel generiert. Zur Verwendung einer benutzerdefinierten Basis geheimer Schlüssel legen Sie diese Variable auf die gewünschte Schlüsselbasis fest. ||
| `RAILS_ENV` | Rails-Umgebung. Der Standardwert lautet `production`. ||
| `GEM_PRISTINE` | Legen Sie diese Variable auf einen beliebigen Wert fest, um `gem pristine --all` auszuführen. ||

-----

## <a name="domain-and-dns"></a>Domäne und DNS

| Einstellungsname| BESCHREIBUNG | Beispiel |
|-|-|-|
| `WEBSITE_DNS_SERVER` | IP-Adresse des primären DNS-Servers für ausgehende Verbindungen (z. B. zu einem Back-End-Dienst). Der DNS-Standardserver für App Service ist Azure DNS, dessen IP-Adresse `168.63.129.16` ist. Wenn Ihre App die [VNet-Integration](./overview-vnet-integration.md) verwendet oder sich in einer [App Service-Umgebung](environment/intro.md) befindet, erbt sie standardmäßig die DNS-Serverkonfiguration vom VNet. | `10.0.0.1` |
| `WEBSITE_DNS_ALT_SERVER` | IP-Adresse des Fallback-DNS-Servers für ausgehende Verbindungen. Siehe `WEBSITE_DNS_SERVER`. | |

<!-- 
DOMAIN_OWNERSHIP_VERIFICATION_IDENTIFIERS
 -->

## <a name="tlsssl"></a>TLS/SSL

Weitere Informationen finden Sie unter [Verwenden eines TLS-/SSL-Zertifikats in Ihrem Code in Azure App Service](configure-ssl-certificate-in-code.md).

| Einstellungsname| BESCHREIBUNG |
|-|-|
| `WEBSITE_LOAD_CERTIFICATES` | Durch Trennzeichen getrennte Fingerabdruckwerte für das Zertifikat, das Sie in Ihren Code laden möchten, oder `*`, um das Laden aller Zertifikate in Code zu ermöglichen. Nur [Zertifikate, die Ihrer App hinzugefügt wurden](configure-ssl-certificate.md), können geladen werden. |
| `WEBSITE_PRIVATE_CERTS_PATH` | Schreibgeschützt. Pfad in einem Windows-Container zu den geladenen privaten Zertifikaten. |
| `WEBSITE_PUBLIC_CERTS_PATH` | Schreibgeschützt. Pfad in einem Windows-Container zu den geladenen öffentlichen Zertifikaten. |
| `WEBSITE_INTERMEDIATE_CERTS_PATH` | Schreibgeschützt. Pfad in einem Windows-Container zu den geladenen Zwischenzertifikaten. |
| `WEBSITE_ROOT_CERTS_PATH` | Schreibgeschützt. Pfad in einem Windows-Container zu den geladenen Stammzertifikaten. |

## <a name="deployment-slots"></a>Bereitstellungsslots 

Weitere Informationen zu Bereitstellungsslots finden Sie unter [Einrichten von Stagingumgebungen in Azure App Service](deploy-staging-slots.md).

| Einstellungsname| BESCHREIBUNG | Beispiel |
|-|-|-|
|`WEBSITE_SLOT_NAME`| Schreibgeschützt. Name des aktuellen Bereitstellungsslots. Der Name des Produktionsslots ist `Production`. ||
|`WEBSITE_OVERRIDE_STICKY_EXTENSION_VERSIONS`| Standardmäßig sind die Versionen für Websiteerweiterungen für jeden Slot spezifisch. Dies verhindert unerwartetes Anwendungsverhalten aufgrund von Änderungen der Erweiterungsversionen nach einem Austausch. Wenn Sie möchten, dass die Erweiterungsversionen auch ausgetauscht werden, legen Sie für *alle Slots* den Wert auf `1` fest. ||
|`WEBSITE_OVERRIDE_PRESERVE_DEFAULT_STICKY_SLOT_SETTINGS`| Bestimmte Einstellungen werden [standardmäßig als „persistent“ oder „nicht austauschbar“](deploy-staging-slots.md#which-settings-are-swapped) festgelegt. Der Standardwert ist `true`. Legen Sie diese Einstellung für *alle Bereitstellungsslots* auf `false` oder `0` fest, damit sie stattdessen ausgetauscht werden können. Es gibt keine fein abgrenzende Steuerung für bestimmte Einstellungstypen. ||
|`WEBSITE_SWAP_WARMUP_PING_PATH`| Ein Pfad zum Pingen, um den Zielslot in einem Austausch vorzubereiten. Er beginnt mit einem Schrägstrich. Der Standardwert ist `/` und pingt den Stammpfad über HTTP. | `/statuscheck` |
|`WEBSITE_SWAP_WARMUP_PING_STATUSES`| Gültige HTTP-Antwortcodes für den Vorbereitungsvorgang während eines Austauschs. Ist der zurückgegebene Statuscode nicht in der Liste enthalten, werden die Vorbereitungs- und Austauschvorgänge beendet. Standardmäßig sind alle Antwortcodes gültig. | `200,202` |
| `WEBSITE_SLOT_NUMBER_OF_TIMEOUTS_BEFORE_RESTART` | Während eines Slottauschs die maximale Anzahl von Timeouts, nach denen ein Neustart der Site auf einer bestimmten VM-Instanz erzwungen wird. Der Standardwert lautet `3`. ||
| `WEBSITE_SLOT_MAX_NUMBER_OF_TIMEOUTS` | Während eines Slottauschs die maximale Anzahl von Timeoutanforderungen für eine einzelne URL, bevor aufgegeben wird. Der Standardwert lautet `5`. ||
| `WEBSITE_SKIP_ALL_BINDINGS_IN_APPHOST_CONFIG` | Legen Sie den Wert auf `true` oder `1` fest, um alle Bindungen in `applicationHost.config` zu überspringen. Der Standardwert lautet `false`. Wenn Ihre App einen Neustart auslöst, weil `applicationHost.config` mit den ausgetauschten Hostnamen der Slots aktualisiert wird, legen Sie diese Variable auf `true` fest, um einen Neustart dieser Art zu vermeiden. Wenn Sie eine WCF-App (Windows Communication Foundation) ausführen, legen Sie diese Variable nicht fest. ||

<!-- 
|`WEBSITE_SWAP_SLOTNAME`||| 
-->

## <a name="custom-containers"></a>Benutzerdefinierte Container

Weitere Informationen zu benutzerdefinierten Containern finden Sie unter [Ausführen eines benutzerdefinierten Containers in Azure](quickstart-custom-container.md).

| Einstellungsname| BESCHREIBUNG | Beispiel |
|-|-|-|
| `WEBSITES_ENABLE_APP_SERVICE_STORAGE` | Legen Sie diese Option auf `true` fest, damit das `/home`-Verzeichnis für skalierte Instanzen freigegeben werden kann. Der Standard ist `false` für benutzerdefinierte Container. ||
| `WEBSITES_CONTAINER_START_TIME_LIMIT` | Die Zeit in Sekunden, die auf den Abschluss des Containerstartvorgangs gewartet wird, bevor der Container neu gestartet wird. Der Standardwert ist `230`. Sie können ihn bis zum Maximum von `1800` erhöhen. ||
| `DOCKER_REGISTRY_SERVER_URL` | URL des Registrierungsservers beim Ausführen eines benutzerdefinierten Containers in App Service. Aus Sicherheitsgründen wird diese Variable nicht an den Container übergeben. | `https://<server-name>.azurecr.io` |
| `DOCKER_REGISTRY_SERVER_USERNAME` | Benutzername für die Authentifizierung beim Registrierungsserver unter `DOCKER_REGISTRY_SERVER_URL`. Aus Sicherheitsgründen wird diese Variable nicht an den Container übergeben. ||
| `DOCKER_REGISTRY_SERVER_PASSWORD` | Kennwort für die Authentifizierung beim Registrierungsserver unter `DOCKER_REGISTRY_SERVER_URL`. Aus Sicherheitsgründen wird diese Variable nicht an den Container übergeben. ||
| `WEBSITES_WEB_CONTAINER_NAME` | In einer Docker Compose-App kann nur einer der Container über das Internet zugänglich sein. Legen Sie den Wert auf den Namen des Containers fest, der in der Konfigurationsdatei definiert ist, um die Standardauswahl des Containers zu überschreiben. Standardmäßig ist der über das Internet zugängliche Container der erste Container, der Port 80 oder 8080 definiert, oder, wenn kein solcher Container gefunden wird, der erste Container, der in der Konfigurationsdatei definiert ist. |  |
| `WEBSITES_PORT` | Bei einem benutzerdefinierten Container die benutzerdefinierte Portnummer für den Container, an den App Service Anforderungen weiterleiten soll. Standardmäßig versucht App Service die automatische Porterkennung der Ports 80 und 8080. Diese Einstellung wird *nicht* als Umgebungsvariable in den Container eingefügt. ||
| `WEBSITE_CPU_CORES_LIMIT` | Standardmäßig wird ein Windows-Container mit allen verfügbaren Kernen für Ihren ausgewählten Tarif ausgeführt. Zum Verringern der Anzahl der Kerne legen Sie den Wert auf den Grenzwert für die Anzahl der gewünschten Kerne fest. Weitere Informationen finden Sie unter [Anpassen der Anzahl der Compute-Kerne](configure-custom-container.md?pivots=container-windows#customize-the-number-of-compute-cores).||
| `WEBSITE_MEMORY_LIMIT_MB` | Standardmäßig sind alle in Azure App Service bereitgestellten Windows-Container auf 1 GB RAM beschränkt. Legen Sie den Wert auf den gewünschten Arbeitsspeichergrenzwert in MB fest. Der kumulierte Gesamtwert dieser Einstellung für Apps im gleichen Plan darf den im ausgewählten Tarif zulässigen Betrag nicht überschreiten. Weitere Informationen finden Sie unter [Anpassen des Containerarbeitsspeichers](configure-custom-container.md?pivots=container-windows#customize-container-memory). ||
| `CONTAINER_WINRM_ENABLED` | Legen Sie für eine Windows Container-App den Wert auf `1` fest, um Windows-Remoteverwaltung (WIN-RM) zu aktivieren. ||

<!-- 
CONTAINER_ENCRYPTION_KEY
CONTAINER_NAME
CONTAINER_IMAGE_URL
AzureWebEncryptionKey
CONTAINER_START_CONTEXT_SAS_URI
CONTAINER_AZURE_FILES_VOLUME_MOUNT_PATH
CONTAINER_START_CONTEXT
DOCKER_ENABLE_CI
WEBSITE_DISABLE_PRELOAD_HANG_MITIGATION
 -->

## <a name="scaling"></a>Skalierung

| Einstellungsname| BESCHREIBUNG |
|-|-|
| `WEBSITE_INSTANCE_ID` | Schreibgeschützt. Eindeutige ID der aktuellen VM-Instanz, wenn die App auf mehrere Instanzen ausskaliert wird. |
| `WEBSITE_IIS_SITE_NAME` | Veraltet. Verwenden Sie `WEBSITE_INSTANCE_ID`. |
| `WEBSITE_DISABLE_OVERLAPPED_RECYCLING` | Durch die überlappende Wiederverwendung wird eine neue VM-Instanz gestartet, bevor die aktuelle VM-Instanz einer App heruntergefahren wird. In einigen Fällen kann dies zu Problemen beim Sperren von Dateien führen. Sie können versuchen, sie zu deaktivieren, indem Sie den Wert auf `1` festlegen. |
| `WEBSITE_DISABLE_CROSS_STAMP_SCALE` | Standardmäßig dürfen Apps stempelübergreifend skaliert werden, wenn sie Azure Files oder einen Docker-Container verwenden. Legen Sie den Wert auf `1` oder `true` fest, um die stempelübergreifende Skalierung innerhalb der Region der App zu deaktivieren. Der Standardwert lautet `0`. Benutzerdefinierte Docker-Container, die `WEBSITES_ENABLE_APP_SERVICE_STORAGE` auf `true` oder `1` festlegen, können nicht stempelübergreifend skalieren, da ihr Inhalt nicht vollständig im Docker-Container gekapselt ist. |

## <a name="logging"></a>Protokollierung

| Einstellungsname| BESCHREIBUNG | Beispiel |
|-|-|-|
| `WEBSITE_HTTPLOGGING_ENABLED` | Schreibgeschützt. Zeigt an, ob die Webserverprotokollierung für native Windows-Apps aktiviert (`1`) oder nicht aktiviert (`0`) ist. ||
| `WEBSITE_HTTPLOGGING_RETENTION_DAYS` | Aufbewahrungsdauer von Webserverprotokollen für native Windows-Apps in Tagen, wenn Webserverprotokolle aktiviert sind. | `10` |
| `WEBSITE_HTTPLOGGING_CONTAINER_URL` | SAS-URL des Blob Storage-Containers zum Speichern von Webserverprotokollen für native Windows-Apps in Tagen, wenn Webserverprotokolle aktiviert sind. Wenn diese Einstellung nicht festgelegt ist, werden Webserverprotokolle im Dateisystem der App gespeichert (freigegebener Standardspeicher). | |
| `DIAGNOSTICS_AZUREBLOBRETENTIONINDAYS` | Aufbewahrungsdauer von Anwendungsprotokollen für native Windows-Apps in Tagen, wenn Anwendungsprotokolle aktiviert sind. | `10` |
| `DIAGNOSTICS_AZUREBLOBCONTAINERSASURL` | SAS-URL des Blob Storage-Containers zum Speichern von Anwendungsprotokollen für native Windows-Apps in Tagen, wenn Anwendungsprotokolle aktiviert sind. | |
| `APPSERVICEAPPLOGS_TRACE_LEVEL` | Minimale Protokollebene, die für den Protokolltyp [AppServiceAppLogs](troubleshoot-diagnostic-logs.md#supported-log-types) an Log Analytics versendet werden soll. | |
| `DIAGNOSTICS_LASTRESORTFILE` | Der zu erstellende Dateiname oder ein relativer Pfad zum Protokollverzeichnis für die Protokollierung interner Fehler für die Problembehandlung des Listeners. Der Standardwert lautet `logging-errors.txt`. ||
| `DIAGNOSTICS_LOGGINGSETTINGSFILE` | Pfad zur Protokolleinstellungsdatei relativ zu `D:\home` oder `/home`. Der Standardwert lautet `site\diagnostics\settings.json`. | |
| `DIAGNOSTICS_TEXTTRACELOGDIRECTORY` | Der Protokollordner relativ zum App-Stammverzeichnis (`D:\home\site\wwwroot` oder `/home/site/wwwroot`). | `..\..\LogFiles\Application`|
| `DIAGNOSTICS_TEXTTRACEMAXLOGFILESIZEBYTES` | Die maximale Größe der Protokolldatei in Byte. Die Standardeinstellung ist `131072` (128 KB). ||
| `DIAGNOSTICS_TEXTTRACEMAXLOGFOLDERSIZEBYTES` | Die maximale Größe des Protokollordners in Byte. Der Standardwert ist `1048576` (1 MB). ||
| `DIAGNOSTICS_TEXTTRACEMAXNUMLOGFILES` | Die Maximale Anzahl von Protokolldateien, die beibehalten werden sollen. Der Standardwert lautet `20`. | |
| `DIAGNOSTICS_TEXTTRACETURNOFFPERIOD` | Timeout in Millisekunden, bis zu dem die Anwendungsprotokollierung aktiviert bleiben soll. Die Standardeinstellung beträgt `43200000` (12 Stunden). ||
| `WEBSITE_LOG_BUFFERING` | Standardmäßig ist die Protokollpufferung aktiviert. Legen Sie den Wert auf `0` fest, um sie zu deaktivieren. ||
| `WEBSITE_ENABLE_PERF_MODE` | Legen Sie für native Windows-Apps den Wert auf `TRUE` fest, um IIS-Protokolleinträge für erfolgreiche Anforderungen zu deaktivieren, die innerhalb von 10 Sekunden zurückgegeben werden. Dies ist eine schnelle Möglichkeit, Leistungsvergleichstests durchzuführen, indem die erweiterte Protokollierung entfernt wird. ||

<!-- 
| `DIAGNOSTICS_AZURETABLESASURL` | old? | |
| WEBSITE_APPSERVICEAPPLOGS_TRACE_ENABLED | Read-only. Added when | | 
| AZMON_LOG_CATEGORY_APPSERVICEAPPLOGS_ENABLED | Read-only. Shows when the AppServiceAppLogs category in Azure Monitor settings is enabled. |
AZMON_LOG_CATEGORY_APPSERVICEPLATFORMLOGS_ENABLED  | Read-only. Shows when the AppServiceAppLogs category in Azure Monitor settings is enabled. |
AZMON_LOG_CATEGORY_APPSERVICECONSOLELOGS_ENABLED | Read-only. Shows when the AppServiceConsoleLogs category in Azure Monitor settings is enabled. |
WEBSITE_FUNCTIONS_AZUREMONITOR_CATEGORIES
WINDOWS_TRACING_FLAGS
WINDOWS_TRACING_LOGFILE
WEBSITE_FREB_DISABLE
WEBSITE_ARR_SESSION_AFFINITY_DISABLE

-->

## <a name="performance-counters"></a>Leistungsindikatoren

Im Folgenden sind „Fake“-Umgebungsvariablen aufgeführt, die nicht vorhanden sind, wenn Sie sie aufzählen, aber ihren Wert zurückgeben, wenn Sie sie einzeln suchen. Der Wert ist dynamisch und kann sich bei jedem Lookup ändern.

| Einstellungsname| BESCHREIBUNG |
|-|-|
| `WEBSITE_COUNTERS_ASPNET` | Ein JSON-Objekt, das die ASP.NET Leistungsindikatoren enthält. |
| `WEBSITE_COUNTERS_APP` | Ein JSON-Objekt, das die Sandbox-Indikatoren enthält. |
| `WEBSITE_COUNTERS_CLR` | Ein JSON-Objekt, das die CLR-Indikatoren enthält. |
| `WEBSITE_COUNTERS_ALL` | Ein JSON-Objekt, das die Kombination der anderen drei Variablen enthält. |

## <a name="caching"></a>Caching

| Einstellungsname| BESCHREIBUNG |
|-|-|
| `WEBSITE_LOCAL_CACHE_OPTION` | Gibt an, ob der lokale Cache aktiviert ist. Die verfügbaren Optionen lauten wie folgt: <br/>- `Default`: die globale Einstellung auf Stempelebene erben.<br/>- `Always`: für die App aktivieren.<br/>– OnStorageUnavailability<br/>- `Disabled`: für die App deaktiviert. |
| `WEBSITE_LOCAL_CACHE_READWRITE_OPTION` | Lese-/Schreiboptionen des lokalen Caches. Die verfügbaren Optionen lauten wie folgt: <br/>- `ReadOnly`: Cache ist schreibgeschützt.<br/>- `WriteWithCopyBack`: Schreibvorgänge in den lokalen Cache zulassen und in regelmäßigen Abständen in freigegebenen Speicher kopieren. Gilt nur für Einzelinstanz-Apps, da die SCM-Website auf den lokalen Cache verweist.<br/>- `WriteButDiscardChanges`: Schreibvorgänge in den lokalen Cache zulassen, aber lokal vorgenommene Änderungen verwerfen. |
| `WEBSITE_LOCAL_CACHE_SIZEINMB` | Größe des lokalen Caches in MB. Der Standardwert lautet `1000` (1 GB). |
| `WEBSITE_LOCALCACHE_READY` | Schreibgeschütztes Flag, das angibt, ob die App den lokalen Cache verwendet. |
| `WEBSITE_DYNAMIC_CACHE` | Aufgrund der gemeinsamen Nutzung von Netzwerkdateien, um den Zugriff für mehrere Instanzen zu ermöglichen, verbessert der dynamische Cache die Leistung, indem die zuletzt verwendeten Dateien lokal auf einer Instanz zwischengespeichert werden. Der Cache wird ungültig, wenn die Datei geändert wird. Der Cachespeicherort ist `%SYSTEMDRIVE%\local\DynamicCache` (das gleiche `%SYSTEMDRIVE%\local`-Kontingent wird angewendet). Standardmäßig ist das vollständige Content Caching aktiviert (auf `1` festgelegt), das sowohl Dateiinhalte als auch Verzeichnis-/Dateimetadaten (Zeitstempel, Größe, Verzeichnisinhalt) umfasst. Zum Einsparen von Speicherplatz auf dem lokalen Datenträger legen Sie den Wert auf `2` fest, um nur Verzeichnis-/Dateimetadaten (Zeitstempel, Größe, Verzeichnisinhalt) zwischenzuspeichern. Zum Deaktivieren des Zwischenspeicherns legen Sie den Wert auf `0` fest. |
| `WEBSITE_READONLY_APP` | Bei Verwendung des dynamischen Caches können Sie den Schreibzugriff auf den App-Stamm (`D:\home\site\wwwroot` oder `/home/site/wwwroot`) deaktivieren, indem Sie diese Variable auf `1` festlegen. Mit Ausnahme des Verzeichnisses `App_Data` sind keine exklusiven Sperren zulässig, damit Bereitstellungen nicht durch gesperrte Dateien blockiert werden. |

<!-- 
HTTP_X_LOCALCACHE_READY_CHECK
HTTP_X_APPINIT_CHECK
X_SERVER_ROUTED
HTTP_X_MS_REQUEST_ID
HTTP_X_MS_APIM_HOST
HTTP_X_MS_FORWARD_HOSTNAMES
HTTP_X_MS_FORWARD_TOKEN
HTTP_MWH_SECURITYTOKEN
IS_SERVICE_ENDPOINT
VNET_CLIENT_IP
HTTP_X_MS_SITE_RESTRICTED_TOKEN
HTTP_X_FROM
| LOCAL_ADDR | internal private IP address of app |
SERVERS_FOR_HOSTING_SERVER_PROVIDER
NEED_PLATFORM_AUTHORIZATION
TIP_VALUE
TIP_RULE_NAME
TIP_RULE_MAX_AGE
REWRITE_PATH
NEGOTIATE_CLIENT_CERT
| CLIENT_CERT_MODE | used with ClientCertEnabled. Required means ClientCert is required.  Optional means ClientCert is optional or accepted. OptionalInteractiveUser is similar to Optional; however, it will not ask user for Certificate in Browser Interactive scenario.|
| HTTPS_ONLY | set with terraform? |
 -->

## <a name="networking"></a>Netzwerk

Die folgenden Umgebungsvariablen beziehen sich auf [Hybridverbindungen](app-service-hybrid-connections.md) und die [VNet-Integration](./overview-vnet-integration.md).

| Einstellungsname | BESCHREIBUNG |
|-|-|
| `WEBSITE_RELAYS` | Schreibgeschützt. Daten, die zum Konfigurieren der Hybridverbindung erforderlich sind, einschließlich Endpunkte und Service Bus-Daten. |
| `WEBSITE_REWRITE_TABLE` | Schreibgeschützt. Wird zur Laufzeit verwendet, um die Lookups durchzuführen und die Verbindungen entsprechend neu zu schreiben. | 
| `WEBSITE_VNET_ROUTE_ALL` | Wenn Sie die [regionale VNet-Integration](./overview-vnet-integration.md#regional-virtual-network-integration) verwenden, leitet Ihre App standardmäßig nur RFC1918-Datenverkehr an Ihr VNet weiter. Legen Sie diese Einstellung auf `1` fest, um den gesamten ausgehenden Datenverkehr an Ihr VNet weiterzuleiten und denselben NSGs und UDRs zu unterliegen. Mit der Einstellung können Sie über Ihr VNet auf Nicht-RFC1918-Endpunkte zugreifen, den gesamten ausgehenden Datenverkehr, der Ihre App verlässt, schützen und erzwingen, dass der gesamte ausgehende Datenverkehr zu einer von Ihnen ausgewählten Netzwerkappliance getunnelt wird. |
| `WEBSITE_PRIVATE_IP` | Schreibgeschützt. IP-Adresse, die der App zugeordnet ist, wenn [sie mit einem VNet integriert](./overview-vnet-integration.md) ist. Bei der regionalen VNet-Integration ist der Wert eine IP-Adresse aus dem Adressbereich des delegierten Subnetzes, und bei der für das Gateway erforderlichen VNet-Integration ist der Wert eine IP-Adresse aus dem Adressbereich des Point-to-Site-Adresspools, der auf dem Gateway des virtuellen Netzwerks konfiguriert ist. Diese IP-Adresse wird von der App zum Herstellen einer Verbindung mit den Ressourcen über das VNet verwendet. Sie kann sich auch innerhalb des beschriebenen Adressbereichs ändern. |
| `WEBSITE_PRIVATE_PORTS` | Schreibgeschützt. Zeigt in der VNet-Integration an, welche Ports von der App für die Kommunikation mit anderen Knoten verwendet werden können. |

<!-- | WEBSITE_SLOT_POLL_WORKER_FOR_CHANGE_NOTIFICATION | Poll worker before pinging the site to detect when change notification has been processed. |
WEBSITE_SPECIAL_CACHE
WEBSITE_SOCKET_STATISTICS_ENABLED
| `WEBSITE_ENABLE_NETWORK_HEALTHCHECK` | Enable network health checks that won't be blocked by CORS or built-in authentication. Three check methods can be utilized: <br/>- Ping an IP address (configurable by `WEBSITE_NETWORK_HEALTH_IPADDRS`). <br/>- Check DNS resolution (configurable by `WEBSITE_NETWORK_HEALTH_DNS_ENDPOINTS`). <br/>- Poll URI endpoints (configurable by `WEBSITE_NETWORK_HEALTH_URI_ENDPOINTS`).<br/> |
| `WEBSITE_NETWORK_HEALTH_IPADDRS` | https://msazure.visualstudio.com/One/_git/AAPT-Antares-EasyAuth/pullrequest/3763264 |
| `WEBSITE_NETWORK_HEALTH_DNS_ENDPOINTS` | |
| `WEBSITE_NETWORK_HEALTH_URI_ENDPOINTS` | |
| `WEBSITE_NETWORK_HEALTH_INTEVALSECS` | Interval of the network health check in seconds. The minimum value is 30 seconds. | |
| `WEBSITE_NETWORK_HEALTH_TIMEOUT_INTEVALSECS` | Time-out of the network health check in seconds. | |

-->
<!-- | CONTAINER_WINRM_USERNAME |
| CONTAINER_WINRM_PASSWORD| -->

## <a name="key-vault-references"></a>Key Vault-Verweise

Die folgenden Umgebungsvariablen beziehen sich auf [Key Vault-Verweise](app-service-key-vault-references.md).

| Einstellungsname | BESCHREIBUNG |
|-|-|
| `WEBSITE_KEYVAULT_REFERENCES` | Schreibgeschützt. Enthält Informationen (einschließlich Status) für alle Key Vault-Verweise, die derzeit in der App konfiguriert sind. |
| `WEBSITE_SKIP_CONTENTSHARE_VALIDATION` | Wenn Sie die freigegebene Speicherverbindung Ihrer App (mithilfe von `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`) auf einen Key Vault-Verweis festlegen, kann die App den Key Vault-Verweis bei der App-Erstellung oder -Aktualisierung nicht auflösen, wenn eine der folgenden Bedingungen zutrifft: <br/>– Die App greift mit einer vom System zugewiesenen Identität auf den Schlüsseltresor zu.<br/>– Die App greift mit einer vom Benutzer zugewiesenen Identität auf den Schlüsseltresor zu, und der Schlüsseltresor wird [ mit einem VNet gesperrt](../key-vault/general/overview-vnet-service-endpoints.md).<br/>Zum Vermeiden von Fehlern beim Erstellen oder Aktualisieren legen Sie diese Variable auf `1` fest. |
| `WEBSITE_DELAY_CERT_DELETION` | Diese Umgebungsvariable kann von Benutzern auf 1 festgelegt werden, um sicherzustellen, dass ein Zertifikat, von dem ein Workerprozess abhängig ist, nicht gelöscht wird, bis es beendet wird. |
<!-- | `WEBSITE_ALLOW_DOUBLE_ESCAPING_URL` | TODO | -->

## <a name="cors"></a>CORS

Die folgenden Umgebungsvariablen beziehen sich auf die CORS-Konfiguration (Cross-Origin Resource Sharing).

| Einstellungsname | BESCHREIBUNG |
|-|-|
| `WEBSITE_CORS_ALLOWED_ORIGINS` | Schreibgeschützt. Zeigt die zulässigen Ursprünge für CORS an. |
| `WEBSITE_CORS_SUPPORT_CREDENTIALS` | Schreibgeschützt. Zeigt an, ob die Einstellung des `Access-Control-Allow-Credentials`-Headers auf `true` aktiviert (`True`) oder nicht aktiviert (`False`) ist. |

## <a name="authentication--authorization"></a>Authentifizierung und Autorisierung

Die folgenden Umgebungsvariablen beziehen sich auf die [App Service-Authentifizierung](overview-authentication-authorization.md).

| Einstellungsname| BESCHREIBUNG|
|-|-|
| `WEBSITE_AUTH_DISABLE_IDENTITY_FLOW`  | Wenn diese Einstellung auf `true` festgelegt ist, wird die Zuweisung der Threadprinzipalidentität in ASP.NET-basierten Webanwendungen (einschließlich v1-Funktions-Apps) deaktiviert. Dies dient dazu, dass Entwickler den Zugriff auf ihre Website mit Authentifizierung schützen können, aber dennoch einen separaten Anmeldemechanismus innerhalb ihrer App-Logik verwenden können. Der Standardwert lautet `false`. |
| `WEBSITE_AUTH_HIDE_DEPRECATED_SID` | `true` oder `false`. Der Standardwert ist `false`. Dies ist eine Einstellung für die Legacyintegration von Azure Mobile Apps für Azure App Service. Durch Festlegen dieser Einstellung auf `true` wird ein Problem behoben, bei dem sich die für authentifizierte Benutzer generierte SID (Sicherheits-ID) möglicherweise ändert, wenn der Benutzer seine Profilinformationen ändert. Das Ändern dieses Werts kann dazu führen, dass vorhandene Azure Mobile Apps-Benutzer-IDs geändert werden. Die meisten Apps müssen diese Einstellung nicht verwenden. |
| `WEBSITE_AUTH_NONCE_DURATION`| Ein _timespan_-Wert im Format `_hours_:_minutes_:_seconds_`. Der Standardwert ist `00:05:00` (5 Minuten). Diese Einstellung steuert die Lebensdauer der [kryptografischen Nonce](https://en.wikipedia.org/wiki/Cryptographic_nonce), die für alle browsergesteuerten Anmeldungen generiert wird. Wenn eine Anmeldung zum angegebenen Zeitpunkt nicht abgeschlossen werden kann, wird der Anmeldeflow automatisch wiederholt. Diese Anwendungseinstellung ist für die Verwendung mit der (klassischen) V1-Konfigurationsumgebung vorgesehen. Wenn Sie das Konfigurationsschema für die V2-Authentifizierung verwenden, sollten Sie stattdessen den Konfigurationswert `login.nonce.nonceExpirationInterval` verwenden. |
| `WEBSITE_AUTH_PRESERVE_URL_FRAGMENT` | Wenn diese Einstellung auf `true` festgelegt ist und Benutzer auf App-Links klicken, die URL-Fragmente enthalten, stellt der Anmeldevorgang sicher, dass der URL-Fragmentteil Ihrer URL beim Anmeldeumleitungsprozess nicht verloren geht. Weitere Informationen finden Sie unter [Anpassen der Anmeldung und Abmeldung in der Azure App Service-Authentifizierung](configure-authentication-customize-sign-in-out.md#preserve-url-fragments). |
| `WEBSITE_AUTH_USE_LEGACY_CLAIMS` | Damit die Abwärtskompatibilität zwischen Upgrades erhalten bleibt, verwendet das Authentifizierungsmodul die Legacy-Anspruchszuordnung von kurzen zu langen Namen in der `/.auth/me`-API, sodass bestimmte Zuordnungen ausgeschlossen werden (z. B. „roles“). Zum Abrufen der moderneren Version der Anspruchszuordnungen legen Sie diese Variable auf `False` fest. Im „roles“-Beispiel würde eine Zuordnung zum langen Anspruchsnamen „http://schemas.microsoft.com/ws/2008/06/identity/claims/role“ durchgeführt. |
| `WEBSITE_AUTH_DISABLE_WWWAUTHENTICATE` | `true` oder `false`. Der Standardwert ist `false`. Bei Festlegung auf `true` wird der [`WWW-Authenticate`](https://developer.mozilla.org/docs/Web/HTTP/Headers/WWW-Authenticate)-HTTP-Antwortheader aus modulgenerierten HTTP 401-Antworten entfernt. Diese Anwendungseinstellung ist für die Verwendung mit der (klassischen) V1-Konfigurationsumgebung vorgesehen. Wenn Sie das Konfigurationsschema für die V2-Authentifizierung verwenden, sollten Sie stattdessen den Konfigurationswert `identityProviders.azureActiveDirectory.login.disableWwwAuthenticate` verwenden. |
| `WEBSITE_AUTH_STATE_DIRECTORY`  | Ein lokaler Dateisystemverzeichnispfad, in dem Token gespeichert werden, wenn der dateibasierte Tokenspeicher aktiviert ist. Der Standardwert ist `%HOME%\Data\.auth`. Diese Anwendungseinstellung ist für die Verwendung mit der (klassischen) V1-Konfigurationsumgebung vorgesehen. Wenn Sie das Konfigurationsschema für die V2-Authentifizierung verwenden, sollten Sie stattdessen den Konfigurationswert `login.tokenStore.fileSystem.directory` verwenden. |
| `WEBSITE_AUTH_TOKEN_CONTAINER_SASURL` | Eine vollqualifizierte Blobcontainer-URL. Weist das Authentifizierungsmodul an, alle verschlüsselten Token in den angegebenen Blobspeichercontainer zu speichern und zu laden, anstatt das lokale Standarddateisystem zu verwenden.  |
| `WEBSITE_AUTH_TOKEN_REFRESH_HOURS` | Eine beliebige positive Dezimalzahl. Der Standardwert ist `72` (Stunden). Mit dieser Einstellung wird gesteuert, wie lange nach dem Ablauf eines Sitzungstokens die `/.auth/refresh`-API zu seiner Aktualisierung verwendet werden kann. Sie ist in erster Linie für die Verwendung mit Azure Mobile Apps vorgesehen, die von Sitzungstokens abhängen. Aktualisierungsversuche nach diesem Zeitraum schlagen fehl, und Endbenutzer müssen sich noch mal anmelden. Diese Anwendungseinstellung ist für die Verwendung mit der (klassischen) V1-Konfigurationsumgebung vorgesehen. Wenn Sie das Konfigurationsschema für die V2-Authentifizierung verwenden, sollten Sie stattdessen den Konfigurationswert `login.tokenStore.tokenRefreshExtensionHours` verwenden. |
| `WEBSITE_AUTH_TRACE_LEVEL`| Steuert die Ausführlichkeit von Authentifizierungsablaufverfolgungen, die in [Application Logging](troubleshoot-diagnostic-logs.md#enable-application-logging-windows) geschrieben werden. Gültige Werte sind `Off`, `Error`, `Warning`, `Information` und `Verbose`. Der Standardwert ist `Verbose`. |
| `WEBSITE_AUTH_VALIDATE_NONCE`| `true` oder `false`. Der Standardwert ist `true`. Dieser Wert sollte nie auf `false` festgelegt werden, außer wenn während interaktiver Anmeldungen vorübergehend Fehler bei der Überprüfung [kryptografischer Nonces](https://en.wikipedia.org/wiki/Cryptographic_nonce) debuggt werden. Diese Anwendungseinstellung ist für die Verwendung mit der (klassischen) V1-Konfigurationsumgebung vorgesehen. Wenn Sie das Konfigurationsschema für die V2-Authentifizierung verwenden, sollten Sie stattdessen den Konfigurationswert `login.nonce.validateNonce` verwenden. |
| `WEBSITE_AUTH_V2_CONFIG_JSON` | Diese Umgebungsvariable wird automatisch von der Azure App Service-Plattform aufgefüllt und zum Konfigurieren des integrierten Authentifizierungsmoduls verwendet. Der Wert dieser Umgebungsvariablen entspricht der V2-Authentifizierungskonfiguration (nicht klassisch) für die aktuelle App in Azure Resource Manager. Er ist nicht dazu gedacht, explizit konfiguriert zu werden. |
| `WEBSITE_AUTH_ENABLED` | Schreibgeschützt. Wird in eine Windows- oder Linux-App eingefügt, um anzugeben, ob App Service-Authentifizierung aktiviert ist. |
| `WEBSITE_AUTH_ENCRYPTION_KEY` | Standardmäßig wird der automatisch generierte Schlüssel als Verschlüsselungsschlüssel verwendet. Legen Sie ihn zur Außerkraftsetzung auf einen gewünschten Schlüssel fest. Dies wird empfohlen, wenn Sie Token oder Sitzungen für mehrere Apps freigeben möchten. Wenn angegeben, wird die `MACHINEKEY_DecryptionKey`-Einstellung dadurch ersetzt. |
| `WEBSITE_AUTH_SIGNING_KEY` | Standardmäßig wird der automatisch generierte Schlüssel als Signaturschlüssel verwendet. Legen Sie ihn zur Außerkraftsetzung auf einen gewünschten Schlüssel fest. Dies wird empfohlen, wenn Sie Token oder Sitzungen für mehrere Apps freigeben möchten. Wenn angegeben, wird die `MACHINEKEY_ValidationKey`-Einstellung dadurch ersetzt. |

<!-- System settings
WEBSITE_AUTH_RUNTIME_VERSION
WEBSITE_AUTH_API_PREFIX
WEBSITE_AUTH_TOKEN_STORE
WEBSITE_AUTH_MOBILE_COMPAT
WEBSITE_AUTH_AAD_BYPASS_SINGLE_TENANCY_CHECK
WEBSITE_AUTH_COOKIE_EXPIRATION_TIME
WEBSITE_AUTH_COOKIE_EXPIRATION_MODE
WEBSITE_AUTH_PROXY_HEADER_CONVENTION
WEBSITE_AUTH_PROXY_HOST_HEADER
WEBSITE_AUTH_PROXY_PROTO_HEADER
WEBSITE_AUTH_REQUIRE_HTTPS
WEBSITE_AUTH_DEFAULT_PROVIDER
WEBSITE_AUTH_UNAUTHENTICATED_ACTION
WEBSITE_AUTH_EXTERNAL_REDIRECT_URLS
WEBSITE_AUTH_CUSTOM_IDPS
WEBSITE_AUTH_CUSTOM_AUTHZ_SETTINGS
WEBSITE_AUTH_CLIENT_ID
WEBSITE_AUTH_CLIENT_SECRET
WEBSITE_AUTH_CLIENT_SECRET_SETTING_NAME
WEBSITE_AUTH_CLIENT_SECRET_CERTIFICATE_THUMBPRINT
WEBSITE_AUTH_OPENID_ISSUER
WEBSITE_AUTH_ALLOWED_AUDIENCES
WEBSITE_AUTH_LOGIN_PARAMS
WEBSITE_AUTH_AUTO_AAD
WEBSITE_AUTH_AAD_CLAIMS_AUTHORIZATION
WEBSITE_AUTH_GOOGLE_CLIENT_ID
WEBSITE_AUTH_GOOGLE_CLIENT_SECRET
WEBSITE_AUTH_GOOGLE_CLIENT_SECRET_SETTING_NAME
WEBSITE_AUTH_GOOGLE_SCOPE
WEBSITE_AUTH_FB_APP_ID
WEBSITE_AUTH_FB_APP_SECRET
WEBSITE_AUTH_FB_APP_SECRET_SETTING_NAME
WEBSITE_AUTH_FB_SCOPE
WEBSITE_AUTH_GITHUB_CLIENT_ID
WEBSITE_AUTH_GITHUB_CLIENT_SECRET
WEBSITE_AUTH_GITHUB_CLIENT_SECRET_SETTING_NAME
WEBSITE_AUTH_GITHUB_APP_SCOPE
WEBSITE_AUTH_TWITTER_CONSUMER_KEY
WEBSITE_AUTH_TWITTER_CONSUMER_SECRET
WEBSITE_AUTH_TWITTER_CONSUMER_SECRET_SETTING_NAME
WEBSITE_AUTH_MSA_CLIENT_ID
WEBSITE_AUTH_MSA_CLIENT_SECRET
WEBSITE_AUTH_MSA_CLIENT_SECRET_SETTING_NAME
WEBSITE_AUTH_MSA_SCOPE
WEBSITE_AUTH_FROM_FILE
WEBSITE_AUTH_FILE_PATH
| `WEBSITE_AUTH_CONFIG_DIR` | (Used for the deprecated "routes" feature) |
| `WEBSITE_AUTH_ZUMO_USE_TOKEN_STORE_CLAIMS` | (looks like only a tactical fix) ||
 -->

## <a name="managed-identity"></a>Verwaltete Identität

Die folgenden Umgebungsvariablen beziehen sich auf [verwaltete Identitäten](overview-managed-identity.md).

|Einstellungsname | BESCHREIBUNG |
|-|-|
|`IDENTITY_ENDPOINT` | Schreibgeschützt. Die URL zum Abrufen des Tokens für die [verwaltete Identität](overview-managed-identity.md) der App. |
| `MSI_ENDPOINT` | Veraltet. Verwenden Sie `IDENTITY_ENDPOINT`. |
| `IDENTITY_HEADER` | Schreibgeschützt. Wert, der dem Header `X-IDENTITY-HEADER` hinzugefügt werden muss, wenn eine HTTP GET-Anforderung an `IDENTITY_ENDPOINT` gestellt wird. Der Wert wird von der Plattform rotiert. |
| `MSI_SECRET` | Veraltet. Verwenden Sie `IDENTITY_HEADER`. |
<!-- | `WEBSITE_AUTHENTICATION_ENDPOINT_ENABLED` | Standardmäßig deaktiviert? TODO | -->

## <a name="health-check"></a>Integritätsprüfung

Die folgenden Umgebungsvariablen beziehen sich auf [Integritätsprüfungen](monitor-instances-health-check.md).

| Einstellungsname | BESCHREIBUNG |
|-|-|
| `WEBSITE_HEALTHCHECK_MAXPINGFAILURES` | Die maximale Anzahl fehlgeschlagener Pings, bevor die Instanz entfernt wird. Legen Sie einen Wert zwischen `2` und `100` fest. Beim Hoch- oder Aufskalieren pingt App Service außerdem den Pfad der Integritätsprüfung, um sicherzustellen, dass neue Instanzen bereit sind. Weitere Informationen finden Sie unter [Integritätsprüfung](monitor-instances-health-check.md).|
| `WEBSITE_HEALTHCHECK_MAXUNHEALTHYWORKERPERCENT` | Um zu vermeiden, dass fehlerfreie Instanzen überlastet werden, wird nicht mehr als die Hälfte der Instanzen ausgeschlossen. Wenn z. B. ein App Service-Plan auf vier Instanzen skaliert ist und drei fehlerhaft sind, werden höchstens zwei ausgeschlossen. Die anderen beiden Instanzen (eine fehlerfreie und eine fehlerhafte) empfangen weiterhin Anforderungen. Im ungünstigsten Fall, in dem alle Instanzen fehlerhaft sind, wird keine ausgeschlossen. Zur Außerkraftsetzung dieses Verhaltens legen Sie einen Wert zwischen `0` und `100` fest. Ein höherer Wert führt dazu, dass mehr fehlerhafte Instanzen entfernt werden. Der Standardwert ist `50` (50 %). |

## <a name="push-notifications"></a>Pushbenachrichtigungen

Die folgenden Umgebungsvariablen hängen mit dem Feature [Pushbenachrichtigungen](/previous-versions/azure/app-service-mobile/app-service-mobile-xamarin-forms-get-started-push#configure-hub) zusammen.

| Einstellungsname | BESCHREIBUNG |
|-|-|
| `WEBSITE_PUSH_ENABLED` | Schreibgeschützt. Wird hinzugefügt, wenn Pushbenachrichtigungen aktiviert sind. |
| `WEBSITE_PUSH_TAG_WHITELIST` | Schreibgeschützt. Enthält die Tags in der Benachrichtigungsregistrierung. |
| `WEBSITE_PUSH_TAGS_REQUIRING_AUTH` | Schreibgeschützt. Enthält eine Liste der Tags in der Benachrichtigungsregistrierung, die eine Benutzerauthentifizierung erfordert. |
| `WEBSITE_PUSH_TAGS_DYNAMIC` | Schreibgeschützt. Enthält eine Liste der Tags in der Benachrichtigungsregistrierung, die automatisch hinzugefügt wurden. | 

>[!NOTE]
> Dieser Artikel enthält Verweise auf den Begriff *Whitelist*, den Microsoft nicht länger verwendet. Sobald der Begriff aus der Software entfernt wird, wird er auch aus diesem Artikel entfernt.

<!-- 
## WellKnownAppSettings

WEBSITE_ALWAYS_PERFORM_PRELOAD
| WEBSITE_DISABLE_PRIMARY_VOLUMES | Set to `true` to disable the primary storage volume for that app. The default is `false`. |
| WEBSITE_DISABLE_STANDBY_VOLUMES | Set to `true` to disable the stand-by storage volume for that app. The default is `false`. This setting has no effect if `WEBSITE_DISABLE_PRIMARY_VOLUMES` is `true`. |
WEBSITE_FAILOVER_ONLY_ON_SBX_NW_FAILURES
WEBSITE_ENABLE_SYSTEM_LOG
WEBSITE_FRAMEWORK_JIT
WEBSITE_ADMIN_SITEID
WEBSITE_STAMP_DEPLOYMENT_ID
| WEBSITE_DEPLOYMENT_ID | Read-only. internal ID of deployment slot |
| WEBSITE_DISABLE_MSI | deprecated |
WEBSITE_VNET_BLOCK_FOR_SETUP_MAIN_SITE
WEBSITE_VNET_BLOCK_FOR_SETUP_SCM_SITE

 -->

## <a name="webjobs"></a>WebJobs

Die folgenden Umgebungsvariablen beziehen sich auf [WebJobs](webjobs-create.md).

| Einstellungsname| BESCHREIBUNG |
|-|-|
| `WEBJOBS_RESTART_TIME`|Bei fortlaufenden Aufträgen die Verzögerung in Sekunden, bevor der Prozess eines Auftrags, der aus irgendeinem Grund ausgefallen ist, neu gestartet wird. |
| `WEBJOBS_IDLE_TIMEOUT`| Bei ausgelösten Aufträgen das Timeout in Sekunden, nach dem der Auftrag abgebrochen wird, wenn er sich im Leerlauf befindet oder keine CPU-Zeit oder Ausgabe aufweist. |
| `WEBJOBS_HISTORY_SIZE`| Bei ausgelösten Aufträgen die maximale Anzahl von Ausführungen im Verlaufsverzeichnis pro Auftrag. Der Standardwert lautet `50`. |
| `WEBJOBS_STOPPED`| Legen Sie diese Einstellung auf `1` fest, um die Ausführung von Aufträgen zu deaktivieren und alle aktuell ausgeführten Aufträge zu beenden. |
| `WEBJOBS_DISABLE_SCHEDULE`| Legen Sie sie auf `1` fest, um alle geplanten Trigger zu deaktivieren. Aufträge können weiterhin manuell aufgerufen werden. |
| `WEBJOBS_ROOT_PATH`| Absoluter oder relativer Pfad von WebJob-Dateien. Bei einem relativen Pfad wird der Wert mit dem Standardstammpfad (`D:/home/site/wwwroot/` oder `/home/site/wwwroot/`) kombiniert. |
| `WEBJOBS_LOG_TRIGGERED_JOBS_TO_APP_LOGS`| Legen Sie diese Einstellung auf „true“ fest, um die Ausgabe von ausgelösten WebJobs an die Anwendungsprotokollpipeline zu senden (die Dateisystem, Blobs und Tabellen unterstützt). |
| `WEBJOBS_SHUTDOWN_FILE` | Datei, die App Service erstellt, wenn eine Anforderung zum Herunterfahren erkannt wird. Der WebJob-Prozess ist dafür verantwortlich, das Vorhandensein dieser Datei zu erkennen und das Herunterfahren zu initiieren. Bei Verwendung des WebJobs SDK wird dieser Teil automatisch verarbeitet. |
| `WEBJOBS_PATH` | Schreibgeschützt. Stammpfad des aktuell ausgeführten Auftrags (befindet sich in einem temporären Verzeichnis). |
| `WEBJOBS_NAME` | Schreibgeschützt. Name des aktuellen Auftrags. |
| `WEBJOBS_TYPE` | Schreibgeschützt. Typ des aktuellen Auftrags (`triggered` oder `continuous`). |
| `WEBJOBS_DATA_PATH` | Schreibgeschützt. Der aktuelle Auftragsmetadatenpfad, der die Protokolle, den Verlauf und jedes Artefakt des Auftrags enthalten soll. |
| `WEBJOBS_RUN_ID` | Schreibgeschützt. Bei ausgelösten Aufträgen die aktuelle Ausführungs-ID des Auftrags. |

## <a name="functions"></a>Funktionen

| Einstellungsname | BESCHREIBUNG |
|-|-|
| `WEBSITE_FUNCTIONS_ARMCACHE_ENABLED` | Legen Sie den Wert auf `0` fest, um den Funktionscache zu deaktivieren. |
| `WEBSITE_MAX_DYNAMIC_APPLICATION_SCALE_OUT` | [Referenz zu App-Einstellungen für Azure Functions](../azure-functions/functions-app-settings.md) |
| `FUNCTIONS_EXTENSION_VERSION` | [Referenz zu App-Einstellungen für Azure Functions](../azure-functions/functions-app-settings.md) |
`AzureWebJobsSecretStorageType` | [Referenz zu App-Einstellungen für Azure Functions](../azure-functions/functions-app-settings.md) |
| `FUNCTIONS_WORKER_RUNTIME` | [Referenz zu App-Einstellungen für Azure Functions](../azure-functions/functions-app-settings.md) |
| `AzureWebJobsStorage` | [Referenz zu App-Einstellungen für Azure Functions](../azure-functions/functions-app-settings.md) |
| `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` | [Referenz zu App-Einstellungen für Azure Functions](../azure-functions/functions-app-settings.md) |
| `WEBSITE_CONTENTSHARE` | [Referenz zu App-Einstellungen für Azure Functions](../azure-functions/functions-app-settings.md) |
| `WEBSITE_CONTENTOVERVNET` | [Referenz zu App-Einstellungen für Azure Functions](../azure-functions/functions-app-settings.md) |
| `WEBSITE_ENABLE_BROTLI_ENCODING` | [Referenz zu App-Einstellungen für Azure Functions](../azure-functions/functions-app-settings.md) |
| `WEBSITE_USE_PLACEHOLDER` | Legen Sie diese Einstellung auf `0` fest, um die Optimierung der Platzhalterfunktionen für den Verbrauchsplan zu deaktivieren. Der Platzhalter ist eine Optimierung, die [den Kaltstart verbessert](../azure-functions/functions-scale.md#cold-start-behavior). |
| `WEBSITE_PLACEHOLDER_MODE` | Schreibgeschützt. Zeigt an, ob die Funktions-App auf einem Platzhalterhost ( `generalized`) oder einem eigenen Host (`specialized` ) ausgeführt wird. |
| `WEBSITE_DISABLE_ZIP_CACHE` | Wenn Ihre App über ein [ZIP-Paket](deploy-run-package.md) (`WEBSITE_RUN_FROM_PACKAGE=1`) ausgeführt wird, werden die fünf zuletzt bereitgestellten ZIP-Pakete im Dateisystem der App (D:\home\data\SitePackages) zwischengespeichert. Legen Sie diese Variable auf `1` fest, um diesen Cache zu deaktivieren. Bei Linux-Verbrauch-Apps ist der ZIP-Paketcache standardmäßig deaktiviert. |
<!--
| `FUNCTIONS_RUNTIME_SCALE_MONITORING_ENABLED` | TODO |
| `WEBSITE_SKIP_FUNCTION_APP_WARMUP` | Apps can use appsetting to opt out of warmup. Restricted to linux only since this is primarily for static sites that use Linux dynamic function apps. Linux dynamic sites are used as placeholder sites for static sites. Function apps don't get specialized until static sites are deployed. This allows function apps used by static sites to skip warmup and using up containers before any content is deployed. TODO |
 WEBSITE_IDLE_TIMEOUT_IN_MINUTES | removed WEBSITE_IDLE_TIMEOUT_IN_MINUTES because they aren't used in Linux Consumption.???
| `WEBSITE_DISABLE_FUNCTIONS_STARTUPCONTEXT_CACHE`| This env var can be set to 1 by users in order to avoid using the Functions StartupContext Cache feature. |
| `WEBSITE_CONTAINER_READY` | The env var is set to '1' to indicate to the Functions Runtime that it can proceed with initializing/specializing 
        // itself. For placeholders, it is set once specialization is complete on DWAS side and detours are reinitialized. For 
        // non-placeholder function apps, it is simply set to 1 when the process is started, because detours are initialized 
        // as part of starting the process (when PicoHelper.dll is loaded, well before any managed code is running).
        // NOTE: This is set on all sites, irrespective of whether it is a Functions site, because the EnvSettings module depends 
        // upon it to decide when to inject the app-settings.|
| `WEBSITE_PLACEHOLDER_PING_PATH` | This env var can be used to set a special warmup ping path on placeholder template sites. |
| ` WEBSITE_PLACEHOLDER_DISABLE_AUTOSPECIALIZATION` | This env var can be used to disabe specialization from being enabled automatically for a given placeholder template site. |
| `WEBSITE_FUNCTIONS_STARTUPCONTEXT_CACHE` | This env var is set only during specialization of a placeholder, to indicate to the Functions Runtime that
        // some function-app related data needed at startup, like secrets, are available in a file at the path specified
        // by this env var. |
WEBSITE_ENABLE_COLD_START_PROFILING | This env var can be set to 1 by internal SLA sites in order to trigger collection of perf profiles, if feature is enabled on the stamp. |
WEBSITE_CURRENT_STAMPNAME | these environments contain the stamp name used for various scale decisions |
WEBSITE_HOME_STAMPNAME | these environments contain the stamp name used for various scale decisions |
WEBSITE_ELASTIC_SCALING_ENABLED
WEBSITE_FILECHANGEAUDIT_ENABLED
| `WEBSITE_HTTPSCALEV2_ENABLED` | This is the default behavior for both v1 and v2 Azure Functions apps. | 
WEBSITE_CHANGEANALYSISSCAN_ENABLED
WEBSITE_DISABLE_CHILD_SPECIALIZATION
 -->

<!-- 
## Server variables
|HTTP_HOST| |
|HTTP_DISGUISED_HOST|the runtime site name for inbound requests.|
HTTP_CACHE_CONTROL
HTTP_X_SITE_DEPLOYMENT_ID
HTTP_WAS_DEFAULT_HOSTNAME
HTTP_X_ORIGINAL_URL
HTTP_X_FORWARDED_FOR
HTTP_X_ARR_SSL
HTTP_X_FORWARDED_PROTO
HTTP_X_APPSERVICE_PROTO
HTTP_X_FORWARDED_TLSVERSION
X-WAWS-Unencoded-URL
CLIENT-IP
X-ARR-LOG-ID
DISGUISED-HOST
X-SITE-DEPLOYMENT-ID
WAS-DEFAULT-HOSTNAME
X-Original-URL
X-MS-CLIENT-PRINCIPAL-NAME
X-MS-CLIENT-DISPLAY-NAME
X-Forwarded-For
X-ARR-SSL
X-Forwarded-Proto
X-AppService-Proto
X-Forwarded-TlsVersion
URL
HTTP_CLIENT_IP
APP_WARMING_UP |Regular/external requests made while warmup is in progress will have a APP_WARMING_UP server variable set to 1|
HTTP_COOKIE
SERVER_NAME
HTTP_X_FORWARDED_HOST
| HTTP_X_AZURE_FDID | Azure Front Door ID. See [](../frontdoor/front-door-faq.md#how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door-) |
HTTP_X_FD_HEALTHPROBE
|WEBSITE_LOCALCACHE_ENABLED | shows up in w3wp.exe worker process |
HTTP_X_ARR_LOG_ID
| SCM_BASIC_AUTH_ALLOWED | set to "false" or "0" to disable basic authentication |
HTTP_X_MS_WAWS_JWT
HTTP_MWH_SecurityToken
LB_ALGO_FOR_HOSTING_SERVER_PROVIDER
ENABLE_CLIENT_AFFINITY
HTTP_X_MS_FROM_GEOMASTER
HTTP_X_MS_USE_GEOMASTER_CERT
HTTP_X_MS_STAMP_TOKEN
HTTPSCALE_REQUEST_ID
HTTPSCALE_FORWARD_FRONTEND_KEY
HTTPSCALE_FORWARD_REQUEST
IS_VALID_STAMP_TOKEN
NEEDS_SITE_RESTRICTED_TOKEN
HTTP_X_MS_PRIVATELINK_ID
  -->