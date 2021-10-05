---
title: 'Tutorial: Konfigurieren von Azure Active Directory B2C mit Zscaler'
titleSuffix: Azure AD B2C
description: Erfahren Sie, wie Sie die Azure AD B2C-Authentifizierung in Zscaler integrieren.
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/09/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: fb4ff79e1115086ef84694485cd9e7978295a9c0
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128598839"
---
# <a name="tutorial-configure-zscaler-private-access-with-azure-active-directory-b2c"></a>Tutorial: Konfigurieren von Zscaler Private Access (ZPA) mit Azure Active Directory B2C

In diesem Tutorial erfahren Sie, wie Sie die Azure AD B2C-Authentifizierung (Azure Active Directory B2C) in [Zscaler Private Access (ZPA)](https://www.zscaler.com/products/zscaler-private-access) integrieren. ZPA ermöglicht einen richtlinienbasierten, sicheren Zugriff auf private Anwendungen und Ressourcen ohne die Kosten, den Aufwand oder die Sicherheitsrisiken eines virtuellen privaten Netzwerks (VPN). Das Angebot des sicheren Hybridzugriffs von Zscaler ermöglicht in Kombination mit Azure AD B2C eine unangreifbare Oberfläche für kundenorientierte Anwendungen.

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie beginnen, müssen die folgenden Voraussetzungen erfüllt sein:

- Ein Azure-Abonnement. Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.  
- [Einen Azure AD B2C-Mandanten](./tutorial-create-tenant.md), der mit Ihrem Azure-Abonnement verknüpft ist.  
- [Ein ZPA-Abonnement](https://azuremarketplace.microsoft.com/marketplace/apps/aad.zscalerprivateaccess?tab=Overview)

## <a name="scenario-description"></a>Beschreibung des Szenarios

Die ZPA-Integration umfasst die folgenden Komponenten:

- **Azure AD B2C**: Der Identitätsanbieter (IdP), mit dem die Anmeldeinformationen des Benutzers überprüft werden. Außerdem ist er für die Registrierung eines neuen Benutzers verantwortlich.  
- **ZPA:** Der Dienst, der für den Schutz der Webanwendung durch Erzwingen des [Zero Trust-Zugriffs](https://www.microsoft.com/security/blog/2018/12/17/zero-trust-part-1-identity-and-access-management/#:~:text=Azure%20Active%20Directory%20%28Azure%20AD%29%20provides%20the%20strong%2C,to%20express%20their%20access%20requirements%20in%20simple%20terms.) zuständig ist.  
- **Die Webanwendung:** Hostet den Dienst, auf den der Benutzer zugreifen möchte.

Das folgende Diagramm zeigt, wie ZPA in Azure AD B2C integriert wird.

![Diagramm der Zscaler-Architektur, das zeigt, wie ZPA in Azure AD B2C integriert ist.](media/partner-zscaler/zscaler-architecture-diagram.png)

Diese Sequenz wird in der folgenden Tabelle beschrieben:

|Schritt | BESCHREIBUNG |
| :-----:| :-----------|
| 1 | Ein Benutzer öffnet ein ZPA-Benutzerportal oder eine ZPA-Browserzugriffsanwendung.
| 2 | ZPA benötigt die Benutzerkontextinformationen für die Entscheidung, ob der Benutzer auf die Webanwendung zugreifen darf. Zum Authentifizieren des Benutzers führt ZPA eine SAML-Umleitung zur Azure AD B2C-Anmeldeseite aus.  
| 3 | Der Benutzer gelangt zur Azure AD B2C-Anmeldeseite. Neue Benutzer registrieren sich für die Erstellung eines Kontos, und vorhandene Benutzer melden mit ihren bestehenden Anmeldeinformationen an. Azure AD B2C überprüft die Identität des Benutzers.
| 4 | Bei erfolgreicher Authentifizierung leitet Azure AD B2C den Benutzer zusammen mit der SAML-Assertion zurück an ZPA. ZPA überprüft die SAML-Assertion und legt den Benutzerkontext fest.
| 5 | ZPA wertet die Zugriffsrichtlinien für den Benutzer aus. Wenn der Benutzer Zugriff auf die Webanwendung hat, darf die Verbindung weitergeleitet werden.

## <a name="onboard-to-zpa"></a>Integrieren in ZPA

In diesem Tutorial wird davon ausgegangen, dass Sie bereits eine funktionierende ZPA-Einrichtung haben. Lesen Sie zum Einstieg in ZPA den [Step-by-Step Configuration Guide for ZPA](https://help.zscaler.com/zpa/step-step-configuration-guide-zpa) (Leitfaden zur ZPA-Konfiguration in einzelnen Schritten).

## <a name="integrate-zpa-with-azure-ad-b2c"></a>Integration von ZPA mit Azure AD B2C

### <a name="step-1-configure-azure-ad-b2c-as-an-idp-on-zpa"></a>Schritt 1: Konfigurieren von Azure AD B2C als IdP in ZPA

Führen Sie die folgenden Schritte aus, um Azure AD B2C als [IdP in ZPA](https://help.zscaler.com/zpa/configuring-idp-single-sign) zu konfigurieren:

1. Melden Sie sich beim [ZPA Admin Portal](https://admin.private.zscaler.com) (ZPA-Verwaltungsportal) an.

1. Wechseln Sie zu **Administration** > **IdP Configuration** (Verwaltung > IdP-Konfiguration).

1. Wählen Sie **Add IdP Configuration** (IdP-Konfiguration hinzufügen) aus.

   Der Bereich **Add IdP Configuration** (IdP-Konfiguration hinzufügen) wird geöffnet.

   ![Screenshot der Registerkarte mit IdP-Informationen im Bereich „Add IdP Configuration“ (IdP-Konfiguration hinzufügen)](media/partner-zscaler/add-idp-configuration.png)

1. Wählen Sie die Registerkarte **IdP Information** (IdP-Informationen) aus, und gehen Sie dann wie folgt vor:

   a. Geben Sie im Feld **Name** die Bezeichnung **Azure AD B2C** ein.  
   b. Wählen Sie unter **Single Sign-On** (Einmaliges Anmelden) die Option **User** (Benutzer) aus.  
   c. Wählen Sie in der Dropdownliste **Domains** (Domänen) die Authentifizierungsdomänen aus, die Sie diesem IdP zuordnen möchten.

1. Wählen Sie **Weiter** aus.

1. Wählen Sie die Registerkarte **SP Metadata** (SP-Metadaten) aus, und gehen Sie dann wie folgt vor:

   a. Kopieren oder notieren Sie den Wert unter **Service Provider URL** (URL des Dienstanbieters) für die spätere Verwendung.  
   b. Kopieren oder notieren Sie den Wert unter **Service Provider Entity ID** (Entitäts-ID des Dienstanbieters) für die spätere Verwendung.

   ![Screenshot der Registerkarte mit SP-Metadaten im Bereich „Add IdP Configuration“ (IdP-Konfiguration hinzufügen)](media/partner-zscaler/sp-metadata.png)

1. Wählen Sie **Pause** (Anhalten) aus.

Die übrige IdP-Konfiguration wird nach dem Konfigurieren von Azure AD B2C fortgesetzt.

### <a name="step-2-configure-custom-policies-in-azure-ad-b2c"></a>Schritt 2: Konfigurieren benutzerdefinierter Richtlinien in Azure AD B2C

>[!Note]
>Dieser Schritt ist nur erforderlich, wenn Sie noch keine benutzerdefinierten Richtlinien konfiguriert haben. Wenn Sie bereits über eine oder mehrere benutzerdefinierte Richtlinien verfügen, können Sie diesen Schritt überspringen.

Weitere Informationen zum Konfigurieren benutzerdefinierter Richtlinien in Ihrem Azure AD B2C-Mandanten finden Sie unter [Erste Schritte für benutzerdefinierte Richtlinien in Azure Active Directory B2C](./tutorial-create-user-flows.md?pivots=b2c-custom-policy).

### <a name="step-3-register-zpa-as-a-saml-application-in-azure-ad-b2c"></a>Schritt 3: Registrieren von ZPA als SAML-Anwendung in Azure AD B2C

Der Artikel [Registrieren einer SAML-Anwendung in Azure AD B2C](./saml-service-provider.md) enthält Anweisungen zum Konfigurieren einer SAML-Anwendung in Azure AD B2C. 

Kopieren oder notieren Sie im Schritt [Hochladen Ihrer Richtlinie](./saml-service-provider.md#upload-your-policy) die URL zu den SAML-Metadaten des Identitätsanbieters, die von Azure AD B2C verwendet wird. Sie benötigen die Information später.

Folgen Sie den Anweisungen im Schritt [Konfigurieren Ihrer Anwendung in Azure AD B2C](./saml-service-provider.md#configure-your-application-in-azure-ad-b2c). Aktualisieren Sie in Schritt 4.2 die Eigenschaften im App-Manifest wie folgt:

- Bei **identifierUris**: Verwenden Sie die Entitäts-ID des Dienstanbieters, die Sie zuvor in Schritt 1.6.b kopiert oder notiert haben.  
- Bei **samlMetadataUrl**: Überspringen Sie diese Eigenschaft, da ZPA keine URL zu SAML-Metadaten hostet.  
- Bei **replyUrlsWithType**: Verwenden Sie die Dienstanbieter-URL, die Sie zuvor in Schritt 1.6.a kopiert oder notiert haben.  
- Bei **logoutUrl**: Überspringen Sie diese Eigenschaft, da ZPA keine Abmelde-URL unterstützt.

Die restlichen Schritte sind für dieses Tutorial nicht relevant.

### <a name="step-4-extract-the-idp-saml-metadata-from-azure-ad-b2c"></a>Schritt 4: Extrahieren der IdP-SAML-Metadaten aus Azure AD B2C

Als Nächstes müssen Sie eine SAML-Metadaten-URL im folgenden Format abrufen:

`https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/Samlp/metadata`

Dabei ist `<tenant-name>` der Name Ihres Azure AD B2C-Mandanten und `<policy-name>` der Name der benutzerdefinierten SAML-Richtlinie, die Sie im letzten Schritt erstellt haben.

Die URL lautet z. B.:

`https://safemarch.b2clogin.com/safemarch.onmicrosoft.com/B2C_1A_signup_signin_saml/Samlp/metadata`.

Öffnen Sie einen Webbrowser, und navigieren Sie zur SAML-Metadaten-URL. Klicken Sie mit der rechten Maustaste auf eine beliebige Stelle auf der Seite, wählen Sie **Speichern unter** aus, und speichern Sie die Datei auf Ihrem Computer für die Verwendung im nächsten Schritt.

### <a name="step-5-complete-the-idp-configuration-on-zpa"></a>Schritt 5: Abschließen der IdP-Konfiguration in ZPA

Schließen Sie die [IdP-Konfiguration im ZPA-Verwaltungsportal](https://help.zscaler.com/zpa/configuring-idp-single-sign) ab, die Sie in „Schritt 1: Konfigurieren von Azure AD B2C als IdP in ZPA“ teilweise konfiguriert haben.

1. Wechseln Sie im [ZPA Admin Portal](https://admin.private.zscaler.com) (ZPA-Verwaltungsportal) zu **Administration** > **IdP Configuration** (Verwaltung > IdP-Konfiguration).

1. Wählen Sie den in Schritt 1 konfigurierten IdP und dann **Resume** (Fortsetzen) aus.

1. Wählen Sie im Bereich **Add IdP Configuration** (IdP-Konfiguration hinzufügen) die Registerkarte **Create IdP** (IdP erstellen) aus, und gehen Sie dann wie folgt vor:

   a. Wechseln Sie zu **IdP Metadata File** (IdP-Metadatendatei), und laden Sie die Metadatendatei hoch, die Sie in „Schritt 4: Extrahieren der IdP-SAML-Metadaten aus Azure AD B2C“ gespeichert haben.  
   b. Überprüfen Sie, ob der **Status** für die IdP-Konfiguration **Enabled** (Aktiviert) lautet.  
   c. Wählen Sie **Speichern** aus.

   ![Screenshot der Registerkarte „Create IdP“ (IdP erstellen) im Bereich „Add IdP Configuration“ (IdP-Konfiguration hinzufügen)](media/partner-zscaler/create-idp.png)

## <a name="test-the-solution"></a>Testen der Lösung

Wechseln Sie zu einem ZPA-Benutzerportal oder einer Browserzugriffsanwendung, und testen Sie den Registrierungs- oder Anmeldevorgang. Das Testergebnis sollte eine erfolgreiche SAML-Authentifizierung sein.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in den folgenden Artikeln:

- [Erste Schritte mit benutzerdefinierten Richtlinien in Azure AD B2C](./tutorial-create-user-flows.md?pivots=b2c-custom-policy)
- [Registrieren einer SAML-Anwendung in Azure AD B2C](./saml-service-provider.md)
- [Step-by-Step Configuration Guide for ZPA](https://help.zscaler.com/zpa/step-step-configuration-guide-zpa) (Leitfaden zur ZPA-Konfiguration in einzelnen Schritten)
- [Configuring an IdP for Single Sign-On](https://help.zscaler.com/zpa/configuring-idp-single-sign) (Konfigurieren eines IdP für einmaliges Anmelden)
