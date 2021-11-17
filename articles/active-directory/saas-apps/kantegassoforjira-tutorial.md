---
title: 'Tutorial: Azure Active Directory-Integration mit Kantega SSO for JIRA | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Kantega SSO for JIRA konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/27/2021
ms.author: jeedes
ms.openlocfilehash: cb972e9ac5b04f4f27c3717fc7b99a4c2d3eff8e
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132313752"
---
# <a name="tutorial-azure-active-directory-integration-with-kantega-sso-for-jira"></a>Tutorial: Azure Active Directory-Integration mit Kantega SSO for JIRA

In diesem Tutorial erfahren Sie, wie Sie Kantega SSO for JIRA in Azure Active Directory (Azure AD) integrieren. Die Integration von Kantega SSO for JIRA in Azure AD ermöglicht Ihnen Folgendes:

* In Azure AD kontrollieren, wer Zugriff auf Kantega SSO for JIRA hat.
* Ihren Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei Kantega SSO for JIRA anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit Kantega SSO for JIRA konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Sollten Sie nicht über eine Azure AD-Umgebung verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) verwenden.

* Ein Kantega SSO for JIRA-Abonnement, das für das einmaliges Anmelden aktiviert ist.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Das Kantega SSO for JIRA unterstützt **SP- und IDP**-initiiertes einmaliges Anmelden.

## <a name="add-kantega-sso-for-jira-from-the-gallery"></a>Eine Anleitung zum Hinzufügen von Kantega SSO for JIRA aus dem Katalog

Zum Konfigurieren der Integration von Kantega SSO for JIRA in Azure AD müssen Sie Kantega SSO for JIRA aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus dem Katalog hinzufügen** den Suchbegriff **Kantega SSO for JIRA** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Kantega SSO for JIRA** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-kantega-sso-for-jira"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Kantega SSO for JIRA

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Kantega SSO for JIRA mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Kantega SSO for JIRA eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Kantega SSO for JIRA die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Kantega SSO for JIRA](#configure-kantega-sso-for-jira-sso)** , um die Einstellungen für das einmalige Anmelden auf der Anwendungsseite zu konfigurieren.
    1. **[Erstellen eines Kantega SSO for JIRA-Testbenutzers](#create-kantega-sso-for-jira-test-user)** , um eine Entsprechung von B. Simon in Kantega SSO for JIRA zu erhalten, die mit der Darstellung des Benutzers in Azure AD verknüpft ist.
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Kantega SSO for JIRA** zum Abschnitt **Verwalten** und wählen Sie **einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus, wenn Sie die Anwendung im **IDP-initiierten** Modus konfigurieren möchten:

    a. Geben Sie im Textfeld **Bezeichner** eine URL im folgenden Format ein: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<UNIQUE_ID>/login`

    b. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<UNIQUE_ID>/login`

5. Klicken Sie auf **Zusätzliche URLs festlegen**, und führen Sie den folgenden Schritt aus, wenn Sie die Anwendung im **SP-initiierten Modus** konfigurieren möchten:

    Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<UNIQUE_ID>/login`

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch den tatsächlichen Bezeichner, die Antwort-URL und die Anmelde-URL. Diese Werte werden während der Konfiguration des JIRA-Plug-Ins empfangen, die später im Tutorial beschrieben wird.

6. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um den Ihren Anforderungen entsprechenden **Verbundmetadaten-XML**-Code aus den verfügbaren Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

7. Kopieren Sie im Abschnitt **Kantega SSO for JIRA einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

Ermöglichen Sie B. Simon in diesem Abschnitt die Verwendung des einmaligen Anmeldens von Azure, indem Sie den Zugriff auf Kantega SSO for JIRA gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Kantega SSO for JIRA** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-kantega-sso-for-jira-sso"></a>Eine Anleitung zum Konfigurieren des einmaligen Anmeldens für Kantega SSO for JIRA

1. Melden Sie sich in einem anderen Webbrowserfenster an Ihrem lokalen JIRA-Server als Administrator an.

1. Fahren Sie über das Zahnrad, und klicken Sie dann auf die **Add-Ons**.

    ![Screenshot, in dem das Zahnradsymbol ausgewählt und im Dropdownmenü die Option „Add-Ons“ hervorgehoben ist](./media/kantegassoforjira-tutorial/settings.png)

1. Klicken Sie im Registerkartenabschnitt „Add-Ons“ auf **Nach neuen Add-Ons suchen**. Suchen Sie nach **Kantega SSO for JIRA (SAML & Kerberos)** , und klicken Sie auf die Schaltfläche **Installieren**, um das neue SAML-Plug-In zu installieren.

    ![Screenshot: Abschnitt „Find new Add-ons“ (Nach neuen Add-Ons suchen) mit der Eingabe „Kantega SSO for JIRA (SAML & Kerberos)“ im Suchfeld und der ausgewählten Schaltfläche „Install“ (Installieren)](./media/kantegassoforjira-tutorial/install-tab.png)

1. Die Installation des Plug-Ins wird gestartet.

    ![Screenshot: Dialogfeld „Installing“ (Wird installiert...) des Plug-Ins](./media/kantegassoforjira-tutorial/installation.png)

1. Gehen Sie nach Abschluss der Installation wie folgt vor: Klicken Sie auf **Schließen**.

    ![Screenshot: Dialogfeld „Installed and ready to go!“ (Installiert und einsatzbereit!) mit der ausgewählten Aktion „Close“ (Schließen)](./media/kantegassoforjira-tutorial/close-tab.png)

1.  Klicken Sie auf **Manage**.

    ![Screenshot: App-Seite „Kantega SSO“ mit der ausgewählten Schaltfläche „Manage“ (Verwalten)](./media/kantegassoforjira-tutorial/manage-tab.png)
    
1. Das neue Plug-In wird unter **INTEGRATIONEN** aufgeführt. Klicken Sie auf **Konfigurieren**, um das neue Plug-In zu konfigurieren.

    ![Screenshot, auf dem die Option „INTEGRATIONS“ (INTEGRATIONEN) im Navigationsmenü auf der linken Seite hervorgehoben und die Schaltfläche „Configure“ (Konfigurieren) im Abschnitt „Manage add-ons“ (Add-Ons verwalten) ausgewählt ist](./media/kantegassoforjira-tutorial/integration.png)

1. Im Abschnitt **SAML**: Wählen Sie in der Dropdownliste **Identitätsanbieter hinzufügen** die Option **Azure Active Directory (Azure AD)**.

    ![Screenshot: Dropdownmenü „Add identity provider“ (Identitätsanbieter hinzufügen) mit der ausgewählten Option „Azure Active Directory (Azure AD)“](./media/kantegassoforjira-tutorial/identity-provider.png)

1. Wählen Sie als Abonnementebene die Option **Basic**.

    ![Screenshot: Abschnitt „Preparing Azure AD“ (Azure AD wird vorbereitet...) mit Auswahl der Option „Basic“](./media/kantegassoforjira-tutorial/basic-tab.png)

1. Führen Sie im Abschnitt **App-Eigenschaften** die folgenden Schritte aus: 

    ![Screenshot: Abschnitt „App properties“ (App-Eigenschaften) mit dem hervorgehobenen Textfeld „App ID URL“ (App-ID-URL), der hervorgehobenen Schaltfläche zum Kopieren und der ausgewählten Schaltfläche „Next“ (Weiter)](./media/kantegassoforjira-tutorial/properties.png)

    1. Kopieren Sie den Wert für den **App-ID-URI**, und verwenden Sie ihn als **Bezeichner, Antwort-URL und Anmelde-URL** im Abschnitt **Grundlegende SAML-Konfiguration** des Azure-Portals.

    1. Klicken Sie auf **Weiter**.

1. Führen Sie im Abschnitt **Metadata import** (Metadatenimport) die folgenden Schritte aus: 

    ![Screenshot: Abschnitt „Metadata import“ (Metadatenimport) mit Auswahl der Option „Metadata file on my computer“ (Metadatendatei auf meinem Computer)](./media/kantegassoforjira-tutorial/metadata.png)

    1. Wählen Sie **Metadata file on my computer** (Metadatendatei auf meinem Computer), und laden Sie die Metadatendatei hoch, die Sie aus dem Azure-Portal heruntergeladen haben.

    1. Klicken Sie auf **Weiter**.

1. Führen Sie im Abschnitt **Name and SSO location** (Name und SSO-Standort) die folgenden Schritte aus:

    ![Screenshot: „Name and SSO location“ (Name und SSO-Standort) mit hervorgehobenem Textfeld „Identity provider name“ (Name des Identitätsanbieters) und Auswahl der Schaltfläche „Next“ (Weiter)](./media/kantegassoforjira-tutorial/location.png)

    1. Fügen Sie im Textfeld **Name des Identitätsanbieters** den Namen des Identitätsanbieters hinzu (z.B. Azure AD).

    1. Klicken Sie auf **Weiter**.

1. Überprüfen Sie das Signaturzertifikat, und klicken Sie auf **Weiter**.

    ![Screenshot: Abschnitt „Signature verification“ (Signaturüberprüfung) mit hervorgehobener Schaltfläche „Next“ (Weiter)](./media/kantegassoforjira-tutorial/certificate.png)

1. Führen Sie im Abschnitt **JIRA user accounts** (JIRA-Benutzerkonten) die folgenden Schritte aus:

    ![Screenshot: „JIRA user accounts“ (JIRA-Benutzerkonten) mit hervorgehobener Option „Create users in JIRA's Internal Directory if needed“ (Benutzer im internen JIRA-Verzeichnis erstellen, falls erforderlich) und ausgewählter Schaltfläche „Next“ (Weiter)](./media/kantegassoforjira-tutorial/accounts.png)

    1. Wählen Sie **Create users in JIRA's internal Directory if needed** (Benutzer im internen JIRA-Verzeichnis erstellen, falls erforderlich), und geben Sie den entsprechenden Namen der Gruppe für Benutzer ein (können mehrere durch Kommas getrennte Gruppen sein).

    1. Klicken Sie auf **Weiter**.

1. Klicken Sie auf **Fertig stellen**.

    ![Screenshot: Abschnitt „Summary“ (Zusammenfassung) mit ausgewählter Schaltfläche „Finish“ (Fertig stellen)](./media/kantegassoforjira-tutorial/finish-tab.png)

1. Führen Sie im Abschnitt **Known domains for Azure AD** (Bekannte Domänen für Azure AD) die folgenden Schritte aus:

    ![Einmaliges Anmelden konfigurieren](./media/kantegassoforjira-tutorial/save-tab.png)

    1. Wählen Sie im linken Bereich der Seite die Option **Known domains** (Bekannte Domänen).

    2. Geben Sie den Domänennamen im Textfeld **Known domains** (Bekannte Domänen) ein.

    3. Klicken Sie auf **Speichern**.

### <a name="create-kantega-sso-for-jira-test-user"></a>Erstellen eines Kantega SSO for JIRA-Testbenutzers

Damit sich Azure AD-Benutzer bei JIRA anmelden können, müssen sie in JIRA bereitgestellt werden. In Kantega SSO for JIRA ist die Bereitstellung eine manuelle Aufgabe.

**Führen Sie zum Bereitstellen eines Benutzerkontos die folgenden Schritte aus:**

1. Melden Sie sich bei Ihrem lokalen JIRA-Server als Administrator an.

1. Fahren Sie mit dem Mauszeiger über das Zahnrad, und klicken Sie auf **Benutzerverwaltung**.

    ![Screenshot, in dem das Zahnradsymbol ausgewählt und im Dropdownmenü die Option „User management“ (Benutzerverwaltung) hervorgehoben ist](./media/kantegassoforjira-tutorial/user.png) 

1. Klicken Sie im Registerkartenabschnitt **Benutzerverwaltung** auf **Benutzer erstellen**.

    ![Screenshot: Abschnitt „User management“ (Benutzerverwaltung), in dem die Schaltfläche „Create user“ (Benutzer erstellen) ausgewählt ist](./media/kantegassoforjira-tutorial/create-user.png) 

1. Führen Sie auf der Dialogfeldseite **Neuen Benutzer erstellen** die folgenden Schritte durch:

    ![Mitarbeiter hinzufügen](./media/kantegassoforjira-tutorial/new-user.png) 

    1. Geben Sie im Textfeld **E-Mail-Adresse** die E-Mail-Adresse des Benutzers, z.B. Brittasimon@contoso.com, ein.

    2. Geben Sie im Textfeld **Vollständiger Name** den vollständigen Namen des Benutzers, z.B. „Britta Simon“, ein.

    3. Geben Sie im Textfeld **Benutzername** die E-Mail-Adresse des Benutzers, z.B. Brittasimon@contoso.com, ein.

    4. Geben Sie im Textfeld **Kennwort** das Kennwort des Benutzers ein.

    5. Klicken Sie auf **Benutzer erstellen**.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

#### <a name="sp-initiated"></a>SP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für Kantega SSO for JIRA weitergeleitet, wo Sie den Anmeldeablauf initiieren können.  

* Navigieren Sie direkt zur Anmelde-URL für Kantega SSO for JIRA, und initiieren Sie den Anmeldeablauf.

#### <a name="idp-initiated"></a>IDP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Daraufhin sollten Sie automatisch bei der Kantega SSO for JIRA -Instanz angemeldet werden, für die Sie das einmalige Anmelden eingerichtet haben. 

Sie können auch den Microsoft-Bereich „Meine Apps“ verwenden, um die Anwendung in einem beliebigen Modus zu testen. Beim Klicken auf die Kachel „Kantega SSO for JIRA“ in „Meine Apps“ geschieht Folgendes: Wenn Sie die Anwendung im SP-Modus konfiguriert haben, werden Sie zum Initiieren des Anmeldeablaufs zur Anmeldeseite der Anwendung weitergeleitet. Wenn Sie die Anwendung im IDP-Modus konfiguriert haben, sollten Sie automatisch bei der Kantega SSO for JIRA-Instanz angemeldet werden, für die Sie das einmalige Anmelden eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Kantega SSO for JIRA können Sie die Sitzungssteuerung erzwingen, die Ihre vertraulichen Unternehmensdaten in Echtzeit vor der Exfiltration und Infiltration schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-aad)
