---
title: Flexible Orchestrierung für VM-Skalierungsgruppen in Azure
description: In diesem Artikel wird beschrieben, wie Sie den Orchestrierungsmodus „Flexibel“ für VM-Skalierungsgruppen in Azure verwenden.
author: fitzgeraldsteele
ms.author: fisteele
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: flexible-scale-sets
ms.date: 10/13/2021
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurecli, vmss-flex
ms.openlocfilehash: 3403cebab79b49f3a22353bec0a03b68b5a24f0f
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131432831"
---
# <a name="flexible-orchestration-for-virtual-machine-scale-sets-in-azure"></a>Flexible Orchestrierung für VM-Skalierungsgruppen in Azure

**Gilt für:** :heavy_check_mark: Flexible Skalierungsgruppen

VM-Skalierungsgruppen im Orchestrierungsmodus „Flexibel“ ermöglichen Ihnen das Kombinieren der Skalierbarkeit von [VM-Skalierungsgruppen im Orchestrierungsmodus „Uniform“ (einheitlich)](../virtual-machine-scale-sets/overview.md) mit den regionalen Verfügbarkeitsgarantien von [Verfügbarkeitsgruppen](availability-set-overview.md).

Mit Azure-VM-Skalierungsgruppen können Sie eine Gruppe VMs mit Lastenausgleich erstellen und verwalten. Die Anzahl von VM-Instanzen kann automatisch erhöht oder verringert werden, wenn sich der Bedarf ändert, oder es kann ein Zeitplan festgelegt werden. Skalierungsgruppen verfügen über die folgenden wichtigen Vorteile:
- Einfaches Erstellen und Verwalten von mehreren VMs
- Bietet Hochverfügbarkeit und Anwendungsresilienz durch das Verteilen von VMs auf Verfügbarkeitszonen oder Fehlerdomänen.
- Automatische Skalierung der Anwendung bei sich änderndem Ressourcenbedarf
- Großer Umfang

Im Orchestrierungsmodus „Flexibel“ können Sie im gesamten Azure-VM-Ökosystem eine einheitliche Benutzeroberfläche nutzen. Bei der flexiblen Orchestrierung wird Hochverfügbarkeit garantiert (für bis zu 1.000 VMs), indem VMs auf Fehlerdomänen in einer Region oder einer Verfügbarkeitszone verteilt werden. Dies ermöglicht Ihnen das Aufskalieren Ihrer Anwendung bei gleichzeitiger Fehlerdomänenisolation, die für die Ausführung von quorumbasierten oder zustandsbehafteten Workloads von entscheidender Bedeutung ist. Beispiele:
- Quorumbasierte Workloads
- Open-Source-Datenbanken
- Zustandsbehaftete Anwendungen
- Dienste, die Hochverfügbarkeit und eine starke Skalierung erfordern
- Dienste, bei denen VM-Typen gemischt oder Spot- und On-Demand-VMs zusammen genutzt werden sollen
- Vorhandene Verfügbarkeitsgruppen-Anwendungen

> [!IMPORTANT]
> In diesem Artikel geht es um VM-Skalierungsgruppen im Orchestrierungsmodus „Flexibel“, der für alle neuen Bereitstellungen von Skalierungsgruppen empfohlen wird. Informationen zu einheitlichen (Uniform) Skalierungsgruppen finden Sie in der Dokumentation [VM-Skalierungsgruppen im Orchestrierungsmodus „Uniform“ (einheitlich)](../virtual-machine-scale-sets/overview.md).

Informieren Sie sich unter [Vorschau: Orchestrierungsmodi für VM-Skalierungsgruppen in Azure](../virtual-machine-scale-sets/virtual-machine-scale-sets-orchestration-modes.md) über die Unterschiede der Skalierungsgruppen vom Typ „Einheitlich“ und „Flexibel“.

> [!CAUTION]
> Der Orchestrierungsmodus wird beim Erstellen der Skalierungsgruppe festgelegt und kann später nicht mehr geändert oder aktualisiert werden.


## <a name="why-use-virtual-machine-scale-sets"></a>Gründe für die Verwendung von VM-Skalierungsgruppen
Zur Sicherstellung von Redundanz und einer höheren Leistung werden Anwendungen normalerweise auf mehrere Instanzen verteilt. Kunden können auf Ihre Anwendung über ein Lastenausgleichsmodul zugreifen, mit dem Anforderungen jeweils auf eine der Anwendungsinstanzen verteilt werden. Wenn Sie Wartungsarbeiten durchführen oder eine Anwendungsinstanz aktualisieren müssen, müssen Ihre Kunden auf eine andere verfügbare Anwendungsinstanz verteilt werden. Unter Umständen müssen Sie die Anzahl von Anwendungsinstanzen erhöhen, die Ihre Anwendung ausführen, um die zusätzliche Kundennachfrage zu decken.

Über Azure-VM-Skalierungsgruppen werden die Verwaltungsfunktionen für Anwendungen, die auf vielen VMs ausgeführt werden, die automatische Skalierung von Ressourcen und der Lastenausgleich für Datenverkehr bereitgestellt. Skalierungsgruppen verfügen über die folgenden wichtigen Vorteile:

- **Einfaches Erstellen und Verwalten von mehreren VMs**
    - Wenn Sie über viele VMs verfügen, auf denen Ihre Anwendung ausgeführt wird, ist es wichtig, für Ihre gesamte Umgebung eine einheitliche Konfiguration zu nutzen. Eine zuverlässige Leistung der Anwendung wird erzielt, wenn die VM-Größe, Datenträgerkonfiguration und Anwendungsinstallationen für alle VMs gleich sind.
    - Bei Skalierungsgruppen werden alle VM-Instanzen aus demselben Betriebssystem-Basisimage und derselben Konfiguration erstellt. Mit diesem Ansatz können Sie problemlos Hunderte von VMs ohne zusätzliche Konfigurationstasks oder Netzwerkverwaltung verwalten.
    - Skalierungsgruppen unterstützen die Verwendung von [Azure Load Balancer](../load-balancer/load-balancer-overview.md) für eine grundlegende Layer-4-Datenverkehrsverteilung und von [Azure Application Gateway](../application-gateway/overview.md) für eine erweiterte Layer-7-Datenverkehrsverteilung und TLS-Terminierung.

- **Hochverfügbarkeit und Anwendungsresilienz**
    - Skalierungsgruppen werden verwendet, um mehrere Instanzen Ihrer Anwendung auszuführen. Wenn für eine dieser VM-Instanzen ein Problem besteht, können Kunden nach einer minimalen Unterbrechung über eine der anderen VM-Instanzen weiter auf Ihre Anwendung zugreifen.
    - Zur Steigerung der Verfügbarkeit können Sie [Verfügbarkeitszonen](../availability-zones/az-overview.md) einsetzen. Hierbei werden VM-Instanzen einer Skalierungsgruppe in einem Rechenzentrum oder rechenzentrumsübergreifend automatisch verteilt.

- **Automatische Skalierung der Anwendung bei sich änderndem Ressourcenbedarf**
    - Die Kundennachfrage nach Ihrer Anwendung kann sich innerhalb eines Tages oder einer Woche ändern. Zur Erfüllung der Kundennachfrage können Skalierungsgruppen die Anzahl von VM-Instanzen automatisch erhöhen, wenn der Bedarf für die Anwendung zunimmt, und sie dann wieder verringern, wenn der Bedarf sinkt.
    - Die automatische Skalierung verringert auch die Anzahl von unnötigen VM-Instanzen, auf denen Ihre Anwendung ausgeführt wird, wenn die Nachfrage gering ist. Kunden erhalten weiterhin eine akzeptable Leistungsebene, wenn die Nachfrage zunimmt und automatisch weitere VM-Instanzen hinzugefügt werden. Diese Möglichkeit trägt zur Reduzierung von Kosten und zur effizienten bedarfsabhängigen Erstellung von Azure-Ressourcen bei.

- **Großer Umfang**
    - Skalierungssätze unterstützen bis zu 1.000 VM-Instanzen für Standard-Marktplatz-Images und benutzerdefinierte Images über die Azure Compute Gallery (früher bekannt als Shared Image Gallery). Wenn Sie eine Skalierungsgruppe mithilfe eines verwalteten Images erstellen, beträgt der Grenzwert 600 VM-Instanzen.
    - Verwenden Sie [Azure Managed Disks](../virtual-machines/managed-disks-overview.md), um für Produktionsworkloads die beste Leistung zu erzielen.



## <a name="get-started-with-flexible-orchestration-mode"></a>Erste Schritte mit dem Orchestrierungsmodus „Flexibel“

Führen Sie die ersten Schritte im Orchestrierungsmodus „Flexibel“ für Ihre Skalierungsgruppen über das [Azure-Portal](flexible-virtual-machine-scale-sets-portal.md), die [Azure CLI](flexible-virtual-machine-scale-sets-cli.md), [Azure PowerShell](flexible-virtual-machine-scale-sets-powershell.md) oder eine [ARM-Vorlage](flexible-virtual-machine-scale-sets-rest-api.md) aus. 

> [!IMPORTANT]
> Vergewissern Sie sich, dass Sie über explizite ausgehende Netzwerkkonnektivität verfügen. Erfahren Sie mehr darüber in [Virtuelle Netzwerke und virtuelle Computer in Azure](../virtual-network/network-overview.md), und stellen Sie sicher, dass Sie die [bewährten Methoden](../virtual-network/concepts-and-best-practices.md) für Netzwerke von Azure beachten.


## <a name="add-instances-with-autoscaling-or-manually"></a>Hinzufügen von Instanzen mithilfe der automatischen Skalierung oder durch manuelles Hinzufügen
VM-Skalierungsgruppen mit der Orchestrierung „Flexibel“ fungieren als dünne Orchestrierungsebene zum Verwalten mehrerer VMs. Es gibt mehrere Möglichkeiten zum Hinzufügen von VMs, die durch die Skalierungsgruppe verwaltet werden:

- **Festlegen der Anzahl der Instanzen**

    Wenn Sie die Skalierungsgruppe mit der Orchestrierung „Flexibel“ erstellen, definieren Sie ein VM-Profil oder eine Vorlage, das bzw. die die aufzuskalierende Vorlage beschreibt. Anschließend können Sie den Kapazitätsparameter festlegen, um die Anzahl der durch die Skalierungsgruppe verwalteten VM-Instanzen zu erhöhen oder zu verringern. 

- **Automatische Skalierung mit Metriken oder Zeitplan** 

    Alternativ können Sie Regeln für die Autoskalierung einrichten, um die Kapazität basierend auf Metriken oder einem Zeitplan zu erhöhen oder zu verringern. Weitere Informationen finden Sie unter [Übersicht über die automatische Skalierung mit Azure-VM-Skalierungsgruppen](..\virtual-machine-scale-sets\virtual-machine-scale-sets-autoscale-overview.md). 

- **Festlegen einer Skalierungsgruppe beim Erstellen einer VM**

    Wenn Sie eine VM erstellen, können Sie optional festlegen, dass diese zu einer VM-Skalierungsgruppe hinzugefügt wird. Eine VM kann nur zum Zeitpunkt ihrer Erstellung zu einer Skalierungsgruppe hinzugefügt werden. Der neu erstellte virtuelle Computer muss sich unabhängig von den Bereitstellungsmethoden in derselben Ressourcengruppe wie die flexible Skalierungsgruppe finden.

Der flexible Orchestrierungsmodus kann mit VM-SKUs verwendet werden, die [speichererhaltende Updates oder Livemigration](../virtual-machines/maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot) unterstützen. Dies umfasst 90 % aller iaaS-VMs, die in Azure bereitgestellt werden. Im Großen und Ganzen schließt dies universelle Größenfamilien ein, wie virtuelle Computer der B-, D-, E- und F-Serie. Derzeit kann der flexible Modus nicht über VM-SKUs oder Familien orchestrieren, die keine speichererhaltenden Updates unterstützen, einschließlich virtueller Computer der G-, H-, L-, M-, N-Serie. Sie können die [Computeressourcen-SKUs-API](/rest/api/compute/resource-skus/list) verwenden, um zu bestimmen, ob eine bestimmte VM-SKU unterstützt wird.

```azurecli-interactive
az vm list-skus -l eastus --size standard_d2s_v3 --query "[].capabilities[].[name, value]" -o table
```

> [!IMPORTANT]
> Das Netzwerkverhalten hängt davon ab, wie Sie virtuelle Computer in Ihrer Skalierungsgruppe erstellen. Weitere Informationen finden Sie im Abschnitt [Skalierbare Netzwerkkonnektivität](../virtual-machines/flexible-virtual-machine-scale-sets-migration-resources.md#create-scalable-network-connectivity).


## <a name="features"></a>Funktionen
In den folgenden Tabellen sind die Features des Orchestrierungsmodus „Flexibel“ sowie Links zur entsprechenden Dokumentation aufgeführt.

### <a name="basic-setup"></a>Grundlegende Einrichtung

| Feature | Von flexibler Orchestrierung für Skalierungsgruppen unterstützt |
|---|---|
| Typ des virtuellen Computers  | Azure IaaS-Standard-VM (Microsoft.compute /virtualmachines)  |
| Maximale Anzahl von Instanzen (mit Garantien für die Fehlerdomänen)  | 1000  |
| Unterstützte SKUs  | D-Serie, E-Serie, F-Serie, A-Serie, B-Serie, Intel, AMD; spezielle SKUs (G, H, L, M, N) werden nicht unterstützt. |
| Vollständige Kontrolle über VM, NICs, Datenträger  | Ja  |
| RBAC-Berechtigungen erforderlich  | Compute-VMSS Schreiben, Compute-VM Schreiben, Netzwerk |
| Beschleunigte Netzwerke  | Ja  |
| Spot-Instanzen und Preise   | Ja. Sie können sowohl Spot-Instanzen als auch Instanzen mit regulärer Priorität verwenden.  |
| Mischung von Betriebssystemen  | Ja. Linux und Windows können in derselben flexiblen Skalierungsgruppe vorhanden sein.  |
| Datenträgertypen  | Nur verwaltete Datenträger, alle Speichertypen  |
| Schreibbeschleunigung   | Nein  |
| Näherungsplatzierungsgruppen   | Ja. Weitere Informationen finden Sie unter [Erstellen und Verwenden von Näherungsplatzierungsgruppen mit PowerShell](../virtual-machine-scale-sets/proximity-placement-groups.md). |
| Dedizierte Azure-Hosts   | Nein  |
| Verwaltete Identität  | Nur benutzerseitig zugewiesene Identität  |
| Hinzufügung/Entfernung einer vorhandenen VM für Gruppe  | Nein  |
| Service Fabric  | Nein  |
| Azure Kubernetes Service (AKS)/AKE  | Nein  |
| UserData  | Partiell, UserData können für einzelne VMs angegeben werden. |


### <a name="autoscaling-and-instance-orchestration"></a>Autoskalierung und Instanzorchestrierung

| Feature | Von flexibler Orchestrierung für Skalierungsgruppen unterstützt |
|---|---|
| Auflistung von VMs einer Gruppe | Ja |
| Automatische Skalierung (manuell, basierend auf Metriken, basierend auf einem Zeitplan) | Ja |
| Automatische Entfernung von NICs und Datenträgern beim Löschen von VM-Instanzen | Ja |
| Upgraderichtlinie (VM-Skalierungsgruppen) | Nein, die Upgraderichtlinie muss während der Erstellung NULL oder [] sein |
| Automatische Betriebssystemupdates (VM-Skalierungsgruppen) | Nein |
| Sicherheitspatches auf Gastbetriebssystem | Ja |
| Beendigung von Benachrichtigungen (VM-Skalierungsgruppen) | Ja. Weitere Informationen finden Sie unter [Beendigungsbenachrichtigung für Instanzen von Azure-VM-Skalierungsgruppen](../virtual-machine-scale-sets/virtual-machine-scale-sets-terminate-notification.md). |
| Überwachung der Anwendungsintegrität | Erweiterung für Anwendungsintegrität |
| Instanzreparatur (VM-Skalierungsgruppen) | Ja. Weitere Informationen finden Sie unter [Automatische Instanzreparaturen für Azure-VM-Skalierungsgruppen](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-instance-repairs.md). |
| Instanzschutz | Nein, verwenden Sie [Azure-Ressourcensperre](../azure-resource-manager/management/lock-resources.md). |
| Richtlinie für Abskalieren | Nein |
| VMSS-Instanzansicht abrufen | Nein |
| VM-Batchvorgänge (Alle starten, Alle beenden, Teilmenge löschen usw.) | Nein (kann Vorgänge auf jeder Instanz mithilfe der VM-API auslösen) |

### <a name="high-availability"></a>Hochverfügbarkeit 

| Feature | Von flexibler Orchestrierung für Skalierungsgruppen unterstützt |
|---|---|
| Verfügbarkeits-SLA | 99,95 % für auf Fehlerdomänen verteilte Instanzen; 99,99 % für auf mehrere Zonen verteilte Instanzen |
| Verfügbarkeitszonen | Geben Sie an, dass Instanzen in einer, zwei oder drei Verfügbarkeitszonen angeordnet sein sollen |
| VM zu einer bestimmten Verfügbarkeitszone zuweisen | Ja |
| Fehlerdomäne – Maximale Verteilung (Azure verteilt die Instanzen maximal) | Ja |
| Fehlerdomäne – Feste Verteilung | 2 bis 3 Fehlerdomänen (je nach regionaler maximaler Anzahl der Fehlerdomänen), 1 für zonale Bereitstellungen |
| Zuweisung eines virtuellen Computers zu einer bestimmten Fehlerdomäne | Ja |
| Updatedomänen | Veraltet (Plattformwartung wird nacheinander für jede Fehlerdomäne einzeln durchgeführt) |
| Wartung durchführen | Wartung auf jeder Instanz mithilfe der VM-API auslösen |

### <a name="networking"></a>Netzwerk 

| Feature | Von flexibler Orchestrierung für Skalierungsgruppen unterstützt |
|---|---|
| Standardmäßige ausgehende Konnektivität | Nein, es muss [explizite ausgehende Konnektivität](../virtual-network/ip-services/default-outbound-access.md) vorhanden sein. |
| Azure Load Balancer Standard-SKU | Ja |
| Application Gateway | Ja |
| InfiniBand-Netzwerk | Nein |
| Basis-SLB | Nein |
| Netzwerkportweiterleitung | Ja (NAT-Regeln für einzelne Instanzen) |

### <a name="backup-and-recovery"></a>Sicherung und Wiederherstellung 

| Feature | Von flexibler Orchestrierung für Skalierungsgruppen unterstützt |
|---|---|
| Azure Backup  | Ja |
| Azure Site Recovery | Ja (über PowerShell) |
| Azure-Warnungen  | Ja |
| VM Insights  | Kann auf einzelnen VMs installiert werden. |

### <a name="unsupported-parameters"></a>Nicht unterstützte Parameter

Die folgenden Parameter für VM-Skalierungsgruppen werden für VM-Skalierungsgruppen im Orchestrierungsmodus „Flexibel“ derzeit nicht unterstützt:
- Einzelne Platzierungsgruppe: Sie müssen `singlePlacementGroup=False` auswählen.
- Bereitstellung mithilfe von speziellen SKUs: VM-Familien der Serien G, H, L, M und N
- Ultra-Datenträgerkonfiguration: `diskIOPSReadWrite`, `diskMBpsReadWrite`
- Überbereitstellung von VM-Skalierungsgruppen
- Imagebasierte automatische Betriebssystemupgrades
- Anwendungsintegrität über SLB-Integritätstest: Verwenden Sie die Erweiterung für Anwendungsintegrität für Instanzen.
- Upgraderichtlinie für VM-Skalierungsgruppen: Dieser Wert muss „NULL“ lauten oder leer sein.
- Bereitstellung in Azure Dedicated Host
- Nicht verwaltete Datenträger
- Abskalierungsrichtlinie für VM-Skalierungsgruppen
- Schutz von VM-Skalierungsgruppeninstanzen
- Load Balancer Basic
- Portweiterleitung über Load Balancer Standard-NAT-Pools: Sie können NAT-Regeln für bestimmte Instanzen konfigurieren.


## <a name="troubleshoot-scale-sets-with-flexible-orchestration"></a>Problembehandlung für Skalierungsgruppen mit flexibler Orchestrierung
Ermitteln Sie die richtige Lösung für Ihr Problembehandlungsszenario.

<!-- error -->
### <a name="invalidparameter-the-specified-fault-domain-count-3-must-fall-in-the-range-1-to-2"></a>Fehlermeldung: InvalidParameter. Die angegebene Fehlerdomänenanzahl 3 muss im Bereich 1 bis 2 liegen.

```
InvalidParameter. The specified fault domain count 3 must fall in the range 1 to 2.
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
**Ursache:** Der Parameter `singlePlacementGroup` ist auf *True* festgelegt.

**Lösung:** `singlePlacementGroup` muss auf *False* festgelegt werden.


<!-- error -->
### <a name="outboundconnectivitynotenabledonvm-no-outbound-connectivity-configured-for-virtual-machine"></a>OutboundConnectivityNotEnabledOnVM. Für den virtuellen Computer ist keine ausgehende Konnektivität konfiguriert.

```
OutboundConnectivityNotEnabledOnVM. No outbound connectivity configured for virtual machine.
```
**Ursache:** Es wurde versucht, eine VM-Skalierung gruppe im Orchestrierungsmodus „Flexibel“ ohne ausgehende Internetverbindung zu erstellen.

**Lösung:** Aktivieren Sie sicheren ausgehenden Zugriff für Ihre VM-Skalierungsgruppe auf die für Ihre Anwendung am besten geeignete Weise. Der ausgehende Zugriff kann mit einem NAT Gateway in Ihrem Subnetz, durch Hinzufügen von Instanzen zu einem Load Balancer-Back-End-Pool oder durch Hinzufügen einer expliziten öffentlichen IP-Adresse pro Instanz aktiviert werden. Für hochsichere Anwendungen können Sie angepasste benutzerdefinierte Routen (UDRs) über Ihre Firewall oder virtuellen Netzwerkanwendungen angeben. Weitere Detailinformationen finden Sie unter [Standardmäßiger ausgehender Zugriff](../virtual-network/ip-services/default-outbound-access.md).

## <a name="next-steps"></a>Nächste Schritte
> [!div class="nextstepaction"]
> [Orchestrierungsmodus „Flexibel“ für Ihre Skalierungsgruppen mit dem Azure-Portal](flexible-virtual-machine-scale-sets-portal.md)