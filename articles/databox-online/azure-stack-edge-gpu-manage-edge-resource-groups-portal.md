---
title: Verwalten von Edge-Ressourcengruppen auf Ihrem Azure Stack Edge Pro GPU-Gerät
description: Erfahren Sie, wie Sie Edge-Ressourcengruppen auf Ihrem Azure Stack Edge Pro GPU-, Azure Stack Edge Pro R-, and Azure Stack Edge Mini R-Gerät über das Azure-Portal verwalten.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/02/2021
ms.author: alkohli
ms.openlocfilehash: c77fb42579af8d1e4bad7e55746ad36d16c983b3
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122346976"
---
# <a name="manage-edge-resource-groups-on-azure-stack-edge-pro-gpu-devices"></a>Verwalten von Edge-Ressourcengruppen auf Azure Stack Edge Pro GPU-Geräten

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Edge-Ressourcengruppen enthalten Ressourcen, die während der Erstellung und Bereitstellung virtueller Computer über den lokalen Azure Resource Manager auf dem Gerät erstellt werden. Diese lokalen Ressourcen können virtuelle Computer, VM-Images, Datenträger, Netzwerkschnittstellen und andere Ressourcentypen wie Edge-Speicherkonten umfassen.

In diesem Artikel wird beschrieben, wie Sie Edge-Ressourcengruppen auf einem Azure Stack Edge Pro GPU-Gerät anzeigen und löschen.

## <a name="view-edge-resource-groups"></a>Anzeigen von Edge-Ressourcengruppen

Führen Sie die folgenden Schritte aus, um die Edge-Ressourcengruppen für das aktuelle Abonnement anzuzeigen.

1. Navigieren Sie auf Ihrem Gerät zu **Virtuelle Computer**, und wechseln Sie zum Bereich **Ressourcen**. Wählen Sie **Edge-Ressourcengruppen** aus.

    ![Screenshot der Ansicht „Ressourcen“ für virtuelle Computer auf einem Azure Stack Edge-Gerät. Die Registerkarte „Edge-Ressourcengruppen“ wird angezeigt und ist hervorgehoben.](media/azure-stack-edge-gpu-manage-edge-resource-groups-portal/edge-resource-groups-01.png)

    > [!NOTE]
    > Sie können dieselbe Auflistung erhalten, indem Sie [Get-AzResource](/powershell/module/az.resources/get-azresource?view=azps-6.1.0&preserve-view=true) in Azure PowerShell verwenden, nachdem Sie die Azure Resource Manager-Umgebung auf Ihrem Gerät eingerichtet haben. Weitere Informationen finden Sie unter [Herstellen einer Verbindung mit Azure Resource Manager](azure-stack-edge-gpu-connect-resource-manager.md).


## <a name="delete-an-edge-resource-group"></a>Löschen einer Edge-Ressourcengruppe

Führen Sie die folgenden Schritte aus, um eine nicht mehr verwendete Edge-Ressourcengruppe zu löschen.

> [!NOTE]
> - Eine Ressourcengruppe muss leer sein, damit sie gelöscht werden kann. 
> - Die ASERG-Ressourcengruppe kann nicht gelöscht werden. In dieser Ressourcengruppe wird das virtuelle Netzwerk ASEVNET gespeichert, das automatisch erstellt wird, wenn Sie Compute auf Ihrem Gerät aktivieren.

1. Navigieren Sie auf Ihrem Gerät zu **Virtuelle Computer**, und wechseln Sie zum Bereich **Ressourcen**. Wählen Sie **Edge-Ressourcengruppen** aus.

    ![Screenshot der Ansicht „Ressourcen“ für virtuelle Computer auf einem Azure Stack Edge-Gerät. Die Registerkarte „Edge-Ressourcengruppen“ wird angezeigt und ist hervorgehoben.](media/azure-stack-edge-gpu-manage-edge-resource-groups-portal/edge-resource-groups-01.png)

1. Wählen Sie die Ressourcengruppe aus, die Sie löschen möchten. Wählen Sie ganz rechts neben der Ressourcengruppe das Löschsymbol (Papierkorb) aus.

   Das Löschsymbol wird nur angezeigt, wenn eine Ressourcengruppe keine Ressourcen enthält.

    ![Screenshot der Ansicht „Ressourcen“ mit der Registerkarte „Edge-Ressourcengruppen“ für VMs auf einem Azure Stack Edge-Gerät. Ein Papierkorbsymbol neben einer Ressourcengruppe zeigt an, dass sie gelöscht werden kann. Das Symbol ist hervorgehoben.](media/azure-stack-edge-gpu-manage-edge-resource-groups-portal/edge-resource-groups-02.png)

1. Es wird eine Meldung angezeigt, in der Sie bestätigen sollen, dass Sie die Edge-Ressourcengruppe löschen möchten. Der Vorgang kann nicht rückgängig gemacht werden. Wählen Sie **Ja**.

    ![Screenshot der Registerkarte „Edge-Ressourcengruppen“ in der Ansicht „Ressourcen“ für VMs. Das hervorgehobene Papierkorbsymbol zeigt an, dass eine Ressourcengruppe gelöscht werden kann.](./media/azure-stack-edge-gpu-manage-edge-resource-groups-portal/edge-resource-groups-03.png)

    Nach Abschluss des Löschvorgangs wird die Ressourcengruppe aus der Liste entfernt.

## <a name="next-steps"></a>Nächste Schritte

- Informationen zum Verwalten Ihres Azure Stack Edge Pro GPU-Geräts finden Sie unter [Verwenden der lokalen Webbenutzeroberfläche zur Verwaltung eines Azure Stack Edge Pro GPU-Geräts](azure-stack-edge-manage-access-power-connectivity-mode.md).

- [Richten Sie die Azure Resource Manager-Umgebung auf Ihrem Gerät ein](azure-stack-edge-gpu-connect-resource-manager.md).