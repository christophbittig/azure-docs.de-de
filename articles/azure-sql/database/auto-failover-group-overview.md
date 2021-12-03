---
title: Autofailover-Gruppen
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Mit Autofailover-Gruppen können Sie die Georeplikation und das automatische/koordinierte Failover einer Gruppe von Datenbanken auf einem Server oder aller Datenbanken in einer verwalteten Instanz verwalten.
services: sql-database
ms.service: sql-db-mi
ms.subservice: high-availability
ms.custom: sqldbrb=2
ms.topic: conceptual
author: emlisa
ms.author: emlisa
ms.reviewer: mathoma
ms.date: 10/25/2021
ms.openlocfilehash: 2d4aa937a29170ff1ebabc9b1e0bbe316b526189
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2021
ms.locfileid: "131894125"
---
# <a name="use-auto-failover-groups-to-enable-transparent-and-coordinated-geo-failover-of-multiple-databases"></a>Verwenden von Autofailover-Gruppen für ein transparentes und koordiniertes Geofailover mehrerer Datenbanken
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Mit dem Feature „Autofailover-Gruppen“ können Sie die Replikation und das Failover einer Gruppe von Datenbanken auf einem Server oder aller Datenbanken in einer verwalteten Instanz in eine andere Region verwalten. Hierbei handelt es sich um eine deklarative Abstraktion, die auf dem bereits vorhandenen Feature [Aktive Georeplikation](active-geo-replication-overview.md) basiert und dazu dient, die Bereitstellung und Verwaltung georeplizierter Datenbanken zu vereinfachen. Sie können ein Geofailover manuell einleiten oder basierend auf einer benutzerdefinierten Richtlinie an den Azure-Dienst delegieren. Letzteres gibt Ihnen die Möglichkeit, nach schwerwiegenden Ausfällen oder anderen ungeplanten Ereignissen, die zum vollständigen oder teilweisen Verlust der Verfügbarkeit der SQL-Datenbank-Instanz oder SQL Managed Instance in der primären Region führen, automatisch mehrere verwandte Datenbanken in einer sekundären Region wiederherzustellen. Eine Failovergruppe kann eine oder mehrere Datenbanken enthalten, die in der Regel von der gleichen Anwendung verwendet werden. Außerdem können sie die lesbaren sekundären Datenbanken zur Auslagerung schreibgeschützter Abfrageworkloads verwenden.

> [!NOTE]
> Gruppen für automatisches Failover unterstützen die Georeplikation aller Datenbanken in der Gruppe auf nur einen sekundären Server oder eine sekundäre Instanz in einer anderen Region. Wenn Sie mehrere sekundäre Azure SQL-Datenbank-Georeplikate (in der gleichen oder in verschiedenen Regionen) für dasselbe primäre Replikat erstellen müssen, nutzen Sie die [aktive Georeplikation](active-geo-replication-overview.md).
> 
> Autofailover-Gruppen werden in der Dienstebene [Hyperscale](service-tier-hyperscale.md) zurzeit nicht unterstützt. Verwenden Sie für geografisches Failover einer Hyperscale-Datenbank die [aktive Georeplikation](active-geo-replication-overview.md).

Wenn Sie Autofailover-Gruppen mit einer Richtlinie für automatisches Failover verwenden, führt jeder Ausfall, der eine oder mehrere der Datenbanken in der Gruppe betrifft, zu einem automatischen Geofailover. In der Regel handelt es sich hierbei um Ausfälle, die nicht automatisch durch die integrierte Hochverfügbarkeitsinfrastruktur behoben werden können. Zu den Beispielen für Geofailoverauslöser gehört ein Vorfall, der dadurch verursacht wird, dass ein SQL-Datenbank-Mandantenring oder -Steuerungsring aufgrund eines Speicherlecks des Betriebssystemkernels auf Computeknoten ausgefallen ist, oder ein Vorfall, der dadurch verursacht wird, dass ein oder mehrere Mandantenringe ausgefallen sind, weil während der routinemäßigen Außerbetriebnahme von Hardware versehentlich ein falsches Netzwerkkabel durchtrennt wurde. Weitere Informationen finden Sie unter [Hochverfügbarkeit von SQL-Datenbank](high-availability-sla.md).

Darüber hinaus bieten Gruppen für automatisches Failover Lese-/Schreib-Listenerendpunkte, die während eines Geofailovers unverändert bleiben. Sowohl bei manueller als auch automatischer Failover-Aktivierung schaltet das Geofailover alle sekundären Datenbanken in der Gruppe zur primären Rolle um. Nach Abschluss des Geofailovers wird der DNS-Eintrag automatisch aktualisiert, um die Endpunkte in die neue Region umzuleiten. Informationen zu Geofailover-RPO und -RTO finden Sie unter [Übersicht über die Geschäftskontinuität](business-continuity-high-availability-disaster-recover-hadr-overview.md).

Wenn Sie Autofailover-Gruppen mit einer Richtlinie für automatisches Failover verwenden, führt jeder Ausfall, der Datenbanken auf einem Server oder in der verwalteten Instanz betrifft, zu einem automatischen Geofailover. 

Sie können Autofailover-Gruppen über folgende Komponenten verwalten:

- [Azure portal](geo-distributed-application-configure-tutorial.md)
- [Azure-Befehlszeilenschnittstelle: Failovergruppe](scripts/add-database-to-failover-group-cli.md)
- [PowerShell: Failovergruppe](scripts/add-database-to-failover-group-powershell.md)
- [REST-API: Failovergruppe](/rest/api/sql/failovergroups)

Stellen Sie beim Konfigurieren einer Failovergruppe sicher, dass die Authentifizierung und der Netzwerkzugriff auf der sekundären Datenbank so eingerichtet sind, dass sie nach dem Geofailover ordnungsgemäß funktionieren, wenn die sekundäre Geodatenbank zur neuen primären Datenbank wird. Weitere Informationen finden Sie unter [Verwalten der Sicherheit der Azure SQL-Datenbank nach der Notfallwiederherstellung](active-geo-replication-security-configure.md).

Wenn Sie echte Geschäftskontinuität erreichen möchten, ist das Hinzufügen einer regionalen Datenbankredundanz nur ein Teil der Lösung. Für die komplette Wiederherstellung einer Anwendung bzw. eines Diensts nach einem schwerwiegenden Fehler ist das Wiederherstellen aller Komponenten erforderlich, aus denen sich der Dienst und alle abhängigen Dienste zusammensetzen. Beispiele dieser Komponenten sind die Clientsoftware (z. B. ein Browser mit benutzerdefiniertem JavaScript), Web-Front-Ends, Speicher und DNS. Es ist wichtig, dass alle Komponenten hinsichtlich derselben Fehler gegen Ausfälle geschützt und innerhalb des RTO (Recovery Time Objective) der Anwendung wieder verfügbar sind. Daher müssen Sie alle abhängigen Dienste bestimmen und mit dem Leistungsumfang und den Funktionen vertraut sein, die sie bieten. Dann müssen Sie entsprechende Maßnahmen ergreifen, um sicherzustellen, dass Ihr Dienst während des Failovers der Dienste funktioniert, von denen er abhängig ist. Weitere Informationen zum Entwerfen von Lösungen für die Notfallwiederherstellung finden Sie unter [Entwerfen von Cloudlösungen für die Notfallwiederherstellung mithilfe der aktiven Georeplikation](designing-cloud-solutions-for-disaster-recovery.md).

## <a name="failover-group-terminology-and-capabilities"></a><a name="terminology-and-capabilities"></a> Failovergruppen – Terminologie und Funktionen

- **Failovergruppe**

  Eine Failovergruppe ist eine benannte Gruppe von Datenbanken, die von einem einzelnen Server oder innerhalb einer einzelnen verwalteten Instanz verwaltet werden. Diese Datenbanken sind eine Einheit, für die ein Failover in eine andere Region durchgeführt werden kann, falls alle oder einige primäre Datenbanken aufgrund eines Ausfalls in der primären Region nicht mehr verfügbar sind. Wenn eine Failovergruppe für SQL Managed Instance erstellt wird, enthält sie alle Benutzerdatenbanken in der Instanz. Deshalb kann nur eine Failovergruppe für eine Instanz konfiguriert werden.
  
  > [!IMPORTANT]
  > Der Name der Failovergruppe muss innerhalb der `.database.windows.net`-Domäne global eindeutig sein.

- **Server**

  Einige oder alle Benutzerdatenbanken auf einem logischen Server können in einer Failovergruppe platziert werden. Außerdem unterstützt ein Server mehrere Failovergruppen auf einem einzelnen Server.

- **Primärer Server/verwaltete Instanz**

  Der Server oder die verwaltete Instanz, auf dem bzw. in der die primären Datenbanken in der Failovergruppe gehostet werden.

- **Sekundärer Server/verwaltete Instanz**

  Der Server oder die verwaltete Instanz, auf dem bzw. in der die sekundären Datenbanken in der Failovergruppe gehostet werden. Der sekundäre Server/verwaltete Instanz kann sich nicht in der gleichen Region wie der primäre Server/verwaltete Instanz befinden.

- **Hinzufügen einzelner Datenbanken zu Failovergruppe**

  Sie können mehrere einzelne Datenbanken auf demselben Server in dieselbe Failovergruppe einfügen. Wenn Sie der Failovergruppe eine einzelne Datenbank hinzufügen, wird auf dem sekundären Server automatisch eine sekundäre Datenbank mit derselben Edition und Computegröße erstellt. Sie haben diesen Server beim Erstellen der Failovergruppe angegeben. Wenn Sie eine Datenbank hinzufügen, die bereits auf dem sekundären Server eine sekundäre Datenbank hat, erbt die Gruppe diese Verknüpfung für die Georeplikation. Wenn Sie eine Datenbank hinzufügen, die bereits eine sekundäre Datenbank auf einem Server hat, der nicht Teil der Failovergruppe ist, wird eine neue sekundäre Datenbank auf dem sekundären Server erstellt.

  > [!IMPORTANT]
  > Stellen Sie sicher, dass der sekundäre Server keine Datenbank mit dem gleichen Namen hat, es sei denn, es handelt sich um eine vorhandene sekundäre Datenbank. In Failovergruppen für SQL Managed Instance werden alle Benutzerdatenbanken repliziert. Sie können nicht eine Teilmenge der Benutzerdatenbanken für die Replikation in der Failovergruppe auswählen.

- **Hinzufügen von Datenbanken im Pool für elastische Datenbanken zu Failovergruppe**

  Sie können mehrere oder alle Datenbanken in einem Pool für elastische Datenbanken in dieselbe Failovergruppe einfügen. Wenn sich die primäre Datenbank in einem Pool für elastische Datenbanken befindet, wird die sekundäre Datenbank automatisch im Pool für elastische Datenbanken desselben Namens (sekundärer Pool) erstellt. Sie müssen sicherstellen, dass der sekundäre Server einen Pool für elastische Datenbanken mit genau demselben Namen und ausreichend freier Kapazität zum Hosten der sekundären Datenbanken enthält, die von der Failovergruppe erstellt werden. Wenn Sie im Pool eine Datenbank hinzufügen, die bereits im sekundären Pool eine sekundäre Datenbank hat, erbt die Gruppe diese Verknüpfung für die Georeplikation. Wenn Sie eine Datenbank hinzufügen, die bereits eine sekundäre Datenbank auf einem Server hat, der nicht Teil der Failovergruppe ist, wird eine neue sekundäre Datenbank im sekundären Pool erstellt.
  
- **Anfängliches Seeding**

  Beim Hinzufügen von Datenbanken, Pools für elastische Datenbanken oder verwalteten Instanzen zu einer Failovergruppe gibt es eine anfängliche Seedingphase, bevor die Datenreplikation startet. Die anfängliche Seedingphase ist der längste und aufwendigste Vorgang. Sobald das anfängliche Seeding abgeschlossen ist, werden die Daten synchronisiert, und anschließend werden nur nachfolgende Datenänderungen repliziert. Die Zeit, die für das anfängliche Seeding benötigt wird, hängt von der Größe Ihrer Daten, der Anzahl der replizierten Datenbanken, der Last für primäre Datenbanken und der Geschwindigkeit der Verbindung zwischen der primären und der sekundären Datenbank ab. Unter normalen Umständen beträgt die mögliche Seedinggeschwindigkeit bis zu 500 GB pro Stunde für SQL-Datenbank und bis zu 360 GB pro Stunde für SQL Managed Instance. Das Seeding wird für alle Datenbanken parallel ausgeführt.

  Bei SQL Managed Instance muss zum Abschätzen der Zeit für die anfängliche Seedingphase auch die Geschwindigkeit der ExpressRoute-Verbindung zwischen den beiden Instanzen berücksichtigt werden. Wenn die Geschwindigkeit der Verbindung zwischen den beiden Instanzen langsamer als erforderlich ist, wirkt sich dies wahrscheinlich erheblich auf die Zeit für das Seeding aus. Anhand der angegebenen Seedinggeschwindigkeit, der Anzahl der Datenbanken, der Gesamtgröße der Daten und der Verbindungsgeschwindigkeit können Sie abschätzen, wie lange die anfängliche Seedingphase vor dem Starten der Datenreplikation dauern wird. Beispielsweise würde für eine einzelne Datenbank mit 100 GB die anfängliche Seedingphase etwa 1,2 Stunden dauern, wenn die Verbindung eine Pushübertragung von 84 GB pro Stunde ermöglicht und für keine anderen Datenbanken ein Seeding ausgeführt wird. Können über die Verbindung nur 10 GB pro Stunde übertragen werden, dauert das Seeding einer 100-GB-Datenbank ungefähr 10 Stunden. Wenn mehrere Datenbanken zu replizieren sind, wird das Seeding parallel ausgeführt, und in Kombination mit einer niedrigen Verbindungsgeschwindigkeit kann die anfängliche Seedingphase erheblich länger dauern, insbesondere dann, wenn das parallele Seeding von Daten aus allen Datenbanken die verfügbare Verbindungsbandbreite überschreitet. Wenn die Netzwerkbandbreite zwischen zwei Instanzen begrenzt ist und Sie einer Failovergruppe mehrere verwaltete Instanzen hinzufügen, sollten Sie der Failovergruppe mehrere verwaltete Instanzen ggf. einzeln nacheinander hinzufügen. Wenn eine Gateway-SKU mit entsprechender Größe zwischen den beiden verwalteten Instanzen vorhanden ist und die Bandbreite des Unternehmensnetzwerks dies zulässt, können Geschwindigkeiten von bis zu 360 GB pro Stunde erreicht werden.  

- **DNS-Zone**

  Eine eindeutige ID, die automatisch generiert wird, wenn eine neue SQL Managed Instance erstellt wird. Ein Zertifikat mit mehreren Domänen (SAN) für diese Instanz wird bereitgestellt, um die Clientverbindungen mit einer beliebigen Instanz in der gleichen DNS-Zone zu authentifizieren. Die beiden verwalteten Instanzen in der gleichen Failovergruppe müssen die DNS-Zone gemeinsam verwenden.
  
  > [!NOTE]
  > Eine DNS-Zonen-ID ist für Failovergruppen, die für SQL-Datenbank erstellt werden, nicht erforderlich und wird nicht verwendet.

- **Lese-/Schreib-Failovergruppenlistener**

  Ein DNS CNAME-Eintrag, der auf die aktuelle primäre Datenbank verweist. Er wird automatisch erstellt, wenn die Failovergruppe erstellt wird, und ermöglicht der Lese-/Schreibworkload die transparente Verbindungswiederherstellung mit der primären Datenbank, wenn sich die primäre Datenbank nach dem Failover ändert. Wenn die Failovergruppe auf einem Server erstellt wird, hat der DNS CNAME-Eintrag für die Listener-URL die Form `<fog-name>.database.windows.net`. Wenn die Failovergruppe in einer SQL Managed Instance erstellt wird, hat der DNS CNAME-Eintrag für die Listener-URL die Form `<fog-name>.<zone_id>.database.windows.net`.

- **Nur-Lese-Failovergruppenlistener**

  Ein DNS CNAME-Eintrag, der auf die aktuelle sekundäre Datenbank verweist. Er wird automatisch erstellt, wenn die Failovergruppe erstellt wird, und ermöglicht der Lese-/Schreib-SQL-Workload die transparente Verbindungsherstellung mit der sekundären Datenbank, wenn sich die sekundäre Datenbank nach dem Failover ändert. Wenn die Failovergruppe auf einem Server erstellt wird, hat der DNS CNAME-Eintrag für die Listener-URL die Form `<fog-name>.secondary.database.windows.net`. Wenn die Failovergruppe in einer SQL Managed Instance erstellt wird, hat der DNS CNAME-Eintrag für die Listener-URL die Form `<fog-name>.secondary.<zone_id>.database.windows.net`.

- **Richtlinie für automatisches Failover**

  Standardmäßig wird eine Failovergruppe mit einer Richtlinie für automatisches Failover konfiguriert. Das System löst ein Geofailover aus, nachdem der Fehler erkannt wurde und die Toleranzperiode abgelaufen ist. Das System muss sicherstellen, dass der Ausfall zum Beispiel aufgrund des Ausmaßes der Auswirkungen nicht durch die integrierte [Hochverfügbarkeitsinfrastruktur](high-availability-sla.md) behoben werden kann. Wenn Sie den Geofailoverworkflow über die Anwendung oder manuell steuern möchten, können Sie die Richtlinie für automatisches Failover deaktivieren.
  
  > [!NOTE]
  > Da Maßnahmen ergriffen werden müssen, um das Ausmaß des Ausfalls zu überprüfen und festzustellen, wie schnell dieser minimiert werden kann, kann die Toleranzperiode nicht auf einen Wert unter einer Stunde festgelegt werden. Diese Einschränkung gilt unabhängig vom jeweiligen Datensynchronisierungsstatus für alle Datenbanken in der Failovergruppe.

- **Schreibgeschützte Failoverrichtlinie**

  Standardmäßig ist das Failover des schreibgeschützten Listeners deaktiviert. Dadurch wird sichergestellt, dass die Leistung der primären Datenbank nicht beeinträchtigt wird, wenn die sekundäre Datenbank offline ist. Es bedeutet jedoch auch, dass die schreibgeschützten Sitzungen erst dann eine Verbindung herstellen können, nachdem die sekundäre Datenbank wiederhergestellt wurde. Wenn Sie keine Downtime für die schreibgeschützten Sitzungen tolerieren und die primäre Datenbank sowohl für schreibgeschützten Datenverkehr als auch für Lese-/Schreibdatenverkehr verwenden können (auf Kosten der möglichen Leistungseinbußen für die primäre Datenbank), können Sie das Failover für den schreibgeschützten Listener aktivieren, indem Sie die Eigenschaft `AllowReadOnlyFailoverToPrimary` konfigurieren. In diesem Fall wird der schreibgeschützte Datenverkehr automatisch zur primären Datenbank umgeleitet, wenn die sekundäre Datenbank nicht verfügbar ist.

  > [!NOTE]
  > Die Eigenschaft `AllowReadOnlyFailoverToPrimary` ist nur wirksam, wenn die Richtlinie für automatisches Failover aktiviert ist und ein automatisches Geofailover ausgelöst wurde. Ist die Eigenschaft in diesem Fall auf „True“ festgelegt, werden von der primären Datenbank sowohl Sitzung mit Lese-/Schreibzugriff als auch schreibgeschützte Sitzungen bedient.

- **Geplantes Failover**

  Beim geplanten Failover wird eine vollständige Datensynchronisierung zwischen primärer und sekundärer Datenbank ausgeführt, bevor die sekundäre Datenbank die Rolle der primären Datenbank übernimmt. Dadurch ist sichergestellt, dass keine Daten verloren gehen. Ein geplantes Failover wird in den folgenden Szenarien verwendet:

  - Ausführen von DR-Drills (Notfallwiederherstellung) in der Produktion, wenn ein Datenverlust nicht akzeptabel ist
  - Verschieben der Datenbanken in eine andere Region
  - Rückkehr der Datenbanken in die primäre Region, nachdem der Ausfall behoben wurde (Failback)

- **Nicht geplantes Failover**

  Beim ungeplanten oder erzwungenen Failover übernimmt die sekundäre Datenbank sofort die Rolle der primären Datenbank, ohne dass auf die Weitergabe kürzlicher Änderungen von der primären Datenbank gewartet wird. Dieser Vorgang kann zu Datenverlust führen. Ein ungeplantes Failover wird als Wiederherstellungsmethode bei Ausfällen verwendet, wenn auf die primäre Datenbank nicht zugegriffen werden kann. Wenn der Ausfall behoben wird, stellt die alte primäre Datenbank automatisch wieder eine Verbindung her und wird zu einer neuen sekundären Datenbank. Ein geplantes Failover kann ausgeführt werden, um ein Failback durchzuführen und die Replikate an ihre ursprünglichen primären und sekundären Rollen zurückzugeben.

- **Manuelles Failover**

  Sie können ein Geofailover zu einem beliebigen Zeitpunkt unabhängig von der Konfiguration für automatisches Failover manuell initiieren. Wenn bei einem Ausfall, der sich auf die primäre Datenbank auswirkt, keine Richtlinie für automatisches Failover konfiguriert ist, ist ein manuelles Failover erforderlich, um die sekundäre Datenbank auf die Rolle der primären Datenbank heraufzustufen. Sie können ein erzwungenes (ungeplantes) oder ein freundliches (geplantes) Failover initiieren. Ein freundliches Failover ist nur möglich, wenn auf die alte primäre Datenbank zugegriffen werden kann. Dieses Failover kann verwendet werden, um die primäre Datenbank ohne Datenverlust in die sekundäre Region zu verschieben. Nach Abschluss des Failovers werden die DNS-Einträge automatisch aktualisiert, um die Konnektivität mit der neuen primären Datenbank sicherzustellen.

- **Toleranzperiode mit Datenverlust**

  Da die sekundären Datenbanken mithilfe der asynchronen Replikation synchronisiert werden, kann ein automatisches Geofailover zu Datenverlust führen. Sie können die Richtlinie für automatisches Failover entsprechend der Toleranz Ihrer Anwendung gegenüber Datenverlust anpassen. Durch Konfiguration von `GracePeriodWithDataLossHours` können Sie steuern, wie lange das System wartet, bevor ein erzwungenes Failover initiiert wird, das zu Datenverlust führen kann.

- **Mehrere Failovergruppen**

  Sie können mehrere Failovergruppen für das gleiche Serverpaar konfigurieren, um den Umfang von Geofailovern zu steuern. Jede Gruppe führt ein unabhängiges Failover durch. Wenn Ihre Mandant-pro-Datenbank-Anwendung in mehreren Regionen bereitgestellt wird und Pools für elastische Datenbanken verwendet, können Sie diese Funktion verwenden, um primäre und sekundäre Datenbanken in jedem Pool zu kombinieren. Auf diese Weise können Sie möglicherweise die Auswirkungen eines Ausfalls auf einige Mandantendatenbanken reduzieren.

  > [!NOTE]
  > SQL Managed Instance bietet keine Unterstützung für mehrere Failovergruppen.
  
## <a name="permissions"></a>Berechtigungen

Berechtigungen für eine Failovergruppe werden über die [rollenbasierte Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC)](../../role-based-access-control/overview.md) verwaltet. Die Rolle [Mitwirkender von SQL Server](../../role-based-access-control/built-in-roles.md#sql-server-contributor) verfügt über die erforderlichen Berechtigungen zum Verwalten von Failovergruppen.

### <a name="create-a-failover-group"></a><a name="create-failover-group"></a> Erstellen einer Failovergruppe

Zum Erstellen einer Failovergruppe benötigen Sie Azure RBAC-Schreibzugriff auf den primären und die sekundären Server sowie auf alle Datenbanken in der Failovergruppe. Bei einer SQL Managed Instance benötigen Sie Azure RBAC-Schreibzugriff auf die primäre und sekundäre SQL Managed Instance. Berechtigungen für einzelne Datenbanken sind dabei nicht relevant, da einzelne Datenbanken der SQL Managed Instance nicht zu einer Failovergruppe hinzugefügt bzw. aus einer Failovergruppe entfernt werden können.

### <a name="update-a-failover-group"></a>Aktualisieren einer Failovergruppe

Zum Aktualisieren einer Failovergruppe benötigen Sie Azure RBAC-Schreibzugriff auf die Failovergruppe und auf alle Datenbanken auf dem aktuellen primären Server bzw. in der aktuellen verwalteten Instanz.  

### <a name="fail-over-a-failover-group"></a>Ausführen des Failovers einer Failovergruppe

Zum Erstellen eines Failovers für eine Failovergruppe benötigen Sie Azure RBAC-Schreibzugriff auf die Failovergruppe auf dem neuen primären Server bzw. in der neuen verwalteten Instanz.

## <a name="failover-group-best-practices-for-sql-database"></a><a name="best-practices-for-sql-database"></a>Bewährte Methoden für Failovergruppen für SQL-Datenbank

Die Autofailover-Gruppe muss auf dem primären Server konfiguriert werden und stellt eine Verbindung mit dem sekundären Server in einer anderen Azure-Region her. Die Gruppen können alle oder einige Datenbanken auf diesen Servern umfassen. Das folgende Diagramm zeigt eine typische Konfiguration einer georedundanten Cloudanwendung mit mehreren Datenbanken und einer Autofailover-Gruppe.

![Das Diagramm zeigt eine typische Konfiguration einer georedundanten Cloudanwendung mit mehreren Datenbanken und einer Autofailover-Gruppe.](./media/auto-failover-group-overview/auto-failover-group.png)

> [!NOTE]
> Ein ausführliches Tutorial zum Hinzufügen einer Datenbank in SQL-Datenbank zu einer Failovergruppe finden Sie unter [Hinzufügen einer SQL-Datenbank-Instanz zu einer Failovergruppe](failover-group-add-single-database-tutorial.md).

Beim Entwerfen eines Diensts, der die Geschäftskontinuität aufrechterhalten soll, sind die folgenden allgemeinen Richtlinien zu beachten:

### <a name="use-one-or-several-failover-groups-to-manage-failover-of-multiple-databases"></a><a name="using-one-or-several-failover-groups-to-manage-failover-of-multiple-databases"></a> Verwenden Sie mindestens eine Failovergruppe, um das Failover mehrerer Datenbanken zu verwalten.

Eine oder mehrere Failovergruppen können zwischen zwei Servern in verschiedenen Regionen erstellt werden (primäre und sekundäre Server). Jede Gruppe kann eine oder mehrere Datenbanken enthalten, die als Einheit wiederhergestellt werden, falls alle oder einige primäre Datenbanken aufgrund eines Ausfalls in der primären Region nicht mehr verfügbar sind. Beim Erstellen einer Failovergruppe werden sekundäre Geodatenbanken mit dem gleichen Dienstziel wie bei der primären Datenbank erstellt. Wenn Sie einer Failovergruppe eine vorhandene Georeplikationsbeziehung hinzufügen, stellen Sie sicher, dass die sekundäre Geodatenbank mit der gleichen Dienstebene und Computegröße wie die primäre Datenbank konfiguriert ist.
  
### <a name="use-the-read-write-listener-to-connect-to-primary"></a><a name="using-read-write-listener-for-oltp-workload"></a> Verwenden des Lese-/Schreiblisteners zum Herstellen einer Verbindung mit der primären Datenbank

Verwenden Sie für Lese-/Schreibworkloads `<fog-name>.database.windows.net` als Servernamen in der Verbindungszeichenfolge. Verbindungen werden automatisch an die primäre Datenbank weitergeleitet. Dieser Name wird nach dem Failover nicht geändert. Beachten Sie, dass das Failover die Aktualisierung von DNS-Einträgen einschließt, damit die Clientverbindungen erst dann an die neue primäre Datenbank weitergeleitet werden, wenn der Client-DNS-Cache aktualisiert wurde. Die Gültigkeitsdauer (TTL) des DNS-Eintrags für den primären und sekundären Listener beträgt 30 Sekunden.

### <a name="use-the-read-only-listener-to-connect-to-geo-secondary"></a><a name="using-read-only-listener-for-read-only-workload"></a> Verwenden des schreibgeschützten Listeners zum Herstellen einer Verbindung mit der sekundären Geodatenbank

Wenn Sie über logisch isolierte schreibgeschützte Workloads verfügen, die datenlatenztolerant sind, können Sie sie auf der sekundären Geodatenbank ausführen. Verwenden Sie für schreibgeschützte Sitzungen `<fog-name>.secondary.database.windows.net` als Servernamen in der Verbindungszeichenfolge. Verbindungen werden automatisch an die sekundäre Geodatenbank weitergeleitet. Darüber hinaus wird empfohlen, in der Verbindungszeichenfolge die Leseabsicht anzugeben. Verwenden Sie dazu `ApplicationIntent=ReadOnly`.

> [!NOTE]
> Auf den Dienstebenen „Premium“, „Unternehmenskritisch“ und „Hyperscale“ unterstützt SQL-Datenbank die Verwendung [schreibgeschützter Replikate](read-scale-out.md) zur Auslagerung schreibgeschützter Abfrageworkloads unter Verwendung des Parameters `ApplicationIntent=ReadOnly` in der Verbindungszeichenfolge. Wenn Sie eine sekundäre Geodatenbank konfiguriert haben, können Sie mit dieser Funktion eine Verbindung entweder mit einem schreibgeschützten Replikat am primären Standort oder am geografisch replizierten Standort herstellen.
>
> - Verwenden Sie `ApplicationIntent=ReadOnly` und `<fog-name>.database.windows.net`, um eine Verbindung mit einem schreibgeschützten Replikat am primären Standort herzustellen.
> - Wenn Sie eine Verbindung mit einem schreibgeschützten Replikat am sekundären Standort herstellen möchten, verwenden Sie `ApplicationIntent=ReadOnly` und `<fog-name>.secondary.database.windows.net`.

### <a name="potential-performance-degradation-after-geo-failover"></a><a name="preparing-for-performance-degradation"></a> Potenzielle Leistungsbeeinträchtigung nach einem Geofailover

Eine typische Azure-Anwendung nutzt mehrere Azure-Dienste und besteht aus mehreren Komponenten. Das automatisierte Geofailover der Failovergruppe wird ausschließlich anhand des Status der Azure SQL-Komponenten ausgelöst. Andere Azure-Dienste in der primären Region sind vom Ausfall ggf. nicht betroffen, und es kann sein, dass die entsprechenden Komponenten in dieser Region weiterhin verfügbar sind. Nachdem die primären Datenbanken auf die sekundäre Region (DR-Region) umgestellt wurden, erhöht sich unter Umständen die Wartezeit zwischen den abhängigen Komponenten. Damit die Auswirkungen einer höheren Latenz auf die Leistung der Anwendung vermieden werden, stellen Sie die Redundanz aller Anwendungskomponenten in der DR-Region sicher, befolgen Sie diese [Netzwerksicherheitsrichtlinien](#failover-groups-and-network-security), und orchestrieren Sie das Geofailover relevanter Anwendungskomponenten zusammen mit der Datenbank.

### <a name="potential-data-loss-after-geo-failover"></a><a name="preparing-for-data-loss"></a> Potenzieller Datenverlust nach einem Geofailover

Wenn es in der primären Region zu einem Ausfall kommt, können kürzlich durchgeführte Transaktionen möglicherweise nicht in die sekundäre Geodatenbank repliziert werden. Wenn die Richtlinie für automatisches Failover konfiguriert ist, wartet das System so lange, wie von Ihnen mit `GracePeriodWithDataLossHours` angegeben, bevor ein automatisches Geofailover initiiert wird. Der Standardwert ist 1 Stunde. Dadurch wird die Datenbankverfügbarkeit gegenüber Datenerhalt bevorzugt. Wenn Sie `GracePeriodWithDataLossHours` auf einen höheren Wert festlegen, z. B. 24 Stunden, oder das automatische Geofailover deaktivieren, können Sie die Wahrscheinlichkeit von Datenverlusten auf Kosten der Datenbankverfügbarkeit verringern.

> [!IMPORTANT]
> Bei Pools für elastische Datenbanken mit 800 oder weniger DTUs oder 8 oder weniger virtuellen Kernen und über 250 Datenbanken, können Probleme wie längere geplante Geofailover und Leistungseinbußen auftreten. Diese Probleme treten häufiger bei schreibintensiven Workloads auf, wenn Georeplikate geographisch weit auseinander liegen oder wenn mehrere sekundäre Georeplikate für jede Datenbank verwendet werden. Ein Symptom für diese Probleme ist eine im Laufe der Zeit zunehmende Verzögerung bei der Georeplikation, die möglicherweise bei einem Ausfall zu einem umfangreicheren Datenverlust führt. Diese Verzögerung können Sie mit [sys.dm_geo_replication_link_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-geo-replication-link-status-azure-sql-database) überwachen. Wenn diese Probleme auftreten, umfasst die Entschärfung das Hochskalieren des Pools, um mehr DTUs oder virtuelle Kerne zu erhalten, oder die Reduzierung der Anzahl georeplizierter Datenbanken im Pool.

### <a name="change-the-secondary-region-of-a-failover-group"></a><a name="changing-secondary-region-of-the-failover-group"></a> Ändern der sekundären Region einer Failovergruppe

Zur Verdeutlichung der Änderungssequenz treffen wir die Annahme, dass Server A der primäre Server, Server B der vorhandene sekundäre Server und Server C der neue sekundäre Server in der dritten Region ist. Führen Sie diese Schritte aus, um die Umstellung durchzuführen:

1. Erstellen Sie für jede Datenbank zusätzliche sekundäre Replikate von Server A auf Server C, indem Sie die [aktive Georeplikation](active-geo-replication-overview.md) verwenden. Jede Datenbank auf Server A verfügt über zwei sekundäre Replikate: eins auf Server B und eins auf Server C. So wird garantiert, dass die primären Datenbanken während der Umstellung geschützt bleiben.
2. Löschen Sie die Failovergruppe. An diesem Punkt treten bei Anmeldeversuchen, die Failovergruppenendpunkte verwenden, Fehler auf.
3. Erstellen Sie die Failovergruppe mit dem gleichen Namen zwischen den Servern A und C neu.
4. Fügen Sie alle primären Datenbanken von Server A der neuen Failovergruppe hinzu. An diesem Punkt werden die Anmeldeversuche nicht mehr fehlschlagen.
5. Löschen Sie Server B. Alle Datenbanken auf Server B werden automatisch gelöscht.

### <a name="change-the-primary-region-of-a-failover-group"></a><a name="changing-primary-region-of-the-failover-group"></a> Ändern der primären Region einer Failovergruppe

Zur Verdeutlichung der Änderungssequenz nehmen wir an, dass Server A der primäre Server, Server B der vorhandene sekundäre Server und Server C der neue primäre Server in der dritten Region ist. Führen Sie diese Schritte aus, um die Umstellung durchzuführen:

1. Führen Sie ein geplantes Geofailover durch, um den primären Server auf B umzustellen. Server A wird zum neuen sekundären Server. Beim Failover kann es zu einem Ausfall mit einer Dauer von mehreren Minuten kommen. Die tatsächliche Dauer hängt von der Größe der Failovergruppe ab.
2. Erstellen Sie für jede Datenbank zusätzliche sekundäre Replikate von Server B auf Server C, indem Sie die [aktive Georeplikation](active-geo-replication-overview.md) verwenden. Jede Datenbank auf Server B verfügt über zwei sekundäre Replikate: eins auf Server A und eins auf Server C. So wird garantiert, dass die primären Datenbanken während der Umstellung geschützt bleiben.
3. Löschen Sie die Failovergruppe. An diesem Punkt treten bei Anmeldeversuchen, die Failovergruppenendpunkte verwenden, Fehler auf.
4. Erstellen Sie die Failovergruppe mit dem gleichen Namen zwischen den Servern B und C neu.
5. Fügen Sie alle primären Datenbanken von Server B der neuen Failovergruppe hinzu. An diesem Punkt werden die Anmeldeversuche nicht mehr fehlschlagen.
6. Führen Sie für die Failovergruppe ein geplantes Geofailover durch, um B und C umzustellen. Server C wird zum primären und Server B zum sekundären Server. Alle sekundären Datenbanken auf Server A werden automatisch mit den primären Replikaten auf C verknüpft. Wie in Schritt 1 auch, kann es beim Failover zu einem Ausfall von mehreren Minuten kommen.
7. Löschen Sie Server A. Alle Datenbanken auf A werden automatisch gelöscht.

> [!IMPORTANT]
> Wenn die Failovergruppe gelöscht wird, werden auch die DNS-Einträge für die Listenerendpunkte gelöscht. An diesem Punkt ist nicht völlig ausgeschlossen, dass eine andere Person eine Failovergruppe oder einen Server-DNS-Alias mit dem gleichen Namen erstellt. Da Failovergruppennamen und DNS-Aliase global eindeutig sein müssen, wird verhindert, dass Sie denselben Namen noch mal verwenden. Verwenden Sie keine generischen Namen für Failovergruppen um dieses Risiko zu minimieren.

## <a name="failover-group-best-practices-for-sql-managed-instance"></a><a name="best-practices-for-sql-managed-instance"></a>Bewährte Methoden für Failovergruppen für SQL Managed Instance

Die Autofailover-Gruppe muss auf der primären Instanz konfiguriert werden und stellt eine Verbindung zur sekundären Instanz in einer anderen Azure-Region her.  Alle Benutzerdatenbanken in der Instanz werden in der sekundären Instanz repliziert. Systemdatenbanken wie _master_ und _msdb_ werden nicht repliziert.

Das folgende Diagramm zeigt eine typische Konfiguration einer georedundanten Cloudanwendung mit einer verwalteten Instanz und Autofailover-Gruppe.

![Autofailoverdiagramm](./media/auto-failover-group-overview/auto-failover-group-mi.png)

> [!NOTE]
> Eine ausführliche schrittweise Anleitung zum Hinzufügen einer SQL Managed Instance zum Verwenden einer Failovergruppe finden Sie unter [Hinzufügen einer verwalteten Instanz zu einer Failovergruppe](../managed-instance/failover-group-add-instance-tutorial.md).

Wenn Ihre Anwendung SQL Managed Instance als Datenebene verwendet, beachten Sie beim Entwerfen für Geschäftskontinuität die folgenden allgemeinen Richtlinien:

### <a name="create-the-geo-secondary-managed-instance"></a><a name="creating-the-secondary-instance"></a> Erstellen der verwalteten sekundären Geoinstanz

Damit eine unterbrechungsfreie Verbindung mit der primären SQL Managed Instance nach einem Failover sichergestellt ist, müssen sich primäre und sekundäre Instanz in der gleichen DNS-Zone befinden. Es wird sichergestellt, dass das gleiche Zertifikat mit mehreren Domänen (SAN) zum Authentifizieren von Clientverbindungen mit einer der zwei Instanzen in der Failovergruppe verwendet werden kann. Wenn Ihre Anwendung für die Bereitstellung in der Produktionsumgebung bereit ist, erstellen Sie eine sekundäre SQL Managed Instance in einer anderen Region, und stellen Sie sicher, dass sie die gleiche DNS-Zone wie die primäre SQL Managed Instance verwendet. Dies können Sie erreichen, indem Sie während der Erstellung einen optionalen Parameter angeben. Wenn Sie PowerShell oder die REST-API verwenden, ist der Name des optionalen Parameters `DNSZonePartner`. Der Name des entsprechenden optionalen Felds im Azure-Portal ist *Primäre verwaltete Instanz*.

> [!IMPORTANT]
> Die erste im Subnetz erstellte verwaltete Instanz bestimmt die DNS-Zone für alle nachfolgenden Instanzen im gleichen Subnetz. Dies bedeutet, dass zwei Instanzen aus dem gleichen Subnetz nicht zu verschiedenen DNS-Zonen gehören können.

Weitere Informationen zum Erstellen der sekundären SQL Managed Instance in derselben DNS-Zone wie die primäre Instanz finden Sie unter [Erstellen einer sekundären verwalteten Instanz](../managed-instance/failover-group-add-instance-tutorial.md#create-a-secondary-managed-instance).

### <a name="use-paired-regions"></a><a name="using-geo-paired-regions"></a> Verwenden von Regionspaaren

Stellen Sie beide verwalteten Instanzen aus Leistungsgründen in [gekoppelten Regionen](../../best-practices-availability-paired-regions.md) bereit. SQL Managed Instance-Failovergruppen in gekoppelten Regionen weisen im Vergleich zu nicht gekoppelten Regionen eine bessere Leistung auf.

### <a name="enable-geo-replication-traffic-between-two-managed-instances"></a><a name="enabling-replication-traffic-between-two-instances"></a> Aktivieren von Georeplikationsdatenverkehr zwischen zwei verwalteten Instanzen

Da jede verwaltete Instanz in einem eigenen VNet isoliert ist, muss der bidirektionale Datenverkehr zwischen diesen VNets zugelassen werden. Informationen dazu finden Sie unter [Azure VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md).

### <a name="create-a-failover-group-between-managed-instances-in-different-subscriptions"></a><a name="creating-a-failover-group-between-managed-instances-in-different-subscriptions"></a> Erstellen einer Failovergruppe zwischen verwalteten Instanzen in verschiedenen Abonnements

Sie können eine Failovergruppe zwischen SQL Managed Instances in zwei verschiedenen Abonnements erstellen, solange Abonnements dem gleichen [Azure Active Directory-Mandanten](../../active-directory/fundamentals/active-directory-whatis.md#terminology) zugeordnet sind. Wenn Sie die PowerShell-API verwenden, können Sie dazu den Parameter `PartnerSubscriptionId` für die sekundäre SQL Managed Instance angeben. Wenn Sie die REST-API verwenden, kann jede im Parameter `properties.managedInstancePairs` enthaltene Instanz-ID eine eigene Abonnement-ID haben.
  
> [!IMPORTANT]
> Das Erstellen von Failovergruppen über verschiedene Abonnements hinweg wird im Azure-Portal nicht unterstützt. Für vorhandene Failovergruppen, die verschiedene Abonnements und/oder Ressourcengruppen umfassen, kann das Failover zudem nicht von der primären SQL Managed Instance aus manuell über das Portal initiiert werden. Initiieren Sie das Failover stattdessen über die sekundäre Geoinstanz.

### <a name="manage-geo-failover-to-a-geo-secondary-instance"></a><a name="managing-failover-to-secondary-instance"></a> Verwalten von Geofailovern auf eine sekundäre Geoinstanz

Die Failovergruppe verwaltet das Geofailover aller Datenbanken auf der primären verwalteten Instanz. Beim Erstellen einer Gruppe wird jede Datenbank in der Instanz automatisch in der sekundären Geoinstanz geografisch repliziert. Sie können Failovergruppen nicht verwenden, um ein Teilfailover einer Untergruppe von Datenbanken zu initiieren.

> [!IMPORTANT]
> Wird eine Datenbank aus der primären verwalteten Instanz entfernt, wird sie automatisch auch in der sekundären verwalteten Geoinstanz gelöscht.

### <a name="use-the-read-write-listener-to-connect-to-the-primary-managed-instance"></a><a name="using-read-write-listener-for-oltp-workload"></a> Verwenden des Lese-/Schreiblisteners zum Herstellen einer Verbindung mit der primären verwalteten Instanz

Verwenden Sie für Lese-/Schreibworkloads `<fog-name>.zone_id.database.windows.net` als Servernamen. Verbindungen werden automatisch an die primäre Datenbank weitergeleitet. Dieser Name wird nach dem Failover nicht geändert. Das Geofailover umfasst die Aktualisierung von DNS-Einträgen, damit die Clientverbindungen erst dann an die neue primäre Datenbank weitergeleitet werden, wenn der Client-DNS-Cache aktualisiert wurde. Da die sekundäre Instanz die gleiche DNS-Zone wie die primäre Instanz verwendet, kann die Clientanwendung mit dem gleichen serverseitigen SAN-Zertifikat erneut eine Verbindung herstellen.

### <a name="use-the-read-only-listener-to-connect-to-the-geo-secondary-managed-instance"></a><a name="using-read-only-listener-to-connect-to-the-secondary-instance"></a> Verwenden des schreibgeschützten Listeners zum Herstellen einer Verbindung mit der sekundären verwalteten Geoinstanz

Wenn Sie über logisch isolierte schreibgeschützte Workloads verfügen, die datenlatenztolerant sind, können Sie sie auf der sekundären Geodatenbank ausführen. Verwenden Sie `<fog-name>.secondary.<zone_id>.database.windows.net` als Servernamen, um eine direkte Verbindung mit der sekundären Geodatenbank herzustellen.

> [!NOTE]
> Auf der Dienstebene „Unternehmenskritisch“ unterstützt SQL Managed Instance die Verwendung [schreibgeschützter Replikate](read-scale-out.md) zur Auslagerung schreibgeschützter Abfrageworkloads unter Verwendung des Parameters `ApplicationIntent=ReadOnly` in der Verbindungszeichenfolge. Wenn Sie eine georeplizierte sekundäre Datenbank konfiguriert haben, können Sie mit dieser Funktion eine Verbindung entweder mit einem schreibgeschützten Replikat am primären Standort oder am geografisch replizierten Standort herstellen.
>
> - Verwenden Sie `ApplicationIntent=ReadOnly` und `<fog-name>.<zone_id>.database.windows.net`, um eine Verbindung mit einem schreibgeschützten Replikat am primären Standort herzustellen.
> - Wenn Sie eine Verbindung mit einem schreibgeschützten Replikat am sekundären Standort herstellen möchten, verwenden Sie `ApplicationIntent=ReadOnly` und `<fog-name>.secondary.<zone_id>.database.windows.net`.

### <a name="potential-performance-degradation-after-failover-to-the-geo-secondary-managed-instance"></a>Potenzielle Leistungsbeeinträchtigung nach einem Failover auf die sekundäre verwaltete Geoinstanz

Eine typische Azure-Anwendung nutzt mehrere Azure-Dienste und besteht aus mehreren Komponenten. Das automatisierte Geofailover der Failovergruppe wird ausschließlich anhand des Status der Azure SQL-Komponenten ausgelöst. Andere Azure-Dienste in der primären Region sind vom Ausfall ggf. nicht betroffen, und es kann sein, dass die entsprechenden Komponenten in dieser Region weiterhin verfügbar sind. Nachdem die primären Datenbanken auf die sekundäre Region umgestellt wurden, erhöht sich unter Umständen die Wartezeit zwischen den abhängigen Komponenten. Gewährleisten Sie die Redundanz aller Anwendungskomponenten in der sekundären Region, und führen Sie Failover für Anwendungskomponenten zusammen mit der Datenbank durch, um zu vermeiden, dass die Leistung der Anwendung durch höhere Wartezeit beeinträchtigt wird. Halten Sie sich bei der Konfiguration an die [Richtlinien für die Netzwerksicherheit](#failover-groups-and-network-security), um die Konnektivität mit der Datenbank in der sekundären Region sicherzustellen.

### <a name="potential-data-loss-after-failover-to-the-geo-secondary-managed-instance"></a>Potenzieller Datenverlust nach einem Failover auf die sekundäre verwaltete Geoinstanz

Wenn es in der primären Region zu einem Ausfall kommt, können kürzlich durchgeführte Transaktionen möglicherweise nicht in die sekundäre Geodatenbank repliziert werden. Wenn die Richtlinie für automatisches Failover konfiguriert ist, wird nach bestem Wissen ein Geofailover ausgelöst, wenn keine Daten verloren gehen. Andernfalls wird das Failover für den mithilfe von `GracePeriodWithDataLossHours` angegebenen Zeitraum zurückgestellt. Wenn Sie die Richtlinie für automatisches Failover konfiguriert haben, sollten Sie auf Datenverluste vorbereitet sein. Bei Ausfällen priorisiert Azure grundsätzlich die Verfügbarkeit. Wenn Sie `GracePeriodWithDataLossHours` auf einen höheren Wert festlegen, z. B. 24 Stunden, oder das automatische Geofailover deaktivieren, können Sie die Wahrscheinlichkeit von Datenverlusten auf Kosten der Datenbankverfügbarkeit verringern.

Die DNS-Aktualisierung des Lese-/Schreib-Listeners erfolgt sofort nach dem Initiieren des Failovers. Bei diesem Vorgang gehen keine Daten verloren. Allerdings kann der Prozess zum Wechseln der Datenbankrollen unter normalen Bedingungen bis zu 5 Minuten dauern. Bis er abgeschlossen ist, sind einige Datenbanken in der neuen primären Instanz noch schreibgeschützt. Wenn ein Failover mithilfe von PowerShell initiiert wird, ist der Vorgang für den Wechsel der primären Replikatrolle synchron. Wird es über das Azure-Portal initiiert, wird auf der Benutzeroberfläche der Abschlussstatus angegeben. Bei Initiierung über die REST-API verwenden Sie den Standardabfragemechanismus von Azure Resource Manager, um den Vorgang auf seinen Abschluss zu überwachen.

> [!IMPORTANT]
> Verwenden Sie ein manuelles geplantes Failover, um die primäre Datenbank zurück an den ursprünglichen Speicherort zu verschieben, sobald der Ausfall, der das Geofailover verursacht hat, entschärft wurde.
  
### <a name="change-the-secondary-region-of-the-managed-instance-failover-group"></a>Ändern der sekundären Region der Failovergruppe der verwalteten Instanz

Angenommen, Instanz A ist die primäre Instanz, Instanz B die vorhandene sekundäre Instanz und Instanz C die neue sekundäre Instanz in der dritten Region. Führen Sie diese Schritte aus, um die Umstellung durchzuführen:

1. Erstellen Sie Instanz C mit der gleichen Größe wie A und in derselben DNS-Zone.
2. Löschen Sie die Failovergruppe zwischen den Instanzen A und B. An diesem Punkt treten bei Anmeldungen Fehler auf, weil die SQL-Aliase für die Failovergruppenlistener gelöscht wurden und das Gateway den Namen der Failovergruppe nicht erkennt. Die sekundären Datenbanken werden von den primären Replikaten getrennt und werden zu Datenbanken mit Lese-/Schreibzugriff.
3. Erstellen Sie eine Failovergruppe mit dem gleichen Namen zwischen Instanz A und C. Befolgen Sie die Anleitung im [Tutorial zu Failovergruppen mit SQL Managed Instance](../managed-instance/failover-group-add-instance-tutorial.md). Dies ist ein Vorgang, bei dem der Umfang der Daten eine Rolle spielt. Er ist abgeschlossen, wenn für alle Datenbanken von Instanz A das Seeding und die Synchronisierung durchgeführt wurde.
4. Löschen Sie Instanz B, wenn sie nicht benötigt wird, um unnötige Kosten zu vermeiden.

> [!NOTE]
> Nach Schritt 2 und bis zum Abschluss von Schritt 3 sind die Datenbanken auf Instanz A vor dem Ausfall von Instanz A aufgrund eines Katastrophenfalls ungeschützt.

### <a name="change-the-primary-region-of-the-managed-instance-failover-group"></a>Ändern der primären Region der Failovergruppe der verwalteten Instanz

Angenommen, Instanz A ist die primäre Instanz, Instanz B die vorhandene sekundäre Instanz und Instanz C die neue primäre Instanz in der dritten Region. Führen Sie diese Schritte aus, um die Umstellung durchzuführen:

1. Erstellen Sie Instanz C mit der gleichen Größe wie B und in derselben DNS-Zone.
2. Stellen Sie eine Verbindung mit Instanz B her, und führen Sie ein manuelles Failover durch, um die primäre Instanz auf B umzustellen. Instanz A wird automatisch zur neuen sekundären Instanz.
3. Löschen Sie die Failovergruppe zwischen den Instanzen A und B. An diesem Punkt treten bei Anmeldeversuchen, die Failovergruppenendpunkte verwenden, Fehler auf. Die sekundären Datenbanken auf A werden von den primären Datenbanken getrennt und werden zu Datenbanken mit Lese-/Schreibzugriff.
4. Erstellen Sie eine Failovergruppe mit dem gleichen Namen zwischen Instanz A und C. Befolgen Sie die Anleitung im [Tutorial zu Failovergruppen mit verwalteten Instanzen](../managed-instance/failover-group-add-instance-tutorial.md). Dies ist ein Vorgang, bei dem der Umfang der Daten eine Rolle spielt. Er ist abgeschlossen, wenn für alle Datenbanken von Instanz A das Seeding und die Synchronisierung durchgeführt wurde. An diesem Punkt werden Anmeldeversuche nicht mehr fehlschlagen.
5. Löschen Sie Instanz A, wenn sie nicht benötigt wird, um unnötige Kosten zu vermeiden.

> [!CAUTION]
> Nach Schritt 3 und bis zum Abschluss von Schritt 4 sind die Datenbanken auf Instanz A vor dem Ausfall von Instanz A aufgrund eines Katastrophenfalls ungeschützt.

> [!IMPORTANT]
> Wenn die Failovergruppe gelöscht wird, werden auch die DNS-Einträge für die Listenerendpunkte gelöscht. An diesem Punkt ist nicht völlig ausgeschlossen, dass eine andere Person eine Failovergruppe mit dem gleichen Namen erstellt. Da Failovergruppennamen global eindeutig sein müssen, wird verhindert, dass Sie denselben Namen noch mal verwenden. Verwenden Sie keine generischen Namen für Failovergruppen um dieses Risiko zu minimieren.

### <a name="enable-scenarios-dependent-on-objects-from-the-system-databases"></a>Aktivieren von Szenarien in Abhängigkeit von Objekten in den Systemdatenbanken

Systemdatenbanken werden **nicht** in die sekundäre Instanz in einer Failovergruppe repliziert. Um Szenarien zu ermöglichen, in denen Objekte aus den Systemdatenbanken erforderlich sind, stellen Sie sicher, dass diese Objekte in der sekundären Instanz erstellt werden, und synchronisieren Sie sie mit der primären Instanz. 

Wenn Sie beispielsweise in der sekundären Instanz die gleichen Anmeldungen nutzen möchten, stellen Sie sicher, dass sie mit identischer SID erstellt werden. 

```SQL
-- Code to create login on the secondary instance
CREATE LOGIN foo WITH PASSWORD = '<enterStrongPasswordHere>', SID = <login_sid>;
``` 
### <a name="synchronize-instance-properties-and-retention-policies-between-primary-and-secondary-instance"></a>Synchronisieren von Instanzeigenschaften und Aufbewahrungsrichtlinien zwischen primärer und sekundärer Instanz

Instanzen in einer Failovergruppe bleiben separate Azure-Ressourcen, und Änderungen an der Konfiguration der primären Instanz werden nicht automatisch in die sekundäre Instanz repliziert. Stellen Sie sicher, dass Sie alle relevanten Änderungen in der primären _und_ der sekundären Instanz vornehmen. Wenn Sie beispielsweise die Sicherungsspeicherredundanz oder die Richtlinie für die langfristige Sicherungsaufbewahrung für die primäre Instanz ändern, stellen Sie sicher, dass Sie sie auch in der sekundären Instanz ändern.

## <a name="failover-groups-and-network-security"></a>Failovergruppen und Netzwerksicherheit

Für einige Anwendungen erfordern die Sicherheitsregeln, dass der Netzwerkzugriff auf die Datenebene auf eine bestimmte Komponente oder Komponenten wie eine VM, einen Webdienst usw. beschränkt ist. Diese Anforderung stellt einige Herausforderungen für den Entwurf der Geschäftskontinuität und die Verwendung von Failovergruppen dar. Berücksichtigen Sie bei der Implementierung eines solchen eingeschränkten Zugriffs die folgenden Optionen.

### <a name="use-failover-groups-and-virtual-network-service-endpoints"></a><a name="using-failover-groups-and-virtual-network-rules"></a> Verwenden von Failovergruppen und VNET-Dienstendpunkten

Wenn Sie [VNET-Dienstendpunkte und -Regeln](vnet-service-endpoint-rule-overview.md) verwenden, um den Zugriff auf Ihre Datenbank in SQL-Datenbank oder SQL Managed Instance einzuschränken, denken Sie daran, dass jeder VNET-Dienstendpunkt nur für eine einzige Azure-Region gilt. Der Endpunkt ermöglicht anderen Regionen nicht das Akzeptieren von Nachrichten aus dem Subnetz. Aus diesem Grund können nur die Clientanwendungen, die in der gleichen Region bereitgestellt werden, eine Verbindung mit der primären Datenbank herstellen. Da ein Geofailover dazu führt, dass die SQL-Datenbank-Clientsitzungen zu dem Server in der anderen (sekundären) Region umgeleitet werden, treten bei diesen Sitzungen Fehler auf, wenn sie von Clients ausgehen, die sich außerhalb dieser Region befinden. Aus diesem Grund kann die Richtlinie für automatisches Failover nicht aktiviert werden, wenn die beteiligten Server oder Instanzen in den VNET-Regeln enthalten sind. Um das manuelle Failover zu unterstützen, gehen Sie folgendermaßen vor:

1. Stellen Sie die redundanten Kopien der Front-End-Komponenten der Anwendung (Webdienst, VMs usw.) in der sekundären Region bereit.
2. Konfigurieren Sie die [VNET-Regeln](vnet-service-endpoint-rule-overview.md) einzeln für den primären und sekundären Server.
3. Aktivieren Sie das [Front-End-Failover durch eine Traffic Manager-Konfiguration](designing-cloud-solutions-for-disaster-recovery.md#scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime).
4. Initiieren Sie ein manuelles Geofailover, wenn der Ausfall erkannt wird. Diese Option ist für die Anwendungen optimiert, die konsistente Latenz zwischen Front-End und Datenebene erfordern, und unterstützt die Wiederherstellung, wenn Front-End, Datenebene oder beide vom Ausfall betroffen sind.

> [!NOTE]
> Stellen Sie bei Verwendung des **schreibgeschützten Listeners** für den Lastenausgleich einer schreibgeschützten Workload sicher, dass diese Workload auf einem virtuellen Computer oder einer anderen Ressource in der sekundären Region ausgeführt wird, damit sie eine Verbindung mit der sekundären Datenbank herstellen kann.

### <a name="use-failover-groups-and-firewall-rules"></a>Verwenden von Failovergruppen und Firewallregeln

Wenn Ihr Geschäftskontinuitätsplan das Durchführen eines Failovers mithilfe von Gruppen mit automatischem Failover erfordert, können Sie den Zugriff auf Ihre Datenbank in SQL-Datenbank mithilfe der öffentlichen IP-Firewallregeln einschränken. Um das automatische Failover zu unterstützen, gehen Sie folgendermaßen vor:

1. [Erstellen Sie eine öffentliche IP-Adresse.](../../virtual-network/ip-services/virtual-network-public-ip-address.md#create-a-public-ip-address)
2. [Erstellen Sie einen öffentlichen Lastenausgleich](../../load-balancer/quickstart-load-balancer-standard-public-portal.md), und weisen Sie ihm die öffentliche IP-Adresse zu.
3. [Erstellen Sie ein virtuelles Netzwerk und die VMs](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) für Ihre Front-End-Komponenten.
4. [Erstellen Sie eine Netzwerksicherheitsgruppe](../../virtual-network/network-security-groups-overview.md), und konfigurieren Sie eingehende Verbindungen.
5. Stellen Sie mithilfe eines `Sql.<Region>`-[Diensttags](../../virtual-network/network-security-groups-overview.md#service-tags) sicher, dass die ausgehenden Verbindungen für Azure SQL-Datenbank in einer Region geöffnet sind.
6. Erstellen Sie eine [SQL-Datenbank-Firewallregel](firewall-configure.md), um eingehenden Datenverkehr von der öffentlichen IP-Adresse, die Sie in Schritt 1 erstellt haben, zuzulassen.

Weitere Informationen zur Konfiguration des ausgehenden Zugriffs und IP-Adresse, die in den Firewallregeln verwendet werden soll, finden Sie unter [Ausgehende Verbindungen in Azure](../../load-balancer/load-balancer-outbound-connections.md).

Die oben genannte Konfiguration sorgt dafür, dass ein automatisches Geofailover keine Verbindungen von den Front-End-Komponenten blockiert, und setzt voraus, dass die Anwendung die längeren Wartezeiten zwischen dem Front-End und der Datenebene tolerieren kann.

> [!IMPORTANT]
> Damit die Geschäftskontinuität bei regionalen Ausfällen gewährleistet ist, müssen Sie die geografische Redundanz sowohl für die Front-End-Komponenten als auch die Datenbanken sicherstellen.

## <a name="enabling-geo-replication-between-managed-instance-virtual-networks"></a><a name="enabling-geo-replication-between-managed-instances-and-their-vnets"></a> Aktivieren der Georeplikation zwischen virtuellen Netzwerken der verwalteten Instanz

Wenn Sie eine Failovergruppe zwischen primären und sekundären SQL Managed Instances in zwei verschiedenen Regionen einrichten, wird jede Instanz mit einem unabhängigen virtuellen Netzwerk isoliert. Um Replikationsdatenverkehr zwischen diesen VNETs zuzulassen, müssen diese Voraussetzungen erfüllt sein:

- Die beiden Instanzen von SQL Managed Instance müssen sich in verschiedenen Azure-Regionen befinden.
- Die beiden Instanzen von SQL Managed Instance müssen dieselbe Dienstebene und Speichergröße aufweisen.
- Ihre sekundäre Instanz von SQL Managed Instances muss leer sein (ohne Benutzerdatenbanken).
- Die von den Instanzen von SQL Managed Instance verwendeten virtuellen Netzwerke müssen per [VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md) oder [ExpressRoute](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md) verbunden werden. Wenn zwei virtuelle Netzwerke über ein lokales Netzwerk verbunden sind, müssen Sie sicherstellen, dass die Ports 5022 und 11000-11999 nicht durch Firewallregeln blockiert werden. Globales VNET-Peering wird unterstützt. Die einzige Einschränkung ist im folgenden Hinweis beschrieben.

   > [!IMPORTANT]
   > [Am 22.09.2020 wurde globales Peering virtueller Netzwerke für neu erstellte virtuelle Cluster angekündigt](https://azure.microsoft.com/updates/global-virtual-network-peering-support-for-azure-sql-managed-instance-now-available/). Dies bedeutet, dass globales Peering virtueller Netzwerke sowohl für SQL Managed Instances, die nach dem Ankündigungsdatum in leeren Subnetzen erstellt wurden, als auch für alle späteren verwalteten Instanzen, die in diesen Subnetzen erstellt werden, unterstützt wird. Für alle anderen SQL Managed Instances ist die Peeringunterstützung aufgrund der [Einschränkungen beim globalen Peering virtueller Netzwerke](../../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints) auf die Netzwerke in derselben Region beschränkt. Ausführliche Informationen finden Sie im entsprechenden Abschnitt des Artikels [Azure Virtual Network – häufig gestellte Fragen](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers). Um globales Peering virtueller Netzwerke für verwaltete SQL-Instanzen aus virtuellen Clustern verwenden zu können, die vor dem Ankündigungsdatum erstellt wurden, sollten Sie das nicht standardmäßige [Wartungsfenster](./maintenance-window.md) für die Instanzen konfigurieren, da die Instanzen in neue virtuelle Cluster verschoben werden, die globales Peering virtueller Netzwerke unterstützen.

- Die VNETs der beiden SQL Managed Instances dürfen keine überlappenden IP-Adressen aufweisen.
- Sie müssen die Netzwerksicherheitsgruppen (NSGs) so einrichten, dass die Ports 5022 und der Bereich von 11.000 bis 12.000 für ein- und ausgehende Verbindungen vom Subnetz der jeweils anderen verwalteten Instanz geöffnet sind. Hierdurch wird der Replikationsdatenverkehr zwischen den Instanzen zugelassen.

   > [!IMPORTANT]
   > Falsch konfigurierte NSG-Sicherheitsregeln führen zu hängenden Datenbank-Seedingvorgängen.

- Die sekundäre SQL Managed Instance ist mit der richtigen DNS-Zonen-ID konfiguriert. Die DNS-Zone ist eine Eigenschaft einer SQL Managed Instance und eines zugrunde liegenden virtuellen Clusters, und ihre ID ist in der Hostnamenadresse enthalten. Die Zonen-ID wird als zufällige Zeichenfolge generiert, wenn die erste SQL Managed Instance in jedem VNET erstellt und die gleiche ID alle anderen Instanzen im selben Subnetz zugewiesen wird. Nach der Zuweisung kann die DNS-Zone nicht geändert werden. SQL Managed Instances in der gleichen Failovergruppe müssen die DNS-Zone gemeinsam nutzen. Sie erreichen dies durch Übergeben der Zonen-ID der primären Instanz als Wert des DnsZonePartner-Parameters beim Erstellen der sekundären Instanz.

   > [!NOTE]
   > Ein ausführliches Tutorial zum Konfigurieren von Failovergruppen mit einer SQL Managed Instance finden Sie unter [Hinzufügen einer SQL Managed Instance zu einer Failovergruppe](../managed-instance/failover-group-add-instance-tutorial.md).

## <a name="scale-primary-database"></a><a name="upgrading-or-downgrading-primary-database"></a> Skalieren der primären Datenbank

Sie können die primäre Datenbank auf eine andere Computegröße (innerhalb derselben Dienstebene) hoch- oder herunterskalieren, ohne die Verbindung zu sekundären Geodatenbanken zu trennen. Beim Hochskalieren wird empfohlen, zuerst die sekundäre Geodatenbank und dann die primäre Datenbank hochzuskalieren. Beim Herunterskalieren gehen Sie in umgekehrter Reihenfolge vor: Skalieren Sie zuerst die primäre Datenbank herunter, dann die sekundäre Datenbank. Wenn Sie eine Datenbank auf eine andere Dienstebene skalieren, wird diese Empfehlung erzwungen.

Diese Reihenfolge wird besonders zur Vermeidung des Überladens der sekundären Geodatenbank auf einer niedrigeren SKU empfohlen, damit nicht während des Upgrade- oder Downgradevorgangs ein erneutes Seeding durchgeführt werden muss. Sie können dieses Problem auch vermeiden, indem Sie die primäre Datenbank mit Schreibschutz versehen. Das geht allerdings zu Lasten aller Lese-/Schreibworkloads für die primäre Datenbank.

> [!NOTE]
> Wenn Sie im Rahmen der Konfiguration der Failovergruppe eine sekundäre Geodatenbank erstellt haben, wird davon abgeraten, die sekundäre Geodatenbank herunterzuskalieren. Damit soll sichergestellt werden, dass Ihre Datenebene über genügend Kapazität verfügt, um die reguläre Arbeitslast nach einem Geofailover zu verarbeiten.

## <a name="prevent-loss-of-critical-data"></a><a name="preventing-the-loss-of-critical-data"></a> Verhindern des Verlusts kritischer Daten

Aufgrund der hohen Latenzzeit von WANs verwendet die Georeplikation einen asynchronen Replikationsmechanismus. Bei der asynchronen Replikation ist die Möglichkeit eines Datenverlusts unvermeidbar, wenn die primäre Datenbank ausfällt. Zum Schutz kritischer Transaktionen vor Datenverlust, kann ein Anwendungsentwickler die gespeicherte Prozedur [sp_wait_for_database_copy_sync](/sql/relational-databases/system-stored-procedures/active-geo-replication-sp-wait-for-database-copy-sync) unmittelbar nach dem Commit der Transaktion aufrufen. Der Aufruf von `sp_wait_for_database_copy_sync` blockiert den aufrufenden Thread, bis die letzte committete Transaktion übertragen und im Transaktionsprotokoll der sekundären Datenbank gehärtet wurde. Er wartet jedoch nicht darauf, dass die übertragenen Transaktionen in der sekundären Datenbank wiedergegeben (wiederholt) werden. `sp_wait_for_database_copy_sync` ist auf eine bestimmte Georeplikationslink beschränkt. Jeder Benutzer mit den Rechten zum Herstellen der Verbindung mit der primären Datenbank kann diese Prozedur aufrufen.

> [!NOTE]
> `sp_wait_for_database_copy_sync` verhindert Datenverluste nach einem Geofailover für bestimmte Transaktionen, garantiert aber keine vollständige Synchronisierung für Lesezugriffe. Die durch einen `sp_wait_for_database_copy_sync`-Prozeduraufruf verursachte Verzögerung kann beträchtlich sein und hängt von der Größe des noch nicht übertragenen Transaktionsprotokolls auf der Primärseite zum Zeitpunkt des Aufrufs ab.

## <a name="failover-groups-and-point-in-time-restore"></a>Failovergruppen und Point-in-Time-Wiederherstellung

Informationen zur Verwendung der Point-in-Time-Wiederherstellung mit Failovergruppen finden Sie unter [Point-in-Time-Wiederherstellung](recovery-using-backups.md#point-in-time-restore).

## <a name="limitations-of-failover-groups"></a>Einschränkungen von Failovergruppen

Bedenken Sie dabei folgende Einschränkungen:

- Failovergruppen können nicht zwischen zwei Servern oder Instanzen in derselben Azure-Region erstellt werden.
- Failovergruppen können nicht umbenannt werden. Sie müssen die Gruppe löschen und unter einem anderen Namen neu erstellen.
- Die Datenbankumbenennung wird für Datenbanken in der Failovergruppe nicht unterstützt. Sie müssen die Failovergruppe vorübergehend löschen, um eine Datenbank umbenennen oder die Datenbank aus der Failovergruppe entfernen zu können.
- Systemdatenbanken werden nicht in die sekundäre Instanz in einer Failovergruppe repliziert. Daher erfordern Szenarien, die von Objekten in den Systemdatenbanken abhängen, dass die Objekte manuell auf den sekundären Instanzen erstellt und auch manuell synchron gehalten werden, nachdem Änderungen an der primären Instanz vorgenommen wurden. Die einzige Ausnahme ist der Diensthauptschlüssel (Service Master Key, SMK) für SQL Managed Instance, der während der Erstellung der Failovergruppe automatisch auf die sekundäre Instanz repliziert wird. Alle nachfolgenden Änderungen des SMK auf der primären Instanz werden jedoch nicht auf die sekundäre Instanz repliziert.
- Failovergruppen können nicht zwischen Instanzen erstellt werden, wenn sich eine von ihnen in einem Instanzpool befindet.

## <a name="programmatically-manage-failover-groups"></a><a name="programmatically-managing-failover-groups"></a> Programmgesteuertes Verwalten von Failovergruppen

Wie bereits zuvor erwähnt, können Gruppen für automatisches Failover auch programmgesteuert mit Azure PowerShell, Azure CLI und der REST-API verwaltet werden. Die folgenden Tabellen beschreiben den verfügbaren Satz von Befehlen. Die aktive Georeplikation umfasst eine Reihe von Azure Resource Manager-APIs für die Verwaltung. Hierzu zählen unter anderem die [Azure SQL-Datenbank-REST-API](/rest/api/sql/) und [Azure PowerShell-Cmdlets](/powershell/azure/). Diese APIs erfordern die Verwendung von Ressourcengruppen und unterstützen die rollenbasierte Zugriffssteuerung (Azure RBAC) in Azure. Weitere Informationen zur Implementierung von Zugriffsrollen finden Sie unter [Rollenbasierte Zugriffssteuerung in Azure (Azure RBAC)](../../role-based-access-control/overview.md).

### <a name="manage-sql-database-geo-failover"></a><a name="manage-sql-database-failover"></a> Verwalten des Geofailovers von SQL-Datenbank

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

| Cmdlet | BESCHREIBUNG |
| --- | --- |
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) |Dieser Befehl erstellt eine Failovergruppe und registriert sie auf primären und sekundären Servern.|
| [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup) | Entfernt eine Failovergruppe vom Server. |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Ruft die Konfiguration einer Failovergruppe ab. |
| [Set-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/set-azsqldatabasefailovergroup) |Ändert die Konfiguration einer Failovergruppe. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) | Löst das Failover einer Failovergruppe auf den sekundären Server aus. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup)|Fügt einer Failovergruppe eine oder mehrere Datenbanken hinzu.|

# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

| Get-Help | BESCHREIBUNG |
| --- | --- |
| [az sql failover-group create](/cli/azure/sql/failover-group#az_sql_failover_group_create) |Dieser Befehl erstellt eine Failovergruppe und registriert sie auf primären und sekundären Servern.|
| [az sql failover-group delete](/cli/azure/sql/failover-group#az_sql_failover_group_delete) | Entfernt eine Failovergruppe vom Server. |
| [az sql failover-group show](/cli/azure/sql/failover-group#az_sql_failover_group_show) | Ruft die Konfiguration einer Failovergruppe ab. |
| [az sql failover-group update](/cli/azure/sql/failover-group#az_sql_failover_group_update) |Ändert die Konfiguration einer Failovergruppe bzw. fügt einer Failovergruppe mindestens eine Datenbank hinzu.|
| [az sql failover-group set-primary](/cli/azure/sql/failover-group#az_sql_failover_group_set_primary) | Löst das Failover einer Failovergruppe auf den sekundären Server aus. |

# <a name="rest-api"></a>[REST-API](#tab/rest-api)

| API | BESCHREIBUNG |
| --- | --- |
| [Create or Update Failover Group](/rest/api/sql/failovergroups/createorupdate) | Erstellt oder aktualisiert eine Failovergruppe. |
| [Delete Failover Group](/rest/api/sql/failovergroups/delete) | Entfernt eine Failovergruppe vom Server. |
| [Failover (Planned)](/rest/api/sql/failovergroups/failover) | Löst das Failover vom aktuellen primären Server auf den sekundären Server mit vollständiger Datensynchronisierung aus.|
| [Force Failover Allow Data Loss](/rest/api/sql/failovergroups/forcefailoverallowdataloss) | Löst das Failover vom aktuellen primären Server auf den sekundären Server ohne Datensynchronisierung aus. Dieser Vorgang kann zu Datenverlust führen. |
| [Get Failover Group](/rest/api/sql/failovergroups/get) | Ruft die Konfiguration einer Failovergruppe ab. |
| [List Failover Groups By Server](/rest/api/sql/failovergroups/listbyserver) | Listet die Failovergruppen eines Servers auf. |
| [Update Failover Group](/rest/api/sql/failovergroups/update) | Aktualisiert die Konfiguration einer Failovergruppe. |

---

### <a name="manage-sql-managed-instance-geo-failover"></a><a name="manage-sql-managed-instance-failover"></a> Verwalten des Geofailovers von SQL Managed Instance

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

| Cmdlet | BESCHREIBUNG |
| --- | --- |
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup) |Dieser Befehl erstellt eine Failovergruppe und registriert sie auf primären und sekundären Instanzen.|
| [Set-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/set-azsqldatabaseinstancefailovergroup) |Ändert die Konfiguration einer Failovergruppe.|
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) |Ruft die Konfiguration einer Failovergruppe ab.|
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) |Löst das Failover einer Failovergruppe auf der sekundären Instanz aus.|
| [Remove-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/remove-azsqldatabaseinstancefailovergroup) | Entfernt eine Failovergruppe.|


# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

| Get-Help | BESCHREIBUNG |
| --- | --- |
| [az sql failover-group create](/cli/azure/sql/failover-group#az_sql_failover_group_create) |Dieser Befehl erstellt eine Failovergruppe und registriert sie auf primären und sekundären Servern.|
| [az sql failover-group delete](/cli/azure/sql/failover-group#az_sql_failover_group_delete) | Entfernt eine Failovergruppe vom Server. |
| [az sql failover-group show](/cli/azure/sql/failover-group#az_sql_failover_group_show) | Ruft die Konfiguration einer Failovergruppe ab. |
| [az sql failover-group update](/cli/azure/sql/failover-group#az_sql_failover_group_update) |Ändert die Konfiguration einer Failovergruppe bzw. fügt einer Failovergruppe mindestens eine Datenbank hinzu.|
| [az sql failover-group set-primary](/cli/azure/sql/failover-group#az_sql_failover_group_set_primary) | Löst das Failover einer Failovergruppe auf den sekundären Server aus. |

# <a name="rest-api"></a>[REST-API](#tab/rest-api)

| API | BESCHREIBUNG |
| --- | --- |
| [Create or Update Failover Group](/rest/api/sql/instancefailovergroups/createorupdate) | Erstellt oder aktualisiert die Konfiguration einer Failovergruppe. |
| [Delete Failover Group](/rest/api/sql/instancefailovergroups/delete) | Entfernt eine Failovergruppe von der Instanz. |
| [Failover (Planned)](/rest/api/sql/instancefailovergroups/failover) | Löst ein Failover von der aktuellen primären Instanz auf diese Instanz mit vollständiger Datensynchronisierung aus. |
| [Force Failover Allow Data Loss](/rest/api/sql/instancefailovergroups/forcefailoverallowdataloss) | Löst das Failover von der aktuellen primären Instanz auf die sekundäre Instanz ohne Datensynchronisierung aus. Dieser Vorgang kann zu Datenverlust führen. |
| [Get Failover Group](/rest/api/sql/instancefailovergroups/get) | Ruft die Konfiguration einer Failovergruppe ab. |
| [List Failover Groups - List By Location](/rest/api/sql/instancefailovergroups/listbylocation) | Listet die Failovergruppen an einem Standort auf. |

---

## <a name="next-steps"></a>Nächste Schritte

- Ausführliche Tutorials finden Sie unter:
  - [Hinzufügen von SQL-Datenbank zu einer Failovergruppe](failover-group-add-single-database-tutorial.md)
  - [Hinzufügen eines Pools für elastische Datenbanken zu einer Failovergruppe](failover-group-add-elastic-pool-tutorial.md)
  - [Hinzufügen einer SQL Managed Instance zu einer Failovergruppe](../managed-instance/failover-group-add-instance-tutorial.md)
- Beispielskripts:
  - [Verwenden von PowerShell zum Konfigurieren der aktiven Georeplikation für Azure SQL-Datenbank](scripts/setup-geodr-and-failover-database-powershell.md)
  - [Verwenden von PowerShell zum Konfigurieren der aktiven Georeplikation für eine Pooldatenbank in Azure SQL-Datenbank](scripts/setup-geodr-and-failover-elastic-pool-powershell.md)
  - [Hinzufügen einer Azure SQL-Datenbank-Instanz zu einer Failovergruppe mithilfe von PowerShell](scripts/add-database-to-failover-group-powershell.md)
- Eine Übersicht und verschiedene Szenarien zum Thema Geschäftskontinuität finden Sie unter [Übersicht über die Geschäftskontinuität](business-continuity-high-availability-disaster-recover-hadr-overview.md)
- Informationen über automatisierte Sicherungen von Azure SQL-Datenbanken finden Sie unter [Automatisierte SQL-Datenbanksicherungen](automated-backups-overview.md).
- Informationen zum Verwenden automatisierter Sicherungen für die Wiederherstellung finden Sie unter [Wiederherstellen einer Datenbank aus vom Dienst initiierten Sicherungen](recovery-using-backups.md).
- Weitere Informationen zu Authentifizierungsanforderungen für einen neuen primären Server und die Datenbank finden Sie unter [Verwalten der Sicherheit der Azure SQL-Datenbank nach der Notfallwiederherstellung](active-geo-replication-security-configure.md).
