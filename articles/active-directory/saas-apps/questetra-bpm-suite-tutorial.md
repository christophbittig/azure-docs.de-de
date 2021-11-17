---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD mit Questetra BPM Suite'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und der Questetra BPM Suite konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/06/2021
ms.author: jeedes
ms.openlocfilehash: 07f24ff22044bd1bd908e78fa26a3e21cf76be8d
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132309075"
---
# <a name="tutorial-azure-ad-sso-integration-with-questetra-bpm-suite"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD mit Questetra BPM Suite

In diesem Tutorial erfahren Sie, wie Sie Questetra BPM Suite in Azure Active Directory (Azure AD) integrieren. Die Integration von Questetra BPM Suite in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf Questetra BPM Suite hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Questetra BPM Suite anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Questetra BPM Suite-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Questetra BPM Suite unterstützt **SP**-initiiertes einmaliges Anmelden.

## <a name="add-questetra-bpm-suite-from-the-gallery"></a>Hinzufügen der Questetra BPM Suite aus dem Katalog

Zum Konfigurieren der Integration der Questetra BPM Suite in Azure AD müssen Sie die Questetra BPM Suite der Liste mit den verwalteten SaaS-Apps aus dem Katalog hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Questetra BPM Suite** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Questetra BPM Suite** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-questetra-bpm-suite"></a>Konfigurieren und Testen des Azure AD-SSO für Questetra BPM Suite

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Questetra BPM Suite mithilfe eines Testbenutzers mit dem Namen **B.Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Questetra BPM Suite eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Questetra BPM Suite die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Questetra BPM Suite](#configure-questetra-bpm-suite-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines Questetra BPM Suite-Testbenutzers](#create-questetra-bpm-suite-test-user)** , um eine Entsprechung von B. Simon in Questetra BPM Suite zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Questetra BPM Suite** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    a. Geben Sie im Textfeld **Bezeichner (Entitäts-ID)** eine URL im folgenden Format ein: `https://<subdomain>.questetra.net/`.

    b. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<subdomain>.questetra.net/saml/SSO/alias/bpm`.

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch den tatsächlichen Bezeichner und die tatsächliche Anmelde-URL. Sie erhalten diese Werte aus dem Abschnitt **SP Information** (SP-Informationen) auf der **Questetra BPM Suite**-Unternehmenswebsite, wie weiter unten im Tutorial erläutert, oder wenden Sie sich an das [Supportteam der Questetra BPM Suite](https://www.questetra.com/contact/). Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

5. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um das Ihrer Anforderung entsprechende **Zertifikat (Base64)** aus den angegebenen Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

6. Kopieren Sie im Abschnitt **Questetra BPM Suite einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Questetra BPM Suite gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Questetra BPM Suite** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-questetra-bpm-suite-sso"></a>Konfigurieren des einmaligen Anmeldens für Questetra BPM Suite

1. Melden Sie sich in einem anderen Webbrowserfenster bei der Unternehmenswebsite von **Questetra BPM Suite** als Administrator an.

2. Klicken Sie im oberen Menü auf **Systemeinstellungen**. 
   
    ![Screenshot, in dem auf Ihrer Questetra BPM Suite-Unternehmenswebsite die Option „Systemeinstellungen“ ausgewählt ist.](./media/questetra-bpm-suite-tutorial/settings.png)

3. Klicken Sie zum Öffnen der Seite **SingleSignOnSAML** auf **SSO (SAML)** . 

    ![Screenshot, in dem die Option „SSO (SAML)“ ausgewählt ist.](./media/questetra-bpm-suite-tutorial/apps.png)

4. Führen Sie auf der **Questetra BPM-Suite**-Unternehmenswebsite im Abschnitt **SP Information** (SP-Informationen) die folgenden Schritte aus:

    a. Kopieren Sie die **ACS-URL**, und fügen Sie sie im Azure-Portal im Abschnitt **Grundlegende SAML-Konfiguration** in das Textfeld **Anmelde-URL** ein.
    
    b. Kopieren Sie den Wert für **Entity ID** (Entitäts-ID), und fügen Sie sie im Azure-Portal im Abschnitt **Grundlegende SAML-Konfiguration** in das Textfeld **Bezeichner** ein.

5. Führen Sie auf der Unternehmenswebsite von **Questetra BPM Suite** die folgenden Schritte aus: 
   
    ![Einmaliges Anmelden konfigurieren](./media/questetra-bpm-suite-tutorial/certificate.png)
   
    a. Wählen Sie **Einmaliges Anmelden aktivieren** aus.
   
    b. Fügen Sie in das Textfeld **Entity ID** (Entitäts-ID) den Wert für **Azure AD-Bezeichner** ein, den Sie aus dem Azure-Portal kopiert haben.
    
    c. Fügen Sie in das Textfeld **Sign-in page URL** (URL der Anmeldeseite) den Wert der **Anmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.
    
    d. Fügen Sie in das Textfeld **Sign-out page URL** (URL der Abmeldeseite) den Wert der **Abmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.
    
    e. Geben Sie im Textfeld **NameID format** (NameID-Format) `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress` ein.

    f. Öffnen Sie in Editor das **Base64**-codierte Zertifikat, das Sie aus dem Azure-Portal heruntergeladen haben, kopieren Sie den Inhalt des Zertifikats in die Zwischenablage, und fügen Sie ihn anschließend in das Textfeld **Verification certificate** (Überprüfungszertifikat) ein. 

    g. Klicken Sie auf **Speichern**.

### <a name="create-questetra-bpm-suite-test-user"></a>Erstellen eines Questetra BPM Suite-Testbenutzers

Das Ziel dieses Abschnitts ist das Erstellen einer Benutzerin namens Britta Simon in der Questetra BPM Suite.

**Führen Sie die folgenden Schritte aus, um eine Benutzerin namens Britta Simon in der Questetra BPM Suite zu erstellen:**

1. Melden Sie sich auf der Questetra BPM Suite-Unternehmenswebsite als Administrator an.

2. Wechseln Sie zu **Systemeinstellungen > Benutzerliste > Neuer Benutzer**.
 
3. Führen Sie im Dialogfeld „Neuer Benutzer“ die folgenden Schritte aus: 
   
    ![Testbenutzer erstellen](./media/questetra-bpm-suite-tutorial/users.png)
   
    a. Geben Sie im Textfeld **Name** als **Namen** des Benutzers britta.simon@contoso.com ein.
   
    b. Geben Sie im Textfeld **Email** (E-Mail) als **E-Mail-Adresse** des Benutzers britta.simon@contoso.com ein.
   
    c. Geben Sie im Textfeld **Password** (Kennwort) ein **Kennwort** des Benutzers ein.
    
    d. Klicken Sie auf **Neuen Benutzer hinzufügen**.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für Questetra BPM Suite weitergeleitet, über die Sie den Anmeldeprozess initiieren können. 

* Rufen Sie direkt die Anmelde-URL für Questetra BPM Suite auf, und initiieren Sie den Anmeldeprozess.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie unter „Meine Apps“ auf die Kachel „Questetra BPM Suite“ klicken, werden Sie zur Anmelde-URL für Questetra BPM Suite weitergeleitet. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Questetra BPM Suite können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-aad)
