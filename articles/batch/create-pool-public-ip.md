---
title: Erstellen eines Pools mit angegebenen öffentlichen IP-Adressen
description: Erfahren Sie, wie Sie einen Batch-Pool erstellen, der Ihre eigenen öffentlichen IP-Adressen verwendet.
ms.topic: how-to
ms.date: 10/08/2020
ms.openlocfilehash: fc652a7f4719d5cc23e84caaef63ec9d1c5f7ff1
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130255696"
---
# <a name="create-an-azure-batch-pool-with-specified-public-ip-addresses"></a>Erstellen eines Azure Batch-Pools mit angegebenen öffentlichen IP-Adressen

Beim Erstellen eines Azure Batch-Pools können Sie einen Pool in einem von Ihnen angegebenen Subnetz eines [virtuellen Azure-Netzwerks](batch-virtual-network.md) (VNET) bereitstellen. Auf virtuelle Computer im Batch-Pool wird über öffentliche IP-Adressen zugegriffen, die von Batch erstellt werden. Diese öffentlichen IP-Adressen können sich während der Nutzungsdauer des Pools ändern, was bedeutet, dass Ihre Netzwerkeinstellungen veraltet sein können, wenn die IP-Adressen nicht aktualisiert werden.

Sie können eine Liste mit statischen öffentlichen IP-Adressen erstellen, die Sie mit den virtuellen Computer in Ihrem Pool verwenden können. Dies ermöglicht Ihnen, die Liste der öffentlichen IP-Adressen zu kontrollieren und sicherzustellen, dass sie nicht unerwartet geändert werden. Dies kann besonders nützlich sein, wenn Sie mit einem externen Dienst, wie z. B. einer Datenbank, arbeiten, der den Zugriff auf bestimmte IP-Adressen einschränkt.

Weitere Informationen zum Erstellen von Pools ohne öffentliche IP-Adressen finden Sie unter [Erstellen eines Azure Batch Pools ohne öffentliche IP-Adressen](./batch-pool-no-public-ip-address.md).

## <a name="prerequisites"></a>Voraussetzungen

- **Authentifizierung**. Zur Verwendung einer öffentlichen IP-Adresse muss die Batch-Client-API die [Azure Active Directory-Authentifizierung](batch-aad-auth.md) verwenden.

- **Ein Azure VNET** Sie müssen ein [virtuelles Netzwerk](batch-virtual-network.md) aus demselben Azure-Abonnement verwenden, in dem Sie Ihren Pool und Ihre IP-Adressen erstellen. Nur Azure Resource Manager-basierte VNETs dürfen verwendet werden. Stellen Sie sicher, dass das VNET alle [allgemeinen Anforderungen](batch-virtual-network.md#vnet-requirements) erfüllt.

- **Mindestens eine öffentliche Azure-IP-Adresse**. Zum Erstellen von mindestens einer öffentlichen IP-Adresse können Sie das [Azure-Portal](../virtual-network/ip-services/virtual-network-public-ip-address.md#create-a-public-ip-address), die [Azure CLI](/cli/azure/network/public-ip#az_network_public_ip_create) (Befehlszeilenschnittstelle) oder [Azure PowerShell](/powershell/module/az.network/new-azpublicipaddress) verwenden. Achten Sie darauf, dass Sie die unten aufgeführten Anforderungen befolgen.

> [!NOTE]
> Batch ordnet in der Ressourcengruppe, die die öffentliche IP-Adresse enthält, automatisch zusätzliche Netzwerkressourcen zu. Für jeweils 100 dedizierte Knoten weist Batch in der Regel eine Netzwerksicherheitsgruppe (NSG) und einen Lastenausgleich zu. Diese Ressourcen werden durch die Ressourcenkontingente des Abonnements beschränkt. Bei umfangreichen Pools muss ggf. eine [Kontingenterhöhung](batch-quota-limit.md#increase-a-quota) für eine oder mehrere der Ressourcen angefordert werden.

## <a name="public-ip-address-requirements"></a>Anforderungen an die öffentliche IP-Adresse

Beachten Sie beim Erstellen Ihrer öffentlichen IP-Adressen die folgenden Anforderungen:

- Die öffentliche IP-Adresse muss sich im gleichen Abonnement und in der gleichen Region befinden wie das für die Poolerstellung verwendete Batch-Konto.
- Der **IP-Adresszuweisung** muss auf **Statisch** festgelegt werden.
- **SKU** muss auf **Standard** festgelegt sein.
- Ein DNS-Name muss angegeben werden.
- Die öffentlichen IP-Adressen dürfen nur für die Konfigurationspools der virtuellen Computer verwendet werden. Keine anderen Ressourcen sollten diese IP-Adressen verwenden, oder für den Pool können Zuordnungsfehler auftreten.
- Der Zugriff eines Benutzers auf die öffentliche IP-Adresse sollte durch keine Sicherheitsrichtlinien oder Ressourcensperren eingeschränkt werden.
- Die Anzahl der für den Pool angegebenen öffentlichen IP-Adressen muss groß genug sein, um die Anzahl der VMs für den Pool aufzunehmen. Dies muss mindestens die Summe der Eigenschaften  **targetDedicatedNodes**  und  **targetLowPriorityNodes**  des Pools sein. Wenn nicht genügend IP-Adressen vorhanden sind, weist der Pool die Computeknoten teilweise zu, und es kommt zu einem Anpassungsfehler. Derzeit verwendet Batch eine öffentliche IP-Adresse für jeweils 100 VMs.
- Sie sollten immer über einen zusätzlichen Puffer von öffentlichen IP-Adressen verfügen. Es wird empfohlen, mindestens eine zusätzliche öffentliche IP-Adresse hinzuzufügen, oder etwa 10 % der gesamten öffentlichen IP-Adressen, die Sie einem Pool hinzufügen, je nachdem, welcher Wert größer ist. Dieser zusätzliche Puffer hilft Batch bei der internen Optimierung beim Herunterskalieren und ermöglicht ein schnelleres Hochskalieren nach einem erfolglosen Hoch- oder Herunterskalieren.
- Sobald der Pool erstellt ist, können Sie die Liste der vom Pool verwendeten öffentlichen IP-Adressen nicht mehr hinzufügen oder ändern. Wenn Sie die Liste ändern möchten, müssen Sie den Pool löschen und anschließend neu erstellen.

## <a name="create-a-batch-pool-with-public-ip-addresses"></a>Erstellen eines Batch-Pools mit öffentlichen IP-Adressen

Das folgende Beispiel zeigt, wie die [Azure Batch-Dienste-REST-API](/rest/api/batchservice/pool/add) verwendet wird, um einen Pool zu erstellen, der öffentliche IP-Adressen verwendet.

### <a name="batch-service-rest-api"></a>Batch-Dienste-REST-API

REST-API-URI

```http
POST {batchURL}/pools?api-version=2020-03-01.11.0
client-request-id: 00000000-0000-0000-0000-000000000000
```

Anforderungstext

```json
"pool": {
      "id": "pool2",
      "vmSize": "standard_a1",
      "virtualMachineConfiguration": {
        "imageReference": {
          "publisher": "Canonical",
          "offer": "UbuntuServer",
          "sku": "16.04.0-LTS"
        },
        "nodeAgentSKUId": "batch.node.ubuntu 16.04"
      },
"networkConfiguration": {
          "subnetId": "/subscriptions/<subId>/resourceGroups/<rgId>/providers/Microsoft.Network/virtualNetworks/<vNetId>/subnets/<subnetId>",
          "publicIPAddressConfiguration": {
            "provision": "usermanaged",
            "ipAddressIds": [
              "/subscriptions/<subId>/resourceGroups/<rgId>/providers/Microsoft.Network/publicIPAddresses/<publicIpId>"
          ]
        },

       "resizeTimeout":"PT15M",
      "targetDedicatedNodes":5,
      "targetLowPriorityNodes":0,
      "taskSlotsPerNode":3,
      "taskSchedulingPolicy": {
        "nodeFillType":"spread"
      },
      "enableAutoScale":false,
      "enableInterNodeCommunication":true,
      "metadata": [ {
        "name":"myproperty",
        "value":"myvalue"
      } ]
    }
```

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über den [Workflow des Batch-Diensts und primäre Ressourcen](batch-service-workflow-features.md) wie Pools, Knoten, Aufträge und Aufgaben.
- Erfahren Sie mehr über das [Erstellen eines Pools in einem Subnetz eines virtuellen Azure-Netzwerks](batch-virtual-network.md).
- Erfahren Sie mehr über das [Erstellen eines Azure Batch-Pools ohne öffentliche IP-Adressen](./batch-pool-no-public-ip-address.md).