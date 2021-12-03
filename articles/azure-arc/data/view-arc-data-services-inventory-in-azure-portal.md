---
title: Anzeigen des Bestands Ihrer Instanzen im Azure-Portal
description: Anzeigen des Bestands Ihrer Instanzen im Azure-Portal
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: 5a5f13c74799e172d7ce9aefb170626cf61eb98e
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131558780"
---
# <a name="inventory-of-arc-enabled-data-services"></a>Inventar der Datendienste mit Arc-Unterstützung


## <a name="view-resources-in-azure-portal"></a>Anzeigen von Ressourcen im Azure-Portal

Nachdem Sie Ihre [Metriken, Protokolle](upload-metrics-and-logs-to-azure-monitor.md) oder [Nutzung](view-billing-data-in-azure.md)sdaten hochgeladen haben, können Sie Ihre SQL Managed Instances mit Azure Arc-Unterstützung oder Postgres Hyperscale-Servergruppen mit Azure Arc-Unterstützung im Azure-Portal anzeigen. Um Ihre Ressourcengruppe im [Portal](https://portal.azure.com) anzuzeigen, gehen Sie wie folgt vor:

1. Navigieren Sie zu **Alle Dienste**.
1. Suchen Sie nach Ihrem Datenbankinstanztyp.
1. Fügen Sie den Typ Ihren Favoriten hinzu.
1. Wählen Sie im linken Menü den Instanztyp aus.
1. Zeigen Sie Ihre Instanzen in derselben Ansicht wie Ihre anderen Azure SQL- oder Azure PostgreSQL Hyperscale-Ressourcen an (verwenden Sie Filter, um eine präzisere Ansicht zu erhalten).

## <a name="view-resources-in-your-kubernetes-cluster"></a>Anzeigen von Ressourcen in Ihrem Kubernetes-Cluster

Wenn der Azure Arc-Datencontroller im **indirekten** Konnektivitätsmodus bereitgestellt wird, können Sie den folgenden Befehl ausführen, um eine Liste aller SQL Managed Instances mit Azure Arc-Unterstützung abzurufen:
```
az sql mi-arc list --k8s-namespace <namespace> --use-k8s
#Example
az sql mi-arc list --k8s-namespace arc --use-k8s
```

Wenn der Azure Arc-Datencontroller im **direkten** Konnektivitätsmodus bereitgestellt wird, können Sie den folgenden Befehl ausführen, um eine Liste aller SQL Managed Instances mit Azure Arc-Unterstützung abzurufen:
```
az sql mi-arc list --resource-group <resourcegroup>
#Example
az sql mi-arc list --resource-group myResourceGroup
```
