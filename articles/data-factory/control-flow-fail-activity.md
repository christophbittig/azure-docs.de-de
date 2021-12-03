---
title: Ausführen einer Fail-Aktivität in Azure Data Factory und Synapse Analytics
titleSuffix: Azure Data Factory & Azure Synapse
description: Dieser Artikel behandelt die Fail-Aktivität in Azure Data Factory und Synapse Analytics löst absichtlich einen Fehler in einer Pipeline aus.
author: chez-charlie
ms.author: chez
ms.service: data-factory
ms.subservice: orchestration
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/22/2021
ms.openlocfilehash: 2f0acb894d6bdacef69dcf8adb2088214e094415
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132517955"
---
# <a name="execute-a-fail-activity-in-azure-data-factory-and-synapse-analytics"></a>Ausführen einer Fail-Aktivität in Azure Data Factory und Synapse Analytics

Sie sollten von Zeit zu Zeit gelegentlich einen Fehler in einer Pipeline auslösen. Eine [Lookup-Aktivität](control-flow-lookup-activity.md) gibt möglicherweise keine übereinstimmenden Daten zurück, oder eine [Benutzerdefinierte Aktivität](transform-data-using-dotnet-custom-activity.md) beendet möglicherweise mit einem internen Fehler. Unabhängig vom Grund können Sie die Fail-Aktivität jetzt in einer Pipeline verwenden und sowohl deren Fehlermeldung als auch dessen Fehlercode anpassen.

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]


## <a name="syntax"></a>Syntax

```json
{
    "name": "MyFailActivity",
    "type": "Fail",
    "typeProperties": {
        "message": "500",
        "errorCode": "My Custom Error Message"
    }
}

```

## <a name="type-properties"></a>Typeigenschaften

| Eigenschaft | BESCHREIBUNG | Zulässige Werte | Erforderlich |
| --- | --- | --- | --- |
| name | Name der Fail-Aktivität | String | Ja |
| type | Muss auf **Fail** festgelegt sein | String | Ja |
| message | In der Fail-Aktivität angezeigte Fehlermeldung. Es kann dynamischer Inhalt sein, der zur Laufzeit ausgewertet wird. | String | Ja |
| errorCode | Der Fehlercode, der den Fehlertyp der Fail-Aktivität kategorisiert. Es kann dynamischer Inhalt sein, der zur Laufzeit ausgewertet wird. | String | Ja |
| | |

## <a name="understand-the-fail-activity-error-code"></a>Grundlegendes zum Fehlercode der Fail-Aktivität

Die Fehlermeldung und der Fehlercode einer Fail-Aktivität werden normalerweise von Benutzer*innen festgelegt. Wenden Sie sich an die Pipelineentwickler*innen, um mehr über die spezifischen Bedeutungen der Fehlercodes zu erfahren. In den folgenden Edgefällen legt Azure Data Factory jedoch die Fehlermeldung und/oder den Fehlercode fest.

| Situationsbeschreibung | Fehlermeldung | Fehlercode |
| --- | --- | --- |
Der (dynamische) Inhalt in `message` und `errorCode` wird richtig interpretiert. | Die Fehlermeldung, die vom*von Benutzer*innen festgelegt wird | Der von Benutzer*innen festgelegte Fehlercode |
Der dynamische Inhalt in `message` und `errorCode` kann nicht interpretiert werden. | „Failed to interpret _<activity_name>_ fail message or error code“ (Die Fehlermeldung oder der Fehlercode von <Name_der_Aktivität> konnte nicht interpretiert werden.) | `ErrorCodeNotString` |
| Der dynamische Inhalt in `message` kann nicht als Zeichenfolge interpretiert werden. | „ _<activity_name>_ fail message parameter could not be interpreted as a string“ (Der Parameter der Fehlermeldung von <Name_der_Aktivität> konnte nicht als Zeichenfolge interpretiert werden.) | Der von Benutzer*innen festgelegte Fehlercode |
| Der dynamische Inhalt in `message` wird zu NULL, einer leeren Zeichenfolge oder Leerzeichen aufgelöst. | „Failed to interpret _<activity_name>_ fail message or error code“ (Die Fehlermeldung oder der Fehlercode von <Name_der_Aktivität> konnte nicht interpretiert werden.) | Der von Benutzer*innen festgelegte Fehlercode |
| Der dynamische Inhalt in `errorCode` kann nicht als Zeichenfolge interpretiert werden. | Die Fehlermeldung, die vom*von Benutzer*innen festgelegt wird | `ErrorCodeNotString` |
| Der dynamische Inhalt in `errorCode` wird zu NULL, einer leeren Zeichenfolge oder Leerzeichen aufgelöst. | Die Fehlermeldung, die vom*von Benutzer*innen festgelegt wird | `ErrorCodeNotString` |
| Der Wert für `message` oder `errorCode`, der vom*von Benutzer*innen bereitgestellt wird, ist nicht zeichenfolgenfähig.* | Die Pipeline schlägt mit folgender Meldung _fehl_: „Invalid value for property <`errorCode`/`message`>“ (Ungültiger Wert für <Meldung>.) | |
| Das Feld `message` fehlt.* | „Fail message was not provided“ (Die Fehlermeldung wurde nicht bereitgestellt.) | Der von Benutzer*innen festgelegte Fehlercode |
| Das Feld `errorCode` fehlt.* | Die Fehlermeldung, die vom*von Benutzer*innen festgelegt wird | `ErrorCodeNotString` |
| | |

\* Diese Situation, sollte nicht auftreten, wenn die Pipeline mit der Webbenutzeroberfläche von Data Factory entwickelt wird.

## <a name="next-steps"></a>Nächste Schritte

Machen Sie sich mit weiteren unterstützten Ablaufsteuerungsaktivitäten vertraut, z. B.:

- [If Condition-Aktivität](control-flow-if-condition-activity.md)
- [Aktivität „Pipeline ausführen“](control-flow-execute-pipeline-activity.md)
- [ForEach-Aktivität](control-flow-for-each-activity.md)
- [Aktivität „Metadaten abrufen“](control-flow-get-metadata-activity.md)
- [Lookup-Aktivität](control-flow-lookup-activity.md)
- [Webaktivität](control-flow-web-activity.md)
- [Until-Aktivität](control-flow-until-activity.md)
- [Grundlegendes zu Pipelinefehlern](tutorial-pipeline-failure-error-handling.md)
