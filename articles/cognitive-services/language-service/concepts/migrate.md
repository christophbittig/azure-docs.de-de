---
title: Migrieren zu Azure Cognitive Services for Language von LUIS, QnA Maker und Textanalyse
titleSuffix: Azure Cognitive Services
description: In diesem Artikel erhalten Sie Informationen, wenn Sie Ihre Anwendungen von LUIS, QnA Maker und Textanalyse migrieren müssen.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: ignite-fall-2021
ms.openlocfilehash: 1e2a2945700e0537b7ae4b220f97eafb1ce19a69
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131021582"
---
# <a name="migrating-to-azure-cognitive-service-for-language"></a>Migrieren zu Azure Cognitive Services for Language

Am 2. November 2021 wurde Azure Cognitive Services for Language als öffentliche Vorschauversion veröffentlicht. Dieser Sprachdienst vereinheitlicht die Dienstangebote für Textanalyse, QnA Maker und LUIS und bietet zudem mehrere neue Features. 

## <a name="do-i-need-to-migrate-to-the-language-service-if-i-am-using-text-analytics"></a>Muss ich zum Sprachdienst migrieren, wenn ich die Textanalyse verwende?

Die Textanalyse wurde in den Sprachdienst integriert, und ihre Features sind weiterhin verfügbar. Wenn Sie die Textanalyse verwendet haben, sollten Ihre Anwendungen weiterhin ohne Breaking Changes funktionieren. Sie können auch die Informationen im [Migrationsleitfaden für Textanalyse v2](migrate-from-text-analytics-v2.md) lesen, wenn Sie eine ältere Anwendung aktualisieren müssen. 

Verwenden Sie ggf. einen der verfügbaren Schnellstartartikel, um die neuesten Informationen zu Dienstendpunkten und API-Aufrufen einzusehen. 

## <a name="how-do-i-migrate-to-the-language-service-if-i-am-using-luis"></a>Wie migriere ich bei Verwendung von LUIS zum Sprachdienst?

Wenn Sie Language Understanding (LUIS) verwenden, können Sie [Ihre LUIS-JSON-Datei](../conversational-language-understanding/concepts/backwards-compatibility.md) in das neue Feature für Unterhaltungssprachverständnis importieren. 

## <a name="how-do-i-migrate-to-the-language-service-if-i-am-using-qna-maker"></a>Wie migriere ich bei Verwendung von QnA Maker zum Sprachdienst?

Wenn Sie QnA Maker verwenden, finden Sie im [Migrationsleitfaden](../question-answering/how-to/migrate-qnamaker.md) Informationen zum Migrieren von Wissensdatenbanken von QnA Maker zu Fragen und Antworten.

## <a name="see-also"></a>Weitere Informationen

* [Azure Cognitive Services for Language](../overview.md)
* [Übersicht über das Conversational Language Understanding](../conversational-language-understanding/overview.md)
* [Übersicht über Fragen und Antworten](../question-answering/overview.md)
