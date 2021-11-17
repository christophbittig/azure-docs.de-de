---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Bizagi for Digital Process Automation | Microsoft-Dokumentation'
description: Es wird beschrieben, wie Sie einmaliges Anmelden zwischen Azure Active Directory und Bizagi for Digital Process Automation konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/27/2021
ms.author: jeedes
ms.openlocfilehash: 16389290e20f74c24b683818502eaff96f380d77
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132335060"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-bizagi-for-digital-process-automation"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Bizagi for Digital Process Automation

In diesem Tutorial erfahren Sie, wie Sie den Server oder Dienst von Bizagi for Digital Process Automation in Azure Active Directory (Azure AD) integrieren. Die Integration von Bizagi for Digital Process Automation in Azure AD ermöglicht Folgendes:

* Sie können steuern in Azure AD, wer Zugriff auf ein Bizagi-Projekt for Digital Process Automation-Dienste oder -Server hat.
* Sie können es Ihren Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei einem Projekt der Bizagi Studio for Digital Process Automation-Dienste oder -Server anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Bizagi-Projekt, dass Automation-Dienste oder -Server verwendet. 
* Sie verfügen über eigene Zertifikate für SAML-Assertionssignaturen. Diese Zertifikate müssen im P12- oder PFX-Format generiert werden.
* Sie verfügen über eine Metadatendatei im XML-Format, die aus dem Bizagi-Projekt generiert wurde. 

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einem Bizagi-Projekt mithilfe von Automation-Diensten oder -Servern.

* Bizagi for Digital Process Automation unterstützt das **SP**-initiierte einmalige Anmelden.
* Bizagi for Digital Process Automation unterstützt [automatisierte Benutzerbereitstellung](bizagi-studio-for-digital-process-automation-provisioning-tutorial.md).

## <a name="add-bizagi-for-digital-process-automation-from-the-gallery"></a>Hinzufügen von Bizagi for Digital Process Automation aus dem Katalog

Zum Konfigurieren der Integration von Bizagi for Digital Process Automation in Azure AD müssen Sie Bizagi for Digital Process Automation aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** im Suchfeld den Suchbegriff **Bizagi for Digital Process Automation** ein.
1. Wählen Sie im Ergebnisbereich **Bizagi for Digital Process Automation** aus, und fügen Sie anschließend die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-bizagi-for-digital-process-automation"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Bizagi for Digital Process Automation

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Bizagi for Digital Process Automation mithilfe einer Testbenutzerin mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer im Bizagi-Projekt eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Bizagi for Digital Process Automation die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Bizagi for Digital Process Automation](#configure-bizagi-for-digital-process-automation-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
    1. **[Erstellen eines Bizagi for Digital Process Automation-Testbenutzers](#create-bizagi-for-digital-process-automation-test-user)** , um eine Entsprechung von B. Simon in Bizagi for Digital Process Automation zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist.
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Bizagi for Digital Process Automation** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden (SSO)** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    a. Geben Sie im Textfeld **Bezeichner (Entitäts-ID)** eine URL im folgenden Format ein: `https://<COMPANY_NAME>.bizagi.com/<PROJECT_NAME>`.

    b. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<COMPANY_NAME>.bizagi.com/<PROJECT_NAME>`.

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch den tatsächlichen Bezeichner und die tatsächliche Anmelde-URL. Wenden Sie sich an das [Supportteam von Bizagi for Digital Process Automation](mailto:jarvein.rivera@bizagi.com), um diese Werte zu erhalten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf die Schaltfläche „Kopieren“, um die **App-Verbundmetadaten-URL** zu kopieren, und speichern Sie sie auf Ihrem Computer.

    ![Downloadlink für das Zertifikat](common/copy-metadataurl.png)
    
    Diese Metadaten-URL muss in den Authentifizierungsoptionen Ihres Bizagi-Projekts registriert werden.
    
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **User Attributes & Claims** (Benutzerattribute und Ansprüche), um die eindeutige Benutzer-ID zu bearbeiten.
    
    Legen Sie die eindeutige Benutzer-ID als „user.mail“ fest.

### <a name="create-an-azure-ad-test"></a>Erstellen eines Azure AD-Testbenutzers 

In diesem Abschnitt erstellen Sie im Azure-Portal einen Testbenutzer mit dem Namen B. Simon.

1. Wählen Sie im linken Bereich des Microsoft Azure-Portals **Azure Active Directory** > **Benutzer** > **Alle Benutzer** aus.
1. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.
1. Führen Sie unter den Eigenschaften für **Benutzer** die folgenden Schritte aus:
   1. Geben Sie im Feld **Name** die Zeichenfolge `B.Simon` ein.  
   1. Geben Sie im Feld **Benutzername** die Zeichenfolge username@companydomain.extension ein. Beispiel: `B.Simon@contoso.com`.
   1. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**, und notieren Sie sich den Wert aus dem Feld **Kennwort**.
   1. Klicken Sie auf **Erstellen**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Bizagi for Digital Process Automation gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste die Anwendung **Bizagi for Digital Process Automation** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-bizagi-for-digital-process-automation-sso"></a>Konfigurieren des einmaligen Anmeldens für Bizagi for Digital Process Automation

Zum Konfigurieren des einmaligen Anmeldens aufseiten von **Bizagi for Digital Process Automation** müssen Sie die **App-Verbundmetadaten-URL** an das [Supportteam von Bizagi for Digital Process Automation](mailto:jarvein.rivera@bizagi.com) senden. Es führt die Einrichtung durch, damit die SAML-SSO-Verbindung auf beiden Seiten richtig festgelegt ist.

### <a name="create-bizagi-for-digital-process-automation-test-user"></a>Erstellen eines Bizagi for Digital Process Automation-Testbenutzers

In diesem Abschnitt erstellen Sie in Bizagi for Digital Process Automation eine Benutzerin namens Britta Simon. Wenden Sie sich an das [Supportteam von Bizagi for Digital Process Automation](mailto:jarvein.rivera@bizagi.com), um die Benutzer auf der Bizagi for Digital Process Automation-Plattform hinzufügen zu lassen. Benutzer müssen erstellt und aktiviert werden, damit Sie einmaliges Anmelden verwenden können.

Außerdem unterstützt Bizagi for Digital Process Automation automatische Benutzerbereitstellung. Weitere Informationen zum Konfigurieren der automatischen Benutzerbereitstellung finden Sie [hier](./bizagi-studio-for-digital-process-automation-provisioning-tutorial.md).

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für Bizagi for Digital Process Automation weitergeleitet. Dort können Sie den Anmeldeflow initiieren. 

* Navigieren Sie direkt zur Anmelde-URL für Bizagi for Digital Process Automation, und initiieren Sie den Anmeldeflow.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie unter „Meine Apps“ auf die Kachel „Bizagi for Digital Process Automation“ klicken, erfolgt eine Weiterleitung zur Anmelde-URL von Bizagi for Digital Process Automation. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Bizagi for Digital Process Automation können Sie eine Sitzungssteuerung erzwingen, die in Echtzeit vor der Ex- und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-aad)
