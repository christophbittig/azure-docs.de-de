---
title: 'Tutorial: Azure Active Directory-Integration mit SAML SSO for Jira by resolution GmbH | Microsoft-Dokumentation'
description: In diesem Artikel erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und SAML SSO for Jira by resolution GmbH konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/23/2021
ms.author: jeedes
ms.openlocfilehash: e5fae6e9f304364fecf9af9a55c235b5ae829828
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132299594"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-jira-by-resolution-gmbh"></a>Tutorial: Azure Active Directory-Integration mit SAML SSO for Jira by resolution GmbH

In diesem Tutorial erfahren Sie, wie Sie SAML SSO for Jira by resolution GmbH in Azure Active Directory (Azure AD) integrieren. Die Integration von SAML SSO for Jira by resolution GmbH in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf SAML SSO for Jira by resolution GmbH hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei SAML SSO for Jira by resolution GmbH anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Abonnement für SAML SSO for Jira by resolution GmbH, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* SAML SSO for Jira by resolution GmbH unterstützt **SP**- und **IDP**-initiiertes einmaliges Anmelden.

> [!NOTE]
> Der Bezeichner dieser Anwendung ist ein fester Zeichenfolgenwert, daher kann in einem Mandanten nur eine Instanz konfiguriert werden.

## <a name="add-saml-sso-for-jira-by-resolution-gmbh-from-the-gallery"></a>Hinzufügen von SAML SSO for Jira by resolution GmbH aus dem Katalog

Um die Integration von SAML SSO for Jira by resolution GmbH in Azure AD zu konfigurieren, müssen Sie SAML SSO for Jira by resolution GmbH aus dem Katalog zu Ihrer Liste der verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **SAML SSO for Jira by resolution GmbH** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **SAML SSO for Jira by resolution GmbH** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-saml-sso-for-jira-by-resolution-gmbh"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für SAML SSO for Jira by resolution GmbH

Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit SAML SSO for Jira by resolution GmbH mithilfe eines Testbenutzers namens **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in SAML SSO for Jira by resolution GmbH eingerichtet werden.

Führen Sie die folgenden Schritte aus, um das einmalige Anmelden von Azure AD mit SAML SSO for Jira by resolution GmbH zu konfigurieren und zu testen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für SAML SSO for Jira by resolution GmbH SSO](#configure-saml-sso-for-jira-by-resolution-gmbh-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines Testbenutzers für SAML SSO for Jira by resolution GmbH](#create-saml-sso-for-jira-by-resolution-gmbh-test-user)** , um eine Entsprechung von B. Simon in SAML SSO for Jira by resolution GmbH zu erhalten, die mit Ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **SAML SSO for Jira by resolution GmbH** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus, wenn Sie die Anwendung im **IDP-initiierten** Modus konfigurieren möchten:

    a. Geben Sie im Textfeld **Bezeichner** eine URL im folgenden Format ein: `https://<server-base-url>/plugins/servlet/samlsso`

    b. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://<server-base-url>/plugins/servlet/samlsso`

    c. Klicken Sie auf **Zusätzliche URLs festlegen**, und führen Sie den folgenden Schritt aus, wenn Sie die Anwendung im **SP-initiierten** Modus konfigurieren möchten:

    Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > Ersetzen Sie **\<server-base-url>** für den Bezeichner, die Antwort-URL und die Anmelde-URL durch die Basis-URL Ihrer Jira-Instanz. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen. Sollten Sie Probleme haben, wenden Sie sich an das [Supportteam für den SAML SSO for Jira by resolution GmbH-Client](https://www.resolution.de/go/support).

4. Laden Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** die **Verbundmetadaten-XML** herunter, und speichern Sie sie auf Ihrem Computer.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf SAML SSO for Jira by resolution GmbH gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **SAML SSO for Jira by resolution GmbH** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-saml-sso-for-jira-by-resolution-gmbh-sso"></a>Konfigurieren des einmaligen Anmeldens für SAML SSO for Jira by resolution GmbH 

1. Melden Sie sich in einem anderen Webbrowserfenster bei Ihrer Jira-Instanz als Administrator an.

2. Zeigen Sie auf der rechten Seite auf das Zahnrad, und klicken Sie auf **Manage apps** (Apps verwalten).
    
    ![Screenshot: Pfeil, der auf das Zahnradsymbol zeigt, und ausgewählte Option „Manage apps“ (Apps verwalten) in der Dropdownliste](./media/samlssojira-tutorial/add-on-1.png)

3. Sollten Sie auf die Seite für den Administratorzugriff umgeleitet werden, geben Sie dort Ihr **Kennwort** ein, und klicken Sie auf die **Bestätigungsschaltfläche**.

    ![Screenshot: Seite für den Administratorzugriff](./media/samlssojira-tutorial/add-on-2.png)

4. Jira leitet Sie normalerweise zum Atlassian-Marketplace um. Sollte das nicht der Fall sein, klicken Sie im linken Bereich auf **Find new apps** (Nach neuen Apps suchen). Suchen Sie nach **SAML Single Sign On (SSO) for JIRA**, und klicken Sie auf die Schaltfläche **Install** (Installieren), um das SAML-Plug-In zu installieren.

    ![Screenshot: Seite „Atlassian Marketplace for JIRA“ (Atlassian-Marketplace für JIRA) mit einem Pfeil, der auf die Schaltfläche „Install“ (Installieren) für die App „SAML Single Sign On (SSO) Jira, SAML/SSO“ zeigt](./media/samlssojira-tutorial/store.png)

5. Die Installation des Plug-Ins wird gestartet. Klicken Sie nach Abschluss des Vorgangs auf die Schaltfläche **Close** (Schließen).

    ![Screenshot: Dialogfeld „Installing“ (Wird installiert...)](./media/samlssojira-tutorial/store-2.png)

    ![Screenshot: Dialogfeld „Installed and ready to go!“ (Installiert und einsatzbereit!) mit ausgewählter Schaltfläche „Close“ (Schließen)](./media/samlssojira-tutorial/store-3.png)

6. Klicken Sie anschließend auf **Manage** (Verwalten).

    ![Screenshot: App „SAML Single Sign On (SSO) Jira, SAML/SSO“ mit ausgewählter Schaltfläche „Manage“ (Verwalten)](./media/samlssojira-tutorial/store-4.png)
    
7. Klicken Sie auf **Configure** (Konfigurieren), um das soeben installierte Plug-In zu konfigurieren.

    ![Screenshot: Seite „Manage apps“ (Apps verwalten) mit ausgewählter Schaltfläche „Configure“ (Konfigurieren) für die App „SAML SingleSignOn for Jira“](./media/samlssojira-tutorial/store-5.png)

8. Klicken Sie im **Konfigurations-Assistenten für das SAML SSO-Plug-In** auf **Add new IdP** (Neuen IdP hinzufügen), um Azure AD als neuen Identitätsanbieter zu konfigurieren.

    ![Screenshot: Willkommensseite mit ausgewählter Schaltfläche „Add new IdP“ (Neuen IdP hinzufügen)](./media/samlssojira-tutorial/add-on-4.png) 

9. Führen Sie auf der Seite **Choose your SAML Identity Provider** (SAML-Identitätsanbieter auswählen) die folgenden Schritte aus:

    ![Screenshot: Seite „Choose your SAML Identity Provider“ (SAML-Identitätsanbieter auswählen) mit hervorgehobenen Textfeldern „IdP Type“ (IdP-Typ) und „Name“ sowie mit ausgewählter Schaltfläche „Next“ (Weiter)](./media/samlssojira-tutorial/identity-provider.png)
 
    a. Legen Sie als IdP-Typ die Option **Azure AD** fest.
    
    b. Fügen Sie den **Namen** des Identitätsanbieters hinzu (beispielsweise „Azure AD“).
    
    c. Fügen Sie optional eine **Beschreibung** des Identitätsanbieters hinzu (beispielsweise „Azure AD“).
    
    d. Klicken Sie auf **Weiter**.
    
10. Klicken Sie auf der Seite **Identity provider configuration** (Identitätsanbieterkonfiguration) auf die Schaltfläche **Next** (Weiter).
 
    ![Screenshot: Seite für die Identitätsanbieterkonfiguration](./media/samlssojira-tutorial/configuration.png)

11. Führen Sie auf der Seite **Import SAML IdP Metadata** (SAML-IdP-Metadaten importieren) die folgenden Schritte aus:

    ![Screenshot: Seite „Import SAML IdP Metadata“ (SAML-IdP-Metadaten importieren) mit ausgewählter Aktion „Select Metadata XML File“ (Metadaten-XML-Datei auswählen)](./media/samlssojira-tutorial/metadata.png)

    a. Klicken Sie auf die Schaltfläche **Select Metadata XML File** (Metadaten-XML-Datei auswählen), und wählen Sie die **Verbundmetadaten-XML-Datei** aus, die Sie zuvor heruntergeladen haben.

    b. Klicken Sie auf die Schaltfläche **Import** (Importieren).
     
    c. Warten Sie kurz, bis der Importvorgang abgeschlossen ist.  
     
    d. Klicken Sie auf die Schaltfläche **Weiter**.
    
12. Klicken Sie auf der Seite **User ID attribute and transformation** (Benutzer-ID-Attribut und Transformation) auf die Schaltfläche **Next** (Weiter).

    ![Screenshot: Seite „User ID attribute and transformation“ (Benutzer-ID-Attribut und Transformation) mit ausgewählter Schaltfläche „Next“ (Weiter)](./media/samlssojira-tutorial/transformation.png)
    
13. Klicken Sie auf der Seite **User creation and update** (Benutzererstellung und -aktualisierung) auf **Save & Next** (Speichern und weiter), um die Einstellungen zu speichern.
    
    ![Screenshot: Seite „User creation and update“ (Benutzererstellung und -aktualisierung) mit ausgewählter Option „Save & Next“ (Speichern und weiter)](./media/samlssojira-tutorial/update.png)
    
14. Klicken Sie auf der Seite **Test your settings** (Einstellungen testen) auf **Skip test & configure manually** (Test überspringen und manuell konfigurieren), um den Benutzertest vorerst zu überspringen. Dieser wird im nächsten Abschnitt durchgeführt und erfordert einige Einstellungen im Azure-Portal.
    
    ![Screenshot: Seite „Test your settings“ (Einstellungen testen) mit ausgewählter Schaltfläche „Skip test & configure manually“ (Test überspringen und manuell konfigurieren)](./media/samlssojira-tutorial/test.png)
    
15. Klicken Sie auf **OK**, um die Warnung zu überspringen.
    
    ![Screenshot: Warnungsdialogfeld mit ausgewählter Schaltfläche „OK“](./media/samlssojira-tutorial/warning.png)

### <a name="create-saml-sso-for-jira-by-resolution-gmbh-test-user"></a>Erstellen eines Testbenutzers für SAML SSO for Jira by resolution GmbH

Um Azure AD-Benutzern die Anmeldung bei SAML SSO for Jira by resolution GmbH zu ermöglichen, müssen sie in SAML SSO for Jira by resolution GmbH bereitgestellt werden. Im Rahmen dieses Tutorials muss die Bereitstellung manuell durchgeführt werden. Für das SAML SSO-Plug-In von resolution stehen allerdings auch andere Bereitstellungsmodelle zur Verfügung – beispielsweise für die **Just-In-Time-Bereitstellung**. Weitere Informationen finden Sie in der [Dokumentation für SAML SSO by resolution GmbH](https://wiki.resolution.de/doc/saml-sso/latest/all). Sollten Sie Fragen haben, wenden Sie sich an den [Support von resolution](https://www.resolution.de/go/support).

**Führen Sie zum manuellen Bereitstellen eines Benutzerkontos die folgenden Schritte aus:**

1. Melden Sie sich bei der Jira-Instanz als Administrator an.

2. Zeigen Sie auf das Zahnrad, und wählen Sie **User management** (Benutzerverwaltung) aus.

   ![Screenshot: Pfeil, der auf das Zahnradsymbol zeigt, und ausgewählte Option „User management“ (Benutzerverwaltung) in der Dropdownliste](./media/samlssojira-tutorial/user-1.png)

3. Sollten Sie auf die Seite für den Administratorzugriff umgeleitet werden, geben Sie dort Ihr **Kennwort** ein, und klicken Sie anschließend auf die **Bestätigungsschaltfläche**.

    ![Screenshot: Seite für den Administratorzugriff mit hervorgehobenem Textfeld „Password“ (Kennwort)](./media/samlssojira-tutorial/user-2.png) 

4. Klicken Sie im Registerkartenabschnitt **User management** (Benutzerverwaltung) auf **create user** (Benutzer erstellen).

    ![Screenshot: Registerkarte „User management“ (Benutzerverwaltung) mit ausgewählter Schaltfläche „Create user“ (Benutzer erstellen)](./media/samlssojira-tutorial/user-3-new.png) 

5. Führen Sie auf der Dialogfeldseite **Create new user** (Neuen Benutzer erstellen) die folgenden Schritte aus. Der erstellte Benutzer muss exakt dem Benutzer in Azure AD entsprechen:

    ![Mitarbeiter hinzufügen](./media/samlssojira-tutorial/user-4-new.png) 

    a. Geben Sie im Textfeld **Email address** (E-Mail-Adresse) die E-Mail-Adresse des Benutzers ein: <b>BrittaSimon@contoso.com</b>.

    b. Geben Sie im Textfeld **Full Name** (Vollständiger Name) den vollständigen Namen des Benutzers ein: **Britta Simon**.

    c. Geben Sie im Textfeld **Username** (Benutzername) die E-Mail-Adresse des Benutzers ein: <b>BrittaSimon@contoso.com</b>. 

    d. Geben Sie im Textfeld **Password** (Kennwort) das Kennwort des Benutzers ein.

    e. Klicken Sie auf **Create user** (Benutzer erstellen), um die Benutzererstellung abzuschließen.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

#### <a name="sp-initiated"></a>SP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für SAML SSO for Jira by resolution GmbH weitergeleitet, wo Sie den Anmeldeflow initiieren können.  

* Rufen Sie direkt die Anmelde-URL für SAML SSO for Jira by resolution GmbH auf, und initiieren Sie den Anmeldeflow.

#### <a name="idp-initiated"></a>IDP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch sollten Sie automatisch bei der Instanz von SAML SSO for Jira by resolution GmbH angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. 

Sie können auch den Microsoft-Bereich „Meine Apps“ verwenden, um die Anwendung in einem beliebigen Modus zu testen. Beim Klicken auf die Kachel „SAML SSO for Jira by resolution GmbH“ in „Meine Apps“ geschieht Folgendes: Wenn Sie die Anwendung im SP-Modus konfiguriert haben, werden Sie zum Initiieren des Anmeldeflows zur Anmeldeseite der Anwendung weitergeleitet. Wenn Sie die Anwendung im IDP-Modus konfiguriert haben, sollten Sie automatisch bei der Instanz von SAML SSO for Jira by resolution GmbH angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="enable-sso-redirection-for-jira"></a>Aktivieren der SSO-Umleitung für Jira

Wie im vorherigen Abschnitt bereits erwähnt kann einmaliges Anmelden derzeit auf zwei Arten ausgelöst werden: entweder über das **Azure-Portal** oder unter Verwendung eines **speziellen Links zu Ihrer Jira-Instanz**. Mit dem SAML SSO-Plug-In der resolution GmbH können Sie das einmalige Anmelden auslösen, indem Sie einfach **auf eine beliebige URL zugreifen, die auf Ihre Jira-Instanz verweist**.

Nach Aktivierung einer Option im Plug-In werden im Wesentlichen alle Benutzer, die auf Jira zugreifen, zum einmaligen Anmelden umgeleitet.

Gehen Sie in **Ihrer Jira-Instanz** wie folgt vor, um die SSO-Umleitung zu aktivieren:

1. Öffnen Sie die Konfigurationsseite des SAML SSO-Plug-Ins in Jira.
1. Klicken Sie im linken Bereich auf **Redirection** (Umleitung).

   ![Partieller Screenshot der Jira-Seite für die Konfiguration des SAML SingleSignOn-Plug-Ins, in dem im linken Navigationsbereich der Link „Redirection“ (Umleitung) hervorgehoben ist](./media/samlssojira-tutorial/configure-1.png)

1. Aktivieren Sie das Kontrollkästchen **Enable SSO Redirect** (SSO-Umleitung aktivieren).

   ![Partieller Screenshot der Jira-Seite für die Konfiguration des SAML SingleSignOn-Plug-Ins, in dem das aktivierte Kontrollkästchen „Enable SSO Redirect“ (SSO-Umleitung aktivieren) hervorgehoben ist](./media/samlssojira-tutorial/configure-2.png) 

1. Klicken Sie in der rechten oberen Ecke auf die Schaltfläche **Save Settings** (Einstellungen speichern).

Nach Aktivierung der Option ist die Eingabeaufforderung für Benutzername und Kennwort weiterhin unter `https://<server-base-url>/login.jsp?nosso` erreichbar, wenn das Kontrollkästchen **Enable nosso** („nosso“ aktivieren) aktiviert ist. **\<server-base-url>** muss wie immer durch Ihre Basis-URL ersetzt werden.

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von SAML SSO for Jira by resolution GmbH können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-any-app)
