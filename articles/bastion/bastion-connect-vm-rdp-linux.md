---
title: Herstellen einer Verbindung mit einer Linux-VM über RDP
titleSuffix: Azure Bastion
description: Hier erfahren Sie, wie Sie mithilfe von Azure Bastion über RDP eine Verbindung mit einer Linux-VM herstellen.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 10/12/2021
ms.author: cherylmc
ms.custom: ignite-fall-2021
ms.openlocfilehash: 8704ade5def1938e789ad4e092cca804cb9af62b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131033511"
---
# <a name="create-an-rdp-connection-to-a-linux-vm-using-azure"></a>Erstellen einer RDP-Verbindung zu einer Linux-VM mit Azure 

In diesem Artikel erfahren Sie, wie Sie sicher und nahtlos eine RDP-Verbindung mit Ihren Linux-VMs in einem virtuellen Azure-Netzwerk direkt über das Azure-Portal erstellen. Wenn Sie Azure Bastion verwenden, benötigen Ihre virtuellen Computer keinen Client, keinen Agent und auch keine zusätzliche Software. Sie können auch mithilfe von SSH eine Verbindung mit einer Linux-VM herstellen. Weitere Informationen finden Sie unter [Erstellen einer SSH-Verbindung mit einer Linux-VM](bastion-connect-vm-ssh-linux.md).

Azure Bastion bietet sichere Verbindungen mit allen virtuellen Computern in dem virtuellen Netzwerk, in dem der Dienst bereitgestellt wird. Durch die Verwendung von Azure Bastion wird verhindert, dass Ihre virtuellen Computer RDP- und SSH-Ports öffentlich verfügbar machen. Gleichzeitig wir weiterhin der sichere Zugriff per RDP/SSH ermöglicht. Weitere Informationen finden Sie unter [Was ist Azure Bastion?](bastion-overview.md).

> [!NOTE]
> Die Verwendung von RDP zur Verbindung mit einer virtuellen Linux-Maschine erfordert die Azure Bastion Standard SKU.
>

Wenn Sie Azure Bastion verwenden, um über RDP eine Verbindung mit einem virtuellen Linux-Computer herzustellen, müssen Sie Benutzername/Kennwort für die Authentifizierung verwenden.

## <a name="prerequisites"></a>Voraussetzungen

Vergewissern Sie sich zunächst, dass die folgenden Kriterien erfüllt sind:

Stellen Sie sicher, dass Sie einen Azure Bastion-Host für das virtuelle Netzwerk eingerichtet haben, in dem sich die VM befindet. Weitere Informationen finden Sie unter [Erstellen eines Azure Bastion-Hosts](tutorial-create-host-portal.md). Sobald der Bastion-Dienst provisioniert und in Ihrem virtuellen Netzwerk bereitgestellt wurde, können Sie ihn verwenden, um sich mit jeder VM in diesem virtuellen Netzwerk zu verbinden.

Um eine RDP-Verbindung mit einem virtuellen Linux-Computer zu erstellen, müssen Sie auch sicherstellen, dass „xrdp“ auf Ihrem virtuellen Linux-Computer installiert und konfiguriert ist. Informationen dazu finden Sie unter [Verwenden von xrdp mit Linux](../virtual-machines/linux/use-remote-desktop.md).

### <a name="required-roles"></a>Erforderliche Rollen

Zum Herstellen einer Verbindung sind die folgenden Rollen erforderlich:

* Rolle „Leser“ auf dem virtuellen Computer
* Rolle „Leser“ auf dem Netzwerkadapter mit privater IP-Adresse des virtuellen Computers
* Rolle „Leser“ für die Azure Bastion-Ressource

### <a name="ports"></a>Ports

Zum Herstellen einer Verbindung mit dem virtuellen Linux-Computer über RDP müssen die folgenden Ports auf Ihrer VM geöffnet sein:

* Eingehender Port: RDP (3389) *oder*
* Eingehender Port: Benutzerdefinierter Wert (Sie müssen diesen benutzerdefinierten Port dann angeben, wenn Sie über Azure Bastion eine Verbindung mit der VM herstellen)

### <a name="supported-configurations"></a>Unterstützte Konfigurationen

Derzeit unterstützt Azure Bastion nur das Herstellen einer Verbindung mit Linux-VMs über RDP mithilfe von **xrdp**.

## <a name="connect"></a><a name="rdp"></a>Verbinden

[!INCLUDE [Connect to a Linux VM using RDP](../../includes/bastion-vm-rdp-linux.md)]
 
## <a name="next-steps"></a>Nächste Schritte

Lesen Sie die [häufig gestellten Fragen zu Bastion](bastion-faq.md).
