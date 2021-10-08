---
title: Verfügbarkeitszonen in Azure Monitor
description: Verfügbarkeitszonen in Azure Monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/18/2021
ms.custom: references_regions
ms.openlocfilehash: 6e10ace3ca0932cf5803719429a68b89a4118dfa
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/26/2021
ms.locfileid: "129059319"
---
# <a name="availability-zones-in-azure-monitor"></a>Verfügbarkeitszonen in Azure Monitor

[Azure-Verfügbarkeitszonen](../../availability-zones/az-overview.md) schützen Ihre Anwendungen und Daten vor Rechenzentrumsausfällen und können Resilienz für Azure Monitor-Features wie Application Insights und andere Features bieten, die einen Log Analytics-Arbeitsbereich erfordern. Wenn ein Arbeitsbereich mit einer Verfügbarkeitszone verknüpft ist, bleibt er aktiv und betriebsbereit, auch wenn ein bestimmtes Rechenzentrum fehlerhaft oder vollständig ausgeschaltet ist, indem die Verfügbarkeit anderer Zonen in der Region genutzt wird. Sie müssen nichts unternehmen und müssen auch nicht über den Incident Bescheid wissen, um zu einer alternativen Zone zu wechseln. 


## <a name="regions"></a>Regions
Weitere Informationen zu Azure-Regionen mit Verfügbarkeitszonen finden Sie unter [Regionen und Verfügbarkeitszonen in Azure](https://azure.microsoft.com/global-infrastructure/geographies/#geographies). Azure Monitor unterstützt derzeit die folgenden Regionen: 

- USA (Ost) 2
- USA, Westen 2

## <a name="dedicated-clusters"></a>Dedicated-Cluster
Die Azure Monitor-Unterstützung für Verfügbarkeitszonen erfordert einen Log Analytics-Arbeitsbereich, der mit einem [Azure Monitor-dedizierten Cluster](logs-dedicated-clusters.md) verknüpft ist. Dedizierte Cluster sind eine Bereitstellungsoption, die erweiterte Funktionen für Azure Monitor-Protokolle einschl. Verfügbarkeitszonen bietet.

Nicht alle dedizierten Cluster können Verfügbarkeitszonen verwenden. Dedizierte Cluster, die nach Mitte Oktober 2020 erstellt wurden, können so festgelegt werden, dass sie Verfügbarkeitszonen unterstützen, wenn sie erstellt werden. Neue Cluster, die nach diesem Datum erstellt wurden, werden standardmäßig für Verfügbarkeitszonen in Regionen aktiviert, in denen Azure Monitor sie unterstützt.


> [!NOTE]
> Application Insights-Ressourcen können Verfügbarkeitszonen nur verwenden, wenn sie arbeitsbereichsbasiert sind und der Arbeitsbereich einen dedizierten Cluster verwendet. Klassische Application Insights-Ressourcen können keine Verfügbarkeitszonen verwenden.


## <a name="determine-current-cluster-for-your-workspace"></a>Ermitteln des aktuellen Clusters für Ihren Arbeitsbereich
Um für Ihren Arbeitsbereich den aktuellen Status der Arbeitsbereichsverknüpfung zu ermitteln, verwenden Sie [CLI, PowerShell oder REST](logs-dedicated-clusters.md#check-workspace-link-status), um die [Clusterdetails](logs-dedicated-clusters.md#check-cluster-provisioning-status) abzurufen. Wenn der Cluster eine Verfügbarkeitszone verwendet, verfügt er über eine Eigenschaft namens `isAvailabilityZonesEnabled` mit einem Wert `true`. Nachdem ein Cluster erstellt wurde, kann diese Eigenschaft nicht mehr geändert werden.

## <a name="create-dedicated-cluster-with-availability-zone"></a>Erstellen eines dedizierten Clusters mit Verfügbarkeitszone
Verschieben Sie Ihren Arbeitsbereich in eine Verfügbarkeitszone, indem [Sie einen neuen dedizierten Cluster](logs-dedicated-clusters.md#create-a-dedicated-cluster) in einer Region erstellen, die Verfügbarkeitszonen unterstützt. Der Cluster wird automatisch für Verfügbarkeitszonen aktiviert. [Verknüpfen Sie dann Ihren Arbeitsbereich mit dem neuen Cluster](logs-dedicated-clusters.md#link-a-workspace-to-a-cluster).

> [!IMPORTANT]
> Die Verfügbarkeitszone wird zum Zeitpunkt der Erstellung im Cluster definiert und kann nicht geändert werden.

Der Übergang zu einem neuen Cluster kann ein schrittweiser Prozess sein. Entfernen Sie den vorherigen Cluster erst, nachdem alle Daten von ihm gelöscht wurden. Wenn die Datenaufbewahrung in Ihrem Arbeitsbereich beispielsweise auf 60 Tage festgelegt ist, sollten Sie den alten Cluster für diesen Zeitraum weiter laufen lassen, bevor Sie ihn entfernen.

Abfragen für Ihren Arbeitsbereich fragen jeweils beide Cluster ab, um ein einzelnes, einheitliches Resultset bereitstellen zu können. Dies bedeutet, dass alle Azure Monitor Features, die auf dem Arbeitsbereich basieren (z. B. Arbeitsmappen und Dashboards), weiterhin das vollständige, einheitliche Resultset basierend auf Daten aus beiden Clustern enthalten.

## <a name="billing"></a>Abrechnung
[Für die Verwendung eines dedizierten Clusters](logs-dedicated-clusters.md#create-a-dedicated-cluster) entstehen Kosten. Hierfür ist eine tägliche Kapazitätsreservierung von 500 GB erforderlich. 

Wenn Sie bereits über einen dedizierten Cluster verfügen und diesen für den Zugriff auf seine Daten behalten möchten, werden Ihnen beide dedizierte Cluster in Rechnung gestellt. Seit 4. August 2021 ist die mindestens erforderliche Kapazitätsreservierung für dedizierte Cluster von 1000 GB auf 500 GB pro Tag reduziert. Daher empfiehlt es sich, diesen Mindestwert auf Ihren alten Cluster anzuwenden, um Gebühren zu senken.

Der neue Cluster wird am ersten Tag nicht in Rechnung gestellt, um eine doppelte Abrechnung während der Konfiguration zu vermeiden. Nur die Daten, die vor Abschluss der Migration erfasst wurden, werden weiterhin am Migrationsdatum in Rechnung gestellt. 


## <a name="next-steps"></a>Nächste Schritte

- Unter [Verwenden von Abfragen in Azure Monitor Log Analytics](queries.md) erfahren Sie, wie die Benutzer mit Abfragepaketen in Log Analytics interagieren.

