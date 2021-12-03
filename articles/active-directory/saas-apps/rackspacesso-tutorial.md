---
title: 'Tutorial: Azure Active Directory-Integration mit Rackspace SSO | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und Rackspace SSO konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/14/2021
ms.author: jeedes
ms.openlocfilehash: 5fffea39509166bcf600537372f0f37f1534308c
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132306907"
---
# <a name="tutorial-azure-active-directory-integration-with-rackspace-sso"></a>Tutorial: Azure Active Directory-Integration mit Rackspace SSO

In diesem Tutorial erfahren Sie, wie Sie Rackspace SSO in Azure Active Directory (Azure AD) integrieren. Die Integration von Rackspace SSO in Azure AD ermöglicht Folgendes:

* Sie können in Azure AD steuern, wer Zugriff auf Rackspace SSO besitzt.
* Sie können es Ihren Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei Rackspace SSO anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Um die Azure AD-Integration mit Rackspace SSO konfigurieren zu können, benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Sollten Sie nicht über eine Azure AD-Umgebung verfügen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Rackspace SSO-Abonnement, für das einmaliges Anmelden aktiviert ist.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Rackspace SSO unterstützt **SP-initiiertes** einmaliges Anmelden.

> [!NOTE]
> Der Bezeichner dieser Anwendung ist ein fester Zeichenfolgenwert, daher kann in einem Mandanten nur eine Instanz konfiguriert werden.

## <a name="add-rackspace-sso-from-the-gallery"></a>Hinzufügen von Rackspace SSO aus dem Katalog

Zum Konfigurieren der Integration von Rackspace SSO in Azure AD müssen Sie Rackspace SSO aus dem Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Rackspace SSO** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Rackspace SSO** aus, und fügen Sie die dann App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-rackspace-sso"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Rackspace SSO

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Rackspace SSO mithilfe eines Testbenutzers namens **Britta Simon**.
Bei der Verwendung des einmaligen Anmeldens mit Rackspace werden die Rackspace-Benutzer bei der ersten Anmeldung am Rackspace-Portal automatisch erstellt. 

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD mit Rackspace SSO die folgenden Schritte aus:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden mit Azure AD mit dem Testbenutzer Britta Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.
2. **[Konfigurieren des einmaligen Anmeldens für Rackspace SSO](#configure-rackspace-sso-single-sign-on)**, um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. **[Einrichten der Attributzuordnung in der Rackspace-Systemsteuerung](#set-up-attribute-mapping-in-the-rackspace-control-panel)**, um Azure AD-Benutzern Rackspace Rollen zuzuweisen.
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Rackspace SSO** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

4. Laden Sie im Abschnitt **Grundlegende SAML-Konfiguration** die **Dienstanbieter-Metadatendatei** hoch, die Sie von der [URL](https://login.rackspace.com/federate/sp.xml) herunterladen können, und führen Sie die folgenden Schritte aus:

    a. Klicken Sie auf **Metadatendatei hochladen**.

    ![Screenshot: Abschnitt „Grundlegende SAML-Konfiguration“ mit dem Link „Metadatendatei hochladen“](common/upload-metadata.png)

    b. Klicken Sie auf das **Ordnerlogo**, wählen Sie die Metadatendatei aus, und klicken Sie auf **Hochladen**.

    ![Screenshot: Dialogfeld, in dem Sie eine Datei auswählen und hochladen können](common/browse-upload-metadata.png)

    c. Sobald die Metadaten-Datei erfolgreich hochgeladen wurde, werden die erforderlichen URLs automatisch ausgefüllt.

    d. Geben Sie im Textfeld **Anmelde-URL** die URL ein: `https://login.rackspace.com/federate/`.

5. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** auf **Herunterladen**, um den Ihren Anforderungen entsprechenden **Verbundmetadaten-XML**-Code aus den verfügbaren Optionen herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

Diese Datei wird in Rackspace hochgeladen, um die erforderlichen Konfigurationseinstellungen für den Identitätsverbund auszufüllen.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie dem Benutzer Zugriff auf Rackspace SSO gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Rackspace SSO** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-rackspace-sso-single-sign-on"></a>Konfigurieren des einmaligen Anmeldens für Rackspace SSO

Konfigurieren des einmaligen Anmeldens aufseiten von **Rackspace SSO**:

1. Beachten Sie die Dokumentation zum [Hinzufügen eines Identitätsanbieters zur Systemsteuerung](https://developer.rackspace.com/docs/rackspace-federation/gettingstarted/add-idp-cp/).
1. Sie führt Sie durch die Schritte zum:
    1. Erstellen eines neuen Identitätsanbieters
    1. Festlegen einer E-Mail-Domäne, die Benutzer bei der Anmeldung zur Identifizierung Ihres Unternehmens verwenden
    1. Hochladen der zuvor aus der Azure-Systemsteuerung heruntergeladen **Verbundmetadaten-XML**.

Dadurch werden die grundlegenden erforderlichen SSO-Einstellungen für die Verbindung von Azure und Rackspace ordnungsgemäß konfiguriert.

### <a name="set-up-attribute-mapping-in-the-rackspace-control-panel"></a>Einrichten der Attributzuordnung in der Rackspace-Systemsteuerung

Rackspace verwendet eine **Richtlinie für Attributzuordnung**, um Ihren Benutzern mit einmaliger Anmeldung Rackspace-Rollen und -Gruppen zuzuweisen. Die **Richtlinie für Attributzuordnung** übersetzt Azure AD-SAML-Ansprüche in die für Rackspace erforderlichen Benutzerkonfigurationsfelder. Weitere Informationen finden Sie in der Rackspace-Dokumentation zu den [Grundlagen der Attributzuordnung](https://developer.rackspace.com/docs/rackspace-federation/appendix/map/). Daher sollten Sie Folgendes berücksichtigen:

* Wenn Sie mithilfe von Azure AD-Gruppen unterschiedliche Rackspace-Zugriffsebenen zuweisen möchten, müssen Sie den Gruppenanspruch in den Azure-Einstellungen für einmaliges Anmelden für **Rackspace SSO** aktivieren. Die **Richtlinie für Attributzuordnung** wird dann verwendet, um diese Gruppen den gewünschten Rackspace-Rollen und -Gruppen zuzuordnen:

    ![Die Gruppenanspruch-Einstellungen](common/sso-groups-claim.png)

* Azure AD sendet standardmäßig die UID von Azure AD-Gruppen im SAML-Anspruch anstelle des Namens der Gruppe. Wenn Sie jedoch Ihre lokale Active Directory-Instanz mit Azure AD synchronisieren, haben Sie die Möglichkeit, die tatsächlichen Namen der Gruppen zu senden:

    ![Die Einstellungen für den Gruppenanspruchsnamen](common/sso-groups-claims-names.png)

Die **Beispielrichtlinie für Attributzuordnung** unten veranschaulicht Folgendes:
1. Festlegen des Rackspace-Benutzernamens auf den SAML-Anspruch `user.name`. Jeder Anspruch kann verwendet werden, am häufigsten wird er jedoch auf ein Feld mit der E-Mail-Adresse des Benutzers festgelegt.
1. Festlegen der Rackspace-Rollen `admin` und `billing:admin` für einen Benutzer durch Zuordnen einer Azure AD-Gruppe anhand des Gruppennamens oder der Gruppen-UID. Eine *Ersetzung* von `"{0}"` im Feld `roles` wird verwendet, und die Ergebnisse werden durch die Ergebnisse der `remote`-Regelausdrücke ersetzt.
1. Verwenden der  *Standardersetzung*`"{D}"`, damit Rackspace zusätzliche SAML-Felder abrufen kann, indem im SAML-Austausch nach standardmäßigen und bekannten SAML-Ansprüchen gesucht wird.

```yaml
---
mapping:
    rules:
    - local:
        user:
          domain: "{D}"
          name: "{At(http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name)}"
          email: "{D}"
          roles:
              - "{0}"
          expire: "{D}"
      remote:
          - path: |
              (
                if (mapping:get-attributes('http://schemas.microsoft.com/ws/2008/06/identity/claims/groups')='7269f9a2-aabb-9393-8e6d-282e0f945985') then ('admin', 'billing:admin') else (),
                if (mapping:get-attributes('http://schemas.microsoft.com/ws/2008/06/identity/claims/groups')='MyAzureGroup') then ('admin', 'billing:admin') else ()
              )
            multiValue: true
  version: RAX-1
```
> [!TIP]
> Achten Sie darauf, bei der Bearbeitung Ihrer Richtliniendatei einen Texteditor zu verwenden, der die YAML-Syntax überprüft.

Weitere Beispiele finden Sie in der [Rackspace-Dokumentation zu den Grundlagen der Attributzuordnung](https://developer.rackspace.com/docs/rackspace-federation/appendix/map/).

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für Rackspace SSO weitergeleitet. Dort können Sie den Anmeldeflow initiieren. 

* Navigieren Sie direkt zur Rackspace SSO-Anmelde-URL, und initiieren Sie dort den Anmeldeflow.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie unter „Meine Apps“ auf die Kachel „Rackspace SSO“ klicken, werden Sie an die Anmelde-URL von Rackspace SSO umgeleitet. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

Sie können auch die Schaltfläche **Überprüfen** in den Einstellungen für einmaliges Anmelden für **Rackspace SSO** verwenden:

   ![Schaltfläche „Überprüfen“ für SSO](common/sso-validate-sign-on.png)

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren des einmaligen Anmeldens von Rackspace SSO können Sie Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-aad)
