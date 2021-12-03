---
title: Einrichten der lokalen Entwicklung für Azure Static Web Apps
description: Es wird beschrieben, wie Sie Ihre lokale Entwicklungsumgebung für Azure Static Web Apps einrichten.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: how-to
ms.date: 10/21/2021
ms.author: cshoe
ms.custom: devx-track-js
ms.openlocfilehash: 7419eea1503d8d0692bd1b112226c8ce626d9776
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130261574"
---
# <a name="set-up-local-development-for-azure-static-web-apps"></a>Einrichten der lokalen Entwicklung für Azure Static Web Apps

Bei Veröffentlichung in der Cloud verknüpft eine Azure Static Web Apps-Website zahlreiche Dienste, die wie eine einzelne Anwendung zusammenarbeiten. Zu diesen Diensten gehören:

- Die statische Web-App
- Azure Functions-API
- Authentifizierungs- und Autorisierungsdienste
- Routing- und Konfigurationsdienste

Diese Dienste müssen miteinander kommunizieren, und in der Cloud nimmt Azure Static Web Apps Ihnen diese Integration ab.

Bei lokaler Verwendung sind diese Dienste allerdings nicht automatisch miteinander verknüpft.

Um eine ähnliche Funktionalität wie in Azure zu erreichen, bietet die [Azure Static Web Apps-Befehlszeilenschnittstelle](https://github.com/Azure/static-web-apps-cli) folgende Dienste:

- Lokaler statischer Websiteserver
- Proxy für den Entwicklungsserver des Front-End-Frameworks
- Proxy für Ihre API-Endpunkte (verfügbar über Azure Functions Core Tools)
- Simulierter Authentifizierungs- und Autorisierungsserver
- Erzwingung lokaler Routen und Konfigurationseinstellungen

> [!NOTE]
> Häufig erfordern Websites, die mit einem Front-End-Framework erstellt wurden, eine Proxykonfigurationseinstellung, um Anforderungen unter der `api`-Route ordnungsgemäß zu verarbeiten. Wenn Sie die Azure Static Web Apps CLI verwenden, lautet der Wert für den Proxyspeicherort `/api`, und ohne die CLI ist der Wert `http://localhost:7071/api`.

## <a name="how-it-works"></a>Funktionsweise

Das folgende Diagramm veranschaulicht die lokale Behandlung von Anforderungen:

:::image type="content" source="media/local-development/cli-conceptual.png" alt-text="Azure Static Web Apps-Befehlszeilenschnittstelle: Anforderungs- und Antwortflow":::

> [!IMPORTANT]
> Navigieren Sie zu `http://localhost:4280`, um auf die von der Befehlszeilenschnittstelle bereitgestellte Anwendung zuzugreifen.

- An den Port `4280` gesendete **Anforderungen** werden je nach Art der Anforderung an den entsprechenden Server weitergeleitet.

- Anforderungen vom Typ **Statischer Inhalt** (beispielsweise HTML oder CSS) werden entweder vom statischen Inhaltsserver der internen Befehlszeilenschnittstelle behandelt oder vom Front-End-Framework-Server (zum Debuggen).

- Anforderungen vom Typ **Authentifizierung und Autorisierung** werden von einem Emulator behandelt, der ein unechtes Identitätsprofil für Ihre App bereitstellt.

- Von der **Functions Core Tools-Runtime** werden Anforderungen für die API der Website behandelt.

- **Antworten** von allen Diensten werden an den Browser zurückgegeben, als würde es sich um eine einzelne Anwendung handeln.

## <a name="prerequisites"></a>Voraussetzungen

- **Vorhandene Azure Static Web Apps-Website:** Sollten Sie über keine Website verfügen, beginnen Sie mit der Starter-App [vanilla-api](https://github.com/staticwebdev/vanilla-api/generate?return_to=/staticwebdev/vanilla-api/generate).
- **[Node.js](https://nodejs.org) mit npm:** Verwenden Sie die Version [Node.js LTS](https://nodejs.org) (beinhaltet Zugriff auf [npm](https://www.npmjs.com/)).
- **[Visual Studio Code:](https://code.visualstudio.com/)** Dient zum Debuggen der API-Anwendung, ist für die Befehlszeilenschnittstelle aber nicht erforderlich.
- **[Azure Functions Core Tools:](https://github.com/Azure/azure-functions-core-tools#installing)** erforderlich, um die API lokal ausführen zu können

## <a name="get-started"></a>Erste Schritte

Öffnen Sie ein Terminal am Stammordner Ihrer vorhandenen Azure Static Web Apps-Website.

1. Installieren Sie die Befehlszeilenschnittstelle.

    ```console
    npm install -g @azure/static-web-apps-cli
    ```

1. Erstellen Sie Ihre App, falls dies für Ihre Anwendung erforderlich ist.

    Führen Sie `npm run build` oder den entsprechenden Befehl für Ihr Projekt aus.

1. Wechseln Sie zum Ausgabeverzeichnis für Ihre App. Ausgabeordner haben häufig den Namen _build_ oder eine ähnliche Bezeichnung.

1. Starten Sie die Befehlszeilenschnittstelle.

    ```console
    swa start
    ```

1. Navigieren Sie zu `http://localhost:4280`, um die App im Browser anzuzeigen.

### <a name="other-ways-to-start-the-cli"></a>Weitere Möglichkeiten zum Starten der Befehlszeilenschnittstelle

| BESCHREIBUNG | Befehl |
|--- | --- |
| Bereitstellen eines bestimmten Ordners | `swa start ./output-folder` |
| Verwenden eines aktiven Framework-Entwicklungsservers | `swa start http://localhost:3000` |
| Starten einer Funktions-App in einem Ordner | `swa start ./output-folder --api-location ./api` |
| Verwenden einer aktiven Funktions-App | `swa start ./output-folder --api-location http://localhost:7071` |

## <a name="authorization-and-authentication-emulation"></a>Autorisierungs- und Authentifizierungsemulation

Die Static Web Apps-Befehlszeilenschnittstelle emuliert den in Azure implementierten [Sicherheitsflow](./authentication-authorization.md). Wenn sich ein Benutzer anmeldet, können Sie ein unechtes Identitätsprofil definieren, das an die App zurückgegeben wird.

Wenn Sie also beispielsweise versuchen, zu `/.auth/login/github` zu navigieren, wird eine Seite zurückgegeben, auf der Sie ein Identitätsprofil definieren können.

> [!NOTE]
> Der Emulator kann mit einem beliebigen Sicherheitsanbieter verwendet werden, nicht nur mit GitHub.

:::image type="content" source="media/local-development/auth-emulator.png" alt-text="Lokaler Authentifizierungs- und Autorisierungsemulator":::

Der Emulator bietet eine Seite, auf der Sie die folgenden Werte für den [Clientprinzipal](./user-information.md#client-principal-data) angeben können:

| Wert | BESCHREIBUNG |
| --- | --- |
| **Benutzername** | Der dem Sicherheitsanbieter zugeordnete Kontoname. Dieser Wert wird im Clientprinzipal als Eigenschaft `userDetails` angezeigt und automatisch generiert, wenn Sie keinen Wert angeben. |
| **Benutzer-ID** | Automatisch von der Befehlszeilenschnittstelle generierter Wert.  |
| **Rollen** | Eine Rollennamenliste mit jeweils einer Zeile pro Name.  |

Nach der Anmeldung gilt Folgendes:

- Sie können den Endpunkt `/.auth/me` oder einen Funktionsendpunkt verwenden, um den [Clientprinzipal](./user-information.md) des Benutzers abzurufen.

- Wenn Sie zu `/.auth/logout` navigieren, wird der Clientprinzipal gelöscht und der simulierte Benutzer abgemeldet.

## <a name="debugging"></a>Debuggen

In einer statischen Web-App gibt es zwei Debugkontexte: einen für die statische Inhaltswebsite und einen für API-Funktionen. Lokales Debuggen ist möglich, indem der Static Web Apps-Befehlszeilenschnittstelle die Verwendung von Entwicklungsservern für einen oder beide dieser Kontexte ermöglicht wird.

Die folgenden Schritte zeigen ein gängiges Szenario, in dem Entwicklungsserver für beide Debugkontexte verwendet werden.

1. Starten Sie den statischen Websiteentwicklungsserver. Dieser Befehl ist für das von Ihnen verwendete Front-End-Framework spezifisch, lautet aber häufig `npm run build`, `npm start` oder `npm run dev`.

1. Öffnen Sie den API-Anwendungsordner in Visual Studio Code, und starten Sie eine Debugsitzung.

1. Starten Sie die Static Web Apps-CLI mit dem folgenden Befehl.


    ```console
    swa start http://localhost:<DEV-SERVER-PORT-NUMBER> --api-location http://localhost:7071
    ```

    Ersetzen Sie `<DEV-SERVER-PORT-NUMBER>` durch die Portnummer des Entwicklungsservers.

Die folgenden Screenshots zeigen die Terminals für ein typisches Debugszenario:

Die statische Inhaltswebsite wird über `npm run dev` ausgeführt.

:::image type="content" source="media/local-development/run-dev-static-server.png" alt-text="Statischer Websiteentwicklungsserver":::

Von der Azure Functions-API-Anwendung wird eine Debugsitzung in Visual Studio Code ausgeführt.

:::image type="content" source="media/local-development/visual-studio-code-debugging.png" alt-text="Visual Studio Code: API-Debuggen":::

Die Static Web Apps-Befehlszeilenschnittstelle wird unter Verwendung beider Entwicklungsserver gestartet.

:::image type="content" source="media/local-development/static-web-apps-cli-terminal.png" alt-text="Azure Static Web Apps: Terminal der Befehlszeilenschnittstelle":::

Anforderungen über den Port `4280` werden nun entweder an den statischen Inhaltsentwicklungsserver oder an die API-Debugsitzung weitergeleitet.

Weitere Informationen zu verschiedenen Debugszenarien sowie Anleitungen zum Anpassen von Ports und Serveradressen finden Sie im [Repository für die Azure Static Web Apps-Befehlszeilenschnittstelle](https://github.com/Azure/static-web-apps-cli).

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Konfigurieren der Anwendung](configuration.md)
