---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD mit Change Process Management'
description: Hier erfahren Sie, wie Sie einmaliges Anmelden zwischen Azure Active Directory und Change Process Management konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/07/2020
ms.author: jeedes
ms.openlocfilehash: 357080a6349c97bfd517a29cc30ba0c4e0702f9d
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132349032"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-change-process-management"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Change Process Management

In diesem Tutorial erfahren Sie, wie Sie Change Process Management in Azure Active Directory (Azure AD) integrieren. Die Integration von Change Process Management in Azure AD ermöglicht Folgendes:

* Verwenden Sie Azure AD, um festzulegen, wer auf Change Process Management zugreifen kann.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Change Process Management anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Einmaliges Anmelden bei Anwendungen in Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Change Process Management-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist.

## <a name="tutorial-description"></a>Beschreibung des Tutorials

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

Change Process Management unterstützt IDP-initiiertes einmaliges Anmelden.

Nach dem Konfigurieren von Change Process Management können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Sitzungssteuerungen basieren auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-any-app)

## <a name="add-change-process-management-from-the-gallery"></a>Hinzufügen von Change Process Management über den Katalog

Zum Konfigurieren der Integration von Change Process Management in Azure AD müssen Sie Change Process Management über den Katalog Ihrer Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie im linken Bereich **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Change Process Management** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Change Process Management** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-change-process-management"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Change Process Management

Sie konfigurieren und testen einmaliges Anmelden von Azure AD mit Change Process Management mithilfe eines Testbenutzers mit dem Namen B. Simon. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Change Process Management eingerichtet werden.

Führen Sie diese Schritte aus, um einmaliges Anmelden von Azure AD mit Change Process Management zu konfigurieren und zu testen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD zu testen.
    1. **[Gewähren der Zugriffsberechtigungen für den Testbenutzer](#grant-access-to-the-test-user)** , um dem Benutzer die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Change Process Management](#configure-change-process-management-sso)** auf Anwendungsseite.
    1. **[Erstellen eines Change Process Management-Testbenutzers](#create-a-change-process-management-test-user)** als Pendant zur Azure AD-Darstellung des Benutzers.
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert.

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **Change Process Management** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Wählen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** das Stiftsymbol für **Grundlegende SAML-Konfiguration** aus, um die Einstellungen zu bearbeiten:

   ![Schaltfläche mit dem Bleistift für die grundlegende SAML-Konfiguration](common/edit-urls.png)

1. Führen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** diese Schritte aus:

    a. Geben Sie im Feld **Bezeichner** eine URL im folgenden Format ein: `https://<hostname>:8443/`

    b. Geben Sie im Feld **Antwort-URL** eine URL im folgenden Format ein: `https://<hostname>:8443/changepilot/saml/sso`

    > [!NOTE]
    > Die vorhergehenden Werte für **Bezeichner** und **Antwort-URL** sind nicht die tatsächlichen Werte, die Sie nutzen sollten. Die tatsächlichen Werte erhalten Sie vom [Supportteam für Change Process Management](mailto:support@realtech-us.com). Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

1. Wählen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** den Link **Download** für **Zertifikat (Base64)** aus, um das Zertifikat herunterzuladen, und speichern Sie es auf Ihrem Computer:

    ![Downloadlink für Zertifikat](common/certificatebase64.png)

1. Kopieren Sie im Abschnitt **Change Process Management einrichten** die entsprechenden URLs basierend auf Ihren Anforderungen:

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

In diesem Abschnitt erstellen Sie im Azure-Portal einen Testbenutzer mit dem Namen B. Simon.

1. Wählen Sie im linken Bereich des Azure-Portals den Dienst **Azure Active Directory** aus. Wählen Sie **Benutzer** und dann **Alle Benutzer** aus.
1. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.
1. Führen Sie in den **Benutzereigenschaften** die folgenden Schritte aus:
   1. Geben Sie im Feld **Name** den Namen **B.Simon** ein.  
   1. Geben Sie im Feld **Benutzername** Folgendes ein: \<username>@\<companydomain>.\<extension>. Beispiel: `B.Simon@contoso.com`.
   1. Aktivieren Sie **Kennwort anzeigen**, und notieren Sie sich den Wert, der im Feld **Kennwort** angezeigt wird.
   1. Klicken Sie auf **Erstellen**.

### <a name="grant-access-to-the-test-user"></a>Gewähren von Zugriff für den Testbenutzer

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Change Process Management gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste die Option **Change Process Management** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus:

   ![Benutzer und Gruppen auswählen](common/users-groups-blade.png)

1. Wählen Sie die Schaltfläche **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.

    ![Auswählen von „Benutzer hinzufügen“](common/add-assign-user.png)

1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste **Benutzer** den Eintrag **B. Simon** aus, und klicken Sie dann am unteren Bildschirmrand auf die Schaltfläche **Auswählen**.
1. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** die entsprechende Rolle für den Benutzer in der Liste aus, und klicken Sie dann im unteren Bildschirmbereich auf die Schaltfläche **Auswählen**.
1. Wählen Sie im Dialogfeld **Zuweisung hinzufügen** die Option **Zuweisen** aus.

## <a name="configure-change-process-management-sso"></a>Konfigurieren des einmaligen Anmeldens für Change Process Management

Zum Konfigurieren des einmaligen Anmeldens aufseiten von Change Process Management müssen Sie das heruntergeladene Base64-Zertifikat und die entsprechenden aus dem Azure-Portal kopierten URLs an das [Supportteam von Change Process Management](mailto:support@realtech-us.com) senden. Die SAML-SSO-Verbindung wird so konfiguriert, dass Sie auf beiden Seiten richtig ist.

### <a name="create-a-change-process-management-test-user"></a>Erstellen eines Change Process Management-Testbenutzers
 Wenden Sie sich an das [Change Process Management-Supportteam](mailto:support@realtech-us.com), um den Benutzer B.Simon in Change Process Management hinzuzufügen. Benutzer müssen erstellt und aktiviert werden, damit Sie einmaliges Anmelden verwenden können.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden (SSO) über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich die Kachel „Change Process Management“ auswählen, sollten Sie automatisch bei der Change Process Management-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Tutorials zur Integration von SaaS-Apps in Azure Active Directory](./tutorial-list.md)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Was ist bedingter Zugriff?](../conditional-access/overview.md)

- [Change Process Management mit Azure AD ausprobieren](https://aad.portal.azure.com/)

- [Was ist die Sitzungssteuerung in Microsoft Defender for Cloud Apps?](/cloud-app-security/proxy-intro-aad)

- [Schützen von Apps mit der App-Steuerung für bedingten Zugriff von Microsoft Cloud App Security](/cloud-app-security/proxy-intro-aad)
