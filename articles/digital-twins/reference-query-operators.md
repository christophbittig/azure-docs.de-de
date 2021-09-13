---
title: 'Referenz zur Azure Digital Twins-Abfragesprache: Operatoren'
titleSuffix: Azure Digital Twins
description: Referenzdokumentation zu den Operatoren der Azure Digital Twins-Abfragesprache
author: baanders
ms.author: baanders
ms.date: 03/22/2021
ms.topic: article
ms.service: digital-twins
ms.openlocfilehash: 06945efc6be8700955b1a475d00d21951102c214
ms.sourcegitcommit: bc29cf4472118c8e33e20b420d3adb17226bee3f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/08/2021
ms.locfileid: "113492791"
---
# <a name="azure-digital-twins-query-language-reference-operators"></a>Referenz zur Azure Digital Twins-Abfragesprache: Operatoren

Dieses Dokument enthält Referenzinformationen zu **Operatoren** für die [Azure Digital Twins-Abfragesprache](concepts-query-language.md).

## <a name="comparison-operators"></a>Vergleichsoperatoren

Die folgenden Operatoren aus der Vergleichsfamilie werden unterstützt.

* `=`, `!=`: Wird zum Vergleichen der Gleichheit von Ausdrücken verwendet.
* `<`, `>`: Wird für den geordneten Vergleich von Ausdrücken verwendet.
* `<=`, `>=`: Wird für den geordneten Vergleich von Ausdrücken, einschließlich Gleichheit, verwendet.

### <a name="example"></a>Beispiel

Hier sehen Sie ein Beispiel mit `=`. Die folgende Abfrage gibt Zwillinge zurück, deren Temperaturwert gleich 80 ist.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="EqualityExample":::

Hier sehen Sie ein Beispiel mit `<`. Die folgende Abfrage gibt Zwillinge zurück, deren Temperaturwert kleiner als 80 ist.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="ComparisonExample":::

Hier sehen Sie ein Beispiel mit `<=`. Die folgende Abfrage gibt Zwillinge zurück, deren Temperaturwert kleiner als oder gleich 80 ist.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="OrderedComparisonExample":::

## <a name="contains-operators"></a>Enthält Operatoren

Die folgenden Operatoren aus der „contains“-Familie werden unterstützt.

* `IN`: Wird als „true“ ausgewertet, wenn der angegebene Wert in einer Gruppe von Werten enthalten ist.
* `NIN`: Wird als „true“ ausgewertet, wenn der angegebene Wert nicht in einer Gruppe von Werten enthalten ist.

### <a name="example"></a>Beispiel

Hier sehen Sie ein Beispiel mit `IN`. Die folgende Abfrage gibt Zwillinge zurück, deren `owner`-Eigenschaft eine von mehreren Optionen in einer Liste ist.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="InExample":::

## <a name="logical-operators"></a>Logische Operatoren

Die folgenden Operatoren aus der logischen Familie werden unterstützt:
* `AND`: Dient zum Verbinden zweier Ausdrücke und wird als „true“ ausgewertet, wenn beide „true“ sind.
* `OR`: Dient zum Verbinden zweier Ausdrücke und wird zu „true“ ausgewertet, wenn mindestens einer von beiden „true“ ist.
* `NOT`: Dient zum Negieren eines Ausdrucks und wird als „true“ ausgewertet, wenn die Ausdrucksbedingung nicht erfüllt ist.

### <a name="example"></a>Beispiel

Hier sehen Sie ein Beispiel mit `AND`. Die folgende Abfrage gibt Zwillinge zurück, die die beiden Bedingungen „Temperatur kleiner als 80“ und „Feuchtigkeit kleiner als 50“ erfüllen.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="AndExample":::

Hier sehen Sie ein Beispiel mit `OR`. Die folgende Abfrage gibt Zwillinge zurück, die mindestens eine der beiden Bedingungen „Temperatur kleiner als 80“ und „Feuchtigkeit kleiner als 50“ erfüllen.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="OrExample":::

Hier sehen Sie ein Beispiel mit `NOT`. Die folgende Abfrage gibt Zwillinge zurück, die die Bedingung „Temperatur kleiner als 80“ nicht erfüllen.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="NotExample":::

## <a name="limitations"></a>Einschränkungen

Für Abfragen mit Operatoren gelten die folgenden Grenzwerte.
* [Grenzwert für IN/NIN](#limit-for-innin)

Ausführliche Informationen finden Sie im Abschnitt unten.

### <a name="limit-for-innin"></a>Grenzwert für IN/NIN

Der Grenzwert für die Anzahl der Werte, die in einen `IN`- oder `NIN`-Satz eingeschlossen werden können, beträgt 100 Werte.
