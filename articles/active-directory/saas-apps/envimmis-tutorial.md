---
title: 'Tutorial: Azure Active Directory-Integration mit Envi MMIS | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Envi MMIS konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/12/2021
ms.author: jeedes
ms.openlocfilehash: ec135c2f117a01ac5438b4c2ab22af5174b910fd
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132296554"
---
# <a name="tutorial-azure-active-directory-integration-with-envi-mmis"></a>Tutorial: Azure Active Directory-Integration mit Envi MMIS

In diesem Tutorial erfahren Sie, wie Sie Envi MMIS in Azure Active Directory (Azure AD) integrieren. Die Integration von Envi MMIS in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer auf Envi MMIS Zugriff hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Envi MMIS anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Envi MMIS-Abonnement, für das einmaliges Anmelden aktiviert ist.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Envi MMIS unterstützt **SP**- und **IDP**-initiiertes einmaliges Anmelden.

## <a name="add-envi-mmis-from-the-gallery"></a>Hinzufügen von Envi MMIS aus dem Katalog

Zum Konfigurieren der Integration von Envi MMIS in Azure AD müssen Sie Envi MMIS aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Envi MMIS**  in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Envi MMIS** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-envi-mmis"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Envi MMIS

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Envi MMIS mithilfe eines Testbenutzers namens **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Envi MMIS eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Envi MMIS die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Envi MMIS](#configure-envi-mmis-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines Envi MMIS-Testbenutzers](#create-envi-mmis-test-user)** , um eine Entsprechung von B. Simon in Envi MMIS zu erhalten, die mit der Darstellung des Benutzers in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Envi MMIS** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus, wenn Sie die Anwendung im **IDP**-initiierten Modus konfigurieren möchten:

    1. Geben Sie im Textfeld **Bezeichner** eine URL im folgenden Format ein: `https://www.<CUSTOMER DOMAIN>.com/Account`

    1. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://www.<CUSTOMER DOMAIN>.com/Account/Acs`

5. Klicken Sie auf **Zusätzliche URLs festlegen**, und führen Sie den folgenden Schritt aus, wenn Sie die Anwendung im **SP-initiierten Modus** konfigurieren möchten:

    Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://www.<CUSTOMER DOMAIN>.com/Account`

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Sie müssen diese Werte mit dem tatsächlichen Bezeichner, der Antwort-URL und der Anmelde-URL aktualisieren. Wenden Sie sich an das [Clientsupportteam von Envi MMIS](mailto:support@ioscorp.com), um diese Werte zu erhalten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

6. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um den Ihren Anforderungen entsprechenden **Verbundmetadaten-XML**-Code aus den verfügbaren Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

7. Kopieren Sie im Abschnitt **Envi MMIS einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie Zugriff auf Envi MMIS gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste den Eintrag **Envi MMIS** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-envi-mmis-sso"></a>Konfigurieren des einmaligen Anmeldens für Envi MMIS

1. Melden Sie sich in einem anderen Webbrowserfenster bei der Envi MMIS-Website als Administrator an.

2. Klicken Sie auf die Registerkarte **My Domain**.

    ![Screenshot des Menüs „Benutzer“, in dem „My Domain“ ausgewählt ist](./media/envimmis-tutorial/domain.png)

3. Klicken Sie auf **Bearbeiten**.

    ![Screenshot der ausgewählten Schaltfläche „Hinzufügen“](./media/envimmis-tutorial/edit-icon.png)

4. Aktivieren Sie das Kontrollkästchen **Use remote authentication** (Remoteauthentifizierung verwenden), und wählen Sie dann **HTTP Redirect** (HTTP-Umleitung) in der Dropdownliste **Authentication Type** (Authentifizierungstyp) aus.

    ![Screenshot der Registerkarte „Details“, auf der „Use remote authentication“ aktiviert und „H T T P Redirect“ ausgewählt ist](./media/envimmis-tutorial/details.png)

5. Wählen Sie die Registerkarte **Resources** (Ressourcen) aus, und klicken Sie dann auf **Upload Metadata** (Metadaten hochladen).

    ![Screenshot der Registerkarte „Ressourcen“, auf der die Aktion „Upload Metadata“ ausgewählt ist](./media/envimmis-tutorial/metadata.png)

6. Führen Sie im Popupmenü **Upload Metadata** (Metadaten hochladen) die folgenden Schritte aus:

    ![Screenshot des Popupmenüs „Upload Metadata“, in dem die Option „Datei“ ausgewählt und das Symbol „Datei auswählen“ und die Schaltfläche „OK“ hervorgehoben sind](./media/envimmis-tutorial/file.png)

    1. Wählen Sie im Dropdownmenü **Upload from** (Hochladen aus) die Option **File** (Datei) aus.

    1. Laden Sie die heruntergeladene Metadatendatei aus dem Azure-Portal hoch, indem Sie auf das Symbol **Choose File** (Datei auswählen) klicken.

    1. Klicken Sie auf **OK**.

7. Nach dem Hochladen der heruntergeladenen Metadatendatei werden die Felder automatisch aufgefüllt. Klicken Sie auf **Aktualisieren**.

    ![Schaltfläche „Speichern“ beim Konfigurieren des einmaligen Anmeldens](./media/envimmis-tutorial/fields.png)

### <a name="create-envi-mmis-test-user"></a>Erstellen eines Envi MMIS-Testbenutzers

Damit sich Azure AD-Benutzer bei Envi MMIS anmelden können, müssen sie in Envi MMIS bereitgestellt werden. Im Fall von Envi MMIS ist die Bereitstellung eine manuelle Aufgabe.

**Führen Sie zum Bereitstellen eines Benutzerkontos die folgenden Schritte aus:**

1. Melden Sie sich bei der Envi MMIS-Unternehmenswebsite als Administrator an.

2. Klicken Sie auf die Registerkarte **User List** (Benutzerliste).

    ![Screenshot des Menüs „User“, in dem „User List“ ausgewählt ist](./media/envimmis-tutorial/list.png)

3. Klicken Sie auf die Schaltfläche **Add User** (Benutzer hinzufügen).

    ![Screenshot des Abschnitts „Users“, in dem die Schaltfläche „Add User“ ausgewählt ist](./media/envimmis-tutorial/user.png)

4. Führen Sie im Abschnitt **Add User** die folgenden Schritte aus:

    ![Screenshot: Hinzufügen eines Mitarbeiters.](./media/envimmis-tutorial/add-user.png)

    1. Geben Sie im Textfeld **User Name** (Benutzername) den Benutzernamen des Kontos von Britta Simon ein. (Beispiel: **brittasimon\@contoso.com**)
    
    1. Geben Sie im Textfeld **First Name** (Vorname) den Vornamen von Britta Simon ein: **Britta**.

    1. Geben Sie im Textfeld **Last Name** (Nachname) den Nachnamen von Britta Simon ein: **Simon**.

    1. Geben Sie im Textfeld **Title** (Titel) den Titel des Benutzers ein.

    1. Geben Sie im Textfeld **Email Address** (E-Mail-Adresse) die E-Mail-Adresse des Kontos von Britta Simon ein. (Beispiel: **brittasimon\@contoso.com**)

    1. Geben Sie im Textfeld **SSO User Name** (Benutzername für einmaliges Anmelden) den Benutzernamen des Kontos von Britta Simon ein. (Beispiel: **brittasimon\@contoso.com**)

    1. Klicken Sie auf **Speichern**.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

#### <a name="sp-initiated"></a>SP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für Envi MMIS weitergeleitet, wo Sie den Anmeldeflow initiieren können.  

* Navigieren Sie direkt zur Anmelde-URL für Envi MMIS, und initiieren Sie dort den Anmeldeflow.

#### <a name="idp-initiated"></a>IDP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch sollten Sie automatisch bei der Envi MMIS-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. 

Sie können auch den Microsoft-Bereich „Meine Apps“ verwenden, um die Anwendung in einem beliebigen Modus zu testen. Beim Klicken auf die Kachel „Envi MMIS“ in „Meine Apps“ geschieht Folgendes: Wenn Sie die Anwendung im SP-Modus konfiguriert haben, werden Sie zum Initiieren des Anmeldeflows zur Anmeldeseite der Anwendung weitergeleitet. Wenn Sie die Anwendung im IDP-Modus konfiguriert haben, sollten Sie automatisch bei der Envi MMIS-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Envi MMIS können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-aad)
