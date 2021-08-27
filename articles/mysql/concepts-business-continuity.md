---
title: Geschäftskontinuität – Azure Database for MySQL
description: Erfahren Sie mehr über Geschäftskontinuität (Point-in Time-Wiederherstellung, Rechenzentrumsausfälle, Geowiederherstellung), wenn Sie den Dienst Azure Database for MySQL verwenden.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 7/7/2020
ms.openlocfilehash: a02457c177e509280a93d7e6939143da3f7e9a84
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/30/2021
ms.locfileid: "122639772"
---
# <a name="overview-of-business-continuity-with-azure-database-for-mysql---single-server"></a>Übersicht über die Geschäftskontinuität mit Azure Database for MySQL – Single Server

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

Dieser Artikel beschreibt die Funktionen, die Azure Database for MySQL für Geschäftskontinuität und Notfallwiederherstellung bereitstellt. Erfahren Sie etwas über Optionen für die Wiederherstellung nach Störungen, die Datenverluste nach sich ziehen oder dazu führen können, dass Ihre Datenbank und Ihre Anwendungen nicht mehr verfügbar sind. Erfahren Sie, was zu tun ist, wenn ein Benutzer- oder Anwendungsfehler die Datenintegrität gefährdet, eine Azure-Region ausfällt oder Wartungsaufgaben für Ihre Anwendung ausgeführt werden müssen.

## <a name="features-that-you-can-use-to-provide-business-continuity"></a>Features zum Sicherstellen der Geschäftskontinuität

Wenn Sie Ihren Plan für die Geschäftskontinuität entwickeln, müssen Sie ermitteln, wie viel Zeit maximal vergehen darf, bis die Anwendung nach einer Störung vollständig wiederhergestellt ist – diese Zeitspanne ist Ihre RTO (Recovery Time Objective). Sie müssen auch herausfinden, wie viele kürzlich durchgeführte Datenupdates (in einem bestimmten Zeitraum) verloren gehen dürfen, wenn die Anwendung nach einer Störung wiederhergestellt wird – diese Zeitspanne ist Ihre RPO (Recovery Point Objective).

Azure Database for MySQL Single Server bietet Features für Geschäftskontinuität und Notfallwiederherstellung, die georedundante Sicherungen mit der Möglichkeit zum Initiieren der Geowiederherstellung und zum Bereitstellen von Lesereplikaten in einer anderen Region umfassen. Jedes Feature weist unterschiedliche Eigenschaften für die Wiederherstellungszeit und mögliche Datenverluste auf. Beim Feature für die [Geowiederherstellung](concepts-backup.md) wird ein neuer Server mithilfe der Sicherungsdaten erstellt, die aus einer anderen Region repliziert werden. Die Gesamtzeit der Wiederherstellung hängt von der Größe der Datenbank und der Anzahl der wiederherzustellenden Protokolle ab. Die Gesamtzeit zum Einrichten des Servers variiert von wenigen Minuten bis zu einigen Stunden. Mit [Lesereplikaten](concepts-read-replicas.md) werden Transaktionsprotokolle vom primären Standort asynchron an das Replikat gestreamt. Beim Ausfall einer primären Datenbank aufgrund eines Fehlers auf Zonen- oder Regionsebene bietet ein Failover auf das Replikat eine kürzere RTO und reduziert Datenverluste.

> [!NOTE]
> Die Verzögerung zwischen dem primären Server und dem Replikat hängt von der Latenz zwischen den Standorten, der Menge der zu übertragenden Daten und vor allem von der Schreibworkload des primären Servers ab. Schreibintensive Workloads können erhebliche Verzögerungen verursachen. 
>
> Da die Replikation von Lesereplikaten asynchron abläuft, sollten diese **nicht** für Lösungen mit Hochverfügbarkeit eingeplant werden, da die höheren Verzögerungen auch höhere RTOs und RPOs bedeuten können. Nur bei Workloads mit einer geringeren Verzögerung zu Zeiten mit und ohne Spitzenwerte können Lesereplikate als Alternative für Hochverfügbarkeit fungieren. Davon abgesehen sind Lesereplikate nur für Szenarien mit echter Leseskalierung für leseintensive Workloads und für die Notfallwiederherstellung vorgesehen.

In der folgenden Tabelle werden RTO und RPO in einem Szenario mit einer **typischen Workload** verglichen:

| **Funktion** | **Grundlegend** | **Allgemeiner Zweck** | **Arbeitsspeicheroptimiert** |
| :------------: | :-------: | :-----------------: | :------------------: |
| Point-in-Time-Wiederherstellung von Sicherung | Beliebiger Wiederherstellungspunkt innerhalb der Aufbewahrungsdauer <br/> RTO: variiert <br/>RPO < 15 Min.| Beliebiger Wiederherstellungspunkt innerhalb der Aufbewahrungsdauer <br/> RTO: variiert <br/>RPO < 15 Min. | Beliebiger Wiederherstellungspunkt innerhalb der Aufbewahrungsdauer <br/> RTO: variiert <br/>RPO < 15 Min. |
| Geowiederherstellung von georeplizierten Sicherungen | Nicht unterstützt | RTO: variiert <br/>RPO < 1 Stunde | RTO: variiert <br/>RPO < 1 Stunde |
| Lesereplikate | RTO – Minuten* <br/>RPO < 5 Min.* | RTO – Minuten* <br/>RPO < 5 Min.*| RTO – Minuten* <br/>RPO < 5 Min.*|

 \* RTOs und RPOs können in einigen Fällen in Abhängigkeit von verschiedenen Faktoren wie der Latenz zwischen den Standorten, der Menge der zu übertragenden Daten und vor allem der Schreibworkload in der primären Datenbank **deutlich höher sein**.

## <a name="recover-a-server-after-a-user-or-application-error"></a>Wiederherstellen eines Servers nach einem Benutzer- oder Anwendungsfehler

Sie können die Sicherungen des Diensts verwenden, um einen Server nach verschiedenen Störungen wiederherzustellen. Es kann passieren, dass ein Benutzer versehentlich Daten, eine wichtige Tabelle oder sogar eine ganze Datenbank löscht. Es kann auch vorkommen, dass eine Anwendung aufgrund eines Anwendungsfehlers unbeabsichtigt gültige Daten mit ungültigen Daten überschreibt usw.

Sie können eine Point-in-Time-Wiederherstellung ausführen, um eine Kopie Ihres Servers zu einem als fehlerfrei bekannten Zeitpunkt zu erstellen. Dieser Zeitpunkt muss innerhalb der Aufbewahrungszeit für Sicherungen liegen, die Sie für den Server konfiguriert haben. Nach der Wiederherstellung der Daten auf dem neuen Server können Sie entweder den ursprünglichen Server durch den wiederhergestellten Server ersetzen oder die benötigten Daten vom wiederhergestellten Server auf den ursprünglichen Server kopieren.

> [!IMPORTANT]
> Gelöschte Server können nur innerhalb von **fünf Tagen** nach dem Löschen wiederhergestellt werden. Danach werden die Sicherungen gelöscht. Auf die Datenbanksicherung kann nur über das Azure-Abonnement zugegriffen werden, unter dem der Server gehostet wird. Und nur über dieses Abonnement kann die Datenbanksicherung auch wiederhergestellt werden. Informationen zum Wiederherstellen eines gelöschten Servers finden Sie in den [dokumentierten Schritten](howto-restore-dropped-server.md). Um Serverressourcen nach der Bereitstellung vor versehentlichem Löschen oder unerwarteten Änderungen zu schützen, können Administratoren [Verwaltungssperren](../azure-resource-manager/management/lock-resources.md) nutzen.

## <a name="recover-from-an-azure-regional-data-center-outage"></a>Wiederherstellen nach einem Ausfall des regionalen Azure-Rechenzentrums

Es kommt zwar sehr selten vor, aber es ist möglich, dass ein Azure-Rechenzentrum ausfällt. Ein Ausfall kann den Geschäftsbetrieb einige wenige Minuten oder auch mehrere Stunden unterbrechen.

Eine Möglichkeit ist, einfach zu warten, bis der Server wieder online ist, wenn der Rechenzentrumsausfall behoben wurde. Dies funktioniert bei Anwendungen, bei denen der Server für eine längere Zeit offline sein kann, z.B. in einer Entwicklungsumgebung. Wenn ein Rechenzentrum ausfällt, wissen Sie nicht, wie lange der Ausfall dauern kann, daher ist diese Option nur dann in Erwägung zu ziehen, wenn Sie Ihren Server eine Zeit lang nicht benötigen.

## <a name="geo-restore"></a>Geowiederherstellung

Das Geowiederherstellungsfeature stellt den Server mithilfe georedundanter Sicherungen wieder her. Die Sicherungen werden in der [gekoppelten Region](../best-practices-availability-paired-regions.md) Ihres Servers gehostet. Auf diese Sicherungen kann selbst dann zugegriffen werden, wenn die Region, in der Ihr Server gehostet wird, offline ist. Sie können eine Wiederherstellung aus diesen Sicherungen in eine andere Region ausführen und den Server wieder online schalten. Weitere Informationen zur Geowiederherstellung finden Sie im [Konzeptartikel zur Sicherung und Wiederherstellung](concepts-backup.md).

> [!IMPORTANT]
> Die Geowiederherstellung ist nur möglich, wenn Sie den Server mit einem georedundanten Sicherungsspeicher bereitgestellt haben. Wenn Sie für einen vorhandenen Server von lokal redundanten zu georedundanten Sicherungen wechseln möchten, müssen Sie mit „mysqldump“ ein Speicherabbild Ihres vorhandenen Servers erstellen und dieses auf einem neu erstellten Server wiederherstellen, der mit georedundanten Sicherungen konfiguriert ist.

## <a name="cross-region-read-replicas"></a>Regionsübergreifende Lesereplikate

Mithilfe regionsübergreifender Lesereplikate können Sie Ihre BCDR-Planung (Business Continuity & Disaster Recovery) verbessern. Lesereplikate werden mithilfe der binären Protokollreplikation von MySQL asynchron aktualisiert. Weitere Informationen zu Lesereplikaten, zu verfügbaren Regionen und zum Failover finden Sie im [Konzeptartikel zu Lesereplikaten](concepts-read-replicas.md). 

## <a name="faq"></a>Häufig gestellte Fragen

### <a name="where-does-azure-database-for-mysql-store-customer-data"></a>Wo speichert Azure Database for MySQL Kundendaten?
Standardmäßig speichert Azure Database for MySQL Kundendaten in der Region, in der sie bereitgestellt werden, und verschiebt sie nicht aus dieser Region. Kunden können jedoch optional [georedundante Sicherungen](concepts-backup.md#backup-redundancy-options) aktivieren oder [regionsübergreifende Lesereplikate](concepts-read-replicas.md#cross-region-replication) zum Speichern von Daten in einer anderen Region erstellen.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu [automatisierten Sicherungen in Azure Database for MySQL](concepts-backup.md).
- Erfahren Sie, wie Sie eine Wiederherstellung mithilfe des [Azure-Portals](howto-restore-server-portal.md) oder der [Azure CLI](howto-restore-server-cli.md) ausführen.
- Weitere Informationen zu [Lesereplikaten in Azure Database for MySQL](concepts-read-replicas.md).
