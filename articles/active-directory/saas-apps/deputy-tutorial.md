---
title: 'Tutorial: Azure Active Directory-Integration mit Deputy | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Deputy konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/11/2021
ms.author: jeedes
ms.openlocfilehash: 258c7b6e88129367c512af4b21c794351f31f243
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132280440"
---
# <a name="tutorial-azure-active-directory-integration-with-deputy"></a>Tutorial: Azure Active Directory-Integration mit Deputy

In diesem Tutorial erfahren Sie, wie Sie Deputy in Azure Active Directory (Azure AD) integrieren. Die Integration von Deputy in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf Deputy hat.
* Ermöglichen Sie Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Deputy anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Deputy-Abonnement, für das einmaliges Anmelden (SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Deputy unterstützt **SP- und IDP-initiiertes** einmaliges Anmelden.
* Deputy unterstützt die **Just-In-Time**-Benutzerbereitstellung.

## <a name="add-deputy-from-the-gallery"></a>Hinzufügen von Deputy aus dem Katalog

Zum Konfigurieren der Integration von Deputy in Azure AD müssen Sie Deputy aus dem Katalog der Liste der verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Deputy** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Deputy** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-deputy"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Deputy

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Deputy mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Deputy eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Deputy die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Deputy](#configure-deputy-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
    1. **[Erstellen eines Deputy-Testbenutzers](#create-deputy-test-user)** , um ein Pendant von B. Simon in Deputy zu erhalten, das mit ihrer Darstellung in Azure AD verknüpft ist.
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Deputy** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus, wenn Sie die Anwendung im **IDP**-initiierten Modus konfigurieren möchten:

    a. Geben Sie im Textfeld **Bezeichner** eine URL in einem der folgenden Formate ein:

    ```http
    https://<subdomain>.<region>.au.deputy.com
    https://<subdomain>.<region>.ent-au.deputy.com
    https://<subdomain>.<region>.na.deputy.com
    https://<subdomain>.<region>.ent-na.deputy.com
    https://<subdomain>.<region>.eu.deputy.com
    https://<subdomain>.<region>.ent-eu.deputy.com
    https://<subdomain>.<region>.as.deputy.com
    https://<subdomain>.<region>.ent-as.deputy.com
    https://<subdomain>.<region>.la.deputy.com
    https://<subdomain>.<region>.ent-la.deputy.com
    https://<subdomain>.<region>.af.deputy.com
    https://<subdomain>.<region>.ent-af.deputy.com
    https://<subdomain>.<region>.an.deputy.com
    https://<subdomain>.<region>.ent-an.deputy.com
    https://<subdomain>.<region>.deputy.com
    ```

    b. Geben Sie im Textfeld **Antwort-URL** eine URL in einem der folgenden Formate ein:
    
    ```http
    https://<subdomain>.<region>.au.deputy.com/exec/devapp/samlacs
    https://<subdomain>.<region>.ent-au.deputy.com/exec/devapp/samlacs
    https://<subdomain>.<region>.na.deputy.com/exec/devapp/samlacs
    https://<subdomain>.<region>.ent-na.deputy.com/exec/devapp/samlacs
    https://<subdomain>.<region>.eu.deputy.com/exec/devapp/samlacs
    https://<subdomain>.<region>.ent-eu.deputy.com/exec/devapp/samlacs
    https://<subdomain>.<region>.as.deputy.com/exec/devapp/samlacs.
    https://<subdomain>.<region>.ent-as.deputy.com/exec/devapp/samlacs
    https://<subdomain>.<region>.la.deputy.com/exec/devapp/samlacs
    https://<subdomain>.<region>.ent-la.deputy.com/exec/devapp/samlacs
    https://<subdomain>.<region>.af.deputy.com/exec/devapp/samlacs
    https://<subdomain>.<region>.ent-af.deputy.com/exec/devapp/samlacs
    https://<subdomain>.<region>.an.deputy.com/exec/devapp/samlacs
    https://<subdomain>.<region>.ent-an.deputy.com/exec/devapp/samlacs
    https://<subdomain>.<region>.deputy.com/exec/devapp/samlacs
    ```

5. Klicken Sie auf **Zusätzliche URLs festlegen**, und führen Sie den folgenden Schritt aus, wenn Sie die Anwendung im **SP-initiierten Modus** konfigurieren möchten:

    Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<your-subdomain>.<region>.deputy.com`

    >[!NOTE]
    > Das Regionssuffix für Deputy ist optional und muss bei Verwendung auf eines der folgenden Suffixe festgelegt werden: au | na | eu |as |la |af |an |ent-au |ent-na |ent-eu |ent-as | ent-la | ent-af | ent-an

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Sie müssen diese Werte mit dem tatsächlichen Bezeichner, der Antwort-URL und der Anmelde-URL aktualisieren. Wenden Sie sich an das [Supportteam für den Deputy-Client](https://www.deputy.com/call-centers-customer-support-scheduling-software), um diese Werte zu erhalten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

1. Die Deputy-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Daher müssen Sie Ihrer Konfiguration der SAML-Tokenattribute benutzerdefinierte Attributzuordnungen hinzufügen. Der folgende Screenshot zeigt die Liste der Standardattribute.

    ![image](common/default-attributes.png)

1. Darüber hinaus erwartet die Deputy-Anwendung, dass in der SAML-Antwort noch einige weitere Attribute zurückgegeben werden (siehe unten). Diese Attribute werden ebenfalls vorab aufgefüllt, Sie können sie jedoch nach Bedarf überprüfen.
    
    | Name |  Quellattribut|
    | -------------- | --------- |
    | Vorname | user.givenname|
    | Nachname | user.surname |

6. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um das Ihrer Anforderung entsprechende **Zertifikat (Base64)** aus den angegebenen Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

7. Kopieren Sie im Abschnitt **Deputy einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Deputy gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Deputy** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-deputy-sso"></a>Konfigurieren des einmaligen Anmeldens für Deputy

1. Melden Sie sich bei Ihrem Deputy-Konto als Administrator an.

1. Klicken Sie in der oberen rechten Ecke auf Ihr Konto, und wählen Sie **Geschäftseinstellungen** aus.

    ![Screenshot: Geschäftseinstellungen](./media/deputy-tutorial/business-settings.png)

1. Klicken Sie dann auf der Registerkarte **Allgemein** auf **SSO-Einstellungen**.

    ![Screenshot: SSO-Einstellungen](./media/deputy-tutorial/general.png)

2. Führen Sie auf der Seite **SSO-Einstellungen** die folgenden Schritte aus.

    ![Einmaliges Anmelden konfigurieren](./media/deputy-tutorial/configuration.png)
    
    a. Klicken Sie auf **Einmaliges Anmelden aktivieren**.
   
    b. Fügen Sie im Textfeld **Anmelde-URL des Identitätsanbieters** die **Anmelde-URL** ein, die Sie im Azure-Portal kopiert haben.

    c. Fügen Sie im Textfeld **Aussteller des Identitätsanbieters** den **Bezeichner (Entitäts-ID)** ein, den Sie im Azure-Portal kopiert haben.

    d. Öffnen Sie das aus dem Azure-Portal heruntergeladene **Zertifikat (Base64)** im Editor, und fügen Sie dessen Inhalt im Textfeld **x.509-Zertifikat** ein.

    e. Aktivieren Sie **Einmaliges Anmelden erforderlich**, wenn die Anmeldung mit einmaligem Anmelden erfolgen soll.
   
    f. Aktivieren Sie die **Just-in-Time-Bereitstellung**, und geben Sie in den Feldern **Vorname** und **Nachname** die Namen der Attribute an, die Sie im Abschnitt **Benutzerattribute und Ansprüche** eingerichtet haben (z. B. `First name` und `Last name`).

    g. Klicken Sie auf **Änderungen übernehmen**.

### <a name="create-deputy-test-user"></a>Erstellen eines Deputy-Testbenutzers

In diesem Abschnitt wird in Deputy ein Benutzer mit dem Namen Britta Simon erstellt. Deputy unterstützt die Just-In-Time-Benutzerbereitstellung (standardmäßig aktiviert). Für Sie steht in diesem Abschnitt kein Aktionselement zur Verfügung. Ist ein Benutzer noch nicht in Deputy vorhanden, wird nach der Authentifizierung ein neuer Benutzer erstellt.

#### <a name="to-add-the-user-manually-perform-the-following-steps"></a>Um manuell einen Benutzer hinzuzufügen, führen Sie die folgenden Schritte aus:

1. Melden Sie sich bei der Deputy-Unternehmenswebsite als Administrator an.

2. Klicken Sie oben im Navigationsbereich auf **Personen**.

3. Klicken Sie auf die Schaltfläche **Personen hinzufügen**, und klicken Sie auf **Einzelne Person hinzufügen**.
   
    ![Add People (Personen hinzufügen)](./media/deputy-tutorial/create-user-1.png "Personen hinzufügen")

4. Führen Sie auf der Registerkarte **Allgemein** die folgenden Schritte aus, um einen Benutzer hinzuzufügen.
   
    ![Neuer Benutzer](./media/deputy-tutorial/create-user-2.png "Neuer Benutzer")

    a. Füllen Sie die Textfelder **Vorname** und **Nachname** aus (z. B. **Britta** und **Simon**).
   
    b. Geben Sie im Textfeld **Firma** den Namen des Unternehmens ein.
   
    c. Klicken Sie auf die Schaltfläche **Save** .

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

#### <a name="sp-initiated"></a>SP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für TeamzSkill weitergeleitet, wo Sie den Anmeldeflow initiieren können.  

* Rufen Sie direkt die TeamzSkill-Anmelde-URL auf, und initiieren Sie den Anmeldeflow.

#### <a name="idp-initiated"></a>IDP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch sollten Sie automatisch bei der TeamzSkill-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. 

Sie können auch den Microsoft-Bereich „Meine Apps“ verwenden, um die Anwendung in einem beliebigen Modus zu testen. Wenn Sie in „Meine Apps“ auf die Kachel „TeamzSkill“ klicken, geschieht Folgendes: Wenn Sie den SP-Modus konfiguriert haben, werden Sie zum Initiieren des Anmeldeflows zur Anmeldeseite der Anwendung weitergeleitet, und wenn Sie den IDP-Modus konfiguriert haben, sollten Sie automatisch bei der TeamzSkill-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).


## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Deputy können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-any-app)
