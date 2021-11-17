---
title: IoT-Micro-Agent von Defender und Gerätezwillinge
description: Hier erfahren Sie mehr über das Konzept von Zwillingen für den IoT-Micro-Agent von Defender und deren Verwendung in Azure Defender für IoT.
ms.topic: conceptual
ms.date: 11/09/2021
ms.openlocfilehash: f8a634eaa3947d08e6ab88b8f05e119fbcf54665
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132278928"
---
# <a name="defender-iot-micro-agent"></a>IoT-Micro-Agent von Defender

In diesem Artikel wird erläutert, wie Defender für IoT Gerätezwillinge und -module verwendet.

## <a name="device-twins"></a>Gerätezwillinge

Für in Azure erstellte IoT-Lösungen spielen Gerätezwillinge eine wichtige Rolle – sowohl bei der Geräteverwaltung als auch bei der Prozessautomatisierung.

Defender für IoT ermöglicht die vollständige Integration in Ihre vorhandene Plattform für die IoT-Geräteverwaltung. Auf diese Weise können Sie Ihren Gerätesicherheitsstatus verwalten und die vorhandenen Funktionen für die Gerätesteuerung nutzen. Die Integration wird erreicht, indem der Mechanismus für IoT Hub-Zwillinge verwendet wird.

Weitere Informationen zum Konzept von Gerätezwillingen in Azure IoT Hub finden Sie [hier](../../iot-hub/iot-hub-devguide-device-twins.md#device-twins).

## <a name="defender-iot-micro-agent-twins"></a>Defender-IoT-Micro-Agent-Zwillinge

Azure Defender für IoT verwaltet für jedes im Dienst enthaltene Gerät einen Zwilling für den IoT-Micro-Agent von Defender.
Der Zwilling für den IoT-Micro-Agent von Defender enthält alle für die Gerätesicherheit relevanten Informationen für jedes einzelne Gerät in Ihrer Lösung.
Die Gerätesicherheitseigenschaften werden in einem dedizierten Zwilling für den IoT-Micro-Agent von Defender verwaltet, um sicherere Kommunikation zu gewährleisten sowie um Updates und Wartungen zu ermöglichen, die weniger Ressourcen erfordern.

Unter [Erstellen eines Zwillings für den IoT-Micro-Agent von Defender](quickstart-create-security-twin.md) und [Konfigurieren von Sicherheits-Agents](how-to-agent-configuration.md) erfahren Sie, wie Sie den Zwilling erstellen, anpassen und konfigurieren können. Weitere Informationen zum Konzept von Modulzwillingen in IoT Hub finden Sie unter [Verstehen und Verwenden von Modulzwillingen in IoT Hub](../../iot-hub/iot-hub-devguide-module-twins.md).

## <a name="see-also"></a>Weitere Informationen

- [Übersicht über Defender für IoT](overview.md)
- [Bereitstellen von Sicherheits-Agents](how-to-deploy-agent.md)
- [Sicherheits-Agent-Authentifizierungsmethoden](concept-security-agent-authentication-methods.md)
