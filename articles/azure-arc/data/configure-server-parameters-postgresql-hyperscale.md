---
title: Konfigurieren der Serverparameter für die Postgres-Engine Ihrer PostgreSQL Hyperscale-Servergruppe in Azure Arc
titleSuffix: Azure Arc-enabled data services
description: Konfigurieren der Serverparameter für die Postgres-Engine Ihrer PostgreSQL Hyperscale-Servergruppe in Azure Arc
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: 609ed203c794447f4fd0e0cd1a5267186a8eb67e
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132301739"
---
# <a name="set-the-database-engine-settings-for-azure-arc-enabled-postgresql-hyperscale"></a>Festlegen der Einstellungen der Datenbank-Engine für PostgreSQL Hyperscale mit Azure Arc-Unterstützung

In diesem Dokument wird beschrieben, wie Sie die Einstellungen der Datenbank-Engine Ihrer PostgreSQL Hyperscale-Servergruppe auf benutzerdefinierte (nicht standardmäßige) Werte festlegen. Ausführliche Informationen dazu, welche Parameter für die Datenbank-Engine festgelegt werden können und wie die Standardwerte lauten, finden Sie [hier](https://www.postgresql.org/docs/current/runtime-config.html) in der PostgreSQL-Dokumentation.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

> [!NOTE]
> Das Festlegen der folgenden Parameter wird in der Vorschau nicht unterstützt: 
>
> - `archive_command`
> - `archive_timeout`
> - `log_directory`
> - `log_file_mode`
> - `log_filename`
> - `restore_command`
> - `shared_preload_libraries`
> - `synchronous_commit`
> - `ssl`
> - `wal_level`

## <a name="syntax"></a>Syntax

Der Befehl zum Konfigurieren der Einstellungen für die Datenbank-Engine hat das folgende allgemeine Format:

```azurecli
az postgres arc-server edit -n <server group name>, [{--engine-settings, -e}] [{--replace-settings , --re}] {'<parameter name>=<parameter value>, ...'} --k8s-namespace <namespace> --use-k8s
```

## <a name="show-current-custom-values-if-they-have-been-set"></a>Anzeigen der aktuellen benutzerdefinierten Werte (sofern festgelegt)

**Mit einem Azure CLI-Befehl:**

```azurecli
az postgres arc-server show -n <server group name> --k8s-namespace <namespace> --use-k8s
```

Zum Beispiel:

```azurecli
az postgres arc-server show -n postgres01 --k8s-namespace arc --use-k8s 
```


**Oder mit einem kubectl-Befehl:**
```console
   kubectl describe postgresql <server group name> -n <namespace name>
   ```

   Zum Beispiel:

   ```console
   kubectl describe postgresql postgres01 -n arc
```

Durch beide Befehle werden die Spezifikationen der Servergruppe zurückgegeben, in der die von Ihnen festgelegten Parameter angezeigt werden sollen. Wenn kein Abschnitt für die Engine/Einstellungen vorhanden ist, bedeutet dies, dass alle Parameter mit dem Standardwert ausgeführt werden:

:::row:::
    :::column:::
        Beispiel für eine Ausgabe, wenn keine benutzerdefinierten Werte für die Einstellungen der Postgres-Engine festgelegt wurden. Die Spezifikationen enthalten keinen Abschnitt „engine\settings“.
    :::column-end:::
    :::column:::
        ```console
          ...
          "spec": {
            "dev": false,
            "engine": {
              "extensions": [
                {
                  "name": "citus"
                }
              ],
              "version": 12
            },
            "scale": {
              "replicas": 1,
              "syncReplicas": "0",
          "workers": 4
            },
            ...
        ```
        :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        Example of an output when custom values have been set for some of Postgres engine setting. The specs do show a section engine\settings.
    :::column-end:::
    :::column:::
        ```console
             ...
                Spec:
                  Dev:  false
                  Engine:
                    Extensions:
                      Name:  citus
                    Settings:
                      Default:
                        max_connections:  51
                      Roles:
                        Coordinator:
                          max_connections:  53
                        Worker:
                          max_connections:  52
                    Version:                12
                  Scale:
                    Replicas:       1
                    Sync Replicas:  0
                    Workers:        4
            ...
            ```
    :::column-end:::
:::row-end:::


The default value is, refer to the PostgreSQL documentation [here](https://www.postgresql.org/docs/current/runtime-config.html).



## Set custom values for engine settings

### Set a single parameter

**On both coordinator and worker roles:**

General syntax of the command:
```azurecli
az postgres arc-server edit -n <servergroup name> --engine-settings  '<ParameterName>=<CustomParameterValue>' --k8s-namespace <namespace> --use-k8s
```

Zum Beispiel:
```azurecli
az postgres arc-server edit -n postgres01 --engine-settings  'max_connections=51' --k8s-namespace arc --use-k8s
```

**Nur für die Workerrolle:**

Allgemeine Syntax des Befehls:
```azurecli
az postgres arc-server edit -n <servergroup name> --worker-settings '<ParameterName>=<CustomParameterValue>' --k8s-namespace <namespace> --use-k8s
```

Zum Beispiel:
```azurecli
az postgres arc-server edit -n postgres01 --worker-settings 'max_connections=52' --k8s-namespace arc --use-k8s
```

**Nur für die Koordinatorrolle:**

Allgemeine Syntax des Befehls:
```azurecli
az postgres arc-server edit -n <servergroup name> --coordinator-settings '<ParameterName>=<CustomParameterValue>' --k8s-namespace <namespace> --use-k8s
```

Zum Beispiel:
```azurecli
az postgres arc-server edit -n postgres01 --coordinator-settings 'max_connections=53' --k8s-namespace arc --use-k8s
```



### <a name="set-multiple-parameters-with-a-single-command"></a>Festlegen mehrerer Parameter mit einem einzigen Befehl

**Für Koordinator- und Workerrollen:**  
 
Allgemeine Syntax des Befehls:
```azurecli
az postgres arc-server edit -n <servergroup name> --engine-settings  '<ParameterName1>=<CustomParameterValue1>, ..., <ParameterNameN>=<CustomParameterValueN>' --k8s-namespace <namespace> --use-k8s
```

Zum Beispiel:
```azurecli
az postgres arc-server edit -n postgres01 --engine-settings  'shared_buffers=8MB, max_connections=60' --k8s-namespace arc --use-k8s
```

**Nur für die Workerrolle:**

Allgemeine Syntax des Befehls:
```azurecli
az postgres arc-server edit -n <servergroup name> --worker-settings '<ParameterName1>=<CustomParameterValue1>, ..., <ParameterNameN>=<CustomParameterValueN>' --k8s-namespace <namespace> --use-k8s
```

Zum Beispiel:
```azurecli
az postgres arc-server edit -n postgres01 --worker-settings 'shared_buffers=8MB, max_connections=60' --k8s-namespace arc --use-k8s
```

**Nur für die Koordinatorrolle:**

Allgemeine Syntax des Befehls:
```azurecli
az postgres arc-server edit -n <servergroup name> --coordinator-settings '<ParameterName1>=<CustomParameterValue1>, ..., <ParameterNameN>=<CustomParameterValueN>' --k8s-namespace <namespace> --use-k8s
```

Zum Beispiel:
```azurecli
az postgres arc-server edit -n postgres01 --coordinator-settings 'shared_buffers=8MB, max_connections=60' --k8s-namespace arc --use-k8s
```

### <a name="reset-one-parameter-to-its-default-value"></a>Zurücksetzen eines Parameters auf den Standardwert

**Für Koordinator- und Workerrollen:**

Allgemeine Syntax des Befehls:
```azurecli
az postgres arc-server edit -n <servergroup name> --engine-settings '<ParameterName>='  --coordinator-settings '<ParameterName>=' --worker-settings '<ParameterName>=' --k8s-namespace <namespace> --use-k8s
```
Zum Beispiel:
```azurecli
az postgres arc-server edit -n postgres01 --engine-settings  'shared_buffers='  --coordinator-settings 'shared_buffers=' --worker-settings 'shared_buffers=' --k8s-namespace arc --use-k8s
```

**Nur für die Koordinatorrolle:**

Allgemeine Syntax des Befehls:
```azurecli
az postgres arc-server edit -n <servergroup name> --coordinator-settings '<ParameterName>=' --k8s-namespace <namespace> --use-k8s
```
Zum Beispiel:
```azurecli
az postgres arc-server edit -n postgres01 --coordinator-settings 'shared_buffers=' --k8s-namespace arc --use-k8s
```

**Nur für die Workerrolle:**

Allgemeine Syntax des Befehls:
```azurecli
az postgres arc-server edit -n <servergroup name> --worker-settings '<ParameterName>=' --k8s-namespace <namespace> --use-k8s
```
Zum Beispiel:
```azurecli
az postgres arc-server edit -n postgres01 --worker-settings 'shared_buffers=' --k8s-namespace arc --use-k8s
````

### <a name="reset-all-parameters-to-their-default-values"></a>Zurücksetzen aller Parameter auf die Standardwerte

**Für Koordinator- und Workerrollen:**

Allgemeine Syntax des Befehls:
```azurecli
az postgres arc-server edit -n <servergroup name> --engine-settings  `'`' --worker-settings `'`' --coordinator-settings `'`' --replace-settings --k8s-namespace <namespace> --use-k8s
```

Zum Beispiel:
```azurecli
az postgres arc-server edit -n postgres01 --engine-settings  `'`' --worker-settings `'`' --coordinator-settings `'`'  --replace-settings --k8s-namespace arc --use-k8s
```

**Nur für die Koordinatorrolle:**

Allgemeine Syntax des Befehls:
```azurecli
az postgres arc-server edit -n <servergroup name> --coordinator-settings `'`'  --replace-settings --k8s-namespace <namespace> --use-k8s

```
Zum Beispiel:
```azurecli
az postgres arc-server edit -n postgres01 --coordinator-settings `'`'  --replace-settings --k8s-namespace arc --use-k8s
```

**Nur für die Workerrolle:**

Allgemeine Syntax des Befehls:
```azurecli
az postgres arc-server edit -n <servergroup name> --worker-settings `'`'  --replace-settings --k8s-namespace <namespace> --use-k8s
```
Zum Beispiel:
```azurecli
az postgres arc-server edit -n postgres01 --worker-settings `'`'  --replace-settings --k8s-namespace arc --use-k8s
```



## <a name="special-considerations"></a>Besondere Überlegungen

### <a name="set-a-parameter-which-value-contains-a-comma-space-or-special-character"></a>Festlegen eines Parameters, dessen Wert ein Komma, ein Leerzeichen oder ein Sonderzeichen enthält

```azurecli
az postgres arc-server edit -n <server group name> --engine-settings  '<parameter name>="<parameter value>"' --k8s-namespace <namespace> --use-k8s
```

Zum Beispiel:

```azurecli
az postgres arc-server edit -n postgres01 --engine-settings  'custom_variable_classes = "plpgsql,plperl"' --k8s-namespace <namespace> --use-k8s
```

### <a name="pass-an-environment-variable-in-a-parameter-value"></a>Übergeben einer Umgebungsvariablen in einem Parameterwert

Die Umgebungsvariable sollte in Anführungszeichen (" ") eingeschlossen werden, damit sie nicht aufgelöst wird, bevor sie festgelegt wird.

Beispiel: 

```azurecli
az postgres arc-server edit -n postgres01 --engine-settings  'search_path = "$user"' --k8s-namespace <namespace> --use-k8s
```

## <a name="next-steps"></a>Nächste Schritte
- Erfahren Sie mehr über das [Aufskalieren](scale-out-in-postgresql-hyperscale-server-group.md) der Servergruppe (Hinzufügen von Workerknoten).
- Erfahren Sie mehr über das [zentrale Hoch- oder Herunterskalieren](scale-up-down-postgresql-hyperscale-server-group-using-cli.md) der Servergruppe (Vergrößern/Verkleinern von Arbeitsspeicher/virtuellen Kernen).
