---
title: Verwenden von Azure DevTest Labs zu Schulungszwecken
description: Dieser Artikel enthält ausführliche Schritte, die Sie befolgen können, um ein Lab für das Training in Azure DevTest Labs einzurichten.
ms.topic: conceptual
ms.date: 06/26/2020
ms.openlocfilehash: 6809ee2a9d08e059184ccbcb98c20ac5ea2160fa
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132398115"
---
# <a name="use-azure-devtest-labs-for-training"></a>Verwenden von Azure DevTest Labs zu Schulungszwecken
Sie können Azure DevTest Labs verwenden, um neben Dev/Test viele wichtige Szenarien zu implementieren. Ein Szenario ist die Einrichtung eines Labs für das Training. Azure DevTest Labs können Sie ein Lab erstellen, das benutzerdefinierte Vorlagen für jeden Trainer zur Erstellung identischer und isolierter Trainingsumgebungen bietet. Sie können Richtlinien anwenden, um sicherzustellen, dass Schulungsumgebungen für jeden Einzelnen nur verfügbar sind, wenn er sie benötigt und genügend Ressourcen für das Training enthält, z. B. virtuelle Computer. Schließlich können Sie das Lab problemlos freigeben, damit Benutzer mit nur einem Klick darauf zugreifen können.

![Verwenden von DevTest Labs zu Schulungszwecken](./media/devtest-lab-training-lab/devtest-lab-training.png)

Azure DevTest Labs erfüllt die folgenden Anforderungen für die Durchführung von Schulungen in einer beliebigen virtuellen Umgebung: 

* Benutzer können keine VMs sehen, die von anderen Benutzer erstellt wurden.
* Jeder Trainingscomputer sollte identisch sein.
* Benutzer können ihre Trainingsumgebungen schnell bereitstellen.
* Steuern Sie die Kosten, indem Sie sicherstellen, dass Benutzer nicht mehr VMs als benötigt erhalten, und VMs herunterfahren, wenn sie sie nicht verwenden.
* Teilen Sie das Trainingslabor problemlos mit jedem Einzelnen.
* Verwenden Sie das Trainingslabor immer wieder.

In diesem Artikel erfahren Sie mehr über verschiedene Azure DevTest Labs, die Sie verwenden können, um die Trainingsanforderungen zu erfüllen. Sie können die ausführlichen Schritte zum Einrichten eines Labs für das Training ausführen.  

## <a name="implementing-training-with-azure-devtest-labs"></a>Implementieren von Schulungen mit Azure DevTest Labs
1. **Erstellen des Labs** 
   
    Labs sind der Ausgangspunkt in Azure DevTest Labs. Nach dem Erstellen eines Labs können Sie z. B. Benutzer hinzufügen, Richtlinien für die Kostenkontrolle festlegen, VM-Images definieren, die schnell erstellen können und vieles mehr.   
   
    Klicken Sie auf die Links in der folgenden Tabelle, um weitere Informationen zu erhalten:
   
   | Aufgabe | Lerninhalt |
   | --- | --- |
   | [Erstellen eines Labs in Azure DevTest Labs](devtest-lab-create-lab.md) |Erfahren Sie, wie im Azure-Portal ein Lab in Azure DevTest Labs erstellt wird. |
2. **Erstellen von virtuellen Schulungscomputern innerhalb weniger Minuten mithilfe von vordefinierten Marketplace-Images und benutzerdefinierten Images** 
   
    Aus einer Vielzahl von Images in Azure Marketplace können Sie vorgefertigte Images auswählen und den Benutzern im Lab zur Verfügung stellen. Wenn die vorgefertigten Images Ihre Anforderungen nicht erfüllen, können Sie ein benutzerdefiniertes Image erstellen. Sie erstellen eine Lab-VM mit einem vorgefertigten Image von Azure Marketplace, installieren die Software, die Sie für das Training benötigen, und speichern die VM als benutzerdefiniertes Image im Lab. 
   
    Klicken Sie auf die Links in der folgenden Tabelle, um weitere Informationen zu erhalten:
   
   | Aufgabe | Lerninhalt |
   | --- | --- |
   | [Konfigurieren von Azure Marketplace-Images](devtest-lab-configure-marketplace-images.md) |Erfahren Sie, wie Sie Azure Marketplace-Images zulassen; dabei werden nur die Images zur Auswahl zur Verfügung gestellt, die Sie für die Schulung verwenden möchten. |
   | [Erstellen eines benutzerdefinierten Images](devtest-lab-create-template.md) |Erstellen Sie ein benutzerdefiniertes Image, indem Sie die für die Schulung erforderliche Software vorinstallieren, sodass Lab-Benutzer mithilfe des benutzerdefinierten Images schnell einen virtuellen Computer erstellen können. |
3. **Erstellen von wiederverwendbaren Vorlagen für Schulungscomputer** 
   
    Eine Formel in Azure DevTest Labs ist eine Liste standardmäßiger Eigenschaftswerte, die zum Erstellen eines virtuellen Computers verwendet werden. Sie können im Lab eine Formel erstellen, indem Sie ein Image, eine Größe für den virtuellen Computer (eine Kombination aus CPU und Arbeitsspeicher) und ein virtuelles Netzwerk auswählen. Jeder Lab-Benutzer kann die Formel im Lab sehen und zum Erstellen eines virtuellen Computers verwenden. 
   
    Klicken Sie auf die Links in der folgenden Tabelle, um weitere Informationen zu erhalten:
   
   | Aufgabe | Lerninhalt |
   | --- | --- |
   | [Verwalten von DevTest Labs-Formeln zum Erstellen virtueller Computer](devtest-lab-manage-formulas.md) |Erfahren Sie, wie Sie eine Formel erstellen, indem Sie ein Image, eine Größe für den virtuellen Computer (Kombination aus CPU und Arbeitsspeicher) und ein virtuelles Netzwerk auswählen. |
4. **Kostenkontrolle**
   
    Mit Azure DevTest Labs können Sie eine Richtlinie im Lab festlegen, um die Anzahl von virtuellen Computern zu begrenzen, die von einem Mitarbeiter im Lab erstellt werden können. 
   
    Wenn Sie ein tagesübergreifendes Training durchführen, können Sie alle virtuellen Computer zu einer bestimmten Tageszeit beenden und am nächsten Tag automatisch neu starten. Wenn Sie dies tun möchten, legen Sie Richtlinien für automatisches Herunterfahren und automatischen Start im Lab fest. 
   
    Nach Abschluss der Schulung können Sie alle virtuellen Computer durch Ausführen eines einzigen PowerShell-Skripts gleichzeitig löschen. 
   
    Klicken Sie auf die Links in der folgenden Tabelle, um weitere Informationen zu erhalten:
   
   | Aufgabe | Lerninhalt |
   | --- | --- |
   | [Definieren von Labrichtlinien](devtest-lab-set-lab-policy.md) |Kontrollieren Sie Kosten durch Festlegen von Richtlinien im Lab. |
   | [Löschen aller virtuellen Computer mithilfe eines PowerShell-Skripts](./devtest-lab-faq.yml) |Löschen Sie alle Labs in einem Vorgang, wenn die Schulung abgeschlossen ist. |
5. **Freigeben des Labs für alle Benutzer**
   
    Labs sind direkt über einen Link zugänglich, den Sie für Ihre Benutzer freigeben. Die Benutzer benötigen nicht einmal ein Azure-Konto, sofern sie über ein [Microsoft-Konto](./devtest-lab-faq.yml)verfügen. Benutzer können keine VMs sehen, die von anderen Benutzer erstellt wurden.  
   
    Klicken Sie auf die Links in der folgenden Tabelle, um weitere Informationen zu erhalten:
   
   | Aufgabe | Lerninhalt |
   | --- | --- |
   | [Hinzufügen eines Benutzers zu einem Lab in Azure DevTest Labs](devtest-lab-add-devtest-user.md) |Fügen Sie Ihrem Schulungs-Lab mithilfe des Azure-Portals Benutzer hinzu. |
   | [Hinzufügen von Benutzern zum Lab mithilfe eines PowerShell-Skripts](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell) |Automatisieren Sie mithilfe von PowerShell das Hinzufügen von Benutzern zu Ihrem Schulungs-Lab. |
   | [Abrufen eines Links zum Lab](./devtest-lab-faq.yml) |Erfahren Sie, wie ein Lab direkt über einen Hyperlink aufgerufen werden kann. |
6. **Mehrfaches Verwenden des Labs** 
   
    Sie können die Lab-Erstellung, einschließlich der benutzerdefinierten Einstellungen, automatisieren, indem Sie eine Resource Manager-Vorlage erstellen und diese mehrfach zum Erstellen identischer Labs verwenden. 
   
    Klicken Sie auf die Links in der folgenden Tabelle, um weitere Informationen zu erhalten:
   
   | Aufgabe | Lerninhalt |
   | --- | --- |
   | [Erstellen eines Labs mithilfe einer Resource Manager-Vorlage](./devtest-lab-faq.yml) |Erstellen Sie Labs in Azure DevTest Labs mithilfe von Resource Manager-Vorlagen. |

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]
