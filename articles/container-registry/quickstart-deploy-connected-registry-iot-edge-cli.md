---
title: 'Schnellstart: Bereitstellen einer verbundenen Registrierung auf einem IoT Edge-Gerät'
description: Verwenden Sie Azure CLI-Befehle und das Azure-Portal, um eine verbundene Azure Container Registry auf einem Azure IoT Edge-Gerät bereitzustellen.
ms.topic: quickstart
ms.date: 10/21/2021
ms.author: memladen
author: toddysm
ms.custom: ignite-fall-2021
ms.openlocfilehash: e7d7ac92bda1aade5a446e6b0fe2a2b9966d5eb9
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131100314"
---
# <a name="quickstart-deploy-a-connected-registry-to-an-iot-edge-device"></a>Schnellstart: Bereitstellen einer verbundenen Registrierung auf einem IoT Edge-Gerät

In dieser Schnellstartanleitung verwenden Sie die Azure CLI, um eine [verbundene Registrierung](intro-connected-registry.md) als Modul auf einem Azure IoT Edge-Gerät bereitzustellen. Das IoT Edge-Gerät kann auf die übergeordnete Azure Container Registry in der Cloud zugreifen.

Eine Übersicht über die Verwendung einer verbundenen Registrierung mit IoT Edge finden Sie unter [Verwenden einer verbundenen Registrierung mit Azure IoT Edge](overview-connected-registry-and-iot-edge.md). Dieses Szenario entspricht einem Gerät auf der [obersten Ebene](overview-connected-registry-and-iot-edge.md#top-layer) einer IoT Edge-Hierarchie. 


[!INCLUDE [Prepare Azure CLI environment](../../includes/azure-cli-prepare-your-environment.md)]
* Azure IoT Hub- und IoT Edge-Gerät Die Bereitstellungsschritte finden Sie unter [Schnellstart: Bereitstellen Ihres ersten IoT Edge-Moduls auf einem virtuellen Linux-Gerät](../iot-edge/quickstart-linux.md).
  > [!IMPORTANT]
  > Stellen Sie sicher, dass Sie die Ports 8000, 5671 und 8883 auf dem Gerät öffnen, um später auf die Module zugreifen zu können, die auf dem IoT Edge-Gerät bereitgestellt werden. Die Konfigurationsschritte finden Sie unter [Öffnen von Ports zu einem virtuellen Computer mit dem Azure-Portal](../virtual-machines/windows/nsg-quickstart-portal.md). 

* Verbundene Registrierungsressource in Azure. Informationen zu den Bereitstellungsschritten finden Sie unter Schnellstarts mit der [Azure CLI][quickstart-connected-registry-cli] oder dem [Azure Portal][quickstart-connected-registry-portal]. 

    * In diesem Szenario kann eine verbundene Registrierung im Modus `ReadWrite` oder `ReadOnly` verwendet werden. 
    * In den Befehlen in diesem Artikel ist der Name der verbundenen Registrierung in der Umgebungsvariablen *CONNECTED_REGISTRY_RW* gespeichert.

[!INCLUDE [container-registry-connected-import-images](../../includes/container-registry-connected-import-images.md)]

## <a name="retrieve-connected-registry-configuration"></a>Abrufen der verbundenen Registrierungskonfiguration

Bevor Sie die verbundene Registrierung auf dem IoT Edge-Gerät bereitstellen, müssen Sie die Konfigurationseinstellungen aus der verbundenen Registrierungsressource in Azure abrufen.

Verwenden Sie den Befehl [az acr connected-registry get-settings][az-acr-connected-registry-get-settings], um die Einstellungsinformationen abzurufen, die zum Installieren einer verbundenen Registrierung erforderlich sind. Im folgenden Beispiel wird HTTPS als übergeordnetes Protokoll angegeben. Dieses Protokoll ist erforderlich, wenn die übergeordnete Registrierung eine Cloudregistrierung ist.

```azurecli
az acr connected-registry get-settings \
  --registry $REGISTRY_NAME \
  --name $CONNECTED_REGISTRY_RW \
  --parent-protocol https
```

Standardmäßig enthalten die Einstellungsinformationen nicht das [Sync-Token](overview-connected-registry-access.md#sync-token)-Kennwort, das ebenfalls für die Bereitstellung der verbundenen Registrierung benötigt wird. Optional können Sie eines der Kennwörter generieren, indem Sie den Parameter `--generate-password 1` oder `generate-password 2` übergeben. Speichern Sie das generierte Kennwort an einem sicheren Ort. Es kann nicht wieder abgerufen werden.

> [!WARNING]
> Durch die erneute Generierung eines Kennworts werden die Sync-Token-Anmeldeinformationen rotiert. Wenn Sie ein Gerät unter Verwendung des vorherigen Kennworts konfiguriert haben, müssen Sie die Konfiguration aktualisieren.

[!INCLUDE [container-registry-connected-connection-configuration](../../includes/container-registry-connected-connection-configuration.md)]

## <a name="configure-a-deployment-manifest-for-iot-edge"></a>Konfigurieren eines Bereitstellungsmanifests für IoT Edge

Ein Bereitstellungsmanifest ist ein JSON-Dokument, das beschreibt, welche Module auf dem IoT Edge-Gerät bereitgestellt werden sollen. Weitere Informationen finden Sie unter [Verstehen, wie IoT Edge-Module verwendet, konfiguriert und wiederverwendet werden können](../iot-edge/module-composition.md).

Um die verbundenen Registrierungs- und API-Proxymodule mithilfe der Azure CLI bereitzustellen, speichern Sie das folgende Bereitstellungsmanifest lokal als `manifest.json`-Datei. Sie verwenden den Dateipfad im nächsten Abschnitt, wenn Sie den Befehl zum Anwenden der Konfiguration auf Ihr Gerät ausführen.

[!INCLUDE [container-registry-connected-iot-edge-manifest](../../includes/container-registry-connected-iot-edge-manifest.md)]

## <a name="deploy-the-connected-registry-and-api-proxy-modules-on-iot-edge"></a>Bereitstellen der verbundenen Registrierungs- und API-Proxymodule auf IoT Edge

Verwenden Sie den folgenden Befehl, um die verbundenen Registrierungs- und API-Proxymodule auf dem IoT Edge-Gerät bereitzustellen, indem Sie das im vorherigen Abschnitt erstellte Bereitstellungsmanifest verwenden. Stellen Sie die ID des IoT Edge-Geräts der obersten Ebene und den IoT Hub-Namen bereit, sofern angegeben.

```azurecli
# Set the IOT_EDGE_TOP_LAYER_DEVICE_ID and IOT_HUB_NAME environment variables for use in the following Azure CLI command
IOT_EDGE_TOP_LAYER_DEVICE_ID=<device-id>
IOT_HUB_NAME=<hub-name>

az iot edge set-modules \
  --device-id $IOT_EDGE_TOP_LAYER_DEVICE_ID \
  --hub-name $IOT_HUB_NAME \
  --content manifest.json
```

Einzelheiten finden Sie unter [Bereitstellen von Azure IoT Edge-Modulen mit der Azure CLI](../iot-edge/how-to-deploy-modules-cli.md).

Um den Status der verbundenen Registrierung zu überprüfen, verwenden Sie den folgenden [az acr connected-registry show][az-acr-connected-registry-show]-Befehl: Der Name der verbundenen Registrierung ist der Wert von *$CONNECTED_REGISTRY_RW*.

```azurecli
az acr connected-registry show \
  --registry $REGISTRY_NAME \
  --name $CONNECTED_REGISTRY_RW \
  --output table
```

Nach erfolgreicher Bereitstellung weist die verbundene Registrierung den Status `Online` auf.

## <a name="next-steps"></a>Nächste Schritte

In diesem Schnellstart haben Sie gelernt, wie Sie eine verbundene Registrierung für ein IoT Edge-Gerät bereitstellen. Fahren Sie mit den nächsten Leitfäden fort, um zu erfahren, wie Sie Images aus der neu bereitgestellten verbundenen Registrierung pullen oder die verbundene Registrierung auf geschachtelten IoT Edge-Geräten bereitstellen.


> [!div class="nextstepaction"]
> [Pullen von Images aus einer verbundenen Registrierung][pull-images-from-connected-registry]

> [!div class="nextstepaction"]
> [Tutorial: Bereitstellen einer verbundenen Registrierung auf geschachtelten IoT Edge-Geräten][tutorial-connected-registry-nested]

<!-- LINKS - internal -->
[az-acr-connected-registry-get-settings]: /cli/azure/acr/connected-registry/install#az_acr_connected_registry_get_settings
[az-acr-connected-registry-show]: /cli/azure/acr/connected-registr#az_acr_connected_registry_show
[az-acr-import]:/cli/azure/acr#az_acr_import
[az-acr-token-credential-generate]: /cli/azure/acr/token/credential?#az_acr_token_credential_generate
[container-registry-intro]: container-registry-intro.md
[pull-images-from-connected-registry]: pull-images-from-connected-registry.md
[quickstart-connected-registry-cli]: quickstart-connected-registry-cli.md
[quickstart-connected-registry-portal]: quickstart-connected-registry-portal.md
[tutorial-connected-registry-nested]: tutorial-deploy-connected-registry-nested-iot-edge-cli.md
