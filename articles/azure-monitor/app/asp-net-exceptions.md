---
title: Diagnostizieren von Fehlern und Ausnahmen mit Azure Application Insights
description: Erfassen von Ausnahmen von ASP.NET-Apps zusammen mit der Anforderungstelemetrie.
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 05/19/2021
ms.openlocfilehash: 5ad103890d481db18c3df9633bf3b58186136e93
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2021
ms.locfileid: "123431548"
---
# <a name="diagnose-exceptions-in-web-apps-with-application-insights"></a>Diagnostizieren von Ausnahmen in Web-Apps mit Application Insights

Ausnahmen in Webanwendungen können mit [Application Insights](./app-insights-overview.md) gemeldet werden. Auf diese Weise können Sie Anforderungsfehler mit Ausnahmen und anderen Ereignissen auf dem Client und auf dem Server zueinander in Beziehung setzen und dadurch die Ursachen schnell diagnostizieren. In diesem Artikel wird beispielsweise erläutert, wie Sie die Ausnahmeberichterstattung einrichten, Ausnahmen explizit melden und Fehler diagnostizieren.

## <a name="set-up-exception-reporting"></a>Einrichten der Ausnahmeerfassung

Sie können Application Insights so einrichten, dass Ausnahmen gemeldet werden, die entweder auf dem Server oder dem Client auftreten. Abhängig von der Plattform, von der Ihre Anwendung abhängig ist, benötigen Sie die entsprechende Erweiterung oder das entsprechende SDK.

### <a name="server-side"></a>Serverseitig

Berücksichtigen Sie die folgenden Szenarios, damit Ausnahmen Ihrer serverseitigen Anwendung gemeldet werden:

  * **Azure-Web-Apps:** Fügen Sie die [Application Insights-Erweiterung](./azure-web-apps.md) hinzu.
  * **Auf Azure-VMs oder über Azure-VM-Skalierungsgruppen ausgeführte, in IIS gehostete Apps:** Fügen Sie die [Erweiterung zur Anwendungsüberwachung](./azure-vm-vmss-apps.md) hinzu.
  * Installieren Sie das [Application Insights SDK](./asp-net.md) in Ihrer App.
  * **IIS-Webserver:** Führen Sie den [Application Insights-Agent](./status-monitor-v2-overview.md) aus.
  * **Java-Web-Apps:** Aktivieren Sie den [Java-Agent](./java-in-process-agent.md).

### <a name="client-side"></a>Clientseitig

Das JavaScript-SDK bietet die Möglichkeit zur clientseitigen Berichterstellung für Ausnahmen, die in Webbrowsern auftreten. Informationen zum Einrichten der Berichterstellung für Ausnahmen auf dem Client finden Sie unter [Application Insights für Webseiten](./javascript.md).

### <a name="application-frameworks"></a>Anwendungsframeworks

Bei einigen Anwendungsframeworks ist etwas mehr Konfiguration erforderlich. Berücksichtigen Sie dabei die folgenden Technologien:

  * [Webformulare](#web-forms)
  * [MVC](#mvc)
  * [Web-API 1.*](#web-api-1x)
  * [Web-API 2.*](#web-api-2x)
  * [WCF](#wcf)

> [!IMPORTANT]
> Dieser Artikel konzentriert sich speziell auf .NET Framework-Apps aus Codebeispielperspektive. Einige der Methoden, die für .NET Framework eingesetzt werden, sind im .NET Core SDK veraltet. Weitere Informationen zum Erstellen von Apps mit .NET Core finden Sie unter [Application Insights für ASP.NET Core-Anwendungen](./asp-net-core.md).

## <a name="diagnose-exceptions-using-visual-studio"></a>Diagnostizieren von Ausnahmen mithilfe von Visual Studio

Öffnen Sie in Visual Studio die Anwendungsprojektmappe. Führen Sie die App entweder auf dem Server oder Ihrem Entwicklungscomputer aus, indem Sie <kbd>F5</kbd> drücken. Erstellen Sie die Ausnahme neu.

Öffnen Sie in Visual Studio das Application Insights-Fenster **Telemetriedaten suchen**. Klicken Sie während des Debuggens auf das Dropdownmenü **Application Insights**.

![Klicken Sie mit der rechten Maustaste auf das Projekt, und wählen Sie „Application Insights > Öffnen“ aus.](./media/asp-net-exceptions/34.png)

Wählen Sie einen Ausnahmebericht aus, um dessen Stapelüberwachung anzuzeigen. Klicken Sie in der Stapelüberwachung auf einen Zeilenverweis, um die relevante Codedatei zu öffnen.

Wenn CodeLens aktiviert ist, werden Daten zu den Ausnahmen angezeigt:

![CodeLens-Benachrichtigung über Ausnahmen.](./media/asp-net-exceptions/35.png)

## <a name="diagnose-failures-using-the-azure-portal"></a>Diagnostizieren von Fehlern mithilfe des Azure-Portals

Application Insights umfasst eine kuratierte APM-Benutzeroberfläche (Application Performance Management), die Sie beim Diagnostizieren von Fehlern in Ihren überwachten Anwendungen unterstützt. Klicken Sie für den Start im Application Insights-Ressourcenmenü, das Sie im Abschnitt **Untersuchen** finden, auf die Option **Fehler**.
Daraufhin wird angezeigt, welche Fehlerratentrends es bei Ihren Anforderungen gibt, bei wie vielen ein Fehler auftritt und wie viele Benutzer davon betroffen sind. Über die Ansicht **Gesamt** sehen Sie einige der nützlichsten Verteilungen für den ausgewählten fehlerhaften Vorgang, einschließlich der Top-3-Antwortcodes, der Top-3-Ausnahmetypen und der Top-3-Abhängigkeitstypen mit Fehlern.

![Ansicht der Fehlerselektierung (Registerkarte mit Vorgängen)](./media/asp-net-exceptions/failures0719.png)

Klicken Sie auf den entsprechenden Link, um repräsentative Beispiele für jede dieser Teilmengen von Vorgängen zu überprüfen. Sie können zum Diagnostizieren von Ausnahmen beispielsweise die Anzahl einer bestimmten Ausnahme anklicken, die auf der Detailregisterkarte **End-to-End-Transaktion** angezeigt werden soll:

![Registerkarte mit ausführlichen Transaktionsdetails](./media/asp-net-exceptions/end-to-end.png)

Alternativ zur Anzeige von Ausnahmen eines bestimmten Vorgangs, bei dem ein Fehler aufgetreten ist, können Sie mit der Ansicht **Gesamt** für die Ausnahmen beginnen, indem Sie zur Registerkarte **Ausnahmen** im oberen Bereich wechseln. Hier können Sie alle Ausnahmen anzeigen, die für Ihre überwachte App erfasst wurden.

## <a name="custom-tracing-and-log-data"></a>Benutzerdefinierte Ablaufverfolgung und Protokolldaten

Um spezifische Diagnosedaten für Ihre App zu erhalten, können Sie Code zum Senden Ihrer eigenen Telemetriedaten einfügen. Ihre benutzerdefinierten Telemetrie- oder Protokolldaten werden in der Diagnosesuche zusammen mit der Anforderung, der Seitenansicht und anderen automatisch gesammelten Daten angezeigt.

Wenn Sie <xref:Microsoft.VisualStudio.ApplicationInsights.TelemetryClient?displayProperty=fullName> verwenden, sind mehrere APIs verfügbar:

* <xref:Microsoft.VisualStudio.ApplicationInsights.TelemetryClient.TrackEvent%2A?displayProperty=nameWithType> wird normalerweise zum Überwachen von Verwendungsmustern verwendet, jedoch werden die damit gesendeten Daten in der Diagnosesuche auch unter **Benutzerdefinierte Ereignisse** angezeigt. Ereignisse werden benannt und können Zeichenfolgeneigenschaften und numerische Metriken aufweisen, nach denen Sie [Ihre Diagnosesuchvorgänge filtern](./diagnostic-search.md) können.
* <xref:Microsoft.VisualStudio.ApplicationInsights.TelemetryClient.TrackTrace%2A?displayProperty=nameWithType> können Sie längere Daten wie POST-Informationen senden.
* <xref:Microsoft.VisualStudio.ApplicationInsights.TelemetryClient.TrackException%2A?displayProperty=nameWithType> sendet Ausnahmedetails wie Stapelüberwachungen an Application Insights.

Öffnen Sie zum Anzeigen der Ergebnisse im linken Menü die [Suche](./diagnostic-search.md), wählen Sie das Dropdownmenü **Ereignistypen** aus, und wählen Sie dann **Benutzerdefiniertes Ereignis**, **Ablaufverfolgung** oder **Ausnahme** aus.

![Drillthrough](./media/asp-net-exceptions/customevents.png)

> [!NOTE]
> Wenn die Anwendung viele Telemetriedaten generiert, reduziert das adaptive Stichprobenmodul automatisch die an das Verwaltungsportal gesendete Datenmenge, indem nur ein repräsentativer Bruchteil der Ereignisse gesendet wird. Ereignisse, die Teil des gleichen Vorgangs sind, werden als Gruppe aus- oder abgewählt, sodass Sie zwischen verwandten Ereignissen navigieren können. Weitere Informationen finden Sie unter [Erstellen von Stichproben in Application Insights](./sampling.md).

### <a name="how-to-see-request-post-data"></a>Anzeigen von POST-Daten von Anforderungen

Anforderungsdetails enthalten nicht die Daten, die in einem POST-Aufruf an Ihre App gesendet wurden. So werden diese Daten gemeldet:

* [Installieren Sie das SDK](./asp-net.md) in Ihrem Anwendungsprojekt.
* Fügen Sie Code in die Anwendung ein, um [Microsoft.ApplicationInsights.TrackTrace()](./api-custom-events-metrics.md#tracktrace) aufzurufen. Senden Sie die POST-Daten im "message"-Parameter. Es gibt eine Größenbeschränkung, daher sollten Sie versuchen, nur die notwendigen Daten zu senden.
* Wenn Sie eine fehlerhafte Anforderung untersuchen, suchen Sie die zugehörigen Ablaufverfolgungen.

## <a name="capturing-exceptions-and-related-diagnostic-data"></a><a name="exceptions"></a> Erfassen von Ausnahmen und zugehörigen Diagnosedaten
Zunächst werden im Portal nicht alle Ausnahmen angezeigt, die in Ihrer App zu Fehlern führen. Sie sehen alle Browserausnahmen (bei Verwendung des [JavaScript-SDK](./javascript.md) in Ihren Webseiten). Die meisten Serverausnahmen werden jedoch von IIS abgefangen, und Sie müssen ein wenig Code schreiben, um sie anzuzeigen.

Ihre Möglichkeiten:

* **Ausnahmen explizit protokollieren** durch Einfügen von Code in Ausnahmehandlern, um Ausnahmen zu melden.
* **Ausnahmen automatisch erfassen** durch Konfigurieren Ihres ASP.NET-Frameworks. Die erforderlichen Änderungen unterscheiden sich für verschiedene Frameworktypen.

## <a name="reporting-exceptions-explicitly"></a>Explizites Melden von Ausnahmen

Am einfachsten ist es, in einem Ausnahmehandler einen Aufruf von `trackException()` einzufügen.

```javascript
try
{
    // ...
}
catch (ex)
{
    appInsights.trackException(ex, "handler loc",
    {
        Game: currentGame.Name,
        State: currentGame.State.ToString()
    });
}
```

```csharp
var telemetry = new TelemetryClient();

try
{
    // ...
}
catch (Exception ex)
{
    var properties = new Dictionary<string, string>
    {
        ["Game"] = currentGame.Name
    };

    var measurements = new Dictionary<string, double>
    {
        ["Users"] = currentGame.Users.Count
    };

    // Send the exception telemetry:
    telemetry.TrackException(ex, properties, measurements);
}
```

```VB
Dim telemetry = New TelemetryClient

Try
    ' ...
Catch ex as Exception
    ' Set up some properties:
    Dim properties = New Dictionary (Of String, String)
    properties.Add("Game", currentGame.Name)

    Dim measurements = New Dictionary (Of String, Double)
    measurements.Add("Users", currentGame.Users.Count)

    ' Send the exception telemetry:
    telemetry.TrackException(ex, properties, measurements)
End Try
```

Die Eigenschaften und Messparameter sind optional, aber hilfreich zum [Filtern und Hinzufügen](./diagnostic-search.md) zusätzlicher Informationen. Wenn Sie z. B. eine Anwendung haben, die mehrere Spiele ausführen kann, können Sie alle im Zusammenhang mit einem bestimmten Spiel stehenden Ausnahmeberichte ermitteln. Sie können jedem Wörterbuch beliebig viele Elemente hinzufügen.

## <a name="browser-exceptions"></a>Browserausnahmen

Die meisten Browserausnahmen werden gemeldet.

Wenn Ihre Webseite Skriptdateien aus Content Delivery Networks oder anderen Domänen umfasst, stellen Sie sicher, dass Ihr Skript-Tag das Attribut `crossorigin="anonymous"`aufweist und dass der Server [CORS-Header](https://enable-cors.org/)sendet. Dadurch können Sie eine Stapelüberwachung und Details für nicht behandelte JavaScript-Ausnahmen von diesen Ressourcen erhalten.

## <a name="reuse-your-telemetry-client"></a>Wiederverwenden Ihres Telemetrieclients

> [!NOTE]
> Es wird empfohlen, `TelemetryClient` einmal zu instanziieren und über die gesamte Lebensdauer einer Anwendung hinweg wiederzuverwenden.

Mit der [Abhängigkeitsinjektion (Dependency Injection, DI) in .NET](/dotnet/core/extensions/dependency-injection), dem entsprechenden .NET SDK und einer ordnungsgemäß konfigurierten Application Insights-Instanz für die Abhängigkeitsinjektion können Sie <xref:Microsoft.VisualStudio.ApplicationInsights.TelemetryClient> als Konstruktorparameter anfordern.

```csharp
public class ExampleController : ApiController
{
    private readonly TelemetryClient _telemetryClient;

    public ExampleController(TelemetryClient telemetryClient)
    {
        _telemetryClient = telemetryClient;
    }
}
```

Im vorherigen Beispiel wird `TelemetryClient` in die `ExampleController`-Klasse eingefügt.

## <a name="web-forms"></a>Webformulare

Für Webformulare kann das HTTP-Modul die Ausnahmen erfassen, wenn keine Umleitungen mit `CustomErrors` konfiguriert sind. Wenn jedoch aktive Umleitungen bestehen, fügen Sie der `Application_Error`-Funktion in *Global.asax.cs* die folgenden Zeilen hinzu.

```csharp
void Application_Error(object sender, EventArgs e)
{
    if (HttpContext.Current.IsCustomErrorEnabled &&
        Server.GetLastError () != null)
    {
        _telemetryClient.TrackException(Server.GetLastError());
    }
}
```

Im vorherigen Beispiel ist `_telemetryClient` eine klassenspezifische Variable vom Typ <xref:Microsoft.VisualStudio.ApplicationInsights.TelemetryClient>.

## <a name="mvc"></a>MVC

Ab Application Insights Web SDK Version 2.6 (beta3 und höher) sammelt Application Insights Ausnahmefehler automatisch, die in den MVC 5+-Controllermethoden ausgelöst werden. Falls Sie bereits einen benutzerdefinierten Handler zum Nachverfolgen solcher Ausnahmen hinzugefügt haben, sollten Sie ihn entfernen, um eine doppelte Nachverfolgung von Ausnahmen zu verhindern.

Es gibt eine Reihe von Szenarios, in denen ein Ausnahmefilter Fehler nicht ordnungsgemäß behandeln kann, wenn Ausnahmen ausgelöst werden:

* von Controllerkonstruktoren
* von Meldungshandlern
* während des Routings
* während der Serialisierung des Antwortinhalts
* während des Anwendungsstarts
* bei Hintergrundaufgaben

Alle Ausnahmen die von der Anwendung *verarbeitet* werden, müssen weiterhin manuell nachverfolgt werden.
Ausnahmefehler, die von Controllern ausgehen, führen normalerweise zu folgender Antwort: 500 „Interner Serverfehler“. Wenn eine Antwort dieser Art aufgrund einer behandelten Ausnahme (oder keiner Ausnahme) manuell erstellt wird, wird sie in der dazugehörigen Anforderungstelemetrie mit „`ResultCode` 500“ nachverfolgt. Mit dem Application Insights SDK kann die entsprechende Ausnahme aber nicht nachverfolgt werden.

### <a name="prior-versions-support"></a>Unterstützung von früheren Versionen

Wenn Sie MVC 4 (und früher) des Application Insights Web SDK 2.5 (und früher) verwenden, sind die folgenden Beispiele zum Nachverfolgen von Ausnahmen hilfreich.

Wenn die [CustomErrors](/previous-versions/dotnet/netframework-4.0/h0hfz6fc(v=vs.100))-Konfiguration `Off` lautet, stehen für das zu erfassende [HTTP-Modul](/previous-versions/dotnet/netframework-3.0/ms178468(v=vs.85)) Ausnahmen zur Verfügung. Lautet sie allerdings `RemoteOnly` (Standardeinstellung) oder `On`, wird die Ausnahme gelöscht und steht für die automatische Erfassung durch Application Insights nicht zur Verfügung. Diesen Umstand können Sie beheben, indem Sie die [System.Web.Mvc.HandleErrorAttribute-Klasse](/dotnet/api/system.web.mvc.handleerrorattribute) außer Kraft setzen und die außer Kraft gesetzte Klasse wie unten gezeigt für die verschiedenen MVC-Versionen anwenden ([GitHub-Quelle](https://github.com/AppInsightsSamples/Mvc2UnhandledExceptions/blob/master/MVC2App/Controllers/AiHandleErrorAttribute.cs)):

```csharp
using System;
using System.Web.Mvc;
using Microsoft.ApplicationInsights;

namespace MVC2App.Controllers
{
    [AttributeUsage(AttributeTargets.Class | AttributeTargets.Method, Inherited = true, AllowMultiple = true)]
    public class AiHandleErrorAttribute : HandleErrorAttribute
    {
        public override void OnException(ExceptionContext filterContext)
        {
            if (filterContext != null && filterContext.HttpContext != null && filterContext.Exception != null)
            {
                //If customError is Off, then AI HTTPModule will report the exception
                if (filterContext.HttpContext.IsCustomErrorEnabled)
                {   //or reuse instance (recommended!). see note above
                    var ai = new TelemetryClient();
                    ai.TrackException(filterContext.Exception);
                }
            }
            base.OnException(filterContext);
        }
    }
}
```

#### <a name="mvc-2"></a>MVC 2

Ersetzen Sie das HandleError-Attribut durch das neue Attribut in Ihren Controllern.

```csharp
    namespace MVC2App.Controllers
    {
        [AiHandleError]
        public class HomeController : Controller
        {
            // Omitted for brevity
        }
    }
```

[Beispiel](https://github.com/AppInsightsSamples/Mvc2UnhandledExceptions)

#### <a name="mvc-3"></a>MVC 3

Registrieren Sie `AiHandleErrorAttribute` als globalen Filter in *Global.asax.cs*:

```csharp
public class MyMvcApplication : System.Web.HttpApplication
{
    public static void RegisterGlobalFilters(GlobalFilterCollection filters)
    {
        filters.Add(new AiHandleErrorAttribute());
    }
}
```

[Beispiel](https://github.com/AppInsightsSamples/Mvc3UnhandledExceptionTelemetry)

#### <a name="mvc-4-mvc5"></a>MVC 4, MVC5

Registrieren Sie `AiHandleErrorAttribute` als globalen Filter in *FilterConfig.cs*:

```csharp
public class FilterConfig
{
    public static void RegisterGlobalFilters(GlobalFilterCollection filters)
    {
        // Default replaced with the override to track unhandled exceptions
        filters.Add(new AiHandleErrorAttribute());
    }
}
```

[Beispiel](https://github.com/AppInsightsSamples/Mvc5UnhandledExceptionTelemetry)

## <a name="web-api"></a>Web-API

Ab Application Insights Web SDK Version 2.6 (beta3 und höher) sammelt Application Insights Ausnahmefehler, die in den Controllermethoden ausgelöst werden, für WebAPI 2+ automatisch. Wenn Sie bereits vorher einen benutzerdefinierten Handler zum Nachverfolgen dieser Ausnahmen hinzugefügt haben (wie in den folgenden Beispielen beschrieben), können Sie ihn entfernen, um eine doppelte Nachverfolgung von Ausnahmen zu verhindern.

Es gibt eine Reihe von Fällen, die von den Ausnahmefiltern nicht verarbeitet werden können. Beispiel:

* Von Controllerkonstruktoren ausgelöste Ausnahmen.
* Von Meldungshandlern ausgelöste Ausnahmen
* Während des Routings ausgelöste Ausnahmen.
* Während der Serialisierung von Antwortinhalten ausgelöste Ausnahmen.
* Ausnahme, die während des Anwendungsstarts ausgelöst wird.
* In Hintergrundaufgaben ausgelöste Ausnahme.

Alle Ausnahmen die von der Anwendung *verarbeitet* werden, müssen weiterhin manuell nachverfolgt werden.
Ausnahmefehler, die von Controllern ausgehen, führen normalerweise zu folgender Antwort: 500 „Interner Serverfehler“. Wenn eine Antwort dieser Art aufgrund einer behandelten Ausnahme (oder keiner Ausnahme) manuell erstellt wird, wird sie in einer dazugehörigen Anforderungstelemetrie mit „`ResultCode` 500“ nachverfolgt. Mit dem Application Insights SDK kann die entsprechende Ausnahme aber nicht nachverfolgt werden.

### <a name="prior-versions-support"></a>Unterstützung von früheren Versionen

Wenn Sie WebAPI 1 (und früher) des Application Insights Web SDK 2.5 (und früher) verwenden, sind die folgenden Beispiele zum Nachverfolgen von Ausnahmen hilfreich.

#### <a name="web-api-1x"></a>Web-API 1.x

Überschreiben Sie `System.Web.Http.Filters.ExceptionFilterAttribute`:

```csharp
using System.Web.Http.Filters;
using Microsoft.ApplicationInsights;

namespace WebAPI.App_Start
{
    public class AiExceptionFilterAttribute : ExceptionFilterAttribute
    {
    public override void OnException(HttpActionExecutedContext actionExecutedContext)
    {
        if (actionExecutedContext != null && actionExecutedContext.Exception != null)
        {  //or reuse instance (recommended!). see note above
            var ai = new TelemetryClient();
            ai.TrackException(actionExecutedContext.Exception);
        }
        base.OnException(actionExecutedContext);
    }
    }
}
```

Sie könnten dieses außer Kraft gesetzte Attribut bestimmten Controllern oder der globalen Filterkonfiguration in der `WebApiConfig`-Klasse hinzufügen:

```csharp
using System.Web.Http;
using WebApi1.x.App_Start;

namespace WebApi1.x
{
    public static class WebApiConfig
    {
        public static void Register(HttpConfiguration config)
        {
            config.Routes.MapHttpRoute(
                name: "DefaultApi",
                routeTemplate: "api/{controller}/{id}",
                defaults: new { id = RouteParameter.Optional });
    
            // ...
            config.EnableSystemDiagnosticsTracing();
    
            // Capture exceptions for Application Insights:
            config.Filters.Add(new AiExceptionFilterAttribute());
        }
    }
}
```

[Beispiel](https://github.com/AppInsightsSamples/WebApi_1.x_UnhandledExceptions)

#### <a name="web-api-2x"></a>Web-API 2.x

Fügen Sie eine Implementierung von `IExceptionLogger` hinzu:

```csharp
using System.Web.Http.ExceptionHandling;
using Microsoft.ApplicationInsights;

namespace ProductsAppPureWebAPI.App_Start
{
    public class AiExceptionLogger : ExceptionLogger
    {
        public override void Log(ExceptionLoggerContext context)
        {
            if (context != null && context.Exception != null)
            {
                //or reuse instance (recommended!). see note above
                var ai = new TelemetryClient();
                ai.TrackException(context.Exception);
            }
            base.Log(context);
        }
    }
}
```

Fügen Sie den Diensten in WebApiConfig Folgendes hinzu:

```csharp
using System.Web.Http;
using System.Web.Http.ExceptionHandling;
using ProductsAppPureWebAPI.App_Start;

namespace WebApi2WithMVC
{
    public static class WebApiConfig
    {
        public static void Register(HttpConfiguration config)
        {
            // Web API configuration and services
    
            // Web API routes
            config.MapHttpAttributeRoutes();
    
            config.Routes.MapHttpRoute(
                name: "DefaultApi",
                routeTemplate: "api/{controller}/{id}",
                defaults: new { id = RouteParameter.Optional });

            config.Services.Add(typeof(IExceptionLogger), new AiExceptionLogger());
        }
    }
}
```

[Beispiel](https://github.com/AppInsightsSamples/WebApi_2.x_UnhandledExceptions)

Als Alternativen können Sie folgende Aktionen ausführen:

1. Ersetzen Sie den einzigen ExceptionHandler durch eine benutzerdefinierte Implementierung von IExceptionHandler. Diese wird nur aufgerufen, wenn das Framework weiterhin wählen kann, welche Antwortnachricht gesendet wird (nicht wenn z. B. die Verbindung abgebrochen wird).
2. Ausnahmefilter (wie im obigen Abschnitt zu Web-API-1.x-Controllern beschrieben) – werden nicht in allen Fällen aufgerufen.

## <a name="wcf"></a>WCF

Fügen Sie eine Klasse hinzu, die "Attribute" erweitert und "IErrorHandler" und "IServiceBehavior" implementiert.

```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.ServiceModel.Description;
    using System.ServiceModel.Dispatcher;
    using System.Web;
    using Microsoft.ApplicationInsights;

    namespace WcfService4.ErrorHandling
    {
      public class AiLogExceptionAttribute : Attribute, IErrorHandler, IServiceBehavior
      {
        public void AddBindingParameters(ServiceDescription serviceDescription,
            System.ServiceModel.ServiceHostBase serviceHostBase,
            System.Collections.ObjectModel.Collection<ServiceEndpoint> endpoints,
            System.ServiceModel.Channels.BindingParameterCollection bindingParameters)
        {
        }

        public void ApplyDispatchBehavior(ServiceDescription serviceDescription,
            System.ServiceModel.ServiceHostBase serviceHostBase)
        {
            foreach (ChannelDispatcher disp in serviceHostBase.ChannelDispatchers)
            {
                disp.ErrorHandlers.Add(this);
            }
        }

        public void Validate(ServiceDescription serviceDescription,
            System.ServiceModel.ServiceHostBase serviceHostBase)
        {
        }

        bool IErrorHandler.HandleError(Exception error)
        {//or reuse instance (recommended!). see note above
            var ai = new TelemetryClient();

            ai.TrackException(error);
            return false;
        }

        void IErrorHandler.ProvideFault(Exception error,
            System.ServiceModel.Channels.MessageVersion version,
            ref System.ServiceModel.Channels.Message fault)
        {
        }
      }
    }
```

Fügen Sie das Attribut den Dienstimplementierungen hinzu:

```csharp
namespace WcfService4
{
    [AiLogException]
    public class Service1 : IService1
    {
        // Omitted for brevity
    }
}
```

[Beispiel](https://github.com/AppInsightsSamples/WCFUnhandledExceptions)

## <a name="exception-performance-counters"></a>Ausnahmeleistungsindikatoren

Wenn Sie den [Azure Monitor Application Insights-Agent auf Ihrem Server installiert haben](./status-monitor-v2-overview.md), können Sie ein Diagramm mit der von .NET gemessenen Ausnahmenrate abrufen. Dies enthält sowohl behandelte als auch nicht behandelte .NET-Ausnahmen.

Öffnen Sie eine Metrik-Explorer-Registerkarte, fügen Sie ein neues Diagramm hinzu, und wählen Sie in den Leistungsindikatoren **Ausnahmerate** aus.

.NET Framework berechnet die Rate, indem die Anzahl von Ausnahmen innerhalb eines Intervalls gezählt und diese durch die Länge des Intervalls geteilt wird.

Dieser Wert unterscheidet sich von der Anzahl der „Ausnahmen“, die vom Application Insights-Portal durch Zählen von TrackException-Meldungen berechnet wird. Die Samplingintervalle sind unterschiedlich, und das SDK sendet keine TrackException-Meldungen für alle behandelten und nicht behandelten Ausnahmen.

## <a name="next-steps"></a>Nächste Schritte

* [Überwachen von REST, SQL und anderen Aufrufen von Abhängigkeiten](./asp-net-dependencies.md)
* [Überwachen von Seitenladezeiten, Browserausnahmen und AJAX-Aufrufen](./javascript.md)
* [Überwachen von Leistungsindikatoren](./performance-counters.md)
