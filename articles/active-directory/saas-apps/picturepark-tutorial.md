---
title: 'Tutorial: Azure Active Directory-Integration in Picturepark | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Picturepark konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/01/2021
ms.author: jeedes
ms.openlocfilehash: 9d245ab63e65048f91179b6255f23ee3a12942ee
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132280057"
---
# <a name="tutorial-azure-active-directory-integration-with-picturepark"></a>Tutorial: Azure Active Directory-Integration in Picturepark

In diesem Tutorial erfahren Sie, wie Sie Picturepark in Azure Active Directory (Azure AD) integrieren. Die Integration von Picturepark in Azure AD ermöglicht Ihnen Folgendes:

* Sie können in Azure AD steuern, wer Zugriff auf Picturepark hat.
* Sie können es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Picturepark anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration in Picturepark konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Sollten Sie nicht über eine Azure AD-Umgebung verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Picturepark-Abonnement, für das ein einmaliges Anmelden aktiviert ist.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Picturepark unterstützt **SP**-initiiertes einmaliges Anmelden.

## <a name="add-picturepark-from-the-gallery"></a>Hinzufügen von Picturepark aus dem Katalog

Zum Konfigurieren der Integration von Picturepark in Azure AD müssen Sie Picturepark aus dem Katalog zur Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus dem Katalog hinzufügen** den Suchbegriff **Picturepark** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich die App **Picturepark** aus, und fügen Sie sie dann hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-picturepark"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Picturepark

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Picturepark mithilfe eines Testbenutzers namens **B. Simon**. Damit das einmalige Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Picturepark eingerichtet werden.

Führen Sie die folgenden Schritte aus, um das einmalige Anmelden von Azure AD mit Picturepark zu konfigurieren und zu testen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Picturepark](#configure-picturepark-sso)** , um die Einstellungen für das einmalige Anmelden auf der Anwendungsseite zu konfigurieren.
    1. **[Erstellen eines Picturepark-Testbenutzers](#create-picturepark-test-user)** , um eine Entsprechung von B. Simon in Picturepark zu erhalten, die mit der entsprechenden Benutzerdarstellung in Azure AD verknüpft ist.
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Picturepark** zum Abschnitt **Verwalten** und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    a. Geben Sie im Textfeld **Bezeichner (Entitäts-ID)** eine URL in einem der folgenden Formate ein:

    | Bezeichner-URL |
    |---|
    |`https://<COMPANY_NAME>.current-picturepark.com`|
    |`https://<COMPANY_NAME>.picturepark.com`|
    |`https://<COMPANY_NAME>.next-picturepark.com`|
    |
    
    b. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://<COMPANY_NAME>.picturepark.com`.

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch den tatsächlichen Bezeichner und die tatsächliche Anmelde-URL. Wenden Sie sich an das [Supportteam für den Picturepark-Client](https://picturepark.com/company/picturepark-customer-support), um diese Werte zu erhalten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

5. Klicken Sie im Abschnitt **SAML-Signaturzertifikat** auf die Schaltfläche **Bearbeiten**, um das Dialogfeld **SAML-Signaturzertifikat** zu öffnen.

    ![Bearbeiten des SAML-Signaturzertifikats](common/edit-certificate.png)

6. Kopieren Sie im Abschnitt **SAML-Signaturzertifikat** den **Fingerabdruck**, und speichern Sie ihn auf Ihrem Computer.

    ![Kopieren des Fingerabdruckwerts](common/copy-thumbprint.png)

7. Kopieren Sie im Abschnitt **Picturepark einrichten** die entsprechenden URLs gemäß Ihren Anforderungen. Verwenden Sie für **Anmelde-URL** einen Wert im folgenden Format: `https://login.microsoftonline.com/_my_directory_id_/wsfed`.

    > [!Note]
    > _my_directory_id_ ist die Mandanten-ID des Azure AD-Abonnements.

    ![Kopieren der Konfiguration-URLs](./media/picturepark-tutorial/configure.png)

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie dem Benutzer den Zugriff auf Picturepark gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Picturepark** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-picturepark-sso"></a>Konfigurieren des einmaligen Anmeldens für Picturepark

1. Melden Sie sich in einem anderen Webbrowserfenster bei der Picturepark-Unternehmenswebsite als Administrator an.

2. Klicken Sie oben auf der Symbolleiste auf **Verwaltung**, und klicken Sie dann auf **Verwaltungskonsole**.
   
    ![Management Console (Verwaltungskonsole)](./media/picturepark-tutorial/tools.png "Verwaltungskonsole")

3. Klicken Sie auf **Authentifizierung**, und klicken Sie dann auf **Identitätsanbieter**.
   
    ![Authentifizierung](./media/picturepark-tutorial/identity-provider.png "Authentifizierung")

4. Führen Sie im Abschnitt **Identitätsanbieterkonfiguration** die folgenden Schritte aus:
   
    ![Identity provider configuration (Identitätsanbieterkonfiguration)](./media/picturepark-tutorial/add-configuration.png "Identitätsanbieterkonfiguration")
   
    a. Klicken Sie auf **Hinzufügen**.
  
    b. Geben Sie einen Namen für die Konfiguration ein.
   
    c. Wählen Sie **Als Standard festlegen** aus.
   
    d. Fügen Sie in das Textfeld **Issuer URI** (Aussteller-URI) den Wert der **Anmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.
   
    e. Fügen Sie im Textfeld **Trusted Issuer Thumb Print** (Fingerabdruck eines vertrauenswürdigen Anbieters) den Wert von **Fingerabdruck** ein, den Sie aus dem Abschnitt **SAML-Signaturzertifikat** kopiert haben. 

5. Klicken Sie auf **JoinDefaultUsersGroup**.

6. Um das Attribut **E-Mail-Adresse** im Textfeld **Anspruch** festzulegen, geben Sie `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` ein, und klicken Sie auf **Speichern**.

      ![Configuration](./media/picturepark-tutorial/claim.png "Konfiguration")

### <a name="create-picturepark-test-user"></a>Erstellen eines Picturepark-Testbenutzers

Damit sich Azure AD-Benutzer bei Picturepark anmelden können, müssen sie in Picturepark bereitgestellt werden. Im Fall von Picturepark ist die Bereitstellung eine manuelle Aufgabe.

**Führen Sie zum Bereitstellen eines Benutzerkontos die folgenden Schritte aus:**

1. Melden Sie sich bei Ihrem **Picturepark**-Mandanten an.

1. Klicken Sie oben auf der Symbolleiste auf **Verwaltung**, und klicken Sie dann auf **Benutzer**.
   
    ![Benutzer](./media/picturepark-tutorial/user.png "Benutzer")

1. Klicken Sie auf der Registerkarte **Users overview** auf **New**.
   
    ![Benutzerverwaltung](./media/picturepark-tutorial/new-user.png "Benutzerverwaltung")

1. Führen Sie im Dialogfeld **Benutzer erstellen** die folgenden Schritte für einen gültigen Azure Active Directory-Benutzer aus, den Sie bereitstellen möchten:
   
    ![Benutzer erstellen](./media/picturepark-tutorial/details.png "Benutzer erstellen")
   
    a. Geben Sie im Textfeld **Email address** (E-Mail-Adresse) die **E-Mail-Adresse** des Benutzers ein: `BrittaSimon@contoso.com`.  
   
    b. Geben Sie in den Textfeldern **Kennwort** und **Kennwort bestätigen** das **Kennwort** von Britta Simon ein. 
   
    c. Geben Sie im Textfeld **Vorname** den **Vornamen** des Benutzers ein, z.B. **Britta**. 
   
    d. Geben Sie im Textfeld **Nachname** den **Nachnamen** des Benutzers ein, z.B. **Simon**.
   
    e. Geben Sie im Textfeld **Unternehmen** den **Unternehmensnamen** des Benutzers ein. 
   
    f. Wählen Sie **Land/Region** des Benutzers im Textfeld **Country** (Land) aus.
  
    g. Geben Sie im Textfeld **PLZ** die **Postleitzahl** des Orts ein.
   
    h. Geben Sie im Textfeld **Ort** den **Ortsnamen** des Benutzers ein.

    i. Wählen Sie eine **Language** aus.
   
    j. Klicken Sie auf **Erstellen**.

>[!NOTE]
>Sie können Azure AD-Benutzerkonten auch mithilfe anderer Tools zum Erstellen von Picturepark-Benutzerkonten oder mithilfe der von Picturepark bereitgestellten APIs erstellen.
>

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für Picturepark weitergeleitet. Dort können Sie den Anmeldeablauf initiieren. 

* Navigieren Sie direkt zur Picturepark-Anmelde-URL, und initiieren Sie von dort aus den Anmeldeablauf.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie in dem Zugriffsbereich von „Meine Apps“ auf die Kachel „Picturepark“ klicken, werden Sie zur Anmelde-URL für Picturepark weitergeleitet. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Picturepark können Sie die Sitzungssteuerung erzwingen, die Ihre vertraulichen Unternehmensdaten in Echtzeit vor der Exfiltration und Infiltration schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-aad)
