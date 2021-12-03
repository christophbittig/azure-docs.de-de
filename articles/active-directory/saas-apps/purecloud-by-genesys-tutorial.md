---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory in Genesys Cloud for Azure | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Genesys Cloud for Azure konfigurieren.
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
ms.openlocfilehash: e3e5cc70f889eb24d50e67beb95a1e6324cecb02
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132341728"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-genesys-cloud-for-azure"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory in Genesys Cloud for Azure

In diesem Tutorial erfahren Sie, wie Sie Genesys Cloud for Azure in Azure Active Directory (Azure AD) integrieren. Die Integration von Genesys Cloud for Azure in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf Genesys Cloud for Azure hat.
* Ermöglichen Sie Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Genesys Cloud for Azure anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie nicht über ein Abonnement dieser Art verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) bei Genesys Cloud for Azure aktiviert ist.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Genesys Cloud for Azure unterstützt **SP- und IDP**-initiiertes einmaliges Anmelden.

* Genesys Cloud for Azure unterstützt [automatisierte Benutzerbereitstellung](purecloud-by-genesys-provisioning-tutorial.md).

> [!NOTE]
> Der Bezeichner dieser Anwendung ist ein fester Zeichenfolgenwert, daher kann in einem Mandanten nur eine Instanz konfiguriert werden.

## <a name="add-genesys-cloud-for-azure-from-the-gallery"></a>Hinzufügen von Genesys Cloud für Azure aus dem Katalog

Zum Konfigurieren der Integration von Genesys Cloud for Azure in Azure AD müssen Sie Genesys Cloud for Azure aus dem Katalog Ihrer Liste mit den verwalteten SaaS-Apps hinzufügen. Gehen Sie dazu folgendermaßen vor:

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Genesys Cloud for Azure** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Genesys Cloud for Azure** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-genesys-cloud-for-azure"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Genesys Cloud for Azure

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Genesys Cloud for Azure mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Genesys Cloud for Azure eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Genesys Cloud for Azure die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Genesys Cloud for Azure](#configure-genesys-cloud-for-azure-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
    1. **[Erstellen eines Genesys Cloud for Azure-Testbenutzers](#create-genesys-cloud-for-azure-test-user)** , um eine Entsprechung von B. Simon in Genesys Cloud for Azure zu erhalten, die mit der Darstellung in Azure AD verknüpft ist.
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Führen Sie die folgenden Schritte aus, um einmaliges Anmelden von Azure AD im Azure-Portal zu aktivieren:

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Genesys Cloud for Azure** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Wählen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** das Stiftsymbol für **Grundlegende SAML-Konfiguration** aus, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus, wenn Sie die Anwendung im **IDP**-initiierten Modus konfigurieren möchten:

    a. Geben Sie im Feld **Bezeichner** die URLs ein, die Ihrer Region entsprechen:
    
    | Bezeichner-URL |
    |---|
    | https://login.mypurecloud.com/saml |
    | https://login.mypurecloud.de/saml |
    | https://login.mypurecloud.jp/saml |
    | https://login.mypurecloud.ie/saml |
    | https://login.mypurecloud.com.au/saml |
    |

    b. Geben Sie im Feld **Antwort-URL** die URLs ein, die Ihrer Region entsprechen:

    | Antwort-URL |
    |---|
    | https://login.mypurecloud.com/saml |
    | https://login.mypurecloud.de/saml |
    | https://login.mypurecloud.jp/saml |
    | https://login.mypurecloud.ie/saml |
    | https://login.mypurecloud.com.au/saml |
    |

1. Wählen Sie **Zusätzliche URLs festlegen** aus, und führen Sie den folgenden Schritt aus, wenn Sie die Anwendung im **SP-initiierten** Modus konfigurieren möchten:

    Geben Sie im Feld **Anmelde-URL** die URLs ein, die Ihrer Region entsprechen:
    
    |Anmelde-URL |
    |---|
    | https://login.mypurecloud.com |
    | https://login.mypurecloud.de |
    | https://login.mypurecloud.jp |
    | https://login.mypurecloud.ie |
    | https://login.mypurecloud.com.au |
    |

1. Ihre Genesys Cloud for Azure-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Daher müssen Sie Ihrer Konfiguration der SAML-Tokenattribute benutzerdefinierte Attributzuordnungen hinzufügen. Der folgende Screenshot zeigt die Liste der Standardattribute:

    ![image](common/default-attributes.png)

1. Darüber hinaus wird von der Genesys Cloud for Azure-Anwendung erwartet, dass in der SAML-Antwort noch einige weitere Attribute zurückgegeben werden, wie in der folgenden Tabelle gezeigt. Diese Attribute werden ebenfalls vorab aufgefüllt, Sie können sie jedoch nach Bedarf überprüfen.

    | Name | Quellattribut|
    | ---------------| --------------- |
    | Email | user.userprincipalname |
    | OrganizationName | `Your organization name` |

1. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** zum Eintrag **Zertifikat (Base64)** . Wählen Sie **Herunterladen** aus, um das Zertifikat herunterzuladen, und speichern Sie es auf Ihrem Computer.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

1. Kopieren Sie im Abschnitt **Genesys Cloud for Azure einrichten** die entsprechende(n) URL(s) gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

In diesem Abschnitt erstellen Sie im Azure-Portal einen Testbenutzer mit dem Namen B. Simon:

1. Wählen Sie im Azure-Portal im linken Bereich **Azure Active Directory** > **Benutzer** > **Alle Benutzer** aus.
1. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.
1. Führen Sie unter den Eigenschaften für **Benutzer** die folgenden Schritte aus:
   1. Geben Sie im Feld **Name** die Zeichenfolge `B.Simon` ein.  
   1. Geben Sie im Feld **Benutzername** den Benutzernamen im folgenden Format ein: username@companydomain.extension. Beispiel: `B.Simon@contoso.com`.
   1. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**, und notieren Sie den Wert im Feld **Kennwort**.
   1. Klicken Sie auf **Erstellen**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie B. Simon das einmalige Anmelden von Azure, indem Sie ihr Zugriff auf Genesys Cloud for Azure gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Genesys Cloud for Azure** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-genesys-cloud-for-azure-sso"></a>Konfigurieren des einmaligen Anmeldens für Genesys Cloud for Azure

1. Melden Sie sich in einem anderen Webbrowserfenster bei Genesys Cloud for Azure als Administrator an.

1. Wählen Sie oben **Admin** aus, und navigieren Sie unter **Integrations** (Integrationen) zu **Single Sign-on** (Einmaliges Anmelden).

    ![Screenshot: Fenster „Admin“ von PureCloud zum Auswählen von „Single Sign-On“ (Einmaliges Anmelden)](./media/purecloud-by-genesys-tutorial/configure-1.png)

1. Wechseln Sie zur Registerkarte **ADFS/Azure AD (Premium)** , und führen Sie die folgenden Schritte aus:

    ![Screenshot: Seite „Integrations“ (Integrations), auf der Sie die beschriebenen Werte eingeben können](./media/purecloud-by-genesys-tutorial/configure-2.png)

    a. Wählen Sie **Browse** (Durchsuchen) aus, um das aus dem Azure-Portal heruntergeladene Base-64-codierte Zertifikat unter **ADFS Certificate** (ADFS-Zertifikat) hochzuladen.

    b. Fügen Sie in das Feld **ADFS Issuer URI** (ADFS-Aussteller-URI) den Wert von **Azure AD-Bezeichner** ein, den Sie aus dem Azure-Portal kopiert haben.

    c. Fügen Sie in das Feld **Target URI** (Ziel-URI) den Wert von **Anmelde-URL** ein, den Sie aus dem Azure-Portal kopiert haben.

    d. Wechseln Sie für den Wert **Relying Party Identifier** (Bezeichner der vertrauenden Seite) zum Azure-Portal. Wählen Sie dann auf der Anwendungsintegrationsseite für **Genesys Cloud for Azure** die Registerkarte **Eigenschaften** aus, und kopieren Sie den Wert **Anwendungs-ID**. Fügen Sie ihn in das Feld **Relying Party Identifier** (Bezeichner der vertrauenden Seite) ein.

    ![Screenshot: Bereich „Eigenschaften“ mit dem Wert für die Anwendungs-ID.](./media/purecloud-by-genesys-tutorial/configuration.png)

    e. Wählen Sie **Speichern** aus.

### <a name="create-genesys-cloud-for-azure-test-user"></a>Erstellen eines Genesys Cloud for Azure-Testbenutzers

Damit sich Azure AD-Benutzer bei Genesys Cloud for Azure anmelden können, müssen sie in Genesys Cloud for Azure bereitgestellt werden. In Genesys Cloud for Azure ist die Bereitstellung eine manuelle Aufgabe.

**Führen Sie zum Bereitstellen eines Benutzerkontos die folgenden Schritte aus:**

1. Melden Sie sich bei Genesys Cloud for Azure als Administrator an.

1. Wählen Sie oben **Admin** aus, und navigieren Sie unter **People & Permissions** (Personen und Berechtigungen) zu **People** (Personen).

    ![Screenshot: Fenster „Admin“ in PureCloud zum Auswählen von „People“ (Personen)](./media/purecloud-by-genesys-tutorial/configure-3.png)

1. Wählen Sie auf der Seite **People** (Personen) die Option **Add Person** (Person hinzufügen) aus.

    ![Screenshot: Seite „People“ (Personen), auf der Sie eine Person hinzufügen können](./media/purecloud-by-genesys-tutorial/configure-4.png)

1. Führen Sie im Dialogfeld **Add People to the Organization** (Personen der Organisation hinzufügen) die folgenden Schritte aus:

    ![Screenshot: Seite, auf der Sie die beschriebenen Werte eingeben können](./media/purecloud-by-genesys-tutorial/configure-5.png)

    a. Geben Sie im Feld **Full Name** (Vollständiger Name) den Namen eines Benutzers ein. Beispiel: **B.simon**

    b. Geben Sie im Feld **Email** (E-Mail) die E-Mail-Adresse des Benutzers ein. Beispiel: **b.simon\@contoso.com**

    c. Klicken Sie auf **Erstellen**.

> [!NOTE]
> Außerdem unterstützt Genesys Cloud for Azure automatische Benutzerbereitstellung. Weitere Informationen zum Konfigurieren der automatischen Benutzerbereitstellung finden Sie [hier](./purecloud-by-genesys-provisioning-tutorial.md).

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

#### <a name="sp-initiated"></a>SP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für Genesys Cloud for Azure weitergeleitet, wo Sie den Anmeldeflow initiieren können.  

* Rufen Sie direkt die Genesys Cloud for Azure-Anmelde-URL auf, und initiieren Sie dort den Anmeldeflow.

#### <a name="idp-initiated"></a>IDP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch sollten Sie automatisch bei der Genesys Cloud for Azure-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. 

Sie können auch den Microsoft-Bereich „Meine Apps“ verwenden, um die Anwendung in einem beliebigen Modus zu testen. Beim Klicken auf die Kachel „Genesys Cloud for Azure“ in „Meine Apps“ geschieht Folgendes: Wenn Sie den SP-Modus konfiguriert haben, werden Sie zum Initiieren des Anmeldeflows zur Anmeldeseite der Anwendung weitergeleitet. Wenn Sie den IDP-Modus konfiguriert haben, sollten Sie automatisch bei der Genesys Cloud for Azure-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Genesys Cloud for Azure können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-any-app)
