---
title: 'Schnellstart: Erstellen einer verbundenen Registrierung mithilfe der CLI'
description: Verwenden Sie Azure CLI-Befehle, um eine verbundene Azure-Containerregistrierungsressource zu erstellen, die Images und andere Artefakte mit der Cloudregistrierung synchronisieren kann.
ms.topic: quickstart
ms.date: 10/21/2021
ms.author: memladen
author: toddysm
ms.custom: ignite-fall-2021
ms.openlocfilehash: 926d9ca087f509a1aa76dcec235fd7996bce4a78
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131101784"
---
# <a name="quickstart-create-a-connected-registry-using-the-azure-cli"></a>Schnellstart: Erstellen einer verbundenen Registrierung mithilfe der Azure CLI

In dieser Schnellstartanleitung verwenden Sie die Azure CLI, um eine [verbundene Registrierungsressource](intro-connected-registry.md) in Azure zu erstellen. Mit dem Azure Container Registry-Feature für verbundene Registrierungen können Sie eine Registrierung remote oder lokal bereitstellen und Images und andere Artefakte mit der Cloudregistrierung synchronisieren. 

Hier erstellen Sie zwei verbundene Registrierungsressourcen für eine Cloudregistrierung: Eine verbundene Registrierung ermöglicht Lese- und Schreibfunktionen (Pull- und Pushvorgänge für Artefakte), die andere ermöglicht schreibgeschützte Funktionen. 

Nach dem Erstellen der verbundenen Registrierung können Sie in anderen Leitfäden erfahren, wie Sie diese in Ihrer lokalen oder Remoteinfrastruktur bereitstellen und verwenden.

[!INCLUDE [Prepare Azure CLI environment](../../includes/azure-cli-prepare-your-environment.md)]

* Azure Container Registry: Wenn Sie noch nicht über eine Containerregistrierung verfügen, [erstellen Sie eine](container-registry-get-started-azure-cli.md) (Premium-Tarif erforderlich) in einer [Region](intro-connected-registry.md#available-regions), die verbundene Registrierungen unterstützt. 

## <a name="enable-the-dedicated-data-endpoint-for-the-cloud-registry"></a>Aktivieren des dedizierten Datenendpunkts für die Cloudregistrierung

Aktivieren Sie den dedizierten Datenendpunkt für die Azure-Containerregistrierung in der Cloud mithilfe des Befehls [az acr update][az-acr-update]. Dieser Schritt ist erforderlich, damit eine verbundene Registrierung mit der Cloudregistrierung kommunizieren kann.

```azurecli
# Set the REGISTRY_NAME environment variable to identify the existing cloud registry
REGISTRY_NAME=<container-registry-name>

az acr update --name $REGISTRY_NAME \
  --data-endpoint-enabled
```

[!INCLUDE [container-registry-connected-import-images](../../includes/container-registry-connected-import-images.md)]

## <a name="create-a-connected-registry-resource-for-read-and-write-functionality"></a>Erstellen einer verbundenen Registrierungsressource für Lese- und Schreibfunktionen

Erstellen Sie eine verbundene Registrierung mithilfe des Befehls [az acr connected-registry create][az-acr-connected-registry-create]. Der Name der verbundenen Registrierung muss mit einem Buchstaben beginnen und darf nur alphanumerische Zeichen enthalten. Er muss 5 bis 40 Zeichen lang und in der Hierarchie für diese Azure-Containerregistrierung eindeutig sein.

```azurecli
# Set the CONNECTED_REGISTRY_RW environment variable to provide a name for the connected registry with read/write functionality
CONNECTED_REGISTRY_RW=<connnected-registry-name>

az acr connected-registry create --registry $REGISTRY_NAME \
  --name $CONNECTED_REGISTRY_RW \
  --repository "hello-world" "acr/connected-registry" "azureiotedge-agent" "azureiotedge-hub" "azureiotedge-api-proxy"
```

Dieser Befehl erstellt eine verbundene Registrierungsressource, deren Name der Wert von *$CONNECTED_REGISTRY_RW* ist, und verknüpft sie mit der Cloudregistrierung, deren Name der Wert *$REGISTRY_NAME* ist. In späteren Schnellstartanleitungen erfahren Sie mehr über Optionen zum Bereitstellen der verbundenen Registrierung. 
* Die angegebenen Repositorys werden nach der Bereitstellung zwischen der Cloudregistrierung und der verbundenen Registrierung synchronisiert. 
* Da für die verbundene Registrierung keine `--mode`-Option angegeben ist, wird sie im standardmäßigen [ReadWrite-Modus](intro-connected-registry.md#modes) erstellt. 
* Da für diese verbundene Registrierung kein Synchronisierungszeitplan definiert ist, werden die Repositorys ohne Unterbrechungen zwischen der Cloudregistrierung und der verbundenen Registrierung synchronisiert.

  > [!IMPORTANT]
  > Zur Unterstützung von geschachtelten Szenarien, in denen niedrigere Ebenen keinen Internetzugriff haben, müssen Sie die Synchronisierung des Repositorys `acr/connected-registry` immer zulassen. Dieses Repository enthält das Image für die Runtime der verbundenen Registrierung.

## <a name="create-a-connected-registry-resource-for-read-only-functionality"></a>Erstellen einer verbundenen Registrierungsressource für schreibgeschützte Funktionen

Sie können auch den Befehl [az acr connected-registry create][az-acr-connected-registry-create] verwenden, um eine verbundene Registrierung mit schreibgeschützter Funktionalität zu erstellen. 

```azurecli
# Set the CONNECTED_REGISTRY_READ environment variable to provide a name for the connected registry with read-only functionality
CONNECTED_REGISTRY_RO=<connnected-registry-name>
az acr connected-registry create --registry $REGISTRY_NAME \
  --parent $CONNECTED_REGISTRY_RW \
  --name $CONNECTED_REGISTRY_RO \
  --repository "hello-world" "acr/connected-registry" "azureiotedge-agent" "azureiotedge-hub" "azureiotedge-api-proxy" \
  --mode ReadOnly
```

Dieser Befehl erstellt eine verbundene Registrierungsressource, deren Name der Wert von *$CONNECTED_REGISTRY_RO* ist, und verknüpft sie mit der Cloudregistrierung, deren Name der Wert *$REGISTRY_NAME* ist. 
* Die angegebenen Repositorys werden nach der Bereitstellung zwischen der übergeordneten Registrierung, deren Name der Wert *$CONNECTED_REGISTRY_RW* ist, und der verbundenen Registrierung synchronisiert.
* Diese Ressource wird im [ReadOnly-Modus](intro-connected-registry.md#modes) erstellt, der nach der Bereitstellung schreibgeschützte Funktionen (Pullvorgänge für Artefakte) ermöglicht. 
* Da für diese verbundene Registrierung kein Synchronisierungszeitplan definiert ist, werden die Repositorys ohne Unterbrechungen zwischen übergeordneten Registrierung und der verbundenen Registrierung synchronisiert.

## <a name="verify-that-the-resources-are-created"></a>Überprüfen, ob die Ressourcen erstellt wurden

Sie können den Befehl [az acr connected-registry list][az-acr-connected-registry-list] für die verbundene Registrierung verwenden, um zu überprüfen, ob die Ressourcen erstellt wurden. 

```azurecli
az acr connected-registry list \
  --registry $REGISTRY_NAME \
  --output table
```

Es sollte eine Antwort wie die folgende zu sehen sein. Da die verbundenen Registrierungen noch nicht bereitgestellt wurden, gibt der Verbindungsstatus „Offline“ an, dass sie derzeit nicht mit der Cloud verbunden sind.

```
NAME                 MODE        CONNECTION STATE    PARENT               LOGIN SERVER    LAST SYNC (UTC)
-------------------  --------    ------------------  -------------------  --------------  -----------------
myconnectedregrw    ReadWrite    Offline
myconnectedregro    ReadOnly     Offline             myconnectedregrw
```

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie die Azure CLI verwendet, um zwei verbundene Registrierungsressourcen in Azure zu erstellen. Diese neuen verbundenen Registrierungsressourcen sind an Ihre Cloudregistrierung gebunden und ermöglichen die Synchronisierung von Artefakten mit der Cloudregistrierung.

Fahren Sie mit den Leitfäden zur Bereitstellung verbundener Registrierungen fort, um zu erfahren, wie Sie eine verbundene Registrierung in Ihrer IoT Edge-Infrastruktur bereitstellen und verwenden.

> [!div class="nextstepaction"]
> [Schnellstart: Bereitstellen einer verbundenen Registrierung in IoT Edge][quickstart-deploy-connected-registry-iot-edge-cli]

<!-- LINKS - internal -->
[az-acr-connected-registry-create]: /cli/azure/acr/connected-registry#az_acr_connected_registry_create
[az-acr-connected-registry-list]: /cli/azure/acr/connected-registry#az_acr_connected_registry_list
[az-acr-create]: /cli/azure/acr#az_acr_create
[az-acr-update]: /cli/azure/acr#az_acr_update
[az-acr-import]: /cli/azure/acr#az_acr_import
[az-group-create]: /cli/azure/group#az_group_create
[container-registry-intro]: container-registry-intro.md
[container-registry-skus]: container-registry-skus.md
[quickstart-deploy-connected-registry-iot-edge-cli]: quickstart-deploy-connected-registry-iot-edge-cli.md
