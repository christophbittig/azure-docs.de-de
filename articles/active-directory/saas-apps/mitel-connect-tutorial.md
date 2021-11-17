---
title: 'Tutorial: Azure Active Directory-Integration in Mitel Connect | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Mitel Connect konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/14/2021
ms.author: jeedes
ms.openlocfilehash: 03312977c4901593c433113d88766dcc7a6b2a55
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132300672"
---
# <a name="tutorial-azure-active-directory-integration-with-mitel-micloud-connect-or-cloudlink-platform"></a>Tutorial: Azure Active Directory-Integration mit Mitel MiCloud Connect oder CloudLink Platform

In diesem Tutorial erfahren Sie, wie Sie die Mitel Connect-App verwenden, um Azure Active Directory (Azure AD) mit Mitel MiCloud Connect oder CloudLink Platform zu integrieren. Die Mitel Connect-App ist im Azure-Katalog verfügbar. Die Integration von Azure AD mit MiCloud Connect oder CloudLink Platform bietet Ihnen folgende Vorteile:

* Sie können den Benutzerzugriff auf MiCloud Connect- und CloudLink-Apps in Azure AD unter Verwendung ihrer Anmeldeinformationen steuern.
* Sie können es Benutzern in Ihrem Konto ermöglichen, sich mit ihren Azure AD-Konten automatisch bei MiCloud Connect oder CloudLink anzumelden (einmaliges Anmelden; Single Sign-On, SSO).

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration in MiCloud Connect zu konfigurieren, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Sollten Sie nicht über eine Azure AD-Umgebung verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Mitel MiCloud Connect- oder ein Mitel CloudLink-Konto, abhängig von der zu konfigurierenden Anwendung

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden (SSO) von Azure AD.

* Mitel Connect unterstützt **SP**-initiiertes einmaliges Anmelden.

## <a name="adding-mitel-connect-from-the-gallery"></a>Hinzufügen von Mitel Connect aus dem Katalog

Zum Konfigurieren der Integration von Mitel Connect in Azure AD müssen Sie Mitel Connect aus dem Katalog der Liste der verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Mitel Connect** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Mitel Connect** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso"></a>Konfigurieren und Testen des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit MiCloud Connect oder CloudLink Platform mithilfe eines Testbenutzers namens **_Britta Simon_**. Damit einmaliges Anmelden funktioniert, muss zwischen dem Benutzer im Azure AD-Portal und dem entsprechenden Benutzer auf der Mitel-Plattform eine Verknüpfung eingerichtet werden. In den folgenden Abschnitten finden Sie Informationen zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit MiCloud Connect oder CloudLink Platform.
* Konfigurieren und Testen des einmaligen Anmelden von Azure AD mit MiCloud Connect
* Konfigurieren und Testen des einmaligen Anmelden von Azure AD mit CloudLink Platform

## <a name="configure-and-test-azure-ad-sso-with-micloud-connect"></a>Konfigurieren und Testen des einmaligen Anmelden von Azure AD mit MiCloud Connect

So konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit MiCloud Connect:

1. **[Konfigurieren von MiCloud Connect für das einmalige Anmelden mit Azure AD](#configure-micloud-connect-for-sso-with-azure-ad)**, um Ihren Benutzern die Verwendung des Features zu ermöglichen und die SSO-Einstellungen auf der Anwendungsseite zu konfigurieren.
2. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
3. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
4. **[Erstellen eines Mitel MiCloud Connect-Testbenutzers](#create-a-mitel-micloud-connect-test-user)**, um in Ihrem MiCloud Connect-Konto eine Entsprechung von Britta Simon zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist.
5. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-micloud-connect-for-sso-with-azure-ad"></a>Konfigurieren von MiCloud Connect für das einmalige Anmelden mit Azure AD

In diesem Abschnitt aktivieren Sie im Azure-Portal das einmalige Anmelden von Azure AD für MiCloud Connect, und Sie konfigurieren Ihr MiCloud Connect-Konto zum Zulassen des einmaligen Anmeldens mit Azure AD.

Um MiCloud Connect mit einmaligem Anmelden für Azure AD zu konfigurieren, empfiehlt es sich, das Azure-Portal und das Mitel-Kontoportal nebeneinander zu öffnen. Sie müssen einige Informationen aus dem Azure-Portal in das Mitel-Kontoportal kopieren und umgekehrt.


1. So öffnen Sie die Konfigurationsseite im Azure-Portal:

    1. Wählen Sie auf der Anwendungsintegrationsseite für **Mitel Connect** die Option **Einmaliges Anmelden** aus.

    1. Wählen Sie im Dialogfeld **SSO-Methode auswählen** die Methode **SAML** aus. Die Seite „SAML-basierte Anmeldung“ wird angezeigt.

2. So öffnen Sie das Konfigurationsdialogfeld im Mitel-Kontoportal:

    1. Wählen Sie im Menü **Phone System** (Telefonsystem) die Option **Add-On Features** (Add-On-Features) aus.

    1. Wählen Sie rechts neben **Single Sign-On** (Einmaliges Anmelden) die Option **Activate** (Aktivieren) oder **Settings** (Einstellungen) aus.
    
    Das Dialogfeld „Connect Single Sign-On Settings“ (Connect-SSO-Einstellungen) wird angezeigt.
    
3. Aktivieren Sie das Kontrollkästchen **Enable Single Sign-On** (Einmaliges Anmelden aktivieren).
    
    ![Screenshot: Mitel Connect-Seite mit Einstellungen für einmaliges Anmelden, auf der das Kontrollkästchen „Enable Single Sign-On“ (Einmaliges Anmelden aktivieren) aktiviert ist](./media/mitel-connect-tutorial/mitel-connect-enable.png)

4. Wählen Sie im Azure-Portal im Abschnitt **Grundlegende SAML-Konfiguration** das Symbol **Bearbeiten** aus.
   
    ![Screenshot: Seite „Einmaliges Anmelden (SSO) mit SAML einrichten“ mit Auswahl des Symbols „Bearbeiten“](common/edit-urls.png)

    Das Dialogfeld „Grundlegende SAML-Konfiguration“ wird angezeigt.

5.  Kopieren Sie im Mitel-Kontoportal die URL im Feld **Mitel Identifier (Entity ID)** (Mitel-Bezeichner (Entitäts-ID)), und fügen Sie sie in das Feld **Bezeichner (Entitäts-ID)** im Azure-Portal ein.

6. Kopieren Sie im Mitel-Kontoportal die URL im Feld **Reply URL (Assertion Consumer Service URL)** (Antwort-URL (Assertionsverbraucherdienst-URL)), und fügen Sie sie in das Feld **Antwort-URL (Assertionsverbraucherdienst-URL)** im Azure-Portal ein.

   ![Screenshot: „Grundlegende SAML-Konfiguration“ im Azure-Portal und Abschnitt „Set Up Identity Provider“ (Identitätsanbieter einrichten) im Mitel-Kontoportal mit Linien zur Verdeutlichung der Beziehungen](./media/mitel-connect-tutorial/mitel-azure-basic-configuration.png)

7. Geben Sie im Textfeld **Anmelde-URL** eine der folgenden URLs ein:

    1. **https://portal.shoretelsky.com** , um das Mitel-Kontoportal als Mitel-Standardanwendung zu verwenden
    1. **https://teamwork.shoretel.com** , um Teamwork als Mitel-Standardanwendung zu verwenden

    > [!NOTE]
    > Die Mitel-Standardanwendung ist die Anwendung, auf die zugegriffen wird, wenn ein Benutzer im Zugriffsbereich die Kachel „Mitel Connect“ auswählt. Auf diese Anwendung wird auch zugegriffen, wenn Sie eine Testeinrichtung in Azure AD vornehmen.

8. Wählen Sie im Azure-Portal im Dialogfeld **Grundlegende SAML-Konfiguration** die Option **Speichern** aus.

9. Wählen Sie im Azure-Portal auf der Seite **SAML-basierte Anmeldung** im Abschnitt **SAML-Signaturzertifikat** neben **Zertifikat (Base64)** die Option **Herunterladen** aus, um das **Signaturzertifikat** herunterzuladen, und speichern Sie es auf Ihrem Computer.

    ![Screenshot: Bereich „SAML-Signaturzertifikat“, in dem Sie ein Zertifikat herunterladen können](./media/mitel-connect-tutorial/azure-signing-certificate.png)

10. Öffnen Sie das Signaturzertifikat in einem Text-Editor, kopieren Sie alle Daten in der Datei, und fügen Sie sie dann in das Feld **Signing Certificate** (Signaturzertifikat) im Mitel-Kontoportal ein. 

      ![Screenshot: Feld „Signaturzertifikat“](./media/mitel-connect-tutorial/mitel-connect-signing-certificate.png)

11. Führen Sie im Azure-Portal auf der Seite **SAML-basierte Anmeldung** im Abschnitt **Mitel Connect einrichten** die folgenden Schritte aus:

     1. Kopieren Sie die URL im Feld **Anmelde-URL**, und fügen Sie sie in das Feld **Sign-in URL** (Anmelde-URL) im Mitel-Kontoportal ein.

     1. Kopieren Sie die URL im Feld **Azure AD-Bezeichner**, und fügen Sie sie in das Feld **Entity ID** (Entitäts-ID) im Mitel-Kontoportal ein.
         
         ![Screenshot: Beziehung zwischen der Seite „SAML-basierte Anmeldung“ im Azure-Portal und dem Mitel-Kontoportal](./media/mitel-connect-tutorial/mitel-azure-set-up-connect.png)

12. Wählen Sie im Mitel-Kontoportal im Dialogfeld **Connect Single Sign-On Settings** (Connect-SSO-Einstellungen) die Option **Save** (Speichern) aus.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Mitel Connect gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Mitel Connect** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

### <a name="create-a-mitel-micloud-connect-test-user"></a>Erstellen eines Mitel MiCloud Connect-Testbenutzers

In diesem Abschnitt erstellen Sie in Ihrem MiCloud Connect-Konto eine Benutzerin mit Namen Britta Simon. Benutzer müssen erstellt und aktiviert werden, um einmaliges Anmelden verwenden zu können.

Informationen zum Hinzufügen von Benutzern im Mitel-Kontoportal finden Sie im Artikel zum [Hinzufügen eines Benutzers](https://shoretelcommunity.force.com/s/article/Adding-Users-092815) in der Mitel-Knowledge Base.

Erstellen Sie in Ihrem MiCloud Connect-Konto einen Benutzer mit den folgenden Details:

* **Name:** Britta Simon
* **Geschäftliche E-Mail-Adresse:** `brittasimon@<yourcompanydomain>.<extension>`   
  (Beispiel: [brittasimon@contoso.com](mailto:brittasimon@contoso.com))
* **Benutzername:** `brittasimon@<yourcompanydomain>.<extension>`  
  (Beispiel: [brittasimon@contoso.com](mailto:brittasimon@contoso.com). Der Benutzername ist in der Regel mit der geschäftlichen E-Mail-Adresse des Benutzers identisch.)

> [!NOTE]
> Der MiCloud Connect-Benutzername muss mit der E-Mail-Adresse des Benutzers in Azure identisch sein.

### <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für Mitel Connect weitergeleitet, wo Sie den Anmeldeflow initiieren können. 

* Rufen Sie die Anmelde-URL von Mitel Connect direkt auf, und initiieren Sie den Anmeldeflow.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie in „Meine Apps“ auf die Kachel „Mitel Connect“ klicken, werden Sie zur Anmelde-URL für Mitel Connect weitergeleitet. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).


## <a name="configure-and-test-azure-ad-sso-with-cloudlink-platform"></a>Konfigurieren und Testen des einmaligen Anmelden von Azure AD mit CloudLink Platform

In diesem Abschnitt erfahren Sie, wie Sie im Azure-Portal das einmalige Anmelden von Azure AD für CloudLink Platform aktivieren und das CloudLink Platform-Konto konfigurieren, um einmaliges Anmelden unter Verwendung von Azure AD zu ermöglichen.

Zum Konfigurieren von CloudLink Platform mit einmaligem Anmelden für Azure AD werden die folgenden Schritte empfohlen: Öffnen Sie das Azure-Portal und das CloudLink-Kontoportal nebeneinander, da Sie einige Informationen aus dem Azure-Portal in das CloudLink-Kontoportal (und umgekehrt) kopieren müssen.

1. So öffnen Sie die Konfigurationsseite im Azure-Portal:

    1. Wählen Sie auf der Anwendungsintegrationsseite für **Mitel Connect** die Option **Einmaliges Anmelden** aus.
    1. Wählen Sie im Dialogfeld **SSO-Methode auswählen** die Methode **SAML** aus. Die Seite **SAML-basierte Anmeldung** wird mit dem Abschnitt **Grundlegende SAML-Konfiguration** geöffnet.

       ![Screenshot: Seite „SAML-basierte Anmeldung“ mit dem Abschnitt „Grundlegende SAML-Konfiguration“](./media/mitel-connect-tutorial/mitel-azure-saml-settings.png)

2. So greifen Sie im CloudLink-Kontoportal auf den Konfigurationsbereich **Azure AD Single Sign On** (Azure AD-SSO) zu:

    1. Navigieren Sie zur Seite **Account Information** (Kontoinformationen) des Kundenkontos, mit dem Sie die Integration aktivieren möchten.

    1. Wählen Sie im Abschnitt **Integrations** (Integrationen) die Option **+ Add new**  (+ Neu hinzufügen) aus. Auf einem Popupbildschirm wird der Bereich **Integrations** (Integrationen) angezeigt.

    1. Wählen Sie die Registerkarte **3rd party** (Drittanbieter) aus. Eine Liste der unterstützten Drittanbieteranwendungen wird angezeigt. Wählen Sie die Schaltfläche **Add** (Hinzufügen) für **Azure AD Single Sign On** (Azure AD-SSO) und dann **Done** (Fertig) aus.

       ![Screenshot: Seite „Integrationen“, auf der Sie einmaliges Anmelden für Azure AD hinzufügen können](./media/mitel-connect-tutorial/mitel-cloudlink-integrations.png)

       **Azure AD Single Sign On** (Azure AD-SSO) wird für das Kundenkonto aktiviert und dem Abschnitt **Integrations** (Integrationen) auf der Seite **Account Information** (Kontoinformationen) hinzugefügt.   

   1. Wählen Sie **Complete Setup** (Setup abschließen) aus.
    
      ![Screenshot: Option „Complete Setup“ (Setup abschließen) für Azure AD-SSO](./media/mitel-connect-tutorial/mitel-cloudlink-complete-setup.png)
      
      Der Konfigurationsbereich **Azure AD Single Sign On** (Azure AD-SSO) wird geöffnet.
      
       ![Screenshot: Konfigurationsbereich „Azure AD Single Sign-On“ (Azure AD-SSO)](./media/mitel-connect-tutorial/mitel-cloudlink-sso-setup.png)
       
       Mitel empfiehlt, das Kontrollkästchen **Enable Mitel Credentials (Optional)** (Mitel-Anmeldeinformationen aktivieren (optional)) im Abschnitt **Optional Mitel credentials** (Optionale Mitel-Anmeldeinformationen) nicht zu aktivieren. Aktivieren Sie dieses Kontrollkästchen nur, wenn Sie möchten, dass sich der Benutzer nicht nur mit der Option für einmaliges Anmelden, sondern auch mithilfe der Mitel-Anmeldeinformationen bei der CloudLink-Anwendung anmelden kann.

3. Wählen Sie im Azure-Portal auf der Seite **SAML-basierte Anmeldung** im Abschnitt **Grundlegende SAML-Konfiguration** das Symbol **Bearbeiten** aus. Der Bereich **Grundlegende SAML-Konfiguration** wird geöffnet.

    ![Screenshot: Bereich „Grundlegende SAML-Konfiguration“ mit Auswahl des Symbols „Bearbeiten“](./media/mitel-connect-tutorial/mitel-azure-saml-basic.png)
 
 4. Kopieren Sie im CloudLink-Kontoportal die URL im Feld **Mitel Identifier (Entity ID)** (Mitel-Bezeichner (Entitäts-ID)), und fügen Sie sie im Azure-Portal in das Feld **Bezeichner (Entitäts-ID)** ein.

 5. Kopieren Sie im CloudLink-Kontoportal die URL im Feld **Reply URL (Assertion Consumer Service URL)** (Antwort-URL (Assertionsverbraucherdienst-URL)), und fügen Sie sie im Azure-Portal in das Feld **Antwort-URL (Assertionsverbraucherdienst-URL)** ein.  
    
    ![Screenshot: Beziehung zwischen Seiten im CloudLink-Kontoportal und dem Azure-Portal](./media/mitel-connect-tutorial/mitel-cloudlink-saml-mapping.png) 

 6. Geben Sie im Textfeld **Anmelde-URL** die URL `https://accounts.mitel.io` ein, um das CloudLink-Kontoportal als Mitel-Standardanwendung zu nutzen.
     
     ![Screenshot: Textfeld „Anmelde-URL“](./media/mitel-connect-tutorial/mitel-cloudlink-sign-on-url.png)
  
     > [!NOTE]
     > Die Mitel-Standardanwendung ist die Anwendung, die geöffnet wird, wenn ein Benutzer im Zugriffsbereich die Kachel „Mitel Connect“ auswählt. Auf diese Anwendung wird auch zugegriffen, wenn der Benutzer eine Testeinrichtung in Azure AD konfiguriert.

7. Wählen Sie im Dialogfeld **Grundlegende SAML-Konfiguration** die Option **Speichern** aus.

8. Wählen Sie im Azure-Portal auf der Seite **SAML-basierte Anmeldung** im Abschnitt **SAML-Signaturzertifikat** neben **Zertifikat (Base64)** die Option **Herunterladen** aus, um das **Signaturzertifikat** herunterzuladen. Speichern Sie das Zertifikat auf Ihrem Computer.
  
    ![Screenshot: Abschnitt „SAML-Signaturzertifikat“, in dem Sie ein Base64-Zertifikat herunterladen können](./media/mitel-connect-tutorial/mitel-cloudlink-save-certificate.png)

9. Öffnen Sie das Signaturzertifikat in einem Text-Editor, kopieren Sie alle Daten in der Datei, und fügen Sie sie dann im CloudLink-Kontoportal in das Feld **Signing Certificate** (Signaturzertifikat) ein.  

    > [!NOTE]
    > Wenn Sie über mehrere Zertifikate verfügen, empfiehlt es sich, sie nacheinander einzufügen. 
       
    ![Screenshot: Zweiter Schritt des Verfahrens, in dem Sie Werte aus Ihrer Azure AD-Integration einfügen](./media/mitel-connect-tutorial/mitel-cloudlink-enter-certificate.png)

10. Führen Sie im Azure-Portal auf der Seite **SAML-basierte Anmeldung** im Abschnitt **Mitel Connect einrichten** die folgenden Schritte aus:

     1. Kopieren Sie die URL im Feld **Anmelde-URL**, und fügen Sie sie im CloudLink-Kontoportal in das Feld **Sign-in URL** (Anmelde-URL) ein.

     1. Kopieren Sie die URL im Feld **Azure AD-Bezeichner**, und fügen Sie sie im CloudLink-Kontoportal in das Feld **IDP Identifier (Entity ID)** (IDP-Bezeichner (Entitäts-ID)) ein.
     
        ![Screenshot: Anzeige der Quelle für die hier beschriebenen Werte in Mitel Connect](./media/mitel-connect-tutorial/mitel-cloudlink-copy-settings.png)

11. Wählen Sie im CloudLink-Kontoportal im Bereich **Azure AD Single Sign On** (Azure AD-SSO) die Option **Save** (Speichern) aus:

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Mitel Connect gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Mitel Connect** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

### <a name="create-a-cloudlink-test-user"></a>Erstellen eines CloudLink-Testbenutzers

In diesem Abschnitt wird beschrieben, wie Sie in CloudLink Platform einen Testbenutzer namens **_Britta Simon_** erstellen. Benutzer müssen erstellt und aktiviert werden, damit sie einmaliges Anmelden verwenden können.

Ausführliche Informationen zum Hinzufügen von Benutzern im CloudLink-Kontoportal finden Sie in der [Dokumentation zu CloudLink-Konten](https://www.mitel.com/document-center/technology/cloudlink/all-releases/en/cloudlink-accounts-html) unter **_Verwalten von Benutzern_**.

Erstellen Sie in Ihrem CloudLink-Kontoportal einen Benutzer mit den folgenden Details:

* Name: Britta Simon
* Vorname: Britta
* Nachname: Simon
* E-Mail: BrittaSimon@contoso.com

> [!NOTE]
> Die CloudLink-E-Mail-Adresse des Benutzers muss mit dem Wert für **Benutzerprinzipalname** im Azure-Portal übereinstimmen.

### <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für CloudLink weitergeleitet, wo Sie den Anmeldeflow initiieren können. 

* Navigieren Sie direkt zur Anmelde-URL für CloudLink, und initiieren Sie den Anmeldeflow.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie in „Meine Apps“ auf die Kachel „CloudLink“ klicken, werden Sie zur Anmelde-URL für CloudLink weitergeleitet. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Mitel Connect können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-aad)
