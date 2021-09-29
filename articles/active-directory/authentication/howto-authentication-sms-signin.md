---
title: SMS-basierte Benutzeranmeldung für Azure Active Directory
description: Erfahren Sie, wie Sie die Benutzeranmeldung per SMS bei Azure Active Directory konfigurieren und aktivieren.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/18/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: anjusingh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6cf0cc0ebc60a5b401ac8bd9726e39d23d8504a0
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124766102"
---
# <a name="configure-and-enable-users-for-sms-based-authentication-using-azure-active-directory"></a>Konfigurieren und Aktivieren der SMS-basierten Authentifizierung von Benutzern mit Azure Active Directory 

Um die Anmeldung bei Anwendungen und Diensten zu vereinfachen und zu sichern, bietet Azure Active Directory (Azure AD) mehrere Authentifizierungsoptionen. Bei der SMS-basierten Authentifizierung können sich Benutzer ohne Angabe oder sogar Kenntnis ihres Benutzernamens und Kennworts anmelden. Nachdem das Konto von einem Identitätsadministrator erstellt wurde, können sie bei der Anmeldeaufforderung ihre Telefonnummer eingeben. Sie erhalten einen Authentifizierungscode per SMS, den sie bereitstellen können, um die Anmeldung abzuschließen. Diese Authentifizierungsmethode vereinfacht den Zugriff auf Anwendungen und Dienste, insbesondere für Außendienstmitarbeiter.

In diesem Artikel erfahren Sie, wie Sie die SMS-basierte Authentifizierung für ausgewählte Benutzer oder Gruppen in Azure AD aktivieren. Eine Liste der Apps, die die Verwendung von SMS-basierter Anmeldung unterstützen, finden Sie unter [App-Unterstützung für die SMS-basierte Authentifizierung](how-to-authentication-sms-supported-apps.md).

## <a name="before-you-begin"></a>Voraussetzungen

Für diesen Artikel benötigen Sie die folgenden Ressourcen und Berechtigungen:

* Ein aktives Azure-Abonnement.
    * Wenn Sie kein Azure-Abonnement besitzen, [erstellen Sie ein Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Ihr Abonnement muss einem Azure Active Directory-Mandanten zugeordnet sein.
    * [Erstellen Sie einen Azure Active Directory-Mandanten][create-azure-ad-tenant], oder [verknüpfen Sie ein Azure-Abonnement mit Ihrem Konto][associate-azure-ad-tenant], sofern erforderlich.
* Sie benötigen Berechtigungen als *globaler Administrator* in Ihrem Azure AD-Mandanten, um die SMS-basierte Authentifizierung zu aktivieren.
* Jeder Benutzer, der in der Richtlinie für die Authentifizierung per Textnachricht aktiviert wird, muss lizenziert werden, auch wenn er dies nicht nutzt. Jeder aktivierte Benutzer muss über eine der folgenden Azure AD-, EMS- oder Microsoft 365-Lizenzen verfügen:
    * [Microsoft 365 F1 oder F3][m365-firstline-workers-licensing]
    * [Azure Active Directory Premium P1 oder P2][azure-ad-pricing]
    * [Enterprise Mobility + Security (EMS) E3 oder E5][ems-licensing] oder [Microsoft 365 E3 oder E5][m365-licensing]
    * [Office 365 F3][o365-f3]

## <a name="known-issues"></a>Bekannte Probleme

Hier sind einige bekannte Probleme aufgeführt:

* Die SMS-basierte Authentifizierung ist zurzeit nicht mit Azure AD Multi-Factor Authentication kompatibel.
* Mit Ausnahme von Teams ist die SMS-basierte Authentifizierung nicht mit nativen Office-Anwendungen kompatibel.
* Die SMS-basierte Authentifizierung wird für B2B-Konten nicht empfohlen.
* Verbundbenutzer werden nicht im Basismandanten authentifiziert. Sie werden nur in der Cloud authentifiziert.
* Wenn die Standardanmeldemethode eines Benutzers eine SMS oder ein Anruf an seine Telefonnummer ist, wird der SMS-Code oder Sprachanruf während der mehrstufigen Authentifizierung automatisch gesendet. Ab Juni 2021 werden einige Apps Benutzer auffordern, zuerst **SMS** oder **Anruf** auszuwählen. Diese Option verhindert, dass zu viele Sicherheitscodes für verschiedene Apps gesendet werden. Wenn die Standardanmeldungsmethode die Microsoft Authenticator-App ist ([was dringend empfohlen wird](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/it-s-time-to-hang-up-on-phone-transports-for-authentication/ba-p/1751752)), wird die App-Benachrichtigung automatisch gesendet.

## <a name="enable-the-sms-based-authentication-method"></a>Aktivieren der SMS-basierten Authentifizierungsmethode

Das Aktivieren und Verwenden der SMS-basierten Authentifizierung in Ihrer Organisation umfasst drei Hauptschritte:

* Aktivieren der Authentifizierungsmethodenrichtlinie
* Auswählen der Benutzer oder Gruppen, die die SMS-basierte Authentifizierungsmethode verwenden können
* Zuweisen einer Telefonnummer zu jedem Benutzerkonto
    * Diese Telefonnummer kann im Azure-Portal (in diesem Artikel gezeigt) und unter *Meine Mitarbeiter* oder *Mein Konto* zugewiesen werden.

Zunächst aktivieren Sie die SMS-basierte Authentifizierung für Ihren Azure AD-Mandanten.

1. Melden Sie sich als *globaler Administrator* beim [Azure-Portal][azure-portal] an.
1. Suchen Sie nach **Azure Active Directory**, und wählen Sie diese Option aus.
1. Wählen Sie im Navigationsmenü auf der linken Seite im Azure Active Directory-Fenster **Sicherheit > Authentifizierungsmethoden > Authentifizierungsmethodenrichtlinie** aus.

    [![Navigieren Sie im Azure-Portal zum Fenster „Authentifizierungsmethodenrichtlinie“, und wählen Sie es aus.](media/howto-authentication-sms-signin/authentication-method-policy-cropped.png)](media/howto-authentication-sms-signin/authentication-method-policy.png#lightbox)

1. Wählen Sie in der Liste der verfügbaren Authentifizierungsmodi **Textnachricht** aus.
1. Legen Sie **Aktivieren** auf *Ja* fest.

    ![Aktivieren der Textauthentifizierung im Fenster „Authentifizierungsmethodenrichtlinie“](./media/howto-authentication-sms-signin/enable-text-authentication-method.png)

    Sie können die SMS-basierte Authentifizierung für *Alle Benutzer* aktivieren oder *Benutzer* und Gruppen auswählen. Im nächsten Abschnitt aktivieren Sie die SMS-basierte Authentifizierung für einen Testbenutzer.

## <a name="assign-the-authentication-method-to-users-and-groups"></a>Zuweisen der Authentifizierungsmethode zu Benutzern und Gruppen

Nachdem die SMS-basierte Authentifizierung in Ihrem Azure AD Mandanten nun aktiviert ist, wählen Sie einige Benutzer oder Gruppen aus, die diese Authentifizierungsmethode verwenden dürfen.

1. Legen Sie im Fenster der Authentifizierungsrichtlinie für Textnachrichten das **Ziel** auf *Benutzer auswählen* fest.
1. Wählen Sie **Benutzer oder Gruppen hinzufügen** und dann einen Testbenutzer oder eine Gruppe aus, z. B. *Contoso User* oder *Contoso SMS Users*.

    [![Wählen Sie Benutzer oder Gruppen im Azure-Portal, die für die SMS-basierte Authentifizierung aktiviert werden sollen.](media/howto-authentication-sms-signin/add-users-or-groups-cropped.png)](media/howto-authentication-sms-signin/add-users-or-groups.png#lightbox)

1. Wenn Sie die Benutzer oder Gruppen ausgewählt haben, wählen Sie **Auswählen** und dann **Speichern** aus, um die aktualisierte Authentifizierungsmethodenrichtlinie zu speichern.

Jeder Benutzer, der in der Richtlinie für die Authentifizierung per Textnachricht aktiviert wird, muss lizenziert werden, auch wenn er dies nicht nutzt. Stellen Sie sicher, dass Sie über die entsprechenden Lizenzen für die Benutzer verfügen, die Sie in der Authentifizierungsmethodenrichtlinie aktivieren, insbesondere wenn Sie das Feature für umfangreiche Benutzergruppen aktivieren.

## <a name="set-a-phone-number-for-user-accounts"></a>Festlegen einer Telefonnummer für Benutzerkonten

Die Benutzer sind jetzt für die SMS-basierte Authentifizierung aktiviert, aber Ihre Telefonnummer muss noch dem Benutzerprofil in Azure AD zugeordnet werden, bevor sie sich anmelden können. Ein Benutzer kann [diese Telefonnummer selbstständig](https://support.microsoft.com/account-billing/set-up-sms-sign-in-as-a-phone-verification-method-0aa5b3b3-a716-4ff2-b0d6-31d2bcfbac42) unter *Mein Konto* festlegen, oder Sie können die Telefonnummer im Azure-Portal zuweisen. Telefonnummern können von *globalen Administratoren*, *Authentifizierungsadministratoren* oder *privilegierten Authentifizierungsadministratoren* festgelegt werden.

Wenn eine Telefonnummer für SMS-Anmeldung festgelegt ist, kann sie auch für [Azure AD Multi-Factor Authentication][tutorial-azure-mfa] und [Self-Service-Kennwortzurücksetzung][tutorial-sspr] verwendet werden.

1. Suchen Sie nach **Azure Active Directory**, und wählen Sie diese Option aus.
1. Wählen Sie im Navigationsmenü auf der linken Seite im Fenster „Azure Active Directory“ die Option **Benutzer** aus.
1. Wählen Sie den Benutzer aus, für den Sie im vorherigen Abschnitt die SMS-basierte Authentifizierung aktiviert haben (z. B. *Contoso User*), und wählen Sie dann **Authentifizierungsmethoden** aus.
1. Wählen Sie **+ Authentifizierungsmethode hinzufügen** und dann im Dropdownmenü *Methode auswählen* die Option **Telefonnummer** aus.

    Geben Sie die Telefonnummer des Benutzers ein, einschließlich des Ländercodes, z. B. *+49 xxxxxxxxx*. Das Azure-Portal überprüft das korrekte Format der Telefonnummer.

    Wählen Sie dann im Dropdownmenü *Telefontyp* die Option *Mobil*, *Alternative Mobiltelefonnummer* oder bei Bedarf *Sonstige* aus.

    :::image type="content" source="media/howto-authentication-sms-signin/set-user-phone-number.png" alt-text="Festlegen einer Telefonnummer für einen Benutzer im Azure-Portal für die Verwendung mit der SMS-basierten Authentifizierung":::

    Die Telefonnummer muss in Ihrem Mandanten eindeutig sein. Wenn Sie versuchen, dieselbe Telefonnummer für mehrere Benutzer zu verwenden, wird eine Fehlermeldung angezeigt.

1. Wenn Sie die Telefonnummer auf das Konto eines Benutzers anwenden möchten, wählen Sie **Hinzufügen** aus.

Wenn die Bereitstellung erfolgreich ist, wird ein Häkchen bei *SMS Sign-in enabled* (SMS-Anmeldung aktiviert) angezeigt.

## <a name="test-sms-based-sign-in"></a>Testen der SMS-basierten Anmeldung

Führen Sie die folgenden Schritte aus, um zu testen, ob das Benutzerkonto jetzt für die SMS-basierte Anmeldung aktiviert ist:

1. Öffnen Sie ein neues Webbrowserfenster im InPrivate- oder Inkognitomodus, und browsen Sie zu [https://www.office.com][office].
1. Wählen Sie in der rechten oberen Ecke **Anmelden** aus.
1. Geben Sie an der Anmeldeaufforderung die Telefonnummer ein, die dem Benutzer im vorherigen Abschnitt zugeordnet wurde, und wählen Sie dann **Weiter** aus.

    ![Geben Sie an der Anmeldeaufforderung eine Telefonnummer für den Testbenutzer ein.](./media/howto-authentication-sms-signin/sign-in-with-phone-number.png)

1. Eine SMS wird an die angegebene Telefonnummer gesendet. Um den Anmeldevorgang abzuschließen, geben Sie den sechsstelligen Code aus der SMS an der Anmeldeaufforderung ein.

    ![Eingeben des Bestätigungscodes, der per SMS an die Telefonnummer des Benutzers gesendet wurde](./media/howto-authentication-sms-signin/sign-in-with-phone-number-confirmation-code.png)

1. Der Benutzer ist jetzt angemeldet, ohne dass er einen Benutzernamen oder ein Kennwort angeben musste.


## <a name="troubleshoot-sms-based-sign-in"></a>Behandeln von Problemen mit der SMS-basierten Anmeldung

Sie können die folgenden Szenarien und Schritte zum Troubleshooting anwenden, wenn Sie Probleme beim Aktivieren und Verwenden der SMS-basierten Anmeldung haben. Eine Liste von Apps, die die Verwendung von SMS-basierter Anmeldung unterstützen, finden Sie unter [App-Unterstützung für die SMS-basierte Authentifizierung](how-to-authentication-sms-supported-apps.md).


### <a name="phone-number-already-set-for-a-user-account"></a>Telefonnummer bereits für ein Benutzerkonto festgelegt

Wenn ein Benutzer bereits für Azure AD Multi-Factor Authentication oder die Self-Service-Kennwortzurücksetzung (SSPR) registriert ist, wurde seinem Konto bereits eine Telefonnummer zugeordnet. Diese Telefonnummer steht nicht automatisch für die Verwendung bei der SMS-basierten Anmeldung zur Verfügung.

Für Benutzer, in deren Konto bereits eine Telefonnummer festgelegt wurde, wird auf der Seite **Mein Profil** die Schaltfläche *Enable for SMS sign-in* (SMS-Anmeldung aktivieren) angezeigt. Durch Auswählen dieser Schaltfläche wird das Konto für die Verwendung der SMS-basierten Anmeldung und der zuvor bereits erfolgten Registrierung für Azure AD Multi-Factor Authentication oder SSPR aktiviert.

Weitere Informationen zum Ablauf für den Endbenutzer finden Sie unter [SMS-Anmeldung mit einer Telefonnummer](https://support.microsoft.com/account-billing/set-up-sms-sign-in-as-a-phone-verification-method-0aa5b3b3-a716-4ff2-b0d6-31d2bcfbac42).

### <a name="error-when-trying-to-set-a-phone-number-on-a-users-account"></a>Fehler beim Festlegen einer Telefonnummer für ein Benutzerkonto

Wenn Sie beim Versuch, im Azure-Portal eine Telefonnummer für ein Benutzerkonto festzulegen, eine Fehlermeldung erhalten, versuchen Sie die folgenden Schritte zum Troubleshooting:

1. Stellen Sie sicher, dass Sie für die SMS-basierte Anmeldung aktiviert wurden.
1. Vergewissern Sie sich, dass das Benutzerkonto in der *SMS*-Authentifizierungsmethodenrichtlinie aktiviert ist.
1. Stellen Sie im Azure-Portal sicher, dass Sie die Telefonnummer im richtigen Format festgelegt haben (z. B. *+ 1 4251234567*).
1. Stellen Sie sicher, dass die Telefonnummer nicht an anderer Stelle in Ihrem Mandanten verwendet wird.
1. Vergewissern Sie sich, dass für das Konto keine Voicemailnummer festgelegt wurde. Wenn eine Voicemailnummer festgelegt wurde, löschen Sie die Telefonnummer, und versuchen Sie es erneut.

## <a name="next-steps"></a>Nächste Schritte

- Eine Liste von Apps, die die Verwendung von SMS-basierter Anmeldung unterstützen, finden Sie unter [App-Unterstützung für die SMS-basierte Authentifizierung](how-to-authentication-sms-supported-apps.md).
- Weitere Möglichkeiten zum Anmelden bei Azure AD ohne Kennwort, z. B. die Microsoft Authenticator-App oder FIDO2-Sicherheitsschlüssel, finden Sie unter [Optionen für die kennwortlose Authentifizierung für Azure Active Directory][concepts-passwordless].
- Sie können auch Microsoft Graph-REST-API verwenden, um die SMS-basierte Anmeldung zu [aktivieren][rest-enable] oder zu [deaktivieren][rest-disable].


<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../fundamentals/active-directory-how-subscriptions-associated-directory.md
[concepts-passwordless]: concept-authentication-passwordless.md
[tutorial-azure-mfa]: tutorial-enable-azure-mfa.md
[tutorial-sspr]: tutorial-enable-sspr.md
[rest-enable]: /graph/api/phoneauthenticationmethod-enablesmssignin?tabs=http
[rest-disable]: /graph/api/phoneauthenticationmethod-disablesmssignin?tabs=http

<!-- EXTERNAL LINKS -->
[azure-portal]: https://portal.azure.com
[office]: https://www.office.com
[m365-firstline-workers-licensing]: https://www.microsoft.com/licensing/news/m365-firstline-workers
[azuread-licensing]: https://azure.microsoft.com/pricing/details/active-directory/
[ems-licensing]: https://www.microsoft.com/microsoft-365/enterprise-mobility-security/compare-plans-and-pricing
[m365-licensing]: https://www.microsoft.com/microsoft-365/compare-microsoft-365-enterprise-plans
[o365-f1]: https://www.microsoft.com/microsoft-365/business/office-365-f1?market=af
[o365-f3]: https://www.microsoft.com/microsoft-365/business/office-365-f3?activetab=pivot%3aoverviewtab
[azure-ad-pricing]: https://www.microsoft.com/security/business/identity-access-management/azure-ad-pricing