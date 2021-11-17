---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD mit Kantega SSO for Bitbucket'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Kantega SSO for Bitbucket konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/28/2021
ms.author: jeedes
ms.openlocfilehash: f3b50658114c843d0de5ff609b132d67a9cee52d
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132291196"
---
# <a name="tutorial-azure-ad-sso-integration-with-kantega-sso-for-bitbucket"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD mit Kantega SSO for Bitbucket

In diesem Tutorial erfahren Sie, wie Sie Kantega SSO for Bitbucket in Azure Active Directory (Azure AD) integrieren. Die Integration von Kantega SSO for Bitbucket in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf Kantega SSO for Bitbucket hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Kantega SSO for Bitbucket anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Zum Konfigurieren der Azure AD-Integration mit Kantega SSO for Bitbucket benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Sollten Sie nicht über eine Azure AD-Umgebung verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Kantega SSO for Bitbucket-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Kantega SSO for Bitbucket unterstützt **SP- und IDP-initiiertes** einmaliges Anmelden.

## <a name="add-kantega-sso-for-bitbucket-from-the-gallery"></a>Hinzufügen von Kantega SSO for Bitbucket aus dem Katalog

Zum Konfigurieren der Integration von Kantega SSO for Bitbucket in Azure AD müssen Sie Kantega SSO for Bitbucket aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Kantega SSO for Bitbucket** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Kantega SSO for Bitbucket** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-kantega-sso-for-bitbucket"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Kantega SSO for Bitbucket

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Kantega SSO for Bitbucket mithilfe eines Testbenutzers mit dem Namen **B.Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Kantega SSO for Bitbucket eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Kantega SSO for Bitbucket die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Kantega SSO for Bitbucket](#configure-kantega-sso-for-bitbucket-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines Kantega SSO for Bitbucket-Testbenutzers](#create-kantega-sso-for-bitbucket-test-user)** , um eine Entsprechung von B. Simon in Kantega SSO for Bitbucket zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Kantega SSO for Bitbucket** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus, wenn Sie die Anwendung im **IDP**-initiierten Modus konfigurieren möchten:

    a. Geben Sie im Textfeld **Bezeichner** eine URL im folgenden Format ein: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    b. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

5. Klicken Sie auf **Zusätzliche URLs festlegen**, und führen Sie den folgenden Schritt aus, wenn Sie die Anwendung im **SP-initiierten Modus** konfigurieren möchten:

    Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch den tatsächlichen Bezeichner, die Antwort-URL und die Anmelde-URL. Diese Werte werden während der Konfiguration des Bitbucket-Plug-Ins empfangen, die später im Tutorial beschrieben wird.

6. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um den Ihren Anforderungen entsprechenden **Verbundmetadaten-XML**-Code aus den verfügbaren Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

7. Kopieren Sie im Abschnitt **Kantega SSO for Bitbucket einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Kantega SSO for Bitbucket gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Kantega SSO for Bitbucket** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-kantega-sso-for-bitbucket-sso"></a>Konfigurieren des einmaligen Anmeldens für Kantega SSO for Bitbucket

1. Melden Sie sich in einem anderen Webbrowserfenster im Bitbucket-Verwaltungsportal als Administrator an.

1. Klicken Sie auf das Zahnrad und dann auf **Find new add-ons** (Nach neuen Add-Ons suchen).

    ![Screenshot: Bitbucket-Verwaltung mit Auswahl von „Find new add-ons“ (Nach neuen Add-Ons suchen)](./media/kantegassoforbitbucket-tutorial/admin.png)

1. Suchen Sie nach **Kantega SSO for Bitbucket SAML & Kerberos**, und klicken Sie auf die Schaltfläche **Installieren**, um das neue SAML-Plug-In zu installieren.

    ![Screenshot: Kantega SSO for Bitbucket SAML & Kerberos mit der Option „Installieren“](./media/kantegassoforbitbucket-tutorial/menu.png)

1. Die Installation des Plug-Ins wird gestartet.

    ![Screenshot: Installationsstatus](./media/kantegassoforbitbucket-tutorial/installation.png)

1. Gehen Sie nach Abschluss der Installation wie folgt vor: Klicken Sie auf **Schließen**.

    ![Screenshot: Schaltfläche „Close“ (Schließen)](./media/kantegassoforbitbucket-tutorial/license.png)

1. Klicken Sie auf **Manage**.

    ![Screenshot: Schaltfläche „Manage“ (Verwalten)](./media/kantegassoforbitbucket-tutorial/integration.png)

1. Klicken Sie auf **Konfigurieren**, um das neue Plug-In zu konfigurieren.

    ![Screenshot: Benutzerseitig installierte Add-Ons mit Auswahl von „Configure“ (Konfigurieren)](./media/kantegassoforbitbucket-tutorial/profile.png)

1. Im Abschnitt **SAML**: Wählen Sie in der Dropdownliste **Identitätsanbieter hinzufügen** die Option **Azure Active Directory (Azure AD)** .

    ![Screenshot: Kantega SSO mit Auswahl von „Azure AD“ als Identitätsanbieter](./media/kantegassoforbitbucket-tutorial/azure.png)

1. Wählen Sie als Abonnementebene die Option **Basic**.

    ![Screenshot: Vorbereitung von Azure AD mit Auswahl von „Basic“](./media/kantegassoforbitbucket-tutorial/subscription.png)

1. Führen Sie im Abschnitt **App-Eigenschaften** die folgenden Schritte aus:

    ![Screenshot: Abschnitt „App-Eigenschaften“, in dem Sie die Informationen dieses Schritts angeben können](./media/kantegassoforbitbucket-tutorial/properties.png)

    a. Kopieren Sie den Wert für den **App-ID-URI**, und verwenden Sie ihn als **Bezeichner, Antwort-URL und Anmelde-URL** im Abschnitt **Grundlegende SAML-Konfiguration** des Azure-Portals.

    b. Klicken Sie auf **Weiter**.

1. Führen Sie im Abschnitt **Metadata import** (Metadatenimport) die folgenden Schritte aus:

    ![Screenshot: Abschnitt „Metadatenimport“, in dem Sie auf eine Metadatendatei zugreifen können](./media/kantegassoforbitbucket-tutorial/metadata.png)

    a. Wählen Sie **Metadata file on my computer** (Metadatendatei auf meinem Computer), und laden Sie die Metadatendatei hoch, die Sie aus dem Azure-Portal heruntergeladen haben.

    b. Klicken Sie auf **Weiter**.

1. Führen Sie im Abschnitt **Name and SSO location** (Name und SSO-Standort) die folgenden Schritte aus:

    ![Screenshot: Abschnitt „Name and SSO location“ (Name und SSO-Standort) mit „Azure AD“ als Name des Identitätsanbieters](./media/kantegassoforbitbucket-tutorial/location.png)

    a. Fügen Sie im Textfeld **Name des Identitätsanbieters** den Namen des Identitätsanbieters hinzu (z.B. Azure AD).

    b. Klicken Sie auf **Weiter**.

1. Überprüfen Sie das Signaturzertifikat, und klicken Sie auf **Weiter**.

    ![Screenshot: Überprüfung der Signatur](./media/kantegassoforbitbucket-tutorial/certificate.png)

1. Führen Sie im Abschnitt **Bitbucket user accounts** (Bitbucket-Benutzerkonten) die folgenden Schritte aus:

    ![Screenshot: Bitbucket-Benutzerkonten mit der Option zum Erstellen von Benutzern](./media/kantegassoforbitbucket-tutorial/accounts.png)

    a. Wählen Sie **Create users in Bitbucket's internal Directory if needed** (Benutzer im internen Bitbucket-Verzeichnis erstellen, falls erforderlich), und geben Sie den entsprechenden Namen der Gruppe für Benutzer ein (können mehrere durch Kommas getrennte Gruppen sein).

    b. Klicken Sie auf **Weiter**.

1. Klicken Sie auf **Fertig stellen**.

    ![Screenshot: Seite „Zusammenfassung“](./media/kantegassoforbitbucket-tutorial/groups.png)

1. Führen Sie im Abschnitt **Known domains for Azure AD** (Bekannte Domänen für Azure AD) die folgenden Schritte aus:

    ![Screenshot: Abschnitt „Known domains for Azure AD“ (Bekannte Domänen für Azure AD), in dem Sie die Schritte ausführen können](./media/kantegassoforbitbucket-tutorial/domain.png)

    a. Wählen Sie im linken Bereich der Seite die Option **Known domains** (Bekannte Domänen).

    b. Geben Sie den Domänennamen im Textfeld **Known domains** (Bekannte Domänen) ein.

    c. Klicken Sie auf **Speichern**.

### <a name="create-kantega-sso-for-bitbucket-test-user"></a>Erstellen eines Kantega SSO for Bitbucket-Testbenutzers

Damit sich Azure AD-Benutzer bei Bitbucket anmelden können, müssen sie in Bitbucket bereitgestellt werden. Im Fall von Kantega SSO for Bitbucket ist die Bereitstellung eine manuelle Aufgabe.

**Führen Sie zum Bereitstellen eines Benutzerkontos die folgenden Schritte aus:**

1. Melden Sie sich bei der Bitbucket-Unternehmenswebsite als Administrator an.

1. Klicken Sie auf das Symbol „Einstellungen“.

    ![Screenshot: Symbol „Einstellungen“](./media/kantegassoforbitbucket-tutorial/user.png) 

1. Klicken Sie im Registerkartenabschnitt **Verwaltung** auf **Benutzer**.

    ![Screenshot: Bitbucket-Verwaltung mit Auswahl von „Benutzer“ ](./media/kantegassoforbitbucket-tutorial/add-user.png)

1. Klicken Sie auf **Benutzer erstellen**.

    ![Screenshot: Bitbucket-Verwaltung mit Auswahl von „Benutzer erstellen“](./media/kantegassoforbitbucket-tutorial/create-user.png)     

1. Führen Sie auf der Dialogfeldseite **Benutzer erstellen** die folgenden Schritte aus:

    ![Screenshot: Dialogfeld „Benutzer erstellen“, in dem Sie die Schritte ausführen können](./media/kantegassoforbitbucket-tutorial/details.png) 

    a. Geben Sie im Textfeld **Benutzername** die E-Mail-Adresse des Benutzers, z.B. Brittasimon@contoso.com, ein.

    b. Geben Sie im Textfeld **Vollständiger Name** den vollständigen Namen des Benutzers, z.B. „Britta Simon“, ein.

    c. Geben Sie im Textfeld **E-Mail-Adresse** die E-Mail-Adresse des Benutzers, z.B. Brittasimon@contoso.com, ein.

    d. Geben Sie im Textfeld **Kennwort** das Kennwort des Benutzers ein.

    e. Geben Sie im Textfeld **Kennwort bestätigen** das Kennwort des Benutzers erneut ein.

    f. Klicken Sie auf **Benutzer erstellen**.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

#### <a name="sp-initiated"></a>SP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für Kantega SSO for Bitbucket weitergeleitet, wo Sie den Anmeldeflow initiieren können.  

* Navigieren Sie direkt zur Anmelde-URL für Kantega SSO for Bitbucket, und initiieren Sie den Anmeldeflow.

#### <a name="idp-initiated"></a>IDP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch sollten Sie automatisch bei der Kantega SSO for Bitbucket-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. 

Sie können auch den Microsoft-Bereich „Meine Apps“ verwenden, um die Anwendung in einem beliebigen Modus zu testen. Beim Klicken auf die Kachel „Kantega SSO for Bitbucket“ unter „Meine Apps“ geschieht Folgendes: Wenn Sie die Anwendung im SP-Modus konfiguriert haben, werden Sie zum Initiieren des Anmeldeflows zur Anmeldeseite der Anwendung weitergeleitet. Wenn Sie die Anwendung im IDP-Modus konfiguriert haben, sollten Sie automatisch bei der Kantega SSO for Bitbucket-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Kantega SSO for Bitbucket können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-aad)
