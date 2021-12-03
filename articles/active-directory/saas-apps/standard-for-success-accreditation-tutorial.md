---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Standard for Success Accreditation | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Standard for Success Accreditation konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/18/2021
ms.author: jeedes
ms.openlocfilehash: 37dfb407f1c787f69684de9fe5acc0d46c93fc57
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132320591"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-standard-for-success-accreditation"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Standard for Success Accreditation

In diesem Tutorial erfahren Sie, wie Sie Standard for Success Accreditation in Azure Active Directory (Azure AD) integrieren. Die Integration von Standard for Success Accreditation in Azure AD ermöglicht Folgendes:

* Sie können in Azure AD steuern, wer Zugriff auf Standard for Success Accreditation haben soll.
* Sie können es Ihren Benutzern ermöglichen, sich mit ihrem Azure AD-Konto automatisch bei Standard for Success Accreditation anzumelden.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Standard for Success Accreditation-Abonnement mit SSO-Unterstützung

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Standard for Success Accreditation unterstützt **SP- und IDP-initiiertes** einmaliges Anmelden.

## <a name="add-standard-for-success-accreditation-from-the-gallery"></a>Hinzufügen von Standard for Success Accreditation aus dem Katalog

Um die Integration von Standard for Success Accreditation in Azure AD zu konfigurieren, müssen Sie Standard for Success Accreditation über den Katalog zu Ihrer Liste verwalteter SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Standard for Success Accreditation** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Standard for Success Accreditation** aus, und fügen Sie die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-standard-for-success-accreditation"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD Standard for Success Accreditation

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Standard for Success Accreditation mithilfe eines Testbenutzers namens **B.Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Standard for Success Accreditation eingerichtet werden.

Führen Sie die folgenden Schritte aus, um das einmalige Anmelden von Azure AD mit Standard for Success Accreditation zu konfigurieren und zu testen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)**, um Ihren Benutzern die Verwendung dieses Features zu ermöglichen
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)**, um B.Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen
1. **[Konfigurieren des einmaligen Anmeldens für Standard for Success Accreditation](#configure-standard-for-success-accreditation-sso)**, um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines Standard for Success Accreditation-Testbenutzers](#create-standard-for-success-accreditation-test-user)**, um in Standard for Success Accreditation eine Entsprechung von B.Simon zu erhalten, die mit der Benutzerdarstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Standard for Success Accreditation** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus, wenn Sie die Anwendung im **IDP-initiierten** Modus konfigurieren möchten:

    a. Geben Sie im Textfeld **Bezeichner** einen Wert nach folgendem Muster ein: `api://<ApplicationId>`

    b. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://edu.sfsed.com/access/saml_consume?did=<INSTITUTION-ID>`

1. Klicken Sie auf **Zusätzliche URLs festlegen**, und führen Sie die folgenden Schritte aus, wenn Sie die Anwendung im **SP-initiierten** Modus konfigurieren möchten:

    a. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://edu.sfsed.com/access/saml_int?did=<INSTITUTION-ID>`

    b. Geben Sie im Textfeld **Relayzustand** eine URL nach folgendem Muster ein: `https://edu.sfsed.com/access/saml_consume?did=<INSTITUTION-ID>`.

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Aktualisieren Sie diese Werte mit dem tatsächlichen Bezeichner, der Antwort-URL, der Anmelde-URL und dem Relayzustand. Diese Werte erhalten Sie vom [Supportteam für den Standard for Success Accreditation-Client](mailto:help_he@standardforsuccess.com). Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

1. Klicken Sie im Abschnitt **SAML-Signaturzertifikat** auf die Schaltfläche **Bearbeiten**, um das Dialogfeld **SAML-Signaturzertifikat** zu öffnen.

    ![Bearbeiten des SAML-Signaturzertifikats](common/edit-certificate.png)

1. Kopieren Sie im Abschnitt **SAML-Signaturzertifikat** unter **Fingerabdruck** den Fingerabdruckwert, und speichern Sie ihn auf Ihrem Computer.

    ![Kopieren des Fingerabdruckwerts](common/copy-thumbprint.png)

1. Kopieren Sie im Abschnitt **Standard for Success Accreditation einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

In diesem Abschnitt erstellen Sie im Azure-Portal einen Testbenutzer mit dem Namen B. Simon.

1. Wählen Sie im linken Bereich des Microsoft Azure-Portals **Azure Active Directory** > **Benutzer** > **Alle Benutzer** aus.
1. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.
1. Führen Sie unter den Eigenschaften für **Benutzer** die folgenden Schritte aus:
   1. Geben Sie im Feld **Name** die Zeichenfolge `B.Simon` ein.  
   1. Geben Sie im Feld **Benutzername** die Zeichenfolge username@institutiondomain.extension ein. Beispiel: `B.Simon@contoso.com`.
   1. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**, und notieren Sie sich den Wert aus dem Feld **Kennwort**.
   1. Klicken Sie auf **Erstellen**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie B.Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie Zugriff auf Standard for Success Accreditation gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Standard for Success Accreditation** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-standard-for-success-accreditation-sso"></a>Konfigurieren des einmaligen Anmeldens für Standard for Success Accreditation

1. Öffnen Sie ein neues Webbrowserfenster, und melden Sie sich bei der Website von Standard for Success Accreditation als Administrator mit Superuser-Zugriff an.

1. Klicken Sie im Menü auf **Admin Portal** (Verwaltungsportal).

1. Scrollen Sie nach unten zu **Single Sign On Settings** (Einstellungen für einmaliges Anmelden), klicken Sie auf den Link **Microsoft Azure Single Sign On** (Einmaliges Anmelden von Microsoft Azure), und führen Sie die folgenden Schritte aus:

    :::image type="content" source="./media/standard-for-success-accreditation-tutorial/configuration.png" alt-text="Screenshot: Aktivieren des einmaligen Anmeldens von Azure in Standard for Success Accreditation":::

    a. Aktivieren Sie das Kontrollkästchen **Enable Azure Single Sign On** (Einmaliges Anmelden von Azure aktivieren).

    b. Füllen Sie die Felder „URL“ und „Bezeichner“ mit den entsprechenden URLs aus, die sie aus dem SAML-Setup im Azure-Portal kopiert haben.

    c. Geben Sie im Textfeld **Application ID** (Anwendungs-ID) die Anwendungs-ID ein.

    d. Fügen Sie im Textfeld **Certificate Thumbprint** (Zertifikatfingerabdruck) den aus dem Azure-Portal kopierten **Fingerabdruckwert** ein.

    e. Klicken Sie auf **Speichern**. 

### <a name="create-standard-for-success-accreditation-test-user"></a>Erstellen eines Standard for Success Accreditation-Testbenutzers

1.  Melden Sie sich bei Standard for Success Accreditation als Administrator mit Superuser-Berechtigungen an.

1. Klicken Sie im Menü auf **Admin Portal** > **Create New Evaluatee** („Verwaltungsportal“ > „Neue auszuwertende Person erstellen“), und führen Sie die folgenden Schritte aus:

    ![Erstellen eines Testbenutzers](./media/standard-for-success-accreditation-tutorial/new-user.png)

    a. Geben Sie im Textfeld **First Name** (Vorname) den Buchstaben „B“ ein.

    b. Geben Sie im Textfeld **Last Name** (Nachname) den Namen „Simon“ ein.

    c. Geben Sie im Textfeld **University Email** (E-Mail-Adresse der Universität) die E-Mail-Adresse ein, die Sie in Azure für B. Simon hinzugefügt haben.

    d. Scrollen Sie nach unten, und klicken Sie auf **Create User** (Benutzer erstellen).

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

#### <a name="sp-initiated"></a>SP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für Standard for Success Accreditation weitergeleitet, wo Sie den Anmeldeflow initiieren können.  

* Rufen Sie direkt die Anmelde-URL für Standard for Success Accreditation auf, und initiieren Sie den Anmeldeflow.

#### <a name="idp-initiated"></a>IDP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch sollten Sie automatisch bei der Standard for Success Accreditation-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. 

Sie können auch den Microsoft-Bereich „Meine Apps“ verwenden, um die Anwendung in einem beliebigen Modus zu testen. Wenn Sie unter „Meine Apps“ auf die Kachel „Standard for Success Accreditation“ klicken, geschieht Folgendes: Falls Sie den SP-Modus konfiguriert haben, werden Sie zum Initiieren des Anmeldeflows zur Anmeldeseite der Anwendung weitergeleitet. Falls Sie den IDP-Modus konfiguriert haben, sollten Sie automatisch bei der Standard for Success Accreditation-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Standard for Success Accreditation können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-any-app)
