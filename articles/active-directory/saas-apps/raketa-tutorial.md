---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Raketa | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Raketa konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/28/2020
ms.author: jeedes
ms.openlocfilehash: 0d4f9439c99b2e9496a8ae14e441ee471bea77bb
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132306641"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-raketa"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Raketa

In diesem Tutorial erfahren Sie, wie Sie Raketa in Azure Active Directory (Azure AD) integrieren. Die Integration von Raketa in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf Raketa hat.
* Ermöglichen Sie Ihren Benutzern, sich mit ihrem Azure AD-Konto automatisch bei Raketa anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

Weitere Informationen zur Integration von SaaS-Apps in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Raketa-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Raketa unterstützt **SP-initiiertes** einmaliges Anmelden.
* Nach dem Konfigurieren von Raketa können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-any-app)

## <a name="adding-raketa-from-the-gallery"></a>Hinzufügen von Raketa aus dem Katalog

Zum Konfigurieren der Integration von Raketa in Azure AD müssen Sie Raketa aus dem Katalog zur Liste der verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** [1] aus.

    ![rkt_1](./media/raketa-tutorial/azure-active-directory.png)

1. Navigieren Sie zu **Unternehmensanwendungen** [2], und wählen Sie dann **Alle Anwendungen** [3] aus.

1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** [4] aus. 

    ![rkt_2](./media/raketa-tutorial/new-app.png)

1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** [5] den Suchbegriff **Raketa** in das Suchfeld [6] ein.

1. Wählen Sie im Ergebnisbereich [7] **Raketa** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen** [8]. 

    ![rkt_3](./media/raketa-tutorial/add-btn.png)


## <a name="configure-and-test-azure-ad-single-sign-on-for-raketa"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Raketa

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Raketa mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Raketa eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Raketa die folgenden Bausteine aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Raketa](#configure-raketa-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines Raketa-Testbenutzers](#create-raketa-test-user)** , um eine Entsprechung von B. Simon in Raketa zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) auf der Anwendungsintegrationsseite für **Raketa** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** [9] aus.

    ![rkt_4](./media/raketa-tutorial/manage-sso.png)

1. Wählen Sie auf der Seite **SSO-Methode auswählen** [9] die Methode **SAML** [10] aus.

    ![rkt_5](./media/raketa-tutorial/saml.png)

1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Bearbeitungs- bzw. Stiftsymbol für **Grundlegende SAML-Konfiguration** [11], um die Einstellungen zu bearbeiten.

1. Geben Sie im Abschnitt **Grundlegende SAML-Konfiguration** die Werte für die folgenden Felder ein:

    1. Geben Sie in die Textfelder **Bezeichner (Entitäts-ID)** [12] und **Anmelde-URL** [14] die folgende URL ein: `https://raketa.travel/`.

    1. Geben Sie im Textfeld **Antwort-URL** [13] eine URL im folgenden Format ein: `https://raketa.travel/sso/acs?clientId=<CLIENT_ID>`.  

    ![rkt_6](./media/raketa-tutorial/enter-urls.png)

    > [!NOTE]
    > Der Wert der Antwort-URL entspricht nicht dem tatsächlichen Wert. Aktualisieren Sie den Wert mit der richtigen Antwort-URL. Wenden Sie sich an das [Clientsupportteam von Raketa](mailto:help@raketa.travel), um diesen Wert zu erhalten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

1. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** zum Eintrag **Zertifikat (Base64)** . Wählen Sie **Herunterladen** [15] aus, um das Zertifikat herunterzuladen, und speichern Sie es auf Ihrem Computer.

1. Kopieren Sie im Abschnitt **Raketa einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    1. Anmelde-URL [16]: Die URL der Autorisierungswebseite, die zum Umleiten der Benutzer zum Authentifizierungssystem verwendet wird

    1. Azure AD-Bezeichner [17]: Azure AD-Bezeichner

    1. Abmelde-URL [18]: Die URL der Webseite, die zum Umleiten der Benutzer nach der Abmeldung verwendet wird

    ![rkt_7](./media/raketa-tutorial/copy-urls.png)


### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

In diesem Abschnitt erstellen Sie im Azure-Portal einen Testbenutzer mit dem Namen B. Simon.

1. Wählen Sie im linken Bereich des Azure-Portals **Azure Active Directory** [1] > **Benutzer** [19] > **Alle Benutzer** [20] aus.

1. Wählen Sie oben auf dem Bildschirm die Option **Neuer Benutzer** [21] aus.

    ![rkt_8](./media/raketa-tutorial/new-user.png)

1. Führen Sie unter den Eigenschaften für **Benutzer** die folgenden Schritte aus:

   1. Geben Sie im Feld **Benutzername** [22] die Zeichenfolge username@companydomain.extension ein. Beispiel: `B.Simon@contoso.com`.

   1. Geben Sie im Feld **Name** [23] die Zeichenfolge `B.Simon` ein.

   1. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen** [25], und notieren Sie sich den Wert aus dem Feld **Kennwort** [24].

   1. Klicken Sie auf **Erstellen** [26]. 

    ![rkt_9](./media/raketa-tutorial/create-user.png)


### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Raketa gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** [2] > **Alle Anwendungen** [3] aus.

1. Wählen Sie in der Anwendungsliste **Raketa** [27] aus.  

    ![rkt_10](./media/raketa-tutorial/add-raketa.png)

1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** [28] aus. 

    ![rkt_11](./media/raketa-tutorial/users-groups.png)

1. Wählen Sie **Benutzer hinzufügen** [29] und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** [30] aus.

    ![rkt_12](./media/raketa-tutorial/add-user-raketa.png)

1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** [31] aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen** [32].

1. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** die entsprechende Rolle für den Benutzer in der Liste aus, und klicken Sie dann im unteren Bildschirmbereich auf die Schaltfläche **Auswählen**.

1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen** [33]. 

    ![rkt_13](./media/raketa-tutorial/assign-user.png)

## <a name="configure-raketa-sso"></a>Konfigurieren des einmaligen Anmeldens für Raketa

Zum Konfigurieren des einmaligen Anmeldens aufseiten von **Raketa** müssen Sie das heruntergeladene **Zertifikat (Base64)** und die kopierten URLs aus dem Azure-Portal an das [Supportteam von Raketa](mailto:help@raketa.travel) senden. Es führt die Einrichtung durch, damit die SAML-SSO-Verbindung auf beiden Seiten richtig festgelegt ist.

### <a name="create-raketa-test-user"></a>Erstellen eines Raketa-Testbenutzers

In diesem Abschnitt wird in Raketa ein Benutzer namens B. Simon erstellt. Arbeiten Sie mit dem [Supportteam von Raketa](mailto:help@raketa.travel) zusammen, um die Benutzer auf der Raketa-Plattform hinzuzufügen. Benutzer müssen erstellt und aktiviert werden, damit Sie einmaliges Anmelden verwenden können.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden über den Zugriffsbereich.

Wenn Sie im Zugriffsbereich auf die Kachel „Raketa“ klicken, sollten Sie automatisch bei der Raketa-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

- [Liste mit den Tutorials zur Integration von SaaS-Apps in Azure Active Directory](./tutorial-list.md)

- [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

- [Was ist der bedingte Zugriff in Azure Active Directory?](../conditional-access/overview.md)

- [Raketa mit Azure AD ausprobieren](https://aad.portal.azure.com/)

- [Was ist die Sitzungssteuerung in Microsoft Defender for Cloud Apps?](/cloud-app-security/proxy-intro-aad)

- [Schützen von Apps mit der App-Steuerung für bedingten Zugriff von Microsoft Cloud App Security](/cloud-app-security/proxy-intro-aad)
