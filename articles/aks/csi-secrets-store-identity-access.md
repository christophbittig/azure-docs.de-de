---
title: Bereitstellen einer Zugriffsidentität für den Azure Key Vault-Anbieter für den Geheimnisspeicher-CSI-Treiber für AKS-Geheimnisse (Azure Kubernetes Service)
description: Erfahren Sie mehr über die verschiedenen Methoden, mit denen der Azure Key Vault-Anbieter für den Geheimnisspeicher-CSI-Treiber in Azure Key Vault (AKV) integriert werden kann.
author: nickomang
ms.author: nickoman
ms.service: container-service
ms.topic: article
ms.date: 10/13/2021
ms.custom: devx-track-azurecli
ms.openlocfilehash: e9e0741cd9f1f90efc4184891401d1d05fc5b5b2
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131511388"
---
# <a name="provide-an-identity-to-access-azure-key-vault-provider-for-secrets-store-csi-driver"></a>Bereitstellen einer Identität für den Zugriff auf den Azure Key Vault-Anbieter für den Geheimnisspeicher-CSI-Treiber

Der Geheimnisspeicher-CSI-Treiber in Azure Kubernetes Service (AKS) ermöglicht verschiedene Methoden des identitätsbasierten Zugriffs auf Azure Key Vault (AKV). In diesem Artikel werden diese Methoden und ihre Verwendung für den Zugriff auf eine AKV-Instanz und ihren Inhalt aus Ihrem Cluster heraus beschrieben. Weitere Informationen finden Sie unter [Verwenden des Secrets Store CSI-Treibers für Kubernetes in einem AKS-Cluster][csi-secrets-store-driver].

## <a name="use-pod-identity"></a>Verwenden von Podidentitäten

Für verwaltete Azure Active Directory-Podidentitäten werden Kubernetes-Primitive verwendet, um verwaltete Identitäten für Azure-Ressourcen und Identitäten in Azure Active Directory (AAD) Pods zuzuordnen. Mithilfe dieser Identitäten kann der Zugriff auf den Azure Key Vault-Geheimnisanbieter für den Geheimnisspeicher-CSI-Treiber gewährt werden.

### <a name="prerequisites"></a>Voraussetzungen

- Stellen Sie sicher, dass das [AAD-Add-On für Podidentitäten][aad-pod-identity] in Ihrem Cluster aktiviert wurde.
- Sie müssen einen Linux-basierten Cluster verwenden.

### <a name="usage"></a>Verwendung

1. Führen Sie die Schritte unter [Verwenden von verwalteten Azure Active Directory-Podidentitäten in Azure Kubernetes Service][aad-pod-identity-create] aus, um eine Clusteridentität zu erstellen, ihr Berechtigungen zuzuweisen und eine Podidentität zu erstellen. Notieren Sie sich die `clientId` und den `name` der neu erstellten Identität.

2. Weisen Sie der neuen Identität Berechtigungen zu, sodass sie Ihre AKV-Instanz lesen und ihren Inhalt anzeigen kann, indem Sie Folgendes ausführen:

```azurecli-interactive
# set policy to access keys in your keyvault
az keyvault set-policy -n <keyvault-name> --key-permissions get --spn <pod-identity-client-id>
# set policy to access secrets in your keyvault
az keyvault set-policy -n <keyvault-name> --secret-permissions get --spn <pod-identity-client-id>
# set policy to access certs in your keyvault
az keyvault set-policy -n <keyvault-name> --certificate-permissions get --spn <pod-identity-client-id>
```

3. Erstellen Sie eine SecretProviderClass mit dem folgenden YAML-Code, geben Sie die Werte für `aadpodidbinding`, `tenantId` und die Objekte an, die Sie aus Ihrer AKV-Instanz abrufen möchten:

```yml
# This is a SecretProviderClass example using aad-pod-identity to access Keyvault
apiVersion: secrets-store.csi.x-k8s.io/v1
kind: SecretProviderClass
metadata:
  name: azure-kvname-podid
spec:
  provider: azure
  parameters:
    usePodIdentity: "true"               # Set to true for using aad-pod-identity to access keyvault
    keyvaultName: <key-vault-name>       # Set to the name of your Azure Key Vault instance
    cloudName: ""                        # [OPTIONAL for Azure] if not provided, azure environment will default to AzurePublicCloud
    objects:  |
      array:
        - |
          objectName: secret1
          objectType: secret             # object types: secret, key or cert
          objectVersion: ""              # [OPTIONAL] object versions, default to latest if empty
        - |
          objectName: key1
          objectType: key
          objectVersion: ""
    tenantId: <tenant-Id>                # The tenant ID of the Azure Key Vault instance
```

4. Wenden Sie die SecretProviderClass auf Ihren Cluster an:

```bash
kubectl apply -f secretproviderclass.yaml
```

5. Erstellen Sie einen Pod mit folgendem YAML-Code, und geben Sie dabei den Namen Ihrer Identität ein:

```yml
# This is a sample pod definition for using SecretProviderClass and aad-pod-identity to access Keyvault
kind: Pod
apiVersion: v1
metadata:
  name: busybox-secrets-store-inline-podid
  labels:
    aadpodidbinding: <name>                   # Set the label value to the name of your pod identity
spec:
  containers:
    - name: busybox
      image: k8s.gcr.io/e2e-test-images/busybox:1.29-1
      command:
        - "/bin/sleep"
        - "10000"
      volumeMounts:
      - name: secrets-store01-inline
        mountPath: "/mnt/secrets-store"
        readOnly: true
  volumes:
    - name: secrets-store01-inline
      csi:
        driver: secrets-store.csi.k8s.io
        readOnly: true
        volumeAttributes:
          secretProviderClass: "azure-kvname-podid"
```

6. Wenden Sie den Pod auf Ihren Cluster an:

```bash
kubectl apply -f pod.yaml
```

## <a name="use-a-user-assigned-managed-identity"></a>Verwenden einer benutzerseitig zugewiesenen verwalteten Identität

1. Sie können die benutzerseitig zugewiesene verwaltete Identität, die beim [Aktivieren der verwalteten Identität in Ihrem AKS-Cluster][use-managed-identity] erstellt wurde, für den Zugriff auf Ihre AKV-Instanz verwenden:

```azurecli-interactive
az aks show -g <resource-group> -n <cluster-name> --query identityProfile.kubeletidentity.clientId -o tsv
```

Alternativ können Sie eine neue erstellen und Ihrer VM-Skalierungsgruppe oder jeder VM-Instanz in Ihrer Verfügbarkeitsgruppe zuweisen:

```azurecli-interactive
az identity create -g <resource-group> -n <identity-name> 
az vmss identity assign -g <resource-group> -n <agent-pool-vmss> --identities <identity-resource-id>
az vm identity assign -g <resource-group> -n <agent-pool-vm> --identities <identity-resource-id>
```

2. Weisen Sie Ihrer Identität Berechtigungen zu, sodass sie Ihre AKV-Instanz lesen und ihren Inhalt anzeigen kann, indem Sie Folgendes ausführen:

```azurecli-interactive
# set policy to access keys in your keyvault
az keyvault set-policy -n <keyvault-name> --key-permissions get --spn <identity-client-id>
# set policy to access secrets in your keyvault
az keyvault set-policy -n <keyvault-name> --secret-permissions get --spn <identity-client-id>
# set policy to access certs in your keyvault
az keyvault set-policy -n <keyvault-name> --certificate-permissions get --spn <identity-client-id>
```

3. Erstellen Sie eine SecretProviderClass mit dem folgenden YAML-Code, geben Sie die Werte für `userAssignedIdentityID`, `keyvaultName`, `tenantId` und die Objekte an, die Sie aus Ihrer AKV-Instanz abrufen möchten:

```yml
# This is a SecretProviderClass example using user-assigned identity to access Keyvault
apiVersion: secrets-store.csi.x-k8s.io/v1
kind: SecretProviderClass
metadata:
  name: azure-kvname-user-msi
spec:
  provider: azure
  parameters:
    usePodIdentity: "false"
    useVMManagedIdentity: "true"          # Set to true for using managed identity
    userAssignedIdentityID: <client-id>   # Set the clientID of the user-assigned managed identity to use
    keyvaultName: <key-vault-name>        # Set to the name of your Azure Key Vault instance
    cloudName: ""                         # [OPTIONAL for Azure] if not provided, azure environment will default to AzurePublicCloud
    objects:  |
      array:
        - |
          objectName: secret1
          objectType: secret              # object types: secret, key or cert
          objectVersion: ""               # [OPTIONAL] object versions, default to latest if empty
        - |
          objectName: key1
          objectType: key
          objectVersion: ""
    tenantId: <tenant-id>                 # The tenant ID of the Azure Key Vault instance
```

4. Wenden Sie die SecretProviderClass auf Ihren Cluster an:

```bash
kubectl apply -f secretproviderclass.yaml
```

5. Erstellen Sie einen Pod mit folgendem YAML-Code, und geben Sie dabei den Namen Ihrer Identität ein:

```yml
# This is a sample pod definition for using SecretProviderClass and user-assigned identity to access Keyvault
kind: Pod
apiVersion: v1
metadata:
  name: busybox-secrets-store-inline-user-msi
spec:
  containers:
    - name: busybox
      image: k8s.gcr.io/e2e-test-images/busybox:1.29-1
      command:
        - "/bin/sleep"
        - "10000"
      volumeMounts:
      - name: secrets-store01-inline
        mountPath: "/mnt/secrets-store"
        readOnly: true
  volumes:
    - name: secrets-store01-inline
      csi:
        driver: secrets-store.csi.k8s.io
        readOnly: true
        volumeAttributes:
          secretProviderClass: "azure-kvname-user-msi"
```

6. Wenden Sie den Pod auf Ihren Cluster an:

```bash
kubectl apply -f pod.yaml
```

## <a name="use-a-system-assigned-managed-identity"></a>Verwenden einer systemseitig zugewiesenen verwalteten Identität

### <a name="prerequisites"></a>Voraussetzungen

- [Aktivieren der systemseitig zugewiesenen verwalteten Identität][enable-system-assigned-identity] auf den VMs oder in den Skalierungsgruppen Ihres Clusters

### <a name="usage"></a>Verwendung

1. Vergewissern Sie sich, dass die Knoten Ihrer VM-Skalierungsgruppe oder Verfügbarkeitsgruppe über eine eigene systemseitig zugewiesene Identität verfügen:

```azurecli-interactive
az vmss identity show -g <resource group>  -n <vmss scalset name> -o yaml
az vm identity show -g <resource group> -n <vm name> -o yaml
```

Die Ausgabe sollte `type: SystemAssigned` enthalten. Notieren Sie sich den Namen in `principalId`.

2. Weisen Sie der Identität Berechtigungen zu, sodass sie Ihre AKV-Instanz lesen und ihren Inhalt anzeigen kann, indem Sie Folgendes ausführen:

```azurecli-interactive
# set policy to access keys in your keyvault
az keyvault set-policy -n <keyvault-name> --key-permissions get --spn <identity-principal-id>
# set policy to access secrets in your keyvault
az keyvault set-policy -n <keyvault-name> --secret-permissions get --spn <identity-principal-id>
# set policy to access certs in your keyvault
az keyvault set-policy -n <keyvault-name> --certificate-permissions get --spn <identity-principal-id>
```

3. Erstellen Sie eine SecretProviderClass mit dem folgenden YAML-Code, geben Sie die Werte für `keyvaultName`, `tenantId` und die Objekte an, die Sie aus Ihrer AKV-Instanz abrufen möchten:

```yml
# This is a SecretProviderClass example using system-assigned identity to access Keyvault
apiVersion: secrets-store.csi.x-k8s.io/v1
kind: SecretProviderClass
metadata:
  name: azure-kvname-system-msi
spec:
  provider: azure
  parameters:
    usePodIdentity: "false"
    useVMManagedIdentity: "true"    # Set to true for using managed identity
    userAssignedIdentityID: ""      # If empty, then defaults to use the system assigned identity on the VM
    keyvaultName: <key-vault-name>
    cloudName: ""                   # [OPTIONAL for Azure] if not provided, azure environment will default to AzurePublicCloud
    objects:  |
      array:
        - |
          objectName: secret1
          objectType: secret        # object types: secret, key or cert
          objectVersion: ""         # [OPTIONAL] object versions, default to latest if empty
        - |
          objectName: key1
          objectType: key
          objectVersion: ""
    tenantId: <tenant-id>           # The tenant ID of the Azure Key Vault instance
```

4. Wenden Sie die SecretProviderClass auf Ihren Cluster an:

```bash
kubectl apply -f secretproviderclass.yaml
```

5. Erstellen Sie einen Pod mit folgendem YAML-Code, und geben Sie dabei den Namen Ihrer Identität ein:

```yml
# This is a sample pod definition for using SecretProviderClass and system-assigned identity to access Keyvault
kind: Pod
apiVersion: v1
metadata:
  name: busybox-secrets-store-inline-system-msi
spec:
  containers:
    - name: busybox
      image: k8s.gcr.io/e2e-test-images/busybox:1.29-1
      command:
        - "/bin/sleep"
        - "10000"
      volumeMounts:
      - name: secrets-store01-inline
        mountPath: "/mnt/secrets-store"
        readOnly: true
  volumes:
    - name: secrets-store01-inline
      csi:
        driver: secrets-store.csi.k8s.io
        readOnly: true
        volumeAttributes:
          secretProviderClass: "azure-kvname-system-msi"
```

## <a name="next-steps"></a>Nächste Schritte

[Überprüfen Sie die Einbindung der Geheimnisse][validate-secrets] unter dem Volumepfad, der im YAML-Code Ihres Pods angegeben ist.

<!-- LINKS INTERNAL -->

[csi-secrets-store-driver]: ./csi-secrets-store-driver.md
[aad-pod-identity]: ./use-azure-ad-pod-identity.md
[aad-pod-identity-create]: ./use-azure-ad-pod-identity.md#create-an-identity
[use-managed-identity]: ./use-managed-identity.md
[validate-secrets]: ./csi-secrets-store-driver.md#validate-the-secrets
[enable-system-assigned-identity]: ../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-azure-vm

<!-- LINKS EXTERNAL -->
