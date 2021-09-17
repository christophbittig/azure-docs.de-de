---
title: Verwenden von xrdp mit Linux
description: Erfahren Sie, wie Sie Remotedesktop (XRDP) zum Herstellen einer Verbindung mit einem virtuellen Linux-Computer in Azure mithilfe von Tools mit grafischer Benutzeroberfläche installieren und konfigurieren.
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 03/03/2021
ms.author: cynthn
ms.openlocfilehash: d6dd3fd6e4f4196c49fc6431218448e632c9e8b4
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122698873"
---
# <a name="install-and-configure-xrdp-to-use-remote-desktop-with-ubuntu"></a>Installieren und Konfigurieren von xrdp für die Verwendung von Remotedesktop mit Ubuntu

**Gilt für**: :heavy_check_mark: Linux-VMs :heavy_check_mark: Flexible Skalierungsgruppen 

Virtuelle Linux-Computer (Linux-VMs) in Azure werden normalerweise von der Befehlszeile aus mithilfe einer SSH-Verbindung (Secure Shell) verwaltet. Wenn Sie erst in Linux einsteigen oder schnell eine Fehlerbehandlung durchführen müssen, ist die Verwendung von Remotedesktop unter Umständen einfacher. Dieser Artikel erläutert im Detail die Installation und Konfiguration von Desktopumgebung ([xfce](https://www.xfce.org)) und Remotedesktop ([xrdp](http://xrdp.org)) für Ihre Linux-VM unter Ubuntu.

Für den Artikel wurde eine VM mit Ubuntu 18.04 verfasst und getestet. 

## <a name="prerequisites"></a>Voraussetzungen

Für diesen Artikel ist ein vorhandener virtueller Ubuntu 18.04 LTS-Computer (VM) in Azure erforderlich. Wenn Sie eine VM erstellen müssen, verwenden Sie eine der folgenden Methoden:

- Die [Azure CLI](quick-create-cli.md)
- Das [Azure-Portal](quick-create-portal.md)


## <a name="install-a-desktop-environment-on-your-linux-vm"></a>Installieren Sie eine Desktopumgebung auf Ihrem virtuellen Linux-Computer

Auf den meisten virtuellen Linux-Computern in Azure ist standardmäßig keine Desktopumgebung installiert. Virtuelle Linux-Computer werden üblicherweise mithilfe von SSH-Verbindungen anstelle einer Desktopumgebung verwaltet. Unter Linux stehen verschiedene Desktopumgebungen zur Wahl. Je nach der gewählten Desktopumgebung werden 1 bis 2 GB Speicherplatz auf dem Datenträger und 5 bis 10 Minuten zum Installieren und Konfigurieren aller erforderlichen Pakete benötigt.

Im folgenden Beispiel wird die schlanke [XFCE4](https://www.xfce.org/)-Desktopumgebung auf einem virtuellen Ubuntu 18.04 LTS-Computer (VM) installiert. Die Befehle für andere Distributionen weichen geringfügig ab (verwenden Sie beispielsweise `yum`, um unter Red Hat Enterprise Linux zu installieren und passende `selinux`-Regeln zu konfigurieren, oder verwenden Sie `zypper` für die Installation unter SUSE).

Stellen Sie zuerst eine SSH-Verbindung mit Ihrer VM her. Im folgenden Beispiel wird mit dem Benutzernamen *azureuser* eine Verbindung mit dem virtuellen Computer namens *myvm.westus.cloudapp.azure.com* hergestellt. Verwenden Sie Ihre eigenen Werte:

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Wenn Sie Windows verwenden und weitere Informationen zur Verwendung von SSH benötigen, lesen Sie [Verwenden von SSH-Schlüsseln mit Windows](ssh-from-windows.md).

Installieren Sie anschließend XFCE mithilfe von `apt`, wie hier dargestellt:

```bash
sudo apt-get update
sudo apt-get -y install xfce4
sudo apt install xfce4-session
```

## <a name="install-and-configure-a-remote-desktop-server"></a>Installieren und Konfigurieren eines Remotedesktopservers
Da Sie jetzt über eine installierte Remotedesktopumgebung verfügen, konfigurieren Sie einen Remotedesktopdienst, um nach eingehenden Verbindungen zu lauschen. [XRDP](http://xrdp.org) ist ein Open Source-RDP-Server (Remote Desktop Protocol), der in den meisten Linux-Distributionen verfügbar ist und gut mit XFCE funktioniert. Installieren Sie XRDP wie folgt auf Ihrem virtuellen Ubuntu-Computer:

```bash
sudo apt-get -y install xrdp
sudo systemctl enable xrdp
```

Teilen Sie XRDP beim Starten der Sitzung die verwendete Desktopumgebung mit. Konfigurieren Sie XRDP in folgender Weise für die Verwendung von XFCE als Ihrer Desktopumgebung:

```bash
echo xfce4-session >~/.xsession
```

Starten Sie den XRDP-Dienst wie folgt neu, damit die Änderungen wirksam werden:

```bash
sudo service xrdp restart
```


## <a name="set-a-local-user-account-password"></a>Festlegen eines Kennworts für das lokale Benutzerkonto
Wenn Sie beim Erstellen des virtuellen Computers ein Kennwort für Ihr Benutzerkonto erstellt haben, überspringen Sie diesen Schritt. Wenn Sie nur Authentifizierung per SSH-Schlüssel verwenden und kein Kennwort für das lokale Konto festgelegt ist, geben Sie ein Kennwort an, bevor Sie XRDP für die Anmeldung bei Ihrem virtuellen Computer verwenden. XRDP kann keine SSH-Schlüssel zur Authentifizierung annehmen. Im folgenden Beispiel wird ein Kennwort für das Benutzerkonto *azureuser* angegeben:

```bash
sudo passwd azureuser
```

> [!NOTE]
> Das Festlegen eines Kennworts bewirkt keine Aktualisierung Ihrer SSHD-Konfiguration für die Zulassung von Kennwortanmeldungen, wenn diese aktuell nicht zugelassen sind. Unter Sicherheitsgesichtspunkten kann es sinnvoll sein, Verbindungen mit Ihrem virtuellen Computer mithilfe eines SSH-Tunnels mit schlüsselbasierter Authentifizierung und dann erst die Verbindung mit XRDP herzustellen. In diesem Fall können Sie den folgenden Schritt zum Erstellen einer Netzwerksicherheitsgruppen-Regel zum Zulassen von Remotedesktopverkehr überspringen.


## <a name="create-a-network-security-group-rule-for-remote-desktop-traffic"></a>Erstellen einer Netzwerksicherheitsgruppen-Regel für den Remotedesktop-Datenverkehr
Um zuzulassen, dass Remotedesktop-Datenverkehr Ihren virtuellen Linux-Computer erreicht, muss eine Netzwerksicherheitsgruppen-Regel erstellt werden, die TCP an Port 3389 an Ihren virtuellen Computer durchlässt. Weitere Informationen zu Netzwerksicherheitsgruppen-Regeln finden Sie unter [Was ist eine Netzwerksicherheitsgruppe?](../../virtual-network/network-security-groups-overview.md). Alternativ können Sie [das Azure-Portal zum Erstellen einer Netzwerksicherheitsgruppen-Regel verwenden](../windows/nsg-quickstart-portal.md).

Im folgenden Beispiel wird eine Netzwerksicherheitsgruppen-Regel mit [az vm open-port](/cli/azure/vm#az_vm_open_port) für Port *3389* erstellt. Öffnen Sie über die Azure CLI – nicht die SSH-Sitzung mit Ihrem virtuellen Computer – die folgende Netzwerksicherheitsgruppen-Regel:

```azurecli
az vm open-port --resource-group myResourceGroup --name myVM --port 3389
```


## <a name="connect-your-linux-vm-with-a-remote-desktop-client"></a>Herstellen einer Verbindung mit dem virtuellen Linux-Computer mithilfe eines Remotedesktopclients

Öffnen Sie Ihren lokalen Remotedesktopclient, und stellen Sie eine Verbindung mit der IP-Adresse oder dem DNS-Namen Ihres virtuellen Linux-Computers her. 

:::image type="content" source="media/use-remote-desktop/remote-desktop.png" alt-text="Screenshot: Remotedesktopclient":::

Geben Sie den Benutzernamen und das Kennwort des Benutzerkontos für Ihren virtuellen Computer wie folgt ein:

:::image type="content" source="media/use-remote-desktop/xrdp-login.png" alt-text="Screenshot: xrdp-Anmeldebildschirm":::

Nach der Authentifizierung wird die XFCE-Desktopumgebung geladen. Sie sieht ähnlich wie das folgende Beispiel aus:

![XFCE-Desktopverbindung über XRDP](./media/use-remote-desktop/xfce-desktop-environment.png)

Wenn Ihr lokaler RDP-Client die Authentifizierung auf Netzwerkebene (NLA) verwendet, müssen Sie diese Verbindungseinstellung deaktivieren. NLA wird derzeit nicht von XRDP unterstützt. Sie können auch alternative RDP-Lösungen verwenden, die NLA unterstützen, wie z.B. [FreeRDP](https://www.freerdp.com).


## <a name="troubleshoot"></a>Problembehandlung
Wenn Sie keine Verbindung mit Ihrem virtuellen Linux-Computer mithilfe eines Remotedesktopclients herstellen können, überprüfen Sie unter Verwendung von `netstat` auf dem virtuellen Linux-Computer, ob der virtuelle Computer nach RDP-Verbindungen lauscht, wie hier dargestellt:

```bash
sudo netstat -plnt | grep rdp
```

Das folgende Beispiel zeigt, dass der virtuelle Computer erwartungsgemäß an TCP-Port 3389 lauscht:

```bash
tcp     0     0      127.0.0.1:3350     0.0.0.0:*     LISTEN     53192/xrdp-sesman
tcp     0     0      0.0.0.0:3389       0.0.0.0:*     LISTEN     53188/xrdp
```

Wenn der Dienst *xrdp-sesman* nicht lauscht, starten Sie diesen auf einem virtuellen Ubuntu-Computer in folgender Weise neu:

```bash
sudo service xrdp restart
```

Gehen Sie die Protokolle in */var/log* auf Ihrem virtuellen Ubuntu-Computer auf Hinweise durch, warum der Dienst nicht reagiert. Sie können auch während eines Verbindungsversuchs per Remotedesktop das syslog-Protokoll überwachen, um eventuelle Fehler anzuzeigen:

```bash
tail -f /var/log/syslog
```

Andere Linux-Distributionen, wie etwa Red Hat Enterprise Linux und SUSE, verwenden möglicherweise andere Verfahren zum erneuten Starten von Diensten sowie andere Speicherorte für die zu überprüfenden Protokolldateien.

Wenn Sie in Ihrem Remotedesktopclient keine Antwort empfangen und keine Ereignisse im Systemprotokoll finden, zeigt dieses Verhalten an, dass der Remotedesktop-Datenverkehr den virtuellen Computer nicht erreicht. Überprüfen Sie die Regeln Ihrer Netzwerksicherheitsgruppe, um sicherzustellen, dass eine Regel in Kraft ist, die TCP an Port 3389 zulässt. Weitere Informationen finden Sie unter [Behandlung von Konnektivitätsproblemen mit Anwendungen](/troubleshoot/azure/virtual-machines/troubleshoot-app-connection).


## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zum Erstellen und Verwenden von SSH-Schlüsseln mit virtuellen Linux-Computern finden Sie unter [Erstellen von SSH-Schlüsseln für virtuelle Linux-Computer in Azure](mac-create-ssh-keys.md).

Informationen zum Verwenden von SSH unter Windows finden Sie unter [Verwenden von SSH-Schlüsseln mit Windows](ssh-from-windows.md).
