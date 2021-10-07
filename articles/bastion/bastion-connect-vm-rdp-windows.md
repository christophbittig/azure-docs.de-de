---
title: Herstellen einer Verbindung mit einer Windows-VM über RDP
titleSuffix: Azure Bastion
description: Hier erfahren Sie, wie Sie mithilfe von Azure Bastion über RDP eine Verbindung mit einer Windows-VM herstellen.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 09/20/2021
ms.author: cherylmc
ms.openlocfilehash: 1a1423a0587a64452671e74a210baa9d2ad16ea8
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128700245"
---
# <a name="create-an-rdp-connection-to-a-windows-vm-using-azure-bastion"></a>Erstellen einer RDP-Verbindung mit einer Windows-VM mithilfe von Azure Bastion

In diesem Artikel erfahren Sie, wie Sie sicher und nahtlos eine RDP-Verbindung mit Ihren Windows-VMs in einem virtuellen Azure-Netzwerk direkt über das Azure-Portal erstellen. Wenn Sie Azure Bastion verwenden, benötigen Ihre virtuellen Computer keinen Client, keinen Agent und auch keine zusätzliche Software. Sie können auch mithilfe von SSH eine Verbindung mit einer Windows-VM herstellen. Weitere Informationen finden Sie unter [Erstellen einer SSH-Verbindung mit einer Windows-VM](bastion-connect-vm-ssh-windows.md).

Azure Bastion bietet sichere Verbindungen mit allen virtuellen Computern in dem virtuellen Netzwerk, in dem der Dienst bereitgestellt wird. Durch die Verwendung von Azure Bastion wird verhindert, dass Ihre virtuellen Computer RDP- und SSH-Ports öffentlich verfügbar machen. Gleichzeitig wir weiterhin der sichere Zugriff per RDP/SSH ermöglicht. Weitere Informationen finden Sie unter [Was ist Azure Bastion?](bastion-overview.md).

## <a name="prerequisites"></a>Voraussetzungen

Vergewissern Sie sich zunächst, dass die folgenden Kriterien erfüllt sind:

* Ein VNET mit bereits installiertem Bastion-Host

  * Stellen Sie sicher, dass Sie einen Azure Bastion-Host für das virtuelle Netzwerk eingerichtet haben, in dem sich die VM befindet. Sobald der Bastion-Dienst provisioniert und in Ihrem virtuellen Netzwerk bereitgestellt wurde, können Sie ihn verwenden, um sich mit jeder VM im virtuellen Netzwerk zu verbinden. 
  * Informationen zum Einrichten eines Azure Bastion-Hosts finden Sie unter [Erstellen eines Bastion-Hosts](tutorial-create-host-portal.md#createhost). Wenn Sie benutzerdefinierte Portwerte konfigurieren möchten, müssen Sie beim Konfigurieren von Bastion die Standard-SKU auswählen.

* Ein virtueller Windows-Computer im virtuellen Netzwerk

### <a name="required-roles"></a>Erforderliche Rollen

* Rolle „Leser“ für den virtuellen Computer
* Rolle „Leser“ für den Netzwerkadapter mit privater IP-Adresse des virtuellen Computers
* Rolle „Leser“ für die Azure Bastion-Ressource

### <a name="ports"></a>Ports

Zum Herstellen einer Verbindung mit dem virtuellen Windows-Computer müssen die folgenden Ports auf Ihrer Windows-VM geöffnet sein:

*   Eingehender Port: RDP (3389) ***oder***
*   Eingehender Port: benutzerdefinierter Wert (Sie müssen diesen benutzerdefinierten Port dann angeben, wenn Sie über Azure Bastion eine Verbindung mit der VM herstellen)

> [!NOTE]
> Wenn Sie einen benutzerdefinierten Portwert angeben möchten, muss Azure Bastion mithilfe der Standard-SKU konfiguriert werden. Mit der Basic-SKU können Sie keine benutzerdefinierten Ports angeben. Die Standard-SKU ist zurzeit als Vorschauversion verfügbar.
>

## <a name="connect"></a><a name="rdp"></a>Verbinden

[!INCLUDE [Connect to a Windows VM](../../includes/bastion-vm-rdp.md)]
 
## <a name="next-steps"></a>Nächste Schritte

Lesen Sie die [häufig gestellten Fragen zu Bastion](bastion-faq.md).
