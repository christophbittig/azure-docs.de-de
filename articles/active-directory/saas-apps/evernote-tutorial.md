---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Evernote | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Evernote konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/18/2021
ms.author: jeedes
ms.openlocfilehash: cf47fe3054b3ebbfc8b85de45894c083fcae2106
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132285878"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-evernote"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Evernote

In diesem Tutorial erfahren Sie, wie Sie Evernote in Azure Active Directory (Azure AD) integrieren. Die Integration von Evernote in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf Evernote hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Evernote anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Evernote-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Evernote unterstützt **SP- und IDP-initiiertes** einmaliges Anmelden.

> [!NOTE]
> Der Bezeichner dieser Anwendung ist ein fester Zeichenfolgenwert, daher kann in einem Mandanten nur eine Instanz konfiguriert werden.

## <a name="add-evernote-from-the-gallery"></a>Hinzufügen von Evernote aus dem Katalog

Zum Konfigurieren der Integration von Evernote in Azure AD müssen Sie Evernote über den Katalog zur Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Evernote** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Evernote** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-evernote"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Evernote

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Evernote mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Evernote eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD bei Evernote die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Evernote](#configure-evernote-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines Evernote-Testbenutzers](#create-evernote-test-user)** , um eine Entsprechung von B. Simon in Evernote zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Evernote** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus, wenn Sie die Anwendung im **IDP-initiierten** Modus konfigurieren möchten: 

    Geben Sie im Textfeld **Bezeichner** die URL `https://www.evernote.com/saml2` ein.

1. Klicken Sie auf **Zusätzliche URLs festlegen**, und führen Sie den folgenden Schritt aus, wenn Sie die Anwendung im **SP-initiierten Modus** konfigurieren möchten:

    Geben Sie im Textfeld **Anmelde-URL** die URL ein: `https://www.evernote.com/Login.action`.

1. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** zum Eintrag **Zertifikat (Base64)** . Wählen Sie **Herunterladen** aus, um das Zertifikat herunterzuladen, und speichern Sie es auf Ihrem Computer.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

7. Klicken Sie zum Ändern der Optionen unter **Signieren** auf die Schaltfläche **Bearbeiten**, um das Dialogfeld **SAML-Signaturzertifikat** zu öffnen.

    ![Screenshot: Dialogfeld „SAML-Signaturzertifikat“ mit ausgewählter Schaltfläche „Bearbeiten“](common/edit-certificate.png) 

    ![image](./media/evernote-tutorial/assertion.png)

    a. Wählen Sie unter **Signaturoption** die Option **SAML-Antwort und-Assertion signieren**.

    b. Klicken Sie unten auf der Seite auf **Speichern**.

1. Kopieren Sie im Abschnitt **Evernote einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Evernote gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste den Eintrag **Evernote** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-evernote-sso"></a>Konfigurieren des einmaligen Anmeldens für Evernote

1. Wenn Sie die Konfiguration in Evernote automatisieren möchten, müssen Sie die **Browsererweiterung „Meine Apps“ für die sichere Anmeldung** installieren, indem Sie auf **Erweiterung installieren** klicken.

    ![Erweiterung „Meine Apps“](common/install-myappssecure-extension.png)

2. Klicken Sie nach dem Hinzufügen der Erweiterung zum Browser auf **Evernote einrichten**, um zur Anwendung Evernote weitergeleitet zu werden. Geben Sie dort die Administratoranmeldeinformationen ein, um sich bei Evernote anzumelden. Die Browsererweiterung konfiguriert die Anwendung automatisch für Sie und automatisiert die Schritte 3 bis 6.

    ![Einrichtungskonfiguration](common/setup-sso.png)

3. Wenn Sie Evernote manuell einrichten möchten, melden Sie sich in einem neuen Webbrowserfenster bei der Evernote-Unternehmenswebsite als Administrator an, und führen Sie die folgenden Schritte aus:

4. Wechseln Sie zur **Verwaltungskonsole**.

    ![Admin-Console](./media/evernote-tutorial/admin.png)

5. Wechseln Sie von der **Verwaltungskonsole** zu **Sicherheit**, und wählen Sie **Einmaliges Anmelden**.

    ![SSO-Setting](./media/evernote-tutorial/security.png)

6. Konfigurieren Sie die folgenden Werte:

    ![Certificate-Setting](./media/evernote-tutorial/certificate.png)
    
    a.  **SSO aktivieren**: SSO ist standardmäßig aktiviert (Klicken Sie auf **Einmaliges Anmelden deaktivieren**, um die SSO-Aktivierung aufzuheben).

    b. Fügen Sie den Wert der **Anmelde-URL**, den Sie aus dem Azure-Portal kopiert haben, in das Textfeld **SAML HTTP Request URL** (SAML-HTTP-Anforderungs-URL) ein.

    c. Öffnen Sie das heruntergeladene Zertifikat von Azure AD in einem Editor, kopieren Sie den Inhalt, einschließlich „BEGIN CERTIFICATE“ und „END CERTIFICATE“, und fügen Sie ihn in das Textfeld **X.509-Zertifikat** ein. 

    d. Klicken Sie auf **Änderungen speichern**.

### <a name="create-evernote-test-user"></a>Erstellen eines Evernote-Testbenutzers

Damit sich Azure AD-Benutzer bei Evernote anmelden können, müssen sie in Evernote bereitgestellt werden.  
Im Fall von Evernote ist die Bereitstellung eine manuelle Aufgabe.

**Führen Sie zum Bereitstellen von Benutzerkonten die folgenden Schritte aus:**

1. Melden Sie sich bei der Evernote-Unternehmenswebsite als Administrator an.

2. Klicken Sie auf die **Verwaltungskonsole**.

    ![Admin-Console](./media/evernote-tutorial/admin.png)

3. Wechseln Sie in der **Verwaltungskonsole** zu **Benutzer hinzufügen**.

    ![Screenshot: Menü „Benutzer“ mit ausgewählter Option „Benutzer hinzufügen“](./media/evernote-tutorial/create-user.png)

4. Geben Sie zum **Hinzufügen von Teammitgliedern** im Feld **E-Mail** die E-Mail-Adresse des Benutzerkontos ein, und klicken Sie auf **Einladen**.

    ![Add-testUser](./media/evernote-tutorial/add-user.png)
    
5. Nachdem die Einladung gesendet wurde, erhalten die Inhaber des Azure Active Directory-Kontos eine E-Mail, in der sie die Einladung annehmen können.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

#### <a name="sp-initiated"></a>SP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für Evernote weitergeleitet, wo Sie den Anmeldeflow initiieren können.  

* Rufen Sie direkt die Evernote-Anmelde-URL auf, und initiieren Sie den Anmeldeflow.

#### <a name="idp-initiated"></a>IDP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch sollten Sie automatisch bei der Evernote-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. 

Sie können auch den Microsoft-Bereich „Meine Apps“ verwenden, um die Anwendung in einem beliebigen Modus zu testen. Beim Klicken auf die Kachel „Evernote“ in „Meine Apps“ geschieht Folgendes: Wenn Sie die Anwendung im SP-Modus konfiguriert haben, werden Sie zum Initiieren des Anmeldeflows zur Anmeldeseite der Anwendung weitergeleitet. Wenn Sie die Anwendung im IDP-Modus konfiguriert haben, sollten Sie automatisch bei der Evernote-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Evernote können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-aad)
