---
title: Häufig gestellte Fragen zum Azure Arc-fähigen Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 02/19/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Dieser Artikel enthält eine Liste mit häufig gestellten Fragen zu Azure Arc-fähigem Kubernetes.
keywords: Kubernetes, Arc, Azure, Container, Konfiguration, GitOps, FAQ, Häufig gestellte Fragen
ms.openlocfilehash: f678bd23bc4e9e40f718d72ccde8069c36673ac6
ms.sourcegitcommit: 7b6ceae1f3eab4cf5429e5d32df597640c55ba13
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/31/2021
ms.locfileid: "123272923"
---
# <a name="frequently-asked-questions---azure-arc-enabled-kubernetes"></a>Häufig gestellte Fragen: Azure Arc-fähiges Kubernetes

In diesem Artikel werden häufig gestellte Fragen zu Azure Arc-fähigem Kubernetes behandelt.

## <a name="what-is-the-difference-between-azure-arc-enabled-kubernetes-and-azure-kubernetes-service-aks"></a>Worin besteht der Unterschied zwischen Azure Arc-fähigem Kubernetes und Azure Kubernetes Service (AKS)?

AKS ist das Managed Kubernetes-Angebot von Azure. AKS vereinfacht die Bereitstellung eines Managed Kubernetes-Clusters in Azure, indem ein Großteil der Komplexität und des betrieblichen Aufwands nach Azure ausgelagert wird. Da die Kubernetes-Mastereinheiten von Azure verwaltet werden, müssen Sie sich nur um die Verwaltung und Wartung der Agent-Knoten kümmern.

Azure Arc-fähiges Kubernetes ermöglicht es Ihnen, die Verwaltungsfunktionen von Azure (wie Azure Monitor und Azure Policy) zu erweitern, indem Kubernetes-Cluster mit Azure verbunden werden. Den zugrunde liegenden Kubernetes-Cluster pflegen Sie selber.

## <a name="do-i-need-to-connect-my-aks-clusters-running-on-azure-to-azure-arc"></a>Muss ich meine AKS-Cluster, die in Azure ausgeführt werden, mit Azure Arc verbinden?

Das Verbinden eines Azure Kubernetes Service-Clusters (AKS) mit Azure Arc ist nur erforderlich, um Arc-fähige Dienste wie App Services und Data Services auf dem Cluster ausführen zu können. Dies kann mithilfe des Features für [benutzerdefinierte Standorte](custom-locations.md) von Kubernetes mit Arc-Aktivierung erfolgen. Dies ist vorerst eine zeitliche Einschränkung, bis Clustererweiterungen und benutzerdefinierte Standorte nativ auf AKS-Clustern eingeführt werden.

Wenn Sie keine benutzerdefinierten Standorte verwenden und lediglich Verwaltungsfeatures wie Azure Monitor und Azure Policy (Gatekeeper) verwenden möchten, sind diese nativ in AKS verfügbar, und in solchen Fällen ist keine Verbindung mit Azure Arc erforderlich.
    
## <a name="should-i-connect-my-aks-hci-cluster-and-kubernetes-clusters-on-azure-stack-hub-and-azure-stack-edge-to-azure-arc"></a>Sollte ich meinen AKS-HCI-Cluster und meine Kubernetes-Cluster auf Azure Stack Hub und Azure Stack Edge mit Azure Arc verbinden?

Ja, wenn Sie Ihren AKS-HCI-Cluster oder Ihre Kubernetes-Cluster auf Azure Stack Edge oder Azure Stack Hub mit Azure Arc verbinden, wird Clustern die Ressourcendarstellung in Azure Resource Manager bereitgestellt. Diese Ressourcendarstellung erweitert Funktionen wie Clusterkonfiguration, Azure Monitor und Azure Policy (Gatekeeper) auf die verbundenen Kubernetes-Cluster.

Wenn sich der Kubernetes-Cluster mit Azure Arc-Aktivierung unter Azure Stack Edge, AKS unter Azure Stack HCI (>= Update von April 2021) oder AKS unter Windows Server 2019 Datacenter (>= Update von April 2021) befindet, dann ist die Kubernetes-Konfiguration kostenlos enthalten.

## <a name="how-to-address-expired-azure-arc-enabled-kubernetes-resources"></a>Wie geht man mit abgelaufenen Azure Arc-fähigen Kubernetes-Ressourcen um?

Die systemseitig zugewiesene verwaltete Identität, die Ihrem Kubernetes-Cluster mit Azure Arc-Unterstützung zugeordnet ist, wird nur von den Arc-Agents für die Kommunikation mit den Azure Arc-Diensten verwendet. Das Zertifikat, das dieser systemseitig zugewiesenen verwalteten Identität zugeordnet ist, hat ein Ablauffenster von 90 Tagen, und die Agents versuchen weiterhin, dieses Zertifikat zwischen Tag 46 und Tag 90 zu erneuern. Sobald dieses Zertifikat abläuft, wird die Ressource als `Expired` (abgelaufen) betrachtet, und alle Features (z.  B. Konfiguration, Überwachung und Richtlinie) funktionieren in diesem Cluster nicht mehr, und Sie müssen dann Cluster löschen und erneut mit Azure Arc verbinden. Daher ist es ratsam, den Cluster mindestens einmal innerhalb des Zeitfensters von Tag 46 bis Tag 90 online zu bringen, um die Erneuerung des Zertifikats der verwalteten Identität sicherzustellen.

Führen Sie den folgenden Befehl aus, um zu überprüfen, wann das Zertifikat für einen bestimmten Cluster abläuft:

```console
az connectedk8s show -n <name> -g <resource-group>
```

In der Ausgabe zeigt der Wert von `managedIdentityCertificateExpirationTime` an, wann das Zertifikat der verwalteten Identität abläuft (90D-Markierung für dieses Zertifikat). 

Wenn der Wert von `managedIdentityCertificateExpirationTime` einen Zeitstempel aus der Vergangenheit anzeigt, wird das Feld `connectivityStatus` in der obigen Ausgabe auf `Expired` festgelegt. Führen Sie in solchen Fällen folgende Schritte aus, damit Ihr Kubernetes-Cluster wieder mit Azure Arc zusammenarbeitet:

1. Löschen Sie die Kubernetes-Ressource mit Azure Arc-Aktivierung sowie die Agents auf dem Cluster. 

    ```console
    az connectedk8s delete -n <name> -g <resource-group>
    ```

1. Erstellen Sie die Azure Arc-fähige Kubernetes-Ressource erneut, indem Sie Agents auf dem Cluster bereitstellen.
    
    ```console
    az connectedk8s connect -n <name> -g <resource-group>
    ```

> [!NOTE]
> `az connectedk8s delete` löscht außerdem Konfigurationen und Clustererweiterungen auf dem Cluster. Nachdem Sie `az connectedk8s connect` ausgeführt haben, erstellen Sie die Konfigurationen und Clustererweiterungen auf dem Cluster erneut, entweder manuell oder mithilfe von Azure Policy.

## <a name="if-i-am-already-using-cicd-pipelines-can-i-still-use-azure-arc-enabled-kubernetes-and-configurations"></a>Kann ich, wenn ich bereits CI/CD-Pipelines verwende, trotzdem Azure Arc-aktiviertes Kubernetes und Konfigurationen verwenden?

Ja, Sie können immer noch Konfigurationen auf einem Cluster verwenden, der Bereitstellungen über eine CI/CD-Pipeline empfängt. Im Vergleich zu herkömmlichen CI/CD-Pipelines bietet die Funktion „Konfigurationen“ zwei zusätzliche Vorteile:

**Driftausgleich**

Die CI/CD-Pipeline wendet Änderungen während der Pipelineausführung nur einmal an. Der GitOps-Operator im Cluster fragt jedoch kontinuierlich das Git-Repository ab, um den gewünschten Zustand der Kubernetes-Ressourcen im Cluster abzurufen. Wenn der GitOps-Operator feststellt, dass der gewünschte Zustand von Ressourcen vom tatsächlichen Zustand der Ressourcen im Cluster abweicht, wird diese Abweichung (Drift) ausgeglichen.

**Anwenden von GitOps im großen Stil**

CI/CD-Pipelines sind hilfreich für ereignisgesteuerte Bereitstellungen in Ihrem Kubernetes-Cluster (Beispiel: Push in ein Git-Repository). Wenn Sie jedoch dieselbe Konfiguration für alle Ihre Kubernetes-Cluster bereitstellen möchten, müssen Sie die Anmeldeinformationen jedes Kubernetes-Clusters manuell für die CI/CD-Pipeline konfigurieren. 

Da Azure Resource Manager Ihre Konfigurationen für Kubernetes mit Azure Arc-Aktivierung verwaltet, können Sie mithilfe von Azure Policy im Rahmen eines Abonnements oder einer Ressourcengruppe automatisch dieselbe Konfiguration für alle Kubernetes-Ressourcen mit Azure Arc-Aktivierung erstellen. Diese Funktion kann sogar auf Azure Arc-fähige Kubernetes-Ressourcen angewendet werden, die nach der Richtlinienzuweisung erstellt wurden.

Dieses Feature wendet Baselinekonfigurationen (wie Netzwerkrichtlinien, Rollenbindungen und Podsicherheitsrichtlinien) auf den gesamten Bestand der Kubernetes-Cluster an, um Compliance- und Governanceanforderungen zu entsprechen.

## <a name="next-steps"></a>Nächste Schritte

* Führen Sie den Schnellstart zum [Verbinden eines Kubernetes-Clusters mit Azure Arc](./quickstart-connect-cluster.md) durch.
* Sie haben bereits einen Kubernetes-Cluster, der mit Azure Arc verbunden ist? [Erstellen Sie Konfigurationen in Ihrem Arc-fähigen Kubernetes-Cluster](./tutorial-use-gitops-connected-cluster.md).
* Erfahren Sie, wie Sie [Azure Policy zum Anwenden von Konfigurationen im großen Stil verwenden](./use-azure-policy.md).