---
title: 'Application Insights: Programmiersprachen, Plattformen und Integrationsmöglichkeiten | Microsoft-Dokumentation'
description: Verfügbare Programmiersprachen, Plattformen und Integrationsmöglichkeiten für Application Insights
ms.topic: conceptual
ms.date: 07/18/2019
ms.reviewer: olegan
ms.openlocfilehash: d388914badbd9ac8870a9d5e23370cd5b0319eea
ms.sourcegitcommit: 8154d7f8642d783f637cf6d857b4abbe28033f53
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/12/2021
ms.locfileid: "113616395"
---
# <a name="supported-languages"></a>Unterstützte Sprachen

* [C#|VB (.NET)](./asp-net.md)
* [Java](./java-in-process-agent.md)
* [JavaScript](./javascript.md)
* [Node.JS](./nodejs.md)
* [Python](./opencensus-python.md)

## <a name="supported-platforms-and-frameworks"></a>Unterstützte Plattformen und Frameworks

### <a name="instrumentation-for-already-deployed-applications-codeless-agent-based"></a>Instrumentierung für bereits bereitgestellte Anwendungen (ohne Code, Agent-basiert)
* [Virtuelle Azure-Computer und Azure-VM-Skalierungsgruppen](./azure-vm-vmss-apps.md)
* [Azure App Service](./azure-web-apps.md)
* [ASP.NET (für mit IIS gehostete Web-Apps)](./status-monitor-v2-overview.md)
* [Azure Cloud Services](./cloudservices.md), einschließlich Web- und Workerrollen
* [Azure-Funktionen](../../azure-functions/functions-monitoring.md)
### <a name="instrumentation-through-code-sdks"></a>Instrumentierung durch Code (SDKs)
* [ASP.NET](./asp-net.md)
* [ASP.NET Core](./asp-net-core.md)
* [Android](../app/mobile-center-quickstart.md) (App Center)
* [iOS](../app/mobile-center-quickstart.md) (App Center)
* [Java EE](./java-in-process-agent.md)
* [Node.JS](https://www.npmjs.com/package/applicationinsights)
* [Python](./opencensus-python.md)
* [Universelle Windows-App](../app/mobile-center-quickstart.md) (App Center)
* [Windows-Desktopanwendungen, -Dienste und -Workerrollen](./windows-desktop.md)
* [React](./javascript-react-plugin.md)
* [React Native](./javascript-react-native-plugin.md)

## <a name="logging-frameworks"></a>Protokollierungsframeworks
* [ILogger](./ilogger.md)
* [Log4Net, NLog oder System.Diagnostics.Trace](./asp-net-trace-logs.md)
* [Java, Log4J oder Logback](java-2x-trace-logs.md)
* [LogStash-Plug-in](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-output-applicationinsights)
* [Azure Monitor](/archive/blogs/msoms/application-insights-connector-in-oms)

## <a name="export-and-data-analysis"></a>Export und Datenanalyse
* [Power BI](https://powerbi.microsoft.com/blog/explore-your-application-insights-data-with-power-bi/)
* [Stream Analytics](./export-power-bi.md)

## <a name="unsupported-sdks"></a>Nicht unterstützte SDKs
Wir sind uns bewusst, dass es noch einige andere von der Community unterstützte SDKs gibt. Für Azure Monitor wird aber nur Support geleistet, wenn die auf dieser Seite aufgeführten unterstützten SDKs verwendet werden. Wir prüfen ständig neue Möglichkeiten zur Unterstützung anderer Sprachen. Verfolgen Sie daher die Seite mit den [GitHub-Ankündigungen](https://github.com/microsoft/ApplicationInsights-Announcements/issues), um die aktuellen SDK-News zu erhalten. 

