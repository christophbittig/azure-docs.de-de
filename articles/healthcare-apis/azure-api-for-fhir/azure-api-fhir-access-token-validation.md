---
title: Überprüfung des Zugriffstokens in Azure API for FHIR
description: Hier werden die Schritte zur Überprüfung des Tokens erläutert und Tipps zur Behandlung von Zugriffsproblemen gegeben.
services: healthcare-apis
author: caitlinv39
ms.reviewer: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 08/05/2021
ms.author: cavoeg
ms.openlocfilehash: c823e74fa76b1b0d8d2b9974305e7f7df44c57a9
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122340039"
---
# <a name="azure-api-for-fhir-access-token-validation"></a>Überprüfung des Zugriffstokens in Azure API for FHIR

In welcher Form Azure API for FHIR das Zugriffstoken überprüft, hängt von der Implementierung und Konfiguration ab. In diesem Artikel werden die Überprüfungsschritte erläutert, die bei der Behandlung von Zugriffsproblemen hilfreich sein können.

## <a name="validate-token-has-no-issues-with-identity-provider"></a>Überprüfen, dass beim Token keine Probleme mit dem Identitätsanbieter auftreten

Im ersten Schritt der Tokenüberprüfung wird sichergestellt, dass das Token vom richtigen Identitätsanbieter ausgestellt und nicht geändert wurde. Der FHIR-Server wird für die Verwendung eines bestimmten Identitätsanbieters konfiguriert, der als autoritative Stelle (`Authority`) bezeichnet wird. Der FHIR-Server ruft Informationen zum Identitätsanbieter vom Endpunkt `/.well-known/openid-configuration` ab. Bei Verwendung von Azure AD lautet die vollständige URL wie folgt:

```
GET https://login.microsoftonline.com/<TENANT-ID>/.well-known/openid-configuration
```

Dabei ist `<TENANT-ID>` der jeweilige Azure AD-Mandant (entweder eine Mandanten-ID oder ein Domänenname).

Azure AD gibt ein Dokument wie das folgende an den FHIR-Server zurück.

```json
{
    "authorization_endpoint": "https://login.microsoftonline.com/<TENANT-ID>/oauth2/authorize",
    "token_endpoint": "https://login.microsoftonline.com/<TENANT-ID>/oauth2/token",
    "token_endpoint_auth_methods_supported": [
        "client_secret_post",
        "private_key_jwt",
        "client_secret_basic"
    ],
    "jwks_uri": "https://login.microsoftonline.com/common/discovery/keys",
    "response_modes_supported": [
        "query",
        "fragment",
        "form_post"
    ],
    "subject_types_supported": [
        "pairwise"
    ],
    "id_token_signing_alg_values_supported": [
        "RS256"
    ],
    "http_logout_supported": true,
    "frontchannel_logout_supported": true,
    "end_session_endpoint": "https://login.microsoftonline.com/<TENANT-ID>/oauth2/logout",
    "response_types_supported": [
        "code",
        "id_token",
        "code id_token",
        "token id_token",
        "token"
    ],
    "scopes_supported": [
        "openid"
    ],
    "issuer": "https://sts.windows.net/<TENANT-ID>/",
    "claims_supported": [
        "sub",
        "iss",
        "cloud_instance_name",
        "cloud_instance_host_name",
        "cloud_graph_host_name",
        "msgraph_host",
        "aud",
        "exp",
        "iat",
        "auth_time",
        "acr",
        "amr",
        "nonce",
        "email",
        "given_name",
        "family_name",
        "nickname"
    ],
    "microsoft_multi_refresh_token": true,
    "check_session_iframe": "https://login.microsoftonline.com/<TENANT-ID>/oauth2/checksession",
    "userinfo_endpoint": "https://login.microsoftonline.com/<TENANT-ID>/openid/userinfo",
    "tenant_region_scope": "WW",
    "cloud_instance_name": "microsoftonline.com",
    "cloud_graph_host_name": "graph.windows.net",
    "msgraph_host": "graph.microsoft.com",
    "rbac_url": "https://pas.windows.net"
}
``` 
Die wichtigen Eigenschaften für den FHIR-Server sind `jwks_uri`, die dem Server angibt, wo die zum Überprüfen der Tokensignatur erforderlichen Verschlüsselungsschlüssel abzurufen sind, und `issuer`, die dem Server mitteilt, was im Ausstelleranspruch (`iss`) der von diesem Server ausgestellten Token enthalten sein soll. Der FHIR-Server kann hiermit überprüfen, ob er ein authentisches Token empfängt.

## <a name="validate-claims-of-the-token"></a>Überprüfen von Ansprüchen des Tokens

Nachdem der Server die Authentizität des Tokens überprüft hat, stellt der FHIR-Server anschließend fest, ob der Client über die erforderlichen Ansprüche zum Zugreifen auf das Token verfügt.

Bei Verwendung von Azure API for FHIR überprüft der Server Folgendes:

1. Das Token weist die richtige `Audience` (`aud`-Anspruch) auf.
1. Der Benutzer oder Prinzipal, für den das Token ausgestellt wurde, ist zum Zugreifen auf die Datenebene des FHIR-Servers berechtigt. Der `oid`-Anspruch des Tokens enthält eine Identitätsobjekt-ID, die den Benutzer oder Prinzipal eindeutig identifiziert.

Es wird empfohlen, den FHIR-Dienst für die [Verwendung von Azure RBAC zu konfigurieren](configure-azure-rbac.md), um die Rollenzuweisungen für die Datenebene zu verwalten. Sie können aber auch die [lokale RBAC konfigurieren](configure-local-rbac.md), wenn Ihr FHIR-Dienst einen externen oder sekundären Azure Active Directory-Mandanten verwendet. 

Bei Verwendung des Microsoft-OSS-FHIR-Servers für Azure überprüft der Server Folgendes:

1. Das Token weist die richtige `Audience` (`aud`-Anspruch) auf.
1. Das Token verfügt über eine Rolle im `roles`-Anspruch, die Zugriff auf den FHIR-Server besitzt.

Sehen Sie sich ausführlichere Informationen zum [Definieren von Rollen auf dem FHIR-Server](https://github.com/microsoft/fhir-server/blob/master/docs/Roles.md) an.

Ein FHIR-Server kann auch überprüfen, ob ein Zugriffstoken über die Bereiche (im Tokenanspruch `scp`) für den Zugriff auf den Teil der FHIR-API verfügt, auf den ein Client zuzugreifen versucht. Derzeit werden von Azure API for FHIR und dem FHIR-Server für Azure keine Tokenbereiche überprüft.

## <a name="next-steps"></a>Nächste Schritte
Nachdem Sie nun wissen, wie die Tokenüberprüfung ausgeführt wird, können Sie das Tutorial zum Erstellen einer JavaScript-Anwendung und Lesen von FHIR-Daten durcharbeiten.

>[!div class="nextstepaction"]
>[Tutorial zur Webanwendung](tutorial-web-app-fhir-server.md)