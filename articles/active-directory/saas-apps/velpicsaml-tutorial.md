---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Velpic SAML | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Velpic SAML konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/02/2021
ms.author: jeedes
ms.openlocfilehash: 5d8ded66ff7fd04c699a2dd48dc4391eb91ea9ee
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132282403"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-velpic-saml"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Velpic SAML

In diesem Tutorial erfahren Sie, wie Sie Velpic SAML in Azure Active Directory (Azure AD) integrieren. Die Integration von Velpic SAML in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf Velpic SAML hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Velpic SAML anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Velpic SAML-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Velpic SAML unterstützt **SP**-initiiertes einmaliges Anmelden.
* Velpic SAML unterstützt [Automatisierte Benutzerbereitstellung](velpic-provisioning-tutorial.md).

## <a name="adding-velpic-saml-from-the-gallery"></a>Hinzufügen von Velpic SAML aus dem Katalog

Zum Konfigurieren der Integration von Velpic SAML in Azure AD müssen Sie Velpic SAML aus dem Katalog der Liste der verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Velpic SAML** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Velpic SAML** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.    

## <a name="configure-and-test-azure-ad-sso-for-velpic-saml"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Velpic SAML

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Velpic SAML mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Velpic SAML eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Velpic SAML die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Velpic SAML](#configure-velpic-saml-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines Velpic SAML-Testbenutzers](#create-velpic-saml-test-user)** , um eine Entsprechung von B. Simon in Velpic SAML zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Velpic SAML** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    a. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<sub-domain>.velpicsaml.net`.

    b. Geben Sie im Textfeld **Bezeichner (Entitäts-ID)** eine URL im folgenden Format ein: `https://auth.velpic.com/saml/v2/<entity-id>/login`.

    > [!NOTE]
    > Beachten Sie, dass die Anmelde-URL vom Velpic SAML-Team bereitgestellt wird und dass der Bezeichnerwert zur Verfügung steht, wenn Sie das SSO-Plug-In auf der Velpic SAML-Seite konfigurieren. Sie müssen diesen Wert von der Velpic SAML-Anwendungsseite kopieren und hier einfügen.

1. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** zu **Verbundmetadaten-XML**, und wählen Sie **Herunterladen** aus, um das Zertifikat herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

1. Kopieren Sie im Abschnitt **Velpic SAML einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Velpic SAML gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Velpic SAML** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** die entsprechende Rolle für den Benutzer in der Liste aus, und klicken Sie dann im unteren Bildschirmbereich auf die Schaltfläche **Auswählen**.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-velpic-saml-sso"></a>Konfigurieren des einmaligen Anmeldens für Velpic SAML

1. Wenn Sie die Konfiguration in Velpic SAML automatisieren möchten, müssen Sie die **Browsererweiterung „Meine Apps“ für die sichere Anmeldung** installieren, indem Sie auf **Erweiterung installieren** klicken.

    ![Erweiterung „Meine Apps“](common/install-myappssecure-extension.png)

2. Klicken Sie nach dem Hinzufügen der Erweiterung zum Browser auf **Velpic SAML einrichten**, um zur Anwendung Velpic SAML weitergeleitet zu werden. Geben Sie dort die Administratoranmeldeinformationen ein, um sich bei Velpic SAML anzumelden. Die Browsererweiterung konfiguriert die Anwendung automatisch für Sie und automatisiert die Schritte 3 bis 8.

    ![Einrichtungskonfiguration](common/setup-sso.png)

3. Wenn Sie Velpic SAML manuell einrichten möchten, melden Sie sich in einem neuen Webbrowserfenster bei der Velpic SAML-Unternehmenswebsite als Administrator an, und führen Sie die folgenden Schritte aus:

4. Klicken Sie auf die Registerkarte **Verwalten**, und wechseln Sie zum Abschnitt **Integration**. Hier müssen Sie auf die Schaltfläche **Plug-Ins** klicken, um ein neues Plug-In für die Anmeldung zu erstellen.

    ![Screenshot: Seite „Integration“, auf der Sie Plug-Ins auswählen können](./media/velpicsaml-tutorial/plugin.png)

5. Klicken Sie auf die Schaltfläche **Plug-In hinzufügen**.
    
    ![Screenshot: Ausgewählte Schaltfläche „Add Plugin“ (Plug-In hinzufügen)](./media/velpicsaml-tutorial/add-button.png)

6. Klicken Sie auf der Seite „Plug-In hinzufügen“ auf die Kachel **SAML**.
    
    ![Screenshot: Seite „Add Plugin“ (Plug-In hinzufügen), auf der „SAML“ ausgewählt ist](./media/velpicsaml-tutorial/integration.png)

7. Geben Sie den Namen des neuen SAML-Plug-Ins ein, und klicken Sie auf die Schaltfläche **Hinzufügen**.

    ![Screenshot: Dialogfeld „Add new SAML plugin“ (Neues SAML-Plug-In hinzufügen), in dem „Azure AD“ eingegeben ist](./media/velpicsaml-tutorial/new-plugin.png)

8. Geben Sie die Details wie folgt ein:

    ![Screenshot: Seite „Azure AD“, auf der Sie die beschriebenen Werte eingeben können](./media/velpicsaml-tutorial/details.png)

    a. Geben Sie im Textfeld **Name** den Namen des SAML-Plug-Ins ein.

    b. Fügen Sie im Textfeld **Issuer URL** (Aussteller-URL) den **Azure AD-Bezeichner** ein, den Sie im Azure-Portal im Fenster **Anmeldung konfigurieren** kopiert haben.

    c. Laden Sie in der **Konfiguration der Anbietermetadaten** die XML-Metadatendatei hoch, die Sie aus dem Azure-Portal heruntergeladen haben.

    d. Sie können auch die SAML-Just-in-Time-Bereitstellung aktivieren, indem Sie das Kontrollkästchen **Neue Benutzer automatisch erstellen** aktivieren. Wenn ein Benutzer nicht in Velpic vorhanden und dieses Flag nicht aktiviert ist, tritt bei der Anmeldung über Azure ein Fehler auf. Wenn das Flag aktiviert ist, wird der Benutzer zum Zeitpunkt der Anmeldung automatisch in Velpic bereitgestellt. 

    e. Kopieren Sie die **SSO-URL** aus dem Textfeld, und fügen Sie sie im Azure-Portal ein.
    
    f. Klicken Sie auf **Speichern**.

### <a name="create-velpic-saml-test-user"></a>Erstellen eines Velpic SAML-Testbenutzers

Dieser Schritt ist in der Regel nicht erforderlich, da die Anwendung die Just-in-Time-Benutzerbereitstellung unterstützt. Wenn die automatische Benutzerbereitstellung nicht aktiviert ist, kann die manuelle Erstellung von Benutzern wie unten beschrieben durchgeführt werden.

Melden Sie sich als Administrator bei Ihrer Velpic SAML-Unternehmenswebsite an, und führen Sie die folgenden Schritte aus:
    
1. Klicken Sie auf die Registerkarte „Verwalten“, und wechseln Sie zum Abschnitt „Benutzer“. Klicken Sie dann auf die Schaltfläche „Neu“, um Benutzer hinzuzufügen.

    ![Benutzer hinzufügen](./media/velpicsaml-tutorial/new-user.png)

2. Führen Sie im Dialogfeld **Neuen Benutzer erstellen** die folgenden Schritte aus.

    ![Benutzer](./media/velpicsaml-tutorial/create-user.png)
    
    a. Geben Sie im Textfeld **First Name** (Vorname) den Vornamen ein, z. B. „B“.

    b. Geben Sie im Textfeld **Last Name** (Nachname) den Nachnamen ein, z. B. Simon.

    c. Geben Sie im Textfeld **User Name** (Benutzername) den Benutzernamen „B. Simon“ ein.

    d. Geben Sie im Textfeld **Email** (E-Mail) die E-Mail-Adresse des Kontos ein, z. B. B.Simon@contoso.com.

    e. Die übrigen Informationen sind optional und können nach Bedarf ausgefüllt werden.
    
    f. Klicken Sie auf **SPEICHERN**.

> [!NOTE]
> Außerdem unterstützt Velpic SAML die automatische Benutzerbereitstellung. Weitere Informationen zum Konfigurieren der automatischen Benutzerbereitstellung finden Sie [hier](./velpic-provisioning-tutorial.md).

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mithilfe von „Meine Apps“.

1. Wenn Sie im Zugriffsbereich auf die Velpic SAML-Kachel klicken, wird die Anmeldeseite der Velpic SAML-Anwendung aufgerufen. Auf der Anmeldeseite wird Ihnen die Schaltfläche **Mit Azure AD anmelden** angezeigt.

    ![Screenshot: Dialogfeld „Learning Portal“ (Lernportal), in dem „Log In With Azure AD“ (Mit Azure AD anmelden) ausgewählt ist](./media/velpicsaml-tutorial/login.png)

1. Klicken Sie auf die Schaltfläche **Mit Azure AD anmelden**, um sich über das Azure AD-Konto bei Velpic anzumelden.

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Velpic SAML können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-aad)
