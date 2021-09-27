---
title: Verwenden von Azure DevTest Labs zu Schulungszwecken
description: Dieser Artikel enthält ausführliche Schritte, die Sie befolgen können, um ein Lab für das Training in Azure DevTest Labs einzurichten.
ms.topic: conceptual
ms.date: 06/26/2020
ms.openlocfilehash: d72d87cf2108a5e6d372edbf698e6adc7b100618
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128629254"
---
# <a name="use-azure-devtest-labs-for-training"></a>Verwenden von Azure DevTest Labs zu Schulungszwecken
Azure DevTest Labs kann außer zum Entwickeln und Testen auch zum Implementieren zahlreicher gängiger Szenarien verwendet werden. Eines dieser Szenarien ist das Einrichten eines Labs zu Schulungszwecken. Mit Azure DevTest Labs können Sie ein Lab erstellen, in dem Sie benutzerdefinierte Vorlagen bereitstellen, mit denen jeder Lab-Benutzer identische und isolierte Umgebungen zu Schulungszwecken erstellen kann. Sie können durch das Anwenden von Richtlinien sicherstellen, dass Schulungsumgebungen für jeden Lab-Benutzer nur bei Bedarf verfügbar sind und genügend Ressourcen – z.B. virtuelle Computer – enthalten, die zur Schulung erforderlich sind. Schließlich können Sie das Lab problemlos für Benutzer freigeben, die dann mit einem Klick darauf zugreifen können.

![Verwenden von DevTest Labs zu Schulungszwecken](./media/devtest-lab-training-lab/devtest-lab-training.png)

Azure DevTest Labs erfüllt die folgenden Anforderungen für das Durchführen von Schulungen in virtuellen Umgebungen: 

* Lab-Benutzer können die von anderen Benutzern erstellten virtuellen Computer nicht sehen
* Jeder Schulungscomputer muss identisch sein
* Lab-Benutzer können ihre Schulungsumgebungen schnell bereitstellen
* Kostenkontrolle, indem sichergestellt wird, dass Lab-Benutzer nur die zur Schulung erforderliche Anzahl von virtuellen Computer erhalten und nicht verwendete virtuelle Computer heruntergefahren werden
* Einfache Freigabe des Schulungs-Labs für alle Benutzer
* Schulungs-Labs können beliebig oft wiederverwendet werden

In diesem Artikel erfahren Sie mehr zu verschiedenen Azure DevTest Labs-Features, die zum Erfüllen der zuvor beschriebenen Schulungsanforderungen verwendet werden können. Zudem enthält er detaillierte Schritte, die Sie ausführen können, um ein Lab zu Schulungszwecken einzurichten.  

## <a name="implementing-training-with-azure-devtest-labs"></a>Implementieren von Schulungen mit Azure DevTest Labs
1. **Erstellen des Labs** 
   
    Labs sind der Ausgangspunkt in Azure DevTest Labs. Nach dem Erstellen eines Labs können Sie z. B. Benutzer hinzufügen, Richtlinien für die Kostenkontrolle festlegen, VM-Images definieren, die schnell erstellen können und vieles mehr.   
   
    Klicken Sie auf die Links in der folgenden Tabelle, um weitere Informationen zu erhalten:
   
   | Aufgabe | Lerninhalt |
   | --- | --- |
   | [Erstellen eines Labs in Azure DevTest Labs](devtest-lab-create-lab.md) |Erfahren Sie, wie im Azure-Portal ein Lab in Azure DevTest Labs erstellt wird. |
2. **Erstellen von virtuellen Schulungscomputern innerhalb weniger Minuten mithilfe von vordefinierten Marketplace-Images und benutzerdefinierten Images** 
   
    Aus einer Vielzahl von Images in Azure Marketplace können Sie vorgefertigte Images auswählen und den Benutzern im Lab zur Verfügung stellen. Wenn die vorgefertigten Images Ihren Anforderungen nicht entsprechen, können Sie ein benutzerdefiniertes Image erstellen. Dazu erstellen Sie mithilfe eines vorgefertigten Images aus Azure Marketplace einen virtuellen Lab-Computer, installieren die für die Schulung erforderliche Software und speichern den virtuellen Computers als benutzerdefiniertes Image im Lab. 
   
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
   
    Mit Azure DevTest Labs können Sie eine Richtlinie im Lab festlegen, um die Anzahl von virtuellen Computern zu begrenzen, die von einem Benutzer im Lab erstellt werden können. 
   
    Wenn Sie eine mehrtägige Schulung durchführen und alle virtuellen Computer zu einer bestimmten Tageszeit herunterfahren und am folgenden Tag neu starten möchten, können Sie im Lab Richtlinien zum automatischen Herunterfahren und Starten festlegen. 
   
    Nach Abschluss der Schulung können Sie alle virtuellen Computer durch Ausführen eines einzigen PowerShell-Skripts gleichzeitig löschen. 
   
    Klicken Sie auf die Links in der folgenden Tabelle, um weitere Informationen zu erhalten:
   
   | Aufgabe | Lerninhalt |
   | --- | --- |
   | [Definieren von Labrichtlinien](devtest-lab-set-lab-policy.md) |Kontrollieren Sie Kosten durch Festlegen von Richtlinien im Lab. |
   | [Löschen aller virtuellen Computer mithilfe eines PowerShell-Skripts](/azure/devtest-labs/devtest-lab-faq#how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab) |Löschen Sie alle Labs in einem Vorgang, wenn die Schulung abgeschlossen ist. |
5. **Freigeben des Labs für alle Benutzer**
   
    Labs sind direkt über einen Link zugänglich, den Sie für Ihre Benutzer freigeben. Die Benutzer benötigen nicht einmal ein Azure-Konto, sofern sie über ein [Microsoft-Konto](/azure/devtest-labs/devtest-lab-faq#what-is-a-microsoft-account)verfügen. Lab-Benutzer können die von anderen Benutzern erstellten virtuellen Computer nicht sehen  
   
    Klicken Sie auf die Links in der folgenden Tabelle, um weitere Informationen zu erhalten:
   
   | Aufgabe | Lerninhalt |
   | --- | --- |
   | [Hinzufügen eines Benutzers zu einem Lab in Azure DevTest Labs](devtest-lab-add-devtest-user.md) |Fügen Sie Ihrem Schulungs-Lab mithilfe des Azure-Portals Benutzer hinzu. |
   | [Hinzufügen von Benutzern zum Lab mithilfe eines PowerShell-Skripts](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell) |Automatisieren Sie mithilfe von PowerShell das Hinzufügen von Benutzern zu Ihrem Schulungs-Lab. |
   | [Abrufen eines Links zum Lab](/azure/devtest-labs/devtest-lab-faq#how-do-i-share-a-direct-link-to-my-lab) |Erfahren Sie, wie ein Lab direkt über einen Hyperlink aufgerufen werden kann. |
6. **Mehrfaches Verwenden des Labs** 
   
    Sie können die Lab-Erstellung, einschließlich der benutzerdefinierten Einstellungen, automatisieren, indem Sie eine Resource Manager-Vorlage erstellen und diese mehrfach zum Erstellen identischer Labs verwenden. 
   
    Klicken Sie auf die Links in der folgenden Tabelle, um weitere Informationen zu erhalten:
   
   | Aufgabe | Lerninhalt |
   | --- | --- |
   | [Erstellen eines Labs mithilfe einer Resource Manager-Vorlage](/azure/devtest-labs/devtest-lab-faq#how-do-i-create-a-lab-from-a-resource-manager-template) |Erstellen Sie Labs in Azure DevTest Labs mithilfe von Resource Manager-Vorlagen. |

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]