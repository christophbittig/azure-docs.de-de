---
title: 'Tutorial: Azure Active Directory-Integration mit Palo Alto Networks Captive Portal | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Palo Alto Networks Captive Portal konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/10/2020
ms.author: jeedes
ms.openlocfilehash: 7b23857f3a14bc659f2c3a16113d6b238cc3a3a2
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132344330"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks-captive-portal"></a>Tutorial: Azure Active Directory-Integration mit Palo Alto Networks Captive Portal

In diesem Tutorial erfahren Sie, wie Sie Palo Alto Networks Captive Portal in Azure Active Directory (Azure AD) integrieren.
Die Integration von Palo Alto Networks Captive Portal in Azure AD bietet Ihnen folgende Vorteile:

* Sie können in Azure AD steuern, wer Zugriff auf Palo Alto Networks Captive Portal hat.
* Sie können es Ihren Benutzern ermöglichen, dass sie mit ihren Azure AD-Konten automatisch bei Palo Alto Networks Captive Portal angemeldet werden (einmaliges Anmelden; Single Sign-On, SSO).
* Sie können Ihre Konten über das Azure-Portal an einem zentralen Ort verwalten.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit Palo Alto Networks Captive Portal einrichten zu können, benötigen Sie Folgendes:

* Ein Azure Active Directory-Abonnement. Falls Sie nicht über Azure AD verfügen, können Sie eine [einmonatige Testversion](https://azure.microsoft.com/pricing/free-trial/) verwenden.
* Ein SSO-fähiges Abonnement für Palo Alto Networks Captive Portal.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Palo Alto Networks Captive Portal unterstützt **IDP**-initiiertes einmaliges Anmelden.
* Palo Alto Networks Captive Portal unterstützt die **Just-In-Time**-Benutzerbereitstellung.

## <a name="adding-palo-alto-networks-captive-portal-from-the-gallery"></a>Hinzufügen von Palo Alto Networks Captive Portal aus dem Katalog

Zum Konfigurieren der Integration von Palo Alto Networks Captive Portal in Azure AD müssen Sie Palo Alto Networks Captive Portal aus dem Katalog zur Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Palo Alto Networks Captive Portal** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Palo Alto Networks Captive Portal** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso"></a>Konfigurieren und Testen des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Palo Alto Networks Captive Portal mithilfe eines Testbenutzers namens **B.Simon**.
Damit einmaliges Anmelden funktioniert, muss zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Palo Alto Networks Captive Portal eine Linkbeziehung eingerichtet werden.

Führen Sie zum Konfigurieren des einmaligen Anmeldens von Azure AD mit Palo Alto Networks Captive Portal die folgenden Schritte aus:

1. **[Konfigurieren des einmalige Anmeldens von Azure AD](#configure-azure-ad-sso)** , um dem Benutzer die Nutzung dieses Features zu ermöglichen
    * **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Benutzer B. Simon zu testen
    * **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon für die Verwendung des einmaligen Anmeldens von Azure AD einzurichten
2. **[Konfigurieren des einmaligen Anmeldens für Palo Alto Networks Captive Portal](#configure-palo-alto-networks-captive-portal-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    * **[Erstellen eines Palo Alto Networks Captive Portal-Testbenutzers](#create-a-palo-alto-networks-captive-portal-test-user)** , um ein Pendant von B. Simon in Palo Alto Networks Captive Portal zu erhalten, das mit ihrer Darstellung in Azure AD verknüpft ist
3. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Palo Alto Networks Captive Portal** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Bereich **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

   1. Geben Sie unter **Bezeichner** eine URL mit dem Muster `https://<customer_firewall_host_name>/SAML20/SP` ein.

   2. Geben Sie unter **Antwort-URL** eine URL mit dem Muster `https://<customer_firewall_host_name>/SAML20/SP/ACS` ein.

      > [!NOTE]
      > Aktualisieren Sie die Platzhalterwerte in diesem Schritt mit den tatsächlichen Werten für Bezeichner und Antwort-URL. Die tatsächlichen Werte erhalten Sie vom [Clientsupportteam für Palo Alto Networks Captive Portal](https://support.paloaltonetworks.com/support).

5. Wählen Sie im Abschnitt **SAML-Signaturzertifikat** neben **Verbundmetadaten-XML** die Option **Herunterladen** aus. Speichern Sie die heruntergeladene Datei auf Ihrem Computer.

    ![Downloadlink für Verbundmetadaten-XML](common/metadataxml.png)

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Palo Alto Networks Captive Portal gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Palo Alto Networks Captive Portal** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-palo-alto-networks-captive-portal-sso"></a>Konfigurieren des einmaligen Anmeldens für Palo Alto Networks Captive Portal

Richten Sie als Nächstes das einmalige Anmelden in Palo Alto Networks Captive Portal ein:

1. Melden Sie sich in einem anderen Browserfenster als Administrator bei der Palo Alto Networks-Website an.

2. Klicken Sie auf die Registerkarte **Device** (Gerät).

    ![Geräteregisterkarte der Palo Alto Networks-Website](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin1.png)

3. Wählen Sie im Menü die Option **SAML Identity Provider** (SAML-Identitätsanbieter) und anschließend **Import** (Importieren) aus.

    ![Importschaltfläche](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin2.png)

4. Führen Sie im Dialogfeld **SAML Identity Provider Server Profile Import** (SAML-Identitätsanbieter: Importieren des Serverprofils) die folgenden Schritte aus:

    ![Konfigurieren des einmaligen Anmeldens für Palo Alto Networks](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin3.png)

    1. Geben Sie einen **Profilnamen** ein (beispielsweise **AzureAD-CaptivePortal**).
    
    2. Wählen Sie neben **Identity Provider Metadata** (Metadaten des Identitätsanbieters) die Option **Browse** (Durchsuchen) aus. Wählen Sie die Datei „metadata.xml“ aus, die Sie im Azure-Portal heruntergeladen haben.
    
    3. Klicken Sie auf **OK**.

### <a name="create-a-palo-alto-networks-captive-portal-test-user"></a>Erstellen eines Testbenutzers für Palo Alto Networks Captive Portal

Erstellen Sie als Nächstes in Palo Alto Networks Captive Portal einen Benutzer namens *Britta Simon*. Palo Alto Networks Captive Portal unterstützt die Just-in-Time-Benutzerbereitstellung, und sie ist standardmäßig aktiviert. In diesem Abschnitt müssen keine Aufgaben ausgeführt werden. Falls ein Benutzer noch nicht in Palo Alto Networks Captive Portal vorhanden ist, wird nach der Authentifizierung ein neuer Benutzer erstellt.

> [!NOTE]
> Setzen Sie sich mit dem [Clientsupportteam für Palo Alto Networks Captive Portal](https://support.paloaltonetworks.com/support) in Verbindung, wenn Sie einen Benutzer manuell erstellen möchten.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen:

* Klicken Sie im Azure-Portal auf „Diese Anwendung testen“. Dadurch sollten Sie automatisch bei der Palo Alto Networks Captive Portal-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie in „Meine Apps“ auf die Kachel „Palo Alto Networks Captive Portal“ klicken, sollten Sie automatisch bei der Instanz für Palo Alto Networks Captive Portal angemeldet werden, für die Sie das einmalige Anmelden eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).


## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Palo Alto Networks Captive Portal können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-any-app)
