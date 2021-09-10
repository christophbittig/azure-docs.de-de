---
title: Entfernen von Ressourcen aus einer Sammlung für die Verschiebung in Azure Resource Mover
description: Hier erfahren Sie, wie Ressourcen aus einer Sammlung für die Verschiebung in Azure Resource Mover entfernt werden.
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 02/22/2020
ms.author: raynew
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: a655e8f0a4e4a6d44ce8960b45991cf6e394e2db
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/22/2021
ms.locfileid: "112454278"
---
# <a name="manage-move-collections-and-resource-groups"></a>Verwalten von Verschiebungssammlungen und Ressourcengruppen

In diesem Artikel wird beschrieben, wie in [Azure Resource Mover](overview.md) Ressourcen aus einer Verschiebungssammlung entfernt werden, oder wie eine Verschiebungssammlung/Ressourcengruppe entfernt wird. Sammlungen für die Verschiebung werden beim Verschieben von Azure-Ressourcen zwischen Azure-Regionen verwendet.

## <a name="remove-a-resource-portal"></a>Entfernen einer Ressource (Portal)

Sie können Ressourcen in einer Verschiebungssammlung im Resource Mover-Portal wie folgt entfernen:

1. Wählen Sie unter **Regionsübergreifend** alle Ressourcen aus, die Sie aus der Sammlung entfernen möchten, und wählen Sie **Entfernen** aus. 

    ![Schaltfläche, die Sie zum Entfernen auswählen](./media/remove-move-resources/portal-select-resources.png)

2. Klicken Sie unter **Ressourcen entfernen** auf **Entfernen**.

    ![Schaltfläche, die Sie auswählen, um Ressourcen in einer Sammlung für die Verschiebung zu entfernen](./media/remove-move-resources/remove-portal.png)

## <a name="remove-a-move-collectionresource-group-portal"></a>Entfernen einer Verschiebungssammlung/Ressourcengruppe (Portal)

Sie können eine Verschiebungssammlung/Ressourcengruppe im Portal entfernen.

1. Befolgen Sie die Anweisungen in der obigen Prozedur, um Ressourcen aus der Sammlung zu entfernen. Wenn Sie eine Ressourcengruppe entfernen, stellen Sie sicher, dass sie keine Ressourcen enthält.
2. Löschen Sie die Verschiebungssammlung oder Ressourcengruppe.  

## <a name="remove-a-resource-powershell"></a>Entfernen einer Ressource (PowerShell)

Mithilfe von PowerShell-Cmdlets können Sie eine einzelne Ressource aus einer MoveCollection entfernen oder mehrere Ressourcen entfernen.

### <a name="remove-a-single-resource"></a>Entfernen einer einzelnen Ressource

Entfernen Sie eine Ressource (in unserem Beispiel das virtuelle Netzwerk *psdemorm-vnet*) wie folgt:

```azurepowershell-interactive
# Remove a resource using the resource ID
Remove-AzResourceMoverMoveResource -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS" -Name "psdemorm-vnet"
```
**Ausgabe nach dem Ausführen des Cmdlets**

![Ausgegebener Text nach dem Entfernen einer Ressource aus einer Verschiebungssammlung](./media/remove-move-resources/powershell-remove-single-resource.png)

### <a name="remove-multiple-resources"></a>Entfernen mehrerer Ressourcen

Entfernen Sie mehrere Ressourcen wie folgt:

1. Überprüfen von Abhängigkeiten:

    ```azurepowershell-interactive
    $resp = Invoke-AzResourceMoverBulkRemove -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"  -MoveResource $('psdemorm-vnet') -ValidateOnly
    ```

    **Ausgabe nach dem Ausführen des Cmdlets**

    ![Ausgegebener Text nach dem Entfernen mehrerer Ressourcen aus einer Verschiebungssammlung](./media/remove-move-resources/remove-multiple-validate-dependencies.png)

2. Abrufen der abhängigen Ressourcen, die (zusammen mit unserem virtuellen Beispielnetzwerk „psdemorm-vnet“) entfernt werden müssen:

    ```azurepowershell-interactive
    $resp.AdditionalInfo[0].InfoMoveResource
    ```

    **Ausgabe nach dem Ausführen des Cmdlets**

    ![Ausgabetext nach dem Abrufen abhängiger Ressourcen, die entfernt werden müssen](./media/remove-move-resources/remove-multiple-get-dependencies.png)


3. Entfernen aller Ressourcen zusammen mit dem virtuellen Netzwerk:

    
    ```azurepowershell-interactive
    Invoke-AzResourceMoverBulkRemove -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"  -MoveResource $('PSDemoVM','psdemovm111', 'PSDemoRM-vnet','PSDemoVM-nsg')
    ```

    **Ausgabe nach dem Ausführen des Cmdlets**

    ![Ausgegebener Text nach dem Entfernen aller Ressourcen aus einer Verschiebungssammlung](./media/remove-move-resources/remove-multiple-all.png)


## <a name="remove-a-collection-powershell"></a>Entfernen einer Sammlung (PowerShell)

Entfernen Sie wie folgt eine ganze Verschiebungssammlung aus dem Abonnement:

1. Befolgen Sie die voranstehenden Anweisungen, um Ressourcen in der Sammlung mithilfe der PowerShell zu entfernen.
2. Entfernen Sie eine Sammlung wie folgt:

    ```azurepowershell-interactive
    Remove-AzResourceMoverMoveCollection -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"
    ```

    **Ausgabe nach dem Ausführen des Cmdlets**
    
    ![Ausgegebener Text nach dem Entfernen einer Verschiebungssammlung](./media/remove-move-resources/remove-collection.png)

## <a name="vm-resource-state-after-removing"></a>Zustand einer VM-Ressource nach dem Entfernen

Was geschieht, wenn Sie eine VM-Ressource in einer Sammlung für die Verschiebung entfernen, hängt vom Zustand der Ressource ab und ist in der Tabelle aufgeführt.

###  <a name="remove-vm-state"></a>Zustand einer VM-Ressource nach dem Entfernen
**Ressourcenzustand** | **VM** | **Netzwerk**
--- | --- | --- 
**Zur Sammlung für die Verschiebung hinzugefügt** | Wird in der Sammlung für die Verschiebung gelöscht. | Wird in der Sammlung für die Verschiebung gelöscht. 
**Abhängigkeiten aufgelöst/Vorbereitung steht aus** | Wird in der Sammlung für die Verschiebung gelöscht.  | Wird in der Sammlung für die Verschiebung gelöscht. 
**Vorbereitung wird durchgeführt**<br/> (oder ein anderer Zustand in Ausführung) | Beim Löschen tritt ein Fehler auf.  | Beim Löschen tritt ein Fehler auf.
**Fehler bei Vorbereitung** | Wird in der Sammlung für die Verschiebung gelöscht.<br/>Alle in der Zielregion erstellten Elemente werden gelöscht – auch Replikatdatenträger. <br/><br/> Beim Verschieben erstellte Infrastrukturressourcen müssen manuell gelöscht werden. | Wird in der Sammlung für die Verschiebung gelöscht.  
**Einleitung der Verschiebung ausstehend** | Wird in der Sammlung für die Verschiebung gelöscht.<br/><br/> Alle in der Zielregion erstellten Elemente werden gelöscht – auch der virtuelle Computer, Replikatdatenträger usw.  <br/><br/> Beim Verschieben erstellte Infrastrukturressourcen müssen manuell gelöscht werden. | Wird in der Sammlung für die Verschiebung gelöscht.
**Fehler beim Einleiten der Verschiebung** | Wird in der Sammlung für die Verschiebung gelöscht.<br/><br/> Alle in der Zielregion erstellten Elemente werden gelöscht – auch der virtuelle Computer, Replikatdatenträger usw.  <br/><br/> Beim Verschieben erstellte Infrastrukturressourcen müssen manuell gelöscht werden. | Wird in der Sammlung für die Verschiebung gelöscht.
**Commit ausstehend** | Es wird empfohlen, die Verschiebung zu verwerfen, sodass die Zielressourcen zuerst gelöscht werden.<br/><br/> Die Ressource wird wieder in den Zustand **Einleitung der Verschiebung ausstehend** versetzt, und Sie können den Vorgang von dort aus fortsetzen. | Es wird empfohlen, die Verschiebung zu verwerfen, sodass die Zielressourcen zuerst gelöscht werden.<br/><br/> Die Ressource wird wieder in den Zustand **Einleitung der Verschiebung ausstehend** versetzt, und Sie können den Vorgang von dort aus fortsetzen. 
**Fehler bei Commit** | Es wird empfohlen, die Verschiebung zu verwerfen, sodass die Zielressourcen zuerst gelöscht werden.<br/><br/> Die Ressource wird wieder in den Zustand **Einleitung der Verschiebung ausstehend** versetzt, und Sie können den Vorgang von dort aus fortsetzen. | Es wird empfohlen, die Verschiebung zu verwerfen, sodass die Zielressourcen zuerst gelöscht werden.<br/><br/> Die Ressource wird wieder in den Zustand **Einleitung der Verschiebung ausstehend** versetzt, und Sie können den Vorgang von dort aus fortsetzen.
**Verwerfen abgeschlossen** | Die Ressource wird wieder in den Zustand **Einleitung der Verschiebung ausstehend** versetzt.<br/><br/> Sie wird in der Sammlung für die Verschiebung zusammen mit allen am Ziel erstellten Elementen – virtuelle Computer, Replikatdatenträger, Tresore usw. – gelöscht.  <br/><br/> Beim Verschieben erstellte Infrastrukturressourcen müssen manuell gelöscht werden. <br/><br/> Beim Verschieben erstellte Infrastrukturressourcen müssen manuell gelöscht werden. |  Die Ressource wird wieder in den Zustand **Einleitung der Verschiebung ausstehend** versetzt.<br/><br/> Sie wird in der Sammlung für die Verschiebung gelöscht.
**Fehler beim Verwerfen** | Es wird empfohlen, die Verschiebung zu verwerfen, sodass die Zielressourcen zuerst gelöscht werden.<br/><br/> Danach wird die Ressource wieder in den Zustand **Einleitung der Verschiebung ausstehend** versetzt, und Sie können den Vorgang von dort aus fortsetzen. | Es wird empfohlen, die Verschiebung zu verwerfen, sodass die Zielressourcen zuerst gelöscht werden.<br/><br/> Danach wird die Ressource wieder in den Zustand **Einleitung der Verschiebung ausstehend** versetzt, und Sie können den Vorgang von dort aus fortsetzen.
**Löschung der Quelle ausstehend** | Wird in der Sammlung für die Verschiebung gelöscht.<br/><br/> Die in der Zielregion erstellten Elemente werden nicht gelöscht.  | Wird in der Sammlung für die Verschiebung gelöscht.<br/><br/> Die in der Zielregion erstellten Elemente werden nicht gelöscht.
**Fehler beim Löschen der Quelle** | Wird in der Sammlung für die Verschiebung gelöscht.<br/><br/> Die in der Zielregion erstellten Elemente werden nicht gelöscht. | Wird in der Sammlung für die Verschiebung gelöscht.<br/><br/> Die in der Zielregion erstellten Elemente werden nicht gelöscht.
**Verschieben abgeschlossen** | Wird in der Sammlung für die Verschiebung gelöscht.<br/><br/> Die in der Ziel- oder Quellregion erstellten Elemente werden nicht gelöscht. |  Wird in der Sammlung für die Verschiebung gelöscht.<br/><br/> Die in der Ziel- oder Quellregion erstellten Elemente werden nicht gelöscht.

## <a name="sql-resource-state-after-removing"></a>Zustand einer SQL-Ressource nach dem Entfernen

Was geschieht, wenn Sie eine Azure SQL-Ressource in einer Sammlung für die Verschiebung entfernen, hängt vom Zustand der Ressource ab und ist in der Tabelle aufgeführt.

**Ressourcenzustand** | **SQL** 
--- | --- 
**Zur Sammlung für die Verschiebung hinzugefügt** | Wird in der Sammlung für die Verschiebung gelöscht. 
**Abhängigkeiten aufgelöst/Vorbereitung steht aus** | Wird in der Sammlung für die Verschiebung gelöscht. 
**Vorbereitung wird durchgeführt**<br/> (oder ein anderer Zustand in Ausführung)  | Beim Löschen tritt ein Fehler auf. 
**Fehler bei Vorbereitung** | Wird in der Sammlung für die Verschiebung gelöscht.<br/><br/>Alle in der Zielregion erstellten Elemente werden gelöscht. 
**Einleitung der Verschiebung ausstehend** |  Wird in der Sammlung für die Verschiebung gelöscht.<br/><br/>Alle in der Zielregion erstellten Elemente werden gelöscht. Die SQL-Datenbank ist zu diesem Zeitpunkt vorhanden und wird gelöscht. 
**Fehler beim Einleiten der Verschiebung** | Wird in der Sammlung für die Verschiebung gelöscht.<br/><br/>Alle in der Zielregion erstellten Elemente werden gelöscht. Die SQL-Datenbank ist zu diesem Zeitpunkt vorhanden und muss gelöscht werden. 
**Commit ausstehend** | Es wird empfohlen, die Verschiebung zu verwerfen, sodass die Zielressourcen zuerst gelöscht werden.<br/><br/> Die Ressource wird wieder in den Zustand **Einleitung der Verschiebung ausstehend** versetzt, und Sie können den Vorgang von dort aus fortsetzen.
**Fehler bei Commit** | Es wird empfohlen, die Verschiebung zu verwerfen, sodass die Zielressourcen zuerst gelöscht werden.<br/><br/> Die Ressource wird wieder in den Zustand **Einleitung der Verschiebung ausstehend** versetzt, und Sie können den Vorgang von dort aus fortsetzen. 
**Verwerfen abgeschlossen** |  Die Ressource wird wieder in den Zustand **Einleitung der Verschiebung ausstehend** versetzt.<br/><br/> Sie wird in der Sammlung für die Verschiebung zusammen mit allen am Ziel erstellten Elementen wie SQL-Datenbanken gelöscht. 
**Fehler beim Verwerfen** | Es wird empfohlen, die Verschiebung zu verwerfen, sodass die Zielressourcen zuerst gelöscht werden.<br/><br/> Danach wird die Ressource wieder in den Zustand **Einleitung der Verschiebung ausstehend** versetzt, und Sie können den Vorgang von dort aus fortsetzen. 
**Löschung der Quelle ausstehend** | Wird in der Sammlung für die Verschiebung gelöscht.<br/><br/> Die in der Zielregion erstellten Elemente werden nicht gelöscht. 
**Fehler beim Löschen der Quelle** | Wird in der Sammlung für die Verschiebung gelöscht.<br/><br/> Die in der Zielregion erstellten Elemente werden nicht gelöscht. 
**Verschieben abgeschlossen** | Wird in der Sammlung für die Verschiebung gelöscht.<br/><br/> Die in der Ziel- oder Quellregion erstellten Elemente werden nicht gelöscht.

## <a name="next-steps"></a>Nächste Schritte

Versuchen Sie, eine VM mit Resource Mover in eine andere Region zu [verschieben](tutorial-move-region-virtual-machines.md).
