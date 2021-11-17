---
title: 'Tutorial: Azure Active Directory-Integration mit Five9 Plus Adapter (CTI, Contact Center-Agents) | Microsoft-Dokumentation'
description: Informationen zum Konfigurieren des einmaligen Anmeldens zwischen Azure Active Directory und Five9 Plus Adapter (CTI, Contact Center-Agents)
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/17/2021
ms.author: jeedes
ms.openlocfilehash: 7c78580358f549913087b5d55fe7998631bfc874
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132348576"
---
# <a name="tutorial-azure-active-directory-integration-with-five9-plus-adapter-cti-contact-center-agents"></a>Tutorial: Azure Active Directory-Integration mit Five9 Plus Adapter (CTI, Contact Center -)

In diesem Tutorial erfahren Sie, wie Sie Five9 Plus Adapter (CTI, Contact Center-Agents) in Azure Active Directory (Azure AD) integrieren. Wenn Sie Five9 plus Adapter (CTI, Contact Center-Agents) mit Azure AD integrieren, können Sie Folgendes:

* Sie können in Azure AD steuern, wer Zugriff auf Five9 Plus Adapter (CTI, Contact Center Agents) hat.
* Sie können es Benutzern ermöglichen, dass sie mit ihren Azure AD-Konten automatisch bei Five9 Plus Adapter (CTI, Contact Center Agents) angemeldet werden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die Konfiguration der Azure AD-Konfiguration mit Five9 Plus Adapter (CTI, Contact Center-Agents) benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Sollten Sie nicht über eine Azure AD-Umgebung verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) verwenden.
* Five9 Plus Adapter (CTI, Contact Center Agents)-Abonnement, für das einmaliges Anmelden aktiviert .

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Five9 Plus Adapter (CTI, Contact Center Agents) unterstützt **IDP**-initiiertes einmaliges Anmelden.

> [!NOTE]
> Der Bezeichner dieser Anwendung ist ein fester Zeichenfolgenwert, daher kann in einem Mandanten nur eine Instanz konfiguriert werden.

## <a name="add-five9-plus-adapter-cti-contact-center-agents-from-the-gallery"></a>Hinzufügen von Five9 Plus Adapter (CTI, Contact Center-Agents) aus dem Katalog

Um die Integration von Five9 Plus Adapter (CTI, Contact Center-Agents) in Azure Active Directory zu konfigurieren, müssen Sie Five9 Plus Adapter (CTI, Contact Center-Agents) aus dem Katalog Ihrer Liste der verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Bereich **aus dem Katalog hinzufügen** **Five9 plus Adapter (CTI, Contact Center-Agents)** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Five9 plus Adapter (CTI, Contact Center-Agents)** aus, und fügen Sie die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-five9-plus-adapter-cti-contact-center-agents"></a>Konfigurieren und testen Azure AD für Five9 plus Adapter (CTI, Contact Center-Agents)

Konfigurieren und testen Sie Azure AD mit Five9 Plus Adapter (CTI, Contact Center Agents) mithilfe einer Testbenutzerin mit dem Namen **B. Simon**. Damit SSO funktioniert, müssen Sie eine Verknüpfungsbeziehung zwischen einem Azure AD-Benutzer und dem zugehörigen Benutzer im Five9 Plus Adapter (CTI, Contact Center Agents) herstellen.

Führen Sie für die Konfiguration des einmaligen Anmeldens von Azure AD mit Five9 Plus Adapter (CTI, Contact Center Agents) die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des SSO für Five9 Plus Adapter (CTI, Contact Center Agents)](#configure-five9-plus-adapter-cti-contact-center-agents-sso)** -um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
    1. **[Erstellen eines Five9 Plus Adapter (CTI, Contact Center Agents)-Testbenutzers](#create-five9-plus-adapter-cti-contact-center-agents-test-user)** -um eine Entsprechung von B. Simon in Five9 Plus Adapter (CTI, Contact Center Agents) zu erhalten, die mit der Darstellung des Benutzers in Azure AD verknüpft ist.
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Wählen Sie im **Azure-Portal** auf der Anwendungsintegrationsseite für Five9 Plus Adapter (CTI, Contact Center Agents) **unter verwalten** die Option **Einmaliges Anmelden**.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Führen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** die folgenden Schritte aus:

    a. Geben Sie im Textfeld **Bezeichner** eine der folgenden URLs ein:
    
    |    Environment      |       URL      |
    | :-- | :-- |
    | Für „Five9 Plus Adapter für Microsoft Dynamics CRM“ | `https://app.five9.com/appsvcs/saml/metadata/alias/msdc` |
    | Für „Five9 Plus Adapter für Zendesk“ | `https://app.five9.com/appsvcs/saml/metadata/alias/zd` |
    | Für „Five9 Plus Adapter für Agent Desktop Toolkit“ | `https://app.five9.com/appsvcs/saml/metadata/alias/adt` |

    b. Geben Sie im Textfeld **Antwort-URL** eine der folgenden URLs ein:

    |      Environment     |      URL      |
    | :--                  | :--           |
    | Für „Five9 Plus Adapter für Microsoft Dynamics CRM“ | `https://app.five9.com/appsvcs/saml/SSO/alias/msdc` |
    | Für „Five9 Plus Adapter für Zendesk“ | `https://app.five9.com/appsvcs/saml/SSO/alias/zd` |
    | Für „Five9 Plus Adapter für Agent Desktop Toolkit“ | `https://app.five9.com/appsvcs/saml/SSO/alias/adt` |

6. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um das Ihrer Anforderung entsprechende **Zertifikat (Base64)** aus den angegebenen Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

7. Kopieren Sie im Abschnitt **Five9 Plus Adapter (CTI, Contact Center Agents) einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Five9 Plus Adapter (CTI, Contact Center-Agents) gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Liste der Anwendungen **Five9 Plus Adapter (CTI, Contact Center-Agents)** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-five9-plus-adapter-cti-contact-center-agents-sso"></a>Konfigurieren des einmaligen Anmeldens für Five9 Plus Adapter (CTI, Contact Center Agents)

1. Zum Konfigurieren des einmaligen Anmeldens bei **Five9 Plus Adapter (CTI, Contact Center Agents)** müssen Sie das heruntergeladene **Zertifikat (Base64)** und die entsprechenden kopierten URLs an das [Supportteam von Five9 Plus Adapter (CTI, Contact Center Agents)](https://www.five9.com/about/contact) senden. Um zudem SSO weiter zu konfigurieren, befolgen Sie die nachstehenden Schritte gemäß des Adapters:

    a. Administratorhandbuch „Five9 Plus Adapter für Agent Desktop Toolkit“: [https://webapps.five9.com/assets/files/for_customers/documentation/integrations/agent-desktop-toolkit/plus-agent-desktop-toolkit-administrators-guide.pdf](https://webapps.five9.com/assets/files/for_customers/documentation/integrations/agent-desktop-toolkit/plus-agent-desktop-toolkit-administrators-guide.pdf)
    
    b. Administratorhandbuch „Five9 Plus Adapter für Microsoft Dynamics CRM“: [https://webapps.five9.com/assets/files/for_customers/documentation/integrations/microsoft/microsoft-administrators-guide.pdf](https://webapps.five9.com/assets/files/for_customers/documentation/integrations/microsoft/microsoft-administrators-guide.pdf)
    
    c. Administratorhandbuch „Five9 Plus Adapter für Zendesk“: [https://webapps.five9.com/assets/files/for_customers/documentation/integrations/zendesk/zendesk-plus-administrators-guide.pdf](https://webapps.five9.com/assets/files/for_customers/documentation/integrations/zendesk/zendesk-plus-administrators-guide.pdf)

### <a name="create-five9-plus-adapter-cti-contact-center-agents-test-user"></a>Erstellen eines Five9 Plus Adapter (CTI, Contact Center Agents)-Testbenutzers

In diesem Abschnitt erstellen Sie einen Benutzer namens Britta Simon in Five9 Plus Adapter (CTI, Contact Center-Agents). Arbeiten Sie mit dem [Supportteam von Five9 Plus Adapter (CTI, Contact Center-Agents)](https://www.five9.com/about/contact), um die Benutzer zur Five9 Plus Adapter-Plattform (CTI, Contact Center-Agents) hinzuzufügen. Benutzer müssen erstellt und aktiviert werden, damit Sie einmaliges Anmelden verwenden können. 

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen:

* Klicken Sie im Azure-Portal auf „Diese Anwendung testen“ und dadurch sollten Sie automatisch bei der Five9 Plus Adapter-Instanz (CTI, Contact Center Agents) angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie unter „Meine Apps“ auf die Kachel „Five9 Plus Adapter (CTI, Contact Center Agents)“ klicken, sollten Sie automatisch bei Ihrer Instanz von Five9 Plus Adapter (CTI, Contact Center Agents) angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Five9 Plus Adapter (CTI, Contact Center Agents) können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-any-app)
