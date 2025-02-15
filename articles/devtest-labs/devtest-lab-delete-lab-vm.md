---
title: Löschen eines Labs oder eines virtuellen Computers in einem Lab
description: In diesem Artikel erfahren Sie, wie Sie mithilfe des Azure-Portals (Azure DevTest Labs) ein Lab oder eine VM in einem Lab löschen.
ms.topic: how-to
ms.date: 01/24/2020
ms.openlocfilehash: af8a1691bbd0f34647b7e52a8f05b7acffb86d2b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128616363"
---
# <a name="delete-a-lab-or-vm-in-a-lab-in-azure-devtest-labs"></a>Löschen eines Labs oder eines virtuellen Computers in einem Lab in Azure DevTest Labs
In diesem Artikel wird veranschaulicht, wie Sie ein Lab oder eine VM in einem Lab löschen.

## <a name="delete-a-lab"></a>Löschen eines Labs
Wenn Sie eine DevTest Labs-Instanz aus einer Ressourcengruppe löschen, führt der DevTest Labs-Dienst die folgenden Aktionen durch: 

- Alle Ressourcen, die beim Erstellen des Labs automatisch erstellt wurden, werden automatisch gelöscht. Die Ressourcengruppe selbst wird nicht gelöscht. Wenn Sie in dieser Ressourcengruppe manuell Ressourcen erstellt haben, werden sie vom Dienst nicht gelöscht. 
- Alle VMs im Lab und die den VMs zugeordneten Ressourcengruppen werden automatisch gelöscht. Wenn Sie eine VM in einem Lab erstellen, erstellt der Dienst Ressourcen (Datenträger, Netzwerkschnittstelle, öffentliche IP-Adresse usw.) für die VM in einer separaten Ressourcengruppe. Aber wenn Sie in diesen Ressourcengruppen manuell zusätzliche Ressourcen erstellen, werden diese Ressourcen und die Ressourcengruppe vom DevTest Labs-Dienst nicht gelöscht. 

Führen Sie die folgenden Aktionen durch, um ein Lab zu löschen: 

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Wählen Sie im Menü auf der linken Seite die Option **Alle Ressourcen**, als Diensttyp die Option **DevTest Labs** und dann das Lab aus.

    ![Auswählen Ihres Labs](media/devtest-lab-delete-lab-vm/select-lab.png)
3. Klicken Sie auf der Seite **DevTest Lab** in der Symbolleiste auf **Löschen**. 

    ![Schaltfläche „Löschen“](media/devtest-lab-delete-lab-vm/delete-button.png)
4. Geben Sie auf der Seite **Bestätigung** den **Namen** Ihres Labs ein, und wählen Sie **Löschen**. 

    ![Confirm](media/devtest-lab-delete-lab-vm/confirm-delete.png)
5. Wählen Sie zum Anzeigen des Vorgangsstatus das Symbol **Benachrichtigungen** (Glocke). 

    ![Benachrichtigungen](media/devtest-lab-delete-lab-vm/delete-status.png)

 
## <a name="delete-a-vm-in-a-lab"></a>Löschen einer VM in einem Lab
Wenn ich einen virtuellen Computer (VM) in einem Lab lösche, werden einige Ressourcen (nicht alle) gelöscht, die bei der Erstellung des Labs erstellt wurden. Die folgenden Ressourcen werden nicht gelöscht: 

-   Schlüsseltresor in der Hauptressourcengruppe
-   Verfügbarkeitsgruppe, Lastenausgleich und öffentliche IP-Adresse in der VM-Ressourcengruppe. Diese Ressourcen werden von mehreren VMs in einer Ressourcengruppe gemeinsam genutzt. 

Der virtuelle Computer, die Netzwerkschnittstelle und der Datenträger der VM werden gelöscht. 

Führen Sie die folgenden Aktionen durch, um eine VM in einem Lab zu löschen: 

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Wählen Sie im Menü auf der linken Seite die Option **Alle Ressourcen**, als Diensttyp die Option **DevTest Labs** und dann das Lab aus.

    ![Auswählen Ihres Labs](media/devtest-lab-delete-lab-vm/select-lab.png)
3. Wählen Sie in der Liste mit den VMs **...** (Auslassungspunkte) für die jeweilige VM und dann **Löschen**. 

    ![Löschen einer VM im Menü](media/devtest-lab-delete-lab-vm/delete-vm-menu-in-list.png)
4. Wählen Sie im **Bestätigungsdialogfeld****OK**. 
5. Wählen Sie zum Anzeigen des Vorgangsstatus das Symbol **Benachrichtigungen** (Glocke). 

Wählen Sie in der Symbolleiste die Option **Löschen**, wie in der folgenden Abbildung gezeigt, um eine VM auf der Seite **Virtueller Computer** zu löschen:

![Löschen einer VM auf der VM-Seite](media/devtest-lab-delete-lab-vm/delete-from-vm-page.png) 


## <a name="next-steps"></a>Nächste Schritte
Informationen zur Erstellung eines Labs finden Sie in den folgenden Artikeln: 

- [Erstellen eines Labs](devtest-lab-create-lab.md)
- [Hinzufügen einer VM zum Lab](devtest-lab-add-vm.md)