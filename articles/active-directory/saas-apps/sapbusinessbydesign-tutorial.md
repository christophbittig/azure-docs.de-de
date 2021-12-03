---
title: 'Tutorial: Azure Active Directory-Integration mit SAP Business ByDesign | Microsoft Docs'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und SAP Business ByDesign konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/22/2021
ms.author: jeedes
ms.openlocfilehash: 69fceff94faebb3acce3a4c512d811841d455ce5
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132341443"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-business-bydesign"></a>Tutorial: Azure Active Directory-Integration mit SAP Business ByDesign

In diesem Tutorial erfahren Sie, wie Sie SAP Business ByDesign in Azure Active Directory (Azure AD) integrieren. Die Integration von SAP Business ByDesign in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf SAP Business ByDesign hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei SAP Business ByDesign anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein SAP Business ByDesign-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* SAP Business ByDesign unterstützt **SP**-initiiertes einmaliges Anmelden.

## <a name="add-sap-business-bydesign-from-the-gallery"></a>Hinzufügen von SAP Business ByDesign aus dem Katalog

Zum Konfigurieren der Integration von SAP Business ByDesign in Azure AD müssen Sie SAP Business ByDesign aus dem Katalog der Liste der verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **SAP Business ByDesign** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **SAP Business ByDesign** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso"></a>Konfigurieren und Testen des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit SAP Business ByDesign mithilfe eines Testbenutzers namens **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in SAP Business ByDesign eingerichtet werden.

Führen Sie die folgenden Schritte aus, um das einmalige Anmelden von Azure AD mit SAP Business ByDesign zu konfigurieren und zu testen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für SAP Business ByDesign](#configure-sap-business-bydesign-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines SAP Business ByDesign-Testbenutzers](#create-sap-business-bydesign-test-user)** , um eine Entsprechung von Britta Simon in SAP Business ByDesign zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist.
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

### <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **SAP Business ByDesign** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    a. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<servername>.sapbydesign.com`.

    b. Geben Sie im Textfeld **Bezeichner (Entitäts-ID)** eine URL im folgenden Format ein: `https://<servername>.sapbydesign.com`.

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch die tatsächliche Anmelde-URL und den tatsächlichen Bezeichner. Wenden Sie sich an das [Clientsupportteam von SAP Business ByDesign](https://www.sap.com/products/cloud-analytics.support.html), um diese Werte zu erhalten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

5. Die SAP Business ByDesign-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Konfigurieren Sie die folgenden Ansprüche für diese Anwendung. Sie können die Werte dieser Attribute im Abschnitt **Benutzerattribute** auf der Anwendungsintegrationsseite verwalten. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf die Schaltfläche **Bearbeiten**, um das Dialogfeld **Benutzerattribute** zu öffnen.

    ![image1](common/edit-attribute.png)

6. Klicken Sie auf das Symbol **Bearbeiten**, um den **Wert für Namensbezeichner** zu bearbeiten.

    ![Bild2](media/sapbusinessbydesign-tutorial/mail-prefix1.png)

7. Führen Sie im Abschnitt **Benutzeransprüche verwalten** die folgenden Schritte aus:

    ![image3](media/sapbusinessbydesign-tutorial/mail-prefix2.png)

    a. Wählen Sie als **Quelle** die Option **Transformation** aus.

    b. Wählen Sie in der Dropdownliste **Transformation** den Eintrag **ExtractMailPrefix()** aus.

    > [!NOTE]
    > ByD verwendet für die Benutzerzuordnung standardmäßig das NameID-Format **unspecified**. ByD ordnet die Namens-ID (NameID) der SAML-Assertionen für den ByD-Benutzeralias zu. Darüber hinaus unterstützt ByD das Namens-ID-Format **emailAddress**. In diesem Fall ordnet ByD die Namens-ID (NameID) der SAML-Assertion für die ByD-Benutzer-E-Mail-Adresse der Kontaktdaten des ByD-Mitarbeiters zu. Ausführlichere Informationen finden Sie in [diesem SAP-Blog](https://blogs.sap.com/2017/05/24/single-sign-on-sso-with-sap-business-bydesign/).

8. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um den Ihren Anforderungen entsprechenden **Verbundmetadaten-XML**-Code aus den verfügbaren Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

9. Kopieren Sie im Abschnitt **SAP Business ByDesign einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf SAP Business ByDesign gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **SAP Business ByDesign** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.

1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-sap-business-bydesign-sso"></a>Konfigurieren des einmaligen Anmeldens für SAP Business ByDesign

1. Melden Sie sich beim SAP Business ByDesign-Portal mit Administratorrechten an.

2. Navigieren Sie zum **allgemeinen Task für die Anwendungs- und Benutzerverwaltung**, und klicken Sie auf die Registerkarte **Identitätsanbieter**.

3. Klicken Sie auf **New Identity Provider** (Neuer Identitätsanbieter), und wählen Sie die XML-Metadatendatei aus, die Sie aus dem Azure-Portal heruntergeladen haben. Beim Importieren der Metadaten lädt das System automatisch das erforderliche Signatur- und Verschlüsselungszertifikat hoch.

    ![Konfigurieren von einmaligem Anmelden 1](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_54.png)

4. Um die **Assertion Consumer Service-URL** in der SAML-Anforderung hinzuzufügen, aktivieren Sie **Include Assertion Consumer Service URL** (Assertion Consumer Service-URL aufnehmen).

5. Klicken Sie auf **Activate Single Sign-On**(Einmaliges Anmelden aktivieren).

6. Speichern Sie die Änderungen.

7. Klicken Sie auf die Registerkarte **My System** (Mein System).

    ![Konfigurieren von einmaligem Anmelden 2](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_52.png)

8. Fügen Sie im Textfeld **Azure AD Sign On URL** (Azure AD-Anmelde-URL) den Wert der **Anmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    ![Konfigurieren von einmaligem Anmelden 3](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_53.png)

9. Geben Sie an, ob der Mitarbeiter manuell zwischen der Anmeldung mit Benutzer-ID und Kennwort oder SSO wählen kann. Aktivieren Sie dazu die Option **Manual Identity Provider Selection** (Manuelle Auswahl des Identitätsanbieters).

10. Geben Sie im Abschnitt **SSO URL** (SSO-URL) die URL an, die von Mitarbeitern für die Anmeldung beim System verwendet werden soll.
    In der Dropdownliste „URL Sent to Employee“ (An Mitarbeiter gesendete URL) können Sie zwischen den folgenden Optionen wählen:

    **Non-SSO URL**

    Das System sendet nur die normale System-URL an den Mitarbeiter. Der Mitarbeiter kann sich nicht per SSO anmelden, sondern muss stattdessen ein Kennwort oder Zertifikat verwenden.

    **SSO-URL**

    Das System sendet nur die SSO-URL an den Mitarbeiter. Der Mitarbeiter kann sich per SSO anmelden. Die Authentifizierungsanforderung wird über den IdP umgeleitet.

    **Automatische Auswahl**

    Ist SSO nicht aktiv, sendet das System die normale System-URL an den Mitarbeiter. Wenn SSO aktiv ist, überprüft das System, ob der Mitarbeiter über ein Kennwort verfügt. Ist ein Kennwort verfügbar, werden sowohl die SSO-URL als auch die URL ohne SSO an den Mitarbeiter gesendet. Besitzt der Mitarbeiter kein Kennwort, wird jedoch nur die SSO-URL an den Mitarbeiter gesendet.

11. Speichern Sie die Änderungen.

### <a name="create-sap-business-bydesign-test-user"></a>Erstellen eines SAP Business ByDesign-Testbenutzers

In diesem Abschnitt erstellen Sie in SAP Business ByDesign einen Benutzer mit dem Namen Britta Simon. Wenden Sie sich an das [Clientsupportteam von SAP Business ByDesign](https://www.sap.com/products/cloud-analytics.support.html), um die Benutzer in der SAP Business ByDesign-Plattform hinzuzufügen. 

> [!NOTE]
> Stellen Sie sicher, dass der Wert für „NameID“ mit dem Feld „username“ der SAP Business ByDesign-Plattform übereinstimmt.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

1. Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für SAP Business ByDesign weitergeleitet, wo Sie den Anmeldeflow initiieren können. 

2. Rufen Sie direkt die SAP Business ByDesign-Anmelde-URL auf, und initiieren Sie den Anmeldeflow.

3. Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie in „Meine Apps“auf die Kachel „SAP Business ByDesign“ klicken, werden Sie zur Anmelde-URL für SAP Business ByDesign weitergeleitet. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

* Nach dem Konfigurieren von SAP Business ByDesign können Sie Sitzungssteuerungen erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützen. Sitzungssteuerungen basieren auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-any-app)
