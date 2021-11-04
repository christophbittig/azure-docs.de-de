---
title: Was sind Stimmungsanalyse und Opinion Mining in Azure Cognitive Service für Sprache?
titleSuffix: Azure Cognitive Services
description: Eine Übersicht über das Feature für die Stimmungsanalyse in Azure Cognitive Services, mit dem Sie durch Mining von Text auf Anhaltspunkte feststellen können, was Menschen von einem Thema halten.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-sentiment-opinion-mining, ignite-fall-2021
ms.openlocfilehash: 8807b12f9920fd62901aebed862ecba483e77bb0
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131101217"
---
# <a name="what-is-sentiment-analysis-and-opinion-mining-in-azure-cognitive-service-for-language"></a>Was sind Stimmungsanalyse und Opinion Mining in Azure Cognitive Service für Sprache?

Die Stimmungsanalyse und Opinion Mining sind Features von [Azure Cognitive Service für Sprache](../overview.md). Dabei handelt es sich um eine Sammlung von Machine Learning- und KI-Algorithmen in der Cloud für die Entwicklung intelligenter Anwendungen, die geschriebene Sprache umfassen. Mithilfe dieser Features können Sie durch Mining von Text auf Anhaltspunkte zu positiven oder negativen Stimmungen feststellen, was Personen von Ihrer Marke oder Ihrem Thema halten, und diese bestimmten Aspekten des Texts zuordnen. 

* [**Schnellstarts**](quickstart.md) sind Anleitungen zu den ersten Schritten, die Sie durch das Senden von Anforderungen an den Dienst führen.
* [**Schrittanleitungen**](how-to/call-api.md) enthalten Anweisungen zur spezifischeren oder individuelleren Verwendung des Diensts.

## <a name="sentiment-analysis"></a>Stimmungsanalyse 

Beim Stimmungsanalyse-Feature werden Stimmungsbezeichnungen (z. B. „negativ“, „neutral“ und „positiv“) basierend auf der höchsten Zuverlässigkeitsbewertung angegeben, die vom Dienst auf Satz- und Dokumentebene gefunden wird. Darüber hinaus werden bei diesem Feature Zuverlässigkeitsbewertungen zwischen 0 und 1 für jedes Dokument und jeden darin enthaltenen Satz zurückgegeben (positive, neutrale und negative Stimmung). 

### <a name="deploy-on-premises-using-docker-containers"></a>Lokales Bereitstellen unter Verwendung von Docker-Containern

Verwenden Sie den verfügbaren Docker-Container, um [die Stimmungsanalyse lokal bereitzustellen](how-to/use-containers.md). Mithilfe dieser Docker-Container können Sie den Dienst näher an Ihre Daten heranbringen, um Compliance- oder Sicherheitsanforderungen zu erfüllen oder anderen betrieblichen Anforderungen gerecht zu werden.

## <a name="opinion-mining"></a>Opinion Mining

Opinion Mining ist ein Feature der Stimmungsanalyse. Dieses Feature wird in der Verarbeitung natürlicher Sprache (Natural Language Processing, NLP) auch als aspektbasierte Stimmungsanalyse bezeichnet und bietet feiner abgestufte Informationen zu den Meinungen in Bezug auf Wörter (z. B. Attribute von Produkten oder Dienstleistungen) in Texten.

[!INCLUDE [Typical workflow for pre-configured language features](../includes/overview-typical-workflow.md)]

## <a name="responsible-ai"></a>Verantwortungsbewusste künstliche Intelligenz 

Ein KI-System umfasst nicht nur die Technologie, sondern auch die Personen, die das System verwenden, die davon betroffenen Personen, und die Umgebung, in der es bereitgestellt wird. Weitere Informationen zur verantwortungsbewussten Verwendung und Bereitstellung von KI in Ihren Systemen finden Sie im [Transparenzhinweis für die Stimmungsanalyse](/legal/cognitive-services/language-service/transparency-note-sentiment-analysis?context=/azure/cognitive-services/language-service/context/context). Weitere Informationen finden Sie auch in den folgenden Artikeln:

[!INCLUDE [Responsible AI links](../includes/overview-responsible-ai-links.md)]

## <a name="next-steps"></a>Nächste Schritte

Es gibt zwei Möglichkeiten für die ersten Schritte zur Verwendung des Features für die Entitätsverknüpfung:
* [Language Studio](../language-studio.md) ist eine webbasierte Plattform, mit der Sie mehrere Features von Azure Cognitive Services for Language ausprobieren können, ohne Code schreiben zu müssen.
* Im [Schnellstartartikel](quickstart.md) finden Sie eine Anleitung zum Senden von Anfragen an den Dienst mithilfe der REST-API und des Clientbibliothek-SDK.  
