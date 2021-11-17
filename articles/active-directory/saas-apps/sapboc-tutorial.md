---
title: 'Tutorial: Azure Active Directory-Integration mit SAP Analytics Cloud | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und SAP Analytics Cloud konfigurieren.
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
ms.openlocfilehash: eb9388896a4039a070e67d4a66f49d7c2c8e94ba
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132341538"
---
# <a name="tutorial-integrate-sap-analytics-cloud-with-azure-active-directory"></a>Tutorial: Integrieren von SAP Analytics Cloud in Azure Active Directory

In diesem Tutorial erfahren Sie, wie Sie SAP Analytics Cloud in Azure Active Directory (Azure AD) integrieren. Die Integration von SAP Analytics Cloud mit Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf SAP Analytics Cloud hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei SAP Analytics Cloud anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* SAP Analytics Cloud-Abonnement, für das einmaliges Anmelden aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* SAP Analytics Cloud unterstützt **SP**-initiiertes einmaliges Anmelden.

* SAP Analytics Cloud unterstützt [automatisierte Benutzerbereitstellung](sap-analytics-cloud-provisioning-tutorial.md). 

## <a name="add-sap-analytics-cloud-from-the-gallery"></a>Hinzufügen von SAP Analytics Cloud aus dem Katalog

Zum Konfigurieren der Integration von SAP Analytics Cloud in Azure AD müssen Sie SAP Analytics Cloud aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **SAP Analytics Cloud** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **SAP Analytics Cloud** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-sap-analytics-cloud"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für SAP Analytics Cloud

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit SAP Analytics Cloud mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in SAP Analytics Cloud eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit SAP Analytics Cloud die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für SAP Analytics Cloud](#configure-sap-analytics-cloud-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines SAP Analytics Cloud-Testbenutzers](#create-sap-analytics-cloud-test-user)**, um eine Entsprechung von B. Simon in SAP Analytics Cloud zu erhalten, die mit ihrer Benutzerdarstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **SAP Analytics Cloud** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Geben Sie im Abschnitt **Grundlegende SAML-Konfiguration** die Werte für die folgenden Felder ein:

    a. Geben Sie im Textfeld **Bezeichner (Entitäts-ID)** eine URL in einem der folgenden Formate ein:

    | **Bezeichner-URL** |
    |----|
    | `<sub-domain>.sapbusinessobjects.cloud` |
    | `<sub-domain>.sapanalytics.cloud` |

    b. Geben Sie im Textfeld **Anmelde-URL** eine URL in einem der folgenden Formate ein:
    
    | **Anmelde-URL** |
    |------|
    | `https://<sub-domain>.sapanalytics.cloud/` |
    | `https://<sub-domain>.sapbusinessobjects.cloud/` |

    > [!NOTE] 
    > Die Werte in diesen URLs dienen nur Demonstrationszwecken. Ersetzen Sie diese Werte durch den tatsächlichen Bezeichner und die tatsächliche Anmelde-URL. Die Anmelde-URL erhalten Sie vom [Supportteam für den SAP Analytics Cloud-Client](https://help.sap.com/viewer/product/SAP_BusinessObjects_Cloud/release/). Sie können die Bezeichner-URL abrufen, indem Sie die SAP Analytics Cloud-Metadaten aus der Verwaltungskonsole herunterladen. Dies wird weiter unten im Tutorial erläutert.

4. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** zu **Verbundmetadaten-XML**, und wählen Sie **Herunterladen** aus, um das Zertifikat herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

6. Kopieren Sie im Abschnitt **SAP Analytics Cloud einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon das einmalige Anmelden bei Azure, indem Sie ihr Zugriff auf SAP Analytics Cloud gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **SAP Analytics Cloud** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-sap-analytics-cloud-sso"></a>Konfigurieren des einmaligen Anmeldens für SAP Analytics Cloud

1. Melden Sie sich in einem anderen Webbrowserfenster bei der SAP Analytics Cloud-Unternehmenswebsite als Administrator an.

2. Wählen Sie **Menü** > **System** > **Administration**.
    
    ![„Menü“, „System“ und dann „Administration“ wählen](./media/sapboc-tutorial/configure-1.png)

3. Wählen Sie auf der Registerkarte **Security** das Symbol **Bearbeiten** (Stift) aus.
    
    ![Auf der Registerkarte „Security“ das Symbol „Bearbeiten“ auswählen](./media/sapboc-tutorial/configure-2.png)  

4. Wählen Sie als **Authentication Method** die Option **SAML Single Sign-On (SSO)** aus.

    ![„SAML Single Sign-On (SSO)“ als Authentifizierungsmethode auswählen](./media/sapboc-tutorial/configure-3.png)  

5. Um die Metadaten des Dienstanbieters herunterzuladen, (Schritt 1), wählen Sie **Download**. Suchen Sie in der Metadatendatei den Wert **entityID**, und kopieren Sie ihn. Fügen Sie im Azure-Portal im Dialogfeld **Grundlegende SAML-Konfiguration** den Wert in das Feld **Bezeichner** ein.

    ![Wert „entityID“ kopieren und einfügen](./media/sapboc-tutorial/configure-4.png)  

6. Um die Metadaten des Dienstanbieters (Schritt 2) in der Datei hochzuladen, die Sie aus dem Azure-Portal heruntergeladen haben, wählen Sie unter **Upload Identity Provider metadata** die Option **Upload**.  

    ![Unter „Upload Identity Provider metadata“ die Option „Upload“ wählen](./media/sapboc-tutorial/configure-5.png)

7. Wählen Sie in der Liste **User Attribute** das Benutzerattribut aus (Schritt 3), das Sie für Ihre Implementierung verwenden möchten. Dieses Benutzerattribut wird dem Identitätsanbieter zugeordnet. Um ein benutzerdefiniertes Attribut auf der Seite des Benutzers einzugeben, verwenden Sie die Option **Custom SAML Mapping**. Alternativ können Sie entweder **Email** oder **USER ID** als Benutzerattribut wählen. In unserem Beispiel haben wir **Email** ausgewählt, weil wir den Benutzer-ID-Anspruch mit dem Attribut **userprincipalname** im Abschnitt **Benutzerattribute und Ansprüche** im Azure-Portal zugeordnet haben. Hiermit wird eine individuelle Benutzer-E-Mail bereitgestellt, die in jeder erfolgreichen SAML-Antwort an die SAP Analytics Cloud-Anwendung gesendet wird.

    ![„Benutzerattribut“ auswählen](./media/sapboc-tutorial/configure-6.png)

8. Um das Konto mit dem Identitätsanbieter zu überprüfen (Schritt 4), geben Sie im Feld **Login Credential (Email)** die E-Mail-Adresse des Benutzers ein. Wählen Sie dann **Verify Account**. Das System fügt dem Benutzerkonto Anmeldeinformationen hinzu.

    ![E-Mail-Adresse eingeben und „Konto überprüfen“ wählen](./media/sapboc-tutorial/configure-7.png)

9. Wählen Sie das Symbol **Speichern** aus.

    ![Symbol „Speichern“](./media/sapboc-tutorial/save.png)

### <a name="create-sap-analytics-cloud-test-user"></a>Erstellen eines SAP Analytics Cloud-Testbenutzers

Damit sich Azure AD-Benutzer bei SAP Analytics Cloud anmelden können, müssen sie in SAP Analytics Cloud bereitgestellt werden. In SAP Analytics Cloud wird die Bereitstellung manuell ausgeführt.

So stellen Sie ein Benutzerkonto bereit:

1. Melden Sie sich bei der SAP Analytics Cloud-Unternehmenswebsite als Administrator an.

2. Wählen Sie **Menü** > **Security** > **Users**.

    ![Mitarbeiter hinzufügen](./media/sapboc-tutorial/user-1.png)

3. Um neue Benutzerdetails hinzuzufügen, wählen Sie auf der Seite **Users** die Option **+**. 

    ![Seite „Add Users“](./media/sapboc-tutorial/user-4.png)

    Führen Sie anschließend die folgenden Schritte aus:

    1. Geben Sie im Textfeld **USER ID** (BENUTZER-ID) die Benutzer-ID des Benutzers ein, z. B. **B**.

    1. Geben Sie im Textfeld **FIRST NAME** (VORNAME) den Vornamen des Benutzers ein (z. B. **B**).

    1. Geben Sie im Textfeld **LAST NAME** den Nachnamen des Benutzers ein (z.B. **Simon**).

    1. Geben Sie im Textfeld **DISPLAY NAME** (ANZEIGENAME) den vollständigen Namen des Benutzers ein (z. B. **B. Simon**).

    1. Geben Sie im Textfeld **E-MAIL** die E-Mail-Adresse des Benutzers ein, z.B. `b.simon@contoso.com`.

    1. Wählen Sie auf der Seite **Select Roles** die passende Rolle für den Benutzer, und wählen Sie dann **OK**.

        ![Rolle auswählen](./media/sapboc-tutorial/user-3.png)

    1. Wählen Sie das Symbol **Speichern** aus.

> [!NOTE]
> Außerdem unterstützt SAP Analytics Cloud automatische Benutzerbereitstellung. Weitere Informationen zum Konfigurieren der automatischen Benutzerbereitstellung finden Sie [hier](./sap-analytics-cloud-provisioning-tutorial.md).

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für SAP Analytics Cloud weitergeleitet, wo Sie den Anmeldeflow initiieren können. 

* Rufen Sie direkt die SAP Analytics Cloud-Anmelde-URL auf, und initiieren Sie den Anmeldeflow.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie unter „Meine Apps“ auf die Kachel „SAP Analytics Cloud“ klicken, werden Sie zur Anmelde-URL für SAP Analytics Cloud umgeleitet. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von SAP Analytics Cloud können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-any-app)
