---
title: 'Microsoft Identity Platform: Aktualisierungstoken | Azure'
titleSuffix: Microsoft identity platform
description: Hier erfahren Sie mehr über Aktualisierungstoken, die von der Azure AD ausgegeben werden.
services: active-directory
author: SHERMANOUKO
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 05/25/2021
ms.author: shermanouko
ms.reviewer: mmacy, hirsin
ms.custom: aaddev, identityplatformtop40, fasttrack-edit
ms.openlocfilehash: e4d2721905d1e344cd0825466e0b0eb08578ef47
ms.sourcegitcommit: 63f3fc5791f9393f8f242e2fb4cce9faf78f4f07
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/26/2021
ms.locfileid: "114688123"
---
# <a name="microsoft-identity-platform-refresh-tokens"></a>Microsoft Identity Platform: Aktualisierungstoken

Wenn ein Client ein Zugriffstoken für den Zugriff auf eine geschützte Ressource abruft, erhält er auch ein Aktualisierungstoken. Das Aktualisierungstoken wird verwendet, um neue Zugriffs-/Aktualisierungstoken-Paare abzurufen, wenn das aktuelle Zugriffstoken abläuft. Aktualisierungstoken werden auch verwendet, um zusätzliche Zugriffstoken für andere Ressourcen abzurufen. Aktualisierungstoken sind an eine Kombination aus Benutzer und Client gebunden, aber nicht an eine Ressource oder einen Mandanten. Dadurch kann ein Client mithilfe eines Aktualisierungstoken Zugriffstoken für eine beliebige Kombination von Ressourcen und Mandanten beziehen, für die er berechtigt ist. Aktualisierungstoken werden verschlüsselt und können nur von Microsoft Identity Platform gelesen werden.

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie sich diesen Artikel durchlesen, sollten Sie die folgenden Artikel durchgehen:

* Microsoft Identity Platform – [ID-Token](id-tokens.md)
* Microsoft Identity Platform-[Zugriffstoken](access-tokens.md)

## <a name="refresh-token-lifetime"></a>Lebensdauer von Aktualisierungstoken

Aktualisierungstoken haben eine längere Gültigkeitsdauer als Zugriffstoken. Die Standardlebensdauer für die Token beträgt 90 Tage,und sie ersetzen sich selbst bei jeder Verwendung durch ein neues Token. Das bedeutet, dass immer dann, wenn ein Aktualisierungstoken zum Beziehen eines neuen Zugriffstokens verwendet wird, auch ein neues Aktualisierungstoken ausgestellt wird. Microsoft Identity Platform widerruft keine alten Aktualisierungstoken, wenn damit neue Zugriffstoken abgerufen werden. Löschen Sie das alte Aktualisierungstoken dauerhaft, nachdem Sie ein neues Token abgerufen haben. Aktualisierungstoken müssen wie Zugriffstoken oder Anwendungsanmeldeinformationen sicher gespeichert werden. 

## <a name="refresh-token-expiration"></a>Ablauf von Aktualisierungstoken

Aktualisierungstoken können aufgrund von Timeouts und Sperrungen jederzeit widerrufen werden. Ihre App muss in diesem Fall Ablehnungen durch den Anmeldedienst ordnungsgemäß behandeln. Dazu wird der Benutzer an eine interaktive Anmeldeaufforderung zur erneuten Anmeldung weitergeleitet. 

### <a name="token-timeouts"></a>Tokenzeitüberschreitungen

Sie können die Gültigkeitsdauer eines Aktualisierungstokens nicht konfigurieren. Sie können ihre Gültigkeitsdauer nicht verkürzen oder verlängern. Konfigurieren Sie die Anmeldehäufigkeit in „Bedingter Zugriff“, um die Zeiträume festzulegen, bevor sich ein Benutzer erneut anmelden muss. Erfahren Sie mehr zum [Konfigurieren der Verwaltung von Authentifizierungssitzungen mit bedingtem Zugriff](../conditional-access/howto-conditional-access-session-lifetime.md).

Nicht alle Aktualisierungstoken folgen den Regeln, die in der Richtlinie für die Tokengültigkeitsdauer festgelegt sind. Insbesondere in [Single-Page-Apps](reference-third-party-cookies-spas.md) verwendete Aktualisierungstoken sind stets auf 24 Stunden Aktivität festgelegt, als würde für sie eine `MaxAgeSessionSingleFactor`-Richtlinie von 24 Stunden gelten. 

### <a name="revocation"></a>Widerruf

Aktualisierungstoken können vom Server aufgrund einer Änderung der Anmeldeinformationen oder aufgrund einer Benutzeraktion oder Administratoraktion widerrufen werden.  Aktualisierungstoken werden in zwei Klassen unterteilt: Token, die für vertrauliche Clients ausgestellt werden (die Spalte ganz rechts), und Token, die für öffentliche Clients (alle anderen Spalten) ausgestellt werden.

| Change | Kennwortbasiertes Cookie | Kennwortbasiertes Token | Nicht kennwortbasiertes Cookie | Nicht kennwortbasiertes Token | Vertrauliches Clienttoken |
|---|-----------------------|----------------------|---------------------------|--------------------------|---------------------------|
| Kennwort läuft ab | Bleibt aktiv | Bleibt aktiv | Bleibt aktiv | Bleibt aktiv | Bleibt aktiv |
| Kennwort wird vom Benutzer geändert | Widerrufen | Widerrufen | Bleibt aktiv | Bleibt aktiv | Bleibt aktiv |
| Benutzer verwendet SSPR | Widerrufen | Widerrufen | Bleibt aktiv | Bleibt aktiv | Bleibt aktiv |
| Administrator setzt Kennwort zurück | Widerrufen | Widerrufen | Bleibt aktiv | Bleibt aktiv | Bleibt aktiv |
| Benutzer widerruft Aktualisierungstoken [über PowerShell](/powershell/module/azuread/revoke-azureadsignedinuserallrefreshtoken) | Widerrufen | Widerrufen | Widerrufen | Widerrufen | Widerrufen |
| Administrator widerruft alle Aktualisierungstoken für einen Benutzer [über PowerShell](/powershell/module/azuread/revoke-azureaduserallrefreshtoken) | Widerrufen | Widerrufen |Widerrufen | Widerrufen | Widerrufen |
| Einmaliges Abmelden [im Web](v2-protocols-oidc.md#single-sign-out) | Widerrufen | Bleibt aktiv | Widerrufen | Bleibt aktiv | Bleibt aktiv |

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie mehr über die [konfigurierbare Tokengültigkeitsdauer](active-directory-configurable-token-lifetimes.md).
* Weitere Informationen zu primären Aktualisierungstoken finden Sie unter [Was ist ein primäres Aktualisierungstoken (Primary Refresh Token, PRT)?](../devices/concept-primary-refresh-token.md).
