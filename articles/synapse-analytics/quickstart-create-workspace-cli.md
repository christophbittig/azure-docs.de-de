---
title: 'Schnellstart: Erstellen eines Synapse-Arbeitsbereichs über die Azure CLI'
description: Erstellen Sie anhand der Schritte in diesem Leitfaden einen Azure Synapse-Arbeitsbereich über die Azure CLI.
services: synapse-analytics
author: rothja
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: workspace
ms.date: 08/25/2020
ms.author: jroth
ms.reviewer: jrasnick
ms.openlocfilehash: 4856142a7c075405930969a3e1f6da5804ef8c6c
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/28/2021
ms.locfileid: "129093153"
---
# <a name="quickstart-create-an-azure-synapse-workspace-with-azure-cli"></a>Schnellstart: Erstellen eines Azure Synapse-Arbeitsbereichs über die Azure CLI

Die Azure CLI ist die Befehlszeilenumgebung von Azure und dient zum Verwalten von Azure-Ressourcen. Sie können sie in Ihrem Browser mit Azure Cloud Shell verwenden. Außerdem können Sie sie unter macOS, Linux oder Windows installieren und über die Befehlszeile ausführen.

In dieser Schnellstartanleitung erfahren Sie, wie Sie über die Azure CLI einen Synapse-Arbeitsbereich erstellen.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Voraussetzungen

- Herunterladen und Installieren von [jq](https://stedolan.github.io/jq/download/), einem schlanken, flexiblen JSON-Befehlszeilenprozessor
- [Azure Data Lake Storage Gen2-Speicherkonto](../storage/common/storage-account-create.md)

    > [!IMPORTANT]
    > Der Azure Synapse-Arbeitsbereich muss Lese- und Schreibvorgänge im ausgewählten ADLS Gen2-Konto ausführen können. Darüber hinaus müssen Sie für jedes Speicherkonto, das Sie als primäres Speicherkonto verknüpfen, bei der Erstellung des Speicherkontos den **hierarchischen Namespace** aktiviert haben. Die Vorgehensweise ist auf der Seite [Erstellen eines Azure-Speicherkontos](../storage/common/storage-account-create.md?tabs=azure-portal#create-a-storage-account) beschrieben. 

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="create-an-azure-synapse-workspace-using-the-azure-cli"></a>Erstellen eines Azure Synapse-Arbeitsbereichs mithilfe der Azure CLI

1. Definieren Sie erforderliche Umgebungsvariablen zum Erstellen von Ressourcen für den Azure Synapse-Arbeitsbereich.

    | Name der Umgebungsvariablen | Beschreibung |
    |---|---|---|
    |StorageAccountName| Name des vorhandenen ADLS Gen2-Speicherkontos|
    |StorageAccountResourceGroup| Name der Ressourcengruppe des vorhandenen ADLS Gen2-Speicherkontos |
    |FileShareName| Name des vorhandenen Speicherdateisystems|
    |SynapseResourceGroup| Wählen Sie einen neuen Namen für Ihre Azure Synapse-Ressourcengruppe aus. |
    |Region| Wählen Sie eine der [Azure-Regionen](https://azure.microsoft.com/global-infrastructure/geographies/#overview) aus. |
    |SynapseWorkspaceName| Wählen Sie einen eindeutigen Namen für den neuen Azure Synapse-Arbeitsbereich aus. |
    |SqlUser| Wählen Sie einen Wert für einen neuen Benutzernamen aus.|
    |SqlPassword| Wählen Sie ein sicheres Kennwort aus.|
    |||

1. Erstellen Sie eine Ressourcengruppe als Container für Ihren Azure Synapse-Arbeitsbereich:
    ```azurecli
    az group create --name $SynapseResourceGroup --location $Region
    ```

1. Erstellen Sie einen Azure Synapse-Arbeitsbereich:
    ```azurecli
    az synapse workspace create \
      --name $SynapseWorkspaceName \
      --resource-group $SynapseResourceGroup \
      --storage-account $StorageAccountName \
      --file-system $FileShareName \
      --sql-admin-login-user $SqlUser \
      --sql-admin-login-password $SqlPassword \
      --location $Region
    ```

1. Rufen Sie die Web- und Entwicklungs-URL für den Azure Synapse-Arbeitsbereich ab:
    ```azurecli
    WorkspaceWeb=$(az synapse workspace show --name $SynapseWorkspaceName --resource-group $SynapseResourceGroup | jq -r '.connectivityEndpoints | .web')

    WorkspaceDev=$(az synapse workspace show --name $SynapseWorkspaceName --resource-group $SynapseResourceGroup | jq -r '.connectivityEndpoints | .dev')
    ```

1. Erstellen Sie eine Firewallregel, um auf Ihrem Computer den Zugriff auf den Azure Synapse-Arbeitsbereich zuzulassen:

    ```azurecli
    ClientIP=$(curl -sb -H "Accept: application/json" "$WorkspaceDev" | jq -r '.message')
    ClientIP=${ClientIP##'Client Ip address : '}
    echo "Creating a firewall rule to enable access for IP address: $ClientIP"

    az synapse workspace firewall-rule create --end-ip-address $ClientIP --start-ip-address $ClientIP --name "Allow Client IP" --resource-group $SynapseResourceGroup --workspace-name $SynapseWorkspaceName
    ```

1. Öffnen Sie die Web-URL-Adresse für den Azure Synapse-Arbeitsbereich, die in der Umgebungsvariablen `WorkspaceWeb` gespeichert ist, um auf den Arbeitsbereich zuzugreifen:

    ```azurecli
    echo "Open your Azure Synapse Workspace Web URL in the browser: $WorkspaceWeb"
    ```
    
    [ ![Azure Synapse-Arbeitsbereich (Web)](media/quickstart-create-synapse-workspace-cli/create-workspace-cli-1.png) ](media/quickstart-create-synapse-workspace-cli/create-workspace-cli-1.png#lightbox)


## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Führen Sie die folgenden Schritte aus, um den Azure Synapse-Arbeitsbereich zu löschen.
> [!WARNING]
> Wenn Sie einen Azure Synapse-Arbeitsbereich löschen, werden die Analyse-Engines und die Daten, die in der Datenbank der enthaltenen SQL-Pools gespeichert sind, sowie die Arbeitsbereichsmetadaten entfernt. Es kann keine Verbindung mehr mit den SQL- oder Apache Spark-Endpunkten hergestellt werden. Alle Codeartefakte werden gelöscht (Abfragen, Notebooks, Auftragsdefinitionen und Pipelines).
>
> Das Löschen des Arbeitsbereichs wirkt sich **nicht** auf die Daten in der Data Lake Store Gen2-Instanz aus, die mit dem Arbeitsbereich verknüpft ist.

Führen Sie den folgenden Befehl aus, wenn Sie den Azure Synapse-Arbeitsbereich löschen möchten:

```azurecli
az synapse workspace delete --name $SynapseWorkspaceName --resource-group $SynapseResourceGroup
```

## <a name="next-steps"></a>Nächste Schritte

Als Nächstes können Sie [SQL-Pools](quickstart-create-sql-pool-studio.md) oder [Apache Spark-Pools](quickstart-create-apache-spark-pool-studio.md) erstellen, um mit der Analyse und Untersuchung Ihrer Daten zu beginnen.
