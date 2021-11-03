---
title: Ändern der Zieleinstellungen beim Verschieben von Azure-VMs zwischen Regionen mithilfe von Azure Resource Mover
description: Erfahren Sie, wie Sie die Zieleinstellungen beim Verschieben von Azure-VMs zwischen Regionen mithilfe von Azure Resource Mover ändern.
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 02/08/2021
ms.author: raynew
ms.openlocfilehash: 54c2594e823e2e78f7a3fd62afba1d0d023ff347
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131048119"
---
# <a name="modify-destination-settings"></a>Ändern der Zieleinstellungen

In diesem Artikel wird beschrieben, wie Sie die Zieleinstellungen ändern, wenn Sie Ressourcen mithilfe von [Azure Resource Mover](overview.md) zwischen Azure-Regionen verschieben.


## <a name="modify-vm-settings"></a>Ändern der VM-Einstellungen

Wenn Sie Azure-VMs (Virtual Machines, virtuelle Computer) und die zugehörigen Ressourcen verschieben, können Sie die Zieleinstellungen ändern. Es wird Folgendes empfohlen:

- Ändern Sie die Zieleinstellungen erst, nachdem die Verschiebungssammlung überprüft wurde. Dabei gilt jedoch Folgendes:
    - Wenn Sie die Quellressource verschieben, können Sie diese Einstellungen in der Regel solange ändern, wie die Verschiebung noch nicht begonnen hat.
    - Wenn Sie eine vorhandene Ressource in der Quellregion zuweisen, können Sie die Zieleinstellungen ändern, bis die Verschiebung beendet ist.
- Dass Sie die Einstellungen ändern, bevor Sie die Ressourcen vorbereiten. Der Grund ist, dass einige Zieleigenschaften nach Abschluss der Vorbereitung ggf. nicht mehr zur Bearbeitung zur Verfügung stehen.

### <a name="settings-you-can-modify"></a>Änderbare Einstellungen

In der folgenden Tabelle sind die Konfigurationseinstellungen zusammengefasst, die Sie bearbeiten können.

**Ressource** | **Optionen** 
--- | --- 
**VM-Name** | Zieloptionen:<br/><br/> - Erstellen einer neuen VM mit demselben Namen in der Zielregion<br/><br/> - Erstellen einer neuen VM mit einem anderen Namen in der Zielregion<br/><br/> - Verwenden einer vorhandenen VM in der Zielregion<br/><br/> Wenn Sie eine neue VM erstellen, werden der neuen Ziel-VM bis auf die von Ihnen geänderten Einstellungen dieselben Einstellungen wie der Quell-VM zugewiesen.
**VM-Verfügbarkeitszone** | Die Verfügbarkeitszone, in der die Ziel-VM platziert wird. Wählen Sie **Nicht zutreffend** aus, wenn Sie die Quelleinstellungen nicht ändern oder die VM in keiner Verfügbarkeitszone platzieren möchten.
**VM-SKU** | Der [VM-Typ](https://azure.microsoft.com/pricing/details/virtual-machines/series/) (verfügbar in der Zielregion), der für die Ziel-VM verwendet wird.<br/><br/> Die ausgewählte Ziel-VM sollte nicht kleiner sein als die Quell-VM.
**VM-Verfügbarkeitsgruppe** | Die Verfügbarkeitsgruppe, in der die Ziel-VM platziert wird. Wählen Sie **Nicht zutreffend** aus, wenn Sie die Quelleinstellungen nicht ändern oder die VM in keiner Verfügbarkeitsgruppe platzieren möchten.
**VM-Schlüsseltresor** | Der zugeordnete Schlüsseltresor, wenn Sie auf einer VM die Azure-Datenträgerverschlüsselung aktivieren.
**Datenträgerverschlüsselungssatz** | Der zugehörige Datenträgerverschlüsselungssatz, wenn die VM einen vom Kunden verwalteten Schlüssel für die serverseitige Verschlüsselung verwendet.
**Ressourcengruppe** | Die Ressourcengruppe, in der die Ziel-VM platziert wird.
**Netzwerkressourcen** | Optionen für Netzwerkschnittstellen, virtuelle Netzwerke (VNets) und Netzwerksicherheitsgruppen/Netzwerkschnittstellen:<br/><br/> - Erstellen einer neuen Ressource mit demselben Namen in der Zielregion<br/><br/> - Erstellen einer neuen Ressource mit einem anderen Namen in der Zielregion<br/><br/> - Verwenden einer vorhandene Netzwerkressource in der Zielregion<br/><br/> Wenn Sie eine neue Zielressource erstellen, werden dieser bis auf die von Ihnen geänderten Einstellungen dieselben Einstellungen wie der Quellressource zugewiesen.
**Name, SKU und Zone der öffentlichen IP-Adresse** | Gibt den Namen, die [SKU](../virtual-network/ip-services/public-ip-addresses.md#sku) und [Zone](../virtual-network/ip-services/public-ip-addresses.md#standard) für die standardmäßige öffentliche IP-Adresse an.<br/><br/> Wenn die Zone redundant sein soll, geben Sie **Zonenredundant** ein.
**Name, SKU und Zone des Lastenausgleichs** | Gibt den Namen, die SKU (Basic oder Standard) und Zone des Lastenausgleichs an.<br/><br/> Wir empfehlen die Verwendung der Standard-SKU.<br/><br/> Wenn die Zone redundant sein soll, geben Sie sie als **Zonenredundant** an.
**Ressourcenabhängigkeiten** | Optionen für die einzelnen Abhängigkeiten:<br/><br/>- Die Ressource verwendet quellenabhängige Ressourcen, die in die Zielregion verschoben werden.<br/><br/> - Die Ressource verwendet andere abhängige Ressourcen, die sich in der Zielregion befinden. In diesem Fall können Sie aus beliebigen ähnlichen Ressourcen in der Zielregion auswählen.

### <a name="edit-vm-destination-settings"></a>Bearbeiten der VM-Zieleinstellungen

Wenn Sie abhängige Ressourcen nicht aus der Quellregion ins Ziel verschieben möchten, haben Sie eine Reihe anderer Optionen:

- Erstellen Sie eine neue Ressource in der Zielregion. Die neue Ressource verfügt über dieselben Einstellungen wie die Quellressource, es sei denn, Sie geben andere Einstellungen an.
- Verwenden Sie eine vorhandene Ressource in der Zielregion.

Das genaue Verhalten hängt vom Ressourcentyp ab. [Weitere Informationen zur Bearbeitung von Zieleinstellungen](modify-target-settings.md)

Sie können die Zieleinstellungen für eine Ressource mithilfe des Eintrags **Destination configuration** (Zielkonfiguration) in der Sammlung für die Ressourcenverschiebung ändern. 

So ändern Sie einen Filter 

1. Klicken Sie auf der Seite **Across regions** (Regionsübergreifend) in der Spalte **Destination configuration** (Zielkonfiguration) auf den Link für den Ressourceneintrag.
2. Unter **Konfigurationseinstellungen** können Sie eine neue VM in der Zielregion erstellen.
3. Weisen Sie der Ziel-VM eine neue Verfügbarkeitszone, Verfügbarkeitsgruppe oder SKU zu. **Verfügbarkeitszone** und **SKU**.

Änderungen werden an der Ressource vorgenommen, die Sie bearbeiten. Alle abhängigen Ressourcen müssen separat aktualisiert werden.


## <a name="modify-sql-settings"></a>Ändern der SQL-Einstellungen

Wenn Sie Azure SQL-Datenbank-Ressourcen verschieben, können Sie die Zieleinstellungen für die Verschiebung ändern. 

- Für SQL-Datenbank:
    - Es wird empfohlen, die Zielkonfigurationseinstellungen vor der Vorbereitung der Verschiebung zu ändern.
    - Sie können die Einstellungen für die Zieldatenbank sowie Zonenredundanz der Datenbank ändern.
- Bei Pools für elastische Datenbanken:
    -  Sie können die Zielkonfiguration jederzeit ändern, bevor die Verschiebung eingeleitet wird.
    - Sie können den Zielpool für elastische Datenbanken sowie die Zonenredundanz des Pool ändern. 

### <a name="sql-settings-you-can-modify"></a>Änderbare SQL-Einstellungen

**Einstellung** | **SQL-Datenbank** | **Pool für elastische Datenbanken**
--- | --- | ---
**Name** | Erstellen einer neuen Datenbank mit demselben Namen in der Zielregion<br/><br/> Erstellen einer neuen Datenbank mit einem anderen Namen in der Zielregion<br/><br/> Verwenden einer vorhandenen Datenbank in der Zielregion | Erstellen eines neuen Pools für elastische Datenbanken mit demselben Namen in der Zielregion<br/><br/> Erstellen eines neuen Pools für elastische Datenbanken mit einem anderen Namen in der Zielregion<br/><br/> Verwenden eines vorhandenen Pools für elastische Datenbanken in der Zielregion
**Zonenredundanz** | Wenn Sie Ressourcen von einer Region, die Zonenredundanz unterstützt, in eine Region verschieben möchten, die keine Zonenredundanz unterstützt, geben Sie **Deaktivieren** in der Zoneneinstellung ein.<br/><br/> Wenn Sie Ressourcen von einer Region, die keine Zonenredundanz unterstützt, in eine Region verschieben möchten, die Zonenredundanz unterstützt, geben Sie **Aktivieren** in der Zoneneinstellung ein. | Wenn Sie Ressourcen von einer Region, die Zonenredundanz unterstützt, in eine Region verschieben möchten, die keine Zonenredundanz unterstützt, geben Sie **Deaktivieren** in der Zoneneinstellung ein.<br/><br/> Wenn Sie Ressourcen von einer Region, die keine Zonenredundanz unterstützt, in eine Region verschieben möchten, die Zonenredundanz unterstützt, geben Sie **Aktivieren** in der Zoneneinstellung ein.

### <a name="edit-sql-destination-settings"></a>Bearbeiten der SQL-Zieleinstellungen

Die Zieleinstellungen für eine Azure SQL-Datenbank-Ressource werden wie folgt geändert: 

1. Klicken Sie unter **Across regions** (Regionsübergreifend) für die Ressource, die Sie ändern möchten, auf den Eintrag **Destination configuration** (Zielkonfiguration).
2. Geben Sie unter **Konfigurationseinstellungen** die in der obigen Tabelle zusammengefassten Zieleinstellungen an.


## <a name="modify-settings-in-powershell"></a>Ändern von Einstellungen in PowerShell

Sie können Einstellungen in PowerShell ändern.

1)  Rufen Sie die Verschiebungsressource ab, für die Sie Eigenschaften bearbeiten möchten. So rufen Sie beispielsweise eine VM-Ausführung ab:

    ```azurepowershell
    $moveResourceObj = Get-AzResourceMoverMoveResource -MoveCollectionName "PS-centralus-westcentralus-demoRMS1" -ResourceGroupName "RG-MoveCollection-demoRMS" -Name "PSDemoVM"
    ```
2)  Kopieren Sie die Ressourceneinstellung in ein Zielressourcen-Einstellungsobjekt.

    ```azurepowershell
    $TargetResourceSettingObj = $moveResourceObj.ResourceSetting
    ```

3)  Legen Sie den Parameter im Zielressourcen-Einstellungsobjekt fest. So ändern Sie beispielsweise den Namen des virtuellen Zielcomputers:

    ```azurepowershell
    $TargetResourceSettingObj.TargetResourceName="PSDemoVM-target"
    ```

4)  Aktualisieren Sie die Einstellungen für das Ziel der Ressourcenverschiebung. In diesem Beispiel ändern wir den Namen des virtuellen Computers von *PSDemoVM* in *PSDemoVMTarget*.

    ```azurepowershell
    Update-AzResourceMoverMoveResource -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS" -SourceId "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/PSDemoRM/providers/Microsoft.Compute/virtualMachines/PSDemoVM" -Name "PSDemoVM" -ResourceSetting $TargetResourceSettingObj
    ```
    **Ausgabe**
    ![Ausgabetext nach Änderung der Zieleinstellungen](./media/modify-target-settings/update-settings.png)


## <a name="next-steps"></a>Nächste Schritte

[Verschieben Sie eine Azure-VM](tutorial-move-region-virtual-machines.md) in eine andere Region.