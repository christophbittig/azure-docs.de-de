---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit ekarda | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie einmaliges Anmelden zwischen Azure Active Directory und ekarda konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/24/2020
ms.author: jeedes
ms.openlocfilehash: e5b0abc3cd6aa0850f48bced115f0b43d326b081
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132285960"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ekarda"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit ekarda

In diesem Tutorial erfahren Sie, wie Sie ekarda in Azure Active Directory (Azure AD) integrieren. Die Integration von ekarda in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf ekarda hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihrem Azure AD-Konto automatisch bei ekarda anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein ekarda-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* ekarda unterstützt SP- und IDP-initiiertes einmaliges Anmelden.
* ekarda unterstützt die Just-In-Time-Benutzerbereitstellung.

## <a name="add-ekarda-from-the-gallery"></a>Hinzufügen von ekarda aus dem Katalog

Fügen Sie zum Konfigurieren der Integration von ekarda in Azure AD ekarda über den Katalog zur Liste mit den verwalteten SaaS-Apps hinzu:

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.

1. Wählen Sie im linken Bereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen der neuen Anwendung die Option **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **ekarda** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **ekarda** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-ekarda"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für ekarda

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit ekarda mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in ekarda eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit ekarda die folgenden Schritte aus:

1. [Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso), um Ihren Benutzern die Verwendung dieses Features zu ermöglichen

    1. [Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user), um das einmalige Anmelden von Azure AD mit B.Simon zu testen
    1. [Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user), um B.Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen
1. [Konfigurieren des einmaligen Anmeldens für ekarda](#configure-ekarda-sso), um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    * [Erstellen eines ekarda-Testbenutzers](#create-an-ekarda-test-user), um eine Entsprechung von B. Simon in ekarda zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
1. [Testen des einmaligen Anmeldens](#test-sso), um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Führen Sie die folgenden Schritte im Azure-Portal aus, um das einmalige Anmelden von Azure AD zu aktivieren:

1. Melden Sie sich beim Azure-Portal an.
1. Navigieren Sie auf der Anwendungsintegrationsseite für **ekarda** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Wählen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** das Stiftsymbol aus, um die Einstellungen für **Grundlegende SAML-Konfiguration** zu bearbeiten.

   ![Screenshot: Seite „Einmaliges Anmelden (SSO) mit SAML einrichten“ mit hervorgehobenem Stiftsymbol](common/edit-urls.png)

1. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus, wenn die **Dienstanbieter-Metadatendatei** angezeigt wird:
    1. Wählen Sie **Metadatendatei hochladen** aus.
    1. Wählen Sie zum Auswählen der Metadatendatei das Ordnersymbol und dann **Hochladen** aus.
    1. Nachdem die Metadatendatei erfolgreich hochgeladen wurde, werden die Werte **Bezeichner** und **Antwort-URL** automatisch im Textfeld des ekarda-Abschnitts angezeigt.

    > [!Note]
    > Sollten die Werte **Bezeichner** und **Antwort-URL** nicht automatisch angezeigt werden, geben Sie die erforderlichen Werte manuell ein.

1. Wenn Sie keine **Metadatendatei des Dienstanbieters** im Abschnitt **Grundlegende SAML-Konfiguration** sehen und die Anwendung im IDP-initiierten Modus konfigurieren möchten, geben Sie Werte in die folgenden Felder ein:

    1. Geben Sie im Textfeld **Bezeichner** eine URL im folgenden Format ein: `https://my.ekarda.com/users/saml_metadata/<COMPANY_ID>`.
    1. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://my.ekarda.com/users/saml_acs/<COMPANY_ID>`.

1. Wenn Sie die Anwendung im SP-initiierten Modus konfigurieren möchten, wählen Sie **Zusätzliche URLs festlegen** aus, und führen Sie den folgenden Schritt aus:

    Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://my.ekarda.com/users/saml_sso/<COMPANY_ID>`.

    > [!NOTE]
    > Die Werte in den beiden vorherigen Schritten sind nicht real. Aktualisieren Sie sie mit den tatsächlichen Werten für Bezeichner, Antwort-URL und Anmelde-URL. Diese Werte erhalten Sie vom [Supportteam für den ekarda-Client](mailto:contact@ekarda.com). Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

1. Wählen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** die Option **Herunterladen** aus, um das **Zertifikat (Base64)** auf Ihrem Computer zu speichern.

    ![Screenshot: Abschnitt „SAML-Signaturzertifikat“ auf der Seite „Einmaliges Anmelden (SSO) mit SAML einrichten“ mit hervorgehobenem Downloadlink für das Base64-Zertifikat](common/certificatebase64.png)

1. Kopieren Sie im Abschnitt **ekarda einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Screenshot: Abschnitt „ekarda einrichten“ auf der Seite „Einmaliges Anmelden (SSO) mit SAML einrichten“ mit hervorgehobenen Links zum Kopieren der URLs](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

In diesem Abschnitt erstellen Sie im Azure-Portal einen Testbenutzer mit dem Namen B. Simon.

1. Wählen Sie im Azure-Portal im linken Bereich **Azure Active Directory** > **Benutzer** > **Alle Benutzer** aus.

1. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.
1. Führen Sie unter den Eigenschaften für **Benutzer** die folgenden Schritte aus:
   1. Geben Sie im Feld **Name** die Zeichenfolge `B.Simon` ein.  
   1. Geben Sie im Feld **Benutzername** die Zeichenfolge username@companydomain.extension ein. Geben Sie z. B. `B.Simon@contoso.com` ein
   1. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**, und notieren Sie sich den im Feld **Kennwort** angezeigten Wert.
   1. Klicken Sie auf **Erstellen**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf ekarda gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **ekarda** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.

1. Wählen Sie die Schaltfläche **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste der Benutzer die Option **B.Simon** aus. Wählen Sie anschließend am unteren Bildschirmrand **Auswählen** aus.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Wählen Sie im Dialogfeld **Zuweisung hinzufügen** die Option **Zuweisen** aus.

## <a name="configure-ekarda-sso"></a>Konfigurieren des einmaligen Anmeldens von ekarda

1. Wenn Sie die Konfiguration in ekarda automatisieren möchten, müssen Sie die **Browsererweiterung „Meine Apps“ für die sichere Anmeldung** installieren, indem Sie auf **Erweiterung installieren** klicken.

    ![Erweiterung „Meine Apps“](common/install-myappssecure-extension.png)

2. Klicken Sie nach dem Hinzufügen der Erweiterung zum Browser auf **ekarda einrichten**, um zur Anwendung ekarda weitergeleitet zu werden. Geben Sie dort die Administratoranmeldeinformationen ein, um sich bei ekarda anzumelden. Die Browsererweiterung konfiguriert die Anwendung automatisch für Sie und automatisiert die Schritte 3 bis 6.

    ![Einrichtungskonfiguration](common/setup-sso.png)

3. Wenn Sie ekarda manuell einrichten möchten, melden Sie sich in einem anderen Webbrowserfenster als Administrator bei der ekarda-Unternehmenswebsite an.

1. Wählen Sie **Admin** > **My Account** (Verwaltung > Mein Konto) aus.

    ![Screenshot: Benutzeroberfläche der ekarda-Website mit hervorgehobener Option „My Account“ (Mein Konto) im Verwaltungsmenü](./media/ekarda-tutorial/ekarda.png)

1. Navigieren Sie unten auf der Seite zum Abschnitt **SAML SETTINGS** (SAML-EINSTELLUNGEN). In diesem Abschnitt Konfigurieren Sie die SAML-Integration.
1. Gehen Sie im Abschnitt **SAML SETTINGS** (SAML-EINSTELLUNGEN) wie folgt vor:

    ![Screenshot: Seite „SAML SETTINGS“ (SAML-EINSTELLUNGEN) von ekarda mit hervorgehobenen Feldern für die SAML-Konfiguration](./media/ekarda-tutorial/ekarda1.png)

    1. Wählen Sie den Link **Service Provider metadata** (Metadaten des Dienstanbieters) aus, und speichern Sie sie als Datei auf Ihrem Computer.
    1. Aktivieren Sie das Kontrollkästchen **Enable SAML** (SAML aktivieren).
    1. Fügen Sie im Feld **IDP Entity ID** (IDP-Entitäts-ID) den Wert für **Azure AD-Bezeichner** ein, den Sie zuvor aus dem Azure-Portal kopiert haben.
    1. Fügen Sie im Feld **IDP Login URL** (IDP-Anmelde-URL) den Wert für **Anmelde-URL** ein, den Sie zuvor aus dem Azure-Portal kopiert haben.
    1. Fügen Sie im Feld **IDP Logout URL** (IDP-Abmelde-URL) den Wert für **Abmelde-URL** ein, den Sie zuvor aus dem Azure-Portal kopiert haben.
    1. Öffnen Sie im Editor die Datei **Certificate (Base64)** (Zertifikat (Base64)), die Sie aus dem Azure-Portal heruntergeladen haben. Fügen Sie den Inhalt in das Textfeld **IDP x509 Certificate** (IDP-x509-Zertifikat) ein.
    1. Aktivieren Sie im Abschnitt **OPTIONS** (OPTIONEN) das Kontrollkästchen **Enable SLO** (SLO aktivieren).
    1. Wählen Sie **Update** aus.

### <a name="create-an-ekarda-test-user"></a>Erstellen eines ekarda-Testbenutzers

In diesem Abschnitt wird in ekarda ein Benutzer namens B. Simon erstellt. ekarda unterstützt die Just-In-Time-Benutzerbereitstellung, die standardmäßig aktiviert ist. In diesem Abschnitt müssen keine Aktionen ausgeführt werden. Ist noch kein Benutzer namens B. Simon in ekarda vorhanden, wird nach der Authentifizierung ein neuer Benutzer erstellt.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen:

#### <a name="sp-initiated"></a>SP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für ekarda weitergeleitet, wo Sie den Anmeldeflow initiieren können.

* Rufen Sie direkt die ekarda-Anmelde-URL auf, und initiieren Sie den Anmeldeflow.

#### <a name="idp-initiated"></a>IDP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch sollten Sie automatisch bei der ekarda-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben.

Sie können auch den Microsoft-Bereich „Meine Apps“ verwenden, um die Anwendung in einem beliebigen Modus zu testen. Beim Klicken auf die Kachel „ekarda“ in „Meine Apps“ geschieht Folgendes: Wenn Sie die Anwendung im SP-Modus konfiguriert haben, werden Sie zum Initiieren des Anmeldeflows zur Anmeldeseite der Anwendung weitergeleitet. Wenn Sie die Anwendung im IDP-Modus konfiguriert haben, sollten Sie automatisch bei der ekarda-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von ekarda können Sie die Sitzungssteuerung erzwingen. Diese schützt in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten. Die Sitzungssteuerung basiert auf der App-Steuerung für bedingten Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-any-app)
