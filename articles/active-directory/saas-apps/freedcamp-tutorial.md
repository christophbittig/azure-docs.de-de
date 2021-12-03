---
title: 'Tutorial: Azure Active Directory-Integration mit Freedcamp | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Freedcamp konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/30/2021
ms.author: jeedes
ms.openlocfilehash: 847b3ed779777badaa5b6105f561bb2034e9aa0e
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132334243"
---
# <a name="tutorial-integrate-freedcamp-with-azure-active-directory"></a>Tutorial: Integrieren von Freedcamp in Azure Active Directory

In diesem Tutorial erfahren Sie, wie Sie Freedcamp in Azure Active Directory (Azure AD) integrieren. Bei der Integration von Freedcamp in Azure AD haben Sie folgende Möglichkeiten:

* Steuern Sie in Azure AD, wer Zugriff auf Freedcamp hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Freedcamp anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Freedcamp-Abonnement, für das einmaliges Anmelden (SSO) aktiviert ist

> [!NOTE]
> Diese Integration kann auch über die Azure AD-Umgebung für die US Government-Cloud verwendet werden. Sie finden diese Anwendung im Azure AD-Katalog für US Government-Cloudanwendungen und konfigurieren sie auf die gleiche Weise wie in der öffentlichen Cloud.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Freedcamp unterstützt **SP- und IDP-initiiertes** einmaliges Anmelden.

## <a name="add-freedcamp-from-the-gallery"></a>Hinzufügen von Freedcamp aus dem Katalog

Zum Konfigurieren der Integration von Freedcamp in Azure AD müssen Sie Freedcamp aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Freedcamp** in das Suchfeld ein.
1. Wählen Sie **Freedcamp** im Ergebnisbereich aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-freedcamp"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Freedcamp

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Freedcamp mithilfe eines Testbenutzers mit dem Namen **Britta Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Freedcamp eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Freedcamp die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Freedcamp](#configure-freedcamp-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
    1. **[Erstellen eines Freedcamp-Testbenutzers](#create-freedcamp-test-user)** , um in Freedcamp eine Entsprechung von B. Simon zu erhalten, die mit der Benutzerdarstellung in Azure AD verknüpft ist.
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Freedcamp** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus, wenn Sie die Anwendung im **IDP-initiierten** Modus konfigurieren möchten:

    1. Geben Sie im Textfeld **Bezeichner** eine URL im folgenden Format ein: `https://<SUBDOMAIN>.freedcamp.com/sso/<UNIQUEID>`

    2. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://<SUBDOMAIN>.freedcamp.com/sso/acs/<UNIQUEID>`

1. Klicken Sie auf **Zusätzliche URLs festlegen**, und führen Sie den folgenden Schritt aus, wenn Sie die Anwendung im **SP-initiierten Modus** konfigurieren möchten:

    Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<SUBDOMAIN>.freedcamp.com/login`

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Sie müssen diese Werte mit dem tatsächlichen Bezeichner, der Antwort-URL und der Anmelde-URL aktualisieren. Benutzer*innen können auch die URL-Werte für ihre eigene Benutzerdomäne eingeben, und diese Werte müssen nicht unbedingt das Format `freedcamp.com` haben. Sie können einen beliebigen, für die Benutzerdomäne spezifischen Wert eingeben, der speziell für ihre Anwendungsinstanz gilt. Sie können sich auch an das [Supportteam für Freedcamp Client](mailto:devops@freedcamp.com) wenden, um weitere Informationen zu URL-Mustern zu erhalten.

1. Suchen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** den Eintrag **Zertifikat (Base64)** . Klicken Sie auf **Herunterladen**, um das Zertifikat herunterzuladen, und speichern Sie es auf Ihrem Computer.

   ![Downloadlink für das Zertifikat](common/certificatebase64.png)

1. Kopieren Sie im Abschnitt **Freedcamp einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

   ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

In diesem Abschnitt erstellen Sie im Azure-Portal eine Testbenutzerin mit dem Namen Britta Simon.

1. Wählen Sie im linken Bereich des Microsoft Azure-Portals **Azure Active Directory** > **Benutzer** > **Alle Benutzer** aus.
1. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.
1. Führen Sie unter den Eigenschaften für **Benutzer** die folgenden Schritte aus:
   1. Geben Sie im Feld **Name** die Zeichenfolge `Britta Simon` ein.  
   1. Geben Sie im Feld **Benutzername** die Zeichenfolge username@companydomain.extension ein. Beispiel: `BrittaSimon@contoso.com`.
   1. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**, und notieren Sie sich den Wert aus dem Feld **Kennwort**.
   1. Klicken Sie auf **Erstellen**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Freedcamp gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Freedcamp** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Benutzerliste den Eintrag **Britta Simon** aus, und klicken Sie anschließend im unteren Bildschirmbereich auf die Schaltfläche **Auswählen**.
1. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** die entsprechende Rolle für den Benutzer in der Liste aus, und klicken Sie dann im unteren Bildschirmbereich auf die Schaltfläche **Auswählen**.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-freedcamp-sso"></a>Konfigurieren des einmaligen Anmeldens für Freedcamp

1. Wenn Sie die Konfiguration in Freedcamp automatisieren möchten, müssen Sie die **Browsererweiterung „Meine Apps“ für die sichere Anmeldung** installieren, indem Sie auf **Erweiterung installieren** klicken.

    ![Erweiterung „Meine Apps“](common/install-myappssecure-extension.png)

2. Klicken Sie nach dem Hinzufügen der Erweiterung zum Browser auf **Freedcamp einrichten**, um zur Anwendung Freedcamp weitergeleitet zu werden. Geben Sie dort die Administratoranmeldeinformationen ein, um sich bei Freedcamp anzumelden. Die Browsererweiterung konfiguriert die Anwendung automatisch für Sie und automatisiert die Schritte 3 bis 5.

    ![Einrichtungskonfiguration](common/setup-sso.png)

3. Wenn Sie Freedcamp manuell einrichten möchten, öffnen Sie ein neues Webbrowserfenster, melden Sie sich bei der Freedcamp-Unternehmenswebsite als Administrator an, und führen Sie die folgenden Schritte aus:

4. Klicken Sie in der oberen rechten Ecke der Seite auf **Profile** (Profil), und navigieren Sie dann zu **My Account** (Mein Konto).

    ![Screenshot: Ausgewählte Optionen „Profil“ und „Mein Konto“](./media/freedcamp-tutorial/config01.png)

5. Klicken Sie links auf der Menüleiste auf **SSO**, und führen Sie auf der Seite **Your SSO connections** (Ihre SSO-Verbindungen) die folgenden Schritte aus:

    ![Screenshot: „SSO“ (Einmaliges Anmelden) in der linken Menüleiste ausgewählt und die Seite „Your SSO Connections“ (Ihre Verbindungen mit einmaligem Anmelden) mit eingegeben Werten und ausgewählter Schaltfläche „Submit“ (Übermitteln)](./media/freedcamp-tutorial/config02.png)

    a. Geben Sie im Textfeld **Title** (Titel) den Titel ein.

    b. Fügen Sie in das Textfeld **Entity ID** (Entitäts-ID) den Wert für den **Azure AD-Bezeichner** ein, den Sie aus dem Azure-Portal kopiert haben.

    c. Fügen Sie im Textfeld **Login URL** (Anmelde-URL) den Wert der **Anmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    d. Öffnen Sie das Base64-codierte Zertifikat im Editor, kopieren Sie den Inhalt, und fügen Sie ihn in das Textfeld **Certificate** (Zertifikat) ein.

    e. Klicken Sie auf **Submit**(Senden).

### <a name="create-freedcamp-test-user"></a>Erstellen eines Freedcamp-Testbenutzers

Damit sich Azure AD-Benutzer bei Freedcamp anmelden können, müssen sie in Freedcamp bereitgestellt werden. Im Fall von Freedcamp muss die Bereitstellung manuell ausgeführt werden.

**Führen Sie zum Bereitstellen eines Benutzerkontos die folgenden Schritte aus:**

1. Melden Sie sich in einem anderen Webbrowserfenster bei Freedcamp als Sicherheitsadministrator an.

2. Klicken Sie in der oberen rechten Ecke der Seite auf **Profile** (Profil), und navigieren Sie dann zu **Manage System** (System verwalten).

    ![Freedcamp-Konfiguration](./media/freedcamp-tutorial/config03.png)

3. Führen Sie auf der rechten Seite der Seite „Manage System“ (System verwalten) die folgenden Schritte aus:

    ![Screenshot: Ausgewählte Schaltfläche „Add Or Invite Users“ (Benutzer hinzufügen oder einladen), hervorgehobenes Feld „E-Mail-Adresse“ und ausgewählte Schaltfläche „Benutzer hinzufügen“](./media/freedcamp-tutorial/config04.png)

    a. Klicken Sie auf **Add or invite Users** (Benutzer hinzufügen oder einladen).

    b. Geben Sie im Textfeld **Email** (E-Mail-Adresse) die E-Mail-Adresse des Benutzers ein, z. B. `Brittasimon@contoso.com`.

    c. Klicken Sie auf **Benutzer hinzufügen**.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

#### <a name="sp-initiated"></a>SP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für Freedcamp weitergeleitet, wo Sie den Anmeldeflow initiieren können.  

* Navigieren Sie direkt zur Anmelde-URL für Freedcamp, und initiieren Sie dort den Anmeldeflow.

#### <a name="idp-initiated"></a>IDP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch sollten Sie automatisch bei der Freedcamp-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. 

Sie können auch den Microsoft-Bereich „Meine Apps“ verwenden, um die Anwendung in einem beliebigen Modus zu testen. Beim Klicken auf die Kachel „Freedcamp“ in „Meine Apps“ geschieht Folgendes: Wenn Sie die Anwendung im SP-Modus konfiguriert haben, werden Sie zum Initiieren des Anmeldeflows zur Anmeldeseite der Anwendung weitergeleitet. Wenn Sie die Anwendung im IDP-Modus konfiguriert haben, sollten Sie automatisch bei der Freedcamp-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Freedcamp können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-aad)
