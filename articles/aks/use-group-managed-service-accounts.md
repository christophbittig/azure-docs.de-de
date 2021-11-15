---
title: Aktivieren von gruppenverwalteten Dienstkonten (GMSA) für Ihre Windows Server-Knoten auf Ihrem AKS-Cluster (Azure Kubernetes Service) (Vorschau)
description: Hier erfahren Sie, wie Sie gruppenverwaltete Dienstkonten (GMSAs, Group Managed Service Accounts) für Ihre Windows Server-Knoten auf Ihrem AKS-Cluster (Azure Kubernetes Service) zum Sichern Ihrer Pods aktivieren.
services: container-service
ms.topic: article
ms.date: 11/01/2021
ms.openlocfilehash: f0acd8cc19ab8a0994b6218a171d8343eef21096
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478625"
---
# <a name="enable-group-managed-service-accounts-gmsa-for-you-windows-server-nodes-on-your-azure-kubernetes-service-aks-cluster-preview"></a>Aktivieren von gruppenverwalteten Dienstkonten (GMSA) für Ihre Windows Server-Knoten auf Ihrem AKS-Cluster (Azure Kubernetes Service) (Vorschau)

[Ein gruppenverwaltetes Dienstkonto (GMSA)][gmsa-overview] ist ein verwaltetes Domänenkonto für mehrere Server, das eine automatische Kennwortverwaltung, eine vereinfachte Verwaltung von Dienstprinzipalnamen (Service Principal Name, SPN) und die Möglichkeit bietet, die Verwaltung an andere Administratoren zu delegieren. AKS bietet die Möglichkeit, ein GMSA auf Ihren Windows Server-Knoten zu aktivieren, wodurch Container, die auf Windows Server-Knoten ausgeführt werden, mit dem GMSA integriert und von diesem verwaltet werden können.

Die Aktivierung eines gruppenverwalteten Dienstkontos mit Windows Server-Knoten in AKS befindet sich in der Vorschau.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="pre-requisites"></a>Voraussetzungen

Für die Aktivierung eines gruppenverwalteten Dienstkontos mit Windows Server-Knoten in AKS müssen folgende Voraussetzungen erfüllt sein:

* Kubernetes 1.19 oder höher
* Die Erweiterungsversion 0.5.37 oder höher für `aks-preview`
* Die Docker-Containerruntime, die derzeit die Standardeinstellung ist
* [Verwaltete Identitäten][aks-managed-id] mit Ihrem AKS-Cluster
* Berechtigungen zum Erstellen oder Aktualisieren einer Azure Key Vault-Instanz
* Berechtigungen zum Konfigurieren von GMSA für Active Directory Domain Services oder die lokale Active Directory-Instanz
* Auf dem Domänencontroller müssen Active Directory-Webdienste aktiviert sein, und der AKS-Cluster muss über Port 9389 erreichbar sein.

### <a name="install-the-aks-preview-azure-cli"></a>Installieren der Azure-Befehlszeilenschnittstelle `aks-preview`

Sie benötigen die Azure CLI-Erweiterung *aks-preview*. Installieren Sie die Erweiterung *aks-preview* der Azure-Befehlszeilenschnittstelle mithilfe des Befehls [az extension add][az-extension-add]. Alternativ können Sie verfügbare Updates mithilfe des Befehls [az extension update][az-extension-update] installieren.

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-the-akswindowsgmsapreview-preview-feature"></a>Registrieren der Previewfunktion `AKSWindowsGmsaPreview`

Sie müssen darüber hinaus das Featureflag `AKSWindowsGmsaPreview` in Ihrem Abonnement aktivieren, um dieses Feature zu verwenden.

Registrieren Sie das Featureflag `AKSWindowsGmsaPreview` mithilfe des Befehls [az feature register][az-feature-register], wie im folgenden Beispiel gezeigt:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "AKSWindowsGmsaPreview"
```

Es dauert einige Minuten, bis der Status *Registered (Registriert)* angezeigt wird. Überprüfen Sie den Registrierungsstatus mithilfe des Befehls [az feature list][az-feature-list]:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKSWindowsGmsaPreview')].{Name:name,State:properties.state}"
```

Wenn der Vorgang abgeschlossen ist, können Sie die Registrierung des *Microsoft.ContainerService*-Ressourcenanbieters mit dem Befehl [az provider register][az-provider-register] aktualisieren:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="configure-gmsa-on-active-directory-domain-controller"></a>Konfigurieren eines gruppenverwalteten Dienstkontos auf einem Active Directory-Domänencontroller

Um ein gruppenverwaltetes Dienstkonto mit AKS verwenden zu können, benötigen Sie sowohl das GMSA als auch Benutzeranmeldeinformationen der Standarddomäne, um auf die für Ihren Domänencontroller konfigurierten GMSA-Anmeldeinformationen zugreifen zu können. Informationen zum Konfigurieren eines gruppenverwalteten Dienstkontos auf Ihrem Domänencontroller finden Sie unter [Erste Schritte mit gruppenverwalteten Dienstkonten][gmsa-getting-started]. Für die Anmeldeinformationen der Standarddomäne können Sie einen vorhandenen Benutzer verwenden oder einen neuen erstellen. Dieser muss jedoch Zugriff auf die GMSA-Anmeldeinformationen haben.

> [!IMPORTANT]
> Sie müssen entweder Active Directory Domain Services oder die lokale Active Directory-Instanz verwenden. Derzeit können Sie Azure Active Directory nicht für die Konfiguration eines gruppenverwalteten Dienstkontos mit einem AKS-Cluster verwenden.

## <a name="store-the-standard-domain-user-credentials-in-azure-key-vault"></a>Speichern der Standardbenutzeranmeldeinformationen für die Domäne in Azure Key Vault

Ihr AKS-Cluster verwendet die Standardbenutzeranmeldeinformationen der Domäne, um vom Domänencontroller aus auf die GMSA-Anmeldeinformationen zuzugreifen. Um sicheren Zugriff auf diese Anmeldeinformationen für den AKS-Cluster zu ermöglichen, sollten diese Anmeldeinformationen in einer Azure Key Vault-Instanz gespeichert werden. Sie können einen neuen Schlüsseltresor erstellen oder einen vorhandenen Schlüsseltresor verwenden.

Verwenden Sie `az keyvault secret set`, um die Benutzeranmeldeinformationen der Standarddomäne als Geheimnis in Ihrem Schlüsseltresor zu speichern. Im folgenden Beispiel werden die Anmeldeinformationen des Domänenbenutzers mit dem Schlüssel *GMSADomainUserCred* im Schlüsseltresor *MyAKSGMSAVault* gespeichert. Ersetzen Sie die Parameter durch Ihre eigenen Anmeldeinformationen für den Schlüsseltresor, den Schlüssel und den Domänenbenutzer.

```azurecli
az keyvault secret set --vault-name MyAKSGMSAVault --name "GMSADomainUserCred" --value "$Domain\\$DomainUsername:$DomainUserPassword"
```

## <a name="optional-use-a-custom-vnet-with-custom-dns"></a>Optional: Verwenden eines benutzerdefinierten VNet mit benutzerdefiniertem DNS

Ihr Domänencontroller muss über DNS konfiguriert werden, damit er vom AKS-Cluster erreicht werden kann. Sie können Ihr Netzwerk und DNS außerhalb Ihres AKS-Clusters konfigurieren, damit Ihr Cluster auf den Domänencontroller zugreifen kann. Alternativ können Sie ein benutzerdefiniertes VNet mit einem benutzerdefinierten DNS konfigurieren, indem Sie Azure CNI mit Ihrem AKS-Cluster verwenden, um Zugriff auf Ihren Domänencontroller zu ermöglichen. Weitere Informationen finden Sie unter [Konfigurieren von Azure CNI-Netzwerken in Azure Kubernetes Service (AKS)][aks-cni].

## <a name="optional-use-your-own-kubelet-identity-for-your-cluster"></a>Optional: Verwenden Ihrer eigenen Kubelet-Identität für Ihren Cluster

Um dem AKS-Cluster Zugriff auf Ihren Schlüsseltresor zu ermöglichen, benötigt die Kubelet-Identität des Clusters Zugriff auf Ihren Schlüsseltresor. Wenn Sie einen Cluster mit aktivierter verwalteter Identität erstellen, wird standardmäßig automatisch eine Kubelet-Identität erstellt. Sie können nach der Clustererstellung Zugriff auf Ihren Schlüsseltresor für diese Identität gewähren. Dies erfolgt in einem späteren Schritt.

Alternativ können Sie Ihre eigene Identität erstellen und diese Identität in einem späteren Schritt während der Clustererstellung verwenden. Weitere Informationen zu den bereitgestellten verwalteten Identitäten finden Sie unter [Zusammenfassung der verwalteten Identitäten][aks-managed-id-kubelet].

Verwenden Sie `az identity create`, um Ihre eigene Identität zu erstellen. Im folgenden Beispiel wird eine *myIdentity*-Identität in der Ressourcengruppe *myResourceGroup* erstellt.

```azurecli
az identity create --name myIdentity --resource-group myResourceGroup
```

Sie können Ihrer Kubelet-Identität Zugriff auf Ihren Schlüsseltresor gewähren, bevor oder nachdem Sie Ihren Cluster erstellt haben. Im folgenden Beispiel wird `az identity list` zum Abrufen der ID der Identität verwendet. Die Identität wird dann auf *MANAGED_ID* festgelegt, daraufhin wird `az keyvault set-policy` verwendet, um der Identität Zugriff auf den Schlüsseltresor *MyAKSGMSAVault* zu gewähren.

```azurecli
MANAGED_ID=$(az identity list --query "[].id" -o tsv)
az keyvault set-policy --name "MyAKSGMSAVault" --object-id $MANAGED_ID --secret-permissions get
```

## <a name="create-aks-cluster"></a>Erstellen eines ACS-Clusters

Wenn Sie das gruppenverwaltete Dienstkonto mit Ihrem AKS-Cluster verwenden möchten, verwenden Sie die Parameter *enable-windows-gmsa*, *gmsa-dns-server*, *gmsa-root-domain-name* und *enable-managed-identity*.

> [!NOTE]
> Beim Erstellen eines Clusters mit Windows Server-Knotenpools müssen Sie beim Erstellen des Clusters die Administratoranmeldeinformationen angeben. Die folgenden Befehle fordern einen Benutzernamen von Ihnen an und legen ihn auf WINDOWS_USERNAME für die spätere Verwendung in einem Befehl fest (beachten Sie, dass die Befehle in diesem Artikel in eine BASH-Shell eingegeben werden).
> 
> ```azurecli
> echo "Please enter the username to use as administrator credentials for Windows Server nodes on your cluster: " && read WINDOWS_USERNAME
> ```

Verwenden Sie `az aks create` zum Erstellen eines AKS-Clusters und anschließend `az aks nodepool add`, um einen Windows Server-Knotenpool hinzuzufügen. Im folgenden Beispiel wird ein *MyAKS*-Cluster in der *MyResourceGroup*-Ressourcengruppe erstellt, das gruppenverwaltete Dienstkonto aktiviert und dann ein neuer Knotenpool namens *npwin* hinzugefügt.

> [!NOTE]
> Wenn Sie ein benutzerdefiniertes VNet verwenden, müssen Sie auch die ID des VNet über *vnet-subnet-id* angeben und ggf. *docker-bridge-address*, *dns-service-ip* und *service-cidr* je nach Konfiguration hinzufügen.
> 
> Wenn Sie Ihre eigene Identität für die Kubelet-Identität erstellt haben, verwenden Sie den *assign-kubelet-identity*-Parameter, um Ihre Identität anzugeben.

```azurecli
DNS_SERVER=<IP address of DNS server>
ROOT_DOMAIN_NAME="contoso.com"

az aks create \
    --resource-group MyResourceGroup \
    --name MyAKS \
    --vm-set-type VirtualMachineScaleSets \
    --network-plugin azure \
    --load-balancer-sku standard \
    --windows-admin-username $WINDOWS_USERNAME \
    --enable-managed-identity \
    --enable-windows-gmsa \
    --gmsa-dns-server $DNS_SERVER \
    --gmsa-root-domain-name $ROOT_DOMAIN_NAME

az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKS \
    --os-type Windows \
    --name npwin \
    --node-count 1    
```

Sie können das gruppenverwaltete Dienstkonto auch auf vorhandenen Clustern aktivieren, für die bereits Windows Server-Knoten und verwaltete Identitäten mit `az aks update` aktiviert sind. Beispiel:

```azurecli
az aks update \
    --resource-group MyResourceGroup \
    --name MyAKS \
    --enable-windows-gmsa \
    --gmsa-dns-server $DNS_SERVER \
    --gmsa-root-domain-name $ROOT_DOMAIN_NAME
```

Verwenden Sie nach der Clustererstellung oder nach dem Update Ihres Clusters `az keyvault set-policy`, um der Identität Zugriff auf Ihren Schlüsseltresor zu gewähren. Das folgende Beispiel gewährt der Kubelet-Identität, die vom Cluster erstellt wurde, Zugriff auf den Schlüsseltresor *MyAKSGMSAVault*.

> [!NOTE]
> Wenn Sie Ihre eigene Identität für die Kubelet-Identität angegeben haben, überspringen Sie diesen Schritt.

```azurecli
MANAGED_ID=$(az aks show -g MyResourceGroup -n MyAKS --query "identityProfile.kubeletidentity.objectId" -o tsv)

az keyvault set-policy --name "MyAKSGMSAVault" --object-id $MANAGED_ID --secret-permissions get
```

## <a name="install-gmsa-cred-spec"></a>Installieren der GMSA-CRED-Spezifikation

Mit dem Befehl [az aks get-credentials][] können Sie `kubectl` für die Verbindungsherstellung mit Ihrem Kubernetes-Cluster konfigurieren. Im folgenden Beispiel werden Anmeldeinformationen für den AKS-Cluster namens *MyAKS* in der Ressourcengruppe *myResourceGroup* abgerufen:

```azurecli
az aks get-credentials --resource-group MyResourceGroup --name MyAKS
```

Erstellen Sie eine *gmsa-spec.yaml*-Datei mit dem folgenden Code, und ersetzen Sie dabei die Platzhalter durch Ihre eigenen Werte.

```yml
apiVersion: windows.k8s.io/v1alpha1
kind: GMSACredentialSpec
metadata:
  name: aks-gmsa-spec  # This name can be changed, but it will be used as a reference in the pod spec
credspec:
  ActiveDirectoryConfig:
    GroupManagedServiceAccounts:
    - Name: $GMSA_ACCOUNT_USERNAME
      Scope: $NETBIOS_DOMAIN_NAME
    - Name: $GMSA_ACCOUNT_USERNAME
      Scope: $DNS_DOMAIN_NAME
    HostAccountConfig:
      PluginGUID: '{CCC2A336-D7F3-4818-A213-272B7924213E}'
      PortableCcgVersion: "1"
      PluginInput: ObjectId=$MANAGED_ID;SecretUri=$SECRET_URI  # SECRET_URI takes the form https://$akvName.vault.azure.net/secrets/$akvSecretName
  CmsPlugins:
  - ActiveDirectory
  DomainJoinConfig:
    DnsName: $DNS_DOMAIN_NAME
    DnsTreeName: $DNS_ROOT_DOMAIN_NAME
    Guid:  $AD_DOMAIN_OBJECT_GUID
    MachineAccountName: $GMSA_ACCOUNT_USERNAME
    NetBiosName: $NETBIOS_DOMAIN_NAME
    Sid: $GMSA_SID
```

Erstellen Sie wie folgt eine *gmsa-role.yaml*-Datei.

```yml
#Create the Role to read the credspec
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: aks-gmsa-role
rules:
- apiGroups: ["windows.k8s.io"]
  resources: ["gmsacredentialspecs"]
  verbs: ["use"]
  resourceNames: ["aks-gmsa-spec"]
```

Erstellen Sie wie folgt eine *gmsa-role-binding.yaml*-Datei.

```yml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: allow-default-svc-account-read-on-aks-gmsa-spec
  namespace: default
subjects:
- kind: ServiceAccount
  name: default
  namespace: default
roleRef:
  kind: ClusterRole
  name: aks-gmsa-role
  apiGroup: rbac.authorization.k8s.io
```

Verwenden Sie `kubectl apply`, um die Änderungen aus *gmsa-spec.yaml*, *gmsa-role.yaml* und *gmsa-role-binding.yaml* anzuwenden.

```azurecli
kubectl apply -f gmsa-spec.yaml
kubectl apply -f gmsa-role.yaml
kubectl apply -f gmsa-role-binding.yaml
```

## <a name="verify-gmsa-is-installed-and-working"></a>Überprüfen, ob das gruppenverwaltete Dienstkonto installiert ist und funktioniert

Erstellen Sie wie folgt eine *gmsa-demo.yaml*-Datei.

```yml
---
kind: ConfigMap
apiVersion: v1
metadata:
  labels:
   app: gmsa-demo
  name: gmsa-demo
  namespace: default
data:
  run.ps1: |
   $ErrorActionPreference = "Stop"

   Write-Output "Configuring IIS with authentication."

   # Add required Windows features, since they are not installed by default.
   Install-WindowsFeature "Web-Windows-Auth", "Web-Asp-Net45"

   # Create simple ASP.Net page.
   New-Item -Force -ItemType Directory -Path 'C:\inetpub\wwwroot\app'
   Set-Content -Path 'C:\inetpub\wwwroot\app\default.aspx' -Value 'Authenticated as <B><%=User.Identity.Name%></B>, Type of Authentication: <B><%=User.Identity.AuthenticationType%></B>'

   # Configure IIS with authentication.
   Import-Module IISAdministration
   Start-IISCommitDelay
   (Get-IISConfigSection -SectionPath 'system.webServer/security/authentication/windowsAuthentication').Attributes['enabled'].value = $true
   (Get-IISConfigSection -SectionPath 'system.webServer/security/authentication/anonymousAuthentication').Attributes['enabled'].value = $false
   (Get-IISServerManager).Sites[0].Applications[0].VirtualDirectories[0].PhysicalPath = 'C:\inetpub\wwwroot\app'
   Stop-IISCommitDelay

   Write-Output "IIS with authentication is ready."

   C:\ServiceMonitor.exe w3svc
---
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: gmsa-demo
  name: gmsa-demo
  namespace: default
spec:
  replicas: 1
  selector:
    matchLabels:
      app: gmsa-demo
  template:
    metadata:
      labels:
        app: gmsa-demo
    spec:
      securityContext:
        windowsOptions:
          gmsaCredentialSpecName: aks-gmsa-spec
      containers:
      - name: iis
        image: mcr.microsoft.com/windows/servercore/iis:windowsservercore-ltsc2019
        imagePullPolicy: IfNotPresent
        command:
         - powershell
        args:
          - -File
          - /gmsa-demo/run.ps1
        volumeMounts:
          - name: gmsa-demo
            mountPath: /gmsa-demo
      volumes:
      - configMap:
          defaultMode: 420
          name: gmsa-demo
        name: gmsa-demo
      nodeSelector:
        kubernetes.io/os: windows
---
apiVersion: v1
kind: Service
metadata:
  labels:
    app: gmsa-demo
  name: gmsa-demo
  namespace: default
spec:
  ports:
  - port: 80
    targetPort: 80
  selector:
    app: gmsa-demo
  type: LoadBalancer
```

Verwenden Sie `kubectl apply`, um die Änderungen aus *gmsa-demo.yaml* anzuwenden.

```azurecli
kubectl apply -f gmsa-demo.yaml
```

Verwenden Sie `kubectl get service`, um die IP-Adresse der Beispielanwendung anzuzeigen.

```console
kubectl get service gmsa-demo --watch
```

Die externe IP-Adresse *EXTERNAL-IP* für den *gmsa-demo*-Dienst wird zunächst als *ausstehend* angezeigt.

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
gmsa-demo          LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Sobald die externe IP-Adresse (*EXTERNAL-IP*) von pending (*ausstehend*) in eine tatsächliche öffentliche IP-Adresse geändert wurde, verwenden Sie `CTRL-C`, um die `kubectl`-Überwachung zu beenden. Die folgende Beispielausgabe zeigt eine gültige öffentliche IP-Adresse, die dem Dienst zugewiesen ist:

```output
gmsa-demo  LoadBalancer   10.0.37.27   EXTERNAL-IP   80:30572/TCP   2m
```

Um zu überprüfen, ob GMSA ordnungsgemäß funktioniert und konfiguriert ist, öffnen Sie einen Webbrowser mit der externen IP-Adresse des *gmsa-demo*-Diensts. Führen Sie die Authentifizierung mit `$NETBIOS_DOMAIN_NAME\$AD_USERNAME` und dem Kennwort durch, und überprüfen Sie, ob `Authenticated as $NETBIOS_DOMAIN_NAME\$AD_USERNAME, Type of Authentication: Negotiate` angezeigt wird.

## <a name="troubleshooting"></a>Problembehandlung

### <a name="no-authentication-is-prompted-when-loading-the-page"></a>Beim Laden der Seite wird keine Authentifizierung angezeigt

Wenn die Seite geladen wird, Sie aber nicht zur Authentifizierung aufgefordert werden, verwenden Sie `kubelet logs POD_NAME`, um die Protokolle Ihres Pods anzuzeigen und sicherzustellen, dass *IIS mit Authentifizierung bereitsteht*.

### <a name="connection-timeout-when-trying-to-load-the-page"></a>Verbindungszeitüberschreitung beim Versuch, die Seite zu laden

Wenn bei dem Versuch, die Seite zu laden, ein Verbindungstimeout auftritt, überprüfen Sie, ob die Beispiel-App mit `kubectl get pods --watch` ausgeführt wird. Manchmal ist die externe IP-Adresse für den App-Beispieldienst verfügbar, bevor der App-Beispielpod ausgeführt wird.

### <a name="pod-fails-to-start-and-an-winapi-error-shows-in-the-pod-events"></a>Fehler beim Starten des Pods und *winapi-Fehler* in den Podereignissen

Wenn Ihr Pod nach der Ausführung von `kubectl get pods --watch` und einer kurzen Wartezeit nicht gestartet wird, führen Sie `kubectl describe pod POD_NAME` aus. Wenn ein *winapi-Fehler* in den Podereignissen angezeigt wird, liegt möglicherweise ein Fehler bei der Konfiguration Ihrer GMSA CRED-Spezifikation vor. Überprüfen Sie, ob alle Ersetzungswerte in *gmsa-spec.yaml* richtig sind, führen Sie `kubectl apply -f gmsa-spec.yaml` erneut aus, und stellen Sie die Beispielanwendung erneut bereit.


[aks-cni]: configure-azure-cni.md
[aks-managed-id]: use-managed-identity.md
[aks-managed-id-kubelet]: use-managed-identity.md#summary-of-managed-identities
[az aks get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[gmsa-getting-started]: /windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts
[gmsa-overview]: /windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview
[rdp]: rdp.md