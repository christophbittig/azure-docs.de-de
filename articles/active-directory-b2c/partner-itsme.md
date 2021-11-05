---
title: itsme OpenID Connect mit Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: Es wird beschrieben, wie Sie die Azure AD B2C-Authentifizierung mit itsme OIDC integrieren, indem Sie die Benutzerflowrichtlinie „client_secret“ verwenden. itsme ist eine App für digitale IDs. Sie ermöglicht Ihnen das sichere Anmelden, ohne dass Sie Kartenleser, Kennwörter, die zweistufige Authentifizierung oder mehrere PIN-Codes verwenden müssen.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/20/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: 73802a0d429d2eb71ec24be8b18d635eb5c2f003
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131044568"
---
# <a name="configure-itsme-openid-connect-oidc-with-azure-active-directory-b2c"></a>Konfigurieren von itsme OpenID Connect (OIDC) mit Azure Active Directory B2C

Mit der itsme-App für digitale IDs können Sie sich sicher anmelden, ohne Kartenleser, Kennwörter, die zweistufige Authentifizierung oder mehrere PIN-Codes verwenden zu müssen. Die itsme-App ermöglicht eine sichere Kundenauthentifizierung mit einer verifizierten Identität. In diesem Artikel wird beschrieben, wie Sie die Azure AD B2C-Authentifizierung mit itsme OpenID Connect (OIDC) integrieren, indem Sie eine Benutzerflowrichtlinie mit einem geheimen Clientschlüssel verwenden.

## <a name="prerequisites"></a>Voraussetzungen

Zunächst benötigen Sie Folgendes:

* Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.
* [Einen Azure AD B2C-Mandanten](tutorial-create-tenant.md), der mit Ihrem Azure-Abonnement verknüpft ist.
* Ihre Client-ID (auch als Partnercode bezeichnet), die von itsme bereitgestellt wird.
* Ihren Dienstcode, der von itsme bereitgestellt wird.
* Ihren geheimen Clientschlüssel für Ihr itsme-Konto.

## <a name="scenario-description"></a>Beschreibung des Szenarios

![Architekturdiagramm für itsme](media/partner-itsme/itsme-architecture-diagram.png)

<!--
Please clarify step 1 in the description below - we don't have steps in this tutorial for "adapting in the Azure AD B2C Custom Policy- User Journeys" - should this be added somewhere?
-->

| Schritt | BESCHREIBUNG |
|------|------|
|1     | Fügen Sie auf Ihrer Website bzw. in Ihrer Anwendung die Schaltfläche **Log in with itsme** (Mit itsme anmelden) hinzu, indem Sie den Azure AD B2C-Benutzerflow anpassen. Der Interaktionsfluss beginnt, wenn Benutzer auf diese Schaltfläche klicken.  |
|2     | Azure AD B2C startet den OpenID Connect-Ablauf, indem eine „Authorize“-Anforderung an die itsme-API für den geheimen Clientschlüssel gesendet wird. Es ist ein bekannter bzw. OpenID-Konfigurationsendpunkt verfügbar, der Informationen zu den Endpunkten enthält.  |
|3     | Der Benutzer wird von der itsme-Umgebung an die itsme-Seite für die Identifikation umgeleitet, damit er seine Telefonnummer angeben kann.  |
|4     | Die itsme-Umgebung empfängt die Telefonnummer vom Benutzer und überprüft ihre Richtigkeit.  |
|5     | Wenn die Telefonnummer zu einem aktiven itsme-Benutzer gehört, wird eine Aktion für die itsme-App erstellt.  |
|6     | Der Benutzer öffnet die itsme-App, überprüft die Anforderung und bestätigt die Aktion.  |
|7     |  Die App informiert die itsme-Umgebung darüber, dass die Aktion bestätigt wurde. |
|8     |  Die itsme-Umgebung gibt den OAuth-Autorisierungscode an Azure AD B2C zurück. |
|9     |  Mit dem Autorisierungscode wird von Azure AD B2C eine Tokenanforderung durchgeführt. |
| 10 | Die itsme-Umgebung überprüft die Tokenanforderung. Falls sie noch gültig ist, gibt sie das OAuth-Zugriffstoken und das ID-Token mit den angeforderten Benutzerinformationen zurück. |
| 11 | Abschließend werden Benutzer*innen als authentifizierte Benutzer*innen an die Umleitungs-URL umgeleitet.  |
|   |   |

## <a name="onboard-with-itsme"></a>Durchführen des Onboardings mit itsme

1. Greifen Sie auf dem [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace) auf itsme zu, um ein Konto dafür zu erstellen.

1. Aktivieren Sie Ihr itsme-Konto, indem Sie eine E-Mail an onboarding@itsme.be senden. Sie erhalten einen **Partnercode** und einen **Dienstcode**, die Sie für Ihre B2C-Einrichtung benötigen.

1. Nachdem Sie Ihr itsme-Partnerkonto aktiviert haben, erhalten Sie eine E-Mail mit einem einmaligen Link zum **geheimen Clientschlüssel**.

1. Befolgen Sie die Anleitung unter [itsme](https://business.itsme.be/en), um die Konfiguration abzuschließen.

## <a name="integrate-with-azure-ad-b2c"></a>Integration in Azure AD B2C

### <a name="set-up-a-new-identity-provider-in-azure-ad-b2c"></a>Einrichten eines neuen Identitätsanbieters in Azure AD B2C

> [!NOTE]
> Wenn Sie noch nicht über einen Azure AD B2C-Mandanten verfügen, [erstellen Sie einen](tutorial-create-tenant.md), der mit Ihrem Azure-Abonnement verknüpft ist.

1. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das den Azure AD B2C-Mandanten enthält. Wählen Sie auf der Symbolleiste des Portals das Symbol **Verzeichnisse und Abonnements** aus.
1. Suchen Sie auf der Seite **Portaleinstellungen > Verzeichnisse und Abonnements** das Azure AD B2C-Verzeichnis in der Liste **Verzeichnisname**, und klicken Sie dann auf **Wechseln**.
1. Wählen Sie unter **Azure-Dienste** die Option **Azure AD B2C** aus (oder **Weitere Dienste**, und verwenden Sie dann das Suchfeld **Alle Dienste**, um nach *Azure AD B2C* zu suchen).
1. Wählen Sie **Identitätsanbieter** und dann **Neuer OpenID Connect-Anbieter** aus.
1. Füllen Sie das Formular mit den folgenden Informationen aus:

   |Eigenschaft | Wert |
   |------------ |------- |
   | Name | itsme |
   | Metadaten-URL | `https://oidc.<environment>.itsme.services/clientsecret-oidc/csapi/v0.1/.well-known/openid-configuration` <br>Hierbei ist `<environment>` entweder `e2e` (Testumgebung) oder `prd` (Produktion).  |
   | ClientID     | Ihre **Client-ID**, auch als **Partnercode** bezeichnet  |
   | Geheimer Clientschlüssel | Ihr **client_secret** |
   | `Scope`  | openid service:YOURSERVICECODE profile email [phone] [address]  |
   |Antworttyp | code |
   |Antwortmodus | Abfrage |
   |Domänenhinweis | *Kann leer bleiben* |
   |UserID | sub |
   |Anzeigename | name |
   |Vorname | given_name |
   |Surname | family_name |
   |Email | email|

1. Wählen Sie **Speichern** aus.

### <a name="configure-a-user-flow"></a>Konfigurieren eines Benutzerflows

1. Wählen Sie auf Ihrem Azure AD B2C-Mandanten unter **Richtlinien** die Option **Benutzerflows** aus.

1. Wählen Sie die Option **Neuer Benutzerflow** aus.

1. Wählen Sie **Registrierung und Anmeldung**, dann eine Version und anschließend **Erstellen** aus.

1. Geben Sie einen **Namen** ein.

1. Wählen Sie im Abschnitt **Identitätsanbieter** die Option **itsme** aus.

1. Klicken Sie auf **Erstellen**.

1. Öffnen Sie den neu erstellten Benutzerflow, indem Sie dessen Namen auswählen.

1. Wählen Sie **Eigenschaften** aus, und passen Sie die folgenden Werte an:

   * Ändern Sie **Lebensdauer von Zugriffs- und ID-Token (Minuten)** in **5**.
   * Ändern Sie **Lebensdauer für gleitendes Fenster des Aktualisierungstokens** in **Kein Ablauf**.

### <a name="register-an-application"></a>Registrieren einer Anwendung

1. Wählen Sie auf Ihrem B2C-Mandanten unter **Verwalten** die Option **App-Registrierungen** > **Neue Registrierung** aus.

1. Geben Sie unter **Name** einen Namen für die Anwendung und Ihren **Umleitungs-URI** ein. Geben Sie zu Testzwecken `https://jwt.ms` ein.

1. Stellen Sie sicher, dass die mehrstufige Authentifizierung **Deaktiviert** ist.

1. Wählen Sie **Registrieren**.

   a. Wählen Sie zu Testzwecken die Option **Authentifizierung** aus, und aktivieren Sie unter **Implizite Gewährung** die Kontrollkästchen **Zugriffstoken** und **ID-Token**.  

   b. Wählen Sie **Speichern** aus.

## <a name="test-the-user-flow"></a>Testen des Benutzerflows

1. Wählen Sie auf Ihrem B2C-Mandanten unter **Richtlinien** die Option **Benutzerflows** aus.

1. Wählen Sie den zuvor erstellten Benutzerflow aus.

1. Wählen Sie **Benutzerflow ausführen** aus.

   a. **Anwendung**: *Wählen Sie die registrierte App aus*.

   b. **Antwort-URL**: *Wählen Sie die Umleitungs-URL aus*.

1. Die itsme-Seite für die Identifikation (**Identify yourself**) wird angezeigt.  

1. Geben Sie Ihre Mobiltelefonnummer ein, und wählen Sie **Send** (Senden) aus.

1. Bestätigen Sie die Aktion in der itsme-App.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in den folgenden Artikeln:

* [Benutzerdefinierte Richtlinien in Azure AD B2C](custom-policy-overview.md)

* [Erste Schritte mit benutzerdefinierten Richtlinien in Azure AD B2C](tutorial-create-user-flows.md?pivots=b2c-custom-policy)
