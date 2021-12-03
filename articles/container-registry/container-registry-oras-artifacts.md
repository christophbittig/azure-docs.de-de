---
title: Pushen und Pullen von Lieferkettenartefakten
description: Pushen und Pullen von Lieferkettenartefakten mithilfe einer privaten Containerregistrierung in Azure
author: SteveLasker
manager: gwallace
ms.topic: article
ms.date: 11/11/2021
ms.author: stevelas
ms.custom: references_regions
ms.openlocfilehash: 4a8d3a4b73993a386d2cd49e2daa784a8bb7aced
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132495366"
---
# <a name="push-and-pull-supply-chain-artifacts-using-a-private-container-registry-in-azure-preview"></a>Pushen und Pullen von Lieferkettenartefakten mithilfe einer privaten Containerregistrierung in Azure (Vorschau)

Mithilfe einer Azure-Containerregistrierung können Sie einen Graphen von Artefakten speichern und verwalten, z. B. Signaturen, Softwarestücklisten (Software Bill of Materials, SBoM), Ergebnisse von Sicherheitsüberprüfungen oder andere Typen. 

![Graph von Artefakten, einschließlich Containerimage, Signatur und signierter Softwarestückliste](./media/container-registry-artifacts/oras-artifact-graph.svg)

Zur Veranschaulichung dieser Funktion wird in diesem Artikel gezeigt, wie Sie das Tool [OCI Registry as Storage (ORAS)](https://oras.land) zum Pushen und Pullen eines Graphen von Artefakten in bzw. aus einer Azure-Containerregistrierung verwenden.

Unterstützung von ORAS Artifacts ist eine Previewfunktion, die [Einschränkungen](#preview-limitations) unterliegt. Hierfür ist [Zonenredundanz](zone-redundancy.md) erforderlich, die auf der Dienstebene „Premium“ verfügbar ist. Informationen zu den Tarifen des Registrierungsdiensts und zu den Einschränkungen finden Sie unter [Azure Container Registry-Tarife](container-registry-skus.md).

## <a name="prerequisites"></a>Voraussetzungen

* **ORAS CLI**: Die ORAS-CLI ermöglicht das Pushen, Ermitteln und Pullen von Artefakten in eine ORAS Artifacts-fähige Registrierung.
* **Azure CLI**: Um eine Identität zu erstellen, Repositorys aufzulisten und zu löschen, muss die Azure CLI lokal installiert sein. Empfohlen wird mindestens Version 2.29.1. Führen Sie `az --version ` aus, um die Version zu ermitteln. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sei bei Bedarf unter [Installieren der Azure CLI](/cli/azure/install-azure-cli).
* **Docker (optional)** : Um die exemplarische Vorgehensweise abzuschließen, wird auf ein Containerimage verwiesen. Sie können lokal installiertes Docker verwenden, um ein Containerimage zu erstellen und zu pushen, oder auf ein vorhandenes Containerimage verweisen. Für Docker sind Pakete erhältlich, mit denen Docker auf einem [macOS][docker-mac]-, [Windows][docker-windows]- oder [Linux][docker-linux]-System problemlos konfiguriert werden kann.

## <a name="preview-limitations"></a>Einschränkungen der Vorschau

ORAS Artifacts-Unterstützung ist auf die Region „USA, Süden-Mitte“ (einschließlich Unterstützung von Verfügbarkeitszonen) begrenzt.

* Georeplizierte Registrierungen replizieren keine referenzierten Artefakte in andere Regionen. Sobald zusätzliche Regionen ORAS Artifacts unterstützen, werden die referenzierten Artefakte repliziert.

## <a name="oras-installation"></a>Installation von ORAS

Laden Sie eine ORAS-Vorschauversion für Ihr Betriebssystem herunter, und installieren Sie sie. Eine Anleitung zum Extrahieren und Installieren der Datei für Ihr Betriebssystem finden Sie in den [ORAS-Installationsanweisungen][oras-install-docs], die sich auf einen Alpha.1-Vorschaubuild im [GitHub-Repository zu ORAS][oras-preview-install] beziehen.

## <a name="configure-a-private-registry"></a>Konfigurieren einer privaten Registrierung

Konfigurieren Sie Umgebungsvariablen, um Befehle einfach in Ihre Shell zu kopieren/einzufügen. Die Befehle können in [Azure Cloud Shell](https://shell.azure.com/) ausgeführt werden.

```console
ACR_NAME=myregistry
REGISTRY=$ACR_NAME.azurecr.io
REPO=net-monitor
TAG=v1
IMAGE=$REGISTRY/${REPO}:$TAG
```

### <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Führen Sie bei Bedarf den Befehl [az group create](/cli/azure/group#az_group_create) aus, um eine Ressourcengruppe für die Registrierung zu erstellen.

```azurecli
az group create --name $ACR_NAME --location southcentralus
```
### <a name="create-oras-artifact-enabled-registry"></a>Erstellen einer ORAS Artifacts-fähigen Registrierung

Die Vorschauunterstützung für ORAS Artifacts erfordert Zonenredundanz, wofür die Dienstebene „Premium“ in der Region „USA, Süden-Mitte“ vorausgesetzt wird. Führen Sie den Befehl [az acr create](/cli/azure/acr#az_acr_create) aus, um eine ORAS Artifacts-fähige Registrierung zu erstellen. In der Hilfe zum Befehl `az acr create` finden Sie Informationen zu weiteren Registrierungsoptionen.

```azurecli
az acr create \
  --resource-group $ACR_NAME \
  --name $ACR_NAME \
  --zone-redundancy enabled \
  --sku Premium \
  --output jsonc
```

Beachten Sie in der Befehlsausgabe die Eigenschaft `zoneRedundancy` für die Registrierung. Sobald aktiviert, ist die Registrierung zonenredundant und ORAS Artefacts aktiviert:

```JSON
{
  [...]
  "zoneRedundancy": "Enabled",
}
```

### <a name="sign-in-with-azure-cli"></a>Anmelden mit der Azure CLI

[Melden Sie sich mit Ihrer Identität bei der Azure CLI an](/cli/azure/authenticate-azure-cli), um Artefakte in die Containerregistrierung zu pushen und daraus zu pullen.

Greifen Sie dann mithilfe des Azure CLI-Befehls [az acr login](/cli/azure/acr#az_acr_login) auf die Registrierung zu.

```azurecli
az login
az acr login --name $ACR_NAME
```

> [!NOTE]
> `az acr login` verwendet den Docker-Client, um ein Azure Active Directory-Token in der Datei `docker.config` festzulegen. Der Docker-Client muss installiert sein und ausgeführt werden, um den individuellen Authentifizierungsablauf durchführen zu können.

## <a name="sign-in-with-oras"></a>Anmelden mit ORAS

In diesem Abschnitt werden Optionen zum Anmelden bei der Registrierung gezeigt. Wählen Sie die für Ihre Umgebung geeignete Methode.

Führen Sie `oras login` aus, um sich bei der Registrierung zu authentifizieren. Sie können für Ihr Szenario geeignete [Registrierungsanmeldeinformationen](container-registry-authentication.md) übergeben, z. B. Anmeldeinformationen für den Dienstprinzipal, Benutzeridentität oder ein Token für den Repositorybereich (Vorschau).

- Authentifizieren Sie sich mit Ihrer [individuellen Azure AD-Identität](container-registry-authentication.md?tabs=azure-cli#individual-login-with-azure-ad), um ein AD-Token zu verwenden.

  ```bash
  USER_NAME="00000000-0000-0000-0000-000000000000"
  PASSWORD=$(az acr login --name $ACR_NAME --expose-token --output tsv --query accessToken)
  ```

- Authentifizieren Sie sich mit einem [repositorybezogenen Token](container-registry-repository-scoped-permissions.md) (Vorschau), um nicht auf AD basierende Token zu verwenden.

  ```bash
  USER_NAME="oras-token"
  PASSWORD=$(az acr token create -n $USER_NAME \
                    -r $ACR_NAME \
                    --repository $REPO content/write \
                    --only-show-errors \
                    --query "credentials.passwords[0].value" -o tsv)
  ```

- Authentifizieren Sie sich mit einem [Azure Active Directory-Dienstprinzipal mit Pull- und Pushberechtigung](container-registry-auth-service-principal.md#create-a-service-principal) (Rolle AcrPush) für die Registrierung.

  ```bash
  SERVICE_PRINCIPAL_NAME="oras-sp"
  ACR_REGISTRY_ID=$(az acr show --name $ACR_NAME --query id --output tsv)
  PASSWORD=$(az ad sp create-for-rbac --name $SERVICE_PRINCIPAL_NAME \
            --scopes $(az acr show --name $ACR_NAME --query id --output tsv) \
             --role acrpush \
            --query "password" --output tsv)
  USER_NAME=$(az ad sp list --display-name $SERVICE_PRINCIPAL_NAME --query "[].appId" --output tsv)
  ```

### <a name="sign-in-with-oras"></a>Anmelden mit ORAS

Stellen Sie die Anmeldeinformationen für `oras login` bereit.

  ```bash
  oras login $REGISTRY \
    --username $USER_NAME \
    --password $PASSWORD
  ```

Verwenden Sie `--password-stdin`, um das Kennwort aus STDIN zu lesen.

## <a name="push-a-container-image"></a>Pushen eines Containerimages

In diesem Beispiel wird einem Containerimage ein Graph von Artefakten zugeordnet. Erstellen und pushen Sie ein Containerimage, oder verweisen Sie auf ein vorhandenes Image in der privaten Registrierung.

```bash
docker build -t $IMAGE https://github.com/wabbit-networks/net-monitor.git#main
docker push $IMAGE
```

## <a name="create-a-sample-signature-to-the-container-image"></a>Erstellen einer Beispielsignatur für das Containerimage

```bash
echo '{"artifact": "'${IMAGE}'", "signature": "pat hancock"}' > signature.json
```

### <a name="push-a-signature-to-the-registry-as-a-reference-to-the-container-image"></a>Pushen einer Signatur in die Registrierung (als Verweis auf das Containerimage)

Der ORAS-Befehl pusht die Signatur in ein Repository und verweist über den Parameter `subject` auf ein anderes Artefakt. `--artifact-type` dient der Unterscheidung von Artefakten, ähnlich wie Dateierweiterungen, die unterschiedliche Dateitypen ermöglichen. Eine oder mehrere Dateien können durch Angabe von `file:mediaType` gepusht werden.

```bash
oras push $REGISTRY/$REPO \
    --artifact-type 'signature/example' \
    --subject $IMAGE \
    ./signature.json:application/json
```

Weitere Informationen zu ORAS Push finden Sie in der [ORAS-Dokumentation][oras-push-docs].

## <a name="push-a-multi-file-artifact-as-a-reference"></a>Pushen eines Artefakts mit mehreren Dateien als Referenz

Erstellen von Dokumentation zu einem Artefakt

```bash
echo 'Readme Content' > readme.md
echo 'Detailed Content' > readme-details.md
```

Pushen eines Artefakts mit mehreren Dateien als Verweis

```bash
oras push $REGISTRY/$REPO \
    --artifact-type 'readme/example' \
    --subject $IMAGE \
    ./readme.md:application/markdown \
    ./readme-details.md:application/markdown
```

## <a name="discovering-artifact-references"></a>Ermitteln von Artefaktverweisen

Die ORAS Artifacts-Spezifikation definiert eine [Referrer-API][oras-artifacts-referrers] zum Ermitteln von Verweisen auf ein `subject`-Artefakt. Der Befehl `oras discover` kann die Liste der Verweise auf das Containerimage zeigen.

Zeigen Sie mit `oras discover` den Graphen der jetzt in der Registrierung gespeicherten Artefakte an.

```bash
oras discover -o tree $IMAGE
```

Die Ausgabe zeigt den Beginn eines Graphen von Artefakten, in dem Signatur und Dokumente als untergeordnete Elemente des Containerimages angezeigt werden.

```output
myregistry.azurecr.io/net-monitor:v1
├── signature/example
│   └── sha256:555ea91f39e7fb30c06f3b7aa483663f067f2950dcb...
└── readme/example
    └── sha256:1a118663d1085e229ff1b2d4d89b5f6d67911f22e55...
```

## <a name="creating-a-deep-graphs-of-artifacts"></a>Erstellen tiefer Graphen von Artefakten

Die ORAS Artifacts-Spezifikation ermöglicht tiefe Graphen, sodass signierte Softwarestücklisten (Software Bill of Materials, SBoM) und andere Artefakttypen aktiviert werden.

### <a name="create-a-sample-sbom"></a>Erstellen einer Beispiel-SBoM

```bash
echo '{"version": "0.0.0.0", "artifact": "'${IMAGE}'", "contents": "good"}' > sbom.json
```

### <a name="push-a-sample-sbom-to-the-registry"></a>Pushen einer Beispiel-SBoM in die Registrierung

```bash
oras push $REGISTRY/$REPO \
  --artifact-type 'sbom/example' \
  --subject $IMAGE \
  ./sbom.json:application/json
```

### <a name="sign-the-sbom"></a>Signieren der SBoM

Artefakte, die als Verweise gepusht werden, verfügen in der Regel nicht über Tags, da sie als Teil des Antragstellerartefakts betrachtet werden. Um eine Signatur in ein Artefakt zu übertragen, das ein untergeordnetes Element eines anderen Artefakts ist, filtern Sie mit `oras discover` und `--artifact-type`, um den Digest zu finden.

```bash
SBOM_DIGEST=$(oras discover -o json \
                --artifact-type sbom/example \
                $IMAGE | jq -r ".references[0].digest")
```

Erstellen einer Signatur einer SBoM

```bash
echo '{"artifact": "'$REGISTRY/${REPO}@$SBOM_DIGEST'", "signature": "pat hancock"}' > sbom-signature.json
```

### <a name="push-the-sbom-signature"></a>Pushen der SBoM-Signatur

```bash
oras push $REGISTRY/$REPO \
  --artifact-type 'signature/example' \
  --subject $REGISTRY/$REPO@$SBOM_DIGEST \
  ./sbom-signature.json:application/json
```

### <a name="view-the-graph"></a>Anzeigen des Graphen

```bash
oras discover -o tree $IMAGE
```

Generiert folgende Ausgabe:

```output
myregistry.azurecr.io/net-monitor:v1
├── signature/example
│   └── sha256:555ea91f39e7fb30c06f3b7aa483663f067f2950dcb...
├── readme/example
│   └── sha256:1a118663d1085e229ff1b2d4d89b5f6d67911f22e55...
└── sbom/example
    └── sha256:4280eef9adb632b42cf200e7cd5a822a456a558e4f3142da6b...
        └── signature/example
            └── sha256:a31ab875d37eee1cca68dbb14b2009979d05594d44a075bdd7...
```

## <a name="pull-a-referenced-artifact"></a>Pullen eines Artefakts, auf das verwiesen wird

Um einen Typ, auf den verwiesen wird, zu pullen, wird der Digest des Verweises mit dem Befehl `oras discover` ermittelt.

```bash
DOC_DIGEST=$(oras discover -o json \
              --artifact-type 'readme/example' \
              $IMAGE | jq -r ".references[0].digest")
```

### <a name="create-a-clean-directory-for-downloading"></a>Erstellen eines bereinigten Verzeichnisses zum Herunterladen

```bash
mkdir ./download
```

### <a name="pull-the-docs-into-the-download-directory"></a>Pullen der Dokumentation in das Downloadverzeichnis
```bash
oras pull -a -o ./download $REGISTRY/$REPO@$DOC_DIGEST
```
### <a name="view-the-docs"></a>Anzeigen der Dokumentation

```bash
ls ./download
```

## <a name="view-the-repository-and-tag-listing"></a>Anzeigen des Repositorys und der Tagauflistung

ORAS Artifacts ermöglicht das Pushen, Entdecken, Pullen und Kopieren von Artefaktdiagrammen, ohne Tags zuweisen zu müssen. Auf diese Weise kann sich eine Tagauflistung auf die Artefakte konzentrieren, die die Benutzer interessieren, im Gegensatz zu den Signaturen und SBoMs, die Containerimages, Helm-Diagrammen und anderen Artefakten zugeordnet sind.

### <a name="view-a-list-of-tags"></a>Anzeigen einer Liste mit Tags

```azurecli
az acr repository show-tags \
  -n $ACR_NAME \
  --repository $REPO \
  -o jsonc
```

### <a name="view-a-list-of-manifests"></a>Anzeigen einer Liste mit Manifesten

Ein Repository kann eine Liste mit Manifesten enthalten, die mit Tags versehen sind oder nicht.

```azurecli
az acr repository show-manifests \
  -n $ACR_NAME \
  --repository $REPO \
  --detail -o jsonc
```

Beachten Sie, dass die Containerimagemanifeste `"tags":` aufweisen.

```json
{
  "architecture": "amd64",
  "changeableAttributes": {
    "deleteEnabled": true,
    "listEnabled": true,
    "readEnabled": true,
    "writeEnabled": true
  },
  "configMediaType": "application/vnd.docker.container.image.v1+json",
  "createdTime": "2021-11-12T00:18:54.5123449Z",
  "digest": "sha256:a0fc570a245b09ed752c42d600ee3bb5b4f77bbd70d8898780b7ab4...",
  "imageSize": 2814446,
  "lastUpdateTime": "2021-11-12T00:18:54.5123449Z",
  "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
  "os": "linux",
  "tags": [
    "v1"
  ]
}
```

Die Signatur ist nicht mit Tags versehen, wird aber als `oras.artifact.manifest`-Verweis auf das Containerimage nachverfolgt.

```json
{
  "changeableAttributes": {
    "deleteEnabled": true,
    "listEnabled": true,
    "readEnabled": true,
    "writeEnabled": true
  },
  "createdTime": "2021-11-12T00:19:10.987156Z",
  "digest": "sha256:555ea91f39e7fb30c06f3b7aa483663f067f2950dcbcc0b0d...",
  "imageSize": 85,
  "lastUpdateTime": "2021-11-12T00:19:10.987156Z",
  "mediaType": "application/vnd.cncf.oras.artifact.manifest.v1+json"
}
```
## <a name="delete-all-artifacts-in-the-graph"></a>Löschen aller Artefakte im Graphen

Unterstützung der ORAS Artifacts-Spezifikation ermöglicht das Löschen des Graphen der Artefakte, die dem Stammartefakt zugeordnet sind. Mit dem Befehl [az acr repository delete][az-acr-repository-delete] können Sie Signatur, SBoM und SBoM-Signatur löschen.

```bash
az acr repository delete \
  -n $ACR_NAME \
  -t ${REPO}:$TAG -y
```

### <a name="view-the-remaining-manifests"></a>Anzeigen der verbleibenden Manifeste

```azurecli
az acr repository show-manifests \
  -n $ACR_NAME \
  --repository $REPO \
  --detail -o jsonc
```

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie mehr zur [ORAS CLI](https://oras.land).
* Erfahren Sie mehr über [ORAS Artifacts][oras-artifacts] zum Pushen, Ermitteln, Pullen und Kopieren eines Graphen von Lieferkettenartefakten.

<!-- LINKS - external -->
[docker-linux]:         https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]:           https://docs.docker.com/docker-for-mac/
[docker-windows]:       https://docs.docker.com/docker-for-windows/
[oras-install-docs]:    https://oras.land/cli/
[oras-preview-install]: https://github.com/oras-project/oras/releases/tag/v0.2.1-alpha.1
[oras-push-docs]:       https://oras.land/cli/1_pushing/
[oras-artifacts]:       https://github.com/oras-project/artifacts-spec/
<!-- LINKS - internal -->
[az-acr-repository-show]: /cli/azure/acr/repository?#az_acr_repository_show
[az-acr-repository-delete]: /cli/azure/acr/repository#az_acr_repository_delete
