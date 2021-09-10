---
title: 'Migrieren lokaler MySQL-Instanzen zu Azure Database for MySQL: Bewertung'
description: Bevor Sie direkt in die Migration einer MySQL-Workload einsteigen, müssen einige Due-Diligence-Maßnahmen getroffen werden.
ms.service: mysql
ms.subservice: migration-guide
ms.topic: how-to
author: arunkumarthiags
ms.author: arthiaga
ms.reviewer: maghan
ms.custom: ''
ms.date: 06/21/2021
ms.openlocfilehash: 4510cbe04181da7badb10c61bd510bed084580e8
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/16/2021
ms.locfileid: "114284259"
---
# <a name="migrate-mysql-on-premises-to-azure-database-for-mysql-assessment"></a>Migrieren lokaler MySQL-Instanzen zu Azure Database for MySQL: Bewertung

[!INCLUDE[applies-to-mysql-single-flexible-server](../../includes/applies-to-mysql-single-flexible-server.md)]

## <a name="prerequisites"></a>Voraussetzungen

[Repräsentativer Anwendungsfall](02-representative-use-case.md)

## <a name="overview"></a>Übersicht

Bevor Sie direkt in die Migration einer MySQL-Workload einsteigen, müssen einige Due-Diligence-Maßnahmen getroffen werden. Dazu gehört die Analyse der Daten, der Hostingumgebung und der Anwendungsworkloads, um zu überprüfen, ob die Azure-Zielzone ordnungsgemäß konfiguriert wurde und zum Hosten der in Kürze zu migrierenden Workloads vorbereitet ist.

## <a name="limitations"></a>Einschränkungen

Azure Database for MySQL ist eine voll unterstützte Version der MySQL Community Edition, die als Platform-as-a-Service ausgeführt wird. Es gibt jedoch [einige Einschränkungen](../../concepts-limits.md), mit denen Sie sich bei der ersten Bewertung vertraut machen müssen.

Zu den wichtigsten davon gehören:

  - Speicher-Engine-Unterstützung nur für `InnoDB` und `Memory`

  - Eingeschränkte `Privilege`-Unterstützung (`DBA`, `SUPER`, `DEFINER`)

  - Deaktivierte Datenmanipulationsanweisungen (`SELECT ... INTO OUTFILE`, `LOAD DATA INFILE`)

  - Automatische Migration wichtiger Datenbanken (5.6 zu 5.7, 5.7 zu 8.0)

  - Bei Verwendung von [benutzerdefinierten MySQL Server-Funktionen (User-Defined Functions, UDFs)](https://dev.mysql.com/doc/refman/5.7/en/server-udfs.html) ist die einzige sinnvolle Hostingoption Azure Hosted VMs, da es keine Möglichkeit zum Hochladen der `so`- oder `dll`-Komponente in Azure Database for MySQL gibt.

Viele der anderen Elemente sind operative Aspekte, mit denen sich Administratoren im Rahmen der Lebenszyklusverwaltung für operative Datenworkloads vertraut machen sollten. In diesem Leitfaden werden viele dieser operativen Aspekte im Abschnitt „Verwaltung nach der Migration“ beschrieben.

## <a name="mysql-versions"></a>MySQL-Versionen

MySQL hat eine reiche Geschichte, die 1995 beginnt. Seitdem hat es sich zu einem weit verbreiteten Datenbank-Managementsystem entwickelt. Azure Database for MySQL begann mit der Unterstützung von MySQL Version 5.6, gefolgt von 5.7 und seit kurzem 8.0. Aktuelle Informationen zur Unterstützung der Azure Database for MySQL-Version finden Sie unter [Unterstützte Azure Database for MySQL-Serverversionen](../../concepts-supported-versions.md). Im Abschnitt „Verwaltung nach der Migration“ wird erläutert, wie Upgrades (z. B. 5.7.20 auf 5.7.21) auf die MySQL-Instanzen in Azure angewendet werden.

> [!NOTE]
> Der Sprung von 5.x auf 8.0 erfolgt größtenteils aufgrund der Übernahme von MySQL durch Oracle. Weitere Informationen zur Geschichte von MySQL finden Sie auf der [Wiki-Seite zu MySQL](https://en.wikipedia.org/wiki/MySQL).

Es ist wichtig, die MySQL-Quellversion zu kennen. Die Anwendungen, die das System verwenden, nutzen möglicherweise Datenbankobjekte und -features, die für diese Version spezifisch sind. Die Migration einer Datenbank zu einer niedrigeren Version kann zu Kompatibilitätsproblemen und Funktionsverlusten führen. Es wird außerdem empfohlen, die Daten und die Anwendungsinstanz vor der Migration zu einer neueren Version gründlich zu testen, da die eingeführten Features Ihre Anwendung unterbrechen könnten.

Beispiele, die den Migrationspfad und die Version beeinflussen können:

  - 5.6: TIMESTAMP-Spalte für das richtige Speichern von Millisekunden und Volltextsuche

  - 5.7: Unterstützung für den nativen JSON-Datentyp

  - 8.0: Unterstützung für NoSQL-Dokumentspeicher, atomische und absturzsichere DDL- und JSON-Tabellenfunktionen

    > [!NOTE]
    > Die allgemeine Unterstützung für MySQL 5.6 wird im Februar 2021 eingestellt. MySQL-Workloads müssen zu MySQL Version 5.7 oder höher migriert werden.

So überprüfen Sie die MySQL-Serverversion:

```
SHOW VARIABLES LIKE "%version%";
```

### <a name="database-storage-engines"></a>Datenbankspeicher-Engines

Azure Database for MySQL unterstützt nur [InnoDB](https://dev.mysql.com/doc/refman/8.0/en/innodb-storage-engine.html)- und [Memory](https://dev.mysql.com/doc/refman/8.0/en/memory-storage-engine.html)-Datenbankspeicher-Engines. Andere Speicher-Engines wie [MyISAM ](https://dev.mysql.com/doc/refman/8.0/en/myisam-storage-engine.html) müssen zu einer unterstützten Speicher-Engine migriert werden. Die Unterschiede zwischen MyISAM und InnoDB sind die operativen Features und die Leistungsausgabe. Die Tabellen auf höherer Ebene und die Schemastruktur ändern sich in der Regel nicht zwischen den Engines, aber die Index- und Tabellenspaltentypen können sich aus Speicher- und Leistungsgründen ändern. Obwohl InnoDB für große Datendateigrößen bekannt ist, werden diese Speicherdetails vom Azure Database for MySQL-Dienst verwaltet.

#### <a name="migrating-from-myisam-to-innodb"></a>Migrieren von MyISAM zu InnoDB

Die MyISAM-Datenbank und die Tabellen müssen in InnoDB-Tabellen konvertiert werden. Nach der Konvertierung sollten Anwendungen auf Kompatibilität und Leistung getestet werden. In den meisten Fällen machen es die Tests erforderlich, die Tabelle neu zu erstellen und die Zielspeicher-Engine über DDL-Anweisungen zu ändern. Es ist unwahrscheinlich, dass diese Änderung während der Migration durchgeführt werden muss, da sie während der Schemaerstellung im Azure-Ziel erfolgt. Weitere Informationen finden Sie in der [Dokumentation zum Konvertieren von Tabellen](https://dev.mysql.com/doc/refman/5.6/en/converting-tables-to-innodb.html) auf der MySQL-Website.

Verwenden Sie die folgende Abfrage, um nützliche Tabelleninformationen zu finden:

```dotnetcli
    SELECT 
        tab.table_schema,
        tab.table_name,
        tab.engine as engine_type,
        tab.auto_increment,
        tab.table_rows,
        tab.create_time,
        tab.update_time,
        tco.constraint_type
    FROM information_schema.tables tab
    LEFT JOIN information_schema.table_constraints tco
        ON (tab.table_schema = tco.table_schema
            AND tab.table_name = tco.table_name
            )
    WHERE  
        tab.table_schema NOT IN ('mysql', 'information_schema', 'performance_
schema', 'sys')  
        AND tab.table_type = 'BASE TABLE';
```

> [!NOTE]
> Die Ausführung einer Abfrage für INFORMATION\_SCHEMA über mehrere Datenbanken hinweg kann sich auf die Leistung auswirken. Führen Sie sie in Zeiten mit geringer Nutzung aus.

Zum Konvertieren einer Tabelle von MyISAM in InnoDB führen Sie Folgendes aus:

```
ALTER TABLE {table\_name} ENGINE=InnoDB;
```

> [!NOTE]
> Dieser Konvertierungsansatz bewirkt, dass die Tabelle gesperrt wird. Alle Anwendungen können warten, bis der Vorgang abgeschlossen ist. Durch das Sperren der Tabelle wird die Datenbank für einen kurzen Zeitraum offline angezeigt.

Stattdessen kann die Tabelle mit der gleichen Struktur, aber mit einer anderen Speicher-Engine erstellt werden. Kopieren Sie die Zeilen nach dem Erstellen in die neue Tabelle, nachdem sie erstellt wurde:

```
INSERT INTO {table\_name} SELECT * FROM {myisam\_table} ORDER BY {primary\_key\_columns}
```

> [!NOTE]
> Damit dieser Ansatz erfolgreich ist, muss die ursprüngliche Tabelle gelöscht und die neue Tabelle umbenannt werden. Durch diese Aufgabe wird eine kurze Downtime verursacht.

### <a name="database-data-and-objects"></a>Datenbankdaten und -objekte

Daten sind eine Komponente der Datenbankmigration. Die datenbankunterstützenden Objekte müssen migriert und überprüft werden, um sicherzustellen, dass die Anwendungen weiterhin zuverlässig ausgeführt werden.

Im Folgenden finden Sie eine Liste der Elemente, die Sie vor und nach der Migration inventarisieren sollten:

  - Tabellen (Schema)

  - Primärschlüssel, Fremdschlüssel

  - Indizes

  - Funktionen

  - Prozeduren

  - Auslöser

  - Ansichten

### <a name="user-defined-functions"></a>Benutzerdefinierte Funktionen

MySQL ermöglicht die Verwendung von Funktionen, die externen Code aufrufen. Leider können Datenworkloads, die benutzerdefinierte Funktionen (User-Defined Functions, UDFs) mit externem Code verwenden, nicht zu Azure Database for MySQL migriert werden. Der Grund dafür ist, dass der erforderliche Unterstützungs- oder-DLL-Code der MySQL-Funktion nicht auf den Azure-Server hochgeladen werden kann.

Führen Sie die folgende Abfrage aus, um nach allen UDFs zu suchen, die installiert werden können:

```
SELECT * FROM mysql.func;
```

## <a name="source-systems"></a>Quellsysteme

Der Umfang der Migrationsvorbereitung kann je nach Quellsystem und Standort variieren. Überlegen Sie, wie Sie die Daten zusätzlich zu den Datenbankobjekten aus dem Quellsystem in das Zielsystem übertragen. Das Migrieren von Daten kann schwierig werden, wenn sich Firewalls und andere Netzwerkkomponenten zwischen Quelle und Ziel befinden.

Darüber hinaus kann das Verschieben von Daten über das Internet langsamer sein als die Verwendung dedizierter Verbindungen zu Azure. Wenn Sie viele Gigabytes, Terabytes und Petabytes von Daten verschieben, sollten Sie daher eine [ExpressRoute](../../../expressroute/expressroute-introduction.md)-Verbindung zwischen dem Quellnetzwerk und dem Azure-Netzwerk einrichten.

Wenn ExpressRoute bereits vorhanden ist, wird die Verbindung wahrscheinlich von anderen Anwendungen verwendet. Das Durchführen einer Migration über eine vorhandene Route kann den Netzwerkdurchsatz beeinträchtigen und möglicherweise sowohl für die Migration als auch für andere Anwendungen zu erheblichen Leistungseinbußen führen.

Zu guter Letzt muss der Speicherplatz untersucht werden. Berücksichtigen Sie beim Exportieren einer großen Datenbank die Größe der Daten. Stellen Sie sicher, dass das System, auf dem das Tool ausgeführt wird, und letztendlich der Exportspeicherort über genügend Speicherplatz verfügt, um den Exportvorgang auszuführen.

### <a name="cloud-providers"></a>Cloudanbieter

Für die Migration von Datenbanken von Clouddienstanbietern wie Amazon Web Services (AWS) ist möglicherweise ein zusätzlicher Netzwerkkonfigurationsschritt erforderlich, um auf die in der Cloud gehosteten MySQL-Instanzen zugreifen zu können. Migrationstools wie Data Migration Services erfordern Zugriff von externen IP-Adressbereichen und werden andernfalls möglicherweise blockiert.

### <a name="on-premises"></a>Lokal

Wenn sich die MySQL-Datenworkload hinter Firewalls oder anderen Netzwerksicherheitsebenen befindet, muss genau wie bei Cloudanbietern ein Pfad zwischen der lokalen Instanz und Azure Database for MySQL erstellt werden.

## <a name="tools"></a>Tools

Zum Bewerten der MySQL-Datenworkloads und -Umgebungen können viele Tools und Methoden verwendet werden. Jedes Tool bietet andere Bewertungs- und Migrationsfeatures und -funktionen. Im Rahmen dieses Leitfadens werden die am häufigsten verwendeten Tools zur Bewertung von MySQL-Datenworkloads überprüft.

### <a name="azure-migrate"></a>Azure Migrate

Obwohl [Azure Migrate](../../../migrate/migrate-services-overview.md) die direkte Migration von MySQL-Datenbankworkloads nicht unterstützt, kann es verwendet werden, wenn Administratoren nicht sicher sind, welche Benutzer und Anwendungen die Daten nutzen – unabhängig davon, ob sie auf einem virtuellen oder hardwarebasierten Computer gehostet werden. Eine [Abhängigkeitsanalyse](../../../migrate/concepts-dependency-visualization.md) kann durch Installieren und Ausführen des Überwachungs-Agents auf dem Computer, auf dem die MySQL-Workload ausgeführt wird, durchgeführt werden. Der Agent sammelt die Informationen über einen festgelegten Zeitraum, z. B. einen Monat. Die Abhängigkeitsdaten können analysiert werden, um unbekannte Verbindungen zu finden, die mit der Datenbank hergestellt werden. Anhand der Verbindungsdaten können Anwendungsbesitzer ermittelt werden, die über die anstehende Migration benachrichtigt werden müssen.

Zusätzlich zur Abhängigkeitsanalyse von Anwendungen und Benutzerkonnektivitätsdaten kann Azure Migrate auch zum Analysieren der [Hyper-V-, VMware- oder physischen Server](../../../migrate/migrate-appliance-architecture.md) verwendet werden, um Auslastungsmuster der Datenbankworkloads bereitzustellen und die dafür geeignete Zielumgebung vorzuschlagen.

### <a name="telgraf-for-linux"></a>Telgraf für Linux

Linux-Workloads können [Microsoft Monitoring Agent (MMA)](../../../azure-monitor/agents/agent-linux.md) verwenden, um Daten auf Ihren virtuellen und physischen Computern zu erfassen. Erwägen Sie außerdem die Verwendung des [Telegraf-Agents](../../../azure-monitor/essentials/collect-custom-metrics-linux-telegraf.md) und seiner zahlreichen Plug-Ins, um Ihre Leistungsmetriken zu erfassen.

### <a name="service-tiers"></a>Dienstebenen

Ausgestattet mit den Bewertungsinformationen (CPU, Arbeitsspeicher, Speicher usw.) muss der Migrationsbenutzer sich als Nächstes für einen Azure Database for MySQL-[Nutzungstarif](../../concepts-pricing-tiers.md) entscheiden.

Derzeit stehen drei Tarife zur Auswahl:

  - **Basic**: Für Workloads mit geringen Anforderungen an Compute- und E/A-Leistung

  - **Universell**: Für die meisten geschäftlichen Workloads, die ausgeglichene Compute- und Speicherkapazitäten mit skalierbarem E/A-Durchsatz erfordern

  - **Arbeitsspeicheroptimiert**: Für Hochleistungs-Datenbankworkloads, für die zur schnelleren Transaktionsverarbeitung und Erhöhung der Parallelität In-Memory-Leistung erforderlich ist

Die Tarifentscheidung kann durch die RTO- und RPO-Anforderungen der Datenworkload beeinflusst werden. Wenn die Datenworkload mehr als 4 TB Speicher erfordert, ist ein zusätzlicher Schritt erforderlich. Überprüfen Sie die Speicheranforderungen, und wählen Sie [eine Region aus, die bis zu 16 TB Speicher unterstützt](../../concepts-pricing-tiers.md#storage).

> [!NOTE]
> Für Regionen, die Ihre Speicheranforderungen nicht unterstützen, wenden Sie sich an das MySQL-Team (AskAzureDBforMySQL@service.microsoft.com).

In der Regel konzentriert sich die Entscheidung auf die Speicher- und IOPS-Anforderungen oder die erforderlichen Eingabe-/Ausgabevorgänge pro Sekunde. Daher benötigt das Zielsystem immer mindestens so viel Speicher wie im Quellsystem. Da IOPS 3 GB zugeordnet werden, ist es außerdem wichtig, die IOPS-Anforderungen der endgültigen Speichergröße anzupassen.

| Faktoren | Tarif |
|---------|------|
| **Grundlegend** | Entwicklungscomputer, kein Bedarf an hoher Leistung, weniger als 1 TB Speicher. |
| **Allgemeiner Zweck** | Höhere IOPS-Anforderungen als der Basic-Tarif bieten kann, aber für weniger als 16 TB Speicher und weniger als 4 GB Arbeitsspeicher. |
| **Arbeitsspeicheroptimiert** | Datenworkloads, die den oberen Speicher oder Cache und eine pufferbezogene Serverkonfiguration nutzen, z. B. innodb_buffer_pool_instances mit hoher Parallelität, umfangreiche Blobs und Systeme mit zahlreichen Replikationskopien |

### <a name="costs"></a>Kosten

Nach Auswertung seiner gesamten MySQL-Datenworkloads hat WWI ermittelt, dass mindestens 4 virtuelle Kerne und 20 GB Arbeitsspeicher sowie mindestens 100 GB Speicherplatz mit einer IOP-Kapazität von 450 IOPS benötigt werden. Aufgrund der 450-IOPS-Anforderung muss das Unternehmen wegen der [IOPS-Zuordnungsmethode von Azure Database for MySQL](../../concepts-pricing-tiers.md#storage) 150 GB Speicher zuordnen. Darüber hinaus sind mindestens bis zu 100 % des bereitgestellten Serverspeichers als Sicherungsspeicher sowie ein Lesereplikat erforderlich. Es wird kein ausgehender Datenverkehr von mehr als 5 GB erwartet.

Mithilfe des [Azure Database for MySQL-Preisrechners](https://azure.microsoft.com/pricing/details/mysql/) konnte WWI die Kosten für die Azure Database for MySQL-Instanz ermitteln. Ab September 2020 werden die Gesamtkosten in der folgenden Tabelle für die WWI Conference-Datenbank angezeigt.

| Ressource | BESCHREIBUNG | Menge | Cost |
|----------|-------------|----------|------|
| **Compute (Universell)** | 4 virtuelle Kerne, 20 GB                  | 1 zu 0,351 USD/Std.                                              | 3\.074,76 USD/Jahr |
| **Storage**                   | 5 GB                             | 12 x 150 zu 0,115 USD                                          | 207 USD/Jahr     |
| **Backup**                    | Bis zu 100 % des bereitgestellten Speichers| Keine zusätzlichen Kosten für bis zu 100 % des bereitgestellten Serverspeichers     | 0,00 USD/Jahr    |
| **Lesereplikat**              | 1-Sekunden-Regionsreplikat          | Compute + Speicher                                          | 3\.281,76 USD/Jahr |
| **Network**                   | < 5 GB/Monat ausgehend               | Kostenlos                                                       |               |
| **Gesamt**                     |                                  |                                                            | 6\.563,52 USD/Jahr |

Nach Überprüfung der Anfangskosten hat der CIO von WWI bestätigt, dass Azure wesentlich länger als 3 Jahre genutzt wird. Es wurde beschlossen, 3-Jahres-[Reserveinstanzen](../../concept-reserved-pricing.md) zu verwenden, um zusätzlich \~4.000 USD/Jahr zu sparen.

| Ressource | BESCHREIBUNG | Menge | Cost |
|----------|-------------|----------|------|
| **Compute (Universell)** | 4 virtuelle Kerne                          | 1 zu 0,1375/Std.                                              | 1\.204,5 USD/Jahr |
| **Storage**                   | 5 GB                              | 12 x 150 zu 0,115 USD                                           | 207 USD/Jahr    |
| **Backup**                    | Bis zu 100 % des bereitgestellten Speichers | Keine zusätzlichen Kosten für bis zu 100 % des bereitgestellten Serverspeichers      | 0,00 USD/Jahr   |
| **Network**                   | < 5 GB/Monat ausgehend                | Kostenlos                                                        |              |
| **Lesereplikat**              | 1-Sekunden-Regionsreplikat           | Compute + Speicher                                           | 1\.411,5 USD/Jahr |
| **Gesamt**                     |                                   |                                                             | 2\.823 USD/Jahr   |

Wie die obige Tabelle zeigt, müssen Sicherungen, ausgehender Netzwerkverkehr und alle Lesereplikate in die Gesamtkosten einbezogen werden. Wenn weitere Datenbanken hinzugefügt werden, ist der generierte Speicher- und Netzwerkdatenverkehr der einzige zusätzliche kostenbasierte Faktor, der zu berücksichtigen ist.

> [!NOTE]
> Die oben stehenden Schätzungen enthalten keine [ExpressRoute](../../../expressroute/expressroute-introduction.md)-, [Azure-App-Gateway](../../../application-gateway/overview.md)-, [Azure Load Balancer](../../../load-balancer/load-balancer-overview.md)- oder [App Service](../../../app-service/overview.md)-Kosten für die Anwendungsebenen.
>
> Die oben genannten Preise können sich jederzeit ändern und variieren je nach Region.

### <a name="application-implications"></a>Auswirkungen auf Anwendungen

Beim Wechsel zu Azure Database for MySQL ist die Konvertierung in SSL-basierte Kommunikation (Secure Sockets Layer) wahrscheinlich eine der bedeutendsten Änderungen für Ihre Anwendungen. SSL ist standardmäßig in Azure Database for MySQL aktiviert, und es ist wahrscheinlich, dass die lokale Anwendungs- und Datenworkload nicht für die Verbindung mit MySQL per SSL eingerichtet ist. Wenn diese Option aktiviert ist, wird der Verarbeitungsaufwand durch die Nutzung von SSL zusätzlich erhöht. Dies sollte überwacht werden.

> [!NOTE]
> SSL ist zwar standardmäßig aktiviert, doch haben Sie die Möglichkeit, es zu deaktivieren.

Befolgen Sie die Aktivitäten unter [Konfigurieren von SSL-Verbindungen in der Anwendung für eine sichere Verbindung mit Azure Database for MySQL](../../howto-configure-ssl.md), um die Anwendung neu zu konfigurieren, damit dieser sichere Authentifizierungspfad unterstützt wird.

Ändern Sie zum Schluss den Servernamen in den Anwendungsverbindungszeichenfolgen, oder ändern Sie das DNS so, dass es auf den neuen Azure Database for MySQL-Server verweist.

## <a name="wwi-scenario"></a>WWI-Szenario

Für die Bewertung hat WWI zuerst Informationen zum MySQL-Datenbestand erfasst, wie in der folgenden Tabelle gezeigt.

| Name | `Source` | Datenbank-Engine | Size | IOPS | Version | Besitzer | Ausfallzeit |
|------|--------|-----------|------|------|---------|-------|----------|
| **WwwDB**        | AWS (PaaS)  | InnoDB | 1 GB  | 150 | 5.7 | Marketingabteilung | 1 Std.  |
| **BlogDB**       | AWS (PaaS)  | InnoDB | 1 GB  | 100 | 5.7 | Marketingabteilung | 4 Std. |
| **ConferenceDB** | Lokal | InnoDB | 5 GB  | 50  | 5.5 | Vertriebsabteilung     | 4 Std. |
| **CustomerDB**   | Lokal | InnoDB | 10 GB | 75  | 5.5 | Vertriebsabteilung     | 2 Stunden |
| **SalesDB**      | Lokal | InnoDB | 20 GB | 75  | 5.5 | Vertriebsabteilung     | 1 Std.  |

Jeder Datenbankbesitzer wurde kontaktiert, um die akzeptable Downtimedauer zu ermitteln. Die ausgewählte Planungs- und Migrationsmethode basierte auf der akzeptablen Datenbankdowntime.

In der ersten Phase konzentrierte sich WWI ausschließlich auf die ConferenceDB-Datenbank. Das Team benötigte die bei der Migration gemachten Erkenntnisse für die weiteren Datenworkloadmigrationen. Die ConferenceDB-Datenbank wurde aufgrund der einfachen Datenbankstruktur und der hohen akzeptablen Downtime ausgewählt. Nach Migration der Datenbank konzentrierte sich das Team auf die Migration der Anwendung in die sichere Azure-Zielzone.

## <a name="assessment-checklist"></a>Bewertungsprüfliste

  - Testen, ob die Workload erfolgreich auf dem Zielsystem ausgeführt wird.

  - Stellen Sie sicher, dass die richtigen Netzwerkkomponenten für die Migration verwendet werden.

  - Machen Sie sich mit den Ressourcenanforderungen der Datenworkloads vertraut.

  - Schätzen Sie Gesamtkosten.

  - Ermitteln Sie die Downtimeanforderungen.

  - Bereiten Sie sich auf Anwendungsänderungen vor.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Planung](./04-planning.md)