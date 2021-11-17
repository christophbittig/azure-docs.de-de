---
title: 'Tutorial: Azure Active Directory-Integration in Wandera RADAR Admin'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Wandera RADAR Admin konfigurieren.
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/10/2021
ms.author: jeedes
ms.openlocfilehash: 3a5b163f6feeb99e38a00a9351052b9468bbe3d0
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132320245"
---
# <a name="tutorial-integrate-wandera-radar-admin-with-azure-active-directory"></a>Tutorial: Integrieren von Wandera RADAR Admin in Azure Active Directory

In diesem Tutorial erfahren Sie, wie Sie Wandera RADAR Admin in Azure Active Directory (Azure AD) integrieren. Die Integration von Wandera RADAR Admin in Azure AD ermöglicht Ihnen Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf Wandera RADAR Admin hat.
* Ermöglichen Sie Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Wandera RADAR Admin anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Wandera RADAR Admin-Abonnement, für das das einmalige Anmelden (SSO) aktiviert ist.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Wandera RADAR Admin unterstützt **IDP-initiiertes** einmaliges Anmelden.

> [!NOTE]
> Der Bezeichner dieser Anwendung ist ein fester Zeichenfolgenwert, daher kann in einem Mandanten nur eine Instanz konfiguriert werden.

## <a name="add-wandera-radar-admin-from-the-gallery"></a>Hinzufügen von Wandera RADAR Admin aus dem Katalog

Zum Konfigurieren der Integration von Wandera RADAR Admin in Azure AD müssen Sie Wandera RADAR Admin aus dem Katalog der Liste der verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Wandera RADAR Admin** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Wandera RADAR Admin** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-wandera-radar-admin"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Wandera RADAR Admin

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Wandera RADAR Admin mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Wandera RADAR Admin eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Wandera RADAR Admin die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
   1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
   1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Wandera RADAR Admin](#configure-wandera-radar-admin-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
   1. **[Erstellen eines Wandera RADAR Admin-Testbenutzers](#create-wandera-radar-admin-test-user)** , um eine Entsprechung von B. Simon in Wandera RADAR Admin zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist.
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Wandera RADAR Admin** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** den folgenden Schritt aus:

    Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://radar.wandera.com/saml/acs/<TENANT_ID>`.

    > [!NOTE]
    > Dieser Wert entspricht nicht dem tatsächlichen Wert. Aktualisieren Sie den Wert mit der richtigen Antwort-URL. Wenden Sie sich an das [Clientsupportteam von Wandera RADAR Admin](https://www.wandera.com/about-wandera/contact/#supportsection), um diesen Wert zu erhalten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen. Ersetzen Sie \<tenant id\> in der oben genannten URL durch die Mandanten-ID. Diese wird im Wandera-Konto unter **Settings** > **Administration** > **Single Sign-On** (Einstellungen > Verwaltung > Einmaliges Anmelden) angezeigt.

1. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** zu **Verbundmetadaten-XML**, und wählen Sie **Herunterladen** aus, um das Zertifikat herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **SAML-Signaturzertifikat**, um die Einstellungen zu bearbeiten.

    ![Signaturoption](common/signing-option.png)

    1. Wählen Sie **Signaturoption** als **SAML-Antwort und -Assertion signieren** aus.

    1. Wählen Sie unter **Signaturalgorithmus** die Option **SHA-256** aus.

1. Kopieren Sie im Abschnitt **Wandera RADAR Admin einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Wandera RADAR Admin gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Wandera RADAR Admin** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-wandera-radar-admin-sso"></a>Konfigurieren von SSO für Wandera RADAR Admin

1. Wenn Sie die Konfiguration in Wandera RADAR Admin automatisieren möchten, müssen Sie die **Browsererweiterung „Meine Apps“ für die sichere Anmeldung** installieren, indem Sie auf **Erweiterung installieren** klicken.

    ![Erweiterung „Meine Apps“](common/install-myappssecure-extension.png)

2. Klicken Sie nach dem Hinzufügen der Erweiterung zum Browser auf **Wandera RADAR Admin einrichten**, um zur Anwendung Wandera RADAR Admin weitergeleitet zu werden. Geben Sie dort die Administratoranmeldeinformationen ein, um sich bei Wandera RADAR Admin anzumelden. Die Browsererweiterung konfiguriert die Anwendung automatisch für Sie und automatisiert die Schritte 3 und 4.

    ![Einrichtungskonfiguration](common/setup-sso.png)

3. Wenn Sie Wandera RADAR Admin manuell einrichten möchten, öffnen Sie ein neues Webbrowserfenster, melden Sie sich bei der Wandera RADAR Admin-Unternehmenswebsite als Administrator an, und führen Sie die folgenden Schritte aus:

4. Klicken Sie oben rechts auf der Seite auf **Settings** > **Administration** > **Single Sign-On** (Einstellungen > Verwaltung > Einmaliges Anmelden), und aktivieren Sie die Option **Enable SAML 2.0** (SAML 2.0 aktivieren), um die folgenden Schritte auszuführen:

    ![Konfiguration von Wandera RADAR Admin](./media/wandera-tutorial/configure.png)

    a. Klicken Sie auf **Or manually enter the required fields** (Oder erforderliche Felder manuell ausfüllen).

    b. Fügen Sie in das Textfeld **IdP entityID** (IdP-Entitäts-ID) den Wert für den **Azure AD-Bezeichner** ein, den Sie aus dem Azure-Portal kopiert haben.

    c. Öffnen Sie die Verbundmetadaten-XML im Editor, kopieren Sie den Inhalt, und fügen Sie ihn ins Textfeld **IdP Public X.509 Certificate** (Öffentliches X.509-Zertifikat des Identitätsanbieters) ein.

    d. Klicken Sie auf **Speichern**.

### <a name="create-wandera-radar-admin-test-user"></a>Erstellen eines Wandera RADAR Admin-Testbenutzers

In diesem Abschnitt erstellen Sie in Wandera RADAR Admin einen Benutzer namens B. Simon. Wenden Sie sich an das [Wandera RADAR Admin-Supportteam](https://www.wandera.com/about-wandera/contact/#supportsection), um die Benutzer auf der Wandera RADAR Admin-Plattform hinzuzufügen. Benutzer müssen erstellt und aktiviert werden, damit Sie einmaliges Anmelden verwenden können.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen:

* Klicken Sie im Azure-Portal auf „Diese Anwendung testen“. Dadurch sollten Sie automatisch bei der Wandera RADAR Admin-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie in „Meine Apps“ auf die Kachel „Wandera RADAR Admin“ klicken, sollten Sie automatisch bei der Wandera RADAR Admin-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Wandera RADAR Admin können Sie Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-aad)
