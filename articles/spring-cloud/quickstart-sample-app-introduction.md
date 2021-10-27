---
title: 'Schnellstart: Einführung in die Beispiel-App – Azure Spring Cloud'
description: Hier wird die Beispiel-App beschrieben, die in dieser Reihe von Schnellstartanleitungen für die Bereitstellung in Azure Spring Cloud verwendet wird.
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/12/2021
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 2966f77bb070269e11a01303204362d3b6950207
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "130005860"
---
# <a name="introduction-to-the-sample-app"></a>Einführung in die Beispiel-App

::: zone pivot="programming-language-csharp"
In dieser Reihe von Schnellstartanleitungen wird mithilfe einer Beispiel-App, die aus zwei Microservices besteht, veranschaulicht, wie Sie eine .NET Core-Steeltoe-App im Azure Spring Cloud-Dienst bereitstellen. Sie verwenden Azure Spring Cloud-Funktionen wie Dienstermittlung, Konfigurationsserver, Protokolle, Metriken und verteilte Ablaufverfolgung.

## <a name="functional-services"></a>Funktionsdienste

Die Beispiel-App besteht aus zwei Microservices:

* Der Dienst `planet-weather-provider` gibt Wettertext als Antwort auf eine HTTP-Anforderung zurück, die den Namen des Planeten angibt. Beispielsweise kann für den Planeten Merkur „sehr warm“ zurückgegeben werden. Die Wetterdaten werden vom Konfigurationsserver abgerufen. Der Konfigurationsserver ruft die Wetterdaten aus einer YAML-Datei in einem Git-Repository ab, z. B.:

   ```yaml
   MercuryWeather: very warm
   VenusWeather: quite unpleasant
   MarsWeather: very cool
   SaturnWeather: a little bit sandy
   ```

* Der Dienst `solar-system-weather` gibt Daten für vier Planeten als Antwort auf eine HTTP-Anforderung zurück. Er ruft die Daten ab, indem vier HTTP-Anforderungen an `planet-weather-provider` gesendet werden. Der Dienst nutzt den Eureka Server-Ermittlungsdienst, um `planet-weather-provider` aufzurufen. JSON-Code wird zurückgegeben, z. B.:

   ```json
   [{
       "Key": "Mercury",
       "Value": "very warm"
   }, {
       "Key": "Venus",
       "Value": "quite unpleasant"
   }, {
       "Key": "Mars",
       "Value": "very cool"
   }, {
       "Key": "Saturn",
       "Value": "a little bit sandy"
   }]
   ```

Die Architektur der Beispiel-App wird anhand des folgenden Diagramms veranschaulicht:

:::image type="content" source="media/spring-cloud-quickstart-sample-app-introduction/sample-app-diagram.png" alt-text="Beispiel-App-Diagramm":::

## <a name="code-repository"></a>Coderepository

Die Beispiel-App befindet sich im Ordner [steeltoe-sample](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/steeltoe-sample) des Repositorys „Azure-Samples/Azure-Spring-Cloud-Samples“ auf GitHub.

In den Anweisungen der folgenden Schnellstartanleitungen wird bei Bedarf auf den Quellcode verwiesen.

::: zone-end

::: zone pivot="programming-language-java"
In dieser Schnellstartanleitung verwenden Sie die Microservicesversion der bekannten Beispiel-App [PetClinic](https://github.com/spring-petclinic/spring-petclinic-microservices). Diese zeigt, wie Sie Apps für den Azure Spring Cloud-Dienst bereitstellen. Das Beispiel **PetClinic** veranschaulicht das Microservicearchitekturmuster sowie die Aufschlüsselung der Dienste. Sie erfahren, wie Dienste in Azure mit Azure Spring Cloud-Funktionen wie Dienstermittlung, Konfigurationsserver, Protokolle, Metriken, verteilte Ablaufverfolgung und entwicklerfreundliche Toolunterstützung bereitgestellt wird.

Für die Verwendung der Azure Spring Cloud-Bereitstellungsbeispiele benötigen Sie nur den Speicherort des Quellcodes. Dieser ist jeweils nach Bedarf angegeben.

![Architektur von PetClinic](media/build-and-deploy/microservices-architecture-diagram.jpg)

## <a name="functional-services-to-be-deployed"></a>Bereitzustellende Funktionsdienste

PetClinic ist in vier wichtige Microservices unterteilt. Alle sind eigenständig bereitstellbare Anwendungen, die nach Geschäftsbereichen organisiert sind.

* **customers-service:** Enthält allgemeine Benutzereingabelogik und -validierung, einschließlich Informationen zu Haustieren und Besitzern (Name, Adresse, Ort, Telefon).
* **visits-service:** Speichert Informationen zu den Besuchen für die Anmerkungen zu den einzelnen Haustieren und zeigt sie an.
* **vets-service:** Speichert Informationen zu Tierärzten und zeigt sie an, u. a. Name und Besonderheiten.
* **API-Gateway:** Das API-Gateway ist ein einzelner Einstiegspunkt in das System und wird zum Verarbeiten von Anforderungen und zum Weiterleiten dieser Anforderungen an den entsprechenden Dienst oder zum Aufrufen mehrerer Dienste verwendet. Die Ergebnisse werden dabei aggregiert.  Die drei Kerndienste machen eine externe API für den Client verfügbar. In realen Systemen kann die Anzahl von Funktionen mit der Systemkomplexität sehr schnell wachsen. Am Rendern einer einzigen komplexen Webseite können Hunderte von Diensten beteiligt sein.

## <a name="infrastructure-services-hosted-by-azure-spring-cloud"></a>Von Azure Spring Cloud gehostete Infrastrukturdienste

Es gibt mehrere gängige Muster in verteilten Systemen, die Kerndienste unterstützen. Azure Spring Cloud bietet Tools, die Spring Boot-Anwendungen zur Implementierung dieser Muster verbessern:

* **Konfigurationsdienst:** Die Azure Spring Cloud-Konfiguration ist ein horizontal skalierbarer zentralisierter Konfigurationsdienst für verteilte Systeme. Er nutzt ein austauschbares Repository, das derzeit lokalen Speicher, Git und Subversion unterstützt.
* **Dienstermittlung:** Sie ermöglicht die automatische Erkennung von Netzwerkadressen für Dienstinstanzen, die aufgrund von automatischer Skalierung, Fehlern und Upgrades dynamisch zugewiesene Adressen aufweisen können.

## <a name="database-configuration"></a>Datenbankkonfiguration

In der Standardkonfiguration verwendet **PetClinic** eine In-Memory Database-Instanz (HSQLDB), die beim Start mit Daten aufgefüllt wird. Ein ähnliches Setup wird für MySql bereitgestellt, wenn eine persistente Datenbankkonfiguration erforderlich ist. Eine Abhängigkeit für Connector/J, den MySQL JDBC-Treiber, ist bereits in den Dateien vom Typ „pom.xml“ enthalten.

## <a name="sample-usage-of-petclinic"></a>Beispielnutzung von PetClinic

Vollständige Implementierungsdetails finden Sie im Fork von [PetClinic](https://github.com/Azure-Samples/spring-petclinic-microservices). In den Beispielen wird nach Bedarf auf den Quellcode verwiesen.

::: zone-end

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Bereitstellen einer Azure Spring Cloud-Instanz](./quickstart-provision-service-instance.md)
