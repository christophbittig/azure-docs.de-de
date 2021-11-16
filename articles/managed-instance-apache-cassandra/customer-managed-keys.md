---
title: 'Azure Managed Instance for Apache Cassandra: Kundenseitig verwaltete Schlüssel'
description: Vom Kunden verwaltete Schlüssel
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: conceptual
ms.date: 10/29/2021
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 4cf92148accf1d07aaafc0a1640de36e2884a9c5
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2021
ms.locfileid: "131893441"
---
# <a name="customer-managed-keys---overview"></a>Übersicht über kundenseitig verwaltete Schlüssel

Azure Managed Instance for Apache Cassandra bietet die Möglichkeit, Daten auf dem Datenträger mithilfe Ihres eigenen Schlüssels zu verschlüsseln. In diesem Artikel wird beschrieben, wie Sie kundenseitig verwaltete Schlüssel mit Azure Key Vault implementieren.

## <a name="prerequisites"></a>Voraussetzungen

- Richten Sie ein Geheimnis mit Azure Key Vault ein. Erfahren Sie [hier](/azure/key-vault/secrets/about-secrets) mehr über Azure Key Vault.
- Stellen Sie ein virtuelles Netzwerk in Ihrer Ressourcengruppe bereit, und wenden Sie die Rolle „Netzwerkmitwirkender“ mit dem Azure Cosmos DB-Dienstprinzipal als Mitglied an. Weitere Informationen finden Sie unter [Erstellen eines Clusters vom Typ „Azure Managed Instance for Apache Cassandra“ mithilfe der Azure CLI](create-cluster-cli.md). 

> [!IMPORTANT]
> Für diesen Artikel ist die Azure CLI-Version 2.30.0 oder höher erforderlich. Wenn Sie Azure Cloud Shell verwenden, ist die neueste Version bereits installiert.

## <a name="create-a-cluster-with-system-assigned-identity"></a><a id="create-cluster"></a>Erstellen eines Clusters mit systemseitig zugewiesener Identität

   > [!NOTE]
   > Wie in den Voraussetzungen erwähnt, müssen Sie zur Vermeidung einer fehlerhaften Bereitstellung sicherstellen, dass Sie die entsprechende Rolle auf Ihr virtuelles Netzwerk angewendet haben, bevor Sie versuchen, einen Cluster für eine verwaltete Instanz bereitzustellen:
   > ```azurecli-interactive  
   >     az role assignment create \
   >     --assignee a232010e-820c-4083-83bb-3ace5fc29d0b \
   >     --role 4d97b98b-1d4f-4787-a291-c67834d212e7 \
   >     --scope /subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Network/virtualNetworks/<vnetName>
   > ```

1. Erstellen Sie einen Cluster, indem Sie den Identitätstyp als „SystemAssigned“ angeben und `<subscriptionID>`, `<resourceGroupName>`, `<vnetName>` und `<subnetName>` durch die entsprechenden Werte ersetzen:

    ```azurecli-interactive    
    subnet="/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Network/virtualNetworks/<vnetName>/subnets/<subnetName>"
    cluster="thvankra-cmk-test-wcus"
    group="thvankra-nova-cmk-test"
    region="westcentralus"
    password="PlaceholderPassword"
    
    az managed-cassandra cluster create \
        --identity-type SystemAssigned \
        --resource-group $group \
        --location $region \
        --cluster-name $cluster \
        --delegated-management-subnet-id $subnet \
        --initial-cassandra-admin-password $password
    ```

1. Abrufen der Identitätsinformationen des erstellten Clusters

    ```azurecli-interactive
    az managed-cassandra cluster show -c $cluster -g $group
    ```

    Die Ausgabe enthält einen Identitätsabschnitt wie den folgenden. Kopieren Sie `principalId` für die spätere Verwendung:

    ```shell
      "identity": {
        "principalId": "1aa51c7f-196a-4013-a656-1ccabfdc54e0",
        "tenantId": "72f988bf-86f1-41af-91ab-2d7cd011db47",
        "type": "SystemAssigned"
      }
    ```
 
1. Erstellen Sie in Azure Key Vault eine Zugriffsrichtlinie für Ihre Schlüssel:

   :::image type="content" source="./media/cmk/key-vault-access-policy-1.png" alt-text="Key Vault-Zugriffsrichtlinie 1" lightbox="./media/cmk/key-vault-access-policy-1.png" border="true":::

1. Weisen Sie der oben abgerufenen `principalId` des Clusters die Schlüsselberechtigungen `get`, `wrap` und `unwrap` für den Schlüsseltresor zu. Im Portal können Sie die Prinzipal-ID des Clusters auch über den Clusternamen abrufen:
 

   :::image type="content" source="./media/cmk/key-vault-access-policy-2.png" alt-text="Key Vault-Zugriffsrichtlinie 2" lightbox="./media/cmk/key-vault-access-policy-2.png" border="true":::

   > [!WARNING]
   > Vergewissern Sie sich, dass der Bereinigungsschutz für den Schlüsseltresor aktiviert ist. Ohne ihn wird bei der Bereitstellung von Rechenzentren ein Fehler auftreten. 

1. Nachdem Sie auf `add` geklickt haben, um die Richtlinie hinzuzufügen, müssen Sie sie speichern:

   :::image type="content" source="./media/cmk/save.png" alt-text="Zugriffsrichtlinie speichern" lightbox="./media/cmk/key-vault-access-policy-2.png" border="true":::

1. Um die Schlüsselkennung zu erhalten, wählen Sie Ihren Schlüssel aus:

   :::image type="content" source="./media/cmk/select-key.png" alt-text="Schlüssel auswählen" lightbox="./media/cmk/key-identifier-1.png" border="true":::

1. Klicken Sie auf die aktuelle Version:

   :::image type="content" source="./media/cmk/current-version.png" alt-text="Aktuelle Version auswählen" lightbox="./media/cmk/key-identifier-1.png" border="true":::

1. Speichern Sie die Schlüsselkennung zur späteren Verwendung:

   :::image type="content" source="./media/cmk/key-identifier-2.png" alt-text="Schlüsselkennungsschritt 2" lightbox="./media/cmk/key-identifier-1.png" border="true":::


1. Erstellen Sie das Rechenzentrum, indem Sie `<key identifier>` durch denselben Schlüssel (dem URI, den Sie im vorherigen Schritt kopiert haben) für die Verschlüsselung des verwalteten Datenträgers (managed-disk-customer-key-uri) und des Sicherungsspeichers (backup-storage-customer-key-uri) wie unten dargestellt zu ersetzen (verwenden Sie denselben Wert für `subnet`, den Sie zuvor verwendet haben): 

    ```azurecli-interactive
    managedDiskKeyUri = "<key identifier>"
    backupStorageKeyUri = "<key identifier>"
    group="thvankra-nova-cmk-test"
    region="westcentralus"
    cluster="thvankra-cmk-test-2"
    dc="dc1"
    nodecount=3
    subnet="/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Network/virtualNetworks/<vnetName>/subnets/<subnetName>"
        
    az managed-cassandra datacenter create \
        --resource-group $group \
        --cluster-name $cluster \
        --data-center-name $dc \
        --managed-disk-customer-key-uri $managedDiskKeyUri \
        --backup-storage-customer-key-uri $backupStorageKeyUri \
        --node-count $nodecount \
        --delegated-subnet-id $subnet \
        --data-center-location $region \
        --sku Standard_DS14_v2
    ```

1. Einem bestehenden Cluster ohne Identitätsinformationen kann eine Identität zugewiesen werden (siehe unten):

    ```azurecli-interactive
    az managed-cassandra cluster update --identity-type SystemAssigned -g $group -c $cluster
    ```

## <a name="rotating-the-key"></a><a id="update-cluster"></a>Rotieren des Schlüssels

1. Nachfolgend finden Sie den Befehl zur Aktualisierung des Schlüssels:

    ```azurecli-interactive
    managedDiskKeyUri = "<key identifier>"
    backupStorageKeyUri = "<key identifier>"
    
    az managed-cassandra datacenter update \
        --resource-group $group \
        --cluster-name $cluster \ 
        --data-center-name $dc \
        --managed-disk-customer-key-uri $managedDiskKeyUri \
        --backup-storage-customer-key-uri $backupStorageKeyUri
    ```