---
title: 'Tutorial: Azure Active Directory-Integration mit Costpoint | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Costpoint konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/11/2021
ms.author: jeedes
ms.openlocfilehash: 476b11ba71bf9b8aa829ab21bcfbfefa9365c5fa
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132288009"
---
# <a name="tutorial-integrate-costpoint-with-azure-active-directory"></a>Tutorial: Integrieren von Costpoint in Azure Active Directory

In diesem Tutorial erfahren Sie, wie Sie Costpoint in Azure Active Directory (Azure AD) integrieren. Bei der Integration von Costpoint in Azure AD haben Sie folgende Möglichkeiten:

* Steuern Sie in Azure AD, wer Zugriff auf Costpoint hat.
* Ermöglichen Sie Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Costpoint anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Costpoint-Abonnement, für das das einmalige Anmelden (SSO) aktiviert ist.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung. 

* Costpoint unterstützt **SP- und IDP**-initiiertes einmaliges Anmelden.

## <a name="generate-costpoint-metadata"></a>Generieren von Costpoint-Metadaten

Die SAML-SSO-Konfiguration für Costpoint wird im Leitfaden **DeltekCostpoint711Security.pdf** erläutert. Laden Sie diesen Leitfaden von der Deltek Costpoint-Supportwebsite herunter, und sehen Sie sich die Abschnitte **SAML Single Sign-on Setup** (SAML-SSO-Einrichtung) > **Configure SAML Single Sign-on between Costpoint and Microsoft Azure** (Konfigurieren von SSO für SAML zwischen Costpoint und Azure AD) an. Befolgen Sie die Anweisungen, und generieren Sie die Datei **Costpoint SP Federation Metadata XML** (Costpoint-SP-Verbundmetadaten-XML). 

![Screenshot des Fensters „Product Configuration Utility“, in dem die Registerkarte „WebLogic – Security“ ausgewählt ist](./media/costpoint-tutorial/configuration-utility.png)

## <a name="add-costpoint-from-the-gallery"></a>Hinzufügen von Costpoint aus dem Katalog

Zum Konfigurieren der Integration von Costpoint in Azure AD müssen Sie Costpoint aus dem Katalog der Liste der verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Costpoint** in das Suchfeld ein.
1. Wählen Sie **Costpoint** im Ergebnisbereich aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-costpoint"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Costpoint

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Costpoint mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Costpoint eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Costpoint die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Costpoint](#configure-costpoint-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines Costpoint-Testbenutzers](#create-costpoint-test-user)** , um ein Pendant von B. Simon in Costpoint zu erhalten, das mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren:

1. Wählen Sie auf der Anwendungsintegrationsseite für **Costpoint** die Option **Einmaliges Anmelden** aus.

1. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus, wenn Sie über eine **Dienstanbieter-Metadatendatei** verfügen:

   > [!NOTE]
   > Die Dienstanbieter-Metadatendatei können Sie unter [Generieren von Costpoint-Metadaten](#generate-costpoint-metadata) abrufen. Die Verwendung der Datei wird weiter unten im Tutorial erläutert.
 
   1. Wählen Sie die Schaltfläche **Metadatendatei hochladen** und dann die Datei **Costpoint SP Federation Metadata XML** (Costpoint-SP-Verbundmetadaten-XML) aus, die zuvor von Costpoint generiert wurde. Wählen Sie anschließend die Schaltfläche **Hinzufügen** aus, um die Datei hochzuladen.

      ![Hochladen der Metadatendatei](./media/costpoint-tutorial/upload-metadata.png)
    
   1. Nachdem die Metadatendatei erfolgreich hochgeladen wurde, werden die Werte **Bezeichner** und **Antwort-URL** im Costpoint-Abschnitt automatisch aufgefüllt.

      > [!NOTE]
      > Falls die Werte **Bezeichner** und **Antwort-URL** nicht automatisch aufgefüllt werden, geben Sie die erforderlichen Werte manuell ein. Vergewissern Sie sich, dass **Bezeichner (Entitäts-ID)** und **Antwort-URL (Assertionsverbraucherdienst-URL)** richtig festgelegt sind und es sich beim Wert unter **ACS-URL** um eine gültige Costpoint-URL handelt, die auf **/LoginServlet.cps** endet.

   1. Wählen Sie **Zusätzliche URLs festlegen** aus. Geben Sie für **Relayzustand** einen Wert im folgenden Format ein: `system=[your system]` (Beispiel: **system=DELTEKCP**).

1. Wählen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** das **Kopiersymbol** aus, um die **App-Verbundmetadaten-URL** zu kopieren, und speichern Sie sie im Editor.

   ![SAML-Signaturzertifikat](common/copy-metadataurl.png)

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Costpoint gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Costpoint** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-costpoint-sso"></a>Konfigurieren des einmaligen Anmeldens für Costpoint

1. Wechseln Sie zurück zum Costpoint-Konfigurationshilfsprogramm. Fügen Sie in das Textfeld **IdP Federation Metadata XML** (IdP-Verbundmetadaten-XML) den Inhalt der Datei *App-Verbundmetadaten-URL* ein. 

   ![Costpoint-Konfigurationshilfsprogramm](./media/costpoint-tutorial/configuration-utility-metadata.png)

1. Befolgen Sie die übrigen Anweisungen im Leitfaden **DeltekCostpoint711Security.pdf**, um die Costpoint-SAML-Einrichtung abzuschließen.

### <a name="create-costpoint-test-user"></a>Erstellen eines Costpoint-Testbenutzers

In diesem Abschnitt erstellen Sie einen Benutzer in Costpoint. Angenommen, die Benutzer-ID lautet **B.SIMON** und der Benutzername **B.Simon**. Arbeiten Sie mit dem [Supportteam für den Costpoint-Client](https://www.deltek.com/about/contact-us) zusammen, um die Benutzer zur Costpoint-Plattform hinzuzufügen. Der Benutzer muss erstellt und aktiviert werden, damit er einmaliges Anmelden verwenden kann.

Nach der Erstellung des Benutzers muss für **Authentication Method** (Authentifizierungsmethode) die Option **Active Directory** ausgewählt werden, das Kontrollkästchen **SAML Single Sign-on** (Einmaliges Anmelden für SAML) muss aktiviert werden, und für **Active Directory or Certificate ID** (Active Directory- oder Zertifikat-ID) muss der Benutzername von Azure Active Directory angegeben werden, wie im folgenden Screenshot gezeigt:

![Costpoint-Benutzer](./media/costpoint-tutorial/costpoint-user.png)

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

#### <a name="sp-initiated"></a>SP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für Costpoint weitergeleitet, wo Sie den Anmeldeflow initiieren können.  

* Rufen Sie direkt die Costpoint-Anmelde-URL auf, und initiieren Sie den Anmeldeflow.

#### <a name="idp-initiated"></a>IDP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch sollten Sie automatisch bei der Costpoint-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. 

Sie können auch den Microsoft-Bereich „Meine Apps“ verwenden, um die Anwendung in einem beliebigen Modus zu testen. Beim Klicken auf die Kachel „Costpoint“ in „Meine Apps“ geschieht Folgendes: Wenn Sie den SP-Modus konfiguriert haben, werden Sie zum Initiieren des Anmeldeflows zur Anmeldeseite der Anwendung weitergeleitet. Wenn Sie den IDP-Modus konfiguriert haben, sollten Sie automatisch bei der Costpoint-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Costpoint können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-any-app)
