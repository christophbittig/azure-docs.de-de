---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD mit AWS Single Sign-on'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und AWS Single Sign-on konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/26/2021
ms.author: jeedes
ms.openlocfilehash: b6154d6542e0f61326c31aeca7abc15d671935ea
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132336371"
---
# <a name="tutorial-azure-ad-sso-integration-with-aws-single-sign-on"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD mit AWS Single Sign-on

In diesem Tutorial erfahren Sie, wie Sie AWS Single Sign-on in Azure Active Directory (Azure AD) integrieren. Die Integration von AWS Single Sign-on in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf AWS Single Sign-on hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei AWS Single Sign-on anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* AWS Single Sign-on-Abonnement, das für das einmalige Anmelden (Single Sign-On, SSO) aktiviert ist.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* AWS Single Sign-on unterstützt **SP- und IDP-initiiertes** einmaliges Anmelden.

* AWS Single Sign-on unterstützt die [**automatisierte Benutzerbereitstellung**](./aws-single-sign-on-provisioning-tutorial.md).

## <a name="adding-aws-single-sign-on-from-the-gallery"></a>Hinzufügen von AWS Single Sign-on aus dem Katalog

Zum Konfigurieren der Integration von AWS Single Sign-on in Azure AD müssen Sie AWS Single Sign-on aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** im Suchfeld den Suchbegriff **AWS Single Sign-on** ein.
1. Wählen Sie im Ergebnisbereich **AWS Single Sign-on** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.


## <a name="configure-and-test-azure-ad-sso-for-aws-single-sign-on"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für AWS Single Sign-on

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD für AWS Single Sign-on mithilfe eines Testbenutzers mit dem Namen **B.Simon**. Damit das einmalige Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in AWS Single Sign-on eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für AWS Single Sign-on die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für AWS Single Sign-on](#configure-aws-single-sign-on-sso)** , um die Einstellungen für das einmalige Anmelden auf der Anwendungsseite zu konfigurieren.
    1. **[Erstellen eines AWS Single Sign-on-Testbenutzers](#create-aws-single-sign-on-test-user)** , um eine Entsprechung von B.Simon in AWS Single Sign-on zu erhalten, die mit der Darstellung des Benutzers in Azure AD verknüpft ist.
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **AWS Single Sign-on** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Wenn Sie über eine **Dienstanbieter-Metadatendatei** verfügen, führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    a. Klicken Sie auf **Metadatendatei hochladen**.

    b. Klicken Sie auf das **Ordnerlogo**, um die Metadatendatei auszuwählen, deren Download im Abschnitt **[Konfigurieren des einmaligen Anmeldens für AWS Single Sign-on](#configure-aws-single-sign-on-sso)** erläutert wird, und klicken Sie auf **Hinzufügen**.

    ![Bild2](common/browse-upload-metadata.png)

    c. Nach dem erfolgreichen Upload der Metadatendatei werden die Werte unter **Bezeichner** und **Antwort-URL** im Abschnitt „Grundlegende SAML-Konfiguration“ automatisch eingefügt.

    ![image3](common/idp-intiated.png)

    > [!Note]
    > Falls die Werte **Bezeichner** und **Antwort-URL** nicht automatisch aufgefüllt werden, geben Sie die erforderlichen Werte manuell ein.

1. Wenn Sie keine **Dienstanbieter-Metadatendatei** haben und die Anwendung im **IdP**-initiierten Modus konfigurieren möchten, führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    a. Geben Sie im Textfeld **Bezeichner** eine URL im folgenden Format ein: `https://<REGION>.signin.aws.amazon.com/platform/saml/<ID>`

    b. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://<REGION>.signin.aws.amazon.com/platform/saml/acs/<ID>`

1. Klicken Sie auf **Zusätzliche URLs festlegen**, und führen Sie den folgenden Schritt aus, wenn Sie die Anwendung im **SP-initiierten Modus** konfigurieren möchten:

    Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://portal.sso.<REGION>.amazonaws.com/saml/assertion/<ID>`

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Sie müssen diese Werte mit dem tatsächlichen Bezeichner, der Antwort-URL und der Anmelde-URL aktualisieren. Wenden Sie sich an das [Clientsupportteam von AWS Single Sign-on](mailto:aws-sso-partners@amazon.com), um diese Werte zu erhalten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

1. Die AWS Single Sign-on-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Daher müssen Sie Ihrer Konfiguration der SAML-Tokenattribute benutzerdefinierte Attributzuordnungen hinzufügen. Der folgende Screenshot zeigt die Liste der Standardattribute.

    ![image](common/edit-attribute.png)


    > [!NOTE]
    > Wenn ABAC in AWS SSO aktiviert ist, können die zusätzlichen Attribute als Sitzungstags direkt an die AWS-Konten übergeben werden.

1. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** zum Eintrag **Zertifikat (Base64)** . Wählen Sie **Herunterladen** aus, um das Zertifikat herunterzuladen, und speichern Sie es auf Ihrem Computer.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

1. Kopieren Sie im Abschnitt **AWS Single Sign-on einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B.Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie dem Benutzer Zugriff auf AWS Single Sign-on gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste den Eintrag **AWS Single Sign-on** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-aws-single-sign-on-sso"></a>Konfigurieren von AWS Single Sign-on (AWS SSO)

1. Wenn Sie die Konfiguration in AWS Single Sign-on automatisieren möchten, müssen Sie die **Browsererweiterung „Meine Apps“ für die sichere Anmeldung** installieren, indem Sie auf **Erweiterung installieren** klicken.

    ![Erweiterung „Meine Apps“](common/install-myappssecure-extension.png)

2. Klicken Sie nach dem Hinzufügen der Erweiterung zum Browser auf **AWS Single Sign-on einrichten**, um zur Anwendung AWS Single Sign-on weitergeleitet zu werden. Geben Sie dort die Administratoranmeldeinformationen ein, um sich bei AWS Single Sign-on anzumelden. Die Browsererweiterung konfiguriert die Anwendung automatisch für Sie und automatisiert die Schritte 3 bis 10.

    ![Einrichtungskonfiguration](common/setup-sso.png)

3. Wenn Sie AWS Single Sign-on manuell einrichten möchten, melden Sie sich in einem anderen Webbrowserfenster als Administrator bei der AWS Single Sign-on-Unternehmenswebsite an.

1. Navigieren Sie zu **Services > Security, Identity, & Compliance > AWS Single Sign-On** (Dienste > Sicherheit, Identität und Compliance > AWS Single Sign-On).
2. Klicken Sie im linken Navigationsbereich auf **Settings** (Einstellungen).
3. Suchen Sie auf der Seite **Settings** (Einstellungen) nach **Identity source** (Identitätsquelle), und klicken Sie auf **Change** (Ändern).

    ![Screenshot : Ändern der Identitätsquelle](./media/aws-single-sign-on-tutorial/settings.png)

4. Wählen Sie unter „Change identity source“ (Identitätsquelle ändern) die Option **External identity provider** (Externer Identitätsanbieter) aus.

    
    ![Screenshot: Ausgewählter Abschnitt für den externen Identitätsanbieter](./media/aws-single-sign-on-tutorial/external-identity-provider.png)


1. Führen Sie im Abschnitt **Configure external identity provider** (Externen Identitätsanbieter konfigurieren) die folgenden Schritte aus:

    ![Screenshot: Abschnitt zum Herunter- und Hochladen von Metadaten](./media/aws-single-sign-on-tutorial/upload-metadata.png)

    a. Suchen Sie im Abschnitt **Service provider metadata** (Dienstanbieter-Metadaten) nach **AWS SSO SAML metadata** (AWS SSO SAML-Metadaten). Wählen Sie **Download metadata file** (Metadatendatei herunterladen) aus, um die Metadatendatei herunterzuladen, und speichern Sie die Datei auf Ihrem Computer. Laden Sie diese Metadatendatei im Azure-Portal hoch.

    b. Kopieren Sie den Wert im Feld **AWS SSO Sign-in URL** (AWS SSO-Anmelde-URL), und fügen Sie ihn im Azure-Portal im Abschnitt **Grundlegende SAML-Konfiguration** in das Textfeld **Anmelde-URL** ein.

    c. Klicken Sie im Abschnitt **Identity provider metadata** (Identitätsanbieter-Metadaten) auf **Browse** (Durchsuchen), um die Metadatendatei hochzuladen, die Sie aus dem Azure-Portal heruntergeladen haben.

    d. Klicken Sie auf **Next: Review** (Weiter: Überprüfen).

8. Geben Sie in das Textfeld **ACCEPT** (AKZEPTIEREN) ein, um die Identitätsquelle zu ändern.

    ![Screenshot: Bestätigen der Konfiguration](./media/aws-single-sign-on-tutorial/accept.png)

9. Klicken Sie auf **Change identity source** (Identitätsquelle ändern).

### <a name="create-aws-single-sign-on-test-user"></a>Erstellen eines AWS Single Sign-on-Testbenutzers

1. Öffnen Sie die **AWS-SSO-Konsole**.

2. Klicken Sie im linken Navigationsbereich auf **Users** (Benutzer).

3. Wählen Sie auf der Seite „Users“ (Benutzer) die Option **Add user** (Benutzer hinzufügen) aus.

4. Führen Sie auf der Seite „Add user“ (Benutzer hinzufügen) die folgenden Schritte aus:

    a. Geben Sie im Feld **User name** (Benutzername) „B.Simon“ ein.

    b. Geben Sie im Feld **Email address** (E-Mail-Adresse) die Adresse ein (`username@companydomain.extension`). Beispiel: `B.Simon@contoso.com`.

    c. Geben Sie im Feld **Confirm email address** (E-Mail-Adresse bestätigen) die E-Mail-Adresse aus dem vorherigen Schritt erneut ein.

    d. Geben Sie im Feld „First name“ (Vorname) `Jane` ein.

    e. Geben Sie im Feld „Last name“ (Nachname) `Doe` ein.

    f. Geben Sie im Feld „Display name“ (Anzeigename) `Jane Doe` ein.

    g. Klicken Sie auf **Next: Groups** (Weiter: Gruppen).

    > [!NOTE]
    > Stellen Sie sicher, dass der in AWS SSO eingegebene Benutzername mit dem Anmeldenamen des Benutzers in Azure AD übereinstimmt. So vermeiden Sie mögliche Authentifizierungsprobleme.

5. Wählen Sie **Benutzer hinzufügen** aus.
6. Als Nächstes weisen Sie den Benutzer Ihrem AWS-Konto zu. Dazu wählen Sie im linken Navigationsbereich der AWS SSO-Konsole die Option **AWS accounts** (AWS-Konten) aus.
7. Auf der Seite „AWS accounts“ (AWS-Konten) wählen Sie die Registerkarte Ihrer AWS-Organisation aus und aktivieren das Kontrollkästchen neben dem AWS-Konto, dem Sie den Benutzer zuweisen möchten. Klicken Sie dann auf **Assign users** (Benutzer zuweisen).
8. Aktivieren Sie auf der Seite „Assign users“ (Benutzer zuweisen) das Kontrollkästchen neben „B.Simon“. Klicken Sie dann auf **Next: Permission sets** (Weiter: Berechtigungssätze).
9. Aktivieren Sie unterhalb des Abschnitts zum Auswählen von Berechtigungssätzen das Kontrollkästchen neben dem Berechtigungssatz, den Sie B.Simon zuweisen möchten. Wenn kein Berechtigungssatz vorhanden ist, wählen Sie **Create new permission set** (Neuen Berechtigungssatz erstellen) aus.

    > [!NOTE]
    > Berechtigungen definieren die Zugriffsebene, über die Benutzer und Gruppen für ein AWS-Konto verfügen. Weitere Informationen zu Berechtigungssätzen finden Sie auf der Seite **Berechtigungssätze** der AWS SSO-Dokumentation.
10. Klicken Sie auf **Fertig stellen**.

> [!NOTE]
> AWS Single Sign-on unterstützt auch die automatische Benutzerbereitstellung. Weitere Informationen zum Konfigurieren der automatischen Benutzerbereitstellung finden Sie [hier](./aws-single-sign-on-provisioning-tutorial.md).

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

#### <a name="sp-initiated"></a>SP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für AWS Single Sign-on weitergeleitet, wo Sie den Anmeldeflow initiieren können.  

* Rufen Sie die AWS Single Sign-on-Anmelde-URL direkt auf, und initiieren Sie den Anmeldeflow.

#### <a name="idp-initiated"></a>IDP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch sollten Sie automatisch bei der AWS Single Sign-on-Instanz angemeldet werden, für die Sie das einmalige Anmelden eingerichtet haben. 

Sie können auch den Microsoft-Bereich „Meine Apps“ verwenden, um die Anwendung in einem beliebigen Modus zu testen. Beim Klicken auf die Kachel „AWS Single Sign-on“ in „Meine Apps“ geschieht Folgendes: Wenn Sie den SP-Modus konfiguriert haben, werden Sie zum Initiieren des Anmeldeflows zur Anmeldeseite der Anwendung weitergeleitet. Wenn Sie den IdP-Modus konfiguriert haben, sollten Sie automatisch bei der Instanz von AWS Single Sign-on angemeldet werden, für die Sie das einmalige Anmelden eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).


## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von AWS Single Sign-on können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Daten Ihrer Organisation schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-any-app)
