---
title: 'Tutorial: Integrieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Splan Visitor | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie einmaliges Anmelden zwischen Azure Active Directory und Splan Visitor konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/21/2021
ms.author: jeedes
ms.openlocfilehash: 5c628e67b258d3d51b5f7b05b08c0ac0b2accb9a
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132304091"
---
# <a name="tutorial-integrate-azure-active-directory-single-sign-on-sso-with-splan-visitor"></a>Tutorial: Integrieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Splan Visitor

In diesem Tutorial erfahren Sie, wie Sie Splan Visitor in Azure Active Directory (Azure AD) integrieren. Die Integration von Splan Visitor in Azure AD ermöglicht Folgendes:

* Steuern Sie mit Azure AD, wer Zugriff auf Splan Visitor hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihrem Azure AD-Konto automatisch bei Splan Visitor anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Zunächst benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Splan Visitor-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

> [!NOTE]
> Diese Integration kann auch über die Azure AD-Umgebung für die US Government-Cloud verwendet werden. Sie finden diese Anwendung im Azure AD-Katalog für US Government-Cloudanwendungen und konfigurieren sie auf die gleiche Weise wie in der öffentlichen Cloud.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Splan Visitor unterstützt IdP-initiiertes einmaliges Anmelden.

## <a name="add-splan-visitor-from-the-gallery"></a>Hinzufügen von Splan Visitor aus dem Katalog

Um die Integration von Splan Visitor in Azure AD zu konfigurieren, fügen Sie Splan Visitor über den Katalog Ihrer Liste mit den verwalteten SaaS-Apps hinzu.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Bereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Splan Visitor** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Splan Visitor**  aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-splan-visitor"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Splan Visitor

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Splan Visitor mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Splan Visitor eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Splan Visitor die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Splan Visitor](#configure-splan-visitor-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines Splan Visitor-Testbenutzers](#create-a-splan-visitor-test-user)** , um in Splan Visitor eine Entsprechung von B. Simon zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren:

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Splan Visitor** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Wählen Sie auf der Seite **Einmaliges Anmelden mit SAML einrichten** das **Stiftsymbol** für die **Grundlegende SAML-Konfiguration** aus, um die Einstellungen zu bearbeiten.

   ![Screenshot: Hervorgehobenes Bearbeitungs-/Stiftsymbol für die grundlegende SAML-Konfiguration](common/edit-urls.png)

1. Im Abschnitt **Grundlegende SAML-Konfiguration** ist die Anwendung vorkonfiguriert, und die erforderlichen URLs sind in Azure vorausgefüllt. Wählen Sie die Schaltfläche **Speichern** aus, um die Konfiguration zu speichern.

1. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** zu **Verbundmetadaten-XML**. Wählen Sie **Herunterladen** aus, um das Zertifikat herunterzuladen und auf Ihrem Computer zu speichern.

    ![Screenshot: Hervorgehobener Downloadlink für Verbundmetadaten-XML](common/metadataxml.png)

1. Kopieren Sie im Abschnitt **Splan Visitor einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Screenshot: Hervorgehobener Abschnitt mit Konfigurations-URLs](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

In diesem Abschnitt erstellen Sie im Azure-Portal einen Testbenutzer mit dem Namen B. Simon.

1. Wählen Sie im linken Bereich des Azure-Portals Folgendes aus: **Azure Active Directory** > **Benutzer** > **Alle Benutzer**.
1. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.
1. Führen Sie unter den Eigenschaften für **Benutzer** die folgenden Schritte aus:
   1. Geben Sie **B.Simon** in das Feld **Name** ein.  
   1. Geben Sie im Feld **Benutzername** den Benutzernamen im Format _username@companydomain.extension_ ein. Geben Sie z.B. **B.Simon@contoso.com** ein.
   1. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**, und notieren Sie sich den Wert aus dem Feld **Kennwort**.
   1. Klicken Sie auf **Erstellen**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie B.Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie Zugriff auf Splan Visitor gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Splan Visitor** aus, um die App-Übersicht zu öffnen.
1. Navigieren Sie zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie die Schaltfläche **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste **Benutzer** den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf **Auswählen**.
1. Wird dem Benutzer eine Rolle zugewiesen, wählen Sie sie im Dropdownmenü **Rolle auswählen** aus. Wurde für diese App keine Rolle eingerichtet, übernehmen Sie die ausgewählte Rolle **Standardzugriff**.
1. Wählen Sie im Dialogfeld **Zuweisung hinzufügen** die Option **Zuweisen** aus.

## <a name="configure-splan-visitor-sso"></a>Konfigurieren des einmaligen Anmeldens mit Splan Visitor

Senden Sie zum Konfigurieren des einmaligen Anmeldens aufseiten von Splan Visitor die heruntergeladene **Verbundmetadaten-XML** und die kopierten URLs aus dem Azure-Portal an das [Supportteam von Splan Visitor](mailto:support@splan.com). Dadurch wird sichergestellt, dass die SAML-Verbindung für einmaliges Anmelden auf beiden Seiten ordnungsgemäß eingerichtet wird.

### <a name="create-a-splan-visitor-test-user"></a>Erstellen eines Testbenutzers für Splan Visitor

Erstellen Sie in Splan Visitor einen Testbenutzer namens **Britta Simon**. Wenden Sie sich an das [Splan Visitor-Supportteam](mailto:support@splan.com), um den Benutzer zu Splan Visitor hinzuzufügen. Sie müssen den Benutzer erstellen und aktivieren, bevor Sie einmaliges Anmelden verwenden.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

Testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit einer der folgenden Optionen:

* **Azure-Portal**: Wählen Sie **Diese Anwendung testen** aus, um sich automatisch bei der Splan Visitor-Instanz anzumelden, für die Sie einmaliges Anmelden eingerichtet haben.
* **Portal „Meine Apps“ von Microsoft:** Wählen Sie die Kachel **Splan Visitor** aus, um sich automatisch bei der Splan Visitor-Instanz anzumelden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Portal „Meine Apps“ finden Sie unter [Anmelden beim Portal „Meine Apps“ und Starten von Apps über dieses](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Splan Visitor können Sie sich [hier](/cloud-app-security/proxy-deployment-any-app) darüber informieren, wie Sie Sitzungssteuerungen in Microsoft Defender for Cloud Apps erzwingen. Sitzungssteuerungen schützen in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten. Sitzungssteuerungen basieren auf bedingtem Zugriff.
