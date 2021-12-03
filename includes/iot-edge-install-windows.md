---
ms.topic: include
ms.date: 10/29/2021
author: kgremban
ms.author: kgremban
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 94b115654d50fcc7924d86afcc9b78ddab05688d
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131845085"
---
## <a name="install-iot-edge"></a>Installieren von IoT Edge

In diesem Abschnitt bereiten Sie Ihre Windows-VM oder Ihr physisches Gerät für IoT Edge vor. Anschließend installieren Sie IoT Edge.

Azure IoT Edge basiert auf einer OCI-kompatiblen Containerruntime. [Moby](https://github.com/moby/moby), eine Moby-basierte Engine, ist im Installationsskript enthalten. Dies bedeutet, dass keine zusätzlichen Schritte zum Installieren der Engine erforderlich sind.

So installieren Sie die IoT Edge-Runtime

1. Führen Sie PowerShell als Administrator aus.

   Verwenden Sie eine AMD64-Sitzung von PowerShell, nicht PowerShell(x86). Wenn Sie nicht sicher sind, welchen Sitzungstyp Sie verwenden, führen Sie den folgenden Befehl aus:

   ```powershell
   (Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   ```

2. Führen Sie den Befehl [Deploy-IoTEdge](../articles/iot-edge/reference-windows-scripts.md#deploy-iotedge) aus, der die folgenden Aufgaben ausführt:

   * Überprüft, ob Ihr Windows-Computer mit einer unterstützten Version läuft.
   * Aktiviert das Containerfeature.
   * Lädt die Moby-Engine und die IoT Edge-Runtime herunter.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

3. Starten Sie das Gerät neu, wenn Sie dazu aufgefordert werden.

Wenn Sie IoT Edge auf einem Gerät installieren, können Sie zusätzliche Parameter verwenden, um den Prozess folgendermaßen anzupassen:

* Sie können direkten Datenverkehr über einen Proxyserver leiten.
* Legen Sie im Installationsprogramm ein lokales Verzeichnis für die Offlineinstallation fest.

Weitere Informationen zu diesen zusätzlichen Parametern finden Sie unter [PowerShell-Skripts für IoT Edge mit Windows-Containern](../articles/iot-edge/reference-windows-scripts.md).
