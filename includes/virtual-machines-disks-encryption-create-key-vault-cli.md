---
title: Datei einfügen
description: Datei einfügen
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 08/24/2021
ms.author: rogarana
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 23b6c4dddd839351e7eff49b6dea4c42fe6452d8
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122825031"
---
1. Achten Sie darauf, dass Sie die neueste Version der [Azure CLI](/cli/azure/install-az-cli2) installiert haben und mit [az login](/cli/azure/reference-index) bei einem Azure-Konto angemeldet sind.

1. Erstellen Sie eine Azure Key Vault-Instanz und den Verschlüsselungsschlüssel.

    Beim Erstellen der Key Vault-Instanz müssen Sie den Schutz vor endgültigem Löschen aktivieren. Der Schutz vor endgültigem Löschen stellt sicher, dass ein gelöschter Schlüssel erst nach Ablauf der Aufbewahrungsdauer dauerhaft gelöscht werden kann. Diese Einstellungen schützen Sie vor dem Verlust von Daten durch versehentliches Löschen. Diese Einstellungen sind obligatorisch, wenn ein Schlüsseltresor für die Verschlüsselung verwalteter Datenträger verwendet wird.

    > [!IMPORTANT]
    > Verwenden Sie keine gemischte Groß-/Kleinschreibung für die Region, da ansonsten Probleme beim Zuweisen zusätzlicher Datenträger zur Ressource im Azure-Portal auftreten.

    ```azurecli
    subscriptionId=yourSubscriptionID
    rgName=yourResourceGroupName
    location=westcentralus
    keyVaultName=yourKeyVaultName
    keyName=yourKeyName
    diskEncryptionSetName=yourDiskEncryptionSetName
    diskName=yourDiskName

    az account set --subscription $subscriptionId

    az keyvault create -n $keyVaultName \
    -g $rgName \
    -l $location \
    --enable-purge-protection true 
                       
    az keyvault key create --vault-name $keyVaultName \
    -n $keyName \
    --protection software
    ```

1.    Erstellen Sie eine DiskEncryptionSet-Instanz. Sie können „enable-auto-key-rotation“ auf TRUE festlegen, um die automatische Rotation des Schlüssels zu aktivieren. Wenn Sie die automatische Rotation aktivieren, aktualisiert das System innerhalb von einer Stunde automatisch alle verwalteten Datenträger, Momentaufnahmen und Images, die auf den Datenträgerverschlüsselungssatz verweisen, damit die neue Version des Schlüssels verwendet wird.
    
        ```azurecli
        keyVaultId=$(az keyvault show --name $keyVaultName --query [id] -o tsv)
    
        keyVaultKeyUrl=$(az keyvault key show --vault-name $keyVaultName --name $keyName --query [key.kid] -o tsv)
    
        az disk-encryption-set create -n $diskEncryptionSetName 
        -l $location \
        -g $rgName \
        --source-vault $keyVaultId \
        --key-url $keyVaultKeyUrl \
        --enable-auto-key-rotation false
        ```

1.    Gewähren Sie der DiskEncryptionSet-Ressource Zugriff auf den Schlüsseltresor. 

        > [!NOTE]
        > Es kann einige Minuten dauern, bis Azure die Identität des Datenträgerverschlüsselungssatzes in Azure Active Directory erstellt hat. Wenn Sie bei der Ausführung des folgenden Befehls einen ähnlichen Fehler wie „Active Directory-Objekt kann nicht gefunden werden“ erhalten, warten Sie einige Minuten, und versuchen Sie es erneut.

        ```azurecli
        desIdentity=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [identity.principalId] -o tsv)
    
        az keyvault set-policy -n $keyVaultName \
        -g $rgName \
        --object-id $desIdentity \
        --key-permissions wrapkey unwrapkey get
        ```
