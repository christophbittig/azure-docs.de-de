---
title: Verwenden von Verfügbarkeitszonen in Azure Kubernetes Service (AKS)
description: Erfahren Sie, wie Sie in Azure Kubernetes Service (AKS) einen Cluster erstellen, der Knoten über Verfügbarkeitszonen verteilt.
services: container-service
ms.custom: fasttrack-edit, references_regions, devx-track-azurecli
ms.topic: article
ms.date: 03/16/2021
ms.openlocfilehash: 18d7cba9fe92f2021757fdb58c9e76cd0c413e70
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2021
ms.locfileid: "132026829"
---
# <a name="create-an-azure-kubernetes-service-aks-cluster-that-uses-availability-zones"></a>Erstellen eines Azure Kubernetes Service-Clusters (AKS), der Verfügbarkeitszonen verwendet

Ein Azure Kubernetes Service-Cluster (AKS) verteilt Ressourcen wie Knoten und Speicher auf logische Abschnitte der zugrunde liegenden Azure-Infrastruktur. Dieses Bereitstellungsmodell stellt bei Verwendung von Verfügbarkeitszonen sicher, dass Knoten in einer bestimmten Verfügbarkeitszone physisch von den in einer anderen Verfügbarkeitszone definierten Knoten getrennt sind. AKS-Cluster, die mit mehreren in einem Cluster konfigurierten Verfügbarkeitszonen bereitgestellt werden, bieten ein höheres Maß an Verfügbarkeit zum Schutz vor Hardwareausfällen oder geplanten Wartungsereignissen.

Wenn Sie in einem Cluster Knotenpools definieren, die sich über mehrere Zonen erstrecken, können Knoten in einem bestimmten Knotenpool auch dann weiter ausgeführt werden, wenn eine einzelne Zone nicht mehr vorhanden ist. Ihre Anwendungen können auch dann weiterhin verfügbar sein, wenn in einem einzelnen Rechenzentrum ein physischer Fehler auftritt, wenn sie so orchestriert werden, dass sie einen Ausfall einer Teilmenge der Knoten tolerieren.

Dieser Artikel zeigt Ihnen, wie Sie einen AKS-Cluster erstellen und die Knotenkomponenten auf Verfügbarkeitszonen verteilen.

## <a name="before-you-begin"></a>Voraussetzungen

Azure CLI-Version 2.0.76 oder höher muss installiert und konfiguriert sein. Führen Sie `az --version` aus, um die Version zu ermitteln. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI][install-azure-cli].

## <a name="limitations-and-region-availability"></a>Einschränkungen und regionale Verfügbarkeit

AKS-Cluster können derzeit über Verfügbarkeitszonen in den folgenden Regionen erstellt werden:

* Australien (Osten)
* Brasilien Süd
* Kanada, Mitte
* Indien, Mitte
* USA (Mitte)
* Asien, Osten
* East US 
* USA (Ost) 2
* Frankreich, Mitte
* Deutschland, Westen-Mitte
* Japan, Osten
* Korea, Mitte
* Nordeuropa
* Norwegen, Osten
* Asien, Südosten
* USA Süd Mitte
* Schweden, Mitte
* UK, Süden
* US Government, Virginia
* Europa, Westen
* USA, Westen 2
* USA, Westen 3

Die folgenden Einschränkungen gelten, wenn Sie einen AKS-Cluster mit Verfügbarkeitszonen erstellen:

* Verfügbarkeitszonen können Sie nur beim Erstellen des Clusters oder Knotenpools definieren.
* Die Einstellungen der Verfügbarkeitszone können nach dem Erstellen des Clusters nicht mehr aktualisiert werden. Außerdem können Sie einen bestehenden, nicht verfügbaren Zonencluster nicht aktualisieren, wenn Sie Verfügbarkeitszonen verwenden möchten.
* Die gewählte Knotengröße (VM-SKU) muss in allen ausgewählten Verfügbarkeitszonen verfügbar sein.
* Cluster mit aktivierten Verfügbarkeitszonen erfordern den Einsatz von Azure Load Balancer Standard-Instanzen zur Verteilung über die Zonen. Dieser Load Balancer-Typ kann nur zum Zeitpunkt der Clustererstellung definiert werden. Weitere Informationen und die Einschränkungen des Standardlastenausgleichs finden Sie unter [Einschränkungen][standard-lb-limitations].

### <a name="azure-disks-limitations"></a>Einschränkungen für Azure-Datenträger

Volumes, die von Azure verwaltete Datenträger verwenden, sind derzeit keine zonenredundanten Ressourcen. Volumes können nicht Zonen übergreifend angefügt werden und müssen in derselben Zone wie ein bestimmter Knoten zusammengestellt werden, der den Zielpod hostet.

Kubernetes kennt Azure-Verfügbarkeitszonen seit Version 1.12. Wenn Sie ein PersistentVolumeClaim-Objekt bereitstellen, das auf einen verwalteten Azure-Datenträger in einem AKS-Cluster mit mehreren Zonen verweist, [kümmert sich Kubernetes um die Planung](https://kubernetes.io/docs/setup/best-practices/multiple-zones/#storage-access-for-zones) aller Pods, die diesen PVC in der richtigen Verfügbarkeitszone beanspruchen.

### <a name="azure-resource-manager-templates-and-availability-zones"></a>Azure Resource Manager-Vorlagen und -Verfügbarkeitszonen

Wenn Sie beim *Erstellen* eines AKS-Clusters einen [NULL-Wert in einer Vorlage][arm-template-null] mit Syntax wie `"availabilityZones": null` explizit definieren, behandelt die Resource Manager-Vorlage die Eigenschaft so, als ob sie nicht vorhanden wäre. Dies bedeutet, dass bei Ihrem Cluster keine Verfügbarkeitszonen aktiviert sind. Außerdem sind Verfügbarkeitszonen deaktiviert, wenn Sie einen Cluster mit einer Resource Manager-Vorlage erstellen, bei der die Eigenschaft für Verfügbarkeitszonen weggelassen wird.

Sie können die Einstellungen für Verfügbarkeitszonen in einem vorhandenen Cluster nicht aktualisieren, sodass das Verhalten beim Aktualisieren eines AKS-Clusters mit Resource Manager-Vorlagen anders ist.  Wenn Sie in Ihrer Vorlage einen NULL-Wert für Verfügbarkeitszonen explizit festlegen und Ihren Cluster *aktualisieren*, werden daran keine Änderungen für Verfügbarkeitszonen vorgenommen. Wenn Sie jedoch die Eigenschaft für Verfügbarkeitszonen mit Syntax wie `"availabilityZones": []` weglassen, versucht die Bereitstellung, Verfügbarkeitszonen in Ihrem vorhandenen AKS-Cluster zu deaktivieren, und **schlägt fehl**.

## <a name="overview-of-availability-zones-for-aks-clusters"></a>Übersicht über Verfügbarkeitszonen für AKS-Cluster

Verfügbarkeitszonen sind ein Hochverfügbarkeitsangebot, das Anwendungen und Daten vor Ausfällen von Rechenzentren schützt. Zonen sind eindeutige physische Standorte in einer Azure-Region. Jede Zone besteht aus mindestens einem Rechenzentrum, dessen Stromversorgung, Kühlung und Netzwerkbetrieb unabhängig funktionieren. Zur Gewährleistung der Resilienz ist in allen für Zonen aktivierten Regionen immer mehr als eine Zone aktiviert. Die physische Trennung von Verfügbarkeitszonen innerhalb einer Region schützt Anwendungen und Daten vor Ausfällen von Rechenzentren.

Weitere Informationen finden Sie unter [Was sind Verfügbarkeitszonen in Azure?][az-overview].

AKS-Cluster, die über Verfügbarkeitszonen bereitgestellt werden, können Knoten über mehrere Zonen innerhalb einer einzelnen Region verteilen. So kann beispielsweise ein Cluster in der Region  *USA, Osten 2* Knoten in allen drei Verfügbarkeitszonen in *USA, Osten 2* anlegen. Diese Verteilung der AKS-Clusterressourcen verbessert die Clusterverfügbarkeit, da sie gegen den Ausfall einer bestimmten Zone resistent sind.

![AKS-Knotenverteilung über Verfügbarkeitszonen hinweg](media/availability-zones/aks-availability-zones.png)

Wenn eine einzelne Zone nicht mehr verfügbar ist, werden Ihre Anwendungen weiterhin ausgeführt, wenn der Cluster auf mehrere Zonen verteilt ist.

## <a name="create-an-aks-cluster-across-availability-zones"></a>Erstellen eines AKS-Clusters über Verfügbarkeitszonen hinweg

Wenn Sie einen Cluster mit dem Befehl [az aks create][az-aks-create] erstellen, definiert der Parameter `--zones`, in welchen Zonen Agent-Knoten eingesetzt werden. Die Komponenten der Steuerungsebene, z. B. etcd oder die API, sind auf die verfügbaren Zonen in der Region verteilt, wenn Sie den `--zones`-Parameter zum Zeitpunkt der Clustererstellung definieren. Die spezifischen Zonen, auf die die Komponenten der Steuerungsebene verteilt werden, sind unabhängig davon, welche expliziten Zonen für den anfänglichen Knotenpool ausgewählt werden.

Wenn Sie beim Erstellen eines AKS-Clusters keine Zonen für den Standard-Agentpool definieren, ist die Verteilung der Steuerungsebenenkomponenten auf Verfügbarkeitszonen nicht garantiert. Sie können zwar zusätzliche Knotenpools mit dem Befehl [az aks nodepool add][az-aks-nodepool-add] hinzufügen und `--zones` für neue Knoten angeben, doch ändert dies nichts an der Verteilung der Steuerungsebene auf Zonen. Verfügbarkeitszoneneinstellungen können nur zum Zeitpunkt der Erstellung des Clusters oder Knotenpools definiert werden.

Das folgende Beispiel erstellt einen AKS-Cluster namens *myAKSCluster* in der Ressourcengruppe namens *myResourceGroup*. Es werden insgesamt *3* Knoten angelegt – ein Agent in Zone *1*, einer in *2* und dann ein weiterer in *3*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus2

az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --generate-ssh-keys \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --node-count 3 \
    --zones 1 2 3
```

Die Erstellung des AKS-Clusters dauert einige Minuten.

Bei der Entscheidung, welcher Zone ein neuer Knoten angehören soll, verwendet ein bestimmter AKS-Knotenpool ein [bestmögliches Zonengleichgewicht, das von den zugrunde liegenden Azure VM-Skalierungsgruppen geboten wird][vmss-zone-balancing]. Ein bestimmter AKS-Knotenpool befindet sich „im Gleichgewicht“, wenn die gleiche Anzahl von VMs oder +\- 1 VM in allen anderen Zonen der Skalierungsgruppe liegt.

## <a name="verify-node-distribution-across-zones"></a>Überprüfen der Verteilung der Knoten auf die Zonen

Wenn der Cluster bereit ist, listen Sie die Agent-Knoten in der Skalierungsgruppe auf, um zu sehen, in welcher Verfügbarkeitszone sie bereitgestellt werden.

Rufen Sie zuerst die Anmeldeinformationen für den AKS-Cluster mit dem Befehl [az aks get-credentials][az-aks-get-credentials] ab:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Verwenden Sie als Nächstes den Befehl [kubectl describe][kubectl-describe], um die Knoten im Cluster aufzulisten, und filtern Sie nach dem Wert *failure-domain.beta.kubernetes.io/zone*. Das folgende Beispiel gilt für eine Bash-Shell.

```console
kubectl describe nodes | grep -e "Name:" -e "failure-domain.beta.kubernetes.io/zone"
```

Das folgende Beispiel zeigt die drei Knoten, die auf die angegebene Region und die Verfügbarkeitszonen verteilt sind, wie z. B. *eastus2-1* für die erste Verfügbarkeitszone und *eastus2-2* für die zweite Verfügbarkeitszone:

```console
Name:       aks-nodepool1-28993262-vmss000000
            failure-domain.beta.kubernetes.io/zone=eastus2-1
Name:       aks-nodepool1-28993262-vmss000001
            failure-domain.beta.kubernetes.io/zone=eastus2-2
Name:       aks-nodepool1-28993262-vmss000002
            failure-domain.beta.kubernetes.io/zone=eastus2-3
```

Wenn Sie einem Agentpool zusätzliche Knoten hinzufügen, verteilt die Azure-Plattform die zugrunde liegenden VMs automatisch auf die angegebenen Verfügbarkeitszonen.

Beachten Sie, dass in neueren Kubernetes-Versionen (ab 1.17.0) in AKS `topology.kubernetes.io/zone`zusätzlich zur veralteten Bezeichnung`failure-domain.beta.kubernetes.io/zone` die neuere Bezeichnung verwendet wird. Sie können dasselbe Ergebnis wie oben auch durch Ausführen des folgenden Skripts erreichen:

```console
kubectl get nodes -o custom-columns=NAME:'{.metadata.name}',REGION:'{.metadata.labels.topology\.kubernetes\.io/region}',ZONE:'{metadata.labels.topology\.kubernetes\.io/zone}'
```

Dabei erhalten Sie eine kompaktere Ausgabe:

```console
NAME                                REGION   ZONE
aks-nodepool1-34917322-vmss000000   eastus   eastus-1
aks-nodepool1-34917322-vmss000001   eastus   eastus-2
aks-nodepool1-34917322-vmss000002   eastus   eastus-3
```

## <a name="verify-pod-distribution-across-zones"></a>Überprüfen der Verteilung der Pods auf die Zonen

Wie unter [Well-Known Labels, Annotations and Taints][kubectl-well_known_labels] (Bekannte Bezeichnungen, Anmerkungen und Taints) dokumentiert, wird in Kubernetes die Bezeichnung `failure-domain.beta.kubernetes.io/zone` zum automatischen Verteilen von Pods in einem Replikationscontroller oder Replikationsdienst in den verschiedenen verfügbaren Zonen verwendet. Um dies zu testen, können Sie den Cluster von 3 auf 5 Knoten hochskalieren, um die korrekte Verteilung der Pods zu überprüfen:

```azurecli-interactive
az aks scale \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 5
```

Wenn der Skalierungsvorgang nach einigen Minuten abgeschlossen ist, sollte über den Befehl `kubectl describe nodes | grep -e "Name:" -e "failure-domain.beta.kubernetes.io/zone"` in einer Bash-Shell eine Ausgabe ähnlich dem folgenden Beispiel erfolgen:

```console
Name:       aks-nodepool1-28993262-vmss000000
            failure-domain.beta.kubernetes.io/zone=eastus2-1
Name:       aks-nodepool1-28993262-vmss000001
            failure-domain.beta.kubernetes.io/zone=eastus2-2
Name:       aks-nodepool1-28993262-vmss000002
            failure-domain.beta.kubernetes.io/zone=eastus2-3
Name:       aks-nodepool1-28993262-vmss000003
            failure-domain.beta.kubernetes.io/zone=eastus2-1
Name:       aks-nodepool1-28993262-vmss000004
            failure-domain.beta.kubernetes.io/zone=eastus2-2
```

Nun sind zwei zusätzliche Knoten in Zone 1 und Zone 2 vorhanden. Sie können eine Anwendung bereitstellen, die aus drei Replikaten besteht. Als Beispiel wird NGINX verwendet:

```console
kubectl create deployment nginx --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
kubectl scale deployment nginx --replicas=3
```

Wenn Sie die Knoten anzeigen, auf denen Ihre Pods ausgeführt werden, sehen Sie, dass die Pods auf den Knoten ausgeführt werden, die den drei verschiedenen Verfügbarkeitszonen entsprechen. Beispielsweise erhalten Sie mit dem Befehl `kubectl describe pod | grep -e "^Name:" -e "^Node:"` in einer Bash-Shell eine Ausgabe ähnlich der folgenden:

```console
Name:         nginx-6db489d4b7-ktdwg
Node:         aks-nodepool1-28993262-vmss000000/10.240.0.4
Name:         nginx-6db489d4b7-v7zvj
Node:         aks-nodepool1-28993262-vmss000002/10.240.0.6
Name:         nginx-6db489d4b7-xz6wj
Node:         aks-nodepool1-28993262-vmss000004/10.240.0.8
```

Wie Sie in dieser Ausgabe sehen können, wird der erste Pod auf dem Knoten 0 ausgeführt, der sich in der Verfügbarkeitszone `eastus2-1` befindet. Der zweite Pod wird auf dem Knoten 2 ausgeführt, der `eastus2-3` entspricht, und der dritte Knoten auf dem Knoten 4, der `eastus2-2` entspricht. Ohne zusätzliche Konfiguration verteilt Kubernetes die Pods ordnungsgemäß auf alle drei Verfügbarkeitszonen.

## <a name="next-steps"></a>Nächste Schritte

Dieser Artikel beschreibt, wie Sie einen AKS-Cluster erstellen, der Verfügbarkeitszonen verwendet. Weitere Informationen zu hochverfügbaren Clustern finden Sie unter [Best Practices für Geschäftskontinuität und Notfallwiederherstellung in Azure Kubernetes Service (AKS)][best-practices-bc-dr].

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-overview]: ../availability-zones/az-overview.md
[best-practices-bc-dr]: operator-best-practices-multi-region.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[standard-lb-limitations]: load-balancer-standard.md#limitations
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-aks-nodepool-add]: /cli/azure/aks/nodepool#az_aks_nodepool_add
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[vmss-zone-balancing]: ../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md#zone-balancing
[arm-template-null]: ../azure-resource-manager/templates/template-expressions.md#null-values

<!-- LINKS - external -->
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-well_known_labels]: https://kubernetes.io/docs/reference/labels-annotations-taints/
