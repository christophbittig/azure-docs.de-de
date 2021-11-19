---
title: Agent-Portfolioübersicht und Betriebssystemunterstützung (Vorschau)
description: Microsoft Defender für IoT bietet ein umfangreiches Portfolio an gerätetypbasierten Agents.
ms.date: 11/09/2021
ms.topic: conceptual
ms.openlocfilehash: fbad1c486a9bf553b7f7effffe8dcf5e12b5d03a
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132319014"
---
# <a name="agent-portfolio-overview-and-os-support-preview"></a>Agent-Portfolioübersicht und Betriebssystemunterstützung (Vorschau)

Microsoft Defender für IoT bietet ein umfangreiches Portfolio an gerätetypbasierten Agents.

## <a name="standalone-agent"></a>Eigenständiger Agent

Der eigenständige Agent deckt die meisten Linux-Betriebssysteme ab. Diese können als binäres Paket oder als Quellcode bereitgestellt werden, der in die Firmware integriert werden kann, und ermöglichen Änderungen und Anpassungen gemäß den Kundenanforderungen. Nachfolgend finden Sie Beispiele für unterstützte Betriebssysteme:

| Betriebssystem | AMD64 | ARM32v7 | ARM64 |
|--|--|--|--|
| Debian 9 | ✓ | ✓ | |
| Ubuntu 18.04 | ✓ |  | ✓ |
| Ubuntu 20.04 | ✓ |  | |

Eine detailliertere Ansicht der Betriebssystemabhängigkeiten des Micro-Agents finden Sie unter [Linux-Abhängigkeiten](concept-micro-agent-linux-dependencies.md#linux-dependencies).

Wenn Sie weitere Informationen oder Angaben zu unterstützten Betriebssystemen benötigen oder Zugriff auf den Quellcode anfordern möchten, um ihn in die Firmware des Geräts integrieren zu können, wenden Sie sich entweder an Ihren Konto-Manager, oder senden Sie eine E-Mail an <defender_micro_agent@microsoft.com>.

## <a name="azure-rtos-micro-agent"></a>Micro-Agent für Azure RTOS

Der Micro-Agent für Microsoft Defender für IoT bietet eine umfassende und einfache Sicherheitslösung für Geräte, die Azure RTOS verwenden. Der Micro-Agent für Microsoft Defender für IoT deckt allgemeine Bedrohungen und potenziell schädliche Aktivitäten auf Geräten mit Echtzeitbetriebssystem (Real-Time Operating System, RTOS) ab. Der Micro-Agent ist in die Komponente „Azure RTOS NetX Duo“ integriert und überwacht die Netzwerkaktivität des Geräts.

Der Micro-Agent für Microsoft Defender für IoT ist in die Komponente „Azure RTOS NetX Duo“ integriert und überwacht die Netzwerkaktivität des Geräts. Der Micro-Agent besteht aus einer umfassenden Lightweight-Sicherheitslösung zum Schutz vor allgemeinen Bedrohungen und potenziell schädlichen Aktivitäten auf Geräten mit Echtzeitbetriebssystem (Real-Time Operating System, RTOS).

## <a name="next-steps"></a>Nächste Schritte

Lernen Sie mehr über [Linux-Abhängigkeiten des Micro-Agents (Vorschau)](concept-micro-agent-linux-dependencies.md).

Weitere Informationen finden Sie in der [Übersicht über den eigenständigen Micro-Agent (Vorschau)](concept-standalone-micro-agent-overview.md).
