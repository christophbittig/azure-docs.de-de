---
title: Aktivieren des grafischen Remotedesktops für Linux in Azure Lab Services | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie Remotedesktop für virtuelle Linux-Computer in einem Lab in Azure Lab Services aktivieren.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: aaeec2cc74e24061c9c022fdc4526732030c1f97
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130232677"
---
# <a name="enable-graphical-remote-desktop-for-linux-virtual-machines-in-azure-lab-services"></a>Aktivieren des grafischen Remotedesktops für Linux-VMs in Azure Lab Services
In diesem Artikel wird gezeigt, wie Sie Folgendes durchführen:

- Aktivieren von grafischen Remotedesktopsitzungen für eine Linux-VM
- Herstellen einer Verbindung mit einer Linux-VM mit dem RDP (Remotedesktopprotokoll) oder mit X2Go-Remotedesktopclients

## <a name="set-up-graphical-remote-desktop-solution"></a>Einrichten einer grafischen Remotedesktoplösung
Wenn ein Lab aus einem **Linux**-Image erstellt wird, wird der **SSH**-Zugriff (Secure Shell) automatisch so konfiguriert, dass der Kursleiter über die Befehlszeile mit SSH eine Verbindung zur Vorlagen-VM herstellen kann.  Ebenso können Kursteilnehmer per SSH eine Verbindung mit ihren VMs herstellen, wenn die Vorlagen-VM veröffentlicht wurde.

Zum Herstellen einer Verbindung mit einer Linux-VM mithilfe einer **GUI** (grafische Benutzeroberfläche) wird entweder **RDP** oder **X2Go** empfohlen.  Im weiteren Verlauf dieses Artikels werden die Schritte zum Einrichten von RDP oder X2Go auf der Vorlagen-VM eines Labs erläutert.

> [!NOTE]
> Linux verwendet eine Open-Source-Version von RDP mit dem Namen [Xrdp](https://en.wikipedia.org/wiki/Xrdp).  Der Einfachheit halber verwenden wir den Begriff RDP in diesem Artikel.

### <a name="rdp-setup"></a>RDP-Setup
Für die Verwendung von RDP muss der Kursleiter:
  - die Remotedesktopverbindung aktivieren (dies ist spezifisch zum Öffnen des Ports der VM für RDP erforderlich).
  - den RDP-Remotedesktopserver installieren.
  - Installieren einer grafischen Linux-Desktopumgebung

### <a name="x2go-setup"></a>X2Go-Setup
Für die Verwendung von X2Go muss der Kursleiter:
- den X2Go-Remotedesktopserver installieren.
- Installieren einer grafischen Linux-Desktopumgebung

X2Go nutzt denselben Port, der bereits für SSH aktiviert ist.  Daher ist keine weitere Konfiguration erforderlich, um einen Port der VM für X2Go zu öffnen.

> [!NOTE]
> In einigen Fällen, z. B. bei Ubuntu LTS 18.04, bietet X2Go bessere Leistung.  Wenn Sie RDP verwenden und Verzögerungen bei der Interaktion mit der grafischen Desktopumgebung bemerken, sollten Sie X2Go testen, da Sie möglicherweise eine bessere Leistung erzielen.

> [!IMPORTANT]
>  Auf einigen Marketplace-Images sind bereits eine grafische Desktopumgebung und ein Remotedesktopserver installiert.  Beispielsweise ist auf dem Produkt [Data Science Virtual Machine for Linux (Ubuntu)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804) bereits [XFCE und X2Go-Server installiert und zum Akzeptieren von Clientverbindungen konfiguriert](../machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro.md#x2go).

## <a name="enable-remote-desktop-connection-for-rdp"></a>Aktivieren der Remotedesktopverbindung für RDP

Dieser Schritt ist nur zum Herstellen einer Verbindung mit dem RDP erforderlich.  Wenn Sie stattdessen planen, X2Go zu verwenden, können Sie mit dem nächsten Abschnitt fortfahren, da X2Go den SSH-Port verwendet.

1.  Während das Lab erstellt wird, hat der Kursleiter die Option, die **Remotedesktopverbindung zu aktivieren**.  Der Kursleiter muss diese Option **aktivieren**, um den Port der Linux-VM zu öffnen, der für RDP-Remotedesktopsitzungen erforderlich ist.  Wenn diese Option **deaktiviert** bleibt, ist nur der Port für SSH geöffnet.
  
    ![Screenshot: Fenster „Neues Lab“ mit der Option „Remotedesktopverbindung aktivieren“](./media/how-to-enable-remote-desktop-linux/enable-rdp-option.png)

2. Wählen Sie im Meldungsfeld **Enabling Remote Desktop Connection** (Aktivieren der Remotedesktopverbindung) die Option **Continue with Remote Desktop** (Weiter mit Remotedesktop) aus. 

    ![Aktivieren der Remotedesktopverbindung für ein Linux-Image](./media/how-to-enable-remote-desktop-linux/enabling-remote-desktop-connection-dialog.png)

## <a name="install-rdp-or-x2go"></a>Installieren von RDP oder X2Go

Nachdem das Lab erstellt wurde, muss der Kursleiter sicherstellen, dass eine grafische Desktopumgebung und ein Remotedesktopserver auf der Vorlagen-VM aktiviert sind.  Kursleiter müssen zunächst eine Verbindung mit der Vorlagen-VM per SSH herstellen, um die folgenden Pakete zu installieren:
- Entweder den RDP- oder X2Go-Remotedesktopserver
- Eine grafische Desktopumgebung wie zum Beispiel [GNOME](https://www.gnome.org/), [MATE](https://mate-desktop.org/), [XFCE](https://www.xfce.org/) und [Xubuntu](https://xubuntu.org/)

Sobald dies eingerichtet ist, kann der Kursleiter über den **Microsoft-Remotedesktopclient (RDP)** oder den **X2Go-Client** eine Verbindung mit der Vorlagen-VM herstellen.

Führen Sie die folgenden Schritte aus, um die Vorlagen-VM einzurichten:

1. Wird **Customize template** (Vorlage anpassen) auf der Symbolleiste angezeigt, wählen Sie die Option aus. Wählen Sie anschließend im Dialogfeld **Customize template** (Vorlage anpassen) die Option **Weiter** aus. Durch diese Aktion wird die Vorlage für virtuelle Computer gestartet.  

    ![Anpassen der Vorlage](./media/how-to-enable-remote-desktop-linux/customize-template.png)
1. Nach dem Starten der Vorlage für virtuelle Computer können Sie **Connect template** (Vorlage verbinden) und dann auf der Symbolleiste **Connect via SSH** (Über SSH verbinden) auswählen. 

    ![Herstellen einer Verbindung mit der Vorlage per RDP nach der Lab-Erstellung](./media/how-to-enable-remote-desktop-linux/rdp-after-lab-creation.png) 
1. Das Dialogfeld **Eine Verbindung zu Ihrem virtuellen Computer herstellen** wird angezeigt. Wählen Sie neben dem Textfeld die Schaltfläche **Kopieren** aus, um den Wert in die Zwischenablage zu kopieren. Speichern Sie die SSH-Verbindungsinformationen. Verwenden Sie diese Verbindungsinformationen über ein SSH-Terminal (z. B. [Putty](https://www.putty.org/)), um eine Verbindung mit der VM herzustellen.
 
    ![SSH-Verbindungszeichenfolge](./media/how-to-enable-remote-desktop-linux/ssh-connection-string.png)

1. Installieren Sie schließlich RDP oder X2Go sowie eine grafische Desktopumgebung Ihrer Wahl.

  Für eine optimale Leistung empfehlen wir in der Regel die Verwendung des grafischen XFCE-Desktops und das Herstellen einer Verbindung mit dem Desktop mithilfe von X2Go.  Um XFCE mit X2Go unter Ubuntu einzurichten, befolgen Sie die folgenden Anweisungen:
    - [Installieren und Konfigurieren von X2Go](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/LinuxGraphicalDesktopSetup/XFCE_Xubuntu/ReadMe.md)

  In Fällen, in denen Benutzer*innen stattdessen über RDP eine Verbindung mit dem grafischen Desktop herstellen müssen, befolgen Sie die folgenden Anweisungen für Ubuntu:
    - [Installieren und Konfigurieren des RDPs](../virtual-machines/linux/use-remote-desktop.md)

### <a name="using-gnome-or-mate-graphical-desktops"></a>Verwenden von grafischen GNOME- oder MATE-Desktops

Für die grafischen Desktopumgebungen GNOME oder MATE kann es zu einem Netzwerkkonflikt mit dem Azure Linux-Agent kommen, der erforderlich ist, damit die virtuellen Computer in Azure Lab Services ordnungsgemäß funktionieren.  Dieser Netzwerkkonflikt verursacht die folgenden Nebeneffekte, wenn Ubuntu 18.04 LTS mit einer GNOME- oder MATE-Installation verwendet wird:
 - Bei der Lab-Erstellung mithilfe des Images tritt die Fehlermeldung **Communication could not be established with the VM agent.  Please verify that the VM agent is enabled and functioning.** (Kommunikation mit dem VM-Agent konnte nicht hergestellt werden. Überprüfen Sie, ob der VM-Agent aktiviert ist und funktioniert.) auf.  
 - Das Veröffentlichen von Kursteilnehmer-VMs reagiert nicht mehr, wenn die Einstellungen für automatisches Herunterfahren aktiviert sind.
 - Das Zurücksetzen des Kursteilnehmer-VM-Kennworts reagiert nicht mehr.

Es wird empfohlen, die folgenden Anweisungen zum Einrichten der grafischen GNOME- oder MATE-Desktops unter Ubuntu zu verwenden.  Diese Anweisungen enthalten einen Fix für den Netzwerkkonflikt, der bei Ubuntu 18.04 LTS besteht.  Außerdem wird die Installation von GNOME und UBUNTU unter Ubuntu 20.04 LTS und 21.04 LTS unterstützt:
 - [Installieren und Konfigurieren von GNOME/RDP und MATE/X2go](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/LinuxGraphicalDesktopSetup/GNOME_MATE/ReadMe.md)

## <a name="connect-to-the-template-vm-via-the-gui"></a>Herstellen einer Verbindung mit der Vorlagen-VM über die GUI

Sobald die Vorlagen-VM eingerichtet ist, kann der Kursleiter über die GUI mit dem **Microsoft-Remotedesktopclient (RDP)** oder dem **X2Go-Client** eine Verbindung mit der Vorlagen-VM herstellen.  Welchen Client Sie hierfür verwenden, hängt davon ab, ob RDP oder X2Go als Remotedesktopserver für die Vorlagen-VM konfiguriert ist.  

### <a name="microsoft-remote-desktop-rdp-client"></a>Microsoft-Remotedesktopclient (RDP)

Der Microsoft-Remotedesktopclient wird zum Herstellen einer Verbindung mit einer Vorlagen-VM verwendet, für die das RDP konfiguriert wurde.  Der Remotedesktopclient kann unter Windows, auf Chromebooks, auf Macs und weiteren Geräten verwendet werden.  Ausführliche Informationen finden Sie im Artikel zu [Remotedesktopclients](/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients).

Führen Sie die Schritte für den jeweiligen Computer aus, den Sie zum Herstellen einer Verbindung mit der Vorlagen-VM verwendet haben:

- Windows
  1. Klicken Sie in der Symbolleiste Ihres Labs auf **Mit Vorlage verbinden**, und wählen Sie dann **Über RDP verbinden** aus, um eine Verbindung mit der Vorlagen-VM herzustellen. 
  1. Speichern Sie die RDP-Datei, und verwenden Sie sie, um mithilfe des Remotedesktopclients eine Verbindung mit der Vorlagen-VM herzustellen. 
  1. Der Remotedesktopclient ist üblicherweise bereits unter Windows installiert und konfiguriert.  Daher müssen Sie lediglich auf die RDP-Datei klicken, um die Remotesitzung zu öffnen und zu starten.

- Mac
  1. Klicken Sie in der Symbolleiste Ihres Labs auf **Mit Vorlage verbinden**, und wählen Sie dann **Über RDP verbinden** aus, um die RDP-Datei zu speichern.  
  1. Führen Sie dann die Schritte im Artikel [Herstellen einer Verbindung mit einer VM mithilfe des RDPs auf einem Mac](connect-virtual-machine-mac-remote-desktop.md) aus.

- Chromebook
  1. Klicken Sie in der Symbolleiste Ihres Labs auf **Mit Vorlage verbinden**, und wählen Sie dann **Über RDP verbinden** aus, um die RDP-Datei zu speichern.  
  1. Führen Sie dann die Schritte im Artikel [Herstellen einer Verbindung mit einer VM mithilfe des RDPs auf einem Chromebook](connect-virtual-machine-chromebook-remote-desktop.md).

### <a name="x2go-client"></a>X2Go-Client

Der X2Go-Client wird zum Herstellen einer Verbindung mit einer Vorlagen-VM verwendet, für die X2Go konfiguriert ist.  Führen Sie die Schritte im Artikel [Herstellen einer Verbindung mit einer VM mithilfe von X2Go](how-to-use-remote-desktop-linux-student.md#connect-to-the-student-vm-using-x2go) mit den SSH-Verbindungsinformationen der Vorlagen-VM aus.

## <a name="next-steps"></a>Nächste Schritte
Nachdem ein Kursleiter das RDP oder X2Go auf seiner Vorlagen-VM eingerichtet hat und diese veröffentlicht, können Kursteilnehmer über die Remotedesktop-GUI oder über SSH eine Verbindung mit den VMs herstellen.

Weitere Informationen finden Sie unter
 - [Herstellen einer Verbindung mit einer Linux-VM](how-to-use-remote-desktop-linux-student.md)
