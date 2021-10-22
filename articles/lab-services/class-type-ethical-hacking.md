---
title: Einrichten eines Labs für ethisches Hacken mit Azure Lab Services | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mithilfe von Azure Lab Services ein Lab einrichten, um ethisches Hacken zu vermitteln.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 649646e560906c499cacda13c7745be3f2f3f758
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/19/2021
ms.locfileid: "130176907"
---
# <a name="set-up-a-lab-to-teach-ethical-hacking-class"></a>Einrichten eines Labs zum Vermitteln von Kursen für ethisches Hacken

In diesem Artikel erfahren Sie, wie Sie einen Kurs einrichten, der sich auf die forensische Seite des ethischen Hackens konzentriert. Bei Penetrationstests, eine von der Community für ethisches Hacken verwendete Vorgehensweise, versucht eine Person, auf das System oder Netzwerk zuzugreifen, um Schwachstellen zu demonstrieren, die ein böswilliger Angreifer ausnutzen könnte.

In einem Kurs für ethisches Hacken erlernen die Kursteilnehmer moderne Techniken zum Schützen vor Schwachstellen kennen. Jeder Kursteilnehmer erhält einen virtuellen Windows Server-Hostcomputer mit zwei geschachtelten virtuellen Computern: einem virtuellen Computer mit einem [Metaspoiltable3](https://github.com/rapid7/metasploitable3)-Image und einem anderen Computer mit einem [Kali Linux](https://www.kali.org/)-Image. Der virtuelle Metasploitable-Computer wird angegriffen, und der virtuelle Kali-Computer stellt die Tools zur Verfügung, die zum Ausführen forensischer Aufgaben erforderlich sind.

Dieser Artikel besteht aus zwei Hauptteilen. Im ersten Abschnitt wird erläutert, wie das Kurs-Lab erstellt wird. Im zweiten Abschnitt wird erläutert, wie der Vorlagencomputer mit aktivierter geschachtelter Virtualisierung sowie mit den benötigten Tools und Images erstellt wird. In diesem Fall ein Metasploitable-Image und ein Kali Linux-Image auf einem Computer, auf dem Hyper-V zum Hosten der Images aktiviert ist.

## <a name="lab-configuration"></a>Labkonfiguration

Zum Einrichten dieses Labs benötigen Sie als Einstieg ein Azure-Abonnement. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen. Sobald Sie ein Azure-Abonnement erhalten, können Sie entweder ein neues Lab-Konto in Azure Lab Services erstellen oder ein vorhandenes Konto verwenden. Sehen Sie sich das folgende Tutorial zum Erstellen eines neuen Lab-Kontos an: [Tutorial zum Einrichten eines Lab-Kontos](tutorial-setup-lab-account.md).

Absolvieren Sie [dieses Tutorial](tutorial-setup-classroom-lab.md), um ein neues Lab mit den folgenden Einstellungen zu erstellen:

| Größe des virtuellen Computers | Image |
| -------------------- | ----- |
| Mittel (geschachtelte Virtualisierung) | Windows Server 2019 Datacenter |

## <a name="template-machine"></a>Vorlagencomputer

Nachdem der Vorlagencomputer erstellt wurde, starten Sie den Computer, und stellen Sie eine Verbindung mit ihm her, um die folgenden drei Hauptaufgaben auszuführen.

1. Richten Sie den Computer für geschachtelte Virtualisierung ein. Hierdurch werden alle geeigneten Windows-Funktionen wie Hyper-V aktiviert und das Netzwerke für die Hyper-V-Images eingerichtet, damit sie miteinander und mit dem Internet kommunizieren können.
2. Richten Sie das [Kali](https://www.kali.org/) Linux-Image ein. Kali ist eine Linux-Distribution, die Tools für Penetrationstests und Sicherheitsüberwachung umfasst.
3. Richten Sie das Metasploitable-Image ein. In diesem Beispiel wird das [Metasploitable3](https://github.com/rapid7/metasploitable3)-Image verwendet. Dieses Image wurde vorsätzlich so erstellt, dass es Schwachstellen enthält.

Im restlichen Teil dieses Artikels werden die manuellen Schritte zum Durchführen der obigen Aufgaben beschrieben.  Alternativ können Sie auch die [Hyper-V-Skripts von Lab Services](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/HyperV) und die [Skripts für ethisches Hacken von Lab Services](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/EthicalHacking) ausführen.

### <a name="prepare-template-machine-for-nested-virtualization"></a>Vorbereiten des Vorlagencomputers für geschachtelte Virtualisierung

Befolgen Sie die Anleitung zum [Aktivieren der geschachtelten Virtualisierung](how-to-enable-nested-virtualization-template-vm.md), um ihren virtuellen Vorlagencomputer für die geschachtelte Virtualisierung vorzubereiten.

### <a name="set-up-a-nested-virtual-machine-with-kali-linux-image"></a>Einrichten eines geschachtelten virtuellen Computers mit dem Kali Linux-Image

Kali ist eine Linux-Distribution, die Tools für Penetrationstests und Sicherheitsüberwachung umfasst.

1. Laden Sie das Image über die Seite mit den [Kali Linux-VM-Images von Offensive Security](https://www.offensive-security.com/kali-linux-vm-vmware-virtualbox-image-download/) herunter.  Merken Sie sich die Angaben zum Standardbenutzernamen und zum zugehörigen Kennwort auf der Downloadseite.
    1. Laden Sie das Image **Kali Linux VMware 64-Bit (7z)** für VMware herunter.
    1. Extrahieren Sie die .7z-Datei.  Wenn Sie 7 zip noch nicht besitzen, laden Sie es unter [https://www.7-zip.org/download.html](https://www.7-zip.org/download.html) herunter. Merken Sie sich den Speicherort des extrahierten Ordners, da Sie ihn später noch benötigen.
1. Konvertieren Sie die extrahierte VMDK-Datei in eine VHDX-Datei, damit Sie die VHDX-Datei mit Hyper-V verwenden können. Es stehen mehrere Tools zum Konvertieren von VMware-Images in Hyper-V-Images zur Verfügung.  Wir verwenden den [StarWind V2V Converter](https://www.starwindsoftware.com/starwind-v2v-converter).  Informationen zum Herunterladen finden Sie auf der [StarWind V2V Converter-Downloadseite](https://www.starwindsoftware.com/starwind-v2v-converter#download).
    1. Starten Sie **StarWind V2V Converter**.
    1. Wählen Sie auf der Seite **Select location of image to convert** (Speicherort des zu konvertierenden Images auswählen) **Local file** (Lokale Datei) aus.  Wählen Sie **Weiter** aus.
    1. Navigieren Sie auf der Seite **Source image** (Quellimage) zu der im vorherigen Schritt extrahierten Kali Linux-VMDK-Datei, und wählen Sie sie für die Einstellung **File name** (Dateiname) aus.  Die Datei hat das Format „Kali-Linux-{version}-vmware-amd64.vmdk“.  Wählen Sie **Weiter** aus.
    1. Wählen Sie auf der Seite **Select location of destination image** (Speicherort des Zielimages auswählen) **Local file** (Lokale Datei) aus.  Wählen Sie **Weiter** aus.
    1. Wählen Sie auf der Seite **Select destination image format** (Zielimageformat auswählen) **VHD/VHDX** aus.  Wählen Sie **Weiter** aus.
    1. Wählen Sie auf der Seite **Select option for VHD/VHDX image format** (Option für das VHD/VHDX-Imageformat) **VHDX growable image** (Wachstumsfähiges VHDX-Image) aus.  Wählen Sie **Weiter** aus.
    1. Übernehmen Sie auf der Seite **Select destination file name** (Zieldateiname auswählen) den Standarddateinamen.  Wählen Sie **Konvertieren** aus.
    1. Warten Sie auf der Seite **Converting** (Konvertieren) auf das Konvertieren des Images.  Dieser Vorgang kann einige Minuten dauern.  Wählen Sie **Finish** (Fertigstellen) aus, wenn die Konvertierung abgeschlossen ist.
1. Erstellen Sie einen neuen virtuellen Hyper-V-.Computer.
    1. Öffnen Sie den **Hyper-V-Manager**.
    1. Wählen Sie **Aktion** -> **Neu** -> **Virtueller Computer** aus.
    1. Wählen Sie auf der Seite **Vorbereitung** des **Assistenten für neue virtuelle Computer** die Option **Weiter** aus.
    1. Geben Sie auf der Seite **Namen und Speicherort angeben** unter **Name** den Namen **Kali-Linux** ein, und wählen Sie **Weiter** aus.
    1. Übernehmen Sie auf der Seite **Generation angeben** die Standardeinstellungen, und wählen Sie **Weiter** aus.
    1. Geben Sie auf der Seite **Arbeitsspeicher zuweisen** unter **Startspeicher** den Wert **2048 MB** ein, und wählen Sie **Weiter** aus.
    1. Lassen Sie auf der Seite **Netzwerk konfigurieren** die Verbindung als **Nicht verbunden**. Den Netzwerkadapter richten Sie später ein.
    1. Wählen Sie auf der Seite **Virtuelle Festplatte verbinden** den Eintrag **Vorhandene virtuelle Festplatte verwenden** aus. Navigieren Sie zum Speicherort der Datei **Kali-Linux-{version}-vmware-amd64.vmdk**, die Sie im vorherigen Schritt erstellt haben, und wählen Sie **Weiter** aus.
    1. Wählen Sie auf der Seite zum **Abschließen des Assistenten für neue virtuelle Computer** den Befehl **Fertig stellen** aus.
    1. Sobald der virtuelle Computer erstellt ist, wählen Sie ihn im Hyper-V-Manager aus. Schalten Sie den Computer noch nicht ein.  
    1. Wählen Sie **Aktion** -> **Einstellungen** aus.
    1. Wählen Sie im Dialogfeld **Einstellungen für Kali-Linux** die Option **Hardware hinzufügen** aus.
    1. Wählen Sie **Älterer Netzwerkadapter** aus, und wählen Sie **Hinzufügen** aus.
    1. Wählen Sie auf der Seite **Älterer Netzwerkadapter** den Eintrag **LabServicesSwitch** für die Einstellung **Virtueller Switch** aus, und wählen Sie **OK** aus. „LabServicesSwitch“ wurde während der Vorbereitung des Vorlagencomputers für Hyper-V im Abschnitt **Vorbereiten des Vorlagencomputers für geschachtelte Virtualisierung** erstellt.
    1. Das Kali-Linux-Image ist jetzt bereit für die Verwendung. Wählen Sie im **Hyper-V-Manager****Aktion** -> **Starten** und dann **Aktion** -> **Verbinden** aus, um eine Verbindung mit dem virtuellen Computer herzustellen.  Der Standardbenutzername lautet **kali**, und auch das Kennwort ist **kali**.

## <a name="set-up-a-nested-vm-with-metasploitable-image"></a>Einrichten eines geschachtelten virtuellen Computers mit dem Metasploitable-Image  

Das Rapid7 Metasploitable-Image ist ein Image, das absichtlich mit Schwachstellen konfiguriert ist. Sie verwenden dieses Image zum Testen und Ermitteln von Problemen. In den folgenden Anweisungen erfahren Sie, wie Sie ein vorgefertigtes Metasploitable-Image verwenden. Wenn jedoch eine neuere Version des Metasploitable-Images erforderlich ist, sehen Sie unter [https://github.com/rapid7/metasploitable3](https://github.com/rapid7/metasploitable3) nach.

1. Laden Sie das Metasploitable-Image herunter.
    1. Navigieren Sie zu [https://information.rapid7.com/download-metasploitable-2017.html](https://information.rapid7.com/download-metasploitable-2017.html). Füllen Sie das Formular zum Herunterladen des Images aus, und klicken Sie auf die Schaltfläche **Absenden**.
    2. Wählen Sie die Schaltfläche **Metasploitable jetzt herunterladen** aus.
    3. Wenn die ZIP-Datei heruntergeladen ist, extrahieren Sie sie, und merken Sie sich den Speicherort der Datei „Metasploitable.vmdk“.
1. Konvertieren Sie die extrahierte VMDK-Datei in eine VHDX-Datei, damit Sie die VHDX-Datei mit Hyper-V verwenden können. Es stehen mehrere Tools zum Konvertieren von VMware-Images in Hyper-V-Images zur Verfügung.  Wir verwenden wieder den [StarWind V2V Converter](https://www.starwindsoftware.com/starwind-v2v-converter).  Informationen zum Herunterladen finden Sie auf der [StarWind V2V Converter-Downloadseite](https://www.starwindsoftware.com/starwind-v2v-converter#download).
    1. Starten Sie **StarWind V2V Converter**.
    1. Wählen Sie auf der Seite **Select location of image to convert** (Speicherort des zu konvertierenden Images auswählen) **Local file** (Lokale Datei) aus.  Wählen Sie **Weiter** aus.
    1. Navigieren Sie auf der Seite **Source image** (Quellimage) zu der im vorherigen Schritt extrahierten Datei „Metasploitable.vmdk“, und wählen Sie sie für die Einstellung **File name** (Dateiname) aus.  Wählen Sie **Weiter** aus.
    1. Wählen Sie auf der Seite **Select location of destination image** (Speicherort des Zielimages auswählen) **Local file** (Lokale Datei) aus.  Wählen Sie **Weiter** aus.
    1. Wählen Sie auf der Seite **Select destination image format** (Zielimageformat auswählen) **VHD/VHDX** aus.  Wählen Sie **Weiter** aus.
    1. Wählen Sie auf der Seite **Select option for VHD/VHDX image format** (Option für das VHD/VHDX-Imageformat) **VHDX growable image** (Wachstumsfähiges VHDX-Image) aus.  Wählen Sie **Weiter** aus.
    1. Übernehmen Sie auf der Seite **Select destination file name** (Zieldateiname auswählen) den Standarddateinamen.  Wählen Sie **Konvertieren** aus.
    1. Warten Sie auf der Seite **Converting** (Konvertieren) auf das Konvertieren des Images.  Dieser Vorgang kann einige Minuten dauern.  Wählen Sie **Finish** (Fertigstellen) aus, wenn die Konvertierung abgeschlossen ist.
1. Erstellen Sie einen neuen virtuellen Hyper-V-.Computer.
    1. Öffnen Sie den **Hyper-V-Manager**.
    1. Wählen Sie **Aktion** -> **Neu** -> **Virtueller Computer** aus.
    1. Wählen Sie auf der Seite **Vorbereitung** des **Assistenten für neue virtuelle Computer** die Option **Weiter** aus.
    1. Geben Sie auf der Seite **Namen und Speicherort angeben** die **Metasploitable** als **Name** n ein, und wählen Sie **Weiter** aus.

        ![Assistent für neue VM-Images](./media/class-type-ethical-hacking/new-vm-wizard-1.png)
    1. Übernehmen Sie auf der Seite **Generation angeben** die Standardeinstellungen, und wählen Sie **Weiter** aus.
    1. Geben Sie auf der Seite **Arbeitsspeicher zuweisen** den Wert **512 MB** als **Startspeicher** ein, und wählen Sie **Weiter** aus.

        ![Seite „Speicher zuweisen“](./media/class-type-ethical-hacking/assign-memory-page.png)
    1. Lassen Sie auf der Seite **Netzwerk konfigurieren** die Verbindung als **Nicht verbunden**. Den Netzwerkadapter richten Sie später ein.
    1. Wählen Sie auf der Seite **Virtuelle Festplatte verbinden** den Eintrag **Vorhandene virtuelle Festplatte verwenden** aus. Navigieren Sie zum Speicherort der Datei **metasploitable.vhdx**, die Sie im vorherigen Schritt erstellt haben, und wählen Sie **Weiter** aus.

        ![Seite „Virtuelle Festplatte verbinden“](./media/class-type-ethical-hacking/connect-virtual-network-disk.png)
    1. Wählen Sie auf der Seite zum **Abschließen des Assistenten für neue virtuelle Computer** den Befehl **Fertig stellen** aus.
    1. Sobald der virtuelle Computer erstellt ist, wählen Sie ihn im Hyper-V-Manager aus. Schalten Sie den Computer noch nicht ein.  
    1. Wählen Sie **Aktion** -> **Einstellungen** aus.
    1. Wählen Sie im Dialogfeld **Einstellungen für Metasploitable** die Option **Hardware hinzufügen** aus.
    1. Wählen Sie **Älterer Netzwerkadapter** aus, und wählen Sie **Hinzufügen** aus.

        ![Seite „Netzwerkadapter“](./media/class-type-ethical-hacking/network-adapter-page.png)
    1. Wählen Sie auf der Seite **Älterer Netzwerkadapter** den Eintrag **LabServicesSwitch** für die Einstellung **Virtueller Switch** aus, und wählen Sie **OK** aus. „LabServicesSwitch“ wurde während der Vorbereitung des Vorlagencomputers für Hyper-V im Abschnitt **Vorbereiten des Vorlagencomputers für geschachtelte Virtualisierung** erstellt.

        ![Seite „Älterer Netzwerkadapter“](./media/class-type-ethical-hacking/legacy-network-adapter-page.png)
    1. Das Metasploitable-Image ist jetzt einsatzbereit. Wählen Sie im **Hyper-V-Manager****Aktion** -> **Starten** und dann **Aktion** -> **Verbinden** aus, um eine Verbindung mit dem virtuellen Computer herzustellen.  Der Standardbenutzername lautet **msfadmin** und das Kennwort **msfadmin**.

Die Vorlage wird jetzt aktualisiert und verfügt über Images, die für einen Kurs zu Penetrationstests im Rahmen des ethischen Hackens erforderlich sind, ein Image mit Tools zur Ausführung der Penetrationstests sowie ein weiteres Image mit zu ermittelnden Schwachstellen. Das Vorlagenimage kann jetzt im Kurs veröffentlicht werden. Wählen Sie auf der Vorlagenseite die Schaltfläche **Veröffentlichen** aus, um die Vorlage im Lab zu veröffentlichen.

## <a name="cost"></a>Kosten  

Das folgende Beispiel dient der Einschätzung der Lab-Kosten:

Für einen Kurs mit 25 Teilnehmern, 20 planmäßigen Kursstunden und 10 Stunden Hausaufgaben bzw. Arbeitsaufträgen entstünden folgende Kosten für das Lab:

25 Kursteilnehmer · (20 + 10) Stunden · 55 Lab-Einheiten · 0,01 USD pro Stunde = 412,50 USD

>[!IMPORTANT]
>Diese Kostenschätzung dient ausschließlich zu Beispielzwecken. Weitere Informationen zu den aktuellen Preisen finden Sie unter [Azure Lab Services – Preise](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Zusammenfassung

In diesem Artikel wurden die Schritte zum Erstellen eines Labs für einen Kurs zum ethischen Hacken erläutert. Er enthält Schritte zum Einrichten der geschachtelten Virtualisierung, um zwei virtuelle Computer auf dem virtuellen Hostcomputer für Penetrationstests zu erstellen.

## <a name="next-steps"></a>Nächste Schritte

Die nächsten Schritte sind die gleichen für sämtliche Labs:

- [Hinzufügen von Benutzern](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Kontingent festlegen](how-to-configure-student-usage.md#set-quotas-for-users)
- [Zeitplan festlegen](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Registrierungslinks an Kursteilnehmer senden](how-to-configure-student-usage.md#send-invitations-to-users).
