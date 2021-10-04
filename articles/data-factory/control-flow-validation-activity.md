---
title: Aktivität „Prüfung“
titleSuffix: Azure Data Factory & Azure Synapse
description: Die Validation-Aktivität in Azure Data Factory und Synapse Analytics verzögert die Ausführung der Pipeline, bis ein Dataset anhand benutzerdefinierter Kriterien validiert wird.
author: chez-charlie
ms.author: chez
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: orchestration
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/09/2021
ms.openlocfilehash: fa98ef27b5dbcc7949f37bf548c414d015224e4c
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124750615"
---
# <a name="validation-activity-in-azure-data-factory-and-synapse-analytics-pipelines"></a>Validation-Aktivität in Azure Data Factory- und Synapse Analytics-Pipelines
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Sie können mithilfe einer Prüfung in einer Pipeline sicherstellen, dass diese die Ausführung nur fortsetzt, nachdem sie überprüft hat, ob der angefügte Datasetverweis vorhanden ist und die angegebenen Kriterien erfüllt oder ob das Timeout erreicht wurde.


## <a name="syntax"></a>Syntax

```json

{
    "name": "Validation_Activity",
    "type": "Validation",
    "typeProperties": {
        "dataset": {
            "referenceName": "Storage_File",
            "type": "DatasetReference"
        },
        "timeout": "7.00:00:00",
        "sleep": 10,
        "minimumSize": 20
    }
},
{
    "name": "Validation_Activity_Folder",
    "type": "Validation",
    "typeProperties": {
        "dataset": {
            "referenceName": "Storage_Folder",
            "type": "DatasetReference"
        },
        "timeout": "7.00:00:00",
        "sleep": 10,
        "childItems": true
    }
}

```


## <a name="type-properties"></a>Typeigenschaften

Eigenschaft | BESCHREIBUNG | Zulässige Werte | Erforderlich
-------- | ----------- | -------------- | --------
name | Name der Aktivität „Prüfung“. | String | Ja |
type | Muss auf **Prüfung** festgelegt werden. | String | Ja |
dataset | Die Aktivität blockiert die Ausführung, bis sie überprüft hat, ob dieser Datasetverweis vorhanden ist und die angegebenen Kriterien erfüllt oder ob das Timeout erreicht wurde. Das bereitgestellte Dataset sollte eine der Eigenschaften „MinimumSize“ oder „ChildItems“ unterstützen. | Datasetverweis | Ja |
timeout | Gibt das Zeitlimit für die Ausführung der Aktivität an. Wenn kein Wert angegeben wird, ist der Standardwert 7 Tage („7.00:00:00“). Das Format ist „d.hh:mm:ss“. | String | Nein |
sleep | Eine Verzögerung in Sekunden zwischen Prüfungsversuchen. Wenn kein Wert angegeben wird, ist der Standardwert 10 Sekunden. | Integer | Nein |
childItems | Überprüft, ob der Ordner untergeordnete Elemente enthält. Kann festgelegt werden auf: – „true“: Überprüft, ob der Ordner vorhanden ist und Elemente enthält. Blockiert, bis mindestens ein Element im Ordner vorhanden oder der Timeoutwert erreicht ist. – „false“: Überprüft, ob der Ordner vorhanden und leer ist. Blockiert, bis der Ordner leer oder der Timeoutwert erreicht ist. Wenn kein Wert angegeben wird, blockiert die Aktivität, bis der Ordner vorhanden oder das Timeout erreicht ist. | Boolesch | Nein |
minimumSize | Mindestgröße einer Datei in Bytes. Wenn kein Wert angegeben wird, ist der Standardwert 0 Bytes. | Integer | Nein |


## <a name="next-steps"></a>Nächste Schritte
Informationen zu weiteren unterstützten Ablaufsteuerungsaktivitäten:

- [Aktivität „If Condition“](control-flow-if-condition-activity.md)
- [Aktivität „Pipeline ausführen“](control-flow-execute-pipeline-activity.md)
- [ForEach-Aktivität](control-flow-for-each-activity.md)
- [Aktivität „Metadaten abrufen“](control-flow-get-metadata-activity.md)
- [Lookup-Aktivität](control-flow-lookup-activity.md)
- [Webaktivität](control-flow-web-activity.md)
- [Until-Aktivität](control-flow-until-activity.md)
