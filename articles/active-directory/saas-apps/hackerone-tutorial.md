---
title: 'Tutorial: Azure Active Directory-Integration mit HackerOne | Microsoft Docs'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und HackerOne konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/15/2021
ms.author: jeedes
ms.openlocfilehash: 0959fff0f3020b91ba61168e811f0a4a1cde5f8e
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132309308"
---
# <a name="tutorial-azure-active-directory-integration-with-hackerone"></a>Tutorial: Azure Active Directory-Integration mit HackerOne

In diesem Tutorial erfahren Sie, wie Sie HackerOne in Azure Active Directory (Azure AD) integrieren. Die Integration von HackerOne in Azure AD ermöglicht Ihnen Folgendes:

* Sie können in Azure AD steuern, wer Zugriff auf HackerOne hat.
* Sie ermöglichen es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei HackerOne anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein HackerOne-Abonnement, für das einmaliges Anmelden (SSO) aktiviert ist.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* HackerOne unterstützt **SP-initiiertes** einmaliges Anmelden.
* HackerOne unterstützt die **Just-in-Time**-Benutzerbereitstellung.

> [!NOTE]
> Der Bezeichner dieser Anwendung ist ein fester Zeichenfolgenwert, daher kann in einem Mandanten nur eine Instanz konfiguriert werden.

## <a name="add-hackerone-from-the-gallery"></a>Hinzufügen von HackerOne aus dem Katalog

Zum Konfigurieren der Integration von HackerOne in Azure AD müssen Sie HackerOne aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus dem Katalog hinzufügen** den Suchbegriff **HackerOne** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **HackerOne** aus und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-hackerone"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für HackerOne

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit HackerOne mithilfe eines Testbenutzers namens **B. Simon**. Damit das einmalige Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in HackerOne eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit HackerOne die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für HackerOne](#configure-hackerone-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines Testbenutzers für HackerOne](#create-hackerone-test-user)** , um eine Entsprechung von B. Simon in HackerOne zu erhalten, die mit der Benutzerdarstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **HackerOne** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    a. Geben Sie im Textfeld **Bezeichner (Entitäts-ID)** den folgenden Wert ein: `hackerone.com`.

    b. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://hackerone.com/users/saml/sign_in?email=<CONFIGURED_DOMAIN>`.

    > [!Note]
    > Der Wert der Anmelde-URL entspricht nicht dem tatsächlichen Wert. Aktualisieren Sie diesen Wert mit der tatsächlichen Anmelde-URL. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

5. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um das Ihrer Anforderung entsprechende **Zertifikat (Base64)** aus den angegebenen Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

6. Kopieren Sie im Abschnitt **HackerOne einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie den Zugriff auf HackerOne gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **HackerOne** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-hackerone-sso"></a>Konfigurieren des einmaligen Anmeldens für HackerOne

1. Melden Sie sich bei Ihrem HackerOne-Mandanten als Administrator an.

2. Klicken Sie im oberen Menü auf **Settings**.

    ![Screenshot, auf dem im Menü „Settings“ (Einstellungen) ausgewählt ist](./media/hackerone-tutorial/menu.png)

3. Navigieren Sie zu **Authentication** (Authentifizierung), und klicken Sie auf **Add SAML settings** (SAML-Einstellungen hinzufügen).

    ![Screenshot: Seite „Authentication Settings“ (Authentifizierungseinstellungen) mit der ausgewählten Schaltfläche „Add SAML settings“ (SAML-Einstellungen hinzufügen)](./media/hackerone-tutorial/settings.png)

4. Führen Sie im Dialogfeld **SAML Settings** (SAML-Einstellungen) die folgenden Schritte aus:

    ![Einmaliges Anmelden konfigurieren](./media/hackerone-tutorial/certificate.png)

    a. Geben Sie im Textfeld **Email Domain** eine registrierte Domäne ein.

    b. Fügen Sie in die Textfelder für die **URL zur einmaligen Anmeldung** den Wert der **Anmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    c. Öffnen Sie Ihre aus dem Azure-Portal heruntergeladene **Zertifikatdatei** im Editor, kopieren Sie den Inhalt in die Zwischenablage, und fügen Sie ihn anschließend in das Textfeld **X.509-Zertifikat** ein.

    d. Klicken Sie auf **Speichern**.

5. Führen Sie im Dialogfeld „Authentication Settings“ die folgenden Schritte aus:

    ![Screenshot: Dialogfeld „Authentication Settings“ (Authentifizierungseinstellungen) mit der ausgewählten Schaltfläche „Run test“ (Test ausführen)](./media/hackerone-tutorial/test.png)

    a. Klicken Sie auf **Run test**.

6. Wenn der Test erfolgreich abgeschlossen wurde und das Feld **Status** die Angabe **Letzter Teststatus: Erfolg** enthält, wählen Sie die Schaltfläche **Überprüfung anfordern** zur Übermittlung an HackerOne zur Genehmigung aus.

    ![Übermitteln an HackerOne zur Genehmigung](./media/hackerone-tutorial/verification.png)

7. Nachdem die Einstellungen von HackerOne genehmigt wurden, können Sie die Schaltfläche **Benutzer migrieren** auswählen, um die SSO-Authentifizierung für alle Benutzer erforderlich zu machen.

    ![Aktivieren von SAML](./media/hackerone-tutorial/users.png)

### <a name="create-hackerone-test-user"></a>Erstellen eines HackerOne-Testbenutzers

In diesem Abschnitt wird in HackerOne ein Benutzer namens Britta Simon erstellt. HackerOne unterstützt die Just-in-Time-Benutzerbereitstellung, die standardmäßig aktiviert ist. Für Sie steht in diesem Abschnitt kein Aktionselement zur Verfügung. Ist ein Benutzer noch nicht in HackerOne vorhanden, wird nach der Authentifizierung ein neuer Benutzer erstellt.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für HackerOne weitergeleitet, wo Sie den Anmeldeablauf initiieren können. 

* Navigieren Sie direkt zur HackerOne-Anmelde-URL, und initiieren Sie dort den Anmeldeablauf.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie in „Meine Apps“ auf die Kachel „HackerOne“ klicken, werden Sie zur Anmelde-URL für HackerOne umgeleitet. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von HackerOne können Sie die Sitzungssteuerung erzwingen, die Ihre vertraulichen Unternehmensdaten in Echtzeit vor der Exfiltration und Infiltration schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-aad)
