---
title: 'Referenz: Bekannte Probleme und Problembehandlung'
titleSuffix: Azure Data Science Virtual  Machine
description: Hier finden Sie eine Liste mit bekannten Problemen, Problemumgehungen und Problembehandlungsinformationen für Azure Data Science Virtual Machine.
services: machine-learning
ms.service: data-science-vm
author: michalmar
ms.author: mimarusa
ms.topic: reference
ms.date: 08/02/2021
ms.openlocfilehash: c928dfa133172f83c097aa9df7d92486524f62ff
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122346163"
---
# <a name="known-issues-and-troubleshooting-the-azure-data-science-virtual-machine"></a>Bekannte Probleme und Problembehandlung für Azure Data Science Virtual Machine

Dieser Artikel hilft Ihnen, Fehler oder Ausfälle zu ermitteln und zu beheben, auf die Sie bei der Verwendung der Azure Data Science Virtual Machine stoßen könnten.


## <a name="ubuntu"></a>Ubuntu

### <a name="fix-gpu-on-nvidia-a100-gpu-chip---azure-ndasrv4-series"></a>Korrektur der GPU beim NVIDIA A100-GPU-Chip – Azure NDasrv4-Serie 

Die VM der ND A100 v4-Serie ist ein neues Flagship-Mitglied der Azure-GPU-Familie, die für hoch anspruchsvolles Deep Learning-Training und eng gekoppelte, vertikal und horizontal skalierende HPC-Workloads konzipiert ist.

Aufgrund der abweichenden Architektur ist ein anderes Setup für Ihre anspruchsvollen Workloads erforderlich, um von der GPU-Beschleunigung mit TensorFlow- oder PyTorch-Frameworks zu profitieren.

Wir arbeiten daran, die ND A100-GPUs direkt und ohne weitere Konfiguration zu unterstützen. In der Zwischenzeit können Sie Ihre GPU funktionsfähig machen, indem Sie den NVIDIA Fabric Manager hinzufügen und die Treiber aktualisieren. 

Führen Sie im Terminal die folgenden einfachen Schritte aus:

1. Fügen Sie das NVIDIA-Repository hinzu, um Treiber zu installieren/aktualisieren. Eine ausführliche Anleitung finden Sie [hier](https://docs.nvidia.com/datacenter/tesla/tesla-installation-notes/index.html#ubuntu-lts).
2. [OPTIONAL:] Sie können auch Ihre CUDA-Treiber aktualisieren (aus dem obigen Repository).
3. Installieren Sie die Fabric Manager-Treiber von NVIDIA:

    ```
    sudo apt-get install cuda-drivers-460
    sudo apt-get install cuda-drivers-fabricmanager-460
    ```

4. Starten Sie Ihre VM neu (um die Treiber einsatzbereit zu machen).
5. Aktivieren und starten Sie den neu installierten NVIDIA Fabric Manager-Dienst:

    ```
    sudo systemctl enable nvidia-fabricmanager
    sudo systemctl start nvidia-fabricmanager
    ```

Sie können nun überprüfen, ob Treiber und GPU funktionieren, indem Sie Folgendes ausführen:
```
systemctl status nvidia-fabricmanager.service
``` 

Danach sollte angezeigt werden, dass der Fabric Manager-Dienst ![nvidia-fabric-manager-status](./media/nvidia-fabricmanager-status-ok-marked.png) ausführt.


### <a name="connection-to-desktop-environment-fails"></a>Fehler beim Herstellen einer Verbindung mit der Desktopumgebung

Wenn Sie eine Verbindung mit der DSVM zwar über das SSH-Terminal herstellen können, aber nicht über x2go, haben Sie in x2go möglicherweise den falschen Sitzungstyp festgelegt.
Um eine Verbindung mit der Desktopumgebung der DSVM herzustellen, muss der Sitzungstyp in *x2go/session preferences/session* auf *XFCE* festgelegt sein. Andere Desktopumgebungen werden derzeit nicht unterstützt.

### <a name="fonts-look-wrong-when-connecting-to-dsvm-using-x2go"></a>Falsche Darstellung von Schriftarten bei einer Verbindung mit der DSVM über x2go

Wenn Sie eine Verbindung mit x2go herstellen können, aber einige Schriftarten falsch dargestellt werden, kann dies mit einer Sitzungseinstellung in x2go zusammenhängen. Deaktivieren Sie vor dem Herstellen einer Verbindung mit der DSVM im Dialogfeld für die Sitzungseinstellungen auf der Registerkarte „Input/Output“ (Ein-/Ausgabe) das Kontrollkästchen „Set display DPI“ (Anzeige-DPI festlegen).

### <a name="prompted-for-unknown-password"></a>Aufforderung zur Eingabe eines unbekannten Kennworts

Wenn Sie eine DSVM-Einstellung *Authentifizierungstyp* erstellen und auf *Öffentlichen SSH-Schlüssel* festlegen (der gegenüber der Kennwortauthentifizierung empfohlen wird), wird Ihnen kein Kennwort zugewiesen. In einigen Szenarien werden Sie von einigen Anwendungen jedoch trotzdem nach einem Kennwort gefragt. Führen Sie `sudo passwd <user_name>` aus, um ein neues Kennwort für einen bestimmten Benutzer zu erstellen. Mit `sudo passwd` können Sie ein neues Kennwort für den Stammbenutzer erstellen.

Wenn Sie diese Befehle ausführen, wird die SSH-Konfiguration nicht geändert, die zulässigen Anmeldemechanismen bleiben unverändert. 

### <a name="prompted-for-password-when-running-sudo-command"></a>Aufforderung zur Eingabe eines Kennworts beim Ausführen eines sudo-Befehls

Wenn Sie einen `sudo`-Befehl auf einem Ubuntu-Computer ausführen, werden Sie möglicherweise immer wieder aufgefordert, Ihr Kennwort einzugeben, um zu bestätigen, dass Sie tatsächlich der angemeldete Benutzer sind. Dieses Verhalten ist zu erwarten und entspricht dem Standard unter Ubuntu. In einigen Szenarien ist eine wiederholte Authentifizierung jedoch nicht erforderlich, sondern eher störend.

Um die erneute Authentifizierung in den meisten Situationen zu deaktivieren, können Sie den folgenden Befehl in einem Terminal ausführen.

 `echo -e "\n$USER ALL=(ALL) NOPASSWD: ALL\n" | sudo tee -a /etc/sudoers`

Nach dem Neustart des Terminals wird für sudo keine weitere Anmeldung angefordert und stattdessen die Authentifizierung bei Ihrer Sitzungsanmeldung als ausreichend betrachtet.

### <a name="cannot-use-docker-as-non-root-user"></a>Verwendung von Docker nur als Stammbenutzer möglich

Um Docker als Nicht-Stammbenutzer verwenden zu können, muss Ihr Benutzer Mitglied der Gruppe „docker“ sein. Mit dem Befehl `getent group docker` können Sie überprüfen, welche Benutzer zu dieser Gruppe gehören. Um Ihren Benutzer der Gruppe „docker“ hinzuzufügen, führen Sie den Befehl `sudo usermod -aG docker $USER` aus.

### <a name="docker-containers-cannot-interact-with-the-outside-via-network"></a>Keine externe Kommunikation von Docker-Containern über das Netzwerk möglich

Standardmäßig fügt Docker neue Container dem sogenannten „Bridgenetzwerk“ `172.17.0.0/16` hinzu. Wenn sich das Subnetz dieses Bridgenetzwerks mit dem Subnetz Ihrer DSVM oder mit einem anderen privaten Subnetz in Ihrem Abonnement überschneidet, ist keine Netzwerkkommunikation zwischen dem Host und dem Container möglich. In diesem Fall können im Container ausgeführte Webanwendungen nicht erreicht werden, und der Container kann keine Pakete über apt aktualisieren.

Um das Problem zu beheben, müssen Sie Docker so neu konfigurieren, dass für das Bridgenetzwerk ein IP-Adressraum verwendet wird, der sich nicht mit anderen Netzwerken Ihres Abonnements überschneidet. Durch Hinzufügen von z. B.

```json
"default-address-pools": [
        {
            "base": "10.255.248.0/21",
            "size": 21
        }
    ]
```

zum JSON-Dokument, das in der Datei `/etc/docker/daemon.json` enthalten ist, weist Docker dem Bridgenetzwerk ein anderes Subnetz zu. (Die Datei muss mithilfe von sudo bearbeitet werden, z. B. durch Ausführen von `sudo nano /etc/docker/daemon.json`.)

Nach der Änderung muss der Docker-Dienst durch Ausführen von `service docker restart` neu gestartet werden.

Um zu überprüfen, ob die Änderungen angewandt wurden, können Sie `docker network inspect bridge` ausführen. Der Wert unter *IPAM.Config.Subnet* sollte dem oben angegebenen Adresspool entsprechen.

### <a name="gpus-not-available-in-docker-container"></a>GPU(s) im Docker-Container nicht verfügbar

Der auf der DSVM installierte Docker-Dienst unterstützt standardmäßig GPUs. Es müssen jedoch einige Voraussetzungen erfüllt sein.

* Zunächst muss die VM-Größe der DSVM natürlich mindestens eine GPU enthalten.
* Wenn Sie Ihren Docker-Container mit `docker run` starten, müssen Sie einen Parameter *--gpus* hinzufügen, z. B. `--gpus all`.
* Für VM-Größen, die NVIDIA A100-GPUs enthalten, müssen zusätzliche Softwarepakete installiert werden, insbesondere der [NVIDIA Fabric Manager](https://docs.nvidia.com/datacenter/tesla/pdf/fabric-manager-user-guide.pdf). Diese Pakete sind möglicherweise nicht in Ihrem Image vorinstalliert.


## <a name="windows"></a>Windows

### <a name="accessing-sql-server"></a>Zugreifen auf SQL Server

Wenn Sie versuchen, eine Verbindung mit der vorinstallierten SQL Server-Instanz herzustellen, tritt möglicherweise der Fehler „Fehler bei der Anmeldung“ auf. Um erfolgreich eine Verbindung mit der SQL Server-Instanz herzustellen, müssen Sie das Programm, mit dem Sie eine Verbindung herstellen, z. B. SQL Server Management Studio (SSMS), im Administratormodus ausführen. Der Administratormodus ist erforderlich, da gemäß DSVM-Standard nur Administratoren eine Verbindung herstellen dürfen.

### <a name="hyper-v-does-not-work"></a>Hyper-V funktioniert nicht

Dass Hyper-V unter Windows zunächst nicht funktioniert, ist ein erwartetes Verhalten. Für die Startleistung haben wir einige Dienste deaktiviert.
So aktivieren Sie Hyper-V

1. Öffnen Sie die Suchleiste auf Ihrer Windows-DSVM.
1. Geben Sie „Dienste“ ein.
1. Legen Sie alle Hyper-V-Dienste auf „Manuell“ fest.
1. Legen Sie „Verwaltung virtueller Hyper-V-Computer“ auf „Automatisch“ fest.

Ihr Bildschirm sollte dann wie folgt aussehen:

   

![Aktivieren von Hyper-V](./media/workaround/hyperv-enable-dsvm.png)
