---
title: GitOps- und Azure Dev/Test-Angebot
description: Verwenden von GitOps in Verbindung mit Azure Dev/Test
author: jamestramel
ms.author: jametra
ms.prod: visual-studio-windows
ms.topic: how-to
ms.date: 10/20/2021
ms.custom: devtestoffer
ms.openlocfilehash: df3fd830c04f90315126bd68a07a22a95cbfbc53
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095567"
---
# <a name="using-gitops-with-azure-devtest-offer-to-optimize-and-secure-applications"></a>Verwenden von GitOps mit einem Azure Dev/Test-Angebot zum Optimieren und Sichern von Anwendungen

GitOps ist ein Betriebsframework. GitOps verwendet bewährte DevOps-Methoden für das Entwickeln von Anwendungen und wendet sie auf die Infrastrukturautomatisierung an.  

Wenn Teams GitOps anwenden, verwenden Sie Konfigurationsdateien, die als Code gespeichert sind (Infrastructure-as-Code). Diese Dateien generieren bei jedem Bereitstellen dieselbe Umgebung. Stellen Sie sich dies wie einen Anwendungsquellcode vor, der bei jedem Erstellen die gleichen Anwendungsbinärdateien generiert.  

## <a name="gitops-methodology"></a>GitOps-Methodik  

Bei diesem Prozess oder dieser Methodik werden Git-Repositorys verwendet. Diese Repositorys sind Ihre konsolidierten Daten für einen Zustand und eine Konfiguration, die Sie für Ihre Anwendung definieren. Sie enthalten deklarative Beschreibungen der Infrastruktur, die Sie in der Produktion benötigen. Ein automatisierter Prozess bewirkt, dass diese Umgebung mit dem im Repository beschriebenen Zustand übereinstimmt.  

Um eine neue Anwendung bereitstellen oder eine bereits vorhandene zu aktualisieren, müssen Sie nur das Repository aktualisieren. Der automatisierte Prozess übernimmt alles andere.  

## <a name="benefits-of-gitops"></a>Vorteile von GitOps  

- Ermöglicht die Zusammenarbeit bei Infrastrukturänderungen  
- Verbesserte Zugriffssteuerung  
- Schnellere Markteinführung  
- Geringeres Risiko  
- Geringere Kosten  
- Weniger fehleranfällig  

## <a name="use-gitops-with-devtest"></a>Verwenden von GitOps mit Dev/Test  

GitOps als Prozess und Framework sollte auf Ihre Nichtproduktionsinstanzen angewendet werden. GitOps kann in Ihren DevTest-Umgebungen überprüft oder verwendet werden. Sie können GitOps-Prinzipien verwenden, um Ihre DevOps-Prozesse zu verbessern. Nutzen Sie Ihre DevTest-Vorteile und -Umgebungen mit GitOps-Prinzipien, um Ihre Aktivitäten zu optimieren und die Sicherheit und Zuverlässigkeit Ihrer Anwendungen aufrechtzuerhalten.  

GitOps kombiniert Automatisierung und gängige Zusammenarbeitsframeworks wie Git. Sie können kombiniert werden, um eine schnelle Bereitstellung der Cloudinfrastruktur bei gleichzeitiger Einhaltung von Unternehmenssicherheitsstandards zu ermöglichen.  

Weitere Informationen zu GitOps und Azure:  

- [Azure Friday-Video: Kubernetes mit Azure Arc-Unterstützung mit GitOps](https://azure.microsoft.com/resources/videos/azure-friday-azure-arc-enabled-kubernetes-with-gitops/)  
- [Azure Friday-Blog: Kubernetes mit Azure Arc-Unterstützung mit GitOps](https://techcommunity.microsoft.com/t5/azure-arc/azure-arc-enabled-kubernetes-with-gitops/ba-p/1654171?ocid=AID754288&wt.mc_id=azfr-c9-scottha&wt.mc_id=CFID0570)  
- [GitOps für die Lebenszyklusautomatisierung der Azure-Infrastruktur](https://github.com/travisnielsen/azure-gitops)
