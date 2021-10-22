---
title: Herstellen einer Verbindung mit einer Azure Lab Services-VM über ein Chromebook | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie über ein Chromebook eine Verbindung mit einem virtuellen Computer in Azure Lab Services herstellen.
services: devtest-lab, lab-services, virtual-machines
author: nicolela
ms.topic: how-to
ms.date: 06/26/2020
ms.author: nicolela
ms.openlocfilehash: 7fb33b580aa0984366b47f0358df7f915a77730b
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/19/2021
ms.locfileid: "130176147"
---
# <a name="connect-to-a-vm-using-remote-desktop-protocol-on-a-chromebook"></a>Herstellen einer Verbindung mit einem virtuellen Computer mithilfe des Remotedesktopprotokolls auf einem Chromebook

In diesem Abschnitt wird gezeigt, wie ein Kursteilnehmer über ein Chromebook eine RDP-Verbindung mit einem virtuellen Classroom-Lab-Computer herstellen kann.

## <a name="install-microsoft-remote-desktop-on-a-chromebook"></a>Installieren von Microsoft-Remotedesktop auf einem Chromebook

1. Öffnen Sie den App Store auf Ihrem Chromebook, und suchen Sie nach **Microsoft-Remotedesktop**.

    ![Microsoft-Remotedesktop](./media/how-to-use-classroom-lab/install-ms-remote-desktop-chromebook.png)
    
1. Installieren Sie die neueste Version von Microsoft-Remotedesktop. 

## <a name="access-the-vm-from-your-chromebook-using-rdp"></a>Zugreifen auf den virtuellen Computer über Ihr Chromebook per RDP

1. Öffnen Sie die auf Ihren Computer heruntergeladene **RDP**-Datei. (Auf dem Computer muss **Microsoft-Remotedesktop** installiert sein.) Daraufhin sollte eine Verbindung mit dem virtuellen Computer hergestellt werden. 

    ![Herstellen einer Verbindung mit dem virtuellen Computer](./media/how-to-use-classroom-lab/connect-vm-chromebook.png)

1. Geben Sie nach entsprechender Aufforderung Ihr Kennwort ein.

    ![Screenshot: Anmeldebildschirm, in dem Sie Ihren Benutzernamen und Ihr Kennwort eingeben.](./media/how-to-use-classroom-lab/password-chromebook.png)

1. Wählen Sie **Weiter** aus, wenn Sie die folgende Warnung erhalten. 

    ![Zertifikatwarnung](./media/how-to-use-classroom-lab/certificate-error-chromebook.png)

1. Der Desktop des virtuellen Computers, mit dem Sie eine Verbindung herstellen, sollte angezeigt werden.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Herstellen einer Verbindung mit virtuellen Linux-Computern finden Sie unter [Verwenden von Remotedesktop für virtuelle Linux-Computer in einem Classroom-Lab in Azure Lab Services](how-to-use-remote-desktop-linux-student.md).

