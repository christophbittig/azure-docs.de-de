---
title: Registrieren des Azure VMware Solution-Ressourcenanbieters
description: Hier sind die Schritte zum Registrieren des Azure VMware Solution-Ressourcenanbieters erläutert.
ms.topic: include
ms.date: 02/17/2021
author: shortpatti
ms.author: v-patsho
ms.service: azure-vmware
ms.openlocfilehash: 8f1074a0fa703afb6878b708861a67b4bc6d4454
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121729074"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-create-private-cloud.md -->

Für die Verwendung von Azure VMware Solution müssen Sie zunächst den Ressourcenanbieter für Ihr Abonnement registrieren. Weitere Informationen zu Ressourcenanbietern finden Sie unter [Azure-Ressourcenanbieter und -typen](../../azure-resource-manager/management/resource-providers-and-types.md).


### <a name="portal"></a>[Portal](#tab/azure-portal)
 
1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Wählen Sie im Menü des Azure-Portals die Option **Alle Dienste** aus.

1. Geben Sie im Feld **Alle Dienste****Abonnement** ein, und wählen Sie dann **Abonnements** aus.

1. Wählen Sie das Abonnement aus der Abonnentenliste aus, um es anzuzeigen.

1. Wählen Sie die Option **Ressourcenanbieter** aus, und geben Sie **Microsoft.AVS** als Suchbegriff ein. 
 
1. Wählen Sie **Registrieren** aus, falls der Ressourcenanbieter nicht registriert ist.

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

So beginnen Sie mit der Verwendung der Azure CLI:

[!INCLUDE [azure-cli-prepare-your-environment-no-header](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

Melden Sie sich über die Azure CLI bei dem Azure-Abonnement an, das Sie für die Azure VMware Solution-Bereitstellung verwenden. Verwenden Sie zum Registrieren des Ressourcenanbieters `Microsoft.AVS` den Befehl [az provider register](/cli/azure/provider#az_provider_register):

```azurecli-interactive
az provider register -n Microsoft.AVS --subscription <your subscription ID>
```

Mit dem Befehl [az provider list](/cli/azure/provider#az_provider_list) zeigen Sie alle verfügbaren Anbieter an.

---


 
