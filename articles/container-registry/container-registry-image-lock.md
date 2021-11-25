---
title: Sperren von Images
description: Festlegen von Attributen für ein Containerimage oder Repository, sodass es in einer Azure-Containerregistrierung nicht gelöscht oder überschrieben werden kann.
ms.topic: article
ms.date: 09/30/2019
ms.openlocfilehash: 0b2cdd770233833e45c84bea1916ddf7f6e1e317
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132494546"
---
# <a name="lock-a-container-image-in-an-azure-container-registry"></a>Sperren von Containerimages in einer Azure-Containerregistrierung

Sie können eine Imageversion oder ein Repository in einer Azure-Containerregistrierung sperren, damit es nicht gelöscht oder aktualisiert werden kann. Aktualisieren Sie die Attribute mithilfe des Azure CLI-Befehls [az acr repository update][az-acr-repository-update], um ein Image oder ein Repository zu sperren. 

Für die Vorgehensweisen in diesem Artikel ist erforderlich, dass Sie die Azure CLI in Azure Cloud Shell oder lokal ausführen (Version 2.0.55 oder höher werden empfohlen). Führen Sie `az --version` aus, um die Version zu ermitteln. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sie bei Bedarf unter [Installieren der Azure CLI][azure-cli].

> [!IMPORTANT]
> Dieser Artikel gilt nicht für das Sperren einer ganzen Registrierung, z. B. durch die Verwendung von **Einstellungen > Sperren** im Azure-Portal oder von `az lock`-Befehlen in der Azure CLI. Das Sperren einer Registrierungsressource hindert Sie nicht am Erstellen, Aktualisieren oder Löschen von Daten in Repositorys. Das Sperren einer Registrierung wirkt sich nur auf Verwaltungsvorgänge wie das Hinzufügen oder Löschen von Replizierungen oder das Löschen der Registrierung selbst aus. Weitere Informationen finden Sie unter [Sperren von Ressourcen, um unerwartete Änderungen zu verhindern](../azure-resource-manager/management/lock-resources.md).

## <a name="scenarios"></a>Szenarien

Markierte Images sind in Azure Container Registry standardmäßig *änderbar*. Mit den entsprechenden Berechtigungen können Sie ein Image also wiederholt mit dem gleichen Tag aktualisieren und an eine Registrierung pushen. Containerimages können bei Bedarf auf [gelöscht](container-registry-delete.md) werden. Dieses Verhalten ist nützlich, wenn Sie Images entwickeln und eine Größe für Ihre Registrierung einhalten müssen.

Wenn Sie jedoch ein Containerimage für die Produktionsumgebung bereitstellen, benötigen Sie möglicherweise ein *unveränderliches* Containerimage. Ein unveränderliches Containerimage kann nicht versehentlich gelöscht oder überschrieben werden.

Informationen zu Strategien zum Taggen und für die Versionsverwaltung von Images in Ihrer Registrierung finden Sie unter [Empfehlungen für das Taggen und die Versionsverwaltung von Containerimages](container-registry-image-tag-version.md).

Verwenden Sie den Befehl [az acr repository update][az-acr-repository-update], um Repositoryattribute für Folgendes festzulegen:

* Sperren einer Imageversion oder eines gesamten Repositorys

* Schützen einer Imageversion oder eines Repositorys mit zugelassenen Updates

* Verhindern von Lesevorgängen (Pull) für eine Imageversion oder ein gesamtes Repository

Beispiele hierzu finden Sie in den nachfolgenden Abschnitten. 

## <a name="lock-an-image-or-repository"></a>Sperren von Images oder Repositorys 

### <a name="show-the-current-repository-attributes"></a>Anzeigen der aktuellen Repository-Attribute
Führen Sie den folgenden Befehl vom Typ [az acr repository show][az-acr-repository-show] aus, um die aktuellen Attribute eines Repositorys anzuzeigen:

```azurecli
az acr repository show \
    --name myregistry --repository myrepo \
    --output jsonc
```

### <a name="show-the-current-image-attributes"></a>Anzeigen der aktuellen Imageattribute
Führen Sie den folgenden Befehl vom Typ [az acr repository show][az-acr-repository-show] aus, um die aktuellen Attribute eines Tags anzuzeigen:

```azurecli
az acr repository show \
    --name myregistry --image myimage:tag \
    --output jsonc
```

### <a name="lock-an-image-by-tag"></a>Sperren von Images mithilfe von Tags

Um das Bild *myimage:tag* in *myregistry* zu sperren, führen Sie den folgenden Befehl [az acr repository update][az-acr-repository-update] aus:

```azurecli
az acr repository update \
    --name myregistry --image myimage:tag \
    --write-enabled false
```

### <a name="lock-an-image-by-manifest-digest"></a>Sperren von Images anhand von Manifest-Digests

Um ein Bild *myimage* zu sperren, das durch den Manifest-Digest (SHA-256-Hash, dargestellt als `sha256:...`) identifiziert wird, führen Sie den folgenden Befehl aus. (Führen Sie den Befehl [az acr repository show-manifests][az-acr-repository-show-manifests] aus, um den Manifest-Digest zu ermitteln, der mindestens einem Imagetag zugeordnet ist.)

```azurecli
az acr repository update \
    --name myregistry --image myimage@sha256:123456abcdefg \
    --write-enabled false
```

### <a name="lock-a-repository"></a>Sperren eines Repositorys

Um das Repository *myrepo* und alle darin enthaltenen Images zu sperren, führen Sie den folgenden Befehl aus:

```azurecli
az acr repository update \
    --name myregistry --repository myrepo \
    --write-enabled false
```

## <a name="protect-an-image-or-repository-from-deletion"></a>Schützen von Images und Repositorys vor dem Löschen

### <a name="protect-an-image-from-deletion"></a>Schützen von Images vor dem Löschen

Um zuzulassen, dass das Bild *myimage:tag* aktualisiert, aber nicht gelöscht wird, führen Sie den folgenden Befehl aus:

```azurecli
az acr repository update \
    --name myregistry --image myimage:tag \
    --delete-enabled false --write-enabled true
```

### <a name="protect-a-repository-from-deletion"></a>Schützen von Repositorys vor dem Löschen

Der folgende Befehl setzt das Repository *myrepo* so, dass es nicht gelöscht werden kann. Einzelne Images können weiterhin aktualisiert oder gelöscht werden.

```azurecli
az acr repository update \
    --name myregistry --repository myrepo \
    --delete-enabled false --write-enabled true
```

## <a name="prevent-read-operations-on-an-image-or-repository"></a>Verhindern von Lesevorgängen für Images oder Repositorys

Um Lese- (Pull-) Operationen auf das Bild *myimage:tag* zu verhindern, führen Sie den folgenden Befehl aus:

```azurecli
az acr repository update \
    --name myregistry --image myimage:tag \
    --read-enabled false
```

Um Lesevorgänge für alle Images im Repository *myrepo* zu verhindern, führen Sie den folgenden Befehl aus:

```azurecli
az acr repository update \
    --name myregistry --repository myrepo \
    --read-enabled false
```

## <a name="unlock-an-image-or-repository"></a>Entsperren von Images oder Repositorys

Um das Standardverhalten des Bildes *myimage:tag* wiederherzustellen, so dass es gelöscht und aktualisiert werden kann, führen Sie den folgenden Befehl aus:

```azurecli
az acr repository update \
    --name myregistry --image myimage:tag \
    --delete-enabled true --write-enabled true
```

Um das Standardverhalten des Repositorys *myrepo* und aller Images wiederherzustellen, so dass sie gelöscht und aktualisiert werden können, führen Sie den folgenden Befehl aus:

```azurecli
az acr repository update \
    --name myregistry --repository myrepo \
    --delete-enabled true --write-enabled true
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie gelernt, wie Sie den Befehl [az acr repository update][az-acr-repository-update] verwenden, um das Löschen und Aktualisieren von Imageversionen in einem Repository zu verhindern. Informationen zum Festlegen zusätzlicher Attribute finden Sie in der Referenz zum Befehl [az acr repository update][az-acr-repository-update].

Mit dem Befehl [az acr repository show][az-acr-repository-show] können Sie die für eine Imageversion oder ein Repository festgelegten Attribute anzeigen.

Ausführliche Informationen zu Löschvorgängen finden Sie unter [Löschen von Containerimages in Azure Container Registry][container-registry-delete].

<!-- LINKS - Internal -->
[az-acr-repository-update]: /cli/azure/acr/repository#az_acr_repository_update
[az-acr-repository-show]: /cli/azure/acr/repository#az_acr_repository_show
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az_acr_repository_show_manifests
[azure-cli]: /cli/azure/install-azure-cli
[container-registry-delete]: container-registry-delete.md
