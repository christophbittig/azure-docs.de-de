---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit EasySSO for Jira | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie einmaliges Anmelden zwischen Azure Active Directory und EasySSO for Jira konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/13/2021
ms.author: jeedes
ms.openlocfilehash: 17473ee82168c05ecd24ccca47e57f28ed2e40d5
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132338134"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-easysso-for-jira"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit EasySSO for Jira

In diesem Tutorial erfahren Sie, wie Sie EasySSO for Jira in Azure Active Directory (Azure AD) integrieren. Die Integration von EasySSO for Jira in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf Jira hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Jira anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* EasySSO for Jira-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* EasySSO for Jira unterstützt **SP- und IDP-initiiertes** einmaliges Anmelden.
* EasySSO for Jira unterstützt die **Just-In-Time**-Benutzerbereitstellung.

## <a name="add-easysso-for-jira-from-the-gallery"></a>Hinzufügen von EasySSO for Jira aus dem Katalog

Zum Konfigurieren der Integration von EasySSO for Jira in Azure AD müssen Sie EasySSO for Jira aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **EasySSO for Jira** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **EasySSO for Jira** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-easysso-for-jira"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für EasySSO for Jira

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit EasySSO for Jira mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in EasySSO for Jira eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit EasySSO for Jira die folgenden Schritte aus

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für EasySSO for Jira](#configure-easysso-for-jira-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines EasySSO for Jira-Testbenutzers](#create-easysso-for-jira-test-user)** , um eine Entsprechung von B. Simon in EasySSO for Jira zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **EasySSO for Jira** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus, wenn Sie die Anwendung im **IDP-initiierten** Modus konfigurieren möchten:

    a. Geben Sie im Textfeld **Bezeichner** eine URL im folgenden Format ein: `https://<server-base-url>/plugins/servlet/easysso/saml`

    b. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://<server-base-url>/plugins/servlet/easysso/saml`

1. Klicken Sie auf **Zusätzliche URLs festlegen**, und führen Sie den folgenden Schritt aus, wenn Sie die Anwendung im **SP-initiierten Modus** konfigurieren möchten:

    Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<server-base-url>/login.jsp`

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Sie müssen diese Werte mit dem tatsächlichen Bezeichner, der Antwort-URL und der Anmelde-URL aktualisieren. Diese Werte erhalten Sie im Zweifelsfall vom [Supportteam für EasySSO](mailto:support@techtime.co.nz). Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

1. Die Anwendung EasySSO for Jira erwartet die SAML-Assertionen in einem bestimmten Format. Daher müssen Sie Ihrer Konfiguration der SAML-Tokenattribute benutzerdefinierte Attributzuordnungen hinzufügen. Der folgende Screenshot zeigt die Liste der Standardattribute.

    ![image](common/default-attributes.png)

1. Darüber hinaus wird von der EasySSO for Jira-Anwendung erwartet, dass in der SAML-Antwort noch einige weitere Attribute zurückgegeben werden (siehe unten). Diese Attribute werden ebenfalls vorab aufgefüllt, Sie können sie jedoch nach Bedarf überprüfen.
    
    | Name | Quellattribut|
    | ---------------| --------- |
    | urn:oid:0.9.2342.19200300.100.1.1 | user.userprincipalname |
    | urn:oid:0.9.2342.19200300.100.1.3 | user.mail |
    | urn:oid:2.16.840.1.113730.3.1.241 | user.displayname |
    | urn:oid:2.5.4.4 | user.surname |
    | urn:oid:2.5.4.42 | user.givenname |
    
    Falls für Ihre Azure AD-Benutzer **sAMAccountName** konfiguriert ist, müssen Sie **urn:oid:0.9.2342.19200300.100.1.1** dem Attribut **sAMAccountName** zuordnen.
    
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** für **Zertifikat (Base64)** oder **Verbundmetadaten-XML** auf den Link **Herunterladen**, und speichern Sie die Datei(en) auf Ihrem Computer. Sie benötigen sie später für die Konfiguration von Jira EasySSO.

    ![Downloadlink für das Zertifikat](media/easysso-for-jira-tutorial/azure-ad-SAML-certificate.png)
    
    Wenn Sie die Konfiguration von EasySSO for Jira manuell mit einem Zertifikat ausführen möchten, müssen Sie außerdem die Werte für **Anmelde-URL** und **Azure AD-Bezeichner** aus dem Abschnitt unten kopieren und auf dem Computer speichern.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf EasySSO for Jira gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **EasySSO for Jira** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-easysso-for-jira-sso"></a>Konfigurieren des einmaligen Anmeldens für EasySSO for Jira

1. Melden Sie sich bei Ihrer Atlassian JIRA-Instanz mit Administratorrechten an, und navigieren Sie zum Abschnitt **Apps verwalten**. 

    ![Apps verwalten](media/easysso-for-jira-tutorial/jira-admin-1.png)

2. Navigieren Sie auf der linken Seite zu **EasySSO**, und klicken Sie auf den Eintrag.

    ![Einfaches einmaliges Anmelden (SSO)](media/easysso-for-jira-tutorial/jira-admin-2.png)

3. Wählen Sie die Option **SAML** aus. Hierdurch gelangen Sie zum Abschnitt „SAML-Konfiguration“.

    ![SAML](media/easysso-for-jira-tutorial/jira-admin-3.png)

4. Wählen Sie oben die Registerkarte **Zertifikate** aus, und der folgende Bildschirm wird angezeigt:

    ![Metadaten-URL](media/easysso-for-jira-tutorial/jira-admin-4.png)

5. Navigieren Sie nun zum **Zertifikat (Base64)** oder zur **Metadatendatei**, das bzw. die Sie weiter oben bei der Konfiguration von **Azure AD-SSO** gespeichert haben. Folgende Möglichkeiten, um fortzufahren, stehen zur Verfügung:

    a. Verwenden Sie die **Metadatendatei** des App-Verbunds, die Sie als lokale Datei auf Ihren Computer heruntergeladen haben. Aktivieren Sie das Optionsfeld **Hochladen**, und befolgen Sie das Dialogfeld „Datei hochladen“, das für Ihr Betriebssystem zutrifft.

    **OR**

    b. Öffnen Sie die **Metadatendatei** des App-Verbunds, um den Inhalt der Datei (in einem beliebigen Text-Editor) anzuzeigen und in die Zwischenablage zu kopieren. Wählen Sie die Option **Eingabe** aus, und fügen Sie den Inhalt der Zwischenablage in das Textfeld ein.

     **OR**

    c. Vollständig manuelle Konfiguration. Öffnen Sie das **Zertifikat (Base64)** des App-Verbunds, um den Inhalt der Datei (in einem beliebigen Text-Editor) anzuzeigen und in die Zwischenablage zu kopieren. Fügen Sie ihn in das Textfeld **IdP-Tokensignaturzertifikate** ein. Navigieren Sie dann zur Registerkarte **Allgemein**, und füllen Sie die Felder **POST-Bindungs-URL** und **Entitäts-ID** mit den entsprechenden Werten für **Anmelde-URL** und **Azure AD-Bezeichner**, die Sie zuvor gespeichert haben, aus.
 
6. Klicken Sie unten auf der Seite auf die Schaltfläche **Speichern**. Sie sehen dann, wie der Inhalt der Metadaten- oder Zertifikatsdatei in die Konfigurationsfelder analysiert wird. Die Jira-Konfiguration von EasySSO ist abgeschlossen.

7. Für eine optimale Testerfahrung navigieren Sie zur Registerkarte **Aussehen und Verhalten**, und aktivieren Sie die Option **SAML-Anmeldeschaltfläche**. Dadurch wird eine separate Schaltfläche auf dem Jira-Anmeldebildschirm aktiviert,die speziell dem End-to-End-Testen Ihrer Azure AD SAML-Integration dient. Sie können diese Schaltfläche aktiviert lassen und außerdem ihre Platzierung, Farbe und Übersetzung für den Produktionsmodus konfigurieren.

    ![Aussehen und Verhalten](media/easysso-for-jira-tutorial/jira-admin-5.png)

    > [!NOTE]
    > Sollten Probleme auftreten, wenden Sie sich an das [EasySSO-Supportteam](mailto:support@techtime.co.nz).

### <a name="create-easysso-for-jira-test-user"></a>Erstellen eines EasySSO for Jira-Testbenutzers

In diesem Abschnitt wird in Jira ein Benutzer mit dem Namen Britta Simon erstellt. EasySSO für Jira unterstützt die Just-In-Time-Benutzerbereitstellung (standardmäßig **deaktiviert**). Um die Benutzerbereitstellung zu aktivieren, müssen Sie die Option **Benutzer bei erfolgreicher Anmeldung erstellen** im Abschnitt „Allgemein“ der EasySSO-Plug-In-Konfiguration explizit aktivieren. Ist ein Benutzer noch nicht in Jira vorhanden, wird nach der Authentifizierung ein neuer Benutzer erstellt.

Wenn Sie die automatische Benutzerbereitstellung bei der ersten Benutzeranmeldung jedoch nicht aktivieren möchten, müssen Benutzer in Back-End-Benutzerverzeichnissen vorhanden sein, die von der Jira-Instanz verwendet werden, wie z. B. „LDAP“ oder „Atlassian Crowd“.

![Benutzerbereitstellung](media/easysso-for-jira-tutorial/jira-admin-6.png)

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

### <a name="idp-initiated-workflow"></a>IdP-initiierter Workflow

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Daraufhin sollten Sie automatisch bei der EasySSO for Jira-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. 

Sie können auch den Microsoft-Bereich „Meine Apps“ verwenden, um die Anwendung in einem beliebigen Modus zu testen. Beim Klicken auf die Kachel „EasySSO for Jira“ in „Meine Apps“ geschieht Folgendes: Wenn Sie die Anwendung im SP-Modus konfiguriert haben, werden Sie zum Initiieren des Anmeldeflows zur Anmeldeseite der Anwendung weitergeleitet. Wenn Sie die Anwendung im IDP-Modus konfiguriert haben, sollten Sie automatisch bei der EasySSO for Jira-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

### <a name="sp-initiated-workflow"></a>SP-initiierter Workflow

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über die Schaltfläche **SAML Login** (SAML-Anmeldung).

![Benutzer-SAML-Anmeldung](media/easysso-for-jira-tutorial/jira-admin-7.png)

In diesem Szenario wird davon ausgegangen, dass Sie auf der Jira EasySSO-Konfigurationsseite auf der Registerkarte **Look & Feel** (Erscheinungsbild) die Schaltfläche **SAML Login** (SAML-Anmeldung) aktiviert haben (siehe oben). Öffnen Sie die Jira-Anmelde-URL im Inkognitomodus des Browsers, um Beeinträchtigungen der vorhandenen Sitzungen zu vermeiden. Klicken Sie auf die Schaltfläche **SAML Login** (SAML-Anmeldung), und Sie werden zum Azure AD-Benutzerauthentifizierungsflow umgeleitet. Wenn der Vorgang erfolgreich abgeschlossen wurde, werden Sie über SAML als authentifizierter Benutzer zurück an Ihre Jira-Instanz umgeleitet.

Möglicherweise wird der folgende Bildschirm angezeigt, nachdem Sie von Azure AD umgeleitet wurden:

![EasySSO-Fehlerbildschirm](media/easysso-for-jira-tutorial/jira-admin-8.png)

In diesem Fall müssen Sie die [Anweisungen auf dieser Seite]( https://techtime.co.nz/display/TECHTIME/EasySSO+How+to+get+the+logs#EasySSOHowtogetthelogs-RETRIEVINGTHELOGS) befolgen, um Zugriff auf die Datei **atlassian-jira.log** zu erhalten. Details zum Fehler können Sie mithilfe der Referenz-ID auf der EasySSO-Fehlerseite ermitteln.

Sollten bei den Protokollmeldungen Probleme auftreten, wenden Sie sich an das [EasySSO-Supportteam](mailto:support@techtime.co.nz).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von EasySSO for Jira können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-aad)
