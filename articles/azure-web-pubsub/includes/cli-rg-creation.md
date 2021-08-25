---
author: vicancy
ms.service: azure-web-pubsub
ms.topic: include
ms.date: 08/06/2021
ms.author: lianwei
ms.openlocfilehash: c2f05a81e52edc858bc13bc016e030236241a6f0
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121783495"
---
Eine Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden. Verwenden Sie den Befehl [az group create](/cli/azure/group#az_group_create), um eine Ressourcengruppe mit dem Namen *myResourceGroup* am Standort *eastus* zu erstellen.

```azurecli-interactive
az group create --name "myResourceGroup" -l "EastUS"
```
