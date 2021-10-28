---
title: Aktivieren eines lizenzierten Images in Ihrem Lab
description: Erfahren Sie, wie Sie mit dem Azure-Portal ein lizenziertes Image in Azure DevTest Labs aktivieren.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: c7f0ce7998d4c77596bb04559465135015da9846
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130254931"
---
# <a name="enable-a-licensed-image-in-your-lab-in-azure-devtest-labs"></a>Aktivieren eines lizenzierten Image im Lab in Azure DevTest Labs

Ein lizenziertes Image in Azure DevTest Labs beinhaltet Geschäftsbedingungen (in der Regel von einem Drittanbieter), die akzeptiert werden müssen, bevor Benutzer im Lab auf das Image zugreifen können. In den folgenden Abschnitten wird beschrieben, wie lizenzierte Images für die Erstellung virtueller Computer zur Verfügung gestellt werden.

## <a name="determining-whether-a-licensed-image-is-available-to-users"></a>Bestimmen der Verfügbarkeit eines lizenzierten Image für Benutzer
Der erste Schritt, um Benutzern die Erstellung von VMs anhand eines lizenzierten Image zu ermöglichen, besteht darin, sicherzustellen, dass die Geschäftsbedingungen für das lizenzierte Image akzeptiert wurden. Die folgenden Schritte zeigen, wie Sie den Angebotsstatus eines lizenzierten Image einsehen und ggf. die Geschäftsbedingungen akzeptieren können.

1. Melden Sie sich beim [Azure-Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) an.

1. Wählen Sie **Alle Dienste** und dann in der Liste die Option **DevTest Labs**.

1. Wählen Sie in der Liste der Labs das gewünschte Lab aus.  

1. Wählen Sie im linken Bereich unter **EINSTELLUNGEN** die Option **Konfiguration und Richtlinien** aus.

1. Wählen Sie im linken Bereich unter **BASEN VIRTUELLER COMPUTER** die Option **Marketplace-Images** aus. 

    ![Menüelement „Marketplace-Images“](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-marketplace-images.png)

    Eine Liste aller verfügbaren Marketplace-Images wird angezeigt, einschließlich des **ANGEBOTSSTATUS** von jedem Image.

    ![Liste der Marketplace-Images mit Angebotsstatus von jedem Image](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-offer-status.png)

    Ein lizenziertes Image zeigt den Angebotsstatus von Folgendem an: 
    
    - **Nutzungsbedingungen akzeptiert**: Das lizenzierte Image ist für Benutzer zum Erstellen von VMs verfügbar. 
    - **Überprüfung der Nutzungsbedingungen erforderlich**: Das lizenzierte Image ist zurzeit nicht für Benutzer verfügbar. Die Lizenzbedingungen müssen akzeptiert werden, bevor Labbenutzer mithilfe des Image VMs erstellen können. 

## <a name="making-a-licensed-image-available-to-lab-users"></a>Bereitstellen eines lizenzierten Image für Labbenutzer
Um sicherzustellen, dass ein lizenziertes Image für Labbenutzer verfügbar ist, muss ein Labbesitzer mit Administratorrechten zuerst die Geschäftsbedingungen dieses lizenzierten Image akzeptieren. Durch die Aktivierung der programmgesteuerten Bereitstellung für das Abonnement, das einem lizenzierten Image zugeordnet ist, werden automatisch die rechtlichen Bedingungen und Datenschutzbestimmungen für dieses Image akzeptiert. Die Seite [Arbeiten mit Marketplace-Images im Azure Resource Manager](https://azure.microsoft.com/blog/working-with-marketplace-images-on-azure-resource-manager/) enthält zusätzliche Informationen über die programmgesteuerte Bereitstellung von Marketplace-Images.

Sie können die programmgesteuerte Bereitstellung für ein lizenziertes Image anhand der folgenden Schritte aktivieren:

1. Navigieren Sie im [Azure-Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) zur Liste **Marketplace-Images**.

1. Ermitteln Sie ein lizenziertes Image, auf das Benutzer Zugriff haben sollen, deren Bedingungen jedoch nicht akzeptiert wurden. In diesem Fall wird beispielsweise eventuell eine Data Science Virtual Machine (DSVM) mit dem Status **Bedingungen akzeptiert** oder **Überprüfung der Nutzungsbedingungen erforderlich** angezeigt.

    ![Screenshot, der Data Science Virtual Machine-Images mit dem Angebotsstatus „Bedingungen akzeptiert“ und „Überprüfung der Nutzungsbedingungen erforderlich“ anzeigt.](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-licensed-images.png)

   > [!NOTE]
   > Data Science VMs sind Azure-VM-Images, die mit verschiedenen beliebten Tools vorinstalliert, konfiguriert und getestet wurden. Diese Images werden häufig in den Bereichen Datenanalyse, maschinelles Lernen und KI-Training eingesetzt. Der Artikel [Einführung in Azure Data Science Virtual Machine für Linux und Windows](../machine-learning/data-science-virtual-machine/overview.md) enthält ausführliche Informationen über DSVMs.
   >
   >

1. Wählen Sie in der Spalte **ANGEBOTSSTATUS** des Image **Überprüfung der Nutzungsbedingungen erforderlich** aus.

1. Wählen Sie im Fenster „Programmgesteuerte Bereitstellung konfigurieren“ die Option **Aktivieren** aus.

    ![Fenster „Programmgesteuerte Bereitstellung konfigurieren“](./media/devtest-lab-create-custom-image-from-licensed-image/devtest-lab-enable-programmatic-deployment.png)

   > [!IMPORTANT]
   > Im Fenster „Programmgesteuerte Bereitstellung konfigurieren“ werden u.U. mehrere Abonnements aufgeführt. Aktivieren Sie die programmgesteuerte Bereitstellung nur für das vorgesehene Abonnement.
   >
   >


1. Wählen Sie **Speichern** aus. 

    In der Liste der Marketplace-Images weist dieses Image nun den Status **Nutzungsbedingungen akzeptiert** auf und steht für Benutzer zum Erstellen virtueller Computer zur Verfügung.

> [!NOTE]
> Benutzer können ein benutzerdefiniertes Image anhand eines lizenzierten Image erstellen. Weitere Informationen finden Sie unter [Erstellen eines benutzerdefinierten Images aus einer VHD-Datei](devtest-lab-create-template.md).
>
>


## <a name="related-blog-posts"></a>Verwandte Blogbeiträge

- [Custom images or formulas? (Benutzerdefinierte Images oder Formeln?)](./devtest-lab-faq.yml#blog-post)
- [Copying Custom Images between Azure DevTest Labs (Kopieren benutzerdefinierter Images zwischen Azure DevTest Labs)](https://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>Nächste Schritte

- [Erstellen eines benutzerdefinierten Images von einem virtuellen Computer](devtest-lab-create-custom-image-from-vm-using-portal.md)
- [Erstellen eines benutzerdefinierten Images aus einer VHD-Datei mithilfe von PowerShell](devtest-lab-create-template.md)
- [Hinzufügen eines virtuellen Computers zum Lab](devtest-lab-add-vm.md)