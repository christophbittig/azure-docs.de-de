---
title: Orchestrierungsmodi für VM-Skalierungsgruppen in Azure
description: Es wird beschrieben, wie Sie die Orchestrierungsmodi „Flexibel“ und „Einheitlich“ für VM-Skalierungsgruppen in Azure verwenden.
author: fitzgeraldsteele
ms.author: fisteele
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.date: 08/05/2021
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurecli, vmss-flex, devx-track-azurepowershell
ms.openlocfilehash: 7983ae912d29f2a27d35b261d1654205fe503651
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/31/2021
ms.locfileid: "123305068"
---
# <a name="preview-orchestration-modes-for-virtual-machine-scale-sets-in-azure"></a>Vorschau: Orchestrierungsmodi für VM-Skalierungsgruppen in Azure


**Gilt für**: :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows-VMs :heavy_check_mark: Flexible Skalierungsgruppen :heavy_check_mark: Einheitliche Skalierungsgruppen

VM-Skalierungsgruppen ermöglichen eine logische Gruppierung von plattformseitig verwalteten virtuellen Computern. Mit Skalierungsgruppen erstellen Sie ein Konfigurationsmodell für virtuelle Computer, fügen automatisch zusätzliche Instanzen basierend auf der CPU- oder Speicherauslastung hinzu oder entfernen sie und führen automatisch ein Upgrade auf die neueste Betriebssystemversion durch. Traditionell ermöglichen Skalierungsgruppen die Erstellung virtueller Computer mit einem VM-Konfigurationsmodell, das zum Zeitpunkt der Erstellung der Skalierungsgruppen bereitgestellt wird. Die Skalierungsgruppen können nur virtuelle Computer verwalten, die implizit basierend auf dem Konfigurationsmodell erstellt werden. 

Mit den Orchestrierungsmodi für Skalierungsgruppen haben Sie eine bessere Kontrolle darüber, wie die Instanzen virtueller Computer von der Skalierungsgruppe verwaltet werden.

> [!IMPORTANT]
> Der Orchestrierungsmodus wird beim Erstellen der Skalierungsgruppe festgelegt und kann später nicht mehr geändert oder aktualisiert werden.


## <a name="scale-sets-with-uniform-orchestration"></a>Skalierungsgruppen mit einheitlicher Orchestrierung
Optimiert für umfangreiche zustandslose Workloads mit identischen Instanzen.

Für VM-Skalierungsgruppen mit dem Orchestrierungsmodus „Einheitlich“ wird ein VM-Profil oder eine entsprechende Vorlage verwendet, um auf die gewünschte Kapazität hochzuskalieren. Einzelne VM-Instanzen können zwar bis zu einem gewissen Maße verwaltet oder angepasst werden, aber im Modus „Einheitlich“ werden identische VM-Instanzen verwendet. Einzelne einheitliche VM-Instanzen werden über die API-Befehle für die VMs der VM-Skalierungsgruppe verfügbar gemacht. Einzelne Instanzen sind nicht mit den Standardbefehlen der Azure IaaS-VM-API, den Azure-Verwaltungsfeatures, z. B. Azure Resource Manager-Ressourcenmarkierung oder RBAC-Berechtigungen, mit Azure Backup oder mit Azure Site Recovery kompatibel. Im Orchestrierungsmodus „Einheitlich“ ist die Hochverfügbarkeit für Fehlerdomänen garantiert, wenn eine Konfiguration mit weniger als 100 Instanzen genutzt wird. Der Orchestrierungsmodus „Einheitlich“ ist allgemein verfügbar und unterstützt viele verschiedene Features für die Verwaltung und Orchestrierung, z. B. metrikbasierte Autoskalierung, Instanzschutz und automatische Betriebssystemupgrades.


## <a name="scale-sets-with-flexible-orchestration"></a>Skalierungsgruppen mit flexibler Orchestrierung
Erzielen Sie Hochverfügbarkeit im großen Stil mit identischen oder mehreren unterschiedlichen VM-Typen.

Im Orchestrierungsmodus „Flexibel“ können Sie im gesamten Azure-VM-Ökosystem eine einheitliche Benutzeroberfläche nutzen. Bei der flexiblen Orchestrierung wird Hochverfügbarkeit garantiert (für bis zu 1.000 VMs), indem VMs auf Fehlerdomänen in einer Region oder einer Verfügbarkeitszone verteilt werden. Dies ermöglicht Ihnen das Aufskalieren Ihrer Anwendung bei gleichzeitiger Fehlerdomänenisolation, die für die Ausführung von quorumbasierten oder zustandsbehafteten Workloads von entscheidender Bedeutung ist. Beispiele:
- Quorumbasierte Workloads
- Open-Source-Datenbanken
- Zustandsbehaftete Anwendungen
- Dienste, für die Hochverfügbarkeit und starke Skalierung erforderlich sind
- Dienste, bei denen VM-Typen gemischt oder Spot- und On-Demand-VMs zusammen genutzt werden sollen
- Vorhandene Verfügbarkeitsgruppen-Anwendungen

> [!IMPORTANT]
> Das Feature für VM-Skalierungsgruppen im Orchestrierungsmodus „Flexibel“ befindet sich derzeit in der öffentlichen Vorschauphase. Es ist ein Opt-in-Verfahren erforderlich, um die unten beschriebenen Funktionen der öffentlichen Vorschauversion zu nutzen.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="what-has-changed-with-flexible-orchestration-mode"></a>Was hat sich mit dem Orchestrierungsmodus „Flexibel“ geändert?
Einer der Hauptvorteile des Orchestrierungsmodus „Flexibel“ sind die für Azure IaaS-Standard-VMs verfügbaren Orchestrierungsfeatures (anstelle von untergeordneten VMs von Skalierungsgruppen). Dies bedeutet, dass Sie beim Verwalten von Instanzen mit flexibler Orchestrierung alle Standard-VM-APIs verwenden können, anstatt der APIs für die VMs von VM-Skalierungsgruppen im Orchestrierungsmodus „Einheitlich“. Während der Vorschauphase bestehen zwischen dem Verwalten von Instanzen im Orchestrierungsmodus „Flexibel“ und „Einheitlich“ mehrere Unterschiede. Im Allgemeinen lautet unsere Empfehlung, nach Möglichkeit die Standard-APIs für Azure IaaS-VMs zu verwenden. In diesem Abschnitt werden Beispiele für bewährte Methoden zum Verwalten von VM-Instanzen mit flexibler Orchestrierung beschrieben.

### <a name="assign-fault-domain-during-vm-creation"></a>Zuweisen einer Fehlerdomäne während der VM-Erstellung
Sie können die Anzahl von Fehlerdomänen für die Skalierungsgruppe mit flexibler Orchestrierung auswählen. Wenn Sie eine VM einer flexiblen Skalierungsgruppe hinzufügen, werden die Instanzen von Azure gleichmäßig auf die Fehlerdomänen verteilt. Unsere Empfehlung lautet zwar, die Fehlerdomäne von Azure zuweisen zu lassen, aber für erweiterte oder Problembehandlungsszenarien können Sie dieses Standardverhalten außer Kraft setzen und die Fehlerdomäne angeben, in der die Instanz angeordnet werden soll.

```azurecli-interactive
az vm create –vmss "myVMSS"  –-platform_fault_domain 1
```

### <a name="instance-naming"></a>Benennung von Instanzen
Wenn Sie eine VM erstellen und einer flexiblen Skalierungsgruppe hinzufügen, haben Sie über die Azure-Regeln für die Namenskonvention die vollständige Kontrolle über die Instanznamen. Wenn VMs der Skalierungsgruppe über die Autoskalierung automatisch hinzugefügt werden, geben Sie ein Präfix an, und Azure fügt am Ende des Namens dann eine eindeutige Zahl an.

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

### <a name="list-scale-sets-vm-api-changes"></a>Auflisten der API-Änderungen für VMs einer Skalierungsgruppe
Mit VM-Skalierungsgruppen können Sie die Instanzen auflisten, die zur Skalierungsgruppe gehören. Bei der flexiblen Orchestrierung wird mit dem Befehl zum Auflisten der VMs einer VM-Skalierungsgruppe eine Liste angezeigt, die die IDs der VMs einer Skalierungsgruppe enthält. Anschließend können Sie die GET-Befehle für die VMs einer VM-Skalierungsgruppe verwenden, um weitere Details zur Zusammenarbeit zwischen Skalierungsgruppe und VM-Instanz abzurufen. Verwenden Sie zum Abrufen der vollständigen Details zur VM die GET-VM-Standardbefehle oder [Azure Resource Graph](../governance/resource-graph/overview.md).

### <a name="retrieve-boot-diagnostics-data"></a>Abrufen von Startdiagnosedaten
Verwenden Sie die Standard-VM-APIs und -Befehle, um Daten und Screenshots zur Startdiagnose abzurufen. Die Startdiagnose-APIs und -befehle für VMs einer VM-Skalierungsgruppe werden für Instanzen mit dem Orchestrierungsmodus „Flexibel“ nicht verwendet.

### <a name="vm-extensions"></a>VM-Erweiterungen
Verwenden Sie für Standard-VMs bestimmte Erweiterungen, und keine Erweiterungen, die für Instanzen mit dem Orchestrierungsmodus „Einheitlich“ bestimmt sind.


## <a name="a-comparison-of-flexible-uniform-and-availability-sets"></a>Vergleich der Modi „Flexibel“ und „Einheitlich“ und von Verfügbarkeitsgruppen
In der folgenden Tabelle werden die Orchestrierungsmodi „Flexibel“ und „Einheitlich“ und Verfügbarkeitsgruppen in Bezug auf ihre Features verglichen.

| Funktion  | Von flexibler Orchestrierung unterstützt (Vorschau)  | Von einheitlicher Orchestrierung unterstützt (allgemeine Verfügbarkeit)  | Von Verfügbarkeitsgruppen unterstützt (allgemeine Verfügbarkeit)  |
|-|-|-|-|
| Typ des virtuellen Computers  | Azure IaaS-Standard-VM (Microsoft.compute /virtualmachines)  | Skalierungsgruppenspezifische VMs (Microsoft.compute /virtualmachinescalesets/virtualmachines)  | Azure IaaS-Standard-VM (Microsoft.compute /virtualmachines)  |
| Unterstützte SKUs  | D-Serie, E-Serie, F-Serie, A-Serie, B-Serie, Intel, AMD  | Alle SKUs  | Alle SKUs  |
| Verfügbarkeitszonen  | Geben Sie optional an, dass alle Instanzen in nur einer Verfügbarkeitszone angeordnet sein sollen  | Geben Sie an, dass Instanzen in einer, zwei oder drei Verfügbarkeitszonen angeordnet sein sollen  | Nicht unterstützt  |
| Fehlerdomäne – Maximale Verteilung (Azure verteilt die Instanzen maximal)  | Ja  | Ja  | Nein  |
| Fehlerdomäne – Feste Verteilung  | 2-3 FDs (je nach regionalem Maximum); 1 für zonale Bereitstellungen  | 2, 3, 5 FDs; 1, 5 für zonale Bereitstellungen  | 2-3 FDs (abhängig vom regionalen Maximum)  |
| Updatedomänen  | Veraltete, von FD durchgeführte Plattformwartung | 5 Updatedomänen  | Bis zu 20 Updatedomänen  |
| Verfügbarkeits-SLA  | Jetzt nicht  | 99,95 % für FD>1 in einer einzelnen Platzierungsgruppe; 99,99 % für Instanzen, die auf mehrere Zonen verteilt sind  | 99,95 %  |
| Vollständige Kontrolle über VM, NICs, Datenträger  | Ja  | Eingeschränkte Kontrolle mit einer API für virtuelle Computer in der Skalierungsgruppe für virtuelle Computer  | Ja  |
| Automatische Skalierung (manuell, basierend auf Metriken, basierend auf einem Zeitplan)  | Ja  | Ja  | Nein  |
| Zuweisung eines virtuellen Computers zu einer bestimmten Fehlerdomäne  | Ja  | Nein  | Nein  |
| Automatische Entfernung von NICs und Datenträgern beim Löschen von VM-Instanzen  | Ja  | Ja  | Nein  |
| Upgraderichtlinie (VM-Skalierungsgruppen)  | Nein, die Upgraderichtlinie muss während der Erstellung NULL oder [] sein  | Automatisch, Parallel, Manuell  | –  |
| Automatische imagebasierte Betriebssystemupdates  | Nein  | Ja  | –  |
| Sicherheitspatches auf Gastbetriebssystem  | Ja  | Nein  | Ja  |
| Beendigung von Benachrichtigungen (VM-Skalierungsgruppen)  | Ja  | Ja  | –  |
| Instanzreparatur (VM-Skalierungsgruppen)  | Ja  | Ja  | –  |
| Beschleunigte Netzwerke  | Nein  | Ja  | Ja  |
| Spot-Instanzen und Preise   | Ja. Sie können sowohl Spot-Instanzen als auch Instanzen mit regulärer Priorität verwenden  | Ja. Entweder nur Spot-Instanzen oder nur reguläre Instanzen  | Nein. Nur Instanzen mit regulärer Priorität.  |
| Mischung von Betriebssystemen  | Ja. Linux und Windows können in derselben flexiblen Skalierungsgruppe vorhanden sein.  | Nein. Instanzen weisen das gleiche Betriebssystem auf  | Ja. Linux und Windows können in derselben flexiblen Skalierungsgruppe vorhanden sein.  |
| Überwachung der Anwendungsintegrität  | Erweiterung für Anwendungsintegrität  | Erweiterung für einer Anwendungsintegrität oder Azure Load Balancer-Test  | Erweiterung für Anwendungsintegrität  |
| SSD Ultra-Datenträger   | Ja  | Ja, nur für zonale Bereitstellungen  | Nein  |
| InfiniBand   | Nein  | Ja, nur eine Platzierungsgruppe  | Ja  |
| Schreibbeschleunigung   | Nein  | Ja  | Ja  |
| Näherungsplatzierungsgruppen   | Ja  | Ja  | Ja  |
| Dedizierte Azure-Hosts   | Nein  | Ja  | Ja  |
| Basis-SLB   | Nein  | Ja  | Ja  |
| Azure Load Balancer Standard-SKU  | Ja  | Ja  | Ja  |
| Application Gateway  | Ja  | Ja  | Ja  |
| Wartungssteuerung   | Nein  | Ja  | Ja  |
| Auflistung von VMs einer Gruppe  | Ja  | Ja  | Ja, Auflistung der VMs einer Verfügbarkeitsgruppe  |
| Azure-Warnungen  | Nein  | Ja  | Ja  |
| VM Insights  | Nein  | Ja  | Ja  |
| Azure Backup  | Ja  | Nein  | Ja  |
| Azure Site Recovery  | Ja (über PowerShell)  | Nein  | Ja  |
| Service Fabric  | Nein  | Ja  | Nein  |
| Azure Kubernetes Service (AKS)/AKE  | Nein  | Ja  | Nein  |


## <a name="get-started-with-flexible-orchestration-mode"></a>Erste Schritte mit dem Orchestrierungsmodus „Flexibel“

Registrieren Sie den [flexiblen Orchestrierungsmodus](..\virtual-machines\flexible-virtual-machine-scale-sets.md) für Ihre Skalierungsgruppe für virtuelle Computer und beginnen Sie damit. 


## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

- **Welcher Skalierungsgrad wird für die flexible Orchestrierung unterstützt?**

    Im Orchestrierungsmodus „Flexibel“ können Sie einer Skalierungsgruppe bis zu 1.000 VMs hinzufügen.

- **Was ist in Bezug auf die Verfügbarkeit der flexiblen Orchestrierung der Unterschied zu Verfügbarkeitsgruppen oder zur einheitlichen Orchestrierung?**

    | Verfügbarkeitsattribut  | Flexible Orchestrierung  | Einheitliche Orchestrierung  | Verfügbarkeitsgruppen  |
    |-|-|-|-|
    | Übergreifende Bereitstellung in mehreren Verfügbarkeitszonen  | Nein  | Ja  | Nein  |
    | Garantien zur Verfügbarkeit von Fehlerdomänen in einer Region  | Ja. Maximal 1.000 Instanzen können auf bis zu drei Fehlerdomänen in der Region verteilt werden. Maximale Anzahl von Fehlerdomänen variiert je nach Region  | Ja, bis zu 100 Instanzen  | Ja, bis zu 200 Instanzen  |
    | Platzierungsgruppen  | Im Modus „Flexibel“ werden immer mehrere Platzierungsgruppen genutzt (singlePlacementGroup = false).  | Sie können „Einzelne Platzierungsgruppe“ oder „Mehrere Platzierungsgruppen“ auswählen. | –  |
    | Updatedomänen  | Keine. Wartungs- oder Hostupdates werden Fehlerdomäne für Fehlerdomäne durchgeführt.  | Bis zu 5 Updatedomänen  | Bis zu 20 Updatedomänen  |

- **Wie hoch ist die absolute maximale Anzahl von Instanzen mit Verfügbarkeitsgarantie für die Fehlerdomänen?**

    | Funktion  | Von flexibler Orchestrierung unterstützt (Vorschau)  | Von einheitlicher Orchestrierung unterstützt (allgemeine Verfügbarkeit)  | Von Verfügbarkeitsgruppen unterstützt (allgemeine Verfügbarkeit)  |
    |-|-|-|-|
    | Maximale Anzahl von Instanzen (mit Verfügbarkeitsgarantie für die Fehlerdomänen)  | 1000  | 3000  | 200  |


## <a name="troubleshoot-scale-sets-with-flexible-orchestration"></a>Problembehandlung für Skalierungsgruppen mit flexibler Orchestrierung
Ermitteln Sie die richtige Lösung für Ihr Problembehandlungsszenario.

```
InvalidParameter. The value 'False' of parameter 'singlePlacementGroup' is not allowed. Allowed values are: True
```

**Ursache:** Das Abonnement ist nicht für den Orchestrierungsmodus „Flexibel“ (öffentliche Vorschauphase) registriert.

**Lösung:** Befolgen Sie die obige Anleitung, um die Registrierung für den Orchestrierungsmodus „Flexibel“ (öffentliche Vorschauphase) durchzuführen.

```
InvalidParameter. The specified fault domain count 2 must fall in the range 1 to 1.
```

**Ursache:** Der Parameter `platformFaultDomainCount` ist für die ausgewählte Region oder Zone ungültig.

**Lösung:** Sie müssen einen gültigen Wert für `platformFaultDomainCount` auswählen. Für zonale Bereitstellungen beträgt der Maximalwert für `platformFaultDomainCount` „1“. Bei regionalen Bereitstellungen ohne Angabe einer Zone variiert der Maximalwert für `platformFaultDomainCount` je nach Region. Lesen Sie die Informationen im Abschnitt zum [Verwalten der Verfügbarkeit von VMs für Skripts](../virtual-machines/availability.md), um die maximale Anzahl von Fehlerdomänen pro Region zu ermitteln.

```
OperationNotAllowed. Deletion of Virtual Machine Scale Set is not allowed as it contains one or more VMs. Please delete or detach the VM(s) before deleting the Virtual Machine Scale Set.
```

**Ursache:** Es wird versucht, eine Skalierungsgruppe im Modus „Flexibel“ zu löschen, die mindestens einem virtuellen Computer zugeordnet ist.

**Lösung:** Löschen Sie alle virtuellen Computer, die der Skalierungsgruppe im Orchestrierungsmodus „Flexibel“ zugeordnet sind. Anschließend können Sie die Skalierungsgruppe löschen.

```
InvalidParameter. The value 'True' of parameter 'singlePlacementGroup' is not allowed. Allowed values are: False.
```
**Ursache:** Das Abonnement ist für den Orchestrierungsmodus „Flexibel“ (Vorschau) registriert, aber der Parameter `singlePlacementGroup` ist auf *True* festgelegt.

**Lösung:** `singlePlacementGroup` muss auf *False* festgelegt werden.


## <a name="next-steps"></a>Nächste Schritte
> [!div class="nextstepaction"]
> [Informieren Sie sich über die Bereitstellung Ihrer Anwendung in einer Skalierungsgruppe.](virtual-machine-scale-sets-deploy-app.md)
