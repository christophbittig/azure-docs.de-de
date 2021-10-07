---
title: Operationalisieren von Datenpipelines
description: Es wird beschrieben, wie Sie Vereinbarungen zum Servicelevel für Datenpipelines bereitstellen.
ms.service: data-factory
ms.subservice: orchestration
author: chez-charlie
ms.author: chez
ms.reviewer: jburchel
ms.topic: tutorial
ms.date: 09/22/2021
ms.openlocfilehash: d80d2abf4d51d99fb207ec728e29b362079ba64a
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129207543"
---
# <a name="deliver-service-level-agreement-for-data-pipelines"></a>Bereitstellen einer Vereinbarung zum Servicelevel für Datenpipelines

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Factory wird von Unternehmen auf der ganzen Welt geschätzt und als vertrauenswürdig angesehen. Als nativer ETL-Clouddienst von Azure für die serverlose Datenintegration und -transformation mit horizontaler Skalierung wird Azure Data Factory sehr häufig für die Implementierung von Datenpipelines eingesetzt, um Daten aufzubereiten, zu verarbeiten und in das Data Warehouse oder den Data Lake eines Unternehmens zu laden.

Nachdem Datenpipelines entweder per [Continuous Integration/Delivery (CI/CD)](continuous-integration-delivery.md) im Git-Modus oder direkt im Livemodus veröffentlicht wurden, werden sie normalerweise per Autopilot ausgeführt. Die Ausführung kann nach einem vordefinierten Zeitplan über einen [Plantrigger](how-to-create-schedule-trigger.md) oder einen [Trigger mit rollierendem Fenster](how-to-create-tumbling-window-trigger.md) erfolgen. Alternativ kann für die Ausführung auch eine ereignisgesteuerte Architektur mit einem [Trigger für Speicherereignisse](how-to-create-event-trigger.md) oder einem [Trigger für benutzerdefinierte Ereignisse](how-to-create-custom-event-trigger.md) genutzt werden. Hierbei werden unternehmenskritische Workloads verarbeitet, indem Daten für Geschäftsberichte oder Datenanalysen und Machine Learning-Projekte aufbereitet werden.

## <a name="preemptive-warnings-for-long-running-jobs"></a>Präventive Warnungen für Aufträge mit langer Ausführungszeit

Bei der Bereitstellung von Vereinbarungen zum Servicelevel (SLAs) für diese Datenpipelines müssen zwei Hauptaufgaben bewältigt werden:

* Unter Umständen wird die Compute-Umgebung für bestimmte Aktivitäten (z. B. SQL für eine Aktivität vom Typ „Gespeicherte Prozedur“) gedrosselt, was dazu führt, dass sich die gesamte Datenpipeline verlangsamt und die Einhaltung der zugehörigen SLA gefährdet ist.
* Pipelineentwickler überwachen die Factory nicht immer aktiv und suchen nicht proaktiv nach zeitintensiven Pipelines, für die SLAs nicht eingehalten werden.

Wenn eine Pipeline richtig konfiguriert ist, werden zur Behebung dieser Probleme bei fehlenden SLAs Metriken zur Ausgabe der _verstrichenen Zeit für die Pipelineausführung_ ausgeführt. In Kombination mit [Data Factory-Warnungen](monitor-metrics-alerts.md#data-factory-alerts) ermöglichen wir es Entwicklern von Datenpipelines, ihren Kunden bessere SLAs bereitzustellen: Sie teilen uns mit, wie lange eine Pipeline ausgeführt werden soll, und wir benachrichtigen Sie dann proaktiv, wenn die Ausführung der Pipeline länger als erwartet dauert.

Navigieren Sie für jede Pipeline, für die Sie während der Erstellungsphase Warnungen erstellen möchten, zu den Pipelineeinstellungen, indem Sie in der Pipelinecanvas auf den Leerraum klicken.

:::image type="content" source="media/tutorial-operationalize-pipelines/elapsed-time-1-set-up.png" alt-text="Screenshot: Angeben der erwarteten Ausführungsdauer für eine Pipeline":::

Aktivieren Sie auf der Registerkarte __Einstellungen__ die Option „Metrik für verstrichene Zeit“, und geben Sie die erwartete Ausführungsdauer der Pipeline im Format `D.HH:MM:SS` an. Wir empfehlen Ihnen dringend, den Wert aus Ihrer Geschäfts-SLA zu verwenden. Dies ist der Zeitraum, der für die Pipeline zulässig ist, um Ihre Geschäftsanforderungen zu erfüllen. Wenn die Dauer einer Pipeline diese Einstellung überschreitet, wird von Data Factory für die Metrik vom Typ _Verstrichene Zeit für Pipelineausführung_ (Metrik-ID: `PipelineElapsedTimeRuns`) ein Protokolleintrag in Azure Monitor erstellt. Anders ausgedrückt: Sie werden über zeitintensive Pipelines _präventiv_ benachrichtigt, bevor der Pipelinevorgang abgeschlossen ist.

Hierbei ist uns natürlich klar, dass einige Pipelines mehr Zeit als andere benötigen, weil sie mehr Schritte umfassen oder eine größere Datenmenge verschoben wird. Für zeitintensive Pipelines gibt es keine allgemeingültige Definition. Wir bitten Sie, jeweils den Schwellenwert für jede Pipeline zu definieren, für die Sie eine SLA benötigen. Beim Protokollieren der Metrik für eine bestimmte Pipeline vergleichen wir den Wert mit der vom Benutzer angegebenen Einstellung für die erwartete Ausführungsdauer.

> [!NOTE]
> Dieses Feature kann bei Bedarf jeweils pro Pipeline verwendet werden. Für eine Pipeline wird keine Metrik protokolliert, wenn dafür keine erwartete Ausführungsdauer angegeben ist.

Führen Sie die Schritte zum Einrichten von [Data Factory-Warnungen](monitor-metrics-alerts.md#data-factory-alerts) für die Metrik aus. Ihre Techniker werden per E-Mail oder SMS benachrichtigt, damit sie reagieren und Maßnahmen zur Einhaltung der SLAs ergreifen können.

## <a name="next-steps"></a>Nächste Schritte

[Data Factory-Metriken und -Warnungen](monitor-metrics-alerts.md)

[Visuelles Überwachen](monitor-visually.md#alerts)
