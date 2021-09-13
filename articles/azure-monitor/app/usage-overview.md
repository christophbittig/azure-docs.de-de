---
title: Verwendungsanalyse mit Application Insights | Azure Monitor
description: Verstehen Sie Ihre Benutzer und wie sie Ihre App verwenden.
ms.topic: conceptual
ms.date: 07/30/2021
ms.openlocfilehash: 67739c94afeb841450eb465fe5cd5259093e2e78
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122355021"
---
# <a name="usage-analysis-with-application-insights"></a>Verwendungsanalyse mit Application Insights

Welche Funktionen Ihrer Web- oder mobilen App sind die beliebtesten? Erreichen die Benutzer mit Ihrer App ihre Ziele? Brechen sie an bestimmten Stellen ab, und kehren sie später zurück?  Mit [Application Insights](./app-insights-overview.md) erhalten Sie wertvolle Erkenntnisse zur Nutzung Ihrer App durch die Benutzer. Bei jeder Aktualisierung Ihrer App können Sie beurteilen, wie gut sie für Benutzer funktioniert. Mit diesem Wissen können Sie datengesteuerte Entscheidungen über die nächsten Entwicklungszyklen treffen.


## <a name="send-telemetry-from-your-app"></a>Senden von Telemetriedaten aus der App

Sie erzielen optimale Ergebnisse, wenn Sie Application Insights im Servercode der App und in den Webseiten installieren. Die Client- und die Serverkomponente der App senden Telemetriedaten zur Analyse an das Azure-Portal.

1. **Servercode:** Installieren Sie das entsprechende Modul für die [ASP.NET](./asp-net.md)-, [Azure](./app-insights-overview.md)-, [Java](./java-in-process-agent.md)-, [Node.js](./nodejs.md)-App oder einen [anderen Typ](./platforms.md) von App.

    * *Sie möchten keinen Servercode installieren? [Erstellen Sie einfach eine Azure Application Insights-Ressource](./create-new-resource.md).*

2. **Webseitencode:** Fügen Sie Ihrer Webseite vor dem schließenden ``</head>``-Tag das folgende Skript hinzu. Ersetzen Sie den Instrumentierungsschlüssel durch den geeigneten Wert für Ihre Application Insights-Ressource:
    
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
      instrumentationKey:"INSTRUMENTATION_KEY"
    }});
    </script>
    ```

    Informationen zu komplexeren Konfigurationen für die Websiteüberwachung finden Sie im [Referenzartikel zum JavaScript SDK](./javascript.md).

3. **Code der mobilen App:** Verwenden Sie das App Center SDK, um Ereignisse aus Ihrer App zu erfassen, und senden Sie Kopien dieser Ereignisse zur Analyse an Application Insights. Eine entsprechende Anleitung finden Sie [hier](../app/mobile-center-quickstart.md).

4. **Abrufen von Telemetriedaten:** Führen Sie das Projekt einige Minuten lang im Debugmodus aus, und suchen Sie dann im Bereich „Übersicht“ von Application Insights nach Ergebnissen.

    Veröffentlichen Sie die App, um die Leistung der App zu überwachen und zu ermitteln, was die Benutzer mit Ihrer App tun.

## <a name="explore-usage-demographics-and-statistics"></a>Untersuchen von demografische Daten und Statistiken zur Nutzung
Ermitteln Sie, wann Personen Ihre App verwenden, für welche Seiten sie sich am meisten interessieren, wo sich die Benutzer befinden, welche Browser und Betriebssysteme sie verwenden. 

In Benutzer- und Sitzungsberichte werden Ihre Daten nach Seiten oder benutzerdefinierten Ereignissen gefiltert und nach Eigenschaften wie Speicherort, Umgebung und Seite segmentiert. Sie können auch eigene Filter hinzufügen.

:::image type="content" source="./media/usage-overview/users.png" alt-text="Screenshot der Registerkarte „Benutzer“ mit einem Balkendiagramm" lightbox="./media/usage-overview/users.png":::

Aus den Informationen auf der rechten Seite gehen interessante Muster im Datensatz hervor.  

* Der Bericht **Benutzer** zählt die eindeutigen Benutzer, die innerhalb der ausgewählten Zeiträume auf Ihre Seiten zugreifen. Für Web-Apps werden Benutzer mithilfe von Cookies gezählt. Wenn eine Person mit verschiedenen Browsern oder Clientcomputern auf Ihre Website zugreift oder ihre Cookies löscht, wird sie mehrfach gezählt.
* Der Bericht **Sitzungen** zählt die Benutzersitzungen, die auf Ihre Website zugreifen. Eine Sitzung ist ein Zeitraum der Aktivität eines Benutzers, der von einem Zeitraum der Inaktivität von mehr als eine halben Stunde beendet wird.

[Weitere Informationen zu den Tools für Benutzer, Sitzungen und Ereignisse](usage-segmentation.md)  

## <a name="retention---how-many-users-come-back"></a>Vermerkdauer – wie viele Benutzer kehren zurück?

Anhand der Vermerkdauer können Sie basierend auf Kohorten von Benutzern, die während eines bestimmten Zeitrahmens eine Businessaktion durchgeführt haben, nachvollziehen, wie oft Ihre Benutzer die App erneut verwenden. 

- Ermitteln, welche bestimmten Funktionen dazu führen, dass einige Benutzer häufiger zurückkehren als andere 
- Bilden von Hypothesen basierend auf echten Benutzerdaten 
- Bestimmen, ob die Vermerkdauer ein Problem in Ihrem Produkt darstellt 

:::image type="content" source="./media/usage-overview/retention.png" alt-text="Screenshot der Arbeitsmappe „Aufbewahrung“ mit Informationen darüber, wie oft Benutzer zur Verwendung ihrer App zurückkehren" lightbox="./media/usage-overview/retention.png":::

Die Vermerkdauer-Steuerelemente oben ermöglichen Ihnen das Definieren bestimmter Ereignisse und des Zeitbereichs für die Berechnen der Vermerkdauer. Das Diagramm in der Mitte bietet eine visuelle Darstellung des Prozentsatzes der gesamten Vermerkdauer nach dem angegebenen Zeitbereichs. Im Diagramm unten wird eine einzelne Vermerkdauer in einem bestimmten Zeitraum dargestellt. Dank dieses Detaillierungsgrads können Sie mit höherer Granularität verstehen, was Ihre Benutzer tun und was sich auf zurückkehrende Benutzer auswirkt.  

[Weitere Informationen zur Arbeitsmappe „Aufbewahrung“](usage-retention.md)

## <a name="custom-business-events"></a>Benutzerdefinierte Geschäftsereignisse

Um ein genaues Verständnis davon zu erhalten, was Benutzer mit Ihrer App machen, ist es hilfreich, Codezeilen einzufügen, um benutzerdefinierte Ereignisse zu protokollieren. Diese Ereignisse können alles nachverfolgen – von detaillierten Benutzeraktionen wie das Klicken auf bestimmte Schaltflächen bis hin zu aussagekräftigeren Geschäftsereignissen wie das Tätigen eines Kaufs oder das Gewinnen eines Spiels.

Sie können auch das [Plug-In für die automatische Erfassung von Klickanalysen](javascript-click-analytics-plugin.md) verwenden, um benutzerdefinierte Ereignisse zu erfassen.

Seitenaufrufe können zwar in einigen Fällen nützliche Ereignisse darstellen, im Allgemeinen ist dies jedoch nicht der Fall. Ein Benutzer kann eine Produktseite öffnen, ohne das Produkt zu kaufen. 

Mit bestimmten Geschäftsereignisse können Sie den Aufenthalt von Benutzern auf Ihrer Website in einem Diagramm darstellen. Sie können deren Einstellungen für unterschiedliche Optionen ermitteln und herausfinden, wo sie abbrechen oder Schwierigkeiten haben. Mit diesem Wissen können Sie fundierte Entscheidungen über die Prioritäten in Ihrem Entwicklungs-Backlog treffen.

Ereignisse können von der Clientseite der App aus protokolliert werden:

```JavaScript
    appInsights.trackEvent("ExpandDetailTab", {DetailTab: tabName});
```

Oder von der Serverseite:

```csharp
    var tc = new Microsoft.ApplicationInsights.TelemetryClient();
    tc.TrackEvent("CreatedAccount", new Dictionary<string,string> {"AccountType":account.Type}, null);
    ...
    tc.TrackEvent("AddedItemToCart", new Dictionary<string,string> {"Item":item.Name}, null);
    ...
    tc.TrackEvent("CompletedPurchase");
```

Sie können diesen Ereignissen Eigenschaftswerte anfügen, damit Sie die Ereignisse beim Überprüfen im Portal filtern oder teilen können. Jedem Ereignis wird ein Standardsatz von Eigenschaften angefügt, z. B. eine anonyme Benutzer-ID, die Ihnen die Ablaufverfolgung der Sequenz von Aktivitäten eines einzelnen Benutzers ermöglicht.

Erfahren Sie mehr über [benutzerdefinierte Ereignisse](./api-custom-events-metrics.md#trackevent) und [Eigenschaften](./api-custom-events-metrics.md#properties).

### <a name="slice-and-dice-events"></a>Aufteilen von Ereignissen

In den Tools für Benutzer, Sitzungen und Ereignisse können Sie benutzerdefinierte Ereignisse nach Benutzer, Ereignisname und Eigenschaften aufteilen.

:::image type="content" source="./media/usage-overview/events.png" alt-text="Screenshot der Registerkarte „Ereignisse“, gefiltert nach AnalyticsItemsOperation und unterteilt nach AppID" lightbox="./media/usage-overview/events.png":::
  
## <a name="design-the-telemetry-with-the-app"></a>Entwerfen der Telemetrie mit der App

Berücksichtigen Sie beim Entwerfen der einzelnen Funktionen Ihrer App, wie Sie deren Erfolg bei den Benutzern messen werden. Entscheiden Sie, welche Geschäftsereignisse aufgezeichnet werden müssen, und codieren Sie die Aufrufnachverfolgung für diese Ereignisse von Anfang an in die App.

## <a name="a--b-testing"></a>A-B-Tests
Wenn Sie nicht wissen, welche Variante eines Features erfolgreicher sein wird, veröffentlichten Sie beide, und ermöglichen Sie verschiedenen Benutzern den Zugriff darauf. Messen Sie den jeweiligen Erfolg, und erstellen Sie anschließend eine vereinheitlichte Version.

Für dieses Verfahren fügen Sie unterschiedliche Eigenschaftswerte an alle Telemetriedaten an, die von jeder Version Ihrer App gesendet wird. Dazu definieren Sie Eigenschaften im aktiven "TelemetryContext"-Element. Diese Standardeigenschaften werden jeder Telemetrienachricht hinzugefügt, die die Anwendung sendet, nicht nur Ihren benutzerdefinierten Nachrichten, sondern auch den Standardtelemetriedaten.

Filtern und teilen Sie im Application Insights-Portal Ihre Daten anhand der Eigenschaftswerte, um die verschiedenen Versionen zu vergleichen.

Dazu [richten Sie einen Telemetrieinitialisierer ein](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer):

**ASP.NET-Apps**

```csharp
    // Telemetry initializer class
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry item)
            {
                var itemProperties = item as ISupportProperties;
                if (itemProperties != null && !itemProperties.Properties.ContainsKey("AppVersion"))
                {
                    itemProperties.Properties["AppVersion"] = "v2.1";
                }
            }
    }
```

Im Web-App-Initialisierer wie „Global.asax.cs“:

```csharp

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers
         .Add(new MyTelemetryInitializer());
    }
```

**ASP.NET Core Apps**

> [!NOTE]
> Das Hinzufügen eines Initialisierers mit `ApplicationInsights.config` oder `TelemetryConfiguration.Active` ist für ASP.NET Core-Anwendungen nicht zulässig. 

Zum Hinzufügen eines neuen `TelemetryInitializer` für [ASP.NET Core](asp-net-core.md#adding-telemetryinitializers)-Anwendungen wird dieser wie unten gezeigt dem Abhängigkeitsinjektionscontainer hinzugefügt. Dies erfolgt in der `ConfigureServices`-Methode Ihrer `Startup.cs`-Klasse.

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyTelemetryInitializer>();
}
```

Alle neuen TelemetryClients-Elemente fügen automatisch den von Ihnen angegebenen Eigenschaftswert hinzu. Einzelne Telemetrieereignisse können die Standardwerte außer Kraft setzen.

## <a name="next-steps"></a>Nächste Schritte
   - [Benutzer, Sitzungen, Ereignisse](usage-segmentation.md)
   - [Trichter](usage-funnels.md)
   - [Vermerkdauer](usage-retention.md)
   - [Benutzerabläufe](usage-flows.md)
   - [Arbeitsmappen](../visualize/workbooks-overview.md)
