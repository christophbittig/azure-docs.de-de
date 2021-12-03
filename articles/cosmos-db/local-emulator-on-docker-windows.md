---
title: Ausführen des Emulators in Docker für Windows
itleSuffix: Running the Azure Cosmos DB emulator on Docker for Windows
description: Hier erfahren Sie, wie Sie den Azure Cosmos DB-Emulator in Docker für Windows ausführen und verwenden. Mit dem Emulator können Sie Ihre Anwendung kostenlos lokal entwickeln und testen, ohne ein Azure-Abonnement erstellen zu müssen.
ms.service: cosmos-db
ms.topic: how-to
author: StefArroyo
ms.author: esarroyo
ms.date: 04/20/2021
ms.openlocfilehash: a083efb897c0ce3c0628134da0ea3d29720ac0e7
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132054883"
---
# <a name="use-the-emulator-on-docker-for-windows"></a><a id="run-on-windows-docker"></a>Verwenden des Emulators in Docker für Windows
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Sie können den Azure Cosmos DB-Emulator in einem Windows-Docker-Container ausführen. [ und weitere Informationen finden Sie auf ](https://github.com/Azure/azure-cosmos-db-emulator-docker)GitHub`Dockerfile`. Derzeit funktioniert der Emulator nicht in Docker für Oracle Linux. Verwenden Sie die folgenden Anweisungen, um den Emulator in Docker für Windows auszuführen:

1. Nachdem [Docker für Windows](https://www.docker.com/docker-windows) installiert wurde, wechseln Sie zu Windows-Containern, indem Sie mit der rechten Maustaste auf der Symbolleiste auf das Docker-Symbol klicken und **Switch to Windows containers** (Zu Windows-Containern wechseln) auswählen.

1. Rufen Sie als Nächstes das Emulator-Image per Pullvorgang aus dem Docker-Hub ab, indem Sie in Ihrer bevorzugten Shell den folgenden Befehl ausführen.

   ```bash
   docker pull mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator
   ```

1. Um das Image zu starten, führen Sie die folgenden Befehle abhängig von der Befehlszeile oder PowerShell-Umgebung aus:

   # <a name="command-line"></a>[Befehlszeile](#tab/cli)

   ```bash

   md %LOCALAPPDATA%\CosmosDBEmulator\bind-mount

   docker run --name azure-cosmosdb-emulator --memory 2GB --mount "type=bind,source=%LOCALAPPDATA%\CosmosDBEmulator\bind-mount,destination=C:\CosmosDB.Emulator\bind-mount" --interactive --tty -p 8081:8081 -p 8900:8900 -p 8901:8901 -p 8902:8902 -p 10250:10250 -p 10251:10251 -p 10252:10252 -p 10253:10253 -p 10254:10254 -p 10255:10255 -p 10256:10256 -p 10350:10350 mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator
   ```
   Windows-basierte Docker-Images sind möglicherweise nicht grundsätzlich mit allen Windows-Hostbetriebssystemen kompatibel. Beispielsweise ist das Standardimage für den Azure Cosmos DB-Emulator nur mit Windows 10 und Windows Server 2016 kompatibel. Wenn Sie ein Image benötigen, das mit Windows Server 2019 kompatibel ist, führen Sie stattdessen den folgenden Befehl aus:

   ```bash
   docker run --name azure-cosmosdb-emulator --memory 2GB --mount "type=bind,source=%hostDirectory%,destination=C:\CosmosDB.Emulator\bind-mount" --interactive --tty -p 8081:8081 -p 8900:8900 -p 8901:8901 -p 8902:8902 -p 10250:10250 -p 10251:10251 -p 10252:10252 -p 10253:10253 -p 10254:10254 -p 10255:10255 -p 10256:10256 -p 10350:10350 mcr.microsoft.com/cosmosdb/winsrv2019/azure-cosmos-emulator:latest
   ```

   # <a name="powershell"></a>[PowerShell](#tab/powershell)

   ```powershell

   md $env:LOCALAPPDATA\CosmosDBEmulator\bind-mount 2>null

   docker run --name azure-cosmosdb-emulator --memory 2GB --mount "type=bind,source=$env:LOCALAPPDATA\CosmosDBEmulator\bind-mount,destination=C:\CosmosDB.Emulator\bind-mount" --interactive --tty -p 8081:8081 -p 8900:8900 -p 8901:8901 -p 8902:8902 -p 10250:10250 -p 10251:10251 -p 10252:10252 -p 10253:10253 -p 10254:10254 -p 10255:10255 -p 10256:10256 -p 10350:10350 mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator

   ```

   Die Antwort kann wie folgt aussehen:

   ```bash
   Starting emulator
   Emulator Endpoint: https://172.20.229.193:8081/
   Primary Key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
   Exporting SSL Certificate
   You can import the SSL certificate from an administrator command prompt on the host by running:
   cd /d %LOCALAPPDATA%\CosmosDBEmulatorCert
   powershell .\importcert.ps1
   --------------------------------------------------------------------------------------------------
   Starting interactive shell
   ```
   ---

   > [!NOTE]
   > Wenn bei der Ausführung des Befehls `docker run` ein Portkonfliktfehler auftritt (d. h. der angegebene Port bereits verwendet wird), übergeben Sie einen benutzerdefinierten Port, indem Sie die Portnummern ändern. Beispielsweise können Sie den Parameter „-p 8081:8081“ in „-p 443:8081“ ändern.

1. Verwenden Sie nun den Emulatorendpunkt und Primärschlüssel aus der Antwort, und importieren Sie das TLS-/SSL-Zertifikat in Ihren Host. Führen Sie an einer Eingabeaufforderung mit Administratorberechtigungen folgende Schritte aus, um das TLS-/SSL-Zertifikat zu importieren:

   # <a name="command-line"></a>[Befehlszeile](#tab/cli)

   ```bash
   cd  %LOCALAPPDATA%\CosmosDBEmulator\bind-mount
   powershell .\importcert.ps1
   ```

   # <a name="powershell"></a>[PowerShell](#tab/powershell)

   ```powershell
   cd $env:LOCALAPPDATA\CosmosDBEmulator\bind-mount
   .\importcert.ps1
   ```
   ---

1. Wenn Sie die interaktive Shell schließen, nachdem der Emulator gestartet wurde, wird der Container des Emulators geschlossen. Navigieren Sie in Ihrem Browser zu folgender URL, um den Daten-Explorer erneut zu öffnen. Der Emulator-Endpunkt wird in der oben gezeigten Antwortnachricht bereitgestellt.

   `https://<emulator endpoint provided in response>/_explorer/index.html`

Wenn Sie über eine in einem Linux-Docker-Container ausgeführte .NET-Clientanwendung verfügen und den Azure Cosmos DB-Emulator auf einem Hostcomputer ausführen, befolgen Sie die Anweisungen im nächsten Abschnitt, um das Zertifikat in den Linux-Docker-Container zu importieren.

## <a name="regenerate-the-emulator-certificates"></a>Erneutes Generieren von Emulatorzertifikaten

Wenn der Emulator in einem Docker-Container ausgeführt wird, werden die dem Emulator zugeordneten Zertifikate jedes Mal neu generiert, wenn Sie den jeweiligen Container beenden und neu starten. Aus diesem Grund müssen Sie die Zertifikate nach jedem Containerstart neu importieren. Um diese Einschränkung zu umgehen, können Sie eine Docker Compose-Datei verwenden, um den Docker-Container an eine bestimmte IP-Adresse und ein Containerimage zu binden.

Sie können z. B. die folgende Konfiguration innerhalb der Docker Compose-Datei verwenden. Achten Sie darauf, sie entsprechend Ihren Anforderungen zu formatieren: 

```yml
version: '2.4' # Do not upgrade to 3.x yet, unless you plan to use swarm/docker stack: https://github.com/docker/compose/issues/4513

networks:
  default:
    external: false
    ipam:
      driver: default
      config:
        - subnet: "172.16.238.0/24"

services:

  # First create a directory that will hold the emulator traces and certificate to be imported
  # set hostDirectory=C:\emulator\bind-mount
  # mkdir %hostDirectory%

  cosmosdb:
    container_name: "azurecosmosemulator"
    hostname: "azurecosmosemulator"
    image: 'mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator'
    platform: windows
    tty: true
    mem_limit: 3GB
    ports:
        - '8081:8081'
        - '8900:8900'
        - '8901:8901'
        - '8902:8902'
        - '10250:10250'
        - '10251:10251'
        - '10252:10252'
        - '10253:10253'
        - '10254:10254'
        - '10255:10255'
        - '10256:10256'
        - '10350:10350'
    networks:
      default:
        ipv4_address: 172.16.238.246
    volumes:
        - '${hostDirectory}:C:\CosmosDB.Emulator\bind-mount'
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie Sie den lokalen Emulator für die kostenlose lokale Entwicklung verwenden. Sie können jetzt mit dem nächsten Artikel fortfahren:

* [Exportieren der Azure Cosmos DB-Emulatorzertifikate für die Verwendung mit Java-, Python- und Node.js-Apps](local-emulator-export-ssl-certificates.md)
* [Befehlszeilen- und PowerShell-Referenz für den Azure Cosmos DB-Emulator](emulator-command-line-parameters.md)
* [Debuggen von Problemen mit dem Emulator](troubleshoot-local-emulator.md)
