---
title: Verwenden von MSAL.js mit Azure AD B2C
titleSuffix: Microsoft identity platform
description: Die Microsoft Authentication Library für JavaScript (MSAL.js) ermöglicht es Anwendungen, mit Azure AD B2C zusammenzuarbeiten und Token zum Aufrufen gesicherter Web-APIs abzurufen. Bei diesen Web-APIs kann es sich um Microsoft Graph, andere Microsoft-APIs, Web-APIs von Dritten oder um Ihre eigene Web-API handeln.
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 06/05/2020
ms.author: negoe
ms.reviewer: nacanuma
ms.custom: aaddev devx-track-js
ms.openlocfilehash: 1aa07a4135452ae4303ecfee410b840cc75efffb
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122339192"
---
# <a name="use-the-microsoft-authentication-library-for-javascript-to-work-with-azure-ad-b2c"></a>Verwenden der Microsoft Authentication Library für JavaScript für die Zusammenarbeit mit Azure AD B2C

Mit der [Microsoft Authentication Library für JavaScript (MSAL.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js) können JavaScript-Entwickler Benutzern die Authentifizierung mit Social-Media- und lokalen Identitäten über [Azure Active Directory B2C (Azure AD B2C)](../../active-directory-b2c/overview.md) ermöglichen.

Wenn Sie Azure AD B2C als Identitätsverwaltungsdienst verwenden, können Sie anpassen und steuern, wie Kunden sich registrieren, anmelden und ihre Profile verwalten, wenn sie Ihre Anwendungen verwenden. 

Mit Azure AD B2C können Sie auch die Benutzeroberfläche, die von Ihrer Anwendung während des Authentifizierungsvorgangs angezeigt wird, mit Ihrer Marke versehen und anpassen.

## <a name="supported-app-types-and-scenarios"></a>Unterstützte App-Typen und Szenarien

MSAL.js ermöglicht [Single-Page-Webanwendungen](../../active-directory-b2c/application-types.md#single-page-applications) die Anmeldung von Benutzern mit Azure AD B2C unter Verwendung des [Autorisierungscodeflows mit PKCE](../../active-directory-b2c/authorization-code-flow.md). Mit MSAL.js und Azure AD B2C:

- Benutzer **können** sich mit ihren Social Media- und ihren lokalen Identitäten authentifizieren.
- Benutzer **können** für den Zugriff auf durch Azure AD B2C geschützte Ressourcen autorisiert werden (aber nicht für durch Azure AD geschützte Ressourcen).
- Benutzern **ist es nicht möglich**, mithilfe von [delegierten Berechtigungen](./v2-permissions-and-consent.md#permission-types) Token für Microsoft-APIs (z. B. für die MS Graph-API) abzurufen.
- Benutzer mit Administratorberechtigungen **können** mithilfe von [delegierten Berechtigungen](./v2-permissions-and-consent.md#permission-types) Token für Microsoft-APIs (z. B. für die MS Graph-API) abrufen.

Weitere Informationen finden Sie unter [Arbeiten mit Azure AD B2C](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-browser/docs/working-with-b2c.md).

## <a name="next-steps"></a>Nächste Schritte

Arbeiten Sie die folgenden Tutorials durch:

- [Anmelden von Benutzern mit Azure AD B2C in einer Single-Page-Webanwendung](../../active-directory-b2c/configure-authentication-sample-spa-app.md)
- [Aufrufen einer durch Azure AD B2C geschützten Web-API](../../active-directory-b2c/enable-authentication-web-api.md)