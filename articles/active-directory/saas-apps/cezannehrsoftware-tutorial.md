---
title: 'Tutorial: Azure Active Directory-Integration mit Cezanne HR Software | Microsoft Docs'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Cezanne HR Software konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/11/2021
ms.author: jeedes
ms.openlocfilehash: 437427e80870fc4f86a6f5953a2ac4a29affed0b
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132349127"
---
# <a name="tutorial-azure-active-directory-integration-with-cezanne-hr-software"></a>Tutorial: Azure Active Directory-Integration mit Cezanne HR Software

In diesem Tutorial erfahren Sie, wie Sie Cezanne HR Software in Azure Active Directory (Azure AD) integrieren. Die Integration von Cezanne HR Software in Azure AD ermöglicht Folgendes:

* Sie können in Azure AD steuern, wer Zugriff auf Cezanne HR Software hat.
* Sie können Ihren Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei Cezanne HR Software anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Cezanne HR Software-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Cezanne HR Software unterstützt **SP-initiiertes** einmaliges Anmelden.

> [!NOTE]
> Der Bezeichner dieser Anwendung ist ein fester Zeichenfolgenwert, daher kann in einem Mandanten nur eine Instanz konfiguriert werden.

## <a name="add-cezanne-hr-software-from-the-gallery"></a>Hinzufügen von Cezanne HR Software aus dem Katalog

Zum Konfigurieren der Integration von Cezanne HR Software in Azure AD müssen Sie Cezanne HR Software aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** im Suchfeld den Suchbegriff **Cezanne HR Software** ein.
1. Wählen Sie im Ergebnisbereich den Eintrag **Cezanne HR Software** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-cezanne-hr-software"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Cezanne HR Software

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD bei Cezanne HR Software mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Cezanne HR Software eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD bei Cezanne HR Software die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Cezanne HR Software](#configure-cezanne-hr-software-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
    1. **[Erstellen eines Cezanne HR Software-Testbenutzers](#create-cezanne-hr-software-test-user)** , um eine Entsprechung von B. Simon in Cezanne HR Software zu erhalten, die mit der Benutzerdarstellung in Azure AD verknüpft ist.
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Cezanne HR Software** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    a. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://w3.cezanneondemand.com/CezanneOnDemand/-/<tenantidentifier>`.

    b. Geben Sie im Textfeld **Bezeichner (Entitäts-ID)** die folgende URL ein: `https://w3.cezanneondemand.com/CezanneOnDemand/`.

    c. Geben Sie im Textfeld **Antwort-URL** eine URL nach folgendem Muster ein: `https://w3.cezanneondemand.com:443/cezanneondemand/-/<tenantidentifier>/Saml/samlp`
    
    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch die tatsächliche Anmelde-URL und Antwort-URL. Wenden Sie sich an das [Kundensupportteam von Cezanne HR Software](https://cezannehr.com/services/support/), um diese Werte zu erhalten.

5. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um das Ihrer Anforderung entsprechende **Zertifikat (Base64)** aus den angegebenen Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

6. Kopieren Sie im Abschnitt **Cezanne HR Software einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Cezanne HR Software gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Cezanne HR Software** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-cezanne-hr-software-sso"></a>Konfigurieren des einmaligen Anmeldens für Cezanne HR Software

1. Melden Sie sich in einem anderen Webbrowserfenster an Ihrem Cezanne HR Software-Mandanten als Administrator an.

2. Klicken Sie im seitlichen Menü auf **Administration** (Verwaltung). Navigieren Sie dann zu **Security Settings** (Sicherheitseinstellungen), und klicken Sie auf **Single Sign-On** (Einmaliges Anmelden).

    ![Screenshot des Cezanne H R Software-Mandanten, bei dem „Sicherheitseinstellungen“ und „S S O-Konfiguration“ ausgewählt sind](./media/cezannehrsoftware-tutorial/settings.png)

3. Aktivieren Sie im Bereich **Allow users to log in using the following Single Sign-On (SSO) Service** (Benutzeranmeldung per SSO-Dienst zulassen) das Kontrollkästchen **SAML 2.0**, und wählen Sie die Option für die **Erweiterte Konfiguration** aus.

    ![Screenshot des Bereichs „Benutzer zulassen“, in dem „SAML 2.0“ und „Erweiterte Konfiguration“ ausgewählt sind](./media/cezannehrsoftware-tutorial/configuration.png)

4. Klicken Sie auf die Schaltfläche **Neu hinzufügen** .

    ![Screenshot der Schaltfläche „Neu hinzufügen“](./media/cezannehrsoftware-tutorial/new-button.png)

5. Füllen Sie im Abschnitt **SAML 2.0 IDENTITY PROVIDERS** (SAML 2.0-IDENTITÄTSANBIETER) die folgenden Felder aus, und klicken Sie auf **OK**.

    ![Screenshot eines Abschnitts, in dem Sie die in diesem Schritt beschriebenen Werte eingeben können](./media/cezannehrsoftware-tutorial/identity-provider.png)

    a. **Display Name** (Anzeigename): Geben Sie den Namen Ihres Identitätsanbieters als Anzeigenamen ein.

    b. **Entity Identifier** (Entitäts-ID): Fügen Sie in das Textfeld „Entity Identifier“ (Entitäts-ID) den Wert für den Azure AD-Bezeichner ein, den Sie aus dem Azure-Portal kopiert haben.

    c. **SAML Binding** (SAML-Bindung): Ändern Sie die SAML-Bindung in „POST“.

    d. **Security Token Service Endpoint** (Sicherheitstoken-Dienstendpunkt): Fügen Sie in das Textfeld „Security Token Service Endpoint“ (Sicherheitstoken-Dienstendpunkt) den Wert der Anmelde-URL ein, den Sie aus dem Azure-Portal kopiert haben.

    e. **User ID Attribute Name** (Benutzer-ID-Attributname): Geben Sie im Textfeld „User ID Attribute Name“ (Benutzer-ID-Attributname) die Zeichenfolge „http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress“ ein.

    f. **Public Key Certificate** (Öffentliches Schlüsselzertifikat): Klicken Sie auf das Symbol „Hochladen“, um das heruntergeladene Zertifikat aus dem Azure-Portal hochzuladen.

6. Klicken Sie auf OK.

7. Klicken Sie auf die Schaltfläche Save . 

    ![Screenshot der Schaltfläche „Speichern“ für die SSO-Konfiguration](./media/cezannehrsoftware-tutorial/save-button.png)

### <a name="create-cezanne-hr-software-test-user"></a>Erstellen eines Cezanne HR Software-Testbenutzers

Damit sich Azure AD-Benutzer bei Cezanne HR Software anmelden können, müssen sie in Cezanne HR Software bereitgestellt werden. Bei Cezanne HR Software ist die Bereitstellung ein manueller Vorgang.

**Führen Sie zum Bereitstellen eines Benutzerkontos die folgenden Schritte aus:**

1. Melden Sie sich als Administrator auf Ihrer Cezanne HR Software-Unternehmenswebsite an.

2. Klicken Sie im seitlichen Menü auf **Administration** (Verwaltung). Navigieren Sie dann zu **Users** (Benutzer), und klicken Sie auf **Add New User** (Neuen Benutzer hinzufügen).

    ![Screenshot des Cezanne H R Software-Mandanten, bei dem „Benutzer verwalten“ und „Neuen Benutzer hinzufügen“ ausgewählt sind](./media/cezannehrsoftware-tutorial/manage-users.png "Neuer Benutzer")

3. Führen Sie im Abschnitt **Person Details** (Angaben zur Person) die folgenden Schritte aus:

    ![Screenshot des Abschnitts „Person Details“, in dem Sie die in diesem Schritt beschriebenen Werte eingeben können](./media/cezannehrsoftware-tutorial/details.png "Neuer Benutzer")

    a. Legen Sie **Interner Benutzer** auf „AUS“ fest.

    b. Geben Sie den Vornamen ein.   

    c. Geben Sie den Nachnamen ein.

    d. Geben Sie die E-Mail-Adresse ein.

4. Führen Sie unter **Kontoinformationen** die folgenden Schritte aus:

    ![Screenshot der Kontoinformationen, in denen Sie die in diesem Schritt beschriebenen Werte eingeben können](./media/cezannehrsoftware-tutorial/account.png "Neuer Benutzer")

    a. Geben Sie im Textfeld **Benutzername** die E-Mail-Adresse des Benutzers, z.B. Brittasimon@contoso.com, ein.

    b. Geben Sie im Textfeld **Kennwort** das Kennwort des Benutzers ein.

    c. Wählen Sie unter **Sicherheitsrolle** die Option **HR Professional** (HR-Mitarbeiter).

    d. Klicken Sie auf **OK**.
    ![Screenshot: Schaltfläche „OK“](https://user-images.githubusercontent.com/80324891/115694644-f6eb5700-a358-11eb-9b23-a87a24921052.png)

5. Navigieren Sie zur Registerkarte **Einmalige Anmeldung**, und wählen Sie im Bereich **SAML 2.0 Identifiers** (SAML 2.0-Bezeichner) die Option **Neu hinzufügen**.

    ![Screenshot der Registerkarte „Einmaliges Anmelden“, auf der Sie „Neu hinzufügen“ auswählen können](./media/cezannehrsoftware-tutorial/single-sign-on.png "Benutzer")

6. Wählen Sie unter **Identity Provider** (Identitätsanbieter) Ihren Identitätsanbieter aus, und geben Sie in das Textfeld **User Identifier** (Benutzer-ID) die E-Mail-Adresse des Benutzers ein.

    ![Screenshot der SAML 2.0-Bezeichner, bei denen Sie Ihren Identitätsanbieter und die Benutzer-ID auswählen können](./media/cezannehrsoftware-tutorial/user-identifier.png "Benutzer")

7. Klicken Sie auf die Schaltfläche **Save** .

    ![Screenshot der Schaltfläche „Speichern“ für die Benutzereinstellungen](./media/cezannehrsoftware-tutorial/save.png "Benutzer")

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für Cezanne HR Software weitergeleitet, wo Sie den Anmeldeflow initiieren können. 

* Rufen Sie direkt die Cezanne HR Software-Anmelde-URL auf, und initiieren Sie den Anmeldeflow.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie in „Meine Apps“ auf die Kachel „Cezanne HR Software“ klicken, werden Sie zur Anmelde-URL für Cezanne HR Software umgeleitet. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Cezanne HR Software können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-aad)
