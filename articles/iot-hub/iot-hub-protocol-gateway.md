---
title: Azure IoT-Protokollgateway | Microsoft Docs
description: Erfahren Sie, wie Sie mit einem Azure IoT-Protokollgateway die IoT Hub-Funktionen und die unterstützten Protokolle erweitern, damit Geräte Verbindungen mit Ihrem Hub mithilfe von Protokollen, die nicht nativ von IoT Hub unterstützt werden, herstellen können.
author: eross-msft
ms.author: lizross
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/19/2021
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
ms.openlocfilehash: 6874c4d55395dea7e58d27d0cd7ba1d159e21dee
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132552506"
---
# <a name="support-additional-protocols-for-iot-hub"></a>Unterstützen zusätzlicher Protokolle für IoT Hub

Azure IoT Hub bietet nativ Unterstützung für die Kommunikation über die Protokolle MQTT, AMQP und HTTPS. In einigen Fällen können Geräte oder Bereichsgateways möglicherweise keines dieser Standardprotokolle verwenden und erfordern eine Protokollanpassung. In solchen Fällen können Sie ein benutzerdefiniertes Gateway verwenden. Ein benutzerdefiniertes Gateway dient für den IoT Hub-Datenverkehr als Brücke und ermöglicht damit die Protokollanpassung für IoT Hub-Endpunkte. Sie können das [Azure IoT-Protokollgateway](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md) als benutzerdefiniertes Gateways zum Ermöglichen der Protokollanpassung für IoT Hub verwenden.

## <a name="azure-iot-protocol-gateway"></a>Azure IoT-Protokollgateway

Das Azure IoT-Protokollgateway ist ein Framework zur Protokollanpassung für die hoch skalierbare, bidirektionale Gerätekommunikation mit IoT Hub. Das Protokollgateway ist eine Passthrough-Komponente, die Geräteverbindungen über ein bestimmtes Protokoll akzeptiert. Es fungiert als Brücke für den Datenverkehr zum IoT Hub über AMQP 1.0.

Sie können das Protokollgateway in Azure auf hochgradig skalierbare Weise mithilfe von Azure Service Fabric, Azure Cloud Services-Workerrollen oder virtuellen Windows-Computern bereitstellen. Darüber hinaus kann das Protokollgateway in lokalen Umgebungen wie z. B. Bereichsgateways bereitgestellt werden.

Das Azure IoT-Protokollgateway bietet einen MQTT-Protokolladapter, mit dem Sie bei Bedarf das Verhalten des Protokolls MQTT anpassen können. Da IoT Hub eine integrierte Unterstützung des Protokolls MQTT 3.1.1 bietet, sollten Sie den MQTT-Protokolladapter nur erwägen, wenn Protokollanpassungen erforderlich sind oder bestimmte Anforderungen für zusätzliche Funktionalität gelten.

Der MQTT-Adapter veranschaulicht außerdem das Programmiermodell zum Erstellen von Protokolladaptern für andere Protokolle. Darüber hinaus unterstützt das Programmiermodell des Azure IoT-Protokollgateways die Integration benutzerdefinierter Komponenten für spezielle Verarbeitungsaufgaben – zum Beispiel benutzerdefinierte Authentifizierung, Nachrichtentransformationen, Komprimierung/Dekomprimierung oder Verschlüsselung/Entschlüsselung des Datenverkehrs zwischen Geräten und IoT Hub.

Für mehr Flexibilität werden das Azure IoT-Protokollgateway und die MQTT-Implementierung als Open-Source-Softwareprojekt bereitgestellt. Mit diesem können Sie verschiedene Protokolle und Protokollversionen unterstützen oder die Implementierung für Ihr Szenario anpassen. 

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen über das Azure IoT-Protokollgateway sowie dessen Verwendung und Bereitstellung im Rahmen Ihrer IoT-Lösung finden Sie unter:

* [Repository für das Azure IoT-Protokollgateway auf GitHub](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md)

* [Entwicklungshandbuch für das Azure IoT-Protokollgateway](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/docs/DeveloperGuide.md)

Weitere Informationen zum Planen Ihrer IoT Hub-Bereitstellung finden Sie unter:

* [Vergleich mit Event Hubs](iot-hub-compare-event-hubs.md)

* [Skalierung, Hochverfügbarkeit und Notfallwiederherstellung](iot-hub-scaling.md)

* [Entwicklungsleitfaden für IoT Hub](iot-hub-devguide.md)