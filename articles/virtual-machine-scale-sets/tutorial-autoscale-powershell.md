---
title: 'Tutorial: Automatisches Skalieren einer Skalierungsgruppe mit Azure PowerShell'
description: Es wird beschrieben, wie Sie eine VM-Skalierungsgruppe mit Azure PowerShell automatisch skalieren, wenn sich die CPU-Anforderungen erhöhen oder verringern.
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.subservice: autoscale
ms.date: 03/27/2018
ms.reviewer: avverma
ms.custom: avverma, devx-track-azurepowershell
ms.openlocfilehash: e7caa3f2495748305621ff20d5fc553d4d26ea57
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122693059"
---
# <a name="tutorial-automatically-scale-a-virtual-machine-scale-set-with-azure-powershell"></a>Tutorial: Automatisches Skalieren einer VM-Skalierungsgruppe mit Azure PowerShell

**Gilt für:** :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows-VMs :heavy_check_mark: Flexible Skalierungsgruppen :heavy_check_mark: Einheitliche Skalierungsgruppen

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

Beim Erstellen einer Skalierungsgruppe definieren Sie die Anzahl von VM-Instanzen, die Sie ausführen möchten. Wenn sich die Nachfrage nach Ihrer Anwendung ändert, können Sie die Anzahl von VM-Instanzen automatisch erhöhen oder verringern lassen. Dank der Möglichkeit zum automatischen Skalieren können Sie über den gesamten Lebenszyklus Ihrer App die Kundennachfrage decken oder auf Änderungen der Anwendungsleistung reagieren. In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Verwenden der automatischen Skalierung mit einer Skalierungsgruppe
> * Erstellen und Verwenden von Regeln für die automatische Skalierung
> * Durchführen eines Belastungstests für VM-Instanzen und Auslösen von Regeln für die automatische Skalierung
> * Zurückfahren der automatischen Skalierung bei einer Reduzierung des Bedarfs

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

Es gibt ein bekanntes Problem für die Version 6.8.1 und höhere Versionen des Azure PowerShell-Moduls. Davon ist auch die aktuelle Version von Azure Cloud Shell betroffen. Für dieses Tutorial können nur die Versionen 6.0.0 bis 6.8.0 des Azure PowerShell-Moduls verwendet werden. Führen Sie `Get-Module -ListAvailable AzureRM` aus, um die Version zu ermitteln. Wenn Sie PowerShell lokal ausführen, müssen Sie auch `Connect-AzureRmAccount` ausführen, um eine Verbindung mit Azure herzustellen.


## <a name="create-a-scale-set"></a>Erstellen einer Skalierungsgruppe
Definieren Sie einige Variablen für Ihre Skalierungsgruppe, um die Erstellung der Regeln für die automatische Skalierung zu vereinfachen. Im folgenden Beispiel werden Variablen für die Skalierungsgruppe *myScaleSet* in der Ressourcengruppe *myResourceGroup* in der Region *USA, Osten* definiert. Sie können Ihre Abonnement-ID mit [Get-AzureRmSubscription](/powershell/module/azurerm.profile/get-azurermsubscription) abrufen. Wenn Ihrem Konto mehrere Abonnements zugeordnet sind, wird nur das erste Abonnement zurückgegeben. Passen Sie die Namen und die Abonnement-ID wie folgt an:

```azurepowershell-interactive
$mySubscriptionId = (Get-AzureRmSubscription)[0].Id
$myResourceGroup = "myResourceGroup"
$myScaleSet = "myScaleSet"
$myLocation = "East US"
```

Erstellen Sie nun mit [New-AzureRmVmss](/powershell/module/azurerm.compute/new-azurermvmss) eine VM-Skalierungsgruppe. Um Datenverkehr an die einzelnen VM-Instanzen zu verteilen, wird auch ein Lastenausgleich erstellt. Der Lastenausgleich enthält Regeln zum Verteilen von Datenverkehr über TCP-Port 80 und zum Zulassen von Remotedesktop-Datenverkehr über TCP-Port 3389 und PowerShell-Remoting über TCP-Port 5985. Geben Sie Ihre gewünschten Administratoranmeldeinformationen für die VM-Instanzen in der Skalierungsgruppe an, wenn Sie dazu aufgefordert werden:

```azurepowershell-interactive
New-AzureRmVmss `
  -ResourceGroupName $myResourceGroup `
  -VMScaleSetName $myScaleSet `
  -Location $myLocation `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer"
```

Die Erstellung und Konfiguration aller Ressourcen und virtuellen Computer der Skalierungsgruppe dauert einige Minuten.

## <a name="create-a-rule-to-autoscale-out"></a>Erstellen einer Regel zum automatischen Aufskalieren
Wenn sich die Nachfrage für Ihre Anwendung erhöht, erhöht sich auch die Last für die VM-Instanzen in Ihrer Skalierungsgruppe. Falls es sich um eine dauerhafte Last und nicht nur um eine kurzzeitige höhere Nachfrage handelt, können Sie die Regeln für die automatische Skalierung konfigurieren, um die Anzahl von VM-Instanzen in der Skalierungsgruppe zu erhöhen. Nachdem diese VM-Instanzen erstellt und Ihre Anwendungen bereitgestellt wurden, beginnt die Skalierungsgruppe damit, über das Lastenausgleichsmodul Datenverkehr darauf zu verteilen. Sie steuern, welche Metriken überwacht werden, z.B. CPU oder Datenträger, wie lange die Anwendungslast einen bestimmten Schwellenwert einhalten muss und wie viele VM-Instanzen der Skalierungsgruppe hinzugefügt werden sollen.

Wir erstellen mit [New-AzureRmAutoscaleRule](/powershell/module/AzureRM.Insights/New-AzureRmAutoscaleRule) eine Regel, mit der die Anzahl von VM-Instanzen in einer Skalierungsgruppe erhöht wird, wenn die durchschnittliche CPU-Last für einen Zeitraum von 5 Minuten über 70 % liegt. Wenn die Regel ausgelöst wird, wird die Anzahl von VM-Instanzen um 3 erhöht.

Für diese Regel werden die folgenden Parameter verwendet:

| Parameter               | Erklärung                                                                                                         | Wert          |
|-------------------------|---------------------------------------------------------------------------------------------------------------------|----------------|
| *-MetricName*           | Die Leistungsmetrik zum Überwachen und Anwenden von Skalierungsgruppenaktionen.                                                   | CPU in Prozent |
| *-TimeGrain*            | Gibt an, wie häufig Metriken zu Analysezwecken gesammelt werden.                                                                   | 1 Minute       |
| *-MetricStatistic*      | Definiert, wie die gesammelten Metriken zu Analysezwecken aggregiert werden sollen.                                                | Average        |
| *-TimeWindow*           | Der überwachte Zeitraum, bevor die Metrik und Schwellenwerte verglichen werden.                                   | 5 Minuten      |
| *-Operator*             | Operator zum Vergleichen der Metrikdaten mit dem Schwellenwert.                                                     | Größer als   |
| *-Threshold*            | Der Wert, der für die Regel für die automatische Skalierung das Auslösen einer Aktion bewirkt.                                                      | 70 %            |
| *-ScaleActionDirection* | Definiert, ob die Skalierungsgruppe hoch- oder herunterskaliert werden soll, wenn die Regel zutrifft.                                             | Erhöhung       |
| *-ScaleActionScaleType* | Gibt an, dass die Anzahl von VM-Instanzen um einen bestimmten Wert geändert werden soll.                                    | Änderungsanzahl   |
| *-ScaleActionValue*     | Der Prozentsatz der VM-Instanzen sollte geändert werden, wenn diese Regel ausgelöst wird.                                            | 3              |
| *-ScaleActionCooldown*  | Gibt an, wie lange gewartet wird, bevor die Regel erneut angewendet wird, damit die Aktionen für die automatische Skalierung wirksam werden können. | 5 Minuten      |

Das folgende Beispiel erstellt das Objekt *myRuleScaleOut*, das diese Regel für das Hochskalieren enthält. Die *-MetricResourceId* verwendet die Variablen, die zuvor für die Abonnement-ID, den Namen der Ressourcengruppe und den Namen der Skalierungsgruppe definiert wurden:

```azurepowershell-interactive
$myRuleScaleOut = New-AzureRmAutoscaleRule `
  -MetricName "Percentage CPU" `
  -MetricResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -TimeGrain 00:01:00 `
  -MetricStatistic "Average" `
  -TimeWindow 00:05:00 `
  -Operator "GreaterThan" `
  -Threshold 70 `
  -ScaleActionDirection "Increase" `
  -ScaleActionScaleType "ChangeCount" `
  -ScaleActionValue 3 `
  -ScaleActionCooldown 00:05:00
```


## <a name="create-a-rule-to-autoscale-in"></a>Erstellen einer Regel zum automatischen Abskalieren
Es kann sein, dass die Nachfrage nach Ihrer Anwendung abends oder am Wochenende abnimmt. Wenn diese Verringerung der Last für einen bestimmten Zeitraum anhält, können Sie Regeln zur automatischen Skalierung konfigurieren, um die Anzahl von VM-Instanzen in der Skalierungsgruppe zu reduzieren. Mit dieser Aktion zum horizontalen Herunterskalieren werden die Kosten für die Ausführung Ihrer Skalierungsgruppe gesenkt, da Sie nur so viele Instanzen ausführen, wie für die Erfüllung der derzeitigen Nachfrage erforderlich sind.

Erstellen Sie mit [New-AzureRmAutoscaleRule](/powershell/module/AzureRM.Insights/New-AzureRmAutoscaleRule) eine weitere Regel, mit der die Anzahl von VM-Instanzen in einer Skalierungsgruppe verringert wird, wenn die durchschnittliche CPU-Last 5 Minuten lang unter 30% fällt. Wenn die Regel ausgelöst wird, wird die Anzahl von VM-Instanzen um eins verringert. Das folgende Beispiel erstellt das Objekt *myRuleScaleDown*, das diese Regel für das Herunterskalieren enthält. Die *-MetricResourceId* verwendet die Variablen, die zuvor für die Abonnement-ID, den Namen der Ressourcengruppe und den Namen der Skalierungsgruppe definiert wurden:

```azurepowershell-interactive
$myRuleScaleIn = New-AzureRmAutoscaleRule `
  -MetricName "Percentage CPU" `
  -MetricResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -Operator "LessThan" `
  -MetricStatistic "Average" `
  -Threshold 30 `
  -TimeGrain 00:01:00 `
  -TimeWindow 00:05:00 `
  -ScaleActionCooldown 00:05:00 `
  -ScaleActionDirection "Decrease" `
  -ScaleActionScaleType "ChangeCount" `
  -ScaleActionValue 1
```


## <a name="define-an-autoscale-profile"></a>Definieren eines Profils für die automatische Skalierung
Erstellen Sie ein Profil, um Ihre Regeln zur automatischen Skalierung einer Skalierungsgruppe zuzuordnen. Das Profil für die automatische Skalierung definiert die Standard-, Mindest- und Höchstkapazität der Skalierungsgruppe und ordnet Regeln zur automatischen Skalierung zu. Erstellen Sie mit [New-AzureRmAutoscaleProfile](/powershell/module/AzureRM.Insights/New-AzureRmAutoscaleProfile) ein Profil für die automatische Skalierung. Im folgenden Beispiel werden als Standard- und Mindestwert *2* und als Maximalwert *10* VM-Instanzen festgelegt. Anschließend werden die Regeln für das Auf- und Abskalieren aus den vorherigen Schritten zugeordnet:

```azurepowershell-interactive
$myScaleProfile = New-AzureRmAutoscaleProfile `
  -DefaultCapacity 2  `
  -MaximumCapacity 10 `
  -MinimumCapacity 2 `
  -Rule $myRuleScaleOut,$myRuleScaleIn `
  -Name "autoprofile"
```


## <a name="apply-autoscale-profile-to-a-scale-set"></a>Anwenden eines Autoskalierungsprofils auf eine Skalierungsgruppe
Der letzte Schritt ist das Anwenden des Profils für die automatische Skalierung auf Ihre Skalierungsgruppe. Ihre Skalierungsgruppe kann dann automatisch je nach Anwendungsnachfrage das Ab- oder Aufskalieren durchführen. Sie wenden das Profil für die automatische Skalierung wie folgt mit [Add-AzureRmAutoscaleSetting](/powershell/module/AzureRM.Insights/Add-AzureRmAutoscaleSetting) an:

```azurepowershell-interactive
Add-AzureRmAutoscaleSetting `
  -Location $myLocation `
  -Name "autosetting" `
  -ResourceGroup $myResourceGroup `
  -TargetResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -AutoscaleProfile $myScaleProfile
```


## <a name="generate-cpu-load-on-scale-set"></a>Generieren einer CPU-Last in einer Skalierungsgruppe
Generieren Sie zum Testen der Regeln für die automatische Skalierung eine CPU-Last auf den VM-Instanzen in der Skalierungsgruppe. Diese simulierte CPU-Last bewirkt über die Regeln für die automatische Skalierung das Aufskalieren und eine Erhöhung der Anzahl von VM-Instanzen. Wenn die simulierte CPU-Last dann verringert wird, wird über die Regeln für das automatische Skalieren das Abskalieren durchgeführt und die Anzahl von VM-Instanzen reduziert.

Rufen Sie zuerst mit [Get-AzureRmLoadBalancer](/powershell/module/AzureRM.Network/Get-AzureRmLoadBalancer) das Lastenausgleichsobjekt ab, um die NAT-Ports zum Herstellen einer Verbindung mit den VM-Instanzen in einer Skalierungsgruppe aufzulisten. Zeigen Sie anschließend mit [Get-AzureRmLoadBalancerInboundNatRuleConfig](/powershell/module/AzureRM.Network/Get-AzureRmLoadBalancerInboundNatRuleConfig) die NAT-Eingangsregeln an:

```azurepowershell-interactive
# Get the load balancer object
$lb = Get-AzureRmLoadBalancer -ResourceGroupName "myResourceGroup" -Name "myLoadBalancer"

# View the list of inbound NAT rules
Get-AzureRmLoadBalancerInboundNatRuleConfig -LoadBalancer $lb | Select-Object Name,Protocol,FrontEndPort,BackEndPort
```

Die folgende Beispielausgabe enthält den Instanznamen, die öffentliche IP-Adresse des Lastenausgleichs und die Portnummer, an die Datenverkehr von den NAT-Regeln weitergeleitet wird:

```powershell
Name        Protocol FrontendPort BackendPort
----        -------- ------------ -----------
myRDPRule.0 Tcp             50001        3389
myRDPRule.1 Tcp             50002        3389
```

Der *Name* der Regel ist am Namen der VM-Instanz ausgerichtet, wie im vorherigen Befehl [Get-AzureRmVmssVM](/powershell/module/azurerm.compute/get-azurermvmssvm) zu sehen ist. Wenn Sie beispielsweise eine Verbindung mit der VM-Instanz *0* herstellen möchten, verwenden Sie *myRDPRule.0* und stellen die Verbindung mit Port *50001* her. Verwenden Sie zum Herstellen einer Verbindung mit der VM-Instanz *1* den Wert aus *myRDPRule.1*, und stellen Sie eine Verbindung mit Port *50002* her.

Zeigen Sie mit [Get-AzureRmPublicIpAddress](/powershell/module/AzureRM.Network/Get-AzureRmPublicIpAddress) die öffentliche IP-Adresse des Lastenausgleichs an:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -ResourceGroupName "myResourceGroup" -Name myPublicIP | Select IpAddress
```

Beispielausgabe:

```powershell
IpAddress
---------
52.168.121.216
```

Erstellen Sie eine Remoteverbindung mit Ihrer ersten VM-Instanz. Geben Sie Ihre eigene öffentliche IP-Adresse und Portnummer der erforderlichen VM-Instanz an, wie in den vorherigen Befehlen zu sehen. Geben Sie bei entsprechender Aufforderung die Anmeldeinformationen ein, die Sie beim Erstellen der Skalierungsgruppe verwendet haben (in den Beispielbefehlen standardmäßig *azureuser* und *P\@ssw0rd!* ). Führen Sie diesen Schritt bei Verwendung von Azure Cloud Shell an einer lokalen PowerShell-Eingabeaufforderung oder über einen Remotedesktopclient aus. Im folgenden Beispiel wird eine Verbindung mit der VM-Instanz *0* hergestellt:

```powershell
mstsc /v 52.168.121.216:50001
```

Öffnen Sie nach dem Anmelden über die Taskleiste Internet Explorer.

- Wählen Sie **OK**, um *Empfohlene Sicherheits-, Datenschutz- und Kompatibilitätseinstellungen verwenden* zu akzeptieren.
- Geben Sie in der Adressleiste *http://download.sysinternals.com/files/CPUSTRES.zip* ein.
- Da die verstärkte Sicherheitskonfiguration für Internet Explorer aktiviert ist, können Sie **Hinzufügen** wählen, um die Domäne *http://download.sysinternals.com* Ihrer Liste mit den vertrauenswürdigen Sites hinzuzufügen.
- Wählen Sie bei Aufforderung zum Herunterladen der Datei die Option **Öffnen**, wählen Sie das Tool *CPUSTRES.EXE* aus, und wählen Sie **Ausführen**, um das Tool auszuführen.

Aktivieren Sie für zwei Threads das Kontrollkästchen **Aktiv**, um eine CPU-Last zu generieren. Wählen Sie im Dropdownmenü **Aktivität** für beide Threads die Option *Maximum*. Sie können den Task-Manager öffnen, um sich zu vergewissern, dass die CPU-Last für die VM bei 100% liegt.

![Hilfsprogramm CPU Stress generiert Last auf der VM-Instanz](media/tutorial-autoscale-powershell/cpu-stress-load.PNG)

Lassen Sie die Remotedesktop-Verbindungssitzung geöffnet, und öffnen Sie eine weitere Sitzung dieser Art. Stellen Sie für die zweite VM-Instanz eine Verbindung mit der Portnummer aus dem vorherigen [Get-AzureRmLoadBalancerInboundNatRuleConfig](/powershell/module/AzureRM.Network/Get-AzureRmLoadBalancerInboundNatRuleConfig)-Cmdlet her:

```powershell
mstsc /v 52.168.121.216:50002
```

Wiederholen Sie die vorherigen Schritte zum Herunterladen und Ausführen von *CPUSTRES.EXE*, nachdem Sie sich an der zweiten VM-Instanz angemeldet haben. Legen Sie erneut zwei Threads auf **Aktiv** und die Aktivität auf *Maximum* fest.

Lassen Sie beide Remotedesktop-Verbindungssitzungen geöffnet, damit das Tool **CPU Stress** weiter ausgeführt werden kann.


## <a name="monitor-the-active-autoscale-rules"></a>Überwachen der aktiven Regeln für die automatische Skalierung
Verwenden Sie **while**, um die Anzahl von VM-Instanzen in Ihrer Skalierungsgruppe zu überwachen. Es dauert fünf Minuten, bis die Regeln für die automatische Skalierung als Reaktion auf die CPU-Last, die mit **CPUStress** für die einzelnen VM-Instanzen generiert wurde, mit dem horizontalen Hochskalieren beginnen:

```azurepowershell-interactive
while (1) {Get-AzureRmVmssVM `
    -ResourceGroupName $myResourceGroup `
    -VMScaleSetName $myScaleSet; sleep 10}
```

Nachdem der CPU-Schwellenwert erreicht wurde, wird mit den Regeln für die automatische Skalierung die Anzahl von VM-Instanzen in der Skalierungsgruppe erhöht. In der folgenden Ausgabe werden drei VMs angezeigt, die erstellt werden, wenn für die Skalierungsgruppe das automatische horizontale Hochskalieren durchgeführt wird:

```powershell
ResourceGroupName         Name Location          Sku Capacity InstanceID ProvisioningState
-----------------         ---- --------          --- -------- ---------- -----------------
MYRESOURCEGROUP   myScaleSet_2   eastus Standard_DS2                   2         Succeeded
MYRESOURCEGROUP   myScaleSet_3   eastus Standard_DS2                   3         Succeeded
MYRESOURCEGROUP   myScaleSet_4   eastus Standard_DS2                   4          Creating
MYRESOURCEGROUP   myScaleSet_5   eastus Standard_DS2                   5          Creating
MYRESOURCEGROUP   myScaleSet_6   eastus Standard_DS2                   6          Creating
```

Schließen Sie in Ihrer Remotedesktop-Verbindungssitzung für jede VM-Instanz das Tool **CPU Stress**. Die durchschnittliche CPU-Last für die gesamte Skalierungsgruppe normalisiert sich wieder. Nach weiteren fünf Minuten wird über die Regeln für die automatische Skalierung dann das Abskalieren für die Anzahl von VM-Instanzen durchgeführt. Bei Aktionen zum Abskalieren werden die VM-Instanzen mit den höchsten IDs zuerst entfernt. Wenn eine Skalierungsgruppe Verfügbarkeitsgruppen oder Verfügbarkeitszonen verwendet, werden Vorgänge zum Abskalieren gleichmäßig auf diese VM-Instanzen verteilt. In der folgenden Beispielausgabe ist zu sehen, dass beim automatischen horizontalen Herunterskalieren der Skalierungsgruppe eine VM-Instanz gelöscht wurde:

```powershell
MYRESOURCEGROUP   myScaleSet_6   eastus Standard_DS2                   6          Deleting
```

Beenden Sie *while* mit `Ctrl-c`. Für die Skalierungsgruppe wird alle fünf Minuten das Abskalieren durchgeführt und eine VM-Instanz entfernt, bis die Mindestanzahl von zwei Instanzen erreicht ist.


## <a name="clean-up-resources"></a>Bereinigen von Ressourcen
Löschen Sie die Ressourcengruppe und alle dazugehörigen Ressourcen mit [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup), um Ihre Skalierungsgruppe und die weiteren Ressourcen zu entfernen. Der Parameter `-Force` bestätigt ohne eine zusätzliche Aufforderung, dass Sie die Ressourcen löschen möchten. Der Parameter `-AsJob` gibt die Steuerung an die Eingabeaufforderung zurück, ohne zu warten, bis der Vorgang abgeschlossen ist.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name "myResourceGroup" -Force -AsJob
```


## <a name="next-steps"></a>Nächste Schritte
In diesem Tutorial wurde beschrieben, wie Sie für eine Skalierungsgruppe mit Azure PowerShell das automatische Ab- oder Aufskalieren durchführen:

> [!div class="checklist"]
> * Verwenden der automatischen Skalierung mit einer Skalierungsgruppe
> * Erstellen und Verwenden von Regeln für die automatische Skalierung
> * Durchführen eines Belastungstests für VM-Instanzen und Auslösen von Regeln für die automatische Skalierung
> * Zurückfahren der automatischen Skalierung bei einer Reduzierung des Bedarfs
