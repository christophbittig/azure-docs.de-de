---
title: Aktualisieren einer indirekten modusbasierten Azure Arc-fähigen verwalteten Instanz mit der CLI
description: Aktualisieren einer indirekten modusbasierten Azure Arc-fähigen verwalteten Instanz mit der CLI
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: grrlgeek
ms.author: jeschult
ms.reviewer: mikeray
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: daff60736185f448183d24207099670884402795
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131566487"
---
# <a name="upgrade-an-indirect-mode-azure-arc-enabled-managed-instance-using-the-cli"></a>Aktualisieren einer indirekten modusbasierten Azure Arc-fähigen verwalteten Instanz mit der CLI

## <a name="prerequisites"></a>Voraussetzungen

### <a name="install-tools"></a>Installieren von Tools

Bevor Sie mit den Aufgaben in diesem Artikel fortfahren können, müssen Sie Folgendes installieren:

- Die [Azure CLI (az)](/cli/azure/install-azure-cli)
- Die [`arcdata`-Erweiterung für die Azure CLI](install-arcdata-extension.md)

## <a name="limitations"></a>Einschränkungen

Der Azure Arc-Datencontroller muss auf die neue Version aktualisiert werden, bevor die verwaltete Instanz aktualisiert werden kann.

Derzeit kann nur eine verwaltete Instanz gleichzeitig aktualisiert werden.

## <a name="upgrade-the-managed-instance"></a>Aktualisieren der verwalteten Instanz

Zunächst kann ein Testlauf durchgeführt werden. Dies überprüft das Versionsschema und listet auf, welche Instanzen aktualisiert werden.

````cli
az sql mi-arc upgrade --name <instance name> --k8s-namespace <namespace> --dry-run --use-k8s
````

Ausgabe:

```output
Preparing to upgrade sql sqlmi-1 in namespace arc to data controller version.
****Dry Run****1 instance(s) would be upgraded by this commandsqlmi-1 would be upgraded to 20211024.1.
```

### <a name="general-purpose"></a>Universell

Führen Sie zum Upgrade der verwalteten Instanz den folgenden Befehl aus:

````cli
az sql mi-arc upgrade --name <instance name> --desired-version <version> --k8s-namespace <namespace> --use-k8s
````

Beispiel:

````cli
az sql mi-arc upgrade --name instance1 --target v1.0.0.20211028 --k8s-namespace arc1 --use-k8s
````

## <a name="monitor"></a>Überwachen

Sie können den Fortschritt des Upgrades mit kubectl oder der CLI überwachen.

### <a name="kubectl"></a>kubectl

```console
kubectl describe sqlmi --namespace <namespace>
```

### <a name="cli"></a>CLI

```cli
az sql mi-arc show --name <instance name> --k8s-namespace <namespace> --use-k8s
```

### <a name="output"></a>Output

Die Ausgabe für den Befehl zeigt die Ressourceninformationen an. Die Upgradeinformationen werden unter Status angezeigt.

Während des Upgrades zeigt ```State``` ```Updating``` an und ```Running Version``` ist die aktuelle Version:

```output
Status:
  Log Search Dashboard:  https://30.88.222.48:5601/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:sqlmi-1'))
  Metrics Dashboard:     https://30.88.221.32:3000/d/40q72HnGk/sql-managed-instance-metrics?var-hostname=sqlmi-1-0
  Observed Generation:   2
  Primary Endpoint:      30.76.129.38,1433
  Ready Replicas:        1/1
  Running Version:       v1.0.0_2021-07-30
  State:                 Updating
```

Nach Abschluss des Upgrades zeigt ```State``` ```Ready``` an und ```Running Version``` ist die neue Version:

```output
Status:
  Log Search Dashboard:  https://30.88.222.48:5601/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:sqlmi-1'))
  Metrics Dashboard:     https://30.88.221.32:3000/d/40q72HnGk/sql-managed-instance-metrics?var-hostname=sqlmi-1-0
  Observed Generation:   2
  Primary Endpoint:      30.76.129.38,1433
  Ready Replicas:        1/1
  Running Version:       20211024.1
  State:                 Ready
```

## <a name="troubleshoot-upgrade-problems"></a>Behandeln von Upgradeproblemen

Wenn Probleme beim Upgrade auftreten, finden Sie weitere Informationen im [Handbuch zur Problembehandlung](troubleshoot-guide.md).
