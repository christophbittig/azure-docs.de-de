---
title: 'Tutorial: Azure Active Directory-Integration mit New Relic by Account | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie einmaliges Anmelden zwischen Azure Active Directory und New Relic by Account konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/02/2021
ms.author: jeedes
ms.openlocfilehash: a14a99fe949f0902d9bd861975418ee0e7d04026
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132294540"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-new-relic-by-account"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit New Relic by Account

In diesem Tutorial erfahren Sie, wie Sie New Relic by Account in Azure Active Directory (Azure AD) integrieren. Die Integration von New Relic by Account in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf New Relic by Account hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei New Relic by Account anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Abonnement für New Relic by Account, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* New Relic by Account unterstützt **SP-initiiertes** einmaliges Anmelden.
* New Relic unterstützt die [**automatisierte Benutzerbereitstellung und Bereitstellungsaufhebung**](new-relic-by-organization-provisioning-tutorial.md) (empfohlen).


## <a name="add-new-relic-by-account-from-the-gallery"></a>Hinzufügen von New Relic by Account aus dem Katalog

Zum Konfigurieren der Integration von New Relic by Account in Azure AD müssen Sie New Relic by Account über den Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **New Relic by Account** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **New Relic by Account** aus, und fügen Sie die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-new-relic-by-account"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für New Relic by Account

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit New Relic by Account mithilfe eines Testbenutzers namens **B.Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in New Relic by Account eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit New Relic by Account die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    * **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    * **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für New Relic by Account](#configure-new-relic-by-account-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    * **[Erstellen eines Testbenutzers für New Relic by Account](#create-new-relic-by-account-test-user)** , um ein Pendant von B. Simon in New Relic by Account zu erhalten, das mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **New Relic by Account** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)
   
1. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    a. Geben Sie im Textfeld **Anmelde-URL** die URL im folgenden Format ein: 

    `https://rpm.newrelic.com:443/accounts/{acc_id}/sso/saml/finalize`: Ersetzen Sie `acc_id` unbedingt durch Ihre eigene Konto-ID für New Relic by Account.

    b. Geben Sie im Textfeld **Bezeichner (Entitäts-ID)** die folgende URL ein: `rpm.newrelic.com`.

1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um das Ihrer Anforderung entsprechende **Zertifikat (Base64)** aus den angegebenen Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

1. Kopieren Sie im Abschnitt **New Relic by Account einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf New Relic by Account gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **New Relic by Account** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-new-relic-by-account-sso"></a>Konfigurieren des einmaligen Anmeldens für New Relic by Account

1. Melden Sie sich in einem anderen Webbrowserfenster bei der **New Relic by Account**-Unternehmenswebsite als Administrator an.

2. Klicken Sie im oberen Menü auf **Kontoeinstellungen**.
   
    ![Screenshot: Homepage mit Auswahl von „Account settings“ (Kontoeinstellungen)](./media/new-relic-tutorial/settings.png "Kontoeinstellungen")

3. Klicken Sie auf die Registerkarte **Sicherheit und Authentifizierung** und anschließend auf die Registerkarte **Einmaliges Anmelden**.
   
    ![Einmaliges Anmelden](./media/new-relic-tutorial/single-sign-on-tab.png "Single Sign-On")

4. Führen Sie auf der Dialogfeldseite für SAML die folgenden Schritte aus:
   
    ![SAML](./media/new-relic-tutorial/save.png "SAML")
   
    a. Klicken Sie auf **Datei auswählen** , um Ihr heruntergeladenes Azure Active Directory-Zertifikat hochzuladen.

    b. Fügen Sie in das Textfeld **Remote Login URL** (Remoteanmelde-URL) den Wert der **Anmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.
   
    c. Fügen Sie in das Textfeld **Logout landing URL** (Abmeldestart-URL) den Wert der **Abmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    d. Klicken Sie auf **Änderungen speichern**.

### <a name="create-new-relic-by-account-test-user"></a>Erstellen eines New Relic by Account-Testbenutzers

1. Melden Sie sich bei der **New Relic by Account**-Unternehmenswebsite als Administrator an.

2. Klicken Sie im oberen Menü auf **Kontoeinstellungen**.
   
    ![Screenshot: Auswahl von „Account settings“ (Kontoeinstellungen) auf Homepage](./media/new-relic-tutorial/account.png "Kontoeinstellungen")

3. Klicken Sie links im Bereich **Konto** auf **Zusammenfassung** und anschließend auf **Benutzer hinzufügen**.
   
    ![Screenshot: Bereich „Zusammenfassung“, in dem Sie „Benutzer hinzufügen“ auswählen können](./media/new-relic-tutorial/add.png "Kontoeinstellungen")

4. Führen Sie im Dialogfeld **Aktive Benutzer** die folgenden Schritte aus:
   
    ![Aktive Benutzer](./media/new-relic-tutorial/user.png "Aktive Benutzer")
   
    a. Geben Sie im Textfeld **E-Mail** die E-Mail-Adresse eines gültigen Azure Active Directory-Benutzers ein, den Sie bereitstellen möchten.

    b. Wählen Sie für **Rolle** die Option **Benutzer** aus.

    c. Klicken Sie auf **Diesen Benutzer hinzufügen**.

> [!NOTE]
> Sie können Azure AD-Benutzerkonten auch mit anderen Tools zum Erstellen von New Relic by Account-Benutzerkonten oder mit den APIs von New Relic by Account bereitstellen.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für New Relic by Account weitergeleitet, wo Sie den Anmeldeflow initiieren können. 

* Rufen Sie direkt die New Relic by Account-Anmelde-URL auf, und initiieren Sie den Anmeldeflow.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie unter „Meine Apps“ auf die Kachel „New Relic by Account“ klicken, werden Sie zur Anmelde-URL für New Relic by Account umgeleitet. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von New Relic by Account können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-any-app)
