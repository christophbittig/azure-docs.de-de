---
author: phillipgibson
ms.topic: include
ms.date: 03/15/2021
ms.author: phillipgibson
ms.openlocfilehash: ea974adf5e3651074f8e7e477837c75bd9258be2
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2021
ms.locfileid: "123440704"
---
## <a name="download-and-install-the-open-service-mesh-osm-client-binary"></a>Herunterladen und Installieren der Open Service Mesh (OSM)-Clientbinärdatei

Verwenden Sie in einer Bash-basierten Shell unter Linux oder dem [Windows-Subsystem für Linux][install-wsl]`curl`, um das OSM-Release herunterzuladen, und extrahieren Sie dieses dann wie folgt mit `tar`:

```bash
# Specify the OSM version that will be leveraged throughout these instructions
OSM_VERSION=v0.9.1

curl -sL "https://github.com/openservicemesh/osm/releases/download/$OSM_VERSION/osm-$OSM_VERSION-linux-amd64.tar.gz" | tar -vxzf -
```

Die `osm` Clientbinärdatei läuft auf Ihrem Client-Rechner und ermöglicht Ihnen die Verwaltung von OSM in Ihrem AKS-Cluster. Verwenden Sie die folgenden Befehle, um die OSM-Clientbinärdatei `osm` in einer Bash-basierten Shell unter Linux oder [Windows-Subsystem für Linux][install-wsl] zu installieren. Diese Befehle kopieren die `osm`-Clientbinärdatei an den Speicherort des Standardbenutzerprogramms in Ihrem `PATH`.

```bash
sudo mv ./linux-amd64/osm /usr/local/bin/osm
sudo chmod +x /usr/local/bin/osm
```

Mithilfe des folgenden Befehls können Sie überprüfen, ob die `osm` Clientbibliothek ordnungsgemäß zu Ihrem Pfad und ihrer Versionsnummer hinzugefügt wurde.

```
osm version
```

<!-- LINKS - external -->

[install-wsl]: /windows/wsl/install-win10
