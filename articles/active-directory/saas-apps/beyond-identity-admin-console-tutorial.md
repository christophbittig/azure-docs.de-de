---
title: 'Tutorial: Azure Active Directory Einmaliges Anmelden (SSO) Integration mit Beyond Identity Admin Console | Microsoft-Dokumentation'
description: Lernen Sie, wie man Einmaliges Anmelden zwischen Azure Active Directory und Beyond Identity Admin Console konfiguriert.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/03/2021
ms.author: jeedes
ms.openlocfilehash: 9c2274b95b284c72b09de533d85d96be6dc0f75c
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132282979"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-beyond-identity-admin-console"></a>Tutorial: Azure Active Directory Einmaliges Anmelden (SSO) Integration mit Beyond Identity Admin Console

In diesem Tutorial lernen Sie, wie Sie die Beyond Identity Admin Console mit Azure Active Directory (Azure AD) integrieren können. Wenn Sie Beyond Identity Admin Console mit Azure AD integrieren, können Sie:

* In Azure AD kontrollieren, wer Zugriff auf Beyond Identity Admin Console hat.
* Aktivieren Sie die automatische Anmeldung Ihrer Nutzer bei Beyond Identity Admin Console mit ihren Azure AD-Konten.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein Abonnement der Beyond Identity Admin Console mit Einmaligem Anmelden (SSO).

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Beyond Identity Admin Console unterstützt **SP** veranlasste SSO.

## <a name="add-beyond-identity-admin-console-from-the-gallery"></a>Hinzufügen von Beyond Identity Admin Console aus dem Katalog

Um die Integration von Beyond Identity Admin Console in Azure AD zu konfigurieren, müssen Sie Beyond Identity Admin Console aus dem Katalog zu Ihrer Liste der verwalteten SaaS-Anwendungen hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Navigationsbereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie dann **Alle Anwendungen** aus.
1. Wählen Sie zum Hinzufügen einer neuen Anwendung **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus dem Katalog hinzufügen** **Beyond Identity Admin Console** in das Suchfeld ein.
1. Wählen Sie im Ergebnisfeld **Beyond Identity Admin Console** und fügen Sie die Anwendung hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-beyond-identity-admin-console"></a>Azure AD SSO für Beyond Identity Admin Console konfigurieren und testen

Konfigurieren und testen Sie Azure AD SSO mit Beyond Identity Admin Console unter Verwendung eines Testbenutzers namens **B.Simon**. Damit SSO funktioniert, müssen Sie eine Link-Beziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Beyond Identity Admin Console herstellen.

Um Azure AD SSO mit Beyond Identity Admin Console zu konfigurieren und testen, führen Sie die folgenden Schritte durch:

1. **[Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso)** , um Ihren Benutzern die Verwendung dieses Features zu ermöglichen.
    1. **[Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user)** , um das einmalige Anmelden von Azure AD mit dem Testbenutzer B. Simon zu testen.
    1. **[Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user)** , um B. Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen.
1. **[Konfigurieren Sie Beyond Identity Admin Console SSO](#configure-beyond-identity-admin-console-sso)** – um die Einmaliges Anmelden-Einstellungen auf der Anwendungsseite zu konfigurieren.
    1. **[Erstellen Sie einen Testbenutzer von Beyond Identity Admin Console](#create-beyond-identity-admin-console-test-user)** – um ein Gegenüber von B.Simon in Beyond Identity Admin Console zu haben, das mit der Azure AD Repräsentation des Benutzers verbunden ist.
1. **[Testen des einmaligen Anmeldens](#test-sso)** , um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Suchen Sie im Azure-Portal auf der Seite **Beyond Identity Admin Console** Anwendungsintegration den Abschnitt **Verwalten** und wählen Sie  **Einmaliges Anmelden**.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Klicken Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** auf das Stiftsymbol für **Grundlegende SAML-Konfiguration**, um die Einstellungen zu bearbeiten.

   ![Bearbeiten der SAML-Basiskonfiguration](common/edit-urls.png)

1. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    a. Geben Sie im Textfeld **Bezeichner (Entitäts-ID)** eine URL im folgenden Format ein: `https://admin.byndid.com/auth/saml/<azure-tenant-id>/sso/metadata.xml`.

    b. Geben Sie im Textfeld **Anmelde-URL** eine URL im folgenden Format ein: `https://admin.byndid.com/auth/?org_id=<bi-tenant-id>`.

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie diese Werte durch den tatsächlichen Bezeichner und die tatsächliche Anmelde-URL. Wenden Sie sich an das [Clientsupportteam von Beyond Identity Admin Console](mailto:support@beyondidentity.com), um diese Werte zu erhalten. Sie können sich auch die Muster im Abschnitt **Grundlegende SAML-Konfiguration** im Azure-Portal ansehen.

1. Die Anwendung Beyond Identity Admin Console erwartet die SAML-Assertions in einem bestimmten Format, so dass Sie Ihrer SAML-Token-Attributkonfiguration benutzerdefinierte Attributzuordnungen hinzufügen müssen. Der folgende Screenshot zeigt die Liste der Standardattribute.

    ![image](common/default-attributes.png)

1. Zusätzlich zu dem oben Genannten erwartet die Anwendung Beyond Identity Admin Console, dass in der SAML-Antwort einige weitere Attribute zurückgegeben werden, die unten aufgeführt sind. Diese Attribute werden ebenfalls vorab aufgefüllt, Sie können sie jedoch nach Bedarf überprüfen.
    
    | Name | Namespace  |  Quellattribut|
    | ---------------| --------------- | --------- |
    | immutableId | externalId | user.immutableld |

1. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** im Abschnitt **SAML-Signaturzertifikat** zu **Verbundmetadaten-XML**, und wählen Sie **Herunterladen** aus, um das Zertifikat herunterzuladen und auf Ihrem Computer zu speichern.

    ![Downloadlink für das Zertifikat](common/metadataxml.png)

1. Kopieren Sie im Abschnitt **Einrichten von Beyond Identity Admin Console** die entsprechende(n) URL(s) gemäß Ihren Anforderungen.

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

In diesem Abschnitt aktivieren Sie B.Simon für die Nutzung von Azure Einmaliges Anmelden, indem Sie den Zugriff auf die Beyond Identity Admin Console gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **Beyond Identity Admin Console**.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-beyond-identity-admin-console-sso"></a>Konfigurieren von Beyond Identity Admin Console SSO

Um Einmaliges Anmelden auf der Seite **Beyond Identity Admin Console** zu konfigurieren, müssen Sie die heruntergeladenen **Verbundmetadaten XML** und die entsprechenden kopierten URLs vom Azure-Portal an das [ Beyond Identity Admin Console-Supportteam](mailto:support@beyondidentity.com) senden. Es führt die Einrichtung durch, damit die SAML-SSO-Verbindung auf beiden Seiten richtig festgelegt ist.

### <a name="create-beyond-identity-admin-console-test-user"></a>Erstellen von einem Beyond Identity Admin Console Testbenutzer

In diesem Abschnitt erstellen Sie einen Benutzer namens Britta Simon in Beyond Identity Admin Console. Arbeiten Sie mit dem Supportteam von [Beyond Identity Admin Console](mailto:support@beyondidentity.com) zusammen, um die Benutzer in der Beyond Identity Admin Console-Plattform hinzuzufügen. Benutzer müssen erstellt und aktiviert werden, damit Sie einmaliges Anmelden verwenden können.

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie auf die Anmelde-URL der Beyond Identity Admin Console weitergeleitet, wo Sie den Anmeldevorgang veranlassen können. 

* Rufen Sie die Anmelde-URL der Beyond Identity Admin Console direkt auf und veranlassen Sie den Anmeldevorgang von dort aus.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie in MyApps auf die Kachel Beyond Identity Admin Console klicken, werden Sie zur Anmelde-URL für die Beyond Identity Admin Console umgeleitet. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie die Beyond Identity Admin Console konfiguriert haben, können Sie die Sitzungskontrolle erzwingen, die vor Exfiltration und Infiltration von sensiblen Daten Ihres Unternehmens in Echtzeit schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-aad)
