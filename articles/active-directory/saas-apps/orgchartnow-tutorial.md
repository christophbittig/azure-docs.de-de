---
title: 'Tutorial: Azure Active Directory-Integration mit OrgChart Now | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und OrgChart Now konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/11/2021
ms.author: jeedes
ms.openlocfilehash: b326114013502840e05284ba6d7049273ea42a44
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132287614"
---
# <a name="tutorial-azure-active-directory-integration-with-orgchart-now"></a>Tutorial: Azure Active Directory-Integration mit OrgChart Now

In diesem Tutorial erfahren Sie, wie Sie OrgChart Now in Azure Active Directory (Azure AD) integrieren. Die Integration von OrgChart in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf OrgChart Now hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei OrgChart Now anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* OrgChart Now-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* OrgChart Now unterstützt **SP**- und **IDP-initiiertes** einmaliges Anmelden.

> [!NOTE]
> Der Bezeichner dieser Anwendung ist ein fester Zeichenfolgenwert, daher kann in einem Mandanten nur eine Instanz konfiguriert werden.

## <a name="add-orgchart-now-from-the-gallery"></a>Hinzufügen von OrgChart Now aus dem Katalog

Zum Konfigurieren der Integration von OrgChart Now in Azure AD müssen Sie OrgChart Now aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **OrgChart Now** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **OrgChart Now** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-orgchart-now"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für OrgChart Now

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit OrgChart Now mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in OrgChart Now eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit OrgChart Now die folgenden Schritte aus

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für OrgChart Now](#configure-orgchart-now-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
    1. **[Erstellen eines OrgChart Now-Testbenutzers](#create-orgchart-now-test-user)** , um eine Entsprechung von B. Simon in OrgChart Now zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist.
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD 

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **OrgChart Now** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus, wenn Sie die Anwendung im **IDP-initiierten** Modus konfigurieren möchten:

    Geben Sie im Textfeld **Bezeichner** die URL `https://sso2.orgchartnow.com` ein.

5. Klicken Sie auf **Zusätzliche URLs festlegen**, und führen Sie den folgenden Schritt aus, wenn Sie die Anwendung im **SP-initiierten Modus** konfigurieren möchten:

    Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://sso2.orgchartnow.com/Shibboleth.sso/Login?entityID=<YourEntityID>&target=https://sso2.orgchartnow.com`

    > [!NOTE]
    > `<YourEntityID>` ist der **Azure AD-Bezeichner**, der im Abschnitt **OrgChart Now einrichten** kopiert wurde (Beschreibung weiter unten in diesem Tutorial).

6. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um den Ihren Anforderungen entsprechenden **Verbundmetadaten-XML**-Code aus den verfügbaren Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

7. Kopieren Sie im Abschnitt **OrgChart Now einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf OrgChart Now gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **OrgChart Now** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-orgchart-now-sso"></a>Konfigurieren des einmaliges Anmeldens für OrgChart Now

Zum Konfigurieren des einmaligen Anmeldens aufseiten von **OrgChart Now** müssen Sie die heruntergeladene **Verbundmetadaten-XML** und die kopierten URLs aus dem Azure-Portal an das [Supportteam von OrgChart Now](mailto:ocnsupport@officeworksoftware.com) senden. Es führt die Einrichtung durch, damit die SAML-SSO-Verbindung auf beiden Seiten richtig festgelegt ist.

### <a name="create-orgchart-now-test-user"></a>Erstellen eines OrgChart Now-Testbenutzers

Damit sich Azure AD-Benutzer an OrgChart Now anmelden können, müssen sie in OrgChart Now bereitgestellt werden. 

1. OrgChart Now unterstützt die Just-In-Time-Bereitstellung, die standardmäßig aktiviert ist. Wenn noch kein Benutzer vorhanden ist, wird beim Zugreifen auf OrgChart Now ein neuer Benutzer erstellt. Mit dem Feature für die Just-in-Time-Bereitstellung wird nur dann ein Benutzer mit **Lesezugriff** erstellt, wenn eine SSO-Anforderung von einem bekannten IDP stammt und die E-Mail-Adresse in der Benutzerliste der SAML-Assertion nicht gefunden wird. Für dieses Feature zur automatischen Bereitstellung müssen Sie in OrgChart Now eine Zugriffsgruppe mit dem Titel **General** (Allgemein) erstellen. Führen Sie die unten angegebenen Schritte aus, um eine Zugriffsgruppe zu erstellen:

    a. Navigieren Sie zur Option **Gruppen verwalten**, nachdem Sie oben rechts auf der Benutzeroberfläche auf das **Zahnrad** geklickt haben.

      ![OrgChart Now-Gruppen](./media/orgchartnow-tutorial/groups.png)   

    b. Wählen Sie das Symbol **Hinzufügen**, und geben Sie der Gruppe den Namen **General**. Klicken Sie anschließend auf **OK**. 

      ![OrgChart Now – Hinzufügen](./media/orgchartnow-tutorial/general.png)

    c. Wählen Sie die Ordner aus, für die Benutzer allgemeinen oder schreibgeschützten Zugriff erhalten sollen:
    
      ![OrgChart Now-Ordner](./media/orgchartnow-tutorial/folders.png)

    d. Sperren Sie die Ordner mit **Lock** (Sperren), damit nur Administratorbenutzer diese ändern können. Wählen Sie anschließend **OK**.

      ![OrgChart Now – Sperren](./media/orgchartnow-tutorial/lock.png)

2. Wenn Sie Benutzer vom Typ **Admin** und **Read/Write** (Lesen/Schreiben) erstellen möchten, müssen Sie einen Benutzer manuell erstellen, um Zugriff auf seine Berechtigungsstufe per SSO zu erhalten. Führen Sie zum Bereitstellen eines Benutzerkontos die folgenden Schritte aus:

    a. Melden Sie sich als Sicherheitsadministrator an OrgChart Now an.

    b. Klicken Sie oben rechts auf **Settings** (Einstellungen), und navigieren Sie anschließend zu **Manage Users** (Benutzer verwalten).

      ![OrgChart Now-Einstellungen](./media/orgchartnow-tutorial/settings.png)

    c. Klicken Sie auf **Add** (Hinzufügen), und führen Sie die folgenden Schritte aus:

      ![OrgChart Now – Verwalten](./media/orgchartnow-tutorial/manage-users.png)

    1. Geben Sie im Textfeld **User ID** (Benutzer-ID) die Benutzer-ID im Format **brittasimon\@contoso.com** ein.

    1. Geben Sie im Textfeld **Email Address** (E-Mail-Adresse) die E-Mail-Adresse des Benutzers ein, z. B. **brittasimon\@contoso.com**.

    1. Klicken Sie auf **Hinzufügen**.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

#### <a name="sp-initiated"></a>SP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Daraufhin werden Sie zur Anmelde-URL für OrgChart Now weitergeleitet, wo Sie den Anmeldeflow initiieren können.  

* Navigieren Sie direkt zur Anmelde-URL für OrgChart Now, und initiieren Sie den Anmeldeflow.

#### <a name="idp-initiated"></a>IDP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Daraufhin sollten Sie automatisch bei der OrgChart Now-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. 

Sie können auch den Microsoft-Bereich „Meine Apps“ verwenden, um die Anwendung in einem beliebigen Modus zu testen. Beim Klicken auf die Kachel „OrgChart Now“ in „Meine Apps“ geschieht Folgendes: Wenn Sie den SP-Modus konfiguriert haben, werden Sie zum Initiieren des Anmeldeflows zur Anmeldeseite der Anwendung weitergeleitet. Wenn Sie den IDP-Modus konfiguriert haben, sollten Sie automatisch bei der OrgChart Now-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von OrgChart Now können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-aad)
