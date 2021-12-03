---
title: 'Tutorial: Azure Active Directory-Integration mit Rollbar | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Rollbar konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/31/2021
ms.author: jeedes
ms.openlocfilehash: 1761979d554bc3318ef8f2e06fe872a6e674d0c4
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132341671"
---
# <a name="tutorial-azure-active-directory-integration-with-rollbar"></a>Tutorial: Azure Active Directory-Integration mit Rollbar

In diesem Tutorial erfahren Sie, wie Sie Rollbar in Azure Active Directory (Azure AD) integrieren. Die Integration von Rollbar in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf Rollbar hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Rollbar anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit Rollbar konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Sollten Sie nicht über eine Azure AD-Umgebung verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) verwenden.
* Rollbar-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Rollbar unterstützt **SP- und IDP-initiiertes** einmaliges Anmelden.
* Rollbar unterstützt die [Automatisierte Benutzerbereitstellung](rollbar-provisioning-tutorial.md).

> [!NOTE]
> Der Bezeichner dieser Anwendung ist ein fester Zeichenfolgenwert, daher kann in einem Mandanten nur eine Instanz konfiguriert werden.

## <a name="add-rollbar-from-the-gallery"></a>Hinzufügen von Rollbar aus dem Katalog

Zum Konfigurieren der Integration von Rollbar in Azure AD müssen Sie Rollbar aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Rollbar** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Rollbar** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-rollbar"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Rollbar

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Rollbar mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Rollbar eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Rollbar die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Rollbar](#configure-rollbar-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines Rollbar-Testbenutzers](#create-rollbar-test-user)** , um eine Entsprechung von B. Simon in Rollbar zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Rollbar** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus, wenn Sie die Anwendung im **IDP-initiierten** Modus konfigurieren möchten:

    a. Geben Sie im Textfeld **Bezeichner** die URL `https://saml.rollbar.com` ein.

    b. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://rollbar.com/<ACCOUNT_NAME>/saml/sso/azure/`

5. Klicken Sie auf **Zusätzliche URLs festlegen**, und führen Sie den folgenden Schritt aus, wenn Sie die Anwendung im **SP-initiierten Modus** konfigurieren möchten:

    Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://rollbar.com/<ACCOUNT_NAME>/saml/login/azure/`

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Die Werte müssen durch die tatsächliche Antwort-URL und die tatsächliche Anmelde-URL ersetzt werden. Wenden Sie sich an den [Rollbar-Support](mailto:support@rollbar.com), um diese Werte zu erhalten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

6. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um den Ihren Anforderungen entsprechenden **Verbundmetadaten-XML**-Code aus den verfügbaren Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

7. Kopieren Sie im Abschnitt **Rollbar einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Rollbar gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Rollbar** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-rollbar-sso"></a>Konfigurieren des einmaligen Anmeldens für Rollbar

1. Melden Sie sich in einem anderen Webbrowserfenster bei der Rollbar-Unternehmenswebsite als Administrator an.

1. Klicken Sie in der oberen rechten Ecke auf **Profileinstellungen**, und klicken Sie dann auf **Einstellungen für den Kontonamen**.

    ![Screenshot: Auswahl von „Einstellungen für den Kontonamen“ unter „Profileinstellungen“](./media/rollbar-tutorial/general.png)

1. Klicken Sie unter SICHERHEIT auf **Identitätsanbieter**.

    ![Screenshot: Auswahl von „Identitätsanbieter“ unter „SICHERHEIT“](./media/rollbar-tutorial/security.png)

1. Führen Sie im Abschnitt **SAML-Identitätsanbieter** die folgenden Schritte aus:

    ![Screenshot: Abschnitt „SAML-Identitätsanbieter“, in dem Sie die beschriebenen Werte eingeben können](./media/rollbar-tutorial/configure.png)

    a. Wählen Sie in der Dropdownliste **SAML-Identitätsanbieter** den Wert **AZURE** aus.

    b. Öffnen Sie Ihre Metadatendatei in Editor, kopieren Sie den Inhalt in die Zwischenablage, und fügen Sie ihn anschließend in das Textfeld **SAML-Metadaten** ein.

    c. Klicken Sie auf **Speichern**.

1. Nach dem Klicken auf die Schaltfläche „Speichern“ sieht der Bildschirm wie folgt aus:

    ![Screenshot: Ergebnisse auf der Seite „SAML-Identitätsanbieter“](./media/rollbar-tutorial/identity-provider.png)

    > [!NOTE]
    > Zum Abschließen des folgenden Schritts müssen Sie sich zunächst selbst als Benutzer der Rollbar-App in Azure hinzufügen.
    
    a. Wenn sich alle Benutzer über Azure authentifizieren sollen, klicken Sie auf **Über Identitätsanbieter anmelden**, damit eine erneute Authentifizierung über Azure erfolgt.  

    b.  Nachdem Sie zu dem Bildschirm zurückgekehrt sind, aktivieren Sie das Kontrollkästchen **Anmeldung über SAML-Identitätsanbieter vorschreiben**.

    b. Klicken Sie auf **Speichern**.

### <a name="create-rollbar-test-user"></a>Erstellen eines Rollbar-Testbenutzers

Damit sich Azure AD-Benutzer bei Rollbar anmelden können, müssen sie in Rollbar bereitgestellt werden. Im Fall von Rollbar ist die Bereitstellung eine manuelle Aufgabe.

**Führen Sie zum Bereitstellen eines Benutzerkontos die folgenden Schritte aus:**

1. Melden Sie sich bei Ihrer Rollbar-Unternehmenswebsite als Administrator an.

1. Klicken Sie in der oberen rechten Ecke auf **Profileinstellungen**, und klicken Sie dann auf **Einstellungen für den Kontonamen**.

    ![Benutzer](./media/rollbar-tutorial/general.png)

1. Klicken Sie auf **Users**.

    ![Mitarbeiter hinzufügen](./media/rollbar-tutorial/user.png)

1. Klicken Sie auf **Teammitglieder einladen**.

    ![Screenshot: Auswahl der Option „Teammitglieder einladen“](./media/rollbar-tutorial/invite-user.png)

1. Geben Sie im Textfeld den Namen eines Benutzers ein, z. B. **brittasimon\@contoso.com**, und klicken Sie dann auf **Hinzufügen/Einladen**.

    ![Screenshot: Option zum Hinzufügen/Einladen von Mitgliedern mit Angabe einer Adresse](./media/rollbar-tutorial/add-user.png)

1. Der Benutzer empfängt eine Einladung, und nachdem diese akzeptiert wurde, wird er im System erstellt.

> [!NOTE]
> Außerdem unterstützt Rollbar die automatische Benutzerbereitstellung. Weitere Informationen zum Konfigurieren der automatischen Benutzerbereitstellung finden Sie [hier](./rollbar-provisioning-tutorial.md).

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

#### <a name="sp-initiated"></a>SP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL von Rollbar weitergeleitet. Dort können Sie den Anmeldeflow initiieren.  

* Rufen Sie direkt die Rollbar-Anmelde-URL auf, und initiieren Sie den Anmeldeflow.

#### <a name="idp-initiated"></a>IDP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch sollten Sie automatisch bei der Rollbar-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. 

Sie können auch den Microsoft-Bereich „Meine Apps“ verwenden, um die Anwendung in einem beliebigen Modus zu testen. Beim Klicken auf die Kachel „Rollbar“ in „Meine Apps“ geschieht Folgendes: Wenn Sie die Anwendung im SP-Modus konfiguriert haben, werden Sie zum Initiieren des Anmeldeflows zur Anmeldeseite der Anwendung weitergeleitet. Wenn Sie die Anwendung im IDP-Modus konfiguriert haben, sollten Sie automatisch bei der Rollbar-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Rollbar können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-aad)
