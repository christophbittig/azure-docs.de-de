---
title: 'Tutorial: Azure Active Directory-Integration mit Hosted Graphite | Microsoft Docs'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Hosted Graphite konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/11/2021
ms.author: jeedes
ms.openlocfilehash: 185838b45f656e1b28b25749b215ab992d039131
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132291364"
---
# <a name="tutorial-azure-active-directory-integration-with-hosted-graphite"></a>Tutorial: Azure Active Directory-Integration mit Hosted Graphite

In diesem Tutorial erfahren Sie, wie Sie Hosted Graphite in Azure Active Directory (Azure AD) integrieren. Die Integration von Hosted Graphite in Azure AD ermöglicht Folgendes:

* Sie können in Azure AD steuern, wer Zugriff auf Hosted Graphite hat.
* Sie können es Ihren Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei Hosted Graphite anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Hosted Graphite-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Hosted Graphite unterstützt **SP- und IDP-initiiertes** einmaliges Anmelden.
* Hosted Graphite unterstützt die **Just-in-Time**-Benutzerbereitstellung.

## <a name="add-hosted-graphite-from-the-gallery"></a>Hinzufügen von Hosted Graphite aus dem Katalog

Zum Konfigurieren der Integration von Hosted Graphite in Azure AD müssen Sie Hosted Graphite aus dem Katalog der Liste der verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Hosted Graphite** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Hosted Graphite** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-hosted-graphite"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Hosted Graphite

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Hosted Graphite mithilfe eines Testbenutzers namens **B.Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Hosted Graphite eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Hosted Graphite die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Hosted Graphite](#configure-hosted-graphite-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines Hosted Graphite-Testbenutzers](#create-hosted-graphite-test-user)** , um in Hosted Graphite eine Entsprechung von B. Simon zu erhalten, die mit der Benutzerdarstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Hosted Graphite** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus, wenn Sie die Anwendung im **IDP**-initiierten Modus konfigurieren möchten:

    a. Geben Sie im Textfeld **Bezeichner** eine URL im folgenden Format ein: `https://www.hostedgraphite.com/metadata/<USER_ID>`

    b. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://www.hostedgraphite.com/complete/saml/<USER_ID>`

5. Klicken Sie auf **Zusätzliche URLs festlegen**, und führen Sie den folgenden Schritt aus, wenn Sie die Anwendung im **SP-initiierten Modus** konfigurieren möchten:

    Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://www.hostedgraphite.com/login/saml/<USER_ID>/`

    > [!NOTE]
    > Hinweis: Hierbei handelt es sich um Beispielwerte. Sie müssen diese Werte durch den tatsächlichen Bezeichner und die tatsächliche Antwort- und Abmelde-URL ersetzen. Um diese Werte abzurufen, können Sie auf der Seite Ihrer Anwendung zu „Access -> SAML Setup“ wechseln oder sich an das [Hosted Graphite-Supportteam](mailto:help@hostedgraphite.com) wenden.

6. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um das Ihrer Anforderung entsprechende **Zertifikat (Base64)** aus den angegebenen Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

7. Kopieren Sie im Abschnitt **Hosted Graphite einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie Zugriff auf Hosted Graphite gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Hosted Graphite** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-hosted-graphite-sso"></a>Konfigurieren des einmaligen Anmeldens für Hosted Graphite

1. Melden Sie sich bei Ihrem Hosted Graphite-Mandanten als Administrator an.

2. Wechseln Sie über die Randleiste zur Seite **SAML Setup** (**Access -> SAML Setup**).

    ![Screenshot: Menü „Access“ (Zugriff) mit ausgewählter Option „SAML Setup“ (SAML-Einrichtung)](./media/hostedgraphite-tutorial/setup.png)

3. Vergewissern Sie sich, dass diese URLs Ihrer Konfiguration entsprechen, die Sie im Azure-Portal im Abschnitt **Grundlegende SAML-Konfiguration** durchgeführt haben.

    ![Screenshot: Grundlegende SAML-Konfiguration](./media/hostedgraphite-tutorial/configuration.png)

4. Fügen Sie in den Textfeldern **Entity or Issuer ID** (Entitäts- oder Aussteller-ID) und **SSO-Anmelde-URL** die Werte von **Azure AD-Bezeichner** bzw. **Anmelde-URL** ein, die Sie im Azure-Portal kopiert haben.

    ![Screenshot: Einträge für den Identitätsanbieter](./media/hostedgraphite-tutorial/integration.png)

5. Wählen Sie für **Default User Role** (Standardbenutzerrolle) die Option **Schreibgeschützt**.

    ![Screenshot: schreibgeschützte Standardbenutzerrolle](./media/hostedgraphite-tutorial/role.png)

6. Öffnen Sie das Base64-codierte Zertifikat im Editor, das Sie aus dem Azure-Portal heruntergeladen haben, kopieren Sie den Inhalt des Zertifikats in die Zwischenablage, und fügen Sie ihn anschließend in das Textfeld **X.509-Zertifikat** ein.

    ![Screenshot: X.509-Zertifikat](./media/hostedgraphite-tutorial/certificate.png)

7. Klicken Sie auf die Schaltfläche **Save** .

### <a name="create-hosted-graphite-test-user"></a>Erstellen eines Hosted Graphite-Testbenutzers

In diesem Abschnitt wird in Hosted Graphite ein Benutzer mit dem Namen „Britta Simon“ erstellt. Hosted Graphite unterstützt die Just-in-Time-Benutzerbereitstellung, die standardmäßig aktiviert ist. Für Sie steht in diesem Abschnitt kein Aktionselement zur Verfügung. Wenn ein Benutzer in Hosted Graphite noch nicht vorhanden ist, wird nach der Authentifizierung ein neuer Benutzer erstellt.

> [!NOTE]
> Setzen Sie sich mit dem [Supportteam von Hosted Graphite](mailto:help@hostedgraphite.com) in Verbindung, wenn Sie einen Benutzer manuell erstellen müssen.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

#### <a name="sp-initiated"></a>SP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für Hosted Graphite weitergeleitet, wo Sie den Anmeldeflow initiieren können.  

* Navigieren Sie direkt zur Anmelde-URL von Hosted Graphite, und initiieren Sie dort den Anmeldeflow.

#### <a name="idp-initiated"></a>IDP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch sollten Sie automatisch bei der Hosted Graphite-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. 

Sie können auch den Microsoft-Bereich „Meine Apps“ verwenden, um die Anwendung in einem beliebigen Modus zu testen. Beim Klicken auf die Kachel „Hosted Graphite“ in „Meine Apps“ geschieht Folgendes: Wenn Sie die Anwendung im SP-Modus konfiguriert haben, werden Sie zum Initiieren des Anmeldeflows zur Anmeldeseite der Anwendung weitergeleitet. Wenn Sie die Anwendung im IDP-Modus konfiguriert haben, sollten Sie automatisch bei der Hosted Graphite-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Hosted Graphite können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-aad)
