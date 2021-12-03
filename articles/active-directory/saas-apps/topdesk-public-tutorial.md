---
title: 'Tutorial: Azure Active Directory-Integration mit TOPdesk - Public | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und TOPdesk - Public konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/08/2021
ms.author: jeedes
ms.openlocfilehash: 54afb9b36df429702ee9a17c30fbad43c53e3b91
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132282447"
---
# <a name="tutorial-azure-active-directory-integration-with-topdesk---public"></a>Tutorial: Azure Active Directory-Integration mit TOPdesk - Public

In diesem Tutorial erfahren Sie, wie Sie TOPdesk - Public in Azure Active Directory (Azure AD) integrieren. Die Integration von TOPdesk - Public in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf TOPdesk - Public hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei TOPdesk - Public anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* TOPdesk - Public-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* TOPdesk - Public unterstützt **SP**-initiiertes einmaliges Anmelden.

## <a name="add-topdesk---public-from-the-gallery"></a>Hinzufügen von TOPdesk - Public aus dem Katalog

Zum Konfigurieren der Integration von TOPdesk - Public in Azure AD müssen Sie TOPdesk - Public aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **TOPdesk - Public** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **TOPdesk - Public** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-topdesk---public"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für TOPdesk - Public

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit TOPdesk - Public mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in TOPdesk - Public eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit TOPdesk - Public die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für TOPdesk - Public](#configure-topdesk---public-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines TOPdesk - Public-Testbenutzers](#create-topdesk---public-test-user)** , um ein Pendant von B. Simon in TOPdesk - Public zu erhalten, das mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **TOPdesk - Public** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4.  Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus, wenn Sie über eine **Dienstanbieter-Metadatendatei** verfügen:

    >[!NOTE]
    >Die **Dienstanbieter-Metadatendatei** rufen Sie später im Tutorial im Abschnitt **Konfigurieren des einmaligen Anmeldens für TOPdesk - Public** ab.

    a. Klicken Sie auf **Metadatendatei hochladen**.
    
    ![Metadatendatei hochladen](common/upload-metadata.png)

    b. Klicken Sie auf das **Ordnerlogo**, wählen Sie die Metadatendatei aus, und klicken Sie auf **Hochladen**.

    ![Metadatendatei auswählen](common/browse-upload-metadata.png)

    c. Nach dem erfolgreichen Upload der Metadatendatei werden die Werte unter **Bezeichner** und **Antwort-URL** im Abschnitt „Grundlegende SAML-Konfiguration“ automatisch eingefügt.

    d. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<companyname>.topdesk.net`

    e. Geben Sie im Textfeld **Identifier URL** (Bezeichner-URL) die TOPdesk-Metadaten-URL ein, die Sie aus der TOPdesk-Konfiguration abrufen können. Verwenden Sie das folgende Format: `https://<companyname>.topdesk.net/saml-metadata/<identifier>`.
    
    f. Geben Sie im Textfeld **Antwort-URL** eine URL nach folgendem Muster ein: `https://<companyname>.topdesk.net/tas/public/login/verify`
    
    > [!NOTE] 
    > Werden die Werte für **Bezeichner** und **Antwort-URL** nicht automatisch aufgefüllt, müssen Sie sie manuell eingeben. Verwenden Sie für „Bezeichner“ das oben genannte Format. Den Wert für „Antwort-URL“ rufen Sie später im Tutorial im Abschnitt **Konfigurieren des einmaligen Anmeldens für TOPdesk - Public** ab. Der Wert für die **Anmelde-URL** entspricht nicht dem tatsächlichen Wert. Aktualisieren Sie den Wert mit der richtigen Anmelde-URL. Den Wert erhalten Sie vom [Supportteam für den TOPdesk - Public-Client](https://my.topdesk.com/). Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

5. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um den Ihren Anforderungen entsprechenden **Verbundmetadaten-XML**-Code aus den verfügbaren Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

6. Kopieren Sie im Abschnitt **TOPdesk - Public einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf TOPdesk - Public gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste den Eintrag **TOPdesk - Public** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-topdesk---public-sso"></a>Konfigurieren des einmaligen Anmeldens für TOPdesk - Public

1. Melden Sie sich auf Ihrer **TOPdesk - Public**-Unternehmenswebsite als Administrator an.

2. Klicken Sie im **TOPdesk**-Menü auf **Einstellungen**.
   
    ![Einstellungen](./media/topdesk-public-tutorial/menu.png "Einstellungen")

3. Klicken Sie auf **Anmeldeeinstellungen**.
   
    ![Login Settings (Anmeldeeinstellungen)](./media/topdesk-public-tutorial/login.png "Anmeldeeinstellungen")

4. Erweitern Sie das Menü **Anmeldeeinstellungen**, und klicken Sie dann auf **Allgemein**.
   
    ![Allgemeine Einstellungen](./media/topdesk-public-tutorial/general.png "Allgemeine Einstellungen")

5. Führen Sie im Abschnitt **Öffentlich** des Konfigurationsabschnitts **SAML-Anmeldung** die folgenden Schritte aus:
   
    ![Technische Einstellungen](./media/topdesk-public-tutorial/public.png "Technische Einstellungen")
   
    a. Klicken Sie auf **Herunterladen**, um die öffentliche Metadatendatei herunterzuladen, und speichern Sie sie dann lokal auf Ihrem Computer.
   
    b. Öffnen Sie die heruntergeladene Metadatendatei, und suchen Sie den Knoten **AssertionConsumerService**.

    ![AssertionConsumerService](./media/topdesk-public-tutorial/service.png "AssertionConsumerService")
   
    c. Kopieren Sie den Wert **AssertionConsumerService**, und fügen Sie ihn im Abschnitt **Basic SAML Configuration** (Grundlegende SAML-Konfiguration) in das Textfeld **Reply URL** (Antwort-URL) ein.      
   
6. Zum Erstellen einer Zertifikatsdatei führen Sie die folgenden Schritte durch:
    
    ![Certificate](./media/topdesk-public-tutorial/certificate-file.png "Zertifikat")
    
    a. Öffnen Sie die aus dem Azure-Portal heruntergeladene Metadatendatei.
    
    b. Erweitern Sie den Knoten **RoleDescriptor**, der einen **xsi:type** gleich **fed:ApplicationServiceType** aufweist.
    
    c. Kopieren Sie den Wert des Knotens **X509Certificate**.
    
    d. Speichern Sie den kopierten **X509Certificate**-Wert lokal auf Ihrem Computer in einer Datei.

7. Klicken Sie im Abschnitt **Öffentlich** auf **Hinzufügen**.
    
    ![SAML Login (SAML-Anmeldung)](./media/topdesk-public-tutorial/add.png "SAML-Anmeldung")

8. Führen Sie auf der Dialogseite **SAML-Konfigurationsassistent** die folgenden Schritte aus:
    
    ![SAML Configuration Assistant](./media/topdesk-public-tutorial/configuration.png "SAML Configuration Assistant")
    
    a. Klicken Sie zum Hochladen der aus dem Azure-Portal heruntergeladenen Metadatendatei unter **Verbundmetadaten** auf **Durchsuchen**.

    b. Zum Hochladen der Zertifikatsdatei klicken Sie unter **Zertifikat (RSA)** auf **Durchsuchen**.

    c. Wenn Sie die Logodatei hochladen möchten, die Sie vom TOPdesk-Supportteam erhalten haben, klicken Sie unter **Logo-Symbol** auf **Durchsuchen**.

    d. Geben Sie `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` in das Textfeld **Benutzernamenattribut** ein.

    e. Geben Sie in das Textfeld **Anzeigename** einen Namen für Ihre Konfiguration ein.

    f. Klicken Sie auf **Speichern**.

### <a name="create-topdesk---public-test-user"></a>Erstellen eines TOPdesk - Public-Testbenutzers

Damit sich Azure AD-Benutzer bei TOPdesk - Public anmelden können, müssen sie in TOPdesk - Public bereitgestellt werden. Im Fall von TOPdesk - Public ist die Bereitstellung eine manuelle Aufgabe.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>So konfigurieren Sie die Benutzerbereitstellung

1. Melden Sie sich bei Ihrem **TOPdesk - Public** -Unternehmenswebsite als Administrator an.

2. Klicken Sie im Menü oben auf **TOPdesk \> Neu \> Support-Dateien \> Person**.
   
    ![Person](./media/topdesk-public-tutorial/files.png "Person")

3. Führen Sie im Dialogfeld „Neue Person“ die folgenden Schritte aus:
   
    ![Neue Person](./media/topdesk-public-tutorial/new.png "Neue Person")
   
    a. Klicken Sie auf die Registerkarte „Allgemein“.

    b. Geben Sie im Textfeld **Nachname** den Nachnamen des Benutzers ein, z.B. „Simon“.
 
    c. Wählen Sie eine **Website** für das Konto aus.
 
    d. Klicken Sie auf **Speichern**.

> [!NOTE]
> Sie können Azure AD-Benutzerkonten auch mithilfe anderer Tools zum Erstellen von TOPdesk - Public-Benutzerkonten oder mithilfe der von TOPdesk - Public bereitgestellten APIs erstellen.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für TOPdesk - Public weitergeleitet, wo Sie den Anmeldeflow initiieren können. 

* Rufen Sie direkt die Anmelde-URL für TOPdesk - Public auf, und initiieren Sie den Anmeldeflow.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie in „Meine Apps“ auf die Kachel „TOPdesk - Public“ klicken, werden Sie zur Anmelde-URL für TOPdesk - Public weitergeleitet. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von TOPdesk - Public können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-any-app)
