---
title: 'Tutorial: Azure Active Directory-Integration mit Ungerboeck Software | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Ungerboeck Software konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/27/2021
ms.author: jeedes
ms.openlocfilehash: 822e553d30d68990e961a29540e8d7a9c1d76f07
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132328564"
---
# <a name="tutorial-integrate-ungerboeck-software-with-azure-active-directory"></a>Tutorial: Integrieren von Ungerboeck Software in Azure Active Directory

In diesem Tutorial erfahren Sie, wie Sie Ungerboeck Software in Azure Active Directory (Azure AD) integrieren. Die Integration von Ungerboeck Software in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer auf Ungerboeck Software Zugriff hat.
* Ermöglichen Sie Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Ungerboeck Software anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Ungerboeck Software-Abonnement, für das einmaliges Anmelden (SSO, Single Sign-On) aktiviert ist.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Ungerboeck Software unterstützt **SP-initiiertes** einmaliges Anmelden.

## <a name="add-ungerboeck-software-from-the-gallery"></a>Hinzufügen von Ungerboeck Software aus dem Katalog

Zum Konfigurieren der Integration von Ungerboeck Software in Azure AD müssen Sie Ungerboeck Software über den Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Ungerboeck Software** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Ungerboeck Software** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-ungerboeck-software"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Ungerboeck Software

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Ungerboeck Software mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Ungerboeck Software eingerichtet werden.

Führen Sie die folgenden Schritte aus, um das einmalige Anmelden von Azure AD mit Ungerboeck Software zu konfigurieren und zu testen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Ungerboeck Software](#configure-ungerboeck-software-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines Ungerboeck Software-Testbenutzers](#create-ungerboeck-software-test-user)** , um in Ungerboeck Software eine Entsprechung von B.Simon zu erhalten, die mit der Benutzerdarstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Ungerboeck Software** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Führen Sie auf der Seite **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    1. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<SUBDOMAIN>.ungerboeck.com/prod`.

    1. Geben Sie im Textfeld **Bezeichner (Entitäts-ID)** eine URL in einem der folgenden Formate ein:
    
    *  **Für die Produktionsumgebung**:

       - `https://<SUBDOMAIN>.ungerboeck.com/prod`
       - `https://<SUBDOMAIN>.ungerboeck.net/prod`
       - `https://<SUBDOMAIN>.ungerboeck.io/prod`

   * **Für die Testumgebung**:

     - `https://<SUBDOMAIN>.ungerboeck.com/test`
     - `https://<SUBDOMAIN>.ungerboeck.net/test`
     - `https://<SUBDOMAIN>.ungerboeck.io/test`

   > [!NOTE]
   > Hierbei handelt es sich um Beispielwerte. Aktualisieren Sie diese Werte mit der tatsächlichen Anmelde-URL und dem Bezeichner. Dies ist später in diesem Tutorial im Abschnitt **Konfigurieren des einmaligen Anmeldens für Ungerboeck Software** beschrieben.

1. Klicken Sie im Abschnitt **SAML-Signaturzertifikat** auf die Schaltfläche **Bearbeiten**, um das Dialogfeld **SAML-Signaturzertifikat** zu öffnen.

    ![Bearbeiten des SAML-Signaturzertifikats](common/edit-certificate.png)

1. Kopieren Sie im Abschnitt **SAML-Signaturzertifikat** den **Fingerabdruck**, und speichern Sie ihn auf Ihrem Computer.

    ![Kopieren des Fingerabdruckwerts](common/copy-thumbprint.png)

1. Kopieren Sie im Abschnitt **Ungerboeck Software einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Ungerboeck Software gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Ungerboeck Software** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-ungerboeck-software-sso"></a>Konfigurieren von Ungerboeck Software SSO

Um das einmalige Anmelden auf der Seite von **Ungerboeck Software** zu konfigurieren, müssen Sie den **Fingerabdruckwert** und die aus dem Azure-Portal kopierten URLs an das [Ungerboeck Software-Supportteam](mailto:Rhonda.Jannings@ungerboeck.com) senden. Es führt die Einrichtung durch, damit die SAML-SSO-Verbindung auf beiden Seiten richtig festgelegt ist.

### <a name="create-ungerboeck-software-test-user"></a>Erstellen eines Ungerboeck Software-Testbenutzers

In diesem Abschnitt erstellen Sie in Ungerboeck Software einen Benutzer namens B. Simon. Wenden Sie sich an das [Ungerboeck Software-Supportteam](mailto:Rhonda.Jannings@ungerboeck.com), um die Benutzer auf der Ungerboeck Software-Plattform hinzuzufügen. Benutzer müssen erstellt und aktiviert werden, damit Sie einmaliges Anmelden verwenden können.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für Ungerboeck Software weitergeleitet, wo Sie den Anmeldeflow initiieren können. 

* Rufen Sie direkt die Anmelde-URL für Ungerboeck Software auf, und initiieren Sie den Anmeldeflow.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie in „Meine Apps“ auf die Kachel „Ungerboeck Software“ klicken, werden Sie zur Anmelde-URL für Ungerboeck Software umgeleitet. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Ungerboeck Software können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-aad)
