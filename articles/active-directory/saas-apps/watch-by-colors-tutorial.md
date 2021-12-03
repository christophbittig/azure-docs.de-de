---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD mit Watch by Colors'
description: Hier erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Watch by Colors konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/19/2021
ms.author: jeedes
ms.openlocfilehash: 3a48fd180e0f40039f36e0f76471fdba6e101e52
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132303522"
---
# <a name="tutorial-azure-ad-sso-integration-with-watch-by-colors"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD mit Watch by Colors

In diesem Tutorial erfahren Sie, wie Sie Watch by Colors in Azure Active Directory (Azure AD) integrieren. Die Integration von Watch by Colors in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf Watch by Colors hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Watch by Colors anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Watch by Colors-Abonnement, für das einmaliges Anmelden (SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Watch by Colors unterstützt **SP- und IDP-initiiertes** einmaliges Anmelden.

## <a name="add-watch-by-colors-from-the-gallery"></a>Hinzufügen von Watch by Colors aus dem Katalog

Zum Konfigurieren der Integration von Watch by Colors in Azure AD müssen Sie Watch by Colors aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Watch by Colors** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Watch by Colors** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-watch-by-colors"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Watch by Colors

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Watch by Colors mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Watch by Colors eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Watch by Colors die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Watch by Colors](#configure-watch-by-colors-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines Watch by Colors-Testbenutzers](#create-watch-by-colors-test-user)** , um in Watch by Colors eine Entsprechung von B. Simon zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Watch by Colors** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Im Abschnitt **Grundlegende SAML-Konfiguration** ist die Anwendung im **IDP-initiierten** Modus vorkonfiguriert, und die erforderlichen URLs sind bereits mit Azure vorausgefüllt. Der Benutzer muss die Konfiguration speichern, indem er auf die Schaltfläche **Speichern** klickt.

1. Klicken Sie auf **Zusätzliche URLs festlegen**, und führen Sie den folgenden Schritt aus, wenn Sie die Anwendung im **SP-initiierten Modus** konfigurieren möchten:

    Geben Sie im Textfeld **Anmelde-URL** die URL ein: `https://app.colorscorporation.com/login`.

1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf die Schaltfläche „Kopieren“, um die **App-Verbundmetadaten-URL** zu kopieren, und speichern Sie sie auf Ihrem Computer.

    ![Downloadlink für das Zertifikat](common/copy-metadataurl.png)

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Watch by Colors gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Watch by Colors** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** die entsprechende Rolle für den Benutzer in der Liste aus, und klicken Sie dann im unteren Bildschirmbereich auf die Schaltfläche **Auswählen**.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-watch-by-colors-sso"></a>Konfigurieren des einmaligen Anmeldens für Watch by Colors

1. Wenn Sie die Konfiguration in Watch by Colors automatisieren möchten, müssen Sie die **Browsererweiterung „Meine Apps“ für die sichere Anmeldung** installieren, indem Sie auf **Erweiterung installieren** klicken.

    ![Erweiterung „Meine Apps“](common/install-myappssecure-extension.png)

2. Klicken Sie nach dem Hinzufügen der Erweiterung zum Browser auf **Watch by Colors einrichten**, um zur Anwendung Watch by Colors weitergeleitet zu werden. Geben Sie dort die Administratoranmeldeinformationen ein, um sich bei Watch by Colors anzumelden. Die Browsererweiterung konfiguriert die Anwendung automatisch für Sie und automatisiert die Schritte 3 bis 5.

    ![Einrichtungskonfiguration](common/setup-sso.png)

3. Wenn Sie Watch by Colors manuell einrichten möchten, öffnen Sie ein neues Webbrowserfenster, melden Sie sich bei der Watch by Colors-Unternehmenswebsite als Administrator an, und führen Sie die folgenden Schritte aus:

4. Klicken Sie in der oberen rechten Ecke der Seite auf **Profile** > **Account Settings** > **SSO (Single Sign On)** (Profil > Kontoeinstellungen > Einmaliges Anmelden).

    ![Der Screenshot zeigt die Seite „Kontoeinstellungen“, auf der „SSO“ deaktiviert ist.](./media/watch-by-colors-tutorial/account.png)

5. Führen Sie auf der Seite **SSO (Single Sign On)** (Einmaliges Anmelden) die folgenden Schritte aus:

    ![Der Screenshot zeigt die Registerkarte „SAML-Setup“, auf der Sie SAML aktivieren können.](./media/watch-by-colors-tutorial/profile.png)

    a. Legen Sie **Enable SAML** (SAML aktivieren) auf **ON** (EIN) fest.

    b. Fügen Sie im Textfeld **URL** die **Verbundmetadaten-URL** ein, die Sie aus dem Azure-Portal kopiert haben.

    c. Wenn Sie auf **Import** (Importieren) klicken, werden die übrigen Felder auf der Seite automatisch aufgefüllt.

    d. Klicken Sie auf **Speichern**.

### <a name="create-watch-by-colors-test-user"></a>Erstellen eines Watch by Colors-Testbenutzers

Damit sich Azure AD-Benutzer bei Watch by Colors anmelden können, müssen sie in Watch by Colors bereitgestellt werden. Im Fall von Watch by Colors muss die Bereitstellung manuell ausgeführt werden.

**Führen Sie zum Bereitstellen eines Benutzerkontos die folgenden Schritte aus:**

1. Melden Sie sich bei Watch by Colors als Sicherheitsadministrator an.

1. Klicken Sie in der oberen rechten Ecke der Seite auf **Profile** > **Users** > **Add User** (Profil > Benutzer > Benutzer hinzufügen).

    ![Der Screenshot zeigt die Seite „Benutzer“.](./media/watch-by-colors-tutorial/user.png)

1. Führen Sie auf der Seite **User Details** (Benutzerdetails) die folgenden Schritte aus:

    ![Der Screenshot zeigt die Seite „Benutzerdetails“, auf der Sie die beschriebenen Werte eingeben können.](./media/watch-by-colors-tutorial/details.png)

    a. Geben Sie im Textfeld **First name** (Vorname) den Vornamen des Benutzers ein, z. B. **B**.

    b. Geben Sie im Textfeld **Last Name** (Nachname) den Nachnamen des Benutzers ein, z.B. **Simon**.

    c. Geben Sie im Textfeld **Email** (E-Mail-Adresse) die E-Mail-Adresse des Benutzers ein, z.B. `B.Simon@contoso.com`.

    d. Geben Sie in das Textfeld **Password** (Kennwort) das Kennwort ein.

    e. Wählen Sie unter **Account Permissions** (Kontoberechtigungen) die entsprechenden Berechtigungen für Ihre Organisation aus.

    f. Klicken Sie auf **Speichern**.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

#### <a name="sp-initiated"></a>SP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für Watch by Colors weitergeleitet, wo Sie den Anmeldeflow initiieren können.  

* Rufen Sie direkt die Anmelde-URL für Watch by Colors auf, und initiieren Sie den Anmeldeflow.

#### <a name="idp-initiated"></a>IDP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch sollten Sie automatisch bei der Watch by Colors-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. 

Sie können auch den Microsoft-Bereich „Meine Apps“ verwenden, um die Anwendung in einem beliebigen Modus zu testen. Beim Klicken auf die Kachel „Watch by Colors“ unter „Meine Apps“ geschieht Folgendes: Wenn Sie die Anwendung im SP-Modus konfiguriert haben, werden Sie zum Initiieren des Anmeldeflows zur Anmeldeseite der Anwendung weitergeleitet. Wenn Sie die Anwendung im IDP-Modus konfiguriert haben, sollten Sie automatisch bei der Watch by Colors-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Watch by Colors können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-aad)
