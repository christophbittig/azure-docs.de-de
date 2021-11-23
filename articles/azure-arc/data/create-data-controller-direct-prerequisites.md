---
title: Voraussetzungen | Direkter Verbindungsmodus
description: Hier werden die Voraussetzungen zum Bereitstellen des Datencontrollers im direkten Verbindungsmodus erläutert.
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 11/03/2021
ms.topic: overview
ms.openlocfilehash: cd1c12eaf4c4d5df575c7774386da007c9124712
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132549829"
---
# <a name="prerequisites-to-deploy-the-data-controller-in-direct-connectivity-mode"></a>Hier werden die Voraussetzungen zum Bereitstellen des Datencontrollers im direkten Konnektivitätsmodus erläutert

In diesem Artikel wird beschrieben, wie Sie die Bereitstellung eines Datencontrollers für Azure Arc-fähige Datendienste im direkten Verbindungsmodus vorbereiten. Bevor Sie einen Azure Arc-Datencontroller bereitstellen, sollten Sie sich mit den Konzepten vertraut machen, die unter [Planen der Bereitstellung von Azure Arc-fähigen Datendiensten](plan-azure-arc-data-services.md) beschrieben werden.

Zu den wichtigsten Voraussetzungen für die Erstellung von Azure Arc Data Controllern im **direkten** Konnektivitätsmodus gehören:

1. Das Verbinden eines Kubernetes-Clusters mit Azure unter der Verwendung von Azure Arc-fähigem Kubernetes
2. Das Erstellen von Azure Arc-fähigen Datendienstdatencontrollern. Dieser Schritt umfasst das Erstellen von
    - einer Azure Arc-Datendiensterweiterung
    - Benutzerdefinierter Speicherort
    - einem Azure Arc-Datencontroller
3. Wenn Protokolle automatisch nach Azure Log Analytics hochgeladen werden sollen, werden die Log Analytics-Arbeitsbereichs-ID und der Schlüssel für den gemeinsamen Zugriff als Teil der Bereitstellung benötigt.

## <a name="1-connect-kubernetes-cluster-to-azure-using-azure-arc-enabled-kubernetes"></a>1. Das Verbinden eines Kubernetes-Clusters mit Azure unter der Verwendung von Azure Arc-fähigem Kubernetes

Um Ihren Kubernetes-Cluster mit Azure zu verbinden, verwenden Sie die Azure CLI `az` mit den folgenden Erweiterungen oder Helm.

### <a name="install-tools"></a>Installieren von Tools

- Helm-Version 3.3 oder höher ([Installieren](https://helm.sh/docs/intro/install/))
- Installieren oder Aktualisieren auf die neueste Version der Azure CLI ([Herunterladen](https://aka.ms/installazurecliwindows))

### <a name="add-extensions-for-azure-cli"></a>Hinzufügen von Erweiterungen für die Azure CLI

Installieren Sie die neuesten Versionen der folgenden Azure CLI-Erweiterungen:
- `k8s-extension`
- `connectedk8s`
- `k8s-configuration`
- `customlocation`

Führen Sie den folgenden Befehl aus, um die Azure CLI-Erweiterungen zu installieren:

```azurecli
az extension add --name k8s-extension
az extension add --name connectedk8s
az extension add --name k8s-configuration
az extension add --name customlocation
```

Wenn Sie zuvor die `k8s-extension`, `connectedk8s`, `k8s-configuration`, `customlocation` Erweiterungen installiert haben, können Sie über den folgenden Befehl auf die neueste Version aktualisieren:

```azurecli
az extension update --name k8s-extension
az extension update --name connectedk8s
az extension update --name k8s-configuration
az extension update --name customlocation
```

### <a name="connect-your-cluster-to-azure"></a>Verbinden Sie Ihren Cluster mit Azure

Führen Sie zum Abschließen dieser Aufgabe die Schritte unter [Schnellstart: Herstellen einer Verbindung eines vorhandenen Kubernetes-Clusters mit Azure Arc](../kubernetes/quickstart-connect-cluster.md) aus.

## <a name="2-optionally-keep-the-log-analytics-workspace-id-and-shared-access-key-ready"></a>2. Halten Sie optional die Log Analytics-Arbeitsbereichs-ID und den gemeinsam genutzten Zugriffsschlüssel bereit.

Wenn Sie einen Azure Arc-fähigen Datencontroller bereitstellen, können Sie das automatische Hochladen von Metriken und Protokollen während des Setups aktivieren. Beim Hochladen von Metriken wird die systemseitig zugewiesene verwaltete Identität verwendet. Das Hochladen von Protokollen erfordert jedoch eine Arbeitsbereichs-ID und den Zugriffsschlüssel für den Arbeitsbereich. 

Sie können das automatische Hochladen von Metriken und Protokollen außerdem aktivieren oder deaktivieren, nachdem Sie den Datencontroller bereitgestellt haben. 

## <a name="3-create-azure-arc-data-services"></a>3. Das Erstellen eines Azure Arc-Datendienstes

Wenn diese Voraussetzungen erfüllt sind, können Sie die Schritte unter [Bereitstellen eines Azure Arc-Datencontrollers | Direkter Verbindungsmodus](create-data-controller-direct-azure-portal.md) ausführen.
