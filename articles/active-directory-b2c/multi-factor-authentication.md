---
title: Multi-Factor Authentication in Azure Active Directory B2C | Microsoft-Dokumentation
description: Aktivieren der Multi-Factor Authentication in kundenorientierten Anwendungen, die mit Azure Active Directory B2C geschützt werden
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/10/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 42feac542760bbebc703cabc4ecc114b0ab4259d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122345950"
---
# <a name="enable-multi-factor-authentication-in-azure-active-directory-b2c"></a>Aktivieren der Multi-Factor Authentication in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Azure Active Directory B2C (Azure AD B2C) bietet eine direkte Integration von [Azure AD Multi-Factor Authentication](../active-directory/authentication/concept-mfa-howitworks.md), damit Sie eine zweite Sicherheitsebene zu Registrierungs- und Anmeldeoberflächen in Anwendungen hinzufügen können. Sie können die Multi-Factor Authentication aktivieren, ohne eine einzige Codezeile schreiben zu müssen. Wenn Sie bereits Benutzerflows für Registrierung und Anmeldung erstellt haben, können Sie dennoch die mehrstufige Authentifizierung aktivieren.

Mithilfe dieses Features können Anwendungen beispielsweise diese Szenarien handhaben:

- Für den Zugriff auf eine Anwendung ist keine Multi-Factor Authentication erforderlich, jedoch für den Zugriff auf eine andere Anwendung. Beispielsweise kann sich der Kunde bei der Anwendung einer Kfz-Versicherung mit einem lokalen Konto oder dem Konto eines sozialen Netzwerks anmelden. Er muss jedoch seine Telefonnummer bestätigen, bevor er auf die Anwendung für die Hausversicherung zugreifen kann, die im selben Verzeichnis registriert ist.
- Die Multi-Factor Authentication ist allgemein für den Zugriff auf eine Anwendung nicht erforderlich, jedoch für vertrauliche Unterbereiche dieser Anwendung. Beispielsweise kann sich der Kunde bei einer Onlinebanking-Anwendung mit einem lokalen Konto oder dem Konto eines sozialen Netzwerks anmelden, um den Kontostand abzufragen. Um eine Überweisung zu tätigen, ist jedoch die Bestätigung der Telefonnummer erforderlich.

### <a name="verification-methods"></a>Überprüfungsmethoden

Bei [Bedingtem Zugriff](conditional-access-identity-protection-overview.md) können Benutzer aufgrund von Konfigurationsentscheidungen, die Sie als Administrator treffen können, zur MFA aufgefordert werden oder nicht. Es folgen die Methoden zur mehrstufigen Authentifizierung:

- Email
- sms
- Telefonanrufe

## <a name="set-multi-factor-authentication"></a>Festlegen der Multi-Factor Authentication

::: zone pivot="b2c-user-flow"

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com)
1. Wählen Sie im Hauptmenü über den Filter **Verzeichnis + Abonnement** das Verzeichnis aus, das Ihren Azure AD B2C-Mandanten enthält.
1. Wählen Sie im linken Menü die Option **Azure AD B2C** aus. Oder wählen Sie **Alle Dienste** aus, suchen Sie nach dem Eintrag **Azure AD B2C**, und wählen Sie ihn aus.
1. Wählen Sie **Benutzerflows** aus.
1. Wählen Sie den Benutzerflow aus, für den Sie die mehrstufige Authentifizierung (Multifactor Authentication, MFA) aktivieren möchten. beispielsweise *B2C_1_signinsignup*.
1. Wählen Sie **Eigenschaften** aus.
1. Wählen Sie im Abschnitt **mehrstufige Authentifizierung** den gewünschten **Typ der Methode**. Wählen Sie dann unter **MFA-Erzwingung** eine Option:

   - **Off** - MFA wird während der Anmeldung nie erzwungen und Benutzer werden während der Registrierung oder Anmeldung nicht aufgefordert, MFA zu aktivieren.
   - **Immer aktiviert:** MFA wird unabhängig von der Konfiguration des bedingten Zugriffs stets verlangt. Während der Registrierung werden Benutzer aufgefordert, sich für MFA zu registrieren. Wenn Benutzer bei der Anmeldung noch nicht für MFA registriert sind, werden sie aufgefordert, sich zu registrieren.
   - **Bedingt:** Während der Registrierung und Anmeldung werden Benutzer aufgefordert, sich für MFA zu registrieren (sowohl neue als auch vorhandene Benutzer, die nicht für MFA registriert sind). Während der Anmeldung wird MFA nur dann erzwungen, wenn die Auswertung einer aktiven Richtlinie für bedingten Zugriff dies erfordert:

    - Wenn das Ergebnis einer MFA-Abfrage ohne Risiko ist, wird MFA erzwungen. Wenn der Benutzer noch nicht für MFA registriert ist, wird er aufgefordert, sich zu registrieren.
    - Wenn das Ergebnis eine MFA-Abfrage aufgrund eines Risikos ist *und* der Benutzer nicht für MFA registriert ist, wird die Anmeldung blockiert.

   > [!NOTE]
   >
   > - Mit der allgemeinen Verfügbarkeit des bedingten Zugriffs in Azure AD B2C werden Benutzer jetzt bei der Registrierung aufgefordert, sich für eine MFA-Methode zu registrieren. Alle Benutzerflows für die Registrierung, die Sie vor der allgemeinen Verfügbarkeit erstellt haben, spiegeln dieses neue Verhalten nicht automatisch wider. Sie können das Verhalten jedoch einschließen, indem Sie neue Benutzerflows erstellen.
   > - Wenn Sie **Bedingt** auswählen, müssen Sie den [Benutzerflows auch eine Richtlinie für bedingten Zugriff hinzufügen](conditional-access-user-flow.md) und die Apps angeben, für die diese Richtlinie gelten soll.
   > - Die mehrstufige Authentifizierung (Multi-Factor Authentication, MFA) ist bei Benutzerflows für die Registrierung standardmäßig deaktiviert. In Benutzerflows mit telefonischer Registrierung ist es zwar möglich, MFA zu aktivieren. Als zweiter Authentifizierungsfaktor steht jedoch nur die Einmalkennung per E-Mail zur Verfügung, da die Telefonnummer als primärer Bezeichner verwendet wird.

1. Wählen Sie **Speichern** aus. MFA ist für diesen Benutzerflow jetzt aktiviert.

Sie können die Benutzeroberflächenfunktion mit **Benutzerflow ausführen** überprüfen. Bestätigen Sie das folgende Szenario:

In Ihrem Mandanten wird ein Kundenkonto erstellt, bevor der Schritt für die Multi-Factor Authentication ausgeführt wird. Während dieses Schritts wird der Kunde aufgefordert, seine Telefonnummer anzugeben und zu bestätigen. Wenn die Überprüfung erfolgreich ist, wird die Telefonnummer zur späteren Verwendung an das Kundenkonto angefügt. Auch wenn der Kunde den Vorgang abbricht, kann er bei der nächsten Anmeldung aufgefordert werden, eine Telefonnummer erneut zu bestätigen (bei aktivierter Multi-Factor Authentication).

::: zone-end

::: zone pivot="b2c-custom-policy"

Holen Sie sich zum Aktivieren der mehrstufigen Authentifizierung die Starter Packs für benutzerdefinierte Richtlinien von GitHub, und aktualisieren Sie dann die XML-Dateien im Starter Pack **SocialAndLocalAccountsWithMFA** mit dem Namen Ihres Azure AD B2C-Mandanten. **SocialAndLocalAccountsWithMFA** ermöglicht lokale Optionen und Optionen für soziale Netzwerke sowie zur mehrstufigen Authentifizierung. Weitere Informationen finden Sie unter [Erste Schritte mit benutzerdefinierten Richtlinien in Active Directory B2C](tutorial-create-user-flows.md?pivots=b2c-custom-policy). 

::: zone-end
