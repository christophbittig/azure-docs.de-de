---
title: Problembehandlung für Azure Key Vault-Anbieter für Secrets Store CSI-Treiber in Azure Kubernetes Service (AKS)
description: Erfahren Sie, wie Sie den Azure Key Vault Anbiete für Secrets Store CSI-Treiber verwenden, um Geheimnisse in Azure Kubernetes Service (AKS) zu integrieren.
author: nickomang
ms.author: nickoman
ms.service: container-service
ms.topic: how-to
ms.date: 10/13/2021
ms.custom: template-how-to, devx-track-azurecli
ms.openlocfilehash: cdab72e0a1633aa75a5594acf9b506d944f0eaa0
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2021
ms.locfileid: "131893005"
---
# <a name="use-the-azure-key-vault-provider-for-secrets-store-csi-driver-in-an-azure-kubernetes-service-aks-cluster"></a>Verwenden Sie den Azure Key Vault Anbieter für Secrets Store CSI-Treiber in einem Azure Kubernetes Service(AKS)-Cluster

Der Secrets Store CSI-Treiber für Kubernetes ermöglicht, Azure Key Vault über ein [CSI-Volume][kube-csi] als Geheimnisspeicher in einen Kubernetes-Cluster zu integrieren.

## <a name="prerequisites"></a>Voraussetzungen

- Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

- Bevor Sie beginnen, stellen Sie sicher, dass Ihre Azure CLI-Version >=`2.30.0` oder höher ist, oder [installieren Sie die neueste Version](/cli/azure/install-azure-cli).

### <a name="supported-kubernetes-versions"></a>Unterstützte Kubernetes-Versionen

Die empfohlene Mindestversion von Kubernetes basiert auf dem [rollierendem Kubernetes-Versionsunterstützungsfenster.][kubernetes-version-support] Stellen Sie sicher, dass N-2 oder eine neuere Version ausgeführt wird.

## <a name="features"></a>Features

- Einbinden von Geheimnissen, Schlüsseln und Zertifikaten in einen Pod mithilfe eines CSI-Volumes
- Unterstützt CSI-Inlinevolumes
- Unterstützung für das Einbinden mehrerer Geheimnisspeicherobjekte als einzelnes Volume
- Unterstützung der Podportabilität mit der SecretProviderClass-CRD
- Unterstützt Windows-Container.
- Synchronisieren mit Kubernetes-Geheimnissen
- Unterstützung für die automatische Rotation eingebundener Inhalte und synchronisierter Kubernetes-Geheimnisse

## <a name="create-an-aks-cluster-with-azure-key-vault-provider-for-secrets-store-csi-driver-support"></a>Erstellen eines AKS-Clusters mit Azure Key Vault Provider für Secrets Store CSI-Treiberunterstützung

Erstellen Sie als Erstes eine Azure-Ressourcengruppe:

```azurecli-interactive
az group create -n myResourceGroup -l eastus2
```

Verwenden Sie den Befehl [az aks create][az-aks-create] mit dem Add-On `azure-keyvault-secrets-provider`, um einen AKS-Cluster mit Azure Key Vault Provider für den Secrets Store CSI-Treiber zu erstellen.

```azurecli-interactive
az aks create -n myAKSCluster -g myResourceGroup --enable-addons azure-keyvault-secrets-provider --enable-managed-identity
```

Das Add-On erstellt eine benutzerseitig zugewiesene verwaltete Identität mit dem Namen `azurekeyvaultsecretsprovider-*` für den Zugriff auf Azure-Ressourcen. In diesem Beispiel verwenden wir diese Identität, um eine Verbindung mit dem Azure Key Vault herzustellen, in dem unsere Geheimnisse gespeichert werden, doch andere [Methoden für den Identitätszugriff][identity-access-methods] können verwendet werden. Notieren Sie sich die in der Ausgabe angezeigte `clientId` der Identität:

```json
...,
 "addonProfiles": {
    "azureKeyvaultSecretsProvider": {
      ...,
      "identity": {
        "clientId": "<client-id>",
        ...
      }
    }
```

## <a name="upgrade-an-existing-aks-cluster-with-azure-key-vault-provider-for-secrets-store-csi-driver-support"></a>Upgrade eines vorhandenen AKS-Clusters mit Azure Key Vault Provider für Secrets Store CSI-Treiberunterstützung

Verwenden Sie zum Aktualisieren eines vorhandenen AKS-Clusters mit Azure Key Vault Provider für Secrets Store CSI Driver-Funktion den Befehl [az aks enable-addons][az-aks-enable-addons] mit dem Add-On `azure-keyvault-secrets-provider`:

```azurecli-interactive
az aks enable-addons --addons azure-keyvault-secrets-provider --name myAKSCluster --resource-group myResourceGroup
```

Wie oben erwähnt, erstellt das Add-On eine benutzerseitig zugewiesene verwaltete Identität, die zur Authentifizierung bei Azure Key Vault verwendet werden kann.

## <a name="verify-azure-key-vault-provider-for-secrets-store-csi-driver-installation"></a>Überprüfen des Azure Key Vault-Anbieters für Secrets Store CSI-Treiberinstallation

Mit den oben angegebenen Befehlen werden der Secrets Store CSI-Treiber und der Azure Key Vault-Anbieter auf Ihren Knoten installiert. Überprüfen Sie den Abschluss, indem Sie alle Pods mit den Bezeichnunen `secrets-store-csi-driver` und `secrets-store-provider-azure`  im Namespace kube-system auflisten und sicherstellen, dass Ihre Ausgabe ähnlich wie folgt aussieht:

```bash
kubectl get pods -n kube-system -l 'app in (secrets-store-csi-driver, secrets-store-provider-azure)'

NAME                                     READY   STATUS    RESTARTS   AGE
aks-secrets-store-csi-driver-4vpkj       3/3     Running   2          4m25s
aks-secrets-store-csi-driver-ctjq6       3/3     Running   2          4m21s
aks-secrets-store-csi-driver-tlvlq       3/3     Running   2          4m24s
aks-secrets-store-provider-azure-5p4nb   1/1     Running   0          4m21s
aks-secrets-store-provider-azure-6pqmv   1/1     Running   0          4m24s
aks-secrets-store-provider-azure-f5qlm   1/1     Running   0          4m25s
```

Stellen Sie sicher, dass auf jedem Knoten in den Knotenpools Ihres Clusters ein Secrets Store CSI-Treiberpod und ein Azure Key Vault Anbietet-Pod ausgeführt werden.

## <a name="create-or-use-an-existing-azure-key-vault"></a>Erstellen einer Azure Key Vault-Instanz oder Verwenden einer vorhandenen

Zusätzlich zu einem AKS-Cluster benötigen Sie eine Azure Key Vault-Ressource, die den Inhalt des Geheimnisses enthält. Beachten Sie, dass der Key Vault-Name global eindeutig sein muss.

```azurecli
az keyvault create -n <keyvault-name> -g myResourceGroup -l eastus2
```

Azure Key Vault kann Schlüssel, Geheimnisse und Zertifikate speichern. In diesem Beispiel legen wir ein Nur-Text-Geheimnis mit dem Namen `ExampleSecret` fest:

```azurecli
az keyvault secret set --vault-name <keyvault-name> -n ExampleSecret --value MyAKSExampleSecret
```

Notieren Sie sich die folgenden Eigenschaften für die Verwendung im nächsten Abschnitt:

- Name des Geheimnisobjekts in Key Vault
- Objekttyp (Geheimnis, Schlüssel oder Zertifikat)
- Name Ihrer Azure Key Vault-Ressource
- Azure-Mandanten-ID, zu der das Abonnement gehört

## <a name="provide-identity-to-access-azure-key-vault"></a>Bereitstellen einer Identität für den Zugriff auf Azure Key Vault

Der Secrets Store CSI-Treiber ermöglicht den Zugriff auf eine Azure Key Vault-Instanz:
- [Azure Active Directory Podidentität][aad-pod-identity]
- Vom Benutzer oder vom System zugewiesene verwaltete Identität

Führen Sie die Schritte zum [Bereitstellen einer Identität für den Zugriff auf Azure Key Vault][identity-access-methods] für die ausgewählte Methode aus.

## <a name="validate-the-secrets"></a>Überprüfen der Geheimnisse

Nachdem der Pod gestartet wurde, ist der eingebundene Inhalt unter dem in Ihrer Bereitstellungs-YAML angegebenen Volumepfad verfügbar.

```Bash
## show secrets held in secrets-store
kubectl exec busybox-secrets-store-inline -- ls /mnt/secrets-store/

## print a test secret 'ExampleSecret' held in secrets-store
kubectl exec busybox-secrets-store-inline -- cat /mnt/secrets-store/ExampleSecret
```

## <a name="obtaining-certificates-and-keys"></a>Abrufen von Zertifikaten und Schlüsseln

Azure Key Vault unterscheidet stark zwischen Schlüsseln, Geheimnissen und Zertifikaten. Die Zertifikatsfunktionen des Key Vault-Dienstes wurden unter Verwendung seiner Schlüssel- und Geheimhaltungsfunktionen entwickelt. Wenn ein Key Vault-Zertifikat erstellt wird, werden auch ein adressierbarer Schlüssel und ein Geheimnis gleichen Namens erstellt. Der Key Vault-Schlüssel ermöglicht Schlüsselvorgänge, und das Key Vault-Geheimnis ermöglicht den Abruf des Zertifikatwerts als Geheimnis. Ein Key Vault-Zertifikat enthält auch öffentliche X509-Zertifikatmetadaten. Azure Key Vault speichert sowohl den öffentlichen als auch den privaten Teil Ihres Zertifikats in einem Geheimnis. Jede einzelne Komponente kann durch Angabe von `objectType` in Ihrer SecretProviderClass ermittelt werden. Die folgende Tabelle zeigt, welches Objekt den verschiedenen Ressourcen zugeordnet ist, die mit Ihrem Zertifikat verbunden sind:

|Object|Rückgabewert|Zeigt die gesamte Zertifikatskette an|
|---|---|---|
|`key`|Der öffentliche Schlüssel im PEM-Format|–|
|`cert`|Das Zertifikat im PEM-Format|Nein|
|`secret`|Der private Schlüssel und das Zertifikat im PEM-Format|Ja|

## <a name="disable-azure-key-vault-provider-for-secrets-store-csi-driver-on-an-existing-aks-cluster"></a>Deaktivieren eines Azure Key Vault-Anbieters für Secrets Store CSI-Treiber in einem vorhandenen AKS-Cluster

> [!NOTE]
> Stellen Sie vor dem Deaktivieren des Add-Ons sicher, dass kein `SecretProviderClass` verwendet wird. Der Versuch, das Add-On zu deaktivieren, während ein `SecretProviderClass` vorhanden ist, führt zu einem Fehler.

Verwenden Sie den Befehl [az aks disable-addons][az-aks-disable-addons] mit dem Flag `azure-keyvault-secrets-provider`, um die Funktionen des Azure Key Vault-Anbieters für den Secrets Store CSI-Treiber in einem vorhandenen Cluster zu deaktivieren:

```azurecli-interactive
az aks disable-addons --addons azure-keyvault-secrets-provider -g myResourceGroup -n myAKSCluster
```

> [!NOTE]
> Wenn das Addon deaktiviert ist, gibt es für bestehende Workloads keine Probleme, und die eingebrachten Geheimnisse werden nicht aktualisiert. Wenn der Pod neu gestartet wird oder ein neuer Pod im Rahmen des Ereignisses zum hochskalieren erstellt wird, kann der Pod nicht gestartet werden, da der Treiber nicht mehr ausgeführt wird.

## <a name="additional-configuration-options"></a>Zusätzliche Konfigurationsoptionen

### <a name="enabling-and-disabling-autorotation"></a>Aktivieren und Deaktivieren von automatischen Rotation

> [!NOTE]
> Wenn diese Option aktiviert ist, aktualisiert der Azure Key Vault-Anbieter für Secrets Store CSI-Treiber die Podbereitstellung und das Kubernetes-Geheimnis, die in secretObjects der SecretProviderClass definiert sind, indem er basierend auf dem definierten Rotations-Abrufintervall regelmäßig Änderungen abruft. Das standardmäßige Rotations-Abrufintervall beträgt 2 m.

Um die automatische Rotation von Geheimnissen zu aktivieren, verwenden Sie beim Erstellen Ihres Clusters das Flag `enable-secret-rotation`:

```azurecli-interactive
az aks create -n myAKSCluster2 -g myResourceGroup --enable-addons azure-keyvault-secrets-provider --enable-secret-rotation
```

Oder aktualisieren Sie einen vorhandenen Cluster mit aktivierten Add-Ons:

```azurecli-interactive
az aks update -g myResourceGroup -n myAKSCluster2 --enable-secret-rotation
```

Um ein benutzerdefiniertes Rotationsintervall anzugeben, verwenden Sie das Flag `rotation-poll-interval`:

```azurecli-interactive
az aks update -g myResourceGroup -n myAKSCluster2 --enable-secret-rotation --rotation-poll-interval 5m
```

Verwenden Sie zum Deaktivieren das Flag `disable-secret-rotation`:

```azurecli-interactive
az aks update -g myResourceGroup -n myAKSCluster2 --disable-secret-rotation
```

### <a name="sync-mounted-content-with-a-kubernetes-secret"></a>Synchronisieren von bereitgestelltem Inhalt mit einem Kubernetes-Geheimnis

Unter Umständen können Sie ein Kubernetes-Geheimnis erstellen, um den bereitgestellten Inhalt zu spiegeln.

Verwenden Sie beim Erstellen einer SecretProviderClass das Feld `secretObjects`, um den gewünschten Status von Kubernetes-Geheimnissen zu definieren:

> [!NOTE]
> Dies ist keine abschließende Liste. Sie müssen Änderungen an diesem Beispiel vorgeben, um die von Ihnen gewählte Methode zum Azure Key Vault Identitätszugriff zu unterstützen.

> [!NOTE]
> Die Geheimnisse werden erst synchronisiert, wenn Sie einen Pod zum Einbinden der Geheimnisse starten. Die ausschließliche Verwendung der Synchronisierung mit dem Kubernetes-Geheimnisfeature funktioniert nicht. Wenn alle Pods, die das Geheimnis verwenden, gelöscht werden, wird auch das Kubernetes-Geheimnis gelöscht.


> [!NOTE]
> Stellen Sie sicher, dass der `objectName` in `secretObjects` mit dem Dateinamen des eingebundenen Inhalts übereinstimmt. Wenn stattdessen `objectAlias` verwendet wird, sollte Übereinstimmung mit dem Objektalias bestehen.

```yml
apiVersion: secrets-store.csi.x-k8s.io/v1
kind: SecretProviderClass
metadata:
  name: azure-sync
spec:
  provider: azure                             
  secretObjects:                              # [OPTIONAL] SecretObject defines the desired state of synced K8s secret objects
  - data:
    - key: username                           # data field to populate
      objectName: foo1                        # name of the mounted content to sync. this could be the object name or the object alias
    secretName: foosecret                     # name of the Kubernetes Secret object
    type: Opaque                              # type of the Kubernetes Secret object e.g. Opaque, kubernetes.io/tls
```

#### <a name="set-environment-variables-to-reference-kubernetes-secrets"></a>Festlegen von Umgebungsvariablen zum Verweisen auf Kubernetes-Geheimnisse

Nachdem das Kubernetes-Geheimnis erstellt wurde, können Sie eine Umgebungsvariable in Ihrem Pod festlegen, die auf das Kubernetes-Geheimnis verweist:

> [!NOTE]
> Dies ist keine abschließende Liste. Sie müssen Änderungen an diesem Beispiel vorgeben, um die von Ihnen gewählte Methode zum Azure Key Vault Identitätszugriff zu unterstützen.

```yml
kind: Pod
apiVersion: v1
metadata:
  name: busybox-secrets-store-inline
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
      env:
      - name: SECRET_USERNAME
        valueFrom:
          secretKeyRef:
            name: foosecret
            key: username
  volumes:
    - name: secrets-store01-inline
      csi:
        driver: secrets-store.csi.k8s.io
        readOnly: true
        volumeAttributes:
          secretProviderClass: "azure-sync"
```

## <a name="metrics"></a>Metriken

### <a name="azure-key-vault-provider"></a>Azure Key Vault Provider

Metriken werden über Prometheus von Port 8898 aus bereitgestellt, doch wird dieser Port nicht standardmäßig außerhalb des Pods verfügbar gemacht. Greifen Sie über Localhost auf die Metriken zu mittels `kubectl port-forward`:

```bash
kubectl port-forward -n kube-system ds/aks-secrets-store-provider-azure 8898:8898 &
curl localhost:8898/metrics
```

In der folgenden Tabelle sind die Metriken aufgeführt, die vom Azure Key Vault-Anbieter für Secrets Store CSI-Treiber bereitgestellt werden:

|Metric|BESCHREIBUNG|Tags|
|----|----|----|
|keyvault_request|Zeit, die füe das Abrufen von keyvault gebraucht wurde|`os_type=<runtime os>`, `provider=azure`, `object_name=<keyvault object name>`, `object_type=<keyvault object type>`, `error=<error if failed>`|
|grpc_request|Zeit, die für die gRPC-Anforderungen gebraucht wurde|`os_type=<runtime os>`, `provider=azure`, `grpc_method=<rpc full method>`, `grpc_code=<grpc status code>`, `grpc_message=<grpc status message>`|

### <a name="secrets-store-csi-driver"></a>Secrets Store CSI-Treiber

Metriken werden über Prometheus von Port 8095 aus bereitgestellt, doch wird dieser Port nicht standardmäßig außerhalb des Pods verfügbar gemacht. Greifen Sie über Localhost auf die Metriken zu mittels `kubectl port-forward`:

```bash
kubectl port-forward -n kube-system ds/aks-secrets-store-csi-driver 8095:8095 &
curl localhost:8095/metrics
```

In der folgenden Tabelle sind die Metriken aufgeführt, die vom Secrets Store CSI-Treiber bereitgestellt werden:

|Metric|BESCHREIBUNG|Tags|
|----|----|----|
|total_node_publish|Gesamtzahl der erfolgreichen Anforderungen für die Volume-Bereitstellung|`os_type=<runtime os>`, `provider=<provider name>`|
|total_node_unpublish|Gesamtzahl der erfolgreichen Anforderungen zum Aufheben der Volume-Bereitstellung|`os_type=<runtime os>`|
|total_node_publish_error|Gesamtanzahl von Fehlern bei Anforderungen für die Volume-Bereitstellung|`os_type=<runtime os>`, `provider=<provider name>`, `error_type=<error code>`|
|total_node_unpublish|Gesamtanzahl von Fehlern bei Anforderungen zum Aufheben der Volume-Bereitstellung|`os_type=<runtime os>`|
|total_sync_k8s_secret|Gesamtanzahl synchronisierter k8s-Geheimnisse|`os_type=<runtime os`, `provider=<provider name>`|
|sync_k8s_secret_duration_sec|Zeit, wie lange die Synchronisierung des k8s-Geheimnisses gedauert hat|`os_type=<runtime os>`|
|total_rotation_reconcile|Gesamtzahl der Rotationsabgleiche|`os_type=<runtime os>`, `rotated=<true or false>`|
|total_rotation_reconcile_error|Gesamtzahl der Rotationsabgleiche mit Fehler|`os_type=<runtime os>`, `rotated=<true or false>`, `error_type=<error code>`|
|total_rotation_reconcile_error|Zeit, die zum Rotieren von Inhalten im Geheimnisspeicher für Pods benötigt wurde|`os_type=<runtime os>`|

## <a name="next-steps"></a>Nächste Schritte
<!-- Add a context sentence for the following links -->
Nachdem Sie erfahren haben, wie Sie den Azure Key Vault-Anbieter für Secrets Store CSI-Treiber mit einem AKS-Cluster verwenden, sehen Sie sich die folgenden Ressourcen an:

- [Aktivieren von CSI-Treibern für Azure-Datenträger und Azure Files in AKS][csi-storage-drivers]

<!-- Links -->
<!-- Internal -->
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-enable-addons]: /cli/azure/aks#az_aks_enable_addons
[az-aks-disable-addons]: /cli/azure/aks#az_aks_disable_addons
[key-vault-provider]: ../key-vault/general/key-vault-integrate-kubernetes.md
[csi-storage-drivers]: ./csi-storage-drivers.md
[create-key-vault]: ../key-vault/general/quick-create-cli.md
[set-secret-key-vault]: ../key-vault/secrets/quick-create-portal.md
[aks-managed-identity]: ./use-managed-identity.md
[identity-access-methods]: ./csi-secrets-store-identity-access.md
[aad-pod-identity]: ./use-azure-ad-pod-identity.md
[kubernetes-version-support]: ./supported-kubernetes-versions.md?tabs=azure-cli#kubernetes-version-support-policy

<!-- External -->
[kube-csi]: https://kubernetes-csi.github.io/docs/
[key-vault-provider-install]: https://azure.github.io/secrets-store-csi-driver-provider-azure/getting-started/installation
[sample-secret-provider-class]: https://azure.github.io/secrets-store-csi-driver-provider-azure/getting-started/usage/#create-your-own-secretproviderclass-object
