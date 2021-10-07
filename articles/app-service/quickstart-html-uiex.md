---
title: 'Schnellstart: Erstellen einer statischen HTML-Web-App'
description: Stellen Sie in wenigen Minuten Ihre erste HTML-App vom Typ „Hallo Welt“ in Azure App Service bereit. Die Bereitstellung erfolgt mithilfe von Git. Dies ist eine von zahlreichen verschiedenen Möglichkeiten für die Bereitstellung in App Service.
author: msangapu-msft
ms.assetid: 60495cc5-6963-4bf0-8174-52786d226c26
ms.topic: quickstart
ms.date: 08/23/2019
ms.author: msangapu
ms.custom: mvc, cli-validate, seodec18
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 6c342a898a7ad9f0918313b2292a0105c75f6c8a
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/05/2021
ms.locfileid: "129535556"
---
# <a name="create-a-static-html-web-app-in-azure"></a>Erstellen einer statischen HTML-Web-App in Azure

In diesem Schnellstart wird erläutert, wie Sie eine einfache Website mit HTML und CSS im folgenden Dienst bereitstellen: <abbr title="Ein HTTP-basierter Dienst zum Hosten von Webanwendungen, REST-APIs und mobilen Back-End-Anwendungen">Azure App Service</abbr>. In dieser Schnellstartanleitung wird [Cloud Shell](../cloud-shell/overview.md) verwendet. Die Befehle können aber auch lokal über die [Azure CLI](/cli/azure/install-azure-cli) ausgeführt werden.

## <a name="1-prepare-your-environment"></a>1. Vorbereiten der Umgebung

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Erstellen Sie in [Cloud Shell](../cloud-shell/overview.md) ein Schnellstartverzeichnis, und wechseln Sie dorthin.

```bash
mkdir quickstart

cd $HOME/quickstart
```

Führen Sie als Nächstes den folgenden Befehl aus, um das Beispiel-App-Repository in Ihrem Schnellstartverzeichnis zu klonen.

```bash
git clone https://github.com/Azure-Samples/html-docs-hello-world.git
```
<hr/>

## <a name="2-create-a-web-app"></a>2. Erstellen einer Web-App

Wechseln Sie zum Verzeichnis mit dem Beispielcode, und führen Sie den Befehl `az webapp up` aus. **Ersetzen** Sie `<app-name>` durch einen global eindeutigen Namen.

```bash
cd html-docs-hello-world

az webapp up --location westeurope --name <app_name> --html
```

<details>
<summary>Problembehandlung</summary>
<ul>
<li>Wenn der Befehl <code>az</code> nicht erkannt wird, stellen Sie sicher, dass Sie die Azure CLI gemäß der Beschreibung im Abschnitt <a href="#1-prepare-your-environment">Vorbereiten der Umgebung</a> installiert haben.</li>
<li>Ersetzen Sie <code>&lt;app-name&gt;</code> durch einen Namen, der innerhalb von Azure eindeutig ist (<em>gültige Zeichen: <code>a-z</code>, <code>0-9</code> und <code>-</code></em>). Ein bewährtes Muster ist eine Kombination aus Ihrem Firmennamen und einer App-ID.</li>
<li>Mit dem Argument <code>--sku F1</code> wird die Web-App im Tarif „Free“ erstellt. Lassen Sie dieses Argument weg, um einen schnelleren Premium-Tarif zu verwenden. Dieser verursacht jedoch stündlich Kosten.</li>
<li>Laut <code>--html</code>-Argument werden alle Inhalte als statische Inhalte behandelt, und die Buildautomatisierung wird deaktiviert.</li>
<li>Optional können Sie das Argument <code>--location &lt;location-name&gt;</code> einfügen, wobei <code>&lt;location-name&gt;</code> eine verfügbare Azure-Region ist. Sie können eine Liste der zulässigen Regionen für Ihr Azure-Konto abrufen, indem Sie den Befehl <a href="/cli/azure/appservice#az_appservice_list_locations"><code>az account list-locations</code></a> ausführen.</li>
</ul>
</details>

Die Ausführung dieses Befehls kann einige Minuten in Anspruch nehmen. 

<details>
<summary>Was bewirkt der Befehl <code>az webapp up</code>?</summary>
<p>Der Befehl <code>az webapp up</code> bewirkt Folgendes:</p>
<ul>
<li>Erstellen einer Standardressourcengruppe</li>
<li>Er erstellt einen App Service-Standardplan.</li>
<li>Er <a href="/cli/azure/webapp#az_webapp_create">erstellt eine App Service-App</a> mit dem angegebenen Namen.</li>
<li><a href="/azure/app-service/deploy-zip">Bereitstellen von ZIP-Dateien</a> aus dem aktuellen Arbeitsverzeichnis für die App</li>
<li>Während der Ausführung werden Meldungen zur Ressourcenerstellung, Protokollierung und ZIP-Bereitstellung angezeigt.</li>
</ul>

Nach Abschluss der Ausführung werden Informationen angezeigt, die den Informationen im folgenden Beispiel ähneln:

```output
{
  "app_url": "https://&lt;app_name&gt;.azurewebsites.net",
  "location": "westeurope",
  "name": "&lt;app_name&gt;",
  "os": "Windows",
  "resourcegroup": "appsvc_rg_Windows_westeurope",
  "serverfarm": "appsvc_asp_Windows_westeurope",
  "sku": "FREE",
  "src_path": "/home/&lt;username&gt;/quickstart/html-docs-hello-world ",
  &lt; JSON data removed for brevity. &gt;
}
```

</details>

Sie benötigen den `resourceGroup`-Wert, um später [Ressourcen zu bereinigen](#6-clean-up-resources).

<hr/>

## <a name="3-browse-to-the-app"></a>3. Navigieren zur App

Wechseln Sie in einem Browser zur URL der App: `http://<app_name>.azurewebsites.net`.

Die Seite wird als Azure App Service-Web-App ausgeführt.

![Startseite der Beispiel-App](media/quickstart-html/hello-world-in-browser-az.png)

<hr/>

## <a name="4-update-and-redeploy-the-app"></a>4. Aktualisieren und erneutes Bereitstellen der App

**Geben Sie in Cloud Shell**  ein`nano index.html`, um den Nano-Text-Editor zu öffnen. 

Ändern Sie im `<h1>`-Tag der Überschrift „Azure App Service – Sample Static HTML Site“ in „Azure App Service“.

![Nano index.html](media/quickstart-html/nano-index-html.png)

**Speichern** Sie Ihre Änderungen mithilfe des `^O`-Befehls.

**Schließen** Sie den Nano-Editor mithilfe des `^X`-Befehls.

Stellen Sie die App mithilfe des `az webapp up`-Befehls erneut bereit.

```bash
az webapp up --html
```

Wechseln Sie wieder zu dem Browserfenster, das im Schritt **Navigieren zur App** geöffnet wurde.

**Aktualisieren** Sie die Seite.

![Aktualisierte Startseite der Beispiel-App](media/quickstart-html/hello-azure-in-browser-az.png)

<hr/>

## <a name="5-manage-your-new-azure-app"></a>5. Verwalten Ihrer neuen Azure-App

**Navigieren** Sie zum [Azure-Portal](https://portal.azure.com). 

**Suchen** Sie nach **App Services**, und **wählen Sie die Option aus**.

![Auswählen von „App Services“ im Azure-Portal](./media/quickstart-html/portal0.png)

**Wählen** Sie den Namen Ihrer Azure-App aus.

![Portalnavigation zur Azure-App](./media/quickstart-html/portal1.png)

Die Übersichtsseite Ihrer Web-App wird angezeigt. Hier können Sie einfache Verwaltungsaufgaben wie Durchsuchen, Beenden, Neustarten und Löschen durchführen.

![App Service-Blatt im Azure-Portal](./media/quickstart-html/portal2.png)

Im linken Menü werden verschiedene Seiten für die Konfiguration Ihrer App angezeigt.

<hr/>

## <a name="6-clean-up-resources"></a>6. Bereinigen von Ressourcen

In den vorherigen Schritten haben Sie Azure-Ressourcen in einer Ressourcengruppe erstellt. Wenn Sie diese Ressourcen in Zukunft nicht mehr benötigen, löschen Sie die Ressourcengruppe, indem Sie den folgenden Befehl in Cloud Shell ausführen. Denken Sie daran, dass der Name der Ressourcengruppe im Schritt [Erstellen einer Web-App](#2-create-a-web-app) automatisch für Sie generiert wurde.

```bash
az group delete --name appsvc_rg_Windows_westeurope
```

Die Ausführung dieses Befehls kann eine Minute in Anspruch nehmen.

<hr/>

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> Zuordnen einer benutzerdefinierte Domäne
