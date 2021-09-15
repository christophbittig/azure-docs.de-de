---
author: vladvino
ms.service: api-management
ms.topic: include
ms.date: 08/23/2021
ms.author: vlvinogr
ms.openlocfilehash: 4f75dc57d6f94983b390d45de657029dd157cf64
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/04/2021
ms.locfileid: "123485068"
---
## <a name="how-api-management-proxy-server-responds-with-ssl-certificates-in-the-tls-handshake"></a>Antworten des API Management-Proxyservers mit SSL-Zertifikaten beim TLS-Handshake

### <a name="clients-calling-with-server-name-indication-sni-header"></a>Aufrufen von Clients mit dem SNI-Header (Server Name Indication, Servernamensanzeige)
Wenn Sie eine oder mehrere benutzerdefinierte Domänen für den Proxy (Gateway) konfiguriert haben, kann API Management auf HTTPS-Anforderungen von beiden antworten:
* Benutzerdefinierte Domäne (z. B. `contoso.com`)
* Standarddomäne (z. B. `apim-service-name.azure-api.net`) 

Basierend auf den Informationen im SNI-Header antwortet API Management mit dem entsprechenden Serverzertifikat.

### <a name="clients-calling-without-sni-header"></a>Aufrufen von Clients ohne den SNI-Header
Wenn Sie einen Client verwenden, der den [SNI](https://tools.ietf.org/html/rfc6066#section-3)-Header nicht sendet, erstellt API Management Antworten gemäß der folgenden Logik:

* **Wenn der Dienst nur eine für den Proxy konfigurierte benutzerdefinierte Domäne enthält**, ist das Standardzertifikat das Zertifikat, das für die benutzerdefinierte Proxydomäne ausgestellt wurde.
* **Wenn der Dienst mehrere benutzerdefinierte Domänen für den Proxy konfiguriert hat (Unterstützung in den Tarifen **Developer** und **Premium**)** , können Sie das Standardzertifikat definieren, indem Sie die Eigenschaft [defaultSslBinding](/rest/api/apimanagement/2020-12-01/api-management-service/create-or-update#hostnameconfiguration) auf „true“ festlegen ("defaultSslBinding":"true"). Wenn Sie die Eigenschaft nicht festlegen, ist das Standardzertifikat das Zertifikat, das für die unter `*.azure-api.net` gehostete Proxystandarddomäne ausgestellt wird.

## <a name="support-for-putpost-request-with-large-payload"></a>Unterstützung für PUT/POST-Anforderung mit großer Nutzlast

Der API Management-Proxyserver unterstützt Anforderungen mit großer Nutzlast (> 40 KB) bei der Verwendung von clientseitigen Zertifikaten in HTTPS. Um zu verhindern, dass die Serveranforderung blockiert wird, können Sie die Eigenschaft ["negotiateClientCertificate": "true"](/rest/api/apimanagement/2020-12-01/api-management-service/create-or-update#hostnameconfiguration) für den Proxyhostnamen festlegen. 

Wenn die Eigenschaft auf „true“ festgelegt ist, wird vor jedem Austausch von HTTP-Anforderungen zum Zeitpunkt der SSL/TLS-Verbindung das Clientzertifikat angefordert. Da die Einstellung für die Ebene **Proxyhostname** gilt, wird bei allen Verbindungsanforderungen das Clientzertifikat angefordert. Sie können diese Einschränkung umgehen und bis zu 20 benutzerdefinierte Domänen für den Proxy konfigurieren (nur im **Premium**-Tarif unterstützt).
