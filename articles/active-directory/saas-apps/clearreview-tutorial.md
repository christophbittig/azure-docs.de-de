---
title: 'Tutorial: Azure Active Directory-Integration mit Clear Review | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Clear Review konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/19/2021
ms.author: jeedes
ms.openlocfilehash: 190558bcf528ff45d16a215f4cdd4b6bd1a37963
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132305159"
---
# <a name="tutorial-azure-active-directory-integration-with-clear-review"></a>Tutorial: Azure Active Directory-Integration mit Clear Review

In diesem Tutorial erfahren Sie, wie Sie Clear Review in Azure Active Directory (Azure AD) integrieren. Die Integration von Clear Review in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf Clear Review hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Clear Review anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit Clear Review konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Sollten Sie nicht über eine Azure AD-Umgebung verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Clear Review-Abonnement, für das einmaliges Anmelden aktiviert ist

> [!NOTE]
> Diese Integration kann auch über die Azure AD-Umgebung für die US Government-Cloud verwendet werden. Sie finden diese Anwendung im Azure AD-Katalog für US Government-Cloudanwendungen und konfigurieren sie auf die gleiche Weise wie in der öffentlichen Cloud.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Clear Review unterstützt **SP- und IDP-initiiertes** einmaliges Anmelden.

## <a name="add-clear-review-from-the-gallery"></a>Hinzufügen von Clear Review aus dem Katalog

Zum Konfigurieren der Integration von Clear Review in Azure AD müssen Sie Clear Review aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Clear Review** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Clear Review** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-clear-review"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Clear Review

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Clear Review mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Clear Review eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Clear Review die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Clear Review](#configure-clear-review-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines Clear Review-Testbenutzers](#create-clear-review-test-user)** , um ein Pendant von B. Simon in Clear Review zu erhalten, das mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Clear Review** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus, wenn Sie die Anwendung im **IDP-initiierten** Modus konfigurieren möchten:

    a. Geben Sie im Textfeld **Bezeichner** eine URL im folgenden Format ein: `https://<CUSTOMER_NAME>.clearreview.com/sso/metadata/`

    b. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://<CUSTOMER_NAME>.clearreview.com/sso/acs/`

5. Klicken Sie auf **Zusätzliche URLs festlegen**, und führen Sie den folgenden Schritt aus, wenn Sie die Anwendung im **SP-initiierten Modus** konfigurieren möchten:

    Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<CUSTOMER_NAME>.clearreview.com`

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Sie müssen diese Werte mit dem tatsächlichen Bezeichner, der Antwort-URL und der Anmelde-URL aktualisieren. Wenden Sie sich an das [Supportteam für den Clear Review-Client](https://clearreview.com/contact/), um diese Werte zu erhalten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

6. Die Clear Review-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Daher müssen Sie Ihrer Konfiguration der SAML-Tokenattribute benutzerdefinierte Attributzuordnungen hinzufügen. Der folgende Screenshot zeigt die Liste der Standardattribute. **nameidentifier** ist hier **user.userprincipalname** zugeordnet. Die Clear Review-Anwendung erwartet, dass **nameidentifier** der Wert **user.mail** zugeordnet ist. Sie müssen die Attributzuordnung daher entsprechend ändern, indem Sie auf das Symbol **Bearbeiten** klicken.

    ![Screenshot der Benutzerattribute mit ausgewähltem Symbol „Bearbeiten“](common/edit-attribute.png)

7. Führen Sie im Dialogfeld **Benutzerattribute und Ansprüche** die folgenden Schritte aus:

    a. Klicken Sie auf das **Symbol „Bearbeiten“** rechts vom **Namensbezeichnerwert**.

    ![Screenshot von „Benutzerattribute und Ansprüche“, bei dem das Bearbeitungssymbol ausgewählt ist](./media/clearreview-tutorial/attribute-2.png)

    ![Screenshot des Dialogfelds „Benutzeransprüche verwalten“, in dem Sie die hier beschriebenen Werte eingeben können](./media/clearreview-tutorial/attribute-1.png)

    b. Wählen Sie in der Liste **Quellattribut** den Attributwert **user.mail** für diese Zeile aus.

    c. Klicken Sie auf **Speichern**.

8. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um das Ihrer Anforderung entsprechende **Zertifikat (Base64)** aus den angegebenen Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

9. Kopieren Sie im Abschnitt **Clear Review einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Clear Review gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste die Option **Clear Review** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-clear-review-sso"></a>Konfigurieren des einmaligen Anmeldens für Clear Review

1. Wenn Sie das einmalige Anmelden auf der Seite von **Clear Review** konfigurieren möchten, öffnen Sie das **Clear Review**-Portal mit Administratoranmeldeinformationen.

2. Wählen Sie **Admin** aus dem linken Navigationsbereich aus.

    ![Screenshot des Clear Review-Portals, in dem „Admin“ ausgewählt ist](./media/clearreview-tutorial/admin.png)

3. Klicken Sie im Abschnitt **Integrationen** am unteren Rand der Seite auf die Schaltfläche **Änderung** rechts neben **Einstellungen für einmaliges Anmelden**.

    ![Screenshot der Schaltfläche „Ändern“ für einmaliges Anmelden](./media/clearreview-tutorial/integrations.png)

4. Führen Sie auf der Seite **Einstellungen für einmaliges Anmelden (SSO)** die folgenden Schritte aus:

    ![Screenshot des Abschnitts „Einstellungen für einmaliges Anmelden“ zum Eingeben der Informationen in diesem Schritt](./media/clearreview-tutorial/settings.png)

    a. Fügen Sie in das Textfeld **Issuer URL** (Aussteller-URL) den Wert des **Azure AD-Bezeichners** ein, den Sie aus dem Azure-Portal kopiert haben.

    b. Fügen Sie in das Textfeld **SAML Endpoint** (SAML-Endpunkt) den Wert der **Anmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.  

    c. Fügen Sie in das Textfeld **SLO Endpoint** (SLO-Endpunkt) den Wert der **Abmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.  

    d. Öffnen Sie das heruntergeladene Zertifikat im Editor, und fügen Sie den Inhalt in das Textfeld **X.509-Zertifikat** ein.   

    e. Klicken Sie auf **Speichern**.

### <a name="create-clear-review-test-user"></a>Erstellen eines Clear Review-Testbenutzers

In diesem Abschnitt erstellen Sie in Clear Review einen Benutzer namens Britta Simon. Wenden Sie sich an das [Supportteam von Clear Review](https://clearreview.com/contact/), um die Benutzer der Clear Review-Plattform hinzufügen zu lassen.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

#### <a name="sp-initiated"></a>SP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für Clear Review weitergeleitet, wo Sie den Anmeldeflow initiieren können.  

* Navigieren Sie direkt zur Anmelde-URL für Clear Review, und initiieren Sie den Anmeldeflow.

#### <a name="idp-initiated"></a>IDP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch sollten Sie automatisch bei der Clear Review-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. 

Sie können auch den Microsoft-Bereich „Meine Apps“ verwenden, um die Anwendung in einem beliebigen Modus zu testen. Beim Klicken auf die Kachel „Clear Review“ in „Meine Apps“ geschieht Folgendes: Wenn Sie den SP-Modus konfiguriert haben, werden Sie zum Initiieren des Anmeldeflows zur Anmeldeseite der Anwendung weitergeleitet. Wenn Sie den IDP-Modus konfiguriert haben, sollten Sie automatisch bei der Clear Review-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Clear Review können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-aad)
