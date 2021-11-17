---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory in iHASCO Training | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und iHASCO Training konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/25/2021
ms.author: jeedes
ms.openlocfilehash: f1d5726df4f7e112529e6271813b9d2b732e63da
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132336029"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ihasco-training"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory in iHASCO Training

In diesem Tutorial erfahren Sie, wie Sie iHASCO Training in Azure Active Directory (Azure AD) integrieren. Die Integration von iHASCO Training in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf iHASCO Training hat.
* Ermöglichen Sie Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei iHASCO Training anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) bei iHASCO Training aktiviert ist.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* iHASCO Training unterstützt **SP**-initiiertes einmaliges Anmelden.
* iHASCO Training unterstützt die **Just-in-Time**-Benutzerbereitstellung.


## <a name="adding-ihasco-training-from-the-gallery"></a>Hinzufügen von iHASCO Training aus dem Katalog

Zum Konfigurieren der Integration von iHASCO Training in Azure AD müssen Sie iHASCO Training aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **iHASCO Training** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **iHASCO Training** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.


## <a name="configure-and-test-azure-ad-sso-for-ihasco-training"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für iHASCO Training

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit iHASCO Training mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in iHASCO Training eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit iHASCO Training die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für iHASCO Training](#configure-ihasco-training-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
    1. **[Erstellen eines iHASCO Training-Testbenutzers](#create-ihasco-training-test-user)** , um in iHASCO Training eine Entsprechung von B. Simon zu erhalten, die mit der Darstellung in Azure AD verknüpft ist.
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **iHASCO Training** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Geben Sie im Abschnitt **Grundlegende SAML-Konfiguration** die Werte für die folgenden Felder ein:

    a. Geben Sie im Feld **Bezeichner** eine URL im folgenden Format ein: `https://authentication.ihasco.co.uk/saml2/<ID>/metadata`.

    b. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://authentication.ihasco.co.uk/saml2/<ID>/acs`
    
    c. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://app.ihasco.co.uk/<ID>`

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Sie müssen diese Werte mit dem tatsächlichen Bezeichner, der Antwort-URL und der Anmelde-URL aktualisieren. Diese Werte erhalten Sie vom [Supportteam für den iHASCO Training-Client](mailto:support@ihasco.co.uk). Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

1. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** zum Eintrag **Zertifikat (Base64)** . Wählen Sie **Herunterladen** aus, um das Zertifikat herunterzuladen, und speichern Sie es auf Ihrem Computer.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

1. Kopieren Sie im Abschnitt **iHASCO Training einrichten** die entsprechende(n) URL(s) gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie Zugriff auf iHASCO Training gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **iHASCO Training** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-ihasco-training-sso"></a>Konfigurieren des einmaligen Anmeldens für iHASCO Training

1. Melden Sie sich bei der iHASCO Training-Unternehmenswebsite als Administrator an.

1. Klicken Sie im oberen rechten Navigationsbereich auf **Settings** (Einstellungen), scrollen Sie zur Kachel **ADVANCED** (ERWEITERT), und klicken Sie auf **Configure Single Sign On** (Einmaliges Anmelden konfigurieren).

    ![Screenshot: iHASCO Training-Schaltfläche für einmaliges Anmelden.](./media/ihasco-training-tutorial/settings.png)

1. Klicken Sie auf der Registerkarte **IDENTITY PROVIDERS** (IDENTITÄTSANBIETER) auf **Add provider** (Anbieter hinzufügen), und wählen Sie **SAML2** aus.

    ![Screenshot: „IDENTITY PROVIDERS“ (IDENTITÄTSANBIETER) für iHASCO Training.](./media/ihasco-training-tutorial/add-provider.png)

1. Führen Sie auf der Seite **Single Sign On / New SAML2** (Einmaliges Anmelden / Neues SAML2) die folgenden Schritte aus:

    ![Screenshot: Einmaliges Anmelden für iHASCO Training.](./media/ihasco-training-tutorial/single-sign-on.png)

    a. Geben Sie unter **GENERAL** (ALLGEMEIN) eine **Description** (Beschreibung) ein, um diese Konfiguration zu identifizieren.

    b. Fügen Sie unter **IDENTITY PROVIDER DETAILS** (IDENTITÄTSANBIETERDETAILS) in das Textfeld **Single Sign-On URL** (URL für einmaliges Anmelden) den Wert **Login URL** (Anmelde-URL) ein, den Sie aus dem Azure-Portal kopiert haben.

    c. Fügen Sie in das Textfeld **Single Logout URL** (URL für einmaliges Abmelden) den Wert der **Logout URL** (Abmelde-URL) ein, den Sie aus dem Azure-Portal kopiert haben.

    d. Fügen Sie im Textfeld **Entity ID** (Entitäts-ID) den Wert für den **Bezeichner** ein, den Sie aus dem Azure-Portal kopiert haben.

    e. Öffnen Sie das aus dem Azure-Portal heruntergeladene **Zertifikat (Base64)** im Editor, und fügen Sie den Inhalt in das Textfeld **X509 (Public) Certificate** ((Öffentliches) X509-Zertifikat) ein.

    f. Geben Sie unter **USER ATTRIBUTE MAPPING** (ZUORDNUNG DES BENUTZERATTRIBUTS) unter **Email address** (E-Mail-Adresse) einen Wert ein, z. B. `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    g. Geben Sie unter **First name** (Vorname) einen Wert ein, z. B. `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`.

    h. Geben Sie unter **Last name** (Nachname) einen Wert ein, z. B. `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`.

    i. Klicken Sie auf **Speichern**.

    j. Klicken Sie nach dem erneuten Laden der Seite auf **Enable now** (Jetzt aktivieren).

1. Klicken Sie im linken Navigationsbereich auf **Security** (Sicherheit), und wählen Sie **Single Sign On provider** (Anbieter für einmaliges Anmelden) als Methode zur **Registration** (Registrierung) und Ihre **Azure AD configuration** (Azure AD-Konfiguration) als **Selected provider** (Ausgewählter Anbieter) aus.

    ![Screenshot: iHASCO Training-Sicherheit.](./media/ihasco-training-tutorial/security.png)

1. Klicken Sie auf **Änderungen speichern**.

### <a name="create-ihasco-training-test-user"></a>Erstellen eines iHASCO Training-Testbenutzers

In diesem Abschnitt wird in iHASCO Training ein Benutzer mit dem Namen Britta Simon erstellt. iHASCO Training unterstützt die Just-in-Time-Benutzerbereitstellung, die standardmäßig aktiviert ist. Für Sie steht in diesem Abschnitt kein Aktionselement zur Verfügung. Ist ein Benutzer noch nicht in iHASCO Training vorhanden, wird nach der Authentifizierung ein neuer Benutzer erstellt.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für iHASCO Training weitergeleitet, wo Sie den Anmeldeflow initiieren können. 

* Rufen Sie direkt die iHASCO Training-Anmelde-URL auf, und initiieren Sie den Anmeldeflow.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie unter „Meine Apps“ auf die Kachel „iHASCO Training“ klicken, werden Sie zur Anmelde-URL für iHASCO Training weitergeleitet. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).


## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von iHASCO Training können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-any-app)
