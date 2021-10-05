---
title: Neustarten einer VM in einem Lab
description: Dieser Artikel enthält die Schritte zum schnellen und einfachen Neustarten virtueller Computer (VMs) in Azure DevTest Labs.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 07d580421c4f4bca8fbf81e7d300d1e38873c386
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128625181"
---
# <a name="restart-a-vm-in-a-lab-in-azure-devtest-labs"></a>Neustarten eines virtuellen Computers in einem Lab in Azure DevTest Labs
Sie können einen virtuellen Computer mithilfe der Schritte in diesem Artikel schnell und problemlos neu starten. Beachten Sie Folgendes vor dem Neustart eines virtuellen Computers:

- Der virtuelle Computer muss aktiv sein, damit die Neustartfunktion aktiviert werden kann.
- Wenn ein Benutzer mit einem aktiven virtuellen Computer verbunden ist, wenn der Neustart ausgeführt wird, muss die Verbindung mit dem virtuellen Computer nach dem Neustart erneut hergestellt werden.
- Wenn beim Neustart des virtuellen Computers ein Artefakt angewendet wird, erhalten Sie eine Warnung, dass das Artefakt möglicherweise nicht angewendet wird.

    ![Warnung beim Neustart während der Anwendung von Artefakten](./media/devtest-lab-restart-vm/devtest-lab-restart-vm-apply-artifacts.png)


   > [!NOTE]
   > Wenn der virtuelle Computer beim Anwenden eines Artefakts angehalten wurde, können Sie die Funktion zum Neustart des virtuellen Computers als potenzielle Möglichkeit zur Behebung des Problems verwenden.
   >
   >

## <a name="steps-to-restart-a-vm-in-a-lab-in-azure-devtest-labs"></a>Schritte zum Neustarten eines virtuellen Computers in einem Lab in Azure DevTest Labs
1. Melden Sie sich beim [Azure-Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) an.
1. Wählen Sie **Alle Dienste** und dann in der Liste die Option **DevTest Labs**.
1. Wählen Sie in der Liste der Labs das Lab aus, das den neu zu startenden virtuellen Computer enthält.
1. Wählen Sie im linken Bereich die Option **Meine virtuellen Computer** aus.
1. Wählen Sie in der Liste der virtuellen Computer einen aktiven virtuellen Computer aus.
1. Wählen Sie am Anfang des Bereichs zur Verwaltung virtueller Computer die Option **Neu starten** aus.

    ![Schaltfläche „Neu starten“ für virtuellen Computer](./media/devtest-lab-restart-vm/devtest-lab-restart-vm.png)

1. Überwachen Sie den Status des Neustarts, indem Sie das Symbol **Benachrichtigungen** am oberen rechten Rand des Fensters auswählen.

    ![Anzeigen des Status für den Neustart des virtuellen Computers](./media/devtest-lab-restart-vm/devtest-lab-restart-notification.png)

Sie können auch einen aktiven virtuellen Computer neu starten, indem Sie dessen Auslassungspunkte (...) in der Liste von **Meine virtuellen Computer** auswählen.

![Neustarten virtueller Computer mithilfe der Auslassungspunkte](./media/devtest-lab-restart-vm/devtest-lab-restart-elipses.png)

## <a name="next-steps"></a>Nächste Schritte
* Nachdem der virtuelle Computer neu gestartet wurde, können Sie die Verbindung mit diesem wiederherstellen, indem sie im Verwaltungsbereich **Verbinden** auswählen.
* Erkunden Sie den [DevTest Labs-Azure Resource Manager-Katalog mit Schnellstartvorlagen](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates).
