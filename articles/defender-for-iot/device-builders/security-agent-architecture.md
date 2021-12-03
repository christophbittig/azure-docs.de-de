---
title: Übersicht über Sicherheits-Agents
description: In dieser Schnellstartanleitung erhalten Sie grundlegende Informationen zur Sicherheits-Agent-Architektur für die im Dienst „Microsoft Defender für IoT“ verwendeten Agents.
ms.topic: quickstart
ms.date: 11/09/2021
ms.openlocfilehash: 0fba4f592bdfa0ca055fcccb4808bb8d5dbd37b5
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132281758"
---
# <a name="quickstart-security-agent-reference-architecture"></a>Schnellstart: Sicherheits-Agent-Referenzarchitektur

Microsoft Defender für IoT bietet eine Referenzarchitektur für Sicherheits-Agents, die Sicherheitsdaten über IoT Hub protokollieren, verarbeiten, aggregieren und senden.

Sicherheits-Agents sind für den Einsatz in einer eingeschränkten IoT-Umgebung konzipiert und können in Bezug auf die Werte, die sie im Vergleich zu den von ihnen verbrauchten Ressourcen bereitstellen, sehr flexibel angepasst werden.

Sicherheits-Agents unterstützen die folgenden Features:

- Authentifizieren mit vorhandener Geräteidentität oder einer dedizierten Modulidentität. Weitere Informationen finden Sie unter  [Authentifizierungsmethoden des Sicherheits-Agents](concept-security-agent-authentication-methods.md).

- Sammeln von Rohdaten zu Sicherheitsereignissen aus dem zugrunde liegenden Betriebssystem (Linux, Windows). Weitere Informationen zu verfügbaren Sicherheitsdatensammlern finden Sie unter [für die IoT-Agent-Konfiguration](how-to-agent-configuration.md).

- Aggregieren von Rohdaten zu Sicherheitsereignissen in Nachrichten, die über IoT Hub gesendet werden.

- Remotekonfigurieren durch Verwendung des Modulzwillings **azureiotsecurity**. Weitere Informationen finden Sie unter [Konfigurieren eines Defender für IoT-Agents](how-to-agent-configuration.md).

Sicherheits-Agents von Defender für IoT werden als Open-Source-Projekte entwickelt und sind über GitHub verfügbar:

- [Defender für IoT – C-basierter Agent](https://github.com/Azure/Azure-IoT-Security-Agent-C)
- [Defender für IoT – C#-basierter Agent](https://github.com/Azure/Azure-IoT-Security-Agent-CS)

## <a name="prerequisites"></a>Voraussetzungen

- Keine

## <a name="agent-supported-platforms"></a>Vom Agent unterstützte Plattformen

Defender für IoT bietet verschiedene Installer-Agents für 32-Bit- und 64-Bit-Windows sowie für 32-Bit- und 64-Bit-Linux. Stellen Sie anhand der folgenden Tabelle sicher, dass Sie den richtigen Agent-Installer für jedes Ihrer Geräte verwenden:

| Aufbau | Linux | Windows | Details |
|--|--|--|--|
| 32-Bit | C | C# |  |
| 64-Bit | C# oder C | C# | Wir empfehlen, den C-Agent für Geräte mit eingeschränkteren oder minimalen Geräteressourcen zu verwenden. |

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie eine allgemeine Übersicht über die Architektur des Defender-IoT-Micro-Agents von Defender für IoT und die verfügbaren Installationsprogramme erhalten. Sehen Sie sich die verfügbaren Authentifizierungsmethoden des Sicherheits-Agents an, um erste Schritte bei der Bereitstellung von Defender für IoT auszuführen.

> [!div class="nextstepaction"]
> [Sicherheits-Agent-Authentifizierungsmethoden](concept-security-agent-authentication-methods.md)
