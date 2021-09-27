---
title: AD FS-Unterstützung (MSAL für Java)
titleSuffix: Microsoft identity platform
description: Erfahren Sie mehr über die Unterstützung der Active Directory-Verbunddienste (AD FS) in der Microsoft-Authentifizierungsbibliothek für Java (Microsoft Authentication Library for Java, MSAL4j).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/21/2019
ms.author: marsma
ms.reviewer: nacanuma
ms.custom: aaddev, devx-track-java
ms.openlocfilehash: 9eea63573d2da462d055a784353a18bcee822998
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128601157"
---
# <a name="active-directory-federation-services-support-in-msal-for-java"></a>Unterstützung von Active Directory-Verbunddienste in MSAL für Java

Mithilfe von Active Directory-Verbunddienste (AD FS) in Windows Server können Sie Ihren MSAL für Java-Apps die auf OpenID Connect und OAuth 2.0 basierende Authentifizierung und Autorisierung hinzufügen. Nach der Integration kann Ihre App Benutzer in AD FS über einen Verbund mit Azure AD authentifizieren. Weitere Informationen zu den Szenarien finden Sie unter [AD FS-Szenarien für Entwickler](/windows-server/identity/ad-fs/ad-fs-development).

Eine App, die MSAL für Java verwendet, kommuniziert mit Azure Active Directory (Azure AD), das dann einen Verbund mit AD FS bildet.

MSAL für Java stellt eine Verbindung mit Azure AD her, das Benutzer anmeldet, die in Azure AD verwaltet werden (verwaltete Benutzer) oder von einem anderen Identitätsanbieter, z. B. AD FS (Verbundbenutzer). MSAL für Java erkennt nicht, ob ein Benutzer zu einem Verbund gehört. Es kommuniziert lediglich mit Azure AD.

Die in diesem Fall verwendete [Autorität](msal-client-application-configuration.md#authority) ist die übliche Autorität (Hostname der Autorität + „tenant“, „common“ oder „organizations“).

## <a name="acquire-a-token-interactively-for-a-federated-user"></a>Interaktives Abrufen eines Tokens für einen Verbundbenutzer

Wenn Sie `ConfidentialClientApplication.AcquireToken()` oder `PublicClientApplication.AcquireToken()` mit `AuthorizationCodeParameters` oder `DeviceCodeParameters` aufrufen, verläuft die Benutzeranmeldung normalerweise wie folgt:

1. Der Benutzer gibt seine Konto-ID ein.
2. Azure AD zeigt kurz „Weiterleitung zur Anmeldeseite Ihrer Organisation“ an, und der Benutzer wird zur Anmeldeseite des Identitätsanbieters umgeleitet. Die Anmeldeseite ist in der Regel angepasst und weist das Logo der Organisation auf.

Die unterstützten AD FS-Versionen in diesem Verbundszenario lauten wie folgt:
- Active Directory-Verbunddienste FS v2
- Active Directory-Verbunddienste v3 (Windows Server 2012 R2)
- Active Directory-Verbunddienste v4 (AD FS 2016)

## <a name="acquire-a-token-via-username-and-password"></a>Abrufen eines Tokens mittels Benutzername und Kennwort

Beim Abrufen eines Tokens mit `ConfidentialClientApplication.AcquireToken()` oder `PublicClientApplication.AcquireToken()` mit `IntegratedWindowsAuthenticationParameters` oder `UsernamePasswordParameters` ruft MSAL für Java den zu kontaktierenden Identitätsanbieter anhand des Benutzernamens ab. MSAL für Java ruft ein [SAML 1.1-Token](reference-saml-tokens.md) vom Identitätsanbieter ab, das es dann Azure AD bereitstellt, das wiederum das JSON Web Token (JWT) zurückgibt.

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Verbundfall finden Sie unter [Konfigurieren des Verhaltens der Azure Active Directory-Anmeldung für eine Anwendung mit einer Richtlinie für die Startbereichsermittlung (Home Realm Discovery, HDR)](../manage-apps/configure-authentication-for-federated-users-portal.md).