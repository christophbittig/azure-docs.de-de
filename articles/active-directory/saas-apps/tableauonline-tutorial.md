---
title: 'Tutorial: Azure AD-SSO-Integration mit Tableau Online'
description: Erfahren Sie, wie Sie das einmalige Anmelden für Azure Active Directory und Tableau Online konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/24/2021
ms.author: jeedes
ms.openlocfilehash: 63e812633bd19332fde73d3ebb908d13cb6faf6b
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132341025"
---
# <a name="tutorial-azure-ad-sso-integration-with-tableau-online"></a>Tutorial: Azure AD-SSO-Integration mit Tableau Online

In diesem Tutorial erfahren Sie, wie Sie Tableau Online in Azure Active Directory (Azure AD) integrieren. Die Integration von Tableau Online in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf Tableau Online hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Tableau Online anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Tableau Online-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

> [!NOTE]
> Diese Integration kann auch über die Azure AD-Umgebung für die US Government-Cloud verwendet werden. Sie finden diese Anwendung im Azure AD-Katalog für US Government-Cloudanwendungen und konfigurieren sie auf die gleiche Weise wie in der öffentlichen Cloud.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Tableau Online unterstützt **SP-initiiertes** einmaliges Anmelden.
* Tableau Online unterstützt die [**automatisierte Benutzerbereitstellung und Bereitstellungsaufhebung**](tableau-online-provisioning-tutorial.md) (empfohlen).

## <a name="add-tableau-online-from-the-gallery"></a>Hinzufügen von Tableau Online aus dem Katalog

Zum Konfigurieren der Integration von Tableau Online in Azure AD müssen Sie Tableau Online aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Tableau Online** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Tableau Online** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-tableau-online"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Tableau Online

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Tableau Online basierend auf einem Testbenutzer namens **Britta Simon**.
Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Tableau Online eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD in Tableau Online die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Tableau Online](#configure-tableau-online-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines Tableau Online-Testbenutzers](#create-tableau-online-test-user)** , um eine Entsprechung von B. Simon in Tableau Online zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Tableau Online** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    a. Geben Sie im Textfeld **Anmelde-URL** die URL ein: `https://sso.online.tableau.com/public/sp/login?alias=<entityid>`.

    b. Geben Sie im Textfeld **Bezeichner (Entitäts-ID)** die folgende URL ein: `https://sso.online.tableau.com/public/sp/metadata?alias=<entityid>`.

    > [!NOTE]
    > Den Wert für `<entityid>` erhalten Sie in diesem Tutorial im Abschnitt **Tableau Online einrichten**. Der Wert der Entitäts-ID entspricht dem **Azure AD-Bezeichner** im Abschnitt **Tableau Online einrichten**.

5. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um den Ihren Anforderungen entsprechenden **Verbundmetadaten-XML**-Code aus den verfügbaren Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

6. Kopieren Sie im Abschnitt **Tableau Online einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Tableau Online gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Tableau Online** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-tableau-online-sso"></a>Konfigurieren des einmaligen Anmeldens für Tableau Online

1. Wenn Sie die Konfiguration in Tableau Online automatisieren möchten, müssen Sie die **Browsererweiterung „Meine Apps“ für die sichere Anmeldung** installieren, indem Sie auf **Erweiterung installieren** klicken.

    ![Erweiterung „Meine Apps“](common/install-myappssecure-extension.png)

2. Klicken Sie nach dem Hinzufügen der Erweiterung zum Browser auf **Tableau Online einrichten**, um zur Anwendung Tableau Online weitergeleitet zu werden. Geben Sie dort die Administratoranmeldeinformationen ein, um sich bei Tableau Online anzumelden. Die Browsererweiterung konfiguriert die Anwendung automatisch für Sie und automatisiert die Schritte 3 bis 7.

    ![Einrichtungskonfiguration](common/setup-sso.png)

3. Wenn Sie Tableau Online manuell einrichten möchten, melden Sie sich in einem anderen Webbrowserfenster als Administrator bei der Tableau Online-Unternehmenswebsite an.

1. Wechseln Sie zu **Einstellungen** und dann zu **Authentifizierung**.

    ![Screenshot, der die im Menü „Settings“ (Einstellungen) ausgewählte Option „Authentication“ (Authentifizierung) zeigt](./media/tableauonline-tutorial/menu.png)

2. Gehen Sie im Abschnitt **Authentifizierungstypen** zum Aktivieren von SAML wie folgt vor. Aktivieren Sie die Option **Enable an additional authentication method** (Zusätzliche Authentifizierungsmethode aktivieren) und anschließend das Kontrollkästchen **SAML**.

    ![Screenshot: Abschnitt „Authentication types“ (Authentifizierungstypen), in dem Sie die beschriebenen Werte auswählen können](./media/tableauonline-tutorial/authentication.png)

3. Scrollen Sie nach unten bis zum Abschnitt **Import metadata file into Tableau Online** (Metadatendatei in Tableau Online importieren).  Klicken Sie auf „Durchsuchen“, und importieren Sie die Metadatendatei, die Sie aus Azure AD heruntergeladen haben. Klicken Sie anschließend auf **Übernehmen**.

   ![Screenshot: Abschnitt, in dem Sie die Metadatendatei importieren können](./media/tableauonline-tutorial/metadata.png)

4. Fügen Sie im Abschnitt **Match assertions** (Assertionen abgleichen) den entsprechenden Identitätsanbieter-Assertionsnamen für **E-Mail-Adresse**, **Vorname** und **Nachname** ein. Gehen Sie wie folgt vor, um diese Informationen aus Azure AD abzurufen: 
  
    a. Wechseln Sie im Azure-Portal zur Anwendungsintegrationsseite von **Tableau Online**.

    b. Klicken Sie im Abschnitt **Benutzerattribute und Ansprüche** auf das Bearbeitungssymbol.

   ![Screenshot: Abschnitt „Benutzerattribute und Ansprüche“, in dem Sie das Bearbeitungssymbol auswählen können](./media/tableauonline-tutorial/attribute-section.png)

    c. Kopieren Sie den Namespacewert für die Attribute „givenname“, „email“ und „surname“ mithilfe der folgenden Schritte:

   ![Screenshot: Attribute „Givenname“, „Surname“ und „Emailaddress“](./media/tableauonline-tutorial/name.png)

    d. Klicken Sie auf den Wert **user.givenname**.

    e. Kopieren Sie den Wert aus dem Textfeld **Namespace**.

    ![Screenshot: Abschnitt „Benutzeransprüche verwalten“, in dem Sie den Namespace eingeben können](./media/tableauonline-tutorial/attributes.png)

    f. Befolgen Sie die vorherigen Anweisungen, um die Namespacewerte für „email“ und „surname“ zu kopieren.

    g. Wechseln Sie zur Tableau Online-Anwendung, und legen Sie den Abschnitt **User Attributes & Claim** (Benutzerattribute und Ansprüche) dann wie folgt fest:

    * E-Mail: **mail** oder **userprincipalname**

    * Vorname: **givenname**

    * Nachname: **surname**

    ![Screenshot: Abschnitt „Match attributes“ (Attribute abgleichen), in dem Sie die Werte eingeben können](./media/tableauonline-tutorial/claims.png)

### <a name="create-tableau-online-test-user"></a>Erstellen eines Tableau Online-Testbenutzers

In diesem Abschnitt erstellen Sie in Tableau Online einen Benutzer namens Britta Simon.

1. Klicken Sie in **Tableau Online** auf **Settings** (Einstellungen) und dann auf den Abschnitt **Authentication** (Authentifizierung). Scrollen Sie nach unten zum Abschnitt **Manage Users** (Benutzer verwalten). Klicken Sie auf **Add Users** (Benutzer hinzufügen) und dann auf **Enter Email Addresses** (E-Mail-Adressen eingeben).
  
    ![Screenshot: Abschnitt „Manage users“ (Benutzer verwalten), in dem Sie „Add users“ (Benutzer hinzufügen) auswählen können](./media/tableauonline-tutorial/users.png)

2. Wählen Sie **Add users for (SAML) authentication** (Benutzer für (SAML-)Authentifizierung auswählen). Fügen Sie im Textfeld **Enter Email Addresses** (E-Mail-Adressen eingeben) den Eintrag „britta.simon\@contoso.com“ hinzu.
  
    ![Screenshot: Seite „Add Users“ (Benutzer hinzufügen), auf der Sie eine E-Mail-Adresse eingeben können](./media/tableauonline-tutorial/add-users.png)

3. Klicken Sie auf **Benutzer hinzufügen**.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für Tableau Online weitergeleitet, wo Sie den Anmeldeflow initiieren können.

* Rufen Sie direkt die Tableau Online-Anmelde-URL auf, und initiieren Sie den Anmeldeflow.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie in „Meine Apps“ auf die Tableau Online-Kachel klicken, wird diese zur Anmelde-URL von Tableau Online weitergeleitet. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Tableau Online können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-aad)
