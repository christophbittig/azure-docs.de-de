---
title: 'Tutorial: Azure AD SSO-Integration mit EY GlobalOne'
description: Hier erfahren Sie, wie Sie einmaliges Anmelden zwischen Azure Active Directory und EY GlobalOne konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/22/2021
ms.author: jeedes
ms.openlocfilehash: 497c72f53b183d7ce5a6c49d4fcee07dc46cdbd7
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132285480"
---
# <a name="tutorial-azure-ad-sso-integration-with-ey-globalone"></a>Tutorial: Azure AD SSO-Integration mit EY GlobalOne

In diesem Tutorial erfahren Sie, wie Sie EY GlobalOne in Azure Active Directory (Azure AD) integrieren. Die Integration von EY GlobalOne in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf EY GlobalOne hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei EY GlobalOne anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein EY GlobalOne-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.
* EY GlobalOne unterstützt **SP- und IDP** veranlasstes einmaliges Anmelden. 
* EY GlobalOne unterstützt die **Just-In-Time**-Benutzerbereitstellung.

## <a name="add-ey-globalone-from-the-gallery"></a>Hinzufügen von EY GlobalOne aus dem Katalog

Zum Konfigurieren der Integration von EY GlobalOne in Azure AD müssen Sie EY GlobalOne aus dem Katalog zu Ihrer Liste der verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **EY GlobalOne** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **EY GlobalOne** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-ey-globalone"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für EY GlobalOne

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit EY GlobalOne mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in EY GlobalOne eingerichtet werden.

Um Azure AD SSO mit EY GlobalOne zu konfigurieren und testen, führen Sie die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon für das einmalige Anmelden von Azure AD zu aktivieren.
1. **[Konfigurieren sie das SSO von EY GlobalOne](#configure-ey-globalone-sso)** , um die SSO-Einstellungen auf der Anwendungsseite zu konfigurieren.
    1. **[Erstellen eines EY GlobalOne-Testbenutzers](#create-ey-globalone-test-user)** , um eine Entsprechung von B. Simon in EY GlobalOne zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **EY GlobalOne** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Im Abschnitt **SAML-Basiskonfiguration** ist die Anwendung vorkonfiguriert und die notwendigen URLs sind bereits mit Azure vorausgefüllt. Der Benutzer muss die Konfiguration speichern, indem er auf die Schaltfläche **Speichern** klickt.

1. Die EY GlobalOne-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Daher müssen Sie Ihrer Konfiguration der SAML-Tokenattribute benutzerdefinierte Attributzuordnungen hinzufügen. Der folgende Screenshot zeigt die Liste der Standardattribute. Klicken Sie auf das Symbol **Bearbeiten**, um das Dialogfeld Benutzerattribute zu öffnen.

    ![Screenshot: Abschnitt „User Attributes“ (Benutzerattribute) mit dem Symbol „Bearbeiten“ ausgewählt](common/edit-attribute.png)

1. Darüber hinaus wird von der EY GlobalOne-Anwendung erwartet, dass in der SAML-Antwort noch einige weitere Attribute zurückgegeben werden. Führen Sie im Dialogfeld **Benutzerattribute** im Abschnitt **Benutzeransprüche** die folgenden Schritte aus, um das SAML-Tokenattribut wie in der folgenden Tabelle gezeigt hinzuzufügen:

    | Name | Quellattribut|
    | ---------------| --------------- |
    | FirstName | user.givenname |
    | LastName | user.surname |
    | Email | user.mail |
    | Company | `<YOUR COMPANY NAME>` |

    a. Klicken Sie auf **Neuen Anspruch hinzufügen**, um das Dialogfeld **Benutzeransprüche verwalten** zu öffnen.

    ![Screenshot: Abschnitt „Benutzeransprüche“ mit den hervorgehobenen Aktionen „Neuen Anspruch hinzufügen“ und „Speichern“](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Geben Sie im Textfeld **Name** den für die Zeile angezeigten Attributnamen ein.

    c. Lassen Sie den **Namespace** leer.

    d. Wählen Sie „Source“ als **Attribut** aus.

    e. Geben Sie in der Liste **Quellattribut** den für diese Zeile angezeigten Attributwert ein.

    f. Klicken Sie auf **OK**.

    g. Klicken Sie auf **Speichern**.

1. Suchen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** den Eintrag **Zertifikat (Rohdaten)** . Wählen Sie **Herunterladen** aus, um das Zertifikat herunterzuladen, und speichern Sie es auf Ihrem Computer.

   ![Downloadlink für das Zertifikat](common/certificateraw.png)

1. Kopieren Sie im Abschnitt **EY GlobalOne einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

   ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

In diesem Abschnitt erstellen Sie im Azure-Portal einen Testbenutzer mit dem Namen B. Simon.

1. Wählen Sie im linken Bereich des Microsoft Azure-Portals **Azure Active Directory** > **Benutzer** > **Alle Benutzer** aus.
1. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.
1. Führen Sie unter den Eigenschaften für **Benutzer** die folgenden Schritte aus:
   1. Geben Sie im Feld **Name** die Zeichenfolge `B. Simon` ein.  
   1. Geben Sie im Feld **Benutzername** die Zeichenfolge username@companydomain.extension ein. Beispiel: `B. Simon@contoso.com`.
   1. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**, und notieren Sie sich den Wert aus dem Feld **Kennwort**.
   1. Klicken Sie auf **Erstellen**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf EY GlobalOne gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **EY GlobalOne** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann am unteren Bildschirmrand auf die Schaltfläche **Auswählen**.
1. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** die entsprechende Rolle für den Benutzer in der Liste aus, und klicken Sie dann im unteren Bildschirmbereich auf die Schaltfläche **Auswählen**.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-ey-globalone-sso"></a>Konfigurieren von EY GlobalOne SSO

Zum Konfigurieren des einmaligen Anmeldens aufseiten von **EY GlobalOne** müssen Sie das heruntergeladene **Zertifikat (Rohdaten)** und die kopierten URLs aus dem Azure-Portal an das [Supportteam von EY GlobalOne](mailto:globalone.support@ey.com) senden. Es führt die Einrichtung durch, damit die SAML-SSO-Verbindung auf beiden Seiten richtig festgelegt ist.

### <a name="create-ey-globalone-test-user"></a>Erstellen eines EY GlobalOne-Testbenutzers

In diesem Abschnitt wird in EY GlobalOne ein Benutzer mit dem Namen Britta Simon erstellt. EY GlobalOne unterstützt die Just-In-Time-Benutzerbereitstellung (standardmäßig aktiviert). Für Sie steht in diesem Abschnitt kein Aktionselement zur Verfügung. Ist ein Benutzer noch nicht in EY GlobalOne vorhanden, wird nach der Authentifizierung ein neuer Benutzer erstellt.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

#### <a name="sp-initiated"></a>SP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dies wird Sie zur EY GlobalOne Anmelde-URL umleiten, wo Sie den Anmeldevorgang veranlassen können.  

* Gehen Sie direkt zur EY GlobalOne Anmelde-URL und veranlassen Sie von dort aus den Anmeldevorgang.

#### <a name="idp-initiated"></a>IDP-initiiert:

* Klicken Sie auf **Diese Anwendung testen** im Azure Portal und Sie sollten automatisch bei EY GlobalOne angemeldet werden, für das Sie das SSO eingerichtet haben. 

Sie können auch den Microsoft-Bereich „Meine Apps“ verwenden, um die Anwendung in einem beliebigen Modus zu testen. Wenn Sie auf die Kachel EY GlobalOne in My Apps klicken, werden Sie, wenn sie im SP-Modus konfiguriert ist, zur Anmeldeseite der Anwendung umgeleitet, um den Anmeldevorgang zu veranlassen, und wenn sie im IDP-Modus konfiguriert ist, sollten Sie automatisch bei EY GlobalOne angemeldet werden, für das Sie SSO eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie EY GlobalOne konfiguriert haben, können Sie die Sitzungskontrolle durchsetzen, die vor Exfiltrieren und Infiltrieren der sensiblen Daten Ihres Unternehmens in Echtzeit schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-aad)
