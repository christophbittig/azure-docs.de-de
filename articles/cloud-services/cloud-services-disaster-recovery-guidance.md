---
title: Behandeln einer Azure-Dienstunterbrechung mit Auswirkungen auf Azure Cloud Services (klassisch)
description: Erfahren Sie, wie Sie im Fall einer Azure-Dienstunterbrechung mit Auswirkungen auf Azure-Clouddienste vorgehen.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
author: hirenshah1
ms.author: hirshah
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 05e415b404783b9eefcb9486efd1f384c40e11d8
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122822373"
---
# <a name="what-to-do-in-the-event-of-an-azure-service-disruption-that-impacts-azure-cloud-services-classic"></a>Vorgehensweise im Falle einer Azure-Dienstunterbrechung mit Auswirkungen auf Azure Cloud Services (klassisch)

[!INCLUDE [Cloud Services (classic) deprecation announcement](includes/deprecation-announcement.md)]

Bei Microsoft setzen wir uns mit großem Engagement dafür ein, dass unsere Dienste immer verfügbar sind, wenn Sie sie benötigen. Aufgrund von höherer Gewalt können jedoch gelegentlich ungeplante Dienstausfälle auftreten.

Microsoft stellt für seine Dienste Vereinbarungen zum Servicelevel (Service Level Agreements, SLAs) bereit, um dem Engagement für Verfügbarkeit und Konnektivität Nachdruck zu verleihen. Die SLAs für einzelne Azure-Dienste finden Sie unter [Vereinbarungen zum Servicelevel (SLAs)](https://azure.microsoft.com/support/legal/sla/).

Azure weist bereits viele integrierte Plattformfunktionen auf, die hoch verfügbare Anwendungen unterstützen. Weitere Informationen zu diesen Diensten finden Sie unter [Notfallwiederherstellung und Hochverfügbarkeit für in Microsoft Azure erstellte Anwendungen](/azure/architecture/framework/resiliency/backup-and-recovery).

Dieser Artikel behandelt ein echtes Szenario der Notfallwiederherstellung für den Fall, dass eine ganze Region aufgrund einer Naturkatastrophe oder einer umfangreichen Dienstunterbrechung von einem Ausfall betroffen ist. So etwas kommt zwar äußerst selten vor, dennoch müssen Sie für den Ausfall einer gesamten Region vorbereitet sein. Falls eine ganze Region von einer Dienstunterbrechung betroffen ist, sind die lokal redundanten Kopien Ihrer Daten vorübergehend nicht verfügbar. Bei aktivierter Georeplikation sind drei zusätzliche Kopien Ihrer Azure Storage-Blobs und -Tabellen in einer anderen Region gespeichert. Sollte eine gesamte Region ausfallen oder die primäre Region aufgrund einer Katastrophe nicht wiederherstellbar sein, ordnet Azure alle DNS-Einträge der georeplizierten Region zu.

> [!NOTE]
> Beachten Sie, dass Sie keine Kontrolle über diesen Prozess haben. Er tritt nur bei rechenzentrumsweiten Dienstunterbrechungen auf. Setzen Sie daher auch auf andere anwendungsspezifische Sicherungsstrategien, um eine möglichst hohe Verfügbarkeit zu erreichen. Weitere Informationen finden Sie unter [Notfallwiederherstellung und Hochverfügbarkeit für in Microsoft Azure erstellte Anwendungen](/azure/architecture/framework/resiliency/backup-and-recovery). Wenn Sie das Failover selbst beeinflussen möchten, können Sie beispielsweise [georedundanten Speicher mit Lesezugriff](../storage/common/storage-redundancy.md) verwenden, um eine schreibgeschützte Kopie Ihrer Daten in einer anderen Region zu erstellen.
>
>


## <a name="option-1-use-a-backup-deployment-through-azure-traffic-manager"></a>Option 1: Verwenden einer Sicherungsbereitstellung über Azure Traffic Manager
Als zuverlässigste Lösung für die Notfallwiederherstellung empfiehlt es sich, mehrere Bereitstellungen der Anwendung in unterschiedlichen Regionen zu verwalten und dann mithilfe von [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) den Datenverkehr zwischen den Bereitstellungen weiterzuleiten. Azure Traffic Manager stellt mehrere [Routingmethoden](../traffic-manager/traffic-manager-routing-methods.md) bereit, sodass Sie auswählen können, ob ihre Bereitstellungen über ein primäres oder Sicherungsmodell verwaltet werden oder ob der Datenverkehr zwischen den Bereitstellungen aufgeteilt wird.

![Lastenausgleich von Azure Cloud Services zwischen Regionen mit Azure Traffic Manager](./media/cloud-services-disaster-recovery-guidance/using-azure-traffic-manager.png)

Für die schnellste Reaktion auf den Ausfall einer Region ist es wichtig, dass Sie die [Endpunktüberwachung](../traffic-manager/traffic-manager-monitoring.md) von Traffic Manager konfigurieren.

## <a name="option-2-deploy-your-application-to-a-new-region"></a>Option 2: Bereitstellen der Anwendung in einer neuen Region
Durch die in der vorherigen Option beschriebene Verwaltung mehrerer aktiver Bereitstellungen fallen zusätzliche laufende Kosten an. Wenn Ihr Recovery Time Objective (RTO) die erforderliche Flexibilität bietet und Sie über den ursprünglichen Code oder das kompilierte Cloud Services-Paket verfügen, können Sie eine neue Instanz der Anwendung in einer anderen Region erstellen und die DNS-Einträge so aktualisieren, dass sie auf die neue Bereitstellung verweisen.

Ausführlichere Informationen zum Erstellen und Bereitstellen einer Clouddienstanwendung finden Sie unter [Erstellen und Bereitstellen eines Clouddiensts](cloud-services-how-to-create-deploy-portal.md).

Abhängig von Ihren Anwendungsdatenquellen müssen Sie möglicherweise die Wiederherstellungsvorgänge für Ihre Anwendungsdatenquelle überprüfen.

* Informationen zu Azure Storage-Datenquellen finden Sie unter [Azure Storage-Redundanz](../storage/common/storage-redundancy.md). Dort können Sie basierend auf dem ausgewählten Redundanzmodell für Ihre Anwendung prüfen, welche Optionen Ihnen zur Verfügung stehen.
* Informationen zu SQL-Datenbankquellen finden Sie unter [Übersicht: Geschäftskontinuität für die Cloud und Notfallwiederherstellung für Datenbanken mit SQL-Datenbank](../azure-sql/database/business-continuity-high-availability-disaster-recover-hadr-overview.md). Dort können Sie basierend auf dem ausgewählten Replikationsmodell für Ihre Anwendung prüfen, welche Optionen Ihnen zur Verfügung stehen.


## <a name="option-3-wait-for-recovery"></a>Option 3: Warten auf die Wiederherstellung
In diesem Fall ist keine weitere Aktion erforderlich. Bis zur Wiederherstellung der Region ist Ihr Dienst allerdings nicht verfügbar. Der aktuelle Dienststatus wird auf dem [Dashboard zur Azure-Dienstintegrität](https://azure.microsoft.com/status/) angezeigt.

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zur Implementierung einer Strategie für Notfallwiederherstellung und Hochverfügbarkeit finden Sie unter [Notfallwiederherstellung und Hochverfügbarkeit für in Microsoft Azure erstellte Anwendungen](/azure/architecture/framework/resiliency/backup-and-recovery).

Im [technischen Leitfaden zur Resilienz in Azure](/azure/architecture/checklist/resiliency-per-service)können Sie detaillierte technische Kenntnisse hinsichtlich der Funktionen einer Cloudplattform erwerben.