---
title: 'Tutorial: Bereitstellen von statisch gerenderten Next.js-Websites in Azure Static Web Apps'
description: Es wird beschrieben, wie Sie dynamische Next.js-Websites mit Azure Static Web Apps generieren und bereitstellen.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 08/05/2021
ms.author: cshoe
ms.custom: devx-track-js
ms.openlocfilehash: 1f8ef3146ce7ef1b1767c04284ddbdb191b50d81
ms.sourcegitcommit: d01c2b2719e363178720003b67b968ac2a640204
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/19/2021
ms.locfileid: "122455902"
---
# <a name="deploy-static-rendered-nextjs-websites-on-azure-static-web-apps"></a>Bereitstellen von statisch gerenderten Next.js-Websites in Azure Static Web Apps

In diesem Tutorial erfahren Sie, wie Sie eine mit [Next.js](https://nextjs.org) generierte statische Website für [Azure Static Web Apps](overview.md) bereitstellen. Weitere Informationen zu den Besonderheiten von Next.js finden Sie in der [Infodatei mit der Starter-Vorlage](https://github.com/staticwebdev/nextjs-starter#readme).

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/).
- Ein GitHub-Konto. Sie können [kostenlos ein Konto erstellen](https://github.com/join).
- Installation von [Node.js](https://nodejs.org)

## <a name="set-up-a-nextjs-app"></a>Einrichten einer Next.js-App

Anstatt die Next.js-CLI zum Erstellen einer App zu verwenden, können Sie auch ein Startrepository nutzen. Das Startrepository enthält eine vorhandene Next.js-Anwendung, die dynamische Routen unterstützt.

Erstellen Sie zunächst aus einem Vorlagenrepository unter Ihrem GitHub-Konto ein neues Repository.

1. Navigieren Sie zu [https://github.com/staticwebdev/nextjs-starter/generate](https://github.com/login?return_to=/staticwebdev/nextjs-starter/generate).
1. Geben Sie dem Repository den Namen **nextjs-starter**.
1. Klonen Sie das neue Repository anschließend auf Ihrem Computer. Ersetzen Sie `<YOUR_GITHUB_ACCOUNT_NAME>` durch Ihren Kontonamen.

    ```bash
    git clone http://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/nextjs-starter
    ```

1. Navigieren Sie zur neu geklonten Next.js-App:

   ```bash
   cd nextjs-starter
   ```

1. Installieren von Abhängigkeiten:

    ```bash
    npm install
    ```

1. Starten Sie die Next.js-App in der Entwicklung:

    ```bash
    npm run dev
    ```

Navigieren Sie zu `http://localhost:3000`, um die App zu öffnen. Die folgende Website sollte in Ihrem bevorzugten Browser geöffnet werden:

:::image type="content" source="media/deploy-nextjs/start-nextjs-app.png" alt-text="Starten der Next.js-App":::

Wenn Sie ein Framework oder eine Bibliothek auswählen, wird eine Detailseite zum ausgewählten Element angezeigt:

:::image type="content" source="media/deploy-nextjs/start-nextjs-details.png" alt-text="Detailseite":::

## <a name="deploy-your-static-website"></a>Bereitstellen Ihrer statischen Website

Die folgenden Schritte veranschaulichen, wie Sie Ihre App mit Azure Static Web Apps verknüpfen. Wenn sich die Anwendung in Azure befindet, können Sie sie in einer Produktionsumgebung bereitstellen.

### <a name="create-a-static-app"></a>Erstellen einer statischen App

1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com).
1. Klicken Sie auf **Ressource erstellen**.
1. Suchen Sie nach **Static Web Apps**.
1. Klicken Sie auf **statische Web-Apps**.
1. Klicken Sie auf **Erstellen**.
1. Geben Sie auf der Registerkarte _Grundlagen_ die folgenden Werte ein.

    | Eigenschaft | Wert |
    | --- | --- |
    | _Abonnement_ | Den Namen Ihres Azure-Abonnements |
    | _Ressourcengruppe_ | **my-nextjs-group**  |
    | _Name_ | **my-nextjs-app** |
    | _Plantyp_ | **Free** |
    | _Region für Azure Functions-API und Stagingumgebungen_ | Wählen Sie die Region aus, die Ihnen am nächsten ist. |
    | _Quelle_ | **GitHub** |

1. Wählen Sie **Mit GitHub anmelden** aus, und authentifizieren Sie sich bei GitHub.

1. Geben Sie die folgenden GitHub-Werte ein.

    | Eigenschaft | Wert |
    | --- | --- |
    | _Organisation_ | Wählen Sie die entsprechende GitHub-Organisation aus. |
    | _Repository_ | Wählen Sie **nextjs-starter** aus. |
    | _Branch_ | Wählen Sie **main** aus. |

1. Wählen Sie im Abschnitt _Builddetails_ unter _Buildvoreinstellungen_ die Option **Benutzerdefiniert** aus. Fügen Sie die folgenden Werte für die Buildkonfiguration hinzu:

    | Eigenschaft | Wert |
    | --- | --- |
    | _App-Speicherort_ | Geben Sie **/** in das Feld ein. |
    | _API-Speicherort_ | Lassen Sie dieses Feld leer. |
    | _Ausgabespeicherort_ | Geben Sie **out** in das Feld ein. |

### <a name="review-and-create"></a>Überprüfen und Erstellen

1. Wählen Sie die Schaltfläche **Überprüfen und erstellen** aus, um sicherzustellen, dass alle Details stimmen.

1. Wählen Sie **Erstellen** aus, um mit der Erstellung der statischen App Service-Web-App zu beginnen und einen GitHub Action-Vorgang für die Bereitstellung anzugeben.

1. Wählen Sie nach Abschluss der Bereitstellung die Option **Zu Ressource wechseln** aus.

1. Wählen Sie im Fenster _Übersicht_ den Link *URL* aus, um Ihre bereitgestellte Anwendung zu öffnen.

Wenn die Website nicht sofort geladen wird, wird der Build noch ausgeführt. Nach Abschluss des Workflows können Sie den Browser aktualisieren, um Ihre Web-App anzuzeigen.

Wenn Sie den Status des Actions-Workflows überprüfen möchten, navigieren Sie zum Actions-Dashboard für Ihr Repository:

```url
https://github.com/<YOUR_GITHUB_USERNAME>/nextjs-starter/actions
```

Alle am `main`-Branch vorgenommenen Änderungen starten nun eine neue Erstellung und Bereitstellung Ihrer Website.

### <a name="sync-changes"></a>Synchronisieren von Änderungen

Als Sie die App erstellt haben, wurde von Azure Static Web Apps in Ihrem Repository eine GitHub Actions-Datei erstellt. Führen Sie die Synchronisierung mit dem Server aus, indem Sie die neueste Version in Ihr lokales Repository pullen.

Wechseln Sie zurück zum Terminal, und führen Sie den folgenden Befehl aus: `git pull origin main`.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Falls Sie diese Anwendung nicht weiter nutzen möchten, können Sie die Azure Static Web Apps-Instanz mit den folgenden Schritten löschen:

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com).
1. Suchen Sie über die obere Suchleiste nach **my-nextjs-group**.
1. Wählen Sie den Gruppennamen aus.
1. Klicken Sie auf die Schaltfläche **Löschen**.
1. Klicken Sie zum Bestätigen des Löschvorgangs auf **Ja**.

> [!div class="nextstepaction"]
> [Einrichten einer benutzerdefinierten Domäne](custom-domain.md)
