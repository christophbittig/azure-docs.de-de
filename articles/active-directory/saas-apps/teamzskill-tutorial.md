---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit TeamzSkill | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie einmaliges Anmelden zwischen Azure Active Directory und TeamzSkill konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/18/2020
ms.author: jeedes
ms.openlocfilehash: edfb0a70b1372d9b61d07514cb2dd25e5805af82
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132282736"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-teamzskill"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit TeamzSkill

In diesem Tutorial erfahren Sie, wie Sie TeamzSkill in Azure Active Directory (Azure AD) integrieren. Die Integration von TeamzSkill in Azure AD ermöglicht Folgendes:

- Steuern Sie in Azure AD, wer Zugriff auf TeamzSkill hat.
- Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei TeamzSkill anzumelden.
- Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

- Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
- TeamzSkill-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

- TeamzSkill unterstützt **SP- und IDP-initiiertes** einmaliges Anmelden.
- TeamzSkill unterstützt die **Just-In-Time**-Benutzerbereitstellung.

## <a name="adding-teamzskill-from-the-gallery"></a>Hinzufügen von TeamzSkill aus dem Katalog

Zum Konfigurieren der Integration von TeamzSkill in Azure AD müssen Sie TeamzSkill aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **TeamzSkill** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **TeamzSkill** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-teamzskill"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für TeamzSkill

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit TeamzSkill mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in TeamzSkill eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit TeamzSkill die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
   1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
   1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für TeamzSkill](#configure-teamzskill-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
   1. **[Erstellen eines TeamzSkill-Testbenutzers](#create-teamzskill-test-user)** , um ein Pendant von B. Simon in TeamzSkill zu erhalten, das mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **TeamzSkill** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Geben Sie im Abschnitt **Grundlegende SAML-Konfiguration** die Werte in die folgenden Felder ein, wenn Sie die Anwendung im **IDP**-initiierten Modus konfigurieren möchten:

   a. Geben Sie im Textfeld **Bezeichner** eine URL im folgenden Format ein: `https://<CUSTOMER_SUBDOMAIN>.teamzskill.com/login/callback`

   b. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein: `https://<CUSTOMER_SUBDOMAIN>.teamzskill.com/login/callback`

1. Klicken Sie auf **Zusätzliche URLs festlegen**, und führen Sie den folgenden Schritt aus, wenn Sie die Anwendung im **SP-initiierten Modus** konfigurieren möchten:

   Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<CUSTOMER_SUBDOMAIN>.teamzskill.com/login/callback`

   > [!NOTE]
   > Hierbei handelt es sich um Beispielwerte. Sie müssen diese Werte mit dem tatsächlichen Bezeichner, der Antwort-URL und der Anmelde-URL aktualisieren. Diese Werte erhalten Sie vom [Supportteam für den TeamzSkill-Client](mailto:support@teamzskill.com). Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

1. Die TeamzSkill-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Daher müssen Sie Ihrer Konfiguration der SAML-Tokenattribute benutzerdefinierte Attributzuordnungen hinzufügen. Der folgende Screenshot zeigt die Liste der Standardattribute.

   ![image](common/default-attributes.png)

1. Darüber hinaus wird von der TeamzSkill-Anwendung erwartet, dass in der SAML-Antwort noch einige weitere Attribute zurückgegeben werden (siehe unten). Diese Attribute werden ebenfalls vorab aufgefüllt, Sie können sie jedoch nach Bedarf überprüfen.

   | Name       | Quellattribut   |
   | ---------- | ------------------ |
   | Firstname  | user.givenname     |
   | Lastname   | user.surname       |
   | jobtitle   | user.jobtitle      |
   | department | user.department    |
   | employeeid | user.employeeid    |
   | postalcode | user.postalcode    |
   | country    | user.country       |
   | Rolle (role)       | user.assignedroles |

   > [!NOTE]
   > TeamzSkill erwartet, dass der Anwendung Rollen für Benutzer zugewiesen werden. Richten Sie diese Rollen in Azure AD ein, damit Benutzern die passenden Rollen zugewiesen werden können. Informationen zum Konfigurieren von Rollen in Azure AD finden Sie [hier](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui).

1. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** zu **Verbundmetadaten-XML**, und wählen Sie **Herunterladen** aus, um das Zertifikat herunterzuladen und auf Ihrem Computer zu speichern.

   ![Downloadlink für das Zertifikat](common/metadataxml.png)

1. Kopieren Sie im Abschnitt **TeamzSkill einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf TeamzSkill gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste den Eintrag **TeamzSkill** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn Sie die Rollen wie oben erläutert eingerichtet haben, können Sie sie in der Dropdownliste **Rolle auswählen** auswählen.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-teamzskill-sso"></a>Konfigurieren des einmaligen Anmeldens für TeamzSkill

1. Melden Sie sich in einem anderen Webbrowserfenster bei der TeamzSkill-Website als Administrator an.

1. Klicken Sie auf das Symbol für das Benutzerprofil, und wählen Sie dann **Company settings** (Unternehmenseinstellungen) aus.

   ![„Company settings“ (Unternehmenseinstellungen) in Teamzskill](./media/teamzskill-tutorial/settings.png)

1. Führen Sie auf der Seite **Settings** (Einstellungen) die folgenden Schritte aus:

   ![„Settings“ (Einstellungen) in Teamzskill](./media/teamzskill-tutorial/metadata.png)

   a. Navigieren Sie zu **Company > Single Sign-On** (Unternehmen > Einmaliges Anmelden), und wählen Sie die Registerkarte **Metadata Upload** (Metadatenupload) aus.

   b. Fügen Sie im Feld **XML Metadata** (XML-Metadaten) den Wert der **Verbundmetadaten-XML** ein, den Sie aus dem Azure-Portal kopiert haben.

   c. Klicken Sie anschließend auf **Speichern**.

### <a name="create-teamzskill-test-user"></a>Erstellen eines TeamzSkill-Testbenutzers

In diesem Abschnitt wird in TeamzSkill ein Benutzer namens B. Simon erstellt. TeamzSkill unterstützt die Just-In-Time-Bereitstellung (standardmäßig aktiviert). Für Sie steht in diesem Abschnitt kein Aktionselement zur Verfügung. Falls ein Benutzer nicht bereits in TeamzSkill vorhanden ist, wird beim Versuch, auf TeamzSkill zuzugreifen, ein neuer Benutzer erstellt.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen:

#### <a name="sp-initiated"></a>SP-initiiert:

- Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für TeamzSkill weitergeleitet, wo Sie den Anmeldeflow initiieren können.

- Rufen Sie direkt die TeamzSkill-Anmelde-URL auf, und initiieren Sie den Anmeldeflow.

#### <a name="idp-initiated"></a>IDP-initiiert:

- Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch sollten Sie automatisch bei der TeamzSkill-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben.

Sie können auch den Microsoft-Zugriffsbereich verwenden, um die Anwendung in einem beliebigen Modus zu testen. Wenn Sie im Zugriffsbereich auf die Kachel „TeamzSkill“ klicken, geschieht Folgendes: Wenn Sie den SP-Modus konfiguriert haben, werden Sie zum Initiieren des Anmeldeflows zur Anmeldeseite der Anwendung weitergeleitet, und wenn Sie den IDP-Modus konfiguriert haben, sollten Sie automatisch bei der TeamzSkill-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von TeamzSkill können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-any-app)
