---
title: Bereitstellen von Dateien in App Service
description: Hier erfahren Sie, wie Sie verschiedene App-Pakete oder diskrete Bibliotheken, statische Dateien oder Startskripts für Azure App Service bereitstellen.
ms.topic: article
ms.date: 08/13/2021
ms.reviewer: sisirap
ms.custom: seodec18, devx-track-azurepowershell
ms.openlocfilehash: a73d03d97e2b5d5d743b1db3980c8e587a0f454b
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/30/2021
ms.locfileid: "123225474"
---
# <a name="deploy-files-to-app-service"></a>Bereitstellen von Dateien in App Service

In diesem Artikel erfahren Sie, wie Sie Ihren Code als ZIP-, WAR-, JAR- oder EAR-Paket für [Azure App Service](overview.md) bereitstellen. Außerdem wird gezeigt, wie einzelne Dateien getrennt von Ihrem Anwendungspaket in App Service bereitgestellt werden.

## <a name="prerequisites"></a>Voraussetzungen

Um die Schritte in diesem Artikel durchzuführen, [erstellen Sie eine App Service-App](./index.yml), oder verwenden Sie eine App, die Sie für ein anderes Tutorial erstellt haben.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Create a project ZIP file](../../includes/app-service-web-deploy-zip-prepare.md)]

## <a name="deploy-a-zip-package"></a>Bereitstellen eines ZIP-Pakets

Wenn Sie ein ZIP-Paket bereitstellen, entpackt App Service seinen Inhalt im Standardpfad für Ihre App (`D:\home\site\wwwroot` für Windows, `/home/site/wwwroot` für Linux).

Bei dieser Bereitstellung per ZIP-Paket wird der gleiche Kudu-Dienst verwendet, der auch bei der Continuous Integration-basierten Bereitstellungen zum Einsatz kommt. Kudu unterstützt die folgenden Funktionen für die Bereitstellung per ZIP-Paket: 

- Löschen von Dateien aus einer vorherigen Bereitstellung
- Aktivieren des Standarderstellungsprozesses, der die Paketwiederherstellung umfasst
- Anpassen der Bereitstellung, einschließlich der Ausführung von Bereitstellungsskripts  
- Bereitstellungsprotokolle 
- Eine maximale Paketgröße von 2048 MB

Weitere Informationen finden Sie in der [Kudu-Dokumentation](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file).

> [!NOTE]
> Dateien im ZIP-Paket werden nur kopiert, wenn ihre Zeitstempel nicht mit dem übereinstimmen, was bereits bereitgestellt wurde. Das Erstellen einer ZIP-Datei mit einem Buildprozess, der Ausgaben zwischenspeichert, kann Bereitstellungen beschleunigen. Weitere Informationen finden Sie unter [Deploying from a zip file or url](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url) (Bereitstellen über eine ZIP-Datei oder URL).

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/cli)

Stellen Sie mithilfe des Befehls [az webapp deploy](/cli/azure/webapp#az_webapp_deploy) ein ZIP-Paket für Ihre Web-App bereit. Der CLI-Befehl verwendet die [Kudu-Veröffentlichungs-API](#kudu-publish-api-reference) zum Bereitstellen der Dateien und kann vollständig angepasst werden.

Im folgenden Beispiel wird ein ZIP-Paket an Ihre Website gepusht. Geben Sie den Pfad zu Ihrem lokalen ZIP-Paket für `--src-path` an.

```azurecli-interactive
az webapp deploy --resource-group <group-name> --name <app-name> --src-path <zip-package-path>
```

Mit diesem Befehl wird die App nach der Bereitstellung des ZIP-Pakets neu gestartet. 

[!INCLUDE [deploying to network secured sites](../../includes/app-service-deploy-network-secured-sites.md)]

Im folgenden Beispiel wird der `--src-url`-Parameter verwendet, um die URL eines Azure Storage Kontos anzugeben, aus dem die Website das ZIP-Paket pullen soll.

```azurecli-interactive
az webapp deploy --resource-group <grou-name> --name <app-name> --src-url "https://storagesample.blob.core.windows.net/sample-container/myapp.zip?sv=2021-10-01&sb&sig=slk22f3UrS823n4kSh8Skjpa7Naj4CG3
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Im folgenden Beispiel wird das ZIP-Paket mit [Publish-AzWebapp](/powershell/module/az.websites/publish-azwebapp) hochgeladen. Ersetzen Sie die Platzhalter `<group-name>`, `<app-name>` und `<zip-package-path>`.

```powershell
Publish-AzWebApp -ResourceGroupName Default-Web-WestUS -Name MyApp -ArchivePath <zip-package-path> 
```

# <a name="kudu-api"></a>[Kudu-API](#tab/api)

Im folgenden Beispiel wird das cURL-Tool verwendet, um eine ZIP-Datei bereitzustellen. Ersetzen Sie die Platzhalter `<username>`, `<zip-package-path>` und `<app-name>`. Wenn Sie von cURL dazu aufgefordert werden, geben Sie das [Bereitstellungskennwort](deploy-configure-credentials.md) ein.

```bash
curl -X POST -u <username> --data-binary @"<zip-package-path>" https://<app-name>.scm.azurewebsites.net/api/publish&type=zip
```

[!INCLUDE [deploying to network secured sites](../../includes/app-service-deploy-network-secured-sites.md)]

Im folgenden Beispiel wird der `packageUri`-Parameter verwendet, um die URL eines Azure Storage Kontos anzugeben, aus dem die Web-App das ZIP-Paket pullen soll.

```bash
curl -X POST -u <username> https://<app-name>.scm.azurewebsites.net/api/publish -d '{"packageUri": "https://storagesample.blob.core.windows.net/sample-container/myapp.zip?sv=2021-10-01&sb&sig=slk22f3UrS823n4kSh8Skjpa7Naj4CG3"}'
```

# <a name="kudu-ui"></a>[Kudu-Benutzeroberfläche](#tab/kudu-ui)

Navigieren Sie im Browser zu `https://<app_name>.scm.azurewebsites.net/ZipDeployUI`.

Laden Sie das unter [Erstellen eines ZIP-Pakets für das Projekt](#create-a-project-zip-package) erstellte ZIP-Paket hoch, indem Sie sie auf der Webseite in den Bereich für den Datei-Explorer ziehen.

Wenn die Bereitstellung gerade ausgeführt wird, zeigt ein Symbol in der oberen rechten Ecke den Status in Prozent an. Auf der Seite werden darüber hinaus unterhalb des Explorer-Bereichs ausführliche Meldungen für den Vorgang angezeigt. Ist der Vorgang abgeschlossen, muss die letzte Bereitstellungsmeldung `Deployment successful` lauten.

Der oben erwähnte Endpunkt funktioniert zurzeit nicht für App Services unter Linux. Verwenden Sie stattdessen FTP oder die [ZIP Deploy-API](/azure/app-service/faq-app-service-linux#continuous-integration-and-deployment).

-----

## <a name="enable-build-automation-for-zip-deploy"></a>Aktivieren der Buildautomatisierung für die ZIP-Bereitstellung

Standardmäßig geht die Bereitstellungs-Engine davon aus, dass ein ZIP-Paket ohne weitere Maßnahmen ausführungsfähig ist, und führt deshalb auch keine Buildautomatisierung aus. Um dieselbe Buildautomatisierung wie bei einer [Git-Bereitstellung](deploy-local-git.md) zu aktivieren, legen Sie die App-Einstellung `SCM_DO_BUILD_DURING_DEPLOYMENT` fest, indem Sie in den folgenden Befehl in [Cloud Shell](https://shell.azure.com) ausführen:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings SCM_DO_BUILD_DURING_DEPLOYMENT=true
```

Weitere Informationen finden Sie in der [Kudu-Dokumentation](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url).

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="deploy-warjarear-packages"></a>Bereitstellen von WAR-/JAR-/EAR-Paketen

Sie können Ihr [WAR](https://wikipedia.org/wiki/WAR_(file_format))-, [JAR](https://wikipedia.org/wiki/JAR_(file_format))- oder [EAR](https://wikipedia.org/wiki/EAR_(file_format))-Paket auf App Service bereitstellen, um Ihre Java-Web-App mithilfe der Azure CLI, PowerShell oder der Kudu-Veröffentlichungs-API auszuführen.

Beim Bereitstellungsprozess wird das Paket ordnungsgemäß auf dem freigegebenen Dateilaufwerk platziert (siehe [Referenz zur Kudu-Veröffentlichungs-API](#kudu-publish-api-reference)). Aus diesem Grund wird die Bereitstellung von WAR-/JAR-/EAR-Paketen über [FTP](deploy-ftp.md) oder WebDeploy nicht empfohlen.

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/cli)

Stellen Sie mithilfe des Befehls [az webapp deploy](/cli/azure/webapp#az_webapp_deploy) ein WAR-Paket für Tomcat oder JBoss EAP bereit. Geben Sie den Pfad zu Ihrem lokalen Java-Paket für `--src-path` an.

```azurecli-interactive
az webapp deploy --resource-group <group-name> --name <app-name> --src-path ./<package-name>.war
```

[!INCLUDE [deploying to network secured sites](../../includes/app-service-deploy-network-secured-sites.md)]

Im folgenden Beispiel wird der `--src-url`-Parameter verwendet, um die URL eines Azure Storage Kontos anzugeben, aus dem die Web-App das ZIP-Paket pullen soll.

```azurecli-interactive
az webapp deploy --resource-group <grou-name> --name <app-name> --src-url "https://storagesample.blob.core.windows.net/sample-container/myapp.war?sv=2021-10-01&sb&sig=slk22f3UrS823n4kSh8Skjpa7Naj4CG3
```

Der CLI-Befehl verwendet die [Kudu-Veröffentlichungs-API](#kudu-publish-api-reference) zum Bereitstellen des Pakets und kann vollständig angepasst werden.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Im folgenden Beispiel wird die .war-Datei mit [Publish-AzWebapp](/powershell/module/az.websites/publish-azwebapp) hochgeladen. Ersetzen Sie die Platzhalter `<group-name>`, `<app-name>` und `<package-path>` (nur WAR- und JAR-Dateien werden in Azure PowerShell unterstützt).

```powershell
Publish-AzWebapp -ResourceGroupName <group-name> -Name <app-name> -ArchivePath <package-path>
```

# <a name="kudu-api"></a>[Kudu-API](#tab/api)

Im folgenden Beispiel wird das cURL-Tool verwendet, um eine .war-, .jar-, oder .ear-Datei bereitzustellen. Ersetzen Sie die Platzhalter `<username>`, `<file-path>`, `<app-name>` und `<package-type>` (jeweils `war`, `jar` oder `ear`). Wenn Sie von cURL dazu aufgefordert werden, geben Sie das [Bereitstellungskennwort](deploy-configure-credentials.md) ein.

```bash
curl -X POST -u <username> --data-binary @"<file-path>" https://<app-name>.scm.azurewebsites.net/api/publish&type=<package-type>
```

[!INCLUDE [deploying to network secured sites](../../includes/app-service-deploy-network-secured-sites.md)]

Im folgenden Beispiel wird der `packageUri`-Parameter verwendet, um die URL eines Azure Storage Kontos anzugeben, aus dem die Web-App das WAR-Paket pullen soll. Die WAR-Datei kann auch eine JAR- oder EAR-Datei sein.

```bash
curl -X POST -u <username> https://<app-name>.scm.azurewebsites.net/api/publish -d '{"packageUri": "https://storagesample.blob.core.windows.net/sample-container/myapp.war?sv=2021-10-01&sb&sig=slk22f3UrS823n4kSh8Skjpa7Naj4CG3"}'
```

Weitere Informationen finden Sie in der [Referenz zur Kudu-Veröffentlichungs-API](#kudu-publish-api-reference).

# <a name="kudu-ui"></a>[Kudu-Benutzeroberfläche](#tab/kudu-ui)

Die Kudu-Benutzeroberfläche unterstützt keine Bereitstellung von JAR-, WAR- oder EAR-Anwendungen. Wählen Sie eine der anderen Optionen aus.

-----

## <a name="deploy-individual-files"></a>Bereitstellen einzelner Dateien

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/cli)

Stellen Sie ein Startskript, eine Bibliothek und eine statische Datei in Ihrer Web-App bereit, indem Sie den Befehl [az webapp deploy](/cli/azure/webapp#az_webapp_deploy) mit dem `--type`-Parameter verwenden.

Wenn Sie ein Startskript auf diese Weise bereitstellen, verwendet App Service automatisch Ihr Skript, um Ihre App zu starten.

Der CLI-Befehl verwendet die [Kudu-Veröffentlichungs-API](#kudu-publish-api-reference) zum Bereitstellen der Dateien und kann vollständig angepasst werden.

### <a name="deploy-a-startup-script"></a>Bereitstellen eines Startskripts

```bash
az webapp deploy --resource group <group-name> --name <app-name> --src-path scripts/startup.sh --type=startup
```

### <a name="deploy-a-library-file"></a>Bereitstellen einer Bibliotheksdatei

```bash
az webapp deploy --resource group <group-name> --name <app-name> --src-path driver.jar --type=lib
```

### <a name="deploy-a-static-file"></a>Bereitstellen einer statischen Datei

```bash
az webapp deploy --resource group <group-name> --name <app-name> --src-path config.json --type=static
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Wird nicht unterstützt. Weitere Informationen finden Sie unter Azure CLI- oder Kudu-API.

# <a name="kudu-api"></a>[Kudu-API](#tab/api)

### <a name="deploy-a-startup-script"></a>Bereitstellen eines Startskripts

Im folgenden Beispiel wird das cURL-Tool verwendet, um eine Startdatei für ihre Anwendung bereitzustellen. Ersetzen Sie die Platzhalter `<username>`, `<startup-file-path>` und `<app-name>`. Wenn Sie von cURL dazu aufgefordert werden, geben Sie das [Bereitstellungskennwort](deploy-configure-credentials.md) ein.

```bash
curl -X POST -u <username> --data-binary @"<startup-file-path>" https://<app-name>.scm.azurewebsites.net/api/publish&type=startup
```

### <a name="deploy-a-library-file"></a>Bereitstellen einer Bibliotheksdatei

Im folgenden Beispiel wird das cURL-Tool verwendet, um eine Bibliotheksdatei für ihre Anwendung bereitzustellen. Ersetzen Sie die Platzhalter `<username>`, `<lib-file-path>` und `<app-name>`. Wenn Sie von cURL dazu aufgefordert werden, geben Sie das [Bereitstellungskennwort](deploy-configure-credentials.md) ein.

```bash
curl -X POST -u <username> --data-binary @"<lib-file-path>" https://<app-name>.scm.azurewebsites.net/api/publish&type=lib&path="/home/site/deployments/tools/my-lib.jar"
```

### <a name="deploy-a-static-file"></a>Bereitstellen einer statischen Datei

Im folgenden Beispiel wird das cURL-Tool verwendet, um eine Konfigurationsdatei für ihre Anwendung bereitzustellen. Ersetzen Sie die Platzhalter `<username>`, `<config-file-path>` und `<app-name>`. Wenn Sie von cURL dazu aufgefordert werden, geben Sie das [Bereitstellungskennwort](deploy-configure-credentials.md) ein.

```bash
curl -X POST -u <username> --data-binary @"<config-file-path>" https://<app-name>.scm.azurewebsites.net/api/publish&type=static&path="/home/site/deployments/tools/my-config.json"
```

# <a name="kudu-ui"></a>[Kudu-Benutzeroberfläche](#tab/kudu-ui)

Die Kudu-Benutzeroberfläche unterstützt keine Bereitstellung einzelner Dateien. Verwenden Sie die Azure CLI- oder Kudu-REST-API.

-----

## <a name="kudu-publish-api-reference"></a>Referenz zur Kudu-Veröffentlichungs-API

Mit der `publish` Kudu-API können Sie die gleichen Parameter aus dem CLI-Befehl wie URL-Abfrageparameter angeben. Für die Authentifizierung mit der Kudu-API können Sie die Standardauthentifizierung mit den [Anmeldeinformationen für die Bereitstellung](deploy-configure-credentials.md#userscope) Ihrer App verwenden.

Die folgende Tabelle zeigt die verfügbaren Abfrageparameter, ihre zulässigen Werte und Beschreibungen.

| Key | Zulässige Werte | BESCHREIBUNG | Erforderlich | type  |
|-|-|-|-|-|
| `type` | `war`\|`jar`\|`ear`\|`lib`\|`startup`\|`static`\|`zip` | Der Typ des bereitgestellten Artefakts. Dadurch wird der Standardzielpfad festgelegt, und die Web-App wird darüber informiert, wie die Bereitstellung behandelt werden soll. <br/> - `type=zip`: Bereitstellen eines ZIP-Pakets durch Entzippen des Inhalts in `/home/site/wwwroot` Der Parameter `path` ist optional. <br/> - `type=war`: Bereitstellen eines WAR-Pakets Standardmäßig wird das WAR-Paket in `/home/site/wwwroot/app.war` bereitgestellt. Der Zielpfad kann mit `path` angegeben werden. <br/> - `type=jar`: Bereitstellen eines JAR-Pakets in `/home/site/wwwroot/app.jar` Der `path`-Parameter wird ignoriert. <br/> - `type=ear`: Bereitstellen eines EAR-Pakets in `/home/site/wwwroot/app.ear` Der `path`-Parameter wird ignoriert. <br/> - `type=lib`: Bereitstellen einer JAR-Bibliotheksdatei Standardmäßig wird die Datei in `/home/site/libs` bereitgestellt. Der Zielpfad kann mit `path` angegeben werden. <br/> - `type=static`: Bereitstellen einer statischen Datei (z. B. eines Skripts) Standardmäßig wird die Datei in `/home/site/scripts` bereitgestellt. Der Zielpfad kann mit `path` angegeben werden. <br/> - `type=startup`: Stellen Sie ein Skript bereit, das App Service automatisch als Startskript für Ihre App verwendet. Standardmäßig wird das Skript in `D:\home\site\scripts\<name-of-source>` für Windows und `home/site/wwwroot/startup.sh` für Linux bereitgestellt. Der Zielpfad kann mit `path` angegeben werden. | Ja | String |
| `restart` | `true`\|`false` | Standardmäßig startet die API die App nach dem Bereitstellungsvorgang (`restart=true`) neu. Um mehrere Artefakte bereitzustellen, verhindern Sie Neustarts für alle bis auf die endgültige Bereitstellung, indem Sie `restart=false` festlegen. | Nein | Boolean |
| `clean` | `true`\|`false` | Gibt an, ob die Zielbereitstellung bereinigt (gelöscht) werden soll, bevor das Artefakt dort bereitgestellt wird | Nein | Boolean |
| `ignorestack` | `true`\|`false` | Die Veröffentlichungs-API verwendet die Umgebungsvariable `WEBSITE_STACK`, um je nach Sprachstapel Ihrer Website sichere Standardwerte zu wählen. Wenn Sie diesen Parameter auf `false` festlegen, werden alle sprachspezifischen Standardwerte deaktiviert. | Nein | Boolean |
| `path` | `"<absolute-path>"` | Der absolute Pfad, in dem das Artefakt bereitgestellt werden soll Platzhalter in einer derartigen Schreibweise sind z.B. `"/home/site/deployments/tools/driver.jar"` und `"/home/site/scripts/helper.sh"`. | Nein | String |

## <a name="next-steps"></a>Nächste Schritte

Für komplexere Bereitstellungsszenarien versuchen Sie die [Bereitstellung in Azure mit Git](deploy-local-git.md). Die Git-basierte Bereitstellung in Azure ermöglicht Versionskontrolle, Paketwiederherstellung, MSBuild und mehr.

## <a name="more-resources"></a>Weitere Ressourcen

* [Kudu: Bereitstellen aus einer ZIP-Datei](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)
* [Anmeldeinformationen für die Azure App Service-Bereitstellung](deploy-ftp.md)
* [Referenz zu Umgebungsvariablen und App-Einstellungen](reference-app-settings.md)
