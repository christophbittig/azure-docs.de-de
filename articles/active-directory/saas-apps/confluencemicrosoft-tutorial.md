---
title: 'Tutorial: Integration des einmaligen Anmeldens von Azure Active Directory mit Confluence SAML SSO by Microsoft | Microsoft-Dokumentation'
description: In diesem Artikel erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Confluence SAML SSO by Microsoft konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/07/2021
ms.author: jeedes
ms.openlocfilehash: 2c76e4b2c463e309be637cc8a0dd5e5ea2e3efc2
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132280645"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-confluence-saml-sso-by-microsoft"></a>Tutorial: Integration des einmaligen Anmeldens von Azure Active Directory mit Confluence SAML SSO by Microsoft

In diesem Tutorial erfahren Sie, wie Sie Confluence SAML SSO by Microsoft in Azure Active Directory (Azure AD) integrieren. Die Integration von Confluence SAML SSO by Microsoft in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf Confluence SAML SSO by Microsoft hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Confluence SAML SSO by Microsoft anzumelden (einmaliges Anmelden; Single Sign-On, SSO).
* Verwalten Sie Ihre Konten zentral im Azure-Portal.


## <a name="description"></a>Beschreibung:

Verwenden Sie Ihr Microsoft Azure Active Directory-Konto mit einem Atlassian Confluence-Server, um einmaliges Anmelden zu ermöglichen. Auf diese Weise können sich alle Benutzer in Ihrer Organisation mit den Azure AD-Anmeldeinformationen bei der Confluence-Anwendung anmelden. Dieses Plug-In nutzt SAML 2.0 für den Verbund.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit Confluence SAML SSO by Microsoft konfigurieren zu können, benötigen Sie Folgendes:

- Ein Azure AD-Abonnement
- Eine Confluence-Serveranwendung, die auf einem 64-Bit-Server unter Windows installiert ist (lokal oder in der Cloud-IaaS-Infrastruktur)
- Einen HTTPS-fähigen Confluence-Server
- Beachten Sie, dass die unterstützten Versionen für das Confluence-Plug-In weiter unten aufgeführt werden.
- Der Confluence-Server sollte über das Internet erreichbar sein, insbesondere zur Authentifizierung über die Azure AD-Anmeldeseite. Außerdem sollte er in der Lage sein, das Token von Azure AD zu erhalten.
- In Confluence eingerichtete Administratoranmeldeinformationen
- WebSudo in Confluence deaktiviert
- Einen in der Confluence-Serveranwendung erstellten Testbenutzer

> [!NOTE]
> Zum Testen der Schritte in diesem Tutorial wird empfohlen, keine Produktionsumgebung von Confluence zu verwenden. Testen Sie die Integration zuerst in der Entwicklungs- oder Stagingumgebung der Anwendung, und verwenden Sie erst danach die Produktionsumgebung.

> [!NOTE]
> Diese Integration kann auch über die Azure AD-Umgebung für die US Government-Cloud verwendet werden. Sie finden diese Anwendung im Azure AD-Katalog für US Government-Cloudanwendungen und konfigurieren sie auf die gleiche Weise wie in der öffentlichen Cloud.

Für die ersten Schritte benötigen Sie Folgendes:

* Verwenden Sie die Produktionsumgebung nur, wenn dies unbedingt erforderlich ist.
* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Abonnement für Confluence SAML SSO by Microsoft, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="supported-versions-of-confluence"></a>Unterstützte Confluence-Versionen

Zum aktuellen Zeitpunkt werden die folgenden Confluence-Versionen unterstützt:

- Confluence: 5.0 bis 5.10
- Confluence: 6.0.1 bis 6.15.9
- Confluence: 7.0.1 bis 7.10.0

> [!NOTE]
> Beachten Sie, dass unser Confluence-Plug-In auch unter Ubuntu Version 16.04 funktioniert.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Confluence SAML SSO by Microsoft unterstützt **SP**-initiiertes einmaliges Anmelden.

## <a name="adding-confluence-saml-sso-by-microsoft-from-the-gallery"></a>Hinzufügen von Confluence SAML SSO by Microsoft aus dem Katalog

Zum Konfigurieren der Integration von Confluence SAML SSO by Microsoft in Azure AD müssen Sie Confluence SAML SSO by Microsoft aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Confluence SAML SSO by Microsoft** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Confluence SAML SSO by Microsoft** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-confluence-saml-sso-by-microsoft"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Confluence SAML SSO by Microsoft

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Confluence SAML SSO by Microsoft mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Confluence SAML SSO by Microsoft eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Confluence SAML SSO by Microsoft die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Confluence SAML SSO by Microsoft](#configure-confluence-saml-sso-by-microsoft-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines Confluence SAML SSO by Microsoft-Testbenutzers](#create-confluence-saml-sso-by-microsoft-test-user)** , um in Confluence SAML SSO by Microsoft eine Entsprechung von B. Simon zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Confluence SAML SSO by Microsoft** zu **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Geben Sie im Abschnitt **Grundlegende SAML-Konfiguration** die Werte für die folgenden Felder ein:

    a. Geben Sie im Feld **Bezeichner** eine URL im folgenden Format ein: `https://<DOMAIN:PORT>/`.

    b. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://<DOMAIN:PORT>/plugins/servlet/saml/auth`
    
    c. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<DOMAIN:PORT>/plugins/servlet/saml/auth`

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch den tatsächlichen Bezeichner, die Antwort-URL und die Anmelde-URL. Der Port ist optional, sofern es sich um eine benannte URL handelt. Diese Werte werden während der Konfiguration des Confluence-Plug-Ins empfangen, die später im Tutorial beschrieben wird.

1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf die Schaltfläche „Kopieren“, um die **App-Verbundmetadaten-URL** zu kopieren, und speichern Sie sie auf Ihrem Computer.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Confluence SAML SSO by Microsoft gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Confluence SAML SSO by Microsoft** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-confluence-saml-sso-by-microsoft-sso"></a>Konfigurieren des einmaligen Anmeldens für Confluence SAML SSO by Microsoft

1. Melden Sie sich in einem anderen Webbrowserfenster bei Ihrer Confluence-Instanz als Administrator an.

1. Fahren Sie mit dem Mauszeiger über das Zahnrad, und klicken Sie auf die **Add-Ons**.

    ![Screenshot, in dem das „Zahnrad“-Symbol ausgewählt und im Dropdownmenü die Option „Add-ons“ hervorgehoben ist](./media/confluencemicrosoft-tutorial/add-on-1.png)

1. Laden Sie das Plug-In aus dem [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=56503) herunter. Laden Sie das von Microsoft bereitgestellte Plug-In manuell mithilfe des Menüs **Add-On hochladen** hoch. Das Herunterladen von Plug-Ins wird unter [Microsoft-Servicevertrag](https://www.microsoft.com/servicesagreement/) beschrieben.

    ![Screenshot der Seite „Manage add-ons“ mit ausgewählter Aktion „Upload add-on“](./media/confluencemicrosoft-tutorial/add-on-12.png)

1. Führen Sie die folgenden Schritte aus, um das Reverseproxy- oder Lastenausgleichsszenario von Confluence auszuführen:

    > [!NOTE]
    > Sie sollten den Server zunächst mit den folgenden Anweisungen konfigurieren und dann das Plug-In installieren.

    a. Fügen Sie das folgende Attribut in **connector**-Port in der **server.xml**-Datei der JIRA-Serveranwendung ein.

    `scheme="https" proxyName="<subdomain.domain.com>" proxyPort="<proxy_port>" secure="true"`

    ![Screenshot der Datei „server.xml“, in der das Attribut zum „Connector“-Port hinzugefügt ist](./media/confluencemicrosoft-tutorial/reverse-proxy-1.png)

    b. Ändern Sie **Basis-URL** in **Systemeinstellungen** gemäß Proxy/Lastenausgleich.

    ![Screenshot der Seite „Administration – Settings“ mit hervorgehobener „Base URL“](./media/confluencemicrosoft-tutorial/reverse-proxy-2.png)

1. Sobald das Plug-In installiert ist, wird es im Abschnitt **Add-Ons verwalten** unter **User Installed** (Vom Benutzer installiert) angezeigt. Klicken Sie auf **Konfigurieren**, um das neue Plug-In zu konfigurieren.

    ![Screenshot des Abschnitts „User Installed“ mit hervorgehobener Schaltfläche „Configure“](./media/confluencemicrosoft-tutorial/add-on-15.png)

1. Führen Sie auf der Konfigurationsseite die folgenden Schritte aus:

    ![Screenshot mit der Konfigurationsseite für einmaliges Anmelden](./media/confluencemicrosoft-tutorial/add-on-53.png)

    > [!TIP]
    > Stellen Sie sicher, dass der App nur ein Zertifikat zugeordnet wird, um einen Fehler bei der Auflösung der Metadaten zu vermeiden. Bei mehreren Zertifikaten erhält der Administrator nach dem Auflösen der Metadaten eine Fehlermeldung.

    1. Fügen Sie im Textfeld **Metadaten-URL** den Wert für die **Verbundmetadaten-URL der App** ein, den Sie aus dem Azure-Portal kopiert haben, und klicken Sie auf die Schaltfläche **Auflösen**. Die IdP-Metadaten-URL wird gelesen, und alle Felder werden aufgefüllt.

    1. Kopieren Sie die Werte von **Bezeichner, Antwort-URL und Anmelde-URL**. Fügen Sie sie anschließend in die Textfelder **Bezeichner, Antwort-URL und Anmelde-URL** bzw. im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ein.

    1. Geben Sie in **Login Button Name** (Name der Anmeldeschaltfläche) den Schaltflächennamen ein, der auf dem Anmeldebildschirm für Ihre Benutzer angezeigt werden soll.

    1. Geben Sie unter **Login Button Description** (Beschreibung der Anmeldeschaltfläche) die Schaltflächenbeschreibung ein, die auf dem Anmeldebildschirm für Ihre Benutzer angezeigt werden soll.

    1. Wählen Sie unter **SAML User ID Locations** (Speicherorte der SAML-Benutzer-ID) entweder die Option **User ID is in the NameIdentifier element of the Subject statement** (Benutzer-ID ist im NameIdentifier-Element der Subject-Anweisung enthalten) oder **User ID is in an Attribute element** (Benutzer-ID ist in einem Attribute-Element enthalten) aus.  Diese ID muss die Confluence-Benutzer-ID sein. Wenn die Benutzer-ID nicht übereinstimmt, ist eine Anmeldung nicht möglich. 

       > [!Note]
       > Der Standardspeicherort der SAML-Benutzer-ID ist „Name Identifier“. Sie können dies in ein Attribut ändern und den entsprechenden Attributnamen eingeben.

    1. Wenn Sie die Option **User ID is in an Attribute element** (Benutzer-ID ist in einem Attribute-Element enthalten) auswählen, geben Sie im Textfeld **Attributname** den Namen des Attributs ein, in dem die Benutzer-ID erwartet wird. 

    1. Wenn Sie die Verbunddomäne von Azure AD (z.B. AD FS usw.) verwenden, klicken Sie auf die Option **Startbereichserkennung aktivieren**, und konfigurieren Sie den **Domänennamen**.

    1. Geben Sie in **Domänenname** den Domänennamen für den Fall einer auf AD FS basierenden Anmeldung ein.

    1. Setzen Sie ein Häkchen bei **Einmaliges Abmelden aktivieren**, wenn ein Benutzer beim Abmelden von Confluence bei Azure AD abgemeldet werden soll. 

    1. Aktivieren Sie das Kontrollkästchen **Force Azure Login** (Azure-Anmeldung erzwingen), wenn Sie für die Anmeldung ausschließlich Azure AD-Anmeldeinformationen verwenden möchten.

       > [!Note]
       > Fügen Sie den Abfrageparameter in der Browser-URL hinzu, um das Standardanmeldeformular für die Administratoranmeldung auf der Anmeldeseite zu aktivieren, wenn „Force Azure Login“ (Azure-Anmeldung erzwingen) aktiviert ist.
       > `https://<DOMAIN:PORT>/login.action?force_azure_login=false`

    1. Klicken Sie auf die Schaltfläche **Save**, um die Änderungen zu speichern.

       > [!NOTE]
       > Weitere Informationen zur Installation und Problembehandlung finden Sie im [Administratorhandbuch zum MS Confluence SSO-Connector](./ms-confluence-jira-plugin-adminguide.md). Es enthält außerdem [FAQs](./ms-confluence-jira-plugin-adminguide.md) zur weiteren Unterstützung.

### <a name="create-confluence-saml-sso-by-microsoft-test-user"></a>Erstellen eines Confluence SAML SSO by Microsoft-Testbenutzers

Um es Azure AD-Benutzern zu ermöglichen, sich auf dem lokalen Confluence-Server anzumelden, müssen sie in Confluence SAML SSO by Microsoft bereitgestellt werden. Bei Confluence SAML SSO by Microsoft ist die Bereitstellung eine manuelle Aufgabe.

**Führen Sie zum Bereitstellen eines Benutzerkontos die folgenden Schritte aus:**

1. Melden Sie sich bei Ihrem lokalen Confluence-Server als Administrator an.

1. Fahren Sie mit dem Mauszeiger über das Zahnrad, und klicken Sie auf **Benutzerverwaltung**.

    ![Mitarbeiter hinzufügen](./media/confluencemicrosoft-tutorial/user-1.png)

1. Klicken Sie im Abschnitt „Benutzer“ auf die Registerkarte **Benutzer hinzufügen**. Führen Sie auf der Dialogfeldseite **Benutzer hinzufügen** die folgenden Schritte aus:

    ![Screenshot des Fensters „Confluence administration“ mit ausgewählter Registerkarte „Add Users“ und eingegebenen „Add a User“-Informationen](./media/confluencemicrosoft-tutorial/user-2.png)

    a. Geben Sie im Textfeld **Username** (Benutzername) die E-Mail-Adresse des Benutzers ein, etwa von B. Simon.

    b. Geben Sie im Textfeld **Full Name** (Vollständiger Name) den vollständigen Namen des Benutzers ein, etwa von B. Simon.

    c. Geben Sie im Textfeld **E-Mail-Adresse** die E-Mail-Adresse des Benutzers, z.B. B.Simon@contoso.com, ein.

    d. Geben Sie im Textfeld **Password** (Kennwort) das Kennwort für B. Simon ein.

    e. Klicken Sie auf **Kennwort bestätigen**, um das Kennwort erneut einzugeben.

    f. Klicken Sie auf die Schaltfläche **Hinzufügen**.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für Confluence SAML SSO by Microsoft weitergeleitet, wo Sie den Anmeldeflow initiieren können. 

* Rufen Sie direkt die Anmelde-URL für Confluence SAML SSO by Microsoft auf, und initiieren Sie den Anmeldeflow.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie in „Meine Apps“ auf die Kachel „Confluence SAML SSO by Microsoft“ klicken, werden Sie zur Anmelde-URL für Confluence SAML SSO by Microsoft weitergeleitet. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Confluence SAML SSO by Microsoft können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-aad)
