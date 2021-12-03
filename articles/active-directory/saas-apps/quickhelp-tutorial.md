---
title: 'Lernprogramm: Azure Active Directory-Integration mit QuickHelp | Microsoft Docs'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und QuickHelp konfigurieren.
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
ms.openlocfilehash: a6d9efffcfdf2df8ef52e34a220857f1ab06b104
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132299815"
---
# <a name="tutorial-azure-active-directory-integration-with-quickhelp"></a>Lernprogramm: Azure Active Directory-Integration mit QuickHelp

In diesem Tutorial erfahren Sie, wie Sie QuickHelp in Azure Active Directory (Azure AD) integrieren. Die Integration von QuickHelp in Azure AD ermöglicht Folgendes:

* Sie können in Azure AD steuern, wer Zugriff auf QuickHelp hat.
* Sie können es Ihren Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei QuickHelp anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein QuickHelp-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* QuickHelp unterstützt **SP-initiiertes** einmaliges Anmelden.

* QuickHelp unterstützt die **Just-in-Time**-Benutzerbereitstellung.

> [!NOTE]
> Der Bezeichner dieser Anwendung ist ein fester Zeichenfolgenwert, daher kann in einem Mandanten nur eine Instanz konfiguriert werden.

## <a name="add-quickhelp-from-the-gallery"></a>Hinzufügen von QuickHelp aus dem Katalog

Zum Konfigurieren der Integration von QuickHelp in Azure AD müssen Sie QuickHelp aus dem Katalog der Liste der verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **QuickHelp** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **QuickHelp** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-quickhelp"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für QuickHelp

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit QuickHelp mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Verknüpfung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in MOBI eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit QuickHelp die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für QuickHelp](#configure-quickhelp-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines QuickHelp-Testbenutzers](#create-quickhelp-test-user)** , um ein Pendant von B. Simon in QuickHelp zu erhalten, das mit der dazugehörigen Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Suchen Sie im Azure-Portal auf der Anwendungsintegrationsseite für **QuickHelp** den Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    a. Geben Sie im Textfeld **Bezeichner (Entitäts-ID)** die folgende URL ein: `https://auth.quickhelp.com`.

    b. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://quickhelp.com/<ROUTE_URL>`.

    > [!NOTE]
    > Der Wert der Anmelde-URL entspricht nicht dem tatsächlichen Wert. Ersetzen Sie diesen Wert durch die tatsächliche Anmelde-URL. Wenden Sie sich an den Schnellhilfeadministrator Ihres Unternehmens oder Ihren BrainStorm Client Success Manager, um den Wert abzurufen. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

5. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um den Ihren Anforderungen entsprechenden **Verbundmetadaten-XML**-Code aus den verfügbaren Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

6. Kopieren Sie im Abschnitt **QuickHelp einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie Zugriff auf QuickHelp gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **QuickHelp** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-quickhelp-sso"></a>Konfigurieren des einmaligen Anmeldens für QuickHelp

1. Melden Sie sich bei Ihrer QuickHelp-Unternehmenswebsite als Administrator an.

2. Klicken Sie oben im Menü auf **Administrator**.
   
    ![Screenshot des Menüelements „Administrator“ für Brainstorm](./media/quickhelp-tutorial/admin.png "Administratormenü")

3. Klicken Sie im Menü **QuickHelp Admin** auf **Einstellungen**.
   
    ![Screenshot, auf dem im Menü „QuickHelp Admin“ die Option „Einstellungen“ ausgewählt ist](./media/quickhelp-tutorial/menu.png "Einstellungen")

4. Klicken Sie auf **Authentifizierungseinstellungen**.

5. Führen Sie auf der Seite **Authentifizierungseinstellungen** die folgenden Schritte aus.
   
    ![Screenshot der Seite „Authentifizierungseinstellungen“, auf der Sie die beschriebenen Werte eingeben können](./media/quickhelp-tutorial/authenticate.png "Authentifizierungseinstellungen")
   
    a. Wählen Sie für **SSO-Typ** die Option **WSFederation** aus.
   
    b. Klicken Sie zum Hochladen der heruntergeladenen Azure-Metadatendatei auf **Durchsuchen**, navigieren Sie zur Datei, und klicken Sie dann auf **Metadaten hochladen**.
   
    c. Geben Sie in das Textfeld **E-Mail** die Adresse `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` ein.
   
    d. Geben Sie in das Textfeld **Vorname** den Namen `type http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` ein.
   
    e. Geben Sie in das Textfeld **Nachname** den Namen `type http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` ein.
   
    f. Klicken Sie in der **Aktionsleiste** auf **Speichern**.

### <a name="create-quickhelp-test-user"></a>Erstellen einen QuickHelp-Testbenutzers

In diesem Abschnitt wird in QuickHelp ein Benutzer mit dem Namen „Britta Simon“ erstellt. QuickHelp unterstützt die Just-in-Time-Benutzerbereitstellung (standardmäßig aktiviert). Für Sie steht in diesem Abschnitt kein Aktionselement zur Verfügung. Ist ein Benutzer noch nicht in QuickHelp vorhanden, wird nach der Authentifizierung ein neuer Benutzer erstellt.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für QuickHelp weitergeleitet. Dort können Sie den Anmeldeflow initiieren. 

* Navigieren Sie direkt zur Anmelde-URL für QuickHelp, und initiieren Sie dort den Anmeldeflow.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie in „Meine Apps“auf die Kachel „QuickHelp“ klicken, werden Sie zur Anmelde-URL für QuickHelp weitergeleitet. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von QuickHelp können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-aad)
