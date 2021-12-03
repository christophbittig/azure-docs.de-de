---
title: 'Tutorial: Azure Active Directory-Integration mit Jive | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Jive konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/02/2021
ms.author: jeedes
ms.openlocfilehash: 2522a625983117044ffd00b4b4e798d4c57ad7c1
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132295118"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-jive"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Jive

In diesem Tutorial erfahren Sie, wie Sie Jive in Azure Active Directory (Azure AD) integrieren. Die Integration von Jive in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf Jive hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Jive anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Jive-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Jive unterstützt **SP-initiiertes** einmaliges Anmelden.
* Jive unterstützt die [**automatisierte** Benutzerbereitstellung](jive-provisioning-tutorial.md).

## <a name="add-jive-from-the-gallery"></a>Hinzufügen von Jive über den Katalog

Um die Integration von Jive in Azure AD zu konfigurieren, müssen Sie Jive über den Katalog zu Ihrer Liste mit verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Jive** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Jive** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.


## <a name="configure-and-test-azure-ad-sso-for-jive"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Jive

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Jive mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Jive eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Jive die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Jive](#configure-jive-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines Jive-Testbenutzers](#create-jive-test-user)** , um in Jive ein Pendant von B. Simon zu erhalten, das mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Jive** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Geben Sie im Abschnitt **Grundlegende SAML-Konfiguration** die Werte für die folgenden Felder ein:

   a. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<instance name>.jivecustom.com`.

   b. Geben Sie im Textfeld **Bezeichner (Entitäts-ID)** eine URL im folgenden Format ein:
   ```http
   https://<instance name>.jiveon.com
   ```

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch die tatsächliche Anmelde-URL und den tatsächlichen Bezeichner. Wenden Sie sich an das [Supportteam für den Jive-Client](https://www.jivesoftware.com/services-support/), um diese Werte zu erhalten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

5. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um den Ihren Anforderungen entsprechenden **Verbundmetadaten-XML**-Code aus den verfügbaren Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

6. Kopieren Sie im Abschnitt **Jive einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Jive gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste die Option **Jive** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-jive-sso"></a>Konfigurieren des einmaligen Anmeldens für Jive

1. Um einmaliges Anmelden seitens **Jive** zu konfigurieren, melden Sie sich bei Ihrem Jive-Mandanten als Administrator an.

1. Klicken Sie im Menü am oberen Rand auf **SAML**.

    ![Screenshot: Registerkarte „SAML“, auf der „Enabled“ (Aktiviert) ausgewählt ist](./media/jive-tutorial/jive-2.png)

    a. Wählen auf der Registerkarte **Allgemein** die Option **Aktiviert** aus.

    b. Klicken Sie auf die Schaltfläche **SAVE ALL SAML SETTINGS** (ALLE SAML-EINSTELLUNGEN SPEICHERN).

1. Navigieren Sie zur Registerkarte **IDP METADATA** (IDP-METADATEN).

    ![Screenshot: Registerkarte „SAML“, auf der „IDP METADATA“ (IDP-METADATEN) ausgewählt ist](./media/jive-tutorial/jive-3.png)

    a. Kopieren Sie den Inhalt der heruntergeladenen XML-Metadatendatei, und fügen Sie ihn in das Textfeld **Identity Provider (IDP) Metadata** (IDP-Metadaten) ein.

    b. Klicken Sie auf die Schaltfläche **SAVE ALL SAML SETTINGS** (ALLE SAML-EINSTELLUNGEN SPEICHERN).

1. Navigieren Sie zur Registerkarte **USER ATTRIBUTE MAPPING**(BENUTZERATTRIBUTZUORDNUNG).

    ![Screenshot: Registerkarte „SAML“, auf der „USER ATTRIBUTE MAPPING“ (BENUTZERATTRIBUTZUORDNUNG) ausgewählt ist](./media/jive-tutorial/jive-4.png)

    a. Fügen Sie im Textfeld **E-Mail** den zuvor kopierten Attributnamen des Werts **mail** ein.

    b. Fügen Sie im Textfeld **Vorname** den zuvor kopierten Attributnamen des Werts **givenname** ein.

    c. Fügen Sie im Textfeld **Nachname** den zuvor kopierten Attributnamen des Werts **surname** ein.

### <a name="create-jive-test-user"></a>Erstellen eines Jive-Testbenutzers

In diesem Abschnitt wird in Jive eine Benutzerin namens Britta Simon erstellt. Jive unterstützt die automatische Benutzerbereitstellung, die standardmäßig aktiviert ist. Weitere Details zur Konfiguration der automatischen Benutzerbereitstellung finden Sie [hier](jive-provisioning-tutorial.md).

Wenn Sie Benutzer manuell erstellen möchten, kann Sie das [Jive-Supportteam](https://www.jivesoftware.com/services-support/) beim Hinzufügen der Benutzer zur Jive-Plattform unterstützen.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für Jive weitergeleitet, wo Sie den Anmeldeflow initiieren können. 

* Navigieren Sie direkt zur Anmelde-URL für Jive, und initiieren Sie den Anmeldeflow.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie in „Meine Apps“auf die Kachel „Jive“ klicken, werden Sie zur Anmelde-URL für Jive weitergeleitet. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Jive können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-any-app)
