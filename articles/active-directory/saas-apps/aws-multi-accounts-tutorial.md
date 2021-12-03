---
title: 'Tutorial: Azure Active Directory-Integration von Amazon Web Services zum Verbinden mehrerer Konten | Microsoft-Dokumentation'
description: Es wird beschrieben, wie Sie das einmalige Anmelden zwischen Azure AD und Amazon Web Services konfigurieren (altes Tutorial).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/24/2020
ms.author: jeedes
ms.openlocfilehash: 90890843c5377f22647634fad158fb7e26df17f5
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132288350"
---
# <a name="tutorial-azure-active-directory-integration-with-amazon-web-services"></a>Tutorial: Azure Active Directory-Integration mit Amazon Web Services

In diesem Tutorial wird beschrieben, wie Sie Azure Active Directory (Azure AD) mit Amazon Web Services integrieren (altes Tutorial).

Diese Integration bietet folgende Vorteile:

- Sie können in Azure AD steuern, wer Zugriff auf AWS haben soll.
- Sie können es Benutzern ermöglichen, sich mit ihren Azure AD-Konten automatisch bei AWS anzumelden (einmaliges Anmelden, SSO).
- Sie können Ihre Konten an einem zentralen Ort, im Azure-Portal, verwalten.

![Diagramm: Azure AD-Integration mit AWS](./media/aws-multi-accounts-tutorial/amazonwebservice.png)

> [!NOTE]
> Wir empfehlen Ihnen, _nicht_ für nur eine AWS-App eine Verbindung mit Ihren gesamten AWS-Konten herzustellen. Stattdessen empfehlen wir Ihnen, die [Azure AD-SSO-Integration mit AWS](./amazon-web-service-tutorial.md) zu verwenden, um für mehrere Instanzen Ihres AWS-Kontos eine Verbindung mit mehreren Instanzen von AWS-Apps in Azure AD zu konfigurieren.

Es hat die folgenden Gründe, warum wir Ihnen empfehlen, _nicht_ für nur eine AWS-App eine Verbindung mit Ihren gesamten AWS-Konten herzustellen:

- Nutzen Sie diesen Ansatz nur, wenn Sie über eine kleinere Anzahl von AWS-Konten und -Rollen verfügen. Dieses Modell ist nicht skalierbar, wenn sich die Anzahl von AWS-Konten und der zugehörigen Rollen erhöht. Bei diesem Ansatz wird nicht die AWS-Rollenimportfunktion mit Azure AD-Benutzerbereitstellung verwendet, sodass Sie die Rollen manuell hinzufügen, aktualisieren oder löschen müssen.

- Sie müssen den Microsoft Graph-Tester-Ansatz verwenden, um der App alle Rollen zuzuweisen. Wir empfehlen nicht, den Ansatz mittels Manifestdatei zu verwenden.

- Kunden haben uns berichtet, dass das Hinzufügen von ca. 1.200 App-Rollen für eine einzelne AWS-App für alle weiteren Vorgänge in der App dazu geführt hat, dass Fehler in Bezug auf die Größe ausgelöst wurden. Für das Anwendungsobjekt gilt eine feste Größenbegrenzung.

- Sie müssen die Rollen manuell aktualisieren, wenn diese in einem der Konten hinzugefügt werden. Leider handelt es sich hierbei um einen Ansatz, bei dem nicht _angefügt_, sondern _ersetzt_ wird. Wenn die Anzahl Ihrer Konten zunimmt, ergibt sich hierbei in Bezug auf Konten und Rollen außerdem eine _n_:_n_-Beziehung.

- Für alle AWS-Konten wird die gleiche Verbundmetadaten-XML-Datei verwendet. Beim Zertifikatrollover kann das gleichzeitige Aktualisieren des Zertifikats in allen AWS-Konten mit sehr hohem Aufwand verbunden sein.

## <a name="prerequisites"></a>Voraussetzungen

Zum Konfigurieren der Azure AD-Integration von AWS benötigen Sie Folgendes:

- Ein Azure AD-Abonnement. Falls Sie nicht über ein Azure AD-Abonnement verfügen, können Sie eine [einmonatige Testversion](https://azure.microsoft.com/pricing/free-trial/) erhalten.
- Ein AWS-SSO-fähiges Abonnement.

> [!NOTE]
> Wir empfehlen Ihnen, die Schritte dieses Tutorials nur dann als Test in einer Produktionsumgebung auszuführen, wenn dies nicht anders möglich ist.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

AWS unterstützt SP- und IDP-initiiertes einmaliges Anmelden.

## <a name="add-aws-from-the-gallery"></a>Hinzufügen von AWS über den Katalog

Zum Konfigurieren der Integration von AWS in Azure AD fügen Sie AWS aus dem Katalog Ihrer Liste mit den verwalteten SaaS-Apps (Software-as-a-Service) hinzu.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Bereich den Azure AD-Dienst aus, den Sie verwenden möchten.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** den Suchbegriff **Amazon Web Services** in das Suchfeld ein.
1. Wählen Sie in der Ergebnisliste den Eintrag **Amazon Web Services** aus, und fügen Sie dann die App hinzu. Innerhalb von einigen Sekunden wird die App Ihrem Mandanten hinzugefügt.

1. Navigieren Sie zum Bereich **Eigenschaften**, und kopieren Sie den Wert, der im Feld **Objekt-ID** angezeigt wird.

   ![Screenshot: Feld „Objekt-ID“ im Bereich „Eigenschaften“](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-properties.png)

## <a name="configure-and-test-azure-ad-sso"></a>Konfigurieren und Testen des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

In diesem Abschnitt konfigurieren und testen Sie das einmalige Anmelden von Azure AD bei AWS mithilfe eines Testbenutzers namens „Britta Simon“.

Damit einmaliges Anmelden funktioniert, muss Azure AD wissen, welcher Benutzer in AWS als Entsprechung für den Azure AD-Benutzer fungiert. Anders ausgedrückt: Zwischen dem Azure AD-Benutzer und dem entsprechenden Benutzer in AWS muss eine Linkbeziehung eingerichtet werden.

Weisen Sie den Wert für **Benutzername** aus Azure AD in AWS als Wert für **Benutzername** zu, um eine Linkbeziehung herzustellen.

Gehen Sie wie folgt vor, um einmaliges Anmelden von Azure AD mit AWS zu konfigurieren und zu testen:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen
1. **[Konfigurieren des einmaligen Anmeldens für AWS](#configure-aws-sso)** , um die SSO-Einstellungen aufseiten der Anwendung zu konfigurieren.
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert.

### <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

In diesem Abschnitt aktivieren Sie wie folgt das einmalige Anmelden von Azure AD im Azure-Portal und führen die entsprechende Konfiguration in Ihrer AWS-Anwendung durch:

1. Wählen Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Amazon Web Services (AWS)** im linken Bereich die Option **Einmaliges Anmelden** aus.

   ![Screenshot: Option „Einmaliges Anmelden“](common/select-sso.png)

1. Wählen Sie im Bereich **SSO-Methode auswählen** den Modus **SAML/WS-Fed** aus, um einmaliges Anmelden zu aktivieren.

   ![Screenshot: Bereich „SSO-Methode auswählen“](common/select-saml-option.png)

1. Wählen Sie im Bereich **Einmaliges Anmelden (Single Sign-On, SSO) mit SAML einrichten** die Schaltfläche **Bearbeiten** (Stiftsymbol) aus.

   ![Screenshot: Schaltfläche „Bearbeiten“ im Bereich „Einmaliges Anmelden (SSO) mit SAML einrichten“](common/edit-urls.png)

1. Der Bereich **Grundlegende SAML-Konfiguration** wird geöffnet. Sie können diesen Abschnitt überspringen, da die App bereits in Azure integriert ist. Wählen Sie **Speichern** aus.

   Die AWS-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Sie können die Werte dieser Attribute im Abschnitt **Benutzerattribute und Ansprüche** auf der Seite **Anwendungsintegration** verwalten.

1. Wählen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** die Schaltfläche **Bearbeiten** aus.

   ![Screenshot: Schaltfläche „Bearbeiten“ im Bereich „Benutzerattribute“](common/edit-attribute.png)

1. Konfigurieren Sie im Bereich **Benutzerattribute** im Abschnitt **Benutzeransprüche** das SAML-Tokenattribut, indem Sie die Werte aus der folgenden Tabelle verwenden:

   | Name            | Quellattribut                                                            | Namespace                                |
   | --------------- | --------------------------------------------------------------------------- | ---------------------------------------- |
   | RoleSessionName | user.userprincipalname                                                      | `https://aws.amazon.com/SAML/Attributes` |
   | Role            | user.assignedroles                                                          | `https://aws.amazon.com/SAML/Attributes` |
   | SessionDuration | „Geben Sie einen Wert zwischen 900 Sekunden (15 Minuten) und 43.200 Sekunden (12 Stunden) an“ | `https://aws.amazon.com/SAML/Attributes` |

   a. Wählen Sie die Option **Neuen Anspruch hinzufügen** aus, und gehen Sie im Bereich **Benutzeransprüche verwalten** dann wie folgt vor:

   ![Screenshot: Schaltflächen „Neuen Anspruch hinzufügen“ und „Speichern“ im Bereich „Benutzeransprüche“](common/new-save-attribute.png)

   ![Screenshot: Bereich „Benutzeransprüche verwalten“](common/new-attribute-details.png)

   b. Geben Sie im Feld **Name** den Attributnamen ein.

   c. Geben Sie im Feld **Namespace** den Namespacewert ein.

   d. Wählen Sie für **Quelle** die Option **Attribut** aus.

   e. Wählen Sie in der Dropdownliste **Quellattribut** das Attribut aus.

   f. Wählen Sie **OK** und anschließend **Speichern** aus.

   > [!NOTE]
   > Weitere Informationen zu Rollen in Azure AD finden Sie unter [Vorgehensweise: Hinzufügen von App-Rollen zu Ihrer Anwendung und Empfangen der Rollen im Token](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui).

1. Wählen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** die Option **Herunterladen** aus, um die Verbundmetadaten-XML-Datei herunterzuladen, und speichern Sie sie anschließend auf Ihrem Computer.

   ![Screenshot: Downloadlink für „Verbundmetadaten-XML-Datei“ im Bereich „SAML-Signaturzertifikat“](common/metadataxml.png)

### <a name="configure-aws-sso"></a>Konfigurieren von AWS-SSO

1. Melden Sie sich in einem neuen Browserfenster als Administrator bei Ihrer AWS-Unternehmenswebsite an.

1. Wählen Sie das Symbol **AWS Home** (AWS-Startseite) aus.

   ![Screenshot: Symbol „AWS Home“ (AWS-Startseite)][11]

1. Wählen Sie im Bereich **AWS Services** unter **Security, Identity & Compliance** (Sicherheit, Identität und Compliance) die Option **IAM (Identity & Access Management)** aus.

   ![Screenshot: Link „Identity & Access Management“ im Bereich „AWS Services“][12]

1. Wählen Sie im linken Bereich die Option **Identity Providers** (Identitätsanbieter) und dann **Create Provider** (Anbieter erstellen) aus.

   ![Screenshot: Schaltfläche „Create Provider“ (Anbieter erstellen)][13]

1. Gehen Sie im Bereich **Configure Provider** (Anbieter konfigurieren) wie folgt vor:

   ![Screenshot: Bereich „Configure Provider“ (Anbieter konfigurieren)][14]

   a. Wählen Sie in der Dropdownliste **Provider Type** (Anbietertyp) den Eintrag **SAML** aus.

   b. Geben Sie im Feld **Provider Name** (Anbietername) einen Anbieternamen ein (z. B. _WAAD_).

   c. Wählen Sie neben dem Feld **Metadata Document** (Metadatendokument) die Option **Choose File** (Datei auswählen) aus, um Ihre heruntergeladene Verbundmetadaten-XML-Datei in das Azure-Portal hochzuladen.

   d. Wählen Sie **Nächster Schritt**.

1. Wählen Sie im Bereich **Verify Provider Information** (Anbieterinformationen überprüfen) die Option **Create** (Erstellen) aus.

   ![Screenshot: Bereich „Verify Provider Information“ (Anbieterinformationen überprüfen)][15]

1. Wählen Sie im linken Bereich die Option **Roles** (Rollen) und dann **Create role** (Rolle erstellen) aus.

   ![Screenshot: Schaltfläche „Create role“ (Rolle erstellen) im Bereich „Roles“ (Rollen)][16]

   > [!NOTE]
   > Zusammen dürfen die Rolle „Amazon Resource Name“ (Amazon-Ressourcenname) (ARN) und der SAML-Anbieter-ARN für eine importierte Rolle maximal 240 Zeichen lang sein.

1. Gehen Sie auf der Seite **Create role** (Rolle erstellen) wie folgt vor:

   ![Screenshot: Schaltfläche „SAML 2.0 federation“ (SAML 2.0-Verbund) für die vertrauenswürdige Entität auf der Seite „Create role“ (Rolle erstellen)][19]

   a. Wählen Sie unter **Select type of trusted entity** (Art der vertrauenswürdigen Entität auswählen) die Option **SAML 2.0 federation** (SAML 2.0-Verbund) aus.

   b. Wählen Sie unter **Choose a SAML 2.0 Provider** (SAML 2.0-Anbieter auswählen) den SAML-Anbieter aus, den Sie zuvor erstellt haben (z. B. _WAAD_).

   c. Wählen Sie **Programmgesteuerten und AWS Management Console-Zugriff zulassen** aus.

   d. Klicken Sie auf **Weiter: Permissions** (Weiter: Berechtigungen).

1. Geben Sie im Suchfeld **Administrator Access** ein, aktivieren Sie das Kontrollkästchen **AdministratorAccess**, und wählen Sie anschließend die Option **Next: Tags** (Weiter: Tags) aus.

   ![Screenshot: Liste „Policy name“ (Richtlinienname) mit Auswahl der Richtlinie „AdministratorAccess“](./media/aws-multi-accounts-tutorial/administrator-access.png)

1. Gehen Sie im Bereich **Add tags (optional)** (Tags hinzufügen (optional)) wie folgt vor:

   ![Screenshot: Bereich „Add tags (optional)“ (Tags hinzufügen (optional))](./media/aws-multi-accounts-tutorial/config2.png)

   a. Geben Sie im Feld **Key** (Schlüssel) den Schlüsselnamen ein (z. B. _Azureadtest_).

   b. Geben Sie im Feld **Value (optional)** (Wert (optional)) den Schlüsselwert im folgenden Format ein: `<accountname-aws-admin>`. Hierbei sollte der Kontoname nur Kleinbuchstaben enthalten.

   c. Klicken Sie auf **Weiter: Review** (Weiter: Überprüfen).

1. Gehen Sie im Bereich **Review** (Überprüfen) wie folgt vor:

   ![Screenshot: Bereich „Review“ (Überprüfen) mit Hervorhebung der Felder „Role name“ (Rollenname) und „Role description“ (Rollenbeschreibung)][34]

   a. Geben Sie im Feld **Role name** (Rollenname) den Wert im folgenden Format ein: `<accountname-aws-admin>`.

   b. Geben Sie im Feld **Role description** (Rollenbeschreibung) den Wert ein, den Sie für den Rollennamen verwendet haben.

   c. Wählen Sie **Create role** (Rolle erstellen) aus.

   d. Erstellen Sie so viele Rollen, wie Sie benötigen, und ordnen Sie sie dem Identitätsanbieter zu.

   > [!NOTE]
   > Sie können auch andere Rollen erstellen, z. B. _accountname-finance-admin_, _accountname-read-only-user_, _accountname-devops-user_ oder _accountname-tpm-user_, an die jeweils eine andere Richtlinie angefügt ist. Sie können diese Rollenrichtlinien später gemäß den Anforderungen für die einzelnen AWS-Konten ändern. Hierbei ist es ratsam, für die Rollen über die AWS-Konten hinweg jeweils die gleichen Richtlinien beizubehalten.

1. Notieren Sie sich die Konto-ID für das AWS-Konto entweder im Eigenschaftenbereich von Amazon Elastic Compute Cloud (Amazon EC2) oder im IAM-Dashboard. Dies ist im folgenden Screenshot dargestellt:

   ![Screenshot: Anzeige der Konto-ID im Bereich „Identity & Access Management“](./media/aws-multi-accounts-tutorial/aws-accountid.png)

1. Melden Sie sich beim Azure-Portal an, und navigieren Sie dann zu **Gruppen**.

1. Erstellen Sie neue Gruppen mit dem gleichen Namen wie für die oben erstellten IAM-Rollen, und notieren Sie sich anschließend für diese neuen Gruppen jeweils den Wert im Feld **Objekt-ID**.

   ![Screenshot: Kontodetails für eine neue Gruppe](./media/aws-multi-accounts-tutorial/copy-objectids.png)

1. Melden Sie sich vom aktuellen AWS-Konto ab und dann bei einem anderen Konto an, für das Sie SSO mit Azure AD konfigurieren möchten.

1. Nachdem Sie alle Rollen in den Konten erstellt haben, werden diese jeweils in der Liste **Roles** (Rollen) der Konten angezeigt.

   ![Screenshot: Liste „Roles“ (Rollen) mit Name, Beschreibung und vertrauenswürdigen Entitäten der Rollen](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-listofroles.png)

Als Nächstes müssen Sie alle Rollen-ARNs und vertrauenswürdigen Entitäten für alle Rollen aller Konten erfassen. Hierfür ist es erforderlich, dass Sie diese manuell mit der Azure AD-Anwendung zuordnen. Gehen Sie folgendermaßen vor:

1. Wählen Sie die einzelnen Rollen aus, um die zugehörigen Werte für den Rollen-ARN und die vertrauenswürdige Entität zu kopieren. Sie benötigen diese Werte für alle Rollen, die Sie in Azure AD erstellen.

   ![Screenshot: Bereich „Summary“ (Zusammenfassung) für die Rollen-ARNs und vertrauenswürdigen Entitäten](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-role-summary.png)

1. Wiederholen Sie den vorherigen Schritt für alle Rollen aller Konten, und speichern Sie die Angaben im folgenden Format in einer Textdatei: `<Role ARN>,<Trusted entities>`.

1. Öffnen Sie den [Microsoft Graph-Tester](https://developer.microsoft.com/graph/graph-explorer), und führen Sie Folgendes durch:

   a. Melden Sie sich bei der Microsoft Graph-Tester-Website mit den Anmeldeinformationen des globalen Administrators oder Co-Admins für Ihren Mandanten an.

   b. Sie müssen über ausreichende Berechtigungen zum Erstellen der Rollen verfügen. Wählen Sie die Option **Berechtigungen ändern** aus.

   ![Screenshot: Link „Berechtigungen ändern“ im Bereich „Authentifizierung“ des Microsoft Graph-Testers](./media/aws-multi-accounts-tutorial/graph-explorer-new9.png)

   c. Gehen Sie in der Liste mit den Berechtigungen wie folgt vor, falls Sie noch nicht über die Berechtigungen verfügen, die im folgenden Screenshot aufgeführt sind: Wählen Sie jeweils eine Berechtigung und dann die Option **Berechtigungen ändern** aus.

   ![Screenshot: Liste mit Berechtigungen im Microsoft Graph-Tester mit Hervorhebung der entsprechenden Berechtigungen](./media/aws-multi-accounts-tutorial/graph-explorer-new10.png)

   d. Melden Sie sich erneut am Graph-Tester an, und akzeptieren Sie die Nutzungsbedingungen der Website.

   e. Wählen Sie oben im Bereich die Option **GET** als Methode und dann **beta** als Version aus. Geben Sie im Abfragefeld anschließend eine der folgenden URLs ein:

   - Verwenden Sie `https://graph.microsoft.com/beta/servicePrincipals`, um alle Dienstprinzipale von Ihrem Mandanten abzurufen.
   - Verwenden Sie `https://graph.microsoft.com/beta/contoso.com/servicePrincipals` (mit Ihrer primären Domäne), falls Sie mehrere Verzeichnisse nutzen.

   ![Screenshot: Bereich „Anforderungstext“ im Microsoft Graph-Tester für Abfrage](./media/aws-multi-accounts-tutorial/graph-explorer-new1.png)

   f. Entnehmen Sie der Liste mit den Dienstprinzipalen den Namen, den Sie ändern möchten.

   Sie haben auch die Möglichkeit, die Anwendung mit STRG+F nach allen aufgelisteten Dienstprinzipalen zu durchsuchen. Fügen Sie zum Abrufen eines bestimmten Dienstprinzipals die Objekt-ID des Dienstprinzipals für die Abfrage ein, die Sie oben aus dem Eigenschaftenbereich von Azure AD kopiert haben. Dies ist hier dargestellt:

   `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.

   ![Screenshot: Dienstprinzipalabfrage mit Objekt-ID](./media/aws-multi-accounts-tutorial/graph-explorer-new2.png)

   g. Extrahieren Sie die appRoles-Eigenschaft aus dem Dienstprinzipalobjekt.

   ![Screenshot: Code zum Extrahieren der appRoles-Eigenschaft aus dem Dienstprinzipalobjekt](./media/aws-multi-accounts-tutorial/graph-explorer-new3.png)

   h. Jetzt müssen Sie neue Rollen für Ihre Anwendung generieren.

   i. Der folgende JSON-Code ist ein Beispiel für ein appRoles-Objekt. Erstellen Sie ein ähnliches Objekt, um die gewünschten Rollen für Ihre Anwendung hinzuzufügen.

   ```
   {
   "appRoles": [
       {
           "allowedMemberTypes": [
               "User"
           ],
           "description": "msiam_access",
           "displayName": "msiam_access",
           "id": "7dfd756e-8c27-4472-b2b7-38c17fc5de5e",
           "isEnabled": true,
           "origin": "Application",
           "value": null
       },
       {
           "allowedMemberTypes": [
               "User"
           ],
           "description": "Admin,WAAD",
           "displayName": "Admin,WAAD",
           "id": "4aacf5a4-f38b-4861-b909-bae023e88dde",
           "isEnabled": true,
           "origin": "ServicePrincipal",
           "value": "arn:aws:iam::12345:role/Admin,arn:aws:iam::12345:saml-provider/WAAD"
       },
       {
           "allowedMemberTypes": [
               "User"
           ],
           "description": "Auditors,WAAD",
           "displayName": "Auditors,WAAD",
           "id": "bcad6926-67ec-445a-80f8-578032504c09",
           "isEnabled": true,
           "origin": "ServicePrincipal",
           "value": "arn:aws:iam::12345:role/Auditors,arn:aws:iam::12345:saml-provider/WAAD"
       }    ]
   }
   ```

   > [!Note]
   > Sie können erst dann neue Rollen hinzufügen, nachdem Sie _msiam_access_ für den Patchvorgang hinzugefügt haben. Sie können beliebig viele Rollen hinzufügen. Dies hängt von den Anforderungen Ihrer Organisation ab. Azure AD sendet den Wert (_value_) dieser Rollen als Anspruchswert in der SAML-Antwort.

   j. Ändern Sie im Microsoft Graph-Tester die Methode von **GET** in **PATCH**. Patchen Sie das Dienstprinzipalobjekt mit den gewünschten Rollen, indem Sie die appRoles-Eigenschaft (siehe vorheriges Beispiel) aktualisieren. Wählen Sie die Option **Abfrage ausführen**, um den Patchvorgang auszuführen. In einer Erfolgsmeldung wird die Erstellung der Rolle für Ihre AWS-Anwendung bestätigt.

   ![Screenshot: Bereich im Microsoft Graph-Tester mit Auswahl von „PATCH“ als Methode](./media/aws-multi-accounts-tutorial/graph-explorer-new11.png)

1. Nachdem der Dienstprinzipal mit weiteren Rollen gepatcht wurde, können Sie den jeweiligen Rollen Benutzer und Gruppen zuweisen. Hierfür navigieren Sie im Azure-Portal zur AWS-Anwendung und wählen dann oben die Registerkarte **Benutzer und Gruppen** aus.

1. Wir empfehlen Ihnen, eine neue Gruppe für jede AWS-Rolle zu erstellen, damit Sie die jeweilige Rolle in der Gruppe zuweisen können. Diese 1:1-Zuordnung bedeutet, dass eine Gruppe einer Rolle zugeordnet ist. Anschließend können Sie die Mitglieder hinzufügen, die dieser Gruppe angehören.

1. Nachdem Sie die Gruppen erstellt haben, wählen Sie eine Gruppe aus und weisen sie der Anwendung zu.

   ![Screenshot: Bereich „Benutzer und Gruppen“](./media/aws-multi-accounts-tutorial/graph-explorer-new5.png)

   > [!Note]
   > Geschachtelte Gruppen werden nicht unterstützt, wenn Sie Gruppen zuweisen.

1. Wählen Sie die Rolle und dann die Option **Zuweisen** aus, um die Rolle der Gruppe zuzuweisen.

   ![Screenshot: Bereich „Zuweisung hinzufügen“](./media/aws-multi-accounts-tutorial/graph-explorer-new6.png)

   > [!Note]
   > Nachdem Sie die Rollen zugewiesen haben, können Sie sie anzeigen, indem Sie Ihre Sitzung im Azure-Portal aktualisieren.

### <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit „Meine Apps“ von Microsoft.

Wenn Sie in „Meine Apps“ die Kachel **AWS** auswählen, wird die AWS-Anwendungsseite mit einer Option zum Auswählen der Rolle geöffnet.

![Screenshot: AWS-Seite zum Testen des einmaligen Anmeldens](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-test-screen.png)

Sie können auch die SAML-Antwort überprüfen, um die als Ansprüche übergebenen Rollen anzuzeigen.

![Screenshot: SAML-Antwort](./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-test-saml.png)

Weitere Informationen zu „Meine Apps“ finden Sie unter [Anmelden beim Portal „Meine Apps“ und Starten von Apps über dieses](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von AWS können Sie die Sitzungssteuerung erzwingen, die in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. Weitere Informationen finden Sie im Artikel zum [Erzwingen der Sitzungssteuerung mit Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).

<!--Image references-->

[11]: ./media/aws-multi-accounts-tutorial/ic795031.png
[12]: ./media/aws-multi-accounts-tutorial/ic795032.png
[13]: ./media/aws-multi-accounts-tutorial/ic795033.png
[14]: ./media/aws-multi-accounts-tutorial/ic795034.png
[15]: ./media/aws-multi-accounts-tutorial/ic795035.png
[16]: ./media/aws-multi-accounts-tutorial/ic795022.png
[17]: ./media/aws-multi-accounts-tutorial/ic795023.png
[18]: ./media/aws-multi-accounts-tutorial/ic795024.png
[19]: ./media/aws-multi-accounts-tutorial/ic795025.png
[32]: ./media/aws-multi-accounts-tutorial/ic7950251.png
[33]: ./media/aws-multi-accounts-tutorial/ic7950252.png
[35]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-provisioning.png
[34]: ./media/aws-multi-accounts-tutorial/config3.png
[36]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-securitycredentials.png
[37]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-securitycredentials-continue.png
[38]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-createnewaccesskey.png
[39]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-provisioning-automatic.png
[40]: ./media/aws-multi-accounts-tutorial/tutorial-amazonwebservices-provisioning-testconnection.png
[41]: ./media/aws-multi-accounts-tutorial/
