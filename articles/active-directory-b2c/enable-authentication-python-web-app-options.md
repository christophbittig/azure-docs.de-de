---
title: Aktivieren von Python-Webanwendungsoptionen mit Azure Active Directory B2C
description: In diesem Artikel erfahren Sie, wie Sie die Verwendung von Python-Webanwendungsoptionen aktivieren.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 07/05/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: 155e09cc0aeda6e9a93358f25ca44d852fd34ea7
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "130041061"
---
# <a name="enable-authentication-options-in-a-python-web-app-by-using-azure-ad-b2c"></a>Aktivieren von Authentifizierungsoptionen in einer Python-Web-App mit Azure AD B2C 

In diesem Artikel werden die Möglichkeiten beschrieben, wie Sie die Authentifizierungsfunktionalität von Azure Active Directory B2C (Azure AD B2C) für Ihre Python-Webanwendung aktivieren, anpassen und verbessern können. 

Bevor Sie beginnen, ist es wichtig, sich mit dem [Konfigurieren der Authentifizierung in einer Python-Beispiel-Web-App mithilfe von Azure AD B2C](configure-authentication-sample-python-web-app.md) vertraut machen.

[!INCLUDE [active-directory-b2c-app-integration-custom-domain](../../includes/active-directory-b2c-app-integration-custom-domain.md)]

So verwenden Sie eine benutzerdefinierte Domäne und Ihre Mandanten-ID in der Authentifizierungs-URL: 

1. Befolgen Sie die Anleitung unter [Das Aktivieren benutzerdefinierter Domänen](custom-domain.md).
1. Aktualisieren Sie das Klassenmitglied `authority_template` mit Ihrer benutzerdefinierten Domäne in der Datei *app_config.py* den.

Die folgende Python-Code zeigt die App-Einstellungen vor der Änderung:

```python
authority_template = "https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{user_flow}"
```

Die folgende Python-Code zeigt die App-Einstellungen nach der Änderung:

```python
authority_template = "https://custom.domain.com/00000000-0000-0000-0000-000000000000/{user_flow}" 
```

[!INCLUDE [active-directory-b2c-app-integration-login-hint](../../includes/active-directory-b2c-app-integration-login-hint.md)]

1. Wenn Sie eine benutzerdefinierte Richtlinie verwenden, fügen Sie den erforderlichen Eingabeanspruch hinzu, wie unter dem Verfahren [Einrichten der direkten Anmeldung](direct-signin.md#prepopulate-the-sign-in-name) beschrieben. 
1. Suchen Sie nach der `initiate_auth_code_flow`-Methode und fügen Sie dann den `login_hint`Parameter mit dem Domänennamen des Identitätsanbieters hinzu (z. B. *facebook.com*).

```python
def _build_auth_code_flow(authority=None, scopes=None):
    return _build_msal_app(authority=authority).initiate_auth_code_flow(
        scopes or [],
        redirect_uri=url_for("authorized", _external=True),
        login_hint="bob@contoso.com")
```

[!INCLUDE [active-directory-b2c-app-integration-domain-hint](../../includes/active-directory-b2c-app-integration-domain-hint.md)]

1. Überprüfen Sie den Domänennamen Ihres externen Identitätsanbieters. Weitere Informationen finden Sie unter [Umleiten einer Anmeldung zu einem Anbieter sozialer Netzwerke](direct-signin.md#redirect-sign-in-to-a-social-provider). 
1. Suchen Sie die `initiate_auth_code_flow`-Methode und fügen Sie dann den Parameter `domain_hint` mit dem Anmeldehinweis hinzu.

    ```python
    def _build_auth_code_flow(authority=None, scopes=None):
        return _build_msal_app(authority=authority).initiate_auth_code_flow(
            scopes or [],
            redirect_uri=url_for("authorized", _external=True),
            domain_hint="facebook.com")
    ```


## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen finden Sie unter [MSAL für Python-Konfigurationsoptionen](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki).
