---
title: 'Tutorial: Azure AD-SSO-Integration in HubSpot'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und HubSpot konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/07/2021
ms.author: jeedes
ms.openlocfilehash: 54ca75be0467b2b08c3195dcba1849c42910dd2c
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132291291"
---
# <a name="tutorial-azure-ad-sso-integration-with-hubspot"></a>Tutorial: Azure AD-SSO-Integration in HubSpot

In diesem Tutorial erfahren Sie, wie Sie HubSpot in Azure Active Directory (Azure AD) integrieren. Die Integration von HubSpot in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf HubSpot hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei HubSpot anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit HubSpot konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Wenn Sie kein Azure AD-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.
* Ein HubSpot-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung und integrieren HubSpot in Azure AD.

HubSpot unterstützt die folgenden Features:

* **SP-initiiertes einmaliges Anmelden**
* **IDP-initiiertes einmaliges Anmelden**

## <a name="add-hubspot-from-the-gallery"></a>Hinzufügen von HubSpot aus dem Katalog

Zum Konfigurieren der Integration von HubSpot in Azure AD müssen Sie HubSpot über den Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **HubSpot** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **HubSpot** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-hubspot"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für HubSpot

Konfigurieren und testen Sie Azure AD-SSO mit HubSpot mithilfe einer Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in HubSpot eingerichtet werden.

Führen Sie zum Konfigurieren und Testen von Azure AD-SSO mit HubSpot die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren von SSO für HubSpot](#configure-hubspot-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
    1. **[Erstellen eines HubSpot-Testbenutzers](#create-hubspot-test-user)** , um eine Entsprechung von B. Simon in HubSpot zu erhalten, die mit der Darstellung in Azure AD verknüpft ist.
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **HubSpot** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Zum Konfigurieren des **IDP-initiierten Modus** führen Sie im Bereich **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    1. Geben Sie im Feld **Bezeichner** eine URL im folgenden Format ein: https:\//api.hubspot.com/login-api/v1/saml/login?portalId=\<CUSTOMER ID\>.

    1. Geben Sie im Feld **Antwort-URL** eine URL im folgenden Format ein: https:\//api.hubspot.com/login-api/v1/saml/acs?portalId=\<CUSTOMER ID\>.

    > [!NOTE]
    > Sie können sich zum Formatieren der URLs auch die Muster im Bereich **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

1. So konfigurieren Sie die Anwendung im *SP-initiierten Modus*:

    1. Wählen Sie **Zusätzliche URLs festlegen** aus.

    1. Geben Sie im Feld **Anmelde-URL** die URL **https:\//app.hubspot.com/login** ein.

1. Wählen Sie im Bereich **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** die Option **Herunterladen** neben **Zertifikat (Base64)** aus. Wählen Sie eine Option zum Herunterladen entsprechend Ihren Anforderungen aus. Speichern Sie das Zertifikat auf Ihrem Computer.

    ![Option zum Herunterladen des Zertifikats (Base64)](common/certificatebase64.png)

1. Kopieren Sie im Abschnitt **HubSpot einrichten** die folgenden URLs entsprechend Ihren Anforderungen:

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf HubSpot gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **HubSpot** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-hubspot-sso"></a>Konfigurieren von SSO für HubSpot

1. Öffnen Sie eine neue Registerkarte in Ihrem Browser, und melden Sie sich bei Ihrem HubSpot-Administratorkonto an.

1. Wählen Sie oben rechts auf der Seite das Symbol für die **Einstellungen** aus.

    ![Symbol „Settings“ (Einstellungen) in HubSpot](./media/hubspot-tutorial/icon.png)

1. Wählen Sie **Account Defaults** (Kontostandardwerte) aus.

    ![Option „Account Defaults“ (Kontostandardwerte) in HubSpot](./media/hubspot-tutorial/account.png)

1. Scrollen Sie nach unten zum Abschnitt **Security** (Sicherheit), und wählen Sie **Set up** (Einrichten) aus.

    ![Option „Set up“ (Einrichten) in HubSpot](./media/hubspot-tutorial/security.png)

1. Führen Sie im Abschnitt **Einmaliges Anmelden einrichten** die folgenden Schritte aus:

    1. Wählen Sie im Feld **Audience URL (Service Provider Entity ID)** (Zielgruppen-URL (ID der Dienstanbieterentität)) die Option **Copy** (Kopieren) aus, um den Wert zu kopieren. Fügen Sie im Azure-Portal im Bereich **Grundlegende SAML-Konfiguration** den Wert in das Feld **Bezeichner** ein.

    1. Wählen Sie im Feld **Sign on URL, ACS, Recipient, or Redirect** (Anmelde-URL, ACS, Empfänger oder Umleitung) die Option **Copy** (Kopieren) aus, um den Wert zu kopieren. Fügen Sie im Azure-Portal im Bereich **Grundlegende SAML-Konfiguration** den Wert in das Feld **Antwort-URL** ein.

    1. Fügen Sie in HubSpot im Feld **Identity Provider Identifier or Issuer URL** (Identitätsanbieterbezeichner oder Aussteller-URL) den Wert für **Azure AD-Bezeichner** ein, den Sie aus dem Azure-Portal kopiert haben.

    1. Fügen Sie in HubSpot im Feld **Identity Provider Single Sign-On URL** (URL für einmaliges Anmelden des Identitätsanbieters) den Wert für **Anmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    1. Öffnen Sie im Windows-Editor die heruntergeladene Datei **Certificate(Base64)** . Kopieren Sie den Inhalt der Datei. Fügen Sie ihn anschließend in HubSpot in das Feld **X.509 Certificate** (X.509-Zertifikat) ein.

    1. Wählen Sie **Überprüfen** aus.

        ![Abschnitt „Set up single sign-on“ (Einmaliges Anmelden einrichten) in HubSpot](./media/hubspot-tutorial/certificate.png)

### <a name="create-hubspot-test-user"></a>Erstellen eines HubSpot-Testbenutzers

Damit sich Azure AD-Benutzer bei HubSpot anmelden können, müssen sie in HubSpot bereitgestellt werden. Im Fall von HubSpot ist die Bereitstellung eine manuelle Aufgabe.

So stellen Sie in HubSpot ein Benutzerkonto bereit:

1. Melden Sie sich bei der HubSpot-Unternehmenswebsite als Administrator an.

1. Wählen Sie oben rechts auf der Seite das Symbol für die **Einstellungen** aus.

    ![Symbol „Settings“ (Einstellungen) in HubSpot](./media/hubspot-tutorial/icon.png)

1. Wählen Sie **Users & Teams** (Benutzer und Teams) aus.

    ![Option „Users & Teams“ (Benutzer und Teams) in HubSpot](./media/hubspot-tutorial/users.png)

1. Wählen Sie **Create User** (Benutzer erstellen) aus.

    ![Option „Create user“ (Benutzer erstellen) in HubSpot](./media/hubspot-tutorial/teams.png)

1. Geben Sie im Feld **Add email addess(es)** (E-Mail-Adresse(n) hinzufügen) die E-Mail-Adresse des Benutzers im Format „brittasimon\@contoso.com“ ein, und wählen Sie **Next** (Weiter) aus.

    ![Das Feld „Add email address(es)“ (E-Mail-Adresse(n) hinzufügen) im Abschnitt „Create users“ (Benutzer erstellen) in HubSpot](./media/hubspot-tutorial/add-user.png)

1. Wählen Sie im Abschnitt **Create users** (Benutzer erstellen) jede Registerkarte aus. Legen Sie auf jeder Registerkarte die relevanten Optionen und Berechtigungen für den Benutzer fest. Klicken Sie anschließend auf **Weiter**.

    ![Registerkarten im Abschnitt „Create users“ (Benutzer erstellen) in HubSpot](./media/hubspot-tutorial/create-user.png)

1. Wählen Sie **Send** (Senden) aus, um die Einladung an den Benutzer zu senden.

    ![Option „Send“ (Senden) in HubSpot](./media/hubspot-tutorial/invitation.png)

    > [!NOTE]
    > Der Benutzer wird aktiviert, nachdem der Benutzer die Einladung angenommen hat.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

#### <a name="sp-initiated"></a>SP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für HubSpot weitergeleitet, wo Sie den Anmeldeflow initiieren können.  

* Rufen Sie direkt die HubSpot-Anmelde-URL auf, und initiieren Sie den Anmeldeflow.

#### <a name="idp-initiated"></a>IDP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch sollten Sie automatisch bei der HubSpot-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. 

Sie können auch den Microsoft-Bereich „Meine Apps“ verwenden, um die Anwendung in einem beliebigen Modus zu testen. Beim Klicken auf die Kachel „HubSpot“ in „Meine Apps“ geschieht Folgendes: Wenn Sie den SP-Modus konfiguriert haben, werden Sie zum Initiieren des Anmeldeflows zur Anmeldeseite der Anwendung weitergeleitet. Wenn Sie den IDP-Modus konfiguriert haben, sollten Sie automatisch bei der HubSpot-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von HubSpot können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-aad)
