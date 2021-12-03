---
title: Java und Basisbetriebssystem für Azure Spring Cloud-Microservice-Apps
description: Grundsätze zum Beibehalten der Integrität von Java und Basisbetriebssystem für Azure Spring Cloud-Microservice-Apps
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/12/2021
ms.custom: devx-track-java
ms.openlocfilehash: 1f8ef8ded5dc86af6ab8dc8b3344fae86dc8cdd7
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130258029"
---
# <a name="java-and-base-os-for-spring-microservice-apps"></a>Java und Basisbetriebssystem für Spring-Microservice-Apps

**Dieser Artikel gilt für:** ✔️ Java

Im folgenden sind Grundsätze zum Beibehalten der Integrität von Java und Basisbetriebssystem für Spring-Microservice-Apps aufgeführt.

## <a name="principles-for-healthy-java-and-base-os"></a>Grundsätze für die Integrität von Java und Basisbetriebssystem

* Gleiches Basisbetriebssystem für alle Ebenen – Basic | Standard | Premium.

    * Derzeit verwenden Apps in Azure Spring Cloud eine Mischung aus Debian 10 und Ubuntu 18.04.
    * VMware Build Service verwendet Ubuntu 18.04.

* Gleiches Basisbetriebssystem unabhängig von den Startpunkten der Bereitstellung – Quelle | JAR

    * Derzeit verwenden Apps in Azure Spring Cloud eine Mischung aus Debian 10 und Ubuntu 18.04.

* Basisbetriebssystem weist keine Sicherheitsrisiken auf.

    * Das Basisbetriebssystem Debian 10 weist 147 geöffnete CVEs auf.
    * Das Basisbetriebssystem Ubuntu 18.04 weist 132 geöffnete CVEs auf.

* Verwendung von JRE-headless.

    * Derzeit verwenden Apps in Azure Spring Cloud JDK. JRE-headless ist ein kleineres Image.

* Verwendung der neuesten Java-Builds.

    * Derzeit verwenden Apps in Azure Spring Cloud Java 8 Build 242. Dabei handelt es sich um einen veralteten Build.

Azul Systems sucht kontinuierlich nach Änderungen an Basisbetriebssystemen und bleibt auf dem Stand der zuletzt erstellten Images. Azure Spring Cloud sucht nach Änderungen an Images und hält Bereitstellungen kontinuierlich auf neuestem Stand.

## <a name="faq-for-azure-spring-cloud"></a>Häufig gestellte Fragen zu Azure Spring Cloud

* Welche Versionen von Java werden unterstützt? Hauptversion und Buildnummer.

    * Unterstützung von LTS-Versionen (Java 8 und 11).
    * Verwendung des neuesten Builds, z. B. jetzt Java 8 Build 252 und Java 11 Build 7.

* Wer hat diese Java-Runtimeversionen erstellt?

    * Azul Systems.

* Was ist das Basisbetriebssystem für Images?

    * Ubuntu 20.04 LTS (Focal Fossa). Apps bleiben weiterhin auf der neuesten LTS-Version von Ubuntu.
    * Weitere Informationen finden Sie unter [Ubuntu 20.04 LTS (Focal Fossa)](http://releases.ubuntu.com/focal/).

* Wie kann ich eine unterstützte Java-Runtime für die lokale Entwicklung herunterladen?

    * Weitere Informationen finden Sie unter [Installieren des JDK für Azure und Azure Stack](/azure/developer/java/fundamentals/java-jdk-install).

* Wie erhalte ich Support für Probleme auf der Ebene der Java-Runtime?

    * Öffnen Sie ein Supportticket beim Azure-Support.

## <a name="default-deployment-on-azure-spring-cloud"></a>Standardbereitstellung in Azure Spring Cloud

> ![Standardbereitstellung](media/spring-cloud-principles/spring-cloud-default-deployment.png)

## <a name="next-steps"></a>Nächste Schritte

* [Schnellstart: Bereitstellen Ihrer ersten Spring Boot-App in Azure Spring Cloud](./quickstart.md)
* [Langfristiger Java-Support für Azure und Azure Stack](/azure/developer/java/fundamentals/java-support-on-azure)
