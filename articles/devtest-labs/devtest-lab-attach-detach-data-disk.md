---
title: Anfügen eines vorhandenen Datenträgers an eine Lab-VM
description: In diesem Artikel erfahren Sie, wie Sie einen Lab-Datenträger an eine Lab-VM in Azure DevTest Labs anfügen oder von dieser trennen.
ms.topic: how-to
ms.date: 10/26/2021
ms.openlocfilehash: 8afd2fff50a893fb293d7b419c4839c34487c32d
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131453934"
---
# <a name="attach-or-detach-a-lab-data-disk-to-a-lab-virtual-machine-in-azure-devtest-labs"></a>Anfügen oder Trennen eines Lab-Datenträgers an eine bzw. von einer Lab-VM in Azure DevTest Labs

Sie können einen neuen [Labdatenträger](../virtual-machines/managed-disks-overview.md) für einen virtuellen Azure-Labcomputer (VM) erstellen und anfügen. Dieser Datenträger kann dann getrennt und gelöscht, wieder angefügt oder an eine andere Lab-VM angefügt werden, deren Eigentümer*in Sie sind. Diese Funktionalität ist besonders für die Verwaltung von Speicher oder Software außerhalb der einzelnen virtuellen Computer praktisch.

In diesem Artikel erfahren Sie, wie Sie einen Datenträger an einen virtuellen Labcomputer anfügen und davon trennen.

## <a name="prerequisites"></a>Voraussetzungen

Ihr virtueller Labcomputer muss ausgeführt werden. Die Größe des virtuellen Computers entscheidet darüber, wie viele Datenträger Sie anfügen können. Ausführliche Informationen finden Sie unter [Größen für virtuelle Computer](../virtual-machines/sizes.md).

## <a name="attach-a-new-data-disk"></a>Anschließen eines neuen Datenträgers

Führen Sie die folgenden Schritte durch, um einen neuen verwalteten Datenträger zu erstellen und an eine VM in Azure DevTest Labs anzufügen:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

1. Navigieren Sie in **DevTest Labs** zu Ihrem Lab.

1. Wählen Sie Ihre gegenwärtig ausgeführte VM aus.

1. Klicken Sie auf der Seite **Virtueller Computer** unter **Einstellungen** auf **Datenträger**.
 
1. Klicken Sie auf **Neuen anfügen**.

    :::image type="content" source="./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-new.png" alt-text="Screenshot: Anfügen eines neuen Datenträgers an einen virtuellen Computer":::

1. Geben Sie auf der Seite **Neuen Datenträger anfügen** die folgenden Informationen an: 

    |Eigenschaft | BESCHREIBUNG |
    |---|---|
    |Name|Geben Sie einen eindeutigen Namen ein.|
    |Datenträgertyp| Wählen Sie in der Dropdownliste einen [Datenträgertyp](../virtual-machines/disks-types.md) aus.|
    |Größe (GiB)|Geben Sie eine Größe in Gigabyte ein.|

    :::image type="content" source="./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-new-form.png" alt-text="Screenshot: Ausfüllen des Formulars „Neuen Datenträger anfügen“":::

1. Wählen Sie **OK** aus.

1. Sie werden zurück auf die Seite **Virtueller Computer** geleitet. Zeigen Sie unter **Datenträger** Ihren angefügten Datenträger an.

    :::image type="content" source="./media/devtest-lab-attach-detach-data-disk/devtest-lab-attached-data-disk.png" alt-text="Screenshot: Angefügter Datenträger unter „Datenträger“":::

## <a name="detach-a-data-disk"></a>Trennen eines Datenträgers

Beim Trennen wird der Labdatenträger von der Lab-VM entfernt, aber für eine spätere Verwendung im Speicher aufbewahrt.

### <a name="detach-from-the-vms-management-page"></a>Trennen über die Verwaltungsseite der VM

1. Navigieren Sie in **DevTest Labs** zu Ihrem Lab.

1. Wählen Sie Ihre gegenwärtig ausgeführte VM mit einem angefügten Datenträger aus.

1. Klicken Sie auf der Seite **Virtueller Computer** unter **Einstellungen** auf **Datenträger**.

1. Wählen Sie unter **Datenträger** den Datenträger aus, der getrennt werden soll.

    :::image type="content" source="./media/devtest-lab-attach-detach-data-disk/devtest-lab-detach-button.png" alt-text="Screenshot: Ausgewählte Datenträger für einen virtuellen Computer":::

1. Klicken Sie auf der Seite **Datenträger** auf **Trennen**.

    :::image type="content" source="./media/devtest-lab-attach-detach-data-disk/devtest-lab-detach-data-disk-2.png" alt-text="Screenshot: Detailbereich eines Datenträgers mit hervorgehobener Aktion „Trennen“":::

1. Klicken Sie auf **OK**, um zu bestätigen, dass der Datenträger getrennt werden soll. Der Datenträger wird getrennt und steht zum Anfügen an eine andere VM zur Verfügung. 

### <a name="detach-from-the-labs-management-page"></a>Trennen über die Verwaltungsseite des Labs

1. Navigieren Sie in **DevTest Labs** zu Ihrem Lab.

1. Klicken Sie unter **Eigenes Lab** auf **Eigene Datenträger**.

1. Klicken Sie bei dem Datenträger, der getrennt werden soll, auf die Auslassungspunkte ( **...** ) und anschließend auf **Trennen**.

    :::image type="content" source="./media/devtest-lab-attach-detach-data-disk/devtest-lab-detach-data-disk.png" alt-text="Screenshot: Trennen eines Datenträgers":::

1. Wählen Sie **Ja** aus, um zu bestätigen, dass der Datenträger getrennt werden sollen.

   > [!NOTE]
   > Wenn ein Datenträger bereits getrennt ist, können Sie festlegen, dass er aus der Liste der verfügbaren Datenträger entfernt werden soll. Klicken Sie hierzu auf **Löschen**.

## <a name="attach-an-existing-disk"></a>Anfügen eines vorhandenen Datenträgers

Führen Sie die folgenden Schritte aus, um einen bereits vorhandenen verfügbaren Datenträger an eine gegenwärtig ausgeführte VM anzufügen. 

1. Navigieren Sie in **DevTest Labs** zu Ihrem Lab.

1. Wählen Sie Ihre gegenwärtig ausgeführte VM aus.

1. Klicken Sie auf der Seite **Virtueller Computer** unter **Einstellungen** auf **Datenträger**.

1. Klicken Sie auf **Vorhandenen anfügen**.

    :::image type="content" source="./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-existing-button.png" alt-text="Screenshot: Ausgewählte Einstellung „Datenträger“ und hervorgehobene Option „Vorhandenen anfügen“":::

1. Wählen Sie auf der Seite **Vorhandenen Datenträger anfügen** einen Datenträger aus, und klicken Sie dann auf **OK**. Nach einem kurzen Augenblick wird der Datenträger an die VM angefügt und in der Liste der **Datenträger** für diese VM angezeigt.

    :::image type="content" source="./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-existing.png" alt-text="Screenshot: Anfügen eines vorhandenen Datenträgers an einen virtuellen Computer":::

## <a name="upgrade-an-unmanaged-data-disk"></a>Durchführen eines Upgrades für einen nicht verwalteten Datenträger

Wenn Sie über eine VM mit nicht verwalteten Datenträgern verfügen, können Sie diese VM so konvertieren, dass sie verwaltete Datenträger verwendet. Bei diesem Prozess werden sowohl der Betriebssystemdatenträger als auch alle anderen angefügten Datenträger konvertiert.

[Trennen Sie zunächst den Datenträger](#detach-a-data-disk) von der nicht verwalteten VM. [Fügen Sie den Datenträger erneut an eine verwaltete VM an](#attach-an-existing-disk), damit automatisch ein Upgrade für den nicht verwalteten Datenträger auf einen verwalteten Datenträger durchgeführt wird.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie, wie Datenträger für [abrufbare VMs](devtest-lab-add-claimable-vm.md#unclaim-a-vm) verwaltet werden.
