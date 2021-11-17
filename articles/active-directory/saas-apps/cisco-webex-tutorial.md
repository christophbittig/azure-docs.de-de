---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD mit Cisco Webex Meetings'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Cisco WebEx Meetings konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/01/2021
ms.author: jeedes
ms.openlocfilehash: 133eb2af33bf3313716d16d3b0e748d4c9adcc0e
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132344919"
---
# <a name="tutorial-azure-ad-sso-integration-with-cisco-webex-meetings"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD mit Cisco Webex Meetings

In diesem Tutorial erfahren Sie, wie Sie Cisco Webex Meetings in Azure Active Directory (Azure AD) integrieren. Die Integration von Cisco Webex Meetings in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf Cisco Webex Meetings hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Cisco Webex Meetings anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.


## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Cisco Webex Meetings-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist
*  Dienstanbieter-Metadatendatei von Cisco Webex Meetings

> [!NOTE]
> Diese Integration kann auch über die Azure AD-Umgebung für die US Government-Cloud verwendet werden. Sie finden diese Anwendung im Azure AD-Katalog für US Government-Cloudanwendungen und konfigurieren sie auf die gleiche Weise wie in der öffentlichen Cloud.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Cisco Webex Meetings unterstützt **SP- und IDP-initiiertes** einmaliges Anmelden.
* Cisco Webex Meetings unterstützt die [**automatisierte** Benutzerbereitstellung und Bereitstellungsaufhebung](cisco-webex-provisioning-tutorial.md) (empfohlen).
* Cisco Webex Meetings unterstützt die **Just-in-Time**-Benutzerbereitstellung.

## <a name="adding-cisco-webex-meetings-from-the-gallery"></a>Hinzufügen von Cisco WebEx Meetings aus dem Katalog

Zum Konfigurieren der Integration von Cisco WebEx Meetings in Azure AD müssen Sie Cisco WebEx Meetings aus dem Katalog der Liste der verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Cisco Webex Meetings** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Cisco Webex Meetings** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-cisco-webex-meetings"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Cisco Webex Meetings

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Cisco Webex Meetings mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Cisco Webex Meetings eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Cisco Webex Meetings die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
   1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
   1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
   
1. **[Konfigurieren des einmaligen Anmeldens für Cisco Webex Meetings](#configure-cisco-webex-meetings-sso)**, um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
   * **[Erstellen eines Cisco Webex Meetings-Testbenutzers](#create-cisco-webex-meetings-test-user)**, um eine Entsprechung von B. Simon in Cisco Webex Meetings zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
    
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Cisco Webex Meetings** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** können Sie die Anwendung im **IDP-initiierten** Modus konfigurieren, indem Sie die Datei mit den **Metadaten des Dienstanbieters** wie folgt hochladen:
   1. Klicken Sie auf **Metadatendatei hochladen**.
   1. Klicken Sie auf das **Ordnerlogo**, wählen Sie die Metadatendatei aus, und klicken Sie auf **Hochladen**.
   1. Nachdem Sie die Metadatendatei des Dienstanbieters hochgeladen haben, werden im Abschnitt **Grundlegende SAML-Konfiguration** die Werte für **Bezeichner** und **Antwort-URL** automatisch ausgefüllt.
   
      > [!Note]
      > Sie erhalten die Metadatendatei des Dienstanbieters im Abschnitt **Konfigurieren des einmaligen Anmeldens für Cisco Webex Meetings** (später in diesem Tutorial beschrieben). 

1. Wenn Sie die Anwendung im **SP-initiierten Modus** konfigurieren möchten, führen Sie die folgenden Schritte durch:  
   1. Klicken Sie im Abschnitt **Grundlegende SAML-Konfiguration** auf das Bearbeitungssymbol (Stift).

      ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

   1. Geben Sie im Textfeld **Anmelde-URL** die URL im folgenden Format ein: `https://<customername>.my.webex.com`.

1. Die Cisco WebEx Meetings-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Daher müssen Sie Ihrer Konfiguration der SAML-Tokenattribute benutzerdefinierte Attributzuordnungen hinzufügen. Der folgende Screenshot zeigt die Liste der Standardattribute. Klicken Sie auf das Symbol **Bearbeiten**, um das Dialogfeld „Benutzerattribute“ zu öffnen.

   ![image](common/edit-attribute.png)

1. Darüber hinaus wird von der Cisco Webex Meetings-Anwendung erwartet, dass in der SAML-Antwort noch einige weitere Attribute zurückgegeben werden. Führen Sie im Dialogfeld „Benutzerattribute“ im Abschnitt „Benutzeransprüche“ die folgenden Schritte aus, um das SAML-Tokenattribut wie in der folgenden Tabelle gezeigt hinzuzufügen: 

   | Name | Quellattribut|
   | ---------------|  --------- |
   |   firstname    | user.givenname |
   |   lastname    | user.surname |
   |   email       | user.mail |
   |   uid    | user.mail |

   1. Klicken Sie auf **Neuen Anspruch hinzufügen**, um das Dialogfeld **Benutzeransprüche verwalten** zu öffnen.
   1. Geben Sie im Textfeld **Name** den für die Zeile angezeigten Attributnamen ein.
   1. Lassen Sie den **Namespace** leer.
   1. Wählen Sie „Source“ als **Attribut** aus.
   1. Wählen Sie in der Liste **Quellattribut** den für diese Zeile angezeigten Attributwert in der Dropdownliste aus.
   1. Klicken Sie auf **Speichern**.

1. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** zu **Verbundmetadaten-XML**, und wählen Sie **Herunterladen** aus, um das Zertifikat herunterzuladen und auf Ihrem Computer zu speichern.

   ![Downloadlink für das Zertifikat](common/metadataxml.png)

1. Kopieren Sie im Abschnitt **Cisco WebEx Meetings einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Cisco Webex Meetings gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Cisco WebEx Meetings** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-cisco-webex-meetings-sso"></a>Konfigurieren des einmaligen Anmeldens für Cisco Webex Meetings

1. Melden Sie sich bei Cisco Webex Meetings mit Ihren Administratoranmeldeinformationen an.
1. Navigieren Sie zu **Common Site Settings** (Allgemeine Websiteeinstellungen) und dann zu **SSO Configuration** (SSO-Konfiguration).

   ![Screenshot der Cisco Webex Administration, in der „Common Site Settings“ (Allgemeine Websiteeinstellungen) und „S S O Configuration“ (SSO-Konfiguration) ausgewählt sind](./media/cisco-webex-tutorial/tutorial-cisco-webex-11.png)

1. Führen Sie auf der Seite **Webex Administration** (Webex-Verwaltung) die folgenden Schritte aus:

   ![Screenshot der Seite „Webex Administration“ mit den in diesem Schritt beschriebenen Informationen](./media/cisco-webex-tutorial/tutorial-cisco-webex-10.png)

   1. Wählen Sie unter **Federation Protocol** (Verbundprotokoll) die Option **SAML 2.0** aus.
   1. Klicken Sie auf den Link **Import SAML Metadata** (SAML-Metadaten importieren), um die Metadatendatei hochzuladen, die Sie aus dem Azure-Portal heruntergeladen haben.
   1. Wählen Sie **SSO Profile** (SSO-Profil) als **IDP initiated** (IDP-initiiert) aus, klicken Sie auf die Schaltfläche **Export** (Exportieren), um die Metadatendatei des Dienstanbieters herunterzuladen, und laden Sie sie im Azure-Portal im Abschnitt **SAML-Basiskonfiguration** hoch.
   1. Geben Sie im **AuthContextClassRef**-Textfeld einen der folgenden Wert ein:
      * `urn:oasis:names:tc:SAML:2.0:ac:classes:unspecified`
      * `urn:oasis:names:tc:SAML:2.0:ac:classes:Password`
    
      Geben Sie die zwei Werte wie folgt ein, um die mehrstufige Authentifizierung mithilfe von Azure AD zu aktivieren: `urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport;urn:oasis:names:tc:SAML:2.0:ac:classes:X509`

   1. Wählen Sie **Auto Account Creation** (Automatische Kontoerstellung) aus.
   
      > [!NOTE]
      > Zum Aktivieren der **Just-in-Time**-Benutzerbereitstellung müssen Sie **Auto Account Creation** (Automatische Kontoerstellung) aktivieren. Zusätzlich müssen noch SAML-Tokenattribute in der SAML-Antwort übergeben werden.

   1. Klicken Sie auf **Speichern**.

      > [!NOTE]
      > Diese Konfiguration gilt nur für Kunden, die die WebEx-Benutzer-ID im E-Mail-Format verwenden.
      > 
      > Weitere Informationen zum Konfigurieren von Cisco Webex Meetings finden Sie auf der Seite zur [Webex-Dokumentation](https://help.webex.com/WBX000022701/How-Do-I-Configure-Microsoft-Azure-Active-Directory-Integration-with-Cisco-Webex-Through-Site-Administration#:~:text=In%20the%20Azure%20portal%2C%20select,in%20the%20Add%20Assignment%20dialog).

### <a name="create-cisco-webex-meetings-test-user"></a>Erstellen eines Cisco WebEx Meetings-Testbenutzers

Das Ziel dieses Abschnitts ist das Erstellen eines Benutzers namens B. Simon in Cisco WebEx Meetings. Cisco WebEx Meetings unterstützt die **Just-in-Time**-Bereitstellung, die standardmäßig aktiviert ist. Für Sie steht in diesem Abschnitt kein Aktionselement zur Verfügung. Falls ein Benutzer nicht bereits in Cisco WebEx Meetings vorhanden ist, wird beim Versuch, auf Cisco WebEx Meetings zuzugreifen, ein neuer Benutzer erstellt.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

#### <a name="sp-initiated"></a>SP-initiiert

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für Cisco Webex Meetings weitergeleitet, wo Sie den Anmeldeflow initiieren können.  

* Navigieren Sie direkt zur Anmelde-URL für Cisco Webex Meetings, und initiieren Sie den Anmeldeflow.

#### <a name="idp-initiated"></a>IDP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch sollten Sie automatisch bei der Cisco Webex Meetings-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben.

Sie können auch den Microsoft-Bereich „Meine Apps“ verwenden, um die Anwendung in einem beliebigen Modus zu testen. Beim Klicken auf die Kachel „Cisco Webex Meetings“ in „Meine Apps“ geschieht Folgendes: Wenn Sie den SP-Modus konfiguriert haben, werden Sie zum Initiieren des Anmeldeflows zur Anmeldeseite der Anwendung weitergeleitet. Wenn Sie den IDP-Modus konfiguriert haben, sollten Sie automatisch bei der Cisco Webex Meetings-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Cisco Webex Meetings können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-aad)
