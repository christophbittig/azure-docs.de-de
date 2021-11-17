---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD mit ADP'
description: Erfahren Sie, wie Sie einmaliges Anmelden zwischen Azure Active Directory und ADP konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/30/2021
ms.author: jeedes
ms.openlocfilehash: c008122f0354b77505df61bdade90adb3960782f
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132280800"
---
# <a name="tutorial-azure-ad-sso-integration-with-adp"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD mit ADP

In diesem Tutorial erfahren Sie, wie Sie ADP in Azure Active Directory (Azure AD) integrieren. Die Integration von ADP in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf ADP hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei ADP anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* ADP-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

> [!NOTE]
> Diese Integration kann auch über die Azure AD-Umgebung für die US Government-Cloud verwendet werden. Sie finden diese Anwendung im Azure AD-Katalog für US Government-Cloudanwendungen und konfigurieren sie auf die gleiche Weise wie in der öffentlichen Cloud.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* ADP unterstützt **IDP**-initiiertes einmaliges Anmelden.

> [!NOTE]
> Der Bezeichner dieser Anwendung ist ein fester Zeichenfolgenwert, daher kann in einem Mandanten nur eine Instanz konfiguriert werden.

## <a name="add-adp-from-the-gallery"></a>Hinzufügen von ADP über den Katalog

Zum Konfigurieren der Integration von ADP in Azure AD müssen Sie ADP aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **ADP** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **ADP** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-adp"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für ADP

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit ADP mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in ADP eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit ADP die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
2. **[Konfigurieren des einmaligen Anmeldens für ADP](#configure-adp-sso)**, um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines ADP-Testbenutzers](#create-adp-test-user)**, um in ADP eine Entsprechung von B. Simon zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
3. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Klicken Sie im Azure-Portal auf der Anwendungsintegrationsseite für **ADP** auf die Registerkarte **Eigenschaften**, und führen Sie die folgenden Schritte aus: 

    ![Eigenschaften für das einmalige Anmelden](./media/adpfederatedsso-tutorial/properties.png)

    a. Legen Sie den Wert im Feld **Aktiviert für die Benutzeranmeldung** auf **Ja** fest.

    b. Kopieren Sie die **URL für den Benutzerzugriff**. Diese muss im Abschnitt **Anmelde-URL konfigurieren** eingefügt werden, dies wird später in diesem Tutorial erläutert.

    c. Legen Sie das Feld **Benutzerzuweisung erforderlich** auf **Ja** fest.

    d. Legen Sie das Feld **Für Benutzer sichtbar** auf **Nein** fest.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **ADP** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    Geben Sie im Textfeld **Bezeichner (Entitäts-ID)** die URL ein: `https://fed.adp.com`.

1. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** zu **Verbundmetadaten-XML**, und wählen Sie **Herunterladen** aus, um das Zertifikat herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

1. Kopieren Sie im Abschnitt **ADP einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf ADP gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste die Option **ADP** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-adp-sso"></a>Konfigurieren des einmaligen Anmeldens für ADP

Um das einmalige Anmelden für **ADP** zu konfigurieren, müssen Sie die heruntergeladene **Metadaten-XML** auf die [ADP-Website](https://adpfedsso.adp.com/public/login/index.fcc) hochladen.

> [!NOTE]  
> Dieser Vorgang kann einige Tage in Anspruch nehmen.

### <a name="configure-your-adp-services-for-federated-access"></a>Konfigurieren Ihrer ADP-Dienste für den Verbundzugriff

>[!Important]
> Mitarbeiter, die Verbundzugriff auf Ihre ADP-Dienste benötigen, müssen der ADP-Dienst-App zugewiesen und anschließend dem jeweiligen ADP-Dienst erneut zugewiesen werden.
Nach der Bestätigung durch Ihren ADP-Vertriebsbeauftragten konfigurieren Sie Ihre ADP-Dienste und weisen Benutzer zum Steuern des Benutzerzugriffs für die jeweiligen ADP-Dienste zu bzw. verwalten diese.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **ADP** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **ADP** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.
1. Klicken Sie im Azure-Portal auf Ihrer Anwendungsintegrationsseite für **ADP** auf die Registerkarte **Eigenschaften**, und führen Sie die folgenden Schritte aus:  

    ![Registerkarte „Eigenschaften“ für einmaliges Anmelden](./media/adpfederatedsso-tutorial/application.png)

    1. Legen Sie den Wert im Feld **Aktiviert für die Benutzeranmeldung** auf **Ja** fest.

    1. Legen Sie das Feld **Benutzerzuweisung erforderlich** auf **Ja** fest.

    1. Legen Sie das Feld **Für Benutzer sichtbar** auf **Ja** fest.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **ADP** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.

1. Wählen Sie im Dialogfeld **SSO-Methode auswählen** unter **Modus** die Option **Verknüpft**, um Ihre Anwendung mit **ADP** zu verknüpfen.

    ![SSO über Link](./media/adpfederatedsso-tutorial/linked.png)

1. Navigieren Sie zum Abschnitt **Anmelde-URL konfigurieren**, und führen Sie die folgenden Schritte aus:

    ![Konfigurieren des einmaligen Anmeldens](./media/adpfederatedsso-tutorial/users.png)

    1. Fügen Sie die **URL für den Benutzerzugriff** ein, die Sie oben aus der Registerkarte **Eigenschaften** (aus der Haupt-ADP-App) kopiert haben.

    1. Nachfolgend werden die 5 Apps aufgeführt, die unterschiedliche **Relayzustand-URLs** unterstützen. Sie müssen die geeignete **Relayzustand-URL** für die jeweilige Anwendung manuell an die **URL für den Benutzerzugriff** anfügen.

        * **ADP Workforce Now**

            `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?WFN`

        * **ADP Workforce Now Enhanced Time**

            `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?EETDC2`

        * **ADP Vantage HCM**

            `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?ADPVANTAGE`

        * **ADP Enterprise HR**

            `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?PORTAL`

        * **MyADP**

            `<User access URL>&relaystate=https://fed.adp.com/saml/fedlanding.html?REDBOX`

1. **Speichern** Sie die Änderungen.

1. Beginnen Sie nach Bestätigung durch Ihren ADP-Vertriebsbeauftragten mit dem Test für einen oder zwei Benutzer.

    1. Weisen Sie der ADP-Dienst-App einige Benutzer zu, um den Verbundzugriff zu testen.

    1. Der Test ist erfolgreich, wenn die Benutzer auf die ADP-Dienst-App im Katalog und auf ihren ADP-Dienst zugreifen können.

1. Wenn der Test erfolgreich war, weisen Sie den ADP-Verbunddienst einzelnen Benutzern oder Benutzergruppen zu, und führen Sie ein Rollout für Ihre Mitarbeiter durch. Dies wird später in diesem Tutorial erläutert.

### <a name="create-adp-test-user"></a>Erstellen eines ADP-Testbenutzers

Das Ziel dieses Abschnitts ist das Erstellen eines Benutzers namens B. Simon in ADP. Wenden Sie sich an das [ADP-Supportteam](https://www.adp.com/contact-us/overview.aspx), um die Benutzer unter dem ADP-Konto hinzufügen zu lassen. 

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen:

* Klicken Sie im Azure-Portal auf „Diese Anwendung testen“. Dadurch sollten Sie automatisch bei der ADP-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie in „Meine Apps“ auf die Kachel „ADP“ klicken, sollten Sie automatisch bei der ADP-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von ADP können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-any-app)
