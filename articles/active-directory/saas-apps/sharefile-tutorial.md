---
title: 'Tutorial: Azure AD-SSO-Integration in Citrix ShareFile'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Citrix ShareFile konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/13/2021
ms.author: jeedes
ms.openlocfilehash: 1b59f3da3f2bd56a895a0e6b97d6052622e6b51d
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132333465"
---
# <a name="tutorial-azure-ad-sso-integration-with-citrix-sharefile"></a>Tutorial: Azure AD-SSO-Integration in Citrix ShareFile

In diesem Tutorial erfahren Sie, wie Sie Citrix ShareFile in Azure Active Directory (Azure AD) integrieren. Die Integration von Citrix ShareFile in Azure AD ermöglicht Folgendes:

* Steuern in Azure AD, wer Zugriff auf Citrix ShareFile besitzt.
* Benutzer können sich mit ihren Azure AD-Konten automatisch bei Citrix ShareFile anmelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Citrix ShareFile-Abonnement, für das einmaliges Anmelden (SSO) aktiviert ist.

> [!NOTE]
> Diese Integration kann auch über die Azure AD-Umgebung für die US Government-Cloud verwendet werden. Sie finden diese Anwendung im Azure AD-Katalog für US Government-Cloudanwendungen und konfigurieren sie auf die gleiche Weise wie in der öffentlichen Cloud.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Citrix ShareFile unterstützt **SP**-initiiertes einmaliges Anmelden.

## <a name="add-citrix-sharefile-from-the-gallery"></a>Hinzufügen von Citrix ShareFile aus dem Katalog

Zum Konfigurieren der Integration von Citrix ShareFile in Azure AD müssen Sie Citrix ShareFile aus dem Katalog zur Liste der verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Citrix ShareFile** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Citrix ShareFile** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-citrix-sharefile"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Citrix ShareFile

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD bei Citrix ShareFile mithilfe eines Testbenutzers namens **Britta Simon**.
Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Citrix ShareFile eingerichtet werden.

Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD mit Citrix ShareFile die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
2. **[Konfigurieren des einmaligen Anmeldens für Citrix ShareFile](#configure-citrix-sharefile-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines Citrix ShareFile-Testbenutzers](#create-citrix-sharefile-test-user)**, um eine Entsprechung von Britta Simon in Citrix ShareFile zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist.
3. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Citrix ShareFile** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus: 

    a. Geben Sie im Textfeld **Bezeichner (Entitäts-ID)** eine URL in einem der folgenden Formate ein:

    | **Identifier** |
    |--------|
    | `https://<tenant-name>.sharefile.com` |
    | `https://<tenant-name>.sharefile.com/saml/info` |
    | `https://<tenant-name>.sharefile1.com/saml/info` |
    | `https://<tenant-name>.sharefile1.eu/saml/info` |
    | `https://<tenant-name>.sharefile.eu/saml/info` |

    b. Geben Sie im Textfeld **Antwort-URL** eine URL in einem der folgenden Formate ein:
    
    | **Antwort-URL** |
    |-------|
    | `https://<tenant-name>.sharefile.com/saml/acs` |
    | `https://<tenant-name>.sharefile.eu/saml/<URL path>` |
    | `https://<tenant-name>.sharefile.com/saml/<URL path>` |

    c. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<tenant-name>.sharefile.com/saml/login`

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Sie müssen diese Werte mit dem tatsächlichen Bezeichner, der Antwort-URL und Anmelde-URL aktualisieren. Wenden Sie sich an das [Clientsupportteam von Citrix ShareFile](https://www.citrix.co.in/products/citrix-content-collaboration/support.html), um diese Werte zu erhalten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

4. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um das Ihrer Anforderung entsprechende **Zertifikat (Base64)** aus den angegebenen Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

6. Kopieren Sie im Abschnitt **Citrix ShareFile einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Citrix ShareFile gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Liste der Anwendungen **Citrix ShareFile** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-citrix-sharefile-sso"></a>Konfigurieren des einmaligen Anmeldens für Citrix ShareFile

1. Wenn Sie die Konfiguration in **Citrix ShareFile** automatisieren möchten, müssen Sie die **Browsererweiterung „Meine Apps“ für die sichere Anmeldung** installieren, indem Sie auf **Erweiterung installieren** klicken.

    ![Erweiterung „Meine Apps“](common/install-myappssecure-extension.png)

2. Klicken Sie nach dem Hinzufügen der Erweiterung zum Browser auf **Citrix ShareFile einrichten**. Sie werden dann zur Anwendung Citrix ShareFile weitergeleitet. Geben Sie dort die Administratoranmeldeinformationen ein, um sich bei Citrix ShareFile anzumelden. Die Browsererweiterung konfiguriert die Anwendung automatisch für Sie und automatisiert die Schritte 3 bis 7.

    ![Einrichtungskonfiguration](common/setup-sso.png)

3. Wenn Sie Citrix ShareFile manuell einrichten möchten, melden Sie sich in einem anderen Webbrowserfenster als Administrator bei der Citrix ShareFile-Unternehmenswebsite an.

1. Klicken Sie auf dem **Dashboard** auf **Settings** (Einstellungen), und wählen Sie **Admin Settings** (Administratoreinstellungen) aus.

    ![Verwaltung](./media/sharefile-tutorial/settings.png)

1. Navigieren Sie in den Administratoreinstellungen zu **Security** -> **Login & Security Policy** (Sicherheit > Anmelde- und Sicherheitsrichtlinie).
   
    ![Account Administration (Kontoverwaltung)](./media/sharefile-tutorial/settings-security.png "Kontoverwaltung")

1. Führen Sie auf der Dialogseite **Einmaliges Anmelden/SAML 2.0-Konfiguration** unter **Grundeinstellungen** die folgenden Schritte aus:
   
    ![Einmaliges Anmelden](./media/sharefile-tutorial/saml-configuration.png "Einmaliges Anmelden")
   
    a. Wählen Sie **Ja** für **Enable SAML** (SAML aktivieren) aus.

    b. Kopieren Sie den Wert für **ShareFile Issuer/ Entity ID** (ShareFile-Aussteller/Entitäts-ID), und fügen Sie ihn im Azure-Portal im Dialogfeld **Grundlegende SAML-Konfiguration** in das Feld **Bezeichner-URL** ein.
    
    c. Fügen Sie in das Textfeld **IDP-Aussteller/Entitäts-ID** den Wert von **Azure AD-Bezeichner** ein, den Sie aus dem Azure-Portal kopiert haben.

    d. Klicken Sie auf **Ändern** neben dem Feld **X.509-Zertifikat**, und laden Sie dann das aus dem Azure-Portal heruntergeladene Zertifikat hoch.
    
    e. Fügen Sie in das Textfeld **Login URL** (Anmelde-URL) den Wert der **Anmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.
    
    f. Fügen Sie in das Textfeld **Logout URL** (Abmelde-URL) den Wert der **Abmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    g. Wählen Sie unter **Optional Settings** (Optionale Einstellungen) für **SP-Initiated Auth Context** (SP-initiierter Authentifizierungskontext) die Optionen **User Name and Password** (Benutzername und Kennwort) und **Exact** (Genau) aus.

5. Klicken Sie auf **Speichern**.

## <a name="create-citrix-sharefile-test-user"></a>Erstellen eines Citrix ShareFile-Testbenutzers

1. Melden Sie sich bei Ihrem **Citrix ShareFile**-Mandanten an.

2. Klicken Sie auf **People** -> **Manage Users Home** -> **Create New Users** -> **Create Employee** (Personen > Benutzerbasis verwalten > Neue Benutzer erstellen > Mitarbeiter erstellen).
   
    ![Create Employee (Mitarbeiter erstellen)](./media/sharefile-tutorial/create-user.png "Create Employee")

3. Führen Sie unter **Grundlegende Informationen** die folgenden Schritte aus:
   
    ![Basic Information (Grundlegende Informationen)](./media/sharefile-tutorial/user-form.png "Basic Information")
   
    a. Geben Sie im Textfeld **Vorname** den **Vornamen** des Benutzers ein, z.B. **Britta**.
   
    b.  Geben Sie im Textfeld **Nachname** den **Nachnamen** des Benutzers ein, z.B. **Simon**.
   
    c. Geben Sie im Textfeld **Email Address** (E-Mail-Adresse) die E-Mail-Adresse von Britta Simon ein (Beispiel: **brittasimon\@contoso.com**).

4. Klicken Sie auf **Benutzer hinzufügen**.
  
    >[!NOTE]
    >Der Azure AD-Kontoinhaber erhält eine E-Mail mit einem Link zur Bestätigung des Kontos, bevor dieses aktiviert wird. Sie können Azure AD-Benutzerkonten auch mithilfe anderer Tools zum Erstellen von Citrix ShareFile-Benutzerkonten oder mithilfe der von Citrix ShareFile bereitgestellten APIs erstellen.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für Citrix ShareFile weitergeleitet, wo Sie den Anmeldeflow initiieren können.

* Rufen Sie direkt die Citrix ShareFile-Anmelde-URL auf, und initiieren Sie den Anmeldeflow.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie unter „Meine Apps“ auf die Kachel „Citrix ShareFile“ klicken, werden Sie zur Anmelde-URL für Citrix ShareFile umgeleitet. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Citrix ShareFile können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-any-app)
