---
title: Informationen zu Azure Policy für Kubernetes
description: Hier erfahren Sie, wie Rego und Open Policy Agent von Azure Policy genutzt werden, um Cluster mit Kubernetes in Azure oder lokal zu verwalten.
ms.date: 09/13/2021
ms.topic: conceptual
ms.custom: devx-track-azurecli
ms.openlocfilehash: 80f9f1e796580964df14cc15cafc0b844b227a5d
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132492870"
---
# <a name="understand-azure-policy-for-kubernetes-clusters"></a>Grundlegendes zu Azure Policy für Kubernetes-Cluster

Azure Policy erweitert [Gatekeeper](https://github.com/open-policy-agent/gatekeeper) v3, einen _Webhook für die Zugangssteuerung_ für [Open Policy Agent](https://www.openpolicyagent.org/) (OPA), um zentral und einheitlich bedarfsgesteuerte Erzwingungs- und Schutzmaßnahmen auf Ihre Cluster anzuwenden. Mithilfe von Azure Policy kann der Konformitätszustand Ihrer Kubernetes-Cluster von einem zentralen Ort aus verwaltet und gemeldet werden. Das Add-On bietet folgende Funktionen:

- Überprüfen auf Richtlinienzuweisungen für den Cluster mit dem Azure Policy-Dienst
- Bereitstellen von Richtliniendefinitionen im Cluster als [Einschränkungsvorlage](https://open-policy-agent.github.io/gatekeeper/website/docs/howto/#constraint-templates) und benutzerdefinierte [Einschränkungsressource](https://github.com/open-policy-agent/gatekeeper#constraints)
- Senden von Details zur Überwachung und Konformität zurück an den Azure Policy-Dienst

Von Azure Policy für Kubernetes werden folgende Clusterumgebungen unterstützt:

- [Azure Kubernetes Service (AKS)](../../../aks/intro-kubernetes.md)
- [Kubernetes mit Azure Arc-Aktivierung](../../../azure-arc/kubernetes/overview.md)
- [AKS-Engine](https://github.com/Azure/aks-engine/blob/master/docs/README.md)

> [!IMPORTANT]
> Die Add-Ons für AKS-Engine und Kubernetes mit Arc-Aktivierung sind in der **Vorschauversion** verfügbar. Azure Policy für Kubernetes unterstützt nur Linux-Knotenpools und integrierte Richtliniendefinitionen (benutzerdefinierte Richtliniendefinitionen sind eine _öffentliche Previewfunktion_). Integrierte Richtliniendefinitionen befinden sich in der Kategorie **Kubernetes**. Die Richtliniendefinitionen der eingeschränkten Vorschauversion mit der Auswirkung **EnforceOPAConstraint** und **EnforceRegoPolicy** und der zugehörigen Kategorie **Kubernetes Service** sind _veraltet_.
> Verwenden Sie stattdessen die Auswirkungen _audit_ und _deny_ mit dem Ressourcenanbietermodus `Microsoft.Kubernetes.Data`.

## <a name="overview"></a>Übersicht

Gehen Sie wie folgt vor, um Azure Policy zu aktivieren und mit Ihrem Kubernetes-Cluster zu verwenden:

1. Konfigurieren Sie Ihren Kubernetes-Cluster, und installieren Sie das Add-On:
   - [Azure Kubernetes Service (AKS)](#install-azure-policy-add-on-for-aks)
   - [Kubernetes mit Azure Arc-Aktivierung](#install-azure-policy-extension-for-azure-arc-enabled-kubernetes)
   - [AKS-Engine](#install-azure-policy-add-on-for-aks-engine)

   > [!NOTE]
   > Informationen zu häufigen Problemen bei der Installation finden Sie unter [Problembehandlung – Azure Policy-Add-On](../troubleshoot/general.md#add-on-for-kubernetes-installation-errors).

1. [Machen Sie sich mit der Azure Policy-Sprache für Kubernetes vertraut.](#policy-language)

1. [Weisen Sie Ihrem Kubernetes-Cluster eine Definition zu.](#assign-a-policy-definition)

1. [Warten auf die Validierung](#policy-evaluation)

## <a name="limitations"></a>Einschränkungen

Die folgenden allgemeinen Einschränkungen gelten für das Azure Policy-Add-On für Kubernetes-Cluster:

- Das Azure Policy-Add-On für Kubernetes wird unter der Kubernetes-Version **1.14** oder höher unterstützt.
- Das Azure Policy-Add-On für Kubernetes kann nur in Linux-Knotenpools bereitgestellt werden.
- Es werden nur integrierte Richtliniendefinitionen unterstützt. Benutzerdefinierte Richtliniendefinitionen sind eine _öffentliche Previewfunktion_.
- Maximale Anzahl von Pods, die vom Azure Policy-Add-On unterstützt werden: **10.000**
- Maximale Anzahl nicht konformer Datensätze pro Richtlinie pro Cluster: **500**
- Maximale Anzahl nicht konformer Datensätze pro Abonnement: **1 Mio.**
- Installationen von Gatekeeper außerhalb des Azure Policy-Add-Ons werden nicht unterstützt. Deinstallieren Sie alle Komponenten, die durch eine frühere Installation von Gatekeeper installiert wurden, bevor Sie das Azure Policy-Add-On aktivieren.
- [Ursachen für Nichtkonformität](../how-to/determine-non-compliance.md#compliance-reasons) sind für den `Microsoft.Kubernetes.Data`
  [Ressourcenanbietermodus](./definition-structure.md#resource-provider-modes) nicht verfügbar. Verwenden Sie [Komponentendetails](../how-to/determine-non-compliance.md#component-details-for-resource-provider-modes).
- [Ausnahmen](./exemption-structure.md) auf Komponentenebene werden für [Ressourcenanbietermodi](./definition-structure.md#resource-provider-modes) nicht unterstützt.

Die folgenden Einschränkungen gelten nur für das Azure Policy-Add-On für AKS:

- Die [AKS-Podsicherheitsrichtlinie](../../../aks/use-pod-security-policies.md) und das Azure Policy-Add-On für AKS können nicht beide aktiviert sein. Weitere Informationen hierzu finden Sie unter [Einschränkungen im Hinblick auf die AKS-Podsicherheit](../../../aks/use-azure-policy.md).
- Namespaces, die vom Azure Policy-Add-On bei der Auswertung automatisch ausgeschlossen werden: _kube-system_, _gatekeeper-system_ und _aks-periscope_.

## <a name="recommendations"></a>Empfehlungen

Im folgenden finden Sie allgemeine Empfehlungen für die Verwendung des Azure Policy-Add-Ons:

- Zur Verwendung des Azure Policy-Add-Ons müssen drei Gatekeeper-Komponenten ausgeführt werden: ein Überwachungspod und zwei Webhook-Podreplikate. Diese Komponenten nutzen umso mehr Ressourcen, je stärker die Anzahl der Kubernetes-Ressourcen und Richtlinienzuweisungen im Cluster zunimmt. Dadurch werden entsprechende Überwachungs- und Erzwingungsvorgänge erforderlich.

  - Bei weniger als 500 Pods in einem Cluster mit maximal 20 Einschränkungen sind zwei vCPUs und 350 MB Arbeitsspeicher pro Komponente erforderlich.
  - Bei mehr als 500 Pods in einem Cluster mit maximal 40 Einschränkungen gilt Folgendes: drei vCPUs und 600 MB Arbeitsspeicher pro Komponente.

- Windows-Pods [unterstützen keine Sicherheitskontexte](https://kubernetes.io/docs/concepts/security/pod-security-standards/#what-profiles-should-i-apply-to-my-windows-pods).
  Daher können einige Azure Policy-Richtlinien wie das Sperren von Stammberechtigungen in Windows-Pods nicht eskaliert und nur auf Linux-Pods angewendet werden.

Die folgende Empfehlung gilt nur für AKS und das Azure Policy-Add-On:

- Verwenden Sie zum Planen von Gatekeeper-Pods den Systemknotenpool mit einem `CriticalAddonsOnly`-Taint. Weitere Informationen finden Sie unter [Verwenden von Systemknotenpools](../../../aks/use-system-pools.md#system-and-user-node-pools).
- Schützen Sie von Ihren AKS-Clustern ausgehenden Datenverkehr. Weitere Informationen finden Sie unter [Steuern des ausgehenden Datenverkehrs für Clusterknoten](../../../aks/limit-egress-traffic.md).
- Wenn für den Cluster `aad-pod-identity` aktiviert wurde, werden die IPTables der Knoten von NMI-Pods (Node Managed Identity) so geändert, dass Aufrufe für den Azure Instance Metadata-Endpunkt abgefangen werden. Diese Konfiguration bedeutet, dass jede Anforderung, die an den Metadatenendpunkt gerichtet ist, von NMI abgefangen wird, auch wenn `aad-pod-identity` vom Pod nicht verwendet wird. Die AzurePodIdentityException-CRD kann so konfiguriert werden, dass `aad-pod-identity` darüber informiert wird, dass an den Metadatenendpunkt gerichtete Anforderungen, die von einem Pod stammen, der in der CRD definierte Bezeichnungen abgleicht, ohne Verarbeitung in NMI über einen Proxy zu senden sind. Die Systempods mit der Bezeichnung `kubernetes.azure.com/managedby: aks` im Namespace _kube-system_ müssen in `aad-pod-identity` durch Konfiguration der AzurePodIdentityException-CRD ausgeschlossen werden. Weitere Informationen finden Sie unter [Disable aad-pod-identity for a specific pod or application (Deaktivieren von „aad-pod-identity“ für einen bestimmten Pod oder eine bestimmte Anwendung)](https://azure.github.io/aad-pod-identity/docs/configure/application_exception).
  Installieren zur Konfiguration einer Ausnahme die [YAML-Datei „mic-exception“](https://github.com/Azure/aad-pod-identity/blob/master/deploy/infra/mic-exception.yaml).

## <a name="install-azure-policy-add-on-for-aks"></a>Installieren des Azure Policy-Add-Ons für AKS

Bevor Sie das Azure Policy-Add-On installieren oder eines der Dienstfeatures aktivieren, müssen Sie in Ihrem Abonnement den Ressourcenanbieter **Microsoft.PolicyInsights** aktivieren.

1. Azure CLI-Version 2.12.0 oder höher muss installiert und konfiguriert sein. Führen Sie `az --version` aus, um die Version zu ermitteln. Installations- und Upgradeinformationen finden Sie bei Bedarf unter [Installieren von Azure CLI](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-cli).

1. Registrieren Sie die Ressourcenanbieter und die Previewfunktionen.

   - Azure-Portal:

     Registrieren Sie den Ressourcenanbieter **Microsoft.PolicyInsights**. Weitere Informationen finden Sie unter [Ressourcenanbieter und -typen](../../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider).

   - Azure CLI:

     ```azurecli-interactive
     # Log in first with az login if you're not using Cloud Shell

     # Provider register: Register the Azure Policy provider
     az provider register --namespace Microsoft.PolicyInsights
     ```

1. Wenn Richtliniendefinitionen der eingeschränkten Vorschauversion installiert wurden, entfernen Sie das Add-On in Ihrem AKS-Cluster auf der Seite **Richtlinien** mithilfe der Schaltfläche **Deaktivieren**.

1. Der AKS-Cluster muss die Version _1.14_ oder höher aufweisen. Verwenden Sie das folgende Skript, um Ihre AKS-Clusterversion zu überprüfen:

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Look for the value in kubernetesVersion
   az aks list
   ```

1. Installieren Sie die Azure-Befehlszeilenschnittstelle Version _2.12.0_ oder höher. Weitere Informationen finden Sie unter [Installieren der Azure-Befehlszeilenschnittstelle](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-cli).

Sind die obigen Voraussetzungen erfüllt, installieren Sie das Azure Policy-Add-On in dem zu verwaltenden AKS-Cluster.

- Azure-Portal

  1. Starten Sie den AKS-Dienst im Azure-Portal, indem Sie die Option **Alle Dienste** auswählen und dann nach **Kubernetes-Dienste** suchen und die entsprechende Option auswählen.

  1. Wählen Sie einen Ihrer AKS-Cluster aus.

  1. Wählen Sie links **Richtlinien** auf der Seite des Kubernetes-Diensts aus.

  1. Wählen Sie auf der Hauptseite die Schaltfläche **Add-On aktivieren** aus.

- Azure CLI

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks enable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

Führen Sie den folgenden Befehl aus, um zu überprüfen, ob die Installation des Add-Ons erfolgreich war und die Pods _azure-policy_ und _gatekeeper_ ausgeführt werden:

```bash
# azure-policy pod is installed in kube-system namespace
kubectl get pods -n kube-system

# gatekeeper pod is installed in gatekeeper-system namespace
kubectl get pods -n gatekeeper-system
```

Vergewissern Sie sich abschließend, dass das neueste Add-On installiert ist. Führen Sie hierzu den folgenden Azure CLI-Befehl aus, und ersetzen Sie dabei `<rg>` durch den Namen Ihrer Ressourcengruppe und `<cluster-name>` durch den Namen Ihres AKS-Clusters: `az aks show --query addonProfiles.azurePolicy -g <rg> -n <cluster-name>`. Das Ergebnis sollte so ähnlich wie die folgende Ausgabe aussehen:

```output
{
        "config": null,
        "enabled": true,
        "identity": null
}
```
## <a name="install-azure-policy-extension-for-azure-arc-enabled-kubernetes-preview"></a><a name="install-azure-policy-extension-for-azure-arc-enabled-kubernetes"></a>Installieren der Azure Policy-Erweiterung für Kubernetes mit Azure Arc-Unterstützung (Vorschau)

Mithilfe von [Azure Policy für Kubernetes](./policy-for-kubernetes.md) kann der Konformitätszustand Ihrer Kubernetes-Cluster von einem zentralen Ort aus verwaltet und gemeldet werden.

In diesem Artikel wird beschrieben, wie Sie die Azure Policy für Kubernetes-Erweiterung [erstellen](#create-azure-policy-extension) und [löschen](#delete-azure-policy-extension) sowie den [Erweiterungsstatus anzeigen](#show-azure-policy-extension).

Eine Übersicht der Erweiterungsplattform finden Sie unter [Azure Arc-Clustererweiterungen](/azure/azure-arc/kubernetes/conceptual-extensions).

### <a name="prerequisites"></a>Voraussetzungen

> Hinweis: Wenn Sie Azure Policy für Kubernetes bereits auf einem Azure Arc-Cluster mittels Helm direkt ohne Erweiterungen bereitgestellt haben, befolgen Sie die aufgeführten Anweisungen zum [Löschen des Helm-Charts](#remove-the-add-on-from-azure-arc-enabled-kubernetes). Sobald der Löschvorgang erfolgt ist, können Sie fortfahren.
1. Stellen Sie sicher, dass Ihr Kubernetes-Cluster eine unterstützte Distribution ist.

    > Hinweis: Die Azure Policy für Arc-Erweiterung wird in den [folgenden Kubernetes-Distributionen](/azure/azure-arc/kubernetes/validation-program) unterstützt.
1. Stellen Sie sicher, dass alle [hier](/azure/azure-arc/kubernetes/extensions) aufgeführten allgemeinen Voraussetzungen für Kubernetes-Erweiterungen erfüllt sind, einschließlich des [Verbindens Ihres Clusters mit Azure Arc](/azure/azure-arc/kubernetes/quickstart-connect-cluster?tabs=azure-cli).

    > Hinweis: Die Azure Policy-Erweiterung wird für Kubernetes-Cluster mit Arc-Unterstützung [in diesen Regionen](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc) unterstützt.
1. Öffnen Sie Ports für die Azure Policy-Erweiterung. Die Azure Policy-Erweiterung verwendet diese Domänen und Ports, um Richtliniendefinitionen und Zuweisungen abzurufen sowie um die Konformität des Clusters zurück an Azure Policy zu melden.

   |Domain |Port |
   |---|---|
   |`data.policy.core.windows.net` |`443` |
   |`store.policy.core.windows.net` |`443` |
   |`login.windows.net` |`443` |
   |`dc.services.visualstudio.com` |`443` |

1. Bevor Sie die Azure Policy-Erweiterung installieren oder eins der Dienstfeatures aktivieren, müssen Sie in Ihrem Abonnement den Ressourcenanbieter **Microsoft.PolicyInsights** aktivieren.
    > Hinweis: Zum Aktivieren des Ressourcenanbieters führen Sie die Schritte unter [Ressourcenanbieter und -typen](/azure/azure-resource-manager/management/resource-providers-and-types#azure-portal) aus, oder führen Sie entweder den Azure CLI- oder Azure PowerShell-Befehl aus:
   - Azure CLI

     ```azurecli-interactive
     # Log in first with az login if you're not using Cloud Shell
     # Provider register: Register the Azure Policy provider
     az provider register --namespace 'Microsoft.PolicyInsights'
     ```

   - Azure PowerShell

     ```azurepowershell-interactive
     # Log in first with Connect-AzAccount if you're not using Cloud Shell
    
     # Provider register: Register the Azure Policy provider
     Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
     ```

### <a name="create-azure-policy-extension"></a>Erstellen der Azure Policy-Erweiterung

> Beachten Sie Folgendes beim Erstellen der Azure Policy-Erweiterung:
> - Automatische Upgrades sind standardmäßig aktiviert, wodurch die Nebenversion der Azure Policy-Erweiterung aktualisiert wird, wenn neue Änderungen bereitgestellt werden.
> - Alle Proxyvariablen, die als Parameter an `connectedk8s` übergeben werden, werden an die Azure Policy-Erweiterung weitergegeben, um den Proxy für ausgehenden Datenverkehr zu unterstützen.
> 
Führen Sie zum Erstellen einer Erweiterungsinstanz für Ihren Cluster mit Arc-Unterstützung den folgenden Befehl aus, wobei Sie `<>` durch Ihre Werte ersetzen:

```azurecli-interactive
az k8s-extension create --cluster-type connectedClusters --cluster-name <CLUSTER_NAME> --resource-group <RESOURCE_GROUP> --extension-type Microsoft.PolicyInsights --name <EXTENSION_INSTANCE_NAME>
```

#### <a name="example"></a>Beispiel:

```azurecli-interactive
az k8s-extension create --cluster-type connectedClusters --cluster-name my-test-cluster --resource-group my-test-rg --extension-type Microsoft.PolicyInsights --name azurepolicy
```

#### <a name="example-output"></a>Beispielausgabe:

```json
{
  "aksAssignedIdentity": null,
  "autoUpgradeMinorVersion": true,
  "configurationProtectedSettings": {},
  "configurationSettings": {},
  "customLocationSettings": null,
  "errorInfo": null,
  "extensionType": "microsoft.policyinsights",
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-test-rg/providers/Microsoft.Kubernetes/connectedClusters/my-test-cluster/providers/Microsoft.KubernetesConfiguration/extensions/azurepolicy",
 "identity": {
    "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "tenantId": null,
    "type": "SystemAssigned"
  },
  "location": null,
  "name": "azurepolicy",
  "packageUri": null,
  "provisioningState": "Succeeded",
  "releaseTrain": "Stable",
  "resourceGroup": "my-test-rg",
  "scope": {
    "cluster": {
      "releaseNamespace": "kube-system"
    },
    "namespace": null
  },
  "statuses": [],
  "systemData": {
    "createdAt": "2021-10-27T01:20:06.834236+00:00",
    "createdBy": null,
    "createdByType": null,
    "lastModifiedAt": "2021-10-27T01:20:06.834236+00:00",
    "lastModifiedBy": null,
    "lastModifiedByType": null
  },
  "type": "Microsoft.KubernetesConfiguration/extensions",
  "version": "1.1.0"
}
```

### <a name="show-azure-policy-extension"></a>Anzeigen der Azure Policy-Erweiterung

Um zu überprüfen, ob die Erstellung der Erweiterungsinstanz erfolgreich war, und die Metadaten der Erweiterung zu überprüfen, führen Sie den folgenden Befehl aus, wobei Sie `<>` durch Ihre Werte ersetzen:

```console
az k8s-extension show --cluster-type connectedClusters --cluster-name <CLUSTER_NAME> --resource-group <RESOURCE_GROUP> --name <EXTENSION_INSTANCE_NAME>
```

#### <a name="example"></a>Beispiel:

```console
az k8s-extension show --cluster-type connectedClusters --cluster-name my-test-cluster --resource-group my-test-rg --name azurepolicy
```

Führen Sie den folgenden Befehl aus, um zu überprüfen, ob die Installation des Erweiterung erfolgreich war und die Pods „azure-policy“ und „gatekeeper“ ausgeführt werden:

```bash
# azure-policy pod is installed in kube-system namespace
kubectl get pods -n kube-system

# gatekeeper pod is installed in gatekeeper-system namespace
kubectl get pods -n gatekeeper-system
```

### <a name="delete-azure-policy-extension"></a>Löschen der Azure Policy-Erweiterung
Um die Erweiterungsinstanz zu löschen, führen Sie den folgenden Befehl aus, wobei Sie `<>` durch Ihre Werte ersetzen:

```azurecli-interactive
az k8s-extension delete --cluster-type connectedClusters --cluster-name <CLUSTER_NAME> --resource-group <RESOURCE_GROUP> --name <EXTENSION_INSTANCE_NAME>
```

## <a name="install-azure-policy-add-on-using-helm-for-azure-arc-enabled-kubernetes-preview"></a><a name="install-azure-policy-add-on-for-azure-arc-enabled-kubernetes"></a>Installieren des Azure Policy-Add-Ons für Kubernetes mit Azure Arc-Unterstützung mittels Helm (Vorschau)

> [!NOTE]
> Das Helm-Modell des Azure Policy-Add-Ons wird bald eingestellt. Installieren Sie stattdessen die [Azure Policy-Erweiterung für Kubernetes mit Azure Arc-Unterstützung](#install-azure-policy-extension-for-azure-arc-enabled-kubernetes).

Bevor Sie das Azure Policy-Add-On installieren oder eines der Dienstfeatures aktivieren, muss in Ihrem Abonnement der **Microsoft.PolicyInsights**-Ressourcenanbieter aktiviert und eine Rollenzuweisung für den Clusterdienstprinzipal erstellt werden.

1. Azure CLI-Version 2.12.0 oder höher muss installiert und konfiguriert sein. Führen Sie `az --version` aus, um die Version zu ermitteln. Installations- und Upgradeinformationen finden Sie bei Bedarf unter [Installieren von Azure CLI](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-cli).

1. Zum Aktivieren des Ressourcenanbieters führen Sie die Schritte unter [Ressourcenanbieter und -typen](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal) aus, oder führen Sie entweder den Azure CLI- oder Azure PowerShell-Befehl aus:

   - Azure CLI

     ```azurecli-interactive
     # Log in first with az login if you're not using Cloud Shell

     # Provider register: Register the Azure Policy provider
     az provider register --namespace 'Microsoft.PolicyInsights'
     ```

   - Azure PowerShell

     ```azurepowershell-interactive
     # Log in first with Connect-AzAccount if you're not using Cloud Shell

     # Provider register: Register the Azure Policy provider
     Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
     ```

1. Die Version des Kubernetes-Clusters muss mindestens _1.14_ sein.

1. Installieren Sie [Helm 3](https://v3.helm.sh/docs/intro/install/).

1. Für Ihren Kubernetes-Cluster muss Azure Arc aktiviert werden. Weitere Informationen finden Sie unter [Herstellen einer Verbindung für einen Kubernetes-Cluster mit Azure Arc-Aktivierung (Vorschau)](../../../azure-arc/kubernetes/quickstart-connect-cluster.md).

1. Sie benötigen die vollqualifizierte Azure-Ressourcen-ID des Kubernetes-Clusters mit Azure Arc-Aktivierung.

1. Öffnen Sie Ports für das Add-On. Vom Azure Policy-Add-On werden die folgenden Domänen und Ports verwendet, um Richtliniendefinitionen und Zuweisungen abzurufen und um die Konformität des Clusters an Azure Policy zu melden:

   |Domain |Port |
   |---|---|
   |`data.policy.core.windows.net` |`443` |
   |`store.policy.core.windows.net` |`443` |
   |`login.windows.net` |`443` |
   |`dc.services.visualstudio.com` |`443` |

1. Weisen Sie dem Kubernetes-Cluster mit Azure Arc-Aktivierung die Rolle „Policy Insights Data Writer (Preview)“ (Policy Insights-Datenschreiber (Vorschau)) zu. Ersetzen Sie `<subscriptionId>` durch Ihre Abonnement-ID, `<rg>` durch die Ressourcengruppe des Kubernetes-Clusters mit Azure Arc-Aktivierung und `<clusterName>` durch den Namen des Kubernetes-Clusters mit Azure Arc-Aktivierung. Erfassen Sie die zurückgegebenen Werte für _appId_, _password_ und _tenant_. Sie werden für die Installationsschritte benötigt.

   - Azure CLI

     ```azurecli-interactive
     az ad sp create-for-rbac --role "Policy Insights Data Writer (Preview)" --scopes "/subscriptions/<subscriptionId>/resourceGroups/<rg>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"
     ```

   - Azure PowerShell

     ```azurepowershell-interactive
     $sp = New-AzADServicePrincipal -Role "Policy Insights Data Writer (Preview)" -Scope "/subscriptions/<subscriptionId>/resourceGroups/<rg>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"

     @{ appId=$sp.ApplicationId;password=[System.Runtime.InteropServices.Marshal]::PtrToStringAuto([System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($sp.Secret));tenant=(Get-AzContext).Tenant.Id } | ConvertTo-Json
     ```

   Beispielausgabe der obigen Befehle:

   ```json
   {
       "appId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
       "password": "bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb",
       "tenant": "cccccccc-cccc-cccc-cccc-cccccccccccc"
   }
   ```

Sind die obigen Voraussetzungen erfüllt, installieren Sie das Azure Policy-Add-On in Ihrem Kubernetes-Cluster mit Azure Arc-Aktivierung:

1. Fügen Sie das Azure Policy-Add-On-Repository zu Helm hinzu:

   ```bash
   helm repo add azure-policy https://raw.githubusercontent.com/Azure/azure-policy/master/extensions/policy-addon-kubernetes/helm-charts
   ```

1. Installieren Sie das Azure Policy-Add-On per Helm-Chart:

   ```bash
   # In below command, replace the following values with those gathered above.
   #    <AzureArcClusterResourceId> with your Azure Arc enabled Kubernetes cluster resource Id. For example: /subscriptions/<subscriptionId>/resourceGroups/<rg>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>
   #    <ServicePrincipalAppId> with app Id of the service principal created during prerequisites.
   #    <ServicePrincipalPassword> with password of the service principal created during prerequisites.
   #    <ServicePrincipalTenantId> with tenant of the service principal created during prerequisites.
   helm install azure-policy-addon azure-policy/azure-policy-addon-arc-clusters \
       --set azurepolicy.env.resourceid=<AzureArcClusterResourceId> \
       --set azurepolicy.env.clientid=<ServicePrincipalAppId> \
       --set azurepolicy.env.clientsecret=<ServicePrincipalPassword> \
       --set azurepolicy.env.tenantid=<ServicePrincipalTenantId>
   ```

   Weitere Informationen dazu, was mit dem Helm-Diagramm des Add-Ons installiert wird, finden Sie in der [Definition des Helm-Diagramms für das Azure Policy Add-On](https://github.com/Azure/azure-policy/tree/master/extensions/policy-addon-kubernetes/helm-charts/azure-policy-addon-arc-clusters) auf GitHub.

Führen Sie den folgenden Befehl aus, um zu überprüfen, ob die Installation des Add-Ons erfolgreich war und die Pods _azure-policy_ und _gatekeeper_ ausgeführt werden:

```bash
# azure-policy pod is installed in kube-system namespace
kubectl get pods -n kube-system

# gatekeeper pod is installed in gatekeeper-system namespace
kubectl get pods -n gatekeeper-system
```

## <a name="install-azure-policy-add-on-for-aks-engine-preview"></a><a name="install-azure-policy-add-on-for-aks-engine"></a>Installieren des Azure Policy-Add-Ons für die AKS-Engine (Vorschau)

> Hinweis: Das Azure Policy-Add-On für die AKS-Engine wird bald eingestellt. Es wird empfohlen, stattdessen die [Azure Policy-Erweiterung für Kubernetes mit Arc-Unterstützung](#install-azure-policy-extension-for-azure-arc-enabled-kubernetes) zu installieren.

1. Stellen Sie sicher, dass Ihr Kubernetes-Cluster eine unterstützte Distribution ist.

Bevor Sie das Azure Policy-Add-On installieren oder eines der Dienstfeatures aktivieren, muss in Ihrem Abonnement der **Microsoft.PolicyInsights**-Ressourcenanbieter aktiviert und eine Rollenzuweisung für den Clusterdienstprinzipal erstellt werden.

1. Azure CLI-Version 2.0.62 oder höher muss installiert und konfiguriert sein. Führen Sie `az --version` aus, um die Version zu ermitteln. Installations- und Upgradeinformationen finden Sie bei Bedarf unter [Installieren von Azure CLI](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-cli).

1. Zum Aktivieren des Ressourcenanbieters führen Sie die Schritte unter [Ressourcenanbieter und -typen](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal) aus, oder führen Sie entweder den Azure CLI- oder Azure PowerShell-Befehl aus:

   - Azure CLI

     ```azurecli-interactive
     # Log in first with az login if you're not using Cloud Shell

     # Provider register: Register the Azure Policy provider
     az provider register --namespace 'Microsoft.PolicyInsights'
     ```

   - Azure PowerShell

     ```azurepowershell-interactive
     # Log in first with Connect-AzAccount if you're not using Cloud Shell

     # Provider register: Register the Azure Policy provider
     Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
     ```

1. Erstellen Sie eine Rollenzuweisung für den Clusterdienstprinzipal.

   - Wenn Ihnen die App-ID des Clusterdienstprinzipals nicht bekannt ist, suchen Sie mit dem folgenden Befehl danach.

     ```bash
     # Get the kube-apiserver pod name
     kubectl get pods -n kube-system

     # Find the aadClientID value
     kubectl exec <kube-apiserver pod name> -n kube-system cat /etc/kubernetes/azure.json
     ```

   - Weisen Sie der App-ID des Clusterdienstprinzipals (Wert _aadClientID_ aus dem vorherigen Schritt) mit der Azure CLI die Rolle „Policy Insights Data Writer (Preview)“ zu. Ersetzen Sie `<subscriptionId>` durch Ihre Abonnement-ID und `<aks engine cluster resource group>` durch die Ressourcengruppe, in der sich der selbstverwaltete Kubernetes-Cluster der AKS-Engine befindet.

     ```azurecli-interactive
     az role assignment create --assignee <cluster service principal app ID> --scope "/subscriptions/<subscriptionId>/resourceGroups/<aks engine cluster resource group>" --role "Policy Insights Data Writer (Preview)"
     ```

Sind die obigen Voraussetzungen erfüllt, installieren Sie das Azure Policy-Add-On. Die Installation kann während des Erstellungs- oder Aktualisierungszyklus einer AKS-Engine oder als unabhängige Aktion auf einem vorhandenen Cluster erfolgen.

- Installation während des Erstellungs- oder Aktualisierungszyklus

  Um das Azure Policy Add-On während der Erstellung eines neuen selbstverwalteten Clusters oder als Update für einen vorhandenen Cluster zu aktivieren, schließen Sie die Eigenschaft [addons](https://github.com/Azure/aks-engine/tree/master/examples/addons/azure-policy) in die Clusterdefinition für die AKS-Engine ein.

  ```json
  "addons": [{
      "name": "azure-policy",
      "enabled": true
  }]
  ```

  Weitere Informationen dazu finden Sie im externen Leitfaden zur [Clusterdefinition für die AKS-Engine](https://github.com/Azure/aks-engine/blob/master/docs/topics/clusterdefinitions.md).

- Installation in vorhandenem Cluster mit Helm-Diagrammen

  Führen Sie die folgenden Schritte aus, um den Cluster vorzubereiten und das Add-On zu installieren:

  1. Installieren Sie [Helm 3](https://v3.helm.sh/docs/intro/install/).

  1. Fügen Sie Helm das Azure Policy-Repository hinzu.

     ```bash
     helm repo add azure-policy https://raw.githubusercontent.com/Azure/azure-policy/master/extensions/policy-addon-kubernetes/helm-charts
     ```

     Weitere Informationen finden Sie in der [Schnellstartanleitung zum Helm-Diagramm](https://helm.sh/docs/using_helm/#quickstart-guide).

  1. Installieren Sie das Add-On mit einem Helm-Diagramm. Ersetzen Sie `<subscriptionId>` durch Ihre Abonnement-ID und `<aks engine cluster resource group>` durch die Ressourcengruppe, in der sich der selbstverwaltete Kubernetes-Cluster der AKS-Engine befindet.

     ```bash
     helm install azure-policy-addon azure-policy/azure-policy-addon-aks-engine --set azurepolicy.env.resourceid="/subscriptions/<subscriptionId>/resourceGroups/<aks engine cluster resource group>"
     ```

     Weitere Informationen dazu, was mit dem Helm-Diagramm des Add-Ons installiert wird, finden Sie in der [Definition des Helm-Diagramms für das Azure Policy Add-On](https://github.com/Azure/azure-policy/tree/master/extensions/policy-addon-kubernetes/helm-charts/azure-policy-addon-aks-engine) auf GitHub.

     > [!NOTE]
     > Aufgrund der Beziehung zwischen Azure Policy-Add-On und Ressourcengruppen-ID unterstützt Azure Policy nur einen AKS-Engine-Cluster für jede Ressourcengruppe.

Führen Sie den folgenden Befehl aus, um zu überprüfen, ob die Installation des Add-Ons erfolgreich war und die Pods _azure-policy_ und _gatekeeper_ ausgeführt werden:

```bash
# azure-policy pod is installed in kube-system namespace
kubectl get pods -n kube-system

# gatekeeper pod is installed in gatekeeper-system namespace
kubectl get pods -n gatekeeper-system
```

## <a name="policy-language"></a>Richtliniensprache

Die Struktur der Azure Policy-Sprache für die Verwaltung von Kubernetes orientiert sich an der Struktur bereits vorhandener Richtliniendefinitionen. Mit einem [Ressourcenanbietermodus](./definition-structure.md#resource-provider-modes) von `Microsoft.Kubernetes.Data` werden die Auswirkungen von [audit](./effects.md#audit) und [deny](./effects.md#deny) zum Verwalten Ihrer Kubernetes-Cluster verwendet. _Audit_ und _deny_ müssen **details**-Eigenschaften bereitstellen, die für das Arbeiten mit dem [OPA Constraint Framework](https://github.com/open-policy-agent/frameworks/tree/master/constraint) und Gatekeeper v3 spezifisch sind.

Als Teil der Eigenschaften _details.templateInfo_, _details.constraint_ und _details.constraintTemplate_ in der Richtliniendefinition übergibt Azure Policy die URIs oder Base64-Werte dieser [CustomResourceDefinitions](https://open-policy-agent.github.io/gatekeeper/website/docs/howto/#constraint-templates) (CRD) an das Add-On. Rego ist die Sprache, die von OPA und Gatekeeper unterstützt wird, um eine Anforderung an den Kubernetes-Cluster zu validieren. Durch die Unterstützung eines bestehenden Standards für das Kubernetes-Management ermöglicht Azure Policy die Wiederverwendung bestehender Regeln und deren Kombination mit Azure Policy für eine einheitliche Berichterstellungsumgebung zur Cloud-Compliance. Weitere Informationen finden Sie unter [Was ist Rego?](https://www.openpolicyagent.org/docs/latest/policy-language/#what-is-rego)

## <a name="assign-a-policy-definition"></a>Zuweisen einer Richtliniendefinition

Um Ihrem Kubernetes-Cluster eine Richtliniendefinition zuweisen zu können, müssen Ihnen die entsprechenden Azure RBAC-Richtlinienzuweisungsvorgänge (Azure Role-Based Access Control, rollenbasierte Zugriffssteuerung) zugewiesen sein. Die in Azure integrierten Rollen **Mitwirkender bei Ressourcenrichtlinien** und **Besitzer** verfügen über diese Vorgänge. Weitere Informationen finden Sie unter [Azure RBAC-Berechtigungen in Azure Policy](../overview.md#azure-rbac-permissions-in-azure-policy).

> [!NOTE]
> Benutzerdefinierte Richtliniendefinitionen sind eine _öffentliche Previewfunktion_.

Führen Sie die folgenden Schritte aus, um die integrierten Richtliniendefinitionen für die Verwaltung Ihres Clusters über das Azure-Portal zu finden. Wenn Sie eine benutzerdefinierte Richtliniendefinition verwenden, suchen Sie nach deren Namen oder der Kategorie, mit der Sie sie erstellt haben.

1. Starten Sie den Azure Policy-Dienst im Azure-Portal. Wählen Sie **Alle Dienste** im linken Bereich aus, suchen Sie dann nach der Option **Richtlinie** und wählen Sie sie aus.

1. Wählen Sie im linken Bereich der Seite „Azure Policy“ die Option **Definitionen** aus.

1. Verwenden Sie im Dropdown-Listenfeld „Kategorie“ die Option **Alle auswählen**, um den Filter zu löschen, und wählen Sie dann **Kubernetes** aus.

1. Wählen Sie die Richtliniendefinition und dann die Schaltfläche **Zuweisen** aus.

1. Legen Sie den **Bereich** auf die Verwaltungsgruppe, das Abonnement oder die Ressourcengruppe des Kubernetes-Clusters fest, in dem die Richtlinienzuweisung angewendet wird.

   > [!NOTE]
   > Beim Zuweisen der Definition für Azure Policy für Kubernetes muss der **Bereich** die Clusterressource enthalten. Bei einem AKS-Engine-Cluster muss der **Bereich** die Ressourcengruppe des Clusters sein.

1. Weisen Sie der Richtlinienzuweisung einen **Namen** und eine **Beschreibung** zu, die Sie zur einfachen Identifizierung verwenden können.

1. Legen Sie die [Policy enforcement](./assignment-structure.md#enforcement-mode) (Richtlinienerzwingung) auf einen der unten aufgeführten Werte fest.

   - **Aktiviert**: Erzwingen Sie die Richtlinie auf dem Cluster. Kubernetes-Zulassungsanforderungen mit Verstößen werden verweigert.

   - **Deaktiviert**: Erzwingen Sie die Richtlinie nicht auf dem Cluster. Kubernetes-Zulassungsanforderungen mit Verstößen werden nicht verweigert. Die Ergebnisse der Konformitätsbewertung sind weiterhin verfügbar. Beim Rollout neuer Richtliniendefinitionen für aktive Cluster ist die Option _Deaktiviert_ hilfreich, um die Richtliniendefinition zu testen, da Zulassungsanforderungen mit Verstößen nicht verweigert werden.

1. Wählen Sie **Weiter** aus.

1. Festlegen von **Parameterwerten**

   - Geben Sie die Liste der Namespaces im Parameter **Namespaceausschlüsse** an, um Kubernetes-Namespaces aus der Richtlinienauswertung auszuschließen. Es wird empfohlen, Folgendes auszuschließen: _kube-system_, _gatekeeper-system_ und _azure-arc_.

1. Klicken Sie auf **Überprüfen + erstellen**.

Verwenden Sie alternativ [Schnellstart: Erstellen einer Richtlinienzuweisung zum Identifizieren nicht konformer Ressourcen](../assign-policy-portal.md), um eine Kubernetes-Richtlinie zu finden und zuzuweisen. Suchen Sie nach einer Kubernetes-Richtliniendefinition anstelle des Musters „audit vms“.

> [!IMPORTANT]
> Integrierte Richtliniendefinitionen stehen für Kubernetes-Cluster in der Kategorie **Kubernetes** zur Verfügung. Eine Liste der integrierten Richtliniendefinitionen finden Sie unter [Kubernetes-Beispiele](../samples/built-in-policies.md#kubernetes).

## <a name="policy-evaluation"></a>Richtlinienauswertung

Das Add-On prüft alle 15 Minuten mit dem Azure Policy-Dienst, ob sich die Richtlinienzuweisungen geändert haben.
Während dieses Aktualisierungszyklus nimmt das Add-On eine Prüfung auf Änderungen vor. Diese Änderungen lösen das Erstellen, Aktualisieren oder Löschen der Einschränkungsvorlagen und Einschränkungen aus.

Wenn ein Namespace in einem Kubernetes-Cluster über die dem Cluster entsprechende Bezeichnungen verfügt, werden die Zulassungsanforderungen mit Verstößen nicht verweigert. Die Ergebnisse der Konformitätsbewertung sind weiterhin verfügbar.

- Kubernetes-Cluster mit Azure Arc-Unterstützung: `admission.policy.azure.com/ignore`
- Azure Kubernetes Service-Cluster: `control-plane`

> [!NOTE]
> Ein Clusteradministrator verfügt zwar möglicherweise über die Berechtigung zum Erstellen und Aktualisieren von Einschränkungsvorlagen und -ressourcen, die durch das Azure Policy-Add-On installiert wurden, hierbei handelt es sich jedoch nicht um unterstützte Szenarien, da manuelle Updates überschrieben werden. Richtlinien, die vor der Installation des Add-Ons und vor der Zuweisung von Azure Policy-Richtliniendefinitionen vorhanden waren, werden weiterhin von Gatekeeper ausgewertet.

Das Add-On fordert alle 15 Minuten einen vollständigen Scan des Clusters an. Nachdem Details des vollständigen Scans und alle Echtzeitauswertungen von versuchten Änderungen am Cluster durch Gatekeeper erfasst wurden, meldet das Add-On die Ergebnisse an Azure Policy zurück, um sie in [Konformitätsdetails](../how-to/get-compliance-data.md) wie bei allen Azure Policy-Zuweisungen aufzunehmen. Während des Prüfzyklus werden nur Ergebnisse für aktive Richtlinienzuweisungen zurückgegeben. Prüfungsergebnisse können auch als [Violations](https://github.com/open-policy-agent/gatekeeper#audit) (Verstöße) angezeigt werden, die im Statusfeld der fehlerhaften Einschränkung aufgeführt sind. Weitere Informationen zu _nicht konformen_ Ressourcen finden Sie unter [Komponentendetails für Ressourcenanbietermodi](../how-to/determine-non-compliance.md#component-details-for-resource-provider-modes).

> [!NOTE]
> Jeder Konformitätsbericht in Azure Policy für Ihre Kubernetes-Cluster umfasst sämtliche Verstöße der letzten 45 Minuten. Der Zeitstempel gibt an, wann ein Verstoß aufgetreten ist.

Einige weitere Überlegungen:

- Wenn das Clusterabonnement bei Azure Security Center registriert ist, werden automatisch Kubernetes-Richtlinien von Azure Security Center auf den Cluster angewendet.

- Wenn eine Ablehnungsrichtlinie auf einen Cluster mit vorhandenen Kubernetes-Ressourcen angewendet wird, werden alle bereits vorhandenen Ressourcen, die nicht mit der neuen Richtlinie konform sind, weiterhin ausgeführt. Wenn die nicht konforme Ressource auf einem anderen Knoten neu geplant wird, wird die Ressourcenerstellung durch Gatekeeper blockiert.

- Wenn ein Cluster über eine Ablehnungsrichtlinie verfügt, durch die Ressourcen überprüft werden, wird dem Benutzer beim Erstellen einer Bereitstellung keine Ablehnungsmeldung angezeigt. Stellen Sie sich beispielsweise eine Kubernetes-Bereitstellung vor, die Replikatgruppen und Pods enthält. Wenn ein Benutzer `kubectl describe deployment $MY_DEPLOYMENT` ausführt, wird im Rahmen von Ereignissen keine Ablehnungsmeldung zurückgegeben. Von `kubectl describe replicasets.apps $MY_DEPLOYMENT` werden jedoch die mit der Ablehnung zusammenhängenden Ereignisse zurückgegeben.

> [!NOTE]
> Init-Container können während der Richtlinienauswertung eingeschlossen werden. Um zu überprüfen, ob Init-Container enthalten sind, überprüfen Sie die CRD auf die folgende oder eine ähnliche Deklaration:
>
> ```rego
> input_containers[c] { 
>    c := input.review.object.spec.initContainers[_] 
> }
> ```

### <a name="constraint-template-conflicts"></a>Konflikte mit Einschränkungsvorlagen

Wenn Einschränkungsvorlagen denselben Ressourcenmetadatennamen haben, die Richtliniendefinition aber an verschiedenen Stellen auf die Quelle verweist, gelten die Richtliniendefinitionen als in Konflikt miteinander. Beispiel: Zwei Richtliniendefinitionen verweisen auf dieselbe Datei `template.yaml`, die an verschiedenen Quellspeicherorten gespeichert ist, z. B. im Azure Policy-Vorlagenspeicher (`store.policy.core.windows.net`) und auf GitHub.

Wenn Richtliniendefinitionen und ihre Einschränkungsvorlagen zugewiesen, aber noch nicht im Cluster installiert sind und in Konflikt miteinander stehen, werden sie als Konflikt gemeldet und erst im Cluster installiert, wenn der Konflikt behoben wurde. Ebenso funktionieren alle vorhandenen Richtliniendefinitionen und ihre Einschränkungsvorlagen, die sich bereits im Cluster befinden und mit neu zugewiesenen Richtliniendefinitionen in Konflikt stehen, weiterhin normal. Wenn eine vorhandene Zuweisung aktualisiert wird und die Einschränkungsvorlage nicht synchronisiert werden kann, wird der Cluster ebenfalls als konfliktbehaftet eingestuft. Informationen zu allen Konfliktmeldungen finden Sie unter [Konformitätsgründe für den AKS-Ressourcenanbietermodus](../how-to/determine-non-compliance.md#aks-resource-provider-mode-compliance-reasons).

## <a name="logging"></a>Protokollierung

Als Kubernetes-Controller/-Container speichern die Pods _azure-policy_ und _gatekeeper_ Protokolle im Kubernetes-Cluster. Die Protokolle können auf der Seite **Insights** des Kubernetes-Clusters verfügbar gemacht werden. Weitere Informationen finden Sie unter [Überwachen der Leistung von Kubernetes-Clustern mit Azure Monitor für Container](../../../azure-monitor/containers/container-insights-analyze.md).

Verwenden Sie `kubectl`, um die Add-On-Protokolle anzuzeigen:

```bash
# Get the azure-policy pod name installed in kube-system namespace
kubectl logs <azure-policy pod name> -n kube-system

# Get the gatekeeper pod name installed in gatekeeper-system namespace
kubectl logs <gatekeeper pod name> -n gatekeeper-system
```

Weitere Informationen finden Sie in der Gatekeeper-Dokumentation unter [Debuggen](https://open-policy-agent.github.io/gatekeeper/website/docs/debug/).

## <a name="view-gatekeeper-artifacts"></a>Anzeigen von Gatekeeper-Artefakten

Nachdem das Add-On die Richtlinienzuweisungen heruntergeladen und die Einschränkungsvorlagen und Einschränkungen im Cluster installiert hat, werden beide mit Azure Policy-Informationen wie der Richtlinienzuweisungs-ID und der Richtliniendefinitions-ID kommentiert. Führen Sie die folgenden Schritte aus, um Ihren Client zum Anzeigen der Add-On-bezogenen Artefakte zu konfigurieren:

1. Richten Sie `kubeconfig` für den Cluster ein.

   Verwenden Sie die folgende Azure CLI für einen Azure Kubernetes Service-Cluster:

   ```azurecli-interactive
   # Set context to the subscription
   az account set --subscription <YOUR-SUBSCRIPTION>

   # Save credentials for kubeconfig into .kube in your home folder
   az aks get-credentials --resource-group <RESOURCE-GROUP> --name <CLUSTER-NAME>
   ```

1. Testen Sie die Clusterverbindung.

   Führen Sie den Befehl `kubectl cluster-info` aus. Bei einer erfolgreichen Ausführung antwortet jeder Dienst mit einer URL, unter der er ausgeführt wird.

### <a name="view-the-add-on-constraint-templates"></a>Anzeigen der Add-On-Einschränkungsvorlagen

Führen Sie `kubectl get constrainttemplates` aus, um die vom Add-On heruntergeladenen Einschränkungsvorlagen anzuzeigen.
Einschränkungsvorlagen, die mit `k8sazure` beginnen, werden vom Add-On installiert.

### <a name="get-azure-policy-mappings"></a>Abrufen von Azure Policy-Zuordnungen

Verwenden Sie `kubectl get constrainttemplates <TEMPLATE> -o yaml`, um die Zuordnung zwischen einer Einschränkungsvorlage, die in den Cluster heruntergeladen wurde, und der Richtliniendefinition zu identifizieren. Die Ergebnisse sehen in etwa wie in der folgenden Ausgabe aus:

```yaml
apiVersion: templates.gatekeeper.sh/v1beta1
kind: ConstraintTemplate
metadata:
    annotations:
    azure-policy-definition-id: /subscriptions/<SUBID>/providers/Microsoft.Authorization/policyDefinitions/<GUID>
    constraint-template-installed-by: azure-policy-addon
    constraint-template: <URL-OF-YAML>
    creationTimestamp: "2021-09-01T13:20:55Z"
    generation: 1
    managedFields:
    - apiVersion: templates.gatekeeper.sh/v1beta1
    fieldsType: FieldsV1
...
```

`<SUBID>` ist die Abonnement-ID und `<GUID>` die ID der zugeordneten Richtliniendefinition.
`<URL-OF-YAML>` ist der Quellspeicherort der Einschränkungsvorlage, die das Add-On heruntergeladen hat, um es im Cluster zu installieren.

### <a name="view-constraints-related-to-a-constraint-template"></a>Anzeigen von Einschränkungen im Zusammenhang mit einer Einschränkungsvorlage

Sobald Sie über die Namen der [heruntergeladenen Add-On-Einschränkungsvorlagen](#view-the-add-on-constraint-templates) verfügen, können Sie den Namen verwenden, um die zugehörigen Einschränkungen anzuzeigen. Verwenden Sie `kubectl get <constraintTemplateName>`, um die Liste abzurufen.
Vom Add-On installierte Einschränkungen beginnen mit `azurepolicy-`.

### <a name="view-constraint-details"></a>Anzeigen von Einschränkungsdetails

Die Einschränkung enthält Details zu Verstößen und Zuordnungen zur Richtliniendefinition und -zuweisung. Verwenden Sie `kubectl get <CONSTRAINT-TEMPLATE> <CONSTRAINT> -o yaml`, um die Details anzuzeigen. Die Ergebnisse sehen in etwa wie in der folgenden Ausgabe aus:

```yaml
apiVersion: constraints.gatekeeper.sh/v1beta1
kind: K8sAzureContainerAllowedImages
metadata:
  annotations:
    azure-policy-assignment-id: /subscriptions/<SUB-ID>/resourceGroups/<RG-NAME>/providers/Microsoft.Authorization/policyAssignments/<ASSIGNMENT-GUID>
    azure-policy-definition-id: /providers/Microsoft.Authorization/policyDefinitions/<DEFINITION-GUID>
    azure-policy-definition-reference-id: ""
    azure-policy-setdefinition-id: ""
    constraint-installed-by: azure-policy-addon
    constraint-url: <URL-OF-YAML>
  creationTimestamp: "2021-09-01T13:20:55Z"
spec:
  enforcementAction: deny
  match:
    excludedNamespaces:
    - kube-system
    - gatekeeper-system
    - azure-arc
  parameters:
    imageRegex: ^.+azurecr.io/.+$
status:
  auditTimestamp: "2021-09-01T13:48:16Z"
  totalViolations: 32
  violations:
  - enforcementAction: deny
    kind: Pod
    message: Container image nginx for container hello-world has not been allowed.
    name: hello-world-78f7bfd5b8-lmc5b
    namespace: default
  - enforcementAction: deny
    kind: Pod
    message: Container image nginx for container hello-world has not been allowed.
    name: hellow-world-89f8bfd6b9-zkggg
```

## <a name="troubleshooting-the-add-on"></a>Behandeln von Problemen mit dem Add-On

Weitere Informationen zur Behandlung von Problemen im Zusammenhang mit dem Add-On für Kubernetes finden Sie im [Kubernetes-Abschnitt](../troubleshoot/general.md#add-on-for-kubernetes-general-errors) des Artikels zur Azure Policy-Problembehandlung.

Informationen zu Problemen im Zusammenhang mit der Azure Policy-Erweiterung für Arc finden Sie hier:
- [Problembehandlung bei Kubernetes mit Azure Arc-Aktivierung](/azure/azure-arc/kubernetes/troubleshooting#azure-arc-enabled-kubernetes-troubleshooting)

Informationen zu Problemen im Zusammenhang mit Azure Policy finden Sie hier:
- [Untersuchen von Azure Policy-Protokollen](/azure/governance/policy/concepts/policy-for-kubernetes#logging)
- [Allgemeine Problembehandlung für Azure Policy in Kubernetes](/azure/governance/policy/troubleshoot/general#add-on-for-kubernetes-general-errors)

## <a name="remove-the-add-on"></a>Entfernen des Add-Ons

### <a name="remove-the-add-on-from-aks"></a>Entfernen des Add-Ons aus AKS

Verwenden Sie zum Entfernen des Azure Policy-Add-Ons aus Ihrem AKS-Cluster entweder das Azure-Portal oder die Azure CLI:

- Azure-Portal

  1. Starten Sie den AKS-Dienst im Azure-Portal, indem Sie die Option **Alle Dienste** auswählen und dann nach **Kubernetes-Dienste** suchen und die entsprechende Option auswählen.

  1. Wählen Sie Ihren AKS-Cluster aus, in dem Sie das Azure Policy-Add-On deaktivieren möchten.

  1. Wählen Sie links **Richtlinien** auf der Seite des Kubernetes-Diensts aus.

  1. Wählen Sie auf der Hauptseite die Schaltfläche **Add-On deaktivieren** aus.

- Azure CLI

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks disable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

### <a name="remove-the-add-on-from-azure-arc-enabled-kubernetes"></a>Entfernen des Add-Ins aus Kubernetes mit Azure Arc-Aktivierung

Führen Sie den folgenden Helm-Befehl aus, um das Azure Policy-Add-On und Gatekeeper aus Ihrem Kubernetes-Cluster mit Azure Arc-Aktivierung zu entfernen:

```bash
helm uninstall azure-policy-addon
```

### <a name="remove-the-add-on-from-aks-engine"></a>Entfernen des Add-Ons aus der AKS-Engine

Wenn Sie das Azure Policy Add-On und Gatekeeper aus dem AKS-Engine-Cluster entfernen möchten, verwenden Sie die Methode, die der Installationsweise des Add-Ons entspricht:

- Bei Installation durch Festlegen der **addons**-Eigenschaft in der Clusterdefinition für die AKS-Engine:

  Stellen Sie die Clusterdefinition erneut für die AKS-Engine bereit, nachdem Sie die **addons**-Eigenschaft für _azure-policy_ in „false“ geändert haben:

  ```json
  "addons": [{
      "name": "azure-policy",
      "enabled": false
  }]
  ```

  Weitere Informationen finden Sie unter [Deaktivieren des Azure Policy-Add-Ons](https://github.com/Azure/aks-engine/blob/master/examples/addons/azure-policy/README.md#disable-azure-policy-add-on).

- Wenn die Installation mit Helm-Charts durchgeführt wurde, führen Sie den folgenden Helm-Befehl aus:

  ```bash
  helm uninstall azure-policy-addon
  ```

## <a name="diagnostic-data-collected-by-azure-policy-add-on"></a>Vom Azure Policy-Add-On gesammelte Diagnosedaten

Das Azure Policy-Add-On für Kubernetes sammelt begrenzte Clusterdiagnosedaten. Diese Diagnosedaten sind wichtige technische Daten in Bezug auf Software und Leistung. Sie werden für folgende Zwecke verwendet:

- Azure Policy Add-On auf dem neuesten Stand halten
- Azure Policy Add-On sicher, zuverlässig und leistungsfähig halten
- Azure Policy Add-On verbessern – durch die aggregierte Analyse der Verwendung des Add-Ons

Die vom Add-On gesammelten Informationen sind keine persönlichen Daten. Die folgenden Details werden derzeit gesammelt:

- Version des Azure Policy-Add-On-Agents
- Clustertyp
- Clusterregion
- Clusterressourcengruppe
- Clusterressourcen-ID
- Clusterabonnement-ID
- Clusterbetriebssystem (Beispiel: Linux)
- Ort für den Cluster (Beispiel: Seattle)
- Bundesland oder Kanton für den Cluster (Beispiel: Washington)
- Land oder Region für den Cluster (Beispiel: USA)
- Ausnahmen/Fehler, die während der Installation des Agents bei der Richtlinienauswertung durch das Azure Policy Add-On festgestellt werden
- Anzahl von Gatekeeper-Richtliniendefinitionen, die nicht durch das Azure Policy-Add-On installiert wurden

## <a name="next-steps"></a>Nächste Schritte

- Sehen Sie sich die Beispiele unter [Azure Policy-Beispiele](../samples/index.md) an.
- Befassen Sie sich mit der [Struktur von Azure Policy-Definitionen](definition-structure.md).
- Lesen Sie [Grundlegendes zu Richtlinienauswirkungen](effects.md).
- Informieren Sie sich über das [programmgesteuerte Erstellen von Richtlinien](../how-to/programmatically-create.md).
- Informieren Sie sich über das [Abrufen von Konformitätsdaten](../how-to/get-compliance-data.md).
- Erfahren Sie, wie Sie [nicht konforme Ressourcen korrigieren](../how-to/remediate-resources.md) können.
- Weitere Informationen zu Verwaltungsgruppen finden Sie unter [Organisieren Ihrer Ressourcen mit Azure-Verwaltungsgruppen](../../management-groups/overview.md).
