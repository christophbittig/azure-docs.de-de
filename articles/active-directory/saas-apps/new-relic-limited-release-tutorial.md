---
title: 'Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit New Relic | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und New Relic konfigurieren.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/31/2021
ms.author: jeedes
ms.openlocfilehash: 67bbd26e4c565a5c7372be81fdf2398afe53c277
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132307218"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-new-relic"></a>Tutorial: Integration des einmaligen Anmeldens (Single Sign-On, SSO) von Azure Active Directory mit New Relic

In diesem Tutorial erfahren Sie, wie Sie New Relic in Azure Active Directory (Azure AD) integrieren. Die Integration von New Relic in Azure AD ermöglicht Folgendes:

* Steuern in Azure AD, wer Zugriff auf New Relic hat
* Ermöglichen, dass sich Benutzer mit ihren Azure AD-Konten automatisch bei New Relic anmelden können
* Verwalten Sie Ihre Konten zentral im Azure-Portal.

## <a name="prerequisites"></a>Voraussetzungen

Zunächst benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* Ein New Relic-Unternehmen mit dem [New Relic One Konto-/Benutzermodell](https://docs.newrelic.com/docs/accounts/original-accounts-billing/original-product-based-pricing/overview-changes-pricing-user-model/#user-models) entweder als Pro oder Enterprise Edition. Weitere Informationen hierzu finden Sie unter [New Relic-Anforderungen](https://docs.newrelic.com/docs/accounts/accounts-billing/new-relic-one-user-management/authentication-domains-saml-sso-scim-more).

## <a name="scenario-description"></a>Beschreibung des Szenarios

In diesem Tutorial konfigurieren und testen Sie das einmalige Anmelden von Azure AD in einer Testumgebung.

* New Relic unterstützt vom Dienstanbieter oder vom Identitätsanbieter initiiertes einmaliges Anmelden.

* New Relic unterstützt [automatisierte Benutzerbereitstellung](new-relic-by-organization-provisioning-tutorial.md).

## <a name="add-new-relic-from-the-gallery"></a>Hinzufügen von New Relic aus dem Katalog

Zum Konfigurieren der Integration von New Relic in Azure AD müssen Sie **New Relic (By Organization)** über den Katalog der Liste mit den verwalteten SaaS-Apps hinzufügen.

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim Azure-Portal an.
1. Wähle den Dienst **Azure Active Directory** aus.
1. Wählen Sie **Unternehmensanwendungen** > **Neue Anwendung** aus.
1. Geben Sie auf der Seite **Azure AD-Katalog durchsuchen** im Suchfeld den Suchbegriff **New Relic (By Organization)** ein.
1. Wählen Sie in den Ergebnissen den Eintrag **New Relic by Organization** aus, und wählen Sie dann **Erstellen** aus. Warten Sie einige Sekunden, während die App Ihrem Mandanten hinzugefügt wird.

## <a name="configure-and-test-azure-ad-sso-for-new-relic"></a>Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für New Relic

Konfigurieren und testen Sie das einmalige Anmelden von Azure AD für New Relic mithilfe eines Testbenutzers mit dem Namen **B. Simon**. Damit einmaliges Anmelden funktioniert, muss eine Linkbeziehung zwischen einem Azure AD-Benutzer und dem entsprechenden Benutzer in New Relic eingerichtet werden.

Führen Sie zum Konfigurieren und Testen des einmaligen Anmeldens von Azure AD für New Relic die folgenden Schritte aus:

1. [Konfigurieren des einmaligen Anmeldens von Azure AD](#configure-azure-ad-sso), um Ihren Benutzern die Verwendung dieses Features zu ermöglichen
   1. [Erstellen eines Azure AD-Testbenutzers](#create-an-azure-ad-test-user), um das einmalige Anmelden von Azure AD mit B.Simon zu testen
   1. [Zuweisen des Azure AD-Testbenutzers](#assign-the-azure-ad-test-user), um B.Simon die Verwendung des einmaligen Anmeldens von Azure AD zu ermöglichen
1. [Konfigurieren des einmaligen Anmeldens für New Relic](#configure-new-relic-sso), um die Einstellungen für einmaliges Anmelden aufseiten von New Relic zu konfigurieren.
   1. [Erstellen eines New Relic-Testbenutzers](#create-a-new-relic-test-user), um eine Entsprechung von B. Simon in New Relic zu erhalten, die mit dem Azure AD-Benutzer verknüpft ist.
1. [Testen des einmaligen Anmeldens](#test-sso), um zu überprüfen, ob die Konfiguration funktioniert

## <a name="configure-azure-ad-sso"></a>Konfigurieren des einmaligen Anmeldens (Single Sign-On, SSO) von Azure AD

Gehen Sie wie folgt vor, um das einmalige Anmelden von Azure AD im Azure-Portal zu aktivieren.

1. Navigieren Sie im Azure-Portal auf der Anwendungsintegrationsseite für **New Relic by Organization** zum Abschnitt **Verwalten**. Wählen Sie dann **Einmaliges Anmelden** aus.

1. Wählen Sie auf der Seite **SSO-Methode auswählen** die Methode **SAML** aus.

1. Wählen Sie auf der Seite **Einmaliges Anmelden (SSO) mit SAML einrichten** das Stiftsymbol für **Grundlegende SAML-Konfiguration** aus, um die Einstellungen zu bearbeiten.

   ![Screenshot: „Einmaliges Anmelden (SSO) mit SAML einrichten“ mit hervorgehobenem Stiftsymbol](common/edit-urls.png)

1. Geben Sie im Abschnitt **Grundlegende SAML-Konfiguration** die Werte für **Bezeichner** und **Antwort-URL** ein.

   * Rufen Sie diese Werte von der Benutzeroberfläche der [New Relic Authentifizierungsdomäne](https://docs.newrelic.com/docs/accounts/accounts-billing/new-relic-one-user-management/authentication-domains-saml-sso-scim-more/#ui) ab. Von dort aus: 
      1. Falls Sie über mehr als eine Authentifizierungsdomäne verfügen, wählen Sie die aus, mit der Sie für das einmalige Anmelden von Azure AD eine Verbindung herstellen möchten. Die meisten Unternehmen verfügen nur über eine Authentifizierungsdomäne mit dem Namen **Default**. Wenn nur eine Authentifizierungsdomäne vorhanden ist, müssen Sie nichts auswählen.
      1. Im Abschnitt **Authentication** (Authentifizierung) enthält das Feld **Assertion consumer URL** (Assertionsverbraucher-URL) den Wert, der für die **Antwort-URL** verwendet wird.
      1. Im Abschnitt **Authentication** (Authentifizierung) enthält das Feld **Our entity ID** (Unsere Entitäts-ID) den Wert, der als **Bezeichner** verwendet wird.

1. Stellen Sie im Abschnitt **Benutzerattribute und Ansprüche** sicher, dass **Eindeutige Benutzer-ID** einem Feld zugeordnet ist, das die E-Mail-Adresse von New Relic enthält.

   * Sie können das Standardfeld **user.userprincipalname** verwenden, wenn die zugehörigen Werte den New Relic-E-Mail-Adressen entsprechen.
   * Möglicherweise ist jedoch das Feld **user.mail** besser für Sie geeignet, wenn **user.userprincipalname** nicht die New Relic-E-Mail-Adresse ist.

1. Kopieren Sie im Abschnitt **SAML-Signaturzertifikat** die **App-Verbundmetadaten-URL**, und speichern Sie den Wert zur späteren Verwendung.

1. Kopieren Sie im Abschnitt **New Relic by Organization einrichten** die **Anmelde-URL**, und speichern Sie den Wert zur späteren Verwendung.

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

In diesem Abschnitt ermöglichen Sie B. Simon die Verwendung des einmaligen Anmeldens von Azure, indem Sie ihr Zugriff auf New Relic gewähren.

1. Wählen Sie im Azure-Portal **Unternehmensanwendungen** > **Alle Anwendungen** aus.
1. Wählen Sie in der Anwendungsliste **New Relic** aus.
1. Navigieren Sie auf der Übersichtsseite der App zum Abschnitt **Verwalten**, und wählen Sie **Benutzer und Gruppen** aus.
1. Wählen Sie **Benutzer hinzufügen** und anschließend im Dialogfeld **Zuweisung hinzufügen** die Option **Benutzer und Gruppen** aus.
1. Wählen Sie im Dialogfeld **Benutzer und Gruppen** in der Liste „Benutzer“ den Eintrag **B. Simon** aus, und klicken Sie dann unten auf dem Bildschirm auf die Schaltfläche **Auswählen**.
1. Wenn den Benutzern eine Rolle zugewiesen werden soll, können Sie sie im Dropdownmenü **Rolle auswählen** auswählen. Wurde für diese App keine Rolle eingerichtet, ist die Rolle „Standardzugriff“ ausgewählt.
1. Klicken Sie im Dialogfeld **Zuweisung hinzufügen** auf die Schaltfläche **Zuweisen**.

## <a name="configure-new-relic-sso"></a>Konfigurieren des einmaligen Anmeldens für New Relic

Führen Sie die folgenden Schritte aus, um einmaliges Anmelden für New Relic zu konfigurieren. 

1. [Melden Sie sich bei New Relic an](https://login.newrelic.com/).

1. Navigieren Sie zu der Benutzeroberfläche der [Authentifizierungsdomäne](https://docs.newrelic.com/docs/accounts/accounts-billing/new-relic-one-user-management/authentication-domains-saml-sso-scim-more/#ui). 

1. Wählen Sie die Authentifizierungsdomäne aus, mit der Sie für das einmalige Anmelden von Azure AD eine Verbindung herstellen möchten (falls Sie über mehr als eine Authentifizierungsdomäne verfügen). Die meisten Unternehmen verfügen nur über eine Authentifizierungsdomäne mit dem Namen **Default**. Wenn nur eine Authentifizierungsdomäne vorhanden ist, müssen Sie nichts auswählen.

1. Wählen Sie im Abschnitt **Authentifizierung** die Option **Konfigurieren** aus.

   1. Geben Sie für die **Quelle der SAML-Metadaten** den Wert ein, den Sie zuvor in Azure AD aus dem Feld **App-Verbundmetadaten-URL** kopiert und gespeichert haben.

   1. Geben Sie für die **SSO-Ziel-URL** den Wert ein, den Sie zuvor in Azure AD aus dem Feld **Anmelde-URL** kopiert und gespeichert haben.

   1. Nachdem Sie sich vergewissert haben, dass die Einstellungen sowohl für Azure AD als auch für New Relic korrekt sind, wählen Sie **Speichern** aus. Wenn die Einstellungen nicht auf beiden Seiten ordnungsgemäß konfiguriert sind, können sich Ihre Benutzer nicht bei New Relic anmelden.

### <a name="create-a-new-relic-test-user"></a>Erstellen eines New Relic-Testbenutzers

In diesem Abschnitt erstellen Sie in New Relic einen Benutzer namens „B. Simon“.

1. [Melden Sie sich bei New Relic an](https://login.newrelic.com/).

1. Navigieren Sie zu der [**Benutzerverwaltungsoberfläche**](https://docs.newrelic.com/docs/accounts/accounts-billing/new-relic-one-user-management/add-manage-users-groups-roles/#where).

1. Klicken Sie auf **Benutzer hinzufügen**.

   1. Geben Sie im Feld **Name** die Zeichenfolge **B. Simon** ein.
   
   1. Geben Sie im Feld **E-Mail** den Wert ein, der beim einmaligen Anmelden von Azure AD gesendet wird.
   
   1. Wählen Sie den **Typ** und die **Gruppe** für den Benutzer aus. Für einen Testbenutzer sind **Basisbenutzer** als Typ und **Benutzer** als Gruppe eine gute Wahl.
   
   1. Wählen Sie zum Speichern des Benutzers die Option **Benutzer hinzufügen** aus.

> [!NOTE]
> Außerdem unterstützt New Relic automatische Benutzerbereitstellung. Weitere Informationen zum Konfigurieren der automatischen Benutzerbereitstellung finden Sie [hier](./new-relic-by-organization-provisioning-tutorial.md).

## <a name="test-sso"></a>Testen des einmaligen Anmeldens 

In diesem Abschnitt testen Sie die Azure AD-Konfiguration für einmaliges Anmelden mit den folgenden Optionen: 

#### <a name="sp-initiated"></a>SP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch werden Sie zur Anmelde-URL für New Relic weitergeleitet, wo Sie den Anmeldeflow initiieren können.  

* Rufen Sie direkt die New Relic-Anmelde-URL auf, und initiieren Sie den Anmeldeflow.

#### <a name="idp-initiated"></a>IDP-initiiert:

* Klicken Sie im Azure-Portal auf **Diese Anwendung testen**. Dadurch sollten Sie automatisch bei der New Relic-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. 

Sie können auch den Microsoft-Bereich „Meine Apps“ verwenden, um die Anwendung in einem beliebigen Modus zu testen. Beim Klicken auf die Kachel „New Relic“ in „Meine Apps“ geschieht Folgendes: Wenn Sie die Anwendung im SP-Modus konfiguriert haben, werden Sie zum Initiieren des Anmeldeflows zur Anmeldeseite der Anwendung weitergeleitet. Wenn Sie die Anwendung im IDP-Modus konfiguriert haben, sollten Sie automatisch bei der New Relic-Instanz angemeldet werden, für die Sie einmaliges Anmelden eingerichtet haben. Weitere Informationen zu „Meine Apps“ finden Sie in [dieser Einführung](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510).

## <a name="next-steps"></a>Nächste Schritte

Anschließend können Sie überprüfen, ob Ihre Benutzer in New Relic hinzugefügt wurden, indem Sie die [**Benutzerverwaltungsoberfläche**](https://docs.newrelic.com/docs/accounts/accounts-billing/new-relic-one-user-management/add-manage-users-groups-roles/#where) aufrufen und sehen, ob sie dort vorhanden sind. 

Als Nächstes möchten Sie Ihre Benutzer wahrscheinlich bestimmten New Relic Konten oder Rollen zuweisen. Weitere Informationen hierzu finden Sie in der Dokumentation [Konzepte der Benutzerverwaltung](https://docs.newrelic.com/docs/accounts/accounts-billing/new-relic-one-user-management/add-manage-users-groups-roles/#understand-concepts). 

In der Benutzeroberfläche der Authentifizierungsdomäne von New Relic können Sie [weitere Einstellungen](https://docs.newrelic.com/docs/accounts/accounts-billing/new-relic-one-user-management/authentication-domains-saml-sso-scim-more/#session-mgmt) konfigurieren, wie z. B. die Sitzungssteuerung, die Ihre vertraulichen Unternehmensdaten in Echtzeit vor der Exfiltration und Infiltration schützt. Die Sitzungssteuerung basiert auf bedingtem Zugriff. [Erfahren Sie, wie Sie die Sitzungssteuerung mit Microsoft Defender for Cloud Apps erzwingen.](/cloud-app-security/proxy-deployment-any-app)
