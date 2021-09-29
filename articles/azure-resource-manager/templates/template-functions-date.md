---
title: Vorlagenfunktionen – Datumsangaben
description: Informationen zu den Funktionen, die in einer Azure Resource Manager-Vorlage (ARM-Vorlage) zum Arbeiten mit Datumsangaben verwendet werden können.
ms.topic: conceptual
ms.date: 09/09/2021
ms.openlocfilehash: ed7c9d44458acb6733618ff84cf98b9bed2e2a36
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124793415"
---
# <a name="date-functions-for-arm-templates"></a>Datumsfunktionen für ARM-Vorlagen

Resource Manager stellt die folgenden Funktionen für das Arbeiten mit Datumsangaben in Ihren Azure Resource Manager-Vorlagen (ARM-Vorlagen) bereit:

* [dateTimeAdd](#datetimeadd)
* [utcNow](#utcnow)

## <a name="datetimeadd"></a>dateTimeAdd

`dateTimeAdd(base, duration, [format])`

Hiermit wird einem Basiswert eine Dauer hinzugefügt. ISO 8601-Format wird erwartet.

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| base | Ja | Zeichenfolge | Der datetime-Startwert für die Hinzufügung. Verwenden Sie das [Zeitstempelformat nach ISO 8601](https://en.wikipedia.org/wiki/ISO_8601). |
| duration | Ja | Zeichenfolge | Der Zeitwert, der der Basis hinzugefügt werden soll. Dieser darf kein negativer Wert sein. Verwenden Sie das [Format der Zeitspanne nach ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Durations). |
| format | Nein | Zeichenfolge | Das Ausgabeformat für das datetime-Ergebnis. Ist dieser Wert nicht angegeben, wird das Format des Basiswerts verwendet. Verwenden Sie entweder [Standardformatzeichenfolgen](/dotnet/standard/base-types/standard-date-and-time-format-strings) oder [benutzerdefinierte Formatzeichenfolgen](/dotnet/standard/base-types/custom-date-and-time-format-strings). |

### <a name="return-value"></a>Rückgabewert

Der datetime-Wert, der durch Hinzufügen der Dauer zum Basiswert erhalten wird.

### <a name="examples"></a>Beispiele

In der folgenden Beispielvorlage werden verschiedene Möglichkeiten zum Hinzufügen von time-Werten veranschaulicht.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/date/datetimeadd.json":::

Wenn die obige Vorlage mit dem time-Basiswert `2020-04-07 14:53:14Z` bereitgestellt wird, erhalten wir folgende Ausgabe:

| Name | Typ | Wert |
| ---- | ---- | ----- |
| add3YearsOutput | String | 07.04.2023 14:53:14 |
| subtract9DaysOutput | String | 29.03.2020 14:53:14 |
| add1HourOutput | String | 07.04.2020 15:53:14 |

Mit der nächsten Beispielvorlage wird veranschaulicht, wie die Startzeit für einen Automation-Zeitplan festgelegt wird.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/date/datetimeadd-automation.json":::

## <a name="utcnow"></a>utcNow

`utcNow(format)`

Gibt den aktuellen datetime-Wert (UTC) im festgelegten Format zurück. Wenn kein Format angegeben wird, wird das ISO 8601-Format (`yyyyMMddTHHmmssZ`) verwendet. **Diese Funktion kann nur für den Standardwert eines Parameters verwendet werden.**

### <a name="parameters"></a>Parameter

| Parameter | Erforderlich | Typ | BESCHREIBUNG |
|:--- |:--- |:--- |:--- |
| format |Nein |Zeichenfolge |Der als URI codierte Wert, der in eine Zeichenfolge konvertiert werden soll. Verwenden Sie entweder [Standardformatzeichenfolgen](/dotnet/standard/base-types/standard-date-and-time-format-strings) oder [benutzerdefinierte Formatzeichenfolgen](/dotnet/standard/base-types/custom-date-and-time-format-strings). |

### <a name="remarks"></a>Bemerkungen

Sie können diese Funktion nur in einem Ausdruck für den Standardwert eines Parameters verwenden. Wenn diese Funktion an einer anderen Stelle in einer Vorlage verwendet wird, wird ein Fehler zurückgegeben. Die Funktion ist in anderen Teilen der Vorlage nicht zulässig, da bei jedem Aufruf ein anderer Wert zurückgegeben wird. Das Bereitstellen der gleichen Vorlage mit den gleichen Parametern würde nicht zuverlässig zu den gleichen Ergebnissen führen.

Wenn Sie die [Option für ein Rollback zu einer vorherigen erfolgreichen Bereitstellung bei einem Fehler ](rollback-on-error.md) verwenden und die vorherige Bereitstellung einen Parameter enthält, der die Funktion „utcNow“ nutzt, wird dieser Parameter nicht erneut ausgewertet. Stattdessen wird der Parameterwert der vorherigen Bereitstellung in der Rollbackbereitstellung automatisch wiederverwendet.

Seien Sie vorsichtig, wenn Sie eine Vorlage erneut bereitstellen, die die utcNow-Funktion für einen Standardwert nutzt. Wenn Sie die erneute Bereitstellung durchführen und keinen Wert für den Parameter bereitstellen, wird die Funktion erneut ausgewertet. Wenn Sie eine vorhandene Ressource aktualisieren möchten, anstatt eine neue zu erstellen, übergeben Sie den Parameterwert aus der früheren Bereitstellung.

### <a name="return-value"></a>Rückgabewert

Der aktuelle UTC-datetime-Wert

### <a name="examples"></a>Beispiele

In der folgenden Beispielvorlage werden verschiedene Formate für den datetime-Wert veranschaulicht.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/date/utcnow.json":::

Die Ausgabe des vorherigen Beispiels variiert bei jeder Bereitstellung. Sie sollte jedoch folgender ähneln:

| Name | Typ | Wert |
| ---- | ---- | ----- |
| utcOutput | Zeichenfolge | 20190305T175318Z |
| utcShortOutput | Zeichenfolge | 03/05/2019 |
| utcCustomOutput | Zeichenfolge | 3 5 |

Im folgenden Beispiel wird gezeigt, wie ein Wert der Funktion beim Festlegen eines Tagwerts verwendet wird.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/date/utcnow-tag.json":::

## <a name="next-steps"></a>Nächste Schritte

* Eine Beschreibung der Abschnitte in einer ARM-Vorlage finden Sie unter [Grundlegendes zur Struktur und Syntax von ARM-Vorlagen](./syntax.md).
