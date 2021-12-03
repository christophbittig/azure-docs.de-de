---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Catchpoint'
description: Hier erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Catchpoint konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/16/2020
ms.author: jeedes
ms.openlocfilehash: 7c313863cc4c25a7285c9c682225259e67235bd0
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132326814"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-catchpoint"></a>Tutorial: Integration des einmaligen Anmeldens von Azure Active Directory mit Catchpoint

In diesem Tutorial erfahren Sie, wie Sie Catchpoint in Azure Active Directory (Azure AD) integrieren. Die Integration von Catchpoint in Azure AD ermöglicht Folgendes:

- Steuern Sie den Benutzerzugriff auf Catchpoint über Azure AD.
- Aktivieren Sie die automatische Catchpoint-Anmeldung für Benutzer mit Azure AD-Konten.
- Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

- Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
- Ein Catchpoint-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

- Catchpoint unterstützt SP- und IdP-initiiertes einmaliges Anmelden.
- Catchpoint unterstützt die JIT-Benutzerbereitstellung (Just-In-Time).

## <a name="add-catchpoint-from-the-gallery"></a>Hinzufügen von Catchpoint über den Katalog

Fügen Sie zum Konfigurieren der Integration von Catchpoint in Azure AD Catchpoint zu Ihrer Liste mit den verwalteten SaaS-Apps hinzu.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Bereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Catchpoint** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Catchpoint** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-catchpoint"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Catchpoint

Damit einmaliges Anmelden funktioniert, müssen Sie einen Azure AD-Benutzer mit einem Benutzer in Catchpoint verknüpfen. In diesem Tutorial konfigurieren wir einen Testbenutzer mit dem Namen **B. Simon**.

Durchlaufen Sie die folgenden Abschnitte:

1. [Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso), um dieses Feature für Ihre Benutzer zu aktivieren
   - [Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user), um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen
   - [Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user), um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen
1. [Konfigurieren des einmaligen Anmeldens für Catchpoint](#configure-catchpoint-sso), um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
   - [Erstellen eines Catchpoint-Testbenutzers](#create-a-catchpoint-test-user), um die Verknüpfung des Azure AD-Testkontos von B. Simon mit einem ähnlichen Konto in Catchpoint zu ermöglichen
1. [Testen des einmaligen Anmeldens](#test-sso), um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Führen Sie die folgenden Schritte im Azure-Portal aus, um das einmalige Anmelden von Azure AD zu aktivieren:

1. Melden Sie sich beim Azure-Portal an.
1. Navigieren Sie auf der Anwendungsintegrationsseite für **Catchpoint** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Wählen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** das Stiftsymbol aus, um die Einstellungen für **Grundlegende SAML-Konfiguration** zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Konfigurieren Sie den initiierten Modus für Catchpoint:

   - Geben Sie für den **IDP-initiierten** Modus die Werte für die folgenden Felder an:
     - Für **Bezeichner**: `https://portal.catchpoint.com/SAML2`
     - Für **Antwort-URL**: `https://portal.catchpoint.com/ui/Entry/SingleSignOn.aspx`
   - Wählen Sie für den **SP-initiierten** Modus die Option **Zusätzliche URLs festlegen** aus, und geben Sie den folgenden Wert ein:
     - Für **Anmelde-URL**: `https://portal.catchpoint.com/ui/Entry/SingleSignOn.aspx`

1. Die Catchpoint-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Fügen Sie Ihrer Konfiguration der SAML-Tokenattribute benutzerdefinierte Attributzuordnungen hinzu. Die nachfolgende Tabelle enthält die Liste der Standardattribute:

   | Name                   | Quellattribut       |
   | ---------------------- | ---------------------- |
   | Givenname              | user.givenneame        |
   | Surname                | user.surname           |
   | Emailaddress           | user.mail              |
   | Name                   | user.userprincipalname |
   | Eindeutige Benutzer-ID | user.userprincipalname |

   ![Screenshot: Liste „Benutzerattribute und Ansprüche“](common/default-attributes.png)

1. Außerdem erwartet die Catchpoint-Anwendung, dass ein weiteres Attribut in einer SAML-Antwort übermittelt wird. Siehe hierzu die folgende Tabelle. Dieses Attribut ist ebenfalls bereits ausgefüllt, Sie können es jedoch überprüfen und an Ihre Anforderungen anpassen.

   | Name      | Quellattribut  |
   | --------- | ----------------- |
   | Namespace | user.assignedrole |

   > [!NOTE]
   > Der `namespace`-Anspruch muss dem Kontonamen zugeordnet werden. Dieser Kontoname muss mit einer Rolle in Azure AD eingerichtet werden, damit er in einer SAML-Antwort zurückgegeben werden kann. Weitere Informationen zu Rollen in Azure AD finden Sie unter [Gewusst wie: Konfigurieren von im SAML-Token ausgestellten Rollenansprüchen für Unternehmensanwendungen](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui).

1. Navigieren Sie zur Seite **Einmaliges Anmelden (SSO) mit SAML einrichten**. Navigieren Sie im Abschnitt **SAML-Signaturzertifikat** zur Option **Zertifikat (Base64)** . Wählen Sie **Herunterladen** aus, um das Zertifikat auf Ihrem Computer zu speichern.

   ![Downloadlink für das Zertifikat](common/certificatebase64.png)

1. Kopieren Sie im Abschnitt **Catchpoint einrichten** die URLs, die Sie in einem späteren Schritt benötigen.

   ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

In diesem Abschnitt erstellen Sie im Azure-Portal einen Azure AD-Testbenutzer mit dem Namen B. Simon.

1. Wählen Sie im Azure-Portal im linken Bereich **Azure Active Directory** > **Benutzer** > **Alle Benutzer** aus.
1. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.
1. Führen Sie unter den Eigenschaften für **Benutzer** die folgenden Schritte aus:
   1. Geben Sie im Feld **Name** die Zeichenfolge `B.Simon` ein.
   1. Geben Sie im Feld **Benutzername** die Zeichenfolge username@companydomain.extension ein. Geben Sie z. B. `B.Simon@contoso.com` ein
   1. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**. Notieren Sie sich den angezeigten Kennwortwert.
   1. Klicken Sie auf **Erstellen**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Catchpoint gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Catchpoint** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie die Schaltfläche **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste der Benutzer die Option **B.Simon** aus. Klicken Sie am unteren Bildschirmrand auf **Auswählen**.
1. Wenn Sie die Rollen wie oben erläutert eingerichtet haben, können Sie sie in der Dropdownliste **Rolle auswählen** auswählen.
1. Wählen Sie im Dialogfeld **Zuweisung hinzufügen** die Option **Zuweisen** aus.

## <a name="configure-catchpoint-sso"></a>Konfigurieren des einmaligen Anmeldens für Catchpoint

1. Melden Sie sich in einem anderen Webbrowserfenster bei der Catchpoint-Anwendung als Administrator an.

1. Wählen Sie das Symbol für **Einstellungen** und dann **SSO Identity Provider** (SSO-Identitätsanbieter) aus.

   ![Screenshot: Catchpoint-Einstellungen mit der ausgewählten Option „SSO Identity Provider“ (SSO-Identitätsanbieter)](./media/catchpoint-tutorial/configuration1.png)

1. Füllen Sie auf der Seite **Single Sign-On** (Einmaliges Anmelden) die folgenden Felder aus:

   ![Screenshot: Seite „Single Sign On“ (Einmaliges Anmelden) in Catchpoint](./media/catchpoint-tutorial/configuration2.png)

   | Feld                        | Wert                                                                                                           |
   | ---------------------------- | --------------------------------------------------------------------------------------------------------------- |
   | **Namespace**                | Gültiger Namespacewert                                                                                        |
   | **Identity Provider Issuer** (Aussteller des Identitätsanbieters) | Der Wert `Azure AD Identifier` aus dem Azure-Portal                                                          |
   | **Single Sign On Url** (URL für einmaliges Anmelden)       | Der Wert `Login URL` aus dem Azure-Portal                                                                    |
   | **Certificate**              | Der Inhalt der heruntergeladenen `Certificate (Base64)`-Datei aus dem Azure-Portal. Verwenden Sie Editor zum Anzeigen und Kopieren. |

   Sie können auch die **Verbundmetadaten-XML** hochladen, indem Sie die Option **Upload Metadata** (Metadaten hochladen) auswählen.

1. Wählen Sie **Speichern** aus.

### <a name="create-a-catchpoint-test-user"></a>Erstellen eines Catchpoint-Testbenutzers

Catchpoint unterstützt die Just-In-Time-Benutzerbereitstellung (standardmäßig aktiviert). Dieser Abschnitt enthält keine Aktionselemente. Ist B. Simon noch nicht in Catchpoint vorhanden, wird sie nach der Authentifizierung erstellt.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen:

#### <a name="sp-initiated"></a>SP-initiiert:

- Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für Catchpoint weitergeleitet, wo Sie den Anmeldeflow initiieren können.

- Rufen Sie direkt die Catchpoint-Anmelde-URL auf, und initiieren Sie den Anmeldeflow.

#### <a name="idp-initiated"></a>IDP-initiiert:

- Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch sollten Sie automatisch bei der Catchpoint-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben.

Sie können auch den Microsoft-Bereich „Meine Apps“ verwenden, um die Anwendung in einem beliebigen Modus zu testen. Beim Klicken auf die Kachel „Catchpoint“ in „Meine Apps“ geschieht Folgendes: Wenn Sie den SP-Modus konfiguriert haben, werden Sie zum Initiieren des Anmeldeflows zur Anmeldeseite der Anwendung weitergeleitet. Wenn Sie den IDP-Modus konfiguriert haben, sollten Sie automatisch bei der Catchpoint-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

> [!NOTE]
> Wenn Sie sich über die Anmeldeseite bei der Catchpoint-Anwendung angemeldet haben, geben Sie nach der Angabe der **Catchpoint-Anmeldeinformationen** den gültigen **Namespacewert** in das Feld **Company Credentials (SSO)** (Anmeldeinformationen des Unternehmens (SSO)) ein, und wählen Sie **Login** (Anmelden) aus.
>
> ![Catchpoint-Konfiguration](./media/catchpoint-tutorial/loginimage.png)

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Catchpoint können Sie die Sitzungssteuerung erzwingen. Diese schützt in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten. Die Sitzungssteuerung ist eine Erweiterung des bedingten Zugriffs. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-any-app)
