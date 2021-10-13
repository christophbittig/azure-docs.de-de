---
title: IoT Plug & Play-Bibliotheken und SDKs
description: Informationen zu den Geräte- und Dienstbibliotheken, die für die Entwicklung von IoT Plug & Play-fähigen Lösungen zur Verfügung stehen.
author: rido-min
ms.author: rmpablos
ms.date: 07/22/2020
ms.topic: reference
ms.service: iot-develop
services: iot-develop
ms.custom: mvc
ms.openlocfilehash: 089c82d2b7a682e5f9a655dbc2a4b34dc94c58ac
ms.sourcegitcommit: 216b6c593baa354b36b6f20a67b87956d2231c4c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/11/2021
ms.locfileid: "129728701"
---
# <a name="microsoft-sdks-for-iot-plug-and-play"></a>Microsoft-SDKs für IoT Plug & Play

Die IoT Plug & Play-Bibliotheken und SDKs ermöglichen es Entwicklern, IoT-Lösungen mithilfe verschiedener Programmiersprachen auf mehreren Plattformen zu erstellen. Die nachstehende Tabelle enthält Links zu Beispielen und Schnellstarts, die Ihnen den Einstieg erleichtern sollen:

## <a name="device-sdks"></a>Geräte-SDKs

| Sprache | Paket | Coderepository | Beispiele | Schnellstart | Verweis |
|---|---|---|---|---|---|
| C (Gerät) | [vcpkg 1.3.9](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/setting_up_vcpkg.md) | [GitHub](https://github.com/Azure/azure-iot-sdk-c) | [Beispiele](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/pnp) | [Herstellen einer Verbindung mit IoT Hub](tutorial-connect-device.md) | [Referenz](/azure/iot-hub/iot-c-sdk-ref/) |
| .NET (Gerät) | [NuGet 1.31.0](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp/tree/main/) | [Beispiele](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/main/iot-hub/Samples/device/PnpDeviceSamples) | [Herstellen einer Verbindung mit IoT Hub](tutorial-connect-device.md) | [Referenz](/dotnet/api/microsoft.azure.devices.client) |
| Java (Gerät) | [Maven 1.26.0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-device-client) | [GitHub](https://github.com/Azure/azure-iot-sdk-java/tree/main/) | [Beispiele](https://github.com/Azure/azure-iot-sdk-java/tree/main/device/iot-device-samples/pnp-device-sample) | [Herstellen einer Verbindung mit IoT Hub](tutorial-connect-device.md) | [Referenz](/java/api/com.microsoft.azure.sdk.iot.device) |
| Python (Gerät) | [pip 2.3.0](https://pypi.org/project/azure-iot-device/) | [GitHub](https://github.com/Azure/azure-iot-sdk-python/tree/master/) | [Beispiele](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/pnp) | [Herstellen einer Verbindung mit IoT Hub](tutorial-connect-device.md) | [Referenz](/python/api/azure-iot-device/azure.iot.device) |
| Node (Gerät) | [npm 1.17.2](https://www.npmjs.com/package/azure-iot-device)  | [GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/master/) | [Beispiele](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples/javascript/pnp) | [Herstellen einer Verbindung mit IoT Hub](tutorial-connect-device.md) | [Referenz](/javascript/api/azure-iot-device/) |
| Embedded C: Gerät | – | [GitHub](https://github.com/Azure/azure-sdk-for-c/)| [Beispiele](tutorial-connect-device.md?pivots=programming-language-embedded-c#samples) | [Verwenden von Embedded C](tutorial-connect-device.md?pivots=programming-language-embedded-c) | NICHT ZUTREFFEND

## <a name="service-sdks"></a>Dienst-SDKs

| Plattform  | Paket | Coderepository | Beispiele | Schnellstart | Verweis |
|---|---|---|---|---|---|
| .NET – IoT Hub-Dienst | [NuGet 1.27.1](https://www.nuget.org/packages/Microsoft.Azure.Devices ) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp) | [Beispiele](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/main/iot-hub/Samples/service/PnpServiceSamples) | N/V | [Verweis](/dotnet/api/microsoft.azure.devices) |
| Java – IoT Hub-Dienst | [Maven 1.26.0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-service-client/1.26.0) | [GitHub](https://github.com/Azure/azure-iot-sdk-java) | [Beispiele](https://github.com/Azure/azure-iot-sdk-java/tree/main/service/iot-service-samples/pnp-service-sample) | N/V | [Verweis](/java/api/com.microsoft.azure.sdk.iot.service) |
| Node – IoT Hub-Dienst | [npm 1.13.0](https://www.npmjs.com/package/azure-iothub) | [GitHub](https://github.com/Azure/azure-iot-sdk-node) | [Beispiele](https://github.com/Azure/azure-iot-sdk-node/tree/master/service/samples) | N/V | [Verweis](/javascript/api/azure-iothub/) |
| Python – Digital Twins-Dienst | [pip 2.2.3](https://pypi.org/project/azure-iot-hub) | [GitHub](https://github.com/Azure/azure-iot-sdk-python) | [Beispiele](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-hub/samples) | [Interaktion mit IoT Hub Digital Twins-API](tutorial-service.md) | – |
| Node – Digital Twins-Dienst | [npm 1.13.0](https://www.npmjs.com/package/azure-iot-digitaltwins-service) | [GitHub](https://github.com/Azure/azure-iot-sdk-node) | [Beispiele](https://github.com/Azure/azure-iot-sdk-node/tree/master/service/samples/javascript) | [Interaktion mit IoT Hub Digital Twins-API](tutorial-service.md) | – |

## <a name="next-steps"></a>Nächste Schritte

Informationen zum Testen der SDKs und Bibliotheken finden Sie im [Entwicklerhandbuch](concepts-developer-guide-device.md) und in den [Tutorials für Geräte](tutorial-connect-device.md) und [Tutorials für Dienste](tutorial-service.md).
