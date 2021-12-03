---
title: Untersuchen von Sicherheitsempfehlungen
description: Hier finden Sie eine Beschreibung der Untersuchung von Sicherheitsempfehlungen mit dem Sicherheitsdienst „Defender für IoT“.
ms.topic: quickstart
ms.date: 11/09/2021
ms.openlocfilehash: 7f4dab5ed0590d0e2658145f17d657beb5af1177
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132306128"
---
# <a name="quickstart-investigate-security-recommendations"></a>Schnellstart: Untersuchen von Sicherheitsempfehlungen

Die rechtzeitige Analyse und Entschärfung in Bezug auf die Empfehlungen von Defender für IoT ist die beste Vorgehensweise, um den Sicherheitsstatus zu verbessern und die Angriffsfläche für Ihre IoT-Lösung zu verkleinern.

In dieser Schnellstartanleitung erkunden wir die Informationen in den einzelnen IoT-Sicherheitsempfehlungen. Darüber hinaus wird beschrieben, wie Sie einen Drilldown ausführen und die Details der einzelnen Empfehlungen und der zugehörigen Geräte verwenden, um das Risiko zu verringern.

Los geht’s!

## <a name="investigate-new-recommendations"></a>Untersuchen von neuen Empfehlungen

In der Liste mit den IoT Hub-Empfehlungen werden alle aggregierten Sicherheitsempfehlungen für Ihre IoT Hub-Instanz angezeigt.

1. Öffnen Sie im Azure-Portal die **IoT Hub**-Instanz, die Sie auf neue Empfehlungen untersuchen möchten.

1. Wählen Sie im Menü **Sicherheit** die Option **Empfehlungen** aus. Alle Sicherheitsempfehlungen für die IoT Hub-Instanz werden angezeigt. Die Empfehlungen mit dem Flag **Neu** sind die Empfehlungen der letzten 24 Stunden.

1. Wählen Sie in der Liste eine beliebige Empfehlung aus, und öffnen Sie sie, um die Details dazu anzuzeigen und einen Drilldown auszuführen.

## <a name="security-recommendation-details"></a>Details von Sicherheitsempfehlungen

Öffnen Sie die einzelnen aggregierten Empfehlungen, um dazu jeweils die ausführliche Beschreibung, die Problembehandlungsschritte und die Geräte-ID für jedes Gerät anzuzeigen, von dem eine Empfehlung ausgelöst wurde. Darüber hinaus werden der Schweregrad der Empfehlung und die Zugriffsoption zur direkten Untersuchung mit Log Analytics angezeigt.

1. Wählen Sie in der Liste unter **IoT Hub** > **Sicherheit** > **Empfehlungen** eine beliebige Sicherheitsempfehlung aus, und öffnen Sie sie.

1. Sehen Sie sich für jede Empfehlung die **Beschreibung**, den **Schweregrad** und die **Gerätedetails** aller Geräte an, von denen diese Empfehlung während des Aggregationszeitraums ausgegeben wurde.

1. Verwenden Sie nach dem Überprüfen der Empfehlungsdetails die Anleitung im **Schritt zur manuellen Behebung**, um das Problem zu behandeln bzw. zu beheben, das zur Empfehlung geführt hat.

    :::image type="content" source="media/quickstart/remediate-security-recommendations-inline.png" alt-text="Beheben von Sicherheitsempfehlungen mit Defender für IoT" lightbox="media/quickstart/remediate-security-recommendations-expanded.png":::

1. Untersuchen Sie die Empfehlungsdetails für ein bestimmtes Gerät, indem Sie das gewünschte Gerät auf der Drilldownseite auswählen.

    :::image type="content" source="media/quickstart/explore-security-recommendation-detail-inline.png" alt-text="Untersuchen von bestimmten Sicherheitsempfehlungen für ein Gerät mit Defender für IoT" lightbox="media/quickstart/explore-security-recommendation-detail-expanded.png":::

1. Falls eine weitere Untersuchung erforderlich ist, können Sie den Link verwenden, um die **Empfehlung in Log Analytics zu untersuchen**. 

## <a name="next-steps"></a>Nächste Schritte

Fahren Sie mit dem nächsten Artikel fort, um sich über das Erstellen von benutzerdefinierten Warnungen zu informieren.

> [!div class="nextstepaction"]
> [Erstellen benutzerdefinierter Benachrichtigungen](quickstart-create-custom-alerts.md)
