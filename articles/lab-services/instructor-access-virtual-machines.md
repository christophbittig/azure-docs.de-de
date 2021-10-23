---
title: Zugriff auf VMs in Azure Lab Services durch einen Lehrer/Dozenten
description: In diesem Artikel wird erläutert, wie Lehrer/Dozenten über die Lehrer-/Dozentenansicht auf ihre Kursteilnehmer-VMs zugreifen können. Ein Tutor kann z. B. ein Lehrer/Dozent für eine Klasse, aber ein Kursteilnehmer in anderen Klassen sein.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: d20d95f7f86e3116d9daf42417a3a0aae5855bbb
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/19/2021
ms.locfileid: "130179638"
---
# <a name="access-virtual-machines-as-a-student-from-the-educator-view"></a>Zugreifen auf virtuelle Computer als Kursteilnehmer in der Lehrer-/Dozentenansicht
In diesem Artikel wird gezeigt, wie Lehrer/Dozenten auf ihre VMs für Klassen zugreifen können, die sie als Kursteilnehmer besuchen. 

Im Folgenden finden Sie ein Szenario, in dem dieses Feature hilfreich ist. Ein Tutor ist ein Lehrer/Dozent für eine Klasse, aber ein Kursteilnehmer in anderen Klassen. Und der Tutor möchte die Kursteilnehmer-VMs in der Lehrer-/Dozentenansicht, in der die Labs in seinem Besitz aufgeführt werden, anzeigen und auf sie zugreifen. 

## <a name="access-vms-from-educator-view"></a>Zugreifen auf virtuelle Computer über die Lehrer-/Dozentenansicht

1. Melden Sie sich bei der [Azure Lab Services-Website](https://labs.azure.com) an. Sie sehen die Labs in Ihrem Besitz. Dies können Labs sein, die Sie selbst erstellt haben, oder Labs, die der Administrator Ihnen als Besitzer zugewiesen hat. Weitere Informationen finden Sie unter [Hinzufügen zusätzlicher Besitzer zu einem vorhandenen Lab](how-to-add-user-lab-owner.md).
2. Wählen Sie das Computersymbol in der rechten oberen Ecke aus, um auf VMs für Klassen zuzugreifen, die Sie als Kursteilnehmer besuchen. Vergewissern Sie sich, dass VMs angezeigt werden, auf die Sie als Kursteilnehmer zugreifen können. Im folgenden Beispiel ist der Benutzer ein Tutor für das Python-Lab, aber ein Kursteilnehmer im Java-Lab. Der Benutzer sieht daher die VM aus dem Java-Lab in der Dropdownliste. Der Benutzer kann die VM starten und eine Verbindung mit ihr herstellen. 
    
    ![Zugreifen auf Kursteilnehmer-VMs](./media/instructors-access-virtual-machines/access-student-virtual-machines.png)

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie in folgenden Artikeln:

- [Herstellen einer Verbindung mit einem virtuellen Computer](how-to-use-classroom-lab.md#connect-to-the-vm)
- [Herstellen einer RDP-Verbindung mit einem virtuellen Computer auf einem Mac](connect-virtual-machine-mac-remote-desktop.md)
- [Herstellen einer Verbindung zu einer VM mithilfe des Remotedesktopprotokolls auf einem Chromebook](connect-virtual-machine-chromebook-remote-desktop.md)
- [Verwenden von Remotedesktop für virtuelle Linux-Computer](how-to-use-remote-desktop-linux-student.md)
