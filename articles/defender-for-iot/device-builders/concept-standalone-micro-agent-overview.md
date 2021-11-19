---
title: Übersicht über den eigenständigen Micro-Agent (Vorschau)
description: Mit den Sicherheits-Agents von Microsoft Defender für IoT können Sie Sicherheit direkt in Ihre neuen IoT-Geräte und Azure IoT-Projekte integrieren.
ms.date: 11/09/2021
ms.topic: article
ms.openlocfilehash: 9a8600f4a75bb9a4294ab44842fbae22d8c3e6ea
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132335668"
---
# <a name="standalone-micro-agent-overview-preview"></a>Übersicht über den eigenständigen Micro-Agent (Vorschau)

Für diejenigen, die das Internet der Dinge (IoT) implementieren, stellt die Sicherheit ein nahezu universelles Problem dar. IoT-Geräte mit ihren sehr spezifischen Leistungsanforderungen stellen besondere Anforderungen an die Endpunktüberwachung, die Sicherheitsstatusverwaltung und die Bedrohungserkennung.

Mit dem Sicherheits-Agent von Microsoft Defender für IoT können Sie Sicherheit direkt in Ihre neuen IoT-Geräte und Azure IoT-Projekte integrieren. Der Micro-Agent bietet flexible Bereitstellungsoptionen, einschließlich der Möglichkeit für eine Bereitstellung als binäres Paket oder die Änderung von Quellcode. Er ist für IoT-Standardbetriebssysteme wie Linux und Azure RTOS verfügbar.

Der Micro-Agent für Microsoft Defender für IoT stellt Endpunktsichtbarkeit für die Sicherheitsstatusverwaltung, Bedrohungserkennung sowie die Integration in andere Sicherheitstools von Microsoft zugunsten einer einheitlichen Sicherheitsverwaltung bereit.

## <a name="security-posture-management"></a>Sicherheitsstatusverwaltung

Überwachen Sie proaktiv den Sicherheitsstatus ihrer IoT-Geräte. Microsoft Defender für IoT stellt Empfehlungen zum Sicherheitsstatus auf Basis der CIS-Benchmarks sowie gerätespezifische Empfehlungen bereit. Erhalten Sie Einblick in die Betriebssystemsicherheit einschließlich Betriebssystemkonfiguration, Firewallkonfiguration und Berechtigungen.

## <a name="endpoint-iot-and-ot-threat-detection"></a>Bedrohungserkennung an IoT-/OT-Endpunkten

Erkennen Sie Bedrohungen wie Botnets, Brute-Force-Angriffsversuche, Crypto-Miner und verdächtige Netzwerkaktivitäten. Erstellen Sie benutzerdefinierte Warnungen für die wichtigsten Bedrohungen in ihrem speziellen Unternehmen.

## <a name="flexible-distribution-and-deployment-models"></a>Flexible Verteilungs- und Bereitstellungsmodelle

Der Micro-Agent für Microsoft Defender für IoT enthält Quellcode, sodass Sie ihn in die Firmware einbinden oder so anpassen können, dass er nur das von Ihnen Benötigte enthält. Der Micro-Agent ist auch als binäres Paket verfügbar oder in andere Azure IoT-Lösungen direkt integriert.

## <a name="meets-the-needs-of-your-iot-devices-with-minimal-impact"></a>Erfüllt mit minimaler Auswirkung die Anforderungen Ihrer IoT-Geräte

Der Micro-Agent für Microsoft Defender für IoT lässt sich einfach bereitstellen und hat nur minimale Auswirkungen auf die Leistung des Endpunkts. Der Micro-Agent für Defender für IoT bietet folgende Möglichkeiten:

- **Optimieren der Leistung**: Der Micro-Agent für Microsoft Defender für IoT hat einen geringen Speicherbedarf und eine geringe CPU-Auslastung.

- **Plug & Play**: Es gibt keine spezifischen Kernelabhängigkeiten des Betriebssystems, und nicht alle wichtigen IoT-Betriebssysteme müssen unterstützt werden. Der Micro-Agent für Microsoft Defender für IoT erfüllt die Anforderungen Ihrer Geräte dort, wo sie sich befinden.

- **Flexible Bereitstellung**: Als eigenständiger Agent unterstützt der Micro-Agent für Microsoft Defender für IoT verschiedene Verteilungsmodelle und eine flexible Bereitstellung.

## <a name="data-processing-and-residency"></a>Datenverarbeitung und Wohnsitz

> [!NOTE]
> Microsoft Defender für IoT-Datenverarbeitung und -residenz können in einer anderen Region als der IoT Hub-Region erfolgen bzw. sich befinden. Defender für IoT verwendet den Gerätezwilling, nicht maskierte IP-Adressen und zusätzliche Konfigurationsdaten als Teil seiner Sicherheitserkennungslogik.

Defender für IoT-Datenverarbeitung und -residenz können in anderen Regionen als der IoT Hub-Region erfolgen bzw. sich befinden. Die Zuordnung zwischen dem IoT Hub und Defender für IoT-Regionen geschieht so:

- Für einen Hub, der sich in Europa befindet, werden die Daten in der Region *Europa, Western* gespeichert.

- Bei einem Hub, der sich außerhalb Europa befindet, werden die Daten in der Region *East US* gespeichert.

Defender für IoT verwendet standardmäßig den Gerätezwilling, nicht maskierte IP-Adressen und zusätzliche Konfigurationsdaten als Teil seiner Sicherheitserkennungslogik. Um den Gerätezwilling zu deaktivieren und die IP-Adressensammlung zu demaskieren, navigieren Sie zur Seite mit den Einstellungen der Datensammlung.

:::image type="content" source="media/data-collection-settings.png" alt-text="Screenshot der Einstellungsseite für Datensammlungen.":::

Weitere Einzelheiten finden Sie unter [Anpassung Ihrer Defender for IoT-Lösung](concept-micro-agent-configuration.md).

## <a name="next-steps"></a>Nächste Schritte

Informationen Sie sich unter [Authentifizierungsmethoden für den Micro-Agent (Vorschau)](concept-security-agent-authentication.md).
