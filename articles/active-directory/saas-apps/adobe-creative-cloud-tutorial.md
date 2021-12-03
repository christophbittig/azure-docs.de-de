---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Adobe Creative Cloud | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Adobe Creative Cloud konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/13/2021
ms.author: jeedes
ms.openlocfilehash: 15a94bcd0dc3fa7f6eb3f18a793a562947b6d07f
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132281018"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-adobe-creative-cloud"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Adobe Creative Cloud

In diesem Tutorial erfahren Sie, wie Sie Adobe Creative Cloud in Azure Active Directory (Azure AD) integrieren. Die Integration von Adobe Creative Cloud in Azure AD ermöglicht Folgendes:

* Sie können in Azure AD steuern, wer Zugriff auf Adobe Creative Cloud haben soll.
* Sie können es Ihren Benutzern ermöglichen, sich mit ihrem Azure AD-Konto automatisch bei Adobe Creative Cloud anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* SSO-fähiges Adobe Creative Cloud-Abonnement

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Adobe Creative Cloud unterstützt **SP-initiiertes** einmaliges Anmelden.

## <a name="add-adobe-creative-cloud-from-the-gallery"></a>Hinzufügen von Adobe Creative Cloud aus dem Katalog

Zum Konfigurieren der Integration von Adobe Creative Cloud in Azure AD müssen Sie Adobe Creative Cloud aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Adobe Creative Cloud** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich die Option **Adobe Creative Cloud** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-adobe-creative-cloud"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Adobe Creative Cloud

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Adobe Creative Cloud mithilfe eines Testbenutzers namens **B.Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Adobe Creative Cloud eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Adobe Creative Cloud die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Adobe Creative Cloud](#configure-adobe-creative-cloud-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
    1. **[Erstellen eines Adobe Creative Cloud-Testbenutzers](#create-adobe-creative-cloud-test-user)** , um in Adobe Creative Cloud eine Entsprechung von B.Simon zu erhalten, die mit der Darstellung des Benutzers in Azure AD verknüpft ist.
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Adobe Creative Cloud** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Geben Sie im Abschnitt **Grundlegende SAML-Konfiguration** die Werte für die folgenden Felder ein:

    a. Geben Sie im Textfeld **Anmelde-URL** die URL ein: `https://adobe.com`.

    b. Geben Sie im Textfeld **Bezeichner (Entitäts-ID)** eine URL im folgenden Format ein: `https://www.okta.com/saml2/service-provider/<token>`.

    > [!NOTE]
    > Der ID-Wert ist nicht der tatsächliche Wert. Befolgen Sie die Anleitung in Schritt 4 des Abschnitts zum **Konfigurieren des einmaligen Anmeldens für Adobe Cloud**. Bei diesem Vorgang können Sie die **Verbundmetadaten-XML-Datei** öffnen und den Wert der Entitäts-ID daraus entnehmen und als Bezeichnerwert in die Azure AD-Konfiguration einfügen. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

1. Ihre Adobe Creative Cloud-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Daher müssen Sie Ihrer Konfiguration der SAML-Tokenattribute benutzerdefinierte Attributzuordnungen hinzufügen. Der folgende Screenshot zeigt die Liste der Standardattribute.

    ![image](common/edit-attribute.png)

1. Darüber hinaus wird von der Adobe Creative Cloud-Anwendung erwartet, dass in der SAML-Antwort noch einige weitere Attribute zurückgegeben werden (siehe unten). Diese Attribute werden ebenfalls vorab aufgefüllt, Sie können sie jedoch nach Bedarf überprüfen.

    | Name | Quellattribut|
    |----- | --------- |
    | FirstName | user.givenname |
    | LastName | user.surname |
    | Email | user.mail |

    > [!NOTE]
    > Benutzer benötigen eine gültige Microsoft 365-ExO-Lizenz, damit der E-Mail-Anspruchswert in der SAML-Antwort aufgefüllt wird.

1. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** zu **Verbunddaten-XML**, und wählen Sie **Herunterladen** aus, um die XML-Metadatendatei herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

1. Kopieren Sie im Abschnitt **Adobe Creative Cloud einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Adobe Creative Cloud gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Adobe Creative Cloud** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Klicken Sie auf **Benutzer hinzufügen**. Wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste der Benutzer die Option **B.Simon** aus. Wählen Sie anschließend am unteren Bildschirmrand **Auswählen** aus.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Wählen Sie im Dialogfeld **Zuweisung hinzufügen** die Option **Zuweisen** aus.

## <a name="configure-adobe-creative-cloud-sso"></a>Konfigurieren des einmaligen Anmeldens für Adobe Creative Cloud

1. Melden Sie sich in einem anderen Webbrowserfenster bei der [Adobe-Administratorkonsole](https://adminconsole.adobe.com) als Systemadministrator an.

1. Navigieren Sie auf der oberen Navigationsleiste zu **Settings** (Einstellungen), und wählen Sie dann **Identity** (Identität) aus. Die Liste der Verzeichnisse wird geöffnet. Wählen Sie das gewünschte Verbundverzeichnis aus.

1. Wählen Sie auf der Seite **Directory Details** (Verzeichnisdetails) die Option **Configure** (Konfigurieren) aus.

1. Kopieren Sie die Entitäts-ID und die ACS-URL (Assertionsverbraucherdienst-URL oder Antwort-URL). Geben Sie die URLs in die entsprechenden Felder im Azure-Portal ein.

    ![Konfigurieren des einmaligen Anmeldens aufseiten der App](./media/adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_003.png)

    a. Verwenden Sie den von Adobe bereitgestellten Entitäts-ID-Wert für **Bezeichner** im Dialogfeld **App-Einstellungen konfigurieren**.

    b. Verwenden Sie die von Adobe bereitgestellte ACS-URL (Assertionsverbraucherdienst-URL) für **Antwort-URL** im Dialogfeld **App-Einstellungen konfigurieren**.

1. Laden Sie am unteren Seitenrand die **Verbunddaten-XML-Datei** hoch, die Sie aus dem Azure-Portal heruntergeladen haben. 

    ![Verbunddaten-XML-Datei](https://helpx.adobe.com/content/dam/help/en/enterprise/kb/configure-microsoft-azure-with-adobe-sso/jcr_content/main-pars/procedure/proc_par/step_228106403/step_par/image_copy/saml_signinig_certificate.png "IdP-Metadaten-XML")

1. Wählen Sie **Speichern** aus.

### <a name="create-adobe-creative-cloud-test-user"></a>Erstellen eines Adobe Creative Cloud-Testbenutzers

Damit sich Azure AD-Benutzer bei Adobe Creative Cloud anmelden können, müssen sie in Adobe Creative Cloud bereitgestellt werden. Im Fall von Adobe Creative Cloud ist die Bereitstellung eine manuelle Aufgabe.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>So stellen Sie Benutzerkonten bereit:

1. Melden Sie sich bei der [Adobe-Administratorkonsole](https://adminconsole.adobe.com) als Administrator an.

2. Fügen Sie den Benutzer in der Adobe-Konsole als „Federated ID“ (Verbund-ID) hinzu, und weisen Sie diesem ein Produktprofil zu. Ausführliche Informationen zum Hinzufügen von Benutzern finden Sie unter [Hinzufügen von Benutzern in der Adobe-Administratorkonsole](https://helpx.adobe.com/enterprise/using/users.html#Addusers).

3. Geben Sie nun Ihre E-Mail-Adresse und Ihren UPN in das Adobe-Anmeldeformular ein, und drücken Sie die TAB-TASTE. Daraufhin sollte der Verbund mit Azure AD eingerichtet sein:
   * Webzugriff: www\.adobe.com > sign-in
   * Im Desktop-App-Hilfsprogramm > sign-in
   * In der Anwendung > help > sign-in

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für Adobe Creative Cloud weitergeleitet, wo Sie den Anmeldeflow initiieren können. 

* Rufen Sie direkt die Adobe Creative Cloud-Anmelde-URL auf, und initiieren Sie den Anmeldeflow.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie unter „Meine Apps“ auf die Kachel „Adobe Creative Cloud“ klicken, werden Sie zur Anmelde-URL für Adobe Creative Cloud umgeleitet. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Adobe Creative Cloud können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-any-app)
