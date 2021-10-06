---
title: Azure IoT Hub-SDKs | Microsoft-Dokumentation
description: Links zu den Azure IoT Hub-SDKs, die Sie zum Erstellen von Geräte-Apps und Back-End-Apps verwenden können.
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 06/01/2021
ms.custom:
- mqtt
- 'Role: IoT Device'
- 'Role: Cloud Development'
ms.openlocfilehash: e8bfc95d80aa58b9781258c1b87902c2e8d0c37f
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/05/2021
ms.locfileid: "129458584"
---
# <a name="azure-iot-hub-sdks"></a>Azure IoT Hub-SDKs

Es gibt zwei Kategorien von Software Development Kits (SDKs) für die Arbeit mit IoT Hub:

* [**Mit IoT Hub-Dienst-SDKs**](#azure-iot-hub-service-sdks) können Sie Back-End-Anwendungen zum Verwalten Ihrer IoT-Hub-Instanz erstellen und optional Nachrichten senden, Aufträge planen, direkte Methoden aufrufen oder gewünschte Eigenschaftsupdates an Ihre IoT-Geräte oder -Module senden.

* [**IoT Hub-Geräte-SDKs**](../iot-develop/about-iot-sdks.md) ermöglichen das Erstellen von Apps, die auf Ihren IoT-Geräten ausgeführt werden, mit dem Geräteclient oder Modulclient. Mit diesen Apps werden Telemetriedaten an die IoT Hub-Instanz gesendet und optional Nachrichten oder Updates für Aufträge, Methoden oder Gerätezwillingen von der IoT Hub-Instanz empfangen. Sie können mithilfe dieser SDKs Geräte-Apps erstellen, die[Azure IoT Plug & Play](../iot-develop/overview-iot-plug-and-play.md)-Konventionen und -Modelle verwenden, um ihre Funktionen für IoT Plug & Play-fähige Anwendungen anzukündigen. Sie können mit dem Modulclient auch [Module](../iot-edge/iot-edge-modules.md) für [Azure IoT Edge-Runtime](../iot-edge/about-iot-edge.md) erstellen.

Darüber hinaus bieten wir auch ein Spektrum an SDKs zum Arbeiten mit dem [Device Provisioning Service](../iot-dps/about-iot-dps.md).

* Mit **Gerätebereitstellungs-SDKs** können Sie auf Ihren IoT-Geräten ausgeführte Apps für die Kommunikation mit dem Device Provisioning Service erstellen.

* Mit **Dienstbereitstellungs-SDKs** können Sie Back-End-Anwendungen zum Verwalten Ihrer Registrierungen im Device Provisioning Service erstellen.

Erfahren Sie mehr über die [Vorteile beim Entwickeln mit Azure IoT SDKs](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/).

## <a name="azure-iot-hub-service-sdks"></a>Azure IoT Hub-Dienst-SDKs

Die Azure IoT-Dienst-SDKs enthalten Code zum Erstellen von Anwendungen, die direkt mit IoT Hub interagieren, um Geräte und Sicherheit zu verwalten.

| Plattform  | Paket | Coderepository | Beispiele |  Verweis |
|---|---|---|---|---|
| .NET | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Devices ) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp) | [Beispiele](https://github.com/Azure-Samples/azure-iot-samples-csharp) | [Referenz](/dotnet/api/microsoft.azure.devices) |
| Java | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-service-client) | [GitHub](https://github.com/Azure/azure-iot-sdk-java) | [Beispiele](https://github.com/Azure/azure-iot-sdk-java/tree/main/service/iot-service-samples/pnp-service-sample) | [Referenz](/java/api/com.microsoft.azure.sdk.iot.service) |
| Node | [npm](https://www.npmjs.com/package/azure-iothub) | [GitHub](https://github.com/Azure/azure-iot-sdk-node) | [Beispiele](https://github.com/Azure/azure-iot-sdk-node/tree/master/service/samples) | [Referenz](/javascript/api/azure-iothub/) |
| Python | [pip](https://pypi.org/project/azure-iot-hub) | [GitHub](https://github.com/Azure/azure-iot-sdk-python) | [Beispiele](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-hub/samples) | [Referenz](/python/api/azure-iot-hub) |
| Node.js | [npm](https://www.npmjs.com/package/azure-iot-common) | [GitHub](https://github.com/Azure/azure-iot-sdk-node) | [Beispiele](https://github.com/Azure/azure-iot-sdk-node/tree/master/service/samples/javascript) | [Referenz](/javascript/api/azure-iothub/) |

Azure IoT Hub Service SDK für iOS:

* Installation über [CocoaPod](https://cocoapods.org/pods/AzureIoTHubServiceClient)
* [Beispiele](https://github.com/Azure-Samples/azure-iot-samples-ios)

## <a name="microsoft-azure-provisioning-sdks"></a>Microsoft Azure-Bereitstellungs-SDKs

Mit **Microsoft Azure-Bereitstellungs-SDKs** können Sie Geräte in IoT Hub mit dem [Device Provisioning Service](../iot-dps/about-iot-dps.md) bereitstellen.

| Plattform | Paket | Quellcode | Verweis |
| -----|-----|-----|-----|
| .NET|[Geräte-SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Client/), [Dienst-SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/) |[GitHub](https://github.com/Azure/azure-iot-sdk-csharp/)|[Referenz](/dotnet/api/microsoft.azure.devices.provisioning.client) |
| C|[apt-get, MBED, Arduino IDE oder iOS](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md#packages-and-libraries)|[GitHub](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning\_client)|[Referenz](/azure/iot-hub/iot-c-sdk-ref/) |
| Java|[Maven](https://github.com/Azure/azure-iot-sdk-java/blob/main/doc/java-devbox-setup.md#for-the-service-sdk)|[GitHub](https://github.com/Azure/azure-iot-sdk-java/blob/main/provisioning)|[Referenz](/java/api/com.microsoft.azure.sdk.iot.provisioning.device) |
| Node.js|[Geräte-SDK](https://badge.fury.io/js/azure-iot-provisioning-device), [Dienst-SDK](https://badge.fury.io/js/azure-iot-provisioning-service) |[GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning)|[Referenz](/javascript/api/overview/azure/iothubdeviceprovisioning) |
| Python|[Geräte-SDK](https://pypi.org/project/azure-iot-device/), [Dienst-SDK](https://pypi.org/project/azure-iothub-provisioningserviceclient/)|[GitHub](https://github.com/Azure/azure-iot-sdk-python)|[Gerätereferenz](/python/api/azure-iot-device/azure.iot.device.provisioningdeviceclient), [Dienstreferenz](/python/api/azure-mgmt-iothubprovisioningservices) |

## <a name="azure-iot-hub-device-sdks"></a>Azure IoT Hub-Geräte-SDKs

Die Microsoft Azure IoT-Geräte-SDKs enthalten Code, der das Erstellen von Anwendungen erleichtert, die eine Verbindung mit Azure IoT Hub-Diensten herstellen und von ihnen verwaltet werden.

Weitere Informationen zu den IoT Hub-Geräte-SDKs finden Sie in der [Dokumentation zur IoT-Geräteentwicklung.](../iot-develop/about-iot-sdks.md)

## <a name="sdk-and-hardware-compatibility"></a>SDK- und Hardwarekompatibilität

Weitere Informationen zum Auswählen eines Geräte-SDKs finden Sie unter [Übersicht über Azure IoT-Geräte-SDKs](../iot-develop/about-iot-sdks.md).

Weitere Informationen zur Kompatibilität von SDKs mit bestimmten Hardwaregeräten finden Sie im [Azure Certified for IoT-Gerätekatalog](https://devicecatalog.azure.com/) oder im jeweiligen Repository.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="next-steps"></a>Nächste Schritte

Relevante Dokumentation, die im Zusammenhang mit der Entwicklung mithilfe der Azure-IoT-SDKs steht:

* Informieren Sie sich über das [Verwalten von Konnektivität und zuverlässigem Messaging](iot-hub-reliability-features-in-sdks.md) mithilfe von IoT Hub-SDKs.
* Informieren Sie sich über das [Entwickeln für mobile Plattformen](iot-hub-how-to-develop-for-mobile-devices.md) wie etwa iOS und Android.
* [Dokumentation zur IoT-Geräteentwicklung](../iot-develop/about-iot-sdks.md)

Weitere Referenzthemen in diesem IoT Hub-Entwicklungsleitfaden:

* [IoT Hub-Endpunkte](iot-hub-devguide-endpoints.md)
* [IoT Hub-Abfragesprache für Gerätezwillinge, Aufträge und Nachrichtenrouting](iot-hub-devguide-query-language.md)
* [Kontingente und Drosselung](iot-hub-devguide-quotas-throttling.md)
* [IoT Hub-MQTT-Unterstützung](iot-hub-mqtt-support.md)
* [REST-API-Referenz zu IoT Hub](/rest/api/iothub/)
