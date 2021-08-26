---
title: 'Tutorial: Bereitstellen von per Server gerenderten Nuxt.js-Websites in Azure Static Web Apps'
description: Es wird beschrieben, wie Sie dynamische Nuxt.js-Websites mit Azure Static Web Apps generieren und bereitstellen.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 05/08/2020
ms.author: cshoe
ms.custom: devx-track-js
ms.openlocfilehash: 615bba10b410ec054227e1f2d45d2fdfde7b225d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121748020"
---
# <a name="deploy-server-rendered-nuxtjs-websites-on-azure-static-web-apps"></a>Bereitstellen von per Server gerenderten Nuxt.js-Websites in Azure Static Web Apps

In diesem Tutorial erfahren Sie, wie Sie eine mit [Nuxt.js](https://nuxtjs.org) generierte statische Website für [Azure Static Web Apps](overview.md) bereitstellen. Zunächst wird beschrieben, wie Sie eine Nuxt.js-App einrichten, konfigurieren und bereitstellen. Im Rahmen dieses Prozesses erfahren Sie auch, wie Sie häufige Schwierigkeiten überwinden, die beim Generieren von statischen Seiten mit Nuxt.js auftreten können.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/).
- Ein GitHub-Konto. Sie können [kostenlos ein Konto erstellen](https://github.com/join).
- Installation von [Node.js](https://nodejs.org)

## <a name="set-up-a-nuxtjs-app"></a>Einrichten einer Nuxt.js-App

Sie können ein neues Nuxt.js-Projekt einrichten, indem Sie `create-nuxt-app` verwenden. In diesem Tutorial beginnen Sie nicht mit einem neuen Projekt, sondern Sie klonen ein vorhandenes Repository. Mit diesem Repository wird veranschaulicht, wie Sie eine dynamische Nuxt.js-App als statische Website bereitstellen.

1. Erstellen Sie aus einem Vorlagenrepository unter Ihrem GitHub-Konto ein neues Repository.
1. Navigieren Sie zu [http://github.com/staticwebdev/nuxtjs-starter/generate](https://github.com/login?return_to=/staticwebdev/nuxtjs-starter/generate).
1. Geben Sie dem Repository den Namen **nuxtjs-starter**.
1. Klonen Sie das neue Repository anschließend auf Ihrem Computer. Ersetzen Sie „<YOUR_GITHUB_ACCOUNT_NAME>“ durch den Namen Ihres Kontos.

    ```bash
    git clone http://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/nuxtjs-starter
    ```

1. Navigieren Sie zur neu geklonten Nuxt.js-App:

   ```bash
   cd nuxtjs-starter
   ```

1. Installieren von Abhängigkeiten:

    ```bash
    npm install
    ```

1. Starten Sie die Nuxt.js-App in der Entwicklung:

    ```bash
    npm run dev
    ```

Navigieren Sie zu `http://localhost:3000`, um die App zu öffnen. Die folgende Website sollte in Ihrem bevorzugten Browser geöffnet werden:

:::image type="content" source="media/deploy-nuxtjs/start-nuxtjs-app.png" alt-text="Starten der Nuxt.js-App":::

Wenn Sie auf ein Framework bzw. eine Bibliothek klicken, sollte eine Detailseite zum ausgewählten Element angezeigt werden:

:::image type="content" source="media/deploy-nuxtjs/start-nuxtjs-details.png" alt-text="Detailseite":::

## <a name="generate-a-static-website-from-nuxtjs-build"></a>Generieren einer statischen Website aus dem Nuxt.js-Build

Beim Erstellen einer Nuxt.js-Website mit `npm run build` wird die App als herkömmliche Web-App und nicht als statische Website erstellt. Verwenden Sie die unten angegebene Anwendungskonfiguration, um eine statische Website zu generieren.

1. Aktualisieren Sie das Buildskript der Datei _package.json_, um mit dem Befehl `nuxt generate` nur eine statische Website zu generieren:

    ```json
    "scripts": {
      "dev": "nuxt dev",
      "build": "nuxt generate"
    },
    ```

    Nach der Ausführung dieses Befehls wird das Skript `build` von Azure Static Web Apps jedes Mal ausgeführt, wenn Sie einen Commit pushen.

1. Generieren einer statischen Website:

    ```bash
    npm run build
    ```

    Nuxt.js generiert die statische Website und kopiert sie in den Ordner _dist_ im Stammverzeichnis Ihres Arbeitsverzeichnisses.

    > [!NOTE]
    > Dieser Ordner ist in der _GITIGNORE_-Datei aufgeführt, damit er während des CI/CD-Vorgangs generiert werden kann, wenn Sie die Bereitstellung durchführen.

## <a name="push-your-static-website-to-github"></a>Pushen Ihrer statischen Website zu GitHub

Bei Azure Static Web Apps wird Ihre App aus einem GitHub-Repository bereitgestellt. Dies wird dann für jedes Pushen eines Commits zu einem entsprechend angegebenen Branch durchgeführt. Verwenden Sie die folgenden Befehle, um Ihre Änderungen mit GitHub zu synchronisieren.

1. Stellen Sie alle geänderten Dateien bereit:

    ```bash
    git add .
    ```

1. Führen Sie einen Commit für alle Änderungen aus.

    ```bash
    git commit -m "Update build config"
    ```

1. Pushen Sie Ihre Änderungen zu GitHub.

    ```bash
    git push origin main
    ```

## <a name="deploy-your-static-website"></a>Bereitstellen Ihrer statischen Website

Die folgenden Schritte veranschaulichen, wie Sie die App, die Sie gerade zu GitHub gepusht haben, mit Azure Static Web Apps verknüpfen. Wenn sich die Anwendung in Azure befindet, können Sie sie in einer Produktionsumgebung bereitstellen.

### <a name="create-an-azure-static-web-apps-resource"></a>Erstellen einer Azure Static Web Apps-Ressource

1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com).
1. Klicken Sie auf **Ressource erstellen**.
1. Suchen Sie nach **Static Web Apps**.
1. Klicken Sie auf **statische Web-Apps**.
1. Klicken Sie auf **Erstellen**.
1. Geben Sie auf der Registerkarte _Grundlagen_ die folgenden Werte ein.

    | Eigenschaft | Wert |
    | --- | --- |
    | _Abonnement_ | Den Namen Ihres Azure-Abonnements |
    | _Ressourcengruppe_ | **my-nuxtjs-group**  |
    | _Name_ | **my-nuxtjs-app** |
    | _Plantyp_ | **Free** |
    | _Region für Azure Functions-API und Stagingumgebungen_ | Wählen Sie die Region aus, die Ihnen am nächsten ist. |
    | _Quelle_ | **GitHub** |

1. Wählen Sie **Mit GitHub anmelden** aus, und authentifizieren Sie sich bei GitHub.

1. Geben Sie die folgenden GitHub-Werte ein.

    | Eigenschaft | Wert |
    | --- | --- |
    | _Organisation_ | Wählen Sie Ihre gewünschte GitHub-Organisation aus. |
    | _Repository_ | Wählen Sie das zuvor erstellte Repository aus. |
    | _Branch_ | Wählen Sie **main** aus. |

1. Wählen Sie im Abschnitt _Builddetails_ in der Dropdownliste _Buildvoreinstellungen_ die Option **Benutzerdefiniert** aus, und übernehmen Sie die Standardwerte.

1. Geben Sie im Feld _API-Speicherort_ **./** ein.
1. Lassen Sie das Feld _API-Speicherort_ leer.
1. Geben Sie im Feld _Ausgabespeicherort_ **out** ein.

### <a name="review-and-create"></a>Überprüfen und Erstellen

1. Wählen Sie die Schaltfläche **Überprüfen und erstellen** aus, um sicherzustellen, dass alle Details stimmen.

1. Wählen Sie **Erstellen** aus, um mit der Erstellung der statischen App Service-Web-App zu beginnen und einen GitHub Action-Vorgang für die Bereitstellung anzugeben.

1. Wenn die Bereitstellung abgeschlossen ist, klicken Sie auf **Zu Ressource wechseln**.

1. Klicken Sie im Fenster _Übersicht_ auf den Link *URL*, um Ihre bereitgestellte Anwendung zu öffnen.

Falls die Website nicht sofort geladen wird, ist der Workflow für GitHub Actions im Hintergrund noch aktiv. Nach Abschluss des Workflows können Sie auf die Schaltfläche zum Aktualisieren des Browsers klicken, um Ihre Web-App anzuzeigen.

Sie können den Status der GitHub Actions-Workflows überprüfen, indem Sie zu den Aktionen für Ihr Repository navigieren:

```url
https://github.com/<YOUR_GITHUB_USERNAME>/nuxtjs-starter/actions
```

### <a name="sync-changes"></a>Synchronisieren von Änderungen

Als Sie die App erstellt haben, wurde von Azure Static Web Apps in Ihrem Repository eine GitHub Actions-Workflowdatei erstellt. Sie müssen diese Datei in Ihr lokales Repository einfügen, damit Ihr Git-Verlauf synchronisiert wird.

Wechseln Sie zurück zum Terminal, und führen Sie den folgenden Befehl aus: `git pull origin main`.

## <a name="configure-dynamic-routes"></a>Konfigurieren dynamischer Routen

Navigieren Sie zur neu bereitgestellten Website, und klicken Sie auf eines der Logos für ein Framework oder eine Bibliothek. Anstelle der Detailseite wird eine Seite mit dem Fehler 404 angezeigt.

:::image type="content" source="media/deploy-nuxtjs/404-in-production.png" alt-text="Fehler 404 bei dynamischen Routen":::

Der Grund ist, dass Nuxt.js die statische Website nur für die Startseite generiert hat. Nuxt.js kann gleichwertige statische `.html`-Dateien für jede `.vue`-Seitendatei generieren, aber es gibt eine Ausnahme. 

Wenn es sich bei der Seite um eine dynamische Seite handelt, z. B. `_id.vue`, sind nicht genügend Informationen zum Generieren einer statischen HTML-Datei aus dieser dynamischen Seite vorhanden. Sie müssen die möglichen Pfade für die dynamischen Routen explizit angeben.

## <a name="generate-static-pages-from-dynamic-routes"></a>Generieren von statischen Seiten aus dynamischen Routen

1. Aktualisieren Sie die Datei _nuxt.config.js_, damit von Nuxt.js eine Liste mit allen verfügbaren Daten verwendet wird, um statische Seiten für jedes Framework bzw. jede Bibliothek zu generieren:

   ```javascript
     import { projects } from "./utils/projectsData";

     export default {
       mode: "universal",

       //...truncated

       generate: {
         async routes() {
           const paths = [];

           projects.forEach(project => {
             paths.push(`/project/${project.slug}`);
           });

           return paths;
         }
       }
     };
   ```

   > [!NOTE]
   > Da `routes` eine asynchrone Funktion ist, können Sie darin eine Anforderung an eine API senden und die zurückgegebene Liste verwenden, um die Pfade zu generieren.

2. Pushen Sie die neuen Änderungen in Ihr GitHub-Repository, und warten Sie einige Minuten, während Ihre Website von GitHub Actions neu erstellt wird. Nach Abschluss des Buildvorgangs wird der Fehler 404 nicht mehr angezeigt.

   :::image type="content" source="media/deploy-nuxtjs/404-in-production-fixed.png" alt-text="Behobener Fehler 404 für dynamische Routen":::

> [!div class="nextstepaction"]
> [Einrichten einer benutzerdefinierten Domäne](custom-domain.md)
