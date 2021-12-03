---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Cisco AnyConnect | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie einmaliges Anmelden zwischen Azure Active Directory und Cisco AnyConnect konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/11/2021
ms.author: jeedes
ms.openlocfilehash: 7dd906ec56327b85f8439fe5a0a27a0ef3555132
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132307781"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cisco-anyconnect"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit Cisco AnyConnect

In diesem Tutorial erfahren Sie, wie Sie Cisco AnyConnect in Azure Active Directory (Azure AD) integrieren. Die Integration von Cisco AnyConnect in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf Cisco AnyConnect hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Cisco AnyConnect anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Cisco AnyConnect-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Cisco AnyConnect unterstützt **IDP**-initiiertes einmaliges Anmelden.

## <a name="adding-cisco-anyconnect-from-the-gallery"></a>Hinzufügen von Cisco AnyConnect aus dem Katalog

Zum Konfigurieren der Integration von Cisco AnyConnect in Azure AD müssen Sie Cisco AnyConnect aus dem Katalog zu Ihrer Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Cisco AnyConnect** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Cisco AnyConnect** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-cisco-anyconnect"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Cisco AnyConnect

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Cisco AnyConnect mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Cisco AnyConnect eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Cisco AnyConnect die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Cisco AnyConnect](#configure-cisco-anyconnect-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Erstellen eines Cisco AnyConnect-Testbenutzers](#create-cisco-anyconnect-test-user)** , um ein Pendant zu B. Simon in Cisco AnyConnect zu erhalten, das mit ihrer Darstellung in Azure AD verknüpft ist
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Cisco AnyConnect** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Bearbeitungs- bzw. Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Geben Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** die Werte für die folgenden Felder ein: (Beachten Sie die Groß- und Kleinschreibung der Werte):

   1. Geben Sie im Textfeld **Bezeichner** eine URL im folgenden Format ein:  
      `https://<SUBDOMAIN>.YourCiscoServer.com/saml/sp/metadata/<Tunnel_Group_Name>`

   1. Geben Sie im Textfeld **Antwort-URL** eine URL im folgenden Format ein:  
      `https://<YOUR_CISCO_ANYCONNECT_FQDN>/+CSCOE+/saml/sp/acs?tgname=<Tunnel_Group_Name>`

    > [!NOTE]
    > Wenden Sie sich an den Cisco TAC-Support, um weitere Informationen zu diesen Werten zu erfragen. Aktualisieren Sie diese Werte mit dem von Cisco TAC bereitgestellten tatsächlichen Bezeichner und der Antwort-URL. Nehmen Sie Kontakt zum [Supportteam für den Cisco AnyConnect-Client](https://www.cisco.com/c/en/us/support/index.html) auf, um diese Werte zu beziehen. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

1. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** zum Eintrag **Zertifikat (Base64)** . Wählen Sie **Herunterladen** aus, um die Zertifikatdatei herunterzuladen, und speichern Sie es auf Ihrem Computer.

    ![Downloadlink für das Zertifikat](common/certificatebase64.png)

1. Kopieren Sie im Abschnitt **Cisco AnyConnect einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Kopieren der Konfiguration-URLs](common/copy-configuration-urls.png)

> [!NOTE]
> Wenn Sie das Onboarding für mehrere TGTs des Servers durchführen möchten, müssen Sie mehrere Instanzen der Cisco AnyConnect-Anwendung aus dem Katalog hinzufügen. Sie können auch Ihr eigenes Zertifikat für alle diese Anwendungsinstanzen in Azure AD hochladen. Auf diese Weise können Sie das gleiche Zertifikat für die Anwendungen verwenden, aber für jede Anwendung eine andere ID und Antwort-URL konfigurieren.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Cisco AnyConnect gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Cisco AnyConnect** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-cisco-anyconnect-sso"></a>Konfigurieren des einmaligen Anmeldens für Cisco AnyConnect

1. Hierzu wird zunächst die Befehlszeilenschnittstelle verwendet. Sie können aber gerne zu einem späteren Zeitpunkt hierher zurückkehren und die exemplarische Vorgehensweise für ASDM durchlaufen.

1. Stellen Sie eine Verbindung mit dem VPN-Gerät her. Hier wird ein ASA-Gerät mit der Codeversion 9.8 verwendet, und die VPN-Clients haben mindestens die Version 4.6.

1. Sie erstellen zunächst einen Vertrauenspunkt und importieren das SAML-Zertifikat.

   ```
    config t

    crypto ca trustpoint AzureAD-AC-SAML
      revocation-check none
      no id-usage
      enrollment terminal
      no ca-check
    crypto ca authenticate AzureAD-AC-SAML
    -----BEGIN CERTIFICATE-----
    …
    PEM Certificate Text from download goes here
    …
    -----END CERTIFICATE-----
    quit
   ```

1. Mit den folgenden Befehlen wird der SAML-Identitätsanbieter bereitgestellt:

   ```
    webvpn
    saml idp https://sts.windows.net/xxxxxxxxxxxxx/ (This is your Azure AD Identifier from the Set up Cisco AnyConnect section in the Azure portal)
    url sign-in https://login.microsoftonline.com/xxxxxxxxxxxxxxxxxxxxxx/saml2 (This is your Login URL from the Set up Cisco AnyConnect section in the Azure portal)
    url sign-out https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0 (This is Logout URL from the Set up Cisco AnyConnect section in the Azure portal)
    trustpoint idp AzureAD-AC-SAML
    trustpoint sp (Trustpoint for SAML Requests - you can use your existing external cert here)
    no force re-authentication
    no signature
    base-url https://my.asa.com
    ```

1. Nun können Sie die SAML-Authentifizierung auf eine VPN-Tunnelkonfiguration anwenden.

    ```
    tunnel-group AC-SAML webvpn-attributes
      saml identity-provider https://sts.windows.net/xxxxxxxxxxxxx/
      authentication saml
    end

    write mem
    ```

    > [!NOTE]
    > Bei der SAML-IdP-Konfiguration gibt es eine Problemumgehung. Wenn Sie Änderungen an der IdP-Konfiguration vornehmen, müssen Sie die SAML-Identitätsanbieterkonfiguration aus der Tunnelgruppe entfernen und erneut anwenden, damit die Änderungen übernommen werden.

### <a name="create-cisco-anyconnect-test-user"></a>Erstellen eines Cisco AnyConnect-Testbenutzers

In diesem Abschnitt erstellen Sie in Cisco AnyConnect einen Benutzer mit dem Namen Britta Simon. Wenden Sie sich an das [Cisco AnyConnect-Supportteam](https://www.cisco.com/c/en/us/support/index.html), um die Benutzer auf der Cisco AnyConnect-Plattform hinzuzufügen. Benutzer müssen erstellt und aktiviert werden, damit Sie einmaliges Anmelden verwenden können.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen:

* Klicken Sie im Azure-Portal auf „Diese Anwendung testen“. Dadurch sollten Sie automatisch bei der Cisco AnyConnect-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben.
* Sie können den Microsoft-Zugriffsbereich verwenden. Wenn Sie im Zugriffsbereich auf die Kachel „Cisco AnyConnect“ klicken, sollten Sie automatisch bei der Cisco AnyConnect-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zum Zugriffsbereich finden Sie unter [Einführung in den Zugriffsbereich](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Cisco AnyConnect können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-any-app)
