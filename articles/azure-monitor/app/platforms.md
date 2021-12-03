---
title: 'Application Insights: Programmiersprachen, Plattformen und Integrationsmöglichkeiten | Microsoft-Dokumentation'
description: Verfügbare Programmiersprachen, Plattformen und Integrationsmöglichkeiten für Application Insights
ms.topic: conceptual
ms.date: 10/29/2021
ms.reviewer: olegan
ms.openlocfilehash: 44c08d1008691826299d1fa41507df26064f6274
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131462024"
---
# <a name="supported-languages"></a>Unterstützte Sprachen

* [C#|VB (.NET)](./asp-net.md)
* [Java](./java-in-process-agent.md)
* [JavaScript](./javascript.md)
* [Node.js](./nodejs.md)
* [Python](./opencensus-python.md)

## <a name="supported-platforms-and-frameworks"></a>Unterstützte Plattformen und Frameworks

### <a name="azure-service-integration-portal-enablement-arm-deployments"></a>Azure-Dienstintegration (Portal-Aktivierung, ARM-Bereitstellungen)
* [Virtuelle Azure-Computer und Azure-VM-Skalierungsgruppen](./azure-vm-vmss-apps.md)
* [Azure App Service](./azure-web-apps.md)
* [Azure-Funktionen](../../azure-functions/functions-monitoring.md)
* [Azure Cloud Services](./cloudservices.md), einschließlich Web- und Workerrollen

### <a name="auto-instrumentation-enable-without-code-changes"></a>Automatische Instrumentierung (ohne Codeänderungen aktivieren)
* [ASP.NET (für mit IIS gehostete Web-Apps)](./status-monitor-v2-overview.md)
* [Java](./java-in-process-agent.md)

### <a name="manual-instrumentation--sdk-some-code-changes-required"></a>Manuelle Instrumentierung/SDK (einige Codeänderungen erforderlich)
* [ASP.NET](./asp-net.md)
* [ASP.NET Core](./asp-net-core.md)
* [Node.js](./nodejs.md)
* [Python](./opencensus-python.md)
* [JavaScript – Web](./javascript.md)
  * [React](./javascript-react-plugin.md)
  * [React Native](./javascript-react-native-plugin.md)
  * [Angular](./javascript-angular-plugin.md)
* [Windows-Desktopanwendungen, -Dienste und -Workerrollen](./windows-desktop.md)
* [Universelle Windows-App](../app/mobile-center-quickstart.md) (App Center)
* [Android](../app/mobile-center-quickstart.md) (App Center)
* [iOS](../app/mobile-center-quickstart.md) (App Center)

> [!NOTE]
> Die OpenTelemetry-basierte Instrumentierung ist im Vorschauzustand für [C#, Node.js und Python](opentelemetry-enable.md) verfügbar. Prüfen Sie die Beschränkungen, die am Anfang der offiziellen Dokumentation der einzelnen Sprachen angegeben sind. Diejenigen, die den vollständigen Funktionsumfang benötigen, sollten die vorhandenen Application Insights-SDKs verwenden.

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
Es gibt mehrere andere von der Community unterstützte Application Insights-SDKs. Jedoch bietet Azure Monitor nur dann Unterstützung, wenn die auf dieser Seite aufgelisteten Instrumentierungsoptionen verwendet werden. Es wird dauerhaft nach Möglichkeiten gesucht, die Unterstützung auf andere Sprachen zu erweitern. Unter [Azure-Updates für Application Insights](https://azure.microsoft.com/updates/?query=application%20insights) finden Sie Neuigkeiten zu den neuesten SDKs.
