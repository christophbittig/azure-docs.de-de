---
title: Übersicht über den eigenständigen Micro-Agent (Vorschau)
description: Mit den Sicherheits-Agents von Azure Defender für IoT können Sie Sicherheit direkt in Ihre neuen IoT-Geräte und Azure IoT-Projekte integrieren.
ms.date: 11/04/2021
ms.topic: article
ms.openlocfilehash: 2dfdc4b1d3696a3887e251d6ccd3213f5bc2bc0c
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131577066"
---
# <a name="standalone-micro-agent-overview-preview"></a>Übersicht über den eigenständigen Micro-Agent (Vorschau)

Für diejenigen, die das Internet der Dinge (IoT) implementieren, stellt die Sicherheit ein nahezu universelles Problem dar. IoT-Geräte mit ihren sehr spezifischen Leistungsanforderungen stellen besondere Anforderungen an die Endpunktüberwachung, die Sicherheitsstatusverwaltung und die Bedrohungserkennung.

Mit den Sicherheits-Agents von Azure Defender für IoT können Sie Sicherheit direkt in Ihre neuen IoT-Geräte und Azure IoT-Projekte integrieren. Der Micro-Agent ist mit flexiblen Bereitstellungsoptionen ausgestattet. Dazu zählen auch die Bereitstellung als binäres Paket oder Änderungen des Quellcodes. Der Micro-Agent ist zudem für IoT-Standardbetriebssysteme wie Linux und Azure RTOS verfügbar.

Der Micro-Agent von Azure Defender für IoT stellt Endpunktsichtbarkeit für die Sicherheitsstatusverwaltung, Bedrohungserkennung sowie die Integration in andere Sicherheitstools von Microsoft zugunsten einer einheitlichen Sicherheitsverwaltung bereit.

## <a name="security-posture-management"></a>Sicherheitsstatusverwaltung

Überwachen Sie proaktiv den Sicherheitsstatus ihrer IoT-Geräte. Azure Defender für IoT stellt auf Basis der CIS-Benchmarks Empfehlungen zum Sicherheitsstatus sowie gerätespezifische Empfehlungen bereit. Erhalten Sie Einblick in die Betriebssystemsicherheit einschließlich Betriebssystemkonfiguration, Firewallkonfiguration und Berechtigungen.

## <a name="endpoint-iot-and-ot-threat-detection"></a>Bedrohungserkennung an IoT-/OT-Endpunkten

Erkennen Sie Bedrohungen wie Botnets, Brute-Force-Angriffsversuche, Crypto-Miner und verdächtige Netzwerkaktivitäten. Erstellen Sie benutzerdefinierte Warnungen für die wichtigsten Bedrohungen in ihrem speziellen Unternehmen.

## <a name="flexible-distribution-and-deployment-models"></a>Flexible Verteilungs- und Bereitstellungsmodelle

Der Micro-Agent von Azure Defender für IoT enthält Quellcode, sodass Sie ihn in die Firmware einbinden oder so anpassen können, dass er nur das enthält, was Sie benötigen. Der Micro-Agent ist auch als binäres Paket verfügbar oder direkt in andere Azure IoT-Lösungen integriert.

## <a name="meets-the-needs-of-your-iot-devices-with-minimal-impact"></a>Erfüllt mit minimaler Auswirkung die Anforderungen Ihrer IoT-Geräte

Der Micro-Agent von Azure Defender für IoT lässt sich einfach bereitstellen und hat nur minimale Auswirkungen auf die Leistung des Endpunkts. Der Micro-Agent für Defender für IoT bietet folgende Möglichkeiten:

- **Optimieren der Leistung**: Der Micro-Agent von Azure Defender für IoT hat einen geringen Platzbedarf und eine niedrige CPU-Auslastung.  

- **Plug & Play**: Es gibt keine spezifischen Kernelabhängigkeiten des Betriebssystems, und nicht alle wichtigen IoT-Betriebssysteme müssen unterstützt werden. Der Micro-Agent von Azure Defender für IoT erfüllt Ihre Geräte dort, wo sie sich befinden.

- **Flexible Bereitstellung**: Als eigenständiger Agent unterstützt der Micro-Agent von Azure Defender für IoT verschiedene Verteilungsmodelle und eine flexible Bereitstellung.

## <a name="data-processing-and-residency"></a>Datenverarbeitung und Wohnsitz

> [!Note]
> Azure Defender für IoT-Datenverarbeitung und -Residenz können in einer Region stattfinden, die sich von der IoT Hub-Region unterscheidet. Azure Defender für IoT verwendet Gerätezwillinge, unmaskierte IP und zusätzliche Konfigurationsdaten als Teil seiner Sicherheitserkennungslogik.

Azure Defender für die IoT-Datenverarbeitung, und der Wohnsitz kann in Regionen liegen, die sich von der Region des IoT-Hubs unterscheiden. Die Zuordnung zwischen dem IoT Hub und Azure Defender für IoT-Regionen ist wie folgt;

- Für einen Hub, der sich in Europa befindet, werden die Daten in der Region *Europa, Western* gespeichert.

- Bei einem Hub, der sich außerhalb Europa befindet, werden die Daten in der Region *East US* gespeichert.

Azure Defender für IoT verwendet standardmäßig den Gerätezwilling, unmaskierte IP-Adressen und zusätzliche Konfigurationsdaten als Teil seiner Sicherheitserkennungslogik. Um den Gerätezwilling zu deaktivieren und die IP-Adressensammlung zu demaskieren, navigieren Sie zur Seite mit den Einstellungen der Datensammlung.

:::image type="content" source="media/data-collection-settings.png" alt-text="Screenshot der Einstellungsseite für Datensammlungen.":::

Weitere Einzelheiten finden Sie unter [Anpassung Ihrer Defender for IoT-Lösung](concept-micro-agent-configuration.md).

## <a name="next-steps"></a>Nächste Schritte

Informationen Sie sich unter [Authentifizierungsmethoden für den Micro-Agent (Vorschau)](concept-security-agent-authentication.md).
