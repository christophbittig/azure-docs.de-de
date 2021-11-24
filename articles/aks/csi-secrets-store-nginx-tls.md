---
title: Einrichten des CSI-Treibers für den Geheimnisspeicher zum Aktivieren des NGINX-Eingangsdatencontrollers mit TLS für Azure Kubernetes Service
description: Konfigurieren des CSI-Treibers für den Geheimnisspeicher zum Aktivieren des NGINX-Eingangsdatencontrollers mit TLS für Azure Kubernetes Service (AKS).
author: nickomang
ms.author: nickoman
ms.service: container-service
ms.topic: how-to
ms.date: 10/19/2021
ms.custom: template-how-to
ms.openlocfilehash: dd6945da0b3c4170082d20e5481d06048b7c3dde
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132284795"
---
# <a name="set-up-secrets-store-csi-driver-to-enable-nginx-ingress-controller-with-tls"></a>Einrichten des CSI-Treibers für den Geheimnisspeicher zum Aktivieren des NGINX-Eingangsdatencontrollers mit TLS

Dieser Artikel führt Sie schrittweise durch den Prozess zum Sichern eines NGINX-Eingangsdatencontrollers mit TLS mit einem AKS-Cluster (Azure Kubernetes Service) und einer Azure Key Vault-Instanz (AKV). Weitere Informationen finden Sie unter [TLS in Kubernetes][kubernetes-ingress-tls].

Das Importieren des TLS-Eingangszertifikats in den Cluster kann mit einer von zwei Methoden durchgeführt werden:

- **Anwendung**: Das Anwendungsbereitstellungsmanifest deklariert das Anbietervolume und bindet es ein. Nur wenn die Anwendung bereitgestellt wird, wird das Zertifikat im Cluster verfügbar gemacht. Wenn die Anwendung entfernt wird, wird auch das Geheimnis entfernt. Dieses Szenario eignet sich für Bereitstellungsteams, die für die Sicherheitsinfrastruktur der Anwendung und ihre Integration in den Cluster verantwortlich sind.
- **Eingangsdatencontroller**: Die Eingangsbereitstellung wird so geändert, dass das Anbietervolume deklariert und eingebunden wird. Das Geheimnis wird importiert, wenn Eingangspods erstellt werden. Die Pods der Anwendung besitzen keinen Zugriff auf das TLS-Zertifikat. Dieses Szenario eignet sich für Situationen, in denen ein Team (d. h. die IT) Infrastruktur- und Netzwerkkomponenten (einschließlich HTTPS-TLS-Zertifikate) verwaltet und andere Teams den Anwendungslebenszyklus verwalten. In diesem Fall ist der Dateneingang spezifisch für einen einzelnen Namespace bzw. eine einzelne Workload und wird im selben Namespace wie die Anwendung bereitgestellt.

## <a name="prerequisites"></a>Voraussetzungen

- Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.
- Bevor Sie beginnen, stellen Sie sicher, dass Ihre Azure CLI-Version `2.30.0` oder höher ist, oder [installieren Sie die neueste Version](/cli/azure/install-azure-cli).
- Ein AKS-Cluster, für den der CSI-Treiber des Geheimnisspeichers konfiguriert ist.
- Eine Azure Key Vault-Instanz.

## <a name="generate-a-tls-certificate"></a>Generieren eines TLS-Zertifikats

```bash
export CERT_NAME=ingresscert
openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
    -out ingress-tls.crt \
    -keyout ingress-tls.key \
    -subj "/CN=demo.test.com/O=ingress-tls"
```

### <a name="import-the-certificate-to-akv"></a>Importieren des Zertifikats in AKV

```bash
export AKV_NAME="[YOUR AKV NAME]"
openssl pkcs12 -export -in ingress-tls.crt -inkey ingress-tls.key  -out $CERT_NAME.pfx
# skip Password prompt
```

```azurecli-interactive
az keyvault certificate import --vault-name $AKV_NAME -n $CERT_NAME -f $CERT_NAME.pfx
```

## <a name="deploy-a-secretproviderclass"></a>Bereitstellen einer SecretProviderClass

Erstellen Sie zunächst einen neuen Namespace:

```bash
export NAMESPACE=ingress-test
```

```azurecli-interactive
kubectl create ns $NAMESPACE
```

Wählen Sie eine [Methode aus, um eine Zugriffsidentität bereitzustellen][csi-ss-identity-access], und konfigurieren Sie Ihre SecretProviderClass-YAML-Datei entsprechend. Darüber hinaus gilt:

- Achten Sie darauf, `objectType=secret` zu verwenden, da dies die einzige Möglichkeit ist, den privaten Schlüssel und das Zertifikat aus AKV abzurufen.
- Legen Sie `kubernetes.io/tls` als `type` in Ihrem Abschnitt `secretObjects` fest.

Im Folgenden finden Sie ein Beispiel, wie Ihre SecretProviderClass aussehen kann:

```yml
apiVersion: secrets-store.csi.x-k8s.io/v1
kind: SecretProviderClass
metadata:
  name: azure-tls
spec:
  provider: azure
  secretObjects:                            # secretObjects defines the desired state of synced K8s secret objects
  - secretName: ingress-tls-csi
    type: kubernetes.io/tls
    data: 
    - objectName: $CERT_NAME
      key: tls.key
    - objectName: $CERT_NAME
      key: tls.crt
  parameters:
    usePodIdentity: "false"
    keyvaultName: $AKV_NAME                 # the name of the AKV instance
    objects: |
      array:
        - |
          objectName: $CERT_NAME
          objectType: secret
    tenantId: $TENANT_ID                    # the tenant ID of the AKV instance
```

Wenden Sie die SecretProviderClass auf Ihren Kubernetes-Cluster an:

```bash
kubectl apply -f secretProviderClass.yaml -n $NAMESPACE
```

## <a name="deploy-the-ingress-controller"></a>Bereitstellen des Eingangsdatencontrollers

### <a name="add-the-official-ingress-chart-repository"></a>Hinzufügen des offiziellen Repositorys für das Eingangsdiagramm

```bash
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
helm repo update
```

### <a name="configure-and-deploy-the-nginx-ingress"></a>Konfigurieren und Bereitstellen des NGINX-Eingangsdatencontrollers

Wie bereits erwähnt, können Sie je nach Szenario das Zertifikat entweder an die Anwendung oder an den Eingangsdatencontroller binden. Befolgen Sie die Anweisungen unten gemäß Ihrer Auswahl:

#### <a name="bind-certificate-to-application"></a>Binden eines Zertifikats an eine Anwendung

Die Bereitstellung der Anwendung verweist auf den Azure Key Vault-Anbieter des CSI-Treibers des Geheimnisspeichers.

```bash
helm install ingress-nginx/ingress-nginx --generate-name \
    --namespace $NAMESPACE \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

#### <a name="bind-certificate-to-ingress-controller"></a>Binden eines Zertifikats an den Eingangsdatencontroller

Die Bereitstellung des Eingangsdatencontrollers verweist auf den Azure Key Vault-Anbieter des CSI-Treibers des Geheimnisspeichers.

> [!NOTE]
> Wenn Sie nicht die Azure Active Directory-Podidentität (AAD) als Zugriffsmethode verwenden, entfernen Sie die Zeile mit `--set controller.podLabels.aadpodidbinding=$AAD_POD_IDENTITY_NAME`.

```bash
helm install ingress-nginx/ingress-nginx --generate-name \
    --namespace $NAMESPACE \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set controller.podLabels.aadpodidbinding=$AAD_POD_IDENTITY_NAME \
    -f - <<EOF
controller:
  extraVolumes:
      - name: secrets-store-inline
        csi:
          driver: secrets-store.csi.k8s.io
          readOnly: true
          volumeAttributes:
            secretProviderClass: "azure-tls"
  extraVolumeMounts:
      - name: secrets-store-inline
        mountPath: "/mnt/secrets-store"
        readOnly: true
EOF
```

Überprüfen Sie, ob das Kubernetes-Geheimnis erstellt wurde:

```bash
kubectl get secret -n $NAMESPACE

NAME                                             TYPE                                  DATA   AGE
ingress-tls-csi                                  kubernetes.io/tls                     2      1m34s
```

## <a name="deploy-the-application"></a>Bereitstellen der Anwendung

Auch hier ändern sich die Anweisungen je nach Szenario geringfügig. Befolgen Sie die Anweisungen für das Szenario, das Sie bisher ausgewählt haben:

### <a name="deploy-the-application-using-an-application-reference"></a>Bereitstellen der Anwendung mithilfe eines Anwendungsverweises

Erstellen Sie eine Datei namens `deployment.yaml` mit folgendem Inhalt:

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: busybox-one
  labels:
    app: busybox-one
spec:
  replicas: 1
  selector:
    matchLabels:
      app: busybox-one
  template:
    metadata:
      labels:
        app: busybox-one
    spec:
      containers:
      - name: busybox
        image: k8s.gcr.io/e2e-test-images/busybox:1.29-1
        command:
          - "/bin/sleep"
          - "10000"
        volumeMounts:
        - name: secrets-store-inline
          mountPath: "/mnt/secrets-store"
          readOnly: true
      volumes:
        - name: secrets-store-inline
          csi:
            driver: secrets-store.csi.k8s.io
            readOnly: true
            volumeAttributes:
              secretProviderClass: "azure-tls"
---
apiVersion: v1
kind: Service
metadata:
  name: busybox-one
spec:
  type: ClusterIP
  ports:
  - port: 80
  selector:
    app: busybox-one
```

Wenden Sie sie auf Ihren Cluster an:

```bash
kubectl apply -f deployment.yaml -n $NAMESPACE
```

Überprüfen Sie, ob das Kubernetes-Geheimnis erstellt wurde:

```bash
kubectl get secret -n $NAMESPACE

NAME                                             TYPE                                  DATA   AGE
ingress-tls-csi                                  kubernetes.io/tls                     2      1m34s
```

### <a name="deploy-the-application-using-an-ingress-controller-reference"></a>Bereitstellen der Anwendung mithilfe eines Eingangsdatencontroller-Verweises

Erstellen Sie eine Datei namens `deployment.yaml` mit folgendem Inhalt:

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: busybox-one
  labels:
    app: busybox-one
spec:
  replicas: 1
  selector:
    matchLabels:
      app: busybox-one
  template:
    metadata:
      labels:
        app: busybox-one
    spec:
      containers:
      - name: busybox
        image: k8s.gcr.io/e2e-test-images/busybox:1.29-1
        command:
          - "/bin/sleep"
          - "10000"
---
apiVersion: v1
kind: Service
metadata:
  name: busybox-one
spec:
  type: ClusterIP
  ports:
  - port: 80
  selector:
    app: busybox-one
```

Wenden Sie sie auf Ihren Cluster an:

```bash
kubectl apply -f deployment.yaml -n $NAMESPACE
```

## <a name="deploy-an-ingress-resource-referencing-the-secret"></a>Bereitstellen einer Eingangsressource, die auf das Geheimnis verweist

Schließlich können Sie eine Kubernetes-Eingangsressource bereitstellen, die auf das Geheimnis verweist. Erstellen Sie eine Datei namens `ingress.yaml` mit folgendem Inhalt:

```yml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: ingress-tls
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/rewrite-target: /$1
spec:
  tls:
  - hosts:
    - demo.test.com
    secretName: ingress-tls-csi
  rules:
  - host: demo.test.com
    http:
      paths:
      - backend:
          service:
            name: busybox-one
            port:
              number: 80
        path: /(.*)
      - backend:
          service:
            name: busybox-two
            port:
              number: 80
        path: /two(/|$)(.*)
```

Notieren Sie sich den Abschnitt `tls`, der auf das zuvor erstellte Geheimnis verweist, und wenden Sie die Datei auf Ihren Cluster an:

```bash
kubectl apply -f ingress.yaml -n $NAMESPACE
```

## <a name="obtain-the-external-ip-address-of-the-ingress-controller"></a>Abrufen der externen IP-Adresse des Eingangsdatencontrollers

Verwenden Sie `kubectl get service`, um die externe IP-Adresse des Eingangsdatencontrollers abzurufen.

```bash
 kubectl get service -l app=nginx-ingress --namespace $NAMESPACE

NAME                                       TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)                      AGE
nginx-ingress-1588032400-controller        LoadBalancer   10.0.255.157   52.xx.xx.xx      80:31293/TCP,443:31265/TCP   19m
nginx-ingress-1588032400-default-backend   ClusterIP      10.0.223.214   <none>           80/TCP                       19m 
```

## <a name="test-ingress-secured-with-tls"></a>Testen des mit TLS gesicherten Eingangs

Verwenden Sie `curl`, um zu überprüfen, ob Ihr Eingang ordnungsgemäß mit TLS konfiguriert wurde. Achten Sie darauf, die externe IP-Adresse zu verwenden, die Sie im vorherigen Schritt abgerufen haben:

```bash
curl -v -k --resolve demo.test.com:443:52.xx.xx.xx https://demo.test.com

# You should see output similar to the following
*  subject: CN=demo.test.com; O=ingress-tls
*  start date: Oct 15 04:23:46 2021 GMT
*  expire date: Oct 15 04:23:46 2022 GMT
*  issuer: CN=demo.test.com; O=ingress-tls
*  SSL certificate verify result: self signed certificate (18), continuing anyway.
```

<!-- LINKS INTERNAL -->
[csi-ss-identity-access]: ./csi-secrets-store-identity-access.md
<!-- LINKS EXTERNAL -->
[kubernetes-ingress-tls]: https://kubernetes.io/docs/concepts/services-networking/ingress/#tls
