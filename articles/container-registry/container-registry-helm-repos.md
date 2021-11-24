---
title: Speichern von Helm-Diagrammen
description: Erfahren Sie, wie Sie Helm-Charts für Ihre Kubernetes-Anwendungen mithilfe von Depots in Azure Container Registry speichern.
ms.topic: article
ms.date: 10/20/2021
ms.openlocfilehash: 5c96df6458a1f1fc40f4033d367c988c246a0fde
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2021
ms.locfileid: "132135773"
---
# <a name="push-and-pull-helm-charts-to-an-azure-container-registry"></a>Pushen und Pullen von Helm-Charts in Azure Container Registry

Sie können den [Open Source-Paket-Manager von Helm][helm] verwenden, um Anwendungen für Kubernetes ohne großen Aufwand zu verwalten und bereitzustellen. Bei Helm werden Anwendungspakete als [Charts](https://helm.sh/docs/topics/charts/) definiert, die gesammelt und in einem [Helm-Chartrepository](https://helm.sh/docs/topics/chart_repository/) gespeichert werden.

In diesem Artikel erfahren Sie, wie Sie mithilfe von Helm 3-Befehlen und durch Speichern von Charts als [OCI-Artefakte](container-registry-image-formats.md#oci-artifacts) Helm-Chartrepositorys in einer Azure-Containerregistrierung hosten. In vielen Szenarien würden Sie für Ihre selbst entwickelten Anwendungen eigene Charts erstellen und hochladen. Weitere Informationen zum Erstellen eigener Helm-Charts finden Sie unter [Entwicklerhandbuch für Chartvorlagen][develop-helm-charts]. Sie können auch ein vorhandenes Helm-Chart aus einem anderen Helm-Repository speichern.

> [!IMPORTANT]
> Dieser Artikel wurde mit Helm 3-Befehlen der Version **3.7.1** aktualisiert. Helm 3.7.1 enthält Änderungen an Helm CLI-Befehlen und der OCI-Unterstützung, die in früheren Versionen von Helm 3 eingeführt wurden. 

## <a name="helm-3-or-helm-2"></a>Helm 3 oder Helm 2?

Zum Speichern, Verwalten und Installieren von Helm-Charts verwenden Sie Befehle in der Helm-Befehlszeilenschnittstelle. Hauptreleases von Helm umfassen Helm 3 und Helm 2. Ausführliche Informationen zu den Versionsunterschieden finden Sie in den [häufig gestellten Fragen (FAQ) zur Version](https://helm.sh/docs/faq/). 

Helm 3 sollte zum Hosten von Helm-Charts in Azure Container Registry verwendet werden. Mit Helm 3 haben Sie folgende Möglichkeiten:

* Speichern und Verwalten von Helm-Charts in Repositorys in einer Azure-Containerregistrierung.
* Speichern von Helm-Charts in einer Registrierung als [OCI-Artefakte](container-registry-image-formats.md#oci-artifacts). Azure Container Registry bietet allgemeine Unterstützung für OCI-Artefakte, einschließlich Helm-Charts.
* Authentifizieren bei Ihrer Registrierung mithilfe des Befehls `helm registry login` oder `az acr login`.
* Pushen, Pullen und Verwalten von Helm-Charts in einer Registrierung mithilfe von `helm`-Befehlen.
* Verwenden Sie `helm install`, um Charts aus der Registrierung in einem Kubernetes-Cluster zu installieren.

### <a name="feature-support"></a>Featureunterstützung

Azure Container Registry unterstützt bestimmte Verwaltungsfeatures für Helm-Charts, je nachdem, ob Sie Helm 3 (aktuell) oder Helm 2 (veraltet) verwenden.

| Komponente | Helm 2 | Helm 3 |
| ---- | ---- | ---- |
| Verwalten von Charts mithilfe von `az acr helm`-Befehlen | :heavy_check_mark: | |
| Speichern von Charts als OCI-Artefakte | | :heavy_check_mark:  |
| Verwalten von Charts mithilfe von `az acr repository`-Befehlen und über das Blatt **Repositorys** im Azure-Portal| | :heavy_check_mark:  |


> [!NOTE]
> Ab Helm 3 gelten [az acr helm][az-acr-helm]-Befehle für die Verwendung mit dem Helm 2-Client als veraltet. Sie werden mindestens drei Monate im Voraus über das Entfernen eines Befehls informiert.

### <a name="chart-version-compatibility"></a>Kompatibilität von Chartversionen

Die folgenden Helm-[Chartversionen](https://helm.sh/docs/topics/charts/#the-apiversion-field) können in Azure Container Registry gespeichert und von den Helm 2- und Helm 3-Clients installiert werden. 

| Version | Helm 2 | Helm 3 |
| ---- | ---- | ---- |
| apiVersion v1 | :heavy_check_mark: | :heavy_check_mark: |
| apiVersion v2 | | :heavy_check_mark: |

### <a name="migrate-from-helm-2-to-helm-3"></a>Migrieren von Helm 2 zu Helm 3

Wenn Sie zuvor Charts mithilfe von Helm 2 und Azure Container Registry gespeichert und bereitgestellt haben, empfiehlt sich eine Migration zu Helm 3. Thema

* [Migrieren von Helm 2 zu 3](https://helm.sh/docs/topics/v2_v3_migration/) in der Helm-Dokumentation
* [Migrieren Ihrer Registrierung zum Speichern von Helm-OCI-Artefakten](#migrate-your-registry-to-store-helm-oci-artifacts) weiter unten in diesem Artikel

## <a name="prerequisites"></a>Voraussetzungen

Die folgenden Ressourcen sind für das Szenario in diesem Artikel erforderlich:

- Eine **Azure Container Registry**-Instanz in Ihrem Azure-Abonnement. Erstellen Sie bei Bedarf eine Registrierung im [Azure-Portal](container-registry-get-started-portal.md) oder über die [Azure-Befehlszeilenschnittstelle](container-registry-get-started-azure-cli.md).
- **Version 3.7.1 oder höher des Helm-Clients:** Führen Sie `helm version` aus, um Ihre aktuelle Version zu ermitteln. Weitere Informationen zum Installieren und Aktualisieren von Helm finden Sie unter [Installieren von Helm][helm-install]. Wenn Sie ein Upgrade von einer früheren Version von Helm 3 durchführen, lesen Sie die [Versionshinweise](https://github.com/helm/helm/releases).
- **Ein Kubernetes-Cluster**, in dem Sie später ein Helm-Chart installieren. Erstellen Sie bei Bedarf einen [Azure Kubernetes Service-Cluster][aks-quickstart]. 
- **Version 2.0.71 oder höher der Azure-Befehlszeilenschnittstelle:** Führen Sie `az --version` aus, um die Version zu ermitteln. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI][azure-cli-install].

## <a name="enable-oci-support"></a>Aktivieren der OCI-Unterstützung

Verwenden Sie den Befehl `helm version`, um zu überprüfen, ob Sie Helm 3 installiert haben:

```console
helm version
```

Legen Sie die folgende Umgebungsvariable fest, um die OCI-Unterstützung im Helm 3-Client zu aktivieren. Diese Unterstützung ist derzeit experimentell und kann noch geändert werden. 

```console
export HELM_EXPERIMENTAL_OCI=1
```

Legen Sie die folgenden Umgebungsvariablen für die Zielregistrierung fest. Der ACR_NAME ist der Name der Registrierungsressource. Wenn die URL der ACR-Registrierung myregistry.azurecr.io ist, legen Sie ACR_NAME auf myregistry fest.

```console
ACR_NAME=<container-registry-name>
```

## <a name="create-a-sample-chart"></a>Erstellen eines Beispielcharts

Erstellen Sie mithilfe der folgenden Befehle ein Testchart:

```console
mkdir helmtest

cd helmtest
helm create hello-world
```

Wechseln Sie für dieses einfache Beispiel zunächst in den Ordner `templates`, und löschen Sie den Inhalt des Ordners:

```console
cd hello-world/templates
rm -rf *
```

Erstellen Sie im Ordner `templates` eine Datei namens `configmap.yaml`, indem Sie den folgenden Befehl ausführen:

```console
cat <<EOF > configmap.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: hello-world-configmap
data:
  myvalue: "Hello World"
EOF
```

Weitere Informationen zum Erstellen und Ausführen dieses Beispiels finden Sie in der Helm-Dokumentation unter [Erste Schritte](https://helm.sh/docs/chart_template_guide/getting_started/).

## <a name="save-chart-to-local-archive"></a>Speichern eines Charts im lokalen Archiv

Wechseln Sie in das Unterverzeichnis `hello-world`. Führen Sie dann `helm package` aus, um das Chart in einem lokalen Archiv zu speichern. 

Im folgenden Beispiel wird das Chart mit dem Namen und der Version in `Chart.yaml` gespeichert.

```console
cd ..
helm package .
```

Die Ausgabe Die Ausgabe lautet in etwa wie folgt:

```output
Successfully packaged chart and saved it to: /my/path/hello-world-0.1.0.tgz
```

## <a name="authenticate-with-the-registry"></a>Authentifizieren bei der Registrierung

Führen Sie `helm registry login` aus, um sich bei der Registrierung zu authentifizieren. Sie können für Ihr Szenario geeignete [Registrierungsanmeldeinformationen](container-registry-authentication.md) übergeben, z. B. Anmeldeinformationen für den Dienstprinzipal, die Benutzeridentität oder ein Token für den Repositorygültigkeitsbereich.

- Authentifizieren Sie sich für die Registrierung mit einem [Azure Active Directory-Dienstprinzipal mit Pull- und Pushberechtigung](container-registry-auth-service-principal.md#create-a-service-principal) (Rolle AcrPush).
  ```bash
  SERVICE_PRINCIPAL_NAME=<acr-helm-sp>
  ACR_REGISTRY_ID=$(az acr show --name $ACR_NAME --query id --output tsv)
  PASSWORD=$(az ad sp create-for-rbac --name $SERVICE_PRINCIPAL_NAME \
            --scopes $(az acr show --name $ACR_NAME --query id --output tsv) \
             --role acrpull \
            --query "password" --output tsv)
  USER_NAME=$(az ad sp list --display-name $SERVICE_PRINCIPAL_NAME --query "[].appId" --output tsv)
  ```
- Sie können sich bei der Registrierung auch mit Ihrer [individuellen Azure AD-Identität](container-registry-authentication.md?tabs=azure-cli#individual-login-with-azure-ad) anmelden, um Helm-Diagramme zu pushen und zu pullen.
  ```bash
  USER_NAME="00000000-0000-0000-0000-000000000000"
  PASSWORD=$(az acr login --name $ACR_NAME --expose-token --output tsv --query accessToken)
  ```
- Authentifizieren mit einem [Token für den Repositorygültigkeitsbereich](container-registry-repository-scoped-permissions.md) (Vorschau).
  ```bash
  USER_NAME="helm-token"
  PASSWORD=$(az acr token create -n $USER_NAME \
                    -r $ACR_NAME \
                    --scope-map _repositories_admin \
                    --only-show-errors \
                    --query "credentials.passwords[0].value" -o tsv)
  ```
- Stellen Sie die Anmeldeinformationen für `helm registry login` bereit.
  ```bash
  helm registry login $ACR_NAME.azurecr.io \
    --username $USER_NAME \
    --password $PASSWORD
  ```

## <a name="push-chart-to-registry-as-oci-artifact"></a>Pushen eines Charts als OCI-Artefakt in die Registrierung

Führen Sie den Befehl `helm push` in der Helm 3-Befehlszeilenschnittstelle aus, um das Chartarchiv in das vollqualifizierte Zielrepository zu pushen. Im folgenden Beispiel lautet der Ziel-Repositorynamespace `helm/hello-world`, und das Chart ist mit `0.1.0` gekennzeichnet:

```console
helm push hello-world-0.1.0.tgz oci://$ACR_NAME.azurecr.io/helm
```

Nach einem erfolgreichen Push ähnelt die Ausgabe der folgenden:

```output
Pushed: <registry>.azurecr.io/helm/hello-world:0.1.0
digest: sha256:5899db028dcf96aeaabdadfa5899db02589b2899b025899b059db02
```

## <a name="list-charts-in-the-repository"></a>Auflisten von Diagrammen im Repository

Wie bei in Azure Container Registry gespeicherten Images können Sie mithilfe von [az acr repository][az-acr-repository]-Befehlen die Depots anzeigen, in denen Ihre Charts gehostet werden, sowie die Charttags und -manifeste. 

Führen Sie z. B. [az acr repository show][az-acr-repository-show] aus, um die Eigenschaften des im vorherigen Schritt erstellten Repositorys anzuzeigen:

```azurecli
az acr repository show \
  --name $ACR_NAME \
  --repository helm/hello-world
```

Die Ausgabe Die Ausgabe lautet in etwa wie folgt:

```output
{
  "changeableAttributes": {
    "deleteEnabled": true,
    "listEnabled": true,
    "readEnabled": true,
    "writeEnabled": true
  },
  "createdTime": "2021-10-05T12:11:37.6701689Z",
  "imageName": "helm/hello-world",
  "lastUpdateTime": "2021-10-05T12:11:37.7637082Z",
  "manifestCount": 1,
  "registry": "mycontainerregistry.azurecr.io",
  "tagCount": 1
}
```

Führen Sie den Befehl [az acr repository show-manifests][az-acr-repository-show-manifests] aus, um die Details des im Repository gespeicherten Charts anzuzeigen. Beispiel:

```azurecli
az acr repository show-manifests \
  --name $ACR_NAME \
  --repository helm/hello-world --detail
```

Die Ausgabe (in diesem Beispiel abgekürzt) zeigt `application/vnd.cncf.helm.config.v1+json` als `configMediaType` an:

```output
[
  {
    [...]
    "configMediaType": "application/vnd.cncf.helm.config.v1+json",
    "createdTime": "2021-10-05T12:11:37.7167893Z",
    "digest": "sha256:0c03b71c225c3ddff53660258ea16ca7412b53b1f6811bf769d8c85a1f0663ee",
    "imageSize": 3301,
    "lastUpdateTime": "2021-10-05T12:11:37.7167893Z",
    "mediaType": "application/vnd.oci.image.manifest.v1+json",
    "tags": [
      "0.1.0"
    ]
```

## <a name="install-helm-chart"></a>Installieren von Helm-Charts

Führen Sie `helm install` aus, um das Helm-Chart zu installieren, das Sie in die Registrierung gepusht haben. Das Charttag wird mithilfe des Parameters `--version` übergeben. Geben Sie einen Releasenamen (beispielsweise *myhelmtest*) an, oder übergeben Sie den Parameter `--generate-name`. Beispiel:

```console
helm install myhelmtest oci://$ACR_NAME.azurecr.io/helm/hello-world --version 0.1.0
```

Die Ausgabe nach erfolgreicher Chartinstallation sieht in etwa wie folgt aus:

```console
NAME: myhelmtest
LAST DEPLOYED: Tue Oct  4 16:59:51 2021
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
```

Führen Sie den Befehl `helm get manifest` aus, um die Installation zu überprüfen. 

```console
helm get manifest myhelmtest
```

Der Befehl gibt die YAML-Daten in der Vorlagendatei `configmap.yaml` zurück.

Führen Sie `helm uninstall` aus, um das Chart-Release in Ihrem Cluster zu deinstallieren:

```console
helm uninstall myhelmtest
```

## <a name="pull-chart-to-local-archive"></a>Pullen eines Charts in das lokale Archiv

Optional können Sie ein Chart mithilfe von `helm pull` aus der Containerregistrierung in ein lokales Archiv pullen. Das Charttag wird mithilfe des Parameters `--version` übergeben. Wenn ein lokales Archiv im aktuellen Pfad vorhanden ist, wird es durch diesen Befehl überschrieben.

```console
helm pull oci://$ACR_NAME.azurecr.io/helm/hello-world --version 0.1.0
```

## <a name="delete-chart-from-the-registry"></a>Löschen eines Charts aus der Registrierung

Wenn Sie ein Chart aus der Containerregistrierung löschen möchten, verwenden Sie den Befehl [az acr repository delete][az-acr-repository-delete]. Führen Sie den folgenden Befehl aus, und bestätigen Sie den Vorgang, wenn Sie dazu aufgefordert werden:

```azurecli
az acr repository delete --name $ACR_NAME --image helm/hello-world:0.1.0
```

## <a name="migrate-your-registry-to-store-helm-oci-artifacts"></a>Migrieren Ihrer Registrierung zum Speichern von Helm-OCI-Artefakten

Wenn Sie Ihre Azure-Containerregistrierung zuvor mit Helm 2 und den `az acr helm`-Befehlen als Chartrepository eingerichtet haben, wird empfohlen, ein [Upgrade][helm-install] auf den Helm 3-Client auszuführen. Führen Sie dann die folgenden Schritte aus, um die Charts als OCI-Artefakte in Ihrer Registrierung zu speichern. 

> [!IMPORTANT]
> * Nachdem Sie die Migration von einem Helm 2-Chartrepository (index.yaml-basiert) zu OCI-Artefaktrepositorys abgeschlossen haben, verwenden Sie die Helm-Befehlszeilenschnittstelle und die `az acr repository`-Befehle, um die Charts zu verwalten. Informationen hierzu finden Sie in den früheren Abschnitten dieses Artikels. 
> * Die Helm-OCI-Artefaktrepositorys können nicht mit Helm-Befehlen wie `helm search` und `helm repo list` gefunden werden. Weitere Informationen zu Helm-Befehlen zum Speichern von Charts als OCI-Artefakte finden Sie in der [Helm-Dokumentation](https://helm.sh/docs/topics/registries/).

### <a name="enable-oci-support"></a>Aktivieren der OCI-Unterstützung

Stellen Sie sicher, dass Sie den Helm 3-Client verwenden:

```console
helm version
```

Aktivieren Sie die OCI-Unterstützung im Helm 3-Client. Diese Unterstützung ist derzeit experimentell und kann noch geändert werden.

```console
export HELM_EXPERIMENTAL_OCI=1
```

### <a name="list-current-charts"></a>Auflisten aktueller Charts

Listen Sie die Charts auf, die derzeit in der Registrierung gespeichert sind, hier als *myregistry* benannt:

```console
helm search repo myregistry
```

Die Ausgabe zeigt die Charts und Chartversionen:

```
NAME                            CHART VERSION   APP VERSION     DESCRIPTION                                       
myregistry/ingress-nginx        3.20.1          0.43.0          Ingress controller for Kubernetes...
myregistry/wordpress            9.0.3           5.3.2           Web publishing platform for building...
[...]
```

### <a name="pull-chart-archives-locally"></a>Lokales Pullen von Chartarchiven

Pullen Sie für jedes Chart im Repository das Chartarchiv lokal, und notieren Sie sich den Dateinamen:

```console 
helm pull myregisry/ingress-nginx
ls *.tgz
```

Ein lokales Chartarchiv wie `ingress-nginx-3.20.1.tgz` wird erstellt.

### <a name="push-charts-as-oci-artifacts-to-registry"></a>Pushen von Charts als OCI-Artefakte in eine Registrierung

Melden Sie sich bei der Registrierung an:

```azurecli
az acr login --name $ACR_NAME
```

Pushen Sie jedes Chartarchiv in die Registrierung. Beispiel:

```console
helm push ingress-nginx-3.20.1.tgz oci://$ACR_NAME.azurecr.io/helm
```

Vergewissern Sie sich nach dem Pushen eines Charts, dass es in der Registrierung gespeichert ist:

```azurecli
az acr repository list --name $ACR_NAME
```

Entfernen Sie nach dem Pushen aller Charts optional das Helm 2-Chartrepository aus der Registrierung. Dadurch wird der beanspruchte Speicher in Ihrer Registrierung reduziert:

```console
helm repo remove $ACR_NAME
```

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zum Erstellen und Bereitstellen von Helm-Charts finden Sie unter [Charts][develop-helm-charts].
* Weitere Informationen zum Installieren von Anwendungen mit Helm in Azure Kubernetes Service (AKS) finden Sie [hier](../aks/kubernetes-helm.md).
* Helm-Diagramme können als Teil des Containererstellungsprozesses verwendet werden. Weitere Informationen finden Sie unter [Verwenden von Azure Container Registry Tasks][acr-tasks].

<!-- LINKS - external -->
[helm]: https://helm.sh/
[helm-install]: https://helm.sh/docs/intro/install/
[develop-helm-charts]: https://helm.sh/docs/chart_template_guide/

<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-quickstart]: ../aks/kubernetes-walkthrough.md
[acr-bestpractices]: container-registry-best-practices.md
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-acr-helm]: /cli/azure/acr/helm
[az-acr-repository]: /cli/azure/acr/repository
[az-acr-repository-show]: /cli/azure/acr/repository#az_acr_repository_show
[az-acr-repository-delete]: /cli/azure/acr/repository#az_acr_repository_delete
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az_acr_repository_show_manifests
[acr-tasks]: container-registry-tasks-overview.md
