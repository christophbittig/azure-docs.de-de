---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit BlogIn | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und BlogIn konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/20/2021
ms.author: jeedes
ms.openlocfilehash: c53810f2d313cbb2d4ca34f2776aa9123c2caf6c
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132349070"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-blogin"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit BlogIn

In diesem Tutorial erfahren Sie, wie Sie BlogIn in Azure Active Directory (Azure AD) integrieren. Die Integration von BlogIn in Azure AD ermöglicht Folgendes:

* Sie können in Azure AD steuern, wer Zugriff auf BlogIn haben soll.
* Sie können es Ihren Benutzern ermöglichen, sich mit ihrem Azure AD-Konto automatisch bei BlogIn anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* BlogIn-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* BlogIn unterstützt **SP- und IDP**-initiiertes einmaliges Anmelden.
* BlogIn unterstützt **Just-in-Time**-Benutzerbereitstellung.
* BlogIn unterstützt die [automatisierte Benutzerbereitstellung](blogin-provisioning-tutorial.md).

## <a name="add-blogin-from-the-gallery"></a>Hinzufügen von BlogIn aus dem Katalog

Zum Konfigurieren der Integration von BlogIn in Azure AD müssen Sie BlogIn aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **BlogIn** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich die Option **BlogIn** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-blogin"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für BlogIn

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit BlogIn mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in BlogIn eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit BlogIn die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für BlogIn](#configure-blogin-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
    1. **[Erstellen eines BlogIn-Testbenutzers](#create-blogin-test-user)** , um in BlogIn eine Entsprechung von B.Simon zu erhalten, die mit der Benutzerdarstellung in Azure AD verknüpft ist.
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **BlogIn** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus, wenn Sie die Anwendung im **IDP**-initiierten Modus konfigurieren möchten:

    a. Geben Sie im Textfeld **Bezeichner** eine URL im folgenden Format ein: `https://<SUBDOMAIN>.blogin.co/`

    b. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://<SUBDOMAIN>.blogin.co/sso/saml/callback`

1. Klicken Sie auf **Zusätzliche URLs festlegen**, und führen Sie den folgenden Schritt aus, wenn Sie die Anwendung im **SP-initiierten** Modus konfigurieren möchten:

    Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<SUBDOMAIN>.blogin.co/`

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Sie müssen diese Werte mit dem tatsächlichen Bezeichner, der Antwort-URL und der Anmelde-URL aktualisieren. Die genauen Werte für diese Felder erhalten Sie auf der Seite **Einstellungen** auf BlogIn (Registerkarte **Benutzerauthentifizierung** > **SSO und Benutzerbereitstellung konfigurieren**). Alternativ können Sie sich an das [Clientsupportteam von BlogIn](mailto:support@blogin.co) wenden, um diese Werte zu erhalten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

1. Die BlogIn-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Daher müssen Sie Ihrer Konfiguration der SAML-Tokenattribute benutzerdefinierte Attributzuordnungen hinzufügen. Der folgende Screenshot zeigt die Liste der Standardattribute.

    ![image](common/default-attributes.png)

1. Darüber hinaus erwartet die BlogIn-Anwendung, dass in der SAML-Antwort noch einige weitere Attribute zurückgegeben werden (siehe unten). Diese Attribute werden ebenfalls vorab aufgefüllt, Sie können sie jedoch nach Bedarf überprüfen.
    
    | Name | Quellattribut |
    | ------ | --------- |
    | title |user.jobtitle |
    

1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf die Schaltfläche „Kopieren“, um die **App-Verbundmetadaten-URL** zu kopieren, und speichern Sie sie auf Ihrem Computer.

    ![Downloadlink für das Zertifikat](common/copy-metadataurl.png)

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf BlogIn gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **BlogIn** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-blogin-sso"></a>Konfigurieren von BlogIn SSO

Um das einmalige Anmelden auf der **BlogIn**-Seite zu konfigurieren, melden Sie sich bei Ihrem BlogIn-Konto an, und befolgen Sie die folgenden Schritte:

1. Wechseln Sie zu **Einstellungen** > **Benutzerauthentifizierung** > **SSO und Benutzerbereitstellung konfigurieren**.
2. Ändern Sie auf dem nächsten Bildschirm den Status für das einmalige Anmelden in **Ein**, und wählen Sie einen benutzerdefinierten Namen für die Schaltfläche zur SSO-Anmeldung, die auf dem Anmeldebildschirm angezeigt wird.

3. Wenn Sie die **App-Verbundmetadaten-URL** im letzten Schritt des vorigen Abschnitts gespeichert haben, wählen Sie die Konfigurationsmethode **Metadaten-URL** aus, und fügen Sie **App-Verbundmetadaten-URL** in das Feld „Metadaten-URL“ ein. Andernfalls ändern Sie die Konfigurationsmethode in **Manuell**, füllen die Felder für die **SSO-URL des Identitätsanbieters (Anmelde-URL)** und den **Aussteller des Identitätsanbieters (Entitäts-ID)** manuell aus und laden das **Zertifikat (Base64)** hoch, das Sie von Azure AD erhalten haben.

4. Wählen Sie die Standardbenutzerrolle für neue Benutzer aus, die BlogIn über das einmalige Anmelden (SSO) beitreten.

5. Klicken Sie auf **Save changes** (Änderungen speichern).

Eine ausführlichere Erläuterung der Einrichtung des einmaligen Anmeldens (SSO) für BlogIn finden Sie unter [Einrichten des einmaligen Anmeldens für Microsoft Azure AD für BlogIn](https://blogin.co/blog/how-to-set-up-single-sign-on-sso-for-microsoft-azure-active-directory-azure-ad-267/). Sie können sich jederzeit an das [Supportteam von BlogIn](mailto:support@blogin.co) wenden, wenn Sie Fragen haben oder Hilfe benötigen.

### <a name="create-blogin-test-user"></a>Erstellen eines BlogIn-Testbenutzers

In diesem Abschnitt wird in BlogIn ein Benutzer namens B. Simon erstellt. BlogIn unterstützt die Just-in-Time-Benutzerbereitstellung (standardmäßig aktiviert). Für Sie steht in diesem Abschnitt kein Aktionselement zur Verfügung. Ist ein Benutzer noch nicht in BlogIn vorhanden, wird nach der Authentifizierung ein neuer Benutzer erstellt.

Außerdem unterstützt BlogIn die automatische Benutzerbereitstellung. Weitere Informationen zum Konfigurieren der automatischen Benutzerbereitstellung finden Sie [hier](./blogin-provisioning-tutorial.md).

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

#### <a name="sp-initiated"></a>SP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL von BlogIn weitergeleitet. Dort können Sie den Anmeldeflow initiieren.  

* Navigieren Sie direkt zur BlogIn-Anmelde-URL, und initiieren Sie dort den Anmeldeflow.

#### <a name="idp-initiated"></a>IDP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch sollten Sie automatisch bei der BlogIn-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. 

Sie können auch den Microsoft-Bereich „Meine Apps“ verwenden, um die Anwendung in einem beliebigen Modus zu testen. Beim Klicken auf die Kachel „BlogIn“ in „Meine Apps“ geschieht Folgendes: Wenn Sie die Anwendung im SP-Modus konfiguriert haben, werden Sie zum Initiieren des Anmeldeflows zur Anmeldeseite der Anwendung weitergeleitet. Wenn Sie die Anwendung im IDP-Modus konfiguriert haben, sollten Sie automatisch bei der BlogIn-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von BlogIn können Sie Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Daten Ihrer Organisation schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-aad)
