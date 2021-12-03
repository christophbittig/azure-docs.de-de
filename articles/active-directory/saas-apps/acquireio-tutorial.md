---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit AcquireIO | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und AcquireIO konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/27/2021
ms.author: jeedes
ms.openlocfilehash: 614170bac224b6b10b77bb66d21255a42b84ce10
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132281265"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-acquireio"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit AcquireIO

In diesem Tutorial erfahren Sie, wie Sie AcquireIO in Azure Active Directory (Azure AD) integrieren. Bei der Integration von AcquireIO in Azure AD haben Sie folgende Möglichkeiten:

* Steuern Sie in Azure AD, wer Zugriff auf AcquireIO hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei AcquireIO anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* AcquireIO-Abonnement, für das einmaliges Anmelden (SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* AcquireIO unterstützt **IDP-initiiertes** einmaliges Anmelden.

> [!NOTE]
> Der Bezeichner dieser Anwendung ist ein fester Zeichenfolgenwert, daher kann in einem Mandanten nur eine Instanz konfiguriert werden.

## <a name="add-acquireio-from-the-gallery"></a>Hinzufügen von AcquireIO aus dem Katalog

Zum Konfigurieren der Integration von AcquireIO in Azure AD müssen Sie AcquireIO aus dem Katalog der Liste der verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **AcquireIO** in das Suchfeld ein.
1. Wählen Sie **AcquireIO** im Ergebnisbereich aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-acquireio"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für AcquireIO

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit AcquireIO mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in AcquireIO eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit AcquireIO die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für AcquireIO](#configure-acquireio-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines AcquireIO-Testbenutzers](#create-acquireio-test-user)** , um eine Entsprechung von B. Simon in AcquireIO zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **AcquireIO** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

    ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** den folgenden Schritt aus:

    Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://app.acquire.io/ad/<acquire_account_uid>`.

    > [!NOTE]
    > Dieser Wert entspricht nicht dem tatsächlichen Wert. Sie verwenden die eigentliche Antwort-URL. Dies wird später im Abschnitt **Konfigurieren von AcquireIO** dieses Tutorials beschrieben. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

1. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** zum Eintrag **Zertifikat (Base64)** . Wählen Sie **Herunterladen** aus, um das Zertifikat herunterzuladen, und speichern Sie es auf Ihrem Computer.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

1. Kopieren Sie im Abschnitt **AcquireIO einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf AcquireIO gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste den Eintrag **AcquireIO** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** die entsprechende Rolle für den Benutzer in der Liste aus, und klicken Sie dann im unteren Bildschirmbereich auf die Schaltfläche **Auswählen**.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-acquireio-sso"></a>Konfigurieren des einmaligen Anmeldens für AcquireIO

1. Wenn Sie die Konfiguration in AcquireIO automatisieren möchten, müssen Sie die **Browsererweiterung „Meine Apps“ für die sichere Anmeldung** installieren, indem Sie auf **Erweiterung installieren** klicken.

    ![Erweiterung „Meine Apps“](common/install-myappssecure-extension.png)

1. Klicken Sie nach dem Hinzufügen der Erweiterung zum Browser auf **AcquireIO einrichten**. Dadurch werden Sie zur Anwendung AcquireIO weitergeleitet. Geben Sie dort die Administratoranmeldeinformationen ein, um sich bei AcquireIO anzumelden. Die Browsererweiterung konfiguriert die Anwendung automatisch für Sie und automatisiert die Schritte 3 bis 6.

    ![Einrichtungskonfiguration](common/setup-sso.png)

1. Wenn Sie AcquireIO manuell einrichten möchten, melden Sie sich in einem anderen Webbrowserfenster als Administrator bei AcquireIO an.

1. Klicken Sie im linken Bereich des Menüs auf **App Store**.

    ![Screenshot, in dem der App Store hervorgehoben ist](./media/acquireio-tutorial/menu.png)

1. Scrollen Sie nach unten zu **Active Directory**, und klicken Sie auf **Installieren**.

    ![Screenshot, in dem der Abschnitt „Active Directory“ und die Schaltfläche „Installieren“ hervorgehoben sind](./media/acquireio-tutorial/install-button.png)

1. Führen Sie im Popupfenster „Active Directory“ die folgenden Schritte aus:

    ![Screenshot des Bildschirms „Active Directory“](./media/acquireio-tutorial/configuration.png)

    a. Klicken Sie auf **Kopieren**, um die Antwort-URL für Ihre Instanz zu kopieren, und fügen Sie sie im Azure-Portal im Abschnitt **Grundlegende SAML-Konfiguration** im Textfeld **Antwort-URL** ein.

    b. Fügen Sie in das Textfeld **Anmelde-URL** den Wert der **Anmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    c. Öffnen Sie das Base64-codierte Zertifikat im Editor, kopieren Sie den Inhalt, und fügen Sie ihn im Textfeld **X.509-Zertifikat** ein.

    d. Klicken Sie auf **Jetzt verbinden**.

### <a name="create-acquireio-test-user"></a>Erstellen eines AcquireIO-Testbenutzers

Damit sich Azure AD-Benutzer bei AcquireIO anmelden können, müssen sie in AcquireIO bereitgestellt werden. Im Fall von AcquireIO muss die Bereitstellung manuell ausgeführt werden.

**Führen Sie zum Bereitstellen eines Benutzerkontos die folgenden Schritte aus:**

1. Melden Sie sich in einem anderen Webbrowserfenster als Administrator bei AcquireIO an.

1. Klicken Sie im linken Bereich des Menüs auf **Profiles** (Profile), und navigieren Sie zu **Add Profile** (Profil hinzufügen).

    ![Screenshot, in dem im Menü auf der linken Seite des Bildschirms „Profile“ sowie die Option „Profil hinzufügen“ hervorgehoben sind](./media/acquireio-tutorial/profile.png)

1. Führen Sie auf der Registerkarte **Add customer** (Kunden hinzufügen) die folgenden Schritte aus:

    ![AcquireIO-Konfiguration](./media/acquireio-tutorial/add-profile.png)

    a. Geben Sie im Textfeld **Name** den Namen des Benutzers ein, z. B. **B. Simon**.

    b. Geben Sie im Textfeld **Email** (E-Mail-Adresse) die E-Mail-Adresse des Benutzers ein, z.B. **B.simon@contoso.com** .

    c. Klicken Sie auf **Submit**(Senden).

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen:

* Klicken Sie im Azure-Portal auf „Diese Anwendung testen“. Daraufhin sollten Sie automatisch bei der AcquireIO-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie unter „Meine Apps“ auf die Kachel „AcquireIO“ klicken, sollten Sie automatisch bei der AcquireIO-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von AcquireIO können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-aad)
