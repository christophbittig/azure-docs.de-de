---
title: Bicep-Funktionen
description: Es werden die Funktionen beschrieben, die in einer Bicep-Datei zum Abrufen von Werten, Arbeiten mit Zeichenfolgen und numerischen Werten sowie Abrufen von Bereitstellungsinformationen verwendet werden.
ms.topic: conceptual
ms.date: 09/09/2021
ms.openlocfilehash: bd697d6c1a1eaac7877341ffbbc10b1027882dd6
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124794024"
---
# <a name="bicep-functions"></a>Bicep-Funktionen

In diesem Artikel werden alle Funktionen beschrieben, die Sie in einer Bicep-Datei verwenden können. Eine Beschreibung der Abschnitte in einer Bicep-Datei finden Sie unter [Grundlegendes zur Struktur und Syntax von Bicep-Dateien](./file.md).

Die meisten Funktionen funktionieren auf die gleiche Weise, wenn sie in einer Ressourcengruppe, einem Abonnement, einer Verwaltungsgruppe oder einem Mandanten bereitgestellt werden. Einige Funktionen können nicht in allen Bereichen verwendet werden. Diese sind in den folgenden Listen aufgeführt.

## <a name="any-function"></a>Beliebige Funktion

Die [any-Funktion](./bicep-functions-any.md) wird in Bicep zur Unterstützung bei der Lösung von Problemen in Zusammenhang mit Datentypwarnungen bereitgestellt.

## <a name="array-functions"></a>Arrayfunktionen

Die folgenden Funktionen sind für die Arbeit mit Arrays verfügbar.

* [array](./bicep-functions-array.md#array)
* [concat](./bicep-functions-array.md#concat)
* [contains](./bicep-functions-array.md#contains)
* [empty](./bicep-functions-array.md#empty)
* [first](./bicep-functions-array.md#first)
* [intersection](./bicep-functions-array.md#intersection)
* [last](./bicep-functions-array.md#last)
* [length](./bicep-functions-array.md#length)
* [min](./bicep-functions-array.md#min)
* [max](./bicep-functions-array.md#max)
* [range](./bicep-functions-array.md#range)
* [skip](./bicep-functions-array.md#skip)
* [take](./bicep-functions-array.md#take)
* [union](./bicep-functions-array.md#union)

## <a name="date-functions"></a>Datumsfunktionen

Die folgenden Funktionen sind für die Arbeit mit Datumsangaben verfügbar.

* [dateTimeAdd](./bicep-functions-date.md#datetimeadd)
* [utcNow](./bicep-functions-date.md#utcnow)

## <a name="deployment-value-functions"></a>Funktionen für Bereitstellungswerte

Die folgenden Funktionen sind zum Abrufen von Werten im Zusammenhang mit der Bereitstellung zur verfügbar:

* [deployment](./bicep-functions-deployment.md#deployment)
* [Umgebung](./bicep-functions-deployment.md#environment)

## <a name="file-functions"></a>Dateifunktionen

Die folgenden Funktionen stehen zum Laden des Inhalts aus externen Dateien in Ihre Bicep-Datei zur Verfügung.

* [loadFileAsBase64](bicep-functions-files.md#loadfileasbase64)
* [loadTextContent](bicep-functions-files.md#loadtextcontent)

## <a name="logical-functions"></a>Logische Funktionen

Die folgende Funktion ist für die Arbeit mit logischen Bedingungen verfügbar:

* [bool](./bicep-functions-logical.md#bool)

## <a name="numeric-functions"></a>Numerische Funktionen

Die folgenden Funktionen sind für die Arbeit mit ganzen Zahlen verfügbar:

* [int](./bicep-functions-numeric.md#int)
* [min](./bicep-functions-numeric.md#min)
* [max](./bicep-functions-numeric.md#max)

## <a name="object-functions"></a>Objektfunktionen

Die folgenden Funktionen sind für die Arbeit mit Objekten verfügbar.

* [contains](./bicep-functions-object.md#contains)
* [empty](./bicep-functions-object.md#empty)
* [intersection](./bicep-functions-object.md#intersection)
* [json](./bicep-functions-object.md#json)
* [length](./bicep-functions-object.md#length)
* [union](./bicep-functions-object.md#union)

## <a name="resource-functions"></a>Ressourcenfunktionen

Die folgenden Funktionen sind zum Abrufen von Ressourcenwerten verfügbar:

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

Die folgenden Funktionen sind zum Abrufen von Bereichswerten verfügbar.

* [managementGroup](./bicep-functions-scope.md#managementgroup)
* [resourceGroup](./bicep-functions-scope.md#resourcegroup): Kann nur in Bereitstellungen in einer Ressourcengruppe verwendet werden.
* [subscription](./bicep-functions-scope.md#subscription): Kann nur in Bereitstellungen in einer Ressourcengruppe oder einem Abonnement verwendet werden.
* [tenant](./bicep-functions-scope.md#tenant)

## <a name="string-functions"></a>Zeichenfolgenfunktionen

Bicep stellt die folgenden Funktionen für das Arbeiten mit Zeichenfolgen bereit:

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
* Informationen dazu, wie Sie beim Erstellen eines Ressourcentyps eine bestimmte Anzahl von Durchläufen ausführen, finden Sie unter [Bereitstellen mehrerer Instanzen von Ressourcen in Bicep](./loop-resources.md).
* Informationen zum Bereitstellen der von Ihnen erstellten Bicep-Datei finden Sie unter [Bereitstellen von Ressourcen mit Bicep und Azure PowerShell](./deploy-powershell.md).
