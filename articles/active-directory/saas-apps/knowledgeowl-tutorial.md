---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit KnowledgeOwl | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und KnowledgeOwl konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/11/2021
ms.author: jeedes
ms.openlocfilehash: 10e134d6c3fa7e3a6a68269a894694b6ab279c28
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132346209"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-knowledgeowl"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit KnowledgeOwl

In diesem Tutorial erfahren Sie, wie Sie KnowledgeOwl in Azure Active Directory (Azure AD) integrieren. Die Integration von KnowledgeOwl in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf KnowledgeOwl hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei KnowledgeOwl anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein KnowledgeOwl-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* KnowledgeOwl unterstützt **SP- und IDP-initiiertes** einmaliges Anmelden.
* KnowledgeOwl unterstützt die **Just-In-Time**-Benutzerbereitstellung.

## <a name="add-knowledgeowl-from-the-gallery"></a>Hinzufügen von KnowledgeOwl aus dem Katalog

Zum Konfigurieren der Integration von KnowledgeOwl in Azure AD müssen Sie KnowledgeOwl aus dem Katalog zur Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **KnowledgeOwl** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **KnowledgeOwl** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-knowledgeowl"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für KnowledgeOwl

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit KnowledgeOwl mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in KnowledgeOwl eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit KnowledgeOwl die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für KnowledgeOwl](#configure-knowledgeowl-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines KnowledgeOwl-Testbenutzers](#create-knowledgeowl-test-user)** , um eine Entsprechung von B. Simon in KnowledgeOwl zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **KnowledgeOwl** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Geben Sie im Abschnitt **Grundlegende SAML-Konfiguration** die Werte in die folgenden Felder ein, wenn Sie die Anwendung im **IDP**-initiierten Modus konfigurieren möchten:

    a. Geben Sie im Textfeld **Bezeichner** eine URL in einem der folgenden Formate ein:
    
    ```http
    https://app.knowledgeowl.com/sp
    https://app.knowledgeowl.com/sp/id/<unique ID>
    ```

    b. Geben Sie im Textfeld **Antwort-URL** eine URL in einem der folgenden Formate ein:
    
    ```http
    https://subdomain.knowledgeowl.com/help/saml-login
    https://subdomain.knowledgeowl.com/docs/saml-login
    https://subdomain.knowledgeowl.com/home/saml-login
    https://privatedomain.com/help/saml-login
    https://privatedomain.com/docs/saml-login
    https://privatedomain.com/home/saml-login
    ```

1. Klicken Sie auf **Zusätzliche URLs festlegen**, und führen Sie den folgenden Schritt aus, wenn Sie die Anwendung im **SP-initiierten Modus** konfigurieren möchten:

    Geben Sie im Textfeld **Anmelde-URL** eine URL in einem der folgenden Formate ein:
    
    ```http
    https://subdomain.knowledgeowl.com/help/saml-login
    https://subdomain.knowledgeowl.com/docs/saml-login
    https://subdomain.knowledgeowl.com/home/saml-login
    https://privatedomain.com/help/saml-login
    https://privatedomain.com/docs/saml-login
    https://privatedomain.com/home/saml-login
    ```

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Sie müssen diesen Wert durch die tatsächlichen Werte für Bezeichner, Antwort-URL und Anmelde-URL ersetzen. Dies wird an späterer Stelle im Tutorial erläutert.

1. Die KnowledgeOwl-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Daher müssen Sie Ihrer Konfiguration der SAML-Tokenattribute benutzerdefinierte Attributzuordnungen hinzufügen. Der folgende Screenshot zeigt die Liste der Standardattribute.

    ![image](common/default-attributes.png)

1. Darüber hinaus wird von der KnowledgeOwl-Anwendung erwartet, dass in der SAML-Antwort noch einige weitere Attribute zurückgegeben werden (siehe unten). Diese Attribute werden ebenfalls vorab aufgefüllt, Sie können sie jedoch nach Bedarf überprüfen.

    | Name | Quellattribut | Namespace |
    | ------------ | -------------------- | -----|
    | SSO-ID | user.mail | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|

1. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** zum Eintrag **Zertifikat (Rohdaten)** . Wählen Sie **Herunterladen** aus, um das Zertifikat herunterzuladen, und speichern Sie es auf Ihrem Computer.

    ![Downloadlink für das Zertifikat](common/certificateraw.png)

1. Kopieren Sie im Abschnitt **KnowledgeOwl einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf KnowledgeOwl gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **KnowledgeOwl** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-knowledgeowl-sso"></a>Konfigurieren des einmaligen Anmeldens für KnowledgeOwl

1. Melden Sie sich in einem anderen Webbrowserfenster bei der KnowledgeOwl-Unternehmenswebsite als Administrator an.

1. Klicken Sie auf **Settings** (Einstellungen), und wählen Sie dann **SSO** aus.

    ![Screenshot: Auswahl von SSO im Menü „Settings“ (Einstellungen).](./media/knowledgeowl-tutorial/settings-sso-dropdown.png)

1. Führen Sie auf der Registerkarte **SAML-Einstellungen** die folgenden Schritte aus:

    ![Screenshot: Abschnitt „SAML SSO Integration“ (SAML-SSO-Integration), in dem Sie die hier beschriebenen Änderungen vornehmen können](./media/knowledgeowl-tutorial/sso-settings-required-fields.png)

    a. Wählen Sie **Enable SAML SSO** (SAML-SSO aktivieren) aus.

    b. Kopieren Sie den Wert im Feld **SP Entity ID** (SP-Entitäts-ID), und fügen Sie ihn im Azure-Portal im Abschnitt **Grundlegende SAML-Konfiguration** ins Feld **Bezeichner (Entitäts-ID)** ein.

    c. Kopieren Sie den Wert im Feld **SP Login URL** (SP-Anmelde-URL), und fügen Sie ihn im Azure-Portal im Abschnitt **Grundlegende SAML-Konfiguration** in die Textfelder **Anmelde-URL und Antwort-URL** ein.

    d. Fügen Sie in das Textfeld **IdP entityID** (IdP-Entitäts-ID) den Wert für den **Azure AD-Bezeichner** ein, den Sie aus dem Azure-Portal kopiert haben.

    e. Fügen Sie im Textfeld **IdP Login URL** (IdP-Anmelde-URL) den Wert der **Anmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    f. Fügen Sie im Textfeld **IdP Logout URL** (IdP-Abmelde-URL) den Wert der **Abmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    g. Laden Sie das heruntergeladene Zertifikat aus dem Azure-Portal hoch, indem Sie auf den Link **Upload** unter **IdP Certificate** (IdP-Zertifikat) klicken.

    h. Klicken Sie unten auf der Seite auf **Speichern** .

    ![Screenshot der Schaltfläche „Save“](./media/knowledgeowl-tutorial/sso-settings-saml-save.png)

    i. Öffnen Sie die Registerkarte **Map SAML Attributes** (SAML-Attribute zuordnen), um Attribute zuzuordnen, und führen Sie die folgenden Schritte aus:

    ![Screenshot: Abschnitt „Map SAML Attributes“ (SAML-Attribute zuordnen), in dem Sie die hier beschriebenen Änderungen vornehmen können](./media/knowledgeowl-tutorial/sso-settings-direct-attribute-fields.png)

    * Geben Sie `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/ssoid` im Textfeld **SSO ID** ein.
    * Geben Sie `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` im Textfeld **Username/Email** (Benutzername/E-Mail) ein.
    * Geben Sie `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` im Textfeld **First Name** (Vorname) ein.
    * Geben Sie `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` im Textfeld **Last Name** (Nachname) ein.

    j. Klicken Sie unten auf der Seite auf **Speichern** .

    ![Screenshot: Schaltfläche „Speichern“.](./media/knowledgeowl-tutorial/sso-settings-direct-attribute-save.png)

### <a name="create-knowledgeowl-test-user"></a>Erstellen eines KnowledgeOwl-Testbenutzers

In diesem Abschnitt wird in KnowledgeOwl ein Benutzer mit dem Namen B. Simon erstellt. KnowledgeOwl unterstützt die Just-in-Time-Benutzerbereitstellung, die standardmäßig aktiviert ist. Für Sie steht in diesem Abschnitt kein Aktionselement zur Verfügung. Ist ein Benutzer noch nicht in KnowledgeOwl vorhanden, wird nach der Authentifizierung ein neuer Benutzer erstellt.

> [!Note]
> Setzen Sie sich mit dem [KnowledgeOwl-Supportteam](mailto:support@knowledgeowl.com) in Verbindung, wenn Sie einen Benutzer manuell erstellen müssen.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

#### <a name="sp-initiated"></a>SP-initiiert

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für KnowledgeOwl weitergeleitet, wo Sie den Anmeldeflow initiieren können.  

* Rufen Sie direkt die Anmelde-URL für KnowledgeOwl auf, und initiieren Sie den Anmeldeflow.

#### <a name="idp-initiated"></a>IDP-initiiert

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch sollten Sie automatisch bei der KnowledgeOwl-Anwendung angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. 

Sie können auch das Microsoft-Portal „Meine Apps“ verwenden, um die Anwendung in einem beliebigen Modus zu testen. Beim Klicken auf die Kachel „KnowledgeOwl“ im Portal „Meine Apps“ geschieht Folgendes: Wenn Sie die Anwendung im SP-Modus konfiguriert haben, werden Sie zum Initiieren des Anmeldeflows zur Anmeldeseite der Anwendung weitergeleitet. Wenn Sie die Anwendung im IDP-Modus konfiguriert haben, sollten Sie automatisch bei der KnowledgeOwl-Anwendung angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Portal „Meine Apps“ finden Sie in dieser [Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von KnowledgeOwl können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-any-app)
