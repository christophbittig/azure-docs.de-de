---
title: Verschieben von Ressourcen zwischen Regionen mithilfe von PowerShell in Azure Resource Mover
description: Hier erfahren Sie, wie Sie Ressourcen zwischen Regionen mithilfe von PowerShell in Azure Resource Mover verschieben.
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: tutorial
ms.date: 10/04/2021
ms.author: raynew
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 57d08ea3997dac79f680881d3adb096afd8c9ea2
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131433458"
---
# <a name="move-resources-across-regions-in-powershell"></a>Verschieben von Ressourcen zwischen Regionen mithilfe von PowerShell

In diesem Artikel erfahren Sie, wie Sie PowerShell in [Azure Resource Mover](overview.md) verwenden, um Azure-Ressourcen in eine andere Azure-Region verschieben.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Prüfen der Voraussetzungen und Anforderungen
> * Einrichten der Verschiebungssammlung
> * Hinzufügen von Ressourcen zur Verschiebungssammlung und Auflösen von Abhängigkeiten
> * Vorbereiten und Verschieben der Quellressourcengruppe 
> * Vorbereiten und Verschieben der anderen Ressourcen
> * Verwerfen oder Committen der Verschiebung 
> * Optionales Entfernen von Ressourcen in der Quellregion nach der Verschiebung

> [!NOTE]
> Tutorials zeigen den schnellsten Weg zum Ausprobieren eines Szenarios, dabei kommen die Standardoptionen zum Einsatz. 

Wenn Sie kein Azure-Abonnement besitzen, erstellen Sie ein [kostenloses Konto](https://azure.microsoft.com/pricing/free-trial/), bevor Sie beginnen. Melden Sie sich dann beim [Azure-Portal](https://portal.azure.com)an.

## <a name="prerequisites"></a>Voraussetzungen
**Anforderung** | **Beschreibung**
--- | ---
**Abonnementberechtigungen** | Vergewissern Sie sich, dass Sie *Besitzerzugriff* auf das Abonnement haben, das die zu verschiebenden Ressourcen enthält.<br/><br/> **Warum benötige ich Besitzerzugriff?** Wenn Sie zum ersten Mal eine Ressource für ein bestimmtes Quelle-Ziel-Paar in einem Azure-Abonnement hinzufügen, erstellt Resource Mover eine [vom System zugewiesene verwaltete Identität](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) (früher als Managed Service Identify (MSI) bezeichnet), die vom Abonnement als vertrauenswürdig eingestuft wird. Zum Erstellen der Identität und zum Zuweisen der erforderlichen Rolle (Mitwirkender oder Benutzerzugriffsadministrator im Quellabonnement) benötigt das Konto, das Sie zum Hinzufügen von Ressourcen verwenden, Berechtigungen als *Besitzer* für das Abonnement. Hier [erfahren Sie mehr](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) zu Azure-Rollen.
**Unterstützung von Resource Mover** | Informationen zu unterstützten Regionen und Antworten auf andere häufig gestellte Fragen finden Sie [hier](common-questions.md).
**VM-Support** |  Vergewissern Sie sich, dass alle virtuellen Computer, die Sie verschieben möchten, unterstützt werden.<br/><br/> - Informationen zu unterstützten virtuellen Windows-Computern finden Sie [hier](support-matrix-move-region-azure-vm.md#windows-vm-support).<br/><br/> - Informationen zu unterstützten virtuellen Linux-Computern und Kernel-Versionen finden Sie [hier](support-matrix-move-region-azure-vm.md#linux-vm-support).<br/><br/> - Überprüfen Sie die unterstützten Einstellungen für [Compute](support-matrix-move-region-azure-vm.md#supported-vm-compute-settings), [Speicher](support-matrix-move-region-azure-vm.md#supported-vm-storage-settings) und [Netzwerk](support-matrix-move-region-azure-vm.md#supported-vm-networking-settings).
**SQL-Unterstützung** | Wenn Sie SQL-Ressourcen verschieben möchten, überprüfen Sie die [Liste mit den SQL-Anforderungen](tutorial-move-region-sql.md#check-sql-requirements).
**Zielabonnement** | Das Abonnement in der Zielregion benötigt ein ausreichendes Kontingent zum Erstellen der Ressourcen, die Sie in die Zielregion verschieben. Wenn kein ausreichendes Kontingent vorhanden ist, [fordern Sie eine Heraufsetzung des Kontingents an](../azure-resource-manager/management/azure-subscription-service-limits.md).
**Gebühren für die Zielregion** | Überprüfen Sie die Preise und Gebühren für die Zielregion, in die Sie virtuelle Computer verschieben. Verwenden Sie hierfür den [Preisrechner](https://azure.microsoft.com/pricing/calculator/).

### <a name="review-powershell-requirements"></a>Überprüfen der PowerShell-Anforderungen

Die meisten Vorgänge zum Verschieben von Ressourcen sind sowohl mit dem Azure-Portal als auch mit PowerShell gleich. Es gibt jedoch ein paar Ausnahmen.

**Vorgang** | **Portal** | **PowerShell**
--- | --- | ---
**Erstellen einer Verschiebungssammlung** | Eine Verschiebungssammlung (eine Liste aller zu verschiebenden Ressourcen) wird automatisch erstellt. Erforderliche Identitätsberechtigungen werden im Back-End durch das Portal zugewiesen. | Mithilfe von PowerShell-Cmdlets müssen folgende Schritte ausgeführt werden:<br/><br/> - Erstellen einer Ressourcengruppe für die Verschiebungssammlung und Angeben des Standorts<br/><br/> - Zuweisen einer verwalteten Identität zur Sammlung<br/><br/> - Hinzufügen von Ressourcen zur Sammlung
**Entfernen einer Verschiebungssammlung** | Eine Verschiebungssammlung kann nicht direkt im Portal entfernt werden. | Verschiebungssammlungen werden mithilfe eines PowerShell-Cmdlets entfernt.
**Vorgänge zum Verschieben von Ressourcen**<br/><br/> (Vorbereiten, Verschiebung initiieren, committen usw.)| Einzelne Schritte mit automatischer Validierung durch Ressource Mover | PowerShell-Cmdlets für Folgendes:<br/><br/> 1. Überprüfen der Abhängigkeiten<br/><br/> 2. Durchführen der Verschiebung
**Löschen der Quellressourcen** | Direkt im Resource Mover-Portal | PowerShell-Cmdlets auf Ressourcentypebene


### <a name="sample-values"></a>Beispielwerte

Wir verwenden diese Werte in unseren Skriptbeispielen:

**Einstellung** | **Wert** 
--- | ---
Abonnement-ID | subscription-id
Quellregion |  USA (Mitte)
Zielregion | USA, Westen-Mitte
Ressourcengruppe (mit Metadaten für die Verschiebungssammlung) | RG-MoveCollection-demoRMS
Sammlungsnamen verschieben | PS-centralus-westcentralus-demoRMS
Ressourcengruppe (Quellregion) | PSDemoRM 
Ressourcengruppe (Zielregion) | PSDemoRM-target
Standort des Resource Mover-Diensts | USA (Ost) 2
IdentityType | SystemAssigned
Zu verschiebende VM | PSDemoVM


## <a name="sign-into-azure"></a>Anmelden bei Azure

Melden Sie sich mithilfe des Cmdlets „Connect-AzAccount“ bei Ihrem Azure-Abonnement an:

```azurepowershell-interactive
Connect-AzAccount – Subscription "<subscription-id>"
```

## <a name="set-up-the-move-collection"></a>Einrichten der Verschiebungssammlung

Das MoveCollection-Objekt speichert Metadaten und Konfigurationsinformationen zu den Ressourcen, die Sie verschieben möchten. Gehen Sie zum Einrichten einer Verschiebungssammlung wie folgt vor:

- Erstellen Sie eine Ressourcengruppe für die Verschiebungssammlung.
- Registrieren Sie den Dienstanbieter bei dem Abonnement, damit die Ressource „MoveCollection“ erstellt werden kann.
- Erstellen Sie das Objekt „MoveCollection“ mit verwalteter Identität. Damit das Objekt „MoveCollection“ auf das Abonnement zugreifen kann, in dem sich der Resource Mover-Dienst befindet, ist eine [systemseitig zugewiesene verwaltete Identität](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) (ehemals als Managed Service Identity (MSI) bezeichnet) erforderlich, für die eine Vertrauensstellung mit dem Abonnement besteht.
- Gewähren Sie der verwalteten Identität Zugriff auf das Resource Mover-Abonnement.

### <a name="create-the-resource-group"></a>Erstellen der Ressourcengruppe

Erstellen Sie eine Ressourcengruppe für die Metadaten und Konfigurationsinformationen der Verschiebungssammlung:

```azurepowershell-interactive
New-AzResourceGroup -Name "RG-MoveCollection-demoRMS" -Location "East US 2"
```
**Ausgabe**:

![Ausgabetext nach der Erstellung einer Ressourcengruppe](./media/tutorial-move-region-powershell/create-metadata-resource-group.png)

### <a name="register-the-resource-provider"></a>Registrieren des Ressourcenanbieters

1. Registrieren Sie den Ressourcenanbieter „Microsoft.Migrate“, damit die Ressource „MoveCollection“ erstellt werden kann:

    ```azurepowershell-interactive
    Register-AzResourceProvider -ProviderNamespace Microsoft.Migrate
    
2. Wait for registration:

    ```azurepowershell-interactive 
    While(((Get-AzResourceProvider -ProviderNamespace Microsoft.Migrate)| where {$_.RegistrationState -eq "Registered" -and $_.ResourceTypes.ResourceTypeName -eq "moveCollections"}|measure).Count -eq 0)
    {
        Start-Sleep -Seconds 5
        Write-Output "Waiting for registration to complete."
    }
    ```
### <a name="create-a-movecollection-object"></a>Erstellen eines Objekts vom Typ „MoveCollection“

Erstellen Sie ein Objekt vom Typ „MoveCollection“, und weisen Sie ihm eine verwaltete Identität zu: 

```azurepowershell-interactive
New-AzResourceMoverMoveCollection -Name "PS-centralus-westcentralus-demoRMS"  -ResourceGroupName "RG-MoveCollection-demoRMS" -SourceRegion "centralus" -TargetRegion "westcentralus" -Location "centraluseuap" -IdentityType "SystemAssigned"
```

**Ausgabe**:

![Ausgabetext nach der Erstellung einer zu verschiebenden Sammlung](./media/tutorial-move-region-powershell/output-move-collection.png)


### <a name="grant-access-to-the-managed-identity"></a>Gewähren von Zugriff für die verwaltete Identität

Gewähren Sie der verwalteten Identität Zugriff auf das Resource Mover-Abonnement: Sie müssen der Abonnementbesitzer sein.

1. Rufen Sie Identitätsdetails aus dem Objekt „MoveCollection“ ab.

    ```azurepowershell-interactive
    $moveCollection = Get-AzResourceMoverMoveCollection -SubscriptionId $subscriptionId -ResourceGroupName "RG-MoveCollection-demoRMS" -Name "PS-centralus-westcentralus-demoRMS"

    $identityPrincipalId = $moveCollection.IdentityPrincipalId   
    ``` 

2. Weisen Sie der Identität die erforderlichen Rollen zu, damit Azure Resource Mover auf Ihr Abonnement zugreifen und Sie bei der Ressourcenverschiebung unterstützen kann.

    ```azurepowershell-interactive
    New-AzRoleAssignment -ObjectId $identityPrincipalId -RoleDefinitionName Contributor -Scope "/subscriptions/$subscriptionId"

    New-AzRoleAssignment -ObjectId $identityPrincipalId -RoleDefinitionName "User Access Administrator" -Scope "/subscriptions/$subscriptionId"
    ``` 

## <a name="add-resources-to-the-move-collection"></a>Hinzufügen von Ressourcen zur Verschiebungssammlung

Rufen Sie die IDs für vorhandene Quellressourcen ab, die Sie verschieben möchten. Erstellen Sie das Einstellungsobjekt für die Zielressource, und fügen Sie der Verschiebungssammlung anschließend Ressourcen hinzu.

> [!NOTE]
> Zu einer Verschiebungssammlung hinzugefügte Ressourcen müssen sich im selben Abonnement befinden, können jedoch zu unterschiedlichen Ressourcengruppen gehören.

Gehen Sie zum Hinzufügen von Ressourcen wie folgt vor:

1. Rufen Sie die Quellressourcen-ID ab:

    ```azurepowershell-interactive
    Get-AzResource -Name PSDemoVM -ResourceGroupName PSDemoRM
    ```

    **Ausgabe**

    ![Ausgabetext nach dem Abrufen der Ressourcen-ID](./media/tutorial-move-region-powershell/output-retrieve-resource.png)

2. Erstellen Sie das Einstellungsobjekt für die Zielressource unter Berücksichtigung der zu verschiebenden Ressource. In diesem Fall handelt es sich um einen virtuellen Computer.

    ```azurepowershell-interactive
    $targetResourceSettingsObj = New-Object Microsoft.Azure.PowerShell.Cmdlets.ResourceMover.Models.Api202101.VirtualMachineResourceSettings
    ```

3. Legen Sie den Ressourcentyp und den Zielressourcennamen für das Objekt fest.

    ```azurepowershell-interactive
    $targetResourceSettingsObj.ResourceType = "Microsoft.Compute/virtualMachines"
    $targetResourceSettingsObj.TargetResourceName = "PSDemoVM"
    ```
    > [!NOTE]
    > In diesem Fall hat der virtuelle Zielcomputer gleichen Namen wie der virtuelle Computer in der Quellregion. Sie können aber auch einen anderen Namen verwenden.

4. Fügen Sie die Quell Ressourcen unter Verwendung der abgerufenen Ressourcen-ID und des erstellten Zieleinstellungsobjekts der Verschiebungssammlung hinzu.

    ```azurepowershell-interactive
    Add-AzResourceMoverMoveResource -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS" -SourceId "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx xxxxxxxxxxxx/resourceGroups/
    PSDemoRM/providers/Microsoft.Compute/virtualMachines/PSDemoVM" -Name "PSDemoVM" -ResourceSetting $targetResourceSettingsObj
    ```

    **Ausgabe** ![Ausgabetext nach dem Hinzufügen der Ressource](./media/tutorial-move-region-powershell/output-add-resource.png)

## <a name="validate-and-add-dependencies"></a>Überprüfen und Hinzufügen von Abhängigkeiten

Überprüfen Sie, ob die hinzugefügten Ressourcen von anderen Ressourcen abhängig sind, und fügen Sie sie bei Bedarf hinzu. 

1. Überprüfen Sie die Abhängigkeiten wie folgt:

    ```azurepowershell-interactive
    Resolve-AzResourceMoverMoveCollectionDependency -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"
    ```
    **Ausgabe (wenn Abhängigkeiten vorhanden sind)**

    ![Ausgabetext nach dem Überprüfen der Abhängigkeiten](./media/tutorial-move-region-powershell/dependency-output.png)

2. Identität mit fehlenden Abhängigkeiten:

    - So rufen Sie eine Liste mit allen fehlenden Abhängigkeiten ab:

        ```azurepowershell-interactive
        Get-AzResourceMoverUnresolvedDependency -MoveCollectionName "PS-centralus-westcentralus-demoRMS" -ResourceGroupName "RG-MoveCollection-demoRMS" -DependencyLevel Descendant
        ```
        **Ausgabe** ![Ausgabetext nach dem Abrufen einer Liste mit allen Abhängigkeiten](./media/tutorial-move-region-powershell/dependencies-list.png)  

    - So rufen Sie nur Abhängigkeiten der ersten Ebene (direkte Abhängigkeiten für die Ressource) ab:

        ```azurepowershell-interactive
        Get-AzResourceMoverUnresolvedDependency -MoveCollectionName "PS-centralus-westcentralus-demoRMS" -ResourceGroupName "RG-MoveCollection-demoRMS" -DependencyLevel Direct
        ```
        **Ausgabe** ![Ausgabetext nach dem Abrufen einer Liste mit den Abhängigkeiten der ersten Ebene](./media/tutorial-move-region-powershell/dependencies-list-direct.png)  

3. Wiederholen Sie die oben angegebenen Schritte zum [Hinzufügen von Ressourcen zur Verschiebungssammlung](#add-resources-to-the-move-collection), um ggf. fehlende Abhängigkeiten hinzuzufügen, und wiederholen Sie anschließend die Überprüfung, bis keine ausstehenden Ressourcen mehr vorhanden sind.

> [!NOTE]
> Falls Sie Ressourcen aus der Ressourcensammlung entfernen möchten, gehen Sie wie in [diesem Artikel](remove-move-resources.md) beschrieben vor.

## <a name="add-the-source-resource-group"></a>Hinzufügen der Quellressourcengruppe

Fügen Sie die Quellressourcengruppe mit den zu verschiebenden Ressourcen der Verschiebungssammlung hinzu.

1. Rufen Sie die ID der Ressourcengruppe ab.

    ```azurepowershell-interactive
    Get-AzResourceMoverUnresolvedDependency -MoveCollectionName "PS-centralus-westcentralus-demoRMS" -ResourceGroupName "RG-MoveCollection-demoRMS" -DependencyLevel Direct
    ```
    **Ausgabe** ![Ausgabetext nach dem Abrufen der ID der Quellressourcengruppe](./media/tutorial-move-region-powershell/source-resource-group-id.png)  

    > [!NOTE]
    > Hier wird eine Ressourcengruppe verwendet, die sich bereits in der Zielregion befindet.

 
2. Verwenden Sie die abgerufene ID, um die Ressourcengruppe der Sammlung hinzuzufügen.

    ```azurepowershell-interactive
    Add-AzResourceMoverMoveResource -ResourceGroupName "RG-MoveCollection-demoRMS"  -MoveCollectionName "PS-centralus-westcentralus-demoRMS" -SourceId "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/psdemorm"  -Name "psdemorm"  -ExistingTargetId "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/PSDemoRM-target"
    ```
    **Ausgabe** ![Ausgabetext nach dem Hinzufügen der Quellressourcengruppe zur Verschiebungssammlung](./media/tutorial-move-region-powershell/add-source-resource-group.png)

3. Überprüfen Sie die Abhängigkeiten nach dem Hinzufügen der Ressourcengruppe, um sicherzustellen, dass nichts mehr fehlt.

    ```azurepowershell-interactive
    Resolve-AzResourceMoverMoveCollectionDependency -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"
    ```
4. Wie Sie sehen, sind keine ausstehenden Abhängigkeiten mehr vorhanden.

    **Ausgabe** ![Ausgabetext nach dem Überprüfen der Abhängigkeiten](./media/tutorial-move-region-powershell/all-dependencies-added.png)


## <a name="prepare-resources"></a>Vorbereiten der Ressourcen

Die Ressourcen in der Quellregion In der Regel müssen in der Regel müssen vor dem Verschieben vorbereitet werden. Beispiel:

- Wenn Sie zustandslose Ressourcen wie virtuelle Azure-Netzwerke, Netzwerkadapter, Lastenausgleichsmodule und Netzwerksicherheitsgruppen verschieben, muss ggf. eine Azure Resource Manager-Vorlage exportiert werden.
- Wenn Sie zustandsbehaftete Ressourcen wie virtuelle Azure-Computer und SQL-Datenbanken verschieben, müssen ggf. Ressourcen aus der Quellregion in der Zielregion repliziert werden.

Da in diesem Tutorial virtuelle Computer verschoben werden, muss die Quellressourcengruppe vorbereitet werden. Danach muss die Verschiebung initiiert und committet werden, bevor mit der Vorbereitung der virtuellen Computer begonnen werden kann.

> [!NOTE]
> Wenn Sie über eine bereits vorhandene Zielressourcengruppe verfügen, können Sie die Verschiebung für die Quellressourcengruppe direkt committen und die Vorbereitungs- und Initiierungsphase überspringen.

  
### <a name="prepare-the-source-resource-group"></a>Bereiten Sie die Quellressourcengruppe vor:

1. Bereiten Sie die Ressourcengruppe vor:

    ```azurepowershell-interactive
    Invoke-AzResourceMoverPrepare -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"  -MoveResource “PSDemoRM”
    ```
    **Ausgabe**

    ![Ausgabetext nach der Vorbereitung der Quellressourcengruppe](./media/tutorial-move-region-powershell/prepare-source-resource-group.png)

2. Initiieren Sie die Verschiebung der Quellressourcengruppe.

    ```azurepowershell-interactive
    Invoke-AzResourceMoverInitiateMove -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"  -MoveResource “PSDemoRM”
    ```
    ![Ausgabetext nach dem Initiieren der Verschiebung der Quellressourcengruppe](./media/tutorial-move-region-powershell/initiate-move-source-resource-group.png)

3. Committen Sie die Verschiebung für die Quellressourcengruppe.

    ```azurepowershell-interactive
    Invoke-AzResourceMoverCommit -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"  -MoveResource “PSDemoRM”
    ```
    **Ausgabe**

    ![Ausgabetext nach dem Committen der Quellressourcengruppe](./media/tutorial-move-region-powershell/commit-move-source-resource-group.png)


### <a name="prepare-vm-resources"></a>Vorbereiten der VM-Ressourcen

Nachdem Sie die Quellressourcengruppe vorbereitet und verschoben haben, können Sie die VM-Ressourcen für die Verschiebung vorbereiten.

1. Überprüfen Sie die Abhängigkeiten, bevor Sie die VM-Ressourcen vorbereiten.

    ```azurepowershell-interactive
    $resp = Invoke-AzResourceMoverPrepare -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"  -MoveResource $('psdemovm') -ValidateOnly
    ```
    **Ausgabe**

    ![Ausgabetext nach dem Überprüfen des virtuellen Computers vor der Vorbereitung](./media/tutorial-move-region-powershell/validate-vm-before-move.png)

2. Rufen Sie die abhängigen Ressourcen ab, die zusammen mit dem virtuellen Computer vorbereitet werden müssen.

    ```azurepowershell-interactive
    $resp.AdditionalInfo[0].InfoMoveResource
    ```
    **Ausgabe**

    ![Ausgabetext nach dem Abrufen der abhängigen VM-Ressourcen](./media/tutorial-move-region-powershell/get-resources-before-prepare.png)

3. Initiieren Sie den Vorbereitungsprozess für alle abhängigen Ressourcen.

    ```azurepowershell-interactive
    Invoke-AzResourceMoverPrepare -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"  -MoveResource $('PSDemoVM','psdemovm111', 'PSDemoRM-vnet','PSDemoVM-nsg')
    ```
    **Ausgabe**

    ![Ausgabetext nach dem Initiieren der Vorbereitung aller Ressourcen](./media/tutorial-move-region-powershell/initiate-prepare-all.png)


    > [!NOTE]
    > Als Eingabeparameter für das Vorbereitungs-Cmdlet kann anstelle des Ressourcennamens auch die Quellressourcen-ID angegeben werden. Gleiches gilt für das Cmdlet zum Initiieren der Verschiebung sowie für das Cmdlet zum Committen. Führen Sie dazu Folgendes aus:


    ```azurepowershell-interactive
        Invoke-AzResourceMoverPrepare -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS" -MoveResourceInputType MoveResourceSourceId  -MoveResource $('/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/PSDemoRMS/providers/Microsoft.Network/networkSecurityGroups/PSDemoVM-nsg')
    ```

## <a name="initiate-move-of-vm-resources"></a>Initiieren der Verschiebung von VM-Ressourcen

1. Vergewissern Sie sich, dass sich die VM-Ressourcen im Zustand *InitiateMovePending* (Einleitung der Verschiebung ausstehend) befinden:

    ```azurepowershell-interactive
    Get-AzResourceMoverMoveResource  -SubscriptionId “ xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx “ -ResourceGroupName “RG-MoveCollection-demoRMS” -MoveCollectionName “PS-centralus-westcentralus-demoRMS ”   | Where-Object {  $_.MoveStatusMoveState -eq “InitiateMovePending” } | Select Name
    ```    

    **Ausgabe**

    ![Ausgabetext nach dem Überprüfen des Initiierungszustands](./media/tutorial-move-region-powershell/verify-initiate-move-pending.png)

2. Initiieren Sie die Verschiebung:

    ```azurepowershell-interactive
    Invoke-AzResourceMoverInitiateMove -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"  -MoveResource $('psdemovm111', 'PSDemoRM-vnet','PSDemoVM-nsg', ‘PSDemoVM’) -MoveResourceInputType "MoveResourceId"
    ```    

    **Ausgabe**

    ![Ausgabetext nach dem Initiieren der Ressourcenverschiebung](./media/tutorial-move-region-powershell/initiate-resources-move.png)


## <a name="discard-or-commit"></a>Verwerfen oder committen?

Nach der anfänglichen Verschiebung können Sie entscheiden, ob Sie einen Commit für die Verschiebung durchführen oder sie verwerfen möchten. 

- **Verwerfen**: Sie können eine Verschiebung verwerfen, wenn Sie Tests durchführen und die Quellressource letztendlich nicht verschoben werden soll. Beim Verwerfen der Verschiebung wird die Ressource in den Status *Initiate move pending* (Initiieren der Verschiebung ausstehend) zurückgesetzt. Sie können den Verschiebevorgang dann bei Bedarf noch mal initiieren.
- **Commit**: Beim Commit wird die Verschiebung in die Zielregion abgeschlossen. Nach dem Commit hat eine Quellressource den Status *Delete source pending* (Löschen der Quelle ausstehend), und Sie können entscheiden, ob sie gelöscht werden soll.

### <a name="discard-the-move"></a>Verwerfen der Verschiebung

So verwerfen Sie die Verschiebung

```azurepowershell-interactive
Invoke-AzResourceMoverDiscard -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"  -MoveResource $('psdemovm111', 'PSDemoRM-vnet','PSDemoVM-nsg', ‘PSDemoVM’) -MoveResourceInputType "MoveResourceId"
```
**Ausgabe**

![Ausgabetext nach dem Verwerfen der Verschiebung](./media/tutorial-move-region-powershell/discard-move.png)


### <a name="commit-the-move"></a>Committen der Verschiebung

1. Committen Sie die Verschiebung:

    ```azurepowershell-interactive
    Invoke-AzResourceMoverCommit -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS"  -MoveResource $('psdemovm111', 'PSDemoRM-vnet','PSDemoVM-nsg', ‘PSDemoVM’) -MoveResourceInputType "MoveResourceId"
    ```
    **Ausgabe**

    ![Ausgabetext nach dem Commit der Verschiebung](./media/tutorial-move-region-powershell/commit-move.png)

2. Vergewissern Sie sich, dass alle Ressourcen in die Zielregion verschoben wurden:

    ```azurepowershell-interactive
    Get-AzResourceMoverMoveResource  -ResourceGroupName “RG-MoveCollection-demoRMS ” -MoveCollectionName “PS-centralus-westcentralus-demoRMS”   
    ```
    Alle Ressourcen befinden sich nun im Zustand *Löschung der Quelle ausstehend* in der Zielregion.

## <a name="delete-source-resources"></a>Löschen der Quellressourcen

Nach dem Committen der Verschiebung und der Überprüfung, ob die Ressourcen in der Zielregion erwartungsgemäß funktionieren, können Sie die einzelnen Quellressourcen über das [Azure-Portal](../azure-resource-manager/management/manage-resources-portal.md#delete-resources), mithilfe von [PowerShell](../azure-resource-manager/management/manage-resources-powershell.md#delete-resources) oder über die [Azure CLI](../azure-resource-manager/management/manage-resources-cli.md#delete-resources) löschen.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes durchgeführt:

> [!div class="checklist"]
> * Verschieben virtueller Azure-Computer in eine andere Azure-Region mithilfe von PowerShell
> * Verschieben von Ressourcen, die virtuellen Computern zugeordnet sind, in eine andere Region

Im nächsten Artikel erfahren Sie, wie Sie virtuelle Azure-Computer mithilfe des Portals verschieben:

> [!div class="nextstepaction"]
> [Tutorial: Verschieben von Azure-VMs zwischen Regionen](./tutorial-move-region-virtual-machines.md)


