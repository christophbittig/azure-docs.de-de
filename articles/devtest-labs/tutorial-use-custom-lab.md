---
title: Zugriff auf ein Lab
description: In diesem Tutorial greifen Sie auf das Lab zu, das unter Verwendung von Azure DevTest Labs erstellt wird, beanspruchen virtuelle Computer, verwenden diese, und heben den Anspruch wieder auf.
ms.topic: tutorial
ms.date: 06/26/2020
ms.author: spelluru
ms.openlocfilehash: d9338bc746158802c86b9631323f3523d01714ce
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128561313"
---
# <a name="tutorial-access-a-lab-in-azure-devtest-labs"></a>Tutorial: Zugreifen auf ein Lab in Azure DevTest Labs
In diesem Tutorial verwenden Sie das Lab, das im Tutorial [Erstellen eines benutzerdefinierten Labs mithilfe von Azure DevTest Labs](tutorial-create-custom-lab.md) erstellt wurde.

In diesem Tutorial führen Sie die folgenden Aktionen aus:

> [!div class="checklist"]
> * Beanspruchen eines virtuellen Computers (virtual machine, VM) im Lab
> * Herstellen der Verbindung zur VM
> * Aufheben des Anspruchs auf den virtuellen Computer

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen.

## <a name="access-the-lab"></a>Zugreifen auf das Lab

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Wählen Sie im linken Menü die Option **Alle Ressourcen**. 
3. Wählen Sie **DevTest Labs** als Ressourcentyp aus. 
4. Wählen Sie das Lab aus. 

    ![Auswählen des Labs](./media/tutorial-use-custom-lab/search-for-select-custom-lab.png)

## <a name="claim-a-vm"></a>Beanspruchen eines virtuellen Computers

1. Klicken Sie in der Liste **Abrufbare virtuelle Computer** auf die Schaltfläche mit den drei Auslassungspunkten (**...**), und wählen Sie **Computer beanspruchen** aus.

    ![Beanspruchen eines virtuellen Computers](./media/tutorial-use-custom-lab/claim-virtual-machine.png)
1. Vergewissern Sie sich, dass der virtuelle Computer in der Liste **Meine virtuellen Computer** angezeigt wird.

    ![Mein virtueller Computer](./media/tutorial-use-custom-lab/my-virtual-machines.png)

## <a name="connect-to-the-vm"></a>Herstellen der Verbindung zur VM

1. Wählen Sie Ihren virtuellen Computer aus der Liste aus. Die Seite **Virtueller Computer** wird angezeigt. Klicken Sie in der Symbolleiste auf **Verbinden**.

    ![Herstellen der Verbindung mit dem virtuellen Computer](./media/tutorial-use-custom-lab/connect-button.png)
2. Speichern Sie die heruntergeladene **RDP**-Datei auf Ihrer Festplatte, und verwenden Sie sie, um eine Verbindung mit dem virtuellen Computer herzustellen. Geben Sie den Benutzernamen und das Kennwort an, den bzw. das Sie beim Erstellen des virtuellen Computers im vorigen Abschnitt angegeben haben. 

    Für die VM muss SSH- bzw. RDP-Zugriff aktiviert sein, um eine Verbindung mit einer Linux-VM herzustellen. Schritte zum Herstellen einer Verbindung mit einer Linux-VM per RDP finden Sie unter [Installieren und Konfigurieren von Remotedesktop zum Herstellen einer Verbindung mit einem virtuellen Linux-Computer in Azure](../virtual-machines/linux/use-remote-desktop.md). 

    > [!NOTE]
    > Es gibt andere Möglichkeiten, zur Seite „Virtueller Computer“ für Ihren virtuellen Computer zu gelangen. Hier sind einige davon: 
    > 
    > 1. Suchen Sie nach allen VMs in Ihrem Abonnement. Wählen Sie Ihre VM in der Liste der virtuellen Computer aus, um zur Seite **Virtueller Computer** zu gelangen.
    > 2. Navigieren Sie zur Seite **Ressourcengruppe** für die Ressourcengruppe. Wählen Sie dann Ihre VM aus der Liste der Ressourcen in der Ressourcengruppe aus, um zur Seite **Virtueller Computer** zu gelangen. 
    >
    > Verwenden Sie nicht die Schaltfläche **Verbinden** auf der Symbolleiste der Seite **Virtueller Computer**, zu der Sie mithilfe dieser Optionen gelangen. Navigieren Sie stattdessen von der Seite **DevTest Labs**, wie in diesem Artikel gezeigt, zur Seite **Virtueller Computer**, und verwenden Sie dann die Schaltfläche **Verbinden** auf der Symbolleiste.


## <a name="unclaim-the-vm"></a>Aufheben des Anspruchs auf den virtuellen Computer
Wenn Sie den virtuellen Computer nicht benötigen, führen Sie folgende Schritte aus, um den Anspruch auf den virtuellen Computer aufzuheben: 

1. Wählen Sie auf der Seite des virtuellen Computers auf der Symbolleiste die Option **Anspruch aufheben** aus. 

    ![Aufheben des Anspruchs auf den virtuellen Computer](./media/tutorial-use-custom-lab/unclaim-vm-menu.png)
1. Der virtuelle Computer wird heruntergefahren, bevor der Anspruch aufgehoben wird. Der Status dieses Vorgangs wird in den Benachrichtigungen angezeigt.  
3. Navigieren Sie wieder zur DevTest Lab-Seite, indem Sie im Breadcrumb-Menü im oberen Bereich auf den Namen Ihres Labs klicken. 
    
    ![Navigieren zum Lab](./media/tutorial-use-custom-lab/breadcrumb-to-lab.png)
1. Vergewissern Sie sich im unteren Bereich, dass der virtuelle Computer in der Liste **Abrufbare virtuelle Computer** enthalten ist.

    
## <a name="next-steps"></a>Nächste Schritte
In diesem Tutorial wurde gezeigt, wie Sie auf ein Lab zugreifen, das unter Verwendung von Azure DevTest Labs erstellt wurde, und wie Sie dieses Lab verwenden. Weitere Informationen zum Zugriff auf virtuelle Computer in einem Lab sowie zu deren Verwendung finden Sie hier: 

> [!div class="nextstepaction"]
> [Hinzufügen eines virtuellen Computers zu einem Lab in Azure DevTest Labs](devtest-lab-add-vm.md)
