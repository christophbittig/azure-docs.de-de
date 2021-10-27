---
title: Konfigurieren der Überwachung für Azure Functions
description: Es wird beschrieben, wie Sie Ihre Funktions-App zur Überwachung mit Application Insights verbinden und die Datensammlung konfigurieren.
ms.date: 8/31/2020
ms.topic: how-to
ms.custom: contperf-fy21q2
ms.openlocfilehash: 3afe10184ba2b3f0eba02111b98b31e86b26e075
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "130006479"
---
# <a name="how-to-configure-monitoring-for-azure-functions"></a>Konfigurieren der Überwachung für Azure Functions

Die Integration von Azure Functions mit Application Insights ermöglicht Ihnen eine bessere Überwachung Ihrer Funktions-Apps. Application Insights, ein Feature von Azure Monitor, ist ein erweiterbarer Dienst für die Steuerung der Anwendungsleistung (Application Performance Management, APM), mit dem von Ihrer Funktions-App generierte Daten gesammelt werden (z. B. auch Informationen, die von Ihrer App in Protokolle geschrieben werden). Die Application Insights-Integration wird in der Regel beim Erstellen Ihrer Funktions-App aktiviert. Wenn für Ihre App kein Instrumentierungsschlüssel festgelegt ist, müssen Sie die [Application Insights-Integration zunächst aktivieren](#enable-application-insights-integration). 

Sie können Application Insights ganz ohne benutzerdefinierte Konfiguration verwenden. Die Standardkonfiguration kann zu großen Datenmengen führen. Wenn Sie ein Visual Studio Azure-Abonnement verwenden, erreichen Sie unter Umständen Ihr Datenlimit für Application Insights. Weitere Informationen zu den Application Insights-Kosten finden Sie unter [Verwalten der Nutzung und der Kosten für Application Insights](../azure-monitor/app/pricing.md). Weitere Informationen finden Sie unter [Lösungen mit großen Mengen an Telemetriedaten](#solutions-with-high-volume-of-telemetry).

Später in diesem Artikel erfahren Sie, wie Sie die Daten konfigurieren und anpassen, die Ihre Funktionen an Application Insights senden. Für eine Funktions-App wird die Protokollierung in der Datei [host.json] konfiguriert. 

> [!NOTE]
> Sie können speziell konfigurierte Anwendungseinstellungen verwenden, um bestimmte Einstellungen in der Datei „host.json“ für eine bestimmte Umgebung darzustellen. Hierdurch können Sie Einstellungen der Datei „host.json“ auf effektive Weise ändern, ohne dass Sie diese Datei erneut in Ihrem Projekt veröffentlichen müssen. Weitere Informationen finden Sie unter [Außerkraftsetzung der Werte in der Datei „host.json“](functions-host-json.md#override-hostjson-values).

## <a name="configure-categories"></a>Konfigurieren von Kategorien

Die Azure Functions-Protokollierung enthält eine *Kategorie* für jedes Protokoll. Mit der Kategorie wird angegeben, von welchem Teil des Laufzeitcodes bzw. Ihres Funktionscodes das Protokoll geschrieben wurde. Kategorien unterscheiden sich für Version 1.x und höhere Versionen. In der folgenden Tabelle werden die Hauptkategorien der Protokolle beschrieben, die von der Laufzeit erstellt werden. 

# <a name="v2x"></a>[v2.x+](#tab/v2)

| Kategorie | Tabelle | BESCHREIBUNG |
| ----- | ----- | ----- |
| **`Function.<YOUR_FUNCTION_NAME>`** | **dependencies**| Abhängigkeitsdaten werden für einige Dienste automatisch gesammelt. Bei erfolgreichen Ausführungen befinden sich diese Protokolle auf der Stufe `Information`. Weitere Informationen finden Sie unter [Abhängigkeiten](functions-monitoring.md#dependencies). Ausnahmen werden auf der Stufe `Error` protokolliert. Von der Runtime werden auch Protokolle mit der Stufe `Warning` erstellt, z. B. wenn Warteschlangennachrichten an die [Warteschlange für nicht verarbeitete Nachrichten](functions-bindings-storage-queue-trigger.md#poison-messages) gesendet werden. | 
| **`Function.<YOUR_FUNCTION_NAME>`** | **customMetrics**<br/>**customEvents** | Mit C#- und JavaScript-SDKs können Sie benutzerdefinierte Metriken erfassen und benutzerdefinierte Ereignisse protokollieren. Weitere Informationen finden Sie unter [Benutzerdefinierte Telemetriedaten](functions-monitoring.md#custom-telemetry-data).|
| **`Function.<YOUR_FUNCTION_NAME>`** | **traces**| Enthält die gestartete Funktion und abgeschlossene Protokolle für bestimmte Funktionsausführungen. Bei erfolgreichen Ausführungen befinden sich diese Protokolle auf der Stufe `Information`. Ausnahmen werden auf der Stufe `Error` protokolliert. Von der Runtime werden auch Protokolle mit der Stufe `Warning` erstellt, z. B. wenn Warteschlangennachrichten an die [Warteschlange für nicht verarbeitete Nachrichten](functions-bindings-storage-queue-trigger.md#poison-messages) gesendet werden. | 
| **`Function.<YOUR_FUNCTION_NAME>.User`** | **traces**| Vom Benutzer generierte Protokolle mit einer beliebigen Protokollstufe. Weitere Informationen zum Schreiben in Protokolle aus Ihren Funktionen finden Sie unter [Schreiben in Protokolle](functions-monitoring.md#writing-to-logs). | 
| **`Host.Aggregator`** | **customMetrics** | Diese von der Runtime generierten Protokolle enthalten die Anzahl und Durchschnittswerte von Funktionsaufrufen für einen [konfigurierbaren](#configure-the-aggregator) Zeitraum. Der Standardzeitraum beträgt 30 Sekunden oder 1.000 Ergebnisse, je nachdem, was früher eintritt. Beispiele hierfür sind die Ausführungsanzahl, die Erfolgsrate und die Dauer. Alle diese Protokolle werden auf der Stufe `Information` geschrieben. Wenn Sie nach `Warning` oder höheren Stufen filtern, finden Sie keine dieser Daten. |
| **`Host.Results`** | **requests** | Diese von der Runtime generierten Protokolle enthalten einen Hinweis zum Erfolg bzw. Misserfolg einer Funktion. Alle diese Protokolle werden auf der Stufe `Information` geschrieben. Wenn Sie nach `Warning` oder höheren Stufen filtern, finden Sie keine dieser Daten. |
| **`Microsoft`** | **traces** | Vollständig qualifizierte Protokollkategorie, die eine vom Host aufgerufene .NET-Runtimekomponente widerspiegelt.  |
| **`Worker`** | **traces** | Protokolle, die vom Sprachworkerprozess für nicht zu .NET gehörende Sprachen generiert werden. Sprachworkerprotokolle können auch in einer Kategorie vom Typ `Microsoft.*` protokolliert werden, z. B. `Microsoft.Azure.WebJobs.Script.Workers.Rpc.RpcFunctionInvocationDispatcher`. Diese Protokolle werden auf der Stufe `Information` geschrieben.|

> [!NOTE]
> Für .NET-Klassenbibliotheksfunktionen wird bei diesen Kategorien davon ausgegangen, dass Sie `ILogger` verwenden und nicht `ILogger<T>`. Weitere Informationen finden Sie in der [ILogger-Dokumentation von Functions](functions-dotnet-class-library.md#ilogger). 

# <a name="v1x"></a>[v1.x](#tab/v1)

| Kategorie | Tabelle | BESCHREIBUNG |
| ----- | ----- | ----- |
| **`Function`** | **traces**| Vom Benutzer generierte Protokolle mit einer beliebigen Protokollstufe. Weitere Informationen zum Schreiben in Protokolle aus Ihren Funktionen finden Sie unter [Schreiben in Protokolle](functions-monitoring.md#writing-to-logs). | 
| **`Host.Aggregator`** | **customMetrics** | Diese von der Runtime generierten Protokolle enthalten die Anzahl und Durchschnittswerte von Funktionsaufrufen für einen [konfigurierbaren](#configure-the-aggregator) Zeitraum. Der Standardzeitraum beträgt 30 Sekunden oder 1.000 Ergebnisse, je nachdem, was früher eintritt. Beispiele hierfür sind die Ausführungsanzahl, die Erfolgsrate und die Dauer. Alle diese Protokolle werden auf der Stufe `Information` geschrieben. Wenn Sie nach `Warning` oder höheren Stufen filtern, finden Sie keine dieser Daten. |
| **`Host.Executor`** | **traces** | Enthält die **gestartete Funktion** und die **abgeschlossene Funktion** für bestimmte Funktionsausführungen. Bei erfolgreichen Ausführungen haben diese Protokolle die Stufe `Information`. Ausnahmen werden mit der Stufe `Error` protokolliert. Von der Runtime werden auch Protokolle mit der Stufe `Warning` erstellt, z. B. wenn Warteschlangennachrichten an die [Warteschlange für nicht verarbeitete Nachrichten](functions-bindings-storage-queue-trigger.md#poison-messages) gesendet werden.  |
| **`Host.Results`** | **requests** | Diese von der Runtime generierten Protokolle enthalten einen Hinweis zum Erfolg bzw. Misserfolg einer Funktion. Alle diese Protokolle werden auf der Stufe `Information` geschrieben. Wenn Sie nach `Warning` oder höheren Stufen filtern, finden Sie keine dieser Daten. |

---

In der Spalte **Tabelle** ist angegeben, in welche Tabelle in Application Insights das Protokoll geschrieben wird. 

## <a name="configure-log-levels"></a>Konfigurieren von Protokollstufen

[!INCLUDE [functions-log-levels](../../includes/functions-log-levels.md)]

Für jede Kategorie geben Sie zu sendende Mindestprotokollebene an. Die Einstellungen für „host.json“ variieren je nach [Version der Functions-Runtime](functions-versions.md). 

Im folgenden Beispiel wird die Protokollierung anhand der folgenden Regeln definiert:

+ Für Protokolle von `Host.Results` oder `Function` werden nur Ereignisse der Stufe `Error` oder höher protokolliert. 
+ Für Protokolle von `Host.Aggregator` werden alle generierten Metriken protokolliert (`Trace`).
+ Für alle anderen Protokolle, einschließlich der Benutzerprotokolle, werden nur Ereignisse der Stufe `Information` und höher protokolliert.

# <a name="v2x"></a>[v2.x+](#tab/v2)

```json
{
  "logging": {
    "fileLoggingMode": "always",
    "logLevel": {
      "default": "Information",
      "Host.Results": "Error",
      "Function": "Error",
      "Host.Aggregator": "Trace"
    }
  }
}
```

# <a name="v1x"></a>[v1.x](#tab/v1) 

```json
{
  "logger": {
    "categoryFilter": {
      "defaultLevel": "Information",
      "categoryLevels": {
        "Host.Results": "Error",
        "Function": "Error",
        "Host.Aggregator": "Trace"
      }
    }
  }
}
```

---

Wenn [host.json] mehrere Protokolle enthält, die mit der gleichen Zeichenfolge beginnen, werden zuerst die stärker definierten Protokolle abgeglichen. Sehen Sie sich das folgende Beispiel an, bei dem alle Elemente der Runtime, mit Ausnahme von `Host.Aggregator`, mit der Stufe `Error` protokolliert werden:

# <a name="v2x"></a>[v2.x+](#tab/v2)

```json
{
  "logging": {
    "fileLoggingMode": "always",
    "logLevel": {
      "default": "Information",
      "Host": "Error",
      "Function": "Error",
      "Host.Aggregator": "Information"
    }
  }
}
```

# <a name="v1x"></a>[v1.x](#tab/v1) 

```json
{
  "logger": {
    "categoryFilter": {
      "defaultLevel": "Information",
      "categoryLevels": {
        "Host": "Error",
        "Function": "Error",
        "Host.Aggregator": "Information"
      }
    }
  }
}
```

---

Sie können die Protokollstufeneinstellung `None` verwenden, um zu verhindern, dass Protokolle für eine Kategorie geschrieben werden. 

> [!CAUTION]
> Azure Functions lässt sich in Application Insights integrieren, indem Telemetrieereignisse in Application Insights-Tabellen gespeichert werden. Das Festlegen einer Kategorieprotokollebene auf einen anderen Wert als `Information` verhindert, dass die Telemetriedaten in diese Tabellen einfließen. Daher können Sie die zugehörigen Daten weder in Application Insights noch auf der Registerkarte „Überwachung“ für die Funktion anzeigen.
>
> Für die obigen Beispiele gilt Folgendes:
> * Wenn die Kategorie `Host.Results` auf die Protokollebene `Error` festgelegt ist, werden in der Tabelle `requests` nur Telemetrieereignisse zur Hostausführung für fehlerhafte Funktionsausführungen gesammelt. Dadurch wird verhindert, dass Details zu erfolgreichen Hostausführungen in Application Insights und auf der Registerkarte „Überwachung“ für die Funktion angezeigt werden.
> * Wenn die Kategorie `Function` auf die Protokollebene `Error` festgelegt ist, wird das Sammeln von Funktionstelemetriedaten im Zusammenhang mit `dependencies`, `customMetrics` und `customEvents` für alle Funktionen beendet, und diese Daten werden nicht in Application Insights angezeigt. Es werden nur `traces` gesammelt, die mit der Ebene `Error` protokolliert werden. 
>
> In beiden Fällen sammeln Sie weiterhin Fehler- und Ausnahmedaten in Application Insights und auf der „Überwachung“ für die Funktion. Weitere Informationen finden Sie unter [Lösungen mit großen Mengen an Telemetriedaten](#solutions-with-high-volume-of-telemetry).


## <a name="configure-the-aggregator"></a>Konfigurieren des Aggregators

Wie im vorherigen Abschnitt erwähnt, werden von der Laufzeit Daten zu den Funktionsausführungen in einem bestimmten Zeitraum aggregiert. Der Standardzeitraum beträgt 30 Sekunden oder 1.000 Ausführungen, je nachdem, was früher eintritt. Sie können diese Einstellung in der Datei [host.json] konfigurieren.  Hier sehen Sie ein Beispiel:

```json
{
    "aggregator": {
      "batchSize": 1000,
      "flushTimeout": "00:00:30"
    }
}
```

## <a name="configure-sampling"></a>Konfigurieren des Samplings

Application Insights verfügt über ein Feature zur [Stichprobenentnahme](../azure-monitor/app/sampling.md) als Schutz davor, dass bei Spitzenlast zu viele Telemetriedaten für erfolgte Vorgänge produziert werden. Wenn die Rate der eingehenden ausgeführten Vorgänge einen bestimmten Schwellenwert übersteigt, beginnt Application Insights, einige der eingehenden ausgeführten Vorgänge nach dem Zufallsprinzip zu ignorieren. Die Standardeinstellung für die maximale Anzahl ausgeführter Vorgänge pro Sekunde ist 20 (5 in Version 1.x). Sie können das Sampling in der Datei [host.json](./functions-host-json.md#applicationinsights) konfigurieren.  Hier sehen Sie ein Beispiel:

# <a name="v2x"></a>[v2.x+](#tab/v2)

```json
{
  "logging": {
    "applicationInsights": {
      "samplingSettings": {
        "isEnabled": true,
        "maxTelemetryItemsPerSecond" : 20,
        "excludedTypes": "Request;Exception"
      }
    }
  }
}
```


Sie können bestimmte Arten von Telemetriedaten aus der Stichprobenentnahme ausschließen. In diesem Beispiel werden Daten vom Typ `Request` und `Exception` aus der Stichprobenentnahme ausgeschlossen. Hierdurch wird sichergestellt, dass *alle* Funktionsausführungen (Anforderungen) und Ausnahmen protokolliert werden, während für andere Arten von Telemetriedaten weiterhin die Stichprobenentnahme verwendet wird. 

# <a name="v1x"></a>[v1.x](#tab/v1)  

```json
{
  "applicationInsights": {
    "sampling": {
      "isEnabled": true,
      "maxTelemetryItemsPerSecond" : 5
    }
  }
}
```
---

Weitere Informationen finden Sie unter [Stichprobenentnahme in Application Insights](../azure-monitor/app/sampling.md).

## <a name="configure-scale-controller-logs"></a>Konfigurieren von Skalierungscontrollerprotokollen

_Dieses Feature befindet sich in der Vorschauphase._ 

Der [Skalierungscontroller von Azure Functions](./event-driven-scaling.md#runtime-scaling) kann Protokolle an Application Insights oder an den Blobspeicher ausgeben, damit sie die Entscheidungen, die der Skalierungscontroller für Ihre Funktions-App trifft, besser nachvollziehen können.

Um dieses Feature zu aktivieren, fügen Sie den Einstellungen Ihrer Funktions-App eine Anwendungseinstellung mit dem Namen `SCALE_CONTROLLER_LOGGING_ENABLED` hinzu. Der Wert dieser Einstellung muss das Format `<DESTINATION>:<VERBOSITY>` haben und auf Folgendem basieren:

[!INCLUDE [functions-scale-controller-logging](../../includes/functions-scale-controller-logging.md)]

Mit dem folgenden Azure CLI-Befehl wird beispielsweise die ausführliche Protokollierung des Skalierungscontrollers in Application Insights aktiviert:

```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--settings SCALE_CONTROLLER_LOGGING_ENABLED=AppInsights:Verbose
```

Ersetzen Sie in diesem Beispiel `<FUNCTION_APP_NAME>` und `<RESOURCE_GROUP_NAME>` durch den Namen Ihrer Funktions-App bzw. durch den Namen der Ressourcengruppe. 

Durch den folgenden Azure CLI-Befehl wird die Protokollierung deaktiviert, indem die Ausführlichkeit auf `None` festgelegt wird:

```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--settings SCALE_CONTROLLER_LOGGING_ENABLED=AppInsights:None
```

Sie können die Protokollierung auch deaktivieren, indem Sie die Einstellung `SCALE_CONTROLLER_LOGGING_ENABLED` mithilfe des folgenden Azure CLI-Befehls entfernen:

```azurecli-interactive
az functionapp config appsettings delete --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--setting-names SCALE_CONTROLLER_LOGGING_ENABLED
```

Wenn die Skalierungscontrollerprotokollierung aktiviert ist, können Sie jetzt [Ihre Skalierungscontrollerprotokolle abfragen](analyze-telemetry-data.md#query-scale-controller-logs). 

## <a name="enable-application-insights-integration"></a>Aktivieren der Application Insights-Integration

Damit eine Funktionen-App Daten an Application Insights senden kann, muss sie den Instrumentierungsschlüssel einer Application Insights-Ressource kennen. Der Schlüssel muss in der App-Einstellung **APPINSIGHTS_INSTRUMENTATIONKEY** angegeben werden.

Unabhängig davon, ob Sie Ihre Funktions-App [im Azure-Portal](./functions-get-started.md), über die Befehlszeile mithilfe der [Azure Functions Core Tools](./create-first-function-cli-csharp.md) oder mit [Visual Studio Code](./create-first-function-vs-code-csharp.md) erstellen, wird die Integration mit Application Insights automatisch aktiviert. Die Application Insights-Ressource hat den gleichen Namen wie Ihre Funktions-App und wird entweder in der gleichen oder nächstgelegenen Region erstellt.

### <a name="new-function-app-in-the-portal"></a>Neue Funktions-App im Azure-Portal

Zum Überprüfen der Application Insights-Ressource, die erstellt wird, wählen Sie sie aus, um das Fenster **Application Insights** zu erweitern. Sie können den **neuen Ressourcennamen** ändern oder einen anderen **Standort** in einer [Azure-Region](https://azure.microsoft.com/global-infrastructure/geographies/) wählen, in der Sie Ihre Daten speichern möchten.

![Aktivieren von Application Insights beim Erstellen einer Funktionen-App](media/functions-monitoring/enable-ai-new-function-app.png)

Wenn Sie auf **Erstellen** klicken, wird eine Application Insights-Ressource mit Ihrer Funktions-App erstellt, bei der `APPINSIGHTS_INSTRUMENTATIONKEY` in den Anwendungseinstellungen festgelegt ist. Alles ist betriebsbereit.

<a id="manually-connect-an-app-insights-resource"></a>
### <a name="add-to-an-existing-function-app"></a>Ergänzen einer vorhandenen Funktions-App 

Verwenden Sie die folgenden Schritte zum Erstellen der entsprechenden Ressource, falls für Ihre Funktions-App keine Application Insights-Ressource erstellt wurde. Sie können dann den Instrumentierungsschlüssel dieser Ressource Ihrer Funktions-App als [Anwendungseinstellung](functions-how-to-use-azure-function-app-settings.md#settings) hinzufügen.

1. Suchen Sie im [Azure-Portal](https://portal.azure.com) nach **Funktions-App**, und wählen Sie diese Option und dann Ihre Funktions-App aus. 

1. Wählen Sie oben im Fenster das Banner **Application Insights ist nicht konfiguriert** aus. Sollte dieses Banner nicht angezeigt werden, ist Application Insights möglicherweise bereits für Ihre App aktiviert.

    :::image type="content" source="media/configure-monitoring/enable-application-insights.png" alt-text="Aktivieren von Application Insights über das Portal":::

1. Erweitern Sie **Ressource ändern**, und erstellen Sie eine Application Insights-Ressource. Verwenden Sie dazu die Einstellungen, die in der folgenden Tabelle angegeben sind.  

    | Einstellung      | Vorgeschlagener Wert  | BESCHREIBUNG                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Name der neuen Ressource** | Eindeutiger App-Name | Es ist am einfachsten, den gleichen Namen wie für Ihre Funktionen-App zu verwenden, der in Ihrem Abonnement eindeutig sein muss. | 
    | **Location** | Europa, Westen | Verwenden Sie nach Möglichkeit dieselbe [Region](https://azure.microsoft.com/regions/) wie für Ihre Funktions-App (oder eine Region in der Nähe). |

    :::image type="content" source="media/configure-monitoring/ai-general.png" alt-text="Erstellen Sie eine Application Insights-Ressource":::.

1. Wählen Sie **Übernehmen**. 

   Die Application Insights-Ressource wird in derselben Ressourcengruppe und unter demselben Abonnement wie Ihre Funktionen-App erstellt. Schließen Sie nach der Erstellung der Ressource das Application Insights-Fenster.

1. Wählen Sie in Ihrer Funktions-App unter **Einstellungen** die Option **Konfiguration** und dann **Anwendungseinstellungen** aus. Wenn die Einstellung `APPINSIGHTS_INSTRUMENTATIONKEY` angezeigt wird, bedeutet dies, dass die Application Insights-Integration für Ihre unter Azure ausgeführte Funktions-App aktiviert ist. Falls diese Einstellung aus irgendeinem Grund nicht vorhanden ist, müssen Sie sie mit Ihrem Application Insights-Instrumentierungsschlüssel als Wert hinzufügen.

> [!NOTE]
> In früheren Versionen von Azure Functions wurde die integrierte Überwachung verwendet, was nicht mehr empfohlen wird. Wenn Sie die Application Insights-Integration für eine solche Funktions-App aktivieren, müssen Sie auch die [integrierte Protokollierung deaktivieren](#disable-built-in-logging).  

## <a name="disable-built-in-logging"></a>Deaktivieren der integrierten Protokollierung

Wenn Sie Application Insights aktivieren, deaktivieren Sie die integrierte Protokollierung mit Verwendung von Azure Storage. Die integrierte Protokollierung ist für Tests mit einfachen Workloads hilfreich, aber sie ist nicht für die Nutzung in der Produktion mit hohen Auslastungen bestimmt. Für die Produktionsüberwachung empfehlen wir die Verwendung von Application Insights. Bei Nutzung der integrierten Protokollierung in der Produktion kann der Protokollierungsdatensatz aufgrund einer Drosselung von Azure Storage ggf. unvollständig sein.

Löschen Sie die App-Einstellung `AzureWebJobsDashboard`, um die integrierte Protokollierung zu deaktivieren. Informationen zum Löschen von App-Einstellungen im Azure-Portal finden Sie im Abschnitt **Anwendungseinstellungen** unter [Verwalten einer Funktionen-App im Azure-Portal](functions-how-to-use-azure-function-app-settings.md#settings). Stellen Sie vor dem Löschen der App-Einstellung sicher, dass sie nicht für vorhandene Funktionen in derselben Funktions-App für Azure Storage-Trigger oder -Bindungen verwendet wird.

## <a name="solutions-with-high-volume-of-telemetry"></a>Lösungen mit großen Mengen an Telemetriedaten 

Ihre Funktions-Apps können ein wesentlicher Bestandteil von Lösungen sein, die naturgemäß große Mengen an Telemetriedaten verursachen (IoT-Lösungen, ereignisgesteuerte Lösungen, Finanzsysteme mit hoher Auslastung, Integrationssysteme ...). In diesem Fall sollten Sie eine zusätzliche Konfiguration in Betracht ziehen, um Kosten zu senken und gleichzeitig die Beobachtbarkeit aufrechtzuerhalten.

Je nachdem, wie die generierten Telemetriedaten genutzt werden – Echtzeitdashboards, Warnungen, detaillierte Diagnosen usw. –, müssen Sie eine Strategie definieren, um die Menge an generierten Daten zu reduzieren. Mit dieser Strategie können Sie Ihre Funktions-Apps in einer Produktionsumgebung ordnungsgemäß überwachen, betreiben und diagnostizieren. Ziehen Sie folgende Optionen in Betracht:

* **Stichprobenentnahme verwenden**: Wie [oben](#configure-sampling) erwähnt, kann damit die Menge der erfassten Telemetrieereignisse erheblich reduziert werden, während statistisch korrekte Analysen erhalten bleiben. Es kann vorkommen, dass Sie selbst bei Stichprobenentnahmen immer noch eine große Menge an Telemetriedaten erhalten. Überprüfen Sie die Optionen, die die [adaptive Stichprobenerstellung](../azure-monitor/app/sampling.md#configuring-adaptive-sampling-for-aspnet-applications) bietet. Sie können beispielsweise `maxTelemetryItemsPerSecond` auf einen Wert festlegen, der das generierte Volume gegen Ihre Überwachungsanforderungen ausbalanciert. Beachten Sie, dass die Stichprobenentnahme für Telemetrie für jeden Host angewendet wird, der Ihre Funktions-App ausführt. 

* **Standardprotokollebene**: Verwenden Sie `Warning` oder `Error` als Standardwert für alle Telemetriekategorien. Nun können Sie entscheiden, welche [Kategorien](#configure-categories) Sie als `Information` festlegen möchten, damit Sie Ihre Funktionen ordnungsgemäß überwachen und diagnostizieren können.

* **Funktionstelemetrie optimieren**: Wenn die Standardprotokollebene auf `Error` oder `Warning` festgelegt ist, werden keine detaillierten Informationen von jeder Funktion gesammelt (Abhängigkeiten, benutzerdefinierte Metriken, benutzerdefinierte Ereignisse und Ablaufverfolgungen). Definieren Sie für die Funktionen, die für die Produktionsüberwachung von entscheidender Bedeutung sind, einen expliziten Eintrag für die Kategorie `Function.<YOUR_FUNCTION_NAME>`, und legen Sie die Kategorie auf `Information` fest, um ausführliche Informationen sammeln zu können. Um das Sammeln von [benutzergenerierten Protokolle](functions-monitoring.md#writing-to-logs) auf der Ebene `Information` zu vermeiden, legen Sie an diesem Punkt die Kategorie `Function.<YOUR_FUNCTION_NAME>.User` auf die Protokollebene `Error` oder `Warning` fest.

* **Host.Aggregator-Kategorie**: Wie unter [Konfigurieren von Kategorien](#configure-categories)beschrieben, stellt diese Kategorie aggregierte Informationen zu Funktionsaufrufen bereit. Die Informationen aus dieser Kategorie werden in der Application Insights-Tabelle `customMetrics` gesammelt und im Azure-Portal auf der Registerkarte „Übersicht“ für die Funktion angezeigt. Je nachdem, wie Sie den Aggregator konfigurieren, ziehen Sie in Betracht, dass es bei den gesammelten Telemetriedaten zu einer Verzögerung kommt, die von `flushTimeout` bestimmt wird. Wenn Sie diese Kategorie auf einen anderen Wert als `Information` festlegen, wird das Sammeln der Daten in der Tabelle `customMetrics` beendet, und auf der Registerkarte „Übersicht“ für die Funktion werden keine Metriken angezeigt.

  Der folgende Screenshot zeigt Host.Aggregator-Telemetriedaten, die auf der Registerkarte „Übersicht“ für die Funktion angezeigt werden. :::image type="content" source="media/configure-monitoring/host-aggregator-function-overview.png" alt-text="Screenshot: auf der Registerkarte „Übersicht“ für die Funktion angezeigte Host.Aggregator-Telemetriedaten." lightbox="media/configure-monitoring/host-aggregator-function-overview-big.png":::

  Der folgende Screenshot zeigt Host.Aggregator-Telemetriedaten in der Application Insights-Tabelle „customMetrics“.
  :::image type="content" source="media/configure-monitoring/host-aggregator-custom-metrics.png" alt-text="Screenshot: Host.Aggregator-Telemetriedaten in der Application Insights-Tabelle „customMetrics“." lightbox="media/configure-monitoring/host-aggregator-custom-metrics-big.png":::

* **Host.Results-Kategorie**: Wie unter [Konfigurieren von Kategorien](#configure-categories) beschrieben, stellt diese Kategorie die zur Laufzeit generierten Protokolle bereit, die eine erfolgreiche oder fehlerhafte Ausführung eines Funktionsaufrufs angeben. Die Informationen aus dieser Kategorie werden in der Application Insights-Tabelle `requests` gesammelt und auf der Registerkarte „Überwachung“ für die Funktion sowie in verschiedenen Application Insights-Dashboards angezeigt (Leistung, Fehler ...). Wenn Sie diese Kategorie auf einen anderen Wert als `Information` festlegen, werden nur Telemetriedaten gesammelt, die auf der definierten Protokollebene (oder höher) generiert wurden. Durch Festlegung auf `error` werden beispielsweise Anforderungsdaten nur für fehlerhafte Ausführungen nachverfolgt. 

  Der folgende Screenshot zeigt die Host.Results-Telemetriedaten, die auf der Registerkarte „Überwachung“ für die Funktion angezeigt werden. :::image type="content" source="media/configure-monitoring/host-results-function-monitor.png" alt-text="Screenshot: Host.Results-Telemetriedaten auf der Registerkarte „Überwachung“ für die Funktion." lightbox="media/configure-monitoring/host-results-function-monitor-big.png":::

  Der folgende Screenshot zeigt die Host.Results-Telemetriedaten, die im Application Insights-Dashboard „Leistung“ angezeigt werden.
  :::image type="content" source="media/configure-monitoring/host-results-application-insights.png" alt-text="Screenshot: Host.Results-Telemetriedaten im Application Insights-Dashboard „Leistung“." lightbox="media/configure-monitoring/host-results-application-insights-big.png":::

* **Host.Aggregator im Vergleich zu Host.Results**: Beide Kategorien bieten gute Erkenntnisse zu Funktionsausführungen. Bei Bedarf können Sie die ausführlichen Informationen aus einer dieser Kategorien entfernen, sodass Sie die andere für Überwachung und Warnungen verwenden können.
Hier ein Beispiel:
# <a name="v2x"></a>[Ab v2.x](#tab/v2)

``` json
{
  "version": "2.0",  
  "logging": {
    "logLevel": {
      "default": "Warning",
      "Function": "Error",
      "Host.Aggregator": "Error",
      "Host.Results": "Information", 
      "Function.Function1": "Information",
      "Function.Function1.User": "Error"
    },
    "applicationInsights": {
      "samplingSettings": {
        "isEnabled": true,
        "maxTelemetryItemsPerSecond": 1,
        "excludedTypes": "Exception"
      }
    }
  }
} 
```
# <a name="v1x"></a>[v1.x](#tab/v1) 
```json
{
  "logger": {
    "categoryFilter": {
      "defaultLevel": "Warning",
      "categoryLevels": {
        "Function": "Error",
        "Host.Aggregator": "Error",
        "Host.Results": "Information",
        "Host.Executor": "Warning"
      }
    }
  },
  "applicationInsights": {
    "sampling": {
      "isEnabled": true,
      "maxTelemetryItemsPerSecond" : 5
    }
  }
}
```
---

Diese Konfiguration bietet Folgendes:

* Der Standardwert für alle Funktionen und Telemetriekategorien ist auf `Warning` festgelegt (einschließlich Microsoft- und Workerkategorien), sodass standardmäßig alle Fehler und Warnungen gesammelt werden, die sowohl von der Runtime als auch von der benutzerdefinierten Protokollierung generiert werden. 

* Die Kategorieprotokollebene `Function` ist auf `Error` festgelegt, sodass für alle Funktionen standardmäßig nur Ausnahmen und Fehlerprotokolle gesammelt werden (Abhängigkeiten sowie benutzergenerierte Metriken und Ereignisse werden übersprungen).

* Da die Kategorie `Host.Aggregator` auf die Protokollebene `Error` festgelegt ist, werden in der Application Insights-Tabelle `customMetrics` keine aggregierten Informationen aus Funktionsaufrufen gesammelt, und im Dashboard der Funktionsübersicht werden keine Informationen zur Anzahl von Ausführungen (Gesamtanzahl, erfolgreich, Fehler ...) angezeigt.

* Für die Kategorie `Host.Results` werden alle Informationen zur Hostausführung in der Application Insights-Tabelle `requests` gesammelt. Alle Aufrufergebnisse werden im Dashboard der Funktionsüberwachung und in Application Insights-Dashboards angezeigt.

* Für die Funktion namens `Function1` haben wir die Protokollebene auf `Information` festgelegt, sodass für diese konkrete Funktion alle Telemetriedaten gesammelt werden (Abhängigkeit, benutzerdefinierte Metriken, benutzerdefinierte Ereignisse). Für dieselbe Funktion wird die Kategorie `Function1.User` (vom Benutzer generierte Ablaufverfolgungen) auf `Error` festgelegt, sodass nur die benutzerdefinierte Fehlerprotokollierung gesammelt wird. Beachten Sie, dass die funktionsbasierte Konfiguration in v1.x nicht unterstützt wird. 

* Die Stichprobenentnahme ist so konfiguriert, dass pro Sekunde und Typ ein Telemetrieelement gesendet wird, außer für Ausnahmen. Diese Stichprobenentnahme erfolgt für jeden Serverhost, auf dem unsere Funktions-App ausgeführt wird. Wenn wir also über vier Instanzen verfügen, gibt diese Konfiguration vier Telemetrieelemente pro Sekunde und Typ sowie alle Ausnahmen aus, die möglicherweise auftreten. Beachten Sie Folgendes: Die Anzahl von Metrikdaten wie z. B. die Anforderungs- und Ausnahmerate wird zum Kompensieren der Stichprobenrate angepasst, sodass im Metrik-Explorer annähernd korrekte Werte angezeigt werden.  

> [!TIP]
> Experimentieren Sie mit verschiedenen Konfigurationen, um Ihre Anforderungen an Protokollierung, Überwachung und Warnungen zu erfüllen. Stellen Sie sicher, dass Sie über detaillierte Diagnosen für unerwartete Fehler oder Fehlfunktionen verfügen.

### <a name="overriding-monitoring-configuration-at-runtime"></a>Außerkraftsetzen der Überwachungskonfiguration zur Laufzeit
Es kann Situationen geben, in denen Sie das Protokollierungsverhalten einer bestimmten Kategorie in der Produktionsumgebung schnell ändern müssen und Sie nicht nur für eine Änderung in der Datei `host.json` eine vollständige Bereitstellung erstellen möchten. In solchen Fällen können Sie die [Werte in der Datei „host.json“](functions-host-json.md#override-hostjson-values) außer Kraft setzen.


Um diese Werte auf App-Einstellungsebene zu konfigurieren (und eine erneute Bereitstellung nur bei Änderungen an „host.json“ zu vermeiden), sollten Sie bestimmte `host.json`-Werte außer Kraft setzen, indem Sie einen entsprechenden Wert als Anwendungseinstellung erstellen. Wenn die Runtime eine Anwendungseinstellung im Format `AzureFunctionsJobHost__path__to__setting` ermittelt, wird die entsprechende `host.json`-Einstellung in der host.json-Datei unter `path.to.setting` außer Kraft gesetzt. Bei der Angabe als Anwendungseinstellung wird der Punkt (`.`), mit dem die JSON-Hierarchie angegeben wird, durch einen doppelten Unterstrich (`__`) ersetzt. Sie können Sie die folgenden App-Einstellungen beispielsweise verwenden, um einzelne Funktionsprotokollebenen zu konfigurieren, wie oben in `host.json` geschehen.


| Host.json-Pfad | App-Einstellung |
|----------------|-------------|
| logging.logLevel.default  | AzureFunctionsJobHost__logging__logLevel__default  |
| logging.logLeve.Host.Aggregator | AzureFunctionsJobHost__logging__logLevel__Host__Aggregator |
| logging.logLevel.Function | AzureFunctionsJobHost__logging__logLevel__Function |
| logging.logLevel.Function.Function1 | AzureFunctionsJobHost__logging__logLevel__Function1 |
| logging.logLevel.Function.Function1.User | AzureFunctionsJobHost__logging__logLevel__Function1.User |


Sie können die Einstellungen direkt im Azure-Portal auf dem Blatt „Konfiguration von Funktions-App“ oder mithilfe eines Azure CLI-Befehls oder PowerShell-Skripts überschreiben.

# <a name="az-cli"></a>[az cli](#tab/v2)
```azurecli-interactive
az functionapp config appsettings set --name MyFunctionApp --resource-group MyResourceGroup --settings "AzureFunctionsJobHost__logging__logLevel__Host__Aggregator=Information"
```
# <a name="powershell"></a>[PowerShell](#tab/v1) 
```powershell
Update-AzFunctionAppSetting -Name MyAppName -ResourceGroupName MyResourceGroupName -AppSetting @{"AzureFunctionsJobHost__logging__logLevel__Host__Aggregator" = "Information"}
```
---

> [!NOTE]
> Wenn Sie die `host.json`-Datei durch Ändern der App-Einstellungen außer Kraft setzen, wird Ihre Funktions-App neu gestartet.
 
## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Überwachung finden Sie unter folgenden Themen:

+ [Überwachen von Azure Functions](functions-monitoring.md)
+ [Analysieren von Azure Functions-Telemetriedaten in Application Insights](analyze-telemetry-data.md)
+ [Application Insights](/azure/application-insights/)


[host.json]: functions-host-json.md
