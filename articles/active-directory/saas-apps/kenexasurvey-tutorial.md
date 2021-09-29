---
title: 'Tutorial: Azure Active Directory-Integration mit IBM Kenexa Survey Enterprise | Microsoft Docs'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und IBM Kenexa Survey Enterprise konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/08/2019
ms.author: jeedes
ms.openlocfilehash: 67a69462e4648c6e2cdd32a89dfbe01e2f9ba40c
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124822156"
---
# <a name="tutorial-azure-active-directory-integration-with-ibm-kenexa-survey-enterprise"></a>Tutorial: Azure Active Directory-Integration mit IBM Kenexa Survey Enterprise

In diesem Tutorial erfahren Sie, wie Sie IBM Kenexa Survey Enterprise in Azure Active Directory (Azure AD) integrieren.
Die Integration von IBM Kenexa Survey Enterprise in Azure AD bietet die folgenden Vorteile:

* Sie können in Azure AD steuern, wer Zugriff auf IBM Kenexa Survey Enterprise hat.
* Sie können es Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei IBM Kenexa Survey Enterprise anzumelden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit IBM Kenexa Survey Enterprise konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Wenn Sie keine Azure AD-Umgebung besitzen, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/) eine einmonatige Testversion anfordern.
* Ein IBM Kenexa Survey Enterprise-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* IBM Kenexa Survey Enterprise unterstützt **IDP-initiiertes** einmaliges Anmelden.

## <a name="adding-ibm-kenexa-survey-enterprise-from-the-gallery"></a>Hinzufügen von IBM Kenexa Survey Enterprise aus dem Katalog

Zum Konfigurieren der Integration von IBM Kenexa Survey Enterprise in Azure AD müssen Sie IBM Kenexa Survey Enterprise aus dem Katalog der Liste der verwalteten SaaS-Apps hinzufügen.

**Um IBM Kenexa Survey Enterprise aus dem Katalog hinzuzufügen, führen Sie die folgenden Schritte aus:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld **IBM Kenexa Survey Enterprise** ein, wählen Sie im Ergebnisbereich **IBM Kenexa Survey Enterprise** aus, und klicken Sie dann auf **Hinzufügen**, um die Anwendung hinzuzufügen.

     ![IBM Kenexa Survey Enterprise in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit IBM Kenexa Survey Enterprise basierend auf einem Testbenutzer mit dem Namen **Britta Simon**.
Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in IBM Kenexa Survey Enterprise eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens in Azure AD bei IBM Kenexa Survey Enterprise müssen Sie die folgenden Bausteine ausführen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)** , um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für IBM Kenexa Survey Enterprise](#configure-ibm-kenexa-survey-enterprise-single-sign-on)**, um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Erstellen eines IBM Kenexa Survey Enterprise-Testbenutzers](#create-ibm-kenexa-survey-enterprise-test-user)**, um ein Gegenstück zu Britta Simon in IBM Kenexa Survey Enterprise zu erhalten, das mit ihrer Darstellung in Azure AD verknüpft ist
6. **[Testen der einmaligen Anmeldung](#test-single-sign-on)** , um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.

Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD mit IBM Kenexa Survey Enterprise die folgenden Schritte aus:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **IBM Kenexa Survey Enterprise** die Option **Einmaliges Anmelden** aus.

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Symbol **Bearbeiten**, um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** die folgenden Schritte aus:

    ![SSO-Informationen zur Domäne und zu den URLs für IBM Kenexa Survey Enterprise](common/idp-intiated.png)

    a. Geben Sie im Textfeld **Bezeichner** eine URL im folgenden Format ein: `https://surveys.kenexa.com/<companycode>`

    b. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://surveys.kenexa.com/<companycode>/tools/sso.asp`

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Aktualisieren Sie diese Werte mit dem eigentlichen Bezeichner und der Antwort-URL. Diese Werte erhalten Sie vom [Supportteam für den IBM Kenexa Survey Enterprise-Client](https://www.ibm.com/support/home/?lnk=fcw). Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

5. Die IBM Kenexa Survey Enterprise-Anwendung erwartet die SAML-Assertionen (Security Assertions Markup Language) in einem bestimmten Format. Hierzu müssen Sie der Konfiguration Ihrer SAML-Tokenattribute benutzerdefinierte Attributzuordnungen hinzufügen. Der Wert des Benutzer-ID-Anspruchs in der Antwort muss mit der SSO-ID übereinstimmen, die im Kenexa-System konfiguriert ist. Arbeiten Sie mit dem [IBM Kenexa Survey Enterprise-Supportteam](https://www.ibm.com/support/home/?lnk=fcw) zusammen, um die richtige Benutzer-ID in Ihrer Organisation als Internet Datagram Protocol (IDP) für das einmalige Anmelden zuzuordnen.

    Standardmäßig wird die Benutzer-ID in Azure AD als Wert für den Benutzerprinzipalnamen (User Principal Name, UPN) festgelegt. Sie können diesen Wert auf der Registerkarte **Benutzerattribute** ändern. Dies ist im folgenden Screenshot dargestellt. Die Integration funktioniert erst, nachdem Sie die Zuordnung korrekt durchgeführt haben.

    ![image](common/edit-attribute.png)

6. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um das Ihrer Anforderung entsprechende **Zertifikat (Base64)** aus den angegebenen Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

7. Kopieren Sie im Abschnitt **IBM Kenexa Survey Enterprise einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

    a. Anmelde-URL

    b. Azure AD-Bezeichner

    c. Abmelde-URL

### <a name="configure-ibm-kenexa-survey-enterprise-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für IBM Kenexa Survey Enterprise

Zum Konfigurieren des einmaligen Anmeldens aufseiten von **IBM Kenexa Survey Enterprise** müssen Sie das heruntergeladene **Zertifikat (Base64)** und die kopierten URLs aus dem Azure-Portal an das [Supportteam von IBM Kenexa Survey Enterprise](https://www.ibm.com/support/home/?lnk=fcw) senden. Es führt die Einrichtung durch, damit die SAML-SSO-Verbindung auf beiden Seiten richtig festgelegt ist.

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

In diesem Abschnitt ermöglichen Sie für Britta Simon das einmalige Anmelden von Azure, indem Sie ihr Zugriff auf IBM Kenexa Survey Enterprise gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** > **IBM Kenexa Survey Enterprise** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **IBM Kenexa Survey Enterprise** aus.

    ![Link „IBM Kenexa Survey Enterprise“ in der Liste „Anwendungen“](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen**, und wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **Britta Simon** aus, und klicken Sie dann unten im Bildschirm auf die Schaltfläche **Auswählen**.

6. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** in der Liste die entsprechende Rolle für den Benutzer aus, und klicken Sie dann unten auf dem Bildschirm auf **Auswählen**.

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

### <a name="create-ibm-kenexa-survey-enterprise-test-user"></a>Erstellen eines IBM Kenexa Survey Enterprise-Testbenutzers

In diesem Abschnitt erstellen Sie in IBM Kenexa Survey Enterprise einen Benutzer mit dem Namen Britta Simon.

Sie können mit dem [IBM Kenexa Survey Enterprise-Supportteam](https://www.ibm.com/support/home/?lnk=fcw) zusammenarbeiten, um im IBM Kenexa Survey Enterprise-System Benutzer zu erstellen und jeweils die SSO-ID für diese Benutzer zuzuordnen. Dieser SSO-ID-Wert sollte auch dem Benutzer-ID-Wert aus Azure AD zugeordnet werden. Sie können diese Standardeinstellung auf der Registerkarte **Attribut** ändern.

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „IBM Kenexa Survey Enterprise“ klicken, sollten Sie automatisch bei der IBM Kenexa Survey Enterprise-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](./tutorial-list.md)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Was ist bedingter Zugriff?](../conditional-access/overview.md)