---
title: Dapr-Erweiterung für Azure Kubernetes Service (AKS) (Vorschau)
description: Installieren und konfigurieren Sie Dapr in Ihrem Azure Kubernetes Service-Cluster (AKS) mithilfe der Dapr-Clustererweiterung.
author: greenie-msft
ms.author: nigreenf
ms.service: container-service
ms.topic: article
ms.date: 10/15/2021
ms.custom: devx-track-azurecli, ignite-fall-2021
ms.openlocfilehash: 9d7a263a17d117aea162b39682fa5bd9ec4c1fde
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132485748"
---
# <a name="dapr-extension-for-azure-kubernetes-service-aks-preview"></a>Dapr-Erweiterung für Azure Kubernetes Service (AKS) (Vorschau)

[Dapr](https://dapr.io/) ist eine portable, ereignisgesteuerte Runtime, die es jedem Entwickler einfach macht, robuste, zustandslose und zustandsbehaftete Anwendungen zu erstellen, die in der Cloud und im Edge-Zustand ausgeführt werden und die Vielfalt der Sprachen und Entwicklerframeworks nutzen. Dapr nutzt die Vorteile einer Sidecar-Architektur und hilft Ihnen bei der Bewältigung der Herausforderungen, die mit der Entwicklung von Microservices verbunden sind, und sorgt dafür, dass Ihre Codeplattform unabhängig bleibt. Insbesondere hilft es bei der Lösung von Problemen im Zusammenhang mit Diensten, die andere Dienste zuverlässig und sicher aufrufen, beim Erstellen ereignisgesteuerter Apps mit pub-sub und beim Erstellen von Anwendungen, die über mehrere Clouddienste und Hosts portierbar sind (z. B. Kubernetes im Vergleich zu einem virtuellen Computer).

Durch die Verwendung der AKS-Dapr-Erweiterung zum Bereitstellen von Dapr in Ihrem AKS-Cluster entfällt der Aufwand für das Herunterladen von Dapr-Tools und das manuelle Installieren und Verwalten der Runtime in Ihrem AKS-Cluster. Darüber hinaus bietet die Erweiterung Unterstützung für alle [nativen Dapr-Konfigurationsfunktionen][dapr-configuration-options] über einfache Befehlszeilenargumente.

> [!NOTE]
> Wenn Sie Planen, Dapr in einer Kubernetes-Produktionsumgebung zu installieren, finden Sie weitere Informationen auf der Dokumentationsseite [dapr guidelines for production usage (Dapr-Richtlinien][kubernetes-production] für die Produktionsnutzung).

## <a name="how-it-works"></a>Funktionsweise

Die AKS Dapr-Erweiterung verwendet die Azure CLI, um die Dapr-Steuerungsebene in Ihrem AKS-Cluster bereitstellen zu können. Dadurch wird folgendes erstellt:

- **dapr-operator**: Verwaltet Komponentenupdates und Kubernetes-Dienstendpunkte für Dapr (Zustandsspeicher, Pub/Subs usw.).
- **dapr-sidecar-injector:** Fügt Dapr in bereitstellungspods mit Anmerkungen ein und fügt die Umgebungsvariablen hinzu und ermöglicht es benutzerdefinierten Anwendungen, problemlos mit Dapr zu kommunizieren, ohne `DAPR_HTTP_PORT` Dapr-Portwerte hart zu `DAPR_GRPC_PORT` codieren.
- **dapr-placement**: Wird nur für Akteure verwendet. Erstellt Zuordnungstabellen, die Actor-Instanzen Pods zuordnen.
- **dapr-sentry**: Verwaltet mTLS zwischen Diensten und fungiert als Zertifizierungsstelle. Weitere Informationen finden Sie unter [Sicherheitsübersicht][dapr-security].

Sobald Dapr in Ihrem AKS-Cluster installiert ist, wird für Ihre Anwendungsdienste nun der Dapr-Sidecar parallel ausgeführt. Dadurch können Sie sofort mit der Verwendung der Dapr-Baustein-APIs beginnen. Eine detailliertere Übersicht über die Baustein-APIs und deren optimale Verwendung finden Sie in der Übersicht über [Dapr-Bausteine.][building-blocks-concepts]

> [!WARNING]
> Wenn Sie Dapr über die AKS-Erweiterung installieren, wird empfohlen, die Erweiterung weiterhin für die zukünftige Verwaltung von Dapr anstelle der Dapr-CLI zu verwenden. Das Kombinieren der beiden Tools kann Konflikte verursachen und zu unerwünschtem Verhalten führen.

## <a name="supported-kubernetes-versions"></a>Unterstützte Kubernetes-Versionen

Die Dapr-Erweiterung verwendet das gleiche Supportfenster wie AKS. Weitere Informationen finden Sie unter [Richtlinie zur Unterstützung der Kubernetes-Version][k8s-version-support-policy].

## <a name="prerequisites"></a>Voraussetzungen 

- Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.
- Bevor Sie beginnen, installieren Sie die neueste Version der [Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli-windows) und die Erweiterung *aks-preview*.
- Wenn Sie keinen besitzen, müssen Sie einen solchen [AKS cluster][deploy-cluster] erstellen.


### <a name="register-the-aks-extensionmanager-and-aks-dapr-preview-features"></a>Registrieren der `AKS-ExtensionManager`- und `AKS-Dapr`-Previewfunktionen

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

Sie müssen die Featureflags `AKS-ExtensionManager` und `AKS-Dapr` in Ihrem Abonnement aktivieren, um einen AKS-Cluster zu erstellen, der eine hostbasierte Verschlüsselung verwendet.

Registrieren Sie die Featureflags `AKS-ExtensionManager` und `AKS-Dapr` mit dem Befehl [az feature register][az-feature-register], wie im folgenden Beispiel gezeigt:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "AKS-ExtensionManager"
az feature register --namespace "Microsoft.ContainerService" --name "AKS-Dapr"
```

Es dauert einige Minuten, bis der Status *Registered (Registriert)* angezeigt wird. Überprüfen Sie den Registrierungsstatus mithilfe des Befehls [az feature list][az-feature-list]:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-ExtensionManager')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-Dapr')].{Name:name,State:properties.state}"
```

Wenn Sie so weit sind, aktualisieren Sie die Registrierung der *Microsoft.KubernetesConfiguration* und *Microsoft.ContainerService* Ressourcenanbieter mit dem Befehl [az provider register][az-provider-register]:

```azurecli-interactive
az provider register --namespace Microsoft.KubernetesConfiguration
az provider register --namespace Microsoft.ContainerService
```

### <a name="setup-the-azure-cli-extension-for-cluster-extensions"></a>Einrichten der Azure CLI-Erweiterung für Clustererweiterungen

Sie benötigen auch die `k8s-extension`Azure CLI-Erweiterung. Führen Sie zur Installation die folgenden Befehle aus:
  
```azurecli-interactive
az extension add --name k8s-extension
```

Wenn die Erweiterung `k8s-extension` bereits installiert ist, können Sie sie mit dem folgenden Befehl auf die neueste Version aktualisieren:

```azurecli-interactive
az extension update --name k8s-extension
```

## <a name="create-the-extension-and-install-dapr-on-your-aks-cluster"></a>Erstellen der Erweiterung und Installieren von Dapr in Ihrem AKS-Cluster

Nachdem Ihr Abonnement für die Verwendung von Kubernetes-Erweiterungen registriert wurde, können Sie die Dapr-Erweiterung erstellen, die Dapr in Ihrem AKS-Cluster installiert. Beispiel:

```azure-cli-interactive
az k8s-extension create --cluster-type managedClusters \
--cluster-name myAKSCluster \
--resource-group myResourceGroup \
--name myDaprExtension \
--extension-type Microsoft.Dapr
```

Sie haben die Möglichkeit, Dapr die automatische Aktualisierung der Nebenversion zu erlauben, indem Sie den `--auto-upgrade-minor-version`Parameter angeben und den Wert auf `true` festlegen:

```azure-cli-interactive
--auto-upgrade-minor-version true
```

## <a name="configuration-settings"></a>Konfigurationseinstellungen

Mit der Erweiterung können Sie Dapr-Konfigurationsoptionen mithilfe des `--configuration-settings`-Parameters festlegen. Wenn Sie z. B. Dapr mit aktivierter Hochverfügbarkeit (High Availability, HA) bereitstellen, legen Sie den `global.ha.enabled` Parameter auf `true` fest:

```azure-cli-interactive
az k8s-extension create --cluster-type managedClusters \
--cluster-name myAKSCluster \
--resource-group myResourceGroup \
--name myDaprExtension \
--extension-type Microsoft.Dapr \
--auto-upgrade-minor-version true \
--configuration-settings "global.ha.enabled=true" \
--configuration-settings "dapr_operator.replicaCount=2"
```

> [!NOTE]
> Wenn Konfigurationseinstellungen vertraulich sind und geschützt werden müssen, z. B. zertifikatbezogene Informationen, übergeben Sie den -Parameter, und der Wert wird vor dem `--configuration-protected-settings` Lesen geschützt.

Wenn keine Konfigurationseinstellungen übergeben werden, ist die Dapr-Konfiguration standardmäßig wie die folgenden:

```yaml
  ha:
    enabled: true
    replicaCount: 3
    disruption:
      minimumAvailable: ""
      maximumUnavailable: "25%"
  prometheus:
    enabled: true
    port: 9090
  mtls:
    enabled: true
    workloadCertTTL: 24h
    allowedClockSkew: 15m
```

Eine Liste der verfügbaren Optionen finden Sie unter [Dapr-Konfiguration][dapr-configuration-options].

## <a name="targeting-a-specific-dapr-version"></a>Als Ziel für eine bestimmte Dapr-Version

> [!NOTE]
> Dapr wird mit einem rollierendem Fenster unterstützt, das nur die aktuelle und frühere Version enthält. Es liegt in Ihrer operativen Verantwortung, mit diesen unterstützten Versionen auf dem neuesten Stand zu bleiben. Wenn Sie über eine ältere Version von Dapr verfügen, müssen Sie möglicherweise Zwischenupgrades für eine unterstützte Version durchzuführen.

Dasselbe Befehlszeilenargument wird zum Installieren einer bestimmten Dapr-Version oder zum Roll back auf eine frühere Version verwendet. Legen `--auto-upgrade-minor-version` Sie auf `false` und `--version` auf die Version von Dapr fest, die Sie installieren möchten. Wenn der `version` Parameter weggelassen wird, installiert die Erweiterung die neueste Version von Dapr. So verwenden Sie z. B. Dapr X.X.X: 

```azure-cli-interactive
az k8s-extension create --cluster-type managedClusters \
--cluster-name myAKSCluster \
--resource-group myResourceGroup \
--name myDaprExtension \
--extension-type Microsoft.Dapr \
--auto-upgrade-minor-version false \
--version X.X.X
```

## <a name="limiting-the-extension-to-certain-nodes-nodeselector"></a>Beschränken der Erweiterung auf bestimmte Knoten (`nodeSelector`)

In manchen Konfiguration kann es sein, dass sie Dapr ausführen müssen. Dies kann durch die Eingabe einer `nodeSelector`in der Erweiterungskonfiguration bewerkstelligt werden. Beachten Sie: Wenn das gewünschte `nodeSelector` eine `.` enthält, müssen Sie sie aus der Shell und der Erweiterung extrahieren. Zum Beispiel wird die folgende Konfiguration Dapr nur auf Knoten mit `kubernetes.io/os=linux`installieren:

```azure-cli-interactive
az k8s-extension create --cluster-type managedClusters \
--cluster-name myAKSCluster \
--resource-group myResourceGroup \
--name myDaprExtension \
--extension-type Microsoft.Dapr \
--auto-upgrade-minor-version true \
--configuration-settings "global.ha.enabled=true" \
--configuration-settings "dapr_operator.replicaCount=2" \
--configuration-settings "global.nodeSelector.kubernetes\.io/os=linux"
```

## <a name="show-current-configuration-settings"></a>Zeigen Sie die aktuellen Konfigurationseinstellungen an

Verwenden Sie den `az k8s-extension show` Befehl, um die aktuellen Dapr-Konfigurationseinstellungen zu zeigen:  

```azure-cli-interactive
az k8s-extension show --cluster-type managedClusters \
--cluster-name myAKSCluster \
--resource-group myResourceGroup \
--name myDaprExtension
```

## <a name="update-configuration-settings"></a>Konfigurationseinstellungen aktualisieren

> [!IMPORTANT]
> Einige Konfigurationsoptionen können nach der Erstellung nicht mehr geändert werden. Anpassungen an diesen Optionen erfordern das Löschen und die erneute Erstellung der Erweiterung. Dies gilt für die folgenden Einstellungen:
> * `global.ha.*`
> * `dapr_placement.*`

> [!NOTE]
> Hochverfügbarkeit (High Availability, HA) kann jederzeit aktiviert werden. Nach der Aktivierung muss die Erweiterung jedoch gelöscht und neu geschaltet werden. Wenn Sie nicht sicher sind, ob Hochverfügbarkeit für Ihren Anwendungsfall erforderlich ist, empfiehlt es sich, mit deaktiviert zu beginnen, um Unterbrechungen zu minimieren.

Um Ihre Dapr-Konfigurationseinstellungen zu aktualisieren, erstellen Sie einfach die Erweiterung mit dem gewünschten Zustand neu. Angenommen, wir haben die Erweiterung zuvor mit der folgenden Konfiguration erstellt und installiert:

```azurecli-interactive
az k8s-extension create --cluster-type managedClusters \
--cluster-name myAKSCluster \
--resource-group myResourceGroup \
--name myDaprExtension \
--extension-type Microsoft.Dapr \
--auto-upgrade-minor-version true \  
--configuration-settings "global.ha.enabled=true" \
--configuration-settings "dapr_operator.replicaCount=2" 
```

Um den von `dapr_operator.replicaCount` 2 auf 3 zu aktualisieren, verwenden Sie Folgendes:

```azurecli-interactive
az k8s-extension create --cluster-type managedClusters \
--cluster-name myAKSCluster \
--resource-group myResourceGroup \
--name myDaprExtension \
--extension-type Microsoft.Dapr \
--auto-upgrade-minor-version true \
--configuration-settings "global.ha.enabled=true" \
--configuration-settings "dapr_operator.replicaCount=3"
```

## <a name="troubleshooting-extension-errors"></a>Problembehandlung bei Erweiterungsfehlern

Wenn die Erweiterung nicht erstellt oder aktualisiert werden kann, können Sie überprüfen, wo bei der Erstellung der Erweiterung ein Fehler auftritt, indem Sie den Befehl `az k8s-extension list` ausführen. Wenn beispielsweise ein falscher Schlüssel in den Konfigurationseinstellungen verwendet wird, z. B. `global.ha=false` anstelle von `global.ha.enabled=false`: 

```azure-cli-interactive
az k8s-extension list --cluster-type managedClusters --cluster-name myAKSCluster --resource-group myResourceGroup
```

Der folgende JSON-Code wird zurückgegeben, und die Fehlermeldung wird in der -Eigenschaft `message` erfasst.

```json
"statuses": [
      {
        "code": "InstallationFailed",
        "displayStatus": null,
        "level": null,
        "message": "Error: {failed to install chart from path [] for release [dapr-1]: err [template: dapr/charts/dapr_sidecar_injector/templates/dapr_sidecar_injector_poddisruptionbudget.yaml:1:17: executing \"dapr/charts/dapr_sidecar_injector/templates/dapr_sidecar_injector_poddisruptionbudget.yaml\" at <.Values.global.ha.enabled>: can't evaluate field enabled in type interface {}]} occurred while doing the operation : {Installing the extension} on the config",
        "time": null
      }
],
```

## <a name="delete-the-extension"></a>Entfernen der Erweiterung

Wenn Sie die Erweiterung löschen und Dapr aus Ihrem AKS-Cluster entfernen müssen, können Sie den folgenden Befehl verwenden: 

```azure-cli-interactive
az k8s-extension delete --resource-group myResourceGroup --cluster-name myAKSCluster --cluster-type managedClusters --name myDaprExtension
```

## <a name="next-steps"></a>Nächste Schritte

- Nachdem Sie Dapr erfolgreich in Ihrem AKS-Cluster bereitgestellt haben, versuchen Sie, eine [Beispielanwendung][sample-application] bereitzustellen.

<!-- LINKS INTERNAL -->
[deploy-cluster]: ./tutorial-kubernetes-deploy-cluster.md
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[sample-application]: ./quickstart-dapr.md
[k8s-version-support-policy]: ./supported-kubernetes-versions.md?tabs=azure-cli#kubernetes-version-support-policy

<!-- LINKS EXTERNAL -->
[kubernetes-production]: https://docs.dapr.io/operations/hosting/kubernetes/kubernetes-production
[building-blocks-concepts]: https://docs.dapr.io/developing-applications/building-blocks/
[dapr-configuration-options]: https://github.com/dapr/dapr/blob/master/charts/dapr/README.md#configuration
[sample-application]: https://github.com/dapr/quickstarts/tree/master/hello-kubernetes#step-2---create-and-configure-a-state-store
[dapr-security]: https://docs.dapr.io/concepts/security-concept/
