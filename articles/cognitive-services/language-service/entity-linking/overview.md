---
title: Was ist Entitätsverknüpfung in Azure Cognitive Services für Sprache?
titleSuffix: Azure Cognitive Services
description: Eine Übersicht der Entitätsverknüpfung in Azure Cognitive Services, die Ihnen hilft, Entitäten aus Text zu extrahieren, und Links zu einer Online-Wissensdatenbank enthält.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-entity-linking, ignite-fall-2021
ms.openlocfilehash: bbd4483702055a4bebb05251606fb705b1e0deb2
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132487452"
---
# <a name="what-is-entity-linking-in-azure-cognitive-service-for-language"></a>Was ist Entitätsverknüpfung in Azure Cognitive Services für Sprache?

Entitätsverknüpfung ist ein Feature von [Azure Cognitive Service für Sprache](../overview.md). Dabei handelt es sich um eine Sammlung von Machine Learning- und KI-Algorithmen in der Cloud für die Entwicklung intelligenter Anwendungen, die geschriebene Sprache umfassen. Durch die Entitätsverknüpfung wird die Identität von Entitäten im Text erkannt und eindeutig bestimmt. Im Satz „We *went to Seattle last week.* “ (Wir sind letzte Woche nach Seattle gefahren.) würde beispielsweise das Wort „*Seattle*“ erkannt und ein Link zu weiteren Informationen auf Wikipedia angegeben.

Diese Dokumentation enthält die folgenden Arten von Artikeln:

* [**Schnellstarts**](quickstart.md) sind Anleitungen zu den ersten Schritten, die Sie durch das Senden von Anforderungen an den Dienst führen.
* [**Anleitungen**](how-to/call-api.md) enthalten Anweisungen zur spezifischeren Verwendung des Diensts.

[!INCLUDE [Typical workflow for pre-configured language features](../includes/overview-typical-workflow.md)]

Das Ergebnis ist eine Sammlung von erkannten Entitäten in Ihrem Text mit URLs zu Wikipedia als Online-Wissensdatenbank. 

## <a name="responsible-ai"></a>Verantwortungsbewusste künstliche Intelligenz 

Ein KI-System umfasst nicht nur die Technologie, sondern auch die Personen, die das System verwenden, die davon betroffenen Personen, und die Umgebung, in der es bereitgestellt wird. Weitere Informationen zur verantwortungsbewussten Verwendung und Bereitstellung von KI in Ihren Systemen finden Sie im [Transparenzhinweis für Entitätsverknüpfung](/legal/cognitive-services/language-service/transparency-note?context=/azure/cognitive-services/language-service/context/context). Weitere Informationen finden Sie auch in den folgenden Artikeln:

[!INCLUDE [Responsible AI links](../includes/overview-responsible-ai-links.md)]

## <a name="next-steps"></a>Nächste Schritte

Es gibt zwei Möglichkeiten für die ersten Schritte zur Verwendung des Features für die Entitätsverknüpfung:
* [Language Studio](../language-studio.md) ist eine webbasierte Plattform, mit der Sie mehrere Features von Azure Cognitive Services for Language ausprobieren können, ohne Code schreiben zu müssen.
* Im [Schnellstartartikel](quickstart.md) finden Sie eine Anleitung zum Senden von Anfragen an den Dienst mithilfe der REST-API und des Clientbibliothek-SDK.  
