---
title: 'Tutorial: Azure Active Directory-Integration mit Lifesize Cloud | Microsoft Docs'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Lifesize Cloud konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/24/2021
ms.author: jeedes
ms.openlocfilehash: 948a97d598710ad8cd10a3bf89dfd2cb7b512413
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132313462"
---
# <a name="tutorial-azure-active-directory-integration-with-lifesize-cloud"></a>Tutorial: Azure Active Directory-Integration mit Lifesize Cloud

In diesem Tutorial erfahren Sie, wie Sie Lifesize Cloud in Azure Active Directory (Azure AD) integrieren. Die Integration von Lifesize Cloud in Azure AD ermöglicht Folgendes:

* Sie können in Azure AD steuern, wer Zugriff auf Lifesize Cloud hat.
* Sie können Ihren Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei Lifesize Cloud anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Lifesize Cloud-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist.

> [!NOTE]
> Diese Integration kann auch über die Azure AD-Umgebung für die US Government-Cloud verwendet werden. Sie finden diese Anwendung im Azure AD-Katalog für US Government-Cloudanwendungen und konfigurieren sie auf die gleiche Weise wie in der öffentlichen Cloud.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Lifesize Cloud unterstützt **SP-initiiertes** einmaliges Anmelden.

* Lifesize Cloud unterstützt die **automatisierte** Benutzerbereitstellung.

## <a name="add-lifesize-cloud-from-the-gallery"></a>Hinzufügen von Lifesize Cloud aus dem Katalog

Zum Konfigurieren der Integration von Lifesize Cloud in Azure AD müssen Sie Lifesize Cloud aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** im Suchfeld den Suchbegriff **Lifesize Cloud** ein.
1. Wählen Sie im Ergebnisbereich den Eintrag **Lifesize Cloud** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-lifesize-cloud"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Lifesize Cloud

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD bei Lifesize Cloud mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Lifesize Cloud eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD bei Lifesize Cloud die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Lifesize Cloud](#configure-lifesize-cloud-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
    1. **[Erstellen eines Lifesize Cloud-Testbenutzers](#create-lifesize-cloud-test-user)** , um in Lifesize Cloud eine Entsprechung von B. Simon zu erhalten, die mit der Benutzerdarstellung in Azure AD verknüpft ist.
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Lifesize Cloud** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    a. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://login.lifesizecloud.com/ls/?acs`

    b. Geben Sie im Textfeld **Bezeichner** eine URL im folgenden Format ein: `https://login.lifesizecloud.com/<COMPANY_NAME>`

    c. Klicken Sie auf **Zusätzliche URLs festlegen**.

    d. Geben Sie im Textfeld **Relayzustand** eine URL nach folgendem Muster ein: `https://webapp.lifesizecloud.com/?ent=<IDENTIFIER>`.

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch die tatsächlichen Werte von Anmelde-URL, Bezeichner und Relayzustand. Wenden Sie sich an das [Kundensupportteam von Lifesize Cloud](https://legacy.lifesize.com/en/support), um die Werte für die Anmelde-URL und den Bezeichner zu erhalten. Den Wert für den Relayzustand können Sie der SSO-Konfiguration entnehmen, die später in diesem Tutorial erläutert wird. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

5. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um das Ihrer Anforderung entsprechende **Zertifikat (Base64)** aus den angegebenen Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

6. Kopieren Sie im Abschnitt **Lifesize Cloud einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Lifesize Cloud gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Lifesize Cloud** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-lifesize-cloud-sso"></a>Konfigurieren des einmaligen Anmeldens für Lifesize Cloud

1. Melden Sie sich zum Konfigurieren von SSO für Ihre Anwendung bei der Lifesize Cloud-Anwendung mit Administratorrechten an.

2. Klicken Sie in der rechten oberen Ecke auf Ihren Namen und dann auf **Erweiterte Einstellungen**.

    ![Screenshot: Menüelement „Erweiterte Einstellungen“](./media/lifesize-cloud-tutorial/settings.png)

3. Klicken Sie in den erweiterten Einstellungen nun auf den Link **SSO-Konfiguration**. Damit wird die SSO-Konfigurationsseite für Ihre Instanz geöffnet.

    ![Screenshot: Option „Erweiterte Einstellungen“, unter der Sie „SSO-Konfiguration“ auswählen können](./media/lifesize-cloud-tutorial/configuration.png)

4. Konfigurieren Sie jetzt die folgenden Werte in der SSO-Konfigurationsbenutzeroberfläche.

    ![Screenshot: Seite „SSO-Konfiguration“, auf der Sie die beschriebenen Werte eingeben können](./media/lifesize-cloud-tutorial/values.png)

    a. Fügen Sie in das Textfeld **Aussteller des Identitätsanbieters** den Wert vom **Azure AD-Bezeichner** ein, den Sie aus dem Azure-Portal kopiert haben.

    b.  Fügen Sie in das Textfeld **Login URL** (Anmelde-URL) den Wert der **Anmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    c. Öffnen Sie das Base64-codierte Zertifikat im Editor, das Sie aus dem Azure-Portal heruntergeladen haben, kopieren Sie den Inhalt des Zertifikats in die Zwischenablage, und fügen Sie ihn anschließend in das Textfeld **X.509-Zertifikat** ein.
  
    d. Geben Sie in der Zuordnung des SAML-Attributs für das Textfeld „Vorname“ den Wert als `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` ein.

    e. Geben Sie in der Zuordnung des SAML-Attributs für das Textfeld **Nachname** den Wert als `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` ein.

    f. Geben Sie in der Zuordnung des SAML-Attributs für das Textfeld **E-Mail** den Wert als `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` ein.

5. Um die Konfiguration zu überprüfen, klicken Sie auf die Schaltfläche **Testen**.

    >[!NOTE]
    >Zum erfolgreichen Testen müssen Sie den Konfigurations-Assistenten in Azure AD abschließen und den Benutzer oder Gruppen, die den Test durchführen können, Zugriff gewähren.

6. Aktivieren Sie SSO, indem Sie auf die Schaltfläche **SSO aktivieren** klicken.

7. Klicken Sie nun auf die Schaltfläche **Update**, damit alle Einstellungen gespeichert werden. Dadurch wird den Wert RelayState generiert. Kopieren Sie den RelayState-Wert, der im Textfeld generiert wird, und fügen Sie ihn in das Textfeld **Relayzustand** im Abschnitt **Domäne und URLs für Lifesize Cloud** ein.

### <a name="create-lifesize-cloud-test-user"></a>Erstellen eines Lifesize Cloud-Testbenutzers

In diesem Abschnitt erstellen Sie in Lifesize Cloud einen Benutzer namens Britta Simon. Lifesize Cloud unterstützt die automatische Benutzerbereitstellung. Nach erfolgreicher Authentifizierung bei Azure AD wird der Benutzer automatisch in der Anwendung bereitgestellt.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für Lifesize Cloud weitergeleitet, wo Sie den Anmeldeflow initiieren können. 

* Rufen Sie direkt die Lifesize Cloud-Anmelde-URL auf, und initiieren Sie den Anmeldeflow.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie in „Meine Apps“ auf die Kachel „Lifesize Cloud“ klicken, werden Sie zur Anmelde-URL für Lifesize Cloud weitergeleitet. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Lifesize Cloud können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-aad)
