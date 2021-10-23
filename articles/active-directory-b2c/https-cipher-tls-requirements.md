---
title: 'Anforderungen an TLS und Verschlüsselungssammlungen: Azure AD B2C'
titleSuffix: Azure AD B2C
description: Hinweise für Entwickler zu HTTPS-Verschlüsselungssammlungs- und TLS-Anforderungen bei der Interaktion mit Web-API-Endpunkten.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 04/30/2021
ms.custom: project-no-code
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: 353ab6e58e0c8f563a75d2bf7fa48d48a5d1dfc2
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "130036870"
---
# <a name="azure-active-directory-b2c-tls-and-cipher-suite-requirements"></a>Azure Active Directory B2C TLS- und Cipher Suite-Anforderungen

Azure Active Directory B2C (Azure AD B2C) stellt in [Benutzerflows](user-flow-overview.md) über [API-Connectors](api-connectors-overview.md) und [Identitätsanbieter](oauth2-technical-profile.md) eine Verbindung mit Ihren Endpunkten her. In diesem Artikel werden die Anforderungen für TLS und Verschlüsselungssammlung für Ihre Endpunkte erläutert.

Die mit API-Connectors und Identitätsanbietern konfigurierten Endpunkte müssen in einem öffentlich zugänglichen HTTPS-URI veröffentlicht werden. Bevor eine sichere Verbindung mit dem Endpunkt hergestellt wird, werden das Protokoll und die Verschlüsselung basierend auf den Funktionen beider Seiten der Verbindung zwischen Azure AD B2C und dem Endpunkt ausgehandelt.

Azure AD B2C muss sich mit Ihren Endpunkten mithilfe von Transport Layer Security (TLS) und Verschlüsselungssammlungen verbinden können, wie in diesem Artikel beschrieben.

## <a name="tls-versions"></a>TLS-Versionen

TLS Version 1.2 ist ein kryptografisches Protokoll, welches Authentifizierungs- und Datenverschlüsselung zwischen Servern und Clients bereitstellt. Ihr Endpunkt muss sichere Kommunikation über **TLS Version 1.2** unterstützen. Die älteren TLS Versionen 1.0 und 1.1 sind veraltet. 

## <a name="cipher-suites"></a>Verschlüsselungssammlungen

Verschlüsselungssammlungen sind Sammlungen kryptografischer Algorithmen. Sie enthalten wichtige Informationen zur sicheren Kommunikation von Daten bei Verwendung des HTTPS-Protokolls über TLS.

Ihr Endpunkt muss mindestens einen der folgenden Verschlüsselungen unterstützen:

- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384

## <a name="endpoints-in-scope"></a>Abgedeckte Endpunkte

Die folgenden Endpunkte Ihrer Azure AD B2C-Umgebung müssen den in diesem Artikel beschriebenen Anfordrungen entsprechen:

- [API-Connectors](api-connectors-overview.md) 
- OAuth1
    - Token endpoint (Tokenendpunkt) 
    - Benutzerinfo-Endpunkt
- OAuth2 und OpenId Connect Identitätsanbieter
    - OpenId Connect Discovery-Endpunkt
    - OpenId Connect JWKS-Endpunkt
    - Token endpoint (Tokenendpunkt) 
    - Benutzerinfo-Endpunkt
- [ID-Tokenhinweis](id-token-hint.md)
    - OpenId Connect Discovery-Endpunkt
    - OpenId Connect JWKS-Endpunkt
- [SAML-Identitätsanbieter](saml-service-provider.md) Metadaten-Endpunkt
- [SAML-Dienstanbieter](identity-provider-generic-saml.md) Metadaten-Endpunkt

## <a name="check-your-endpoint-compatibility"></a>Prüfen Sie Ihre Endpunkt-Kompatibilität

Um sicherzustellen, dass Ihre Endpunkte den in diesem Artikel beschriebenen Anforderungen entsprechen, führen Sie einen Test mit einem TLS Verschlüsselungs- und Scannertools durch. Testen Sie Ihren Endpunkt mit [SSLLABS](https://www.ssllabs.com/ssltest/analyze.html).


## <a name="next-steps"></a>Nächste Schritte

Beachten Sie außerdem folgende Artikel:

- [Problembehandlung für Anwendungen, die TLS 1.2 nicht unterstützen](../cloud-services/applications-dont-support-tls-1-2.md)
- [Cipher Suites in TLS/SSL (Schannel SSP)](/windows/win32/secauthn/cipher-suites-in-schannel)
- [Aktivieren von TLS 1.2](/mem/configmgr/core/plan-design/security/enable-tls-1-2)
- [Beheben des Problems mit TLS 1.0](/security/engineering/solving-tls1-problem)