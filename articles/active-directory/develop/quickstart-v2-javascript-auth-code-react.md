---
title: 'Schnellstart: Anmelden von Benutzern bei JavaScript React-Single-Page-Webanwendungen (SPA) mit Autorisierungscode und Aufrufen von Microsoft Graph | Azure'
titleSuffix: Microsoft identity platform
description: In dieser Schnellstartanleitung erfahren Sie, wie eine JavaScript React-Single-Page-Webanwendung (SPA) mithilfe des Autorisierungscodeflows Benutzer von persönlichen Konten sowie von Geschäfts-, Schul- und Unikonten anmelden und Microsoft Graph aufrufen kann.
services: active-directory
author: j-mantu
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: portal
ms.workload: identity
ms.date: 11/12/2021
ROBOTS: NOINDEX
ms.author: jamesmantu
ms.custom: aaddev, "scenarios:getting-started", "languages:JavaScript", devx-track-js, mode-other
ms.openlocfilehash: 2cdbab9976e4df00012597ec1f8c865c945e3e9e
ms.sourcegitcommit: ee9bae378f0b2b63b356a3ef3131640572f8c795
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/14/2021
ms.locfileid: "133790692"
---
# <a name="quickstart-sign-in-and-get-an-access-token-in-a-react-spa-using-the-auth-code-flow"></a>Schnellstart: Anmelden und Abrufen eines Zugriffstokens in einer React-SPA mithilfe des Autorisierungscodeflows

In diesem Schnellstart laden Sie ein Codebeispiel herunter und führen es aus, das zeigt, wie eine JavaScript React-Single-Page-Webanwendung Benutzer anmelden und Microsoft Graph mithilfe des Autorisierungscodeflow aufrufen kann. Das Codebeispiel veranschaulicht das Abrufen eines Zugriffstokens zum Aufrufen der Microsoft Graph-API oder einer beliebigen Web-API.

Eine Abbildung finden Sie unter [Funktionsweise des Beispiels](#how-the-sample-works).

## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement ([kostenloses Azure-Abonnement erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F))
* [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download) oder ein anderer Code-Editor

#### <a name="step-1-configure-your-application-in-the-azure-portal"></a>Schritt 1: Konfigurieren Ihrer Anwendung im Azure-Portal

Für die Codebeispiele ist der **Umleitungs-URI** `http://localhost:3000/` erforderlich.
> [!div class="nextstepaction"]
> [Make these changes for me]() (Diese Änderungen für mich vornehmen)

> [!div class="alert alert-info"]
> ![Bereits konfiguriert](media/quickstart-v2-javascript/green-check.png): Ihre Anwendung ist mit diesen Attributen konfiguriert.

#### <a name="step-2-download-the-project"></a>Schritt 2: Herunterladen des Projekts

Ausführen des Projekts mit einem Webserver unter Verwendung von Node.js

> [!div class="nextstepaction"]
> [Laden Sie das Codebeispiel herunter](https://github.com/Azure-Samples/ms-identity-javascript-react-spa/archive/main.zip).

> [!div class="sxs-lookup"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`


#### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>Schritt 3: Ihre App ist konfiguriert und betriebsbereit
Wir haben das Projekt mit Werten der Eigenschaften ihrer App konfiguriert.

#### <a name="step-4-run-the-project"></a>Schritt 4: Ausführen des Projekts

Führen Sie das Projekt mit einem Webserver unter Verwendung von „Node.js“ aus:

1. Führen Sie im Projektverzeichnis die folgenden Befehle aus, um den Server zu starten:
    ```console
    npm install
    npm start
    ```
1. Navigieren Sie zu `http://localhost:3000/`.

1. Wählen Sie **Anmelden** aus, um den Anmeldevorgang zu starten, und rufen Sie anschließend die Microsoft Graph-API auf.

    Bei der ersten Anmeldung werden Sie aufgefordert, einzuwilligen, dass die Anwendung auf Ihr Profil zugreifen und Sie anmelden darf. Klicken Sie nach erfolgreicher Anmeldung auf **Request Profile Information** (Profilinformationen anfordern), um Ihre Benutzerprofilinformationen auf der Seite anzuzeigen.

## <a name="more-information"></a>Weitere Informationen

### <a name="how-the-sample-works"></a>Funktionsweise des Beispiels

![Diagramm, in dem der Autorisierungscodeflow für eine Single-Page-Webanwendung dargestellt ist.](media/quickstart-v2-javascript-auth-code/diagram-01-auth-code-flow.png)

### <a name="msaljs"></a>msal.js

Über die MSAL.js-Bibliothek werden Benutzer angemeldet und die Token angefordert, die für den Zugriff auf eine durch Microsoft Identity Platform geschützte API verwendet werden.

Wenn Sie „Node.js“ installiert haben, können Sie die aktuelle Version auch mithilfe von Node.js-Paket-Manager (npm) herunterladen:

```console
npm install @azure/msal-browser @azure/msal-react
```

## <a name="next-steps"></a>Nächste Schritte

Probieren Sie als Nächstes ein schrittweises Tutorial aus, um zu erfahren, wie Sie eine React-SPA von Grund auf neu erstellen, die Benutzer anmeldet und die Microsoft Graph-API aufruft, um Benutzerprofildaten abzurufen:

> [!div class="nextstepaction"]
> [Tutorial: Anmelden von Benutzern und Aufrufen von Microsoft Graph](tutorial-v2-react.md)
