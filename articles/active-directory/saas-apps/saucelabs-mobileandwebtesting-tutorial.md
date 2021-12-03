---
title: 'Tutorial: Azure Active Directory-Integration in „Sauce Labs - Mobile and Web Testing“ | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und „Sauce Labs - Mobile and Web Testing“ konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/20/2021
ms.author: jeedes
ms.openlocfilehash: 46037ff025b11af5e7acbd2ecac1d3eac485b63c
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132341348"
---
# <a name="tutorial-azure-active-directory-integration-with-sauce-labs---mobile-and-web-testing"></a>Tutorial: Azure Active Directory-Integration in „Sauce Labs - Mobile and Web Testing“

In diesem Tutorial erfahren Sie, wie Sie Sauce Labs - Mobile and Web Testing in Azure Active Directory (Azure AD) integrieren. Die Integration von Sauce Labs - Mobile and Web Testing in Azure AD ermöglicht Folgendes:

* Sie können in Azure AD steuern, wer Zugriff auf Sauce Labs - Mobile and Web Testing hat.
* Sie können Ihren Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei Sauce Labs - Mobile and Web Testing anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration in „Sauce Labs - Mobile and Web Testing“ zu konfigurieren, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Sollten Sie nicht über eine Azure AD-Umgebung verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Sauce Labs-Abonnement, für das einmaliges Anmelden aktiviert ist.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Sauce Labs - Mobile and Web Testing unterstützt **IDP-initiiertes** einmaliges Anmelden.
* Sauce Labs - Mobile and Web Testing unterstützt die **Just-In-Time**-Benutzerbereitstellung.

## <a name="add-sauce-labs---mobile-and-web-testing-from-the-gallery"></a>Hinzufügen von Sauce Labs - Mobile and Web Testing aus dem Katalog

Zum Konfigurieren der Integration von „Sauce Labs - Mobile and Web Testing“ in Azure AD müssen Sie „Sauce Labs - Mobile and Web Testing“ aus dem Katalog der Liste der verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** im Suchfeld den Suchbegriff **Sauce Labs - Mobile and Web Testing** ein.
1. Wählen Sie im Ergebnisbereich den Eintrag **Sauce Labs - Mobile and Web Testing** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-sauce-labs---mobile-and-web-testing"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Sauce Labs - Mobile and Web Testing

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD bei Sauce Labs - Mobile and Web Testing mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Sauce Labs - Mobile and Web Testing eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD bei Sauce Labs - Mobile and Web Testing die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Sauce Labs - Mobile and Web Testing](#configure-sauce-labs---mobile-and-web-testing-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
    1. **[Erstellen eines Sauce Labs-Testbenutzers](#create-sauce-labs---mobile-and-web-testing-test-user)** , um in Sauce Labs - Mobile and Web Testing eine Entsprechung von B. Simon zu erhalten, die mit der Benutzerdarstellung in Azure AD verknüpft ist.
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Sauce Labs - Mobile and Web Testing** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Im Abschnitt **Grundlegende SAML-Konfiguration** muss der Benutzer keine Schritte ausführen, weil die App bereits in Azure integriert ist.

5. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um den Ihren Anforderungen entsprechenden **Verbundmetadaten-XML**-Code aus den verfügbaren Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

6. Kopieren Sie im Abschnitt **Sauce Labs - Mobile and Web Testing einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Sauce Labs - Mobile and Web Testing gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Sauce Labs - Mobile and Web Testing** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-sauce-labs---mobile-and-web-testing-sso"></a>Konfigurieren des einmaligen Anmeldens für Sauce Labs - Mobile and Web Testing

1. Melden Sie sich in einem anderen Webbrowserfenster bei der Sauce Labs-Unternehmenswebsite als Administrator an.

2. Klicken Sie auf das **Benutzersymbol**, und wählen Sie die Registerkarte **Team Management** (Teamverwaltung) aus.

    ![Screenshot, auf dem das Benutzersymbol und das Dropdownmenü „Team Management“ (Teamverwaltung) ausgewählt sind](./media/saucelabs-mobileandwebtesting-tutorial/user.png)

3. Geben Sie in das Textfeld Ihren **Domänennamen** ein.

    ![Screenshot: Beispieldomänenname im Textfeld](./media/saucelabs-mobileandwebtesting-tutorial/domain.png)

4. Klicken Sie auf die Registerkarte **Configure** (Konfigurieren).

    ![Screenshot: Registerkarte „Configure“ (Konfigurieren), die unter „Single Sign On is Enabled“ (Einmaliges Anmelden ist aktiviert.) ausgewählt ist](./media/saucelabs-mobileandwebtesting-tutorial/configure.png)

5. Führen Sie im Abschnitt **Configure Single Sign On** (Einmaliges Anmelden konfigurieren) die folgenden Schritte aus:

    ![Einmaliges Anmelden konfigurieren](./media/saucelabs-mobileandwebtesting-tutorial/browse.png)

    a. Klicken Sie auf **Browse** (Durchsuchen), und laden Sie die aus Azure AD heruntergeladene Metadatendatei hoch.

    b. Aktivieren Sie das Kontrollkästchen **ALLOW JUST-IN-TIME PROVISIONING** (Just-in-Time-Bereitstellung zulassen).

    c. Klicken Sie auf **Speichern**.

### <a name="create-sauce-labs---mobile-and-web-testing-test-user"></a>Erstellen eines Sauce Labs-Testbenutzers

In diesem Abschnitt wird ein Benutzer mit dem Namen Britta Simon in „Sauce Labs - Mobile and Web Testing“ erstellt. „Sauce Labs - Mobile and Web Testing“ unterstützt die Just-In-Time-Benutzerbereitstellung, die standardmäßig aktiviert ist. Für Sie steht in diesem Abschnitt kein Aktionselement zur Verfügung. Ist ein Benutzer noch nicht in „Sauce Labs - Mobile and Web Testing“ vorhanden, wird nach der Authentifizierung ein neuer Benutzer erstellt.

> [!Note]
> Wenn Sie einen Benutzer manuell erstellen möchten, wenden Sie sich an das [Sauce Labs-Supportteam](mailto:support@saucelabs.com).

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen:

* Klicken Sie im Azure-Portal auf „Diese Anwendung testen“. Dadurch sollten Sie automatisch bei der Sauce Labs-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie in „Meine Apps“ auf die Kachel „Sauce Labs - Mobile and Web Testing“ klicken, sollten Sie automatisch bei der Sauce Labs-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Sauce Labs - Mobile and Web Testing können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-aad)
