---
title: 'Tutorial: Azure AD-SSO-Integration mit 10,000ft Plans'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und 10,000ft Plans konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/29/2021
ms.author: jeedes
ms.openlocfilehash: a521736d32b51b5a4b6c22292e7dd6e60c4c0248
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132335212"
---
# <a name="tutorial-azure-ad-sso-integration-with-10000ft-plans"></a>Tutorial: Azure AD-SSO-Integration mit 10,000ft Plans

In diesem Tutorial erfahren Sie, wie Sie 10,000ft Plans in Azure Active Directory (Azure AD) integrieren. Die Integration von 10,000ft Plans in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf 10,000ft Plans hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei 10,000ft Plans anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Um die Integration von 10,000ft Plans in Azure AD konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Sollten Sie nicht über eine Azure AD-Umgebung verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) verwenden.
* 10,000ft Plans-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* 10,000ft Plans unterstützt **SP**-initiiertes SSO.
* 10,000ft Plans unterstützt die **Just-In-Time**-Benutzerbereitstellung.

## <a name="add-10000ft-plans-from-the-gallery"></a>Hinzufügen von 10,000ft Plans aus dem Katalog

Zum Konfigurieren der Integration von 10,000ft Plans in Azure AD müssen Sie 10,000ft Plans aus dem Katalog zu Ihrer Liste der verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **10,000ft Plans** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **10,000ft Plans** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-10000ft-plans"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für 10,000ft Plans

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit 10,000ft Plans mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in 10,000ft Plans eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit 10,000ft Plans die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für 10,000ft Plans](#configure-10000ft-plans-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines 10,000ft Plans-Testbenutzers](#create-10000ft-plans-test-user)** , um eine Entsprechung von B. Simon in 10,000ft Plans zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **10,000ft Plans** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    a. Geben Sie im Textfeld **Bezeichner (Entitäts-ID)** die folgende URL ein: `https://rm.smartsheet.com/saml/metadata`.

    b. Geben Sie im Textfeld **Antwort-URL** die folgende URL ein: `https://rm.smartsheet.com/saml/acs`.
    
    c. Geben Sie im Textfeld **Anmelde-URL** die URL ein: ` https://rm.smartsheet.com`.

    > [!NOTE]
    > Der Wert für **Bezeichner** lautet anders, wenn Sie über eine benutzerdefinierte Domäne verfügen. Wenden Sie sich an das [Kundensupportteam von 10,000ft Plans](https://www.10000ft.com/plans/support), um diesen Wert zu erhalten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

1. Wählen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** das Kopiersymbol aus, um die **App-Verbundmetadaten-URL** zu kopieren. Speichern Sie das Zertifikat auf Ihrem Computer.

    ![Screenshot: „SAML-Signaturzertifikat“ mit hervorgehobenem Kopiersymbol](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

In diesem Abschnitt erstellen Sie im Azure-Portal einen Testbenutzer mit dem Namen B. Simon.

1. Wählen Sie im Azure-Portal im Bereich **Azure-Dienste** die Option **Benutzer** und dann **Alle Benutzer** aus.
1. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.
1. Führen Sie unter den Eigenschaften für **Benutzer** die folgenden Schritte aus:
   1. Geben Sie im Feld **Name** die Zeichenfolge `B.Simon` ein.  
   1. Geben Sie im Feld **Benutzername** die Zeichenfolge username@companydomain.extension ein. Beispiel: `B.Simon@contoso.com`.
   1. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**, und notieren Sie sich den Wert aus dem Feld **Kennwort**.
   1. Klicken Sie auf **Erstellen**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf 10,000ft Plans gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **10,000ft Plans** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer/Gruppe hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-10000ft-plans-sso"></a>Konfigurieren des einmaliges Anmeldens für 10,000ft Plans

1. Melden Sie sich bei Ihrer 10,000ft Plans-Website als Administrator an.

1. Klicken Sie auf **Settings** (Einstellungen), und wählen Sie in der Dropdownliste **Account Settings** (Kontoeinstellungen) aus.

    ![Screenshot des Symbols „Settings“ (Einstellungen)](./media/10000ftplans-tutorial/settings.png)

1. Klicken Sie im Menü auf der linken Seite auf **SSO**, und führen Sie die folgenden Schritte aus:

    ![Screenshot der Einstellungsseite „SSO“](./media/10000ftplans-tutorial/setup-sso.png)

    a. Wählen Sie im Abschnitt „Setup SSO“ (SSO einrichten) die Option **Automatic Configuration** (Automatische Konfiguration) aus.

    b. Fügen Sie im Textfeld **IdP Metadata URL** (IdP-Metadaten-URL) den Wert der **Verbundmetadaten-URL der App** ein, den Sie aus dem Azure-Portal kopiert haben.

    c. Aktivieren Sie das Kontrollkästchen **Auto-provision authenticated users not in account** (Authentifizierte Benutzer*innen automatisch bereitstellen, die nicht im Konto sind).

    d. Klicken Sie auf **Speichern**.

### <a name="create-10000ft-plans-test-user"></a>Erstellen von 10,000ft Plans-Testbenutzern

In diesem Abschnitt wird eine Benutzerin mit dem Namen „Britta Simon“ in 10,000ft Plans erstellt. 10,000ft Plans unterstützt die Just-in-Time-Benutzerbereitstellung, die standardmäßig aktiviert ist. Für Sie steht in diesem Abschnitt kein Aktionselement zur Verfügung. Ist ein Benutzer noch nicht in 10,000ft Plans vorhanden, wird nach der Authentifizierung ein neuer Benutzer erstellt.

> [!NOTE]
> Wenn Sie einen Benutzer manuell erstellen müssen, setzen Sie sich mit dem [Kundensupportteam von 10,000ft Plans](https://www.10000ft.com/plans/support) in Verbindung.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für 10,000ft Plans weitergeleitet, wo Sie den Anmeldeflow initiieren können. 

* Rufen Sie direkt die 10,000ft Plans-Anmelde-URL auf, und initiieren Sie den Anmeldeflow.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie in „Meine Apps“ auf die Kachel „10,000ft Plans“ klicken, werden Sie zur Anmelde-URL von 10,000ft Plans umgeleitet. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von 10,000ft Plans können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-any-app)
