---
author: phillipgibson
ms.topic: include
ms.date: 03/15/2021
ms.author: pgibson
ms.openlocfilehash: 10759b4d80b39b1fc9a04d513e75d98b7c691281
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/03/2021
ms.locfileid: "123440700"
---
## <a name="download-and-install-the-open-service-mesh-osm-client-binary"></a>Herunterladen und Installieren der Open Service Mesh (OSM)-Clientbinärdatei

Verwenden Sie in einer PowerShell-basierten Shell unter Windows `Invoke-WebRequest`, um das OSM-Release herunterzuladen, und führen Sie das Extrahieren dann wie folgt mit `Expand-Archive` durch:

```powershell
# Specify the OSM version that will be leveraged throughout these instructions
$OSM_VERSION="v0.9.1"

[Net.ServicePointManager]::SecurityProtocol = "tls12"
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/openservicemesh/osm/releases/download/$OSM_VERSION/osm-$OSM_VERSION-windows-amd64.zip&quot; -OutFile &quot;osm-$OSM_VERSION.zip"
Expand-Archive -Path "osm-$OSM_VERSION.zip" -DestinationPath .
```

Das `osm`Clientbinär läuft auf Ihrem Client-Rechner und ermöglicht Ihnen die Verwaltung des OSM-Controllers in Ihrem AKS-Cluster. Verwenden Sie die folgenden Befehle, um die OSM-Clientbinärdatei `osm` in einer PowerShell-basierten Shell unter Windows zu installieren. Mit diesen Befehlen wird die `osm`Clientbinärdatei in einen Ordner „OSM“ kopiert und dann sowohl sofort (in der aktuellen Shell) als auch dauerhaft (über Neustarts der Shell) über Ihren `PATH` verfügbar gemacht. Sie benötigen keine erhöhten Rechte (Administrator), um diese Befehle auszuführen, und Sie müssen Ihre Shell nicht neu starten.

```powershell
# Copy osm.exe to C:\OSM
New-Item -ItemType Directory -Force -Path "C:\OSM"
Move-Item -Path .\windows-amd64\osm.exe -Destination "C:\OSM\"

# Add C:\OSM to PATH.
# Make the new PATH permanently available for the current User
$USER_PATH = [environment]::GetEnvironmentVariable("PATH&quot;, &quot;User&quot;) + &quot;;C:\OSM\"
[environment]::SetEnvironmentVariable("PATH", $USER_PATH, "User")
# Make the new PATH immediately available in the current shell
$env:PATH += ";C:\OSM\"
```
