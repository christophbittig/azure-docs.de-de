---
title: Verwenden von Azure Policy zum Anwenden skalierbarer Clusterkonfigurationen
services: azure-arc
ms.service: azure-arc
ms.date: 03/03/2021
ms.topic: article
description: Verwenden von Azure Policy zum Anwenden skalierbarer Clusterkonfigurationen
keywords: Kubernetes, Arc, Azure, K8s, Container
ms.openlocfilehash: 9713f57207942c9fed5613b298ea83865ef685bd
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132059429"
---
# <a name="use-azure-policy-to-apply-gitops-configurations-at-scale"></a>Verwenden von Azure Policy zum Anwenden von GitOps-Konfigurationen im großen Stil.

Sie können Azure Policy verwenden, um Konfigurationen (Ressourcentyp `Microsoft.KubernetesConfiguration/sourceControlConfigurations`) in großem Umfang auf Azure Arc-fähige Kubernetes-Cluster (`Microsoft.Kubernetes/connectedclusters`) anzuwenden.

Um Azure Policy zu verwenden, wählen Sie eine integrierte GitOps-Richtliniendefinition aus, und erstellen Sie eine Richtlinienzuweisung. Beim Erstellen der Richtlinienzuweisung:
1. Legen Sie den Bereich für die Zuweisung fest.
    * Der Bereich sind alle Ressourcengruppen in einem Abonnement, einer Verwaltungsgruppe oder bestimmten Ressourcengruppen.
2. Legen Sie die Parameter für die GitOps-Konfiguration fest, die erstellt wird. 

Sobald die Zuweisung erstellt ist, identifiziert die Azure Policy Engine alle Azure Arc-fähigen Kubernetes-Cluster, die sich innerhalb des Bereichs befinden, und wendet die GitOps-Konfiguration auf jeden Cluster an.

Um die Trennung von Belangen zu ermöglichen, können Sie mehrere Richtlinienzuweisungen erstellen, die jeweils über eine andere GitOps-Konfiguration verfügen, die auf ein anderes Git-Repository verweist. Beispielsweise kann ein Repository von Clusteradministratoren und andere Repositorys von Anwendungsteams verwendet werden.

> [!TIP]
> Für diese Szenarien gibt es integrierte Richtliniendefinitionen:
> * Öffentliches Repository oder privates Repository mit SSH-Schlüsseln, die von Flux erstellt wurden: `Configure Kubernetes clusters with specified GitOps configuration using no secrets`
> * Privates Repository mit benutzerseitig bereitgestellten SSH-Schlüsseln: `Configure Kubernetes clusters with specified GitOps configuration using SSH secrets`
> * Privates Repository mit benutzerseitig bereitgestellten HTTPS-Schlüsseln: `Configure Kubernetes clusters with specified GitOps configuration using HTTPS secrets`

## <a name="prerequisite"></a>Voraussetzung

Überprüfen Sie, ob Sie über `Microsoft.Authorization/policyAssignments/write`-Berechtigungen für den Bereich (Abonnement oder Ressourcengruppe) verfügen, in dem Sie diese Richtlinienzuweisung erstellen möchten.

## <a name="create-a-policy-assignment"></a>Erstellen einer Richtlinienzuweisung

1. Navigieren Sie im Azure-Portal zu **Policy**.
1. Wählen Sie im Abschnitt **Erstellung** auf der Randleiste die Option **Definitionen** aus.
1. Wählen Sie in der Kategorie "Kubernetes" die integrierte Richtliniendefinition "Kubernetes-Cluster mit der angegebenen GitOps-Konfiguration ohne Geheimnisse konfigurieren". 
1. Klicken Sie auf **Zuweisen**.
1. Legen Sie den **Bereich** auf die Verwaltungsgruppe, das Abonnement oder die Ressourcengruppe fest, in der oder dem die Richtlinienzuweisung angewandt wird.
    * Wenn Sie irgendwelche Ressourcen aus dem Zuweisungsbereich der Richtlinie ausschließen möchten, setzen Sie **Ausschlüsse**.
1. Legen Sie für die Richtlinienzuweisung einen einfach erkennbaren **Namen** und eine **Beschreibung** fest.
1. Stellen Sie sicher, dass **Richtlinienerzwingung** auf **Aktiviert** festgelegt ist.
1. Wählen Sie **Weiter** aus.
1. Legen Sie die Parameterwerte für die Erstellung der `sourceControlConfiguration` fest.
    * Weitere Informationen zu Parametern finden Sie im [Tutorial zum Bereitstellen von GitOps-Konfigurationen](./tutorial-use-gitops-connected-cluster.md).
1. Wählen Sie **Weiter** aus.
1. Aktivieren Sie **Korrekturtask erstellen**.
1. Stellen Sie sicher, dass **Verwaltete Identität erstellen** aktiviert ist und dass die Identität **Mitwirkender**-Berechtigungen besitzt. 
    * Weitere Informationen finden Sie im [Schnellstart zum Erstellen einer Richtlinienzuweisung](../../governance/policy/assign-policy-portal.md) und im Artikel [Korrigieren nicht konformer Ressourcen mit Azure Policy](../../governance/policy/how-to/remediate-resources.md).
1. Klicken Sie auf **Überprüfen + erstellen**.

Nach der Erstellung der Richtlinienzuweisung wird die Konfiguration auf neue Azure Arc-fähige Kubernetes-Cluster angewendet, die innerhalb des Bereichs der Richtlinienzuweisung erstellt werden.

Bei vorhandenen Clustern müssen Sie eventuell manuell einen Wartungstask ausführen. Es dauert in der Regel 10 bis 20 Minuten, bis die Richtlinienzuweisung wirksam wird.

## <a name="verify-a-policy-assignment"></a>Überprüfen einer Richtlinienzuweisung

1. Navigieren Sie im Azure-Portal zu einem Ihrer Azure Arc-aktivierten Kubernetes-Cluster.
1. Wählen Sie im Abschnitt **Einstellungen** auf der Randleiste **Richtlinien** aus. 
    * In der Liste sollten Sie die Richtlinienzuweisung sehen, die Sie zuvor erstellt haben und deren **Konformitätsstatus** auf *konform* gesetzt ist.
1. Wählen Sie im Abschnitt **Einstellungen** auf der Randleiste **GitOps** aus.
    * In der Liste der Konfigurationen sollte die durch die Richtlinienzuweisung erstellte Konfiguration angezeigt werden.
1. Verwenden Sie `kubectl`, um den Cluster abzufragen. 
    * Sie sollten den Namespace und die Artefakte sehen, die von der GitOps-Konfiguration erstellt wurden.
    * Die von den Manifesten im Git-Repository beschriebenen Objekte sollten sichtlich im Cluster bereitgestellt werden.

## <a name="next-steps"></a>Nächste Schritte

[Azure Monitor für Container mit Azure Arc-aktivierten Kubernetes-Clustern einrichten](../../azure-monitor/containers/container-insights-enable-arc-enabled-clusters.md).
