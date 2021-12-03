---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory in Kemp LoadMaster Azure AD integration | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie einmaliges Anmelden zwischen Azure Active Directory und Kemp LoadMaster Azure AD integration konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/30/2021
ms.author: jeedes
ms.openlocfilehash: d0b2f4d4af71f964752161973cd086fa679a8a68
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132285312"
---
# <a name="tutorial-azure-active-directory-sso-integration-with-kemp-loadmaster-azure-ad-integration"></a>Tutorial: Azure Active Directory-SSO-Integration in Kemp LoadMaster Azure AD integration

In diesem Tutorial erfahren Sie, wie Sie Kemp LoadMaster Azure AD integration in Azure Active Directory (Azure AD) integrieren. Die Integration von Kemp LoadMaster Azure AD integration in Azure AD ermöglicht Folgendes:

* Sie können in Azure AD steuern, wer Zugriff auf Kemp LoadMaster Azure AD integration haben soll.
* Sie können es Ihren Benutzern ermöglichen, sich mit ihrem Azure AD-Konto automatisch bei Kemp LoadMaster Azure AD integration anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* SSO-fähiges Abonnement für Kemp LoadMaster Azure AD integration

> [!NOTE]
> Diese Integration kann auch über die Azure AD-Umgebung für die US Government-Cloud verwendet werden. Sie finden diese Anwendung im Azure AD-Katalog für US Government-Cloudanwendungen und konfigurieren sie auf die gleiche Weise wie in der öffentlichen Cloud.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Kemp LoadMaster Azure AD integration unterstützt **IDP**-initiiertes einmaliges Anmelden.

## <a name="add-kemp-loadmaster-azure-ad-integration-from-the-gallery"></a>Hinzufügen von Kemp LoadMaster Azure AD integration über den Katalog

Um die Integration von Kemp LoadMaster Azure AD integration in Azure AD zu konfigurieren, müssen Sie Kemp LoadMaster Azure AD integration über den Katalog Ihrer Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Kemp LoadMaster Azure AD integration** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Kemp LoadMaster Azure AD integration** aus, und fügen Sie die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-kemp-loadmaster-azure-ad-integration"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Kemp LoadMaster Azure AD integration

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Kemp LoadMaster Azure AD integration unter Verwendung eines Testbenutzers namens **B.Simon**. Damit das einmalige Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Kemp LoadMaster Azure AD integration eingerichtet werden.

Führen Sie die folgenden Schritte aus, um das einmalige Anmelden von Azure AD mit Kemp LoadMaster Azure AD integration zu konfigurieren und zu testen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Kemp LoadMaster Azure AD integration](#configure-kemp-loadmaster-azure-ad-integration-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.

1. **[Veröffentlichen des Webservers](#publishing-web-server)**
    1. **[Erstellen eines virtuellen Diensts](#create-a-virtual-service)**
    1. **[Zertifikate und Sicherheit](#certificates-and-security)**
    1. **[SAML-Profil für Kemp LoadMaster Azure AD integration](#kemp-loadmaster-azure-ad-integration-saml-profile)**
    1. **[Überprüfen der Änderungen](#verify-the-changes)**
1. **[Konfigurieren der Kerberos-basierten Authentifizierung](#configuring-kerberos-based-authentication)**
    1. **[Erstellen eines Kerberos-Delegierungskontos für Kemp LoadMaster Azure AD integration](#create-a-kerberos-delegation-account-for-kemp-loadmaster-azure-ad-integration)**
    1. **[KCD (Kerberos-Delegierungskonten) für Kemp LoadMaster Azure AD integration](#kemp-loadmaster-azure-ad-integration-kcd-kerberos-delegation-accounts)**
    1. **[ESP für Kemp LoadMaster Azure AD integration](#kemp-loadmaster-azure-ad-integration-esp)**
    1. **[Erstellen eines Testbenutzers für Kemp LoadMaster Azure AD integration](#create-kemp-loadmaster-azure-ad-integration-test-user)** , um in Kemp LoadMaster Azure AD integration eine Entsprechung von B.Simon zu erhalten, die mit der Benutzerdarstellung in Azure AD verknüpft ist.
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Kemp LoadMaster Azure AD integration** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Geben Sie im Abschnitt **Grundlegende SAML-Konfiguration** die Werte für die folgenden Felder ein:

    a. Geben Sie im Textfeld **Bezeichner (Entitäts-ID)** eine URL ein: `https://<KEMP-CUSTOMER-DOMAIN>.com/`.

    b. Geben Sie im Textfeld **Antwort-URL** eine URL ein: `https://<KEMP-CUSTOMER-DOMAIN>.com/`.

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Aktualisieren Sie diese Werte mit dem eigentlichen Bezeichner und der Antwort-URL. Wenden Sie sich an das [Kundensupportteam von Kemp LoadMaster Azure AD integration](mailto:support@kemp.ax), um diese Werte zu erhalten. Sie können sich auch die Muster im Abschnitt „Grundlegende SAML-Konfiguration“ im Azure-Portal ansehen.

1. Suchen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** nach **Zertifikat (Base64)** sowie nach **Verbundmetadaten-XML**, und wählen Sie anschließend **Herunterladen** aus, um die Zertifikat- und die Verbundmetadaten-XML-Datei herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](./media/kemp-tutorial/certificate-base-64.png)

1. Kopieren Sie im Abschnitt **Kemp LoadMaster Azure AD integration einrichten** die entsprechenden URLs basierend auf Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B.Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie dem Benutzer Zugriff auf Kemp LoadMaster Azure AD integration gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste die Option **Kemp LoadMaster Azure AD integration** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-kemp-loadmaster-azure-ad-integration-sso"></a>Konfigurieren des einmaligen Anmeldens für Kemp LoadMaster Azure AD integration

## <a name="publishing-web-server"></a>Veröffentlichen des Webservers 

### <a name="create-a-virtual-service"></a>Erstellen eines virtuellen Diensts 

1. Navigieren Sie auf der LoadMaster-Webbenutzeroberfläche von Kemp LoadMaster Azure AD integration zu „Virtual Services“ (Virtuelle Dienste) > „Add New“ (Neu hinzufügen).

1. Klicken Sie auf „Add New“ (Neu hinzufügen).

1. Geben Sie die Parameter für den virtuellen Dienst an.

    ![Screenshot: Seite mit der Aufforderung zum Angeben der Parameter für den virtuellen Dienst und mit Beispielwerten in den Feldern](./media/kemp-tutorial/kemp-1.png)

    a. Virtuelle Adresse
    
    b. Port
    
    c. Dienstname (optional)
    
    d. Protokoll 

1. Navigieren Sie zum Abschnitt „Real Servers“ (Echte Server).

1. Klicken Sie auf „Add New“ (Neu hinzufügen).

1. Geben Sie die Parameter für den echten Server an.
    
    ![Screenshot: Seite mit der Aufforderung zum Angeben der Parameter für den echten Server und mit Beispielwerten in den Feldern](./media/kemp-tutorial/kemp-2.png)

    a. Aktivieren Sie das Kontrollkästchen, um Remoteadressen zuzulassen.
    
    b. Geben Sie die Adresse des echten Servers ein.
    
    c. Port
    
    d. Weiterleitungsmethode
    
    e. Gewichtung
    
    f. Verbindungslimit
    
    g. Klicken Sie auf „Add This Real Server“ (Diesen echten Server hinzufügen).

## <a name="certificates-and-security"></a>Zertifikate und Sicherheit
 
### <a name="import-certificate-on-kemp-loadmaster-azure-ad-integration"></a>Importieren des Zertifikats in Kemp LoadMaster Azure AD integration 
 
1. Navigieren Sie im Webportal für Kemp LoadMaster Azure AD integration zu „Certificates & Security“ (Zertifikate und Sicherheit) > „SSL Certificates“ (SSL-Zertifikate).

1. Navigieren Sie zu „Manage Certificates“ (Zertifikate verwalten) > „Certificate Configuration“ (Zertifikatkonfiguration).

1. Klicken Sie auf „Import Certificate“ (Zertifikat importieren).

1. Geben Sie den Namen der Datei an, die das Zertifikat enthält. Die Datei kann auch den privaten Schlüssel enthalten. Sollte der private Schlüssel nicht in der Datei enthalten sein, muss auch die Datei mit dem privaten Schlüssel angegeben werden. Zulässige Zertifikatformate sind PEM und PFX (IIS).

1. Klicken Sie in der Dateiauswahl auf „Certificate File“ (Zertifikatdatei).

1. Optional: Klicken Sie auf „Key File“ (Schlüsseldatei).

1. Klicken Sie auf „Save“ (Speichern).

### <a name="ssl-acceleration"></a>SSL-Beschleunigung
 
1. Navigieren Sie auf der Webbenutzeroberfläche von Kemp LoadMaster zu „Virtual Services“ (Virtuelle Dienste) > „View/Modify Services“ (Dienste anzeigen/ändern).

1. Klicken Sie unter „Operation“ (Betrieb) auf „Modify“ (Ändern).

1. Klicken Sie auf „SSL Properties“ (SSL-Eigenschaften) (Layer 7).
    
    ![Screenshot: Abschnitt „SSL Properties“ (SSL-Eigenschaften) mit Auswahl der Option „SSL Acceleration“ > „Enabled“ (SSL-Beschleunigung > Aktiviert) und eines Beispielzertifikats](./media/kemp-tutorial/kemp-3.png)
    
    a. Klicken Sie unter „SSL Acceleration“ (SSL-Beschleunigung) auf „Enabled“ (Aktiviert).
    
    b. Wählen Sie unter „Available Certificates“ (Verfügbare Zertifikate) das importierte Zertifikat aus, und klicken Sie auf das Symbol `>`.
    
    c. Wenn das gewünschte SSL-Zertifikat im Bereich für zugewiesene Zertifikate angezeigt wird, klicken Sie auf **Set Certificates** (Zertifikate festlegen).

    > [!NOTE]
    > Klicken Sie unbedingt auf **Set Certificates** (Zertifikate festlegen).

## <a name="kemp-loadmaster-azure-ad-integration-saml-profile"></a>SAML-Profil für Kemp LoadMaster Azure AD integration
 
### <a name="import-idp-certificate"></a>Importieren des IdP-Zertifikats

Navigieren Sie zur Webkonsole für Kemp LoadMaster Azure AD integration. 

1. Klicken Sie unter „Certificates and Security“ (Zertifikate und Sicherheit) auf „Intermediate Certs“ (Zwischenzertifikate).

    ![Screenshot: Abschnitt mit den derzeit installierten Zwischenzertifikaten mit Auswahl eines Beispielzertifikats](./media/kemp-tutorial/kemp-6.png)

    a. Klicken Sie unter „Add a new Intermediate Certificate“ (Neues Zwischenzertifikat hinzufügen) auf „Choose File“ (Datei auswählen).
    
    b. Navigieren Sie zu der Zertifikatdatei, die Sie zuvor aus der Azure AD-Unternehmensanwendung heruntergeladen haben.
    
    c. Klicken Sie auf „Open“ (Öffnen).
    
    d. Geben Sie einen Zertifikatnamen an.
    
    e. Klicken Sie auf „Add Certificate“ (Zertifikat hinzufügen).

### <a name="create-authentication-policy"></a>Erstellen der Authentifizierungsrichtlinie 
 
Navigieren Sie unter „Virtual Services“ (Virtuelle Dienste) zu „Manage SSO“ (SSO verwalten).

   ![Screenshot: Seite „Manage SSO“ (SSO verwalten)](./media/kemp-tutorial/kemp-7.png)
   
   a. Klicken Sie unter „Add new Client Side Configuration“ (Neue clientseitige Konfiguration hinzufügen) auf „Add“ (Hinzufügen), nachdem Sie einen Namen angegeben haben.

   b. Wählen Sie unter „Authentication Protocol“ (Authentifizierungsprotokoll) die Option „SAML“ aus.

   c. Wählen Sie unter „IdP Provisioning“ (IdP-Bereitstellung) die Option „MetaData File“ (Metadatendatei) aus. 

   d. Klicken Sie auf „Choose File“ (Datei auswählen).

   e. Navigieren Sie zu der XML-Datei, die Sie zuvor aus dem Azure-Portal heruntergeladen haben.

   f. Klicken Sie auf „Open“ (Öffnen) und anschließend auf „Import IdP MetaData file“ (IdP-Metadatendatei importieren).

   g. Wählen Sie unter „IdP Certificate“ (IdP-Zertifikat) das Zwischenzertifikat aus.

   h. Legen Sie die SP-Entitäts-ID fest. Sie muss der im Azure-Portal erstellten Identität entsprechen. 

   i. Klicken Sie auf „Set SP Entity ID“ (SP-Entitäts-ID festlegen).

### <a name="set-authentication"></a>Festlegen der Authentifizierung  
 
Führen Sie in der Webkonsole für Kemp LoadMaster Azure AD integration die folgenden Schritte aus.

1. Klicken Sie auf „Virtual Services“ (Virtuelle Dienste).

1. Klicken Sie auf „View/Modify Services“ (Dienste anzeigen/ändern).

1. Klicken Sie auf „Modify“ (Ändern), und navigieren Sie zu den ESP-Optionen.
    
    ![Screenshot: Seite „View/Modify Services“ (Dienste anzeigen/ändern) mit den erweiterten Abschnitten „ESP Options“ (ESP-Optionen) und „Real Servers“ (Echte Server)](./media/kemp-tutorial/kemp-8.png)

    a. Klicken Sie auf „Enable ESP“ (ESP aktivieren).
    
    b. Legen Sie den Clientauthentifizierungsmodus auf „SAML“ fest.
    
    c. Wählen Sie unter „SSO Domain“ (SSO-Domäne) die zuvor erstellte clientseitige Authentifizierung aus.
    
    d. Geben Sie unter „Allowed Virtual Hosts“ (Zulässige virtuelle Hosts) einen Hostnamen ein, und klicken Sie auf „Set Allowed Virtual Hosts“ (Zulässige virtuelle Hosts festlegen).
    
    e. Geben Sie „/*“ in das Feld „Allowed Virtual Directories“ (Zulässige virtuelle Verzeichnisse) ein (basierend auf den Zugriffsanforderungen), und klicken Sie auf „Set Allowed Directories“ (Zulässige Verzeichnisse festlegen).

### <a name="verify-the-changes"></a>Überprüfen der Änderungen 
 
Navigieren Sie zur Anwendungs-URL. 

Daraufhin sollte anstelle des vorherigen nicht authentifizierten Zugriffs Ihre Mandantenanmeldeseite angezeigt werden. 

![Screenshot: Mandantenanmeldeseite](./media/kemp-tutorial/kemp-9.png)

## <a name="configuring-kerberos-based-authentication"></a>Konfigurieren der Kerberos-basierten Authentifizierung 
 
### <a name="create-a-kerberos-delegation-account-for-kemp-loadmaster-azure-ad-integration"></a>Erstellen eines Kerberos-Delegierungskontos für Kemp LoadMaster Azure AD integration 

1. Erstellen Sie ein Benutzerkonto (in diesem Beispiel: AppDelegation).
    
    ![Screenshot: Fenster mit den Eigenschaften des KCD-Benutzers und Auswahl der Registerkarte „Konto“](./media/kemp-tutorial/kemp-10.png)


    a. Wählen Sie die Registerkarte „Attribut-Editor“ aus.

    b. Navigieren Sie zu „servicePrincipalName“. 

    c. Wählen Sie „servicePrincipalName“ aus, und klicken Sie auf „Bearbeiten“.

    d. Geben Sie im Feld „Hinzuzufügender Wert“ die Zeichenfolge „http/kcduser“ ein, und klicken Sie anschließend auf „Hinzufügen“. 

    e. Klicken Sie auf „Übernehmen“ und anschließend auf „OK“. Das Fenster muss geschlossen und erneut geöffnet werden, damit die neue Registerkarte „Delegierung“ angezeigt wird. 

1. Öffnen Sie das Fenster mit den Benutzereigenschaften erneut. Nun ist die Registerkarte „Delegierung“ verfügbar. 

1. Wählen Sie die Registerkarte „Delegierung“ aus.

    ![Screenshot: Fenster mit den Eigenschaften des KCD-Benutzers und Auswahl der Registerkarte „Delegierung“](./media/kemp-tutorial/kemp-11.png)

    a. Klicken Sie auf „Benutzer bei Delegierungen angegebener Dienste vertrauen“.

    b. Wählen Sie „Beliebiges Authentifizierungsprotokoll verwenden“ aus.

    c. Fügen Sie die echten Server sowie „http“ als Dienst hinzu. 
    
    d. Aktivieren Sie das Kontrollkästchen „Erweitert“. 

    e. Daraufhin werden alle Server mit Hostname und vollqualifiziertem Domänennamen angezeigt.
    
    f. Klicken Sie auf „OK“.

> [!NOTE]
> Legen Sie den SPN für die Anwendung/Website entsprechend fest, um auf die Anwendung zuzugreifen, wenn die Anwendungspoolidentität festgelegt wurde. Wenn Sie unter Verwendung des vollqualifizierten Domänennamens auf die IIS-Anwendung zugreifen möchten, navigieren Sie zur Eingabeaufforderung für echte Server, und geben Sie „SetSpn“ mit den erforderlichen Parametern ein. Beispiel: Setspn –S HTTP/sescoindc.sunehes.co.in suneshes\kdcuser 

### <a name="kemp-loadmaster-azure-ad-integration-kcd-kerberos-delegation-accounts"></a>KCD (Kerberos-Delegierungskonten) für Kemp LoadMaster Azure AD integration 

Navigieren Sie in der Webkonsole für Kemp LoadMaster Azure AD integration zu „Virtual Services“ (Virtuelle Dienste) > „Manage SSO“ (SSO verwalten).

![Screenshot: „Manage SSO“ > „Manage Domain“ (SSO verwalten > Domäne verwalten)](./media/kemp-tutorial/kemp-12.png)

a. Navigieren Sie zu den serverseitigen SSO-Konfigurationen.

b. Geben Sie unter „Add new Server-Side Configuration“ (Neue serverseitige Konfiguration hinzufügen) einen Namen ein, und klicken Sie anschließend auf „Add“ (Hinzufügen).

c. Wählen Sie unter „Authentication Protocol“ (Authentifizierungsprotokoll) die Option „Kerberos Constrained Delegation“ (Eingeschränkte Kerberos-Delegierung) aus.

d. Geben Sie unter „Kerberos Realm“ (Kerberos-Bereich) den Domänennamen ein.

e. Klicken Sie auf „Set Kerberos realm“ (Kerberos-Bereich festlegen).

f. Geben Sie unter „Kerberos Key Distribution Center“ (Kerberos-Schlüsselverteilungscenter) die IP-Adresse des Domänencontrollers ein.

g. Klicken Sie auf „Set Kerberos KDC“ (Kerberos-KDC festlegen).

h. Geben Sie unter „Kerberos Trusted User Name“ (Vertrauenswürdiger Kerberos-Benutzername) den KCD-Benutzernamen ein.

i. Klicken Sie auf „Set KDC trusted user name“ (Vertrauenswürdigen KDC-Benutzernamen festlegen).

j. Geben Sie unter „Kerberos Trusted User Password“ (Kennwort des vertrauenswürdigen Kerberos-Benutzers) das Kennwort ein.

k. Klicken Sie auf „Set KCD trusted user password“ (Kennwort des vertrauenswürdigen KCD-Benutzers festlegen).

### <a name="kemp-loadmaster-azure-ad-integration-esp"></a>ESP für Kemp LoadMaster Azure AD integration 

Navigieren Sie zu „Virtual Services“ (Virtuelle Dienste) > „View/Modify Services“ (Dienste anzeigen/ändern).

![Kemp LoadMaster Azure AD integration: Webserver](./media/kemp-tutorial/kemp-13.png)

a. Klicken Sie für den Spitznamen des virtuellen Diensts auf „Modify“ (Ändern).
    
b. Klicken Sie auf „ESP Options“ (ESP-Optionen).
    
c. Wählen Sie unter „Server Authentication Mode“ (Serverauthentifizierungsmodus) die Option „KCD“ aus.
        
d. Wählen Sie unter „Server Side configuration“ (Serverseitige Konfiguration) das zuvor erstellte serverseitige Profil aus.

### <a name="create-kemp-loadmaster-azure-ad-integration-test-user"></a>Erstellen eines Testbenutzers für Kemp LoadMaster Azure AD integration

In diesem Abschnitt wird in Kemp LoadMaster Azure AD integration ein Benutzer namens B.Simon erstellt. Arbeiten Sie mit dem [Clientsupportteam von Kemp LoadMaster Azure AD integration](mailto:support@kemp.ax) zusammen, um Benutzer zur Plattform von Kemp LoadMaster Azure AD integration hinzuzufügen. Benutzer müssen erstellt und aktiviert werden, damit Sie einmaliges Anmelden verwenden können.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen:

* Klicken Sie im Azure-Portal auf „Diese Anwendung testen“. Dadurch sollten Sie automatisch bei der Instanz von Kemp LoadMaster Azure AD integration angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie unter „Meine Apps“ auf die Kachel „Kemp LoadMaster Azure AD integration“ klicken, sollten Sie automatisch bei der Instanz von Kemp LoadMaster Azure AD integration angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Kemp LoadMaster Azure AD integration können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-any-app)
