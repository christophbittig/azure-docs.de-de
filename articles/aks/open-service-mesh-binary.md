---
title: Herunterladen der OSM-Clientbibliothek
description: Herunterladen und Konfigurieren der OSM-Clientbibliothek (Open Service Mesh)
services: container-service
ms.topic: article
ms.date: 8/26/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 2524dca92126ac0fdd6c0ace502cc16d78b6d480
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131066841"
---
# <a name="download-and-configure-the-open-service-mesh-osm-client-library"></a>Herunterladen und Konfigurieren der OSM-Clientbibliothek (Open Service Mesh)

In diesem Artikel wird erläutert, wie Sie die OSM-Clientbibliothek herunterladen, die zum Betreiben und Konfigurieren des OSM-Add-Ons für AKS verwendet werden soll, und wie Sie die Binärdatei für Ihre Umgebung konfigurieren.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download and install client binary](includes/servicemesh/osm/open-service-mesh-binary-install-linux.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [macOS - download and install client binary](includes/servicemesh/osm/open-service-mesh-binary-install-macos.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download and install client binary](includes/servicemesh/osm/open-service-mesh-binary-install-windows.md)]

::: zone-end

> [!WARNING]
> Versuchen Sie nicht, OSM mithilfe der Binärdatei zu installieren `osm install` . Dies führt zu einer Installation von OSM, die nicht als Add-on für AKS integriert ist.

## <a name="configure-osm-cli-variables-with-an-osm_config-file"></a>Konfigurieren von OSM-CLI-Variablen mit einer OSM_CONFIG-Datei

Benutzer können die Standard-OSM-CLI-Konfiguration überschreiben, um das Add-On zu verbessern. Dies kann durch Erstellen einer Konfigurationsdatei, ähnlich wie `kubeconfig`, erfolgen. Die Konfigurationsdatei kann entweder unter `$HOME/.osm/config.yaml` oder unter einem anderen Pfad erstellt werden, der mit der Umgebungsvariablen `OSM_CONFIG` exportiert wird.

Die Datei muss den folgenden Inhalt im YAML-Format enthalten:

```yaml
install:
  kind: managed
  distribution: AKS
  namespace: kube-system
```

Wenn die Datei nicht unter `$HOME/.osm/config.yaml` erstellt wird, denken Sie daran, die Umgebungsvariable `OSM_CONFIG` so festzulegen, dass sie auf den Pfad weist, unter dem die Konfigurationsdatei erstellt wird.

Nachdem Sie OSM_CONFIG festgelegt haben, sollte die Ausgabe des Befehls `osm env` wie folgt lauten:

```console
$ osm env
---
install:
  kind: managed
  distribution: AKS
  namespace: kube-system
```
