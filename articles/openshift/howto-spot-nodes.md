---
title: Verwenden von Azure Spot Virtual Machines in einem Azure Red Hat OpenShift (ARO)-Cluster
description: Erfahren Sie, wie Sie Azure Spot Virtual Machines in Azure Red Hat OpenShift (ARO) verwenden.
author: nilsanderselde
ms.author: suvetriv
ms.service: azure-redhat-openshift
keywords: spot, knoten, aro, bereitstellen, openshift, red hat
ms.topic: how-to
ms.date: 10/21/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 941db40b96d9e7a00c32b42817a3f2a93e17643a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131084424"
---
# <a name="use-azure-spot-virtual-machines-in-an-azure-red-hat-openshift-aro-cluster"></a>Verwenden von Azure Spot Virtual Machines in einem Azure Red Hat OpenShift (ARO)-Cluster

Dieser Artikel enthält alle Informationen, die Sie zum Konfigurieren Ihres ARO-Clusters (Azure Red Hat OpenShift) für die Verwendung von Azure Spot Virtual Machines benötigen.

Wenn Sie Azure Spot-VMs verwenden, profitieren Sie von unserer ungenutzten Kapazität und erzielen erhebliche Kosteneinsparungen. Wenn die Kapazität von Azure wieder benötigt wird, werden die Azure-Spot-VMs durch die Azure-Infrastruktur entfernt. Weitere Informationen zu Spot-Instanzen finden Sie unter [Spot Virtual Machines](../virtual-machines/spot-vms.md).

## <a name="before-you-begin"></a>Vorbereitung

Stellen Sie im Vorfeld sicher, dass Sie bereits einen Azure Red Hat Openshift-Cluster bereitgestellt haben. Wenn Sie einen ARO-Cluster benötigen, finden Sie unter [Tutorial: Erstellen eines Azure Red Hat OpenShift 4-Clusters](tutorial-create-cluster.md) Informationen zu einem öffentlichen Cluster bzw. unter [Erstellen eines privaten Azure Red Hat OpenShift 4-Clusters](howto-create-private-cluster-4x.md) Informationen zu einem privaten Cluster. Die Schritte zum Konfigurieren Ihres Clusters für die Verwendung von Spot-VMs sind bei privaten und öffentlichen Clustern identisch.

## <a name="add-spot-vms"></a>Hinzufügen von Spot-VMs

Die Verwaltung von Computern in Azure Red Hat Openshift erfolgt mithilfe von MachineSet. Bei MachineSet-Ressourcen handelt es sich um Computergruppen. MachineSets sind also ähnlich wie ReplicaSets für Pods. Wenn Sie mehr Computer benötigen oder die Zahl herunterskalieren müssen, ändern Sie das Feld *Replicas* des MachineSets so, dass Ihre Computeanforderungen erfüllt werden. Weitere Informationen finden Sie in unserer OpenShift [MachineSet-Dokumentation](https://docs.openshift.com/container-platform/4.8/machine_management/creating_machinesets/creating-machineset-azure.html).

Die Verwendung von Spot-VMs wird über das Hinzufügen des Felds `spotVMOptions` innerhalb der Vorlagenspezifikation eines MachineSets definiert.
Um dieses MachineSet zu erstellen, werden wir:

1. eine Kopie eines MachineSets in Ihrem Cluster ausführen,
2. eine modifizierte MachineSet-Konfiguration erstellen, und
3. dieses MachineSet in Ihrem Cluster bereitstellen.

Als Erstes müssen Sie [mithilfe der CLI eine Verbindung zu Ihrem OpenShift-Cluster herstellen](tutorial-connect-cluster.md).

```azurecli-interactive
oc login $apiServer -u kubeadmin -p <kubeadmin password>
```

Als Nächstes müssen Sie die MachineSets in Ihrem Cluster auflisten. In einem Standardcluster werden drei MachineSets bereitgestellt: 
```azurecli-interactive
oc get machinesets -n openshift-machine-api
```

Im Folgenden wird eine Beispielausgabe dieses Befehls angezeigt: 
```
NAME                                    DESIRED   CURRENT   READY   AVAILABLE   AGE
aro-cluster-5t2dj-worker-eastus1   1         1         1       1           2d22h
aro-cluster-5t2dj-worker-eastus2   1         1         1       1           2d22h
aro-cluster-5t2dj-worker-eastus3   1         1         1       1           2d22h
```

Als Nächstes müssen Sie das bereitgestellte MachineSet beschreiben. Ersetzen Sie \<machineset\> durch eines der oben aufgelisteten MachineSets und geben Sie es in eine Datei aus.

```azurecli-interactive
oc get machineset <machineset> -n openshift-machine-api -o yaml > spotmachineset.yaml
```

Sie müssen die folgenden Parameter des MachineSets ändern:
- `metadata.name`
- `spec.selector.matchLabels.machine.openshift.io/cluster-api-machineset`
- `spec.template.metadata.labels.machine.openshift.io/cluster-api-machineset`
- `spec.template.spec.providerSpec.value.spotVMOptions` (Fügen Sie dieses Feld hinzu und legen Sie es auf `{}` fest.)


Nachfolgend finden Sie ein verkürztes Beispiel für Spot MachineSet-YAML. Dieses Beispiel hebt die wichtigsten Änderungen hervor, die vorgenommen werden müssen, wenn Sie ein neues Spot MachineSet auf Grundlage eines vorhandenen Worker-MachineSets erstellen. Darüber hinaus enthält es einige zusätzliche Kontextinformationen. (Es stellt jedoch kein vollständiges und funktionales MachineSet dar; nachfolgend wurden viele Felder weggelassen.)

```
apiVersion: machine.openshift.io/v1beta1
kind: MachineSet
metadata:
  name: aro-cluster-abcd1-spot-eastus
spec:
  replicas: 2
  selector:
    matchLabels:
      machine.openshift.io/cluster-api-cluster: aro-cluster-abcd1
      machine.openshift.io/cluster-api-machineset: aro-cluster-abcd1-spot-eastus
  template:
    metadata:
        machine.openshift.io/cluster-api-machineset: aro-cluster-abcd1-spot-eastus
    spec:
      providerSpec:
        value:
          spotVMOptions: {}
      taints:
        - effect: NoExecute
          key: spot
          value: 'true'
```

Nachdem die Datei aktualisiert wurde, müssen Sie sie anwenden.

```azurecli-interactive
oc create -f spotmachineset.yaml
```

Führen Sie den folgenden Befehl aus, um zu überprüfen, ob Ihr MachineSet erfolgreich erstellt wurde:
```azurecli-interactive
oc get machinesets -n openshift-machine-api
```

Nachfolgend ist eine Beispielausgabe gezeigt. Ihr MachineSet ist bereit, sobald sich Ihre Computer im Zustand „Bereit“ befinden.
```
  NAME                                    DESIRED   CURRENT   READY   AVAILABLE   AGE
aro-cluster-5t2dj-worker-eastus1           1         1         1       1           3d1h
aro-cluster-5t2dj-worker-eastus2           1         1         1       1           3d1h
aro-cluster-5t2dj-worker-eastus3           1         1         1       1           3d1h
spot                                       1         1         1       1           2m47s
```

## <a name="schedule-interruptible-workloads"></a>Planen von unterbrechbaren Workloads

Es wird empfohlen, einen Taint auf die Spot-Knoten anzuwenden, um zu verhindern, dass nicht-unterbrechbare Knoten auf ihnen geplant werden. In diesem Fall sollten Sie zu allen Pods, die auf den Knoten geplant sind, Toleranzen für diesen Taint hinzufügen. Sie können den Taint über die MachineSet-Spezifikation auf die Knoten anwenden.

Zum Beispiel indem Sie den folgenden YAML-Code zu `spec.template.spec` hinzufügen:

```
     taints:
        - effect: NoExecute
          key: spot
          value: 'true'
```

Dies würde zum einen verhindern, dass Pods, die keine Toleranz für diesen Taint haben, auf dem entsprechenden Knoten geplant werden, und zum anderen würden alle Pods ohne diese Toleranz direkt vom Knoten entfernt werden.

Weitere Informationen über das Anwenden von Taints und Toleranzen finden Sie unter [Steuern von Podplatzierungen mithilfe von Knotentaints](https://docs.openshift.com/container-platform/4.7/nodes/scheduling/nodes-scheduler-taints-tolerations.html).

## <a name="quota"></a>Kontingent

Computer können aufgrund von Kontingentproblemen in einen fehlerhaften Zustand wechseln. Dies geschieht u. a. wenn das Kontingent für den von Ihnen verwendeten Computertyp für einen kurzen Zeitraum zu niedrig ist, obwohl es letztendlich ausreichen würde (z. B. wenn der Löschvorgang eines Knoten noch läuft, während ein anderer erstellt wird). Aus diesem Grund wird empfohlen, das Kontingent für den Computertyp, den Sie für Spot-Instanzen verwenden, etwas höher als erforderlich anzusetzen (z. B. um 2*n, wobei n die Anzahl der von einem Computer verwendeten Kerne ist). Durch diesen Puffer können Sie die Wiederherstellung fehlerhafter Computer vermeiden. Dies ist zwar relativ einfach, stellt aber dennoch einen manuellen Eingriff dar.

## <a name="node-readiness"></a>Bereitschaft von Knoten

Wie in der oben verlinkten Spot-VM-Dokumentation erläutert, wechseln VMs in den Bereitstellungsstatus „Zuordnung aufgehoben“, wenn sie generell nicht mehr oder nicht mehr zum angegebenen Höchstpreis verfügbar sind.

In OpenShift zeigt sich dies in Form von Knoten, die **Nicht bereit** sind. In der Phase **Als Knoten bereitgestellt** bleiben die Computer fehlerfrei.

Die Knoten wechseln wieder in den Zustand **Bereit**, sobald die VMs wieder verfügbar sind.

## <a name="troubleshooting"></a>Problembehandlung

### <a name="node-stuck-in-not-ready-state-underlying-vm-deallocated"></a>Knoten dauerhaft im „Nicht bereit“-Status, Zuordnung von zugrundeliegender VM aufgehoben

Wenn ein Knoten über einen längeren Zeitraum den Status „Nicht bereit“ hat, nachdem die Zuordnung seiner zugrundeliegenden VM aufgehoben wurde, können Sie entweder versuchen, den Knoten oder das entsprechende OpenShift-Computerobjekt zu löschen.

### <a name="spot-machine-stuck-in-failed-state"></a>Spot-Computer dauerhaft im fehlerhaften Zustand

Wenn sich ein Computer (OpenShift-Objekt), der eine Spot-VM verwendet, dauerhaft im fehlerhaften Zustand befindet, können Sie versuchen, ihn manuell zu löschen. Wenn er aufgrund eines 403-Fehlers nicht gelöscht werden kann (weil die VM nicht mehr vorhanden ist), müssen Sie den Computer bearbeiten und die Finalizer entfernen.
