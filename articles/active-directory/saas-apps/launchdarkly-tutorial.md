---
title: 'Tutorial: Azure Active Directory-Integration mit LaunchDarkly | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und LaunchDarkly konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/17/2021
ms.author: jeedes
ms.openlocfilehash: 9312c481e8d8196feb593bd5bb817f60e875a361
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132294663"
---
# <a name="tutorial-azure-active-directory-integration-with-launchdarkly"></a>Tutorial: Azure Active Directory-Integration mit LaunchDarkly

In diesem Tutorial erfahren Sie, wie Sie LaunchDarkly in Azure Active Directory (Azure AD) integrieren. Die Integration von LaunchDarkly in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf LaunchDarkly hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei LaunchDarkly anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

    > [!NOTE]
    > Die Integration von LaunchDarkly in Azure Active Directory ist unidirektional. Nach dem Konfigurieren der Integration können Sie mithilfe von Azure AD Benutzer, SSO und Konten in LaunchDarkly verwalten. Sie können LaunchDarkly jedoch **nicht** zum Verwalten von Benutzern, SSO und Konten in Azure nutzen.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* LaunchDarkly-Abonnement, das für einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* LaunchDarkly unterstützt **IDP**-initiiertes einmaliges Anmelden.
* LaunchDarkly unterstützt die **Just-In-Time**-Benutzerbereitstellung.

> [!NOTE]
> Der Bezeichner dieser Anwendung ist ein fester Zeichenfolgenwert, daher kann in einem Mandanten nur eine Instanz konfiguriert werden.

## <a name="add-launchdarkly-from-the-gallery"></a>Hinzufügen von LaunchDarkly aus dem Katalog

Zum Konfigurieren der Integration von LaunchDarkly in Azure AD müssen Sie LaunchDarkly aus dem Katalog zur Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **LaunchDarkly** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **LaunchDarkly** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-launchdarkly"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für LaunchDarkly

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit LaunchDarkly mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in LaunchDarkly eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit LaunchDarkly die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für LaunchDarkly](#configure-launchdarkly-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines LaunchDarkly-Testbenutzers](#create-launchdarkly-test-user)** , um eine Entsprechung von B. Simon in LaunchDarkly zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **LaunchDarkly** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    a. Geben Sie im Textfeld **Bezeichner** die URL `app.launchdarkly.com` ein.

    b. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://app.launchdarkly.com/trust/saml2/acs/<customers-unique-id>`

    > [!NOTE]
    > Der Wert der Antwort-URL entspricht nicht dem tatsächlichen Wert. Sie aktualisieren den Wert mit der tatsächlichen Antwort-URL. Dies wird später in diesem Tutorial beschrieben. LaunchDarkly unterstützt derzeit **IDP**-initiiertes einmaliges Anmelden. Wenn Sie die Anwendung im **IDP**-Modus verwenden möchten, müssen Sie das Feld **Anmelde-URL** leer lassen, da Sie ansonsten die Anmeldung nicht über **IDP** einleiten können. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

6. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um das Ihrer Anforderung entsprechende **Zertifikat (Base64)** aus den angegebenen Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

7. Kopieren Sie im Abschnitt **LaunchDarkly einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf LaunchDarkly gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **LaunchDarkly** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-launchdarkly-sso"></a>Konfigurieren des einmaligen Anmeldens für LaunchDarkly

1. Melden Sie sich in einem anderen Webbrowserfenster bei der LaunchDarkly-Unternehmenswebsite als Administrator an.

2. Wählen Sie **Account Settings** (Kontoeinstellungen) im linken Navigationsbereich aus.

    ![Screenshot: Auswahl der Option „Account Settings“ (Kontoeinstellungen) unter „Produktion“](./media/launchdarkly-tutorial/configure-1.png)

3. Klicken Sie auf die Registerkarte **Security** (Sicherheit).

    ![Screenshot: Registerkarte „Sicherheit“ in den Kontoeinstellungen](./media/launchdarkly-tutorial/configure-2.png)

4. Klicken Sie auf **ENABLE SSO** (SSO aktivieren) und dann auf **EDIT SAML CONFIGURATION** (SAML-Konfiguration bearbeiten).

    ![Screenshot: Seite „Einmaliges Anmelden“, auf der Sie „SSO aktivieren“ und „SAML-Konfiguration bearbeiten“ auswählen können](./media/launchdarkly-tutorial/configure-3.png)

5. Führen Sie im Abschnitt **Edit your SAML Configuration** (SAML-Konfiguration bearbeiten) die folgenden Schritte aus:

    ![Screenshot: Abschnitt „Edit your SAML configuration“ (SAML-Konfiguration bearbeiten), in dem Sie die hier beschriebenen Änderungen vornehmen können](./media/launchdarkly-tutorial/configure-4.png)

    a. Kopieren Sie die **SAML consumer service URL** (SAML-Verbraucherdienst-URL) für Ihre Instanz, und fügen Sie diese im Azure-Portal im Abschnitt **Domäne und URLs für LaunchDarkly** im Textfeld „Antwort-URL“ ein.

    b. Fügen Sie im Textfeld **Anmelde-URL** den Wert der **Anmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    c. Öffnen Sie das heruntergeladene Zertifikat aus dem Azure-Portal im Editor, kopieren Sie den Inhalt, und fügen Sie ihn im Feld **X.509 certificate** ein. Sie können das Zertifikat auch direkt hochladen, indem Sie auf **Upload one** klicken.

    d. Klicken Sie auf **Speichern**.

### <a name="create-launchdarkly-test-user"></a>Erstellen eines LaunchDarkly-Testbenutzers

In diesem Abschnitt wird in LaunchDarkly ein Benutzer namens B. Simon erstellt. LaunchDarkly unterstützt die Just-In-Time-Benutzerbereitstellung (standardmäßig aktiviert). Für Sie steht in diesem Abschnitt kein Aktionselement zur Verfügung. Ist ein Benutzer noch nicht in LaunchDarkly vorhanden, wird nach der Authentifizierung ein neuer Benutzer erstellt.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen:

* Klicken Sie im Azure-Portal auf „Diese Anwendung testen“. Dadurch sollten Sie automatisch bei der LaunchDarkly-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie unter „Meine Apps“ auf die Kachel „LaunchDarkly“ klicken, sollten Sie automatisch bei der LaunchDarkly-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von LaunchDarkly können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-any-app)
