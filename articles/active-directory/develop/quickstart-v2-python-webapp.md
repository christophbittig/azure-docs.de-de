---
title: 'Schnellstart: Hinzufügen von „Mit Microsoft anmelden“ zu einer Python-Web-App | Azure'
titleSuffix: Microsoft identity platform
description: In dieser Schnellstartanleitung erfahren Sie, wie aus einer Python-Web-App Benutzer angemeldet werden und ein Zugriffstoken aus der Microsoft Identity Platform abgerufen und die Microsoft Graph-API aufgerufen wird.
services: active-directory
author: abhidnya13
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: portal
ms.workload: identity
ms.date: 11/22/2021
ms.author: abpati
ms.custom: aaddev, devx-track-python, "scenarios:getting-started", "languages:Python", mode-api
ms.openlocfilehash: e108c50cd83126c1dc16f99287f3b41f48598c43
ms.sourcegitcommit: 34d047300d800cf6ff7d9dd3e573a0d785f61abc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/12/2022
ms.locfileid: "135925812"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-a-python-web-app"></a>Schnellstart: Hinzufügen von „Mit Microsoft anmelden“ zu einer Python-Web-App

In dieser Schnellstartanleitung laden Sie ein Codebeispiel herunter und führen es aus, das zeigt, wie eine Python-Webanwendung Benutzer anmelden und ein Zugriffstoken abrufen kann, um die Microsoft Graph-API aufzurufen. Benutzer mit einem persönlichen Microsoft-Konto oder einem Konto in einer beliebigen Azure AD-Organisation (Azure Active Directory) können sich bei der Anwendung anmelden.

Eine Abbildung finden Sie unter [Funktionsweise des Beispiels](#how-the-sample-works).

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Python 2.7+](https://www.python.org/downloads/release/python-2713) oder [Python 3+](https://www.python.org/downloads/release/python-364/)
- [Flask](http://flask.pocoo.org/), [Flask-Sitzung](https://pypi.org/project/Flask-Session/), [Anforderungen](https://requests.kennethreitz.org/en/master/)
- [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python)

#### <a name="step-1-configure-your-application-in-azure-portal"></a>Schritt 1: Konfigurieren Ihrer Anwendung im Azure-Portal

Damit das Codebeispiel in dieser Schnellstartanleitung funktioniert, müssen folgende Schritte ausgeführt werden:

1. Fügen Sie eine Antwort-URL als `http://localhost:5000/getAToken` hinzu.
1. Erstellen Sie einen geheimen Clientschlüssel.
1. Fügen Sie die delegierte Berechtigung User.ReadBasic.All der Microsoft Graph-API hinzu.

> [!div class="nextstepaction"]
> [Make these changes for me]() (Diese Änderungen für mich vornehmen)

> [!div class="alert alert-info"]
> ![Bereits konfiguriert](./media/quickstart-v2-aspnet-webapp/green-check.png): Ihre Anwendung ist mit diesem Attribut konfiguriert.

#### <a name="step-2-download-your-project"></a>Schritt 2: Herunterladen Ihres Projekts

Laden Sie das Projekt herunter, und extrahieren Sie die ZIP-Datei in einen lokalen Ordner, der sich näher am Stammordner befindet (beispielsweise **C:\Azure-Samples**).
> [!div class="nextstepaction"]
> [Laden Sie das Codebeispiel herunter](https://github.com/Azure-Samples/ms-identity-python-webapp/archive/master.zip).

> [!NOTE]
> `Enter_the_Supported_Account_Info_Here`

#### <a name="step-3-run-the-code-sample"></a>Schritt 3: Ausführen des Codebeispiels

1. Sie müssen die MSAL Python-Bibliothek, das Flask-Framework, Flask-Sitzungen für die serverseitige Sitzungsverwaltung und Anforderungen mit PIP wie folgt installieren:

    ```shell
    pip install -r requirements.txt
    ```

2. Führen Sie `app.py` über die Shell oder die Befehlszeile aus:

    ```shell
    python app.py
    ```

   > [!IMPORTANT]
   > Für die Anwendung in dieser Schnellstartanleitung wird ein Clientgeheimnis verwendet, um sich selbst als vertraulicher Client zu identifizieren. Da das Clientgeheimnis Ihren Projektdateien als Nur-Text hinzugefügt wird, wird aus Sicherheitsgründen empfohlen, ein Zertifikat anstelle eines Clientgeheimnisses zu verwenden, bevor die Anwendung als Produktionsanwendung eingestuft wird. Weitere Informationen zur Verwendung eines Zertifikats finden Sie in [diesen Anweisungen](./active-directory-certificate-credentials.md).

## <a name="more-information"></a>Weitere Informationen

### <a name="how-the-sample-works"></a>Funktionsweise des Beispiels
![Zeigt, wie die in diesem Schnellstart generierte Beispiel-App funktioniert](media/quickstart-v2-python-webapp/python-quickstart.svg)

### <a name="getting-msal"></a>Abrufen von MSAL
MSAL ist die Bibliothek zum Anmelden von Benutzern und Anfordern von Token, die für den Zugriff auf eine durch Microsoft Identity Platform geschützte API verwendet wird.
Sie können Ihrer Anwendung MSAL Python mithilfe von Pip hinzufügen.

```Shell
pip install msal
```

### <a name="msal-initialization"></a>MSAL-Initialisierung
Sie können den Verweis auf MSAL Python hinzufügen, indem Sie den folgenden Code am Anfang der Datei hinzufügen, in der Sie MSAL verwenden möchten:

```Python
import msal
```

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über Web-Apps, aus denen Benutzer in unserer mehrteiligen Szenarioreihe angemeldet werden.

> [!div class="nextstepaction"]
> [Szenario: Web-App, die Benutzer anmeldet](scenario-web-app-sign-user-overview.md)
