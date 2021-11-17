---
title: Regionsübergreifende Replikation in Azure
description: Erfahren Sie mehr über die regionsübergreifende Replikation in Azure.
author: prsandhu
ms.service: azure
ms.topic: conceptual
ms.date: 10/21/2021
ms.author: rarco
ms.reviewer: cynthn
ms.custom: references_regions
ms.openlocfilehash: aa074d5f745f9a427fe3f4c197d0cd09de660a84
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132325598"
---
# <a name="cross-region-replication-in-azure-business-continuity-and-disaster-recovery"></a>Regionsübergreifende Replikation in Azure: Business Continuity & Disaster Recovery

Viele Unternehmen benötigen sowohl eine Hochverfügbarkeit, die durch Verfügbarkeitszonen sichergestellt wird, als auch einen Schutz vor größeren Katastrophen und regionalen Notfällen. Wie in der [Übersicht](overview.md) zur Resilienz von Regionen und Verfügbarkeitszonen beschrieben, sind Azure-Regionen so konzipiert, dass sie Schutz vor lokalen Katastrophen durch Verfügbarkeitszonen bieten. Sie können aber auch Schutz vor regionalen oder größeren Katastrophen durch Notfallwiederherstellung bieten, indem sie eine andere Region verwenden, die eine *regionsübergreifende Replikation* verwendet.

## <a name="cross-region-replication"></a>Regionsübergreifende Replikation

Um sicherzustellen, dass Kunden auf der ganzen Welt unterstützt werden, unterhält Azure mehrere geografische Regionen. Diese separaten Abgrenzungen definieren Grenzen für Notfallwiederherstellung und Datenresidenz in einer oder mehreren Azure-Regionen. 

Die regionsübergreifende Replikation ist eine von mehreren wichtigen Säulen der Azure-Strategie für Business Continuity & Disaster Recovery (Geschäftskontinuität und Notfallwiederherstellung). Die regionsübergreifende Replikation beruht auf der synchronen Replikation Ihrer vorhandenen Anwendungen und Daten, die mithilfe von Verfügbarkeitszonen innerhalb Ihrer primären Azure-Region für Hochverfügbarkeit sorgt. Bei der regionsübergreifenden Replikation werden die gleichen Anwendungen und Daten für den Schutz der Notfallwiederherstellung asynchron in anderen Azure-Regionen repliziert. 

![Abbildung: Hochverfügbarkeit durch asynchrone Replikation von Anwendungen und Daten in anderen Azure-Regionen für den Schutz der Notfallwiederherstellung.](./media/cross-region-replication.png)

Einige Azure-Dienste nutzen die Vorteile der regionsübergreifenden Replikation, um die Geschäftskontinuität sicherzustellen und vor Datenverlust zu schützen. Azure bietet mehrere [Speicherlösungen](../storage/common/storage-redundancy.md#redundancy-in-a-secondary-region), die die regionsübergreifende Replikation zur Sicherstellung der Datenverfügbarkeit verwenden. Beispielsweise repliziert der [georedundante Azure-Speicher](../storage/common/storage-redundancy.md#geo-redundant-storage) (GRS) Daten automatisch in eine sekundäre Region. Dieser Ansatz stellt sicher, dass Daten auch dann dauerhaft verfügbar sind, wenn die primäre Region nicht wiederherstellbar ist.

Nicht alle Azure-Dienste replizieren automatisch Daten oder greifen automatisch auf eine ausgefallene Region zurück, um eine regionsübergreifende Replikation in eine andere aktivierte Region durchzuführen. In diesen Szenarien müssen Wiederherstellung und Replikation vom Kunden konfiguriert werden. Diese Beispiele sind Abbildungen des *Modells der gemeinsamen Verantwortung*. Es ist ein grundlegender Pfeiler in Ihrer Strategie für die Notfallwiederherstellung. Weitere Informationen zum Modell der gemeinsamen Verantwortung und zur Business Continuity & Disaster Recovery (Geschäftskontinuität und Notfallwiederherstellung) in Azure finden Sie unter [Verwaltung der Geschäftskontinuität in Azure](business-continuity-management-program.md).

Die gemeinsame Verantwortung wird zum Kernpunkt Ihrer strategischen Entscheidungsfindung, wenn es um die Notfallwiederherstellung geht. Azure erfordert nicht, dass Sie die regionsübergreifende Replikation verwenden, und Sie können Dienste verwenden, um Resilienz ohne regionsübergreifende Replikation in eine andere aktivierte Region zu ermöglichen. Wir empfehlen jedoch dringend, dass Sie Ihre wichtigen Dienste regionsübergreifend konfigurieren, um von der [Isolation](../security/fundamentals/isolation-choices.md) zu profitieren und die [Verfügbarkeit](overview.md) zu verbessern. 

Für Anwendungen, die mehrere aktive Regionen unterstützen, empfehlen wir, dass Sie verfügbare mehrfach aktivierte Regionen verwenden. Diese Vorgehensweise stellt eine optimale Verfügbarkeit für Anwendungen und eine minimierte Wiederherstellungszeit sicher, wenn ein Ereignis die Verfügbarkeit beeinträchtigt. Entwerfen Sie Ihre Anwendung nach Möglichkeit für [maximale Resilienz](/azure/architecture/framework/resiliency/overview) und einfache [Wiederherstellung im Notfall](/azure/architecture/framework/resiliency/backup-and-recovery).

## <a name="benefits-of-cross-region-replication"></a>Vorteile der regionsübergreifenden Replikation

Die Architektur der regionsübergreifenden Replikation für Ihre Dienste und Daten kann auf Dienstbasis entschieden werden. Sie verwenden notwendigerweise einen Kosten-Nutzen-Analyseansatz, der auf den strategischen und geschäftlichen Anforderungen Ihres Unternehmens basiert. Die primären und sekundären Vorteile der Replikation von Kostenregionen sind komplex und umfangreich und verdienen eine ausführliche Darstellung. Zu diesen Vorteilen zählen:

- **Reihenfolge der Regionswiederherstellung**: Bei einem geografischen Ausfall wird die Wiederherstellung einer Region aus jeder aktivierten Regionsgruppe priorisiert. Bei Anwendungen, die über aktivierte Regionsgruppen bereitgestellt werden, wird garantiert, dass eine der Regionen für die Wiederherstellung priorisiert wird. Wenn eine Anwendung regionsübergreifend bereitgestellt wird, von denen eine nicht für die regionsübergreifende Replikation aktiviert ist, kann die Wiederherstellung verzögert werden.
- **Sequenzielle Aktualisierung**: Geplante Azure-Systemupdates für Ihre aktivierten Regionen werden chronologisch gestaffelt, um Downtime, Auswirkungen von Fehlern und logischen Fehlern im seltenen Fall eines fehlerhaften Updates zu minimieren.
- **Physische Isolation**: Azure versucht, eine Mindestentfernung von 300 Meilen (483 Kilometer) zwischen Rechenzentren in aktivierten Regionen sicherzustellen, obwohl dies nicht über alle geografischen Regionen hinweg möglich ist. Die Trennung der Rechenzentren verringert die Wahrscheinlichkeit, dass Naturkatastrophen, politische Unruhen, Stromausfälle oder physische Netzwerkausfälle mehrere Regionen betreffen können. Die Isolation unterliegt den Einschränkungen innerhalb eines Gebiets, z. B. der Größe des Gebiets, der Verfügbarkeit von Energieversorgungs- oder Netzwerkinfrastrukturen und Vorschriften.
- **Datenresidenz**: Regionen befinden sich innerhalb derselben Geografie wie ihre aktivierte Gruppe (mit Ausnahme von „Brasilien, Süden“ und „Singapur“), um die Anforderungen an die Datenresidenz für steuerliche und rechtliche Anforderungen zu erfüllen. 

## <a name="azure-cross-region-replication-pairings-for-all-geographies"></a>Regionsübergreifende Azure-Replikationspaare für alle geografischen Regionen

Die Regionen werden für die regionsübergreifende Replikation auf der Grundlage der Nähe und anderer Faktoren gekoppelt.

**Azure-Regionspaare**

| Gebiet | Regionspaar A | Regionspaar B |
| --- | --- | --- |
| Asien-Pazifik |Asien, Osten (Hongkong) | Asien, Südosten (Singapur) |
| Australien |Australien (Osten) |Australien, Südosten |
| Australien |Australien, Mitte |Australien, Mitte 2\* |
| Brasilien |Brasilien Süd |USA Süd Mitte |
| Brasilien |Brasilien, Südosten\* |Brasilien Süd |
| Canada |Kanada, Mitte |Kanada, Osten |
| China |China, Norden |China, Osten|
| China |China, Norden 2 |China, Osten 2|
| Europa |Europa, Norden (Irland) |Europa, Westen (Niederlande) |
| Frankreich |Frankreich, Mitte|Frankreich, Süden\*|
| Deutschland |Deutschland, Westen-Mitte |Deutschland, Norden\* |
| Indien |Indien, Mitte |Indien (Süden) |
| Indien |Indien, Westen |Indien (Süden) |
| Japan |Japan, Osten |Japan, Westen |
| Korea |Korea, Mitte |Korea, Süden |
| Nordamerika |East US |USA (Westen) |
| Nordamerika |USA (Ost) 2 |USA (Mitte) |
| Nordamerika |USA Nord Mitte |USA Süd Mitte |
| Nordamerika |USA, Westen 2 |USA, Westen-Mitte |
| Nordamerika |USA, Westen 3 |East US |
| Norwegen | Norwegen, Osten | Norwegen, Westen\* |
| Südafrika | Südafrika, Norden |Südafrika, Westen\* |
| Schweiz | Schweiz, Norden |Schweiz, Westen\* |
| UK |UK, Westen |UK, Süden |
| Vereinigte Arabische Emirate | Vereinigte Arabische Emirate, Norden | VAE, Mitte\* |
| US-Verteidigungsministerium |US DoD, Osten\* |US DoD, Mitte\* |
| US Government |US Gov Arizona\* |US Gov Texas\* |
| US Government |US Gov Iowa\* |US Government, Virginia\* |
| US Government |US Government, Virginia\* |US Gov Texas\* |

(\*) Bestimmte Regionen unterliegen Zugriffseinschränkungen, um bestimmte Kundenszenarien zu unterstützen, z. B. landesinterne Notfallwiederherstellung. Diese Regionen sind nur auf Anforderung verfügbar, indem Sie [eine neue Supportanfrage im Azure-Portal erstellen](https://portal.azure.com/#blade/Microsoft\_Azure\_Support/HelpAndSupportBlade/newsupportrequest).

> [!IMPORTANT]
> - „Indien, Westen“ wird nur in einer Richtung zugeordnet. Die sekundäre Region von Indien (Westen) ist Indien (Süden), aber die sekundäre Region für Indien (Süden) ist Indien (Mitte).
> - „Brasilien, Süden“ ist einzigartig, da diese Region ein Paar mit einer Region außerhalb ihrer Geografie bildet. Die sekundäre Region von „Brasilien, Süden“ ist „USA, Süden-Mitte“. Die sekundäre Region für „USA, Süden-Mitte“ ist nicht „Brasilien, Süden“.

## <a name="next-steps"></a>Nächste Schritte

- [Regionen, die Verfügbarkeitszonen in Azure unterstützen](az-region.md)
- [Schnellstartvorlagen](https://aka.ms/azqs)
