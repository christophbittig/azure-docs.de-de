---
title: Sicherheits-Agents
description: Machen Sie sich mit dem Verständnis, der Konfiguration, der Bereitstellung und der Verwendung von Microsoft Defender für IoT-Sicherheitsdienstagenten auf Ihren IoT-Geräten vertraut.
ms.topic: conceptual
ms.date: 11/09/2021
ms.openlocfilehash: 86c0295726989d142e10e01e8f4950cfcf385126
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132331545"
---
# <a name="get-started-with-microsoft-defender-for-iot-device-micro-agents"></a>Erste Schritte mit Micro-Agents für Azure Defender für IoT-Geräte

Sicherheits-Agents für Defender für IoT bieten erweiterte Sicherheitsfunktionen wie etwa die Überwachung bewährter Methoden für die Betriebssystemkonfiguration. Übernehmen Sie mit einem einzigen Dienst die Kontrolle über den Bedrohungsschutz und den Sicherheitsstatus Ihres Gerätefelds.

Die Defender für IoT-Sicherheits-Agents verarbeiten unformatierte Ereigniserfassungen aus dem Betriebssystem des Geräts, ermöglichen eine Ereignisaggregation zur Kostensenkung und erlauben die Konfiguration über einen Modulzwilling des Geräts. Sicherheitsmeldungen werden über Ihre IoT Hub-Instanz an die Analysedienste von Defender für IoT gesendet.

Verwenden Sie den folgenden Workflow, um Ihre Defender für IoT-Sicherheits-Agents bereitzustellen und zu testen:

1. [Aktivieren des Defender für IoT-Diensts für Ihre IoT Hub-Instanz](quickstart-onboard-iot-hub.md)

1. Wenn für Ihre IoT Hub-Instanz keine Geräte registriert sind, [registrieren Sie ein neues Gerät](/previous-versions/azure/iot-accelerators/iot-accelerators-device-simulation-overview).

1. [Erstellen Sie einen DefenderIotMicroAgent-Modulzwilling](quickstart-create-micro-agent-module-twin.md) für Ihre Geräte.

1. Um den Agent auf einem simulierten Azure-Gerät anstatt auf einem echten Gerät zu installieren, [starten Sie eine neue Azure-VM](../../virtual-machines/linux/quick-create-portal.md).

1. [Stellen Sie einen Sicherheits-Agent von Defender für IoT auf Ihrem IoT-Gerät](how-to-deploy-linux-cs.md) oder einer neuen VM bereit.

1. Folgen Sie den Anweisungen für [trigger_events](https://aka.ms/iot-security-github-trigger-events), um ein Baselineereignis des Betriebssystems auszuführen.

1. Überprüfen Sie die Empfehlungen von Defender für IoT als Reaktion auf das simulierte Fehlschlagen der Prüfung der Betriebssystem-Baseline im vorherigen Schritt. Beginnen Sie die Überprüfung 30 Minuten nach Ausführung des Skripts.

## <a name="next-steps"></a>Nächste Schritte

- [Quickstart: Configure your IoT solution](quickstart-configure-your-solution.md) (Schnellstart: Konfigurieren Ihrer IoT-Lösung)
- [Erstellen von Defender-IoT-Micro-Agents](quickstart-create-security-twin.md)
- [Quickstart: Create custom alerts](quickstart-create-custom-alerts.md) (Schnellstart: Erstellen benutzerdefinierter Benachrichtigungen)
- [Select and deploy a security agent on your IoT device](how-to-deploy-agent.md) (Auswählen und Bereitstellen eines Sicherheits-Agents auf Ihrem IoT-Gerät)
