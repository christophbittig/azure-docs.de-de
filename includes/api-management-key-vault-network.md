---
author: dlepow
ms.service: api-management
ms.topic: include
ms.date: 01/26/2021
ms.author: danlep
ms.openlocfilehash: 86b5a62d7aaf16009e59637ebe9a967fce1e0358
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128569590"
---
#### <a name="requirements-for-key-vault-firewall"></a>Anforderungen an Key Vault-Firewall

Wenn die [Key Vault-Firewall](../articles/key-vault/general/network-security.md) in Ihrem Schlüsseltresor aktiviert ist, gelten die folgenden zusätzlichen Anforderungen:

* Sie müssen die **systemseitig zugewiesene** verwaltete Identität der API Management-Instanz verwenden, um auf den Schlüsseltresor zuzugreifen.
* Aktivieren Sie in der Key Vault-Firewall die Option **Vertrauenswürdigen Microsoft-Diensten die Umgehung dieser Firewall erlauben?** .

#### <a name="virtual-network-requirements"></a>Anforderungen für virtuelle Netzwerke

Wenn die API Management-Instanz in einem virtuellen Netzwerk bereitgestellt wird, müssen Sie darüber hinaus die folgenden Netzwerkeinstellungen konfigurieren:

* Aktivieren Sie im API Management-Subnetz einen [Dienstendpunkt](../articles/key-vault/general/overview-vnet-service-endpoints.md) für Azure Key Vault.
* Konfigurieren Sie eine Netzwerksicherheitsgruppen-Regel (NSG), um ausgehenden Datenverkehr an die [Diensttags](../articles/virtual-network/service-tags-overview.md) AzureKeyVault und AzureActiveDirectory zuzulassen. 

Weitere Informationen zur Netzwerkkonfiguration finden Sie unter [Herstellen einer Verbindung mit einem virtuellen Netzwerk](../articles/api-management/api-management-using-with-vnet.md#network-configuration-issues).