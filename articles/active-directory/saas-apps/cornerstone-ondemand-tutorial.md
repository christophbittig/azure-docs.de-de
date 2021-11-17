---
title: 'Tutorial: Integration von einmaligem Anmelden (SSO) zwischen Azure Active Directory und Cornerstone | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie einmaliges Anmelden zwischen Azure Active Directory und Cornerstone Single Sign-On konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/27/2021
ms.author: jeedes
ms.openlocfilehash: 33d462c1811e8889478fae50a22d45e185fb6e10
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132338647"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cornerstone"></a>Tutorial: Integration von einmaligem Anmelden (SSO) zwischen Azure Active Directory und Cornerstone

In diesem Tutorial erfahren Sie, wie Sie die Integration des einmaligen Anmeldens zwischen Cornerstone und Azure Active Directory (Azure AD) einrichten. Die Integration von Cornerstone in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer SSO-Zugriff auf Cornerstone hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Cornerstone anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* SSO in Cornerstone wurde aktiviert.

> [!NOTE]
> Diese Integration kann auch über die Azure AD-Umgebung für die US Government-Cloud verwendet werden. Sie finden diese Anwendung im Azure AD-Katalog für US Government-Cloudanwendungen und konfigurieren sie auf die gleiche Weise wie in der öffentlichen Cloud.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Cornerstone unterstützt **SP**-initiiertes einmaliges Anmelden.

* Cornerstone unterstützt die [automatisierte Benutzerbereitstellung](cornerstone-ondemand-provisioning-tutorial.md).

* Zum Integrieren von einem oder mehreren Produkten aus dieser speziellen Liste sollten Sie diese Cornerstone Single Sign-On-App aus dem Katalog verwenden.

    Wir bieten Lösungen für Folgendes:

    1. Mitarbeitersuche
    2. Lernen
    3. Entwicklung
    4. Inhalt
    5. Leistung
    6. Karriere
    7. HR

## <a name="adding-cornerstone-single-sign-on-from-the-gallery"></a>Hinzufügen von Cornerstone Single Sign-On aus dem Katalog

Zum Konfigurieren der Azure AD SSO-Integration mit Cornerstone müssen Sie …

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** im Suchfeld den Suchbegriff **Cornerstone Single Sign-On** ein.
1. Wählen Sie im Ergebnisbereich **Cornerstone Single Sign-On** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-cornerstone"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Cornerstone

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Cornerstone mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Cornerstone eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Cornerstone die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für Cornerstone](#configure-cornerstone-single-sign-on)** , um das einmalige Anmelden in Cornerstone zu konfigurieren.
    1. **[Erstellen eines Cornerstone Single Sign-On-Testbenutzers](#create-cornerstone-single-sign-on-test-user)** , um in Cornerstone eine Entsprechung von B. Simon zu erhalten, die mit der Darstellung des Benutzers in Azure AD verknüpft ist.
3. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert
4. **[Testen des einmaligen Anmeldens für Cornerstone (mobil)](#test-sso-for-cornerstone-mobile)** , um zu überprüfen, ob die Konfiguration funktioniert.

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Cornerstone Single Sign-On** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    a. Geben Sie im Textfeld **Bezeichner** eine URL im folgenden Format ein: `https://<PORTAL_NAME>.csod.com`

    b. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://<PORTAL_NAME>.csod.com/samldefault.aspx?ouid=<OUID>`

    c. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<PORTAL_NAME>.csod.com/samldefault.aspx?ouid=<OUID>`.

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch die tatsächliche Antwort-URL, den tatsächlichen Bezeichner und die tatsächliche Anmelde-URL. Wenden Sie sich an Ihr Cornerstone-Implementierungsprojektteam, um diese Werte zu erhalten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

4. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** zum Eintrag **Zertifikat (Base64)** . Wählen Sie **Herunterladen** aus, um das Zertifikat herunterzuladen, und speichern Sie es auf Ihrem Computer.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

6. Kopieren Sie im Abschnitt **Cornerstone Single Sign-On einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie den Zugriff auf Cornerstone gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Cornerstone Single Sign-On** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-cornerstone-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für Cornerstone

Um das SSO in Cornerstone zu konfigurieren, müssen Sie sich an Ihr Cornerstone-Implementierungsprojektteam zu wenden. Es führt die Einrichtung durch, damit die SAML-SSO-Verbindung auf beiden Seiten richtig festgelegt ist.

### <a name="create-cornerstone-single-sign-on-test-user"></a>Erstellen eines Cornerstone Single Sign-On-Testbenutzers

In diesem Abschnitt erstellen Sie in Cornerstone einen Benutzer namens Britta Simon. Wenden Sie sich bitte an Ihr Cornerstone-Implementierungsprojektteam, um die Benutzer in Cornerstone hinzuzufügen. Benutzer müssen erstellt und aktiviert werden, damit Sie einmaliges Anmelden verwenden können.

Außerdem unterstützt Cornerstone Single Sign-On die automatische Benutzerbereitstellung. Weitere Informationen zum Konfigurieren der automatischen Benutzerbereitstellung finden Sie [hier](./cornerstone-ondemand-provisioning-tutorial.md).

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für Cornerstone weitergeleitet, wo Sie den Anmeldeablauf initiieren können. 

* Rufen Sie direkt die Cornerstone-Anmelde-URL auf, und initiieren Sie den Anmeldeablauf.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie in „Meine Apps“ auf die Kachel „Cornerstone Single Sign-On“ klicken, werden Sie zur Anmelde-URL von Cornerstone Single Sign-On weitergeleitet. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="test-sso-for-cornerstone-mobile"></a>Testen von SSO für Cornerstone (mobil)

1. Melden Sie sich in einem anderen Browserfenster bei Ihrer Cornerstone-Website als Administrator an, und führen Sie die folgenden Schritte aus.

    a. Wechseln Sie zu **Admin -> Extras -> KERN FUNKTIONEN -> Kern-Einstellungen -> Authentifizierungs-Einstellungen**.

    ![Screenshot der die Authentifizierungs-Einstellungen in der mobilen Anwendung für Cornerstone zeigt.](./media/cornerstone-ondemand-tutorial/division-mobile.png)

    b. Suchen Sie den **Divisionsnamen**, indem Sie den Divisionsnamen im Suchfeld eingeben.

    c. Klicken Sie in den Ergebnissen auf den **Divisionnamen**.

    d. Wählen Sie in der Dropdownliste SAML/IDP-Server-URL den entsprechenden SAML/IDP-Server aus, der für die Benutzerauthentifizierung verwendet werden soll.

    ![Screenshot, für die anderen Anmeldeinformationen, die für den SAML-/IDP-Server überprüft wurden.](./media/cornerstone-ondemand-tutorial/other-credentials.png)

    e. Klicken Sie auf **Speichern**.

1. Wechseln Sie zu **Admin -> Extras -> Kern-Funktionen -> Kern-Einstellungen -> Mobil**.

    a. Wählen Sie die entsprechenden **Division OE** aus.

    b. Wählen Sie **Benutzern in dieser Organisationseinheit den Zugriff auf die Cornerstone Learn-App auf ihrem mobilen und Tablet-Gerät gewähren** aus, und aktivieren Sie das Kontrollkästchen für den mobilen Zugriff.

    c. Klicken Sie auf **Speichern**.

2. Öffnen Sie die mobile **Cornerstone Learn-Anwendung**. Geben Sie auf der Anmeldeseite den Portalnamen ein.

    ![Screenshot, der die mobile Anwendung „Cornerstone“ zeigt.](./media/cornerstone-ondemand-tutorial/welcome-mobile.png)

3. Klicken Sie auf **Alternative Anmeldung** und dann auf **SSO**.

    ![Screenshot, der die mobile Anwendung „Alternatives Anmelden“ zeigt.](./media/cornerstone-ondemand-tutorial/sso-mobile.png)

4. .  Geben Sie Ihre **Azure AD-Anmeldeinformationen** ein, um sich bei der Cornerstone.-Anwendung anzumelden, und klicken Sie auf **Weiter**.

    ![Screenshot, der die mobile Anwendung „Azure AD Anmeldeinformationen“ zeigt.](./media/cornerstone-ondemand-tutorial/credentials-mobile.png)

5. Nach einer erfolgreicher Anmeldung wird die Startseite der Anwendung wie nachfolgend angezeigt.

    ![Screenshot, der die Startseite der mobile Anwendung.](./media/cornerstone-ondemand-tutorial/home-page-mobile.png)

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Cornerstone Single Sign-On können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-aad)
