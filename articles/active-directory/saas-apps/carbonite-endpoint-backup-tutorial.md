---
title: 'Tutorial: Azure Active Directory-Integration mit Carbonite Endpoint Backup | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Carbonite Endpoint Backup konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/03/2021
ms.author: jeedes
ms.openlocfilehash: c5da66a5bfd91dd352dbd225bab40a65c5ae1a72
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132292290"
---
# <a name="tutorial-integrate-carbonite-endpoint-backup-with-azure-active-directory"></a>Tutorial: Integrieren von Carbonite Endpoint Backup in Azure Active Directory

In diesem Tutorial erfahren Sie, wie Sie Carbonite Endpoint Backup in Azure Active Directory (Azure AD) integrieren. Die Integration von Carbonite Endpoint Backup in Azure AD bietet die folgenden Vorteile:

* Steuern Sie in Azure AD, wer Zugriff auf Carbonite Endpoint Backup hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Carbonite Endpoint Backup anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Carbonite Endpoint Backup-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Carbonite Endpoint Backup unterstützt **SP- und IDP-initiiertes** einmaliges Anmelden.

> [!NOTE]
> Der Bezeichner dieser Anwendung ist ein fester Zeichenfolgenwert, daher kann in einem Mandanten nur eine Instanz konfiguriert werden.

## <a name="add-carbonite-endpoint-backup-from-the-gallery"></a>Hinzufügen von Carbonite Endpoint Backup aus dem Katalog

Zum Konfigurieren der Integration von Carbonite Endpoint Backup in Azure AD müssen Sie Carbonite Endpoint Backup aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Carbonite Endpoint Backup** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Carbonite Endpoint Backup** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-carbonite-endpoint-backup"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Carbonite Endpoint Backup

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Carbonite Endpoint Backup mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Carbonite Endpoint Backup eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Carbonite Endpoint Backup die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Carbonite Endpoint Backup](#configure-carbonite-endpoint-backup-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines Carbonite Endpoint Backup-Testbenutzers](#create-carbonite-endpoint-backup-test-user)** , um eine Entsprechung für B. Simon in Carbonite Endpoint Backup zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Carbonite Endpoint Backup** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Geben Sie im Abschnitt **Grundlegende SAML-Konfiguration** die Werte in die folgenden Felder ein, wenn Sie die Anwendung im **IDP**-initiierten Modus konfigurieren möchten:

    a. Geben Sie im Textfeld **Bezeichner** eine der folgenden URLs ein:

    ```http
    https://red-us.mysecuredatavault.com
    https://red-apac.mysecuredatavault.com
    https://red-fr.mysecuredatavault.com
    https://red-emea.mysecuredatavault.com
    https://kamino.mysecuredatavault.com
    ```

    b. Geben Sie im Textfeld **Antwort-URL** eine der folgenden URLs ein:

    ```http
    https://red-us.mysecuredatavault.com/AssertionConsumerService.aspx
    https://red-apac.mysecuredatavault.com/AssertionConsumerService.aspx
    https://red-fr.mysecuredatavault.com/AssertionConsumerService.aspx
    https://red-emea.mysecuredatavault.com/AssertionConsumerService.aspx
    ```

1. Klicken Sie auf **Zusätzliche URLs festlegen**, und führen Sie den folgenden Schritt aus, wenn Sie die Anwendung im **SP-initiierten Modus** konfigurieren möchten:

    Geben Sie im Textfeld **Anmelde-URL** eine der folgenden URLs ein:

    ```http
    https://red-us.mysecuredatavault.com/
    https://red-apac.mysecuredatavault.com/
    https://red-fr.mysecuredatavault.com/
    https://red-emea.mysecuredatavault.com/
    ```

1. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** zum Eintrag **Zertifikat (Base64)** . Wählen Sie **Herunterladen** aus, um das Zertifikat herunterzuladen, und speichern Sie es auf Ihrem Computer.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

1. Kopieren Sie im Abschnitt **Carbonite Endpoint Backup einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Carbonite Endpoint Backup gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Carbonite Endpoint Backup** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-carbonite-endpoint-backup-sso"></a>Konfigurieren des einmaligen Anmeldens für Carbonite Endpoint Backup

1. Wenn Sie die Konfiguration in Carbonite Endpoint Backup automatisieren möchten, müssen Sie die **Browsererweiterung „Meine Apps“ für die sichere Anmeldung** installieren, indem Sie auf **Erweiterung installieren** klicken.

    ![Erweiterung „Meine Apps“](common/install-myappssecure-extension.png)

2. Wenn Sie nach dem Hinzufügen der Erweiterung zum Browser auf **Carbonite Endpoint Backup einrichten** klicken, werden Sie zur Carbonite Endpoint Backup-Anwendung umgeleitet. Geben Sie dort die Administratoranmeldeinformationen ein, um sich bei Carbonite Endpoint Backup anzumelden. Die Browsererweiterung konfiguriert die Anwendung automatisch für Sie und automatisiert die Schritte 3 bis 7.

    ![Einrichtungskonfiguration](common/setup-sso.png)

3. Wenn Sie Carbonite Endpoint Backup manuell einrichten möchten, melden Sie sich in einem neuen Webbrowserfenster bei der Carbonite Endpoint Backup-Unternehmenswebsite als Administrator an, und führen Sie die folgenden Schritte aus:

4. Klicken Sie im linken Bereich auf **Company** (Unternehmen).

    ![Screenshot von Carbonite Endpoint, in dem „Company“ ausgewählt ist](media/carbonite-endpoint-backup-tutorial/company.png)

5. Klicken Sie auf **Single sign-on** (Einmaliges Anmelden).

    ![Screenshot von „Company“ (Unternehmen), wobei „Single sign-on“ (Einmaliges Anmelden) ausgewählt ist](media/carbonite-endpoint-backup-tutorial/single-sign-on.png)

6. Klicken Sie zum Konfigurieren auf **Enable** (Aktivieren) und dann auf **Edit settings** (Einstellungen bearbeiten).

    ![Screenshot der Registerkarte „Einmaliges Anmelden“, auf der „Aktivieren“ und „Einstellungen bearbeiten“ hervorgehoben sind](media/carbonite-endpoint-backup-tutorial/settings.png)

7. Führen Sie auf der Einstellungsseite **Single sign-on** (Einmaliges Anmelden) die folgenden Schritte aus:

    ![Screenshot der Registerkarte „Einmaliges Anmelden“ mit den in diesem Schritt beschriebenen Informationen](media/carbonite-endpoint-backup-tutorial/save.png)

    1. Fügen Sie in das Textfeld **Identity provider name** (Name des Identitätsanbieters) den Wert von **Azure AD-Bezeichner** ein, den Sie aus dem Azure-Portal kopiert haben.

    1. Fügen Sie in das Textfeld **Identity Provider URL** (Identitätsanbieter-URL) den Wert der **Anmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    1. Klicken Sie auf **Choose File** (Datei auswählen), um das aus dem Azure-Portal heruntergeladene **Zertifikat (Base64)** hochzuladen.

    1. Klicken Sie auf **Speichern**.

### <a name="create-carbonite-endpoint-backup-test-user"></a>Erstellen eines Carbonite Endpoint Backup-Testbenutzers

1. Melden Sie sich in einem anderen Webbrowserfenster bei der Carbonite Endpoint Backup-Unternehmenswebsite als Administrator an.

1. Klicken Sie im linken Bereich auf **Users** (Benutzer) und anschließend auf **Add user** (Benutzer hinzufügen).

    ![Screenshot der Seite „Carbonite Endpoint“, auf der „Benutzer“ und „Benutzer hinzufügen“ ausgewählt sind](media/carbonite-endpoint-backup-tutorial/add-user-1.png)

1. Führen Sie auf der Seite **Add user** (Benutzer hinzufügen) die folgenden Schritte aus:

    ![Screenshot der Seite „Benutzer hinzufügen“, auf der Sie die hier beschriebenen Schritte ausführen können](media/carbonite-endpoint-backup-tutorial/add-user-2.png)

    1. Geben Sie die E-Mail-Adresse (**Email**), den Vornamen (**First name**) und den Nachnamen (**Last name**) des Benutzers ein, und weisen Sie ihm gemäß den Anforderungen der Organisation die erforderlichen Berechtigungen zu.

    1. Klicken Sie auf **Benutzer hinzufügen**.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

#### <a name="sp-initiated"></a>SP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für Carbonite Endpoint Backup weitergeleitet, wo Sie den Anmeldeflow initiieren können.  

* Rufen Sie direkt die Carbonite Endpoint Backup-Anmelde-URL auf, und initiieren Sie den Anmeldeflow.

#### <a name="idp-initiated"></a>IDP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch sollten Sie automatisch bei der Carbonite Endpoint Backup-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. 

Sie können auch den Microsoft-Bereich „Meine Apps“ verwenden, um die Anwendung in einem beliebigen Modus zu testen. Beim Klicken auf die Kachel „Carbonite Endpoint Backup“ in „Meine Apps“ geschieht Folgendes: Wenn Sie den SP-Modus konfiguriert haben, werden Sie zum Initiieren des Anmeldeflows zur Anmeldeseite der Anwendung weitergeleitet. Wenn Sie den IDP-Modus konfiguriert haben, sollten Sie automatisch bei der Carbonite Endpoint Backup-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Carbonite Endpoint Backup können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-any-app)
