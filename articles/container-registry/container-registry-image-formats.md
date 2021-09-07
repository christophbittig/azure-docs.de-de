---
title: Unterstützte Inhaltsformate
description: Erfahren Sie mehr über die von Azure Container Registry unterstützten Inhaltsformate, einschließlich Docker-kompatibler Containerimages, Helm-Diagramme, OCI-Images und OCI-Artefakte.
ms.topic: article
ms.date: 03/02/2021
ms.openlocfilehash: 1203bc8cbebfd59ef28f03d19e939b73d707a3f3
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/22/2021
ms.locfileid: "114438880"
---
# <a name="content-formats-supported-in-azure-container-registry"></a>In Azure Container Registry unterstützte Inhaltsformate

Verwenden Sie ein privates Repository in Azure Container Registry, um eines der folgenden Inhaltsformate zu verwalten. 

## <a name="docker-compatible-container-images"></a>Docker-kompatible Containerimages

Die folgenden Formate für Docker-Containerimages werden unterstützt:

* [Docker Image Manifest V2, Schema 1](https://docs.docker.com/registry/spec/manifest-v2-1/)

* [Docker Image Manifest V2, Schema 2](https://docs.docker.com/registry/spec/manifest-v2-2/): enthält Manifestlisten, die es Registrierungen ermöglichen, [Images mehrerer Architekturen](push-multi-architecture-images.md) unter einem einzigen `image:tag`-Verweis zu speichern

## <a name="oci-images"></a>OCI-Images

Azure Container Registry unterstützt Images, die der [Spezifikation für das Imageformat der Open Container Initiative (OCI)](https://github.com/opencontainers/image-spec/blob/master/spec.md) entsprechen, einschließlich der optionalen Spezifikation für [Indizes von Images](https://github.com/opencontainers/image-spec/blob/master/image-index.md). Zu Packformaten zählt das Format [Singularity Image Format (SIF)](https://github.com/sylabs/sif).

## <a name="oci-artifacts"></a>OCI-Artefakte

Azure Container Registry unterstützt die [OCI Distribution Specification](https://github.com/opencontainers/distribution-spec) (OCI-Verteilungsspezifikation). Hierbei handelt es sich um eine anbieterneutrale, cloudunabhängige Spezifikation zum Speichern, Freigeben, Schützen und Bereitstellen von Containerimages und anderen Inhaltstypen (Artefakten). Die Spezifikation ermöglicht einer Registrierung das Speichern eines breiten Bereichs von Artefakten zusätzlich zu den Containerimages. Sie verwenden Tools, die für das Artefakt geeignet sind, um Push- und Pullvorgänge für Artefakte durchzuführen. Beispiele finden Sie hier:

* [Pushen und Pullen eines OCI-Artefakts unter Verwendung einer Azure-Containerregistrierung](container-registry-oci-artifacts.md)
* [Pushen und Pullen von Helm-Charts in Azure Container Registry](container-registry-helm-repos.md)

Weitere Informationen zu OCI-Artefakten finden Sie im Repository [OCI Registry as Storage (ORAS)](https://github.com/deislabs/oras) (OCI-Registrierung als Speicher (ORAS)) und im Repository [OCI Artifacts](https://github.com/opencontainers/artifacts) (OCI-Artefakte) auf GitHub.

## <a name="helm-charts"></a>Helm-Diagramme

Azure Container Registry kann Repositorys für [Helm-Diagramme](https://helm.sh/) hosten, ein Paketformat, das zur schnellen Verwaltung und Bereitstellung von Anwendungen für Kubernetes verwendet wird. Empfohlen wird Version 3 des [Helm-Clients](https://docs.helm.sh/using_helm/#installing-helm). Informationen finden Sie unter [Pushen und Pullen von Helm-Charts in Azure Container Registry](container-registry-helm-repos.md).

## <a name="next-steps"></a>Nächste Schritte

* Schauen Sie sich an wie Sie Images mit Azure Container Registry [per Push übertragen und abrufen](container-registry-get-started-docker-cli.md).

* Verwenden Sie [ACR-Aufgaben](container-registry-tasks-overview.md), um Containerimages zu erstellen und zu testen. 

* Verwenden Sie das [Moby BuildKit](https://github.com/moby/buildkit) zum Erstellen und Packen von Containern im OCI-Format.

* Richten Sie ein [Helm-Repository](container-registry-helm-repos.md) ein, dass in Azure Container Registry gehostet wird. 


