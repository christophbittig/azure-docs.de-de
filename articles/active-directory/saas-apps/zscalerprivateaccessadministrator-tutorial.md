---
title: 'Tutorial: Azure Active Directory-Integration mit Zscaler Private Access Administrator | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Zscaler Private Access Administrator konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/31/2021
ms.author: jeedes
ms.openlocfilehash: 95cec5299405a8e5960eba46b4ee19b37383b829
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132302290"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-private-access-administrator"></a>Tutorial: Azure Active Directory-Integration mit Zscaler Private Access Administrator

In diesem Tutorial erfahren Sie, wie Sie Zscaler Private Access Administrator in Azure Active Directory (Azure AD) integrieren. Die Integration von Zscaler Private Access Administrator in Azure AD ermöglicht Folgendes:

* In Azure AD steuern, wer Zugriff auf Zscaler Private Access Administrator hat.
* Ihren Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei Zscaler Private Access Administrator anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit Zscaler Private Access Administrator konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Sollten Sie nicht über eine Azure AD-Umgebung verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) verwenden.
* Abonnement für Zscaler Private Access Administrator, für das einmaliges Anmelden aktiviert ist

> [!NOTE]
> Diese Integration kann auch über die Azure AD-Umgebung für die US Government-Cloud verwendet werden. Sie finden diese Anwendung im Azure AD-Katalog für US Government-Cloudanwendungen und konfigurieren sie auf die gleiche Weise wie in der öffentlichen Cloud.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Zscaler Private Access Administrator unterstützt **SP**- und **IDP**-initiiertes SSO.

## <a name="add-zscaler-private-access-administrator-from-the-gallery"></a>Hinzufügen von Zscaler Private Access Administrator über den Katalog

Zum Konfigurieren der Integration von Zscaler Private Access Administrator in Azure AD müssen Sie Zscaler Private Access Administrator aus dem Katalog zur Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Zscaler Private Access Administrator** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Zscaler Private Access Administrator** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-zscaler-private-access-administrator"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Zscaler Private Access Administrator

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Zscaler Private Access Administrator mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Zscaler Private Access Administrator eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Zscaler Private Access Administrator die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Zscaler Private Access Administrator](#configure-zscaler-private-access-administrator-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
    1. **[Erstellen eines Testbenutzers für Zscaler Private Access Administrator](#create-zscaler-private-access-administrator-test-user)** , um eine Entsprechung von B. Simon in Zscaler Private Access Administrator zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist.
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Zscaler Private Access Administrator** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus, wenn Sie die Anwendung im **IDP-initiierten** Modus konfigurieren möchten:

    a. Geben Sie im Textfeld **Bezeichner** eine URL im folgenden Format ein: `https://<SUBDOMAIN>.private.zscaler.com/auth/metadata`

    b. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://<SUBDOMAIN>.private.zscaler.com/auth/sso`

    c. Klicken Sie auf **Zusätzliche URLs festlegen**.

    d. Geben Sie im Textfeld **Relayzustand** den Wert `idpadminsso` ein.

5.  Wenn Sie die Anwendung im **SP-initiierten Modus** konfigurieren möchten, führen Sie die folgenden Schritte durch:

    Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<SUBDOMAIN>.private.zscaler.com/auth/sso`   

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Sie müssen diese Werte mit dem tatsächlichen Bezeichner, der Antwort-URL und der Anmelde-URL aktualisieren. Wenden Sie sich an das [Kundensupportteam von Zscaler Private Access Administrator](https://help.zscaler.com/zpa-submit-ticket), um diese Werte zu erhalten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

6. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um den Ihren Anforderungen entsprechenden **Verbundmetadaten-XML**-Code aus den verfügbaren Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

7. Kopieren Sie im Abschnitt für die **Einrichtung von Zscaler Private Access Administrator** die URLs, die Ihre Anforderungen erfüllen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Zscaler Private Access Administrator gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Liste der Anwendungen **Zscaler Private Access Administrator** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-zscaler-private-access-administrator-sso"></a>Konfigurieren des einmaligen Anmeldens für Zscaler Private Access Administrator

1. Melden Sie sich in einem anderen Webbrowserfenster bei Zscaler Private Access Administrator als Administrator an.

2. Klicken Sie im oberen Bereich auf **Verwaltung** und dann im Abschnitt **AUTHENTIFIZIERUNG** auf **IdP-Konfiguration**.

    ![Administrator für Zscaler Private Access Administrator](./media/zscalerprivateaccessadministrator-tutorial/admin.png)

3. Klicken Sie in der Ecke oben rechts auf **Add IdP Configuration** (IdP-Konfiguration hinzufügen). 

    ![Hinzufügen der IdP-Konfiguration in Zscaler Private Access Administrator](./media/zscalerprivateaccessadministrator-tutorial/add-configuration.png)

4. Führen Sie auf der Seite **Add IdP Configuration** (IdP-Konfiguration hinzufügen) die folgenden Schritte aus:
 
    ![Auswählen der IdP-Konfiguration in Zscaler Private Access Administrator](./media/zscalerprivateaccessadministrator-tutorial/select-file.png)

    a. Klicken Sie auf **Select File** (Datei auswählen), um im Feld **IdP Metadata File Upload** (Upload der IdP-Metadatendatei) die heruntergeladene Metadatendatei von Azure AD hochzuladen.

    b. Die **IdP-Metadaten** werden in Azure AD gelesen, und alle Felder werden aufgefüllt, wie hier gezeigt:

    ![IdP-Konfiguration von Zscaler Private Access Administrator](./media/zscalerprivateaccessadministrator-tutorial/metadata.png)

    c. Wählen Sie für **Single Sign On** (Einmaliges Anmelden) die Option **Administrator**.

    d. Wählen Sie im Feld **Domains** (Domänen) Ihre Domäne aus.
    
    e. Klicken Sie auf **Speichern**.

### <a name="create-zscaler-private-access-administrator-test-user"></a>Erstellen eines Zscaler Private Access Administrator-Testbenutzers

Um Azure AD-Benutzern die Anmeldung bei Zscaler Private Access Administrator zu ermöglichen, müssen diese in Zscaler Private Access Administrator bereitgestellt werden. Im Fall von Zscaler Private Access Administrator ist die Bereitstellung eine manuelle Aufgabe.

**Führen Sie zum Bereitstellen eines Benutzerkontos die folgenden Schritte aus:**

1. Melden Sie sich bei Ihrer Zscaler Private Access Administrator-Unternehmenswebsite als Administrator an.

2. Klicken Sie im oberen Bereich auf **Verwaltung** und dann im Abschnitt **AUTHENTIFIZIERUNG** auf **IdP-Konfiguration**.

    ![Administrator für Zscaler Private Access Administrator](./media/zscalerprivateaccessadministrator-tutorial/admin.png)

3. Klicken Sie links im Menü auf **Administrators** (Administratoren).

    ![Administrator für Zscaler Private Access Administrator](./media/zscalerprivateaccessadministrator-tutorial/administrator.png)

4. Klicken Sie in der Ecke oben rechts auf **Add Administrator** (Administrator hinzufügen):

    ![Hinzufügen eines Administrators für Zscaler Private Access Administrator](./media/zscalerprivateaccessadministrator-tutorial/add-administrator.png)

5. Führen Sie auf der Seite **Add Administrator** (Administrator hinzufügen) die folgenden Schritte aus:

    ![Benutzeradministrator in Zscaler Private Access Administrator](./media/zscalerprivateaccessadministrator-tutorial/user-admin.png)

    a. Geben Sie im Textfeld **Username** (Benutzername) die E-Mail-Adresse des Benutzers ein, z. B. BrittaSimon@contoso.com.

    b. Geben Sie im Textfeld **Password** (Kennwort) das Kennwort des Benutzers ein.

    c. Geben Sie im Textfeld **Confirm Password** (Kennwort bestätigen) das Kennwort erneut ein.

    d. Wählen Sie unter **Role** (Rolle) die Option **Zscaler Private Access Administrator** aus.

    e. Geben Sie im Textfeld **Email** (E-Mail-Adresse) die E-Mail-Adresse des Benutzers ein, z.B. BrittaSimon@contoso.com.

    f. Geben Sie im Textfeld **Phone** (Telefon) die Telefonnummer ein.

    g. Wählen Sie im Textfeld **Timezone** (Zeitzone) die Zeitzone aus.

    h. Klicken Sie auf **Speichern**.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

#### <a name="sp-initiated"></a>SP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für Zscaler Private Access Administrator weitergeleitet, wo Sie den Anmeldeflow initiieren können.  

* Rufen Sie direkt die Anmelde-URL für Zscaler Private Access Administrator auf, und initiieren Sie den Anmeldeflow.

#### <a name="idp-initiated"></a>IDP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch sollten Sie automatisch bei der Zscaler Private Access Administrator-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. 

Sie können auch den Microsoft-Bereich „Meine Apps“ verwenden, um die Anwendung in einem beliebigen Modus zu testen. Beim Klicken auf die Kachel „Zscaler Private Access Administrator“ in „Meine Apps“ geschieht Folgendes: Wenn Sie den SP-Modus konfiguriert haben, werden Sie zum Initiieren des Anmeldeflows zur Anmeldeseite der Anwendung weitergeleitet. Wenn Sie den IDP-Modus konfiguriert haben, sollten Sie automatisch bei der Zscaler Private Access Administrator-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Zscaler Private Access Administrator können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Ex- und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-aad)
