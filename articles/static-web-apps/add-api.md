---
title: Hinzufügen einer API zu Azure Static Web Apps mit Azure Functions
description: Steigen Sie in Azure Static Web Apps ein, indem Sie Ihrer statischen Web-App mit Azure Functions eine serverlose API hinzufügen.
services: static-web-apps
author: manekinekko
ms.service: static-web-apps
ms.topic: how-to
ms.date: 05/14/2021
ms.author: wachegha
ms.custom: devx-track-js
ms.openlocfilehash: be664a5bf29c95143e7706b2df2b49648959f584
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131013667"
---
# <a name="add-an-api-to-azure-static-web-apps-with-azure-functions"></a>Hinzufügen einer API zu Azure Static Web Apps mit Azure Functions

Sie können Azure Static Web Apps, die von Azure Functions unterstützt werden, serverlose APIs hinzufügen. In diesem Artikel wird veranschaulicht, wie Sie eine API für eine Azure Static Web Apps-Website hinzufügen und bereitstellen.

> [!NOTE]
> Die Funktionen, die standardmäßig in Static Web Apps bereitgestellt werden, sind so vorkonfiguriert, dass sie sichere API-Endpunkte bereitstellen und nur HTTP-ausgelöste Funktionen unterstützen. Informationen zu den Unterschieden gegenüber eigenständigen Azure Functions-Apps finden Sie unter [API-Unterstützung mit Azure Functions](apis.md).

## <a name="prerequisites"></a>Voraussetzungen

- Azure-Konto mit einem aktiven Abonnement.
  - Falls Sie kein Konto besitzen, können Sie [kostenlos eines erstellen](https://azure.microsoft.com/free).
- [Visual Studio Code](https://code.visualstudio.com/)
- [Azure Static Web Apps-Erweiterung](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestaticwebapps) für Visual Studio Code
- [Node.js](https://nodejs.org/download/) zum Ausführen der Front-End-App und API

## <a name="create-the-static-web-app"></a>Erstellen der statischen Web-App

Erstellen Sie vor dem Hinzufügen einer API eine Front-End-Anwendung, und stellen Sie sie in Azure Static Web Apps bereit. Verwenden Sie eine vorhandene App, die Sie bereits bereitgestellt haben, oder erstellen Sie eine, indem Sie den Schnellstart [Erstellen Ihrer ersten statischen Site mit Azure Static Web Apps](getting-started.md) befolgen.

Öffnen Sie in Visual Studio Code den Stamm des Repositorys Ihrer App. Die Ordnerstruktur enthält die Quelle für Ihre Front-End-App und den Static Web Apps GitHub-Workflow im Ordner _.github/workflows_.

```Files
├── .github
│   └── workflows
│       └── azure-static-web-apps-<DEFAULT_HOSTNAME>.yml
│
└── (folders and files from your static web app)
```

## <a name="create-the-api"></a>Erstellen der API

Sie erstellen ein Azure Functions-Projekt für die API Ihrer statischen Web-App. Standardmäßig erstellt die Static Web Apps Visual Studio Code-Erweiterung das Projekt in einem Ordner namens _api_ im Stammverzeichnis Ihres Repositorys.

1. Drücken Sie <kbd>F1</kbd>, um die Befehlspalette zu öffnen.

1. Wählen Sie **Azure Static Web Apps: HTTP-Funktion erstellen...** aus. Wenn Sie aufgefordert werden, die Azure Functions-Erweiterung zu installieren, installieren Sie sie, und führen Sie diesen Befehl erneut aus.

1. Geben Sie die folgenden Werte ein, wenn Sie dazu aufgefordert werden:

    | Prompt | Wert |
    | --- | --- |
    | Sprache auswählen | **JavaScript** |
    | Angeben eines Funktionsnamens | **Nachricht** |

    Ein Azure Functions-Projekt wird mit einer HTTP-ausgelösten Funktion generiert. Ihre App besitzt jetzt eine Projektstruktur, die dem folgenden Beispiel ähnelt.

    ```Files
    ├── .github
    │   └── workflows
    │       └── azure-static-web-apps-<DEFAULT_HOSTNAME>.yml
    │
    ├── api
    │   ├── message
    │   │   ├── function.json
    │   │   └── index.js
    │   ├── host.json
    │   ├── local.settings.json
    │   └── package.json
    │
    └── (folders and files from your static web app)
    ```

1. Als Nächstes ändern Sie die `message`-Funktion, um eine Nachricht an das Front-End zurückzugeben. Aktualisieren Sie die Funktion unter _api/message/index.js_ mit dem folgenden Code.

    ```javascript
    module.exports = async function (context, req) {
        context.res.json({
            text: "Hello from the API"
        });
    };
    ```

> [!TIP]
> Sie können weitere API-Funktionen hinzufügen, indem Sie den Befehl **Azure Static Web Apps: HTTP-Funktion erstellen...** erneut ausführen.

## <a name="update-the-frontend-app-to-call-the-api"></a>Aktualisieren der Front-End-App zum Aufrufen der API

Aktualisieren Sie Ihre Front-End-App, um die API unter `/api/message` aufzurufen und die Antwortnachricht anzuzeigen.

Wenn Sie die Schnellstartanleitungen zum Erstellen der App verwendet haben, befolgen Sie die folgenden Anweisungen, um die Updates anzuwenden.

# <a name="no-framework"></a>[Kein Framework](#tab/vanilla-javascript)

Aktualisieren Sie den Inhalt der Datei _src/index.html_ mit dem folgenden Code, um den Text aus der API-Funktion abzurufen und auf dem Bildschirm anzuzeigen.

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <link rel="stylesheet" href="styles.css">
    <title>Vanilla JavaScript App</title>
</head>

<body>
    <main>
    <h1>Vanilla JavaScript App</h1>
    <p>Loading content from the API: <b id="name">...</b></p>
    </main>

    <script>
    (async function() {
        const { text } = await( await fetch(`/api/message`)).json();
        document.querySelector('#name').textContent = text;
    }())
    </script>
</body>

</html>
```

# <a name="angular"></a>[Angular](#tab/angular)

1. Aktualisieren Sie den Inhalt von _src/app/app.module.ts_ mit dem folgenden Code, um `HttpClient` in Ihrer App zu aktivieren.

    ```typescript
    import { BrowserModule } from "@angular/platform-browser";
    import { NgModule } from "@angular/core";
    import { HttpClientModule } from '@angular/common/http';
    
    import { AppComponent } from "./app.component";
    
    @NgModule({
      declarations: [AppComponent],
      imports: [BrowserModule, HttpClientModule],
      bootstrap: [AppComponent]
    })
    export class AppModule {}
    ```

1. Aktualisieren Sie den Inhalt der Datei _src/app/app.component.ts_ mit dem folgenden Code, um den Text aus der API-Funktion abzurufen und auf dem Bildschirm anzuzeigen.

    ```typescript
    import { HttpClient } from '@angular/common/http';
    import { Component } from '@angular/core';
    
    @Component({
      selector: 'app-root',
      template: `<div>{{message}}</div>`,
    })
    export class AppComponent {
      message = '';
    
      constructor(private http: HttpClient) {
        this.http.get('/api/message')
          .subscribe((resp: any) => this.message = resp.text);
      }
    }
    ```

# <a name="react"></a>[React](#tab/react)

Aktualisieren Sie den Inhalt der Datei _src/App.js_ mit dem folgenden Code, um den Text aus der API-Funktion abzurufen und auf dem Bildschirm anzuzeigen.

```javascript
import React, { useState, useEffect } from 'react';

function App() {
  const [data, setData] = useState('');

  useEffect(() => {
    (async function () {
      const { text } = await( await fetch(`/api/message`)).json();
      setData(text);
    })();
  });

  return <div>{data}</div>;
}

export default App;
```

# <a name="vue"></a>[Vue](#tab/vue)

Aktualisieren Sie den Inhalt der Datei _src/App.vue_ mit dem folgenden Code, um den Text aus der API-Funktion abzurufen und auf dem Bildschirm anzuzeigen.

```javascript
<template>
  <div>{{ message }}</div>
</template>

<script>
export default {
  name: "App",
  data() {
    return {
      message: ""
    };
  },
  async mounted() {
    const { text } = await (await fetch("/api/message")).json();
    this.message = text;
  }
};
</script>
```

---

## <a name="run-the-frontend-and-api-locally"></a>Lokales Ausführen von Front-End und API

Um Ihre Front-End-App und API zusammen lokal auszuführen, bietet Azure Static Web Apps eine CLI, die die Cloudumgebung emuliert. Die CLI nutzt die Azure Functions Core Tools zum Ausführen der API.

### <a name="install-command-line-tools"></a>Installieren von Befehlszeilentools

Stellen Sie sicher, dass die erforderlichen Befehlszeilentools installiert sind.

1. Installieren Sie die Azure Static Web Apps-CLI.
    ```bash
    npm install -g @azure/static-web-apps-cli
    ```

1. Installieren Sie die Azure Functions Core Tools V3.
    ```bash
    npm install -g azure-functions-core-tools@3
    ```

### <a name="build-frontend-app"></a>Erstellen der Front-End-App

Wenn Ihre App ein Framework verwendet, erstellen Sie die App, um die Ausgabe zu generieren, bevor Sie die Static Web Apps-CLI ausführen.

# <a name="no-framework"></a>[Kein Framework](#tab/vanilla-javascript)

Es ist nicht erforderlich, die App zu erstellen.

# <a name="angular"></a>[Angular](#tab/angular)

Erstellen Sie die App im Ordner _dist/angular-basic_.

```bash
npm run build --prod
```

# <a name="react"></a>[React](#tab/react)

Erstellen Sie die App im Ordner _build_.

```bash
npm run build
```

# <a name="vue"></a>[Vue](#tab/vue)

Erstellen Sie die App im Ordner _dist_.

```bash
npm run build
```

---

### <a name="start-the-cli"></a>Starten der CLI

Führen Sie die Front-End-App und API zusammen aus, indem Sie die App mit der Static Web Apps-CLI starten. Wenn Sie die beiden Teile Ihrer Anwendung auf diese Weise ausführen, kann die CLI die Buildausgabe Ihres Front-End aus einem Ordner bereitstellen und die API für die ausgeführte App zugänglich machen.

1. Starten Sie im Stammverzeichnis Ihres Repositorys die Static Web Apps-CLI mit dem Befehl `start`. Passen Sie die Argumente an, wenn Ihre App eine andere Ordnerstruktur aufweist.

    # <a name="no-framework"></a>[Kein Framework](#tab/vanilla-javascript)

    Übergeben Sie den aktuellen Ordner (`src`) und den API-Ordner (`api`) an die CLI.
     
    ```bash
    swa start src --api-location api
    ```

    # <a name="angular"></a>[Angular](#tab/angular)

    Übergeben Sie den Buildausgabeordner (`dist/angular-basic`) und den API-Ordner (`api`) an die CLI.

    ```bash
    swa start dist/angular-basic --api-location api
    ```

    # <a name="react"></a>[React](#tab/react)

    Übergeben Sie den Buildausgabeordner (`build`) und den API-Ordner (`api`) an die CLI.

    ```bash
    swa start build --api-location api
    ```

    # <a name="vue"></a>[Vue](#tab/vue)

    Übergeben Sie den Buildausgabeordner (`dist`) und den API-Ordner (`api`) an die CLI.

    ```bash
    swa start dist --api-location api
    ```

    ---

1. Wenn die CLI-Prozesse gestartet werden, greifen Sie unter `http://localhost:4280/` auf Ihre App zu. Beachten Sie, wie die Seite die API aufruft und ihre Ausgabe `Hello from the API` anzeigt.

1. Um die CLI zu beenden, drücken Sie <kbd>STRG+C</kbd>.

## <a name="add-api-location-to-workflow"></a>Hinzufügen eines API-Speicherorts zum Workflow

Bevor Sie Ihre App in Azure bereitstellen können, aktualisieren Sie den GitHub Actions-Workflow Ihres Repositorys mit dem richtigen Speicherort Ihres API-Ordners.

1. Öffnen Sie Ihren Workflow unter _.github/workflows/azure-static-web-apps-\<DEFAULT-HOSTNAME>.yml_.

1. Suchen Sie nach der Eigenschaft `api_location`, und legen Sie den Wert auf `api` fest.
1. Speichern Sie die Datei .

## <a name="deploy-changes"></a>Bereitstellen von Änderungen

Um Änderungen an Ihrer statischen Web-App in Azure zu veröffentlichen, committen Sie Ihren Code, und pushen Sie ihn in das GitHub-Remoterepository.

1. Drücken Sie <kbd>F1</kbd>, um die Befehlspalette zu öffnen.

1. Wählen Sie den Befehl **Git: Commit All** aus.

1. Wenn Sie zur Eingabe einer Commitnachricht aufgefordert werden, geben Sie **add API** (API hinzufügen) ein, und committen Sie alle Änderungen in Ihr lokales Git-Repository.

1. Drücken Sie <kbd>F1</kbd>, um die Befehlspalette zu öffnen.

1. Wählen Sie den Befehl **Git: push** aus.

    Ihre Änderungen werden in das Remoterepository in GitHub gepusht, was den Static Web Apps GitHub Actions-Workflow zum Erstellen und Bereitstellen Ihrer App auslöst.

1. Öffnen Sie Ihr Repository in GitHub, um den Status Ihrer Workflowausführung zu überwachen.

1. Wenn die Workflowausführung abgeschlossen ist, besuchen Sie Ihre statische Web-App, um Ihre Änderungen anzuzeigen.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Konfigurieren von App-Einstellungen](./application-settings.md)
