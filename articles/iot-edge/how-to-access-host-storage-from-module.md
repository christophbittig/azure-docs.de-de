---
title: Verwenden des lokalen Speichers eines IoT Edge-Geräts von einem Modul aus – Azure IoT Edge | Microsoft-Dokumentation
description: Verwenden Sie Umgebungsvariablen und Erstellungsoptionen, um den Modulzugriff auf den lokalen Speicher eines IoT Edge-Geräts zu ermöglichen.
author: kgremban
ms.author: kgremban
ms.date: 08/14/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: fc89cbfd01ef827be277d332ecd770b3fa6d7016
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130249797"
---
# <a name="give-modules-access-to-a-devices-local-storage"></a>Gewähren des Zugriff auf den lokalen Speicher eines Geräts für Module

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Zusätzlich zum Speichern von Daten mithilfe von Azure Storage-Diensten oder im Containerspeicher Ihres Geräts können Sie auch Speicher auf dem eigentlichen IoT Edge-Hostgerät für verbesserte Zuverlässigkeit reservieren – insbesondere beim Offlinebetrieb.

## <a name="link-module-storage-to-device-storage"></a>Verknüpfen des Modulspeichers mit dem Gerätespeicher

Um einen Link zwischen dem Modulspeicher und dem Speicher auf dem Hostsystem zu aktivieren, erstellen Sie eine Umgebungsvariable für Ihr Modul, die auf einen Speicherordner im Container verweist. Binden Sie diesen Speicherordner anschließend mit den Erstellungsoptionen an einem Ordner auf dem Hostcomputer.

Wenn Sie beispielsweise möchten, dass der IoT Edge-Hub Nachrichten im lokalen Speicher Ihres Geräts speichert und später abruft, können Sie die Umgebungsvariablen und die Erstellungsoptionen über das Azure-Portal im Abschnitt **Runtimeeinstellungen** konfigurieren.

1. Fügen Sie sowohl für den IoT Edge-Hub als auch für den IoT Edge-Agent eine Umgebungsvariable namens **storageFolder** hinzu, die auf ein Verzeichnis im Modul verweist.
1. Fügen Sie sowohl für den IoT Edge-Hub als auch für den IoT Edge-Agent Bindungen hinzu, um ein lokales Verzeichnis auf dem Hostcomputer mit einem Verzeichnis im Modul zu verbinden. Beispiel:

   ![Fügen Sie Erstellungsoptionen und Umgebungsvariablen für den lokalen Speicher hinzu.](./media/how-to-access-host-storage-from-module/offline-storage.png)

Oder Sie können den lokalen Speicher auch direkt im Bereitstellungsmanifest konfigurieren. Beispiel:

```json
"systemModules": {
    "edgeAgent": {
        "settings": {
            "image": "mcr.microsoft.com/azureiotedge-agent:1.1",
            "createOptions": {
                "HostConfig": {
                    "Binds":["<HostStoragePath>:<ModuleStoragePath>"]
                }
            }
        },
        "type": "docker",
        "env": {
            "storageFolder": {
                "value": "<ModuleStoragePath>"
            }
        }
    },
    "edgeHub": {
        "settings": {
            "image": "mcr.microsoft.com/azureiotedge-hub:1.1",
            "createOptions": {
                "HostConfig": {
                    "Binds":["<HostStoragePath>:<ModuleStoragePath>"],
                    "PortBindings":{"5671/tcp":[{"HostPort":"5671"}],"8883/tcp":[{"HostPort":"8883"}],"443/tcp":[{"HostPort":"443"}]}}}
        },
        "type": "docker",
        "env": {
            "storageFolder": {
                "value": "<ModuleStoragePath>"
            }
        },
        "status": "running",
        "restartPolicy": "always"
    }
}
```

Ersetzen Sie `<HostStoragePath>` und `<ModuleStoragePath>` durch den Speicherpfad für Ihren Host und Ihr Modul; beide Werte müssen ein absoluter Pfad sein.

Beispielsweise bedeutet `"Binds":["/etc/iotedge/storage/:/iotedge/storage/"]` auf einem Linux-System, dass das Verzeichnis **/etc/iotedge/storage** in Ihrem Hostsystem dem Verzeichnis **/iotedge/storage/** im Container zugeordnet ist. Ein weiteres Beispiel: Auf einem Windows-System bedeutet `"Binds":["C:\\temp:C:\\contemp"]`, dass das Verzeichnis **C:\\temp** in Ihrem Hostsystem dem Verzeichnis **C:\\contemp** im Container zugeordnet ist.

Weitere Details zu Erstellungsoptionen finden Sie in der [Docker-Dokumentation](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate).

## <a name="host-system-permissions"></a>Hostsystemberechtigungen

Auf Linux-Geräten müssen Sie sicherstellen, dass das Benutzerprofil für Ihr Modul über die erforderlichen Lese-, Schreib- und Ausführungsberechtigungen für das Hostsystemverzeichnis verfügt. Zurück zum vorherigen Beispiel, in dem IoT Edge-Hub zum Speichern von Nachrichten im lokalen Speicher Ihres Geräts aktiviert wurde: Sie müssen Sie dem zugehörigen Benutzerprofil, UID 1000, Berechtigungen erteilen. Es gibt mehrere Möglichkeiten zum Verwalten von Verzeichnisberechtigungen auf Linux-Systemen, einschließlich der Verwendung von `chown` zum Ändern des Verzeichnisbesitzers und dann `chmod` zum Ändern der Berechtigungen, beispielsweise:

```bash
sudo chown 1000 <HostStoragePath>
sudo chmod 700 <HostStoragePath>
```

Auf Windows-Geräten müssen Sie ebenfalls Berechtigungen für das Hostsystemverzeichnis konfigurieren. Sie können PowerShell zum Festlegen von Berechtigungen verwenden:

```powershell
$acl = get-acl <HostStoragePath>
$ace = new-object system.security.AccessControl.FileSystemAccessRule('Authenticated Users','FullControl','Allow')
$acl.AddAccessRule($ace)
$acl | Set-Acl
```

## <a name="encrypted-data-in-module-storage"></a>Verschlüsselte Daten im Modulspeicher

Wenn Module die Workload-API des IoT Edge-Dämons aufrufen, um Daten zu verschlüsseln, wird der Verschlüsselungsschlüssel unter Verwendung der Modul-ID und der Generations-ID des Moduls abgeleitet. Eine Generations-ID wird verwendet, um Geheimnisse zu schützen, wenn ein Modul aus der Bereitstellung entfernt wird und dann später ein anderes Modul mit derselben Modul-ID auf demselben Gerät bereitgestellt wird. Sie können die Generations-ID eines Moduls mit dem Azure CLI-Befehl [az iot hub module-identity show](/cli/azure/iot/hub/module-identity) anzeigen.

Wenn Sie Dateien zwischen Modulen über Generationen hinweg austauschen möchten, dürfen sie keine Geheimnisse enthalten, da sie sonst nicht entschlüsselt werden können.

## <a name="next-steps"></a>Nächste Schritte

Ein weiteres Beispiel für den Zugriff auf den Hostspeicher von einem Modul aus finden Sie unter [Speichern von Daten im Edgebereich mit Azure Blob Storage in IoT Edge](how-to-store-data-blob.md).
