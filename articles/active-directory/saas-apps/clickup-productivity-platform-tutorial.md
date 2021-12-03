---
title: 'Tutorial: Azure Active Directory-Integration mit ClickUp Productivity Platform | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und ClickUp Productivity Platform konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/30/2021
ms.author: jeedes
ms.openlocfilehash: 21c85de4deba9b58072482123e756b6d2660e7a1
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132305140"
---
# <a name="tutorial-azure-active-directory-integration-with-clickup-productivity-platform"></a>Tutorial: Azure Active Directory-Integration mit ClickUp Productivity Platform

In diesem Tutorial erfahren Sie, wie Sie ClickUp Productivity Platform in Azure Active Directory (Azure AD) integrieren. Wenn Sie die Azure AD "Produktivitätsplattform" in Azure Active Directory integrieren, können Sie Folgendes tun:

* Sie können in Azure AD steuern, wer Zugriff auf ClickUp Productivity Platform hat.
* Sie können Ihren Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei ClickUp Productivity Platform anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* ClickUp Productivity Platform-Abonnement, für das einmaliges Anmelden (SSO) aktiviert ist.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* ClickUp Productivity Platform unterstützt **SP**-initiiertes einmaliges Anmelden.

## <a name="add-clickup-productivity-platform-from-the-gallery"></a>Fügen Sie ClickUp Productivity Platform aus dem Katalog dazu

Zum Konfigurieren der Integration von ClickUp Productivity Platform in Azure AD müssen Sie ClickUp Productivity Platform aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **aus der Kategorie hinzufügen** den Abschnitt **ClickUp Productivity Platform** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **ClickUp Productivity Platform** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-clickup-productivity-platform"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für die ClickUp Productivity Platform

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit der ClickUp Productivity Platform mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer auf der ClickUp Productivity Plattform eingerichtet werden.

Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD mit der ClickUp Productivity Platform die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für die ClickUp Productivity Platform](#configure-clickup-productivity-platform-sso)** -um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
    1. **[Erstellen eines ClickUp Productivity Platform-Testbenutzers](#create-clickup-productivity-platform-test-user)** -um eine Entsprechung von Britta Simon in der ClickUp Productivity Platform zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist.
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. In dem  Azure Portal, auf der **ClickUp Productivity Platform** Anwendungsintegrationsseite, wählen Sie den **Verwalten** Abschnitt und wählen **einmaliges anmelden an**.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    a. Geben Sie im Textfeld **Anmelde-URL** die URL ein: `https://app.clickup.com/login/sso`.

    b. Geben Sie im Textfeld **Bezeichner (Entitäts-ID)** eine URL im folgenden Format ein: `https://api.clickup.com/v1/team/<team_id>/microsoft`.

    > [!NOTE]
    > Der ID-Wert ist nicht der tatsächliche Wert. Ersetzen Sie den Wert durch den tatsächlichen Bezeichner. Dies wird später in diesem Tutorial beschrieben.

5. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf die Schaltfläche „Kopieren“, um die **App-Verbundmetadaten-URL** zu kopieren, und speichern Sie sie auf Ihrem Computer.

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

In diesem Abschnitt ermöglichen Sie B.Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf die ClickUp Productivity Platform gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste den Eintrag **ClickUp Productivity Platform** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-clickup-productivity-platform-sso"></a>Konfigurieren der ClickUp Productivity Platform SSO

1. Melden Sie sich in einem anderen Webbrowserfenster an Ihrem ClickUp Productivity Platform-Mandanten als Administrator an.

2. Klicken Sie auf das **Benutzerprofil**, und wählen Sie anschließend die Option **Einstellungen** aus.

    ![Screenshot des ClickUp Productivity-Mandanten, bei dem das Symbol „Einstellungen“ ausgewählt ist](./media/clickup-productivity-platform-tutorial/configure-0.png)

    ![Screenshot der Einstellungen](./media/clickup-productivity-platform-tutorial/configure-1.png)

3. Wählen Sie als Anbieter für einmaliges Anmelden die Option **Microsoft**.

    ![Screenshot des Bereichs „Authentifizierung“, in dem Microsoft ausgewählt ist](./media/clickup-productivity-platform-tutorial/configure-2.png)

4. Führen Sie auf der Seite **Einmaliges Anmelden konfigurieren für ClickUp Productivity Platform** die folgenden Schritte aus:

    ![Screenshot der Seite „Einmaliges Anmelden konfigurieren für Microsoft“, auf der Sie die Entitäts-I D kopieren und die Azure-Verbundmetadaten-U R L speichern können](./media/clickup-productivity-platform-tutorial/configure-3.png)

    a. Klicken Sie auf **Kopieren**, um den Wert im Feld „Entity ID“ (Entitäts-ID) zu kopieren, und fügen Sie ihn im Azure-Portal im Abschnitt **Grundlegende SAML-Konfiguration** in das Textfeld **Bezeichner (Entitäts-ID)** ein.

    b. Fügen Sie im Textfeld **Azure Federation Metadata URL** (Azure-Verbundmetadaten-URL) den Wert der App-Verbundmetadaten-URL ein, den Sie aus dem Azure-Portal kopiert haben, und klicken Sie auf **Speichern**.

5. Klicken Sie zum Abschließen des Setups auf **Authenticate With Microsoft to complete setup** (Zum Beenden des Setups bei Microsoft authentifizieren), und führen Sie die Authentifizierung mit dem Microsoft-Konto durch.

    ![Screenshot der Schaltfläche zum Authentifizieren bei Microsoft zum Abschließen des Setups](./media/clickup-productivity-platform-tutorial/configure-4.png)


### <a name="create-clickup-productivity-platform-test-user"></a>Erstellen eines ClickUp Productivity Platform-Testbenutzers

1. Melden Sie sich in einem anderen Webbrowserfenster an Ihrem ClickUp Productivity Platform-Mandanten als Administrator an.

2. Klicken Sie auf das **Benutzerprofil**, und wählen Sie anschließend die Option **Personen** aus.

    ![Screenshot des ClickUp Productivity-Mandanten](./media/clickup-productivity-platform-tutorial/configure-0.png)

    ![Screenshot des ausgewählten Links „Personen“](./media/clickup-productivity-platform-tutorial/user-1.png)

3. Geben Sie die E-Mail-Adresse des Benutzers im Textfeld ein, und klicken Sie auf **Einladen**.

    ![Screenshot der Teambenutzereinstellungen, in denen Sie Personen per §-Mail einladen können](./media/clickup-productivity-platform-tutorial/user-2.png)

    > [!NOTE]
    > Der Benutzer erhält die Benachrichtigung und muss die Einladung annehmen, um das Konto aktivieren zu können.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für die ClickUp Productivity Platform weitergeleitet, wo Sie den Anmeldeflow initiieren können. 

* Rufen Sie direkt die ClickUp Productivity Platform-Anmelde-URL auf, und initiieren Sie den  Anmeldeflow von dort.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie in den "Meine Apps" auf die Kachel ClickUp Productivity Plattform klicken, leitet dies zur Anmelde-URL der Click Up Productivity Platform weiter. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von der ClickUp Productivity Platform können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-any-app)
