---
title: Herunterladen der OSM-Clientbibliothek
description: Herunterladen der OSM-Clientbibliothek (Open Service Mesh, OSM)
services: container-service
ms.topic: article
ms.date: 8/26/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 128d0371e65d06e413f503094b56522c67ea83c0
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2021
ms.locfileid: "123440699"
---
# <a name="download-the-open-service-mesh-osm-client-library"></a>Herunterladen der OSM-Clientbibliothek (Open Service Mesh, OSM)
In diesem Artikel wird erläutert, wie Sie die OSM-Clientbibliothek herunterladen, die zum Betreiben und Konfigurieren des OSM-Add-Ons für AKS verwendet werden soll.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

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