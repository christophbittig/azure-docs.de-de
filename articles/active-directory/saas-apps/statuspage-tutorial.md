---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit StatusPage | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und StatusPage konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/31/2021
ms.author: jeedes
ms.openlocfilehash: 24ea84daca5c6f613a336984dbd91202d664812d
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132320657"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-statuspage"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit StatusPage

In diesem Tutorial erfahren Sie, wie Sie StatusPage in Azure Active Directory (Azure AD) integrieren. Die Integration von PressPage in Azure AD ermöglicht Folgendes:

* Sie können in Azure AD steuern, wer auf StatusPage Zugriff hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei StatusPage anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit StatusPage konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Sollten Sie nicht über eine Azure AD-Umgebung verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) verwenden.
* StatusPage-Abonnement, für das einmaliges Anmelden aktiviert ist.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* StatusPage unterstützt **IDP** initiiertes einmaliges Anmelden.

## <a name="add-statuspage-from-the-gallery"></a>Hinzufügen von StatusPage aus dem Katalog

Zum Konfigurieren der Integration von StatusPage in Azure AD müssen Sie StatusPage aus dem Katalog zur Liste der verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **StatusPage** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **StatusPage** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-statuspage"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für StatusPage

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden mit Azure AD bei StatusPage mithilfe einer Testbenutzerin namens **Britta Simon**.
Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in StatusPage eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit StatusPage die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
1. **[Konfigurieren des einmaligen Anmeldens für StatusPage](#configure-statuspage-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines StatusPage-Testbenutzers](#create-statuspage-test-user)** , um eine Entsprechung von Britta Simon in StatusPage zu erhalten, die mit der Darstellung des Benutzers in Azure AD verknüpft ist.
6. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **AskYourTeam** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** die folgenden Schritte aus:

    a. Geben Sie im Textfeld **Bezeichner** eine URL in einem der folgenden Formate ein:

    | Bezeichner |
    |--------------|
    | `https://<subdomain>.statuspagestaging.com/` |
    | `https://<subdomain>.statuspage.io/` |
    |

    b. Geben Sie im Textfeld **Antwort-URL** eine URL in einem der folgenden Formate ein:

     | Antwort-URL |
    |--------------|
    | `https://<subdomain>.statuspagestaging.com/sso/saml/consume` |
    | `https://<subdomain>.statuspage.io/sso/saml/consume` |
    |

    > [!NOTE]
    > Wenden Sie sich unter [SupportTeam@statuspage.io](mailto:SupportTeam@statuspage.io)an das Supportteam von StatusPage, um die für das Konfigurieren der einmaligen Anmeldung erforderlichen Metadaten anzufordern. 
    >
    > a. Kopieren Sie in den Metadaten den Wert für „Aussteller“, und fügen Sie ihn in das Textfeld **Bezeichner** ein.
    >
    > b. Kopieren Sie in den Metadaten den Wert für „Antwort-URL“, und fügen Sie ihn in das Textfeld **Antwort-URL** ein.

5. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um das Ihrer Anforderung entsprechende **Zertifikat (Base64)** aus den angegebenen Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

6. Kopieren Sie im Abschnitt **StatusPage einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf StatusPage gewähren.

1. Wählen Sie im Azure-Portal nacheinander die Optionen **Unternehmensanwendungen**, **Alle Anwendungen** und **StatusPage**.

2. Wählen Sie in der Anwendungsliste den Eintrag **StatusPage** aus.

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen**, und wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **Britta Simon** aus, und klicken Sie dann unten im Bildschirm auf die Schaltfläche **Auswählen**.

6. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-statuspage-sso"></a>Konfigurieren des einmaligen Anmeldens für StatusPage

1. Wenn Sie die Konfiguration in StatusPage automatisieren möchten, müssen Sie die **Browsererweiterung „Meine Apps“ für die sichere Anmeldung** installieren, indem Sie auf **Erweiterung installieren** klicken.

    ![Erweiterung „Meine Apps“](common/install-myappssecure-extension.png)

2. Klicken Sie nach dem Hinzufügen der Erweiterung zum Browser auf **StatusPage einrichten**, um zur Anwendung StatusPage weitergeleitet zu werden. Geben Sie dort die Administratoranmeldeinformationen ein, um sich bei StatusPage anzumelden. Die Browsererweiterung konfiguriert die Anwendung automatisch für Sie und automatisiert die Schritte 3 bis 6.

    ![Einrichtungskonfiguration](common/setup-sso.png)

3. Wenn Sie StatusPage manuell einrichten möchten, melden Sie sich in einem anderen Webbrowserfenster als Administrator bei der StatusPage-Unternehmenswebsite an.

1. Klicken Sie auf der Hauptsymbolleiste auf **Konto verwalten**.

    ![Screenshot: Auswahl der Option „Konto verwalten“ auf der StatusPage-Unternehmenswebsite](./media/statuspage-tutorial/account.png)

1. Klicken Sie auf die Registerkarte **Einmaliges Anmelden** .

    ![Screenshot: Registerkarte „Einmaliges Anmelden“](./media/statuspage-tutorial/tab.png)

1. Führen Sie auf der Seite für die SSO-Einrichtung die folgenden Schritte aus:

    ![Screenshot: Seite für die SSO-Einrichtung, auf der Sie die beschriebenen Werte eingeben können](./media/statuspage-tutorial/setup.png)

    ![Screenshot: Schaltfläche „Konfiguration speichern“](./media/statuspage-tutorial/configuration.png)

    a. Fügen Sie in das Textfeld **SSO Target URL** (SSO-Ziel-URL) den Wert der **Anmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    b. Öffnen Sie das heruntergeladene Zertifikat im Editor, kopieren Sie den Inhalt, und fügen Sie ihn anschließend in das Textfeld **Zertifikat** ein.

    c. Klicken Sie auf **KONFIGURATION SPEICHERN**.

### <a name="create-statuspage-test-user"></a>Erstellen eines StatusPage-Testbenutzers

Das Ziel dieses Abschnitts ist das Erstellen eines Benutzers namens Britta Simon in StatusPage.

StatusPage unterstützt die Just-in-Time-Bereitstellung. Sie haben sie bereits unter [Konfigurieren der einmaligen Anmeldung in Azure AD](#configure-azure-ad-sso) aktiviert.

**Um einen Benutzer namens Britta Simon in StatusPage zu erstellen, führen Sie die folgenden Schritte aus:**

1. Melden Sie sich bei der StatusPage-Unternehmenswebsite als Administrator an.

1. Klicken Sie im Menü oben auf **Konto verwalten**.

    ![Screenshot: Auswahl der Option „Konto verwalten“ auf der StatusPage-Unternehmenswebsite](./media/statuspage-tutorial/account.png)

1. Klicken Sie auf die Registerkarte **Teammitglieder**.
  
    ![Screenshot: Registerkarte „Teammitglieder“](./media/statuspage-tutorial/sandbox.png) 

1. Klicken Sie auf **TEAMMITGLIEDER HINZUFÜGEN**.
  
    ![Screenshot: Schaltfläche „Teammitglied hinzufügen“](./media/statuspage-tutorial/team.png) 

1. Geben Sie in die Textfelder **E-Mail-Adresse**, **Vorname** und **Nachname** die entsprechenden Informationen eines gültigen Benutzers ein, den Sie bereitstellen möchten. 

    ![Screenshot: Dialogfeld „Benutzer hinzufügen“, in dem Sie die beschriebenen Werte eingeben können](./media/statuspage-tutorial/user.png) 

1. Wählen Sie als **Rolle** die Option **Clientadministrator** aus.

1. Klicken Sie auf **KONTO ERSTELLEN**.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen:

* Klicken Sie im Azure-Portal auf „Diese Anwendung testen“. Dadurch sollten Sie automatisch bei der StatusPage-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie in „Meine Apps“ auf die Kachel „StatusPage“ klicken, sollten Sie automatisch bei der StatusPage-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von StatusPage können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-any-app)
