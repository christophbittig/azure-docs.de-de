---
title: 'Tutorial: Azure Active Directory-Integration mit Coupa | Microsoft Docs'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Coupa konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/05/2021
ms.author: jeedes
ms.openlocfilehash: 682a49c23f3d3c9e567955b892772a6b07f22a9d
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132338628"
---
# <a name="tutorial-azure-active-directory-integration-with-coupa"></a>Tutorial: Azure Active Directory-Integration mit Coupa

In diesem Tutorial erfahren Sie, wie Sie Coupa in Azure Active Directory (Azure AD) integrieren. Die Integration von Coupa in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf Coupa hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Coupa anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Coupa-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Coupa unterstützt **SP-initiiertes** einmaliges Anmelden.

## <a name="add-coupa-from-the-gallery"></a>Hinzufügen von Coupa aus dem Katalog

Zum Konfigurieren der Integration von Coupa in Azure AD müssen Sie Coupa aus dem Katalog der Liste der verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Coupa** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Coupa** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-coupa"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Coupa

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Coupa mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Coupa eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Coupa die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Coupa](#configure-coupa-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines Coupa-Testbenutzers](#create-coupa-test-user)** , um ein Pendant von B. Simon in Coupa zu erhalten, das mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Coupa** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    a. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<companyname>.coupahost.com`

    > [!NOTE]
    > Der Wert der Anmelde-URL entspricht nicht dem tatsächlichen Wert. Ersetzen Sie diesen Wert durch die tatsächliche Anmelde-URL. Wenden Sie sich an das [Clientsupportteam von Coupa](https://success.coupa.com/Support/Contact_Us?), um diesen Wert zu erhalten.

    b. Geben Sie im Feld **Bezeichner** die folgende URL ein: .

    | Environment  | URL |
    |:-------------|----|
    | Sandbox | `sso-stg1.coupahost.com`|
    | Bereitstellung | `sso-prd1.coupahost.com`|
    | | |

    c. Geben Sie im Textfeld **Antwort-URL** die folgende URL ein: .

    | Environment | URL |
    |------------- |----|
    | Sandbox | `https://sso-stg1.coupahost.com/sp/ACS.saml2`|
    | Bereitstellung | `https://sso-prd1.coupahost.com/sp/ACS.saml2`|
    | | |

4. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um den Ihren Anforderungen entsprechenden **Verbundmetadaten-XML**-Code aus den verfügbaren Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

6. Kopieren Sie im Abschnitt **Coupa einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Coupa gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste die Option **Coupa** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-coupa-sso"></a>Konfigurieren des einmaligen Anmeldens für Coupa

1. Melden Sie sich bei der Coupa-Unternehmenswebsite als Administrator an.

2. Navigieren Sie zu **Einrichtung \> Sicherheitskontrollen**.

    ![Security Controls (Sicherheitskontrollen)](./media/coupa-tutorial/setup.png "Einstellungen für einmaliges Anmelden")

3. Führen Sie im Abschnitt **Log in using Coupa credentials** die folgenden Schritte aus:

    ![Coupa-SP-Metadaten](./media/coupa-tutorial/login.png "Coupa-SP-Metadaten")

    a. Wählen Sie **Log in using SAML** aus.

    b. Klicken Sie auf **Durchsuchen**, um die Metadaten hochzuladen, die Sie zuvor aus dem Azure-Portal heruntergeladen haben.

    c. Klicken Sie auf **Speichern**.

### <a name="create-coupa-test-user"></a>Erstellen eines Coupa-Testbenutzers

Damit sich Azure AD-Benutzer bei Coupa anmelden können, müssen sie in Coupa bereitgestellt werden.  

* Im Fall von Coupa ist die Bereitstellung eine manuelle Aufgabe.

**Um die Benutzerbereitstellung zu konfigurieren, führen Sie die folgenden Schritte durch:**

1. Melden Sie sich bei Ihrer **Coupa**-Unternehmenswebsite als Administrator an.

2. Klicken Sie auf der oben angezeigten Symbolleiste auf **Setup**, und klicken Sie dann auf **Users**.

    ![Benutzer](./media/coupa-tutorial/user.png "Benutzer")

3. Klicken Sie auf **Erstellen**.

    ![Erstellen von Benutzern](./media/coupa-tutorial/create.png "Benutzer erstellen")

4. Führen Sie im Abschnitt **User Create** die folgenden Schritte aus:

    ![User Details (Benutzerdetails)](./media/coupa-tutorial/details.png "User Details")

    a. Geben Sie die Attributwerte von **Login**, **First Name**, **Last Name**, **Single Sign-On ID** und **Email** eines gültigen Azure Active Directory-Kontos, das Sie bereitstellen möchten, in die entsprechenden Textfelder ein.

    b. Klicken Sie auf **Erstellen**.

    >[!NOTE]
    >Der Besitzer des Azure Active Directory-Kontos erhält eine E-Mail mit einem Link zur Bestätigung des Kontos, bevor es aktiv wird.
    >

>[!NOTE]
>Sie können Azure AD-Benutzerkonten auch mit anderen Tools zum Erstellen von Coupa-Benutzerkonten oder mit den APIs von Coupa bereitstellen.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für Coupa weitergeleitet, wo Sie den Anmeldeflow initiieren können. 

* Navigieren Sie direkt zur Anmelde-URL für Coupa, und initiieren Sie den Anmeldeflow.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie unter „Meine Apps“ auf die Kachel „Coupa“ klicken, sollten Sie automatisch bei der Coupa-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Coupa können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-any-app)
