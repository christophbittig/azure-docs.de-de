---
title: Übersicht über Optionen im Zusammenhang mit dem Azure IoT-Geräte-SDK
description: Hier erfahren Sie, welches Azure IoT-Geräte-SDK Sie basierend auf Ihrer Entwicklungsrolle und Ihren Aufgaben verwenden sollten.
author: philmea
ms.author: philmea
ms.service: iot-develop
ms.topic: overview
ms.date: 02/11/2021
ms.openlocfilehash: 2374755dc1874b1dcd421daa97fc77535ef99286
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/05/2021
ms.locfileid: "129455435"
---
# <a name="overview-of-azure-iot-device-sdks"></a>Übersicht über Azure IoT-Geräte-SDKs

Bei den Azure IoT-Geräte-SDKs handelt es sich um eine Sammlung von Geräteclientbibliotheken, Entwicklerhandbüchern, Beispielen und Dokumentationen. Die Geräte-SDKs helfen Ihnen dabei, Geräte programmgesteuert mit Azure IoT-Diensten zu verbinden.

:::image type="content" border="false" source="media/about-iot-sdks/iot-sdk-diagram.png" alt-text="Diagramm: Verschiedene Azure IoT-SDKs":::

Wie im Diagramm zu sehen, stehen mehrere Geräte-SDKs zur Verfügung, um Ihre Anforderungen in puncto Gerät und Programmiersprache zu erfüllen. Informationen zur Wahl des passenden Geräte-SDK finden Sie unter [Welches SDK sollte ich verwenden?](#which-sdk-should-i-use). Es sind auch Azure IoT-Dienst-SDKs verfügbar, mit denen Sie Ihre cloudbasierte Anwendung mit Azure IoT-Diensten am Back-End verbinden können. In diesem Artikel stehen die Geräte-SDKs im Mittelpunkt. Weitere Informationen zu Azure-Dienst-SDKs finden Sie [hier](#service-sdks).

## <a name="why-should-i-use-the-azure-iot-device-sdks"></a>Warum sollte ich die Azure IoT-Geräte-SDKs verwenden?

Wenn Sie eine Verbindung zwischen Geräten und Azure IoT herstellen möchten, können Sie eine benutzerdefinierte Verbindungsschicht erstellen oder Azure IoT-Geräte-SDKs verwenden. Die Verwendung von Azure IoT-Geräte-SDKs hat mehrere Vorteile:

| Entwicklungskosten &nbsp; &nbsp; &nbsp; &nbsp; | Benutzerdefinierte Verbindungsschicht | Azure IoT-Geräte-SDKs |
| :-- | :------------------------------------------------ | :---------------------------------------- |
| Support | Für alles, was Sie erstellen, muss Support bereitgestellt und eine Dokumentation erstellt werden. | Sie haben Zugang zum Support von Microsoft (GitHub, Microsoft Q&A, Microsoft-Dokumentation, Kundensupportteams). |
| Neue Funktionen | Neue Azure-Features müssen der benutzerdefinierten Middleware hinzugefügt werden. | Neue Features, die von Microsoft kontinuierlich den IoT-SDKs hinzugefügt werden, können sofort genutzt werden. |
| Investition | Das Entwickeln, Erstellen, Testen und Pflegen einer benutzerdefinierten Version verschlingt hunderte Stunden an eingebetteter Entwicklung. | Sie profitieren von kostenlosen Open-Source-Tools. Die einzigen Kosten für die SDKs werden durch die Lernkurve verursacht. |

## <a name="which-sdk-should-i-use"></a>Welches SDK sollte ich verwenden?

Azure IoT-Geräte-SDKs sind in gängigen Programmiersprachen wie C, C#, Java, Node.js und Python verfügbar. Bei der Wahl eines SDK müssen hauptsächlich zwei Faktoren berücksichtigt werden: Gerätefunktionen und die Vertrautheit Ihres Teams mit der Programmiersprache.

### <a name="device-capabilities"></a>Gerätefunktionen

Wenn Sie sich für ein SDK entscheiden, müssen Sie die Einschränkungen der von Ihnen verwendeten Geräte berücksichtigen. Ein eingeschränktes Gerät verfügt über einen einzelnen Mikrocontroller (Micro Controller Unit, MCU) und über begrenzten Arbeitsspeicher. Bei Verwendung eines eingeschränkten Geräts empfiehlt es sich, das [Embedded C SDK](#embedded-c-sdk) zu verwenden. Dieses SDK stellt nur die Funktionen bereit, die unbedingt für die Verbindungsherstellung mit Azure IoT erforderlich sind. Sie können auch Komponenten (MQTT-Client, TLS und Socketbibliotheken) auswählen, die für Ihr eingebettetes Gerät am besten optimiert sind. Wenn auf Ihrem eingeschränkten Gerät auch Azure RTOS ausgeführt wird, können Sie die Azure RTOS-Middleware verwenden, um eine Verbindung mit Azure IoT herzustellen. Die Azure RTOS-Middleware umschließt das Embedded C SDK mit zusätzlichen Funktionen, um das Herstellen einer Cloudverbindung für Ihr Azure RTOS-Gerät zu vereinfachen.

Ein nicht eingeschränktes Gerät verfügt über eine robustere CPU, die zum Ausführen eines Betriebssystems zur Unterstützung einer Sprachruntime wie .NET oder Python geeignet ist. Bei Verwendung eines nicht eingeschränkten Geräts ist der ausschlaggebende Aspekt die Vertrautheit mit der Programmiersprache.

### <a name="your-teams-familiarity-with-the-programming-language"></a>Vertrautheit Ihres Teams mit der Programmiersprache

Azure IoT-Geräte-SDKs werden in mehreren Programmiersprachen implementiert, sodass Sie die von Ihnen bevorzugte Programmiersprache auswählen können. Die Geräte-SDKs können auch in andere vertraute sprachspezifische Tools integriert werden. Die Verwendung einer vertrauter Entwicklungssprache und vertrauter Tools ermöglicht es Ihrem Team, den Entwicklungszyklus aus Recherche, Prototyperstellung, Produktentwicklung und laufender Pflege zu optimieren.

Wählen Sie nach Möglichkeit ein SDK, mit dem Ihr Entwicklungsteam vertraut ist. Alle Azure IoT-SDKs sind Open-Source-Tools und stellen mehrere Beispiele bereit, die Ihr Team evaluieren und testen kann, bevor Sie sich auf ein bestimmtes SDK festlegen.

## <a name="how-can-i-get-started"></a>Wie fange ich an?

Sehen Sie sich zunächst die GitHub-Repositorys der Azure-Geräte-SDKs an. Sie können auch eine [Schnellstartanleitung](quickstart-send-telemetry-central.md) durchlaufen, in der gezeigt wird, wie Sie ein SDK verwenden, um Telemetriedaten an Azure IoT zu senden.

Ihre Optionen für den Einstieg hängen von der Art Ihres Geräts ab:
- Verwenden Sie für eingeschränkte Geräte das [Embedded C SDK](#embedded-c-sdk). 
- Bei Geräten mit Azure RTOS können Sie die [Azure RTOS-Middleware](#azure-rtos-middleware) für die Entwicklung verwenden. 
- Bei nicht eingeschränkten Geräten können Sie [ein SDK in einer Programmiersprache Ihrer Wahl auswählen](#unconstrained-device-sdks). 

### <a name="constrained-device-sdks"></a>SDKs für eingeschränkte Geräte
Diese SDKs sind speziell für die Ausführung auf Geräten mit eingeschränkten Compute- oder Speicherressourcen konzipiert. Weitere Informationen zu gängigen Gerätetypen finden Sie in der [Übersicht über Azure IoT-Gerätetypen](concepts-iot-device-types.md).

#### <a name="embedded-c-sdk"></a>Embedded C SDK
* [GitHub-Repository](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/docs/iot)
* [Beispiele](https://github.com/Azure/azure-sdk-for-c/blob/master/sdk/samples/iot/README.md)
* [Referenzdokumentation](https://azure.github.io/azure-sdk-for-c/)
* [Erstellen des Embedded C SDK](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/docs/iot#build)
* [Größendiagramm für eingeschränkte Geräte](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/docs/iot#size-chart)

#### <a name="azure-rtos-middleware"></a>Azure RTOS-Middleware

* [GitHub-Repository](https://github.com/azure-rtos/netxduo/tree/master/addons/azure_iot)
* [Leitfäden mit ersten Schritten](https://github.com/azure-rtos/getting-started) und [weitere Beispiele](https://github.com/azure-rtos/samples)
* [Referenzdokumentation](/azure/rtos/threadx/)

### <a name="unconstrained-device-sdks"></a>SDKs für nicht eingeschränkte Geräte
Diese SDKs können auf Geräten ausgeführt werden, die eine Sprachruntime höherer Ordnung unterstützen. Dazu zählen auch Geräte wie PCs, Raspberry Pi-Geräte und Smartphones. Da sie sich in erster Linie bei der Programmiersprache unterscheiden, können Sie die Bibliothek wählen, die für Ihr Team und Ihr Szenario am besten geeignet ist.

#### <a name="c-device-sdk"></a>C-Geräte-SDK
* [GitHub-Repository](https://github.com/Azure/azure-iot-sdk-c)
* [Beispiele](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples)
* [Pakete](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md#packages-and-libraries)
* [Referenzdokumentation](/azure/iot-hub/iot-c-sdk-ref/)
* [Referenzdokumentation zum Edgemodul](/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h)
* [Kompilieren des C-Geräte-SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#compiling-the-c-device-sdk)
* [Portieren des C-SDK für andere Plattformen](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)
* [Entwicklerdokumentation](https://github.com/Azure/azure-iot-sdk-c/tree/master/doc) mit Informationen zu Crosskompilierung und ersten Schritten auf verschiedenen Plattformen
* [Informationen zum Ressourcenverbrauch beim Azure IoT Hub C SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/c_sdk_resource_information.md)

#### <a name="c-device-sdk"></a>C#-Geräte-SDK

* [GitHub-Repository](https://github.com/Azure/azure-iot-sdk-csharp)
* [Beispiele](https://github.com/Azure/azure-iot-sdk-csharp#samples)
* [Paket](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/)
* [Referenzdokumentation](/dotnet/api/microsoft.azure.devices)
* [Referenzdokumentation zum Edgemodul](/dotnet/api/microsoft.azure.devices.client.moduleclient)

#### <a name="java-device-sdk"></a>Java-Geräte-SDK

* [GitHub-Repository](https://github.com/Azure/azure-iot-sdk-java)
* [Beispiele](https://github.com/Azure/azure-iot-sdk-java/tree/main/device/iot-device-samples)
* [Paket](https://github.com/Azure/azure-iot-sdk-java/blob/main/doc/java-devbox-setup.md#for-the-device-sdk)
* [Referenzdokumentation](/java/api/com.microsoft.azure.sdk.iot.device)
* [Referenzdokumentation zum Edgemodul](/java/api/com.microsoft.azure.sdk.iot.device.moduleclient)

#### <a name="nodejs-device-sdk"></a>Node.js-Geräte-SDK

* [GitHub-Repository](https://github.com/Azure/azure-iot-sdk-node)
* [Beispiele](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples)
* [Paket](https://www.npmjs.com/package/azure-iot-device)
* [Referenzdokumentation](/javascript/api/azure-iot-device/)
* [Referenzdokumentation zum Edgemodul](/javascript/api/azure-iot-device/moduleclient)

#### <a name="python-device-sdk"></a>Python-Geräte-SDK

* [GitHub-Repository](https://github.com/Azure/azure-iot-sdk-python)
* [Beispiele](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples)
* [Paket](https://pypi.org/project/azure-iot-device/)
* [Referenzdokumentation](/python/api/azure-iot-device)
* [Referenzdokumentation zum Edgemodul](/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient)

### <a name="service-sdks"></a>Dienst-SDKs
Azure IoT bietet auch Dienst-SDKs für die Erstellung lösungsseitiger Anwendungen zum Verwalten von Geräten, Gewinnen von Erkenntnissen, Visualisieren von Daten und Ähnlichem. Diese SDKs sind für jeden Azure IoT-Dienst spezifisch und stehen in C#, Java, JavaScript und Python zur Verfügung, um die Entwicklung zu vereinfachen. 

#### <a name="iot-hub"></a>IoT Hub

Mit den IoT Hub-Dienst-SDKs können Sie Anwendungen erstellen, die problemlos mit Ihrer IoT Hub-Instanz interagieren können, um Geräte und die Sicherheit zu verwalten. Diese SDKs können unter anderem zum Senden von Cloud-zu-Gerät-Nachrichten, zum Aufrufen direkter Methoden auf Ihren Geräten sowie zum Aktualisieren von Geräteeigenschaften verwendet werden.

[**Azure IoT Hub**](https://azure.microsoft.com/services/iot-hub/) | [**Schnellstart: Steuern eines mit einer IoT Hub-Instanz verbundenen Geräts (Python)**](../iot-hub/quickstart-control-device.md)

**IoT Hub Service SDK für C#:** [GitHub-Repository](https://github.com/Azure/azure-iot-sdk-csharp/tree/main/iothub/service) | [Paket](https://www.nuget.org/packages/Microsoft.Azure.Devices/) | [Beispiele](https://github.com/Azure/azure-iot-sdk-csharp/tree/main/iothub/service/samples) | [Referenzdokumentation](/dotnet/api/microsoft.azure.devices)

**IoT Hub Service SDK für Java:** [GitHub-Repository](https://github.com/Azure/azure-iot-sdk-java/tree/main/service) | [Paket](https://github.com/Azure/azure-iot-sdk-java/blob/main/doc/java-devbox-setup.md#for-the-service-sdk) | [Beispiele](https://github.com/Azure/azure-iot-sdk-java/tree/main/service/iot-service-samples) | [Referenzdokumentation](/java/api/com.microsoft.azure.sdk.iot.service)

**IoT Hub Service SDK für JavaScript:** [GitHub-Repository](https://github.com/Azure/azure-iot-sdk-node/tree/master/service) | [Paket](https://www.npmjs.com/package/azure-iothub) | [Beispiele](https://github.com/Azure/azure-iot-sdk-node/tree/master/service/samples) | [Referenzdokumentation](/javascript/api/azure-iothub/)

**IoT Hub Service SDK für Python:** [GitHub-Repository](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-hub) | [Paket](https://pypi.python.org/pypi/azure-iot-hub/) | [Beispiele](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-hub/samples) | [Referenzdokumentation](/python/api/azure-iot-hub)

#### <a name="azure-digital-twins"></a>Azure Digital Twins

Azure Digital Twins ist ein PaaS-Angebot (Platform-as-a-Service), mit dem Sie basierend auf digitalen Modellen ganzer Umgebungen Wissensdiagramme erstellen können. Bei einer solchen Umgebung kann es sich um Gebäude, Fabriken, Höfe, Energieversorgungsnetze, Eisenbahnstrecken, Stadien und mehr handeln. Sogar ganze Städte lassen sich modellieren. Diese digitalen Modelle liefern wichtige Einblicke, um Produkte zu verbessern, Vorgänge zu optimieren, Kosten zu senken und die Benutzerfreundlichkeit zu revolutionieren. Azure IoT bietet Dienst-SDKs zur mühelosen Erstellung von Anwendungen, die von Azure Digital Twins profitieren.

[**Azure Digital Twins**](https://azure.microsoft.com/services/digital-twins/) | [**Tutorial: Codieren mit den Azure Digital Twins-APIs**](../digital-twins/tutorial-code.md)

**ADT Service SDK für C#:** [GitHub-Repository](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core) | [Paket](https://www.nuget.org/packages/Azure.DigitalTwins.Core) | [Beispiele](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core/samples) | [Referenzdokumentation](/dotnet/api/overview/azure/digitaltwins/client)

**ADT Service SDK für Java:** [GitHub-Repository](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/digitaltwins/azure-digitaltwins-core) | [Paket](https://search.maven.org/artifact/com.azure/azure-digitaltwins-core/1.0.0/jar) | [Beispiele](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/digitaltwins/azure-digitaltwins-core/src/samples) | [Referenzdokumentation](/java/api/overview/azure/digitaltwins/client)

**ADT Service SDK für Node.js:** [GitHub-Repository](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/digitaltwins/digital-twins-core) | [Paket](https://www.npmjs.com/package/@azure/digital-twins-core) | [Beispiele](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/digitaltwins/digital-twins-core/samples) | [Referenzdokumentation](/javascript/api/@azure/digital-twins-core/)

**ADT Service SDK für Python:** [GitHub-Repository](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/digitaltwins/azure-digitaltwins-core) | [Paket](https://pypi.org/project/azure-digitaltwins-core/) | [Beispiele](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/digitaltwins/azure-digitaltwins-core/samples) | [Referenzdokumentation](/python/api/azure-digitaltwins-core/azure.digitaltwins.core)

#### <a name="device-provisioning-service"></a>Device Provisioning Service

IoT Hub Device Provisioning Service (DPS) ist ein Hilfsdienst für IoT Hub und ermöglicht eine unbeaufsichtigte Just-In-Time-Bereitstellung im richtigen IoT-Hub ganz ohne Benutzereingriff. DPS ermöglicht die skalierbare und sichere Bereitstellung von Millionen von Geräten. Mithilfe der DPS-SDKs können Sie Anwendungen erstellen, die Ihre Geräte durch die Erstellung von Registrierungsgruppen und die Ausführung von Massenvorgängen auf sichere Weise verwalten können.

[**Dokumentation zu Azure IoT Hub Device Provisioning Service (DPS)**](../iot-dps/index.yml) | [**Schnellstart: Registrieren von X.509-Geräten für den Device Provisioning-Dienst per C#**](../iot-dps/quick-enroll-device-x509.md)

**Device Provisioning Service SDK für C#:** [GitHub-Repository](https://github.com/Azure/azure-iot-sdk-csharp/tree/main/provisioning/service) | [Paket](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/) | [Beispiele](https://github.com/Azure/azure-iot-sdk-csharp/tree/main/provisioning/service/samples) | [Referenzdokumentation](/dotnet/api/microsoft.azure.devices.provisioning.service)

**Device Provisioning Service SDK für Java:** [GitHub-Repository](https://github.com/Azure/azure-iot-sdk-java/tree/main/provisioning/provisioning-service-client/src) | [Paket](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot.provisioning/provisioning-service-client) | [Beispiele](https://github.com/Azure/azure-iot-sdk-java/tree/master/provisioning/provisioning-samples#provisioning-service-client) | [Referenzdokumentation](/java/api/com.microsoft.azure.sdk.iot.provisioning.service)

**Device Provisioning Service SDK für Node.js:** [GitHub-Repository](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/service) | [Paket](https://www.npmjs.com/package/azure-iot-provisioning-service) | [Beispiele](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/service/samples) | [Referenzdokumentation](/javascript/api/azure-iot-provisioning-service)

## <a name="next-steps"></a>Nächste Schritte

* [Schnellstart: Senden von Telemetriedaten an IoT Central](quickstart-send-telemetry-central.md)
* [Schnellstart: Senden von Telemetriedaten an IoT Hub](quickstart-send-telemetry-iot-hub.md)
* [Schnellstart: Verbinden eines MXCHIP AZ3166-DevKit mit IoT Central](quickstart-devkit-mxchip-az3166.md)
* Weitere Informationen zu den Vorteilen der Entwicklung mit Azure IoT-SDKs finden Sie [hier](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/).