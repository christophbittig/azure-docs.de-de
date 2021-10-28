---
title: Automatisieren der Dienstregistrierung und -ermittlung
description: Erfahren Sie, wie Sie die Dienstermittlung und -registrierung mithilfe der Spring Cloud-Dienstregistrierung automatisieren.
author: karlerickson
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: karler
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: e73fdd7c07104d791fc5259fbcd6a7edf3c9b0c5
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130241191"
---
# <a name="discover-and-register-your-spring-cloud-services"></a>Ermitteln und Registrieren Ihrer Spring Cloud-Dienste

Die Dienstermittlung ist eine wichtige Voraussetzung für eine auf Microservices basierende Architektur.  Das manuelle Konfigurieren der einzelnen Clients ist zeitaufwendig und kann menschliche Fehler verursachen.  Dieses Problem wird durch die Azure Spring Cloud-Dienstregistrierung gelöst.  Nach der Konfiguration steuert ein Dienstregistrierungsserver die Dienstregistrierung und -ermittlung für die Microservices Ihrer Anwendung. Der Dienstregistrierungsserver verwaltet eine Registrierung der bereitgestellten Microservices, aktiviert den clientseitigen Lastenausgleich und entkoppelt die Dienstanbieter von den Clients, ohne dafür DNS zu benötigen.

::: zone pivot="programming-language-csharp"
Informationen zum Einrichten der Dienstregistrierung für eine Steeltoe-App finden Sie unter [Vorbereiten einer Java Spring-Anwendung für die Bereitstellung in Azure Spring Cloud](how-to-prepare-app-deployment.md).
::: zone-end
::: zone pivot="programming-language-java"

## <a name="register-your-application-using-spring-cloud-service-registry"></a>Registrieren Ihrer Anwendung mithilfe der Spring Cloud-Dienstregistrierung

Bevor Ihre Anwendung die Dienstregistrierung und -ermittlung mithilfe der Spring Cloud-Dienstregistrierung verwalten kann, müssen mehrere Abhängigkeiten in der Datei *pom.xml* der Anwendung hinzugefügt werden.
Fügen Sie in der Datei *pom.xml* Abhängigkeiten für *spring-cloud-starter-netflix-eureka-client* und *spring-cloud-starter-azure-spring-cloud-client* hinzu.

```xml
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </dependency>
```

## <a name="update-the-top-level-class"></a>Aktualisieren der Klasse der obersten Ebene

Abschließend fügen Sie der Klasse auf oberster Ebene Ihrer Anwendung eine Anmerkung hinzu.

 ```java
    package foo.bar;

    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    import org.springframework.cloud.netflix.eureka.EnableEurekaClient;

    @SpringBootApplication
    @EnableEurekaClient
    public class DemoApplication {

        public static void main(String[] args) {
            SpringApplication.run(DemoApplication.class, args);
        }
    }
 ```

Der Serverendpunkt der Spring Cloud-Dienstregistrierung wird als Umgebungsvariable in Ihre Anwendung eingefügt.  Microservices können sich damit selbst beim Dienstregistrierungsserver registrieren und andere abhängige Microservices ermitteln.

Beachten Sie, dass es einige Minuten dauern kann, bis die Änderungen vom Server an alle Microservices weitergegeben wurden.
::: zone-end
