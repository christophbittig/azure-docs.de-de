---
title: Zugriff auf ein Lab
description: In diesem Tutorial greifen Sie auf ein Lab in Azure DevTest Labs zu. Sie verwenden einen virtuellen Computer, heben den Anspruch auf ihn auf und beanspruchen ihn dann.
ms.topic: tutorial
ms.date: 11/03/2021
ms.author: spelluru
ms.openlocfilehash: d5ace346c8f4b853e862b2e12b8b2adee55bab8c
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131578538"
---
# <a name="tutorial-access-a-lab-in-azure-devtest-labs"></a>Tutorial: Zugreifen auf ein Lab in Azure DevTest Labs

In diesem Tutorial verwenden Sie das Lab, das unter [Tutorial: Erstellen eines Labs mithilfe von Azure DevTest Labs](tutorial-create-custom-lab.md) erstellt wurde.

In diesem Tutorial führen Sie die folgenden Aktionen aus:

> [!div class="checklist"]
> * Herstellen einer Verbindung mit dem virtuellen Labcomputer
> * Aufheben des Anspruchs auf den virtuellen Labcomputer
> * Beanspruchen des virtuellen Labcomputers

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Ein [Lab in DevTest Labs mit einem virtuellen Azure-Computer](tutorial-create-custom-lab.md)

## <a name="connect-to-the-lab-vm"></a>Herstellen einer Verbindung mit dem virtuellen Labcomputer

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Navigieren Sie in **DevTest Labs** zu Ihrem Lab.

1. Wählen Sie unter **Meine virtuellen Computer** Ihren virtuellen Computer aus.

    :::image type="content" source="./media/tutorial-use-custom-lab/my-virtual-machines.png" alt-text="Screenshot: Virtueller Computer unter „Meine virtuellen Computer“":::

1. Wählen Sie im oberen Menü **Verbinden** aus. Wählen Sie dann die Datei `.rdp` aus, die auf Ihren Computer heruntergeladen wird.

    :::image type="content" source="./media/tutorial-use-custom-lab/vm-connect.png" alt-text="Screenshot: Schaltfläche „Verbinden“ für den virtuellen Computer":::

1. Wählen Sie im Dialogfeld **Remotedesktopverbindung** die Option **Verbinden** aus.

1. Geben Sie im Dialogfeld **Geben Sie Ihre Anmeldeinformationen ein** das Kennwort ein, und wählen Sie dann **OK** aus.

1. Wenn ein Dialogfeld mit dem Hinweis **Die Identität des Remotecomputers kann nicht überprüft werden.** angezeigt wird, aktivieren Sie das Kontrollkästchen **Nicht erneut nach Verbindungen mit diesem Computer fragen**. Wählen Sie dann **Ja** aus.

    :::image type="content" source="./media/tutorial-use-custom-lab/remote-computer-verification.png" alt-text="Screenshot: Remotecomputerüberprüfung":::

Schritte zum Herstellen einer Verbindung mit einem virtuellen Linux-Computer finden Sie unter [Installieren und Konfigurieren von xrdp für die Verwendung von Remotedesktop mit Ubuntu](../virtual-machines/linux/use-remote-desktop.md). 

## <a name="unclaim-the-lab-vm"></a>Aufheben des Anspruchs auf den virtuellen Labcomputer

Wenn Sie den virtuellen Computer nicht mehr benötigen, führen Sie folgende Schritte aus, um den Anspruch auf den virtuellen Computer aufzuheben: 

1. Wählen Sie Ihren virtuellen Computer in DevTest Labs mit den gleichen Schritten wie zuvor aus.

1. Wählen Sie auf der Seite des **virtuellen Computers** im oberen Menü die Option **Anspruch aufheben** aus. 

    :::image type="content" source="./media/tutorial-use-custom-lab/virtual-machine-unclaim.png" alt-text="Screenshot: Option „Anspruch aufheben“":::

1. Der virtuelle Computer wird heruntergefahren, bevor der Anspruch aufgehoben wird. Der Status dieses Vorgangs kann unter **Benachrichtigungen** überwacht werden.

1. Schließen Sie die Seite des **virtuellen Computers**, um zurück zur Seite **Übersicht** von DevTest Lab zu gelangen.

1. Wählen Sie unter **Mein Lab** die Option **Abrufbare virtuelle Computer** aus. Der virtuelle Computer kann jetzt in Anspruch genommen werden.

    :::image type="content" source="./media/tutorial-use-custom-lab/claimable-virtual-machines.png" alt-text="Screenshot: Optionen unter „Abrufbare virtuelle Computer“":::

## <a name="claim-a-lab-vm"></a>Beanspruchen eines virtuellen Labcomputers

Sie können den virtuellen Computer erneut beanspruchen, wenn Sie ihn verwenden müssen.

1. Klicken Sie in der Liste **Abrufbare virtuelle Computer** auf die Schaltfläche mit den drei Auslassungspunkten (**...**), und wählen Sie **Computer beanspruchen** aus.

    :::image type="content" source="./media/tutorial-use-custom-lab/claimable-virtual-machines-claimed.png" alt-text="Screenshot: Option „Beanspruchen“":::

1. Vergewissern Sie sich, dass der virtuelle Computer in der Liste **Meine virtuellen Computer** angezeigt wird.

    :::image type="content" source="./media/tutorial-use-custom-lab/my-virtual-machines-2.png" alt-text="Screenshot: Virtueller Computer unter „Meine virtuellen Computer“":::

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie Ressourcen, um Gebühren für die Ausführung des Labs und des virtuellen Computers in Azure zu vermeiden. Wenn Sie das nächste Tutorial durcharbeiten möchten, um auf den virtuellen Computer im Lab zuzugreifen, können Sie die Ressourcen nach Abschluss dieses Tutorials bereinigen. Führen Sie andernfalls die folgenden Schritte aus: 

1. Kehren Sie zur Startseite für das Lab zurück, das Sie erstellt haben.

1. Wählen im oberen Menü **Löschen** aus.

   :::image type="content" source="./media/tutorial-use-custom-lab/portal-lab-delete.png" alt-text="Screenshot: Schaltfläche „Löschen“ für das Lab":::

1. Geben Sie auf der Seite **Möchten Sie den Löschvorgang durchführen?** den Lab-Namen in das Textfeld ein, und wählen Sie dann **Löschen** aus.

1. Während des Löschvorgangs können Sie oben auf dem Bildschirm **Benachrichtigungen** auswählen, um den Status anzuzeigen. Das Löschen des Labs dauert eine Weile. Fahren Sie mit dem nächsten Schritt fort, nachdem das Lab gelöscht wurde.

1. Wenn Sie das Lab in einer vorhandenen Ressourcengruppe erstellt haben, wurden alle Lab-Ressourcen entfernt. Wenn Sie eine neue Ressourcengruppe für dieses Tutorial erstellt haben, ist diese jetzt leer und kann gelöscht werden. Zuvor wäre es nicht möglich gewesen, die Ressourcengruppe zu löschen, während sich das Lab noch in ihr befand.
    
## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie gelernt, wie Sie auf ein Lab in Azure DevTest Labs zugreifen und es verwenden. Weitere Informationen zum Zugriff auf virtuelle Computer in einem Lab sowie zu deren Verwendung finden Sie hier:

> [!div class="nextstepaction"]
> [Hinzufügen eines virtuellen Computers zu einem Lab in Azure DevTest Labs](devtest-lab-add-vm.md)
