---
title: Build-Konfiguration für Azure Static Web Apps
description: Erfahren Sie, wie Sie den Build-Prozess für Azure Static Web Apps steuern können.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 08/23/2021
ms.author: cshoe
ms.custom: contperf-fy21q4
ms.openlocfilehash: 54b6231c98ce244913062010782591493ea45a5e
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124838252"
---
# <a name="build-configuration-for-azure-static-web-apps"></a>Build-Konfiguration für Azure Static Web Apps

Die Azure Static Web Apps Build-Konfiguration wird entweder von Azure Pipelines oder GitHub Actions unterstützt.  Jede Site hat eine YAML-Konfigurationsdatei, die steuert, wie eine Site erstellt und bereitgestellt wird. In diesem Artikel werden Struktur und Optionen der Datei erläutert.

In der folgenden Tabelle sind die verfügbaren Konfigurationseinstellungen aufgeführt.

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|---|---|---|
| `app_location` | In diesem Ordner befindet sich der Quellcode für Ihre Front-End-Anwendung. Der Wert ist relativ zum Stamm des Repositorys in GitHub und dem aktuellen Arbeitsordner in Azure DevOps. | Ja |
| `api_location` | Dieser Ordner enthält den Quellcode für Ihre API-Anwendung. Der Wert ist relativ zum Stamm des Repositorys in GitHub und dem aktuellen Arbeitsordner in Azure DevOps. | Nein |
| `output_location` | Wenn Ihre Webanwendung einen Build-Schritt ausführt, ist der Ausgabeort der Ordner, in dem die öffentlichen Dateien generiert werden. Bei den meisten Projekten ist der `output_location` relativ zum `app_location`. Bei .NET-Projekten ist der Speicherort jedoch relativ zum Ausgabeordner für die Veröffentlichung. | Nein |
| `app_build_command` | Für Node.js-Anwendungen können Sie einen benutzerdefinierten Befehl definieren, um die Anwendung mit statischen Inhalten zu erstellen.<br><br>Wenn Sie beispielsweise einen Produktionsbuild für eine Angular-Anwendung konfigurieren möchten, erstellen Sie ein npm-Skript mit dem Namen `build-prod`, um `ng build --prod` auszuführen, und geben Sie `npm run build-prod` als benutzerdefinierten Befehl ein. Wenn Sie das Feld leer lassen, versucht der Workflow, den Befehl `npm run build` oder `npm run build:azure` auszuführen. | Nein |
| `api_build_command` | Für Node.js-Anwendungen können Sie einen benutzerdefinierten Befehl definieren, um die Azure Functions API-Anwendung zu erstellen. | Nein |
| `skip_app_build` | Legen Sie den Wert auf `true` fest, um das Erstellen der Front-End-App zu überspringen. | Nein |

Mit diesen Einstellungen können Sie GitHub Actions oder [Azure Pipelines](publish-devops.md) einrichten, um kontinuierliche Integration/kontinuierliche Bereitstellung (CI/CD) für Ihre statische Webanwendung durchzuführen.

> [!NOTE]
> Derzeit können Sie nur `app_build_command` und `api_build_command` für Node.js-Builds definieren.

## <a name="file-name-and-location"></a>Dateiname und Speicherort

# <a name="github-actions"></a>[GitHub-Aktionen](#tab/github-actions)

Die Konfigurationsdatei wird von GitHub generiert und im Ordner *.github/workflows* gespeichert, der nach folgendem Format benannt ist: `azure-static-web-apps-<RANDOM_NAME>.yml`.

# <a name="azure-devops"></a>[Azure DevOps](#tab/azure-devops)

Standardmäßig wird die Konfigurationsdatei im Stammverzeichnis Ihres Repositorys unter dem Namen `azure-pipelines.yml` gespeichert.

---

## <a name="build-configuration"></a>Buildkonfiguration

In der folgenden Beispielkonfiguration wird das Repository auf Änderungen überwacht. Wenn Commits in den `main`-Zweig gepusht werden, wird die Anwendung im `app_location`-Ordner erstellt und die Dateien im `output_location`-Ordner werden im öffentlichen Web bereitgestellt. Außerdem ist die Anwendung im Ordner *api* unter dem Pfad `api` der Website verfügbar.

# <a name="github-actions"></a>[GitHub-Aktionen](#tab/github-actions)

```yml
name: Azure Static Web Apps CI/CD

on:
  push:
    branches:
      - main
  pull_request:
    types: [opened, synchronize, reopened, closed]
    branches:
      - main

jobs:
  build_and_deploy_job:
    if: github.event_name == 'push' || (github.event_name == 'pull_request' && github.event.action != 'closed')
    runs-on: ubuntu-latest
    name: Build and Deploy Job
    steps:
      - uses: actions/checkout@v2
        with:
          submodules: true
      - name: Build And Deploy
        id: builddeploy
        uses: Azure/static-web-apps-deploy@v1
        with:
          azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN }}
          repo_token: ${{ secrets.GITHUB_TOKEN }} # Used for Github integrations (i.e. PR comments)
          action: "upload"
          ###### Repository/Build Configurations ######
          app_location: "src" # App source code path
          api_location: "api" # Api source code path - optional
          output_location: "public" # Built app content directory - optional
          ###### End of Repository/Build Configurations ######

  close_pull_request_job:
    if: github.event_name == 'pull_request' && github.event.action == 'closed'
    runs-on: ubuntu-latest
    name: Close Pull Request Job
    steps:
      - name: Close Pull Request
        id: closepullrequest
        uses: Azure/static-web-apps-deploy@v1
        with:
          azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN }}
          action: "close"
```

Bei dieser Konfiguration gilt Folgendes:

- Der `main` Zweig wird auf Commits überwacht.
- Ein GitHub Actions-Workflow wird [ausgelöst](https://help.github.com/actions/reference/events-that-trigger-workflows), wenn eine Pull-Anforderung auf dem `main` Zweig: geöffnet, synchronisiert, erneut geöffnet oder geschlossen wird.
- Die `build_and_deploy_job` wird ausgeführt, wenn Sie Commits pushen oder eine Pull Anforderung gegen den in der `on` Eigenschaft aufgeführten Zweig öffnen.
- Der `app_location` verweist auf den Ordner `src`, der die Quelldateien für die Webanwendung enthält.
- Der `api_location` verweist auf den Ordner `api`, der die Azure Functions-Anwendung für die API-Endpunkte der Website enthält.
- Der `output_location` verweist auf den Ordner `public`, der die endgültige Version der Quelldateien der App enthält.

Bitte ändern Sie die Werte für `repo_token`, `action` und `azure_static_web_apps_api_token` nicht, da sie von Azure Static Web Apps für Sie festgelegt werden.

# <a name="azure-devops"></a>[Azure DevOps](#tab/azure-devops)

```yaml
trigger:
  - main

pool:
  vmImage: ubuntu-latest

steps:
  - checkout: self
    submodules: true
  - task: AzureStaticWebApp@0
    inputs:
      app_location: 'src' # App source code path
      api_location: 'api' # Api source code path - optional
      output_location: 'public' # Built app content directory - optional
      azure_static_web_apps_api_token: $(deployment_token)
```

Bei dieser Konfiguration gilt Folgendes:

- Der `main` Zweig wird auf Commits überwacht.
- Der `app_location` verweist auf den Ordner `src`, der die Quelldateien für die Webanwendung enthält.
- Der `api_location` verweist auf den Ordner `api`, der die Azure Functions-Anwendung für die API-Endpunkte der Website enthält.
- Der `output_location` verweist auf den Ordner `public`, der die endgültige Version der Quelldateien der App enthält.
- Die Variable `$(deployment_token)` verweist auf das [ generierte Azure DevOps Bereitstellungs-Token](./publish-devops.md)

---

## <a name="custom-build-commands"></a>Benutzerdefinierte Buildbefehle

Für Node.js-Anwendungen können Sie genau steuern, welche Befehle während des Build-Prozesses der App oder API ausgeführt werden. Das folgende Beispiel zeigt, wie man Build mit Werten für `app_build_command` und `api_build_command` definiert.

# <a name="github-actions"></a>[GitHub-Aktionen](#tab/github-actions)

```yml
...

with:
  azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN }}
  repo_token: ${{ secrets.GITHUB_TOKEN }}
  action: 'upload'
  app_location: '/'
  api_location: 'api'
  output_location: 'dist'
  app_build_command: 'npm run build-ui-prod'
  api_build_command: 'npm run build-api-prod'
```
# <a name="azure-devops"></a>[Azure DevOps](#tab/azure-devops)

```yaml
...

inputs:
  app_location: 'src'
  api_location: 'api'
  app_build_command: 'npm run build-ui-prod'
  api_build_command: 'npm run build-api-prod'
  output_location: 'public'
  azure_static_web_apps_api_token: $(deployment_token)
```

---

## <a name="skip-building-front-end-app"></a>Überspringen des Erstellens einer Front-End-App

Wenn Sie die volle Kontrolle über die Erstellung Ihrer Front-End-Anwendung benötigen, können Sie die automatische Erstellung umgehen und die in einem vorherigen Schritt erstellte Anwendung bereitstellen.

So überspringen Sie die Erstellung der Front-End-Anwendung:

- Setzen Sie `app_location` auf den Speicherort der Dateien, die Sie bereitstellen möchten.
- Legen Sie `skip_app_build` auf `true` fest.
- Setzen Sie `output_location` auf eine leere Zeichenkette (`''`).

# <a name="github-actions"></a>[GitHub-Aktionen](#tab/github-actions)

```yml
...

with:
  azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN }}
  repo_token: ${{ secrets.GITHUB_TOKEN }}
  action: 'upload'
  app_location: 'src/dist'
  api_location: 'api'
  output_location: ''
  skip_app_build: true
```

# <a name="azure-devops"></a>[Azure DevOps](#tab/azure-devops)

```yml
...

inputs:
  app_location: 'src/dist'
  api_location: 'api'
  output_location: '' # Leave this empty
  skip_app_build: true
  azure_static_web_apps_api_token: $(deployment_token)
```

---

> [!NOTE]
> Sie können das Erstellen nur für die Front-End-App überspringen. Die API wird immer erstellt, wenn sie vorhanden ist.

## <a name="environment-variables"></a>Umgebungsvariablen

Umgebungsvariablen für Ihren Build können über den Abschnitt `env` einer Auftragskonfiguration festgelegt werden.

# <a name="github-actions"></a>[GitHub-Aktionen](#tab/github-actions)

```yaml
...

with:
  azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN }}
  repo_token: ${{ secrets.GITHUB_TOKEN }}
  action: 'upload'
  app_location: 'src'
  api_location: 'api'
  output_location: 'public'

env: # Add environment variables here
  HUGO_VERSION: 0.58.0
```

# <a name="azure-devops"></a>[Azure DevOps](#tab/azure-devops)

Die Azure DevOps unterstützen derzeit keine Umgebungsvariablen.

---

## <a name="monorepo-support"></a>Unterstützung für Monorepos

Ein Monorepo ist ein Repository, das Code für mehr als eine Anwendung enthält. Der Workflow verfolgt standardmäßig alle Dateien in einem Repository nach, aber Sie können die Konfiguration so anpassen, dass eine einzelne App als Ziel verwendet wird.

Geben Sie die Pfade in den Abschnitten `push` und `pull_request` an, um für eine Workflowdatei eine einzelne App als Ziel festzulegen.

# <a name="github-actions"></a>[GitHub-Aktionen](#tab/github-actions)

Wenn Sie eine Monorepo einrichten, ist jede statische Anwendungskonfiguration nur auf Dateien für eine einzige Anwendung beschränkt. Die verschiedenen Workflowdateien befinden sich gemeinsam im Ordner _.github/workflows_ des Repositorys.

```files
├── .github
│   └── workflows
│       ├── azure-static-web-apps-purple-pond.yml
│       └── azure-static-web-apps-yellow-shoe.yml
│
├── app1  👉 controlled by: azure-static-web-apps-purple-pond.yml
├── app2  👉 controlled by: azure-static-web-apps-yellow-shoe.yml
│
├── api1  👉 controlled by: azure-static-web-apps-purple-pond.yml
├── api2  👉 controlled by: azure-static-web-apps-yellow-shoe.yml
│
└── README.md
```

Im folgenden Beispiel wird veranschaulicht, wie Sie einen `paths`-Knoten zu den Abschnitten `push` und `pull_request` einer Datei namens _azure-static-web-apps-purple-pond.yml_ hinzufügen.

```yml
on:
  push:
    branches:
      - main
    paths:
      - app1/**
      - api1/**
      - .github/workflows/azure-static-web-apps-purple-pond.yml
  pull_request:
    types: [opened, synchronize, reopened, closed]
    branches:
      - main
    paths:
      - app1/**
      - api1/**
      - .github/workflows/azure-static-web-apps-purple-pond.yml
```

In diesem Beispiel lösen nur Änderungen an den folgenden Dateien einen neuen Build aus:

- alle Dateien im Ordner _app1_
- alle Dateien im Ordner _api1_
- Änderungen an der Workflowdatei _azure-static-web-apps-purple-pond.yml_ der App

# <a name="azure-devops"></a>[Azure DevOps](#tab/azure-devops)

Um mehr als eine Anwendung in einem einzigen Repository zu unterstützen, erstellen Sie eine separate Workflow-Datei und verknüpfen Sie mit verschiedenen Azure Pipelines.

---

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Überprüfen von Pull Requests in Präproduktionsumgebungen](review-publish-pull-requests.md)
