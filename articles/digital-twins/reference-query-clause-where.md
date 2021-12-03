---
title: 'Referenz zur Azure Digital Twins-Abfragesprache: WHERE-Klausel'
titleSuffix: Azure Digital Twins
description: Referenzdokumentation zur WHERE-Klausel der Azure Digital Twins-Abfragesprache
author: baanders
ms.author: baanders
ms.date: 03/31/2021
ms.topic: article
ms.service: digital-twins
ms.openlocfilehash: bf70d1aa4b7a8a3cdfd9d8bcd90a9952e83271bc
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130232828"
---
# <a name="azure-digital-twins-query-language-reference-where-clause"></a>Referenz zur Azure Digital Twins-Abfragesprache: WHERE-Klausel

Dieses Dokument enthält Referenzinformationen zur **WHERE-Klausel** für die [Azure Digital Twins-Abfragesprache](concepts-query-language.md).

Die WHERE-Klausel ist der letzte Teil einer Abfrage. Sie wird verwendet, um die zurückgegebenen Elemente nach bestimmten Bedingungen zu filtern.

Diese Klausel kann bei Abfragen optional genutzt werden.

## <a name="core-syntax-where"></a>Kernsyntax: WHERE

Die WHERE-Klausel wird zusammen mit einer booleschen Bedingung verwendet, um Abfrageergebnisse zu filtern. 

Eine Bedingung kann eine [Funktion](reference-query-functions.md) sein, die zu einem booleschen Ergebnis ausgewertet wird. Sie können auch eine eigene boolesche Anweisung erstellen, indem Sie die Eigenschaften von Zwillingen und Beziehungen (über `.` zugänglich) mit einem Vergleichs- oder „contains-type“-[Operator](reference-query-operators.md) verwenden.

### <a name="syntax"></a>Syntax

Mit Eigenschaften und Operatoren:

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="WhereSyntax":::

Mit einer Funktion:

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="WhereFunctionSyntax":::

### <a name="arguments"></a>Argumente

Eine Bedingung, die zu einem `Boolean`-Wert ausgewertet wird.

### <a name="examples"></a>Beispiele

Im folgenden Beispiel werden Eigenschaften und Operatoren verwendet. Die folgende Abfrage gibt in der WHERE-Klausel an, nur den Zwilling mit dem `$dtId`-Wert „Room1“ zurückzugeben.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="WhereExample":::

Im folgenden Beispiel wird eine Funktion verwendet. Die folgende Abfrage verwendet die Funktion `IS_OF_MODEL`, um in der WHERE-Klausel anzugeben, nur die Zwillinge mit dem Modell `dtmi:sample:Room;1` zurückzugeben. Weitere Informationen zur Funktion `IS_OF_MODEL` finden Sie unter [Referenz zur Azure Digital Twins-Abfragesprache: Funktionen](reference-query-functions.md#is_of_model).

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="WhereFunctionExample":::