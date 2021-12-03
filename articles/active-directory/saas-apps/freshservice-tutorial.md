---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory in Freshservice | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Freshservice konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/31/2021
ms.author: jeedes
ms.openlocfilehash: 21e57d891569e21932a68eb53776d2348dfde5ef
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132348633"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-freshservice"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory in Freshservice

In diesem Tutorial erfahren Sie, wie Sie Freshservice in Azure Active Directory (Azure AD) integrieren. Bei der Integration von Freshservice in Azure AD haben Sie folgende Möglichkeiten:

* Steuern Sie in Azure AD, wer Zugriff auf Freshservice hat.
* Ermöglichen Sie Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Freshservice anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Freshservice-Abonnement, für das einmaliges Anmelden (SSO) aktiviert ist.

> [!NOTE]
> Diese Integration kann auch über die Azure AD-Umgebung für die US Government-Cloud verwendet werden. Sie finden diese Anwendung im Azure AD-Katalog für US Government-Cloudanwendungen und konfigurieren sie auf die gleiche Weise wie in der öffentlichen Cloud.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Freshservice unterstützt **SP**-initiiertes einmaliges Anmelden.
* Freshservice unterstützt [automatisierte Benutzerbereitstellung](freshservice-provisioning-tutorial.md).

## <a name="add-freshservice-from-the-gallery"></a>Hinzufügen von Freshservice aus dem Katalog

Zum Konfigurieren der Integration von Freshservice in Azure AD müssen Sie Freshservice aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Freshservice** in das Suchfeld ein.
1. Wählen Sie **Freshservice** im Ergebnisbereich aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-freshservice"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Freshservice

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD bei Freshservice mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Freshservice eingerichtet werden.

Zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD bei Freshservice müssen Sie die folgenden Schritte ausführen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Freshservice](#configure-freshservice-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
    1. **[Erstellen eines Freshservice-Testbenutzers](#create-freshservice-test-user)** , um eine Entsprechung von B. Simon in Freshservice zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist.
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Freshservice** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Geben Sie im Abschnitt **Grundlegende SAML-Konfiguration** die Werte für die folgenden Felder ein:

    a. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<company-name>.freshservice.com`

    b. Geben Sie im Textfeld **Bezeichner (Entitäts-ID)** eine URL im folgenden Format ein: `https://<company-name>.freshservice.com`.

    c. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://<company-name>.freshservice.com/login/saml`
    
    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch die tatsächlichen Werte für die Anmelde-URL, den Bezeichner und die Antwort-URL. Wenden Sie sich an das [Clientsupportteam von Freshservice](https://support.freshservice.com/), um diese Werte zu erhalten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

1. Suchen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** den Eintrag **Zertifikat (Base64)** . Klicken Sie auf **Herunterladen**, um das Zertifikat herunterzuladen, und speichern Sie es auf Ihrem Computer.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

1. Kopieren Sie im **Azure-Portal** im Abschnitt **Freshservice einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Freshservice gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste den Eintrag **Freshservice** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-freshservice-sso"></a>Konfigurieren des einmaligen Anmeldens für Freshservice

1. Wenn Sie die Konfiguration in Freshservice automatisieren möchten, müssen Sie die **Browsererweiterung „Meine Apps“ für die sichere Anmeldung** installieren, indem Sie auf **Erweiterung installieren** klicken.

    ![Erweiterung „Meine Apps“](common/install-myappssecure-extension.png)

1. Klicken Sie nach dem Hinzufügen der Erweiterung zum Browser auf **Freshservice einrichten**, um zur Anwendung Freshservice weitergeleitet zu werden. Geben Sie dort die Administratoranmeldeinformationen ein, um sich bei Freshservice anzumelden. Die Browsererweiterung konfiguriert die Anwendung automatisch für Sie und automatisiert die Schritte 3 bis 6.

    ![Einrichtungskonfiguration](common/setup-sso.png)

1. Wenn Sie Freshservice manuell einrichten möchten, melden Sie sich bei der Freshservice-Unternehmenswebsite als Administrator an.

1. Klicken Sie im Menü auf der linken Seite auf **Admin** (Verwaltung), und wählen Sie unter **General Settings** (Allgemeine Einstellungen) die Option **Helpdesk Security** (Helpdesksicherheit) aus.

    ![Administrator](./media/freshservice-tutorial/configure-1.png "Admin")

1. Klicken Sie unter **Security** (Sicherheit) auf **Go to Freshservice 360 Security** (Zu Freshservice 360-Sicherheit wechseln).

    ![Security](./media/freshservice-tutorial/configure-2.png "Sicherheit")

1. Führen Sie im Abschnitt **Sicherheit** die folgenden Schritte aus:

    ![Einmaliges Anmelden](./media/freshservice-tutorial/configure-3.png "Einmaliges Anmelden")
  
    a. Wählen Sie unter **Single Sign On** (Einmaliges Anmelden) die Einstellung **On** (Ein) aus.

    b. Wählen Sie unter **Login Method** (Anmeldemethode) die Option **SAML SSO** (SAML-SSO) aus.

    c. Fügen Sie im Textfeld **Entity ID provided by the IdP** (Vom IdP bereitgestellte Entitäts-ID) den Wert der **Entitäts-ID** ein, den Sie aus dem Azure-Portal kopiert haben.

    d. Fügen Sie im Textfeld **SAML SSO URL** (SAML-SSO-URL) den Wert der **Anmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    e. Wählen Sie unter **Signing Options** (Signaturoptionen) im Dropdownmenü die Option **Only Signed Assertions** (Nur signierte Assertionen) aus.

    f. Fügen Sie im Textfeld **Abmelde-URL** den Wert der **Abmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    g. Fügen Sie im Textfeld **Security Certificate** (Sicherheitszertifikat) den Wert für **Zertifikat (Base64)** ein, den Sie zuvor erhalten haben.
  
    h. Klicken Sie auf **Speichern**.


## <a name="create-freshservice-test-user"></a>Erstellen eines Freshservice-Testbenutzers

Damit sich Azure AD-Benutzer bei Freshservice anmelden können, müssen sie in Freshservice bereitgestellt werden. Im Fall von FreshService ist die Bereitstellung eine manuelle Aufgabe.

**Führen Sie zum Bereitstellen eines Benutzerkontos die folgenden Schritte aus:**

1. Melden Sie sich bei der **Freshservice** -Unternehmenswebsite als Administrator an.

2. Klicken Sie im Menü auf der linken Seite auf **Admin** (Verwaltung).

3. Klicken Sie im Abschnitt **Benutzerverwaltung** auf **Anfordernde Personen**.

    ![Anfordernde Personen](./media/freshservice-tutorial/create-user-1.png "Anfordernde Personen")

4. Klicken Sie auf **Neue anfordernde Person**.

    ![Neue anfordernde Personen](./media/freshservice-tutorial/create-user-2.png "Neue anfordernde Personen")

5. Füllen Sie im Abschnitt **New Requester** (Neuer Anforderer) die erforderlichen Felder aus, und klicken Sie auf **Save** (Speichern).
    ![Neue anfordernde Person](./media/freshservice-tutorial/create-user-3.png "Neue anfordernde Person")  

    > [!NOTE]
    > Der Besitzer des Azure Active Directory-Kontos erhält eine E-Mail mit einem Link zur Bestätigung des Kontos, bevor es aktiv wird.
    >  

    > [!NOTE]
    > Sie können Azure AD-Benutzerkonten auch mit anderen Tools zum Erstellen von FreshService-Benutzerkonten oder mit den APIs von FreshService bereitstellen.
   
> [!NOTE]
>Außerdem unterstützt Freshservice automatische Benutzerbereitstellung. Weitere Informationen zum Konfigurieren der automatischen Benutzerbereitstellung finden Sie [hier](./freshservice-provisioning-tutorial.md).

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für Freshservice weitergeleitet, wo Sie den Anmeldeflow initiieren können. 

* Rufen Sie direkt die Freshservice-Anmelde-URL auf, und initiieren Sie den Anmeldeflow.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie unter „Meine Apps“ auf die Kachel „Freshservice“ klicken, sollten Sie automatisch bei der Freshservice-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

 Nach dem Konfigurieren von Freshservice können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-any-app)
