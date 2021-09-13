---
title: Migrieren zur Microsoft Authentication Library (MSAL)
titleSuffix: Microsoft identity platform
description: Erfahren Sie mehr über die Unterschiede zwischen der Microsoft Authentication Library (MSAL) und Azure AD Authentication Library (ADAL) und deren Migration zu MSAL.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/22/2021
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev, has-adal-ref
ms.openlocfilehash: 145302d3569c2fc9c5d5bd58a5f9b7ccefac0b8e
ms.sourcegitcommit: 34aa13ead8299439af8b3fe4d1f0c89bde61a6db
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/18/2021
ms.locfileid: "122418522"
---
# <a name="migrate-applications-to-the-microsoft-authentication-library-msal"></a>Migrieren von Anwendungen zur Microsoft Authentication Library (MSAL)

Wenn eine Ihrer Anwendungen die Azure Active Directory-Authentifizierungsbibliothek (Active Directory Authentication Library, ADAL) für Authentifizierungs- und Autorisierungsfunktionen verwendet, ist es an der Zeit, sie zur [Microsoft Authentication Library (MSAL)](msal-overview.md#languages-and-frameworks) zu migrieren.

- Microsoft beendet Support und Entwicklung für die ADAL, einschließlich Sicherheitskorrekturen, am 30. Juni 2022.
- Der ADAL wurden seit dem 30. Juni 2020 keine neuen Features mehr hinzugefügt.

> [!WARNING]
> Wenn Sie sich dagegen entscheiden, zur MSAL zu migrieren, bevor der ADAL-Support am 30. Juni 2022 endet, riskieren Sie die Sicherheit Ihrer Apps. Vorhandene Apps, die die ADAL verwenden, funktionieren nach dem Supportendedatum weiterhin, aber Microsoft veröffentlicht für die ADAL keine Sicherheitskorrekturen mehr.

## <a name="why-switch-to-msal"></a>Gründe für den Wechsel zur MSAL

Die MSAL bietet gegenüber der ADAL mehrere Vorteile, einschließlich der folgenden Features: 

|Features|MSAL|ADAL|
|---------|---------|---------|
|**Security**|||
|Sicherheitskorrekturen nach dem 30. Juni 2022|![Sicherheitskorrekturen nach dem 30. Juni 2022: MSAL bietet das Feature][y]|![Sicherheitskorrekturen nach dem 30. Juni 2022: ADAL bietet das Feature nicht][n]|
| Proaktives Aktualisieren und Widerrufen von Token basierend auf Richtlinien- oder kritischen Ereignissen für Microsoft Graph und andere APIs, die [fortlaufende Zugriffsevaluierung (Continuous Access Evaluation, CAE)](app-resilience-continuous-access-evaluation.md) unterstützen.|![Proaktives Aktualisieren und Widerrufen von Token basierend auf Richtlinien- oder kritischen Ereignissen für Microsoft Graph und andere APIs, die fortlaufende Zugriffsevaluierung (Continuous Access Evaluation, CAE) unterstützen: MSAL bietet das Feature][y]|![Proaktives Aktualisieren und Widerrufen von Token basierend auf Richtlinien- oder kritischen Ereignissen für Microsoft Graph und andere APIs, die fortlaufende Zugriffsevaluierung (Continuous Access Evaluation, CAE) unterstützen: ADAL bietet das Feature nicht][n]|
| Standards, die mit OAuth v2.0 und OpenID Connect (OIDC) kompatibel sind |![Standards, die mit OAuth v2.0 und OpenID Connect (OIDC) kompatibel sind: MSAL bietet das Feature][y]|![Standards, die mit OAuth v2.0 und OpenID Connect (OIDC) kompatibel sind: ADAL bietet das Feature nicht][n]|
|**Benutzerkonten und -erfahrungen**|||
|Azure AD-Konten (Azure Active Directory)|![Azure AD-Konten (Azure Active Directory): MSAL bietet das Feature][y]|![Azure AD-Konten (Azure Active Directory): ADAL bietet das Feature nicht][y]|
| Microsoft-Konto (MSA) |![Microsoft-Konto (MSA): MSAL bietet das Feature][y]|![Microsoft-Konto (MSA): ADAL bietet das Feature nicht][n]|
| Azure AD B2C-Konten |![Azure AD B2C-Konten: MSAL bietet das Feature][y]|![Azure AD B2C-Konten: ADAL bietet das Feature nicht][n]|
| Beste Erfahrung beim einmaligen Anmelden |![Beste Erfahrung beim einmaligen Anmelden: MSAL bietet das Feature][y]|![Beste Erfahrung beim einmaligen Anmelden: ADAL bietet das Feature nicht][n]|
|**Resilienz**|||
| Proaktive Tokenverlängerung |![Proaktive Tokenverlängerung: MSAL bietet das Feature][y]|![Proaktive Tokenverlängerung: ADAL bietet das Feature nicht][n]|
| Drosselung |![Drosselung: MSAL bietet das Feature][y]|![Drosselung: ADAL bietet das Feature nicht][n]|

## <a name="ad-fs-support-in-msalnet"></a>AD FS-Unterstützung in MSAL.NET

Sie können MSAL.NET, MSAL Java und MSAL Python verwenden, um Token von Active Directory-Verbunddiensten (Active Directory Federation Services, AD FS) 2019 oder höher zu erhalten. Frühere Versionen von AD FS, einschließlich AD FS 2016, werden von MSAL nicht unterstützt.

Wenn Sie die Verwendung von AD FS fortsetzen müssen, sollten Sie ein Upgrade auf AD FS 2019 oder höher durchführen, bevor Sie Ihre Anwendungen von ADAL zu MSAL aktualisieren.

## <a name="how-to-migrate-to-msal"></a>Migrieren zu MSAL

Bevor Sie mit der Migration beginnen, müssen Sie ermitteln, welche Ihrer Apps ADAL für die Authentifizierung verwenden. Führen Sie die Schritte in diesem Artikel aus, um eine Liste im Azure-Portal abzurufen:
- [Gewusst wie: Abrufen einer vollständigen Liste von Apps, die in Ihrem Mandanten die ADAL verwenden](howto-get-list-of-all-active-directory-auth-library-apps.md)

Nachdem Sie Ihre Apps, die die ADAL verwenden, identifiziert haben, migrieren Sie sie wie unten dargestellt je nach Anwendungstyp zur MSAL.

[!INCLUDE [application type](includes/adal-msal-migration.md)]

## <a name="migration-help"></a>Hilfe zur Migration

Wenn Sie Fragen zur Migration Ihrer App von ADAL zu MSAL haben, finden Sie hier einige Optionen:

- Posten Sie Ihre Frage unter Verwendung des Tags `[azure-ad-adal-deprecation]` auf [Microsoft Q&A](/answers/topics/azure-ad-adal-deprecation.html).
- Öffnen Sie ein Issue im GitHub-Repository der Bibliothek. Links zum Repository der einzelnen Bibliotheken finden Sie im Übersichtsartikel zu MSAL im Abschnitt [Sprachen und Frameworks](msal-overview.md#languages-and-frameworks).

Wenn Sie Ihre Anwendung in Partnerschaft mit einem unabhängigen Softwarehersteller (Independent Software Vendor, ISV) entwickelt haben, sollten sich direkt mit ihm in Verbindung setzen, um sich über seine Migrationsjourney zu MSAL zu informieren.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur MSAL, einschließlich Nutzungsinformationen und für verschiedene Programmiersprachen und Anwendungstypen verfügbarer Bibliotheken, finden Sie unter:

- [Abrufen und Zwischenspeichern von Token mit MSAL](msal-acquire-cache-tokens.md)
- [Anwendungskonfigurationsoptionen](msal-client-application-configuration.md)
- [MSAL-Authentifizierungsbibliotheken](reference-v2-libraries.md)

<!--
 ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
-->
[y]: media/common/yes.png
[n]: media/common/no.png