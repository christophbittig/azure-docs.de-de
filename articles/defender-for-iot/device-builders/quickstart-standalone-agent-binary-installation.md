---
title: 'Schnellstart: Installieren eines Micro-Agents für Defender für IoT (Vorschau)'
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie den Micro-Agent für Defender für Cloud installieren und authentifizieren.
ms.date: 11/09/2021
ms.topic: quickstart
ms.openlocfilehash: 5e1ef76bbaf3b4eedae31f08d6cf39efdfaec3df
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132343741"
---
# <a name="quickstart-install-defender-for-iot-micro-agent-preview"></a>Schnellstart: Installieren eines Micro-Agents für Defender für IoT (Vorschau)

In diesem Artikel erfahren Sie, wie Sie den Micro-Agent für Defender für Cloud installieren und authentifizieren.

## <a name="prerequisites"></a>Voraussetzungen

Vor der Installation des Moduls „Defender für IoT“ muss in IoT Hub eine Modulkennung erstellt werden. Weitere Informationen zum Erstellen einer Modulkennung finden Sie unter [Schnellstart: Erstellen eines IoT-Micro-Agent-Modulzwillings für Defender (Vorschau)](quickstart-create-micro-agent-module-twin.md).

## <a name="install-the-package"></a>Installieren des Pakets

**So fügen Sie das passende Microsoft-Paketrepository hinzu**

1. Laden Sie die zum Betriebssystem Ihres Geräts passende Repositorykonfiguration herunter.  

    - Für Ubuntu 18.04

        ```bash
        curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
        ```

    - Für Ubuntu 20.04

        ```bash
            curl https://packages.microsoft.com/config/ubuntu/20.04/prod.list > ./microsoft-prod.list
        ```

    - Für Debian 9 (AMD64 und ARM64)

        ```bash
        curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
        ```

1. Kopieren Sie die Repositorykonfiguration in das Verzeichnis `sources.list.d`.

    ```bash
    sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
    ```

1. Installieren Sie den öffentlichen Schlüssel von Microsoft GPG:

    ```bash
    curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
    sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
    ```

Verwenden Sie den folgenden Befehl, um das Paket des Micro-Agents für Defender für Cloud unter Debian oder unter Ubuntu-basierten Linux-Distributionen zu installieren:

```bash
sudo apt-get install defender-iot-micro-agent 
```

## <a name="micro-agent-authentication-methods"></a>Authentifizierungsmethoden für den Micro-Agent

Zur Authentifizierung des Micro-Agents für Defender für IoT werden die beiden folgenden Optionen verwendet:

- Verbindungszeichenfolge für Modulkennung

- Zertifikat

### <a name="authenticate-using-a-module-identity-connection-string"></a>Authentifizierung per Verbindungszeichenfolge für Modulkennung

Stellen Sie sicher, dass die [Voraussetzungen](#prerequisites) für diesen Artikel erfüllt sind, und erstellen Sie vor diesen Schritten unbedingt eine Modulkennung.

#### <a name="get-the-module-identity-connection-string"></a>Abrufen der Verbindungszeichenfolge für die Modulkennung

So rufen Sie die Verbindungszeichenfolge für die Modulkennung aus IoT Hub ab:

1. Navigieren Sie zu IoT Hub, und wählen Sie Ihren Hub aus.

1. Wählen Sie im Menü auf der linken Seite im Abschnitt **Explorer** die Option **IoT-Geräte** aus.

   :::image type="content" source="media/quickstart-standalone-agent-binary-installation/iot-devices.png" alt-text="Auswählen von „IoT-Geräte“ im linken Menü":::

1. Wählen Sie ein Gerät aus der Liste der Geräte-IDs aus, um die Seite **Details zum Gerät** anzuzeigen.

1. Wählen Sie die Registerkarte  **Modulidentitäten**  aus.

1. Wählen Sie das Modul  **DefenderIotMicroAgent**  aus der Liste der Modulidentitäten aus, die dem Gerät zugeordnet sind.

   :::image type="content" source="media/quickstart-standalone-agent-binary-installation/module-identities.png" alt-text="Auswählen der Registerkarte „Modulkennungen“":::

1. Kopieren Sie auf der Seite **Details zur Modulkennung** die Verbindungszeichenfolge (den Primärschlüssel), indem Sie die Schaltfläche **Kopieren** auswählen.

   :::image type="content" source="media/quickstart-standalone-agent-binary-installation/copy-button.png" alt-text="Auswählen der Schaltfläche „Kopieren“ zum Kopieren der Verbindungszeichenfolge (des Primärschlüssels)":::

#### <a name="configure-authentication-using-a-module-identity-connection-string"></a>Konfigurieren der Authentifizierung mithilfe einer Verbindungszeichenfolge für die Modulkennung

So konfigurieren Sie den Agent für die Authentifizierung mithilfe einer Verbindungszeichenfolge für die Modulkennung:

1. Platzieren Sie eine Datei mit dem Namen `connection_string.txt`, die die UTF-8-codierte Verbindungszeichenfolge enthält, im Verzeichnis `/var/defender_iot_micro_agent` des Defender für Cloud-Agents. Geben Sie hierzu den folgenden Befehl ein:

    ```bash
    sudo bash -c 'echo "<connection string>" > /var/defender_iot_micro_agent/connection_string.txt'
    ```

    Die Datei `connection_string.txt` sollte sich am folgenden Pfad befinden: `/var/defender_iot_micro_agent/connection_string.txt`.

1. Starten Sie den Dienst mithilfe des folgenden Befehls neu:  

    ```bash
    sudo systemctl restart defender-iot-micro-agent.service 
    ```

### <a name="authenticate-using-a-certificate"></a>Authentifizieren mithilfe eines Zertifikats

So verwenden Sie die Authentifizierung per Zertifikat:

1. Gehen Sie wie [hier](../../iot-hub/tutorial-x509-scripts.md) beschrieben vor, um ein Zertifikat zu erhalten.

1. Platzieren Sie den PEM-codierten öffentlichen Teil des Zertifikats und den privaten Schlüssel im Verzeichnis des Agents für Defender für Cloud in der Datei `certificate_public.pem`bzw. `certificate_private.pem`.

1. Platzieren Sie die passende Verbindungszeichenfolge in der Datei `connection_string.txt`. Die Verbindungszeichenfolge sollte wie folgt aussehen:

    `HostName=<the host name of the iot hub>;DeviceId=<the id of the device>;ModuleId=<the id of the module>;x509=true`

    Durch diese Zeichenfolge wird der Agent für Defender für Cloud darauf vorbereitet, dass ein Zertifikat für die Authentifizierung bereitgestellt wird.

1. Starten Sie den Dienst mithilfe des folgenden Befehls neu:  

    ```bash
    sudo systemctl restart defender-iot-micro-agent.service
    ```

### <a name="validate-your-installation"></a>Überprüfen Ihrer Installation

So überprüfen Sie Ihre Installation:

1. Vergewissern Sie sich mithilfe des folgenden Befehls, dass der Micro-Agent ordnungsgemäß ausgeführt wird:  

    ```bash
    systemctl status defender-iot-micro-agent.service
    ```

1. Vergewissern Sie sich, dass der Dienst stabil ist (`active` und angemessene Uptime des Prozesses):

    :::image type="content" source="media/quickstart-standalone-agent-binary-installation/active-running.png" alt-text="Vergewissern, dass Ihr Dienst stabil und aktiv ist.":::

## <a name="testing-the-system-end-to-end"></a>Umfassendes Testen des Systems

Sie können das System umfassend testen, indem Sie auf dem Gerät eine Triggerdatei erstellen. Die Triggerdatei sorgt dafür, dass die Baselineüberprüfung im Agent die Datei als Baselineverstoß erkennt.

Erstellen Sie mithilfe des folgenden Befehls eine Datei im Dateisystem:

```bash
sudo touch /tmp/DefenderForIoTOSBaselineTrigger.txt 
```

Im Hub wird eine Empfehlung für einen Baselinevalidierungsfehler angezeigt. `CceId` hat hierbei den Wert „CIS-debian-9-DEFENDER_FOR_IOT_TEST_CHECKS-0.0“:

:::image type="content" source="media/quickstart-standalone-agent-binary-installation/validation-failure.png" alt-text="Empfehlung für Baselinevalidierungsfehler im Hub" lightbox="media/quickstart-standalone-agent-binary-installation/validation-failure-expanded.png":::

Es kann bis zu einer Stunde dauern, bis die Empfehlung im Hub angezeigt wird.

## <a name="micro-agent-versioning"></a>Versionsverwaltung für den Micro-Agent

Wenn Sie eine bestimmte Version des Micro-Agents für Defender für Cloud installieren möchten, führen Sie den folgenden Befehl aus:

```bash
sudo apt-get install defender-iot-micro-agent=<version>
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Schnellstart: Erstellen eines IoT-Micro-Agent-Modulzwillings für Defender für Cloud (Vorschau)](quickstart-create-micro-agent-module-twin.md)
