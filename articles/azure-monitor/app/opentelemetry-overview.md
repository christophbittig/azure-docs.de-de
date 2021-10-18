---
title: OpenTelemetry mit Azure Monitor Übersicht
description: Bietet einen Überblick über die Verwendung von OpenTelemetry mit Azure Monitor.
ms.topic: conceptual
ms.date: 10/11/2021
author: mattmccleary
ms.author: mmcc
ms.openlocfilehash: 268d7d8372fef1568a369f956e3caa91749f0e8e
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/12/2021
ms.locfileid: "129859628"
---
# <a name="opentelemetry-overview"></a>OpenTelemetry-Übersicht

Microsoft freut sich darauf, [OpenTelemetry](https://opentelemetry.io/) als die Zukunft der Telemetrie-Instrumentierung zu begrüßen. Sie, unsere Kunden, haben nach einer herstellerneutralen Instrumentierung gefragt, und wir freuen uns, mit der OpenTelemetry-Community zusammenzuarbeiten, um konsistente APIs/SDKs für verschiedene Sprachen zu erstellen.

Microsoft hat mit Projektbeteiligten von zwei zuvor populären Open-Source-Telemetrieprojekten, [OpenCensus](https://opencensus.io/) und [OpenTracing](https://opentracing.io/), zusammengearbeitet, um ein einziges Projekt zu schaffen - OpenTelemetry. OpenTelemetry umfasst Beiträge von allen wichtigen Cloud- und Application Performance Management (APM)-Anbietern und wird von der [Cloud Native Computing Foundation (CNCF)](https://www.cncf.io/) betreut, deren Platinmitglied Microsoft ist.

## <a name="concepts"></a>Konzepte

Die Telemetrie, d. h. die zur Beobachtung Ihrer Anwendung gesammelten Daten, kann in drei Arten oder "Säulen" unterteilt werden:
1.  Verteilte Ablaufverfolgung
2.  Metriken
3.  Protokolle

Zunächst hat sich die OpenTelemetry-Gemeinschaft dem Distributed Tracing angenommen. Metriken und Protokolle befinden sich noch in der Entwicklung. Eine vollständige Observability-Story umfasst alle drei Säulen, aber derzeit bieten unsere [Azure Monitor OpenTelemetry-basierten Exporter **Vorschau** für .NET, Python und JavaScript](opentelemetry-enable.md) **nur verteiltes Tracing** enthalten.

Es gibt mehrere Quellen, die diese drei Säulen im Detail erklären, darunter die [OpenTelemetry Community Website](https://opentelemetry.io/docs/concepts/data-sources/), [OpenTelemetry Specifications](https://github.com/open-telemetry/opentelemetry-specification/blob/main/specification/overview.md) und [Distributed Systems Observability](https://www.oreilly.com/library/view/distributed-systems-observability/9781492033431/ch04.html) von Cindy Sridharan.

In den folgenden Abschnitten werden wir einige Grundlagen der Telemetrieerfassung behandeln.

### <a name="instrumenting-your-application"></a>Instrumentierung Ihrer Anwendung

Auf einer grundlegenden Ebene bedeutet "Instrumentierung" einfach, dass eine Anwendung Telemetriedaten erfassen kann.

Es gibt zwei Methoden, Ihre Anwendung zu instrumentieren:
1.  Manuelle Instrumentierung
2.  Automatische Instrumentierung (Auto-Instrumentierung)

Die manuelle Instrumentierung ist die Codierung gegen die OpenTelemetry-API. Im Kontext eines Endbenutzers bezieht sich dies typischerweise auf die Installation eines sprachspezifischen SDKs in einer Anwendung. Manuelle Instrumentierungspakete bestehen aus unseren [Azure Monitor OpenTelemetry-basierten Exportern **Vorschau** für .NET, Python und JavaScript](opentelemetry-enable.md).

> [!IMPORTANT]
> "Manuell" bedeutet **NICHT**, dass Sie komplexen Code schreiben müssen, um Spans für verteilte Traces zu definieren (obwohl dies eine Option bleibt). Mit einer umfassenden und wachsenden Gruppe von Instrumentierungsbibliotheken, die von OpenTelemetry-Mitwirkenden gepflegt werden, können Sie mühelos Telemetriesignale für gängige Frameworks und Bibliotheken erfassen. Eine Teilmenge der OpenTelemetry Instrumentation Libraries wird von Azure Monitor unterstützt werden, basierend auf dem Feedback unserer Kunden. Darüber hinaus arbeiten wir daran, die beliebtesten Azure Service SDKs so zu instrumentieren, dass sie das OpenTelemetry Protocol (OTLP) ausgeben und für Azure Monitor optimiert werden.

Andererseits ermöglicht die automatische Instrumentierung die Sammlung von Telemetriedaten durch Konfiguration, ohne den Code der Anwendung zu berühren. Dies ist zwar bequemer, aber tendenziell weniger konfigurierbar und nicht in allen Sprachen verfügbar. Das OpenTelemetry-basierte Auto-Instrumentierungsangebot von Azure Monitor besteht aus dem [Java 3.X OpenTelemetry-basierten GA-Angebot](java-in-process-agent.md), und wir investieren weiterhin in dieses Angebot auf der Grundlage von Kundenfeedback. Die OpenTelemetry-Community experimentiert auch mit C#- und Python-Autoinstrumentierung, aber Azure Monitor konzentriert sich darauf, in naher Zukunft eine einfache und effektive manuelle Instrumentierung zu entwickeln.

### <a name="sending-your-telemetry"></a>Senden der Telemetrie

Es gibt auch zwei Möglichkeiten, Ihre Daten an Azure Monitor (oder einen anderen Anbieter) zu senden.
1. Direkter Exporter
2. Über einen Agenten

Ein direkter Exporter sendet Telemetriedaten prozessintern (aus dem Code der Anwendung) direkt an den Ingestion-Endpunkt von Azure Monitor. Der Hauptvorteil dieser Methode liegt in der Einfachheit des Onboardings.

**Alle derzeit von Azure Monitor unterstützten OpenTelemetry-basierten Angebote verwenden einen direkten Exporter**. 

Alternativ dazu bietet das Senden von Telemetrie über einen Agenten einen Pfad für jede OpenTelemetry-unterstützte Sprache, um sie über [OTLP](https://github.com/open-telemetry/opentelemetry-specification/blob/main/specification/protocol/README.md) an Azure Monitor zu senden. Kunden können dann auch Anwendungen verfolgen, die in anderen als den von uns [unterstützten Sprachen](platforms.md) geschrieben wurden. 

> [!NOTE]
> Einige Kunden haben begonnen, den [OpenTelemetry-Collector](https://github.com/open-telemetry/opentelemetry-collector/blob/main/docs/design.md) als Agent-Alternative zu verwenden, obwohl Microsoft den Ansatz "Via an Agent" für die Anwendungsüberwachung noch nicht offiziell unterstützt. In der Zwischenzeit wurde von der Open-Source-Community ein Azure Monitor-Exportprogramm für den OpenTelemetry-Collector bereitgestellt, der von einigen Kunden zum Senden von Daten an Azure Monitor Application Insights verwendet wird.

## <a name="terms"></a>Begriffe

Siehe [Glossar](https://github.com/open-telemetry/opentelemetry-specification/blob/main/specification/glossary.md) in den OpenTelemetry-Spezifikationen.

Einige ältere Begriffe in Application Insights sind angesichts der Konvergenz der Branche zu OpenTelemetry verwirrend. Die folgende Tabelle zeigt diese Unterschiede auf. Letztendlich werden die Begriffe in Application Insights durch OpenTelemetry-Begriffe ersetzt.

Application Insights | OpenTelemetry
------ | ------
Traces   | Protokolle
Kanal   | Exporter  
Codelos / Agentenbasiert   | Auto-Instrumentierung

## <a name="next-step"></a>Nächster Schritt

Auf den folgenden Seiten finden Sie für jede Sprache eine Anleitung zur Aktivierung und Konfiguration der OpenTelemetry-basierten Angebote von Microsoft. Wichtig ist, dass wir die verfügbaren Funktionen und Einschränkungen der einzelnen Angebote erläutern, damit Sie entscheiden können, ob OpenTelemetry für Ihr Projekt geeignet ist.
- [.NET](opentelemetry-enable.md) 
- [Java](java-in-process-agent.md)
- [JavaScript](opentelemetry-enable.md)
- [Python](opentelemetry-enable.md)
