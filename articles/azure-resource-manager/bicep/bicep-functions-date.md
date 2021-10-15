---
title: 'Bicep-Funktionen: date'
description: Hier werden die Funktionen beschrieben, die in einer Bicep-Datei für das Arbeiten mit Datumsangaben verwendet werden.
author: mumian
ms.author: jgao
ms.topic: conceptual
ms.date: 09/30/2021
ms.openlocfilehash: 0eb995db6584dd056fa0b1563aec8c117fddeb66
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2021
ms.locfileid: "129357260"
---
# <a name="date-functions-for-bicep"></a>Datumsfunktionen für Bicep

In diesem Artikel werden die Bicep-Funktionen für die Arbeit mit Datumsangaben beschrieben.

## <a name="datetimeadd"></a>dateTimeAdd

`dateTimeAdd(base, duration, [format])`

Hiermit wird einem Basiswert eine Dauer hinzugefügt. ISO 8601-Format wird erwartet.

Namespace: [sys](bicep-functions.md#namespaces-for-functions).

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| base | Ja | Zeichenfolge | Der datetime-Startwert für die Hinzufügung. Verwenden Sie das [Zeitstempelformat nach ISO 8601](https://en.wikipedia.org/wiki/ISO_8601). |
| duration | Ja | Zeichenfolge | Der Zeitwert, der der Basis hinzugefügt werden soll. Dieser darf kein negativer Wert sein. Verwenden Sie das [Format der Zeitspanne nach ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Durations). |
| format | Nein | Zeichenfolge | Das Ausgabeformat für das datetime-Ergebnis. Ist dieser Wert nicht angegeben, wird das Format des Basiswerts verwendet. Verwenden Sie entweder [Standardformatzeichenfolgen](/dotnet/standard/base-types/standard-date-and-time-format-strings) oder [benutzerdefinierte Formatzeichenfolgen](/dotnet/standard/base-types/custom-date-and-time-format-strings). |

### <a name="return-value"></a>Rückgabewert

Der datetime-Wert, der durch Hinzufügen der Dauer zum Basiswert erhalten wird.

### <a name="examples"></a>Beispiele

Im folgenden Beispiel werden verschiedene Möglichkeiten zum Hinzufügen von time-Werten veranschaulicht.

```bicep
param baseTime string = utcNow('u')

var add3Years = dateTimeAdd(baseTime, 'P3Y')
var subtract9Days = dateTimeAdd(baseTime, '-P9D')
var add1Hour = dateTimeAdd(baseTime, 'PT1H')

output add3YearsOutput string = add3Years
output subtract9DaysOutput string = subtract9Days
output add1HourOutput string = add1Hour
```

Wenn das obige Beispiel mit dem time-Basiswert `2020-04-07 14:53:14Z` bereitgestellt wird, erhalten Sie folgende Ausgabe:

| Name | Typ | Wert |
| ---- | ---- | ----- |
| add3YearsOutput | String | 07.04.2023 14:53:14 |
| subtract9DaysOutput | String | 29.03.2020 14:53:14 |
| add1HourOutput | String | 07.04.2020 15:53:14 |

Im nächsten Beispiel wird veranschaulicht, wie die Startzeit für einen Automation-Zeitplan festgelegt wird.

```bicep
param omsAutomationAccountName string = 'demoAutomation'
param scheduleName string = 'demSchedule1'
param baseTime string = utcNow('u')

var startTime = dateTimeAdd(baseTime, 'PT1H')

...

resource scheduler 'Microsoft.Automation/automationAccounts/schedules@2015-10-31' = {
  name: concat(omsAutomationAccountName, '/', scheduleName)
  properties: {
    description: 'Demo Scheduler'
    startTime: startTime
    interval: 1
    frequency: 'Hour'
  }
}
```

## <a name="utcnow"></a>utcNow

`utcNow(format)`

Gibt den aktuellen datetime-Wert (UTC) im festgelegten Format zurück. Wenn kein Format angegeben wird, wird das ISO 8601-Format (`yyyyMMddTHHmmssZ`) verwendet. **Diese Funktion kann nur für den Standardwert eines Parameters verwendet werden.**

Namespace: [sys](bicep-functions.md#namespaces-for-functions).

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| format |Nein |Zeichenfolge |Der als URI codierte Wert, der in eine Zeichenfolge konvertiert werden soll. Verwenden Sie entweder [Standardformatzeichenfolgen](/dotnet/standard/base-types/standard-date-and-time-format-strings) oder [benutzerdefinierte Formatzeichenfolgen](/dotnet/standard/base-types/custom-date-and-time-format-strings). |

### <a name="remarks"></a>Bemerkungen

Sie können diese Funktion nur in einem Ausdruck für den Standardwert eines Parameters verwenden. Wenn diese Funktion an einer anderen Stelle in einer Bicep-Datei verwendet wird, wird ein Fehler zurückgegeben. Die Funktion ist in anderen Teilen der Bicep-Datei nicht zulässig, da bei jedem Aufruf ein anderer Wert zurückgegeben wird. Das Bereitstellen derselben Bicep-Datei mit denselben Parametern würde nicht zuverlässig zu denselben Ergebnissen führen.

Wenn Sie die [Option für ein Rollback zu einer vorherigen erfolgreichen Bereitstellung bei einem Fehler ](../templates/rollback-on-error.md) verwenden und die vorherige Bereitstellung einen Parameter enthält, der die Funktion „utcNow“ nutzt, wird dieser Parameter nicht erneut ausgewertet. Stattdessen wird der Parameterwert der vorherigen Bereitstellung in der Rollbackbereitstellung automatisch wiederverwendet.

Seien Sie vorsichtig, wenn Sie eine Bicep-Datei erneut bereitstellen, die die utcNow-Funktion für einen Standardwert nutzt. Wenn Sie die erneute Bereitstellung durchführen und keinen Wert für den Parameter bereitstellen, wird die Funktion erneut ausgewertet. Wenn Sie eine vorhandene Ressource aktualisieren möchten, anstatt eine neue zu erstellen, übergeben Sie den Parameterwert aus der früheren Bereitstellung.

### <a name="return-value"></a>Rückgabewert

Der aktuelle UTC-datetime-Wert

### <a name="examples"></a>Beispiele

Im folgenden Beispiel werden verschiedene Formate für den datetime-Wert veranschaulicht.

```bicep
param utcValue string = utcNow()
param utcShortValue string = utcNow('d')
param utcCustomValue string = utcNow('M d')

output utcOutput string = utcValue
output utcShortOutput string = utcShortValue
output utcCustomOutput string = utcCustomValue
```

Die Ausgabe des vorherigen Beispiels variiert bei jeder Bereitstellung. Sie sollte jedoch folgender ähneln:

| Name | Typ | Wert |
| ---- | ---- | ----- |
| utcOutput | Zeichenfolge | 20190305T175318Z |
| utcShortOutput | Zeichenfolge | 03/05/2019 |
| utcCustomOutput | Zeichenfolge | 3 5 |

Im folgenden Beispiel wird gezeigt, wie ein Wert der Funktion beim Festlegen eines Tagwerts verwendet wird.

```bicep
param utcShort string = utcNow('d')
param rgName string

resource myRg 'Microsoft.Resources/resourceGroups@2020-10-01' = {
  name: rgName
  location: 'westeurope'
  tags: {
    createdDate: utcShort
  }
}

output utcShortOutput string = utcShort
```

## <a name="next-steps"></a>Nächste Schritte

* Eine Beschreibung der Abschnitte in einer Bicep-Datei finden Sie unter [Grundlegendes zur Struktur und Syntax von Bicep-Dateien](./file.md).
