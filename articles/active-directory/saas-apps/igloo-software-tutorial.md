---
title: 'Tutorial: Azure Active Directory-Integration mit Igloo Software | Microsoft Docs'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Igloo Software konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/06/2019
ms.author: jeedes
ms.openlocfilehash: 243c00ef686f0259a20f564f1641b9399a6691d7
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124749904"
---
# <a name="tutorial-azure-active-directory-integration-with-igloo-software"></a>Tutorial: Azure Active Directory-Integration mit Igloo Software

In diesem Tutorial erfahren Sie, wie Sie Igloo Software in Azure Active Directory (Azure AD) integrieren.
Die Integration von Igloo Software in Azure AD bietet die folgenden Vorteile:

* Sie können in Azure AD steuern, wer Zugriff auf Igloo Software hat.
* Sie können es Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei Igloo Software anzumelden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit Igloo Software konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Wenn Sie keine Azure AD-Umgebung besitzen, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/) eine einmonatige Testversion anfordern.
* Ein Igloo Software-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Igloo Software unterstützt **SP-initiiertes** einmaliges Anmelden.
* Igloo Software unterstützt die **Just-in-Time**-Benutzerbereitstellung.

## <a name="adding-igloo-software-from-the-gallery"></a>Hinzufügen von Igloo Software aus dem Katalog

Zum Konfigurieren der Integration von Igloo Software in Azure AD müssen Sie Igloo Software aus dem Katalog zur Liste der verwalteten SaaS-Apps hinzufügen.

**Um Igloo Software aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **Igloo Software** ein, wählen Sie im Ergebnisbereich **Igloo Software** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

     ![Igloo Software in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

Dieser Abschnitt veranschaulicht anhand eines Testbenutzers namens **Britta Simon**, wie das einmalige Anmelden von Azure AD mit Igloo Software konfiguriert und getestet wird.
Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Igloo Software eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens in Azure AD bei Igloo Software müssen Sie die folgenden Schritte ausführen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)** , um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für Igloo Software](#configure-igloo-software-single-sign-on)**, um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Erstellen eines Igloo Software-Testbenutzers](#create-igloo-software-test-user)**, um ein Pendant von Britta Simon in Igloo Software zu erhalten, das mit ihrer Darstellung in Azure AD verknüpft ist
6. **[Testen der einmaligen Anmeldung](#test-single-sign-on)** , um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD mit Igloo Software die folgenden Schritte aus:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **Igloo Software** die Option **Einmaliges Anmelden** aus.

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Symbol **Bearbeiten**, um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    ![SSO-Informationen zur Domäne und zu den URLs für Igloo Software](common/sp-identifier-reply.png)

    a. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<company name>.igloocommmunities.com`

    b. Geben Sie im Feld **Bezeichner** eine URL im folgenden Format ein: `https://<company name>.igloocommmunities.com/saml.digest`.

    c. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://<company name>.igloocommmunities.com/saml.digest`

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch die tatsächliche Anmelde-URL, den tatsächlichen Bezeichner und die tatsächliche Antwort-URL. Wenden Sie sich an das [Clientsupportteam von Igloo Software](https://www.igloosoftware.com/services/support), um diese Werte zu erhalten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

5. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um das Ihrer Anforderung entsprechende **Zertifikat (Base64)** aus den angegebenen Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

6. Kopieren Sie im Abschnitt **Igloo Software einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

    a. Anmelde-URL

    b. Azure AD-Bezeichner

    c. Abmelde-URL

### <a name="configure-igloo-software-single-sign-on&quot;></a>Konfigurieren des einmaligen Anmeldens für Igloo Software

1. Melden Sie sich in einem anderen Webbrowserfenster bei der Igloo Software-Unternehmenswebsite als Administrator an.

2. Wechseln Sie zum **Control Panel**.

     ![Systemsteuerung](./media/igloo-software-tutorial/ic799949.png &quot;Systemsteuerung")

3. Klicken Sie auf der Registerkarte **Membership** auf **Sign In Settings**.

    ![Anmeldeeinstellungen](./media/igloo-software-tutorial/ic783968.png "Anmeldeeinstellungen")

4. Klicken Sie im Abschnitt "SAML Configuration" auf **Configure SAML Authentication**.

    ![SAML-Konfiguration](./media/igloo-software-tutorial/ic783969.png "SAML-Konfiguration")

5. Führen Sie im Abschnitt **General Configuration** die folgenden Schritte aus:

    ![Allgemeine Konfiguration](./media/igloo-software-tutorial/ic783970.png "Allgemeine Konfiguration")

    a. Geben Sie in das Textfeld **Connection Name** einen benutzerdefinierten Namen für Ihre Konfiguration ein.

    b. Fügen Sie in das Textfeld **IdP Login URL** (IdP-Anmelde-URL) den Wert der **Anmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    c. Fügen Sie in das Textfeld **IdP Logout URL** (IdP-Abmelde-URL) den Wert der **Abmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    d. Wählen Sie für **Abmeldeantwort und HTTP-Anforderungstyp** die Option **POST** aus.

    e. Öffnen Sie das **Base64**-codierte Zertifikat im Editor, das Sie aus dem Azure-Portal heruntergeladen haben, kopieren Sie den Inhalt des Zertifikats in die Zwischenablage, und fügen Sie ihn anschließend in das Textfeld **Öffentliches Zertifikat** ein.

6. Führen Sie in der **Response and Authentication Configuration** die folgenden Schritte aus:

    ![Antwort- und Authentifizierungskonfiguration](./media/igloo-software-tutorial/IC783971.png "Antwort- und Authentifizierungskonfiguration")
  
    a. Wählen Sie als **Identity Provider** den Wert **Microsoft ADFS** aus.

    b. Wählen Sie als **Identifier Type** den Typ **Email Address** aus. 

    c. Geben Sie in das Textfeld **Email Attribute** den Wert **emailaddress** ein.

    d. Geben Sie in das Textfeld **First Name Attribute** den Wert **givenname** ein.

    e. Geben Sie in das Textfeld **Last Name Attribute** den Wert **surname** ein.

7. Führen Sie die folgenden Schritte aus, um die Konfiguration abzuschließen:

    ![Benutzererstellung beim Anmelden](./media/igloo-software-tutorial/IC783972.png "Benutzererstellung beim Anmelden") 

    a. Wählen Sie als **User creation on Sign in** den Wert **Create a new user in your site when they sign in** aus.

    b. Wählen Sie als **Sign in Settings** den Wert **Use SAML button on "Sign in" screen** aus.

    c. Klicken Sie auf **Speichern**.

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im Azure-Portal.

1. Wählen Sie im Azure-Portal im linken Bereich die Option **Azure Active Directory**, **Benutzer** und dann **Alle Benutzer** aus.

    ![Links „Benutzer und Gruppen“ und „Alle Benutzer“](common/users.png)

2. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.

    ![Schaltfläche „Neuer Benutzer“](common/new-user.png)

3. Führen Sie in den Benutzereigenschaften die folgenden Schritte aus.

    ![Dialogfeld „Benutzer“](common/user-properties.png)

    a. Geben Sie im Feld **Name** den Namen **BrittaSimon** ein.
  
    b. Geben Sie im Feld **Benutzername** **brittasimon@yourcompanydomain.extension** ein.  
    Zum Beispiel, BrittaSimon@contoso.com

    c. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**, und notieren Sie sich den Wert, der im Feld „Kennwort“ angezeigt wird.

    d. Klicken Sie auf **Erstellen**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Igloo Software gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** > **Igloo Software** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **Igloo Software** aus.

    ![Igloo Software-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen**, und wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **Britta Simon** aus, und klicken Sie dann unten im Bildschirm auf die Schaltfläche **Auswählen**.

6. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** in der Liste die entsprechende Rolle für den Benutzer aus, und klicken Sie dann unten auf dem Bildschirm auf **Auswählen**.

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

### <a name="create-igloo-software-test-user"></a>Erstellen eines Testbenutzers für Igloo Software

Für das Konfigurieren der Benutzerbereitstellung in Igloo Software steht kein Aktionselement zur Verfügung.  

Wenn ein zugewiesener Benutzer versucht, sich über den Zugriffsbereich bei Igloo Software anzumelden, überprüft Igloo Software, ob der Benutzer vorhanden ist.  Ist noch kein Benutzerkonto verfügbar, wird es von Igloo Software automatisch erstellt.

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „Igloo Software“ klicken, sollten Sie automatisch bei der Igloo Software-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](./tutorial-list.md)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Was ist bedingter Zugriff?](../conditional-access/overview.md)