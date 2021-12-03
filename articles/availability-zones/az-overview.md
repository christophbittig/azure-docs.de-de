---
title: Azure-Regionen und -Verfügbarkeitszonen
description: Erfahren Sie mehr über Regionen und Verfügbarkeitszonen und darüber, wie Sie mit deren Hilfe echte Resilienz erzielen können.
author: prsandhu
ms.service: azure
ms.topic: conceptual
ms.date: 10/01/2021
ms.author: rarco
ms.reviewer: cynthn
ms.custom: references_regions
ms.openlocfilehash: a980cd71f86086a0a0b07354295d12251305bb4f
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132369700"
---
# <a name="regions-and-availability-zones"></a>Regionen und Verfügbarkeitszonen

Azure-Regionen und -Verfügbarkeitszonen sind so konzipiert, dass Sie damit Resilienz und Zuverlässigkeit für Ihre unternehmenskritischen Workloads erreichen können. Azure verwaltet mehrere Geografien. Diese separaten Abgrenzungen definieren Grenzen für Notfallwiederherstellung und Datenresidenz in einer oder mehreren Azure-Regionen. Durch die Verwaltung vieler Regionen wird sichergestellt, dass Kunden auf der ganzen Welt unterstützt werden.

## <a name="regions"></a>Regions

Jede Azure-Region verfügt über Rechenzentren, die in einem durch die Latenz definierten Umkreis bereitgestellt werden. Die Rechenzentren sind über ein dediziertes, regionales Netzwerk mit geringer Latenz verbunden. Durch diesen Entwurf wird sichergestellt, dass Azure-Dienste innerhalb einer Region bestmögliche Leistung und Sicherheit bieten.

## <a name="availability-zones"></a>Verfügbarkeitszonen

Azure-*Verfügbarkeitszonen* sind physisch getrennte Standorte innerhalb der einzelnen Azure-Regionen, die Fehlertoleranz bei lokalen Ausfällen bieten. Ausfälle können von Software- und Hardwareausfällen bis hin zu Ereignissen wie Erdbeben, Überflutungen und Bränden reichen. Fehlertoleranz wird durch Redundanz und logische Isolierung von Azure-Diensten erreicht. Um Resilienz sicherzustellen, liegen in allen Regionen, die Verfügbarkeitszonen unterstützen, mindestens drei separate Verfügbarkeitszonen vor.

Azure-Verfügbarkeitszonen sind über ein Hochleistungsnetzwerk mit einer Roundtriplatenz von weniger als 2 ms verbunden. Sie tragen dazu bei, dass Ihre Daten synchronisiert und zugänglich bleiben, wenn etwas schiefgeht. Jede Zone besteht aus mindestens einem Rechenzentrum, dessen Stromversorgung, Kühlung und Netzwerkinfrastruktur unabhängig funktionieren. Verfügbarkeitszonen sind so konzipiert, dass regionale Dienste, Kapazität und Hochverfügbarkeit von den verbleibenden beiden Zonen unterstützt werden, wenn eine Zone betroffen ist.

![Abbildung: Physisch getrennte Standorte von Verfügbarkeitszonen in einer Azure-Region](media/availability-zones.png)

Standorte von Rechenzentren werden anhand strenger Kriterien für die Sicherheitsrisikobewertung ausgewählt. Bei diesem Prozess werden alle signifikanten, für Rechenzentren spezifischen Risiken identifiziert und gemeinsame Risiken zwischen Verfügbarkeitszonen berücksichtigt.

Mit Verfügbarkeitszonen können Sie Anwendungen und Datenbanken entwerfen und betreiben, die automatisch unterbrechungsfrei zwischen Zonen umgestellt werden. Azure-Verfügbarkeitszonen sind hochverfügbar, fehlertolerant und skalierbarer als herkömmliche Infrastrukturen mit einzelnen oder mehreren Rechenzentren.

Mithilfe von Azure-Diensten, die Resilienz bieten, können Sie Hochverfügbarkeit in Ihre Anwendungsarchitektur integrieren. Legen Sie Ihre Compute-, Speicher-, Netzwerk- und Datenressourcen in einer Verfügbarkeitszone zusammen, und replizieren Sie diese Anordnung in anderen Verfügbarkeitszonen.

*Azure-Dienste mit Unterstützung für Verfügbarkeitszonen* bieten das richtige Maß an Resilienz und Flexibilität. Für die Konfiguration gibt es zwei Möglichkeiten. Sie können entweder *zonenredundant* mit automatischer zonenübergreifender Replikation oder *zonenbasiert* mit Instanzen sein, die an eine bestimmte Zone angeheftet werden. Sie können diese Ansätze auch kombinieren.

Einige Organisationen benötigen hochverfügbare Verfügbarkeitszonen und Schutz vor größeren Naturkatastrophen und regionalen Notfällen. Azure-Regionen sind so konzipiert, dass sie durch Verfügbarkeitszonen Schutz vor lokalen Notfällen bieten und durch die Notfallwiederherstellung Schutz vor regionalen oder großräumigeren Notfällen gewährleisten, indem eine andere Region verwendet wird. Weitere Informationen zu Geschäftskontinuität, Notfallwiederherstellung und regionsübergreifender Replikation finden Sie unter [Regionsübergreifende Replikation in Azure](../best-practices-availability-paired-regions.md).

![Abbildung: Verfügbarkeitszonen, die Schutz vor lokalen Notfällen und anhand einer anderen Region vor regionalen oder großräumigen Notfällen bieten.](media/availability-zones-region-geography.png)

## <a name="azure-regions-with-availability-zones"></a>Azure-Regionen mit Verfügbarkeitszonen

Azure ist umfassender vertreten als jeder andere Cloudanbieter, und es werden in schneller Folge weitere Regionen und Verfügbarkeitszonen eröffnet.

| Amerika | Europa | Afrika | Asien-Pazifik |
|--------------------|----------------------|---------------------|----------------|
| Brasilien Süd | Frankreich, Mitte | Südafrika, Norden | Australien (Osten) |
| Kanada, Mitte | Deutschland, Westen-Mitte | | Indien, Mitte\* |
| USA (Mitte) | Nordeuropa | | Japan, Osten |
| East US | Norwegen, Osten | | Korea, Mitte |
| USA (Ost) 2 | UK, Süden | | Asien, Südosten |
| USA Süd Mitte | Europa, Westen |  | Asien, Osten |
| US Government, Virginia | Schweden | | |
| USA, Westen 2 | | | |
| USA, Westen 3 | | | |

\* Weitere Informationen zur Unterstützung von Verfügbarkeitszonen und verfügbaren Diensten in diesen Regionen erhalten Sie von Ihrem Microsoft-Vertriebs- oder Kundenbeauftragten. Informationen zu den Regionen, die zukünftig Verfügbarkeitszonen unterstützen werden, finden Sie unter [Azure-Geografien](https://azure.microsoft.com/global-infrastructure/geographies/).

## <a name="next-steps"></a>Nächste Schritte

- [Microsoft-Engagement zum Erweitern von Azure-Verfügbarkeitszonen auf weitere Regionen](https://azure.microsoft.com/blog/our-commitment-to-expand-azure-availability-zones-to-more-regions/)
- [Azure-Dienste mit Unterstützung für Verfügbarkeitszonen](az-region.md)
- [Azure-Dienste](region-types-service-categories-azure.md)
