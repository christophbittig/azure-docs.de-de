---
title: Verwenden von Azure Monitor-Metriken mit Azure Functions
description: Hier erfahren Sie, wie Sie Azure Monitor-Metriken zum Anzeigen und Abfragen von Azure Functions-Telemetriedaten verwenden, die von Azure Application Insights gesammelt und gespeichert werden.
ms.topic: how-to
ms.date: 07/4/2021
ms.openlocfilehash: b5f0b483295cccff8caf97503233e1e7881173cf
ms.sourcegitcommit: e0ef8440877c65e7f92adf7729d25c459f1b7549
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/09/2021
ms.locfileid: "113568596"
---
# <a name="using-azure-monitor-metric-with-azure-functions"></a>Verwenden von Azure Monitor-Metriken mit Azure Functions

Azure Functions ist in Azure Monitor-Metriken integriert und ermöglicht Ihnen die Analyse von Metriken, die während der Ausführung von Ihrer Funktions-App generiert werden. Weitere Informationen finden Sie in der [Übersicht über Azure Monitor-Metriken](../azure-monitor/essentials/data-platform-metrics.md). Diese Metriken geben Aufschluss darüber, wie Ihre Funktions-App auf der App Service-Plattform ausgeführt wird. Sie können Daten zum Ressourcenverbrauch überprüfen, mit deren Hilfe die Kosten des Verbrauchstarifs abgeschätzt werden. Um detaillierte Telemetriedaten Ihrer Funktionsausführungen (einschließlich Protokolldaten) zu untersuchen, sollten Sie auch [Application Insights](functions-monitoring.md) in Azure Monitor verwenden. 

> [!NOTE]
> Azure Monitor-Metriken werden derzeit nicht unterstützt, wenn Ihre Funktions-App unter Linux in einem Verbrauchstarif ausgeführt wird.

## <a name="available-metrics"></a>Verfügbare Metriken

Azure Monitor erfasst numerische Daten von einer Gruppe überwachter Ressourcen, die in eine Zeitreihendatenbank eingegeben werden. Azure Monitor erfasst sowohl für Functions als auch die zugrunde liegenden App Service-Ressourcen spezifische Metriken.   

### <a name="functions-specific-metrics"></a>Functions-spezifische Metriken

Zwei spezifische Metriken für Azure Functions sind von Interesse:

| Metrik | BESCHREIBUNG |
| ---- | ---- |
| **FunctionExecutionCount** | Die Anzahl der Funktionsausführungen gibt an, wie oft Ihre Funktions-App ausgeführt wurde. Dieser Wert korreliert mit der Anzahl von Ausführungen einer Funktion in Ihrer App. |
| **FunctionExecutionUnits** | Die Funktionsausführungseinheiten sind eine Kombination aus Ausführungszeit und der Arbeitsspeicherauslastung.  Arbeitsspeicherdaten sind keine aktuell in Azure Monitor verfügbare Metrik. Wenn Sie jedoch die Speichernutzung Ihrer App optimieren möchten, können Sie die von Application Insights erfassten Daten der Leistungsindikatoren verwenden. Diese Metrik für derzeit nicht für die App Service-Tarife Premium und Dedicated unter Linux unterstützt.|

Diese Metriken werden insbesondere zum [Abschätzen der Kosten des Verbrauchstarifs](functions-consumption-costs.md) verwendet. 

### <a name="general-app-service-metrics"></a>Allgemeine App Service-Metriken

Neben funktionsspezifischen Metriken implementiert die App Service-Plattform weitere Metriken, die Sie zum Überwachen von Funktions-Apps verwenden können. Die vollständige Liste finden Sie unter [Für App Service-Apps verfügbare Metriken](../app-service/web-sites-monitor.md#understand-metrics).

## <a name="accessing-metrics"></a>Zugreifen auf Metriken

Zum Abrufen der Metrikdaten von Azure Monitor können Sie entweder den [Azure Monitor-Metrik-Explorer](../azure-monitor/essentials/metrics-getting-started.md) im [Azure-Portal](https://portal.azure.com) oder REST-APIs verwenden. 

In den folgenden Beispielen werden Azure Monitor-Metriken verwendet, um die Kosten für die Ausführung Ihrer Funktions-App in einem Verbrauchstarif abzuschätzen. Weitere Informationen dazu finden Sie unter [Abschätzen der Kosten des Verbrauchstarifs](functions-consumption-costs.md).

[!INCLUDE [functions-monitor-metrics-consumption](../../includes/functions-monitor-metrics-consumption.md)]  

Weitere Informationen über die Verwendung des Metrik-Explorers finden Sie unter [Erste Schritte mit dem Metrik-Explorer von Azure](../azure-monitor/essentials/metrics-getting-started.md).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Überwachung in Azure Functions finden Sie unter:

+ [Überwachen von Azure Functions](functions-monitoring.md)
+ [Analysieren von Azure Functions-Telemetriedaten in Application Insights](analyze-telemetry-data.md)
