---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit SAP Concur Travel and Expense | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und SAP Concur Travel and Expense konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/21/2020
ms.author: jeedes
ms.openlocfilehash: e7ae41e1b4a9438ee2766234054769fa93fbd240
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132314791"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-concur-travel-and-expense"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit SAP Concur Travel and Expense

In diesem Tutorial erfahren Sie, wie Sie SAP Concur Travel and Expense in Azure Active Directory (Azure AD) integrieren. Die Integration von SAP Concur Travel and Expense in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf SAP Concur Travel and Expense hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei SAP Concur Travel and Expense anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Abonnement für SAP Concur Travel and Expense
* Die Rolle „Unternehmensadministrator“ im Concur-Benutzerkonto. Mit dem [Concur-Self-Service-Tool für SSO](https://www.concursolutions.com/nui/authadmin/ssoadmin) können Sie testen, ob Sie über die entsprechenden Zugriffsberechtigungen verfügen. Wenn Sie keinen Zugriff haben, wenden Sie sich an den Concur-Support oder den Projektmanager für die Implementierung. 

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD.

* SAP Concur Travel and Expense unterstützt **IDP-** und **SP-initiiertes** einmaliges Anmelden.
* SAP Concur Travel and Expense unterstützt das Testen von SSO in Produktions- und Implementierungsumgebungen. 

> [!NOTE]
> Der Bezeichner dieser Anwendung ist ein fester Zeichenfolgenwert für die drei folgenden Regionen: USA, EMEA und China. Es kann also nur eine Instanz für jede Region in einem Mandanten konfiguriert werden. 

## <a name="adding-sap-concur-travel-and-expense-from-the-gallery"></a>Hinzufügen von SAP Concur Travel and Expense aus dem Katalog

Zum Konfigurieren der Integration von SAP Concur Travel and Expense in Azure AD müssen Sie SAP Concur Travel and Expense aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **SAP Concur Travel and Expense** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **SAP Concur Travel and Expense** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-sap-concur-travel-and-expense"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für SAP Concur Travel and Expense

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit SAP Concur Travel and Expense mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in SAP Concur Travel and Expense eingerichtet werden.

Führen Sie die folgenden Schritte aus, um das einmalige Anmelden von Azure AD mit SAP Concur Travel and Expense zu konfigurieren und zu testen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für SAP Concur Travel and Expense](#configure-sap-concur-travel-and-expense-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines SAP Concur Travel and Expense-Testbenutzers](#create-sap-concur-travel-and-expense-test-user)** , um eine Entsprechung von B. Simon in SAP Concur Travel and Expense zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **SAP Concur Travel and Expense** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Bearbeitungs- bzw. Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Im Abschnitt **Grundlegende SAML-Konfiguration** ist die Anwendung im **IDP-initiierten** Modus vorkonfiguriert, und die erforderlichen URLs sind bereits mit Azure vorausgefüllt. Der Benutzer muss die Konfiguration speichern, indem er auf die Schaltfläche **Speichern** klickt.

    > [!NOTE]
    > Der Bezeichner (Entitäts-ID) und die Antwort-URL (Assertionsverbraucherdienst-URL) sind regionsspezifisch. Wählen Sie die Option basierend auf dem Rechenzentrum Ihrer Concur-Entität aus. Wenn Sie das Rechenzentrum der Concur-Entität nicht kennen, wenden Sie sich an den Concur-Support. 

5. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Bearbeitungs- bzw. Stiftsymbol für **Benutzerattribut**, um die Einstellungen zu bearbeiten. Die eindeutige Benutzer-ID muss mit der Concur-Benutzeranmelde-ID (login_id) übereinstimmen. In der Regel muss **user.userprincipalname** in **user.mail** geändert werden.

    ![Benutzerattribut bearbeiten](common/edit-attribute.png)

6. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** zu **Verbundmetadaten-XML**, und wählen Sie **Herunterladen** aus, um die Metadaten-XML herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf SAP Concur Travel and Expense gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **SAP Concur Travel and Expense** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.

1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-sap-concur-travel-and-expense-sso"></a>Konfigurieren des einmaligen Anmeldens für SAP Concur Travel and Expense

1. Wenn Sie die Konfiguration in SAP Concur Travel and Expense automatisieren möchten, müssen Sie die **Browsererweiterung „Meine Apps“ für die sichere Anmeldung** installieren, indem Sie auf **Erweiterung installieren** klicken.

    ![Erweiterung „Meine Apps“](common/install-myappssecure-extension.png)

2. Nachdem Sie die Erweiterung zum Browser hinzugefügt haben, klicken Sie auf **SAP Concur Travel and Expense einrichten** und Sie werden zur SAP Concur Travel and Expense-Anwendung weitergeleitet. Geben Sie dort die Administratoranmeldeinformationen ein, um sich bei SAP Concur Travel and Expense anzumelden. Die Browsererweiterung konfiguriert die Anwendung automatisch für Sie und automatisiert die Schritte 3 bis 7.

    ![Einrichtungskonfiguration](common/setup-sso.png)

3. Wenn Sie SAP Concur Travel and Expense manuell in einem anderen Webbrowserfenster einrichten möchten, müssen Sie die heruntergeladene **Verbundmetadaten-XML** in das [Concur-Self-Service-Tool für SSO](https://www.concursolutions.com/nui/authadmin/ssoadmin) hochladen und sich auf Ihrer SAP Concur Travel and Expense-Unternehmensseite als Administrator anmelden.

1. Klicken Sie auf **Hinzufügen**.
1. Geben Sie einen benutzerdefinierten Namen für Ihren IdP ein, etwa „Azure AD (US)“. 
1. Klicken Sie auf **Upload XML File** (XML-Datei hochladen), und fügen Sie die zuvor heruntergeladene **Verbundmetadaten-XML** an.
1. Klicken Sie auf **Add Metadata** (Metadaten hinzufügen), um die Änderung zu speichern.

    ![Screenshot des Concur-Self-Service-Tools für SSO](./media/concur-travel-and-expense-tutorial/add-metadata-concur-self-service-tool.png)

### <a name="create-sap-concur-travel-and-expense-test-user"></a>Erstellen eines Testbenutzers für SAP Concur Travel and Expense

In diesem Abschnitt erstellen Sie in SAP Concur Travel and Expense einen Benutzer namens B. Simon. Wenden Sie sich an das Concur-Supportteam, um die Benutzer auf der SAP Concur Travel and Expense-Plattform hinzuzufügen. Benutzer müssen erstellt und aktiviert werden, damit Sie einmaliges Anmelden verwenden können. 

> [!NOTE]
> Die Concur-Anmelde-ID von B. Simon muss mit dem eindeutigen Bezeichner von B. Simon in Azure AD übereinstimmen. Beispiel: Lautet der eindeutige Azure AD-Bezeichner von B. Simon `B.Simon@contoso.com`, muss die Concur-Anmelde-ID von B. Simon ebenfalls `B.Simon@contoso.com` lauten. 

## <a name="configure-concur-mobile-sso"></a>Konfigurieren von mobilem SSO für Concur
Um mobiles einmaliges Anmelden für Concur zu ermöglichen, müssen Sie die **Benutzerzugriffs-URL** an das Concur-Supportteam senden. Führen Sie die folgenden Schritte aus, um die **Benutzerzugriffs-URL** aus Azure AD abzurufen:
1. Navigieren Sie zu **Unternehmensanwendungen**.
1. Klicken Sie auf **SAP Concur Travel and Expense**.
1. Klicken Sie auf **Eigenschaften**.
1. Kopieren Sie die **Benutzerzugriffs-URL**, und senden Sie diese URL an den Concur-Support.

> [!NOTE]
> Die Self-Service-Option zum Konfigurieren von SSO steht nicht zur Verfügung. Wenden Sie sich daher an das Concur-Supportteam, um mobiles SSO zu aktivieren. 

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen:

#### <a name="sp-initiated"></a>SP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für SAP Concur Travel and Expense weitergeleitet, wo Sie den Anmeldeflow initiieren können.

* Rufen Sie direkt die SAP Concur Travel and Expense-Anmelde-URL auf, und initiieren Sie den Anmeldeflow.

#### <a name="idp-initiated"></a>IDP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch sollten Sie automatisch bei der SAP Concur Travel and Expense-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben.

Sie können auch den Microsoft-Bereich „Meine Apps“ verwenden, um die Anwendung in einem beliebigen Modus zu testen. Beim Klicken auf die Kachel „SAP Concur Travel and Expense“ in „Meine Apps“ geschieht Folgendes: Wenn Sie den SP-Modus konfiguriert haben, werden Sie zum Initiieren des Anmeldeflows zur Anmeldeseite der Anwendung weitergeleitet. Wenn Sie den IDP-Modus konfiguriert haben, sollten Sie automatisch bei der SAP Concur Travel and Expense-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von SAP Concur Travel and Expense können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-any-app)
