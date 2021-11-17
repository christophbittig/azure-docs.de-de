---
title: 'Tutorial: Azure Active Directory-Integration mit ZScaler ZSCloud | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und ZScaler ZSCloud konfigurieren.
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
ms.openlocfilehash: a17068b0e0d4ff5193ac7e9982c8d47a4bd99908
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132348652"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-zscloud"></a>Tutorial: Azure Active Directory-Integration mit Zscaler ZSCloud

In diesem Tutorial erfahren Sie, wie Sie Zscaler ZSCloud in Azure Active Directory (Azure AD) integrieren. Die Integration von Zscaler ZSCloud in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf Zscaler ZSCloud hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Zscaler ZSCloud anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration in ZScaler ZSCloud konfigurieren zu können, ist Folgendes erforderlich:

* Ein Azure AD-Abonnement Sollten Sie nicht über eine Azure AD-Umgebung verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) verwenden.
* Zscaler ZSCloud-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Zscaler ZSCloud unterstützt **SP**-initiiertes einmaliges Anmelden.

* Zscaler ZSCloud unterstützt **Just-in-Time**-Benutzerbereitstellung.

* Zscaler ZSCloud unterstützt [automatisierte Benutzerbereitstellung](zscaler-zscloud-provisioning-tutorial.md).

## <a name="adding-zscaler-zscloud-from-the-gallery"></a>Hinzufügen von Zscaler ZSCloud über den Katalog

Zum Konfigurieren der Integration von Zscaler ZSCloud in Azure AD müssen Sie Zscaler ZSCloud über den Katalog zur Liste der verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Zscaler ZSCloud** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Zscaler ZSCloud** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-zscaler-zscloud"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Zscaler ZSCloud

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Zscaler ZSCloud mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Zscaler ZSCloud eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Zscaler ZSCloud die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
   1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
   1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Zscaler ZSCloud](#configure-zscaler-zscloud-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
   1. **[Erstellen eines Zscaler ZSCloud-Testbenutzers](#create-zscaler-zscloud-test-user)** , um ein Pendant von B. Simon in Zscaler ZSCloud zu erhalten, das mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Zscaler ZSCloud** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Geben Sie im Abschnitt **Grundlegende SAML-Konfiguration** die Werte für die folgenden Felder ein:

   Geben Sie im Textfeld **Anmelde-URL** die von Ihren Benutzern für die Anmeldung bei Ihrer Zscaler ZSCloud-Anwendung verwendete URL ein.

   > [!NOTE]
   > Sie müssen den Wert mit der richtigen Anmelde-URL aktualisieren. Den Wert erhalten Sie vom [Supportteam für den Zscaler ZSCloud-Client](https://help.zscaler.com/). Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

1. Ihre Zscaler ZSCloud-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Daher müssen Sie Ihrer Konfiguration der SAML-Tokenattribute benutzerdefinierte Attributzuordnungen hinzufügen. Der folgende Screenshot zeigt die Liste der Standardattribute. Klicken Sie auf das Symbol **Bearbeiten**, um das Dialogfeld **Benutzerattribute** zu öffnen.

   ![Screenshot: Benutzerattribute mit ausgewähltem Bearbeitungssymbol](common/edit-attribute.png)

1. Darüber hinaus wird von der Zscaler ZSCloud-Anwendung erwartet, dass in der SAML-Antwort noch einige weitere Attribute zurückgegeben werden. Führen Sie im Dialogfeld **Benutzerattribute** im Abschnitt **Benutzeransprüche** die folgenden Schritte aus, um das SAML-Tokenattribut wie in der folgenden Tabelle gezeigt hinzuzufügen:

   | Name     | Quellattribut   |
   | -------- | ------------------ |
   | memberOf | user.assignedroles |

   a. Klicken Sie auf **Neuen Anspruch hinzufügen**, um das Dialogfeld **Benutzeransprüche verwalten** zu öffnen.

   ![Screenshot: Benutzeransprüche mit Option zum Hinzufügen eines neuen Anspruchs](common/new-save-attribute.png)

   ![Screenshot des Dialogfelds „Benutzeransprüche verwalten“, in dem Sie die hier beschriebenen Werte eingeben können](common/new-attribute-details.png)

   b. Geben Sie im Textfeld **Name** den für die Zeile angezeigten Attributnamen ein.

   c. Lassen Sie den **Namespace** leer.

   d. Wählen Sie „Source“ als **Attribut** aus.

   e. Geben Sie in der Liste **Quellattribut** den für diese Zeile angezeigten Attributwert ein.

   f. Klicken Sie auf **Speichern**.

   > [!NOTE]
   > Klicken Sie [hier](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui), um herauszufinden, wie Sie die Rolle in Azure AD konfigurieren.

1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um das Ihrer Anforderung entsprechende **Zertifikat (Base64)** aus den angegebenen Optionen herunterzuladen und auf Ihrem Computer zu speichern.

   ![Downloadlink für das Zertifikat](common/certificatebase64.png)

1. Kopieren Sie im Abschnitt **Zscaler ZSCloud einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie Britta Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Zscaler ZSCloud gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** > **Zscaler ZSCloud** aus.
2. Wählen Sie in der Liste der Anwendungen **Zscaler ZSCloud** aus.
3. Wählen Sie im Menü auf der linken Seite **Benutzer und Gruppen** aus.
4. Klicken Sie auf die Schaltfläche **Benutzer hinzufügen**, und wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
5. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste die Benutzerin **Britta Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.

   ![Screenshot des Dialogfelds „Benutzer und Gruppen“ zum Auswählen eines Benutzers](./media/zscaler-zscloud-tutorial/users.png)

6. Wählen Sie im Dialogfeld **Rolle auswählen** die geeignete Rolle in der Liste aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.

   ![Screenshot des Dialogfelds „Rolle auswählen“ zum Auswählen einer Benutzerrolle](./media/zscaler-zscloud-tutorial/roles.png)

7. Wählen Sie im Dialogfeld **Zuweisung hinzufügen** die Schaltfläche **Zuweisen** aus.

   ![Screenshot des Dialogfelds „Zuweisung hinzufügen“ zum Auswählen der Schaltfläche „Zuweisen“](./media/zscaler-zscloud-tutorial/assignment.png)

   > [!NOTE]
   > Die Standardzugriffsrolle wird nicht unterstützt, da dadurch die Bereitstellung unterbrochen wird. Beim Zuweisen eines Benutzers kann daher nicht die Standardrolle ausgewählt werden.

## <a name="configure-zscaler-zscloud-sso"></a>Konfigurieren des einmaligen Anmeldens für Zscaler ZSCloud

1. Wenn Sie die Konfiguration in Zscaler ZSCloud automatisieren möchten, müssen Sie die **Browsererweiterung „Meine Apps“ für die sichere Anmeldung** installieren, indem Sie auf **Erweiterung installieren** klicken.

   ![Erweiterung „Meine Apps“](common/install-myappssecure-extension.png)

2. Klicken Sie nach dem Hinzufügen der Erweiterung zum Browser auf **Zscaler ZSCloud einrichten**, um zur Zscaler ZSCloud-Anwendung weitergeleitet zu werden. Geben Sie dort die Administratoranmeldeinformationen ein, um sich bei Zscaler ZSCloud anzumelden. Die Browsererweiterung konfiguriert die Anwendung automatisch für Sie und automatisiert die Schritte 3 bis 6.

   ![Einrichten des einmaligen Anmeldens](common/setup-sso.png)

3. Wenn Sie Zscaler ZSCloud manuell einrichten möchten, öffnen Sie ein neues Webbrowserfenster, melden Sie sich bei der Zscaler ZSCloud-Unternehmenswebsite als Administrator an, und führen Sie die folgenden Schritte aus:

4. Navigieren Sie zu **Verwaltung > Authentifizierung > Authentifizierungseinstellungen**, und führen Sie die folgenden Schritte aus:

   ![Screenshot der Zscaler-Website mit den beschriebenen Schritten](./media/zscaler-zscloud-tutorial/setting.png "Verwaltung")

   a. Wählen Sie **SAML** als Authentifizierungstyp aus.

   b. Klicken Sie auf **Configure SAML**.

5. Führen Sie im Fenster **Edit SAML** (SAML bearbeiten) die folgenden Schritte aus, und klicken Sie auf „Speichern“.  

   ![Benutzer & Authentifizierung verwalten](./media/zscaler-zscloud-tutorial/attributes.png "Benutzer & Authentifizierung verwalten")

   a. Fügen Sie in das Textfeld **SAML Portal URL** (SAML-Portal-URL) die **Anmelde-URL** ein, die Sie aus dem Azure-Portal kopiert haben.

   b. Geben Sie im Textfeld **Login Name Attribute** (Anmeldenamensattribut) die Zeichenfolge **NameID** ein.

   c. Klicken Sie auf **Hochladen**, um das Azure-SAML-Signaturzertifikat hochzuladen, das Sie aus dem Azure-Portal unter **Öffentliches SSL-Zertifikat** heruntergeladen haben.

   d. Betätigen Sie den Umschalter **Automatische SAML-Bereitstellung aktivieren**.

   e. Geben Sie im Textfeld **User Display Name Attribute** (Benutzeranzeigenamens-Attribut) die Zeichenfolge **displayName** ein, wenn Sie die automatische SAML-Bereitstellung für displayName-Attribute aktivieren möchten.

   f. Geben Sie im Textfeld **Group Name Attribute** (Gruppennamensattribut) die Zeichenfolge **memberOf** ein, wenn Sie die automatische SAML-Bereitstellung für memberOf-Attribute aktivieren möchten.

   g. Geben Sie unter **Department Name Attribute** (Abteilungsnamensattribut) die Zeichenfolge **department** ein, wenn Sie die automatische SAML-Bereitstellung für Abteilungsattribute aktivieren möchten.

   h. Klicken Sie auf **Speichern**.

6. Führen Sie auf der Dialogseite **Benutzerauthentifizierung konfigurieren** die folgenden Schritte aus:

   ![Screenshot des Dialogfelds „Benutzerauthentifizierung konfigurieren“ mit ausgewählter Option „Aktivieren“](./media/zscaler-zscloud-tutorial/active.png)

   a. Bewegen Sie den Mauszeiger unten links auf das Menü **Aktivierung**.

   b. Klicken Sie auf **Aktivieren**.

## <a name="configuring-proxy-settings"></a>Konfigurieren von Proxyeinstellungen

### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>So konfigurieren Sie die Proxyeinstellungen in Internet Explorer:

1. Starten Sie **Internet Explorer**.

2. Wählen Sie im Menü **Extras** die Option **Internetoptionen**, um das Dialogfeld **Internetoptionen** zu öffnen.

   ![Internetoptionen](./media/zscaler-zscloud-tutorial/network.png "Internetoptionen")

3. Klicken Sie auf die Registerkarte **Verbindungen** .

   ![Verbindungen](./media/zscaler-zscloud-tutorial/server.png "Verbindungen")

4. Klicken Sie zum Öffnen des Dialogfelds **LAN-Einstellungen** auf **LAN-Einstellungen**.

5. Führen Sie im Abschnitt "Proxyserver" die folgenden Schritte aus:

   ![Proxyserver](./media/zscaler-zscloud-tutorial/internet-options.png "Proxyserver")

   a. Wählen Sie **Proxyserver für LAN verwenden** aus.

   b. Geben Sie im Textfeld „Adresse“ die Adresse **gateway.Zscaler ZSCloud.net** ein.

   c. Geben Sie im Textfeld „Port“ **80** ein.

   d. Wählen Sie **Proxyserver für lokale Adressen umgehen**.

   e. Klicken Sie zum Schließen des Dialogfelds **Einstellungen für lokales Netzwerk** auf **OK**.

6. Klicken Sie zum Schließen des Dialogfelds **Internetoptionen** auf **OK**.

### <a name="create-zscaler-zscloud-test-user"></a>Erstellen eines Zscaler ZSCloud-Testbenutzers

In diesem Abschnitt wird in Zscaler ZSCloud ein Benutzer mit dem Namen Britta Simon erstellt. Zscaler ZSCloud unterstützt die Just-in-Time-Benutzerbereitstellung (standardmäßig aktiviert). Für Sie steht in diesem Abschnitt kein Aktionselement zur Verfügung. Ist ein Benutzer noch nicht in Zscaler ZSCloud vorhanden, wird nach der Authentifizierung ein neuer Benutzer erstellt.

> [!Note]
> Wenn Sie einen Benutzer manuell erstellen müssen, wenden Sie sich an das [Supportteam von Zscaler ZSCloud](https://help.zscaler.com/).

> [!NOTE]
> Außerdem unterstützt Zscaler ZSCloud automatische Benutzerbereitstellung. Weitere Informationen zum Konfigurieren der automatischen Benutzerbereitstellung finden Sie [hier](./zscaler-zscloud-provisioning-tutorial.md).

### <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für Zscaler ZSCloud weitergeleitet, wo Sie den Anmeldeflow initiieren können.

* Rufen Sie direkt die Zscaler ZSCloud-Anmelde-URL auf, und initiieren Sie den Anmeldeflow.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie unter „Meine Apps“ auf die Kachel „Zscaler ZSCloud“ klicken, werden Sie zur Anmelde-URL für Zscaler ZSCloud umgeleitet. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Zscaler ZSCloud können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-any-app)
