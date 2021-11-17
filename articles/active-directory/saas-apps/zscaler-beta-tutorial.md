---
title: 'Tutorial: Azure Active Directory-Integration in Zscaler Beta | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Zscaler Beta konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/02/2021
ms.author: jeedes
ms.openlocfilehash: 96a73d9bba60f14c4f9b70aca9cf546cd3cb7e3f
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132323584"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-beta"></a>Tutorial: Azure Active Directory-Integration in Zscaler Beta

In diesem Tutorial erfahren Sie, wie Sie Zscaler Beta in Azure Active Directory (Azure AD) integrieren.
Bei der Integration von Zscaler Beta in Azure AD haben Sie folgende Möglichkeiten:

* Steuern Sie in Azure AD, wer Zugriff auf Zscaler Beta hat.
* Sie können es Ihren Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei Zscaler Beta anzumelden (einmaliges Anmelden; Single Sign-On, SSO). Diese Zugriffssteuerung wird als einmaliges Anmelden (Single Sign-On, SSO) bezeichnet.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration in Zscaler Beta konfigurieren zu können, ist Folgendes erforderlich:

* Ein Azure AD-Abonnement Sollten Sie nicht über eine Azure AD-Umgebung verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) verwenden.
* Zscaler Beta-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Zscaler Beta unterstützt **SP**-initiiertes einmaliges Anmelden.
* Zscaler Beta unterstützt die **Just-In-Time**-Benutzerbereitstellung.
* Zscaler Beta unterstützt [automatisierte Benutzerbereitstellung](zscaler-beta-provisioning-tutorial.md).

## <a name="adding-zscaler-beta-from-the-gallery"></a>Hinzufügen von Zscaler Beta aus dem Katalog

Zum Konfigurieren der Integration von Zscaler Beta in Azure AD müssen Sie Zscaler Beta aus dem Katalog zur Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Zscaler Beta** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Zscaler Beta** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-zscaler-beta"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Zscaler Beta

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Zscaler Beta mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Zscaler Beta eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Zscaler Beta die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
   1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
   1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Zscaler Beta](#configure-zscaler-beta-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
   1. **[Erstellen eines Zscaler Beta-Testbenutzers](#create-zscaler-beta-test-user)** , um ein Pendant von B. Simon in Zscaler Beta zu erhalten, das mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Zscaler Beta** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Geben Sie im Abschnitt **Grundlegende SAML-Konfiguration** die Werte für die folgenden Felder ein:

   Geben Sie im Textfeld **Anmelde-URL** die von Ihren Benutzern für die Anmeldung bei der Zscaler Beta-Anwendung verwendete URL ein.

   > [!NOTE]
   > Dieser Wert entspricht nicht dem tatsächlichen Wert. Aktualisieren Sie den Wert mit der tatsächlichen Anmelde-URL. Den Wert erhalten Sie vom [Supportteam für den Zscaler Beta-Client](https://www.zscaler.com/company/contact).

1. Die Zscaler Beta-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Sie müssen Ihrer Konfiguration der SAML-Tokenattribute benutzerdefinierte Attributzuordnungen hinzufügen. Der folgende Screenshot zeigt die Liste der Standardattribute. Klicken Sie auf **Bearbeiten**, um das Dialogfeld **Benutzerattribute** zu öffnen.

   ![Dialogfeld „Benutzerattribute“](common/edit-attribute.png)

1. Die Zscaler Beta-Anwendung erwartet, dass in der SAML-Antwort noch einige weitere Attribute zurückgegeben werden. Führen Sie im Dialogfeld **Benutzerattribute** im Abschnitt **Benutzeransprüche** die folgenden Schritte aus, um das SAML-Tokenattribut wie in der folgenden Tabelle gezeigt hinzuzufügen:

   | Name     | Quellattribut   |
   | -------- | ------------------ |
   | memberOf | user.assignedroles |

   a. Wählen Sie **Neuen Anspruch hinzufügen** aus, um das Dialogfeld **Benutzeransprüche verwalten** zu öffnen.

   b. Geben Sie im Feld **Name** den für die Zeile angezeigten Attributnamen ein.

   c. Lassen Sie das Feld **Namespace** leer.

   d. Wählen Sie für **Quelle** die Option **Attribut** aus.

   e. Geben Sie in der Liste **Quellattribut** den für diese Zeile angezeigten Attributwert ein.

   f. Klicken Sie auf **OK**.

   g. Wählen Sie **Speichern** aus.

   > [!NOTE]
   > Klicken Sie [hier](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui), um herauszufinden, wie Sie die Rolle in Azure AD konfigurieren.

1. Wählen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** die Option **Herunterladen** aus, um das **Zertifikat (Base64)** herunterzuladen. Speichern Sie das Zertifikat auf Ihrem Computer.

   ![Downloadlink für Zertifikat](common/certificatebase64.png)

1. Kopieren Sie im Abschnitt **Zscaler Beta einrichten** die URLs gemäß Ihren Anforderungen:

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Zscaler Beta gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Liste der Anwendungen **Zscaler Beta** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn Sie die Rollen wie oben erläutert eingerichtet haben, können Sie sie in der Dropdownliste **Rolle auswählen** auswählen.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-zscaler-beta-sso"></a>Konfigurieren des einmaligen Anmeldens für Zscaler Beta

1. Wenn Sie die Konfiguration in Zscaler Beta automatisieren möchten, installieren Sie die **Browsererweiterung „Meine Apps“ für die sichere Anmeldung**, indem Sie **Erweiterung installieren** auswählen.

   ![Erweiterung „Meine Apps“](common/install-myappssecure-extension.png)

2. Wenn Sie die Erweiterung zum Browser hinzugefügt haben, werden Sie durch die Auswahl von **Zscaler Beta einrichten** zur Ihrer Zscaler Beta-Anwendung weitergeleitet. Geben Sie dort die Administratoranmeldeinformationen ein, um sich bei Zscaler Beta anzumelden. Die Browsererweiterung konfiguriert die Anwendung automatisch für Sie und automatisiert die Schritte 3 bis 6.

   ![Einrichtungskonfiguration](common/setup-sso.png)

3. Öffnen Sie zum manuellen Einrichten von Zscaler Beta ein neues Webbrowserfenster. Melden Sie sich bei Ihrer Zscaler Beta-Unternehmenswebsite als Administrator an, und führen Sie die folgenden Schritte aus:

4. Navigieren Sie zu **Administration** > **Authentication** > **Authentication Settings** (Verwaltung > Authentifizierung > Authentifizierungseinstellungen), und führen Sie die folgenden Schritte aus.

   ![Verwaltung](./media/zscaler-beta-tutorial/settings.png "Verwaltung")

   a. Wählen Sie unter **Authentifizierungstyp** die Option **SAML** aus.

   b. Wählen Sie **Configure SAML** (SAML konfigurieren) aus.

5. Gehen Sie im Fenster **Edit SAML** (SAML bearbeiten) nach den folgenden Schritten vor: ![Benutzer verwalten & Authentifizierung](./media/zscaler-beta-tutorial/certificate.png "Benutzer & Authentifizierung verwalten")

   a. Fügen Sie im Feld **SAML Portal URL** (SAML-Portal-URL) die **Anmelde-URL** ein, die Sie aus dem Azure-Portal kopiert haben.

   b. Geben Sie im Feld **Login Name Attribute** (Anmeldenamensattribut) die Zeichenfolge **NameID** ein.

   c. Wählen Sie im Feld **Public SSL Certificate** (Öffentliches SSL-Zertifikat) die Option **Upload** (Hochladen) aus, um das Azure-SAML-Signaturzertifikat hochzuladen, das Sie aus dem Azure-Portal heruntergeladen haben.

   d. Schalten Sie **Enable SAML Auto-Provisioning** (Automatische SAML-Bereitstellung aktivieren) um.

   e. Geben Sie im Feld **User Display Name Attribute** (Benutzeranzeigenamens-Attribut) die Zeichenfolge **displayName** ein, wenn Sie die automatische SAML-Bereitstellung für displayName-Attribute aktivieren möchten.

   f. Geben Sie im Feld **Group Name Attribute** (Gruppennamensattribut) die Zeichenfolge **memberOf** ein, wenn Sie die automatische SAML-Bereitstellung für memberOf-Attribute aktivieren möchten.

   g. Geben Sie im Feld **Department Name Attribute** (Abteilungsnamensattribut) die Zeichenfolge **department** ein, wenn Sie die automatische SAML-Bereitstellung für Abteilungsattribute aktivieren möchten.

   h. Wählen Sie **Speichern** aus.

6. Führen Sie auf der Dialogseite **Configure User Authentication** (Benutzerauthentifizierung konfigurieren) die folgenden Schritte aus:

   ![Menü „Activation“ (Aktivierung) und Schaltfläche „Activate“ (Aktivieren)](./media/zscaler-beta-tutorial/status.png)

   a. Bewegen Sie den Mauszeiger unten links auf das Menü **Activation** (Aktivierung).

   b. Wählen Sie **Aktivieren** aus.

## <a name="configure-proxy-settings"></a>Konfigurieren von Proxyeinstellungen

Führen Sie zum Konfigurieren der Proxyeinstellungen in Internet Explorer die folgenden Schritte aus:

1. Starten Sie **Internet Explorer**.

2. Wählen Sie im Menü **Extras** die Option **Internetoptionen** aus, um das Dialogfeld **Internetoptionen** zu öffnen.

   ![Dialogfeld „Internetoptionen“](./media/zscaler-beta-tutorial/connection.png "Internetoptionen")

3. Wählen Sie die Registerkarte **Verbindungen** aus.

   ![Registerkarte „Verbindungen“](./media/zscaler-beta-tutorial/server.png "Verbindungen")

4. Wählen Sie **LAN-Einstellungen** aus, um das Dialogfeld **Einstellungen für lokales Netzwerk** zu öffnen.

5. Gehen Sie im Abschnitt **Proxyserver** wie folgt vor:

   ![Abschnitt „Proxyserver“](./media/zscaler-beta-tutorial/network.png "Proxyserver")

   a. Aktivieren Sie das Kontrollkästchen **Proxyserver für LAN verwenden**.

   b. Geben Sie in das Feld **Adresse** die Adresse **gateway.Zscaler Beta.net** ein.

   c. Geben Sie im Feld **Port** den Port **80** ein.

   d. Aktivieren Sie das Kontrollkästchen **Proxyserver für lokale Adressen umgehen**.

   e. Wählen Sie zum Schließen des Dialogfelds **Einstellungen für lokales Netzwerk** die Option **OK** aus.

6. Wählen Sie zum Schließen des Dialogfelds **Internetoptionen** die Option **OK** aus.

### <a name="create-zscaler-beta-test-user"></a>Erstellen eines Zscaler Beta-Testbenutzers

In diesem Abschnitt wird in Zscaler Beta der Benutzer Britta Simon erstellt. Zscaler Beta unterstützt die **Just-in-Time-Benutzerbereitstellung**, die standardmäßig aktiviert ist. In diesem Abschnitt müssen Sie keine Schritte ausführen. Ist ein Benutzer noch nicht in Zscaler Beta vorhanden, wird nach der Authentifizierung ein neuer Benutzer erstellt.

> [!Note]
> Wenn Sie einen Benutzer manuell erstellen müssen, wenden Sie sich an das [Supportteam von Zscaler Beta](https://www.zscaler.com/company/contact).

> [!NOTE]
> Außerdem unterstützt Zscaler Beta automatische Benutzerbereitstellung. Weitere Informationen zum Konfigurieren der automatischen Benutzerbereitstellung finden Sie [hier](./zscaler-beta-provisioning-tutorial.md).

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für Zscaler Beta weitergeleitet, wo Sie den Anmeldeflow initiieren können.

* Rufen Sie direkt die Zscaler Beta-Anmelde-URL auf, und initiieren Sie den Anmeldeflow.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie unter „Meine Apps“ auf die Kachel „Zscaler Beta“ klicken, werden Sie zur Anmelde-URL für Zscaler Beta umgeleitet. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Zscaler Beta können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-any-app)
