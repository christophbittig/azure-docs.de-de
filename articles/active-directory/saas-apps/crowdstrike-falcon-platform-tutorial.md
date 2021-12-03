---
title: 'Tutorial: Azure AD SSO-Integration mit der Crowdstrike Falcon Plattform'
description: Lernen Sie, wie man Einmaliges Anmelden zwischen Azure Active Directory und Crowdstrike Falcon Plattform konfiguriert.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/02/2021
ms.author: jeedes
ms.openlocfilehash: 1b44bc1ad77e6a92cda9b085434b756b6598c060
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132287987"
---
# <a name="tutorial-azure-ad-sso-integration-with-crowdstrike-falcon-platform"></a>Tutorial: Azure AD SSO-Integration mit der Crowdstrike Falcon Plattform

In diesem Tutorial lernen Sie, wie Sie Crowdstrike Falcon Plattform mit Azure Active Directory (Azure AD) integrieren können. Wenn Sie Crowdstrike Falcon Plattform mit Azure AD integrieren, können Sie:

* In Azure AD kontrollieren, wer Zugriff auf Crowdstrike Falcon Plattform hat.
* Es Ihren Benutzern ermöglichen, sich automatisch mit ihren Azure AD-Konten bei der Crowdstrike Falcon Plattform anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein gültiges Crowdstrike Falcon-Abonnement.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Crowdstrike Falcon Plattform unterstützt **SP und IDP** veranlasste SSO.

## <a name="adding-crowdstrike-falcon-platform-from-the-gallery"></a>Hinzufügen von der Crowdstrike Falcon Plattform aus dem Katalog

Um die Integration von Crowdstrike Falcon Plattform in Azure AD zu konfigurieren, müssen Sie die Crowdstrike Falcon Plattform aus dem Katalog zu Ihrer Liste der verwalteten SaaS-Anwendungen hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus dem Katalog hinzufügen** **Crowdstrike Falcon Plattform** in das Suchfeld ein.
1. Wählen Sie **Crowdstrike Falcon Plattform** im Ergebnisfeld und fügen Sie die Anwendung hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.


## <a name="configure-and-test-azure-ad-sso-for-crowdstrike-falcon-platform"></a>Konfigurieren und Testen von Azure AD SSO für die Crowdstrike Falcon Plattform

Konfigurieren und testen Sie Azure AD SSO mit der Crowdstrike Falcon Plattform unter Verwendung eines Testbenutzers namens **B.Simon**. Damit SSO funktioniert, müssen Sie eine Verknüpfungsbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in der Crowdstrike Falcon Plattform herstellen.

Um Azure AD SSO mit der Crowdstrike Falcon Plattform zu konfigurieren und testen, führen Sie die folgenden Schritte durch:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren Sie die Crowdstrike Falcon Plattform SSO](#configure-crowdstrike-falcon-platform-sso)** – um die Einmaliges Anmelden-Einstellungen auf der Anwendungsseite zu konfigurieren.
    1. **[Erstellen eines Crowdstrike Falcon Plattform-Testbenutzers](#create-crowdstrike-falcon-platform-test-user)** – um ein Gegenüber von B.Simon in der Crowdstrike Falcon Plattform zu haben, das mit der Azure AD-Darstellung des Benutzers verknüpft ist.
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Suchen Sie im Azure-Portal auf der Seite Anwendungsintegration von der **Crowdstrike Falcon Plattform** den Abschnitt **Verwalten** und wählen Sie **Einmaliges Anmelden**.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Geben Sie im Abschnitt **Grundlegende SAML-Konfiguration** die Werte in die folgenden Felder ein, wenn Sie die Anwendung im **IDP**-initiierten Modus konfigurieren möchten:

    a. Geben Sie im Textfeld **Bezeichner** eine der folgenden URLs ein:

    | Bezeichner |
    | -------------- |
    | `https://falcon.crowdstrike.com/saml/metadata` |
    | `https://falcon.us-2.crowdstrike.com/saml/metadata` |
    | `https://falcon.eu-1.crowdstrike.com/saml/metadata` |
    | `https://falcon.laggar.gcw.crowdstrike.com/saml/metadata` |
    |

    b. Geben Sie im Textfeld **Antwort-URL** eine der folgenden URLs ein:

    | Antwort-URL |
    | -------------- |
    | `https://falcon.crowdstrike.com/saml/acs` |
    | `https://falcon.us-2.crowdstrike.com/saml/acs` |
    | `https://falcon.eu-1.crowdstrike.com/saml/acs` |
    | `https://falcon.laggar.gcw.crowdstrike.com/saml/acs` |
    |

1. Klicken Sie auf **Zusätzliche URLs festlegen**, und führen Sie den folgenden Schritt aus, wenn Sie die Anwendung im **SP-initiierten Modus** konfigurieren möchten:

    Geben Sie im Textfeld **Anmelde-URL** eine der folgenden URLs ein:

    | Anmelde-URL |
    | -------------- |
    | `https://falcon.crowdstrike.com/login` |
    | `https://falcon.us-2.crowdstrike.com/login` |
    | `https://falcon.eu-1.crowdstrike.com/login` |
    | `https://falcon.laggar.gcw.crowdstrike.com/login` |
    |

1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf die Schaltfläche „Kopieren“, um die **App-Verbundmetadaten-URL** zu kopieren, und speichern Sie sie auf Ihrem Computer.

    ![Downloadlink für das Zertifikat](common/copy-metadataurl.png)
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

In diesem Abschnitt aktivieren Sie B.Simon für die Nutzung von Azure Einmaliges Anmelden, indem Sie den Zugriff auf die Crowdstrike Falcon Plattform gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **CrowdStrike Falcon Plattform**.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-crowdstrike-falcon-platform-sso"></a>Konfigurieren von der Crowdstrike Falcon Plattform SSO

Um Einmaliges Anmelden auf der Seite von **Crowdstrike Falcon Plattform** zu konfigurieren, müssen Sie die **App Federation Metadata Url** an das [Crowdstrike Falcon Plattform-Supportteam](mailto:support@crowdstrike.com) senden. Es führt die Einrichtung durch, damit die SAML-SSO-Verbindung auf beiden Seiten richtig festgelegt ist.

### <a name="create-crowdstrike-falcon-platform-test-user"></a>Erstellen von einem CrowdStrike Plattform-Testbenutzer

In diesem Abschnitt erstellen Sie einen Benutzer namens Britta Simon in der Crowdstrike Falcon Plattform. Arbeiten Sie mit dem [Crowdstrike Falcon Plattform-Supportteam](mailto:support@crowdstrike.com) zusammen, um die Benutzer in der Crowdstrike Falcon Plattform-Plattform hinzuzufügen. Benutzer müssen erstellt und aktiviert werden, damit Sie einmaliges Anmelden verwenden können.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

#### <a name="sp-initiated"></a>SP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Crowdstrike Falcon Plattform Anmelde-URL umgeleitet, wo Sie den Anmeldevorgang veranlassen können.  

* Gehen Sie direkt zur Crowdstrike Falcon Plattform Anmelde-URL und veranlassen Sie den Anmeldevorgang von dort aus.

#### <a name="idp-initiated"></a>IDP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen** und Sie sollten automatisch bei der Crowdstrike Falcon Plattform angemeldet werden, für die Sie das SSO eingerichtet haben 

Sie können auch den Microsoft-Bereich „Meine Apps“ verwenden, um die Anwendung in einem beliebigen Modus zu testen. Wenn Sie in Meine Apps auf die Kachel CrowdStrike Falcon Plattform klicken, werden Sie, wenn Sie im SP-Modus konfiguriert sind, zur Anmeldeseite der Anwendung umgeleitet, um den Anmeldevorgang zu veranlassen, und wenn Sie im DP-Modus konfiguriert sind, sollten Sie automatisch bei der CrowdStrike Falcon Plattform angemeldet werden, für die Sie das SSO eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie die CrowdStrike Falcon Plattform konfiguriert haben, können Sie die Sitzungskontrolle erzwingen, die vor Exfiltration und Infiltration der sensiblen Daten Ihres Unternehmens in Echtzeit schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-any-app)
