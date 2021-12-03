---
title: 'Tutorial: Erstellen von Benutzerflows und benutzerdefinierten Richtlinien – Azure Active Directory B2C'
description: In diesem Tutorial erfahren Sie, wie Sie Benutzerflows und benutzerdefinierte Richtlinien im Azure-Portal erstellen, um das Registrieren, Anmelden und Bearbeiten von Benutzerprofilen für Ihre Anwendungen in Azure Active Directory B2C zu ermöglichen.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 10/18/2021
ms.author: kengaderdus
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 5cdf2cfb9b530721a8de31501c2f763f0c61bd21
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130240887"
---
# <a name="tutorial-create-user-flows-and-custom-policies-in-azure-active-directory-b2c"></a>Tutorial: Erstellen von Benutzerflows und benutzerdefinierten Richtlinien in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Ihre Anwendungen enthalten möglicherweise Benutzerflows, mit denen Benutzer sich registrieren und anmelden oder ihr Profil verwalten können. Sie können mehrere Benutzerflows verschiedener Typen in Ihrem Azure Active Directory (Azure AD) B2C-Mandanten erstellen und bei Bedarf in Ihren Anwendungen verwenden. Benutzerflows können anwendungsübergreifend wiederverwendet werden.

::: zone pivot="b2c-user-flow"
Mithilfe eines Benutzerflows können Sie feststellen, wie Benutzer mit Ihrer Anwendung interagieren, wenn sie Aktionen wie Anmelden, Registrieren, Bearbeiten eines Profils oder Zurücksetzen eines Kennworts ausführen. In diesem Artikel werden folgende Vorgehensweisen behandelt:
::: zone-end

::: zone pivot="b2c-custom-policy"
[Benutzerdefinierte Richtlinien](custom-policy-overview.md) sind Konfigurationsdateien, die das Verhalten Ihres Azure AD B2C-Mandanten (Azure Active Directory B2C) definieren. In diesem Artikel werden folgende Vorgehensweisen behandelt:
::: zone-end

> [!div class="checklist"]
> * Erstellen eines Benutzerflows für Registrierung und Anmeldung
> * Aktivieren der Self-Service-Kennwortzurücksetzung
> * Erstellen eines Benutzerflows für die Profilbearbeitung

::: zone pivot="b2c-user-flow"
> [!IMPORTANT]
> Benutzerflowversionen werden jetzt anders angegeben. Zuvor wurden V1-Versionen (bereit für die Produktion) sowie V1.1- und V2-Versionen (Vorschauversion) angeboten. Nun haben Sie Benutzerflows in zwei Versionen zusammengefasst: **Empfohlene** Benutzerflows mit den neuesten Features und **Standardbenutzerflows (Legacy)** . Alle Benutzerflows der Legacyvorschauversionen (V1.1 und V2) sind veraltet. Weitere Informationen finden Sie unter [Benutzerflowversionen in Azure Active Directory B2C](user-flow-versions.md). *Diese Änderungen gelten nur für die öffentliche Azure-Cloud. In anderen Umgebungen werden weiterhin die [Legacyversionen der Benutzerflows](user-flow-versions-legacy.md) verwendet.* 
::: zone-end

## <a name="prerequisites"></a>Voraussetzungen

::: zone pivot="b2c-user-flow"
- Wenn Sie noch nicht über einen Azure AD B2C-Mandanten verfügen, [erstellen Sie einen](tutorial-create-tenant.md), der mit Ihrem Azure-Abonnement verknüpft ist.
- [Registrieren Sie eine Webanwendung](tutorial-register-applications.md), und [aktivieren Sie die implizite Gewährung von ID-Token](tutorial-register-applications.md#enable-id-token-implicit-grant).
::: zone-end

::: zone pivot="b2c-custom-policy"

- Wenn Sie noch nicht über einen Azure AD B2C-Mandanten verfügen, [erstellen Sie einen](tutorial-create-tenant.md), der mit Ihrem Azure-Abonnement verknüpft ist.
- [Registrieren Sie eine Webanwendung](tutorial-register-applications.md), und [aktivieren Sie die implizite Gewährung von ID-Token](tutorial-register-applications.md#enable-id-token-implicit-grant).


::: zone-end

::: zone pivot="b2c-user-flow"
## <a name="create-a-sign-up-and-sign-in-user-flow"></a>Erstellen eines Benutzerflows für Registrierung und Anmeldung

Der Benutzerflow für Registrierung und Anmeldung verarbeitet die Benutzeroberflächen für die Registrierung und Anmeldung in ein und derselben Konfiguration. Die Benutzer Ihrer Anwendung werden je nach Kontext auf den entsprechenden Pfad geleitet.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnisse und Abonnements** aus.
1. Suchen Sie auf der Seite **Portaleinstellungen > Verzeichnisse und Abonnements** das Azure AD B2C-Verzeichnis in der Liste **Verzeichnisname**, und klicken Sie dann auf **Wechseln**.
1. Suchen Sie im Azure-Portal nach **Azure AD B2C**, und wählen Sie diese Option dann aus.
1. Wählen Sie unter **Richtlinien** die Option **Benutzerflows** und dann **Neuer Benutzerflow** aus.

    ![Seite „Benutzerflows“ im Portal mit hervorgehobener Schaltfläche „Neuer Benutzerflow“](./media/tutorial-create-user-flows/sign-up-sign-in-user-flow.png)

1. Wählen Sie auf der Seite **Benutzerflow erstellen** den Benutzerflow **Registrierung und Anmeldung** aus.

    ![Seite „Benutzerflow auswählen“ mit hervorgehobenem „Anmeldung“ und „Anmeldeflow“](./media/tutorial-create-user-flows/select-user-flow-type.png)

1. Wählen Sie unter **Version auswählen** die Option **Empfohlen** und dann **Erstellen** aus. (Weitere Informationen zu [Benutzerflowversionen](user-flow-versions.md))

    ![Seite „Benutzerflow erstellen“ im Azure-Portal mit hervorgehobenen Eigenschaften](./media/tutorial-create-user-flows/select-version.png)

1. Geben Sie unter **Name** einen Namen für den Benutzerflow ein. Beispiel: *signupsignin1*.
1. Wählen Sie unter **Identitätsanbieter** die Option **E-Mail-Registrierung** aus.
1. Wählen Sie für **Benutzerattribute und Ansprüche** die Ansprüche und Attribute aus, die Sie bei der Registrierung vom Benutzer sammeln und senden möchten. Wählen Sie z.B. **Mehr anzeigen** und dann Attribute und Ansprüche für **Land/Region**, **Anzeigename** und **Postleitzahl** aus. Klicken Sie auf **OK**.

    ![Auswahlseite für Attribute und Ansprüche mit drei ausgewählten Ansprüchen](./media/tutorial-create-user-flows/signup-signin-attributes.png)

1. Klicken Sie auf **Erstellen**, um den Benutzerflow hinzuzufügen. Dem Namen wird automatisch das Präfix *B2C_1* vorangestellt.

### <a name="test-the-user-flow"></a>Testen des Benutzerflows

1. Wählen Sie den von Ihnen erstellten Benutzerflow aus, um die entsprechende Übersichtsseite zu öffnen, und wählen Sie dann **Benutzerflow ausführen** aus.
1. Wählen Sie für **Anwendung** die Webanwendung *webapp1* aus, die Sie zuvor registriert haben. Als **Antwort-URL** sollte `https://jwt.ms` angezeigt werden.
1. Klicken Sie auf **Benutzerflow ausführen**, und wählen Sie dann **Jetzt registrieren** aus.

    ![Seite „Benutzerflow ausführen“ im Portal mit hervorgehobener Schaltfläche „Benutzerflow ausführen“](./media/tutorial-create-user-flows/signup-signin-run-now.PNG)

1. Geben Sie eine gültige E-Mail-Adresse ein, klicken Sie auf **Überprüfungscode senden**, geben Sie den Überprüfungscode ein, und wählen Sie dann **Code überprüfen** aus.
1. Geben Sie ein neues Kennwort ein, und bestätigen Sie es.
1. Wählen Sie das Land und die Region aus, geben Sie den anzuzeigenden Namen und eine Postleitzahl ein, und klicken Sie dann auf **Erstellen**. Das Token wird an `https://jwt.ms` zurückgegeben und sollte Ihnen angezeigt werden.
1. Sie können den Benutzerflow jetzt erneut ausführen. Sie sollten in der Lage sein, sich mit dem Konto, das Sie erstellt haben, anzumelden. Das zurückgegebene Token enthält die Ansprüche, die Sie für Land/Region, Name und Postleitzahl ausgewählt haben.

> [!NOTE]
> Die Benutzeroberfläche für „Benutzerflow ausführen“ ist derzeit nicht mit dem SPA-URL-Antworttyp kompatibel, für den der Autorisierungscodeflow verwendet wird. Wenn Sie die Benutzeroberfläche für „Benutzerflow ausführen“ mit dieser Art von App nutzen möchten, müssen Sie eine Antwort-URL vom Typ „Web“ registrieren und den impliziten Flow wie [hier](tutorial-register-spa.md) beschrieben aktivieren.

## <a name="enable-self-service-password-reset"></a>Aktivieren der Self-Service-Kennwortzurücksetzung

So aktivieren Sie die [Self-Service-Kennwortzurücksetzung](add-password-reset-policy.md) für den Benutzerflow für die Registrierung oder Anmeldung:

1. Wählen Sie den von Ihnen erstellten Registrierungs- oder Anmeldebenutzerflow aus.
1. Wählen Sie im Menü auf der linken Seite unter **Einstellungen** die Option **Eigenschaften** aus.
1. Wählen Sie unter **Kennwortkomplexität** die Option **Self-Service-Kennwortzurücksetzung** aus.
1. Wählen Sie **Speichern** aus.

### <a name="test-the-user-flow"></a>Testen des Benutzerflows

1. Wählen Sie den von Ihnen erstellten Benutzerflow aus, um die entsprechende Übersichtsseite zu öffnen, und wählen Sie dann **Benutzerflow ausführen** aus.
1. Wählen Sie für **Anwendung** die Webanwendung *webapp1* aus, die Sie zuvor registriert haben. Als **Antwort-URL** sollte `https://jwt.ms` angezeigt werden.
1. Wählen Sie **Benutzerflow ausführen** aus.
1. Wählen Sie auf der Seite für die Registrierung oder Anmeldung den Link **Kennwort vergessen?** aus.
1. Überprüfen Sie die E-Mail-Adresse des Kontos, das Sie zuvor erstellt haben, und wählen Sie **Weiter** aus.
1. Sie haben nun die Möglichkeit, das Kennwort des Benutzers zu ändern. Ändern Sie das Kennwort, und klicken Sie auf **Weiter**. Das Token wird an `https://jwt.ms` zurückgegeben und sollte Ihnen angezeigt werden.

## <a name="create-a-profile-editing-user-flow"></a>Erstellen eines Benutzerflows für die Profilbearbeitung

Wenn Sie Benutzern die Profilbearbeitung in Ihrer Anwendung ermöglichen möchten, verwenden Sie einen Benutzerflow für die Profilbearbeitung.

1. Wählen Sie auf der Übersichtsseite des Azure AD B2C-Mandanten im Menü die Option **Benutzerflows** und dann **Neuer Benutzerflow** aus.
1. Wählen Sie auf der Seite **Benutzerflow erstellen** den Benutzerflow **Profilbearbeitung** aus. 
1. Wählen Sie unter **Version auswählen** die Option **Empfohlen** und dann **Erstellen** aus.
1. Geben Sie unter **Name** einen Namen für den Benutzerflow ein. Beispiel: *profileediting1*.
1. Wählen Sie für **Identitätsanbieter** die Option **Anmeldung mit lokalem Konto** aus.
2. Wählen Sie für **Benutzerattribute** die Attribute aus, die der Kunde in seinem Profil bearbeiten darf. Wählen Sie z.B. **Mehr anzeigen** und dann sowohl Attribute als auch Ansprüche für **Anzeigename** und **Position** aus. Klicken Sie auf **OK**.
3. Klicken Sie auf **Erstellen**, um den Benutzerflow hinzuzufügen. Dem Namen wird automatisch das Präfix *B2C_1* vorangestellt.

### <a name="test-the-user-flow"></a>Testen des Benutzerflows

1. Wählen Sie den von Ihnen erstellten Benutzerflow aus, um die entsprechende Übersichtsseite zu öffnen, und wählen Sie dann **Benutzerflow ausführen** aus.
1. Wählen Sie für **Anwendung** die Webanwendung *webapp1* aus, die Sie zuvor registriert haben. Als **Antwort-URL** sollte `https://jwt.ms` angezeigt werden.
1. Klicken Sie auf **Benutzerflow ausführen**, und melden Sie sich mit dem zuvor erstellten Konto an.
1. Sie haben jetzt die Möglichkeit, den Anzeigenamen und den Stellentitel für den Benutzer zu ändern. Klicken Sie auf **Weiter**. Das Token wird an `https://jwt.ms` zurückgegeben und sollte Ihnen angezeigt werden.
::: zone-end

::: zone pivot="b2c-custom-policy"
> [!TIP]
> In diesem Artikel wird erläutert, wie Sie Ihren Mandanten manuell einrichten. Sie können den gesamten Prozess in diesem Artikel automatisieren. Durch die Automatisierung wird das [SocialAndLocalAccountsWithMFA-Starter Pack](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack) von Azure AD B2C bereitgestellt, das Journeys zum Registrieren und Anmelden, zur Kennwortzurücksetzung und zur Profilbearbeitung beinhaltet. Befolgen Sie die Anweisungen in der [IEF-Setup-App](https://aka.ms/iefsetup), um die nachfolgende exemplarische Vorgehensweise zu automatisieren.


## <a name="add-signing-and-encryption-keys-for-identity-experience-framework-applications"></a>Hinzufügen von Signatur- und Verschlüsselungsschlüsseln für Identity Experience Framework-Anwendungen

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnisse und Abonnements** aus.
1. Suchen Sie auf der Seite **Portaleinstellungen > Verzeichnisse und Abonnements** das Azure AD B2C-Verzeichnis in der Liste **Verzeichnisname**, und klicken Sie dann auf **Wechseln**.
1. Suchen Sie im Azure-Portal nach **Azure AD B2C**, und wählen Sie diese Option dann aus.
1. Wählen Sie auf der Übersichtsseite unter **Richtlinien** die Option **Identity Experience Framework** aus.

### <a name="create-the-signing-key"></a>Erstellen des Signaturschlüssels

1. Klicken Sie erst auf **Richtlinienschlüssel** und anschließend auf **Hinzufügen**.
1. Klicken Sie unter **Optionen** auf `Generate`.
1. Geben Sie unter **Name**`TokenSigningKeyContainer` ein. Das Präfix `B2C_1A_` wird möglicherweise automatisch hinzugefügt.
1. Wählen Sie **RSA** als **Schlüsseltyp** aus.
1. Wählen Sie unter **Schlüsselverwendung** **Signatur** aus.
1. Klicken Sie auf **Erstellen**.

### <a name="create-the-encryption-key"></a>Erstellen des Verschlüsselungsschlüssels

1. Klicken Sie erst auf **Richtlinienschlüssel** und anschließend auf **Hinzufügen**.
1. Klicken Sie unter **Optionen** auf `Generate`.
1. Geben Sie unter **Name**`TokenEncryptionKeyContainer` ein. Das Präfix `B2C_1A`_ wird möglicherweise automatisch hinzugefügt.
1. Wählen Sie **RSA** als **Schlüsseltyp** aus.
1. Wählen Sie für **Schlüsselverwendung** die Option **Verschlüsselung** aus.
1. Klicken Sie auf **Erstellen**.


## <a name="register-identity-experience-framework-applications"></a>Registrieren von Identity Experience Framework-Anwendungen

Azure AD B2C erfordert, dass Sie zwei Anwendungen registrieren, die zur Registrierung und Anmeldung von Benutzern mit lokalen Konten verwendet werden: *IdentityExperienceFramework* (eine Web-API) und *ProxyIdentityExperienceFramework* (eine native App) mit delegierter Berechtigung für die IdentityExperienceFramework-App. Ihre Benutzer können sich mit einer E-Mail-Adresse oder einem Benutzernamen und einem Kennwort für den Zugriff auf Ihre mandantenregistrierten Anwendungen anmelden, wodurch ein „lokales Konto“ erstellt wird. Lokale Konten sind nur in Ihrem Azure AD B2C-Mandanten vorhanden.

Sie müssen diese beiden Anwendungen nur ein Mal in Ihrem Azure AD B2C-Mandanten registrieren.

### <a name="register-the-identityexperienceframework-application"></a>Registrieren der IdentityExperienceFramework-Anwendung

Zum Registrieren einer Anwendung bei Ihrem Azure AD B2C-Mandanten können Sie die Benutzeroberfläche für **App-Registrierungen** verwenden.

1. Wählen Sie **App-Registrierungen** aus, und wählen Sie dann **Registrierung einer neuen Anwendung** aus.
1. Geben Sie unter **Name**`IdentityExperienceFramework` ein.
1. Wählen Sie unter **Unterstützte Kontotypen** die Option **Nur Konten in diesem Organisationsverzeichnis** aus.
1. Wählen Sie unter **Umleitungs-URI** die Option **Web** aus, und geben Sie dann `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com` ein, wobei `your-tenant-name` für den Domänennamen des Azure AD B2C-Mandanten steht.
1. Aktivieren Sie unter **Berechtigungen** das Kontrollkästchen *Administratoreinwilligung für openid- und offline_access-Berechtigungen erteilen*.
1. Wählen Sie **Registrieren**.
1. Notieren Sie sich die **Anwendungs-ID (Client)** zur Verwendung in einem späteren Schritt.

Stellen Sie nun die API bereit, indem Sie einen Bereich hinzufügen:

1. Wählen Sie im Menü auf der linken Seite unter **Verwalten** die Option **Eine API verfügbar machen** aus.
1. Wählen Sie **Bereich hinzufügen** und dann **Speichern und fortfahren** aus, um den URI der Standardanwendungs-ID zu akzeptieren.
1. Geben Sie die folgenden Werte ein, um einen Bereich zu erstellen, der die benutzerdefinierte Richtlinienausführung in Ihrem Azure AD B2C-Mandanten ermöglicht:
    * **Bereichsname**: `user_impersonation`
    * **Anzeigename der Administratoreinwilligung**: `Access IdentityExperienceFramework`
    * **Beschreibung der Administratoreinwilligung**: `Allow the application to access IdentityExperienceFramework on behalf of the signed-in user.`
1. Wählen Sie **Bereich hinzufügen** aus.

* * *

### <a name="register-the-proxyidentityexperienceframework-application"></a>Registrieren der ProxyIdentityExperienceFramework-Anwendung

1. Wählen Sie **App-Registrierungen** aus, und wählen Sie dann **Registrierung einer neuen Anwendung** aus.
1. Geben Sie unter **Name**`ProxyIdentityExperienceFramework` ein.
1. Wählen Sie unter **Unterstützte Kontotypen** die Option **Nur Konten in diesem Organisationsverzeichnis** aus.
1. Verwenden Sie die Dropdownliste unter **Umleitungs-URI**, um **Öffentlicher Client/nativ (mobil und Desktop)** auszuwählen.
1. Geben Sie `myapp://auth` als **Umleitungs-URI** ein.
1. Aktivieren Sie unter **Berechtigungen** das Kontrollkästchen *Administratoreinwilligung für openid- und offline_access-Berechtigungen erteilen*.
1. Wählen Sie **Registrieren**.
1. Notieren Sie sich die **Anwendungs-ID (Client)** zur Verwendung in einem späteren Schritt.

Geben Sie nun an, dass die Anwendung als öffentlicher Client behandelt werden soll:

1. Wählen Sie im linken Menü unter **Verwalten** die Option **Authentifizierung** aus.
1. Legen Sie unter **Erweiterte Einstellungen** im Abschnitt **Öffentliche Clientflows zulassen** die Option **Folgende Flows für Mobilgerät und Desktop aktivieren** auf **Ja** fest. 
1. Wählen Sie **Speichern** aus.
1. Stellen Sie sicher, dass **„allowPublicClient“: true** im Anwendungsmanifest festgelegt ist.
    1. Wählen Sie im Menü auf der linken Seite unter **Verwalten** die Option **Manifest** aus, um das Anwendungsmanifest zu öffnen.
    1. Suchen Sie nach dem Schlüssel **allowPublicClient**, und vergewissern Sie sich, dass er auf **true** festgelegt ist.

Erteilen Sie nun Berechtigungen für den API-Bereich, den Sie zuvor in der Registrierung *IdentityExperienceFramework* verfügbar gemacht haben:

1. Wählen Sie im Menü auf der linken Seite unter **Verwalten** die Option **API-Berechtigungen** aus.
1. Wählen Sie unter **Konfigurierte Berechtigungen** die Option **Berechtigung hinzufügen** aus.
1. Wählen Sie die Registerkarte **Meine APIs** aus, und wählen Sie dann die Anwendung **IdentityExperienceFramework** aus.
1. Wählen Sie unter **Berechtigung** den Bereich **user_impersonation** aus, den Sie zuvor definiert haben.
1. Wählen Sie **Berechtigungen hinzufügen** aus. Warten Sie einige Minuten, bevor Sie mit dem nächsten Schritt fortfahren.
1. Wählen Sie **Administratorzustimmung für *<Name Ihres Mandanten> erteilen*** aus.
1. Wählen Sie **Ja** aus.
1. Wählen Sie **Aktualisieren** aus, und vergewissern Sie sich, dass für den Bereich unter **Status** der Status „Gewährt für...“ angezeigt wird.

* * *

## <a name="custom-policy-starter-pack"></a>Starter Pack für benutzerdefinierte Richtlinien

Benutzerdefinierte Richtlinien sind ein Satz von XML-Dateien, die Sie in den Azure AD B2C-Mandanten hochladen, um technische Profile und User Journeys zu definieren. Wir stellen Starter Packs mit mehreren vordefinierten Richtlinien bereit, damit Sie schnell loslegen können. Jedes dieser Starter Packs enthält die kleinste Anzahl von technischen Profilen sowie die zum Erreichen der beschriebenen Szenarien benötigten User Journeys:

- **LocalAccounts** ermöglicht die ausschließliche Nutzung von lokalen Konten.
- **SocialAccounts** ermöglicht die ausschließliche Nutzung von Konten sozialer Netzwerke (oder Verbundkonten).
- **SocialAndLocalAccounts** ermöglicht sowohl die Verwendung von lokalen Konten als auch von Konten für soziale Netzwerke.
- **SocialAndLocalAccountsWithMFA** ermöglicht lokale Optionen und Optionen für soziale Netzwerke sowie zur Multi-Factor Authentication.

Die einzelnen Starter Packs enthalten Folgendes:

- **Basisdatei**: Es sind einige Änderungen an der Basisdatei erforderlich. Beispiel: *TrustFrameworkBase.xml*
- **Lokalisierungsdatei:** In dieser Datei werden Konfigurationsänderungen vorgenommen. Beispiel: *TrustFrameworkLocalization.xml*
- **Erweiterungsdatei**: In dieser Datei werden die meisten Konfigurationsänderungen vorgenommen. Beispiel: *TrustFrameworkExtensions.xml*
- **Dateien der vertrauenden Seite**: aufgabenspezifische Dateien, die von Ihrer Anwendung aufgerufen werden. Beispiele: *SignUpOrSignin.xml*, *ProfileEdit.xml*, *PasswordReset.xml*

In diesem Artikel bearbeiten Sie die benutzerdefinierten XML-Richtliniendateien im Starter Pack **SocialAndLocalAccounts**. Wenn Sie einen XML-Editor benötigen, können Sie [Visual Studio Code](https://code.visualstudio.com/download), einen einfachen plattformübergreifenden Editor, ausprobieren.

### <a name="get-the-starter-pack"></a>Abrufen des Starter Packs

Holen Sie sich die Starter Packs für benutzerdefinierte Richtlinien-von GitHub, und aktualisieren Sie dann die XML-Dateien im Starter Pack „SocialAndLocalAccounts“ mit dem Namen Ihres Azure AD B2C-Mandanten.

1. [Laden Sie die ZIP-Datei herunter](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip), oder klonen Sie das Repository:

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
    ```

1. Ersetzen Sie in allen Dateien im Verzeichnis **SocialAndLocalAccounts** die Zeichenfolge `yourtenant` durch den Namen Ihres Azure AD B2C-Mandanten.

    Wenn z. B. der Name des B2C-Mandanten *contosotenant* lautet, werden alle Instanzen von `yourtenant.onmicrosoft.com` in `contosotenant.onmicrosoft.com` geändert.

### <a name="add-application-ids-to-the-custom-policy"></a>Hinzufügen der Anwendungs-IDs zur benutzerdefinierten Richtlinie

Fügen Sie die Anwendung-ID zur Erweiterungsdatei *TrustFrameworkExtensions.xml* hinzu.

1. Öffnen Sie `SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`** , und suchen Sie das Element `<TechnicalProfile Id="login-NonInteractive">`.
1. Ersetzen Sie beide Instanzen von `IdentityExperienceFrameworkAppId` durch die Anwendungs-ID der IdentityExperienceFramework-Anwendung, die Sie zuvor erstellt haben.
1. Ersetzen Sie beide Instanzen von `ProxyIdentityExperienceFrameworkAppId` durch die Anwendungs-ID der ProxyIdentityExperienceFramework-Anwendung, die Sie zuvor erstellt haben.
1. Speichern Sie die Datei .

## <a name="add-facebook-as-an-identity-provider"></a>Hinzufügen von Facebook als Identitätsanbieter

Das Starter Pack **SocialAndLocalAccounts** beinhaltet die Facebook-Anmeldung für soziale Netzwerke. Facebook ist *nicht* erforderlich, um benutzerdefinierte Richtlinien zu verwenden. Die Anwendung wird hier jedoch verwendet, um zu veranschaulichen, wie Sie die Verbundanmeldung bei sozialen Netzwerken in einer benutzerdefinierten Richtlinie aktivieren können.

### <a name="create-facebook-application"></a>Erstellen einer Facebook-Anwendung

Führen Sie die Schritte unter [Erstellen einer Facebook-Anwendung](identity-provider-facebook.md#create-a-facebook-application) aus, um eine *App-ID* und ein *App-Geheimnis* für Facebook zu erhalten. Überspringen Sie die Voraussetzungen und die übrigen Schritte im Artikel [Einrichten der Registrierung und Anmeldung mit einem Facebook-Konto mithilfe von Azure Active Directory B2C](identity-provider-facebook.md). 

### <a name="create-the-facebook-key"></a>Erstellen des Facebook-Schlüssels

Fügen Sie das [App-Geheimnis](identity-provider-facebook.md) der Facebook-Anwendung als Richtlinienschlüssel hinzu. Sie können das App-Geheimnis der Anwendung verwenden, das Sie unter „Voraussetzungen“ in diesem Artikel erstellt haben.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnisse und Abonnements** aus.
1. Suchen Sie auf der Seite **Portaleinstellungen > Verzeichnisse und Abonnements** das Azure AD B2C-Verzeichnis in der Liste **Verzeichnisname**, und klicken Sie dann auf **Wechseln**.
1. Suchen Sie im Azure-Portal nach **Azure AD B2C**, und wählen Sie diese Option dann aus.
1. Wählen Sie auf der Übersichtsseite unter **Richtlinien** die Option **Identity Experience Framework** aus.
1. Klicken Sie erst auf **Richtlinienschlüssel** und anschließend auf **Hinzufügen**.
1. Klicken Sie unter **Optionen** auf `Manual`.
1. Geben Sie unter **Name**`FacebookSecret` ein. Das Präfix `B2C_1A_` wird möglicherweise automatisch hinzugefügt.
1. Geben Sie unter **Geheimnis** das *App-Geheimnis* der Facebook-Anwendung aus developers.facebook.com ein. Bei diesem Wert handelt sich um das Geheimnis und nicht um die Anwendungs-ID.
1. Wählen Sie unter **Schlüsselverwendung** **Signatur** aus.
1. Klicken Sie auf **Erstellen**.

### <a name="update-trustframeworkextensionsxml-in-custom-policy-starter-pack"></a>Aktualisieren von „TrustFrameworkExtensions.xml“ im Starter Pack für benutzerdefinierte Richtlinien
Ersetzen Sie in der Datei `SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`** den Wert `client_id` durch die ID der Facebook-Anwendung, und speichern Sie die Änderungen.

   ```xml
   <TechnicalProfile Id="Facebook-OAUTH">
     <Metadata>
     <!--Replace the value of client_id in this technical profile with the Facebook app ID"-->
       <Item Key="client_id">00000000000000</Item>
   ```


## <a name="upload-the-policies"></a>Hochladen der Richtlinien

1. Wählen Sie in Ihrem B2C-Mandanten im Azure-Portal das Menüelement **Identity Experience Framework** aus.
1. Wählen Sie **Benutzerdefinierte Richtlinie hochladen** aus.
1. Laden Sie die Richtliniendateien in dieser Reihenfolge hoch:
    1. *TrustFrameworkBase.xml*
    2. *TrustFrameworkLocalization.xml*
    3. *TrustFrameworkExtensions.xml*
    4. *SignUpOrSignin.xml*
    5. *ProfileEdit.xml*
    6. *PasswordReset.xml*

Wenn Sie die Dateien hochladen, fügt Azure jeder Datei das Präfix `B2C_1A_` hinzu.

> [!TIP]
> Wenn Ihr XML-Editor Validierungen unterstützt, überprüfen Sie die Dateien anhand des XML-Schemas `TrustFrameworkPolicy_0.3.0.0.xsd` im Stammverzeichnis des Starter Packs. Durch die XML-Schemavalidierung werden vor dem Upload Fehler festgestellt.

## <a name="test-the-custom-policy"></a>Testen der benutzerdefinierten Richtlinie

1. Wählen Sie unter **Benutzerdefinierte Richtlinien** die Richtlinie **B2C_1A_signup_signin** aus.
1. Wählen Sie auf der Übersichtsseite der benutzerdefinierten Richtlinie unter **Anwendung auswählen** die Webanwendung namens *webapp1* aus, die Sie zuvor registriert haben.
1. Stellen Sie sicher, dass die **Antwort-URL**`https://jwt.ms` lautet.
1. Wählen Sie **Jetzt ausführen** aus.
1. Registrieren Sie sich mit einer E-Mail-Adresse.
1. Wählen Sie erneut **Jetzt ausführen** aus.
1. Melden Sie sich zur Bestätigung der richtigen Konfiguration mit demselben Konto an.
1. Wählen Sie erneut **Jetzt ausführen**, und wählen Sie „Facebook“ aus, um sich über Facebook anzumelden und die benutzerdefinierte Richtlinie zu testen.
::: zone-end

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Erstellen eines Benutzerflows für Registrierung und Anmeldung
> * Erstellen eines Benutzerflows für die Profilbearbeitung
> * Erstellen eines Benutzerflows zur Kennwortrücksetzung

Erfahren Sie im Anschluss, wie Sie Azure AD B2C für die Anmeldung und Registrierung von Benutzern in einer Anwendung verwenden. Folgen Sie den unten verlinkten Beispiel-Apps:

- [Konfigurieren einer ASP.NET Core-Beispielwebanwendung](configure-authentication-sample-web-app.md)
- [Konfigurieren einer ASP.NET Core-Beispielwebanwendung, die eine Web-API aufruft](configure-authentication-sample-web-app-with-api.md)
- [Konfigurieren der Authentifizierung in einer Python-Beispielwebanwendung](configure-authentication-sample-python-web-app.md)
- [Konfigurieren einer Single-Page-Beispielanwendung (SPA)](configure-authentication-sample-spa-app.md)
- [Konfigurieren einer Angular-Single-Page-Beispielwebanwendung](configure-authentication-sample-angular-spa-app.md)
- [Konfigurieren einer mobilen Android-Beispielanwendung](configure-authentication-sample-android-app.md)
- [Konfigurieren einer mobilen iOS-Beispielanwendung](configure-authentication-sample-ios-app.md)
- [Konfigurieren der Authentifizierung in einer WPF-Beispieldesktopanwendung](configure-authentication-sample-wpf-desktop-app.md)
- [Aktivieren der Authentifizierung in Ihrer Web-API](enable-authentication-web-api.md)
- [Konfigurieren einer SAML-Anwendung](saml-service-provider.md) 

Weitere Informationen finden Sie außerdem in der [ausführlichen Betrachtung der Architektur von Azure Active Directory B2C](https://www.youtube.com/playlist?list=PLOPotgzC07IKXXCTZcrpuLWbVe3y51kfm).
