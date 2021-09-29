---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Moxtra | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Moxtra konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/05/2019
ms.author: jeedes
ms.openlocfilehash: 7f63ce6cd120f0af7c40fc86eaa5dba531b06779
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124742109"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-moxtra"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Moxtra

In diesem Tutorial erfahren Sie, wie Sie Moxtra in Azure Active Directory (Azure AD) integrieren. Die Integration von Moxtra in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf Moxtra hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Moxtra anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Moxtra-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Moxtra unterstützt **SP-initiiertes** einmaliges Anmelden.

> [!NOTE]
> Der Bezeichner dieser Anwendung ist ein fester Zeichenfolgenwert, daher kann in einem Mandanten nur eine Instanz konfiguriert werden.

## <a name="adding-moxtra-from-the-gallery"></a>Hinzufügen von Moxtra aus dem Katalog

Zum Konfigurieren der Integration von Moxtra in Azure AD müssen Sie Moxtra aus dem Katalog der Liste der verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Moxtra** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Moxtra** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.


## <a name="configure-and-test-azure-ad-single-sign-on-for-moxtra"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Moxtra

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Moxtra mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Moxtra eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Moxtra die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Moxtra](#configure-moxtra-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines Moxtra-Testbenutzers](#create-moxtra-test-user)** , um in Moxtra eine Entsprechung von B. Simon zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **Moxtra** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Bearbeitungs- bzw. Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Geben Sie im Abschnitt **Grundlegende SAML-Konfiguration** die Werte für die folgenden Felder ein:

    Geben Sie im Textfeld **Anmelde-URL** eine URL ein: `https://www.moxtra.com/service/#login`.

1. Die Moxtra-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Daher müssen Sie Ihrer Konfiguration der SAML-Tokenattribute benutzerdefinierte Attributzuordnungen hinzufügen. Der folgende Screenshot zeigt die Liste der Standardattribute. Klicken Sie auf das Symbol **Bearbeiten**, um das Dialogfeld „Benutzerattribute“ zu öffnen.

    ![image](common/edit-attribute.png)

1. Darüber hinaus wird von der Moxtra-Anwendung erwartet, dass in der SAML-Antwort noch einige weitere Attribute zurückgegeben werden. Führen Sie im Dialogfeld „Benutzerattribute“ im Abschnitt „Benutzeransprüche“ die folgenden Schritte aus, um das SAML-Tokenattribut wie in der folgenden Tabelle gezeigt hinzuzufügen:

    | Name | Quellattribut|
    | ------------------- | -------------------- |    
    | firstname | user.givenname |
    | lastname | user.surname |
    | idpid    | < Azure AD-Bezeichner >

    > [!Note]
    > Bei dem Wert des Attributs **idpid** handelt es sich nicht um den tatsächlichen Wert. Den tatsächlichen Wert finden Sie in Schritt 8 im Abschnitt **Moxtra einrichten**. 

    1. Klicken Sie auf **Neuen Anspruch hinzufügen**, um das Dialogfeld **Benutzeransprüche verwalten** zu öffnen.

    1. Geben Sie im Textfeld **Name** den für die Zeile angezeigten Attributnamen ein.

    1. Lassen Sie den **Namespace** leer.

    1. Wählen Sie „Source“ als **Attribut** aus.

    1. Geben Sie in der Liste **Quellattribut** den für diese Zeile angezeigten Attributwert ein.

    1. Klicken Sie auf **OK**.

    1. Klicken Sie auf **Speichern**.

1. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** zum Eintrag **Zertifikat (Base64)** . Wählen Sie **Herunterladen** aus, um das Zertifikat herunterzuladen, und speichern Sie es auf Ihrem Computer.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

1. Kopieren Sie im Abschnitt **Moxtra einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Moxtra gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Moxtra** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.

   ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Link „Benutzer hinzufügen“](common/add-assign-user.png)

1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** die entsprechende Rolle für den Benutzer in der Liste aus, und klicken Sie dann im unteren Bildschirmbereich auf die Schaltfläche **Auswählen**.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-moxtra-sso"></a>Konfigurieren des einmaligen Anmeldens für Moxtra

1. Melden Sie sich in einem anderen Webbrowserfenster bei der Moxtra-Unternehmenswebsite als Administrator an.

2. Klicken Sie auf der Symbolleiste auf der linken Seite auf **Administratorkonsole > Einmalige SAML-Anmeldung** und anschließend auf **Neu**.
   
    ![Screenshot: Seite „SAML Single Sign-on“ (SAML-SSO) mit der Option zum Erstellen einer neuen einmaligen SAML-Anmeldung](./media/moxtra-tutorial/tutorial_moxtra_06.png) 

3. Führen Sie auf der Seite **SAML** die folgenden Schritte aus:
   
    ![Screenshot: Seite „SAML“, auf der Sie die beschriebenen Werte eingeben können](./media/moxtra-tutorial/tutorial_moxtra_08.png)   
 
    a. Geben Sie im Textfeld **Name** einen Namen für die Konfiguration ein (Beispiel: *SAML*). 
  
    b. Fügen Sie in das Textfeld **IdP Entity ID** (IdP-Entitäts-ID) den Wert für **Azure AD-Bezeichner** ein, den Sie aus dem Azure-Portal kopiert haben. 
 
    c. Fügen Sie in das Textfeld **Login URL** (Anmelde-URL) den Wert der **Anmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben. 
 
    d. Geben Sie in das Textfeld **AuthnContextClassRef** die Zeichenfolge **urn:oasis:names:tc:SAML:2.0:ac:classes:Password** ein. 
 
    e. Geben Sie in das Textfeld **NameID Format** (NameID-Format) als Format **urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress** ein. 
 
    f. Öffnen Sie das Zertifikat, das Sie aus dem Azure-Portal heruntergeladen haben, im Editor, kopieren Sie den Inhalt, und fügen Sie ihn anschließend in das Textfeld **Zertifikat** ein.    
 
    g. Geben Sie im Textfeld für die SAML-E-Mail-Domäne Ihre SAML-E-Mail-Domäne ein.    
  
    >[!NOTE]
    >Klicken Sie unten auf „**i**“, um die Schritte zum Überprüfen der Domäne anzuzeigen.

    h. Klicken Sie auf **Aktualisieren**.

### <a name="create-moxtra-test-user"></a>Erstellen eines Moxtra-Testbenutzers

In diesem Abschnitt wird in Moxtra ein Benutzer namens B. Simon erstellt.

**Führen Sie die folgenden Schritte aus, um in Moxtra einen Benutzer namens B. Simon zu erstellen:**

1. Melden Sie sich bei Ihrer Moxtra-Unternehmenswebsite als Administrator an.

1. Klicken Sie auf der Symbolleiste auf der linken Seite auf **Administratorkonsole > Benutzerverwaltung** und dann auf **Benutzer hinzufügen**.
   
    ![Screenshot: Seite „User Management“ (Benutzerverwaltung), auf der die Option „Add User“ (Benutzer hinzufügen) ausgewählt ist](./media/moxtra-tutorial/tutorial_moxtra_10.png) 

1. Führen Sie im Dialogfeld **Add User** die folgenden Schritte aus:
  
    a. Geben Sie **B** in das Textfeld **First Name** (Vorname) ein.
  
    b. Geben Sie in das Textfeld **Nachname** den Namen **Simon** ein.
  
    c. Geben Sie im Textfeld **E-Mail** die E-Mail-Adresse von B. Simon aus dem Azure-Portal ein.
  
    d. Geben Sie im Textfeld **Division** den Text **Dev** ein.
  
    e. Geben Sie im Textfeld **Abteilung** den Text **IT** ein.
  
    f. Wählen Sie **Administrator** aus.
  
    g. Klicken Sie auf **Hinzufügen**.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „Moxtra“ klicken, sollten Sie automatisch bei der Moxtra-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Liste mit den Tutorials zur Integration von SaaS-Apps in Azure Active Directory](./tutorial-list.md)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Was ist der bedingte Zugriff in Azure Active Directory?](../conditional-access/overview.md)

- [Moxtra mit Azure AD ausprobieren](https://aad.portal.azure.com/)