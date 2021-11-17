---
title: 'Tutorial: Integration von Citrix ADC in das einmalige Anmelden von Azure Active Directory (headerbasierte Authentifizierung) | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie einmaliges Anmelden (Single Sign-On, SSO) zwischen Azure Active Directory und Citrix ADC mit headerbasierter Authentifizierung konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/16/2020
ms.author: jeedes
ms.openlocfilehash: bb73942776322aff78dc94b04c978a9a2e9c290b
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132296036"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-citrix-adc-header-based-authentication"></a>Tutorial: Integration von Citrix ADC in das einmalige Anmelden von Azure Active Directory (headerbasierte Authentifizierung)

In diesem Tutorial erfahren Sie, wie Sie Citrix ADC in Azure Active Directory (Azure AD) integrieren. Die Integration von Citrix ADC in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf Citrix ADC haben soll.
* Ermöglichen Sie Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Citrix ADC anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Citrix ADC-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung. Das Tutorial umfasst folgende Szenarien:

* **SP-initiiertes** einmaliges Anmelden für Citrix ADC

* **Just-In-Time**-Benutzerbereitstellung für Citrix ADC

* [Headerbasierte Authentifizierung für Citrix ADC](#publish-the-web-server)

* [Kerberos-basierte Authentifizierung für Citrix ADC](citrix-netscaler-tutorial.md#publish-the-web-server)

## <a name="add-citrix-adc-from-the-gallery"></a>Hinzufügen von Citrix ADC aus dem Katalog

Um Citrix ADC in Azure AD integrieren zu können, müssen Sie zunächst Citrix ADC aus dem Katalog zu Ihrer Liste der verwalteten SaaS-Apps hinzufügen:

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.

1. Wählen Sie im linken Menü **Azure Active Directory** aus.

1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen** aus.

1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.

1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** im Suchfeld den Suchbegriff **Citrix ADC** ein.

1. Wählen Sie in den Ergebnissen den Eintrag **Citrix ADC** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-citrix-adc"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Citrix ADC

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD für Citrix ADC mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Citrix ADC eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Citrix ADC die folgenden Schritte aus:

1. [Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso), um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.

    1. [Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user), um das einmalige Anmelden von Azure AD mit B.Simon zu testen.

    1. [Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user), um B.Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.

1. [Konfigurieren des einmaligen Anmeldens für Citrix ADC](#configure-citrix-adc-sso), um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.

    * [Erstellen eines Citrix ADC-Testbenutzers](#create-a-citrix-adc-test-user), um in Citrix ADC eine Entsprechung von B. Simon zu erhalten, die mit der Benutzerdarstellung in Azure AD verknüpft ist.

1. [Testen des einmaligen Anmeldens](#test-sso), um zu überprüfen, ob die Konfiguration funktioniert.

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Führen Sie die folgenden Schritte aus, um einmaliges Anmelden von Azure AD über das Azure-Portal zu aktivieren:

1. Wählen Sie im Azure-Portal im Anwendungsintegrationsbereich für **Citrix ADC** unter **Verwalten** die Option **Einmaliges Anmelden** aus.

1. Wählen Sie im Bereich **SSO-Methode auswählen** die Methode **SAML** aus.

1. Wählen Sie im Bereich **Einmaliges Anmelden (SSO) mit SAML einrichten** für **Grundlegende SAML-Konfiguration** die Option **Bearbeiten** (Stiftsymbol) aus, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Gehen Sie im Abschnitt **Grundlegende SAML-Konfiguration** wie folgt vor, um die Anwendung im **IDP-initiierten** Modus zu konfigurieren:

    1. Geben Sie im Textfeld **Bezeichner** eine URL im folgenden Format ein: `https://<Your FQDN>`

    1. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://<Your FQDN>/CitrixAuthService/AuthService.asmx`

1. Wenn Sie die Anwendung im **SP-initiierten** Modus konfigurieren möchten, wählen Sie **Zusätzliche URLs festlegen** aus, und führen Sie den folgenden Schritt aus:

    * Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<Your FQDN>/CitrixAuthService/AuthService.asmx`

    > [!NOTE]
    > * Die in diesem Abschnitt angegebenen URLs sind lediglich Beispielwerte. Ersetzen Sie diese Werte durch die tatsächlichen Werte für Bezeichner, Antwort-URL und Anmelde-URL. Wenden Sie sich an das [Supportteam für den Citrix ADC-Client](https://www.citrix.com/contact/technical-support.html), um diese Werte zu erhalten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.
    > * Für die SSO-Einrichtung müssen die URLs über öffentliche Websites zugänglich sein. Sie müssen die Firewall- oder andere Sicherheitseinstellungen aufseiten von Citrix ADC aktivieren, um Azure AD die Veröffentlichung des Tokens unter der konfigurierten URL zu ermöglichen.

1. Kopieren Sie im Bereich **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** die URL unter **App-Verbundmetadaten-URL**, und speichern Sie sie in Editor.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

1. Die Citrix ADC-Anwendung erwartet, dass die SAML-Assertionen in einem bestimmten Format vorliegen. Daher müssen Sie Ihrer Konfiguration der SAML-Tokenattribute benutzerdefinierte Attributzuordnungen hinzufügen. Der folgende Screenshot zeigt die Liste der Standardattribute. Wählen Sie das Symbol **Bearbeiten** aus, und ändern Sie die Attributzuordnungen.

    ![Bearbeiten der SAML-Attributzuordnung](common/edit-attribute.png)

1. Von der Citrix ADC-Anwendung wird außerdem die Rückgabe einiger weiterer Attribute in der SAML-Antwort erwartet. Führen Sie im Dialogfeld **Benutzerattribute** unter **Benutzeransprüche** die folgenden Schritte aus, um die SAML-Tokenattribute gemäß der Tabelle hinzuzufügen:

    | Name | Quellattribut|
    | ---------------| --------------- |
    | mySecretID  | user.userprincipalname |
    
    1. Wählen Sie **Neuen Anspruch hinzufügen** aus, um das Dialogfeld **Benutzeransprüche verwalten** zu öffnen.

    1. Geben Sie im Textfeld **Name** den für die Zeile angezeigten Attributnamen ein.

    1. Lassen Sie den **Namespace** leer.

    1. Wählen unter **Attribut** die Option **Quelle** aus.

    1. Geben Sie in der Liste **Quellattribut** den für diese Zeile angezeigten Attributwert ein.

    1. Klicken Sie auf **OK**.

    1. Wählen Sie **Speichern** aus.

1. Kopieren Sie im Abschnitt **Citrix ADC einrichten** die relevanten URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

In diesem Abschnitt erstellen Sie im Azure-Portal einen Testbenutzer mit dem Namen B. Simon.

1. Wählen Sie im linken Menü des Azure-Portals Folgendes aus: **Azure Active Directory** > **Benutzer** > **Alle Benutzer**.

1. Wählen Sie im oberen Bereich die Option **Neuer Benutzer** aus.

1. Führen Sie in den Eigenschaften für **Benutzer** die folgenden Schritte aus:

   1. Geben Sie unter **Name**`B.Simon` ein.  

   1. Geben Sie unter **Benutzername** einen Benutzernamen im Format _username@companydomain.extension_ ein. Beispiel: `B.Simon@contoso.com`.

   1. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**, und notieren Sie sich den im Feld **Kennwort** angezeigten Wert, oder kopieren Sie ihn.

   1. Klicken Sie auf **Erstellen**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Citrix ADC gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.

1. Wählen Sie in der Anwendungsliste die Anwendung **Citrix ADC** aus.

1. Wählen Sie in der App-Übersicht unter **Verwalten** die Option **Benutzer und Gruppen** aus.
1. Klicken Sie auf **Benutzer hinzufügen**. Wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste **Benutzer** die Option **B.Simon** aus. Wählen Sie **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Wählen Sie im Dialogfeld **Zuweisung hinzufügen** die Option **Zuweisen** aus.

## <a name="configure-citrix-adc-sso"></a>Konfigurieren des einmaligen Anmeldens für Citrix ADC

Wählen Sie einen der Links aus, um die Schritte für die Art der Authentifizierung anzuzeigen, die Sie konfigurieren möchten:

- [Konfigurieren des einmaligen Anmeldens für Citrix ADC (headerbasierte Authentifizierung)](#publish-the-web-server)

- [Konfigurieren des einmaligen Anmeldens für Citrix ADC (Kerberos-basierte Authentifizierung)](citrix-netscaler-tutorial.md#publish-the-web-server)

### <a name="publish-the-web-server"></a>Veröffentlichen des Webservers 

So erstellen Sie einen virtuellen Server:

1. Wählen Sie **Traffic Management** > **Load Balancing** > **Services** (Datenverkehrsverwaltung > Lastenausgleich > Dienste) aus.
    
1. Wählen Sie **Hinzufügen**.

    ![Citrix ADC-Konfiguration: Bereich „Dienste“](./media/header-citrix-netscaler-tutorial/web01.png)

1. Legen Sie die folgenden Werte für den Webserver fest, auf dem die Anwendungen ausgeführt werden:

   * **Dienstname**
   * **IP-Adresse des Servers/vorhandener Server**
   * **Protokoll**
   * **Port**

     ![Citrix ADC-Konfigurationsbereich](./media/header-citrix-netscaler-tutorial/web01.png)

### <a name="configure-the-load-balancer"></a>Konfigurieren Sie den Load Balancer.

So konfigurieren Sie den Lastenausgleich:

1. Navigieren Sie zu **Traffic Management** > **Load Balancing** > **Virtual Servers** (Datenverkehrsverwaltung > Lastenausgleich > Virtuelle Server).

1. Wählen Sie **Hinzufügen**.

1. Legen Sie die folgenden Werte wie im bereitgestellten Screenshot fest:

    * **Name**
    * **Protokoll**
    * **IP-Adresse**
    * **Port**

1. Klicken Sie auf **OK**.

    ![Citrix ADC-Konfiguration: Bereich „Grundeinstellungen“](./media/header-citrix-netscaler-tutorial/load01.png)

### <a name="bind-the-virtual-server"></a>Binden des virtuellen Servers

So binden Sie den Lastenausgleich mit dem virtuellen Server:

1. Wählen Sie im Bereich **Services and Service Groups** (Dienste und Dienstgruppen) die Option **No Load Balancing Virtual Server Service Binding** (Keine Dienstbindung für virtuellen Lastenausgleichsserver) aus.

   ![Citrix ADC-Konfiguration: Bereich für die Dienstbindung für den virtuellen Lastenausgleichsserver](./media/header-citrix-netscaler-tutorial/bind01.png)

1. Vergewissern Sie sich, dass die Einstellungen wie im folgenden Screenshot festgelegt sind, und wählen Sie anschließend **Close** (Schließen) aus.

   ![Citrix ADC-Konfiguration: Überprüfen der Dienstbindung für den virtuellen Server](./media/header-citrix-netscaler-tutorial/bind02.png)

### <a name="bind-the-certificate"></a>Binden des Zertifikats

Wenn Sie diesen Dienst als TLS veröffentlichen möchten, muss das Serverzertifikat gebunden und die Anwendung anschließend getestet werden:

1. Wählen Sie unter **Certificate** (Zertifikat) die Option **No Server Certificate** (Kein Serverzertifikat) aus.

   ![Citrix ADC-Konfiguration: Bereich „Serverzertifikat“](./media/header-citrix-netscaler-tutorial/bind03.png)

1. Vergewissern Sie sich, dass die Einstellungen wie im folgenden Screenshot festgelegt sind, und wählen Sie anschließend **Close** (Schließen) aus.

   ![Citrix ADC-Konfiguration: Überprüfen des Zertifikats](./media/header-citrix-netscaler-tutorial/bind04.png)

## <a name="citrix-adc-saml-profile"></a>Citrix ADC-SAML-Profil

Durchlaufen Sie zum Konfigurieren des Citrix ADC-SAML-Profils die folgenden Abschnitte:

### <a name="create-an-authentication-policy"></a>Erstellen einer Authentifizierungsrichtlinie

So erstellen Sie eine Authentifizierungsrichtlinie:

1. Navigieren Sie zu **Security** > **AAA – Application Traffic** > **Policies** > **Authentication** > **Authentication Policies** (Sicherheit > AAA – Anwendungsdatenverkehr > Richtlinien > Authentifizierung > Authentifizierungsrichtlinien).

1. Wählen Sie **Hinzufügen**.

1. Geben Sie im Bereich **Create Authentication Policy** (Authentifizierungsrichtlinie erstellen) die folgenden Werte ein, oder wählen Sie sie aus:

    * **Name**: Geben Sie einen Namen für Ihre Authentifizierungsrichtlinie ein.
    * **Aktion:** Geben Sie **SAML** ein, und wählen Sie **Add** (Hinzufügen) aus.
    * **Expression** (Ausdruck):  Geben Sie **true** ein.     
    
    ![Citrix ADC-Konfiguration: Bereich „Authentifizierungsrichtlinie erstellen“](./media/header-citrix-netscaler-tutorial/policy01.png)

1. Klicken Sie auf **Erstellen**.

### <a name="create-an-authentication-saml-server"></a>Erstellen eines SAML-Authentifizierungsservers

Navigieren Sie zum Erstellen eines SAML-Authentifizierungsservers zum Bereich **Create Authentication SAML Server** (SAML-Authentifizierungsserver erstellen), und führen Sie die folgenden Schritte aus:

1. Geben Sie unter **Name** einen Namen für den SAML-Authentifizierungsserver ein.

1. Gehen Sie unter **Export SAML Metadata** (SAML-Metadaten exportieren) wie folgt vor:

   1. Aktivieren Sie das Kontrollkästchen **Import Metadata** (Metadaten importieren).

   1. Geben Sie die Verbundmetadaten-URL ein, die Sie zuvor auf der Azure-SAML-Benutzeroberfläche kopiert haben.
    
1. Geben Sie unter **Issuer Name** (Ausstellername) die relevante URL ein.

1. Klicken Sie auf **Erstellen**.

![Citrix ADC-Konfiguration: Bereich „SAML-Authentifizierungsserver erstellen“](./media/header-citrix-netscaler-tutorial/server01.png)

### <a name="create-an-authentication-virtual-server"></a>Erstellen eines virtuellen Authentifizierungsservers

So erstellen Sie einen virtuellen Authentifizierungsserver:

1.  Navigieren Sie zu **Security** > **AAA - Application Traffic** > **Policies** > **Authentication** > **Authentication Virtual Servers** (Sicherheit > AAA – Anwendungsdatenverkehr > Richtlinien > Authentifizierung > Virtuelle Authentifizierungsserver).

1.  Wählen Sie **Add** (Hinzufügen) aus, und gehen Sie anschließend wie folgt vor:

    1. Geben Sie unter **Name** einen Namen für den virtuellen Authentifizierungsserver ein.

    1. Aktivieren Sie das Kontrollkästchen **Non-Addressable** (Nicht adressierbar).

    1. Wählen Sie unter **Protocol** (Protokoll) die Option **SSL** aus.

    1. Klicken Sie auf **OK**.

    ![Citrix ADC-Konfiguration: Bereich „Virtueller Authentifizierungsserver“](./media/header-citrix-netscaler-tutorial/server02.png)
    
### <a name="configure-the-authentication-virtual-server-to-use-azure-ad"></a>Konfigurieren des virtuellen Authentifizierungsservers für die Verwendung von Azure AD

Ändern Sie zwei Abschnitte für den virtuellen Authentifizierungsserver:

1.  Wählen Sie im Bereich **Advanced Authentication Policies** (Erweiterte Authentifizierungsrichtlinien) die Option **No Authentication Policy** (Keine Authentifizierungsrichtlinie) aus.

    ![Citrix ADC-Konfiguration: Bereich „Erweiterte Authentifizierungsrichtlinien“](./media/header-citrix-netscaler-tutorial/virtual01.png)

1. Wählen Sie im Bereich **Policy Binding** (Richtlinienbindung) die Authentifizierungsrichtlinie und anschließend **Bind** (Binden) aus.

    ![Citrix ADC-Konfiguration: Bereich „Richtlinienbindung“](./media/header-citrix-netscaler-tutorial/virtual02.png)

1. Wählen Sie im Bereich **Form Based Virtual Servers** (Formularbasierte virtuelle Server) die Option **No Load Balancing Virtual Server** (Kein virtueller Lastenausgleichsserver) aus.

    ![Citrix ADC-Konfiguration: Bereich „Formularbasierte virtuelle Server“](./media/header-citrix-netscaler-tutorial/virtual03.png)

1. Geben Sie unter **Authentication FQDN** (FQDN für die Authentifizierung) einen vollqualifizierten Domänennamen (Fully Qualified Domain Name, FQDN) ein. (Diese Angabe ist erforderlich.)

1. Wählen Sie den virtuellen Lastenausgleichsserver aus, den Sie mittels Azure AD-Authentifizierung schützen möchten.

1. Wählen Sie **Bind** (Binden) aus.

    ![Citrix ADC-Konfiguration: Bereich zum Binden des virtuellen Lastenausgleichsservers](./media/header-citrix-netscaler-tutorial/virtual04.png)

    > [!NOTE]
    > Wählen Sie im Bereich **Authentication Virtual Server Configuration** (Konfiguration des virtuellen Authentifizierungsservers) die Option **Done** (Fertig) aus.

1. Navigieren Sie zum Überprüfen Ihrer Änderungen in einem Browser zur Anwendungs-URL. Daraufhin sollte anstelle des vorherigen nicht authentifizierten Zugriffs Ihre Mandantenanmeldeseite angezeigt werden.

    ![Citrix ADC-Konfiguration: Anmeldeseite in einem Webbrowser](./media/header-citrix-netscaler-tutorial/virtual05.png)

## <a name="configure-citrix-adc-sso-for-header-based-authentication"></a>Konfigurieren des einmaligen Anmeldens für Citrix ADC (headerbasierte Authentifizierung)

### <a name="configure-citrix-adc"></a>Konfigurieren von Citrix ADC

Durchlaufen Sie die folgenden Abschnitte, wenn Sie Citrix ADC für die headerbasierte Authentifizierung konfigurieren möchten:

#### <a name="create-a-rewrite-action"></a>Erstellen einer Aktion zum erneuten Generieren

1. Navigieren Sie zu **AppExpert** > **Rewrite** > **Rewrite Actions** (AppExpert > Erneut generieren > Aktionen zum erneuten Generieren).
 
    ![Citrix ADC-Konfiguration: Bereich mit Aktionen zum erneuten Generieren](./media/header-citrix-netscaler-tutorial/header01.png)

1.  Wählen Sie **Add** (Hinzufügen) aus, und gehen Sie anschließend wie folgt vor:

    1. Geben Sie unter **Name** einen Namen für die Aktion zum erneuten Generieren ein.

    1. Geben Sie unter **Type** (Typ) die Zeichenfolge **INSERT_HTTP_HEADER** ein.

    1. Geben Sie unter **Header Name** (Headername) einen Headernamen ein (in diesem Beispiel: _SecretID_).

    1. Geben Sie unter **Expression** (Ausdruck) den Ausdruck **aaa.USER.ATTRIBUTE("mySecretID")** ein. **mySecretID** ist hierbei der an Citrix ADC gesendete Azure AD-SAML-Anspruch.

    1. Klicken Sie auf **Erstellen**.

    ![Citrix ADC-Konfiguration: Bereich zum Erstellen von Aktionen zum erneuten Generieren](./media/header-citrix-netscaler-tutorial/header02.png)
 
#### <a name="create-a-rewrite-policy"></a>Erstellen einer Richtlinie zum erneuten Generieren

1.  Navigieren Sie zu **AppExpert** > **Rewrite** > **Rewrite Policies** (AppExpert > Erneut generieren > Richtlinien zum erneuten Generieren).
 
    ![Citrix ADC-Konfiguration: Bereich „Richtlinien zum erneuten Generieren“](./media/header-citrix-netscaler-tutorial/header03.png)

1.  Wählen Sie **Add** (Hinzufügen) aus, und gehen Sie anschließend wie folgt vor:

    1. Geben Sie unter **Name** einen Namen für die Richtlinie zum erneuten Generieren ein.

    1. Wählen Sie unter **Action** (Aktion) die Aktion zum erneuten Generieren aus, die Sie im vorherigen Abschnitt erstellt haben.

    1. Geben Sie unter **Expression** (Ausdruck) den Ausdruck **true** ein.

    1. Klicken Sie auf **Erstellen**.

    ![Citrix ADC-Konfiguration: Bereich „Richtlinien zum erneuten Generieren erstellen“](./media/header-citrix-netscaler-tutorial/header04.png)

### <a name="bind-a-rewrite-policy-to-a-virtual-server"></a>Binden einer Richtlinie zum erneuten Generieren an einen virtuellen Server

So binden Sie eine Richtlinie zum erneuten Generieren unter Verwendung der grafischen Benutzeroberfläche an einen virtuellen Server:

1. Navigieren Sie zu **Traffic Management** > **Load Balancing** > **Virtual Servers** (Datenverkehrsverwaltung > Lastenausgleich > Virtuelle Server).

1. Wählen Sie in der Liste mit den virtuellen Servern den virtuellen Server aus, an den Sie die Richtlinie zum erneuten Generieren binden möchten, und wählen Sie anschließend **Open** (Öffnen) aus.

1. Wählen Sie im Bereich **Load Balancing Virtual Server** (Virtueller Lastenausgleichsserver) unter **Advanced Settings** (Erweiterte Einstellungen) die Option **Policies** (Richtlinien) aus. Die Liste enthält alle für Ihre NetScaler-Instanz konfigurierten Richtlinien.
 
    ![Screenshot: Registerkarte „Configuration“ (Konfiguration), auf der die Felder „Name“, „Action“ (Aktion) und „Expression“ (Ausdruck) hervorgehoben sind und die Schaltfläche „Create“ (Erstellen) ausgewählt ist](./media/header-citrix-netscaler-tutorial/header05.png)

    ![Citrix ADC-Konfiguration: Bereich „Virtueller Lastenausgleichsserver“](./media/header-citrix-netscaler-tutorial/header06.png)

1.  Aktivieren Sie das Kontrollkästchen neben dem Namen der Richtlinie, die Sie an diesen virtuellen Server binden möchten.
 
    ![Citrix ADC-Konfiguration: Bereich zum Binden der Datenverkehrsrichtlinie für den virtuellen Lastenausgleichsserver](./media/header-citrix-netscaler-tutorial/header08.png)

1. Gehen Sie im Dialogfeld **Choose Type** (Typ auswählen) wie folgt vor:

    1. Wählen Sie unter **Choose Policy** (Richtlinie auswählen) die Option **Traffic** (Datenverkehr) aus.

    1. Wählen Sie unter **Choose Type** (Typ auswählen) die Option **Request** (Anforderung) aus.

    ![Citrix ADC-Konfiguration: Dialogfeld „Richtlinien“](./media/header-citrix-netscaler-tutorial/header07.png)

1.  Klicken Sie auf **OK**. Auf der Statusleiste wird eine Meldung mit dem Hinweis angezeigt, dass die Richtlinie erfolgreich konfiguriert wurde.

### <a name="modify-the-saml-server-to-extract-attributes-from-a-claim"></a>Ändern des SAML-Servers, um Attribute aus einem Anspruch zu extrahieren

1.  Navigieren Sie zu **Security** > **AAA - Application Traffic** > **Policies** > **Authentication** > **Advanced Policies** > **Actions** > **Servers** (Sicherheit > AAA – Anwendungsdatenverkehr > Richtlinien > Authentifizierung > Erweiterte Richtlinien > Aktionen > Server).

1.  Wählen Sie den entsprechenden SAML-Authentifizierungsserver für die Anwendung aus.
 
    ![Citrix ADC-Konfiguration: Bereich „SAML-Authentifizierungsserver konfigurieren“](./media/header-citrix-netscaler-tutorial/header09.png)

1. Geben Sie im Bereich **Attributes** (Attribute) die zu extrahierenden SAML-Attribute als kommagetrennte Liste ein. In unserem Beispiel verwenden wir das Attribut `mySecretID`.
 
    ![Citrix ADC-Konfiguration: Bereich „Attribute“](./media/header-citrix-netscaler-tutorial/header10.png)

1. Suchen Sie in einem Browser unter der URL nach dem SAML-Attribut (unter **Headers Collection** (Headerauflistung)), um den Zugriff zu überprüfen.

    ![Citrix ADC-Konfiguration: Headerauflistung unter der URL](./media/header-citrix-netscaler-tutorial/header11.png)

### <a name="create-a-citrix-adc-test-user"></a>Erstellen eines Citrix ADC-Testbenutzers

In diesem Abschnitt wird in Citrix ADC ein Benutzer mit dem Namen B. Simon erstellt. Citrix ADC unterstützt die Just-in-Time-Benutzerbereitstellung, die standardmäßig aktiviert ist. In diesem Abschnitt ist keine Aktion erforderlich. Wenn ein Benutzer in Citrix ADC noch nicht vorhanden ist, wird nach der Authentifizierung ein neuer Benutzer erstellt.

> [!NOTE]
> Wenn Sie einen Benutzer manuell erstellen müssen, wenden Sie sich an das [Supportteam für den Citrix ADC-Client](https://www.citrix.com/contact/technical-support.html).

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für Citrix ADC weitergeleitet, wo Sie den Anmeldeflow initiieren können. 

* Navigieren Sie direkt zur Anmelde-URL für Citrix ADC, und initiieren Sie dort den Anmeldeflow.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie in „Meine Apps“auf die Kachel „Citrix ADC“ klicken, werden Sie zur Anmelde-URL für Citrix ADC weitergeleitet. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).


## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Citrix ADC können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-any-app)
