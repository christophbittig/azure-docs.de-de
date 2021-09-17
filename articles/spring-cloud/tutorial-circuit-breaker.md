---
title: 'Tutorial: Verwenden des Trennschalterdashboards mit Azure Spring Cloud'
description: Erfahren Sie, wie Sie das Trennschalterdashboard mit Azure Spring Cloud verwenden.
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 04/06/2020
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 6fce05bf1f853edd527df920f579689e23f080c8
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122015662"
---
# <a name="tutorial-use-circuit-breaker-dashboard-with-azure-spring-cloud"></a>Tutorial: Verwenden des Trennschalterdashboards mit Azure Spring Cloud

**Dieser Artikel gilt für:** ✔️ Java

[Spring Cloud Netflix Turbine](https://github.com/Netflix/Turbine) wird häufig verwendet, um mehrere [Hystrix](https://github.com/Netflix/Hystrix)-Metrikdatenströme zu aggregieren, sodass die Datenströme mit dem Hystrix-Dashboard in einer einzelnen Ansicht überwacht werden können. In diesem Tutorial wird veranschaulicht, wie sie in Azure Spring Cloud verwendet werden.

> [!NOTE]
> Netflix Hystrix wird in vielen bestehenden Spring Cloud-Apps verwendet, befindet sich aber nicht mehr in aktiver Entwicklung. Wenn Sie ein neues Projekt entwickeln, verwenden Sie stattdessen Spring Cloud-Trennschalterimplementierungen wie [resilience4j](https://github.com/resilience4j/resilience4j). Anders als die in diesem Tutorial gezeigte Turbine vereinigt das neue Spring Cloud-Trennschalterframework alle Implementierungen seiner Metrikdatenpipeline in Micrometer. Dies wird auch von Azure Spring Cloud unterstützt. [Weitere Informationen](./how-to-circuit-breaker-metrics.md).

## <a name="prepare-your-sample-applications"></a>Vorbereiten der Beispielanwendungen

Das Beispiel wird aus diesem [Repository](https://github.com/StackAbuse/spring-cloud/tree/master/spring-turbine) geforkt.

Klonen Sie das Beispielrepository in Ihre Entwicklungsumgebung:

```bash
git clone https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples.git
cd Azure-Spring-Cloud-Samples/hystrix-turbine-sample
```

Erstellen Sie die drei Anwendungen, die in diesem Tutorial verwendet werden sollen:

* user-service: Ein einfacher REST-Dienst mit einem einzelnen Endpunkt von „/personalized/{id}“
* recommendation-service: Ein einfacher REST-Dienst mit einem einzelnen Endpunkt von „/recommendations“, der von „user-service“ aufgerufen wird.
* hystrix-turbine: Ein Hystrix-Dashboarddienst zur Anzeige von Hystrix-Datenströmen und ein Turbine-Dienst, der Hystrix-Metrikdatenströme aus anderen Diensten zusammenfasst.

```bash
mvn clean package -D skipTests -f user-service/pom.xml
mvn clean package -D skipTests -f recommendation-service/pom.xml
mvn clean package -D skipTests -f hystrix-turbine/pom.xml
```

## <a name="provision-your-azure-spring-cloud-instance"></a>Bereitstellen Ihrer Azure Spring Cloud-Instanz

Befolgen Sie das Verfahren: [Bereitstellen einer Dienstinstanz über die Azure CLI](./quickstart.md#provision-an-instance-of-azure-spring-cloud).

## <a name="deploy-your-applications-to-azure-spring-cloud"></a>Bereitstellen von Anwendungen in Azure Spring Cloud

Diese Apps verwenden nicht den **Konfigurationsserver**, daher ist es nicht notwendig, den **Konfigurationsserver** für Azure Spring Cloud einzurichten.  Führen Sie die Erstellung und Bereitstellung wie folgt durch:

```azurecli
az spring-cloud app create -n user-service --assign-endpoint
az spring-cloud app create -n recommendation-service
az spring-cloud app create -n hystrix-turbine --assign-endpoint

az spring-cloud app deploy -n user-service --jar-path user-service/target/user-service.jar
az spring-cloud app deploy -n recommendation-service --jar-path recommendation-service/target/recommendation-service.jar
az spring-cloud app deploy -n hystrix-turbine --jar-path hystrix-turbine/target/hystrix-turbine.jar
```

## <a name="verify-your-apps"></a>Überprüfen Sie Ihre Apps.

Nachdem alle Apps ausgeführt werden und auffindbar sind, greifen Sie auf `user-service` mit dem Pfad `https://<username>-user-service.azuremicroservices.io/personalized/1` von Ihrem Browser aus zu. Wenn der Benutzerdienst auf `recommendation-service` zugreifen kann, sollten Sie die folgende Ausgabe erhalten. Aktualisieren Sie die Webseite mehrmals, wenn sie nicht funktioniert.

```json
[{"name":"Product1","description":"Description1","detailsLink":"link1"},{"name":"Product2","description":"Description2","detailsLink":"link3"},{"name":"Product3","description":"Description3","detailsLink":"link3"}]
```

## <a name="access-your-hystrix-dashboard-and-metrics-stream"></a>Zugreifen auf Ihr Hystrix-Dashboard und den Metrikdatenstrom

Führen Sie die Überprüfung mit öffentlichen Endpunkten oder privaten Testendpunkten durch.

### <a name="using-public-endpoints"></a>Verwenden öffentlicher Endpunkte

Greifen Sie mit Ihrem Browser über den Pfad `https://<SERVICE-NAME>-hystrix-turbine.azuremicroservices.io/hystrix` auf „hystrix-turbine“ zu.  In der folgenden Abbildung ist das Hystrix-Dashboard in dieser App dargestellt.

![Hystrix-Dashboard](media/spring-cloud-circuit-breaker/hystrix-dashboard.png)

Kopieren Sie die URL des Turbine-Datenstroms `https://<SERVICE-NAME>-hystrix-turbine.azuremicroservices.io/turbine.stream?cluster=default` in das Textfeld, und wählen Sie **Datenstrom überwachen** aus.  Dadurch wird das Dashboard angezeigt. Wenn im Viewer nichts angezeigt wird, erfassen Sie die `user-service`-Endpunkte, um Datenströme zu generieren.

![Hystrix-Datenstrom](media/spring-cloud-circuit-breaker/hystrix-stream.png) Jetzt können Sie mit dem Trennschalterdashboard experimentieren.

> [!NOTE]
> In der Produktionsumgebung sollten das Hystrix-Dashboard und der Metrikdatenstrom nicht über das Internet zugänglich sein.

### <a name="using-private-test-endpoints"></a>Verwenden privater Testendpunkte

Hystrix-Metrikdatenströme sind auch von `test-endpoint` aus zugänglich. Als Back-End-Dienst haben wir keinen öffentlichen Endpunkt für `recommendation-service` zugewiesen, aber wir können seine Metriken mit einem Testendpunkt bei `https://primary:<KEY>@<SERVICE-NAME>.test.azuremicroservices.io/recommendation-service/default/actuator/hystrix.stream` anzeigen.

![Datenstrom des Hystrix-Testendpunkts](media/spring-cloud-circuit-breaker/hystrix-test-endpoint-stream.png)

Als Web-App sollte das Hystrix-Dashboard mit `test-endpoint` arbeiten. Wenn es nicht ordnungsgemäß funktioniert, kann dies zwei Gründe haben: Die Verwendung von `test-endpoint` hat die Basis-URL von `/` in `/<APP-NAME>/<DEPLOYMENT-NAME>` geändert oder die Web-App verwendet absolute Pfade für statische Ressourcen. Damit es mit `test-endpoint` funktioniert, müssen Sie möglicherweise die `<base>` in den Front-End-Dateien manuell bearbeiten.

## <a name="next-steps"></a>Nächste Schritte

* [Bereitstellen einer Dienstinstanz über die Azure CLI](./quickstart.md#provision-an-instance-of-azure-spring-cloud)
* [Vorbereiten einer Java Spring-Anwendung für die Bereitstellung in Azure Spring Cloud](how-to-prepare-app-deployment.md)
