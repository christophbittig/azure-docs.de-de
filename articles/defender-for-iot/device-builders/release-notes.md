---
title: Neuerungen bei Azure Defender für IoT für Gerätehersteller
description: Hier erfahren Sie etwas über die neuesten Releases und Features von Defender für IoT für Gerätehersteller.
ms.topic: overview
ms.date: 11/09/2021
ms.openlocfilehash: e49dbbdf1cb0000e69157b57d63e86c9d85c5e6f
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132278879"
---
# <a name="whats-new-in-azure-defender-for-iot-for-device-builders"></a>Neuerungen bei Azure Defender für IoT für Gerätehersteller  

[!INCLUDE [Banner for top of topics](../includes/banner.md)]

In diesem Artikel sind neue und verbesserte Features von Defender für IoT aufgeführt.

Die genannten Features befinden sich in der Vorschauphase. Die [ergänzenden Bestimmungen für Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen, die für Azure-Features gelten, die sich in der Beta- oder Vorschauversion befinden bzw. anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.

## <a name="versioning-and-support-for-azure-defender-for-iot"></a>Versionsverwaltung und Unterstützung für Azure Defender für IoT

Nachfolgend sind die Unterstützung, die Breaking Change-Richtlinien für Defender für IoT sowie die derzeit verfügbaren Versionen von Azure Defender für IoT aufgeführt.

## <a name="september-2021"></a>September 2021

**Version 3.11**:

- **[Anmeldungssammler](concept-event-aggregation.md#login-collector-event-based-collector)** : Der Anmeldungssammler sammelt Benutzeranmeldungen, -abmeldungen und fehlgeschlagene Anmeldeversuche. Beispiel: SSH und TELNET.

- **[Systeminformationssammler](concept-event-aggregation.md#system-information-trigger-based-collector)** : Der Systeminformationssammler sammelt Informationen im Zusammenhang mit dem Betriebssystem des Geräts und Hardwaredetails.

- **[Ereignisaggregation](concept-event-aggregation.md#how-does-event-aggregation-work)** : Der Defender für IoT-Agent aggregiert Ereignisse wie Prozess, Anmeldung und Netzwerkereignisse, die die Anzahl der gesendeten Nachrichten und die Kosten reduzieren, während gleichzeitig die Sicherheit Ihres Geräts gewährleistet wird.  

- **[Zwillingskonfiguration](concept-micro-agent-configuration.md)** : Das Verhalten des Micro-Agents wird durch eine Reihe von Modulzwillingeigenschaften konfiguriert. (z. B.: Häufigkeit des Sendens von Ereignissen und Aggregationsmodus). Sie können den Micro-Agent so konfigurieren, dass er Ihren Anforderungen am besten entspricht.

## <a name="march-2021"></a>März 2021

### <a name="device-builder---new-micro-agent-public-preview"></a>Gerätehersteller: neuer Mikro-Agent (öffentliche Vorschau)

Ein neues Geräteherstellermodul ist verfügbar. Das Modul, das als Micro-Agent bezeichnet wird, ermöglicht Folgendes:

- **Integration in Azure IoT Hub und Azure Defender für IoT** : Integrieren Sie mehr Endpunktsicherheit direkt in Ihre IoT-Geräte, indem Sie sie mit der Überwachungsoption von Azure IoT Hub und Azure Defender für IoT verbinden.

- **Flexible Bereitstellungsoptionen mit Unterstützung für IoT-Standardbetriebssysteme** können entweder als binäres Paket oder als modifizierbarer Quellcode bereitgestellt werden, mit Unterstützung für IoT-Standardbetriebssysteme wie Linux und Azure RTOS.

- **Minimale Ressourcenanforderungen ohne Kernelabhängigkeiten vom Betriebssystem**: geringer Platzbedarf, geringe CPU-Auslastung und keine Kernelabhängigkeiten vom Betriebssystem.

- **Verwaltung des Sicherheitsstatus**: Sie können den Sicherheitsstatus Ihrer IoT-Geräte aktiv verwalten.

- **Kontinuierliche Erkennung von IoT/OT-Bedrohungen in Echtzeit**: Erkennung von Bedrohungen wie Botnets, Brute-Force-Angriffen, Krypto-Minern und verdächtigen Netzwerkaktivitäten

Die veraltete Dokumentation zum Defender für IoT-Micro-Agent wird in den Ordner *Agent-basierte Lösung für Gerätehersteller > Classic* verschoben.

Diese Featuregruppe ist in der aktuellen öffentlichen Vorschauversion verfügbar.

## <a name="next-steps"></a>Nächste Schritte

[Info zu Agent-basierten Lösungen für Gerätehersteller](architecture-agent-based.md)
