---
title: 'Tutorial: Azure Active Directory-Integration mit Fluxx Labs | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Fluxx Labs konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/20/2021
ms.author: jeedes
ms.openlocfilehash: 61d4db378abd9bb4b9284198473ed1d76947f32d
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132285678"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-fluxx-labs"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Fluxx Labs

In diesem Tutorial erfahren Sie, wie Sie Fluxx Labs in Azure Active Directory (Azure AD) integrieren. Die Integration von Fluxx Labs in Azure AD ermöglicht Folgendes:

* Sie können in Azure AD steuern, wer Zugriff auf Fluxx Labs hat.
* Sie können es Ihren Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei Fluxx Labs anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Fluxx Labs-Abonnement, für das einmaliges Anmelden (SSO) aktiviert ist.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Fluxx Labs unterstützt **IDP**-initiiertes einmaliges Anmelden.

## <a name="add-fluxx-labs-from-the-gallery"></a>Hinzufügen von Fluxx Labs aus dem Katalog

Zum Konfigurieren der Integration von Fluxx Labs in Azure AD müssen Sie Fluxx Labs aus dem Katalog zur Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Fluxx Labs** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Fluxx Labs** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-fluxx-labs"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Fluxx Labs

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Fluxx Labs mithilfe einer Testbenutzerin mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Fluxx Labs eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Fluxx Labs die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Fluxx Labs](#configure-fluxx-labs-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
    1. **[Erstellen eines Fluxx Labs-Testbenutzers](#create-fluxx-labs-test-user)** , um eine Entsprechung von B.Simon in Fluxx Labs zu erhalten, die mit der Darstellung des Benutzers in Azure AD verknüpft ist.
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Fluxx Labs** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Führen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** die folgenden Schritte aus:

    a. Geben Sie im Textfeld **Bezeichner** eine URL in einem der folgenden Formate ein:

    | Environment | URL-Muster|
    |-------------|------------|
    | Bereitstellung | `https://<subdomain>.fluxx.io` |
    | Vor der Bereitstellung | `https://<subdomain>.preprod.fluxxlabs.com`|

    b. Geben Sie im Textfeld **Antwort-URL** eine URL in einem der folgenden Formate ein:

    | Environment | URL-Muster|
    |-------------|------------|
    | Bereitstellung | `https://<subdomain>.fluxx.io/auth/saml/callback` |
    | Vor der Bereitstellung | `https://<subdomain>.preprod.fluxxlabs.com/auth/saml/callback`|

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Aktualisieren Sie diese Werte mit dem eigentlichen Bezeichner und der Antwort-URL. Wenden Sie sich an das [Kundensupportteam von Fluxx Labs](https://fluxx.zendesk.com), um diese Werte zu erhalten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

1. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** zum Eintrag **Zertifikat (Base64)** . Wählen Sie **Herunterladen** aus, um das Zertifikat herunterzuladen, und speichern Sie es auf Ihrem Computer.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

1. Kopieren Sie im Abschnitt **Fluxx Labs einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Fluxx Labs gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Fluxx Labs** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-fluxx-labs-sso"></a>Konfigurieren des einmaligen Anmeldens für Fluxx Labs

1. Melden Sie sich in einem anderen Webbrowserfenster an der Fluxx Labs-Unternehmenswebsite als Administrator an.

2. Wählen Sie unten im Abschnitt **Settings** (Einstellungen) die Option **Admin** (Administrator).

    ![Screenshot des Abschnitts „Settings“ mit ausgewählter Option „Admin“](./media/fluxxlabs-tutorial/configure-1.png)

3. Klicken Sie im Verwaltungsbereich auf **Plug-Ins** > **Integrations** (Integrationen) und anschließend auf **SAML SSO-(Disabled)** (SAML-SSO (Deaktiviert)).

    ![Screenshot der Registerkarte „Integrations“ mit ausgewählter Option „SAML SSO - (Disabled)“](./media/fluxxlabs-tutorial/configure-2.png)

4. Führen Sie im Abschnitt mit den Attributen die folgenden Schritte aus:

    ![Screenshot des Abschnitts „Attributes“ mit aktivierter Option „SAML SSO“, in die Felder eingegebenen Werten und ausgewählter Schaltfläche „Save“](./media/fluxxlabs-tutorial/configure-3.png)

    a. Aktivieren Sie das Kontrollkästchen **SAML SSO** (SAML-SSO).

    b. Geben Sie im Textfeld **Request Path** (Anforderungspfad) den Pfad **/auth/saml** ein.

    c. Geben Sie im Textfeld **Callback Path** (Rückrufpfad) den Pfad **/auth/saml/callback** ein.

    d. Geben Sie in das Textfeld **Assertion Consumer Service Url (Single Sign-On URL)** (Assertionsverbraucherdienst-URL (URL für einmaliges Anmelden)) den Wert der **Antwort-URL** ein, den Sie im Azure-Portal eingegeben haben.

    e. Geben Sie in das Textfeld **Audience (SP Entity ID)** (Zielgruppe (SP-Entitäts-ID)) den Wert unter **Bezeichner** ein, den Sie im Azure-Portal eingegeben haben.

    f. Fügen Sie in das Textfeld **Identity Provider SSO Target URL** (URL für einmaliges Anmelden des Identitätsanbieters) den Wert der **Anmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    g. Öffnen Sie das Base64-codierte Zertifikat im Editor, kopieren Sie den Inhalt des Zertifikats in die Zwischenablage, und fügen Sie ihn anschließend in das Textfeld **Zertifikat des Identitätsanbieters** ein.

    h. Geben Sie in das Textfeld **Name identifier Format** (Namensbezeichnerformat) den Wert `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress` ein.

    i. Klicken Sie auf **Speichern**.

    > [!NOTE]
    > Nachdem der Inhalt gespeichert wurde, wird das Feld aus Sicherheitsgründen leer angezeigt, der Wert wurde jedoch in der Konfiguration gespeichert.

### <a name="create-fluxx-labs-test-user"></a>Erstellen eines Fluxx Labs-Testbenutzers

Damit sich Azure AD-Benutzer bei Fluxx Labs anmelden können, müssen sie in Fluxx Labs bereitgestellt werden. Im Fall von Fluxx Labs ist die Bereitstellung eine manuelle Aufgabe.

**Führen Sie zum Bereitstellen eines Benutzerkontos die folgenden Schritte aus:**

1. Melden Sie sich an der Fluxx Labs-Unternehmenswebsite als Administrator an.

2. Klicken Sie auf das unten angezeigte **Symbol**.

    ![Screenshot mit den Administratoroptionen, in dem das „Plus“-Symbol unter „Your Dashboard is Empty“ ausgewählt ist](./media/fluxxlabs-tutorial/configure-6.png)

3. Klicken Sie auf dem Dashboard auf das unten dargestellte Symbol, um die Karte **New PEOPLE** (Neue PERSONEN) zu öffnen.

    ![Screenshot des Menüs „Contact Management“, in dem das „Plus“-Symbol neben „People“ ausgewählt ist](./media/fluxxlabs-tutorial/configure-4.png)

4. Führen Sie im Abschnitt **NEW PEOPLE** (NEUE PERSONEN) die folgenden Schritte aus:

    ![Fluxx Labs-Konfiguration](./media/fluxxlabs-tutorial/configure-5.png)

    a. Fluxx Labs verwendet die E-Mail-Adresse als eindeutigen Bezeichner für SSO-Anmeldenamen. Geben Sie im Feld **SSO UID** (SSO-UID) die E-Mail-Adresse des Benutzers ein, die der Benutzer für die SSO-Anmeldung verwendet.

    b. Klicken Sie auf **Speichern**.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen:

* Klicken Sie im Azure-Portal auf „Diese Anwendung testen“. Dadurch sollten Sie automatisch bei der Fluxx Labs-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie in „Meine Apps“ auf die Kachel „Fluxx Labs“ klicken, sollten Sie automatisch bei der Fluxx Labs-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Fluxx Labs können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-any-app)
