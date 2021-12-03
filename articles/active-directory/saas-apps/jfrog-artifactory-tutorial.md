---
title: 'Tutorial: Azure Active Directory-Integration mit JFrog Artifactory | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und JFrog Artifactory konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/09/2021
ms.author: jeedes
ms.openlocfilehash: 551ec61a167f81be638c72604d982e1903b33b91
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132295388"
---
# <a name="tutorial-integrate-jfrog-artifactory-with-azure-active-directory"></a>Tutorial: Integrieren von JFrog Artifactory in Azure Active Directory

In diesem Tutorial erfahren Sie, wie Sie JFrog Artifactory in Azure Active Directory (Azure AD) integrieren. Die Integration von JFrog Artifactory in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf JFrog Artifactory hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei JFrog Artifactory anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* JFrog Artifactory-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* JFrog Artifactory unterstützt **SP- und IDP-initiiertes** einmaliges Anmelden.
* JFrog Artifactory unterstützt die **Just-In-Time**-Benutzerbereitstellung.

## <a name="add-jfrog-artifactory-from-the-gallery"></a>Hinzufügen von JFrog Artifactory aus dem Katalog

Zum Konfigurieren der Integration von JFrog Artifactory in Azure AD müssen Sie JFrog Artifactory aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **JFrog Artifactory** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **JFrog Artifactory** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-jfrog-artifactory"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für JFrog Artifactory

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit JFrog Artifactory mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in JFrog Artifactory eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit JFrog Artifactory die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für JFrog Artifactory](#configure-jfrog-artifactory-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines JFrog Artifactory-Testbenutzers](#create-jfrog-artifactory-test-user)** , um eine Entsprechung von B. Simon in JFrog Artifactory zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **JFrog Artifactory** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Geben Sie im Abschnitt **Grundlegende SAML-Konfiguration** die Werte in die folgenden Felder ein, wenn Sie die Anwendung im **IDP**-initiierten Modus konfigurieren möchten:

    a. Geben Sie im Textfeld **Bezeichner** eine URL ein, die die Artifactory-URL widerspiegelt.

    b. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein:
    
    - Für selbstgehostete Artifactory-Anwendungen: `https://<servername>.jfrog.io/artifactory/webapp/saml/loginResponse`
    - Für Artifactory-SaaS: `https://<servername>.jfrog.io/<servername>/webapp/saml/loginResponse`

1. Klicken Sie auf **Zusätzliche URLs festlegen**, und führen Sie den folgenden Schritt aus, wenn Sie die Anwendung im **SP-initiierten Modus** konfigurieren möchten:

    Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein:
    - Für selbstgehostete Artifactory-Anwendungen: `https://<servername>.jfrog.io/<servername>/webapp/`
    - Für Artifactory-SaaS: `https://<servername>.jfrog.io/ui/login`

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Sie müssen diese Werte mit dem tatsächlichen Bezeichner, der Antwort-URL und der Anmelde-URL aktualisieren. Diese Werte erhalten Sie vom [Supportteam für den JFrog Artifactory-Client](https://support.jfrog.com). Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

1. Die JFrog Artifactory-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Daher müssen Sie Ihrer Konfiguration der SAML-Tokenattribute benutzerdefinierte Attributzuordnungen hinzufügen. Der folgende Screenshot zeigt die Liste der Standardattribute. Klicken Sie auf das Symbol **Bearbeiten**, um das Dialogfeld „Benutzerattribute & Ansprüche“ zu öffnen.

    ![Screenshot der Benutzerattribute, auf dem das Bearbeitungssteuerelement hervorgehoben ist](common/edit-attribute.png)

1. Darüber hinaus wird von der JFrog Artifactory-Anwendung erwartet, dass in der SAML-Antwort noch einige weitere Attribute zurückgegeben werden. Klicken Sie im Abschnitt **Benutzerattribute und Ansprüche** auf **Gruppenanspruch hinzufügen** und führen Sie die folgenden Schritte aus:

    a. Klicken Sie auf **Öffnen** neben **Im Anspruch zurückgegebene Gruppen**.

    ![Screenshot: „User Attributes & Claims“ (Benutzerattribute und -ansprüche) mit ausgewähltem Symbol „Bearbeiten“](./media/jfrog-artifactory-tutorial/configuration-4.png)

    ![Screenshot: Abschnitt „Gruppenansprüche“ in dem die Option „Alle Gruppen“ ausgewählt ist](./media/jfrog-artifactory-tutorial/configuration-5.png)

    b. Wählen Sie **Alle Gruppen** aus der Optionsfeldliste aus.

    c. Klicken Sie auf **Speichern**.

4. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** zum Eintrag **Zertifikat (Base64)** . Wählen Sie **Herunterladen** aus, um das Zertifikat herunterzuladen, und speichern Sie es auf Ihrem Computer.

    ![Downloadlink für das Zertifikat](./media/jfrog-artifactory-tutorial/certificate-base.png)

6. Konfigurieren Sie die Artifactory-Instanz (SAML-Dienstanbietername) mit dem Feld „Bezeichner“ (siehe Schritt 4). Kopieren Sie im Abschnitt **JFrog Artifactory einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

   - Für selbstgehostete Artifactory-Anwendungen: `https://<servername>.jfrog.io/artifactory/webapp/saml/loginResponse` 
   - Für Artifactory-SaaS: `https://<servername>.jfrog.io/<servername>/webapp/saml/loginResponse`

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf JFrog Artifactory gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **JFrog Artifactory** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-jfrog-artifactory-sso"></a>Konfigurieren des einmaligen Anmeldens für JFrog Artifactory

Zum Konfigurieren des einmaligen Anmeldens aufseiten von **JFrog Artifactory** müssen Sie das heruntergeladene **Zertifikat (Rohdaten)** und die kopierten URLs aus dem Azure-Portal an das [Supportteam von JFrog Artifactory](https://support.jfrog.com) senden. Es führt die Einrichtung durch, damit die SAML-SSO-Verbindung auf beiden Seiten richtig festgelegt ist.

### <a name="create-jfrog-artifactory-test-user"></a>Erstellen eines JFrog Artifactory-Testbenutzers

In diesem Abschnitt wird in JFrog Artifactory ein Benutzer namens B. Simon erstellt. JFrog Artifactory unterstützt die Just-in-Time-Benutzerbereitstellung (standardmäßig aktiviert). Für Sie steht in diesem Abschnitt kein Aktionselement zur Verfügung. Ist ein Benutzer noch nicht in JFrog Artifactory vorhanden, wird nach der Authentifizierung ein neuer Benutzer erstellt.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

#### <a name="sp-initiated"></a>SP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für JFrog Artifactory weitergeleitet, wo Sie den Anmeldeflow initiieren können.  

* Rufen Sie direkt die JFrog Artifactory-Anmelde-URL auf, und initiieren Sie den Anmeldeflow.

#### <a name="idp-initiated"></a>IDP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch sollten Sie automatisch bei der JFrog Artifactory-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. 

Sie können auch den Microsoft-Bereich „Meine Apps“ verwenden, um die Anwendung in einem beliebigen Modus zu testen. Beim Klicken auf die Kachel „JFrog Artifactory“ in „Meine Apps“ geschieht Folgendes: Wenn Sie den SP-Modus konfiguriert haben, werden Sie zum Initiieren des Anmeldeflows zur Anmeldeseite der Anwendung weitergeleitet. Wenn Sie den IDP-Modus konfiguriert haben, sollten Sie automatisch bei der JFrog Artifactory-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von JFrog Artifactory können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-any-app)
