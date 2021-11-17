---
title: "Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit desknet's NEO | Microsoft-Dokumentation"
description: Hier erfahren Sie, wie Sie einmaliges Anmelden zwischen Azure Active Directory und desknet's NEO konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/08/2021
ms.author: jeedes
ms.openlocfilehash: 903c4beeb01899c47aada06f02fc5b24094dd2c3
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132348937"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-desknets-neo"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit desknet's NEO

In diesem Tutorial erfahren Sie, wie Sie desknet's NEO in Azure Active Directory (Azure AD) integrieren. Die Integration von desknet's NEO in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf desknet's NEO hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei desknet's NEO anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein desknet's NEO-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* desknet's NEO unterstützt **SP**-initiiertes einmaliges Anmelden.

## <a name="adding-desknets-neo-from-the-gallery"></a>Hinzufügen von desknet's NEO aus dem Katalog

Zum Konfigurieren der Integration von desknet's NEO in Azure AD müssen Sie desknet's NEO aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **desknet's NEO** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **desknet's NEO** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.


## <a name="configure-and-test-azure-ad-sso-for-desknets-neo"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für desknet's NEO

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit desknet's NEO mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in desknet's NEO eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit desknet's NEO die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für desknet's NEO](#configure-desknets-neo-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines desknet's NEO-Testbenutzers](#create-desknets-neo-test-user)** , um eine Entsprechung von B. Simon in desknet's NEO zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **desknet's NEO** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Geben Sie im Abschnitt **Grundlegende SAML-Konfiguration** die Werte für die folgenden Felder ein:

    a. Geben Sie im Textfeld **Bezeichner (Entitäts-ID)** eine URL im folgenden Format ein: `https://<CUSTOMER_NAME>.dn-cloud.com`.

    b. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://<CUSTOMER_NAME>.dn-cloud.com/cgi-bin/dneo/zsaml.cgi`

    c. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<CUSTOMER_NAME>.dn-cloud.com/cgi-bin/dneo/dneo.cgi`.

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch die tatsächlichen Werte für Bezeichner, Antwort-URL und Anmelde-URL. Diese Werte erhalten Sie vom [Supportteam für den desknet's NEO-Client](mailto:cloudsupport@desknets.com). Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

1. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** zum Eintrag **Zertifikat (Base64)** . Wählen Sie **Herunterladen** aus, um das Zertifikat herunterzuladen, und speichern Sie es auf Ihrem Computer.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

1. Kopieren Sie im Abschnitt **desknet's NEO einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf desknet's NEO gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste den Eintrag **desknet's NEO** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-desknets-neo-sso"></a>Konfigurieren des einmaligen Anmeldens für desknet's NEO

1. Melden Sie sich bei der desknet's NEO-Unternehmenswebsite als Administrator an.

1. Klicken Sie im Menü auf das Symbol **SAML authentication link settings** (Einstellungen für SAML-Authentifizierungslinks).

    ![Screenshot: SAML authentication link settings (Einstellungen für SAML-Authentifizierungslinks)](./media/desknets-neo-tutorial/saml-authentication-icon.png)

1. Klicken Sie unter **Common settings** (Allgemeine Einstellungen) neben „SAML Authentication Collaboration“ (SAML-Authentifizierungszusammenarbeit) auf **use** (Verwenden).

    ![Screenshot: Verwendung der SAML-Authentifizierung](./media/desknets-neo-tutorial/saml-authentication-use.png)

1. Führen Sie im Abschnitt **SAML authentication link settings** (Einstellungen für SAML-Authentifizierungslinks) die folgenden Schritte aus.

    ![Screenshot: Abschnitt „SAML authentication link settings“ (Einstellungen für SAML-Authentifizierungslinks)](./media/desknets-neo-tutorial/saml-authentication.png)

    a. Fügen Sie im Textfeld **Access URL** (Zugriffs-URL) den Wert der **Anmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    b. Fügen Sie in das Textfeld **SP Entity ID** (SP-Entitäts-ID) den Wert für den **Bezeichner** ein, den Sie aus dem Azure-Portal kopiert haben.

    c. Klicken Sie auf **Choose File** (Datei auswählen), um die heruntergeladene Datei mit dem **Zertifikat (Base64)** aus dem Azure-Portal in das Textfeld **x.509 Certificate** (x.509-Zertifikat) hochzuladen.

    d. Klicken Sie auf **change** (Ändern).

### <a name="create-desknets-neo-test-user"></a>Erstellen eines desknet's NEO-Testbenutzers

1. Melden Sie sich bei der desknet's NEO-Unternehmenswebsite als Administrator an.

1. Klicken Sie im **Menü** auf das Symbol **Administrator settings** (Administratoreinstellungen).

    ![Screenshot: Administrator settings (Administratoreinstellungen)](./media/desknets-neo-tutorial/administrator-settings.png)

1. Klicken Sie auf das Symbol **settings** (Einstellungen), und wählen Sie unter **Custom settings** (Benutzerdefinierte Einstellungen) die Option **User management** (Benutzerverwaltung) aus.

    ![Screenshot: Benutzerverwaltungseinstellungen](./media/desknets-neo-tutorial/user-management.png)

1. Klicken Sie auf **Create user information** (Benutzerinformationen erstellen).

    ![Screenshot: Schaltfläche für Benutzerinformationen](./media/desknets-neo-tutorial/create-new-user.png)

1. Füllen Sie auf der folgenden Seite die erforderlichen Felder aus, und klicken Sie auf **creation** (Erstellung).

    ![Screenshot: Abschnitt für die Benutzererstellung](./media/desknets-neo-tutorial/create-new-user-2.png)

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für desknet's NEO weitergeleitet, wo Sie den Anmeldeflow initiieren können. 

* Rufen Sie direkt die desknet's NEO-Anmelde-URL auf, und initiieren Sie den Anmeldeflow.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie in „Meine Apps“ auf die Kachel „desknet's NEO“ klicken, werden Sie zur Anmelde-URL für desknet's NEO weitergeleitet. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).


## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von desknet's NEO können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-any-app)
