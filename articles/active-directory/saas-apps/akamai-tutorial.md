---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD mit Akamai'
description: Hier erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Akamai konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/26/2021
ms.author: jeedes
ms.openlocfilehash: 4d5046f3d783a3e9c7d6035b221c9a55109db179
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132331184"
---
# <a name="tutorial-azure-ad-sso-integration-with-akamai"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD mit Akamai

In diesem Tutorial erfahren Sie, wie Sie Akamai in Azure Active Directory (Azure AD) integrieren. Die Integration von Akamai in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf Akamai hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Akamai anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

Die Integration von Azure Active Directory und Akamai Enterprise Application Access ermöglicht den nahtlosen Zugriff auf in der Cloud oder lokal gehostete Legacyanwendungen. Die integrierte Lösung nutzt alle modernen Azure Active Directory-Funktionen wie [bedingten Azure AD-Zugriff](../conditional-access/overview.md), [Azure AD Identity Protection](../identity-protection/overview-identity-protection.md) und [Azure AD Identity Governance](../governance/identity-governance-overview.md), um den Zugriff auf Legacyanwendungen ohne App-Änderungen oder Agent-Installation zu ermöglichen.

Die folgende Abbildung zeigt, wie sich Akamai EAA in das allgemeine Szenario für sicheren Hybridzugriff einfügt.

![Akamai EAA fügt sich in das allgemeine Szenario für sicheren Hybridzugriff ein.](./media/header-akamai-tutorial/introduction-1.png)

### <a name="key-authentication-scenarios"></a>Wichtige Authentifizierungsszenarien

Neben der nativen Azure Active Directory-Integrationsunterstützung für moderne Authentifizierungsprotokolle wie OpenID Connect, SAML und WS-Fed erweitert Akamai EAA den sicheren Zugriff für legacybasierte Authentifizierungs-Apps sowohl für den internen als auch für den externen Zugriff mit Azure AD, um für diese Anwendungen moderne Szenarien wie etwa Zugriff ohne Kennwort zu ermöglichen. Dies schließt Folgendes ein:

* Apps mit headerbasierter Authentifizierung
* Remotedesktop
* SSH (Secure Shell)
* Apps mit Kerberos-Authentifizierung
* VNC (Virtual Network Computing)
* Apps mit anonymer Authentifizierung oder ohne integrierte Authentifizierung
* Apps mit NTLM-Authentifizierung (Schutz mit doppelten Eingabeaufforderungen für den Benutzer)
* Formularbasierte Anwendung (Schutz mit doppelten Eingabeaufforderungen für den Benutzer)

### <a name="integration-scenarios"></a>Integrationsszenarien

Die Partnerschaft zwischen Microsoft und Akamai EAA ermöglicht eine flexible Erfüllung Ihrer geschäftlichen Anforderungen. Hierzu werden basierend auf Ihrer geschäftlichen Anforderung verschiedene Integrationsszenarien unterstützt. Dies ermöglicht eine Zero-Day-Abdeckung für sämtliche Anwendungen mit anschließender schrittweiser Klassifizierung und Konfiguration geeigneter Richtlinienklassifizierungen.

#### <a name="integration-scenario-1"></a>Integrationsszenario 1

Akamai EAA wird als einzelne Anwendung in der Azure AD-Instanz konfiguriert. Der Administrator kann die Richtlinie für bedingten Zugriff für die Anwendung konfigurieren, und Benutzer können auf das Akamai EAA-Portal zugreifen, sobald die Bedingungen erfüllt sind.

**Vorteile:**

* Der IDP muss nur einmal konfiguriert werden.

**Nachteile:**

* Benutzer verfügen über zwei Anwendungsportale.

* Abdeckung aller Anwendungen durch eine einzelne allgemeine Richtlinie für bedingten Zugriff

![Integrationsszenario 1](./media/header-akamai-tutorial/scenario-1.png)

#### <a name="integration-scenario-2"></a>Integrationsszenario 2

Die Akamai EAA-Anwendung wird individuell im Azure AD-Portal eingerichtet. Der Administrator kann eine individuelle Richtlinie für bedingten Zugriff für die Anwendungen konfigurieren, und Benutzer können direkt zur jeweiligen Anwendung umgeleitet werden, sobald die Bedingungen erfüllt sind.

**Vorteile:**

* Sie können individuelle Richtlinien für bedingten Zugriff definieren.

* Alle Apps werden im Office 365-Waffelmenü und im Bereich „myApps.microsoft.com“ dargestellt.


**Nachteile:**

* Sie müssen mehrere IDPs konfigurieren.

![Integrationsszenario 2](./media/header-akamai-tutorial/scenario-2.png)

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Akamai-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

- Akamai unterstützt IDP-initiiertes einmaliges Anmelden.

#### <a name="important"></a>Wichtig

Alle unten aufgeführten Einrichtungsschritte gelten sowohl für das **Integrationsszenario 1** als auch für das **Szenario 2**. Für das **Integrationsszenario 2** müssen Sie einen individuellen IdP in der Akamai EAA-Instanz einrichten, und die URL-Eigenschaft muss so geändert werden, dass sie auf die Anwendungs-URL verweist.

![Screenshot der Registerkarte „Allgemein“ für AZURESSO-SP in Akamai Enterprise Application Access. Das Feld mit der Konfigurations-ID für die Authentifizierung ist hervorgehoben.](./media/header-akamai-tutorial/important.png)

## <a name="add-akamai-from-the-gallery"></a>Hinzufügen von Akamai über den Katalog

Zum Konfigurieren der Integration von Akamai in Azure AD müssen Sie Akamai aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Akamai** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Akamai** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-akamai"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Akamai

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Akamai mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Akamai eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Akamai die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    * **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    * **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Akamai](#configure-akamai-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    * **[Einrichten des IDP](#setting-up-idp)**
    * **[Headerbasierte Authentifizierung](#header-based-authentication)**
    * **[Remotedesktop](#remote-desktop)**
    * **[SSH](#ssh)**
    * **[Kerberos-Authentifizierung](#kerberos-authentication)**
    * **[Erstellen eines Akamai-Testbenutzers](#create-akamai-test-user)** , um in Akamai eine Entsprechung von B. Simon zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Akamai** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Geben Sie im Abschnitt **Grundlegende SAML-Konfiguration** die Werte in die folgenden Felder ein, wenn Sie die Anwendung im **IDP**-initiierten Modus konfigurieren möchten:

    a. Geben Sie im Textfeld **Bezeichner** eine URL im folgenden Format ein: `https://<Yourapp>.login.go.akamai-access.com/saml/sp/response`

    b. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https:// <Yourapp>.login.go.akamai-access.com/saml/sp/response`

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Aktualisieren Sie diese Werte mit dem eigentlichen Bezeichner und der Antwort-URL. Diese Werte erhalten Sie vom [Supportteam für den Akamai-Client](https://www.akamai.com/us/en/contact-us/). Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

1. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** zu **Verbundmetadaten-XML**, und wählen Sie **Herunterladen** aus, um das Zertifikat herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

1. Kopieren Sie im Abschnitt **Akamai einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

In diesem Abschnitt erstellen Sie im Azure-Portal einen Testbenutzer mit dem Namen B. Simon.

1. Wählen Sie im linken Bereich des Microsoft Azure-Portals **Azure Active Directory** > **Benutzer** > **Alle Benutzer** aus.
1. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.
1. Führen Sie unter den Eigenschaften für **Benutzer** die folgenden Schritte aus:
   1. Geben Sie im Feld **Name** die Zeichenfolge `B.Simon` ein.  
   1. Geben Sie im Feld **Benutzername** die Zeichenfolge username@companydomain.extension ein. Beispiel: `B.Simon@contoso.com`.
   1. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**, und notieren Sie sich den Wert aus dem Feld **Kennwort**.
   1. Klicken Sie auf **Erstellen**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Akamai gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Akamai** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-akamai-sso"></a>Konfigurieren des einmaligen Anmeldens für Akamai

### <a name="setting-up-idp"></a>Einrichten des IDP

**Akamai EAA: IDP-Konfiguration**

1. Melden Sie sich bei der **Enterprise Application Access-Konsole von Akamai** an.
1. Wählen Sie in der **EAA-Konsole von Akamai** die Optionen **Identity** > **Identity Providers** (Identität > Identitätsanbieter) aus, und klicken Sie auf **Add Identity Provider** (Identitätsanbieter hinzufügen).

    ![Screenshot des Fensters „Identity Providers“ (Identitätsanbieter) der Akamai EAA-Konsole. Wählen Sie „Identity Providers“ (Identitätsanbieter) im Menü „Identity“ und dann „Add Identity Provider“ (Identitätsanbieter hinzufügen) aus.](./media/header-akamai-tutorial/configure-1.png)

1. Führen Sie im Dialogfeld **Create New Identity Provider** (Neuen Identitätsanbieter erstellen) die folgenden Schritte aus:

    ![Screenshot des Dialogfelds „Create New Identity Providers“ (Neuen Identitätsanbieter erstellen) in der Akamai EAA-Konsole.](./media/header-akamai-tutorial/configure-2.png)

    a. Geben Sie einen **eindeutigen Namen** ein.

    b. Wählen Sie **Third Party SAML** (Drittanbieter-SAML) aus, und klicken Sie auf **Create Identity Provider and Configure** (Identitätsanbieter erstellen und konfigurieren).

### <a name="general-settings"></a>Allgemeine Einstellungen

1. **Identity Intercept** (Identität abfangen): Geben Sie die SP-basierte URL ein. Diese wird für die Azure AD-Konfiguration verwendet.

    > [!NOTE]
    > Sie können eine eigene benutzerdefinierte Domäne verwenden. (Dafür sind ein DNS-Eintrag und ein Zertifikat erforderlich.) In diesem Beispiel verwenden wir die Akamai-Domäne.

1. **Akamai Cloud Zone** (Akamai-Cloudzone): Wählen Sie die entsprechende Cloudzone aus.
1. **Certificate Validation** (Zertifikatüberprüfung): Lesen Sie die Akamai-Dokumentation (optional).

    ![Screenshot der Registerkarte „General“ (Allgemein) der Akamai EAA-Konsole mit Einstellungen für „Identity Intercept“ (Identität abfangen), „Akamai Cloud Zone“ (Akamai-Cloudzone) und „Certificate Validation“ (Zertifikatüberprüfung).](./media/header-akamai-tutorial/configure-3.png)

### <a name="authentication-configuration"></a>Authentifizierungskonfiguration

1. URL: Geben Sie die gleiche URL wie unter „Identity Intercept“ (Identität abfangen) ein. (An diese URL werden Benutzer nach der Authentifizierung weitergeleitet.)
2. Logout URL (Abmelde-URL): Aktualisieren Sie die Abmelde-URL.
3. Sign SAML Request (SAML-Anforderung signieren): Diese Option ist standardmäßig deaktiviert.
4. Fügen Sie für die IDP-Metadatendatei die Anwendung in der Azure AD-Konsole hinzu.

    ![Screenshot der Authentifizierungskonfiguration der Akamai EAA-Konsole mit Einstellungen für „URL“, „Logout URL“ (Abmelde-URL), „Sign SAML Request“ (SAML-Anforderung signieren) und „IDP Metadata File“ (IDP-Metadatendatei).](./media/header-akamai-tutorial/configure-4.png)

### <a name="session-settings"></a>Sitzungseinstellungen

Übernehmen Sie die Standardeinstellungen.

![Screenshot des Dialogfelds „Session Settings“ (Sitzungseinstellungen) der Akamai EAA-Konsole.](./media/header-akamai-tutorial/session-settings.png)

### <a name="directories"></a>Verzeichnisse

Überspringen Sie die Verzeichniskonfiguration.

![Screenshot der Registerkarte „Directories“ (Verzeichnisse) der Akamai EAA-Konsole.](./media/header-akamai-tutorial/directories.png)

### <a name="customization-ui"></a>Benutzeroberfläche für die Anpassung

Der IDP kann auf Wunsch angepasst werden.

![Screenshot der Registerkarte „Customization“ (Anpassung) der Akamai EAA-Konsole mit Einstellungen für „Customize UI“ (Benutzeroberfläche anpassen), „Language settings“ (Spracheinstellungen) und „Themes“ (Designs).](./media/header-akamai-tutorial/customization.png)

### <a name="advanced-settings"></a>Erweiterte Einstellungen

Überspringen Sie die erweiterten Einstellungen. (Weitere Informationen finden Sie in der Akamai-Dokumentation.)

![Screenshot der Registerkarte „Advanced Settings“ (Erweiterte Einstellungen) der Akamai-EAA-Konsole mit Einstellungen für „EAA Client“ (EAA-Client), „Advanced“ (Erweitert) und „OIDC to SAML bridging“ (OIDC zu SAML-Bridging).](./media/header-akamai-tutorial/advance-settings.png)

### <a name="deployment"></a>Bereitstellung

1. Klicken Sie auf die Option zum Bereitstellen des Identitätsanbieters.

    ![Screenshot der Registerkarte „Deployment“ (Bereitstellung) der Akamai EAA-Konsole mit der Schaltfläche „Deploy Identity provider“ (Identitätsanbieter bereitstellen).](./media/header-akamai-tutorial/deployment.png)

2. Vergewissern Sie sich, dass die Bereitstellung erfolgreich war.

### <a name="header-based-authentication"></a>Headerbasierte Authentifizierung

Headerbasierte Authentifizierung von Akamai

1. Wählen Sie im Assistenten zum Hinzufügen von Anwendungen die Option **Custom HTTP** (Benutzerdefiniertes HTTP) aus.

    ![Screenshot des Assistenten zum Hinzufügen von Anwendungen der Akamai-EAA-Konsole mit „Custom HTTP“ (Benutzerdefiniertes HTTP) im Abschnitt „Access Apps“ (Zugriff auf Apps).](./media/header-akamai-tutorial/configure-5.png)

2. Geben Sie unter **Application Name** (Anwendungsname) einen Anwendungsnamen und unter **Description** (Beschreibung) eine Beschreibung ein.

    ![Screenshot des Dialogfelds „Custom HTTP App“ (Benutzerdefiniertes HTTP-App) mit Einstellungen für „Application Name“ (Anwendungsname) und „Description“ (Beschreibung).](./media/header-akamai-tutorial/configure-6.png)

    ![Screenshot der Registerkarte „General“ (Allgemein) der Akamai EAA-Konsole mit allgemeinen Einstellungen für „MYHEADERAPP“.](./media/header-akamai-tutorial/configure-7.png)

    ![Screenshot der Akamai-EAA-Konsole mit Einstellungen für „Certificate“ (Zertifikat) und „Location“ (Speicherort).](./media/header-akamai-tutorial/configure-8.png)

#### <a name="authentication"></a>Authentifizierung

1. Wählen Sie die Registerkarte **Authentication** (Authentifizierung) aus.

    ![Screenshot der Akamai-EAA-Konsole, auf der die Registerkarte „Authentication“ (Authentifizierung) ausgewählt ist.](./media/header-akamai-tutorial/configure-9.png)

2. Weisen Sie den **Identitätsanbieter** zu.

    ![Screenshot der Registerkarte „Authentication“ (Authentifizierung) der Akamai EAA-Konsole für MYHEADERAPP, wobei der Identitätsanbieter auf „Azure AD SSO“ festgelegt ist.](./media/header-akamai-tutorial/configure-10.png)

#### <a name="services"></a>Dienste

Klicken Sie auf „Save“ (Speichern), und gehen Sie zu „Authentication“ (Authentifizierung).

![Screenshot der Registerkarte „Services“ (Dienste) der Akamai EAA-Konsole für MYHEADERAPP mit der Schaltfläche „Save“ (Speichern) und der Schaltfläche zum Wechseln zu „Advanced Settings“ (Erweiterte Einstellungen) in der unteren rechten Ecke.](./media/header-akamai-tutorial/configure-11.png)

#### <a name="advanced-settings"></a>Erweiterte Einstellungen

1. Geben Sie unter **Custom HTTP Headers** (Benutzerdefinierte HTTP-Header) Werte für **Custom Header** (Benutzerdefinierter Header) und **SAML Attribute** (SAML-Attribut) ein.

    ![Screenshot der Registerkarte „Advanced Settings“ (Erweiterte Einstellungen) der Akamai-EAA-Konsole, auf der das Feld „SSO Logged URL“ (SSO-protokollierte URL) unter „Authentication“ (Authentifizierung) hervorgehoben ist.](./media/header-akamai-tutorial/configure-12.png)

1. Klicken Sie auf die Schaltfläche **Save and go to Deployment** (Speichern und zur Bereitstellung wechseln).

    ![Screenshot der Registerkarte „Advanced Settings“ (Erweiterte Einstellungen) der Akamai EAA-Konsole mit der Schaltfläche „Save“ (Speichern) und der Schaltfläche zum Wechseln zu „Deployment“ (Bereitstellung) in der unteren rechten Ecke.](./media/header-akamai-tutorial/configure-13.png)

#### <a name="deploy-the-application"></a>Bereitstellen der Anwendung

1. Klicken Sie auf die Schaltfläche **Deploy Application** (Anwendung bereitstellen).

    ![Screenshot der Registerkarte „Deployment“ (Bereitstellung) der Akamai EAA-Konsole mit der Schaltfläche „Deploy application“ (Anwendung bereitstellen).](./media/header-akamai-tutorial/configure-14.png)

1. Vergewissern Sie sich, dass die Anwendung erfolgreich bereitgestellt wurde.

    ![Screenshot der Registerkarte „Deployment“ (Bereitstellung) der Akamai EAA-Konsole mit der Anwendungsstatusmeldung: „Application Successfully Deployed“ (Anwendung erfolgreich bereitgestellt).](./media/header-akamai-tutorial/configure-15.png)

1. Endbenutzererfahrung:

    ![Screenshot des Begrüßungsbildschirms für „myapps.microsoft.com“ mit einem Hintergrundbild und einem Anmeldedialogfeld.](./media/header-akamai-tutorial/end-user-1.png)

    ![Screenshot mit einem Teil eines Apps-Fensters mit Symbolen für Add-In, HRWEB, Akamai – CorpApps, Ausgaben, Gruppen und Zugriffsüberprüfungen. ](./media/header-akamai-tutorial/end-user-2.png)

1. Bedingter Zugriff:

    ![Screenshot der Meldung: Anmeldeanforderung bestätigen. Wir haben eine Benachrichtigung an Ihr mobiles Gerät gesendet. Bitte antworten Sie, um fortzufahren.](./media/header-akamai-tutorial/conditional-access-1.png)

    ![Screenshot des Bildschirms „Anwendungen“ mit einem Symbol für MyHeaderApp.](./media/header-akamai-tutorial/conditional-access-2.png)

#### <a name="remote-desktop"></a>Remotedesktop

1. Wählen Sie im Assistenten zum Hinzufügen von Anwendungen die Option **RDP** aus.

    ![Screenshot des Assistenten zum Hinzufügen von Anwendungen der Akamai-EAA-Konsole mit „RDP“ in der Liste der Apps im Abschnitt „Access Apps“ (Zugriff auf Apps).](./media/header-akamai-tutorial/configure-16.png)

1. Geben Sie unter **Application Name** (Anwendungsname) einen Anwendungsnamen und unter **Description** (Beschreibung) eine Beschreibung ein.

    ![Screenshot des Dialogfelds „RDP App“ (RDP-App) mit Einstellungen für „Application Name“ (Anwendungsname) und „Description“ (Beschreibung).](./media/header-akamai-tutorial/configure-17.png)

    ![Screenshot der Registerkarte „General“ (Allgemein) der Akamai EAA-Konsole mit Anwendungsidentitätseinstellungen für „SECRETRDPAPP“.](./media/header-akamai-tutorial/configure-18.png)

1. Geben Sie den Connector dafür an.

    ![Screenshot der Akamai-EAA-Konsole mit Einstellungen für „Certificate“ (Zertifikat) und „Location“ (Speicherort). Zugeordnete Connectors sind auf „USWST-CON1“ festgelegt.](./media/header-akamai-tutorial/configure-19.png)

#### <a name="authentication"></a>Authentifizierung

Klicken Sie auf **Save and go to Services** (Speichern und zu Diensten wechseln).

![Screenshot der Registerkarte „Authentication“ (Authentifizierung) der Akamai EAA-Konsole für SECRETRDPAPP mit der Schaltfläche „Save“ (Speichern) und der Schaltfläche zum Wechseln zu „Services“ (Dienste) in der unteren rechten Ecke.](./media/header-akamai-tutorial/configure-20.png)

#### <a name="services"></a>Dienste

Klicken Sie auf **Save and go to Advanced Settings** (Speichern und zu erweiterten Einstellungen wechseln).

![Screenshot der Registerkarte „Services“ (Dienste) der Akamai EAA-Konsole für SECRETRDPAPP mit der Schaltfläche „Save“ (Speichern) und der Schaltfläche zum Wechseln zu „Advanced Settings“ (Erweiterte Einstellungen) in der unteren rechten Ecke.](./media/header-akamai-tutorial/configure-21.png)

#### <a name="advanced-settings"></a>Erweiterte Einstellungen

1. Klicken Sie auf **Save and go to Deployment** (Speichern und zur Bereitstellung wechseln).

    ![Screenshot der Registerkarte „Advanced Settings“ (Erweiterte Einstellungen) der Akamai EAA-Konsole für SECRETRDPAPP mit den Einstellungen für die Konfiguration des Remotedesktops.](./media/header-akamai-tutorial/configure-22.png)

    ![Screenshot der Registerkarte „Advanced Settings“ (Erweiterte Einstellungen) der Akamai EAA-Konsole für SECRETRDPAPP mit den Einstellungen für „Authentication“ (Authentifizierung) und die Konfiguration der Integritätsüberprüfung.](./media/header-akamai-tutorial/configure-23.png)

    ![Screenshot der Registerkarte „Custom HTTP headers“ (Benutzerdefinierte HTTP-Header) der Akamai EAA-Konsole für SECRETRDPAPP mit der Schaltfläche „Save“ (Speichern) und der Schaltfläche zum Wechseln zu „Deployment“ (Bereitstellung) in der unteren rechten Ecke.](./media/header-akamai-tutorial/configure-24.png)

1. Endbenutzererfahrung

    ![Screenshot eines „myapps.microsoft.com“-Fensters mit einem Hintergrundbild und einem Anmeldedialogfeld.](./media/header-akamai-tutorial/end-user-3.png)

    ![Screenshot des Fensters „Apps“ von „myapps.microsoft.com“ mit Symbolen für Add-In, HRWEB, Akamai – CorpApps, Ausgaben, Gruppen und Zugriffsüberprüfungen.](./media/header-akamai-tutorial/end-user-2.png)

1. Bedingter Zugriff

    ![Screenshot der Meldung über bedingten Zugriff: Anmeldeanforderung bestätigen. Wir haben eine Benachrichtigung an Ihr mobiles Gerät gesendet. Bitte antworten Sie, um fortzufahren.](./media/header-akamai-tutorial/conditional-access-4.png)

    ![Screenshot des Bildschirms „Applications“ (Anwendungen) mit Symbolen für MyHeaderApp und SecretRDPApp.](./media/header-akamai-tutorial/conditional-access-5.png)

    ![Screenshot des Windows Server 2012 RS-Bildschirms mit generischen Benutzersymbolen. Die Symbole für „Administrator“, „user0“ und „user1“ zeigen, dass diese Benutzer angemeldet sind.](./media/header-akamai-tutorial/conditional-access-6.png)

1. Alternativ können Sie die RDP-Anwendungs-URL auch direkt eingeben.

#### <a name="ssh"></a>SSH

1. Navigieren Sie zu „Add Applications“ (Anwendungen hinzufügen), und wählen Sie **SSH** aus.

    ![Screenshot des Assistenten zum Hinzufügen von Anwendungen der Akamai-EAA-Konsole mit „SSH“ in der Liste der Apps im Abschnitt „Access Apps“ (Zugriff auf Apps).](./media/header-akamai-tutorial/configure-25.png)

1. Geben Sie unter **Application Name** (Anwendungsname) einen Anwendungsnamen und unter **Description** (Beschreibung) eine Beschreibung ein.

    ![Screenshot: Dialogfeld „SSH App“ (SSH-App) mit Einstellungen für „Application Name“ (Anwendungsname) und „Description“ (Beschreibung)](./media/header-akamai-tutorial/configure-26.png)

1. Konfigurieren Sie die Anwendungsidentität.

    ![Screenshot der Registerkarte „General“ (Allgemein) der Akamai EAA-Konsole mit Anwendungsidentitätseinstellungen für „SSH-SECURE“.](./media/header-akamai-tutorial/configure-27.png)

    a. Geben Sie einen Namen und eine Beschreibung ein.

    b. Geben Sie die Anwendungsserver-IP bzw. den FQDN und Port für SSH an.

    c. Geben Sie den SSH-Benutzernamen und die SSH-Passphrase an. *Diese finden Sie in Akamai EAA.

    d. Geben Sie den Namen des externen Hosts an.

    e. Geben Sie den Speicherort für den Connector an, und wählen Sie den Connector aus.

#### <a name="authentication"></a>Authentifizierung

Klicken Sie auf **Save and go to Services** (Speichern und zu Diensten wechseln).

![Screenshot der Registerkarte „Authentication“ (Authentifizierung) der Akamai EAA-Konsole für SSH-SECURE mit der Schaltfläche „Save“ (Speichern) und der Schaltfläche zum Wechseln zu „Services“ (Dienste) in der unteren rechten Ecke.](./media/header-akamai-tutorial/configure-28.png)

#### <a name="services"></a>Dienste

Klicken Sie auf **Save and go to Advanced Settings** (Speichern und zu erweiterten Einstellungen wechseln).

![Screenshot der Registerkarte „Services“ (Dienste) der Akamai EAA-Konsole für SSH-SECURE mit der Schaltfläche „Save“ (Speichern) und der Schaltfläche zum Wechseln zu „Advanced Settings“ (Erweiterte Einstellungen) in der unteren rechten Ecke.](./media/header-akamai-tutorial/configure-29.png)

#### <a name="advanced-settings"></a>Erweiterte Einstellungen

Klicken Sie auf „Save and go to Deployment“ (Speichern und zur Bereitstellung wechseln).

![Screenshot der Registerkarte „Advanced Settings“ (Erweiterte Einstellungen) der Akamai EAA-Konsole für SSH-SECURE mit den Einstellungen für „Authentication“ (Authentifizierung) und die Konfiguration der Integritätsüberprüfung.](./media/header-akamai-tutorial/configure-30.png)

![Screenshot der Registerkarte „Custom HTTP headers“ (Benutzerdefinierte HTTP-Header) der Akamai EAA-Konsole für SSH-SECURE mit der Schaltfläche „Save“ (Speichern) und der Schaltfläche zum Wechseln zu „Deployment“ (Bereitstellung) in der unteren rechten Ecke.](./media/header-akamai-tutorial/configure-31.png)

#### <a name="deployment"></a>Bereitstellung

1. Klicken Sie auf **Deploy Application** (Anwendung bereitstellen).

    ![Screenshot der Registerkarte „Deployment“ (Bereitstellung) der Akamai EAA-Konsole für SSH-SECURE mit der Schaltfläche „Deploy application“ (Anwendung bereitstellen).](./media/header-akamai-tutorial/configure-32.png)

1. Endbenutzererfahrung

    ![Screenshot des Anmeldedialogfelds eines myapps.microsoft.com-Fensters.](./media/header-akamai-tutorial/end-user-3.png)

    ![Screenshot des Fensters „Apps“ von „myapps.microsoft.com“ mit Symbolen für Add-In, HRWEB, Akamai – CorpApps, Ausgaben, Gruppen und Zugriffsüberprüfungen.](./media/header-akamai-tutorial/end-user-4.png)

1. Bedingter Zugriff

    ![Screenshot der Meldung: Anmeldeanforderung bestätigen. Wir haben eine Benachrichtigung an Ihr mobiles Gerät gesendet. Bitte antworten Sie, um fortzufahren.](./media/header-akamai-tutorial/conditional-access-4.png)

    ![Screenshot des Bildschirms „Applications“ (Anwendungen) mit Symbolen für MyHeaderApp, SSH Secure und SecretRDPApp.](./media/header-akamai-tutorial/conditional-access-7.png)

    ![Screenshot eines Befehlsfensters für „ssh-secure-go.akamai-access.com“ mit einer Kennworteingabeaufforderung.](./media/header-akamai-tutorial/conditional-access-8.png)

    ![Screenshot eines Befehlsfensters für „ssh-secure-go.akamai-access.com“ mit Informationen über die Anwendung und einer Eingabeaufforderung für Befehle.](./media/header-akamai-tutorial/conditional-access-9.png)

### <a name="kerberos-authentication"></a>Kerberos-Authentifizierung

Im folgenden Beispiel wird ein interner Webserver (<code>http://frp-app1.superdemo.live</code>) veröffentlicht und einmaliges Anmelden mit KCD aktiviert.

#### <a name="general-tab"></a>Registerkarte Allgemein

![Screenshot der Registerkarte „General“ (Allgemein) der Akamai EAA-Konsole für MYKERBOROSAPP.](./media/header-akamai-tutorial/general-tab.png)

#### <a name="authentication-tab"></a>Registerkarte „Authentication“ (Authentifizierung)

Weisen Sie den Identitätsanbieter zu.

![Screenshot der Registerkarte „Authentication“ (Authentifizierung) der Akamai EAA-Konsole für MYKERBOROSAPP, wobei „Identity provider“ (Identitätsanbieter) auf „Azure AD SSO“ festgelegt ist.](./media/header-akamai-tutorial/authentication-tab.png)

#### <a name="services-tab"></a>Registerkarte „Services“ (Dienste)

![Screenshot der Registerkarte „Services“ (Dienste) der Akamai EAA-Konsole für MYKERBOROSAPP.](./media/header-akamai-tutorial/services-tab.png)

#### <a name="advanced-settings"></a>Erweiterte Einstellungen

![Screenshot der Registerkarte „Advanced Settings“ (Erweiterte Einstellungen) der Akamai EAA-Konsole für MYKERBOROSAPP mit Einstellungen für „Related Applications“ (Verwandte Anwendungen) und „Authentication“ (Authentifizierung).](./media/header-akamai-tutorial/advance-settings-2.png)

> [!NOTE]
> Der SPN für den Webserver muss im Format SPN@Domain angegeben werden. (In dieser Demo wird beispielsweise `HTTP/frp-app1.superdemo.live@SUPERDEMO.LIVE` verwendet.) Lassen Sie die restlichen Einstellungen unverändert.

#### <a name="deployment-tab"></a>Registerkarte „Deployment“ (Bereitstellung)

![Screenshot der Registerkarte „Deployment“ (Bereitstellung) der Akamai EAA-Konsole für MYKERBOROSAPP mit der Schaltfläche „Deploy application“ (Anwendung bereitstellen).](./media/header-akamai-tutorial/deployment-tab.png)

#### <a name="adding-directory"></a>Hinzufügen eines Verzeichnisses

1. Wählen Sie in der Dropdownliste die Option **AD** aus.

    ![Screenshot des Fensters „Directories“ (Verzeichnisse) der Akamai EAA-Konsole mit dem Dialogfeld „Create New Directory“ (Neues Verzeichnis erstellen), in dem „AD“ in der Dropdownliste für den Verzeichnistyp ausgewählt ist.](./media/header-akamai-tutorial/configure-33.png)

1. Geben Sie die erforderlichen Daten an.

    ![Screenshot des Fensters „SUPERDEMOLIVE“ der Akamai EAA-Konsole mit Einstellungen für „DirectoryName“ (Verzeichnisname), „Directory Service“ (Verzeichnisdienst), Connector und „Attribute mapping“ (Attributzuordnung).](./media/header-akamai-tutorial/configure-34.png)

1. Überprüfen Sie die Verzeichniserstellung.

    ![Screenshot des Fensters „Directories“ (Verzeichnisse) der Akamai EAA-Konsole mit dem hinzugefügten Verzeichnis „superdemo.live“.](./media/header-akamai-tutorial/directory-domain.png)

1. Fügen Sie die Gruppen/Organisationseinheiten hinzu, die Zugriff benötigen.

    ![Screenshot der Einstellungen für das Verzeichnis „superdemo.live“. Das Symbol, das Sie zum Hinzufügen von Gruppen oder Organisationseinheiten auswählen, wird hervorgehoben.](./media/header-akamai-tutorial/add-group.png)

1. Hier heißt die Gruppe „EAAGroup“ und hat ein einzelnes Mitglied.

    ![Screenshot des Fensters „GROUPS ON SUPERDEMOLIVE DIRECTORY“ (Gruppen im Verzeichnis „superdemo.live“) der Akamai EAA-Konsole. „EAAGroup“ mit 1 Benutzer ist unter den Gruppen aufgeführt.](./media/header-akamai-tutorial/eaagroup.png)

1. Fügen Sie das Verzeichnis dem Identitätsanbieter hinzu, indem Sie unter **Identity** > **Identity Providers** (Identität > Identitätsanbieter) auf der Registerkarte **Directories** (Verzeichnisse) auf **Assign directory** (Verzeichnis zuweisen) klicken.

    ![Screenshot der Registerkarte „Directories“ (Verzeichnisse) der Akamai EAA-Konsole für Azure AD SSO, auf der „superdemo.live“ in der Liste der derzeit zugewiesenen Verzeichnisse angezeigt wird.](./media/header-akamai-tutorial/assign-directory.png)

### <a name="configure-kcd-delegation-for-eaa-walkthrough"></a>Konfigurieren der KCD-Delegierung für EAA: Exemplarische Vorgehensweise

#### <a name="step-1-create-an-account"></a>Schritt 1: Erstellen eines Kontos 

1. In dem Beispiel wird ein Konto namens **EAADelegation** verwendet. Sie können hierzu das Snap-In **Active Directory-Benutzer und -Computer** verwenden.

    ![Screenshot der Registerkarte „Directories“ (Verzeichnisse) der Akamai EAA-Konsole für Azure AD SSO. Das Verzeichnis „superdemo.live“ ist unter den derzeit zugewiesenen Verzeichnissen aufgeführt.](./media/header-akamai-tutorial/assign-directory.png)

    > [!NOTE]
    > Der Benutzername muss in einem bestimmten Format vorliegen, das auf dem für **Identity Intercept** (Identität abfangen) angegebenen Namen basiert. In Abbildung 1 ist zu sehen, dass dieser **corpapps.login.go.akamai-access.com** lautet.

1. Der Benutzeranmeldename lautet: `HTTP/corpapps.login.go.akamai-access.com`

    ![Screenshot der EAADelegation-Eigenschaften, wobei der Vorname auf „EAADelegation“ und der Anmeldename des Benutzers auf „HTTP/corpapps.login.go.akamai-access.com“ festgelegt ist.](./media/header-akamai-tutorial/eaadelegation.png)

#### <a name="step-2-configure-the-spn-for-this-account"></a>Schritt 2: Konfigurieren des SPN für dieses Konto

1. In diesem Beispiel lautet der SPN wie folgt:

2. setspn -s **Http/corpapps.login.go.akamai-access.com eaadelegation**

    ![Screenshot einer Administratoreingabeaufforderung mit den Ergebnissen des Befehls „setspn -s Http/corpapps.login.go.akamai-access.com eaadelegation“.](./media/header-akamai-tutorial/spn.png)

#### <a name="step-3-configure-delegation"></a>Schritt 3: Konfigurieren der Delegierung

1. Klicken Sie für das Konto „EAADelegation“ auf die Registerkarte für die Delegierung.

    ![Screenshot einer Administratoreingabeaufforderung mit dem Befehl zum Konfigurieren des SPN.](./media/header-akamai-tutorial/delegation.png)

    * Verwenden Sie ein beliebiges Authentifizierungsprotokoll.
    * Klicken Sie auf „Add“ (Hinzufügen), und fügen Sie das App-Pool-Konto für die Kerberos-Website hinzu. Bei korrekter Konfiguration sollte es automatisch zum korrekten SPN aufgelöst werden.

#### <a name="step-4-create-a-keytab-file-for-akamai-eaa"></a>Schritt 4: Erstellen einer Schlüsseltabellendatei für Akamai EAA

1. Die generische Syntax lautet wie folgt:

1. ktpass /out ActiveDirectorydomain.keytab  /princ `HTTP/yourloginportalurl@ADDomain.com`  /mapuser serviceaccount@ADdomain.com /pass +rdnPass  /crypto All /ptype KRB5_NT_PRINCIPAL

1. Erläuterung des Beispiels

    | Codeausschnitt | Erklärung |
    | - | - |
    | Ktpass /out EAADemo.keytab | // Der Name der ausgegebenen Schlüsseltabellendatei. |
    | /princ HTTP/corpapps.login.go.akamai-access.com@superdemo.live | // HTTP/yourIDPName@YourdomainName |
    | /mapuser eaadelegation@superdemo.live | // EAA-Delegierungskonto |
    | /pass RANDOMPASS | // Kennwort des EAA-Delegierungskontos |
    | /crypto All ptype KRB5_NT_PRINCIPAL | // Siehe Akamai EAA-Dokumentation |
    | | |

1. Ktpass /out EAADemo.keytab  /princ HTTP/corpapps.login.go.akamai-access.com@superdemo.live /mapuser eaadelegation@superdemo.live /pass RANDOMPASS /crypto All ptype KRB5_NT_PRINCIPAL

    ![Screenshot einer Administratoreingabeaufforderung mit den Ergebnissen des Befehls zum Erstellen einer Schlüsseltabellendatei für AKAMAI EAA.](./media/header-akamai-tutorial/administrator.png)

#### <a name="step-5-import-keytab-in-the-akamai-eaa-console"></a>Schritt 5: Importieren der Schlüsseltabelle in der Akamai EAA-Konsole

1. Klicken Sie auf **System** > **Keytabs** (System > Schlüsseltabellen).

    ![Screenshot der Akamai-EAA-Konsole, mit ausgewählten Schlüsseltabellen im Menü „System“.](./media/header-akamai-tutorial/keytabs.png)

1. Wählen Sie als Schlüsseltabellentyp die Option **Kerberos Delegation** (Kerberos-Delegierung) aus.

    ![Screenshot des Bildschirms „EAAKEYTAB“ der Akamai EAA-Konsole mit den Schlüsseltabelleneinstellungen. Der Schlüsseltabellentyp ist auf „Kerberos Delegation (Kerberos-Delegierung) festgelegt.](./media/header-akamai-tutorial/keytab-delegation.png)

1. Vergewissern Sie sich, dass die Schlüsseltabelle als bereitgestellt und überprüft angezeigt wird.

    ![Screenshot des KEYTABS-Bildschirms der Akamai EAA-Konsole, auf dem die EAA-Schlüsseltabelle als „Schlüsseltabelle bereitgestellt und verifiziert“ aufgeführt ist.](./media/header-akamai-tutorial/keytabs-2.png)

1. Benutzererfahrung

    ![Screenshot des Anmeldedialogfelds unter „myapps.microsoft.com“. ](./media/header-akamai-tutorial/end-user-3.png)

    ![Screenshot des Fensters „Apps“ für „myapps.microsoft.com“ mit App-Symbolen.](./media/header-akamai-tutorial/end-user-4.png)

1. Bedingter Zugriff

    ![Screenshot mit einer Meldung zum Genehmigen der Anmeldeanforderung. die Nachricht.](./media/header-akamai-tutorial/conditional-access-4.png)

    ![Screenshot des Bildschirms „Applications“ (Anwendungen) mit Symbolen für MyHeaderApp, SSH Secure, SecretRDPApp und myKerberosApp.](./media/header-akamai-tutorial/conditional-access-10.png)

    ![Screenshot des Begrüßungsbildschirms für „myKerberosApp“. Die Meldung „Welcome superdemo\user1“ wird über einem Hintergrundbild angezeigt.](./media/header-akamai-tutorial/conditional-access-11.png)

### <a name="create-akamai-test-user"></a>Erstellen eines Akamai-Testbenutzers

In diesem Abschnitt erstellen Sie in Akamai einen Benutzer namens B. Simon. Arbeiten Sie mit dem [Supportteam für den Akamai-Client](https://www.akamai.com/us/en/contact-us/) zusammen, um die Benutzer zur Akamai-Plattform hinzuzufügen. Benutzer müssen erstellt und aktiviert werden, damit Sie einmaliges Anmelden verwenden können. 

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen:

* Klicken Sie im Azure-Portal auf „Diese Anwendung testen“. Dadurch sollten Sie automatisch bei der Akamai-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie unter „Meine Apps“ auf die Kachel „Akamai“ klicken, sollten Sie automatisch bei der Akamai-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Akamai können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-any-app)
