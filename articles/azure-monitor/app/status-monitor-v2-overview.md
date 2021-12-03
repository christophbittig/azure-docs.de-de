---
title: Übersicht über den Azure Application Insights-Agent | Microsoft-Dokumentation
description: Hier finden Sie eine Übersicht über den Application Insights-Agent. Überwachen Sie die Websiteleistung ohne erneute Bereitstellung der Website. Funktioniert mit ASP.NET-Web-Apps, die lokal, auf virtuellen Computern oder in Azure gehostet werden.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 09/16/2019
ms.openlocfilehash: 3337cf0eb5bfff686f9047d0f5ffea6dde670f3e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131078197"
---
# <a name="deploy-azure-monitor-application-insights-agent-for-on-premises-servers"></a>Bereitstellen des Azure Monitor Application Insights-Agents für lokale Server

> [!IMPORTANT]
> Diese Anleitung wird für lokale Bereitstellungen von Application Insights-Agent und Bereitstellungen in einer anderen Cloud als Azure empfohlen. Die empfohlene Vorgehensweise für Bereitstellungen auf Azure-VMs und in VM-Skalierungsgruppen finden Sie [hier](./azure-vm-vmss-apps.md).

Application Insights-Agent (früher Statusmonitor V2) ist ein im [PowerShell-Katalog](https://www.powershellgallery.com/packages/Az.ApplicationMonitor) veröffentlichtes PowerShell-Modul.
Es ersetzt den Statusmonitor.
Telemetriedaten werden an das Azure-Portal gesendet, wo Sie Ihre App [überwachen](./app-insights-overview.md) können.

> [!NOTE]
> Das Modul unterstützt derzeit nur die codelose Instrumentierung von mit IIS gehosteten .NET- und .NET Core-Web-Apps. Verwenden Sie ein SDK zum Instrumentieren von Java- und Node.js-Anwendungen.

## <a name="powershell-gallery"></a>PowerShell-Katalog

Sie finden den Application Insights-Agent unter https://www.powershellgallery.com/packages/Az.ApplicationMonitor.

![PowerShell-Katalog](https://img.shields.io/powershellgallery/v/Az.ApplicationMonitor.svg?color=Blue&label=Current%20Version&logo=PowerShell&style=for-the-badge)


## <a name="instructions"></a>Instructions
- Lesen Sie unsere [Anweisungen für den Einstieg](status-monitor-v2-get-started.md), um mithilfe präziser Codebeispiele starten zu können.
- Lesen Sie unsere [ausführlichen Anweisungen](status-monitor-v2-detailed-instructions.md), um detaillierte Informationen zu den ersten Schritten zu erhalten.

## <a name="powershell-api-reference"></a>PowerShell-API-Referenz
- [Disable-ApplicationInsightsMonitoring](./status-monitor-v2-api-reference.md#disable-applicationinsightsmonitoring)
- [Disable-InstrumentationEngine](./status-monitor-v2-api-reference.md#disable-instrumentationengine)
- [Enable-ApplicationInsightsMonitoring](./status-monitor-v2-api-reference.md#enable-applicationinsightsmonitoring)
- [Enable-InstrumentationEngine](./status-monitor-v2-api-reference.md#enable-instrumentationengine)
- [Get-ApplicationInsightsMonitoringConfig](./status-monitor-v2-api-reference.md#get-applicationinsightsmonitoringconfig)
- [Get-ApplicationInsightsMonitoringStatus](./status-monitor-v2-api-reference.md#get-applicationinsightsmonitoringstatus)
- [Set-ApplicationInsightsMonitoringConfig](./status-monitor-v2-api-reference.md#set-applicationinsightsmonitoringconfig)
- [Start-ApplicationInsightsMonitoringTrace](./status-monitor-v2-api-reference.md#start-applicationinsightsmonitoringtrace)

## <a name="troubleshooting"></a>Problembehandlung
- [Problembehandlung](status-monitor-v2-troubleshoot.md)
- [Bekannte Probleme](status-monitor-v2-troubleshoot.md#known-issues)


## <a name="faq"></a>Häufig gestellte Fragen

- Unterstützt der Application Insights-Agent Proxy-Installationen?

  *Ja*. Es gibt mehrere Möglichkeiten, den Application Insights-Agent herunterzuladen. Wenn Ihr Computer über einen Internetzugang verfügt, können Sie ein Onboarding des PowerShell-Katalogs mithilfe der `-Proxy`-Parameter durchführen.
Sie können dieses Modul auch manuell herunterladen und es auf Ihrem Computer installieren oder direkt verwenden.
Jede dieser Optionen wird in den [ausführlichen Anweisungen](status-monitor-v2-detailed-instructions.md) beschrieben.

- Werden ASP.NET Core-Anwendungen von Version 2 des Statusmonitors unterstützt?

  *Ja*. Ab [Application Insights-Agent 2.0.0-beta1](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/2.0.0-beta1) werden in IIS gehostete ASP.NET Core-Anwendungen unterstützt.

- Wie überprüfe ich, ob die Aktivierung erfolgreich war?

  - Mit dem Cmdlet [Get-ApplicationInsightsMonitoringStatus](./status-monitor-v2-api-reference.md#get-applicationinsightsmonitoringstatus) können Sie überprüfen, ob die Aktivierung erfolgreich war.
  - Es wird empfohlen, anhand von [Livemetriken](./live-stream.md) schnell zu ermitteln, ob Ihre App Telemetriedaten sendet.

  - Sie können auch [Log Analytics](../logs/log-analytics-tutorial.md) verwenden, um alle Cloudrollen aufzulisten, die derzeit Telemetriedaten senden.
      ```Kusto
      union * | summarize count() by cloud_RoleName, cloud_RoleInstance
      ```


## <a name="release-notes"></a>Versionshinweise

### <a name="200-beta2"></a>2.0.0-beta2

- ApplicationInsights .NET/.NET Core SDK wurde auf 2.18.1-redfield aktualisiert.

### <a name="200-beta1"></a>2.0.0-beta1

- ASP.NET Core Feature "Automatische Instrumentierung" hinzugefügt.


## <a name="next-steps"></a>Nächste Schritte

Anzeigen der Telemetrie:

* [Untersuchen Sie Metriken](../essentials/metrics-charts.md) zum Überwachen der Leistung und Nutzung.
* [Durchsuchen Sie Ereignisse und Protokolle](./diagnostic-search.md), um Probleme zu diagnostizieren.
* Verwenden Sie [Analytics](../logs/log-query-overview.md) für erweiterte Abfragen.
* [Erstellen Sie Dashboards](./overview-dashboard.md).

Hinzufügen weiterer Telemetrieelemente:

* [Erstellen Sie Webtests](monitor-web-app-availability.md), um sicherzustellen, dass Ihre Website live bleibt.
* [Fügen Sie Webclient-Telemetriedaten hinzu](./javascript.md), um Ausnahmen von Webseitencode anzuzeigen und Ablaufverfolgungsaufrufe zu aktivieren.
* [Fügen Sie Ihrem Code das Application Insights SDK hinzu](./asp-net.md), um Ablaufverfolgungs- und Protokollaufrufe einfügen zu können.
