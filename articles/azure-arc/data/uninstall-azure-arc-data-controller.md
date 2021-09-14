---
title: Löschen des Azure Arc-Datencontrollers
description: Löschen des Azure Arc-Datencontrollers
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: 26784222d49e6f48ed324ce345dcb1f2ba7d4cf1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122339772"
---
# <a name="delete-azure-arc-data-controller"></a>Löschen des Azure Arc-Datencontrollers

Im folgenden Artikel wird beschrieben, wie Sie einen Azure Arc-Datencontroller löschen.

Bevor Sie fortfahren, sollten Sie sicherstellen, dass alle Datendienste, die auf dem Datencontroller erstellt wurden, wie folgt entfernt wurden:

## <a name="list--delete-existing-data-services"></a>Auflisten und Löschen vorhandener Datendienste

Führen Sie den folgenden Befehl aus, um zu überprüfen, ob SQL Managed Instance-Instanzen erstellt wurden:

```azurecli
az sql mi-arc list --k8s-namespace <namespace> --use-k8s
```

Führen Sie für jede verwaltete SQL-Instanz aus der obigen Liste den Löschbefehl wie folgt aus:

```azurecli
az sql mi-arc delete -n <name> --k8s-namespace <namespace> --use-k8s
# for example: az sql mi-arc delete -n sqlinstance1 --k8s-namespace <namespace> --use-k8s
```

Führen Sie ebenso für die Suche nach PostgreSQL Hyperscale-Instanzen Folgendes aus:

```azurecli
az postgres arc-server list --k8s-namespace <namespace> --use-k8s
```

Führen Sie zudem für jede PostgreSQL Hyperscale-Instanz den Löschbefehl wie folgt aus:

```azurecli
az postgres arc-server delete -n <name> --k8s-namespace <namespace> --use-k8s
# for example: az postgres arc-server delete -n pg1 --k8s-namespace <namespace> --use-k8s
```

## <a name="delete-controller"></a>Löschen des Controllers

Nachdem alle SQL Managed Instance-Instanzen und PostgreSQL Hyperscale-Instanzen entfernt wurden, kann der Datencontroller wie folgt gelöscht werden:

```azurecli
az arcdata dc delete -n <name> -ns <namespace>
# for example: az arcdata dc delete -ns arc -n arcdc
```

### <a name="remove-sccs-red-hat-openshift-only"></a>Entfernen Sie die SCCs (nur in Red Hat OpenShift).

```console
oc adm policy remove-scc-from-user privileged -z default -n arc
oc adm policy remove-scc-from-user anyuid     -z default -n arc
```

### <a name="delete-cluster-level-objects"></a>Löschen Sie Objekte auf Clusterebene.

Zusätzlich zu den im Namespace gültigen Objekten führen Sie, wenn Sie auch die Objekte auf Clusterebene löschen möchten, z. B. die CRDs `clusterroles` und `clusterrolebindings`, die folgenden Befehle aus:

```console
# Cleanup azure arc data service artifacts
#Delete CRDs
kubectl delete crd datacontrollers.arcdata.microsoft.com 
kubectl delete crd sqlmanagedinstances.sql.arcdata.microsoft.com 
kubectl delete crd postgresql-11s.arcdata.microsoft.com 
kubectl delete crd postgresql-12s.arcdata.microsoft.com
#Delete clusterrole
kubectl delete clusterrole <namespace>:cr-arc-metricsdc-reader
#For example: kubectl delete clusterrole arc:cr-arc-metricsdc-reader
#Delete rolebinding
kubectl delete clusterrolebinding <namespace>:crb-arc-metricsdc-reader
#For example: kubectl delete clusterrolebinding arc:crb-arc-metricsdc-reader
```

### <a name="optionally-delete-the-azure-arc-data-controller-namespace"></a>Löschen Sie optional den Namespace des Azure Arc Datacontrollers.


```console
kubectl delete ns <nameSpecifiedDuringCreation>
# for example: kubectl delete ns arc
```

## <a name="next-steps"></a>Nächste Schritte

[Was sind Azure Arc-fähige Datendienste (Vorschauversion)?](overview.md)
