---
title: 'Tutorial: Integration des einmaligen Anmeldens von Azure Active Directory mit F5 | Microsoft-Dokumentation'
description: In diesem Artikel lernen Sie die Schritte kennen, die Sie zum Integrieren von F5 in Azure Active Directory (Azure AD) ausführen müssen.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/11/2019
ms.author: jeedes
ms.openlocfilehash: 908a7e86c14f87a8cf59d62a3290c1b9ce6eb149
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124817839"
---
# <a name="tutorial-azure-active-directory-ad-single-sign-on-sso-integration-with-f5"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory (AD) mit F5

In diesem Tutorial erfahren Sie, wie Sie F5 in Azure Active Directory (Azure AD) integrieren. Die Integration von F5 in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf F5 hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei F5 anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* F5-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

F5 unterstützt **SP- und IDP-initiiertes** einmaliges Anmelden.

Einmaliges Anmelden von F5 kann auf drei unterschiedliche Arten konfiguriert werden:

- [Konfigurieren des einmaligen Anmeldens von F5 für eine Advanced Kerberos-Anwendung](#configure-f5-single-sign-on-for-advanced-kerberos-application)

- [Konfigurieren des einmaligen Anmeldens von F5 für eine headerbasierte Anwendung](headerf5-tutorial.md)

- [Konfigurieren des einmaligen Anmeldens von F5 für eine Kerberos-Anwendung](kerbf5-tutorial.md)

## <a name="adding-f5-from-the-gallery"></a>Hinzufügen von F5 aus dem Katalog

Zum Konfigurieren der Integration von F5 in Azure AD müssen Sie F5 aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **F5** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **F5** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-single-sign-on-for-f5"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für F5

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit F5 mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in F5 eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit F5 die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für F5](#configure-f5-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines F5-Testbenutzers](#create-f5-test-user)** , um eine Entsprechung von B. Simon in F5 zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **F5** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Bearbeitungs- bzw. Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Geben Sie im Abschnitt **Grundlegende SAML-Konfiguration** die Werte in die folgenden Felder ein, wenn Sie die Anwendung im **IDP**-initiierten Modus konfigurieren möchten:

    a. Geben Sie im Textfeld **Bezeichner** eine URL im folgenden Format ein: `https://<YourCustomFQDN>.f5.com/`

    b. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://<YourCustomFQDN>.f5.com/`

1. Klicken Sie auf **Zusätzliche URLs festlegen**, und führen Sie den folgenden Schritt aus, wenn Sie die Anwendung im **SP-initiierten Modus** konfigurieren möchten:

    Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<YourCustomFQDN>.f5.com/`

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Sie müssen diese Werte mit dem tatsächlichen Bezeichner, der Antwort-URL und der Anmelde-URL aktualisieren. Diese Werte erhalten Sie vom [Supportteam für den F5-Client](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45). Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

1. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** zu **Verbundmetadaten-XML**, und wählen Sie **Herunterladen** aus, um das Zertifikat herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

1. Kopieren Sie im Abschnitt **F5 einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf F5 gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **F5** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.

   ![Link „Benutzer und Gruppen“](common/users-groups-blade.png)

1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Link „Benutzer hinzufügen“](common/add-assign-user.png)

1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** die entsprechende Rolle für den Benutzer in der Liste aus, und klicken Sie dann im unteren Bildschirmbereich auf die Schaltfläche **Auswählen**.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-f5-sso"></a>Konfigurieren des einmaligen Anmeldens für F5

- [Konfigurieren des einmaligen Anmeldens von F5 für eine headerbasierte Anwendung](headerf5-tutorial.md)

- [Konfigurieren des einmaligen Anmeldens von F5 für eine Kerberos-Anwendung](kerbf5-tutorial.md)

### <a name="configure-f5-single-sign-on-for-advanced-kerberos-application"></a>Konfigurieren des einmaligen Anmeldens von F5 für eine Advanced Kerberos-Anwendung

1. Melden Sie sich in einem neuen Webbrowserfenster bei der F5-Unternehmenswebsite (Advanced Kerberos) als Administrator an, und führen Sie die folgenden Schritte aus:

1. Sie müssen das Metadatenzertifikat in F5 (Advanced Kerberos) importieren. Es wird später im Setupvorgang verwendet. Navigieren Sie zu **System > Certificate Management > Traffic Certificate Management > SSL Certificate List** (System > Zertifikatverwaltung > Zertifikatverwaltung für Datenverkehr > SSL-Zertifikatliste). Klicken Sie in der rechten Ecke auf **Import** (Importieren).

    ![Screenshot: Hervorgehobene Schaltfläche „Import“ (Importieren) zum Importieren des Metadatenzertifikats](./media/advance-kerbf5-tutorial/configure01.png)
 
1. Navigieren Sie zum Einrichten des SAML-IDP zu **Access > Federation > SAML Service Provider > Create > From Metadata** (Zugriff > Verbund > SAML-Dienstanbieter > Erstellen > Aus Metadaten).

    ![Screenshot: Erstellen des SAML-Identitätsanbieters anhand von Metadaten](./media/advance-kerbf5-tutorial/configure02.png)

    ![Screenshot: Anzeige „Create New SAML IdP Connector“ (Neuen SAML-IdP-Connector erstellen)](./media/advance-kerbf5-tutorial/configure03.png)
 
    ![F5-Konfiguration (Advanced Kerberos)](./media/advance-kerbf5-tutorial/configure04.png)

    ![Screenshot: Anzeige „Single Sign On Service Settings“ (Diensteinstellung für einmaliges Anmelden) ](./media/advance-kerbf5-tutorial/configure05.png)
 
1. Geben Sie das in Schritt 3 hochgeladene Zertifikat an.

    ![Screenshot: Anzeige „Edit SAML IdP Connector“ (SAML-IdP-Connector bearbeiten)](./media/advance-kerbf5-tutorial/configure06.png)

    ![Screenshot: Anzeige „Single Logout Service Settings“ (Diensteinstellung für einmaliges Abmelden)](./media/advance-kerbf5-tutorial/configure07.png)

 1. Navigieren Sie zum Einrichten des SAML-Dienstanbieters zu **Access > Federation > SAML Service Federation > Local SP Services > Create** (Zugriff > Verbund > SAML-Dienstverbund > Lokale SP-Dienste > Erstellen).

    ![Screenshot: Anzeige zum Erstellen eines lokalen Dienstanbieterdiensts](./media/advance-kerbf5-tutorial/configure08.png)
 
1. Klicken Sie auf **OK**.

1. Wählen Sie die SP-Konfiguration aus, und klicken Sie auf **Bind/UnBind IdP Connectors** (IdP-Connectors binden/Bindung der IdP-Connectors aufheben).

     ![Screenshot: SAML-Dienstanbieter](./media/advance-kerbf5-tutorial/configure09.png)
 
 
1. Klicken Sie auf **Add New Row** (Neue Zeile hinzufügen), und wählen Sie den im vorherigen Schritt erstellten **externen IdP-Connector** aus.

    ![Screenshot: Schaltfläche „Add New Row“ (Neue Zeile hinzufügen)](./media/advance-kerbf5-tutorial/configure10.png)
 
1. Wählen Sie zum Konfigurieren des einmaligen Anmeldens für Kerberos **Access > Single Sign-on > Kerberos** (Zugriff > Einmaliges Anmelden > Kerberos) aus.

    >[!Note]
    >Das Kerberos-Delegierungskonto muss erstellt und angegeben werden. Informationen finden Sie im Abschnitt zu KCD. (Variablenverweise finden Sie im Anhang.)

    •   Username Source (Quelle des Benutzernamens): `session.saml.last.attr.name.http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

    •   User Realm Source (Quelle des Benutzerbereichs): `session.logon.last.domain`

    ![Screenshot: Optionen „Access“ > „Single Sign On“ (Zugriff > Einmaliges Anmelden)](./media/advance-kerbf5-tutorial/configure11.png)

1. Wählen Sie zum Konfigurieren des Zugriffsprofils **Access > Profile/Policies > Access Profile (per session policies)** (Zugriff > Profil/Richtlinien > Zugriffsprofil (Richtlinien pro Sitzung)) aus.

    ![Screenshot: Registerkarte „Eigenschaften“ unter der Menüoption „Profiles/Policies“ (Profile/Richtlinien)](./media/advance-kerbf5-tutorial/configure12.png)

    ![Screenshot: Registerkarte „SSO/Auth Domains“ (Einmaliges Anmelden/Authentifizierungsdomänen)](./media/advance-kerbf5-tutorial/configure13.png)

    ![Screenshot: Registerkarte „Zugriffsrichtlinie“](./media/advance-kerbf5-tutorial/configure14.png)

    ![Screenshot: Registerkarte „Eigenschaften“ in der Zugriffsrichtlinie](./media/advance-kerbf5-tutorial/configure15.png)

    ![Screenshot: Eigenschaften für das Zuweisen von Variablen](./media/advance-kerbf5-tutorial/configure16.png)
 
    * session.logon.last.usernameUPN   expr {[mcget {session.saml.last.identity}]}

    * session.ad.lastactualdomain  TEXT superdemo.live

    ![Screenshot: Eigenschaften für AD-Abfragen](./media/advance-kerbf5-tutorial/configure17.png)

    * (userPrincipalName=%{session.logon.last.usernameUPN})

    ![Screenshot: Registerkarte „Branchregeln“ und die Regel „Konto prüfen“](./media/advance-kerbf5-tutorial/configure18.png)

    ![Screenshot: Textfelder für benutzerdefinierte Variablen und benutzerdefinierte Ausdrücke](./media/advance-kerbf5-tutorial/configure19.png)

    * session.logon.last.username  expr { "[mcget {session.ad.last.attr.sAMAccountName}]" }

    ![Screenshot: Werte in den Feldern „SSO Token Name“ und „SSO Token Password“ (SSO-Tokenname und SSO-Tokenkennwort)](./media/advance-kerbf5-tutorial/configure20.png)

    * mcget {session.logon.last.username}
    * mcget {session.logon.last.password}

1. Navigieren Sie zum Hinzufügen eines neuen Knotens zu **Local Traffic > Nodes > Node List > +** (Lokaler Datenverkehr > Knoten > Knotenliste > +).

    ![Screenshot: „Local Traffic“ > „Nodes“ (Lokaler Datenverkehr > Knoten)](./media/advance-kerbf5-tutorial/configure21.png)
 
1. Navigieren Sie zum Erstellen eines neuen Pools zu **Local Traffic > Pools > Pool List > Create** (Lokaler Datenverkehr > Pools > Poolliste > Erstellen).

     ![Screenshot: „Local Traffic“ > „Pools“ (Lokaler Datenverkehr > Pools)](./media/advance-kerbf5-tutorial/configure22.png)

 1. Navigieren Sie zum Erstellen eines neuen virtuellen Servers zu **Local Traffic > Virtual Servers > Virtual Server List > +** (Lokaler Datenverkehr > Virtuelle Server > Liste virtueller Server > +).

    ![Screenshot: „Local Traffic“ > „Virtual Servers“ (Lokaler Datenverkehr > Virtuelle Server)](./media/advance-kerbf5-tutorial/configure23.png)

1. Geben Sie das im vorherigen Schritt erstellte Zugriffsprofil an.

    ![Screenshot: Angeben des von Ihnen erstellten Zugriffsprofils](./media/advance-kerbf5-tutorial/configure24.png) 

### <a name="setting-up-kerberos-delegation"></a>Einrichten der Kerberos-Delegierung 

>[!Note]
>Ausführlichere Informationen finden Sie [hier](https://www.f5.com/pdf/deployment-guides/kerberos-constrained-delegation-dg.pdf).

* **Schritt 1: Erstellen eines Delegierungskontos**

    * Beispiel
    ```
    Domain Name : superdemo.live
    Sam Account Name : big-ipuser

    New-ADUser -Name "APM Delegation Account" -UserPrincipalName host/big-ipuser.superdemo.live@superdemo.live -SamAccountName "big-ipuser" -PasswordNeverExpires $true -Enabled $true -AccountPassword (Read-Host -AsSecureString "Password!1234")
    ```

* **Schritt 2: Festlegen des SPN (für das APM-Delegierungskonto)**

    *  Beispiel
    ```
    setspn –A host/big-ipuser.superdemo.live big-ipuser
    ```

* **Schritt 3: SPN-Delegierung (für das App Service-Konto)**

    * Richten Sie die entsprechende Delegierung für das F5-Delegierungskonto ein.
    * Im folgenden Beispiel wird das APM-Delegierungskonto für KCD für die App „FRP-App1.superdemo.live“ konfiguriert:

        ![Screenshot: Registerkarte „Delegierung“ unter „APM Delegatio Account Properties“ (APM-Delegierungskontoeigenschaften)](./media/advance-kerbf5-tutorial/configure25.png)

1. Geben Sie die Details wie in [diesem Referenzdokument](https://techdocs.f5.com/kb/en-us/products/big-ip_apm/manuals/product/apm-authentication-single-sign-on-11-5-0/2.html) beschrieben ein.

1. Appendix- SAML – F5 BIG-IP Variable mappings shown below:

    ![Screenshot: Registerkarte „Aktive Sitzungen“ unter „Übersicht“](./media/advance-kerbf5-tutorial/configure26.png)

    ![Screenshot: Variablen und Sitzungsschlüssel](./media/advance-kerbf5-tutorial/configure27.png) 

1. Nachfolgend sehen Sie die vollständige Liste der SAML-Standardattribute. „GivenName“ wird mit der folgenden Zeichenfolge dargestellt:
`session.saml.last.attr.name.http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

| Sitzung | attribute |
| -- | -- |
| eb46b6b6.session.saml.last.assertionID | `<TENANT ID>` |
| eb46b6b6.session.saml.last.assertionIssueInstant  | `<ID>` |
| eb46b6b6.session.saml.last.assertionIssuer | `https://sts.windows.net/<TENANT ID>`/ |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.microsoft.com/claims/authnmethodsreferences | `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password` |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.microsoft.com/identity/claims/displayname | user0 |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.microsoft.com/identity/claims/identityprovider | `https://sts.windows.net/<TENANT ID>/` |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.microsoft.com/identity/claims/objectidentifier | `<TENANT ID>` |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.microsoft.com/identity/claims/tenantid | `<TENANT ID>` |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress | `user0@superdemo.live` |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname | user0 |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.xmlsoap.org/ws/2005/05/identity/claims/name | `user0@superdemo.live` |
| eb46b6b6.session.saml.last.attr.name.http:\//schemas.xmlsoap.org/ws/2005/05/identity/claims/surname | 0 |
| eb46b6b6.session.saml.last.audience | `https://kerbapp.superdemo.live` |
| eb46b6b6.session.saml.last.authNContextClassRef | urn:oasis:names:tc:SAML:2.0:ac:classes:Password |
| eb46b6b6.session.saml.last.authNInstant | `<ID>` |
| eb46b6b6.session.saml.last.identity | `user0@superdemo.live` |
| eb46b6b6.session.saml.last.inResponseTo | `<TENANT ID>` |
| eb46b6b6.session.saml.last.nameIDValue | `user0@superdemo.live` |
| eb46b6b6.session.saml.last.nameIdFormat | urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress |
| eb46b6b6.session.saml.last.responseDestination | `https://kerbapp.superdemo.live/saml/sp/profile/post/acs` |
| eb46b6b6.session.saml.last.responseId | `<TENANT ID>` |
| eb46b6b6.session.saml.last.responseIssueInstant | `<ID>` |
| eb46b6b6.session.saml.last.responseIssuer | `https://sts.windows.net/<TENANT ID>/` |
| eb46b6b6.session.saml.last.result | 1 |
| eb46b6b6.session.saml.last.samlVersion | 2.0 |
| eb46b6b6.session.saml.last.sessionIndex | `<TENANT ID>` |
| eb46b6b6.session.saml.last.statusValue | urn:oasis:names:tc:SAML:2.0:status:Success |
| eb46b6b6.session.saml.last.subjectConfirmDataNotOnOrAfter | `<ID>` |
| eb46b6b6.session.saml.last.subjectConfirmDataRecipient | `https://kerbapp.superdemo.live/saml/sp/profile/post/acs` |
| eb46b6b6.session.saml.last.subjectConfirmMethod | urn:oasis:names:tc:SAML:2.0:cm:bearer |
| eb46b6b6.session.saml.last.validityNotBefore | `<ID>` |
| eb46b6b6.session.saml.last.validityNotOnOrAfter | `<ID>` |

### <a name="create-f5-test-user"></a>Erstellen eines F5-Testbenutzers

In diesem Abschnitt erstellen Sie in F5 einen Benutzer namens B. Simon. Wenden Sie sich an das [Supportteam für den F5-Client](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45), um die Benutzer auf der F5-Plattform hinzuzufügen. Benutzer müssen erstellt und aktiviert werden, damit Sie einmaliges Anmelden verwenden können. 

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „F5“ klicken, sollten Sie automatisch bei der F5-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Liste mit den Tutorials zur Integration von SaaS-Apps in Azure Active Directory](./tutorial-list.md)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Was ist der bedingte Zugriff in Azure Active Directory?](../conditional-access/overview.md)

- [F5 mit Azure AD ausprobieren](https://aad.portal.azure.com/)

- [Konfigurieren des einmaligen Anmeldens von F5 für eine headerbasierte Anwendung](headerf5-tutorial.md)

- [Konfigurieren des einmaligen Anmeldens von F5 für eine Kerberos-Anwendung](kerbf5-tutorial.md)

- [F5 BIG-IP APM und Azure AD-Integration für sicheren Hybridzugriff](../manage-apps/f5-aad-integration.md)

- [Tutorial zum Bereitstellen von VMs mit F5 BIG-IP Virtual Edition in Azure IaaS für sicheren Hybridzugriff](../manage-apps/f5-bigip-deployment-guide.md)

- [Tutorial für die Integration des einmaligen Anmeldens von Azure Active Directory mit F5 BIG-IP für ein VPN ohne Kennwort](../manage-apps/f5-aad-password-less-vpn.md)