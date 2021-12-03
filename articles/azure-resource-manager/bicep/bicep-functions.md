---
title: Bicep-Funktionen
description: Es werden die Funktionen beschrieben, die in einer Bicep-Datei zum Abrufen von Werten, Arbeiten mit Zeichenfolgen und numerischen Werten sowie Abrufen von Bereitstellungsinformationen verwendet werden.
ms.topic: conceptual
ms.date: 10/15/2021
ms.openlocfilehash: 4a4f057fe7c29b3ee9b76aa03992ff1efd70079c
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130226908"
---
# <a name="bicep-functions"></a>Bicep-Funktionen

In diesem Artikel werden alle Funktionen beschrieben, die Sie in einer Bicep-Datei verwenden können. Eine Beschreibung der Abschnitte in einer Bicep-Datei finden Sie unter [Grundlegendes zur Struktur und Syntax von Bicep-Dateien](./file.md).

Die meisten Funktionen funktionieren auf die gleiche Weise, wenn sie in einer Ressourcengruppe, einem Abonnement, einer Verwaltungsgruppe oder einem Mandanten bereitgestellt werden. Einige Funktionen können nicht in allen Bereichen verwendet werden. Diese sind in den folgenden Listen aufgeführt.

## <a name="namespaces-for-functions"></a>Namespaces für Funktionen

Alle Bicep-Funktionen sind in zwei Namespaces enthalten: `az` und `sys`. In der Regel müssen Sie den Namespace nicht angeben, wenn Sie die Funktion verwenden. Sie geben den Namespace nur an, wenn der Funktionsname mit einem anderen Element identisch ist, das Sie in der Bicep-Datei definiert haben. Wenn Sie beispielsweise einen Parameter mit dem Namen `range` erstellen, müssen Sie die Funktion `range` unterscheiden, indem Sie den Namespace `sys` hinzufügen.

```bicep
// Parameter contains the same name as a function
param range int

// Must use sys namespace to call the function. 
// The second use of range refers to the parameter.
output result array = sys.range(1, range)
```

Der `az`-Namespace enthält Funktionen, die für eine Azure-Bereitstellung spezifisch sind. Der `sys`-Namespace enthält Funktionen, die zum Erstellen von Werten genutzt werden. Der `sys`-Namespace enthält auch Decorators für Parameter und Ressourcenschleifen. Die Namespaces sind in diesem Artikel beschrieben.

## <a name="any-function"></a>Beliebige Funktion

Die [any-Funktion](./bicep-functions-any.md) wird in Bicep zur Unterstützung bei der Lösung von Problemen in Zusammenhang mit Datentypwarnungen bereitgestellt. Diese Funktion wird im `sys`-Namespace definiert.

## <a name="array-functions"></a>Arrayfunktionen

Die folgenden Funktionen sind für die Arbeit mit Arrays verfügbar. All diese Funktion werden im `sys`-Namespace definiert.

* [array](./bicep-functions-array.md#array)
* [concat](./bicep-functions-array.md#concat)
* [contains](./bicep-functions-array.md#contains)
* [empty](./bicep-functions-array.md#empty)
* [first](./bicep-functions-array.md#first)
* [intersection](./bicep-functions-array.md#intersection)
* [items](./bicep-functions-array.md#items)
* [last](./bicep-functions-array.md#last)
* [length](./bicep-functions-array.md#length)
* [min](./bicep-functions-array.md#min)
* [max](./bicep-functions-array.md#max)
* [range](./bicep-functions-array.md#range)
* [skip](./bicep-functions-array.md#skip)
* [take](./bicep-functions-array.md#take)
* [union](./bicep-functions-array.md#union)

## <a name="date-functions"></a>Datumsfunktionen

Die folgenden Funktionen sind für die Arbeit mit Datumsangaben verfügbar. All diese Funktion werden im `sys`-Namespace definiert.

* [dateTimeAdd](./bicep-functions-date.md#datetimeadd)
* [utcNow](./bicep-functions-date.md#utcnow)

## <a name="deployment-value-functions"></a>Funktionen für Bereitstellungswerte

Die folgenden Funktionen sind zum Abrufen von Werten im Zusammenhang mit der Bereitstellung zur verfügbar: All diese Funktion werden im `az`-Namespace definiert.

* [deployment](./bicep-functions-deployment.md#deployment)
* [Umgebung](./bicep-functions-deployment.md#environment)

## <a name="file-functions"></a>Dateifunktionen

Die folgenden Funktionen stehen zum Laden des Inhalts aus externen Dateien in Ihre Bicep-Datei zur Verfügung. All diese Funktion werden im `sys`-Namespace definiert.

* [loadFileAsBase64](bicep-functions-files.md#loadfileasbase64)
* [loadTextContent](bicep-functions-files.md#loadtextcontent)

## <a name="logical-functions"></a>Logische Funktionen

Die folgende Funktion ist für die Arbeit mit logischen Bedingungen verfügbar: Diese Funktion wird im `sys`-Namespace definiert.

* [bool](./bicep-functions-logical.md#bool)

## <a name="numeric-functions"></a>Numerische Funktionen

Die folgenden Funktionen sind für die Arbeit mit ganzen Zahlen verfügbar. All diese Funktion werden im `sys`-Namespace definiert.

* [int](./bicep-functions-numeric.md#int)
* [min](./bicep-functions-numeric.md#min)
* [max](./bicep-functions-numeric.md#max)

## <a name="object-functions"></a>Objektfunktionen

Die folgenden Funktionen sind für die Arbeit mit Objekten verfügbar. All diese Funktion werden im `sys`-Namespace definiert.

* [contains](./bicep-functions-object.md#contains)
* [empty](./bicep-functions-object.md#empty)
* [intersection](./bicep-functions-object.md#intersection)
* [json](./bicep-functions-object.md#json)
* [length](./bicep-functions-object.md#length)
* [union](./bicep-functions-object.md#union)

## <a name="resource-functions"></a>Ressourcenfunktionen

Die folgenden Funktionen sind zum Abrufen von Ressourcenwerten verfügbar. Die meisten dieser Funktion werden im `az`-Namespace definiert. Die List-Funktionen und die getSecret-Funktion werden direkt für den Ressourcentyp aufgerufen, sodass sie keinen Namespacequalifizierer haben.

* [extensionResourceId](./bicep-functions-resource.md#extensionresourceid)
* [getSecret](./bicep-functions-resource.md#getsecret)
* [listAccountSas](./bicep-functions-resource.md#list)
* [listKeys](./bicep-functions-resource.md#listkeys)
* [listSecrets](./bicep-functions-resource.md#list)
* [list*](./bicep-functions-resource.md#list)
* [pickZones](./bicep-functions-resource.md#pickzones)
* [providers (veraltet)](./bicep-functions-resource.md#providers)
* [Referenz](./bicep-functions-resource.md#reference)
* [resourceId](./bicep-functions-resource.md#resourceid): Kann in jedem Bereich verwendet werden, aber die gültigen Parameter ändern sich je nach Bereich.
* [subscriptionResourceId](./bicep-functions-resource.md#subscriptionresourceid)
* [tenantResourceId](./bicep-functions-resource.md#tenantresourceid)

## <a name="scope-functions"></a>Bereichsfunktionen

Die folgenden Funktionen sind zum Abrufen von Bereichswerten verfügbar. All diese Funktion werden im `az`-Namespace definiert.

* [managementGroup](./bicep-functions-scope.md#managementgroup)
* [resourceGroup](./bicep-functions-scope.md#resourcegroup): Kann nur in Bereitstellungen in einer Ressourcengruppe verwendet werden.
* [subscription](./bicep-functions-scope.md#subscription): Kann nur in Bereitstellungen in einer Ressourcengruppe oder einem Abonnement verwendet werden.
* [tenant](./bicep-functions-scope.md#tenant)

## <a name="string-functions"></a>Zeichenfolgenfunktionen

Bicep stellt die folgenden Funktionen für das Arbeiten mit Zeichenfolgen bereit. All diese Funktion werden im `sys`-Namespace definiert.

* [base64](./bicep-functions-string.md#base64)
* [base64ToJson](./bicep-functions-string.md#base64tojson)
* [base64ToString](./bicep-functions-string.md#base64tostring)
* [concat](./bicep-functions-string.md#concat)
* [contains](./bicep-functions-string.md#contains)
* [dataUri](./bicep-functions-string.md#datauri)
* [dataUriToString](./bicep-functions-string.md#datauritostring)
* [empty](./bicep-functions-string.md#empty)
* [endsWith](./bicep-functions-string.md#endswith)
* [first](./bicep-functions-string.md#first)
* [format](./bicep-functions-string.md#format)
* [guid](./bicep-functions-string.md#guid)
* [indexOf](./bicep-functions-string.md#indexof)
* [last](./bicep-functions-string.md#last)
* [lastIndexOf](./bicep-functions-string.md#lastindexof)
* [length](./bicep-functions-string.md#length)
* [newGuid](./bicep-functions-string.md#newguid)
* [padLeft](./bicep-functions-string.md#padleft)
* [replace](./bicep-functions-string.md#replace)
* [skip](./bicep-functions-string.md#skip)
* [split](./bicep-functions-string.md#split)
* [startsWith](./bicep-functions-string.md#startswith)
* [string](./bicep-functions-string.md#string)
* [substring](./bicep-functions-string.md#substring)
* [take](./bicep-functions-string.md#take)
* [toLower](./bicep-functions-string.md#tolower)
* [toUpper](./bicep-functions-string.md#toupper)
* [trim](./bicep-functions-string.md#trim)
* [uniqueString](./bicep-functions-string.md#uniquestring)
* [uri](./bicep-functions-string.md#uri)
* [uriComponent](./bicep-functions-string.md#uricomponent)
* [uriComponentToString](./bicep-functions-string.md#uricomponenttostring)

## <a name="next-steps"></a>Nächste Schritte

* Eine Beschreibung der Abschnitte in einer Bicep-Datei finden Sie unter [Grundlegendes zur Struktur und Syntax von Bicep-Dateien](./file.md).
* Wenn Sie beim Erstellen eines Ressourcentyps eine angegebene Anzahl von Wiederholungen durchlaufen möchten, finden Sie weitere Informationen unter [Iterative Schleifen in Bicep](loops.md).
* Informationen zum Bereitstellen der von Ihnen erstellten Bicep-Datei finden Sie unter [Bereitstellen von Ressourcen mit Bicep und Azure PowerShell](./deploy-powershell.md).
