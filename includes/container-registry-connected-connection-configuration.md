---
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 10/07/2021
ms.author: danlep
ms.openlocfilehash: eb85c0df13a90afb08a3730ff58457f9897650ae
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131100313"
---
Die Befehlsausgabe umfasst die Registrierungsverbindungszeichenfolge und die zugehörigen Einstellungen. Die folgende Beispielausgabe zeigt die Verbindungszeichenfolge für die verbundene Registrierung namens *myconnectedregistry* mit der übergeordneten Registrierung *contosoregistry*:

```json
{
  "ACR_REGISTRY_CONNECTION_STRING": "ConnectedRegistryName=myconnectedregistry;SyncTokenName=myconnectedregistry-sync-token;SyncTokenPassword=xxxxxxxxxxxxxxxx;ParentGatewayEndpoint=contosoregistry.eastus.data.azurecr.io;ParentEndpointProtocol=https"
}
```
