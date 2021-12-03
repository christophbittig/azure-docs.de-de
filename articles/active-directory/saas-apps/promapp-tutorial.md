---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Nintex Promapp | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie einmaliges Anmelden zwischen Azure Active Directory und Nintex Promapp konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/31/2021
ms.author: jeedes
ms.openlocfilehash: 02eecf37fa94570e26bb795cd3882f2bec9b0aa1
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132306755"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-nintex-promapp"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Nintex Promapp

In diesem Tutorial erfahren Sie, wie Sie Nintex Promapp in Azure Active Directory (Azure AD) integrieren. Die Integration von Nintex Promapp in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf Nintex Promapp hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Nintex Promapp anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Nintex Promapp-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Nintex Promapp unterstützt **SP- und IDP-initiiertes** einmaliges Anmelden.
* Nintex Promapp unterstützt die **Just-in-Time**-Benutzerbereitstellung.

* Nintex Promapp unterstützt [automatisierte Benutzerbereitstellung](promapp-provisioning-tutorial.md).

> [!NOTE]
> Der Bezeichner dieser Anwendung ist ein fester Zeichenfolgenwert, daher kann in einem Mandanten nur eine Instanz konfiguriert werden.

## <a name="add-nintex-promapp-from-the-gallery"></a>Hinzufügen von Nintex Promapp aus dem Katalog

Zum Konfigurieren der Integration von Nintex Promapp in Azure AD müssen Sie Nintex Promapp aus dem Katalog der Liste der verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Nintex Promapp** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Nintex Promapp** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-nintex-promapp"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Nintex Promapp

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Nintex Promapp mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Nintex Promapp eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Nintex Promapp die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Nintex Promapp](#configure-nintex-promapp-sso)**, um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines Nintex Promapp-Testbenutzers](#create-nintex-promapp-test-user)**, um ein Pendant von B. Simon in Nintex Promapp zu erhalten, das mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Nintex Promapp** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus, wenn Sie die Anwendung im **IDP-initiierten** Modus konfigurieren möchten:

    1. Geben Sie im Feld **Bezeichner** eine der folgenden URLs ein:
        
        | Bezeichner-URL |
        |-----|
        |`https://go.promapp.com/TENANTNAME/`|
        |`https://au.promapp.com/TENANTNAME/`|
        |`https://us.promapp.com/TENANTNAME/`|
        |`https://eu.promapp.com/TENANTNAME/`|
        |`https://ca.promapp.com/TENANTNAME/`|

       > [!NOTE]
       > Die Azure AD-Integration mit Nintex Promapp ist derzeit nur für die vom Dienst initiierte Authentifizierung konfiguriert. (Das bedeutet, dass das Aufrufen einer Nintex Promapp-URL den Authentifizierungsprozess initiiert.) Das Feld **Antwort-URL** ist jedoch ein Pflichtfeld.

    1. Geben Sie im Feld **Antwort-URL** eine URL im folgenden Format ein: `https://<DOMAIN_NAME>.promapp.com/TENANTNAME/saml/authenticate.aspx`

1. Klicken Sie auf **Zusätzliche URLs festlegen**, und führen Sie den folgenden Schritt aus, wenn Sie die Anwendung im **SP-initiierten Modus** konfigurieren möchten:

    Geben Sie im Feld **URL für Anmeldung** eine URL im folgenden Format ein: `https://<DOMAIN_NAME>.promapp.com/TENANTNAME/saml/authenticate`

    > [!NOTE]
    > Diese Werte sind Platzhalter. Sie müssen die tatsächlichen Werte für Bezeichner, Antwort-URL und Anmelde-URL verwenden. Die Werte erhalten Sie vom [Nintex Promapp-Supportteam](https://www.promapp.com/about-us/contact-us/). Sie können sich auch die Muster im Azure-Portal im Dialogfeld **Grundlegende SAML-Konfiguration** ansehen.

1. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** zum Eintrag **Zertifikat (Base64)** . Wählen Sie **Herunterladen** aus, um das Zertifikat herunterzuladen, und speichern Sie es auf Ihrem Computer.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

1. Kopieren Sie im Abschnitt **Nintex Promapp einrichten** die entsprechenden URLs basierend auf Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Nintex Promapp gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Nintex Promapp** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-nintex-promapp-sso"></a>Konfigurieren des einmaligen Anmeldens für Nintex Promapp

1. Melden Sie sich bei Ihrer Nintex Promapp-Unternehmenswebsite als Administrator an.

2. Wählen Sie im oberen Fensterbereich **Admin** (Administrator) aus:

    ![Auswählen von „Admin“ (Administrator)](./media/promapp-tutorial/admin.png)

3. Wählen Sie **Configure** (Konfigurieren) aus:

    ![Auswählen von „Configure“ (Konfigurieren)](./media/promapp-tutorial/configuration.png)

4. Führen Sie im Dialogfeld **Security** (Sicherheit) die folgenden Schritte aus:

    ![Dialogfeld „Security“ (Sicherheit)](./media/promapp-tutorial/certificate.png)

    1. Fügen Sie im Feld **SSO-Login URL** (SSO-Anmelde-URL) die **Anmelde-URL** ein, die Sie aus dem Azure-Portal kopiert haben.

    1. Wählen Sie in der Liste **SSO - Single Sign-on Mode** (SSO: SSO-Modus) die Option **Optional** aus. Wählen Sie **Speichern** aus.

       > [!NOTE]
       > Der optionale Modus dient nur zu Testzwecken. Wenn Sie mit der Konfiguration zufrieden sind, wählen Sie in der Liste **SSO - Single Sign-on Mode** (SSO: SSO-Modus) die Option **Required** (Erforderlich) aus, um für alle Benutzer die Authentifizierung über Azure AD zu erzwingen.

    1. Öffnen Sie im Editor das Zertifikat, das Sie im vorherigen Abschnitt heruntergeladen haben. Kopieren Sie den Inhalt des Zertifikats ohne die erste (**-----BEGIN CERTIFICATE-----**) und die letzte Zeile (**-----END CERTIFICATE-----**). Fügen Sie den Zertifikatsinhalt ins Feld **SSO - x.509 Certificate** (SSO: x.509-Zertifikat) ein, und wählen Sie dann **Save** (Speichern) aus.

### <a name="create-nintex-promapp-test-user"></a>Erstellen eines Nintex Promapp-Testbenutzers

In diesem Abschnitt wird in Nintex Promapp ein Benutzer namens B. Simon erstellt. Nintex Promapp unterstützt die Just-In-Time-Benutzerbereitstellung (standardmäßig aktiviert). Für Sie steht in diesem Abschnitt kein Aktionselement zur Verfügung. Ist ein Benutzer noch nicht in Nintex Promapp vorhanden, wird nach der Authentifizierung ein neuer Benutzer erstellt.

Außerdem unterstützt Nintex Promapp automatische Benutzerbereitstellung. Weitere Informationen zum Konfigurieren der automatischen Benutzerbereitstellung finden Sie [hier](./promapp-provisioning-tutorial.md).

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

#### <a name="sp-initiated"></a>SP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für Nintex Promapp weitergeleitet, wo Sie den Anmeldeflow initiieren können.  

* Rufen Sie die Anmelde-URL für Nintex Promapp direkt auf, und initiieren Sie den Anmeldeflow.

#### <a name="idp-initiated"></a>IDP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch sollten Sie automatisch bei der Nintex Promapp-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. 

Sie können auch den Microsoft-Bereich „Meine Apps“ verwenden, um die Anwendung in einem beliebigen Modus zu testen. Beim Klicken auf die Kachel „Nintex Promapp“ in „Meine Apps“ geschieht Folgendes: Wenn Sie die Anwendung im SP-Modus konfiguriert haben, werden Sie zum Initiieren des Anmeldeflows zur Anmeldeseite der Anwendung weitergeleitet. Wenn Sie die Anwendung im IDP-Modus konfiguriert haben, sollten Sie automatisch bei der Nintex Promapp-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Nintex Promapp können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Ex- und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-aad)
