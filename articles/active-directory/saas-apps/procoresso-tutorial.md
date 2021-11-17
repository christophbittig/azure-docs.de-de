---
title: 'Tutorial: Azure Active Directory-Integration mit Procore SSO | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Procore SSO konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/21/2021
ms.author: jeedes
ms.openlocfilehash: d7e6678db28f6edc2dfcaf36a656a309418ead8f
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132341899"
---
# <a name="tutorial-azure-active-directory-integration-with-procore-sso"></a>Tutorial: Azure Active Directory-Integration mit Procore SSO

In diesem Tutorial erfahren Sie, wie Sie Procore SSO in Azure Active Directory (Azure AD) integrieren. Die Integration von Procore SSO in Azure AD ermöglicht Folgendes:

* Sie können in Azure AD steuern, wer Zugriff auf Procore SSO haben soll.
* Sie können es Ihren Benutzern ermöglichen, sich mit ihrem Azure AD-Konto automatisch bei Procore SSO anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit Procore SSO konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Sollten Sie nicht über eine Azure AD-Umgebung verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) verwenden.
* Procore SSO-Abonnement mit SSO-Unterstützung

> [!NOTE]
> Diese Integration kann auch über die Azure AD-Umgebung für die US Government-Cloud verwendet werden. Sie finden diese Anwendung im Azure AD-Katalog für US Government-Cloudanwendungen und konfigurieren sie auf die gleiche Weise wie in der öffentlichen Cloud.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Procore SSO unterstützt **IDP**-initiiertes einmaliges Anmelden.

## <a name="add-procore-sso-from-the-gallery"></a>Hinzufügen von Procore SSO aus dem Katalog

Zum Konfigurieren der Integration von Procore SSO in Azure AD müssen Sie Procore SSO aus dem Katalog zur Liste der verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Procore SSO** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Procore SSO** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-procore-sso"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Procore SSO

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Procore SSO mithilfe eines Testbenutzers namens **B.Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Procore SSO eingerichtet werden.

Führen Sie die folgenden Schritte aus, um das einmalige Anmelden von Azure AD mit Procore SSO zu konfigurieren und zu testen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Procore SSO](#configure-procore-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines Procore SSO-Testbenutzers](#create-procore-sso-test-user)** , um in Procore SSO eine Entsprechung von B.Simon zu erhalten, die mit der Benutzerdarstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Procore SSO** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Im Abschnitt **Grundlegende SAML-Konfiguration** muss der Benutzer keine Schritte ausführen, weil die App bereits in Azure integriert ist.

5. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um den Ihren Anforderungen entsprechenden **Verbundmetadaten-XML**-Code aus den verfügbaren Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

6. Kopieren Sie im Abschnitt **Procore SSO einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B.Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie Zugriff auf Procore SSO gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Procore SSO** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-procore-sso"></a>Konfigurieren des einmaligen Anmeldens für Procore SSO 

1. Um das einmalige Anmelden aufseiten von **Procore SSO** zu konfigurieren, melden Sie sich als Administrator bei Ihrer Procore-Unternehmenswebsite an.

2. Klicken Sie auf der Dropdownliste der Toolbox auf **Admin**, um die Seite mit den Einstellungen für das einmalige Anmelden zu öffnen.

    ![Screenshot: Unternehmenswebsite von Procore mit Auswahl von „Directory“ (Verzeichnis)](./media/procoresso-tutorial/admin.png)

3. Fügen Sie die Werte wie unten beschrieben in die Felder ein.

    ![Screenshot: Dialogfeld „Add a Person“ (Person hinzufügen)](./media/procoresso-tutorial/setting.png)   

    a. Fügen Sie im Textfeld **Single Sign On Issuer URL** (SSO-Aussteller-URL) den Wert des **Azure AD-Bezeichners** ein, den Sie aus dem Azure-Portal kopiert haben.

    b. Fügen Sie im Feld **SAML Sign On Target URL** (Ziel-URL für SAML-Anmeldung) den Wert der **Anmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    c. Öffnen Sie jetzt die **Verbundmetadaten-XML-Datei**, die Sie aus dem Azure-Portal heruntergeladen haben, und kopieren Sie das Zertifikat in das Tag mit der Bezeichnung **X509Certificate**. Fügen Sie den kopierten Wert in das Feld **Single Sign On x509 Certificate** ein.

4. Klicken Sie auf **Save Changes**.

5. Nachdem Sie diese Einstellungen konfiguriert haben, müssen Sie den **Domänennamen**, mit dem Sie sich bei Procore anmelden (z.B. **contoso.com**), an das [Procore-Supportteam](https://support.procore.com/) senden, damit das Team das einmalige Anmelden im Verbund für diese Domäne aktiviert.

### <a name="create-procore-sso-test-user"></a>Erstellen eines Procore SSO-Testbenutzers

Führen Sie die folgenden Schritte aus, um einen Procore-Testbenutzer aufseiten von Procore SSO zu erstellen.

1. Melden Sie sich als Administrator bei Ihrer Procore-Unternehmenswebsite an.    

2. Klicken Sie in der Dropdownliste der Toolbox auf **Directory**, um die Verzeichnisseite für Ihr Unternehmen zu öffnen.

    ![Screenshot: Unternehmenswebsite von Procore mit Auswahl von „Directory“ (Verzeichnis) in der Toolbox](./media/procoresso-tutorial/directory.png)

3. Klicken Sie auf die Option **Add a Person**, um das Formular zu öffnen, und geben Sie die folgenden Optionen ein.

    ![Screenshot: Hinzufügen einer Person zu Boylan Construction und Eingabe von Benutzerinformationen](./media/procoresso-tutorial/user.png)

    a. Geben Sie im Textfeld **First Name** den Vornamen des Benutzers ein, z.B. **Britta**.

    b. Geben Sie im Textfeld **Last Name** den Nachnamen des Benutzers ein, z.B. **Simon**.

    c. Geben Sie im Textfeld **Email Address** die E-Mail-Adresse des Benutzers ein, z. B. BrittaSimon@contoso.com.

    d. Wählen Sie für **Permission Template** die Option **Apply Permission Template Later** aus.

    e. Klicken Sie auf **Erstellen**.

4. Prüfen Sie die Informationen für den neu hinzugefügten Kontakt, und aktualisieren Sie sie ggf.

    ![Screenshot: Bearbeitungsseite, auf der Sie die Benutzereinstellungen überprüfen können](./media/procoresso-tutorial/details.png)

5. Klicken Sie auf **Save and Send Invitiation** (wenn eine Einladung per E-Mail erforderlich ist) oder auf **Save** (zum direkten Speichern), um die Benutzerregistrierung abzuschließen.
    
    ![Screenshot: Aktuelle Projekteinstellungen mit Option zum Speichern und Senden einer Einladung](./media/procoresso-tutorial/save.png)

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen:

* Klicken Sie im Azure-Portal auf „Diese Anwendung testen“. Dadurch sollten Sie automatisch bei der Procore SSO-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie in „Meine Apps“ auf die Kachel „Procore SSO“ klicken, sollten Sie automatisch bei der Procore SSO-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren des einmaligen Anmeldens von Procore SSO können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-aad)
