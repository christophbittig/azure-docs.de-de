---
title: Grundlegendes zu VM-Neustarts
description: Grundlegendes zu VM-Neustarts – Gegenüberstellung von Wartung und Downtime
author: mimckitt
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 03/08/2021
ms.author: mimckitt
ms.reviewer: cynthn
ms.openlocfilehash: dda6b0c5d2087b588e69e6d5507eea4cdcbc1caf
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122690855"
---
# <a name="understand-vm-reboots---maintenance-vs-downtime"></a>Grundlegendes zu VM-Neustarts – Gegenüberstellung von Wartung und Downtime

**Gilt für:** :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows-VMs :heavy_check_mark: Flexible Skalierungsgruppen :heavy_check_mark: Einheitliche Skalierungsgruppen

Drei Szenarien können zu einer Beeinträchtigung von VMs in Azure führen: eine ungeplante Hardwarewartung, eine unerwartete Downtime und eine geplante Wartung.

## <a name="unplanned-hardware-maintenance-event"></a>Ereignis für ungeplante Hardwarewartung
Eine ungeplante Hardwarewartung tritt ein, wenn auf der Azure-Plattform der Ausfall einer Hardware- oder Plattformkomponente für einen physischen Computer prognostiziert wird. Daraufhin wird ein Ereignis für eine ungeplante Hardwarewartung initiiert, um die Auswirkungen auf virtuelle Computer, die die betroffene Hardware nutzen, möglichst gering zu halten. Die virtuellen Computer werden von Azure unter Verwendung von [Livemigrationstechnologie](./maintenance-and-updates.md) von der fehlerhaften Hardware zu einem fehlerfreien physischen Computer migriert. Bei der Livemigration wird der virtuelle Computer lediglich kurzzeitig angehalten. Arbeitsspeicher, geöffnete Dateien und bestehende Netzwerkverbindungen bleiben erhalten, die Leistung kann jedoch vor und/oder nach dem Ereignis beeinträchtigt sein. Falls die Livemigration nicht verwendet werden kann, kommt es bei dem virtuellen Computer wie unten beschrieben zu unerwarteter Ausfallzeit.


## <a name="unexpected-downtime"></a>Unerwartete Downtime
Eine unerwartete Downtime tritt auf, wenn die Hardware oder die physische Infrastruktur für die VM unvorhergesehen ausfällt. Dies kann Ausfälle des lokalen Netzwerks, des lokalen Datenträgers oder andere Fehler auf Rackebene umfassen. Bei Erkennung solcher Probleme migriert die Azure-Plattform die VM automatisch zu einem fehlerfreien physischen Computer im selben Rechenzentrum. Dieser Vorgang ist mit einer gewissen Ausfallzeit (Neustart) und in manchen Fällen mit dem Verlust des temporären Laufwerks verbunden. Die angefügten (Betriebssystem-)Datenträger bleiben in jedem Fall erhalten.

Für VMs kann es im unwahrscheinlichen Fall einer Downtime bzw. eines Notfalls, von dem ein gesamtes Rechenzentrum oder sogar eine ganze Region betroffen ist, auch zu Ausfallzeiten kommen. Für diese Szenarien verfügt Azure über Schutzoptionen, z.B. [Verfügbarkeitszonen](../availability-zones/az-overview.md) und [Regionspaare](regions.md#region-pairs).

## <a name="planned-maintenance-events"></a>Geplante Wartungsereignisse
Geplante Wartungsereignisse sind regelmäßige Updates, die von Microsoft für die zugrunde liegende Azure-Plattform ausgeführt werden können, um die gesamte Verfügbarkeit, Leistung und Sicherheit der Plattforminfrastruktur zu verbessern, unter der die virtuellen Computer ausgeführt werden. Die meisten dieser Updates werden ohne Auswirkungen auf die virtuellen Computer oder Clouddienste ausgeführt. (Weitere Informationen finden Sie unter [Keinen Neustart erfordernde Wartung](maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot)). Die Azure-Plattform versucht zwar, möglichst immer die Wartung mit direkter Migration und Beibehaltung der virtuellen Computer zu verwenden, in seltenen Fällen ist jedoch ein Neustart des virtuellen Computers erforderlich, um die erforderlichen Updates auf die zugrunde liegende Infrastruktur anzuwenden. In diesem Fall können Sie eine geplante Azure-Wartung mit erneuter Bereitstellung nach der Wartung durchführen, indem Sie die Wartung für die entsprechenden virtuellen Computer im geeigneten Zeitfenster initiieren. Weitere Informationen finden Sie unter [Geplante Wartung für virtuelle Windows-Computer](maintenance-and-updates.md).

## <a name="reduce-downtime"></a>Reduzieren der Downtime
Um die Downtime aufgrund eines oder mehrerer dieser Ereignisse zu verringern, sollten Sie die folgenden bewährten Methoden befolgen, um die Hochverfügbarkeit virtueller Computer zu gewährleisten:

* Verwenden von [Verfügbarkeitszonen](../availability-zones/az-overview.md) als Schutz vor Rechenzentrumsausfällen
* Konfigurieren mehrerer VMs in einer [Verfügbarkeitsgruppe](availability-set-overview.md) für höhere Redundanz
* Verwenden von [geplanten Ereignissen für Linux](./linux/scheduled-events.md) oder [geplanten Ereignissen für Windows](./windows/scheduled-events.md) für proaktive Antworten auf Ereignisse, die sich auf VMs auswirken
* Konfigurieren einzelner Anwendungsebenen in separaten Verfügbarkeitsgruppen
* Kombinieren des [Lastenausgleichs](../load-balancer/load-balancer-overview.md) mit Verfügbarkeitszonen oder -gruppen

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zur Verfügbarkeit in Azure finden Sie in der [Übersicht über Verfügbarkeitsoptionen](availability.md).