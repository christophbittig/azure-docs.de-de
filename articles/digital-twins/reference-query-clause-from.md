---
title: 'Referenz zur Azure Digital Twins-Abfragesprache: FROM-Klausel'
titleSuffix: Azure Digital Twins
description: Referenzdokumentation für die FROM-Klausel der Azure Digital Twins-Abfragesprache
author: baanders
ms.author: baanders
ms.date: 03/31/2021
ms.topic: article
ms.service: digital-twins
ms.openlocfilehash: 3bbd9fb23d970173e486cc721e0e2573603287a8
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130232847"
---
# <a name="azure-digital-twins-query-language-reference-from-clause"></a>Referenz zur Azure Digital Twins-Abfragesprache: FROM-Klausel

Dieses Dokument enthält Referenzinformationen zur **FROM-Klausel** für die [Azure Digital Twins-Abfragesprache](concepts-query-language.md).

Die FROM-Klausel ist der zweite Teil einer Abfrage. Sie gibt die Sammlung und alle Joins an, die die Abfrage berücksichtigt.

Diese Klausel ist für alle Abfragen erforderlich.

## <a name="select--from-digitaltwins"></a>SELECT ... FROM DIGITALTWINS

Verwenden Sie `FROM DIGITALTWINS` (keine Unterscheidung von Groß-/Kleinschreibung), um auf die gesamte Sammlung von digitalen Zwillingen in einer Instanz zu verweisen.

Optional können Sie der Sammlung digitaler Zwillinge einen Namen hinzufügen, indem Sie den Namen am Ende der Anweisung hinzufügen.

### <a name="syntax"></a>Syntax

Basic:

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="FromDigitalTwinsSyntax":::

So benennen Sie die Sammlung:

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="FromDigitalTwinsNamedSyntax":::

### <a name="examples"></a>Beispiele

Dies ist eine einfache Abfrage. Die folgende Abfrage gibt alle digitalen Zwillinge in der Instanz zurück. 

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="FromDigitalTwinsExample":::

Dies ist eine Abfrage mit einer benannten Sammlung. Die folgende Abfrage weist der Sammlung einen Namen `T` zu und gibt weiterhin alle digitalen Zwillinge in der Instanz zurück.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="FromDigitalTwinsNamedExample":::

## <a name="select--from-relationships"></a>SELECT ... FROM RELATIONSHIPS

Verwenden Sie `FROM RELATIONSHIPS` (keine Unterscheidung von Groß-/Kleinschreibung), um auf die gesamte Sammlung von Beziehungen in einer Instanz zu verweisen.

Optional können Sie der Sammlung von Beziehungen einen Namen hinzufügen, indem Sie den Namen am Ende der Anweisung hinzufügen.

>[!NOTE]
> Diese Funktion kann nicht mit `JOIN` kombiniert werden.

### <a name="syntax"></a>Syntax

Basic:

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="FromRelationshipsSyntax":::

So benennen Sie die Sammlung:

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="FromRelationshipsNamedSyntax":::

### <a name="examples"></a>Beispiele

Dies ist eine Abfrage, die alle Beziehungen in der Instanz zurückgibt. 

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="FromRelationshipsExample":::

Dies ist eine Abfrage zur Rückgabe aller Beziehungen, die von den Zwillingen `A`, `B`, `C` oder `D` stammen.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="FromRelationshipsFilteredExample":::

## <a name="using-from-and-join-together"></a>Gemeinsames Verwenden von FROM und JOIN

Die `FROM`-Klausel kann mit der `JOIN`-Klausel kombiniert werden, um entitätsübergreifende Traversale im Azure Digital Twins-Graphen auszudrücken.

Weitere Informationen zur `JOIN` -Klausel und zum Erstellen von Graphentraversalenabfragen finden Sie unter [Referenz zur Azure Digital Twins-Abfragesprache: JOIN-Klausel.](reference-query-clause-join.md)

## <a name="limitations"></a>Einschränkungen

Die folgenden Grenzwerte gelten für Abfragen mit `FROM`.
* [Keine Unterabfragen](#no-subqueries)
* [Auswählen zwischen FROM RELATIONSHIPS und JOIN](#choose-from-relationships-or-join)

Weitere Informationen finden Sie in den folgenden Abschnitten.

### <a name="no-subqueries"></a>Keine Unterabfragen

In der `FROM`-Anweisung werden keine Unterabfragen unterstützt.

#### <a name="example-negative"></a>Beispiel (negativ)

Die folgende Abfrage zeigt ein Beispiel dafür, was gemäß dieser Einschränkung **nicht** möglich ist.

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="FromNegativeExample":::

### <a name="choose-from-relationships-or-join"></a>Auswählen zwischen FROM RELATIONSHIPS und JOIN

Die `FROM RELATIONSHIPS`-Funktion kann nicht mit `JOIN` kombiniert werden. Sie müssen auswählen, welche dieser Optionen für die auszuwählenden Informationen am besten geeignet ist.


