---
title: 'Tutorial: Azure Active Directory-Integration mit Workspot Control | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie das einmalige Anmelden für Azure Active Directory und Workspot Control konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/25/2021
ms.author: jeedes
ms.openlocfilehash: b8087fb44d2e40b01bac37da7193af7f3a45f13f
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132298701"
---
# <a name="tutorial-azure-active-directory-integration-with-workspot-control"></a>Tutorial: Azure Active Directory-Integration mit Workspot Control

In diesem Tutorial erfahren Sie, wie Sie Workspot Control in Azure Active Directory (Azure AD) integrieren. Bei der Integration von Workspot Control in Azure AD haben Sie folgende Möglichkeiten:

* Sie können in Azure AD steuern, wer auf Workspot Control Zugriff hat.
* Sie können es Ihren Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei Workspot Control anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit Workspot Control konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Sollten Sie nicht über eine Azure AD-Umgebung verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) verwenden.

* Workspot Control-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Workspot Control unterstützt SP- und IDP-initiiertes einmaliges Anmelden.

## <a name="add-workspot-control-from-the-gallery"></a>Hinzufügen von Workspot Control aus dem Katalog

Zum Konfigurieren der Integration von Workspot Control in Azure AD müssen Sie Workspot Control aus dem Katalog zu Ihrer Liste der verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Workspot Control** in das Suchfeld ein.
1. Wählen Sie **Workspot Control** im Ergebnisbereich aus, und fügen Sie die App dann hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-workspot-control"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Workspot Control

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Workspot Control mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Workspot Control eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Workspot Control die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Workspot Control](#configure-workspot-control-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
    1. **[Erstellen eines Workspot Control-Testbenutzers](#create-workspot-control-test-user)** , um eine Entsprechung von B. Simon in Workspot Control zu erhalten, die mit der Darstellung des Benutzers in Azure AD verknüpft ist.
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Workspot Control** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus, wenn Sie die Anwendung im IDP-initiierten Modus konfigurieren möchten:

    1. Geben Sie im Textfeld **Bezeichner** eine URL im folgenden Format ein:<br/>
    `https://<<i></i>INSTANCENAME>-saml.workspot.com/saml/metadata`

    1. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein:<br/>
    `https://<<i></i>INSTANCENAME>-saml.workspot.com/saml/assertion`

5. Wenn Sie die Anwendung im SP-initiierten Modus konfigurieren möchten, wählen Sie **Zusätzliche URLs festlegen** aus.

    Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein:<br/>
    `https://<<i></i>INSTANCENAME>-saml.workspot.com/`

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch den tatsächlichen Bezeichner, die Antwort-URL und die Anmelde-URL. Diese Werte erhalten Sie vom [Supportteam für den Workspot Control-Client](mailto:support@workspot.com). Sie können sich die Muster auch im Azure-Portal im Abschnitt **Grundlegende SAML-Konfiguration** ansehen.

6. Wählen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** die Option **Herunterladen** aus, um das Ihrer Anforderung entsprechende **Zertifikat (Base64)** aus den verfügbaren Optionen herunterzuladen. Speichern Sie das Zertifikat auf Ihrem Computer.

    ![Link zum Herunterladen des Zertifikats (Base64)](common/certificatebase64.png)

7. Kopieren Sie im Abschnitt **Workspot Control einrichten** die entsprechenden URLs gemäß Ihren Anforderungen:

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Workspot Control gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Workspot Control** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-workspot-control-sso"></a>Konfigurieren von Workspot Control-SSO

1. Melden Sie sich in einem anderen Webbrowserfenster als Sicherheitsadministrator bei Workspot Control an.

2. Wählen Sie auf der Symbolleiste am oberen Rand der Seite **Setup** und dann **SAML** aus.

    ![Setupoptionen](./media/workspotcontrol-tutorial/setup.png)

3. Führen Sie auf der Seite **Security Assertion Markup Language Configuration** (Security Assertion Markup Language-Konfiguration) die folgenden Schritte aus:
 
    ![Fenster „Security Assertion Markup Language Configuration“ (Security Assertion Markup Language-Konfiguration)](./media/workspotcontrol-tutorial/security.png)

    1. Fügen Sie im Feld **Entity ID** (Entitäts-ID) den Wert für **Azure AD-Bezeichner** ein, den Sie aus dem Azure-Portal kopiert haben.

    1. Fügen Sie im Feld **Signon Service URL** (URL des Anmeldediensts) die **Anmelde-URL** ein, die Sie aus dem Azure-Portal kopiert haben.

    1. Fügen Sie im Feld **Logout Service URL** (URL des Abmeldediensts) die **Abmelde-URL** ein, die Sie aus dem Azure-Portal kopiert haben.

    1. Wählen Sie **Update File** (Datei aktualisieren) aus, um das Base64-codierte Zertifikat, das Sie aus dem Azure-Portal heruntergeladen haben, in das X.509-Zertifikat hochzuladen.

    1. Wählen Sie **Speichern** aus.

### <a name="create-workspot-control-test-user"></a>Erstellen eines Workspot Control-Testbenutzers

Damit sich Azure AD-Benutzer bei Workspot Control anmelden können, müssen sie in Workspot Control bereitgestellt werden. Die Bereitstellung ist eine manuelle Aufgabe.

**Führen Sie zum Bereitstellen eines Benutzerkontos die folgenden Schritte aus:**

1. Melden Sie sich bei Workspot Control als Sicherheitsadministrator an.

2. Wählen Sie auf der Symbolleiste am oberen Rand der Seite **Users** (Benutzer) und dann **Add User** (Benutzer hinzufügen) aus.

    ![Optionen „Users“ (Benutzer)](./media/workspotcontrol-tutorial/user.png)

3. Führen Sie im Fenster **Add a New User** (Neuen Benutzer hinzufügen) die folgenden Schritte aus:

    ![Fenster „Add a New User“ (Neuen Benutzer hinzufügen)](./media/workspotcontrol-tutorial/new-user.png)

    1. Geben Sie im Textfeld **First Name** (Vorname) den Vornamen eines Benutzers ein, z. B. **Britta**.

    1. Geben Sie im Textfeld **Last Name** (Nachname) den Nachnamen eines Benutzers ein, z. B. **Simon**.

    1. Geben Sie im Feld **Email** (E-Mail) die E-Mail-Adresse des Benutzers ein, z. B. **Brittasimon@contoso.<i></i>com**.

    1. Wählen Sie die geeignete Benutzerrolle aus der Dropdownliste **Role** (Rolle) aus.

    1. Wählen Sie die geeignete Benutzergruppe aus der Dropdownliste **Group** (Gruppe) aus.

    1. Wählen Sie **Add User** (Benutzer hinzufügen) aus.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

#### <a name="sp-initiated"></a>SP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Sie werden zur Anmelde-URL für Workspot Control weitergeleitet, damit Sie den Anmeldeflow initiieren können.  

* Rufen Sie die Workspot Control-Anmelde-URL direkt auf, und initiieren Sie dort den Anmeldeflow.

#### <a name="idp-initiated"></a>IDP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Sie sollten automatisch bei der Workspot Control-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. 

Sie können auch den Microsoft-Bereich „Meine Apps“ verwenden, um die Anwendung in einem beliebigen Modus zu testen. Beim Klicken auf die Kachel „Workspot Control“ in „Meine Apps“ geschieht Folgendes: Wenn Sie den SP-Modus konfiguriert haben, werden Sie zum Initiieren des Anmeldeflows zur Anmeldeseite der Anwendung weitergeleitet. Wenn Sie den IDP-Modus konfiguriert haben, sollten Sie automatisch bei der Workspot Control-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Workspot Control können Sie Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-aad)
