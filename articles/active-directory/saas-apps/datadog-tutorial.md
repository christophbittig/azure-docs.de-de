---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Datadog | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie einmaliges Anmelden zwischen Azure Active Directory und Datadog konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/11/2021
ms.author: jeedes
ms.openlocfilehash: 23bdf05960af9eb94a2b003af113e7d66c4ea69e
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132338343"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-datadog"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Datadog

In diesem Tutorial erfahren Sie, wie Sie Datadog in Azure Active Directory (Azure AD) integrieren. Die Integration von Datadog in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf Datadog hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihrem Azure AD-Konto automatisch bei Datadog anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Datadog-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Datadog unterstützt **SP- und IDP-initiiertes** einmaliges Anmelden.

## <a name="add-datadog-from-the-gallery"></a>Hinzufügen von Datadog über den Katalog

Zum Konfigurieren der Integration von Datadog in Azure AD müssen Sie Datadog aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Datadog** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Datadog** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-datadog"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Datadog

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Datadog mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Datadog eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Datadog die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Datadog](#configure-datadog-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. Erstellen eines Datadog-Testbenutzers , um eine Entsprechung von B. Simon in Datadog zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Datadog** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.

1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

1. Im Abschnitt **Grundlegende SAML-Konfiguration** muss der Benutzer keine Aktionen ausführen, weil die Anwendung bereits in Azure integriert ist.

1. Klicken Sie auf **Zusätzliche URLs festlegen**, und führen Sie den folgenden Schritt aus, wenn Sie die Anwendung im **SP-initiierten** Modus konfigurieren möchten:

    Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://app.datadoghq.com/account/login/id/<CUSTOM_IDENTIFIER>`

    > [!NOTE]
    > Dieser Wert entspricht nicht dem tatsächlichen Wert. Aktualisieren Sie den Wert mit der tatsächlichen Anmelde-URL in Ihren [Datadog-SAML-Einstellungen](https://app.datadoghq.com/organization-settings/login-methods/saml). Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen. Für die gemeinsame Verwendung der IdP-initiierten Anmeldung und der SP-initiierten Anmeldung müssen beide Versionen der ACS-URL in Azure konfiguriert sein.

1. Klicken Sie auf **Speichern**.

1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** unter **Benutzerattribute und Ansprüche** auf das Stiftsymbol, um die Einstellungen zu bearbeiten.

1. Klicken Sie auf die Schaltfläche **Gruppenanspruch hinzufügen**. In Azure AD ist der Gruppenanspruchsname standardmäßig eine URL. Beispiel: `http://schemas.microsoft.com/ws/2008/06/identity/claims/groups`). Wenn Sie dies in einen Anzeigenamenswert wie **Gruppen** ändern möchten, wählen Sie **Erweiterte Optionen** aus, und ändern Sie dann den Namen des Gruppenanspruchs in **Gruppen**.

   > [!NOTE]
   > Das Quellattribut ist auf `Group ID` festgelegt. Dies ist die UUID der Gruppe in Azure AD. In diesem Fall wird die Gruppen-ID von Azure AD als Gruppenanspruchsattribut-Wert und nicht als Gruppenname gesendet. Sie müssen Zuordnungen in Datadog ändern, damit die Zuordnung zur Gruppen-ID und nicht zum Gruppennamen erfolgt. Weitere Informationen finden Sie unter [Datadog-SAML-Zuordnungen](https://docs.datadoghq.com/account_management/saml/#mapping-saml-attributes-to-datadog-roles).

1. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** zu **Verbundmetadaten-XML**, und wählen Sie **Herunterladen** aus, um das Zertifikat herunterzuladen und auf Ihrem Computer zu speichern.

1. Kopieren Sie im Abschnitt **Datadog einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Datadog gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Datadog** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-datadog-sso"></a>Konfigurieren des einmaligen Anmeldens für Datadog

Zum Konfigurieren des einmaligen Anmeldens aufseiten von **Datadog** müssen Sie die heruntergeladene **Verbundmetadaten-XML-Datei** in die [Datadog-SAML-Einstellungen](https://app.datadoghq.com/organization-settings/login-methods/saml) hochladen.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

Testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen. 

#### <a name="sp-initiated"></a>SP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für Datadog weitergeleitet, wo Sie den Anmeldeflow initiieren können.  

* Rufen Sie direkt die Datadog-Anmelde-URL auf, und starten Sie den Anmeldeflow.

#### <a name="idp-initiated"></a>IDP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch sollten Sie automatisch bei der Datadog-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. 

Sie können auch den Microsoft-Bereich „Meine Apps“ verwenden, um die Anwendung in einem beliebigen Modus zu testen. Beim Klicken auf die Kachel „Datadog“ im Portal „Meine Apps“ geschieht Folgendes: Wenn Sie die Anwendung im SP-Modus konfiguriert haben, werden Sie zum Initiieren des Anmeldeflows zur Anmeldeseite der Anwendung weitergeleitet. Wenn Sie die Anwendung im IDP-Modus konfiguriert haben, sollten Sie automatisch bei der Datadog-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in der [Einführung in das Portal „Meine Apps“](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

### <a name="enable-all-users-from-your-tenant-to-authenticate-with-the-app"></a>Ermöglichen der Authentifizierung mit der App für alle Benutzer in Ihrem Mandanten

In diesem Abschnitt ermöglichen Sie allen Benutzern in Ihrem Mandanten den Zugriff auf Datadog, wenn ein Benutzer über ein Konto aufseiten von Datadog verfügt.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Datadog** aus.
1. Wählen Sie auf der Seite „Übersicht“ der App unter **Verwalten** die Option **Eigenschaften** aus.

    ![Link „Eigenschaften“](common/properties.png)

1. Wählen Sie für **Benutzerzuweisung erforderlich?** die Option **Nein** aus.

    ![Benutzerzuweisung nicht erforderlich](common/user-assignment-not-required.png)

1. Wählen Sie **Speichern** aus.

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Datadog können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-any-app)
