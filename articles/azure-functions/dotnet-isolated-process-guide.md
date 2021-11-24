---
title: Leitfaden zum Ausführen von C#-Azure Functions in einem isolierten Prozess
description: Erfahren Sie, wie Sie einen isolierten .NET-Prozess verwenden, um Ihre C#-Funktionen in Azure auszuführen, das .NET 5.0 und höhere Versionen unterstützt.
ms.service: azure-functions
ms.topic: conceptual
ms.date: 06/01/2021
ms.custom: template-concept
recommendations: false
ms.openlocfilehash: b12841b83e4c2f6f2756ddffdd4adc7bde77e73a
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132401267"
---
# <a name="guide-for-running-c-azure-functions-in-an-isolated-process"></a>Leitfaden zum Ausführen von C#-Azure Functions in einem isolierten Prozess

Dieser Artikel ist eine Einführung in die Verwendung von C#, um isolierte .NET-Prozessfunktionen zu entwickeln, die prozessextern in Azure Functions ausgeführt werden. Durch die prozessexterne Ausführung können Sie den Funktionscode von der Azure Functions-Runtime entkoppeln. Isolierte C#-Prozessfunktionen werden sowohl unter .NET 5.0 als auch in .NET 6.0 ausgeführt. [In-Process-C#-Klassenbibliotheksfunktionen](functions-dotnet-class-library.md) werden in .NET 5.0 nicht unterstützt. 

| Erste Schritte | Konzepte| Beispiele |
|--|--|--| 
| <ul><li>[Verwendung von Visual Studio Code](create-first-function-vs-code-csharp.md?tabs=isolated-process)</li><li>[Verwenden von Befehlszeilentools](create-first-function-cli-csharp.md?tabs=isolated-process)</li><li>[Verwenden von Visual Studio](functions-create-your-first-function-visual-studio.md?tabs=isolated-process)</li></ul> | <ul><li>[Hostingoptionen](functions-scale.md)</li><li>[Überwachung](functions-monitoring.md)</li> | <ul><li>[Referenzbeispiele](https://github.com/Azure/azure-functions-dotnet-worker/tree/main/samples)</li></ul> |

## <a name="why-net-isolated-process"></a>Gründe für isolierte .NET-Prozesse

Zuvor hat Azure Functions nur einen stark integrierten Modus für .NET-Funktionen unterstützt, die als [Klassenbibliothek](functions-dotnet-class-library.md) im selben Prozess wie der Host ausgeführt werden. Dieser Modus ermöglicht die enge Verzahnung zwischen dem Hostprozess und den Funktionen. Funktionen der .NET-Klassenbibliothek können beispielsweise Bindungs-APIs und Bindungstypen freigeben. Diese Integration erfordert jedoch auch eine engere Kopplung zwischen dem Hostprozess und der .NET-Funktion. Prozessintern ausgeführte .NET-Funktionen müssen beispielsweise die gleiche .NET-Version wie die Functions-Runtime aufweisen. Sie können diese Einschränkungen jetzt umgehen, indem Sie sie in einem isolierten Prozess ausführen. Dank dieser Prozessisolierung können Sie Funktionen für aktuelle .NET-Releases (wie .NET 5.0) entwickeln, die von der Functions-Runtime nicht nativ unterstützt werden. Sowohl isolierte Prozess- als auch prozessbezogene C#-Klassenbibliotheksfunktionen werden unter .NET 6.0 ausgeführt. Erfahren Sie mehr über [unterstützte Versionen](#supported-versions). 

Da diese Funktionen in einem separaten Prozess ausgeführt werden, bestehen einige [Feature- und Funktionsunterschiede](#differences-with-net-class-library-functions) zwischen Apps mit isolierten .NET-Funktionen und Apps mit Funktionen der .NET-Klassenbibliothek.

### <a name="benefits-of-running-out-of-process"></a>Vorteile der prozessexternen Ausführung

Bei der prozessexternen Ausführung bestehen die folgenden Vorteile für Ihre .NET-Funktionen: 

+ Weniger Konflikte: Da die Funktionen in einem separaten Prozess ausgeführt werden, geraten die Assemblys Ihrer App nicht mit unterschiedlichen Versionen der gleichen Assemblys in Konflikt, die vom Hostprozess verwendet werden.  
+ Vollständige Kontrolle über den Prozess: Sie steuern den Start der App, die verwendeten Konfigurationen und die Middleware.
+ Dependency Injection: Da Sie die vollständige Kontrolle über den Prozess haben, können Sie aktuelle .NET-Verhaltensweisen für die Dependency Injection und die Einbindung von Middleware in Ihre Funktions-App nutzen. 

[!INCLUDE [functions-dotnet-supported-versions](../../includes/functions-dotnet-supported-versions.md)]

## <a name="net-isolated-project"></a>Isoliertes .NET-Projekt

Ein Projekt mit isolierten .NET-Funktionen ist im Grunde ein Projekt für eine .NET-Konsolen-App mit einer unterstützten .NET-Laufzeit als Ziel. Die folgenden Dateien werden in jedem isolierten .NET-Projekt benötigt:

+ [host.json](functions-host-json.md)
+ [local.settings.json](functions-develop-local.md#local-settings-file)
+ C#-Projektdatei (.csproj), definiert das Projekt und die Abhängigkeiten
+ Program.cs, der Einstiegspunkt für die App

## <a name="package-references"></a>Paketverweise

Bei der prozessexternen Ausführung verwendet Ihr .NET-Projekt spezifische Pakete, die die Kernfunktionalitäten und die Bindungserweiterungen implementieren. 

### <a name="core-packages"></a>Erforderliche Pakete 

Die folgenden Pakete werden benötigt, damit Ihre .NET-Funktionen in einem isolierten Prozess ausgeführt werden können:

+ [Microsoft.Azure.Functions.Worker](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker/)
+ [Microsoft.Azure.Functions.Worker.Sdk](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker.Sdk/)

### <a name="extension-packages"></a>Erweiterungspakete

Da in einem isolierten .NET-Prozess ausgeführte Funktionen andere Bindungstypen verwenden, benötigen sie spezifische Pakete mit Bindungserweiterungen. 

Diese Erweiterungspakete finden Sie unter [Microsoft.Azure.Functions.Worker.Extensions](https://www.nuget.org/packages?q=Microsoft.Azure.Functions.Worker.Extensions).

## <a name="start-up-and-configuration"></a>Start und Konfiguration 

Wenn Sie isolierte .NET-Funktionen verwenden, haben Sie Zugriff auf die Startklasse für Ihre Funktions-App, die sich üblicherweise in „Program.cs“ befindet. Sie sind dafür verantwortlich, eine eigene Hostinstanz zu erstellen und zu starten. Sie haben daher auch direkten Zugriff auf die Konfigurationspipeline für Ihre App. Bei der prozessexternen Ausführung sind das Einfügen von Konfigurationen, Dependency Injection und die Ausführung Ihrer eigenen Middleware einfacher. 

Im Folgenden sehen Sie ein Beispiel für eine [HostBuilder]-Pipeline:

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" id="docsnippet_startup":::

Für diesen Code ist `using Microsoft.Extensions.DependencyInjection;` erforderlich. 

Ein [HostBuilder] wird zur Erstellung und Rückgabe einer vollständig initialisierten [IHost]-Instanz verwendet. Diese führen Sie asynchron aus, um Ihre Funktions-App zu starten. 

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" id="docsnippet_host_run":::

### <a name="configuration"></a>Konfiguration

Die Methode [ConfigureFunctionsWorkerDefaults] wird verwendet, um die Einstellungen hinzuzufügen, die für die prozessexterne Durchführung der Funktions-App erforderlich sind. Dies umfasst die folgenden Funktionen:

+ Standardsatz von Konvertern.
+ Legen Sie die Standard-[JsonSerializerOptions] fest, um die Groß- und Kleinschreibung von Eigenschaftsnamen zu übergehen.
+ Integration in Azure Functions-Protokollierung.
+ Ausgabe verbindlicher Middleware und Funktionen.
+ Middleware für die Funktionsausführung.
+ Standardmäßige gRPC-Unterstützung. 

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" id="docsnippet_configure_defaults" :::   

Da Sie Zugriff auf die HostBuilder-Pipeline haben, können Sie auch App-spezifische Konfigurationen während der Initialisierung festlegen. Zum Hinzufügen der Konfigurationen, die für ihre Funktions-APP erforderlich sind, können Sie die Methode[ConfigureAppConfiguration] mindestens einmal für [HostBuilder] aufrufen. Weitere Informationen zur App-Konfiguration finden Sie unter [Konfiguration in ASP.NET Core](/aspnet/core/fundamentals/configuration/?view=aspnetcore-5.0&preserve-view=true). 

Diese Konfigurationen gelten für die in einem separaten Prozess ausgeführte Funktions-App. Wenn Sie Änderungen am Funktionshost oder -trigger und der Bindungskonfiguration vornehmen möchten, müssen Sie weiterhin die Datei [host.json](functions-host-json.md) verwenden.   

### <a name="dependency-injection"></a>Dependency Injection

Im Vergleich zu .NET-Klassenbibliotheken ist die Dependency Injection vereinfacht. Anstatt eine Startup-Klasse zur Registrierung von Diensten erstellen zu müssen, können Sie [ConfigureServices] im HostBuilder aufrufen und die Erweiterungsverfahren in [IServiceCollection] verwenden, um bestimmte Dienste einzugeben. 

Im folgenden Beispiel wird eine Dependency Injection für einen Singletondienst durchgeführt:  
 
:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" id="docsnippet_dependency_injection" :::

Für diesen Code ist `using Microsoft.Extensions.DependencyInjection;` erforderlich. Weitere Informationen finden Sie unter [Dependency Injection in ASP.NET Core](/aspnet/core/fundamentals/dependency-injection?view=aspnetcore-5.0&preserve-view=true).

### <a name="middleware"></a>Middleware

Die isolierte .NET-Unterstützung unterstützt auch die Middleware-Registrierung mit einem Modell, das dem in ASP.NET vorhandenen Modell ähnelt. Dieses Modell bietet Ihnen die Möglichkeit, Logik in die Aufrufpipeline einzufügen, und vorher und nachher Funktionen auszuführen.

Die Erweiterungsmethode [ConfigureFunctionsWorkerDefaults] weist eine Überladung auf, mit der Sie Ihre eigene Middleware registrieren können, wie im folgenden Beispiel zu sehen ist.  

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/CustomMiddleware/Program.cs" id="docsnippet_middleware_register" :::

Ein ausführlicheres Beispiel für die Verwendung von benutzerdefinierter Middleware in ihrer Funktions-APP finden Sie im Beispiel für eine [benutzerdefinierte Middleware-Referenz](https://github.com/Azure/azure-functions-dotnet-worker/blob/main/samples/CustomMiddleware).

## <a name="execution-context"></a>Ausführungskontext

Der isolierte .NET-Prozess übergibt ein [FunctionContext]-Objekt an Ihre Funktionsmethoden. Mit diesem Objekt erhalten Sie eine [ILogger]-Instanz, um in die Protokolle zu schreiben, indem Sie die [GetLogger]-Methode aufrufen und eine `categoryName` Zeichenfolge anbieten. Weitere Informationen finden Sie unter [Protokollierung](#logging). 

## <a name="bindings"></a>Bindungen 

Bindungen werden mithilfe von Attributen in Methoden, Parametern und Rückgabetypen definiert. Eine Funktionsmethode ist eine Methode mit einem `Function` Attribut und einem Trigger-Attribut, die auf einen Ausgabeparameter angewendet werden. Dies wird im folgenden Beispiel veranschaulicht:

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/Extensions/Queue/QueueFunction.cs" id="docsnippet_queue_trigger" :::

Das Trigger-Attribut gibt den Triggertyp an und bindet die Eingabedaten an einen Methodenparameter. Die zuvor gezeigte Beispielfunktion wird durch eine Warteschlangennachricht ausgelöst, und die Warteschlangennachricht wird im Parameter `myQueueItem` an die Methode übergeben.

Das Attribut `Function` kennzeichnet die Methode als Funktionseinstiegspunkt. Der Name muss innerhalb eines Projekts eindeutig sein, mit einem Buchstaben beginnen und darf nur Buchstaben, Ziffern, `_` und `-` enthalten. Bis zu 127 Zeichen sind zulässig. Projektvorlagen erstellen oft eine Methode namens `Run`, aber der Name der Methode kann ein beliebiger gültiger C#-Methodennamen sein.

Da isolierte .NET-Projekte in einem separaten Workerprozess ausgeführt werden, können Bindungen keine umfangreichen Bindungsklassen wie `ICollector<T>`, `IAsyncCollector<T>` und `CloudBlockBlob` verwenden. Außerdem gibt es keine direkte Unterstützung für von zugrunde liegenden Dienst-SDKs abgeleitete Typen wie [DocumentClient] und [BrokeredMessage]. Bindungen basieren stattdessen auf Zeichenfolgen, Arrays und serialisierbaren Typen wie Plain Old CLR Objects (POCOs). 

Bei HTTP-Triggern müssen Sie [HttpRequestData] und [HttpResponseData] verwenden, um auf die Anfrage- und Antwortdaten zugreifen zu können. Das liegt daran, dass Sie bei der prozessexternen Ausführung nicht auf die ursprüngliche HTTP-Anforderung und die Antwortobjekte zugreifen können.

Einen umfassenden Satz von Verweisbeispielen für die Verwendung von Triggern und Bindungen bei prozessexterner Ausführung finden Sie im [Referenzbeispiel für Bindungserweiterungen](https://github.com/Azure/azure-functions-dotnet-worker/blob/main/samples/Extensions). 

### <a name="input-bindings"></a>Eingabebindungen

Eine Funktion kann über null oder mehr Eingabebindungen verfügen, die Daten an eine andere Funktion übergeben können. Genau wie Trigger werden Eingabebindungen definiert, indem ein Bindungsattribut auf einen Eingabeparameter angewendet wird. Wenn die Funktion ausgeführt wird, versucht die Runtime, die in der Bindung angegebenen Daten abzurufen. Die angeforderten Daten sind häufig von den Informationen abhängig, die vom Triggern mithilfe von Bindungsparametern bereitgestellt werden.  

### <a name="output-bindings"></a>Ausgabebindungen

Wenn Sie eine Ausgabebindung schreiben möchten, müssen Sie ein Ausgabebindungsattribut an die Funktionsmethode anfügen, in der definiert ist, wie in den gebundenen Dienst geschrieben werden soll. Der von der Methode zurückgegebene Wert wird in die Ausgabebindung geschrieben. Im folgenden Beispiel wird ein Zeichenfolgenwert mithilfe einer Ausgabebindung in die Nachrichtenwarteschlange `myqueue-output` geschrieben:

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/Extensions/Queue/QueueFunction.cs" id="docsnippet_queue_output_binding" :::

### <a name="multiple-output-bindings"></a>Mehrere Ausgabebindungen

Die Daten, die in eine Ausgabebindung geschrieben werden, sind immer der Rückgabewert der Funktion. Wenn Sie in mehr als eine Ausgabebindung schreiben müssen, müssen Sie einen benutzerdefinierten Rückgabetyp erstellen. Bei diesem Rückgabetyp muss das Ausgabebindungsattribut auf eine oder mehrere Eigenschaften der Klasse angewendet werden. Im folgenden Beispiel von einem HTTP-Trigger wird sowohl in eine HTTP-Antwort als auch in eine Ausgabebindung für eine Warteschlange geschrieben:

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/Extensions/MultiOutput/MultiOutput.cs" id="docsnippet_multiple_outputs":::

Die Antwort eines HTTP-Triggers wird immer als Ausgabe angesehen, daher ist ein Ergebnis-Attribut nicht erforderlich.

### <a name="http-trigger"></a>HTTP-Trigger

HTTP-Trigger übersetzen eingehende HTTP-Anforderungsnachrichten in ein [HttpRequestData]-Objekt, das an die Funktion übergeben wird. Dieses Objekt liefert die Anforderungsdaten wie `Headers`, `Cookies`, `Identities`, `URL` und optional `Body` (Nachrichtentext). Dieses Objekt ist eine Darstellung des HTTP-Anforderungsobjekts, nicht der Anforderung selbst. 

Entsprechend gibt die Funktion ein [HttpResponseData]-Objekt zurück, das die Daten zur Erstellung der HTTP-Antwort enthält, zum Beispiel Nachricht `StatusCode`, `Headers`, und optional Nachricht `Body`.  

Der folgende Code stellt einen HTTP-Trigger dar: 

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/Extensions/Http/HttpFunction.cs" id="docsnippet_http_trigger" :::

## <a name="logging"></a>Protokollierung

Im .NET-isolierten Prozess können Sie in Protokolle schreiben, indem Sie eine [ILogger]-Instanz verwenden, die von einem [FunctionContext]-Objekt erhalten wird, das von Ihrer Funktion abgerufen wurde. Rufen Sie die Methode [GetLogger] auf, und übergeben Sie dabei einen Zeichenfolgenwert, der dem Namen der Kategorie entspricht, in welche die Protokolle geschrieben werden. Die Kategorie ist normalerweise der Name der spezifischen Funktion, aus der die Protokolle geschrieben werden. Weitere Informationen zu Kategorien finden Sie im [Artikel zur Überwachung](functions-monitoring.md#log-levels-and-categories). 

Im folgenden Beispiel wird gezeigt, wie [ILogger] abgerufen wird und Protokolle in einer Funktion geschrieben werden:

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/Extensions/Http/HttpFunction.cs" id="docsnippet_logging" ::: 

Verwenden Sie verschiedene Methoden von [ILogger`LogError`, um verschiedene Protokolliergrade wie ] oder `LogWarning` zu schreiben. Weitere Informationen zu den Protokolliergraden finden Sie im [Artikel zur Überwachung](functions-monitoring.md#log-levels-and-categories).

[ILogger] wird auch bereitgestellt, wenn Sie [Dependency Injection](#dependency-injection) nutzen.

## <a name="differences-with-net-class-library-functions"></a>Unterschiede zu Funktionen der .NET-Klassenbibliothek

In diesem Abschnitt werden die aktuellen Funktions- und Verhaltensunterschiede zwischen prozessextern ausgeführten Funktionen und prozessintern ausgeführten Funktionen der .NET-Klassenbibliothek erläutert:

| Feature/Verhalten |  In-Process | Out-of-Process  |
| ---- | ---- | ---- |
| .NET-Versionen | .NET Core 3.1<br/>.NET 6.0 | .NET 5.0<br/>.NET 6.0 |
| Erforderliche Pakete | [Microsoft.NET.Sdk.Functions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/) | [Microsoft.Azure.Functions.Worker](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker/)<br/>[Microsoft.Azure.Functions.Worker.Sdk](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker.Sdk) | 
| Pakete für Bindungserweiterungen | [Microsoft.Azure.WebJobs.Extensions.*](https://www.nuget.org/packages?q=Microsoft.Azure.WebJobs.Extensions)  | Unter [Microsoft.Azure.Functions.Worker.Extensions.*](https://www.nuget.org/packages?q=Microsoft.Azure.Functions.Worker.Extensions) | 
| Protokollierung | An die Funktion übergebene [ILogger] | [ILogger] aus [FunctionContext] erhalten |
| Abbruchtoken | [Unterstützt](functions-dotnet-class-library.md#cancellation-tokens) | Nicht unterstützt |
| Ausgabebindungen | Out-Parameter | Rückgabewerte |
| Ausgabebindungstypen |  `IAsyncCollector`, [DocumentClient], [BrokeredMessage] und andere clientspezifische Typen | Einfache Typen, serialisierbare JSON-Typen und Arrays |
| Mehrere Ausgabebindungen | Unterstützt | [Unterstützt](#multiple-output-bindings) |
| HTTP-Trigger | [HttpRequest]/[ObjectResult] | [HttpRequestData]/[HttpResponseData] |
| Langlebige Funktionen | [Unterstützt](durable/durable-functions-overview.md) | Nicht unterstützt | 
| Imperative Bindungen | [Unterstützt](functions-dotnet-class-library.md#binding-at-runtime) | Nicht unterstützt |
| function.json-Artefakt | Generiert | Nicht generiert |
| Konfiguration | [host.json](functions-host-json.md) | [host.json](functions-host-json.md) und benutzerdefinierte Initialisierung |
| Dependency Injection | [Unterstützt](functions-dotnet-dependency-injection.md)  | [Unterstützt](#dependency-injection) |
| Middleware | Nicht unterstützt | Unterstützt |
| Kaltstartdauer | Typisch | Länger (aufgrund des Just-In-Time-Starts), Ausführung unter Linux anstelle von Windows, um potenzielle Verzögerungen zu vermeiden |
| ReadyToRun | [Unterstützt](functions-dotnet-class-library.md#readytorun) | _TBD_ | 

## <a name="next-steps"></a>Nächste Schritte

+ [Weitere Informationen zu Triggern und Bindungen](functions-triggers-bindings.md)
+ [Weitere Informationen zu bewährten Methoden für Azure Functions](functions-best-practices.md)


[HostBuilder]: /dotnet/api/microsoft.extensions.hosting.hostbuilder?view=dotnet-plat-ext-5.0&preserve-view=true
[IHost]: /dotnet/api/microsoft.extensions.hosting.ihost?view=dotnet-plat-ext-5.0&preserve-view=true
[ConfigureFunctionsWorkerDefaults]: /dotnet/api/microsoft.extensions.hosting.workerhostbuilderextensions.configurefunctionsworkerdefaults?view=azure-dotnet&preserve-view=true#Microsoft_Extensions_Hosting_WorkerHostBuilderExtensions_ConfigureFunctionsWorkerDefaults_Microsoft_Extensions_Hosting_IHostBuilder_
[ConfigureAppConfiguration]: /dotnet/api/microsoft.extensions.hosting.hostbuilder.configureappconfiguration?view=dotnet-plat-ext-5.0&preserve-view=true
[IServiceCollection]: /dotnet/api/microsoft.extensions.dependencyinjection.iservicecollection?view=dotnet-plat-ext-5.0&preserve-view=true
[ConfigureServices]: /dotnet/api/microsoft.extensions.hosting.hostbuilder.configureservices?view=dotnet-plat-ext-5.0&preserve-view=true
[FunctionContext]: /dotnet/api/microsoft.azure.functions.worker.functioncontext?view=azure-dotnet&preserve-view=true
[ILogger]: /dotnet/api/microsoft.extensions.logging.ilogger?view=dotnet-plat-ext-5.0&preserve-view=true
[GetLogger]: /dotnet/api/microsoft.azure.functions.worker.functioncontextloggerextensions.getlogger?view=azure-dotnet&preserve-view=true
[DocumentClient]: /dotnet/api/microsoft.azure.documents.client.documentclient
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[HttpRequestData]: /dotnet/api/microsoft.azure.functions.worker.http.httprequestdata?view=azure-dotnet&preserve-view=true
[HttpResponseData]: /dotnet/api/microsoft.azure.functions.worker.http.httpresponsedata?view=azure-dotnet&preserve-view=true
[HttpRequest]: /dotnet/api/microsoft.aspnetcore.http.httprequest?view=aspnetcore-5.0&preserve-view=true
[ObjectResult]: /dotnet/api/microsoft.aspnetcore.mvc.objectresult?view=aspnetcore-5.0&preserve-view=true
[JsonSerializerOptions]: /dotnet/api/system.text.json.jsonserializeroptions?view=net-5.0&preserve-view=true
