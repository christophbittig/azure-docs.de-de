---
title: 'Tutorial: Azure Active Directory-Integration mit Lucidchart | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Lucidchart konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/15/2021
ms.author: jeedes
ms.openlocfilehash: b10162c1a79f30eb20ef57827c533e9fc93ee284
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132307559"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-lucidchart"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Lucidchart

In diesem Tutorial erfahren Sie, wie Sie Lucidchart in Azure Active Directory (Azure AD) integrieren. Die Integration von Lucidchart in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf Lucidchart hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Lucidchart anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Lucidchart-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Lucidchart unterstützt **SP**-initiiertes einmaliges Anmelden.
* Lucidchart unterstützt die [**automatisierte** Benutzerbereitstellung und Bereitstellungsaufhebung](lucidchart-provisioning-tutorial.md) (empfohlen).
* Lucidchart unterstützt die **Just-In-Time**-Benutzerbereitstellung.

## <a name="add-lucidchart-from-the-gallery"></a>Hinzufügen von Lucidchart aus dem Katalog

Zum Konfigurieren der Integration von Lucidchart in Azure AD müssen Sie Lucidchart aus dem Katalog der Liste der verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Lucidchart** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Lucidchart** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.


## <a name="configure-and-test-azure-ad-sso-for-lucidchart"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Lucidchart

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Lucidchart mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Lucidchart eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Lucidchart die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    * **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    * **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Lucidchart](#configure-lucidchart-sso)**, um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    * **[Erstellen eines Lucidchart-Testbenutzers](#create-lucidchart-test-user)**, um in Lucidchart eine Entsprechung von B. Simon zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Lucidchart** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Geben Sie im Abschnitt **Grundlegende SAML-Konfiguration** die Werte für die folgenden Felder ein:

   Geben Sie im Textfeld **Anmelde-URL** die folgende URL ein: `https://chart2.office.lucidchart.com/saml/sso/azure`.

5. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um den Ihren Anforderungen entsprechenden **Verbundmetadaten-XML**-Code aus den verfügbaren Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

6. Kopieren Sie im Abschnitt **Lucidchart einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Lucidchart gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Lucidchart** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-lucidchart-sso"></a>Konfigurieren des einmaligen Anmeldens für Lucidchart

1. Melden Sie sich in einem anderen Webbrowserfenster bei der Lucidchart-Unternehmenswebsite als Administrator an.

2. Klicken Sie im Menü oben auf **Team**.

    ![Team](./media/lucidchart-tutorial/ic791190.png "Team")

3. Klicken Sie auf **Anwendungen \> SAML verwalten**.

    ![Manage SAML (SAML verwalten)](./media/lucidchart-tutorial/ic791191.png "SAML verwalten")

4. Führen Sie auf der Dialogseite **SAML Authentication Settings** die folgenden Schritte aus:

    a. Wählen Sie **Enable SAML Authentication** aus, und klicken Sie dann auf **Optional**.

    ![SAML-Authentifizierungseinstellungen](./media/lucidchart-tutorial/ic791192.png "SAML-Authentifizierungseinstellungen")

    b. Geben Sie im Textfeld **Domain** Ihre Domäne ein, und klicken Sie dann auf **Change Certificate**.

    ![Zertifikat ändern](./media/lucidchart-tutorial/ic791193.png "Ändern eines Zertifikats")

    c. Öffnen Sie die heruntergeladene Metadatendatei, kopieren Sie deren Inhalt, und fügen Sie ihn in das Textfeld **Upload Metadata** ein.

    ![Metadaten hochladen](./media/lucidchart-tutorial/ic791194.png "Hochladen von Metadaten")

    d. Wählen Sie **Automatisch neue Benutzer zum Team hinzufügen** aus, und klicken Sie auf **Änderungen speichern**.

    ![Save Changes](./media/lucidchart-tutorial/ic791195.png "Änderungen speichern")

### <a name="create-lucidchart-test-user"></a>Erstellen eines Lucidchart-Testbenutzers

Für das Konfigurieren der Benutzerbereitstellung in Lucidchart steht kein Aktionselement zur Verfügung.  Wenn sich ein zugewiesener Benutzer über den Zugriffsbereich bei Lucidchart anmelden möchte, überprüft Lucidchart, ob der Benutzer vorhanden ist.  

Ist noch kein Benutzerkonto verfügbar, wird es von Lucidchart automatisch erstellt.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für Lucidchart weitergeleitet, wo Sie den Anmeldeflow initiieren können. 

* Rufen Sie direkt die Lucidchart-Anmelde-URL auf, und initiieren Sie den Anmeldeflow.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie unter „Meine Apps“ auf die Kachel „Lucidchart“ klicken, sollten Sie automatisch bei der Lucidchart-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

 Nach dem Konfigurieren von Lucidchart können Sie Sitzungssteuerungen erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützen. Sitzungssteuerungen basieren auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-aad)
