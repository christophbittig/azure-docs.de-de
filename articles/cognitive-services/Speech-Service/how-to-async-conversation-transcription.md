---
title: Asynchrone Unterhaltungstranskription – Speech-Dienst
titleSuffix: Azure Cognitive Services
description: Hier erfahren Sie, wie Sie die asynchrone Unterhaltungstranskription mit dem Speech-Dienst verwenden. Nur für Java und C# verfügbar.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: devx-track-csharp
zone_pivot_groups: programming-languages-set-twenty-one
ms.openlocfilehash: 9dbee96d6b277ac76b07e3fd8b2b72b1c84b32f9
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132546403"
---
# <a name="asynchronous-conversation-transcription"></a>Asynchrone Unterhaltungstranskription

In diesem Artikel wird die asynchrone Unterhaltungstranskription mithilfe der **RemoteConversationTranscriptionClient**-API veranschaulicht. Wenn Sie die Unterhaltungstranskription für die asynchrone Transkription konfiguriert haben und über eine `conversationId` verfügen, können Sie die mit dieser `conversationId` verbundene Transkription mit der **RemoteConversationTranscriptionClient**-API abrufen.

## <a name="asynchronous-vs-real-time--asynchronous"></a>Asynchron im Vergleich zu Echtzeit + Asynchron

Bei der asynchronen Transkription streamen Sie die Audiodaten der Unterhaltung, benötigen aber keine Transkription, die in Echtzeit zurückgegeben wird. Verwenden Sie stattdessen nach dem Senden der Audiodaten die `conversationId` von `Conversation`, um den Status der asynchronen Transkription abzufragen. Wenn die asynchrone Transkription bereit ist, erhalten Sie ein `RemoteConversationTranscriptionResult`.

Bei „Echtzeit plus asynchron“ erhalten Sie die Transkription in Echtzeit, aber auch die Transkription durch Abfragen mit dem `conversationId` (ähnlich dem asynchronen Szenario).

Es sind zwei Schritte erforderlich, um eine asynchrone Transkription durchzuführen. Der erste Schritt ist das Hochladen der Audiodaten, wobei Sie entweder nur „Asynchron“ oder „Echtzeit plus asynchron“ auswählen. Der zweite Schritt besteht darin, die Ergebnisse der Transkription abzurufen.

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/how-to/remote-conversation/csharp/examples.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [prerequisites](includes/how-to/remote-conversation/java/examples.md)]
::: zone-end


## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erkunden unserer Beispiele auf GitHub](https://aka.ms/csspeech/samples)
