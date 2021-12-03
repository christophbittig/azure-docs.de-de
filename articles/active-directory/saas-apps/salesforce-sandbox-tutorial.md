---
title: 'Tutorial: Azure Active Directory-Integration mit Salesforce Sandbox | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Salesforce Sandbox konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/28/2020
ms.author: jeedes
ms.openlocfilehash: 4d564bbde122a3fddbe4bc2bb2c911fea846f38f
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132299705"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-salesforce-sandbox"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Salesforce Sandbox

In diesem Tutorial erfahren Sie, wie Sie Salesforce Sandbox in Azure Active Directory (Azure AD) integrieren. Die Integration von Salesforce Sandbox in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf Salesforce Sandbox hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Salesforce Sandbox anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Salesforce Sandbox-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Salesforce Sandbox unterstützt **SP- und IDP**-initiiertes einmaliges Anmelden.
* Salesforce Sandbox unterstützt die **Just-in-Time**-Benutzerbereitstellung.
* Salesforce Sandbox unterstützt die [**automatisierte** Benutzerbereitstellung](salesforce-sandbox-provisioning-tutorial.md).

## <a name="adding-salesforce-sandbox-from-the-gallery"></a>Hinzufügen von Salesforce Sandbox aus dem Katalog

Zum Konfigurieren der Integration von Salesforce Sandbox in Azure AD müssen Sie Salesforce Sandbox aus dem Katalog zur Liste der verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Salesforce Sandbox** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Salesforce Sandbox** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.


## <a name="configure-and-test-azure-ad-sso-for-salesforce-sandbox"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Salesforce Sandbox

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Salesforce Sandbox mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Salesforce Sandbox eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Salesforce Sandbox die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Salesforce Sandbox](#configure-salesforce-sandbox-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines Salesforce Sandbox-Testbenutzers](#create-salesforce-sandbox-test-user)** , um eine Entsprechung von B. Simon in Salesforce Sandbox zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Salesforce Sandbox** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus, wenn Sie über eine **Metadatendatei des Dienstanbieters** verfügen und die Anwendung im **IDP**-initiierten Modus konfigurieren möchten:

    a. Klicken Sie auf **Metadatendatei hochladen**.

    ![Metadatendatei hochladen](common/upload-metadata.png)

    b. Klicken Sie auf das **Ordnerlogo**, wählen Sie die Metadatendatei aus, und klicken Sie auf **Hochladen**.

    ![Metadatendatei auswählen](common/browse-upload-metadata.png)

    > [!NOTE]
    > Sie können die Dienstanbieter-Metadatendatei im Salesforce Sandbox-Verwaltungsportal abrufen, was im weiteren Verlauf des Tutorials erläutert wird.

    c. Nachdem die Metadatendatei erfolgreich hochgeladen wurde, wird der Wert von **Antwort-URL** automatisch im Textfeld **Antwort-URL** aufgefüllt.

    ![image](common/both-replyurl.png)

    > [!Note]
    > Falls der Wert für die **Antwort-URL** nicht automatisch eingefügt wird, geben Sie ihn gemäß Ihren Anforderungen manuell ein.

5. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um das Ihrer Anforderung entsprechende **Metadaten-XML** aus den verfügbaren Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

6. Kopieren Sie im Abschnitt **Salesforce Sandbox einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Salesforce Sandbox gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Liste der Anwendungen **Salesforce Sandbox** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-salesforce-sandbox-sso"></a>Konfigurieren des einmaligen Anmeldens für Salesforce Sandbox

1. Öffnen Sie eine neue Registerkarte in Ihrem Browser, und melden Sie sich mit Ihrem Salesforce Sandbox-Administratorkonto an.

2. Klicken Sie in der oberen rechten Ecke der Seite unter dem **Einstellungssymbol** auf **Setup**.

    ![Screenshot: Auswahl des Einstellungssymbols in der oberen rechten Ecke und der Option „Setup“ im Dropdownmenü](./media/salesforce-sandbox-tutorial/configure1.png)

3. Scrollen Sie im linken Navigationsbereich nach unten bis zu den **EINSTELLUNGEN**, und klicken Sie auf **Identität**, um den zugehörigen Bereich zu erweitern. Klicken Sie dann auf **Einstellungen für einmaliges Anmelden**.

    ![Screenshot: Menü „Settings“ (Einstellungen) im linken Bereich, in dem im Menü „Identity“ (Identität) die Option „Single Sign-On Settings“ (Einstellungen für einmaliges Anmelden) ausgewählt ist](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

4. Klicken Sie auf der Seite **Einstellungen für einmaliges Anmelden** auf die Schaltfläche **Bearbeiten**.

    ![Screenshot: Seite „Single Sign-On Settings“ (Einstellungen für einmaliges Anmelden) mit der ausgewählten Schaltfläche „Edit“ (Bearbeiten)](./media/salesforce-sandbox-tutorial/configure3.png)

5. Wählen Sie **SAML aktiviert**, und klicken Sie dann auf **Speichern**.

    ![Screenshot: Seite „Single Sign-On Settings“ (Einstellungen für einmaliges Anmelden), auf der das Kontrollkästchen „SAML Enabled“ (SAML aktiviert) und die Schaltfläche „Save“ (Speichern) ausgewählt sind](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

6. Klicken Sie auf **Neu aus Metadatendatei**, um Ihre SAML-Einstellungen für einmaliges Anmelden zu konfigurieren.

    ![Screenshot: Seite „Single Sign-On Settings“ (Einstellungen für einmaliges Anmelden), auf der die Schaltfläche „New from Metadata File“ (Neu aus Metadatendatei) ausgewählt ist](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

7. Klicken Sie auf **Datei auswählen**, um die Metadaten-XML-Datei hochzuladen, die Sie aus dem Azure-Portal heruntergeladen haben, und klicken Sie dann auf **Erstellen**.

    ![Screenshot: Seite „Single Sign-On Settings“ (Einstellungen für einmaliges Anmelden), auf der die Schaltflächen „Choose File“ (Datei auswählen) und „Create“ (Erstellen) ausgewählt sind](./media/salesforce-sandbox-tutorial/xmlchoose.png)

8. Auf der Seite **SAML-Einstellungen für einmaliges Anmelden** werden die Felder automatisch aufgefüllt. Klicken Sie auf „Speichern“.

    ![Screenshot: Seite „Single Sign-On Settings“ (Einstellungen für einmaliges Anmelden), auf der Felder ausgefüllt sind und die Schaltfläche „Save“ (Speichern) ausgewählt ist](./media/salesforce-sandbox-tutorial/salesforcexml.png)

9. Klicken Sie auf der Seite **Einstellungen für einmaliges Anmelden** auf die Schaltfläche **Metadaten herunterladen**, um die Dienstanbieter-Metadatendatei herunterzuladen. Verwenden Sie diese Datei im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal, um die notwendigen URLs wie oben erläutert zu konfigurieren.

    ![Screenshot: Seite „Single Sign-On Settings“ (Einstellungen für einmaliges Anmelden), auf der Felder ausgefüllt sind und die Schaltfläche „Download Metadata“ (Metadaten herunterladen) ausgewählt ist](./media/salesforce-sandbox-tutorial/configure4.png)

10. Wenn Sie die Anwendung im **SP**-initiierten Modus konfigurieren möchten, sind dies die Voraussetzungen:

    a. Sie sollten über eine überprüfte Domäne verfügen.

    b. Sie müssen Ihre Domäne in Salesforce Sandbox konfigurieren und aktivieren. Die entsprechenden Schritte werden später in diesem Tutorial erläutert.

    c. Klicken Sie im Azure-Portal im Abschnitt **Grundlegende SAML-Konfiguration** auf **Zusätzliche URLs festlegen**, und führen Sie den folgenden Schritt aus:
  
    ![SSO-Informationen zur Domäne und zu den URLs für Salesforce Sandbox](common/both-signonurl.png)

    Geben Sie im Textfeld **Anmelde-URL** den Wert im folgenden Format ein: `https://<instancename>--Sandbox.<entityid>.my.salesforce.com`.

    > [!NOTE]
    > Dieser Wert sollte aus dem Salesforce Sandbox-Portal kopiert werden, nachdem Sie die Domäne aktiviert haben.

11. Klicken Sie im Abschnitt **SAML-Signaturzertifikat** auf **Verbundmetadaten-XML**, und speichern Sie die XML-Datei dann auf Ihrem Computer.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

12. Öffnen Sie eine neue Registerkarte in Ihrem Browser, und melden Sie sich mit Ihrem Salesforce Sandbox-Administratorkonto an.

13. Klicken Sie in der oberen rechten Ecke der Seite unter dem **Einstellungssymbol** auf **Setup**.

    ![Screenshot: Auswahl des Einstellungssymbols in der oberen rechten Ecke und der Option „Setup“ im Dropdownmenü](./media/salesforce-sandbox-tutorial/configure1.png)

14. Scrollen Sie im linken Navigationsbereich nach unten bis zu den **EINSTELLUNGEN**, und klicken Sie auf **Identität**, um den zugehörigen Bereich zu erweitern. Klicken Sie dann auf **Einstellungen für einmaliges Anmelden**.

    ![Screenshot: Menü „Settings“ (Einstellungen) im linken Navigationsbereich, in dem im Menü „Identity“ (Identität) die Option „Single Sign-On Settings“ (Einstellungen für einmaliges Anmelden) ausgewählt ist](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

15. Klicken Sie auf der Seite **Einstellungen für einmaliges Anmelden** auf die Schaltfläche **Bearbeiten**.

    ![Screenshot: Seite „Single Sign-On Settings“ (Einstellungen für einmaliges Anmelden) mit der ausgewählten Schaltfläche „Edit“ (Bearbeiten)](./media/salesforce-sandbox-tutorial/configure3.png)

16. Wählen Sie **SAML aktiviert**, und klicken Sie dann auf **Speichern**.

    ![Screenshot: Seite „Single Sign-On Settings“ (Einstellungen für einmaliges Anmelden), auf der das Kontrollkästchen „SAML Enabled“ (SAML aktiviert) und die Schaltfläche „Save“ (Speichern) ausgewählt sind](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

17. Klicken Sie auf **Neu aus Metadatendatei**, um Ihre SAML-Einstellungen für einmaliges Anmelden zu konfigurieren.

    ![Screenshot: Seite „Single Sign-On Settings“ (Einstellungen für einmaliges Anmelden), auf der die Schaltfläche „New from Metadata File“ (Neu aus Metadatendatei) ausgewählt ist](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

18. Klicken Sie auf **Datei auswählen**, um die Metadaten-XML-Datei hochzuladen, und klicken Sie auf **Erstellen**.

    ![Screenshot: Seite „Single Sign-On Settings“ (Einstellungen für einmaliges Anmelden), auf der die Schaltfläche „Choose File“ (Datei auswählen) und die Schaltfläche „Create“ (Erstellen) ausgewählt sind](./media/salesforce-sandbox-tutorial/xmlchoose.png)

19. Auf der Seite **SAML-Einstellungen für einmaliges Anmelden** werden Felder automatisch aufgefüllt. Geben Sie den Namen der Konfiguration (z.B. *SPSSOWAAD_Test*) in das Textfeld **Name** ein, und klicken Sie auf „Speichern“.

    ![Screenshot: Seite „Single Sign-On Settings“ (Einstellungen für einmaliges Anmelden), auf der Felder ausgefüllt sind, das Textfeld „Name“ einen Beispielnamen enthält und die Schaltfläche „Save“ (Speichern) ausgewählt ist](./media/salesforce-sandbox-tutorial/sf-saml-config.png)

20. Führen Sie die folgenden Schritte aus, um Ihre Domäne in Salesforce Sandbox zu aktivieren.

    > [!NOTE]
    > Vor dem Aktivieren der Domäne müssen Sie die gleiche Domäne in Salesforce Sandbox erstellen. Weitere Informationen finden Sie unter [Defining Your Domain Name](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_define.htm&language=en_US) (Festlegen des Domänennamens). Nachdem die Domäne erstellt wurde, sollten Sie sicherstellen, dass diese richtig konfiguriert wurde.

21. Klicken Sie in Salesforce Sandbox im linken Navigationsbereich auf **Unternehmenseinstellungen**, um den zugehörigen Abschnitt zu erweitern, und klicken Sie dann auf **My Domain** (Meine Domäne).

    ![Screenshot, auf dem im linken Navigationsbereich „Company Settings“ (Unternehmenseinstellungen) und „My Domain“ (Meine Domäne) ausgewählt sind](./media/salesforce-sandbox-tutorial/sf-my-domain.png)

22. Klicken Sie im Abschnitt **Authentifizierungskonfiguration** auf **Bearbeiten**.

    ![Screenshot: Abschnitt „Authentication Configuration“ (Authentifizierungskonfiguration), in dem die Schaltfläche „Edit“ (Bearbeiten) ausgewählt ist](./media/salesforce-sandbox-tutorial/sf-edit-auth-config.png)

23. Wählen Sie im Abschnitt **Authentifizierungskonfiguration** für den **Authentifizierungsdienst** den Namen der SAML-Einstellung für einmaliges Anmelden aus, den Sie während der SSO-Konfiguration in Salesforce Sandbox festgelegt haben, und klicken Sie auf **Speichern**.

    ![Einmaliges Anmelden konfigurieren](./media/salesforce-sandbox-tutorial/configure2.png)

### <a name="create-salesforce-sandbox-test-user"></a>Erstellen eines Salesforce Sandbox-Testbenutzers

In diesem Abschnitt wird ein Benutzer mit dem Namen Britta Simon in Salesforce Sandbox erstellt. Salesforce Sandbox unterstützt die Just-in-Time-Bereitstellung, die standardmäßig aktiviert ist. Für Sie steht in diesem Abschnitt kein Aktionselement zur Verfügung. Falls ein Benutzer nicht bereits in Salesforce Sandbox vorhanden ist, wird beim Versuch, auf Salesforce Sandbox zuzugreifen, ein neuer Benutzer erstellt. Außerdem unterstützt Salesforce Sandbox die automatische Benutzerbereitstellung. Weitere Informationen zum Konfigurieren der automatischen Benutzerbereitstellung finden Sie [hier](salesforce-sandbox-provisioning-tutorial.md).

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

#### <a name="sp-initiated"></a>SP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für Salesforce Sandbox weitergeleitet, wo Sie den Anmeldeflow initiieren können.  

* Rufen Sie direkt die Salesforce Sandbox-Anmelde-URL auf, und initiieren Sie den Anmeldeflow.

#### <a name="idp-initiated"></a>IDP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch sollten Sie automatisch bei der Salesforce Sandbox-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. 

Sie können auch den Microsoft-Bereich „Meine Apps“ verwenden, um die Anwendung in einem beliebigen Modus zu testen. Beim Klicken auf die Kachel „Salesforce Sandbox“ in „Meine Apps“ geschieht Folgendes: Wenn Sie den SP-Modus konfiguriert haben, werden Sie zum Initiieren des Anmeldeflows zur Anmeldeseite der Anwendung weitergeleitet. Wenn Sie den IDP-Modus konfiguriert haben, sollten Sie automatisch bei der Salesforce Sandbox-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Salesforce Sandbox können Sie Sitzungssteuerungen erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützen. Sitzungssteuerungen basieren auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-aad)
