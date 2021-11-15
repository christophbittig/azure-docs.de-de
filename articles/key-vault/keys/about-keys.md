---
title: 'Informationen zu Schlüsseln: Azure Key Vault'
description: Hier finden Sie eine Übersicht über die Azure Key Vault-REST-Schnittstelle sowie Informationen für Entwickler zu Schlüsseln.
services: key-vault
author: mbaldwin
manager: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: overview
ms.date: 02/17/2021
ms.author: mbaldwin
ms.openlocfilehash: d89cde13500757ae38ceaba66044b10225508cb9
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131460904"
---
# <a name="about-keys"></a>Informationen zu Schlüsseln

Azure Key Vault bietet zwei Arten von Ressourcen für die Speicherung und Verwaltung kryptografischer Schlüssel. Vault-Instanzen unterstützen sowohl durch Software als auch durch HSM (Hardware Security Module, Hardwaresicherheitsmodul) geschützte Schlüssel. Verwaltete HSMs unterstützen nur durch HSM geschützte Schlüssel. 

|Ressourcentyp|Schlüsselschutzmethoden|Basis-URL des Datenebenenendpunkts|
|--|--|--|
| **Tresore** | Softwaregeschützt<br/><br/>und<br/><br/>Durch HSM geschützt (mit Premium-SKU)</li></ul> | https://{Tresorname}.vault.azure.net |
| **Verwaltete HSMs** | Durch HSM geschützt | https://{HSM-Name}.managedhsm.azure.net |
||||

- **Tresore:** Tresore bieten eine kostengünstige, leicht bereitzustellende, mehrinstanzenfähige, zonenresiliente (sofern verfügbar) und hochverfügbare Schlüsselverwaltungslösung, die für die meisten gängigen Cloudanwendungsszenarien geeignet ist.
- **Verwaltete HSMs:** Verwaltetes HSM bietet einzelinstanzfähige, zonenresiliente (sofern verfügbar) und hochverfügbare HSMs zum Speichern und Verwalten Ihrer kryptografischen Schlüssel. Diese Lösung eignet sich am besten für Anwendungen und Verwendungsszenarien, in denen Schlüsseln mit hohem Wert verwendet werden. Darüber hinaus ermöglicht sie die Erfüllung besonders strenger Sicherheits- und Complianceanforderungen sowie entsprechender gesetzlicher Anforderungen. 

> [!NOTE]
> Mit Tresoren können neben kryptografischen Schlüsseln auch verschiedene Arten von Objekten wie etwa Geheimnisse, Zertifikate und Speicherkontoschlüssel gespeichert und verwaltet werden.

Kryptografische Schlüssel in Key Vault werden als JSON Web Key-Objekte (JWK) dargestellt. Die Spezifikationen von JavaScript Object Notation (JSON) und JavaScript Object Signing and Encryption (JOSE) lauten wie folgt:

-   [JSON Web Key (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key)  
-   [JSON Web Encryption (JWE)](https://datatracker.ietf.org/doc/html/draft-jones-json-web-encryption)  
-   [JSON Web Algorithms (JWA)](https://datatracker.ietf.org/doc/html/draft-ietf-jose-json-web-algorithms)  
-   [JSON Web Signature (JWS)](https://tools.ietf.org/html/draft-ietf-jose-json-web-signature) 

Die grundlegenden JWK/JWA-Spezifikationen wurden erweitert, um Schlüsseltypen zu ermöglichen, die speziell für die Implementierung von Azure Key Vault und verwalteten HSMs verwendet werden. 

Durch HSM geschützte Schlüssel (auch HSM-Schlüssel genannt) werden in einem HSM (Hardwaresicherheitsmodul) verarbeitet und verlassen nie die HSM-Schutzgrenze. 

- Von Tresoren werden **FIPS 140-2 Level 2**-zertifizierte HSMs verwendet, um HSM-Schlüssel in einer gemeinsam genutzten HSM-Back-End-Infrastruktur zu schützen. 
- Verwaltete HSMs verwenden durch **FIPS 140-2 Level 3** überprüfte HSMs zum Schützen Ihrer Schlüssel. Jeder HSM-Pool ist eine isolierte Einzelmandanteninstanz mit eigener [Sicherheitsdomäne](../managed-hsm/security-domain.md), die vollständige kryptografische Isolation von allen anderen HSMs bietet, die die gleiche Hardwareinfrastruktur nutzen.

Diese Schlüssel werden in HSM-Pools mit einem einzelnen Mandanten geschützt. Sie können einen RSA-Schlüssel, einen EC-Schlüssel und einen symmetrischen Schlüssel importieren – in Soft-Form oder durch Exportieren von einem kompatiblen HSM-Gerät. Außerdem können Sie Schlüssel in HSM-Pools generieren. Wenn Sie HSM-Schlüssel unter Verwendung der in der [BYOK-Spezifikation (Bring Your Own Key)](../keys/byok-specification.md) beschriebenen Methode importieren, ermöglicht dies den sicheren Transport von Schlüsselmaterial in Pools verwalteter HSMs. 

Weitere Informationen zu geografischen Grenzen finden Sie unter [Datenschutz](https://azure.microsoft.com/support/trust-center/privacy/).

## <a name="key-types-and-protection-methods"></a>Schlüsseltypen und Schutzmethoden

Key Vault unterstützt RSA- und EC-Schlüssel. Verwaltete HSMs unterstützen RSA-Schlüssel, EC-Schlüssel und symmetrische Schlüssel. 

### <a name="hsm-protected-keys"></a>HSM-geschützte Schlüssel

|Schlüsseltyp|Tresore (nur Premium-SKU)|Verwaltete HSMs|
|--|--|--|
|**EC-HSM**: Elliptic Curve-Schlüssel | Unterstützt (P-256, P-384, P-521, P-256K)| Unterstützt (P-256, P-256K, P-384, P-521)|
|**RSA-HSM**: RSA-Schlüssel|Unterstützt (2048 Bit, 3072 Bit, 4096 Bit)|Unterstützt (2048 Bit, 3072 Bit, 4096 Bit)|
|**oct-HSM**: symmetrischer Schlüssel|Nicht unterstützt|Unterstützt (128 Bit, 192 Bit, 256 Bit)|
|||

### <a name="software-protected-keys"></a>Softwaregeschützte Schlüssel

|Schlüsseltyp|Tresore|Verwaltete HSMs|
|--|--|--|
**RSA**: Softwaregeschützter RSA-Schlüssel|Unterstützt (2048 Bit, 3072 Bit, 4096 Bit)|Nicht unterstützt
**EC**: Softwaregeschützter Elliptic Curve-Schlüssel. |Unterstützt (P-256, P-384, P-521, P-256K)|Nicht unterstützt
|||

### <a name="compliance"></a>Kompatibilität

|Schlüsseltyp und -ziel|Kompatibilität|
|---|---|
|Softwaregeschützte Schlüssel in Tresoren (Premium- und Standard-SKUs) | FIPS 140-2 Level 1|
|Durch HSM geschützte Schlüssel in Tresoren (Premium-SKU)| FIPS 140-2 Level 2|
|Durch HSM geschützte Schlüssel in verwalteten HSMs|FIPS 140-2 Level 3|
|||

Ausführliche Informationen zu den einzelnen Schlüsseltypen, Algorithmen, Vorgängen, Attributen und Tags finden Sie unter [Schlüsseltypen, Algorithmen und Vorgänge](about-keys-details.md).

## <a name="usage-scenarios"></a>Verwendungsszenarien

| Verwendung | Beispiele |
|--------------|-------------|
| Serverseitige Azure-Datenverschlüsselung für integrierte Ressourcenanbieter mit kundenseitig verwalteten Schlüsseln | - [Serverseitige Verschlüsselung mit vom Kunden verwalteten Schlüsseln in Azure Key Vault](https://docs.microsoft.com/azure/security/fundamentals/encryption-models#server-side-encryption-using-customer-managed-keys-in-azure-key-vault) |
| Clientseitige Datenverschlüsselung | - [Clientseitige Verschlüsselung und Azure Key Vault für Microsoft Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption?tabs=dotnet)|
| Schlüsselloses TLS | - Verwenden Sie [Clientbibliotheken](../general/client-libraries.md#client-libraries-per-language-and-object) für Schlüssel. |

## <a name="next-steps"></a>Nächste Schritte
- [Informationen zu Key Vault](../general/overview.md)
- [Informationen zu verwaltetem HSM](../managed-hsm/overview.md)
- [Informationen zu Geheimnissen](../secrets/about-secrets.md)
- [Informationen zu Zertifikaten](../certificates/about-certificates.md)
- [Übersicht über die Key Vault-REST-API](../general/about-keys-secrets-certificates.md)
- [Authentifizierung, Anforderungen und Antworten](../general/authentication-requests-and-responses.md)
- [Entwicklerhandbuch für Key Vault](../general/developers-guide.md)
