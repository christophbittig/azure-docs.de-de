---
title: Verschlüsseln von Betriebssystemdatenträgern mit einem kundenseitig verwalteten Schlüssel (CMK) in Azure Red Hat OpenShift (ARO)
description: Verschlüsseln von Betriebssystemdatenträgern mit einem kundenseitig verwalteten Schlüssel (CMK) in Azure Red Hat OpenShift (ARO)
author: sayjadha
ms.author: suvetriv
ms.service: azure-redhat-openshift
keywords: Verschlüsselung, BYOK, ARO, Bereitstellen, OpenShift, Red Hat
ms.topic: how-to
ms.date: 10/18/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: b4df39040ac3cfbab2ecfa8cb24c72ff19ee5bdc
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131048233"
---
# <a name="encrypt-os-disks-with-a-customer-managed-key-cmk-on-azure-red-hat-openshift-aro-preview"></a>Verschlüsseln von Betriebssystemdatenträgern mit einem kundenseitig verwalteten Schlüssel (CMK) in Azure Red Hat OpenShift (ARO) (Vorschau)

Standardmäßig wurden die Betriebssystemdatenträger der virtuellen Computer in einem Azure Red Hat OpenShift-Cluster mit automatisch generierten Schlüsseln verschlüsselt, die von Microsoft Azure verwaltet wurden. Aus Sicherheitsgründen können Kunden die Betriebssystemdatenträger bei der Bereitstellung eines ARO-Clusters mit selbstverwalteten Schlüsseln verschlüsseln. Diese Features ermöglichen mehr Kontrolle, indem vertrauliche Daten mit kundenseitig verwalteten Schlüsseln verschlüsselt werden.

Für Cluster, die mit kundenseitig verwalteten Schlüsseln erstellt werden, ist eine Standardspeicherklasse mit ihren Schlüsseln aktiviert. Daher werden sowohl Betriebssystemdatenträger als auch Datenträger mit diesen Schlüsseln verschlüsselt. Die kundenseitig verwalteten Schlüssel werden in Azure Key Vault gespeichert. Weitere Informationen zur Verwendung von Azure Key Vault zum Erstellen und Verwalten von Schlüsseln finden Sie unter [Serverseitige Verschlüsselung von Azure Disk Storage](../key-vault/general/basic-concepts.md) in der Microsoft Azure-Dokumentation.

Mit hostbasierter Verschlüsselung werden die auf dem VM-Host der VMs Ihres ARO-Agent-Knotens gespeicherten Daten ruhend verschlüsselt und verschlüsselt an den Speicherdienst übermittelt. Dies bedeutet, dass temporäre Datenträger im Ruhezustand mit von der Plattform verwalteten Schlüsseln verschlüsselt werden. Der Cache von Betriebssystem- und sonstigen Datenträgern wird im Ruhezustand entweder mit von der Plattform verwalteten Schlüsseln oder vom Kunden verwalteten Schlüsseln verschlüsselt, je nachdem, welcher Verschlüsselungstyp auf diesen Datenträgern festgelegt wurde. Standardmäßig werden bei der Verwendung von ARO Betriebssystem- und sonstige Datenträger im Ruhezustand mit von der Plattform verwalteten Schlüsseln verschlüsselt. Dies bedeutet, dass die Caches für diese Datenträger standardmäßig auch im Ruhezustand mit von der Plattform verwalteten Schlüsseln verschlüsselt werden. Sie können Ihre eigenen verwalteten Schlüssel angeben, indem Sie die nachstehenden Verschlüsselungsschritte befolgen. Der Cache für diese Datenträger wird dann auch mit dem Schlüssel verschlüsselt, den Sie in diesem Schritt angeben.

> [!IMPORTANT]
> ARO-Vorschaufunktionen stehen gemäß dem Self-Service- und Aktivierungsprinzip zur Verfügung. Previewfunktionen werden „wie besehen“ und „wie verfügbar“ bereitgestellt und sind von den Vereinbarungen zum Service Level und der eingeschränkten Garantie ausgeschlossen. Previewfunktionen werden teilweise vom Kundensupport auf Grundlage der bestmöglichen Leistung abgedeckt. Daher sind diese Funktionen nicht für die Verwendung in der Produktion vorgesehen.

## <a name="limitation"></a>Einschränkung
Es liegt in der Verantwortung der Kunden, die Key Vault-Instanz und den Datenträgerverschlüsselungssatz in Azure zu verwalten. Wenn die Schlüssel nicht verwaltet werden, führt dies zu fehlerhaften ARO-Clustern. Die VMs funktionieren nicht mehr, und daher funktioniert auch der gesamte ARO-Cluster nicht mehr. Das Azure Red Hat OpenShift-Engineeringteam kann nicht auf die Schlüssel zugreifen. Daher kann es die Schlüssel nicht sichern, replizieren oder abrufen. Weitere Informationen zur Verwendung von Datenträgerverschlüsselungssätzen zum Verwalten Ihrer Verschlüsselungsschlüssel finden Sie unter [Serverseitige Verschlüsselung von Azure Disk Storage](../virtual-machines/disk-encryption.md) in der Microsoft Azure-Dokumentation.

## <a name="prerequisites"></a>Voraussetzungen
* [Überprüfen von Berechtigungen](tutorial-create-cluster.md#verify-your-permissions). Sie benötigen die Berechtigungen „Mitwirkender“ und „Benutzerzugriffsadministrator“ oder „Besitzer“ für das Abonnement.
* Registrieren Sie die Ressourcenanbieter, wenn Sie über mehrere Azure-Abonnements verfügen. Registrierungsdetails finden Sie unter [Registrieren der Ressourcenanbieter](tutorial-create-cluster.md#register-the-resource-providers).

## <a name="install-the-preview-azure-cli-extension"></a>Installieren der Azure CLI-Erweiterung (Vorschau)
Installieren und verwenden Sie die Azure CLI, um eine Key Vault-Instanz zu erstellen. Die Azure CLI ermöglicht die Ausführung von Befehlen über ein Terminal, indem interaktive Befehlszeilen-Eingabeaufforderungen oder ein Skript verwendet werden.
> [!NOTE]
> Die CLI-Erweiterung ist nur für die Previewfunktion erforderlich.

1. Klicken Sie auf die folgende URL, um sowohl das Python Wheel als auch die CLI-Erweiterung herunterzuladen: [https://aka.ms/az-aroext-latest.whl](https://aka.ms/az-aroext-latest.whl)
1. Führen Sie den folgenden Befehl aus:
    ```azurecli-interactive
    az extension add --upgrade -s <path to downloaded .whl file>
    ```
1. Stellen Sie sicher, dass die CLI-Erweiterung verwendet wird:
    ```azurecli-interactive
    az extension list
    [
      {
        "experimental": false,
        "extensionType": "whl",
        "name": "aro",
        "path": "<path may differ depending on system>",
        "preview": true,
        "version": "1.0.1"
      }
    ]
    ```

## <a name="create-a-virtual-network-containing-two-empty-subnets"></a>Erstellen eines virtuellen Netzwerks mit zwei leeren Subnetzen
Erstellen Sie ein virtuelles Netzwerk mit zwei leeren Subnetzen. Wenn Sie über ein vorhandenes virtuelles Netzwerk verfügen, das Ihre Anforderungen erfüllt, können Sie diesen Schritt überspringen. Informationen zum Erstellen eines virtuellen Netzwerks finden Sie unter [Erstellen eines virtuellen Netzwerks mit zwei leeren Subnetzen](tutorial-create-cluster.md#create-a-virtual-network-containing-two-empty-subnets).

## <a name="create-an-azure-key-vault-instance"></a>Erstellen einer Azure Key Vault-Instanz
Sie müssen eine Azure Key Vault-Instanz zum Speichern Ihrer Schlüssel verwenden. Erstellen Sie eine neue Key Vault-Instanz mit aktiviertem Löschschutz. Erstellen Sie dann einen neuen Schlüssel innerhalb der Key Vault-Instanz zum Speichern Ihres eigenen benutzerdefinierten Schlüssels.
1. Festlegen zusätzlicher Umgebungsberechtigungen:
    ```
    export KEYVAULT_NAME=$USER-enckv
    export KEYVAULT_KEY_NAME=$USER-key
    export DISK_ENCRYPTION_SET_NAME=$USER-des
    ```
1. Erstellen Sie eine Key Vault-Instanz und einen Schlüssel in der Key Vault-Instanz:
    ```azurecli-interactive
    az keyvault create -n $KEYVAULT_NAME \
                   -g $RESOURCEGROUP \
                   -l $LOCATION \
                   --enable-purge-protection true \
                   --enable-soft-delete true

    az keyvault key create --vault-name $KEYVAULT_NAME \
                           -n $KEYVAULT_KEY_NAME \
                           --protection software

    KEYVAULT_ID=$(az keyvault show --name $KEYVAULT_NAME --query "[id]" -o tsv)

    KEYVAULT_KEY_URL=$(az keyvault key show --vault-name $KEYVAULT_NAME \
                                            --name $KEYVAULT_KEY_NAME \
                                            --query "[key.kid]" -o tsv)
    ```

## <a name="create-an-azure-disk-encryption-set"></a>Erstellen eines Azure-Datenträgerverschlüsselungssatzes
Der Azure-Datenträgerverschlüsselungssatz wird als Bezugspunkt für Datenträger in ARO-Clustern verwendet. Er ist mit der im vorherigen Schritt erstellten Azure Key Vault-Instanz verbunden und pullt kundenseitig verwaltete Schlüssel von diesem Speicherort.
```azurecli-interactive
az disk-encryption-set create -n $DISK_ENCRYPTION_SET_NAME \
                              -l $LOCATION \
                              -g $RESOURCEGROUP \
                              --source-vault $KEYVAULT_ID \
                              --key-url $KEYVAULT_KEY_URL

DES_ID=$(az disk-encryption-set show -n $DISK_ENCRYPTION_SET_NAME -g $RESOURCEGROUP --query 'id' -o tsv)

DES_IDENTITY=$(az disk-encryption-set show -n $DISK_ENCRYPTION_SET_NAME \
                                           -g $RESOURCEGROUP \
                                           --query "[identity.principalId]" \
                                           -o tsv)
```

## <a name="grant-permissions-for-the-disk-encryption-set-to-access-the-key-vault"></a>Erteilen von Berechtigungen für den Datenträgerverschlüsselungssatz für den Zugriff auf die Key Vault-Instanz
Verwenden Sie den Datenträgerverschlüsselungssatz, den Sie im vorherigen Schritt erstellt haben, und erteilen Sie dem Datenträgerverschlüsselungssatz die Berechtigung für den Zugriff auf und die Verwendung von Azure Key Vault.
```azurecli-interactive
az keyvault set-policy -n $KEYVAULT_NAME \
                       -g $RESOURCEGROUP \
                       --object-id $DES_IDENTITY \
                       --key-permissions wrapkey unwrapkey get
```

## <a name="create-an-aro-cluster"></a>Erstellen eines ARO-Clusters
Erstellen Sie einen ARO-Cluster, um die kundenseitig verwalteten Schlüssel zu verwenden.
```azurecli-interactive
az aro create --resource-group $RESOURCEGROUP \
              --name $CLUSTER  \
              --vnet aro-vnet  \
              --master-subnet master-subnet \
              --worker-subnet worker-subnet \
              --disk-encryption-set $DES_ID
```
Nach dem Erstellen des ARO-Clusters werden alle VMs mit den kundenseitig verwalteten Verschlüsselungsschlüsseln verschlüsselt.

Führen Sie die folgenden Befehle aus, um zu überprüfen, ob Sie die Schlüssel ordnungsgemäß konfiguriert haben:
1. Rufen Sie den Namen der Clusterressourcengruppe ab, in der sich die Cluster-VMs, Datenträger usw. befinden:
    ```azurecli-interactive
    CLUSTERRESOURCEGROUP=$(az aro show --resource-group $RESOURCEGROUP --name $CLUSTER --query 'clusterProfile.resourceGroupId' -o tsv | cut -d '/' -f 5)
    ```
2. Überprüfen Sie, ob an die Datenträgern der richtige Datenträgerverschlüsselungssatz angefügt ist:
    ```azurecli-interactive
    az disk list -g $CLUSTERRESOURCEGROUP --query '[].encryption'
    ```
    Das Feld `diskEncryptionSetId` in der Ausgabe muss auf den Datenträgerverschlüsselungssatz verweisen, den Sie beim Erstellen des ARO-Clusters angegeben haben.
