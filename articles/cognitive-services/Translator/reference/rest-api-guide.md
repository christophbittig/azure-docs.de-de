---
title: Referenzleitfaden zur REST-API für die Textübersetzung
titleSuffix: Azure Cognitive Services
description: Rufen Sie eine Liste mit Links zu den REST-APIs für die Textübersetzung auf.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 08/10/2021
ms.author: lajanuar
ms.openlocfilehash: f3188f303d7e2945341267c9ea4598682e4d7964
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2021
ms.locfileid: "123426245"
---
# <a name="text-translation-rest-api"></a>REST-API für Textübersetzung

Die Textübersetzung ist ein cloudbasiertes Feature des Azure-Übersetzerdiensts und ist Teil der Azure Cognitive Service-Familie von REST-APIs. Die Textübersetzungs-API übersetzt Text zwischen Sprachpaaren in allen [unterstützten Sprachen und Dialekten](../../language-support.md). Die verfügbaren Methoden werden in der folgenden Tabelle aufgeführt:

| Anforderung| Methode| BESCHREIBUNG|
|---------|--------------|---------|
| [**Sprachen**](v3-0-languages.md) | **GET** | Gibt die Sprachen zurück, die derzeit von den Methoden **Übersetzung**, **Transliteration** und **Wörterbuch** unterstützt werden. Diese Anforderung erfordert keine Authentifizierungsheader, und Sie benötigen keine Textübersetzungsressource, um den unterstützten Sprachsatz anzeigen zu können.|
|[**translate**](v3-0-translate.md) | **POST**| Übersetzen Sie den angegebenen Quellsprachtext in den Zielsprachtext.|
|[**transliterate**](v3-0-transliterate.md) |  **POST** | Ordnen Sie das Skript oder Alphabet der Quellsprache einem Zielsprachenskript oder -alphabet zu.
|[**detect**](v3-0-detect.md) | **POST** | Identifizieren Sie die Quellsprache. |
|[**breakSentence**](v3-0-break-sentence.md) | **POST** | Gibt ein Array von ganzen Zahlen zurück, das die Länge von Sätzen in einem Quelltext darstellt. |
| [**dictionary/lookup**](v3-0-dictionary-lookup.md) | **POST** | Gibt Alternativen für Einzelwortübersetzungen zurück. |
| [**dictionary/examples**](v3-0-dictionary-lookup.md) | **POST** | Gibt zurück, wie ein Begriff im Kontext verwendet wird. |

> [!div class="nextstepaction"]
> [Erstellen Sie Übersetzerressource im Azure-Portal.](../translator-how-to-signup.md)

> [!div class="nextstepaction"]
> [Schnellstart: REST-API und Ihre Programmiersprache](../quickstart-translator.md)
