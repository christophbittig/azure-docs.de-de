---
title: Pipelinefehler und Fehlermeldung
description: Hier finden Sie Informationen zur Bestimmung von Pipelinefehlerstatus und Fehlermeldung.
ms.service: data-factory
ms.subservice: orchestration
author: chez-charlie
ms.author: chez
ms.reviewer: jburchel
ms.topic: tutorial
ms.date: 11/08/2021
ms.openlocfilehash: bc9abf7319b3dc51572670f242b5d18a1a5d270d
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132522325"
---
# <a name="understanding-pipeline-failure"></a>Grundlegendes zu Pipelinefehlern

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

## <a name="error-handling"></a>Fehlerbehandlung

Die Orchestrierung von Azure Data Factory und Synapse-Pipeline ermöglicht die Verwendung von bedingter Logik und ermöglicht es Benutzern, basierend auf den Ergebnissen einer vorherigen Aktivität verschiedenen Pfaden zu folgen. Durch die Verwendung verschiedener Pfade können Benutzer robuste Pipelines erstellen und eine Fehlerbehandlung in die ETL/ELT-Logik integrieren. Insgesamt sind vier bedingte Pfade möglich:

* Bei Erfolg (Standardpfad)
* Bei Fehler
* Bei Abschluss
* Bei Überspringen

:::image type="content" source="media/tutorial-pipeline-failure-error-handling/pipeline-error-1-four-branches.png" alt-text="Screenshot: Die vier Verzweigungen einer Aktivität":::

### <a name="common-error-handling-mechanism"></a>Allgemeiner Fehlerbehandlungsmechanismus

#### <a name="try-catch-block"></a>Try-Catch-Block

Bei diesem Ansatz definiert der Kunde die Geschäftslogik und gibt nur den Pfad _UponFailure_ (Bei Fehler) an, um Fehler aus der vorherigen Aktivität abzufangen. In diesem Fall ist die Pipeline erfolgreich, wenn der Pfad _UponFailure_ (Bei Fehler) erfolgreich ist.

:::image type="content" source="media/tutorial-pipeline-failure-error-handling/pipeline-error-2-try-catch-definition.png" alt-text="Screenshot: Definition und Ergebnis eines Try Catch-Blocks":::

#### <a name="do-if-else-block"></a>Do-If-Else-Block

Bei diesem Ansatz definiert der Kunde die Geschäftslogik und gibt sowohl den Pfad _UponFailure_ (Bei Fehler) als auch den Pfad _UponSuccess_ (Bei Erfolg) an. In diesem Fall ist die Pipeline nicht erfolgreich, auch wenn der Pfad _UponFailure_ (Bei Fehler) erfolgreich ist.

:::image type="content" source="media/tutorial-pipeline-failure-error-handling/pipeline-error-3-do-if-else-definition.png" alt-text="Screenshot: Definition und Ergebnis eines Do-If-Else-Blocks":::

#### <a name="do-if-skip-else-block"></a>Do-If-Skip-Else-Block

Bei diesem Ansatz definiert der Kunde die Geschäftslogik und gibt den Pfad _UponFailure_ (Bei Fehler) sowie den Pfad _UponSuccess_ (Bei Erfolg) mit einer angefügten Aktivität vom Typ _DummyUponSkip_ (Dummyaktivität bei Überspringen) an. In diesem Fall ist die Pipeline erfolgreich, wenn der Pfad _UponFailure_ (Bei Fehler) erfolgreich ist.

:::image type="content" source="media/tutorial-pipeline-failure-error-handling/pipeline-error-4-do-if-skip-else-definition.png" alt-text="Screenshot: Definition und Ergebnis eines Do-If-Skip-Else-Blocks":::

### <a name="summary-table"></a>Zusammenfassungstabelle

Vorgehensweise | Definiert | Anzeige für die gesamte Pipeline bei erfolgreicher Aktivität | Anzeige für die gesamte Pipeline bei nicht erfolgreicher Aktivität
---------------------------- | ------------------- | ------------------- | -------------------
[Try-Catch](#try-catch-block) | Nur Pfad _UponFailure_ (Bei Fehler) | Erfolgreich |  Erfolgreich
[Do-If-Else](#do-if-else-block) | Pfade _UponFailure_ (Bei Fehler) und _UponSuccess_ (Bei Erfolg) | Erfolg |  Fehler
[Do-If-Skip-Else](#do-if-skip-else-block) |  Pfad _UponFailure_ (Bei Fehler) und Pfad _UponSuccess_ (Bei Erfolg) mit _DummyUponSkip_ (Dummyaktivität bei Überspringen) am Ende | Erfolgreich |  Erfolgreich

### <a name="how-pipeline-failure-are-determined"></a>Ermitteln von Pipelinefehlern

Unterschiedliche Fehlerbehandlungsmechanismen führen jeweils zu einem anderen Status für die Pipeline: Einige Pipelines sind erfolgreich, andere nicht. Der Erfolgsstatus von Pipelines wird wie folgt bestimmt:

* Zunächst wird das Ergebnis für alle Aktivitäten auf Blattebene ausgewertet. Wenn eine Blattaktivität übersprungen wurde, wird stattdessen die übergeordnete Aktivität ausgewertet.
* Die Pipeline ist nur erfolgreich, wenn alle Knoten als erfolgreich ausgewertet wurden.

Angenommen, die Aktivität _Bei Fehler_ und die Aktivität _DummyUponFailure_ (Dummyaktivität bei Fehler) sind erfolgreich:

* [Try-Catch-Ansatz](#try-catch-block)

  * Wenn die vorherige Aktivität erfolgreich ist, wird der Knoten _UponFailure_ (Bei Fehler) übersprungen, und der übergeordnete Knoten ist erfolgreich. Die gesamte Pipeline ist erfolgreich.
  * Wenn die vorherige Aktivität nicht erfolgreich ist, wird der Knoten _UponFailure_ (Bei Fehler) angewendet. Die gesamte Pipeline ist erfolgreich.

* [Do-If-Else-Ansatz](#do-if-else-block)

  * Wenn die vorherige Aktivität erfolgreich ist, ist der Knoten _UponSuccess_ (Bei Erfolg) erfolgreich, der Knoten _UponFailure_ (Bei Fehler) wird übersprungen, und der zugehörige übergeordnete Knoten ist erfolgreich. Die gesamte Pipeline ist erfolgreich.
  * Wenn die vorherige Aktivität nicht erfolgreich ist, wird der Knoten _UponSuccess_ (Bei Erfolg) übersprungen, und der übergeordnete Knoten ist nicht erfolgreich. Die gesamte Pipeline ist nicht erfolgreich.

* [Do-If-Skip-Else-Ansatz](#do-if-skip-else-block)

  * Wenn die vorherige Aktivität erfolgreich ist, wird der Knoten _DummyUponSkip_ (Dummyaktivität bei Überspringen) übersprungen, und der übergeordnete Knoten _UponSuccess_ (Bei Erfolg) ist erfolgreich. Die andere Aktivität (_UponFailure_ (Bei Fehler)) wird übersprungen, und der übergeordnete Knoten ist erfolgreich. Die gesamte Pipeline ist erfolgreich.
  * Wenn die vorherige Aktivität nicht erfolgreich ist, sind der Knoten _UponFailure_ (Bei Fehler) und _DummyUponSkip_ (Dummyaktivität bei Überspringen) erfolgreich. Die gesamte Pipeline ist erfolgreich.

## <a name="next-steps"></a>Nächste Schritte

[Data Factory-Metriken und -Warnungen](monitor-metrics-alerts.md)

[Visuelles Überwachen](monitor-visually.md#alerts)
