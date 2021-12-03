---
title: Abfragesprache
titleSuffix: Azure Digital Twins
description: Erfahren Sie mehr über die Azure Digital Twins-Abfragesprache.
author: baanders
ms.author: baanders
ms.date: 10/27/2021
ms.topic: conceptual
ms.service: digital-twins
ms.custom: contperf-fy21q2
ms.openlocfilehash: 620411b5fc8c657f837f3c27077d4ae3dbf16f1c
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131509691"
---
# <a name="azure-digital-twins-query-language"></a>Azure Digital Twins-Abfragesprache

In diesem Artikel werden die Grundlagen der Abfragesprache und ihrer Funktionen beschrieben. Beachten Sie, dass im Mittelpunkt von Azure Digital Twins der [Zwillingsgraph](concepts-twins-graph.md) steht, der sich aus digitalen Zwillingen und Beziehungen zusammensetzt. Dieser Graph kann abgefragt werden, um Informationen zu den darin enthaltenen digitalen Zwillingen und Beziehungen abzurufen. Diese Abfragen werden in einer benutzerdefinierten SQL-ähnlichen Abfragesprache mit der Bezeichnung **Azure Digital Twins-Abfragesprache** geschrieben. Diese Sprache ähnelt der [IoT Hub-Abfragesprache](../iot-hub/iot-hub-devguide-query-language.md) mit vielen vergleichbaren Features.

Ausführlichere Beispiele für die Abfragesyntax und zum Ausführen von Abfrageanforderungen finden Sie unter [Abfragen des Zwillingsgraphen](how-to-query-graph.md).

## <a name="about-the-queries"></a>Informationen zu Abfragen

Sie können die Abfragesprache von Azure Digital Twins verwenden, um digitale Zwillinge gemäß folgender Aspekte abzurufen:
* Eigenschaften (einschließlich [Tageigenschaften](how-to-use-tags.md))
* Modelle
* Beziehungen
  - Eigenschaften der Beziehungen

Wenn Sie eine Abfrage aus einer Client-App an den Dienst senden möchten, verwenden Sie die Azure Digital Twins-[Abfrage-API](/rest/api/digital-twins/dataplane/query). Eine Möglichkeit, die API zu verwenden, ist durch eins der [SDKs für Azure Digital Twins](concepts-apis-sdks.md#overview-data-plane-apis).

[!INCLUDE [digital-twins-query-reference.md](../../includes/digital-twins-query-reference.md)]

## <a name="considerations-for-querying"></a>Hinweise zur Abfrage

Beachten Sie beim Schreiben von Abfragen für Azure Digital Twins Folgendes:
* **Denken Sie an die Beachtung der Groß-/Kleinschreibung:** Bei allen Azure Digital Twins-Abfragevorgängen wird die Groß-/Kleinschreibung beachtet. Verwenden Sie daher genau die in den Modellen definierten Namen. Wenn Eigenschaftennamen falsch oder in falscher Groß-/Kleinschreibung geschrieben werden, ist das Resultset leer, und es werden keine Fehler zurückgegeben.
* **Versehen Sie einfache Anführungszeichen mit Escapezeichen:** Wenn die Daten Ihres Abfragetexts ein einfaches Anführungszeichen enthalten, muss dieses mit dem Escapezeichen `\` versehen werden. Im folgenden Beispiel wird der Eigenschaftswert *D'Souza* verwendet:

  :::code language="sql" source="~/digital-twins-docs-samples/queries/examples.sql" id="EscapedSingleQuote":::

[!INCLUDE [digital-twins-query-latency-note.md](../../includes/digital-twins-query-latency-note.md)]

## <a name="next-steps"></a>Nächste Schritte

Unter [Abfragen des Zwillingsgraphen](how-to-query-graph.md) finden Sie Information zum Verfassen von Abfragen und Clientcodebeispiele.