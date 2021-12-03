---
title: Grundlegendes zu freigegebenen IP-Adressen
description: Erfahren Sie, wie Azure DevTest Labs freigegebene IP-Adressen verwendet, um die Anzahl von öffentlichen IP-Adressen zu minimieren, die für den Zugriff auf die VMs Ihres Labs erforderlich sind.
ms.topic: how-to
ms.date: 11/08/2021
ms.openlocfilehash: 06aac18fb7016a7eb5bee938a4d9988719f86a2f
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132056913"
---
# <a name="understand-shared-ip-addresses-in-azure-devtest-labs"></a>Grundlegendes zu freigegebenen IP-Adressen in Azure DevTest Labs

Azure DevTest Labs-VMs können eine öffentliche IP-Adresse gemeinsam nutzen, um die Anzahl der öffentlichen IP-Adressen zu minimieren, die Sie für den Zugriff auf Lab-VMs benötigen.  In diesem Artikel wird beschrieben, wie freigegebene IP-Adressen funktionieren und wie Sie freigegebene IP-Adressen konfigurieren.

## <a name="shared-ip-settings"></a>Einstellung für freigegebene IP-Adressen

Sie erstellen ein DevTest Labs-Lab in einem virtuellen Netzwerk, das über mindestens ein Subnetz verfügen kann. Für das Standardsubnetz ist **Freigegebene öffentliche IP-Adresse aktivieren** auf **Ja** festgelegt.  Diese Konfiguration erstellt eine einzige öffentliche IP-Adresse für das gesamte Subnetz. Alle VMs in diesem Subnetz verwenden standardmäßig die freigegebene IP-Adresse.

Weitere Informationen zum Konfigurieren virtueller Netzwerke und Subnetze finden Sie unter [Konfigurieren eines virtuellen Netzwerks in Azure DevTest Labs](devtest-lab-configure-vnet.md).

![Screenshot: Einstellung „Freigegebene IP-Adresse“ auf der Seite „Lab-Subnetz“.](media/devtest-lab-shared-ip/lab-subnet.png)

Für vorhandene Labs können Sie diese Option aktivieren oder festlegen, indem Sie im linken Navigationsbereich des Labs **Konfiguration und Richtlinien** und dann unter **Externe Ressourcen** die Option **Virtuelle Netzwerke** auswählen. Wählen Sie ein virtuelles Netzwerk aus der Liste aus, um die Einstellungen für freigegebene IP-Adressen für seine Subnetze anzuzeigen.

Um die Einstellung zu ändern, wählen Sie ein Subnetz aus der Liste aus, und ändern Sie dann **Freigegebene öffentliche IP-Adresse aktivieren** in **Ja** oder **Nein**.

Wenn Sie eine VM erstellen, können Sie auf der Seite **Erweiterte Einstellungen** neben **IP-Adresse** auf diese Einstellung zugreifen.

![Screenshot: Einstellung „Freigegebene IP-Adresse“ unter „Erweiterte Einstellungen“ beim Erstellen einer neuen VM.](media/devtest-lab-shared-ip/new-vm.png)

- **Freigegeben**: Alle VMs, die Sie als **Freigegeben** erstellen, gehören derselben Ressourcengruppe an. Der Ressourcengruppe ist eine IP-Adresse zugewiesen, die von allen VMs in der Ressourcengruppe verwendet wird.
- **Öffentlich**: Jede öffentliche VM verfügt über eine eigene IP-Adresse und Ressourcengruppe.
- **Privat**: Jede private VM verwendet eine private IP-Adresse. Sie können keine Verbindung mit diesen VMs über das Internet herstellen, indem Sie Remotedesktopprotokoll (RDP) verwenden.

Wenn Sie einem Subnetz eine VM mit freigegebener IP-Adresse hinzufügen, fügt DevTest Labs die VM automatisch einem Lastenausgleich hinzu und weist der VM eine TCP-Portnummer für die öffentliche IP-Adresse zu. Die Portnummer leitet an den SSH-Port (Secure Shell) auf der VM weiter.

## <a name="use-a-shared-ip"></a>Verwenden einer freigegebenen IP-Adresse

- **Windows-Benutzer**: Wählen Sie im Azure-Portal die Schaltfläche **Verbinden** auf der Seite **Übersicht** der VM aus, um eine vorkonfigurierte RDP-Datei herunterzuladen und auf die VM zuzugreifen.

- **Linux-Benutzer**: SSH (Secure Shell) stellt eine Verbindung mit der VM her, indem die IP-Adresse oder der vollqualifizierte Domänennamen gefolgt von einem Doppelpunkt und der Portnummer verwendet wird. Der folgende Screenshot zeigt beispielsweise die SSH-Verbindungsadresse `contosolab21000000000000.westus3.cloudapp.azure.com:65013`.

  ![Screenshot: RDP- und SSH-Verbindungsoptionen auf der Seite „Übersicht“ einer VM.](media/devtest-lab-shared-ip/vm-info.png)

## <a name="next-steps"></a>Nächste Schritte

- [Definieren von Labrichtlinien in Azure DevTest Labs](devtest-lab-set-lab-policy.md)
- [Konfigurieren eines virtuellen Netzwerks in Azure DevTest Labs](devtest-lab-configure-vnet.md)
