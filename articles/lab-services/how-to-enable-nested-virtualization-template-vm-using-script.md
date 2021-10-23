---
title: Aktivieren von geschachtelter Virtualisierung auf einem virtuellen Vorlagencomputer in Azure Lab Services (Skript) | Microsoft-Dokumentation
description: 'Hier erfahren Sie, wie Sie mithilfe eines Skripts eine VM-Vorlage erstellen, die mehrere VMs enthält.  Das heißt: Aktivieren Sie die geschachtelte Virtualisierung auf einer VM-Vorlage in Azure Lab Services.'
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: cd2d206ceff358421f7c36f31554a0cb5684a482
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/19/2021
ms.locfileid: "130180417"
---
# <a name="enable-nested-virtualization-on-a-template-virtual-machine-in-azure-lab-services-using-a-script"></a>Aktivieren geschachtelter Virtualisierung auf einem virtuellen Vorlagencomputer in Azure Lab Services mit einem Skript

Geschachtelte Virtualisierung ermöglicht Ihnen, innerhalb einer Vorlage für virtuelle Computer in einem Lab eine Umgebung mit mehreren virtuellen Computern zu erstellen. Wenn diese Vorlage veröffentlicht wird, wird für jeden Benutzer im Lab ein eingerichteter virtueller Computer bereitgestellt, in dem weitere virtuelle Computer eingeschlossen sind.  Weitere Informationen zur geschachtelten Virtualisierung zu und Azure Lab Services finden Sie unter [Aktivieren der geschachtelten Virtualisierung auf einem virtuellen Vorlagencomputer in Azure Lab Services](how-to-enable-nested-virtualization-template-vm.md).

Die Schritte in diesem Artikel konzentrieren sich auf das Einrichten der geschachtelten Virtualisierung für Windows Server 2016, Windows Server 2019 oder Windows 10. Sie verwenden dabei ein Skript, um den Vorlagencomputer mit Hyper-V einzurichten.  Führen Sie die folgenden Schritte aus, um zu erfahren, wie Sie [Hyper-V-Skripts für Lab Services](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/HyperV) verwenden können.

>[!IMPORTANT]
>Wählen Sie bei der Erstellung des Labs als Größe für die VM **Large (nested virtualization)** (Groß (geschachtelte Virtualisierung)) oder **Medium (nested virtualization)** (Mittel (geschachtelte Virtualisierung)) aus.  Andernfalls funktioniert die verschachtelte Virtualisierung nicht.  

## <a name="run-script"></a>Ausführen des Skripts

1. Wenn Sie Internet Explorer verwenden, müssen Sie der Liste vertrauenswürdiger Sites möglicherweise `https://github.com` hinzufügen.
    1. Öffnen Sie Internet Explorer.
    1. Wählen Sie das Zahnradsymbol aus, und wählen Sie **Internetoptionen**.  
    1. Wenn das Dialogfeld **Internetoptionen** angezeigt wird, wählen Sie **Sicherheit**, **Vertrauenswürdige Sites** aus, und klicken Sie auf die Schaltfläche **Sites**.
    1. Wenn das Dialogfeld "**Vertrauenswürdige Sites** angezeigt wird, fügen Sie der Liste vertrauenswürdiger Websites `https://github.com` hinzu, und wählen Sie **Schließen** aus.

        ![Vertrauenswürdige Sites](./media/how-to-enable-nested-virtualization-template-vm-using-script/trusted-sites-dialog.png)
1. Laden Sie die Git-Repositorydateien herunter, wie in den folgenden Schritten beschrieben.
    1. Wechseln Sie zu [https://github.com/Azure/azure-devtestlab/](https://github.com/Azure/azure-devtestlab/).
    1. Klicken Sie auf die Schaltfläche **Clone or Download** (Klonen oder herunterladen).
    1. Klicken Sie auf **ZIP herunterladen**.
    1. Extrahieren Sie die ZIP-Datei.

    >[!TIP]
    >Sie können das Git-Repository unter [https://github.com/Azure/azure-devtestlab.git](https://github.com/Azure/azure-devtestlab.git) auch klonen.

1. Starten Sie die **PowerShell** im **Administrator** modus.
1. Navigieren Sie im PowerShell-Fenster zu dem Ordner mit dem heruntergeladenen Skript. Wenn Sie vom obersten Ordner der Repositorydateien aus navigieren, befindet sich das Skript in `azure-devtestlab\samples\ClassroomLabs\Scripts\HyperV\`.
1. Möglicherweise müssen Sie die Ausführungsrichtlinie ändern, damit das Skript erfolgreich ausgeführt wird. Führen Sie den folgenden Befehl aus:

    ```powershell
    Set-ExecutionPolicy bypass -force
    ```

1. Ausführen des Skripts:

    ```powershell
    .\SetupForNestedVirtualization.ps1
    ```

    > [!NOTE]
    > Das Skript erfordert möglicherweise, dass der Computer neu gestartet wird. Befolgen Sie die Anweisungen des Skripts, und führen Sie das Skript erneut aus, bis in der Ausgabe **Skript abgeschlossen** angezeigt wird.
1. Vergessen Sie nicht, die Ausführungsrichtlinie zurückzusetzen. Führen Sie den folgenden Befehl aus:

    ```powershell
    Set-ExecutionPolicy default -force
    ```

## <a name="conclusion"></a>Zusammenfassung

Jetzt ist Ihr Vorlagencomputer bereit, um virtuelle Hyper-V-Computer zu erstellen. Anweisungen zum Erstellen von virtuellen Hyper-V-Computern finden Sie unter [Erstellen eines virtuellen Computers in Hyper-V](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v). Verfügbare Betriebssysteme und Software finden Sie außerdem im [Microsoft Evaluation Center](https://www.microsoft.com/evalcenter/).  

## <a name="next-steps"></a>Nächste Schritte

Die nächsten Einrichtungsschritte sind für alle Labs gleich.

- [Hinzufügen von Benutzern](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Kontingent festlegen](how-to-configure-student-usage.md#set-quotas-for-users)
- [Zeitplan festlegen](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Registrierungslinks per E-Mail an Kursteilnehmer senden](how-to-configure-student-usage.md#send-invitations-to-users)