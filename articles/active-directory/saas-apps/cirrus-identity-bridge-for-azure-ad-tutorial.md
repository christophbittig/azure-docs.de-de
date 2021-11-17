---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Cirrus Identity Bridge for Azure AD | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie einmaliges Anmelden zwischen Azure Active Directory und Cirrus Identity Bridge for Azure AD konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/03/2021
ms.author: jeedes
ms.openlocfilehash: 4046fdafdcd3d661bc608ccf29d73a647e5c5acd
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132334927"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cirrus-identity-bridge-for-azure-ad"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Cirrus Identity Bridge for Azure AD

In diesem Tutorial erfahren Sie, wie Sie Cirrus Identity Bridge for Azure AD in Azure Active Directory (Azure AD) integrieren. Die Integration von Cirrus Identity Bridge for Azure AD in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf Cirrus Identity Bridge for Azure AD hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Cirrus Identity Bridge for Azure AD anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Cirrus Identity Bridge for Azure AD-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist Wenn Sie noch kein Abonnent sind, besuchen Sie die [Registrierungsseite für Cirrus Identity Bridge for Azure AD](https://info.cirrusidentity.com/cirrus-identity-azure-ad-app-gallery-registration).

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Cirrus Identity Bridge for Azure AD unterstützt **SP**- und **IDP**-initiiertes einmaliges Anmelden.

## <a name="add-cirrus-identity-bridge-for-azure-ad-from-the-gallery"></a>Hinzufügen von Cirrus Identity Bridge for Azure AD aus dem Katalog

Zum Konfigurieren der Integration von Cirrus Identity Bridge for Azure AD in Azure AD müssen Sie Cirrus Identity Bridge for Azure AD aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Cirrus Identity Bridge for Azure AD** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Cirrus Identity Bridge for Azure AD** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-cirrus-identity-bridge-for-azure-ad"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Cirrus Identity Bridge for Azure AD

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Cirrus Identity Bridge for Azure AD mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Cirrus Identity Bridge for Azure AD eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Cirrus Identity Bridge for Azure AD die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Cirrus Identity Bridge for Azure AD](#configure-cirrus-identity-bridge-for-azure-ad-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Einrichten von Cirrus Identity Bridge for Azure AD-Tests](#setup-cirrus-identity-bridge-for-azure-ad-testing)** , um ein Pendant von B. Simon in Cirrus Identity Bridge for Azure AD zu erhalten, das mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Cirrus Identity Bridge for Azure AD** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    a. Geben Sie im Textfeld **Bezeichner (Entitäts-ID)** eine URL im folgenden Format ein: `https://<SUBDOMAIN>.cirrusidentity.com/bridge`.

    b. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://<NAME>.proxy.cirrusidentity.com/module.php/saml/sp/saml2-acs.php/<NAME>_proxy`

1. Klicken Sie auf Zusätzliche URLs festlegen, und führen Sie den folgenden Schritt aus, wenn Sie die Anwendung im SP-initiierten Modus konfigurieren möchten:
 
    Geben Sie im Textfeld **Anmelde-URL** einen Wert im folgenden Format ein: `<CUSTOMER_LOGIN_URL>`.

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch den tatsächlichen Bezeichner und die tatsächliche Anmelde-URL. Wenn Sie Cirrus Bridge noch nicht abonniert haben, besuchen Sie die [Registrierungsseite](https://info.cirrusidentity.com/cirrus-identity-azure-ad-app-gallery-registration). Wenn Sie ein Bestandskunde von Cirrus Bridge sind, erhalten Sie diese Werte vom [Supportteam für den Cirrus Identity Bridge for Azure AD-Client](https://www.cirrusidentity.com/resources/service-desk). Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

1. Die Cirrus Identity Bridge for Azure AD-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Daher müssen Sie Ihrer Konfiguration der SAML-Tokenattribute benutzerdefinierte Attributzuordnungen hinzufügen. Der folgende Screenshot zeigt die Liste der Standardattribute.

    ![image](common/default-attributes.png)

1. Darüber hinaus wird von der Cirrus Identity Bridge for Azure AD-Anwendung erwartet, dass in der SAML-Antwort noch einige weitere Attribute zurückgegeben werden (siehe unten). Diese Attribute werden ebenfalls vorab aufgefüllt, Sie können sie jedoch nach Bedarf überprüfen.
    
    | Name |  Quellattribut|
    | ---------| --------- |
    | displayname | user.displayname |

1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf die Schaltfläche „Kopieren“, um die **App-Verbundmetadaten-URL** zu kopieren, und speichern Sie sie auf Ihrem Computer.

    ![Downloadlink für das Zertifikat](common/copy-metadataurl.png)

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Cirrus Identity Bridge for Azure AD gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Cirrus Identity Bridge for Azure AD** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-cirrus-identity-bridge-for-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens für Cirrus Identity Bridge for Azure AD

Zum Konfigurieren des einmaligen Anmeldens aufseiten von **Cirrus Identity Bridge for Azure AD** müssen Sie die **App-Verbundmetadaten-URL** an das [Supportteam von Cirrus Identity Bridge for Azure AD](https://www.cirrusidentity.com/resources/service-desk) senden. Es führt die Einrichtung durch, damit die SAML-SSO-Verbindung auf beiden Seiten richtig festgelegt ist.

### <a name="setup-cirrus-identity-bridge-for-azure-ad-testing"></a>Einrichten von Cirrus Identity Bridge for Azure AD-Tests

In diesem Abschnitt überprüfen Sie, ob ein Benutzer namens Britta Simon zum Testen verwendet werden kann. Das [Supportteam von Cirrus Identity Bridge for Azure AD](https://www.cirrusidentity.com/resources/service-desk) stellt eine Test-URL zur Verfügung, über die Sie überprüfen können, ob Britta Simon mit der Cirrus Identity Bridge for Azure AD-Plattform verwendet werden kann. Der Testbenutzer Britta Simon muss darüber hinaus allen Anwendungen hinzugefügt werden, die Cirrus Identity Bridge for Azure AD als Methode zur Authentifizierung verwenden (z. B. Anwendungen in multilateralen Verbundmetadaten). 

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

#### <a name="sp-initiated"></a>SP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für Cirrus Identity Bridge for Azure AD weitergeleitet, wo Sie den Anmeldeflow initiieren können.  

* Navigieren Sie direkt zur Anmelde-URL für Cirrus Identity Bridge for Azure AD, und initiieren Sie den Anmeldeflow.

#### <a name="idp-initiated"></a>IDP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch sollten Sie automatisch bei der Cirrus Identity Bridge for Azure AD-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. 

Sie können auch den Microsoft-Bereich „Meine Apps“ verwenden, um die Anwendung in einem beliebigen Modus zu testen. Beim Klicken auf die Kachel „Cirrus Identity Bridge for Azure AD“ in „Meine Apps“ geschieht Folgendes: Wenn Sie den SP-Modus konfiguriert haben, werden Sie zum Initiieren des Anmeldeflows zur Anmeldeseite der Anwendung weitergeleitet. Wenn Sie den IDP-Modus konfiguriert haben, sollten Sie automatisch bei der Cirrus Identity Bridge for Azure AD-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Cirrus Identity Bridge for Azure AD können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-aad)
