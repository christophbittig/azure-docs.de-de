---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Check Point Remote Secure Access VPN | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie einmaliges Anmelden zwischen Azure Active Directory und Check Point Remote Secure Access VPN konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/16/2021
ms.author: jeedes
ms.openlocfilehash: e958f8b13c01e895aa4d6485aedd64dd155e69b5
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132334984"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-check-point-remote-secure-access-vpn"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Check Point Remote Secure Access VPN

In diesem Tutorial erfahren Sie, wie Sie Check Point Remote Secure Access VPN in Azure Active Directory (Azure AD) integrieren. Die Integration von Check Point Remote Secure Access VPN in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf Check Point Remote Secure Access VPN besitzt.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Check Point Remote Secure Access VPN anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Check Point Remote Secure Access VPN-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Check Point Remote Secure Access VPN unterstützt **SP**-initiiertes einmaliges Anmelden.

## <a name="adding-check-point-remote-secure-access-vpn-from-the-gallery"></a>Hinzufügen von Check Point Remote Secure Access VPN aus dem Katalog

Zum Konfigurieren der Integration von Check Point Remote Secure Access VPN in Azure AD müssen Sie Check Point Remote Secure Access VPN aus dem Katalog der Liste der verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Check Point Remote Secure Access VPN** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Check Point Remote Secure Access VPN** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-check-point-remote-secure-access-vpn"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Check Point Remote Secure Access VPN

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Check Point Remote Secure Access VPN mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Check Point Remote Secure Access VPN eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Check Point Remote Secure Access VPN die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Check Point Remote Secure Access VPN](#configure-check-point-remote-secure-access-vpn-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.

    1. **[Erstellen eines Check Point Remote Secure Access VPN-Testbenutzers](#create-check-point-remote-secure-access-vpn-test-user)** , um eine Entsprechung von B. Simon in Check Point Remote Secure Access VPN zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist.
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Check Point Remote Secure Access VPN** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Geben Sie im Abschnitt **Grundlegende SAML-Konfiguration** die Werte für die folgenden Felder ein:

    1. Geben Sie im Textfeld **Bezeichner (Entitäts-ID)** eine URL im folgenden Format ein: `https://<GATEWAY_IP>/saml-vpn/spPortal/ACS/ID/<IDENTIFIER_UID>`.

    1. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://<GATEWAY_IP>/saml-vpn/spPortal/ACS/Login/<IDENTIFIER_UID>`

    1. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<GATEWAY_IP>/saml-vpn/`.

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch die tatsächlichen Werte für Bezeichner, Antwort-URL und Anmelde-URL. Diese Werte erhalten Sie vom [Supportteam für den Check Point Remote Secure Access VPN-Client](mailto:support@checkpoint.com). Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

1. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** zu **Verbundmetadaten-XML**, und wählen Sie **Herunterladen** aus, um das Zertifikat herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

1. Kopieren Sie im Abschnitt **Check Point Remote Secure Access VPN einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Check Point Remote Secure Access VPN gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste den Eintrag **Check Point Remote Secure Access VPN** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-check-point-remote-secure-access-vpn-sso"></a>Konfigurieren des einmaligen Anmeldens für Check Point Remote Secure Access VPN

### <a name="configure-an-external-user-profile-object"></a>Konfigurieren eines externen Benutzerprofilobjekts

> [!NOTE]
> Dieser Abschnitt ist nur erforderlich, wenn Sie keine lokales Active Directory (LDAP) verwenden möchten.

**Konfigurieren Sie ein generisches Benutzerprofil im Legacy-SmartDashboard**:

1. Wechseln Sie in SmartConsole zu **Manage & Settings > Blades (Verwalten & Einstellungen > Blätter)** .

1. Klicken Sie im Abschnitt **Mobiler Zugriff** auf **in SmartDashboard konfigurieren**. Das Legacy-SmartDashboard wird geöffnet.

1. Klicken Sie im Bereich **Netzwerkobjekte** auf **Benutzer**.

1. Klicken Sie mit der rechten Maustaste auf ein leeres Leerzeichen, und wählen Sie **Neu > Externes Benutzerprofil > Alle Benutzer abgleichen** aus.

1. Konfigurieren Sie die Eigenschaften des **externen Benutzerprofils**:

    1. Auf der Seite **Allgemeine Eigenschaften**:
        * Lassen Sie im Namensfeld des **externen Benutzerprofils** den Standardnamen `generic`* stehen
        * Legen Sie im Feld **Ablaufdatum** das entsprechende Datum fest

    1. Vorgehensweise auf der Seite **Authentifizierung**:
        * Wählen Sie in der Dropdownliste **Authentifizierungsschema** `undefined` aus
    1. Auf den Seiten **Speicherort**, **Uhrzeit** und **Verschlüsselung**:
        * Konfigurieren Sie andere anwendbare Einstellungen
    1. Klicken Sie auf **OK**.

1. Klicken Sie in der oberen Symbolleiste auf **Aktualisieren** (oder drücken Sie STRG+S).

1. Schließen Sie SmartDashboard.

1. Installieren Sie in SmartConsole die Access Control-Richtlinie.

### <a name="configure-remote-access-vpn"></a>Konfigurieren Sie Remote Access VPN

1. Öffnen Sie das Objekt des entsprechenden Sicherheitsgateways.

1. Aktivieren Sie auf der Seite „Allgemeine Eigenschaften“ das Softwareblatt **IPSec-VPN**.

1. Klicken Sie im linken Baum auf die Seite **IPSec-VPN**.

1. Klicken Sie im Abschnitt **This Security Gateway participates in the following VPN communitys (Dieses Sicherheitsgateway nimmt an den folgenden VPN-Communitys teil)** auf **Hinzufügen** und wählen Sie **Remote Access Community** aus.

1. Klicken Sie im linken Baum auf **VPN-Clients > Remotezugriff**.

1. Aktivieren Sie den **Support-Besuchermodus**.

1. Klicken Sie im linken Baum auf **VPN-Clients > Office-Modus**.

1. Wählen Sie **Office-Modus zulassen** aus und wählen Sie die anwendbare Office-Modusmethode aus.

1. Klicken Sie im linken Baum auf **VPN-Clients > SAML Portaleinstellungen**.

1. Stellen Sie sicher, dass die Haupt-URL den vollqualifizierten Domänennamen des Gateways enthält.
Dieser Domänenname sollte mit einem DNS-Suffix enden, das von Ihrer Organisation registriert wurde.
Beispiel: `https://gateway1.company.com/saml-vpn`

1. Stellen Sie sicher, dass das Zertifikat vom Browser des Endbenutzers als vertrauenswürdig eingestuft wird.

1. Klicken Sie auf **OK**.

### <a name="configure-an-identity-provider-object"></a>Konfigurieren eines Identitätsproviders

1. Gehen Sie für jedes Sicherheitsgateway, das an Remote Access VPN beteiligt ist, die folgenden Schritte aus.

1. Klicken Sie in SmartConsole in der Ansicht **Gateways & Servers** (Gateways und Server) auf **New > More > User/Identity > Identity Provider** (Neu > Mehr > Benutzer/Identität > Identitätsanbieter).

    ![Screenshot: Neuer Identitätsanbieter](./media/check-point-remote-access-vpn-tutorial/identity-provider.png)

1. Gehen Sie im Fenster **New Identity Provider** (Neuer Identitätsanbieter) wie folgt vor:

    ![Screenshot: Abschnitt „Identity Provider“ (Identitätsanbieter)](./media/check-point-remote-access-vpn-tutorial/new-identity-provider.png)

    a. Wählen Sie im Feld **Gateway** das Sicherheitsgateway aus, das die SAML-Authentifizierung ausführen muss.

    b. Wählen Sie im Feld **Dienst** in der Dropdownliste **Remote Access VPN** aus.

    c. Kopieren Sie den Wert für **Identifier (Entity ID)** (Bezeichner (Entitäts-ID)), und fügen Sie diesen Wert im Azure-Portal im Abschnitt **Grundlegende SAML-Konfiguration** in das Textfeld **Bezeichner** ein.

    d. Kopieren Sie den Wert für **Reply URL** (Antwort-URL), und fügen Sie diesen Wert im Azure-Portal im Abschnitt **Grundlegende SAML-Konfiguration** in das Textfeld **Antwort-URL** ein.

    e. Klicken Sie auf **Import Metadata File** (Metadatendatei importieren), und laden Sie die heruntergeladene **Verbundmetadaten-XML** aus dem Azure-Portal hoch.

    > [!NOTE]
    > Alternativ können Sie auch **Insert Manually** (Manuell einfügen) auswählen, um die Werte für **Entitäts-ID** und **Anmelde-URL** in die entsprechenden Felder einzufügen und die **Zertifikatdatei** aus dem Azure-Portal hochzuladen.

    f. Klicken Sie auf **OK**.

### <a name="configure-the-identity-provider-as-an-authentication-method"></a>Konfigurieren des Identitätsanbieters als Authentifizierungsmethode

1. Öffnen Sie das Objekt des entsprechenden Sicherheitsgateways.

1. Auf der Seite **VPN-Clients > Authentifizierung**:

    a. Deaktivieren Sie das Kontrollkästchen **Zulassen, dass ältere Clients eine Verbindung mit diesem Gateway herstellen**.

    b. Fügen Sie ein neues Objekt hinzu oder bearbeiten Sie einen vorhandenen Bereich.

    ![Screenshot für „Hinzufügen eines neuen Objekts“.](./media/check-point-remote-access-vpn-tutorial/add-new-object.png)

1. Geben Sie einen Namen und einen Anzeigenamen ein, und fügen Sie eine Authentifizierungsmethode hinzu bzw. bearbeiten Sie sie: Falls die Anmeldeoption für GWs verwendet wird, die am GWs teilnehmen, sollte der Name mit dem Präfix „SAMLVPN_“ beginnen, um ein reibungsloses Benutzererlebnis zu ermöglichen.

    ![Screenshot zur Anmeldeoption.](./media/check-point-remote-access-vpn-tutorial/login-option.png)

1. Wählen Sie die Option **Identitätsanbieter** aus, klicken Sie auf die grüne `+`-Schaltfläche und wählen Sie das entsprechende Identitätsanbieterobjekt aus.

    ![Screenshot: Auswählen des entsprechenden Identitätsanbieterobjekts.](./media/check-point-remote-access-vpn-tutorial/green-button.png)

1. Im Fenster „Optionen für mehrere Anmeldungen“: Klicken Sie im linken Bereich auf **Benutzerverzeichnisse** und wählen Sie dann **Manuelle Konfiguration** aus.
Es gibt zwei Optionen:
    1. Wenn Sie keine lokale Active Directory (LDAP) verwenden möchten, wählen Sie nur Externe Benutzerprofile aus, und klicken Sie auf OK.
    2. Wenn Sie eine lokale Active Directory (LDAP) verwenden möchten, wählen Sie nur LDAP-Benutzer und im LDAP-Suchtyp die Option E-Mail aus. Klicken Sie dann auf „OK“.

    ![Screenshot der manuellen Konfiguration.](./media/check-point-remote-access-vpn-tutorial/manual-configuration.png)

1. Konfigurieren Sie die erforderlichen Einstellungen in der Verwaltungsdatenbank:

    1. Schließen Sie SmartConsole.

    2. Stellen Sie eine Verbindung mit dem GuiDBEdit-Tool mit dem Verwaltungsserver her (siehe [sk13009](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails&solutionid=sk13009)).

    3. Wechseln Sie im oberen linken Bereich zu **Bearbeiten > Netzwerkobjekte**.

    4. Wählen Sie im oberen rechten Bereich das **Security Gateway-Objekt** aus.

    5. Wechseln Sie im unteren Bereich zu **realms_for_blades > vpn**.

    6. Wenn Sie keine lokales Active Directory (LDAP) verwenden möchten, legen Sie **do_ldap_fetch** auf **false** und **do_generic_fetch** auf **true** fest. Klicken Sie dann auf **OK**. Wenn Sie eine lokale Active Directory (LDAP) verwenden möchten, legen Sie **do_ldap_fetch** auf **true** und **do_generic_fetch** auf **false** fest. Klicken Sie dann auf **OK**.

    7. Wiederholen Sie die Schritte 4 bis 6 für alle anwendbaren Sicherheitsgateways.

    8. Speichern Sie Ihre Änderungen durch Auswahl von **Datei** > **Alle speichern**.

1. Schließen Sie das GuiDBEdit-Tool.

1. Jedes Sicherheitsgateway und jedes Softwareblatt verfügen über separate Einstellungen. Überprüfen Sie die Einstellungen auf jedem Sicherheitsgateway und jedem Softwareblatt, die die Authentifizierung verwenden (VPN, Mobile Access und Identity Awareness).

    * Achten Sie darauf, die Option **LDAP-Benutzer** nur für Softwareblätter auszuwählen, die LDAP verwenden.

    * Stellen Sie sicher, dass Sie die Option **Externe Benutzerprofile** nur für Softwareblätter auswählen, die LDAP nicht verwenden.

1. Installieren Sie die Access Control-Richtlinie auf jedem Sicherheitsgateway.

### <a name="vpn-ra-client-installation-and-configuration"></a>Installieren und Konfigurieren des VPN RA-Client

1. Installieren Sie den VPN-Client.

1. Legen Sie den Identitätsanbieter-Browsermodus fest (optional).

    Standardmäßig verwendet der Windows-Client seinen eingebetteten Browser, und der macOS-Client verwendet Safari für die Authentifizierung im Portal des Identitätsanbieters. Ändern Sie für Windows-Clients dieses Verhalten, damit Sie stattdessen Internet Explorer verwenden können:

    1. Öffnen Sie auf dem Clientcomputer einen Nur-Text-Editor als Administrator.

    2. Öffnen Sie die Datei `trac.defaults` im Text-Editor.

       * Unter 32-Bit-Windows: 

         `%ProgramFiles%\CheckPoint\Endpoint Connect\trac.defaults`

       * Unter 64-Bit-Windows: 

         `%ProgramFiles(x86)%\CheckPoint\Endpoint Connect\trac.defaults`

    3. Ändern Sie den Attributwert `idp_browser_mode` von `embedded` in `IE`.

    4. Speichern Sie die Datei .

    5. Starten Sie den Check Point Endpoint Security-VPN-Clientdienst neu.

   Öffnen Sie die Windows-Eingabeaufforderung als Administrator, und führen Sie die folgenden Befehle aus:

   `# net stop TracSrvWrapper`

   `# net start TracSrvWrapper`

1. Starten Sie die Authentifizierung mit Browser, der im Hintergrund ausgeführt wird:

    1. Öffnen Sie auf dem Clientcomputer einen Nur-Text-Editor als Administrator.

    2. Öffnen Sie die Datei `trac.defaults` im Text-Editor.

        * Unter 32-Bit-Windows: 

          `%ProgramFiles%\CheckPoint\Endpoint Connect\trac.defaults`

        * Unter 64-Bit-Windows: 

          `%ProgramFiles(x86)%\CheckPoint\Endpoint Connect\trac.defaults`

        * Unter MacOS:

          `/Library/Application Support/Checkpoint/Endpoint Security/Endpoint Connect/trac.defaults`

    3. Ändern Sie den Wert von `idp_show_browser_primary_auth_flow` in `false`.

    4. Speichern Sie die Datei .

    5. Starten Sie den Check Point Endpoint Security-VPN-Clientdienst neu.

       * Öffnen Sie auf Windows-Clients die Windows-Eingabeaufforderung als Administrator, und führen Sie die folgenden Befehle aus:

         `# net stop TracSrvWrapper`

         `# net start TracSrvWrapper`

       * Führen Sie auf macOS-Clients Folgendes aus:

         `sudo launchctl stop com.checkpoint.epc.service`

         `sudo launchctl start com.checkpoint.epc.service`

### <a name="create-check-point-remote-secure-access-vpn-test-user"></a>Erstellen eines Check Point Remote Secure Access VPN-Testbenutzers

In diesem Abschnitt wird in Check Point Remote Secure Access VPN ein Benutzer namens Britta Simon erstellt. Arbeiten Sie mit [Check Point Remote Secure Access VPN-Supportteam](mailto:support@checkpoint.com) zusammen, um die Benutzer auf der Check Point Remote Secure Access VPN-Plattform für den Remotezugriff hinzuzufügen. Benutzer müssen erstellt und aktiviert werden, damit Sie einmaliges Anmelden verwenden können.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

1. Öffnen Sie den VPN-Client, und klicken Sie auf **Verbinden mit...** .

    ![Screenshot: Verbinden mit ...](./media/check-point-remote-access-vpn-tutorial/connect.png)

1. Wählen Sie in der Dropdownliste **Site** aus und klicken Sie auf **Verbinden**.

    ![Screenshot: Auswählen der Site.](./media/check-point-remote-access-vpn-tutorial/site.png)

1. Melden Sie sich im Popupfenster Azure AD zur Anmeldung mit den Azure AD Anmeldeinformationen an, die Sie im Abschnitt **Erstellen eines Azure AD-Testbenutzers** erstellt haben.

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Check Point Remote Secure Access VPN können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-any-app)
