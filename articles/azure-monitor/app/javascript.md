---
title: Azure Application Insights für JavaScript-Web-Apps
description: Rufen Sie die Anzahl der Seitenaufrufe und Sitzungen, Webclientdaten und Single-Page-Anwendungen (SPA) ab, und verfolgen Sie Verwendungsmuster. Erkennen Sie Ausnahmen und Leistungsprobleme in JavaScript-Web-Apps.
ms.topic: conceptual
ms.date: 08/06/2020
ms.custom: devx-track-js
ms.openlocfilehash: 4ce6481a35b8a214a2203a165161183d384b4ea0
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128662170"
---
# <a name="application-insights-for-web-pages"></a>Application Insights für Webseiten

Informieren Sie sich über die Leistung und Nutzung Ihrer Webseite oder App. Wenn Sie [Application Insights](app-insights-overview.md) Ihrem Seitenskript hinzufügen, erhalten Sie Zeitangaben zu Seitenladevorgängen und AJAX-Aufrufen, Anzahl und Details von Browserausnahmen und AJAX-Fehlern sowie die Anzahl von Benutzern und Sitzungen. Diese Informationen können jeweils nach Seite, Clientbetriebssystem und Browserversion, geografischer Position und anderen Dimensionen segmentiert werden. Sie können Warnungen für die Fehleranzahl oder das langsame Laden von Seiten festlegen. Und indem Sie Ablaufverfolgungsaufrufe in JavaScript-Code einfügen, können Sie nachverfolgen, wie die verschiedenen Funktionen Ihre Webseitenanwendung genutzt werden.

Application Insights kann mit allen Webseiten verwendet werden. Hierfür müssen Sie lediglich einen kurzen JavaScript-Codeabschnitt hinzufügen. Wenn Ihr Webdienst [Java](java-in-process-agent.md) oder [ASP.NET](asp-net.md) ist, können Sie die serverseitigen SDKs zusammen mit dem clientseitigen JavaScript SDK verwenden, um die Leistung Ihrer App vollständig zu verstehen.

## <a name="adding-the-javascript-sdk"></a>Hinzufügen des JavaScript SDK

> [!IMPORTANT]
> [Verbindungszeichenfolgen](./sdk-connection-string.md?tabs=js) sind Instrumentierungsschlüsseln vorzuziehen. Neue Azure-Regionen **erfordern** die Verwendung von Verbindungszeichenfolgen anstelle von Instrumentierungsschlüsseln. Die Verbindungszeichenfolge identifiziert die Ressource, der Sie Ihre Telemetriedaten zuordnen möchten. Hier können Sie die Endpunkte ändern, die Ihre Ressource als Ziel für die Telemetrie verwendet. Sie müssen die Verbindungszeichenfolge kopieren und dem Code Ihrer Anwendung oder einer Umgebungsvariable hinzufügen.

1. Zuerst benötigen Sie eine Application Insights-Ressource. Wenn Sie noch keine Ressource und keinen Instrumentierungsschlüssel haben, folgen Sie den Anweisungen unter [Erstellen einer neuen Ressource](create-new-resource.md).
2. Kopieren Sie den _Instrumentierungsschlüssel_ (auch „iKey“ genannt) oder die [Verbindungszeichenfolge](#connection-string-setup) der Ressource, an die Ihre JavaScript-Telemetriedaten (aus Schritt 1) gesendet werden sollen. Sie fügen sie der Einstellung `instrumentationKey` oder `connectionString` des Application Insights JavaScript SDK hinzu.
3. Fügen Sie die Application Insights JavaScript SDK Ihrer Webseite oder App über eine der beiden folgenden Optionen hinzu:
    * [npm-Setup](#npm-based-setup)
    * [JavaScript-Codeausschnitt](#snippet-based-setup)

> [!IMPORTANT]
> Verwenden Sie nur eine Methode, um Ihrer Anwendung das JavaScript SDK hinzuzufügen. Wenn Sie das NPM-Setup verwenden, verwenden Sie nicht den Codeausschnitt und umgekehrt.

> [!NOTE]
> Beim NPM-Setup wird das JavaScript SDK als eine Abhängigkeit in Ihrem Projekt installiert, wodurch IntelliSense aktiviert wird, während mit dem Codeausschnitt das SDK zur Laufzeit abgerufen wird. Beide unterstützen die gleichen Funktionen. Entwickler, die weitere benutzerdefinierte Ereignisse und Konfigurationen wünschen, entscheiden sich in der Regel für das NPM-Setup, während sich Benutzer, die eine schnelle Aktivierung der standardmäßig verfügbaren Webanalyse wünschen, für den Codeausschnitt entscheiden.

### <a name="npm-based-setup"></a>npm-basiertes Setup

Führen Sie die Installation über NPM aus.

```sh
npm i --save @microsoft/applicationinsights-web
```

> [!Note]
> **Dieses Paket enthält Typisierungen**. Sie müssen daher **kein** separates Typisierungspaket installieren.
    
```js
import { ApplicationInsights } from '@microsoft/applicationinsights-web'

const appInsights = new ApplicationInsights({ config: {
  instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE'
  /* ...Other Configuration Options... */
} });
appInsights.loadAppInsights();
appInsights.trackPageView(); // Manually call trackPageView to establish the current user/session/pageview
```

### <a name="snippet-based-setup"></a>Ausschnittbasiertes Setup

Wenn Ihre App npm nicht verwendet, können Sie Ihre Webseiten mit Application Insights direkt instrumentieren, indem Sie diesen Codeausschnitt am Anfang jeder Ihrer Seiten einfügen. Vorzugsweise sollte es das erste Skript in Ihrem `<head>`-Abschnitt sein, damit potenzielle Probleme mit allen Ihren Abhängigkeiten und optional auch alle JavaScript-Fehler überwacht werden können. Wenn Sie die Blazor-Server-App verwenden, fügen Sie den Codeausschnitt am Anfang der Datei `_Host.cshtml` im Abschnitt `<head>` ein.

Ab Version 2.5.5 enthält das Seitenansichtsereignis das neue Tag „ai.internal.snippet“, das wiederum die erkannte Version des Ausschnitts enthält. So können Sie leichter nachverfolgen, welche Version des Ausschnitts von Ihrer App verwendet wird.

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
    instrumentationKey: "YOUR_INSTRUMENTATION_KEY_GOES_HERE"
    /* ...Other Configuration Options... */
}});
</script>
```

> [!NOTE]
> Für eine bessere Lesbarkeit und zur Reduzierung möglicher JavaScript-Fehler werden alle möglichen Konfigurationsoptionen im Codeausschnitt oben in einer eigenen Zeile aufgeführt. Wenn Sie den Wert einer kommentierten Zeile nicht ändern möchten, kann diese entfernt werden.


#### <a name="reporting-script-load-failures"></a>Melden von Skriptladefehlern

Mit dieser Version des Ausschnitts werden Fehler erkannt und gemeldet, die beim Laden des SDK aus dem CDN auftreten. Diese Fehler werden dem Azure Monitor-Portal (unter „Fehler“ &gt; „Ausnahmen“ &gt; „Browser“) als Ausnahme gemeldet. Diese Ausnahme bietet Ihnen Einblicke in die Fehler dieses Typs, damit Sie wissen, dass Ihre Anwendung Telemetriedaten (oder andere Ausnahmen) nicht wie erwartet meldet. Dieses Signal ist wichtig, denn es informiert Sie darüber, dass Sie aufgrund eines Fehlers beim Laden oder Initialisieren des SDK Telemetriedaten verlieren. Dies kann zu folgenden Problemen führen:
- Unzureichende Berichterstattung darüber, wie Benutzer Ihre Website verwenden (oder versuchen, dies zu tun)
- Fehlende Telemetriedaten dazu, wie Ihre Endbenutzer Ihre Website verwenden
- Fehlende JavaScript-Fehler, die unter Umständen verhindern, dass Ihre Endbenutzer Ihre Website erfolgreich nutzen können

Weitere Informationen zu dieser Ausnahme finden Sie unter [Behandlung von SDK-Ladefehlern für JavaScript-Web-Apps](javascript-sdk-load-failure.md).

Wenn dieser Fehler als Ausnahme an das Portal gemeldet wird, geschieht dies nicht mithilfe der Konfigurationsoption ```disableExceptionTracking``` aus der Application Insights-Konfiguration. Tritt dieser Fehler auf, wird er immer vom Codeausschnitt gemeldet, selbst wenn die Unterstützung für „window.onerror“ deaktiviert ist.

Die Berichterstattung über SDK-Ladefehler wird von Internet Explorer 8 (oder niedriger) NICHT unterstützt. Dies trägt dazu bei, die heruntergesetzte Größe des Ausschnitts zu reduzieren, indem davon ausgegangen wird, dass in den meisten Umgebungen nicht ausschließlich Internet Explorer 8 oder niedriger eingesetzt wird. Wenn diese Anforderung für Sie gilt und Sie diese Ausnahmen erhalten möchten, müssen Sie entweder einen fetch-Polyfill in den Code schreiben oder eine Version des Codeausschnitts erstellen, in der ```XDomainRequest``` anstelle von ```XMLHttpRequest``` eingesetzt wird. Es wird empfohlen, den [bereitgestellten Quellcodeausschnitt](https://github.com/microsoft/ApplicationInsights-JS/blob/master/AISKU/snippet/snippet.js) als Ausgangspunkt zu verwenden.

> [!NOTE]
> Wenn Sie eine vorherige Version des Ausschnitts nutzen, wird dringend empfohlen, ein Update auf die neueste Version auszuführen, damit Sie über diese bisher nicht gemeldeten Probleme informiert werden.

#### <a name="snippet-configuration-options"></a>Option für die Konfiguration des Ausschnitts

Alle Konfigurationsoptionen wurden an das Ende des Skripts verlagert, damit nicht aus Versehen JavaScript-Fehler eingeführt werden, die nicht nur einen Ladefehler des SDK hervorrufen, sondern auch die Berichterstattung über diesen Fehler deaktivieren.

Jede Konfigurationsoption ist im Codeausschnitt oben in einer eigenen Zeile aufgeführt. Wenn Sie den Standardwert einer als „[optional]“ gelisteten Konfigurationsoption nicht überschreiben möchten, können Sie die Zeile entfernen, um die Größe der zurückgegebenen Zeile zu verringern.

Folgende Konfigurationsoptionen stehen zur Verfügung:
 
| Name | type | BESCHREIBUNG
|------|------|----------------
| src | Zeichenfolge **[erforderlich]** | Die vollständige URL, von der das SDK geladen werden soll. Dieser Wert wird für das Attribut „src“ eines dynamisch hinzugefügten &lt;script /&gt;-Tags verwendet. Sie können die öffentliche CDN-Adresse oder eine privat gehostete nehmen.
| name | Zeichenfolge *[optional]* | Der globale Name des initialisierten SDK. Der Standardwert ist `appInsights`. ```window.appInsights``` ist daher ein Verweis auf die initialisierte Instanz. Hinweis: Wenn Sie einen Namenswert angeben oder es scheint, als sei eine frühere Instanz (über den globalen Namen „appInsightsSDK“) zugewiesen, wird dieser Namenswert ebenfalls im globalen Namespace als ```window.appInsightsSDK=<name value>``` definiert. Dies wird durch den Initialisierungscode des SDK erzwungen, um sicherzustellen, dass die richtigen Methoden für das Ausschnittsgerüst und den Proxy initialisiert und aktualisiert werden.
| ld | Nummer in ms *[optional]* | Gibt die Lastverzögerungsdauer an, die gewartet werden muss, bevor versucht werden kann, dass SDK zu laden. Der Standardwert sind 0 ms. Durch jeden negativen Wert wird dem &lt;head&gt;-Bereich der Seite umgehend ein Skripttag hinzugefügt. Das Skripttag blockiert das Seitenladeereignis, bis das Skript geladen ist (oder das Laden des Skripts fehlgeschlagen ist).
| useXhr | boolesch *[optional]* | Diese Einstellung wird nur für die Berichterstattung über SDK-Ladefehler verwendet. Bei der Berichterstattung wird zunächst auf die fetch()-Methode zurückgegriffen, falls diese verfügbar ist. Anschließend wird ein Fallback auf Xhr ausgeführt. Wenn Sie diesen Wert auf TRUE festlegen, wird schlicht die fetch-Überprüfung umgangen. Dieser Wert muss nur verwendet werden, wenn Ihre Anwendung in einer Umgebung ausgeführt wird, in der die fetch-Methode keine Fehlerereignisse senden kann.
| crossOrigin | Zeichenfolge *[optional]* | Wenn Sie diese Einstellung aufnehmen, enthält das Skripttag, das für den SDK-Download hinzugefügt wurde, das Attribut „crossOrigin“ mit diesem Zeichenfolgenwert. Ist diese Einstellung nicht konfiguriert (Standardeinstellung), wird kein crossOrigin-Attribut hinzugefügt. Es wird empfohlen, keinen Wert zu definieren (Standardeinstellung) oder die Werte "" oder "anonymous" (anonym) zu verwenden. (Eine vollständige Liste der zulässigen Werte finden Sie in der [Dokumentation zum HTML-Attribut `crossorigin`](https://developer.mozilla.org/en-US/docs/Web/HTML/Attributes/crossorigin))
| cfg | Objekt **[erforderlich]** | Die bei der Initialisierung an das Application Insights SDK übergebene Konfiguration.

### <a name="connection-string-setup"></a>Verbindungszeichenfolgen-Setup

Für das NPM- oder Ausschnittsetup können Sie auch die Instanz von Application Insights mithilfe einer Verbindungszeichenfolge konfigurieren. Ersetzen Sie einfach das Feld `instrumentationKey` durch das Feld `connectionString`.
```js
import { ApplicationInsights } from '@microsoft/applicationinsights-web'

const appInsights = new ApplicationInsights({ config: {
  connectionString: 'YOUR_CONNECTION_STRING_GOES_HERE'
  /* ...Other Configuration Options... */
} });
appInsights.loadAppInsights();
appInsights.trackPageView();
```

### <a name="sending-telemetry-to-the-azure-portal"></a>Senden von Telemetriedaten an das Azure-Portal

Standardmäßig sammelt das Application Insights JavaScript SDK automatisch eine Reihe von Telemetrieelementen, die bei der Ermittlung der Integrität Ihrer Anwendung und der zugrunde liegenden Benutzeroberfläche hilfreich sind. Dazu gehören:

- **Nicht abgefangene Ausnahmen** in Ihrer App, einschließlich Informationen zu
    - Stapelüberwachung
    - Ausnahmedetails und Meldung, die den Fehler begleitet
    - Zeilen- und Spaltennummer des Fehlers
    - URL, bei der der Fehler ausgelöst wurde
- **Anforderungen an die Netzwerkabhängigkeit**, die von den **XHR** und **Fetch**-Anforderungen Ihrer App ausgegeben werden (die Abrufsammlung ist standardmäßig deaktiviert), einschließlich Informationen zu
    - URL der Abhängigkeitsquelle
    - Befehl und Methode, der bzw. die zum Anfordern der Abhängigkeit verwendet wird
    - Dauer der Anforderung
    - Ergebniscode und Erfolgsstatus der Anforderung
    - ID (sofern vorhanden) des Benutzers, der die Anforderung sendet
    - Korrelationskontext (falls vorhanden), in dem die Anforderung ausgegeben wird
- **Benutzerinformationen** (z.B. Speicherort, Netzwerk, IP)
- **Geräteinformationen** (z. B. Browser, Betriebssystem, Version, Sprache, Modell)
- **Sitzungsinformationen**

### <a name="telemetry-initializers"></a>Telemetrieinitialisierer
Telemetrieinitialisierer werden verwendet, um den Inhalt der gesammelten Telemetriedaten zu ändern, bevor sie vom Browser des Benutzers gesendet werden. Sie können auch verwendet werden, um zu verhindern, dass bestimmte Telemetriedaten gesendet werden, indem sie `false` zurückgeben. Ihrer Application Insights-Instanz können mehrere Telemetrieinitialisierer hinzugefügt werden, und sie werden ausgeführt, um sie hinzuzufügen.

Das Eingabeargument für `addTelemetryInitializer` ist ein Rückruf, der ein [`ITelemetryItem`](https://github.com/microsoft/ApplicationInsights-JS/blob/master/API-reference.md#addTelemetryInitializer) als Argument verwendet und ein `boolean` oder `void`zurückgibt. Bei Rückgabe von `false` wird das Telemetrieelement nicht gesendet. Andernfalls fährt es mit dem nächsten Telemetrieinitialisierer (falls vorhanden) fort, oder es wird an den Endpunkt der Telemetriedatensammlung gesendet.

Ein Beispiel für die Verwendung von Telemetrieinitialisierern:
```ts
var telemetryInitializer = (envelope) => {
  envelope.data.someField = 'This item passed through my telemetry initializer';
};
appInsights.addTelemetryInitializer(telemetryInitializer);
appInsights.trackTrace({message: 'This message will use a telemetry initializer'});

appInsights.addTelemetryInitializer(() => false); // Nothing is sent after this is executed
appInsights.trackTrace({message: 'this message will not be sent'}); // Not sent
```

## <a name="configuration"></a>Konfiguration
Die meisten Konfigurationsfelder werden so benannt, dass sie standardmäßig auf „false“ festgelegt werden können. Alle Felder mit Ausnahme von `instrumentationKey` sind optional.

| Name | Beschreibung | Standard |
|------|-------------|---------|
| instrumentationKey | **Erforderlich**<br>Instrumentierungsschlüssel, den Sie aus dem Azure-Portal abgerufen haben. | Zeichenfolge<br/>NULL |
| accountId | Eine optionale Konto-ID, wenn Ihre App Benutzer in Konten gruppiert. Keine Leerzeichen, Kommas, Semikolons, Gleichheitszeichen oder senkrechten Striche | Zeichenfolge<br/>NULL |
| sessionRenewalMs | Eine Sitzung wird protokolliert, wenn der Benutzer während dieser Zeitspanne (in Millisekunden) inaktiv ist. | NUMERIC<br/>1800000<br/>(30 Minuten) |
| sessionExpirationMs | Eine Sitzung wird protokolliert, wenn sie während dieser Zeitspanne (in Millisekunden) fortgesetzt wurde. | NUMERIC<br/>86400000<br/>(24-Stunden) |
| maxBatchSizeInBytes | Maximale Größe des Telemetriedatenbatches. Wenn ein Batch diesen Grenzwert überschreitet, wird er sofort gesendet, und ein neuer Batch wird gestartet. | NUMERIC<br/>10000 |
| maxBatchInterval | Dauer der Batchverarbeitung von Telemetriedaten vor dem Senden (Millisekunden) | NUMERIC<br/>15000 |
| disable&#8203;ExceptionTracking | Bei „true“ werden Ausnahmen nicht automatisch gesammelt. | boolean<br/> false |
| disableTelemetry | Bei „true“ werden Telemetriedaten nicht gesammelt oder gesendet. | boolean<br/>false |
| enableDebug | Bei „true“ werden **interne** Debugdaten als eine Ausnahme ausgelöst **statt** protokolliert zu werden. Dies geschieht unabhängig von den SDK-Protokollierungseinstellungen. Der Standardwert ist "false". <br>**_Hinweis:_** Wenn Sie diese Einstellung aktivieren, werden die Telemetriedaten beim Auftreten eines internen Fehlers verworfen. Dies kann hilfreich sein, um Probleme mit Ihrer Konfiguration oder der Nutzung des SDK schnell zu identifizieren. Wenn beim Debuggen keine Telemetriedaten verloren gehen sollen, empfiehlt es sich, `consoleLoggingLevel` oder `telemetryLoggingLevel` statt `enableDebug` zu verwenden. | boolean<br/>false |
| loggingLevelConsole | Protokolliert **interne** Application Insights-Fehler in der Konsole. <br>0: aus, <br>1: Nur schwerwiegende Fehler, <br>2: Alles (Fehler und Warnungen) | NUMERIC<br/> 0 |
| loggingLevelTelemetry | Sendet **interne** Application Insights-Fehler als Telemetriedaten. <br>0: aus, <br>1: Nur schwerwiegende Fehler, <br>2: Alles (Fehler und Warnungen) | NUMERIC<br/> 1 |
| diagnosticLogInterval | (intern) Abrufintervall (in ms) für interne Protokollierungswarteschlange | NUMERIC<br/> 10000 |
| samplingPercentage | Prozentsatz der Ereignisse, die gesendet werden. Die Standardeinstellung ist „100“. Dies bedeutet, dass alle Ereignisse gesendet werden. Legen Sie diesen Wert fest, wenn Sie Ihre Datenobergrenze für umfangreiche Anwendungen beibehalten möchten. | NUMERIC<br/>100 |
| autoTrackPageVisitTime | Bei „true“ wird in einem Seitenaufruf die Ansichtszeit der _vorherigen_ instrumentierten Seite nachverfolgt und in Form von Telemetriedaten gesendet. Außerdem wird ein neuer Zeitgeber für den aktuellen Seitenaufruf gestartet. Diese Daten werden als benutzerdefinierte Metrik mit dem Namen `PageVisitTime` in `milliseconds` gesendet, und für die Berechnung wird die Funktion [Date.now()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date/now) verwendet (falls verfügbar). Falls „now()“ nicht verfügbar ist (IE8 oder früher), wird [new Date().getTime()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date/getTime) verwendet. Der Standardwert ist "false". | boolean<br/>false |
| disableAjaxTracking | Bei „true“ werden AJAX-Aufrufe nicht automatisch gesammelt. | boolean<br/> false |
| disableFetchTracking | Bei „true“ werden Anforderungen zum Abrufen nicht automatisch gesammelt.|boolean<br/>true |
| overridePageViewDuration | Bei „true“ wird das Standardverhalten von „trackPageView“ geändert, um bei dessen Aufruf das Ende des Intervalls für die Seitenaufrufdauer aufzuzeichnen. Bei „false“ ohne Angabe eines benutzerdefinierten Zeitraums für „trackPageView“ wird die Seitenaufrufleistung mithilfe der Navigationszeit-API berechnet. |boolean<br/>
| maxAjaxCallsPerView | Mit der Standardeinstellung „500“ wird gesteuert, wie viele Ajax-Aufrufe pro Seitenaufruf überwacht werden. Legen Sie „–1“ fest, wenn alle (unbegrenzt) Ajax-Aufrufe auf der Seite überwacht werden sollen. | NUMERIC<br/> 500 |
| disableDataLossAnalysis | Bei „false“ werden interne Absenderpuffer für Telemetriedaten beim Start auf noch nicht gesendete Elemente überprüft. | boolean<br/> true |
| disable&#8203;CorrelationHeaders | Bei „false“ fügt das SDK allen Abhängigkeitsanforderungen zwei Kopfzeilen (‚Request-Id‘ und ‚Request-Context‘) hinzu, um sie mit entsprechenden serverseitigen Anforderungen zu korrelieren. | boolean<br/> false |
| correlationHeader&#8203;ExcludedDomains | Korrelations-Header für bestimmte Domänen deaktivieren | string[]<br/>nicht definiert |
| correlationHeader&#8203;ExcludePatterns | Korrelationskopfzeilen mit regulären Ausdrücken deaktivieren | regex[]<br/>nicht definiert |
| correlationHeader&#8203;Domains | Korrelations-Header für bestimmte Domänen aktivieren | string[]<br/>nicht definiert |
| disableFlush&#8203;OnBeforeUnload | Bei „true“ wird die Methode „Flush“ (Leeren) beim Auslösen eines „onBeforeUnload“-Ereignisses nicht aufgerufen. | boolean<br/> false |
| enableSessionStorageBuffer | Bei „true“ wird der Puffer mit allen nicht gesendeten Telemetriedaten im Sitzungsspeicher gespeichert. Der Puffer wird beim Laden der Seite wiederhergestellt. | boolean<br />true |
| cookieCfg | Standardmäßig ist die Cokieverwendung aktiviert. weitere Standardeinstellungen finden Sie unter [icookiecfgconfig](#icookiemgrconfig)-Einstellungen. | [ICookieCfgConfig](#icookiemgrconfig)<br>(Seit 2.6.0)<br/>nicht definiert |
| ~~isCookieUseDisabled~~<br>disableCookiesUsage | Bei „true“ speichert oder liest das SDK keine Daten aus Cookies. Beachten Sie, dass dadurch die Benutzer- und Sitzungscookies deaktiviert werden und die Nutzungsblätter und Benutzeroberflächen unbrauchbar werden. isCookieUseDisable ist zugunsten von disableCookiesUsage veraltet, wenn beide vorhanden sind, hat disableCookiesUsage Vorrang.<br>(Seit v 2.6.0) Wenn `cookieCfg.enabled` außerdem definiert ist, hat es Vorrang vor diesen Werten. Die Cookieverwendung kann nach der Initialisierung über Core. getcookiemgr (). SetEnabled (true) wieder aktiviert werden. | Alias für [`cookieCfg.enabled`](#icookiemgrconfig)<br>false |
| cookieDomain | Benutzerdefinierte Cookiedomäne. Diese ist hilfreich, wenn Sie Application Insights-Cookies über untergeordnete Domänen hinweg freigeben möchten.<br>(Seit v2.6.0) Wenn `cookieCfg.domain` er definiert ist, hat er Vorrang vor diesem Wert. | alias für [`cookieCfg.domain`](#icookiemgrconfig)<br>NULL |
| cookiePath | Benutzerdefinierter Cookie-Pfad. Dies ist hilfreich, wenn Sie Application Insights-Cookies hinter einem Anwendungs-Gateway freigeben möchten.<br>Wenn `cookieCfg.path` definiert ist, hat es Vorrang vor diesem Wert. | alias für [`cookieCfg.path`](#icookiemgrconfig)<br>(Seit 2.6.0)<br/>NULL |
| isRetryDisabled | Bei „false“ wiederholen Sie den Vorgang für 206 (teilweise erfolgreich), 408 (Timeout), 429 (zu viele Anforderungen), 500 (interner Serverfehler), 503 (Dienst nicht verfügbar) und 0 (offline, nur wenn erkannt). | boolean<br/>false |
| isStorageUseDisabled | Bei „true“ speichert und liest das SDK keine Daten aus dem lokalen Speicher und dem Sitzungsspeicher. | boolean<br/> false |
| isBeaconApiDisabled | Bei „false“ sendet das SDK alle Telemetriedaten mithilfe der [Beacon-API](https://www.w3.org/TR/beacon). | boolean<br/>true |
| onunloadDisableBeacon | Wenn die Registerkarte geschlossen wird, sendet das SDK alle verbleibenden Telemetriedaten mit dem [Beacon API](https://www.w3.org/TR/beacon) | boolean<br/> false |
| sdkExtension | Legt den Erweiterungsnamen „sdk“ fest. Hierbei sind nur alphabetische Zeichen zulässig. Der Erweiterungsname wird dem Tag ‚ai.internal.sdkVersion‘ als Präfix hinzugefügt (z.B. ‚ext_javascript:2.0.0‘). | Zeichenfolge<br/> NULL |
| isBrowserLink&#8203;TrackingEnabled | Bei „true“ wird das SDK alle Anforderungen vom Typ [Browserverknüpfung](/aspnet/core/client-side/using-browserlink) nachverfolgen. | boolean<br/>false |
| appId | „AppId“ wird für die Korrelation zwischen AJAX-Abhängigkeiten verwendet, die clientseitig mit den serverseitigen Anforderungen erfolgt. Wenn die Beacon-API aktiviert ist, kann sie nicht automatisch verwendet werden. Sie kann aber in der Konfiguration manuell festgelegt werden. |Zeichenfolge<br/> NULL |
| enable&#8203;CorsCorrelation | Bei „true“ fügt das SDK allen CORS-Anforderungen zwei Kopfzeilen (‚Request-Id‘ und ‚Request-Context‘) hinzu, um ausgehende AJAX-Abhängigkeiten mit entsprechenden serverseitigen Anforderungen zu korrelieren. | boolean<br/>false |
| namePrefix | Ein optionaler Wert, der als „name“-Postfix für „localStorage“ und den Cookienamen verwendet wird. | Zeichenfolge<br/>nicht definiert |
| enable&#8203;AutoRoute&#8203;Tracking | Routenänderungen in Single-Page-Webanwendungen (Single Page Applications, SPA) automatisch nachverfolgen. Bei „true“ sendet jede Routenänderung einen neuen Seitenaufruf an Application Insights. Hashroutenänderungen (`example.com/foo#bar`) werden ebenfalls als neue Seitenaufrufe aufgezeichnet.| boolean<br/>false |
| enableRequest&#8203;HeaderTracking | Falls „true“ werden AJAX- und Fetch-Anfrage-Header nachverfolgt. | boolean<br/> false |
| enableResponse&#8203;HeaderTracking | Bei „true“ werden die Antwort-Header der AJAX- und Fetch-Anfrage verfolgt. | boolean<br/> false |
| distributedTracingMode | Legt den Modus für verteilte Ablaufverfolgung fest. Wenn der AI_AND_W3C-Modus oder der W3C-Modus festgelegt ist, werden Kontextheader für die W3C-Ablaufverfolgung (traceparent/tracestate) generiert und in alle ausgehenden Anforderungen eingeschlossen. AI_AND_W3C wird für Abwärtskompatibilität mit beliebigen mit älteren Application Insights-Versionen instrumentierten Diensten bereitgestellt. Ein Beispiel finden Sie [hier](./correlation.md#enable-w3c-distributed-tracing-support-for-web-apps).| `DistributedTracingModes`oder<br/>NUMERIC<br/>(Seit v2.6.0) `DistributedTracingModes.AI_AND_W3C`<br />(v 2.5.11 oder früher) `DistributedTracingModes.AI` |
| enable&#8203;AjaxErrorStatusText | Bei TRUE wird der Text zu Antwortfehlerdaten im Abhängigkeitsereignis für fehlerhafte AJAX-Anforderungen aufgeführt. | boolean<br/> false |
| enable&#8203;AjaxPerfTracking |Flag, das die Suche nach und das Aufnehmen von zusätzlichen „window.performance“-Timings von Browsern in gemeldeten `ajax`-Metriken (XHR und fetch) ermöglicht. | boolean<br/> false |
| maxAjaxPerf&#8203;LookupAttempts | Die maximale Anzahl von Malen, die nach „window.performance“-Timings gesucht werden kann (falls vorhanden). Dies ist erforderlich, weil nicht alle Browser „window.performance“ befüllen, bevor das Ende der XHR-Anforderung gemeldet wird. Bei fetch-Anforderungen wird dies nach der Fertigstellung hinzugefügt.| NUMERIC<br/> 3 |
| ajaxPerfLookupDelay | Die Zeitdauer, die gewartet werden muss, bevor noch einmal versucht werden kann, die „windows.performance“-Timings für eine `ajax`-Anforderung zu suchen. Die Zeit wird in Millisekunden angegeben und direkt an setTimeout() übergeben. | NUMERIC<br/> 25 ms |
| enableUnhandled&#8203;PromiseRejection&#8203;Tracking | Bei TRUE werden unverarbeitete Ablehnungen von Zusagen automatisch erfasst und als JavaScript-Fehler gemeldet. Wenn disableExceptionTracking TRUE ist (Ausnahmen werden nicht nachverfolgt), wird der Konfigurationswert ignoriert, und unverarbeitete Ablehnungen von Zusagen werden nicht gemeldet. | boolean<br/> false |
| disable&#8203;InstrumentationKey&#8203;Validation | Bei „wahr“ wird die Validierungsprüfung des Instrumentierungsschlüssels umgangen. | boolean<br/>false |
| enablePerfMgr | Wenn dies aktiviert ist (true), werden lokale PerfEvents für Code erzeugt, der so instrumentiert wurde, dass er PerfEvents ausgibt (über den doPerf()-Helper). Dies kann verwendet werden, um Performance-Probleme innerhalb des SDKs basierend auf Ihrer Nutzung oder optional innerhalb Ihres eigenen instrumentierten Codes zu identifizieren. [Weitere Informationen finden Sie in der grundlegenden Dokumentation](https://github.com/microsoft/ApplicationInsights-JS/blob/master/docs/PerformanceMonitoring.md). Seit v2.5.7 | boolean<br/>false |
| perfEvtsSendAll | Wenn _enableperfmgr_ aktiviert ist und [iperfmanager](https://github.com/microsoft/ApplicationInsights-JS/blob/master/shared/AppInsightsCore/src/JavaScriptSDK.Interfaces/IPerfManager.ts) ein [inotificationmanager](https://github.com/microsoft/ApplicationInsights-JS/blob/master/shared/AppInsightsCore/src/JavaScriptSDK.Interfaces/INotificationManager.ts). perfevent () auslöst, bestimmt dieses Flag, ob ein Ereignis ausgelöst (und an alle Listener) für alle Ereignisse (true) oder nur für übergeordnete Ereignisse (false &lt; Standard) gesendet wird &gt; .<br />Bei einem übergeordneten [iperfevent](https://github.com/microsoft/ApplicationInsights-JS/blob/master/shared/AppInsightsCore/src/JavaScriptSDK.Interfaces/IPerfEvent.ts) -Element handelt es sich um ein Ereignis, bei dem noch kein anderes iperfevent zum Zeitpunkt der Erstellung dieses Ereignisses ausgeführt wird und die über _geordnete_ Eigenschaft nicht NULL oder nicht definiert ist. Seit v2.5.7 |  boolean<br />false |
| idLength | Gibt die Standardlänge an, die zum Erzeugen neuer zufälliger Sitzungs- und Benutzer-ID-Werte verwendet wird. Der Standardwert ist 22, der vorherige Standardwert war 5 (v2.5.8 oder weniger), wenn Sie die vorherige maximale Länge beibehalten wollen, sollten Sie diesen Wert auf 5 setzen. |  NUMERIC<br />22 |

## <a name="cookie-handling"></a>Handhabung clientseitiger Cookies

Ab Version 2.6.0 ist die Cookie-Verwaltung nun direkt in der Instanz verfügbar und kann nach der Initialisierung deaktiviert und wieder aktiviert werden.

Wenn Sie während der Initialisierung über `disableCookiesUsage` die `cookieCfg.enabled` Konfigurationen oder deaktiviert sind, können Sie die Funktion jetzt über die [icookiemgr](https://github.com/microsoft/ApplicationInsights-JS/blob/master/shared/AppInsightsCore/src/JavaScriptSDK.Interfaces/ICookieMgr.ts) -Funktion wieder aktivieren `setEnabled`.

Die instanzbasierte Cookieverwaltung ersetzt auch die vorherigen globalen CcoreUtils `disableCookies()`-Funktionen von`setCookie(...)` `getCookie(...)` und `deleteCookie(...)`. Und um von den ebenfalls mit der Version 2.6.0 eingeführten Tree-Shaking-Verbesserungen zu profitieren, sollten Sie die globalen Funktionen nicht mehr verwenden.

### <a name="icookiemgrconfig"></a>ICookieMgrConfig

Cookie-Konfiguration für instanzbasierte Cookie-Verwaltung in Version 2.6.0 hinzugefügt.

| Name | BESCHREIBUNG | Typ und Voreinstellung |
|------|-------------|------------------|
| enabled | Ein Boolean, das angibt, ob die Verwendung von Cookies durch das SDK von der aktuellen Instanz aktiviert ist. False gibt an, dass die von dieser Konfiguration initialisierte SDK-Instanz keine Daten aus Cookies speichert oder liest. | boolean<br/> true |
| Domäne | Benutzerdefinierte Cookiedomäne. Diese ist hilfreich, wenn Sie Application Insights-Cookies über untergeordnete Domänen hinweg freigeben möchten. Wenn nicht angegeben, wird der Wert aus dem `cookieDomain` Stammwert verwendet. | Zeichenfolge<br/>NULL |
| path | Gibt den Pfad an, der für das Cookie verwendet werden soll. Wenn er nicht angegeben wird, wird ein beliebiger Wert aus dem Stammwert `cookiePath` verwendet. | Zeichenfolge <br/> / |
| getCookie | Funktion zum Abrufen des benannten Cookie-Werts, wenn nicht angegeben, wird das interne Cookie-Parsing / Caching verwendet. | `(name: string) => string` <br/> NULL |
| setCookie | Funktion zum Festlegen des benannten Cookies mit dem angegebenen Wert, das nur beim Hinzufügen oder Aktualisieren eines Cookies aufgerufen wird. | `(name: string, value: string) => void` <br/> NULL |
| delCookie | Funktion zum Löschen des benannten Cookies mit dem angegebenen Wert, getrennt von setCookie, um zu vermeiden, dass der Wert geparst werden muss, um festzustellen, ob das Cookie hinzugefügt oder entfernt wird. Wenn nicht angegeben, wird das interne Cookie-Parsing / Caching verwendet. | `(name: string, value: string) => void` <br/> NULL |

### <a name="simplified-usage-of-new-instance-cookie-manager"></a>Vereinfachte Verwendung des neuen Instanz-Cookie-Managers

- appInsights.[getCookieMgr()](https://github.com/microsoft/ApplicationInsights-JS/blob/master/shared/AppInsightsCore/src/JavaScriptSDK.Interfaces/ICookieMgr.ts).setEnabled(true/false);
- appInsights.[getCookieMgr()](https://github.com/microsoft/ApplicationInsights-JS/blob/master/shared/AppInsightsCore/src/JavaScriptSDK.Interfaces/ICookieMgr.ts).set("MyCookie", "the%20encoded%20value");
- appInsights.[getCookieMgr()](https://github.com/microsoft/ApplicationInsights-JS/blob/master/shared/AppInsightsCore/src/JavaScriptSDK.Interfaces/ICookieMgr.ts).get("MyCookie");
- appInsights.[getCookieMgr()](https://github.com/microsoft/ApplicationInsights-JS/blob/master/shared/AppInsightsCore/src/JavaScriptSDK.Interfaces/ICookieMgr.ts).del("MyCookie");

## <a name="enable-time-on-page-tracking"></a>Aktivieren der Nachverfolgung der Verweildauer auf der Seite

Wenn Sie `autoTrackPageVisitTime: true` festlegen, wird die Zeit nachverfolgt, die ein Benutzer auf jeder Seite verbringt. Bei jedem neuen Seitenaufruf wird die Zeitdauer, die der Benutzer auf der *vorherigen* Seite verbracht hat, als [benutzerdefinierte Metrik](../essentials/metrics-custom-overview.md) mit dem Namen `PageVisitTime`gesendet. Diese benutzerdefinierte Metrik kann im [Metrik-Explorer](../essentials/metrics-getting-started.md) als eine „protokollbasierte Metrik“ angezeigt werden.

## <a name="enable-correlation"></a>Aktivieren der Korrelation

Die Korrelation generiert und sendet Daten, die die verteilte Ablaufverfolgung, die [Anwendungsübersicht](../app/app-map.md), die [Ansicht der End-to-End-Transaktion](../app/app-map.md#go-to-details) und andere Diagnosetools unterstützen.

Das folgende Beispiel zeigt alle möglichen Konfigurationen, die zum Aktivieren der Korrelation erforderlich sind, und gibt Hinweise zu den einzelnen Szenarien:

```javascript
// excerpt of the config section of the JavaScript SDK snippet with correlation
// between client-side AJAX and server requests enabled.
cfg: { // Application Insights Configuration
    instrumentationKey: "YOUR_INSTRUMENTATION_KEY_GOES_HERE"
    disableFetchTracking: false,
    enableCorsCorrelation: true,
    enableRequestHeaderTracking: true,
    enableResponseHeaderTracking: true,
    correlationHeaderExcludedDomains: ['myapp.azurewebsites.net', '*.queue.core.windows.net']
    /* ...Other Configuration Options... */
}});
</script>

``` 

Wenn einer Ihrer Server von Drittanbietern, mit denen der Client kommuniziert, die Header `Request-Id` und `Request-Context` nicht akzeptieren kann und Sie die Konfiguration nicht aktualisieren können, müssen Sie sie über die Konfigurationseigenschaft `correlationHeaderExcludedDomains` in eine Ausschlussliste einfügen. Diese Eigenschaft unterstützt Platzhalter.

Auf Serverseite müssen Verbindungen mit diesen Headern akzeptiert werden können. Je nach serverseitiger Konfiguration von `Access-Control-Allow-Headers` ist es häufig erforderlich, die serverseitige Liste zu erweitern, indem `Request-Id` und `Request-Context` manuell hinzugefügt werden.

Access-Control-Allow-Headers: `Request-Id`, `Request-Context`, `<your header>`

> [!NOTE]
> Wenn Sie OpenTelemtry- oder Application Insights-SDKs verwenden, die 2020 oder später veröffentlicht wurden, empfiehlt es sich, [WC3 TraceContext](https://www.w3.org/TR/trace-context/) zu verwenden. Weitere Informationen finden Sie im [Konfigurationsleitfaden](../app/correlation.md#enable-w3c-distributed-tracing-support-for-web-apps).

## <a name="single-page-applications"></a>Single-Page-Webanwendungen

Standardmäßig verarbeitet dieses SDK **keine** statusbasierte Routenänderung, die in Single-Page-Webanwendungen erfolgt. Um das automatische Nachverfolgen von Routenänderungen für Ihre Single-Page-Webanwendung zu aktivieren, können Sie Ihrer Setupkonfiguration `enableAutoRouteTracking: true` hinzufügen.

Wir bieten derzeit ein separates [React-Plug-In](javascript-react-plugin.md) an, das Sie mit diesem SDK initialisieren können. Es wird auch die Nachverfolgung von Routenänderungen automatisch ausführen sowie weitere React-spezifische Telemetriedaten sammeln.
> [!NOTE]
> Verwenden Sie `enableAutoRouteTracking: true` nur, wenn Sie das React-Plug-In **nicht** verwenden. Beide können bei Routenänderungen neue Seitenaufrufe senden. Wenn beide aktiviert sind, werden möglicherweise doppelte Seitenaufrufe gesendet.

## <a name="extensions"></a>Erweiterungen

| Erweiterungen |
|---------------|
| [React](javascript-react-plugin.md)|
| [React Native](javascript-react-native-plugin.md)|
| [Angular](javascript-angular-plugin.md)|
| [Automatische Erfassung von Klickanalysen](javascript-click-analytics-plugin.md)|

## <a name="explore-browserclient-side-data"></a>Browser-/clientseitige Daten erkunden

Browser-/clientseitige Daten können angezeigt werden, indem Sie zu **Metriken** navigieren und einzelne Metriken hinzufügen, an denen Sie interessiert sind:

![Screenshot der Seite „Metriken“ in Application Insights mit grafischen Darstellungen von Metrikdaten für eine Webanwendung.](./media/javascript/page-view-load-time.png)

Sie können Ihre Daten auch aus dem JavaScript SDK über die Browseroberfläche im Portal anzeigen.

Wählen Sie **Browser** und dann **Fehler** oder **Leistung** aus.

![Screenshot der Seite „Browser“ in Application Insights, der zeigt, wie Sie den Metriken, die Sie für Ihre Webanwendung anzeigen können, Browserfehler oder Browserleistung hinzufügen können.](./media/javascript/browser.png)

### <a name="performance"></a>Leistung

![Screenshot der Seite „Leistung“ in Application Insights mit grafischen Darstellungen von Vorgangsmetriken für eine Webanwendung.](./media/javascript/performance-operations.png)

### <a name="dependencies"></a>Abhängigkeiten

![Screenshot der Seite „Leistung“ in Application Insights mit grafischen Darstellungen von Abhängigkeitsmetriken für eine Webanwendung.](./media/javascript/performance-dependencies.png)

### <a name="analytics"></a>Analytics

Wenn Sie Ihre vom JavaScript SDK gesammelten Telemetriedaten abfragen möchten, wählen Sie die Schaltfläche **In Protokollen anzeigen (Analytics)** aus. Wenn Sie eine `where`-Anweisung von `client_Type == "Browser"`hinzufügen, werden nur Daten aus dem JavaScript SDK angezeigt, und alle von anderen SDKs gesammelten serverseitigen Telemetriedaten werden ausgeschlossen.
 
```kusto
// average pageView duration by name
let timeGrain=5m;
let dataset=pageViews
// additional filters can be applied here
| where timestamp > ago(1d)
| where client_Type == "Browser" ;
// calculate average pageView duration for all pageViews
dataset
| summarize avg(duration) by bin(timestamp, timeGrain)
| extend pageView='Overall'
// render result in a chart
| render timechart
```

### <a name="source-map-support"></a>Unterstützung für die Quellzuordnungsdatei

Die Minimierung der minimierten Aufrufliste Ihrer Ausnahmetelemetriedaten kann im Azure-Portal nicht aufgehoben werden. Alle vorhandenen Integrationen im Bereich „Ausnahmedetails“ funktionieren aber bei der Aufrufliste, deren Minimierung gerade aufgehoben wurde.

#### <a name="link-to-blob-storage-account"></a>Verknüpfung mit Blob Storage-Konto

Sie können Ihre Application Insights-Ressource mit dem eigenen Azure Blob Storage-Container verknüpfen, um die Minimierung von Aufruflisten automatisch aufzuheben. Informationen zu den ersten Schritten finden Sie unter der [automatischen Unterstützung für Quellzuordnungen](./source-map-support.md).

### <a name="drag-and-drop"></a>Drag & Drop

1. Wählen Sie im Azure-Portal ein Element vom Typ „Ausnahmetelemetrie“ aus, um dessen „End-to-End-Transaktionsdetails“ anzuzeigen.
2. Identifizieren Sie, welche Quellzuordnungsdateien dieser Aufrufliste entsprechen. Die Quellzuordnungsdatei muss mit der Quelldatei eines Stapelrahmens übereinstimmen, die allerdings mit dem Suffix `.map` versehen ist.
3. Ziehen Sie die Sourcemaps per Drag & Drop in die Aufrufliste im Azure-Portal ![Animierte Darstellung des Drag & Drop-Vorgangs der Sourcemaps aus einem Buildordner in das Fenster der Aufrufliste im Azure-Portal](https://i.imgur.com/Efue9nU.gif).

### <a name="application-insights-web-basic"></a>Application Insights Web Basic

Wenn Sie es einfacher haben möchten, können Sie stattdessen die Basisversion von Application Insights installieren.
```
npm i --save @microsoft/applicationinsights-web-basic
```
Diese Version ist mit der minimalen Anzahl von Features und Funktionen ausgestattet und basiert darauf, dass Sie sie nach Bedarf erstellen. So führt sie beispielsweise keine automatische Sammlung durch (nicht abgefangene Ausnahmen, AJAX usw.). Weil die APIs zum Senden bestimmter Arten von Telemetriedaten, wie z.B. `trackTrace`, `trackException` usw., in dieser Version nicht enthalten sind, müssen Sie Ihren eigenen Wrapper bereitstellen. Die einzige verfügbare API ist `track`. Ein [Beispiel](https://github.com/Azure-Samples/applicationinsights-web-sample1/blob/master/testlightsku.html) finden Sie hier.

## <a name="examples"></a>Beispiele

Ausführbare Beispiele finden Sie unter den [Beispielen für das Application Insights JavaScript SDK](https://github.com/Azure-Samples?q=applicationinsights-js-demo).

## <a name="upgrading-from-the-old-version-of-application-insights"></a>Upgrade von der alten Application Insights-Version

Breaking Changes in der SDK V2-Version:
- Um bessere API-Signaturen zu ermöglichen, wurden einige API-Aufrufe, wie z. B. „trackPageView“ und „trackException“, aktualisiert. Eine Ausführung in Internet Explorer 8 und früheren Versionen des Browsers wird nicht unterstützt.
- Beim Umschlag für die Telemetriedaten gibt es infolge von Datenschemaaktualisierungen Feldname- und Strukturänderungen.
- `context.operation` wurde in `context.telemetryTrace` verschoben. Einige Felder wurden ebenfalls geändert (`operation.id` --> `telemetryTrace.traceID`).
  - Zum manuellen Aktualisieren der derzeitigen PageView-ID (z. B. in SPA-Apps) verwenden Sie `appInsights.properties.context.telemetryTrace.traceID = Microsoft.ApplicationInsights.Telemetry.Util.generateW3CId()`.
    > [!NOTE]
    > Damit die Ablaufverfolgungs-ID eindeutig bleibt, verwenden Sie dort, wo Sie zuvor `Util.newId()` verwendet haben, jetzt `Util.generateW3CId()`. Beide werden letztendlich zur Vorgangs-ID.

Wenn Sie das aktuelle Application Insights-PRODUKTIONS-SDK (1.0.20) verwenden und überprüfen möchten, ob das neue SDK in der Laufzeit funktioniert, aktualisieren Sie die URL abhängig von Ihrem aktuellen SDK-Ladeszenario.

- Download über CDN-Szenario: Aktualisieren Sie den zurzeit verwendeten Codeausschnitt so, dass er auf die folgende URL verweist:
   ```
   "https://js.monitor.azure.com/scripts/b/ai.2.min.js"
   ```

- npm-Szenario: Rufen Sie `downloadAndSetup` auf, um das vollständige ApplicationInsights-Skript aus dem CDN herunterzuladen und es mit dem Instrumentierungsschlüssel zu initialisieren:

   ```ts
   appInsights.downloadAndSetup({
     instrumentationKey: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx",
     url: "https://js.monitor.azure.com/scripts/b/ai.2.min.jss"
     });
   ```

Testen Sie es in der internen Umgebung, um zu überprüfen, ob die Überwachung der Telemetriedaten wie erwartet funktioniert. Wenn alles funktioniert, aktualisieren Sie Ihre API-Signaturen entsprechend der SDK V2-Version, und stellen Sie sie in Ihren Produktionsumgebungen bereit.

## <a name="sdk-performanceoverhead"></a>SDK-Leistung/Mehraufwand

Mit einer Größe von 36 KB (GZIP-Ordner) und einer Initialisierungsdauer von etwa 15 ms steigert Application Insights die Ladezeit Ihrer Website nur geringfügig. Wenn Sie den Codeausschnitt verwenden, werden minimale Komponenten der Bibliothek schnell geladen. In der Zwischenzeit wird das vollständige Skript im Hintergrund heruntergeladen.

Während des Downloads des Skripts aus dem CDN wird die gesamte Nachverfolgung Ihrer Seite in die Warteschlange eingereiht. Sobald die asynchrone Initialisierung des heruntergeladenen Skripts abgeschlossen ist, werden alle Ereignisse, die in die Warteschlange eingereiht wurden, nachverfolgt. Infolgedessen gehen während des gesamten Lebenszyklus Ihrer Seite keine Telemetriedaten verloren. Dieser Setupvorgang stellt für Ihre Seite ein nahtloses Analysesystem bereit, das für Ihre Benutzer unsichtbar ist.

> Zusammenfassung:
> - ![NPM-Version](https://badge.fury.io/js/%40microsoft%2Fapplicationinsights-web.svg)
> - ![Größe bei GZIP-Komprimierung](https://img.badgesize.io/https://js.monitor.azure.com/scripts/b/ai.2.min.js.svg?compression=gzip)
> - **15 ms** – Gesamtinitialisierungszeit
> - **Null** – Nachverfolgung während des Lebenszyklus der Seite hat gefehlt.

## <a name="browser-support"></a>Browserunterstützung

![Chrome](https://raw.githubusercontent.com/alrra/browser-logos/master/src/chrome/chrome_48x48.png) | ![Firefox](https://raw.githubusercontent.com/alrra/browser-logos/master/src/firefox/firefox_48x48.png) | ![IE](https://raw.githubusercontent.com/alrra/browser-logos/master/src/edge/edge_48x48.png) | ![Opera](https://raw.githubusercontent.com/alrra/browser-logos/master/src/opera/opera_48x48.png) | ![Safari](https://raw.githubusercontent.com/alrra/browser-logos/master/src/safari/safari_48x48.png)
--- | --- | --- | --- | --- |
Chrome (neueste Version) ✔ |  Firefox (neueste Version) ✔ | Internet Explorer ab Version 9 und Microsoft Edge ✔<br>Mit Internet Explorer 8 kompatibel | Opera (neueste Version) ✔ | Safari (neueste Version) ✔ |

## <a name="es3ie8-compatibility"></a>ES3-/IE8-Kompatibilität

Es gibt zahlreiche Benutzer, die nicht steuern können, welche Browser Ihre Kunden benutzen. Daher muss sichergestellt werden, dass dieses SDK weiterhin funktioniert und nicht die Ausführung von JavaScript unterbricht, wenn es von einem älteren Browser geladen wird. Obwohl die Unterstützung für IE8 und Browser älterer Generationen (ES3) idealerweise eingestellt werden sollte, gibt es zahlreiche große Kunden/Benutzer, die darauf angewiesen sind, dass Seiten weiterhin funktionieren. Außerdem können sie, wie bereits erwähnt, nicht steuern, welchen Browser Endbenutzer wählen.

Dies bedeutet NICHT, dass nur die kleinste Schnittmenge der gemeinsamen Features unterstützt wird. Es bedeutet lediglich, dass die ES3-Codekompatibilität beibehalten werden muss und dass neue Features als optional und so hinzugefügt werden müssen, dass die ES3-JavaScript-Analyse nicht unterbrochen wird.

[Weitere Informationen zur IE8-Unterstützung finden Sie auf GitHub.](https://github.com/Microsoft/ApplicationInsights-JS#es3ie8-compatibility)

## <a name="open-source-sdk"></a>Open Source SDK

Das Application Insights JavaScript SDK ist Open Source. Wenn Sie den Quellcode anzeigen oder zum Projekt beitragen möchten, besuchen Sie das [offizielle GitHub-Repository](https://github.com/Microsoft/ApplicationInsights-JS). 

Informationen zu den neuesten Updates und Fehlerbehebungen [finden Sie in den Versionshinweisen](./release-notes.md).

## <a name="next-steps"></a><a name="next"></a> Nächste Schritte
* [Nutzung nachverfolgen](usage-overview.md)
* [Benutzerdefinierte Ereignisse und Metriken](api-custom-events-metrics.md)
* [Erstellen-Messen-Lernen](usage-overview.md)
* [Behandlung von SDK-Ladefehlern für JavaScript-Web-Apps](javascript-sdk-load-failure.md)
