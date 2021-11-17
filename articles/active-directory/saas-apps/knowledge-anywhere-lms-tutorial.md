---
title: 'Tutorial: Azure Active Directory-Integration mit Knowledge Anywhere LMS | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Knowledge Anywhere LMS konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/09/2021
ms.author: jeedes
ms.openlocfilehash: ecb309362b1c7171e7f3a2782de75f7ebd88b310
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132313639"
---
# <a name="tutorial-integrate-knowledge-anywhere-lms-with-azure-active-directory"></a>Tutorial: Integrieren von Knowledge Anywhere LMS mit Azure Active Directory

In diesem Tutorial erfahren Sie, wie Sie Knowledge Anywhere LMS in Azure Active Directory (Azure AD) integrieren. Die Integration von Knowledge Anywhere LMS in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf Knowledge Anywhere LMS hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihrem Azure AD-Konto automatisch bei Knowledge Anywhere LMS anzumelden (einmaliges Anmelden; Single Sign-On, SSO).
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Knowledge Anywhere LMS-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung. 
* Knowledge Anywhere LMS unterstützt **SP-initiiertes** einmaliges Anmelden.
* Knowledge Anywhere LMS unterstützt die **Just-In-Time**-Benutzerbereitstellung.

## <a name="add-knowledge-anywhere-lms-from-the-gallery"></a>Hinzufügen von Knowledge Anywhere LMS über den Katalog

Um die Integration von Knowledge Anywhere LMS in Azure AD zu konfigurieren, müssen Sie Knowledge Anywhere LMS über den Katalog Ihrer Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Knowledge Anywhere LMS** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Knowledge Anywhere LMS** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-knowledge-anywhere-lms"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Knowledge Anywhere LMS

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Knowledge Anywhere LMS mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Knowledge Anywhere LMS eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Knowledge Anywhere LMS die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Knowledge Anywhere LMS](#configure-knowledge-anywhere-lms-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
    1. **[Erstellen eines Knowledge Anywhere LMS-Testbenutzers](#create-knowledge-anywhere-lms-test-user)** , um in Knowledge Anywhere LMS eine Entsprechung von B. Simon zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist.
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Knowledge Anywhere LMS** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus, wenn Sie die Anwendung im **IDP-initiierten** Modus konfigurieren möchten:

    1. Geben Sie im Textfeld **Bezeichner** eine URL im folgenden Format ein: `https://<CLIENT_NAME>.knowledgeanywhere.com/`

    1. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://<CLIENT_NAME>.knowledgeanywhere.com/SSO/SAML/Response.aspx?<IDP_NAME>`

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch den tatsächlichen Bezeichner und die tatsächliche Antwort-URL. Darauf wird später im Tutorial eingegangen.

1. Klicken Sie auf **Zusätzliche URLs festlegen**, und führen Sie den folgenden Schritt aus, wenn Sie die Anwendung im **SP-initiierten Modus** konfigurieren möchten:

    Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<CLIENTNAME>.knowledgeanywhere.com/`

    > [!NOTE]
    > Der Wert der Anmelde-URL entspricht nicht dem tatsächlichen Wert. Ersetzen Sie diesen Wert durch die tatsächliche Anmelde-URL. Wenden Sie sich an das [Kundensupportteam von Knowledge Anywhere LMS](https://knowany.zendesk.com/hc/en-us/articles/360000469034-SAML-2-0-Single-Sign-On-SSO-Set-Up-Guide), um diesen Wert zu erhalten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

1. Suchen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** den Eintrag **Zertifikat (Base64)** . Klicken Sie auf **Herunterladen**, um das Zertifikat herunterzuladen, und speichern Sie es auf Ihrem Computer.

   ![Downloadlink für das Zertifikat](common/certificatebase64.png)

1. Kopieren Sie im Abschnitt **Knowledge Anywhere LMS einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

   ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

In diesem Abschnitt erstellen Sie im Azure-Portal einen Testbenutzer mit dem Namen B. Simon.

1. Wählen Sie im linken Bereich des Microsoft Azure-Portals **Azure Active Directory** > **Benutzer** > **Alle Benutzer** aus.
1. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.
1. Führen Sie unter den Eigenschaften für **Benutzer** die folgenden Schritte aus:
   1. Geben Sie im Feld **Name** die Zeichenfolge `B. Simon` ein.  
   1. Geben Sie im Feld **Benutzername** die Zeichenfolge username@companydomain.extension ein. Beispiel: `BrittaSimon@contoso.com`.
   1. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**, und notieren Sie sich den Wert aus dem Feld **Kennwort**.
   1. Klicken Sie auf **Erstellen**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie Zugriff auf Knowledge Anywhere LMS gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste den Eintrag **Knowledge Anywhere LMS** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-knowledge-anywhere-lms-sso"></a>Konfigurieren des einmaligen Anmeldens für Knowledge Anywhere LMS

1. Wenn Sie die Konfiguration in Knowledge Anywhere LMS automatisieren möchten, müssen Sie die **Browsererweiterung „Meine Apps“ für die sichere Anmeldung** installieren, indem Sie auf **Erweiterung installieren** klicken.

    ![Erweiterung „Meine Apps“](common/install-myappssecure-extension.png)

2. Klicken Sie nach dem Hinzufügen der Erweiterung zum Browser auf **Knowledge Anywhere LMS einrichten**, um zur Anwendung Knowledge Anywhere LMS weitergeleitet zu werden. Geben Sie dort die Administratoranmeldeinformationen ein, um sich bei Knowledge Anywhere LMS anzumelden. Die Browsererweiterung konfiguriert die Anwendung automatisch für Sie und automatisiert die Schritte 3 bis 7.

    ![Einrichtungskonfiguration](common/setup-sso.png)

3. Wenn Sie Knowledge Anywhere LMS manuell einrichten möchten, öffnen Sie ein neues Webbrowserfenster, melden Sie sich bei der Knowledge Anywhere LMS-Unternehmenswebsite als Administrator an, und führen Sie die folgenden Schritte aus:

4. Wählen Sie die Registerkarte **Site** aus.

    ![Screenshot: Registerkarte „Site“](./media/knowledge-anywhere-lms-tutorial/site.png)

5. Wählen Sie die Registerkarte **SAML Settings** (SAML-Einstellungen) aus.

    ![Screenshot: Knowledge Anywhere-Seite mit Auswahl von „SAML Settings“ (SAML-Einstellungen)](./media/knowledge-anywhere-lms-tutorial/settings.png)

6. Klicken Sie auf **Add New** (Neu hinzufügen).

    ![Screenshot: Schaltfläche „Add New“ (Neu hinzufügen) in den Einstellungen für den Dienstanbieter](./media/knowledge-anywhere-lms-tutorial/add-settings.png)

7. Führen Sie auf der Seite **Add/Update SAML Settings** (SAML-Einstellungen hinzufügen/aktualisieren) die folgenden Schritte aus:

    ![Screenshot: Seite „Add/Update SAML Settings“ (SAML-Einstellungen hinzufügen/aktualisieren), auf der Sie die hier beschriebenen Änderungen vornehmen können](./media/knowledge-anywhere-lms-tutorial/update-settings.png)

    a. Geben Sie den IDP-Namen für Ihre Organisation ein. Beispiel: `Azure`.

    b. Fügen Sie in das Textfeld **IDP Entity ID** (IDP-Entitäts-ID) den Wert für den **Azure AD-Bezeichner** ein, den Sie aus dem Azure-Portal kopiert haben.

    c. Fügen Sie in das Textfeld **IDP URL** (IDP-URL) die **Anmelde-URL** ein, die Sie aus dem Azure-Portal kopiert haben.

    d. Öffnen Sie das Zertifikat, das Sie aus dem Azure-Portal heruntergeladen haben, im Editor, kopieren Sie den Inhalt des Zertifikats, und fügen Sie ihn in das Textfeld **Certificate** (Zertifikat) ein.

    e. Fügen Sie in das Textfeld **Logout URL** (Abmelde-URL) die **Abmelde-URL** ein, die Sie aus dem Azure-Portal kopiert haben.

    f. Wählen Sie in der Dropdownliste für die **Domäne** die Option **Main Site** (Hauptwebsite) aus.

    g. Kopieren Sie den Wert im Feld **SP Entity ID** (SP-Entitäts-ID), und fügen Sie ihn im Azure-Portal im Abschnitt **Grundlegende SAML-Konfiguration** in das Textfeld **Bezeichner** ein.

    h. Kopieren Sie den Wert für **SP Response (ACS) URL** (URL der SP-Antwort (ACS)), und fügen Sie ihn im Azure-Portal im Abschnitt **Grundlegende SAML-Konfiguration** in das Textfeld **Antwort-URL** ein.

    i. Klicken Sie auf **Speichern**.

### <a name="create-knowledge-anywhere-lms-test-user"></a>Erstellen eines Knowledge Anywhere LMS-Testbenutzers

In diesem Abschnitt wird in Knowledge Anywhere LMS ein Benutzer namens B. Simon erstellt. Knowledge Anywhere LMS unterstützt die Just-In-Time-Benutzerbereitstellung (standardmäßig aktiviert). Für Sie steht in diesem Abschnitt kein Aktionselement zur Verfügung. Ist ein Benutzer noch nicht in Knowledge Anywhere LMS vorhanden, wird nach der Authentifizierung ein neuer Benutzer erstellt.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für Knowledge Anywhere LMS weitergeleitet, wo Sie den Anmeldeflow initiieren können. 

* Rufen Sie die Anmelde-URL für Knowledge Anywhere LMS direkt auf, und initiieren Sie den Anmeldeflow.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie in „Meine Apps“ auf die Kachel „Knowledge Anywhere LMS“ klicken, werden Sie zur Anmelde-URL von Knowledge Anywhere LMS umgeleitet. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Knowledge Anywhere LMS können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Ex- und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-aad)
