---
title: Verbinden mit Azure Kubernetes Service – Azure Database for MySQL
description: Es wird beschrieben, wie Sie Azure Kubernetes Service mit Azure Database for MySQL verbinden.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 07/14/2020
ms.openlocfilehash: 0906c255c3fd77be4de670a980bdc0a2f108c121
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132347102"
---
# <a name="best-practices-for-azure-kubernetes-service-and-azure-database-for-mysql"></a>Bewährte Methoden für Azure Kubernetes Service und Azure Database for MySQL

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

Azure Kubernetes Service (AKS) stellt einen verwalteten Kubernetes-Cluster bereit, den Sie in Azure nutzen können. Unten sind einige Optionen angegeben, die Sie berücksichtigen sollten, wenn Sie AKS und Azure Database for MySQL zusammen zum Erstellen einer Anwendung verwenden.

## <a name="create-database-before-creating-the-aks-cluster"></a>Erstellen einer Datenbank vor dem Erstellen des AKS-Clusters

Für Azure Database for MySQL gibt es zwei Bereitstellungsoptionen:

- Einzelserver
- Flexible Server

Die Option „Einzelserver“ unterstützt eine einzelne Verfügbarkeitszone, die Option „Flexibler Server“ mehrere Verfügbarkeitszonen. AKS unterstützt andererseits auch die Aktivierung einzelner oder mehrerer Verfügbarkeitszonen.  Erstellen Sie zuerst den Datenbankserver, um die Verfügbarkeitszone einzusehen, in der sich der Server befindet, und dann die AKS-Cluster in derselben Verfügbarkeitszone. Dies kann die Leistung der Anwendung verbessern, da Netzwerklatenz reduziert wird.

## <a name="use-accelerated-networking"></a>Verwenden des beschleunigten Netzwerkbetriebs

Verwenden Sie in Ihrem AKS-Cluster zugrunde liegende virtuelle Computer, für die der beschleunigte Netzwerkbetrieb aktiviert ist. Wenn der beschleunigte Netzwerkbetrieb auf einem virtuellen Computer aktiviert ist, ist die Latenz geringer, Jitter reduziert und die CPU-Auslastung des virtuellen Computers niedriger. Informieren Sie sich eingehender über die Funktionsweise des beschleunigten Netzwerkbetriebs, die unterstützten Betriebssystemversionen und die unterstützten VM-Instanzen für [Linux](../virtual-network/create-vm-accelerated-networking-cli.md).

Ab November 2018 unterstützt AKS den beschleunigten Netzwerkbetrieb auf diesen unterstützen VM-Instanzen. Der beschleunigte Netzwerkbetrieb ist für neue AKS-Cluster, in denen diese virtuellen Computer verwendet werden, standardmäßig aktiviert.

Sie können überprüfen, ob Ihr AKS-Cluster über den beschleunigten Netzwerkbetrieb verfügt:

1. Navigieren Sie zum Azure-Portal, und wählen Sie Ihren AKS-Cluster aus.
2. Wählen Sie die Registerkarte „Eigenschaften“.
3. Kopieren Sie den Namen der **Infrastrukturressourcengruppe**.
4. Suchen Sie über die Suchleiste des Portals nach der Infrastrukturressourcengruppe, und öffnen Sie sie.
5. Wählen Sie in dieser Ressourcengruppe einen virtuellen Computer aus.
6. Navigieren Sie zur Registerkarte **Netzwerk** des virtuellen Computers.
7. Überprüfen Sie, ob die Option **Beschleunigter Netzwerkbetrieb** auf „Aktiviert“ festgelegt ist.

Alternativ über die Azure-Befehlszeilenschnittstelle mithilfe der folgenden beiden Befehle:

```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --query "nodeResourceGroup"
```

Die Ausgabe ist die von AKS erstellte generierte Ressourcengruppe, die die Netzwerkschnittstelle enthält. Verwenden Sie den Namen „nodeResourceGroup“ im nächsten Befehl. **EnableAcceleratedNetworking** ist entweder „true“ oder „false“:

```azurecli
az network nic list --resource-group nodeResourceGroup -o table
```

## <a name="use-azure-premium-fileshare"></a>Verwenden von Azure Premium-Dateifreigaben

 [Azure Premium-Dateifreigaben](../storage/files/storage-how-to-create-file-share.md?tabs=azure-portal) bieten persistenten Speicher, der von einem oder mehreren Pods verwendet und dynamisch oder statisch bereitgestellt werden kann. Azure Premium-Dateifreigaben bieten Ihrer Anwendung die beste Leistung, wenn Sie für den Dateispeicher eine große Anzahl von E/A-Vorgängen erwarten. Weitere Informationen finden Sie unter [Aktivieren von Azure Files](../aks/azure-files-dynamic-pv.md).

## <a name="next-steps"></a>Nächste Schritte

- [Erstellen eines Azure Kubernetes Service-Clusters](../aks/kubernetes-walkthrough.md)