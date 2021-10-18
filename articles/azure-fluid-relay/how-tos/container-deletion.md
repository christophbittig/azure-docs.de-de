---
author: trngo
description: Erfahren Sie, wie Sie einzelne Container mit az-cli löschen können
title: Löschen von Fluid-Containern
ms.author: trngo
ms.date: 09/28/2021
ms.service: azure-fluid
ms.topic: reference
ms.openlocfilehash: 8a94c06bd80b6997c5bd80cbb147bf68ccf91a4b
ms.sourcegitcommit: 216b6c593baa354b36b6f20a67b87956d2231c4c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/11/2021
ms.locfileid: "129728663"
---
# <a name="delete-fluid-containers-in-microsoft-azure-fluid-relay-server"></a>Löschen von Fluid-Containern in Microsoft Azure Fluid Relay Server

> [!NOTE]
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.

In diesem Szenario wird ein vorhandener Fluid-Container gelöscht. Sobald ein Container gelöscht wird, können Anwendungen, die auf den Container verweisen, nicht mehr auf den Container oder seine Daten zugreifen. 

## <a name="requirements-to-delete-a-fluid-container"></a>Voraussetzungen für das Löschen eines Fluid-Containers
- Zunächst müssen Sie die [Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli) (Azure CLI) installieren. Wenn Sie Azure CLI bereits installiert haben, stellen Sie bitte sicher, dass Ihre Version 2.0.67 oder höher ist, indem Sie `az version` ausführen.
- Um einen Fluid-Container zu löschen, müssen Sie sicherstellen, dass Ihre Anwendung und ihre Clients nicht mehr mit dem Container verbunden sind.

## <a name="list-the-containers-within-a-fluid-relay-resource"></a>Auflisten der Container innerhalb einer Fluid-Relay-Ressource
Um alle zu Ihrer Fluid Relay-Ressource gehörenden Container zu sehen, können Sie den folgenden Befehl ausführen:
```
az rest --method get --uri https://management.azure.com/subscriptions/<subscriptionId>/resourcegroups/<resourceGroupName>/providers/Microsoft.FluidRelay/FluidRelayServers/<frsResourceName>/FluidRelayContainers?api-version=<apiVersion>
```
**subscriptionId**: ID des Azure-Abonnements, zu dem Ihre Fluid Relay-Ressource gehört.

**resourceGroupName**: Name der Ressourcengruppe, in der sich Ihre Fluid Relay-Ressource befindet.

**frsResourceName**: Name Ihrer Fluid Relay-Ressource. Beachten Sie, dass sich dies von der tenantId der Fluid Relay-Ressource unterscheidet.

**apiVersion**: API-Version des Ressourcenanbieters. Die minimal unterstützte Version ist **2021-08-30-preview**.  


## <a name="sample-output"></a>Beispielausgabe
Die Ausgabe enthält eine Liste der Container, die zu Ihrer Fluid Relay-Ressource gehören, sowie deren Eigenschaften.
```json
{
  "value": [
    {
      "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupname>/providers/Microsoft.FluidRelay/fluidRelayServers/<frsResourcename>/fluidRelayContainers/<containerId>",
      "name": "<containerId>",
      "properties": {
        "frsContainerId": "<containerId>",
        "frsTenantId": "<frsResourceTenantId>"
      },
      "resourceGroup": "<resourceGroupname>",
      "type": "Microsoft.FluidRelay/fluidRelayServers/fluidRelayContainers"
    },
    ...
  ]
}
```


## <a name="delete-an-existing-container"></a>Einen bestehenden Container löschen
Um einen Container zu löschen, müssen Sie die **containerId** des Containers in der obigen Ausgabe identifizieren und den folgenden Befehl ausführen: 
```
az rest --method delete --uri https://management.azure.com/subscriptions/<subscriptionId>/resourcegroups/<resourceGroupName>/providers/Microsoft.FluidRelay/FluidRelayServers/<frsResourceName>/FluidRelayContainers/<frsContainerId>?api-version=<api-version>
```
  **frsContainerId**: ID des zu löschenden Fluid-Containers. 
