---
title: Konfigurieren der Einstellungen von Azure Marketplace-Images
description: Konfigurieren Sie, welche Azure Marketplace-Images beim Erstellen eines virtuellen Computers in Azure DevTest Labs verwendet werden können.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: a42326662758e045e8a36d6de1ec2b7170b8d1cd
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128645016"
---
# <a name="configure-azure-marketplace-image-settings-in-azure-devtest-labs"></a>Konfigurieren von Azure Marketplace-Image-Einstellungen in Azure DevTest Labs
DevTest Labs unterstützt die Erstellung von virtuellen Computern (VMs, Virtual Machines) auf Basis von Azure Marketplace-Images, abhängig davon, wie Sie Azure Marketplace-Images zur Verwendung in Ihrem Lab konfiguriert haben. In diesem Artikel erfahren Sie, wie Sie ggf. angeben, welche Azure Marketplace-Images zum Erstellen virtueller Computer in einem Lab verwendet werden können. Hierdurch wird sichergestellt, dass das Team nur Zugriff auf die Marketplace-Images hat, die es benötigt. 

## <a name="specify-allowed-images-for-creating-vms"></a>Angeben von zulässigen Images für die Erstellung von VMs
Führen Sie diese Schritte aus, um anzugeben, welche Azure Marketplace-Images beim Erstellen einer VM zulässig sind. 

1. Melden Sie sich beim [Azure-Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) an.
2. Wählen Sie **Alle Dienste** und dann in der Liste die Option **DevTest Labs**.
3. Wählen Sie in der Liste mit den Labs Ihr Lab aus. 
4. Wählen Sie auf der Startseite des Labs die Option **Konfiguration und Richtlinien** aus.
5. Wählen Sie auf der Seite **Konfiguration und Richtlinien** des Labs unter **Basen virtueller Computer** die Option **Marketplace-Images** aus.
6. Geben Sie an, ob alle qualifizierten Azure Marketplace-Images für die Verwendung als Basis einer neuen VM verfügbar sein sollen. Bei Auswahl von **Ja** sind alle Azure Marketplace-Images im Lab zulässig, die alle folgenden Kriterien erfüllen:
   
   * Das Image erstellt einen einzelnen virtuellen Computer, **und**
   * Das Image verwendet den Azure-Resource-Manager zur Bereitstellung von VMs, **und**
   * Das Image erfordert nicht den Erwerb eines zusätzlichen Lizenzplans.
     
     Wenn keine Images zulässig sein sollen, oder Sie angeben möchten, welche Bilder verwendet werden können, wählen Sie **Nein**.
     
     ![Option, die Verwendung aller Marketplace-Images als Basis-Images für virtuelle Computer zuzulassen](./media/devtest-lab-configure-marketplace-images/allow-all-marketplace-images.png)
7. Bei Auswahl von **Nein** im vorherigen Schritt ist das Kontrollkästchen **Allowed images/Select all** (Zulässige Images/Alle auswählen) aktiviert. 
   Sie können diese Option zusammen mit dem Suchfeld verwenden, um schnell alle in der Liste angezeigten Elemente auszuwählen oder ihre Auswahl aufzuheben.
   * Wählen Sie die Azure Marketplace-Images aus, die Sie für die Erstellung des virtuellen Computers zulassen möchten, indem Sie das Kontrollkästchen jedes entsprechenden Images aktivieren.
   * Wählen Sie nichts aus der Liste, wenn keine Azure Marketplace-Images im Lab verwendet werden sollen.
   
     ![Sie können angeben, welche Azure Marketplace-Images als Basis-Images für virtuelle Computer verwendet werden können.](./media/devtest-lab-configure-marketplace-images/select-marketplace-images.png)


## <a name="troubleshoot"></a>Problembehandlung
Führen Sie die folgenden Schritte aus, falls Sie das gewünschte Image, das Sie für das Lab aktivieren möchten, nicht finden: 

- Überprüfen Sie, ob Sie das Image beim Erstellen einer Compute-VM anzeigen können.
- Es kann sein, dass das Image unter dem von Ihnen verwendeten Abonnementtyp nicht verfügbar ist. Fragen Sie den Abonnementadministrator nach dem Abonnementtyp (z. B. MSDN, Free, Nutzungsbasierte Bezahlung usw.). 
- Die Unterstützung für Gen2-Images in DevTest Labs ist eingeschränkt. Wenn Gen1- und Gen2-Images verfügbar sind, wird in DevTest Labs nur das Gen1-Image angezeigt, wenn Sie eine VM erstellen. Die Problemumgehung besteht darin, außerhalb des Labs ein benutzerdefiniertes Gen2-Image zu erstellen und für die Erstellung einer VM zu verwenden. Falls nur die Gen2-Version des Images verfügbar ist, wird sie von DevTest Labs unterstützt und in der Liste angezeigt. 
      


## <a name="next-steps"></a>Nächste Schritte
Nachdem Sie konfiguriert haben, auf welche Weise Azure Marketplace-Images beim Erstellen eines virtuellen Computers zulässig sind, ist der nächste Schritt das [Hinzufügen eines virtuellen Computers zu Ihrem Lab](devtest-lab-add-vm.md).
