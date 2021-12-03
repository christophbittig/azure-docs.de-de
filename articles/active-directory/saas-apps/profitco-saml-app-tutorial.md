---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD mit Profit.co'
description: Hier erfahren Sie, wie Sie einmaliges Anmelden zwischen Azure Active Directory und Profit.co konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/20/2021
ms.author: jeedes
ms.openlocfilehash: ae8e76d33dbab90478a361c16f8f3abbb3346e36
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132341766"
---
# <a name="tutorial-azure-ad-sso-integration-with-profitco"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD mit Profit.co

In diesem Tutorial erfahren Sie, wie Sie Profit.co in Azure Active Directory (Azure AD) integrieren. Die Integration von Profit.co in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf Profit.co hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Profit.co anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Profit.co-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Profit.co unterstützt IDP-initiiertes einmaliges Anmelden.

## <a name="add-profitco-from-the-gallery"></a>Hinzufügen von Profit.co aus dem Katalog

Zum Konfigurieren der Integration von Profit.co in Azure AD müssen Sie Profit.co aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Profit.co** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Profit.co** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-profitco"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Profit.co

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Profit.co mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, richten Sie eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Profit.co ein.

Nachfolgend sind die allgemeinen Schritte zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Profit.co aufgeführt:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Profit.co](#configure-profitco-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines Profit.co-Testbenutzers](#create-a-profitco-test-user)** , um ein Pendant von B. Simon in Profit.co zu erhalten. Dieses Pendant ist mit der Darstellung des Benutzers in Azure AD verknüpft.
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Profit.co** zum Abschnitt **Verwalten**. Wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Wählen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** das Stiftsymbol für **Grundlegende SAML-Konfiguration** aus, um die Einstellungen zu bearbeiten.

   ![Screenshot: Seite „Einmaliges Anmelden (SSO) mit SAML einrichten“ mit hervorgehobenem Stiftsymbol](common/edit-urls.png)

1. Im Abschnitt **Grundlegende SAML-Konfiguration** ist die Anwendung vorkonfiguriert, und die notwendigen URLs sind in Azure bereits aufgefüllt. Benutzer müssen die Konfiguration speichern, indem sie die Schaltfläche **Speichern** auswählen.

1. Wählen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** die Schaltfläche **Kopieren** aus. Dadurch wird die **App-Verbundmetadaten-URL** kopiert und auf Ihrem Computer gespeichert.

    ![Screenshot: „SAML-Signaturzertifikat“ mit hervorgehobener Kopierschaltfläche](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

In diesem Abschnitt erstellen Sie im Azure-Portal einen Testbenutzer namens B.Simon.

1. Wählen Sie im Azure-Portal im linken Bereich **Azure Active Directory** > **Benutzer** > **Alle Benutzer** aus.
1. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.
1. Führen Sie unter den Eigenschaften für **Benutzer** die folgenden Schritte aus:
   1. Geben Sie im Feld **Name** die Zeichenfolge `B.Simon` ein.  
   1. Geben Sie im Feld **Benutzername** die Zeichenfolge username@companydomain.extension ein. Beispiel: `B.Simon@contoso.com`.
   1. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**, und notieren Sie sich den Wert, der im Feld **Kennwort** angezeigt wird.
   1. Klicken Sie auf **Erstellen**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Profit.co gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Profit.co** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Klicken Sie auf **Benutzer hinzufügen**. Wählen Sie im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ die Option **B.Simon** aus. Wählen Sie dann unten auf dem Bildschirm die Schaltfläche **Auswählen** aus.
1. Falls Sie in der SAML-Assertion einen Rollenwert erwarten, wählen Sie im Dialogfeld **Rolle auswählen** die entsprechende Rolle für den Benutzer aus der Liste aus. Wählen Sie dann unten auf dem Bildschirm die Schaltfläche **Auswählen** aus.
1. Wählen Sie im Dialogfeld **Zuweisung hinzufügen** die Option **Zuweisen** aus.

## <a name="configure-profitco-sso"></a>Konfigurieren des einmaligen Anmeldens für Profit.co

Um einmaliges Anmelden aufseiten von Profit.co zu konfigurieren, müssen Sie die App-Verbundmetadaten-URL an das [Profit.co-Supportteam](mailto:support@profit.co) senden. Das Team konfiguriert diese Einstellung, damit die SAML-SSO-Verbindung auf beiden Seiten richtig festgelegt ist.

### <a name="create-a-profitco-test-user"></a>Erstellen eines Profit.co-Testbenutzers

In diesem Abschnitt erstellen Sie in Profit.co einen Benutzer mit dem Namen B. Simon. Wenden Sie sich an das [Profit.co-Supportteam](mailto:support@profit.co), um die Benutzer auf der Profit.co-Plattform hinzuzufügen. Sie können einmaliges Anmelden erst verwenden, wenn Sie Benutzer erstellt und aktiviert haben.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen:

* Klicken Sie im Azure-Portal auf „Diese Anwendung testen“. Dadurch sollten Sie automatisch bei der Profit.co-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie unter „Meine Apps“ auf die Kachel „Profit.co“ klicken, sollten Sie automatisch bei der Profit.co-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Profit.co können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-aad)
