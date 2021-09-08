---
title: 'Migration einer lokalen MySQL-Instanz zu Azure Database for MySQL: Business Continuity & Disaster Recovery (BCDR)'
description: Wie bei jedem unternehmenskritischen System ist die Strategie für Sicherung und Wiederherstellung sowie Notfallwiederherstellung (BCDR) ein wichtiger Bestandteil Ihres gesamten Systementwurfs.
ms.service: mysql
ms.subservice: migration-guide
ms.topic: how-to
author: arunkumarthiags
ms.author: arthiaga
ms.reviewer: maghan
ms.custom: ''
ms.date: 06/21/2021
ms.openlocfilehash: 35ab4f952b2e8082f4923926f11698fef352c8f8
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/16/2021
ms.locfileid: "114296223"
---
# <a name="migrate-mysql-on-premises-to-azure-database-for-mysql-business-continuity-and-disaster-recovery-bcdr"></a>Migration einer lokalen MySQL-Instanz zu Azure Database for MySQL: Business Continuity & Disaster Recovery (BCDR)

[!INCLUDE[applies-to-mysql-single-flexible-server](../../includes/applies-to-mysql-single-flexible-server.md)]

## <a name="prerequisites"></a>Voraussetzungen

[Optimierung](11-optimization.md)

## <a name="back-up-and-restore"></a>Sichern und Wiederherstellen

Wie bei jedem unternehmenskritischen System ist die Strategie für Sicherung und Wiederherstellung sowie Notfallwiederherstellung (BCDR) ein wichtiger Bestandteil Ihres gesamten Systementwurfs. Wenn ein unvorhergesehenes Ereignis eintritt, sollten Sie in der Lage sein, den Stand Ihrer Daten zu einem bestimmten Zeitpunkt (Recovery Point Objective) in einem angemessenen Zeitraum (Recovery Time Objective) wiederherzustellen.

### <a name="backup"></a>Backup

Azure Database for MySQL unterstützt standardmäßig automatische Sicherungen für sieben Tage. Es kann sinnvoll sein, dies in den aktuellen Maximalwert von 35 Tagen zu ändern. Sie müssen beachten, dass bei einer Änderung des Werts in 35 Tage Gebühren für über den zugeordneten Speicher hinausgehenden zusätzlichen Sicherungsspeicher anfallen.

Es gibt mehrere aktuelle Einschränkungen der Datenbanksicherungsfunktion, wie im Dokumentationsartikel [Sicherung und Wiederherstellung in Azure Database for MySQL](../../concepts-backup.md) beschrieben. Diese müssen Sie kennen, wenn Sie entscheiden, welche zusätzlichen Strategien implementiert werden sollen.

Sie müssen z. B. Folgendes beachten:

- Kein direkter Zugriff auf die Sicherungen

- Für Ebenen, die bis zu 4 TB zulassen, wird einmal pro Woche eine vollständige Sicherung durchgeführt, zweimal täglich eine differenzielle und alle fünf Minuten Protokolle.

- Für Ebenen, die bis zu 16 TB zulassen, werden Sicherungen auf Momentaufnahmenbasis durchgeführt.

    > [!NOTE]
    > [Einige Regionen](../../concepts-pricing-tiers.md#storage) unterstützen noch keinen Speicher von bis zu 16 TB.

### <a name="restore"></a>Restore

Redundanz (lokal oder Georedundanz) muss während der Servererstellung konfiguriert werden. Eine Geowiederherstellung kann jedoch durchgeführt werden und ermöglicht die Änderung dieser Optionen während des Wiederherstellungsprozesses. Das Ausführen eines Wiederherstellungsvorgangs kann die Konnektivität vorübergehend beenden, und alle Anwendungen sind während des Wiederherstellungsvorgangs ausgeschaltet.

Während einer Datenbankwiederherstellung müssen alle unterstützenden Elemente außerhalb der Datenbank wiederhergestellt werden. Überprüfen Sie den Migrationsprozess. Weitere Informationen finden Sie unter [Durchführen der Aufgaben nach der Wiederherstellung](../../concepts-backup.md#perform-post-restore-tasks).

## <a name="read-replicas"></a>Lesereplikate

[Lesereplikate](../../concepts-read-replicas.md) können verwendet werden, um den MySQL-Lesedurchsatz zu erhöhen, die Leistung für regionale Benutzer zu verbessern und die Notfallwiederherstellung zu implementieren. Beachten Sie beim Erstellen eines oder mehrerer Lesereplikate, dass zusätzliche Gebühren für die gleiche Compute- und Speichernutzung wie für den primären Server anfallen.

## <a name="deleted-servers"></a>Gelöschte Server

Wenn ein Administrator oder ein böswilliger Benutzer den Server im Azure-Portal oder über automatisierte Methoden löscht, werden alle Sicherungen und Lesereplikate gelöscht. Es ist wichtig, dass [Ressourcensperren](../../../azure-resource-manager/management/lock-resources.md) für die Azure Database for MySQL-Ressourcengruppe erstellt werden, um den Instanzen eine zusätzliche Löschschutzebene hinzuzufügen.

## <a name="regional-failure"></a>Regionaler Ausfall

Wenn ein seltener regionalen Ausfall eintritt, können georedundante Sicherungen oder ein Lesereplikat verwendet werden, um die Datenworkloads wieder auszuführen. Sowohl die Georeplikation als auch ein Lesereplikat sollten zur Verfügung stehen, um den besten Schutz vor unerwarteten regionalen Ausfällen zu bieten.

> [!NOTE]
> Mit dem Ändern der Datenbankserverregion ändert sich auch der Endpunkt, sodass die Anwendungskonfigurationen entsprechend aktualisiert werden müssen.

### <a name="load-balancers"></a>Load Balancer

Wenn die Anwendung aus vielen verschiedenen Instanzen auf der ganzen Welt besteht, ist es möglicherweise nicht möglich, alle Clients zu aktualisieren. Verwenden Sie einen [Azure Load Balancer](../../../load-balancer/load-balancer-overview.md) oder ein [Application Gateway](../../../application-gateway/overview.md), um eine nahtlose Failoverfunktion zu implementieren. Obwohl sie hilfreich und zeitsparend sind, sind diese Tools für regionale Failoverfunktionen nicht erforderlich.

## <a name="wwi-scenario"></a>WWI-Szenario

WWI wollte die Failoverfunktionen von Lesereplikaten testen, sodass sie die unten beschriebenen Schritte ausgeführt haben.

### <a name="creating-a-read-replica"></a>Erstellen eines Lesereplikats

- Öffnen Sie das Azure-Portal.

- Navigieren Sie zur Azure Database for MySQL-Instanz.

- Wählen Sie unter **Einstellungen** die Option **Replikation** aus.

- Wählen Sie **Replikat hinzufügen**.

- Geben Sie einen Servernamen ein.

- Wählen Sie die Region aus.

- Wählen Sie **OK** aus, und warten Sie, bis die Instanz bereitgestellt wurde. Je nach Größe der Hauptinstanz kann die Replikation einige Zeit dauern.

    > [!NOTE]
    > Für jedes Replikat fallen zusätzliche Gebühren an, die denen für die Hauptinstanz entsprechen.

### <a name="fail-over-to-read-replica"></a>Failover zum Lesereplikat

Sobald ein Lesereplikat erstellt und der Replikationsprozess abgeschlossen ist, kann es für ein Failover verwendet werden. Die Replikation wird während eines Failovers beendet und macht das Lesereplikat zu einer eigenen Hauptinstanz.

Failoverschritte:

- Öffnen Sie das Azure-Portal.

- Navigieren Sie zur Azure Database for MySQL-Instanz.

- Wählen Sie unter **Einstellungen** die Option **Replikation** aus.

- Wählen Sie eines der Lesereplikate aus.

- Wählen Sie **Replikation beenden** aus. Dadurch wird das Lesereplikat unterbrochen.

- Ändern Sie alle Anwendungsverbindungszeichenfolgen so, dass sie auf die neue Hauptinstanz verweisen.

## <a name="bcdr-checklist"></a>BCDR-Prüfliste

- Ändern Sie die Sicherungshäufigkeit, um die Anforderungen zu erfüllen.

- Richten Sie Lesereplikate für leseintensive Workloads und regionales Failover ein.

- Erstellen Sie Ressourcensperren für Ressourcengruppen.

- Implementieren Sie eine Lastenausgleichsstrategie für Anwendungen für schnelles Failover.  


## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Security](./13-security.md)