---
title: 'Tutorial: Azure Active Directory-Integration mit Andromeda | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Andromeda konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/12/2021
ms.author: jeedes
ms.openlocfilehash: e15c0cf41010fb5336dcc49ebba4ea43c7c91b7b
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132289412"
---
# <a name="tutorial-azure-active-directory-integration-with-andromeda"></a>Tutorial: Azure Active Directory-Integration mit Andromeda

In diesem Tutorial erfahren Sie, wie Sie Andromeda in Azure Active Directory (Azure AD) integrieren. Die Integration von Andromeda in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf Andromeda hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihrem Azure AD-Konto automatisch bei Andromeda anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit Andromeda konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Sollten Sie nicht über eine Azure AD-Umgebung verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) verwenden.
* Andromeda-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Andromeda unterstützt **SP- und IDP-initiiertes** einmaliges Anmelden.
* Andromeda unterstützt die **Just-In-Time**-Benutzerbereitstellung.

## <a name="add-andromeda-from-the-gallery"></a>Hinzufügen von Andromeda aus dem Katalog

Zum Konfigurieren der Integration von Andromeda in Azure AD müssen Sie Andromeda aus dem Katalog zur Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Andromeda** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Andromeda** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-andromeda"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Andromeda

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Andromeda mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Andromeda eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Andromeda die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
   1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
   1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
2. **[Konfigurieren des einmaligen Anmeldens für Andromeda](#configure-andromeda-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
   1. **[Erstellen eines Andromeda-Testbenutzers](#create-andromeda-test-user)** , um in Andromeda ein Pendant von Britta Simon zu erhalten, das mit ihrer Darstellung in Azure AD verknüpft ist.
3. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Andromeda** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus, wenn Sie die Anwendung im **IDP-initiierten** Modus konfigurieren möchten: 

   a. Geben Sie im Textfeld **Bezeichner** eine URL im folgenden Format ein: `https://<tenantURL>.ngcxpress.com/`

   b. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://<tenantURL>.ngcxpress.com/SAMLConsumer.aspx`

1. Klicken Sie auf **Zusätzliche URLs festlegen**, und führen Sie den folgenden Schritt aus, wenn Sie die Anwendung im **SP-initiierten Modus** konfigurieren möchten:

   Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<tenantURL>.ngcxpress.com/SAMLLogon.aspx`

   > [!NOTE]
   > Hierbei handelt es sich um Beispielwerte. Sie aktualisieren den Wert mit dem tatsächlichen Bezeichner und der tatsächlichen Antwort-URL und Anmelde-URL. Dies wird später in diesem Tutorial beschrieben.

1. Die Andromeda-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Konfigurieren Sie die folgenden Ansprüche für diese Anwendung. Sie können die Werte dieser Attribute im Abschnitt **Benutzerattribute** auf der Anwendungsintegrationsseite verwalten. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf die Schaltfläche **Bearbeiten**, um das Dialogfeld **Benutzerattribute** zu öffnen.

   ![Screenshot: Benutzerattribute](common/edit-attribute.png)

   > [!NOTE]
   > Löschen Sie beim Einrichten dieser Werte die NameSpace-Definitionen.

1. Bearbeiten Sie im Dialogfeld **Benutzerattribute** im Abschnitt **Benutzeransprüche** die Ansprüche mithilfe des Symbols zum **Bearbeiten**, oder fügen Sie die Ansprüche über **Neuen Anspruch hinzufügen** hinzu, um das SAML-Tokenattribut wie in der obigen Abbildung gezeigt zu konfigurieren. Führen Sie dann die folgenden Schritte aus:

   | Name    | Quellattribut  |
   | ------- | ----------------- |
   | Rolle (role)    | App-spezifische Rolle |
   | type    | App-Typ          |
   | company | CompanyName       |

   > [!NOTE]
   > Andromeda erwartet, dass der Anwendung Rollen für Benutzer zugewiesen werden. Richten Sie diese Rollen in Azure AD ein, damit Benutzern die passenden Rollen zugewiesen werden können. Informationen zum Konfigurieren von Rollen in Azure AD finden Sie [hier](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui).

   a. Klicken Sie auf **Neuen Anspruch hinzufügen**, um das Dialogfeld **Benutzeransprüche verwalten** zu öffnen.

   ![Screenshot: Seite „Benutzeransprüche“ mit den Optionen „Neuen Anspruch hinzufügen“ und „Speichern“](common/new-save-attribute.png)

   ![Screenshot: Seite „Benutzeransprüche verwalten“ zum Eingeben der in diesem Schritt beschriebenen Werte](common/new-attribute-details.png)

   b. Geben Sie im Textfeld **Name** den für die Zeile angezeigten Attributnamen ein.

   c. Lassen Sie den **Namespace** leer.

   d. Wählen Sie „Source“ als **Attribut** aus.

   e. Geben Sie in der Liste **Quellattribut** den für diese Zeile angezeigten Attributwert ein.

   f. Klicken Sie auf **OK**.

   g. Klicken Sie auf **Speichern**.

1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um das Ihrer Anforderung entsprechende **Zertifikat (Base64)** aus den angegebenen Optionen herunterzuladen und auf Ihrem Computer zu speichern.

   ![Downloadlink für das Zertifikat](common/certificatebase64.png)

1. Kopieren Sie im Abschnitt **Andromeda einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Andromeda gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Andromeda** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn Sie die Rollen wie oben erläutert eingerichtet haben, können Sie sie in der Dropdownliste **Rolle auswählen** auswählen.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

### <a name="configure-andromeda-sso"></a>Konfigurieren des einmaligen Anmeldens für Andromeda

1. Melden Sie sich bei Ihrer Andromeda-Unternehmenswebsite als Administrator an.

2. Klicken Sie oben auf der Menüleiste auf **Admin** (Administrator), und navigieren Sie zu **Administration** (Verwaltung).

   ![Andromeda-Administrator](./media/andromedascm-tutorial/admin.png)

3. Klicken Sie links auf der Symbolleiste unter **Interfaces** (Schnittstellen) auf **SAML Configuration** (SAML-Konfiguration).

   ![Andromeda: SAML](./media/andromedascm-tutorial/interface.png)

4. Führen Sie auf der Seite mit dem Abschnitt **SAML Configuration** (SAML-Konfiguration) die folgenden Schritte aus:

   ![Andromeda-Konfiguration](./media/andromedascm-tutorial/configure.png)

   a. Aktivieren Sie **Enable SSO with SAML** (SSO mit SAML zulassen).

   b. Kopieren Sie im Abschnitt **Andromeda Information** (Informationen zu Andromeda) den Wert für **SP Identity** (SP-Identität), und fügen Sie ihn im Abschnitt **Grundlegende SAML-Konfiguration** ins Textfeld **Bezeichner** ein.

   c. Kopieren Sie den Wert **Consumer URL** (Consumer-URL), und fügen Sie ihn im Abschnitt **Grundlegende SAML-Konfiguration** ins Textfeld **Antwort-URL** ein.

   d. Kopieren Sie den Wert **Logon URL** (Anmelde-URL), und fügen Sie ihn im Abschnitt **Grundlegende SAML-Konfiguration** ins Textfeld **Anmelde-URL** ein.

   e. Geben Sie im Abschnitt **SAML Identity Provider** (SAML-Identitätsanbieter) den IdP-Namen ein.

   f. Fügen Sie in das Textfeld **Single Sign On End Point** (Endpunkt für einmaliges Anmelden) den Wert der **Anmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

   g. Öffnen Sie das aus dem Azure-Portal heruntergeladene **Base64-codierte Zertifikat** im Editor, und fügen Sie es in das Textfeld **X 509 Certificate** (X.509-Zertifikat) ein.

   h. Weisen Sie den folgenden Attributen die entsprechenden Werte zu, um die SSO-Anmeldung über Azure AD zu ermöglichen. Das Attribut **User ID** (Benutzer-ID) ist für die Anmeldung erforderlich. Für die Bereitstellung sind die Attribute **Email** (E-Mail), **Company** (Unternehmen), **User Type** (Benutzertyp) and **Role** (Rolle) erforderlich. In diesem Abschnitt definieren Sie die Zuordnung von Attributen (Namen und Werte) zu den Attributen des Azure-Portals.

   ![Andromeda-Attribute](./media/andromedascm-tutorial/mapping.png)

   i. Klicken Sie auf **Speichern**.

### <a name="create-andromeda-test-user"></a>Erstellen eines Andromeda-Testbenutzers

In diesem Abschnitt wird eine Benutzerin mit dem Namen „Britta Simon“ in Andromeda erstellt. Andromeda unterstützt die Just-in-Time-Benutzerbereitstellung, die standardmäßig aktiviert ist. Für Sie steht in diesem Abschnitt kein Aktionselement zur Verfügung. Ist ein Benutzer noch nicht in Andromeda vorhanden, wird nach der Authentifizierung ein neuer Benutzer erstellt. Wenn Sie einen Benutzer manuell erstellen müssen, setzen Sie sich mit dem [Supportteam für den Andromeda-Client](https://www.ngcsoftware.com/support/) in Verbindung.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen:

#### <a name="sp-initiated"></a>SP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für Andromeda weitergeleitet, wo Sie den Anmeldeflow initiieren können.

* Rufen Sie direkt die Andromeda-Anmelde-URL auf, und initiieren Sie den Anmeldeflow.

#### <a name="idp-initiated"></a>IDP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch sollten Sie automatisch bei der Andromeda-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben.

* Sie können auch den Microsoft-Bereich „Meine Apps“ verwenden, um die Anwendung in einem beliebigen Modus zu testen. Beim Klicken auf die Kachel „Andromeda“ in „Meine Apps“ geschieht Folgendes: Wenn Sie die Anwendung im SP-Modus konfiguriert haben, werden Sie zum Initiieren des Anmeldeflows zur Anmeldeseite der Anwendung weitergeleitet. Wenn Sie die Anwendung im IDP-Modus konfiguriert haben, sollten Sie automatisch bei der Andromeda-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Andromeda können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-any-app)
