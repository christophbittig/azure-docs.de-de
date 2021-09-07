---
title: Verbindungszeichenfolgen in Azure Application Insights | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Verbindungszeichenfolgen verwenden.
ms.topic: conceptual
author: timothymothra
ms.author: tilee
ms.date: 01/17/2020
ms.custom: devx-track-js, devx-track-csharp
ms.reviewer: mbullwin
ms.openlocfilehash: 2b4a7ac88f92c830daecf943d6937946c4a89f82
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/16/2021
ms.locfileid: "114297443"
---
# <a name="connection-strings"></a>Verbindungszeichenfolgen

## <a name="overview"></a>Übersicht

Verbindungszeichenfolgen bieten Benutzern von Application Insight eine einzelne Konfigurationseinstellung, sodass nicht mehrere Proxyeinstellungen benötigt werden. Dies ist sehr hilfreich für Intranetwebserver sowie Sovereign Cloud- oder Hybrid Cloud-Umgebungen, aus denen Daten an den Überwachungsdienst gesendet werden sollen.

Die Schlüssel-Wert-Paare stellen eine einfache Möglichkeit dar, mit der Benutzer eine Präfix-Suffix-Kombination für jeden Application Insights-Dienst und jedes Application Insights-Produkt definieren können.

> [!IMPORTANT]
> Es wird nicht empfohlen, sowohl die Verbindungszeichenfolge als auch den Instrumentierungsschlüssel festzulegen. Wenn ein Benutzer beide festgelegt hat, hat die zuletzt festgelegte Einstellung Vorrang. 

> [!TIP]
> Es wird empfohlen, eher Verbindungszeichenfolgen als Instrumentierungsschlüssel zu verwenden.

## <a name="scenario-overview"></a>Übersicht über das Szenario 

Kundenszenarien, die die wichtigsten Auswirkungen veranschaulichen:

- Firewallausnahmen oder Proxyumleitungen 

    Wenn eine Überwachung für den Intranetwebserver erforderlich ist, mussten Kunden bei unseren früheren Lösungen ihrer Konfiguration einzelne Dienstendpunkte hinzufügen. Weitere Informationen finden Sie [hier](../faq.yml#can-i-monitor-an-intranet-web-server-). 
    Verbindungszeichenfolgen stellen eine bessere Alternative dar, da der gesamte Aufwand auf eine einzige Einstellung reduziert wird. Ein einfacher Präfix-Suffix-Zusatz ermöglicht das automatische Auffüllen und Umleiten aller Endpunkte an die richtigen Dienste. 

- Sovereign Cloud- oder Hybrid Cloud-Umgebungen

    Benutzer können Daten an eine definierte [Azure Government-Region](../../azure-government/compare-azure-government-global-azure.md#application-insights) senden.
    Mit Verbindungszeichenfolgen können Sie Endpunkteinstellungen für Ihre Intranetserver oder Hybrid Cloud-Einstellungen definieren. 

## <a name="getting-started"></a>Erste Schritte

### <a name="finding-my-connection-string"></a>Wie finde ich meine Verbindungszeichenfolge heraus?

Ihre Verbindungszeichenfolge wird auf dem Übersichtsblatt Ihrer Application Insights-Ressource angezeigt.

![Verbindungszeichenfolge auf Übersichtsblatt](media/overview-dashboard/overview-connection-string.png)

### <a name="schema"></a>Schema

#### <a name="max-length"></a>Max. Länge

Die Verbindung hat eine maximale unterstützte Länge von 4.096 Zeichen.

#### <a name="key-value-pairs"></a>Schlüssel-Wert-Paare

Die Verbindungszeichenfolge besteht aus einer Liste von Einstellungen, die als Schlüssel-Wert-Paare dargestellt werden, die durch Semikolons voneinander getrennt sind: `key1=value1;key2=value2;key3=value3`

#### <a name="syntax"></a>Syntax

- `InstrumentationKey` (Beispiel: 00000000-0000-0000-0000-000000000000) Die Verbindungszeichenfolge ist ein **erforderliches** Feld.
- `Authorization` (Beispiel: iKey) (Diese Einstellung ist optional, da derzeit nur die iKey-Autorisierung unterstützt wird.)
- `EndpointSuffix` (Beispiel: applicationinsights.azure.cn) Das Festlegen des Endpunktsuffixes weist das SDK an, mit welcher Azure-Cloud eine Verbindung hergestellt werden soll. Das SDK stellt den Rest des Endpunkts für die einzelnen Dienste zusammen.
- Explizite Endpunkte.
  Jeder Dienst kann in der Verbindungszeichenfolge explizit überschrieben werden.
   - `IngestionEndpoint` (Beispiel: `https://dc.applicationinsights.azure.com`)
   - `LiveEndpoint` (Beispiel: `https://live.applicationinsights.azure.com`)
   - `ProfilerEndpoint` (Beispiel: `https://profiler.monitor.azure.com`)
   - `SnapshotEndpoint` (Beispiel: `https://snapshot.monitor.azure.com`)

#### <a name="endpoint-schema"></a>Endpunktschema

`<prefix>.<suffix>`
- Präfix: Definiert einen Dienst. 
- Suffix: Definiert den allgemeinen Domänennamen.

##### <a name="valid-suffixes"></a>Gültige Suffixe

Im Folgenden finden Sie eine Liste gültiger Suffixe. 
- applicationinsights.azure.cn
- applicationinsights.us


Weitere Informationen finden Sie unter [Regionen, für die Endpunktänderungen erforderlich sind](./custom-endpoints.md#regions-that-require-endpoint-modification).


##### <a name="valid-prefixes"></a>Gültige Präfixe

- [Telemetriedatenerfassung:](./app-insights-overview.md) `dc`
- [Livemetriken:](./live-stream.md) `live`
- [Profiler:](./profiler-overview.md) `profiler`
- [Momentaufnahme:](./snapshot-debugger.md) `snapshot`



## <a name="connection-string-examples"></a>Beispiele für Verbindungszeichenfolgen


### <a name="minimal-valid-connection-string"></a>Minimal gültige Verbindungszeichenfolge

`InstrumentationKey=00000000-0000-0000-0000-000000000000;`

In diesem Beispiel wurde nur der Instrumentierungsschlüssel festgelegt.

- Das Autorisierungsschema ist standardmäßig „ikey“. 
- Instrumentierungsschlüssel: 00000000-0000-0000-0000-000000000000
- Die regionalen Dienst-URIs basieren auf den [SDK-Standardwerten](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/BASE/src/Microsoft.ApplicationInsights/Extensibility/Implementation/Endpoints/Constants.cs) und bieten eine Verbindung mit der öffentlichen globalen Azure-Cloud:
   - Erfassung: `https://dc.services.visualstudio.com/`
   - Livemetriken: `https://rt.services.visualstudio.com/`
   - Profiler: `https://profiler.monitor.azure.com/`
   - Debugger: `https://snapshot.monitor.azure.com/`



### <a name="connection-string-with-endpoint-suffix"></a>Verbindungszeichenfolge mit Endpunktsuffix

`InstrumentationKey=00000000-0000-0000-0000-000000000000;EndpointSuffix=ai.contoso.com;`

In diesem Beispiel gibt diese Verbindungszeichenfolge das Endpunktsuffix an, und das SDK erstellt die Dienstendpunkte.

- Das Autorisierungsschema ist standardmäßig „ikey“. 
- Instrumentierungsschlüssel: 00000000-0000-0000-0000-000000000000
- Die regionalen Dienst-URIs basieren auf dem bereitgestellten Endpunktsuffix: 
   - Erfassung: `https://dc.ai.contoso.com`
   - Livemetriken: `https://live.ai.contoso.com`
   - Profiler: `https://profiler.ai.contoso.com`
   - Debugger: `https://snapshot.ai.contoso.com`  



### <a name="connection-string-with-explicit-endpoint-overrides"></a>Verbindungszeichenfolge mit expliziten Endpunktüberschreibungen 

`InstrumentationKey=00000000-0000-0000-0000-000000000000;IngestionEndpoint=https://custom.com:111/;LiveEndpoint=https://custom.com:222/;ProfilerEndpoint=https://custom.com:333/;SnapshotEndpoint=https://custom.com:444/;`

In diesem Beispiel gibt diese Verbindungszeichenfolge explizite Überschreibungen für jeden Dienst an. Das SDK verwendet die genauen Endpunkte, die ohne Änderung bereitgestellt werden.

- Das Autorisierungsschema ist standardmäßig „ikey“. 
- Instrumentierungsschlüssel: 00000000-0000-0000-0000-000000000000
- Die regionalen Dienst-URIs basieren auf den expliziten Überschreibungswerten: 
   - Erfassung: `https://custom.com:111/`
   - Livemetriken: `https://custom.com:222/`
   - Profiler: `https://custom.com:333/`
   - Debugger: `https://custom.com:444/`  


## <a name="how-to-set-a-connection-string"></a>Festlegen einer Verbindungszeichenfolge

Verbindungszeichenfolgen werden in den folgenden SDK-Versionen unterstützt:
- .NET und .NET Core v2.12.0
- Java v2.5.1 und Java 3.0
- JavaScript v2.3.0
- NodeJS v1.5.0
- Python v1.0.0

Eine Verbindungszeichenfolge kann im Code, in einer Umgebungsvariable oder in einer Konfigurationsdatei festgelegt werden.



### <a name="environment-variable"></a>Umgebungsvariable

- Verbindungszeichenfolge: `APPLICATIONINSIGHTS_CONNECTION_STRING`

### <a name="code-samples"></a>Codebeispiele

# <a name="netnetcore"></a>[.NET/.NetCore](#tab/net)

Legen Sie die Eigenschaft [TelemetryConfiguration.ConnectionString](https://github.com/microsoft/ApplicationInsights-dotnet/blob/add45ceed35a817dc7202ec07d3df1672d1f610d/BASE/src/Microsoft.ApplicationInsights/Extensibility/TelemetryConfiguration.cs#L271-L274) oder [ApplicationInsightsServiceOptions.ConnectionString](https://github.com/microsoft/ApplicationInsights-dotnet/blob/81288f26921df1e8e713d31e7e9c2187ac9e6590/NETCORE/src/Shared/Extensions/ApplicationInsightsServiceOptions.cs#L66-L69) fest.

.NET (explizit festgelegt):
```csharp
var configuration = new TelemetryConfiguration
{
    ConnectionString = "InstrumentationKey=00000000-0000-0000-0000-000000000000;"
};
```

.NET (Konfigurationsdatei):

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
    <ConnectionString>InstrumentationKey=00000000-0000-0000-0000-000000000000</ConnectionString>
</ApplicationInsights>
```

.NET Core (explizit festgelegt):
```csharp
public void ConfigureServices(IServiceCollection services)
{
    var options = new ApplicationInsightsServiceOptions { ConnectionString = "InstrumentationKey=00000000-0000-0000-0000-000000000000;" };
    services.AddApplicationInsightsTelemetry(options: options);
}
```

NetCore (config.json): 

```json
{
  "ApplicationInsights": {
    "ConnectionString" : "InstrumentationKey=00000000-0000-0000-0000-000000000000;"
    }
  }
```


# <a name="java"></a>[Java](#tab/java)

Sie können die Verbindungszeichenfolge in der Konfigurationsdatei `applicationinsights.json` festlegen:

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000"
}
```

Weitere Informationen finden Sie unter [Konfigurieren der Verbindungszeichenfolge](./java-standalone-config.md#connection-string).

Für Application Insights Java 2.x können Sie die Verbindungszeichenfolge in der Konfigurationsdatei `ApplicationInsights.xml`festlegen:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
    <ConnectionString>InstrumentationKey=00000000-0000-0000-0000-000000000000</ConnectionString>
</ApplicationInsights>
```

# <a name="javascript"></a>[JavaScript](#tab/js)

Wichtig: JavaScript unterstützt die Verwendung von Umgebungsvariablen nicht.

Verwenden des Codeausschnitts:

Der aktuelle Codeausschnitt (unten aufgeführt) ist Version „5“. Die Version ist im Ausschnitt als „sv:"#"“ codiert, und die [aktuelle Version ist auch auf GitHub verfügbar](https://go.microsoft.com/fwlink/?linkid=2156318).

```html
<script type="text/javascript">
!function(T,l,y){var S=T.location,k="script",D="instrumentationKey",C="ingestionendpoint",I="disableExceptionTracking",E="ai.device.",b="toLowerCase",w="crossOrigin",N="POST",e="appInsightsSDK",t=y.name||"appInsights";(y.name||T[e])&&(T[e]=t);var n=T[t]||function(d){var g=!1,f=!1,m={initialize:!0,queue:[],sv:"5",version:2,config:d};function v(e,t){var n={},a="Browser";return n[E+"id"]=a[b](),n[E+"type"]=a,n["ai.operation.name"]=S&&S.pathname||"_unknown_",n["ai.internal.sdkVersion"]="javascript:snippet_"+(m.sv||m.version),{time:function(){var e=new Date;function t(e){var t=""+e;return 1===t.length&&(t="0"+t),t}return e.getUTCFullYear()+"-"+t(1+e.getUTCMonth())+"-"+t(e.getUTCDate())+"T"+t(e.getUTCHours())+":"+t(e.getUTCMinutes())+":"+t(e.getUTCSeconds())+"."+((e.getUTCMilliseconds()/1e3).toFixed(3)+"").slice(2,5)+"Z"}(),iKey:e,name:"Microsoft.ApplicationInsights."+e.replace(/-/g,"")+"."+t,sampleRate:100,tags:n,data:{baseData:{ver:2}}}}var h=d.url||y.src;if(h){function a(e){var t,n,a,i,r,o,s,c,u,p,l;g=!0,m.queue=[],f||(f=!0,t=h,s=function(){var e={},t=d.connectionString;if(t)for(var n=t.split(";"),a=0;a<n.length;a++){var i=n[a].split("=");2===i.length&&(e[i[0][b]()]=i[1])}if(!e[C]){var r=e.endpointsuffix,o=r?e.location:null;e[C]="https://"+(o?o+".":"")+"dc."+(r||"services.visualstudio.com")}return e}(),c=s[D]||d[D]||"",u=s[C],p=u?u+"/v2/track":d.endpointUrl,(l=[]).push((n="SDK LOAD Failure: Failed to load Application Insights SDK script (See stack for details)",a=t,i=p,(o=(r=v(c,"Exception")).data).baseType="ExceptionData",o.baseData.exceptions=[{typeName:"SDKLoadFailed",message:n.replace(/\./g,"-"),hasFullStack:!1,stack:n+"\nSnippet failed to load ["+a+"] -- Telemetry is disabled\nHelp Link: https://go.microsoft.com/fwlink/?linkid=2128109\nHost: "+(S&&S.pathname||"_unknown_")+"\nEndpoint: "+i,parsedStack:[]}],r)),l.push(function(e,t,n,a){var i=v(c,"Message"),r=i.data;r.baseType="MessageData";var o=r.baseData;return o.message='AI (Internal): 99 message:"'+("SDK LOAD Failure: Failed to load Application Insights SDK script (See stack for details) ("+n+")").replace(/\"/g,"")+'"',o.properties={endpoint:a},i}(0,0,t,p)),function(e,t){if(JSON){var n=T.fetch;if(n&&!y.useXhr)n(t,{method:N,body:JSON.stringify(e),mode:"cors"});else if(XMLHttpRequest){var a=new XMLHttpRequest;a.open(N,t),a.setRequestHeader("Content-type","application/json"),a.send(JSON.stringify(e))}}}(l,p))}function i(e,t){f||setTimeout(function(){!t&&m.core||a()},500)}var e=function(){var n=l.createElement(k);n.src=h;var e=y[w];return!e&&""!==e||"undefined"==n[w]||(n[w]=e),n.onload=i,n.onerror=a,n.onreadystatechange=function(e,t){"loaded"!==n.readyState&&"complete"!==n.readyState||i(0,t)},n}();y.ld<0?l.getElementsByTagName("head")[0].appendChild(e):setTimeout(function(){l.getElementsByTagName(k)[0].parentNode.appendChild(e)},y.ld||0)}try{m.cookie=l.cookie}catch(p){}function t(e){for(;e.length;)!function(t){m[t]=function(){var e=arguments;g||m.queue.push(function(){m[t].apply(m,e)})}}(e.pop())}var n="track",r="TrackPage",o="TrackEvent";t([n+"Event",n+"PageView",n+"Exception",n+"Trace",n+"DependencyData",n+"Metric",n+"PageViewPerformance","start"+r,"stop"+r,"start"+o,"stop"+o,"addTelemetryInitializer","setAuthenticatedUserContext","clearAuthenticatedUserContext","flush"]),m.SeverityLevel={Verbose:0,Information:1,Warning:2,Error:3,Critical:4};var s=(d.extensionConfig||{}).ApplicationInsightsAnalytics||{};if(!0!==d[I]&&!0!==s[I]){var c="onerror";t(["_"+c]);var u=T[c];T[c]=function(e,t,n,a,i){var r=u&&u(e,t,n,a,i);return!0!==r&&m["_"+c]({message:e,url:t,lineNumber:n,columnNumber:a,error:i}),r},d.autoExceptionInstrumented=!0}return m}(y.cfg);function a(){y.onInit&&y.onInit(n)}(T[t]=n).queue&&0===n.queue.length?(n.queue.push(a),n.trackPageView({})):a()}(window,document,{
src: "https://js.monitor.azure.com/scripts/b/ai.2.min.js", // The SDK URL Source
// name: "appInsights", // Global SDK Instance name defaults to "appInsights" when not supplied
// ld: 0, // Defines the load delay (in ms) before attempting to load the sdk. -1 = block page load and add to head. (default) = 0ms load after timeout,
// useXhr: 1, // Use XHR instead of fetch to report failures (if available),
crossOrigin: "anonymous", // When supplied this will add the provided value as the cross origin attribute on the script tag
// onInit: null, // Once the application insights instance has loaded and initialized this callback function will be called with 1 argument -- the sdk instance (DO NOT ADD anything to the sdk.queue -- As they won't get called)
cfg: { // Application Insights Configuration
  connectionString:"InstrumentationKey=00000000-0000-0000-0000-000000000000;"
}});
</script>
```

> [!NOTE]
> Für eine bessere Lesbarkeit und zur Reduzierung möglicher JavaScript-Fehler werden alle möglichen Konfigurationsoptionen im Codeausschnitt oben in einer eigenen Zeile aufgeführt. Wenn Sie den Wert einer kommentierten Zeile nicht ändern möchten, kann diese entfernt werden.

Manuelle Einrichtung:
```javascript
import { ApplicationInsights } from '@microsoft/applicationinsights-web'

const appInsights = new ApplicationInsights({ config: {
  connectionString: 'InstrumentationKey=00000000-0000-0000-0000-000000000000;'
  /* ...Other Configuration Options... */
} });
appInsights.loadAppInsights();
appInsights.trackPageView();
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

```javascript
const appInsights = require("applicationinsights");
appInsights.setup("InstrumentationKey=00000000-0000-0000-0000-000000000000;");
appInsights.start();
```

# <a name="python"></a>[Python](#tab/python)

Es wird Benutzern empfohlen, die Umgebungsvariable festzulegen.

So legen Sie die Verbindungszeichenfolge explizit fest

```python
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

tracer = Tracer(exporter=AzureExporter(connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000'), sampler=ProbabilitySampler(1.0))
```

---

## <a name="next-steps"></a>Nächste Schritte

Beginnen mit der Laufzeitmethode mit:

* [Virtueller Azure-Computer und Azure-VM-Skalierungsgruppe – Auf IIS gehostete Apps](./azure-vm-vmss-apps.md)
* [IIS-Server](./status-monitor-v2-overview.md)
* [Azure-Web-Apps](./azure-web-apps.md)

Beginnen mit der Entwicklungszeitmethode mit:

* [ASP.NET](./asp-net.md)
* [ASP.NET Core](./asp-net-core.md)
* [Java](./java-in-process-agent.md)
* [Node.js](./nodejs.md)
* [Python](./opencensus-python.md)