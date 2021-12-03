---
title: Versionshinweise für die Erweiterung für Azure-Web-Apps – Application Insights
description: Versionshinweise für die Erweiterung für Azure-Web-Apps für Laufzeitinstrumentierung mit Application Insights
ms.topic: conceptual
ms.date: 06/26/2020
ms.openlocfilehash: 84d01a2bcc4e371ca03610f2d002eaaf73159b2a
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132316031"
---
# <a name="release-notes-for-azure-web-app-extension-for-application-insights"></a>Versionshinweise für die Erweiterung für Azure-Web-Apps für Application Insights

Dieser Artikel enthält die Versionshinweise für die Erweiterung für Azure-Web-Apps für Laufzeitinstrumentierung mit Application Insights. Sie beziehen sich nur auf vorinstallierte Erweiterungen.

Erfahren Sie mehr über die [Erweiterung für Azure-Web-Apps für Application Insights](azure-web-apps.md)).

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

- Wie finde ich heraus, welche Version der Erweiterung ich zurzeit verwende?
    - Gehe zu `https://<yoursitename>.scm.azurewebsites.net/ApplicationInsights`. Weitere Informationen finden Sie im detaillierten Leitfaden für die Problembehandlung bei der erweiterungsbasierten/Agent-basierten Überwachung für [ASP.NET Core](./azure-web-apps-net-core.md#troubleshooting), [ASP.NET](./azure-web-apps-net.md#troubleshooting), [Java](./azure-web-apps-java.md#troubleshooting) oder [Node.js](./azure-web-apps-nodejs.md#troubleshooting)).

- Was ist, wenn ich private Erweiterungen verwende?
    - Deinstallieren Sie private Websiteerweiterungen, da diese nicht mehr unterstützt werden.

## <a name="release-notes"></a>Versionshinweise

### <a name="2842"></a>2.8.42

- JAVA-Erweiterung: Ein Upgrade zu [Java Agent 3.2.0](https://github.com/microsoft/ApplicationInsights-Java/releases/tag/3.2.0) von 2.5.1.
- Node.js-Erweiterung: Das AI SDK wurde von 2.1.7 auf [2.1.8](https://github.com/microsoft/ApplicationInsights-node.js/releases/tag/2.1.8) aktualisiert. Unterstützung für vom Benutzer und vom System zugewiesene AAD verwaltete Identitäten wurde hinzugefügt.
- .NET Core: Eigenständige Bereitstellungen und .NET 6.0-Unterstützung mit [.NET Startup Hook](https://github.com/dotnet/runtime/blob/main/docs/design/features/host-startup-hook.md) hinzugefügt.

### <a name="2841"></a>2.8.41

- Node.js-Erweiterung: Das AI SDK wurde von 2.1.3 auf [2.1.7](https://github.com/microsoft/ApplicationInsights-node.js/releases/tag/2.1.7) aktualisiert.
- .NET Core: Nicht mehr unterstützte Versionen (2.1) wurden entfernt. Unterstützt werden die Versionen 3.1 und 5.0.

### <a name="2840"></a>2.8.40

- Java-Erweiterung: Ein Upgrade von 3.0.2 auf [Java Agent 3.1.1 (GA)](https://github.com/microsoft/ApplicationInsights-Java/releases/tag/3.1.1) wurde durchgeführt.
- Node.js-Erweiterung: Das AI SDK wurde von 1.8.8 auf [2.1.3](https://github.com/microsoft/ApplicationInsights-node.js/releases/tag/2.1.3) aktualisiert.

### <a name="2839"></a>2.8.39

- Unterstützung für .NET Core 5.0 hinzugefügt.

### <a name="2838"></a>2.8.38

- Java-Erweiterung: Ein Upgrade von 2.5.1 auf [Java Agent 3.0.2 (GA)](https://github.com/microsoft/ApplicationInsights-Java/releases/tag/3.0.2) wurde durchgeführt.
- Node.js-Erweiterung: Das AI SDK wurde von 1.8.7 auf [1.8.8](https://github.com/microsoft/ApplicationInsights-node.js/releases/tag/1.8.8) aktualisiert.
- .NET Core: Nicht mehr unterstützte Versionen (2.0, 2.2, 3.0) wurden entfernt. Unterstützt werden die Versionen 2.1 und 3.1.

### <a name="2837"></a>2.8.37

- AppSvc Windows-Erweiterung: .NET Core kann nun mit jeder Version von „System.Diagnostics.DiagnosticSource.dll“ verwendet werden.

### <a name="2836"></a>2.8.36

- AppSvc Windows-Erweiterung: Interoperabilität mit AI SDK in .NET Core wurde ermöglicht.

### <a name="2835"></a>2.8.35

- AppSvc Windows-Erweiterung: Unterstützung für .NET Core 3.1 wurde hinzugefügt.

### <a name="2833"></a>2.8.33

- .NET-, .NET Core-, Java- und Node.js-Agent und die Windows-Erweiterung: Unterstützung für Sovereign Clouds. Verbindungszeichenfolgen können zum Senden von Daten an Sovereign Clouds verwendet werden.

### <a name="2831"></a>2.8.31

- ASP.NET Core-Agent: Ein Problem im Zusammenhang mit einem der Verweise des aktualisierten Application Insights-SDKs wurde behoben (siehe bekannte Probleme bei 2.8.26). Wenn die falsche Version von `System.Diagnostics.DiagnosticSource.dll` bereits von der Runtime geladen wurde, sorgt die codelose Erweiterung nun nicht mehr dafür, dass die Anwendung abstürzt, und bricht den Vorgang ab. Kunden, die von diesem Problem betroffen sind, wird empfohlen, die Datei `System.Diagnostics.DiagnosticSource.dll` aus dem Ordner „bin“ zu entfernen oder die ältere Version der Erweiterung zu verwenden, indem sie „ApplicationInsightsAgent_EXTENSIONVERSION=2.8.24“ festlegen. Andernfalls ist die Anwendungsüberwachung nicht aktiviert.

### <a name="2826"></a>2.8.26

- ASP.NET Core-Agent: Ein Problem im Zusammenhang mit dem aktualisierten Application Insights-SDK wurde behoben. Der Agent versucht nicht, `AiHostingStartup` zu laden, wenn ApplicationInsights.dll bereits im Ordner „bin“ vorhanden ist. Dies löst Probleme im Zusammenhang mit der Reflexion über Assembly\<AiHostingStartup\>.GetTypes().
- Bekannte Probleme: Die Ausnahme `System.IO.FileLoadException: Could not load file or assembly 'System.Diagnostics.DiagnosticSource, Version=4.0.4.0, Culture=neutral, PublicKeyToken=cc7b13ffcd2ddd51'` (System.IO.FileLoadException: Die Datei oder Assembly „System.Diagnostics.DiagnosticSource, Version=4.0.4.0, Culture=neutral, PublicKeyToken=cc7b13ffcd2ddd51“ konnte nicht geladen werden.) kann ausgelöst werden, wenn eine andere Version von `DiagnosticSource`.dll geladen wird. Dies kann z. B. passieren, wenn `System.Diagnostics.DiagnosticSource.dll` im Ordner „publish“ vorhanden ist. Verwenden Sie zur Risikominderung die vorherige Version der Erweiterung, indem Sie die App-Einstellungen in App Service wie folgt festlegen: ApplicationInsightsAgent_EXTENSIONVERSION=2.8.24.

### <a name="2824"></a>2.8.24

- Neu paketierte Version von 2.8.21

### <a name="2823"></a>2.8.23

- Unterstützung für codelose Überwachung für ASP.NET Core 3.0 wurde hinzugefügt.
- Das ASP.NET Core-SDK wurde für die Runtimeversionen 2.1, 2.2 und 3.0 auf [2.8.0](https://github.com/microsoft/ApplicationInsights-aspnetcore/releases/tag/2.8.0) aktualisiert. .NET Core 2.0-Apps verwenden weiterhin Version 2.1.1 des SDKs.

### <a name="2814"></a>2.8.14

- Die Version des ASP.NET Core-SDK wurde für .NET Core 2.1- und .NET Core 2.2-Apps von 2.3.0 auf die neueste Version aktualisiert (2.6.1). .NET Core 2.0-Apps verwenden weiterhin Version 2.1.1 des SDKs.

### <a name="2812"></a>2.8.12

- Unterstützung für ASP.NET Core 2.2-Apps
- Ein Fehler in der ASP.NET Core-Erweiterung wurde behoben, der ein Einfügen des SDK verursacht hat, auch wenn die Anwendung bereits über das SDK verfügte. Bei 2.1- und 2.2-Apps bewirkt das Vorhandensein von ApplicationInsights.dll im Anwendungsordner nun, dass die Erweiterung den Vorgang abbricht. Bei 2.0-Apps bricht die Erweiterung den Vorgang nur dann ab, wenn ApplicationInsights durch Aufrufen von `UseApplicationInsights()` aktiviert wird.

- Das Problem einer unvollständigen HTML-Antwort für ASP.NET Core-Apps wurde dauerhaft behoben. Diese Problembehebung wurde nun auch auf .NET Core 2.2-Apps erweitert.

- Die Unterstützung der Deaktivierung von JavaScript-Einfügung für ASP.NET Core-Apps (`APPINSIGHTS_JAVASCRIPT_ENABLED=false appsetting`) wurde hinzugefügt. In ASP.NET Core ist JavaScript-Einfügung standardmäßig aktiviert, wenn sie nicht explizit deaktiviert wird. (Diese Standardeinstellung wird vorgenommen, um das aktuelle Verhalten beizubehalten.)

- Ein Fehler in der ASP.NET Core-Erweiterung wurde behoben, der ein Einfügen verursacht hat, auch wenn kein Instrumentierungsschlüssel vorhanden war.
- Ein Fehler in der SDK-Versionspräfixlogik wurde behoben, der zu einer falschen SDK-Version in den Telemetriedaten geführt hat.

- Ein SDK-Versionspräfix für ASP.NET Core-Apps wurde hinzugefügt, um zu ermitteln, wie die Telemetriedaten erfasst wurden.
- Ein Fehler auf der Seite „SCM – ApplicationInsights“ wurde behoben, sodass nun die Version der vorinstallierten Erweiterung korrekt angezeigt wird.

### <a name="2810"></a>2.8.10

- Das Problem einer unvollständigen HTML-Antwort für ASP.NET Core-Apps wurde behoben.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zum Konfigurieren der Überwachung für Azure App Service finden Sie in der [Dokumentation zur Anwendungsüberwachung für Azure App Service](azure-web-apps.md). 
