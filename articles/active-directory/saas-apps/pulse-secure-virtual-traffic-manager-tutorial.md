---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Pulse Secure Virtual Traffic Manager | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie einmaliges Anmelden zwischen Azure Active Directory und Pulse Secure Virtual Traffic Manager konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/18/2021
ms.author: jeedes
ms.openlocfilehash: 70233bbc587df3acffdc2d668665fe000d4c5c67
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132344216"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-pulse-secure-virtual-traffic-manager"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Pulse Secure Virtual Traffic Manager

In diesem Tutorial erfahren Sie, wie Sie Pulse Secure Virtual Traffic Manager in Azure Active Directory (Azure AD) integrieren. Die Integration von Pulse Secure Virtual Traffic Manager in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf Pulse Secure Virtual Traffic Manager hat.
* Ermöglichen Sie Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Pulse Secure Virtual Traffic Manager anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Pulse Secure Virtual Traffic Manager-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Pulse Secure Virtual Traffic Manager unterstützt **SP**-initiiertes einmaliges Anmelden.

## <a name="add-pulse-secure-virtual-traffic-manager-from-the-gallery"></a>Hinzufügen von Pulse Secure Virtual Traffic Manager aus dem Katalog

Zum Konfigurieren der Integration von Pulse Secure Virtual Traffic Manager in Azure AD müssen Sie Pulse Secure Virtual Traffic Manager aus dem Katalog zur Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** im Suchfeld den Begriff **Pulse Secure Virtual Traffic Manager** ein.
1. Wählen Sie im Ergebnisbereich **Pulse Secure Virtual Traffic Manager** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-pulse-secure-virtual-traffic-manager"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Pulse Secure Virtual Traffic Manager

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Pulse Secure Virtual Traffic Manager mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Pulse Secure Virtual Traffic Manager eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Pulse Secure Virtual Traffic Manager die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Pulse Secure Virtual Traffic Manager](#configure-pulse-secure-virtual-traffic-manager-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
    1. **[Erstellen eines Pulse Secure Virtual Traffic Manager-Testbenutzers](#create-pulse-secure-virtual-traffic-manager-test-user)** , um eine Entsprechung von B. Simon in Pulse Secure Virtual Traffic Manager zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist.
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Pulse Secure Virtual Traffic Manager** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    a. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<PUBLISHED VIRTUAL SERVER FQDN>/saml/consume`.

    b. Geben Sie im Textfeld **Bezeichner (Entitäts-ID)** eine URL im folgenden Format ein: `https://<PUBLISHED VIRTUAL SERVER FQDN>/saml/metadata`.

    c. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://<PUBLISHED VIRTUAL SERVER FQDN>/saml/consume`

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch die tatsächlichen Werte für die Anmelde-URL, die Antwort-URL und den Bezeichner. Diese Werte erhalten Sie vom [Supportteam für den Pulse Secure Virtual Traffic Manager-Client](mailto:support@pulsesecure.net). Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

1. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** zum Eintrag **Zertifikat (Base64)** . Wählen Sie **Herunterladen** aus, um das Zertifikat herunterzuladen, und speichern Sie es auf Ihrem Computer.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

1. Kopieren Sie im Abschnitt **Pulse Secure Virtual Traffic Manager einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Pulse Secure Virtual Traffic Manager gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Pulse Secure Virtual Traffic Manager** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-pulse-secure-virtual-traffic-manager-sso"></a>Konfigurieren des einmaligen Anmeldens für Pulse Secure Virtual Traffic Manager

In diesem Abschnitt wird die Konfiguration beschrieben, die zum Aktivieren der SAML-Authentifizierung von Azure AD für Pulse Secure Virtual Traffic Manager erforderlich ist. Alle Konfigurationsänderungen an Pulse Secure Virtual Traffic Manager werden mithilfe der Verwaltungsweboberfläche vorgenommen. 

### <a name="create-a-saml-trusted-identity-provider"></a>Erstellen eines vertrauenswürdigen SAML-Identitätsanbieters

a. Navigieren Sie zu **Pulse Virtual Traffic Manager Appliance-Verwaltungsoberfläche > Katalog > SAML > Katalog der vertrauenswürdigen Identitätsanbieter**, und klicken Sie auf **Bearbeiten**.

![Seite mit SAML-Katalogen](./media/pulse-secure-virtual-traffic-manager-tutorial/saml-catalogs.png)

b. Fügen Sie die Details für den neuen vertrauenswürdigen SAML-Identitätsanbieter hinzu, indem Sie die Informationen von der Azure AD-Unternehmensanwendung auf der Seite „Einstellungen für einmaliges Anmelden“ kopieren und dann auf **Neuen vertrauenswürdigen Identitätsanbieter erstellen** klicken.

![Erstellen eines neuen vertrauenswürdigen Identitätsanbieters](./media/pulse-secure-virtual-traffic-manager-tutorial/identity-provider.png)

* Geben Sie im Textfeld **Name** einen Namen für den vertrauenswürdigen Identitätsanbieter ein. 

* Geben Sie im Textfeld **Entity_id** den Wert für den **Azure AD-Bezeichner** ein, den Sie im Azure-Portal kopiert haben.  

* Geben Sie im Textfeld **Url** den Wert der **Anmelde-URL** ein, den Sie im Azure-Portal kopiert haben. 

* Öffnen Sie das aus dem Azure-Portal heruntergeladene **Zertifikat** im Editor, und fügen Sie den Inhalt im Textfeld **Zertifikat** ein.

c. Überprüfen Sie, ob der neue SAML-Identitätsanbieter erfolgreich erstellt wurde. 

![Überprüfen des vertrauenswürdigen Identitätsanbieters](./media/pulse-secure-virtual-traffic-manager-tutorial/verify-identity-provider.png)

### <a name="configure-the-virtual-server-to-use-azure-ad-authentication"></a>Konfigurieren des virtuellen Servers für die Verwendung der Azure AD-Authentifizierung

a. Navigieren Sie zu **Pulse Virtual Traffic Manager Appliance-Verwaltungsoberfläche > Dienste > Virtuelle Server**, und klicken Sie neben dem zuvor erstellten virtuellen Server auf **Bearbeiten**.

![Virtuelle Server bearbeiten](./media/pulse-secure-virtual-traffic-manager-tutorial/virtual-servers.png)

b. Klicken Sie im Abschnitt **Authentifizierung** auf **Bearbeiten**. 

![Abschnitt „Authentifizierung“](./media/pulse-secure-virtual-traffic-manager-tutorial/authentication.png)

c. Konfigurieren Sie für den virtuellen Server die folgenden Authentifizierungseinstellungen: 

1. Authentifizierung –

    ![Authentifizierungseinstellungen für virtuellen Server](./media/pulse-secure-virtual-traffic-manager-tutorial/authentication-1.png)

    a. Wählen Sie unter **Auth!type** den Eintrag **SAML-Dienstanbieter** aus. 

    b. Legen Sie **Auth!verbose** auf „Ja“ fest, um mögliche Probleme bei der Authentifizierung zu beheben. Andernfalls übernehmen Sie die Standardeinstellung „Nein“. 

2. Authentifizierungssitzungsverwaltung –

    ![Authentifizierungssitzungsverwaltung](./media/pulse-secure-virtual-traffic-manager-tutorial/authentication-session.png)

    a. Übernehmen Sie für **Auth!session!cookie_name** den Standardwert „VS_SamlSP_Auth“. 

    b. Übernehmen Sie für **auth!session!timeout** den Standardwert „7200“. 

    c. Legen Sie **auth!session!log_external_state** auf „Ja“ fest, um mögliche Probleme bei der Authentifizierung zu beheben. Andernfalls übernehmen Sie die Standardeinstellung „Nein“. 

    d. Ändern Sie für **auth!session!cookie_attributes** den Wert in „HTTPOnly“.

3. SAML-Dienstanbieter –

    ![SAML-Dienstanbieter](./media/pulse-secure-virtual-traffic-manager-tutorial/service-provider.png)

    a. Legen Sie im Textfeld **auth!saml!sp_entity_id** dieselbe URL fest, die Sie bei der Konfiguration des einmaligen Anmeldens in Azure AD als Bezeichner (Entitäts-ID) verwendet haben, z. B. `https://pulseweb.labb.info/saml/metadata`. 

    b. Legen Sie für **auth!saml!sp_acs_url** dieselbe URL fest, die Sie bei der Konfiguration des einmaligen Anmeldens in Azure AD als Antwort-URL (Assertionsverbraucherdienst-URL) verwendet haben, z. B. `https://pulseweb.labb.info/saml/consume`. 

    c. Wählen Sie unter **auth!saml!idp** den **vertrauenswürdigen Identitätsanbieter** aus, den Sie im vorherigen Schritt erstellt haben. 

    d. Übernehmen Sie für „auth!saml!time_tolerance“ den Standardwert „5 Sekunden“. 

    e. Wählen Sie unter „auth!saml!nameid_format“ die Option **keine Angabe** aus.

    f. Übernehmen Sie die Änderungen, indem Sie unten auf der Seite auf **Aktualisieren** klicken.
    
### <a name="create-pulse-secure-virtual-traffic-manager-test-user"></a>Erstellen des Pulse Secure Virtual Traffic Manager-Testbenutzers

In diesem Abschnitt erstellen Sie in Pulse Secure Virtual Traffic Manager einen Benutzer namens Britta Simon. Arbeiten Sie mit dem [Supportteam von Pulse Secure Virtual Traffic Manager](mailto:support@pulsesecure.net) zusammen, um die Benutzer der Pulse Secure Virtual Traffic Manager-Plattform hinzuzufügen. Benutzer müssen erstellt und aktiviert werden, damit Sie einmaliges Anmelden verwenden können.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für Pulse Secure Virtual Traffic Manager weitergeleitet, wo Sie den Anmeldeflow initiieren können. 

* Navigieren Sie direkt zur Pulse Secure Virtual Traffic Manager-Anmelde-URL, und initiieren Sie dort den Anmeldeflow.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie unter „Meine Apps“ auf die Kachel „Pulse Secure Virtual Traffic Manager“ klicken, werden Sie zur Anmelde-URL für Pulse Secure Virtual Traffic Manager weitergeleitet. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Pulse Secure Virtual Traffic Manager können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-aad)
