---
title: Aktivieren von CSI-Treibern (Container Storage Interface) in Azure Kubernetes Service (AKS)
description: Hier erfahren Sie, wie Sie die CSI-Treiber (Container Storage Interface) für Azure-Datenträger und Azure Files in einem AKS-Cluster (Azure Kubernetes Service) aktivieren.
services: container-service
ms.topic: article
ms.date: 08/31/2021
author: palma21
ms.openlocfilehash: 7fe0aa073cf1ecb959bc7999ba59a2486c65b7e1
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2021
ms.locfileid: "123429008"
---
# <a name="enable-container-storage-interface-csi-drivers-for-azure-disks-and-azure-files-on-azure-kubernetes-service-aks"></a>Aktivieren von CSI-Treibern (Container Storage Interface) für Azure-Datenträger und Azure Files in Azure Kubernetes Service (AKS)

Container Storage Interface (CSI) ist ein Standard für die Bereitstellung beliebiger Block- und Dateispeichersysteme für containerisierte Workloads in Kubernetes. Durch die Einführung und Verwendung von CSI kann Azure Kubernetes Service (AKS) Plug-Ins schreiben, bereitstellen und durchlaufen, um neue Speichersysteme in Kubernetes verfügbar zu machen oder vorhandene Speichersysteme in Kubernetes zu verbessern, ohne den Kerncode von Kubernetes zu ändern und die Releasezyklen abwarten zu müssen.

Die Unterstützung des CSI-Speichertreibers in AKS ermöglicht die native Verwendung von Folgendem:
- [*Azure-Datenträger:*](azure-disk-csi.md) Diese können zum Erstellen einer Kubernetes-Ressource vom Typ *DataDisk* verwendet werden. Datenträger können auf Hochleistungs-SSDs basierenden Speicher vom Typ „Azure Storage Premium“ oder auf regulären Festplatten oder Standard-SSDs basierenden Speicher vom Typ „Azure Storage Standard“ nutzen. Für die meisten Produktions- und Entwicklungsworkloads wird die Verwendung von Storage Premium empfohlen. Azure-Datenträger werden als *ReadWriteOnce* eingebunden und sind somit nur für einen einzelnen Pod verfügbar. Verwenden Sie für die Speichervolumes, auf die mehrere Pods gleichzeitig zugreifen können, Azure Files.
- [*Azure Files:*](azure-files-csi.md) Kann verwendet werden, um eine auf einem Azure Storage-Konto basierende SMB 3.0-Freigabe in Pods einzubinden. Mit Azure Files können Daten über mehrere Knoten und Pods hinweg gemeinsam genutzt werden. Von Azure Files kann auf regulären Festplatten basierender Speicher vom Typ „Azure Storage Standard“ oder auf Hochleistungs-SSDs basierender Speicher vom Typ „Azure Storage Premium“ genutzt werden.

> [!IMPORTANT]
> Ab der Kubernetes-Version 1.21 werden von Kubernetes standardmäßig nur noch CSI-Treiber verwendet. Diese Treiber sind die Zukunft der Speicherunterstützung in Kubernetes.
> 
> Entfernen Sie vor dem Upgrade auf AKS 1.21 manuell installierte Open-Source-Versionen der Azure Disk- und Azure File CSI-Treiber.
> 
> *Strukturinterne Treiber* bezieht sich auf die aktuellen Speichertreiber, die Teil des Kubernetes-Kerncodes sind (im Gegensatz zu den neuen CSI-Treibern, bei denen es sich um Plug-Ins handelt).

## <a name="limitations"></a>Einschränkungen

- Dieses Feature kann nur bei der Clustererstellung festgelegt werden.
- CSI-Treiber werden ab der Kubernetes-Nebenversion 1.17 unterstützt.
- Die Standardspeicherklasse wird die Speicherklasse `managed-csi`.

## <a name="create-a-new-cluster-that-can-use-csi-storage-drivers"></a>Erstellen eines neuen Clusters, der CSI-Speichertreiber verwenden kann

Erstellen Sie mithilfe der folgenden CLI-Befehle einen neuen Cluster, der CSI-Speichertreiber für Azure-Datenträger und Azure Files verwenden kann. Verwenden Sie das Flag `--aks-custom-headers`, um das Feature `EnableAzureDiskFileCSIDriver` festzulegen.

Erstellen Sie eine Azure-Ressourcengruppe:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location canadacentral
```

Erstellen Sie den AKS-Cluster mit CSI-Speichertreiberunterstützung:

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g MyResourceGroup -n MyManagedCluster --network-plugin azure  --aks-custom-headers EnableAzureDiskFileCSIDriver=true
```

Wenn Sie Cluster mit strukturinternen Speichertreibern anstelle von CSI-Speichertreibern erstellen möchten, lassen Sie den benutzerdefinierten Parameter `--aks-custom-headers` weg.


Führen Sie Folgendes aus, um zu überprüfen, wie viele auf Azure-Datenträgern basierende Volumes Sie anfügen können:

```console
$ kubectl get nodes
aks-nodepool1-25371499-vmss000000
aks-nodepool1-25371499-vmss000001
aks-nodepool1-25371499-vmss000002

$ echo $(kubectl get CSINode <NODE NAME> -o jsonpath="{.spec.drivers[1].allocatable.count}")
8
```

## <a name="next-steps"></a>Nächste Schritte

- Informationen zur Verwendung der CSI-Treiber für Azure-Datenträger finden Sie unter [Verwenden der CSI-Treiber (Container Storage Interface) für Azure-Datenträger in Azure Kubernetes Service (AKS) (Vorschau)](azure-disk-csi.md).
- Informationen zur Verwendung der CSI-Treiber für Azure Files finden Sie unter [Verwenden der CSI-Treiber (Container Storage Interface) für Azure Files in Azure Kubernetes Service (AKS) (Vorschau)](azure-files-csi.md).
- Weitere Informationen zu bewährten Methoden bei der Speicherung finden Sie unter [Best Practices für Speicherung und Sicherungen in Azure Kubernetes Service (AKS)][operator-best-practices-storage].

<!-- LINKS - external -->
[access-modes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-storage-classes]: https://kubernetes.io/docs/concepts/storage/storage-classes/
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/persistent-volumes/
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/

<!-- LINKS - internal -->
[azure-disk-volume]: azure-disk-volume.md
[azure-files-pvc]: azure-files-dynamic-pv.md
[premium-storage]: ../virtual-machines/disks-types.md
[az-disk-list]: /cli/azure/disk#az_disk_list
[az-snapshot-create]: /cli/azure/snapshot#az_snapshot_create
[az-disk-create]: /cli/azure/disk#az_disk_create
[az-disk-show]: /cli/azure/disk#az_disk_show
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-storage]: operator-best-practices-storage.md
[concepts-storage]: concepts-storage.md
[storage-class-concepts]: concepts-storage.md#storage-classes
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
