---
title: Integration von Azure DevTest Labs und DevOps
description: Erfahren Sie hier, wie Sie Labs in CI/CD-Pipelines (Continous Integration - kontinuierliche Integration / Continuous Delivery - kontinuierliche Lieferung) in einer Unternehmensumgebung verwenden.
ms.topic: conceptual
ms.date: 06/26/2020
ms.openlocfilehash: 59c259a27fa7e171a6e6aa6bd7bc2ffa25fa89fd
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132286334"
---
# <a name="integrate-azure-devtest-labs-and-azure-devops"></a>Integration von Azure DevTest Labs und Azure DevOps
DevOps ist eine Softwareentwicklungsmethodik, bei der Softwareentwicklung (Dev) und Betrieb (Ops) für ein System zusammengefasst werden. Dieses System kann neue Features, Updates und Fehlerbehebungen in Einklang mit den Geschäftszielen bereitstellen. Bei dieser Methodik wird alles vom Entwerfen neuer Features anhand von Zielen, Nutzungsmustern und Kundenfeedback bis zum Beheben von Fehlern, dem Wiederherstellen und dem Härten des Systems zusammengefasst. Eine leicht identifizierbare Komponente dieser Methodik ist die CI/CD-Pipeline (Continuous Integration/Continuous Delivery). Eine CI/CD-Pipeline holt Informationen, einen Code und Ressourcen aus einem Commit über eine Reihe von Schritten, um das System zu erzeugen. Die Schritte umfassen Erstellen, Testen und Bereitstellung. In diesem Artikel liegt der Schwerpunkt auf der effektiven Nutzung von Labs in einer Pipeline in einer Unternehmensumgebung. 

## <a name="benefits-of-using-labs-in-devops-workflow"></a>Vorteile der Verwendung von Labs im DevOps-Workflow 

### <a name="focused-access"></a>Präziser Zugriff 
Die Verwendung eines Labs als Komponente ermöglicht die Zuordnung eines bestimmten Ökosystems zu einer eingeschränkten Gruppe von Personen. In der Regel wird einem Team oder einer Gruppe, die an einen gemeinsamen Bereich oder einem bestimmten Feature arbeiten, ein Lab zugewiesen.   

### <a name="infrastructure-replication-in-the-cloud"></a>Infrastrukturreplikation in der Cloud 
Ein Entwickler kann schnell ein Ökosystem für die Entwicklung einrichten, das eine Entwicklungsumgebung mit Quellcode und Tools enthält. Außerdem kann der Entwickler auch eine Umgebung erstellen, die nahezu identisch mit der Produktionskonfiguration ist. Dies trägt zu einer schnelleren Entwicklung des inneren Kreislaufs (Inner Loop) bei. 

### <a name="pre-production"></a>Vor der Produktion 
Ein Lab in der CI/CD-Pipeline vereinfacht die gleichzeitige Ausführung mehrerer Vorproduktionsumgebungen oder -computer für asynchrone Tests. Auch können in einem Lab unterschiedliche unterstützende Infrastrukturen wie Build-Agents bereitgestellt und verwaltet werden. 

## <a name="devops-with-devtest-labs"></a>DevOps mit DevTest Labs 

### <a name="development--operation"></a>Entwicklung/Betrieb 
Ein Lab sollte sich immer nur auf ein Team konzentrieren, das an einem Funktionsbereich arbeitet. Dieser allgemeine Fokus ermöglicht die gemeinsame Nutzung von bereichsspezifischen Ressourcen wie Tools, Skripts oder Resource Manager-Vorlagen. Außerdem sind so schnellere Änderungen möglich, während gleichzeitig die Nachteile einer kleineren Gruppe minimiert werden. Diese gemeinsamen Ressourcen ermöglichen dem Entwickler die Erstellung von virtuellen Computern mit dem gesamten erforderlichen Codes, Tools und einer vollständigen Konfiguration. Ressourcen können entweder dynamisch oder in einem System erstellt werden, das Basisbilder mit Anpassungen erstellt. Der Entwickler kann aber nicht nur virtuelle Computer erstellen, sondern auch DevTest-Labs-Umgebungen, die auf den erforderlichen Vorlagen zum Erstellen der entsprechenden Azure-Ressourcen im Lab basieren. Wenn etwas geändert oder verworfen wird, hat dies nur Auswirkungen auf die Lab-Umgebung, aber nicht auf andere Personen. Betrachten Sie das Szenario, in dem das Produkt ein eigenständiges System ist, das auf dem Kundencomputer installiert ist. In diesem Fall wurde durch DevTest-Labs die Erstellung eines virtuellen Computers vereinfacht, die auch die Installation von Software mit Artefakten und vorab erstellten Kundenkonfigurationen für schnellere Tests im inneren Kreislauf (Inner-Loop-Tests) des Codes umfasst. 
  
## <a name="cicd-pipeline"></a>CI/CD-Pipeline 
Die CI/CD-Pipeline ist eine wichtige Komponenten in DevOps. Die Pipeline verschiebt den Code aus dem Pull Request eines Entwicklers, integriert ihn in den vorhandenen Code und stellt ihn im Produktionsökosystem zur Verfügung. Dabei müssen nicht alle Ressourcen in einem Lab enthalten sein. Beispielsweise könnte ein Jenkins-Host außerhalb des Labs als eine persistentere Ressource eingerichtet werden. Im Folgenden finden Sie einige Beispiele für die Integration von Labs in die Pipeline. 

### <a name="build"></a>Entwickeln 
Schwerpunkt der Build-Pipeline ist das Erstellen eines Pakets von Komponenten, die gemeinsam getestet werden sollen, damit sie an die Release-Pipeline übergeben werden können. Labs können wie die Speicherorte der Build-Agents und andere unterstützende Ressourcen Teil der Build-Pipeline sein. Durch die Möglichkeit der dynamischen Erstellung der Infrastruktur haben Sie mehr Kontrolle. Mit der Möglichkeit, mehrere Umgebungen in einem Lab zu verwenden, kann jeder Build asynchron ausgeführt werden. Die Build-ID ist Teil der Umgebungsinformationen, die die Ressourcen für den jeweiligen Build eindeutig identifizieren.   

Bei Build-Agents erhöht die Möglichkeit von Labs, den Zugriff einzuschränken, die Sicherheit und reduziert die Wahrscheinlichkeit versehentlicher Beschädigungen.  

### <a name="test"></a>Test 
DevTest Labs ermöglicht die automatisierte Erstellung einer Azure-Ressource (wie virtuelle Computer und Umgebungen) durch eine CI/CD-Pipeline. Diese Ressource kann dann für automatisierte und manuelle Tests verwendet werden. Die virtuellen Computer werden mit Artefakten oder Formeln erstellt, die auf Informationen aus dem Buildvorgang beruhen. So ist es möglich, verschiedene benutzerdefinierte Konfigurationen zu erstellen, die für die Testzwecke erforderlich sind.   

### <a name="release"></a>Freigabe 
DevTest Labs wird häufig für die Überprüfung während der Veröffentlichung verwendet, bevor der Code bereitgestellt wird. Der Ablauf ähnelt dem beim Testen im Abschnitt „Entwickeln“. Produktionsressourcen sollten nicht in DevTest Labs bereitgestellt werden. 

### <a name="customization"></a>Anpassung 
Azure DevOps enthält vorhandene Aufgaben, die das Bearbeiten von VMs und Umgebungen in bestimmten Labs ermöglichen. Azure DevOps Services ist eine Möglichkeit, die CI/CD-Pipeline zu verwalten. Sie können das Lab in ein beliebiges System integrieren, das die Fähigkeit zum Aufrufen von REST-APIs, Ausführen von PowerShell-Skripten oder die Nutzung von Azure CLI unterstützt. 

Einige CI-/CD-Pipeline-Manager verfügen auch über bestehende Open-Source-Plug-Ins, mit denen Ressourcen in Azure und DevTest Labs verwaltet werden können. Sie müssen möglicherweise einige benutzerdefinierte Skripts verwenden, um die spezifischen Anforderungen der jeweiligen Pipeline zu erfüllen.  Denken Sie daran: Bei der Ausführung einer Aufgabe wird ein Dienstprinzipal mit der entsprechenden Rolle für den Zugriff auf das Lab verwendet. Der Dienstprinzipal benötigt in der Regel den Zugriff der Rolle „Mitwirkender“ auf das Lab. Weitere Informationen finden Sie unter [Integrieren von Azure DevTest Labs in Ihre Azure DevOps-Pipeline für kontinuierliche Integration und kontinuierliche Lieferung](devtest-lab-integrate-ci-cd.md). 
 