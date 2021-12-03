---
title: Defender für loT azureiotsecurity für loT Edge
description: Machen Sie sich mit der Architektur und den Funktionen von Microsoft Defender für IoT azureiotsecurity für IoT Edge vertraut.
ms.topic: conceptual
ms.date: 11/09/2021
ms.openlocfilehash: 55681d731193e0f73a9fb21b415b03d4411ee6fe
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132318957"
---
# <a name="microsoft-defender-for-iot-edge-azureiotsecurity"></a>Microsoft Defender für loT Edge azureiotsecurity

[Azure IoT Edge](../../iot-edge/index.yml) umfasst leistungsstarke Funktionen zum Verwalten und Ausführen von Geschäftsworkflows am Edge. Seine Schlüsselrolle in IoT-Umgebungen macht IoT Edge zu einem besonders attraktiven Ziel für böswillige Akteure.

Defender für IoT azureiotsecurity stellt eine umfassende Sicherheitslösung für Ihre IoT Edge-Geräte bereit. Das Defender für IoT-Modul sammelt, aggregiert und analysiert Sicherheitsrohdaten von Ihrem Betriebs- und Containersystem und gibt verwertbare Sicherheitsempfehlungen und -warnungen aus.

Ähnlich wie die Defender für IoT-Sicherheits-Agents für IoT-Geräte ist das Defender für IoT Edge-Modul durch seinen Modulzwilling hochgradig anpassbar. Weitere Informationen finden Sie unter [Konfigurieren Ihres Agents](how-to-agent-configuration.md).

Defender für IoT azureiotsecurity für IoT Edge bietet die folgenden Funktionen:

- Sammeln von Sicherheitsrohereignissen vom zugrunde liegenden Betriebssystem (Linux) und den IoT Edge-Containersystemen

  Weitere Informationen zu verfügbaren Sicherheitsdatensammlern finden Sie unter [Defender für IoT-Agent-Konfiguration](how-to-agent-configuration.md).

- Analyse von IoT Edge-Bereitstellungsmanifesten

- Aggregieren von Sicherheitsrohereignissen in Meldungen, die über [IoT Edge-Hub](../../iot-edge/iot-edge-runtime.md#iot-edge-hub) gesendet werden.

- Entfernen Sie die Konfiguration mit Hilfe des azureiotsecurity-Zwillings.

  Weitere Informationen finden Sie unter [Konfigurieren eines Defender für IoT-Agents](how-to-agent-configuration.md).

Defender für loT azureiotsecurity für loT Edge läuft in einem privilegierten Modus unter loT Edge. Der privilegierte Modus ist erforderlich, damit das Modul das Betriebssystem und andere IoT Edge-Module überwachen kann.

## <a name="module-supported-platforms"></a>Vom Modul unterstützte Plattformen

Defender f“rr loT azureiotsecurity für loT Edge ist derzeit nur für Linux verfügbar.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie etwas über die Architektur und die Möglichkeiten von Defender für loT azureiotsecurity für loT Edge gelernt.

Um mit der Defender für IoT-Bereitstellung fortzufahren, lesen Sie die folgenden Artikel:

- Bereitstellung von [azureiotsecurity für loT Edge](how-to-deploy-edge.md)
- Erfahren Sie, wie Sie den [Defender für IoT-Micro-Agent konfigurieren](how-to-agent-configuration.md).
- Erfahren Sie, wie Sie [den Defender für IoT-Dienst in Ihrer IoT Hub-Instanz aktivieren](quickstart-onboard-iot-hub.md).
- Unter [für IoT – Häufig gestellte Fragen](resources-agent-frequently-asked-questions.md) erfahren Sie mehr über den Dienst.
