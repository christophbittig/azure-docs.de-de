---
title: Entwicklerumgebung für innere Schleifen für auf GitOps umsteigende Teams
services: azure-arc
ms.service: azure-arc
ms.date: 06/18/2021
ms.topic: conceptual
author: sudivate
ms.author: sudivate
description: 'Dieser Artikel bietet eine konzeptionelle Übersicht über die Entwicklerumgebung für innere Schleifen für auf GitOps umsteigende Teams. '
keywords: GitOps, Kubernetes, K8s, Azure, Helm, Arc, AKS, Azure Kubernetes Service, Container, CI, CD, Azure DevOps, innere Schleife, Entwicklerumgebung
ms.openlocfilehash: 3bd60c3236bb187f14b924f4246d8e6fcfed4ed3
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/09/2021
ms.locfileid: "129706421"
---
# <a name="inner-loop-developer-experience-for-teams-adopting-gitops"></a>Entwicklerumgebung für innere Schleifen für auf GitOps umsteigende Teams

In diesem Artikel wird beschrieben, wie eine etablierte innere Schleife die Produktivität von Entwicklern steigern und Teams, die auf GitOps umsteigen, einen nahtlosen Übergang von der inneren Entwicklungsschleife zur äußeren Schleife ermöglichen kann.

## <a name="inner-dev-loop-frameworks"></a>Frameworks für innere Entwicklungsschleifen

Das Erstellen und Bereitstellen von Containern kann die innere Entwicklerumgebung verlangsamen und die Produktivität des Teams beeinträchtigen. Cloudnative Entwicklungsteams profitieren von einem stabilen Framework für innere Entwicklungsschleifen. Frameworks für innere Entwicklungsschleifen unterstützen den iterativen Prozess des Schreibens, Erstellens und Debuggens von Code.

Zu den Funktionen von Frameworks für innere Entwicklungsschleifen gehören u. a.:

 
- Automatisieren sich wiederholender Schritte wie Erstellen von Code und Containern sowie deren Bereitstellung im Zielcluster 
- Einfaches Arbeiten mit Remote- und lokalen Clustern und Unterstützung des lokalen Tunneldebuggens bei einer Hybrideinrichtung
- Möglichkeit des Konfigurierens eines benutzerdefinierten Flows für teambasierte Produktivität
- Ermöglichen der Behandlung von Abhängigkeiten von Microservices 
- Hot Reload, Portweiterleitung, Protokoll- und Terminalzugriff 



Je nach Reifegrad und Komplexität des Diensts entscheiden Entwicklungsteams, welches Clustersetup sie zur Beschleunigung der inneren Entwicklungsschleife benötigen: 

* Vollständig lokal
* Vollständig remote
* Hybrid 


Glücklicherweise gibt es zahlreiche Frameworks, die die aufgeführten Funktionen unterstützen. Microsoft bietet Bridge to Kubernetes zum Debuggen lokaler Tunnel. Auf dem Markt gibt ähnliche Angebote wie DevSpace, Scaffold und Scaffold.

> [!NOTE]
> Das Marktangebot [DevSpace](https://github.com/loft-sh/devspace) ist nicht zu verwechseln mit dem früheren Microsoft-Produkt DevSpace, das jetzt [Bridge to Kubernetes](https://code.visualstudio.com/docs/containers/bridge-to-kubernetes) heißt.


## <a name="inner-loop-to-outer-loop-transition"></a>Übergang von innerer Schleife zu äußerer Schleife 

Nachdem Sie ein Entwicklungsframework für innere Schleifen bewertet und ausgewählt haben, sorgen Sie für einen nahtlosen Übergang von innerer Schleife zu äußerer Schleife.

Wie im Beispiel des Artikels [CI/CD-Workflow mit GitOps](conceptual-gitops-ci-cd.md) beschrieben, arbeitet ein Anwendungsentwickler an Anwendungscode innerhalb eines Anwendungsrepositorys. Dieses Anwendungsrepository enthält auch übergeordnete Bereitstellungsvorlagen für Helm und/oder Kustomize. CI/CD-Pipelines:

* Generieren Sie die untergeordneten Manifeste anhand der übergeordneten Vorlagen, und fügen Sie umgebungsspezifische Werte hinzu.
* Erstellen Sie einen Pull Request, bei dem die untergeordneten Manifeste mit dem GitOps-Repository zusammengeführt werden, das den gewünschten Zustand für die spezifische Umgebung enthält. 

Ähnliche untergeordnete Manifeste können lokal für die innere Entwicklungsschleife generiert werden, wobei die lokalen Konfigurationswerte des Entwicklers verwendet werden. Anwendungsentwickler können die Codeänderungen iterieren und die untergeordneten Manifeste verwenden, um Anwendungen bereitzustellen und zu debuggen. Die Generierung der untergeordneten Manifeste kann in einen Workflow mit innerer Schleife integriert werden, wobei die lokale Konfiguration des Entwicklers verwendet wird. Die meisten Frameworks für innere Schleifen ermöglichen das Konfigurieren benutzerdefinierter Flows, indem sie entweder durch benutzerdefinierte Plug-Ins erweitert oder Aufrufe von Skripts auf Grundlage von Hooks eingefügt werden. 

## <a name="example-inner-loop-workflow-built-with-devspace-framework"></a>Beispielworkflow für innere Schleifen, der mit dem Framework DevSpace erstellt wurde


### <a name="diagram-a-inner-loop-flow"></a>Diagramm A: Flow mit innerer Schleife
:::image type="content" source="media/dev-inner-loop.png" alt-text="Diagramm des Flows mit innerer Schleife mit DevSpace.":::

### <a name="diagram-b-inner-loop-to-outer-loop-transition"></a>Diagramm B: Übergang von innerer Schleife zu äußerer Schleife
:::image type="content" source="media/inner-loop-to-outer-loop.png" alt-text="Diagramm des Übergangs von innerer Schleife zu äußerer Schleife." :::


## <a name="example-workflow"></a>Beispielworkflow
Als Anwendungsentwickler führt Alice folgende Schritte aus:
- Erstellen der Datei „devspace.yaml“ zum Konfigurieren der inneren Schleife
- Schreiben und Testen von Anwendungscode auf Effizienz mithilfe der inneren Schleife
- Bereitstellen in der Staging- oder Produktionsumgebung mithilfe der äußeren Schleife


Angenommen, Alice möchte die Anwendung im lokalen oder Remotecluster aktualisieren, ausführen und debuggen.

1. Alice aktualisiert in der ENV-Datei die lokale Konfiguration der Entwicklungsumgebung.
1. Alice führt `devspace use context` aus und wählt den Kubernetes-Clusterkontext aus.
1.  Alice wählt einen Namespace aus, mit dem sie arbeiten möchte, indem sie `devspace use namespace <namespace_name>` ausführt.
1.  Alice kann Änderungen am Anwendungscode iterieren und die Anwendung im Zielcluster bereitstellen und debuggen, indem sie `devspace dev` ausführt.
1. Durch die Ausführung von `devspace dev` werden untergeordnete Manifeste auf Grundlage der lokalen Konfiguration von Alice generiert und die Anwendung bereitgestellt. Diese untergeordneten Manifeste werden in „devspace.yaml“ mit DevSpace-Hooks konfiguriert.
1. Alice muss den Container nicht jedes Mal neu erstellen, wenn sie Codeänderungen vornimmt, da DevSpace Hot Reload aktiviert und die Dateisynchronisierung nutzt, um ihre letzten Änderungen in den Container zu kopieren.
1. Durch die Ausführung von `devspace dev` werden auch alle in „devspace.yaml“ konfigurierten Abhängigkeiten bereitgestellt, z. B. Back-End-Abhängigkeiten vom Front-End. 
1. Alice testet ihre Änderungen, indem sie über die in „devspace.yaml“ konfigurierte Weiterleitung auf die Anwendung zugreift.
1. Sobald Alice ihre Änderungen abgeschlossen hat, kann sie die Bereitstellung löschen, indem sie `devspace purge` ausführt und einen neuen Pull Request erstellt, um ihre Änderungen in den Dev-Branch des Anwendungsrepositorys zu mergen.

> [!NOTE]
> Den Beispielcode für den obigen Workflow finden Sie in diesem [GitHub-Repository](https://github.com/Azure/arc-cicd-demo-src).

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über das Herstellen von Verbindungen zwischen Ihrem Cluster und einem Git-Repository als [Konfigurationsressource mit Kubernetes mit Azure Arc-Unterstützung](./conceptual-configurations.md).