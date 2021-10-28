---
title: Herunterladen der OSM-Clientbibliothek
description: Herunterladen der OSM-Clientbibliothek (Open Service Mesh, OSM)
services: container-service
ms.topic: article
ms.date: 8/26/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
zone_pivot_groups: client-operating-system
ms.openlocfilehash: fb3619ed95f0636ae83829e1ee1c818545dc86c9
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130227338"
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

> [!NOTE]
> Es wird empfohlen, die OSM-CLI so zu konfigurieren, dass die [Benutzeroberfläche des AKS-Add-Ons für OSM](./open-service-mesh-customize-add-on-experience.md) nach der Installation der Binärdatei angepasst wird, bevor Sie die OSM-CLI verwenden.