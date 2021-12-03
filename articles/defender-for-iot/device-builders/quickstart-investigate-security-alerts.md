---
title: 'Schnellstart: Untersuchen von Sicherheitswarnungen'
description: Enthält eine Beschreibung der Grundlagen und der Drilldown- und Untersuchungsvorgänge bei Sicherheitswarnungen in Defender für IoT auf Ihren IoT-Geräten.
ms.topic: quickstart
ms.date: 11/09/2021
ms.openlocfilehash: c55392650edf85c753bcde32a03e8ad313ebd148
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132331507"
---
# <a name="quickstart-investigate-security-alerts"></a>Schnellstart: Untersuchen von Sicherheitswarnungen

Eine geplante Untersuchung und Behebung der von Defender für IoT ausgegebenen Warnungen ist die beste Möglichkeit, um für Ihre gesamte IoT-Lösung Compliance und Schutz sicherzustellen.

## <a name="investigate-new-security-alerts"></a>Untersuchen von neuen Sicherheitswarnungen

In der Liste mit den IoT Hub-Sicherheitswarnungen werden alle aggregierten Sicherheitswarnungen für Ihren IoT-Hub angezeigt. 

1. Öffnen Sie im Azure-Portal den **IoT-Hub**, den Sie auf neue Warnungen untersuchen möchten.

1. Wählen Sie im Menü **Sicherheit** die Option **Warnungen** aus. Alle Sicherheitswarnungen für den IoT-Hub werden angezeigt, und die Warnungen mit dem Flag **Neu** sind Ihre Warnungen der letzten 24 Stunden.

    :::image type="content" source="media/quickstart/investigate-new-security-alerts.png" alt-text="Untersuchen von neuen IoT-Sicherheitswarnungen anhand des Flags für neue Warnungen":::

1. Wählen Sie in der Liste eine Warnung aus, um die zugehörigen Warnungsdetails anzuzeigen und besser zu verstehen. 

## <a name="security-alert-details"></a>Details der Sicherheitswarnung

Beim Öffnen der einzelnen aggregierten Warnungen werden die ausführliche Warnungsbeschreibung, die Schritte zur Behebung und die Geräte-ID für die einzelnen Geräte angezeigt, für die eine Warnung ausgelöst wurde. Der Zugriff auf den Schweregrad der Warnung und die direkte Untersuchung erfolgen über Log Analytics. 

1. Navigieren Sie zu **IoT Hub** > **Sicherheit** > **Warnungen**. 

1. Wählen Sie in der Liste eine beliebige Sicherheitswarnung aus, um sie zu öffnen. 

1. Überprüfen Sie für die Warnungen aller Geräte jeweils die **Beschreibung**, den **Schweregrad**, die **Quelle der Erkennung** und die **Gerätedetails** aller Geräte, von denen die Warnung im Aggregationszeitraum ausgegeben wurde.

    :::image type="content" source="media/quickstart/drill-down-iot-alert-details.png" alt-text="Untersuchen und Überprüfung der Details der einzelnen Geräte in einer aggregierten Warnung "::: 

1. Verwenden Sie nach dem Überprüfen der spezifischen Warnungsinformationen den **Schritt für die manuelle Behebung**, um das Problem, das zur Warnung geführt hat, zu entschärfen bzw. zu beheben.

    :::image type="content" source="media/quickstart/iot-alert-manual-remediation-steps.png" alt-text="Schritte für die manuelle Behebung, um die Sicherheitswarnungen Ihres Geräts zu beheben bzw. zu entschärfen":::

1. Falls eine weitere Untersuchung erforderlich ist, können Sie den Link verwenden, um die **Warnungen in Log Analytics zu untersuchen**.
 
    :::image type="content" source="media/quickstart/investigate-iot-alert-log-analytics.png" alt-text="Weitere Untersuchung einer Warnung über den Log Analytics-Link, der auf dem Bildschirm angezeigt wird":::

## <a name="next-steps"></a>Nächste Schritte

Fahren Sie mit dem nächsten Artikel fort, um weitere Informationen zu Sicherheitswarnungstypen und möglichen Anpassungen zu erhalten.

> [!div class="nextstepaction"]
> [Azure Security Center für IoT – Sicherheitswarnungen](concept-security-alerts.md)
