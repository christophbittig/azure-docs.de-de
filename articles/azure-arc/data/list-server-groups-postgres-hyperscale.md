---
title: Auflisten von Servergruppen mit PostgreSQL Hyperscale mit Azure Arc-Unterstützung, die in einem Azure Arc-Datencontroller erstellt wurden
description: Auflisten von Servergruppen mit PostgreSQL Hyperscale mit Azure Arc-Unterstützung, die in einem Azure Arc-Datencontroller erstellt wurden
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: 9b479950bc5abc47cfc0f86a21262511a74f6852
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132279297"
---
# <a name="list-the-azure-arc-enabled-postgresql-hyperscale-server-groups-created-in-an-azure-arc-data-controller"></a>Auflisten von Servergruppen mit PostgreSQL Hyperscale mit Azure Arc-Unterstützung, die in einem Azure Arc-Datencontroller erstellt wurden

In diesem Artikel wird erläutert, wie Sie die Liste der Servergruppen abrufen, die in Ihrem Azure Arc-Datencontroller erstellt wurden.

Verwenden Sie zum Abrufen eine der folgenden Methoden, nachdem Sie eine Verbindung mit dem Azure Arc-Datencontroller hergestellt haben:

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="from-cli-with-azure-cli-extension-az"></a>Über die Befehlszeilenschnittstelle mit der Azure CLI-Erweiterung (az)

Das allgemeine Befehlsformat lautet:
```azurecli
az postgres arc-server list --k8s-namespace <namespace> --use-k8s
```

Dabei wird eine Ausgabe wie die folgende zurückgegeben:
```console
[
  {
    "name": "postgres01",
    "replicas": 1,
    "state": "Ready",
    "workers": 4
  }
]
```
Weitere Informationen zu den für diesen Befehl verfügbaren Parametern erhalten Sie, wenn Sie Folgendes ausführen:
```azurecli
az postgres arc-server list --help
```

## <a name="from-cli-with-kubectl"></a>Über die CLI mit kubectl
Führen Sie einen der folgenden Befehle aus.

**Führen Sie folgenden Befehl aus, um die Servergruppen unabhängig von der Postgres-Version aufzulisten:**
```console
kubectl get postgresqls -n <namespace>
```
Dabei wird eine Ausgabe wie die folgende zurückgegeben:
```console
NAME         STATE   READY-PODS   PRIMARY-ENDPOINT     AGE
postgres01   Ready   5/5          12.345.67.890:5432   12d
```

## <a name="next-steps"></a>Nächste Schritte:

* [Lesen Sie den Artikel zum Abrufen der Verbindungsendpunkte, und erstellen Sie die Verbindungszeichenfolgen, um eine Verbindung mit Ihrer Servergruppe herzustellen.](get-connection-endpoints-and-connection-strings-postgres-hyperscale.md)
* [Lesen Sie den Artikel zur Konfiguration einer Servergruppe mit PostgreSQL Hyperscale mit Azure Arc-Unterstützung.](show-configuration-postgresql-hyperscale-server-group.md)
