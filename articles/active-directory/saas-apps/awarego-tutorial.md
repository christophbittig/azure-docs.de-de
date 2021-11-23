---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit AwareGo | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie einmaliges Anmelden zwischen Azure Active Directory und AwareGo konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/07/2020
ms.author: jeedes
ms.openlocfilehash: b70fc467fe2a8502f83437c1f9c004033f440993
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132324375"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-awarego"></a>Tutorial: Integration des einmaligen Anmeldens von Azure Active Directory in AwareGo

In diesem Tutorial erfahren Sie, wie Sie AwareGo in Azure Active Directory (Azure AD) integrieren. Die Integration von AwareGo in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf AwareGo hat.
* Ermöglichen Sie Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei AwareGo anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* AwareGo-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung. AwareGo unterstützt SP-initiiertes einmaliges Anmelden.


## <a name="adding-awarego-from-the-gallery"></a>Hinzufügen von AwareGo aus dem Katalog

Zum Konfigurieren der Integration von AwareGo in Azure AD müssen Sie AwareGo aus dem Katalog zu Ihrer Liste der verwalteten SaaS-Apps (Software-as-a-Service) hinzufügen.

1. Melden Sie sich im Azure-Portal mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto an.
1. Wählen Sie im linken Bereich den Dienst **Azure Active Directory** aus.
1. Wählen Sie **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** im Suchfeld den Begriff **AwareGo** ein.
1. Wählen Sie im Ergebnisbereich **AwareGo** aus, und fügen Sie dann die App hinzu. Innerhalb von einigen Sekunden wird die App Ihrem Mandanten hinzugefügt.


## <a name="configure-and-test-azure-ad-sso-for-awarego"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für AwareGo

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit AwareGo mithilfe eines Testbenutzers namens **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in AwareGo eingerichtet werden.

Gehen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit AwareGo wie folgt vor:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen  

    a. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Benutzer B. Simon zu testen  
    b. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen  

1. **[Konfigurieren des einmaligen Anmeldens für AwareGo](#configure-awarego-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren

    a. **[Erstellen eines AwareGo-Testbenutzers](#create-an-awarego-test-user)** , um eine Entsprechung von B. Simon in AwareGo zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist  
    b. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert.

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Führen Sie die folgenden Schritte aus, um einmaliges Anmelden von Azure AD im Azure-Portal zu aktivieren:

1. Wählen Sie im Azure-Portal im Anwendungsintegrationsbereich für **AwareGo** unter **Verwalten** die Option **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Wählen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** die Schaltfläche **Bearbeiten** aus.

   ![Screenshot der Schaltfläche „Bearbeiten“ für die grundlegende SAML-Konfiguration](common/edit-urls.png)

1. Führen Sie unter **Grundlegende SAML-Konfiguration** im Bearbeitungsbereich die folgenden Schritte aus:

    a. Geben Sie im Feld **Anmelde-URL** eine der folgenden URLs ein:

    * `https://lms.awarego.com/auth/signin/` 
    * `https://my.awarego.com/auth/signin/`

    b. Geben Sie im Feld **Bezeichner (Entitäts-ID)** eine URL im folgenden Format ein: `https://<SUBDOMAIN>.awarego.com`

    c. Geben Sie im Feld **Antwort-URL** eine URL im folgenden Format ein: `https://<SUBDOMAIN>.awarego.com/auth/sso/callback`

    > [!NOTE]
    > Die vorangehenden Werte sind keine echten Werte. Aktualisieren Sie diese Werte mit dem eigentlichen Bezeichner und den Antwort-URLs. Wenden Sie sich an das [Supportteam für den AwareGo-Client](mailto:support@awarego.com), um diese Werte zu erhalten. Sie können sich die Beispiele auch im Azure-Portal im Abschnitt **Grundlegende SAML-Konfiguration** ansehen.

1. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** zum Eintrag **Zertifikat (Base64)** . Wählen Sie **Herunterladen** aus, um das Zertifikat herunterzuladen, und speichern Sie es auf Ihrem Computer.

    ![Screenshot des Links zum Herunterladen des Zertifikats im Bereich „SAML-Signaturzertifikat“](common/certificatebase64.png)

1. Kopieren Sie im Abschnitt **AwareGo einrichten** eine oder mehrere URLs gemäß Ihren Anforderungen.

    ![Screenshot des Bereichs „AwareGo einrichten“ zum Kopieren von Konfigurations-URLs](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

In diesem Abschnitt erstellen Sie im Azure-Portal einen Testbenutzer namens B.Simon.

1. Wählen Sie im Azure-Portal im linken Bereich **Azure Active Directory** und dann **Benutzer** > **Alle Benutzer** aus.
1. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.
1. Führen Sie im Eigenschaftenbereich **Benutzer** folgende Schritte aus:

   a. Geben Sie im Feld **Name** den Namen **B.Simon** ein.  
   b. Geben Sie im Feld **Benutzername** den Benutzernamen im folgenden Format ein: `<username>@<companydomain>.<extension>` (z. B. B.Simon@contoso.com).  
   c. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**, und notieren Sie den Wert im Feld **Kennwort** zur späteren Verwendung.  
   d. Klicken Sie auf **Erstellen**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie B.Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf AwareGo gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Liste **Anwendungen** den Eintrag **AwareGo** aus.
1. Wählen Sie auf der Übersichtsseite der App im Abschnitt **Verwalten** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Bereich **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Bereich **Benutzer und Gruppen** in der Liste **Benutzer** den Eintrag **B.Simon** und dann die Schaltfläche **Auswählen** aus.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie in der Dropdownliste **Rolle auswählen** auswählen. Wird für diese App keine Rolle eingerichtet, wird die Rolle *Standardzugriff* ausgewählt.
1. Wählen Sie im Bereich **Zuweisung hinzufügen** die Schaltfläche **Zuweisen** aus.

## <a name="configure-awarego-sso"></a>Konfigurieren des einmaligen Anmeldens für AwareGo

Senden Sie zum Konfigurieren des einmaligen Anmeldens aufseiten von **AwareGo** das zuvor heruntergeladene Zertifikat **Zertifikat (Base64)** und die zuvor im Azure-Portal kopierten URLs an das [AwareGo-Supportteam](mailto:support@awarego.com). Das Supportteam erstellt diese Einstellung, um die SAML-Verbindung für einmaliges Anmelden auf beiden Seiten ordnungsgemäß einzurichten.

### <a name="create-an-awarego-test-user"></a>Erstellen eines AwareGo-Testbenutzers

In diesem Abschnitt erstellen Sie in AwareGo einen Benutzer namens Britta Simon. Wenden Sie sich an das [AwareGo-Supportteam](mailto:support@awarego.com), um die Benutzer zur AwareGo-Plattform hinzuzufügen. Sie müssen die Benutzer erstellen und aktivieren, bevor Sie einmaliges Anmelden verwenden können.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt können Sie die Konfiguration des einmaligen Anmeldens von Azure AD wie folgt testen: 

* Wählen Sie im Azure-Portal die Option **Diese Anwendung testen** aus. Dadurch werden Sie zur AwareGo-Anmeldeseite umgeleitet, auf der Sie den Anmeldeflow initiieren können. 

* Rufen Sie direkt die AwareGo-Anmeldeseite auf, und initiieren Sie von dort den Anmeldeflow.

* Navigieren Sie zu „Meine Apps“ von Microsoft. Wenn Sie unter „Meine Apps“ die Kachel **AwareGo** auswählen, werden Sie auf die Anmeldeseite von AwareGo umgeleitet. Weitere Informationen finden Sie unter [Anmelden beim Portal „Meine Apps“ und Starten von Apps über dieses](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).


## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von AwareGo können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf der App-Steuerung für bedingten Zugriff. Weitere Informationen finden Sie im Artikel zum [Erzwingen der Sitzungssteuerung mit Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
