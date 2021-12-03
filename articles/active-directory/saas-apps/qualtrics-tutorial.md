---
title: 'Tutorial: Azure Active Directory-Integration mit SAP Qualtrics | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie einmaliges Anmelden zwischen Azure Active Directory und SAP Qualtrics konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/18/2021
ms.author: jeedes
ms.openlocfilehash: 7ebc4387dccd7a434602ab6ad8f9713f58300233
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132307078"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-qualtrics"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit SAP Qualtrics

In diesem Tutorial erfahren Sie, wie Sie SAP Qualtrics in Azure Active Directory (Azure AD) integrieren. Die Integration von SAP Qualtrics in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf SAP Qualtrics hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei SAP Qualtrics anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Zunächst benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein SAP Qualtrics-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* SAP Qualtrics unterstützt **SP**- und **IDP**-initiiertes einmaliges Anmelden.
* SAP Qualtrics unterstützt die **Just-In-Time**-Benutzerbereitstellung.

## <a name="add-sap-qualtrics-from-the-gallery"></a>Hinzufügen von SAP Qualtrics aus dem Katalog

Zum Konfigurieren der Integration von SAP Qualtrics in Azure AD müssen Sie SAP Qualtrics aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Bereich **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **SAP Qualtrics** in das Suchfeld ein.
1. Wählen Sie in den Ergebnissen **SAP Qualtrics** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sap-qualtrics"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für SAP Qualtrics

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit SAP Qualtrics mithilfe eines Testbenutzers namens **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in SAP Qualtrics eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit SAP Qualtrics die folgenden Schritte aus:

1. [Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso), um Ihren Benutzern die Verwendung dieses Features zu ermöglichen
    1. [Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user), um das einmalige Anmelden von Azure AD mit B.Simon zu testen
    1. [Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user), um B.Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen
1. [Konfigurieren des einmaligen Anmeldens für SAP Qualtrics](#configure-sap-qualtrics-sso), um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. [Erstellen eines SAP Qualtrics-Testbenutzers](#create-sap-qualtrics-test-user), um ein Pendant von B. Simon in SAP Qualtrics zu erhalten, das mit ihrer Darstellung in Azure AD verknüpft ist
1. [Testen des einmaligen Anmeldens](#test-sso), um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **SAP Qualtrics** zum Abschnitt **Verwalten**. Wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Wählen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** das Stiftsymbol für **Grundlegende SAML-Konfiguration** aus, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Geben Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** die Werte in die folgenden Felder ein, wenn Sie die Anwendung im **IDP-initiierten** Modus konfigurieren möchten:
    
    a. Geben Sie im Textfeld **Bezeichner** eine URL im folgenden Format ein:

    `https://< DATACENTER >.qualtrics.com`
   
    b. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein:

    `https://< DATACENTER >.qualtrics.com/login/v1/sso/saml2/default-sp`

    c. Geben Sie im Textfeld **Relayzustand** eine URL im folgenden Format ein:

    `https://< brandID >.< DATACENTER >.qualtrics.com`

1. Wählen Sie **Zusätzliche URLs festlegen** aus, und führen Sie den folgenden Schritt aus, wenn Sie die Anwendung im **SP-initiierten** Modus konfigurieren möchten:

    Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein:

    `https://< brandID >.< DATACENTER >.qualtrics.com`

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Aktualisieren Sie diese Werte mit den tatsächlichen Werten für Anmelde-URL, Bezeichner, Antwort-URL und Relayzustand. Diese Werte erhalten Sie vom [Supportteam für den Qualtrics-Client](https://www.qualtrics.com/support/). Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

1. Wählen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** das Kopiersymbol aus, um die **App-Verbundmetadaten-URL** zu kopieren, und speichern Sie sie auf Ihrem Computer.

    ![Downloadlink für das Zertifikat](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

In diesem Abschnitt erstellen Sie im Azure-Portal einen Testbenutzer namens B.Simon.

1. Wählen Sie im Azure-Portal im linken Bereich **Azure Active Directory** > **Benutzer** > **Alle Benutzer** aus.
1. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.
1. Führen Sie unter den Eigenschaften für **Benutzer** die folgenden Schritte aus:
   1. Geben Sie im Feld **Name** die Zeichenfolge `B.Simon` ein.  
   1. Geben Sie im Feld **Benutzername** die Zeichenfolge username@companydomain.extension ein. Beispiel: `B.Simon@contoso.com`.
   1. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**, und notieren Sie sich das Kennwort.
   1. Klicken Sie auf **Erstellen**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf SAP Qualtrics gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **SAP Qualtrics** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Klicken Sie auf **Benutzer hinzufügen**. Wählen Sie dann im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste der Benutzer die Option **B.Simon** aus. Wählen Sie anschließend am unteren Bildschirmrand **Auswählen** aus.
1. Falls Sie in der SAML-Assertion einen Rollenwert erwarten, wählen Sie im Dialogfeld **Rolle auswählen** die entsprechende Rolle für den Benutzer aus der Liste aus. Wählen Sie anschließend am unteren Bildschirmrand **Auswählen** aus.
1. Wählen Sie im Dialogfeld **Zuweisung hinzufügen** die Option **Zuweisen** aus.

## <a name="configure-sap-qualtrics-sso"></a>Konfigurieren des einmaligen Anmeldens für SAP Qualtrics

Senden Sie zum Konfigurieren des einmaligen Anmeldens aufseiten von SAP Qualtrics die kopierte **App-Verbundmetadaten-URL** aus dem Azure-Portal an das [Supportteam von SAP Qualtrics](https://www.qualtrics.com/support/). Das Supportteam sorgt dafür, dass die SAML-Verbindung für einmaliges Anmelden auf beiden Seiten ordnungsgemäß eingerichtet wird.

### <a name="create-sap-qualtrics-test-user"></a>Erstellen eines SAP Qualtrics-Testbenutzers

SAP Qualtrics unterstützt die Just-In-Time-Benutzerbereitstellung (standardmäßig aktiviert). Es ist keine weitere Aktion Ihrerseits erforderlich. Ist ein Benutzer noch nicht in SAP Qualtrics vorhanden, wird nach der Authentifizierung ein neuer Benutzer erstellt.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

#### <a name="sp-initiated"></a>SP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für SAP Qualtrics weitergeleitet, wo Sie den Anmeldeflow initiieren können.  

* Rufen Sie direkt die SAP Qualtrics-Anmelde-URL auf, und initiieren Sie den Anmeldeflow.

#### <a name="idp-initiated"></a>IDP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch sollten Sie automatisch bei der SAP Qualtrics-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben.

Sie können auch den Microsoft-Bereich „Meine Apps“ verwenden, um die Anwendung in einem beliebigen Modus zu testen. Wenn Sie in „Meine Apps“ auf die Kachel „SAP Qualtrics“ klicken, geschieht Folgendes: Wenn Sie den SP-Modus konfiguriert haben, werden Sie zum Initiieren des Anmeldeflows zur Anmeldeseite der Anwendung weitergeleitet. Wenn Sie den IDP-Modus konfiguriert haben, sollten Sie automatisch bei der SAP Qualtrics-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von SAP Qualtrics können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. Weitere Informationen finden Sie im Artikel zum [Erzwingen der Sitzungssteuerung mit Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-any-app).
