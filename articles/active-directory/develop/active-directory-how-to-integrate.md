---
title: Integration in Microsoft Identity Platform | Azure
titleSuffix: Microsoft identity platform
description: Lernen Sie die Vorteile der Integration Ihrer Anwendung in Microsoft Identity Platform kennen, und erhalten Sie Featureressourcen wie vereinfachte Anmeldung, Identitätsverwaltung, Multifactor-Factor Authentication und Zugriffssteuerung.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/01/2020
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev, seoapril2019
ROBOTS: NOINDEX
ms.openlocfilehash: 57aefd8cc8a246d485854f72b47c131f75dced0c
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124734809"
---
# <a name="integrating-with-the-microsoft-identity-platform"></a>Integration in Microsoft Identity Platform

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

In diesem Artikel erfahren Sie mehr über die Vorteile der Integration Ihrer Anwendung in Microsoft Identity Platform und erhalten Ressourcen für die Integration. Microsoft Identity Platform und Azure Active Directory (Azure AD) stellen Organisationen eine Identitätsverwaltung für Cloudanwendungen auf Unternehmensniveau bereit. Die Integration in Microsoft Identity Platform bietet Ihren Benutzern eine optimale Anmeldungsumgebung und unterstützt Ihre Anwendung bei der Einhaltung von IT-Richtlinien.

## <a name="how-to-integrate"></a>Möglichkeiten der Integration

Es gibt mehrere Möglichkeiten, Ihre Anwendung in Microsoft Identity Platform zu integrieren. Nutzen Sie diese den Anforderungen Ihrer Anwendung entsprechend.

### <a name="support-the-microsoft-identity-platform-as-a-way-to-sign-in-to-your-application"></a>Unterstützen von Microsoft Identity Platform als Möglichkeit zum Anmelden bei Ihrer Anwendung

**Verringern von Anmeldeaufwand und Supportkosten.** Wenn sich Ihre Benutzer über Microsoft Identity Platform bei Ihrer Anwendung anmelden, müssen sie sich keinen weiteren Benutzernamen und kein weiteres Kennwort merken. Als Entwickler müssen Sie ein Kennwort weniger speichern und schützen. Allein dadurch, dass keine vergessenen Kennwörter zurückgesetzt werden müssen, kann es zu beträchtlichen Kosteneinsparungen kommen. Microsoft Identity Platform unterstützt die Anmeldung bei einigen der weltweit beliebtesten Cloudanwendungen (darunter auch Microsoft 365 und Microsoft Azure). Bei mehreren hundert Millionen Benutzern in Millionen von Organisationen ist es wahrscheinlich, dass Ihr Benutzer bereits bei Microsoft Identity Platform angemeldet ist. Erfahren Sie mehr über das [Hinzufügen der Unterstützung für die Anmeldung über Microsoft Identity Platform](./authentication-vs-authorization.md).

**Vereinfachen der Registrierung für Ihre Anwendung.**  Bei der Registrierung für Ihre Anwendung kann Microsoft Identity Platform wichtige Informationen zu einem Benutzer senden, sodass Sie das Anmeldeformular vorab ausfüllen oder vollständig auf eine Anmeldung verzichten können. Benutzer können sich für Ihre Anwendung mit ihrem Azure AD-Konto in einer vertrauten Genehmigungsumgebung registrieren, die derjenigen in sozialen Medien und mobilen Anwendungen ähnelt. Benutzer können sich ohne Beteiligung der IT bei einer in Microsoft Identity Platform integrierten Anwendung registrieren und anmelden. Erfahren Sie mehr über das [Registrieren Ihrer Anwendung für die Azure AD-Kontoanmeldung](../../app-service/configure-authentication-provider-aad.md).

### <a name="browse-for-users-manage-user-provisioning-and-control-access-to-your-application"></a>Suchen nach Benutzern, Verwalten der Benutzerbereitstellung und Steuern des Zugriffs auf Ihre Anwendung

**Suchen nach Benutzern im Verzeichnis.**  Über die Microsoft Graph-API können Sie Benutzern helfen, nach anderen Personen in ihrer Organisation zu suchen, wenn sie sie einladen oder ihnen Zugriff gewähren möchten, statt dass die Benutzer E-Mail-Adressen eingeben müssen. Benutzer können auf einer vertrauten Adressbuchoberfläche suchen und beispielsweise Details der Organisationshierarchie anzeigen. Weitere Informationen zur [Microsoft Graph-API](/graph/overview).

**Wiederverwenden von Active Directory-Gruppen und Verteilerlisten, die Ihr Kunde bereits verwaltet.**  Azure AD enthält die Gruppen, die Ihr Kunde bereits für die E-Mail-Verteilung und das Verwalten des Zugriffs verwendet. Verwenden Sie diese Gruppen über die Microsoft Graph-API erneut, statt dass der Kunde einen getrennten Satz von Gruppen in Ihrer Anwendung erstellen und verwalten muss. Informationen zu Gruppen können auch in Anmeldetoken an Ihre Anwendung gesendet werden. Weitere Informationen zur [Microsoft Graph-API](/graph/overview).

**Verwenden von Microsoft Identity Platform zum Steuern des Zugriffs auf Ihre Anwendung.**  Administratoren und Besitzer von Anwendungen in Azure AD können bestimmten Benutzern und Gruppen Zugriff auf Anwendungen zuweisen. Über die Microsoft Graph-API können Sie diese Liste lesen und zum Steuern der Bereitstellung bzw. der Aufhebung der Bereitstellung von Ressourcen und des Zugriffs innerhalb Ihrer Anwendung verwenden.

**Verwenden von Microsoft Identity Platform für die rollenbasierte Zugriffssteuerung.**  Administratoren und Besitzer von Anwendungen können Benutzer und Gruppen zu Rollen zuweisen, die Sie beim Registrieren Ihrer Anwendung in Microsoft Identity Platform definieren. Rolleninformationen werden in Anmeldetoken an die Anwendung gesendet und können auch über die Microsoft Graph-API gelesen werden. Erfahren Sie mehr über die [Authentifizierung mit Microsoft Identity Platform](https://cloudblogs.microsoft.com/enterprisemobility/2014/12/18/azure-active-directory-now-with-group-claims-and-application-roles/).

### <a name="get-access-to-users-profile-calendar-email-contacts-files-and-more"></a>Anfordern des Zugriffs auf das Profil, den Kalender, E-Mails, Kontakte, Dateien und andere Informationen von Benutzern

**Microsoft Identity Platform ist der Autorisierungsserver für Microsoft 365 und andere Microsoft-Unternehmensdienste.**  Wenn Sie Microsoft Identity Platform für die Anmeldung bei Ihrer Anwendung oder das Verknüpfen Ihrer aktuellen Benutzerkonten über OAuth 2.0 mit Azure AD-Benutzerkonten unterstützen, können Sie Lese- und Schreibzugriff auf das Profil, den Kalender, Kontakte, E-Mails, Dateien und andere Informationen des Benutzers anfordern. Sie können problemlos Ereignisse in den Kalender der Benutzer schreiben und Dateien auf ihrem OneDrive lesen oder schreiben. Erfahren Sie mehr über die [Microsoft 365-APIs](/graph/overview).

### <a name="promote-your-application-in-the-azure-and-microsoft-365-marketplaces"></a>Werben für Ihre Anwendung im Azure Marketplace und Microsoft 365 Marketplace

**Werben Sie für Ihre Anwendung bei Millionen von Organisationen, die Azure AD bereits verwenden.**  Benutzer, die diese Marketplaces durchsuchen bzw durchstöbern, nutzen bereits einen oder mehrere Clouddienste, sodass sie als potenzielle Kunden von Cloudlösungen in Frage kommen. Erfahren Sie mehr über das Werben für Ihre Anwendung in [Azure Marketplace](https://azure.microsoft.com/marketplace/partner-program/).

**Wenn sich Benutzer für Ihre Anwendung registrieren, wird sie in ihrem Azure AD-Zugriffsbereich und Microsoft 365-App-Startfeld angezeigt.**  Benutzer können später schnell und einfach zu Ihrer Anwendung zurückkehren, was die Benutzerbindung verbessert. Erfahren Sie mehr über den [Azure AD-Zugriffsbereich](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

### <a name="secure-device-to-service-and-service-to-service-communication"></a>Sichere Kommunikation zwischen Gerät und Dienst und zwischen Diensten

**Durch den Einsatz von Microsoft Identity Platform zur Identitätsverwaltung bei Diensten und Geräten reduziert sich der Code, den Sie schreiben müssen, und der Zugriff kann von der IT verwaltet werden.**  Dienste und Geräte können über OAuth Token von Microsoft Identity Platform abrufen und diese Token für den Zugriff auf Web-APIs verwenden. Mithilfe von Microsoft Identity Platform können Sie das Schreiben von komplexem Authentifizierungscode vermeiden. Da die Identitäten der Dienste und Geräte in Azure AD gespeichert sind, kann die IT das Verwalten und Sperren von Schlüsseln zentral verwalten, sodass diese Aufgabe in Ihrer Anwendung nicht separat erfolgen muss.

## <a name="benefits-of-integration"></a>Vorteile der Integration

Die Integration in Microsoft Identity Platform bietet den Vorteil, dass Sie keinen zusätzlichen Code schreiben müssen.

### <a name="integration-with-enterprise-identity-management"></a>Integration der Identitätsverwaltung des Unternehmens

**Optimieren Ihrer Anwendung für die Einhaltung von IT-Richtlinien.**  Organisationen integrieren ihre Identitätsverwaltungssysteme in Microsoft Identity Platform, sodass eine Person beim Verlassen des Unternehmens automatisch den Zugriff auf die Anwendung verliert, ohne dass die IT eingreifen muss. Die IT kann verwalten, wer Zugriff auf Ihre Anwendung hat und bestimmen, welche Zugriffsrichtlinien erfüllt werden müssen, z. B. Multi-Factor Authentication. Dadurch müssen Sie weniger Code schreiben, um komplexe Unternehmensrichtlinien einzuhalten. Azure AD bietet Administratoren ein detailliertes Überwachungsprotokoll dazu, wer sich bei Ihrer Anwendung angemeldet hat, damit die IT die Nutzung nachverfolgen kann.

**Azure AD erweitert Active Directory in die Cloud, damit Ihre Anwendung in Active Directory integriert werden kann.**  Viele Organisationen auf der ganzen Welt nutzen Active Directory als primäres Anmeldungs- und Identitätsverwaltungssystem und verlangen, dass ihre Anwendungen mit AD zusammenarbeiten. Durch die Integration in Azure AD wird Ihre App in Active Directory integriert.

### <a name="advanced-security-features"></a>Erweiterte Sicherheitsfunktionen

**Multi-Factor Authentication.**  Microsoft Identity Platform stellt eine native mehrstufige Authentifizierung (Multi-Factor Authentication, MFA) bereit. IT-Administratoren können für den Zugriff auf Ihre Anwendung die Multi-Factor Authentication anfordern, damit Sie diese Unterstützung nicht selbst programmieren müssen. Erfahren Sie mehr über die [Multi-Factor Authentication](https://azure.microsoft.com/documentation/services/multi-factor-authentication/).

**Erkennung anomaler Anmeldungen.**  Microsoft Identity Platform verarbeitet mehr als eine Milliarde Anmeldungen pro Tag. Dabei kommen Machine Learning-Algorithmen zum Erkennen verdächtiger Aktivitäten zum Einsatz, die IT-Administratoren über mögliche Probleme informieren. Durch die Unterstützung der Anmeldung über Microsoft Identity Platform profitiert Ihre Anwendung von diesem Schutz. Erfahren Sie mehr über das [Anzeigen des Azure Active Directory-Zugriffsberichts](../reports-monitoring/overview-reports.md).

**Bedingter Zugriff.**  Zusätzlich zur Multi-Factor Authentication können Administratoren anfordern, dass bestimmte Bedingungen erfüllt sein müssen, ehe sich Benutzer bei Ihrer Anwendung anmelden können. Bedingungen, die festgelegt werden können, sind u. a. der IP-Adressbereich von Clientgeräten, Mitgliedschaft in angegebenen Gruppen und den Status des Geräts, das für den Zugriff verwendet wird. Erfahren Sie mehr über den [bedingten Azure Active Directory-Zugriff](../conditional-access/overview.md).

### <a name="easy-development"></a>Einfache Entwicklung

**Protokolle gemäß Branchenstandard.**  Microsoft ist bestrebt, Branchenstandards zu unterstützen. Microsoft Identity Platform unterstützt die Branchenstandardprotokolle OAuth 2.0 und OpenID Connect 1.0. Erfahren Sie mehr über die [Authentifizierungsprotokolle von Microsoft Identity Platform](active-directory-v2-protocols.md).

**Open-Source-Bibliotheken.**  Microsoft bietet zur Beschleunigung der Entwicklung für gängige Sprachen und Plattformen vollständig unterstützte Open-Source-Bibliotheken. Der Quellcode wird unter Apache 2.0 lizenziert, und Sie sind eingeladen, sich an den Projekten zu beteiligen. Erfahren Sie mehr über die [Microsoft Authentication Library (MSAL)](reference-v2-libraries.md).

### <a name="worldwide-presence-and-high-availability"></a>Weltweite Präsenz und Hochverfügbarkeit

**Azure AD ist in Datencentern auf der ganzen Welt in Betrieb bereitgestellt und wird rund um die Uhr verwaltet und überwacht.**  Azure AD ist das Identitätsverwaltungssystem für Microsoft Azure und Microsoft 365 und in 28 Datencentern auf der ganzen Welt im Einsatz. Verzeichnisdaten werden garantiert in mindestens drei Datencenter repliziert. Globale Lastenausgleichsfunktionen stellen sicher, dass Benutzer auf die nächstgelegene Kopie von Azure AD mit ihren Daten zugreifen, und leiten Anforderungen automatisch an andere Datencenter weiter, sobald ein Problem erkannt wird.

## <a name="next-steps"></a>Nächste Schritte

[Erste Schritte mit dem Schreiben von Code](v2-overview.md#getting-started).

[Anmelden von Benutzern mithilfe von Microsoft Identity Platform](./authentication-vs-authorization.md)