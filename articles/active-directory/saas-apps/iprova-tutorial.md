---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory in Zenya | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie einmaliges Anmelden zwischen Azure Active Directory und Zenya konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/01/2021
ms.author: jeedes
ms.openlocfilehash: 25440815a219b353ebd44ba6ce60e504cb1862b4
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132346532"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-zenya"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory in Zenya

In diesem Tutorial erfahren Sie, wie Sie Zenya in Azure Active Directory (Azure AD) integrieren. Die Integration von Zenya in Azure AD ermöglicht Folgendes:

* Steuern Sie in Azure AD, wer Zugriff auf Zenya hat.
* Ermöglichen Sie Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Zenya anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) bei Zenya aktiviert ist.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Zenya unterstützt **SP**-initiiertes einmaliges Anmelden.
* Zenya unterstützt die [automatisierte Benutzerbereitstellung](iprova-provisioning-tutorial.md).

## <a name="add-zenya-from-the-gallery"></a>Hinzufügen von Zenya aus dem Katalog

Zum Konfigurieren der Integration von Zenya in Azure AD müssen Sie Zenya aus dem Katalog der Liste der verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Zenya** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Zenya** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-zenya"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Zenya

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Zenya mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Zenya eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Zenya die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren des einmaligen Anmeldens für Zenya](#configure-zenya-sso)** , um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
    1. **[Erstellen eines Zenya-Testbenutzers](#create-zenya-test-user)** , um ein Pendant von B. Simon in Zenya zu erhalten, das mit der Darstellung in Azure AD verknüpft ist.
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="retrieve-configuration-information-from-zenya"></a>Abrufen von Konfigurationsinformationen von Zenya

In diesem Abschnitt rufen Sie Informationen aus Zenya ab, um das einmalige Anmelden von Azure AD zu konfigurieren.

1. Öffnen Sie einen Webbrowser, und verwenden Sie die folgenden URL-Muster, um zur **SAML2-Informationsseite** in Zenya zu gelangen:
    
     `https://<SUBDOMAIN>.zenya.work/saml2info`   
     `https://<SUBDOMAIN>.iprova.nl/saml2info`  
     `https://<SUBDOMAIN>.iprova.be/saml2info`  
     `https://<SUBDOMAIN>.iprova.eu/saml2info` 

    ![Anzeigen der Zenya SAML2-Infoseite](media/iprova-tutorial/information.png)

1. Lassen Sie die Registerkarte Browser geöffnet, während Sie mit den nächsten Schritten in einer anderen Registerkarte des Browsers fortfahren.

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Zenya** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    a. Füllen Sie das Feld **Anmelde-URL** mit dem Wert aus, der hinter der Bezeichnung **Anmelde-URL** auf der **Zenya SAML2-Informationsseite** angezeigt wird. Diese Seite ist auf Ihrer anderen Browserregisterkarte weiterhin geöffnet.

    b. Füllen Sie das Feld **Bezeichner** mit dem Wert aus, der hinter der Bezeichnung **EntityID** auf der **Zenya SAML2-Informationsseite** angezeigt wird. Diese Seite ist auf Ihrer anderen Browserregisterkarte weiterhin geöffnet.

    c. Füllen Sie das Feld **Antwort-URL** mit dem Wert aus, der hinter der Bezeichnung **Antwort-URL** auf der **Zenya SAML2-Informationsseite** angezeigt wird. Diese Seite ist auf Ihrer anderen Browserregisterkarte weiterhin geöffnet.

1. Zenya erwartet die SAML-Assertionen in einem bestimmten Format. Daher müssen Sie Ihrer Konfiguration der SAML-Tokenattribute benutzerdefinierte Attributzuordnungen hinzufügen. Der folgende Screenshot zeigt die Liste der Standardattribute.

    ![image](common/default-attributes.png)

1. Darüber hinaus wird von der Zenya-Anwendung erwartet, dass in der SAML-Antwort noch einige weitere Attribute zurückgegeben werden (siehe unten). Diese Attribute werden ebenfalls vorab aufgefüllt, Sie können sie jedoch nach Bedarf überprüfen.

    | Name | Quellattribut| Namespace  |
    | ---------------| -------- | -----|
    | `samaccountname` | `user.onpremisessamaccountname`| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|

1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf die Schaltfläche „Kopieren“, um die **App-Verbundmetadaten-URL** zu kopieren, und speichern Sie sie auf Ihrem Computer.

    ![Downloadlink für das Zertifikat](common/copy-metadataurl.png)

## <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers

In diesem Abschnitt erstellen Sie im Azure-Portal einen Testbenutzer mit dem Namen B. Simon.

1. Wählen Sie im linken Bereich des Microsoft Azure-Portals **Azure Active Directory** > **Benutzer** > **Alle Benutzer** aus.
1. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.
1. Führen Sie unter den Eigenschaften für **Benutzer** die folgenden Schritte aus:
   1. Geben Sie im Feld **Name** die Zeichenfolge `B.Simon` ein.  
   1. Geben Sie im Feld **Benutzername** die Zeichenfolge username@companydomain.extension ein. Beispiel: `B.Simon@contoso.com`.
   1. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**, und notieren Sie sich den Wert aus dem Feld **Kennwort**.
   1. Klicken Sie auf **Erstellen**.

## <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Zenya gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Zenya** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-zenya-sso"></a>Konfigurieren des einmaligen Anmeldens für Zenya

1. Melden Sie sich mit dem **Administratorkonto** bei Zenya an.

2. Öffnen Sie das Menü **Gehe zu**.

3. Wählen Sie **Anwendungsverwaltung** aus.

4. Wählen Sie im Bereich **Systemeinstellungen** die Option **Allgemein** aus.

5. Wählen Sie **Bearbeiten** aus.

6. Scrollen Sie zu **Zugriffssteuerung**.

    ![Einstellungen für die Zenya-Zugriffssteuerung](media/iprova-tutorial/access-control.png)

7. Suchen Sie die Einstellung **Benutzer werden automatisch mit ihren Netzwerkkonten angemeldet**, und ändern Sie sie in **Ja, Authentifizierung über SAML**. Daraufhin werden zusätzliche Optionen angezeigt.

8. Klicken Sie auf **Einrichten**.

9. Klicken Sie auf **Weiter**.

10. Sie werden von Zenya gefragt, ob Sie Verbunddaten von einer URL herunterladen oder aus einer Datei hochladen möchten. Wählen Sie die Option **Von URL** aus.

    ![Herunterladen von Azure AD-Metadaten](media/iprova-tutorial/metadata.png)

11. Fügen Sie die Metadaten-URL ein, die Sie im letzten Schritt des Abschnitts „Konfigurieren des einmaliges Anmeldens bei Azure AD“ gespeichert haben.

12. Wählen Sie die pfeilförmige Schaltfläche aus, um die Metadaten aus Azure AD herunterzuladen.

13. Nach Abschluss des Downloadvorgangs wird die Bestätigungsmeldung **Gültige Verbunddatendatei heruntergeladen** angezeigt.

14. Wählen Sie **Weiter** aus.

15. Überspringen Sie vorerst die Option **Anmeldung testen**, und wählen Sie **Weiter** aus.

16. Wählen Sie in der Dropdownliste **Verwendung beanspruchen** die Option **windowsaccountname** aus.

17. Wählen Sie **Fertig stellen** aus.

18. Sie kehren jetzt zum Bildschirm **Allgemeine Einstellungen bearbeiten** zurück. Scrollen Sie zum unteren Seitenrand, und wählen Sie **OK** aus, um die Konfiguration zu speichern.

## <a name="create-zenya-test-user"></a>Erstellen eines Zenya-Testbenutzers

1. Melden Sie sich mit dem **Administratorkonto** bei Zenya an.

2. Öffnen Sie das Menü **Gehe zu**.

3. Wählen Sie **Anwendungsverwaltung** aus.

4. Wählen Sie im Bereich **Benutzer und Benutzergruppen** die Option **Benutzer** aus.

5. Wählen Sie **Hinzufügen**.

6. Geben Sie in das Textfeld **Username** (Benutzername) den Namen des Benutzers ein (`B.Simon@contoso.com`).

7. Geben Sie in das Feld **Full name** (Vollständiger Name) einen vollständigen Benutzernamen wie **B.Simon** ein.

8. Wählen Sie die Option **Kein Kennwort (einmaliges Anmelden verwenden)** aus.

9. Geben Sie im Feld **E-mail address** (E-Mail-Adresse) die E-Mail-Adresse des Benutzers ein, z. B. `B.Simon@contoso.com`.

10. Scrollen Sie zum Seitenende, und wählen Sie **Fertig stellen** aus.

> [!NOTE]
> Außerdem unterstützt Zenya die automatische Benutzerbereitstellung. Weitere Informationen zum Konfigurieren der automatischen Benutzerbereitstellung finden Sie [hier](./iprova-provisioning-tutorial.md).

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für Zenya weitergeleitet. Dort können Sie den Anmeldeflow initiieren. 

* Navigieren Sie direkt zur Anmelde-URL für Zenya, und initiieren Sie den Anmeldeflow.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie in „Meine Apps“ auf die Kachel „Zenya“ klicken, werden Sie zur Anmelde-URL für Zenya weitergeleitet. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Zenya können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-aad)
