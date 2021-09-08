---
title: Bereitstellen von dedizierten Azure-Hosts über das Azure-Portal
description: Stellen Sie VMs und Skalierungsgruppen über das Azure-Portal auf dedizierten Hosts bereit.
author: cynthn
ms.service: virtual-machines
ms.subservice: dedicated-hosts
ms.topic: how-to
ms.workload: infrastructure
ms.date: 12/07/2020
ms.author: cynthn
ms.openlocfilehash: d601f215beae8c4fcb1977a08210429408f94e52
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122696474"
---
# <a name="deploy-vms-and-scale-sets-to-dedicated-hosts-using-the-portal"></a>Bereitstellen von VMs und Skalierungsgruppen auf dedizierten Hosts über das Portal

**Gilt für**: :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows VMs :heavy_check_mark: Einheitliche Skalierungsgruppen

Dieser Artikel führt Sie durch die Erstellung eines [dedizierten Azure-Hosts](dedicated-hosts.md) zum Hosten Ihrer virtuellen Computer (VMs). 

## <a name="limitations"></a>Einschränkungen

- Die verfügbaren Größen und Hardwaretypen für dedizierte Hosts variieren je nach Region. Weitere Informationen finden Sie unter [Azure Dedicated Host – Preise](https://aka.ms/ADHPricing).

## <a name="create-a-host-group"></a>Erstellen einer Hostgruppe

Eine **Hostgruppe** ist eine Ressource, die eine Sammlung dedizierter Hosts darstellt. Sie erstellen eine Hostgruppe in einer Region und einer Verfügbarkeitszone und fügen ihr Hosts hinzu. Bei der Planung für Hochverfügbarkeit stehen zusätzliche Optionen zur Verfügung. Sie können eine oder beide der folgenden Optionen mit Ihren dedizierten Hosts verwenden:
- Ausdehnen über mehrere Verfügbarkeitszonen. In diesem Fall muss in jeder Zone, die Sie verwenden möchten, eine Hostgruppe vorhanden sein.
- Ausdehnen über mehrere Fehlerdomänen, die physischen Racks zugeordnet sind.

In jedem Fall müssen Sie die Anzahl der Fehlerdomänen für Ihre Hostgruppe angeben. Wenn Ihre Gruppe keine Fehlerdomänen umfassen soll, verwenden Sie die Anzahl 1 von Fehlerdomänen.

Sie können auch sowohl Verfügbarkeitszonen als auch Fehlerdomänen verwenden.

In diesem Beispiel erstellen Sie eine Hostgruppe mit einer Verfügbarkeitszone und zwei Fehlerdomänen.


1. Öffnen Sie das [Azure-Portal](https://portal.azure.com).
1. Wählen Sie links oben **Ressource erstellen** aus.
1. Suchen Sie nach **Hostgruppe**, und wählen Sie dann in den Ergebnissen **Hostgruppen** aus.
1. Wählen Sie auf der Seite **Hostgruppen** die Option **Erstellen** aus.
1. Wählen Sie das Abonnement aus, das Sie verwenden möchten, und wählen Sie dann **Neu erstellen** aus, um eine neue Ressourcengruppe zu erstellen.
1. Geben Sie *myDedicatedHostsRG* unter **Name** ein, und wählen Sie dann **OK** aus.
1. Geben Sie unter **Host group name** (Name der Hostgruppe) die Zeichenfolge *myHostGroup* ein.
1. Wählen Sie unter **Standort** die Option **USA, Osten** aus.
1. Wählen Sie unter **Verfügbarkeitszone** die Option **1** aus.
1. Wählen Sie für **Fault domain count** (Anzahl von Fehlerdomänen) die Option **2** aus.
1. Wählen Sie **Automatische Platzierung** aus, um virtuelle Computer und Skalierungsgruppeninstanzen automatisch einem verfügbaren Host in dieser Gruppe zuzuweisen.
1. Wählen Sie **Bewerten + erstellen** aus, und warten Sie auf die Überprüfung.
1. Wenn die Meldung **Überprüfung erfolgreich** angezeigt wird, wählen Sie **Erstellen** aus, um die Hostgruppe zu erstellen.

Die Erstellung der Hostgruppe sollte nur wenige Sekunden dauern.


## <a name="create-a-dedicated-host"></a>Erstellen eines dedizierten Hosts

Erstellen Sie nun einen dedizierten Host in der Hostgruppe. Zusätzlich zu einem Namen müssen Sie die SKU für den Host angeben. Die Host-SKU erfasst die unterstützte VM-Serie sowie die Hardwaregenerierung für Ihren dedizierten Host.

Weitere Informationen zu den Host-SKUs und Preisen finden Sie unter [Azure Dedicated Host – Preise](https://aka.ms/ADHPricing).

Beim Festlegen der Anzahl der Fehlerdomänen für die Hostgruppe werden Sie aufgefordert, die Fehlerdomäne für den Host anzugeben.

1. Wählen Sie links oben **Ressource erstellen** aus.
1. Suchen Sie nach **Dedizierter Host**, und wählen Sie dann in den Ergebnissen **Dedizierter Host** aus.
1. Wählen Sie auf der Seite **Dedizierter Host** die Option **Erstellen** aus.
1. Wählen Sie das Abonnement aus, das Sie verwenden möchten.
1. Wählen Sie *myDedicatedHostsRG* unter **Ressourcengruppe** aus.
1. Geben Sie unter **Instanzdetails** die Zeichenfolge *myHost* für **Name** ein, und wählen Sie als Standort *USA, Osten* aus.
1. Wählen Sie in **Hardwareprofil** die Option *Standard Es3 family – Type 1* (Standard-Es3-Familie – Typ 1) unter **Größenfamilie**, *myHostGroup* unter **Hostgruppe** und dann *1* unter **Fehlerdomäne** aus. Behalten Sie in den restlichen Feldern die Standardwerte bei.
1. Wählen Sie nach Abschluss **Bewerten + erstellen** aus, und warten Sie auf die Validierung.
1. Wenn die Meldung **Überprüfung erfolgreich** angezeigt wird, wählen Sie **Erstellen** aus, um den Host zu erstellen.

## <a name="create-a-vm"></a>Erstellen einer VM

1. Klicken Sie links oben im Azure-Portal auf **Ressource erstellen**.
1. Suchen Sie im Suchfeld über der Liste mit den Azure Marketplace-Ressourcen nach dem gewünschten Image, wählen Sie es aus, und klicken Sie anschließend auf **Erstellen**.
1. Stellen Sie auf der Registerkarte **Grundlagen** unter **Projektdetails** sicher, dass das richtige Abonnement ausgewählt ist, und wählen Sie dann *myDedicatedHostsRG* als **Ressourcengruppe** aus.
1. Geben Sie unter **Instanzdetails** *myVM* als **Namen des virtuellen Computers** ein, und wählen Sie *USA, Osten* als Ihre **Ort** aus.
1. Wählen Sie unter **Verfügbarkeitsoptionen** die Option **Verfügbarkeitszone** aus, und wählen Sie in der Dropdownliste *1* aus.
1. Wählen Sie für die Größe **Größe ändern** aus. Wählen Sie in der Liste der verfügbaren Größen eine der Esv3-Serie aus, z. B. **Standard E2s v3**. Möglicherweise müssen Sie den Filter löschen, um alle verfügbaren Größen anzuzeigen.
1. Füllen Sie die restlichen Felder auf der Registerkarte **Grundeinstellungen** nach Bedarf aus.
1. Wenn Sie angeben möchten, welchen Host Sie für Ihre VM verwenden möchten, wählen Sie oben auf der Seite die Registerkarte **Erweitert** und im Abschnitt **Host** die Option *myHostGroup* für die **Hostgruppe** und *myHost* für den **Host** aus. Andernfalls wird Ihr virtueller Computer automatisch auf einem Host mit Kapazität platziert.
    ![Auswählen von Hostgruppe und Host](./media/dedicated-hosts-portal/advanced.png)
1. Belassen Sie die übrigen Standardeinstellungen, und wählen Sie dann die Schaltfläche **Überprüfen + erstellen** am unteren Rand der Seite aus.
1. Wenn eine Meldung über die erfolgreiche Validierung angezeigt wird, wählen Sie **Erstellen** aus.

Die Bereitstellung des virtuellen Computers dauert ein paar Minuten.

## <a name="create-a-scale-set"></a>Erstellen einer Skalierungsgruppe

Wenn Sie eine Skalierungsgruppe bereitstellen, geben Sie die Hostgruppe an.

1. Suchen Sie nach *Skalierungsgruppe*, und wählen Sie in der Liste **VM-Skalierungsgruppen** aus.
1. Klicken Sie auf **Hinzufügen**, um eine neue Skalierungsgruppe zu erstellen.
1. Füllen Sie die Felder auf der Registerkarte **Grundeinstellungen** wie gewohnt aus. Stellen Sie dabei jedoch sicher, dass Sie eine VM-Größe derselben Serie auswählen wie bei Ihrem dedizierten Host, z. B. **Standard E2s v3**.
1. Wählen Sie auf der Registerkarte **Erweitert** für **Zuweisungsalgorithmus** die Option **Maximale Zuweisung** aus.
1. Wählen Sie bei **Hostgruppe** die gewünschte Hostgruppe in der Dropdownliste aus. Wenn Sie die Gruppe vor kurzem erstellt haben, kann es eine Minute dauern, bis diese in der Liste hinzugefügt wurde.

## <a name="add-an-existing-vm"></a>Hinzufügen eines vorhandenen virtuellen Computers

Sie können einem dedizierten Host eine VM hinzufügen. Diese muss allerdings zuerst beendet bzw. Ihre Zuordnung muss aufgehoben werden. Vergewissern Sie sich vor dem Verschieben eines virtuellen Computers auf einen dedizierten Host, dass die VM-Konfiguration unterstützt wird:

- Die VM-Größe muss sich in der gleichen Größenfamilie befinden wie der dedizierte Host. Wenn der dedizierte Host z. B. DSv3 ist, kann die VM-Größe Standard_D4s_v3, aber nicht Standard_A4_v2 sein.
- Der virtuelle Computer muss sich in der gleichen Region befinden wie der dedizierte Host.
- Der virtuelle Computer darf nicht Teil einer Näherungsplatzierungsgruppe sein. Entfernen Sie den virtuellen Computer aus der Näherungsplatzierungsgruppe, bevor Sie ihn auf einen dedizierten Host verschieben. Weitere Informationen finden Sie unter [Verschieben einer vorhandenen VM aus einer Näherungsplatzierungsgruppe](./windows/proximity-placement-groups.md#move-an-existing-vm-out-of-a-proximity-placement-group).
- Der virtuelle Computer darf sich nicht in einer Verfügbarkeitsgruppe befinden.
- Wenn sich der virtuelle Computer in einer Verfügbarkeitszone befindet, muss es sich dabei um die gleiche Verfügbarkeitszone handeln wie bei der Hostgruppe. Die Verfügbarkeitszoneneinstellungen für den virtuellen Computer und die Hostgruppe müssen identisch sein.

Verschieben Sie den virtuellen Computer mithilfe des [Portals](https://portal.azure.com) auf einen dedizierten Host.

1. Öffnen Sie die Seite für den virtuellen Computer.
1. Wählen Sie **Beenden** aus, um den virtuellen Computer zu beenden bzw. seine Zuordnung aufzuheben.
1. Wählen Sie im Menü auf der linken Seite die Option **Konfiguration** aus.
1. Wählen Sie in den entsprechenden Dropdownmenüs eine Hostgruppe und einen Host aus.
1. Wählen Sie abschließend im oberen Bereich der Seite **Speichern** aus.
1. Klicken Sie im linken Menü auf **Übersicht**, nachdem der virtuelle Computer dem Host hinzugefügt wurde.
1. Wählen Sie oben auf der Seite **Starten** aus, um den virtuellen Computer neu zu starten.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen finden Sie in der Übersicht zu [dedizierten Hosts](dedicated-hosts.md).

- Bei den [Azure-Schnellstartvorlagen](https://github.com/Azure/azure-quickstart-templates/blob/master/quickstarts/microsoft.compute/vm-dedicated-hosts/README.md) finden Sie eine Beispielvorlage, die sowohl Zonen als auch Fehlerdomänen für maximale Resilienz in einer Region verwendet.

- Sie können einen dedizierten Host auch über die [Azure CLI](./linux/dedicated-hosts-cli.md) oder [PowerShell](./windows/dedicated-hosts-powershell.md) bereitstellen.
