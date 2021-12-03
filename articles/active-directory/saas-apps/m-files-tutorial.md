---
title: 'Tutorial: Azure Active Directory-Integration mit M-Files | Microsoft Docs'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und M-Files konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/24/2021
ms.author: jeedes
ms.openlocfilehash: 66a641cbf5735f6f136c2e765aca593075179303
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132313328"
---
# <a name="tutorial-azure-active-directory-integration-with-m-files"></a>Tutorial: Azure Active Directory-Integration mit M-Files

In diesem Tutorial erfahren Sie, wie Sie M-Files in Azure Active Directory (Azure AD) integrieren. Die Integration von M-Files in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf M-Files hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei M-Files anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein M-Files-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* M-Files unterstützt **SP**-initiiertes einmaliges Anmelden.

## <a name="add-m-files-from-the-gallery"></a>Hinzufügen von M-Files aus dem Katalog

Zum Konfigurieren der Integration von M-Files in Azure AD müssen Sie M-Files aus dem Katalog der Liste der verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Begriff **M-Files** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **M-Files** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-m-files"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für M-Files

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit M-Files mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in M-Files eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit M-Files die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für M-Files](#configure-m-files-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines M-Files-Testbenutzers](#create-m-files-test-user)** , um eine Entsprechung von B. Simon in M-Files zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **M-Files** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    a. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<tenantname>.cloudvault.m-files.com/authentication/MFiles.AuthenticationProviders.Core/sso`.

    b. Geben Sie im Textfeld **Bezeichner (Entitäts-ID)** eine URL im folgenden Format ein: `https://<tenantname>.cloudvault.m-files.com`.

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch die tatsächliche Anmelde-URL und den tatsächlichen Bezeichner. Wenden Sie sich an das [Supportteam von M-Files](mailto:support@m-files.com), um diese Werte zu erhalten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

5. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um den Ihren Anforderungen entsprechenden **Verbundmetadaten-XML**-Code aus den verfügbaren Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

6. Kopieren Sie im Abschnitt **M-Files einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf M-Files gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **M-Files** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-m-files-sso"></a>Konfigurieren des einmaligen Anmeldens für M-Files

1. Wenden Sie sich unter an das [Supportteam von M-Files](mailto:support@m-files.com), um SSO für Ihre Anwendung konfigurieren zu lassen, und stellen Sie die heruntergeladenen Metadaten bereit.
   
    >[!NOTE]
    >Führen Sie die nächsten Schritte aus, wenn Sie SSO für die M-Files-Desktopanwendung konfigurieren möchten. Es sind keine zusätzlichen Schritte erforderlich, wenn Sie SSO nur für die Webversion von M-Files konfigurieren möchten.  

1. Führen Sie die nächsten Schritte aus, um die M-Files-Desktopanwendung mit aktiviertem SSO mit Azure AD zu konfigurieren. Um M-Files herunterzuladen, navigieren Sie zur Seite [M-Files Herunterladen](https://www.m-files.com/customers/product-downloads/download-update-links/).

1. Öffnen Sie das Fenster **M-Files Desktop Settings** (M-Files-Desktopeinstellungen). Klicken Sie anschließend auf **Hinzufügen**.
   
    ![Screenshot: Abschnitt „M-Files Desktop Settings“ (M-Files-Desktopeinstellungen), in dem Sie „Add“ (Hinzufügen) auswählen können](./media/m-files-tutorial/settings.png)

1. Führen Sie im Fenster **Document Vault Connection Properties** (Eigenschaften von Dokumenttresorverbindung) die folgenden Schritte aus:
   
    ![Screenshot: Abschnitt „Document Vault Connection Properties“ (Eigenschaften von Dokumenttresorverbindung), in dem Sie die beschriebenen Werte eingeben können](./media/m-files-tutorial/general.png)  

    Geben Sie im Abschnitt „Server“ die Werte wie folgt ein:  

    a. Geben Sie für **Name** den Wert `<tenant-name>.cloudvault.m-files.com` ein. 
 
    b. Geben Sie als **Portnummer** den Wert **4466** ein. 

    c. Wählen Sie als **Protokoll** die Option **HTTPS** aus. 

    d. Wählen Sie im Feld **Authentication** (Authentifizierung) die Option **Specific Windows user** (Festgelegter Windows-Benutzer) aus. Anschließend werden Sie auf einer Seite zur Anmeldung aufgefordert. Geben Sie Ihre Azure AD-Anmeldeinformationen ein. 

    e. Wählen Sie für **Vault on Server** (Tresor auf Server) den entsprechenden Tresor auf dem Server aus.
 
    f. Klicken Sie auf **OK**.

### <a name="create-m-files-test-user"></a>Erstellen eines M-Files-Testbenutzers

Das Ziel dieses Abschnitts ist das Erstellen eines Benutzers namens Britta Simon in M-Files. Lassen Sie sich beim Hinzufügen der Benutzer in M-Files ggf. vom [M-Files-Supportteam](mailto:support@m-files.com) unterstützen.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für M-Files weitergeleitet, wo Sie den Anmeldeflow initiieren können. 

* Navigieren Sie direkt zur Anmelde-URL für M-Files, und initiieren Sie den Anmeldeflow.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie unter „Meine Apps“ auf die Kachel „M-Files“ klicken, werden Sie zur Anmelde-URL für M-Files weitergeleitet. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von M-Files können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-any-app)
