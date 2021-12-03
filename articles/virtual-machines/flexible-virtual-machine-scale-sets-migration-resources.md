---
title: Migrieren von Bereitstellungen und Ressourcen zu VM-Skalierungsgruppen im Orchestrierungsmodus „Flexibel“
description: Erfahren Sie, wie Sie Bereitstellungen und Ressourcen zu VM-Skalierungsgruppen im Orchestrierungsmodus „Flexibel“ migrieren.
author: fitzgeraldsteele
ms.author: fisteele
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: flexible-scale-sets
ms.date: 10/14/2021
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurecli, vmss-flex
ms.openlocfilehash: b4662c2fd6c5a0950bc3b2b6f336fabab12fc1aa
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131008473"
---
# <a name="migrate-deployments-and-resources-to-virtual-machine-scale-sets-in-flexible-orchestration"></a>Migrieren von Bereitstellungen und Ressourcen zu VM-Skalierungsgruppen im Orchestrierungsmodus „Flexibel“ 

**Gilt für:** :heavy_check_mark: Flexible Skalierungsgruppen

Wie bei Verfügbarkeitsgruppen können Sie mit VM-Skalierungsgruppen virtuelle Computer auf mehrere Fehlerdomänen verteilen. VM-Skalierungsgruppen im Orchestrierungsmodus „Flexibel“ ermöglichen Ihnen das Kombinieren der Skalierbarkeit von [VM-Skalierungsgruppen im Orchestrierungsmodus „Uniform“ (einheitlich)](../virtual-machine-scale-sets/overview.md) mit den regionalen Verfügbarkeitsgarantien von [Verfügbarkeitsgruppen](availability-set-overview.md). In diesem Artikel werden Überlegungen zur Migration beim Wechsel zum Orchestrierungsmodus „Flexibel“ für VM-Skalierungsgruppen behandelt. 

## <a name="update-availability-set-deployments-templates-and-scripts"></a>Aktualisieren von Vorlagen und Skripts für Bereitstellungen von Verfügbarkeitsgruppen

Zunächst müssen Sie mithilfe der [Azure CLI](flexible-virtual-machine-scale-sets-cli.md), der [Azure PowerShell](flexible-virtual-machine-scale-sets-powershell.md) oder einer [ARM-Vorlage](flexible-virtual-machine-scale-sets-rest-api.md) eine VM-Skalierungsgruppe ohne Autoskalierungsprofil erstellen. Das Azure-Portal gestattet nur das Erstellen einer VM-Skalierungsgruppe mit einem Autoskalierungsprofil. Wenn Sie kein Autoskalierungsprofil wünschen oder benötigen und eine Skalierungsgruppe über das [Azure-Portal](flexible-virtual-machine-scale-sets-portal.md) erstellen möchten, können Sie die Anfangskapazität auf 0 festlegen. 
 
Sie müssen die Anzahl der Fehlerdomänen für die VM-Skalierungsgruppe angeben. Für regionale (nicht zonale) Bereitstellungen bieten VM-Skalierungsgruppen dieselben Fehlerdomänengarantien wie Verfügbarkeitsgruppen. Sie können jedoch auf bis zu 1000 Instanzen hochskalieren. Für zonale Bereitstellungen, bei denen Sie Instanzen auf mehrere Verfügbarkeitszonen verteilen, muss die Fehlerdomänenanzahl auf 1 festgelegt werden.

Updatedomänen wurden im Orchestrierungsmodus „Flexibel“ eingestellt. Die meisten Plattformupdates mit allgemeinen SKUs werden mittels Livemigration durchgeführt und erfordern keinen Instanzneustart. Wenn für eine Plattformwartung ein Neustart von Instanzen erforderlich ist, werden Updates nacheinander auf jede Fehlerdomäne einzeln angewendet.  
    
Der Orchestrierungsmodus „Flexibel“ für VM-Skalierungsgruppen unterstützt auch die Bereitstellung von Instanzen über mehrere Verfügbarkeitszonen hinweg. Sie sollten erwägen, Ihre VM-Bereitstellungen so zu aktualisieren, dass sie auf mehrere Verfügbarkeitszonen verteilt sind. 

Der letzte Schritt dieses Prozesses besteht darin, einen virtuellen Computer zu erstellen. Anstatt eine Verfügbarkeitsgruppe anzugeben, geben Sie die VM-Skalierungsgruppe an. Optional können Sie die Verfügbarkeitszone oder Fehlerdomäne angeben, in der Sie den virtuellen Computer platzieren möchten. 


## <a name="migrate-existing-availability-set-vms"></a>Migrieren vorhandener Verfügbarkeitsgruppen-VMs 

Es gibt derzeit keine automatisierten Tools zum direkten Verschieben vorhandener Instanzen aus einer Verfügbarkeitsgruppe in eine VM-Skalierungsgruppe. Es gibt jedoch mehrere Strategien, die Sie nutzen können, um vorhandene Instanzen zu einer flexiblen Skalierungsgruppe zu migrieren: 

### <a name="bluegreen-or-side-by-side-migration"></a>Blaue/grüne oder parallele Migration 

1. Neue Instanzen von virtuellen Skalierungsgruppencomputern mit ähnlicher Konfiguration in derselben Ressourcengruppe, demselben virtuellen Netzwerk oder Lastenausgleich usw. wie die der virtuellen Computer in der Verfügbarkeitsgruppe einrichten. 
1. Daten, Netzwerkdatenverkehr usw. migrieren, um die neuen Skalierungsgruppeninstanzen zu verwenden. 
1. Die Zuordnung der ursprünglichen virtuellen Computer der Verfügbarkeitsgruppe aufheben oder sie entfernen, wobei die Skalierungsgruppen-VMs für Ihre Anwendung weiterhin ausgeführt werden.

### <a name="replace-vm-instances"></a>Ersetzen von VM-Instanzen 

1. Notieren Sie sich die Parameter, die Sie vom virtuellen Computer behalten möchten (Name, NIC-ID, Betriebssystemdatenträger- und Datenträger-IDs, VM-Konfigurationseinstellungen, Platzierung der Fehlerdomäne usw.). 
1. Löschen Sie den virtuellen Computer der Verfügbarkeitsgruppe. Die NICs und Datenträger für den virtuellen Computer werden nicht gelöscht.  
1. Erstellen Sie ein neues VM-Objekt mithilfe der Parameter des ursprünglichen virtuellen Computers. 
    - NIC-ID 
    - Betriebssystem-Datenträger und Datenträger 
    - Platzierung der Fehlerdomäne 
    - Weitere VM-Einstellungen 


## <a name="update-uniform-scale-sets-deployment-templates-and-scripts"></a>Aktualisieren von Vorlagen und Skripts für die Bereitstellung einheitlicher (Uniform) Skalierungsgruppen

Aktualisieren Sie Vorlagen und Skripts für die Bereitstellung einheitlicher (Uniform) VM-Skalierungsgruppen, um den Orchestrierungsmodus „Flexibel“ zu verwenden. Ändern Sie die folgenden Elemente in Ihren Vorlagen, um den Vorgang erfolgreich abzuschließen. 

- Entfernen Sie `LoadBalancerNATPool` (für flex nicht gültig). 
- Entfernen Sie den Überbereitstellungsparameter (für flex nicht gültig) 
- Entfernen Sie `upgradePolicy` (für flex noch nicht gültig). 
- Aktualisieren Sie die Compute-API-Version auf **2021-03-01**. 
- Fügen Sie den Orchestrierungsmodus `flexible` hinzu. 
- `platformFaultDomainCount` erforderlich. 
- „`singlePlacementGroup`=false“ erforderlich. 
- Fügen Sie die Netzwerk-API-Version zu **2021-11-01** oder höher hinzu. 
- Legen Sie die IP `configuration.properties.primary` auf *true* fest (für Ausgangsregeln erforderlich).


## <a name="migrate-existing-uniform-scale-sets"></a>Migrieren vorhandener einheitlicher (Uniform) Skalierungsgruppen 

Es gibt derzeit keine automatisierten Tools zum direkten Verschieben vorhandener Instanzen oder zum Upgraden einer einheitlichen (Uniform) Skalierungsgruppe in eine/r flexible/n VM-Skalierungsgruppe. Im Folgenden sehen Sie jedoch eine Strategie, die Sie nutzen können, um vorhandene Instanzen zu einer flexiblen Skalierung gruppe zu migrieren:

### <a name="bluegreen-or-side-by-side-migration"></a>Blaue/grüne oder parallele Migration 

1. Skalierungsgruppe im Orchestrierungsmodus „Flexibel“ mit ähnlicher Konfiguration in derselben Ressourcengruppe, demselben virtuellen Netzwerk oder Lastenausgleich usw. wie die der ursprünglichen Skalierungsgruppe im Orchestrierungsmodus „Uniform“ einrichten.
1. Daten, Netzwerkdatenverkehr usw. migrieren, um die neuen Skalierungsgruppeninstanzen zu verwenden. 
1. Die ursprünglichen einheitlichen (Uniform) virtuellen Computer der Skalierungsgruppe herunterskalieren oder entfernen, wobei die Skalierungsgruppen-VMs für Ihre Anwendung weiterhin ausgeführt werden.


## <a name="flexible-scale-sets-considerations"></a>Überlegungen zu flexiblen Skalierungsgruppen 

VM-Skalierungsgruppen im Orchestrierungsmodus „Flexibel“ ermöglichen Ihnen das Kombinieren der Skalierbarkeit von [VM-Skalierungsgruppen im Orchestrierungsmodus „Uniform“ (einheitlich)](../virtual-machine-scale-sets/overview.md) mit den regionalen Verfügbarkeitsgarantien von Verfügbarkeitsgruppen. Im Folgenden finden Sie wichtige Überlegungen zur Entscheidung, mit dem flexiblen Orchestrierungsmodus zu arbeiten. 

### <a name="create-scalable-network-connectivity"></a>Erstellen skalierbarer Netzwerkkonnektivität 
<!-- the following is an important link to use in FLEX documentation to reference this section:
/virtual-machines/flexible-virtual-machine-scale-sets-migration-resources.md#create-scalable-network-connectivity
-->

Das Netzwerkverhalten für externen Zugriff hängt davon ab, wie Sie virtuelle Computer in Ihrer Skalierungsgruppe erstellen. **Manuell hinzugefügte VM-Instanzen** verfügen standardmäßig über einen ausgehenden Konnektivitätszugriff. **Implizit erstellte VM-Instanzen** verfügen über keinen Standardzugriff. 

Zur Verbesserung der Standard-Netzwerksicherheit verfügen **VM-Instanzen, die implizit über das Profil für die automatische Skalierung erstellt wurden, nicht standardmäßig über ausgehenden Zugriff**. Damit VM-Skalierungsgruppen mit implizit erstellten VM-Instanzen verwendet werden können, muss der ausgehende Zugriff explizit mit einer der folgenden Methoden definiert werden: 

- Für die meisten Szenarios werden die unter [Tutorial: Erstellen eines NAT-Gateways mithilfe des Azure-Portals](../virtual-network/nat-gateway/tutorial-create-nat-gateway-portal.md) beschrieben Schritte empfohlen.
- Im Fall von Szenarien mit hohen Sicherheitsanforderungen oder bei Verwendung von Azure Firewall oder virtuellen Netzwerkappliances (Network Virtual Appliance, NVA) können Sie eine benutzerdefinierte Route als nächsten Hop durch die Firewall festlegen. 
- Instanzen befinden sich im Back-End-Pool einer Azure Load Balancer-Instanz der Standard-SKU. 
- Sie fügen eine öffentliche IP-Adresse an die Netzwerkschnittstelle der Instanz an. 

Dies sind häufige Szenarios, die explizite ausgehende Konnektivität erfordern: 

- Die Aktivierung einer Windows-VM erfordert, dass Sie ausgehende Konnektivität zwischen der VM-Instanz mit dem Schlüsselverwaltungsdienst (Key Management Service, KMS) definiert haben. Weitere Informationen finden Sie unter [Behandlung von Problemen bei der VM-Aktivierung](/troubleshoot/azure/virtual-machines/troubleshoot-activation-problems).  
- Zugriff auf Speicherkonten oder Key Vault. Die Verbindung mit Azure-Diensten kann auch über [Private Link](../private-link/private-link-overview.md) hergestellt werden. 
- Windows-Updates
- Zugriff auf Paket-Manager für Linux 

Weitere Details zum Definieren von ausgehender Konnektivität finden Sie unter [Standardzugriff in ausgehender Richtung in Azure](../virtual-network/ip-services/default-outbound-access.md).

Einzelinstanz-VMs, bei denen Sie den Netzwerkadapter explizit erstellen, wird standardmäßig ausgehender Zugriff gewährt. VM-Skalierungsgruppen im Orchestrierungsmodus „Uniform“ (Einheitlich) verfügen ebenfalls standardmäßig über ausgehende Konnektivität. 


> [!IMPORTANT]
> Vergewissern Sie sich, dass Sie über explizite ausgehende Netzwerkkonnektivität verfügen. Erfahren Sie mehr darüber in [Virtuelle Netzwerke und virtuelle Computer in Azure](../virtual-network/network-overview.md), und stellen Sie sicher, dass Sie die [bewährten Methoden](../virtual-network/concepts-and-best-practices.md) für Netzwerke von Azure beachten. 


### <a name="assign-fault-domain-during-vm-creation"></a>Zuweisen einer Fehlerdomäne während der VM-Erstellung
Sie können die Anzahl von Fehlerdomänen für die Skalierungsgruppe mit flexibler Orchestrierung auswählen. Wenn Sie eine VM einer flexiblen Skalierungsgruppe hinzufügen, werden die Instanzen von Azure gleichmäßig auf die Fehlerdomänen verteilt. Unsere Empfehlung lautet zwar, die Fehlerdomäne von Azure zuweisen zu lassen, aber für erweiterte oder Problembehandlungsszenarien können Sie dieses Standardverhalten außer Kraft setzen und die Fehlerdomäne angeben, in der die Instanz angeordnet werden soll.

```azurecli-interactive
az vm create –vmss "myVMSS"  –-platform_fault_domain 1
```

### <a name="instance-naming"></a>Benennung von Instanzen
Wenn Sie eine VM erstellen und einer flexiblen Skalierungsgruppe hinzufügen, haben Sie über die Azure-Regeln für die Namenskonvention die vollständige Kontrolle über die Instanznamen. Wenn VMs der Skalierungsgruppe über die Autoskalierung automatisch hinzugefügt werden, geben Sie ein Präfix an, und Azure fügt am Ende des Namens dann eine eindeutige Zahl an. 

### <a name="list-scale-sets-vm-api-changes"></a>Auflisten der API-Änderungen für VMs einer Skalierungsgruppe
Mit VM-Skalierungsgruppen können Sie die Instanzen auflisten, die zur Skalierungsgruppe gehören. Bei der flexiblen Orchestrierung wird mit dem Befehl zum Auflisten der VMs einer VM-Skalierungsgruppe eine Liste angezeigt, die die IDs der VMs einer Skalierungsgruppe enthält. Anschließend können Sie die GET-Befehle für die VMs einer VM-Skalierungsgruppe verwenden, um weitere Details zur Zusammenarbeit zwischen Skalierungsgruppe und VM-Instanz abzurufen. Verwenden Sie zum Abrufen der vollständigen Details zur VM die GET-VM-Standardbefehle oder [Azure Resource Graph](../governance/resource-graph/overview.md).


### <a name="query-instances-for-power-state"></a>Abfragen des Energiezustands von Instanzen
Die bevorzugte Methode ist hierbei die Verwendung von Azure Resource Graph zum Abfragen aller VMs einer VM-Skalierungsgruppe. Azure Resource Graph verfügt über effiziente Abfragefunktionen, mit denen Azure-Ressourcen im großen Stil abonnementübergreifend abgefragt werden können.

```
| where type =~ 'Microsoft.Compute/virtualMachines'
| where properties.virtualMachineScaleSet contains "demo"
| extend powerState = properties.extended.instanceView.powerState.code
| project name, resourceGroup, location, powerState
| order by resourceGroup desc, name desc
```

Die Funktion zum Abfragen von Ressourcen von [Azure Resource Graph](../governance/resource-graph/overview.md) ist eine bequeme und effiziente Möglichkeit, um Azure-Ressourcen abzufragen, und verringert die Anzahl von API-Aufrufen des Ressourcenanbieters. Azure Resource Graph ist letztlich ein konsistenter Cache, in dem neue oder aktualisierte Ressourcen ggf. bis zu 60 Sekunden lang nicht widergespiegelt werden. Ihre Möglichkeiten:
- Auflisten einer Ressourcengruppe oder eines Abonnements
- Verwenden der Option „Erweitern“ zum Abrufen der Instanzansicht (Zuweisung von Fehlerdomänen, Energie- und Bereitstellungsstatus) für alle VMs Ihres Abonnements
- Verwenden der Get-VM-API und der Befehle zum Abrufen der Modell- und Instanzansicht für eine einzelne Instanz


### <a name="scale-sets-vm-batch-operations"></a>Batchvorgänge für VMs von Skalierungsgruppen
Verwenden Sie die VM-Standardbefehle zum Starten, Beenden, Neustarten oder Löschen von Instanzen (anstelle der APIs für die VMs einer VM-Skalierungsgruppe). Die VM-Batchvorgänge für VM-Skalierungsgruppen (Alle starten, Alle beenden, Reimaging für alle usw.) werden im Orchestrierungsmodus „Flexibel“ nicht verwendet.


### <a name="monitor-application-health"></a>Überwachen der Anwendungsintegrität
Beim Überwachen der Anwendungsintegrität kann Ihre Anwendung für Azure einen Heartbeat bereitstellen, damit ermittelt werden kann, ob Ihre Anwendung fehlerfrei oder fehlerhaft ist. Azure kann fehlerhafte VM-Instanzen automatisch austauschen. Bei flexiblen Skalierungsgruppeninstanzen müssen Sie die Erweiterung zur Anwendungsintegrität auf dem virtuellen Computer installieren und konfigurieren. Für einheitliche Skalierungsgruppeninstanzen können Sie entweder die Erweiterung für die Anwendungsintegrität verwenden oder die Integrität mit einem benutzerdefinierten Azure Load Balancer-Integritätstest messen.


### <a name="retrieve-boot-diagnostics-data"></a>Abrufen von Startdiagnosedaten
Verwenden Sie die Standard-VM-APIs und -Befehle, um Daten und Screenshots zur Startdiagnose abzurufen. Die Startdiagnose-APIs und -befehle für VMs einer VM-Skalierungsgruppe werden für Instanzen mit dem Orchestrierungsmodus „Flexibel“ nicht verwendet.


### <a name="vm-extensions"></a>VM-Erweiterungen
Verwenden Sie für Standard-VMs bestimmte Erweiterungen, und keine Erweiterungen, die für Instanzen mit dem Orchestrierungsmodus „Einheitlich“ bestimmt sind.


### <a name="protect-instances-from-delete"></a>Schützen von Instanzen vor Löschung

Für VM-Skalierungsgruppen im Orchestrierungsmodus „Flexible“ (Flexibel) sind derzeit keine Schutzoptionen für Instanzen verfügbar. Wenn Sie die Autoskalierung für eine VM-Skalierungsgruppe aktiviert haben, besteht bei einigen VMs möglicherweise das Risiko, dass sie bei der Skalierung gelöscht werden. Wenn Sie bestimmte VM-Instanzen davor schützen möchten, gelöscht zu werden, verwenden Sie eine [Sperre im Azure Resource Manager](../azure-resource-manager/management/lock-resources.md).



## <a name="next-steps"></a>Nächste Schritte
> [!div class="nextstepaction"]
> [Vergleich der API-Unterschiede zwischen den Orchestrierungsmodi „Uniform“ (einheitlich) und „Flexibel“.](../virtual-machine-scale-sets/orchestration-modes-api-comparison.md)