---
title: Georedundante Notfallwiederherstellung in Azure Event Grid | Microsoft-Dokumentation
description: Dieser Artikel beschreibt, wie Azure Event Grid die georedundante Notfallwiederherstellung automatisch unterstützt.
ms.topic: conceptual
ms.date: 08/24/2021
ms.openlocfilehash: 47899ba8fe4dd114d1fc3c0fb729589abf3ebd92
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122829973"
---
# <a name="server-side-geo-disaster-recovery-in-azure-event-grid"></a>Serverseitige georedundante Notfallwiederherstellung in Azure Event Grid
Event Grid unterstützt die automatische georedundante Notfallwiederherstellung von Metadaten für Themen, Domänen und Ereignisabonnements. Event Grid synchronisiert Ihre ereignisbezogene Infrastruktur automatisch mit einer gekoppelten Region. Wenn eine gesamte Azure-Region ausfällt, werden die Ereignisse ohne Ihr Eingreifen an die geografisch gekoppelte Region übertragen. 

Beachten Sie, dass Ereignisdaten nicht in die gekoppelte Region repliziert werden. Es werden nur die Metadaten repliziert. Wenn eine Region Verfügbarkeitszonen unterstützt, werden die Ereignisdaten jedoch über Verfügbarkeitszonen hinweg repliziert. 

Die Notfallwiederherstellung wird anhand von zwei Metriken gemessen:

- Recovery Point Objective (RPO): Der Zeitraum in Minuten oder Stunden, innerhalb dessen Daten verloren gehen können.
- Recovery Time Objective (RTO): Der Zeitraum in Minuten oder Stunden, innerhalb dessen der Dienst ausfallen kann.

Beim automatischen Failover von Event Grid gelten verschiedene RPOs und RTOs für Ihre Metadaten (Themen, Domänen und Ereignisabonnements) und für Ihre Daten (Ereignisse). Wenn Sie andere Werte als die unten angegebenen benötigen, können Sie auch weiterhin ein eigenes [clientseitiges Failover mithilfe von APIs für die Themenintegrität](custom-disaster-recovery.md) implementieren.

## <a name="recovery-point-objective-rpo"></a>Recovery Point Objective (RPO)
- **Metadaten-RPO**: Null Minuten. Wann immer eine Ressource in Event Grid erstellt wird, erfolgt eine sofortige Replikation über verschiedene Regionen hinweg. Bei einem Failover gehen keine Metadaten verloren.
- **Daten-RPO**: Wenn Ihr System fehlerfrei funktioniert und den bestehenden Datenverkehr zum Zeitpunkt eines Regionsfailover erfasst hat, beträgt das RPO für Ereignisse etwa 5 Minuten.

## <a name="recovery-time-objective-rto"></a>Recovery Time Objective (RTO)
- **Metadaten-RTO**: Event Grid beginnt innerhalb von 60 Minuten damit, Aufrufe zum Erstellen/Aktualisieren/Löschen von Themen und Abonnements zu akzeptieren. In der Regel startet dieser Vorgang deutlich schneller.
- **Daten-RTO**: Nach einem regionalen Failover beginnt Event Grid innerhalb von 60 Minuten damit, Datenverkehr zu akzeptieren. Wie bei Metadaten startet auch dieser Vorgang in der Regel deutlich schneller.

> [!IMPORTANT]
> - Es gibt keine Vereinbarung zum Servicelevel (SLA) für die serverseitige Notfallwiederherstellung. Wenn die gekoppelte Region über keine Extrakapazität zur Verarbeitung des zusätzlichen Datenverkehrs verfügt, kann Event Grid das Failover nicht einleiten. Servicelevelziele werden bestmöglich erfüllt. 
> - Für die georedundante Notfallwiederherstellung von Metadaten in Event Grid fallen keine Kosten an.


## <a name="next-steps"></a>Nächste Schritte
Wenn Sie selbst eine clientseitige Failoverlogik implementieren möchten, finden Sie Informationen dazu unter [Erstellen einer eigenen Notfallwiederherstellung für benutzerdefinierte Themen in Event Grid](custom-disaster-recovery.md).
