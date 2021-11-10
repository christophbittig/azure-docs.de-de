---
title: Übersicht über DevOps Starter für Azure | Microsoft-Dokumentation
description: Grundlegendes zum Nutzen von DevOps Starter
services: devops-project
documentationcenter: ''
author: georgewallace
manager: gwallace
editor: mlearned
ms.assetid: ''
ms.service: az-devops-project
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 03/24/2020
ms.author: gwallace
ms.openlocfilehash: 04fe33686d640ab84c0bdb532486a15b5582a612
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132057136"
---
# <a name="overview-of-devops-starter"></a>Übersicht über DevOps Starter

 DevOps Starter erleichtert die ersten Schritte mit Azure entweder mit GitHub Actions oder mit Azure DevOps. Mit Azure DevOps Projects können Sie Ihre bevorzugte App für den Azure-Dienst Ihrer Wahl in wenigen kurzen Schritten über das Azure-Portal starten. 

 Azure DevOps Starter richtet alles ein, was Sie zum Entwickeln, Bereitstellen und Überwachen Ihrer Anwendung benötigen. Über das Dashboard von Azure DevOps Starter können Sie Codecommits, Buildvorgänge und Bereitstellungen in einer zentralen Ansicht im Azure-Portal überwachen.

## <a name="advantages-of-using-devops-starter"></a>Vorteile der Verwendung von DevOps Starter

  Von DevOps Starter werden die beiden folgenden CI/CD-Anbieter für die Automatisierung Ihrer Bereitstellungen unterstützt:
  * [GitHub-Aktionen](https://github.com/features/actions)
  * [Azure DevOps](https://azure.microsoft.com/services/devops)

  DevOps Starter automatisiert die Einrichtung aller CI- und CD-Vorgänge (Continuous Integration und Continuous Delivery) für Ihre Anwendung in Azure.  Sie können bereits vorhandenen Code nutzen oder eine der bereitgestellten Beispielanwendungen verwenden. Anschließend können Sie die Anwendung schnell für verschiedene Azure-Dienste wie Virtual Machines, App Service, Azure Kubernetes Service (AKS), Azure SQL-Datenbank oder Azure Service Fabric bereitstellen.  

  DevOps Starter nimmt Ihnen die gesamte Erstkonfiguration einer DevOps-Pipeline ab – von der Einrichtung des anfänglichen Git-Repositorys und der Konfiguration der CI/CD-Pipeline über die Erstellung einer Application Insights-Ressource für die Überwachung bis hin zur Bereitstellung einer zentralen Ansicht der gesamten Lösung durch die Erstellung eines DevOps Starter-Dashboards im Azure-Portal.

DevOps Starter ermöglicht Folgendes:

* Schnelles Bereitstellen Ihrer Anwendung in Azure
* Automatisieren der Einrichtung eines CI/CD-Workflows oder einer -Pipeline
* Anzeigen und Nachvollziehen der richtigen Einrichtung eines CI/CD-Workflows oder einer -Pipeline
* Weiteres Anpassen der Releasepipelines auf der Grundlage Ihres spezifischen Szenarios

## <a name="how-to-use-devops-starter"></a>Wie wird DevOps Starter verwendet?

  DevOps Starter steht über das Azure-Portal zur Verfügung. Eine DevOps Starter-Ressource wird im Portal auf die gleiche Weise erstellt wie jede andere Azure-Ressource. DevOps Projects bietet einen Assistenten zum Durchlaufen der verschiedenen Konfigurationsoptionen.  

Bei der Ersteinrichtung müssen mehrere Konfigurationsoptionen ausgewählt werden. Die Optionen umfassen:

* Auswählen Ihres bevorzugten CI/CD-Anbieters
* Verwenden der bereitgestellten Beispiel-App oder Ihres eigenen Codes (nur für Azure DevOps)
* Auswählen einer App-Sprache
* Auswählen eines App-Frameworks auf der Grundlage der Sprache
* Auswählen eines Azure-Diensts (Bereitstellungsziel)
* Auswählen Ihrer GitHub- bzw. Azure DevOps-Organisation
* Auswählen Ihres Azure-Abonnements
* Auswählen des Standorts von Azure-Diensten
* Auswählen eines Tarifs für Azure-Dienste

Nach der Erstellung von DevOps Starter haben Sie folgende Möglichkeiten:

* Anpassen Ihres GitHub-Workflows bzw. Ihrer Azure DevOps-Pipeline
* Verwenden von Pullanforderungen, um den Codefluss zu verwalten und eine hohe Qualität zu gewährleisten
* Testen und Erstellen jedes Commits vor dem Zusammenführen Ihres Codes zur Erhöhung der Qualität

Nach Verwendung von DevOps Starter können die Ressourcen auch alle von einem zentralen Ort aus über das DevOps Starter-Dashboard im Azure-Portal gelöscht werden.

## <a name="devops-starter-and-github-integration"></a>DevOps Starter- und GitHub-Integration

Von DevOps Starter wird GitHub Actions jetzt als CI/CD-Anbieter unterstützt. Hiermit werden alle Aufgaben automatisiert, die in GitHub zum Einrichten eines CI/CD-Workflows mit GitHub Actions benötigt werden. Es wird ein GitHub-Repository in einer vorhandenen GitHub-Organisation erstellt und anschließend für eine Beispielanwendung ein Commitvorgang für das neue GitHub-Repository durchgeführt.  

Bei der Automatisierung wird auch ein Trigger für den Workflow eingerichtet, damit für jeden neuen Codecommit im Workflow ein Build- und Bereitstellungsauftrag initiiert wird. Die Anwendung wird im Azure-Dienst Ihrer Wahl bereitgestellt. Der GitHub-Workflow kann für weitere Szenarien angepasst werden. 

## <a name="devops-starter-and-azure-devops-integration"></a>DevOps Starter- und Azure DevOps-Integration

DevOps Starter mit Azure DevOps automatisiert sämtliche Aufgaben, die in Azure Pipelines zum Einrichten einer CI/CD-Pipeline ausgeführt werden müssen. Es erstellt ein Git-Repository in einer neuen oder vorhandenen Azure DevOps-Organisation und committet dann eine Beispielanwendung oder Ihren vorhandenen Code in einem neuen Git-Repository.  

Die Automatisierung richtet auch einen CI-Trigger für den Build ein, sodass nach jedem neuen Codecommit ein Buildvorgang initiiert wird. DevOps Starter erstellt einen CD-Trigger und stellt jeden neuen erfolgreichen Build für den Azure-Dienst Ihrer Wahl bereit.  

Build- und Releasepipelines können für zusätzliche Szenarien angepasst werden. Darüber hinaus können Sie die Build- und die Releasepipelines für die Verwendung in anderen Projekten klonen.

## <a name="getting-started-with-devops-starter"></a>Erste Schritten mit DevOps Starter

* [Erste Schritten mit DevOps Starter](./azure-devops-project-github.md)

##  <a name="devops-starter-videos"></a>DevOps Starter-Videos

* [Erstellen Ihrer CI/CD-Pipeline mit Azure DevOps Starter](https://www.youtube.com/watch?v=NuYDAs3kNV8)
