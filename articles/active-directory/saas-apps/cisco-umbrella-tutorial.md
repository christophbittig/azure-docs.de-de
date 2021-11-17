---
title: 'Tutorial: Azure Active Directory-Integration in Cisco Umbrella Admin SSO | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie einmaliges Anmelden zwischen Azure Active Directory und Cisco Umbrella Admin SSO konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/16/2021
ms.author: jeedes
ms.openlocfilehash: 824fc32e4e7a74946c997274311ee5da038188b2
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132311819"
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-umbrella-admin-sso"></a>Tutorial: Azure Active Directory-Integration in Cisco Umbrella Admin SSO

In diesem Tutorial erfahren Sie, wie Sie Cisco Umbrella Admin SSO in Azure Active Directory (Azure AD) integrieren. Die Integration von Cisco Umbrella Admin SSO in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf Cisco Umbrella Admin SSO hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Cisco Umbrella Admin SSO anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Cisco Umbrella Admin SSO-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Cisco Umbrella Admin SSO unterstützt **SP- und IDP-initiiertes** einmaliges Anmelden.

## <a name="add-cisco-umbrella-admin-sso-from-the-gallery"></a>Hinzufügen von Cisco Umbrella Admin SSO aus dem Katalog

Zum Konfigurieren der Integration von Cisco Umbrella Admin SSO in Azure AD müssen Sie Cisco Umbrella Admin SSO aus dem Katalog zu Ihrer Liste verwalteter SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Cisco Umbrella Admin SSO** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Cisco Umbrella Admin SSO** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-cisco-umbrella-admin-sso"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Cisco Umbrella Admin SSO

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Cisco Umbrella Admin SSO mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Cisco Umbrella Admin SSO eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Cisco Umbrella Admin SSO die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Cisco Umbrella Admin SSO](#configure-cisco-umbrella-admin-sso-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines Cisco Umbrella Admin SSO-Testbenutzers](#create-cisco-umbrella-admin-sso-test-user)** , um eine Entsprechung von B. Simon in Cisco Umbrella Admin SSO zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Cisco Umbrella Admin SSO** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Im Abschnitt **Grundlegende SAML-Konfiguration** muss der Benutzer keine Schritte ausführen, weil die App bereits in Azure integriert ist.

    a. Wenn Sie die Anwendung im **SP-initiierten Modus** konfigurieren möchten, führen Sie die folgenden Schritte durch:

    b. Klicken Sie auf **Zusätzliche URLs festlegen**.

    c. Geben Sie im Textfeld **Anmelde-URL** diese URL ein: `https://login.umbrella.com/sso`

5. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um das Ihrer Anforderung entsprechende **Metadaten-XML** aus den verfügbaren Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

6. Kopieren Sie im Abschnitt **Cisco Umbrella Admin SSO einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Cisco Umbrella Admin SSO gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Cisco Umbrella Admin SSO** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-cisco-umbrella-admin-sso-sso"></a>Konfigurieren des einmaligen Anmeldens für Cisco Umbrella Admin SSO

1. Melden Sie sich in einem anderen Browserfenster bei der Cisco Umbrella Admin SSO-Unternehmenswebsite als Administrator an.

2. Klicken Sie auf der linken Seite des Menüs auf **Admin** (Administrator), navigieren Sie zu **Authentication** (Authentifizierung), und klicken Sie dann auf **SAML**.

    ![Administrator](./media/cisco-umbrella-tutorial/admin.png)

3. Wählen Sie **Other** (Andere) aus, und klicken Sie dann auf **Next** (Weiter).

    ![Option „Other“ (Andere)](./media/cisco-umbrella-tutorial/other.png)

4. Klicken Sie auf der Seite **Cisco Umbrella Admin SSO Metadata** (Cisco Umbrella Admin SSO-Metadaten) auf **NEXT** (WEITER).

    ![Metadaten](./media/cisco-umbrella-tutorial/metadata.png)

5. Wenn Sie SAML vorab konfiguriert haben, wählen Sie auf der Registerkarte **Upload Metadata** (Metadaten hochladen) die Option **Click here to change them** (Zum Ändern hier klicken) aus, und führen Sie die folgenden Schritte aus.

    ![Schaltfläche „Next“ (Weiter)](./media/cisco-umbrella-tutorial/next.png)

6. Laden Sie unter **Option A: Upload XML file** (Option A: XML-Datei hochladen) die **Verbundmetadaten-XML**-Datei hoch, die Sie aus dem Azure-Portal heruntergeladen haben. Nach dem Hochladen der Metadaten werden die folgenden Werte automatisch aufgefüllt. Klicken Sie anschließend auf **Next** (Weiter).

    ![Dateiauswahl](./media/cisco-umbrella-tutorial/choose-file.png)

7. Klicken Sie im Abschnitt **Validate SAML Configuration** (SAML-Konfiguration überprüfen) auf **TEST YOUR SAML CONFIGURATION** (SAML-KONFIGURATION TESTEN).

    ![Test](./media/cisco-umbrella-tutorial/test.png)

8. Klicken Sie auf **SPEICHERN**.

### <a name="create-cisco-umbrella-admin-sso-test-user"></a>Erstellen eines Cisco Umbrella Admin SSO-Testbenutzers

Damit sich Azure AD-Benutzer bei Cisco Umbrella Admin SSO anmelden können, müssen sie in Cisco Umbrella Admin SSO bereitgestellt werden.  
Im Fall von Cisco Umbrella Admin SSO ist die Bereitstellung eine manuelle Aufgabe.

**Führen Sie zum Bereitstellen eines Benutzerkontos die folgenden Schritte aus:**

1. Melden Sie sich in einem anderen Browserfenster bei der Cisco Umbrella Admin SSO-Unternehmenswebsite als Administrator an.

2. Klicken Sie auf der linken Seite des Menüs auf **Admin** (Administrator), und navigieren Sie zu **Accounts** (Konten).

    ![Konto](./media/cisco-umbrella-tutorial/account.png)

3. Klicken Sie oben rechts auf der Seite **Accounts** (Konten) auf **Add** (Hinzufügen), und führen Sie die folgenden Schritte aus.

    ![Der Benutzer](./media/cisco-umbrella-tutorial/create-user.png)

    a. Geben Sie im Feld **First Name** (Vorname) den Vornamen ein (in diesem Beispiel **Britta**).

    b. Geben Sie im Feld **Last Name** (Nachname) den Nachnamen ein (in diesem Beispiel **Simon**).

    c. Wählen Sie unter **Choose Delegated Admin Role** (Delegierte Administratorrolle auswählen) Ihre Rolle aus.

    d. Geben Sie im Feld **Email address** (E-Mail-Adresse) die E-Mail-Adresse des Benutzers ein, z. B. **brittasimon\@contoso.com**.

    e. Geben Sie Ihr Kennwort in das Feld **Password** (Kennwort) ein.

    f. Wiederholen Sie die Eingabe des Kennworts im Feld **Confirm Password** (Kennwort bestätigen).

    g. Klicken Sie auf **ERSTELLEN**.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

#### <a name="sp-initiated"></a>SP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für Cisco Umbrella Admin SSO weitergeleitet, wo Sie den Anmeldeflow initiieren können.  

* Navigieren Sie direkt zur Anmelde-URL für Cisco Umbrella Admin SSO, und initiieren Sie den Anmeldeflow.

#### <a name="idp-initiated"></a>IDP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch sollten Sie automatisch bei der Cisco Umbrella Admin SSO-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. 

Sie können auch den Microsoft-Bereich „Meine Apps“ verwenden, um die Anwendung in einem beliebigen Modus zu testen. Beim Klicken auf die Kachel „Cisco Umbrella Admin SSO“ in „Meine Apps“ geschieht Folgendes: Wenn Sie den SP-Modus konfiguriert haben, werden Sie zum Initiieren des Anmeldeflows zur Anmeldeseite der Anwendung weitergeleitet. Wenn Sie den IDP-Modus konfiguriert haben, sollten Sie automatisch bei der Cisco Umbrella Admin SSO-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Cisco Umbrella Admin SSO können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-any-app)
