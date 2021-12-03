---
title: 'Tutorial: Azure Active Directory-Integration mit Egnyte | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Egnyte konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/05/2021
ms.author: jeedes
ms.openlocfilehash: a2ac510b28a81ffc086daf9f97e4bbe86ffb69e4
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132314324"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-egnyte"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Egnyte

In diesem Tutorial erfahren Sie, wie Sie Egnyte in Azure Active Directory (Azure AD) integrieren. Die Integration von Egnyte in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf Egnyte hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Egnyte anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Egnyte-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Egnyte unterstützt **SP**-initiiertes einmaliges Anmelden.

> [!NOTE]
> Der Bezeichner dieser Anwendung ist ein fester Zeichenfolgenwert, daher kann in einem Mandanten nur eine Instanz konfiguriert werden.

## <a name="add-egnyte-from-the-gallery"></a>Hinzufügen von Egnyte aus dem Katalog

Zum Konfigurieren der Integration von Egnyte in Azure AD müssen Sie Egnyte aus dem Katalog der Liste der verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Egnyte** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Egnyte** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-egnyte"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Egnyte

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Egnyte mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Egnyte eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Egnyte die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Egnyte](#configure-egnyte-sso)**, um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines Egnyte-Testbenutzers](#create-egnyte-test-user)**, um ein Pendant von B. Simon in Egnyte zu erhalten, das mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Egnyte** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    a. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<companyname>.egnyte.com`

    b. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://<companyname>.egnyte.com/samlconsumer/AzureAD`
    
    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch die tatsächliche Anmelde-URL und Antwort-URL. Den Wert erhalten Sie vom [Supportteam für den Egnyte-Client](https://www.egnyte.com/corp/contact_egnyte.html). Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

4. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um das Ihrer Anforderung entsprechende **Zertifikat (Base64)** aus den angegebenen Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

5. Kopieren Sie im Abschnitt **Egnyte einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B.Simon die Verwendung von Azure-SSO, indem Sie den Zugriff auf Egnyte gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Egnyte** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-egnyte-sso"></a>Konfigurieren des einmaligen Anmeldens für Egnyte

1. Melden Sie sich in einem anderen Webbrowserfenster bei der Egnyte-Unternehmenswebsite als Administrator an.

2. Klicken Sie auf **Einstellungen**.
   
    ![Einstellungen 1](./media/egnyte-tutorial/settings-tab.png "Einstellungen")

3. Klicken Sie im Menü auf **Settings**.

    ![Menü 1](./media/egnyte-tutorial/menu-tab.png "Menü")

4. Klicken Sie auf die Registerkarte **Configuration**, und klicken Sie dann auf **Security**.

    ![Security](./media/egnyte-tutorial/configuration.png "Sicherheit")

5. Führen Sie im Abschnitt **Single Sign-On Authentication** die folgenden Schritte aus:

    ![Single Sign On Authentication (SSO-Authentifizierung)](./media/egnyte-tutorial/authentication.png "SSO-Authentifizierung")   
    
    1. Klicken Sie unter **Single sign-on authentication** auf **SAML 2.0**.

    1. Wählen Sie als **Identity Provider** den Wert **AzureAD** aus.

    1. Fügen Sie die **Anmelde-URL**, die Sie aus dem Azure-Portal kopiert haben, in das Textfeld **Anmelde-URL des Identitätsanbieters** ein.

    1. Fügen Sie den **Azure AD-Bezeichner**, den Sie aus dem Azure-Portal kopiert haben, in das Textfeld **Identity provider entity ID** (Entitäts-ID des Identitätsanbieters) ein.

    1. Öffnen Sie das Base64-codierte Zertifikat im Editor, das Sie aus dem Azure-Portal heruntergeladen haben, kopieren Sie den Inhalt des Zertifikats in die Zwischenablage, und fügen Sie ihn anschließend in das Textfeld **Identitätsanbieterzertifikat** ein.

    1. Wählen Sie als **Default user mapping** den Typ **Email address** aus.

    1. Wählen Sie als **Use domain-specific issuer value** den Eintrag **disabled** aus.

    1. Klicken Sie auf **Speichern**.

### <a name="create-egnyte-test-user"></a>Erstellen eines Egnyte-Testbenutzers

Damit sich Azure AD-Benutzer bei Egnyte anmelden können, müssen sie in Egnyte bereitgestellt werden. Im Fall von Egnyte ist die Bereitstellung eine manuelle Aufgabe.

**So stellen Sie Benutzerkonten bereit:**

1. Melden Sie sich bei der **Egnyte**-Unternehmenswebsite als Administrator an.

2. Navigieren Sie zu **Einstellungen \> Benutzer und Gruppen**.

3. Klicken Sie auf **Add New User**, und wählen Sie dann den Typ des Benutzers aus, den Sie hinzufügen möchten.
   
    ![Benutzer](./media/egnyte-tutorial/add-user.png "Benutzer")

4. Führen Sie im Abschnitt **New Power User** (Neuer Hauptbenutzer) die folgenden Schritte aus:
    
    ![Neuer Standardbenutzer](./media/egnyte-tutorial/new-user.png "Neuer Standardbenutzer")   

    a. Geben Sie im Textfeld **E-Mail** die E-Mail-Adresse des Benutzers ein, z. B. **brittasimon\@contoso.com**.

    b. Geben Sie in das Textfeld **Benutzername** den Namen des Benutzers (**Brittasimon**) ein.

    c. Wählen Sie unter **Authentifizierungstyp** die Option **Einmaliges Anmelden** aus.
   
    d. Klicken Sie auf **Speichern**.
    
    >[!NOTE]
    >Der Besitzer des Azure Active Directory-Kontos erhält eine Benachrichtigungs-E-Mail.
    >

>[!NOTE]
>Sie können Azure AD-Benutzerkonten auch mit anderen Tools zum Erstellen von Egnyte-Benutzerkonten oder mit den APIs von Egnyte bereitstellen.
>

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für Egnyte weitergeleitet, wo Sie den Anmeldeflow initiieren können. 

* Rufen Sie direkt die Egnyte-Anmelde-URL auf, und initiieren Sie den Anmeldeflow.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie in „Meine Apps“ auf die Kachel „Egnyte“ klicken, werden Sie zur Anmelde-URL für Egnyte weitergeleitet. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Egnyte können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-any-app)
