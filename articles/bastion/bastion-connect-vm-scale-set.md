---
title: Herstellen einer Verbindung mit einer Windows-VM-Skalierungsgruppe mit Azure Bastion
description: Erfahren Sie, wie Sie eine Verbindung mit einer Azure-VM-Skalierungsgruppe mit Azure Bastion herstellen.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 09/20/2021
ms.author: cherylmc
ms.openlocfilehash: e57ee80fa7727a6f5e7e1ec59857746cb4b8d590
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128620653"
---
# <a name="connect-to-a-virtual-machine-scale-set-using-azure-bastion"></a>Herstellen einer Verbindung mit einer VM-Skalierungsgruppe mit Azure Bastion

In diesem Artikel erfahren Sie, wie Sie mit Azure Bastion in einem virtuellen Azure-Netzwerk sicher und problemlos RDP auf Ihrer Windows-VM-Skalierungsgruppeninstanz durchführen können. Sie können direkt vom Azure-Portal aus eine Verbindung mit einer VM-Skalierungsgruppeninstanz herstellen. Wenn Sie Azure Bastion verwenden, benötigen VMs keinen Client, keinen Agent und auch keine zusätzliche Software. Weitere Informationen über Azure Bastion finden Sie in der [Übersicht](bastion-overview.md).

## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie sicher, dass Sie einen Azure Bastion-Host für das virtuelle Netzwerk eingerichtet haben, in dem sich die VM-Skalierungsgruppe befindet. Weitere Informationen finden Sie unter [Erstellen eines Azure Bastion-Hosts](./tutorial-create-host-portal.md). Sobald der Bastion-Dienst provisioniert und in Ihrem virtuellen Netzwerk bereitgestellt wurde, können Sie ihn verwenden, um mit jeder VM-Skalierungsgruppeninstanz in diesem virtuellen Netzwerk eine Verbindung herzustellen. Bastion setzt voraus, dass Sie RDP für die Verbindung mit einer Windows-VM-Skalierungsgruppe und SSH für die Verbindung mit Ihrer Linux-VM-Skalierungsgruppe verwenden. Informationen zur Verbindung mit einer Linux-VM finden Sie unter [Herstellen einer Verbindung mit einer VM – Linux](bastion-connect-vm-ssh-linux.md).

## <a name="connect-using-rdp"></a><a name="rdp"></a>Verbindung über RDP

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com). Navigieren Sie zu der VM-Skalierungsgruppe, mit der Sie eine Verbindung herstellen möchten.

   ![Navigieren](./media/bastion-connect-vm-scale-set/1.png)
2. Navigieren Sie zu der VM-Skalierungsgruppeninstanz, mit der Sie eine Verbindung herstellen möchten, und wählen Sie **Verbinden** aus. Wenn Sie eine RDP-Verbindung verwenden, sollte die VM-Skalierungsgruppe eine Windows-VM-Skalierungsgruppe sein.

   ![VM-Skalierungsgruppe](./media/bastion-connect-vm-scale-set/2.png)
3. Nachdem Sie **Verbinden** ausgewählt haben, wird eine Seitenleiste mit drei Registerkarten angezeigt – „RDP“, „SSH“ und „Bastion“. Wählen Sie in der Seitenleiste die Registerkarte **Bastion** aus. Wenn Sie Bastion nicht für das virtuelle Netzwerk bereitgestellt haben, können Sie den Link auswählen, um Bastion zu konfigurieren. Konfigurationsanweisungen finden Sie unter [Konfigurieren von Bastion](./tutorial-create-host-portal.md).

   ![Registerkarte „Bastion“](./media/bastion-connect-vm-scale-set/3.png)
4. Geben Sie auf der Registerkarte „Bastion“ den Benutzernamen und das Kennwort für Ihre VM-Skalierungsgruppe ein, und wählen Sie dann **Verbinden** aus.

   ![Verbinden](./media/bastion-connect-vm-scale-set/4.png)
5. Die RDP-Verbindung zu diesem virtuellen Computer über Bastion wird direkt im Azure-Portal (über HTML5) über Port 443 und den Bastion-Dienst geöffnet.

## <a name="next-steps"></a>Nächste Schritte

Lesen Sie die [häufig gestellten Fragen zu Bastion](bastion-faq.md).