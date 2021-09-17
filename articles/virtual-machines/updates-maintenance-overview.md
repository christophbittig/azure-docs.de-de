---
title: Übersicht über Updates und Wartung
description: Hier erfahren Sie mehr über die verfügbaren Update- und Wartungsoptionen für virtuelle Computer in Azure.
author: mimckitt
ms.author: mimckitt
ms.service: virtual-machines
ms.topic: overview
ms.date: 08/10/2021
ms.reviewer: cynthn
ms.openlocfilehash: 5e2d200e7133074ffc1dc2732cdedb692c1ea30c
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122698328"
---
# <a name="updates-and-maintenance-overview"></a>Übersicht über Updates und Wartung

**Gilt für:** :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows-VMs :heavy_check_mark: Flexible Skalierungsgruppen :heavy_check_mark: Einheitliche Skalierungsgruppen

Dieser Artikel bietet eine Übersicht über die verschiedenen Update- und Wartungsoptionen für virtuelle Azure-Computer.

## <a name="automatic-os-image-upgrade"></a>Automatisches Upgrade für Betriebssystemimages

Das Aktivieren der [automatischen Betriebssystemimage-Upgrades](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md?context=/azure/virtual-machines/context/context) für Ihre Skalierungsgruppe vereinfacht die Updateverwaltung durch sicheres und automatisches Aktualisieren des Betriebssystem-Datenträgers für alle Instanzen in der Skalierungsgruppe.

Das [automatische Betriebssystemupgrade](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md?context=/azure/virtual-machines/context/context) weist folgende Merkmale auf:

- Nach der Konfiguration wird das neueste von den Imageherausgebern veröffentlichte Betriebssystemimage automatisch ohne Benutzereingriff auf die Skalierungsgruppe angewendet.
- Aktualisiert Batches von Instanzen immer parallel, wenn ein neues Image vom Herausgeber veröffentlicht wird.
- Wird in Anwendungsintegritätstests und [Anwendungsintegritätserweiterung](../virtual-machine-scale-sets/virtual-machine-scale-sets-health-extension.md?context=/azure/virtual-machines/context/context) integriert.
- Unterstützt alle VM-Größen und sowohl Windows- als auch Linux-Images.
- Sie können automatische Upgrades jederzeit abwählen (Betriebssystemupgrades können auch manuell initiiert werden).
- Der Betriebssystemdatenträger eines virtuellen Computers wird durch den neuen, mit der neuesten Imageversion erstellten Betriebssystemdatenträger ersetzt. Konfigurierte Erweiterungen und benutzerdefinierte Datenskripts werden ausgeführt, wobei persistente Datenträger weiterhin aufbewahrt werden.
- [Erweiterungssequenzierung](../virtual-machine-scale-sets/virtual-machine-scale-sets-extension-sequencing.md?context=/azure/virtual-machines/context/context) wird unterstützt.
- Das automatische Upgrade des Betriebssystemimages kann für Skalierungsgruppen beliebiger Größe aktiviert werden.


## <a name="automatic-vm-guest-patching-preview"></a>Automatisches Patchen von VM-Gästen (Vorschau)

Durch das Aktivieren von [automatischen VM-Gastpatches](automatic-vm-guest-patching.md) für Ihre Azure-VMs können Sie die Updateverwaltung vereinfachen, indem Sie virtuelle Computer sicher und automatisch patchen, um Sicherheitskonformität zu wahren.

[Automatische VM-Gastpatches](automatic-vm-guest-patching.md) weisen die folgenden Merkmale auf:
- Patches, die als *Kritisch* oder *Sicherheit* klassifiziert werden, werden automatisch heruntergeladen und auf die VM angewendet.
- Patches werden außerhalb der Spitzenzeiten in der Zeitzone der VM angewendet.
- Die Patchorchestrierung wird von Azure verwaltet, und Patches werden nach den [verfügbarkeitsbasierten Prinzipien](automatic-vm-guest-patching.md#availability-first-updates) angewendet.
- Die Integrität des virtuellen Computers wird anhand von Integritätssignalen der Plattform ermittelt und überwacht, um Patchfehler zu erkennen.
- Funktioniert für alle VM-Größen.


## <a name="automatic-extension-upgrade"></a>Automatisches Erweiterungsupgrade

Das [automatische Erweiterungsupgrade](automatic-extension-upgrade.md) ist für Azure-VMs und Azure Virtual Machine Scale Sets verfügbar. Wenn das automatische Erweiterungsupgrade für eine VM oder eine Skalierungsgruppe aktiviert ist, wird die Erweiterung automatisch aktualisiert, sobald der Erweiterungsherausgeber eine neue Version für diese Erweiterung freigibt.

 Das automatische Erweiterungsupgrade verfügt über die folgenden Features:
- Wird für Azure-VMs und Azure-Virtual Machine Scale Sets unterstützt.
- Upgrades werden in einem verfügbarkeitsbasierten Bereitstellungsmodell angewendet.
- Für eine VM-Skalierungsgruppe werden maximal 20 Prozent der virtuellen Computer in Skalierungsgruppen in einem einzelnen Batch aktualisiert. Die minimale Batchgröße ist ein virtueller Computer.
- Unterstützt alle VM-Größen und sowohl Windows- als auch Linux-Erweiterungen.
- Sie können automatische Upgrades jederzeit kündigen.
- Das automatische Erweiterungsupgrade kann für Virtual Machine Scale Sets beliebiger Größe aktiviert werden.
- Jede unterstützte Erweiterung wird einzeln registriert, und Sie können auswählen, welche Erweiterungen automatisch aktualisiert werden sollen.
- Wird in allen Regionen der öffentlichen Cloud unterstützt.

## <a name="hotpatch"></a>Hotpatch

[Hotpatching](../automanage/automanage-hotpatch.md?context=/azure/virtual-machines/context/context) ist eine neue Methode zur Installation von Updates auf neuen virtuellen Computern (VMs) der Windows Server Azure Edition, die keinen Neustart nach der Installation erfordert. Hotpatches für virtuelle Computer der Windows Server Azure Edition haben die folgenden Vorteile:

- Geringere Auswirkungen auf die Workload mit weniger Neustarts
- Schnellere Bereitstellung von Updates, da die Pakete kleiner sind, schneller installiert werden und eine einfachere Patchorchestrierung mit Azure Update Manager möglich ist
- Besserer Schutz, da die Hotpatch-Updatepakete auf Windows-Sicherheitsupdates beschränkt sind, die schneller und ohne Neustart installiert werden


## <a name="azure-update-management"></a>Azure-Updateverwaltung

Mithilfe der [Updateverwaltung in Azure Automation](../automation/update-management/overview.md?context=/azure/virtual-machines/context/context) können Sie Betriebssystemupdates für Ihre virtuellen Windows- und Linux-Computer in Azure, in lokalen Umgebungen und in anderen Cloudumgebungen verwalten. Sie können den Status der verfügbaren Updates auf allen Agent-Computern schnell auswerten und die Installation der für den Server erforderlichen Updates initiieren.

## <a name="maintenance-control"></a>Wartungssteuerung

Verwalten Sie mit der [Wartungssteuerung](maintenance-control.md) Plattformupdates, die keinen Neustart erfordern. Azure aktualisiert die eigene Infrastruktur häufig, um die Zuverlässigkeit, Leistung und Sicherheit zu verbessern oder neue Features zu integrieren. Die meisten Updates sind für Benutzer transparent. Einige empfindliche Workloads wie Gaming, Medienstreaming und Finanztransaktionen können nicht einmal wenige Sekunden tolerieren, in denen eine VM zur Wartung eingefroren oder getrennt wird. Die Wartungssteuerung bietet Ihnen die Möglichkeit, auf Plattformupdates zu warten und diese innerhalb eines 35-tägigen rollierenden Zeitfensters anzuwenden.

Mit der Wartungssteuerung können Sie entscheiden, wann Sie Updates auf Ihre isolierten VMs und dedizierten Azure-Hosts anwenden.

Mit der [Wartungssteuerung](maintenance-control.md) können Sie folgende Aktionen ausführen:
- Batchupdates in ein Updatepaket
- Warten von bis zu 35 Tagen bis zur Anwendung der Updates
- Automatisieren Sie Plattformupdates durch Konfigurieren eines Wartungszeitplans oder mithilfe von [Azure Functions](https://github.com/Azure/azure-docs-powershell-samples/tree/master/maintenance-auto-scheduler).
- Wartungskonfigurationen funktionieren abonnement- und ressourcengruppenübergreifend.


## <a name="scheduled-events"></a>Geplante Ereignisse

Scheduled Events ist ein Azure-Metadatendienst, der Ihren Anwendungen Zeit zur Vorbereitung auf die Wartung virtueller Computer gibt. Er stellt Informationen zu geplanten Wartungsereignissen (z.B. Neustart) bereit, damit Ihre Anwendung sich darauf vorbereiten und Unterbrechungen begrenzen kann. Der Dienst steht für alle Azure-VM-Typen zur Verfügung, einschließlich PaaS und IaaS unter Windows und Linux.

Informationen zu Scheduled Events finden Sie unter [Instance Metadata Service: Scheduled Events für Windows-VMs](./windows/scheduled-events.md) und [Instance Metadata Service: Scheduled Events für Linux-VMs](./linux/scheduled-events.md).

## <a name="next-steps"></a>Nächste Schritte

Weitere Möglichkeiten zur Erhöhung der Betriebszeit Ihrer Anwendungen und Dienste finden Sie in der Dokumentation [Verfügbarkeitsoptionen für virtuelle Computer in Azure](availability.md).
