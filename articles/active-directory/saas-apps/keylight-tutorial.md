---
title: 'Tutorial: Azure Active Directory-Integration in LockPath Keylight | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden für Azure Active Directory und LockPath Keylight konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/11/2021
ms.author: jeedes
ms.openlocfilehash: abaa441410f5cbccad3374fc75a074a7678e0d8f
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132294757"
---
# <a name="tutorial-azure-active-directory-integration-with-lockpath-keylight"></a>Tutorial: Azure Active Directory-Integration in LockPath Keylight

In diesem Tutorial erfahren Sie, wie Sie LockPath Keylight in Azure Active Directory (Azure AD) integrieren. Die Integration von LockPath Keylight in Azure AD ermöglicht Folgendes:

* Sie können in Azure AD steuern, wer Zugriff auf LockPath Keylight hat.
* Sie können es Ihren Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei LockPath Keylight anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit LockPath Keylight konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Sollten Sie nicht über eine Azure AD-Umgebung verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) verwenden.
* LockPath Keylight-Abonnement, für das einmaliges Anmelden aktiviert ist.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* LockPath Keylight unterstützt **SP-initiiertes** einmaliges Anmelden.
* LockPath Keylight unterstützt **Just-in-Time**-Benutzerbereitstellung.

## <a name="add-lockpath-keylight-from-the-gallery"></a>Hinzufügen von LockPath Keylight aus dem Katalog

Zum Konfigurieren der Integration von LockPath Keylight in Azure AD müssen Sie LockPath Keylight aus dem Katalog zur Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **LockPath Keylight** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **LockPath Keylight** aus, und fügen Sie die App dann hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-lockpath-keylight"></a>Konfigurieren und Testen von Azure AD-SSO für LockPath Keylight

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit LockPath Keylight mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in LockPath Keylight eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit LockPath Keylight die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren von LockPath Keylight-SSO](#configure-lockpath-keylight-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
    1. **[Erstellen eines LockPath Keylight-Testbenutzers](#create-lockpath-keylight-test-user)** , um ein Pendant zu B. Simon in LockPath Keylight zu erhalten, das mit der Darstellung des Benutzers in Azure AD verknüpft ist.
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **LockPath Keylight** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    a. Geben Sie im Textfeld **Bezeichner (Entitäts-ID)** eine URL im folgenden Format ein: `https://<COMPANY_NAME>.keylightgrc.com`.

    b. Geben Sie im Textfeld **Antwort-URL** eine URL nach folgendem Muster ein: `https://<COMPANY_NAME>.keylightgrc.com/Login.aspx`

    c. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<COMPANY_NAME>.keylightgrc.com/`.

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch die tatsächlichen Werte für Bezeichner, Antwort-URL und Anmelde-URL. Wenden Sie sich an das [Supportteam für den LockPath Keylight-Client](https://www.lockpath.com/contact/), um diese Werte zu erhalten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

5. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um das Ihrer Anforderung entsprechende Zertifikat (**Zertifikat (Rohdaten)** ) aus den angegebenen Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/certificateraw.png)

6. Kopieren Sie im Abschnitt **LockPath Keylight einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie Zugriff auf LockPath Keylight gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **LockPath Keylight** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-lockpath-keylight-sso"></a>Konfigurieren von LockPath Keylight-SSO

1. Führen Sie die folgenden Schritte aus, um das einmalige Anmelden in LockPath Keylight zu aktivieren:

    a. Melden Sie sich als Administrator bei Ihrem LockPath Keylight-Konto an.

    b. Klicken Sie im Menü oben auf **Person**, und wählen Sie **Keylight Setup** aus.

    ![Screenshot: Auswahl des Symbols „Person“ und der Option „Keylight Setup“ im Dropdownmenü](./media/keylight-tutorial/setup-icon.png)

    c. Klicken Sie in der Strukturansicht links auf **SAML**.

    ![Screenshot: Auswahl von „SAML“ in der Strukturansicht](./media/keylight-tutorial/treeview.png)

    d. Klicken Sie im Dialogfeld **SAML-Einstellungen** auf **Bearbeiten**.

    ![Screenshot: Fenster „SAML-Einstellungen“ mit Auswahl der Schaltfläche „Bearbeiten“](./media/keylight-tutorial/edit-icon.png)

1. Führen Sie auf der Dialogfeldseite **Edit SAML Settings** die folgenden Schritte aus:

    ![Einmaliges Anmelden konfigurieren](./media/keylight-tutorial/settings.png)

    a. Legen Sie für **SAML-Authentifizierung** die Einstellung **Aktiv** fest.

    b. Fügen Sie in das Textfeld **Identity Provider Login URL** (Anmelde-URL des Identitätsanbieters) den Wert der **Anmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    c. Fügen Sie in das Textfeld **Identity Provider Logout URL** (Abmelde-URL des Identitätsanbieters) den Wert der **Abmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    d. Klicken Sie auf **Datei auswählen**, um das heruntergeladene LockPath Keylight-Zertifikat auszuwählen, und klicken Sie dann auf **Öffnen**, um das Zertifikat hochzuladen.

    e. Legen Sie für **Speicherort der SAML-Benutzer-ID** die Einstellung **NameIdentifier-Element der subject-Anweisung** fest.

    f. Geben Sie den **Keylight-Dienstanbieter** in folgendem Format ein: `https://<CompanyName>.keylightgrc.com`.

    g. Legen Sie für **Benutzer automatisch bereitstellen** die Einstellung **Aktiv** fest.

    h. Legen Sie für **Kontotyp automatisch bereitstellen** die Einstellung **Vollständiger Benutzer** fest.

    i. Wählen Sie für **Sicherheitsrolle automatisch bereitstellen** die Option **Standardbenutzer mit SAML** aus.

    j. Legen Sie für **Sicherheitskonfiguration automatisch bereitstellen** die Einstellung **Standardbenutzerkonfiguration** fest.

    k. Geben Sie im Textfeld **E-Mail-Attribut** die Zeichenfolge `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` ein.

    l. Geben Sie **in das Textfeld** Vornamen-Attribut`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` ein.

    m. Geben Sie **in das Textfeld** Nachnamen-Attribut`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` ein.

    n. Klicken Sie auf **Speichern**.

### <a name="create-lockpath-keylight-test-user"></a>Erstellen eines LockPath Keylight-Testbenutzers

In diesem Abschnitt wird in LockPath Keylight ein Benutzer mit dem Namen Britta Simon erstellt. LockPath Keylight unterstützt die Just-in-Time-Bereitstellung, die standardmäßig aktiviert ist. Für Sie steht in diesem Abschnitt kein Aktionselement zur Verfügung. Ist ein Benutzer noch nicht in LockPath Keylight vorhanden, wird nach der Authentifizierung ein neuer Benutzer erstellt. Wenn Sie einen Benutzer manuell erstellen müssen, setzen Sie sich mit dem [Supportteam für den LockPath Keylight-Client](https://www.lockpath.com/contact/) in Verbindung.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für LockPath Keylight weitergeleitet. Dort können Sie den Anmeldeflow initiieren. 

* Rufen Sie die LockPath Keylight-Anmelde-URL direkt auf, und initiieren Sie dort den Anmeldeflow.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie in „Meine Apps“ auf die Kachel „LockPath Keylight“ klicken, werden Sie zur Anmelde-URL für LockPath Keylight weitergeleitet. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von LockPath Keylight können Sie Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-aad)
