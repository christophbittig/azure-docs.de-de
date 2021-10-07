---
title: Fail-Aktivität
titleSuffix: Azure Data Factory & Azure Synapse
description: Die Fail-Aktivität in Azure Data Factory und Synapse Analytics löst absichtlich einen Fehler in einer Pipeline aus.
author: chez-charlie
ms.author: chez
ms.service: data-factory
ms.subservice: orchestration
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/22/2021
ms.openlocfilehash: 95c4a7245a39ad717ad47ac9e8a7c72b2b5365fd
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/26/2021
ms.locfileid: "129059042"
---
# <a name="execute-fail-activity-in-azure-data-factory-and-synapse-analytics-preview"></a>Ausführen der Fail-Aktivität in Azure Data Factory und Synapse Analytics (Vorschau)

Unter gewissen Umständen sollte absichtlich ein Fehler in einer Pipeline ausgelöst werden, z. B wenn die [Lookup-Aktivität](control-flow-lookup-activity.md) keine übereinstimmenden Daten zurückgibt oder [eine benutzerdefinierte Aktivität](transform-data-using-dotnet-custom-activity.md) einen internen Fehler auslöst, obwohl sie 200 zurückgegeben hat. Unabhängig vom Grund können Sie die Fail-Aktivität jetzt in einer Pipeline verwenden und sowohl den Fehlercode als auch die Fehlermeldung anpassen.

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

Eigenschaft | BESCHREIBUNG | Zulässige Werte | Erforderlich
-------- | ----------- | -------------- | --------
name | Der Name der `Fail`-Aktivität. | String | Ja
type | Muss auf **Fail** festgelegt sein | String | Ja
message | In der Fail-Aktivität angezeigte Fehlermeldung. Kann dynamischer Inhalt sein, der zur Laufzeit ausgewertet wird. | String | Ja
errorCode | Fehlercode, der den Fehlertyp der Fehleraktivität kategorisiert. Kann dynamischer Inhalt sein, der zur Laufzeit ausgewertet wird. | String | ja

## <a name="understand-fail-activity-error-code"></a>Grundlegendes zum Fehlercode der Fail-Aktivität

In der Regel werden der Fehlercode und die Fehlermeldung einer Fail-Aktivität vom Kunden festgelegt. Wenden Sie sich an die Pipelineentwickler*innen, um mehr über die spezifischen Bedeutungen der Fehlercodes zu erfahren. In den folgenden Grenzfällen legt jedoch ADF den Fehlercode und/oder die Fehlermeldungen fest.

Situationsbeschreibung | Fehlermeldung | Fehlercode
-------- | ----------- | --------------
Der (dynamische) Inhalt in `message` und `errorCode` wird richtig interpretiert. | Von Benutzer*innen festgelegte Fehlermeldung | Von Benutzer*innen festgelegter Fehlercode
Der dynamische Inhalt in `message` und `errorCode` kann nicht interpretiert werden. | 'Failed to interpret _<activity_name>_ fail message or error code' (Die Fehlermeldung oder der Fehlercode von <Name_der_Aktivität> konnte nicht interpretiert werden.) | `ErrorCodeNotString`
Der dynamische Inhalt in `message` kann nicht als Zeichenfolge interpretiert werden. | ' _<activity_name>_ fail message parameter could not be interpreted as a string' (Der Parameter der Fehlermeldung von <Name_der_Aktivität> konnte nicht als Zeichenfolge interpretiert werden.) | Von Benutzer*innen festgelegter Fehlercode
Der dynamische Inhalt in `message` wird zu NULL, einer leeren Zeichenfolge oder Leerzeichen aufgelöst. | 'Failed to interpret _<activity_name>_ fail message or error code' (Die Fehlermeldung oder der Fehlercode von <Name_der_Aktivität> konnte nicht interpretiert werden.) | Von Benutzer*innen festgelegter Fehlercode
Der dynamische Inhalt in `errorCode` kann nicht als Zeichenfolge interpretiert werden. | Von Benutzer*innen festgelegte Fehlermeldung | `ErrorCodeNotString`
Der dynamische Inhalt in `errorCode` wird zu NULL, einer leeren Zeichenfolge oder Leerzeichen aufgelöst. | Von Benutzer*innen festgelegte Fehlermeldung | `ErrorCodeNotString`
Der vom Benutzer oder der Benutzerin bereitgestellte Wert für `message` oder `errorCode` ist nicht zeichenfolgenfähig.* | Die Pipeline schlägt mit folgender Meldung __fehl__: 'Invalid value for property <`errorCode`/`message`>' (Ungültiger Wert für <Meldung>.)
Fehlendes `message`-Feld* | 'Fail message was not provided' (Die Fehlermeldung wurde nicht bereitgestellt.) | Von Benutzer*innen festgelegter Fehlercode
Fehlendes `errorCode`-Feld* | Von Benutzer*innen festgelegte Fehlermeldung | `ErrorCodeNotString`

Situationen, die mit einem Asterisk (*) gekennzeichnet sind, sollten nicht auftreten, wenn die Pipeline mit der Webbenutzeroberfläche von Data Factory entwickelt wird.

## <a name="next-steps"></a>Nächste Schritte

Machen Sie sich mit weiteren unterstützten Ablaufsteuerungsaktivitäten vertraut, z. B.:

- [Aktivität „If Condition“](control-flow-if-condition-activity.md)
- [Aktivität „Pipeline ausführen“](control-flow-execute-pipeline-activity.md)
- [ForEach-Aktivität](control-flow-for-each-activity.md)
- [Aktivität „Metadaten abrufen“](control-flow-get-metadata-activity.md)
- [Lookup-Aktivität](control-flow-lookup-activity.md)
- [Webaktivität](control-flow-web-activity.md)
- [Until-Aktivität](control-flow-until-activity.md)
