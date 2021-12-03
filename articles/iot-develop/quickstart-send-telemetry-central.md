---
title: 'Schnellstart: Herstellen einer Verbindung mit einem Gerät und Senden von Telemetriedaten an Azure IoT Central'
description: In dieser Schnellstartanleitung erfahren Geräteentwickler, wie sie eine sichere Verbindung zwischen einem Gerät und Azure IoT Central herstellen. Sie verwenden ein Azure IoT-Geräte-SDK für C, C#, Python, Node.js oder Java, um eine Client-Anwendung auf einem Gerät auszuführen, dann stellen Sie eine Verbindung zu IoT Central her und senden Telemetriedaten.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.topic: quickstart
ms.date: 04/27/2021
ms.collection: embedded-developer, application-developer
zone_pivot_groups: iot-develop-set1
ms.openlocfilehash: f6955c10aab9b21be43cc3b346e564cb9114979d
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131032738"
---
# <a name="quickstart-send-telemetry-from-a-device-to-azure-iot-central"></a>Schnellstart: Senden von Telemetriedaten von einem Gerät an Azure IoT Central

**Gilt für**: [Entwickler von Geräteanwendungen](about-iot-develop.md#device-application-development)

:::zone pivot="programming-language-ansi-c"

[!INCLUDE [iot-develop-send-telemetry-central-c](../../includes/iot-develop-send-telemetry-central-c.md)]

:::zone-end

:::zone pivot="programming-language-csharp"

[!INCLUDE [iot-develop-send-telemetry-central-csharp](../../includes/iot-develop-send-telemetry-central-csharp.md)]

:::zone-end

:::zone pivot="programming-language-java"

[!INCLUDE [iot-develop-send-telemetry-central-java](../../includes/iot-develop-send-telemetry-central-java.md)]

:::zone-end

:::zone pivot="programming-language-nodejs"

[!INCLUDE [iot-develop-send-telemetry-central-nodejs](../../includes/iot-develop-send-telemetry-central-nodejs.md)]

:::zone-end

:::zone pivot="programming-language-python"

[!INCLUDE [iot-develop-send-telemetry-central-python](../../includes/iot-develop-send-telemetry-central-python.md)]

:::zone-end

## <a name="view-telemetry"></a>Anzeigen von Telemetriedaten
Nachdem das Gerät eine Verbindung zur IoT-Zentrale hergestellt hat, beginnt es, Telemetriedaten zu senden. Sie können die Telemetriedaten und andere Details zu verbundenen Geräten in IoT Central anzeigen. 

Wählen Sie in IoT Central **Geräte** aus, klicken Sie auf Ihren Gerätenamen, und wählen Sie dann die Registerkarte **Übersicht** aus. In dieser Ansicht wird ein Diagramm der Temperaturen der beiden Thermostate angezeigt.

:::image type="content" source="media/quickstart-send-telemetry-central/iot-central-telemetry-output-overview.png" alt-text="IoT Central: Übersicht über Gerätetelemetrie":::

Wählen Sie die Registerkarte **Rohdaten** aus. In dieser Ansicht werden die Telemetriedaten jedes Mal angezeigt, wenn ein Thermostatmesswert gesendet wird.

:::image type="content" source="media/quickstart-send-telemetry-central/iot-central-telemetry-output-raw.png" alt-text="IoT Central: Rohdatenausgabe der Gerätetelemetrie":::

Ihr Gerät ist nun sicher verbunden und sendet Telemetriedaten an Azure IoT.
    
## <a name="clean-up-resources"></a>Bereinigen von Ressourcen
Falls Sie die in dieser Schnellstartanleitung erstellten IoT Central-Ressourcen nicht mehr benötigen, können Sie sie löschen. Wenn Sie gemäß der Dokumentation in diesem Handbuch fortfahren möchten, können Sie die von Ihnen erstellte Anwendung beibehalten und für andere Beispiele wiederverwenden.

So entfernen Sie die Azure IoT Central-Beispielanwendung und alle zugehörigen Geräte und Ressourcen:
1. Wählen Sie **Verwaltung** > **Ihre Anwendung** aus.
1. Klicken Sie auf **Löschen**.

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstartanleitung haben Sie einen grundlegenden Workflow für Azure IoT-Anwendungen zum sicheren Verbinden eines Geräts mit der Cloud und zum Senden von Telemetriedaten vom Gerät zur Cloud kennengelernt. Sie haben Azure IoT Central verwendet, um eine Anwendung und eine Geräteinstanz zu erstellen. Dann haben Sie ein Azure IoT Device SDK verwendet, um einen Temperaturregler zu erstellen, eine Verbindung zu IoT Central herzustellen und Telemetriedaten zu senden. Außerdem haben Sie mit IoT Central die Telemetrie überwacht.

Sehen Sie sich anschließend die folgenden Artikel an, um mehr über das Erstellen von Gerätelösungen mit Azure IoT zu erfahren: 

> [!div class="nextstepaction"]
> [Schnellstart: Senden von Telemetriedaten von einem Gerät an Azure IoT Central](./quickstart-send-telemetry-iot-hub.md)
> [!div class="nextstepaction"]
> [Erstellen einer IoT Central-Anwendung](../iot-central/core/quick-deploy-iot-central.md)