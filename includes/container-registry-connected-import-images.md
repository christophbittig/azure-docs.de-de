---
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 09/16/2021
ms.author: danlep
ms.openlocfilehash: a5bfee2b0348a68476855edc2b113338b779f20d
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131029700"
---
## <a name="import-images-to-your-cloud-registry"></a>Importieren von Images in die Cloudregistrierung

Importieren Sie mit dem Befehl [az acr import](/cli/azure/acr#az_acr_import) die folgenden Containerimages in Ihre Cloudregistrierung. Überspringen Sie diesen Schritt, wenn Sie diese Images bereits importiert haben.

### <a name="connected-registry-image"></a>Image der verbundenen Registrierung

Um geschachtelte IoT Edge-Szenarien zu unterstützen, muss das Containerimage der Runtime der verbundenen Registrierung in Ihrer privaten Azure-Containerregistrierung verfügbar sein. Mit dem Befehl [az acr import](/cli/azure/acr#az_acr_import) können Sie das Image der verbundenen Registrierung in Ihre private Registrierung importieren. 

```azurecli
# Use the REGISTRY_NAME variable in the following Azure CLI commands to identify the registry
REGISTRY_NAME=<container-registry-name>

az acr import \
  --name $REGISTRY_NAME \
  --source mcr.microsoft.com/acr/connected-registry:0.5.0
```

### <a name="iot-edge-and-api-proxy-images"></a>Images von IoT Edge- und API-Proxy

Um die verbundene Registrierung für geschachteltes IoT Edge zu unterstützen, müssen Sie Module für den IoT Edge- und API-Proxy bereitstellen. Importieren Sie diese Images in Ihre private Registrierung.

Das [IoT Edge- und API-Proxymodul](../articles/iot-edge/how-to-configure-api-proxy-module.md) ermöglicht einem IoT Edge Gerät, mehrere Dienste über das HTTPS-Protokoll am selben Port verfügbar zu machen, z. B. 443.

```azurecli
az acr import \
  --name $REGISTRY_NAME \
  --source mcr.microsoft.com/azureiotedge-agent:1.2.4

az acr import \
  --name $REGISTRY_NAME \
  --source mcr.microsoft.com/azureiotedge-hub:1.2.4

az acr import \
  --name $REGISTRY_NAME \
  --source mcr.microsoft.com/azureiotedge-api-proxy:1.1.2

az acr import \
  --name $REGISTRY_NAME \
  --source mcr.microsoft.com/azureiotedge-diagnostics:1.2.4
```

### <a name="hello-world-image"></a>Image „hello-world“

Importieren Sie das Image, um die verbundene Registrierung `hello-world` zu testen. Dieses Repository wird mit der verbundenen Registrierung synchronisiert und von den verbundenen Registrierungsclients gepullt.

```azurecli
az acr import \
  --name $REGISTRY_NAME \
  --source mcr.microsoft.com/hello-world:1.1.2
```