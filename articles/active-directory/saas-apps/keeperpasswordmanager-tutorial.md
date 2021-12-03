---
title: 'Tutorial: Azure AD-SSO-Integration mit Keeper Password Manager'
description: Erfahren Sie, wie Sie einmaliges Anmelden zwischen Azure Active Directory und Keeper Password Manager konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/24/2021
ms.author: jeedes
ms.openlocfilehash: 26f56e452b2065fd61180d9e6cc3f757d8392c16
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132294844"
---
# <a name="tutorial-azure-ad-sso-integration-with-keeper-password-manager"></a>Tutorial: Azure AD-SSO-Integration mit Keeper Password Manager

In diesem Tutorial erfahren Sie, wie Sie Keeper Password Manager in Azure Active Directory (Azure AD) integrieren. Die Integration von Keeper Password Manager in Azure AD ermöglicht Ihnen Folgendes:

* Steuern in Azure AD, wer Zugriff auf Keeper Password Manager besitzt.
* Benutzer können sich mit ihren Azure AD-Konten automatisch bei Keeper Password Manager anmelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Keeper Password Manager-Abonnement, für das einmaliges Anmelden (Single Sign-On, SSO) aktiviert ist.

> [!NOTE]
> Diese Integration kann auch über die Azure AD-Umgebung für die US Government-Cloud verwendet werden. Sie finden diese Anwendung im Azure AD-Katalog für US Government-Cloudanwendungen und konfigurieren sie auf die gleiche Weise wie in der öffentlichen Cloud.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Keeper Password Manager unterstützt SP-initiiertes einmaliges Anmelden.
* Keeper Password Manager unterstützt die [**automatisierte** Benutzerbereitstellung und Bereitstellungsaufhebung](keeper-password-manager-digitalvault-provisioning-tutorial.md) (empfohlen).
* Keeper Password Manager unterstützt Just-In-Time-Benutzerbereitstellung.

## <a name="add-keeper-password-manager-from-the-gallery"></a>Hinzufügen von Keeper Password Manager aus dem Katalog

Zum Konfigurieren der Integration von Keeper Password Manager in Azure AD müssen Sie Keeper Password Manager über den Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Bereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie unter **Aus Katalog hinzufügen** den Suchbegriff **Keeper Password Manager** in das Suchfeld ein.
1. Wählen Sie im Ergebnisbereich **Keeper Password Manager** aus, und fügen Sie die App dann hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-keeper-password-manager"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Keeper Password Manager

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD mit Keeper Password Manager mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Keeper Password Manager eine Linkbeziehung eingerichtet werden.

Führen Sie das Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Keeper Password Manager wie folgt durch:

1. [Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso), um Ihren Benutzern die Verwendung dieses Features zu ermöglichen

    1. [Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user), um das einmalige Anmelden mit Azure AD mit der Testbenutzerin Britta Simon zu testen.
    1. [Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user), um Britta Simon für das einmalige Anmelden von Azure AD zu aktivieren.

1. [Konfigurieren des einmaligen Anmeldens für Keeper Password Manager](#configure-keeper-password-manager-sso), um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren.
    1. [Erstellen eines Keeper Password Manager-Testbenutzers](#create-a-keeper-password-manager-test-user), um eine Entsprechung von Britta Simon in Keeper Password Manager zu erhalten, die mit ihrer Darstellung in Azure AD verknüpft ist.
1. [Testen des einmaligen Anmeldens](#test-sso), um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Keeper Password Manager** zum Abschnitt **Verwalten**. Wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Wählen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** das Stiftsymbol für **Grundlegende SAML-Konfiguration** aus, um die Einstellungen zu bearbeiten.

   ![Screenshot: „Einmaliges Anmelden (SSO) mit SAML einrichten“ mit hervorgehobenem Stiftsymbol](common/edit-urls.png)

4. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    a. Geben Sie als **Bezeichner (Entitäts-ID)** eine URL in einem der folgenden Formate ein:
    * Für Cloud-SSO: `https://keepersecurity.com/api/rest/sso/saml/<CLOUD_INSTANCE_ID>`
    * Für SSO in der lokalen Umgebung: `https://<KEEPER_FQDN>/sso-connect`

    b. Geben Sie als **Antwort-URL** eine URL in einem der folgenden Formate ein:
    * Für Cloud-SSO: `https://keepersecurity.com/api/rest/sso/saml/sso/<CLOUD_INSTANCE_ID>`
    * Für SSO in der lokalen Umgebung: `https://<KEEPER_FQDN>/sso-connect/saml/sso`

    c. Geben Sie als **Anmelde-URL** eine URL in einem der folgenden Formate ein:
    * Für Cloud-SSO: `https://keepersecurity.com/api/rest/sso/saml/sso/<CLOUD_INSTANCE_ID>`
    * Für SSO in der lokalen Umgebung: `https://<KEEPER_FQDN>/sso-connect/saml/login`

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Sie müssen diese Werte mit dem tatsächlichen Bezeichner, der Antwort-URL und Anmelde-URL aktualisieren. Wenden Sie sich an das [Supportteam für Keeper Password Manager](https://keepersecurity.com/contact.html), um diese Werte zu erhalten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

1. Die Keeper Password Manager-Anwendung erwartet die SAML-Assertionen in einem bestimmten Format. Daher müssen Sie Ihrer Konfiguration der SAML-Tokenattribute benutzerdefinierte Attributzuordnungen hinzufügen. Der folgende Screenshot zeigt die Liste der Standardattribute.

    ![Screenshot: „Benutzerattribute und Ansprüche“](common/default-attributes.png)

1. Darüber hinaus wird von der Keeper Password Manager-Anwendung erwartet, dass in der SAML-Antwort noch einige weitere Attribute zurückgegeben werden. Diese sind in der folgenden Tabelle angegeben. Diese Attribute werden ebenfalls vorab aufgefüllt, Sie können sie jedoch nach Bedarf überarbeiten.

    | Name | Quellattribut|
    | ------------| --------- |
    | First | user.givenname |
    | Letzter | user.surname |
    | Email | user.mail |

5. Navigieren Sie unter **Einmaliges Anmelden (SSO) mit SAML einrichten** zum Abschnitt **SAML-Signaturzertifikat**, und wählen Sie die Option **Herunterladen** aus. Die **Verbundmetadaten-XML-Datei** wird mit den entsprechenden Optionen für Ihre Anforderungen heruntergeladen und auf Ihrem Computer gespeichert.

    ![Screenshot: „SAML-Signaturzertifikat“ mit hervorgehobener Option „Herunterladen“.](common/metadataxml.png)

6. Kopieren Sie unter **Keeper Password Manager einrichten** die entsprechenden URLs gemäß Ihren Anforderungen.

    ![Screenshot: „Keeper Password Manager einrichten“ mit hervorgehobenen URLs.](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Erstellen eines Azure AD-Testbenutzers 

In diesem Abschnitt erstellen Sie im Azure-Portal einen Testbenutzer mit dem Namen `B.Simon`.

1. Wählen Sie im Azure-Portal im linken Bereich **Azure Active Directory** > **Benutzer** > **Alle Benutzer** aus.
1. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.
1. Führen Sie unter den Eigenschaften für **Benutzer** die folgenden Schritte aus:
   1. Geben Sie unter **Name**`B.Simon` ein.  
   1. Geben Sie unter **Benutzername** einen Benutzernamen im Format `username@companydomain.extension` ein. Beispiel: `B.Simon@contoso.com`.
   1. Wählen Sie **Kennwort anzeigen** aus, und notieren Sie sich den angezeigten Wert.
   1. Klicken Sie auf **Erstellen**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Keeper Password Manager gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Liste der Anwendungen **Keeper Password Manager** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Klicken Sie auf **Benutzer hinzufügen**. Wählen Sie unter **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie unter **Benutzer und Gruppen** in der Liste mit den Benutzern die Option **B. Simon** aus. Wählen Sie anschließend am unteren Bildschirmrand **Auswählen** aus.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie in der Liste **Rolle auswählen** auswählen. Wird für diese App keine Rolle eingerichtet, wird die Rolle **Standardzugriff** ausgewählt.
1. Wählen Sie unter **Zuweisung hinzufügen** die Option **Zuweisen** aus.

## <a name="configure-keeper-password-manager-sso"></a>Konfigurieren des einmaligen Anmeldens für Keeper Password Manager

Informationen zum Konfigurieren des einmaligen Anmeldens für die App finden Sie in den Richtlinien im [Keeper-Supporthandbuch](https://docs.keeper.io/sso-connect-guide/).

### <a name="create-a-keeper-password-manager-test-user"></a>Erstellen eines Keeper Password Manager-Testbenutzers

Damit sich Azure AD-Benutzer bei Keeper Password Manager anmelden können, müssen Sie sie bereitstellen. Die Anwendung unterstützt die Just-In-Time-Benutzerbereitstellung. Nach der Authentifizierung werden Benutzer in der Anwendung automatisch erstellt. Sie können sich an den [Support von Keeper](https://keepersecurity.com/contact.html) wenden, wenn Sie Benutzer manuell einrichten möchten.

> [!NOTE]
> Außerdem unterstützt Keeper Password Manager die automatische Benutzerbereitstellung. Weitere Informationen zum Konfigurieren der automatischen Benutzerbereitstellung finden Sie [hier](./keeper-password-manager-digitalvault-provisioning-tutorial.md).

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für Keeper Password Manager weitergeleitet. Dort können Sie den Anmeldeflow initiieren. 

* Navigieren Sie direkt zur Keeper Password Manager-Anmelde-URL, und initiieren Sie dort den Anmeldeflow.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie unter „Meine Apps“ auf die Kachel „Keeper Password Manager“ klicken, werden Sie zur Anmelde-URL für Keeper Password Manager weitergeleitet. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie Keeper Password Manager konfiguriert haben, können Sie Sitzungssteuerung erzwingen. Diese schützt in Echtzeit vor der Exfiltration und Infiltration vertraulicher Unternehmensdaten. Die Sitzungssteuerung basiert auf bedingtem Zugriff. Weitere Informationen finden Sie im Artikel zum [Erzwingen der Sitzungssteuerung mit Microsoft Defender for Cloud Apps](/cloud-app-security/proxy-deployment-aad).
