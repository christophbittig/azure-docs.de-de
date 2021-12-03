---
title: 'Tutorial: Azure Active Directory-Integration mit Nimblex | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Nimblex konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/27/2021
ms.author: jeedes
ms.openlocfilehash: b0cb2182e1733a9607d5f967e9c11eb9e4ea7f43
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132342146"
---
# <a name="tutorial-azure-active-directory-integration-with-nimblex"></a>Tutorial: Azure Active Directory-Integration mit Nimblex

In diesem Tutorial erfahren Sie, wie Sie Nimblex in Azure Active Directory (Azure AD) integrieren. Die Integration von Nimblex in Azure AD ermöglicht Ihnen Folgendes:

* Sie können in Azure AD steuern, wer Zugriff auf Nimblex hat.
* Sie können es Ihren Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei Nimblex anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Nimblex-Abonnement, für das das einmaliges Anmelden aktiviert ist.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Nimblex unterstützt **SP**-initiiertes einmaliges Anmelden.

* Nimblex unterstützt die **Just-in-Time**-Benutzerbereitstellung.

## <a name="add-nimblex-from-the-gallery"></a>Hinzufügen von Nimblex aus dem Katalog

Zum Konfigurieren der Integration von Nimblex in Azure AD müssen Sie Nimblex über den Katalog zur Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus dem Katalog hinzufügen** den Suchbegriff **Nimblex** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Nimblex** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-nimblex"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Nimblex

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Nimblex mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit das einmalige Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Nimblex eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Nimblex die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Nimblex](#configure-nimblex-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
    1. **[Erstellen eines Nimblex-Testbenutzers](#create-nimblex-test-user)** , um eine Entsprechung von B. Simon in Nimblex zu erhalten, die mit der entsprechenden Benutzerdarstellung in Azure AD verknüpft ist.
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Nimblex** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    a. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<YOUR_APPLICATION_PATH>/Login.aspx`

    b. Geben Sie im Feld **Bezeichner** eine URL im folgenden Format ein: `https://<YOUR_APPLICATION_PATH>/`.

    c. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://<path-to-application>/SamlReply.aspx`

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch die tatsächliche Anmelde-URL, den tatsächlichen Bezeichner und die tatsächliche Antwort-URL. Wenden Sie sich an das [Supportteam von Nimblex](mailto:support@ebms.com.au), um diese Werte zu erhalten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

4. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um das Ihrer Anforderung entsprechende **Zertifikat (Base64)** aus den angegebenen Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

6. Kopieren Sie im Abschnitt **Nimblex einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf NAVEX One gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **NAVEX One** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-nimblex-sso"></a>Konfigurieren des einmaligen Anmeldens von Nimblex

1. Melden Sie sich in einem anderen Webbrowserfenster bei Nimblex als Sicherheitsadministrator an.

2. Klicken Sie rechts oben auf der Seite auf das Logo **Einstellungen**.

    ![Screenshot: Einstellungssymbol](./media/nimblex-tutorial/settings.png)

3. Klicken Sie auf der Seite **Control Panel** unter **Security** auf **Single Sign-on**.

    ![Screenshot, auf dem die Option „Single Sign-On“ (Einmaliges Anmelden) im Menü „Security“ (Sicherheit) ausgewählt ist](./media/nimblex-tutorial/security.png)

4. Wählen Sie auf der Seite **Manage Single Sign-On** den Namen Ihrer Instanz aus, und klicken Sie auf **Edit**.

    ![Screenshot: Abschnitt „Manage Single Sign-On“ (Einmaliges Anmelden verwalten), in dem Sie „Edit“ (Bearbeiten) auswählen können](./media/nimblex-tutorial/edit-tab.png)

5. Führen Sie auf der Seite **Edit SAML SettingsSSO Provider** die folgenden Schritte aus:

    ![Screenshot: Abschnitt „Edit SSO Provider“ (SSO-Anbieter bearbeiten), in dem Sie die beschriebenen Werte eingeben können](./media/nimblex-tutorial/certificate.png)

    a. Geben Sie im Textfeld **Description** den Namen Ihrer Instanz ein.

    b. Öffnen Sie im Editor das Base64-codierte Zertifikat, das Sie aus dem Azure-Portal heruntergeladen haben, kopieren Sie den Inhalt, und fügen Sie ihn anschließend in das Feld **Certificate** ein.

    c. Fügen Sie in das Textfeld **Identity Provider Sso Target Url** (URL für einmaliges Anmelden des Identitätsanbieters) den Wert der **Anmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    d. Klicken Sie auf **Speichern**.

### <a name="create-nimblex-test-user"></a>Erstellen eines Nimblex-Testbenutzers

In diesem Abschnitt wird in Nimblex eine Benutzerin namens „Britta Simon“ erstellt. Nimblex unterstützt die Just-in-Time-Benutzerbereitstellung, die standardmäßig aktiviert ist. Für Sie steht in diesem Abschnitt kein Aktionselement zur Verfügung. Ist ein Benutzer noch nicht in Nimblex vorhanden, wird nach der Authentifizierung ein neuer Benutzer erstellt.

>[!Note]
>Wenn Sie einen Benutzer manuell erstellen müssen, setzen Sie sich mit dem [Nimblex-Supportteam](mailto:support@ebms.com.au) in Verbindung.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für Nimblex weitergeleitet, wo Sie den Anmeldeablauf initiieren können. 

* Rufen Sie direkt die Nimblex-Anmelde-URL auf, und initiieren Sie den Anmeldeablauf.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie in „Meine Apps“ auf die Kachel „Nimblex“ klicken, werden Sie zur Anmelde-URL für Nimblex weitergeleitet. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Nimblex können Sie die Sitzungssteuerung erzwingen, die Ihre vertraulichen Unternehmensdaten in Echtzeit vor der Exfiltration und Infiltration schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-aad)
