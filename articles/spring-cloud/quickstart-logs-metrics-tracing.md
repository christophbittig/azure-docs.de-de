---
title: 'Schnellstart: Überwachen von Azure Spring Cloud-Apps mit Protokollen, Metriken und Ablaufverfolgung'
description: Verwenden Sie Protokollstreaming, Protokollanalyse, Metriken und Ablaufverfolgung, um PetClinic-Beispiel-Apps in Azure Spring Cloud zu überwachen.
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 04/23/2021
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 4ff1666222eedecd944b94de428c7666b0998d03
ms.sourcegitcommit: 0396ddf79f21d0c5a1f662a755d03b30ade56905
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/17/2021
ms.locfileid: "122271572"
---
# <a name="quickstart-monitoring-azure-spring-cloud-apps-with-logs-metrics-and-tracing"></a>Schnellstart: Überwachen von Azure Spring Cloud-Apps mit Protokollen, Metriken und Ablaufverfolgung

::: zone pivot="programming-language-csharp"
Mit der integrierten Überwachungsfunktion in Azure Spring Cloud können Sie komplexe Probleme debuggen und überwachen. Azure Spring Cloud integriert die [verteilte Ablaufverfolgung](https://docs.steeltoe.io/api/v3/tracing/) von Steeltoe in [Azure Application Insights](../azure-monitor/app/app-insights-overview.md). Diese Integration bietet leistungsstarke Protokolle, Metriken und Funktionen für die Ablaufverfolgung aus dem Azure-Portal.

In den folgenden Verfahren wird erläutert, wie Sie Protokollstreaming, die Protokollanalyse, Metriken und die verteilte Ablaufverfolgung mit der Beispiel-App verwenden, die Sie in den vorherigen Schnellstartanleitungen bereitgestellt haben.

## <a name="prerequisites"></a>Voraussetzungen

* Absolvieren Sie die vorherigen Schnellstarts in dieser Serie:

  * [Bereitstellen des Azure Spring Cloud-Diensts](./quickstart-provision-service-instance.md)
  * [Einrichten des Azure Spring Cloud-Konfigurationsservers](./quickstart-setup-config-server.md)
  * [Erstellen und Bereitstellen von Apps](./quickstart-deploy-apps.md)

## <a name="logs"></a>Protokolle

Es gibt zwei Möglichkeiten, um Protokolle in Azure Spring Cloud anzuzeigen: **Protokollstreaming** von Echtzeitprotokollen pro App-Instanz oder **Protokollanalyse** für aggregierte Protokolle mit erweiterter Abfragefunktion.

### <a name="log-streaming"></a>Protokollstreaming

Sie können mit dem folgenden Befehl Protokollstreaming in der Azure CLI verwenden:

```azurecli
az spring-cloud app logs -n solar-system-weather -f
```

Es wird eine Ausgabe angezeigt, die in etwa wie im folgenden Beispiel aussieht:

```output
=> ConnectionId:0HM2HOMHT82UK => RequestPath:/weatherforecast RequestId:0HM2HOMHT82UK:00000003, SpanId:|e8c1682e-46518cc0202c5fd9., TraceId:e8c1682e-46518cc0202c5fd9, ParentId: => Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.Controllers.WeatherForecastController.Get (Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather)
Executing action method Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.Controllers.WeatherForecastController.Get (Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather) - Validation state: Valid
←[40m←[32minfo←[39m←[22m←[49m: Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.Controllers.WeatherForecastController[0]

=> ConnectionId:0HM2HOMHT82UK => RequestPath:/weatherforecast RequestId:0HM2HOMHT82UK:00000003, SpanId:|e8c1682e-46518cc0202c5fd9., TraceId:e8c1682e-46518cc0202c5fd9, ParentId: => Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.Controllers.WeatherForecastController.Get (Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather)
Retrieved weather data from 4 planets
←[40m←[32minfo←[39m←[22m←[49m: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[2]

=> ConnectionId:0HM2HOMHT82UK => RequestPath:/weatherforecast RequestId:0HM2HOMHT82UK:00000003, SpanId:|e8c1682e-46518cc0202c5fd9., TraceId:e8c1682e-46518cc0202c5fd9, ParentId: => Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.Controllers.WeatherForecastController.Get (Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather)
Executing ObjectResult, writing value of type 'System.Collections.Generic.KeyValuePair`2[[System.String, System.Private.CoreLib, Version=4.0.0.0, Culture=neutral, PublicKeyToken=7cec85d7bea7798e],[System.String, System.Private.CoreLib, Version=4.0.0.0, Culture=neutral, PublicKeyToken=7cec85d7bea7798e]][]'.
←[40m←[32minfo←[39m←[22m←[49m: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[2]
```

> [!TIP]
> Verwenden Sie `az spring-cloud app logs -h`, um weitere Parameter und Funktionen für Protokolldatenströme zu untersuchen.

### <a name="log-analytics"></a>Log Analytics

1. Navigieren Sie im Azure-Portal zur Seite **Dienst | Übersicht**, und wählen Sie im Abschnitt **Überwachung** die Option **Protokolle** aus. Wählen Sie in einer der Beispielabfragen für Azure Spring Cloud **Ausführen** aus.

   [ ![Protokollanalyseeintrag](media/spring-cloud-quickstart-logs-metrics-tracing/logs-entry.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-entry.png#lightbox)

1. Bearbeiten Sie die Abfrage, um die Where-Klauseln zu entfernen, die die Anzeige auf Warnungs- und Fehlerprotokolle beschränken.

1. Wählen Sie anschließend `Run` aus, und Protokolle werden angezeigt. Weitere Informationen zum Schreiben von Abfragen finden Sie unter [Erste Schritte mit Protokollabfragen in Azure Monitor](../azure-monitor/logs/get-started-queries.md).

   [ ![Protokollanalyseabfrage: Steeltoe](media/spring-cloud-quickstart-logs-metrics-tracing/logs-query-steeltoe.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-query-steeltoe.png#lightbox)

1. Weitere Informationen zu der in Log Analytics verwendeten Abfragesprache erhalten Sie unter [Azure Monitor-Protokollabfragen](/azure/data-explorer/kusto/query/). Wenn Sie alle Log Analytics-Protokolle über einen zentralisierten Client abfragen möchten, lesen Sie die Informationen unter [Abfragen von Daten in Azure Monitor mit Azure Data Explorer (Vorschau)](/azure/data-explorer/query-monitor-data).

## <a name="metrics"></a>Metriken

1. Navigieren Sie im Azure-Portal zur Seite **Dienst | Übersicht**, und wählen Sie im Abschnitt **Überwachung** die Option **Metriken** aus. Fügen Sie Ihre erste Metrik hinzu, indem Sie eine der .NET-Metriken unter **Leistung (.NET)** oder **Anforderung (.NET)** im Dropdown **Metrik** und `Avg` für **Aggregation** auswählen, um die Zeitachse für diese Metrik anzuzeigen.

   [ ![Eintrag „Metriken“: Steeltoe](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-basic-cpu-steeltoe.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-basic-cpu-steeltoe.png#lightbox)

1. Klicken Sie auf der Symbolleiste auf **Filter hinzufügen**, und wählen Sie `App=solar-system-weather` aus, um die CPU-Auslastung nur für die App **solar-system-weather** anzuzeigen.

   [ ![Verwenden eines Filters in Metriken: Steeltoe](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-filter-steeltoe.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-filter-steeltoe.png#lightbox)

1. Schließen Sie den im vorherigen Schritt erstellten Filter, wählen Sie **Apply Splitting** (Aufteilung anwenden) und dann `App` für **Werte** aus, um die CPU-Auslastung auf der Grundlage verschiedener Apps anzuzeigen.

   [ ![Anwenden der Aufteilung in Metriken: Steeltoe](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-split-steeltoe.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-split-steeltoe.png#lightbox)

## <a name="distributed-tracing"></a>Verteilte Ablaufverfolgung

1. Navigieren Sie im Azure-Portal zur Seite **Dienst | Übersicht**, und wählen Sie im Abschnitt **Überwachung** die Option **Verteilte Ablaufverfolgung** aus. Wählen Sie dann auf der rechten Seite die Registerkarte **Anwendungsübersicht anzeigen** aus.

   [ ![Eintrag der verteilten Ablaufverfolgung: Steeltoe](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-entry.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-entry.png#lightbox)

1. Nun können Sie den Status von Aufrufen zwischen Apps anzeigen.

   [ ![Übersicht über verteilte Ablaufverfolgung: Steeltoe](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-overview-steeltoe.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-overview-steeltoe.png#lightbox)

1. Wählen Sie den Link zwischen **solar-system-weather** und **planet-weather-provider** aus, um weitere Details (etwa langsamste Aufrufe nach HTTP-Methoden) anzuzeigen.

   [ ![Verteilte Ablaufverfolgung: Steeltoe](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-call-steeltoe.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-call-steeltoe.png#lightbox)

1. Wählen Sie abschließend **Leistung untersuchen** aus, um leistungsfähigere integrierte Leistungsanalysen zu untersuchen.

   [ ![Leistung der verteilten Ablaufverfolgung: Steeltoe](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-performance-steeltoe.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-performance-steeltoe.png#lightbox)
::: zone-end

::: zone pivot="programming-language-java"
Mit der integrierten Überwachungsfunktion in Azure Spring Cloud können Sie komplexe Probleme debuggen und überwachen. Azure Spring Cloud integriert [Spring Cloud Sleuth](https://spring.io/projects/spring-cloud-sleuth) in [Azure Application Insights](../azure-monitor/app/app-insights-overview.md). Diese Integration bietet leistungsstarke Protokolle, Metriken und Funktionen für die Ablaufverfolgung aus dem Azure-Portal. In den folgenden Verfahren wird erläutert, wie Sie Protokollstreaming, die Protokollanalyse, Metriken und die verteilte Ablaufverfolgung mit bereitgestellten PetClinic-Apps verwenden.

## <a name="prerequisites"></a>Voraussetzungen

Schließen Sie die vorherigen Schritte ab:

* [Bereitstellen einer Instanz von Azure Spring Cloud](./quickstart-provision-service-instance.md)
* [Einrichten des Konfigurationsservers](./quickstart-setup-config-server.md)
* [Erstellen und Bereitstellen von Apps](./quickstart-deploy-apps.md)

## <a name="logs"></a>Protokolle

Es gibt zwei Möglichkeiten, um Protokolle in Azure Spring Cloud anzuzeigen: **Protokollstreaming** von Echtzeitprotokollen pro App-Instanz oder **Protokollanalyse** für aggregierte Protokolle mit erweiterter Abfragefunktion.

### <a name="log-streaming"></a>Protokollstreaming

#### <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/Azure-CLI)

Sie können mit dem folgenden Befehl Protokollstreaming in der Azure CLI verwenden:

```azurecli
az spring-cloud app logs -s <service instance name> -g <resource group name> -n gateway -f
```

Protokolle wie das folgende werden angezeigt:

[ ![Protokollstreaming über die Azure CLI](media/spring-cloud-quickstart-logs-metrics-tracing/logs-streaming-cli.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-streaming-cli.png#lightbox)

> [!TIP]
> Verwenden Sie `az spring-cloud app logs -h`, um weitere Parameter und Funktionen für Protokolldatenströme zu untersuchen.

Weitere Informationen zu der in Log Analytics verwendeten Abfragesprache erhalten Sie unter [Azure Monitor-Protokollabfragen](/azure/data-explorer/kusto/query/). Wenn Sie alle Log Analytics-Protokolle über einen zentralisierten Client abfragen möchten, lesen Sie die Informationen unter [Abfragen von Daten in Azure Monitor mit Azure Data Explorer](/azure/data-explorer/query-monitor-data).

#### <a name="intellij"></a>[IntelliJ](#tab/IntelliJ)

So rufen Sie die Protokolle mit dem Azure-Toolkit für IntelliJ ab:

1. Wählen Sie **Azure-Explorer** und dann **Spring Cloud** aus.

1. Klicken Sie mit der rechten Maustaste auf die ausgeführte App.

1. Wählen Sie in der Dropdownliste **Streamingprotokolle** aus.

   ![Streamingprotokolle auswählen](media/spring-cloud-intellij-howto/streaming-logs.png)

1. Wählen Sie **Instanz** aus.

   ![Instanz auswählen](media/spring-cloud-intellij-howto/select-instance.png)

1. Das Streamingprotokoll wird im Ausgabefenster angezeigt.

   ![Ausgabe des Streamingprotokolls](media/spring-cloud-intellij-howto/streaming-log-output.png)

 Weitere Informationen zu der in Log Analytics verwendeten Abfragesprache erhalten Sie unter [Azure Monitor-Protokollabfragen](/azure/data-explorer/kusto/query/). Wenn Sie alle Log Analytics-Protokolle über einen zentralisierten Client abfragen möchten, lesen Sie die Informationen unter [Abfragen von Daten in Azure Monitor mit Azure Data Explorer](/azure/data-explorer/query-monitor-data).

---

### <a name="log-analytics"></a>Log Analytics

1. Navigieren Sie zur Seite **Dienst | Übersicht**, und wählen Sie im Abschnitt **Überwachung** die Option **Protokolle** aus. Wählen Sie in einer der Beispielabfragen für Azure Spring Cloud **Ausführen** aus.

   [ ![Eintrag im Logs Analytics-Portal](media/spring-cloud-quickstart-logs-metrics-tracing/update-logs-metrics-tracing/logs-entry.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/update-logs-metrics-tracing/logs-entry.png#lightbox)

1. Anschließend werden gefilterte Protokolle angezeigt. Weitere Informationen zum Schreiben von Abfragen finden Sie unter [Erste Schritte mit Protokollabfragen in Azure Monitor](../azure-monitor/logs/get-started-queries.md).

   [ ![Protokollanalyseabfrage](media/spring-cloud-quickstart-logs-metrics-tracing/update-logs-metrics-tracing/logs-query.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/update-logs-metrics-tracing/logs-query.png#lightbox)

## <a name="metrics"></a>Metriken

Navigieren Sie zum Blatt `Application insights`. Navigieren Sie dann zum Blatt `Metrics`. Dort können Sie von Spring Boot-Apps, Spring Cloud-Modulen und Abhängigkeiten bereitgestellte Metriken anzeigen. 

Das folgende Diagramm zeigt `gateway-requests` (Spring Cloud-Gateway), `hikaricp_connections` (JDBC-Verbindungen) und `http_client_requests`.

[ ![Blatt „Metriken“](media/spring-cloud-quickstart-logs-metrics-tracing/update-logs-metrics-tracing/petclinic-microservices-metrics.jpg) ](media/spring-cloud-quickstart-logs-metrics-tracing/update-logs-metrics-tracing/petclinic-microservices-metrics.jpg#lightbox)

Spring Boot registriert zahlreiche Kernmetriken: JVM, CPU, Tomcat, Logback usw. Die automatische Spring Boot-Konfiguration ermöglicht die Instrumentierung von Anforderungen, die von Spring MVC verarbeitet werden.
Diese drei REST-Controller `OwnerResource`, `PetResource` und `VisitResource` wurden von der Micrometeranmerkung `@Timed` auf Klassenebene instrumentiert.

* Für die Anwendung `customers-service` sind die folgenden benutzerdefinierten Metriken aktiviert:
  * @Timed: `petclinic.owner`
  * @Timed: `petclinic.pet`
* Für die Anwendung `visits-service` sind die folgenden benutzerdefinierten Metriken aktiviert:
  * @Timed: `petclinic.visit`

Sie können diese benutzerdefinierten Metriken auf dem Blatt `Metrics` anzeigen: [ ![Benutzerdefinierte Metriken](media/spring-cloud-quickstart-logs-metrics-tracing/update-logs-metrics-tracing/petclinic-microservices-custom-metrics.jpg) ](media/spring-cloud-quickstart-logs-metrics-tracing/update-logs-metrics-tracing/petclinic-microservices-custom-metrics.jpg#lightbox)

Sie können das Feature „Verfügbarkeitstest“ in Application Insights verwenden und die Verfügbarkeit von Anwendungen überwachen:

[ ![Verfügbarkeitstest](media/spring-cloud-quickstart-logs-metrics-tracing/update-logs-metrics-tracing/petclinic-microservices-availability.jpg) ](media/spring-cloud-quickstart-logs-metrics-tracing/update-logs-metrics-tracing/petclinic-microservices-availability.jpg#lightbox)

Navigieren Sie zum Blatt `Live Metrics`: Auf dem Bildschirm werden Livemetriken mit geringer Wartezeit (unter einer Sekunde) angezeigt: [ ![Livemetriken](media/spring-cloud-quickstart-logs-metrics-tracing/update-logs-metrics-tracing/petclinic-microservices-live-metrics.jpg) ](media/spring-cloud-quickstart-logs-metrics-tracing/update-logs-metrics-tracing/petclinic-microservices-live-metrics.jpg#lightbox)

## <a name="tracing"></a>Ablaufverfolgung

Öffnen Sie die von Azure Spring Cloud erstellte Application Insights-Instanz, und beginnen Sie mit der Überwachung von Microserviceanwendungen.

Navigieren Sie zum Blatt `Application Map`: [ ![Anwendungsübersicht](media/spring-cloud-quickstart-logs-metrics-tracing/update-logs-metrics-tracing/distributed-tracking-new-ai-agent.jpg) ](media/spring-cloud-quickstart-logs-metrics-tracing/update-logs-metrics-tracing/distributed-tracking-new-ai-agent.jpg#lightbox)

Navigieren Sie zum Blatt `Performance`: [ ![Blatt „Leistung“](media/spring-cloud-quickstart-logs-metrics-tracing/update-logs-metrics-tracing/petclinic-microservices-performance.jpg) ](media/spring-cloud-quickstart-logs-metrics-tracing/update-logs-metrics-tracing/petclinic-microservices-performance.jpg#lightbox)

Navigieren Sie zum Blatt `Performance/Dependenices`: Die Leistungszahl für Abhängigkeiten, insbesondere SQL-Aufrufe, wird angezeigt: [ ![Blatt „Leistung“ > „Abhängigkeiten“](media/spring-cloud-quickstart-logs-metrics-tracing/update-logs-metrics-tracing/petclinic-microservices-insights-on-dependencies.jpg) ](media/spring-cloud-quickstart-logs-metrics-tracing/update-logs-metrics-tracing/petclinic-microservices-insights-on-dependencies.jpg#lightbox)

Wählen Sie einen SQL-Aufruf aus, um die End-to-End-Transaktion im Kontext anzuzeigen: [ ![SQL-End-to-End-Transaktion](media/spring-cloud-quickstart-logs-metrics-tracing/update-logs-metrics-tracing/petclinic-microservices-end-to-end-transaction-details.jpg) ](media/spring-cloud-quickstart-logs-metrics-tracing/update-logs-metrics-tracing/petclinic-microservices-end-to-end-transaction-details.jpg#lightbox)

Navigieren Sie zum Blatt `Failures/Exceptions`: Die Auflistung der Ausnahmen wird angezeigt: [ ![Fehler/Ausnahmen](media/spring-cloud-quickstart-logs-metrics-tracing/update-logs-metrics-tracing/petclinic-microservices-failures-exceptions.png) ](media/spring-cloud-quickstart-logs-metrics-tracing/update-logs-metrics-tracing/petclinic-microservices-failures-exceptions.png#lightbox)

Wählen Sie eine Ausnahme aus, um die End-to-End-Transaktion und StackTrace im Kontext anzuzeigen: [ ![StackTrace (End-to-End)](media/spring-cloud-quickstart-logs-metrics-tracing/update-logs-metrics-tracing/end-to-end-transaction-details.jpg) ](media/spring-cloud-quickstart-logs-metrics-tracing/update-logs-metrics-tracing/end-to-end-transaction-details.jpg#lightbox)

::: zone-end

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

In diesen Schnellstartanleitungen haben Sie Azure-Ressourcen erstellt, für die weiterhin Gebühren anfallen, falls sie in Ihrem Abonnement verbleiben. Wenn Sie diese Ressourcen in Zukunft nicht mehr benötigen, löschen Sie die Ressourcengruppe über das Portal oder durch Ausführen des folgenden Befehls in Cloud Shell:

```azurecli
az group delete --name <your resource group name; for example: helloworld-1558400876966-rg> --yes
```

Im Rahmen einer vorherigen Schnellstartanleitung haben Sie auch den Namen der Standardressourcengruppe festgelegt. Wenn Sie nicht mit der nächsten Schnellstartanleitung fortfahren möchten, löschen Sie diesen Standardwert, indem Sie den folgenden CLI-Befehl ausführen:

```azurecli
az config set defaults.group=
```

## <a name="next-steps"></a>Nächste Schritte

Informationen zu weiteren Überwachungsfunktionen von Azure Spring Cloud finden Sie in den folgenden Artikeln:

> [!div class="nextstepaction"]
> [Diagnosedienste](diagnostic-services.md)
>
> [Verteilte Ablaufverfolgung](./how-to-distributed-tracing.md)
>
> [Streamen von Protokollen in Echtzeit](./how-to-log-streaming.md)
