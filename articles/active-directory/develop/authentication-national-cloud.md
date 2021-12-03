---
title: Azure AD-Authentifizierung und nationale Clouds | Azure
titleSuffix: Microsoft identity platform
description: Hier erhalten Sie Informationen zu App-Registrierungs- und Authentifizierungsendpunkten für nationale Clouds.
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/03/2021
ms.author: negoe
ms.reviewer: marsma, negoe,celested
ms.custom: aaddev,references_regions
ms.openlocfilehash: bc62e3a4ae32cf4b2d0a63dd15bdab24ac490d1e
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131564535"
---
# <a name="national-clouds"></a>Nationale Clouds

Nationale Clouds (auch als Sovereign Clouds bezeichnet) sind physisch isolierte Azure-Instanzen. Diese Regionen von Azure sollen dafür sorgen, dass Anforderungen an Datenresidenz, Datenhoheit und Compliance innerhalb geografischer Grenzen erfüllt werden.

Einschließlich der globalen Cloud wird Azure Active Directory (Azure AD) in den folgenden nationalen Clouds bereitgestellt:

- Azure Government
- Azure China 21Vianet
- Azure Deutschland ([Geschlossen am 29. Oktober 2021](https://www.microsoft.com/cloud-platform/germany-cloud-regions)). Erfahren Sie mehr zu [Migration Azure Deutschland](#azure-germany-microsoft-cloud-deutschland).

Die einzelnen nationalen Clouds und die globale Azure-Cloud sind Cloud-_Instanzen_. Jede Cloudinstanz ist von den anderen getrennt und verfügt über eine eigene Umgebung und _Endpunkte_. Cloudspezifische Endpunkte umfassen OAuth 2.0-Zugriffstoken und OpenID Connect ID-Token-Anforderungsendpunkte sowie URLs für die App-Verwaltung und -Bereitstellung, z.B. das Azure-Portal.

Verwenden Sie beim Entwickeln Ihrer Apps die Endpunkte für die Cloudinstanz, in der Sie die Anwendung bereitstellen wollen.

## <a name="app-registration-endpoints"></a>App-Registrierungsendpunkte

Es gibt für jede nationale Cloud ein separates Azure-Portal. Um Anwendungen mit der Microsoft Identity Platform in eine nationale Cloud zu integrieren, müssen Sie Ihre Anwendung jeweils separat im speziellen Azure-Portal für die Umgebung registrieren.

Die folgende Tabelle enthält die Basis-URLs für die Azure AD-Endpunkte, die zum Registrieren einer Anwendung für die einzelnen nationalen Clouds verwendet werden.

| Nationale Cloud                          | Azure-Portal-Endpunkt      |
| --------------------------------------- | -------------------------- |
| Azure-Portal für US-Regierungsbehörden          | `https://portal.azure.us`  |
| Azure-Portal China, betrieben von 21Vianet | `https://portal.azure.cn`  |
| Azure-Portal (globaler Dienst)           | `https://portal.azure.com` |

## <a name="application-endpoints"></a>Anwendungsendpunkte

Sie können die Authentifizierungsendpunkte für Ihre Anwendung im Azure-Portal ermitteln.

1. Melden Sie sich beim <a href="https://portal.azure.com/" target="_blank">Azure-Portal</a> an.
1. Wählen Sie **Azure Active Directory** aus.
1. Wählen Sie unter **Verwalten** die Option **App-Registrierungen** und dann **Endpunkte** im oberen Menü aus.

   Die Seite **Endpunkte** wird angezeigt, auf der die Authentifizierungsendpunkte für Ihre im Azure AD Mandanten registrierte Anwendung angezeigt werden.

   Verwenden Sie den Endpunkt, der dem Authentifizierungsprotokoll entspricht, das Sie zusammen mit der **Anwendungs-ID (Client-ID)** zum Erstellen der spezifischen Authentifizierungsanforderung für Ihre Anwendung verwenden.

## <a name="azure-ad-authentication-endpoints"></a>Azure AD-Authentifizierungsendpunkte

Alle nationalen Clouds authentifizieren Benutzer in jeder Umgebung separat und verfügen über separate Authentifizierungsendpunkte.

Die folgende Tabelle enthält die Basis-URLs für die Azure AD-Endpunkte, die zum Abrufen von Token für die einzelnen nationalen Clouds verwendet werden.

| Nationale Cloud                      | Azure AD-Authentifizierungsendpunkt           |
| ----------------------------------- | ------------------------------------------ |
| Azure AD für US-Regierungsbehörden          | `https://login.microsoftonline.us`         |
| Azure AD China, betrieben von 21Vianet | `https://login.partner.microsoftonline.cn` |
| Azure AD (globaler Dienst)           | `https://login.microsoftonline.com`        |

Anforderungen an die Autorisierungs- oder Tokenendpunkte von Azure AD können mit der entsprechenden regionsspezifischen Basis-URL generiert werden. Beispielsweise, für globales Azure:

- Der allgemeine Autorisierungsendpunkt ist `https://login.microsoftonline.com/common/oauth2/v2.0/authorize`.
- Der allgemeine Tokenendpunkt ist `https://login.microsoftonline.com/common/oauth2/v2.0/token`.

Ersetzen Sie bei Anwendungen mit einem Mandanten in den oben genannten URLs „common“ durch die ID oder den Namen Ihres Mandanten. z. B. `https://login.microsoftonline.com/contoso.com`.

## <a name="azure-germany-microsoft-cloud-deutschland"></a>Azure Deutschland (Microsoft Cloud Deutschland)

Wenn Sie Ihre Anwendung noch nicht aus Azure Deutschland migriert haben, befolgen Sie die [Informationen zum Azure Active Directory für die Migration aus Azure Deutschland](/microsoft-365/enterprise/ms-cloud-germany-transition-azure-ad), um zu beginnen.

## <a name="microsoft-graph-api"></a>Microsoft Graph-API

Informationen zum Aufrufen der Microsoft Graph-APIs in der Umgebung der nationalen Cloud finden Sie unter [Microsoft Graph in nationalen Cloudbereitstellungen](/graph/deployments).

Einige Dienste und Funktionen in der globalen Azure-Cloud sind möglicherweise in anderen Cloudinstanzen wie den nationalen Clouds nicht verfügbar.

Informationen dazu, welche Dienste und Funktionen in einer bestimmten Cloudinstanz verfügbar sind, finden Sie unter [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/?products=all&regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia,china-non-regional,china-east,china-east-2,china-north,china-north-2,germany-non-regional,germany-central,germany-northeast).

Um zu erfahren, wie Sie eine Anwendung mithilfe der Microsoft Identity Plattform erstellen, folgen Sie der [Einzelseitenanwendung (SPA) mithilfe des Tutorials zum Authentifizierungscodeflow](tutorial-v2-angular-auth-code.md). Speziell mit dieser App wird ein Benutzer angemeldet und ein Zugriffstoken zum Aufrufen der Microsoft Graph-API abgerufen.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Sie [Microsoft Authentication Library (MSAL) in einer nationalen Cloudumgebung](msal-national-cloud.md) verwenden.

Dokumentation zur nationalen Cloud:

- [Azure Government](../../azure-government/index.yml)
- [Azure China 21Vianet](/azure/china/)
- [Azure Deutschland (Geschlossen am 29. Oktober 2021)](../../germany/index.yml)
