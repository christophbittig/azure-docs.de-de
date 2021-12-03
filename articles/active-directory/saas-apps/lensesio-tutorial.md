---
title: 'Tutorial: Integrieren von Lenses.io in das einmalige Anmelden (Single Sign-On, SSO) von Azure Active Directory | Microsoft-Dokumentation'
description: In diesem Tutorial erfahren Sie, wie Sie einmaliges Anmelden zwischen Azure Active Directory und Lenses.io konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/21/2021
ms.author: jeedes
ms.openlocfilehash: d2766aede2fc105fae9ca084efcfaab77d16935b
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132344425"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-the-lensesio-dataops-portal"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit dem Lenses.io-DataOps-Portal

In diesem Tutorial erfahren Sie, wie Sie das [Lenses.io](https://lenses.io/)-DataOps-Portal in Azure Active Directory (Azure AD) integrieren. Nach der Integration von Lenses.io in Azure AD haben Sie folgende Möglichkeiten:

* Steuern Sie in Azure AD, wer Zugriff auf das Lenses.io-Portal hat.
* Ermöglichen Sie es Ihren Benutzern, sich mit ihren Azure AD-Konten automatisch bei Lenses anzumelden.
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Für die ersten Schritte benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Eine Instanz eines Lenses-Portals. Sie können zwischen verschiedenen [Bereitstellungsoptionen](https://lenses.io/product/deployment/) wählen.
* Eine Lenses.io-[Lizenz](https://lenses.io/product/pricing/), die einmaliges Anmelden (Single Sign-On, SSO) unterstützt

> [!NOTE]
> Diese Integration kann auch über die Azure AD-Umgebung für die US Government-Cloud verwendet werden. Sie finden diese Anwendung im Azure AD-Katalog für US Government-Cloudanwendungen und konfigurieren sie auf die gleiche Weise wie in der öffentlichen Cloud.

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* Lenses.io unterstützt vom Dienstanbieter (Service Provider, SP) initiiertes einmaliges Anmelden.

## <a name="add-lensesio-from-the-gallery"></a>Hinzufügen von Lenses.io aus dem Katalog

Fügen Sie zum Konfigurieren der Integration von Lenses.io in Azure AD Ihrer Liste der verwalteten SaaS-Apps Lenses.io aus dem Katalog hinzu.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wählen Sie im linken Bereich den Dienst **Azure Active Directory** aus.
1. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen** aus.
1. Wählen Sie **Neue Anwendung** aus.
1. Geben Sie im Abschnitt **Aus Katalog hinzufügen** im Suchfeld den Begriff **Lenses.io** ein.
1. Wählen Sie im Ergebnisbereich den Eintrag **Lenses.io** aus, und fügen Sie dann die App hinzu. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-lensesio"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für Lenses.io

Sie erstellen einen Testbenutzer mit dem Namen *B. Simon*, um das einmalige Anmelden von Azure AD mit Ihrem Lenses.io-Portal zu konfigurieren und testen. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in Lenses.io eingerichtet werden.

Führen Sie die folgenden Schritte aus:

1. [Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso), um Ihren Benutzern die Verwendung dieses Features zu ermöglichen
    1. [Erstellen eines Azure AD-Testbenutzers und einer Testgruppe](#create-an-azure-ad-test-user-and-group), um das einmalige Anmelden von Azure AD mit B. Simon zu testen
    1. [Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user), um B.Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen
1. [Konfigurieren des einmaliges Anmeldens für Lenses.io](#configure-lensesio-sso), um die Einstellungen für einmaliges Anmelden auf der Anwendungsseite zu konfigurieren
    1. [Erstellen von Berechtigungen für Lenses.io-Testgruppen](#create-lensesio-test-group-permissions), um zu steuern, worauf B. Simon in Lenses.io zugreifen kann (Autorisierung)
1. [Testen des einmaligen Anmeldens](#test-sso), um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren:

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **Lenses.io** zum Abschnitt **Verwalten**, und wählen Sie **Einmaliges Anmelden** aus.
1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.
1. Wählen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** das Stiftsymbol für **Grundlegende SAML-Konfiguration** aus, um die Einstellungen zu bearbeiten.

   ![Screenshot: Symbol zum Bearbeiten der grundlegenden SAML-Konfiguration](common/edit-urls.png)

1. Führen Sie im Abschnitt **Grundlegende SAML-Konfiguration** die folgenden Schritte aus:

    a. **Bezeichner (Entitäts-ID)** : Geben Sie eine URL mit folgendem Muster ein: `https://<CUSTOMER_LENSES_BASE_URL>`. z. B. `https://lenses.my.company.com`.

    b. **Antwort-URL**: Geben Sie eine URL mit folgendem Muster ein: `https://<CUSTOMER_LENSES_BASE_URL>/api/v2/auth/saml/callback?client_name=SAML2Client`. z. B. `https://lenses.my.company.com/api/v2/auth/saml/callback?client_name=SAML2Client`.

    c. **Anmelde-URL**: Geben Sie eine URL mit folgendem Muster ein: `https://<CUSTOMER_LENSES_BASE_URL>`. z. B. `https://lenses.my.company.com`.

    > [!NOTE]
    > Hierbei handelt es sich um Beispielwerte. Ersetzen Sie sie durch die tatsächlichen Werte für den Bezeichner, die Antwort-URL, die Anmelde-URL und die Basis-URL Ihrer Lenses-Portalinstanz. Weitere Informationen finden Sie in der [Lenses.io-Dokumentation zu SSO](https://docs.lenses.io/install_setup/configuration/security.html#single-sign-on-sso-saml-2-0).

1. Navigieren Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** zum Abschnitt **SAML-Signaturzertifikat**. Navigieren Sie zu **Verbundmetadaten-XML**, wählen Sie die Option **Herunterladen** aus, um das Zertifikat herunterzuladen, und speichern Sie es auf Ihrem Computer.

    ![Screenshot: Downloadlink für das Zertifikat](common/metadataxml.png)

1. Verwenden Sie im Abschnitt **Lenses.io einrichten** die heruntergeladene XML-Datei, um Lenses für Azure SSO zu konfigurieren.

### <a name="create-an-azure-ad-test-user-and-group"></a>Erstellen eines Azure AD-Testbenutzers und einer zugehörigen Gruppe

Im Azure-Portal erstellen Sie einen Testbenutzer mit dem Namen B. Simon. Anschließend erstellen Sie eine Testgruppe, die den Zugriff steuert, den B. Simon in Lenses hat.

Die [Lenses-Dokumentation zu SSO](https://docs.lenses.io/install_setup/configuration/security.html#id3) enthält Informationen dazu, wie in Lenses die Zuordnung der Gruppenmitgliedschaft für die Autorisierung genutzt wird.

**So erstellen Sie den Testbenutzer:**

1. Wählen Sie im Azure-Portal im linken Bereich **Azure Active Directory** > **Benutzer** > **Alle Benutzer** aus.
1. Wählen Sie oben im Bildschirm die Option **Neuer Benutzer** aus.
1. Führen Sie unter den Eigenschaften für **Benutzer** die folgenden Schritte aus:
   1. Geben Sie im Feld **Name** den Namen **B.Simon** ein.  
   1. Geben Sie im Feld **Benutzername** den Namen username@companydomain.extension ein. Beispiel: B.Simon@contoso.com.
   1. Aktivieren Sie das Kontrollkästchen **Kennwort anzeigen**. Notieren Sie sich das Kennwort, das im Feld **Kennwort** angezeigt wird.
   1. Klicken Sie auf **Erstellen**.

**Erstellen Sie die Gruppe wie folgt:**

1. Navigieren Sie zu **Azure Active Directory**, und wählen Sie dann **Gruppen** aus.
1. Wählen Sie oben auf dem Bildschirm die Option **Neue Gruppe** aus.
1. Führen Sie unter **Gruppeneigenschaften** die folgenden Schritte aus:
   1. Wählen Sie im Feld **Gruppentyp** die Option **Sicherheit** aus.
   1. Gegen Sie in das Feld **Gruppenname** den Namen **LensesUsers** ein.
   1. Klicken Sie auf **Erstellen**.
1. Wählen Sie die Gruppe **LensesUsers** aus, und kopieren Sie die **Objekt-ID** (z. B. f8b5c1ec-45de-4abd-af5c-e874091fb5f7). Diese ID wird in Lenses verwendet, um Benutzer der Gruppe den [richtigen Berechtigungen](https://docs.lenses.io/install_setup/configuration/security.html#id3) zuzuordnen.  

**Weisen Sie die Gruppe wie folgt dem Testbenutzer zu:**

1. Navigieren Sie zu **Azure Active Directory**, und wählen Sie dann **Benutzer** aus.
1. Wählen Sie den Testbenutzer **B. Simon** aus.
1. Wählen Sie **Gruppen** aus.
1. Wählen Sie am oberen Bildschirmrand die Option **Mitgliedschaften hinzufügen** aus.
1. Suchen Sie nach **LensesUsers**, und wählen Sie diese Option aus.
1. Klicken Sie auf **Auswählen**.

### <a name="assign-the-azure-ad-test-user"></a>Zuweisen des Azure AD-Testbenutzers

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf Lenses.io gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste die Anwendung **Lenses.io** aus.
1. Wählen Sie auf der Übersichtsseite der App im Abschnitt **Verwalten** die Option **Benutzer und Gruppen** aus.
1. Klicken Sie auf **Benutzer hinzufügen**.
1. Wählen Sie im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ die Option **B.Simon** aus. Klicken Sie unten auf der Seite auf die Schaltfläche **Auswählen**.
1. Falls Sie in der SAML-Assertion einen Rollenwert erwarten, wählen Sie im Dialogfeld **Rolle auswählen** die entsprechende Rolle für den Benutzer aus der Liste aus. Klicken Sie unten auf der Seite auf die Schaltfläche **Auswählen**.
1. Wählen Sie im Dialogfeld **Zuweisung hinzufügen** die Schaltfläche **Zuweisen**.

## <a name="configure-lensesio-sso"></a>Konfigurieren des einmaligen Anmeldens für Lenses.io

Installieren Sie zum Konfigurieren des einmaligen Anmeldens im **Lenses.io**-Portal die heruntergeladene **Verbundmetadaten-XML** in Ihrer Lenses-Instanz, und [konfigurieren Sie Lenses für die Aktivierung von SSO](https://docs.lenses.io/install_setup/configuration/security.html#configure-lenses).

### <a name="create-lensesio-test-group-permissions"></a>Erstellen von Berechtigungen für die Lenses.io-Testgruppe

1. Verwenden Sie zum Erstellen einer Gruppe in Lenses die **Objekt-ID** der Gruppe **LensesUsers**. Dabei handelt es sich um die ID, die Sie im Abschnitt zum [Erstellen eines Benutzers](#create-an-azure-ad-test-user-and-group) kopiert haben.
1. Weisen Sie die gewünschten Berechtigungen für B. Simon zu.

Weitere Informationen finden Sie im Abschnitt zum Thema [Azure: Lenses-Gruppenzuordnung](https://docs.lenses.io/install_setup/configuration/security.html#azure-groups).

## <a name="test-sso"></a>Testen des einmaligen Anmeldens

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für Lenses.io weitergeleitet, wo Sie den Anmeldeablauf initiieren können. 

* Rufen Sie direkt die Anmelde-URL für Lenses.io auf, und initiieren Sie den Anmeldeablauf.

* Sie können „Meine Apps“ von Microsoft verwenden. Wenn Sie in „Meine Apps“auf die Kachel „Lenses.io“ klicken, werden Sie zur Anmelde-URL für Lenses.io weitergeleitet. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Nach dem Konfigurieren von Lenses.io können Sie die Sitzungssteuerung erzwingen, die Ihre vertraulichen Unternehmensdaten in Echtzeit vor der Exfiltration und Infiltration schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-aad)
