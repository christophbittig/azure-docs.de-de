---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Adobe Identity Management | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Adobe Identity Management konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/15/2021
ms.author: jeedes
ms.openlocfilehash: d0155a54238f42636aeee9121b586852b26196f8
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132280975"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-adobe-identity-management"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Adobe Identity Management

In diesem Tutorial erfahren Sie, wie Sie Adobe Identity Management in Azure Active Directory (Azure AD) integrieren. Die Integration von Adobe Identity Management in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf Adobe Identity Management hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Adobe Identity Management anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Adobe Identity Management-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Adobe Identity Management unterstützt **SP-initiiertes** einmaliges Anmelden.
* Adobe Identity Management unterstützt die [**automatisierte** Benutzerbereitstellung und Bereitstellungsaufhebung](adobe-identity-management-provisioning-tutorial.md) (empfohlen).

## <a name="adding-adobe-identity-management-from-the-gallery"></a>Hinzufügen von Adobe Identity Management aus dem Katalog

Um die Integration von Adobe Identity Management in Azure AD zu konfigurieren, müssen Sie Adobe Identity Management aus dem Katalog Ihrer Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Adobe Identity Management** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Adobe Identity Management** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-adobe-identity-management"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Adobe Identity Management

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Adobe Identity Management mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Adobe Identity Management eingerichtet werden.

Führen Sie die folgenden Schritte aus, um das einmalige Anmelden von Azure AD mit Adobe Identity Management zu konfigurieren und zu testen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Adobe Identity Management](#configure-adobe-identity-management-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines Adobe Identity Management-Testbenutzers](#create-adobe-identity-management-test-user)** , um in Adobe Identity Management eine Entsprechung von B. Simon zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Adobe Identity Management** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Geben Sie im Abschnitt **Grundlegende SAML-Konfiguration** die Werte für die folgenden Felder ein:

    a. Geben Sie im Textfeld **Anmelde-URL** die URL ein: `https://adobe.com`.

    b. Geben Sie im Textfeld **Bezeichner (Entitäts-ID)** eine URL im folgenden Format ein: `https://federatedid-na1.services.adobe.com/federated/saml/metadata/alias/<CUSTOM_ID>`.

    > [!NOTE]
    > Der ID-Wert ist nicht der tatsächliche Wert. Aktualisieren Sie den Wert mit dem tatsächlichen Bezeichner. Den Wert erhalten Sie vom [Supportteam für den Adobe Identity Management-Client](mailto:identity@adobe.com). Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

1. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** zu **Verbundmetadaten-XML**, und wählen Sie **Herunterladen** aus, um das Zertifikat herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

1. Kopieren Sie im Abschnitt **Adobe Identity Management einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Adobe Identity Management gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Adobe Identity Management** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-adobe-identity-management-sso"></a>Konfigurieren des einmaligen Anmeldens für Adobe Identity Management

1. Wenn Sie die Konfiguration in Adobe Identity Management automatisieren möchten, müssen Sie die **Browsererweiterung „Meine Apps“ für die sichere Anmeldung** installieren, indem Sie auf **Erweiterung installieren** klicken.

    ![Erweiterung „Meine Apps“](common/install-myappssecure-extension.png)

2. Klicken Sie nach dem Hinzufügen der Erweiterung zum Browser auf **Adobe Identity Management einrichten**, um zur Adobe Identity Management-Anwendung weitergeleitet zu werden. Geben Sie dort die Administratoranmeldeinformationen ein, um sich bei Adobe Identity Management anzumelden. Die Browsererweiterung konfiguriert die Anwendung automatisch für Sie und automatisiert die Schritte 3 bis 8.

    ![Einrichtungskonfiguration](common/setup-sso.png)

3. Wenn Sie Adobe Identity Management manuell einrichten möchten, melden Sie sich in einem anderen Webbrowserfenster als Administrator bei der Adobe Identity Management-Unternehmenswebsite an.

4. Navigieren Sie zur Registerkarte **Settings** (Einstellungen), und klicken Sie auf **Create Directory** (Verzeichnis erstellen).

    ![Adobe Identity Management-Einstellungen](./media/adobe-identity-management-tutorial/settings.png)

5. Geben Sie den Verzeichnisnamen in das Textfeld ein, wählen Sie **Federated ID** (Verbund-ID) aus, und klicken Sie auf **Next** (Weiter).

    ![Adobe Identity Management: Erstellen eines Verzeichnisses](./media/adobe-identity-management-tutorial/create-directory.png)

6. Wählen Sie **Other SAML Providers** (Andere SAML-Anbieter) aus, und klicken Sie auf **Next** (Weiter).
 
    ![Adobe Identity Management: SAML-Anbieter](./media/adobe-identity-management-tutorial/saml-providers.png)

7. Klicken Sie auf den Link zum **Auswählen**, um die **Metadaten-XML-Datei** hochzuladen, die Sie aus dem Azure-Portal heruntergeladen haben.

    ![Adobe Identity Management: SAML-Konfiguration](./media/adobe-identity-management-tutorial/saml-configuration.png)

8. Klicken Sie auf **Done** (Fertig).

### <a name="create-adobe-identity-management-test-user"></a>Erstellen eines Adobe Identity Management-Testbenutzers

1. Navigieren Sie zur Registerkarte **Users** (Benutzer), und klicken Sie auf **Add User** (Benutzer hinzufügen).

    ![Adobe Identity Management: Hinzufügen von Benutzern](./media/adobe-identity-management-tutorial/add-user.png)

2. Geben Sie im Textfeld **Enter user’s email address** (E-Mail-Adresse des Benutzers eingeben) die **E-Mail-Adresse** ein.

    ![Adobe Identity Management: Speichern des Benutzers](./media/adobe-identity-management-tutorial/save-user.png)

3. Klicken Sie auf **Speichern**.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für Adobe Identity Management weitergeleitet, wo Sie den Anmeldeflow initiieren können.

* Rufen Sie direkt die Adobe Identity Management-Anmelde-URL auf, und initiieren Sie den Anmeldeflow.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie unter „Meine Apps“ auf die Kachel „Adobe Identity Management“ klicken, werden Sie zur Anmelde-URL für Adobe Identity Management weitergeleitet. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Adobe Identity Management können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-any-app)
