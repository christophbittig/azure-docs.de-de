---
title: Klassische Ereignisaggregation für den Defender-IoT-Micro-Agent
description: Weitere Informationen zur Ereignisaggregation in Defender für IoT.
ms.topic: conceptual
ms.date: 11/09/2021
ms.openlocfilehash: 2e7ad46dd7e94c955b722e0d7a3f06237828c9b4
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132306300"
---
# <a name="defender-iot-micro-agent-classic-event-aggregation"></a>Klassische Ereignisaggregation für den Defender-IoT-Micro-Agent

Defender für IoT-Sicherheits-Agents erfassen Daten und Systemereignisse von Ihrem lokalen Gerät und senden diese Daten zur Verarbeitung und Analyse an die Azure-Cloud. Der Sicherheits-Agent sammelt viele Arten von Geräteereignissen, einschließlich neuer Prozess- und neuer Verbindungsereignisse. Sowohl neue Prozess- als auch neue Verbindungsereignisse können innerhalb einer Sekunde auf einem Gerät regelmäßig auftreten und sind wichtig für eine stabile und umfassende Sicherheit. Allerdings können aufgrund der Anzahl der Nachrichten, die die Sicherheits-Agents deshalb senden müssen, Ihr IoT Hub-Kontingent und die Kostengrenzen möglicherweise schnell erreicht oder überschritten werden. Diese Ereignisse enthalten jedoch äußerst wertvolle Sicherheitsinformationen, die für den Schutz Ihres Geräts von entscheidender Bedeutung sind.

Azure Defender für IoT-Agents aggregiert diese Ereignistypen, um das zusätzliche Kontingent und die Kosten zu reduzieren und Ihre Geräte gleichzeitig geschützt zu halten.

Die Ereignisaggregation ist standardmäßig **aktiviert**. Sie kann jederzeit manuell **deaktiviert** werden, obwohl von dieser Vorgehensweise abgeraten wird.

Die Aggregation steht derzeit für die folgenden Ereignistypen zur Verfügung:

* ProcessCreate
* ConnectionCreate
* ProcessTerminate (nur Windows)

## <a name="how-does-event-aggregation-work"></a>Wie funktioniert die Ereignisaggregation?

Wenn die Ereignisaggregation **aktiviert** ist, aggregieren Defender für IoT-Agents Ereignisse für den Intervallzeitraum oder das Zeitfenster.
Nachdem der Intervallzeitraum abgelaufen ist, sendet der Agent die aggregierten Ereignisse zur weiteren Analyse an die Azure-Cloud.
Die aggregierten Ereignisse werden im Arbeitsspeicher gespeichert, bis sie an die Azure-Cloud gesendet werden.

Um den Speicherbedarf des Agents zu verringern, erhöht der Agent immer dann die Trefferanzahl dieses spezifischen Ereignisses, wenn er ein identisches Ereignis zu einem Ereignis erfasst, das im Arbeitsspeicher bereits enthalten ist. Wenn das Aggregationszeitfenster überschritten wird, sendet der Agent die Trefferanzahl für jeden spezifischen Ereignistyp, der aufgetreten ist. Die Ereignisaggregation ist einfach die Aggregation der Trefferanzahl für die einzelnen erfassten Ereignistypen.

Ereignisse werden nur dann als identisch betrachtet, wenn die folgenden Bedingungen erfüllt sind:

* ProcessCreate-Ereignisse: Wenn **commandLine**, **executable**, **username** und **userid** identisch sind
* ConnectionCreate-Ereignisse: **commandLine**, **userId**, **direction**, **local address**, **remote address**, **protocol** und **destination port** sind identisch
* „ProcessTerminate“-Ereignisse – Wenn **executable** und **exit status** identisch sind

### <a name="working-with-aggregated-events"></a>Arbeiten mit aggregierten Ereignissen

Während der Aggregation werden nicht aggregierte Ereigniseigenschaften verworfen und in Log Analytics mit dem Wert „0“ angezeigt.

* „ProcessCreate“-Ereignisse – **processId** und **parentProcessId** sind auf „0“ festgelegt.
* „ConnectionCreate“-Ereignisse – **processId** und **source port** sind auf „0“ festgelegt.

## <a name="event-aggregation-based-alerts"></a>Warnungen, die auf Ereignisaggregation basieren

Nach der Analyse erstellt Defender für IoT Sicherheitswarnungen für verdächtige aggregierte Ereignisse. Aus aggregierten Ereignissen erstellte Warnungen werden für jedes aggregierte Ereignis nur einmal angezeigt.

Die Start- und Endzeit für die Aggregation sowie die Trefferanzahl für jedes Ereignis werden im Ereignisfeld **ExtraDetails** in Log Analytics zur Verwendung während Untersuchungen protokolliert.

Jedes aggregierte Ereignis stellt einen 24-Stunden-Zeitraum von gesammelten Warnungen dar. Über das Menü der Ereignisoptionen oben links in jedem Ereignis können Sie jedes einzelne aggregierte Ereignis **schließen**.

## <a name="event-aggregation-twin-configuration"></a>Konfiguration der Ereignisaggregation von Modulzwillingen

Nehmen Sie Änderungen an der Konfiguration der Ereignisaggregation von Defender für IoT im [Agentkonfigurationsobjekt](how-to-agent-configuration.md) der Modulzwillingsidentität des Moduls **azureiotsecurity** vor.

| Konfigurationsname | Mögliche Werte | Details | Bemerkungen |
|:-----------|:---------------|:--------|:--------|
| aggregationEnabledProcessCreate | boolean | Ereignisaggregation für „ProcessCreate“-Ereignisse aktivieren/deaktivieren |
| aggregationIntervalProcessCreate | ISO8601 TimeSpan-Zeichenfolge | Aggregationsintervall für „ProcessCreates“-Ereignisse |
| aggregationEnabledConnectionCreate | boolean| Ereignisaggregation für „ConnectionCreate“-Ereignisse aktivieren/deaktivieren |
| aggregationIntervalConnectionCreate | ISO8601 TimeSpan-Zeichenfolge | Aggregationsintervall für „ConnectionCreates“-Ereignisse |
| aggregationEnabledProcessTerminate | boolean | Ereignisaggregation für „ProcessTerminate“-Ereignisse aktivieren/deaktivieren | Nur Windows|
| aggregationIntervalProcessTerminate | ISO8601 TimeSpan-Zeichenfolge | Aggregationsintervall für „ProcessTerminates“-Ereignisse | Nur Windows|
|

## <a name="default-configurations-settings"></a>Standardkonfigurationseinstellungen

| Konfigurationsname | Standardwerte |
|:-----------|:---------------|
| aggregationEnabledProcessCreate | true |
| aggregationIntervalProcessCreate | „PT1H“|
| aggregationEnabledConnectionCreate | true |
| aggregationIntervalConnectionCreate | „PT1H“|
| aggregationEnabledProcessTerminate | true |
| aggregationIntervalProcessTerminate | „PT1H“|
|

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie etwas über die Ereignisaggregation beim Defender für IoT-Sicherheits-Agent und die verfügbaren Optionen für die Ereigniskonfiguration erfahren.

Um mit der Defender für IoT-Bereitstellung fortzufahren, lesen Sie die folgenden Artikel:

- Machen Sie sich mit den [Methoden der Sicherheits-Agent-Authentifizierung](concept-security-agent-authentication-methods.md) vertraut.
- Wählen Sie einen [Sicherheits-Agent](how-to-deploy-agent.md) aus, und stellen Sie ihn bereit.
- Erfahren Sie, wie Sie [den Defender für IoT-Dienst in Ihrer IoT Hub-Instanz aktivieren](quickstart-onboard-iot-hub.md).
- Unter [für IoT – Häufig gestellte Fragen](resources-agent-frequently-asked-questions.md) erfahren Sie mehr über den Dienst.
