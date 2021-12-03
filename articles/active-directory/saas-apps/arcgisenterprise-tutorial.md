---
title: 'Tutorial: Azure Active Directory-Integration mit ARcGIS Enterprise | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und ArcGIS Enterprise konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/11/2021
ms.author: jeedes
ms.openlocfilehash: 9fdb22f31f09fd334e2ebfa6e7253e47fc5bdfda
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132288774"
---
# <a name="tutorial-azure-active-directory-integration-with-arcgis-enterprise"></a>Tutorial: Azure Active Directory-Integration in ArcGIS Enterprise

In diesem Tutorial erfahren Sie, wie Sie ArcGIS Enterprise in Azure Active Directory (Azure AD) integrieren. Die Integration von ArcGIS Enterprise in Azure Active Directory ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf ArcGIS Enterprise hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei ArcGIS Enterprise anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein ArcGIS Enterprise-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

> [!NOTE]
> Diese Integration kann auch über die Azure AD-Umgebung für die US Government-Cloud verwendet werden. Sie finden diese Anwendung im Azure AD-Katalog für US Government-Cloudanwendungen und konfigurieren sie auf die gleiche Weise wie in der öffentlichen Cloud.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* ArcGIS Enterprise unterstützt **SP- und IDP-initiiertes** einmaliges Anmelden.
* ArcGIS Enterprise unterstützt die **Just-In-Time**-Benutzerbereitstellung.

## <a name="add-arcgis-enterprise-from-the-gallery"></a>Hinzufügen von ArcGIS Enterprise aus dem Katalog

Zum Konfigurieren der Integration von ArcGIS Enterprise in Azure AD müssen Sie ArcGIS Enterprise aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **ArcGIS Enterprise** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **ArcGIS Enterprise** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-arcgis-enterprise"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für ArcGIS Enterprise

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit ArcGIS Enterprise mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in ArcGIS Enterprise eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit ArcGIS Enterprise die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für ArcGIS Enterprise](#configure-arcgis-enterprise-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines ArcGIS Enterprise-Testbenutzers](#create-arcgis-enterprise-test-user)** , um eine Entsprechung von B. Simon in ArcGIS Enterprise zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **ArcGIS Enterprise** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus, wenn Sie die Anwendung im **IDP**-initiierten Modus konfigurieren möchten:

    a. Geben Sie im Textfeld **Bezeichner** eine URL im folgenden Format ein: `<EXTERNAL_DNS_NAME>.portal`

    b. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://<EXTERNAL_DNS_NAME>/portal/sharing/rest/oauth2/saml/signin2`

    c. Klicken Sie auf **Zusätzliche URLs festlegen**, und führen Sie den folgenden Schritt aus, wenn Sie die Anwendung im **SP-initiierten Modus** konfigurieren möchten:

    Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<EXTERNAL_DNS_NAME>/portal/sharing/rest/oauth2/saml/signin`

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Sie müssen diese Werte mit dem tatsächlichen Bezeichner, der Antwort-URL und der Anmelde-URL aktualisieren. Wenden Sie sich an das [Supportteam von ArcGIS Enterprise Client](mailto:support@esri.com), um diese Werte zu erhalten. Sie erhalten den Bezeichnerwert im Abschnitt **Festlegen des Identitätsanbieters** weiter unten in diesem Tutorial.

5. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf die Schaltfläche „Kopieren“, um die **App-Verbundmetadaten-URL** zu kopieren, und speichern Sie sie auf Ihrem Computer.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf ArcGIS Enterprise gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **ArcGIS Enterprise** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-arcgis-enterprise-sso"></a>Konfigurieren des einmaligen Anmeldens für ArcGIS Enterprise

1. Wenn Sie die Konfiguration in ArcGIS Enterprise automatisieren möchten, müssen Sie die **Browsererweiterung „Meine Apps“ für die sichere Anmeldung** installieren, indem Sie auf **Erweiterung installieren** klicken.

    ![Erweiterung „Meine Apps“](common/install-myappssecure-extension.png)

1. Klicken Sie nach dem Hinzufügen der Erweiterung zum Browser auf **ArcGIS Enterprise einrichten**, um zur Anwendung ArcGIS Enterprise weitergeleitet zu werden. Geben Sie dort die Administratoranmeldeinformationen ein, um sich bei ArcGIS Enterprise anzumelden. Die Browsererweiterung konfiguriert die Anwendung automatisch für Sie und automatisiert die Schritte 3 bis 7.

    ![Einrichtungskonfiguration](common/setup-sso.png)

1. Wenn Sie ArcGIS Enterprise manuell einrichten möchten, melden Sie sich bei der ArcGIS Enterprise-Unternehmenswebsite als Administrator an.


1. Wählen Sie **Organisation > EINSTELLUNGEN BEARBEITEN**.

    ![Screenshot: Registerkarte „Organisation“ für ArcGIS Enterprise mit hervorgehobener Option „Einstellungen bearbeiten“](./media/arcgisenterprise-tutorial/configure-1.png)

1. Wählen Sie die Registerkarte **Security** (Sicherheit) aus.

    ![Screenshot: Ausgewählte Registerkarte „Sicherheit“](./media/arcgisenterprise-tutorial/configure-2.png)

1. Führen Sie einen Bildlauf nach unten zum Abschnitt **Enterprise-Anmeldungen über SAML** aus, und wählen Sie **ENTERPRISE-ANMELDUNG FESTLEGEN** aus.

    ![Screenshot: Abschnitt „Enterprise Logins via SAML“ (Unternehmensanmeldungen über SAML) mit der Option „Set Enterprise Login“ (Unternehmensanmeldung festlegen)](./media/arcgisenterprise-tutorial/configure-3.png)

1. Führen Sie im Abschnitt **Festlegen des Identitätsanbieters** die folgenden Schritte aus:

    ![Screenshot: Abschnitt „Set Identity Provider“ (Identitätsanbieter festlegen) zum Ausführen der hier beschriebenen Schritte](./media/arcgisenterprise-tutorial/configure-4.png)

    a. Geben Sie einen Namen wie **Azure Active Directory-Test** in das Textfeld **Name** ein.

    b. Fügen Sie im Textfeld **URL** den Wert der **Verbundmetadaten-URL der App** ein, den Sie aus dem Azure-Portal kopiert haben.

    c. Klicken Sie auf **Erweiterte Einstellungen anzeigen**, und kopieren Sie den Wert **Entitäts-ID** und fügen Sie ihn in das Textfeld **Bezeichner** im Abschnitt **ArcGIS-Enterprise-Domäne und URLs** im Azure-Portal ein.

    ![Screenshot: Abrufen der Entitäts-ID und Aktualisieren des Identitätsanbieters](./media/arcgisenterprise-tutorial/configure-5.png)

    d. Klicken Sie auf **IDENTITÄTSANBIETER AKTUALISIEREN**.

### <a name="create-arcgis-enterprise-test-user"></a>Erstellen eines ArcGIS Enterprise-Testbenutzers

In diesem Abschnitt wird in ArcGIS Enterprise ein Benutzer namens Britta Simon erstellt. ArcGIS Enterprise unterstützt die Just-in-Time-Benutzerbereitstellung, die standardmäßig aktiviert ist. Für Sie steht in diesem Abschnitt kein Aktionselement zur Verfügung. Wenn ein Benutzer noch nicht in ArcGIS Enterprise vorhanden ist, wird nach der Authentifizierung ein neuer Benutzer erstellt.

> [!Note]
> Wenn Sie einen Benutzer manuell erstellen müssen, wenden Sie sich an das [ArcGIS Enterprise-Supportteam](mailto:support@esri.com).

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

#### <a name="sp-initiated"></a>SP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für ArcGIS Enterprise weitergeleitet, wo Sie den Anmeldeflow initiieren können.  

* Rufen Sie direkt die ArcGIS Enterprise-Anmelde-URL auf, und initiieren Sie den Anmeldeflow.

#### <a name="idp-initiated"></a>IDP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch sollten Sie automatisch bei der ArcGIS Enterprise-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. 

Sie können auch den Microsoft-Bereich „Meine Apps“ verwenden, um die Anwendung in einem beliebigen Modus zu testen. Beim Klicken auf die Kachel „ArcGIS Enterprise“ in „Meine Apps“ geschieht Folgendes: Wenn Sie den SP-Modus konfiguriert haben, werden Sie zum Initiieren des Anmeldeflows zur Anmeldeseite der Anwendung weitergeleitet. Wenn Sie den IDP-Modus konfiguriert haben, sollten Sie automatisch bei der ArcGIS Enterprise-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von ArcGIS Enterprise können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-any-app)
