---
title: Generalisieren einer VM vor Erstellen eines Images
description: Erfahren Sie mehr zum Generalisieren einer VM, um VM-spezifische Informationen zu entfernen, bevor ein Image erstellt wird.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 06/16/2021
ms.author: cynthn
ms.custom: portal
ms.openlocfilehash: e28ec0a10431316f7c5658ec006a7136f5388ff3
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131444609"
---
# <a name="remove-machine-specific-information-by-generalizing-a-vm-before-creating-an-image"></a>Entfernen VM-spezifischer Informationen durch Generalisieren einer VM vor Erstellen eines Images

Die Generalisierung eines virtuellen Computers ist für die Erstellung eines Images in einer [Azure Compute Gallery](shared-image-galleries.md#generalized-and-specialized-images) nicht erforderlich, es sei denn, Sie möchten ausdrücklich ein generalisiertes Image erstellen. Beim Erstellen eines verwalteten Images außerhalb eines Katalogs ist eine Generalisierung erforderlich.

Bei der Generalisierung werden VM-spezifische Informationen entfernt, sodass das Image zum Erstellen mehrerer VMs verwendet werden kann. Nachdem die VM generalisiert wurde, müssen Sie die Plattform darüber informieren, dass dieser Schritt erfolgt ist, damit die Startsequenz ordnungsgemäß festgelegt werden kann. Nachdem eine VM generalisiert wurde, darf sie nicht neu gestartet werden.


## <a name="linux"></a>Linux

Zuerst heben Sie die Bereitstellung des virtuellen Computers mithilfe des Azure-VM-Agents auf, um computerspezifische Dateien und Daten zu löschen. Verwenden Sie auf dem virtuellen Linux-Quellcomputer den Befehl `waagent` mit dem Parameter `-deprovision+user`. Weitere Informationen erhalten Sie im [Benutzerhandbuch für Azure Linux-Agent](./extensions/agent-linux.md). Dieser Vorgang kann nicht rückgängig gemacht werden.

1. Stellen Sie mit einem SSH-Client eine Verbindung mit Ihrem virtuellen Linux-Computer her.
2. Geben Sie im SSH-Fenster den folgenden Befehl ein:
   ```bash
    sudo waagent -deprovision+user
   ```
   > [!NOTE]
   > Führen Sie diesen Befehl nur auf einem virtuellen Computer aus, den Sie als Image erfassen möchten. Die Ausführung dieses Befehls garantiert nicht, dass alle vertraulichen Informationen aus dem Image gelöscht werden oder dass es für eine erneute Verteilung geeignet ist. Der Parameter `+user` entfernt auch das zuletzt bereitgestellte Benutzerkonto. Wenn die Anmeldeinformationen des Benutzerkontos auf dem virtuellen Computer verbleiben sollen, verwenden Sie nur `-deprovision`.
 
3. Geben Sie **y** ein, um fortzufahren. Sie können den Parameter `-force` hinzufügen, um diesen Bestätigungsschritt zu vermeiden.
4. Geben Sie nach der Ausführung dieses Befehls den Befehl **exit** ein, um den SSH-Client zu schließen.  Der virtuelle Computer wird zu diesem Zeitpunkt immer noch ausgeführt.


Anschließend muss die VM auf der Plattform als „Generalisiert“ gekennzeichnet werden. 

```azurecli-interactive
az vm generalize \
   --resource-group myResourceGroup \
   --name myVM
```

## <a name="windows"></a>Windows 

Sysprep entfernt alle persönlichen Konto- und Sicherheitsinformationen und bereitet den Computer darauf vor, als Image verwendet zu werden. Ausführliche Informationen zu Sysprep finden Sie unter [Sysprep (System Preparation) Overview](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview) (Sysprep (Systemvorbereitung): Übersicht).

Stellen Sie sicher, dass die auf dem Computer ausgeführten Serverrollen von Sysprep unterstützt werden. Weitere Informationen finden Sie unter [Sysprep-Unterstützung für Serverrollen](/windows-hardware/manufacture/desktop/sysprep-support-for-server-roles) und [Nicht unterstützte Szenarien](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview#unsupported-scenarios). 

> [!IMPORTANT]
> Nachdem Sie Sysprep auf einem virtuellen Computer ausgeführt haben, gilt dieser als *generalisiert* und kann nicht neu gestartet werden. Der Generalisierungsprozess eines virtuellen Computers kann nicht rückgängig gemacht werden. Wenn Sie die ursprüngliche Funktionsweise des virtuellen Computers beibehalten müssen, erstellen Sie eine [Kopie des virtuellen Computers](./windows/create-vm-specialized.md#option-3-copy-an-existing-azure-vm) und generalisieren diese Kopie. 
>
> Sysprep erfordert, dass die Laufwerke vollständig entschlüsselt werden. Wenn Sie die Verschlüsselung auf Ihrer VM aktiviert haben, deaktivieren Sie diese, bevor Sie Sysprep ausführen.
>
> Wenn Sie Sysprep vor dem Hochladen der virtuellen Festplatte in Azure zum ersten Mal ausführen möchten, stellen Sie sicher, dass Sie [Ihren virtuellen Computer vorbereitet](./windows/prepare-for-upload-vhd-image.md) haben.  
> 
> 

Führen Sie zum Generalisieren Ihres virtuellen Windows-Computers die folgenden Schritte aus:

1. Melden Sie sich beim virtuellen Windows-Computer an.
   
2. Öffnen Sie ein Eingabeaufforderungsfenster als ein Administrator. 

3. Löschen Sie das Verzeichnis „Panther“ (c:\Windows\Panther). Wechseln Sie dann in das Verzeichnis „%windir%\system32\sysprep“, und führen Sie anschließend `sysprep.exe` aus.
   
4. Wählen Sie im Dialogfeld **Systemvorbereitungsprogramm** die Option **Out-of-Box-Experience (OOBE) für System aktivieren**, und aktivieren Sie das Kontrollkästchen **Verallgemeinern**.
   
5. Wählen Sie unter **Optionen für Herunterfahren** die Option **Herunterfahren** aus.
   
6. Klicken Sie auf **OK**.
   
    :::image type="content" source="windows/media/upload-generalized-managed/sysprepgeneral.png" alt-text="![Start Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)":::

6. Nach Abschluss von Sysprep wird der virtuelle Computer heruntergefahren. Starten Sie den virtuellen Computer nicht neu.

> [!TIP]
> **Optional:** Verwenden Sie [DISM](/windows-hardware/manufacture/desktop/dism-optimize-image-command-line-options), um Ihr Image zu optimieren und die Zeit für den ersten Start Ihrer VM zu verringern.
>
> Um das Image zu optimieren, binden Sie die VHD ein, indem Sie in Windows-Explorer darauf doppelklicken und dann DISM mit dem Parameter `/optimize-image` ausführen.
>
> ```cmd
> DISM /image:D:\ /optimize-image /boot
> ```
> Dabei ist „D:“ der Pfad der eingebundenen VHD.
>
> Das Ausführen von `DISM /optimize-image` sollte die letzte Änderung sein, die Sie an Ihrer virtuellen Festplatte vornehmen. Wenn Sie vor der Bereitstellung Änderungen an der virtuellen Festplatte vornehmen, müssen Sie `DISM /optimize-image` erneut ausführen.

Legen Sie nach Abschluss von Sysprep den Status der VM auf **Generalisiert** fest.
   
```azurepowershell-interactive
Set-AzVm -ResourceGroupName $rgName -Name $vmName -Generalized
```
