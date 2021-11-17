---
title: 'Tutorial: Azure Active Directory-Integration mit Clarizen One | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie einmaliges Anmelden zwischen Azure Active Directory und Clarizen One konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/08/2021
ms.author: jeedes
ms.openlocfilehash: ff7953abac61f08f6d609de5722db2bd7f6bc5b6
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132321669"
---
# <a name="tutorial-azure-active-directory-integration-with-clarizen-one"></a>Tutorial: Azure Active Directory-Integration mit Clarizen One

In diesem Tutorial erfahren Sie, wie Sie Clarizen One in Azure Active Directory (Azure AD) integrieren. Die Integration von Clarizen One in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer auf Clarizen One zugreifen kann.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Clarizen One anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Clarizen One-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Clarizen One unterstützt **IDP**-initiiertes einmaliges Anmelden.
* Clarizen One unterstützt die [**automatisierte** Benutzerbereitstellung und Bereitstellungsaufhebung](clarizen-one-provisioning-tutorial.md) (empfohlen).

> [!NOTE]
> Der Bezeichner dieser Anwendung ist ein fester Zeichenfolgenwert, daher kann in einem Mandanten nur eine Instanz konfiguriert werden.

## <a name="add-clarizen-one-from-the-gallery"></a>Hinzufügen von Clarizen One aus dem Katalog

Zum Konfigurieren der Integration von Clarizen One in Azure AD müssen Sie Clarizen One über den Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Clarizen One** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Clarizen One** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-clarizen-one"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Clarizen One

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Clarizen One mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Clarizen One eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Clarizen One die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Clarizen One](#configure-clarizen-one-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines Clarizen One-Testbenutzers](#create-clarizen-one-test-user)** , um eine Entsprechung von B. Simon in Clarizen One zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Clarizen One** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** die folgenden Schritte aus:

    a. Geben Sie im Textfeld **Bezeichner** den folgenden Wert ein: `Clarizen`.

    b. Geben Sie im Textfeld **Antwort-URL** die folgende URL ein: `https://.clarizen.com/Clarizen/Pages/Integrations/SAML/SamlResponse.aspx`.

4. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um das Ihrer Anforderung entsprechende **Zertifikat (Base64)** aus den angegebenen Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

6. Kopieren Sie im Abschnitt **Clarizen One einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Clarizen One gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Clarizen One** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-clarizen-one-sso"></a>Konfigurieren des einmaligen Anmeldens für Clarizen One

1. Melden Sie sich in einem anderen Webbrowserfenster bei der Clarizen One-Unternehmenswebsite als Administrator an.

1. Klicken Sie auf Ihren Benutzernamen und dann auf **Settings**.

    ![Klicken auf „Settings“ (Einstellungen) unter Ihrem Benutzernamen](./media/clarizen-tutorial/setting.png "Einstellungen")

1. Klicken Sie auf die Registerkarte **Global Settings**. Klicken Sie anschließend neben **Federated Authentication** auf **edit**.

    ![Registerkarte „Global Settings“ (Globale Einstellungen)](./media/clarizen-tutorial/authentication.png "Globale Einstellungen")

1. Führen Sie im Dialogfeld **Federated Authentication** die folgenden Schritte aus:

    ![Dialogfeld „Federated Authentication“ (Verbundauthentifizierung)](./media/clarizen-tutorial/federated-authentication.png "Federated Authentication")

    a. Wählen Sie **Enable Federated Authentication** (Verbundauthentifizierung aktivieren).

    b. Klicken Sie auf **Upload**, um das heruntergeladene Zertifikat hochzuladen.

    c. Geben Sie im Feld **Sign-in URL** (Anmelde-URL) den Wert für die **Anmelde-URL** aus dem Konfigurationsfenster der Azure AD-Anwendung ein.

    d. Geben Sie im Feld **Sign-out URL** (Abmelde-URL) den Wert für die **Anmelde-URL** aus dem Konfigurationsfenster der Azure AD-Anwendung ein.

    e. Wählen Sie **Use POST**.

    f. Klicken Sie auf **Speichern**.

### <a name="create-clarizen-one-test-user"></a>Erstellen eines Clarizen One-Testbenutzers

In diesem Abschnitt wird in Clarizen One ein Benutzer namens Britta Simon erstellt.

**Wenn Sie einen Benutzer manuell erstellen möchten, führen Sie die folgenden Schritte aus:**

Damit sich Azure AD-Benutzer bei Clarizen One anmelden können, müssen Sie Benutzerkonten bereitstellen. Im Fall von Clarizen One ist die Bereitstellung eine manuelle Aufgabe.

1. Melden Sie sich bei der Clarizen One-Unternehmenswebsite als Administrator an.

2. Klicken Sie auf **People**.

    ![Klicken auf „People“ (Personen)](./media/clarizen-tutorial/people.png "Personen")

3. Klicken Sie auf **Invite User**.

    ![Schaltfläche „Invite User“ (Benutzer einladen)](./media/clarizen-tutorial/user.png "Invite Users")

1. Führen Sie auf der Dialogfeldseite **Invite People** die folgenden Schritte aus:

    ![Dialogfeld „Invite People“ (Personen einladen)](./media/clarizen-tutorial/invite-people.png "Invite People")

    a. Geben Sie im Feld **E-Mail** die E-Mail-Adresse des Kontos von Britta Simon ein.

    b. Klicken Sie auf **Einladen**.

    > [!NOTE]
    > Der Besitzer des Azure Active Directory-Kontos erhält eine E-Mail und folgt einem Link zur Bestätigung des Kontos, bevor es aktiv wird.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen:

* Klicken Sie im Azure-Portal auf „Diese Anwendung testen“. Dadurch sollten Sie automatisch bei der Clarizen One-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie in „Meine Apps“ auf die Kachel „Clarizen One“ klicken, sollten Sie automatisch bei der Clarizen One-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Clarizen One können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-any-app)
