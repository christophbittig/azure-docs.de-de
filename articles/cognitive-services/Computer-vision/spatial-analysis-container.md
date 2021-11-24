---
title: 'Installieren und Ausführen des Containers für räumliche Analyse: Maschinelles Sehen'
titleSuffix: Azure Cognitive Services
description: Mit dem Container für räumliche Analyse können Sie Personen und Entfernungen erkennen.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 10/14/2021
ms.author: pafarley
ms.custom: ignite-fall-2021
ms.openlocfilehash: beda19bd951cf2750d071286ba066bb3a79c0e94
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132319281"
---
# <a name="install-and-run-the-spatial-analysis-container-preview"></a>Installieren und Ausführen des Containers für räumliche Analyse (Vorschau)

Mit dem Container für räumliche Analyse können Sie in Echtzeit gestreamte Videodaten analysieren, um räumliche Bezüge zwischen Personen, ihre Bewegungen und ihre Interaktionen mit Objekten in physischen Umgebungen zu verstehen. Container eignen sich hervorragend für bestimmte Sicherheits- und Datengovernanceanforderungen.

## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/cognitive-services)
* [!INCLUDE [contributor-requirement](../includes/quickstarts/contributor-requirement.md)]
* Wenn Sie über Ihr Azure-Abonnement verfügen, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="Erstellen einer Ressource für maschinelles Sehen"  target="_blank">erstellen Sie im Azure-Portal eine Ressource für maschinelles Sehen </a> für den Tarif Standard S1, um Ihren Schlüssel und Endpunkt abzurufen. Klicken Sie nach Abschluss der Bereitstellung auf **Zu Ressource wechseln**.
    * Sie benötigen den Schlüssel und den Endpunkt der Ressource, die Sie erstellen, um den Container für räumliche Analyse auszuführen. Sie benötigen Ihren Schlüssel und den Endpunkt zu einem späteren Zeitpunkt des Prozesses.

### <a name="spatial-analysis-container-requirements"></a>Anforderungen an einen Container für räumliche Analyse

Zum Ausführen des Containers für räumliche Analyse benötigen Sie ein Computegerät mit einer [NVIDIA Tesla T4-GPU](https://www.nvidia.com/en-us/data-center/tesla-t4/). Wir empfehlen Ihnen die Verwendung von [Azure Stack Edge](https://azure.microsoft.com/products/azure-stack/edge/) mit GPU-Beschleunigung. Der Container kann aber auch auf allen anderen Desktopcomputern ausgeführt werden, die die Mindestanforderungen erfüllen. Dieses Gerät wird als Hostcomputer bezeichnet.

#### <a name="azure-stack-edge-device"></a>[Azure Stack Edge-Gerät](#tab/azure-stack-edge)

Azure Stack Edge ist eine Hardware-as-a-Service-Lösung und ein KI-fähiges Edgecomputinggerät mit Netzwerkfunktionen für die Datenübertragung. Eine ausführliche Anleitung zur Vorbereitung und Einrichtung finden Sie in der [Azure Stack Edge-Dokumentation](../../databox-online/azure-stack-edge-deploy-prep.md).

#### <a name="desktop-machine"></a>[Desktopcomputer](#tab/desktop-machine)

#### <a name="minimum-hardware-requirements"></a>Hardwaremindestanforderungen

* 4 GB Systemarbeitsspeicher
* 4 GB GPU-Arbeitsspeicher
* CPU mit acht Kernen
* 1 NVIDIA Tesla T4-GPU
* 20 GB Festplattenspeicher

#### <a name="recommended-hardware"></a>Empfohlene Hardware

* 32 GB Systemarbeitsspeicher
* 16 GB GPU-Arbeitsspeicher
* CPU mit acht Kernen
* 2 NVIDIA Tesla T4-GPUs
* 50 GB SSD-Speicherplatz

In diesem Artikel führen Sie den Download und die Installation der folgenden Softwarepakete durch. Auf dem Hostcomputer muss Folgendes ausgeführt werden können (siehe Anleitung unten):

* [NVIDIA-Grafiktreiber](https://docs.nvidia.com/datacenter/tesla/tesla-installation-notes/index.html) und [NVIDIA CUDA-Toolkit](https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html)
* Konfigurationen für [NVIDIA MPS](https://docs.nvidia.com/deploy/pdf/CUDA_Multi_Process_Service_Overview.pdf) (Multi-Process Service, Multiprozessdienst)
* [Docker CE](https://docs.docker.com/install/linux/docker-ce/ubuntu/#install-docker-engine---community-1) und [NVIDIA-Docker2](https://github.com/NVIDIA/nvidia-docker) 
* [Azure IoT Edge](../../iot-edge/how-to-provision-single-device-linux-symmetric.md)-Runtime

#### <a name="azure-vm-with-gpu"></a>[Azure-VM mit GPU](#tab/virtual-machine)
In diesem Beispiel wird eine [VM der NC-Serie](../../virtual-machines/nc-series.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) mit einer K80-GPU verwendet.

---

| Anforderung | Beschreibung |
|--|--|
| Kamera | Der Container für räumliche Analyse ist nicht an eine bestimmte Kameramarke gebunden. Für das Kameragerät müssen die folgenden Bedingungen erfüllt sein: Unterstützung des Real-Time Streaming-Protokolls (RTSP) und der H.264-Codierung, Zugriff auf den Hostcomputer und Streaming mit einer Auflösung von 15 Bilder/Sek. und 1080p. |
| Linux-Betriebssystem | [Ubuntu Desktop 18.04 LTS](http://releases.ubuntu.com/18.04/) muss auf dem Hostcomupter installiert sein.  |

## <a name="set-up-the-host-computer"></a>Einrichten des Hostcomputers

Wir empfehlen Ihnen, ein Azure Stack Edge-Gerät für Ihren Hostcomputer zu verwenden. Klicken Sie auf **Desktopcomputer**, wenn Sie ein anderes Gerät konfigurieren, oder auf **Virtueller Computer**, wenn Sie eine VM verwenden.

#### <a name="azure-stack-edge-device"></a>[Azure Stack Edge-Gerät](#tab/azure-stack-edge)

### <a name="configure-compute-on-the-azure-stack-edge-portal"></a>Konfigurieren von Compute im Azure Stack Edge-Portal 
 
Bei der räumlichen Analyse werden die Computefeatures von Azure Stack Edge zum Ausführen einer KI-Lösung verwendet. Stellen Sie Folgendes sicher, um die Computefeatures zu aktivieren: 

* Sie haben Ihr Azure Stack Edge-Gerät [verbunden und aktiviert](../../databox-online/azure-stack-edge-deploy-connect-setup-activate.md). 
* Sie verfügen über ein Windows-Clientsystem mit PowerShell 5.0 oder höher, um auf das Gerät zuzugreifen.  
* Zum Bereitstellen eines Kubernetes-Clusters müssen Sie Ihr Azure Stack Edge-Gerät über die **lokale Benutzeroberfläche** im [Azure-Portal](https://portal.azure.com/) konfigurieren: 
  1. Aktivieren Sie das Computefeature auf Ihrem Azure Stack Edge-Gerät. Navigieren Sie zum Aktivieren von Compute auf der Weboberfläche Ihres Geräts zur Seite **Compute**. 
  2. Wählen Sie eine Netzwerkschnittstelle aus, die Sie für Compute aktivieren möchten, und klicken Sie anschließend auf **Aktivieren**. Auf Ihrem Gerät wird für diese Netzwerkschnittstelle ein virtueller Switch erstellt.
  3. Lassen Sie die Felder für die IP-Adressen der Kubernetes-Testknoten und der externen Kubernetes-Dienste leer.
  4. Klicken Sie auf **Übernehmen**. Dieser Vorgang kann ungefähr zwei Minuten dauern. 

![Konfigurieren der Computeumgebung](media/spatial-analysis/configure-compute.png)

### <a name="set-up-an-edge-compute-role-and-create-an-iot-hub-resource"></a>Einrichten einer Edgecomputingrolle und Erstellen einer IoT Hub-Ressource

Navigieren Sie im [Azure-Portal](https://portal.azure.com/) zu Ihrer Azure Stack Edge-Ressource. Klicken Sie auf der Seite **Übersicht** oder in der Navigationsliste auf die Schaltfläche **Erste Schritte**. Klicken Sie auf der Kachel  **Edgecomputing konfigurieren** auf **Konfigurieren**. 

![Link](media/spatial-analysis/configure-edge-compute-tile.png)

Wählen Sie auf der Seite  **Edgecomputing konfigurieren** eine vorhandene IoT Hub-Ressource aus, oder entscheiden Sie sich für die Neuerstellung. Standardmäßig wird zum Erstellen einer IoT Hub-Ressource ein Standard-Tarif (S1) verwendet. Wenn Sie eine IoT Hub-Ressource im Free-Tarif verwenden möchten, müssen Sie sie erstellen und anschließend auswählen. Für die IoT Hub-Ressource wird jeweils dasselbe Abonnement und dieselbe Ressourcengruppe wie für die Azure Stack Edge-Ressource genutzt. 

Klicken Sie auf **Erstellen**. Die Erstellung einer IoT Hub-Ressource kann einige Minuten dauern. Nachdem die IoT Hub-Ressource erstellt wurde, wird die Kachel  **Edgecomputing konfigurieren** aktualisiert, um die neue Konfiguration anzuzeigen. Wählen Sie auf der Kachel  **Compute konfigurieren** die Option  **Konfiguration anzeigen** aus, um sich zu vergewissern, dass die Edgecomputingrolle konfiguriert wurde.

Wenn die Edge-Computerolle auf dem Edge-Gerät eingerichtet ist, werden zwei Geräte erstellt: ein IoT-Gerät und ein IoT Edge-Gerät. Beide Geräte können in der IoT Hub-Ressource angezeigt werden. Die Azure IoT Edge-Runtime wird auf dem IoT Edge-Gerät bereits ausgeführt.

> [!NOTE]
> * Derzeit wird nur die Linux-Plattform für IoT Edge-Geräte unterstützt. Informationen zur Problembehandlung für das Azure Stack Edge-Gerät finden Sie im Artikel [Protokollierung und Problembehandlung](spatial-analysis-logging.md).
> * Weitere Informationen zum Konfigurieren eines IoT Edge-Geräts für die Kommunikation über einen Proxyserver finden Sie unter [Konfigurieren eines IoT Edge-Geräts für die Kommunikation über einen Proxyserver](../../iot-edge/how-to-configure-proxy-support.md#azure-portal).

###  <a name="enable-mps-on-azure-stack-edge"></a>Aktivieren von MPS auf Azure Stack Edge 

Befolgen Sie die folgenden Schritte, um eine Remoteverbindung von einem Windows-Client aus herzustellen.

1. Führen Sie eine Windows PowerShell-Sitzung als Administrator aus.
2. Stellen Sie sicher, dass der Dienst Windows-Remoteverwaltung auf dem Client ausgeführt wird. Geben Sie an der Eingabeaufforderung Folgendes ein:

    ```powershell
    winrm quickconfig
    ```

    Weitere Informationen finden Sie unter [Installation und Konfiguration für die Windows-Remoteverwaltung](/windows/win32/winrm/installation-and-configuration-for-windows-remote-management#quick-default-configuration).

3. Weisen Sie der in der Datei `hosts` verwendeten Verbindungszeichenfolge eine Variable zu.

    ```powershell
    $Name = "<Node serial number>.<DNS domain of the device>"
    ``` 

    Ersetzen Sie `<Node serial number>` und `<DNS domain of the device>` durch die Seriennummer des Knotens und die DNS-Domäne Ihres Geräts. Sie können die Werte für die Seriennummer des Knotens von der Seite **Zertifikate** und die DNS-Domäne von der Seite **Gerät** in der lokalen Webbenutzeroberfläche Ihres Geräts abrufen.

4. Geben Sie den folgenden Befehl ein, um der Liste der vertrauenswürdigen Hosts des Clients die Verbindungszeichenfolge für Ihr Gerät hinzuzufügen:

    ```powershell
    Set-Item WSMan:\localhost\Client\TrustedHosts $Name -Concatenate -Force
    ```

5. Starten Sie eine Windows PowerShell-Sitzung auf dem Gerät:

    ```powershell
    Enter-PSSession -ComputerName $Name -Credential ~\EdgeUser -ConfigurationName Minishell -UseSSL
    ```

    Wenn ein Fehler im Zusammenhang mit der Vertrauensstellung auftritt, überprüfen Sie, ob die Signaturkette des Knotenzertifikats, das auf Ihr Gerät hochgeladen wurde, auch auf dem Client installiert ist, der auf Ihr Gerät zugreift.

6. Geben Sie das Kennwort an, wenn Sie dazu aufgefordert werden. Verwenden Sie dasselbe Kennwort wie für die Anmeldung bei der lokalen Webbenutzeroberfläche. Das Standardkennwort für die lokale Webbenutzeroberfläche lautet *Password1*. Wenn Sie mithilfe von PowerShell eine Verbindung mit dem Gerät herstellen konnten, wird die folgende Beispielausgabe angezeigt:  

    ```
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved.
    
    PS C:\WINDOWS\system32> winrm quickconfig
    WinRM service is already running on this machine.
    PS C:\WINDOWS\system32> $Name = "1HXQG13.wdshcsso.com"
    PS C:\WINDOWS\system32> Set-Item WSMan:\localhost\Client\TrustedHosts $Name -Concatenate -Force
    PS C:\WINDOWS\system32> Enter-PSSession -ComputerName $Name -Credential ~\EdgeUser -ConfigurationName Minishell -UseSSL

    WARNING: The Windows PowerShell interface of your device is intended to be used only for the initial network configuration. Please engage Microsoft Support if you need to access this interface to troubleshoot any potential issues you may be experiencing. Changes made through this interface without involving Microsoft Support could result in an unsupported configuration.
    [1HXQG13.wdshcsso.com]: PS>
    ```

#### <a name="desktop-machine"></a>[Desktopcomputer](#tab/desktop-machine)

Befolgen Sie diese Anleitung, falls es sich bei Ihrem Hostcomputer nicht um ein Azure Stack Edge-Gerät handelt.

#### <a name="install-nvidia-cuda-toolkit-and-nvidia-graphics-drivers-on-the-host-computer"></a>Installieren des NVIDIA CUDA-Toolkits und der Nvidia-Grafiktreiber auf dem Hostcomputer

Verwenden Sie das folgende Bash-Skript zum Installieren der erforderlichen Nvidia-Grafiktreiber und des CUDA-Toolkits.

```bash
wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/cuda-ubuntu1804.pin
sudo mv cuda-ubuntu1804.pin /etc/apt/preferences.d/cuda-repository-pin-600
sudo apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/7fa2af80.pub
sudo add-apt-repository "deb http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/ /"
sudo apt-get update
sudo apt-get -y install cuda
```

Starten Sie den Computer neu, und führen Sie den unten angegebenen Befehl aus.

```bash
nvidia-smi
```

Die folgende Ausgabe wird angezeigt.

![NVIDIA-Treiberausgabe](media/spatial-analysis/nvidia-driver-output.png)

### <a name="install-docker-ce-and-nvidia-docker2-on-the-host-computer"></a>Installieren von Docker CE und nvidia-docker2 auf dem Hostcomputer

Installieren Sie Docker CE auf dem Hostcomputer.

```bash
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl gnupg-agent software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
sudo apt-get update
sudo apt-get install -y docker-ce docker-ce-cli containerd.io
```

Installieren Sie das Softwarepaket *nvidia-docker-2*.

```bash
distribution=$(. /etc/os-release;echo $ID$VERSION_ID)
curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | sudo apt-key add -
curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | sudo tee /etc/apt/sources.list.d/nvidia-docker.list
sudo apt-get update
sudo apt-get install -y docker-ce nvidia-docker2
sudo systemctl restart docker
```

## <a name="enable-nvidia-mps-on-the-host-computer"></a>Aktivieren von NVIDIA MPS auf dem Hostcomputer

> [!TIP]
> * Installieren Sie MPS nicht, wenn Ihre GPU-Computefunktion weniger als 7.x (vor Volta) beträgt. Weitere Informationen finden Sie unter [CUDA-Kompatibilität](https://docs.nvidia.com/deploy/cuda-compatibility/index.html#support-title). 
> * Führen Sie die MPS-Anleitung über ein Terminalfenster auf dem Hostcomputer aus. Führen Sie sie nicht auf Ihrer Docker-Containerinstanz aus.

Konfigurieren Sie die GPUs des Hostcomputers für [NVIDIA MPS](https://docs.nvidia.com/deploy/pdf/CUDA_Multi_Process_Service_Overview.pdf) (Multi-Process Service, Multiprozessdienst), um die beste Leistung und Auslastung zu erzielen. Führen Sie die MPS-Anleitung über ein Terminalfenster auf dem Hostcomputer aus.

```bash
# Set GPU(s) compute mode to EXCLUSIVE_PROCESS
sudo nvidia-smi --compute-mode=EXCLUSIVE_PROCESS

# Cronjob for setting GPU(s) compute mode to EXCLUSIVE_PROCESS on boot
echo "SHELL=/bin/bash" > /tmp/nvidia-mps-cronjob
echo "PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin" >> /tmp/nvidia-mps-cronjob
echo "@reboot   root    nvidia-smi --compute-mode=EXCLUSIVE_PROCESS" >> /tmp/nvidia-mps-cronjob

sudo chown root:root /tmp/nvidia-mps-cronjob
sudo mv /tmp/nvidia-mps-cronjob /etc/cron.d/

# Service entry for automatically starting MPS control daemon
echo "[Unit]" > /tmp/nvidia-mps.service
echo "Description=NVIDIA MPS control service" >> /tmp/nvidia-mps.service
echo "After=cron.service" >> /tmp/nvidia-mps.service
echo "" >> /tmp/nvidia-mps.service
echo "[Service]" >> /tmp/nvidia-mps.service
echo "Restart=on-failure" >> /tmp/nvidia-mps.service
echo "ExecStart=/usr/bin/nvidia-cuda-mps-control -f" >> /tmp/nvidia-mps.service
echo "" >> /tmp/nvidia-mps.service
echo "[Install]" >> /tmp/nvidia-mps.service
echo "WantedBy=multi-user.target" >> /tmp/nvidia-mps.service

sudo chown root:root /tmp/nvidia-mps.service
sudo mv /tmp/nvidia-mps.service /etc/systemd/system/

sudo systemctl --now enable nvidia-mps.service
```

## <a name="configure-azure-iot-edge-on-the-host-computer"></a>Konfigurieren von Azure IoT Edge auf dem Hostcomputer

Erstellen Sie zum Bereitstellen des Containers für räumliche Analyse auf dem Hostcomputer eine Instanz des Diensts [Azure IoT Hub](../../iot-hub/iot-hub-create-through-portal.md), indem Sie den Standard-Tarif (S1) oder Free-Tarif (F0) verwenden. 

Verwenden Sie die Azure CLI, um eine Instanz von Azure IoT Hub zu erstellen. Ersetzen Sie die Parameter nach Bedarf. Alternativ können Sie die Instanz von Azure IoT Hub auch über das [Azure-Portal](https://portal.azure.com/) erstellen.

```bash
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```
```bash
sudo az login
```
```bash
sudo az account set --subscription "<name or ID of Azure Subscription>"
```
```bash
sudo az group create --name "<resource-group-name>" --location "<your-region>"
```
Informationen zu den verfügbaren Regionen finden Sie unter [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services).
```bash
sudo az iot hub create --name "<iothub-group-name>" --sku S1 --resource-group "<resource-group-name>"
```
```bash
sudo az iot hub device-identity create --hub-name "<iothub-name>" --device-id "<device-name>" --edge-enabled
```

Sie müssen [Azure IoT Edge](../../iot-edge/how-to-provision-single-device-linux-symmetric.md) Version 1.0.9 installieren. Führen Sie die folgenden Schritte aus, um die richtige Version herunterzuladen:

Ubuntu Server 18.04:
```bash
curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
```

Kopieren Sie die generierte Liste.
```bash
sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
```

Installieren Sie den öffentlichen Schlüssel von Microsoft GPG.

```bash
curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
```
```bash
sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
```

Aktualisieren Sie die Paketlisten auf Ihrem Gerät.

```bash
sudo apt-get update
```

Installieren Sie das Release 1.0.9:

```bash
sudo apt-get install iotedge=1.0.9* libiothsm-std=1.0.9*
```

Registrieren Sie den Hostcomputer als Nächstes als IoT Edge-Gerät auf Ihrer IoT Hub-Instanz, indem Sie eine [Verbindungszeichenfolge](../../iot-edge/how-to-provision-single-device-linux-symmetric.md#register-your-device) verwenden.

Sie müssen das IoT Edge-Gerät mit Ihrer Azure IoT Hub-Instanz verbinden. Sie müssen die Verbindungszeichenfolge von dem IoT Edge-Gerät kopieren, das Sie weiter oben erstellt haben. Alternativ können Sie auch den unten angegebenen Befehl über die Azure CLI ausführen.

```bash
sudo az iot hub device-identity connection-string show --device-id my-edge-device --hub-name test-iot-hub-123
```

Öffnen Sie auf dem Hostcomputer `/etc/iotedge/config.yaml` zur Bearbeitung. Ersetzen Sie `ADD DEVICE CONNECTION STRING HERE` durch die Verbindungszeichenfolge. Speichern und schließen Sie die Datei. Führen Sie diesen Befehl aus, um den IoT Edge-Dienst auf dem Hostcomputer neu zu starten.

```bash
sudo systemctl restart iotedge
```

Stellen Sie den Container für räumliche Analyse über das [Azure-Portal](../../iot-edge/how-to-deploy-modules-portal.md) oder die [Azure CLI](../cognitive-services-apis-create-account-cli.md?tabs=windows) auf dem Hostcomputer als IoT-Modul bereit. Legen Sie bei Verwendung des Portals den Image-URI auf den Speicherort Ihrer Azure Container Registry-Instanz fest. 

Führen Sie die unten angegebenen Schritte aus, um den Container mit der Azure CLI bereitzustellen.

#### <a name="azure-vm-with-gpu"></a>[Azure-VM mit GPU](#tab/virtual-machine)

Ein virtueller Azure-Computer mit einer GPU kann auch verwendet werden, um eine räumliche Analyse durchzuführen. Im Beispiel unten wird eine VM der [NC-Serie](../../virtual-machines/nc-series.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) mit einer K80-GPU verwendet.

#### <a name="create-the-vm"></a>Erstellen des virtuellen Computers

Öffnen Sie den [Assistenten zum Erstellen eines virtuellen Computers](https://ms.portal.azure.com/#create/Microsoft.VirtualMachine) im Azure-Portal.

Geben Sie Ihrer VM einen Namen, und wählen Sie die Region „(USA) USA, Westen 2“ aus. 

> [!IMPORTANT]
> Stellen Sie sicher, dass Sie für „Verfügbarkeitsoptionen“ die Option „Keine Infrastrukturredundanz erforderlich.“ festlegen. Die folgende Abbildung zeigt die vollständige Konfiguration. Der nächste Schritt hilft Ihnen dabei, die richtige VM-Größe zu finden. 

:::image type="content" source="media/spatial-analysis/virtual-machine-instance-details.jpg" alt-text="Konfigurationsdetails für den virtuellen Computer" lightbox="media/spatial-analysis/virtual-machine-instance-details.jpg":::

Klicken Sie zum Finden der VM-Größe auf „See all sizes“ (Alle Größen anzeigen), und zeigen Sie dann die unten gezeigte Liste für „Non-premium storage VM sizes“ (VM-Größen ohne Storage Premium) an.

:::image type="content" source="media/spatial-analysis/virtual-machine-sizes.png" alt-text="VM-Größen" lightbox="media/spatial-analysis/virtual-machine-sizes.png":::

Wählen Sie dann entweder **NC6** oder **NC6_Promo** aus.

:::image type="content" source="media/spatial-analysis/promotional-selection.png" alt-text="Promoauswahl" lightbox="media/spatial-analysis/promotional-selection.png":::

Erstellen Sie anschließend die VM. Navigieren Sie nach der Erstellung im Azure-Portal zur VM-Ressource, und klicken Sie im linken Bereich auf „Erweiterungen“. Klicken Sie auf "Hinzufügen", um das Erweiterungsfenster mit allen verfügbaren Erweiterungen zu öffnen. Suchen Sie nach `NVIDIA GPU Driver Extension` und wählen Sie es aus, klicken Sie auf Erstellen und schließen Sie den Assistenten ab.

Navigieren Sie nach dem erfolgreichen Anwenden der Erweiterung zur VM-Hauptseite im Azure-Portal, und klicken Sie auf „Verbinden“. Auf die VM kann entweder über SSH oder über das RDP zugegriffen werden. Das RDP ist hilfreich, da es das Anzeigen des Schnellansichtsfensters ermöglicht (wird später erläutert). Konfigurieren Sie den RDP-Zugriff, indem Sie [diese Schritte](../../virtual-machines/linux/use-remote-desktop.md) ausführen und eine Remotedesktopverbindung mit der VM öffnen.

### <a name="verify-graphics-drivers-are-installed"></a>Überprüfen, ob die Grafiktreiber installiert sind

Führen Sie den folgenden Befehl aus, um zu überprüfen, ob die Grafiktreiber erfolgreich installiert wurden. 

```bash
nvidia-smi
```

Die folgende Ausgabe wird angezeigt.

![NVIDIA-Treiberausgabe](media/spatial-analysis/nvidia-driver-output.png)

### <a name="install-docker-ce-and-nvidia-docker2-on-the-vm"></a>Installieren von Docker CE und nvidia-docker2 auf der VM

Führen Sie die folgenden Befehle nacheinander aus, um Docker CE und nvidia-docker2 auf der VM zu installieren.

Installieren Sie Docker CE auf dem Hostcomputer.

```bash
sudo apt-get update
```
```bash
sudo apt-get install -y apt-transport-https ca-certificates curl gnupg-agent software-properties-common
```
```bash
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```
```bash
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
```
```bash
sudo apt-get update
```
```bash
sudo apt-get install -y docker-ce docker-ce-cli containerd.io
```


Installieren Sie das Softwarepaket *nvidia-docker-2*.

```bash
distribution=$(. /etc/os-release;echo $ID$VERSION_ID)
```
```bash
curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | sudo apt-key add -
```
```bash
curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | sudo tee /etc/apt/sources.list.d/nvidia-docker.list
```
```bash
sudo apt-get update
```
```bash
sudo apt-get install -y docker-ce nvidia-docker2
```
```bash
sudo systemctl restart docker
```

Nachdem Sie Ihre VM eingerichtet und konfiguriert haben, befolgen Sie die folgenden Schritten, um Azure IoT Edge zu konfigurieren. 

## <a name="configure-azure-iot-edge-on-the-vm"></a>Konfigurieren von Azure IoT Edge auf der VM

Erstellen Sie zum Bereitstellen des Containers für räumliche Analyse auf der VM eine Instanz des Diensts [Azure IoT Hub](../../iot-hub/iot-hub-create-through-portal.md), indem Sie den Standard-Tarif (S1) oder Free-Tarif (F0) verwenden.

Verwenden Sie die Azure CLI, um eine Instanz von Azure IoT Hub zu erstellen. Ersetzen Sie die Parameter nach Bedarf. Alternativ können Sie die Instanz von Azure IoT Hub auch über das [Azure-Portal](https://portal.azure.com/) erstellen.

```bash
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```
```bash
sudo az login
```
```bash
sudo az account set --subscription "<name or ID of Azure Subscription>"
```
```bash
sudo az group create --name "<resource-group-name>" --location "<your-region>"
```
Informationen zu den verfügbaren Regionen finden Sie unter [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services).
```bash
sudo az iot hub create --name "<iothub-name>" --sku S1 --resource-group "<resource-group-name>"
```
```bash
sudo az iot hub device-identity create --hub-name "<iothub-name>" --device-id "<device-name>" --edge-enabled
```

Sie müssen [Azure IoT Edge](../../iot-edge/how-to-provision-single-device-linux-symmetric.md) Version 1.0.9 installieren. Führen Sie die folgenden Schritte aus, um die richtige Version herunterzuladen:

Ubuntu Server 18.04:
```bash
curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
```

Kopieren Sie die generierte Liste.
```bash
sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
```

Installieren Sie den öffentlichen Schlüssel von Microsoft GPG.

```bash
curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
```
```bash
sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
```

Aktualisieren Sie die Paketlisten auf Ihrem Gerät.

```bash
sudo apt-get update
```

Installieren Sie das Release 1.0.9:

```bash
sudo apt-get install iotedge=1.0.9* libiothsm-std=1.0.9*
```

Registrieren Sie die VM als nächstes als IoT Edge-Gerät auf Ihrer IoT Hub-Instanz, indem Sie eine [Verbindungszeichenfolge](../../iot-edge/how-to-provision-single-device-linux-symmetric.md#register-your-device) verwenden.

Sie müssen das IoT Edge-Gerät mit Ihrer Azure IoT Hub-Instanz verbinden. Sie müssen die Verbindungszeichenfolge von dem IoT Edge-Gerät kopieren, das Sie weiter oben erstellt haben. Alternativ können Sie auch den unten angegebenen Befehl über die Azure CLI ausführen.

```bash
sudo az iot hub device-identity connection-string show --device-id my-edge-device --hub-name test-iot-hub-123
```

Öffnen Sie `/etc/iotedge/config.yaml` auf der VM zur Bearbeitung. Ersetzen Sie `ADD DEVICE CONNECTION STRING HERE` durch die Verbindungszeichenfolge. Speichern und schließen Sie die Datei. Führen Sie diesen Befehl aus, um den IoT Edge-Dienst auf der VM neu zu starten.

```bash
sudo systemctl restart iotedge
```

Stellen Sie den Container für räumliche Analyse über das [Azure-Portal](../../iot-edge/how-to-deploy-modules-portal.md) oder die [Azure CLI](../cognitive-services-apis-create-account-cli.md?tabs=windows) auf der VM als IoT-Modul bereit. Legen Sie bei Verwendung des Portals den Image-URI auf den Speicherort Ihrer Azure Container Registry-Instanz fest. 

Führen Sie die unten angegebenen Schritte aus, um den Container mit der Azure CLI bereitzustellen.

---

### <a name="iot-deployment-manifest"></a>IoT-Bereitstellungsmanifest

Zum Optimieren der Containerbereitstellung auf mehreren Hostcomputern können Sie eine Bereitstellungsmanifestdatei erstellen, um die Optionen für die Containererstellung und die Umgebungsvariablen anzugeben. Beispiele für ein Bereitstellungsmanifest [für Azure Stack Edge](https://go.microsoft.com/fwlink/?linkid=2142179), [andere Desktopcomputer](https://go.microsoft.com/fwlink/?linkid=2152270) und [Azure-VMs mit GPU](https://go.microsoft.com/fwlink/?linkid=2152189) finden Sie auf GitHub.

In der folgenden Tabelle sind die verschiedenen Umgebungsvariablen aufgeführt, die vom IoT Edge-Modul verwendet werden. Sie können sie auch im oben verlinkten Bereitstellungsmanifest festlegen, indem Sie das Attribut `env` in `spatialanalysis` verwenden:

| Einstellungsname | Wert | Beschreibung|
|---------|---------|---------|
| ARCHON_LOG_LEVEL | Info, Verbose (Information, Ausführlich) | Protokolliergrad: Wählen Sie einen der beiden Werte aus.|
| ARCHON_SHARED_BUFFER_LIMIT | 377487360 | Nicht ändern|
| ARCHON_PERF_MARKER| false| Legen Sie diese Einstellung auf „true“ fest, wenn Sie die Leistungsprotokollierung verwenden möchten, und andernfalls auf „false“.| 
| ARCHON_NODES_LOG_LEVEL | Info, Verbose (Information, Ausführlich) | Protokolliergrad: Wählen Sie einen der beiden Werte aus.|
| OMP_WAIT_POLICY | PASSIVE | Nicht ändern|
| QT_X11_NO_MITSHM | 1 | Nicht ändern|
| APIKEY | Ihr API-Schlüssel| Ermitteln Sie diesen Wert im Azure-Portal unter Ihrer Maschinelles Sehen-Ressource. Sie finden ihn im Abschnitt **Schlüssel und Endpunkt** für Ihre Ressource. |
| ABRECHNUNG | Ihr Endpunkt-URI| Ermitteln Sie diesen Wert im Azure-Portal unter Ihrer Maschinelles Sehen-Ressource. Sie finden ihn im Abschnitt **Schlüssel und Endpunkt** für Ihre Ressource.|
| ENDBENUTZER-LIZENZVERTRAG | accept (Akzeptieren) | Sie müssen diesen Wert auf *accept* (Akzeptieren) festlegen, damit der Container ausgeführt werden kann. |
| DISPLAY | :1 | Dieser Wert muss der Ausgabe von `echo $DISPLAY` auf dem Hostcomputer entsprechen. Azure Stack Edge-Geräte haben kein Display. Diese Einstellung ist nicht anwendbar.|
| ARCHON_GRAPH_READY_TIMEOUT | 600 | Fügen Sie diese Umgebungsvariable hinzu, wenn Ihre GPU **nicht** T4 oder NVIDIA 2080 Ti ist.|
| ORT_TENSORRT_ENGINE_CACHE_ENABLE | 0 | Fügen Sie diese Umgebungsvariable hinzu, wenn Ihre GPU **nicht** T4 oder NVIDIA 2080 Ti ist.|
| KEY_ENV | ASE-Verschlüsselungsschlüssel | Fügen Sie diese Umgebungsvariable hinzu, wenn der Wert für „VIDEO_URL“ eine verschleierte Zeichenfolge ist. |
| IV_ENV | Initialisierungsvektor | Fügen Sie diese Umgebungsvariable hinzu, wenn der Wert für „VIDEO_URL“ eine verschleierte Zeichenfolge ist.|

> [!IMPORTANT]
> Die Optionen `Eula`, `Billing` und `ApiKey` müssen angegeben werden, um den Container auszuführen, andernfalls wird der Container nicht gestartet.  Weitere Informationen finden Sie unter [Abrechnung](#billing).

Sobald Sie das Bereitstellungsmanifest für [Azure Stack Edge-Geräte](https://go.microsoft.com/fwlink/?linkid=2142179), [einen Desktopcomputer](https://go.microsoft.com/fwlink/?linkid=2152270) oder eine [Azure-VM mit GPU](https://go.microsoft.com/fwlink/?linkid=2152189) mit Ihren eigenen Einstellungen und Ihrer Auswahl von Vorgängen aktualisiert haben, können Sie mit dem unten dargestellten [Azure CLI](../cognitive-services-apis-create-account-cli.md?tabs=windows)-Befehl den Container auf dem Hostcomputer als IoT Edge-Modul bereitstellen.

```azurecli
sudo az login
sudo az extension add --name azure-iot
sudo az iot edge set-modules --hub-name "<iothub-name>" --device-id "<device-name>" --content DeploymentManifest.json --subscription "<name or ID of Azure Subscription>"
```

|Parameter  |Beschreibung  |
|---------|---------|
| `--hub-name` | Der Name Ihrer Azure IoT Hub-Instanz. |
| `--content` | Der Name der Bereitstellungsdatei. |
| `--target-condition` | Der Name Ihres IoT Edge-Geräts für den Hostcomputer. |
| `-–subscription` | Abonnement-ID oder -name. |

Mit diesem Befehl wird die Bereitstellung gestartet. Navigieren Sie im Azure-Portal zur Seite mit Ihrer Azure IoT Hub-Instanz, um den Bereitstellungsstatus anzuzeigen. Als Status wird möglicherweise *417: Die Bereitstellungskonfiguration des Geräts ist nicht festgelegt* angezeigt, bis das Gerät das Herunterladen der Containerimages abgeschlossen hat und gestartet wird.

## <a name="validate-that-the-deployment-is-successful"></a>Überprüfen der erfolgreichen Bereitstellung

Es gibt mehrere Möglichkeiten zu überprüfen, ob ein Container aktiv ist. Suchen Sie im Azure-Portal in den **Einstellungen des IoT Edge-Moduls** für die räumliche Analyse in Ihrer Azure IoT Hub-Instanz nach dem *Laufzeitstatus*. Vergewissern Sie sich, dass **Gewünschter Wert** und **Gemeldeter Wert** für *Laufzeitstatus* auf *Wird ausgeführt* festgelegt sind.

![Beispiel für die Bereitstellungsüberprüfung](./media/spatial-analysis/deployment-verification.png)

Nach Abschluss der Bereitstellung und dem Beginn der Containerausführung beginnt der **Hostcomputer** damit, Ereignisse an Azure IoT Hub zu senden. Wenn Sie die `.debug`-Version der Vorgänge verwendet haben, wird ein Visualisierungsfenster für jede Kamera angezeigt, die Sie im Bereitstellungsmanifest konfiguriert haben. Sie können jetzt die zu überwachenden Linien und Zonen im Bereitstellungsmanifest definieren und die Anleitung zur erneuten Bereitstellung befolgen. 

## <a name="configure-the-operations-performed-by-spatial-analysis"></a>Konfigurieren der von der räumlichen Analyse durchgeführten Vorgänge

Sie müssen die [Vorgänge der räumlichen Analyse](spatial-analysis-operations.md) nutzen, um den Container für die Verwendung von vernetzten Kameras zu konfigurieren, die Vorgänge zu konfigurieren usw. Für jedes von Ihnen konfigurierte Kameragerät wird über die Vorgänge der räumlichen Analyse ein Ausgabestream mit JSON-Nachrichten generiert, die an Ihre Instanz von Azure IoT Hub gesendet werden.

## <a name="use-the-output-generated-by-the-container"></a>Verwenden der vom Container generierten Ausgabe

Falls Sie die vom Container generierte Ausgabe nutzen möchten, helfen Ihnen die Informationen in den folgenden Artikeln weiter:

*    Verwenden Sie das Azure Event Hub-SDK für Ihre gewählte Programmiersprache, um eine Verbindung mit dem Azure IoT Hub-Endpunkt herstellen und die Ereignisse empfangen zu können. Weitere Informationen finden Sie unter [Lesen von Nachrichten, die von Geräten an die Cloud gesendet werden, vom integrierten Endpunkt](../../iot-hub/iot-hub-devguide-messages-read-builtin.md). 
*    Richten Sie das Nachrichtenrouting auf Ihrer Azure IoT Hub-Instanz ein, um Ereignisse an andere Endpunkte zu senden, die Ereignisse unter Azure Blob Storage zu speichern usw. Weitere Informationen finden Sie unter [Verwenden des IoT Hub-Nachrichtenroutings zum Senden von D2C-Nachrichten an verschiedene Endpunkte](../../iot-hub/iot-hub-devguide-messages-d2c.md). 

## <a name="running-spatial-analysis-with-a-recorded-video-file"></a>Ausführen der räumlichen Analyse mit einer aufgezeichneten Videodatei

Sie können die räumliche Analyse sowohl für aufgezeichnete Videos als auch für Livevideos verwenden. Versuchen Sie, eine Videodatei aufzuzeichnen und als MP4-Datei zu speichern, um die räumliche Analyse für ein aufgezeichnetes Video zu verwenden. Erstellen Sie ein Blobspeicherkonto in Azure, oder verwenden Sie ein vorhandenes Konto. Aktualisieren Sie anschließend im Azure-Portal die folgenden Blobspeichereinstellungen:
    1. Ändern Sie **Sichere Übertragung erforderlich** in **Deaktiviert**.
    2. Ändern Sie **Öffentlichen Blobzugriff gestatten** in **Aktiviert**.

Navigieren Sie zum Abschnitt **Container**, und erstellen Sie entweder einen neuen Container, oder verwenden Sie einen vorhandenen. Laden Sie anschließend die Videodatei in den Container hoch. Erweitern Sie die Dateieinstellungen für die hochgeladene Datei, und wählen Sie die Option **SAS generieren** aus. Achten Sie darauf, dass Sie unter **Ablaufdatum** einen Zeitraum angeben, der den Testzeitraum abdeckt. Legen Sie **Zulässige Protokolle** auf *HTTP* fest (*HTTPS* wird nicht unterstützt).

Klicken Sie auf **SAS-Token und -URL generieren**, und kopieren Sie die Blob-SAS-URL. Ersetzen Sie `https` am Anfang durch `http`, und testen Sie die URL in einem Browser, der die Videowiedergabe unterstützt.

Ersetzen Sie `VIDEO_URL` im Bereitstellungsmanifest für Ihr [Azure Stack Edge-Gerät](https://go.microsoft.com/fwlink/?linkid=2142179), Ihren [Desktopcomputer](https://go.microsoft.com/fwlink/?linkid=2152270) oder Ihre [Azure-VM mit GPU](https://go.microsoft.com/fwlink/?linkid=2152189) durch die von Ihnen erstellte URL, und zwar für alle Graphen. Legen Sie `VIDEO_IS_LIVE` auf `false` fest, und stellen Sie den Container für räumliche Analyse mit dem aktualisierten Manifest erneut bereit. Betrachten Sie das folgende Beispiel.

Das Modul für die räumliche Analyse beginnt mit der Nutzung der Videodatei und wird fortlaufend automatisch wiedergegeben.


```json
"zonecrossing": {
    "operationId" : "cognitiveservices.vision.spatialanalysis-personcrossingpolygon",
    "version": 1,
    "enabled": true,
    "parameters": {
        "VIDEO_URL": "Replace http url here",
        "VIDEO_SOURCE_ID": "personcountgraph",
        "VIDEO_IS_LIVE": false,
      "VIDEO_DECODE_GPU_INDEX": 0,
        "DETECTOR_NODE_CONFIG": "{ \"gpu_index\": 0, \"do_calibration\": true }",
        "SPACEANALYTICS_CONFIG": "{\"zones\":[{\"name\":\"queue\",\"polygon\":[[0.3,0.3],[0.3,0.9],[0.6,0.9],[0.6,0.3],[0.3,0.3]], \"events\": [{\"type\": \"zonecrossing\", \"config\": {\"threshold\": 16.0, \"focus\": \"footprint\"}}]}]}"
    }
   },

```

## <a name="troubleshooting"></a>Problembehandlung

Falls beim Starten oder Ausführen des Containers Probleme auftreten, helfen Ihnen die Lösungsschritte für häufige Probleme im Artikel [Telemetrie und Problembehandlung](spatial-analysis-logging.md) weiter. Dieser Artikel enthält auch Informationen zum Generieren und Erfassen von Protokollen und zur Erfassung der Systemintegrität.

[!INCLUDE [Diagnostic container](../containers/includes/diagnostics-container.md)]

## <a name="billing"></a>Abrechnung

Der Container für räumliche Analyse sendet Abrechnungsinformationen an Azure und verwendet dafür eine Ressource für maschinelles Sehen in Ihrem Azure-Konto. Die Verwendung der räumlichen Analyse im Rahmen der öffentlichen Vorschauversion ist derzeit kostenlos. 

Für die Ausführung von Azure Cognitive Services-Containern besteht keine Lizenz, wenn sie nicht mit dem Endpunkt für Messung/Abrechnung verbunden sind. Sie müssen sicherstellen, dass die Container jederzeit Abrechnungsinformationen an den Abrechnungsendpunkt übermitteln können. Cognitive Services-Container senden keine Kundendaten, z. B. das Video oder das analysierte Bild, an Microsoft.


## <a name="summary"></a>Zusammenfassung

In diesem Artikel wurden die Konzepte und der Workflow zum Herunterladen, Installieren und Ausführen des Containers für die räumliche Analyse beschrieben. Zusammenfassung:

* Bei der räumlichen Analyse handelt es sich um einen Linux-Container für Docker.
* Containerimages werden aus Microsoft Container Registry heruntergeladen.
* Containerimages werden als IoT-Module in Azure IoT Edge ausgeführt.
* Konfigurieren des Containers und Bereitstellen auf einem Hostcomputer

## <a name="next-steps"></a>Nächste Schritte

* [Bereitstellen einer Webanwendung für die Erfassung der Personenanzahl](spatial-analysis-web-app.md)
* [Konfigurieren von Vorgängen zur räumlichen Analyse](spatial-analysis-operations.md)
* [Protokollierung und Problembehandlung](spatial-analysis-logging.md)
* [Leitfaden zur Kameraplatzierung](spatial-analysis-camera-placement.md)
* [Leitfaden zur Platzierung von Zonen und Linien](spatial-analysis-zone-line-placement.md)
