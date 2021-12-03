---
title: Schützen des Datenverkehrs zwischen Pods mit einer Netzwerkrichtlinie
titleSuffix: Azure Kubernetes Service
description: Es wird beschrieben, wie Sie ein- und ausgehenden Datenverkehr bei Pods mittels Kubernetes-Netzwerkrichtlinien in Azure Kubernetes Service (AKS) schützen.
services: container-service
ms.topic: article
ms.date: 03/16/2021
ms.openlocfilehash: 4ceb9059456a4f5b20a346e1688b82320e5041fe
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131577648"
---
# <a name="secure-traffic-between-pods-using-network-policies-in-azure-kubernetes-service-aks"></a>Sicherer Datenverkehr zwischen Pods durch Netzwerkrichtlinien in Azure Kubernetes Service (AKS)

Wenn Sie moderne, auf Microservices basierende Anwendungen in Kubernetes ausführen, können Sie steuern, welche Komponenten miteinander kommunizieren können. Bei der Festlegung, wie Datenverkehr zwischen Pods in einem AKS-Cluster (Azure Kubernetes Service) übermittelt werden kann, sollte das Prinzip der geringsten Rechte angewendet werden. Angenommen, Sie möchten den direkten Datenverkehr zu Back-End-Anwendungen blockieren. Mit dem Feature *Netzwerkrichtlinie* in Kubernetes können Sie die Regeln für eingehenden und ausgehenden Datenverkehr zwischen Pods in einem Cluster definieren.

In diesem Artikel wird veranschaulicht, wie Sie das Modul für Netzwerkrichtlinien installieren und Kubernetes-Netzwerkrichtlinien erstellen, um den Datenverkehrsfluss zwischen Pods in AKS zu steuern. Netzwerkrichtlinien sollten nur für Linux-basierte Knoten und Pods in AKS verwendet werden.

## <a name="before-you-begin"></a>Voraussetzungen

Azure CLI-Version 2.0.61 oder höher muss installiert und konfiguriert sein. Führen Sie `az --version` aus, um die Version zu ermitteln. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI][install-azure-cli].

> [!TIP]
> Wenn Sie die Funktion für Netzwerkrichtlinien während der Vorschauphase verwendet haben, empfehlen wir, dass Sie [einen neuen Cluster](#create-an-aks-cluster-and-enable-network-policy) erstellen.
> 
> Wenn Sie vorhandene Testcluster, die Netzwerkrichtlinien während der Vorschauphase verwendet haben, weiterhin verwenden möchten, führen Sie ein Upgrade Ihres Clusters auf eine neue Kubernetes-Versionen für die aktuelle GA-Version durch, und stellen Sie dann das folgende YAML-Manifest bereit, um den abstürzenden Metrikenserver und das Kubernetes-Dashboard zu beheben. Diese Korrektur ist nur bei Clustern erforderlich, die die Netzwerkrichtlinien-Engine von Calico verwendet haben.
>
> Als bewährte Sicherheitsmethode [überprüfen Sie den Inhalt dieses YAML-Manifests][calico-aks-cleanup], um zu verstehen, was in dem AKS-Cluster bereitgestellt wird.
>
> `kubectl delete -f https://raw.githubusercontent.com/Azure/aks-engine/master/docs/topics/calico-3.3.1-cleanup-after-upgrade.yaml`

## <a name="overview-of-network-policy"></a>Übersicht über die Netzwerkrichtlinie

Standardmäßig können alle Pods in einem AKS-Cluster Datenverkehr ohne Einschränkungen senden und empfangen. Zur Verbesserung der Sicherheit können Sie Regeln definieren, die den Datenverkehrsfluss steuern. Back-End-Anwendungen werden häufig nur für erforderliche Front-End-Dienste verfügbar gemacht. Oder Datenbankkomponenten sind nur für die Anwendungsebenen zugänglich, die eine Verbindung damit herstellen.

Netzwerkrichtlinie ist eine Kubernetes-Spezifikation, die Zugriffsrichtlinien für die Kommunikation zwischen Pods definiert. Mithilfe von Netzwerkrichtlinien definieren Sie eine geordnete Menge an Regeln zum Senden und Empfangen von Datenverkehr und wenden sie auf eine Sammlung von Pods an, die einem oder mehreren Bezeichnungsselektoren entsprechen.

Diese Regeln der Netzwerkrichtlinien sind als YAML-Manifeste definiert. Netzwerkrichtlinien können Teil eines umfassenderen Manifests sein, mit dem auch eine Bereitstellung oder ein Dienst erstellt wird.

### <a name="network-policy-options-in-aks"></a>Optionen für Netzwerkrichtlinien in AKS

Azure stellt zwei Verfahren zum Implementieren von Netzwerkrichtlinien bereit. Sie wählen eine Netzwerkrichtlinienoption beim Erstellen eines AKS-Clusters aus. Die Richtlinienoption kann nach dem Erstellen des Clusters nicht geändert werden:

* Die eigene Richtlinienimplementierung von Azure, die als *Azure-Netzwerkrichtlinien* bezeichnet wird.
* *Calico-Netzwerkrichtlinien*, eine von [Tigera][tigera] gegründete Open-Source-Netzwerk- und -Netzwerkrichtlinienlösung.

Beide Implementierungen verwenden Linux *IPTables*, um die angegebenen Richtlinien durchzusetzen. Aus den Richtlinien werden zulässige und unzulässige IP-Adresspaare gebildet. Diese Paare werden anschließend als IPTable-Filterregeln programmiert.

### <a name="differences-between-azure-and-calico-policies-and-their-capabilities"></a>Unterschiede zwischen Azure- und Calico-Richtlinien und zwischen ihren Funktionen

| Funktion                               | Azure                      | Calico                      |
|------------------------------------------|----------------------------|-----------------------------|
| Unterstützte Plattformen                      | Linux                      | Linux, Windows Server 2019 (Vorschauversion)  |
| Unterstützte Netzwerkoptionen             | Azure CNI                  | Azure CNI (Windows Server 2019 und Linux) und kubenet (Linux)  |
| Compliance mit Kubernetes-Spezifikation | Alle Richtlinientypen werden unterstützt |  Alle Richtlinientypen werden unterstützt |
| Zusätzliche Features                      | Keine                       | Erweitertes Richtlinienmodell, das aus globaler Netzwerkrichtlinie, globalem Netzwerksatz und Hostendpunkt besteht. Weitere Informationen zur Verwendung der `calicoctl`-Befehlszeilenschnittstelle zum Verwalten dieser erweiterten Funktionen finden Sie in der [calicoctl-Benutzerreferenz][calicoctl]. |
| Support                                  | Unterstützt durch das Azure-Support- und Engineering-Team | Calico-Communitysupport. Weitere Informationen zu zusätzlichem kostenpflichtigem Support finden Sie unter [Project Calico support options][calico-support] (Supportoptionen für Project „Calico“). |
| Protokollierung                                  | In IPTables hinzugefügte/gelöschte Regeln werden auf jedem Host unter */var/log/azure-npm.log* protokolliert. | Weitere Informationen finden Sie unter [Calico-Komponentenprotokolle][calico-logs]. |

## <a name="create-an-aks-cluster-and-enable-network-policy"></a>Erstellen eines AKS-Clusters und Aktivieren der Netzwerkrichtlinie

Um Netzwerkrichtlinien in Aktion zu sehen, erstellen Sie eine Richtlinie, die Datenverkehr definiert, und erweitern sie dann:

* Lehnen Sie jeglichen Datenverkehr zum Pod ab.
* Lassen Sie Datenverkehr basierend auf Podbezeichnungen zu.
* Lassen Sie Datenverkehr basierend auf dem Namespace zu.

Erstellen wir zunächst einen AKS-Cluster, der Netzwerkrichtlinien unterstützt.

> [!IMPORTANT]
>
> Die Netzwerkrichtlinienfunktion kann nur aktiviert werden, wenn der Cluster erstellt wird. Ohne einen vorhandenen AKS-Cluster können Sie keine Netzwerkrichtlinie aktivieren.

Um eine Azure-Netzwerkrichtlinie zu verwenden, müssen Sie das [Azure CNI-Plug-In][azure-cni] verwenden und eigene virtuelle Netzwerke und Subnetze definieren. Detaillierte Informationen zur Planung der erforderlichen Subnetzadressbereiche finden Sie unter [Konfigurieren von Azure CNI-Netzwerken in Azure Kubernetes Service (AKS)][use-advanced-networking]. Eine Calico-Netzwerkrichtlinie könnte entweder mit diesem selben Azure CNI-Plug-In oder mit dem Kubenet CNI-Plug-In verwendet werden.

Das folgende Beispielskript:

* Erstellt ein virtuelles Netzwerk und ein Subnetz.
* Erstellt einen Azure AD-Dienstprinzipal (Azure Active Directory) für die Verwendung mit dem AKS-Cluster.
* Weist dem Dienstprinzipal des AKS-Clusters in einen virtuellen Netzwerk *Mitwirkender*-Berechtigungen zu.
* Erstellt einen AKS-Cluster im definierten virtuellen Netzwerk und aktiviert die Netzwerkrichtlinie.
    * Die _Azure-Netzwerkrichtlinienoption_ wird verwendet. Um stattdessen Calico als Netzwerkrichtlinienoption zu verwenden, verwenden Sie den Parameter `--network-policy calico`. Hinweis: Calico könnte entweder mit `--network-plugin azure` oder mit `--network-plugin kubenet` verwendet werden.

Beachten Sie, dass Sie anstelle eines Dienstprinzipals eine verwaltete Identität für Berechtigungen verwenden können. Weitere Informationen finden Sie unter [Verwenden verwalteter Identitäten](use-managed-identity.md).

Geben Sie Ihr eigenes sicheres *SP_PASSWORD* ein. Sie können die Variablen *RESOURCE_GROUP_NAME* und *CLUSTER_NAME* ersetzen:

```azurecli-interactive
RESOURCE_GROUP_NAME=myResourceGroup-NP
CLUSTER_NAME=myAKSCluster
LOCATION=canadaeast

# Create a resource group
az group create --name $RESOURCE_GROUP_NAME --location $LOCATION

# Create a virtual network and subnet
az network vnet create \
    --resource-group $RESOURCE_GROUP_NAME \
    --name myVnet \
    --address-prefixes 10.0.0.0/8 \
    --subnet-name myAKSSubnet \
    --subnet-prefix 10.240.0.0/16

# Create a service principal and read in the application ID
SP=$(az ad sp create-for-rbac --output json)
SP_ID=$(echo $SP | jq -r .appId)
SP_PASSWORD=$(echo $SP | jq -r .password)

# Wait 15 seconds to make sure that service principal has propagated
echo "Waiting for service principal to propagate..."
sleep 15

# Get the virtual network resource ID
VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP_NAME --name myVnet --query id -o tsv)

# Assign the service principal Contributor permissions to the virtual network resource
az role assignment create --assignee $SP_ID --scope $VNET_ID --role Contributor

# Get the virtual network subnet resource ID
SUBNET_ID=$(az network vnet subnet show --resource-group $RESOURCE_GROUP_NAME --vnet-name myVnet --name myAKSSubnet --query id -o tsv)
```

### <a name="create-an-aks-cluster-for-azure-network-policies"></a>Erstellen eines AKS-Clusters für Azure-Netzwerkrichtlinien

Erstellen Sie den AKS-Cluster, und geben Sie das virtuelle Netzwerk, Dienstprinzipalinformationen und *azure* für das Netzwerk-Plug-In und die Netzwerkrichtlinie an.

```azurecli
az aks create \
    --resource-group $RESOURCE_GROUP_NAME \
    --name $CLUSTER_NAME \
    --node-count 1 \
    --generate-ssh-keys \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id $SUBNET_ID \
    --service-principal $SP_ID \
    --client-secret $SP_PASSWORD \
    --network-plugin azure \
    --network-policy azure
```

Die Erstellung des Clusters dauert einige Minuten. Wenn der Cluster bereit ist, können Sie `kubectl` mit dem Befehl [az aks get-credentials][az-aks-get-credentials] konfigurieren, um die Verbindung mit Ihrem Kubernetes-Cluster herzustellen. Mit diesem Befehl werden die Anmeldeinformationen heruntergeladen, und die Kubernetes-Befehlszeilenschnittstelle wird für deren Verwendung konfiguriert:

```azurecli-interactive
az aks get-credentials --resource-group $RESOURCE_GROUP_NAME --name $CLUSTER_NAME
```

### <a name="create-an-aks-cluster-for-calico-network-policies"></a>Erstellen eines AKS-Clusters für Calico-Netzwerkrichtlinien

Erstellen Sie den AKS-Cluster, und geben Sie das virtuelle Netzwerk, Dienstprinzipalinformationen und *azure* für das Netzwerk-Plug-In sowie *calico* für die Netzwerkrichtlinie an. Die Verwendung von *calico* als Netzwerkrichtlinie ermöglicht die Verwendung von Calico-Netzwerkfunktionen in Linux- und Windows-basierten Knotenpools.

Wenn Sie vorhaben, Ihrem Cluster Windows-Knotenpools hinzuzufügen, schließen Sie die Parameter `windows-admin-username` und `windows-admin-password` ein, die die [Kennwortanforderungen von Windows Server][windows-server-password] erfüllen. Außerdem muss `Microsoft.ContainerService/EnableAKSWindowsCalico` registriert werden, um Calico mit Windows-Knotenpools verwenden zu können.

Registrieren Sie das `EnableAKSWindowsCalico`-Featureflag mit dem Befehl [az feature register][az-feature-register], wie im folgenden Beispiel gezeigt:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "EnableAKSWindowsCalico"
```

 Sie können den Registrierungsstatus mithilfe des Befehls [az feature list][az-feature-list] überprüfen:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableAKSWindowsCalico')].{Name:name,State:properties.state}"
```

Wenn Sie so weit sind, aktualisieren Sie mithilfe des Befehls [az provider register][az-provider-register] die Registrierung des Ressourcenanbieters *Microsoft.ContainerService*:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

> [!IMPORTANT]
> Die Verwendung von Calico-Netzwerkrichtlinien mit Windows-Knoten ist aktuell nur für neue Cluster ab der Kubernetes-Version 1.20 mit Calico 3.17.2 verfügbar und erfordert die Verwendung eines Azure CNI-Netzwerks. Für Windows-Knoten in AKS-Clustern, in denen Calico aktiviert ist, wird zudem standardmäßig [Direct Server Return (DSR)][dsr] aktiviert.
>
> Bei Clustern, die nur Linux-Knotenpools mit Kubernetes 1.20 und älteren Versionen von Calico enthalten, wird die Calico-Version automatisch auf 3.17.2 aktualisiert.

Calico-Netzwerkrichtlinien mit Windows-Knoten befinden sich aktuell in der Vorschauphase.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

Erstellen Sie einen Benutzernamen, der für die Administratoranmeldeinformationen für Ihre Windows Server-Container in Ihrem Cluster verwendet wird. Die folgenden Befehle fordern einen Benutzernamen von Ihnen an und legen ihn auf WINDOWS_USERNAME für die spätere Verwendung in einem Befehl fest (beachten Sie, dass die Befehle in diesem Artikel in eine BASH-Shell eingegeben werden).

```azurecli-interactive
echo "Please enter the username to use as administrator credentials for Windows Server containers on your cluster: " && read WINDOWS_USERNAME
```

```azurecli
az aks create \
    --resource-group $RESOURCE_GROUP_NAME \
    --name $CLUSTER_NAME \
    --node-count 1 \
    --generate-ssh-keys \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id $SUBNET_ID \
    --service-principal $SP_ID \
    --client-secret $SP_PASSWORD \
    --windows-admin-username $WINDOWS_USERNAME \
    --vm-set-type VirtualMachineScaleSets \
    --kubernetes-version 1.20.2 \
    --network-plugin azure \
    --network-policy calico
```

Die Erstellung des Clusters dauert einige Minuten. Ihr Cluster wird standardmäßig nur mit einem Linux-Knotenpool erstellt. Wenn Sie Windows-Knotenpools verwenden möchten, können Sie einen hinzufügen. Zum Beispiel:

```azurecli
az aks nodepool add \
    --resource-group $RESOURCE_GROUP_NAME \
    --cluster-name $CLUSTER_NAME \
    --os-type Windows \
    --name npwin \
    --node-count 1
```

Wenn der Cluster bereit ist, können Sie `kubectl` mit dem Befehl [az aks get-credentials][az-aks-get-credentials] konfigurieren, um die Verbindung mit Ihrem Kubernetes-Cluster herzustellen. Mit diesem Befehl werden die Anmeldeinformationen heruntergeladen, und die Kubernetes-Befehlszeilenschnittstelle wird für deren Verwendung konfiguriert:

```azurecli-interactive
az aks get-credentials --resource-group $RESOURCE_GROUP_NAME --name $CLUSTER_NAME
```

## <a name="deny-all-inbound-traffic-to-a-pod"></a>Verweigern sämtlichen eingehenden Datenverkehrs zu einem Pod

Bevor Sie Regeln zum Zulassen bestimmten Netzwerkdatenverkehrs definieren, erstellen Sie zuerst eine Netzwerkrichtlinie, um sämtlichen Datenverkehr abzulehnen. Diese Richtlinie bietet Ihnen einen Ausgangspunkt, um mit der Erstellung einer Positivliste zu beginnen, die nur den gewünschten Datenverkehr zulässt. Sie können auch deutlich erkennen, dass Datenverkehr bei Anwendung der Netzwerkrichtlinie verworfen wird.

Für die Beispielanwendungsumgebung und die Regeln für den Netzwerkdatenverkehr erstellen wir zunächst einen Namespace mit dem Namen *development* zur Ausführung der Beispielpods:

```console
kubectl create namespace development
kubectl label namespace/development purpose=development
```

Erstellen Sie einen Beispiel-Back-End-Pod, der NGINX ausführt. Dieser Back-End-Pod kann verwendet werden, um eine Beispielanwendung auf Back-End-Web-Basis zu simulieren. Erstellen Sie diesen Pod im *development* -Namespace, und öffnen Sie Port *80* für den Webdatenverkehr. Geben Sie dem Pod die Bezeichnung *app=webapp,role=backend*, damit wir im nächsten Abschnitt eine auf ihn zielgerichtete Netzwerkrichtlinie einfügen können:

```console
kubectl run backend --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine --labels app=webapp,role=backend --namespace development --expose --port 80
```

Erstellen Sie einen weiteren Pod, und fügen Sie eine Terminalsitzung an, um zu testen, ob Sie die NGINX-Standardwebseite erreichen können:

```console
kubectl run --rm -it --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11 network-policy --namespace development
```

Vergewissern Sie sich an der Shelleingabeaufforderung mit `wget`, dass Sie auf die NGINX-Standardwebseite zugreifen können:

```console
wget -qO- http://backend
```

Die folgende Beispielausgabe zeigt, dass die NGINX-Standardwebseite zurückgegeben wird:

```output
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Schließen Sie die angefügte Terminalsitzung. Der Testpod wird automatisch gelöscht.

```console
exit
```

### <a name="create-and-apply-a-network-policy"></a>Erstellen und Anwenden einer Netzwerkrichtlinie

Nachdem Sie sich nun vergewissert haben, dass Sie die grundlegende NGINX-Webseite auf dem Beispiel-Back-End-Pod verwenden können, können Sie eine Netzwerkrichtlinie erstellen, um sämtlichen Datenverkehr abzulehnen. Erstellen Sie eine Datei namens `backend-policy.yaml`, und fügen Sie das folgende YAML-Manifest ein. Dieses Manifest verwendet einen *podSelector* zum Anfügen der Richtlinie an Pods, die die Bezeichnung *app:webapp,role:backend* haben (wie Ihr Beispiel-NGINX-Pod). Da unter *ingress* keine Regeln definiert sind, wird sämtlicher in den Pod eingehender Datenverkehr verweigert:

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
  namespace: development
spec:
  podSelector:
    matchLabels:
      app: webapp
      role: backend
  ingress: []
```

Navigieren Sie zu [https://shell.azure.com](https://shell.azure.com), um Azure Cloud Shell in Ihrem Browser zu öffnen.

Wenden Sie die Netzwerkrichtlinie über den Befehl [kubectl apply][kubectl-apply] an, und geben Sie den Namen Ihres YAML-Manifests an:

```console
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-network-policy"></a>Testen der Netzwerkrichtlinie

Jetzt prüfen wir, ob Sie die NGINX-Webseite auf dem Back-End-Pod verwenden können. Erstellen Sie einen anderen Testpod, und fügen Sie an diesen eine Terminalsitzung an:

```console
kubectl run --rm -it --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11 network-policy --namespace development
```

Vergewissern Sie sich an der Shelleingabeaufforderung mit `wget`, dass Sie auf die NGINX-Standardwebseite zugreifen können. Legen Sie dieses Mal einen Timeoutwert auf *2* Sekunden fest. Die Netzwerkrichtlinie blockiert jetzt sämtlichen eingehenden Datenverkehr, damit die Seite nicht geladen werden kann. Dies ist im folgenden Beispiel dargestellt:

```console
wget -O- --timeout=2 --tries=1 http://backend
```

```output
wget: download timed out
```

Schließen Sie die angefügte Terminalsitzung. Der Testpod wird automatisch gelöscht.

```console
exit
```

## <a name="allow-inbound-traffic-based-on-a-pod-label"></a>Zulassen eingehenden Datenverkehrs basierend auf einer Podbezeichnung

Im vorherigen Abschnitt wurde ein Back-End-NGINX-Pod geplant und eine Netzwerkrichtlinie zum Ablehnen des gesamten Datenverkehrs erstellt. Wir erstellen nun einen Front-End-Pod und aktualisieren die Netzwerkrichtlinie, damit Datenverkehr von Front-End-Pods zugelassen wird.

Aktualisieren Sie die Netzwerkrichtlinie zum Zulassen von Datenverkehr von Pods mit den Bezeichnungen *app:webapp,role:frontend* und in einem beliebigen Namespace. Bearbeiten Sie die vorherige *backend-policy.yaml*-Datei, und fügen Sie Eingangsregeln vom Typ *matchLabels* hinzu, damit das Manifest dem folgenden Beispiel entspricht:

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
  namespace: development
spec:
  podSelector:
    matchLabels:
      app: webapp
      role: backend
  ingress:
  - from:
    - namespaceSelector: {}
      podSelector:
        matchLabels:
          app: webapp
          role: frontend
```

> [!NOTE]
> Diese Netzwerkrichtlinie verwendet ein *namespaceSelector*- und ein *podSelector*-Element für die Eingangsregel. Die YAML-Syntax ist wichtig, damit die Eingangsregeln additiv sind. In diesem Beispiel müssen beide Elemente übereinstimmen, damit die Eingangsregel angewendet wird. Kubernetes-Versionen vor *1.12* interpretieren diese Elemente unter Umständen nicht korrekt und beschränken den Netzwerkdatenverkehr ggf. nicht wie erwartet. Weitere Informationen zu diesem Verhalten finden Sie unter [Behavior of to and from selectors][policy-rules] (Verhalten von to- und from-Selektoren).

Wenden Sie die aktualisierte Netzwerkrichtlinie über den Befehl [kubectl apply][kubectl-apply] an, und geben Sie den Namen Ihres YAML-Manifests an:

```console
kubectl apply -f backend-policy.yaml
```

Planen Sie einen Pod mit der Bezeichnung *app=webapp,role=frontend*, und fügen Sie eine Terminalsitzung an:

```console
kubectl run --rm -it frontend --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11 --labels app=webapp,role=frontend --namespace development
```

Vergewissern Sie sich an der Shelleingabeaufforderung mit `wget`, dass Sie auf die NGINX-Standardwebseite zugreifen können:

```console
wget -qO- http://backend
```

Da die Eingangsregel Datenverkehr mit Pods zulässt, die die Bezeichnungen *app: webapp,role: frontend* haben, wird der Datenverkehr vom Front-End-Pod zugelassen. Die folgende Beispielausgabe zeigt, dass die NGINX-Standardwebseite zurückgegeben wird:

```output
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Schließen Sie die angefügte Terminalsitzung. Der Pod wird automatisch gelöscht.

```console
exit
```

### <a name="test-a-pod-without-a-matching-label"></a>Testen eines Pods ohne passende Bezeichnung

Die Netzwerkrichtlinie lässt Datenverkehr von Pods mit der Bezeichnung *app: webapp,role: frontend* zu, sollte aber den gesamten sonstigen Datenverkehr verweigern. Wir testen nun, ob ein anderer Pod ohne diese Bezeichnungen auf den Back-End-NGINX-Pod zugreifen kann. Erstellen Sie einen anderen Testpod, und fügen Sie an diesen eine Terminalsitzung an:

```console
kubectl run --rm -it --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11 network-policy --namespace development
```

Vergewissern Sie sich an der Shelleingabeaufforderung mit `wget`, dass Sie auf die NGINX-Standardwebseite zugreifen können. Die Netzwerkrichtlinie blockiert sämtlichen eingehenden Datenverkehr, damit die Seite nicht geladen werden kann. Dies ist im folgenden Beispiel dargestellt:

```console
wget -O- --timeout=2 --tries=1 http://backend
```

```output
wget: download timed out
```

Schließen Sie die angefügte Terminalsitzung. Der Testpod wird automatisch gelöscht.

```console
exit
```

## <a name="allow-traffic-only-from-within-a-defined-namespace"></a>Zulassen von Datenverkehr nur, wenn er von einem definierten Namespace ausgeht

In den vorherigen Beispielen haben Sie eine Netzwerkrichtlinie erstellt, die sämtlichen Datenverkehr verweigert, und haben die Richtlinie dann aktualisiert, um Datenverkehr von Pods mit einer bestimmten Bezeichnung zuzulassen. Eine weitere häufige Anforderung ist das Begrenzen des Datenverkehrs auf einen bestimmten Namespace. Wenn die vorherigen Beispiele sich auf den Datenverkehr in einem *development*-Namespace beziehen würden, könnten Sie eine Netzwerkrichtlinie erstellen, die verhindert, dass Datenverkehr aus einem anderen Namespace, z. B. *production*, die Pods erreicht.

Erstellen Sie zunächst einen neuen Namespace, um einen production-Namespace zu simulieren:

```console
kubectl create namespace production
kubectl label namespace/production purpose=production
```

Planen Sie einen Testpod im *production*-Namespace mit der Bezeichnung *app=webapp,role=frontend*. Fügen Sie eine Terminalsitzung an:

```console
kubectl run --rm -it frontend --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11 --labels app=webapp,role=frontend --namespace production
```

Vergewissern Sie sich an der Shelleingabeaufforderung mit `wget`, dass Sie auf die NGINX-Standardwebseite zugreifen können:

```console
wget -qO- http://backend.development
```

Da die Bezeichnungen für den Pod mit den derzeitigen Zulässigkeitsvorgaben der Netzwerkrichtlinie übereinstimmen, wird der Datenverkehr zugelassen. Die Netzwerkrichtlinie beachtet nicht die Namespaces, sondern nur die Podbezeichnungen. Die folgende Beispielausgabe zeigt, dass die NGINX-Standardwebseite zurückgegeben wird:

```output
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Schließen Sie die angefügte Terminalsitzung. Der Testpod wird automatisch gelöscht.

```console
exit
```

### <a name="update-the-network-policy"></a>Aktualisieren der Netzwerkrichtlinie

Wir aktualisieren jetzt den Eingangsregelabschnitt *namespaceSelector*, um nur vom *development*-Namespace ausgehenden Datenverkehr zuzulassen. Bearbeiten Sie die Manifestdatei *backend-policy.yaml* wie im folgenden Beispiel gezeigt:

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
  namespace: development
spec:
  podSelector:
    matchLabels:
      app: webapp
      role: backend
  ingress:
  - from:
    - namespaceSelector:
        matchLabels:
          purpose: development
      podSelector:
        matchLabels:
          app: webapp
          role: frontend
```

In komplexeren Beispielen können Sie mehrere Eingangsregeln definieren, z. B. die Verwendung eines *namespaceSelector*-Elements und dann eines *podSelector*-Elements.

Wenden Sie die aktualisierte Netzwerkrichtlinie über den Befehl [kubectl apply][kubectl-apply] an, und geben Sie den Namen Ihres YAML-Manifests an:

```console
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-updated-network-policy"></a>Testen der aktualisierten Netzwerkrichtlinie

Planen Sie einen weiteren Pod im *production*-Namespace, und fügen Sie eine Terminalsitzung an:

```console
kubectl run --rm -it frontend --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11 --labels app=webapp,role=frontend --namespace production
```

Vergewissern Sie sich an der Shelleingabeaufforderung mit `wget`, dass die Netzwerkrichtlinie Datenverkehr nun ablehnt:

```console
wget -O- --timeout=2 --tries=1 http://backend.development
```

```output
wget: download timed out
```

Schließen Sie den Test-Pod:

```console
exit
```

Da der Datenverkehr vom *production*-Namespace jetzt abgelehnt wird, können Sie einen Testpod im *development*-Namespace planen und eine Terminalsitzung anfügen:

```console
kubectl run --rm -it frontend --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11 --labels app=webapp,role=frontend --namespace development
```

Vergewissern Sie sich an der Shelleingabeaufforderung mit `wget`, dass die Netzwerkrichtlinie den Datenverkehr zulässt:

```console
wget -qO- http://backend
```

Der Datenverkehr wird zugelassen, da der Pod in dem Namespace geplant ist, der mit den derzeitigen Zulässigkeitsvorgaben der Netzwerkrichtlinie übereinstimmt. Die folgende Beispielausgabe zeigt, dass die NGINX-Standardwebseite zurückgegeben wird:

```output
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Schließen Sie die angefügte Terminalsitzung. Der Testpod wird automatisch gelöscht.

```console
exit
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

In diesem Artikel haben wir zwei Namespaces erstellt und eine Netzwerkrichtlinie angewendet. Verwenden Sie zur Bereinigung dieser Ressourcen den Befehl [kubectl delete][kubectl-delete], und geben Sie die Ressourcennamen an:

```console
kubectl delete namespace production
kubectl delete namespace development
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Netzwerkressourcen in Kubernetes finden Sie unter [Netzwerkkonzepte für Anwendungen in Azure Kubernetes Service (AKS)][concepts-network].

Weitere Informationen zu Richtlinien finden Sie unter [Network Policies][kubernetes-network-policies] (Netzwerkrichtlinien).

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubernetes-network-policies]: https://kubernetes.io/docs/concepts/services-networking/network-policies/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[policy-rules]: https://kubernetes.io/docs/concepts/services-networking/network-policies/#behavior-of-to-and-from-selectors
[aks-github]: https://github.com/azure/aks/issues
[tigera]: https://www.tigera.io/
[calicoctl]: https://docs.projectcalico.org/reference/calicoctl/
[calico-support]: https://www.tigera.io/tigera-products/calico/
[calico-logs]: https://docs.projectcalico.org/maintenance/troubleshoot/component-logs
[calico-aks-cleanup]: https://github.com/Azure/aks-engine/blob/master/docs/topics/calico-3.3.1-cleanup-after-upgrade.yaml

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[use-advanced-networking]: configure-azure-cni.md
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[concepts-network]: concepts-network.md
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[windows-server-password]: /windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements#reference
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[dsr]: ../load-balancer/load-balancer-multivip-overview.md#rule-type-2-backend-port-reuse-by-using-floating-ip
