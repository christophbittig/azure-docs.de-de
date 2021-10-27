---
title: Beheben von Problemen mit Container Insights | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie Sie Probleme mit Container Insights behandeln und lösen können.
ms.topic: conceptual
ms.date: 03/25/2021
ms.openlocfilehash: 04fea3c36cbff4e2c8ecb315f6e3f93bc92aa2b3
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "130003248"
---
# <a name="troubleshooting-container-insights"></a>Problembehandlung für Container Insights

Wenn Sie die Überwachung Ihres AKS-Clusters (Azure Kubernetes Service) mit Container Insights konfigurieren, treffen Sie möglicherweise auf ein Problem, das die Sammlung von Daten oder die Meldung des Status verhindert. In diesem Artikel werden einige allgemeine Probleme und Schritte zur Problembehandlung ausführlich erläutert.

## <a name="authorization-error-during-onboarding-or-update-operation"></a>Autorisierungsfehler bei Onboarding- oder Updatevorgängen

Wenn Sie Container Insights aktivieren oder einen Cluster aktualisieren, damit dieser das Sammeln von Metriken unterstützt, tritt möglicherweise ein Fehler wie der folgende auf: *Client „<Identität des Benutzers>“ mit der Objekt-ID „<objectId des Benutzers>“ hat keine Berechtigung zum Ausführen der Aktion „Microsoft.Authorization/roleAssignments/write“ über Bereich*.

Während des Onboarding- oder Updatevorgangs wird versucht, die Rollenzuweisung **Überwachungsmetriken veröffentlichen** in der Clusterressource zu gewähren. Der Benutzer, der den Prozess zur Aktivierung von Container Insights oder den Aktualisierungsprozess initiiert, damit das Sammeln von Metriken unterstützt wird, muss Zugriff auf die Berechtigung **Microsoft.Authorization/roleAssignments/write** im Bereich der AKS-Clusterressource haben. Nur Mitglieder der Rollen **Besitzer** und **Benutzerzugriffsadministrator** verfügen über Zugriff auf diese Berechtigung. Wenn Ihre Sicherheitsrichtlinien das Zuweisen von differenzierten Berechtigungen erfordern, empfiehlt es sich, [benutzerdefinierte Rollen](../../role-based-access-control/custom-roles.md) anzuzeigen und den Benutzern, die diese Berechtigung benötigen, die entsprechende Rolle zuzuweisen.

Sie können diese Rolle auch manuell über das Azure-Portal zuweisen. Führen Sie dazu die folgenden Schritte aus:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Klicken Sie links oben im Azure-Portal auf **Alle Dienste**. Geben Sie in der Liste mit den Ressourcen **Kubernetes** ein. Sobald Sie mit der Eingabe beginnen, wird die Liste auf der Grundlage Ihrer Eingabe gefiltert. Wählen Sie **Azure Kubernetes** aus.
3. Wählen Sie in der Liste der Kubernetes-Cluster einen Cluster aus.
2. Klicken Sie im linken Menü auf **Zugriffssteuerung (IAM)** .
3. Klicken Sie auf **+ Hinzufügen**, um eine Rollenzuweisung hinzuzufügen. Wählen Sie die Rolle **Überwachungsmetriken veröffentlichen** aus, und geben Sie unterhalb des Felds **Auswählen** die Zeichenfolge **AKS** ein, um die Ergebnisse nur nach denjenigen Clusterdienstprinzipalen zu filtern, die im Abonnement definiert sind. Wählen Sie den Dienstprinzipal für diesen Cluster aus.
4. Wählen Sie **Speichern** aus, um das Zuweisen der Rolle abzuschließen.

## <a name="container-insights-is-enabled-but-not-reporting-any-information"></a>Container Insights ist aktiviert, meldet aber keinerlei Informationen

Wenn Container Insights erfolgreich aktiviert und konfiguriert wurde, Sie aber keine Statusinformationen anzeigen können oder eine Protokollabfrage keine Ergebnisse zurückgibt, diagnostizieren Sie das Problem, indem Sie die folgenden Schritte ausführen:

1. Überprüfen Sie den Zustand des Agents, indem Sie den folgenden Befehl ausführen:

    `kubectl get ds omsagent --namespace=kube-system`

    Die Ausgabe sollte wie im folgenden Beispiel aussehen, was auf eine ordnungsgemäße Bereitstellung hinweist:

    ```
    User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system
    NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
    omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
    ```
2. Wenn Sie über Windows Server-Knoten verfügen, überprüfen Sie den Status des Agents, indem Sie den folgenden Befehl ausführen:

    `kubectl get ds omsagent-win --namespace=kube-system`

    Die Ausgabe sollte wie im folgenden Beispiel aussehen, was auf eine ordnungsgemäße Bereitstellung hinweist:

    ```
    User@aksuser:~$ kubectl get ds omsagent-win --namespace=kube-system
    NAME                   DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                   AGE
    omsagent-win           2         2         2         2            2           beta.kubernetes.io/os=windows   1d
    ```
3. Überprüfen Sie den Bereitstellungsstatus in Agent-Version *06072018* oder höher mit dem Befehl:

    `kubectl get deployment omsagent-rs -n=kube-system`

    Die Ausgabe sollte wie im folgenden Beispiel aussehen, was auf eine ordnungsgemäße Bereitstellung hinweist:

    ```
    User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system
    NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
    omsagent   1         1         1            1            3h
    ```

4. Überprüfen Sie den Status des Pods, um festzustellen, ob er ausgeführt wird, mit dem folgenden Befehl: `kubectl get pods --namespace=kube-system`

    Die Ausgabe sollte etwa dem folgenden Beispiel mit dem Status *Ausgeführt* für „omsagent“ ähneln:

    ```
    User@aksuser:~$ kubectl get pods --namespace=kube-system
    NAME                                READY     STATUS    RESTARTS   AGE
    aks-ssh-139866255-5n7k5             1/1       Running   0          8d
    azure-vote-back-4149398501-7skz0    1/1       Running   0          22d
    azure-vote-front-3826909965-30n62   1/1       Running   0          22d
    omsagent-484hw                      1/1       Running   0          1d
    omsagent-fkq7g                      1/1       Running   0          1d
    omsagent-win-6drwq                  1/1       Running   0          1d
    ```

## <a name="error-messages"></a>Fehlermeldungen

In der unten stehenden Tabelle sind bekannte Fehler aufgeführt, die bei der Verwendung von Container Insights auftreten können.

| Fehlermeldungen  | Aktion |
| ---- | --- |
| Fehlermeldung `No data for selected filters`  | Die Einrichtung der Überwachung des Datenflusses für neu erstellte Cluster kann einige Zeit in Anspruch nehmen. Es dauert mindestens 10 bis 15 Minuten, bis Daten für Ihren Cluster angezeigt werden. |
| Fehlermeldung `Error retrieving data` | Während der Einrichtung eines Azure Kubernetes Service-Clusters für die Überwachung von Integrität und Leistung wird zwischen dem Cluster und dem Azure Log Analytics-Arbeitsbereich eine Verbindung hergestellt. Zum Speichern sämtlicher Überwachungsdaten für Ihren Cluster wird ein Log Analytics-Arbeitsbereich verwendet. Dieser Fehler kann auftreten, wenn Ihr Log Analytics-Arbeitsbereich gelöscht wurde. Überprüfen Sie, ob der Arbeitsbereich gelöscht wurde. Ist das der Fall, müssen Sie die Überwachung des Clusters mit Container Insights erneut aktivieren und einen vorhandenen Arbeitsbereich angeben oder einen neuen Arbeitsbereich erstellen. Zum erneuten Aktivieren müssen Sie die Überwachung für den Cluster [deaktivieren](container-insights-optout.md) und dann mit Container Insights [erneut aktivieren](container-insights-enable-new-cluster.md). |
| `Error retrieving data` nach dem Hinzufügen von Container Insights mithilfe von „az aks cli“ | Beim Aktivieren der Überwachung mithilfe von `az aks cli` wird Container Insights möglicherweise nicht ordnungsgemäß bereitgestellt. Überprüfen Sie, ob die Lösung bereitgestellt ist. Wechseln Sie zur Überprüfung zu Ihrem Log Analytics-Arbeitsbereich, und wählen Sie im linken Bereich **Lösungen** aus, um zu überprüfen, ob die Lösung verfügbar ist. Um dieses Problem zu beheben, müssen Sie die Lösung erneut bereitstellen. Befolgen Sie dazu die Anweisungen zum [Bereitstellen von Container Insights](container-insights-onboard.md). |

Um Sie bei der Diagnose zu unterstützen, haben wir ein [Skript zur Problembehandlung](https://github.com/microsoft/Docker-Provider/tree/ci_dev/scripts/troubleshoot) bereitgestellt.

## <a name="container-insights-agent-replicaset-pods-are-not-scheduled-on-non-azure-kubernetes-cluster"></a>ReplicaSet-Pods des Container Insights-Agents sind in einem Nicht-Azure Kubernetes-Cluster nicht geplant

ReplicaSet-Pods des Container Insights-Agents sind für die Zeitplanung von den folgenden Knotenselektoren auf den Workerknoten (oder Agent-Knoten) abhängig:

```
nodeSelector:
  beta.kubernetes.io/os: Linux
  kubernetes.io/role: agent
```

Wenn Ihren Workerknoten keine Knotenbezeichnungen angefügt sind, werden ReplicaSet-Pods des Agents nicht geplant. Anweisungen zum Anfügen der Bezeichnung finden Sie in der Dokumentation zum [Zuweisen von Bezeichnungsselektoren in Kubernetes](https://kubernetes.io/docs/concepts/configuration/assign-pod-node/).

## <a name="performance-charts-dont-show-cpu-or-memory-of-nodes-and-containers-on-a-non-azure-cluster"></a>Leistungsdiagramme zeigen keine CPU- oder Arbeitsspeicherauslastung für Knoten und Container in einem Nicht-Azure-Cluster

Pods des Container Insights-Agents verwenden den cAdvisor-Endpunkt auf dem Knoten-Agent zum Erfassen der Leistungsmetriken. Vergewissern Sie sich, dass der Container-Agent auf dem Knoten so konfiguriert ist, dass `cAdvisor port: 10255` auf allen Knoten im Cluster zum Erfassen von Leistungsmetriken geöffnet werden kann.

## <a name="non-azure-kubernetes-cluster-are-not-showing-in-container-insights"></a>Nicht-Azure Kubernetes-Cluster werden in Container Insights nicht angezeigt

Um den Nicht-Azure Kubernetes-Cluster in Container Insights anzuzeigen, ist Lesezugriff für den Log Analytics-Arbeitsbereich, der diese Einblicke unterstützt, und für die Container Insights-Lösungsressource **ContainerInsights (*Arbeitsbereich*)** erforderlich.

## <a name="metrics-arent-being-collected"></a>Metriken werden nicht erfasst

1. Überprüfen Sie, ob sich das Cluster in einer [unterstützten Region für benutzerdefinierte Metriken](../essentials/metrics-custom-overview.md#supported-regions)befindet.

2. Überprüfen Sie mithilfe des folgenden CLI-Befehls, ob die Rollenzuweisung des **Herausgebers für Überwachungsmetriken** vorhanden ist

    ``` azurecli
    az role assignment list --assignee "SP/UserassignedMSI for omsagent" --scope "/subscriptions/<subid>/resourcegroups/<RG>/providers/Microsoft.ContainerService/managedClusters/<clustername>" --role "Monitoring Metrics Publisher"
    ```
    Bei Clustern mit MSI ändert sich die vom Benutzer zugewiesene Client-ID für omsagent bei jedem Aktivieren und Deaktivieren der Überwachung. Daher sollte die Rollenzuweisung in der aktuellen MSI-Client-ID vorhanden sein. 

3. Für Cluster mit aktivierter Azure Active Directory Pod-Identität und Verwendung von MSI:

   - Überprüfen Sie mithilfe des folgenden Befehls, ob die erforderliche Bezeichnung **kubernetes.Azure.com/ManagedBy: aks**  auf den omsagent-Pods vorhanden ist:

        `kubectl get pods --show-labels -n kube-system | grep omsagent`

    - Überprüfen Sie, ob Ausnahmen aktiviert sind, wenn die Pod-Identität mithilfe einer der unterstützten Methoden unter https://github.com/Azure/aad-pod-identity#1-deploy-aad-pod-identity aktiviert ist.

        Führen Sie zur Überprüfung den folgenden Befehl aus:

        `kubectl get AzurePodIdentityException -A -o yaml`

        Die Ausgabe sollte in etwa wie folgt aussehen:

        ```
        apiVersion: "aadpodidentity.k8s.io/v1"
        kind: AzurePodIdentityException
        metadata:
        name: mic-exception
        namespace: default
        spec:
        podLabels:
        app: mic
        component: mic
        ---
        apiVersion: "aadpodidentity.k8s.io/v1"
        kind: AzurePodIdentityException
        metadata:
        name: aks-addon-exception
        namespace: kube-system
        spec:
        podLabels:
        kubernetes.azure.com/managedby: aks
        ```

## <a name="installation-of-azure-monitor-containers-extension-fail-with-an-error-containing-manifests-contain-a-resource-that-already-exists-on-azure-arc-enabled-kubernetes-cluster"></a>Bei der Installation der Azure Monitor-Containererweiterung tritt auf Kubernetes-Clustern mit Azure Arc-Unterstützung der Fehler auf, dass Manifeste eine Ressource enthalten, die bereits vorhanden ist.
Der Fehler _Manifeste enthalten eine Ressource, die bereits vorhanden ist_ gibt an, dass Ressourcen des Container Insights-Agents bereits im Kubernetes-Cluster mit Azure Arc-Unterstützung aktiviert sind. Dies weist darauf hin, dass der Container Insights-Agent bereits über das Helm-Chart „azuremonitor-containers“ oder über das Überwachungs-Add-On installiert wurde, wenn es sich um einen AKS-Cluster mit Azure Arc-Verbindung handelt. Die Lösung für dieses Problem besteht darin, die vorhandenen Ressourcen des Container Insights-Agents (sofern vorhanden) zu bereinigen und dann die Azure Monitor-Containererweiterung zu aktivieren.

### <a name="for-non-aks-clusters"></a>Für Nicht-AKS-Cluster 
1.  Führen Sie für den Kubernetes-Cluster mit Azure Arc-Verbindung den folgenden Befehl aus, um zu überprüfen, ob das Helm-Chart-Release „azmon-containers-release-1“ vorhanden ist:

    `helm list  -A`

2.  Wenn die Ausgabe des obigen Befehls darauf hinweist, dass „azmon-containers-release-1“ vorliegt, löschen Sie das Helm-Chart-Release:

    `helm del azmon-containers-release-1`

### <a name="for-aks-clusters"></a>Für AKS-Cluster
1.  Führen Sie die folgenden Befehle aus, und suchen Sie nach dem omsagent-Add-On-Profil, um zu überprüfen, ob das AKS-Überwachungs-Add-On aktiviert ist:

    ```
    az  account set -s <clusterSubscriptionId>
    az aks show -g <clusterResourceGroup> -n <clusterName>
    ```

2.  Wenn in der Ausgabe des obigen Befehls die omsagent-Add-On-Profilkonfiguration mit der Ressourcen-ID des Log Analytics-Arbeitsbereichs vorhanden ist, weist dies darauf hin, dass das AKS-Überwachungs-Add-On aktiviert ist und deaktiviert werden muss:

    `az aks disable-addons -a monitoring -g <clusterResourceGroup> -n <clusterName>`

Wenn die Probleme bei der Installation von Azure Monitor-Containererweiterungen durch die oben genannten Schritte nicht behoben werden konnten, erstellen Sie ein Ticket für Microsoft, um weitere Untersuchungen zu ermöglichen.


## <a name="next-steps"></a>Nächste Schritte

Bei aktivierter Überwachung zum Erfassen der Integritätsmetriken für AKS-Clusterknoten wie auch für Pods stehen diese Integritätsmetriken im Azure-Portal zur Verfügung. Informationen zum Erlernen der Verwendung von Container Insights finden Sie unter [Anzeigen der Integrität von Azure Kubernetes Service](container-insights-analyze.md).
