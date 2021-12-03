---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit InVision | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie einmaliges Anmelden zwischen Azure Active Directory und InVision konfigurieren.
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
ms.openlocfilehash: df92ea58544a35c157ba91ad95412d557eabdb05
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132313929"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-invision"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit InVision

In diesem Tutorial erfahren Sie, wie Sie InVision in Azure Active Directory (Azure AD) integrieren. Die Integration von InVision in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf InVision hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei InVision anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein InVision-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* InVision unterstützt **SP- und IDP-initiiertes** einmaliges Anmelden.
* InVision unterstützt die [automatisierte Benutzerbereitstellung](invision-provisioning-tutorial.md).

## <a name="adding-invision-from-the-gallery"></a>Hinzufügen von InVision aus dem Katalog

Zum Konfigurieren der Integration von InVision in Azure AD müssen Sie InVision aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **InVision** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **InVision** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-invision"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für InVision

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit InVision mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in InVision eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit InVision die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für InVision](#configure-invision-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines InVision-Testbenutzers](#create-invision-test-user)** , um in InVision eine Entsprechung von B. Simon zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **InVision** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Geben Sie im Abschnitt **Grundlegende SAML-Konfiguration** die Werte in die folgenden Felder ein, wenn Sie die Anwendung im **IDP**-initiierten Modus konfigurieren möchten:

    a. Geben Sie im Textfeld **Bezeichner** eine URL im folgenden Format ein: `https://<SUBDOMAIN>.invisionapp.com`

    b. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://<SUBDOMAIN>.invisionapp.com//sso/auth`

1. Klicken Sie auf **Zusätzliche URLs festlegen**, und führen Sie den folgenden Schritt aus, wenn Sie die Anwendung im **SP-initiierten Modus** konfigurieren möchten:

    Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<SUBDOMAIN>.invisionapp.com`

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Sie müssen diese Werte mit dem tatsächlichen Bezeichner, der Antwort-URL und der Anmelde-URL aktualisieren. Diese Werte erhalten Sie vom [Supportteam für den InVision-Client](mailto:support@invisionapp.com). Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

1. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** zum Eintrag **Zertifikat (Base64)** . Wählen Sie **Herunterladen** aus, um das Zertifikat herunterzuladen, und speichern Sie es auf Ihrem Computer.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

1. Kopieren Sie im Abschnitt **InVision einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf InVision gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **InVision** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.

1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-invision-sso"></a>Konfigurieren des einmaligen Anmeldens für InVision

1. Wenn Sie die Konfiguration in InVision automatisieren möchten, müssen Sie die **Browsererweiterung „Meine Apps“ für die sichere Anmeldung** installieren, indem Sie auf **Erweiterung installieren** klicken.

    ![Erweiterung „Meine Apps“](common/install-myappssecure-extension.png)

2. Klicken Sie nach dem Hinzufügen der Erweiterung zum Browser auf **InVision einrichten**, um zur Anwendung InVision weitergeleitet zu werden. Geben Sie dort die Administratoranmeldeinformationen ein, um sich bei InVision anzumelden. Die Browsererweiterung konfiguriert die Anwendung automatisch für Sie und automatisiert die Schritte 3 bis 6.

    ![Einrichtungskonfiguration](common/setup-sso.png)

3. Wenn Sie InVision manuell einrichten möchten, melden Sie sich in einem anderen Webbrowserfenster als Administrator bei der InVision-Unternehmenswebsite an.

1. Klicken Sie auf **Team**, und wählen Sie **Settings** (Einstellungen) aus.

    ![Screenshot: Registerkarte „Team“, auf der „Einstellungen“ ausgewählt ist](./media/invision-tutorial/config1.png)

1. Scrollen Sie nach unten zu **Single sign-on** (Einmaliges Anmelden), und klicken Sie dann auf **Change** (Ändern).

    ![Screenshot: Schaltfläche „Ändern“ für einmaliges Anmelden](./media/invision-tutorial/config3.png)

1. Führen Sie auf der Seite **Single sign-on** (Einmaliges Anmelden) die folgenden Schritte aus:

    ![Screenshot: Seite „Einmaliges Anmelden“, auf der Sie in diesem Schritt die Werte eingeben](./media/invision-tutorial/config4.png)

    a. Ändern Sie **Require SSO for every member of < account name >** (SSO für alle Mitglieder von < Kontoname > erforderlich machen) in **On** (Ein).

    b. Geben Sie im Textfeld **name** (Name) den Namen ein, etwa `azureadsso`.

    c. Geben Sie den Wert für die Anmelde-URL in das Textfeld **Sign-in URL** (Anmelde-URL) ein.

    d. Fügen Sie im Textfeld **Sign-out URL** (Abmelde-URL) den Wert der **Abmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    e. Öffnen Sie die heruntergeladene **Zertifikatdatei (Base64)** im Editor, kopieren Sie den Inhalt, und fügen Sie ihn in das Textfeld „**SAML Certificate“ (SAML-Zertifikat)** ein.

    f. Verwenden Sie `urn:oasis:names:tc:SAML:1.1:nameid-format:Unspecified` im Textfeld **Name ID Format** **(Format der Namens-ID)** .

    g. Wählen Sie im Dropdownmenü **HASH Algorithm** (Hashalgorithmus) die Option **SHA-256** aus.

    h. Geben Sie unter **SSO Button Label** (Bezeichnung für Schaltfläche „SSO“) einen entsprechenden Namen ein.

    i. Aktivieren Sie die Option **Allow Just-in-Time provisioning** (Just-in-Time-Bereitstellung zulassen).

    j. Klicken Sie auf **Aktualisieren**.

### <a name="create-invision-test-user"></a>Erstellen eines InVision-Testbenutzers

1. Melden Sie sich in einem anderen Webbrowserfenster bei der InVision-Website als Administrator an.

1. Klicken Sie auf **Team**, und wählen Sie **People** (Personen) aus.

    ![Screenshot: Registerkarte „Team“, auf der „People“ (Personen) ausgewählt ist](./media/invision-tutorial/config2.png)

1. Klicken Sie auf das Symbol **+** , um einen neuen Benutzer hinzuzufügen.

    ![Screenshot: Plussymbol zum Hinzufügen eines Benutzers](./media/invision-tutorial/user1.png)

1. Geben Sie die E-Mail-Adresse des Benutzers ein, und klicken Sie auf **Next** (Weiter).

    ![Screenshot: Dialogfeld „Invite“ (Einladen), in dem Sie Adressen eingeben können](./media/invision-tutorial/user2.png)

1. Bestätigen Sie die E-Mail-Adresse, und klicken Sie auf **Invite** (Einladen).

    ![Screenshot: Dialogfeld „Invite“ (Einladen), in dem Sie „Invite“ auswählen können, um den Vorgang fortzusetzen](./media/invision-tutorial/user3.png)

> [!NOTE]
> Außerdem unterstützt InVision die automatische Benutzerbereitstellung. Weitere Informationen zum Konfigurieren der automatischen Benutzerbereitstellung finden Sie [hier](./invision-provisioning-tutorial.md).

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen:

#### <a name="sp-initiated"></a>SP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für InVision weitergeleitet, wo Sie den Anmeldeflow initiieren können.

* Rufen Sie direkt die InVision-Anmelde-URL auf, und initiieren Sie den Anmeldeflow.

#### <a name="idp-initiated"></a>IDP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch sollten Sie automatisch bei der InVision-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben.

Sie können auch den Microsoft-Bereich „Meine Apps“ verwenden, um die Anwendung in einem beliebigen Modus zu testen. Beim Klicken auf die Kachel „InVision“ in „Meine Apps“ geschieht Folgendes: Wenn Sie die Anwendung im SP-Modus konfiguriert haben, werden Sie zum Initiieren des Anmeldeflows zur Anmeldeseite der Anwendung weitergeleitet. Wenn Sie die Anwendung im IDP-Modus konfiguriert haben, sollten Sie automatisch bei der InVision-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von InVision können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-any-app)
