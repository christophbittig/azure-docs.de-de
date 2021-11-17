---
title: 'Tutorial: Azure Active Directory-Integration mit Absorb LMS | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Absorb LMS konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/05/2021
ms.author: jeedes
ms.openlocfilehash: 39341f9ca95ec0206b795e02cebc9fb32ffc2767
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132305357"
---
# <a name="tutorial-azure-active-directory-integration-with-absorb-lms"></a>Tutorial: Azure Active Directory-Integration mit Absorb LMS

In diesem Tutorial erfahren Sie, wie Sie Absorb LMS in Azure Active Directory (Azure AD) integrieren. Die Integration von Absorb LMS in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf Absorb LMS hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Absorb LMS anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit Absorb LMS konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Sollten Sie nicht über eine Azure AD-Umgebung verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) verwenden.
* Absorb LMS-Abonnement, für das einmaliges Anmelden aktiviert ist

> [!NOTE]
> Diese Integration kann auch über die Azure AD-Umgebung für die US Government-Cloud verwendet werden. Sie finden diese Anwendung im Azure AD-Katalog für US Government-Cloudanwendungen und konfigurieren sie auf die gleiche Weise wie in der öffentlichen Cloud.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Absorb LMS unterstützt **IDP-initiiertes** einmaliges Anmelden.

> [!NOTE]
> Der Bezeichner dieser Anwendung ist ein fester Zeichenfolgenwert, daher kann in einem Mandanten nur eine Instanz konfiguriert werden.

## <a name="add-absorb-lms-from-the-gallery"></a>Hinzufügen von Absorb LMS über den Katalog

Zum Konfigurieren der Integration von Absorb LMS in Azure AD müssen Sie Absorb LMS aus dem Katalog der Liste der verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Absorb LMS** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Absorb LMS** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-absorb-lms"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Absorb LMS

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Absorb LMS mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Absorb LMS eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Absorb LMS die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Absorb LMS](#configure-absorb-lms-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines Absorb LMS-Testbenutzers](#create-absorb-lms-test-user)** , um eine Entsprechung von B. Simon in Absorb LMS zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Absorb LMS** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf die Schaltfläche **Bearbeiten**, um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    Verwenden Sie für **Absorb 5 – UI** die folgende Konfiguration:

    a. Geben Sie im Textfeld **Bezeichner** eine URL im folgenden Format ein: `https://<SUBDOMAIN>.myabsorb.com/account/saml`

    b. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://<SUBDOMAIN>.myabsorb.com/account/saml`

    Verwenden Sie für **Absorb 5 – New Learner Experience** die folgende Konfiguration:

    a. Geben Sie im Textfeld **Bezeichner** eine URL im folgenden Format ein: `https://<SUBDOMAIN>.myabsorb.com/api/rest/v2/authentication/saml`

    b. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://<SUBDOMAIN>.myabsorb.com/api/rest/v2/authentication/saml`

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Aktualisieren Sie diese Werte mit dem eigentlichen Bezeichner und der Antwort-URL. Wenden Sie sich an das [Supportteam von Absorb LMS](https://support.absorblms.com/hc/), um diese Werte zu erhalten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

5. Der folgende Screenshot zeigt die Liste der Standardattribute. **nameidentifier** ist hier **user.userprincipalname** zugeordnet.

    ![image](common/edit-attribute.png)

6. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um den Ihren Anforderungen entsprechenden **Verbundmetadaten-XML**-Code aus den verfügbaren Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

7. Kopieren Sie im Abschnitt **Absorb LMS einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Absorb LMS gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Absorb LMS** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-absorb-lms-sso"></a>Konfigurieren des einmaligen Anmeldens für Absorb LMS

1. Melden Sie sich in einem neuen Webbrowserfenster bei der Absorb LMS-Unternehmenswebsite als Administrator an.

2. Wählen Sie oben rechts die Schaltfläche **Konto**.

    ![Schaltfläche „Konto“](./media/absorblms-tutorial/account.png)

3. Wählen Sie im Bereich „Konto“ die Option **Portaleinstellungen**.

    ![Link „Portaleinstellungen“](./media/absorblms-tutorial/portal.png)

4. Wählen Sie die Registerkarte **SSO-Einstellungen verwalten** aus.

    ![Die Registerkarte „Benutzer“](./media/absorblms-tutorial/sso.png)

5. Führen Sie auf der Konfigurationsseite zum **Verwalten der SSO-Einstellungen** die folgenden Schritte aus:

    ![Seite für die Konfiguration des einmaligen Anmeldens](./media/absorblms-tutorial/settings.png)

    a. Geben Sie im Textfeld **Name** einen Namen wie Azure AD Marketplace SSO ein.

    b. Wählen Sie **SAML** als **Methode** aus.

    c. Öffnen Sie im Editor das Zertifikat, das Sie aus dem Azure-Portal heruntergeladen haben. Entfernen Sie die Tags **---BEGIN CERTIFICATE---** und **---END CERTIFICATE---**. Fügen Sie anschließend im Feld **Schlüssel** den verbleibenden Inhalt ein.

    d. Wählen Sie im Feld **Modus** die Option **Identity Provider Initiated** (Vom Identitätsanbieter initiiert).

    e. Wählen Sie im Feld **Id Property** (ID-Eigenschaft) das Attribut aus, das Sie in Azure AD als Benutzerbezeichner konfiguriert haben. Wählen Sie beispielsweise *nameidentifier* aus, wenn in Azure AD **Benutzername** ausgewählt ist.

    f. Wählen Sie **Sha256** als **Signaturtyp** aus.

    g. Fügen Sie im Feld **Anmelde-URL** die **URL für den Benutzerzugriff** von der Seite **Eigenschaften** der Anwendung im Azure-Portal hinzu.

    h. Fügen Sie als **Abmelde-URL** den Wert für **Abmelde-URL** ein, den Sie im Azure-Portal im Fenster **Anmelden konfigurieren** kopiert haben.

    i. Wechseln Sie für **Automatische Umleitung** zu **Ein**.

6. Wählen Sie **Speichern** aus.

    ![Umschalter „Only Allow SSO Login“ (Nur SSO-Anmeldung zulassen)](./media/absorblms-tutorial/save.png)

### <a name="create-absorb-lms-test-user"></a>Erstellen eines Absorb LMS-Testbenutzers

Damit sich Azure AD-Benutzer an Absorb LMS anmelden können, müssen sie in Absorb LMS eingerichtet werden. Bei Absorb LMS ist die Bereitstellung eine manuelle Aufgabe.

**Um die Benutzerbereitstellung zu konfigurieren, führen Sie die folgenden Schritte durch:**

1. Melden Sie sich bei der Absorb LMS-Unternehmenswebsite als Administrator an.

2. Wählen Sie im Bereich **Benutzer** die Option **Benutzer**.

    ![Link „Benutzer“](./media/absorblms-tutorial/users.png)

3. Wählen Sie die Registerkarte **Benutzer** aus.

    ![Dropdownliste „Neu hinzufügen“](./media/absorblms-tutorial/add.png)

4. Gehen Sie auf der Seite **Benutzer hinzufügen** wie folgt vor:

    ![Seite „Benutzer hinzufügen“](./media/absorblms-tutorial/user.png)

    a. Geben Sie im Feld **Vorname** den Vornamen ein, z.B. **Britta**.

    b. Geben Sie im Feld **Nachname** den Nachnamen ein, z.B. **Simon**.

    c. Geben Sie im Feld **Benutzername** einen vollständigen Namen ein, z.B. **Britta Simon**.

    d. Geben Sie im Feld **Kennwort** das Benutzerkennwort ein.

    e. Geben Sie das Kennwort im Feld **Kennwort bestätigen** noch einmal ein.

    f. Legen Sie den Umschalter **Ist aktiv** auf **Aktiv** fest.

5. Wählen Sie **Speichern** aus.

    ![Umschalter „Only Allow SSO Login“ (Nur SSO-Anmeldung zulassen)](./media/absorblms-tutorial/save.png)

    > [!NOTE]
    > Standardmäßig ist die Benutzerbereitstellung beim einmaligen Anmelden nicht aktiviert. Wenn der Kunde dieses Feature aktivieren möchte, muss es gemäß der Beschreibung [in dieser](https://support.absorblms.com/hc/en-us/articles/360014083294-Incoming-SAML-2-0-SSO-Account-Provisioning) Dokumentation eingerichtet werden. Beachten Sie auch, dass die Benutzerbereitstellung nur unter **Absorb 5 – New Learner Experience** mit ACS URL-`https://company.myabsorb.com/api/rest/v2/authentication/saml` verfügbar ist.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen:

* Klicken Sie im Azure-Portal auf „Diese Anwendung testen“. Dadurch sollten Sie automatisch bei der Absorb LMS-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie im Zugriffsbereich auf die Kachel „Absorb LMS“ klicken, sollten Sie automatisch bei der Absorb LMS-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Absorb LMS können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-any-app)
