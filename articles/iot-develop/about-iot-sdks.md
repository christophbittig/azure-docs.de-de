---
title: Übersicht über Optionen im Zusammenhang mit dem Azure IoT-Geräte-SDK
description: Hier erfahren Sie, welches Azure IoT-Geräte-SDK Sie basierend auf Ihrer Entwicklungsrolle und Ihren Aufgaben verwenden sollten.
author: philmea
ms.author: philmea
ms.service: iot-develop
ms.topic: overview
ms.date: 02/11/2021
ms.openlocfilehash: e4f5d99caadaedf6389215fccb753aedd6620917
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "129983967"
---
# <a name="overview-of-azure-iot-device-sdks"></a>Übersicht über Azure IoT-Geräte-SDKs

Die Azure IoT-Geräte-SDKs enthalten eine Reihe von Geräteclientbibliotheken, Beispielen und Dokumentationen. Die Geräte-SDKs vereinfachen das programmgesteuerte Verbinden von Geräten mit Azure IoT. Die SDKs stehen in verschiedenen Programmiersprachen für und mit Unterstützung mehrerer RTOS für eingebettete Geräte zur Verfügung.

## <a name="why-use-an-azure-iot-device-sdk"></a>Gründe für die Verwendung eines Azure IoT-Geräte-SDK

Die Verwendung eines Azure IoT-Geräte-SDK hat gegenüber der Erstellung einer benutzerdefinierten Verbindungsschicht folgende Vorteile:

| | Benutzerdefinierte Verbindungsschicht | Azure IoT-Geräte-SDKs |
| :-- | :-- | :-- |
| **Unterstützung** | Sie müssen Support für Ihre Lösung bereitstellen und eine Dokumentation erstellen. | Sie haben Zugang zum Support von Microsoft (GitHub, Microsoft Q&A, Microsoft-Dokumentation, Kundensupportteams). |
| **Neue Features** | Neue Azure-Features müssen manuell hinzugefügt werden. | Neu hinzugefügte Features können sofort genutzt werden. |
| **Investition** | Das Entwickeln, Erstellen, Testen und Pflegen einer benutzerdefinierten Version verschlingt hunderte Stunden an eingebetteter Entwicklung. | Sie profitieren von kostenlosen Open-Source-Tools. Die einzigen Kosten für die SDKs werden durch die Lernkurve verursacht. |

## <a name="which-sdk-should-i-use"></a>Welches SDK sollte ich verwenden?

Das wichtigste Kriterium bei der Wahl eines SDK ist die Hardware des Geräts. Allgemeine Computergeräte wie PCs und Mobiltelefone enthalten Mikroprozessoren (Microprocessor Units, MPUs) und verfügen über vergleichsweise umfangreiche Compute- und Arbeitsspeicherressourcen. Eine besondere Klasse von Geräten, die als Sensoren oder für andere spezielle Zwecke verwendet werden, enthalten Mikrocontroller (Microcontroller Units, MCUs) und verfügen über vergleichsweise begrenzte Compute- und Arbeitsspeicherressourcen. Für diese Geräte sind aufgrund der begrenzten Ressourcen spezielle Entwicklungstools und SDKs erforderlich. Die folgende Tabelle enthält eine Zusammenfassung der verschiedenen Geräteklassen sowie Informationen dazu, welche SDKs für die Geräteentwicklung verwendet werden sollten:

|Geräteklasse|BESCHREIBUNG|Beispiele|SDKs|
|-|-|-|-|
|Embedded-Geräte|Spezielle MCU-basierte Geräte mit begrenzten Compute- und Arbeitsspeicherressourcen|Sensoren|[SDKs für eingebettete Geräte](#embedded-device-sdks)|
|Sonstige|Universelle MPU-basierte Geräte mit umfangreicheren Compute- und Arbeitsspeicherressourcen|PC, Smartphone, Raspberry Pi|[Geräte-SDKs](#device-sdks)|

> [!Note] 
> Weitere Informationen zu verschiedenen Gerätekategorien, die Ihnen dabei helfen, das optimale SDK für Ihr Gerät auszuwählen, finden Sie in der [Übersicht über Azure IoT-Gerätetypen](concepts-iot-device-types.md).

## <a name="device-sdks"></a>Geräte-SDKs

Diese SDKs können auf einem allgemeinen MPU-basierten Computergerät wie einem PC, Tablet, Smartphone oder Raspberry Pi ausgeführt werden. Die SDKs unterstützen die Entwicklung in C sowie in modernen verwalteten Sprachen wie C#, Node.js, Python und Java.

Die SDKs sind in **mehreren Sprachen** verfügbar, sodass Sie sich flexibel für das SDK entscheiden können, das für Ihr Team und Ihr Szenario am besten geeignet ist.

| Sprache | Paket | `Source` | Schnellstarts | Beispiele | Verweis |
| :-- | :-- | :-- | :-- | :-- | :-- |
| **.NET** | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp) | [IoT Hub](quickstart-send-telemetry-iot-hub.md?pivots=programming-language-csharp) / [IoT Central](quickstart-send-telemetry-central.md?pivots=programming-language-csharp) | [Beispiele](https://github.com/Azure-Samples/azure-iot-samples-csharp) | [Referenz](/dotnet/api/microsoft.azure.devices.client) |
| **Python** | [pip](https://pypi.org/project/azure-iot-device/) | [GitHub](https://github.com/Azure/azure-iot-sdk-python) | [IoT Hub](quickstart-send-telemetry-iot-hub.md?pivots=programming-language-python) / [IoT Central](quickstart-send-telemetry-central.md?pivots=programming-language-python) | [Beispiele](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples) | [Referenz](/python/api/azure-iot-device) |
| **Node.js** | [npm](https://www.npmjs.com/package/azure-iot-device) | [GitHub](https://github.com/Azure/azure-iot-sdk-node) | [IoT Hub](quickstart-send-telemetry-iot-hub.md?pivots=programming-language-nodejs) / [IoT Central](quickstart-send-telemetry-central.md?pivots=programming-language-nodejs) | [Beispiele](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples) | [Referenz](/javascript/api/azure-iot-device/) |
| **Java** | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-device-client) | [GitHub](https://github.com/Azure/azure-iot-sdk-java) | [IoT Hub](quickstart-send-telemetry-iot-hub.md?pivots=programming-language-java) / [IoT Central](quickstart-send-telemetry-central.md?pivots=programming-language-java) | [Beispiele](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples) | [Referenz](/java/api/com.microsoft.azure.sdk.iot.device) |
| **C** | [packages](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md#getting-the-sdk) | [GitHub](https://github.com/Azure/azure-iot-sdk-c) | [IoT Hub](quickstart-send-telemetry-iot-hub.md?pivots=programming-language-ansi-c) / [IoT Central](quickstart-send-telemetry-central.md?pivots=programming-language-ansi-c) | [Beispiele](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples) | [Referenz](/azure/iot-hub/iot-c-sdk-ref/) |

> [!WARNING]
> Das oben angegebene **C SDK** ist aufgrund seines Speicherverwaltungs- und Threadingmodells **nicht** für eingebettete Anwendungen geeignet. Informationen für eingebettete Geräte finden Sie unter [SDKs für eingebettete Geräte](#embedded-device-sdks).

## <a name="embedded-device-sdks"></a>SDKs für eingebettete Geräte

Diese SDKs wurden für die Ausführung auf Geräten mit begrenzten Compute- und Arbeitsspeicherressourcen entwickelt und erstellt und werden mit der Programmiersprache C implementiert.

Die SDKs für eingebettete Geräte sind für **mehreren Betriebssysteme** verfügbar, sodass Sie sich flexibel für das SDK entscheiden können, das für Ihr Team und Ihr Szenario am besten geeignet ist.

| RTOS | SDK | `Source` | Beispiele | Verweis |
| :-- | :-- | :-- | :-- | :-- | 
| **Azure RTOS** | Azure RTOS-Middleware | [GitHub](https://github.com/azure-rtos/netxduo) | [Schnellstarts](quickstart-devkit-mxchip-az3166.md) | [Verweis](https://github.com/azure-rtos/netxduo/tree/master/addons/azure_iot) | 
| **FreeRTOS** | FreeRTOS-Middleware | [GitHub](https://github.com/Azure/azure-iot-middleware-freertos) | [Beispiele](https://github.com/Azure-Samples/iot-middleware-freertos-samples) | [Referenz](https://azure.github.io/azure-iot-middleware-freertos) |
| **Bare Metal** | Azure SDK für Embedded C | [GitHub](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/docs/iot) | [Beispiele](https://github.com/Azure/azure-sdk-for-c/blob/master/sdk/samples/iot/README.md) | [Referenz](https://azure.github.io/azure-sdk-for-c) |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen dazu, wie Sie mithilfe der Azure IoT-Geräte-SDKs Verbindungen mit allgemeinen, MPU-basierten Geräten herstellen, finden Sie in den folgenden Artikeln:

* [Schnellstart: Senden von Telemetriedaten an IoT Central](quickstart-send-telemetry-central.md)
* [Schnellstart: Senden von Telemetriedaten an IoT Hub](quickstart-send-telemetry-iot-hub.md)

Weitere Informationen dazu, wie Sie mithilfe der Azure IoT-SDKs für eingebettete Geräte Verbindungen mit MCU-basierten Geräten mit begrenzten Ressourcen herstellen, finden Sie im folgenden Artikel:
* [Schnellstart: Verbinden eines MXCHIP AZ3166-DevKit mit IoT Central](quickstart-devkit-mxchip-az3166.md)
