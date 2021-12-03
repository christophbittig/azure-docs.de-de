---
title: 'Tutorial: Azure AD-SSO-Integration in LinkedIn Learning'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und LinkedIn Learning konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/01/2021
ms.author: jeedes
ms.openlocfilehash: cb4c4f52ad4578cd1d4718d0b709132a846119dd
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132290981"
---
# <a name="tutorial-azure-ad-sso-integration-with-linkedin-learning"></a>Tutorial: Azure AD-SSO-Integration in LinkedIn Learning

In diesem Tutorial erfahren Sie, wie Sie LinkedIn Learning in Azure Active Directory (Azure AD) integrieren. Die Integration von LinkedIn Learning in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf LinkedIn Learning hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei LinkedIn Learning anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein LinkedIn Learning-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* LinkedIn Learning unterstützt durch **SP und IDP** initiiertes einmaliges Anmelden.
* LinkedIn Learning unterstützt die **Just-in-Time**-Benutzerbereitstellung.
* LinkedIn Learnung unterstützt die [Automatisierte Benutzerbereitstellung](linkedin-learning-provisioning-tutorial.md).

## <a name="add-linkedin-learning-from-the-gallery"></a>Hinzufügen von LinkedIn Learning aus dem Katalog

Zum Konfigurieren der Integration von LinkedIn Learning in Azure AD müssen Sie LinkedIn Learning aus dem Katalog zur Liste der verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **LinkedIn Learning** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **LinkedIn Learning** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-linkedin-learning"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für LinkedIn Learning

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit LinkedIn Learning mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in LinkedIn Learning eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit LinkedIn Learning die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für LinkedIn Learning](#configure-linkedin-learning-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Zuweisen von Lizenzen](#assign-licenses)** , um in LinkedIn Learning eine Entsprechung von B. Simon zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **LinkedIn Learning** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus, wenn Sie die Anwendung im **IDP-initiierten** Modus konfigurieren möchten:

    a. Geben Sie im Textfeld **Bezeichner** die **Entitäts-ID** ein, die Sie aus dem LinkedIn-Portal kopiert haben. 

    b. Geben Sie in das Textfeld **Antwort-URL** den Wert für **Assertion Consumer Service (ACS)-URL** ein, den Sie aus dem LinkedIn-Portal kopiert haben.

    c. Wenn Sie die Anwendung im **SP-initiierten** Modus konfigurieren möchten, klicken Sie im Abschnitt **Grundlegende SAML-Konfiguration**, in dem Sie Ihre Anmelde-URL angeben, auf **Zusätzliche URLs festlegen**. Kopieren Sie die **Assertionsverbraucherdienst-URL (ACS)** , und ersetzen Sie „/saml/“ durch „/login/“, um Ihre Anmelde-URL zu erstellen. Danach sollte die Anmelde-URL folgendes Muster aufweisen:

    `https://www.linkedin.com/checkpoint/enterprise/login/<AccountId>?application=learning&applicationInstanceId=<InstanceId>`

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Diese Werte müssen durch die tatsächlichen Werte für Bezeichner, Antwort-URL und Anmelde-URL ersetzt werden. Entsprechende Informationen finden Sie im Abschnitt **Konfigurieren des einmaligen Anmeldens für LinkedIn Learning** dieses Tutorials.

1. Die LinkedIn Learning-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Daher müssen Sie Ihrer Konfiguration der SAML-Tokenattribute benutzerdefinierte Attributzuordnungen hinzufügen. Der folgende Screenshot zeigt die Liste der Standardattribute. **nameidentifier** ist hier **user.userprincipalname** zugeordnet. Die LinkedIn Learning-Anwendung erwartet, dass **nameidentifier** der Wert **user.mail** zugeordnet ist. Sie müssen die Attributzuordnung daher entsprechend ändern, indem Sie auf das Symbol **Bearbeiten** klicken.

    ![image](common/edit-attribute.png)

1. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** zu **Verbundmetadaten-XML**, und wählen Sie **Herunterladen** aus, um das Zertifikat herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

1. Kopieren Sie im Abschnitt **LinkedIn Learning einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf LinkedIn Learning gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **LinkedIn Learning** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-linkedin-learning-sso"></a>Konfigurieren des einmaligen Anmeldens für LinkedIn Learning

1. Melden Sie sich bei Ihrer LinkedIn Learning-Unternehmenswebsite als Administrator an.

1. Wählen Sie **Wechseln Sie zu Admin** > **Ich** > **Authentifizieren**. 

    ![Konto](./media/linkedinlearning-tutorial/welcome-back-authenticate.png "Konto") 

1. Wählen Sie unter **Authentifizieren** die Option **Einmaliges Anmelden konfigurieren** aus, und klicken Sie auf **Neues einmaliges Anmelden hinzufügen**.    

    ![Konfigurieren von einmaligem Anmelden](./media/linkedinlearning-tutorial/admin.png "Einmaliges Anmelden konfigurieren")

1. Wählen Sie in der Dropdownliste **Neues einmaliges Anmelden hinzufügen** die Option **SAML** aus.

    ![SAML Authentication (SAML-Authentifizierung)](./media/linkedinlearning-tutorial/new-method.png "SAML-Authentifizierung")

1. Geben Sie in der Registerkarte **Grundlagen** den **SAML-Verbindungsnamen** ein und klicken Sie auf **Weiter**.

    ![SSO-Verbindung](./media/linkedinlearning-tutorial/users.png "SSO-Verbindung")

1. Navigieren Sie zur Registerkarte **Identitätsanbietereinstellungen**, klicken Sie auf **Datei herunterladen**, um die Metadatendatei herunterzuladen und auf Ihrem Computer zu speichern, und klicken Sie auf **Weiter**.

    ![Identitätsanbietereinstellungen](./media/linkedinlearning-tutorial/download-file.png "Identitätsanbietereinstellungen")

    > [!NOTE]    
    > Möglicherweise können Sie diese Datei nicht in Ihren Identitätsanbieter importieren. Okta verfügt beispielsweise nicht über diese Funktionalität. Wenn dieser Fall Ihren Konfigurationsanforderungen entspricht, fahren Sie mit Arbeiten mit einzelnen Feldern fort.

1. Klicken Sie in der Registerkarte **Identitätsanbietereinstellungen** auf **Informationen aus Feldern laden und kopieren**, um die erforderlichen Felder zu kopieren und im Azure-Portal in den Abschnitt **Grundlegende SAML-Konfiguration** einzufügen, und klicken Sie auf **Weiter**.

    ![Einstellungen](./media/linkedinlearning-tutorial/fields.png "Einstellungen")

1. Klicken Sie im Reiter **SSO Einstellungen** auf **XML-Datei hochladen**, um die **Verbundmetadaten-XML-Datei** hochzuladen, die Sie aus dem Azure-Portal heruntergeladen haben.

    ![Zertifikatsdatei](./media/linkedinlearning-tutorial/upload-file.png "Zertifikatsdatei")

1. Füllen Sie die erforderlichen Felder manuell aus, die Sie in der Registerkarte **SSO-Einstellungen** aus dem Azure-Portal kopiert haben.

    ![Eingeben von Werten](./media/linkedinlearning-tutorial/certificate.png "Eingeben von Werten")

1. Wählen Sie unter **SSO-Einstellungen** Ihre SSO-Optionen gemäß Ihren Anforderungen aus und klicken Sie auf **Speichern**.

    ![SSO-Einstellungen](./media/linkedinlearning-tutorial/options.png "SSO-Einstellungen")

#### <a name="enabling-single-sign-on"></a>Aktivieren des einmaligen Anmeldens (SSO)

Aktivieren Sie nach Abschluss Ihrer Konfiguration das einmalige Anmelden, indem Sie in der Dropdownliste für den SSO-Status die Option **Aktiv** auswählen.

  ![Aktivieren des einmaligen Anmeldens](./media/linkedinlearning-tutorial/configuration.png "Aktivieren des einmaligen Anmeldens (SSO)")

### <a name="assign-licenses"></a>Zuweisen von Lizenzen

Nachdem Sie das einmalige Anmelden aktiviert haben, können Sie Ihren Mitarbeitern automatisch Lizenzen zuweisen, indem Sie die Option **Lizenzen automatisch bereitstellen** auf **Ein** umschalten und auf **Speichern** klicken. Wenn Sie diese Option aktivieren, wird Benutzern automatisch eine Lizenz gewährt, wenn sie zum ersten Mal authentifiziert werden.

   ![Lizenzen zuweisen](./media/linkedinlearning-tutorial/license.png "Zuweisen von Lizenzen")

> [!NOTE]   
> Wenn Sie diese Option nicht aktivieren, muss ein Administrator die Benutzer manuell in der Registerkarte Personen hinzufügen. LinkedIn Learning identifiziert Benutzer anhand ihrer E-Mail-Adresse.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

#### <a name="sp-initiated"></a>SP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für LinkedIn Learning weitergeleitet, wo Sie den Anmeldeflow initiieren können.  

* Rufen Sie direkt die LinkedIn Learning-Anmelde-URL auf, und initiieren Sie den Anmeldeflow.

#### <a name="idp-initiated"></a>IDP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch sollten Sie automatisch bei der LinkedIn Learning-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. 

Sie können auch den Microsoft-Bereich „Meine Apps“ verwenden, um die Anwendung in einem beliebigen Modus zu testen. Beim Klicken auf die Kachel „LinkedIn Learning“ in „Meine Apps“ geschieht Folgendes: Wenn Sie die Anwendung im SP-Modus konfiguriert haben, werden Sie zum Initiieren des Anmeldeflows zur Anmeldeseite der Anwendung weitergeleitet. Wenn Sie die Anwendung im IDP-Modus konfiguriert haben, sollten Sie automatisch bei der LinkedIn Learning-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von LinkedIn Learning können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-aad)
