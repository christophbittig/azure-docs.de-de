---
title: 'Referenz zur Azure Digital Twins-Abfragesprache: Reserviertes Schlüsselwort'
titleSuffix: Azure Digital Twins
description: 'Referenzdokumentation: Reserviertes Schlüsselwort der Azure Digital Twins-Abfragesprache'
author: baanders
ms.author: baanders
ms.date: 9/1/2021
ms.topic: article
ms.service: digital-twins
ms.openlocfilehash: 6ffba46cacf452a4769709b115177bab41d5eca7
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124838162"
---
# <a name="azure-digital-twins-query-language-reference-reserved-keywords"></a>Referenz zur Azure Digital Twins-Abfragesprache: Reserviertes Schlüsselwort

Dieses Dokument enthält die Liste der **reservierten Schlüsselwörter** in der [Azure Digital Twins Abfragesprache](concepts-query-language.md). Diese Wörter können nicht als Bezeichner in Abfragen verwendet werden, es sei denn, sie werden [in doppelten eckigen Klammern mit Escapezeichen versehen](#escaping-reserved-keywords-in-queries). 

## <a name="list-of-reserved-keywords"></a>Liste der reservierten Schlüsselwörter

Im Folgenden sehen Sie die reservierten Schlüsselwörter in Azure Digital Twins-Abfragesprache:

* ALL 
* AND
* AS
* ASC
* DURCHSCHN.
* BY
* COUNT
* DESC
* DEVICES_JOBS
* DEVICES_MODULES
* GERÄTE
* ENDS_WITH
* FALSE
* FROM
* GROUP
* IN
* IS_BOOL
* IS_DEFINED
* IS_NULL
* IS_NUMBER
* IS_OBJECT
* IS_PRIMITIVE
* IS_STRING
* MAX
* MIN
* NICHT
* NOT_IN
* NULL
* oder
* ORDER
* SELECT
* STARTS_WITH
* SUM
* TOP
* TRUE
* WHERE
* IS_OF_MODEL

## <a name="escaping-reserved-keywords-in-queries"></a>Escaping von reservierten Schlüsselwörtern in Abfragen

Um ein reserviertes Schlüsselwort als Bezeichner in einer Abfrage zu verwenden, schließen Sie das Schlüsselwort wie folgt in doppelte eckige Klammern ein: `[[<keyword>]]`

Betrachten Sie beispielsweise eine Gruppe von digitalen Zwillingen mit einer Eigenschaft namens `GROUP`, die ein reserviertes Schlüsselwort ist. Um nach diesem Eigenschaftswert zu filtern, muss der Eigenschaftsname an den Stellen, an denen er in der Abfrage verwendet wird, wie unten gezeigt, mit einem Escapezeichen versehen werden:

:::code language="sql" source="~/digital-twins-docs-samples/queries/reference.sql" id="ReservedKeywordExample":::