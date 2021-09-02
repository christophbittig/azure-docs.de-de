---
title: 'Tutorial: Vorbereiten einer Web-App für Azure Communication Services (Node.js)'
titleSuffix: An Azure Communication Services tutorial
description: Es wird beschrieben, wie Sie eine Baseline-Webanwendung erstellen, die Azure Communication Services unterstützt.
author: nmurav
services: azure-communication-services
ms.author: nmurav
ms.date: 06/30/2021
ms.topic: tutorial
ms.service: azure-communication-services
ms.openlocfilehash: 62c72271e1c72b524de1ff2365a78ec240007bfb
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/31/2021
ms.locfileid: "123250971"
---
# <a name="tutorial-prepare-a-web-app-for-azure-communication-services-nodejs"></a>Tutorial: Vorbereiten einer Web-App für Azure Communication Services (Node.js)

Sie können Azure Communication Services verwenden, um Ihren Anwendungen die Echtzeitkommunikation hinzuzufügen. In diesem Tutorial wird beschrieben, wie Sie eine Webanwendung einrichten, die Azure Communication Services unterstützt. Dies ist ein Einführungstutorial für neue Entwickler, die in die Echtzeitkommunikation einsteigen möchten.

Am Ende dieses Tutorials verfügen Sie über eine grundlegende Webanwendung, für die Azure Communication Services SDKs konfiguriert sind. Anschließend können Sie diese Anwendung nutzen, um mit der Entwicklung Ihrer Lösungen für die Echtzeitkommunikation zu beginnen.

Wir freuen uns auf Ihr Feedback, das Sie uns über die [GitHub-Seite für Azure Communication Services](https://github.com/Azure/communication) zukommen lassen können.

In diesem Tutorial lernen Sie Folgendes:
> [!div class="checklist"]
> * Konfigurieren der Entwicklungsumgebung
> * Einrichten eines lokalen Webservers
> * Hinzufügen der Azure Communication Services-Pakete zu Ihrer Website
> * Veröffentlichen Ihrer Website auf statischen Azure-Websites

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Details finden Sie unter [kostenloses Azure-Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). Beim kostenlosen Konto erhalten Sie eine Azure-Gutschrift in Höhe von 200 US-Dollar, damit Sie Kombinationen verschiedener Dienste ausprobieren können.
- [Visual Studio Code](https://code.visualstudio.com/) für die Bearbeitung von Code in Ihrer lokalen Entwicklungsumgebung.
- [Webpack](https://webpack.js.org/) zum Bündeln und lokalen Hosten Ihres Codes.
- [Node.js](https://nodejs.org/en/) zum Installieren und Verwalten von Abhängigkeiten, z. B. Azure Communication Services SDKs und Webpack
- [nvm und npm](/windows/nodejs/setup-on-windows) zum Verarbeiten der Versionskontrolle.
- [Azure Storage-Erweiterung](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage) für Visual Studio Code. Sie benötigen diese Erweiterung, um Ihre Anwendung in Azure Storage zu veröffentlichen. Informieren Sie sich über das [Hosten von statischen Websites in Azure Storage](../../storage/blobs/storage-blob-static-website.md).
- Die [Azure App Service-Erweiterung](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice). Die Erweiterung ermöglicht die Bereitstellung von Websites, verfügt aber zusätzlich über die Option zum Konfigurieren von Continuous Integration und Continuous Delivery (CI/CD) mit vollständiger Verwaltung.
- Die [Azure Functions-Erweiterung](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) zum Entwickeln Ihrer eigenen serverlosen Anwendungen. Beispielsweise können Sie Ihre Authentifizierungsanwendung in Azure Functions hosten.
- Eine aktive Communication Services-Ressource und eine Verbindungszeichenfolge. Informieren Sie sich über das [Erstellen einer Communication Services-Ressource](../quickstarts/create-communication-resource.md).
- Ein Benutzerzugriffstoken. Eine Anleitung finden Sie in der [Schnellstartanleitung zum Erstellen und Verwalten von Zugriffstoken](../quickstarts/access-tokens.md?pivots=programming-language-javascript) oder im [Tutorial zur Erstellung eines vertrauenswürdigen Authentifizierungsdiensts](./trusted-service-tutorial.md).


## <a name="configure-your-development-environment"></a>Konfigurieren der Entwicklungsumgebung

Ihre lokale Entwicklungsumgebung wird wie folgt konfiguriert:

:::image type="content" source="./media/step-one-pic-one.png" alt-text="Diagramm: Architektur der Entwicklungsumgebung":::


### <a name="install-nodejs-nvm-and-npm"></a>Installieren von Node.js, nvm und npm

Wir verwenden Node.js, um verschiedene Abhängigkeiten herunterzuladen und zu installieren, die wir für unsere clientseitige Anwendung benötigen. Hiermit werden statische Dateien generiert, die von uns dann in Azure gehostet werden, sodass Sie sich nicht um die entsprechende Konfiguration auf Ihrem Server kümmern müssen.

Windows-Entwickler können [dieses Node.js-Tutorial](/windows/nodejs/setup-on-windows) durcharbeiten, um Node, nvm und npm zu konfigurieren. 

Dieses Tutorial basiert auf der LTS-Version 12.20.0. Verwenden Sie nach der Installation von nvm den folgenden PowerShell-Befehl, um die Version bereitzustellen, die Sie nutzen möchten:

```PowerShell
nvm list available
nvm install 12.20.0
nvm use 12.20.0
```

:::image type="content" source="./media/step-one-pic-two.png" alt-text="Screenshot: Befehle zum Bereitstellen einer Node-Version":::

### <a name="configure-visual-studio-code"></a>Konfigurieren von Visual Studio Code

Sie können [Visual Studio Code](https://code.visualstudio.com/) über eine der [unterstützten Plattformen](https://code.visualstudio.com/docs/supporting/requirements#_platforms) herunterladen.

### <a name="create-a-workspace-for-your-azure-communication-services-projects"></a>Erstellen eines Arbeitsbereichs für Ihre Azure Communication Services-Projekte

Erstellen Sie einen Ordner zum Speichern Ihrer Projektdateien, z. B. `C:\Users\Documents\ACS\CallingApp`. Wählen Sie in Visual Studio Code die Option **Datei** > **Ordner zum Arbeitsbereich hinzufügen** aus, und fügen Sie den Ordner Ihrem Arbeitsbereich hinzu.

:::image type="content" source="./media/step-one-pic-three.png" alt-text="Screenshot: Auswahl zum Hinzufügen einer Datei zu einem Arbeitsbereich":::

Im **EXPLORER** wird Ihr Ordner `CallingApp` im linken Bereich im Arbeitsbereich **UNBENANNT** angezeigt.

:::image type="content" source="./media/step-one-pic-four.png" alt-text="Screenshot: Arbeitsbereich „Unbenannt“ im Explorer":::

Es ist für Sie jederzeit möglich, den Namen Ihres Arbeitsbereichs zu ändern. Sie können Ihre Node.js-Version überprüfen, indem Sie mit der rechten Maustaste auf Ihren Ordner `CallingApp` klicken und die Option **In integriertem Terminal öffnen** auswählen.

:::image type="content" source="./media/step-one-pic-five.png" alt-text="Screenshot: Auswahl zum Öffnen eines Ordners in einem integrierten Terminal":::

Geben Sie im Terminal den folgenden Befehl ein, um die im vorherigen Schritt installierte Node.js-Version zu überprüfen:

```JavaScript
node --version
```

:::image type="content" source="./media/step-one-pic-six.png" alt-text="Screenshot: Überprüfen der Node.js-Version":::

### <a name="install-azure-extensions-for-visual-studio-code"></a>Installieren von Azure-Erweiterungen für Visual Studio Code

Installieren Sie die [Azure Storage-Erweiterung](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage) entweder über den Visual Studio Marketplace oder Visual Studio Code (**Ansicht** > **Erweiterungen** > **Azure Storage**).

:::image type="content" source="./media/step-one-pic-seven.png" alt-text="Screenshot: Schaltfläche zum Installieren der Azure Storage-Erweiterung":::

Führen Sie die gleichen Schritte für [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)- und [Azure App Service](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice)-Erweiterungen aus.


## <a name="set-up-a-local-web-server"></a>Einrichten eines lokalen Webservers

### <a name="create-an-npm-package"></a>Erstellen eines npm-Pakets

Geben Sie im Terminal unter dem Pfad Ihres Arbeitsbereichsordners Folgendes ein:

``` console
npm init -y
```

Mit diesem Befehl wird ein neues npm-Paket initialisiert und `package.json` dem Stammordner Ihres Projekts hinzugefügt.

:::image type="content" source="./media/step-one-pic-eight.png" alt-text="Screenshot: JSON-Code des Pakets":::

Weitere Informationen zu `npm init` finden Sie in der [npm-Dokumentation auf der Seite für diesen Befehl](https://docs.npmjs.com/cli/v6/commands/npm-init).

### <a name="install-webpack"></a>Installieren von Webpack

Sie können [Webpack](https://webpack.js.org/) verwenden, um den Code in statischen Paketdateien zusammenzufassen, die Sie in Azure bereitstellen können. Darüber hinaus ist hierbei auch ein Entwicklungsserver vorhanden, den Sie für das Anrufbeispiel konfigurieren.

Geben Sie im Terminal den folgenden Befehl ein, um Webpack zu installieren:

``` Console
npm install webpack@4.42.0 webpack-cli@3.3.11 webpack-dev-server@3.10.3 --save-dev
```

Dieses Tutorial wurde mit den Versionen getestet, die im vorherigen Befehl angegeben wurden. Die Angabe von `-dev` ist ein Hinweis für den Paket-Manager, dass diese Abhängigkeit für Entwicklungszwecke bestimmt ist und nicht Teil des Codes sein sollte, den Sie in Azure bereitstellen.

Sie sehen, dass Ihrer Datei `package.json` zwei neue Pakete als `devDependencies` hinzugefügt werden. Die Pakete werden im Verzeichnis `./CallingApp/node_modules/` installiert.

:::image type="content" source="./media/step-one-pic-ten.png" alt-text="Screenshot: Webpack-Konfiguration":::

### <a name="configure-the-development-server"></a>Konfigurieren des Entwicklungsservers

Beim Ausführen einer statischen Anwendung (z. B. Ihrer Datei `index.html`) über Ihren Browser wird das `file://`-Protokoll verwendet. Damit Ihre npm-Module richtig funktionieren, benötigen Sie das HTTP-Protokoll. Hierfür nutzen Sie Webpack als lokalen Entwicklungsserver.

Sie erstellen zwei Konfigurationen: eine für die Entwicklung und die andere für die Produktion. Für die Produktion vorbereitete Dateien werden verkleinert. Dies bedeutet, dass Sie nicht verwendete Leerzeichen bzw. andere Zeichen entfernen. Diese Konfiguration eignet sich für Produktionsszenarien, in denen die Latenz minimiert oder Code verschleiert werden soll.

Sie verwenden das Tool `webpack-merge`, um [unterschiedliche Konfigurationsdateien für Webpack](https://webpack.js.org/guides/production/) zu nutzen.

Wir beginnen nun mit der Entwicklungsumgebung. Zunächst müssen Sie `webpack merge` installieren. Führen Sie in Ihrem Terminal den folgenden Befehl aus:

```Console
npm install --save-dev webpack-merge
```

In Ihrer Datei `package.json` können Sie sehen, dass `devDependencies` eine oder mehrere Abhängigkeiten hinzugefügt wurden.

Erstellen Sie als Nächstes eine Datei mit dem Namen `webpack.common.js`, und fügen Sie den folgenden Code hinzu:

```JavaScript
const path = require('path');
module.exports ={
    entry: './app.js',
    output: {
        filename:'app.js',
        path: path.resolve(__dirname, 'dist'),
    }
}
```

Anschließend fügen Sie zwei weitere Dateien hinzu (eine für jede Konfiguration):

* `webpack.dev.js`
* `webpack.prod.js`

Ändern Sie nun die Datei `webpack.dev.js`, indem Sie ihr den folgenden Code hinzufügen:

```JavaScript
const { merge } = require('webpack-merge');
const common = require('./webpack.common.js');

module.exports = merge(common, {
    mode: 'development',
    devtool: 'inline-source-map',
});
```
Bei dieser Konfiguration importieren Sie häufig verwendete Parameter aus `webpack.common.js`, führen die beiden Dateien zusammen, legen den Modus `development` fest und konfigurieren die Quellzuordnungsdatei als `inline-source-map`.

Im Entwicklungsmodus wird Webpack angewiesen, die Dateien nicht zu verkleinern und keine optimierten Produktionsdateien zu erstellen. Eine ausführliche Dokumentation zu den Webpack-Modi finden Sie auf der [Webseite zum Webpack-Modus](https://webpack.js.org/configuration/mode/).

Die Optionen für Quellzuordnungsdateien sind auf der [devtool-Webseite von Webpack](https://webpack.js.org/configuration/devtool/#root) aufgeführt. Das Festlegen der Quellzuordnung erleichtert Ihnen das Debuggen über Ihren Browser.

:::image type="content" source="./media/step-one-pic-11.png" alt-text="Screenshot: Code zum Konfigurieren von Webpack":::

Navigieren Sie zum Ausführen des Entwicklungsservers zu `package.json`, und fügen Sie unter `scripts` den folgenden Code hinzu:

```JavaScript
    "build:dev": "webpack-dev-server --config webpack.dev.js"
```

Ihre Datei sollte nun wie folgt aussehen:

```JavaScript
{
  "name": "CallingApp",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "build:dev": "webpack-dev-server --config webpack.dev.js"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "devDependencies": {
    "webpack": "^4.42.0",
    "webpack-cli": "^3.3.11",
    "webpack-dev-server": "^3.10.3"
  }
}
```

Sie haben den Befehl hinzugefügt, der über npm verwendet werden kann.

:::image type="content" source="./media/step-one-pic-12.png" alt-text="Screenshot: Änderung der Datei „package.json“":::

### <a name="test-the-development-server"></a>Testen des Entwicklungsservers

 Erstellen Sie in Visual Studio Code in Ihrem Projekt drei Dateien:

* `index.html`
* `app.js`
* `app.css` (optional, zum Formatieren Ihrer App)

Fügen Sie diesen Code in `index.html` ein:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>My first ACS application</title>
    <link rel="stylesheet" href="./app.css"/>
    <script src="./app.js" defer></script>
</head>
<body>
    <h1>Hello from ACS!</h1>
</body>
</html>
```
:::image type="content" source="./media/step-one-pic-13.png" alt-text="Screenshot: HTML-Datei":::

Fügen Sie den folgenden Code zu `app.js` hinzu:

```JavaScript
alert('Hello world alert!');
console.log('Hello world console!');
```
Fügen Sie den folgenden Code zu `app.css` hinzu:

```CSS
html {
    font-family: sans-serif;
  }
```
Vergessen Sie das Speichern nicht! Die nicht gespeicherte Datei ist im Explorer durch weiße Punkte neben den Dateinamen gekennzeichnet.

:::image type="content" source="./media/step-one-pic-14.png" alt-text="Screenshot: Datei „App.js“ mit JavaScript-Code":::

Wenn Sie diese Seite öffnen, sollte Ihre Meldung mit einer Warnung in der Konsole Ihres Browsers angezeigt werden.

:::image type="content" source="./media/step-one-pic-15.png" alt-text="Screenshot: Datei „App.css“":::

Verwenden Sie den folgenden Terminalbefehl, um Ihre Entwicklungskonfiguration zu testen:

```Console
npm run build:dev
```

In der Konsole wird angezeigt, wo der Server ausgeführt wird. Dieser lautet standardmäßig `http://localhost:8080`. Der Befehl `package.json` ist der Befehl, den Sie weiter oben der Datei `build:dev` hinzugefügt haben.

:::image type="content" source="./media/step-one-pic-16.png" alt-text="Screenshot: Starten eines Entwicklungsservers":::
 
Navigieren Sie in Ihrem Browser zu der Adresse. Es sollte die Seite mit der Warnung angezeigt werden, die Sie in den obigen Schritten konfiguriert haben.
 
:::image type="content" source="./media/step-one-pic-17.png" alt-text="Screenshot: HTML-Seite":::
  
 
Während der Server ausgeführt wird, können Sie den Code und den Server ändern. Die HTML-Seite wird automatisch neu geladen. 

Navigieren Sie als Nächstes zur Datei `app.js` in Visual Studio Code, und löschen Sie `alert('Hello world alert!');`. Speichern Sie Ihre Datei, und vergewissern Sie sich, dass die Warnung nicht mehr in Ihrem Browser angezeigt wird.

Zum Beenden Ihres Servers können Sie in Ihrem Terminal `Ctrl+C` verwenden. Sie können jederzeit `npm run build:dev` eingeben, um Ihren Server zu starten.

## <a name="add-the-azure-communication-services-packages"></a>Hinzufügen der Azure Communication Services-Pakete

Verwenden Sie den Befehl `npm install`, um das Azure Communication Services Calling SDK für JavaScript zu installieren.

```Console
npm install @azure/communication-common --save
npm install @azure/communication-calling --save
```

Mit dieser Aktion werden die allgemeinen Pakete und Aufrufpakete von Azure Communication Services als Abhängigkeiten Ihres Pakets hinzugefügt. Sie sehen, dass der Datei `package.json` zwei neue Pakete hinzugefügt werden. Weitere Informationen zu `npm install` finden Sie in der [npm-Dokumentation auf der Seite für diesen Befehl](https://docs.npmjs.com/cli/v6/commands/npm-install).

:::image type="content" source="./media/step-one-pic-nine.png" alt-text="Screenshot: Code zum Installieren von Azure Communication Services-Paketen":::

Diese Pakete werden vom Azure Communication Services-Team bereitgestellt und enthalten die Authentifizierungs- und Anrufbibliotheken. Mit dem Befehl `--save` wird signalisiert, dass die Anwendung für die Nutzung in der Produktion von diesen Paketen abhängig ist und dass sie in `devDependencies` in der Datei `package.json` enthalten sind. Wenn Sie die Anwendung für die Produktion erstellen, werden die Pakete in Ihren Produktionscode eingefügt.


## <a name="publish-your-website-to-azure-static-websites"></a>Veröffentlichen Ihrer Website auf statischen Azure-Websites

### <a name="create-a-configuration-for-production-deployment"></a>Erstellen einer Konfiguration für die Produktionsbereitstellung

Fügen Sie den folgenden Code zu `webpack.prod.js` hinzu:

```JavaScript
const { merge } = require('webpack-merge');
const common = require('./webpack.common.js');

module.exports = merge(common, {
  mode: 'production',
});
```

Diese Konfiguration wird mit der Datei `webpack.common.js` zusammengeführt (in der Sie die Eingabedatei und den Ort zum Speichern der Ergebnisse angegeben haben). Darüber hinaus wird der Modus bei der Konfiguration auf `production` festgelegt.
 
Fügen Sie in `package.json` den folgenden Code hinzu:

```JavaScript
"build:prod": "webpack --config webpack.prod.js"
```

Ihre Datei sollte wie folgt aussehen:

```JavaScript
{
  "name": "CallingApp",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "build:dev": "webpack-dev-server --config webpack.dev.js",
    "build:prod": "webpack --config webpack.prod.js"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "dependencies": {
    "@azure/communication-calling": "^1.0.0-beta.6",
    "@azure/communication-common": "^1.0.0"
  },
  "devDependencies": {
    "webpack": "^4.42.0",
    "webpack-cli": "^3.3.11",
    "webpack-dev-server": "^3.10.3",
    "webpack-merge": "^5.7.3"
  }
}
```

:::image type="content" source="./media/step-one-pic-20.png" alt-text="Screenshot: Konfigurierte Dateien":::


Führen Sie im Terminal Folgendes aus:

```Console
npm run build:prod
```

Mit dem Befehl wird der Ordner `dist` und darin dann die für die Produktion geeignete statische Datei `app.js` erstellt. 

:::image type="content" source="./media/step-one-pic-21.png" alt-text="Screenshot: Build für die Produktion":::
 
 
### <a name="deploy-your-app-to-azure-storage"></a>Bereitstellen Ihrer App in Azure Storage

Kopieren Sie `index.html` und `app.css` in den Ordner `dist`.

Erstellen Sie im Ordner `dist` eine Datei, und geben Sie ihr den Namen `404.html`. Kopieren Sie den folgenden Markupcode in diese Datei:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <link rel="stylesheet" href="./app.css"/>
    <title>Document</title>
</head>
<body>
    <h1>The page does not exists.</h1>
</body>
</html>
```

Speichern Sie die Datei (STRG+S).

Klicken Sie mit der rechten Maustaste auf den Ordner `dist`, und wählen Sie die Option **Deploy to Static Website via Azure Storage** (Über Azure Storage auf statischer Website bereitstellen) aus.

:::image type="content" source="./media/step-one-pic-22.png" alt-text="Screenshot: Auswahl zum Starten der Bereitstellung in Azure":::
 
Wählen Sie unter **Abonnement auswählen** die Option **Bei Azure anmelden** aus (oder **Kostenloses Azure-Konto erstellen**, falls Sie zuvor noch kein Abonnement erstellt haben).
 
:::image type="content" source="./media/step-one-pic-23.png" alt-text="Screenshot: Auswahl für die Anmeldung bei Azure":::
 
Wählen Sie **Neues Speicherkonto erstellen** > **Erweitert** aus.

:::image type="content" source="./media/step-one-pic-24.png" alt-text="Screenshot: Auswahl zum Erstellen der Speicherkontogruppe":::
 
Geben Sie den Namen der Speichergruppe an.
 
:::image type="content" source="./media/step-one-pic-25.png" alt-text="Screenshot: Hinzufügen eines Namens für das Konto":::
 
Erstellen Sie bei Bedarf eine neue Ressourcengruppe.
 
:::image type="content" source="./media/step-one-pic-26.png" alt-text="Screenshot: Auswahl zum Erstellen einer neuen Ressourcengruppe":::
  
Wählen Sie für **Would you like to enable static website hosting?** (Möchten Sie das Hosten von statischen Websites aktivieren?) die Option **Ja** aus.

:::image type="content" source="./media/step-one-pic-27.png" alt-text="Screenshot: Auswählen der Option zum Aktivieren des Hostens von statischen Websites":::
  
Übernehmen Sie für **Enter the index document name** (Namen des Indexdokuments eingeben) den Standarddateinamen. Sie haben die Datei `index.html` bereits erstellt.

Geben Sie unter **Enter the 404 error document path** (Pfad für 404-Fehlerdokument eingeben) den Pfad **404.html** ein.  
  
Wählen Sie den Standort der Anwendung aus. Anhand des von Ihnen ausgewählten Speicherorts wird definiert, welcher Medienprozessor in Ihrer zukünftigen Anrufanwendung bei Gruppenanrufen verwendet wird. 

Der Medienprozessor wird von Azure Communication Services basierend auf dem Standort der Anwendung ausgewählt.

:::image type="content" source="./media/step-one-pic-28.png" alt-text="Screenshot: Liste mit Standorten":::
  
Warten Sie ab, bis die Ressource und Ihre Website erstellt wurden. 
 
Wählen Sie die Option **Browse to website** (Zur Website wechseln) aus.

:::image type="content" source="./media/step-one-pic-29.png" alt-text="Screenshot: Meldung mit dem Hinweis zum Abschluss der Bereitstellung und Schaltfläche zum Durchsuchen einer Website":::
 
In den Entwicklungstools Ihres Browsers können Sie die Quelle untersuchen und die für die Produktion vorbereitete Datei anzeigen.
 
:::image type="content" source="./media/step-one-pic-30.png" alt-text="Screenshot: Websitequelle mit Datei":::

Wählen Sie im [Azure-Portal](https://portal.azure.com/#home) Ihre Ressourcengruppe und dann die von Ihnen erstellte Anwendung aus. Wählen Sie anschließend **Einstellungen** > **Statische Website** aus. Sie können sehen, dass statische Websites aktiviert sind. Achten Sie auf den primären Endpunkt, den Namen des Indexdokuments und den Pfad des Fehlerdokuments.

:::image type="content" source="./media/step-one-pic-31.png" alt-text="Screenshot: Auswahl der statischen Website":::

Wählen Sie **Container** unter **Blob-Dienst** aus. In der Liste werden zwei erstellte Container angezeigt: einer für Protokolle (`$logs`) und einer für den Inhalt Ihrer Website (`$web`).

:::image type="content" source="./media/step-one-pic-32.png" alt-text="Screenshot: Containerkonfiguration":::

Wenn Sie den Container `$web` öffnen, werden die Dateien angezeigt, die Sie in Visual Studio erstellt und in Azure bereitgestellt haben. 

:::image type="content" source="./media/step-one-pic-33.png" alt-text="Screenshot: In Azure bereitgestellte Dateien":::

Sie können die Anwendung über Visual Studio Code jederzeit erneut bereitstellen.

Sie können nun Ihre erste Azure Communication Services-Webanwendung erstellen.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Hinzufügen von Sprachanrufen zu Ihrer App](../quickstarts/voice-video-calling/getting-started-with-calling.md)

Weitere Möglichkeiten:

- [Hinzufügen von Chatfunktionen zu Ihrer App](../quickstarts/chat/get-started.md)
- [Erstellen von Benutzerzugriffstoken](../quickstarts/access-tokens.md)
- [Erfahren Sie mehr über die Client- und Serverarchitektur](../concepts/client-and-server-architecture.md)
- [Informationen zur Authentifizierung](../concepts/authentication.md)
