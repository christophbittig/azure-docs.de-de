---
title: 'Schnellstart: Hinzufügen von Authentifizierung zu einer Node.js-Web-App mit MSAL Node | Azure'
titleSuffix: Microsoft identity platform
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie Authentifizierung bei einer Node.js-Web-App und der Microsoft-Authentifizierungsbibliothek (Microsoft Authentication Library, MSAL) für Node.js implementieren.
services: active-directory
author: mmacy
manager: celested
ms.service: active-directory
ms.subservice: develop
ms.topic: portal
ms.workload: identity
ms.date: 11/22/2021
ROBOTS: NOINDEX
ms.author: marsma
ms.custom: aaddev, "scenarios:getting-started", "languages:js", devx-track-js, mode-api
ms.openlocfilehash: 9eb5b0e60eeddc0c4eba1f21043bf8d3c222e3b4
ms.sourcegitcommit: 3f20f370425cb7d51a35d0bba4733876170a7795
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/27/2022
ms.locfileid: "137803853"
---
# <a name="quickstart-sign-in-users-and-get-an-access-token-in-a-nodejs-web-app-using-the-auth-code-flow"></a>Schnellstart: Anmelden von Benutzer*innen und Abrufen eines Zugriffstokens in einer Node.js-Web-App mithilfe des Autorisierungscodeflows

In dieser Schnellstartanleitung laden Sie ein Codebeispiel herunter und führen es aus, das zeigt, wie eine Node.js-Web-App Benutzer mithilfe des Autorisierungscodeflows anmelden kann. Das Codebeispiel veranschaulicht außerdem das Abrufen eines Zugriffstokens zum Aufrufen der Microsoft Graph-API.

Eine Abbildung finden Sie unter [Funktionsweise des Beispiels](#how-the-sample-works).

In dieser Schnellstartanleitung wird die Microsoft-Authentifizierungsbibliothek (Microsoft Authentication Library, MSAL) für Node.js (MSAL Node) beim Autorisierungscodeflow verwendet.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. [Erstellen Sie ein kostenloses Azure-Abonnement.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download) oder ein anderer Code-Editor

#### <a name="step-1-configure-the-application-in-azure-portal"></a>Schritt 1: Konfigurieren der Anwendung im Azure-Portal
Damit das Codebeispiel für diese Schnellstartanleitung funktioniert, müssen Sie einen geheimen Clientschlüssel erstellen und die folgende Antwort-URL hinzufügen: `http://localhost:3000/redirect`.
> [!div class="nextstepaction"]
> [Diese Änderung für mich vornehmen]()

> [!div class="alert alert-info"]
> ![Bereits konfiguriert](media/quickstart-v2-windows-desktop/green-check.png): Ihre Anwendung ist mit diesen Attributen konfiguriert.

#### <a name="step-2-download-the-project"></a>Schritt 2: Herunterladen des Projekts

Führen Sie das Projekt mit einem Webserver unter Verwendung von Node.js aus:

> [!div class="nextstepaction"]
> [Laden Sie das Codebeispiel herunter](https://github.com/Azure-Samples/ms-identity-node/archive/main.zip).

#### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Schritt 3: Ihre App ist konfiguriert und betriebsbereit

Führen Sie das Projekt mithilfe von Node.js aus.

1. Führen Sie im Projektverzeichnis die folgenden Befehle aus, um den Server zu starten:

    ```console
    npm install
    npm start
    ```

1. Wechseln Sie zu `http://localhost:3000/`.

1. Wählen Sie **Anmelden** aus, um den Anmeldeprozess zu starten.

    Bei der ersten Anmeldung werden Sie aufgefordert, einzuwilligen, dass die Anwendung auf Ihr Profil zugreifen und Sie anmelden darf. Nachdem Sie sich erfolgreich angemeldet haben, wird in der Befehlszeile eine Protokollmeldung angezeigt.

## <a name="more-information"></a>Weitere Informationen

### <a name="how-the-sample-works"></a>Funktionsweise des Beispiels

Das Beispiel hostet einen Webserver auf „localhost“, Port 3000. Wenn ein Webbrowser auf diese Website zugreift, leitet das Beispiel den Benutzer sofort zu einer Microsoft-Authentifizierungsseite weiter. Aus diesem Grund enthält das Beispiel keine HTML- oder Anzeigeelemente. Bei erfolgreicher Authentifizierung wird die Meldung „OK“ angezeigt.

### <a name="msal-node"></a>MSAL Node

Über die Bibliothek „MSAL Node“ werden Benutzer angemeldet und die Token angefordert, die für den Zugriff auf eine durch Microsoft Identity Platform geschützte API verwendet werden. Sie können die neueste Version mithilfe von Node.js-Paket-Manager (npm) herunterladen:

```console
npm install @azure/msal-node
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Hinzufügen von Auth zu einer vorhandenen Web-App – GitHub-Codebeispiel >](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/samples/msal-node-samples/auth-code)
