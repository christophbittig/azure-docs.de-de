---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit CakeHR | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und CakeHR konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/08/2021
ms.author: jeedes
ms.openlocfilehash: d974c76870b21660ec51e6c71b4bc9cdc7c0ca7a
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132324021"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cakehr"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit CakeHR

In diesem Tutorial erfahren Sie, wie Sie CakeHR in Azure Active Directory (Azure AD) integrieren. Die Integration von CakeHR in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf CakeHR hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei CakeHR anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein CakeHR-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* CakeHR unterstützt **SP**-initiiertes einmaliges Anmelden.

> [!NOTE]
> Der Bezeichner dieser Anwendung ist ein fester Zeichenfolgenwert, daher kann in einem Mandanten nur eine Instanz konfiguriert werden.

## <a name="add-cakehr-from-the-gallery"></a>Hinzufügen von CakeHR aus dem Katalog

Zum Konfigurieren der Integration von CakeHR in Azure AD müssen Sie CakeHR aus dem Katalog der Liste der verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **CakeHR** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **CakeHR** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-cakehr"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für CakeHR

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit CakeHR mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in CakeHR eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit CakeHR die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für CakeHR](#configure-cakehr-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines CakeHR-Testbenutzers](#create-cakehr-test-user)** , um eine Entsprechung von B. Simon in CakeHR zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **CakeHR** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    a. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<CAKE_DOMAIN>.cake.hr/`

    b. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://<CAKE_DOMAIN>.cake.hr/services/saml/consume`
    
    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch die tatsächliche Anmelde-URL und Antwort-URL. Diese Werte erhalten Sie vom [Supportteam für den CakeHR-Client](mailto:info@cake.hr). Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

1. Klicken Sie im Abschnitt **SAML-Signaturzertifikat** auf die Schaltfläche **Bearbeiten**, um das Dialogfeld **SAML-Signaturzertifikat** zu öffnen.

    ![Bearbeiten des SAML-Signaturzertifikats](common/edit-certificate.png)

1. Kopieren Sie im Abschnitt **SAML-Signaturzertifikat** unter **FINGERABDRUCK** den Fingerabdruckwert, und speichern Sie ihn im Editor.

    ![Kopieren des Fingerabdruckwerts](common/copy-thumbprint.png)

1. Kopieren Sie im Abschnitt **CakeHR einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf CakeHR gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **CakeHR** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-cakehr-sso"></a>Konfigurieren des einmaligen Anmeldens für CakeHR

1. Wenn Sie die Konfiguration in CakeHR automatisieren möchten, müssen Sie die **Browsererweiterung „Meine Apps“ für die sichere Anmeldung** installieren, indem Sie auf **Erweiterung installieren** klicken.

    ![Erweiterung „Meine Apps“](common/install-myappssecure-extension.png)

1. Klicken Sie nach dem Hinzufügen der Erweiterung zum Browser auf **CakeHR einrichten**, um zur Anwendung CakeHR weitergeleitet zu werden. Geben Sie dort die Administratoranmeldeinformationen ein, um sich bei CakeHR anzumelden. Die Browsererweiterung konfiguriert die Anwendung automatisch für Sie und automatisiert die Schritte 3 bis 5.

    ![Einrichtungskonfiguration](common/setup-sso.png)

1. Wenn Sie CakeHR manuell einrichten möchten, melden Sie sich in einem neuen Webbrowserfenster bei der CakeHR-Unternehmenswebsite als Administrator an, und führen Sie die folgenden Schritte aus:

1. Klicken Sie in der oberen rechten Ecke der Seite auf **Profile** (Profil), und navigieren Sie dann zu **Settings** (Einstellungen).

    ![Screenshot des Profils, in dem „Einstellungen“ ausgewählt ist](./media/cakehr-tutorial/profile.png)

1. Klicken Sie links auf der Menüleiste auf **INTEGRATIONS** > **SAML SSO** (INTEGRATIONEN > SAML-SSO), und führen Sie die folgenden Schritte aus:

    ![Screenshot des Bereichs „Einstellungen“, in dem Sie diese Schritte ausführen](./media/cakehr-tutorial/menu.png)

    a. Geben Sie `cake.hr` in das Textfeld **Entity ID** (Entitäts-ID) ein.

    b. Fügen Sie in das Textfeld **Authentication URL** (Authentifizierungs-URL) den Wert der **Anmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    c. Fügen Sie in das Textfeld **Key fingerprint (SHA1 format)** (Schlüsselfingerabdruck (SHA-1-Format)) den Wert für **FINGERABDRUCK** ein, den Sie aus dem Azure-Portal kopiert haben.

    d. Aktivieren Sie das Kontrollkästchen **Enable Single Sign on** (Einmaliges Anmelden aktivieren).

    e. Klicken Sie auf **Speichern**.

### <a name="create-cakehr-test-user"></a>Erstellen eines CakeHR-Testbenutzers

Damit sich Azure AD-Benutzer bei CakeHR anmelden können, müssen sie in CakeHR bereitgestellt werden. Im Fall von CakeHR ist die Bereitstellung eine manuelle Aufgabe.

**Führen Sie zum Bereitstellen eines Benutzerkontos die folgenden Schritte aus:**

1. Melden Sie sich bei CakeHR als Sicherheitsadministrator an.

2. Klicken Sie links auf der Menüleiste auf **COMPANY** > **ADD** (UNTERNEHMEN > HINZUFÜGEN).

    ![Screenshot von CakeHR, in dem „COMPANY“ (Unternehmen) und „ADD“ (Hinzufügen) ausgewählt sind](./media/cakehr-tutorial/account.png)

3. Führen Sie im Popup **Add new employee** (Neuen Mitarbeiter hinzufügen) die folgenden Schritte aus:

     ![Screenshot von „Add a new employee“ (Neuen Mitarbeiter hinzufügen), in dem Sie diese Schritte ausführen](./media/cakehr-tutorial/add-account.png)

    a. Geben Sie im Textfeld **Full name** (Vollständiger Name) den Namen des Benutzers ein, z. B. „B. Simon“.

    b. Geben Sie im Textfeld **Work email** (Geschäftliche E-Mail-Adresse) die E-Mail-Adresse des Benutzers ein, z. B. `B.Simon@contoso.com`.

    c. Klicken Sie auf **KONTO ERSTELLEN**.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für CakeHR weitergeleitet. Dort können Sie den Anmeldeflow initiieren. 

* Navigieren Sie direkt zur Anmelde-URL für CakeHR, und initiieren Sie dort den Anmeldeflow.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie in „Meine Apps“ auf die Kachel „CakeHR“ klicken, werden Sie zur Anmelde-URL für CakeHR weitergeleitet. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von CakeHR können Sie Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-aad)
