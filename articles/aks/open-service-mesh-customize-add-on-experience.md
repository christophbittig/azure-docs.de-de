---
title: Anpassen der OSM-CLI-Benutzeroberfläche
description: Passen Sie die OSM-CLI-Benutzeroberfläche mit einer osmconfig-Datei an.
services: container-service
ms.topic: article
ms.date: 9/30/2021
ms.custom: mvc, devx-track-azurecli
ms.author: nshankar
ms.openlocfilehash: 9a18db63f247ed3508f09426841e84ae65c34923
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "129994763"
---
# <a name="configure-osm-cli-variables-with-an-osm_config-file"></a>Konfigurieren von OSM-CLI-Variablen mit einer OSM_CONFIG-Datei

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