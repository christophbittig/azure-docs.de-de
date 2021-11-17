---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Harness | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Harness konfigurieren.
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
ms.openlocfilehash: a5368ab5b5d1464bb2b5638b58366f208ba3d1fb
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132317900"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-harness"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Harness

In diesem Tutorial erfahren Sie, wie Sie Harness in Azure Active Directory (Azure AD) integrieren. Die Integration von Harness in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf Harness hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Harness anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Harness-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Harness unterstützt **SP- und IDP-initiiertes** einmaliges Anmelden.

* Harness unterstützt [Automatisierte Benutzerbereitstellung](harness-provisioning-tutorial.md).

> [!NOTE]
> Der Bezeichner dieser Anwendung ist ein fester Zeichenfolgenwert, daher kann in einem Mandanten nur eine Instanz konfiguriert werden.

## <a name="add-harness-from-the-gallery"></a>Hinzufügen von Harness aus dem Katalog

Zum Konfigurieren der Integration von Harness in Azure AD müssen Sie Harness aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Harness** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Harness** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-harness"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Harness

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Harness mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Harness eingerichtet werden.

Führen Sie die folgenden Schritte aus, um das einmalige Anmelden von Azure AD mit Harness zu konfigurieren und zu testen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Harness](#configure-harness-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines Harness-Testbenutzers](#create-harness-test-user)** , um eine Entsprechung von B. Simon in Harness zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Harness** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus, wenn Sie die Anwendung im **IDP-initiierten** Modus konfigurieren möchten:

    Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://app.harness.io/gateway/api/users/saml-login?accountId=<harness_account_id>`.

1. Klicken Sie auf **Zusätzliche URLs festlegen**, und führen Sie den folgenden Schritt aus, wenn Sie die Anwendung im **SP-initiierten Modus** konfigurieren möchten:

    Geben Sie im Textfeld **Anmelde-URL** die URL ein: `https://app.harness.io/`.

    > [!NOTE]
    > Der Wert der Antwort-URL entspricht nicht dem tatsächlichen Wert. Sie rufen die eigentliche Antwort-URL im Abschnitt **Konfigurieren des einmaligen Anmeldens für Harness** ab. Die Vorgehensweise wird später in diesem Tutorial erläutert. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

1. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** zu **Verbundmetadaten-XML**, und wählen Sie **Herunterladen** aus, um das Zertifikat herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

1. Kopieren Sie im Abschnitt **Harness einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Harness gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Harness** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-harness-sso"></a>Konfigurieren des einmaligen Anmeldens für Harness

1. Wenn Sie die Konfiguration in Harness automatisieren möchten, müssen Sie die **Browsererweiterung „Meine Apps“ für die sichere Anmeldung** installieren, indem Sie auf **Erweiterung installieren** klicken.

    ![Erweiterung „Meine Apps“](common/install-myappssecure-extension.png)

2. Klicken Sie nach dem Hinzufügen der Erweiterung zum Browser auf **Harness einrichten**, um zur Anwendung Harness weitergeleitet zu werden. Geben Sie dort die Administratoranmeldeinformationen ein, um sich bei Harness anzumelden. Die Browsererweiterung konfiguriert die Anwendung automatisch für Sie und automatisiert die Schritte 3 bis 6.

    ![Einrichtungskonfiguration](common/setup-sso.png)

3. Wenn Sie Harness manuell einrichten möchten, öffnen Sie ein neues Webbrowserfenster, melden Sie sich bei der Harness-Unternehmenswebsite als Administrator an, und führen Sie die folgenden Schritte aus:

4. Klicken Sie oben rechts auf der Seite auf **Continuous Security** > **Access Management** > **Authentication Settings** (Kontinuierliche Sicherheit > Zugriffsverwaltung > Authentifizierungseinstellungen).

    ![Screenshot: Menü „Continuous Security“ (Kontinuierliche Sicherheit) mit Auswahl von „Access Management“ (Zugriffsverwaltung) und „Authentication Settings“ (Authentifizierungseinstellungen)](./media/harness-tutorial/authentication.png)

5. Klicken Sie im Abschnitt **SSO Providers** (SSO-Anbieter) auf **+ Add SSO Providers** > **SAML** (+ SSO-Anbieter hinzufügen > SAML).

    ![Screenshot: „SSO Providers“ (SSO-Anbieter) mit Auswahl von „+ Add SSO Providers“ (+ SSO-Anbieter hinzufügen) und „SAML“](./media/harness-tutorial/providers.png)

6. Führen Sie im Popupfenster **SAML Provider** (SAML-Anbieter) die folgenden Schritte aus:

    ![Screenshot: Popupfenster „SAML Provider“ (SAML-Anbieter) mit Hervorhebung der Felder „URL“ und „Display Name“ (Anzeigename) und Auswahl der Schaltflächen „Choose File“ (Datei auswählen) und „Submit“ (Senden)](./media/harness-tutorial/file.png)

    a. Kopieren Sie die Instanz **In your SSO Provider, please enable SAML-based login, then enter the following URL** (Aktivieren Sie in Ihrem SSO-Anbieter die SAML-basierte Anmeldung, und geben Sie dann die folgende URL ein:), und fügen Sie sie im Azure-Portal im Abschnitt **Grundlegende SAML-Konfiguration** ins Textfeld „Antwort-URL“ ein.

    b. Geben Sie im Textfeld **Display Name** (Anzeigename) Ihren Anzeigenamen ein.

    c. Klicken Sie auf **Choose file** (Datei auswählen), um die XML-Datei mit den Verbundmetadaten hochzuladen, die Sie aus Azure AD heruntergeladen haben.

    d. Klicken Sie auf **SUBMIT** (SENDEN).

### <a name="create-harness-test-user"></a>Erstellen eines Harness-Testbenutzers

Damit sich Azure AD-Benutzer bei Harness anmelden können, müssen sie in Harness bereitgestellt werden. Im Fall von Harness ist die Bereitstellung eine manuelle Aufgabe.

**Führen Sie zum Bereitstellen eines Benutzerkontos die folgenden Schritte aus:**

1. Melden Sie sich bei Harness als Administrator an.

1. Klicken Sie oben rechts auf der Seite auf **Continuous Security** > **Access Management** > **Users** (Kontinuierliche Sicherheit > Zugriffsverwaltung > Benutzer).

    ![Screenshot: Menü „Continuous Security“ (Kontinuierliche Sicherheit) mit Auswahl von „Access Management“ (Zugriffsverwaltung) und „Users“ (Benutzer)](./media/harness-tutorial/users.png)

1. Klicken Sie rechts oben auf der Seite auf **+ Add User** (+ Benutzer hinzufügen).

    ![Screenshot: Seite „Users“ (Benutzer) mit ausgewählter Aktion „+ Add User“ (+ Benutzer hinzufügen)](./media/harness-tutorial/add-user.png)

1. Führen Sie im Popupelement **Add User** (Benutzer hinzufügen) die folgenden Schritte aus:

    ![Konfiguration von Harness](./media/harness-tutorial/configure.png)

    a. Geben Sie im Textfeld **Email Address(es)** (E-Mail-Adresse(n)) die E-Mail-Adresse des Benutzers ein, z. B. `B.simon@contoso.com`.

    b. Wählen Sie unter **User Groups** (Benutzergruppen) Ihre Benutzergruppen aus.

    c. Klicken Sie auf **Submit**(Senden).

Außerdem unterstützt Harness automatische Benutzerbereitstellung. Weitere Informationen zum Konfigurieren der automatischen Benutzerbereitstellung finden Sie [hier](./harness-provisioning-tutorial.md).

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

#### <a name="sp-initiated"></a>SP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für Harness weitergeleitet, wo Sie den Anmeldeflow initiieren können.  

* Rufen Sie direkt die Anmelde-URL für Harness auf, und initiieren Sie den Anmeldeflow.

#### <a name="idp-initiated"></a>IDP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch sollten Sie automatisch bei der Harness-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. 

Sie können auch den Microsoft-Bereich „Meine Apps“ verwenden, um die Anwendung in einem beliebigen Modus zu testen. Beim Klicken auf die Kachel „Harness“ in „Meine Apps“ geschieht Folgendes: Wenn Sie die Anwendung im SP-Modus konfiguriert haben, werden Sie zum Initiieren des Anmeldeflows zur Anmeldeseite der Anwendung weitergeleitet. Wenn Sie die Anwendung im IDP-Modus konfiguriert haben, sollten Sie automatisch bei der Harness-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Harness können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-aad)
