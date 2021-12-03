---
title: Konzeptionelle Erläuterung der Grundlagen des Defender-IoT-Micro-Agent für Azure RTOS
description: Machen Sie sich mit den grundlegenden Begriffen und dem Workflow für den Defender-IoT-Micro-Agent für Azure RTOS vertraut.
ms.topic: conceptual
ms.date: 11/09/2021
ms.openlocfilehash: 084c7130c9fe033fb96fcb0a2e3c2820ef2c77e5
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132278947"
---
# <a name="defender-iot-micro-agent-for-azure-rtos"></a>Defender-IoT-Micro-Agent für Azure RTOS

Verwenden Sie diesen Artikel, um ein besseres Verständnis des Defender-IoT-Micro-Agent für Azure RTOS zu erlangen, einschließlich Funktionen und Vorteilen sowie Links zu relevanten Konfigurations- und Referenzressourcen. 

## <a name="azure-rtos-iot-defender-iot-micro-agent"></a>Defender-IoT-Micro-Agent für Azure RTOS

Der Defender-IoT-Micro-Agent für Azure RTOS bietet eine umfassende Sicherheitslösung für Azure RTOS-Geräte als Teil des NetX Duo-Angebots. Innerhalb des NetX Duo-Angebots wird Azure RTOS als Bestandteil des Azure IoT-Defender-IoT-Micro-Agent ausgeliefert, und dieser bietet nach seiner Aktivierung Abdeckung für häufige Bedrohungen auf Geräten mit Echtzeitbetriebssystem.

Der Defender-IoT-Micro-Agent für Azure RTOS wird im Hintergrund ausgeführt und bietet eine nahtlose Benutzererfahrung, während Sicherheitsmeldungen mit den eindeutigen Verbindungen der einzelnen Kunden an deren IoT Hub gesendet werden. Der Defender-IoT-Micro-Agent für Azure RTOS ist standardmäßig aktiviert.  

## <a name="azure-rtos-netx-duo"></a>Azure RTOS NetX Duo

Azure RTOS NetX Duo eist ein erweiterter, TCP/IP-Netzwerkstapel auf Branchenniveau, der speziell für tief eingebettete Echtzeit- und IoT-Anwendungen entwickelt wurde. Azure RTOS NetX Duo ist ein Doppelnetzwerkstack für IPv4 und IPv6, der einen umfangreichen Satz von Protokollen bereitstellt, einschließlich Sicherheit und Cloud. Erfahren Sie mehr über [Azure RTOS NetX Duo](/azure/rtos/netx-duo/)-Lösungen.

Das Modul bietet die folgenden Funktionen:

- **Erkennung bösartiger Netzwerkaktivitäten**
- **Baselines für Geräteverhalten auf Grundlage von benutzerdefinierten Warnungen**
- **Verbesserung der Gerätesicherheit**

## <a name="defender-iot-micro-agent-for-azure-rtos-architecture"></a>Architektur des Defender-IoT-Micro-Agent für Azure RTOS

Der Defender-IoT-Micro-Agent für Azure RTOS wird von der Azure IoT-Middlewareplattform initialisiert und verwendet IoT Hub-Clients, um Sicherheitstelemetriedaten an den Hub zu senden.

:::image type="content" source="media/architecture/security-module-state-diagram.png" alt-text="Zustandsdiagramm und Informationsfluss zum Azure IoT-Defender-IoT-Micro-Agent":::

Der Defender-IoT-Micro-Agent für Azure RTOS überwacht die folgende Geräteaktivitäten und -informationen mithilfe dreier Collectors:
- Gerätenetzwerkaktivitäten **TCP**, **UDP** und **ICM**
- Systeminformationen als **ThreadX**- und **NetX Duo**-Versionen
- Taktereignisse

Jeder Collector ist mit einer Prioritätsgruppe verknüpft, und jede Prioritätsgruppe besitzt ein eigenes Intervall mit den möglichen Werten **Niedrig**, **Mittel** und **Hoch**. Die Intervalle beeinflussen das Zeitintervall, in dem die Daten erfasst und gesendet werden.

Jedes Zeitintervall ist konfigurierbar, und die IoT-Connectors können aktiviert und deaktiviert werden, um [Ihre Lösung noch weiter anzupassen](how-to-azure-rtos-security-module.md). 

## <a name="supported-security-alerts-and-recommendations"></a>Unterstützte Sicherheitswarnungen und -empfehlungen

Der Defender-IoT-Micro-Agent für Azure RTOS unterstützt spezifische Sicherheitswarnungen und -empfehlungen. Stellen Sie sicher, dass Sie die [relevanten Warnungs- und Empfehlungswerte für Ihren Dienst überprüfen und anpassen](concept-rtos-security-alerts-recommendations.md), nachdem Sie die Erstkonfiguration abgeschlossen haben.

## <a name="ready-to-begin"></a>Bereit für den Einstieg?

Defender-IoT-Micro-Agent für Azure RTOS steht als kostenloser Download für Ihre IoT-Geräte zur Verfügung. Der Defender für IoT-Clouddienst ist mit einer 30-tägigen Testversion pro Azure-Abonnement verfügbar. [Laden Sie den Defender-IoT-Micro-Agent jetzt herunter](https://github.com/azure-rtos/azure-iot-preview/releases) und legen Sie gleich los. 

## <a name="next-steps"></a>Nächste Schritte

- Erste Schritte mit dem Defender-IoT-Micro-Agent für Azure RTOS: [Voraussetzungen und Einrichtung](quickstart-azure-rtos-security-module.md).
- Weitere Informationen zur Unterstützung des Defender-IoT-Micro-Agent für Azure RTOS für [Sicherheitswarnungen und -empfehlungen](concept-rtos-security-alerts-recommendations.md). 
- Ziehen Sie die [Referenz-API](azure-rtos-security-module-api.md) für den Defender-IoT-Micro-Agent für Azure RTOS zurate.
