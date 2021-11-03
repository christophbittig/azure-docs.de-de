---
title: Regionale Redundanz und Failoverwiederherstellung mit Azure HPC Cache
description: Erfahren Sie etwas über Verfahren zur Bereitstellung von Failoverfunktionen für die Notfallwiederherstellung mit Azure HPC Cache.
author: femila
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 08/19/2021
ms.author: femila
ms.openlocfilehash: b47e664bcbe5435a527940c0c1df8f92d95d5cb5
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131078538"
---
# <a name="use-multiple-caches-for-regional-failover-recovery"></a>Verwenden mehrerer Caches für die regionale Failoverwiederherstellung

Jede Azure HPC Cache-Instanz wird innerhalb eines bestimmten Abonnements und in genau einer Region ausgeführt. Das bedeutet, dass Ihr Cache-Workflow möglicherweise gestört wird, wenn die Region des Caches vollständig ausfällt.

In diesem Artikel wird eine Strategie beschrieben, mit der Sie das Risiko einer Unterbrechung der Arbeit reduzieren können, indem Sie eine zweite Region für das Cachefailover verwenden.

Zentral ist dabei die Verwendung von Back-End-Speicher, auf den von mehreren Regionen aus zugegriffen werden kann. Bei diesem Speicher kann es sich um ein lokales NAS-System mit entsprechender DNS-Unterstützung oder um eine Azure Blob Storage-Instanz handeln, die sich in einer anderen Region als der Cache befindet.

Während Ihr Workflow in Ihrer primären Region ausgeführt wird, werden die Daten im langfristigen Speicher außerhalb der Region gespeichert. Wenn die Cacheregion nicht mehr verfügbar ist, können Sie ein Duplikat der Azure HPC Cache-Instanz in einer sekundären Region erstellen und mit dem gleichen Speicher verbinden und dann die Arbeit mit dem neuen Cache fortsetzen.

> [!NOTE]
> Dieser Failover-Plan deckt einen kompletten Ausfall in der Region eines *Speicherkontos* nicht ab. Außerdem unterstützt Azure HPC Cache keine geografisch redundanten Speicherkonten (GRS oder GZRS), da ihr asynchrones Kopieren zwischen Regionen für HPC Cache-Workflows nicht konsistent genug ist.
>
> HPC Cache **unterstützt** lokal redundanten Speicher (LRS) und zonenredundanten Speicher (ZRS), die Daten [innerhalb einer Azure-Region replizieren](../storage/common/storage-redundancy.md#redundancy-in-the-primary-region).
>
> Erwägen Sie eine manuelle Sicherungsstrategie, wenn Sie sich vor Speicherausfällen in der gesamten Region schützen müssen.

## <a name="planning-for-regional-failover"></a>Planen regionaler Failover

Führen Sie die folgenden Schritte aus, um einen Cache einzurichten, der auf ein mögliches Failover vorbereitet ist:

1. Stellen Sie sicher, dass von einer zweiten Region aus auf den Back-End-Speicher zugegriffen werden kann.
1. Sie sollten schon bei der Planung der primären Cache-Instanz die Replikation dieses Konfigurationsschritts in der zweiten Region vorbereiten. Fügen Sie Folgendes ein:

   1. Struktur mit virtuellem Netzwerk und Subnetz
   1. Cachekapazität
   1. Details des Speicherziels, Namen und Namespacepfade
   1. Details zu Clientcomputern, sofern diese sich in derselben Region wie der Cache befinden
   1. Befehl zum Einbinden für die Verwendung durch Cacheclients

   > [HINWEIS] Azure HPC Cache kann programmgesteuert erstellt werden, entweder über eine [Azure Resource Manager-Vorlage](../azure-resource-manager/templates/overview.md) oder durch direkten Zugriff auf seine API. Ausführliche Informationen erhalten Sie vom Azure HPC Cache-Team.

## <a name="failover-example"></a>Beispiel für ein Failover

Gehen Sie beispielsweise davon aus, dass Sie Ihre Azure HPC Cache-Instanz in der Region „USA, Osten“ erstellen möchten. Sie greift auf Daten zu, die in Ihrem lokalen Rechenzentrum gespeichert sind.

Sie können einen Cache in der Region „USA, Westen 2“ als Sicherung für Failover verwenden.

Beim Erstellen des Caches in der Region „USA, Osten“ bereiten Sie einen zweiten Cache für die Bereitstellung in der Region „USA, Westen 2“ vor. Sie können Skripts oder Vorlagen verwenden, um diese Vorbereitungsschritte zu automatisieren.

Erstellen Sie im Fall eines regionsweiten Ausfalls in der Region „USA, Osten“ den Cache, den Sie in der Region „USA, Westen 2“ vorbereitet haben.

Fügen Sie nach dem Erstellen des Caches Speicherziele hinzu, die auf dieselben lokalen Datenspeicher verweisen, und verwenden Sie dieselben aggregierten Namespacepfade wie für die Speicherziele des alten Caches.

Wenn die ursprünglichen Clients betroffen sind, erstellen Sie neue Clients in der Region „USA, Westen 2“ für die Verwendung mit dem neuen Cache.

Alle Clients müssen den neuen Cache einbinden, auch wenn sie nicht vom Regionsausfall betroffen waren. Der neue Cache hat andere Einbindungsadressen als der alte.

## <a name="learn-more"></a>Erfahren Sie mehr

Im Leitfaden zur Azure-Anwendungsarchitektur finden Sie weitere Informationen zur [Wiederherstellung nach einer regionsweiten Dienstunterbrechung](/azure/architecture/resiliency/recovery-loss-azure-region).
