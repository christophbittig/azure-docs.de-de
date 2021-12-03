---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit BIC Cloud Design | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie einmaliges Anmelden zwischen Azure Active Directory und BIC Cloud Design konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/15/2021
ms.author: jeedes
ms.openlocfilehash: 95d61e63e62c618cfc982b853fdb2427e7010ea6
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132282929"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-bic-cloud-design"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit BIC Cloud Design

In diesem Tutorial erfahren Sie, wie Sie BIC Cloud Design in Azure Active Directory (Azure AD) integrieren. Die Integration von BIC Cloud Design in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf BIC Cloud Design hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei BIC Cloud Design anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* BIC Cloud Design-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

> [!NOTE]
> Diese Integration kann auch über die Azure AD-Umgebung für die US Government-Cloud verwendet werden. Sie finden diese Anwendung im Azure AD-Katalog für US Government-Cloudanwendungen und konfigurieren sie auf die gleiche Weise wie in der öffentlichen Cloud.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* BIC Cloud Design unterstützt **SP-initiiertes** einmaliges Anmelden.

## <a name="add-bic-cloud-design-from-the-gallery"></a>Hinzufügen von BIC Cloud Design aus dem Katalog

Zum Konfigurieren der Integration von Azure AD in BIC Cloud Design müssen Sie BIC Cloud Design aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **BIC Cloud Design** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **BIC Cloud Design** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-bic-cloud-design"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für BIC Cloud Design

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit BIC Cloud Design mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in BIC Cloud Design eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit BIC Cloud Design die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für BIC Cloud Design](#configure-bic-cloud-design-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines BIC Cloud Design-Testbenutzers](#create-bic-cloud-design-test-user)** , um eine Entsprechung von B. Simon in BIC Cloud Design zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **BIC Cloud Design** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus, wenn Sie über eine **Dienstanbieter-Metadatendatei** verfügen:

    a. Klicken Sie auf **Metadatendatei hochladen**.

    ![Metadatendatei hochladen](common/upload-metadata.png)

    b. Klicken Sie auf das **Ordnerlogo**, wählen Sie die Metadatendatei aus, und klicken Sie auf **Hochladen**.

    ![Metadatendatei auswählen](common/browse-upload-metadata.png)

    c. Nach dem erfolgreichen Upload der Metadatendatei wird der Wert **Bezeichner** automatisch im Abschnitt „Grundlegende SAML-Konfiguration“ eingefügt.

    Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein:

    | Anmelde-URL |
    |-----|
    | `https://<CUSTOMER_SPECIFIC_NAME/TENANT>.biccloud.com` |
    | `https://<CUSTOMER_SPECIFIC_NAME/TENANT>.biccloud.de` |
    
    > [!Note]
    > Falls der Wert **Bezeichner** nicht automatisch aufgefüllt wird, geben Sie den erforderlichen Wert entsprechend Ihren Anforderungen manuell ein. Der Wert der Anmelde-URL entspricht nicht dem tatsächlichen Wert. Ersetzen Sie diesen Wert durch die tatsächliche Anmelde-URL. Diesen Wert erhalten Sie vom [Supportteam für den BIC Cloud Design-Client](mailto:bicsupport@gbtec.de). Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

1. Ihre BIC Cloud Design-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Daher müssen Sie Ihrer Konfiguration der SAML-Tokenattribute benutzerdefinierte Attributzuordnungen hinzufügen. Der folgende Screenshot zeigt die Liste der Standardattribute.

    ![image](common/default-attributes.png)

1. Darüber hinaus erwartet die BIC Cloud Design-Anwendung, dass in der SAML-Antwort noch einige weitere Attribute zurückgegeben werden (siehe unten). Diese Attribute werden ebenfalls vorab aufgefüllt, Sie können sie jedoch nach Bedarf überprüfen.

    | Name | Quellattribut|
    | ------------ | --------- |
    | Name | user.name |
    | E-Mail-Adresse | user.mail |
    | Namens-ID | user.userprincipalname |
    | email | user.mail |
    | nametest | user.displayname |

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf BIC Cloud Design gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **BIC Cloud Design** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-bic-cloud-design-sso"></a>Konfigurieren des einmaligen Anmeldens für BIC Cloud Design

Zum Konfigurieren des einmaligen Anmeldens aufseiten von **BIC Cloud Design** müssen Sie die **App-Verbundmetadaten-URL**-Datei an das [Supportteam von BIC Cloud Design](mailto:bicsupport@gbtec.de) senden. Es führt die Einrichtung durch, damit die SAML-SSO-Verbindung auf beiden Seiten richtig festgelegt ist.

### <a name="create-bic-cloud-design-test-user"></a>Erstellen eines BIC Cloud Design-Testbenutzers

In diesem Abschnitt erstellen Sie in BIC Cloud Design einen Benutzer namens B. Simon. Zum Hinzufügen der Benutzer auf der BIC Cloud Design-Plattform wenden Sie sich an das [Supportteam von BIC Cloud Design](mailto:bicsupport@gbtec.de). Benutzer müssen erstellt und aktiviert werden, damit Sie einmaliges Anmelden verwenden können.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für BIC Cloud Design weitergeleitet, wo Sie den Anmeldeflow initiieren können. 

* Rufen Sie direkt die BIC Cloud Design-Anmelde-URL auf, und initiieren Sie den Anmeldeflow.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie in „Meine Apps“auf die Kachel „BIC Cloud Design“ klicken, werden Sie zur Anmelde-URL für BIC Cloud Design weitergeleitet. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von BIC Cloud Design können Sie Sitzungssteuerungen erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützen. Sitzungssteuerungen basieren auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-any-app)
