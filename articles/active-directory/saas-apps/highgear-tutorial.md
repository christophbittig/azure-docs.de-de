---
title: 'Tutorial: Azure Active Directory-Integration mit HighGear | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und HighGear konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/16/2019
ms.author: jeedes
ms.openlocfilehash: ae50e0d9cee09cafe0b6a43cec1122832857b7fd
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131058975"
---
# <a name="tutorial-azure-active-directory-integration-with-highgear"></a>Tutorial: Azure Active Directory-Integration mit HighGear

In diesem Tutorial erfahren Sie, wie Sie HighGear in Azure Active Directory (Azure AD) integrieren. Die Integration von HighGear in Azure AD hat folgende Vorteile:

* Sie können in Azure AD steuern, wer Zugriff auf HighGear haben soll.
* Sie können Benutzern ermöglichen, sich mit ihrem Azure AD-Konto automatisch bei HighGear anzumelden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).
Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit HighGear konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über keine Azure AD-Umgebung verfügen, erhalten Sie [hier](https://azure.microsoft.com/pricing/free-trial/) eine einmonatige Testversion.
* Ein HighGear-System mit einer Enterprise- oder Unlimited-Lizenz.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial erfahren Sie, wie Sie das einmalige Anmelden von Azure AD konfigurieren und in einer Testumgebung testen.

* HighGear unterstützt **SP- und IdP-initiiertes** einmaliges Anmelden.

## <a name="adding-highgear-from-the-gallery"></a>Hinzufügen von HighGear aus dem Katalog

Um die Integration von HighGear in Azure AD zu konfigurieren, müssen Sie HighGear über den Katalog Ihrer Liste mit den verwalteten SaaS-Apps hinzufügen.

**Führen Sie die folgenden Schritte aus, um HighGear aus dem Katalog hinzuzufügen:**

1. Klicken Sie im **[Azure-Portal](https://portal.azure.com)** im linken Navigationsbereich auf das Symbol für **Azure Active Directory**.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie am oberen Rand des Dialogfelds auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie **HighGear** in das Suchfeld ein, wählen Sie im Ergebnisbereich die Option **HighGear** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

     ![HighGear in der Ergebnisliste](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens in Azure AD

In diesem Abschnitt erfahren Sie, wie Sie das einmalige Anmelden von Azure AD bei Ihrem HighGear-System mit einem Testbenutzer namens **Britta Simon** konfigurieren und testen.
Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Ihrem HighGear-System eingerichtet werden.

Führen Sie die folgenden Schritte aus, um das einmalige Anmelden von Azure AD mit Ihrem HighGear-System zu konfigurieren und zu testen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-single-sign-on)** , um Ihren Benutzern das Verwenden dieses Features zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für HighGear](#configure-highgear-single-sign-on)** , um die Einstellungen für einmaliges Anmelden auf der HighGear-Anwendungsseite zu konfigurieren.
3. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
4. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
5. **[Erstellen eines HighGear-Testbenutzers](#create-highgear-test-user)** , um in HighGear eine Entsprechung von Britta Simon zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist. 
6. **[Testen der einmaligen Anmeldung](#test-single-sign-on)** , um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens in Azure AD

In diesem Abschnitt erfahren Sie, wie Sie das einmalige Anmelden von Azure AD im Azure-Portal aktivieren.

Führen Sie die folgenden Schritte aus, um das einmalige Anmelden von Azure AD mit Ihrem HighGear-System zu konfigurieren:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **HighGear** die Option **Einmaliges Anmelden** aus.

    ![Konfigurieren des Links für einmaliges Anmelden](common/select-sso.png)

2. Wählen Sie im Dialogfeld **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

    ![Auswahlmodus für einmaliges Anmelden](common/select-saml-option.png)

3. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Symbol **Bearbeiten**, um das Dialogfeld **Grundlegende SAML-Konfiguration** zu öffnen.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    ![Screenshot der grundlegenden S A M L-Konfiguration, in der Sie I D und Antwort-U R L eingeben und „Speichern“ auswählen](common/idp-intiated.png)

    1. Fügen Sie im Textfeld **Bezeichner** die **ID der Dienstanbieterentität** ein, die in Ihrem HighGear-System auf der Seite mit den Einstellungen für einmaliges Anmelden angegeben ist.

       ![Das Feld mit der ID der Dienstanbieterentität](media/highgear-tutorial/service-provider-entity-id-field.png)

       > [!NOTE]
       > Sie müssen sich bei Ihrem HighGear-System anmelden, um auf die Seite mit den Einstellungen für einmaliges Anmelden zugreifen zu können. Bewegen Sie nach der Anmeldung Ihren Mauszeiger in HighGear auf die Verwaltungsregisterkarte, und klicken Sie auf das Menüelement für die Einstellungen für einmaliges Anmelden.

       ![Das Menüelement für die Einstellungen für einmaliges Anmelden](media/highgear-tutorial/single-sign-on-settings-menu-item.png)

    1. Fügen Sie im Textfeld **Antwort-URL** die **Assertionsverbraucherdienst-URL** ein, die in Ihrem HighGear-System auf der Seite mit den Einstellungen für einmaliges Anmelden angegeben ist.

       ![Das Feld mit der Assertionsverbraucherdienst-URL](media/highgear-tutorial/assertion-consumer-service-url-field.png)

    1. Klicken Sie auf **Zusätzliche URLs festlegen**, und führen Sie den folgenden Schritt aus, wenn Sie die Anwendung im **SP-initiierten Modus** konfigurieren möchten:

       ![Screenshot von „Zusätzliche U R Ls festlegen“, wo Sie eine Anmelde-U R L eingeben können](common/metadata-upload-additional-signon.png)

       Fügen Sie im Textfeld **URL für Anmeldung** die **ID der Dienstanbieterentität** ein, die in Ihrem HighGear-System auf der Seite mit den Einstellungen für einmaliges Anmelden angegeben ist. (Diese Entitäts-ID ist gleichzeitig die Basis-URL des HighGear-Systems, die bei der SP-initiierten Anmeldung verwendet wird.)

       ![Das Feld mit der ID der Dienstanbieterentität](media/highgear-tutorial/service-provider-entity-id-field.png)

       > [!NOTE]
       > Hierbei handelt es sich um Beispielwerte. Aktualisieren Sie diese Werte mit den tatsächlichen Werten für Bezeichner, Antwort-URL und Anmelde-URL, die in Ihrem HighGear-System auf der Seite mit den **Einstellungen für einmaliges Anmelden** angegeben sind. Sollten Sie Hilfe benötigen, wenden Sie sich an das [HighGear-Supportteam](mailto:support@highgear.com).

4. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um das **Zertifikat (Base64)** herunterzuladen, und speichern Sie es auf Ihrem Computer. Sie benötigen es in einem späteren Konfigurationsschritt.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

6. Beachten Sie im Abschnitt **HighGear einrichten** die folgenden URLs:

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

    1. Anmelde-URL: Diesen Wert benötigen Sie unter **Konfigurieren des einmaligen Anmeldens für HighGear** in Schritt 2.

    1. Azure AD-Bezeichner: Diesen Wert benötigen Sie unter **Konfigurieren des einmaligen Anmeldens für HighGear** in Schritt 3.

    1. Abmelde-URL: Diesen Wert benötigen Sie unter **Konfigurieren des einmaligen Anmeldens für HighGear** in Schritt 4.

### <a name="configure-highgear-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für HighGear

Melden Sie sich zum Konfigurieren des einmaligen Anmeldens für HighGear bei Ihrem HighGear-System an. Bewegen Sie nach der Anmeldung Ihren Mauszeiger in HighGear auf die Verwaltungsregisterkarte, und klicken Sie auf das Menüelement für die Einstellungen für einmaliges Anmelden.

![Das Menüelement für die Einstellungen für einmaliges Anmelden](media/highgear-tutorial/single-sign-on-settings-menu-item.png)

1. Geben Sie als **Name des Identitätsanbieters** eine kurze Beschreibung ein. Diese wird auf der Anmeldeseite auf der SSO-Schaltfläche von HighGear angezeigt. Beispiel: Azure AD

2. Fügen Sie in HighGear im Feld für die **URL für einmaliges Anmelden** den Wert aus dem Feld **Anmelde-URL** ein, das sich in Azure im **Abschnitt HighGear** einrichten befindet.

3. Fügen Sie in HighGear im Feld für die **Entitäts-ID des Identitätsanbieters** den Wert aus dem Feld **Azure AD-Bezeichner** ein, das sich in Azure im **Abschnitt HighGear** einrichten befindet.

4. Fügen Sie in HighGear im Feld für die **URL für einmaliges Abmelden** den Wert aus dem Feld **Abmelde-URL** ein, das sich in Azure im **Abschnitt HighGear** einrichten befindet.

5. Öffnen Sie das Zertifikat, das Sie aus dem Abschnitt **SAML-Signaturzertifikat** in Azure heruntergeladen haben, im Editor. Das Zertifikat muss im Format **Zertifikat (Base64)** vorliegen. Kopieren Sie den Inhalt des Zertifikats aus dem Editor, und fügen Sie ihn in HighGear in das Feld für das **Identitätsanbieterzertifikat** ein.

6. Fordern Sie beim [HighGear-Supportteam](mailto:support@highgear.com) per E-Mail Ihr HighGear-Zertifikat an. Gehen Sie gemäß den erhaltenen Anweisungen vor, um das Feld für das **HighGear-Zertifikat** und das Feld für das **HighGear-Zertifikatkennwort** auszufüllen.

7. Klicken Sie auf die Schaltfläche **Speichern**, um die HighGear-SSO-Konfiguration zu speichern.

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers 

Das Ziel dieses Abschnitts ist das Erstellen eines Testbenutzers namens Britta Simon im Azure-Portal.

1. Wählen Sie im Azure-Portal im linken Bereich die Option **Azure Active Directory**, **Benutzer** und dann **Alle Benutzer** aus.

    ![Links „Benutzer und Gruppen“ und „Alle Benutzer“](common/users.png)

2. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.

    ![Schaltfläche „Neuer Benutzer“](common/new-user.png)

3. Führen Sie in den Benutzereigenschaften die folgenden Schritte aus.

    ![Dialogfeld „Benutzer“](common/user-properties.png)

    1. Geben Sie im Feld **Name** den Namen **BrittaSimon** ein.
  
    1. Geben Sie im Feld **Benutzername** den Namen **brittasimon\@yourcompanydomain.extension** (z. B. BrittaSimon@contoso.com) ein.

    1. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**, und notieren Sie sich den Wert aus dem Kennwortfeld.

    1. Klicken Sie auf **Erstellen**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf HighGear gewähren.

1. Wählen Sie im Azure-Portal die Option **Unternehmensanwendungen** aus, und wählen Sie dann **Alle Anwendungen** > **HighGear** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Anwendungsliste **HighGear** aus.

    ![HighGear-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.

    ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen**, und wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Bereich „Zuweisung hinzufügen“](common/add-assign-user.png)

5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **Britta Simon** aus, und klicken Sie dann unten im Bildschirm auf die Schaltfläche **Auswählen**.

6. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** in der Liste die entsprechende Rolle für den Benutzer aus, und klicken Sie dann unten auf dem Bildschirm auf **Auswählen**.

7. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

### <a name="create-highgear-test-user"></a>Erstellen eines HighGear-Testbenutzers

Melden Sie sich bei Ihrem HighGear-System an, um einen Testbenutzer zu erstellen, mit dem Sie Ihre SSO-Konfiguration testen können.

1. Klicken Sie auf die Schaltfläche **Create New Contact** (Neuen Kontakt erstellen).

    ![Die Schaltfläche „Create New Contact“ (Neuen Kontakt erstellen)](media/highgear-tutorial/create-new-contact-button.png)

    In dem daraufhin angezeigten Menü können Sie die Art des zu erstellenden Kontakts auswählen.

2. Klicken Sie auf das Menüelement **Individual** (Einzelperson), um einen HighGear-Benutzer zu erstellen.

    Auf der rechten Seite wird ein Bereich eingeblendet, in dem Sie die Informationen für den neuen Benutzer eingeben können.  
    ![Das Formular für den neuen Kontakt](media/highgear-tutorial/new-contact-form.png)

3. Geben Sie im Feld **Name** einen Namen für den Kontakt ein. Beispiel: Britta Simon

4. Klicken Sie auf das Menü **More Options** (Weitere Optionen), und wählen Sie das Menüelement **Account Info** (Kontoinformationen) aus.

    ![Klicken auf das Menüelement „Account Info“ (Kontoinformationen)](media/highgear-tutorial/account-info-menu-item.png)

5. Legen Sie das Feld **Can Log In** (Anmeldung möglich) auf „Yes“ (Ja) fest.

    Das Feld **Enable Single Sign-On** (Einmaliges Anmelden aktivieren) wird automatisch ebenfalls auf „Yes“ (Ja) festgelegt.

6. Geben Sie im Feld **Single Sign-On User Id** (Benutzer-ID für einmaliges Anmelden) die ID des Benutzers ein. Beispiel: BrittaSimon@contoso.com

    Der Abschnitt mit den Kontoinformationen sollte nun in etwa wie folgt aussehen:  
    ![Fertig eingerichteter Abschnitt mit Kontoinformationen](media/highgear-tutorial/finished-account-info-section.png)

7. Klicken Sie zum Speichern des Kontakts am unteren Rand des Bereichs auf die Schaltfläche **Speichern**.

### <a name="test-single-sign-on"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „HighGear“ klicken, sollten Sie automatisch bei der HighGear-Anwendung angemeldet werden, für die Sie SSO eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="additional-resources"></a>Weitere Ressourcen

- [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](./tutorial-list.md)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Was ist bedingter Zugriff?](../conditional-access/overview.md)