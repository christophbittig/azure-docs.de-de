---
title: Konfigurieren von SQL Managed Instance mit Azure Arc-Unterstützung
description: Konfigurieren von SQL Managed Instance mit Azure Arc-Unterstützung
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: e84d5be7252f81c4e80d6070ada2151fcc3960f1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122346952"
---
# <a name="configure-azure-arc-enabled-sql-managed-instance"></a>Konfigurieren von SQL Managed Instance mit Azure Arc-Unterstützung

In diesem Artikel wird erläutert, wie SQL Managed Instance mit Azure Arc-Unterstützung konfiguriert wird.


## <a name="configure-resources"></a>Konfigurieren der Ressourcen

### <a name="configure-using-cli"></a>Konfigurieren mithilfe der Befehlszeilenschnittstelle

Sie können die Konfiguration von SQL Managed Instance mit Azure Arc-Unterstützung mit der Befehlszeilenschnittstelle bearbeiten. Führen Sie den folgenden Befehl aus, um die Konfigurationsoptionen anzuzeigen. 

```azurecli
az sql mi-arc edit --help
```

Im folgenden Beispiel werden die CPU-Kern- und Arbeitsspeicheranforderungen und Grenzwerte festgelegt.

```azurecli
az sql mi-arc edit --cores-limit 4 --cores-request 2 --memory-limit 4Gi --memory-request 2Gi -n <NAME_OF_SQL_MI> --k8s-namespace <namespace> --use-k8s
```

Zum Anzeigen der Änderungen, die an der SQL Managed Instance-Instanz vorgenommen wurden, können Sie die folgenden Befehle verwenden, um die YAML-Konfigurationsdatei anzuzeigen:

```azurecli
az sql mi-arc show -n <NAME_OF_SQL_MI> --k8s-namespace <namespace> --use-k8s
```

## <a name="configure-server-options"></a>Konfigurieren von Serveroptionen

Nach der Erstellung können Sie Serverkonfigurationseinstellungen für SQL Managed Instance mit Azure Arc-Unterstützung konfigurieren. In diesem Artikel wird beschrieben, wie Einstellungen wie das Aktivieren oder Deaktivieren des MSSQL-Agents oder das Aktivieren bestimmter Ablaufverfolgungsflags für Problembehandlungsszenarien festgelegt werden können.

Führen Sie die folgenden Schritte aus, um diese Einstellungen zu konfigurieren:

1. Erstellen Sie eine benutzerdefinierte `mssql-custom.conf`-Datei, die die gewünschten Einstellungen enthält. Im folgenden Beispiel werden der SQL-Agent und das Ablaufverfolgungsflag 1204 aktiviert:

   ```
   [sqlagent]
   enabled=true
   
   [traceflag]
   traceflag0 = 1204
   ```

1. Kopieren Sie die `mssql-custom.conf`-Datei in `/var/opt/mssql` im `mssql-miaa`-Container, der sich im `master-0`-Pod befindet. Ersetzen Sie `<namespaceName>` durch den Namen des Arc-Namespace.

   ```bash
   kubectl cp mssql-custom.conf master-0:/var/opt/mssql/mssql-custom.conf -c mssql-server -n <namespaceName>
   ```

1. Starten Sie die SQL Server-Instanz neu.  Ersetzen Sie `<namespaceName>` durch den Namen des Arc-Namespace.

   ```bash
   kubectl exec -it master-0  -c mssql-server -n <namespaceName> -- /bin/bash
   supervisorctl restart mssql-server
   exit
   ```


**Bekannte Einschränkungen**
- Für die oben genannten Schritte sind Administratorberechtigungen für den Kubernetes-Cluster erforderlich.
