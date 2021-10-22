---
title: 'Überwachen von Anwendungen in Azure Kubernetes Service (AKS) mit Application Insights: Azure Monitor | Microsoft-Dokumentation'
description: Azure Monitor kann nahtlos in Ihre in Kubernetes ausgeführte Anwendung integriert werden und ermöglicht eine blitzschnelle Erkennung von Problemen mit Ihren Apps.
ms.topic: conceptual
ms.date: 05/13/2020
ms.openlocfilehash: 63584a3d753f0b20e91697d0d49e4290348fe22b
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/18/2021
ms.locfileid: "130129793"
---
# <a name="zero-instrumentation-application-monitoring-for-kubernetes---azure-monitor-application-insights"></a>Anwendungsüberwachung ohne Instrumentierung für Kubernetes: Azure Monitor Application Insights

> [!IMPORTANT]
>  Aktuell können Sie den [eigenständigen Java-Agent](./java-in-process-agent.md) verwenden, um die Überwachung für Ihre in Kubernetes ausgeführten Java-Apps zu aktivieren, ohne Ihren Code zu instrumentieren. Bis die Lösung für die nahtlose Aktivierung der Anwendungsüberwachung auch für andere Sprachen zur Verfügung steht, können Sie die SDKs verwenden, um Ihre in AKS ausgeführten Apps zu überwachen: [ASP.NET Core](./asp-net-core.md), [ASP.NET](./asp-net.md), [Node.js](./nodejs.md), [JavaScript](./javascript.md) und [Python](./opencensus-python.md).

## <a name="application-monitoring-without-instrumenting-the-code"></a>Anwendungsüberwachung ohne Codeinstrumentierung
Aktuell ist die Aktivierung der Anwendungsüberwachung ohne Codeinstrumentierung nur in Java möglich. Wenn Sie Anwendungen in anderen Sprachen überwachen möchten, verwenden Sie die SDKs. 

## <a name="java"></a>Java
Nach der Aktivierung sammelt der Java-Agent automatisch eine Vielzahl von Anforderungen, Abhängigkeiten, Protokollen und Metriken aus den am häufigsten genutzten Bibliotheken und Frameworks.

Eine ausführliche Anleitung zur Überwachung Ihrer in Kubernetes ausgeführten Java-Apps sowie für andere Umgebungen finden Sie [hier](./java-in-process-agent.md). 

## <a name="other-languages"></a>Andere Sprachen

Für Anwendungen in anderen Sprachen empfehlen wir aktuell die Verwendung der SDKs:
* [ASP.NET Core](./asp-net-core.md)
* [ASP.NET](./asp-net.md)
* [Node.js](./nodejs.md) 
* [JavaScript](./javascript.md)
* [Python](./opencensus-python.md)

## <a name="next-steps"></a>Nächste Schritte

* Informieren Sie sich ausführlicher über [Azure Monitor](../overview.md) und [Application Insights](./app-insights-overview.md).
* Verschaffen Sie sich einen Überblick über die [verteilte Ablaufverfolgung](./distributed-tracing.md), und informieren Sie sich über die Möglichkeiten der [Anwendungsübersicht](./app-map.md?tabs=net) für Ihr Unternehmen.
