---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit SolarWinds Orion | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie einmaliges Anmelden zwischen Azure Active Directory und SolarWinds Orion konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/29/2021
ms.author: jeedes
ms.openlocfilehash: 925ed8e4461fb7c97ba4d29819862fe84c27d82a
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132320655"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-solarwinds-orion"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit SolarWinds Orion

In diesem Tutorial erfahren Sie, wie Sie SolarWinds Orion in Azure Active Directory (Azure AD) integrieren. Die Integration von SolarWinds Orion in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf SolarWinds Orion hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihrem Azure AD-Konto automatisch bei SolarWinds Orion anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein SolarWinds Orion-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* SolarWinds Orion unterstützt **SP- und IDP-initiiertes** einmaliges Anmelden.

## <a name="add-solarwinds-orion-from-the-gallery"></a>Hinzufügen von SolarWinds Orion aus dem Katalog

Zum Konfigurieren der Integration von SolarWinds Orion in Azure AD müssen Sie SolarWinds Orion aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **SolarWinds Orion** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **SolarWinds Orion** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.


## <a name="configure-and-test-azure-ad-sso-for-solarwinds-orion"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für SolarWinds Orion

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit SolarWinds Orion mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in SolarWinds Orion eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit SolarWinds Orion die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für SolarWinds Orion](#configure-solarwinds-orion-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines SolarWinds Orion-Testbenutzers](#create-solarwinds-orion-test-user)** , um in SolarWinds Orion eine Entsprechung von B. Simon zu erhalten, die mit der Darstellung des Benutzers in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **SolarWinds Orion** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Geben Sie im Abschnitt **Grundlegende SAML-Konfiguration** die Werte in die folgenden Felder ein, wenn Sie die Anwendung im **IDP**-initiierten Modus konfigurieren möchten:

    a. Geben Sie im Textfeld **Bezeichner** eine URL im folgenden Format ein: `https://<ORION-HOSTNAME-OR-EXTERNAL-URL>`

    b. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://<ORION-HOSTNAME-OR-EXTERNAL-URL>/Orion/SAMLLogin.aspx`

1. Klicken Sie auf **Zusätzliche URLs festlegen**, und führen Sie den folgenden Schritt aus, wenn Sie die Anwendung im **SP-initiierten Modus** konfigurieren möchten:

    Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<ORION-HOSTNAME-OR-EXTERNAL-URL>/Orion/Login.aspx`

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Sie müssen diese Werte mit dem tatsächlichen Bezeichner, der Antwort-URL und der Anmelde-URL aktualisieren. Diese Werte erhalten Sie vom [Supportteam für den SolarWinds Orion-Client](mailto:technicalsupport@solarwinds.com). Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

1. Die SolarWinds Orion-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Daher müssen Sie Ihrer Konfiguration der SAML-Tokenattribute benutzerdefinierte Attributzuordnungen hinzufügen. Der folgende Screenshot zeigt die Liste der Standardattribute.

    ![image](common/default-attributes.png)

1. Darüber hinaus erwartet die SolarWinds Orion-Anwendung, dass in der SAML-Antwort noch einige weitere Attribute zurückgegeben werden (siehe unten). Diese Attribute werden ebenfalls vorab aufgefüllt, Sie können sie jedoch nach Bedarf überprüfen.
    
    | Name |  Quellattribut|
    | ----------- | --------- |
    | FirstName | user.givenname |
    | LastName | user.surname |
    | Email |user.mail |

1. Klicken Sie im Abschnitt **Benutzerattribute und Ansprüche** auf das Stiftsymbol, um die Einstellung zu bearbeiten, und klicken Sie dann auf **Gruppenanspruch hinzufügen**.

    ![Screenshot: „Benutzerattribute und Ansprüche“](./media/solarwinds-orion-tutorial/group-claim.png)

1. Wählen Sie **Sicherheitsgruppen**.
1. Wenn Azure AD mit Ihrem lokalen Active Directory synchronisiert ist, ändern Sie das **Quellattribut** in **sAMAccountName**. Belassen Sie es andernfalls als Gruppen-ID.

1. Setzen Sie in den **erweiterten Optionen** ein Häkchen bei **Customize the name of the group claim** (Namen des Gruppenanspruchs anpassen) und geben Sie OrionGroups als Namen ein.

1. Klicken Sie auf **Speichern**.

1. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** zum Eintrag **Zertifikat (Base64)** . Wählen Sie **Herunterladen** aus, um das Zertifikat herunterzuladen, und speichern Sie es auf Ihrem Computer.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

1. Kopieren Sie im Abschnitt **SolarWinds Orion** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Evergreen gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste den Eintrag **Evergreen** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-solarwinds-orion-sso"></a>Konfigurieren des einmaligen Anmeldens für SolarWinds Orion

1. Melden Sie sich bei SolarWinds Orion an, und navigieren Sie zu **Settings** -> **All Settings** (Einstellungen > Alle Einstellungen).

    ![Screenshot: Auswahl von „Settings“ > „All Settings“ (Einstellungen > Alle Einstellungen)](./media/solarwinds-orion-tutorial/settings.png)

1. Wählen Sie im Abschnitt **USER ACCOUNTS** (BENUTZERKONTEN) die Option **SAML Configuration** (SAML-Konfiguration) aus.

    ![Screenshot: Auswahl von „SAML Configuration“ (SAML-Konfiguration) unter „User Accounts“ (Benutzerkonten)](./media/solarwinds-orion-tutorial/configure-user-accounts.png)

1. Klicken Sie auf **ADD IDENTITY PROVIDER** (IDENTITÄTSANBIETER HINZUFÜGEN).

    ![Screenshot: Option „SAML Configuration“ (SAML-Konfiguration) mit Auswahl von „ADD IDENTITY PROVIDER“ (IDENTITÄTSANBIETER HINZUFÜGEN)](./media/solarwinds-orion-tutorial/configure-add-identity-provider.png)

1. Gehen Sie auf der Seite **Add Identity Provider** (Identitätsanbieter hinzufügen) wie folgt vor:

    ![Screenshot: Abschnitt „Add Identity Provider“ (Identitätsanbieter hinzufügen), in dem Sie die beschriebenen Werte eingeben können](./media/solarwinds-orion-tutorial/configure-solarwinds.png)

    a. Wechseln Sie zur Registerkarte **Configure** (Konfigurieren):

    b. Geben Sie in das Textfeld **Identity Provider Name** (Name des Identitätsanbieters) einen gültigen Namen ein, etwa `My SSO service`.

    c. Fügen Sie im Textfeld **SSO Target URL** (SSO-Ziel-URL) den Wert der **Anmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    d.  Fügen Sie im Textfeld **Issuer URL** (Aussteller-URL) den **Azure AD-Bezeichner** ein, den Sie aus dem Azure-Portal kopiert haben.

    e. Öffnen Sie das aus dem Azure-Portal heruntergeladene **Zertifikat (Base64)** im Editor, und fügen Sie den Inhalt in das Textfeld **X.509 Signing Certificate** (X.509-Signaturzertifikat) ein.

    f. Klicken Sie auf **Speichern**.

### <a name="create-solarwinds-orion-test-user"></a>Erstellen eines SolarWinds Orion-Testbenutzers

1. Melden Sie sich bei der SolarWinds Orion-Website an, und navigieren Sie zu **Settings** -> **All Settings** (Einstellungen > Alle Einstellungen).

    ![Screenshot: Auswahl von „Settings“ > „All Settings“ (Einstellungen > Alle Einstellungen)](./media/solarwinds-orion-tutorial/settings.png)

1. Wählen Sie im Abschnitt **USER ACCOUNTS** (BENUTZERKONTEN) die Option **Manage Accounts** (Konten verwalten) aus.

    ![Screenshot: Auswahl von „Manage Accounts“ (Konten verwalten)](./media/solarwinds-orion-tutorial/user-accounts.png)

1. Klicken Sie auf der Registerkarte **INDIVIDUAL ACCOUNTS** (EINZELNE KONTEN) auf **ADD NEW ACCOUNT** (NEUES KONTO HINZUFÜGEN).

    ![Screenshot: Auswahl von „ADD NEW ACCOUNT“ (NEUES KONTO HINZUFÜGEN) unter „Manage Accounts“ (Konten verwalten)](./media/solarwinds-orion-tutorial/create-user.png)

1. Wählen Sie den Kontotyp aus, den Sie benötigen, um entweder einzelne SAML-Benutzer oder SAML-Gruppen zu erstellen.

    ![Screenshot: Abschnitt „Add New Account“ (Neues Konto hinzufügen), in dem Sie den Kontotyp auswählen können](./media/solarwinds-orion-tutorial/create-user-new-account.png)

1.  Geben Sie im Textfeld **Name ID** (Namens-ID) den Namen ein, der genau mit dem Benutzernamen oder dem Gruppennamen in Azure AD übereinstimmen muss.

1.  Klicken Sie auf **Next** (Weiter), und übermitteln Sie die Seite.

    ![Screenshot: Abschnitt „Add New Account“ (Neues Konto hinzufügen), in dem Sie die Namens-ID aus Azure AD eingeben können](./media/solarwinds-orion-tutorial/create-user-name-id.png)

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

#### <a name="sp-initiated"></a>SP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für SolarWinds Orion weitergeleitet, wo Sie den Anmeldeflow initiieren können.  

* Rufen Sie direkt die SolarWinds Orion-Anmelde-URL auf, und initiieren Sie den Anmeldeflow.

#### <a name="idp-initiated"></a>IDP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch sollten Sie automatisch bei der SolarWinds Orion-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. 

Sie können auch den Microsoft-Bereich „Meine Apps“ verwenden, um die Anwendung in einem beliebigen Modus zu testen. Beim Klicken auf die Kachel „SolarWinds Orion“ in „Meine Apps“ geschieht Folgendes: Wenn Sie die Anwendung im SP-Modus konfiguriert haben, werden Sie zum Initiieren des Anmeldeflows zur Anmeldeseite der Anwendung weitergeleitet. Wenn Sie die Anwendung im IDP-Modus konfiguriert haben, sollten Sie automatisch bei der SolarWinds Orion-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von SolarWinds Orion können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-any-app)
