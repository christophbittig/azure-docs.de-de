---
title: Informationen zu Registrierungen, Repositorys, Images und Artefakten
description: Einführung in die grundlegenden Konzepte von Azure-Containerregistrierungen, Repositorys, Containerimages und anderen Artefakten
ms.topic: article
ms.date: 01/29/2021
ms.openlocfilehash: add8c20de07a2d520095f257dac0356d1c21af57
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128596142"
---
# <a name="about-registries-repositories-and-artifacts"></a>Informationen zu Registrierungen, Repositorys und Artefakten

Dieser Artikel stellt die grundlegenden Konzepte von Containerregistern, Repositorys, Containerimages und verwandten Artefakten vor. 

:::image type="content" source="media/container-registry-concepts/registry-elements.png" alt-text="Registrierungen, Repositorys und Artefakte":::

## <a name="registry"></a>Registrierung

Eine *Containerregistrierung* ist ein Dienst, der Containerimages und zugehörige Artefakte verteilt und speichert. Docker Hub ist ein Beispiel für eine öffentliche Containerregistrierung, die als allgemeiner Katalog von Docker-Containerimages fungiert. Azure Container Registry bietet Benutzern die direkte Steuerung ihrer Containerinhalte mit integrierter Authentifizierung, [Georeplikation](container-registry-geo-replication.md), die die globale Verteilung und Zuverlässigkeit netzwerknaher Bereitstellungen unterstützt, [Konfiguration von virtuellen Netzwerken mit Private Link](container-registry-private-link.md), [Tagsperren](container-registry-image-lock.md) und vielen anderen erweiterten Funktionen. 

Zusätzlich zu den mit Docker kompatiblen Containerimages unterstützt Azure Container Registry eine Reihe von [Inhaltsartefakten](container-registry-image-formats.md), einschließlich Helm-Diagrammen und Open Container Initiative-Imageformaten (OCI).

## <a name="repository"></a>Repository

Ein *Repository* ist eine Sammlung von Containerimages oder anderen Artefakten in einer Registrierung, die den gleichen Namen, aber unterschiedliche Tags aufweisen. Die folgenden drei Images befinden sich beispielsweise im Repository `acr-helloworld`:

- *acr-helloworld:latest*
- *acr-helloworld:v1*
- *acr-helloworld:v2*

Repository-Namen können auch [Namespaces](container-registry-best-practices.md#repository-namespaces) enthalten. Mit Namespaces können Sie verwandte Repositorys und den Artefaktbesitz in Ihrer Organisation identifizieren, indem Sie durch Schrägstriche getrennte Namen verwenden. Allerdings verwaltet die Registrierung alle Repositorys unabhängig voneinander und nicht als Hierarchie. Beispiel:

- *marketing/campaign10-18/web:v2*
- *marketing/campaign10-18/api:v3*
- *marketing/campaign10-18/email-sender:v2*
- *product-returns/web-submission:20180604*
- *product-returns/legacy-integrator:20180715*

Repositorynamen dürfen nur alphanumerische Kleinbuchstaben, Punkte, Bindestriche, Unterstriche und Schrägstriche enthalten. 

Vollständige Repositorybenennungsregeln finden Sie in der Spezifikation der [Open Container Initiative-Distribution](https://github.com/docker/distribution/blob/master/docs/spec/api.md#overview).

## <a name="artifact"></a>Artefakt

Ein Containerimage oder sonstiges Artefakt in einer Registrierung wird einem oder mehreren Tags zugeordnet, verfügt über eine oder mehrere Ebenen und wird durch ein Manifest identifiziert. Wenn Sie wissen, wie diese Komponenten miteinander in Beziehung stehen, können Sie Ihre Registrierung effektiv verwalten.

### <a name="tag"></a>Tag

Das *Tag* für ein Image oder anderes Artefakt gibt die Version an. Einem einzelnen Artefakt in einem Repository kann eines oder mehrere Tags zugewiesen werden, und Tags können auch entfernt werden. Sie können also alle Tags aus einem Image löschen, während die Daten des Images (die Ebenen) in der Registrierung verbleiben.

Das Repository (oder Repository und Namespace) plus ein Tag definieren den Namen eines Images. Sie können Push- und Pull-Vorgänge für Images ausführen, indem Sie deren Namen im Push- oder Pull-Vorgang festlegen. Das Tag `latest` wird standardmäßig verwendet, wenn Sie keine Angabe in Ihren Docker-Befehlen bereitstellen.

Wie Sie Containerimages mit Tags versehen, hängt von den Szenarien ihrer Entwicklung oder Bereitstellung ab. So werden beispielsweise dauerhafte Tags für die Pflege Ihrer Basisimages und eindeutige Tags für die Bereitstellung von Images empfohlen. Weitere Informationen finden Sie unter [Empfehlungen für das Taggen und die Versionsverwaltung von Containerimages](container-registry-image-tag-version.md).

Informationen zu Benennungsregeln für Tags finden Sie in der [Docker-Dokumentation](https://docs.docker.com/engine/reference/commandline/tag/).

### <a name="layer"></a>Ebene

Containerimages und Artefakte bestehen aus einer oder mehreren *Ebenen*. Die Ebenen sind je nach Artefakttyp unterschiedlich definiert. Beispielsweise entspricht in einem Docker-Containerimage jede Ebene einer Zeile in der Dockerfile, die das Image definiert:

:::image type="content" source="media/container-registry-concepts/container-image-layers.png" alt-text="Ebenen eines Containerimages":::

Artefakte in einer Registrierung haben gemeinsame Ebenen, sodass sich die Speichereffizienz verbessert. Beispielsweise können mehrere Images in unterschiedlichen Repositorys dieselbe ASP.NET Core-Basisebene enthalten, jedoch wird nur eine Kopie dieser Ebene in der Registrierung gespeichert. Die gemeinsame Nutzung von Ebenen optimiert außerdem die Ebenenverteilung auf Knoten, wobei mehrere Artefakte gemeinsame Ebenen verwenden. Wenn ein Image auf einem Knoten bereits die ASP.NET Core-Ebene als Basis enthält, wird beim nachfolgenden Pullvorgang eines anderen Images, das auf dieselbe Ebene verweist, die Ebene nicht auf den Knoten übertragen. Stattdessen verweist es auf die Ebene, die bereits auf dem Knoten vorhanden ist.

Um sichere Isolation und Schutz vor potenzieller Ebenenmanipulation zu ermöglichen, werden die Ebenen nicht Registrierungen übergreifend freigegeben.

### <a name="manifest"></a>Manifest

Jedes Containerimage oder Artefakt, das per Push an eine Containerregistrierung übertragen wird, ist einem *Manifest* zugeordnet. Das Manifest wird von der Registrierung generiert, wenn der Inhalt per Push übertragen wird. Es identifiziert die Artefakte und gibt die Ebenen an.

Ein einfaches Manifest für ein Linux-`hello-world`-Image sieht in etwa wie folgt aus:

  ```json
  {
    "schemaVersion": 2,
    "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
    "config": {
      "mediaType": "application/vnd.docker.container.image.v1+json",
      "size": 1510,
      "digest": "sha256:fbf289e99eb9bca977dae136fbe2a82b6b7d4c372474c9235adc1741675f587e"
    },
    "layers": [
      {
        "mediaType": "application/vnd.docker.image.rootfs.diff.tar.gzip",
        "size": 977,
        "digest": "sha256:2c930d010525941c1d56ec53b97bd057a67ae1865eebf042686d2a2d18271ced"
      }
    ]
  }
  ```

Sie können die Manifeste für ein Repository mit dem Azure CLI-Befehl [az acr repository show-manifests][az-acr-repository-show-manifests] auflisten:

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName>
```

Listen Sie beispielsweise die Manifeste für das Repository "acr-helloworld" auf:

```azurecli
az acr repository show-manifests --name myregistry --repository acr-helloworld
```

```output
[
  {
    "digest": "sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108",
    "tags": [
      "latest",
      "v3"
    ],
    "timestamp": "2018-07-12T15:52:00.2075864Z"
  },
  {
    "digest": "sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57",
    "tags": [
      "v2"
    ],
    "timestamp": "2018-07-12T15:50:53.5372468Z"
  },
  {
    "digest": "sha256:7ca0e0ae50c95155dbb0e380f37d7471e98d2232ed9e31eece9f9fb9078f2728",
    "tags": [
      "v1"
    ],
    "timestamp": "2018-07-11T21:38:35.9170967Z"
  }
]
```

### <a name="manifest-digest"></a>Manifest-Digest

Manifeste werden durch einen eindeutigen SHA-256-Hash oder *manifest digest* identifiziert. Jedes Image oder Artefakt – ob mit oder ohne Tag  – wird durch seinen Digest identifiziert. Der Digest-Wert ist eindeutig, auch wenn die Ebenendaten des Artefakts mit einem anderen Artefakt übereinstimmen. Dieser Mechanismus ermöglicht das wiederholte Pushen von Images mit identischen Tags an eine Registrierung. Beispielsweise können Sie wiederholt `myimage:latest` in Ihre Registrierung pushen, ohne einen Fehler zu erhalten, da jedes Image durch seinen eindeutigen Digest identifiziert wird.

Sie können ein Artefakt aus einer Registrierung pullen, indem Sie im Pullvorgang den Digest angeben. Einige Systeme können so konfiguriert werden, dass sie Pushvorgänge per Digest ausführen, da so sichergestellt ist, dass die Version des Images per Pull abgerufen wird, selbst wenn ein Image mit identischen Tags anschließend per Push in die Registrierung übertragen wird.

> [!IMPORTANT]
> Wenn Sie wiederholt geänderte Artefakte mit identischen Tags per Push übertragen, kann das zu verwaisten Artefakten führen – Artefakte ohne Tags, die Speicherplatz in der Registrierung belegen. Images ohne Tags werden beim Auflisten oder Anzeigen von Images nach Tag nicht in der Azure-Befehlszeilenschnittstelle oder im Azure-Portal angezeigt. Die Ebenen sind jedoch noch vorhanden und verbrauchen Speicherplatz in der Registrierung. Das Löschen eines nicht markierten Image gibt Speicherplatz in der Registrierung frei, wenn das Manifest das einzige oder letzte ist, das auf eine bestimmte Ebene zeigt. Informationen zum Freigeben von Speicherplatz, der von Images ohne Tags verwendet wird, finden Sie unter [Löschen von Containerimages in Azure Container Registry](container-registry-delete.md).

## <a name="addressing-an-artifact"></a>Adressieren eines Artefakts

Um ein Registrierungsartefakt für Push- und Pullvorgänge mit Docker oder anderen Clienttools zu adressieren, kombinieren Sie den vollqualifizierten Registrierungsnamen, den Repositorynamen (ggf. mit Namespacepfad) und ein Artefakttag oder einen Manifest-Digest. In den vorherigen Abschnitten finden Sie Erläuterungen zu diesen Begriffen.

  **Adressierung nach Tag**: `[loginServerUrl]/[repository][:tag]`
    
  **Adressierung nach Digest**: `[loginServerUrl]/[repository@sha256][:digest]`  

Wenn Sie Docker oder andere Clienttools verwenden, um Artefakte per Pull oder Push in eine Azure-Containerregistrierung zu übertragen, verwenden Sie die vollqualifizierte URL der Registrierung, auch als Name des *Anmeldeservers* bezeichnet. In der Azure-Cloud hat die vollqualifizierte URL einer Azure-Containerregistrierung das Format `myregistry.azurecr.io` (nur Kleinbuchstaben).

> [!NOTE]
> * Sie können in der Anmeldeserverregistrierungs-URL keine Portnummer, z. B `myregistry.azurecr.io:443`, angeben. 
> * Wenn Sie im Befehl kein Tag angeben, wird standardmäßig das Tag `latest` verwendet.  

   
### <a name="push-by-tag"></a>Push nach Tag

Beispiele: 

   `docker push myregistry.azurecr.io/samples/myimage:20210106`

   `docker push myregistry.azurecr.io/marketing/email-sender`

### <a name="pull-by-tag"></a>Pull nach Tag

Beispiel: 

  `docker pull myregistry.azurecr.io/marketing/campaign10-18/email-sender:v2`

### <a name="pull-by-manifest-digest"></a>Pull nach Manifest-Digest


Beispiel:

  `docker pull myregistry.azurecr.io/acr-helloworld@sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108`



## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über [Registrierungsspeicher](container-registry-storage.md) und die [unterstützten Inhaltsformate](container-registry-image-formats.md) in Azure Container Registry.

Erfahren Sie, wie [Images per Push und Pull aus Azure Container Registry übertragen](container-registry-get-started-docker-cli.md) werden.

<!-- LINKS - Internal -->
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az_acr_repository_show_manifests
