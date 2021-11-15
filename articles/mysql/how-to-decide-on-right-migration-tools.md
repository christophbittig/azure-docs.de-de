---
title: Auswählen der geeigneten Tools für die Migration zu Azure Database for MySQL
description: Dieser Artikel enthält eine Entscheidungstabelle, mit der Kund*innen die geeigneten Tools für die Migration zu Azure Database for MySQL auswählen können.
ms.service: mysql
author: shriram-muthukrishnan
ms.author: shriramm
ms.reviewer: maghan
ms.topic: how-to
ms.custom: ''
ms.date: 10/12/2021
ms.openlocfilehash: 686874492f44df1171a320aaacf8e1967f8cb060
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130270560"
---
# <a name="select-the-right-tools-for-migration-to-azure-database-for-mysql"></a>Auswählen der geeigneten Tools für die Migration zu Azure Database for MySQL

[!INCLUDE[applies-to-mysql-single-flexible-server](includes/applies-to-mysql-single-flexible-server.md)]

## <a name="overview"></a>Übersicht

Migrationen sind mehrstufige, komplexe Projekte. Die plattformübergreifende Migration von Datenbankservern umfasst mehr als nur die Daten- und Schemamigration. Es müssen auch mehrere andere Komponenten verschoben werden, z. B. Serverkonfigurationsparameter, Netzwerke, Zugriffssteuerungsregeln und mehr. Diese sind erforderlich, um sicherzustellen, dass die Funktionalität des Datenbankservers auf der neuen Zielplattform die Funktionalität in der Quelle imitiert. 

Ausführliche Informationen zur und Anwendungsfälle der Migration von Datenbanken zu Azure Database for MySQL finden Sie im [Leitfaden zur Datenbankmigration](migrate/mysql-on-premises-azure-db/01-mysql-migration-guide-intro.md). In diesem Dokument finden Sie Tipps, mit denen Sie eine MySQL-Migration zu Azure erfolgreich planen und durchführen können. 

Grundsätzlich können Migrationen in offline und online eingeteilt werden. 

- Bei einer Offlinemigration wird der Quellserver offline geschaltet, und auf dem Zielserver wird ein Speicherabbild erstellt und eine Wiederherstellung der Datenbanken durchgeführt. 

- Bei einer Onlinemigration (Migration mit minimaler Downtime) sind auf dem Quellserver Updates möglich, und die Migrationslösung repliziert die laufenden Änderungen zwischen dem Quell- und Zielserver zusammen mit dem anfänglichen Speicherabbild Erstellen und der Wiederherstellung auf dem Zielserver. 

Wenn Downtime bei Ihrer Anwendung kein Problem darstellt, sind Offlinemigrationen immer die bevorzugte Lösung, da sie einfach sind und leicht ausgeführt werden können. Wenn für Ihre Anwendung jedoch nur minimale Downtime in Frage kommt, ist eine Onlinemigration die bessere Wahl. Migrationen der meisten OLTP-Systeme fallen in diese Kategorie, z. B. Zahlungsverarbeitung und E-Commerce. 

## <a name="decision-table"></a>Entscheidungstabelle

Sehen Sie sich die Details in der folgenden Tabelle an, um sich die Auswahl der geeigneten Tools für die Migration zu Azure Database for MySQL zu erleichtern. 

| Szenarien | Empfohlene Tools | Links |
|-------|------|------------|
| Offlinemigrationen bei Datenbanken >= 1 TB | Speicherabbilderstellung und Wiederherstellung mithilfe von **MyDumper/MyLoader** und High Compute VM | [Migrieren großer Datenbanken zu Azure Database for MySQL mit „mydumper“/„myloader“](concepts-migrate-mydumper-myloader.md) <br><br> [Best Practices für die Migration großer Datenbanken zu Azure Database for MySQL](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/best-practices-for-migrating-large-databases-to-azure-database/ba-p/1362699)|
| Offlinemigrationen bei Datenbanken < 1 TB  | Verwenden Sie **Azure DMS** (Database Migration Service), wenn die Netzwerkbandbreite zwischen Quelle und Ziel gut ist (z. B. bei einer Highspeed-Expressroute).  <br><br>   **ODER** <br><br> Verwenden Sie **MyDumper/MyLoader und High Compute-VM**, und nutzen Sie die Komprimierungseinstellungen, um Daten effizient über Netzwerke mit niedriger Geschwindigkeit zu migrieren, wenn Sie über eine geringe Netzwerkbandbreite zwischen Quelle und Azure verfügen.  <br><br> **ODER** <br><br> Verwenden Sie **mysqldump** und das Hilfsprogramm **MySQL Workbench zum Exportieren/Importieren**, um Offlinemigrationen für kleinere Datenbanken durchzuführen.  | [Tutorial: Offlinemigration von MySQL zu Azure Database for MySQL mit DMS – Azure Database Migration Service](../dms/tutorial-mysql-azure-mysql-offline-portal.md)<br><br>  [Migrieren von Amazon RDS für MySQL zu Azure Database for MySQL mithilfe von MySQL Workbench](how-to-migrate-rds-mysql-workbench.md)<br><br>  [Importieren und Exportieren – Azure Database for MySQL](concepts-migrate-import-export.md)|
| Onlinemigration |  **MyDumper/MyLoader mit Datenreplikation** <br><br> **Mysqldump mit Datenreplikation** kann für kleine Datenbanken (kleiner als 100 GB) in Betracht gezogen werden.  Diese Methoden können sowohl bei externen als auch bei plattforminternen Migrationen angewandt werden. | [Konfigurieren der Datenreplikation – Azure Database for MySQL Flexible Server](flexible-server/how-to-data-in-replication.md) <br><br> [Tutorial: Migrieren von Azure Database for MySQL Single Server zu Azure Database for MySQL Flexible Server mit minimaler Downtime](howto-migrate-single-flexible-minimum-downtime.md) |
|Migrationen von Einzelservern zu flexiblen Servern |  **Offline**: Das benutzerdefinierte Shellskript wird auf [GitHub](https://github.com/Azure/azure-mysql/tree/master/azuremysqltomysqlmigrate) gehostet. Dieses Skript verschiebt auch andere Serverkomponenten wie Sicherheitseinstellungen und Serverparameterkonfigurationen. <br><br>**Online**: **MyDumper/MyLoader mit Datenreplikation** |  [Migrieren von Azure Database for MySQL – Einzelserver zu flexiblem Server in 5 einfachen Schritten.](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/migrate-from-azure-database-for-mysql-single-server-to-flexible/ba-p/2674057)<br><br>   [Tutorial: Migrieren von Azure Database for MySQL Single Server zu Azure Database for MySQL Flexible Server mit minimaler Downtime](howto-migrate-single-flexible-minimum-downtime.md)| 

## <a name="next-steps"></a>Nächste Schritte
* [Migrieren einer lokalen MySQL-Instanz zu Azure Database for MySQL](migrate/mysql-on-premises-azure-db/01-mysql-migration-guide-intro.md)

<br><br>
