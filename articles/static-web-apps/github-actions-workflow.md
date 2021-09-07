---
title: GitHub Actions-Workflows für Azure Static Web Apps
description: Es wird beschrieben, wie Sie GitHub-Repositorys zum Einrichten von Continuous Deployment für Azure Static Web Apps verwenden.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 06/23/2021
ms.author: cshoe
ms.custom: contperf-fy21q4
ms.openlocfilehash: 9dc0c8a83279e3a70bceebb316536485e337c873
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122346154"
---
# <a name="github-actions-workflows-for-azure-static-web-apps"></a>GitHub Actions-Workflows für Azure Static Web Apps

Wenn Sie eine GitHub-Bereitstellung verwenden, steuert eine YAML-Datei den Erstellungsworkflow für Ihre Site. In diesem Artikel werden Struktur und Optionen der Datei erläutert.

Eine Bereitstellung wird über einen [Trigger](#triggers) gestartet, der [Aufträge](#jobs) ausführt, die aus einzelnen [Schritten](#steps) bestehen.

> [!NOTE]
> Azure Static Web Apps unterstützt auch Azure DevOps-Workflows. Informationen zum Einrichten einer Pipeline finden Sie unter [Tutorial: Veröffentlichen von Azure Static Web Apps mit Azure DevOps](publish-devops.md).

## <a name="file-name-and-location"></a>Dateiname und Speicherort

Wenn Sie Ihr Repository verknüpfen, generiert Azure Static Web Apps eine Datei, die den Workflow steuert.

Führen Sie diese Schritte aus, um die Workflowdatei anzuzeigen.

1. Öffnen Sie das Repository der App auf GitHub.
1. Wählen Sie die Registerkarte **Code** aus.
1. Wählen Sie den Ordner **.github/workflows** aus.
1. Wählen Sie die Datei aus, die einen ähnlichen Namen wie **azure-static-web-apps-<ZUFÄLLIGER_NAME>.yml** aufweist.

## <a name="triggers"></a>Trigger

Ein GitHub Actions-[Trigger](https://help.github.com/actions/reference/events-that-trigger-workflows) benachrichtigt einen GitHub Actions-Workflow darüber, dass basierend auf bestimmten Ereignissen ein Auftrag ausgeführt werden soll. Trigger werden mit der Eigenschaft `on` in der Workflowdatei aufgelistet.

```yml
on:
  push:
    branches:
      - main
  pull_request:
    types: [opened, synchronize, reopened, closed]
    branches:
      - main
```

In diesem Beispiel beginnt ein Workflow, wenn für den *main*-Branch einer der folgenden Pull Request-Typen ausgeführt wird:

- der
- Synchronisieren
- Erneut öffnen
- closed

Sie können diesen Teil des Workflows anpassen, um andere Branches oder Ereignisse zu verwenden.

## <a name="jobs"></a>Aufträge

Jeder Trigger definiert eine Reihe von [Aufträgen](https://help.github.com/actions/reference/workflow-syntax-for-github-actions#jobs), die als Reaktion auf das Ereignis ausgeführt werden sollen.

| Name | BESCHREIBUNG |
| --- | --- |
| `build_and_deploy_job` | Wird ausgeführt, wenn Sie Commits pushen oder einen Pull Request für den Branch öffnen, der in der Eigenschaft `on` aufgelistet ist.          |
| `close_pull_request_job` | Wird NUR DANN ausgeführt, wenn Sie einen Pull Request schließen, der die aus den Pull Requests erstellte Stagingumgebung entfernt. |

## <a name="steps"></a>Schritte

Schritte sind sequenzielle Aufgaben für einen Auftrag. In einem Schritt werden Aktionen durchgeführt, z. B. das Installieren von Abhängigkeiten, Ausführen von Tests und Bereitstellen Ihrer Anwendung in der Produktion.

| Auftrag | Schritte |
| --- | --- |
| `build_and_deploy_job` | <li>Führt zum Auschecken des Repositorys in der GitHub Actions-Umgebung.<li>Erstellt das Repository für Azure Static Web Apps und führt die Bereitstellung dafür durch. |
| `close_pull_request_job` | <li>Benachrichtigt Azure Static Web Apps darüber, dass ein Pull Request geschlossen wurde. |

## <a name="build-and-deploy"></a>Erstellen und Bereitstellen

Im Schritt mit dem Namen `build_and_deploy_job` wird Ihre Site erstellt und in Azure Static Web Apps bereitgestellt. Im Abschnitt `with` können Sie die folgenden Werte für Ihre Bereitstellung anpassen.

Das folgende Beispiel zeigt, wie diese Werte in einer Workflowdatei dargestellt werden.

```yml
...
with:
  azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN_MANGO_RIVER_0AFDB141E }}
  repo_token: ${{ secrets.GITHUB_TOKEN }} # Used for GitHub integrations (i.e. PR comments)
  action: 'upload'
  ###### Repository/Build Configurations - These values can be configured to match you app requirements. ######
  app_location: '/' # App source code path
  api_location: 'api' # Api source code path - optional
  output_location: 'dist' # Built app content directory - optional
  ###### End of Repository/Build Configurations ######
```

[!INCLUDE [static-web-apps-folder-structure](../../includes/static-web-apps-folder-structure.md)]

Ändern Sie die Werte für `repo_token`, `action` und `azure_static_web_apps_api_token` nicht, da sie von Azure Static Web Apps für Sie festgelegt werden.

## <a name="custom-build-commands"></a>Benutzerdefinierte Buildbefehle

Sie können genau steuern, welche Befehle während des App- oder API-Erstellungsprozesses ausgeführt werden. Die folgenden Befehle werden unter dem Abschnitt `with` eines Auftrags angezeigt.

| Befehl | BESCHREIBUNG |
| --- |--- |
| `app_build_command` | Definiert einen benutzerdefinierten Befehl zum Erstellen der Anwendung mit statischem Inhalt.<br><br>Wenn Sie beispielsweise einen Produktionsbuild für eine Angular-Anwendung konfigurieren möchten, erstellen Sie ein npm-Skript mit dem Namen `build-prod`, um `ng build --prod` auszuführen, und geben Sie `npm run build-prod` als benutzerdefinierten Befehl ein. Wenn Sie das Feld leer lassen, versucht der Workflow, den Befehl `npm run build` oder `npm run build:azure` auszuführen. |
| `api_build_command` | Definiert einen benutzerdefinierten Befehl zum Erstellen der Azure Functions-API-Anwendung. |

Das folgende Beispiel zeigt, wie benutzerdefinierte Buildbefehle im Abschnitt `with` eines Auftrags definiert werden.

```yml
...
with:
  azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN_MANGO_RIVER_0AFDB141E }}
  repo_token: ${{ secrets.GITHUB_TOKEN }} # Used for GitHub integrations (i.e. PR comments)
  action: 'upload'
  ###### Repository/Build Configurations - These values can be configured to match you app requirements. ######
  app_location: '/' # App source code path
  api_location: 'api' # Api source code path - optional
  output_location: 'dist' # Built app content directory - optional
  app_build_command: 'npm run build-ui-prod'
  api_build_command: 'npm run build-api-prod'
  ###### End of Repository/Build Configurations ######
```

> [!NOTE]
> Derzeit können Sie nur benutzerdefinierte Buildbefehle für Node.js-Builds definieren. Der Erstellungsprozess ruft immer zuerst `npm install` auf, bevor ein benutzerdefinierter Befehl aufgerufen wird.

## <a name="skip-building-front-end-app"></a>Überspringen des Erstellens einer Front-End-App

Wenn Sie die vollständige Kontrolle über die Erstellung Ihrer Front-End-App benötigen, können Sie dem Workflow benutzerdefinierte Schritte hinzufügen. Beispielsweise können Sie die automatische Erstellung umgehen und die in einem vorherigen Schritt erstellte App bereitstellen.

Um das Erstellen der Front-End-App zu überspringen, legen Sie `skip_app_build` auf `true` und `app_location` auf den Speicherort des bereitzustellenden Ordners fest.

```yml
with:
  azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN_MANGO_RIVER_0AFDB141E }}
  repo_token: ${{ secrets.GITHUB_TOKEN }} # Used for GitHub integrations (i.e. PR comments)
  action: 'upload'
  ###### Repository/Build Configurations - These values can be configured to match you app requirements. ######
  app_location: 'dist' # Application build output generated by a previous step
  api_location: 'api' # Api source code path - optional
  output_location: '' # Leave this empty
  skip_app_build: true
  ###### End of Repository/Build Configurations ######
```

| Eigenschaft         | BESCHREIBUNG                                                 |
| ---------------- | ----------------------------------------------------------- |
| `skip_app_build` | Legen Sie den Wert auf `true` fest, um das Erstellen der Front-End-App zu überspringen. |

> [!NOTE]
> Sie können das Erstellen nur für die Front-End-App überspringen. Die API wird immer erstellt, wenn sie vorhanden ist.

## <a name="environment-variables"></a>Umgebungsvariablen

Umgebungsvariablen für Ihren Build können über den Abschnitt `env` einer Auftragskonfiguration festgelegt werden.

```yaml
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
          repo_token: ${{ secrets.GITHUB_TOKEN }}
          action: 'upload'
          ###### Repository/Build Configurations
          app_location: '/'
          api_location: 'api'
          output_location: 'public'
          ###### End of Repository/Build Configurations ######
        env: # Add environment variables here
          HUGO_VERSION: 0.58.0
```

## <a name="monorepo-support"></a>Unterstützung für Monorepos

Ein Monorepo ist ein Repository, das Code für mehr als eine Anwendung enthält. Der Workflow verfolgt standardmäßig alle Dateien in einem Repository nach, aber Sie können die Konfiguration so anpassen, dass eine einzelne App als Ziel verwendet wird.

Wenn Sie ein Einzelrepository einrichten, verfügt jede statische App über eine eigene Konfigurationsdatei, die nur für die Dateien in einer einzelnen App gilt. Die verschiedenen Workflowdateien befinden sich gemeinsam im Ordner _.github/workflows_ des Repositorys.

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

Geben Sie die Pfade in den Abschnitten `push` und `pull_request` an, um für eine Workflowdatei eine einzelne App als Ziel festzulegen.

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

In diesem Fall wird nur bei Änderungen, die an den folgenden Dateien vorgenommen wurden, ein neuer Build ausgelöst:

- alle Dateien im Ordner _app1_
- alle Dateien im Ordner _api1_
- Änderungen an der Workflowdatei _azure-static-web-apps-purple-pond.yml_ der App

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Überprüfen von Pull Requests in Präproduktionsumgebungen](review-publish-pull-requests.md)
