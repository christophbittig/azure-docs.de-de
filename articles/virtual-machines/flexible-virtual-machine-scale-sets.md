---
title: Flexible Orchestrierung für VM-Skalierungsgruppen in Azure
description: In diesem Artikel wird beschrieben, wie Sie den Orchestrierungsmodus „Flexibel“ für VM-Skalierungsgruppen in Azure verwenden.
author: fitzgeraldsteele
ms.author: fisteele
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: flexible-scale-sets
ms.date: 08/11/2021
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurecli, vmss-flex
ms.openlocfilehash: bf52db4950fd14e15cbd52d94b2e4ffbb9d225bb
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/31/2021
ms.locfileid: "123314546"
---
# <a name="preview-flexible-orchestration-for-virtual-machine-scale-sets-in-azure"></a>Vorschau: Flexible Orchestrierung für VM-Skalierungsgruppen in Azure

**Gilt für:** :heavy_check_mark: Flexible Skalierungsgruppen

VM-Skalierungsgruppen mit der Orchestrierung „Flexibel“ ermöglichen Ihnen das Kombinieren der Skalierbarkeit von [VM-Skalierungsgruppen](../virtual-machine-scale-sets/overview.md) mit den regionalen Verfügbarkeitsgarantien von [Verfügbarkeitsgruppen](availability-set-overview.md).

Mit Azure-VM-Skalierungsgruppen können Sie eine Gruppe VMs mit Lastenausgleich erstellen und verwalten. Die Anzahl von VM-Instanzen kann automatisch erhöht oder verringert werden, wenn sich der Bedarf ändert, oder es kann ein Zeitplan festgelegt werden. Skalierungsgruppen verfügen über die folgenden wichtigen Vorteile:
- Einfaches Erstellen und Verwalten von mehreren VMs
- Bietet Hochverfügbarkeit und Anwendungsresilienz durch das fehlerdomänenübergreifende Bereitstellen von VMs
- Automatische Skalierung der Anwendung bei sich änderndem Ressourcenbedarf
- Großer Umfang

Im Orchestrierungsmodus „Flexibel“ können Sie im gesamten Azure-VM-Ökosystem eine einheitliche Benutzeroberfläche nutzen. Bei der flexiblen Orchestrierung wird Hochverfügbarkeit garantiert (für bis zu 1.000 VMs), indem VMs auf Fehlerdomänen in einer Region oder einer Verfügbarkeitszone verteilt werden. Dies ermöglicht Ihnen das Aufskalieren Ihrer Anwendung bei gleichzeitiger Fehlerdomänenisolation, die für die Ausführung von quorumbasierten oder zustandsbehafteten Workloads von entscheidender Bedeutung ist. Beispiele:
- Quorumbasierte Workloads
- Open-Source-Datenbanken
- Zustandsbehaftete Anwendungen
- Dienste, die Hochverfügbarkeit und eine starke Skalierung erfordern
- Dienste, bei denen VM-Typen gemischt oder Spot- und On-Demand-VMs zusammen genutzt werden sollen
- Vorhandene Verfügbarkeitsgruppen-Anwendungen

Informieren Sie sich unter [Vorschau: Orchestrierungsmodi für VM-Skalierungsgruppen in Azure](../virtual-machine-scale-sets/virtual-machine-scale-sets-orchestration-modes.md) über die Unterschiede der Skalierungsgruppen vom Typ „Einheitlich“ und „Flexibel“.


> [!IMPORTANT]
> Das Feature für VM-Skalierungsgruppen im Orchestrierungsmodus „Flexibel“ befindet sich derzeit in der öffentlichen Vorschauphase. Es ist ein Opt-in-Verfahren erforderlich, um die unten beschriebenen Funktionen der öffentlichen Vorschauversion zu nutzen.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


> [!CAUTION]
> Der Orchestrierungsmodus wird beim Erstellen der Skalierungsgruppe festgelegt und kann später nicht mehr geändert oder aktualisiert werden.


## <a name="register-for-flexible-orchestration-mode"></a>Registrieren für den Orchestrierungsmodus „Flexibel“
Bevor Sie VM-Skalierungsgruppen im Orchestrierungsmodus „Flexibel“ bereitstellen können, müssen Sie Ihr Abonnement zunächst für die Previewfunktion registrieren. Die Registrierung kann mehrere Minuten dauern. Sie können für die Registrierung das Azure-Portal, Azure PowerShell oder die Azure CLI verwenden.

### <a name="azure-portal"></a>Azure-Portal

Verwenden Sie während der Preview des Orchestrierungsmodus „Flexibel“ für Skalierungsgruppen das *Preview*-Azure-Portal, das in den folgenden Schritten verlinkt ist. 

1. Melden Sie sich unter https://preview.portal.azure.com beim Azure-Portal an.
1. Navigieren Sie zu Ihren **Abonnements**.
1. Navigieren Sie zur Detailseite für das Abonnement, für das Sie eine Skalierungsgruppe im Orchestrierungsmodus „Flexibel“ erstellen möchten, indem Sie den Namen des Abonnements auswählen.
1. Klicken Sie im Menü unter **Einstellungen** auf **Previewfunktionen**.
1. Wählen Sie die folgenden vier zu aktivierenden Orchestratorfeatures aus: *VMOrchestratorSingleFD*, *VMOrchestratorMultiFD*, *VMScaleSetFlexPreview* und *SkipPublicIpWriteRBACCheckForVMNetworkInterfaceConfigurationsPublicPreview*.
1. Wählen Sie **Registrieren**.

Nachdem Sie die Features für Ihr Abonnement registriert haben, schließen Sie den Opt-in-Prozess ab, indem Sie die Änderung an den Computeressourcenanbieter weitergeben. 

1. Wählen Sie im Menü unter **Einstellungen** die Option **Ressourcenanbieter** aus.
1. Wählen Sie `Microsoft.compute` aus.
1. Wählen Sie **Erneut registrieren** aus.


### <a name="azure-powershell"></a>Azure PowerShell
Verwenden Sie das Cmdlet [Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature), um die Vorschauversion für Ihr Abonnement zu aktivieren.

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName VMOrchestratorMultiFD -ProviderNamespace Microsoft.Compute `
Register-AzProviderFeature -FeatureName VMOrchestratorSingleFD -ProviderNamespace Microsoft.Compute `
Register-AzProviderFeature -FeatureName VMScaleSetFlexPreview -ProviderNamespace Microsoft.Compute `
Register-AzProviderFeature -FeatureName SkipPublicIpWriteRBACCheckForVMNetworkInterfaceConfigurationsPublicPreview -ProviderNamespace Microsoft.Compute
```

Die Featureregistrierung kann bis zu 15 Minuten dauern. So überprüfen Sie den Registrierungsstatus:

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName VMOrchestratorMultiFD -ProviderNamespace Microsoft.Compute
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
Verwenden Sie [az feature register](/cli/azure/feature#az_feature_register), um die Vorschauversion für Ihr Abonnement zu aktivieren.

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name VMOrchestratorMultiFD
az feature register --namespace microsoft.compute --name VMOrchestratorSingleFD
az feature register --namespace Microsoft.Compute --name VMScaleSetFlexPreview 
az feature register --namespace Microsoft.Compute --name SkipPublicIpWriteRBACCheckForVMNetworkInterfaceConfigurationsPublicPreview
```

Die Featureregistrierung kann bis zu 15 Minuten dauern. So überprüfen Sie den Registrierungsstatus:

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name VMOrchestratorMultiFD
```


## <a name="get-started-with-flexible-orchestration-mode"></a>Erste Schritte mit dem Orchestrierungsmodus „Flexibel“

Führen Sie die ersten Schritte im Orchestrierungsmodus „Flexibel“ für Ihre Skalierungsgruppen über das [Azure-Portal](flexible-virtual-machine-scale-sets-portal.md), die [Azure CLI](flexible-virtual-machine-scale-sets-cli.md), [Azure PowerShell](flexible-virtual-machine-scale-sets-powershell.md) oder eine [ARM-Vorlage](flexible-virtual-machine-scale-sets-rest-api.md) aus. 


## <a name="add-instances-with-autoscaling-or-manually"></a>Hinzufügen von Instanzen mithilfe der automatischen Skalierung oder durch manuelles Hinzufügen
VM-Skalierungsgruppen mit der Orchestrierung „Flexibel“ fungieren als dünne Orchestrierungsebene zum Verwalten mehrerer VMs. Es gibt mehrere Möglichkeiten zum Hinzufügen von VMs, die durch die Skalierungsgruppe verwaltet werden:

- **Festlegen der Anzahl der Instanzen**

    Wenn Sie die Skalierungsgruppe mit der Orchestrierung „Flexibel“ erstellen, definieren Sie ein VM-Profil oder eine Vorlage, das bzw. die die aufzuskalierende Vorlage beschreibt. Anschließend können Sie den Kapazitätsparameter festlegen, um die Anzahl der durch die Skalierungsgruppe verwalteten VM-Instanzen zu erhöhen oder zu verringern. 

- **Automatische Skalierung mit Metriken oder Zeitplan** 

    Alternativ können Sie Regeln für die Autoskalierung einrichten, um die Kapazität basierend auf Metriken oder einem Zeitplan zu erhöhen oder zu verringern. Weitere Informationen finden Sie unter [Übersicht über die automatische Skalierung mit Azure-VM-Skalierungsgruppen](..\virtual-machine-scale-sets\virtual-machine-scale-sets-autoscale-overview.md). 

- **Festlegen einer Skalierungsgruppe beim Erstellen einer VM**

    Wenn Sie eine VM erstellen, können Sie optional festlegen, dass diese zu einer VM-Skalierungsgruppe hinzugefügt wird. Eine VM kann nur zum Zeitpunkt ihrer Erstellung zu einer Skalierungsgruppe hinzugefügt werden.


## <a name="explicit-network-outbound-connectivity-required"></a>Explizite ausgehende Netzwerkkonnektivität erforderlich 

Um die Standardnetzwerksicherheit zu verbessern, erfordern VM-Skalierungsgruppen mit der Orchestrierung „Flexibel“, dass implizit über das Profil für die automatische Skalierung erstellte Instanzen über ausgehende Konnektivität verfügen, die explizit durch eine der folgenden Methoden definiert wird: 

- Für die meisten Szenarios werden die unter [Tutorial: Erstellen eines NAT-Gateways mithilfe des Azure-Portals](../virtual-network/nat-gateway/tutorial-create-nat-gateway-portal.md) beschrieben Schritte empfohlen.
- Im Fall von Szenarios mit hohen Sicherheitsanforderungen oder bei Verwendung von Azure Firewall oder virtuellen Netzwerkappliances (Network Virtual Appliance, NVA) können Sie eine benutzerdefinierte Route als nächsten Hop durch die Firewall festlegen. 
- Instanzen befinden sich im Back-End-Pool einer Azure Load Balancer-Instanz der Standard-SKU. 
- Sie fügen eine öffentliche IP-Adresse an die Netzwerkschnittstelle der Instanz an. 

Bei Einzelinstanz-VMs und VM-Skalierungsgruppen mit der Orchestrierung „Einheitlich“ wird die ausgehende Konnektivität automatisch bereitgestellt. 

Dies sind häufige Szenarios, die eine explizite ausgehende Konnektivität erfordern: 

- Die Aktivierung einer Windows-VM erfordert, dass Sie die ausgehende Konnektivität zwischen der VM-Instanz mit dem Schlüsselverwaltungsdienst (Key Management Service, KMS) für die Windows-Aktivierung definiert haben. Weitere Informationen finden Sie unter [Behandlung von Problemen bei der Aktivierung virtueller Windows-Computer](https://docs.microsoft.com/troubleshoot/azure/virtual-machines/troubleshoot-activation-problems).  
- Zugriff auf Speicherkonten oder Key Vault. Die Verbindung mit Azure-Diensten kann auch über [Private Link](../private-link/private-link-overview.md) hergestellt werden. 

Weitere Details zum Definieren von sicheren ausgehenden Verbindungen finden Sie unter [Ausgehender Standardzugriff in Azure](https://aka.ms/defaultoutboundaccess).


## <a name="assign-fault-domain-during-vm-creation"></a>Zuweisen einer Fehlerdomäne während der VM-Erstellung
Sie können die Anzahl von Fehlerdomänen für die Skalierungsgruppe mit flexibler Orchestrierung auswählen. Wenn Sie eine VM einer flexiblen Skalierungsgruppe hinzufügen, werden die Instanzen von Azure gleichmäßig auf die Fehlerdomänen verteilt. Unsere Empfehlung lautet zwar, die Fehlerdomäne von Azure zuweisen zu lassen, aber für erweiterte oder Problembehandlungsszenarien können Sie dieses Standardverhalten außer Kraft setzen und die Fehlerdomäne angeben, in der die Instanz angeordnet werden soll.

```azurecli-interactive
az vm create –vmss "myVMSS"  –-platform_fault_domain 1
```

## <a name="instance-naming"></a>Benennung von Instanzen
Wenn Sie eine VM erstellen und einer flexiblen Skalierungsgruppe hinzufügen, haben Sie über die Azure-Regeln für die Namenskonvention die vollständige Kontrolle über die Instanznamen. Wenn VMs der Skalierungsgruppe über die Autoskalierung automatisch hinzugefügt werden, geben Sie ein Präfix an, und Azure fügt am Ende des Namens dann eine eindeutige Zahl an. 

## <a name="list-scale-sets-vm-api-changes"></a>Auflisten der API-Änderungen für VMs einer Skalierungsgruppe
Mit VM-Skalierungsgruppen können Sie die Instanzen auflisten, die zur Skalierungsgruppe gehören. Bei der flexiblen Orchestrierung wird mit dem Befehl zum Auflisten der VMs einer VM-Skalierungsgruppe eine Liste angezeigt, die die IDs der VMs einer Skalierungsgruppe enthält. Anschließend können Sie die GET-Befehle für die VMs einer VM-Skalierungsgruppe verwenden, um weitere Details zur Zusammenarbeit zwischen Skalierungsgruppe und VM-Instanz abzurufen. Verwenden Sie zum Abrufen der vollständigen Details zur VM die GET-VM-Standardbefehle oder [Azure Resource Graph](../governance/resource-graph/overview.md).


## <a name="query-instances-for-power-state"></a>Abfragen des Energiezustands von Instanzen
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


## <a name="scale-sets-vm-batch-operations"></a>Batchvorgänge für VMs von Skalierungsgruppen
Verwenden Sie die VM-Standardbefehle zum Starten, Beenden, Neustarten oder Löschen von Instanzen (anstelle der APIs für die VMs einer VM-Skalierungsgruppe). Die VM-Batchvorgänge für VM-Skalierungsgruppen (Alle starten, Alle beenden, Reimaging für alle usw.) werden im Orchestrierungsmodus „Flexibel“ nicht verwendet.


## <a name="monitor-application-health"></a>Überwachen der Anwendungsintegrität
Beim Überwachen der Anwendungsintegrität kann Ihre Anwendung für Azure einen Heartbeat bereitstellen, damit ermittelt werden kann, ob Ihre Anwendung fehlerfrei oder fehlerhaft ist. Azure kann fehlerhafte VM-Instanzen automatisch austauschen. Bei flexiblen Skalierungsgruppeninstanzen müssen Sie die Erweiterung zur Anwendungsintegrität auf dem virtuellen Computer installieren und konfigurieren. Für einheitliche Skalierungsgruppeninstanzen können Sie entweder die Erweiterung für die Anwendungsintegrität verwenden oder die Integrität mit einem benutzerdefinierten Azure Load Balancer-Integritätstest messen.


## <a name="retrieve-boot-diagnostics-data"></a>Abrufen von Startdiagnosedaten
Verwenden Sie die Standard-VM-APIs und -Befehle, um Daten und Screenshots zur Startdiagnose abzurufen. Die Startdiagnose-APIs und -befehle für VMs einer VM-Skalierungsgruppe werden für Instanzen mit dem Orchestrierungsmodus „Flexibel“ nicht verwendet.


## <a name="vm-extensions"></a>VM-Erweiterungen
Verwenden Sie für Standard-VMs bestimmte Erweiterungen, und keine Erweiterungen, die für Instanzen mit dem Orchestrierungsmodus „Einheitlich“ bestimmt sind.


## <a name="features"></a>Features
In der folgenden Liste sind die Features des Orchestrierungsmodus „Flexibel“ und Links zur entsprechenden Dokumentation aufgeführt.

| Funktion | Von flexibler Orchestrierung unterstützt (Vorschau) |
|-|-|
| Typ des virtuellen Computers | Azure IaaS-Standard-VM (Microsoft.compute /virtualmachines) |
| Maximale Anzahl von Instanzen (mit Verfügbarkeitsgarantie für die Fehlerdomänen) | 1000 |
| Unterstützte SKUs | D-Serie, E-Serie, F-Serie, A-Serie, B-Serie, Intel, AMD |
| Verfügbarkeitszonen | Geben Sie optional an, dass alle Instanzen in nur einer Verfügbarkeitszone angeordnet sein sollen. |
| Fehlerdomäne – Maximale Verteilung (Azure verteilt die Instanzen maximal) | Ja |
| Fehlerdomäne – Feste Verteilung | 2 bis 3 Fehlerdomänen (je nach regionalem Maximum für Fehlerdomänen), 1 Fehlerdomäne für zonale Bereitstellungen |
| Updatedomänen | Veraltete, von Fehlerdomäne durchgeführte Plattformwartung |
| Verfügbarkeits-SLA | Keine (während der Vorschau) |
| Vollständige Kontrolle über VM, NICs, Datenträger | Ja |
| Zuweisung eines virtuellen Computers zu einer bestimmten Fehlerdomäne | Ja |
| Beschleunigte Netzwerke | Nein (während der Vorschau) |
| Sicherheitspatches auf Gastbetriebssystem | Ja |
| Spot-Instanzen und Preise  | Ja. Sie können sowohl Spot-Instanzen als auch Instanzen mit regulärer Priorität verwenden. |
| Mischung von Betriebssystemen | Ja. Linux und Windows können in derselben flexiblen Skalierungsgruppe vorhanden sein. |
| Überwachung der Anwendungsintegrität | Erweiterung für Anwendungsintegrität |
| SSD Ultra-Datenträger  | Ja |
| Näherungsplatzierungsgruppen  | Ja. Weitere Informationen finden Sie unter [Erstellen und Verwenden von Näherungsplatzierungsgruppen mit PowerShell](../virtual-machine-scale-sets/proximity-placement-groups.md). |
| Azure Load Balancer Standard-SKU | Ja |
| Auflistung von VMs einer Gruppe | Ja |
| Azure Backup | Ja |
| Beendigung von Benachrichtigungen (VM-Skalierungsgruppen) | Ja. Weitere Informationen finden Sie unter [Beendigungsbenachrichtigung für Instanzen von Azure-VM-Skalierungsgruppen](../virtual-machine-scale-sets/virtual-machine-scale-sets-terminate-notification.md). |
| Instanzreparatur (VM-Skalierungsgruppen) | Ja. Weitere Informationen finden Sie unter [Automatische Instanzreparaturen für Azure-VM-Skalierungsgruppen](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-instance-repairs.md). |
| Autoskalierung | Ja |
| Entfernung von NICs und Datenträgern beim Löschen von VM-Instanzen | Ja |
| Upgraderichtlinie (VM-Skalierungsgruppen) | Nein |
| Automatische imagebasierte Betriebssystemupdates | Nein |
| InfiniBand  | Nein |
| Schreibbeschleunigung  | Nein |
| Dedizierte Azure-Hosts  | Nein |
| Basis-SLB  | Nein |
| Application Gateway | Ja |
| Wartungssteuerung  | Nein |
| Azure-Warnungen | Nein |
| VM Insights | Nein |
| Azure Site Recovery |  Ja, über PowerShell |
| Service Fabric | Nein |
| Azure Kubernetes Service (AKS) | Nein |
| Vertrauenswürdige virtuelle Computer (Vorschau) | Ja |
| Automatische Erweiterungsaktualisierungen | Ja |


### <a name="unsupported-parameters"></a>Nicht unterstützte Parameter

Die folgenden Parameter für VM-Skalierungsgruppen werden während der Public Preview der Orchestrierung „Flexibel“ für VM-Skalierungsgruppen derzeit nicht unterstützt:
- Einzelne Platzierungsgruppe: Sie müssen `singlePlacementGroup=False` auswählen.
- Bereitstellung in mehreren Zonen: Bereitstellungen sind regional, oder alle VMs befinden sich in einer einzelnen Zone.
- Bereitstellung mithilfe von speziellen SKUs: VM-Familien der Serien G, H, L, M und N
- Überbereitstellung von VM-Skalierungsgruppen
- Imagebasierte automatische Betriebssystemupgrades
- Anwendungsintegrität über SLB-Integritätstest: Verwenden Sie die Erweiterung für Anwendungsintegrität für Instanzen.
- Upgraderichtlinie für VM-Skalierungsgruppen: Dieser Wert muss „NULL“ lauten oder leer sein.
- Bereitstellung in Azure Dedicated Host
- Nicht verwaltete Datenträger
- Abskalierungsrichtlinie für VM-Skalierungsgruppen
- Schutz von VM-Skalierungsgruppeninstanzen
- Beschleunigter Netzwerkbetrieb
- Load Balancer Basic
- Portweiterleitung über Load Balancer Standard-NAT-Pools: Sie können NAT-Regeln für bestimmte Instanzen konfigurieren.


## <a name="troubleshoot-scale-sets-with-flexible-orchestration"></a>Problembehandlung für Skalierungsgruppen mit flexibler Orchestrierung
Ermitteln Sie die richtige Lösung für Ihr Problembehandlungsszenario.

<!-- error -->
### <a name="invalidparameter-parameter-virtualmachineprofile-is-not-allowed"></a>Fehlermeldung: InvalidParameter. Parameter 'virtualMachineProfile' is not allowed.

```
InvalidParameter. Parameter 'virtualMachineProfile' is not allowed.
```

**Ursache:** Das Abonnement ist nicht für den Orchestrierungsmodus „Flexibel“ (öffentliche Vorschauphase) registriert.

**Lösung:** Sie müssen das VMScaleSetPublicPreview-Feature für Ihr Abonnement registrieren. Befolgen Sie die obige Anleitung, um die Registrierung für den Orchestrierungsmodus „Flexibel“ (Public Preview) durchzuführen.


<!-- error -->
### <a name="badrequest-creating-a-virtual-machine-with-a-public-ip-address-via-networkinterfaceconfigurations"></a>Fehlermeldung: BadRequest. Creating a Virtual Machine with a Public IP Address via NetworkInterfaceConfigurations

```
BadRequest. Creating a Virtual Machine with a Public IP Address via NetworkInterfaceConfigurations during the Public Preview of VM NetworkInterfaceConfigurations initially requires the feature 'Microsoft.Compute/SkipPublicIpWriteRBACCheckForVMNetworkInterfaceConfigurationsPublicPreview'.
```

**Ursache:** Das Abonnement ist nicht für den Orchestrierungsmodus „Flexibel“ (öffentliche Vorschauphase) registriert.

**Lösung:** Sie müssen das `SkipPublicIpWriteRBACCheckForVMNetworkInterfaceConfigurationsPublicPreview`-Feature für Ihr Abonnement registrieren. Befolgen Sie die obige Anleitung, um die Registrierung für den Orchestrierungsmodus „Flexibel“ (Public Preview) durchzuführen.


<!-- error -->
### <a name="invalidparameter-the-value-false-of-parameter-singleplacementgroup-is-not-allowed-allowed-values-are-true"></a>Fehlermeldung: InvalidParameter. The value 'False' of parameter 'singlePlacementGroup' is not allowed. Allowed values are: True

```
InvalidParameter. The value 'False' of parameter 'singlePlacementGroup' is not allowed. Allowed values are: True
```

**Ursache:** Das Abonnement ist nicht für den Orchestrierungsmodus „Flexibel“ (öffentliche Vorschauphase) registriert.

**Lösung:** Befolgen Sie die obige Anleitung, um die Registrierung für den Orchestrierungsmodus „Flexibel“ (öffentliche Vorschauphase) durchzuführen.


<!-- error -->
### <a name="invalidparameter-the-specified-fault-domain-count-2-must-fall-in-the-range-1-to-1"></a>Fehlermeldung: InvalidParameter. The specified fault domain count 2 must fall in the range 1 to 1.

```
InvalidParameter. The specified fault domain count 2 must fall in the range 1 to 1.
```

**Ursache:** Der Parameter `platformFaultDomainCount` ist für die ausgewählte Region oder Zone ungültig.

**Lösung:** Sie müssen einen gültigen Wert für `platformFaultDomainCount` auswählen. Für zonale Bereitstellungen beträgt der Maximalwert für `platformFaultDomainCount` „1“. Bei regionalen Bereitstellungen ohne Angabe einer Zone variiert der Maximalwert für `platformFaultDomainCount` je nach Region. Lesen Sie die Informationen im Abschnitt zum [Verwalten der Verfügbarkeit von VMs für Skripts](../virtual-machines/availability.md), um die maximale Anzahl von Fehlerdomänen pro Region zu ermitteln.


<!-- error -->
### <a name="operationnotallowed-deletion-of-virtual-machine-scale-set-is-not-allowed-as-it-contains-one-or-more-vms-please-delete-or-detach-the-vms-before-deleting-the-virtual-machine-scale-set"></a>Fehlermeldung: OperationNotAllowed. Deletion of Virtual Machine Scale Set is not allowed as it contains one or more VMs. Please delete or detach the VM(s) before deleting the Virtual Machine Scale Set.

```
OperationNotAllowed. Deletion of Virtual Machine Scale Set is not allowed as it contains one or more VMs. Please delete or detach the VM(s) before deleting the Virtual Machine Scale Set.
```

**Ursache:** Es wird versucht, eine Skalierungsgruppe im Modus „Flexibel“ zu löschen, die mindestens einem virtuellen Computer zugeordnet ist.

**Lösung:** Löschen Sie alle virtuellen Computer, die der Skalierungsgruppe im Orchestrierungsmodus „Flexibel“ zugeordnet sind. Anschließend können Sie die Skalierungsgruppe löschen.


<!-- error -->
### <a name="invalidparameter-the-value-true-of-parameter-singleplacementgroup-is-not-allowed-allowed-values-are-false"></a>Fehlermeldung: InvalidParameter. The value 'True' of parameter 'singlePlacementGroup' is not allowed. Zulässige Werte: False.

```
InvalidParameter. The value 'True' of parameter 'singlePlacementGroup' is not allowed. Allowed values are: False.
```
**Ursache:** Das Abonnement ist für den Orchestrierungsmodus „Flexibel“ (Vorschau) registriert, aber der Parameter `singlePlacementGroup` ist auf *True* festgelegt.

**Lösung:** `singlePlacementGroup` muss auf *False* festgelegt werden.


## <a name="next-steps"></a>Nächste Schritte
> [!div class="nextstepaction"]
> [Orchestrierungsmodus „Flexibel“ für Ihre Skalierungsgruppen mit dem Azure-Portal](flexible-virtual-machine-scale-sets-portal.md)
