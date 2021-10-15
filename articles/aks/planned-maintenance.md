---
title: Verwenden der geplanten Wartung für Ihren AKS-Cluster (Azure Kubernetes Service) (Vorschau)
titleSuffix: Azure Kubernetes Service
description: Erfahren Sie, wie Sie die geplante Wartung in Azure Kubernetes Service (AKS) verwenden.
services: container-service
ms.topic: article
ms.date: 03/03/2021
ms.author: qpetraroia
author: qpetraroia
ms.openlocfilehash: 24c81290bce53e1399379103ac3d17ea735222a4
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129235286"
---
# <a name="use-planned-maintenance-to-schedule-maintenance-windows-for-your-azure-kubernetes-service-aks-cluster-preview"></a>Verwenden der geplanten Wartung, um Wartungsfenster für AKS-Cluster (Azure Kubernetes Service) zu planen (Vorschau)

Für den AKS-Cluster wird automatisch eine regelmäßige Wartung durchgeführt. Standardmäßig kann diese Aufgabe jederzeit ausgeführt werden. Mit der geplanten Wartung können Sie wöchentliche Wartungsfenster planen, die sowohl Ihre Steuerungsebene als auch Ihre kube-system Pods auf einer VMSS-Instanz aktualisieren und die Auswirkungen auf die Arbeitslast minimieren. Nach der Planung erfolgt die gesamte Wartung während des festgelegten Fensters. Sie können wöchentliche Fenster in Ihrem Cluster planen, indem Sie einen Tages- oder Uhrzeitbereich an einem bestimmten Tag angeben. Wartungsfenster werden mithilfe der Azure-Befehlszeilenschnittstelle konfiguriert.

## <a name="before-you-begin"></a>Voraussetzungen

Es wird vorausgesetzt, dass Sie über ein AKS-Cluster verfügen. Wenn Sie einen AKS-Cluster benötigen, erhalten Sie weitere Informationen im AKS-Schnellstart. Verwenden Sie dafür entweder die [Azure CLI][aks-quickstart-cli] oder das [Azure-Portal][aks-quickstart-portal].

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

### <a name="limitations"></a>Einschränkungen

Bei Verwendung der geplanten Wartung gelten folgende Einschränkungen:

- AKS behält sich vor, diese Fenster für ungeplante/reaktive Wartungsvorgänge zu unterbrechen, falls diese dringend oder kritisch sind.
- Das Ausführen von Wartungsvorgängen wird derzeit nur als *bestmögliche Anstrengung* angesehen, und es kann nicht garantiert werden, dass sie in einem bestimmten Fenster ausgeführt werden.
- Updates können nicht länger als sieben Tage blockiert werden.

### <a name="install-aks-preview-cli-extension"></a>Installieren der CLI-Erweiterung „aks-preview“

Sie benötigen außerdem mindestens Version 0.5.4 der Erweiterung *aks-preview* für die Azure-Befehlszeilenschnittstelle. Installieren Sie die Erweiterung *aks-preview* der Azure-Befehlszeilenschnittstelle mithilfe des Befehls [az extension add][az-extension-add]. Alternativ können Sie verfügbare Updates mithilfe des Befehls [az extension update][az-extension-update] installieren.

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="allow-maintenance-on-every-monday-at-100am-to-200am"></a>Zulassen von Wartungsvorgängen an jedem Montag zwischen 1:00 Uhr und 2:00 Uhr

Zum Hinzufügen eines Wartungsfensters können Sie den Befehl `az aks maintenanceconfiguration add` verwenden.

> [!IMPORTANT]
> Als Wert für `--name` muss `default` festgelegt werden. Bei einem anderen Namen wird das Wartungsfenster nicht ausgeführt.
>
> Geplante Wartungsfenster werden in koordinierter Weltzeit (UTC) angegeben.

```azurecli-interactive
az aks maintenanceconfiguration add -g MyResourceGroup --cluster-name myAKSCluster --name default --weekday Monday  --start-hour 1
```

Die folgende Beispielausgabe zeigt das Wartungsfenster von 1:00 Uhr bis 2:00 Uhr an jedem Montag.

```json
{- Finished ..
  "id": "/subscriptions/<subscriptionID>/resourcegroups/MyResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/maintenanceConfigurations/default",
  "name": "default",
  "notAllowedTime": null,
  "resourceGroup": "MyResourceGroup",
  "systemData": null,
  "timeInWeek": [
    {
      "day": "Monday",
      "hourSlots": [
        1
      ]
    }
  ],
  "type": null
}
```

Um die Wartung jederzeit im Lauf eines Tages zuzulassen, lassen Sie den Parameter *start-hour* weg. Mit dem folgenden Befehl wird beispielsweise das Wartungsfenster jeden Montag auf den ganzen Tag festgelegt:

```azurecli-interactive
az aks maintenanceconfiguration add -g MyResourceGroup --cluster-name myAKSCluster --name default --weekday Monday
```

## <a name="add-a-maintenance-configuration-with-a-json-file"></a>Hinzufügen einer Wartungskonfiguration mit einer JSON-Datei

Sie können auch mithilfe einer JSON-Datei anstelle von Parametern ein Wartungsfenster erstellen. Erstellen Sie eine Datei `test.json` mit folgendem Inhalt:

```json
  {
        "timeInWeek": [
          {
            "day": "Tuesday",
            "hour_slots": [
              1,
              2
            ]
          },
          {
            "day": "Wednesday",
            "hour_slots": [
              1,
              6
            ]
          }
        ],
        "notAllowedTime": [
          {
            "start": "2021-05-26T03:00:00Z",
            "end": "2021-05-30T12:00:00Z"
          }
        ]
}
```

In der obigen JSON-Datei sind Wartungsfenster jeden Dienstag von 1:00 Uhr bis 3:00 Uhr und jeden Mittwoch von 1:00 Uhr bis 2:00 Uhr und von 6:00 Uhr bis 7:00 Uhr angegeben. Außerdem gibt es eine Ausnahme von *2021-05-26T03:00:00Z* bis *2021-05-30T12:00:00Z*, während der auch dann keine Wartung zulässig ist, wenn sie sich mit einem Wartungsfenster überschneidet. Mit dem folgenden Befehl werden die Wartungsfenster aus `test.json` hinzugefügt.

```azurecli-interactive
az aks maintenanceconfiguration add -g MyResourceGroup --cluster-name myAKSCluster --name default --config-file ./test.json
```

## <a name="update-an-existing-maintenance-window"></a>Aktualisieren eines vorhandenen Wartungsfensters

Um eine vorhandene Wartungskonfiguration zu aktualisieren, verwenden Sie den Befehl `az aks maintenanceconfiguration update`.

```azurecli-interactive
az aks maintenanceconfiguration update -g MyResourceGroup --cluster-name myAKSCluster --name default --weekday Monday  --start-hour 1
```

## <a name="list-all-maintenance-windows-in-an-existing-cluster"></a>Auflisten aller Wartungsfenster in einem vorhandenen Cluster

Verwenden Sie den Befehl `az aks maintenanceconfiguration list`, um alle derzeit konfigurierten Wartungsfenster in Ihrem AKS-Cluster anzuzeigen.

```azurecli-interactive
az aks maintenanceconfiguration list -g MyResourceGroup --cluster-name myAKSCluster
```

In der folgenden Ausgabe können Sie sehen, dass für „myAKSCluster“ zwei Wartungsfenster konfiguriert sind: ein Fenster am Montag um 1:00 Uhr und ein weiteres Fenster am Freitag um 4:00 Uhr.

```json
[
  {
    "id": "/subscriptions/<subscriptionID>/resourcegroups/MyResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/maintenanceConfigurations/default",
    "name": "default",
    "notAllowedTime": null,
    "resourceGroup": "MyResourceGroup",
    "systemData": null,
    "timeInWeek": [
      {
        "day": "Monday",
        "hourSlots": [
          1
        ]
      }
    ],
    "type": null
  },
  {
    "id": "/subscriptions/<subscriptionID>/resourcegroups/MyResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/maintenanceConfigurations/testConfiguration",
    "name": "testConfiguration",
    "notAllowedTime": null,
    "resourceGroup": "MyResourceGroup",
    "systemData": null,
    "timeInWeek": [
      {
        "day": "Friday",
        "hourSlots": [
          4
        ]
      }
    ],
    "type": null
  }
]
```

## <a name="show-a-specific-maintenance-configuration-window-in-an-aks-cluster"></a>Anzeigen eines bestimmten Wartungsfensters in einem AKS-Cluster

Um ein bestimmtes konfiguriertes Wartungsfenster in Ihrem AKS-Cluster anzuzeigen, verwenden Sie den Befehl `az aks maintenanceconfiguration show`.

```azurecli-interactive
az aks maintenanceconfiguration show -g MyResourceGroup --cluster-name myAKSCluster --name default
```

Die folgende Beispielausgabe zeigt das Wartungsfenster für *default*:

```json
{
  "id": "/subscriptions/<subscriptionID>/resourcegroups/MyResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/maintenanceConfigurations/default",
  "name": "default",
  "notAllowedTime": null,
  "resourceGroup": "MyResourceGroup",
  "systemData": null,
  "timeInWeek": [
    {
      "day": "Monday",
      "hourSlots": [
        1
      ]
    }
  ],
  "type": null
}
```

## <a name="delete-a-certain-maintenance-configuration-window-in-an-existing-aks-cluster"></a>Löschen eines bestimmten Wartungsfensters in einem vorhandenen AKS-Cluster

Um ein bestimmtes konfiguriertes Wartungsfenster in Ihrem AKS-Cluster zu löschen, verwenden Sie den Befehl `az aks maintenanceconfiguration delete`.

```azurecli-interactive
az aks maintenanceconfiguration delete -g MyResourceGroup --cluster-name myAKSCluster --name default
```

## <a name="using-planned-maintenance-with-cluster-auto-upgrade"></a>Verwenden der geplanten Wartung mit automatischem Clusterupgrade

Die geplante Wartung erkennt, ob Sie automatisches Clusterupgrade verwenden, und plant Ihre Upgrades während Ihres Wartungsfensters automatisch. Weitere Informationen zum automatischen Clusterupgrade finden Sie unter [Aktualisieren eines Azure Kubernetes Service-Clusters (AKS)][aks-upgrade].

## <a name="next-steps"></a>Nächste Schritte

- Die ersten Schritte beim Aktualisieren Ihres AKS-Clusters sind unter [Aktualisieren eines AKS-Clusters][aks-upgrade] beschrieben.


<!-- LINKS - Internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-aks-install-cli]: /cli/azure/aks#az_aks_install_cli
[az-provider-register]: /cli/azure/provider#az_provider_register
[aks-upgrade]: upgrade-cluster.md
