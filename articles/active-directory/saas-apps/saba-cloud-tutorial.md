---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Saba Cloud | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie einmaliges Anmelden zwischen Azure Active Directory und Saba Cloud konfigurieren.
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
ms.openlocfilehash: 0b993380287edd4528a26dd2d9925a2b66c54fd3
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132299727"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-saba-cloud"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Saba Cloud

In diesem Tutorial erfahren Sie, wie Sie Saba Cloud in Azure Active Directory (Azure AD) integrieren. Die Integration von Saba Cloud in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf Saba Cloud hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Saba Cloud anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Saba Cloud-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Saba Cloud unterstützt **SP- und IDP-initiiertes** einmaliges Anmelden.
* Saba Cloud unterstützt die **Just-In-Time**-Bereitstellung von Benutzern.
* Die mobile Saba Cloud-Anwendung kann nun mit Azure AD konfiguriert werden, um SSO zu ermöglichen. In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

## <a name="adding-saba-cloud-from-the-gallery"></a>Hinzufügen von Saba Cloud aus dem Katalog

Zum Konfigurieren der Integration von Saba Cloud in Azure AD müssen Sie Saba Cloud aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Saba Cloud** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Saba Cloud** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-saba-cloud"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Saba Cloud

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Saba Cloud mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Saba Cloud eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Saba Cloud die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Saba Cloud](#configure-saba-cloud-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines Saba Cloud-Testbenutzers](#create-saba-cloud-test-user)** , um eine Entsprechung von B. Simon in Saba Cloud zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert
1. **[Testen des einmaligen Anmeldens für Saba Cloud (mobil)](#test-sso-for-saba-cloud-mobile)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Saba Cloud** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Geben Sie im Abschnitt **Grundlegende SAML-Konfiguration** die Werte in die folgenden Felder ein, wenn Sie die Anwendung im **IDP**-initiierten Modus konfigurieren möchten:

    a. Geben Sie in das Textfeld **Bezeichner** eine URL im folgenden Format ein (diesen Wert ermitteln Sie in Schritt 6 im Abschnitt „Konfigurieren des einmaligen Anmeldens für Saba Cloud“, er hat jedoch in der Regel das Format`<CUSTOMER_NAME>_sp`): `<CUSTOMER_NAME>_sp`.

    b. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein (ENTITY_ID bezieht sich auf den vorherigen Schritt, in der Regel `<CUSTOMER_NAME>_sp`): `https://<CUSTOMER_NAME>.sabacloud.com/Saba/saml/SSO/alias/<ENTITY_ID>`.
    
    > [!NOTE]
    > Wenn Sie die Antwort-URL falsch angeben, müssen Sie sie möglicherweise im Abschnitt **App-Registrierung** von Azure AD anpassen, nicht im Abschnitt **Unternehmensanwendung**. Die Antwort-URL wird nicht immer durch Vornehmen von Änderungen am Abschnitt **Grundlegende SAML-Konfiguration** aktualisiert.

1. Klicken Sie auf **Zusätzliche URLs festlegen**, und führen Sie den folgenden Schritt aus, wenn Sie die Anwendung im **SP-initiierten Modus** konfigurieren möchten:

    a. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<CUSTOMER_NAME>.sabacloud.com`

    b. Geben Sie im Textfeld **Relayzustand** eine URL im folgenden Format ein: `IDP_INIT---SAML_SSO_SITE=<SITE_ID> `. Falls SAML für eine Microsite konfiguriert ist, geben Sie eine URL im folgenden Format ein: `IDP_INIT---SAML_SSO_SITE=<SITE_ID>---SAML_SSO_MICRO_SITE=<MicroSiteId>`.

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Aktualisieren Sie diese Werte mit dem tatsächlichen Bezeichner, der Antwort-URL, der Anmelde-URL und dem Relayzustand. Diese Werte erhalten Sie vom [Supportteam für den Saba Cloud-Client](mailto:support@saba.com). Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.
    > 
    > Weitere Informationen zum Konfigurieren des Relayzustands finden Sie unter [IdP- und SP-initiiertes einmaliges Anmelden für eine Microsite](https://help.sabacloud.com/sabacloud/help-system/topics/help-system-idp-and-sp-initiated-sso-for-a-microsite.html).

1. Passen Sie im Abschnitt **Benutzerattribute und Ansprüche** die eindeutige Benutzer-ID an den Wert an, den Ihre Organisation als primären Benutzernamen für Saba-Benutzer verwenden möchte.

   Dieser Schritt ist nur erforderlich, wenn Sie versuchen, von Benutzername/Kennwort auf einmaliges Anmelden umzustellen. Wenn es sich um eine neue Saba Cloud-Bereitstellung handelt, die noch keine Benutzer enthält, können Sie diesen Schritt überspringen.

1. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** zu **Verbundmetadaten-XML**, und wählen Sie **Herunterladen** aus, um das Zertifikat herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

1. Kopieren Sie im Abschnitt **Saba Cloud einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Saba Cloud gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Saba Cloud** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-saba-cloud-sso"></a>Konfigurieren des einmaligen Anmeldens für Saba Cloud

1. Wenn Sie die Konfiguration in Saba Cloud automatisieren möchten, müssen Sie die **Browsererweiterung „Meine Apps“ für die sichere Anmeldung** installieren, indem Sie auf **Erweiterung installieren** klicken.

    ![Erweiterung „Meine Apps“](common/install-myappssecure-extension.png)

1. Klicken Sie nach dem Hinzufügen der Erweiterung zum Browser auf **Saba Cloud einrichten**, um zur Anwendung „Saba Cloud“ weitergeleitet zu werden. Geben Sie dort die Administratoranmeldeinformationen ein, um sich bei Saba Cloud anzumelden. Die Browsererweiterung konfiguriert die Anwendung automatisch für Sie und automatisiert die Schritte 3 bis 9.

    ![Einrichtungskonfiguration](common/setup-sso.png)

1. Wenn Sie Saba Cloud manuell einrichten möchten, melden Sie sich in einem anderen Webbrowserfenster als Administrator bei der Saba Cloud-Unternehmenswebsite an.

1. Klicken Sie auf das **Menüsymbol** und anschließend auf **Admin** (Administrator), und wählen Sie die Registerkarte **System Admin** (Systemadministrator) aus.

    ![Screenshot: System Admin (Systemadministrator)](./media/saba-cloud-tutorial/system.png)

1. Wählen Sie unter **Configure System** (System konfigurieren) die Option **SAML SSO Setup** (SAML-SSO-Setup) aus, und klicken Sie auf die Schaltfläche **SETUP SAML SSO** (SAML-SSO EINRICHTEN). 

    ![Screenshot: Konfiguration](./media/saba-cloud-tutorial/configure.png)

1. Wählen Sie im Popupfenster im Dropdownmenü die Option **Microsite** aus, und klicken Sie auf **ADD AND CONFIGURE** (HINZUFÜGEN UND KONFIGURIEREN).

    ![Screenshot: Hinzufügen einer Site/Microsite](./media/saba-cloud-tutorial/microsite.png)

1. Klicken Sie im Abschnitt **Configure IDP** (IDP konfigurieren) auf **BROWSE** (DURCHSUCHEN), um die **Verbundmetadaten-XML-Datei** hochzuladen, die Sie aus dem Azure-Portal heruntergeladen haben. Aktivieren Sie das Kontrollkästchen **Site Specific IDP** (Sitespezifischer IDP), und klicken Sie auf **IMPORT** (IMPORTIEREN).

    ![Screenshot: Zertifikatimport](./media/saba-cloud-tutorial/certificate.png) 

1. Kopieren Sie im Abschnitt **Configure SP** (SP konfigurieren) den Wert für **Entity Alias** (Entitätsalias), und fügen Sie diesen Wert im Azure-Portal im Abschnitt **Grundlegende SAML-Konfiguration** in das Textfeld **Bezeichner (Entitäts-ID)** ein. Klicken Sie auf **GENERATE** (GENERIEREN).

    ![Screenshot: Configure SP (SP konfigurieren)](./media/saba-cloud-tutorial/generate-metadata.png) 

1. Überprüfen Sie im Abschnitt **Configure Properties** (Eigenschaften konfigurieren) die ausgefüllten Felder, und klicken Sie auf **SAVE** (SPEICHERN). 

    ![Screenshot: Configure Properties (Eigenschaften konfigurieren)](./media/saba-cloud-tutorial/configure-properties.png) 
    
    Unter Umständen müssen Sie **Max Authentication Age (in seconds)** (Max. Authentifizierungsalter (in Sekunden)) auf **7776000** (90 Tage) festlegen, damit der Wert mit dem jeweiligen standardmäßigen maximalen Alter übereinstimmt, das in Azure AD für eine Anmeldung zulässig ist. Andernfalls kann der Fehler `(109) Login failed. Please contact system administrator.` auftreten.

### <a name="create-saba-cloud-test-user"></a>Erstellen eines Saba Cloud-Testbenutzers

In diesem Abschnitt wird in Saba Cloud ein Benutzer namens Britta Simon erstellt. Saba Cloud unterstützt die Just-In-Time-Benutzerbereitstellung (standardmäßig aktiviert). Für Sie steht in diesem Abschnitt kein Aktionselement zur Verfügung. Ist ein Benutzer noch nicht in Saba Cloud vorhanden, wird nach der Authentifizierung ein neuer Benutzer erstellt.

> [!NOTE]
> Informationen zum Aktivieren der SAML-Just-In-Time-Benutzerbereitstellung mit Saba Cloud finden Sie in [dieser Dokumentation](https://help.sabacloud.com/sabacloud/help-system/topics/help-system-user-provisioning-with-saml.html).

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

#### <a name="sp-initiated"></a>SP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für Saba Cloud weitergeleitet, wo Sie den Anmeldeflow initiieren können.  

* Rufen Sie direkt die Saba Cloud-Anmelde-URL auf, und initiieren Sie den Anmeldeflow.

#### <a name="idp-initiated"></a>IDP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch sollten Sie automatisch bei der Saba Cloud-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. 

Sie können auch den Microsoft-Bereich „Meine Apps“ verwenden, um die Anwendung in einem beliebigen Modus zu testen. Wenn Sie im Zugriffsbereich auf die Kachel „Saba Cloud“ klicken, geschieht Folgendes: Wenn Sie den SP-Modus konfiguriert haben, werden Sie zum Initiieren des Anmeldeflows zur Anmeldeseite der Anwendung weitergeleitet, und wenn Sie den IDP-Modus konfiguriert haben, sollten Sie automatisch bei der Saba Cloud-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

> [!NOTE]
> Ist die Anmelde-URL in Azure AD nicht aufgefüllt, wird die Anwendung als Anwendung mit IDP-initiiertem Modus behandelt. Ist die Anmelde-URL aufgefüllt, leitet Azure AD den Benutzer für den vom Dienstanbieter initiierten Flow immer an die Saba Cloud-Anwendung weiter.

## <a name="test-sso-for-saba-cloud-mobile"></a>Testen des einmaligen Anmeldens für Saba Cloud (mobil)

1. Öffnen Sie die mobile Saba Cloud-Anwendung, geben Sie unter **Site Name** (Sitename) den Namen in das Textfeld ein, und klicken Sie auf **Enter** (Eingabe).

    ![Screenshot: Site name (Sitename)](./media/saba-cloud-tutorial/site-name.png)

1. Geben Sie Ihre **E-Mail-Adresse** ein, und klicken Sie auf **Weiter**.

    ![Screenshot: E-Mail-Adresse](./media/saba-cloud-tutorial/email-address.png)

1. Nach erfolgreicher Anmeldung wird die Anwendungsseite angezeigt.

    ![Screenshot: Erfolgreiche Anmeldung](./media/saba-cloud-tutorial/dashboard.png)

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Saba Cloud können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-any-app)
