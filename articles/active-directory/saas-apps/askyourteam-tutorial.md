---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit AskYourTeam | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und AskYourTeam konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/18/2020
ms.author: jeedes
ms.openlocfilehash: bff068248a0ac47954507966c9c0d69826ce6b7b
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132288429"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-askyourteam"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit AskYourTeam

In diesem Tutorial erfahren Sie, wie Sie AskYourTeam in Azure Active Directory (Azure AD) integrieren. Die Integration von AskYourTeam in Azure AD ermöglicht Folgendes:

* Sie können in Azure AD steuern, wer Zugriff auf AskYourTeam haben soll.
* Sie können es Ihren Benutzern ermöglichen, sich mit ihrem Azure AD-Konto automatisch bei AskYourTeam anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* SSO-fähiges AskYourTeam-Abonnement

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* AskYourTeam unterstützt **SP- und IDP-initiiertes** einmaliges Anmelden.

## <a name="adding-askyourteam-from-the-gallery"></a>Hinzufügen von AskYourTeam über den Katalog

Um die Integration von AskYourTeam in Azure AD zu konfigurieren, müssen Sie AskYourTeam über den Katalog Ihrer Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **AskYourTeam** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **AskYourTeam** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-askyourteam"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für AskYourTeam

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit AskYourTeam mithilfe eines Testbenutzers namens **B.Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in AskYourTeam eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD bei AskYourTeam die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für AskYourTeam](#configure-askyourteam-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
    1. **[Erstellen eines AskYourTeam-Testbenutzers](#create-askyourteam-test-user)** , um in AskYourTeam eine Entsprechung von B.Simon zu erhalten, die mit der Benutzerdarstellung in Azure AD verknüpft ist.
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **AskYourTeam** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Bearbeitungs- bzw. Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Geben Sie im Abschnitt **Grundlegende SAML-Konfiguration** die Werte in die folgenden Felder ein, wenn Sie die Anwendung im **IDP**-initiierten Modus konfigurieren möchten:

    Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://<COMPANY>.app.askyourteam.com/users/auth/saml/callback`.

1. Klicken Sie auf **Zusätzliche URLs festlegen**, und führen Sie den folgenden Schritt aus, wenn Sie die Anwendung im **SP-initiierten Modus** konfigurieren möchten:

    Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<COMPANY>.app.askyourteam.com/login`

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch die tatsächlichen Werte für die Antwort- und die Anmelde-URL. (Weitere Informationen zu diesen Werten finden Sie weiter unten in diesem Tutorial.)

1. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** zum Eintrag **Zertifikat (Base64)** . Wählen Sie **Herunterladen** aus, um das Zertifikat herunterzuladen, und speichern Sie es auf Ihrem Computer.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

1. Kopieren Sie im Abschnitt **AskYourTeam einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B.Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie dem Benutzer Zugriff auf AskYourTeam gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **AskYourTeam** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.

1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-askyourteam-sso"></a>Konfigurieren des einmaligen Anmeldens für AskYourTeam

1. Wenn Sie die Konfiguration in AskYourTeam automatisieren möchten, müssen Sie die **Browsererweiterung „Meine Apps“ für die sichere Anmeldung** installieren, indem Sie auf **Erweiterung installieren** klicken.

    ![Erweiterung „Meine Apps“](common/install-myappssecure-extension.png)

2. Klicken Sie nach dem Hinzufügen der Erweiterung zum Browser auf **AskYourTeam einrichten**, um zur Anwendung AskYourTeam weitergeleitet zu werden. Geben Sie dort die Administratoranmeldeinformationen ein, um sich bei AskYourTeam anzumelden. Die Browsererweiterung konfiguriert die Anwendung automatisch für Sie und automatisiert die Schritte 3 bis 7.

    ![Einrichtungskonfiguration](common/setup-sso.png)

3. Wenn Sie AskYourTeam manuell einrichten möchten, melden Sie sich in einem anderen Webbrowserfenster als Administrator bei der AskYourTeam-Unternehmenswebsite an.

1. Klicken Sie auf **My Organisation** (Meine Organisation).

    ![Screenshot: Link „Meine Organisation“](./media/askyourteam-tutorial/user1.png)

1. Klicken Sie auf **Integrationen**.

    ![Screenshot: Link „Integrationen“](./media/askyourteam-tutorial/configure1.png)

1. Klicken Sie auf **Edit Settings** (Einstellungen bearbeiten).

    ![Screenshot: Meldung für einmaliges Anmelden mit der Schaltfläche „Einstellungen bearbeiten“](./media/askyourteam-tutorial/configure2.png)

1. Führen Sie auf der Seite **Edit Single Sign-On Integration** (SSO-Integration bearbeiten) die folgenden Schritte aus: 

    ![Screenshot: Seite „Edit Single Sign-On Integration“ (SSO-Integration bearbeiten) zum Eingeben der Werte für diesen Schritt](./media/askyourteam-tutorial/configure3.png)

    a. Fügen Sie im Textfeld **SAML Single Sign-On Service URL** (SAML-Dienst-URL für einmaliges Anmelden) den Wert der **Anmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    b. Fügen Sie in das Textfeld **SAML Entity ID** (SAML-Entitäts-ID) den **Azure AD-Bezeichner** ein, den Sie aus dem Azure-Portal kopiert haben.

    c. Fügen Sie im Textfeld **Sign-Out URL** (Abmelde-URL) den Wert der **Abmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    d. Öffnen Sie das aus dem Azure-Portal heruntergeladene **Zertifikat (Base64)** im Editor, und fügen Sie den Inhalt in das Textfeld **SAML Signing Certificate - Base64** (SAML-Signaturzertifikat – Base64) ein.

    > [!NOTE]
    > Alternativ können Sie auch die **Verbundmetadaten-XML** hochladen, indem Sie auf die Option **Choose File** (Datei auswählen) klicken.

    e. Kopieren Sie den Wert für **Reply URL (Assertion Consumer Service URL)** (Antwort-URL (Assertionsverbraucherdienst-URL)), und fügen Sie ihn im Azure-Portal im Abschnitt **Grundlegende SAML-Konfiguration** in das Feld **Antwort-URL** ein.

    f. Kopieren Sie den Wert im Feld **Sign on URL** (Anmelde-URL), und fügen Sie ihn im Azure-Portal im Abschnitt **Grundlegende SAML-Konfiguration** in das Textfeld **Anmelde-URL** ein.

    g. Klicken Sie auf **Speichern**.

### <a name="create-askyourteam-test-user"></a>Erstellen eines AskYourTeam-Testbenutzers

1. Melden Sie sich in einem anderen Webbrowserfenster bei der AskYourTeam-Website als Administrator an.

1. Klicken Sie auf **My Organisation** (Meine Organisation).

    ![Screenshot: Link „Meine Organisation“, wo die Aufgabe beginnt](./media/askyourteam-tutorial/user1.png)

1. Klicken Sie auf **Users** (Benutzer), und wählen Sie **New User** (Neuer Benutzer) aus.

    ![Screenshot: Link „Benutzer“ mit der Option „Neuer Benutzer“](./media/askyourteam-tutorial/user2.png)

1. Führen Sie im Abschnitt **New user** (Neuer Benutzer) die folgenden Schritte aus:

    ![Screenshot: Abschnitt „Neuer Benutzer“ zum Eingeben von Benutzerinformationen](./media/askyourteam-tutorial/user3.png)

    1. Geben Sie im Textfeld **First name** (Vorname) den Vornamen des Benutzers ein.

    1. Geben Sie im Textfeld **Last name** (Nachname) den Nachnamen des Benutzers ein.

    1. Geben Sie im Textfeld **Email** (E-Mail-Adresse) die E-Mail-Adresse des Benutzers ein (beispielsweise B.Simon@contoso.com).

    1. Wählen Sie unter **Role** (Rolle) die erforderliche Rolle für den Benutzer aus.

    1. Klicken Sie auf **Speichern**.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen:

#### <a name="sp-initiated"></a>SP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Daraufhin werden Sie zur Anmelde-URL für AskYourTeam weitergeleitet, wo Sie den Anmeldeflow initiieren können.

* Navigieren Sie direkt zur Anmelde-URL für AskYourTeam, und initiieren Sie den Anmeldeflow.

#### <a name="idp-initiated"></a>IDP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch sollten Sie automatisch bei der AskYourTeam-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben.

Sie können auch den Microsoft-Bereich „Meine Apps“ verwenden, um die Anwendung in einem beliebigen Modus zu testen. Beim Klicken auf die Kachel „AskYourTeam“ in „Meine Apps“ geschieht Folgendes: Wenn Sie den SP-Modus konfiguriert haben, werden Sie zum Initiieren des Anmeldeflows zur Anmeldeseite der Anwendung weitergeleitet. Wenn Sie den IDP-Modus konfiguriert haben, sollten Sie automatisch bei der AskYourTeam-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von AskYourTeam können Sie eine Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-any-app)
