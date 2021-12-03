---
title: 'Tutorial: Azure Active Directory-Integration mit RingCentral | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und RingCentral konfigurieren.
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
ms.openlocfilehash: 69fe78302fa44bf22b56048b8fc9278a02843df0
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132341602"
---
# <a name="tutorial-integrate-ringcentral-with-azure-active-directory"></a>Tutorial: Integrieren von RingCentral in Azure Active Directory

In diesem Tutorial erfahren Sie, wie Sie RingCentral in Azure Active Directory (Azure AD) integrieren. Bei der Integration von RingCentral in Azure AD haben Sie folgende Möglichkeiten:

* Steuern Sie in Azure AD, wer Zugriff auf RingCentral hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei RingCentral anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* RingCentral-Abonnement, für das einmaliges Anmelden aktiviert ist

> [!NOTE]
> Diese Integration kann auch über die Azure AD-Umgebung für die US Government-Cloud verwendet werden. Sie finden diese Anwendung im Azure AD-Katalog für US Government-Cloudanwendungen und konfigurieren sie auf die gleiche Weise wie in der öffentlichen Cloud.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* RingCentral unterstützt **IDP-initiiertes** einmaliges Anmelden.

* RingCentral unterstützt die [Automatisierte Benutzerbereitstellung](ringcentral-provisioning-tutorial.md).

## <a name="add-ringcentral-from-the-gallery"></a>Hinzufügen von RingCentral aus dem Katalog

Zum Konfigurieren der Integration von RingCentral in Azure AD müssen Sie RingCentral aus dem Katalog zu Ihrer Liste der verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **RingCentral** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **RingCentral** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-ringcentral"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für RingCentral

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit RingCentral mithilfe eines Testbenutzers mit dem Namen **Britta Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in RingCentral eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit RingCentral die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    * **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    * **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für RingCentral](#configure-ringcentral-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    * **[Erstellen eines RingCentral-Testbenutzers](#create-ringcentral-test-user)** , um eine Entsprechung von B. Simon in RingCentral zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **RingCentral** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus, wenn Sie über eine **Dienstanbieter-Metadatendatei** verfügen:

    1. Klicken Sie auf **Metadatendatei hochladen**.
    1. Klicken Sie auf das **Ordnerlogo**, wählen Sie die Metadatendatei aus, und klicken Sie auf **Hochladen**.
    1. Nach dem erfolgreichen Upload der Metadatendatei werden die Werte unter **Bezeichner** und **Antwort-URL** im Abschnitt **Grundlegende SAML-Konfiguration** automatisch eingefügt.

    > [!Note]
    > Sie können die **Dienstanbieter-Metadatendatei** auf der SSO-Konfigurationsseite für RingCentral abrufen, die im weiteren Verlauf des Tutorials erläutert wird.

1. Geben Sie Werte in die folgenden Felder ein, wenn Sie keine **Dienstanbieter-Metadatendatei** besitzen:

    a. Geben Sie im Textfeld **Bezeichner** eine der folgenden URLs ein:
  
    | Bezeichner |
    |--|
    |  `https://sso.ringcentral.com` |
    | `https://ssoeuro.ringcentral.com` |

    b. Geben Sie im Textfeld **Antwort-URL** eine der folgenden URLs ein:

    | Antwort-URL |
    |--|
    | `https://sso.ringcentral.com/sp/ACS.saml2` |
    | `https://ssoeuro.ringcentral.com/sp/ACS.saml2` |

1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf die Schaltfläche „Kopieren“, um die **App-Verbundmetadaten-URL** zu kopieren, und speichern Sie sie auf Ihrem Computer.

    ![Downloadlink für das Zertifikat](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

In diesem Abschnitt erstellen Sie im Azure-Portal eine Testbenutzerin mit dem Namen Britta Simon.

1. Wählen Sie im linken Bereich des Microsoft Azure-Portals **Azure Active Directory** > **Benutzer** > **Alle Benutzer** aus.
1. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.
1. Führen Sie unter den Eigenschaften für **Benutzer** die folgenden Schritte aus:
   1. Geben Sie im Feld **Name** die Zeichenfolge `Britta Simon` ein.  
   1. Geben Sie im Feld **Benutzername** die Zeichenfolge username@companydomain.extension ein. Beispiel: `BrittaSimon@contoso.com`.
   1. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**, und notieren Sie sich den Wert aus dem Feld **Kennwort**.
   1. Klicken Sie auf **Erstellen**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie dem Benutzer Zugriff auf RingCentral gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste den Eintrag **RingCentral** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-ringcentral-sso"></a>Konfigurieren des einmaligen Anmeldens für RingCentral

1. Wenn Sie die Konfiguration in RingCentral automatisieren möchten, müssen Sie die **Browsererweiterung „Meine Apps“ für die sichere Anmeldung** installieren, indem Sie auf **Erweiterung installieren** klicken.

    ![Erweiterung „Meine Apps“](common/install-myappssecure-extension.png)

1. Klicken Sie nach dem Hinzufügen der Erweiterung zum Browser auf **RingCentral einrichten**, um zur Anwendung RingCentral weitergeleitet zu werden. Geben Sie dort die Administratoranmeldeinformationen ein, um sich bei RingCentral anzumelden. Die Browsererweiterung konfiguriert die Anwendung automatisch für Sie und automatisiert die Schritte 3 bis 7.

    ![Einrichtungskonfiguration](common/setup-sso.png)

1. Wenn Sie RingCentral manuell einrichten möchten, melden Sie sich in einem neuen Webbrowserfenster bei der RingCentral-Unternehmenswebsite als Administrator an, und führen Sie die folgenden Schritte aus:

1. Klicken Sie oben auf **Tools**.

    ![Screenshot: Auswahl von „Tools“ auf der Unternehmenswebsite von RingCentral](./media/ringcentral-tutorial/ringcentral-1.png)

1. Navigieren Sie zu **Einmaliges Anmelden**.

    ![Screenshot: Auswahl von „Einmaliges Anmelden“ im Menü „Tools“](./media/ringcentral-tutorial/ringcentral-2.png)

1. Klicken Sie auf der Seite **Einmaliges Anmelden** im Abschnitt **SSO-Konfiguration** unter **Schritt 1** auf **Bearbeiten** und führen Sie die folgenden Schritte aus:

    ![Screenshot: Seite „SSO-Konfiguration“ mit Auswahl von „Bearbeiten“](./media/ringcentral-tutorial/ringcentral-3.png)

1. Führen Sie auf der Seite **Einmaliges Anmelden einrichten** die folgenden Schritte aus:

    ![Screenshot: Seite „Einmaliges Anmelden einrichten“, auf der Sie IdP-Metadaten hochladen können](./media/ringcentral-tutorial/ringcentral-4.png)

    a. Klicken Sie zum Hochladen der Metadatendatei, die Sie aus dem Azure-Portal heruntergeladen haben, auf **Durchsuchen**.

    b. Nach dem Upload der Metadatendatei werden die Werte im Abschnitt **Allgemeine SSO-Informationen** automatisch ausgefüllt.

    c. Wählen Sie unter dem Abschnitt **Attributzuordnung** für **E-Mail-Attribut zuordnen zu** die Adresse `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` aus.

    d. Klicken Sie auf **Speichern**.

    e. Klicken Sie in **Schritt 2** auf **Herunterladen**, um die **Dienstanbieter-Metadatendatei** herunterzuladen. Laden Sie die Datei im Abschnitt **Grundlegende SAML-Konfiguration** hoch, damit die Werte **Bezeichner** und **Antwort-URL** im Azure-Portal automatisch ausgefüllt werden.

    ![Screenshot: Seite „SSO-Konfiguration“ mit Auswahl von „Herunterladen“](./media/ringcentral-tutorial/ringcentral-6.png) 

    f. Navigieren Sie auf der gleichen Seite zum Abschnitt **SSO aktivieren**, und führen Sie die folgenden Schritte aus:

    ![Screenshot: Abschnitt „SSO aktivieren“ zum Fertigstellen der Konfiguration](./media/ringcentral-tutorial/ringcentral-5.png)

    * Wählen Sie **SSO-Dienst aktivieren** aus.

    * Wählen Sie **Benutzeranmeldung mit SSO- oder RingCentral-Anmeldeinformationen zulassen** aus.

    * Klicken Sie auf **Speichern**.

### <a name="create-ringcentral-test-user"></a>Erstellen eines RingCentral-Testbenutzers

In diesem Abschnitt erstellen Sie in RingCentral einen Benutzer namens Britta Simon. Wenden Sie sich an das [Kundensupportteam von RingCentral](https://success.ringcentral.com/RCContactSupp), um die Benutzer auf der RingCentral-Plattform hinzuzufügen. Benutzer müssen erstellt und aktiviert werden, damit Sie einmaliges Anmelden verwenden können.

Außerdem unterstützt RingCentral die automatische Benutzerbereitstellung. Weitere Informationen zum Konfigurieren der automatischen Benutzerbereitstellung finden Sie [hier](./ringcentral-provisioning-tutorial.md).

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen:

* Klicken Sie im Azure-Portal auf „Diese Anwendung testen“. Dadurch sollten Sie automatisch bei der RingCentral-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie unter „Meine Apps“ auf die Kachel „RingCentral“ klicken, sollten Sie automatisch bei der RingCentral-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von RingCentral können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-any-app)
