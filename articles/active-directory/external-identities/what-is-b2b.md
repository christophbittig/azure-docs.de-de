---
title: Was ist die B2B-Zusammenarbeit in Azure Active Directory?
description: Die Azure Active Directory B2B-Zusammenarbeit unterstützt einen Gastbenutzerzugriff, um auf sichere Weise Ressourcen freizugeben und mit externen Partnern zusammenzuarbeiten.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: overview
ms.date: 07/13/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 357049d855aacbacb4b1455304e4fb84565bfb27
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/14/2021
ms.locfileid: "113759820"
---
# <a name="what-is-guest-user-access-in-azure-active-directory-b2b"></a>Was ist der Gastzugriff in Azure Active Directory-B2B?

Die B2B-Zusammenarbeit (Business-to-Business) von Azure Active Directory (Azure AD) ist ein Feature im Rahmen von externen Identitäten, mit dem Sie Gastbenutzer zur Zusammenarbeit mit Ihrer Organisation einladen können. Mithilfe der B2B-Zusammenarbeit können Sie Anwendungen und Dienste Ihres Unternehmens auf sichere Weise für Gastbenutzer aus anderen Organisationen freigeben und gleichzeitig die Kontrolle über Ihre eigenen Unternehmensdaten behalten. Arbeiten Sie sicher und geschützt mit externen Partnern zusammen, ob groß oder klein, auch wenn sie keine Azure AD oder eine IT-Abteilung haben. Ein einfacher Prozess zum Senden und Einlösen von Einladungen ermöglicht es Partnern, ihre eigenen Zugangsdaten für den Zugriff auf die Ressourcen Ihres Unternehmens zu verwenden. Entwickler können Azure AD Business-to-Business-APIs verwenden, um den Einladungsprozess anzupassen oder Anwendungen wie Self-Service-Anmeldeportale zu schreiben. Lizenz- und Preisinformationen in Verbindung mit Gastbenutzern finden Sie unter [Azure Active Directory for External Identities – Preise](https://azure.microsoft.com/pricing/details/active-directory/external-identities/).  

> [!IMPORTANT]
>
> - Wenn Azure AD B2B-Kunden ab dem **12. Juli 2021** neue Google-Integrationen für die Self-Service-Registrierung für ihre benutzerdefinierten oder branchenspezifischen Anwendungen einrichten, funktioniert die Authentifizierung mit Google-Identitäten erst, wenn Authentifizierungen in die Systemwebansichten verschoben werden. [Weitere Informationen](google-federation.md#deprecation-of-web-view-sign-in-support)
> - Ab dem **30. September 2021** wird Google die [Unterstützung für die Anmeldung in der eingebetteten Webansicht einstellen](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html). Wenn Ihre Apps Benutzer mit einer eingebetteter Webansicht authentifizieren und Sie den Google-Verbund mit [ Azure AD B2C](../../active-directory-b2c/identity-provider-google.md) oder Azure AD B2B für [externe Benutzereinladungen](google-federation.md) oder die [Self-Service-Registrierung](identity-providers.md) verwenden, werden sich Google Gmail-Benutzer nicht authentifizieren können. [Weitere Informationen](google-federation.md#deprecation-of-web-view-sign-in-support)
> - **Ab Oktober 2021** wird das Einlösen von Einladungen durch die Erstellung von nicht verwalteten Azure AD-Konten und -Mandanten für B2B Collaboration-Szenarien von Microsoft nicht mehr unterstützt. Zur Vorbereitung hierauf wird Kunden die allgemein verfügbare [Authentifizierung mit Einmalkennung per E-Mail](one-time-passcode.md) empfohlen.

## <a name="collaborate-with-any-partner-using-their-identities"></a>Zusammenarbeit mit jedem Partner über seine Identitäten

Mit Azure AD-B2B verwendet der Partner seine eigene Identitätsverwaltungslösung, sodass es keinen externen Verwaltungsaufwand für Ihr Unternehmen gibt. Gastbenutzer können sich mit ihrem Geschäfts-, Schul- oder Unikonto bzw. mit ihrer Identität bei Ihren Anwendungen und Diensten anmelden.

- Der Partner verwendet seine eigenen Identitäten und Anmeldeinformationen; Azure AD ist nicht erforderlich.
- Sie müssen keine externen Konten oder Kennwörter verwalten.
- Sie müssen keine Konten synchronisieren oder Kontolebenszyklen verwalten.  

## <a name="easily-invite-guest-users-from-the-azure-ad-portal"></a>Einfaches Einladen von Gastbenutzern über das Azure AD-Portal

Als Administrator können Sie im Azure-Portal problemlos Gastbenutzer zu Ihrer Organisation hinzufügen.

- [Erstellen Sie einen neuen Gastbenutzer](b2b-quickstart-add-guest-users-portal.md) in Azure AD auf ähnliche Weise, wie Sie einen neuen Benutzer hinzufügen würden.
- Weisen Sie Gastbenutzer Apps oder Gruppen zu.
- Senden Sie eine Einladungs-E-Mail mit dem Einlöselink oder den direkten Link zu der App, die Sie freigeben möchten.

![Screenshot: Seite zur Eingabe einer Einladung an einen neuen Gastbenutzer](media/what-is-b2b/add-a-b2b-user-to-azure-portal.png)

- Gastbenutzer können die Einladung in wenigen Schritten [einlösen](redemption-experience.md), um sich anzumelden.

![Screenshot: Seite „Berechtigungen überprüfen“](media/what-is-b2b/consentscreen.png)


## <a name="use-policies-to-securely-share-your-apps-and-services"></a>Verwenden von Richtlinien zum sicheren Freigeben von Anwendungen und Diensten

Sie können Autorisierungsrichtlinien verwenden, um Ihre Unternehmensinhalte zu schützen. Richtlinien für bedingten Zugriff (beispielsweise Multi-Factor Authentication) können erzwungen werden:

- Auf Mandantenebene
- Auf Anwendungsebene
- Für bestimmte Gastbenutzer, um Anwendungen und Daten des Unternehmens zu schützen

![Screenshot: Option „Bedingter Zugriff“](media/what-is-b2b/tutorial-mfa-policy-2.png)



## <a name="let-application-and-group-owners-manage-their-own-guest-users"></a>Anwendungs- und Gruppenbesitzer können ihre eigenen Gastbenutzer verwalten

Sie können die Gastbenutzerverwaltung an Anwendungsbesitzer delegieren, sodass sie Gastbenutzer direkt zu jeder Anwendung hinzufügen können, die sie freigeben möchten, unabhängig davon, ob es sich um eine Microsoft-Anwendung handelt oder nicht.

- Administratoren richten die Self-Service-Anwendungs- und Gruppenverwaltung ein.
- Benutzer, die keine Administratoren sind, verwenden ihren [Zugriffsbereich](https://myapps.microsoft.com), um Gastbenutzer zu Anwendungen oder Gruppen hinzuzufügen.

![Screenshot: Zugriffsbereich für einen Gastbenutzer](media/what-is-b2b/access-panel-manage-app.png)

## <a name="customize-the-onboarding-experience-for-b2b-guest-users"></a>Anpassen des Onboardings für B2B-Gastbenutzer

Integrieren Sie Ihre externen Partner entsprechend den Anforderungen Ihrer Organisation.

- Konfigurieren Sie mit der [Azure AD-Berechtigungsverwaltung](../governance/entitlement-management-overview.md) Richtlinien, mit denen der [Zugriff für externer Benutzer verwaltet wird](../governance/entitlement-management-external-users.md#how-access-works-for-external-users).
- Verwenden Sie die [Einladungs-API für die B2B-Zusammenarbeit](/graph/api/resources/invitation), um das Onboarding anzupassen.

## <a name="integrate-with-identity-providers"></a>Integrieren mit Identitätsanbietern

Azure AD unterstützt externe Identitätsanbieter wie Facebook, Microsoft-Konten, Google oder Unternehmensidentitätsanbieter. Sie können einen Verbund mit Identitätsanbietern einrichten, damit Ihre externen Benutzer sich mit Ihren vorhandenen Konten für soziale Netzwerke oder Unternehmen anmelden können, anstatt ein neues Konto nur für Ihre Anwendung zu erstellen. Informieren Sie sich ausführlicher über [Identitätsanbieter für externe Identitäten](identity-providers.md).

![Screenshot der Seite mit Identitätsanbietern](media/what-is-b2b/identity-providers.png)


## <a name="create-a-self-service-sign-up-user-flow"></a>Erstellen eines Benutzerflows für die Self-Service-Registrierung

Mit einem Benutzerflow für die Self-Service-Registrierung können Sie eine Registrierungsbenutzeroberfläche für externe Benutzer erstellen, die auf Ihre Apps zugreifen möchten. Im Rahmen des Registrierungsflows können Sie Optionen für verschiedene Identitätsanbieter für soziale Netzwerke oder Unternehmen bereitstellen und Informationen über den Benutzer sammeln. Informieren Sie sich ausführlicher über die [Self-Service-Registrierung und ihre Einrichtung](self-service-sign-up-overview.md).

Sie können auch [API-Connectors](api-connectors-overview.md) verwenden, um Ihre Benutzerflows für die Self-Service-Registrierung in externe Cloudsysteme zu integrieren. Sie können eine Verbindung mit benutzerdefinierten Genehmigungsworkflows herstellen, die Identitätsüberprüfung durchführen, vom Benutzer bereitgestellte Informationen überprüfen usw.

![Screenshot der Seite mit Benutzerflows](media/what-is-b2b/self-service-sign-up-user-flow-overview.png)
<!--TODO: Add screenshot with API connectors -->

## <a name="next-steps"></a>Nächste Schritte

- [Preise für externe Identitäten](external-identities-pricing.md)
- [Hinzufügen von Gastbenutzern für B2B-Zusammenarbeit im Portal](add-users-administrator.md)
- [Verstehen, wie Einladungen eingelöst werden](redemption-experience.md)
