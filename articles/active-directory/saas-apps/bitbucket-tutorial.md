---
title: 'Tutorial: Azure Active Directory-Integration mit SAML SSO for Bitbucket by resolution GmbH | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und SAML SSO for Bitbucket by resolution GmbH konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/12/2021
ms.author: jeedes
ms.openlocfilehash: a3f969aa25e8be7925202647fd0760331e717fee
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132349146"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-bitbucket-by-resolution-gmbh"></a>Tutorial: Azure Active Directory-Integration mit SAML SSO for Bitbucket by resolution GmbH

In diesem Tutorial erfahren Sie, wie Sie SAML SSO for Bitbucket by resolution GmbH in Azure Active Directory (Azure AD) integrieren. Die Integration von SAML SSO for Bitbucket by resolution GmbH in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf SAML SSO for Bitbucket by resolution GmbH hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei SAML SSO for Bitbucket by resolution GmbH anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.


## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit SAML SSO for Bitbucket by resolution GmbH konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Wenn Sie keine Azure AD-Umgebung besitzen, können Sie [hier](https://azure.microsoft.com/pricing/free-trial/) eine einmonatige Testversion anfordern.
* Ein Abonnement, für das einmaliges Anmelden bei SAML SSO for Bitbucket by resolution GmbH aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* SAML SSO for Bitbucket by resolution GmbH unterstützt **SP- und IDP-initiiertes** einmaliges Anmelden.
* SAML SSO for Bitbucket by resolution GmbH unterstützt die **Just-in-Time**-Benutzerbereitstellung.


## <a name="add-saml-sso-for-bitbucket-by-resolution-gmbh-from-the-gallery"></a>Hinzufügen von SAML SSO for Bitbucket by resolution GmbH aus dem Katalog

Um die Integration von SAML SSO for Bitbucket by resolution GmbH in Azure AD zu konfigurieren, müssen Sie SAML SSO for Bitbucket by resolution GmbH aus dem Katalog der Liste der verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Bereich **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **SAML SSO for Bitbucket by resolution GmbH** in das Suchfeld ein.
1. Wählen Sie in den Ergebnissen **SAML SSO for Bitbucket by resolution GmbH** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-saml-sso-for-bitbucket-by-resolution-gmbh"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für SAML SSO for Bitbucket by resolution GmbH

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit SAML SSO for Bitbucket by resolution GmbH mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in SAML SSO for Bitbucket by resolution GmbH eingerichtet werden.

Führen Sie die folgenden Schritte aus, um das einmalige Anmelden von Azure AD mit SAML SSO for Bitbucket by resolution GmbH zu konfigurieren und zu testen:


1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
2. **[Konfigurieren des einmaligen Anmeldens für SAML SSO for Bitbucket by resolution GmbH](#configure-saml-sso-for-bitbucket-by-resolution-gmbh-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines Testbenutzers für SAML SSO for Bitbucket by resolution GmbH](#create-saml-sso-for-bitbucket-by-resolution-gmbh-test-user)** , um eine Entsprechung von Britta Simon in SAML SSO for Bitbucket by resolution GmbH zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
6. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

In diesem Abschnitt aktivieren Sie das einmalige Anmelden von Azure AD im Azure-Portal.
 
1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **SAML SSO for Bitbucket by resolution GmbH** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Wählen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** das Stiftsymbol für **Grundlegende SAML-Konfiguration** aus, um die Einstellungen zu bearbeiten.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus, wenn Sie die Anwendung im **IDP-initiierten Modus** konfigurieren möchten:


    a. Geben Sie im Textfeld **Bezeichner** eine URL im folgenden Format ein: `https://<server-base-url>/plugins/servlet/samlsso`

    b. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://<server-base-url>/plugins/servlet/samlsso`

    c. Klicken Sie auf **Zusätzliche URLs festlegen**, und führen Sie den folgenden Schritt aus, wenn Sie die Anwendung im **SP-initiierten Modus** konfigurieren möchten:
    
    Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Sie müssen diese Werte mit dem tatsächlichen Bezeichner, der Antwort-URL und der Anmelde-URL aktualisieren. Diese Werte erhalten Sie vom [Supportteam für den SAML SSO for Bitbucket by resolution GmbH-Client](https://marketplace.atlassian.com/apps/1217045/saml-single-sign-on-sso-bitbucket?hosting=server&tab=support). Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

5. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um den Ihren Anforderungen entsprechenden **Verbundmetadaten-XML**-Code aus den verfügbaren Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

In diesem Abschnitt erstellen Sie im Azure-Portal einen Testbenutzer namens B.Simon.

1. Wählen Sie im Azure-Portal im linken Bereich **Azure Active Directory** > **Benutzer** > **Alle Benutzer** aus.
1. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.
1. Führen Sie unter den Eigenschaften für **Benutzer** die folgenden Schritte aus:
   1. Geben Sie im Feld **Name** die Zeichenfolge `B.Simon` ein.  
   1. Geben Sie im Feld **Benutzername** die Zeichenfolge username@companydomain.extension ein. Beispiel: `B.Simon@contoso.com`.
   1. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**, und notieren Sie sich das Kennwort.
   1. Klicken Sie auf **Erstellen**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf SAML SSO for Bitbucket by resolution GmbH gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **SAML SSO for Bitbucket by resolution GmbH** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Klicken Sie auf **Benutzer hinzufügen**. Wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste der Benutzer die Option **B.Simon** aus. Wählen Sie anschließend am unteren Bildschirmrand **Auswählen** aus.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Wählen Sie im Dialogfeld **Zuweisung hinzufügen** die Option **Zuweisen** aus.

## <a name="configure-saml-sso-for-bitbucket-by-resolution-gmbh-sso"></a>Konfigurieren des einmaligen Anmeldens für SAML SSO for Bitbucket by resolution GmbH

1. Melden Sie sich bei der Unternehmenswebsite von SAML SSO for Bitbucket by resolution GmbH als Administrator an.

2. Klicken Sie rechts auf der Hauptsymbolleiste auf **Settings** (Einstellungen).

3. Wechseln Sie zum Abschnitt „ACCOUNTS“ (Konten), und klicken Sie auf der Menüleiste auf **SAML SingleSignOn**.

    ![SAML SingleSignOn](./media/bitbucket-tutorial/tutorial_bitbucket_samlsingle.png)

4. Klicken Sie auf der Seite **SAML SingleSignOn Plugin Configuration** (Konfiguration des SAML-SingleSignOn-Plug-Ins) auf **Add IdP** (IdP hinzufügen). 

    ![Add IdP (IdP hinzufügen)](./media/bitbucket-tutorial/tutorial_bitbucket_addidp.png)

5. Führen Sie auf der Seite **Choose your SAML Identity Provider** (SAML-Identitätsanbieter auswählen) die folgenden Schritte aus:

    ![Identitätsanbieter](./media/bitbucket-tutorial/tutorial_bitbucket_identityprovider.png)

    a. Wählen Sie **AZURE AD** als **IdP Type** (IdP-Typ) aus.

    b. Geben Sie im Textfeld **Name** den Namen ein.

    c. Geben Sie im Textfeld **Description** (Beschreibung) die Beschreibung ein.

    d. Klicken Sie auf **Weiter**.

6. Klicken Sie auf der Seite **Identity provider configuration** (Identitätsanbieterkonfiguration) auf die Schaltfläche **Next** (Weiter).

    ![Identitätsanbieterkonfiguration](./media/bitbucket-tutorial/tutorial_bitbucket_identityconfig.png)

7.  Klicken Sie auf der Seite **Import SAML IdP Metadata** (SAML-IdP-Metadaten importieren) auf **Load File** (Datei laden), um die **METADATA-XML**-Datei hochzuladen, die Sie im Azure-Portal heruntergeladen haben.

    ![IPD-Metadaten](./media/bitbucket-tutorial/tutorial_bitbucket_idpmetadata.png)

8. Klicken Sie auf **Weiter**.

9. Klicken Sie auf **Save settings** (Einstellungen speichern).

    ![Save settings (Einstellungen speichern)](./media/bitbucket-tutorial/tutorial_bitbucket_save.png)


## <a name="create-saml-sso-for-bitbucket-by-resolution-gmbh-test-user"></a>Erstellen eines Testbenutzers für SAML SSO for Bitbucket by resolution GmbH

Ziel dieses Abschnitts ist das Erstellen einer Benutzerin namens Britta Simon in SAML SSO for Bitbucket by resolution GmbH. SAML SSO for Bitbucket by resolution GmbH unterstützt die Just-in-Time-Bereitstellung. Zudem können Benutzer auch manuell erstellt werden. Wenden Sie sich nach Bedarf an das [Supportteam für den SAML SSO for Bitbucket by resolution GmbH-Client](https://marketplace.atlassian.com/plugins/com.resolution.atlasplugins.samlsso-bitbucket/server/support).

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

#### <a name="sp-initiated"></a>SP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für SAML SSO for Bitbucket by resolution GmbH weitergeleitet, wo Sie den Anmeldeflow initiieren können.  

* Rufen Sie direkt die Anmelde-URL für SAML SSO for Bitbucket by resolution GmbH auf, und initiieren Sie den Anmeldeflow.

#### <a name="idp-initiated"></a>IDP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch sollten Sie automatisch bei der Instanz von SAML SSO for Bitbucket by resolution GmbH angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben.

Sie können auch den Microsoft-Bereich „Meine Apps“ verwenden, um die Anwendung in einem beliebigen Modus zu testen. Beim Klicken auf die Kachel „SAML SSO for Bitbucket by resolution GmbH“ in „Meine Apps“ geschieht Folgendes: Wenn Sie die Anwendung im SP-Modus konfiguriert haben, werden Sie zum Initiieren des Anmeldeflows zur Anmeldeseite der Anwendung weitergeleitet. Wenn Sie die Anwendung im IDP-Modus konfiguriert haben, sollten Sie automatisch bei der Instanz von SAML SSO for Bitbucket by resolution GmbH angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).


## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von SAML SSO for Bitbucket by resolution GmbH können Sie Sitzungssteuerungen erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützen. Sitzungssteuerungen basieren auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-any-app)
