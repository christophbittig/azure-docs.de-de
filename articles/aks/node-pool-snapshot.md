---
title: Momentaufnahme von Azure Kubernetes Service-Knotenpools (AKS) (Vorschau)
description: Erfahren Sie, wie Sie Momentaufnahmen von AKS-Clusterknotenpools anfertigen und Cluster und Knotenpools aus einer Momentaufnahme erstellen.
ms.service: container-service
ms.topic: article
ms.date: 09/11/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: b547b65fc3f3086e90da073fb898de3980938147
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131858527"
---
# <a name="azure-kubernetes-service-aks-node-pool-snapshot-preview"></a>Momentaufnahme von Azure Kubernetes Service-Knotenpools (AKS) (Vorschau)

AKS veröffentlicht wöchentlich ein neues Knotenimage, und jeder neue Cluster, Knotenpool oder Upgradecluster erhält immer das neueste Image, wodurch es schwierig sein kann, Ihre Umgebungen konsistent zu halten und wiederholbare Umgebungen bereitzustellen.

Mit Knotenpool-Momentaufnahmen können Sie eine Konfigurationsmomentaufnahme Ihres Knotenpools erstellen und dann neue Knotenpools oder neue Cluster basierend auf dieser Momentaufnahme erstellen, solange diese Konfiguration und Kubernetes-Version unterstützt wird. Weitere Informationen zu den Unterstützungsfenstern finden Sie unter [Unterstützte Kubernetes-Versionen in AKS][supported-versions].

Die Momentaufnahme ist eine Azure-Ressource, die die Konfigurationsinformationen aus dem Quellknotenpool enthält, z. B. die Knotenimageversion, die Kubernetes-Version, den Betriebssystemtyp und die Betriebssystem-SKU. Sie können dann auf diese Momentaufnahmeressource und die entsprechenden Werte ihrer Konfiguration verweisen, um einen neuen Knotenpool oder Cluster basierend darauf zu erstellen.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="before-you-begin"></a>Voraussetzungen

Es wird vorausgesetzt, dass Sie über ein AKS-Cluster verfügen. Wenn Sie einen AKS-Cluster benötigen, erhalten Sie weitere Informationen im AKS-Schnellstart. Verwenden Sie dafür entweder die [Azure CLI][aks-quickstart-cli] oder das [Azure-Portal][aks-quickstart-portal].

### <a name="limitations"></a>Einschränkungen

- Jeder Knotenpool oder Cluster, der aus einer Momentaufnahme erstellt wurde, muss eine VM aus derselben VM-Familie wie die Momentaufnahme verwenden. Sie können z. B. keinen neuen Knotenpool der N-Serie erstellen, der auf einer Momentaufnahme basiert, die aus einem Knotenpool der D-Serie erfasst wurde, da sich die Knotenimages in diesen Fällen strukturell unterscheiden.
- Während der Vorschau müssen Momentaufnahmen in derselben Region wie der Quellknotenpool erstellt und verwendet werden.

### <a name="install-aks-preview-cli-extension"></a>Installieren der CLI-Erweiterung „aks-preview“

Sie benötigen außerdem die Azure CLI-Erweiterung *aks-preview*, Version 0.5.40 oder höher. Installieren Sie die Erweiterung *aks-preview* der Azure-Befehlszeilenschnittstelle mithilfe des Befehls [az extension add][az-extension-add]. Alternativ können Sie verfügbare Updates mithilfe des Befehls [az extension update][az-extension-update] installieren.

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-the-snapshotpreview-preview-feature"></a>Registrieren der Previewfunktion `SnapshotPreview`

Sie müssen darüber hinaus das Featureflag `SnapshotPreview` in Ihrem Abonnement aktivieren, um dieses Feature zu verwenden.

Registrieren Sie das Featureflag `SnapshotPreview` mithilfe des Befehls [az feature register][az-feature-register], wie im folgenden Beispiel gezeigt:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "SnapshotPreview"
```

Es dauert einige Minuten, bis der Status *Registered (Registriert)* angezeigt wird. Überprüfen Sie den Registrierungsstatus mithilfe des Befehls [az feature list][az-feature-list]:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'microsoft.ContainerService/SnapshotPreview')].{Name:name,State:properties.state}"
```

Wenn der Vorgang abgeschlossen ist, können Sie die Registrierung des *Microsoft.ContainerService*-Ressourcenanbieters mit dem Befehl [az provider register][az-provider-register] aktualisieren:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="take-a-node-pool-snapshot"></a>Erstellen einer Momentaufnahme des Knotenpools

Um eine Momentaufnahme aus einem Knotenpool zu erstellen, benötigen Sie zuerst die Ressourcen-ID des Knotenpools, die Sie mit dem folgenden Befehl abrufen können:

```azurecli-interactive
NODEPOOL_ID=$(az aks nodepool show --name nodepool1 --cluster-name myAKSCluster --resource-group myResourceGroup --query id -o tsv)
```

> [!IMPORTANT]
> Ihr AKS-Knotenpool muss nach dem 10. November 2021 erstellt oder aktualisiert worden sein, damit eine Momentaufnahme von ihm erstellt werden kann.

Um nun eine Momentaufnahme aus dem vorherigen Knotenpool zu erstellen, verwenden Sie den CLI-Befehl `az aks snapshot`.

```azurecli-interactive
az aks snapshot create --name MySnapshot --resource-group MyResourceGroup --nodepool-id $NODEPOOL_ID --location eastus
```

## <a name="create-a-node-pool-from-a-snapshot"></a>Erstellen eines Knotenpools aus einer Momentaufnahme

Zunächst benötigen Sie die Ressourcen-ID aus der zuvor erstellten Momentaufnahme, die Sie mit dem folgenden Befehl abrufen können:

```azurecli-interactive
SNAPSHOT_ID=$(az aks snapshot show --name MySnapshot --resource-group myResourceGroup --query id -o tsv)
```

Nun können wir den folgenden Befehl verwenden, um basierend auf dieser Momentaufnahme einen neuen Knotenpool hinzuzufügen.

```azurecli-interactive
az aks nodepool add --name np2 --cluster-name myAKSCluster --resource-group myResourceGroup --snapshot-id $SNAPSHOT_ID
```

## <a name="upgrading-a-node-pool-to-a-snapshot"></a>Aktualisieren eines Knotenpools auf eine Momentaufnahme

Sie können ein Upgrade eines Knotenpools auf eine Momentaufnahmekonfiguration durchführen, solange die Kubernetes-Momentaufnahmeversion und die Knotenimageversion aktueller sind als die Versionen im aktuellen Knotenpool.

Zunächst benötigen Sie die Ressourcen-ID aus der zuvor erstellten Momentaufnahme, die Sie mit dem folgenden Befehl abrufen können:

```azurecli-interactive
SNAPSHOT_ID=$(az aks snapshot show --name MySnapshot --resource-group myResourceGroup --query id -o tsv)
```

Nun können wir diesen Befehl verwenden, um diesen Knotenpool auf diese Momentaufnahmekonfiguration zu aktualisieren.

```azurecli-interactive
az aks nodepool upgrade --name nodepool1 --cluster-name myAKSCluster --resource-group myResourceGroup --snapshot-id $SNAPSHOT_ID
```

> [!NOTE]
> Die Imageversion Ihres Knotenpools ist mit der Version in der Momentaufnahme identisch und bleibt bei jedem Skalierungsvorgang gleich. Wenn dieser Knotenpool jedoch aktualisiert wird oder ein Knotenimageupgrade ohne Angabe einer Momentaufnahme-ID ausgeführt wird, wird das Knotenimage auf die neueste Version aktualisiert.

## <a name="create-a-cluster-from-a-snapshot"></a>Erstellen eines Clusters aus einer Momentaufnahme

Wenn Sie einen Cluster aus einer Momentaufnahme erstellen, wird der ursprüngliche Systempool des Clusters aus der Momentaufnahmekonfiguration erstellt.

Zunächst benötigen Sie die Ressourcen-ID aus der zuvor erstellten Momentaufnahme, die Sie mit dem folgenden Befehl abrufen können:

```azurecli-interactive
SNAPSHOT_ID=$(az aks snapshot show --name MySnapshot --resource-group myResourceGroup --query id -o tsv)
```

Nun können wir diesen Befehl verwenden, um diesen Cluster aus der Momentaufnahmekonfiguration zu erstellen.

```azurecli-interactive
az aks cluster create --name myAKSCluster2 --resource-group myResourceGroup --snapshot-id $SNAPSHOT_ID
```

## <a name="next-steps"></a>Nächste Schritte

- Informationen zu den neuesten Knotenimages finden Sie in den [AKS-Versionshinweisen](https://github.com/Azure/AKS/releases).
- Erfahren Sie, wie Sie die Kubernetes-Version durch [Aktualisieren eines AKS-Clusters][upgrade-cluster] aktualisieren können.
- Erfahren Sie, wie Sie die Knotenimageversion mit einem [Knotenimageupgrade][node-image-upgrade] aktualisieren.
- Weitere Informationen zu mehreren Knotenpools und zum Durchführen von Upgrades für Knotenpools erhalten Sie unter [Erstellen und Verwalten von mehreren Knotenpools][use-multiple-node-pools].

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[supported-versions]: supported-kubernetes-versions.md
[upgrade-cluster]: upgrade-cluster.md
[node-image-upgrade]: node-image-upgrade.md
[github-schedule]: node-upgrade-github-actions.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[max-surge]: upgrade-cluster.md#customize-node-surge-upgrade
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-aks-install-cli]: /cli/azure/aks#az_aks_install_cli
[az-provider-register]: /cli/azure/provider#az_provider_register