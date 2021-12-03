---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory-Integration mit LinkedIn Elevate | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und LinkedIn Elevate konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/03/2021
ms.author: jeedes
ms.openlocfilehash: cbf205d925029c4b960bd15775b81c0715af7813
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132326070"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-linkedin-elevate"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory-Integration mit LinkedIn Elevate

In diesem Tutorial erfahren Sie, wie Sie LinkedIn Elevate in Azure Active Directory (Azure AD) integrieren. Die Integration von LinkedIn Elevate in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf LinkedIn Elevate hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei LinkedIn Elevate anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein LinkedIn Elevate-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* LinkedIn Elevate unterstützt **SP- und IDP**-initiiertes einmaliges Anmelden.
* LinkedIn Elevate unterstützt die **Just-in-Time**-Benutzerbereitstellung.
* LinkedIn Elevate unterstützt die [**Automatisierte** Benutzerbereitstellung](linkedinelevate-provisioning-tutorial.md).

## <a name="add-linkedin-elevate-from-the-gallery"></a>Hinzufügen von LinkedIn Elevate aus dem Katalog

Zum Konfigurieren der Integration von LinkedIn Elevate in Azure AD müssen Sie LinkedIn Elevate aus dem Katalog zur Liste der verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **LinkedIn Elevate** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **LinkedIn Elevate** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-linkedin-elevate"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für LinkedIn Elevate

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit LinkedIn Elevate mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in LinkedIn Elevate eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit LinkedIn Elevate die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für LinkedIn Elevate](#configure-linkedin-elevate-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines LinkedIn Elevate-Testbenutzers](#create-linkedin-elevate-test-user)** , um in LinkedIn Elevate eine Entsprechung von B. Simon zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **LinkedIn Elevate** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus, wenn Sie die Anwendung im **IDP-initiierten** Modus konfigurieren möchten:

    a. Geben Sie im Textfeld **Bezeichner** den Wert für **Entitäts-ID** ein, den Sie im Linkedin-Portal kopieren (weiter unten in diesem Tutorial erläutert).

    b. Geben Sie im Textfeld **Antwort-URL** den Wert für **Assertion Consumer Access-URL (ACS)** ein, den Sie im Linkedin-Portal kopieren (weiter unten in diesem Tutorial erläutert).

5. Klicken Sie auf **Zusätzliche URLs festlegen**, und führen Sie den folgenden Schritt aus, wenn Sie die Anwendung im **SP-initiierten Modus** konfigurieren möchten:

    Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://www.linkedin.com/checkpoint/enterprise/login/<ACCOUNT_ID>?application=elevate&applicationInstanceId=<INSTANCE_ID>`

1. Die LinkedIn Elevate-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Daher müssen Sie Ihrer Konfiguration der SAML-Tokenattribute benutzerdefinierte Attributzuordnungen hinzufügen. Der folgende Screenshot zeigt die Liste der Standardattribute. **nameidentifier** ist hier **user.userprincipalname** zugeordnet. Die LinkedIn Elevate-Anwendung erwartet, dass „nameidentifier“ der Wert **user.mail** zugeordnet ist. Sie müssen die Attributzuordnung daher entsprechend ändern, indem Sie auf das Symbol „Bearbeiten“ klicken.

    ![image](common/edit-attribute.png)

1. Darüber hinaus wird von der LinkedIn Elevate-Anwendung erwartet, dass in der SAML-Antwort noch einige weitere Attribute zurückgegeben werden (siehe unten). Diese Attribute werden ebenfalls vorab aufgefüllt, Sie können sie jedoch nach Bedarf überprüfen.

    | Name | Quellattribut|
    | -------| -------------|
    | department | user.department |

1. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** zu **Verbundmetadaten-XML**, und wählen Sie **Herunterladen** aus, um das Zertifikat herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

1. Kopieren Sie im Abschnitt **LinkedIn Elevate einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf LinkedIn Elevate gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **LinkedIn Elevate** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-linkedin-elevate-sso"></a>Konfigurieren des einmaligen Anmeldens für LinkedIn Elevate

1. Melden Sie sich in einem anderen Webbrowserfenster bei Ihrem LinkedIn Elevate-Mandanten als Administrator an.

1. Klicken Sie im **Kontocenter** unter **Einstellungen** auf **Globale Einstellungen**. Wählen Sie außerdem aus der Dropdownliste die Option **Elevate - Elevate AAD Test** aus.

    ![Screenshot der globalen Einstellungen, in denen Sie die Option „Elevate AAD Test“ (AAD-Test erhöhen) auswählen können](./media/linkedinelevate-tutorial/admin.png)

1. Klicken Sie auf **OR Click Here to load and copy individual fields from the form** (ODER klicken Sie hier, um einzelne Felder aus dem Formular zu laden und zu kopieren), und führen Sie die folgenden Schritte aus:

    ![Screenshot: „Single Sign-On“ (Einmaliges Anmelden), wo Sie die beschriebenen Werte eingeben können](./media/linkedinelevate-tutorial/test.png)

    a. Kopieren Sie den Wert im Feld **Entity ID** (Entitäts-ID), und fügen Sie ihn im Azure-Portal im Abschnitt **Grundlegende SAML-Konfiguration** in das Textfeld **Bezeichner** ein.

    b. Kopieren Sie den Wert im Feld **Assertion Consumer Access (ACS) Url** (Assertion Consumer Access-URL), und fügen Sie ihn im Azure-Portal im Abschnitt **Grundlegende SAML-Konfiguration** in das Textfeld **Antwort-URL** ein.

1. Wechseln Sie zum Abschnitt **LinkedIn-Administratoreinstellungen**. Klicken Sie auf „Upload XML file“ (XML-Datei hochladen), um die XML-Datei hochzuladen, die Sie aus dem Azure-Portal heruntergeladen haben.

    ![Screenshot: Abschnitt „Configure the LinkedIn service provider SSO settings“ (SSO-Einstellungen des LinkedIn-Dienstanbieters konfigurieren), in dem Sie eine XML-Datei hochladen können](./media/linkedinelevate-tutorial/metadata.png)

1. Klicken Sie auf **Ein**, um das einmalige Anmelden zu aktivieren. Der Status des einmaligen Anmeldens wechselt von **Nicht verbunden** zu **Verbunden**.

    ![Screenshot: Abschnitt „Single Sign-On“ (Einmaliges Anmelden), in dem Sie „Automatically assign licenses“ (Lizenzen automatisch zuweisen) auswählen können](./media/linkedinelevate-tutorial/connected.png)

### <a name="create-linkedin-elevate-test-user"></a>Erstellen eines LinkedIn Elevate-Testbenutzers

Die LinkedIn Elevate-Anwendung unterstützt die Just-In-Time-Benutzerbereitstellung. Nach der Authentifizierung werden Benutzer in der Anwendung automatisch erstellt. Aktivieren Sie im LinkedIn Elevate-Portal auf der Seite mit den Administratoreinstellungen die Option **Automatically Assign licenses** (Lizenzen automatisch zuweisen), um die Just-In-Time-Bereitstellung zu aktivieren. Dadurch wird dem Benutzer auch eine Lizenz zugewiesen. Außerdem unterstützt LinkedIn Elevate die automatische Benutzerbereitstellung. Weitere Informationen zum Konfigurieren der automatischen Benutzerbereitstellung finden Sie [hier](linkedinelevate-provisioning-tutorial.md).

   ![Erstellen eines Azure AD-Testbenutzers](./media/linkedinelevate-tutorial/switch.png)

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

#### <a name="sp-initiated"></a>SP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für LinkedIn Elevate weitergeleitet, wo Sie den Anmeldeablauf initiieren können.  

* Rufen Sie direkt die LinkedIn Elevate-Anmelde-URL auf und initiieren Sie den Anmeldeablauf.

#### <a name="idp-initiated"></a>IDP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch sollten Sie automatisch bei der LinkedIn Elevate-Instanz angemeldet werden, für die Sie das einmalige Anmelden eingerichtet haben. 

Sie können auch den Microsoft-Bereich „Meine Apps“ verwenden, um die Anwendung in einem beliebigen Modus zu testen. Beim Klicken auf die Kachel „LinkedIn Elevate“ in „Meine Apps“ geschieht Folgendes: Wenn Sie die Anwendung im SP-Modus konfiguriert haben, werden Sie zum Initiieren des Anmeldeablaufs zur Anmeldeseite der Anwendung weitergeleitet. Wenn Sie die Anwendung im IDP-Modus konfiguriert haben, sollten Sie automatisch bei der LinkedIn Elevate-Instanz angemeldet werden, für die Sie das einmalige Anmelden eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von LinkedIn Elevate können Sie die Sitzungssteuerung erzwingen, die Ihre vertraulichen Unternehmensdaten in Echtzeit vor der Exfiltration und Infiltration schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-aad)
