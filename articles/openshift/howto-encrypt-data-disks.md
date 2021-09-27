---
title: Verschlüsseln von Ansprüchen auf persistente Volumes mit einem kundenseitig verwalteten Schlüssel (CMK) in Azure Red Hat OpenShift (ARO)
description: Bring Your Own Key (BYOK) / Anleitung zur Bereitstellung eines kundenseitig verwalteten Schlüssels (CMK) für Azure Red Hat OpenShift
ms.topic: article
ms.date: 02/24/2021
author: stuartatmicrosoft
ms.author: stkirk
ms.service: azure-redhat-openshift
keywords: Verschlüsselung, BYOK, ARO, CMK, OpenShift, Red Hat
ms.openlocfilehash: d5251188dfef87363846d4a19cd7ccc0d5d2976f
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128665110"
---
# <a name="encrypt-persistent-volume-claims-with-a-customer-managed-key-cmk-on-azure-red-hat-openshift-aro-preview"></a>Verschlüsseln von Ansprüchen auf persistente Volumes mit einem kundenseitig verwalteten Schlüssel (CMK) in Azure Red Hat OpenShift (ARO) (Vorschau)

Azure Storage verwendet serverseitige Verschlüsselung (Server Side Encryption, SSE), um Ihre Daten beim Speichern in der Cloud automatisch zu [verschlüsseln](../storage/common/storage-service-encryption.md). Standardmäßig werden Daten mit von der Microsoft-Plattform verwalteten Schlüsseln verschlüsselt. Um zusätzliche Kontrolle über die Verschlüsselungsschlüssel zu erhalten, können Sie eigene kundenseitig verwaltete Schlüssel zum Verschlüsseln von Daten in Ihren Azure Red Hat OpenShift-Clustern bereitstellen.

> [!NOTE]
> Zu diesem Zeitpunkt wird nur die Verschlüsselung persistenter Volumes in ARO mit kundenseitig verwalteten Schlüsseln unterstützt. Diese Funktion steht derzeit nicht für Betriebssystemdatenträger für Master- oder Workerknoten zur Verfügung.

> [!IMPORTANT]
> ARO-Vorschaufunktionen stehen gemäß dem Self-Service- und Aktivierungsprinzip zur Verfügung. Vorschauversionen werden „wie besehen“ und „wie verfügbar“ bereitgestellt und sind von den Vereinbarungen zum Service Level und der eingeschränkten Garantie ausgeschlossen. ARO-Vorschauversionen werden teilweise vom Kundensupport auf Grundlage der bestmöglichen Leistung abgedeckt. Daher sind diese Funktionen nicht für die Verwendung in der Produktion vorgesehen.

## <a name="before-you-begin"></a>Vorbereitung
In diesem Artikel wird Folgendes vorausgesetzt:

* Sie verfügen bereits über einen ARO-Cluster mit OpenShift-Version 4.4 (oder höher).

* Das OpenShift-Befehlszeilentool **oc**, base64 (Bestandteil von coreutils) und die Azure CLI **az** sind installiert.

* Sie sind bei Ihrem ARO-Cluster über **oc** als globaler Clusteradministrator (kubeadmin) angemeldet.

* Sie sind bei der Azure CLI über **az** mit einem Konto angemeldet, das dazu autorisiert ist, Zugriff vom Typ „Mitwirkender“ in demselben Abonnement wie der ARO-Cluster zu gewähren.

## <a name="limitations"></a>Einschränkungen

* Die Verfügbarkeit der Verschlüsselung mit kundenseitig verwalteten Schlüsseln ist regionsspezifisch. Um den Status für eine bestimmte Azure-Region zu ermitteln, überprüfen Sie die [Azure-Regionen][supported-regions].
* Wenn Sie Ultra Disks verwenden möchten, müssen Sie diese zunächst in Ihrem Abonnement aktivieren, bevor Sie beginnen.

## <a name="declare-cluster--encryption-variables"></a>Deklarieren von Cluster- und Verschlüsselungsvariablen
Sie sollten die unten aufgeführten Variablen auf die entsprechenden Einstellungen für den ARO-Cluster festlegen, für den Sie kundenseitig verwaltete Verschlüsselungsschlüssel aktivieren möchten:
```
aroCluster="mycluster"             # The name of the ARO cluster that you wish to enable CMK on. This may be obtained from **az aro list -o table**
buildRG="mycluster-rg"             # The name of the resource group used when you initially built the ARO cluster. This may be obtained from **az aro list -o table**
desName="aro-des"                  # Your Azure Disk Encryption Set name. This must be unique in your subscription.
vaultName="aro-keyvault-1"         # Your Azure Key Vault name. This must be unique in your subscription.
vaultKeyName="myCustomAROKey"      # The name of the key to be used within your Azure Key Vault. This is the name of the key, not the actual value of the key that you will rotate.
```

## <a name="obtain-your-subscription-id"></a>Abrufen Ihrer Abonnement-ID
Ihre Azure-Abonnement-ID wird bei der Konfiguration von CMK mehrmals verwendet. Rufen Sie die ID ab, und speichern Sie sie als Variable:
```azurecli-interactive
# Obtain your Azure Subscription ID and store it in a variable
subId="$(az account list -o tsv | grep True | awk '{print $3}')"
```

## <a name="create-an-azure-key-vault-instance"></a>Erstellen einer Azure Key Vault-Instanz
Es muss eine Azure Key Vault-Instanz zum Speichern Ihrer Schlüssel verwendet werden. Erstellen Sie eine neue Key Vault-Instanz mit aktiviertem Löschschutz. Dann erstellen Sie einen neuen Schlüssel innerhalb des Tresors zum Speichern Ihres eigenen benutzerdefinierten Schlüssels:

```azurecli-interactive
# Create an Azure Key Vault resource in a supported Azure region
az keyvault create -n $vaultName -g $buildRG --enable-purge-protection true -o table

# Create the actual key within the Azure Key Vault
az keyvault key create --vault-name $vaultName --name $vaultKeyName --protection software -o jsonc
```

## <a name="create-an-azure-disk-encryption-set"></a>Erstellen eines Azure-Datenträgerverschlüsselungssatzes

Der Azure-Datenträgerverschlüsselungssatz wird als Bezugspunkt für Datenträger in ARO verwendet. Er ist mit der im vorherigen Schritt erstellten Azure Key Vault-Instanz verbunden und ruft kundenseitig verwaltete Schlüssel von diesem Speicherort ab.

```azurecli-interactive
# Retrieve the Key Vault Id and store it in a variable
keyVaultId="$(az keyvault show --name $vaultName --query [id] -o tsv)"

# Retrieve the Key Vault key URL and store it in a variable
keyVaultKeyUrl="$(az keyvault key show --vault-name $vaultName --name $vaultKeyName  --query [key.kid] -o tsv)"

# Create an Azure disk encryption set
az disk-encryption-set create -n $desName -g $buildRG --source-vault $keyVaultId --key-url $keyVaultKeyUrl -o table
```

## <a name="grant-the-disk-encryption-set-access-to-key-vault"></a>Gewähren des Zugriffs auf Key Vault für den Datenträgerverschlüsselungssatz
Verwenden Sie den im vorherigen Schritt erstellten Datenträgerverschlüsselungssatz, und gewähren Sie ihm Zugriff auf Azure Key Vault:

```azurecli-interactive
# First, find the disk encryption set's Azure Application ID value.
desIdentity="$(az disk-encryption-set show -n $desName -g $buildRG --query [identity.principalId] -o tsv)"

# Next, update the Key Vault security policy settings to allow access to the disk encryption set.
az keyvault set-policy -n $vaultName -g $buildRG --object-id $desIdentity --key-permissions wrapkey unwrapkey get -o table

# Now, ensure the Disk Encryption Set can read the contents of the Azure Key Vault.
az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId -o jsonc
```

### <a name="obtain-other-ids-required-for-role-assignments"></a>Abrufen weiterer IDs, die für Rollenzuweisungen erforderlich sind
Dem ARO-Cluster muss gestattet werden, den Datenträgerverschlüsselungssatz zum Verschlüsseln der Ansprüche auf persistente Volumes (Persistent Volume Claims, PVCs) im ARO-Cluster zu verwenden. Dazu wird eine neue verwaltete Dienstidentität (Managed Service Identity, MSI) erstellt. Außerdem werden weitere Berechtigungen für die ARO-MSI und den Datenträgerverschlüsselungssatz festgelegt.

```azurecli-interactive
# First, get the Azure Application ID of the service principal used in the ARO cluster.
aroSPAppId="$(az aro show -n $aroCluster -g $buildRG -o tsv --query servicePrincipalProfile.clientId)"

# Next, get the object ID of the service principal used in the ARO cluster.
aroSPObjId="$(az ad sp show --id $aroSPAppId -o tsv --query [objectId])"

# Set the name of the ARO Managed Service Identity. 
msiName="$aroCluster-msi"

# Create the Managed Service Identity (MSI) required for disk encryption.
az identity create -g $buildRG -n $msiName -o jsonc

# Get the ARO Managed Service Identity Azure Application ID.
aroMSIAppId="$(az identity show -n $msiName -g $buildRG -o tsv --query [clientId])"

# Get the resource ID for the disk encryption set and the Key Vault resource group.
buildRGResourceId="$(az group show -n $buildRG -o tsv --query [id])"
```

### <a name="implement-other-role-assignments-required-for-cmk-encryption"></a>Implementieren weiterer Rollenzuweisungen, die für die CMK-Verschlüsselung erforderlich sind
Wenden Sie die erforderlichen Rollenzuweisungen mithilfe der im vorherigen Schritt abgerufenen Variablen an:

```azurecli-interactive
# Ensure the Azure Disk Encryption Set can read the contents of the Azure Key Vault.
az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId -o jsonc

# Assign the MSI AppID 'Reader' permission over the disk encryption set & Key Vault resource group.
az role assignment create --assignee $aroMSIAppId --role Reader --scope $buildRGResourceId -o jsonc

# Assign the ARO Service Principal 'Contributor' permission over the disk encryption set & Key Vault Resource Group.
az role assignment create --assignee $aroSPObjId --role Contributor --scope $buildRGResourceId -o jsonc
```

## <a name="create-a-k8s-storage-class-for-encrypted-premium--ultra-disks"></a>Erstellen einer k8s-Speicherklasse für verschlüsselte Premium- und Ultra-Datenträger
Generieren Sie Speicherklassen, die für CMK für Premium_LRS-und UltraSSD_LRS-Datenträger verwendet werden sollen:

```azurecli-interactive
# Premium Disks
cat > managed-premium-encrypted-cmk.yaml<< EOF
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: managed-premium-encrypted-cmk
provisioner: kubernetes.io/azure-disk
parameters:
  skuname: Premium_LRS
  kind: Managed
  diskEncryptionSetID: "/subscriptions/$subId/resourceGroups/$buildRG/providers/Microsoft.Compute/diskEncryptionSets/$desName"
  resourceGroup: $buildRG
reclaimPolicy: Delete
allowVolumeExpansion: true
volumeBindingMode: WaitForFirstConsumer
EOF

# Ultra Disks
cat > managed-ultra-encrypted-cmk.yaml<< EOF
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: managed-ultra-encrypted-cmk
provisioner: kubernetes.io/azure-disk
parameters:
  skuname: UltraSSD_LRS
  kind: Managed
  diskEncryptionSetID: "/subscriptions/$subId/resourceGroups/$buildRG/providers/Microsoft.Compute/diskEncryptionSets/$desName"
  resourceGroup: $buildRG
  cachingmode: None
  diskIopsReadWrite: "2000"  # minimum value: 2 IOPS/GiB
  diskMbpsReadWrite: "320"   # minimum value: 0.032/GiB
reclaimPolicy: Delete
allowVolumeExpansion: true
volumeBindingMode: WaitForFirstConsumer
EOF
```

Anschließend führen Sie diese Bereitstellung in Ihrem ARO-Cluster aus, um die Speicherklassenkonfiguration anzuwenden:

```azurecli-interactive
# Update cluster with the new storage classes
oc apply -f managed-premium-encrypted-cmk.yaml
oc apply -f managed-ultra-encrypted-cmk.yaml
```

## <a name="test-encryption-with-customer-managed-keys-optional"></a>Testen der Verschlüsselung mit kundenseitig verwalteten Schlüsseln (optional)
Um zu überprüfen, ob Ihr Cluster einen kundenseitig verwalteten Schlüssel für die PVC-Verschlüsselung verwendet, erstellen Sie einen Anspruch auf persistentes Volume mithilfe der neuen Speicherklasse. Der folgende Codeausschnitt erstellt einen Pod und stellt einen Anspruch auf persistentes Volume mithilfe von Premium-Datenträgern bereit.
```
# Create a pod which uses a persistent volume claim referencing the new storage class
cat > test-pvc.yaml<< EOF
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mypod-with-cmk-encryption-pvc
spec:
  accessModes:
  - ReadWriteOnce
  storageClassName: managed-premium-encrypted-cmk
  resources:
    requests:
      storage: 1Gi
---
kind: Pod
apiVersion: v1
metadata:
  name: mypod-with-cmk-encryption
spec:
  containers:
  - name: mypod-with-cmk-encryption
    image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    volumeMounts:
    - mountPath: "/mnt/azure"
      name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: mypod-with-cmk-encryption-pvc
EOF
```
### <a name="apply-the-test-pod-configuration-file-optional"></a>Anwenden der Konfigurationsdatei für den Testpod (optional)
Führen Sie die unten aufgeführten Befehle aus, um die Konfiguration für den Testpod anzuwenden und die UID des neuen Anspruchs auf persistentes Volume zurückzugeben. Anhand der UID wird geprüft, ob der Datenträger mit CMK verschlüsselt ist.
```azurecli-interactive
# Apply the test pod configuration file and set the PVC UID as a variable to query in Azure later.
pvcUid="$(oc apply -f test-pvc.yaml -o jsonpath='{.items[0].metadata.uid}')"

# Determine the full Azure Disk name.
pvName="$(oc get pv pvc-$pvcUid -o jsonpath='{.spec.azureDisk.diskName}')"
```
> [!NOTE]
> Gelegentlich kann es beim Anwenden von Rollenzuweisungen in Azure Active Directory zu einer geringfügigen Verzögerung kommen. Je nach Geschwindigkeit, in der diese Anweisungen ausgeführt werden, ist der Befehl zum Ermitteln des vollständigen Azure-Datenträgernamens möglicherweise nicht erfolgreich. Überprüfen Sie in diesem Fall die Ausgabe von **oc describe pvc mypod-with-cmk-encryption-pvc**, um sicherzustellen, dass der Datenträger erfolgreich bereitgestellt wurde. Wenn die Weitergabe der Rollenzuweisung nicht abgeschlossen ist, müssen Sie die Pod- und PVC-YAML-Datei *löschen* und erneut *anwenden*.

### <a name="verify-pvc-disk-is-configured-with-encryptionatrestwithcustomerkey-optional"></a>Überprüfen, ob der PVC-Datenträger mit „EncryptionAtRestWithCustomerKey“ konfiguriert ist (optional)
Der Pod sollte einen Anspruch auf persistentes Volume erstellen, der auf die CMK-Speicherklasse verweist. Durch Ausführen des folgenden Befehls wird geprüft, ob der PVC wie erwartet bereitgestellt wurde:
```azurecli-interactive
# Describe the OpenShift cluster-wide persistent volume claims
oc describe pvc

# Verify with Azure that the disk is encrypted with a customer-managed key
az disk show -n $pvName -g $buildRG -o json --query [encryption]
```

<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[best-practices-security]: ../aks/operator-best-practices-cluster-security.md
[byok-azure-portal]: ../storage/common/customer-managed-keys-configure-key-vault.md
[customer-managed-keys]: ../virtual-machines/disk-encryption.md#customer-managed-keys
[key-vault-generate]: ../key-vault/general/manage-with-cli2.md
[supported-regions]: ../virtual-machines/disk-encryption.md#supported-regions
