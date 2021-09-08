---
title: 'Migration einer lokalen MySQL-Instanz zu Azure Database for MySQL: Migrationsmethoden'
description: Für eine erfolgreiche Migration müssen Tools oder Features von MySQL verwendet werden, um die Daten von der Quelle an das Ziel zu übertragen.
ms.service: mysql
ms.subservice: migration-guide
ms.topic: how-to
author: arunkumarthiags
ms.author: arthiaga
ms.reviewer: maghan
ms.custom: ''
ms.date: 06/21/2021
ms.openlocfilehash: 6a4b0e2dc084168e1bffa539924ad897ac93961e
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/30/2021
ms.locfileid: "113085000"
---
# <a name="migrate-mysql-on-premises-to-azure-database-for-mysql-migration-methods"></a>Migration einer lokalen MySQL-Instanz zu Azure Database for MySQL: Migrationsmethoden

[!INCLUDE[applies-to-mysql-single-flexible-server](../../includes/applies-to-mysql-single-flexible-server.md)]

## <a name="prerequisites"></a>Voraussetzungen

[Planung](04-planning.md)

## <a name="overview"></a>Übersicht

Für eine erfolgreiche Migration müssen Tools oder Features von MySQL verwendet werden, um die Daten von der Quelle an das Ziel zu übertragen.

Schließen Sie zunächst unbedingt alle Bewertungs- und Planungsphasen ab, bevor Sie mit den nächsten Phasen beginnen. Der Migrationspfad und die Toolauswahl hängen von den Entscheidungen und gesammelten Daten ab.

In diesem Abschnitt werden die folgenden gängigen Tools erläutert:

  - MySQL Workbench

  - mysqldump

  - mydumper und myloader

  - Datenreplikation (binlog)

### <a name="mysql-workbench"></a>MySQL Workbench

[MySQL Workbench](https://www.mysql.com/products/workbench/) verfügt über eine umfassende grafische Benutzeroberfläche, über die Entwickler und Administratoren ihre MySQL-Instanzen entwerfen, entwickeln und verwalten können.

Die aktuelle Version von MySQL Workbench bietet ausgefeilte [Objektmigrationsfunktionen](https://www.mysql.com/products/workbench/migrate/) beim Verschieben einer Datenbank von einer Quelle an ein Ziel.

#### <a name="data-import-and-export"></a>Importieren und Exportieren von Daten

MySQL Workbench bietet eine assistentenbasierte Benutzeroberfläche für den vollständigen oder teilweisen Export und Import von Tabellen und Datenbankobjekten. Ein Beispiel für die Verwendung von MySQL Workbench finden Sie unter [Migrieren der MySQL-Datenbank durch Import und Export](../../concepts-migrate-import-export.md).

### <a name="dump-and-restore-mysqldump"></a>Sichern und Wiederherstellen (mysqldump)

`mysqldump` wird in der Regel im Rahmen der MySQL-Installation bereitgestellt. Hierbei handelt es sich um ein [Clienthilfsprogramm](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html), das ausgeführt werden kann, um logische Sicherungen zu erstellen, die einen Satz von SQL-Anweisungen ergeben. Diese Anweisungen können dann wiedergegeben werden, um eine Point-in-Time-Wiederherstellung der Datenbank durchzuführen. `mysqldump` ist nicht als schnelle oder skalierbare Lösung zum Sichern oder Migrieren großer Datenmengen gedacht. Aufgrund der für die Indexaktualisierung erforderlichen Datenträger-E/A kann die Leistung beim Ausführen einer großen Anzahl von SQL-Einfügeanweisungen ungenügend sein. In Kombination mit anderen Tools, die das ursprüngliche Schema erfordern, ist `mysqldump` hingegen ein hervorragendes Tool zum Generieren der Datenbank- und Tabellenschemas. Die Schemas ermöglichen die Erstellung der Zielzonenumgebung des Ziels.

Das Hilfsprogramm `mysqldump` bietet praktische Features für die Datenmigrationsphase. Vor dem Ausführen des Hilfsprogramms müssen Leistungsaspekte ausgewertet werden. Weitere Informationen finden Sie unter [Überlegungen zur Leistung](../../concepts-migrate-dump-restore.md#performance-considerations).

### <a name="mydumper-and-myloader"></a>mydumper und myloader

In Umgebungen mit großen Datenbanken, die eine schnelle Migration erfordern, empfiehlt sich die Verwendung von [mydumper und myloader](https://github.com/maxbube/mydumper). Diese Tools sind in C++ geschrieben und verwenden Verfahren mit mehreren Threads, um die Daten schnellstmöglich an die MySQL-Zielinstanz zu senden. `mydumper` und `myloader` machen sich Parallelismus zunutze und können die Migration um den Faktor 10 oder noch mehr beschleunigen.

Die binären Releases der Tools, die öffentlich zum Download bereitstehen, wurden für Linux kompiliert. Für die Ausführung unter Windows müssen die Open-Source-Projekte neu kompiliert werden. Das Kompilieren von Quellcode und das Erstellen von Releases sind für die meisten Benutzer keine alltäglichen Aufgaben.

### <a name="data-in-replication-binlog"></a>Datenreplikation (binlog)

Mit der [binlog-Replikation](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html) bietet MySQL ähnlich wie andere Datenbankverwaltungssysteme ein Protokollreplikationsfeature. Das Replikationsfeature `binlog` unterstützt Sie bei der Datenmigration und der Erstellung von Lesereplikaten.

Nutzen Sie die binlog-Replikation in einem Onlineszenario zum [Migrieren Ihrer Daten zu Azure Database for MySQL](../../concepts-data-in-replication.md). Die Datenreplikation trägt dazu bei, die Downtime zu verringern, die für die abschließenden Zieldatenänderungen erforderlich ist.

Für die Verwendung des `binlog`-Replikationsfeatures müssen einige [Setupanforderungen](../../howto-data-in-replication.md#link-source-and-replica-servers-to-start-data-in-replication) erfüllt werden:

  - Vom Masterserver sollte die MySQL InnoDB-Engine verwendet werden. Bei Verwendung einer anderen Speicher-Engine müssen die Tabellen zu InnoDB migriert werden.

  - Migrationsbenutzer müssen über Berechtigungen zum Konfigurieren der binären Protokollierung und zum Erstellen neuer Benutzer auf dem Masterserver verfügen.

  - Wenn für den Masterserver SSL aktiviert ist, muss das für die Domäne bereitgestellte SSL-Zertifizierungsstellenzertifikat in die gespeicherte Prozedur „mysql.az\_replication\_change\_master“ eingeschlossen werden. Sehen Sie sich die folgenden [Beispiele](../../howto-data-in-replication.md#link-source-and-replica-servers-to-start-data-in-replication) und den Parameter „master\_ssl\_ca“ an.

  - Vergewissern Sie sich, dass die IP-Adresse des Masterservers den Firewallregeln des Azure Database for MySQL-Replikatservers hinzugefügt wurde. Aktualisieren Sie Firewallregeln über das Azure-Portal oder über die Azure-Befehlszeilenschnittstelle.

  - Vergewissern Sie sich, dass für den Computer, der den Masterserver hostet, sowohl ein- als auch ausgehender Datenverkehr am Port 3306 zugelassen wird.

  - Stellen Sie sicher, dass der Masterserver über eine zugängliche IP-Adresse (öffentlich oder privat) zwischen Quelle und Zielen verfügt.

Ausführliche Informationen zum Durchführen einer Migration mittels Replikation finden Sie unter [Verknüpfen des Quell- und Replikatservers zum Starten der Datenreplikation](../../howto-data-in-replication.md#link-source-and-replica-servers-to-start-data-in-replication).

Die Replikationsmethode `binlog` verursacht eine hohe CPU-Auslastung und erfordert zusätzlichen Speicherplatz. Migrationsbenutzer sollten bei Onlinemigrationen die Last im Quellsystem testen und ermitteln, ob sie akzeptabel ist.

### <a name="azure-database-migration-service-dms"></a>Azure Database Migration Service (DMS)

[Azure Database Migration Service (DMS)](https://azure.microsoft.com/services/database-migration/) ist ein cloudbasiertes Azure-Tool, mit dem Administratoren die verschiedenen Einstellungen für die Migration nachverfolgen und bei Bedarf wiederverwenden können. Von DMS werden Migrationsprojekte mit Einstellungen erstellt, die auf verschiedene Quellen und Ziele verweisen. Das Tool unterstützt [Offlinemigrationen](../../../dms/tutorial-mysql-azure-mysql-offline-portal.md). Darüber hinaus werden lokale Datenworkloads und cloudbasierte Workloads wie Amazon Relational Database Service (RDS) für MySQL unterstützt.

DMS ist zwar ein Onlinetool, aber nicht auf das `binlog`-Replikationsfeature von MySQL angewiesen, um seine Aufgaben zu erfüllen. Aktuell automatisiert DMS zum Teil den Offlinemigrationsprozess. DMS erfordert die Generierung und Anwendung des entsprechenden Schemas in der Azure Database for MySQL-Zielinstanz. Schemas können mithilfe des Clienthilfsprogramms `mysqldump` exportiert werden.

### <a name="fastestminimum-downtime-migration"></a>Schnellste Migration/Migration mit kürzester Downtime

Daten können auf verschiedenste Weise migriert werden. Die Wahl der Methode hängt sowohl von den Qualifikationen des Migrationsteams als auch davon ab, wie viel Downtime für die Datenbank- und Anwendungsbesitzer akzeptabel ist. Einige Tools unterstützen parallele Datenmigrationsansätze mit mehreren Threads, während andere Tools nur für die einfache Migration von Tabellendaten konzipiert sind.

Die schnellste und umfassendste Methode ist die Verwendung der `binlog`-Replikation (entweder direkt mit MySQL oder über DMS). Hierzu müssen jedoch Primärschlüssel hinzugefügt werden.

### <a name="decision-table"></a>Entscheidungstabelle

WWI kann zwischen verschiedenen Methoden für die Migration von MySQL-Workloads wählen. Die folgende Tabelle enthält die potenziellen Methoden sowie die jeweiligen Vor- und Nachteile:

| Ziel | BESCHREIBUNG | Tool | Voraussetzungen | Vorteile | Nachteile |
|-----------|-------------|------|---------------|------------|---------------|
| **Schnellstmögliche Migration** | Paralleler Ansatz| mydumper und myloader | Linux | Stark parallelisiert | Zieldrosselung |
| **Onlinemigration** | Möglichst lange Verfügbarkeit der Quelle | binlog | Keine | Nahtlos | Zusätzlicher Verarbeitungsaufwand und Speicherbedarf |
| **Offlinemigration** | Möglichst lange Verfügbarkeit der Quelle | Database Migration Service (DMS)| Keine | Wiederholbarer Prozess | Auf Daten beschränkt; unterstützt alle MySQL-Versionen |
| **Stark angepasste Offlinemigration** | Selektiver Export von Objekten | mysqldump | Keine | Hochgradig anpassbar | Manuell |
| **Halbautomatisierte Offlinemigration** | Export und Import über eine Benutzeroberfläche | MySQL Workbench | Herunterladen und Installieren | Halbautomatisiert | Nur gängige Switches werden unterstützt |

### <a name="wwi-scenario"></a>WWI-Szenario

WWI hat als erste Migrationsworkload die Konferenzdatenbank des Unternehmens ausgewählt. Die Workload wurde gewählt, da sie aufgrund der Lücke im jährlichen Konferenzzeitplan das geringste Risiko und die höchste akzeptable Downtime aufwies. Darüber hinaus wurde basierend auf der Bewertung des Migrationsteams beschlossen, eine Offlinemigration mithilfe von MySQL Workbench durchzuführen.

### <a name="migration-methods-checklist"></a>Prüfliste für Migrationsmethoden

  - Wahl der passenden Methode für die Ziel- und Quellumgebung

  - Methode kann die geschäftlichen Anforderungen erfüllen

  - Methode wird von der Datenworkload unterstützt (muss immer geprüft werden)  


## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Test Plans](./06-test-plans.md)