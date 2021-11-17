---
title: 'Tutorial: Azure AD-SSO-Integration mit Civic Platform'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Civic Platform konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/30/2021
ms.author: jeedes
ms.openlocfilehash: a809f29010a914c62110387f7b1b6cdc73d7b5a1
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132292037"
---
# <a name="tutorial-azure-ad-sso-integration-with-civic-platform"></a>Tutorial: Azure AD-SSO-Integration mit Civic Platform

In diesem Tutorial erfahren Sie, wie Sie Civic Platform in Azure Active Directory (Azure AD) integrieren. Bei der Integration von Civic Platform in Azure AD haben Sie folgende Möglichkeiten:

* Steuern Sie in Azure AD, wer Zugriff auf Civic Platform hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Civic Platform anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Civic Platform-Abonnement, für das einmaliges Anmelden (SSO) aktiviert ist.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Civic Platform unterstützt **SP-initiiertes** einmaliges Anmelden.

> [!NOTE]
> Der Bezeichner dieser Anwendung ist ein fester Zeichenfolgenwert, daher kann in einem Mandanten nur eine Instanz konfiguriert werden.

## <a name="add-civic-platform-from-the-gallery"></a>Hinzufügen von Civic Platform aus dem Katalog

Zum Konfigurieren der Integration von Civic Platform in Azure AD müssen Sie Civic Platform aus dem Katalog der Liste der verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Civic Platform** in das Suchfeld ein.
1. Wählen Sie **Civic Platform** im Ergebnisbereich aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-civic-platform"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Civic Platform

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Civic Platform mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Civic Platform eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Civic Platform die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Civic Platform](#configure-civic-platform-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
    1. **[Erstellen eines Civic Platform-Testbenutzers](#create-civic-platform-test-user)** , um ein Pendant von B. Simon in Civic Platform zu erhalten, das mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Civic Platform** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    a. Geben Sie im Textfeld **Bezeichner (Entitäts-ID)** den folgenden Wert ein: `civicplatform.accela.com`.

    b. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<SUBDOMAIN>.accela.com`.

    > [!NOTE]
    > Der Wert der Anmelde-URL entspricht nicht dem tatsächlichen Wert. Ersetzen Sie diesen Wert durch die tatsächliche Anmelde-URL. Wenden Sie sich an das [Clientsupportteam von Civic Platform](mailto:skale@accela.com), um diese Werte zu erhalten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf die Schaltfläche „Kopieren“, um die **App-Verbundmetadaten-URL** zu kopieren, und speichern Sie sie auf Ihrem Computer.

    ![Screenshot der Seite „S A M L-Signaturzertifikat“, auf der Sie die App-Verbundmetadaten-U R L kopieren können](common/copy-metadataurl.png)

1. Navigieren Sie in Azure AD zu **Azure Active Directory** > **App-Registrierungen**, und wählen Sie Ihre Anwendung aus.

1. Kopieren Sie die **Verzeichnis-ID (Mandant)** , und speichern Sie sie im Editor.

    ![Verzeichnis-ID (Mandanten-ID) kopieren und im Anwendungscode speichern](media/civic-platform-tutorial/directory.png)

1. Kopieren Sie die **Anwendungs-ID**, und speichern Sie sie im Editor.

   ![Anwendungs-ID (Client-ID) kopieren](media/civic-platform-tutorial/application.png)

1. Navigieren Sie in Azure AD zu **Azure Active Directory** > **App-Registrierungen**, und wählen Sie Ihre Anwendung aus. Wählen Sie **Zertifikate & Geheimnisse** aus.

1. Wählen Sie **Geheime Clientschlüssel -> Neuer geheimer Clientschlüssel** aus.

1. Geben Sie eine Beschreibung des Geheimnisses und eine Dauer ein. Wählen Sie anschließend **Hinzufügen** aus.

   > [!NOTE]
   > Nachdem der geheime Clientschlüssel gespeichert wurde, wird dessen Wert angezeigt. Kopieren Sie diesen Wert jetzt, da Sie den Schlüssel später nicht mehr abrufen können.

   ![Den geheimen Wert kopieren, weil er später nicht mehr abgerufen werden kann](media/civic-platform-tutorial/secret-key.png)

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Civic Platform gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Civic Platform** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn Sie einen beliebigen Rollenwert in der SAML-Assertion erwarten, wählen Sie im Dialogfeld **Rolle auswählen** die entsprechende Rolle für den Benutzer in der Liste aus, und klicken Sie dann im unteren Bildschirmbereich auf die Schaltfläche **Auswählen**.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-civic-platform-sso"></a>Konfigurieren des einmaligen Anmeldens (SSO) für Civic Platform

1. Melden Sie sich in einem neuen Webbrowserfenster bei der Atlassian Cloud-Unternehmenswebsite als Administrator an.

1. Klicken Sie auf **Standard Choices** (Standardauswahl).

    ![Screenshot der Atlassian Cloud-Website, auf der unter „Administrator Tools“ die Option „Standard Choices“ hervorgehoben ist](media/civic-platform-tutorial/standard-choices.png)

1. Erstellen Sie die Standardauswahl **ssoconfig**.

1. Suchen Sie nach **ssoconfig**, und übermitteln Sie diese.

    ![Screenshot der Standard Choices-Suche mit der Eingabe „S S O Config“](media/civic-platform-tutorial/item.png)

1. Erweitern Sie SSOCONFIG, indem Sie auf den roten Punkt klicken.

    ![Screenshot der Standard Choices-Auswahl, in der S S O CONFIG verfügbar ist](media/civic-platform-tutorial/details.png)

1. Geben Sie im folgenden Schritt auf das einmalige Anmelden (SSO) bezogene Konfigurationsinformationen an:

    ![Screenshot der Standard Choices-Elementbearbeitung für S S O CONFIG](media/civic-platform-tutorial/values.png)

    1. Geben Sie im Feld **applicationid** den Wert für die **Anwendungs-ID** ein, den Sie aus dem Azure-Portal kopiert haben.

    1. Geben Sie im Feld **clientSecret** den Wert für das **Geheimnis** ein, den Sie aus dem Azure-Portal kopiert haben.

    1. Geben Sie im Feld **directoryId** den Wert für die **Verzeichnis-ID (Mandant)** ein, den Sie aus dem Azure-Portal kopiert haben.

    1. Geben Sie den „idpNamen“ ein. Beispiel:- `Azure`.

### <a name="create-civic-platform-test-user"></a>Erstellen eines Civic Platform-Testbenutzers

In diesem Abschnitt erstellen Sie in Civic Platform einen Benutzer namens B. Simon. Arbeiten Sie mit dem Civic Platform-Supportteam zusammen, um die Benutzer dem [Civic Platform Client-Supportteam](mailto:skale@accela.com) hinzuzufügen. Benutzer müssen erstellt und aktiviert werden, damit Sie einmaliges Anmelden verwenden können.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für Civic Platform weitergeleitet, wo Sie den Anmeldeflow initiieren können. 

* Navigieren Sie direkt zur Anmelde-URL für Civic Platform, und initiieren Sie dort den Anmeldeflow.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie in „Meine Apps“ auf die Kachel „Civic Platform“ klicken, werden Sie zur Anmelde-URL für Civic Platform weitergeleitet. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Civic Platform können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-aad)
