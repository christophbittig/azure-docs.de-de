---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Elium | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Elium konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/27/2021
ms.author: jeedes
ms.openlocfilehash: 799496f912f27dc41944df71e68cac409ae97282
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132348899"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-elium"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Elium

In diesem Tutorial erfahren Sie, wie Sie Elium in Azure Active Directory (Azure AD) integrieren. Die Integration von Elium in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf Elium hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Elium anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Elium-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

> [!NOTE]
> Diese Integration kann auch über die Azure AD-Umgebung für die US Government-Cloud verwendet werden. Sie finden diese Anwendung im Azure AD-Katalog für US Government-Cloudanwendungen und konfigurieren sie auf die gleiche Weise wie in der öffentlichen Cloud.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Elium unterstützt **SP- und IDP-initiiertes** einmaliges Anmelden.
* Elium unterstützt die **Just-in-Time**-Benutzerbereitstellung.
* Elium unterstützt die [automatisierte Benutzerbereitstellung](elium-provisioning-tutorial.md).

## <a name="add-elium-from-the-gallery"></a>Hinzufügen von Elium aus dem Katalog

Zum Konfigurieren der Integration von Elium in Azure AD müssen Sie Elium aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Elium** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Elium** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-elium"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Elium

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Elium mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Elium eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Elium die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Elium](#configure-elium-sso)**, um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines Elium-Testbenutzers](#create-elium-test-user)**, um eine Entsprechung von B. Simon in Elium zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Elium** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus, wenn Sie die Anwendung im **IDP-initiierten** Modus konfigurieren möchten:

    a. Geben Sie im Textfeld **Bezeichner** eine URL im folgenden Format ein: `https://<platform-domain>.elium.com/login/saml2/metadata`

    b. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://<platform-domain>.elium.com/login/saml2/acs`

1. Klicken Sie auf **Zusätzliche URLs festlegen**, und führen Sie den folgenden Schritt aus, wenn Sie die Anwendung im **SP-initiierten Modus** konfigurieren möchten:

    Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<platform-domain>.elium.com/login/saml2/login`

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Sie erhalten diese Werte in der **SP-Metadatendatei**, die Sie unter `https://<platform-domain>.elium.com/login/saml2/metadata` herunterladen können und die später in diesem Tutorial erläutert wird.

1. Die Elium-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Daher müssen Sie Ihrer Konfiguration der SAML-Tokenattribute benutzerdefinierte Attributzuordnungen hinzufügen. Der folgende Screenshot zeigt die Liste der Standardattribute.

    ![image](common/default-attributes.png)

1. Darüber hinaus wird von der Elium-Anwendung erwartet, dass in der SAML-Antwort noch einige weitere Attribute zurückgegeben werden (siehe unten). Diese Attribute werden ebenfalls vorab aufgefüllt, Sie können sie jedoch nach Bedarf überprüfen.

    | Name | Quellattribut|
    | ---------------| ----------------|
    | email   |user.mail |
    | first_name| user.givenname |
    | last_name| user.surname|
    | job_title| user.jobtitle|
    | company| user.companyname|

    > [!NOTE]
    > Dies sind die Standardansprüche. **Es ist nur ein E-Mail-Anspruch erforderlich**. Auch für die JIT-Bereitstellung ist nur der E-Mail-Anspruch obligatorisch. Andere benutzerdefinierte Ansprüche können je nach Kundenplattform variieren.

1. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** zu **Verbundmetadaten-XML**, und wählen Sie **Herunterladen** aus, um das Zertifikat herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

1. Kopieren Sie im Abschnitt **Elium einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Elium gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste die Option **Elium** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-elium-sso"></a>Konfigurieren des einmaligen Anmeldens für Elium

1. Wenn Sie die Konfiguration in Elium automatisieren möchten, müssen Sie die **Browsererweiterung „Meine Apps“ für die sichere Anmeldung** installieren, indem Sie auf **Erweiterung installieren** klicken.

    ![Erweiterung „Meine Apps“](common/install-myappssecure-extension.png)

1. Klicken Sie nach dem Hinzufügen der Erweiterung zum Browser auf **Elium einrichten**, um zur Anwendung Elium weitergeleitet zu werden. Geben Sie dort die Administratoranmeldeinformationen ein, um sich bei Elium anzumelden. Die Browsererweiterung konfiguriert die Anwendung automatisch für Sie und automatisiert die Schritte 3 bis 6.

    ![Einrichtungskonfiguration](common/setup-sso.png)

1. Wenn Sie Elium manuell einrichten möchten, melden Sie sich in einem neuen Webbrowserfenster bei der Elium-Unternehmenswebsite als Administrator an, und führen Sie die folgenden Schritte aus:

1. Klicken Sie in der oberen rechten Ecke auf das **Benutzerprofil**, und wählen Sie **Settings** (Einstellungen) aus.

    ![Benutzerprofil für einmaliges Anmelden konfigurieren](./media/elium-tutorial/profile.png)

1. Wählen Sie unter **Advanced** (Erweitert) die Option **Security** (Sicherheit) aus.

    ![„Erweitert“ für einmaliges Anmelden konfigurieren](./media/elium-tutorial/security.png)

1. Scrollen Sie zum Abschnitt **Single Sign-On (SSO)** (Einmaliges Anmelden), und führen Sie die folgenden Schritte aus:

    ![Einmaliges Anmelden konfigurieren](./media/elium-tutorial/configuration.png)

    a. Kopieren Sie den Wert von **Verify that SAML2 authentication works for your account** (Überprüfen, ob die SAML2-Authentifizierung für Ihr Konto funktioniert), und fügen Sie ihn im Azure-Portal im Abschnitt **Grundlegende SAML-Konfiguration** ins Textfeld **Anmelde-URL** ein.

    > [!NOTE]
    > Nach dem Konfigurieren des einmaligen Anmeldens können Sie jederzeit unter der URL `https://<platform_domain>/login/regular/login` auf die Standardseite für die Remoteanmeldung zugreifen. 

    b. Aktivieren Sie das Kontrollkästchen **Enable SAML2 federation** (SAML2-Verbund aktivieren).

    c. Aktivieren Sie das Kontrollkästchen **JIT Provisioning** (JIT-Bereitstellung).

    d. Öffnen Sie die **SP Metadata** (SP-Metadaten), indem Sie auf die Schaltfläche **Download** (Herunterladen) klicken.

    e. Suchen Sie in der Datei **SP Metadata** (SP-Metadaten) nach **entityID**, kopieren Sie den **entityID**-Wert, und fügen Sie ihn im Azure-Portal im Abschnitt **Grundlegende SAML-Konfiguration** ins Textfeld **Bezeichner** ein. 

    ![Konfiguration für einmaliges Anmelden festlegen](./media/elium-tutorial/metadata.png)

    f. Suchen Sie in der Datei **SP Metadata** (SP-Metadaten) nach **AssertionConsumerService**, kopieren Sie den Wert für **Location**, und fügen Sie ihn im Azure-Portal im Abschnitt **Grundlegende SAML-Konfiguration** ins Textfeld **Antwort-URL** ein.

    ![„AssertionConsumerService“ für einmaliges Anmelden konfigurieren](./media/elium-tutorial/service.png)

    g. Öffnen Sie die heruntergeladene Metadatendatei im Azure-Portal im Editor, kopieren Sie den Inhalt, und fügen Sie ihn in das Textfeld **IdP Metadata** ein.

    h. Klicken Sie auf **Speichern**.

### <a name="create-elium-test-user"></a>Erstellen eines Elium-Testbenutzers

In diesem Abschnitt wird in Elium ein Benutzer namens B. Simon erstellt. Elium unterstützt die **Just-in-Time-Bereitstellung**, die standardmäßig aktiviert ist. Für Sie steht in diesem Abschnitt kein Aktionselement zur Verfügung. Falls ein Benutzer nicht bereits in Elium vorhanden ist, wird beim Versuch, auf Elium zuzugreifen, ein neuer Benutzer erstellt.

Außerdem unterstützt Elium die automatische Benutzerbereitstellung. Weitere Informationen zum Konfigurieren der automatischen Benutzerbereitstellung finden Sie [hier](./elium-provisioning-tutorial.md).

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 
 
#### <a name="sp-initiated"></a>SP-initiiert:
 
* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für Elium weitergeleitet, wo Sie den Anmeldeflow initiieren können.  
 
* Rufen Sie direkt die Elium-Anmelde-URL auf, und initiieren Sie den Anmeldeflow.
 
#### <a name="idp-initiated"></a>IDP-initiiert:
 
* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch sollten Sie automatisch bei der Elium-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. 
 
Sie können auch den Microsoft-Bereich „Meine Apps“ verwenden, um die Anwendung in einem beliebigen Modus zu testen. Wenn Sie in „Meine Apps“ auf die Kachel „Elium“ klicken, geschieht Folgendes: Wenn Sie die Anwendung im SP-Modus konfiguriert haben, werden Sie zum Initiieren des Anmeldeflows zur Anmeldeseite der Anwendung weitergeleitet. Wenn Sie die Anwendung im IDP-Modus konfiguriert haben, sollten Sie automatisch bei der Elium-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Elium können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-any-app)
