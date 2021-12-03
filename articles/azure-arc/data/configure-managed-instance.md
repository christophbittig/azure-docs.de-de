---
title: Konfigurieren von SQL Managed Instance mit Azure Arc-Unterstützung
description: Konfigurieren von SQL Managed Instance mit Azure Arc-Unterstützung
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: d4dc8843804d48fb98fef7cd336e6b56f54f49ae
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131554353"
---
# <a name="configure-azure-arc-enabled-sql-managed-instance"></a>Konfigurieren von SQL Managed Instance mit Azure Arc-Unterstützung

In diesem Artikel wird erläutert, wie SQL Managed Instance mit Azure Arc-Unterstützung konfiguriert wird.


## <a name="configure-resources-such-as-cores-memory"></a>Konfigurieren von Ressourcen, etwa Kerne und Arbeitsspeicher


### <a name="configure-using-cli"></a>Konfigurieren mithilfe der Befehlszeilenschnittstelle

Sie können die Konfiguration von SQL Managed Instance mit Azure Arc-Unterstützung mit der Befehlszeilenschnittstelle bearbeiten. Führen Sie den folgenden Befehl aus, um die Konfigurationsoptionen anzuzeigen. 

```azurecli
az sql mi-arc edit --help
```

Mithilfe des folgenden Befehls können Sie den verfügbaren Arbeitsspeicher und die verfügbaren Kerne für eine verwaltete SQL-Instanz mit Azure Arc-Unterstützung aktualisieren:

```azurecli
az sql mi-arc edit --cores-limit 4 --cores-request 2 --memory-limit 4Gi --memory-request 2Gi -n <NAME_OF_SQL_MI> --k8s-namespace <namespace> --use-k8s
```

Im folgenden Beispiel werden die CPU-Kern- und Arbeitsspeicheranforderungen und Grenzwerte festgelegt.

```azurecli
az sql mi-arc edit --cores-limit 4 --cores-request 2 --memory-limit 4Gi --memory-request 2Gi -n sqlinstance1 --k8s-namespace arc --use-k8s
```

Zum Anzeigen der Änderungen, die an der verwalteten SQL-Instanz mit Azure Arc-Unterstützung vorgenommen wurden, können Sie die folgenden Befehle verwenden, um die YAML-Konfigurationsdatei anzuzeigen:

```azurecli
az sql mi-arc show -n <NAME_OF_SQL_MI> --k8s-namespace <namespace> --use-k8s
```

## <a name="configure-server-options"></a>Konfigurieren von Serveroptionen

Nach der Erstellung können Sie Serverkonfigurationseinstellungen für SQL Managed Instance mit Azure Arc-Unterstützung konfigurieren. In diesem Artikel wird beschrieben, wie Einstellungen wie das Aktivieren oder Deaktivieren des MSSQL-Agents oder das Aktivieren bestimmter Ablaufverfolgungsflags für Problembehandlungsszenarien festgelegt werden können.


### <a name="enable-sql-server-agent"></a>Aktivieren des SQL Server-Agents

Der SQL Server-Agent ist standardmäßig deaktiviert. Er kann durch Ausführen des folgenden Befehls aktiviert werden:

```azurecli
az sql mi-arc edit -n <NAME_OF_SQL_MI> --k8s-namespace <namespace> --use-k8s --agent-enabled true
```
Beispiel:
```azurecli
az sql mi-arc edit -n sqlinstance1 --k8s-namespace arc --use-k8s --agent-enabled true
```

### <a name="enable-trace-flags"></a>Aktivieren von Ablaufverfolgungsflags

Ablaufverfolgungsflags können wie folgt aktiviert werden:
```azurecli
az sql mi-arc edit -n <NAME_OF_SQL_MI> --k8s-namespace <namespace> --use-k8s --trace-flags "3614,1234" 
```

