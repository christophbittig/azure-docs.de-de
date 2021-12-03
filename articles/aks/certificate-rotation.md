---
title: Rotieren von Zertifikaten in Azure Kubernetes Service (AKS)
description: Erfahren Sie, wie Sie Ihre Zertifikate in einem Azure Kubernetes Service-Cluster (AKS) rotieren.
services: container-service
ms.topic: article
ms.date: 11/03/2021
ms.openlocfilehash: cd1e55df9609adcc8d5d1d33b1853ba855889b8f
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132371617"
---
# <a name="rotate-certificates-in-azure-kubernetes-service-aks"></a>Rotieren von Zertifikaten in Azure Kubernetes Service (AKS)

Azure Kubernetes Service (AKS) verwendet Zertifikate für die Authentifizierung bei vielen seiner Komponenten. Sie müssen diese Zertifikate in regelmäßigen Abständen aus Sicherheits- oder Richtliniengründen rotieren. Beispielsweise kann es eine Richtlinie bei Ihnen geben, dass Sie alle Ihre Zertifikate alle 90 Tage rotieren müssen.

In diesem Artikel wird beschrieben, wie Sie die Zertifikate in Ihrem AKS-Cluster rotieren.

## <a name="before-you-begin"></a>Voraussetzungen

Der Artikel setzt voraus, dass Sie mindestens Version 2.0.77 der Azure-Befehlszeilenschnittstelle (Azure CLI) ausführen. Führen Sie `az --version` aus, um die Version zu ermitteln. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI][azure-cli-install].

## <a name="aks-certificates-certificate-authorities-and-service-accounts"></a>AKS-Zertifikate, Zertifizierungsstellen und Dienstkonten

AKS generiert und verwendet die folgenden Zertifikate, Zertifizierungsstellen und Dienstkonten:

* Der AKS-API-Server erstellt eine Zertifizierungsstelle (CA), die als Clusterzertifizierungsstelle („Cluster CA“) bezeichnet wird.
* Der API-Server verfügt über eine Clusterzertifizierungsstelle, die Zertifikate für die unidirektionale Kommunikation vom API-Server zu kubelets signiert.
* Jedes kubelet erstellt außerdem eine Zertifikatsignierungsanforderung (Certificate Signing Request, CSR), die von der Clusterzertifizierungsstelle signiert wird, für die Kommunikation vom kubelet zum API-Server.
* Der API-Aggregator verwendet die Clusterzertifizierungsstelle zum Ausstellen von Zertifikaten für die Kommunikation mit anderen APIs. Der API-Aggregator kann auch über eine eigene Zertifizierungsstelle zum Ausstellen dieser Zertifikate verfügen, aber zurzeit verwendet er die Clusterzertifizierungsstelle.
* Jeder Knoten verwendet ein Dienstkontotoken (SA-Token), das von der Clusterzertifizierungsstelle signiert wurde.
* Der `kubectl`-Client verfügt über ein Zertifikat für die Kommunikation mit dem AKS-Cluster.

> [!NOTE]
> AKS-Cluster, die vor dem Mai 2019 erstellt wurden, besitzen Zertifikate, die nach zwei Jahren ablaufen. Cluster, die nach dem Mai 2019 erstellt wurden, sowie Cluster mit Zertifikatrotation besitzen Zertifikate der Clusterzertifizierungsstelle, die nach 30 Jahren ablaufen. Alle anderen AKS-Zertifikate, die die Clusterzertifizierungsstelle zum Signieren verwenden, laufen nach zwei Jahren ab und rotieren während des AKS-Versionsupgrades automatisch. Um zu überprüfen, wann Ihr Cluster erstellt wurde, verwenden Sie `kubectl get nodes`, um das *Alter* Ihrer Knotenpools anzuzeigen.
> 
> Darüber hinaus können Sie das Ablaufdatum des Zertifikats Ihres Clusters überprüfen. Beispielsweise werden mit dem folgenden Bash-Befehl die Details des Clientzertifikats für den Cluster *myAKSCluster* in der Ressourcengruppe *rg* angezeigt.
> ```console
> kubectl config view --raw -o jsonpath="{.users[?(@.name == 'clusterUser_rg_myAKSCluster')].user.client-certificate-data}" | base64 -d | openssl x509 -text | grep -A2 Validity
> ```

* Überprüfen des Ablaufdatums des „apiserver“-Zertifikats
```console
curl https://{apiserver-fqdn} -k -v 2>&1 |grep expire
```

* Überprüfen des Ablaufdatums des Zertifikats auf dem VMAS-Agentknoten
```console
az vm run-command invoke -g MC_rg_myAKSCluster_region -n vm-name --command-id RunShellScript --query 'value[0].message' -otsv --scripts "openssl x509 -in /etc/kubernetes/certs/apiserver.crt -noout -enddate"
```

* Überprüfen des Ablaufdatums des Zertifikats auf einem VMSS-Agentknoten
```console
az vmss run-command invoke -g MC_rg_myAKSCluster_region -n vmss-name --instance-id 0 --command-id RunShellScript --query 'value[0].message' -otsv --scripts "openssl x509 -in /etc/kubernetes/certs/apiserver.crt -noout -enddate"
```

## <a name="certificate-auto-rotation"></a>Automatische Zertifikatrotation

Azure Kubernetes Service rotiert automatisch und ohne Ausfallzeiten Zertifikate, die nicht von einer Zertifizierungsstelle stammen, sowohl auf der Steuerungsebene als auch auf Agent-Knoten, bevor sie ablaufen.

Damit AKS Zertifikate, die nicht von Zertifizierungsstellen stammen, automatisch rotieren kann, muss der Cluster [TLS-Bootstrapping](https://kubernetes.io/docs/reference/command-line-tools-reference/kubelet-tls-bootstrapping/) bieten. TLS-Bootstrapping ist derzeit in den folgenden Regionen verfügbar:

* eastus2euap
* centraluseuap
* westcentralus
* uksouth
* eastus
* australiacentral
* australiaest

#### <a name="how-to-check-whether-current-agent-node-pool-is-tls-bootstrapping-enabled"></a>Wie kann überprüft werden, ob der aktuelle Agent-Knotenpool mit TLS-Bootstrapping aktiviert ist?
Um zu überprüfen, ob TLS-Bootstrapping in Ihrem Cluster aktiviert ist, navigieren Sie zu den folgenden Pfaden.  Auf einem Linux-Knoten ist das: /var/lib/kubelet/bootstrap-kubeconfig. Auf einem Windows-Knoten ist das: c:\k\bootstrap-config.

> [Hinweis] Der Dateipfad kann sich ändern, wenn die k8s-Version in der Zukunft weiterentwickelt wird.

> [!IMPORTANT]
>Nachdem eine Region konfiguriert wurde, erstellen Sie entweder einen neuen Cluster oder aktualisieren mit „az aks upgrade -g $RESSOURCEN_GRUPPEN_NAME -n $CLUSTER_NAME“ einen vorhandenen Cluster, um die automatische Zertifikatrotation für diesen Cluster zu konfigurieren. 

### <a name="limitation"></a>Einschränkung

Die automatische Zertifikatrotation wird in einem Cluster ohne RBAC nicht aktiviert.


## <a name="rotate-your-cluster-certificates"></a>Rotieren Ihrer Clusterzertifikate

> [!WARNING]
> Beim Rotieren von Zertifikaten mithilfe von `az aks rotate-certs` werden alle Ihre Knoten und deren Betriebssystemdatenträger neu erstellt. Das kann dazu führen, dass der AKS-Cluster bis zu 30 Minuten ausfällt.

Verwenden Sie [az aks get-credentials][az-aks-get-credentials], um sich bei Ihrem AKS-Cluster anzumelden. Mit diesem Befehl wird auch das `kubectl`-Clientzertifikat auf Ihren lokalen Computer heruntergeladen und konfiguriert.

```azurecli
az aks get-credentials -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME
```

Verwenden Sie `az aks rotate-certs`, um alle Zertifikate, Zertifizierungsstellen und SAs auf Ihrem Cluster zu rotieren.

```azurecli
az aks rotate-certs -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME
```

> [!IMPORTANT]
> Der `az aks rotate-certs`-Vorgang kann bis zu 30 Minuten dauern. Wenn der Befehl vor dem Abschluss fehlschlägt, überprüfen Sie mithilfe von `az aks show`, ob der Status des Clusters *Zertifikat wird rotiert* lautet. Wenn sich der Cluster in einem fehlerhaften Zustand befindet, führen Sie `az aks rotate-certs` noch mal aus, um die Zertifikate erneut zu rotieren.

Vergewissern Sie sich, dass die alten Zertifikate nicht mehr gültig sind, indem Sie einen `kubectl`-Befehl ausführen. Da Sie die von `kubectl` verwendeten Zertifikate nicht aktualisiert haben, wird eine Fehlermeldung angezeigt.  Beispiel:

```console
$ kubectl get no
Unable to connect to the server: x509: certificate signed by unknown authority (possibly because of "crypto/rsa: verification error" while trying to verify candidate authority certificate "ca")
```

Aktualisieren Sie das von `kubectl` verwendete Zertifikat, indem Sie `az aks get-credentials` ausführen.

```azurecli
az aks get-credentials -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME --overwrite-existing
```

Überprüfen Sie, ob die Zertifikate aktualisiert wurden, indem Sie einen `kubectl` Befehl ausführen, der nun erfolgreich sein wird. Beispiel:

```console
kubectl get no
```

> [!NOTE]
> Wenn Sie Dienste haben, die auf AKS basieren, müssen Sie möglicherweise auch die Zertifikate für diese Dienste aktualisieren.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel wurde gezeigt, wie Sie die Zertifikate, Zertifizierungsstellen und SAs Ihres Clusters automatisch rotieren. Weitere Informationen zu bewährte Methoden der AKS-Sicherheit finden Sie unter [Bewährte Methoden für Clustersicherheit und Upgrades in Azure Kubernetes Service (AKS)][aks-best-practices-security-upgrades].


[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[aks-best-practices-security-upgrades]: operator-best-practices-cluster-security.md
