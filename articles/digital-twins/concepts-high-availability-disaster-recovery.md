---
title: Hochverfügbarkeit und Notfallwiederherstellung
titleSuffix: Azure Digital Twins
description: Informationen zu den Azure- und Azure Digital Twins-Features zum Erstellen von Azure IoT-Lösungen mit Hochverfügbarkeit und Notfallwiederherstellung.
author: baanders
ms.author: baanders
ms.date: 10/14/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 6307c99c0796fb4159da7563c951304ceef6ece2
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122340252"
---
# <a name="azure-digital-twins-high-availability-and-disaster-recovery"></a>Hochverfügbarkeit und Notfallwiederherstellung: Azure Digital Twins

Ein wichtiger Aspekt bei robusten IoT-Lösungen sind Geschäftskontinuität und Notfallwiederherstellung. Der Entwurf für **Hochverfügbarkeit (HA)** und **Notfallwiederherstellung (DR)** kann Ihnen beim Definieren und Erreichen angemessener Betriebszeitziele für Ihre Lösung helfen.

Dieser Artikel beschreibt die Funktionen für Hochverfügbarkeit und Notfallwiederherstellung, die der Dienst Azure Digital Twins bietet.

Azure Digital Twins unterstützt die folgenden Featureoptionen:
* *Hochverfügbarkeit zwischen Regionen*: Integrierte Redundanz, um die Betriebszeit des Diensts zu optimieren.
* *Regionsübergreifende Notfallwiederherstellung*: Failover zu einer geografisch gekoppelten Azure-Region im Falle eines unerwarteten Rechenzentrumsfehlers.

Allgemeine Azure-Anleitungen zum Entwerfen von Hochverfügbarkeit/Notfallwiederherstellung finden Sie auch im Abschnitt [Bewährte Methoden](#best-practices).

## <a name="intra-region-ha"></a>Regionale Hochverfügbarkeit
 
Azure Digital Twins bietet Hochverfügbarkeit zwischen Regionen, indem Redundanzen innerhalb des Diensts implementiert werden. Dies spiegelt sich in der [SLA des Diensts](https://azure.microsoft.com/support/legal/sla/digital-twins) für die Uptime wider. **Für die Entwickler einer Azure Digital Twins-Lösung entsteht kein zusätzlicher Aufwand, um die Vorteile dieser Hochverfügbarkeitsfunktionen zu nutzen.** Obwohl Azure Digital Twins eine relativ hohe Betriebszeitgarantie bietet, können hier wie bei jeder verteilten Verarbeitungsplattform auch vorübergehende Fehler auftreten. Entsprechende Wiederholungsrichtlinien sollten in die Komponenten integriert werden, die mit einer Cloudanwendung interagieren, um vorübergehende Fehler zu behandeln.

## <a name="cross-region-dr"></a>Regionenübergreifende Notfallwiederherstellung

Es kann in einigen seltenen Fällen dazu kommen, dass ein Rechenzentrum aufgrund von Stromausfällen oder anderen Ereignissen in der Region ausfällt. Solche Ereignisse sind selten, und bei solchen Ausfällen ist die oben beschriebene Hochverfügbarkeit zwischen Regionen nicht immer hilfreich. Azure Digital Twins löst dieses Problem durch ein von Microsoft initiiertes Failover.

Das **von Microsoft initiierte Failover** wird in seltenen Fällen angewendet, um ein Failover für alle Azure Digital Twins-Instanzen einer betroffenen Region in die entsprechende [geografisch gekoppelte Region](../best-practices-availability-paired-regions.md) auszuführen. Dieser Prozess ist eine Standardoption (Benutzer können sie also nicht abwählen), für die kein Eingriff des Benutzers erforderlich ist. Microsoft behält sich das Recht vor, zu bestimmen, wann diese Option angewendet wird. Dieser Mechanismus bedarf nicht der Zustimmung des Benutzers, bevor ein Failover für die Instanz des Benutzers ausgeführt wird.

>[!NOTE]
> Einige Azure-Dienste bieten eine zusätzliche Option namens **vom Kunden initiiertes Failover**, die es Kunden ermöglicht, ein Failover nur für Ihre Instanz zu initiieren, z. B. zum Ausführen eines Notfallwiederherstellungsdrills. Dieser Mechanismus wird zurzeit von Azure Digital Twins **nicht unterstützt**. 

Wenn es für Sie wichtig ist, alle Daten in bestimmten geografischen Regionen zu behalten, überprüfen Sie den Standort der [geografisch gekoppelten Region](../best-practices-availability-paired-regions.md#azure-regional-pairs) für die Region, in der Sie Ihre Instanz erstellen, um sicherzustellen, dass sie Ihre Anforderungen an die Datenresidenz erfüllt.

>[!NOTE]
> Einige Azure-Dienste bieten Benutzern die Möglichkeit, eine andere Region für das Failover zu konfigurieren, um Anforderungen an die Datenresidenz erfüllen zu können. Diese Funktion wird zurzeit von Azure Digital Twins **nicht unterstützt**. 

## <a name="monitor-service-health"></a>Überwachen der Dienstintegrität

Da für Azure Digital Twins-Instanzen Failovers und Wiederherstellungen durchgeführt werden, können Sie den Prozess mithilfe des Tools [Azure Service Health](../service-health/service-health-overview.md) überwachen. Service Health überwacht die Integrität Ihrer Azure-Dienste in verschiedenen Regionen und Abonnement und meldet wichtige Informationen zu Ausfällen und Downtime.

Während eines Failoverereignisses kann Service Health angeben, ob Ihr Dienst ausgefallen ist und wann er wieder verfügbar ist.

So zeigen Sie Service Health-Ereignisse an:
1. Navigieren Sie im Azure-Portal zu [Service Health](https://portal.azure.com/?feature.customportal=false#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues) (hierzu können Sie den Link hier verwenden oder mithilfe der Suchleiste im Portal suchen).
1. Verwenden Sie das linke Menü, um zur Seite *Integritätsverlauf* zu wechseln.
1. Suchen Sie nach einem *Issue Name* (Problemnamen), der mit **Azure Digital Twins** beginnt, und klicken Sie auf diesen.

    :::image type="content" source="media/concepts-high-availability-disaster-recovery/navigate.png" alt-text="Screenshot des Azure-Portals mit der Seite „Integritätsverlauf“. Ein Problem mit dem Namen „Azure Digital Twins – West Europe – Mitigated“ ist hervorgehoben." lightbox="media/concepts-high-availability-disaster-recovery/navigate.png":::

1. Allgemeine Informationen zum Ausfall finden Sie auf der Registerkarte *Zusammenfassung*.

    :::image type="content" source="media/concepts-high-availability-disaster-recovery/summary.png" alt-text="Screenshot des Azure-Portals mit der Seite „Integritätsverlauf“ mit hervorgehobener Registerkarte „Zusammenfassung“. Auf der Registerkarte werden allgemeine Informationen angezeigt." lightbox="media/concepts-high-availability-disaster-recovery/summary.png":::
1. Weitere Informationen und Updates zum Problem im Zeitverlauf finden Sie auf der Registerkarte *Problemupdates*.

    :::image type="content" source="media/concepts-high-availability-disaster-recovery/issue-updates.png" alt-text="Screenshot des Azure-Portals mit der Seite „Integritätsverlauf“ mit hervorgehobener Registerkarte „Problemupdates“. Auf der Registerkarte wird der Status von Einträgen angezeigt." lightbox="media/concepts-high-availability-disaster-recovery/issue-updates.png":::


Beachten Sie, dass die in diesem Tool angezeigten Informationen nicht für eine spezifische Azure Digital Twins-Instanz sind. Nachdem Sie Service Health verwenden haben, um die aktuelle Situation eines Azure Digital Twins-Diensts in einer bestimmten Region oder einem bestimmten Abonnement zu untersuchen, können Sie die Überwachung ausweiten, indem Sie das [Resource Health-Tool](troubleshoot-resource-health.md) verwenden, um spezifische Instanzen zu analysieren und zu sehen, ob diese betroffen sind.

## <a name="best-practices"></a>Bewährte Methoden

Bewährte Methoden für Hochverfügbarkeit/Notfallwiederherstellung finden Sie in den folgenden Azure-Anleitungen zu diesem Thema: 
* Im Artikel [Entwerfen zuverlässiger Azure-Anwendungen](/azure/architecture/framework/resiliency/app-design) finden Sie eine allgemeine Übersicht über Geschäftskontinuität und Notfallwiederherstellung. 
* Das Dokument [Notfallwiederherstellung und Hochverfügbarkeit für Azure-Anwendungen](/azure/architecture/framework/resiliency/backup-and-recovery) enthält Architekturanleitungen zu Strategien für Azure-Anwendungen in Bezug auf Notfallwiederherstellung und Hochverfügbarkeit.

## <a name="next-steps"></a>Nächste Schritte 

Erfahren Sie mehr über die ersten Schritte mit Azure Digital Twins-Lösungen:
 
* [Was ist Azure Digital Twins?](overview.md)
* [Erste Schritte mit dem Azure Digital Twins-Explorer](quickstart-azure-digital-twins-explorer.md)
