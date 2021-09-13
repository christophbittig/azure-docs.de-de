---
title: Erfassen von Metriken zum Spring Cloud Resilience4J-Trennschalter mit Micrometer
description: Vorgehensweise beim Erfassen von Metriken zum Spring Cloud Resilience4J-Trennschalter mit Micrometer in Azure Spring Cloud.
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: how-to
ms.date: 12/15/2020
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: dae08798aa7b3bc1937295e0cd7701446bd7259e
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122356176"
---
# <a name="collect-spring-cloud-resilience4j-circuit-breaker-metrics-with-micrometer-preview"></a>Erfassen von Metriken zum Spring Cloud Resilience4J-Trennschalter mit Micrometer (Vorschau)

In diesem Dokument wird erklärt, wie Sie Metriken zum Spring Cloud Resilience4J-Trennschalter mit dem Java-In-Process-Agent Application Insights erfassen. Mit diesem Feature können Sie Metriken zum Resilience4J-Trennschalter von Application Insights mit Micrometer überwachen.

In [spring-cloud-circuit-breaker-demo](https://github.com/spring-cloud-samples/spring-cloud-circuitbreaker-demo) wird veranschaulicht, wie dies funktioniert.

## <a name="prerequisites"></a>Voraussetzungen

* Aktivieren Sie den Java-In-Process-Agent entsprechend der Anleitung [Leitfaden zum Java In-Process Agent für Application Insights](./how-to-application-insights.md#enable-java-in-process-agent-for-application-insights).
* Aktivieren Sie die Dimensionssammlung für Resilience4j-Metriken aus dem [Leitfaden zu Application Insights](../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation).
* Installieren Sie git, Maven und Java, falls diese auf dem Entwicklungscomputer noch nicht verwendet werden.

## <a name="build-and-deploy-apps"></a>Erstellen und Bereitstellen von Apps

Mit dem folgenden Verfahren werden Apps erstellt und bereitgestellt.

1. Klonen und erstellen Sie das Demorepository.

```bash
git clone https://github.com/spring-cloud-samples/spring-cloud-circuitbreaker-demo.git
cd spring-cloud-circuitbreaker-demo && mvn clean package -DskipTests
```

2. Erstellen von Anwendungen mit Endpunkten

```azurecli
az spring-cloud app create --name resilience4j --assign-endpoint \
    -s ${asc-service-name} -g ${asc-resource-group}
az spring-cloud app create --name reactive-resilience4j --assign-endpoint \
    -s ${asc-service-name} -g ${asc-resource-group}
```

3. Bereitstellen von Anwendungen.

```azurecli
az spring-cloud app deploy -n resilience4j \
    --jar-path ./spring-cloud-circuitbreaker-demo-resilience4j/target/spring-cloud-circuitbreaker-demo-resilience4j-0.0.1.BUILD-SNAPSHOT.jar \
    -s ${service_name} -g ${resource_group}
az spring-cloud app deploy -n reactive-resilience4j \
    --jar-path ./spring-cloud-circuitbreaker-demo-reactive-resilience4j/target/spring-cloud-circuitbreaker-demo-reactive-resilience4j-0.0.1.BUILD-SNAPSHOT.jar \
    -s ${service_name} -g ${resource_group}
```

> [!Note]
>
> * Binden Sie die erforderliche Abhängigkeit für Resilience4j ein:
>
>   ```xml
>   <dependency>
>       <groupId>io.github.resilience4j</groupId>
>       <artifactId>resilience4j-micrometer</artifactId>
>   </dependency>
>   <dependency>
>       <groupId>org.springframework.cloud</groupId>
>       <artifactId>spring-cloud-starter-circuitbreaker-resilience4j</artifactId>
>   </dependency>
>   ```
>
> * Der Kundencode muss die API von `CircuitBreakerFactory` verwenden, die als `bean` implementiert ist und automatisch erstellt wird, wenn Sie einen Spring Cloud-Trennschalter-Starter einbinden. Weitere Informationen finden Sie unter [Spring Cloud-Trennschalter](https://spring.io/projects/spring-cloud-circuitbreaker#overview).
>
> * Die folgenden 2 Abhängigkeiten führen zu Konflikten mit Resilience4J-Paketen oben.  Stellen Sie sicher, dass der Kunde Sie nicht einbindet.
>
>   ```xml
>   <dependency>
>       <groupId>org.springframework.cloud</groupId>
>       <artifactId>spring-cloud-starter-sleuth</artifactId>
>   </dependency>
>   <dependency>
>       <groupId>org.springframework.cloud</groupId>
>       <artifactId>spring-cloud-starter-zipkin</artifactId>
>   </dependency>
>   ```
>
>
> Navigieren Sie zu der von Gatewayanwendungen bereitgestellten URL, und greifen Sie wie folgt auf den Endpunkt von [spring-cloud-circuit-breaker-demo](https://github.com/spring-cloud-samples/spring-cloud-circuitbreaker-demo) zu:
>
>   ```console
>   /get
>   /get/delay/{seconds}
>   /get/fluxdelay/{seconds}
>   ```

## <a name="locate-resilence4j-metrics-from-portal"></a>Suchen von Resilence4j-Metriken im Portal

1. Wählen Sie im Azure Spring Cloud-Portal das Blatt **Application Insights** und dann **Application Insights** aus.

   [ ![resilience4J 0](media/spring-cloud-resilience4j/resilience4J-0.png)](media/spring-cloud-resilience4j/resilience4J-0.PNG)

2. Wählen Sie auf der Seite **Application Insights** die Option **Metriken** aus.  Wählen Sie **azure.applicationinsights** unter **Metriknamespace** aus.  Wählen Sie außerdem die Metrik **resilience4j_circuitbreaker_buffered_calls** mit **Mittelwert** aus.

   [ ![resilience4J 1](media/spring-cloud-resilience4j/resilience4J-1.png)](media/spring-cloud-resilience4j/resilience4J-1.PNG)

3. Wählen Sie die Metrik **resilience4j_circuitbreaker_calls** und **Mittelwert** aus.

   [ ![resilience4J 2](media/spring-cloud-resilience4j/resilience4J-2.png)](media/spring-cloud-resilience4j/resilience4J-2.PNG)

4. Wählen Sie die Metrik **resilience4j_circuitbreaker_calls** und **Mittelwert** aus. Wählen Sie **Filter hinzufügen** und dann **createNewAccount** als Namen aus.

   [ ![resilience4J 3](media/spring-cloud-resilience4j/resilience4J-3.png)](media/spring-cloud-resilience4j/resilience4J-3.PNG)

5. Wählen Sie die Metrik **resilience4j_circuitbreaker_calls** und **Mittelwert** aus.  Wählen Sie dann **Teilung anwenden** und **Typ** aus.

   [ ![resilience4J 4](media/spring-cloud-resilience4j/resilience4J-4.png)](media/spring-cloud-resilience4j/resilience4J-4.PNG)

6. Wählen Sie die Metriken **resilience4j_circuitbreaker_calls**,**resilience4j_circuitbreaker_buffered_calls** und **resilience4j_circuitbreaker_slow_calls** mit **Mittelwert** aus.

   [ ![resilience4J 5](media/spring-cloud-resilience4j/resilience4j-5.png)](media/spring-cloud-resilience4j/resilience4j-5.PNG)

## <a name="see-also"></a>Weitere Informationen

* [Application Insights](./how-to-application-insights.md)
* [Verteilte Ablaufverfolgung](./how-to-distributed-tracing.md)
* [Dashboard für Trennschalter](./tutorial-circuit-breaker.md)
