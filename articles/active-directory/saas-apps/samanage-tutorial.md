---
title: 'Tutorial: Azure Active Directory-Integration mit SolarWinds Service Desk (vormals Samanage) | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie einmaliges Anmelden zwischen Azure Active Directory und SolarWinds Service Desk (vormals Samanage) konfigurieren.
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
ms.openlocfilehash: d7d1684229e71049c18bd6967722b52d8589ab6a
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132329371"
---
# <a name="tutorial-azure-active-directory-integration-with-solarwinds-service-desk-previously-samanage"></a>Tutorial: Azure Active Directory-Integration mit SolarWinds Service Desk (vormals Samanage)

In diesem Tutorial erfahren Sie, wie Sie SolarWinds in Azure Active Directory (Azure AD) integrieren. Die Integration von SolarWinds in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf SolarWinds hat.
* Ermöglichen Sie Ihren Benutzern, sich mit ihrem Azure AD-Konto automatisch bei SolarWinds anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Abonnement, das für das einmalige Anmelden (SSO) bei SolarWinds aktiviert ist

> [!NOTE]
> Diese Integration kann auch über die Azure AD-Umgebung für die US Government-Cloud verwendet werden. Sie finden diese Anwendung im Azure AD-Katalog für US Government-Cloudanwendungen und konfigurieren sie auf die gleiche Weise wie in der öffentlichen Cloud.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* SolarWinds unterstützt **SP**-initiiertes einmaliges Anmelden.
* SolarWinds unterstützt [Automatisierte Benutzerbereitstellung](samanage-provisioning-tutorial.md).

## <a name="add-solarwinds-from-the-gallery"></a>Hinzufügen von SolarWinds aus dem Katalog

Zum Konfigurieren der Integration von SolarWinds in Azure AD müssen Sie SolarWinds aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **SolarWinds** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **SolarWinds** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-solarwinds"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für SolarWinds

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit SolarWinds mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in SolarWinds eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit SolarWinds die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für SolarWinds](#configure-solarwinds-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
    1. **[Erstellen eines SolarWinds-Testbenutzers](#create-solarwinds-test-user)** , um in SolarWinds eine Entsprechung von B. Simon zu erhalten, die mit der Darstellung des Benutzers in Azure AD verknüpft ist.
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **SolarWinds** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    a. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<Company Name>.samanage.com/saml_login/<Company Name>`.

    b. Geben Sie im Textfeld **Bezeichner (Entitäts-ID)** eine URL im folgenden Format ein: `https://<Company Name>.samanage.com`.

    > [!NOTE] 
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch die tatsächliche Anmelde-URL und den tatsächlichen Bezeichner. Dies wird später in diesem Tutorial beschrieben. Wenden Sie sich an das [Supportteam für den Samanage-Client](https://www.samanage.com/support), wenn Sie weitere Details benötigen. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

4. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um das Ihrer Anforderung entsprechende **Zertifikat (Base64)** aus den angegebenen Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

6. Kopieren Sie im Abschnitt **SolarWinds einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf SolarWinds gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **SolarWinds** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

<a name="configure-solarwinds-single-sign-on"></a>

## <a name="configure-solarwinds-sso"></a>Konfigurieren des einmaligen Anmeldens für SolarWinds

1. Melden Sie sich in einem anderen Webbrowserfenster bei der SolarWinds-Unternehmenswebsite als Administrator an.

2. Klicken Sie auf **Dashboard**, und wählen Sie im linken Navigationsbereich **Setup** aus.
   
    ![Dashboard](./media/samanage-tutorial/tutorial-samanage-1.png "Dashboard")

3. Klicken Sie auf **Einmaliges Anmelden**.
   
    ![Einmaliges Anmelden](./media/samanage-tutorial/tutorial-samanage-2.png "Single Sign-On")

4. Führen Sie im Abschnitt **Login using SAML** (Mit SAML anmelden) die folgenden Schritte aus:
   
    ![Login using SAML (Mit SAML anmelden)](./media/samanage-tutorial/tutorial-samanage-3.png "Login using SAML")
 
    a. Klicken Sie auf **Enable Single Sign-On with SAML**(Einmaliges Anmelden mit SAML aktivieren).  
 
    b. Geben Sie im Textfeld **Identity Provider URL** (Identitätsanbieter-URL) den Wert ein, z. B. `https://YourAccountName.samanage.com`.
 
    c. Vergewissern Sie sich, dass die **Anmelde-URL** mit der **Anmelde-URL** im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal übereinstimmt.
 
    d. Fügen Sie in das Textfeld **Abmelde-URL** den Wert der **Abmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.
 
    e. Geben Sie im Textfeld **SAML Issuer** (SAML-Aussteller) den in Ihrem Identitätsanbieter festgelegten App-ID-URI an.
 
    f. Öffnen Sie das aus dem Azure-Portal heruntergeladene Base-64-codierte Zertifikat in Editor, kopieren Sie den Inhalt des Zertifikats in die Zwischenablage, und fügen Sie ihn anschließend in das Textfeld **Paste your Identity Provider x.509 Certificate below** (X.509-Zertifikat des Identitätsanbieters einfügen) ein.
 
    g. Klicken Sie auf **Create users if they do not exist in SolarWinds**(Benutzer erstellen, wenn sie nicht in SolarWinds vorhanden sind).
 
    h. Klicken Sie auf **Aktualisieren**.

### <a name="create-solarwinds-test-user"></a>Erstellen eines SolarWinds-Testbenutzers

Damit sich Azure AD-Benutzer bei SolarWinds anmelden können, müssen sie in SolarWinds bereitgestellt werden.  
Im Fall von SolarWinds ist die Bereitstellung eine manuelle Aufgabe.

**Führen Sie zum Bereitstellen eines Benutzerkontos die folgenden Schritte aus:**

1. Melden Sie sich bei der SolarWinds-Unternehmenswebsite als Administrator an.

2. Klicken Sie auf **Dashboard**, und wählen Sie im linken Navigationsbereich die Option **Setup** aus.
   
    ![Einrichtung](./media/samanage-tutorial/tutorial-samanage-1.png "Setup")

3. Klicken Sie auf die Registerkarte **Benutzer**
   
    ![Benutzer](./media/samanage-tutorial/tutorial-samanage-6.png "Benutzer")

4. Klicken Sie auf **Neuer Benutzer**.
   
    ![Neuer Benutzer](./media/samanage-tutorial/tutorial-samanage-7.png "Neuer Benutzer")

5. Geben Sie **Name** und **E-Mail-Adresse** eines vorhandenen Azure Active Directory-Kontos ein, das Sie bereitstellen möchten, und klicken Sie auf **Benutzer erstellen**.
   
    ![Benutzer erstellen](./media/samanage-tutorial/tutorial-samanage-8.png "Benutzer erstellen")
   
   >[!NOTE]
   >Der Besitzer des Azure Active Directory-Kontos erhält eine E-Mail und folgt einem Link zur Bestätigung des Kontos, bevor es aktiv wird. Sie können Azure Active Directory-Benutzerkonten auch mithilfe anderer Tools zum Erstellen von SolarWinds-Benutzerkonten oder mithilfe der von SolarWinds bereitgestellten APIs erstellen.

> [!NOTE]
> Außerdem unterstützt SolarWinds automatische Benutzerbereitstellung. Weitere Informationen zum Konfigurieren der automatischen Benutzerbereitstellung finden Sie [hier](./samanage-provisioning-tutorial.md).

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für SolarWinds weitergeleitet, wo Sie den Anmeldeflow initiieren können. 

* Rufen Sie direkt die SolarWinds-Anmelde-URL auf, und initiieren Sie den Anmeldeflow.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie unter „Meine Apps“ auf die Kachel „SolarWinds“ klicken, werden Sie zur Anmelde-URL für SolarWinds weitergeleitet. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von SolarWinds können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-any-app)
