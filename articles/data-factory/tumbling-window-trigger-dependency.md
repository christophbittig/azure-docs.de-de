---
title: Erstellen einer Triggerabhängigkeit für ein rollierendes Fenster
titleSuffix: Azure Data Factory & Azure Synapse
description: Erfahren Sie, wie in Azure Data Factory und Synapse Analytics eine Abhängigkeit von einem Trigger für ein rollierendes Fenster erstellt wird.
ms.author: chez
author: chez-charlie
ms.service: data-factory
ms.subservice: orchestration
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: 89a6db7fcfd78ec2e1115256cc162a91cc9180da
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124805937"
---
# <a name="create-a-tumbling-window-trigger-dependency"></a>Erstellen einer Triggerabhängigkeit für ein rollierendes Fenster
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Dieser Artikel enthält die Schritte zum Erstellen einer Abhängigkeit von einem Trigger für ein rollierendes Fenster. Allgemeine Informationen zu Triggern für rollierende Fenster finden Sie unter [Erstellen eines Triggers für ein rollierendes Fenster](how-to-create-tumbling-window-trigger.md).

Verwenden Sie diese erweiterte Funktion zum Erstellen einer Abhängigkeit für das rollierende Fenster, um eine Abhängigkeitskette zu erstellen und sicherzustellen, dass ein Trigger erst nach der erfolgreichen Ausführung eines anderen Triggers im Dienst ausgeführt wird.

Das folgende Video veranschaulicht die Erstellung abhängiger Pipelines mit einem Trigger für ein rollierendes Fenster:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Create-dependent-pipelines-in-your-Azure-Data-Factory/player]

## <a name="create-a-dependency-in-the-ui"></a>Erstellen einer Abhängigkeit in der Benutzeroberfläche

Wählen Sie zum Erstellen einer Abhängigkeit von einem Trigger die Optionen **Trigger > Erweitert > Neu** aus, und wählen Sie dann den gewünschten Trigger mit dem entsprechenden Offset und der entsprechenden Größe aus. Wählen Sie **Fertig stellen** aus, und veröffentlichen Sie die Änderungen, damit die Abhängigkeiten wirksam werden.

:::image type="content" source="media/tumbling-window-trigger-dependency/tumbling-window-dependency-01.png" alt-text="Abhängigkeitserstellung":::

## <a name="tumbling-window-dependency-properties"></a>Eigenschaften der Abhängigkeit für ein rollierendes Fenster

Ein Trigger für ein rollierendes Fenster mit einer Abhängigkeit weist die folgenden Eigenschaften auf:

```json
{
    "name": "MyTriggerName",
    "properties": {
        "type": "TumblingWindowTrigger",
        "runtimeState": <<Started/Stopped/Disabled - readonly>>,
        "typeProperties": {
            "frequency": <<Minute/Hour>>,
            "interval": <<int>>,
            "startTime": <<datetime>>,
            "endTime": <<datetime – optional>>,
            "delay": <<timespan – optional>>,
            "maxConcurrency": <<int>> (required, max allowed: 50),
            "retryPolicy": {
                "count": <<int - optional, default: 0>>,
                "intervalInSeconds": <<int>>,
            },
            "dependsOn": [
                {
                    "type": "TumblingWindowTriggerDependencyReference",
                    "size": <<timespan – optional>>,
                    "offset": <<timespan – optional>>,
                    "referenceTrigger": {
                        "referenceName": "MyTumblingWindowDependency1",
                        "type": "TriggerReference"
                    }
                },
                {
                    "type": "SelfDependencyTumblingWindowTriggerReference",
                    "size": <<timespan – optional>>,
                    "offset": <<timespan>>
                }
            ]
        }
    }
}
```

Die folgende Tabelle enthält die Liste der Attribute, die zum Definieren einer Abhängigkeit für ein rollierendes Fenster erforderlich sind.

| **Eigenschaftenname** | **Beschreibung**  | **Typ** | **Erforderlich** |
|---|---|---|---|
| type  | In dieser Dropdownliste werden alle vorhandenen Trigger für rollierende Fenster angezeigt. Wählen Sie den Trigger aus, auf den sich die Abhängigkeit beziehen soll.  | TumblingWindowTriggerDependencyReference oder SelfDependencyTumblingWindowTriggerReference | Ja |
| offset | Der Offset des Abhängigkeitstriggers. Geben Sie einen Wert im Format einer Zeitspanne an. Dabei sind positive und negative Offsets zulässig. Diese Eigenschaft ist obligatorisch, wenn der Trigger von sich selbst abhängig ist. In allen anderen Fällen ist sie optional. Die Selbstabhängigkeit muss immer ein negativer Offset sein. Wenn kein Wert angegeben wird, ist das Fenster identisch mit dem Trigger. | Timespan<br/>(hh:mm:ss) | Selbstabhängigkeit: Ja<br/>Sonstiges: Nein |
| size | Die Größe des abhängigen rollierenden Fensters. Geben Sie einen positiven Timespan-Wert an. Diese Eigenschaft ist optional. | Timespan<br/>(hh:mm:ss) | Nein  |

> [!NOTE]
> Ein Trigger für ein rollierendes Fenster kann von maximal fünf anderen Triggern abhängig sein.

## <a name="tumbling-window-self-dependency-properties"></a>Eigenschaften der Selbstabhängigkeit für ein rollierendes Fenster

In Szenarien, in denen der Trigger mit dem nächsten Fenster erst fortfahren soll, wenn das vorhergehende Fenster erfolgreich abgeschlossen wurde, müssen Sie eine Selbstabhängigkeit erstellen. Ein Trigger mit Selbstabhängigkeit, der vom Erfolg eigener früherer Ausführungen innerhalb der vorangegangenen Stunde abhängig ist, hat die im folgenden Code angegebenen Eigenschaften.

> [!NOTE]
> Wenn Ihre ausgelöste Pipeline auf der Ausgabe von Pipelines in zuvor ausgelösten Fenstern basiert, empfehlen wir, nur die Selbstabhängigkeit eines rollierenden Fensters zu verwenden. Zur Einschränkung von parallelen Triggerausführungen legen Sie die maximale Triggerparallelität fest.

```json
{
    "name": "DemoSelfDependency",
    "properties": {
        "runtimeState": "Started",
        "pipeline": {
            "pipelineReference": {
                "referenceName": "Demo",
                "type": "PipelineReference"
            }
        },
        "type": "TumblingWindowTrigger",
        "typeProperties": {
            "frequency": "Hour",
            "interval": 1,
            "startTime": "2018-10-04T00:00:00Z",
            "delay": "00:01:00",
            "maxConcurrency": 50,
            "retryPolicy": {
                "intervalInSeconds": 30
            },
            "dependsOn": [
                {
                    "type": "SelfDependencyTumblingWindowTriggerReference",
                    "size": "01:00:00",
                    "offset": "-01:00:00"
                }
            ]
        }
    }
}
```
## <a name="usage-scenarios-and-examples"></a>Verwendungsszenarien und -beispiele

Nachfolgend finden Sie Abbildungen der Verwendungsszenarien für Eigenschaften der Abhängigkeit für rollierende Fenster.

### <a name="dependency-offset"></a>Offset der Abhängigkeit

:::image type="content" source="media/tumbling-window-trigger-dependency/tumbling-window-dependency-02.png" alt-text="Beispiel für Offset":::

### <a name="dependency-size"></a>Größe der Abhängigkeit

:::image type="content" source="media/tumbling-window-trigger-dependency/tumbling-window-dependency-03.png" alt-text="Beispiel für Größe":::

### <a name="self-dependency"></a>Selbstabhängigkeit

:::image type="content" source="media/tumbling-window-trigger-dependency/tumbling-window-dependency-04.png" alt-text="Selbstabhängigkeit":::

### <a name="dependency-on-another-tumbling-window-trigger"></a>Abhängigkeit von einem anderen Trigger für ein rollierendes Fenster

Ein täglicher Auftrag zur Verarbeitung von Telemetriedaten, der von einem anderen täglichen Auftrag abhängig ist, der die Ausgabe der letzten sieben Tage aggregiert und Datenströme von sieben Tagen des rollierenden Fensters generiert:

:::image type="content" source="media/tumbling-window-trigger-dependency/tumbling-window-dependency-05.png" alt-text="Beispiel für Abhängigkeit":::

### <a name="dependency-on-itself"></a>Abhängigkeit von sich selbst

Ein täglicher Auftrag ohne Lücken in den Ausgabedatenströmen des Auftrags:

:::image type="content" source="media/tumbling-window-trigger-dependency/tumbling-window-dependency-06.png" alt-text="Beispiel für Selbstabhängigkeit":::

## <a name="monitor-dependencies"></a>Überwachen von Abhängigkeiten

Sie können die Abhängigkeitskette und die entsprechenden Fenster auf der Seite für die Überwachung von Triggerausführungen überwachen. Navigieren Sie zu **Überwachung > Triggerausführungen**. Wenn ein Trigger des Typs „Rollierendes Fenster“ Abhängigkeiten aufweist, enthält „Triggername“ einen Link zur Ansicht für die Überwachung auf Abhängigkeiten.  

:::image type="content" source="media/tumbling-window-trigger-dependency/tumbling-window-dependency-07.png" alt-text="Überwachen von Triggerausführungen":::

Klicken Sie sich durch bis zum Namen des Triggers, um die Triggerabhängigkeiten anzuzeigen. Im rechten Bereich werden ausführliche Informationen zur Triggerausführung angezeigt, z. B. RunID, Fensterzeit, Status usw.

:::image type="content" source="media/tumbling-window-trigger-dependency/tumbling-window-dependency-08.png" alt-text="Listenansicht zum Überwachen von Abhängigkeiten":::

Sie können den Status der Abhängigkeiten und Fenster für jeden abhängigen Trigger sehen. Wenn einer der Abhängigkeitstrigger fehlschlägt, müssen Sie ihn erneut erfolgreich ausführen, damit der abhängige Trigger ausgeführt werden kann.

Ein Trigger für ein rollierendes Fenster wartet _sieben Tage_ auf Abhängigkeiten, ehe ein Timeout eintritt. Nach sieben Tagen schlägt die Ausführung des Auslösers fehl.

Wählen Sie die Gantt-Ansicht aus, um eine visuelle Darstellung des Zeitplans für Triggerabhängigkeiten anzuzeigen.

:::image type="content" source="media/tumbling-window-trigger-dependency/tumbling-window-dependency-09.png" alt-text="Gantt-Diagramm zum Überwachen von Abhängigkeiten":::

Transparente Kästchen zeigen die Abhängigkeitsfenster für jeden nachgeordneten abhängigen Trigger, während die einfarbigen Kästchen oben die einzelnen Fensterausführungen zeigen. Es folgen einige Tipps zum Interpretieren der Gantt-Diagrammansicht:

* Das transparente Kästchen wird blau gerendert, wenn sich abhängige Fenster im Zustand „Ausstehend“ oder „Wird ausgeführt“ befinden.
* Nachdem alle Fenster für einen abhängigen Trigger erfolgreich waren, wird das transparente Kästchen grün.
* Das transparente Kästchen wird rot gerendert, wenn ein abhängiges Fenster fehlschlägt. Achten Sie auf ein rotes einfarbiges Kästchen zur Kennzeichnung der fehlerhaften Fensterausführung.

Wenn Sie ein Fenster in der Gantt-Diagrammansicht erneut ausführen möchten, wählen Sie das einfarbige Kästchen für das Fenster aus. Im Aktionsbereich werden die Details und die Optionen zum erneuten Ausführen angezeigt.

## <a name="next-steps"></a>Nächste Schritte

* Lesen Sie [Erstellen eines Triggers für ein rollierendes Fenster](how-to-create-tumbling-window-trigger.md).
