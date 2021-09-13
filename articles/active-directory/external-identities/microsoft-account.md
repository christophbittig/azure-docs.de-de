---
title: „Microsoft-Konto“-Identitätsanbieter (MSA) in Azure AD
description: Verwenden Sie Azure AD, um es einem externen Benutzer (Gast) zu ermöglichen, sich mit seinem Microsoft-Konto (MSA) bei Ihren Azure AD-Apps anzumelden.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 08/09/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2333ba6aec10d124d960dfc746bc9df2c449f3bb
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/14/2021
ms.locfileid: "122342706"
---
# <a name="microsoft-account-msa-identity-provider-for-external-identities"></a>„Microsoft-Konto“-Identitätsanbieter (MSA) für External Identities

Ihre B2B-Gastbenutzer können ihre eigenen persönlichen Microsoft-Konten ohne weitere Konfiguration für B2B Collaboration verwenden. Gastbenutzer können Ihre B2B Collaboration-Einladungen einlösen oder Ihre Benutzerflows zur Registrierung mit ihrem persönlichen Microsoft-Konto durchführen.

Microsoft-Konten werden von einem Benutzer eingerichtet, um Zugriff auf verbraucherorientierte Microsoft-Produkte und -Clouddienste wie Outlook, OneDrive, MSN, Xbox LIVE oder Microsoft 365 zu erhalten. Das Konto wird im von Microsoft betriebenen Microsoft-Kontosystem für Endverbraucheridentitäten erstellt und gespeichert.

## <a name="guest-sign-in-using-microsoft-accounts"></a>Gastanmeldung mit Microsoft-Konten

Die Option „Microsoft-Konto“ ist standardmäßig in der Liste der External Identities-Identitätsanbieter verfügbar. Es ist keine weitere Konfiguration erforderlich, damit sich Gastbenutzer mit ihrem Microsoft-Konto über den Einladungsflow oder einen Benutzerflow für die Self-Service-Registrierung anmelden können.

![„Microsoft-Konto“ in der Liste der Identitätsanbieter](media/microsoft-account/microsoft-account-identity-provider.png)

### <a name="microsoft-account-in-the-invitation-flow"></a>„Microsoft-Konto“ im Einladungsflow

Wenn Sie einen [Gastbenutzer zu B2B Collaboration einladen](add-users-administrator.md), können Sie sein Microsoft-Konto als E-Mail-Adresse für die Anmeldung angeben.

![Einladen mit einem Microsoft-Konto](media/microsoft-account/microsoft-account-invite.png)

### <a name="microsoft-account-in-self-service-sign-up-user-flows"></a>„Microsoft-Konto“ in Benutzerflows für die Self-Service-Registrierung

„Microsoft-Konto“ ist einer der verfügbaren Identitätsanbieter für Ihre Benutzerflows für die Self-Service-Registrierung. Benutzer können sich mit ihren eigenen Microsoft-Konten für Ihre Anwendungen registrieren. Zunächst müssen Sie für Ihren Mandanten die [Self-Service-Registrierung aktivieren](self-service-sign-up-user-flow.md). Danach können Sie einen Benutzerflow für die Anwendung einrichten und „Microsoft-Konto“ als eine der Anmeldeoptionen auswählen.

![„Microsoft-Konto“ in einem Benutzerflow für die Self-Service-Registrierung](media/microsoft-account/microsoft-account-user-flow.png)

## <a name="verifying-the-applications-publisher-domain"></a>Überprüfen der Herausgeberdomäne einer Anwendung
Ab November 2020 werden neue Anwendungsregistrierungen in der Benutzereinwilligungsaufforderung als nicht überprüft angezeigt, es sei denn, [die Herausgeberdomäne der Anwendung wurde überprüft](../develop/howto-configure-publisher-domain.md) ***und*** die Identität des Unternehmens wurde durch das Microsoft Partner Network überprüft und der Anwendung zugeordnet. ([Weitere Informationen](../develop/publisher-verification-overview.md) zu diesen Änderungen) Beachten Sie, dass für Azure AD-Benutzerflows die Domäne des Herausgebers nur angezeigt wird, wenn ein Microsoft-Konto oder ein anderer [Azure AD-Mandant](azure-ad-account.md) als Identitätsanbieter verwendet wird. Gehen Sie wie folgt vor, um diese neuen Anforderungen zu erfüllen:

1. [Überprüfen Sie Ihre Unternehmensidentität mit ihrem Microsoft Partner Network-Konto (MPN)](/partner-center/verification-responses). Bei diesem Prozess werden Informationen zu Ihrem Unternehmen und zum primären Kontakt Ihres Unternehmens überprüft.
1. Schließen Sie die Herausgeberüberprüfung ab, um mithilfe einer der folgenden Optionen Ihr MPN-Konto Ihrer App-Registrierung zuzuordnen:
   - Wenn die App-Registrierung für den Microsoft-Konto-Identitätsanbieter in einem Azure AD-Mandanten ist, [überprüfen Sie Ihre App im App-Registrierungsportal.](../develop/mark-app-as-publisher-verified.md)
   - Wenn sich Ihre App-Registrierung für den Microsoft-Konto-Identitätsanbieter in einem Azure AD B2C-Mandanten befindet, [markieren Sie Ihre App mithilfe von Microsoft Graph-APIs als vom Herausgeber verifiziert](../develop/troubleshoot-publisher-verification.md#making-microsoft-graph-api-calls) (z. B. mithilfe von Graph-Tester).

## <a name="next-steps"></a>Nächste Schritte

- [Hinzufügen von Azure Active Directory B2B Collaboration-Benutzern](add-users-administrator.md)
- [Hinzufügen eines Benutzerflows für die Self-Service-Registrierung zu einer App (Vorschau)](self-service-sign-up-user-flow.md)