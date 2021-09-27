---
title: Einblick in Azure API Management | Microsoft-Dokumentation
description: Übersicht über alle Einblickoptionen in Azure API Management.
services: api-management
documentationcenter: ''
author: begim
manager: alberts
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/01/2020
ms.author: danlep
ms.openlocfilehash: 02a33de6ecb83f87627b84e8606e4dcc88108679
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128664540"
---
# <a name="observability-in-azure-api-management"></a>Einblick in Azure API Management

Einblick ist die Fähigkeit, den internen Zustand eines Systems zu verstehen, von den Daten, die es produziert, und der Möglichkeit, diese Daten zu untersuchen, bis zum Beantworten von Fragen dazu, was passiert ist und warum. 

Azure API Management unterstützt Organisationen dabei, die Verwaltung aller APIs zu zentralisieren. Da es als einziger Einstiegspunkt für den gesamten API-Datenverkehr fungiert, ist es ein idealer Ort, um die APIs zu beobachten. 

## <a name="observability-tools"></a>Einblicktools

In der folgenden Tabelle werden alle Tools zusammengefasst, die von API Management unterstützt werden, um APIs zu beobachten, wobei jedes davon für eins oder mehrere Szenarien nützlich ist:

| Tool        | Hilfreich für    | Datenverzögerung | Aufbewahrung | Stichproben | Datenart | Aktiviert|
|:------------- |:-------------|:---- |:----|:---- |:--- |:---- 
| **[API-Inspektor](api-management-howto-api-inspector.md)** | Testen und Debuggen | Sofort | Letzte 100 Ablaufverfolgungen | Aktivierung auf Anforderung | Ablaufverfolgungen von Anforderungen | Always
| **Integrierte Analysen** | Berichterstellung und Überwachung | Minuten | Gültigkeitsdauer | 100 % | Berichte und Protokolle | Always |
| **[Azure Monitor-Metriken](api-management-howto-use-azure-monitor.md)** | Berichterstellung und Überwachung | Minuten | 93 Tage (zum Verlängern upgraden) | 100 % | Metriken | Always |
| **[Azure Monitor-Protokolle](api-management-howto-use-azure-monitor.md)** | Berichterstellung, Überwachung und Debuggen | Minuten | 31 Tage/5 GB (zum Verlängern upgraden) | 100 % (anpassbar) | Protokolle | Optional |
| **[Azure Application Insights](api-management-howto-app-insights.md)** | Berichterstellung, Überwachung und Debuggen | Sekunden | 90 Tage/5 GB (zum Verlängern upgraden) | Benutzerdefiniert | Protokolle, Metriken | Optional |
| **[Protokollierung über Azure Event Hub](api-management-howto-log-event-hubs.md)** | Benutzerdefinierte Szenarien | Sekunden | Vom Benutzer verwaltet | Benutzerdefiniert | Benutzerdefiniert | Optional |

### <a name="self-hosted-gateway"></a>Selbstgehostetes Gateway

Alle oben erwähnten Tools werden vom verwalteten Gateway in der Cloud unterstützt. Das [selbstgehostete Gateway](self-hosted-gateway-overview.md) sendet aktuell keine Diagnoseprotokolle an Azure Monitor. Es ist jedoch möglich, Protokolle lokal dort zu konfigurieren und persistent zu speichern, wo das selbstgehostete Gateway bereitgestellt ist. Weitere Informationen finden Sie unter [Konfigurieren von Cloudmetriken und -protokollen für selbstgehostete Gateways](how-to-configure-cloud-metrics-logs.md) und [Konfigurieren lokaler Metriken und Protokolle für selbstgehostete Gateways](how-to-configure-local-metrics-logs.md).

## <a name="next-steps"></a>Nächste Schritte

* [Weitere Informationen zu API Management finden Sie in den Tutorials](import-and-publish.md).
