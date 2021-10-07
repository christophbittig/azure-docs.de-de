---
title: Clusterkonfiguration in Azure Kubernetes Services (AKS)
description: Erfahren Sie, wie Sie in Azure Kubernetes Service (AKS) einen Cluster konfigurieren.
services: container-service
ms.topic: article
ms.date: 02/09/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: 6c454c23eec0bb5b0fef1ceca3ad8f8e4c52493d
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124812595"
---
# <a name="configure-an-aks-cluster"></a>Konfigurieren eines AKS-Clusters

Im Rahmen der Erstellung eines AKS-Clusters müssen Sie möglicherweise Ihre Clusterkonfiguration an Ihre Anforderungen anpassen. In diesem Artikel werden einige Optionen zur Anpassung Ihres AKS-Clusters vorgestellt.

## <a name="os-configuration"></a>Betriebssystemkonfiguration

Die allgemein verfügbare Version (General Availability, GA) von AKS unterstützt Ubuntu 18.04 als Standardbetriebssystem für Knoten in Clustern.

## <a name="container-runtime-configuration"></a>Konfiguration der Containerruntime

Eine Containerruntime ist eine Software, die Container ausführt und Containerimages auf einem Knoten verwaltet. Die Runtime erleichtert die Abstraktion von sys-Aufrufen oder betriebssystemspezifischen Funktionen zum Ausführen von Containern unter Linux oder Windows. Bei Linux-Knotenpools wird `containerd` für Knotenpools mit Version 1.19 und höher von Kubernetes verwendet. Für Windows Server 2019-Knotenpools ist `containerd` in der Vorschau verfügbar und kann in Knotenpools mit Kubernetes 1.20 und höher verwendet werden. Docker wird jedoch weiterhin standardmäßig verwendet.

[`Containerd`](https://containerd.io/) ist eine mit [OCI](https://opencontainers.org/) (Open Container Initiative) kompatible Kerncontainerruntime, die den Mindestsatz erforderlicher Funktionen zum Ausführen von Containern und zum Verwalten von Images auf einem Knoten bereitstellt. Sie wurde im März 2017 an die Cloud Native Compute Foundation (CNCF) [übergeben](https://www.cncf.io/announcement/2017/03/29/containerd-joins-cloud-native-computing-foundation/). Die aktuelle Moby-Version (Upstream-Docker), die AKS verwendet, baut bereits auf `containerd` auf, wie oben dargestellt.

Bei Knoten und Knotenpools, die auf `containerd` basieren, kommuniziert das Kubelet statt mit `dockershim` direkt über das CRI-Plug-In (Container Runtime Interface) mit `containerd`. Gegenüber der Docker CRI-Implementierung benötigt der Flow so weniger Hops. Daraus ergibt sich eine verringerte Latenz beim Podstart und eine geringere Ressourcenauslastung (CPU und Arbeitsspeicher).

Durch die Verwendung von `containerd` für AKS-Knoten werden die Podstartlatenz und der Ressourcenverbrauch durch die Containerlaufzeit auf dem Knoten verringert. Diese Verbesserungen sind eine Folge der neuen Architektur, bei der das Kubelet direkt über das CRI-Plug-In mit `containerd` kommuniziert. Bei der Moby/Docker-Architektur kommuniziert das Kubelet hingegen mit `dockershim` und der Docker-Engine, bevor `containerd` erreicht wird, was zu zusätzlichen Hops im Flow führt.

![Docker CRI 2](media/cluster-configuration/containerd-cri.png)

`Containerd` funktioniert mit jeder allgemein verfügbaren Version von Kubernetes in AKS und mit jeder Upstream-Kubernetes-Version ab 1.19 und unterstützt alle Features von Kubernetes und AKS.

> [!IMPORTANT]
> Cluster mit Knotenpools, die mit Kubernetes v1.19 oder höher erstellt wurden, verwenden standardmäßig `containerd` als Containerruntime. Cluster mit Knotenpools mit früheren unterstützten Kubernetes-Versionen erhalten Docker als Containerruntime. Linux-Knotenpools werden auf `containerd` aktualisiert, sobald die Kubernetes-Version des Knotenpools auf eine Version aktualisiert wird, die `containerd` unterstützt. Sie können weiterhin Docker-Knotenpools und -Cluster unter älteren unterstützten Versionen verwenden, bis diese nicht mehr unterstützt werden.
> 
> Die Verwendung von `containerd` mit Windows Server 2019-Knotenpools befindet sich derzeit in der Vorschau. Weitere Informationen finden Sie unter [Hinzufügen eines Windows Server-Knotenpools mit `containerd`][aks-add-np-containerd].
> 
> Es wird dringend empfohlen, Ihre Workloads in AKS-Knotenpools mit `containerd` zu testen, bevor Sie Cluster mit einer Kubernetes-Version verwenden, die `containerd` für Ihre Knotenpools unterstützt.

### <a name="containerd-limitationsdifferences"></a>Einschränkungen und Unterschiede von `Containerd`

* Bei `containerd` wird empfohlen, [`crictl`](https://kubernetes.io/docs/tasks/debug-application-cluster/crictl) anstelle der Docker-CLI als Ersatz-CLI für die **Problembehandlung** bei Pods, Containern und Containerimages auf Kubernetes-Knoten zu verwenden (z. B. `crictl ps`). 
   * Diese unterstützt nicht alle Funktionen der Docker-CLI. Sie ist nur für die Problembehandlung gedacht.
   * `crictl` bietet eine für Kubernetes besser geeignete Ansicht von Containern mit Unterstützung von Konzepten wie Pods.
* Bei `Containerd` wird die Protokollierung im standardisierten `cri`-Protokollierungsformat eingerichtet. (Dieses unterscheidet sich vom Format des aktuellen Docker-JSON-Treibers.) Ihre Protokollierungslösung muss das `cri`-Protokollierungsformat unterstützen (wie z. B. [Azure Monitor für Container](../azure-monitor/containers/container-insights-enable-new-cluster.md)).
* Sie können nicht mehr auf die Docker-Engine `/var/run/docker.sock` zugreifen oder Docker-in-Docker (DinD) verwenden.
  * Wenn Sie zurzeit Anwendungsprotokolle oder Überwachungsdaten aus der Docker-Engine extrahieren, verwenden Sie stattdessen z. B. [Azure Monitor für Container](../azure-monitor/containers/container-insights-enable-new-cluster.md). Darüber hinaus unterstützt AKS keine Ausführung von Out-of-Band-Befehlen auf den Agent-Knoten, die zu einer Instabilität führen könnten.
  * Selbst bei Verwendung von Docker wird dringend davon abgeraten, direkt über die oben genannten Methoden Images zu erstellen und die Docker-Engine zu nutzen. Kubernetes erkennt diese genutzten Ressourcen nicht vollständig, und diese Ansätze bringen zahlreiche Probleme mit sich, die z. B. [hier](https://jpetazzo.github.io/2015/09/03/do-not-use-docker-in-docker-for-ci/) und [hier](https://securityboulevard.com/2018/05/escaping-the-whale-things-you-probably-shouldnt-do-with-docker-part-1/) erläutert werden.
* Erstellen von Images: Sie können Ihren aktuellen Docker-Buildworkflow weiterhin wie gewohnt verwenden, es sei denn, Sie erstellen Images in Ihrem AKS-Cluster. Erwägen Sie in diesem Fall die Umstellung auf die empfohlene Vorgehensweise zum Erstellen von Images mithilfe von [ACR Tasks](../container-registry/container-registry-quickstart-task-cli.md) oder eine sicherere clusterinterne Option wie [docker buildx](https://github.com/docker/buildx).

## <a name="generation-2-virtual-machines"></a>Virtuelle Computer der Generation 2

Azure unterstützt [virtuelle Computer (VMs) der Generation 2 (Gen2)](../virtual-machines/generation-2.md). VMs der Generation 2 unterstützen wichtige Features, die bei VMs der Generation 1 (Gen1) nicht unterstützt werden. Zu diesen Features gehören mehr Speicher, Intel Software Guard Extensions (Intel SGX) und virtualisierter persistenter Speicher (vPMEM).

VMs der Generation 2 verwenden die neue UEFI-basierte Startarchitektur und nicht mehr die BIOS-basierte Architektur von VMs der Generation 1.
Nur bestimmte SKUs und Größen unterstützen Gen2-VMs. Überprüfen Sie die [Liste der unterstützten Größen](../virtual-machines/generation-2.md#generation-2-vm-sizes), um festzustellen, ob Ihre SKU Gen2 unterstützt oder erfordert.

Zusätzlich unterstützen nicht alle VM-Images Gen2. Auf AKS-Gen2 wird für VMs das neue [AKS Ubuntu 18.04-Image](#os-configuration) verwendet. Dieses Image unterstützt alle Gen2-SKUs und -Größen.

## <a name="ephemeral-os"></a>Kurzlebiges Betriebssystem

Standardmäßig repliziert Azure den Betriebssystemdatenträger automatisch für einen virtuellen Computer in Azure Storage, um Datenverluste zu vermeiden, wenn der virtuelle Computer auf einen anderen Host verschoben werden muss. Da Container jedoch nicht für die Speicherung des lokalen Zustands vorgesehen sind, hat dieses Verhalten einen begrenzten Nutzen und einige Nachteile wie etwa eine langsamere Knotenbereitstellung und eine höhere Wartezeit bei Lese-/Schreibvorgängen.

Im Gegensatz dazu werden kurzlebige Betriebssystemdatenträger genau wie ein temporärer Datenträger nur auf dem Hostcomputer gespeichert. Dies führt zu einer geringeren Wartezeit bei Lese-/Schreibvorgängen und ermöglicht eine schnellere Knotenskalierung sowie schnellere Clusterupgrades.

Ein kurzlebiger Betriebssystemdatenträger ist genau wie der temporäre Datenträger im Preis des virtuellen Computers enthalten. Es entstehen also keine zusätzlichen Speicherkosten.

> [!IMPORTANT]
>Wenn ein Benutzer nicht explizit verwaltete Datenträger für das Betriebssystem anfordert, verwendet AKS standardmäßig (soweit möglich) das kurzlebige Betriebssystem für eine bestimmte Knotenpoolkonfiguration.

Wenn das kurzlebige Betriebssystem verwendet wird, muss der Betriebssystemdatenträger in den VM-Cache passen. Die Größen für den VM-Cache sind in der [Azure-Dokumentation](../virtual-machines/dv3-dsv3-series.md) in Klammern neben dem E/A-Durchsatz („Cachegröße in GiB“) aufgeführt.

Beispiel: Standard_DS2_v2, die Standard-VM-Größe für AKS mit der standardmäßigen Betriebssystemdatenträger-Größe von 100 GB unterstützt das kurzlebige Betriebssystem, hat aber nur eine Cachegröße von 86 GB. Diese Konfiguration ist standardmäßig auf verwaltete Datenträger festgelegt, wenn der Benutzer nicht explizit etwas anderes angibt. Wenn ein Benutzer das kurzlebige Betriebssystem explizit angefordert hat, erhält er eine Validierungsfehlermeldung.

Wenn ein Benutzer dieselbe Größe Standard_DS2_v2 mit einem 60-GB-Betriebssystemdatenträger anfordert, würde diese Konfiguration standardmäßig das kurzlebige Betriebssystem unterstützen: die angeforderte Größe von 60 GB liegt unter der maximalen Cachegröße von 86 GB.

Wenn Sie Standard_D8s_v3 mit 100-GB-Betriebssystemdatenträger verwenden, unterstützt diese VM-Größe das kurzlebige Betriebssystem und hat 200 GB Cachespeicher. Wenn ein Benutzer den Typ des Betriebssystemdatenträgers nicht angibt, erhält der Knotenpool standardmäßig das kurzlebige Betriebssystem. 

Ein kurzlebiges Betriebssystem erfordert mindestens Version 2.15.0 der Azure CLI.

### <a name="use-ephemeral-os-on-new-clusters"></a>Verwenden eines kurzlebigen Betriebssystems in neuen Clustern

Konfigurieren Sie den Cluster für die Verwendung kurzlebiger Betriebssystemdatenträger bei der Clustererstellung. Verwenden Sie das Flag `--node-osdisk-type`, um die Art des Betriebssystemdatenträgers für den neuen Cluster auf ein kurzlebiges Betriebssystem festzulegen.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup -s Standard_DS3_v2 --node-osdisk-type Ephemeral
```

Wenn Sie einen herkömmlichen Cluster mit netzwerkbasierten Betriebssystemdatenträgern erstellen möchten, geben Sie hierzu `--node-osdisk-type=Managed` an. Sie können auch weitere kurzlebige Betriebssystemknotenpools hinzufügen, wie im Anschluss beschrieben.

### <a name="use-ephemeral-os-on-existing-clusters"></a>Verwenden eines kurzlebigen Betriebssystems in vorhandenen Clustern
Konfigurieren Sie einen neuen Knotenpool für die Verwendung kurzlebiger Betriebssystemdatenträger. Verwenden Sie das Flag `--node-osdisk-type`, um die Art des Betriebssystemdatenträgers für diesen Knotenpool festzulegen.

```azurecli
az aks nodepool add --name ephemeral --cluster-name myAKSCluster --resource-group myResourceGroup -s Standard_DS3_v2 --node-osdisk-type Ephemeral
```

> [!IMPORTANT]
> Mit einem kurzlebigen Betriebssystem können Sie VM- und Instanzimages bis zur Größe des VM-Caches bereitstellen. Im Fall von AKS werden in der Betriebssystemdatenträger-Konfiguration des Standardknotens 128 GB verwendet. Sie benötigen also eine VM-Größe, deren Cache größer als 128 GB ist. Die Cachegröße von Standard_DS2_v2 beträgt standardmäßig 86 GB und ist somit nicht ausreichend. Standard_DS3_v2 weist eine Cachegröße von 172 GB auf und ist damit groß genug. Sie können auch `--node-osdisk-size` verwenden, um die Standardgröße des Betriebssystemdatenträgers zu verringern. Die Mindestgröße für AKS-Images beträgt 30 GB. 

Wenn Sie Knotenpools mit netzwerkbasierten Betriebssystemdatenträgern erstellen möchten, geben Sie `--node-osdisk-type Managed` an.

## <a name="custom-resource-group-name"></a>Name der benutzerdefinierten Ressourcengruppe

Wenn Sie in Azure einen Azure Kubernetes Service-Cluster bereitstellen, wird für die Workerknoten eine zweite Ressourcengruppe erstellt. In AKS wird der Knotenressourcengruppe standardmäßig der Name `MC_resourcegroupname_clustername_location` zugewiesen. Sie können jedoch auch einen eigenen Namen angeben.

Installieren Sie die aks-preview-Erweiterungsversion 0.3.2 oder höher der Azure CLI, wenn Sie einen eigenen Ressourcengruppennamen angeben möchten. Verwenden Sie in der Azure CLI den Parameter `--node-resource-group` des Befehls `az aks create`, um einen benutzerdefinierten Namen für die Ressourcengruppe anzugeben. Wenn Sie eine Azure Resource Manager-Vorlage verwenden, um einen AKS-Cluster bereitzustellen, können Sie die `nodeResourceGroup`-Eigenschaft verwenden, um den Namen der Ressourcengruppe zu definieren.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --node-resource-group myNodeResourceGroup
```

Die sekundäre Ressourcengruppe wird automatisch vom Azure-Ressourcenanbieter in Ihrem eigenen Abonnement erstellt. Sie können einen benutzerdefinierten Namen für die Ressourcengruppe nur dann angeben, wenn der Cluster erstellt wird. 

Denken Sie bei der Arbeit mit der Knotenressourcengruppe daran, dass Folgendes nicht möglich ist:

- Angeben einer vorhandenen Ressourcengruppe als Knotenressourcengruppe
- Angeben eines anderen Abonnements für die Knotenressourcengruppe
- Ändern des Namens der Knotenressourcengruppe, nachdem der Cluster erstellt wurde
- Angeben von Namen für die verwalteten Ressourcen innerhalb der Knotenressourcengruppe
- Ändern oder Löschen von Azure erstellter Tags für verwaltete Ressourcen innerhalb der Knotenressourcengruppe

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie, wie Sie ein [Upgrade der Knotenimages](node-image-upgrade.md) in Ihrem Cluster durchführen.
- Unter [Upgrade eines Azure Kubernetes Service-Clusters (AKS)](upgrade-cluster.md) erfahren Sie, wie Sie Ihren Cluster auf die neueste Version von Kubernetes aktualisieren können.
- Weitere Informationen zu [`containerd` und Kubernetes](https://kubernetes.io/blog/2018/05/24/kubernetes-containerd-integration-goes-ga/)
- In der Liste der [Häufig gestellten Fragen zu AKS](faq.md) finden Sie Antworten auf einige häufig gestellte Fragen zu AKS.
- Weitere Informationen zu kurzlebigen Betriebssystemdatenträgern finden Sie [hier](../virtual-machines/ephemeral-os-disks.md).


<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[aks-add-np-containerd]: windows-container-cli.md#add-a-windows-server-node-pool-with-containerd-preview