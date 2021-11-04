---
title: Einrichtungsontologie in Microsoft Azure Maps Creator
description: Einrichtungsontologie zur Beschreibung der Featureklassendefinitionen für Azure Maps Creator
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/14/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
zone_pivot_groups: facility-ontology-schema
ms.openlocfilehash: 3ef3d9dd9d992e17c66516de8d13978e6c7c4821
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131450797"
---
# <a name="facility-ontology"></a>Einrichtungsontologie

Die Einrichtungsontologie definiert die interne Speicherung der Daten von Einrichtungen in einem Creator-Dataset durch Azure Maps Creator.  Zusätzlich zur Definition der internen Datenstruktur von Einrichtungen wird die Einrichtungsontologie auch extern über die WFS-API verfügbar gemacht. Wenn die WFS-API zum Abfragen von Einrichtungsdaten in einem Dataset verwendet wird, wird das Antwortformat durch die für dieses Dataset bereitgestellte Ontologie definiert.

Auf hoher Ebene wird das Dataset durch die Einrichtungsontologie in Featureklassen unterteilt. Alle Featureklassen verfügen über eine Gruppe gemeinsamer Eigenschaften wie `ID` und `Geometry`.  Zusätzlich zu den gemeinsamen Eigenschaften definiert jede Featureklasse eigene Eigenschaften. Die einzelnen Eigenschaften werden jeweils durch ihren Datentyp und ihre Einschränkungen definiert. Einige Featureklassen verfügen über Eigenschaften, die von anderen Featureklassen abhängig sind. Abhängige Eigenschaften werden zur `ID` einer anderen Featureklasse ausgewertet.  

## <a name="changes-and-revisions"></a>Änderungen und Revisionen

:::zone pivot="facility-ontology-v1"

Facility 1.0 enthält Revisionen für die Facility-Featureklassendefinitionen für [Azure Maps Services](https://aka.ms/AzureMaps).

:::zone-end

:::zone pivot="facility-ontology-v2"

Facility 2.0 enthält Revisionen für die Facility-Featureklassendefinitionen für [Azure Maps Services](https://aka.ms/AzureMaps).

:::zone-end

### <a name="major-changes"></a>Bedeutende Änderungen

:::zone pivot="facility-ontology-v1"

Folgende Überprüfungen der Einschränkungsvalidierung wurden korrigiert:

* Überprüfung im Rahmen der Einschränkungsvalidierung auf Exklusivität von `isObstruction = true` *oder* auf das Vorhandensein von `obstructionArea` für die Featureklassen `lineElement` und `areaElement`

* Überprüfung im Rahmen der Einschränkungsvalidierung auf Exklusivität von `isRoutable = true` *oder* auf das Vorhandensein von `routeThroughBehavior` für die Featureklasse `category`
:::zone-end

:::zone pivot="facility-ontology-v2"

* Es wurde eine Strukturfeatureklasse für Wände, Säulen und Ähnliches hinzugefügt.
* Die für die Anreicherung von Routenplanungsszenarien vorgesehenen Attribute wurden bereinigt. Sie werden von der aktuellen Routenplanungsengine nicht unterstützt.

:::zone-end

## <a name="unit"></a>unit

Die Featureklasse `unit` definiert einen physischen Bereich ohne Überlappung, der von einem Navigations-Agent genutzt und passiert werden kann. `unit` kann ein Flur, ein Raum, ein Innenhof oder Ähnliches sein.

**Geometrietyp:** Polygon

:::zone pivot="facility-ontology-v1"

| Eigenschaft           | type                        | Erforderlich | BESCHREIBUNG                                                  |
|--------------------|-----------------------------|----------|--------------------------------------------------------------|
|`originalId`        | Zeichenfolge     |true      | Die aus Clientdaten abgeleitete ID. Maximal zulässige Länge: 1.000.|
|`externalId`        | Zeichenfolge     |true      | Eine ID, die vom Client verwendet wird, um das Feature einem anderen Feature in einem anderen Dataset (beispielsweise in einer internen Datenbank) zuzuordnen. Maximal zulässige Länge: 1.000.|
|`categoryId`        | [category.Id](#category)     |true      | Die ID eines Features vom Typ [`category`](#category).|
|`isOpenArea`        | Boolescher Wert (Standardwert: `null`)                    |false     | Gibt an, ob es sich bei der Einheit um einen offenen Bereich handelt. Ist diese Option auf `true` festgelegt, ist die Einheitengrenze nicht von [Strukturen](#structure) umgeben, und `unit` ist ohne [`opening`](#opening) für einen Navigations-Agent zugänglich. Standardmäßig sind Einheiten von physischen Barrieren umgeben und nur offen, wenn an der Grenze der Einheit ein Feature vom Typ „opening“ platziert wird. Falls in einer Einheit mit offenem Bereich Wände benötigt werden, können sie als [`lineElement`](#lineelement) oder [`areaElement`](#areaelement) mit einer auf `true` festgelegten Eigenschaft vom Typ `isObstruction`dargestellt werden.|
|`navigableBy`        | enum ["hire", "railcar", "machine", "bicycle", "automobile", "hiredAuto", "bus", "railcar", "emergency", "ferry", "people"]  | false      |Gibt an, für welche Arten der Fortbewegung die Einheit geeignet ist. Ohne Angabe wird davon ausgegangen, dass die Einheit für jeden beliebigen Navigations-Agent passierbar ist. |
|`isRoutable`        | Boolescher Wert (Standardwert: `null`)                      | false    |  Bestimmt, ob die Einheit Teil des Routenplanungsgraphen ist. Ist diese Einstellung auf `true` festgelegt, kann die Einheit als Ausgangspunkt/Ziel oder Zwischenknoten der Routenplanung verwendet werden. |
|`routeThroughBehavior`        | enum ["disallowed", "allowed", "preferred"] |  false     | Bestimmt, ob durch die Einheit navigiert werden kann. Ohne Angabe wird der Wert vom Kategoriefeature geerbt, auf das in der Eigenschaft `categoryId` verwiesen wird. Ist diese Option angegeben, wird dadurch der im zugehörigen Kategoriefeature angegebene Wert überschrieben. |
|`nonPublic`        |  boolean| false       | Ist diese Option auf `true` festgelegt, dürfen nur privilegierte Benutzer durch die Einheit navigieren.  Der Standardwert ist `false`sein. |
| `levelId`          | [level.Id](#level)        | true     | Die ID eines Ebenenfeatures. |
|`occupants`         |  Array mit [directoryInfo.Id](#directoryinfo) |    false |    Die IDs von Features des Typs [directoryInfo](#directoryinfo). Dient zur Darstellung einzelner oder mehrerer Gebäudenutzer im Feature. |
|`addressId`         | [directoryInfo.Id](#directoryinfo) | true     | Die ID eines Features vom Typ [directoryInfo](#directoryinfo). Dient zur Darstellung der Adresse des Features.|
|`addressRoomNumber`         |  [directoryInfo.Id](#directoryinfo) | true     | Raum-/Einheiten-/Wohnungs-/Suitennummer der Einheit.|
|`name` |    Zeichenfolge |    false |    Name des Features in der Landessprache. Maximal zulässige Länge: 1.000. |
|`nameSubtitle` |    Zeichenfolge |    false |   Untertitel, der unter dem Namen (`name`) des Features angezeigt wird. Kann beispielsweise verwendet werden, um den Namen in einer anderen Sprache anzuzeigen.  Maximal zulässige Länge: 1.000.|
|`nameAlt` |    Zeichenfolge |    false |   Alternativer Name für das Feature. Maximal zulässige Länge: 1.000. |
|`anchorPoint` |   [Point](/rest/api/maps/wfs/get-feature#featuregeojson) | false | [GeoJSON-Punktgeometrie](/rest/api/maps/wfs/get-feature#featuregeojson), durch die das Feature als Punkt dargestellt wird. Kann verwendet werden, um die Bezeichnung des Features zu positionieren.|

:::zone-end

:::zone pivot="facility-ontology-v2"

| Eigenschaft           | type                        | Erforderlich | BESCHREIBUNG                                                  |
|--------------------|-----------------------------|----------|--------------------------------------------------------------|
|`originalId`        | Zeichenfolge     |true      | Die aus Clientdaten abgeleitete ID. Maximal zulässige Länge: 1.000.|
|`externalId`        | Zeichenfolge     |true      | Eine ID, die vom Client verwendet wird, um das Feature einem anderen Feature in einem anderen Dataset (beispielsweise in einer internen Datenbank) zuzuordnen. Maximal zulässige Länge: 1.000.|
|`categoryId`        | [category.Id](#category)     |true      | Die ID eines Features vom Typ [`category`](#category).|
|`isOpenArea`        | Boolescher Wert (Standardwert: `null`)                    |false     | Gibt an, ob es sich bei der Einheit um einen offenen Bereich handelt. Ist diese Option auf `true` festgelegt, ist die Einheitengrenze nicht von [Strukturen](#structure) umgeben, und `unit` ist ohne [`opening`](#opening) für einen Navigations-Agent zugänglich. Standardmäßig sind Einheiten von physischen Barrieren umgeben und nur offen, wenn an der Grenze der Einheit ein Feature vom Typ „opening“ platziert wird. Falls in einer Einheit mit offenem Bereich Wände benötigt werden, können sie als [`lineElement`](#lineelement) oder [`areaElement`](#areaelement) mit einer auf `true` festgelegten Eigenschaft vom Typ `isObstruction`dargestellt werden.|
|`isRoutable`        | Boolescher Wert (Standardwert: `null`)                     | false    |  Bestimmt, ob die Einheit Teil des Routenplanungsgraphen ist. Ist diese Einstellung auf `true` festgelegt, kann die Einheit als Ausgangspunkt/Ziel oder Zwischenknoten der Routenplanung verwendet werden. |
| `levelId`          | [level.Id](#level)        | true     | Die ID eines Ebenenfeatures. |
|`occupants`         |  Array mit [directoryInfo.Id](#directoryinfo) |    false |    Die IDs von Features des Typs [directoryInfo](#directoryinfo). Dient zur Darstellung einzelner oder mehrerer Gebäudenutzer im Feature. |
|`addressId`         | [directoryInfo.Id](#directoryinfo) | true     | Die ID eines Features vom Typ [directoryInfo](#directoryinfo). Dient zur Darstellung der Adresse des Features.|
|`addressRoomNumber`         |  [directoryInfo.Id](#directoryinfo) | true     | Raum-/Einheiten-/Wohnungs-/Suitennummer der Einheit.|
|`name` |    Zeichenfolge |    false |    Name des Features in der Landessprache.  Maximal zulässige Länge: 1.000.|
|`nameSubtitle` |    Zeichenfolge |    false |   Untertitel, der unter dem Namen (`name`) des Features angezeigt wird. Kann beispielsweise verwendet werden, um den Namen in einer anderen Sprache anzuzeigen.  Maximal zulässige Länge: 1.000.|
|`nameAlt` |    Zeichenfolge |    false |   Alternativer Name für das Feature.  Maximal zulässige Länge: 1.000.|
|`anchorPoint` |   [Point](/rest/api/maps/wfs/get-feature#featuregeojson) | false | [GeoJSON-Punktgeometrie](/rest/api/maps/wfs/get-feature#featuregeojson), durch die das Feature als Punkt dargestellt wird. Kann verwendet werden, um die Bezeichnung des Features zu positionieren.|

:::zone-end

:::zone pivot="facility-ontology-v2"

## <a name="structure"></a>structure

Die Featureklasse `structure` definiert einen physischen Bereich ohne Überlappung, durch den nicht navigiert werden kann. Hierbei kann es sich beispielsweise um eine Wand oder um eine Säule handeln.

**Geometrietyp:** Polygon

| Eigenschaft  | type | Erforderlich | BESCHREIBUNG |
|-----------|------|----------|-------------|
|`originalId`        | Zeichenfolge     |true      | Die aus Clientdaten abgeleitete ID. Maximal zulässige Länge: 1.000.|
|`externalId`        | Zeichenfolge     |true      | Eine ID, die vom Client verwendet wird, um das Feature einem anderen Feature in einem anderen Dataset (beispielsweise in einer internen Datenbank) zuzuordnen. Maximal zulässige Länge: 1.000.|
|`categoryId`        | [category.Id](#category)      |true      | Die ID eines Features vom Typ [`category`](#category).|
| `levelId`          |  [level.Id](#level)            | true     | Die ID eines Features vom Typ [`level`](#level). |
|`name` |    Zeichenfolge |    false |    Name des Features in der Landessprache. Maximal zulässige Länge: 1.000. |
|`nameSubtitle` |    Zeichenfolge |    false |   Untertitel, der unter dem Namen (`name`) des Features angezeigt wird. Kann beispielsweise verwendet werden, um den Namen in einer anderen Sprache anzuzeigen. Maximal zulässige Länge: 1.000. |
|`nameAlt` |    Zeichenfolge |    false |   Alternativer Name für das Feature.  Maximal zulässige Länge: 1.000.|
|`anchorPoint` |   [Point](/rest/api/maps/wfs/get-feature#featuregeojson) | false | [GeoJSON-Punktgeometrie](/rest/api/maps/wfs/get-feature#featuregeojson), durch die das Feature als Punkt dargestellt wird. Kann verwendet werden, um die Bezeichnung des Features zu positionieren.|

:::zone-end

## <a name="zone"></a>Zone

Die Featureklasse `zone` definiert einen virtuellen Bereich – beispielsweise eine WLAN-Zone oder ein Sammelplatz. Zonen können als Ziele verwendet werden, sind aber nicht für Durchgangsverkehr bestimmt.

**Geometrietyp:** Polygon

| Eigenschaft  | type | Erforderlich | BESCHREIBUNG |
|-----------|------|----------|-------------|
|`originalId`        | Zeichenfolge     |true      | Die aus Clientdaten abgeleitete ID. Maximal zulässige Länge: 1.000.|
|`externalId`        | Zeichenfolge     |true      | Eine ID, die vom Client verwendet wird, um das Feature einem anderen Feature in einem anderen Dataset (beispielsweise in einer internen Datenbank) zuzuordnen. Maximal zulässige Länge: 1.000.|
|`categoryId`        | [category.Id](#category)      |true      | Die ID eines Features vom Typ [`category`](#category).|
| `setId`          | Zeichenfolge         | true     |Erforderlich für Zonenfeatures, die Zonen mit mehreren Ebenen darstellen. `setId` ist die eindeutige ID für eine Zone, die mehrere Ebenen umfasst. `setId` ermöglicht es, eine Zone mit unterschiedlicher Abdeckung auf verschiedenen Etagen unter Verwendung von unterschiedlicher Geometrie auf unterschiedlichen Ebenen darzustellen. `setId` kann eine beliebige Zeichenfolge sein. Die Groß-/Kleinschreibung wird beachtet. `setId` sollte eine GUID sein.  Maximal zulässige Länge: 1.000.|
| `levelId`          |  [level.Id](#level)             | true     | Die ID eines Features vom Typ [`level`](#level). |
|`name` |    Zeichenfolge |    false |    Name des Features in der Landessprache.  Maximal zulässige Länge: 1.000.|
|`nameSubtitle` |    Zeichenfolge |    false |   Untertitel, der unter dem Namen (`name`) des Features angezeigt wird. Kann beispielsweise verwendet werden, um den Namen in einer anderen Sprache anzuzeigen.  Maximal zulässige Länge: 1.000.|
|`nameAlt` |    Zeichenfolge |    false |   Alternativer Name für das Feature. Maximal zulässige Länge: 1.000. |
|`anchorPoint` |  [Point](/rest/api/maps/wfs/get-feature#featuregeojson) | false | [GeoJSON-Punktgeometrie](/rest/api/maps/wfs/get-feature#featuregeojson), durch die das Feature als Punkt dargestellt wird. Kann verwendet werden, um die Bezeichnung des Features zu positionieren.|

## <a name="level"></a>Level

Das Klassenfeature `level` definiert einen Bereich eines Gebäudes in einer bestimmten Höhe. Ein Beispiel wäre etwa die Etage eines Gebäudes, mit einer Reihe von Features wie etwa [`units`](#unit).  

**Geometrietyp:** MultiPolygon

| Eigenschaft  | type | Erforderlich | BESCHREIBUNG |
|-----------|------|----------|-------------|
|`originalId`        | Zeichenfolge     |true      | Die aus Clientdaten abgeleitete ID. Maximal zulässige Länge: 1.000.|
|`externalId`        | Zeichenfolge     |true      | Eine ID, die vom Client verwendet wird, um das Feature einem anderen Feature in einem anderen Dataset (beispielsweise in einer internen Datenbank) zuzuordnen. Maximal zulässige Länge: 1.000.|
|`categoryId`        | [category.Id](#category)    |true      | Die ID eines Features vom Typ [`category`](#category).|
| `ordinal`          | integer        | true     | Die Nummer der Ebene. Wird vom Feature [`verticalPenetration`](#verticalpenetration) verwendet, um die relative Reihenfolge der Etagen zu bestimmen, was für die Bewegungsrichtung hilfreich ist. Das Erdgeschoss hat üblicherweise den Wert 0. Für darüber liegende Etagen wird jeweils 1 addiert und für darunter liegende Etagen jeweils 1 abgezogen. Für das Modell können beliebige Zahlen verwendet werden. Höher gelegene physische Etagen müssen allerdings durch höhere Ordinalwerte dargestellt werden. |
| `abbreviatedName`          | Zeichenfolge        | false     | Ein vierstelliger abgekürzter Ebenenname – beispielsweise wie auf einer Aufzugstaste.  Maximal zulässige Länge: 1.000.|
| `heightAboveFacilityAnchor`          | double         | false     | Vertikale Entfernung zwischen der Etagenebene und [`facility.anchorHeightAboveSeaLevel`](#facility) (in Metern). |
| `verticalExtent`          | double         | false     | Vertikale Größe der Ebene (in Metern). Ohne Angabe wird standardmäßig [`facility.defaultLevelVerticalExtent`](#facility) verwendet.|
|`name` |    Zeichenfolge |    false |    Name des Features in der Landessprache.  Maximal zulässige Länge: 1.000.|
|`nameSubtitle` |    Zeichenfolge |    false |   Untertitel, der unter dem Namen (`name`) des Features angezeigt wird. Kann beispielsweise verwendet werden, um den Namen in einer anderen Sprache anzuzeigen.  Maximal zulässige Länge: 1.000.|
|`nameAlt` |    Zeichenfolge |    false |   Alternativer Name für das Feature.  Maximal zulässige Länge: 1.000.|
|`anchorPoint` |   [Point](/rest/api/maps/wfs/get-feature#featuregeojson) | false | [GeoJSON-Punktgeometrie](/rest/api/maps/wfs/get-feature#featuregeojson), durch die das Feature als Punkt dargestellt wird. Kann verwendet werden, um die Bezeichnung des Features zu positionieren.|

## <a name="facility"></a>Funktion

Die Featureklasse `facility` definiert den Bereich des Standorts, die Gebäudegrundfläche und Ähnliches.

**Geometrietyp:** MultiPolygon

| Eigenschaft  | type | Erforderlich | BESCHREIBUNG |
|-----------|------|----------|-------------|
|`originalId`        | Zeichenfolge     |true      | Die aus Clientdaten abgeleitete ID. Maximal zulässige Länge: 1.000.|
|`externalId`        | Zeichenfolge     |true      | Eine ID, die vom Client verwendet wird, um das Feature einem anderen Feature in einem anderen Dataset (beispielsweise in einer internen Datenbank) zuzuordnen. Maximal zulässige Länge: 1.000.|
|`categoryId`        | [category.Id](#category)      |true      | Die ID eines Features vom Typ [`category`](#category).|
|`occupants`         | Array mit [directoryInfo.Id](#directoryinfo) |    false |    Die IDs von Features des Typs [directoryInfo](#directoryinfo). Dient zur Darstellung einzelner oder mehrerer Gebäudenutzer im Feature. |
|`addressId`         | [directoryInfo.Id](#directoryinfo)  | true     | Die ID eines Features vom Typ [directoryInfo](#directoryinfo). Dient zur Darstellung der Adresse des Features.|
|`name` |    Zeichenfolge |    false |    Name des Features in der Landessprache. Maximal zulässige Länge: 1.000. |
|`nameSubtitle` |    Zeichenfolge |    false |   Untertitel, der unter dem Namen (`name`) des Features angezeigt wird. Kann beispielsweise verwendet werden, um den Namen in einer anderen Sprache anzuzeigen. Maximal zulässige Länge: 1.000. |
|`nameAlt` |    Zeichenfolge |    false |   Alternativer Name für das Feature.  Maximal zulässige Länge: 1.000.|
|`anchorPoint` |  [Point](/rest/api/maps/wfs/get-feature#featuregeojson) | false | [GeoJSON-Punktgeometrie](/rest/api/maps/wfs/get-feature#featuregeojson), durch die das Feature als Punkt dargestellt wird. Kann verwendet werden, um die Bezeichnung des Features zu positionieren.|
|`anchorHeightAboveSeaLevel` |  double | false | Höhe des Ankerpunkts über dem Meeresspiegel (in Metern). Der Meeresspiegel wird durch EGM2008 definiert.|
|`defaultLevelVerticalExtent` |  double| false | Standardwert für die vertikale Größe von Ebenen (in Metern).|

## <a name="verticalpenetration"></a>verticalPenetration

Das Klassenfeature `verticalPenetration` definiert einen Bereich, der bei Verwendung in einer Gruppe eine Methode für die vertikale Navigation zwischen Ebenen darstellt. Es kann zum Modellieren von Treppen, Aufzügen und Ähnlichem verwendet werden. Die Geometrie kann mit Einheiten und anderen vertikalen Durchdringungsfeatures überlappen.

**Geometrietyp:** Polygon

:::zone pivot="facility-ontology-v1"

| Eigenschaft  | type | Erforderlich | BESCHREIBUNG |
|-----------|------|----------|-------------|
|`originalId`        | Zeichenfolge     |true      | Die aus Clientdaten abgeleitete ID. Maximal zulässige Länge: 1.000.|
|`externalId`        | Zeichenfolge     |true      | Eine ID, die vom Client verwendet wird, um das Feature einem anderen Feature in einem anderen Dataset (beispielsweise in einer internen Datenbank) zuzuordnen. Maximal zulässige Länge: 1.000.|
|`categoryId`        | [category.Id](#category)      |true      | Die ID eines Features vom Typ [`category`](#category).|
| `setId`          | Zeichenfolge       | true     | Vertikale Durchdringungsfeatures müssen in Gruppen verwendet werden, um mehrere Ebenen miteinander zu verbinden. Vertikale Durchdringungsfeatures in der gleichen Gruppe werden als identisch betrachtet. `setId` kann eine beliebige Zeichenfolge sein. Die Groß-/Kleinschreibung wird beachtet. Als `setId` sollte eine GUID verwendet werden.  Maximal zulässige Länge: 1.000.|
| `levelId`          | [level.Id](#level)         | true     | Die ID eines Ebenenfeatures. |
|`direction`         |  string enum [ "both", "lowToHigh", "highToLow", "closed" ]| false     | Für dieses Feature zulässige Bewegungsrichtung. Das Ordinalattribut für das Feature [`level`](#level) wird zur Bestimmung der niedrigen und hohen Ordnung verwendet.|
|`navigableBy`        | enum ["hire", "railcar", "machine", "bicycle", "automobile", "hiredAuto", "bus", "railcar", "emergency", "ferry", "people"]  | false      |Gibt an, für welche Arten der Fortbewegung die Einheit geeignet ist Ohne Angabe ist die Einheit für jeden beliebigen Navigations-Agent passierbar. |
|`nonPublic`        |  boolean| false       | Ist diese Option auf `true` festgelegt, dürfen nur privilegierte Benutzer durch die Einheit navigieren.  Der Standardwert ist `false`sein. |
|`name` |    Zeichenfolge |    false |    Name des Features in der Landessprache.  Maximal zulässige Länge: 1.000.|
|`nameSubtitle` |    Zeichenfolge |    false |   Untertitel, der unter dem Namen (`name`) des Features angezeigt wird. Kann beispielsweise verwendet werden, um den Namen in einer anderen Sprache anzuzeigen.  Maximal zulässige Länge: 1.000.|
|`nameAlt` |    Zeichenfolge |    false |   Alternativer Name für das Feature. Maximal zulässige Länge: 1.000. |
|`anchorPoint` |  [Point](/rest/api/maps/wfs/get-feature#featuregeojson) | false | [GeoJSON-Punktgeometrie](/rest/api/maps/wfs/get-feature#featuregeojson), durch die das Feature als Punkt dargestellt wird. Kann verwendet werden, um die Bezeichnung des Features zu positionieren.|

:::zone-end

:::zone pivot="facility-ontology-v2"

| Eigenschaft  | type | Erforderlich | BESCHREIBUNG |
|-----------|------|----------|-------------|
|`originalId`        | Zeichenfolge     |true      | Die aus Clientdaten abgeleitete ID. Maximal zulässige Länge: 1.000.|
|`externalId`        | Zeichenfolge     |true      | Eine ID, die vom Client verwendet wird, um das Feature einem anderen Feature in einem anderen Dataset (beispielsweise in einer internen Datenbank) zuzuordnen. Maximal zulässige Länge: 1.000.|
|`categoryId`        | [category.Id](#category)      |true      | Die ID eines Features vom Typ [`category`](#category).|
| `setId`          | Zeichenfolge       | true     | Vertikale Durchdringungsfeatures müssen in Gruppen verwendet werden, um mehrere Ebenen miteinander zu verbinden. Vertikale Durchdringungsfeatures in der gleichen Gruppe sind miteinander verbunden. `setId` kann eine beliebige Zeichenfolge sein. Die Groß-/Kleinschreibung wird beachtet. Als `setId` sollte eine GUID verwendet werden. Maximal zulässige Länge: 1.000. |
| `levelId`          | [level.Id](#level)         | true     | Die ID eines Ebenenfeatures. |
|`direction`         |  string enum [ "both", "lowToHigh", "highToLow", "closed" ]| false     | Für dieses Feature zulässige Bewegungsrichtung. Das Ordinalattribut für das Feature [`level`](#level) wird zur Bestimmung der niedrigen und hohen Ordnung verwendet.|
|`name` |    Zeichenfolge |    false |    Name des Features in der Landessprache.  Maximal zulässige Länge: 1.000.|
|`nameSubtitle` |    Zeichenfolge |    false |   Untertitel, der unter dem Namen (`name`) des Features angezeigt wird. Kann beispielsweise verwendet werden, um den Namen in einer anderen Sprache anzuzeigen.  Maximal zulässige Länge: 1.000.|
|`nameAlt` |    Zeichenfolge |    false |   Alternativer Name für das Feature. Maximal zulässige Länge: 1.000. |
|`anchorPoint` |  [Point](/rest/api/maps/wfs/get-feature#featuregeojson) | false | [GeoJSON-Punktgeometrie](/rest/api/maps/wfs/get-feature#featuregeojson), durch die das Feature als Punkt dargestellt wird. Kann verwendet werden, um die Bezeichnung des Features zu positionieren.|

:::zone-end

## <a name="opening"></a>Öffnungszeiten

Das Klassenfeature `opening` definiert eine passierbare Grenze zwischen zwei Einheiten oder eine Einheit (`unit`) und eine vertikale Durchdringung (`verticalPenetration`).

**Geometrietyp:** LineString

:::zone pivot="facility-ontology-v1"

| Eigenschaft  | type | Erforderlich | BESCHREIBUNG |
|-----------|------|----------|-------------|
|`originalId`        | Zeichenfolge     |true      | Die aus Clientdaten abgeleitete ID. Maximal zulässige Länge: 1.000.|
|`externalId`        | Zeichenfolge     |true      | Eine ID, die vom Client verwendet wird, um das Feature einem anderen Feature in einem anderen Dataset (beispielsweise in einer internen Datenbank) zuzuordnen. Maximal zulässige Länge: 1.000.|
|`categoryId`        |[category.Id](#category)     |true      | Die ID eines Kategoriefeatures.|
| `levelId`          | [level.Id](#level)        | true     | Die ID eines Ebenenfeatures. |
| `isConnectedToVerticalPenetration` | boolean | false | Gibt an, ob eine Seite dieses Features mit einem Feature vom Typ `verticalPenetration` verbunden ist. Der Standardwert ist `false`sein. |
|`navigableBy`        | enum ["hire", "railcar", "machine", "bicycle", "automobile", "hiredAuto", "bus", "railcar", "emergency", "ferry", "people"]  | false      |Gibt an, für welche Arten der Fortbewegung die Einheit geeignet ist Ohne Angabe ist die Einheit für jeden beliebigen Navigations-Agent passierbar. |
| `accessRightToLeft`| enum [ "prohibited", "digitalKey", "physicalKey", "keyPad", "guard", "ticket", "fingerprint", "retina", "voice", "face", "palm", "iris", "signature", "handGeometry", "time", "ticketChecker", "other"] | false | Zugangsmethode, wenn die Öffnung von rechts nach links passiert wird. Links und rechts werden durch die Vertices in der Featuregeometrie bestimmt. Ausgangspunkt ist hierbei der erste Vertex mit Blick in Richtung des zweiten Vertex. Wird diese Eigenschaft weggelassen, bedeutet das, dass keine Zugangsbeschränkungen gelten.|
| `accessLeftToRight`| enum [ "prohibited", "digitalKey", "physicalKey", "keyPad", "guard", "ticket", "fingerprint", "retina", "voice", "face", "palm", "iris", "signature", "handGeometry", "time", "ticketChecker", "other"] | false | Zugangsmethode, wenn die Öffnung von links nach rechts passiert wird. Links und rechts werden durch die Vertices in der Featuregeometrie bestimmt. Ausgangspunkt ist hierbei der erste Vertex mit Blick in Richtung des zweiten Vertex. Wird diese Eigenschaft weggelassen, bedeutet das, dass keine Zugangsbeschränkungen gelten.|
| `isEmergency` | boolean | false | Ist diese Option auf `true` festgelegt, kann die Öffnung nur im Notfall verwendet werden. Standardwert: `false` |
|`anchorPoint` | [Point](/rest/api/maps/wfs/get-feature#featuregeojson) | false | [GeoJSON-Punktgeometrie](/rest/api/maps/wfs/get-feature#featuregeojson), durch die das Feature als Punkt dargestellt wird. Kann verwendet werden, um die Bezeichnung des Features zu positionieren.|

:::zone-end

:::zone pivot="facility-ontology-v2"

| Eigenschaft  | type | Erforderlich | BESCHREIBUNG |
|-----------|------|----------|-------------|
|`originalId`        | Zeichenfolge     |true      | Die aus Clientdaten abgeleitete ID. Maximal zulässige Länge: 1.000.|
|`externalId`        | Zeichenfolge     |true      | Eine ID, die vom Client verwendet wird, um das Feature einem anderen Feature in einem anderen Dataset (beispielsweise in einer internen Datenbank) zuzuordnen. Maximal zulässige Länge: 1.000.|
|`categoryId`        |[category.Id](#category)     |true      | Die ID eines Kategoriefeatures.|
| `levelId`          | [level.Id](#level)        | true     | Die ID eines Ebenenfeatures. |
|`anchorPoint` | [Point](/rest/api/maps/wfs/get-feature#featuregeojson) | false | [GeoJSON-Punktgeometrie](/rest/api/maps/wfs/get-feature#featuregeojson), durch die das Feature als Punkt dargestellt wird. Kann verwendet werden, um die Bezeichnung des Features zu positionieren.|

:::zone-end

## <a name="directoryinfo"></a>directoryInfo

Das Objektklassenfeature `directoryInfo` definiert den Namen, die Adresse, die Telefonnummer, die Website und die Geschäftszeiten für eine Einheit, eine Einrichtung oder einen Nutzer einer Einheit oder Einrichtung.

**Geometrietyp:** Keiner

| Eigenschaft  | type | Erforderlich | BESCHREIBUNG |
|-----------|------|----------|-------------|
|`originalId`        | Zeichenfolge     |true      | Die aus Clientdaten abgeleitete ID. Maximal zulässige Länge: 1.000.|
|`externalId`        | Zeichenfolge     |true      | Eine ID, die vom Client verwendet wird, um das Feature einem anderen Feature in einem anderen Dataset (beispielsweise in einer internen Datenbank) zuzuordnen. Maximal zulässige Länge: 1.000.|
|`streetAddress`        |Zeichenfolge    |false    |Straße der Adresse.  Maximal zulässige Länge: 1.000. |
|`unit`        |Zeichenfolge    |false    |Einheitennummer der Adresse.  Maximal zulässige Länge: 1.000. |
|`locality`|    Zeichenfolge|    false    |Örtlichkeit der Adresse (beispielsweise Stadt, Gemeinde oder Ort). Maximal zulässige Länge: 1.000.|
|`adminDivisions`|    Zeichenfolge|    false    |Verwaltungseinheit der Adresse, aufsteigend nach Größe (Landkreis, Bundesland, Land). Beispiel: ["King", "Washington", "USA" ] oder ["West Godavari", "Andhra Pradesh", "IND" ]. Maximal zulässige Länge: 1.000.|
|`postalCode`|    Zeichenfolge |    false    |Postleitzahl der Adresse. Maximal zulässige Länge: 1.000.|
|`name` |    Zeichenfolge |    false |    Name des Features in der Landessprache.  Maximal zulässige Länge: 1.000.|
|`nameSubtitle` |    Zeichenfolge |    false |   Untertitel, der unter dem Namen (`name`) des Features angezeigt wird. Kann beispielsweise verwendet werden, um den Namen in einer anderen Sprache anzuzeigen. Maximal zulässige Länge: 1.000. |
|`nameAlt` |    Zeichenfolge |    false |   Alternativer Name für das Feature. Maximal zulässige Länge: 1.000. |
|`phoneNumber` |    Zeichenfolge |    false |    Telefonnummer Maximal zulässige Länge: 1.000. |
|`website` |    Zeichenfolge |    false |  Website-URL. Maximal zulässige Länge: 1.000. |
|`hoursOfOperation` |    Zeichenfolge |    false |   Geschäftszeiten in Textform gemäß [Open Street Map-Spezifikation](https://wiki.openstreetmap.org/wiki/Key:opening_hours/specification). Maximal zulässige Länge: 1.000. |

## <a name="pointelement"></a>pointElement

`pointElement` ist ein Klassenfeature zum Definieren eines Punktfeatures in einer Einheit – beispielsweise ein Verbandkasten oder ein Sprinklerkopf.

**Geometrietyp:** MultiPoint

| Eigenschaft  | type | Erforderlich | BESCHREIBUNG |
|-----------|------|----------|-------------|
|`originalId`        | Zeichenfolge     |true      | Die aus Clientdaten abgeleitete ID. Maximal zulässige Länge: 1.000.|
|`externalId`        | Zeichenfolge     |true      | Eine ID, die vom Client verwendet wird, um das Feature einem anderen Feature in einem anderen Dataset (beispielsweise in einer internen Datenbank) zuzuordnen. Maximal zulässige Länge: 1.000.|
|`categoryId`        |[category.Id](#category)      |true      | Die ID eines Features vom Typ [`category`](#category).|
| `unitId`          | Zeichenfolge     | true     | Die ID eines Features vom Typ [`unit`](#unit), das dieses Feature enthält.  Maximal zulässige Länge: 1.000.|
| `isObstruction`          | Boolescher Wert (Standardwert: `null`) | false     | Ist diese Option auf `true` festgelegt, stellt das Feature ein Hindernis dar, das bei der Planung der Route durch das enthaltende Einheitenfeature umgangen werden muss. |
|`name` |    Zeichenfolge |    false |    Name des Features in der Landessprache.  Maximal zulässige Länge: 1.000.|
|`nameSubtitle` |    Zeichenfolge |    false |   Untertitel, der unter dem Namen (`name`) des Features angezeigt wird. Kann beispielsweise verwendet werden, um den Namen in einer anderen Sprache anzuzeigen. Maximal zulässige Länge: 1.000. |
|`nameAlt` |    Zeichenfolge |    false |   Alternativer Name für das Feature.  Maximal zulässige Länge: 1.000.|

## <a name="lineelement"></a>lineElement

`lineElement` ist eine Klassenfunktion, die ein Linienfeature in einer Einheit definiert – beispielsweise eine Trennwand oder ein Fenster.

**Geometrietyp:** LinearMultiString

| Eigenschaft  | type | Erforderlich | BESCHREIBUNG |
|-----------|------|----------|-------------|
|`originalId`        | Zeichenfolge     |true      | Die aus Clientdaten abgeleitete ID. Maximal zulässige Länge: 1.000.|
|`externalId`        | Zeichenfolge     |true      | Eine ID, die vom Client verwendet wird, um das Feature einem anderen Feature in einem anderen Dataset (beispielsweise in einer internen Datenbank) zuzuordnen. Maximal zulässige Länge: 1.000.|
|`categoryId`        |[category.Id](#category)      |true      | Die ID eines Features vom Typ [`category`](#category).|
| `unitId`          | Zeichenfolge     | true     | Die ID eines Features vom Typ [`unit`](#unit), das dieses Feature enthält. Maximal zulässige Länge: 1.000. |
| `isObstruction`          | Boolescher Wert (Standardwert: `null`)| false     | Ist diese Option auf `true` festgelegt, stellt das Feature ein Hindernis dar, das bei der Planung der Route durch das enthaltende Einheitenfeature umgangen werden muss. |
|`name` |    Zeichenfolge |    false |    Name des Features in der Landessprache. Maximal zulässige Länge: 1.000. |
|`nameSubtitle` |    Zeichenfolge |    false |   Untertitel, der unter dem Namen (`name`) des Features angezeigt wird. Kann beispielsweise verwendet werden, um den Namen in einer anderen Sprache anzuzeigen. Maximal zulässige Länge: 1.000. |
|`nameAlt` |    Zeichenfolge |    false |   Alternativer Name für das Feature. Maximal zulässige Länge: 1.000. |
|`anchorPoint` |  [Point](/rest/api/maps/wfs/get-feature#featuregeojson) | false | [GeoJSON-Punktgeometrie](/rest/api/maps/wfs/get-feature#featuregeojson), durch die das Feature als Punkt dargestellt wird. Kann verwendet werden, um die Bezeichnung des Features zu positionieren.|
|`obstructionArea` |   [Polygon](/rest/api/maps/wfs/get-feature#featuregeojson)| false | Eine vereinfachte Geometrie des Features, das bei der Routenplanung umgangen werden muss. (Kann im Falle einer komplizierten Liniengeometrie verwendet werden.) Hierfür muss `isObstruction` auf „true“ festgelegt sein.|

## <a name="areaelement"></a>areaElement

`areaElement` ist ein Klassenfeature zum Definieren eines Polygonfeatures in einer Einheit – beispielsweise ein nach unten offener Bereich oder ein Hindernis wie etwa eine Insel in einer Einheit.

**Geometrietyp:** MultiPolygon

| Eigenschaft  | type | Erforderlich | BESCHREIBUNG |
|-----------|------|----------|-------------|
|`originalId`        | Zeichenfolge     |true      | Die aus Clientdaten abgeleitete ID. Maximal zulässige Länge: 1.000.|
|`externalId`        | Zeichenfolge     |true      | Eine ID, die vom Client verwendet wird, um das Feature einem anderen Feature in einem anderen Dataset (beispielsweise in einer internen Datenbank) zuzuordnen. Maximal zulässige Länge: 1.000.|
|`categoryId`        |[category.Id](#category)      |true      | Die ID eines Features vom Typ [`category`](#category).|
| `unitId`          | Zeichenfolge     | true     | Die ID eines Features vom Typ [`unit`](#unit), das dieses Feature enthält. Maximal zulässige Länge: 1.000. |
| `isObstruction`          | boolean | false     | Ist diese Option auf `true` festgelegt, stellt das Feature ein Hindernis dar, das bei der Planung der Route durch das enthaltende Einheitenfeature umgangen werden muss. |
|`obstructionArea` |  Geometrie: ["Polygon","MultiPolygon" ]| false | Eine vereinfachte Geometrie des Features, das bei der Routenplanung umgangen werden muss. (Kann im Falle einer komplizierten Liniengeometrie verwendet werden.) Hierfür muss `isObstruction` auf „true“ festgelegt sein.|
|`name` |    Zeichenfolge |    false |    Name des Features in der Landessprache. Maximal zulässige Länge: 1.000. |
|`nameSubtitle` |    Zeichenfolge |    false |   Untertitel, der unter dem Namen (`name`) des Features angezeigt wird. Kann beispielsweise verwendet werden, um den Namen in einer anderen Sprache anzuzeigen.  Maximal zulässige Länge: 1.000.|
|`nameAlt` |    Zeichenfolge |    false |   Alternativer Name für das Feature.  Maximal zulässige Länge: 1.000.|
|`anchorPoint` |  [Point](/rest/api/maps/wfs/get-feature#featuregeojson) | false | [GeoJSON-Punktgeometrie](/rest/api/maps/wfs/get-feature#featuregeojson), durch die das Feature als Punkt dargestellt wird. Kann verwendet werden, um die Bezeichnung des Features zu positionieren.|

## <a name="category"></a>category

Das Klassenfeature `category` definiert Kategorienamen. Beispiel: "room.conference".

**Geometrietyp:** Keiner

:::zone pivot="facility-ontology-v1"

| Eigenschaft  | type | Erforderlich | BESCHREIBUNG |
|-----------|------|----------|-------------|
|`originalId`        | Zeichenfolge     |true      | Die ursprüngliche, aus Clientdaten abgeleitete ID der Kategorie. Maximal zulässige Länge: 1.000.|
|`externalId`        | Zeichenfolge     |true      | Eine ID, die vom Client verwendet wird, um die Kategorie einer anderen Kategorie in einem anderen Dataset (beispielsweise in einer internen Datenbank) zuzuordnen. Maximal zulässige Länge: 1.000.|
|`name` |    Zeichenfolge |    true |   Name der Kategorie Zur Darstellung der Hierarchie von Kategorien wird die Verwendung von "." empfohlen. Beispiele: "room.conference", "room.privateoffice". Maximal zulässige Länge: 1.000. |
| `routeThroughBehavior` | boolean | false | Bestimmt, ob ein Feature für Durchgangsverkehr verwendet werden kann.|
|`isRoutable`        | Boolescher Wert (Standardwert: `null`)                  | false    |  Bestimmt, ob ein Feature Teil des Routenplanungsgraphen sein soll. Ist diese Einstellung auf `true` festgelegt, kann die Einheit als Ausgangspunkt/Ziel oder Zwischenknoten der Routenplanung verwendet werden. |

:::zone-end

:::zone pivot="facility-ontology-v2"

| Eigenschaft  | type | Erforderlich | BESCHREIBUNG |
|-----------|------|----------|-------------|
|`originalId`        | Zeichenfolge     |true      | Die ursprüngliche, aus Clientdaten abgeleitete ID der Kategorie.  Maximal zulässige Länge: 1.000.|
|`externalId`        | Zeichenfolge     |true      | Eine ID, die vom Client verwendet wird, um die Kategorie einer anderen Kategorie in einem anderen Dataset (beispielsweise in einer internen Datenbank) zuzuordnen. Maximal zulässige Länge: 1.000.|
|`name` |    Zeichenfolge |    true |   Name der Kategorie Zur Darstellung der Hierarchie von Kategorien wird die Verwendung von "." empfohlen. Beispiele: "room.conference", "room.privateoffice". Maximal zulässige Länge: 1.000. |

:::zone-end
