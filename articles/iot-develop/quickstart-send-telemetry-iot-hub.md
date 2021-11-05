---
title: 'Schnellstart: Senden von Gerätetelemetriedaten an Azure IoT Hub'
description: In dieser Schnellstartanleitung erfahren Geräteentwickler, wie sie eine sichere Verbindung zwischen einem Gerät und Azure IoT Hub herstellen. Sie verwenden ein Azure IoT-Geräte-SDK für C, C#, Python, Node.js oder Java, um einen Geräteclient für Windows, Linux oder Raspberry Pi OS (Raspian) zu erstellen. Abschließend verbinden Sie das Gerät mit Azure IoT Hub und senden Telemetriedaten.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.topic: quickstart
ms.date: 08/03/2021
ms.collection: embedded-developer, application-developer
zone_pivot_groups: iot-develop-set1
ms.openlocfilehash: d079fd2380aed72910f5b78e2e3135c077ac59d1
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131077484"
---
# <a name="quickstart-send-telemetry-from-an-iot-plug-and-play-device-to-azure-iot-hub"></a>Schnellstart: Senden von Telemetriedaten von einem IoT Plug & Play-Gerät an Azure IoT Hub

**Gilt für**: [Entwickler von Geräteanwendungen](about-iot-develop.md#device-application-development)

:::zone pivot="programming-language-ansi-c"

[!INCLUDE [iot-develop-send-telemetry-iot-hub-c](../../includes/iot-develop-send-telemetry-iot-hub-c.md)]

:::zone-end

:::zone pivot="programming-language-csharp"

[!INCLUDE [iot-develop-send-telemetry-iot-hub-csharp](../../includes/iot-develop-send-telemetry-iot-hub-csharp.md)]

:::zone-end

:::zone pivot="programming-language-java"

[!INCLUDE [iot-develop-send-telemetry-iot-hub-java](../../includes/iot-develop-send-telemetry-iot-hub-java.md)]

:::zone-end

:::zone pivot="programming-language-nodejs"

[!INCLUDE [iot-develop-send-telemetry-iot-hub-node](../../includes/iot-develop-send-telemetry-iot-hub-node.md)]

:::zone-end

:::zone pivot="programming-language-python"

[!INCLUDE [iot-develop-send-telemetry-iot-hub-python](../../includes/iot-develop-send-telemetry-iot-hub-python.md)]

:::zone-end
    
## <a name="clean-up-resources"></a>Bereinigen von Ressourcen
Falls Sie die in dieser Schnellstartanleitung erstellten Azure-Ressourcen nicht mehr benötigen, können Sie sie mit der Azure CLI löschen.

> [!IMPORTANT]
> Das Löschen einer Ressourcengruppe kann nicht rückgängig gemacht werden. Die Ressourcengruppe und alle darin enthaltenen Ressourcen werden unwiderruflich gelöscht. Achten Sie daher darauf, dass Sie nicht versehentlich die falsche Ressourcengruppe oder die falschen Ressourcen löschen.

So löschen Sie eine Ressourcengruppe anhand des Namens:
1. Führen Sie den Befehl [az group delete](/cli/azure/group#az_group_delete) aus. Die von Ihnen erstellte Ressourcengruppe, der IoT-Hub und die Geräteregistrierung werden mithilfe dieses Befehls entfernt.

    ```azurecli-interactive
    az group delete --name MyResourceGroup
    ```
1. Führen Sie den Befehl [az group list](/cli/azure/group#az_group_list) aus, um sich zu vergewissern, dass die Ressourcengruppe gelöscht wurde.  

    ```azurecli-interactive
    az group list
    ```

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie einen grundlegenden Workflow für Azure IoT-Anwendungen zum sicheren Verbinden eines Geräts mit der Cloud und zum Senden von Telemetriedaten vom Gerät zur Cloud kennengelernt. Sie haben die Azure CLI verwendet, um einen Azure IoT-Hub und eine Geräteinstanz zu erstellen. Anschließend haben Sie mit dem Azure IoT-Geräte-SDK einen Temperaturcontroller erstellt, eine Verbindung mit dem Hub hergestellt und Telemetriedaten gesendet. Sie haben zudem das Azure CLI verwendet, um Telemetriedaten zu überwachen.

Sehen Sie sich anschließend die folgenden Artikel an, um mehr über das Erstellen von Gerätelösungen mit Azure IoT zu erfahren: 

> [!div class="nextstepaction"]
> [Schnellstart: Steuern eines mit einer IoT Hub-Instanz verbundenen Geräts (Python)](../iot-hub/quickstart-control-device.md)
> [!div class="nextstepaction"]
> [Senden von Telemetriedaten an IoT Central](quickstart-send-telemetry-central.md)
> [!div class="nextstepaction"]
> [Verbinden eines MXCHIP AZ3166-DevKits mit IoT Central](quickstart-devkit-mxchip-az3166.md)
