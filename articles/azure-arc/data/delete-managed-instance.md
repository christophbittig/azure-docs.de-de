---
title: Löschen von Instanzen von SQL Managed Instance mit Azure Arc-Unterstützung
description: Löschen von Instanzen von SQL Managed Instance mit Azure Arc-Unterstützung
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: 19f5befde22ed7b16302b7da5df313c476b47194
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122339397"
---
# <a name="delete-azure-arc-enabled-sql-managed-instance"></a>Löschen von Instanzen von SQL Managed Instance mit Azure Arc-Unterstützung
In diesem Artikel wird beschrieben, wie Sie eine Instanz von SQL Managed Instance mit Azure Arc-Unterstützung löschen.


## <a name="view-existing-azure-arc-enabled-sql-managed-instances"></a>Anzeigen vorhandener Instanzen von SQL Managed Instance mit Azure Arc-Unterstützung
Führen Sie den folgenden Befehl aus, um SQL Managed Instance-Instanzen anzuzeigen:

```azurecli
az sql mi-arc list --k8s-namespace <namespace> --use-k8s
```

Die Ausgabe sollte ungefähr wie folgt aussehen:

```console
Name    Replicas    ServerEndpoint    State
------  ----------  ----------------  -------
demo-mi 1/1         10.240.0.4:32023  Ready
```

## <a name="delete-a-azure-arc-enabled-sql-managed-instance"></a>Löschen einer Instanz von SQL Managed Instance mit Azure Arc-Unterstützung
Führen Sie zum Löschen einer SQL Managed Instance-Instanz den folgenden Befehl aus:

```azurecli
az sql mi-arc delete -n <NAME_OF_INSTANCE> --k8s-namespace <namespace> --use-k8s
```

Die Ausgabe sollte ungefähr wie folgt aussehen:

```azurecli
# az sql mi-arc delete -n demo-mi --k8s-namespace <namespace> --use-k8s
Deleted demo-mi from namespace arc
```

## <a name="reclaim-the-kubernetes-persistent-volume-claims-pvcs"></a>Freigeben von Ansprüchen für persistente Kubernetes-Volumes (PVCs)

Durch das Löschen einer SQL Managed Instance-Instanz werden nicht deren zugeordnete [PVCs](https://kubernetes.io/docs/concepts/storage/persistent-volumes/) (Persistent Volume Claims, Ansprüche für persistente Volumes) entfernt. Dies ist beabsichtigt. Auf diese Weise kann der Benutzer einfacher auf die Datenbankdateien zugreifen, falls eine Instanz versehentlich gelöscht wurde. Das Löschen von PVCs ist nicht obligatorisch, Es wird aber empfohlen. Wenn Sie diese PVCs nicht freigeben, treten letztendlich Fehler auf, da dem Kubernetes-Cluster der Speicherplatz ausgeht. Führen Sie die folgenden Schritte aus, um die PVCs freizugeben:

### <a name="1-list-the-pvcs-for-the-server-group-you-deleted"></a>1. Auflisten der PVCs für die Servergruppe, die Sie gelöscht haben
Führen Sie den folgenden Befehl aus, um die PVCs aufzulisten:
```console
kubectl get pvc
```

Beachten Sie im folgenden Beispiel die PVCs für die von Ihnen gelöschten SQL Managed Instance-Instanzen.
```console
# kubectl get pvc -n arc

NAME                    STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS      AGE
data-demo-mi-0        Bound     pvc-1030df34-4b0d-4148-8986-4e4c20660cc4   5Gi        RWO            managed-premium   13h
logs-demo-mi-0        Bound     pvc-11836e5e-63e5-4620-a6ba-d74f7a916db4   5Gi        RWO            managed-premium   13h
```

### <a name="2-delete-each-of-the-pvcs"></a>2. Löschen aller PVCs
Löschen Sie die Daten, und protokollieren Sie die PVCs für jede der von Ihnen gelöschten SQL Managed Instance-Instanzen.
Das allgemeine Format des Befehls lautet folgendermaßen: 
```console
kubectl delete pvc <name of pvc>
```

Zum Beispiel:
```console
kubectl delete pvc data-demo-mi-0 -n arc
kubectl delete pvc logs-demo-mi-0 -n arc
```

Mit jedem dieser kubectl-Befehle wird das erfolgreiche Löschen des PVCs bestätigt. Zum Beispiel:
```console
persistentvolumeclaim "data-demo-mi-0" deleted
persistentvolumeclaim "logs-demo-mi-0" deleted
```
  

> [!NOTE]
> Wie bereits erwähnt, kann das Nichtlöschen der PVCs dazu führen, dass Ihr Kubernetes-Cluster letztlich in eine Situation gerät, in der er Fehler verursacht. Einige dieser Fehler können sein, dass Ressourcen nicht über die Kubernetes-API erstellt, gelesen, aktualisiert oder gelöscht oder dass Befehle wie `az arcdata dc export` nicht ausgeführt werden können, da die Controllerpods aufgrund dieses Speicherproblems (normales Kubernetes-Verhalten) von den Kubernetes-Knoten entfernt werden können.
>
> Beispielsweise können in den Protokollen Meldungen wie die folgende angezeigt werden:  
> - Annotations: microsoft.com/ignore-pod-health: true  
> - Status:         Fehler  
> - Ursache:         Evicted (Entfernt)  
> - Meldung:        The node was low on resource: ephemeral-storage (Zu wenig Speicher des Knotens für die Ressource: ephemeral-storage). Container controller was using 16372Ki, which exceeds its request of 0 (Der Containercontroller hat 16372Ki verwendet, was die Anforderung von 0 überschreitet).

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über [Features und Funktionen von SQL Managed Instance mit Azure Arc-Unterstützung](managed-instance-features.md).

[Beginnen Sie mit der Erstellung eines Datencontrollers](create-data-controller.md)

Wurde bereits ein Datencontroller erstellt? [Erstellen von Instanzen von SQL Managed Instance mit Azure Arc-Unterstützung](create-sql-managed-instance.md)