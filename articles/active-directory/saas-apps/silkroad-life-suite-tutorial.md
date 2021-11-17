---
title: 'Tutorial: Azure Active Directory-Integration in SilkRoad Life Suite | Microsoft Docs'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und der SilkRoad Life Suite konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/09/2021
ms.author: jeedes
ms.openlocfilehash: 8bef3229ca8ca814e55d9dba34c79646367bbd7c
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132304684"
---
# <a name="tutorial-azure-active-directory-integration-with-silkroad-life-suite"></a>Tutorial: Azure Active Directory-Integration in die SilkRoad Life Suite

In diesem Tutorial erfahren Sie, wie Sie SilkRoad Life Suite mit Azure Active Directory (Azure AD) integrieren. Durch die Integration von SilkRoad Life Suite mit Azure AD wird Folgendes ermöglicht:

* Steuerung des Benutzerzugriffs auf SilkRoad Life Suite in Azure AD
* Automatische Benutzeranmeldung bei SilkRoad Life Suite mit dem Azure AD-Konto
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration in die SilkRoad Life Suite konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Sollten Sie nicht über eine Azure AD-Umgebung verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) verwenden.
* SSO-fähiges Abonnement (Single Sign-On, einmaliges Anmelden) für SilkRoad Life Suite

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* SilkRoad Life Suite unterstützt das über **SP** initiierte einmalige Anmelden.

## <a name="add-silkroad-life-suite-from-the-gallery"></a>Hinzufügen der SilkRoad Life Suite aus dem Katalog

Zum Konfigurieren der Integration der SilkRoad Life Suite in Azure AD müssen Sie die SilkRoad Life Suite aus dem Katalog zur Liste der verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **SilkRoad Life Suite** in das Suchfeld ein.
1. Wählen Sie **SilkRoad Life Suite** aus dem Ergebnisbereich aus, und fügen Sie die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-silkroad-life-suite"></a>Konfigurieren und Testen des Azure AD-SSO für SilkRoad Life Suite

Konfigurieren und testen Sie das einmalige Anmelden mit Azure AD bei SilkRoad Life Suite mithilfe eines Testbenutzers mit dem Namen **B.Simon**. Damit das einmalige Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in SilkRoad Life Suite eingerichtet werden.

Führen Sie zum Konfigurieren des einmaligen Anmeldens mit Azure AD bei SilkRoad Life Suite die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens bei SilkRoad Life Suite](#configure-silkroad-life-suite-sso)** : Konfigurieren der SSO-Einstellungen auf Anwendungsseite
    1. **[Erstellen eines Testbenutzers für SilkRoad Life Suite](#create-silkroad-life-suite-test-user)** : Erstellen eines Gegenbenutzers für B.Simon in SilkRoad Life Suite, der mit dem entsprechenden Azure AD-Benutzer verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **SilkRoad Life Suite** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus, wenn Sie über eine **Dienstanbieter-Metadatendatei** verfügen:

    > [!NOTE]
    > Sie erhalten die **Dienstanbieter-Metadatendatei**, die weiter unten in diesem Tutorial erläutert wird.

    a. Klicken Sie auf **Metadatendatei hochladen**.

    ![Screenshot: Abschnitt „Grundlegende SAML-Konfiguration“ mit dem Link „Metadatendatei hochladen“](common/upload-metadata.png)

    b. Klicken Sie auf das **Ordnerlogo**, wählen Sie die Metadatendatei aus, und klicken Sie auf **Hochladen**.

    ![Screenshot: Dialogfeld, in dem Sie eine Datei auswählen und hochladen können](common/browse-upload-metadata.png)

    c. Nach dem erfolgreichen Upload der Metadatendatei werden die Werte unter **Bezeichner** und **Antwort-URL** im Abschnitt „Grundlegende SAML-Konfiguration“ automatisch aufgefüllt.

    > [!Note]
    > Falls die Werte **Bezeichner** und **Antwort-URL** nicht automatisch aufgefüllt werden, geben Sie die erforderlichen Werte manuell ein.

    d. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<SUBDOMAIN>.silkroad-eng.com/Authentication/`

5. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus, wenn Sie nicht über eine **Dienstanbieter-Metadatendatei** verfügen:

    a. Geben Sie im Feld **Bezeichner** eine URL in einem der folgenden Formate ein:

    | Bezeichner-URL |
    |-----|
    |`https://<SUBDOMAIN>.silkroad-eng.com/Authentication/SP`|
    |`https://<SUBDOMAIN>.silkroad.com/Authentication/SP`|
    

    b. Geben Sie im Textfeld **Antwort-URL** eine URL in einem der folgenden Formate ein:

    | Antwort-URL |
    |-----|
    |`https://<SUBDOMAIN>.silkroad-eng.com/Authentication/`|
    |`https://<SUBDOMAIN>.silkroad.com/Authentication/`|

    c. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<SUBDOMAIN>.silkroad-eng.com/Authentication/`

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Sie müssen diese Werte mit dem tatsächlichen Bezeichner, der Antwort-URL und der Anmelde-URL aktualisieren. Wenden Sie sich an das [Clientsupportteam von SilkRoad Life Suite](https://www.silkroad.com/locations/), um diese Werte zu erhalten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

6. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um den Ihren Anforderungen entsprechenden **Verbundmetadaten-XML**-Code aus den verfügbaren Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

7. Kopieren Sie im Abschnitt **SilkRoad Life Suite einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B.Simon das einmalige Anmelden mit Azure, indem Sie ihr Zugriff auf SilkRoad Life Suite gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **SilkRoad Life Suite** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-silkroad-life-suite-sso"></a>Konfigurieren des einmaligen Anmeldens bei SilkRoad Life Suite

1. Melden Sie sich bei Ihrer SilkRoad Life Suite-Unternehmenswebsite als Administrator an.

    > [!NOTE]
    > Um Zugriff auf die Authentifizierungsanwendung der Anwendung SilkRoad Life Suite zu erhalten und den Verbund mit Microsoft Azure AD zu konfigurieren, wenden Sie sich an den SilkRoad-Support oder Ihren Vertriebsmitarbeiter für SilkRoad-Dienste.

1. Wechseln Sie zu **Dienstanbieter**, und klicken Sie dann auf **Verbunddetails**.

    ![Screenshot: Ausgewählte Option „Verbunddetails“ unter „Dienstanbieter“](./media/silkroad-life-suite-tutorial/details.png)

1. Klicken Sie auf **Download Federation Metadata**(Verbundmetadaten herunterladen), und speichern Sie die Metadatendatei dann auf Ihrem Computer. Verwenden Sie die heruntergeladenen Verbundmetadaten als **Dienstanbieter-Metadatendatei** im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal.

    ![Screenshot: Link „Download Federation Metadata“ (Verbundmetadaten herunterladen)](./media/silkroad-life-suite-tutorial/metadata.png)

1. Klicken Sie in der Anwendung **SilkRoad** auf **Authentication Sources** (Authentifizierungsquellen).

    ![Screenshot: Ausgewählte Option „Authentication Sources“ (Authentifizierungsquellen)](./media/silkroad-life-suite-tutorial/sources.png) 

1. Klicken Sie auf **Add Authentication Source**(Authentifizierungsquelle hinzufügen).

    ![Screenshot: Link „Add Authentication Source“ (Authentifizierungsquelle hinzufügen)](./media/silkroad-life-suite-tutorial/add-source.png)

1. Führen Sie im Abschnitt **Add Authentication Source** (Authentifizierungsquelle hinzufügen) die folgenden Schritte aus:

    ![Screenshot: „Add Authentication Source“ (Authentifizierungsquelle hinzufügen) mit ausgewählter Schaltfläche „Create Identity Provider using File Data“ (Identitätsanbieter mithilfe von Dateidaten erstellen)](./media/silkroad-life-suite-tutorial/metadata-file.png)
  
    a. Klicken Sie unter **Option 2 - Metadata File** (Option 2 – Metadatendatei) auf **Browse (Durchsuchen)** , um die aus dem Azure-Portal heruntergeladene Metadatendatei hochzuladen.
  
    b. Klicken Sie auf **Create Identity Provider using File Data**(Identitätsanbieter mithilfe von Dateidaten erstellen).

1. Klicken Sie im Abschnitt **Authentication Sources (Authentifizierungsquellen)** auf **Edit (Bearbeiten)** .

    ![Screenshot: „Authentication Sources“ (Authentifizierungsquellen) mit ausgewählter Bearbeitungsoption](./media/silkroad-life-suite-tutorial/edit-source.png)

1. Führen Sie im Dialogfeld **Edit Authentication Source** (Authentifizierungsquelle bearbeiten) die folgenden Schritte aus:

    ![Screenshot: Dialogfeld „Edit Authentication Source“ (Authentifizierungsquelle bearbeiten), in dem Sie die beschriebenen Werte eingeben können](./media/silkroad-life-suite-tutorial/authentication.png)

    a. Wählen Sie für **Enabled (Aktiviert)** die Option **Yes (Ja)** aus.

    b. Fügen Sie in das Textfeld **EntityId** den Wert für **Azure AD-Bezeichner** ein, den Sie aus dem Azure-Portal kopiert haben.

    c. Geben Sie im Textfeld **IdP Description** (IdP-Beschreibung) eine Beschreibung für die Konfiguration ein (etwa **Azure AD SSO**).

    d. Laden Sie im Textfeld **Metadata file**  (Metadatendatei), die **Metadatendatei** hoch, die Sie aus dem Azure-Portal heruntergeladen haben.
  
    e. Geben Sie im Textfeld **IdP Name** (IdP-Name) einen für Ihre Konfiguration spezifischen Namen ein (etwa *Azure SP*).
  
    f. Fügen Sie in das Textfeld **Abmeldedienst-URL** den Wert der **Abmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    g. Fügen Sie in das Textfeld **Anmeldedienst-URL** den Wert der **Anmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    h. Klicken Sie auf **Speichern**.

1. Deaktivieren Sie alle anderen Authentifizierungsquellen.

    ![Screenshot: „Authentication Sources“ (Authentifizierungsquellen) mit der Möglichkeit zum Deaktivieren anderer Quellen ](./media/silkroad-life-suite-tutorial/manage-source.png)

### <a name="create-silkroad-life-suite-test-user"></a>Erstellen eines SilkRoad Life Suite-Testbenutzers

In diesem Abschnitt erstellen Sie in SilkRoad Life Suite einen Benutzer namens Britta Simon. Arbeiten Sie mit dem [Clientsupportteam von SilkRoad Life Suite](https://www.silkroad.com/locations/) zusammen, um der SilkRoad Life Suite-Plattform Benutzer hinzuzufügen. Benutzer müssen erstellt und aktiviert werden, damit Sie einmaliges Anmelden verwenden können.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für SilkRoad Life Suite weitergeleitet, über die Sie den Anmeldeprozess initiieren können. 

* Rufen Sie die Anmelde-URL für SilkRoad Life Suite direkt auf, und initiieren Sie den Anmeldeprozess.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie unter „Meine Apps“ auf die Kachel „SilkRoad Life Suite“ klicken, werden Sie zur Anmelde-URL für SilkRoad Life Suite weitergeleitet. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von SilkRoad Life Suite können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-aad)
