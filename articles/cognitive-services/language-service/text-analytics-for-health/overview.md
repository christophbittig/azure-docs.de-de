---
title: Was ist die Textanalyse für Gesundheitsdaten in Azure Cognitive Service für Sprache?
titleSuffix: Azure Cognitive Services
description: Eine Übersicht über die Textanalyse für Gesundheitsdaten in Azure Cognitive Services, mit der Sie medizinische Informationen aus unstrukturiertem Text, z. B. aus klinischen Dokumenten, extrahieren können.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-health, ignite-fall-2021
ms.openlocfilehash: 609d60d2094372cd5c1e476afd3abde1df7f68af
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131100319"
---
# <a name="what-is-text-analytics-for-health-in-azure-cognitive-service-for-language"></a>Was ist die Textanalyse für Gesundheitsdaten in Azure Cognitive Service für Sprache?

Die Textanalyse für Gesundheitsdaten ist ein Feature von [Azure Cognitive Service für Sprache](../overview.md). Dabei handelt es sich um eine Sammlung von Machine Learning- und KI-Algorithmen in der Cloud für die Entwicklung intelligenter Anwendungen, die geschriebene Sprache umfassen. 

Die Textanalyse für Gesundheitsdaten ist ein Containerdienst, der relevante medizinische Informationen aus unstrukturierten Texten wie Arztbriefen, Entlassungsberichten, klinischen Dokumenten und elektronischen Gesundheitsakten extrahiert und bezeichnet.

Diese Dokumentation enthält die folgenden Arten von Artikeln:

* [**Schnellstarts**](quickstart.md) sind Anleitungen zu den ersten Schritten, die Sie durch das Senden von Anforderungen an den Dienst führen.
* [**Schrittanleitungen**](how-to/call-api.md) enthalten Anweisungen zur spezifischeren oder individuelleren Verwendung des Diensts.
* Die [**konzeptionellen Artikel**](concepts/health-entity-categories.md) enthalten ausführliche Erläuterungen der Funktionen und Features eines Diensts.

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Introducing-Text-Analytics-for-Health/player]

## <a name="features"></a>Features

[!INCLUDE [Text Analytics for health](includes/features.md)]

[!INCLUDE [Typical workflow for pre-configured language features](../includes/overview-typical-workflow.md)]

## <a name="deploy-on-premises-using-docker-containers"></a>Lokales Bereitstellen unter Verwendung von Docker-Containern

Verwenden Sie den verfügbaren Docker-Container, um [dieses Feature lokal bereitzustellen](how-to/use-containers.md). Mithilfe dieser Docker-Container können Sie den Dienst näher an Ihre Daten heranbringen, um Compliance- oder Sicherheitsanforderungen zu erfüllen oder anderen betrieblichen Anforderungen gerecht zu werden.

## <a name="responsible-ai"></a>Verantwortungsbewusste künstliche Intelligenz 

Ein KI-System umfasst nicht nur die Technologie, sondern auch die Personen, die das System verwenden, die davon betroffenen Personen, und die Umgebung, in der es bereitgestellt wird. Weitere Informationen zur verantwortungsbewussten Verwendung und Bereitstellung von KI in Ihren Systemen finden Sie im [Transparenzhinweis für die Textanalyse für Gesundheitsdaten](/legal/cognitive-services/language-service/transparency-note-health?context=/azure/cognitive-services/language-service/context/context). Weitere Informationen finden Sie auch in den folgenden Artikeln:

[!INCLUDE [Responsible AI links](../includes/overview-responsible-ai-links.md)]

## <a name="next-steps"></a>Nächste Schritte

Es gibt zwei Möglichkeiten für die ersten Schritte zur Verwendung des Features für die Entitätsverknüpfung:
* [Language Studio](../language-studio.md) ist eine webbasierte Plattform, mit der Sie mehrere Features von Azure Cognitive Services for Language ausprobieren können, ohne Code schreiben zu müssen.
* Im [Schnellstartartikel](quickstart.md) finden Sie eine Anleitung zum Senden von Anfragen an den Dienst mithilfe der REST-API und des Clientbibliothek-SDK.  
